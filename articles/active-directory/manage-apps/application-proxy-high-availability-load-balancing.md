---
title: 고가용성 및 부하 분산 - Azure AD 응용 프로그램 프록시
description: 응용 프로그램 프록시 배포와 트래픽 분포가 작동하는 방식입니다. 커넥터 성능을 최적화하고 백 엔드 서버에 로드 분산을 사용하는 방법에 대한 팁이 포함되어 있습니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3202c2fbfedfce0b0b52be94b1e0d165a6e72546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481316"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>애플리케이션 프록시 커넥터 및 애플리케이션의 고가용성 및 부하 분산

이 문서에서는 응용 프로그램 프록시 배포에서 트래픽 배포가 작동하는 방식을 설명합니다. 우리는 논의 할 것이다 :

- 커넥터 성능 최적화를 위한 팁과 함께 사용자와 커넥터 간에 트래픽이 분산되는 방법

- 커넥터와 백 엔드 앱 서버 간에 트래픽흐름, 여러 백 엔드 서버 간의 부하 분산 권장 사항

## <a name="traffic-distribution-across-connectors"></a>커넥터 간 트래픽 분포

커넥터는 고가용성을 위한 원칙에 따라 연결을 설정합니다. 트래픽이 항상 커넥터 간에 균등하게 분산되고 세션 선호도가 없다는 보장은 없습니다. 그러나 사용량은 다양하며 요청은 응용 프로그램 프록시 서비스 인스턴스로 임의로 전송됩니다. 따라서 트래픽은 일반적으로 커넥터 전체에 거의 균등하게 분산됩니다. 아래 다이어그램과 단계는 사용자와 커넥터 간에 연결이 설정되는 방법을 보여 줍니다.

