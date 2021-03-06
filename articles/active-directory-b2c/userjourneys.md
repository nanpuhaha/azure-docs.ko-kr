---
title: UserJourneys | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책의 UserJourneys 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226997"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자 경험은 명시적 경로를 지정합니다. 이 경로를 통해 정책은 신뢰 당사자 애플리케이션이 사용자에 대해 원하는 클레임을 가져오도록 허용합니다. 이러한 경로를 통해 사용자를 가져와서 신뢰 당사자에게 표시할 클레임을 검색합니다. 즉, 사용자 경험은 Azure AD B2C ID 경험 프레임워크가 요청을 처리할 때 최종 사용자에게 적용되는 비즈니스 논리를 정의합니다.

이러한 사용자 여정은 관심 있는 커뮤니티의 다양한 의존 당사자의 핵심 요구를 충족하는 데 사용할 수 있는 템플릿으로 간주될 수 있습니다. 사용자 여정은 정책의 신뢰할 수 있는 당사자 부분의 정의를 용이하게 합니다. 정책 하나가 여러 사용자 경험을 정의할 수 있습니다. 각 사용자 경험은 오케스트레이션 단계 시퀀스입니다.

정책에서 지원하는 사용자 경험을 정의하기 위해 **UserJourneys** 요소가 정책 파일의 최상위 요소 아래에 추가됩니다.

**UserJourneys** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 전체 사용자 흐름에 필요한 모든 구문을 정의하는 사용자 경험입니다. |

**UserJourney** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 정책의 다른 요소에서 사용자 경험을 참조하는 데 사용할 수 있는 사용자 경험의 식별자입니다. [신뢰 당사자 정책](relyingparty.md)의 **DefaultUserJourney** 요소는 이 특성을 가리킵니다. |

**UserJourney** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스입니다. 모든 사용자 경험은 순서대로 실행되는 정렬된 오케스트레이션 단계 목록으로 구성됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. 이러한 오케스트레이션 단계는 정책 파일에서 허용되는 클레임 공급자와 구성 요소를 모두 참조합니다. 사용자 경험을 표시하거나 렌더링해야 하는 모든 오케스트레이션 단계에는 해당하는 콘텐츠 정의 식별자에 대한 참조도 포함됩니다.

오케스트레이션 단계는 오케스트레이션 단계 요소에 정의된 사전 조건에 따라 조건부로 실행할 수 있습니다. 예를 들어 특정 클레임이 있거나 클레임이 지정된 값과 같거나 같지 않은 경우에만 오케스트레이션 단계를 수행하도록 확인할 수 있습니다.

오케스트레이션 단계의 정렬된 목록을 지정하기 위해 **OrchestrationSteps** 요소가 정책의 일부로 추가됩니다. 이 요소는 필수 요소입니다.

**OrchestrationSteps** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 정렬된 오케스트레이션 단계입니다. |

**OrchestrationStep** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Order` | yes | 오케스트레이션 단계의 순서입니다. |
| `Type` | yes | 오케스트레이션 단계의 유형입니다. 가능한 값은 다음과 같습니다. <ul><li>**ClaimsProviderSelection** - 오케스트레이션 단계에서 사용자가 하나를 선택할 수 있도록 여러 클레임 공급자가 표시됨을 나타냅니다.</li><li>**CombinedSignInAndSignUp** - 오케스트레이션 단계에서 소셜 공급자 로그인 및 로컬 계정 등록 페이지가 결합된 형태로 표시됨을 나타냅니다.</li><li>**ClaimsExchange** - 오케스트레이션 단계에서 클레임 공급자와 클레임을 교환함을 나타냅니다.</li><li>**Get클레임** - 오케스트레이션 단계에서 입력 클레임을 읽음을 나타냅니다.</li><li>**SendClaims** - 오케스트레이션 단계에서 클레임 발급자가 발급한 토큰과 함께 클레임을 신뢰 당사자에게 전송함을 나타냅니다.</li></ul> |
| ContentDefinitionReferenceId | 예 | 이 오케스트레이션 단계와 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. 일반적으로 콘텐츠 정의 참조 식별자는 자체 어설션된 기술 프로필에서 정의됩니다. 그러나 Azure AD B2C가 기술 프로필을 사용하지 않고 콘텐츠를 표시해야 하는 경우도 있습니다. 두 가지 예가 있습니다 - 오케스트레이션 단계의 `ClaimsProviderSelection` 유형이 다음 중 하나인 경우 또는 `CombinedSignInAndSignUp`, Azure AD B2C는 기술 프로필없이 ID 공급자 선택을 표시해야 합니다. |
| CpimIssuerTechnicalProfileReferenceId | 예 | 오케스트레이션 단계의 유형이 `SendClaims`입니다. 이 속성은 신뢰 당사자용 토큰을 발급하는 클레임 공급자의 기술 프로필 식별자를 정의합니다.  해당 식별자가 없으면 신뢰 당사자 토큰이 생성되지 않습니다. |


**OrchestrationStep** 요소에는 다음과 같은 요소가 포함될 수 있습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | 오케스트레이션 단계를 실행하려면 충족해야 하는 전제 조건 목록입니다. |
| ClaimsProviderSelections | 0:n | 오케스트레이션 단계의 클레임 공급자 선택 항목 목록입니다. |
| ClaimsExchanges | 0:n | 오케스트레이션 단계의 클레임 교환 목록입니다. |

### <a name="preconditions"></a>Preconditions

**Preconditions** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Precondition | 1:n | 사용 중인 기술 프로필별로 클레임 공급자 선택 항목에 따라 클라이언트를 리디렉션하거나 교환 클레임에 대한 서버 호출을 수행합니다. |


#### <a name="precondition"></a>Precondition

**사전 조건** 요소에는 다음 특성이 포함되어 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Type` | yes | 이 전제 조건에 대해 수행할 확인이나 쿼리의 유형입니다. 값은 **ClaimsExist**(지정한 클레임이 사용자의 현재 클레임 집합에 있으면 작업을 수행해야 함을 지정함) 또는 **ClaimEquals**(지정한 클레임이 있으며 해당 값이 지정된 값과 같으면 작업을 수행해야 함을 지정함)일 수 있습니다. |
| `ExecuteActionsIf` | yes | true 또는 false 테스트를 사용하여 전제 조건의 작업을 수행해야 하는지를 결정합니다. |

