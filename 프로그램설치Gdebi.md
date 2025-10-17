GUI 환경에서 Google Chrome 설치 파일을 사용하는 방법은 CLI 설치 명령을 사용하지 않고, 마치 Windows에서 설치 파일을 실행하는 것과 유사하게 진행할 수 있습니다.

Kali Linux는 데비안(Debian) 계열이므로, 설치 파일(`.deb`)을 처리하는 데 **Gdebi**라는 GUI 도구를 사용하는 것이 가장 쉽고 확실합니다.

-----

## 💡 GUI 패키지 설치 도구 사용 방법: Gdebi 활용

`.deb` 파일은 압축 파일이 아니라 패키지 설치 파일입니다. GUI 환경에서 이 파일을 더블 클릭하여 설치하려면 **Gdebi**와 같은 전용 도구가 필요합니다.

### 단계 1: Gdebi 패키지 설치 (CLI 사용)

Gdebi는 한 번만 설치하면 됩니다. 이 도구가 없다면 터미널에서 설치해야 합니다.

```bash
# Gdebi 설치
sudo apt update
sudo apt install gdebi -y
```

-----

### 단계 2: Chrome `.deb` 파일 다운로드 (GUI 사용)

GUI 브라우저(예: Firefox)를 열고 다음 주소에서 Chrome 설치 파일을 다운로드합니다.

1.  Firefox를 실행합니다.
2.  Google Chrome 다운로드 페이지로 이동합니다.
3.  \*\*"Chrome 다운로드"\*\*를 클릭하고, \*\*`64비트 .deb (Debian/Ubuntu)`\*\*를 선택하여 다운로드합니다.
4.  파일은 보통 **`Downloads`** 폴더에 저장됩니다.

-----

### 단계 3: Gdebi를 이용한 설치 (GUI 실행)

1.  파일 관리자(File Manager)를 엽니다.
2.  다운로드한 **`google-chrome-stable_current_amd64.deb`** 파일을 찾습니다.
3.  **파일을 더블 클릭**합니다. (Gdebi가 설치되어 있다면 자동으로 Gdebi 창이 열립니다.)
4.  Gdebi 창에서 **"설치(Install Package)"** 버튼을 클릭합니다.
5.  시스템 비밀번호를 입력하여 권한을 부여합니다.
6.  Gdebi가 설치를 진행하고, 필요한 모든 의존성 파일(Dependencies)까지 자동으로 처리해 줍니다.

설치가 완료되면, 애플리케이션 메뉴에서 Chrome 아이콘을 찾아 실행할 수 있습니다.

**실전 팁:** $\mathrm{Gdebi}$를 사용하면 패키지 설치 전에 **누락된 의존성 파일**을 명확하게 보여주기 때문에, $\mathrm{dpkg}$나 $\mathrm{apt}$ 명령어로 설치할 때 발생하는 복잡한 오류를 피할 수 있어 GUI 사용자에게 매우 유용합니다.
