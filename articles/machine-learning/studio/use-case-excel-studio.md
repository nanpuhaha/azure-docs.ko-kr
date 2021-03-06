---
title: Excel에서 분석 마이그레이션
titleSuffix: ML Studio (classic) - Azure
description: Excel 및 Azure 기계 학습 스튜디오의 선형 회귀 모델 비교(클래식)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217807"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>엑셀에서 Azure 기계 학습 스튜디오로 분석 마이그레이션(클래식)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *케이트 바로니와* *벤 보트먼은* 마이크로소프트의 데이터 인사이트 센터의 엔터프라이즈 솔루션 아키텍트입니다. 이 문서에서는 기존 회귀 분석 제품군을 Azure 기계 학습 스튜디오(클래식)를 사용하여 클라우드 기반 솔루션으로 마이그레이션한 경험을 설명합니다.

## <a name="goal"></a>목표

다음 두 가지 목표를 염두에 두고 프로젝트를 시작했습니다. 

1. 예측 분석을 사용하여 조직의 월별 수익 예측의 정확도를 개선합니다. 
2. Azure 기계 학습 스튜디오(클래식)를 사용하여 결과의 확인, 최적화, 속도 및 확장을 확인합니다. 

많은 기업과 마찬가지로 우리 조직에서도 월별 수익 예측 프로세스를 수행합니다. 소규모 비즈니스 분석가 팀은 Azure Machine Learning Studio(클래식)를 사용하여 프로세스를 지원하고 예측 정확도를 개선하는 임무를 맡았습니다. 이 팀은 여러 소스에서 데이터를 수집하고 서비스 판매량 매출과 관련된 주요 특성을 식별하는 통계 분석을 통해 데이터 특성을 실행하는 데 몇 달을 보냈습니다. 다음 단계는 Excel에서 데이터에 대한 통계 회귀 모델 프로토타입 작성을 시작하는 것이었습니다. 몇 주 내에 현재 필드 및 재무 예측 프로세스를 능가하는 Excel 회귀 모델이 완성되었습니다. 이를 기준 예측 결과로 사용했습니다. 

그런 다음 예측 분석을 Studio(클래식)로 이동하여 Studio(클래식)가 예측 성능을 어떻게 개선할 수 있는지 알아보는 다음 단계를 수행했습니다.

## <a name="achieving-predictive-performance-parity"></a>예측 성능 패리티 달성
우리의 첫 번째 우선 순위는 스튜디오 (클래식)와 Excel 회귀 모델 사이의 패리티를 달성하는 것이었습니다. 동일한 데이터와 학습 및 테스트 데이터에 대해 동일한 분할을 감안할 때 Excel과 Studio(클래식) 간의 예측 성능 패리티를 달성하고자 했습니다. 처음에는 실패했습니다. 엑셀 모델은 스튜디오 (클래식) 모델을 능가했습니다. 실패는 스튜디오 (클래식)에서 기본 도구 설정의 이해의 부족때문이었다. Studio(클래식) 제품 팀과 동기화한 후 데이터 세트에 필요한 기본 설정을 더 잘 이해하고 두 모델 간의 패리티를 달성했습니다. 

### <a name="create-regression-model-in-excel"></a>Excel에서 회귀 모델 만들기
Excel 회귀에서는 Excel 분석 도구에 있는 표준 선형 회귀 모델을 사용했습니다. 

*절대 평균 오차율(%)* 을 계산하여 이를 모델의 성능 척도로 사용했습니다. Excel을 사용하여 작동하는 모델을 만드는 데 3개월이 걸렸습니다. 우리는 궁극적으로 요구 사항을 이해하는 데 도움이 스튜디오 (고전) 실험에 학습의 대부분을 가져왔다.

### <a name="create-comparable-experiment-in-studio-classic"></a>스튜디오 (클래식)에서 비교 실험 만들기
우리는 스튜디오 (고전)에서 우리의 실험을 만들기 위해 다음 단계를 따랐다 : 

1. 데이터 집합을 Csv 파일로 스튜디오(클래식)에 업로드(매우 작은 파일)
2. 새 실험을 만들고 [데이터 세트의 열 선택][select-columns] 모듈을 사용하여 Excel에서 사용되는 동일한 데이터 기능을 선택했습니다. 
3. [데이터 분할][split] 모듈(*상대 식* 모드)을 사용하여 데이터를 Excel에서 수행한 것과 동일한 학습 데이터 세트로 나누었습니다. 
4. [Linear Regression][linear-regression] 모듈(기본 옵션만)로 실험하고 기록하여 결과를 Excel 회귀 모델과 비교했습니다.

