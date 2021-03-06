---
title: '메타데이터 편집: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 메타데이터 편집 모듈을 사용하여 데이터 집합의 열과 연결된 메타데이터를 변경하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064250"
---
# <a name="edit-metadata-module"></a>메타데이터 모듈 편집

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)에 포함된 모듈에 대해 설명합니다.

메타데이터 편집 모듈을 사용하여 데이터 집합의 열과 연결된 메타데이터를 변경합니다. 데이터 집합의 값 및 데이터 형식은 메타데이터 편집 모듈을 사용한 후 변경됩니다.

일반적인 메타데이터 변경 작업은 다음과 같습니다.
  
+ 부울 또는 숫자 열을 범주값으로 처리합니다.
  
+ **클래스** 레이블을 포함하거나 분류하거나 예측할 값을 포함하는 열을 나타냅니다.
  
+ 열을 피처로 표시합니다.
  
+ 날짜/시간 값을 숫자 값으로 변경하거나 그 반대로 변경합니다.
  
+ 열 이름 바꾸기.
  
 일반적으로 다운스트림 모듈에 대한 요구 사항을 충족하려면 열 정의를 수정해야 할 때마다 메타데이터 편집을 사용합니다. 예를 들어 일부 모듈은 특정 데이터 형식에서만 작동하거나 또는 `IsFeature` 또는 `IsCategorical`와 같은 열에 플래그가 필요합니다.  
  
 필요한 작업을 수행한 후 메타데이터를 원래 상태로 재설정할 수 있습니다.
  
## <a name="configure-edit-metadata"></a>메타데이터 편집 구성
  
1. Azure 기계 학습 디자이너에서 메타데이터 편집 모듈을 파이프라인에 추가하고 업데이트할 데이터 집합을 연결합니다. **데이터 변환** 범주에서 모듈을 찾을 수 있습니다.
  
1. 모듈의 오른쪽 패널에서 **열 편집을** 클릭하고 작업할 열 또는 열 집합을 선택합니다. 이름이나 인덱스별로 열을 개별적으로 선택하거나 유형별로 열 그룹을 선택할 수 있습니다.  
  
1. 선택한 열에 다른 데이터 형식을 할당해야 하는 경우 **데이터 유형** 옵션을 선택합니다. 특정 작업에 대한 데이터 형식을 변경해야 할 수 있습니다. 예를 들어 원본 데이터 집합에 숫자가 텍스트로 처리된 경우 수학 연산을 사용하기 전에 숫자 데이터 유형으로 변경해야 합니다.

    + 지원되는 데이터 형식은 **문자열,** **정수,** **이중,** **부울**및 **DateTime**입니다.

    + 여러 열을 선택하는 경우 선택한 *모든* 열에 메타데이터 변경 내용을 적용해야 합니다. 예를 들어 두 개 또는 세 개의 숫자 열을 선택한다고 가정해 보겠습니다. 모두 문자열 데이터 유형으로 변경하고 한 번의 작업에서 이름을 바꿀 수 있습니다. 그러나 한 열을 문자열 데이터 유형으로 변경하고 다른 열은 float에서 정수로 변경할 수 없습니다.
  
    + 새 데이터 형식을 지정하지 않으면 열 메타데이터가 변경되지 않습니다.

    + 메타데이터 편집 작업을 수행한 후에열 유형 및 값이 변경됩니다. 메타데이터 편집을 사용하여 열 데이터 형식을 재설정하여 언제든지 원래 데이터 형식을 복구할 수 있습니다.  

    > [!NOTE]
    > 모든 유형의 숫자를 **DateTime** 유형으로 변경하면 **DateTime 형식** 필드를 비워 둡니다. 현재 대상 데이터 형식을 지정할 수 없습니다.  

