---
title: Technische Details und Anforderungen für die Migration zu Azure Cloud Services (erweiterter Support)
description: In diesem Artikel werden die technischen Details und Anforderungen für die Migration von Azure Cloud Services (klassisch) zu Azure Cloud Services (erweiterter Support) vorgestellt.
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.openlocfilehash: 55ce5305962562876a97dfd7677e6af5e1eb9e3a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347199"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Technische Details der Migration zu Azure Cloud Services (erweiterter Support)   

In diesem Artikel werden die technischen Details des Migrationstools für Cloud Services (klassisch) erläutert. 

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Details zu den unterstützten Features/Szenarien für die Migration 


### <a name="extensions-and-plugin-migration"></a>Migration von Erweiterung und Plug-Ins 
- Alle aktivierten und unterstützten Erweiterungen werden migriert. 
- Deaktivierte Erweiterungen werden nicht migriert. 
- Plug-Ins sind ein Legacykonzept und sollten vor der Migration entfernt werden. Ihre Migration wird zwar unterstützt, aber falls die Erweiterung aktiviert werden soll, müssen Plug-Ins nach der Migration zunächst entfernt werden. Erst danach kann die Erweiterung installiert werden. Hiervon sind insbesondere Remotedesktop-Plug-Ins und -Erweiterungen betroffen.   
 
### <a name="certificate-migration"></a>Zertifikatmigration
- In Cloud Services (erweiterter Support) werden Zertifikate in einer Key Vault-Instanz gespeichert. Im Rahmen der Migration wird eine Key Vault-Instanz für den Kunden erstellt. Die Instanz erhält den Cloud Service-Namen, und alle Zertifikate aus dem Azure-Dienst-Manager werden nach Key Vault übertragen. 
- Der Verweis auf diese Key Vault-Instanz wird in der Vorlage angegeben oder über PowerShell oder die Azure CLI übergeben. 

