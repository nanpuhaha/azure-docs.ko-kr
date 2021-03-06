---
title: Azure 데이터 탐색기 C# SDK를 사용하여 정책 만들기
description: 이 문서에서는 C#을 사용하여 정책을 만드는 방법을 배웁니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667302"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>C를 사용하여 Azure 데이터 탐색기의 데이터베이스 및 테이블 정책 만들기 #

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 이 문서에서는 C#을 사용하여 Azure 데이터 탐색기의 데이터베이스 및 테이블 정책을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio 2019. Visual Studio 2019가 없는 경우 *무료* [Visual Studio 커뮤니티 2019를](https://www.visualstudio.com/downloads/)다운로드하여 사용할 수 있습니다. Visual Studio 를 설정하는 동안 **Azure 개발을** 선택해야 합니다.
* Azure 구독 필요한 경우 시작하기 전에 [무료 Azure 계정을](https://azure.microsoft.com/free/) 만들 수 있습니다.
* [테스트 클러스터 및 데이터베이스](create-cluster-database-csharp.md).
* [테스트 테이블](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>C# NuGet 설치

* Azure [데이터 탐색기(Kusto) NuGet 패키지를](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)설치합니다.
* [Microsoft.Azure.Kusto.Data.NET표준 NuGet 패키지를](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)설치합니다. (테이블 정책을 변경하는 경우 선택 사항입니다.)
* 인증을 위해 [Microsoft.IdentityModel.Client.ActiveDirectory NuGet 패키지를](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)설치합니다.

## <a name="authentication"></a>인증
이 문서의 예제를 실행하려면 리소스에 액세스할 수 있는 Azure Active Directory(Azure AD) 응용 프로그램 및 서비스 주체가 필요합니다. [테스트 클러스터 및 데이터베이스에서](create-cluster-database-csharp.md#authentication)인증에 동일한 Azure AD 응용 프로그램을 사용할 수 있습니다. 다른 Azure AD 응용 프로그램을 사용하려면 [Azure AD 응용 프로그램을 만들어](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 무료 Azure AD 응용 프로그램을 만들고 구독 범위에서 역할 할당을 추가합니다. 이 문서에서는 `Directory (tenant) ID`을 `Application ID`얻는 방법도 `Client secret`보여 주며 , 새 Azure AD 응용 프로그램을 데이터베이스의 보안 주체로 추가해야 할 수 있습니다. 자세한 내용은 [Azure 데이터 탐색기 데이터베이스 사용 권한 관리를](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)참조하십시오.

## <a name="alter-database-retention-policy"></a>데이터베이스 보존 정책 변경
10일의 일시 삭제 기간을 가진 보존 정책을 설정합니다.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>데이터베이스 캐시 정책 변경
데이터베이스에 대한 캐시 정책을 설정합니다. 이전 5일간의 데이터는 클러스터 SSD에 있습니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>테이블 캐시 정책 변경
테이블에 대한 캐시 정책을 설정합니다. 이전 5일간의 데이터는 클러스터 SSD에 있습니다.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>데이터베이스에 대한 새 보안 주체 추가
새 Azure AD 응용 프로그램을 데이터베이스의 관리자 보안 주체로 추가합니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>다음 단계

* [데이터베이스 및 테이블 정책에 대해 자세히 알아보기](https://docs.microsoft.com/azure/kusto/management/policies)
