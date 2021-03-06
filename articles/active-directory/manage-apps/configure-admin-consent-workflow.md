---
title: 관리자 동의 워크플로 구성 - Azure Active Directory | 마이크로 소프트 문서
description: 최종 사용자가 관리자 동의가 필요한 응용 프로그램에 대한 액세스를 요청하는 방법을 구성하는 방법을 알아봅니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430209"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>관리자 동의 워크플로 구성(미리 보기)

이 문서에서는 최종 사용자에게 관리자 동의가 필요한 응용 프로그램에 대한 액세스를 요청하는 방법을 제공하는 관리자 동의 워크플로(미리 보기) 기능을 사용하도록 설정하는 방법을 설명합니다.

관리자 동의 워크플로가 없으면 조직 데이터에 액세스할 권한이 필요한 앱에 액세스하려고 할 때 사용자 동의가 비활성화된 테넌트의 사용자가 차단됩니다. 사용자에게 앱에 액세스할 수 없는 일반 오류 메시지가 보고 관리자에게 도움을 요청해야 합니다. 그러나 사용자가 연락할 사람을 모르는 경우가 많기 때문에 응용 프로그램에서 새 로컬 계정을 포기하거나 만듭니다. 관리자에게 알림이 전송되더라도 관리자가 액세스 권한을 부여하고 사용자에게 알리는 데 도움이 되는 간소화된 프로세스가 항상 있는 것은 아닙니다.
 
관리자 동의 워크플로는 관리자에게 관리자 승인이 필요한 응용 프로그램에 대한 액세스 권한을 부여하는 안전한 방법을 제공합니다. 사용자가 응용 프로그램에 액세스하려고 했지만 동의를 제공할 수 없는 경우 관리자 승인 요청을 보낼 수 있습니다. 요청은 검토자로 지정된 관리자에게 이메일을 통해 전송됩니다. 검토자는 요청에 대한 작업을 수행하며 사용자에게 작업에 대한 알림을 받습니다.

요청을 승인하려면 검토자가 글로벌 관리자, 클라우드 응용 프로그램 관리자 또는 응용 프로그램 관리자여야 합니다. 검토자는 이미 이러한 관리자 역할 중 하나가 할당되어 있어야 합니다. 단순히 검토자로 지정해도 해당 권한이 높아지는 것은 아닙니다.

## <a name="enable-the-admin-consent-workflow"></a>관리자 동의 워크플로 사용

관리자 동의 워크플로를 사용하도록 설정하고 검토자를 선택하려면 다음 단계를 수행합니다.

