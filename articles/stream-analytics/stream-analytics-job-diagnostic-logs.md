---
title: 진단 로그를 사용하여 Azure Stream Analytics 문제 해결
description: 이 문서에서는 Azure Stream Analytics에서 진단 로그를 분석하는 방법을 설명합니다.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: cdb6629441becd0a8356debe3360830ff11a7a9d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398415"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>진단 로그를 사용하여 Azure Stream Analytics 문제 해결

경우에 따라 Azure Stream Analytics 작업이 예기치 않게 처리를 중지합니다. 이 이벤트 종류의 문제를 해결하는 것이 중요합니다. 오류는 예기치 않은 쿼리 결과, 디바이스에 대한 연결 또는 예기치 않은 서비스 중단으로 인해 발생할 수 있습니다. Stream Analytics에서 진단 로그를 사용하면 이와 같은 오류가 발생했을 때 문제 원인을 파악하고 복구 시간을 단축할 수 있습니다.

디버깅 및 모니터링에 크게 도움이 되기 때문에 모든 작업에 대해 진단 로그를 사용하도록 설정하는 것이 좋습니다.

## <a name="log-types"></a>로그 형식

Stream Analytics에서는 다음과 같은 두 가지 형식의 로그를 제공합니다.

* [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)(Always On) - 작업에서 수행되는 조작에 대한 인사이트를 제공합니다.

* [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)(구성 가능) - 작업과 관련하여 발생하는 모든 항목에 대해 더 다양한 인사이트를 제공합니다. 진단 로그는 작업이 생성될 때 시작되고 작업이 삭제될 때 종료됩니다. 작업이 업데이트되고 실행되는 동안 이벤트를 처리합니다.

> [!NOTE]
> Azure 저장소, Azure 이벤트 허브 및 Azure Monitor 로그와 같은 서비스를 사용하여 부적합 데이터를 분석할 수 있습니다. 그러한 서비스에 대한 가격 책정 모델에 따라 요금이 청구됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>활동 로그를 사용하여 디버그