**Precondition** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 값 | 1:n | 쿼리할 ClaimTypeReferenceId입니다. 확인할 값은 다른 value 요소에 포함되어 있습니다.</li></ul>|
| 작업 | 1:1 | 오케스트레이션 단계 내에서 수행한 전제 조건 확인의 결과가 true이면 수행해야 하는 작업입니다. `Action`의 값이 `SkipThisOrchestrationStep`으로 설정된 경우 연결된 `OrchestrationStep`이 실행되면 안 됩니다. |

#### <a name="preconditions-examples"></a>Preconditions 예제

다음 Preconditions는 사용자의 objectId가 있는지를 확인합니다. 사용자 경험에서 사용자는 로컬 계정을 사용하여 로그인하도록 선택했습니다. objectId가 있으면 이 오케스트레이션 단계를 건너뜁니다.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

다음 Preconditions는 사용자가 소셜 계정을 사용하여 로그인했는지를 확인합니다. 이를 위해 디렉터리에서 사용자 계정 찾기를 시도합니다. 사용자가 로그인하거나 로컬 계정으로 로그인하는 경우 이 오케스트레이션 단계를 건너뜁니다.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Preconditions는 여러 전제 조건을 확인할 수 있습니다. 다음 예제에서는 'objectId' 또는 'email'이 있는지 여부를 확인합니다. 첫 번째 조건이 true이면 여정이 다음 오케스트레이션 단계로 건너뜁니다.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

`ClaimsProviderSelection` 또는 `CombinedSignInAndSignUp` 유형의 오케스트레이션 단계는 사용자가 로그인하는 데 사용할 수 있는 클레임 공급자 목록을 포함할 수 있습니다. `ClaimsProviderSelections` 요소 내의 요소 순서는 사용자에게 표시되는 ID 공급자 순서를 제어합니다.

**클레임공급자선택** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 선택할 수 있는 클레임 공급자의 목록을 제공합니다.|

**클레임공급자선택** 요소에는 다음 특성이 포함되어 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 디스플레이 옵션| 예 | 단일 클레임 공급자 선택을 사용할 수 있는 서비스 케이스의 동작을 제어합니다. 가능한 값: `DoNotShowSingleProvider` (기본값) - 사용자가 페더레이션된 ID 공급자로 즉시 리디렉션됩니다. 또는 `ShowSingleProvider` Azure AD B2C는 단일 ID 공급자 선택과 함께 로그인 페이지를 제공합니다. 이 특성을 사용하려면 콘텐츠 정의 `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` [버전이상이어야](page-layout.md) 합니다.|

**ClaimsProviderSelection** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | 예 | 클레임 공급자 선택 항목의 다음 오케스트레이션 단계에서 실행되는 클레임 교환의 식별자입니다. 이 특성 또는 ValidationClaimsExchangeId 특성 중 하나만 지정해야 하며 둘 다 지정해서는 안 됩니다. |
| ValidationClaimsExchangeId | 예 | 클레임 공급자 선택 항목의 유효성을 검사하기 위해 현재 오케스트레이션 단계에서 실행되는 클레임 교환의 식별자입니다. 이 특성 또는 TargetClaimsExchangeId 특성 중 하나만 지정해야 하며 둘 다 지정해서는 안 됩니다. |

### <a name="claimsproviderselection-example"></a>ClaimsProviderSelection 예제

다음 오케스트레이션 단계에서 사용자는 Facebook, LinkedIn, Twitter, Google 또는 로컬 계정으로 로그인하도록 선택할 수 있습니다. 사용자가 소셜 ID 공급자 중 하나를 선택하면 `TargetClaimsExchangeId` 특성에 지정된 선택한 클레임 교환을 사용하여 두 번째 오케스트레이션 단계가 실행됩니다. 두 번째 오케스트레이션 단계에서는 로그인 프로세스를 완료할 수 있도록 사용자를 소셜 ID 공급자로 리디렉션합니다. 사용자가 로컬 계정을 사용하여 로그인하도록 선택하는 경우 Azure AD B2C는 같은 오케스트레이션 단계(같은 등록 또는 로그인 페이지)로 유지되며 두 번째 오케스트레이션 단계는 건너뜁니다.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 사용 중인 기술 프로필별로 선택한 ClaimsProviderSelection에 따라 클라이언트를 리디렉션하거나 교환 클레임에 대한 서버 호출을 수행합니다. |

**ClaimsExchange** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 클레임 교환 단계의 식별자입니다. 식별자는 정책의 클레임 공급자 선택 단계에서 클레임 교환을 참조하는 데 사용됩니다. |
| TechnicalProfileReferenceId | yes | 실행할 기술 프로필의 식별자입니다. |
