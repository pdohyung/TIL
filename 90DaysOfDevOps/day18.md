## SSH

- 다수의 원격 Linux 서버를 관리하기 위해 SSH가 필요하다. SSH를 통해 원격 시스템과의 보안 터널을 구축할 수 있다.
- 원격 머신이 작업 환경과 동일한 시스템이 아니라 원격 위치, 클라우드 기반 시스템, 인터넷을 통해서만 접근할 수 있는 데이터 센터에서 실행될 경우, SSH를 통해 클라이언트와 서버 간의 보안 터널을 제공하여 공격자가 정보를 가로챌 수 없게 한다.
- 항상 TCP 포트(22)로 대기 중이고, 올바른 자격증명이나 SSH 키를 가진 클라이언트만 접근할 수 있다.

> Virtual Box VM에서 SSH 연결
> 1. 시스템 브리지 네트워크 어댑터 추가
>   - **브리지 네트워크**: 가상 머신이 호스트 컴퓨터와 동일한 물리적 네트워크에 연결된 것처럼 동작하는 설정
>   - 로컬 머신이 IP 주소를 갖게 된다. `IP addr`로 확인
> 2. SSH 서버 실행 확인
>   - `sudo systemctl status ssh`: ssh 설정 확인
>   - `sudo apt install OpenSSH-server`: ssh 설치
>   - `sudo ufw allow ssh`: 방화벽이 실행 중인 경우, ssh를 허용
> 3. 원격 접속
>   - putty나 SSH 클라이언트로 username, password를 입력하면 VM에 접속할 수 있다.
>   - 하지만 악의적인 사용자가 로그인 정보를 알게 되면 쉽게 침입할 수 있기에, SSH 키 방식이 선호된다.
>   - `ssh-copy-id vargrant@192.168.0.1`: VM에 key 복사
>   - `ssh vagrant@192.168.0.1`: SSH 키로 연결
>   - 필요한 경우 `passphrase`를 사용하여 더욱 안전하게 만들 수 있다. 또한 password 없이 키 쌍으로만 SSH를 허용하도록 설정할 수 있다.
>   ```shell
>   sudo nano /etc/ssh/sshd_config # 설정 파일 접근
>   # PasswordAuthentication yes를 no로 수정
>   sudo systemctl reload sshd # SSH 서비스 리로드
>   ```

## Keyword
- SSH로 연결 설정하기
- 파일 전송하기
- 개인 키 만들기
- 보안 shell
- 네트워킹 프로토콜
- 보안 통신 허용
- 모든 네트워크 서비스를 보호
- 일반적으로 원격 command line 엑세스에 사용
- LAMP 스택


