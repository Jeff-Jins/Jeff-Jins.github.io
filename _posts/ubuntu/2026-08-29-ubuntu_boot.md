---
title: "Ubuntu grub boot 세팅"
date: 2026-08-29 06:00:00 +0900
categories: [Ubuntu]
tags: [setup]
---
## GRUB 부팅 순서 설정
```
sudo vi /etc/default/grub
```

### GRUB_DEFAULT 값 변경
```
GRUB_DEFAULT=4 # boot 순서마다 다름
```
- GRUB_DEFAULT는 grub로 부트시 최초 포인팅을 할 인덱스

### 변경 내용 반영
```
sudo update-grub
```

## MEMTEST 삭제
문제가 없는경우 불 필요한 MEMTEST 2개가 GRUB2부터는 설치되는데, 이를 disable하는 법
```
sudo vi /etc/default/grub
```

### disbale 추가
```
GRUB_DISABLE_MEMTEST=true
```

### 변경 내용 반영
```
sudo update-grub
```

