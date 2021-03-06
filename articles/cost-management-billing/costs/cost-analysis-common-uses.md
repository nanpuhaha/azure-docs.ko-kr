---
title: Azure Cost Management에서의 일반적인 비용 분석 사용
description: 이 문서에서는 Azure Cost Management에서 일반적인 비용 분석 작업에 대한 결과를 가져오는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 03/31/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: a333cad51e6fc089e7e6994c7b89210b12686cd5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520873"
---
# <a name="common-cost-analysis-uses"></a>일반적인 비용 분석 사용

Azure Cost Management 사용자는 다른 사람들이 질문하는 문제에 대한 답변이 필요합니다. 이 문서에서는 Cost Management에서 일반적인 비용 분석 작업에 대한 결과를 가져오는 과정을 안내합니다.

## <a name="view-cost-breakdown-by-azure-service"></a>Azure 서비스별 비용 분석 보기

Azure 서비스별 비용 보기는 비용이 가장 많이 드는 인프라 파트를 더 잘 이해하는 데 유용합니다. 예를 들어 VM 컴퓨팅 비용은 적을 수 있습니다. 그러나 VM에서 내보내는 정보의 양으로 인해 상당한 네트워킹 비용이 발생할 수 있습니다. 필요에 따라 서비스 사용량을 조정할 수 있도록 Azure 서비스의 기본 비용 드라이버를 이해하는 것이 중요합니다.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **Cost Management** > **비용 분석**.
1. **서비스별 비용**을 선택한 다음, **서비스 계층**별로 그룹화합니다.
1. 보기를 **테이블**로 변경합니다.

