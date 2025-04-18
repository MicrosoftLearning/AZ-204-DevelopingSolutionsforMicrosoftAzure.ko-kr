---
lab:
  title: Visual Studio Code에서 Azure Function 만들기
  description: Azure Function 및 HTTP 트리거를 만드는 방법을 알아봅니다. Visual Studio Code에서 로컬로 코드를 만들고 테스트한 후 Azure에 함수를 배포합니다.
---

# Visual Studio Code에서 Azure Function 만들기

이 연습에서는 HTTP 요청에 응답하는 C\# 함수를 만드는 방법을 알아봅니다. Visual Studio Code에서 로컬로 코드를 만들고 테스트한 후 Azure에 함수를 배포합니다.

이 연습에서 수행된 작업:

* 컨테이너화 앱에 대한 Azure App Service 리소스 만들기
* 결과 보기
* 리소스 정리

이 연습을 완료하는 데 약 **30**분 정도 소요됩니다.

## 시작하기 전에

시작하려면 먼저 다음 요구 사항을 충족하는지 확인하세요.

* Azure 구독 아직 계정이 없다면 [등록](https://azure.microsoft.com/)할 수 있습니다.

* [Azure Functions Core Tools 버전 4.x](https://github.com/Azure/azure-functions-core-tools#installing)

* [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나인 [Visual Studio Code](https://code.visualstudio.com/).

* [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) 대상 프레임워크입니다.

* Visual Studio Code용 [C# Dev Kit](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit).

* Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## 로컬 프로젝트 만들기

이 섹션에서는 Visual Studio Code를 사용하여 C#에서 로컬 Azure Functions 프로젝트를 만듭니다. 이 연습 후반에 함수 코드를 Azure에 게시합니다.

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 열고 명령 `Azure Functions: Create New Project...`을(를) 검색하고 실행합니다.

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택**을 선택합니다. 새 폴더를 만들거나 프로젝트 작업 영역에 대한 빈 폴더를 선택해야 합니다. 이미 작업 영역의 일부인 프로젝트 폴더를 선택하지 마세요.

1. 프롬프트에서 다음 정보를 제공합니다.

    | 프롬프트 | 작업 |
    |--|--|
    | 함수 프로젝트를 포함할 폴더를 선택합니다. | **찾아보기**를 선택하여 앱에 대한 폴더를 선택합니다.
    | 언어 선택 | **C#** 을 선택합니다. |
    | .NET 런타임 선택 | **.NET 8.0 Isolated**를 선택합니다. |
    | 프로젝트의 첫 번째 함수에 사용할 템플릿 선택 | **HTTP 트리거**를 선택합니다.<sup>1</sup> |
    | 함수 이름 제공 | `HttpExample`를 입력합니다. |
    | 네임스페이스 입력 | `My.Function`를 입력합니다. |
    | 권한 부여 수준 | 누구나 함수 엔드포인트를 호출할 수 있도록 하는 **익명**을 선택합니다. |

    <sup>1</sup> VS Code 설정에 따라 **템플릿 필터 변경** 옵션을 사용하여 템플릿의 전체 목록을 확인해야 할 수 있습니다.

1. Visual Studio Code는 제공된 정보를 사용하고 HTTP 트리거를 통해 Azure Functions 프로젝트를 생성합니다. 탐색기에서 로컬 프로젝트 파일을 볼 수 있습니다.

### 로컬에서 함수 실행

Visual Studio Code는 Azure Functions Core 도구와 통합되어 Azure에 게시하기 전에 로컬 개발 컴퓨터에서 이 프로젝트를 실행할 수 있습니다.

1. Visual Studio Code로 터미널이 열려 있는지 확인합니다. **터미널**을 선택한 다음 메뉴 모음에서 **새 터미널**을 선택하여 터미널을 열 수 있습니다. 

1. **F5** 키를 눌러 디버거에서 함수 앱 프로젝트를 시작합니다. 핵심 도구의 출력이 **터미널** 패널에 표시됩니다. **터미널** 패널에서 앱이 시작됩니다. 로컬에서 실행 중인 HTTP 트리거 함수의 URL 엔드포인트를 볼 수 있습니다.

    ![HTTP 트리거 함수의 엔드포인트의 스크린샷이 터미널 패널에 표시됩니다.](./media/06/run-function-local.png)

1. Core Tools가 실행 중인 상태에서 **Azure: Functions 영역**으로 이동합니다. **Functions**에서 **로컬 프로젝트** > **Functions**를 확장합니다. **HttpExample** 함수를 마우스 오른쪽 단추로 클릭하고 **지금 함수 실행**을 선택합니다.

    ![지금 함수 실행 단계를 보여주는 스크린샷.](./media/06/execute-function-local.png)

1. **Enter request body**에 `{ "name": "Azure" }`의 요청 메시지 본문 값을 입력합니다. **Enter**를 눌러 이 요청 메시지를 함수로 보냅니다. 함수가 로컬로 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다.

    알림을 보려면 알림 벨 아이콘을 선택합니다. 기능 실행에 대한 정보는 **터미널** 패널에 표시됩니다.

1. **Shift + F5** 키를 눌러 Core 도구를 중지하고 디버거 연결을 끊습니다.

함수가 로컬 컴퓨터에서 올바르게 실행되는지 확인한 후에는 Visual Studio Code를 사용하여 프로젝트를 Azure에 직접 게시해야 합니다.

## Azure에서 함수 배포 및 실행

이 섹션에서는 Azure Function App을 만들고 리소스에 함수를 배포합니다.

### Azure에 로그인

앱을 게시하기 전에 Azure에 로그인해야 합니다. 이미 로그인한 경우 다음 섹션으로 이동합니다.

1. 아직 로그인하지 않은 경우 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **Azure에 로그인...** 을 선택합니다.

    ![Azure 로그인 단추의 스크린샷.](./media/06/functions-sign-into-azure.png)

1. 브라우저에 메시지가 표시되면 Azure 계정을 선택하고 Azure 계정 자격 증명을 사용하여 로그인합니다.

1. 성공적으로 로그인하면 새 브라우저 창을 닫을 수 있습니다. Azure 계정에 속하는 구독은 사이드바에 표시됩니다.

### Azure에서 리소스 만들기

이 섹션에서는 로컬 함수 앱을 배포하는 데 필요한 Azure 리소스를 만듭니다.

1. 작업 막대에서 Azure 아이콘을 선택한 다음, **리소스** 영역에서 **리소스 만들기...** 버튼을 선택합니다.

    ![리소스 만들기 단추의 스크린샷.](./media/06/create-resource.png)    

1. 프롬프트에서 다음 정보를 제공합니다.

    | 프롬프트 | 작업 |
    |--|--|
    | 모니터링할 리소스 선택 | **Azure...에서 함수 앱 만들기**를 선택합니다. |
    | 구독 선택 | 사용할 구독을 선택합니다. *구독이 한 개만 있으면 이 메시지가 표시되지 않습니다.* |
    | 함수 앱에 대해 전역적으로 고유한 이름 입력 | URL 경로에 유효한 이름을 입력합니다(예: `myfunctionapp`). 입력한 이름은 고유한지 확인하기 위해 유효성을 검사합니다. |
    | 새 리소스의 위치 선택 | 성능을 향상시키려면 가까운 지역을 선택합니다. |
    | 런타임 스택 선택 | **.NET 8.0이 격리됨**을 선택합니다. |
    | 인스턴스 메모리 크기를 선택합니다. | **2048 Default**를 선택합니다. |
    | 최대 인스턴스 수를 선택합니다. | 기본값 **100**을 적용합니다. |

    확장은 터미널 창의 **AZURE** 영역에서 생성되는 개별 리소스의 상태를 보여줍니다.
    
1. 완료되면 함수 앱 이름을 기반으로 하는 이름을 사용하여 구독에 다음 Azure 리소스가 생성됩니다.

    * 리소스 그룹 - 관련 리소스에 대한 논리 컨테이너입니다.
    * 표준 Azure Storage 계정 - 프로젝트에 대한 상태 및 기타 정보를 유지 관리합니다.
    * Flex 사용 계획은 서버리스 함수 앱의 기본 호스트를 정의합니다.
    * 함수 앱 - 함수 코드를 실행할 수 있는 환경을 제공합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 동일한 호스팅 계획 내에서 더 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다.
    * 함수 앱에 연결된 Application Insights 인스턴스 - 서버리스 함수의 사용을 추적합니다.

### Azure에 프로젝트 배포

> **중요:** 기존 함수에 게시하면 이전 배포를 덮어씁니다.

1. 명령 팔레트에서 **Azure Functions: 함수 앱에 배포** 명령을 검색한 다음 실행합니다.

1. 리소스를 만드는 데 사용한 구독을 선택합니다.

1. 만든 함수 앱을 선택합니다. 이전 배포를 덮어쓰라는 메시지가 표시되면 **배포**를 선택하여 함수 코드를 새 함수 앱 리소스에 배포합니다.

1. 배포가 완료되면 **출력 보기**를 선택하여 배포 결과의 세부 정보를 확인합니다. 알림이 누락된 경우 오른쪽 아래 모서리에 있는 종 모양의 알림 아이콘을 선택하여 다시 확인합니다.

    ![출력 보기 단추의 스크린샷.](./media/06/function-view-output.png)

### Azure에서 함수 실행

1. 사이드바의 **리소스** 영역으로 돌아가서 구독, 새 함수 앱 및 **함수**를 확장합니다. **HttpExample** 함수를 **마우스 오른쪽 단추로 클릭**하고 **지금 함수 실행**을 선택합니다.

    ![지금 함수 실행 옵션의 스크린샷.](./media/06/execute-function-remote.png)

1. **요청 본문 입력**에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다.

1. 함수가 Azure에서 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다. 알림을 보려면 알림 벨 아이콘을 선택합니다.

## 리소스 정리

연습을 마쳤으므로 불필요한 리소스 사용을 방지하기 위해 만든 클라우드 리소스를 삭제해야 합니다.

1. 브라우저에서 Azure Portal[https://portal.azure.com](https://portal.azure.com)로 이동한 다음, 메시지가 나타나면 Azure 자격 증명을 사용하여 로그인합니다.
1. 만든 리소스 그룹으로 이동하여 이 연습에 사용된 리소스의 내용을 봅니다.
1. 도구 모음에서 **리소스 그룹 삭제**를 선택합니다.
1. 리소스 그룹 이름을 입력하고 삭제할 것인지 확인합니다.
