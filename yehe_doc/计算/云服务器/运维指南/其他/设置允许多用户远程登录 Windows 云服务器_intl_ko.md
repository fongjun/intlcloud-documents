## 작업 시나리오
본 문서는 Windows Server 2012 R2 운영 체제 CVM을 예로 들어 여러 사용자가 Windows CVM에 원격 로그인하도록 설정하는 방법을 안내합니다.

## 작업 순서
### 원격 데스크톱 서비스 추가
1. Windows CVM에 로그인합니다.
2. 운영 체제 인터페이스에서 <img src="https://main.qcloudimg.com/raw/f779581f1ce3edfead8c725ce1504009.png" style="margin: 0;"></img>를 클릭하고 "서버 관리자"를 엽니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/66bb5237846f1dd79e3145bfd82d9257.png)
3. [역할 및 기능 추가]를 클릭하면 "역할 및 기능 추가 마법사" 창이 팝업 됩니다.
4. "역할 및 기능 추가 마법사" 창에서 기본 매개변수를 유지하고 세 번 연속 [다음]을 클릭합니다.
5. "서버 역할" 인터페이스에서 [원격 데스크톱 서비스]를 선택하고 [다음]을 클릭합니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/54d329c2667ac5c60ffdc2b74f1fc555.png)
6. 기본 매개변수를 유지하고 [다음]을 더블 클릭합니다.
7. "역할 서비스" 인터페이스에서 [원격 데스크톱 세션 호스트]를 선택합니다. 아래 이미지 참조
"원격 데스크톱 세션 호스트에 필요한 기능 추가" 창이 팝업 됩니다.
![](https://main.qcloudimg.com/raw/8d24fd515bd363dc020257c2843c5562.png)
8. "원격 데스크톱 세션 호스트에 필요한 기능 추가" 창에서 [기능 추가]를 클릭합니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/2a33d896c16b1d98012536cdc3776248.png)
9. "역할 서비스" 인터페이스에서 [원격 데스크톱 라이선싱]을 선택합니다. 아래 이미지 참조
"원격 데스크톱 라이선싱에 필요한 기능 추가" 창이 팝업 됩니다.
![](https://main.qcloudimg.com/raw/1c908dc77f50488387a2fdbfda08ba35.png)
10. "원격 데스크톱 라이선싱에 필요한 기능 추가" 창에서 [기능 추가]를 클릭합니다.
![](https://main.qcloudimg.com/raw/d7aa066366b168ac8a7475155d34ea19.png)
11. [다음]을 클릭합니다.
12. [필요한 경우 자동으로 타깃 서버 자동 다시 시작]을 선택하고 팝업된 창에서 [예]를 클릭합니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/df280b0a66470be404f114bd17c47d21.png)
13. [설치]를 클릭하고 원격 데스크톱 서비스 설치가 완료될 때까지 기다립니다.

### 다중 사용자의 원격 로그인 인스턴스 구성
1. [VNC로 Windows CVM에 로그인합니다].
2. 운영 체제 인터페이스에서 <img src="https://main.qcloudimg.com/raw/f0c84862ef30956c201c3e7c85a26eec.png" style="margin: 0;"></img>를 클릭하여 Windows PowerShell 창을 엽니다.
3. Windows PowerShell 창에서 **gpedit.msc**를 입력한 후 **Enter**를 눌러 "로컬 그룹 정책 편집기"를 엽니다.
4. 왼쪽 메뉴에서 [컴퓨터 구성]>[관리 템플릿]>[Windows 구성 요소]>[터미널 서비스]>[원격 데스크톱 세션 호스트]>[연결]을 선택하고 [연결 개수 제한]을 더블 클릭하여 엽니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/e0420d2bb8ddd3e1524ee688173cb9d1.png)
5. 팝업된 "연결 개수 제한" 창에서 [사용]을 선택하고 [RD 최대 허용 연결]에 최대 동시 원격 사용자 수를 입력합니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/066c9dfb06dc4c092424c4e1142f7471.png)
6. [확인]을 클릭합니다.
7. 왼쪽 메뉴에서 [컴퓨터 구성]>[관리 템플릿]>[Windows 구성 요소]>[터미널 서비스]>[원격 데스크톱 세션 호스트]>[연결]을 선택하고 [원격 데스크톱 서비스 사용자를 하나의 원격 데스크톱 서비스 세션으로 제한]을 더블 클릭하여 엽니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/1183e9f4c6c08b6f99746db42b0d183e.png)
8. 팝업된 "원격 데스크톱 서비스 사용자를 하나의 원격 데스크톱 서비스 세션으로 제한" 창에서 [사용 안 함]을 선택하고 [확인]을 클릭합니다. 아래 이미지 참조
![](https://main.qcloudimg.com/raw/56d910ea359024d34dc05de3a274c91a.png)
9. 로컬 그룹 정책 편집기를 닫습니다.
10. 인스턴스를 재시작합니다.



