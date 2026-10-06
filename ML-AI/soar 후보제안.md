#soar #인지기반아키텍처 #SymbolicAI

soar를 구상하다가 에러 사항을 하나 발견했다.
```
# 엘리베이터 & 명단 복사 공사
sp {elaborate*top-state*top-state
   (state <s> ^superstate nil) 
-->
   (<s> ^top-state <s>)
}

sp {elaborate*state*top-state
   (state <s> ^superstate.top-state <ts>) 
-->
   (<s> ^top-state <ts>)
}

# 윗방의 item을 내 방으로 무조건 복사
sp {elaborate*state*item*down
   (state <s> ^superstate.item <i>)
-->
   (<s> ^item <i>)
}

# 1등 정해졌을 때 Python으로 쏘는 룰
sp {apply*recommend-cafe*send-to-python
   (state <s> ^operator <o>
              ^io.output-link <ol>)
   (<o> ^name recommend-cafe
        ^cafe-name <c-name>)
-->
   (<ol> ^final-recommendation <c-name>)
}

sp {recommend*OPERATOR*1
   (state <s> ^io.input-link <il>)
   (<il> ^cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |분위기| ^property |카공|)
-->
   (<s> ^operator <o> +)
   (<o> ^name recommend-cafe ^cafe-name <c-name>)
}

sp {recommend*OPERATOR*2
   (state <s> ^io.input-link <il>)
   (<il> ^cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |매장| ^property |카공|)
-->
   (<s> ^operator <o> +)
   (<o> ^name recommend-cafe ^cafe-name <c-name>)
}

sp {recommend*OPERATOR*3
   (state <s> ^io.input-link <il>)
   (<il> ^cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |공간| ^property |공부|)
-->
   (<s> ^operator <o> +)
   (<o> ^name recommend-cafe ^cafe-name <c-name>)
}

sp {recommend*OPERATOR*4
   (state <s> ^io.input-link <il>)
   (<il> ^cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |콘센트| ^property |많|)
-->
   (<s> ^operator <o> +)
   (<o> ^name recommend-cafe ^cafe-name <c-name>)
}

sp {recommend*OPERATOR*5
   (state <s> ^io.input-link <il>)
   (<il> ^cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |분위기| ^property |조용|)
-->
   (<s> ^operator <o> +)
   (<o> ^name recommend-cafe ^cafe-name <c-name>)
}

# 1차 심사 (S1) - 기본 제안 & 분위기/카공
sp {recommend*S1*judge
   (state <s> ^operator <o1> +
              ^operator <o2> +
              ^io.input-link <il>)
   (<o1> ^name recommend-cafe ^cafe-name <c1-name>)
   (<o2> ^name recommend-cafe ^cafe-name <c2-name> <> <c1-name>)
   (<il> ^cafe <c1> ^cafe <c2>)
   (<c1> ^name <c1-name> ^feature <f1>)
   (<f1> ^topic |분위기| ^property |카공|)
   (<c2> ^name <c2-name>)
   - { (<c2> ^feature <f2>) (<f2> ^topic |분위기| ^property |카공|) }
-->
   (<s> ^operator <o1> > <o2>)
}
# LLM 호출
sp {resolve*tie*S2*ask-llm
   # state 확인 밑 정리
   (state <s> ^impasse <any-impasse>
              ^superstate <s1>
              ^item <o1> ^item <o2> 
              ^top-state <ts>)
   (<s1> ^superstate nil)

   (<o1> ^cafe-name <c1-name>)
   (<o2> ^cafe-name { <c2-name> > <c1-name> }) 

   # 2. 1층 우편함 확인
   (<ts> ^io.output-link <ol>)
-->
   # 3. 파이썬으로 최종 SOS 발사
   (<ol> ^ask-llm <req>)
   (<req> ^cand1 <c1-name>
          ^cand2 <c2-name>
          ^stopped-depth 2)
}

```
위 soar-rule을 짰을 때 결과가 나오지 않는 문제가 확인 되었다.

아래는 soar의 추론과정 로그증 impasse 상태의 문제 log이다.
```
(O3 ^cafe-name |메이지커피| ^name recommend-cafe)
(O9 ^cafe-name |저스트로맨틱| ^name recommend-cafe)
(O8 ^cafe-name |엔제리너스 대학동점| ^name recommend-cafe)
(O7 ^cafe-name |소재지| ^name recommend-cafe)
(O6 ^cafe-name |방앗간| ^name recommend-cafe)
(O5 ^cafe-name |메이지커피| ^name recommend-cafe)
(O4 ^cafe-name |메이지커피| ^name recommend-cafe)
(O2 ^cafe-name |로드트립| ^name recommend-cafe)
(L1 ^command C2 ^result R3)
```

문제가 되는 매장이 존재한다. 바로 `메이저커피` 이다. 해당 매장은 시나리오 대로라면 원래 제일 처음 추출이 되는 카페여야 한다. 하지만 지금 문제가 존재한다.
soar 룰 파일을 확인하면 각 operatet의 lhs를 확인하면 조건에 해당할때 마다 +를 하면서 추천 리스트에 올리는 중이다.

이러한 방식은 로그와 같이 **매장이 여러 조건에 적합하면 조건에 해당할 때 마다 opertater로 올라가는** 문제가 발생한다. 이렇게 되면 impasse가 발생하더라도 `ask-llm`에서 설정한 조건인 다른 매장명이 존재할때 발동하는 조건에는 해당하지 않게 된다.
> 물론 `메이저 커피` 말고 다른 매장이 리스트에 올라가 있으면 운좋게 작동은 되겠지만 근본적으로 해당 코드 룰을 생성하는 건 잘못됐다.

그래서 아래와 같이 코드를 수정하였다.
```
sp {elaborate*candidate*1
   (state <s> ^io.input-link.cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |분위기| ^property |카공|)
-->
   (<s> ^candidate <c-name>)
}

sp {elaborate*candidate*2
   (state <s> ^io.input-link.cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |매장| ^property |카공|)
-->
   (<s> ^candidate <c-name>)
}

sp {elaborate*candidate*3
   (state <s> ^io.input-link.cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |공간| ^property |공부|)
-->
   (<s> ^candidate <c-name>)
}

sp {elaborate*candidate*4
   (state <s> ^io.input-link.cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |콘센트| ^property |많|)
-->
   (<s> ^candidate <c-name>)
}

sp {elaborate*candidate*5
   (state <s> ^io.input-link.cafe <c>)
   (<c> ^name <c-name> ^feature <f> )
   (<f> ^topic |분위기| ^property |조용|)
-->
   (<s> ^candidate <c-name>)
}

sp {recommend*OPERATOR*propose
   (state <s> ^candidate <c-name>)
-->
   (<s> ^operator <o> +)
   (<o> ^name recommend-cafe ^cafe-name <c-name>)
}
```

해당 코드는 기존과 달리 매번 올리는 방식이 아닌 모든 조건에 해당하는 매장(후보)를 수집하고 마지막에 업로는 하는 방식으로 변경 되었다.

soar의 메모리는 중복된 후보는 operater에 올릴때 set()자연스럽게 진행하고 올려준다.