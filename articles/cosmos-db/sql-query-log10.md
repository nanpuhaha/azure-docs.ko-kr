---
title: Azure 코스모스 DB 쿼리 언어의 LOG10
description: Azure Cosmos DB의 LOG10 SQL 시스템 함수에 대해 알아서 지정된 숫자 식의 기본 10 로그릿헴을 반환합니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302494"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure 코스모스 DB)
 지정한 숫자 식의 상용 로그(밑 10)를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expression*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
  LOG10과 POWER 함수는 서로 역함수 관계에 있습니다. 예를 들어 10 ^ LOG10(n) = n입니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (100)의 LOG10 값을 반환합니다.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
