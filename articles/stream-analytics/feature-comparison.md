---
title: Azure 스트림 분석 기능 비교
description: 이 문서에서는 Azure 포털, Visual Studio 및 Visual Studio 코드에서 Azure Stream Analytics 클라우드 및 IoT Edge 작업에 대해 지원되는 기능을 비교합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: cdae6a3b7319aefa9d4f19b5d613d1afb8b6804a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235320"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure 스트림 분석 기능 비교

Azure Stream Analytics를 사용하면 [Azure 포털,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)및 Visual Studio [코드를](quick-create-vs-code.md)사용하여 클라우드 및 IoT Edge에서 스트리밍 솔루션을 만들 수 있습니다. 이 문서의 표에는 두 작업 유형에 대해 각 플랫폼에서 지원되는 기능이 표시됩니다.

## <a name="cloud-job-features"></a>클라우드 작업 기능


|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|플랫폼 간     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|스크립트 작성     |yes         |yes         |yes         |
|스크립트 인텔리센스     |구문 강조 표시         |구문 강조 표시</br>코드 완성</br>오류 마커         |구문 강조 표시</br>코드 완성</br>오류 마커         |
|모든 유형의 입력, 출력 및 작업 구성 정의     |yes         |yes         |yes         |
|원본 제어     |예         |yes         |yes         |
|CI/CD 지원     |Partial         |yes         |yes         |
|여러 쿼리에서 입력 및 출력 공유     |예         |yes         |yes         |
|샘플 파일로 쿼리 테스트     |yes         |yes        |yes         |
|라이브 데이터 로컬 테스트     |예         |yes       |yes      |
|작업 나열 및 작업 엔터티 보기     |yes         |yes        |yes         |
|로컬 프로젝트로 작업 내보내기     |예         |yes         |yes         |
|작업 제출, 시작 및 중지     |yes         |yes         |yes         |
|작업 메트릭 및 다이어그램 보기     |yes         |yes         |포털에서 열기         |
|작업 런타임 오류 보기     |yes         |yes         |예         |
|진단 로그     |yes         |예         |예         |
|사용자 지정 메시지 속성     |yes         |yes         |예       |
|C# 사용자 지정 코드 함수 및 디직직렬화|읽기 전용 모드|yes|예|
|자바 스크립트 UDF 및 UDA     |yes         |yes         |Windows만 해당         |
|Machine Learning Service     |yes        |yes         |예         |
|Machine Learning Studio     |예, 하지만 쿼리를 테스트할 수 없습니다.        |yes |예         |
|호환성 수준     |1.0</br>1.1</br>1.2(기본값)         |1.0</br>1.1</br>1.2(기본값)           |1.0</br>1.1</br>1.2(기본값)           |
|내장 ML 기반 변칙 감지 기능     |yes         |yes         |yes         |
|내장된 지리 공간 기능     |yes         |yes         |yes         |



## <a name="iot-edge-job-features"></a>IoT 에지 작업 기능

|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|작업 작성     |yes         |yes         |예         |
|원본 제어     |예         |yes         |예         |
|로컬 프로젝트로 작업 내보내기     |예         |yes         |예         |
|샘플 파일로 쿼리 테스트     |yes         |yes         |예         |
|여러 쿼리에서 입력 및 출력 공유     |예         |yes         |예         |
|C # UDF     |예         |yes         |예         |
|작업 제출     |yes         |yes         |예         |
|작업 나열 및 작업 엔터티 보기     |yes         |yes         |예         |
|작업 메트릭 및 다이어그램 보기     |yes         |Partial         |예         |
|작업 런타임 오류 보기     |yes         |Partial         |예         |
|CI/CD 지원     |예         |예         |예         |


## <a name="next-steps"></a>다음 단계

* [IoT Edge의 Azure Stream Analytics](stream-analytics-edge.md)
* [자습서: Azure Stream Analytics IoT Edge 작업에 대한 C# 사용자 정의 함수 작성(미리 보기)](stream-analytics-edge-csharp-udf.md)
* [Visual Studio 도구를 사용하여 스트림 분석 IoT 에지 작업 개발](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
* [비주얼 스튜디오 코드(미리 보기)를 통해 Azure 스트림 분석 살펴보기](visual-studio-code-explore-jobs.md)


