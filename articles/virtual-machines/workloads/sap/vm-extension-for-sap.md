---
title: Implementieren der Azure-VM-Erweiterung für SAP-Lösungen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die VM-Erweiterung für SAP bereitstellen.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: bc85e68ed643e8a39f22343cfb939ab803fcdaef
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067454"
---
# <a name="implement-the-azure-vm-extension-for-sap-solutions"></a>Implementieren der Azure-VM-Erweiterung für SAP-Lösungen

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-resources]:vm-extension-for-sap.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-Ressourcen)
[new-monitoring]:vm-extension-for-sap.md#38d9f33f-d0af-4b8f-8134-f1f97d656fb6 (Neue Version der VM-Erweiterung für SAP)
[std-extension]:vm-extension-for-sap-standard.md (Standardversion der Azure-VM-Erweiterung für SAP-Lösungen)
[new-extension]:vm-extension-for-sap-new.md (Neue Version der Azure-VM-Erweiterung für SAP-Lösungen)
[azure-ps]:/powershell/azure/
[azure-cli]:/cli/azure/install-classic-cli
[azure-cli-2]:/cli/azure/install-azure-cli

[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Bereitstellungsszenarien für virtuelle Computer für SAP in Microsoft Azure)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure-Überwachungslösung für SAP)

> [!NOTE]
> Allgemeine Aussage zum Support: Unterstützung für die Azure-Erweiterung für SAP wird über SAP-Supportkanäle bereitgestellt. Wenn Sie Unterstützung mit der Azure-VM-Erweiterung für SAP-Lösungen benötigen, öffnen Sie einen Supportfall beim SAP-Support.
 
Wenn Sie die VM wie unter [Bereitstellungsszenarien für virtuelle Computer für SAP in Azure][deployment-guide-3] beschrieben vorbereitet haben, ist der Azure-VM-Agent auf dem virtuellen Computer installiert. Der nächste Schritt ist das Bereitstellen der Azure-Erweiterung für SAP, die im Repository für Azure-Erweiterungen in den globalen Azure-Datencentern verfügbar ist. Weitere Informationen finden Sie unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide-9.1].
 
Um sicherzustellen, dass SAP Ihre Umgebung unterstützt, aktivieren Sie die Azure-VM-Erweiterung für SAP so, wie dies unter Konfigurieren der Azure-Erweiterung für SAP beschrieben ist.

## <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-Ressourcen

Beim Einrichten der Bereitstellung Ihrer SAP-Software benötigen Sie die folgenden SAP-Ressourcen:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure

* In SAP-Hinweis [2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [1409604] enthält die erforderliche SAP Host Agent-Version für Windows in Azure.
* SAP-Hinweis [2191498] enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692] enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung für SAP.
* SAP-spezifische PowerShell-Cmdlets, die Teil von [Azure PowerShell][azure-ps] sind.
* SAP-spezifische Befehle der Azure-Befehlszeilenschnittstelle, die Teil der [Azure-Befehlszeilenschnittstelle][azure-cli-2] sind.
 
## <a name="differences-between-the-two-versions-of-the-azure-vm-extension-for-sap-solutions"></a>Unterschiede zwischen den beiden Versionen der Azure-VM-Erweiterung für SAP-Lösungen

Es gibt zwei Versionen der VM-Erweiterung für SAP. Überprüfen Sie die Voraussetzungen für SAP und die erforderlichen Mindestversionen des SAP-Kernels und des SAP-Host-Agents in den Ressourcen, die unter [SAP-Ressourcen][sap-resources] aufgeführt sind.

### <a name="standard-version-of-vm-extension-for-sap"></a>Standardversion der VM-Erweiterung für SAP

Diese Version ist die aktuelle Standard-VM-Erweiterung für SAP. Es gibt einige Ausnahmen, bei denen Microsoft empfiehlt, die neue VM-Erweiterung für SAP zu installieren. Darüber hinaus kann der SAP-Support beim Öffnen einer Supportanfrage die Installation der neuen VM-Erweiterung anfordern. Weitere Informationen, wann die Verwendung der neuen Version der VM-Erweiterung für SAP sinnvoll ist, finden Sie im Kapitel [Neue Version der VM-Erweiterung für SAP][new-monitoring].
 
### <a name="new-version-of-vm-extension-for-sap"></a><a name="38d9f33f-d0af-4b8f-8134-f1f97d656fb6"></a>Neue Version der VM-Erweiterung für SAP

Diese Version ist die neue Azure-VM-Erweiterung für SAP-Lösungen. Mit weiteren Verbesserungen und neuen Azure-Angeboten wurde die neue Erweiterung erstellt, um alle Azure-Ressourcen eines virtuellen Computers überwachen zu können. Diese Erweiterung benötigt Internetzugriff auf die URL „management.azure.com“. Sie unterstützt zusätzliche Speicheroptionen, z. B. Standarddatenträger und Betriebssysteme. Wählen Sie die neue Version der VM-Erweiterung aus, wenn einer der folgenden Punkte zutrifft:
 
* Sie möchten die Erweiterung mit Terraform, Azure Resource Manager-Vorlagen oder mit anderer Software als Azure CLI oder Azure PowerShell installieren.
* Sie möchten die Erweiterung auf SuSE SLES 15 oder höher installieren.
* Sie möchten die Erweiterung unter Red Hat Enterprise Linux 8.1 oder höher installieren.
* Sie möchten Azure Ultra Disk oder Standard Managed Disks verwenden.
* Der Microsoft- oder SAP-Support fordert Sie auf, die neue Erweiterung zu installieren.
 
## <a name="recommendation"></a>Empfehlung

Derzeit wird empfohlen, die Standardversion der Erweiterung für jede Installation zu verwenden, bei der keiner der Anwendungsfälle für die neue Version der Erweiterung gilt. Derzeit wird an der Verbesserung der neuen Version der VM-Erweiterung gearbeitet, um sie zur Standardversion der Erweiterung machen und die jetzige Standardversion als veraltet herunterstufen zu können. Während dieser Zeit können Sie die neue Version verwenden. Sie müssen jedoch sicherstellen, dass die VM-Erweiterung auf management.azure.com zugreifen kann.
 
> [!NOTE]
> Stellen Sie sicher, dass Sie die VM-Erweiterung deinstallieren, bevor Sie zwischen den beiden Versionen wechseln.

## <a name="next-steps"></a>Nächste Schritte
* [Standardversion der Azure-VM-Erweiterung für SAP-Lösungen][std-extension]
* [Neue Version der Azure-VM-Erweiterung für SAP-Lösungen][new-extension]

