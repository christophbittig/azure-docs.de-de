---
title: 'Azure Cloud Services (erweiterter Support): Änderungen nach der Migration'
description: Übersicht über Änderungen nach der Migration zu Cloud Services (erweiterter Support)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: df9a41b6832c9ca93aa7078b0d06082f1441a5e5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439271"
---
# <a name="post-migration-changes"></a>Änderungen nach der Migration
Die Bereitstellung von Cloud Services (klassisch) wird in eine Bereitstellung von Cloud Services (erweiterter Support) konvertiert. Weitere Informationen finden Sie in der [Dokumentation zu Cloud Services (erweiterter Support)](deploy-prerequisite.md).  

## <a name="changes-to-deployment-files"></a>Änderungen an Bereitstellungsdateien 

An der CSDEF- und der CSCFG-Datei des Kunden werden kleinere Änderungen vorgenommen, damit die Bereitstellungsdateien den Anforderungen von Azure Resource Manager und Cloud Services (erweiterter Support) entsprechen. Nach der Migration werden die neuen Bereitstellungsdateien abgerufen oder die vorhandenen Dateien aktualisiert. Dies ist für Aktualisierungs-/Löschvorgänge erforderlich.  

- Virtual Network verwendet die vollständige Azure Resource Manager-Ressourcen-ID anstelle des Ressourcennamens im Abschnitt „NetworkConfiguration“ der CSCFG-Datei. Beispielsweise `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. Bei virtuellen Netzwerken, die derselben Ressourcengruppe wie der Clouddienst angehören, können Sie die CSCFG-Datei wieder auf die Verwendung des VNet-Namen zurücksetzen.  

- Klassische Größen wie „Small“, „Large“ und „ExtraLarge“ werden durch ihre neuen Größennamen ersetzt: Standard_A*. Die Größennamen müssen in der CSDEF-Datei in ihre neuen Namen geändert werden. Weitere Informationen finden Sie unter [Voraussetzungen für die Bereitstellung von Azure Cloud Services (erweiterter Support)](deploy-prerequisite.md#required-service-definition-file-csdef-updates).

- Verwenden Sie die Abruf-API, um die neueste Kopie der Bereitstellungsdateien zu erhalten. 
    - Rufen Sie die Vorlage über das [Portal](../azure-resource-manager/templates/export-template-portal.md), [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), die [CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) oder die [REST-API](/rest/api/resources/resourcegroups/exporttemplate) ab. 
    - Rufen Sie die CSDEF-Datei über [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) oder die [REST-API](/rest/api/compute/cloudservices/rest-get-package) ab. 
    - Rufen Sie die CSCFG-Datei über [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) oder die [REST-API](/rest/api/compute/cloudservices/rest-get-package) ab. 
    
 

## <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Änderungen z. B. an Automatisierung, CI/CD-Pipeline, benutzerdefinierten Skripts, benutzerdefinierten Dashboards oder benutzerdefinierten Tools von Kunden  

Kunden müssen ihre Tools und Automatisierungsfunktionen aktualisieren, um mit der Verwendung der neuen APIs/Befehle zum Verwalten ihrer Bereitstellung zu beginnen. Ein Kunde kann im Rahmen dieser Änderung problemlos neue Features und Funktionen von Azure Resource Manager/Cloud Services (erweiterter Support) übernehmen. 

- Änderungen an Ressourcen- und Ressourcengruppennamen nach der Migration
    - Im Rahmen der Migration werden beispielsweise die Namen einiger Ressourcen wie des Clouddiensts oder öffentliche IP-Adressen geändert. Diese Änderungen müssen möglicherweise vor der Aktualisierung des Clouddiensts in den Bereitstellungsdateien berücksichtigt werden. [Erfahren Sie mehr über die Änderung von Ressourcennamen](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Erneutes Erstellen von Regeln und Richtlinien zum Verwalten und Skalieren von Clouddiensten 
    - [Regeln für die automatische Skalierung](configure-scaling.md) werden nicht migriert. Erstellen Sie die Regeln für die automatische Skalierung nach der Migration neu.  
    - [Warnungen](enable-alerts.md) werden nicht migriert. Erstellen Sie die Warnungen nach der Migration neu.
    - Der Schlüsseltresor wird ohne Zugriffsrichtlinien erstellt. [Erstellen Sie geeignete Richtlinien](../key-vault/general/assign-access-policy-portal.md) für den Schlüsseltresor, um Ihre Zertifikate anzuzeigen oder zu verwalten. Zertifikate werden unter den Einstellungen auf der Registerkarte „Geheimnisse“ angezeigt.


## <a name="changes-to-certificate-management-post-migration"></a>Änderungen an der Zertifikatverwaltung nach der Migration 

Als Standardverfahren bei der Verwaltung Ihrer Zertifikate sollten alle gültigen PFX-Zertifikatdateien dem Zertifikatspeicher in Key Vault hinzugefügt werden. Die Aktualisierung funktioniert einwandfrei über jeden Client: Portal, PowerShell oder REST-API.

Derzeit überprüft das Azure-Portal für Sie, ob alle erforderlichen Zertifikate in den Zertifikatspeicher in Key Vault hochgeladen wurden, und warnt Sie, wenn ein Zertifikat nicht gefunden wird. Wenn Sie jedoch planen, Zertifikate als Geheimnisse zu verwenden, können diese Zertifikate nicht anhand ihres Fingerabdrucks überprüft werden, sodass Aktualisierungsvorgänge, bei denen Geheimnisse hinzugefügt werden müssen, über das Portal nicht funktionieren. Kunden wird empfohlen, PowerShell oder die REST-API zu verwenden, um Aktualisierungen mit Geheimnissen auszuführen.


## <a name="changes-for-update-via-visual-studio"></a>Änderungen bei Aktualisierungen über Visual Studio
Wenn Sie Aktualisierungen direkt über Visual Studio veröffentlichen, müssen Sie zuerst die neueste CSCFG-Datei aus der Bereitstellung nach der Migration herunterladen. Verwenden Sie diese Datei als Referenz, um Netzwerkkonfigurationsdetails zu Ihrer aktuellen CSCFG-Datei im Visual Studio-Projekt hinzuzufügen. Kompilieren Sie dann die Lösung, und veröffentlichen Sie sie. Möglicherweise müssen Sie für diese Aktualisierung die Key Vault-Instanz und die Ressourcengruppe auswählen.


## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrieren zu Cloud Services (erweiterter Support) über das [Azure-Portal](in-place-migration-portal.md)
- Migrieren zu Cloud Services (erweiterter Support) über [PowerShell](in-place-migration-powershell.md)