1. **범주형** 옵션을 선택하여 선택한 열의 값을 범주로 처리하도록 지정합니다.

    예를 들어 숫자 0, 1 및 2가 포함된 열이 있을 수 있지만 숫자는 실제로 "흡연자", "비흡연자", "알 수 없음"을 의미한다는 것을 알고 있습니다. 이 경우 열을 범주형으로 플래그를 지정하면 값이 숫자 계산이 아닌 데이터를 그룹화하는 데만 사용됩니다.
  
1. Azure 기계 학습이 모델의 데이터를 사용하는 방식을 변경하려면 **필드** 옵션을 사용합니다.

    + **특징**: 이 옵션을 사용하여 피처 열에서만 작동하는 모듈의 피쳐로 열에 플래그를 지정합니다. 기본적으로 모든 열은 초기에 기능으로 처리됩니다.  
  
    + **레이블**: 예측 가능한 특성 또는 대상 변수라고도 하는 레이블을 표시하려면 이 옵션을 사용합니다. 대부분의 모듈에서는 데이터 집합에 정확히 하나의 레이블 열이 존재해야 합니다.

        대부분의 경우 Azure 기계 학습은 열에 클래스 레이블이 포함되어 있다고 추론할 수 있습니다. 이 메타데이터를 설정하여 열이 올바르게 식별되었는지 확인할 수 있습니다. 이 옵션을 설정해도 데이터 값은 변경되지 않습니다. 일부 기계 학습 알고리즘이 데이터를 처리하는 방식만 변경됩니다.
  
    > [!TIP]
    > 이러한 범주에 맞지 않는 데이터가 있습니까? 예를 들어 데이터 집합에는 변수로 유용하지 않은 고유 식별자와 같은 값이 포함될 수 있습니다. 경우에 따라 이러한 아이디가 모델에서 사용될 때 문제가 발생할 수 있습니다.
    >
    > 다행히 Azure Machine Learning은 모든 데이터를 유지하므로 데이터 집합에서 이러한 열을 삭제할 필요가 없습니다. 일부 특수 한 열 집합에서 작업을 수행 해야 하는 경우 [데이터 집합에서 열 선택](select-columns-in-dataset.md) 모듈을 사용 하 여 다른 모든 열을 일시적으로 제거 합니다. 나중에 열 추가 모듈을 사용하여 열을 다시 데이터 집합으로 병합할 수 [있습니다.](add-columns.md)  
  
1. 다음 옵션을 사용하여 이전 선택을 지우고 메타데이터를 기본값으로 복원합니다.  
  
    + **기능 지우기**: 이 옵션을 사용하여 피쳐 플래그를 제거합니다.  
  
         모든 열은 처음에 피쳐로 처리됩니다. 수학 연산을 수행하는 모듈의 경우 숫자 열이 변수로 처리되지 않도록 하려면 이 옵션을 사용해야 할 수 있습니다.
  
    + **레이블 지우기**: 이 옵션을 사용하여 지정된 열에서 **레이블** 메타데이터를 제거합니다.  
  
    + **점수 지우기**: 이 옵션을 사용하여 지정된 열에서 **점수** 메타데이터를 제거합니다.  
  
         현재 Azure 기계 학습에서 열을 점수로 명시적으로 표시할 수 없습니다. 그러나 일부 작업으로 인해 열내부적으로 점수로 플래그가 지정됩니다. 또한 사용자 지정 R 모듈은 점수 값을 출력할 수 있습니다.

1. **새 열 이름의**경우 선택한 열 또는 열의 새 이름을 입력합니다.  
  
    + 열 이름은 UTF-8 인코딩에서 지원하는 문자만 사용할 수 있습니다. 공백으로 완전히 구성된 빈 문자열, null 또는 이름은 허용되지 않습니다.  
  
    + 여러 열의 이름을 바꾸려면 이름을 열 인덱스 순서대로 쉼표로 구분된 목록으로 입력합니다.  
  
    + 선택한 모든 열의 이름을 바여야 합니다. 열을 생략하거나 건너뛸 수 없습니다.  
  
1. 파이프라인을 제출합니다.  

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오.
