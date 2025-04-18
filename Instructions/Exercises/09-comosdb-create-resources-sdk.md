---
lab:
  title: '.NET을 사용하여 Azure Cosmos DB for NoSQL에서 리소스 '
  description: Microsoft .NET SDK v3을 사용하여 Azure Cosmos DB에서 데이터베이스 및 컨테이너 리소스를 만드는 방법을 알아봅니다.
---

# .NET을 사용하여 Azure Cosmos DB for NoSQL에서 리소스 

이 연습에서는 Azure Cosmos DB에서 컨테이너, 데이터베이스 및 항목을 만드는 콘솔 앱을 만듭니다.

이 연습에서 수행된 작업:

* Azure Cosmos DB 계정 만들기
* 콘솔 앱 만들기
* 콘솔 앱 실행 및 결과 확인

이 연습을 완료하는 데 약 **30**분 정도 소요됩니다.

## 시작하기 전에

시작하려면 먼저 다음 요구 사항을 충족하는지 확인하세요.

* Azure 구독 아직 계정이 없다면 [등록](https://azure.microsoft.com/)할 수 있습니다.

## Azure Cosmos DB 계정 만들기

이 연습 섹션에서는 리소스 그룹 및 Azure Cosmos DB 계정을 만듭니다. 또한 계정에 대한 엔드포인트 및 액세스 키를 기록합니다.

1. 브라우저에서 Azure Portal[https://portal.azure.com](https://portal.azure.com)로 이동한 다음, 메시지가 나타나면 Azure 자격 증명을 사용하여 로그인합니다.

1. 페이지 상단의 검색 창 오른쪽에 있는 **[\>_]** 단추를 사용하여 Azure Portal에서 새 Cloud Shell을 만들고 ***Bash*** 환경을 선택합니다. Cloud Shell은 다음과 같이 Azure Portal 아래쪽 창에 명령줄 인터페이스를 제공합니다.

    > **참고**: 이전에 *PowerShell* 환경을 사용하는 Cloud Shell을 만든 경우 ***Bash***로 전환합니다.

1. Cloud Shell 도구 모음의 **설정** 메뉴에서 **클래식 버전으로 이동**을 선택합니다(코드 편집기를 사용하는 데 필요).

1. 이 연습에 필요한 리소스에 대한 리소스 그룹을 만듭니다. **\<myResourceGroup>** 을 사용할 리소스 그룹의 이름으로 바꿉니다. 필요한 경우 **useast**를 가까운 지역으로 바꿀 수 있습니다. 

    ```
    az group create --location useast --name <myResourceGroup>
    ```

1. 다음 명령을 실행하여 Azure Cosmos DB 계정을 만듭니다. Azure Cosmos DB 계정을 식별할 수 있는 **\<myCosmosDBacct>** 를 *고유한* 이름으로 바꿉니다. **\<myResourceGroup>** 을 이전에 선택한 이름으로 바꿉니다.

    >**참고:** 계정 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다. 3~31자여야 합니다. 이 명령은 완료되는 데 몇 분 정도 걸립니다.

    ```
    az cosmosdb create -n <myCosmosDBacct> -g <myResourceGroup>
    ```

1.  다음 명령을 실행하여 Azure Cosmos DB 계정에 대한 **documentEndpoint**를 검색합니다. 명령 결과에서 엔드포인트를 기록합니다. 연습의 뒷부분에서 사용됩니다. **\<myCosmosDBacct>** 및 **\<myResourceGroup>** 을 만든 이름으로 바꿉니다.

    ```bash
    az cosmosdb show -n <myCosmosDBacct> -g <myResourceGroup> --query "documentEndpoint" --output tsv
    ```

1. 다음 명령을 사용하여 계정의 기본 키를 검색합니다. 코드에 사용할 명령 결과에서 **primaryMasterKey**를 기록합니다. **\<myCosmosDBacct>** 및 **\<myResourceGroup>** 을 만든 이름으로 바꿉니다.

     ```
    # Retrieve the primary key
    az cosmosdb keys list -n <myCosmosDBacct> -g <myResourceGroup>
    ```

## 콘솔 애플리케이션 만들기

이제 필요한 리소스가 Azure에 배포되었으므로 다음 단계는 Visual Studio Code에서 동일한 터미널 창을 사용하여 콘솔 애플리케이션을 설정하는 것입니다.

1. 프로젝트에 대한 폴더를 만들고 폴더로 변경합니다.

    ```bash
    mkdir cosmosdb
    cd cosmosdb
    ```

1. .NET 콘솔 앱을 만듭니다.

    ```dotnetcli
    dotnet new console --framework net8.0
    ```

1. 다음 명령을 실행하여 프로젝트에 **Microsoft.Azure.Cosmos** 패키지 및 지원 **Newtonsoft.Json** 패키지를 추가합니다.

    ```dotnetcli
    dotnet add package Microsoft.Azure.Cosmos --version 3.*
    dotnet add package Newtonsoft.Json --version 13.*
    ```

이제 Cloud Shell의 편집기를 사용하여 **Program.cs** 파일의 템플릿 코드를 바꿔야 합니다.

### 프로젝트의 시작 코드 추가

1. 애플리케이션을 편집하려면 Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    code Program.cs
    ```

1. **using** 문 다음에 나오는 기존 코드를 다음 코드 조각으로 바꿉니다. 코드 주석의 지침에 따라 **\<documentEndpoint>** 및 **\<primaryKey>** 에 대한 자리 표시자 값을 대체해야 합니다.

    이 코드는 앱의 전체 구조와 필요한 몇 가지 요소를 제공합니다. 코드의 메모를 검토하고 지침에 지정된 영역에 코드를 추가합니다. 

    ```csharp
    using Microsoft.Azure.Cosmos;
    
    namespace CosmosExercise
    {
        // This class represents a product in the Cosmos DB container
        public class Product
        {
            public string? id { get; set; }
            public string? name { get; set; }
            public string? description { get; set; }
        }
    
        public class Program
        {
            // Cosmos DB account URL - replace with your actual Cosmos DB account URL
            static string cosmosDbAccountUrl = "<documentEndpoint>";
    
            // Cosmos DB account key - replace with your actual Cosmos DB account key
            static string accountKey = "<primaryKey>";
    
            // Name of the database to create or use
            static string databaseName = "myDatabase";
    
            // Name of the container to create or use
            static string containerName = "myContainer";
    
            public static async Task Main(string[] args)
            {
                // Create the Cosmos DB client using the account URL and key
    
    
                try
                {
                    // Create a database if it doesn't already exist
    
    
                    // Create a container with a specified partition key
    
    
                    // Define a typed item (Product) to add to the container
    
    
                    // Add the item to the container
                    // The partition key ensures the item is stored in the correct partition
    
    
                }
                catch (CosmosException ex)
                {
                    // Handle Cosmos DB-specific exceptions
                    // Log the status code and error message for debugging
                    Console.WriteLine($"Cosmos DB Error: {ex.StatusCode} - {ex.Message}");
                }
                catch (Exception ex)
                {
                    // Handle general exceptions
                    // Log the error message for debugging
                    Console.WriteLine($"Error: {ex.Message}");
                }
            }
        }
    }
    ```

### 코드를 추가하여 클라이언트를 만들고 작업 수행하기 

이 연습의 섹션에서는 프로젝트의 지정된 영역에 코드를 추가하여 클라이언트, 데이터베이스, 컨테이너를 만들고 컨테이너에 샘플 항목을 추가합니다.

1. **// Create the Cosmos DB client using the account URL and key** 주석 다음에 있는 입력란에 다음 코드를 추가합니다. 이 코드는 Azure Cosmos DB 계정에 연결하는 데 사용되는 클라이언트를 정의합니다.

    ```csharp
    CosmosClient client = new(
        accountEndpoint: cosmosDbAccountUrl,
        authKeyOrResourceToken: accountKey
    );
    ```

    >참고: *Azure Identity* 라이브러리에서 **DefaultAzureCredential**을 사용하는 것이 가장 좋습니다. 이렇게 하려면 구독 설정 방법에 따라 Azure에서 몇 가지 추가 구성 요구 사항이 필요할 수 있습니다. 

1. **// Create a database if it doesn't already exist** 주석 다음에 있는 입력란에 다음 코드를 추가합니다. 

    ```csharp
    Microsoft.Azure.Cosmos.Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
    Console.WriteLine($"Created or retrieved database: {database.Id}");
    ```

1. **지정된 파티션 키가 있는 컨테이너 만들기** 주석 다음에 있는 입력란에 다음 코드를 추가합니다. 

    ```csharp
    Microsoft.Azure.Cosmos.Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
    Console.WriteLine($"Created or retrieved database: {database.Id}");
    ```

1. **컨테이너에 추가할 입력된 항목(상품) 정의하기** 주석 다음에 있는 입력란에 다음 코드를 추가합니다. 이는 컨테이너에 추가되는 항목을 정의합니다.

    ```csharp
    Product newItem = new Product
    {
        id = Guid.NewGuid().ToString(), // Generate a unique ID for the product
        name = "Sample Item",
        description = "This is a sample item in my Azure Cosmos DB exercise."
    };
    ```

1. **컨테이너에 항목 추가하기** 주석 다음에 있는 입력란에 다음 코드를 추가합니다. 

    ```csharp
    ItemResponse<Product> createResponse = await container.CreateItemAsync(
        item: newItem,
        partitionKey: new PartitionKey(newItem.id)
    );
    ```

1. 이제 코드가 완료되었으므로 진행률을 저장하고 **Ctrl + q**를 사용하여 파일을 저장하고 **Ctrl + q**를 사용하여 편집기를 종료합니다.

1. Cloud Shell에서 다음 명령을 실행하여 프로젝트의 오류를 테스트합니다. 오류가 표시되면 편집기 에서 *Program.cs* 파일을 열고 누락된 코드 또는 붙여넣기 오류를 확인합니다.

    ```bash
    dotnet build
    ```

이제 프로젝트가 완료되었으므로 애플리케이션을 실행하고 Azure Portal에서 결과를 확인해야 합니다.

## 애플리케이션을 실행하고 결과를 확인합니다.

1. Cloud Shell에서 `dotnet run` 명령을 실행합니다. 출력은 다음 예시와 비슷해야 합니다.

    ```
    Created or retrieved database: myDatabase
    Created or retrieved container: myContainer
    Created item: c549c3fa-054d-40db-a42b-c05deabbc4a6
    Request charge: 6.29 RUs
    ```

1. Azure Portal에서 이전에 만든 Azure Cosmos DB 리소스로 이동합니다. 왼쪽 탐색에서 **Data Explorer**를 선택합니다. **Data Explorer**에서 **myDatabase**를 선택한 다음, **myContainer**를 확장합니다. **Items**을 선택하여 만든 항목을 볼 수 있습니다.

    ![Data Explorer에서 Items의 위치를 보여주는 스크린샷.](./media/09/cosmos-data-explorer.png)

## 리소스 정리

연습을 마쳤으므로 불필요한 리소스 사용을 방지하기 위해 만든 클라우드 리소스를 삭제해야 합니다.

1. 만든 리소스 그룹으로 이동하여 이 연습에 사용된 리소스의 내용을 봅니다.
1. 도구 모음에서 **리소스 그룹 삭제**를 선택합니다.
1. 리소스 그룹 이름을 입력하고 삭제할 것인지 확인합니다.
