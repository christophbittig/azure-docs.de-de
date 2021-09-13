---
title: Grundlegendes zu Azure Cloud Services (erweiterter Support)
description: Enthält eine Beschreibung der untergeordneten Elemente des NetworkConfiguration-Elements der Dienstkonfigurationsdatei, mit der die Virtual Network- und DNS-Werte angegeben werden.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ffea4e8edd9cc6c11644017e972db3ce3202c766
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355826"
---
# <a name="about-azure-cloud-services-extended-support"></a>Grundlegendes zu Azure Cloud Services (erweiterter Support)

Clouddienste (erweiterter Support) ist ein neues Bereitstellungsmodell für [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/). Es basiert auf  [Azure Resource Manager](../azure-resource-manager/management/overview.md)und ist jetzt allgemein verfügbar. Die Hauptvorteile von Cloud Services (erweiterter Support) sind die regionale Resilienz sowie die Featureparität mit Azure Cloud Services bei der Bereitstellung mit Azure Service Manager. Darüber hinaus bietet es einige ARM-Funktionen, wie rollenbasierte Zugriffssteuerung (RBAC), Tags, Richtlinien und Unterstützung von Bereitstellungsvorlagen.  

Aufgrund dieser Änderung wird das auf Azure Service Manager basierende Bereitstellungsmodell für Cloud Services in [Cloud Services (klassisch)](../cloud-services/cloud-services-choose-me.md) umbenannt. Sie haben weiterhin die Möglichkeit, Ihre Web- und Cloudanwendungen und -dienste zu entwickeln und schnell bereitzustellen. Sie können Ihre Infrastruktur für Clouddienste bedarfsabhängig skalieren und sicherstellen, dass die Leistung Ihrer Anwendungen ausreicht, während gleichzeitig die Kosten reduziert werden.  

:::image type="content" source="media/inside-azure-for-iot.png" alt-text="YouTube-Video zu Cloud Services (erweiterter Support)" link="https://youtu.be/H4K9xTUvNdw":::


## <a name="what-does-not-change"></a>Nicht von der Änderung betroffene Bereiche 
- Sie erstellen den Code, definieren die Konfigurationen und führen die Bereitstellung in Azure durch. Azure richtet die Compute-Umgebung ein, führt Ihren Code aus und übernimmt dann die Überwachung und Verwaltung für Sie.
- Bei Cloud Services (erweiterter Support) werden auch zwei Arten von Rollen unterstützt: [Web und Worker](../cloud-services/cloud-services-choose-me.md). Es werden keine Änderungen am Entwurf, an der Architektur oder den Komponenten von Web- und Workerrollen vorgenommen. 
- Die drei Komponenten eines Clouddiensts, also die Dienstdefinition (.csdef), die Dienstkonfiguration (.cscfg) und das Dienstpaket (.cspkg), sind weiterhin ohne Änderung des [Formats](cloud-services-model-and-package.md) vorhanden. 
- Es müssen keine Änderungen am Runtimecode vorgenommen werden, da die Datenebene identisch ist und nur die Steuerungsebene geändert wurde. 
- Ausrichtung der Releases von Azure-Gastbetriebssystemen und der zugehörigen Updates auf Cloud Services (klassisch)
- Keine Änderung des zugrunde liegenden Updateprozesses in Bezug auf Upgradedomänen, Durchführung des Upgrades, Rollback und zulässige Dienständerungen während eines Updates

## <a name="changes-in-deployment-model"></a>Änderungen am Bereitstellungsmodell

Für die Bereitstellung von Cloud Services (erweiterter Support) sind nur geringfügige Änderungen an der Dienstkonfiguration (.cscfg) und der Dienstdefinition (.csdef) erforderlich. Es müssen keine Änderungen am Runtimecode vorgenommen werden. Es ist aber erforderlich, die Bereitstellungsskripts zu aktualisieren, um die neuen Azure Resource Manager-basierten APIs aufzurufen. 

:::image type="content" source="media/overview-image-1.png" alt-text="Abbildung: Konfiguration von Cloud Service (klassisch) mit hinzugefügtem Vorlagenabschnitt":::

Hauptunterschiede zwischen Cloud Services (klassisch) und Cloud Services (erweiterter Support) in Bezug auf die Bereitstellung: 

- Für Azure Resource Manager-Bereitstellungen werden [ARM-Vorlagen](../azure-resource-manager/templates/overview.md) verwendet. Hierbei handelt es sich um eine JSON-Datei (JavaScript Object Notation), in der die Infrastruktur und die Konfiguration für Ihr Projekt definiert sind. Für die Vorlage wird deklarative Syntax verwendet. Hiermit können Sie angeben, was Sie bereitstellen möchten, ohne dass Sie die Folge der Programmierbefehle für die Erstellung schreiben müssen. Die Datei für die Dienstkonfiguration und die Dienstdefinition muss beim Bereitstellen von Cloud Services (erweiterter Support) jeweils mit der [ARM-Vorlage](../azure-resource-manager/templates/overview.md) konsistent sein. Dies kann erreicht werden, indem entweder die [manuelle Erstellung der ARM-Vorlage](deploy-template.md) gewählt wird oder [PowerShell](deploy-powershell.md), das [Portal](deploy-portal.md) und [Visual Studio](deploy-visual-studio.md) verwendet werden.  

