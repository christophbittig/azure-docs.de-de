---
title: Lösungsarchitekturen mit Azure NetApp Files | Microsoft-Dokumentation
description: Bietet Verweise auf bewährte Methoden für Lösungsarchitekturen unter Verwendung von Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/4/2021
ms.author: b-juche
ms.openlocfilehash: a69dd2528b4804e434cf6652d1ce2b03357025ee
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848608"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Lösungsarchitekturen mit Azure NetApp Files
Dieser Artikel enthält Verweise auf bewährte Methoden, die Ihnen helfen können, die Lösungsarchitekturen für die Verwendung von Azure NetApp Files zu verstehen.  

Das folgende Diagramm zeigt die Kategorien der Lösungsarchitekturen, die Azure NetApp Files bietet:

![Kategorien der Lösungsarchitekturen](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Lösungen für Linux OSS-Apps und Datenbanken

In diesem Abschnitt finden Sie Referenzen zu Lösungen für Linux OSS-Anwendungen und Datenbanken. 

### <a name="linux-oss-apps"></a>Linux OSS-Apps

* [Migration einer lokalen AIX UNIX-Instanz zu Azure Linux – Azure-Beispielszenarien](/azure/architecture/example-scenario/unix-migration/migrate-aix-azure-linux)

### <a name="mainframe-refactor"></a>Mainframe-Umgestaltung

* [Allgemeine Mainframe-Umgestaltung in Azure – Azure-Beispielszenarien](/azure/architecture/example-scenario/mainframe/general-mainframe-refactor)
* [Umgestalten von Mainframeanwendungen mit Erweitert – Azure-Beispielszenarien](/azure/architecture/example-scenario/mainframe/refactor-mainframe-applications-advanced)

### <a name="oracle"></a>Oracle

* [Oracle Database mit Azure NetApp Files – Azure-Beispielszenarien](/azure/architecture/example-scenario/file-storage/oracle-azure-netapp-files)
* [Oracle Databases on Microsoft Azure Using Azure NetApp Files](https://www.netapp.com/media/17105-tr4780.pdf) (Oracle-Datenbanken in Microsoft Azure unter Verwendung von Azure NetApp Files)
* [Oracle VM-Images und deren Bereitstellung in Microsoft Azure: Konfigurationsoptionen für freigegebenen Speicher](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Leistung von Oracle-Datenbanken auf einzelnen Azure NetApp Files-Volumes](performance-oracle-single-volumes.md)
* [Vorteile der Verwendung von Azure NetApp Files mit Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)
*   [Verteiltes Training in Azure: Lane-Erkennung – Lösungsentwurf](https://www.netapp.com/media/32427-tr-4896-design.pdf)
*   [Verteiltes Training in Azure: Vorhersage der Durchklickrate (Click-Through)– Lösungsentwurf](https://docs.netapp.com/us-en/netapp-solutions/ai/aks-anf_introduction.html)

### <a name="education"></a>Education
* [Moodle unter Azure NetApp Files-NFS-Speicher](https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-netapp-files-for-nfs-storage-with-moodle/ba-p/2300630)

## <a name="windows-apps-and-sql-server-solutions"></a>Lösungen für Windows-Apps und SQL Server

In diesem Abschnitt finden Sie Referenzen zu Lösungen für Windows-Anwendungen und SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Dateifreigaben und globales Dateicaching

* [Notfallwiederherstellung für Enterprise-Dateifreigaben mit Azure NetApp Files und DFS-Namespaces](https://techcommunity.microsoft.com/t5/azure-architecture-blog/disaster-recovery-for-enterprise-file-shares/ba-p/2808757)
* [Build Your Own Azure NFS? Wrestling Linux File Shares into Cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares) (Erstellen eines eigenen NFS: Verschieben von Linux-Dateifreigaben in die Cloud)
* [Globally Distributed Enterprise File Sharing with Azure NetApp Files and NetApp Global File Cache](https://f.hubspotusercontent20.net/hubfs/525875/NA-580-0521-Architecture-Doc-R3.pdf) (Global verteilte Unternehmens-Dateifreigaben mit Azure NetApp Files und NetApp Global File Cache)
* [Cloud-Compliance für Azure NetApp Files](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [SQL Server in Azure Virtual Machines mit Azure NetApp Files – Azure-Beispielszenarien](/azure/architecture/example-scenario/file-storage/sql-server-azure-netapp-files)
* [SQL Server im Azure-Bereitstellungshandbuch unter Verwendung von Azure NetApp Files](https://www.netapp.com/pdf.html?item=/media/27154-tr-4888.pdf)
* [Vorteile der Verwendung von Azure NetApp Files für die SQL Server-Bereitstellung](solutions-benefits-azure-netapp-files-sql-server.md)
* [Deploy SQL Server Over SMB with Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs) (Bereitstellen von SQL Server über SMB mit Azure NetApp Files)
* [Deploy SQL Server Always-On Failover Cluster over SMB with Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) (Bereitstellen von SQL Server-Always-On-Failoverclustern über SMB mit Azure NetApp Files) 
* [Deploy Always-On Availability Groups with Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) (Bereitstellen von Always-On-Verfügbarkeitsgruppen mit Azure NetApp Files) 

## <a name="sap-on-azure-solutions"></a>Lösung für SAP in Azure

Dieser Abschnitt enthält Referenzen zu Lösungen für SAP in Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>SAP allgemein und SAP Netweaver 

* [Ausführen von SAP NetWeaver unter Windows in Azure – Azure Architecture Center](/azure/architecture/reference-architectures/sap/sap-netweaver)
* [SAP applications on Microsoft Azure using Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf) (SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files)
* [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux mit Azure NetApp Files für SAP-Anwendungen](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern unter Windows mit Azure NetApp Files (SMB) für SAP-Anwendungen](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP-Anwendungen: Multi-SID-Leitfaden](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA für Linux-VMs in hochskalierten Systemen – Azure Architecture Center](/azure/architecture/reference-architectures/sap/run-sap-hana-for-linux-virtual-machines)
* [SAP S/4HANA unter Linux in Azure – Azure Architecture Center](/azure/architecture/reference-architectures/sap/sap-s4hana)
* [Ausführen von SAP BW/4HANA mit Linux-VMs – Azure Architecture Center](/azure/architecture/reference-architectures/sap/run-sap-bw4hana-with-linux-virtual-machines)
* [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [NFS v4.1-Volumes unter Azure NetApp Files für SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Hochverfügbarkeit bei hochskalierten SAP HANA-Lösungen mit Azure NetApp Files unter Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [Horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure VMs mit Azure NetApp Files auf SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [Horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mithilfe von Azure NetApp Files auf RedHat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [Horizontale SAP HANA-Skalierung mit HSR und Pacemaker unter RHEL: Azure Virtual Machines](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Tool für konsistente Momentaufnahmen in Azure-Anwendungen (AzAcSnap)](azacsnap-introduction.md)
* [SAP HANA-Notfallwiederherstellung mit Azure NetApp Files](https://docs.netapp.com/us-en/netapp-solutions-sap/pdfs/sidebar/SAP_HANA_Disaster_Recovery_with_Azure_NetApp_Files.pdf)

### <a name="sap-anydb"></a>SAP AnyDB

* [SAP-System auf Oracle Database in Azure – Azure Architecture Center](/azure/architecture/example-scenario/apps/sap-on-oracle)
* [Oracle-DBMS-Bereitstellung in Azure Virtual Machines für SAP-Workload: Azure Virtual Machines](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Deploy SAP AnyDB (Oracle 19c) with Azure NetApp Files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043) (Bereitstellen von SAP AnyDB (Oracle 19c) mit Azure NetApp Files)
* [Verwenden von Azure NetApp Files](../virtual-machines/workloads/sap/dbms_guide_ibm.md#using-azure-netapp-files)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [Bereitstellen einer SAP IQ-NLS-Hochverfügbarkeitslösung mithilfe von Azure NetApp Files in SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [Verwalten der SAP IQ-Lizenz in einem Hochverfügbarkeitsszenario](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>Technische SAP-Community und Blogbeiträge 

* [Azure NetApp Files – SAP HANA-Sicherung in Sekunden](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – Wiederherstellen Ihrer HANA-Datenbank aus einer Momentaufnahmesicherung](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – SAP HANA-Abladung der Sicherung mit Cloudsynchronisierung](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Beschleunigen Sie Ihre SAP HANA-Systemkopien mithilfe von Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloudvolumes ONTAP und unter Azure NetApp Files: SAP HANA-Systemmigration leicht gemacht](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [Architekturentscheidungen zum Maximieren von ANF-Investitionen in HANA N+M-Architekturen mit horizontaler Skalierung – Teil 1](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [Architekturentscheidungen zum Maximieren von ANF-Investitionen in HANA N+M-Architekturen mit horizontaler Skalierung – Teil 2](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [Architekturentscheidungen zum Maximieren von ANF-Investitionen in HANA N+M-Architekturen mit horizontaler Skalierung – Teil 3](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [SAP Landscape Dimensionierung und Volumenkonsolidierung mit Azure NetApp Files](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Azure VMware Solutions

* [Azure NetApp Files mit Azure VMware Solution – Einbinden von Gastbetriebssystemen](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>Lösungen für virtuelle Desktopinfrastrukturen

Dieser Abschnitt enthält Referenzen zu Lösungen für virtuelle Desktopinfrastrukturen.

### <a name="azure-virtual-desktop"></a><a name="windows-virtual-desktop"></a>Azure Virtual Desktop

* [Vorteile der Verwendung von Azure NetApp Files mit Azure Virtual Desktop](solutions-windows-virtual-desktop.md)
* [Speicheroptionen für FSLogix-Profilcontainer in Azure Virtual Desktop](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Erstellen eines FSLogix-Profilcontainers für einen Hostpool mit Azure NetApp Files](../virtual-desktop/create-fslogix-profile-container.md)
* [Azure Virtual Desktop auf Unternehmensebene](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Bewährte Methoden für Azure NetApp Files](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [Einrichten von Azure NetApp Files für MSIX App Attach](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

### <a name="citrix"></a>Citrix   

* [Citrix Profile Management with Azure NetApp Files Best Practices Guide](https://www.netapp.com/pdf.html?item=/media/55973-tr-4901.pdf) (Citrix Profile Management mit Azure NetApp Files: Leitfaden zu bewährten Methoden)


## <a name="hpc-solutions"></a>Lösungen für HPC

Dieser Abschnitt enthält Referenzen zu Lösungen für High Performance Computing (HPC). 

### <a name="generic-hpc"></a>HPC allgemein

* [Azure NetApp Files: Getting the most out of your cloud storage](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf) (Azure NetApp Files: Optimale Nutzung Ihres Cloudspeichers)
* [Ausführen von MPI-Workloads mit Azure Batch und Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure CycleCloud: CycleCloud- HPC-Umgebungen in Azure NetApp Files](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Öl- und Gasanlagen

* [High Performance Computing (HPC): Oil and gas in Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926) (High Performance Computing: Lösungen für Öl- und Gasanlagen in Azure)
* [Ausführen von Software zur Reservoirsimulation in Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Electronic Design Automation (EDA)

* [Vorteile der Verwendung von Azure NetApp Files für die elektronische Entwurfsautomatisierung](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab with Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md) (Azure CycleCloud: EDA-HPC-Lab mit Azure NetApp Files)
* [Azure für die Halbleiterindustrie](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analytics

* [Leitfaden zur Architektur von SAS in Azure – Azure Architecture Center | Azure NetApp Files](/azure/architecture/guide/sas/sas-overview#azure-netapp-files-nfs)
* [Azure NetApp Files: A shared file system to use with SAS Grid on Microsoft Azure](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192) (Azure NetApp Files: Dateifreigabesystem zur Verwendung mit SAS Grid in Microsoft Azure)
* [Azure NetApp Files: A shared file system to use with SAS Grid on MS Azure (RHEL8.3/nconnect UPDATE)](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648) (Azure NetApp Files: Dateifreigabesystem für SAS Grid in Microsoft Azure (RHEL8.3/nconnect UPDATE))
* [Best Practices für die Verwendung von Microsoft Azure mit SAS®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

### <a name="healthcare"></a>Integritätsressourcen

* [Epic EHR on Microsoft Azure Using Azure NetApp Files](https://www.netapp.com/pdf.html?item=/media/62375-tr-4909.pdf) (Epic EHR in Microsoft Azure unter Verwendung von Azure NetApp Files)

## <a name="azure-platform-services-solutions"></a>Lösungen für Azure-Plattformdienste

Dieser Abschnitt enthält Lösungen für Azure-Plattformdienste. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services und Kubernetes

* [Astra: Schützen, Wiederherstellen und Verwalten Ihrer AKS-Workloads auf Azure NetApp Files](https://cloud.netapp.com/hubfs/Astra%20Azure%20Documentation.pdf) 
* [Integrieren von Azure NetApp Files in Azure Kubernetes Service](../aks/azure-netapp-files.md)
* [Out-of-This-World Kubernetes performance on Azure with Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift) (Überirdische Leistung von Kubernetes in Azure mit Azure NetApp Files)
* [Azure NetApp Files + Trident = Dynamic and Persistent Storage for Kubernetes](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/) (Azure NetApp Files + Trident = dynamische und dauerhafte Speicherung für Kubernetes)
* [Trident - Storage Orchestrator for Containers](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html) (Trident: Speicherorchestrator für Container)
* [Magento-E-Commerce-Plattform in Azure Kubernetes Service (AKS)](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-red-hat-openshift"></a>Azure Red Hat Openshift   

*   [Verwenden von Trident zum Automatisieren von Azure NetApp Files von OpenShift](https://techcommunity.microsoft.com/t5/fasttrack-for-azure/using-trident-to-automate-azure-netapp-files-from-openshift/ba-p/2367351)

### <a name="azure-batch"></a>Azure Batch

* [Ausführen von MPI-Workloads mit Azure Batch und Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
