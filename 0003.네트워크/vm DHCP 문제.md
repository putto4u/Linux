우분투 시스템에서 `/etc/netplan/` 디렉토리에 여러 개의 `.yaml` 파일이 존재하는 것은 흔한 상황입니다. 
이는 설정의 우선순위와 적용 방식에 차이가 있기 때문입니다. 각 파일의 역할과 처리 방법을 정리합니다.

---

## 1. YAML 파일별 역할 분석

현재 시스템에 존재하는 두 파일은 각각 다음과 같은 목적으로 생성된 것입니다.

* **01-network-manager-all.yaml**: 주로 데스크탑 버전이나 사용자가 직접 네트워크 매니저를 통해 관리하도록 설정할 때 생성됩니다. `renderer: NetworkManager`라는 설정이 포함되어 있어, GUI나 `nmcli` 도구로 네트워크를 제어하겠다는 뜻입니다.
* **50-cloud-init.yaml**: AWS, Azure, GCP와 같은 클라우드 환경이나 설치 시 자동으로 생성되는 파일입니다. 클라우드 공급자가 인스턴스에 네트워크 정보를 주입하기 위해 사용하며, 보통 `renderer: networkd`를 사용합니다.

### 우선순위 규칙

Netplan은 파일 이름의 **숫자 오름차순(01 → 50 → 70...)**으로 파일을 읽어들입니다. 나중에 읽은 파일의 설정이 이전 파일의 설정을 **덮어쓰거나(Override) 병합(Merge)**합니다. 만약 동일한 인터페이스(예: eth0)에 대한 설정이 양쪽 모두에 있다면, 숫자가 높은 `50-cloud-init.yaml`의 설정이 최종적으로 적용됩니다.

---

## 2. 과거 정보가 남는 이유와 해결책

`dhclient`가 없음에도 예전 IP 정보를 유지하고 있다면, 두 파일 간의 설정 충돌로 인해 네트워크 렌더러가 올바르게 갱신되지 않았을 가능성이 큽니다.

### 해결 단계: 설정 통합 및 정리

1. **현재 활성화된 설정 확인**:
어떤 렌더러가 실제로 인터페이스를 제어하고 있는지 확인합니다.
```bash
networkctl status

```


2. **설정 파일 수정 (권장)**:
하나의 방식으로 통일하는 것이 좋습니다. 클라우드 환경이라면 `50-cloud-init.yaml`을 유지하되, 필요 없는 `01-network-manager-all.yaml`은 백업 후 삭제하거나 주석 처리합니다.
```bash
# 백업 생성
sudo mv /etc/netplan/01-network-manager-all.yaml /etc/netplan/01-network-manager-all.yaml.bak

```


3. **설정 적용 및 강제 갱신**:
기존의 잘못된 캐시를 무시하고 설정을 새로 고침합니다.
```bash
sudo netplan generate
sudo netplan apply

```



---

## 3. 특정 인터페이스의 DHCP 강제 갱신

파일을 수정한 후에도 IP가 바뀌지 않는다면, 해당 인터페이스를 물리적으로 껐다 켜는 것과 유사한 효과를 주어야 합니다.

```bash
# 인터페이스 이름이 eth0인 경우
sudo ip link set eth0 down
sudo ip link set eth0 up

```

이 과정에서 시스템은 Netplan 설정에 따라 다시 DHCP 서버(AWS의 경우 VPC 내부 DHCP)에 새로운 리스(Lease)를 요청하게 됩니다.

---

> [!IMPORTANT]
> **AWS 및 클라우드 환경 유료 서비스 알림**
> * **VPC 엔드포인트 및 NAT 게이트웨이**: 만약 외부망과 통신하기 위해 NAT 게이트웨이를 사용 중이라면 시간당 이용 요금과 데이터 처리 비용이 발생합니다.
> * **Public IPv4**: AWS는 2024년 2월부터 사용 중인 모든 퍼블릭 IPv4 주소에 대해 시간당 소액($0.005)의 요금을 부과합니다. IP 갱신 과정에서 불필요한 퍼블릭 IP가 할당되지 않도록 주의하세요.
> 
> 

---

Next Step:
YAML 파일 내부 설정값 비교 방법, renderer(networkd vs NetworkManager) 차이점, 인터페이스별 고정 IP 할당법
