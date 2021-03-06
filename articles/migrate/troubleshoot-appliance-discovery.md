---
title: Azure 마이그레이션 어플라이언스 배포 및 검색 문제 해결
description: Azure 마이그레이션 어플라이언스 배포 및 검색 컴퓨터 에 대한 도움말을 참조하세요.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 9fbf55fbe16d958bf10541894159dade26668bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336717"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Azure 마이그레이션 어플라이언스 및 검색 문제 해결

이 문서에서는 [Azure Migrate](migrate-services-overview.md) 어플라이언스를 배포하고 어플라이언스를 사용하여 온-프레미스 컴퓨터를 검색할 때 문제를 해결하는 데 도움이 됩니다.


## <a name="whats-supported"></a>지원되는 내용은 무엇입니까?

어플라이언스 지원 요구 사항을 [검토합니다.](migrate-appliance.md)


## <a name="invalid-ovf-manifest-entry"></a>"잘못된 OVF 매니페스트 항목"

"제공된 매니페스트 파일이 유효하지 않음: 잘못된 OVF 매니페스트 항목"이라는 오류가 발생하면 다음을 수행합니다.

1. Azure 마이그레이션 어플라이언스 OVA 파일이 해시 값을 확인하여 올바르게 다운로드되었는지 확인합니다. [자세히 알아봅니다](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). 해시 값이 일치하지 않으면 OVA 파일을 다시 다운로드하고 배포를 다시 시도하십시오.
2. 배포가 여전히 실패하고 VMware vSphere 클라이언트를 사용하여 OVF 파일을 배포하는 경우 vSphere 웹 클라이언트를 통해 배포해 보십시오. 배포가 여전히 실패하면 다른 웹 브라우저를 사용해 보십시오.
3. vSphere 웹 클라이언트를 사용하고 vCenter 서버 6.5 또는 6.7에 배포하려는 경우 ESXi 호스트에 직접 OVA를 배포해 보십시오.
   - 웹 클라이언트(https://<호스트 IP 주소>/ui)와 함께 직접(vCenter 서버 대신) ESXi *호스트에* 연결합니다.
   - **홈** > **인벤토리에서** **OVF** > **파일 배포 템플릿을**선택합니다. OVA를 찾아 배포를 완료합니다.
4. 배포가 여전히 실패하는 경우 Azure Migrate 지원에 문의합니다.

## <a name="cant-connect-to-the-internet"></a>인터넷에 연결할 수 없습니다.

어플라이언스 컴퓨터가 프록시 뒤에 있는 경우 이러한 일이 발생할 수 있습니다.

- 프록시에 필요한 경우 권한 부여 자격 증명을 제공해야 합니다.
- URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 허용 목록에 [이러한 URL을](migrate-appliance.md#url-access) 추가합니다.
- 가로채기 프록시를 사용하여 인터넷에 연결하는 경우 [다음 단계를](https://docs.microsoft.com/azure/migrate/concepts-collector)사용하여 프록시 인증서를 어플라이언스 VM으로 가져옵니다.

##  <a name="datetime-synchronization-error"></a>날짜/시간 동기화 오류

날짜 및 시간 동기화에 대한 오류(802)는 서버 클럭이 현재 시간과 동기화되지 않을 수 있음을 5분 이상 나타낸다. 현재 시간과 일치하도록 컬렉터 VM의 클럭 시간을 변경합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
2. 표준 시간대를 확인하려면 **w32tm /tz를**실행합니다.
3. 시간을 동기화하려면 **w32tm /재동기화를**실행합니다.


## <a name="unabletoconnecttoserver"></a>"수 없음ToConnectToServer"

이 연결 오류가 발생하면 vCenter Server Servername.com:9443에 연결하지 못할 수 있습니다. *Servername* 오류 세부 정보는 메시지를 수락할 수 `https://\*servername*.com:9443/sdk` 있는 끝점이 없음을 나타냅니다.

- 최신 버전의 어플라이언스를 실행하고 있는지 확인합니다. 그렇지 않은 경우 어플라이언스를 최신 [버전으로](https://docs.microsoft.com/azure/migrate/concepts-collector)업그레이드합니다.
- 최신 버전에서 여전히 문제가 발생하는 경우 어플라이언스가 지정된 vCenter Server 이름을 확인할 수 없거나 지정된 포트가 잘못되었을 수 있습니다. 기본적으로 포트를 지정하지 않으면 컬렉터가 포트 번호 443에 연결하려고 시도합니다.

    1. 어플라이언스에서 *Ping 서버 이름*.com.
    2. 1단계가 실패하면 IP 주소를 사용하여 vCenter 서버에 연결해 보십시오.
    3. vCenter 서버에 연결할 올바른 포트 번호를 식별합니다.
    4. vCenter 서버가 실행 되고 있는지 확인합니다.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>오류 60052/60039: 어플라이언스가 등록되지 않았을 수 있습니다.

- 오류 60052, "어플라이언스가 Azure 마이그레이션 프로젝트에 성공적으로 등록되지 않았을 수 있습니다" 어플라이언스를 등록하는 데 사용되는 Azure 계정에 사용 권한이 없는 경우 발생합니다.
    - 어플라이언스를 등록하는 데 사용되는 Azure 사용자 계정에 구독에 대한 기여자 권한이 적어도 있는지 확인합니다.
    - 필수 Azure 역할 및 사용 권한에 대해 [자세히 알아보세요.](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)
- 오류 60039, "어플라이언스가 Azure 마이그레이션 프로젝트에 성공적으로 등록되지 않을 수 있습니다" 등록에 사용되는 Azure Migrate 프로젝트를 찾을 수 없기 때문에 등록에 실패하면 발생할 수 있습니다.
    - Azure 포털에서 리소스 그룹에 프로젝트가 있는지 확인합니다.
    - 프로젝트가 없는 경우 리소스 그룹에 새 Azure Migrate 프로젝트를 만들고 어플라이언스를 다시 등록합니다. 새 프로젝트를 만드는 [방법에 대해 알아봅니다.](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)

## <a name="error-6003060031-key-vault-management-operation-failed"></a>오류 60030/60031: 키 볼트 관리 작업 실패

"Azure 키 볼트 관리 작업이 실패했습니다"라는 오류가 발생하면 다음을 수행합니다.
- 어플라이언스를 등록하는 데 사용되는 Azure 사용자 계정에 구독에 대한 기여자 권한이 적어도 있는지 확인합니다.
- 계정이 오류 메시지에 지정된 키 자격 증명 모음에 액세스할 수 있는지 확인한 다음 작업을 다시 시도합니다.
- 문제가 지속되면 Microsoft 지원에 문의하세요.
- 필요한 Azure 역할 및 사용 권한에 대해 [자세히 알아보세요.](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)

## <a name="error-60028-discovery-couldnt-be-initiated"></a>오류 60028: 검색을 시작할 수 없습니다.

오류 60028: "오류로 인해 검색을 시작할 수 없습니다. 지정된 호스트 또는 클러스터 목록에 대해 작업이 실패했음을 나타냅니다." VM 정보에 액세스하거나 검색하는 데 문제가 있어 오류에 나열된 호스트에서 검색을 시작할 수 없습니다. 나머지 호스트가 성공적으로 추가되었습니다.

- 호스트 추가 옵션을 사용하여 오류에 나열된 호스트를 다시 **추가합니다.**
- 유효성 검사 오류가 있는 경우 수정 지침에서 오류를 수정한 다음 **저장 및 검색 옵션을** 다시 시작해 보십시오.

## <a name="error-60025-azure-ad-operation-failed"></a>오류 60025: Azure AD 작업이 실패했습니다. 
오류 60025: "Azure AD 작업이 실패했습니다. Azure AD 응용 프로그램을 만들거나 업데이트하는 동안 오류가 발생합니다" 검색을 시작하는 데 사용되는 Azure 사용자 계정이 어플라이언스를 등록하는 데 사용되는 계정과 다를 때 발생합니다. 다음 중 하나를 수행합니다.

- 검색을 처음에 사용하는 사용자 계정이 어플라이언스를 등록하는 데 사용된 계정과 동일한지 확인합니다.
- 검색 작업이 실패한 사용자 계정에 Azure Active Directory 응용 프로그램 액세스 권한을 제공합니다.
- Azure 마이그레이션 프로젝트에 대해 이전에 만든 리소스 그룹을 삭제합니다. 다시 시작할 다른 리소스 그룹을 만듭니다.
- Azure Active Directory 응용 프로그램 사용 권한에 대해 [자세히 알아보세요.](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>오류 50004: 호스트 또는 클러스터에 연결할 수 없습니다.

오류 50004: "서버 이름을 확인할 수 없으므로 호스트 또는 클러스터에 연결할 수 없습니다. WinRM 오류 코드: 0x803381B9" 어플라이언스에 대 한 Azure DNS 서비스 제공 한 클러스터 또는 호스트 이름을 확인할 수 없는 경우 발생할 수 있습니다.

- 클러스터에 이 오류가 표시되면 FQDN 클러스터가 있습니다.
- 클러스터의 호스트에 대해이 오류가 표시 될 수도 있습니다. 이는 어플라이언스가 클러스터에 연결할 수 있지만 클러스터는 FQDN이 아닌 호스트 이름을 반환합니다. 이 오류를 해결하려면 IP 주소 및 호스트 이름의 매핑을 추가하여 어플라이언스의 호스트 파일을 업데이트합니다.
    1. 관리자로 메모장을 엽니다.
    2. C:\Windows\System32\드라이버\etc\호스트 파일을 엽니다.
    3. IP 주소와 호스트 이름을 행에 추가합니다. 이 오류가 표시되는 각 호스트 또는 클러스터에 대해 반복합니다.
    4. 변경 내용을 저장하고 호스트 파일을 닫습니다.
    5. 어플라이언스 관리 앱을 사용하여 어플라이언스가 호스트에 연결할 수 있는지 확인합니다. 30분 후 Azure 포털에서 이러한 호스트에 대한 최신 정보가 표시됩니다.

## <a name="discovered-vms-not-in-portal"></a>포털에 없는 검색된 VM

검색 상태가 "검색 진행 중"이지만 포털에 VM이 아직 표시되지 않는 경우 몇 분 동안 기다립니다.
- VMware VM에 약 15분이 소요됩니다.
- 하이퍼-V VM 검색을 위해 추가된 각 호스트에는 약 2분이 걸립니다.

기다렸다가 상태가 변경되지 않으면 서버 탭에서 **새로 고침을** **선택합니다.** 이렇게 하면 Azure 마이그레이션: 서버 평가 및 Azure 마이그레이션: 서버 마이그레이션에서 검색된 서버의 수가 표시됩니다.

이것이 작동하지 않고 VMware 서버를 발견하는 경우 :

- 지정한 vCenter 계정에 하나 이상의 VM에 대한 액세스 권한이 올바르게 설정된 권한이 있는지 확인합니다.
- vCenter 계정에 vCenter VM 폴더 수준에서 액세스 권한이 부여된 경우 Azure 마이그레이션은 VMware VM을 검색할 수 없습니다. 범위 지정 검색에 대해 [자세히 알아보세요.](set-discovery-scope.md)

## <a name="vm-data-not-in-portal"></a>포털에 없는 VM 데이터

검색된 VM이 포털에 나타나지 않거나 VM 데이터가 오래된 경우 몇 분 정도 기다립니다. 검색된 VM 구성 데이터의 변경 내용이 포털에 표시되려면 최대 30분이 걸립니다. 응용 프로그램 데이터의 변경 내용이 표시되려면 몇 시간이 걸릴 수 있습니다. 이 시간 이후에 데이터가 없는 경우 다음과 같이 새로 고쳐 보십시오.

1. **서버** > **Azure에서 서버 평가를 마이그레이션하고** **개요를 선택합니다.**
2. **관리**에서 **에이전트 상태를 선택합니다.**
3. **에이전트 새로 고침을**선택합니다.
4. 새로 고침 작업이 완료될 때까지 기다립니다. 이제 최신 정보가 표시됩니다.

## <a name="deleted-vms-appear-in-portal"></a>삭제된 VM이 포털에 나타납니다.

VM을 삭제해도 포털에 계속 표시되는 경우 30분 동안 기다립니다. 그래도 나타나면 위에서 설명한 대로 새로 고칩니다.

## <a name="common-app-discovery-errors"></a>일반적인 앱 검색 오류

Azure Migrate는 Azure 마이그레이션: 서버 평가를 사용하여 응용 프로그램, 역할 및 기능 검색을 지원합니다. 앱 검색은 현재 VMware에서만 지원됩니다. 앱 검색을 설정하기 위한 요구 사항 및 단계에 대해 [자세히 알아보세요.](how-to-discover-applications.md)

일반적인 앱 검색 오류는 표에 요약되어 있습니다. 

**오류** | **원인** | **작업**
--- | --- | --- | ---
10000: "서버에 설치된 응용 프로그램을 검색할 수 없습니다.". | 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우 이 이 가 발생할 수 있습니다. | Windows/Linux용 앱 검색만 사용합니다.
10001: "서버를 설치한 응용 프로그램을 검색할 수 없습니다.". | 내부 오류 - 어플라이언스의 일부 누락된 파일입니다. | Microsoft 지원에 문의하세요.
10002: "서버를 설치한 응용 프로그램을 검색할 수 없습니다.". | 어플라이언스의 검색 에이전트가 제대로 작동하지 않을 수 있습니다. | 24시간 이내에 문제가 해결되지 않으면 지원팀에 문의하세요.
10003 "서버를 설치 한 응용 프로그램을 검색 할 수 없습니다". | 어플라이언스의 검색 에이전트가 제대로 작동하지 않을 수 있습니다. | 24시간 이내에 문제가 해결되지 않으면 지원팀에 문의하세요.
10004: "<Windows/Linux> 컴퓨터에 대 한 설치 된 응용 프로그램을 검색할 수 없습니다." |  Windows/Linux> 컴퓨터에 액세스할 <대한 자격 증명이 어플라이언스에 제공되지 않았습니다.| <Windows/Linux> 컴퓨터에 액세스할 수 있는 어플라이언스에 자격 증명을 추가합니다.
10005: "온-프레미스 서버에 액세스할 수 없습니다.". | 액세스 자격 증명이 잘못되었을 수 있습니다. | 어플라이언스 자격 증명을 업데이트하면 관련 컴퓨터에 액세스할 수 있습니다. 
10006: "온-프레미스 서버에 액세스할 수 없습니다.". | 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우 이 이 가 발생할 수 있습니다.|  Windows/Linux용 앱 검색만 사용합니다.
10007: "검색된 메타데이터를 처리할 수 없음" | 이 내부 오류는 JSON을 역직렬화하는 동안 발생했습니다. | 해결 을 위해 Microsoft 지원에 문의
9000/9001/9002: "서버에 설치된 응용 프로그램을 검색할 수 없습니다." | VMware 도구가 설치되지 않았거나 손상되었을 수 있습니다. | 관련 컴퓨터에 VMware 도구를 설치/다시 설치하고 실행 중인지 확인합니다.
9003: 서버에 설치된 응용 프로그램을 검색할 수 없습니다." | 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우 이 이 가 발생할 수 있습니다. | Windows/Linux용 앱 검색만 사용합니다.
9004: "서버에 설치된 응용 프로그램을 검색할 수 없습니다.". | VM의 전원이 꺼져 있는 경우 이러한 일이 발생할 수 있습니다. | 검색을 위해 VM이 켜졌는지 확인합니다.
9005: "VM에 설치된 응용 프로그램을 검색할 수 없습니다. | 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우 이 이 가 발생할 수 있습니다. | Windows/Linux용 앱 검색만 사용합니다.
9006/9007: "서버를 설치한 응용 프로그램을 검색할 수 없습니다." | 어플라이언스의 검색 에이전트가 제대로 작동하지 않을 수 있습니다. | 24시간 이내에 문제가 해결되지 않으면 지원팀에 문의하세요.
9008: "서버를 설치한 응용 프로그램을 검색할 수 없습니다.". | 내부 오류일 수 있습니다.  | Tf 문제는 24 시간 이내에 자체적으로 해결되지 않으며 지원팀에 문의하십시오.
9009: "서버를 설치한 응용 프로그램을 검색할 수 없습니다.". | 서버의 UAC(Windows 사용자 계정 제어) 설정이 제한적인 경우 발생할 수 있으며 설치된 응용 프로그램의 검색을 방지할 수 있습니다. | 서버에서 '사용자 계정 제어' 설정을 검색하고 서버의 UAC 설정을 하위 두 수준 중 하나로 구성합니다.
9010: "서버를 설치한 응용 프로그램을 검색할 수 없습니다.". | 내부 오류일 수 있습니다.  | Tf 문제는 24 시간 이내에 자체적으로 해결되지 않으며 지원팀에 문의하십시오.
9011: "게스트에서 다운로드할 파일이 게스트 VM에서 찾을 수 없습니다." | 내부 오류로 인해 문제가 발생할 수 있습니다. | 이 문제는 24시간 안에 자동으로 해결됩니다. 그래도 문제가 지속되면 Microsoft 지원에 문의하십시오.
9012: "결과 파일 내용이 비어 있습니다." | 내부 오류로 인해 문제가 발생할 수 있습니다. | 이 문제는 24시간 안에 자동으로 해결됩니다. 그래도 문제가 지속되면 Microsoft 지원에 문의하십시오.
9013: "VMware VM에 로그인할 때마다 새 임시 프로필이 만들어집니다." | VM에 로그인할 때마다 새 임시 프로필이 만들어집니다. | 게스트 VM 자격 증명에 제공된 사용자 이름이 UPN 형식으로 되어 있는지 확인합니다.
9015: "vCenter에서 권한 부족으로 인해 VMware VM에 연결할 수 없습니다." | 게스트 운영 역할은 vCenter 사용자 계정에서 사용할 수 없습니다. | vCenter 사용자 계정에서 게스트 운영 역할이 활성화되어 있는지 확인합니다.
9016: "게스트 운영 에이전트가 데이터가 부족하여 VMware VM에 연결할 수 없습니다." | VMware 도구가 제대로 설치되지 않았거나 최신 상태로 설정되지 않았습니다. | VMware 도구가 제대로 설치되고 최신 상태로 유지되었는지 확인합니다.
9017: "검색된 메타데이터가 있는 파일이 VM에서 찾을 수 없습니다." | 내부 오류로 인해 문제가 발생할 수 있습니다. | 해결 을 위해 Microsoft 지원에 문의 하십시오.
9018: "PowerShell이 게스트 VM에 설치되지 않았습니다." | 게스트 VM에서는 PowerShell을 사용할 수 없습니다. | 게스트 VM에 PowerShell을 설치합니다.
9019: "게스트 VM 작업 오류로 인해 검색할 수 없습니다." | VM웨어 게스트 작업이 VM에서 실패했습니다. | 게스트 VM 자격 증명에 제공된 VM 자격 증명이 유효한지 확인하고 사용자 이름이 UPN 형식으로 되어 있는지 확인합니다.
9020: "파일 생성 권한이 거부되었습니다." | 사용자 또는 그룹 정책과 연결된 역할이 사용자가 폴더에 파일을 만들도록 제한하고 있습니다. | 게스트 사용자가 제공한 폴더의 파일에 대한 생성 권한이 있는지 확인합니다. 폴더 이름에 대한 서버 평가의 **알림을** 참조하십시오.
9021: "파일 만들기 권한 폴더 시스템 임시 경로에서 거부 됩니다." | VM의 VM웨어 도구 버전은 지원되지 않습니다. | VMware 도구 버전을 10.2.0 이상으로 업그레이드합니다.
9022: "WMI 개체 액세스가 거부됩니다." | 사용자 또는 그룹 정책과 관련된 역할은 사용자가 WMI 개체에 액세스하도록 제한하고 있습니다. | Microsoft 지원에 문의하세요.
9023: "SystemRoot 환경 변수 값이 비어 있습니다." | 알 수 없습니다. | Microsoft 지원에 문의하세요.
9024: "TEMP 환경 변수 값이 비어 있습니다." | 알 수 없습니다. | Microsoft 지원에 문의하세요.
9025: "게스트 VM에서 PowerShell이 손상되었습니다." | 알 수 없습니다. | 게스트 VM에서 PowerShell을 다시 설치하고 게스트 VM에서 PowerShell을 실행할 수 있는지 확인합니다.
8084: "VMware 오류로 인해 응용 <Exception from VMware>프로그램을 검색할 수 없습니다. " | Azure 마이그레이션 어플라이언스는 VMware API를 사용하여 응용 프로그램을 검색합니다. 이 문제는 응용 프로그램을 검색하는 동안 vCenter Server에서 예외를 throw하는 경우 발생할 수 있습니다. VMware의 오류 메시지가 포털에 표시된 오류 메시지에 표시됩니다. | [VMware 설명서에서](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)메시지를 검색하고 수정단계를 따릅니다. 수정할 수 없는 경우 Microsoft 지원에 문의하십시오.



## <a name="next-steps"></a>다음 단계
[VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)또는 물리적 서버에 대한 어플라이언스를 [설정합니다.](how-to-set-up-appliance-physical.md)
