IT 전문가로서 시스템 관리의 핵심 도구인 `systemctl`의 사용법과 주요 옵션 목록을 체계적으로 정리해 드립니다.

`systemctl`은 **Systemd** 시스템 및 서비스 관리자를 제어하는 명령어 인터페이스로, 리눅스 서버 운영에 있어 가장 기본이 되는 명령어입니다.

---

##💻 `systemctl` 사용법 및 주요 옵션 목록`systemctl` 명령어는 주로 **Unit(유닛)** 파일을 대상으로 합니다. 유닛은 서비스(`service`), 마운트(`mount`), 소켓(`socket`) 등 Systemd가 관리하는 모든 대상을 통칭합니다.

###I. 🚀 서비스 관리 (가장 자주 사용되는 기능)서비스 유닛(`*.service`)을 대상으로 하여 실행, 중지, 재시작 등의 작업을 수행합니다.

| 명령어 | 목적 | 예시 |
| --- | --- | --- |
| **`start`** | 서비스를 **시작**합니다. | `$ sudo systemctl start apache2` |
| **`stop`** | 서비스를 **중지**합니다. | `$ sudo systemctl stop apache2` |
| **`restart`** | 서비스를 **재시작**합니다. | `$ sudo systemctl restart apache2` |
| **`reload`** | 서비스의 설정 파일만 다시 읽습니다 (데몬 재시작 없이). | `$ sudo systemctl reload nginx` |
| **`status`** | 서비스의 **현재 상태**를 상세하게 출력합니다. | `$ systemctl status ssh` |
| **`enable`** | 시스템 부팅 시 서비스가 **자동 시작**되도록 설정합니다. | `$ sudo systemctl enable docker` |
| **`disable`** | 시스템 부팅 시 서비스 **자동 시작을 비활성화**합니다. | `$ sudo systemctl disable docker` |
| **`is-enabled`** | 서비스가 부팅 시 활성화되었는지 확인합니다. | `$ systemctl is-enabled mariadb` |

###II. 📋 시스템 전반 및 유닛 관리서비스뿐만 아니라 전체 시스템의 상태 및 다른 유형의 유닛을 관리합니다.

| 명령어 | 목적 | 설명 |
| --- | --- | --- |
| **`list-units`** | 현재 로드된 모든 유닛 목록을 출력합니다. | `systemctl list-units` |
| **`list-unit-files`** | 설치된 모든 유닛 파일의 자동 시작 상태를 출력합니다. | `systemctl list-unit-files` |
| **`show`** | 특정 유닛의 자세한 속성(설정 값)을 출력합니다. | `systemctl show ssh` |
| **`poweroff`** | 시스템을 즉시 종료합니다. | `$ sudo systemctl poweroff` |
| **`reboot`** | 시스템을 재부팅합니다. | `$ sudo systemctl reboot` |
| **`isolate`** | 특정 타겟 유닛(예: `multi-user.target`)으로 전환합니다. | `$ sudo systemctl isolate rescue.target` |
| **`daemon-reload`** | 유닛 파일(`.service`, `.target` 등)을 수동으로 수정한 후 Systemd 관리자에게 변경 사항을 적용하도록 알립니다. | `$ sudo systemctl daemon-reload` |

###III. ⚙️ 유용한 옵션 (Flags)| 옵션 | 설명 | 예시 |
| --- | --- | --- |
| **`--all`** (`-a`) | 로드되지 않은(inactive) 유닛까지 모두 표시합니다. | `systemctl list-units --all` |
| **`--type=`** | 특정 유형의 유닛만 표시합니다. (예: `service`, `target`, `mount`) | `systemctl list-units --type=service` |
| **`--state=`** | 특정 상태의 유닛만 표시합니다. (예: `running`, `dead`, `failed`) | `systemctl list-units --state=failed` |
| **`--failed`** | 실행에 실패한 유닛만 표시합니다. | `systemctl --failed` |
| **`--user`** | 시스템 유닛이 아닌, 현재 사용자 계정의 유닛을 관리합니다. | `systemctl --user status` |

###💡 실전 팁: 명령어 자동 완성대부분의 리눅스 배포판에서 `systemctl`을 입력한 후 `Tab` 키를 누르면 사용할 수 있는 명령어 옵션(start, stop 등)이나 유닛 이름(ssh, docker 등)이 자동 완성되어 작업 효율을 높일 수 있습니다.
