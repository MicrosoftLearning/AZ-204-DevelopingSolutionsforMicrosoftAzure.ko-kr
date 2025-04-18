---
lab:
  title: Azure App Service의 배포 슬롯 교환
  description: Azure App Service에서 배포 슬롯을 교환하는 방법을 알아봅니다. 이 연습에서는 App Service에 간단한 앱을 배포합니다. 앱을 약간 변경하고 스테이징 슬롯에 배포합니다. 마지막으로 업데이트된 앱이 프로덕션에 있도록 슬롯을 교환합니다.
---

# Azure App Service의 배포 슬롯 교환

이 연습에서는 Azure CLI **az webapp up** 명령을 사용하여 Azure App Service에 기본 HTML+CSS 사이트를 배포합니다. 다음으로 코드를 업데이트하고 스테이징 슬롯에 변경 사항을 배포합니다. 마지막으로 슬롯을 교환합니다.

이 연습에서 수행된 작업:

* 샘플 앱을 다운로드한 다음, Azure App Service에 배포합니다.
* 스태이징 배포 슬롯을 만듭니다.
* 샘플 앱을 변경하고 스테이징 슬롯에 배포합니다.
* 스테이징 및 기본 프로덕션 슬롯을 교환하여 변경 내용을 프로덕션 슬롯으로 이동합니다.

이 연습을 완료하는 데 약 **20**분 정도 소요됩니다.

## 시작하기 전에

연습을 완료하려면 다음 사항이 필요합니다.

