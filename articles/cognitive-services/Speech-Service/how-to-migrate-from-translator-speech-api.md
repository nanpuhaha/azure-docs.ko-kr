---
title: 번역기 음성 API에서 음성 서비스로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 번역기 음성 API에서 음성 서비스로 응용 프로그램을 마이그레이션하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560901"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>번역기 음성 API에서 음성 서비스로 마이그레이션

이 문서를 사용하여 Microsoft 번역기 음성 API에서 [음성 서비스로](index.yml)응용 프로그램을 마이그레이션합니다. 이 가이드에서는 번역기 음성 API와 음성 서비스 간의 차이점을 간략하게 설명하고 응용 프로그램을 마이그레이션하기 위한 전략을 제안합니다.

> [!NOTE]
> 번역기 음성 API 구독 키는 음성 서비스에서 허용되지 않습니다. 새 음성 서비스 구독을 만들어야 합니다.

## <a name="comparison-of-features"></a>기능 비교

| 기능                                           | Translator Speech API                                  | Speech Service | 세부 정보                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 텍스트로 번역                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 음성으로 번역                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 전역 엔드포인트                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 음성 서비스는 전역 끝점을 제공하지 않습니다. 전역 엔드포인트는 트래픽을 가장 가까운 지역 엔드포인트로 자동으로 전송하여 애플리케이션의 대기 시간을 줄일 수 있습니다.                                                    |
| 지역별 엔드포인트                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 연결 시간 제한                             | 90분                                               | SDK를 사용할 경우 무제한 WebSocket 연결을 사용할 경우 10분                                                                                                                                                                                                                                                                                   |
| 헤더의 인증 키                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 단일 요청을 통해 여러 언어 번역 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK 사용 가능                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 사용 가능한 SDK에 대한 [음성 서비스 설명서를](index.yml) 참조하십시오.                                                                                                                                                    |
| WebSocket 연결                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Languages API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Speech 서비스는 [번역기 API 언어 참조](../translator-speech/languages-reference.md) 문서에 설명된 것과 동일한 범위의 언어를 지원합니다. |
| 욕설 필터 및 표식                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM을 입력으로 사용                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 다른 파일 형식을 입력으로 사용                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 부분 결과                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 타이밍 정보                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 상관관계 ID                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Custom Speech 모델                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 음성 서비스는 조직의 고유한 어휘에 맞게 음성 인식을 사용자 지정할 수 있는 사용자 지정 음성 모델을 제공합니다.                                                                                                                                           |
| 사용자 지정 번역 모델                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Microsoft Text Translation API를 구독하면 [Custom Translator](https://www.microsoft.com/translator/business/customization/)를 사용하여 보다 정확한 번역을 위해 자체 데이터를 사용할 수 있습니다.                                                 |

## <a name="migration-strategies"></a>마이그레이션 전략

사용자 또는 조직에 번역기 음성 API를 사용하는 개발 또는 프로덕션 응용 프로그램이 있는 경우 음성 서비스를 사용하도록 업데이트해야 합니다. 사용 가능한 SDK, 코드 샘플 및 자습서에 대한 [음성 서비스](index.yml) 설명서를 참조하십시오. 마이그레이션하는 경우 다음을 고려합니다.

* 음성 서비스는 전역 끝점을 제공하지 않습니다. 애플리케이션이 모든 해당 트래픽에 대해 단일 지역별 엔드포인트를 사용할 때 효율적으로 작동하는지를 확인합니다. 그렇지 않을 경우 지리적 위치를 사용하여 가장 효율적인 엔드포인트를 확인합니다.

* 애플리케이션이 수명이 긴 연결을 사용하고 사용 가능한 SDK를 사용할 수 없는 경우 WebSocket 연결을 사용할 수 있습니다. 적절한 시간에 다시 연결하여 10분 시간 제한을 관리합니다.

* 응용 프로그램에서 번역기 텍스트 API 및 번역기 음성 API를 사용하여 사용자 지정 번역 모델을 사용하도록 설정하는 경우 음성 서비스를 사용하여 범주 별 입력을 직접 추가할 수 있습니다.

* 번역기 음성 API와 달리 음성 서비스는 단일 요청으로 여러 언어로 번역을 완료할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [무료 음성 서비스를 사용해보십시오.](get-started.md)
* [빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 인식](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>참조

* [음성 서비스란?](overview.md)
* [음성 서비스 및 음성 SDK 문서](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
