---
title: 디바이스에서 IoT Edge 버전 업데이트 - Azure IoT Edge | Microsoft Docs
description: 최신 버전의 보안 디먼 및 IoT Edge 런타임을 실행하도록 IoT Edge 디바이스를 업데이트하는 방법
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186504"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>IoT Edge 보안 디먼 및 런타임 업데이트

IoT Edge 서비스가 새 버전을 출시함에 따라 최신 기능 및 보안 개선을 위해 IoT Edge 장치를 업데이트해야 합니다. 이 문서에서는 새 버전을 사용할 수 있을 때 IoT Edge 디바이스를 업데이트하는 방법에 대한 정보를 제공합니다.

새 버전으로 전환하려면 IoT Edge 디바이스의 두 가지 구성 요소를 업데이트해야 합니다. 첫 번째는 디바이스에서 실행되고 디바이스가 시작될 때 런타임 모듈을 시작하는 보안 디먼입니다. 현재, 보안 디먼은 디바이스 자체에서만 업데이트할 수 있습니다. 두 번째 구성 요소는 런타임으로, IoT Edge 허브 및 IoT Edge 에이전트 모듈로 구성됩니다. 배포 구성 방법에 따라 디바이스에서 또는 원격으로 런타임을 업데이트할 수 있습니다.

최신 버전의 Azure IoT Edge를 찾으려면 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

## <a name="update-the-security-daemon"></a>보안 디먼 업데이트

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 패키지 관리자를 사용하여 업데이트해야 하는 네이티브 구성 요소입니다.

`iotedge version` 명령을 사용하여 디바이스에서 실행 중인 보안 디먼의 버전을 확인합니다.

### <a name="linux-devices"></a>Linux 디바이스

Linux x64 장치에서 apt-get 또는 적절한 패키지 관리자를 사용하여 보안 데몬을 최신 버전으로 업데이트합니다.

```bash
apt-get update
apt-get install libiothsm iotedge
```