### <a name="service-configuration-and-service-definition-files"></a>Dienstkonfiguration und Dienstdefinitionsdateien
- Die CSCFG- und CSDEF-Dateien müssen für Cloud Services (erweiterter Support) geringfügig geändert werden. 
- Die Namen von Ressourcen wie beispielsweise dem virtuellen Netzwerk und der VM-SKU lauten anders. Siehe hierzu [Übersetzung von Ressourcen und Namenskonventionen nach der Migration](#translation-of-resources-and-naming-convention-post-migration).
- Kunden können ihre neuen Bereitstellungen über [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) und die [REST-API](/rest/api/compute/cloudservices/get) abrufen. 

### <a name="cloud-service-and-deployments"></a>Clouddienst und Bereitstellungen
- Jede Bereitstellung von Cloud Services (erweiterter Support) ist ein unabhängiger Clouddienst. Bereitstellungen werden nicht länger mithilfe von Slots in einem Clouddienst gruppiert.
- Wenn Sie in Ihrer Instanz von Cloud Services (klassisch) über zwei Slots verfügen, müssen Sie einen Slot (Staging) löschen und den anderen Slot (Produktion) mithilfe des Migrationstools nach Azure Resource Manager verschieben. 
- Die öffentliche IP-Adresse der Cloud Services-Bereitstellung bleibt nach der Migration zu Azure Resource Manager unverändert erhalten und wird als IP-Ressource (dynamisch oder statisch) der SKU „Basic“ verfügbar gemacht. 
- Der DNS-Name und die Domäne (cloudapp.azure.net) für den migrierten Clouddienst bleiben gleich. 

### <a name="virtual-network-migration"></a>Migration des virtuellen Netzwerks
- Wenn sich eine Cloud Services-Bereitstellung in einem virtuellen Netzwerk befindet, werden alle Cloud Services-Instanzen und die zugehörigen Ressourcen für virtuelle Netzwerke gemeinsam migriert. 
- Nach der Migration wird das virtuelle Netzwerk in einer anderen Ressourcengruppe platziert als die Cloud Services-Instanz. 
- Für virtuelle Netzwerke mit mehreren Cloud Services-Instanzen werden die Clouddienste einzeln und nacheinander migriert. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migration von Bereitstellungen ohne virtuelles Netzwerk
- Seit 2017 werden in Azure neue Bereitstellungen (ohne ein vom Kunden angegebenes virtuelles Netzwerk) automatisch in einem von der Plattform erzeugten virtuellen Netzwerk namens „default“ erstellt. Diese virtuellen Standardnetzwerke sind für Kunden nicht sichtbar.   
- Im Rahmen der Migration werden diese virtuellen Standardnetzwerke nach der Verschiebung in Azure Resource Manager angezeigt. Um die Bereitstellung in Azure Resource Manager zu verwalten oder zu aktualisieren, müssen Kunden diese Informationen zu virtuellen Netzwerken im Abschnitt „NetworkConfiguration“ der CSCFG-Datei hinzufügen.    
- Das virtuelle Netzwerk „default“ wird bei der Migration zu Azure Resource Manager in derselben Ressourcengruppe platziert wie die Cloud Services-Instanz.
- Cloud Services-Instanzen, die vor dieser Zeit erstellt wurden, sind in keinem virtuellen Netzwerk enthalten und können nicht mithilfe des Tools migriert werden. Erwägen Sie, diese Cloud Services-Instanzen direkt in Azure Resource Manager erneut bereitzustellen. 
- Um zu prüfen, ob eine Bereitstellung migriert werden kann, führen Sie die Validierungs-API für die Bereitstellung aus. Das Ergebnis der API-Ausführung enthält eine Fehlermeldung, die explizit angibt, ob die betreffende Bereitstellung migriert werden kann.     

### <a name="load-balancer"></a>Load Balancer   
- Für einen Clouddienst mit Verwendung eines öffentlichen Endpunkts wird ein von der Plattform erstellter Lastenausgleich, der dem Clouddienst zugeordnet ist, innerhalb des Kundenabonnements in Azure Resource Manager verfügbar gemacht. Der Lastenausgleich ist eine schreibgeschützte Ressource, und Aktualisierungen können nur über die Dateien für die Dienstkonfiguration (CSCFG) und die Dienstdefinition (CSDEF) durchgeführt werden. 

### <a name="key-vault"></a>Key Vault
- Im Rahmen der Migration erstellt Azure automatisch eine neue Key Vault-Instanz und migriert alle Zertifikate in diese Instanz. Das Tool erlaubt keine Verwendung einer vorhandenen Key Vault-Instanz. 
- Für Cloud Services (erweiterter Export) wird eine Key Vault-Instanz benötigt, die sich in derselben Region und im selben Abonnement befindet. Diese Key Vault-Instanz wird während der Migration automatisch erstellt. 

## <a name="resources-and-features-not-available-for-migration"></a>Für die Migration nicht verfügbare Ressourcen und Features
Dies sind die wichtigsten Szenarien für Kombinationen aus Ressourcen, Features und Cloud Services. Diese Liste ist nicht vollständig. 

| Resource | Nächste Schritte/Problemumgehung | 
|---|---|
| Regeln zur automatischen Skalierung | Die Migration wird durchlaufen, aber Regeln werden gelöscht. [Erstellen Sie die Regeln nach der Migration zu Cloud Services (erweiterter Support) neu](./configure-scaling.md). | 
| Alerts | Die Migration wird durchlaufen, aber Warnungen werden gelöscht. [Erstellen Sie die Regeln nach der Migration zu Cloud Services (erweiterter Support) neu](./enable-alerts.md). | 
| VPN Gateway | Entfernen Sie die VPN Gateway-Instanz, bevor Sie mit der Migration beginnen, und erstellen Sie sie nach Abschluss der Migration neu. | 
| ExpressRoute-Gateway (nur im gleichen Abonnement wie Virtual Network) | Entfernen Sie das ExpressRoute-Gateway, bevor Sie mit der Migration beginnen, und erstellen Sie es nach Abschluss der Migration neu. | 
| Kontingent  | Kontingente werden nicht migriert. Fordern Sie vor der Migration ein [neues Kontingent](../azure-resource-manager/templates/error-resource-quota.md#solution) für Azure Resource Manager an, damit die Überprüfung erfolgreich ist. | 
| Affinitätsgruppen | Wird nicht unterstützt. Entfernen Sie alle Affinitätsgruppen vor der Migration.  | 
| Virtuelle Netzwerke mit [VNet-Peering](../virtual-network/virtual-network-peering-overview.md)| Bevor Sie ein virtuelles Netzwerk migrieren, das mittels Peering mit einem anderen virtuellen Netzwerk verbunden ist, löschen Sie das Peering, migrieren Sie das virtuelle Netzwerk zu Resource Manager, und erstellen Sie das Peering neu. Je nach Architektur kann dies zu Downtime führen. | 
| Virtuelle Netzwerke, die App Service-Umgebungen enthalten | Nicht unterstützt | 
| Virtuelle Netzwerke, die HDInsight-Dienste enthalten | Wird nicht unterstützt. 
| Virtuelle Netzwerke, die Azure API Management-Bereitstellungen enthalten | Wird nicht unterstützt. <br><br> Um das virtuelle Netzwerk zu migrieren, ändern Sie das virtuelle Netzwerk der API Management-Bereitstellung. Dies ist ein Vorgang ohne Downtime. | 
| Klassische ExpressRoute-Verbindungen | Wird nicht unterstützt. <br><br>Diese Verbindungen müssen vor Beginn der PaaS-Migration zu Azure Resource Manager migriert werden. Weitere Informationen finden Sie unter [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell](../expressroute/expressroute-howto-move-arm.md). |  
| Rollenbasierte Access Control | Nach der Migration ändert sich der URI der Ressource von `Microsoft.ClassicCompute` in `Microsoft.Compute`. RBAC-Richtlinien müssen nach der Migration aktualisiert werden. | 
| Application Gateway | Nicht unterstützt. <br><br> Entfernen Sie die Application Gateway-Instanz, bevor Sie mit der Migration beginnen, und erstellen Sie sie nach Abschluss der Migration zu Azure Resource Manager neu. | 

## <a name="unsupported-configurations--migration-scenarios"></a>Nicht unterstützte Konfigurationen/Migrationsszenarien

| Konfiguration/Szenario  | Nächste Schritte/Problemumgehung | 
|---|---|
| Migration einiger älterer Bereitstellungen, die sich nicht in einem virtuellen Netzwerk befinden | Für einige Cloud Services-Bereitstellungen, die nicht in einem virtuellen Netzwerk enthalten sind, wird die Migration nicht unterstützt. <br><br> 1. Überprüfen Sie mithilfe der Überprüfungs-API, ob die Bereitstellung für die Migration geeignet ist. <br> 2. Wenn sie geeignet ist, werden die Bereitstellungen in Azure Resource Manager unter ein virtuelles Netzwerk mit dem Präfix „DefaultRdfeVnet“ verschoben. | 
| Migration von Bereitstellungen, die sowohl die Produktions- als auch die Stagingslotbereitstellung über dynamische IP-Adressen beinhalten | Bei der Migration eines Clouddiensts mit zwei Slots muss der Stagingslot gelöscht werden. Anschließend migrieren Sie den Produktionsslot als unabhängige Instanz von Cloud Services (erweiterter Support) zu Azure Resource Manager. Stellen Sie dann die Stagingumgebung als neue Instanz von Cloud Services (erweiterter Support) erneut bereit, und konfigurieren Sie sie als mit der ersten Instanz austauschbar. | 
| Migration von Bereitstellungen, die sowohl die Produktions- als auch die Stagingslotbereitstellung über reservierte IP-Adressen beinhalten | Wird nicht unterstützt. | 
| Migration einer Produktions- und Stagingbereitstellung in einem anderen virtuellen Netzwerk|Bei der Migration eines Clouddiensts mit zwei Slots muss der Stagingslot gelöscht werden. Anschließend migrieren Sie den Produktionsslot als unabhängige Instanz von Cloud Services (erweiterter Support) zu Azure Resource Manager. Eine neue Bereitstellung von Cloud Services (erweiterter Support) kann dann mit der migrierten Bereitstellung verknüpft werden. Die Eigenschaft „Austauschbar“ muss aktiviert sein. Bereitstellungsdateien der alten Stagingslotbereitstellung können wiederverwendet werden, um diese neue austauschbare Bereitstellung zu erstellen. | 
| Migration eines leeren Clouddiensts (Clouddienst ohne Bereitstellung) | Wird nicht unterstützt. | 
| Migration einer Bereitstellung mit dem Remotedesktop-Plug-In und den Remotedesktoperweiterungen | Option 1: Entfernen Sie das Remotedesktop-Plug-In vor der Migration. Dazu sind Änderungen an den Bereitstellungsdateien erforderlich. Die Migration wird dann erfolgreich durchlaufen. <br><br> Option 2: Entfernen Sie die Remotedesktoperweiterung, und migrieren Sie die Bereitstellung. Entfernen Sie nach der Migration das Plug-In, und installieren Sie die Erweiterung. Dazu sind Änderungen an den Bereitstellungsdateien erforderlich. <br><br> Entfernen Sie das Plug-In und die Erweiterung vor der Migration. [Plug-Ins werden für die Verwendung in Cloud Services (erweiterter Support) nicht empfohlen](./deploy-prerequisite.md#required-service-definition-file-csdef-updates).| 
| Virtuelle Netzwerke mit PaaS- und IaaS-Bereitstellung |Nicht unterstützt <br><br> Verschieben Sie entweder die PaaS- oder die IaaS-Bereitstellungen in ein anderes virtuelles Netzwerk. Dies führt zu Downtime. | 
Clouddienstbereitstellungen mit älteren Rollengrößen (z. B. „Small“ oder „ExtraLarge“) | Die Migration wird ausgeführt, aber die Rollengrößen werden auf moderne Rollengrößen aktualisiert. Die Kosten oder SKU-Eigenschaften bleiben unverändert, und die VM wird für diese Änderung nicht neu gestartet. Aktualisieren Sie alle Bereitstellungsartefakte, damit sie auf diese neuen modernen Rollengrößen verweisen. Weitere Informationen finden Sie unter [Verfügbare VM-Größen](available-sizes.md).|
| Migration des Clouddiensts zu einem anderen virtuellen Netzwerk | Nicht unterstützt <br><br> 1. Verschieben Sie die Bereitstellung vor der Migration in ein anderes klassisches virtuelles Netzwerk. Dies führt zu Downtime. <br> 2. Migrieren Sie das neue virtuelle Netzwerk zu Azure Resource Manager. <br><br> oder <br><br> 1. Migrieren Sie das virtuelle Netzwerk zu Azure Resource Manager. <br>2. Verschieben Sie den Clouddienst in ein neues virtuelles Netzwerk. Dies führt zu Downtime. | 
| Clouddienst in einem virtuellen Netzwerk, aber ohne Zuweisung eines expliziten Subnetzes | Wird nicht unterstützt. Zur Entschärfung muss die Rolle in ein Subnetz verschoben werden. Hierfür ist ein Rollenneustart erforderlich (Downtime). | 

## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Übersetzung von Ressourcen und Namenskonventionen nach der Migration
Während der Migration werden die Ressourcennamen geändert, und einige wenige Cloud Services-Features werden als Azure Resource Manager-Ressourcen verfügbar gemacht. In der folgenden Tabelle werden die für eine Cloud Services-Migration spezifischen Änderungen zusammengefasst.

| Cloud Services (klassisch) <br><br> Ressourcenname | Cloud Services (klassisch) <br><br> Syntax| Cloud Services (erweiterter Support) <br><br> Ressourcenname| Cloud Services (erweiterter Support) <br><br> Syntax | 
|---|---|---|---|
| Clouddienst | `cloudservicename` | Nicht zugeordnet| Nicht zugeordnet |
| Bereitstellung (über Portal erstellt) <br><br> Bereitstellung (nicht über Portal erstellt)  | `deploymentname` | Cloud Services (erweiterter Support) | `cloudservicename` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Nicht zugeordnet |  Virtuelles Netzwerk (nicht über Portal erstellt) <br><br> Virtuelles Netzwerk (über Portal erstellt) <br><br> Virtuelle Netzwerke (Standard) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `VNet-cloudservicename`|
| Nicht zugeordnet | Nicht zugeordnet | Key Vault | `KV-cloudservicename` | 
| Nicht zugeordnet | Nicht zugeordnet | Ressourcengruppe für Cloud Services-Bereitstellungen | `cloudservicename-migrated` | 
| Nicht zugeordnet | Nicht zugeordnet | Ressourcengruppe für virtuelles Netzwerk | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Nicht zugeordnet | Nicht zugeordnet | Öffentliche IP-Adresse (dynamisch) | `cloudservicenameContractContract` | 
| Reservierter IP-Name | `reservedipname` | Reservierte IP-Adresse (nicht über Portal erstellt) <br><br> Reservierte IP-Adresse (über Portal erstellt) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Nicht zugeordnet| Nicht zugeordnet | Load Balancer | `LB-cloudservicename`|



## <a name="migration-issues-and-how-to-handle-them"></a>Migrationsprobleme und deren Behandlung 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Bei der Migration zeigt ein Vorgang lange Zeit keinen Fortschritt 
- Commit-, Vorbereitungs- und Abbruchvorgänge können abhängig von der Anzahl von Bereitstellungen viel Zeit in Anspruch nehmen. Vorgänge werden nach 24 Stunden mit einem Timeout abgebrochen.   
- Commit-, Vorbereitungs- und Abbruchvorgänge sind idempotent. Die meisten Problem können durch einen erneuten Versuch behoben werden. Es können vorübergehende Fehler vorliegen, die innerhalb weniger Minuten aufgelöst werden. Es wird empfohlen, den Vorgang nach einiger Zeit zu wiederholen. Wenn eine Migration mithilfe des Azure-Portals durchgeführt wird und ein Vorgang im Zustand „In Bearbeitung“ verbleibt, verwenden Sie PowerShell, um den Vorgang zu wiederholen. 
- Wenden Sie sich an den Support, um die Migration abzuschließen oder vom Back-End aus ein Rollback für die Bereitstellung durchzuführen. 

### <a name="migration-failed-in-an-operation"></a>Vorgangsfehler bei der Migration 
- Wenn es bei der Validierung zu einem Fehler kommt, ist dies auf ein(e) nicht unterstützte(s) Szenario/Feature/Ressource in der Bereitstellung oder im virtuellen Netzwerk zurückzuführen. Verwenden Sie die Liste der nicht unterstützten Szenarien, um in den Dokumenten nach einer Problemumgehung zu suchen.  
- Der Vorbereitungsvorgang führt zunächst eine Validierung durch, darunter einige ressourcenintensive Überprüfungen (nicht in der Validierung enthalten). Der Vorbereitungsfehler könnte auf ein nicht unterstütztes Szenario zurückzuführen sein. Suchen Sie in den öffentlich zugänglichen Dokumenten nach dem Szenario und einer Problemumgehung. Um zum ursprünglichen Zustand zurückzukehren und die Bereitstellung für Aktualisierungen und Löschvorgänge freizugeben, muss „Abbrechen“ aufgerufen werden.
- Wenn der Abbruch nicht erfolgreich ist, wiederholen Sie den Vorgang. Wenn die Wiederholung nicht erfolgreich ist, wenden Sie sich an den Support.
- Wenn der Commit nicht erfolgreich ist, wiederholen Sie den Vorgang. Wenn die Wiederholung nicht erfolgreich ist, wenden Sie sich an den Support. Selbst bei einem Commitfehler sollte kein Datenebenenfehler für Ihre Bereitstellung vorliegen. Ihre Bereitstellung sollte in der Lage sein, Kundendatenverkehr fehlerfrei zu verarbeiten. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Portal nach dem Vorbereiten aktualisiert, nach dem Neustart der Benutzeroberfläche sind „Commit“ oder „Abbrechen“ nicht mehr sichtbar 
- Das Portal speichert die Migrationsinformationen lokal und startet deshalb nach einer Aktualisierung selbst dann aus der Validierungsphase, wenn der Clouddienst sich in der Vorbereitungsphase befindet.  
- Sie können mithilfe des Portals erneut die Schritte für Validierung und Vorbereitung durchlaufen, um die Schaltflächen „Abbrechen“ und „Commit“ verfügbar zu machen. Es werden keine Fehler verursacht.
- Kunden können PowerShell oder REST-API für Abbruch oder Commit verwenden. 

### <a name="how-much-time-can-the-operations-takebr"></a>Wie lange können die Vorgänge dauern?<br>
Die Validierung ist als schneller Vorgang konzipiert. Die Vorbereitung dauert am längsten und kann abhängig von der Gesamtanzahl der migrierten Rolleninstanzen einige Zeit in Anspruch nehmen. Abbruch und Commit können ebenfalls eine Weile dauern, nehmen aber weniger Zeit in Anspruch als die Vorbereitung. Alle Vorgänge werden nach 24 Stunden mit einem Timeout abgebrochen.

## <a name="next-steps"></a>Nächste Schritte
Unterstützung bei der Migration Ihrer (klassischen) Cloud Services-Bereitstellung zu Cloud Services (erweiterter Support) finden Sie auf unserer Landing Page [Support und Problembehandlung](support-help.md).