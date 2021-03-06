---
title: '빠른 시작: 음성 텍스트 변환 - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74980801"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 한 언어에서 다른 언어의 텍스트로 음성을 대화형으로 변환합니다. 몇 가지 필수 구성 요소를 충족한 후 음성 텍스트 변환 작업은 다석 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 ````SpeechConfig```` 개체를 만듭니다.
> * ````SpeechConfig```` 개체를 업데이트하여 원본 및 대상 언어를 지정합니다.
> * 위의 ````SpeechConfig```` 개체를 사용하여 ````TranslationRecognizer```` 개체를 만듭니다.
> * ````TranslationRecognizer```` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 ````TranslationRecognitionResult````를 검사합니다.
