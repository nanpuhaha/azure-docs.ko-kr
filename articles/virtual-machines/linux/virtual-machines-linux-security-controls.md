---
title: Azure 리눅스 가상 머신에 대 한 보안 컨트롤 - 리눅스
description: Azure Linux 가상 컴퓨터를 평가하기 위한 보안 컨트롤 검사 목록
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190566"
---
# <a name="security-controls-for-linux-virtual-machines"></a>리눅스 가상 머신에 대 한 보안 컨트롤

이 문서에서는 Linux 가상 컴퓨터에 내장된 보안 컨트롤을 문서화합니다.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| yes | |
| VNet 주입 지원| yes | |
| 네트워크 격리 및 방화벽 지원| yes |  |
| 강제 터널링 지원| yes | [Azure 리소스 관리자 배포 모델을 사용하여 강제 터널링 구성을](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)참조하십시오. |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | [Azure에서 Linux 가상 컴퓨터 모니터링 및 업데이트를](/azure/virtual-machines/linux/tutorial-monitoring)참조하십시오. |
| 제어 및 관리 평면 로깅 및 감사| yes |  |
| 데이터 평면 로깅 및 감사 | 예 |  |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes |  |
| 권한 부여| yes |  |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes | [리눅스 VM에 대한 Azure 디스크 암호화를](disk-encryption-overview.md)참조하십시오. |
| 전송 중 암호화(예: 익스프레스라우팅 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | Azure 가상 [머신은 ExpressRoute](/azure/expressroute) 및 VNet 암호화를 지원합니다. [VM의 전송 중 암호화를](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)참조하십시오. |
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | yes | 고객 관리 키는 지원되는 Azure 암호화 시나리오입니다. [Azure 암호화 개요를](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)참조하십시오.|
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |
| API 호출 암호화| yes | HTTPS 및 TLS를 통해. |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes |  | 

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
