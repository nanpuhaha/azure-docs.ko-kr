---
title: Azure VM에서 SQL Server 데이터베이스 복원
description: 이 문서에서는 Azure VM에서 실행 중이며 Azure Backup으로 백업되는 SQL Server 데이터베이스를 복원하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252455"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM에서 SQL Server 데이터베이스 복원

이 문서에서는 [Azure 백업](backup-overview.md) 서비스가 Azure 백업 복구 서비스 자격 증명 모음에 백업한 Azure 가상 시스템(VM)에서 실행 중인 SQL Server 데이터베이스를 복원하는 방법에 대해 설명합니다.

이 문서에서는 SQL Server 데이터베이스를 복원하는 방법을 설명합니다. 자세한 내용은 [Azure VM의 SQL Server 데이터베이스 백업을](backup-azure-sql-database.md)참조하십시오.

## <a name="restore-to-a-time-or-a-recovery-point"></a>시간 또는 복구 지점으로 복원

Azure Backup은 다음과 같이 Azure VM에서 실행 중인 SQL Server 데이터베이스를 복원할 수 있습니다.

- 트랜잭션 로그 백업을 사용하여 특정 날짜 또는 시간(두 번째)으로 복원합니다. Azure Backup은 선택한 시간에 따라 복원하는 데 필요한 적절한 전체 차동 백업 및 로그 백업 체인을 자동으로 결정합니다.
- 특정 전체 또는 차등 백업을 복원하여 특정 복구 지점으로 복원합니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터베이스를 복원하기 전에 다음 을 기록해 둡을 기록합니다.

- 동일한 Azure 지역의 SQL Server 인스턴스에 데이터베이스를 복원할 수 있습니다.
- 대상 서버를 원본과 동일한 자격 증명 모음에 등록해야 합니다.
- TDE 암호화된 데이터베이스를 다른 SQL Server로 복원하려면 먼저 [인증서를 대상 서버로 복원해야](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)합니다.
- "마스터" 데이터베이스를 복원하기 전에 시작 옵션 **-m AzureWorkloadBackup을**사용하여 단일 사용자 모드에서 SQL Server 인스턴스를 시작합니다.
  - **-m의** 값은 클라이언트의 이름입니다.
  - 지정된 클라이언트 이름만 연결을 열 수 있습니다.
- 모든 시스템 데이터베이스(모델, 마스터, msdb)의 경우 복원을 트리거하기 전에 SQL Server 에이전트 서비스를 중지합니다.
- 이러한 데이터베이스에 연결을 시도할 수 있는 모든 응용 프로그램을 닫습니다.
- 서버에서 실행 중인 인스턴스가 여러 개 있는 경우 모든 인스턴스가 실행되고 실행되어야 하며 그렇지 않으면 데이터베이스를 복원할 대상 서버 목록에 서버가 나타나지 않습니다.

## <a name="restore-a-database"></a>데이터베이스 복원

복원하려면 다음 권한이 필요합니다.

- 복원을 수행하는 볼트의 **백업 운영자** 권한입니다.
- 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
- 대상 VM에 대한 **참가자(쓰기)** 액세스 권한
  - 동일한 VM으로 복원하는 경우 원본 VM입니다.
  - 대체 위치로 복원하는 경우 새 대상 VM입니다.

다음과 같이 복원합니다.

