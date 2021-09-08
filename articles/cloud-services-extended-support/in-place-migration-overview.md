---
title: Migrieren von Azure Cloud Services (klassisch) zu Azure Cloud Services (erweiterter Support)
description: Übersicht über die Migration von Cloud Services (klassisch) zu Cloud Services (erweiterter Support)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: ef2d8b68d6fb56f5d9b3508d550303aa9d9526d1
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349840"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migrieren von Azure Cloud Services (klassisch) zu Azure Cloud Services (erweiterter Support)

Dieses Dokument bietet eine Übersicht über das Migrieren von Cloud Services (klassisch) zu Cloud Services (erweiterter Support).     

Die Hauptvorteile von [Cloud Services (erweiterter Support)](overview.md) sind die regionale Resilienz sowie die Featureparität mit Azure Cloud Services bei der Bereitstellung mit Azure Service Manager. Darüber hinaus bietet es einige Azure Resource Manager-Funktionen, wie rollenbasierte Zugriffssteuerung (RBAC), Tags, Richtlinien sowie Unterstützung von Bereitstellungsvorlagen und Private Link. Beide Bereitstellungsmodelle (erweiterter Support und klassisch) sind mit [ähnlichen Preisstrukturen](https://azure.microsoft.com/pricing/details/cloud-services/) erhältlich.

Cloud Services (erweiterter Support) unterstützt zwei Möglichkeiten, wie Kunden die Migration von Azure Service Manager zu Azure Resource Manager durchführen können: erneutes Bereitstellen und direkte Migration. 

In der folgenden Tabelle werden die beiden Optionen miteinander verglichen.  


| Erneute Bereitstellung | Direkte Migration | 
|---|---|
| Kunden können einen neuen Clouddienst direkt in Azure Resource Manager bereitstellen und anschließend den alten Clouddienst in Azure Service Manager nach gründlicher Überprüfung löschen. | Das Tool für die direkte Migration unterstützt die nahtlose, durch die Plattform orchestrierte Migration vorhandener Bereitstellungen von Cloud Services (klassisch) zu Cloud Services (erweiterter Support). | 
| Die erneute Bereitstellung bietet Kunden folgende Möglichkeiten: <br><br> – Definieren von Ressourcennamen <br><br> – Organisieren oder Wiederverwenden von Ressourcen <br><br> – Wiederverwendung von Dienstkonfigurations- und Dienstdefinitionsdateien mit minimalen Änderungen | Bei der direkten Migration führt die Plattform Folgendes aus: <br><br> – Definieren von Ressourcennamen <br><br> – Organisieren jeder Bereitstellung und der zugehörigen Ressourcen in einzelnen Ressourcengruppen <br><br> – Ändern der vorhandenen Konfigurations- und Definitionsdatei für Azure Resource Manager | 
| Die Kunden müssen den Datenverkehr entsprechend der neuen Bereitstellung orchestrieren. | Bei der Migration bleiben die IP-Adresse und der Datenpfad erhalten. | 
| Die Kunden müssen die alten Clouddienste in Azure Resource Manager löschen. | Die Cloud Services-Ressourcen (klassisch) werden von der Plattform nach der Migration gelöscht. | 
| Dies ist eine Lift & Shift-Migration, die größere Flexibilität bietet, jedoch mehr Zeit erfordert. | Dies ist eine automatische Migration, die schneller erfolgt, jedoch geringere Flexibilität bietet. | 

Bei der Evaluierung von Plänen für die Migration von Cloud Services (klassisch) zu Cloud Services (erweiterter Support) kann es auch ratsam sein, weitere Azure-Dienste zu evaluieren, z. B.: [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [Azure Kubernetes Service](../aks/intro-kubernetes.md) und [Azure Service Fabric](../service-fabric/overview-managed-cluster.md). Diese Dienste verfügen weiterhin über zusätzliche Funktionen, während für Cloud Services (erweiterter Support) weitestgehend die Featureparität mit Cloud Services (klassisch) beibehalten wird.

Je nach Anwendung ist der Aufwand für die Umstellung auf Azure Resource Manager für Cloud Services (erweiterter Support) unter Umständen deutlich geringer als bei anderen Optionen. Falls Ihre Anwendung nicht weiterentwickelt wird, ist Cloud Services (erweiterter Support) eine gute Option, weil Sie einen kurzen Migrationspfad nutzen können. Wenn Ihre Anwendung dagegen ständig weiterentwickelt wird und moderne Features benötigt, sollten Sie andere Azure-Dienste erkunden, die Ihre derzeitigen und zukünftigen Anforderungen ggf. besser erfüllen.

## <a name="redeploy-overview"></a>Übersicht über die erneute Bereitstellung

Das erneute Bereitstellen Ihrer Dienste mit [Cloud Services (erweiterter Support)](overview.md) bietet die folgenden Vorteile: 

- Unterstützt Web- und Workerrollen, ähnlich wie Cloud Services (klassisch).
- Es werden keine Änderungen am Entwurf, an der Architektur oder den Komponenten von Web- und Workerrollen vorgenommen. 
- Es müssen keine Änderungen am Runtimecode vorgenommen werden, da die Datenebene mit der Datenebene der Clouddienste identisch ist. 
- Azure-Gastbetriebssysteme und die zugehörigen Updates werden auf Cloud Services (klassisch) ausgerichtet. 
- Es erfolgt keine Änderung des zugrunde liegenden Updateprozesses in Bezug auf Upgradedomänen, Durchführung des Upgrades, Rollback und zulässige Dienständerungen während eines Updates.

Ein neuer Cloud Service (erweiterter Support) kann mit den folgenden Clienttools direkt in Azure Resource Manager erneut bereitgestellt werden:

- [Bereitstellen eines Clouddiensts: Portal](deploy-portal.md)
- [Bereitstellen eines Clouddiensts: PowerShell](deploy-powershell.md)
- [Bereitstellen eines Clouddiensts: Vorlage](deploy-template.md)
- [Bereitstellen eines Clouddiensts: SDK](deploy-sdk.md)
- [Bereitstellen eines Clouddiensts: Visual Studio](/visualstudio/azure/cloud-services-extended-support?context=%2fazure%2fcloud-services-extended-support%2fcontext%2fcontex)


## <a name="migration-tool-overview"></a>Übersicht über das Migrationstool

Die plattformgestützte Migration bietet die folgenden wesentlichen Vorteile:

- Ermöglicht für die meisten Szenarien eine nahtlose, durch die Plattform orchestrierte Migration ohne Ausfallzeiten. Erfahren Sie mehr über [unterstützte Szenarien](in-place-migration-technical-details.md).  
- Migriert vorhandene Clouddienste in drei einfachen Schritten: überprüfen, vorbereiten, committen (oder abbrechen). Weitere Informationen zur [Funktionsweise des Migrationstools]in-place-migration-overview.md#migration-steps).
- Ermöglicht das Testen migrierter Bereitstellungen nach erfolgreicher Vorbereitung. Committet die Migration und schließt sie ab, während durch den Abbruch ein Rollback der Migration durchgeführt wird.

Das Migrationstool verwendet dieselben APIs und bietet dieselbe Funktionalität wie die [Migration von Virtual Machines (klassisch)](../virtual-machines/migration-classic-resource-manager-overview.md). 

## <a name="setup-access-for-migration"></a>Einrichten des Zugriffs für die Migration

Um diese Migration auszuführen, müssen Sie als Co-Administrator für das Abonnement hinzugefügt werden und die erforderlichen Anbieter registrieren. 

1. Melden Sie sich beim Azure-Portal an.
3. Wählen Sie im Menü Hub die Option Abonnement aus. Wählen Sie Alle Dienste aus, falls die Option nicht angezeigt wird.
3. Suchen Sie den entsprechenden Abonnementeintrag, und prüfen Sie dann das Feld MEINE ROLLE. Für einen Co-Administrator muss der Wert „Kontoadministrator“ lauten. Sollten Sie keinen Co-Administrator hinzufügen können, lassen Sie sich von einem Dienstadministrator oder Co-Administrator für das Abonnement hinzufügen.

4. Registrieren Sie Ihr Abonnement über das [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) oder die [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) für den Namespace „Microsoft.ClassicInfrastructureMigration“.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Überprüfen Sie den Status Ihrer Registrierung. Die Registrierung kann einige Minuten dauern. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Migrationsunterschiede zwischen Cloud Services (klassisch) und Virtual Machines (klassisch)
Azure Service Manager unterstützt zwei verschiedene Computeprodukte: [Azure Virtual Machines (klassisch)](/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) und [Azure Cloud Services (klassisch)](../cloud-services/cloud-services-choose-me.md), auch als Web-/Workerrollen bezeichnet. Die beiden Produkte unterscheiden sich in Bezug auf den Bereitstellungstyp innerhalb des Clouddiensts. Azure Cloud Services (klassisch) verwendet einen Clouddienst, der Bereitstellungen mit Web-/Workerrollen enthält. Azure Virtual Machines (klassisch) verwendet einen Clouddienst, der Bereitstellungen mit IaaS-VMs enthält.

Die Liste unterstützter Szenarien unterscheidet sich zwischen Cloud Services (klassisch) und Virtual Machines (klassisch) aufgrund von Unterschieden bei den Bereitstellungstypen.

## <a name="migration-steps"></a>Schritte bei der Migration
 
Kunden können ihre Bereitstellungen von Cloud Services (klassisch) mithilfe derselben vier Vorgänge migrieren, die auch für die Migration von Virtual Machines (klassisch) verwendet werden. 

1. **Migration überprüfen**: Stellt sicher, dass die Migration nicht durch gängige, nicht unterstützte Szenarien verhindert wird.
2. **Migration vorbereiten**: Dupliziert die Ressourcenmetadaten in Azure Resource Manager. Alle Ressourcen werden für Erstellungs-, Aktualisierungs- und Löschvorgänge gesperrt, um sicherzustellen, dass die Ressourcenmetadaten zwischen Azure Service Manager und Azure Resource Manager synchronisiert sind. Alle Lesevorgänge funktionieren sowohl mit der API für Cloud Services (klassisch) als auch mit der API für Cloud Services (erweiterter Support).
3. **Migration abbrechen**: Entfernt Ressourcenmetadaten aus Azure Resource Manager. Entsperrt alle Ressourcen für Erstellungs-, Aktualisierungs- und Löschvorgänge.
4. **Commit für Migration ausführen**: Entfernt Ressourcenmetadaten aus Azure Service Manager. Entsperrt die Ressourcen für Erstellungs-, Aktualisierungs- und Löschvorgänge. Nach einem Commitversuch ist ein Abbruch nicht mehr zulässig.

>[!NOTE]
> Vorbereitung, Abbruch und Commit sind idempotent. Wenn Fehler auftreten, sollte sich das Problem durch einen Wiederholungsversuch beheben lassen.

:::image type="content" source="media/in-place-migration-1.png" alt-text="Abbildung mit dem Diagramm der bei der Migration durchzuführenden Schritte.":::

Weitere Informationen finden Sie unter [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Für die Migration von Cloud Services (klassisch) verfügbare unterstützte Ressourcen und Features
-   Speicherkonten
-   Virtuelle Netzwerke
-   Netzwerksicherheitsgruppen
-   Reservierte öffentliche IP-Adressen
-   Endpunkt-Zugriffssteuerungslisten
-   Benutzerdefinierte Routen
-   Interner Lastenausgleich
-   Zertifikatmigration zu Key Vault
-   Plug-Ins und Erweiterung (XML- und JSON-basiert)
-   Aufgaben beim Start/beim Beenden
-   Bereitstellungen mit beschleunigtem Netzwerkbetrieb
-   Bereitstellungen mit einzelnen oder mehreren Rollen
-   Load Balancer Basic
-   Eingabe, Instanzeingabe, interne Endpunkte
-   Dynamische öffentliche IP-Adressen
-   DNS-Name 
-   Regeln für den Netzwerkdatenverkehr

## <a name="supported-configurations--migration-scenarios"></a>Unterstützte Konfigurationen/Migrationsszenarien
Dies sind die wichtigsten Szenarien für Kombinationen aus Ressourcen, Features und Cloud Services. Diese Liste ist nicht vollständig.

| Dienst | Konfiguration | Kommentare | 
|---|---|---|
| [Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) | Virtuelle Netzwerke, die Azure Active Directory Domain Services enthalten | Ein virtuelles Netzwerk mit Clouddienstbereitstellung und Azure AD Domain Services wird unterstützt. Der Kunde muss Azure AD Domain Services zunächst separat migrieren und dann eine Migration des übrigen virtuellen Netzwerks nur mit der Clouddienstbereitstellung durchführen. |
| Clouddienst | Clouddienst mit einer Bereitstellung nur in einem einzigen Slot | Clouddienste mit einer Produktions- oder Stagingslotbereitstellung können migriert werden. |
| Clouddienst | Bereitstellung nicht in einem öffentlich sichtbaren virtuellen Netzwerk (VNet-Standardbereitstellung) | Ein Clouddienst kann sich in einem öffentlich sichtbaren virtuellen Netzwerk, in einem verborgenen virtuellen Netzwerk oder in gar keinem virtuellen Netzwerk befinden.  Clouddienste in einem verborgenen virtuellen Netzwerk und Clouddienste in einem öffentlich sichtbaren virtuellen Netzwerk werden für die Migration unterstützt. Der Kunde kann anhand der Überprüfungs-API ermitteln, ob eine Bereitstellung innerhalb eines Standard-VNet erfolgt, und somit feststellen, ob die Migration möglich ist. |
|Clouddienst | XML-Erweiterungen (BGInfo, Visual Studio Debugger, Web Deploy und Remotedebuggen) | Alle XML-Erweiterungen werden für die Migration unterstützt. 
| Virtual Network | Virtuelles Netzwerk mit mehreren Clouddiensten | Ein virtuelles Netzwerk mit mehreren Clouddiensten wird für die Migration unterstützt. Das virtuelle Netzwerk und alle darin enthaltenen Clouddienste werden zu Azure Resource Manager migriert. |
| Virtual Network | Migration virtueller Netzwerke, die über das Portal erstellt wurden (erfordert die Verwendung von „Group Resource-group-name VNet-Name“ in der CSCFG-Datei)  | Im Rahmen der Migration wird der VNet-Name in der CSCFG-Datei in die Azure Resource Manager-ID des virtuellen Netzwerks geändert. (subscription/subscription-id/resource-group/resource-group-name/resource/vnet-name) <br><br>Um die Bereitstellung nach der Migration zu verwalten, aktualisieren Sie die lokale Kopie der CSCFG-Datei so, dass sie die Azure Resource Manager-ID anstelle des VNet-Namens verwendet. <br><br>Eine CSCFG-Datei, die das alte Benennungsschema verwendet, führt bei der Überprüfung zu einem Fehler. 
| Virtual Network | Migration einer Bereitstellung mit Rollen in einem anderen Subnetz | Ein Clouddienst mit unterschiedlichen Rollen in verschiedenen Subnetzen wird für die Migration unterstützt. |

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrieren zu Cloud Services (erweiterter Support) über das [Azure-Portal](in-place-migration-portal.md)
- Migrieren zu Cloud Services (erweiterter Support) über [PowerShell](in-place-migration-powershell.md)