- Kunden müssen [Azure Key Vault](../key-vault/general/overview.md) nutzen, um [Zertifikate in Cloud Services (erweiterter Support) zu verwalten](certificates-and-key-vault.md). Mit Azure Key Vault können Sie die Anmeldeinformationen von Anwendungen, z. B. Geheimnisse, Schlüssel und Zertifikate, in einem zentralen und geschützten Cloudrepository sicher speichern und verwalten. Ihre Anwendungen können sich zur Laufzeit bei Key Vault authentifizieren, um die Anmeldeinformationen abzurufen. 

- Alle Ressourcen, die über [Azure Resource Manager](../azure-resource-manager/templates/overview.md) bereitgestellt werden, müssen sich in einem virtuellen Netzwerk befinden. Virtuelle Netzwerke und Subnetze werden in Azure Resource Manager erstellt, indem vorhandene Azure Resource Manager-APIs verwendet werden. Beim Bereitstellen von Cloud Services (erweiterter Support) muss im Abschnitt „NetworkConfiguration“ der CSCFG-Datei dann darauf verwiesen werden.   

- Bei jeder Instanz von Cloud Services (erweiterter Support) handelt es sich um eine unabhängige Bereitstellung. Für Cloud Services (erweiterter Support) werden für einen Clouddienst nicht mehrere Slots unterstützt.  
    - Die Funktion für den VIP-Austausch (VIP Swap) kann genutzt werden, um zwischen zwei Instanzen von Cloud Services (erweiterter Support) zu wechseln. Um eine neue Version eines Clouddiensts zu testen und zu stagen, stellen Sie einen Clouddienst (erweiterter Support) bereit und geben dafür an, dass der VIP-Austausch mit einem anderen Clouddienst vom Typ „Erweiterter Support“ möglich ist.  

- Die DNS-Bezeichnung (Domain Name Service) ist für Cloud Services (erweiterter Support) optional. In Azure Resource Manager ist die DNS-Bezeichnung eine Eigenschaft der öffentlichen IP-Ressource, die dem Clouddienst zugeordnet ist. 

## <a name="migration-to-azure-resource-manager"></a>Migration zu Azure Resource Manager

In Cloud Services (erweiterter Support) gibt es zwei Möglichkeiten, wie Sie die Migration von [Azure Service Manager](/powershell/azure/servicemanagement/overview) zu [Azure Resource Manager](../azure-resource-manager/management/overview.md) durchführen können. 
1) Kunden stellen Clouddienste direkt in Azure Resource Manager bereit und löschen anschließend den alten Clouddienst in Azure Service Manager. 
2) Bei der direkten Migration wird das Verfahren unterstützt, bei dem Cloud Services (klassisch) ohne bzw. nur mit minimaler Ausfallzeit zu Cloud Services (erweiterter Support) migriert wird. 

### <a name="additional-migration-options"></a>Zusätzliche Migrationsoptionen

Bei der Evaluierung von Plänen für die Migration von Cloud Services (klassisch) zu Cloud Services (erweiterter Support) kann es auch ratsam sein, weitere Azure-Dienste zu evaluieren, z. B.: [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [Azure Kubernetes Service](../aks/intro-kubernetes.md) und [Azure Service Fabric](../service-fabric/service-fabric-overview.md). Diese Dienste verfügen weiterhin über zusätzliche Funktionen, während für Cloud Services (erweiterter Support) weitestgehend die Featureparität mit Cloud Services (klassisch) beibehalten wird. 

Je nach Anwendung ist der Aufwand für die Umstellung auf Azure Resource Manager für Cloud Services (erweiterter Support) unter Umständen deutlich geringer als bei anderen Optionen. Falls Ihre Anwendung nicht weiterentwickelt wird, ist Cloud Services (erweiterter Support) eine gute Option, weil Sie einen kurzen Migrationspfad nutzen können. Wenn Ihre Anwendung dagegen ständig weiterentwickelt wird und moderne Features benötigt, sollten Sie andere Azure-Dienste erkunden, die Ihre derzeitigen und zukünftigen Anforderungen ggf. besser erfüllen. 

## <a name="next-steps"></a>Nächste Schritte
- Überprüfen Sie die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support).
- Stellen Sie eine Cloud Service-Instanz (erweiterter Support) über das [Azure-Portal](deploy-portal.md), mit [PowerShell](deploy-powershell.md), einer [Vorlage](deploy-template.md) oder [Visual Studio](deploy-visual-studio.md) bereit.
- Sehen Sie sich die [häufig gestellten Fragen](faq.yml) zu Cloud Services (erweiterter Support) an.
