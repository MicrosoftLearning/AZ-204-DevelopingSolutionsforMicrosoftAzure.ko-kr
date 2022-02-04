# 랩 가상 머신 설정

## 설치된 소프트웨어

| 소프트웨어 | 링크 |
| --- | --- |
| Windows 10 (Build 2004) | <https://www.microsoft.com/software-download/windows10> |
| Visual Studio Code | <https://code.visualstudio.com> |
| Visual Studio Code Azure 계정 확장 | <https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account> |
| Visual Studio Code Azure Functions 확장 | <https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions> |
| Visual Studio Code Azure Resource Manager Tools 확장 | <https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools> |
| Visual Studio Code Azure CLI Tools 확장 | <https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli> |
| Visual Studio Code PowerShell 확장 | <https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell> |
| Visual Studio Code C# 확장 | <https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp> |
| PowerShell 7 | <https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3> |
| .NET Core 3.1 SDK | <https://dotnet.microsoft.com/download/dotnet-core/3.1> |
| Azure PowerShell | <https://docs.microsoft.com/powershell/azure/install-az-ps> |
| Azure CLI | <https://docs.microsoft.com/cli/azure/install-azure-cli> |
| Azure Storage Explorer | <https://azure.microsoft.com/features/storage-explorer> |
| .NET 도구 - HttpRepl | <https://github.com/dotnet/HttpRepl> |
| Azure Functions Core Tools | <https://docs.microsoft.com/azure/azure-functions/functions-run-local#v3> |
| Windows Terminal | <https://aka.ms/terminal> |
| Edge(Chromium) | <https://www.microsoft.com/edge> |

## 기타 고려 사항

- ClearType 사용
  
- Microsoft Edge를 기본 브라우저로 구성

- VSCode 구성 업데이트

  ```json
  {
    "editor.fontFamily": "'Cascadia Code', Consolas, 'Courier New', monospace",
    "update.enableWindowsBackgroundUpdates": false,
    "update.mode": "manual",
    "terminal.integrated.shell.windows": "C:\\Program Files\\PowerShell\\7\\pwsh.exe",
    "workbench.startupEditor": "none",
    "terminal.integrated.rendererType": "dom",
    "csharp.suppressDotnetInstallWarning": true,
    "csharp.suppressDotnetRestoreNotification": true,
    "csharp.supressBuildAssetsNotification": true,
    "azureFunctions.showProjectWarning": false
  }
  ```

- Windows 터미널 구성 업데이트

  ```json
  {
    "$schema": "https://aka.ms/terminal-profiles-schema",
    "defaultProfile": "{574e775e-4f2a-5b96-ac1e-a2962a402336}",
    "profiles": [
      {
        "guid": "{574e775e-4f2a-5b96-ac1e-a2962a402336}",
        "useAcrylic": true,
        "acrylicOpacity": 0.85,
        "colorScheme": "Campbell",
        "fontFace": "Cascadia Code",
        "hidden": false,
        "name": "PowerShell",
        "source": "Windows.Terminal.PowershellCore"
      },
      {
        "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b8}",
        "hidden": false,
        "name": "Azure Cloud Shell",
        "source": "Windows.Terminal.Azure"
      }
    ],
    "schemes": [],
    "keybindings": []
  }
  ```

- 다음 아이콘만 포함되도록 시작 메뉴 및 작업 표시줄 구성:
  - 파일 탐색기
  - Edge
  - Windows Terminal
  - Visual Studio Code
  - Azure Storage Explorer

- PowerShell 7 업데이트 알림을 사용하지 않도록 설정

  1. ``POWERSHELL_UPDATECHECK`` [환경 변수 만들기](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_update_notifications?view=powershell-7)
  
  1. 환경 변수 값을 ``Off``로 설정(대/소문자 구분)

- Azure Functions Core Tools를 한 번 이상 실행하여 Windows 방화벽 구성

  ```bash
  func init test --worker-runtime dotnet
  cd test
  func new --template 'HTTP trigger' --name web
  func start --build
  ```