활동 로그는 기본적으로 켜져 있고 Stream Analytics 작업에서 수행한 조작에 대한 높은 수준의 인사이트를 제공합니다. 활동 로그에 있는 정보는 작업에 영향을 미치는 문제의 근본 원인을 찾는 데 도움이 될 수 있습니다. Stream Analytics의 활동 로그를 사용하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인하고 **개요** 아래에서 **활동 로그**를 선택합니다.

   ![Stream Analytics 활동 로그](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 수행된 조작 목록을 볼 수 있습니다. 작업 실패의 원인이 된 조작에는 빨간색 정보 풍선이 있습니다.

3. 해당 요약 보기를 보려면 조작을 클릭합니다. 여기에는 보통 제한된 정보가 표시됩니다. 조작에 대해 자세히 알아보려면 **JSON**을 클릭합니다.

   ![Stream Analytics 활동 로그 조작 요약](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. 아래로 스크롤하여 실패한 조작의 원인이 된 오류의 세부 정보를 제공하는 JSON의 **속성** 섹션으로 이동합니다. 이 예제에서는 범위를 벗어난 위도 값의 런타임 오류로 인해 조작이 실패했습니다. Stream Analytics 작업에서 처리되는 데이터의 불일치로 인해 데이터 오류가 발생합니다. 다양한 [입력 및 출력 데이터 오류와 데이터 오류](https://docs.microsoft.com/azure/stream-analytics/data-errors)발생 이유에 대해 알아볼 수 있습니다.

   ![JSON 오류 세부 정보](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. JSON의 오류 메시지에 따라 정정 작업을 수행할 수 있습니다. 이 예제에서는 쿼리에 추가해야 하는 위도 값이 -90도-90도 사이인지 확인합니다.

6. 활동 로그의 오류 메시지가 근본 원인을 식별하는 데 도움이 되지 않으면 진단 로그를 활성화하고 Azure Monitor 로그를 사용합니다.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Azure 모니터 로그에 진단 보내기

진단 로그를 켜고 Azure Monitor 로그로 보내는 것이 좋습니다. 진단 로그는 기본적으로 **해제**되어 있습니다. 진단 로그를 켜려면 다음과 같은 단계를 완료합니다.

1.  Azure Portal에 로그인하고 Stream Analytics 작업으로 이동합니다. **모니터링** 아래에서 **진단 로그**를 선택합니다. 다음으로, **진단 켜기**를 선택합니다.

    ![진단 로그에 대한 블레이드 탐색](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  **진단 설정**에서 **이름**을 만들고 **Log Analytics에 보내기** 옆에 있는 상자를 선택합니다. 그런 다음, 기존 **Log Analytics 작업 영역**을 추가하거나 새 작업 영역을 만듭니다. **로그** 아래에서 **실행** 및 **작성** 상자를 선택하고 **메트릭** 아래에서 **AllMetrics** 상자를 선택합니다. **저장**을 클릭합니다. 추가 비용을 방지하려면 스트림 애널리틱스 작업과 동일한 Azure 리전에서 로그 분석 작업 영역을 사용하는 것이 좋습니다.

    ![진단 로그 설정](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Stream Analytics 작업이 시작되면 진단 로그가 Log Analytics 작업 영역으로 라우팅됩니다. 작업에 대한 진단 로그를 보려면 **모니터링** 섹션에서 **로그를 선택합니다.**

   ![모니터링 중인 진단 로그](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics는 관심 있는 로그를 쉽게 검색할 수 있는 미리 정의된 쿼리를 제공합니다. 3 가지 범주는 **일반**, **입력 데이터 오류** 및 출력 데이터 **오류입니다.** 예를 들어 지난 7일 동안 작업의 모든 오류에 대한 요약을 보려면 미리 정의된 적절한 쿼리 **실행을** 선택할 수 있습니다. 

   ![모니터링 중인 진단 로그](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![로그 결과](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>진단 로그 범주

Azure Stream Analytics는 두 가지 진단 로그 범주를 캡처합니다.

* **작성**: 작업 생성, 입력 및 출력 추가 및 삭제, 쿼리 추가 및 업데이트, 작업 시작 또는 중지와 같은 작업 작성 작업과 관련된 로그 이벤트를 캡처합니다.

* **실행**: 작업 실행 중에 발생하는 이벤트를 캡처합니다.
    * 연결 오류
    * 다음을 포함하는 데이터 처리 오류
        * 쿼리 정의를 따르지 않는 이벤트(필드 형식 및 값 불일치, 필드 누락 등)
        * 식 평가 오류
    * 다른 이벤트 및 오류

## <a name="diagnostics-logs-schema"></a>진단 로그 스키마

모든 로그는 JSON 형식으로 저장됩니다. 각 항목에는 다음과 같은 일반적인 문자열 필드가 있습니다.

속성 | Description
------- | -------
time | 로그의 타임스탬프(UTC)입니다.
resourceId | 작업이 수행되는 리소스의 ID(대문자)입니다. 여기에는 구독 ID, 리소스 그룹 및 작업 이름이 포함됩니다. 예: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | 로그 범주로, **실행** 또는 **작성** 중 하나입니다.
operationName | 기록된 작업의 이름 예를 들어 **이벤트 보내기: SQL Output은 mysqloutput 에 실패를 기록합니다.**
상태 | 작업의 상태입니다. 예: **실패** 또는 **성공**.
level | 로그 수준. 예: **오류**, **경고** 또는 **정보**.
properties | 로그 항목별 세부 정보로, JSON 문자열로 직렬화됩니다. 자세한 내용은 이 문서의 다음 섹션을 참조하세요.

### <a name="execution-log-properties-schema"></a>실행 로그 속성 스키마

실행 로그에는 Stream Analytics 작업 실행 중에 발생한 이벤트에 대한 정보가 포함됩니다. 속성스키마는 이벤트가 데이터 오류인지 제네릭 이벤트인지에 따라 달라집니다.

### <a name="data-errors"></a>데이터 오류

작업이 데이터를 처리하는 동안 발생한 오류는 이 로그 범주에 속합니다. 이러한 로그는 데이터 읽기, serialization 및 쓰기 작업 도중에 가장 자주 생성됩니다. 이러한 로그는 연결 오류를 포함하지 않습니다. 연결 오류는 일반 이벤트로 처리됩니다. 다양한 입력 및 출력 데이터 [오류의](https://docs.microsoft.com/azure/stream-analytics/data-errors)원인에 대해 자세히 알아볼 수 있습니다.

속성 | Description
------- | -------
원본 | 오류가 발생한 작업 입력 또는 출력의 이름입니다.
메시지 | 오류와 연결된 메시지
Type | 오류 유형입니다. 예: **DataConversionError**, **CsvParserError** 또는 **ServiceBusPropertyColumnMissingError**.
데이터 | 오류 출처를 정확히 찾는 데 도움이 되는 데이터를 포함합니다. 크기에 따라 잘릴 수 있습니다.

**operationName** 값에 따라 데이터 오류의 스키마는 다음과 같습니다.

* **이벤트 읽기** 작업 중에 발생 합니다. 이는 데이터 입력 시 쿼리 스키마를 충족하지 않을 때 다음과 같은 이유 중 하나로 발생합니다.

   * *이벤트 (역)직렬화 도중 형식 불일치*: 오류를 발생시키는 필드를 식별합니다.

   * *이벤트를 읽을 수 없음, 잘못된 serialization*: 입력 데이터에서 오류가 발생하는 위치에 대한 정보를 나열합니다. Blob 입력에 대한 Blob 이름, 오프셋 및 데이터 샘플이 포함됩니다.

* **보내기 이벤트는** 쓰기 작업 중에 발생합니다. 오류를 발생시키는 스트리밍 이벤트를 식별합니다.

### <a name="generic-events"></a>일반 이벤트

일반 이벤트는 다른 모든 항목을 처리합니다.

속성 | Description
-------- | --------
Error | (선택 사항) 오류 정보입니다. 일반적으로 예외 정보입니다(사용 가능한 경우).
메시지| 로그 메시지
Type | 메시지 형식입니다. 내부 오류 분류에 매핑합니다. 예: **JobValidationError** 또는 **BlobOutputAdapterInitializationFailure**.
상관관계 ID | 작업 실행을 고유하게 식별하는 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). 작업 시작 시간부터 작업이 중지될 때까지 모든 실행 로그 항목에는 동일한 **상관 관계 ID** 값이 있습니다.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [스트림 분석 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [스트림 분석 데이터 오류](https://docs.microsoft.com/azure/stream-analytics/data-errors)
