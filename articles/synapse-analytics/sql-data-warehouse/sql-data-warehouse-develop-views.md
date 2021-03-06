---
title: T-SQL 보기 사용
description: T-SQL 뷰를 사용하고 Synapse SQL 풀에서 솔루션을 개발하기 위한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633387"
---
# <a name="views-in-synapse-sql-pool"></a>시냅스 SQL 풀의 보기

뷰를 여러 가지 다양한 방법으로 사용하여 솔루션의 품질을 개선할 수 있습니다.

SQL 풀은 표준 뷰와 구체화된 뷰를 모두 지원합니다. 둘 다 SELECT 식으로 만든 가상 테이블이며 쿼리에 논리적 테이블로 제공됩니다.

뷰는 일반적인 데이터 계산의 복잡성을 캡슐화하고 계산 변경 사항에 추상화 계층을 추가하여 쿼리를 다시 작성할 필요가 없습니다.

## <a name="standard-view"></a>표준 뷰

표준 뷰는 뷰를 사용할 때마다 데이터를 계산합니다. 디스크에 저장된 데이터가 없습니다. 사람들은 일반적으로 표준 뷰를 데이터베이스의 논리 적 개체와 쿼리를 구성하는 데 도움이되는 도구로 사용합니다.

표준 뷰를 사용하려면 쿼리를 직접 참조해야 합니다. 자세한 내용은 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.

SQL 풀의 보기는 메타데이터로만 저장됩니다. 따라서 다음 옵션을 사용할 수 없습니다.

* 스키마 바인딩 옵션이 없습니다.
* 뷰를 통해 기본 테이블을 업데이트할 수 없습니다.
* 임시 테이블에서 뷰를 만들 수 없습니다.
* EXPAND / NOEXPAND 힌트에 대한 지원이 없습니다.
* SQL 풀에 인덱싱된 보기가 없습니다.

표준 뷰를 사용하여 테이블 간에 성능 최적화 조인을 적용할 수 있습니다. 예를 들어, 뷰는 데이터 이동을 최소화하는 조인 조건의 일부로 배포 중복 키를 통합할 수 있습니다.

뷰의 또 다른 장점은 특정 쿼리 또는 조인 힌트를 강제 적용할 수 있다는 것입니다. 이 방식으로 뷰를 사용하면 조인이 항상 최적의 방식으로 수행되므로 사용자가 자신의 조인에 대한 올바른 구문을 기억해야 할 필요가 없습니다.

## <a name="materialized-view"></a>구체화된 뷰

구체화된 뷰는 테이블과 마찬가지로 SQL 풀에서 데이터를 미리 계산, 저장 및 유지 관리합니다. 구체화된 뷰를 사용할 때마다 다시 계산할 필요가 없습니다.

데이터가 기본 테이블에 로드되면 SQL 풀은 구체화된 뷰를 동기적으로 새로 고칩니다.  쿼리 최적화 프로그램은 배포된 구체화된 뷰를 자동으로 사용하여 쿼리에서 뷰를 참조하지 않더라도 쿼리 성능을 향상시킵니다.  

구체화된 뷰에서 가장 많은 이점을 제공하는 쿼리는 작은 결과 집합을 생성하는 큰 테이블의 복잡한 쿼리(일반적으로 조인 및 집계가 있는 쿼리)입니다.  

구체화된 뷰 구문 및 기타 요구 사항에 대한 자세한 내용은 [구체화된 뷰 만들기 를 선택으로](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)참조하십시오.  

쿼리 튜닝 지침의 경우 [구체화된 뷰를 사용하여 성능 튜닝을](performance-tuning-materialized-views.md)선택합니다.

## <a name="example"></a>예제

일반적인 응용 프로그램 패턴은 데이터를 로드하는 동안 개체 이름 바꾸기 패턴다음에 CTAS(SELECT)로 만들기 테이블을 다시 만드는 것입니다.  

다음 예제에서는 새 날짜 레코드를 날짜 차원에 추가합니다. 먼저 새 테이블 DimDate_New를 만든 다음, 이름을 바꾸어 원래 버전의 테이블을 바꾸는 방법을 확인합니다.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

그러나 이 방법을 사용하면 "테이블이 존재하지 않습니다" 오류 메시지를 발행하는 대신 테이블이 나타나고 사용자 보기에서 사라질 수 있습니다.

뷰를 사용하여 사용자에게 일관된 프레젠테이션 계층을 제공하고 기본 개체의 이름을 바꿀 수 있습니다. 뷰를 통해 데이터에 대한 액세스를 제공함으로써 사용자는 기본 테이블에 대한 가시성을 필요로 하지 않습니다.

이 계층은 일관된 사용 환경을 제공하는 동시에 데이터 웨어하우스 디자이너가 데이터 모델을 발전시킬 수 있도록 보장합니다. 기본 테이블을 발전시킬 수 있으면 설계자가 CTAS를 사용하여 데이터 로드 프로세스 중에 성능을 최대화 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 개발 팁은 [SQL 풀 개발 개요를](sql-data-warehouse-overview-develop.md)참조하십시오.
