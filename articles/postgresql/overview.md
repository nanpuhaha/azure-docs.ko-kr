---
title: Azure Database for PostgreSQL 관계형 데이터베이스 서비스 개요
description: PostgreSQL 관계형 데이터베이스 서비스에 대한 Azure Docs 개요를 제공합니다.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74481664"
---
# <a name="what-is-azure-database-for-postgresql"></a>PostgreSQL용 Azure Database란?
Azure Database for PostgreSQL은 개발자용으로 빌드된 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. 오픈 소스 [PostgreSQL](https://www.postgresql.org/) 데이터베이스 엔진의 커뮤니티 버전을 기반으로 하며, 단일 서버 및 하이퍼스케일(Citus)의 두 가지 배포 옵션으로 사용할 수 있습니다.

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버
단일 서버 배포 옵션은 다음과 같은 이점을 누릴 수 있습니다.

- 추가 비용 없이 기본 제공되는 [고가용성](concepts-high-availability.md)(99.99% SLA)
- 예측 가능한 성능, 포괄적인 종량제 가격 책정 사용
- 몇 초 내에 [필요에 따라 수직적 크기 조정](concepts-pricing-tiers.md)
- 서버를 평가하기 위한 [모니터링 및 경고](concepts-monitoring.md)
- 엔터프라이즈급 보안 및 규정 준수
- 중요한 미사용 및 이동 데이터 [보호](concepts-security.md)
- 최대 35일 동안 [자동 백업 및 지정 시간 복원](concepts-business-continuity.md)


이러한 모든 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 그러면 가상 머신과 인프라를 관리하는 데 귀중한 시간과 리소스를 소모하는 대신, 빠른 앱 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. 새로운 기술을 습득하지 않고도 원하는 오픈 소스 도구와 플랫폼을 사용하여 애플리케이션을 계속 개발할 수 있습니다.

단일 서버 배포 옵션에서 제공하는 세 가지 가격 책정 계층은 기본, 범용 및 메모리 최적화의 세 가지 가격 책정 계층 중 하나에서 만들 수 있습니다. 각 계층에서는 데이터베이스 작업을 지원하는 다양한 리소스 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 솔루션의 요구에 맞게 규모를 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다. 자세한 내용은  [가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus)
하이퍼스케일(Citus) 옵션은 분할을 사용하여 여러 머신에 걸쳐 쿼리 크기를 수평으로 조정합니다. 쿼리 엔진은 들어오는 SQL 쿼리를 이러한 서버 간에 병렬 처리하여 큰 데이터 세트에서 더 빠르게 응답합니다. 이 옵션은 더 뛰어난 크기 조정과 성능이 필요한 애플리케이션, 일반적으로 100GB의 데이터에 이르거나 이미 초과한 워크로드를 처리합니다.

하이퍼스케일(Citus) 배포 옵션은 다음과 같은 이점을 누릴 수 있습니다.

- 분할을 사용하여 여러 머신에서 수평적 크기 조정
- 이러한 서버 간에 쿼리를 병렬 처리하여 큰 데이터 세트에서 더 빠르게 응답
- 다중 테넌트 애플리케이션, 실시간 운영 분석 및 높은 처리량 트랜잭션 워크로드에 대한 탁월한 지원

PostgreSQL용으로 빌드된 애플리케이션은 표준 [연결 라이브러리](./concepts-connection-libraries.md) 및 최소한의 변경을 통해 하이퍼스케일(Citus)에서 분산 쿼리를 실행할 수 있습니다.

## <a name="contacts"></a>연락처
Azure Database for PostgreSQL 작업에 대한 질문이나 제안 사항이 있으면 Azure Database for PostgreSQL 팀([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com))으로 이메일을 보내주세요. 이 주소는 지원 티켓이 아니라 일반적인 질문을 위해 제공되는 주소입니다.

또한 문의 시 다음 사항을 적절히 고려합니다.
- Azure 지원에 문의하거나 계정 관련 문제를 해결하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계
- 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
- 첫 번째 Azure Database for PostgreSQL [단일 서버](./quickstart-create-server-database-portal.md) 또는 [하이퍼스케일(Citus)](./quickstart-create-hyperscale-portal.md)을 만들어 시작합니다.
- Python, PHP, Ruby, C\#, Java, Node.js에서 첫 번째 앱을 빌드합니다([연결 라이브러리](./concepts-connection-libraries.md)).