[![Azure 서비스별 비용 분석](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Azure 리소스별 비용 분석 보기

서비스는 Azure 리소스를 사용하여 빌드됩니다. 리소스를 기준으로 비용을 검토하면 기본 비용 기여자를 빠르게 식별하는 데 도움이 됩니다. 서비스에 비용이 너무 많이 드는 리소스가 있는 경우 비용을 줄이기 위해 변경을 고려하는 것이 좋습니다.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **Cost Management** > **비용 분석**.
1. **리소스별 비용**을 선택합니다.
1. 보기를 **테이블**로 변경합니다.

[![Azure 리소스별 비용 분석 보기](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>선택한 차원별 비용 분석 보기

차원을 사용하면 요금에 표시된 다양한 메타데이터 값에 따라 비용을 구성할 수 있습니다. 예를 들어, 위치별로 비용을 그룹화할 수 있습니다.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **Cost Management** > **비용 분석**.
1. **그룹화 기준** 필터를 선택합니다.  
    [![항목별 그룹화 선택](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. 필요에 따라 나중에 사용할 수 있도록 보기를 저장합니다.
1. 그래프 아래에 있는 원형 차트를 클릭하면 자세한 데이터를 볼 수 있습니다.  
    [![선택한 차원별 비용 분석 보기](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>일별 또는 월별 비용 보기

일별 및 월별 비용을 살펴보면 비용이 더 높은 주 또는 연도가 있는지 파악하는 데 도움이 될 수 있습니다. 연휴 기간에 고객 트래픽이 더 많은 경우 그에 따라 Azure 비용도 증가하나요? 금요일이 월요일보다 비용이 더 많이 드나요?

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **Cost Management** > **비용 분석**.
1. **세분성**을 **월별** 또는 **일별**로 설정합니다.

[![일별 비용 보기](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>스폿 VM 요금 보기

스폿 VM은 중단을 처리할 수 있는 워크로드에 대한 비용을 크게 절감할 수 있습니다. 워크로드는 사용되지 않는 Azure 용량으로 실행됩니다. 스폿 VM은 언제든지 제거할 수 있으므로 상당한 비용이 절감됩니다. 스폿 VM 요금을 보려면 다음 단계를 따르세요.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 예를 들면 **Cost Management + 청구** > **Cost Management** > **비용 분석**입니다.
2. **가격 책정 모델: 스폿**에 대한 필터를 추가합니다.

![스폿 VM 필터를 보여주는 예](./media/cost-analysis-common-uses/spot-vm-filter.png)

가격 책정 모델 차원은 요청 시 요금 및 예약 요금을 확인하는 데도 사용됩니다.

## <a name="view-your-reservation-charges"></a>예약 요금 보기

예약 인스턴스는 Azure를 사용하여 비용을 절감할 수 있는 방법을 제공합니다. 예약은 정해진 리소스 수에 대한 비용을 선불로 결제하고 특정 기간 동안 사용하는 것입니다. 비용 분석은 청구서에 표시되는 대로 요금을 보여줍니다. 요금은 실제 비용 또는 예약 기간 동안의 분할 상환 비용으로 표시됩니다.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 예를 들면 **Cost Management + 청구** > **Cost Management** > **비용 분석**입니다.
1. **가격 책정 모델: 예약**에 대한 필터를 추가합니다.
1. **범위** 아래에서 표시되는 비용 옆에 있는 아래쪽 화살표 기호를 클릭하고 **실제 비용** 또는 **분할 상환 비용** 메트릭을 선택합니다.

![비용 메트릭 선택](./media/cost-analysis-common-uses/metric-cost.png)

각 메트릭은 예약 요금에 대한 데이터가 표시되는 방식에 영향을 줍니다.

**실제 비용** - 청구서에 표시되는 대로 구매건을 표시합니다. 예를 들어 1월에 1년 예약을 $1,200로 구매한 경우 비용 분석에서 예약의 1월 비용이 $1,200로 표시됩니다. 해당 연도의 다른 월에 대한 예약 비용은 표시되지 않습니다. VM을 기준으로 실제 비용을 그룹화하면 지정된 월에 예약 혜택을 받은 VM의 해당 월 비용은 0이 됩니다.

**분할 상환 비용** - 예약 기간 동안 분할 상환 비용으로 분할된 예약 구매건을 보여줍니다. 위와 동일한 예에 적용해 보면 1월에 1년 예약건을 $1,200로 구매한 경우 비용 분석에는 1년 동안 매월 $100의 비용이 청구되는 것으로 표시됩니다. 이 예에서 VM별로 비용을 그룹화하면 예약 혜택을 받은 각 VM에서 발생하는 비용을 확인할 수 있습니다.

## <a name="view-your-reservation-utilization"></a>예약 사용률 보기

예약을 구매한 후에는 지불한 비용을 제대로 활용할 수 있도록 사용률을 추적하는 것이 중요합니다. 예를 들어 1년 동안 10개의 VM을 구매하고 그 중 5개만 사용한 경우 기본적으로 구매 비용 중 절반을 낭비한 것입니다. 다음 두 가지 방법으로 사용률을 평가할 수 있습니다.

### <a name="view-unused-ri-costs-in-cost-analysis"></a>비용 분석에서 사용되지 않은 RI 비용 보기

예약 구매와 관련하여 매월 얼마나 많은 비용이 낭비되고 있는지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에서 예약이 적용되는 범위에 대한 비용 분석으로 이동합니다. 예를 들면 **Cost Management + 청구** > **Cost Management** > **비용 분석**입니다.
1. **가격 책정 모델: 예약**에 대한 필터를 추가합니다.
1. **분할 상환 비용** 보기를 선택합니다.
1. 세분성을 **월별**로 설정합니다.
1. 기간을 현재 연도 또는 예약 기간으로 설정합니다.
1. 차트 유형을 **세로 막대형(누적)** 으로 설정합니다.
1. **요금 유형**으로 요금을 그룹화합니다.
1. `unusedreservation` 값의 결과를 검토합니다.

[![예약 사용을 보여주는 예](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>예약 사용률 보기

자세한 내용은 [예약 사용 최적화](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use)를 참조하세요.

## <a name="view-costs-for-a-specific-tag"></a>특정 태그에 대한 비용 보기

많은 Azure 사용자는 비용을 더 잘 분류하기 위해 비용 센터나 개발 환경(생산 및 테스트)과 같은 리소스에 태그를 적용합니다. 태그는 비용 분석에서 차원으로 표시됩니다. 차원을 사용하여 사용자 지정 태그 분류에 대한 인사이트를 얻을 수 있습니다.

태그에 대한 지원은 리소스에 적용된 *후*에 보고된 사용량에 적용됩니다. 비용 롤업에는 태그가 소급 적용되지 않습니다.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **Cost Management** > **비용 분석**.
1. 태그에 사용할 **그룹화 기준**을 선택합니다.

[![특정 태그에 대한 비용 보기](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>사용 세부 정보 다운로드

사용 현황 세부 정보 보고서 파일(CSV 형식)에는 청구서에 대해 발생하는 모든 요금에 대한 분석이 포함됩니다. 보고서를 사용하면 청구서와 비교하면서 더 잘 이해할 수 있습니다. 청구서에 나온 각 청구 요금은 사용 현황 보고서에서 분석된 요금에 해당합니다.

1. Azure Portal에서 청구 계정 또는 구독에 대한 **사용량 및 요금** 탭으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **청구** > **사용량 + 요금**.
1. 다운로드할 항목을 선택한 다음, 다운로드 기호를 클릭합니다.  
    [![사용량 및 요금 다운로드](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  다운로드할 사용량 파일을 선택합니다.  
    ![다운로드할 사용량 파일을 선택합니다.](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>월간 EA 비용 분석 보기

EA 등록은 전체 조직에 대한 비용을 계산합니다. 시간에 따라 비용이 계산되고 청구서가 발부되는 방법을 이해하면 비용을 책임감 있게 관리할 수 있도록 적절한 관련자와 협력할 수 있습니다.

비용은 활성 등록에 대해서만 표시됩니다. 등록(비활성)을 새 등록(활성)으로 이전한 경우 이전 등록에 대한 비용은 Cost Management에 표시되지 않습니다.


1. Azure Portal에서 **Cost Management + 청구** > **개요**로 이동합니다.
1. 현재 달의 **분석**을 클릭하고 금액 약정 번다운을 표시합니다.  
    [![EA 비용 개요 - 분석 요약](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  **사용량 및 요금** 탭을 클릭하고 선택한 시간 범위에서 이전 달의 분석 결과를 확인합니다.  
    [![사용량 및 요금 탭](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>기간별 등록 월별 비용 보기

등록 월별 비용에 대한 그래픽 보기를 사용하여 지정된 기간 동안 비용 추세와 청구서가 발부된 금액을 파악합니다.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **Cost Management** > **비용 분석**.
1. 등록을 선택하고 등록 기간을 설정합니다.
1. 세분성을 월별로 설정한 다음, 보기를 **열(누적)** 로 설정합니다.

데이터를 그룹화하고 필터링하여 더 자세한 분석을 수행할 수 있습니다.

[![기간별 월간 등록 비용](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>EA 등록 누적 비용 보기

시간에 따른 순 누적 요금을 확인하여 지정된 기간 동안 조직의 전반적인 비용을 파악합니다.

1. Azure Portal에서 해당 범위에 대한 비용 분석으로 이동합니다. 다음은 그 예입니다.  **Cost Management + 청구** > **Cost Management** > **비용 분석**.
1. 등록을 선택한 다음, 현재 누적 비용을 확인합니다.

[![등록 누적 비용](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>다음 단계
- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
- [Cost Management 설명서](../index.yml)를 읽어보세요.
