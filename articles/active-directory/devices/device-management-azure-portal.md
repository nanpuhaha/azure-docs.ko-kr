---
title: Azure Portal을 사용하여 디바이스를 관리하는 방법 | Microsoft Docs
description: Azure Portal을 사용하여 디바이스를 관리하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262244"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Azure 포털을 사용하여 장치 ID 관리

Azure Active Directory(Azure AD)의 장치 ID 관리를 사용하면 사용자가 보안 및 규정 준수 에 대한 표준을 충족하는 장치에서 리소스에 액세스하고 있는지 확인할 수 있습니다.

이 문서의 내용:

- [Azure Active Directory에서 장치 ID 관리 소개에](overview.md) 대해 잘 알고 있다고 가정합니다.
- Azure AD 포털을 사용하여 장치 ID 관리에 대한 정보를 제공합니다.

## <a name="manage-device-identities"></a>디바이스 ID 관리

Azure AD 포털은 장치 ID를 관리할 수 있는 중앙 위치를 제공합니다. [직접 링크를](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) 사용하거나 다음을 사용하여 이 곳으로 갈 수 있습니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. Azure **Active 디렉터리** > **장치로**검색합니다.

**디바이스** 페이지에서 다음을 수행할 수 있습니다.

- 디바이스 설정 구성
- 디바이스 찾기
- 장치 ID 관리 작업 수행
- 장치 관련 감사 로그 검토  
  
## <a name="configure-device-settings"></a>디바이스 설정 구성

Azure AD 포털을 사용하여 장치 ID를 관리하려면 장치를 등록하거나 Azure AD에 [조인해야](overview.md) 합니다. 관리자는 디바이스 설정을 구성하여 디바이스 등록 및 조인 프로세스를 구체적으로 조정할 수 있습니다.

장치 설정 페이지에서 장치 ID와 관련된 설정을 구성할 수 있습니다.

![Intune 디바이스 관리](./media/device-management-azure-portal/21.png)

- **사용자는 Azure AD에 장치를 조인할 수 있습니다** - 이 설정을 사용하면 Azure AD가 가입한 장치로 장치를 등록할 수 있는 사용자를 선택할 수 있습니다. 기본값은 **All**입니다.

> [!NOTE]
> **사용자는 Azure AD 설정에 장치를 조인할 수** 있으며 Windows 10의 Azure AD 조인에만 적용할 수 있습니다.

- **Azure AD 조인 디바이스의 추가 로컬 관리자** - 디바이스에서 로컬 관리자 권한이 부여된 사용자를 선택할 수 있습니다. 여기에 추가한 사용자는 Azure AD의 *디바이스 관리자* 역할에 추가됩니다. Azure AD의 전역 관리자 및 디바이스 소유자에게는 기본적으로 로컬 관리자 권한이 부여됩니다. 이 옵션은 Azure AD Premium 또는 EMS(Enterprise Mobility Suite) 등의 제품을 통해 사용할 수 있는 프리미엄 버전 기능입니다.
- **사용자는 Azure AD에 장치를 등록할 수 있습니다** - Windows 10 개인, iOS, Android 및 macOs 장치를 Azure AD에 등록할 수 있도록 이 설정을 구성해야 합니다. **없음을**선택하면 장치는 Azure AD에 등록할 수 없습니다. Office 365용 Microsoft Intune 또는 MDM(모바일 디바이스 관리)에 등록하려면 먼저 디바이스를 등록해야 합니다. 이러한 서비스 중 하나를 구성한 경우 **모두**가 선택되고 **없음**은 사용할 수 없습니다.
- **디바이스에 가입하기 위해 다중 요소 인증 요구** - 사용자가 Azure AD에 장치를 조인하기 위해 추가 인증 요소를 제공해야 하는지 여부를 선택할 수 있습니다. 기본값은 **아니오입니다.** 그러나 디바이스를 등록하는 경우 Multi-Factor Authentication을 사용하는 것이 좋습니다. 이 서비스에 대해 Multi-Factor Authentication을 사용하도록 설정하기 전에 디바이스를 등록하는 사용자에 대해 Multi-Factor Authentication을 구성해야 합니다. 다양한 Azure Multi-Factor Authentication 서비스에 대한 자세한 내용은 [Azure Multi-Factor Authentication 시작](../authentication/concept-mfa-whichversion.md)을 참조하세요. 

> [!NOTE]
> **장치 조인을 위해 다중 요소 인증이 필요하도록 설정하면** Azure AD 조인또는 등록된 Azure AD인 장치에 적용됩니다. 이 설정은 하이브리드 Azure AD 조인 장치에는 적용되지 않습니다.

