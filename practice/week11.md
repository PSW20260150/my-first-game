# Week 11 실습

## 오늘 한 것

### 1. PyInstaller 설치 및 빌드

#### 사용 코드
```bash
pip install pyinstaller
```

#### 설명
PyInstaller는 Python 프로그램을 `.exe` 파일로 변환해주는 프로그램이다.  
이를 사용하면 Python이 설치되지 않은 컴퓨터에서도 게임이나 프로그램을 실행할 수 있다.

---

### 2. resource_path() 함수 추가

#### 사용 코드
```python
import os
import sys

def resource_path(relative_path):
    try:
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")

    return os.path.join(base_path, relative_path)
```

#### 설명
PyInstaller로 빌드하면 실행 파일 내부의 경로 구조가 변경된다.  
그래서 이미지나 사운드 파일을 기존 방식으로 불러오면 오류가 발생할 수 있다.  
`resource_path()` 함수는 실행 환경에 맞는 올바른 파일 경로를 찾아주기 때문에 `.exe` 환경에서도 에셋을 정상적으로 사용할 수 있다.

---

### 3. --add-data 옵션으로 에셋 포함

#### 사용 코드
```bash
pyinstaller --onefile --add-data "assets;assets" main.py
```

#### 설명
PyInstaller는 기본적으로 Python 코드만 포함하기 때문에 이미지, 사운드 같은 에셋 파일은 따로 추가해야 한다.  
`--add-data` 옵션을 사용하면 `assets` 폴더를 실행 파일 안에 함께 포함시킬 수 있다.

---

### 4. .exe 실행 확인

#### 사용 코드
```bash
dist/main.exe
```

#### 설명
빌드가 완료되면 `dist` 폴더 안에 `.exe` 파일이 생성된다.  
실행 파일을 직접 실행하여 게임이 정상적으로 동작하는지 확인하였다.  
이 과정에서 에셋 경로 문제나 실행 오류가 없는지도 함께 점검하였다.

---

## AI 활용 내역

- PyInstaller 설치 방법 질문
- `.exe` 빌드 오류 해결 도움
- `resource_path()` 함수 작성 도움
- `--add-data` 옵션 사용 방법 확인
- 실행 파일에서 이미지가 보이지 않는 문제 해결
- `.exe` 실행 테스트 중 오류 원인 분석