![사용자와 커넥터 간의 연결을 보여주는 다이어그램](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. 클라이언트 장치의 사용자가 응용 프로그램 프록시를 통해 게시된 온-프레미스 응용 프로그램에 액세스하려고 시도합니다.
2. 요청은 Azure Load Balancer를 통해 요청을 수행해야 하는 응용 프로그램 프록시 서비스 인스턴스를 결정합니다. 리전당 요청을 수락할 수 있는 인스턴스가 수십 개 있습니다. 이 방법을 사용하면 서비스 인스턴스 간에 트래픽을 균등하게 분산할 수 있습니다.
3. 요청이 서비스 [버스로](https://docs.microsoft.com/azure/service-bus-messaging/)전송됩니다.
4. 서비스 버스는 연결이 이전에 커넥터 그룹의 기존 커넥터를 사용했는지 확인합니다. 그렇다면 연결을 다시 사용합니다. 아직 연결과 페어링된 커넥터가 없는 경우 신호를 표시하기 위해 임의로 사용 가능한 커넥터를 선택합니다. 그런 다음 커넥터가 서비스 버스에서 요청을 선택합니다.

   - 2단계에서는 요청이 다른 응용 프로그램 프록시 서비스 인스턴스로 이동하므로 다른 커넥터로 연결될 가능성이 높습니다. 결과적으로 커넥터는 그룹 내에서 거의 균등하게 사용됩니다.

   - 연결이 끊어지거나 유휴 기간이 10분인 경우에만 연결이 다시 설정됩니다. 예를 들어 컴퓨터 또는 커넥터 서비스가 다시 시작되거나 네트워크 중단이 있을 때 연결이 끊어질 수 있습니다.

5. 커넥터는 요청을 응용 프로그램의 백 엔드 서버로 전달합니다. 그런 다음 응용 프로그램이 응답을 커넥터로 다시 보냅니다.
6. 커넥터는 요청이 발생한 위치에서 서비스 인스턴스에 대한 아웃바운드 연결을 열어 응답을 완료합니다. 그런 다음 이 연결이 즉시 닫힙됩니다. 기본적으로 각 커넥터는 200개의 동시 아웃바운드 연결로 제한됩니다.
7. 그런 다음 응답이 서비스 인스턴스에서 클라이언트로 다시 전달됩니다.
8. 동일한 연결의 후속 요청은 이 연결이 끊어지거나 10분 동안 유휴 상태가 될 때까지 위의 단계를 반복합니다.

응용 프로그램에는 많은 리소스가 있고 로드될 때 여러 연결을 엽니다. 각 연결은 위의 단계를 거쳐 서비스 인스턴스에 할당되고 연결이 아직 커넥터와 페어링되지 않은 경우 사용 가능한 새 커넥터를 선택합니다.


## <a name="best-practices-for-high-availability-of-connectors"></a>커넥터의 고가용성을 위한 모범 사례

- 고가용성을 위해 커넥터 간에 트래픽이 분산되는 방식 때문에 항상 커넥터 그룹에 두 개 이상의 커넥터가 있어야 합니다. 커넥터 사이에 추가 버퍼를 제공하는 데 세 개의 커넥터가 선호됩니다. 필요한 커넥터의 정확한 수를 확인하려면 용량 계획 설명서를 따르십시오.

- 단일 실패 지점을 피하기 위해 커넥터를 다른 아웃바운드 연결에 배치합니다. 커넥터가 동일한 아웃바운드 연결을 사용하는 경우 연결에 문제가 있으면 커넥터를 사용하는 모든 커넥터에 영향을 미칠 수 있습니다.

- 프로덕션 응용 프로그램에 연결될 때 커넥터가 다시 시작되지 않도록 합니다. 이렇게 하면 커넥터 간 트래픽 분포에 부정적인 영향을 줄 수 있습니다. 커넥터를 다시 시작하면 더 많은 커넥터를 사용할 수 없게 되고 사용 가능한 나머지 커넥터에 대한 연결이 강제로 활성화됩니다. 그 결과 처음에는 커넥터를 고르지 않게 사용할 수 있습니다.

- 커넥터와 Azure 간의 아웃바운드 TLS 통신에 대한 모든 형태의 인라인 검사를 피하십시오. 이러한 유형의 인라인 검사는 통신 흐름이 저하됩니다.

- 커넥터에 대해 자동 업데이트를 계속 실행해야 합니다. 응용 프로그램 프록시 커넥터 업데이트 프로그램이 실행 중인 경우 커넥터가 자동으로 업데이트되고 업그레이드된 최신 커넥터를 받게 됩니다. 서버에 커넥터 업데이터 서비스가 표시되지 않는 경우 업데이트를 받으려면 커넥터를 다시 설치해야 합니다.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>커넥터와 백 엔드 응용 프로그램 서버 간의 트래픽 흐름

고가용성이 중요한 또 다른 핵심 영역은 커넥터와 백 엔드 서버 간의 연결입니다. Azure AD 응용 프로그램 프록시를 통해 응용 프로그램을 게시하면 사용자에서 응용 프로그램으로의 트래픽이 세 홉을 통해 흐릅니다.

1. 사용자는 Azure에서 Azure AD 응용 프로그램 프록시 서비스 공용 끝점에 연결합니다. 연결은 클라이언트의 원래 클라이언트 IP 주소(공용)와 응용 프로그램 프록시 끝점의 IP 주소 간에 설정됩니다.
2. 응용 프로그램 프록시 커넥터는 응용 프로그램 프록시 서비스에서 클라이언트의 HTTP 요청을 가져옵니다.
3. 응용 프로그램 프록시 커넥터는 대상 응용 프로그램에 연결합니다. 커넥터는 연결을 설정하기 위해 자체 IP 주소를 사용합니다.

![응용 프로그램 프록시를 통해 응용 프로그램에 연결하는 사용자 다이어그램](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-전달-헤더 필드 고려 사항
감사, 부하 분산 등과 같은 일부 상황에서는 외부 클라이언트의 원래 IP 주소를 온-프레미스 환경과 공유하는 것이 요구됩니다. 요구 사항을 해결하기 위해 Azure AD 응용 프로그램 프록시 커넥터는 HTTP 요청에 원래 클라이언트 IP 주소(public)가 있는 X-Forwarded-For 헤더 필드를 추가합니다. 적절한 네트워크 장치(로드 밸런서, 방화벽) 또는 웹 서버 또는 백 엔드 응용 프로그램이 해당 정보를 읽고 사용할 수 있습니다.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>여러 앱 서버 간의 로드 균형 조정모범 사례
응용 프로그램 프록시 응용 프로그램에 할당된 커넥터 그룹에 두 개 이상의 커넥터가 있고 여러 서버(서버 팜)에서 백 엔드 웹 응용 프로그램을 실행하는 경우 올바른 부하 분산 전략이 필요합니다. 좋은 전략은 서버가 클라이언트 요청을 균등하게 선택하고 서버 팜에 있는 서버의 과다 또는 과소 사용률을 방지합니다.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>시나리오 1: 백 엔드 응용 프로그램에는 세션 지속성이 필요하지 않습니다.
가장 간단한 시나리오는 백 엔드 웹 응용 프로그램에 세션 끈기(세션 지속성)가 필요하지 않은 경우입니다. 사용자의 모든 요청은 서버 팜의 모든 백 엔드 응용 프로그램 인스턴스에서 처리할 수 있습니다. 레이어 4 로드 밸런서를 사용하고 선호도 없이 구성할 수 있습니다. 일부 옵션에는 Microsoft 네트워크 로드 균형 조정 및 Azure 로드 밸런서 또는 다른 공급업체의 로드 밸런서가 포함됩니다. 또는 라운드 로빈 DNS를 구성할 수 있습니다.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>시나리오 2: 백 엔드 응용 프로그램에세션 지속성이 필요합니다.
이 시나리오에서 백 엔드 웹 응용 프로그램에는 인증된 세션 중에 세션 끈기(세션 지속성)가 필요합니다. 사용자의 모든 요청은 서버 팜의 동일한 서버에서 실행되는 백 엔드 응용 프로그램 인스턴스에서 처리해야 합니다.
이 시나리오는 클라이언트가 일반적으로 응용 프로그램 프록시 서비스에 대한 여러 연결을 설정하기 때문에 더 복잡할 수 있습니다. 서로 다른 연결을 통해 요청은 팜의 다른 커넥터 및 서버에 도착할 수 있습니다. 각 커넥터는 이 통신에 자체 IP 주소를 사용하기 때문에 로드 밸런서에서는 커넥터의 IP 주소를 기반으로 세션 고정성을 보장할 수 없습니다. 소스 IP 선호도도 사용할 수 없습니다.
시나리오 2에 대한 몇 가지 옵션은 다음과 같습니다.

- 옵션 1: 로드 밸런서에 의해 설정된 세션 쿠키에 세션 지속성을 기반으로 합니다. 이 옵션은 백 엔드 서버 간에 부하를 더 균등하게 분산할 수 있으므로 권장됩니다. 이 기능을 갖춘 계층 7 로드 밸런서가 필요하며 HTTP 트래픽을 처리하고 TLS 연결을 종료할 수 있습니다. Azure 응용 프로그램 게이트웨이(세션 선호도) 또는 다른 공급업체의 로드 밸러버를 사용할 수 있습니다.

- 옵션 2: X-전달-For 헤더 필드에 세션 지속성을 기반으로 합니다. 이 옵션에는 이 기능을 갖춘 계층 7 로드 밸런서가 필요하며 HTTP 트래픽을 처리하고 TLS 연결을 종료할 수 있습니다.  

- 옵션 3: 세션 지속성을 필요로 하지 않도록 백 엔드 응용 프로그램을 구성합니다.

백 엔드 응용 프로그램의 로드 밸런싱 요구 사항을 이해하려면 소프트웨어 공급업체의 설명서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 프록시 사용](application-proxy-add-on-premises-application.md)
- [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
- [조건부 액세스 사용](application-proxy-integrate-with-sharepoint-server.md)
- [애플리케이션 프록시에서 발생한 문제 해결](application-proxy-troubleshoot.md)
- [Azure AD 아키텍처가 고가용성을 지원하는 방법 알아보기](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
