
CVM 스팟 인스턴스 모드는 가격 및 재고 원인으로 시스템의 능동적인 인스턴스 회수가 발생할 수 있습니다. 사용자의 편의를 위해서 인스턴스 회수 전에 일부 사용자 정의 작업을 수행할 수 있도록, 인스턴스 내부에서 Metadata 메커니즘을 통해 회수 상태를 획득할 수 있는 인터페이스를 제공합니다. 자세한 사용 방법은 다음과 같습니다.

## Metadata 설명
인스턴스 메타데이터는 인스턴스를 표시하는 관련 데이터로, 실행 중인 인스턴스를 설정하거나 관리하는 데 활용됩니다. 사용자는 인스턴스 내부를 통해 인스턴스 메타데이터에 액세스하고 획득할 수 있습니다. 자세한 내용은 [인스턴스 메타데이터 조회](http://intl.cloud.tencent.com/document/product/213/4934)를 참조하십시오.


## Metadata를 통해 스팟 인스턴스 회수 상태 정보 획득
cURL 툴 또는 HTTP의 GET 요청을 통해 metadata에 액세스하고 스팟 인스턴스 회수 상태 정보를 획득합니다.
```
curl metadata.tencentyun.com/latest/meta-data/spot/termination-time
```
- 다음과 같은 정보를 리턴하면 스팟 인스턴스 회수 시간을 의미합니다.
```
2018-08-18 12:05:33
```
- 404를 리턴하면 해당 인스턴스가 스팟 인스턴스가 아니거나 회수하지 않았음을 의미합니다.

더 자세한 작업 정보는 [인스턴스 메타데이터 조회](http://intl.cloud.tencent.com/document/product/213/4934)를 참조하십시오.
