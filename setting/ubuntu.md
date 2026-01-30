### Tailscale 설정 가이드

```bash
# Ubuntu 서버에 Tailscale 설치
curl -fsSL https://tailscale.com/install.sh | sh

# 실행 및 로그인 링크 생성
sudo tailscale up

# Ubuntu 서버에서 ssh 키 생성
ssh-keygen -t ed25519

# 상대방 키 등록
echo "[상대방_키]" >> ~/.ssh/authorized_keys

# 클램쉘 모드
# config 파일 열기 (vi, vim, nano 등 편집기 사용)
sudo vi /etc/systemd/logind.conf

# #HandleLidSwitch의 주석을 지우고 다음과 같이 수정
HandleLidSwitch=ignore

# 저장 후 재시작
sudo reboot
```

### Ubuntu 배터리 보존모드 설정

```bash
sudo apt install tlp -y

# 레노버 기준, 아래와 같이 수정
# START_CHARGE_THRESH_BAT0=0
# STOP_CHARGE_THRESH_BAT0=1
sudo nano /etc/tlp.conf

# 설정 반영
sudo tlp start

# 보존 모드 확인
sudo tlp-stat -b
```
