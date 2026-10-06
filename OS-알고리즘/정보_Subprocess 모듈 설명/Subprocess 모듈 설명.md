#Python #linux 

`subprocess` 모듈은 Python에서 외부 프로세스를 생성하고 그와 통신할 수 있는 기능을 제공하는 모듈. 즉, Python 코드에서 운영 체제의 명령어나 다른 프로그램을 실행하고, 실행된 명령의 입력, 출력, 오류를 제어하거나 캡처가 가증하다.

### 주요 기능 및 역할

- 외부 명령어나 스크립트를 실행
- 실행된 프로세스와 상호작용하거나, 실행 결과를 프로그램 내에서 처리
- 시스템 명령을 실행하고 그 결과를 얻어와서 Python 코드로 처리하는 데 유용

### 기본적으로 제공하는 기능

- **프로세스 생성 및 실행**: 새로운 서브 프로세스를 시작
- **입출력 파이프 설정**: 서브 프로세스에 데이터를 보내거나 출력
- **프로세스 종료 상태 확인**: 실행된 명령의 종료 코드를 확인

### 기본적인 사용법

1. **`subprocess.run()`**:

   - Python 3.5 이후의 고수준 함수로, 명령을 실행하고 그 결과를 반환받는 데 사용. .

   ```python
   python코드 복사import subprocess
   
   # "ls" 명령어 실행
   result = subprocess.run(['ls', '-l'], capture_output=True, text=True)
   print(result.stdout)  # 표준 출력 내용
   ```

2. **`subprocess.Popen()`**:

   - 더 복잡한 사용을 위해, 프로세스 생성 및 통신을 제어할 수 있는 저수준 함수

   ```python
   python코드 복사import subprocess
   
   # "echo" 명령어 실행 및 표준 출력 가져오기
   process = subprocess.Popen(['echo', 'Hello, World!'], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
   stdout, stderr = process.communicate()
   
   print(stdout.decode('utf-8'))  # 표준 출력 내용
   ```

### 주요 함수 및 메서드

1. **`subprocess.run()`**:

   - 외부 명령을 실행하고, 명령어의 결과를 캡처
   - Python 3.5 이상에서 선호되는 방법

   ```python
   python코드 복사result = subprocess.run(['ls', '-l'], capture_output=True, text=True)
   print(result.returncode)  # 실행 후 반환 코드
   print(result.stdout)      # 실행 후 표준 출력
   print(result.stderr)      # 실행 후 표준 오류 출력
   ```

2. **`subprocess.Popen()`**:

   - 프로세스를 생성하고 통신할 수 있는 저수준 API
   - 명령 실행 중에 입력과 출력을 실시간으로 제어하고 싶을 때 사용

   ```python
   python코드 복사process = subprocess.Popen(['ls', '-l'], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
   stdout, stderr = process.communicate()  # 실행 후 프로세스와 상호작용
   ```

3. **`subprocess.call()`**:

   - 외부 명령을 실행하고, 명령이 종료되기를 기다렸다가 종료 상태를 반환

   ```python
   python코드 복사return_code = subprocess.call(['ls', '-l'])
   print(f"Return code: {return_code}")
   ```

4. **`subprocess.check_output()`**:

   - 명령어를 실행하고, 표준 출력을 문자열로 반환
   - 프로세스가 실패하면 예외가 발생

   ```python
   python코드 복사output = subprocess.check_output(['echo', 'Hello, World!'])
   print(output.decode('utf-8'))
   ```

### 주요 매개변수

- **`args`**: 실행할 명령어와 인자를 리스트로 전달, 문자열로도 전달할 수 있지만 보안 문제를 방지하려면 리스트 형태를 사용하는 것이 좋음
- **`stdout`, `stderr`**: 표준 출력과 표준 오류를 어디로 보낼지를 지정 `subprocess.PIPE`를 사용하여 해당 데이터를 캡처 가능
- **`shell`**: 명령어를 셸에서 실행할지를 결정,  `True`로 설정하면 명령어를 셸에서 실행하지만, 보안 위험이 존재
- **`capture_output`**: Python 3.5 이후, `stdout`과 `stderr`를 자동으로 캡처하는 매개변수
- **`text`**: `True`로 설정하면, 출력이 `bytes` 대신 문자열(`str`)로 반환

### 예시

#### 1. 기본 명령 실행

```
python코드 복사import subprocess

# "ls -l" 명령을 실행하고 결과 출력
result = subprocess.run(['ls', '-l'], capture_output=True, text=True)
print(result.stdout)
```

#### 2. 명령어 출력 처리

```
python코드 복사import subprocess

# "echo Hello" 명령어 실행하고 결과 출력
process = subprocess.Popen(['echo', 'Hello'], stdout=subprocess.PIPE)
output, _ = process.communicate()
print(output.decode('utf-8'))
```

### 요약

- **`subprocess` 모듈**은 외부 프로그램 실행 및 프로세스와의 통신을 가능하게 해주는 파이썬의 강력한 기능
- 간단한 명령 실행에는 **`subprocess.run()`**을 사용하는 것이 가장 간편하고, 더 복잡한 요구사항이 있을 때는 **`Popen()`**을 사용하여 세부적으로 제어 가능

이 모듈은 특히 스크립트에서 시스템 명령어를 실행해야 할 때 매우 유용합니다.