### <a name="review-initial-results"></a>초기 결과 검토
처음에 Excel 모델은 Studio (클래식) 모델을 분명히 능가했습니다. 

|  | Excel | Studio(클래식) |
| --- |:---:|:---:|
| 성능 | | |
| <ul style="list-style-type: none;"><li>조정된 R 제곱</li></ul> |0.96 |해당 없음 |
| <ul style="list-style-type: none;"><li>결정 <br />계수</li></ul> |해당 없음 |0.78<br />(낮은 정확도) |
| 절대 평균 오차 |$9.5M |$19.4M |
| 평균 절대 오차율(%) |6.03% |12.2% |

프로세스를 실행한 결과 Machine Learning 팀의 개발자 및 데이터 과학자는 몇 가지 유용한 팁을 신속하게 제공했습니다. 

* Studio(클래식)에서 [선형 회귀 모듈을][linear-regression] 사용하는 경우 다음 두 가지 방법이 제공됩니다.
  * 온라인 기울기 하강: 보다 큰 규모의 문제에 적합할 수 있습니다.
  * 최소 자승법: 대부분의 사람들이 선형 회귀에 대해 떠올리는 방법입니다. 데이터 세트가 작은 경우 최소 자승법이 보다 적합할 수 있습니다.
* L2 정규화 가중치 매개 변수를 조정하여 성능을 개선하는 것이 좋습니다. 기본적으로 0.001로 설정되지만 작은 데이터 집합에서는 성능 향상을 위해 0.005로 설정했습니다. 

### <a name="mystery-solved"></a>문제 해결!
권장 사항을 적용했을 때 Excel과 동일한 Studio(클래식)에서 동일한 기준 성능을 달성했습니다. 

