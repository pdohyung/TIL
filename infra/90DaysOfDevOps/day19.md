## 변수, 조건문

### 변수

```shell
# 변수를 선언하고 $변수명으로 사용한다.
text=#Hello World
day=#day19

echo "$text"
echo "Today is $day"

# 사용자의 입력을 받아 $name으로 변수를 사용한다.
echo "Enter your name"
read name
```

### 조건문

```shell
# 점수를 입력 받아 조건문으로 결과를 출력한다.
read score

if [$score -ge 80]
then 
  echo "Good"
elif [$score -ge 50]
then 
  echo "Not Bad"
else
  echo "Bad"
fi

# 파일 조건문
FILE="19day.txt"
if [-f "$FILE"]
then
  echo "$FILE is a file"
else 
  echo "$FILE is not a file"
fi
```

```shell
# 비교 조건
eq - 두 값이 같으면 TRUE 반환
ne - 두 값이 같지 않으면 TRUE 반환
gt - 첫 번째 값이 두 번째 값보다 크면 TRUE 반환
ge - 첫 번째 값이 두 번째 값보다 크거나 같으면 TRUE 반환
lt - 첫 번째 값이 두 번째 값보다 작으면 TRUE 반환
le - 첫 번째 값이 두 번째 값보다 작거나 같으면 TRUE 반환

# 파일 조건
-d file 파일이 디렉토리인 경우 True
-e file 파일이 존재하는 경우 True
-f file 제공된 문자열이 파일인 경우 True
-g file 파일에 그룹 ID가 설정된 경우 True
-r file 파일이 읽을 수 있는 경우 True
-s file 파일의 크기가 0이 아닌 경우 True
```

## 예제

### 요구사항
- 사용자 이름이 command line 인수로 전달될 수 있습니다.
- command line 인수의 이름으로 사용자가 생성됩니다.
- password가 커맨드 라인 인수로 전달될 수 있습니다.
- 사용자의 password가 설정됩니다.
- 계정 생성이 성공적으로 이루어졌음을 나타내는 메시지가 표시됩니다.

```shell
touch create_user.sh # shell 스크립트 생성
chmod +x create_user.sh # 실행 권한 설정
nano create_user.sh # 스크립트 편집
sudo userdel 사용자이름 # 사용자 삭제

########### 사용자 생성 자동화 코드

echo "Enter your username" # 사용자 이름 입력
read username
echo "Enter your password" # 패스워드 입력, -s 옵션을 사용하여 숨길 수 있음.
read password
sudo useradd -m $username # 인수의 이름으로 사용자 생성
sudo chpasswd <<< $username:$password # password 설정
echo "The account for $username has successfully been created." # 성공 메시지
```
