# apply 도중 끊겼을 때

- 날짜: 2026-11-23

## 문제

VM 크기를 바꾸는 `terraform apply` 도중 터미널을 닫아 작업이 끊겼다.

## 원인

- 크기 변경은 VM을 끄고(할당 취소) 크기를 바꾼 뒤 다시 켜는 데 몇 분이 걸린다. 중간에 끊기거나 크기 변경이 실패하면 VM이 할당 취소(중지)된 채 남을 수 있다.
- 끊기거나 실패한 apply 뒤에는 상태 파일의 크기가 실제와 다를 수 있다. 실패한 크기가 상태 파일에 적혀 있었다.

## 해결

1. 실제 상태 확인: `az vm show -d --query "{size:hardwareProfile.vmSize,power:powerState}"`
2. 할당 취소 상태면 VM 시작: `az vm start`
3. `terraform plan`으로 설정·상태·실제가 맞는지 확인하고, 다르면 변수 값을 실제에 맞춘다(`No changes`가 목표).

## 배운 점

- 긴 apply는 tmux 같은 세션 안에서 돌린다.
- 끊긴 뒤엔 상태 파일을 믿지 말고 실제 리소스부터 본다.
