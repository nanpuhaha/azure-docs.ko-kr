---
title: Azure Media Services v3 질문과 대답 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3에 대해 자주 묻는 질문과 대답을 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: a4f4bd6eaa07907dd672abe068b515b5127adac9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886826"
---
# <a name="media-services-v3-frequently-asked-questions"></a>미디어 서비스 v3 자주 묻는 질문

이 문서에서는 AMS(Azure Media Services) v3에 대해 자주 묻는 질문과 대답을 제공합니다.

## <a name="general"></a>일반

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 미디어 서비스 리소스에서 작업을 수행할 수 있는 Azure 역할은 무엇입니까? 

[미디어 서비스 계정에 대한 역할 기반 액세스 제어(RBAC)를](rbac-overview.md)참조하십시오.

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Apple iOS 디바이스에 스트리밍하려면 어떻게 하나요?

경로 끝에 "(format=m3u8-aapl)"(URL의 "/매니페스트" 부분 후)가 있어 스트리밍 원본 서버에 Apple iOS 네이티브 장치에서 사용할 수 있도록 HLS 콘텐츠를 반환하도록 지시합니다(자세한 내용은 [콘텐츠 제공](dynamic-packaging-overview.md)참조).

### <a name="how-do-i-configure-media-reserved-units"></a>미디어 예약 단위를 구성하려면 어떻게 할까요?

Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU를 사용하여 계정을 프로비전하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