특정 버전의 보안 데몬으로 업데이트하려면 [IoT Edge 릴리스에서](https://github.com/Azure/azure-iotedge/releases)대상으로 할 버전을 찾습니다. 해당 버전에서 장치에 적합한 **리바이오트스름-std** 및 **iotedge** 파일을 찾습니다. 각 파일에 대해 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다. 링크 주소를 사용하여 해당 구성 요소의 특정 버전을 설치합니다.

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows 디바이스

Windows 장치에서 PowerShell 스크립트를 사용하여 보안 데몬을 업데이트합니다. 스크립트는 보안 데몬의 최신 버전을 자동으로 가져옵니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Update-IoTEdge 명령을 실행하면 두 개의 런타임 컨테이너 이미지와 함께 장치에서 보안 데몬이 제거되고 업데이트됩니다. config.yaml 파일은 장치에 보관되며 Moby 컨테이너 엔진의 데이터(Windows 컨테이너를 사용하는 경우)도 보관됩니다. 구성 정보를 유지한다는 것은 업데이트 프로세스 중에 장치에 대한 연결 문자열 또는 장치 프로비저닝 서비스 정보를 다시 제공할 필요가 없다는 것을 의미합니다.

특정 버전의 보안 데몬으로 업데이트하려면 [IoT Edge 릴리스에서](https://github.com/Azure/azure-iotedge/releases)대상으로 할 버전을 찾습니다. 해당 버전에서 **Microsoft-Azure-IoTEdge.cab** 파일을 다운로드합니다. 그런 다음 `-OfflineInstallationPath` 매개 변수를 사용하여 로컬 파일 위치를 가리킵니다. 예를 들어:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>매개 `-OfflineInstallationPath` 변수는 제공된 디렉터리에서 **Microsoft-Azure-IoTEdge.cab라는** 파일을 찾습니다. IoT Edge 버전 1.0.9-rc4부터 AMD64 장치용 및 ARM32용 .cab 파일 2개가 있습니다. 장치에 맞는 파일을 다운로드한 다음 파일 이름을 변경하여 아키텍처 접미사를 제거합니다.

업데이트 옵션에 대한 자세한 내용은 `Get-Help Update-IoTEdge -full` 명령을 사용하거나 [모든 설치 매개 변수를](how-to-install-iot-edge-windows.md#all-installation-parameters)참조하십시오.

## <a name="update-the-runtime-containers"></a>런타임 컨테이너 업데이트

IoT Edge 에이전트 및 IoT Edge 허브 컨테이너를 업데이트하는 방법은 배포에서 롤링 태그(예: 1.0) 또는 특정 태그(예: 1.0.7)를 사용하는지 여부에 따라 달라집니다.

`iotedge logs edgeAgent` 또는 `iotedge logs edgeHub` 명령을 사용하여 현재 사용 중인 디바이스에 있는 IoT Edge 에이전트 및 IoT Edge 허브 모듈 버전을 확인합니다.

  ![로그의 컨테이너 버전 찾기](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge 태그 이해

IoT Edge 에이전트 및 IoT Edge 허브 이미지에는 연결된 IoT Edge 버전으로 태그가 지정됩니다. 런타임 이미지에 태그를 사용하는 방법에는 다음 두 가지가 있습니다.

* **롤링 태그** - 버전 번호의 처음 두 개 값만 사용하여 해당 숫자와 일치하는 최신 이미지를 가져옵니다. 예를 들어, 최신 1.0.x 버전을 가리키는 새 릴리스가 있을 때마다 1.0이 업데이트됩니다. IoT Edge 디바이스의 컨테이너 런타임이 이미지를 다시 끌어오면 런타임 모듈은 최신 버전으로 업데이트됩니다. 이 접근 방법은 개발 목적으로 제안됩니다. Azure Portal에서 배포할 때는 기본적으로 롤링 태그가 사용됩니다.

* **특정 태그** - 버전 번호의 세 값을 모두 사용하여 이미지 버전을 명시적으로 설정합니다. 예를 들어 1.0.7은 초기 릴리스 이후에 변경되지 않습니다. 업데이트할 준비가 되면 배포 매니페스트에서 새 버전 번호를 선언할 수 있습니다. 이 접근 방법은 프로덕션 목적으로 제안됩니다.

### <a name="update-a-rolling-tag-image"></a>롤링 태그 이미지 업데이트

배포에 롤링 태그를 사용하는 경우(예: mcr.microsoft.com/azureiotedge-hub: ** 1.0**) 디바이스의 컨테이너 런타임이 강제로 최신 버전의 이미지를 가져오도록 해야 합니다.

IoT Edge 디바이스에서 이미지의 로컬 버전을 삭제합니다. Windows 머신에서 보안 디먼을 제거하면 런타임 이미지도 제거되므로 이 단계를 다시 수행할 필요가 없습니다.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

이미지를 제거하기 위해 강제 `-f` 플래그를 사용해야 할 수 있습니다.

IoT Edge 서비스는 최신 버전의 런타임 이미지를 끌어오고 해당 이미지를 디바이스에서 자동으로 다시 시작합니다.

### <a name="update-a-specific-tag-image"></a>특정 태그 이미지 업데이트

배포에서 특정 태그(예: mcr.microsoft.com/azureiotedge-hub:**1.0.8)를**사용하는 경우 배포 매니페스트의 태그를 업데이트하고 변경 내용을 장치에 적용하기만 하면 됩니다.

1. Azure 포털의 IoT Hub에서 IoT Edge 장치를 선택하고 **모듈 설정을 선택합니다.**

1. **IoT 에지 모듈** 섹션에서 **런타임 설정을**선택합니다.

   ![런타임 설정 구성](./media/how-to-update-iot-edge/configure-runtime.png)

1. **런타임 설정에서**원하는 버전으로 **Edge Hub의** **이미지** 값을 업데이트합니다. 아직 **저장을** 선택하지 마십시오.

   ![에지 허브 이미지 버전 업데이트](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Edge **Hub** 설정을 축소하거나 아래로 스크롤한 다음 원하는 버전과 동일한 **Edge 에이전트의** **이미지** 값을 업데이트합니다.

   ![에지 허브 에이전트 버전 업데이트](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. **저장**을 선택합니다.

1. **검토 + 만들기,** 배포 검토 및 **만들기를**선택합니다.

## <a name="update-to-a-release-candidate-version"></a>릴리스 후보 버전으로 업데이트

Azure IoT Edge는 정기적으로 새로운 버전의 IoT Edge 서비스를 출시합니다. 각 안정 릴리스 전에 하나 이상의 RC(릴리스 후보) 버전이 있습니다. RC 버전에는 릴리스에 대한 계획된 모든 기능이 포함되어 있지만 여전히 테스트 및 유효성 검사를 거치고 있습니다. 새 기능을 조기에 테스트하려는 경우 RC 버전을 설치하고 GitHub를 통해 피드백을 제공할 수 있습니다.

릴리스 후보 버전은 릴리스의 동일한 번호 매기기 규칙을 따르지만 **-rc와** 끝에 증분 번호가 추가됩니다. [동일한 Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases) 목록에서 릴리스 후보를 안정 버전으로 볼 수 있습니다. 예를 들어 **1.0.7-rc1** 및 **1.0.7-rc2,** **1.0.7**이전에 나온 두 릴리스 후보를 찾습니다. RC 버전에 **시험판** 레이블이 표시되어 있음을 확인할 수도 있습니다.

IoT Edge 에이전트 및 허브 모듈에는 동일한 규칙으로 태그가 지정된 RC 버전이 있습니다. 예를 들어, **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**.

미리 보기로 릴리스 후보 버전은 일반 설치 관리자가 대상으로 하는 최신 버전으로 포함되지 않습니다. 대신 테스트하려는 RC 버전의 에셋을 수동으로 대상으로 지정해야 합니다. 대부분의 경우 RC 버전에 대한 설치 또는 업데이트는 Windows 장치에 대한 한 가지 차이점을 제외하고 다른 특정 버전의 IoT Edge를 대상으로 하는 것과 동일합니다. 

릴리스 후보에서 Windows 장치에서 IoT Edge 보안 데몬을 설치하고 관리할 수 있는 PowerShell 스크립트는 일반적으로 사용 가능한 최신 버전과 다른 기능을 가질 수 있습니다. RC용 IoT Edge .cab 파일을 다운로드하는 것 외에도 **IotEdgeSecurityDaemon.ps1** 스크립트를 다운로드할 수도 있습니다. [점 소싱을](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) 사용하여 현재 소스에서 다운로드한 스크립트를 실행합니다. 예를 들어: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

이 문서의 섹션을 사용하여 IoT Edge 장치를 특정 버전의 보안 데몬 또는 런타임 모듈로 업데이트하는 방법을 알아봅니다.

새 컴퓨터에 IoT Edge를 설치하는 경우 다음 링크를 사용하여 장치 운영 체제에 따라 특정 버전을 설치하는 방법을 알아봅니다.

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>다음 단계

최신 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 확인합니다.

[IoT(사물 인터넷) 블로그](https://azure.microsoft.com/blog/topics/internet-of-things/)에서 최신 업데이트 및 알림을 받아 보세요.