- **최대 장치 수** - 이 설정을 사용하면 사용자가 Azure AD에 가질 수 있는 최대 Azure AD 조인 또는 Azure AD 등록 장치의 최대 수를 선택할 수 있습니다. 사용자가 이 할당량에 도달하는 경우 기존 디바이스 중 하나 이상이 제거될 때까지 디바이스를 더 추가할 수 없습니다. 기본값은 **20입니다.**

> [!NOTE]
> **최대 장치** 설정 수는 Azure AD 조인또는 등록된 Azure AD인 장치에 적용됩니다. 이 설정은 하이브리드 Azure AD 조인 장치에는 적용되지 않습니다.

- **사용자자 디바이스 간에 설정 및 앱 데이터를 동기화할 수 있음** - 기본적으로 이 설정은 **없음**으로 지정됩니다. 특정 사용자 또는 그룹을 선택하거나 모두를 선택하면 Windows 10 디바이스 간에 사용자 설정 및 앱 데이터를 동기화할 수 있습니다. Windows 10에서 동기화가 작동되는 방식에 대해 자세히 알아봅니다.
이 옵션은 Azure AD Premium 또는 EMS(Enterprise Mobility Suite) 등의 제품을 통해 사용할 수 있는 프리미엄 기능입니다.

## <a name="locate-devices"></a>디바이스 찾기

두 가지 옵션으로 등록 및 조인된 디바이스를 찾을 수 있습니다.

- **디바이스** 페이지의 **관리** 섹션에 있는 **모든 디바이스**  
- **사용자** 페이지의 **관리** 섹션에 있는 **디바이스**

두 옵션 모두에서 다음과 같은 보기가 표시될 수입니다.

- 표시 이름 또는 장치 ID를 필터로 사용하여 장치를 검색할 수 있습니다.
- 등록 및 조인된 디바이스의 자세한 개요를 제공합니다.
- 일반 디바이스 관리 작업을 수행할 수 있습니다.

