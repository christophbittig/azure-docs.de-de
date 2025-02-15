---
title: Bereitstellung von SAP MaxDB, SAP liveCache und SAP Content Server auf Azure-VMs | Microsoft-Dokumentation
description: Bereitstellung von SAP MaxDB, SAP liveCache und SAP Content Server in Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63bfe178f22ed4e405fe70f0a9b1bbabb780af40
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284885"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Bereitstellung von SAP MaxDB, SAP liveCache und SAP Content Server auf Azure-VMs

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Dieses Dokument behandelt verschiedene wichtige Themen für die Bereitstellung von MaxDB, liveCache und Content Server in Azure IaaS. Es ist sinnvoll, im Vorfeld das Dokument [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) und andere Artikel der [Azure-Dokumentation für SAP-Workload](./get-started.md) zu lesen. 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Besonderheiten von SAP MaxDB-Bereitstellungen unter Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Versionsunterstützung für SAP MaxDB in Azure
SAP unterstützt derzeit SAP MaxDB Version 7.9 und höher für die Verwendung mit SAP NetWeaver-basierten Produkten in Azure. Jegliche Updates für SAP MaxDB-Server sowie JDBC- und ODBC-Treiber für die Verwendung mit SAP NetWeaver-basierten Produkten werden ausschließlich über den SAP Service Marketplace unter <https://support.sap.com/swdc> bereitgestellt.
Allgemeine Informationen zur gemeinsamen Verwendung von SAP NetWeaver und SAP MaxDB erhalten Sie unter <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Unterstützte Microsoft Windows-Versionen und Azure-VM-Typen für SAP MaxDB-DBMS
Die unterstützten Microsoft Windows-Versionen für SAP MaxDB-DBMS auf Azure finden Sie unter:

* [SAP-Produktverfügbarkeitsmatrix (PAM)][sap-pam]
* SAP-Hinweis [1928533]

