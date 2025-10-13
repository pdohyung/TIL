## 명령어들

- `man man`을 실행하면 각 명령에 대한 더 많은 옵션을 찾을 수 있다.
- `sudo su`로 root 권한을 유지할 수 있고, `exit`로 권한을 해제할 수 있다.

```shell
mkdir day15       # day15 폴더 생성
cd day15          # day15 디렉토리로 이동
rmdir day15       # day15 디렉토리 삭제 (폴더가 비어있는 경우만)
pwd               # 작업 디렉토리의 경로를 출력
touch day15       # day15 파일 생성
ls                # 현재 디렉토리 내 모든 파일과 폴더를 나열
locate day15      # day15 파일의 위치를 출력
sudo updatedb     # 파일이 존재하는데 검색결과가 없다면 실행
mv day15 90DaysOfDevOps # day15 파일을 90DaysOfDevOps 폴더로 이동
mv day15 Day15    # day15 파일 이름을 Day15로 변경
rm -R -f day15    # day15 파일을 강제로 삭제
cp day15 Desktop  # 다른 폴더로 파일을 복사
echo "Hello World!" > day15 # day15 파일에 내용 추가
cat day15         # day15 파일 내용을 출력
cat day15 | grep "Hello" # 특정 단어 찾기

clear   # 터미널 출력화면 초기화
history # 이전에 실행한 명령어 조회
history 3 # 목록에서 3번째 명령어 선택
history | grep "ls" # 특정 명령어 조회
history -c # 명령어 제거
HISTTIMEFORMAT="%d-%m-%Y %T " # history 각 명령에 날짜와 시간을 추가
echo 'export HISTTIMEFORMAT="%d-%m-%Y %T "' >> ~/.bash_profile # bash_profile에 추가
echo 'export HISTSIZE=100000' >> ~/.bash_profile
echo 'export HISTFILESIZE=10000000' >> ~/.bash_profile  # history 파일을 더 크게 설정
passwd # 비밀번호 변경, -p PASSWORD가 포함되면 history에 표시
sudo useradd NewUser # 새로운 사용자 추가
sudo groupadd DevOps # 그룹 재생성
sudo usermod NewUser -a -G DevOps # 사용자를 그룹에 추가
usermod -a -G sudo NewUser # sudo 그룹에 사용자 추가
```

### 권한

- 0 = 없음 ---
- 1 = 실행만 --X
- 2 = 쓰기만 -W-
- 3 = 쓰기 및 실행 -WX
- 4 = 읽기 전용 R--
- 5 = 읽기 & 실행 R-X
- 6 = 읽기 및 쓰기 RW-
- 7 = 읽기, 쓰기 및 실행 RWX

777, 775 이런식으로 표시되며 각각 User-Group-Everyone을 나타낸다.

```shell
ls -al day15 # -rw-rw-r-- day15 파일의 권한 조회
chmod 750 day15 # day15 파일의 권한 수정, -R 옵션으로 재귀 설정
sudo chown NewUser day15 # day15의 소유권을 변경, -R 옵션 가능
who | awk '{print $1}' # who는 정보를 출력하고, 해당 awk를 포함하면 첫 번째 열(이름 목록)만 출력
```
