---
title: Bereitstellen von Citrix auf Azure VMware Solution
description: Erfahren Sie, wie Sie VMware Horizon in Azure VMware Solution bereitstellen.
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d3acd7ac8772a3d6e501b43f7277bcb57d8cd08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021374"
---
# <a name="deploy-citrix-on-azure-vmware-solution"></a>Bereitstellen von Citrix auf Azure VMware Solution

Der Citrix Virtual Apps- und Desktopdienst unterstützt Azure VMware Solution. Azure VMware Solution stellt eine Cloudinfrastruktur mit vSphere-Clustern zur Verfügung, die von der Azure-Infrastruktur erstellt wurden. Sie können Citrix Virtual Apps und Desktop Service nutzen, um Azure VMware Solution für die Bereitstellung Ihrer [VDA-Workload (Virtual Delivery Agent)](https://www.citrix.com/downloads/xendesktop/components/xendesktop-and-xenapp-76-VDA.html) auf die gleiche Weise wie vSphere in lokalen Umgebungen zu verwenden. 

[Weitere Informationen zu virtuellen Citrix-Apps und Desktops](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/)

[Bereitstellungshandbuch](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-vmware-solution-avs-integration)

[Lösungsübersicht](https://www.citrix.com/content/dam/citrix/en_us/documents/solution-brief/citrix-virtual-apps-and-desktop-service-on-azure-vmware-solution.pdf)

**Häufig gestellte Fragen (Q&As)**
 
- Q. Kann ich meine vorhandenen Citrix-Desktops und -Apps zu Azure VMware Solution migrieren oder eine Hybridumgebung betreiben, die aus lokalen und Azure VMware Solution Citrix-Workloads besteht? 

    A. Ja. Sie können die gleichen Computerabbilder, Anwendungspakete und Prozesse verwenden, die Sie derzeit verwenden. Sie können lokale und Azure VMware Solution für eine Migration nahtlos miteinander verknüpfen.

- Q. Kann Citrix als eigenständige Umgebung innerhalb von Azure VMware Solution? 

    A. Ja. Sie können eine Hybridumgebung migrieren, betreiben oder eine eigenständige Umgebung direkt in einem Azure VMware Solution 

- Q. Unterstützt Azure VMware Solution sowohl PVS als auch MCS? 

    A. Ja 

- Q. Werden GPU-basierte Workloads in Citrix auf Azure VMware Solution? 

    A. Derzeit leider nicht. Citrix-Workloads auf Microsoft Azure unterstützen jedoch GPU, wenn dieser Fall für Sie wichtig ist. 

- Q. Wird Azure VMware Solution mit der verwendungsbasierten Citrix-Bereitstellung oder LTSR unterstützt?  

    A. Nein.  Azure VMware Solution wird nur mit den Citrix Virtual Apps und Desktops-Dienstangeboten unterstützt.  

- Q. An wen wende ich mich für Support? 

    A. Kunden sollten sich an den Citrix Support www.citrix.com/support wenden, um Unterstützung zu erhalten. 

- Q. Kann ich meinen Azure Virtual Desktop-Vorteil von Microsoft mit Citrix auf Azure VMware Solution?
 
    A. Nein. Azure Virtual Desktop-Vorteile gelten nur für native Microsoft Azure Workloads. Der Citrix Virtual Apps and Desktops Service ist ein natives Azure-Angebot, mit dem Sie die Vorteile von Azure Virtual Desktop neben Ihrer Azure VMware Solution-Bereitstellung nutzen können. 

- Q. Wie erwerbe ich den Citrix Virtual Apps and Desktops Service, um die Azure VMware Solution zu nutzen? 
    
    A. Sie können Citrix-Angebote über Ihren Citrix-Partner oder direkt im Azure Marketplace. 