자세한 내용은 [CLI를 사용하여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="what-is-the-recommended-method-to-process-videos"></a>비디오 처리에 권장하는 방법은 무엇입니까?

[Transforms](https://docs.microsoft.com/rest/api/media/transforms)는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환**은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. [작업은](https://docs.microsoft.com/rest/api/media/jobs) 지정된 입력 비디오 또는 오디오 콘텐츠에 **변환을** 적용하기 위해 미디어 서비스에 대한 실제 요청입니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. 자세한 내용은 [Transform 및 Jobs](transforms-jobs-concept.md)를 참조하세요.

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>비디오를 업로드, 인코딩 및 게시합니다. 스트리밍하려고 할 때 어떤 이유로 비디오가 재생되지 않는 걸까요?

가장 일반적인 이유 중 하나는 재생하려고 하는 스트리밍 엔드포인트가 실행 중 상태가 아니기 때문입니다.

### <a name="how-does-pagination-work"></a>페이지 매김은 어떻게 작동하나요?

페이지 매김을 사용할 때는 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다. 자세한 내용과 예제는 [필터링, 정렬, 페이징](entities-overview.md)을 참조하세요.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure 미디어 서비스 v3에서 아직 사용할 수 없는 기능은 무엇입니까?

자세한 내용은 [v2 API에 대한 피쳐 간격을](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)참조하십시오.

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>구독 간에 미디어 서비스 계정을 이동하는 프로세스는 무엇입니까?  

자세한 내용은 [구독 간에 미디어 서비스 계정 이동을](media-services-account-concept.md)참조하십시오.

## <a name="live-streaming"></a>라이브 스트리밍 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>방송이 끝난 후 라이브 스트림을 중지하는 방법은 무엇입니까?

클라이언트 측이나 서버 측에서 접근할 수 있습니다.

#### <a name="client-side"></a>클라이언트 측

웹 응용 프로그램은 브라우저를 닫는 경우 브로드캐스트를 종료하려는 경우 사용자에게 메시지를 표시해야 합니다. 웹 응용 프로그램이 처리할 수 있는 브라우저 이벤트입니다.

#### <a name="server-side"></a>서버 쪽

이벤트 그리드 이벤트에 가입하여 라이브 이벤트를 모니터링할 수 있습니다. 자세한 내용은 [eventgrid 이벤트 스키마를](media-services-event-schemas.md#live-event-types)참조하십시오.

* [Microsoft.Media.LiveEventEncoder](media-services-event-schemas.md#liveeventencoderdisconnected) 연결 해제 된 스트림 수준에 [가입](reacting-to-media-services-events.md) 하 고 라이브 이벤트를 중지 하 고 삭제 하려면 잠시 동안 아무 반복되지 않습니다 모니터링할 수 있습니다.
* 또는 트랙 레벨 [하트비트](media-services-event-schemas.md#liveeventingestheartbeat) 이벤트를 [구독할](reacting-to-media-services-events.md) 수 있습니다. 모든 트랙에 수신 비트 레이트가 0으로 떨어지는 경우; 또는 마지막 타임 스탬프가 더 이상 증가하지 않습니다, 당신은 또한 안전하게 라이브 이벤트를 종료 할 수 있습니다. 하트 비트 이벤트는 모든 트랙에 대해 매 20 초마다 제공되므로 조금 더 자세한 내용이 될 수 있습니다.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>라이브 스트리밍 도중에 중단/비디오 및 이미지 슬레이트를 삽입하는 방법은 무엇입니까?

Media Services v3 라이브 인코딩은 아직 라이브 스트리밍 도중에 비디오 또는 이미지 슬레이트를 삽입하는 기능을 지원하지 않습니다. 

[라이브 온-프레미스 인코더](recommended-on-premises-live-encoders.md)를 사용하여 원본 비디오를 바꿀 수 있습니다. Telestream Wirecast, Switcher Studio(iOS), OBS Studio(무료 앱) 등 여러 앱에서 원본을 바꾸는 기능을 제공합니다.

## <a name="content-protection"></a>콘텐츠 보호

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 클리어 키 암호화 또는 DRM 시스템을 사용해야 합니까?

고객들은 종종 AES 암호화 또는 DRM 시스템을 사용해야 할지 여부를 궁금해 합니다. 두 시스템의 주요 차이점은 AES 암호화를 사용하면 콘텐츠 키가 TLS를 통해 클라이언트로 전송되어 전송 중에 암호화되지만 추가 암호화없이("지우기")된다는 것입니다. 따라서 콘텐츠를 해독하는 데 사용되는 키는 클라이언트 플레이어가 액세스할 수 있으며 클라이언트의 네트워크 추적에서 일반 텍스트로 볼 수 있습니다. AES-128 clear 키 암호화는 뷰어가 신뢰할 수 있는 당사자인 사용 사례(예: 직원이 볼 수 있도록 회사 내에서 배포된 회사 비디오 암호화)에 적합합니다.

PlayReady, 와이드바인 및 페어플레이와 같은 DRM 시스템은 모두 AES-128 클리어 키와 비교하여 콘텐츠를 해독하는 데 사용되는 키에 대한 추가 수준의 암호화를 제공합니다. 콘텐츠 키는 TLS에서 제공하는 전송 수준 암호화에 추가하여 DRM 런타임으로 보호되는 키로 암호화됩니다. 또한 암호 해독은 악의적인 사용자가 공격하기에 좀 더 어려운 운영 체제 수준의 보안 환경에서 처리됩니다. DRM은 뷰어가 신뢰할 만한 당사자가 아니고 가장 높은 수준의 보안이 필요한 사용 사례에 권장됩니다.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD를 사용하지 않고 특정 권한이 있는 사용자에게만 비디오를 표시하는 방법은 무엇입니까?

Azure AD와 같은 특정 토큰 공급자를 사용할 필요가 없습니다. 비대칭 키 암호화를 사용하여 자체 [JWT](https://jwt.io/) 공급자(소위 STS, 보안 토큰 서비스)를 만들 수 있습니다. 사용자 지정 STS에서 비즈니스 논리에 따라 클레임을 추가할 수 있습니다.

발행자, 대상 및 클레임이 모두 JWT에 있는 내용과 ContentKeyPolicy에 사용되는 ContentKeyPolicy제한 사항 간에 정확히 일치하는지 확인합니다.

자세한 내용은 [미디어 서비스 동적 암호화를 사용하여 콘텐츠 보호](content-protection-overview.md)를 참조하십시오.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>라이선스 또는 키를 요청하는 데 사용하기 전에 JWT 토큰을 가져올 수 있는 방법과 위치는 어떻게 되나요?

1. 프로덕션의 경우 HTTPS 요청에 따라 JWT 토큰을 발급하는 보안 토큰 서비스(웹 서비스)가 있어야 합니다. 테스트를 위해 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)에 정의된 **GetTokenAsync** 메서드에 표시된 코드를 사용할 수 있습니다.
2. 사용자가 인증되면 플레이어에서 이러한 토큰에 대해 STS에 요청하고 토큰의 값으로 할당해야 합니다. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용할 수 있습니다.

* 대칭 키와 비대칭 키를 사용 하 고 STS를 실행 하는 예제를 참조 [https://aka.ms/jwt](https://aka.ms/jwt)하십시오. 
* 이러한 JWT 토큰을 사용하는 Azure Media Player를 기반으로 [https://aka.ms/amtest](https://aka.ms/amtest) 하는 플레이어의 예는 (토큰 입력을 보려면 "player_settings" 링크를 확장)을 참조하십시오.

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 암호화를 사용하여 비디오를 스트림할 수 있도록 요청에 권한을 부여하려면 어떻게 할까요?

올바른 방법은 다음과 같이 STS(보안 토큰 서비스)를 활용하는 것입니다.

STS에서 사용자 프로필에 따라 서로 다른 클레임(예: "프리미엄 사용자", "기본 사용자", "평가판 사용자")을 추가합니다. JWT에서 서로 다른 클레임을 사용하면 사용자가 각각의 콘텐츠를 볼 수 있습니다. 물론 ContentKeyPolicyRestriction에는 서로 다른 콘텐츠/자산에 해당하는 RequiredClaims가 있습니다.

[이 샘플과](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)같이 라이선스/키 배달을 구성하고 자산을 암호화하는 데 Azure Media 서비스 API를 사용합니다.

자세한 내용은 다음을 참조하세요.

- [콘텐츠 보호 개요](content-protection-overview.md)
- [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP 또는 HTTPS
ASP.NET MVC 플레이어 애플리케이션은 다음을 지원해야 합니다.

* Azure AD를 통한 사용자 인증이 HTTPS를 사용해야 함
* 클라이언트 및 Azure AD 간 JWT 교환이 HTTPS를 사용해야 함
* Media Services에서 라이선스 배달이 제공된 경우 클라이언트에 의한 DRM 라이선스 획득 시 HTTPS를 사용해야 함. PlayReady 제품군은 라이선스 배달에 대한 HTTPS를 위임하지 않습니다. PlayReady 라이선스 서버가 Media Services 외부에 있는 경우 HTTP 또는 HTTPS를 사용할 수 있습니다.

ASP.NET 플레이어 애플리케이션은 HTTPS를 사용하는 것이 가장 좋으므로 Media Player는 HTTPS를 사용하는 페이지에 있게 됩니다. 그러나 스트리밍의 경우 HTTP를 선호하므로 콘텐츠 혼합 문제를 고려해야 합니다.

* 브라우저에서는 혼합 콘텐츠를 허용하지 않습니다. 하지만 Silverlight과 같은 플러그인, 부드러운 스트리밍을 위한 OSMF 플러그인 및 DASH는 허용합니다. 악성 JavaScript를 주입할 수 있는 위협으로 인해 고객 데이터가 위험해질 수 있으므로 혼합 콘텐츠는 보안 문제를 발생할 수 있습니다. 브라우저는 기본적으로 이 기능을 차단합니다. 이 문제를 해결하는 유일한 방법이 서버(원본) 쪽에서 HTTPS 또는 HTTP에 관계없이 모든 도메인을 허용하는 것입니다. 하지만 좋은 방법은 아닙니다.
* 혼합 콘텐츠를 피해야 합니다. 플레이어 애플리케이션과 Media Player 둘 다 HTTP 또는 HTTPS를 사용해야 합니다. 혼합된 콘텐츠를 재생할 때 silverlightSS 기술은 혼합된 콘텐츠 경고를 지워야 합니다. flashSS 기술은 혼합된 콘텐츠 경고 없이 혼합된 콘텐츠를 처리합니다.
* 스트리밍 엔드포인트가 2014년 8월 전에 만들어진 경우 HTTPS를 지원하지 않습니다. 이 경우 HTTPS에 대한 새 스트리밍 엔드포인트를 만들어 사용하세요.

### <a name="what-about-live-streaming"></a>라이브 스트리밍의 경우는 어떨까요?

프로그램과 연결된 자산을 VOD 자산으로 처리하여 Media Services에서 라이브 스트리밍을 보호하는 데 정확히 동일한 디자인 및 구현을 사용할 수 있습니다. 라이브 콘텐츠의 다중 DRM 보호를 제공하려면 에셋을 라이브 출력과 연결하기 전에 VOD 자산인 것처럼 에셋에 동일한 설정/처리를 적용합니다.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services 외부에서 라이선스 서버는 어떨까요?

고객들은 대체로 고유한 데이터 센터 또는 DRM 서비스 공급자가 호스트하는 형태로 라이선스 서버 팜에 투자할 수 있습니다. Media Services 콘텐츠 보호를 사용하면 하이브리드 모드로 작업할 수 있습니다. DRM 라이선스는 Media Services 외부 서버에서 제공되지만 콘텐츠는 Media Services에서 호스트되고 동적으로 보호될 수 있습니다. 이 경우 다음과 같은 변경을 고려하세요.

* STS는 라이선스 서버 팜에서 허용되고 확인할 수 있는 토큰을 발급해야 합니다. 예를 들어 Axinom에서 제공하는 Widevine 라이선스 서버에는 자격 메시지를 포함하는 특정 JWT가 필요합니다. 따라서 이러한 JWT를 발급하는 STS가 있어야 합니다. 
* 더 이상 Media Services에서 라이선스 배달 서비스를 구성하지 않아도 됩니다. ContentKeyPolicies를 구성할 때 라이선스 획득 URL(PlayReady, Widevine 및 FairPlay에 대해)을 제공해야 합니다.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="media-services-v2-vs-v3"></a>Media Services v2와 v3 비교 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal을 사용하여 v3 리소스를 관리할 수 있나요?

현재 [Azure 포털을](https://portal.azure.com/) 사용하여 다음을 수행할 수 있습니다.

* 미디어 서비스 v3 [라이브 이벤트](live-events-outputs-concept.md)관리 , 
* 뷰(관리 안 됨) v3 [자산](assets-concept.md), 
* [API 에 액세스하는 것에 대한 정보를 얻을 수 있습니다.](access-api-portal.md) 

다른 모든 관리 작업(예: [변환 및 작업](transforms-jobs-concept.md) 및 콘텐츠 [보호)의](content-protection-overview.md)경우 [REST API,](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)또는 지원되는 [SDK](media-services-apis-overview.md#sdks)중 하나를 사용합니다.

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3에 AssetFile 개념이 있나요?

Media Services를 Storage SDK 종속성과 분리하기 위해 AssetFiles가 AMS API에서 제거되었습니다. 이제 Media Services가 아닌 Storage에서 해당 Storage에 속한 정보를 보관합니다. 

자세한 내용은 [Media Services v3로 마이그레이션](media-services-v2-vs-v3.md)을 참조하세요.

### <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 스토리지 암호화는 어디에 있나요?

이제 서버 쪽 스토리지 암호화(기본적으로 설정됨)를 사용하는 것이 좋습니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 참조하세요.

## <a name="offline-streaming"></a>오프라인 스트리밍

### <a name="fairplay-streaming-for-ios"></a>iOS용 페어플레이 스트리밍

다음 자주 묻는 질문은 iOS용 오프라인 FairPlay 스트리밍 문제 해결에 대한 지원을 제공합니다.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>왜 오프라인 모드에서는 오디오만 재생되고 비디오는 재생되지 않나요?

이 동작은 의도적으로 샘플 앱의 것입니다. 오프라인 모드에서 대체 오디오 트랙(HLS의 경우)이 있는 경우 iOS 10과 iOS 11이 대체 오디오 트랙으로 기본설정됩니다. FPS 오프라인 모드에 대한 이 동작을 보정하려면 스트림에서 대체 오디오 트랙을 제거합니다. Media Services에서 이를 실행하기 위해 동적 매니페스트 필터 "audio-only=false"를 추가합니다. 즉, HLS URL은 .ism/manifest(format=m3u8-aapl,audio-only=false)로 끝납니다. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>audio-only=false를 추가한 후에도 왜 여전히 오프라인 모드에서 동영상 없이 오디오만 재생되나요?

CDN(콘텐츠 배달 네트워크) 캐시 키 디자인에 따라, 콘텐츠가 캐시될 수 있습니다. 캐시를 제거합니다.

#### <a name="is-fps-offline-mode-also-supported-on-ios-11-in-addition-to-ios-10"></a>FPS 오프라인 모드 또한 iOS 10 외에도 iOS 11에서 지원됩니까?

예. FPS 오프라인 모드는 iOS 10과 iOS 11 모두에서 지원됩니다.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>FPS Server SDK에서 “FairPlay 스트리밍 및 HTTP 라이브 스트리밍을 사용하여 오프라인 재생” 문서를 찾을 수 없는 이유는 무엇인가요?

FPS Server SDK 버전 4부터 이 문서는 “FairPlay Streaming Programming Guide”에 병합되었습니다.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 디바이스에서 다운로드된/오프라인 파일 구조체는 무엇입니까?

iOS 디바이스에 다운로드된 파일 구조체는 다음 스크린샷과 같습니다. `_keys` 폴더에는 다운로드된 FPS 라이선스가 저징됩니다(각 라이선스 서비스 호스트당 하나의 저장소 파일). `.movpkg` 폴더에는 오디오 및 동영상 콘텐츠가 저장됩니다. 대시에 이어 숫자로 끝나는 이름의 첫 번째 폴더는 동영상 콘텐츠를 포함합니다. 숫자 값은 동영상 변환의 PeakBandwidth입니다. 대시에 이어 0으로 끝나는 이름의 두 번째 폴더는 오디오 콘텐츠를 포함합니다. "Data"라는 이름의 세 번째 폴더는 FPS 콘텐츠의 마스터 재생 목록을 포함합니다. 마지막으로, boot.xml은 `.movpkg` 폴더 내용에 대한 전체 설명을 제공합니다. 

![오프라인 FairPlay iOS 샘플 앱 파일 구조체](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

샘플 boot.xml 파일:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>안드로이드에 대한 와이드 스트리밍

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-key"></a>일부 클라이언트/사용자에게는 영구 라이선스(오프라인 사용 가능)를 제공하고 일부에게는 비영구 라이선스(오프라인 사용 불가)를 제공하려면 어떻게 해야 하나요? 콘텐츠를 복제하고 별도 콘텐츠 키를 사용해야 하나요?

Media Services v3에서는 자산에 여러 StreamingLocator가 포함될 수 있기 때문입니다. 다음을 유지할 수 있습니다.

* license_type = "persistent"인 하나의 ContentKeyPolicy, "persistent"에 대한 클레임이 있는 ContentKeyPolicyRestriction 및 해당 StreamingLocator
* license_type = "nonpersistent"인 다른 ContentKeyPolicy, "nonpersistent"에 대한 클레임이 있는 ContentKeyPolicyRestriction 및 해당 StreamingLocator
* 두 StreamingLocator의 ContentKey가 다릅니다.

사용자 지정 STS의 비즈니스 논리에 따라 JWT 토큰에서 다른 클레임이 발급됩니다. 이 토큰을 사용하여 해당 라이선스만 가져올 수 있고 해당 URL만 재생할 수 있습니다.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>와이드바인과 미디어 서비스 DRM 보안 수준 간의 매핑은 무엇입니까?

Google의 "와이드바인 DRM 아키텍처 개요"는 세 가지 보안 수준을 정의합니다. 그러나 [Widevine 라이선스 템플릿에 대한 Azure Media Services 설명서](widevine-license-template-overview.md)에는 5개의 보안 수준이 설명되어 있습니다. 이 섹션에서는 보안 수준이 매핑되는 방법을 설명합니다.

Google의 "와이드바인 DRM 아키텍처 검토" 문서는 다음과 같은 세 가지 보안 수준을 정의합니다.

* 보안 수준 1: 모든 콘텐츠 처리, 암호화 및 제어가 TEE(신뢰할 수 있는 실행 환경)에서 수행됩니다. 일부 구현 모델에서 보안 처리는 다른 칩에서 수행될 수 있습니다.
* 보안 수준 2: TEE 내에서 암호화(비디오 처리는 아님)를 수행합니다. 암호 해독된 버퍼는 애플리케이션 도메인으로 반환되고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리됩니다. 그러나 수준 2에서 암호화 정보는 여전히 TEE 내에서만 처리됩니다.
* 보안 수준 3: 디바이스에 TEE가 없습니다. 호스트 운영 체제의 암호화 정보 및 암호 해독된 콘텐츠를 보호하기 위해 적절한 조치가 수행될 수 있습니다. 수준 3 구현에도 하드웨어 암호화 엔진이 포함될 수 있지만 보안이 아닌 성능만 향상시킵니다.

동시에, [Widevine 라이선스 템플릿에 대한 Azure Media Services 설명서](widevine-license-template-overview.md)에서 content_key_specs의 security_level 속성은 다음과 같은 5가지 값(재생을 위한 클라이언트 견고성 요구 사항)을 가질 수 있습니다.

* 소프트웨어 기반 화이트 박스 암호화가 필요합니다.
* 소프트웨어 암호화 및 난독 처리된 디코더가 필요합니다.
* 하드웨어 기반의 TEE에서 키 자료 및 암호화 작업을 수행해야 합니다.
* 하드웨어 기반의 TEE에서 콘텐츠의 암호화 및 디코딩을 수행해야 합니다.
* 하드웨어 기반의 TEE에서 미디어의 암호화, 디코딩 및 모든 처리(압축 및 압축 해제)를 수행해야 합니다.

두 보안 수준은 Google Widevine에 의해 정의됩니다. 차이점은 사용 수준이 아키텍처 수준인지 또는 API 수준인지 입니다. 5가지 보안 수준은 Widevine API에서 사용됩니다. security_level을 포함하는 content_key_specs 개체는 Azure Media Services Widevine 라이선스 서비스에 의해 역직렬화된 후 Widevine 전역 배달 서비스로 전달됩니다. 아래 표에서는 두 보안 수준 집합 간의 매핑을 보여 줍니다.

| **Widevine 아키텍처에 정의되는 보안 수준** |**Widevine API에서 사용되는 보안 수준**|
|---|---| 
| **보안 수준 1**: 모든 콘텐츠 처리, 암호화 및 제어는 신뢰할 수 있는 실행 환경(TEE) 내에서 수행됩니다. 일부 구현 모델에서 보안 처리는 다른 칩에서 수행될 수 있습니다.|**보안 수준 5**: 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 미디어의 암호화, 디코딩 및 모든 처리(압축 및 압축 해제)를 수행해야 합니다.<br/><br/>**보안 수준 4**: 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 콘텐츠의 암호화 및 디코딩을 수행해야 합니다.|
**보안 수준 2**: TEE 내에서 암호화(비디오 처리는 아님)를 수행합니다. 암호 해독된 버퍼는 애플리케이션 도메인으로 반환되고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리됩니다. 그러나 수준 2에서 암호화 정보는 여전히 TEE 내에서만 처리됩니다.| **보안 수준 3**: 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 키 자료 및 암호화 작업을 수행해야 합니다. |
| **보안 수준 3**: 디바이스에 TEE가 없습니다. 호스트 운영 체제의 암호화 정보 및 암호 해독된 콘텐츠를 보호하기 위해 적절한 조치가 수행될 수 있습니다. 수준 3 구현에도 하드웨어 암호화 엔진이 포함될 수 있지만 보안이 아닌 성능만 향상시킵니다. | **security_level =2**: 소프트웨어 암호 및 난독 처리 된 디코더가 필요합니다.<br/><br/>**security_level =1**: 소프트웨어 기반 화이트 박스 암호화가 필요합니다.|

#### <a name="why-does-content-download-take-so-long"></a>콘텐츠 다운로드 시간이 너무 오래 걸리는 이유는 무엇인가요?

다운로드 속도를 개선하는 방법으로는 다음 두 가지가 있습니다.

* 최종 사용자가 콘텐츠 다운로드를 위해 원본/스트리밍 엔드포인트 대신, CDN에 연결하기 쉽도록 CDN을 사용하도록 설정합니다. 사용자가 스트리밍 엔드포인트에 도달하는 경우 각 HLS 세그먼트 또는 DASH 조각이 동적으로 패키지되고 암호화됩니다. 이러한 대기 시간이 각 세그먼트/조각에 대해 밀리초 규모에 불과하지만, 1시간짜리 비디오가 있는 경우 누적된 대기 시간 때문에 다운로드가 더 오래 걸릴 수 있습니다.
* 최종 사용자에게 모든 콘텐츠가 아닌, 비디오 화질 계층 및 오디오 트랙만 선택적으로 다운로드하는 옵션을 제공합니다. 오프라인 모드에서는 모든 화질 계층을 다운로드할 수 있는 시점이 없습니다. 두 가지 방법으로 이 작업을 수행할 수 있습니다.

   * 클라이언트 제어: 비디오 화질 계층 및 다운로드할 오디오 트랙을 플레이어 앱이 자동으로 선택하도록 하거나 사용자가 선택하도록 합니다.
   * 서비스 제어: Azure Media Services의 동적 매니페스트 기능으로 (전역) 필터를 만들어, HLS 재생 목록 또는 DASH MPD를 단일 비디오 화질 계층 및 선택한 오디오 트랙으로 제한할 수 있습니다. 그러면 최종 사용자에게 표시되는 다운로드 URL에 이 필터가 포함됩니다.

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