1. [Azure 포털에](https://portal.azure.com) 글로벌 관리자로 로그인합니다.
2. 왼쪽 탐색 메뉴의 맨 위에 있는 **모든 서비스**를 클릭합니다. **Azure Active Directory 확장**이 열립니다.
3. 필터 검색 상자에서 **"Azure Active Directory"를**입력하고 **Azure Active Directory** 항목을 선택합니다.
4. 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다. 
5. **에서 관리**에서 **사용자 설정을**선택합니다.
6. **관리자 동의 요청(미리 보기)에**따라 설정 된 사용자는 **예에** **동의 할 수없는 앱에 대한 관리자 동의를 요청할 수 있습니다.**

   ![관리자 동의 워크플로 설정 구성](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. 다음 설정을 구성합니다.

   * **관리자 동의 요청을 검토할 사용자를 선택합니다.** 글로벌 관리자, 클라우드 응용 프로그램 관리자 및 응용 프로그램 관리자 역할이 있는 사용자 집합에서 이 워크플로에 대한 검토자를 선택합니다.
   * **선택한 사용자는 요청에 대한 이메일 알림을 받게 됩니다.** 요청이 있을 때 검토자에게 이메일 알림을 활성화하거나 사용하지 않도록 설정합니다.  
   * **선택한 사용자는 요청 만료 알림을 받게 됩니다.** 요청이 만료될 때 검토자에게 미리 알림 전자 알림을 사용 하거나 사용하지 않도록 설정합니다.  
   * **동의 요청은 일 후에 만료됩니다.** 요청이 유효한 상태를 지정합니다.

7. **저장**을 선택합니다. 기능이 활성화되려면 최대 1시간이 걸릴 수 있습니다.

> [!NOTE]
> **관리자 동의 요청 검토자** 선택 목록을 수정하여 이 워크플로에 대한 검토자를 추가하거나 제거할 수 있습니다. 이 기능의 현재 제한사항은 검토자가 검토자로 지정된 동안 이루어진 요청을 검토할 수 있다는 것입니다.

## <a name="how-users-request-admin-consent"></a>사용자가 관리자 동의를 요청하는 방법

관리자 동의 워크플로를 사용하도록 설정한 후 사용자는 승인되지 않은 응용 프로그램에 대한 관리자 승인을 요청할 수 있습니다. 다음 단계는 승인을 요청할 때의 사용자 환경을 설명합니다. 

1. 사용자가 응용 프로그램에 로그인하려고 시도합니다.

2. **승인 필수** 메시지가 나타납니다. 사용자는 앱에 대한 액세스가 필요한 근거를 입력한 다음 **승인 요청을**선택합니다.

   ![관리자 동의 사용자 요청 및 정당화](media/configure-admin-consent-workflow/end-user-justification.png)

3. **보낸 요청** 메시지는 요청이 관리자에게 제출되었다는 것을 확인합니다. 사용자가 여러 요청을 보내는 경우 첫 번째 요청만 관리자에게 제출됩니다.

   ![관리자 동의 사용자 요청 및 정당화](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. 요청이 승인, 거부 또는 차단되면 사용자에게 이메일 알림을 받습니다. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>관리자 동의 요청 검토 및 조치

관리자 동의 요청을 검토하고 조치를 취하려면 다음 단계를 수행합니다.

1. 관리자 동의 워크플로의 등록된 검토자 중 하나로 [Azure 포털에](https://portal.azure.com) 로그인합니다.
2. 왼쪽 탐색 메뉴 상단의 **모든 서비스를** 선택합니다. **Azure Active Directory 확장**이 열립니다.
3. 필터 검색 상자에서 **"Azure Active Directory"를**입력하고 **Azure Active Directory** 항목을 선택합니다.
4. 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.
5. **활동**에서 **관리자 동의 요청(미리 보기)을**선택합니다.

   > [!NOTE]
   > 검토자는 검토자로 지정된 후에 생성된 관리자 요청만 볼 수 있습니다.

1. 요청되는 응용 프로그램을 선택합니다.
2. 요청에 대한 세부 정보를 검토합니다.  

   * 액세스를 요청하는 사람과 그 이유를 확인하려면 **요청됨 탭을** 선택합니다.
   * 응용 프로그램에서 요청하는 권한을 보려면 **사용 권한 검토 및 동의를 선택합니다.**

8. 요청을 평가하고 적절한 조치를 취하십시오.

   * **요청을 승인합니다.** 요청을 승인하려면 응용 프로그램에 대한 관리자 동의를 부여합니다. 요청이 승인되면 모든 요청자가 액세스 권한이 부여되었다는 알림을 받습니다.  
   * **요청을 거부합니다.** 요청을 거부하려면 모든 요청자에게 제공되는 근거를 제공해야 합니다. 요청이 거부되면 모든 요청자가 응용 프로그램에 대한 액세스가 거부되었음을 알 수 있습니다. 요청을 거부하면 나중에 사용자가 앱에 대한 관리자 동의를 다시 요청하는 것을 방지할 수 없습니다.  
   * **요청을 차단합니다.** 요청을 차단하려면 모든 요청자에게 제공되는 근거를 제공해야 합니다. 요청이 차단되면 모든 요청자가 응용 프로그램에 대한 액세스가 거부되었음을 알수 있습니다. 요청을 차단하면 비활성화된 상태의 테넌트에서 응용 프로그램에 대한 서비스 주체 개체가 만들어집니다. 사용자는 나중에 응용 프로그램에 대한 관리자 동의를 요청할 수 없습니다.
 
## <a name="email-notifications"></a>이메일 알림
 
구성된 경우 모든 검토자는 다음과 같은 경우 전자 메일 알림을 받게 됩니다.

* 새 요청이 만들어졌습니다.
* 요청이 만료되었습니다.
* 요청이 만료 날짜가 가까워지고 있습니다.  
 
다음과 같은 경우 요청자는 이메일 알림을 받게 됩니다.

* 새 액세스 요청을 제출합니다.
* 요청이 만료되었습니다.
* 요청이 거부되거나 차단되었습니다.
* 그들의 요청이 승인되었습니다.
 
## <a name="audit-logs"></a>감사 로그 
 
아래 표에서는 관리자 동의 워크플로에 사용할 수 있는 시나리오 및 감사 값을 간략하게 설명합니다. 

> [!NOTE]
> 감사 행위자의 사용자 컨텍스트는 현재 모든 시나리오에서 누락되었습니다. 미리 보기 버전에서 알려진 제한 사항입니다.


|시나리오  |감사 서비스  |감사 범주  |감사 활동  |감사 행위자  |감사 로그 제한  |
|---------|---------|---------|---------|---------|---------|
|동의 요청 워크플로를 사용하도록 설정하는 관리자        |액세스 검토           |사용자 관리           |거버넌스 정책 템플릿 만들기          |앱 컨텍스트            |현재 사용자 컨텍스트를 찾을 수 없습니다.            |
|동의 요청 워크플로를 사용하지 않도록 설정하는 관리자       |액세스 검토           |사용자 관리           |거버넌스 정책 템플릿 삭제          |앱 컨텍스트            |현재 사용자 컨텍스트를 찾을 수 없습니다.           |
|동의 워크플로 구성을 업데이트하는 관리자        |액세스 검토           |사용자 관리           |거버넌스 정책 템플릿 업데이트          |앱 컨텍스트            |현재 사용자 컨텍스트를 찾을 수 없습니다.           |
|앱에 대한 관리자 동의 요청을 만드는 최종 사용자       |액세스 검토           |정책         |요청 만들기           |앱 컨텍스트            |현재 사용자 컨텍스트를 찾을 수 없습니다.           |
|관리자 동의 요청을 승인하는 검토자       |액세스 검토           |사용자 관리           |비즈니스 흐름의 모든 요청 승인          |앱 컨텍스트            |현재 관리자 동의가 부여된 사용자 컨텍스트 또는 앱 ID를 찾을 수 없습니다.           |
|관리자 동의 요청을 거부하는 검토자       |액세스 검토           |사용자 관리           |비즈니스 흐름의 모든 요청 승인          |앱 컨텍스트            | 현재 관리자 동의 요청을 거부 한 행위자의 사용자 컨텍스트를 찾을 수 없습니다.          |

## <a name="faq"></a>FAQ 

**이 워크플로를 켜고 있지만 기능을 테스트할 때 액세스를 요청할 수 있는 새 "승인 필요" 프롬프트가 표시되지 않는 이유는 무엇입니까?**

이 기능을 켜면 최종 사용자가 업데이트를 확인하는 데 최대 60분이 걸릴 수 있습니다. `https://graph.microsoft.com/beta/settings` API에서 **EnableAdminConsentRequests** 값을 확인하여 구성이 제대로 적용되었는지 확인할 수 있습니다.

**검토자는 보류 중인 모든 요청을 볼 수 없는 이유는 무엇입니까?**

검토자는 검토자로 지정된 후에 생성된 관리자 요청만 볼 수 있습니다. 따라서 최근에 검토자로 추가된 경우 할당 전에 생성된 요청이 표시되지 않습니다.

**검토자는 왜 동일한 응용 프로그램에 대한 여러 요청이 표시되는가?**
  
응용 프로그램 개발자가 정적 및 동적 동의를 사용하여 최종 사용자의 데이터에 대한 액세스를 요청하도록 앱을 구성한 경우 두 개의 관리자 동의 요청이 표시됩니다. 한 요청은 정적 사용 권한을 나타내고 다른 요청은 동적 사용 권한을 나타냅니다.

**요청자로서 요청 상태를 확인할 수 있습니까?**  

아니요, 현재 요청자는 이메일 알림을 통해서만 업데이트를 받을 수 있습니다.

**검토자로서 응용 프로그램을 승인할 수 있지만 모든 사람이 승인할 수는 있습니까?**
 
관리자 동의를 부여하고 테넌트의 모든 사용자가 응용 프로그램을 사용하도록 허용하는 것이 염려되는 경우 요청을 거부하는 것이 좋습니다. 그런 다음 사용자 할당을 요구하고 응용 프로그램에 사용자 또는 그룹을 할당하여 응용 프로그램에 대한 액세스를 제한하여 관리자동의를 수동으로 부여합니다. 자세한 내용은 [사용자 및 그룹을 할당하는 메서드](methods-for-assigning-users-and-groups.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

[최종 사용자가 응용 프로그램에 동의하는 방식 구성](configure-user-consent.md)

[응용 프로그램에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)

[Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

[스택 오버플로에서 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