|  | Excel | 스튜디오(클래식) (이심셜) | 스튜디오 (클래식) w / 최소 사각형 |
| --- |:---:|:---:|:---:|
| 레이블이 지정된 값 |실제 값(숫자) |동일 |동일 |
| 학습자 |Excel -> 데이터 분석 ->회귀 |선형 회귀 |선형 회귀 |
| 학습자 옵션 |해당 없음 |기본값 |최소 자승법<br />L2 = 0.005 |
| 데이터 집합 |26개 행, 3가지 기능, 1개 레이블 모든 숫자 |동일 |동일 |
| 분할: 학습 |처음 18개 행에서 학습되고 마지막 8개 행에서 테스트된 Excel |동일 |동일 |
| 분할: 테스트 |마지막 8개 행에 적용되는 Excel 회귀 수식 |동일 |동일 |
| **성능** | | | |
| 조정된 R 제곱 |0.96 |해당 없음 | |
| 결정 계수 |해당 없음 |0.78 |0.952049 |
| 절대 평균 오차 |$9.5M |$19.4M |$9.5M |
| 평균 절대 오차율(%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

또한 Excel 계수를 Azure 학습 모델의 기능 가중치와 비교했습니다.

|  | Excel 계수 | Azure 기능 가중치 |
| --- |:---:|:---:|
| 가로채기/바이어스 |19470209.88 |19328500 |
| 기능 A |0.832653063 |0.834156 |
| 기능 B |11071967.08 |11007300 |
| 기능 C |25383318.09 |25140800 |

## <a name="next-steps"></a>다음 단계
Excel 내에서 Machine Learning 웹 서비스를 사용하려고 했습니다. 비즈니스 분석가는 Excel에 의존하므로 Excel 데이터 행으로 Machine Learning 웹 서비스를 호출하고 예측 값을 Excel로 반환하는 방법이 필요했습니다. 

우리는 또한 Studio (클래식)에서 사용할 수있는 옵션과 알고리즘을 사용하여 모델을 최적화하고 싶었습니다.

### <a name="integration-with-excel"></a>Excel과 통합
솔루션은 학습된 모델에서 웹 서비스를 만들어Machine Learning 회귀 모델을 조작할 수 있게 하는 것이었습니다. 몇 분 이내에 웹 서비스를 만들었으며 Excel에서 직접 호출하여 예측 수익 값을 반환할 수 있었습니다. 

*웹 서비스 대시보드* 섹션에 다운로드할 수 있는 Excel 통합 문서가 포함되어 있습니다. 이 통합 문서는 웹 서비스 API 및 스키마 정보가 포함되어 있으며 서식이 미리 지정되어 있습니다. *Excel 통합 문서 다운로드*를 클릭하면 통합 문서가 열리고 로컬 컴퓨터에 저장할 수 있습니다. 

![웹 서비스 대시보드에서 Excel 통합 문서 다운로드](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

통합 문서가 열리면 아래 그림과 같이 파란색 매개 변수 섹션에 미리 정의된 매개 변수를 복사합니다. 매개 변수를 입력하면 Excel에서 Machine Learning 웹 서비스를 호출하고 예측 점수가 매겨진 레이블이 녹색 예측 값 섹션에 표시됩니다. 이 통합 문서는 매개 변수 아래에 입력된 모든 행 항목에 대해 학습된 모델을 기반으로 매개 변수의 예측 값을 계속 생성합니다. 이 기능을 사용하는 방법에 대한 자세한 내용은 [Excel에서 Azure Machine Learning 웹 서비스 사용](consuming-from-excel.md)을 참조하세요. 

![배포된 웹 서비스에 연결하는 템플릿 Excel 통합 문서](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>최적화 및 추가 실험
Excel 모델을 사용하여 기준을 만들었으므로 이제 Machine Learning 선형 회귀 모델을 최적화하는 과정을 진행했습니다. [Filter-Based Feature Selection][filter-based-feature-selection] 모듈을 사용하여 초기 데이터 요소 선택을 개선함으로써 절대 평균 오차가 4.6%로 향상되는 효과를 얻었습니다. 향후 프로젝트에서는 데이터 특성을 반복하여 모델링에 사용할 올바른 기능 집합을 찾는 데 몇 주를 절약할 수 있는 이 기능을 사용할 것입니다. 

다음에는 [Bayesian][bayesian-linear-regression] 또는 [Boosted Decision Trees][boosted-decision-tree-regression]와 같은 추가 알고리즘을 포함하여 성능을 비교할 계획입니다. 

회귀를 사용하여 실험하려는 경우 많은 숫자 특성이 포함된 Energy Efficiency Regression 샘플 데이터 세트를 사용하는 것이 좋습니다. 데이터 집합은 Studio(클래식)의 샘플 데이터 집합의 일부로 제공됩니다. 다양한 학습 모듈을 사용하여 난방 부하 또는 냉방 부하를 예측할 수 있습니다. 아래 차트에는 대상 변수 Cooling Load를 예측하여 Energy Efficiency 데이터 세트에 대해 다양한 회귀에서 학습한 결과의 성능이 비교되어 있습니다. 

| 모델 | 절대 평균 오차 | 제곱 평균 오차 | 상대 절대 오차 | 상대 제곱 오차 | 결정 계수 |
| --- | --- | --- | --- | --- | --- |
| 향상된 의사 결정 트리 |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| 선형 회귀(기울기 하강) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| 신경망 회귀 |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| 선형 회귀(최소 자승법) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>핵심 내용
우리는 병렬로 Excel 회귀 및 스튜디오 (고전) 실험을 실행하여 많은 것을 배웠습니다. Excel에서 기준 모델을 만들고 기계 학습 [선형 회귀를][linear-regression] 사용하여 모델과 비교하면 Studio(클래식)를 배울 수 있었고 데이터 선택 및 모델 성능을 향상시킬 수 있는 기회를 발견했습니다. 

또한 향후 예측 프로젝트를 가속화하려면 [Filter-Based Feature Selection][filter-based-feature-selection]을 사용하는 것이 좋다는 것도 알았습니다. 데이터에 기능 선택을 적용하면 전체 적인 성능이 향상된 Studio(클래식)에서 향상된 모델을 만들 수 있습니다. 

예측 분석 예측을 Studio(클래식)에서 Excel로 체계적으로 전송하는 기능을 통해 광범위한 비즈니스 사용자에게 결과를 성공적으로 제공할 수 있습니다. 

## <a name="resources"></a>리소스
회귀 작업에 유용한 일부 리소스는 다음과 같습니다. 

* Excel의 회귀 Excel에서 회귀를 시도한 적이 없는 경우 이 자습서를 사용하면 쉽게 수행할 수 있습니다.[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* 회귀와 예측 타일러 체스만은 선형 회귀에 대한 좋은 초보자의 설명을 포함하는 Excel에서 타임 시리즈 예측을 수행하는 방법을 설명하는 블로그 기사를 작성했습니다. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* 최소 자승법 선형 회귀: 결함, 문제점 및 단점 회귀에 대한 소개 및 설명은 다음을 참조하세요. [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

