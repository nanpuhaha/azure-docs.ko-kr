---
title: Azure 관리 디스크에 대한 공유 디스크 사용
description: 여러 VM에서 공유할 수 있도록 공유 디스크(미리 보기)로 Azure 관리 디스크 구성
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970373"
---
# <a name="enable-shared-disk"></a>공유 디스크 사용

이 문서에서는 Azure 관리 디스크에 대한 공유 디스크(미리 보기) 기능을 활성화하는 방법을 설명합니다. Azure 공유 디스크(미리 보기)는 관리되는 디스크를 여러 가상 시스템(VM)에 동시에 연결할 수 있는 Azure 관리 디스크의 새로운 기능입니다. 관리 디스크를 여러 VM에 연결하면 새 디스크를 배포하거나 기존 클러스터된 응용 프로그램을 Azure에 마이그레이션할 수 있습니다. 

공유 디스크를 사용하도록 설정한 관리 디스크에 대한 개념 정보를 찾고 있는 경우 [Azure 공유 디스크 를](disks-shared.md)참조하십시오.
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]