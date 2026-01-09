이 가이드는 자체 호스팅 클라우드 저장소인 **Nextcloud**를 Ubuntu 서버 환경에서 Docker를 사용하여 설치하는 방법을 다룹니다. Docker를 활용하면 시스템 환경을 깨끗하게 유지하면서도 유지보수와 업데이트가 매우 간편해집니다.

---

## 1. 사전 준비 및 Docker 설치

Nextcloud를 안정적으로 구동하기 위해서는 Docker와 Docker Compose가 필요합니다.

### 시스템 업데이트 및 필수 패키지 설치

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl git

```

### Docker 및 Docker Compose 설치

```bash
# Docker 설치 스크립트 실행
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 현재 사용자를 docker 그룹에 추가 (로그아웃 후 재접속 필요)
sudo usermod -aG docker $USER

```

---

## 2. Docker Compose 구성

Nextcloud는 애플리케이션 서버와 데이터를 저장할 데이터베이스(MariaDB)가 함께 작동해야 합니다. 이를 위해 `docker-compose.yml` 파일을 생성합니다.

### 작업 디렉토리 생성

```bash
mkdir nextcloud-server && cd nextcloud-server
nano docker-compose.yml

```

### docker-compose.yml 작성

아래 내용을 복사하여 붙여넣습니다. `MYSQL_PASSWORD` 부분은 본인만의 안전한 비밀번호로 수정하십시오.

```yaml
version: '3'

services:
  db:
    image: mariadb:10.6
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - db_data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=strong_root_password
      - MYSQL_PASSWORD=nextcloud_db_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud:latest
    restart: always
    ports:
      - 8080:80
    links:
      - db
    volumes:
      - nextcloud_data:/var/www/html
    environment:
      - MYSQL_PASSWORD=nextcloud_db_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db

volumes:
  db_data:
  nextcloud_data:

```

---

## 3. 컨테이너 실행 및 초기 설정

### 서버 실행

설정 파일 작성이 완료되었다면 다음 명령어로 컨테이너를 백그라운드에서 실행합니다.

```bash
sudo docker compose up -d

```

### 웹 인터페이스 접속

1. 웹 브라우저를 열고 `http://서버_IP_주소:8080`에 접속합니다.
2. **관리자 계정 생성**: 사용할 아이디와 비밀번호를 입력합니다.
3. **데이터베이스 설정**: `docker-compose.yml`에서 설정한 정보를 입력합니다.
* **사용자**: `nextcloud`
* **비밀번호**: (설정한 MYSQL_PASSWORD)
* **데이터베이스 이름**: `nextcloud`
* **호스트**: `db` (Docker 내부 네트워크 이름을 사용함)



---

## 4. 운영 시 주의사항 및 과금 요소

### 외부 스토리지 및 클라우드 과금 (중요)

Nextcloud 자체는 오픈소스 소프트웨어로 별도의 라이선스 비용이 발생하지 않지만, 인프라 구성 방식에 따라 다음과 같은 비용이 발생할 수 있습니다.

* **AWS EC2 인스턴스**: 프리티어(t2.micro 등) 범위를 초과하는 사양 선택 시 시간당 요금이 청구됩니다.
* **AWS EBS (스토리지)**: 클라우드에 데이터를 저장하는 양에 따라 매달 고정 비용이 발생합니다.
* **AWS S3 연동**: Nextcloud의 기본 저장소를 S3로 설정할 경우, 데이터 업로드/다운로드 트래픽(Data Transfer Out) 및 스토리지 용량에 따른 **유료 과금**이 발생합니다.
* **고정 IP (Elastic IP)**: 인스턴스에 연결되지 않은 탄력적 IP를 보유하거나 특정 조건을 벗어날 경우 소액의 요금이 부과됩니다.

---

## 5. SSL 보안 설정 (HTTPS)

실제 운영 환경에서는 보안을 위해 HTTPS 적용이 필수입니다. 이를 위해 **Nginx Proxy Manager**나 **Traefik** 같은 리버스 프록시를 앞에 두는 것을 권장합니다. 8080 포트를 직접 노출하는 것은 내부 테스트용으로만 사용하는 것이 좋습니다.

---

**Next Step:** SSL 인증서 적용(HTTPS), 외부 스토리지(S3) 연동, 성능 최적화(Redis)
