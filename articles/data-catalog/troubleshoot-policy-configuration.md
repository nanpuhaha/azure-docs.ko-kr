---
title: Azure 데이터 카탈로그 문제를 해결하는 방법
description: 이 문서에서는 Azure 데이터 카탈로그 리소스에 대한 일반적인 문제 해결 문제에 대해 설명합니다.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879548"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure Data Catalog 문제 해결

이 문서에서는 Azure 데이터 카탈로그 리소스에 대한 일반적인 문제 해결 문제에 대해 설명합니다. 

## <a name="functionality-limitations"></a>기능 제한

Azure 데이터 카탈로그를 사용하는 경우 다음 기능이 제한됩니다.

- **게스트 역할을** 입력한 계정은 지원되지 않습니다. Azure 데이터 카탈로그의 사용자로 게스트 계정을 추가할 수 없으며 [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)게스트 사용자는 에서 포털을 사용할 수 없습니다.

- Azure 리소스 관리자 템플릿 또는 Azure PowerShell 명령을 사용하여 Azure 데이터 카탈로그 리소스 만들기는 지원되지 않습니다.

- Azure 데이터 카탈로그 리소스를 Azure 테넌테 간에 이동할 수 없습니다.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 정책 구성

Azure Data Catalog 포털에 로그인할 수 있는 상황이 발생할 수 있지만, 데이터 원본 등록 도구에 로그인을 시도할 때 로그인하지 않도록 하는 오류 메시지가 발생합니다. 이 오류는 회사 네트워크에 있거나 회사 네트워크 외부에서 연결할 때 발생할 수 있습니다.

등록 도구가 *폼 인증* 을 사용하여 Azure Active Directory에 대한 사용자 로그인의 유효성 검사를 수행합니다. 로그인이 성공하려면 Azure Active Directory 관리자가 *전역 인증 정책*에서 폼 인증을 사용할 수 있도록 해야 합니다.

전역 인증 정책을 사용하면 다음 이미지에 나와 있는 것처럼 인트라넷 및 엑스트라넷 연결에 대해 개별 인증을 사용하도록 설정할 수 있습니다. 연결하는 네트워크에 양식 인증을 사용하도록 설정하지 않은 경우 로그인 오류가 발생할 수 있습니다.

 ![Azure Active Directory 전역 인증 정책](./media/troubleshoot-policy-configuration/global-auth-policy.png)

자세한 내용은 [인증 정책 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Data Catalog 만들기](data-catalog-get-started.md)
