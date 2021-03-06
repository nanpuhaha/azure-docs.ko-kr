---
title: 자주 묻는 질문 - Azure VM 백업
description: 이 문서에서는 Azure 백업 서비스를 사용하여 Azure VM을 백업하는 것에 대한 일반적인 질문에 대한 답변을 알아보십시오.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 5d2f702b49e1e7aeb2ab33008556e91264b39427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705414"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>자주 묻는 질문 - Azure VM 백업

이 문서에서는 Azure [백업](backup-introduction-to-azure-backup.md) 서비스를 통해 Azure VM을 백업하는 것에 대한 일반적인 질문에 대해 답변합니다.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>어떤 VM 이미지를 만들 때 백업에 사용할 수 있습니까?

VM을 만들 때 [지원되는 운영 체제를](backup-support-matrix-iaas.md#supported-backup-actions) 실행하는 VM에 대한 백업을 활성화할 수 있습니다.

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>백업 비용이 VM 비용에 포함되어 있습니까?

아니요. 백업 비용은 VM 비용과 는 별개입니다. [Azure 백업 가격 책정에](https://azure.microsoft.com/pricing/details/backup/)대해 자세히 알아보십시오.

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>VM에 대한 백업을 활성화하는 데 필요한 권한은 무엇입니까?

VM 기고자인 경우 VM에서 백업을 활성화할 수 있습니다. 사용자 지정 역할을 사용하는 경우 VM에서 백업을 사용하려면 다음 권한이 필요합니다.

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

복구 서비스 자격 증명 모음 및 VM에 다른 리소스 그룹이 있는 경우 복구 서비스 자격 증명 모음에 대한 리소스 그룹에 쓰기 권한이 있는지 확인합니다.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>주문형 백업 작업은 예약된 백업과 동일한 보존 일정을 사용하나요?

아니요. 주문형 백업 작업에 대한 보존 범위를 지정합니다. 기본적으로 포털에서 트리거된 이후 30일 동안 유지됩니다.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>최근에 일부 VM에서 Azure Disk Encryption을 사용할 수 있습니다. 내 백업이 계속 작동하나요?

Azure 백업에서 키 자격 증명 모음에 액세스할 수 있는 권한을 제공합니다. [Azure Backup PowerShell](backup-azure-vms-automation.md) 설명서의 **백업 사용** 섹션에 설명된 대로 PowerShell에서 권한을 지정합니다.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>VM 디스크를 관리 디스크로 마이그레이션했습니다. 내 백업이 계속 작동하나요?

예. 백업은 원활하게 작동합니다. 아무 것도 다시 구성할 필요가 없습니다.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>백업 구성 마법사에서 내 VM을 볼 수 없는 이유는 무엇인가요?

이 마법사는 자격 증명 모음과 동일한 지역에 있고 아직 백업되지 않은 VM만 나열합니다.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>내 VM이 종료되었습니다. 요청 시 또는 예약된 백업 작업도 종료되나요?

예. 머신이 종료되면 백업이 실행됩니다. 복구 지점은 충돌 일치로 표시됩니다.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>진행 중인 백업 작업을 취소할 수 있나요?

예. **스냅샷 만들기** 단계에 있는 백업 작업을 취소할 수 있습니다. 스냅샷에서 데이터 전송이 진행 중인 경우 작업을 취소할 수 없습니다.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Azure 백업 서비스에서 만든 리소스 그룹에 대한 잠금을 사용하도록 설정했습니다(예: `AzureBackupRG_<geo>_<number>`) 백업이 계속 작동합니까?

Azure Backup Service에서 만든 리소스 그룹을 잠그면 최대 18개의 복원 지점제한이 있으므로 백업이 실패하기 시작합니다.

사용자는 향후 백업을 성공적으로 수행하려면 잠금을 제거하고 해당 리소스 그룹에서 복원 지점 컬렉션을 지워야 하며 [다음 단계를 수행하여](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) 복원 지점 컬렉션을 제거해야 합니다.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Azure 백업이 표준 SSD 관리 디스크를 지원합니까?

예. Azure Backup은 [표준 SSD 관리 디스크를](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)지원합니다.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>WA(쓰기 가속기) 지원 디스크를 사용하여 VM을 백업할 수 있나요?

WA 지원 디스크에는 스냅샷을 만들 수 없습니다. 그러나 Azure Backup 서비스는 백업에서 WA 지원 디스크를 제외할 수 있습니다.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>WA(쓰기 가속기) 디스크를 사용하고 SAP HANA가 설치된 VM을 갖고 있습니다. 어떻게 백업해야 하나요?

Azure Backup은 WA 지원 디스크를 백업할 수 없지만 백업에서 제외할 수는 있습니다. 그러나 WA 지원 디스크의 정보가 백업되지 않으므로 백업하더라도 데이터베이스 일관성이 제공되지 않습니다. 운영 체제 디스크 백업 및 WA 미사용 디스크 백업을 원하는 경우 이 구성으로 디스크를 백업하면 됩니다.

15분의 RPO를 통해 SAP HANA 백업에 대한 비공개 미리 보기를 실행하고 있습니다. SQL DB 백업과 비슷한 방식으로 빌드되었으며, SAP HANA에서 인증한 backInt 인터페이스를 타사 솔루션에 사용합니다. 관심이 있으시면 Azure `AskAzureBackupTeam@microsoft.com` **VM에서 SAP HANA 백업을 위한 비공개 미리 보기에**등록하는 제목으로 이메일을 보내주십시오.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>VM 백업 정책에 설정된 예약된 백업 시간에서 백업 시작 시간에 예상할 수 있는 최대 지연은 얼마입니까?

예약된 백업은 예약된 백업 시간보다 2시간 이내에 트리거됩니다. 예를 들어 100개의 VM에 2:00 AM으로 예약된 백업 시작 시간이 있는 경우 최대 4:00 AM까지 모든 100개의 VM은 백업 작업이 진행 중입니다. 가동 중단으로 인해 예약된 백업이 일시 중지되고 다시 시작/다시 시도된 경우 예약된 2시간 기간 이외에 백업을 시작할 수 있습니다.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>일일 백업 포인트에 허용되는 최소 보존 범위는 얼마입니까?

Azure 가상 시스템 백업 정책은 최대 9999일까지 7일의 최소 보존 범위를 지원합니다. 7일 미만의 기존 VM 백업 정책을 수정하려면 최소 보존 범위를 7일 동안 충족하도록 업데이트해야 합니다.

### <a name="can-i-backup-or-restore-selective-disks-attached-to-a-vm"></a>VM에 연결된 선택적 디스크를 백업하거나 복원할 수 있습니까?

Azure Backup은 이제 Azure 가상 시스템 백업 솔루션을 사용하여 선택적 디스크 백업 및 복원을 지원합니다.

현재 Azure Backup은 가상 시스템 백업 솔루션을 사용하여 VM의 모든 디스크(운영 체제 및 데이터)를 함께 백업할 수 있도록 지원합니다. 제외 디스크 기능을 사용하면 VM의 여러 데이터 디스크에서 하나 또는 몇 개를 백업할 수 있는 옵션이 있습니다. 이를 통해 백업 및 복원 요구 사항에 대한 효율적이고 비용 효율적인 솔루션을 제공합니다. 각 복구 지점에는 백업 작업에 포함된 디스크 데이터가 포함되어 있으므로 복원 작업 중에 지정된 복구 지점에서 디스크 하위 집합을 복원할 수 있습니다. 이는 스냅샷과 볼트모두에서 복원하는 데 적용됩니다.

미리 보기에 등록하려면AskAzureBackupTeam@microsoft.com

## <a name="restore"></a>복원

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>디스크만 복원할지 아니면 전체 VM을 복원할지 결정하는 방법은 무엇인가요?

VM 복원은 Azure VM의 빠른 만들기 옵션이라고 생각하시면 됩니다. 이 옵션은 디스크 이름, 디스크에서 사용하는 컨테이너, 공용 IP 주소 및 네트워크 인터페이스 이름을 변경합니다. VM이 만들어지면 변경 내용이 고유한 리소스를 유지합니다. VM은 가용성 집합에 추가되지 않습니다.

원할 경우 다음과 같이 디스크 복원 옵션을 사용할 수 있습니다.

- 생성되는 VM을 사용자 지정합니다. 예를 들어 크기를 변경합니다.
- 백업 시 에는 없었던 구성 설정을 추가합니다.
- 생성되는 리소스의 명명 규칙을 제어합니다.
- 가용성 집합에 VM을 추가합니다.
- PowerShell 또는 템플릿을 사용하여 구성해야 하는 다른 설정을 추가합니다.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>관리 디스크로 업그레이드한 후 비관리형 VM 디스크의 백업을 복원할 수 있나요?

예, 디스크를 비관리형에서 관리형으로 마이그레이션하기 전에 만든 백업을 사용할 수 있습니다.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>VM이 관리 디스크로 마이그레이션되기 전에 VM을 복원 지점으로 복원하려면 어떻게 할까요?

복원 프로세스는 동일하게 유지됩니다. VM에 관리되지 않는 디스크가 있는 시점인 경우 [디스크를 관리되지 않는 것으로 복원할](tutorial-restore-disk.md#unmanaged-disks-restore)수 있습니다. VM이 디스크를 관리한 경우 [디스크를 관리 디스크로 복원할](tutorial-restore-disk.md#managed-disk-restore)수 있습니다. 그런 다음 [해당 디스크에서 VM을 만들](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)수 있습니다.

PowerShell에서 이 작업을 수행하는 방법을 [자세히 알아보세요](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>삭제된 VM을 복원할 수 있나요?

예. VM을 삭제하더라도 자격 증명 모음에서 해당 백업으로 이동하여 복구 지점으로 복원할 수 있습니다.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>VM을 동일한 가용성 집합으로 복원하려면 어떻게 해야 하나요?

관리 디스크 Azure VM의 경우 관리 디스크로 복원하는 동안 템플릿에서 옵션을 제공하여 가용성 집합에 복원하는 기능을 설정합니다. 이 템플릿에 **가용성 집합**이라는 입력 매개 변수가 있습니다.

### <a name="how-do-we-get-faster-restore-performances"></a>복원 성능을 높이려면 어떻게 해야 하나요?

[인스턴트 복원](backup-instant-restore-capability.md) 기능은 스냅샷에서 더 빠른 백업 및 즉각적인 복원에 도움이 됩니다.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>암호화된 VM의 키 자격 증명 모음 설정을 변경하면 어떻게 되나요?

암호화된 VM에 대한 KeyVault 설정을 변경한 후에도 백업은 새로운 세부 정보 집합으로 계속 작동합니다. 그러나 변경 하기 전에 복구 지점에서 복원 한 후 KeyVault에서 VM을 만들려면 하려면 KeyVault의 비밀을 복원 해야 합니다. 자세한 내용은 이 [문서를](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret) 참조하십시오.

비밀/키 롤오버와 같은 작업은 이 단계를 필요로 하지 않으며 복원 후 동일한 KeyVault를 사용할 수 있습니다.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>도메인 컨트롤러와의 관계가 끊어지면 VM에 액세스할 수 있습니까?

예. 도메인 컨트롤러와의 관계가 끊어지면 VM이 복원된 VM에 액세스할 수 있습니다. 자세한 내용은 이 [문서를](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) 참조하십시오.

## <a name="manage-vm-backups"></a>VM 백업 관리

### <a name="what-happens-if-i-modify-a-backup-policy"></a>백업 정책을 수정하면 어떻게 되나요?

VM은 수정된 정책 또는 새 정책의 일정 및 보존 설정을 사용하여 백업됩니다.

- 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 표시되고 유지됩니다.
- 보존 기간을 줄이면 복구 지점이 다음 정리 작업에서 정리하도록 표시되고, 결국 삭제됩니다.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Azure Backup에서 백업한 VM을 다른 리소스 그룹으로 이동하려면 어떻게 할까요?

1. 일시적으로 백업을 중지하고 백업 데이터를 보존합니다.
2. VM을 대상 리소스 그룹으로 이동합니다.
3. 동일하거나 새 볼트에서 백업을 다시 활성화합니다.

이동 작업 전에 만든 사용 가능한 복원 지점에서 VM을 복원할 수 있습니다.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>동일한 백업 정책과 연결할 수 있는 VM 수에 제한이 있습니까?

예. 포털에서 동일한 백업 정책에 연결할 수 있는 VM은 100개로 제한됩니다. 100개 이상의 VM의 경우 일정이 같거나 일정이 다른 여러 백업 정책을 만드는 것이 좋습니다.
