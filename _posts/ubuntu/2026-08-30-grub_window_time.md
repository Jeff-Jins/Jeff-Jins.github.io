---
title: "grub 부팅시 윈도우 시간이 안맞는 경우"
date: 2026-08-30 06:00:00 +0900
categories: [Ubuntu]
tags: [setup]
---
## GRUB로 부팅시 윈도우 시간이 안맞는 이유
리눅스는 하드웨어 시계를 UTC로, 윈도우는 로컬 시간으로 인식하여 부팅할 때마다 시간이 9시간씩 차이가 발생함.

### 해결 방법
Window에서 하드웨어 시계를 UTC로 설정하면 해결 가능
```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /t REG_DWORD /d 1 /f
```
윈도우 cmd에서 위 명령어 실행. 만약 엑세스 불가 에러가 발생하면 관리자 권한으로 cmd를 열어서 입력하면 됨.

이 후 PC 재 부팅시 하드웨어 시계가 UTC 표준으로 고정되어 시간이 안맞는 문제가 해결되었음을 알 수 있음.