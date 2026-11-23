# 스왑 파일 추가

- 날짜: 2026-11-23

## 문제

메모리 1GB VM이라 메모리가 모자라면 프로세스가 죽을(OOM) 수 있다.

## 원인

1GB에 DB와 웹 앱을 같이 돌리면 여유가 적다.

## 해결

2GB 스왑 파일을 추가했다(root로).

```bash
fallocate -l 2G /swapfile          # 안 되면 dd if=/dev/zero of=/swapfile bs=1M count=2048
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab               # 재부팅 뒤에도 켜지게
echo 'vm.swappiness=10' > /etc/sysctl.d/99-swappiness.conf    # 메모리가 정말 모자랄 때만 쓰게
sysctl -w vm.swappiness=10
```

확인: `free -m`, `swapon --show`

## 배운 점

- 스크립트로 만들 땐 각 단계마다 "이미 되어 있으면 건너뛰기"를 넣어 여러 번 돌려도 안전하게 한다(fstab 줄 중복 등).
- 스왑은 OOM을 막는 안전망이지 메모리 대신이 아니다. 스왑 사용이 계속 크면 크기를 다시 올린다.
