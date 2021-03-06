---
title: '빠른 시작: Ruby용 Text Analytics 클라이언트 라이브러리 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Azure Cognitive Services의 Ruby Text Analytics 클라이언트 라이브러리를 사용하여 언어를 감지합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0d4d32a413dd22c55f1b2f01dce3a3df81f5f729
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77919671"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>빠른 시작: Ruby용 Text Analytics 클라이언트 라이브러리 사용

Text Analytics 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

Text Analytics 클라이언트 라이브러리를 사용하여 수행하는 작업은 다음과 같습니다.

* 정서 분석
* 언어 검색
* 엔터티 인식
* 핵심 문구 추출

> [!NOTE]
> 이 빠른 시작은 Text Analytics 버전 2.1에만 적용됩니다. 현재 Ruby용 v3 클라이언트 라이브러리를 사용할 수 없습니다.

[참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [패키지(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Ruby](https://www.ruby-lang.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스를 만들어"  target="_blank">Text Analytics 리소스를 만들어<span class="docon docon-navigate-external x-hidden-focus"></span></a> 키와 엔드포인트를 가져옵니다. 
    * 애플리케이션을 Text Analytics API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 작업은 나중에 빠른 시작에서 수행합니다.
    * 평가판 가격 책정 계층을 사용하여 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-ruby-application"></a>새 Ruby 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 그런 다음, `GemFile` 파일과 코드에 대한 Ruby 파일을 만듭니다.

```console
mkdir myapp && cd myapp
```

`GemFile`에서 다음 줄을 추가하여 클라이언트 라이브러리를 종속성으로 추가합니다.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Ruby 파일에서 다음 패키지를 가져옵니다.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>개체 모델 

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증합니다. 클라이언트는 텍스트를 단일 문자열 또는 일괄 처리로 분석하는 몇 가지 메서드를 제공합니다. 

텍스트는 사용된 메서드에 따라 `id`, `text` 및 `language` 특성의 조합이 포함된 `dictionary` 개체인 `documents`의 목록으로 API에 보내집니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Text Analytics 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [엔터티 인식](#entity-recognition)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>클라이언트 인증

`TextAnalyticsClient`라는 클래스를 만듭니다. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

이 클래스에서 키와 엔드포인트를 사용하여 클라이언트를 인증하는 `initialize`라는 함수를 만듭니다. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

클래스 외부에서 클라이언트의 `new()` 함수를 사용하여 클라이언트를 인스턴스화합니다.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>정서 분석

클라이언트 개체에서 `AnalyzeSentiment()` 함수를 만듭니다. 이 함수는 나중에 만들 입력 문서 목록을 가져옵니다. 클라이언트의 `sentiment()` 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

클라이언트 함수 외부에서, 이전에 만든 `TextAnalyticsClient` 개체를 사용하는 `SentimentAnalysisExample()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `MultiLanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id`, `Language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 그런 다음, 클라이언트의 `AnalyzeSentiment()` 함수를 호출합니다.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

`SentimentAnalysisExample()` 함수를 호출합니다.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>출력

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>언어 검색

클라이언트 개체에서 `DetectLanguage()` 함수를 만듭니다. 이 함수는 나중에 만들 입력 문서 목록을 가져옵니다. 클라이언트의 `detect_language()` 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 검색된 언어를 인쇄합니다.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

클라이언트 함수 외부에서, 이전에 만든 `TextAnalyticsClient` 개체를 사용하는 `DetectLanguageExample()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `LanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하며, `id`는 임의의 값이 될 수 있습니다. 그런 다음, 클라이언트의 `DetectLanguage()` 함수를 호출합니다.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

`DetectLanguageExample()` 함수를 호출합니다.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>출력

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>엔터티 인식

클라이언트 개체에서 `RecognizeEntities()` 함수를 만듭니다. 이 함수는 나중에 만들 입력 문서 목록을 가져옵니다. 클라이언트의 `entities()` 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 인식된 엔터티를 출력합니다.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

클라이언트 함수 외부에서, 이전에 만든 `TextAnalyticsClient` 개체를 사용하는 `RecognizeEntitiesExample()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `MultiLanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 텍스트의 언어이고, `id`는 아무 값이나 가능합니다. 그런 다음, 클라이언트의 `RecognizeEntities()` 함수를 호출합니다.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

`RecognizeEntitiesExample()` 함수를 호출합니다.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>출력

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>핵심 문구 추출

클라이언트 개체에서 `ExtractKeyPhrases()` 함수를 만듭니다. 이 함수는 나중에 만들 입력 문서 목록을 가져옵니다. 클라이언트의 `key_phrases()` 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 추출된 핵심 구를 출력합니다.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

클라이언트 함수 외부에서, 이전에 만든 `TextAnalyticsClient` 개체를 사용하는 `KeyPhraseExtractionExample()`이라는 새 함수를 만듭니다. 분석하려는 문서가 포함된 `MultiLanguageInput` 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 텍스트의 언어이고, `id`는 아무 값이나 가능합니다. 그런 다음, 클라이언트의 `ExtractKeyPhrases()` 함수를 호출합니다.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


`KeyPhraseExtractionExample()` 함수를 호출합니다.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>출력

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
