---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963661"
---
성능을 최대화하려면 각 기능 앱에 대해 별도의 저장소 계정을 사용합니다. 이는 둘 다 대량의 저장소 트랜잭션을 생성하는 지속 기능 또는 이벤트 허브 트리거 함수가 있는 경우에 특히 중요합니다. 응용 프로그램 논리가 직접(저장소 SDK 사용) 또는 저장소 바인딩 중 하나를 통해 Azure Storage와 상호 작용하는 경우 전용 저장소 계정을 사용해야 합니다. 예를 들어, Event Hub 트리거 함수가 일부 데이터를 Blob 저장소에 쓰는&mdash;경우 함수 앱에 대해 두 개의 저장소 계정과 함수에 의해 저장되는 Blob에 대해 두 개의 저장소 계정을 사용합니다.