Es wird ausdrücklich empfohlen, stets die neueste Version des Betriebssystems Microsoft Windows zu verwenden. Derzeit ist dies Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Verfügbare SAP MaxDB-Dokumentation
Die aktualisierte Liste zur SAP MaxDB-Dokumentation finden Sie in SAP-Hinweis [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP MaxDB-Konfigurationsrichtlinien für SAP-Installationen in Azure-VMs
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Speicherkonfiguration
Die Best Practices für Azure Storage mit SAP MaxDB orientieren sich an den allgemeinen Empfehlungen, die im Kapitel [Speicherstruktur einer VM für RDBMS-Bereitstellungen](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64) beschrieben werden.

> [!IMPORTANT]
> Wie andere Datenbanken verfügt auch SAP MaxDB über Daten- und Protokolldateien. Die korrekte Terminologie bei SAP MaxDB lautet allerdings „Volume“ (nicht „Datei“). Bei SAP MaxDB sprechen wir also z.B. von Datenvolumes und Protokollvolumes. Diese sind nicht zu verwechseln mit den Datenträgervolumes des Betriebssystems. 
> 
> 

Folgende Schritte müssen ausgeführt werden:

* Wenn Sie Azure-Speicherkonten verwenden, legen Sie das Azure-Speicherkonto mit den SAP MaxDB-Datenvolumes und -Protokollvolumes (d. h. Daten- und Protokolldateien) als **lokal redundanten Speicher** (LRS) fest. Eine Anleitung dafür finden Sie unter [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md).
* Trennen Sie den E/A-Pfad für die SAP MaxDB-Datenvolumes (Datendateien) vom E/A-Pfad für Protokollvolumes (Protokolldateien). Das bedeutet: SAP MaxDB-Datenvolumes (Datendateien) müssen auf einem logischen Laufwerk installiert werden und SAP MaxDB-Protokollvolumes (Protokolldateien) auf einem anderen logischen Laufwerk.
* Legen Sie, wie in Artikel [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) beschrieben, für jeden Datenträger den entsprechenden Cachetyp fest, je nachdem, ob Sie SAP MaxDB-Daten- oder -Protokollvolumes (d. h. Daten- und Protokolldateien) sowie den Azure-Standardspeicher oder Azure Storage Premium verwenden.
* Solange die bestehenden IOPS-Kontingente pro Datenträger den Anforderungen genügen, ist es möglich, alle Datenvolumes auf einem einzigen, bereitgestellten Datenträger und alle Protokollvolumes der Datenbank auf einem anderen einzelnen, bereitgestellten Datenträger zu speichern.
* Wenn mehr IOPS und/oder Speicherplatz erforderlich sind, wird empfohlen, Microsoft Windows-Speicherpools (nur verfügbar unter Microsoft Windows Server 2012 und höher) zu verwenden, um ein einziges großes, logisches Gerät aus mehreren bereitgestellten Datenträgern zu erstellen. Weitere Informationen finden Sie unter [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). Durch diese Herangehensweise wird der Aufwand verringert, der zur Verwaltung des Speicherplatzes notwendig ist. Außerdem müssen Dateien nicht mehr manuell auf mehrere bereitgestellte Datenträger verteilt werden.
* Es wird dringend empfohlen, Azure Storage Premium für MaxDB-Bereitstellungen zu verwenden. 

![Referenzkonfiguration der Azure-IaaS-VM für SAP MaxDB-DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Sichern und Wiederherstellen
Bei der Bereitstellung von SAP MaxDB in Azure ist es erforderlich, die Sicherungsmethode zu überprüfen. Auch wenn es sich bei dem System nicht um ein Produktionssystem handelt, muss die durch SAP MaxDB gehostete SAP-Datenbank regelmäßig gesichert werden. Da Azure Storage drei Images vorhält, ist die Sicherung, um einen etwaigen Speicherabsturz und vor allem Betriebsausfälle und Verwaltungsfehler kompensieren zu können, weniger dringlich. Der wichtigste Grund, einen ordnungsgemäßen Sicherungs- und Wiederherstellungsplan zu verfolgen, besteht darin, dass Sie mithilfe der Point-in-Time-Wiederherstellung logische und manuelle Fehler beheben können. Das Ziel ist also entweder, Sicherungen für die Wiederherstellung des Zustands der Datenbank zu einem bestimmten Zeitpunkt zu erstellen, oder, mithilfe einer Sicherung in Azure durch Seeding ein anderes System aufzusetzen, indem die bestehende Datenbank kopiert wird. 

Sicherung und Wiederherstellung einer Datenbank in Azure funktionieren wie bei lokalen Umgebungen. Sie können also die standardmäßigen Tools in SAP MaxDB zum Sichern und Wiederherstellen verwenden. Diese werden in einer SAP MaxDB-Dokumentation beschrieben, die im SAP-Hinweis [767598] aufgeführt wird. 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Leistungsüberlegungen hinsichtlich Sicherung und Wiederherstellung
Wie bei Bare-Metal-Bereitstellungen hängt die Leistung bei der Sicherung und Wiederherstellung davon ab, wie viele Volumes parallel gelesen werden können und wie hoch deren Durchsatz ist. Es gilt also Folgendes:

* Je geringer die Anzahl an Datenträgern, auf denen Datenbankgeräte gespeichert sind, desto geringer der Durchsatz beim Auslesen insgesamt.
* Je weniger Ziele (Stripesetverzeichnisse, Datenträger), in bzw. auf die die Sicherung geschrieben wird, desto geringer der Durchsatz.

Wenn Sie die Anzahl der Ziele erhöhen möchten, stehen Ihnen je nach Anforderungen zwei Optionen zur Verfügung, die Sie verwenden bzw. kombinieren können:

* Getrennte, dedizierte Volumes für das Sichern
* Striping des Zielvolumes der Sicherung auf mehrere bereitgestellte Datenträger, wodurch der IOPS-Durchsatz auf den betreffenden Datenträgervolumes verbessert wird
* Abgegrenzte, dedizierte logische Datenträger für:
  * SAP MaxDB-Sicherungsvolumes (also Dateien)
  * SAP MaxDB-Datenvolumes (d.h. Dateien)
  * SAP MaxDB-Protokollvolumes (d.h. Dateien)

Weitere Informationen zum Striping von Volumes über mehrere bereitgestellte Datenträger finden Sie in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Weitere Überlegungen
Alle anderen allgemeinen Themen wie Azure-Verfügbarkeitsgruppen oder SAP-Überwachung (siehe [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md))  gelten ebenfalls für die Bereitstellung von VMs mit der SAP MaxDB-Datenbank.
Andere SAP MaxDB-spezifische Einstellungen sind für Azure-VMs transparent. Diese sind in verschiedenen Dokumenten beschrieben, die in SAP-Hinweis [767598] und den folgenden SAP-Hinweisen aufgelistet sind:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Besonderheiten von SAP liveCache-Bereitstellungen unter Windows
### <a name="sap-livecache-version-support"></a>Versionsunterstützung für SAP liveCache
SAP liveCache wird in Azure Virtual Machines ab Version **SAP LC/LCAPPS 10.0 SP 25** unterstützt. Dies schließt **liveCache 7.9.08.31** und **LCA-Build 25** ein, veröffentlicht für **EhP 2 für SAP SCM 7.0** und höher.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Unterstützte Microsoft Windows-Versionen und Azure-VM-Typen für SAP liveCache-DBMS
Die unterstützten Microsoft Windows-Versionen für SAP liveCache auf Azure finden Sie unter:

* [SAP-Produktverfügbarkeitsmatrix (PAM)][sap-pam]
* SAP-Hinweis [1928533]

Es wird ausdrücklich empfohlen, stets die neueste Version des Betriebssystems Microsoft Windows Server zu verwenden. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache-Konfigurationsrichtlinien für SAP-Installationen in Azure-VMs
#### <a name="recommended-azure-vm-types-for-livecache"></a>Empfohlene Azure-VM-Typen für liveCache
Da es sich bei SAP liveCache um eine Anwendung handelt, die für umfangreiche Berechnungen vorgesehen ist, wird die Leistung von SAP liveCache maßgeblich durch die Geschwindigkeit von RAM und CPU bestimmt. 

Bei den von SAP unterstützten Typen der Azure-VM (siehe SAP-Hinweis [1928533]) werden alle virtuellen CPU-Ressourcen von dedizierten physischen CPU-Ressourcen des Hypervisors gestützt. Eine Überversorgung (und damit eine Konkurrenz um CPU-Ressourcen) findet nicht statt.

Analog wird bei den von SAP unterstützten Typen der Azure-VM-Instanzen der gesamte VM-Speicher dem physischen Speicher zugeordnet. Es erfolgt z. B. keine Überbereitstellung (Over-Commitment).

Daher wird dringend empfohlen, die neuesten VMs der Dv2-, Dv3-, Ev3- und M-Serie zu verwenden. Die Auswahl der verschiedenen VM-Typen, hängt vom Speicherbedarf für liveCache und den benötigten CPU-Ressourcen ab. Wie bei allen anderen DBMS-Bereitstellungen ist es ratsam, Azure Storage Premium für leistungskritische Volumes einzusetzen.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Speicherkonfiguration für liveCache in Azure
Da SAP liveCache auf SAP MaxDB-Technologie basiert, gelten alle Best Practices für Azure Storage, die in diesem Artikel für SAP MaxDB erläutert wurden, auch für SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dediziertes Szenario: Azure-VM für liveCache
Da SAP liveCache viel an Rechenleistung erfordert, wird im Sinne des produktiven Nutzens nachdrücklich empfohlen, einen dedizierten, virtuellen Azure-Computer bereitzustellen. 

![Dedizierte Azure-VM für liveCache im Anwendungsfall für produktiven Nutzen](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Sichern und Wiederherstellen für liveCache in Azure
Sicherung und Wiederherstellung, einschließlich Überlegungen zur Leistung, wurden bereits in den entsprechenden Kapiteln zu SAP MaxDB beschrieben. 

#### <a name="other-considerations"></a>Weitere Überlegungen
Alle anderen allgemeinen Aspekte wurden im Kapitel zu SAP MaxDB beschrieben. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Besonderheiten der SAP Content Server-Bereitstellung unter Windows in Azure
Der SAP Content Server ist eine separate, serverbasierte Komponente zum Speichern von Inhalt wie elektronischen Dokumenten in verschiedenen Formaten. Der SAP Content Server ist eine technologische Errungenschaft und kann für alle SAP-Anwendungen verwendet werden. Er wird auf einem gesonderten System installiert. Typischer Inhalt sind z.B. Schulungsmaterialien und Dokumentationen des Knowledge Warehouse oder technische Zeichnungen aus dem mySAP PLM Document Management System. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Versionsunterstützung für SAP Content Server auf Azure-VMs
Momentan unterstützt SAP:

* **SAP Content Server** mit Version **6.50 (und höher)**
* **SAP MaxDB Version 7.9**
* **Microsoft IIS (Internet Information Server) Version 8.0 (und höher)**

Es wird nachdrücklich empfohlen, stets die neueste Version von SAP Content Server und von **Microsoft IIS** zu verwenden. 

Die neuesten unterstützten Versionen von SAP Content Server und Microsoft IIS sind in der [SAP-Produktverfügbarkeitsmatrix (Product Availability Matrix, PAM)][sap-pam] aufgelistet.

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Unterstützte Microsoft Windows-Versionen und Azure-VM-Typen für SAP Content Server
Die für den SAP Content Server auf Azure unterstützten Windows-Versionen finden Sie hier:

* [SAP-Produktverfügbarkeitsmatrix (PAM)][sap-pam]
* SAP-Hinweis [1928533]

Es wird ausdrücklich empfohlen, stets die neueste Version von Microsoft Windows Server zu verwenden.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP Content Server-Konfigurationsrichtlinien für SAP-Installationen in Azure-VMs
#### <a name="storage-configuration-for-content-server-in-azure"></a>Speicherkonfiguration für Content Server in Azure
Wenn Sie SAP Content Server so konfigurieren, dass Dateien in der SAP MaxDB-Datenbank gespeichert werden, gelten alle Best Practices für Azure Storage, die in diesem Artikel für SAP MaxDB beschrieben wurden, auch für SAP Content Server. 

Konfigurieren Sie SAP Content Server so, dass Dateien im Dateisystem gespeichert werden, empfiehlt es sich, ein dediziertes logisches Laufwerk zu verwenden. Wenn Sie Windows-Speicherplätze nutzen, können Sie auch die logische Datenträgergröße und den IOPS-Durchsatz erhöhen, wie im Artikel [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) beschrieben. 

#### <a name="sap-content-server-location"></a>Speicherort für den SAP Content Server
SAP Content Server muss in derselben Azure-Region und im selben Azure-VNET wie das SAP-System bereitgestellt werden. Sie haben die Möglichkeit, die SAP Content Server-Komponenten auf einer dedizierten Azure-VM oder auf derselben VM wie das SAP-System zu installieren. 

![Dedizierte Azure-VM für SAP Content Server](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>Speicherort für den SAP Cache Server
Der SAP Cache Server ist eine zusätzliche, serverbasierte Komponente für den lokalen Zugriff auf (zwischengespeicherte) Dokumente. Der SAP Cache Server speichert die Dokumente auf einem SAP Content Server zwischen. Dies geschieht, um für den Fall, dass Dokumente mehr als einmal von verschiedenen Speicherorten abgerufen werden müssen, den Netzwerkdatenverkehr zu optimieren. Allgemein gilt, dass sich der SAP Cache Server in physischer Nähe zu dem Client befinden muss, der auf den SAP Cache Server zugreift. 

Hier haben Sie zwei Möglichkeiten:

1. **Client ist ein Back-End-SAP-System** Falls ein Back-End-SAP-System für den Zugriff auf den SAP Content Server konfiguriert wurde, ist dieses SAP-System ein Client. Da sowohl das SAP-System als auch der SAP Content Server im selben Datencenter einer Azure-Region bereitgestellt werden, befinden sie sich in physischer Nähe zueinander. Es besteht also keine Veranlassung für einen dedizierten SAP Cache Server. SAP UI-Clients (SAP-GUI oder Webbrowser) haben direkten Zugriff auf das SAP-System, während das SAP-System Dokumente vom SAP Content Server abruft.
2. **Client ist ein lokaler Webbrowser** Der SAP Content Server lässt sich so konfigurieren, dass der Zugriff direkt über den Webbrowser erfolgen kann. In diesem Fall ist ein lokal ausgeführter Webbrowser ein Client des SAP Content Servers. Das lokale Datencenter und das Azure-Datencenter befinden sich an unterschiedlichen physischen Standorten (idealerweise nicht weit voneinander entfernt). Ihr lokales Datencenter ist per Azure-Site-to-Site-VPN oder ExpressRoute mit Azure verbunden. Beide Optionen bieten eine sichere VPN-Netzwerkverbindung mit Azure. Die Site-to-Site-Verbindung verfügt allerdings nicht über Netzwerkbandbreite und Latenz-SLA zwischen dem lokalen Datencenter und dem Azure-Datencenter. Um den Zugriff auf Dokumente zu beschleunigen, können Sie eine der folgenden Maßnahmen ergreifen:
   1. Lokale Installation des SAP Cache Servers in der Nähe zum lokalen Webbrowser (Option im Bild unten)
   2. Konfigurieren von Azure ExpressRoute, das hohe Geschwindigkeit und niedrige Wartezeit mit einer dedizierten Netzwerkverbindung zwischen dem lokalen Datencenter und dem Azure-Datencenter bietet

![Option zur lokalen Installation des SAP Cache Servers](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Sicherung/Wiederherstellung
Wenn Sie den SAP Content Server für das Speichern von Dateien in der SAP MaxDB-Datenbank konfigurieren, gelten die Angaben zum Sichern und Wiederherstellen sowie die Leistungsüberlegungen, die in den SAP MaxDB-Abschnitten dieses Dokuments beschrieben wurden. 

Konfigurieren Sie den SAP Content Server so, dass Dateien im Dateisystem gespeichert werden, haben Sie die Option, das Sichern und Wiederherstellen der gesamten Dateistruktur am Speicherort der Dokumente auszuführen. Ähnlich dem Sichern und Wiederherstellen bei SAP MaxDB wird empfohlen, für Sicherungszwecke ein dediziertes Datenträgervolume zur Verfügung zu halten. 

#### <a name="other"></a>Andere
Andere, für den SAP Content Server spezifische Einstellungen sind für Azure-VMs transparent. Sie werden in verschiedenen Dokumenten und SAP-Hinweisen beschrieben:

* <https://service.sap.com/contentserver> 
* SAP-Hinweis [1619726]  
