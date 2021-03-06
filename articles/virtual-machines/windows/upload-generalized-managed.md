---
title: 업로드된 일반화된 VHD에서 VM 만들기
description: 일반화된 VHD를 Azure에 업로드하고 이를 사용하여 Resource Manager 배포 모델에서 새 VM을 만듭니다.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464941"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>일반화된 VHD를 업로드하고 사용하여 Azure에서 새 VM 만들기

이 문서에서는 PowerShell을 사용하여 일반화된 VM의 VHD를 Azure에 업로드하고, VHD에서 이미지를 만들고, 해당 이미지에서 새 VM을 만드는 과정을 안내합니다. 다른 클라우드 또는 온-프레미스 가상화 도구에서 내보낸 VHD를 업로드할 수 있습니다. 새 VM에 대해 [Managed Disks](managed-disks-overview.md)를 사용하면 VM 관리가 간소화되고 VM이 가용성 집합에 배치되는 경우 가용성이 증가됩니다. 

샘플 스크립트는 [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

- Azure에 VHD를 업로드하기 전에 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행해야 합니다.
- [Managed Disks](managed-disks-overview.md)로 마이그레이션을 시작하기 전에 [Managed Disks로 마이그레이션하기 위한 계획](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)을 검토하세요.

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Sysprep을 사용하여 원본 VM 일반화

아직 VHD를 Azure에 업로드하기 전에 VM을 Sysprep해야 합니다. Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 개요](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원을](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)참조하십시오.

> [!IMPORTANT]
> Azure에 VHD를 처음으로 업로드하기 전에 Sysprep을 실행하려는 경우 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다. 
> 
> 

1. Windows 가상 머신에 로그인
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 %windir%\system32\sysprep으로 변경한 다음, `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택합니다.
5. **확인**을 선택합니다.
   
    ![Sysprep 시작](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep이 완료되면 가상 머신을 종료합니다. VM을 다시 시작하지 않습니다.


## <a name="upload-the-vhd"></a>VHD 업로드 

이제 VHD를 관리 디스크에 바로 업로드할 수 있습니다. 지침은 Azure [PowerShell을 사용하여 Azure에 VHD 업로드를](disks-upload-vhd-to-managed-disk-powershell.md)참조하십시오.



VHD가 관리 디스크에 업로드되면 [Get-AzDisk를](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) 사용하여 관리디스크를 받아야 합니다.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>이미지 만들기
일반화된 OS 관리 디스크에서 관리되는 이미지를 만듭니다. 다음 값을 사용자 고유의 정보로 바꿉니다.

먼저 몇 가지 변수를 설정합니다.

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

관리 디스크를 사용하여 이미지를 만듭니다.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

이미지를 만듭니다.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>VM 만들기

이미지가 생겼으니, 이 이미지에서 하나 이상의 새 VM을 만들 수 있습니다. 이 예제에서는 *myResourceGroup*에 *myImage*에서 *myVM*이라는 VM을 만듭니다.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>다음 단계

새 가상 머신에 로그인합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 