1. SQL Server VM이 등록된 자격 증명 모음을 엽니다.
2. 자격 증명 모음 대시보드의 **사용 현황** 아래에서 **백업 항목**을 선택합니다.
3. **백업 항목**의 **백업 관리 유형**에서 **Azure VM의 SQL**을 선택합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 복원할 데이터베이스를 선택합니다.

    ![복원할 데이터베이스를 선택합니다.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 데이터베이스 메뉴를 검토합니다. 다음과 같은 데이터베이스 백업에 대한 정보를 제공합니다.

    - 가장 오래된 복원 지점 및 최신 복원 지점
    - 전체 및 대량 로깅 복구 모드에 있고 트랜잭션 로그 백업용으로 구성된 데이터베이스의 지난 24시간 로그 백업 상태입니다.

6. **복원**을 선택합니다.

    ![복원 선택](./media/backup-azure-sql-database/restore-db.png)

7. **복원 구성에서**데이터를 복원할 위치(또는 방법)를 지정합니다.
   - **대체 위치**: 데이터베이스를 대체 위치로 복원하고 원래 원본 데이터베이스를 유지합니다.
   - **DB 덮어쓰기**: 원래 원본과 동일한 SQL Server 인스턴스에 데이터를 복원합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다.

    > [!IMPORTANT]
    > 선택한 데이터베이스가 Always On 가용성 그룹에 속하면 SQL Server에서 데이터베이스를 덮어쓸 수 없습니다. **대체 위치**만 사용할 수 있습니다.
    >
   - **파일로 복원**: 데이터베이스로 복원하는 대신 SQL Server Management Studio를 사용하여 파일이 있는 컴퓨터에서 나중에 데이터베이스로 복구할 수 있는 백업 파일을 복원합니다.
     ![복원 구성 메뉴](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. 구성 **복원** 메뉴에서 **복원 할 위치**아래에서 **대체 위치를**선택합니다.
2. 데이터베이스를 복원하려는 SQL Server 이름 및 인스턴스를 선택합니다.
3. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.
4. 해당하는 경우 **선택한 SQL 인스턴스에 이름이 같은 DB가 이미 있는 경우 덮어쓰기를**선택합니다.
5. **확인**을 선택합니다.

    ![복원 구성 메뉴에 대한 값 제공](./media/backup-azure-sql-database/restore-configuration.png)

6. **복원 지점 선택에서** [특정 시점으로 복원할지](#restore-to-a-specific-point-in-time) 또는 [특정 복구 지점으로 복원할지 선택합니다.](#restore-to-a-specific-restore-point)

    > [!NOTE]
    > 시점 복원은 전체 및 대량 로깅 복구 모드에 있는 데이터베이스에 대한 로그 백업에만 사용할 수 있습니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. 구성 **복원** 메뉴에서 **복원 할 위치**아래에서 DB >  **덮어쓰기****확인을**선택합니다.

    ![DB 덮어쓰기 선택](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. **복원 지점 선택에서** **로그(시간 시점)를** 선택하여 [특정 시점으로 복원합니다.](#restore-to-a-specific-point-in-time) 또는 [특정 복구 지점으로](#restore-to-a-specific-restore-point)복원하려면 **전체 & 차동을** 선택합니다.

    > [!NOTE]
    > 시점 복원은 전체 및 대량 로깅 복구 모드에 있는 데이터베이스에 대한 로그 백업에만 사용할 수 있습니다.

### <a name="restore-as-files"></a>파일로 복원

백업 데이터를 데이터베이스 대신 .bak 파일로 복원하려면 **파일로 복원을**선택합니다. 파일이 지정된 경로로 덤프되면 이러한 파일을 데이터베이스로 복원하려는 모든 컴퓨터로 가져갈 수 있습니다. 이러한 파일을 모든 컴퓨터로 이동할 수 있으므로 이제 구독 및 지역 간에 데이터를 복원할 수 있습니다.

1. 구성 **복원** 메뉴에서 **복원 위치**아래에서 **파일로 복원을**선택합니다.
2. 백업 파일을 복원할 SQL Server 이름을 선택합니다.
3. **서버의 대상 경로에서** 2단계에서 선택한 서버의 폴더 경로를 입력합니다. 서비스가 필요한 모든 백업 파일을 덤프하는 위치입니다. 일반적으로 대상 경로로 지정될 때 네트워크 공유 경로 또는 탑재된 Azure 파일 공유의 경로를 사용하면 동일한 네트워크의 다른 컴퓨터 또는 동일한 Azure 파일 공유가 탑재되어 이러한 파일에 쉽게 액세스할 수 있습니다.<BR>

    >등록된 대상 VM에 탑재된 Azure 파일 공유에서 데이터베이스 백업 파일을 복원하려면 NT AUTHORITY\SYSTEM이 파일 공유에 액세스할 수 있는지 확인합니다. 아래 단계를 수행하여 VM에 장착된 AFS에 읽기/쓰기 권한을 부여할 수 있습니다.
    >
    >- NT `PsExec -s cmd` 기관\SYSTEM 셸에 입력하려면 실행
    >   - `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` 실행
    >   - 를 통해 액세스 확인`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- 백업 볼트에서 경로로 파일로 `\\<storageacct>.file.core.windows.net\<filesharename>` 복원시작<BR>
    당신은 통해 Psexec을 다운로드 할 수 있습니다<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. **확인**을 선택합니다.

    ![파일로 복원 선택](./media/backup-azure-sql-database/restore-as-files.png)

5. 사용 가능한 모든 .bak 파일이 복원될 복원 **지점을** 선택합니다.

    ![복원 점 선택](./media/backup-azure-sql-database/restore-point.png)

6. 선택한 복구 지점과 연결된 모든 백업 파일이 대상 경로에 덤프됩니다. SQL Server 관리 Studio를 사용하여 있는 모든 컴퓨터에서 파일을 데이터베이스로 복원할 수 있습니다.

    ![대상 경로에서 복원된 백업 파일](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

1. **복원 날짜/시간**에서 달력을 엽니다. 캘린더에서 복구 지점이 있는 날짜가 굵게 표시되고 현재 날짜가 강조 표시됩니다.
1. 복구 지점이 있는 날짜를 선택합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

    ![캘린더 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 날짜를 선택한 후에는 타임라인 그래프에 사용 가능한 복구 지점이 연속적인 범위로 표시됩니다.
1. 타임라인 그래프에서 복구 시간을 지정하거나 시간을 선택합니다. 그런 다음 **확인을**선택합니다.

    ![복원 시간 선택](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. 고급 **구성** 메뉴에서 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면 **NORECOVERY를 사용하여 복원을**사용하도록 설정합니다.
1. 대상 서버에서 복원 위치를 변경하려면 새 대상 경로를 입력합니다.
1. **확인**을 선택합니다.

    ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.
1. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **작업 복원을** 선택하여 복원진행 상황을 추적합니다.

    ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>특정 복원 지점으로 복원

복원 유형으로 **전체 및 차등**을 선택한 경우 다음을 수행합니다.

1. 목록에서 복구 지점을 선택하고 **확인**을 선택하여 복원 지점 절차를 완료합니다.

    ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > 기본적으로 지난 30일 간의 복구 지점이 표시됩니다. **필터를** 클릭하고 사용자 지정 범위를 선택하여 30일이 지난 복구 지점을 표시할 수 있습니다.

1. 고급 **구성** 메뉴에서 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면 **NORECOVERY를 사용하여 복원을**사용하도록 설정합니다.
1. 대상 서버에서 복원 위치를 변경하려면 새 대상 경로를 입력합니다.
1. **확인**을 선택합니다.

    ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.
1. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **작업 복원을** 선택하여 복원진행 상황을 추적합니다.

    ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>파일 수가 많은 데이터베이스 복원

데이터베이스의 파일의 총 문자열 크기가 [특정 제한보다](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)큰 경우 Azure Backup은 복원 작업 중에 대상 복원 경로를 설정할 수 없는 다른 구덩이 구성 요소에 데이터베이스 파일 목록을 저장합니다. 대신 파일이 SQL 기본 경로로 복원됩니다.

  ![큰 파일로 데이터베이스 복원](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>다음 단계

[관리 및 모니터링](manage-monitor-sql-database-backup.md) Azure 백업에서 백업하는 SQL Server 데이터베이스입니다.