![모든 디바이스](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* 등록된 열 아래에 "하이브리드 Azure AD 조인"상태와 "하이브리드 Azure AD 조인"인 장치가 표시되면 장치가 Azure AD 연결에서 동기화되어 클라이언트에서 등록을 완료하기를 기다리고 있음을 나타냅니다. [하이브리드 Azure AD 조인 구현을 계획하는](hybrid-azuread-join-plan.md)방법에 대해 자세히 읽어보십시오. 추가 정보는 문서에서 찾을 수 [있습니다.](faq.md)
>
>   ![보류 중인 장치](./media/device-management-azure-portal/75.png)
>
>* 일부 iOS 디바이스의 경우 아포스트로피를 포함하는 디바이스 이름이 아포스트로피처럼 보이는 다른 문자를 사용할 수 있습니다. 따라서 이러한 장치를 검색하는 것은 다소 까다롭습니다 - 검색 결과가 제대로 표시되지 않으면 검색 문자열에 일치하는 아포스트로피 문자가 포함되어 있는지 확인하십시오.

## <a name="device-identity-management-tasks"></a>장치 ID 관리 작업

글로벌 관리자 또는 클라우드 장치 관리자는 등록된 또는 조인된 장치를 관리할 수 있습니다. Intune 서비스 관리자는 다음을 수행할 수 있습니다.

- 디바이스 업데이트 - 예제는 디바이스를 사용/사용 안 함과 같은 일상적인 작업임
- 디바이스 삭제 - 디바이스가 사용 중지되고 Azure AD에서 삭제돼야 하는 경우

이 섹션에서는 일반적인 장치 ID 관리 작업에 대한 정보를 제공합니다.

### <a name="manage-an-intune-device"></a>Intune 디바이스 관리

Intune 관리자인 경우 **Microsoft Intune**으로 표시된 디바이스를 관리할 수 있습니다. 장치가 Microsoft Intune에 등록되지 않은 경우 "관리" 옵션이 회색으로 표시됩니다.

![Intune 디바이스 관리](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Azure AD 디바이스 사용/사용 안 함

디바이스를 사용/사용 안 함으로 설정하려면 두 가지 옵션이 있습니다.

- **모든 디바이스** 페이지의 작업 메뉴("…")

   ![Intune 디바이스 관리](./media/device-management-azure-portal/71.png)

- **디바이스** 페이지의 도구 모음

   ![Intune 디바이스 관리](./media/device-management-azure-portal/32.png)

**발언:**

- 장치를 활성화/비활성화하려면 Azure AD의 글로벌 관리자 또는 클라우드 장치 관리자여야 합니다. 
- 장치를 사용하지 않도록 설정하면 장치가 Azure AD로 성공적으로 인증되지 못하므로 장치가 장치 CA에서 보호되는 Azure AD 리소스에 액세스하거나 WH4B 자격 증명을 사용하는 것을 방지할 수 있습니다.
- 장치를 사용하지 않도록 설정하면 PRT(기본 새로 고침 토큰)와 장치의 모든 새로 고침 토큰(RT)이 모두 취소됩니다.

### <a name="delete-an-azure-ad-device"></a>Azure AD 디바이스 삭제

디바이스를 삭제하려면 두 가지 옵션이 있습니다.

- **모든 디바이스** 페이지의 작업 메뉴("…")

   ![Intune 디바이스 관리](./media/device-management-azure-portal/72.png)

- **디바이스** 페이지의 도구 모음

   ![디바이스 삭제](./media/device-management-azure-portal/34.png)

**발언:**

- 디바이스를 삭제하려면 Azure AD에서 글로벌 관리자 또는 Intune 관리자여야 합니다.
- 디바이스 삭제:
   - 디바이스에서 Azure AD 리소스에 액세스할 수 없게 됩니다.
   - 디바이스에 연결되어 있는 모든 세부 정보(예: Windows 디바이스에 대한 BitLocker 키)를 제거합니다.  
   - 복구할 수 없으며, 반드시 필요한 경우가 아니면 권장되지 않는 작업을 나타냅니다.

다른 관리 기관(예: Microsoft Intune)에서 장치를 관리하는 경우 Azure AD에서 장치를 삭제하기 전에 장치가 초기화/폐기되었는지 확인합니다. 장치를 삭제하기 전에 [오래된 장치를 관리하는](device-management-azure-portal.md) 방법을 검토합니다.

### <a name="view-or-copy-device-id"></a>디바이스 ID 확인 또는 복사

디바이스 ID를 사용하여 디바이스의 디바이스 ID 세부 정보를 확인하거나 문제 해결 동안 PowerShell을 사용할 수 있습니다. 복사 옵션에 액세스하려면 디바이스를 클릭합니다.

![디바이스 ID 보기](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker 키 확인 또는 복사

BitLocker 키를 보고 복사하여 사용자가 암호화된 드라이브를 복구하도록 지원할 수 있습니다. 이러한 키는 암호화되고 해당 키가 Azure AD에 저장된 Windows 디바이스에 대해서만 사용할 수 있습니다. 디바이스 세부 정보에 액세스할 때 이러한 키를 복사할 수 있습니다.

![BitLocker 키 보기](./media/device-management-azure-portal/36.png)

BitLocker 키를 보거나 복사하려면, 디바이스의 소유자 또는 다음 역할 중 하나 이상이 할당된 사용자여야 합니다.

- 클라우드 디바이스 관리자
- 전역 관리자
- 기술 지원팀 관리자
- Intune 서비스 관리자
- 보안 관리자
- 보안 판독기

> [!NOTE]
> 하이브리드 Azure AD 조인 Windows 10 디바이스에는 소유자가 없습니다. 따라서 소유자로 디바이스를 찾으려는 경우 찾지 못하면 디바이스 ID로 검색합니다.

## <a name="audit-logs"></a>감사 로그

디바이스 활동은 활동 로그를 통해 사용할 수 있습니다. 이러한 로그에는 장치 등록 서비스 및 사용자가 트리거하는 활동이 포함됩니다.

- 디바이스 만들기 및 디바이스에 소유자/사용자 추가
- 디바이스 설정 변경
- 디바이스 삭제 또는 업데이트 등의 디바이스 작업

감사 데이터에 대한 진입점은 **디바이스** 페이지의 **작업** 섹션에 있는 **감사 로그**입니다.

감사 로그에는 다음을 보여 주는 기본 목록 보기가 있습니다.

- 발생 날짜와 시간
- 대상
- 작업의 초기자/행위자(누가)
- 작업(무엇)

![감사 로그](./media/device-management-azure-portal/63.png)

도구 모음에서 열을 클릭하여 목록 보기를 사용자 지정할 수 **있습니다.**

![감사 로그](./media/device-management-azure-portal/64.png)

보고된 데이터를 자신에게 적합한 수준으로 좁히려면 다음 필드를 사용하여 감사 데이터를 필터링할 수 있습니다.

- Category
- 활동 리소스 종류
- 활동
- 날짜 범위
- 대상
- 초기자(작업자)

필터 이외의 방법으로도 특정 항목을 검색할 수 있습니다.

![감사 로그](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>다음 단계

[Azure AD에서 오래된 장치를 관리하는 방법](manage-stale-devices.md)
