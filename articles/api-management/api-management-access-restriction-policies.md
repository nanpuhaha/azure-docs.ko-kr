---
title: Azure API Management 액세스 제한 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 액세스 제한 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266118"
---
# <a name="api-management-access-restriction-policies"></a>API Management 액세스 제한 정책

이 문서에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a>액세스 제한 정책

-   [HTTP 헤더 확인](api-management-access-restriction-policies.md#CheckHTTPHeader) - HTTP 헤더의 존재 및/또는 값을 적용합니다.
-   [구독으로 호출 속도 제한](api-management-access-restriction-policies.md#LimitCallRate) - 구독을 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
-   [키로 호출 속도 제한](#LimitCallRateByKey) - 키를 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
-   [호출자 IP 제한](api-management-access-restriction-policies.md#RestrictCallerIPs) - 특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.
-   [구독으로 사용 할당량 설정](api-management-access-restriction-policies.md#SetUsageQuota) - 구독을 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
-   [키로 사용 할당량 설정](#SetUsageQuotaByKey) - 키를 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
-   [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) - 지정된 HTTP 헤더 또는 지정된 쿼리 매개 변수에서 추출된 JWT의 존재 및 유효성을 적용합니다.

> [!TIP]
> 다른 목적으로 다른 범위에서 액세스 제한 정책을 사용할 수 있습니다. 예를 들어 API 수준에 `validate-jwt` 정책을 적용하여 AAD 인증을 사용하여 전체 API를 보호하거나 API 작업 수준에 `claims` 적용하여 보다 세분화된 제어에 사용할 수 있습니다.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> HTTP 헤더 확인

`check-header` 정책을 사용하여 요청에 HTTP 헤더가 지정되도록 합니다. 필요에 따라 헤더에 특정 값이 있는지, 허용되는 범위의 값인지를 확인할 수 있습니다. 확인에 실패하면 정책에서 요청 처리를 종료하고 정책에 지정된 HTTP 상태 코드 및 오류 메시지를 반환합니다.

### <a name="policy-statement"></a>정책 문

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>예제

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>요소

| 이름         | 설명                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | 루트 요소입니다.                                                                                                                                 | yes      |
| value        | 허용된 HTTP 헤더 값입니다. 여러 값 요소가 지정된 경우 값 중 하나와 일치하면 확인에 성공한 것으로 간주됩니다. | 예       |

### <a name="attributes"></a>특성

| 이름                       | 설명                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | 헤더가 없거나 잘못된 값이 있는 경우 HTTP 응답 본문에 반환할 오류 메시지입니다. 이 메시지는 적절히 이스케이프된 특수 문자를 포함해야 합니다. | yes      | 해당 없음     |
| failed-check-httpcode      | 헤더가 없거나 잘못된 값이 있는 경우 반환할 HTTP 상태 코드입니다.                                                                                        | yes      | 해당 없음     |
| header-name                | 확인할 HTTP 헤더의 이름입니다.                                                                                                                                  | yes      | 해당 없음     |
| ignore-case                | True 또는 False로 설정할 수 있습니다. True로 설정되면 헤더 값을 허용 가능한 값 집합과 비교할 때 대소문자가 무시됩니다.                                    | yes      | 해당 없음     |

### <a name="usage"></a>사용

이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound

-   **정책 범위:** 모든 범위

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> 구독으로 호출 속도 제한

`rate-limit` 정책은 호출 속도를 지정된 기간당 지정된 숫자로 제한하여 구독 하나당 최대 API 사용을 차단합니다. 이 정책이 트리거되는 경우 호출자는 `429 Too Many Requests` 응답 상태 코드를 수신합니다.

> [!IMPORTANT]
> 이 정책은 정책 문서당 한 번만 사용할 수 있습니다.
>
> 이 정책의 정책 특성에는 [정책 식](api-management-policy-expressions.md)을 사용할 수 없습니다.

> [!CAUTION]
> 제한 아키텍처의 분산 특성으로 인해 속도 제한은 완전히 정확하지 않습니다. 구성된 요청과 허용된 요청의 실제 수 의 차이는 요청 볼륨 및 속도, 백 엔드 대기 시간 및 기타 요인에 따라 다릅니다.

### <a name="policy-statement"></a>정책 문

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>예제

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름       | 설명                                                                                                                                                                                                                                                                                              | 필수 |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | 루트 요소입니다.                                                                                                                                                                                                                                                                                            | yes      |
| api        | 이러한 요소 중 하나 이상을 추가하여 제품 내의 API에 통화 속도 제한을 적용합니다. 제품 및 API 호출 속도 제한은 독립적으로 적용됩니다. API는 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다.                    | 예       |
| operation(작업)  | 이러한 요소 중 하나 이상을 추가하여 API 내의 작업에 통화 속도 제한을 적용합니다. 제품, API 및 작업 호출 속도 제한은 독립적으로 적용됩니다. 작업은 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다. | 예       |

### <a name="attributes"></a>특성

| 이름           | 설명                                                                                           | 필수 | 기본값 |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | 속도 제한을 적용할 API의 이름입니다.                                                | yes      | 해당 없음     |
| calls          | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다. | yes      | 해당 없음     |
| renewal-period | 할당량이 재설정되는 초 단위의 기간입니다.                                              | yes      | 해당 없음     |

### <a name="usage"></a>사용

이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 제품, API, 작동

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> 키로 호출 속도 제한

> [!IMPORTANT]
> 이 기능은 API Management의 **Consumption** 계층에서 사용할 수 없습니다.

`rate-limit-by-key` 정책은 호출 속도를 지정된 기간당 지정된 숫자로 제한하여 키 하나당 최대 API 사용을 차단합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다. 제한에 포함될 요청을 지정하기 위해 선택적 증분 조건을 추가할 수 있습니다. 이 정책이 트리거되는 경우 호출자는 `429 Too Many Requests` 응답 상태 코드를 수신합니다.

이 정책에 대한 자세한 내용과 예제는 [Azure API Management로 고급 요청 제한](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)을 참조하세요.

> [!CAUTION]
> 제한 아키텍처의 분산 특성으로 인해 속도 제한은 완전히 정확하지 않습니다. 구성된 요청과 허용된 요청의 실제 수 의 차이는 요청 볼륨 및 속도, 백 엔드 대기 시간 및 기타 요인에 따라 다릅니다.

### <a name="policy-statement"></a>정책 문

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>예제

다음 예제에서 속도 제한은 호출자 IP 주소를 키로 사용합니다.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름              | 설명   | 필수 |
| ----------------- | ------------- | -------- |
| 키별 속도 제한 | 루트 요소입니다. | yes      |

### <a name="attributes"></a>특성

| 이름                | 설명                                                                                           | 필수 | 기본값 |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다. | yes      | 해당 없음     |
| counter-key         | 속도 제한 정책에 사용할 키입니다.                                                             | yes      | 해당 없음     |
| increment-condition | 요청을 할당량에 포함할지를 지정하는 부울 식입니다(`true`).        | 예       | 해당 없음     |
| renewal-period      | 할당량이 재설정되는 초 단위의 기간입니다.                                              | yes      | 해당 없음     |

### <a name="usage"></a>사용

이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> 호출자 IP 제한

`ip-filter` 정책은 특정 IP 주소 및/또는 주소 범위에서 호출을 필터링(허용/거부)합니다.

### <a name="policy-statement"></a>정책 문

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>예제

다음 예제에서는 이 정책은 단일 IP 주소 또는 지정된 IP 주소 범위에서 오는 요청만 허용합니다.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>요소

| 이름                                      | 설명                                         | 필수                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | 루트 요소입니다.                                       | yes                                                            |
| address                                   | 필터링할 단일 IP 주소를 지정합니다.   | 하나 이상의 `address` 또는 `address-range` 요소가 필요합니다. |
| address-range from="address" to="address" | 필터링할 IP 주소 범위를 지정합니다. | 하나 이상의 `address` 또는 `address-range` 요소가 필요합니다. |

### <a name="attributes"></a>특성

| 이름                                      | 설명                                                                                 | 필수                                           | 기본값 |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | 액세스를 허용 또는 거부할 IP 주소 범위                                        | `address-range` 요소가 사용될 때 필요합니다. | 해당 없음     |
| ip-filter action="allow &#124; forbid"    | 지정된 IP 주소 및 범위에 대해 호출을 허용해야 할지 여부를 지정합니다. | yes                                                | 해당 없음     |

### <a name="usage"></a>사용

이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** 모든 범위

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> 구독으로 사용 할당량 설정

`quota` 정책은 구독을 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.

> [!IMPORTANT]
> 이 정책은 정책 문서당 한 번만 사용할 수 있습니다.
>
> 이 정책의 정책 특성에는 [정책 식](api-management-policy-expressions.md)을 사용할 수 없습니다.

### <a name="policy-statement"></a>정책 문

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>예제

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름      | 설명                                                                                                                                                                                                                                                                                  | 필수 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | 루트 요소입니다.                                                                                                                                                                                                                                                                                | yes      |
| api       | 이러한 요소 중 하나 이상을 추가하여 제품 내의 API에 통화 할당량을 부과합니다. 제품 및 API 호출 할당량은 독립적으로 적용됩니다. API는 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다.                    | 예       |
| operation(작업) | 이러한 요소 중 하나 이상을 추가하여 API 내의 작업에 호출 할당량을 적용합니다. 제품, API 및 작업 호출 할당량은 독립적으로 적용됩니다. 작업은 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다. | 예       |

### <a name="attributes"></a>특성

| 이름           | 설명                                                                                               | 필수                                                         | 기본값 |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | 할당량이 적용되는 API 또는 작업의 이름입니다.                                             | yes                                                              | 해당 없음     |
| bandwidth      | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 킬로바이트 수입니다. | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| calls          | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다.     | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| renewal-period | 할당량이 재설정되는 초 단위의 기간입니다.                                                  | yes                                                              | 해당 없음     |

### <a name="usage"></a>사용

이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** product

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> 키로 사용 할당량 설정

> [!IMPORTANT]
> 이 기능은 API Management의 **Consumption** 계층에서 사용할 수 없습니다.

`quota-by-key` 정책은 키를 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다. 할당량에 포함될 요청을 지정하기 위해 선택적 증분 조건을 추가할 수 있습니다. 여러 정책이 동일한 키 값을 증가시키는 경우 요청당 한 번만 증가됩니다. 요청 제한에 도달하면 호출자는 `403 Forbidden` 응답 상태 코드를 수신합니다.

이 정책에 대한 자세한 내용과 예제는 [Azure API Management로 고급 요청 제한](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)을 참조하세요.

### <a name="policy-statement"></a>정책 문

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>예제

다음 예제에서 할당량은 호출자 IP 주소를 키로 사용합니다.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름  | 설명   | 필수 |
| ----- | ------------- | -------- |
| quota | 루트 요소입니다. | yes      |

### <a name="attributes"></a>특성

| 이름                | 설명                                                                                               | 필수                                                         | 기본값 |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 킬로바이트 수입니다. | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| calls               | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다.     | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| counter-key         | 할당량 정책에 사용할 키입니다.                                                                      | yes                                                              | 해당 없음     |
| increment-condition | 요청을 할당량에 포함할지를 지정하는 부울 식입니다(`true`).             | 예                                                               | 해당 없음     |
| renewal-period      | 할당량이 재설정되는 초 단위의 기간입니다.                                                  | yes                                                              | 해당 없음     |

### <a name="usage"></a>사용

이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** 모든 범위

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> JWT 유효성 검사

`validate-jwt` 정책은 지정된 HTTP 헤더 또는 지정된 쿼리 매개 변수에서 추출된 JWT의 존재 및 유효성을 적용합니다.

> [!IMPORTANT]
> `validate-jwt` 정책에서는 `require-expiration-time` 특정이 지정되지 않고 `false`로 설정된 경우가 아니면 `exp` 등록 클레임이 JWT 토큰에 포함되어야 합니다.
> `validate-jwt` 정책은 HS256 및 RS256 서명 알고리즘을 지원합니다. HS256의 경우 키를 base64 인코딩 양식으로 정책 내에 인라인으로 제공해야 합니다. RS256의 경우 Open ID 구성 엔드포인트를 통해 키를 제공해야 합니다.
> `validate-jwt` 정책은 A128CBC-HS256, A192CBC-HS384, A256CBC-HS512 암호화 알고리즘을 사용하여 대칭 키로 암호화된 토큰을 지원합니다.

### <a name="policy-statement"></a>정책 문

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>예

#### <a name="simple-token-validation"></a>간단한 토큰 유효성 검사

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory 토큰 유효성 검사

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C 토큰 유효성 검사

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>토큰 클레임 기반 작업에 대한 액세스 권한 부여

이 예제에서는 [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 토큰 클레임 값을 기반으로 작업에 대한 액세스를 승인하는 방법을 보여 주어집니다.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>요소

| 요소             | 설명                                                                                                                                                                                                                                                                                                                                           | 필수 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | 루트 요소입니다.                                                                                                                                                                                                                                                                                                                                         | yes      |
| audiences           | 토큰에 제공할 수 있는 허용 가능한 대상 그룹 클레임 목록을 포함합니다. 여러 대상 그룹 값이 있는 경우 각 값은 모든 값이 소진(이 경우 유효성 검사 실패)되거나 한 값이 성공할 때까지 시도됩니다. 한 명 이상의 대상 그룹을 지정해야 합니다.                                                                     | 예       |
| issuer-signing-keys | 서명된 토큰의 유효성을 검사하는 데 사용되는 Base64 인코딩 보안 키의 목록입니다. 보안 키가 여러 개 있는 경우 각 키는 모든 키가 소진(이 경우 유효성 검사 실패)되거나 한 개 키가 성공할 때까지 시도됩니다(토큰 롤오버에 유용함). 키 요소에는 `kid` 클레임에 대한 일치에 사용된 `id` 특성(선택적)이 포함됩니다.               | 예       |
| decryption-keys     | 토큰의 암호를 해독하는 데 사용되는 Base64로 인코딩된 키의 목록입니다. 여러 개의 보안 키가 있는 경우 모든 키가 사용되거나(유효성 검사가 실패한 경우) 키가 성공할 때까지 각 키를 시도합니다. 키 요소에는 `kid` 클레임에 대한 일치에 사용된 `id` 특성(선택적)이 포함됩니다.                                                 | 예       |
| issuers             | 토큰을 발행한 허용 가능한 보안 주체의 목록입니다. 여러 발급자 값이 있는 경우 각 값은 모든 값이 소진(이 경우 유효성 검사 실패)되거나 한 값이 성공할 때까지 시도됩니다.                                                                                                                                         | 예       |
| openid-config       | 서명 키 및 발급자를 획득할 수 있는 준수 Open ID 구성 엔드포인트를 지정하는 데 사용됩니다.                                                                                                                                                                                                                        | 예       |
| required-claims     | 유효성을 고려할 토큰에 있을 것으로 예상되는 클레임 목록을 포함합니다. `match` 특성이 `all`로 설정되면 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 합니다. `match` 특성이 `any`로 설정되면 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 합니다. | 예       |

### <a name="attributes"></a>특성

| 이름                            | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                            | 필수                                                                         | 기본값                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | Timespan입니다. 토큰 발급자와 API Management 인스턴스의 시스템 시계 간 최대 예상 시간 차이를 지정하는 데 사용합니다.                                                                                                                                                                                                                                                                                                               | 예                                                                               | 0초                                                                         |
| failed-validation-error-message | JWT가 유효성 검사를 통과하지 못한 경우 HTTP 응답 본문에 반환할 오류 메시지입니다. 이 메시지는 적절히 이스케이프된 특수 문자를 포함해야 합니다.                                                                                                                                                                                                                                                                                                 | 예                                                                               | 기본 오류 메시지는 유효성 검사 문제에 따라 달라집니다(예: "JWT not present(JWT 없음)"). |
| failed-validation-httpcode      | JWT가 유효성 검사를 통과하지 못한 경우 반환할 HTTP 상태 코드입니다.                                                                                                                                                                                                                                                                                                                                                                                         | 예                                                                               | 401                                                                               |
| header-name                     | 토큰을 보유하는 HTTP 헤더의 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                         | 을 `query-parameter-name` `header-name`중 `token-value` 하나 또는 지정해야 합니다. | 해당 없음                                                                               |
| query-parameter-name            | 토큰을 보유하는 쿼리 매개 변수의 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                     | 을 `query-parameter-name` `header-name`중 `token-value` 하나 또는 지정해야 합니다. | 해당 없음                                                                               |
| 토큰 값                     | JWT 토큰이 포함된 문자열을 반환하는 표현식                                                                                                                                                                                                                                                                                                                                                                                                     | 을 `query-parameter-name` `header-name`중 `token-value` 하나 또는 지정해야 합니다. | 해당 없음                                                                               |
| id                              | `key` 요소에 있는 `id` 특성을 통해 토큰(있는 경우)에 있는 `kid` 클레임과 일치시킬 문자열을 지정하여 서명 유효성 검사에 사용할 적절한 키를 확인할 수 있습니다.                                                                                                                                                                                                                                           | 예                                                                               | 해당 없음                                                                               |
| match                           | `claim` 요소에 있는 `match` 특성에 따라 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 하는지가 지정됩니다. 가능한 값은 다음과 같습니다.<br /><br /> - `all` - 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 합니다.<br /><br /> - `any` - 유효성 검사 성공을 위해 하나 이상의 클레임 값이 토큰에 표시되어야 합니다.                                                       | 예                                                                               | all                                                                               |
| require-expiration-time         | Boolean입니다. 토큰에 만료 클레임이 필요한지를 지정합니다.                                                                                                                                                                                                                                                                                                                                                                               | 예                                                                               | true                                                                              |
| require-scheme                  | 토큰 구성표의 이름(예: "베어러")입니다. 이 특성이 설치되면 정책은 지정된 스키마가 권한 부여 헤더 값에 있는지를 확인합니다.                                                                                                                                                                                                                                                                                    | 예                                                                               | 해당 없음                                                                               |
| require-signed-tokens           | Boolean입니다. 토큰에 서명이 필요한지를 지정합니다.                                                                                                                                                                                                                                                                                                                                                                                           | 예                                                                               | true                                                                              |
| 구분 기호                       | 문자열입니다. 다중 값 클레임에서 값 집합을 추출하는 데 사용된 구분 기호(예: “,”)를 지정합니다.                                                                                                                                                                                                                                                                                                                                          | 예                                                                               | 해당 없음                                                                               |
| url                             | Open ID 구성 메타데이터를 가져올 수 있는 Open ID 구성 엔드포인트 URL입니다. 응답은 URL `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`에서 정의된 사양을 따라야 합니다. Azure Active Directory의 경우 다음 URL을 사용합니다. `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` 여기서 사용자의 디렉터리 테넌트 이름을 대체합니다(예: `contoso.onmicrosoft.com`). | yes                                                                              | 해당 없음                                                                               |
| 출력 토큰 변수 이름      | 문자열입니다. 성공적인 토큰 유효성 검사 시 형식의 [`Jwt`](api-management-policy-expressions.md) 개체로 토큰 값을 받을 컨텍스트 변수의 이름                                                                                                                                                                                                                                                                                     | 예                                                                               | 해당 없음                                                                               |

### <a name="usage"></a>사용

이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** 모든 범위

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

-   [API Management의 정책](api-management-howto-policies.md)
-   [API 변환](transform-api.md)
-   [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
-   [정책 샘플](policy-samples.md)
