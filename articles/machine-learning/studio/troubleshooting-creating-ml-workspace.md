---
title: 작업 영역 문제 해결
titleSuffix: ML Studio (classic) - Azure
description: 이 가이드에서는 Azure 기계 학습 스튜디오(클래식) 작업 영역을 설정할 때 자주 발생하는 몇 가지 문제에 대한 솔루션을 제공합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217829"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>문제 해결 가이드: Azure 기계 학습 스튜디오(클래식) 작업 영역 만들기 및 연결

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

이 가이드에서는 Azure 기계 학습 스튜디오(클래식) 작업 영역을 설정할 때 자주 발생하는 몇 가지 문제에 대한 솔루션을 제공합니다.

## <a name="workspace-owner"></a>작업 영역 소유자
기계 학습 스튜디오(클래식)에서 작업 영역을 열려면 작업 영역을 만드는 데 사용한 Microsoft 계정에 로그인해야 하거나 소유자로부터 작업 영역에 참여하라는 초대를 받아야 합니다. Azure Portal에서 액세스를 구성하는 기능을 포함하여 작업 영역을 관리할 수 있습니다.

작업 영역 관리에 대한 자세한 내용은 [Azure 기계 학습 스튜디오(클래식) 작업 영역 관리를]참조하십시오.

[Azure 기계 학습 스튜디오(클래식) 작업 영역 관리]: manage-workspace.md

## <a name="allowed-regions"></a>허용되는 지역
Machine Learning은 현재 제한된 지역에서 사용할 수 있습니다. 구독에 이러한 리전 중 하나가 포함되지 않은 경우 "허용된 지역에 구독이 없습니다."라는 오류 메시지가 표시될 수 있습니다.

구독에 영역을 추가하도록 요청하려면 Azure Portal에서 Microsoft 지원 요청을 만들고 문제 유형으로 **청구**를 선택한 다음 프롬프트에 따라 요청을 제출합니다.

## <a name="storage-account"></a>스토리지 계정
Machine Learning 서비스에서 데이터를 저장하려면 스토리지 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 기계 학습 스튜디오(클래식) 작업 영역을 만들 때 새 저장소 계정을 만들 수 있습니다(새 저장소 계정을 만들 수 있는 할당량이 있는 경우).

새 기계 학습 스튜디오(클래식) 작업 영역이 만들어지면 작업 영역을 만드는 데 사용한 Microsoft 계정을 사용하여 기계 학습 스튜디오(클래식)에 로그인할 수 있습니다. 다음 스크린샷과 유사하게 "작업 영역을 찾을 수 없습니다"라는 오류 메시지가 발생하면 다음 단계를 사용하여 브라우저 쿠키를 삭제하십시오.

![작업 영역을 찾을 수 없음](media/troubleshooting-creating-ml-workspace/screen3.png)

**브라우저 쿠키를 삭제하려면**

1. Internet Explorer를 사용하는 경우 오른쪽 위에 있는 **도구** 단추를 클릭하고 **인터넷 옵션**을 선택합니다.  

   ![인터넷 옵션](media/troubleshooting-creating-ml-workspace/screen4.png)

2. **일반** 탭에서 **삭제…** 를 클릭합니다.

   ![일반 탭](media/troubleshooting-creating-ml-workspace/screen5.png)

3. **검색 기록 삭제** 대화 상자에서 **쿠키 및 웹 사이트 데이터**가 선택되었는지 확인하고 **삭제**를 클릭합니다.

   ![쿠키 삭제](media/troubleshooting-creating-ml-workspace/screen6.png)

쿠키가 삭제된 후 브라우저를 다시 시작한 다음 [Microsoft Azure 기계 학습 스튜디오(클래식)](https://studio.azureml.net) 페이지로 이동합니다. 사용자 이름과 암호를 묻는 메시지가 표시되면 작업 영역을 만드는 데 사용한 동일한 Microsoft 계정을 입력합니다.

## <a name="comments"></a>주석

Microsoft는 Machine Learning 환경을 가능한 한 원활하게 만들기 위해 노력하고 있습니다. 의견이나 문제가 있을 경우 더 나은 서비스를 제공할 수 있도록 [Azure Machine Learning 포럼](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)에 게시해 주세요.