* Azure 구독 아직 계정이 없다면 등록할 수 있습니다([https://azure.microsoft.com/](https://azure.microsoft.com/)).

## 샘플 앱 다운로드 및 배포

이 섹션에서는 샘플 앱을 다운로드하고 변수를 설정하여 명령을 쉽게 입력하도록 만든 다음, Azure App Service 리소스를 만들고 Azure CLI 명령을 사용하여 정적 HTML 사이트를 배포합니다.

1. 브라우저에서 Azure Portal[https://portal.azure.com](https://portal.azure.com)로 이동한 다음, 메시지가 나타나면 Azure 자격 증명을 사용하여 로그인합니다.

1. 페이지 상단의 검색 창 오른쪽에 있는 **[\>_]** 단추를 사용하여 Azure Portal에서 새 Cloud Shell을 만들고 ***Bash*** 환경을 선택합니다. Cloud Shell은 다음과 같이 Azure Portal 아래쪽 창에 명령줄 인터페이스를 제공합니다.

    > **참고**: 이전에 *PowerShell* 환경을 사용하는 Cloud Shell을 만든 경우 ***Bash***로 전환합니다.

1. Cloud Shell 도구 모음의 **설정** 메뉴에서 **클래식 버전으로 이동**을 선택합니다(코드 편집기를 사용하는 데 필요).

1. 다음 **Git** 명령을 실행하여 샘플 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure-Samples/html-docs-hello-world.git
    ```

1. 다음 명령을 실행하여 리소스 그룹 및 앱 이름을 포함하도록 변수를 설정합니다. 명령 실행 후 표시되는 **appName** 값을 기록해 둡니다. 이 연습의 뒷부분에서 필요합니다.

    ```bash
    resourceGroup=rg-mywebapp

    appName=mywebapp$RANDOM
    echo $appName
    ```

1. 샘플 코드가 들어있는 디렉터리로 이동한 후 **az webapp up** 명령을 실행합니다. **참고:** 이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다.

    ```bash
    cd html-docs-hello-world

    az webapp up -g $resourceGroup -n $appName --sku P0V3 --html
    ```

    이제 배포가 완료되었으므로 이제 웹앱을 검토해야 합니다.

1. Azure Portal에서 배포한 웹앱으로 이동합니다. 앞서 기록한 이름을 **리소스, 서비스 및 문서 검색(G + /)** 검색 창에 입력한 다음, 목록에서 리소스를 선택할 수 있습니다.

1. **Essentials** 섹션의 **기본 도메인** 필드에 있는 웹앱 링크를 선택합니다. 이 링크는 새 탭에서 사이트를 엽니다.

## 배포 슬롯에 업데이트된 코드 배포

이 섹션에서는 배포 슬롯을 만들고, 앱에서 HTML을 수정하고, 업데이트된 코드를 새 배포 슬롯에 배포합니다.

### 배포 슬롯 만들기 

1. Azure Portal 및 Cloud Shell을 사용하여 탭으로 돌아갑니다.

1. Cloud Shell에 다음 명령을 입력하여 *staging*이라는 배포 슬롯을 만듭니다.

    ```bash
    az webapp deployment slot create -n $appName -g $resourceGroup --slot staging
    ```

1. 명령이 완료되기를 기다린 다음, 왼쪽 메뉴에서 **배포 > 배포 슬롯**을 선택하여 웹앱의 배포 슬롯을 확인합니다. 새 슬롯의 이름에는 웹앱 이름에 추가된 *-staging*이 포함되어 있습니다.

### 코드를 업데이트하고 스테이징 슬롯에 배포

1. Cloud Shell에서 `code index.html`을 입력하여 편집기를 엽니다. `<h1>` 제목 태그에서 *Azure App Service - 샘플 정적 HTML 사이트*를 *Azure App Service 스테이징 슬롯* 또는 원하는 다른 이름으로 바꿉니다.

1. **ctrl-s** 명령을 사용하여 저장하고, **ctrl-q** 명령을 사용하여 종료합니다.

1. Cloud Shell에서 다음 명령을 실행하여 업데이트된 프로젝트의 zip 파일을 만듭니다. 다음 단계에서는 zip 또는 WAR(웹 애플리케이션 리소스) 파일이 필요합니다.

    ```bash
    zip -r stagingcode.zip .
    ```

1. Cloud Shell에서 다음 명령을 실행하여 스테이징 슬롯에 업데이트를 배포합니다.

    ```bash
    az webapp deploy -g $resourceGroup -n $appName --src-path ./stagingcode.zip --slot staging
    ```

1. 웹앱에서 왼쪽 메뉴에 있는 **배포 > 배포 슬롯**을 선택한 다음, 이전에 만든 스테이징 슬롯을 선택합니다.

1. **Essentials** 섹션의 **기본 도메인** 필드에서 링크를 선택합니다. 링크는 새 탭에서 스테이징 슬롯에 대한 웹 사이트를 엽니다.

## 스테이징 및 프로덕션 슬롯 

도구 모음의 **교환** 옵션을 사용하여 Azure Portal 에서 교환을 수행할 수 있습니다. 왼쪽 메뉴에서 **개요** 또는 **배포 > 배포 슬롯**을 선택하면 **교환** 옵션이 도구 모음에 표시됩니다.

1. Azure Portal의 도구 모음에서 **교환**을 선택하여 **교환** 패널을 엽니다.

1. 교환 패널에서 설정을 검토합니다. **소스**에 **-staging** 슬롯이 표시되고 **대상**에 기본 프로덕션 슬롯이 표시되어야 합니다.

    ![교환 패널의 스크린샷.](./media/02/app-service-swap-panel.png)

1. **교환 시작**을 선택하고 작업이 완료되기를 기다립니다. 포털 맨 위에 있는 벨 아이콘을 선택하면 열리는 **알림** 패널에서 완료를 추적할 수 있습니다.

1. 교환을 확인하려면 배포한 웹앱으로 이동합니다. 이전에 만든 웹앱 이름(예: *mywebapp12360*)을 **리소스, 서비스 및 문서 검색(G + /)** 검색 창에 입력한 다음, 목록에서 리소스를 선택합니다.

1. **Essentials** 섹션의 **기본 도메인** 필드에 있는 웹앱 링크를 선택합니다. 링크는 새 탭에서 사이트(프로덕션 슬롯)를 엽니다.

1. 변경 내용을 확인합니다. 변경 내용을 표시하려면 페이지를 새로 고쳐야 할 수 있습니다.

## 리소스 정리

연습을 마쳤으므로 불필요한 리소스 사용을 방지하기 위해 만든 클라우드 리소스를 삭제해야 합니다.

1. 만든 리소스 그룹으로 이동하여 이 연습에 사용된 리소스의 내용을 봅니다.
1. 도구 모음에서 **리소스 그룹 삭제**를 선택합니다.
1. 리소스 그룹 이름을 입력하고 삭제할 것인지 확인합니다.
