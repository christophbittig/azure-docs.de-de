---
title: Archiv der Neuerungen in Defender für Cloud
description: Eine Beschreibung der Neuerungen und Änderungen in Defender für Cloud, die älter als sechs Monate sind.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: reference
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: f88b40cacd6b741c239f271311b0ec132f6f052e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526136"
---
# <a name="archive-for-whats-new-in-defender-for-cloud"></a>Archiv der Neuerungen in Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Die Hauptseite mit Versionshinweisen zu [Neuerungen in Defender für Cloud](release-notes.md) enthält Aktualisierungen aus den letzten sechs Monaten. Ältere Einträge finden Sie hingegen auf dieser Seite.

Auf dieser Seite finden Sie folgende Informationen:

- Neue Funktionen
- Behebung von Programmfehlern
- Veraltete Funktionen

## <a name="may-2021"></a>Mai 2021

Zu den Updates im Mai gehören:

- [Azure Defender für DNS und Azure Defender für Resource Manager, veröffentlicht zur allgemeinen Verfügbarkeit](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [Azure Defender für relationale Open-Source-Datenbanken, veröffentlicht zur allgemeinen Verfügbarkeit](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Neue Warnungen für Azure Defender für Resource Manager](#new-alerts-for-azure-defender-for-resource-manager)
- [CI/CD-Sicherheitsrisikoüberprüfung von Containerimages mit GitHub-Workflows und Azure Defender (Vorschau)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [Verfügbarkeit weiterer Resource Graph-Abfragen für einige Empfehlungen](#more-resource-graph-queries-available-for-some-recommendations)
- [Schweregrad der Empfehlung zur SQL-Datenklassifizierung geändert](#sql-data-classification-recommendation-severity-changed)
- [Neue Empfehlungen zur Aktivierung von Funktionen für den vertrauenswürdigen Start (Vorschau)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Neue Empfehlungen für die Härtung von Kubernetes-Clustern (Vorschau)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [Die Bewertungs-API wurde um zwei neue Felder erweitert.](#assessments-api-expanded-with-two-new-fields)
- [Cloudumgebungsfilter für Ressourcenbestand](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>Azure Defender für DNS und Azure Defender für Resource Manager, veröffentlicht zur allgemeinen Verfügbarkeit

Diese beiden breit gefächerten cloudnativen Bedrohungsschutzpläne befinden sich nun in der Phase der allgemeinen Verfügbarkeit.

Diese neuen Schutzmaßnahmen sorgen für eine erhebliche Verbesserung der Resilienz gegenüber Angriffen von Bedrohungsakteuren sowie für eine deutliche Erhöhung der Anzahl von Azure-Ressourcen, die durch Azure Defender geschützt werden.

- **Azure Defender für Resource Manager**: Überwacht automatisch alle in Ihrer Organisation ausgeführten Ressourcenverwaltungsvorgänge. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für Resource Manager](defender-for-resource-manager-usage.md)
    - [Warnungen für Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender für DNS**: Überwacht kontinuierlich alle DNS-Abfragen Ihrer Azure-Ressourcen. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für DNS](defender-for-dns-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für DNS](defender-for-dns-usage.md)
    - [Warnungen für DNS](alerts-reference.md#alerts-dns)

Verwenden Sie die folgenden Empfehlungen, um den Prozess für die Aktivierung dieser Pläne zu vereinfachen:

- **Azure Defender für Resource Manager muss aktiviert sein**
- **Azure Defender für DNS muss aktiviert sein**

> [!NOTE]
> Bei Aktivierung dieser Azure Defender-Pläne fallen Gebühren an. Weitere Informationen zu den Preisen pro Region finden Sie in der Security Center-Preisübersicht unter https://aka.ms/pricing-security-center.


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>Azure Defender für relationale Open-Source-Datenbanken, veröffentlicht zur allgemeinen Verfügbarkeit

Das Angebot zum SQL-Schutz von Azure Security Center wird um ein neues Paket erweitert, das für Ihre relationalen Open-Source-Datenbanken gilt:

- **Azure Defender für Azure SQL-Datenbankserver** – schützt Ihre nativen Azure SQL Server.
- **Azure Defender für SQL Server auf Computern** – erweitert denselben Schutz auf Ihre SQL Server-Instanzen in Hybrid-, Multicloud- und lokalen Umgebungen
- **Azure Defender für relationale Open-Source-Datenbanken**: Dient zur Verteidigung Ihrer Single Server-Versionen von Azure Database for MySQL, PostgreSQL und MariaDB.

Mit Azure Defender für relationale Open-Source-Datenbanken werden Ihre Server ständig auf Sicherheitsbedrohungen überwacht, und es werden anomale Datenbankaktivitäten erkannt, die auf potenzielle Bedrohungen für Azure Database for MySQL, PostgreSQL und MariaDB hinweisen. Beispiele:

- **Präzise Erkennung von Brute-Force-Angriffen**: Azure Defender für relationale Open-Source-Datenbanken liefert ausführliche Informationen zu versuchten und erfolgreichen Brute-Force-Angriffen. So verfügen Sie über alle Informationen zur Art und zum Status des Angriffs auf Ihre Umgebung, die Sie benötigen, um die Untersuchung durchführen und entsprechend reagieren zu können.
- **Warnungen zur Erkennung von bestimmtem Verhalten**: Mit Azure Defender für relationale Open-Source-Datenbanken werden Sie vor verdächtigem und unerwartetem Verhalten auf Ihren Servern gewarnt, z. B. Änderungen beim Zugriffsmuster Ihrer Datenbank.
- **Auf Threat Intelligence basierende Erkennung**: Azure Defender wendet die Threat Intelligence-Informationen und eine umfangreiche Wissensdatenbank von Microsoft auf die Anzeige von Bedrohungswarnungen an, damit Sie entsprechende Maßnahmen ergreifen können.

Weitere Informationen finden Sie unter [Einführung in Azure Defender für relationale Open-Source-Datenbanken](defender-for-databases-introduction.md).

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Neue Warnungen für Azure Defender für Resource Manager

Wir haben die folgenden Warnungen hinzugefügt, um den Bedrohungsschutz von Azure Defender für Resource Manager zu erweitern:

| Warnung (Warnungstyp)                                                                                                                                                | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE-Taktiken | severity |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**In Ihrer Azure-Umgebung wurden für eine RBAC-Rolle auf ungewöhnliche Weise Berechtigungen gewährt (Vorschau)**<br>(ARM_AnomalousRBACRoleAssignment)|Azure Defender für Resource Manager hat die Zuweisung einer RBAC-Rolle entdeckt, die gegenüber anderen Zuweisungen derselben zuweisenden Person bzw. für dieselbe zuweisende Person oder auf Ihrem Mandanten ungewöhnlich ist, weil Anomalien in den folgenden Bereichen bestehen: Zuweisungszeitpunkt, Standort der zuweisenden Person, zuweisende Person, Authentifizierungsmethode, zugewiesene Entitäten, verwendete Clientsoftware, Umfang der Zuweisung. Dieser Vorgang wurde ggf. von einem legitimen Benutzer Ihrer Organisation durchgeführt. Unter Umständen kann dies auch ein Hinweis darauf sein, dass ein Konto Ihrer Organisation übernommen wurde und dass der Bedrohungsakteur versucht, Berechtigungen für ein weiteres Konto in seinem Besitz zu gewähren.|Seitwärtsbewegung, Umgehen von Verteidigungsmaßnahmen|Medium|
|**Für Ihr Abonnement wurde auf verdächtige Weise eine benutzerdefinierte privilegierte Rolle erstellt (Vorschau)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Azure Defender für Resource Manager hat in Ihrem Abonnement eine verdächtige Erstellung der Definition einer benutzerdefinierten privilegierten Rolle erkannt. Dieser Vorgang wurde ggf. von einem legitimen Benutzer Ihrer Organisation durchgeführt. Unter Umständen kann dies auch ein Hinweis darauf sein, dass ein Konto in Ihrer Organisation übernommen wurde und der Bedrohungsakteur versucht, eine privilegierte Rolle für die zukünftige Verwendung zu erstellen, um eine Erkennung zu vermeiden.|Seitwärtsbewegung, Umgehen von Verteidigungsmaßnahmen|Niedrig|
|**Azure Resource Manager-Vorgang von einer verdächtigen IP-Adresse (Vorschau)**<br>(ARM_OperationFromSuspiciousIP)|Azure Defender für Resource Manager hat einen Vorgang über eine IP-Adresse erkannt, die in Threat Intelligence-Feeds als verdächtig gekennzeichnet wurde.|Ausführung|Medium|
|**Azure Resource Manager-Vorgang von einer verdächtigen Proxy-IP-Adresse (Vorschau)**<br>(ARM_OperationFromSuspiciousProxyIP)|Azure Defender für Resource Manager hat einen Ressourcenverwaltungsvorgang von einer IP-Adresse erkannt, die Proxydiensten zugeordnet ist, z. B. TOR. Dieses Verhalten kann legitim sein, aber es wird häufig mit bösartigen Aktivitäten in Verbindung gebracht, wenn Bedrohungsakteure versuchen, ihre Quell-IP-Adresse zu verbergen.|Umgehen von Verteidigungsmaßnahmen|Medium|
||||

Weitere Informationen finden Sie unter
- [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
- [Reagieren auf Warnungen von Azure Defender für Resource Manager](defender-for-resource-manager-usage.md)
- [Warnungen für Resource Manager](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>CI/CD-Sicherheitsrisikoüberprüfung von Containerimages mit GitHub-Workflows und Azure Defender (Vorschau)

Azure Defender für Containerregistrierungen ermöglicht DevSecOps-Teams jetzt Einblicke in GitHub Actions-Workflows.

Das neue Feature für Sicherheitsrisikoüberprüfungen für Containerimages mit Nutzung von Trivy unterstützt Ihre Entwickler bei der Überprüfung auf häufige Sicherheitsrisiken in Containerimages, *bevor* für Images der Pushvorgang in Containerregistrierungen erfolgt.

Die Berichte zu Containerüberprüfungen sind in Azure Security Center zusammengefasst und ermöglichen Sicherheitsteams einen besseren Einblick und ein tieferes Verständnis der Ursache für anfällige Containerimages und die zugehörigen ursprünglichen Workflows und Repositorys.

Weitere Informationen finden Sie unter [Identifizieren von anfälligen Containerimages in Ihren CI/CD-Workflows](defender-for-container-registries-cicd.md).

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>Verfügbarkeit weiterer Resource Graph-Abfragen für einige Empfehlungen

Für alle Empfehlungen von Security Center gibt es die Option, die Informationen zum Status der betroffenen Ressourcen mit Azure Resource Graph über **Abfrage öffnen** anzuzeigen. Ausführliche Informationen zu diesem leistungsstarken Feature finden Sie unter [Überprüfen von Empfehlungsdaten im Azure Resource Graph-Explorer (ARG)](review-security-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg).

Security Center umfasst integrierte Überprüfungen auf Sicherheitsrisiken, um Ihre VMs, SQL Server-Instanzen und zugehörigen Hosts sowie die Containerregistrierungen auf Sicherheitsrisiken zu überprüfen. Die Ergebnisse werden als Empfehlungen zurückgegeben, wobei alle Einzelergebnisse für die einzelnen Ressourcentypen jeweils in einer Ansicht zusammengefasst sind. Die Empfehlungen lauten wie folgt:

- Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden (unterstützt von Qualys).
- Sicherheitsrisiken für VMs müssen behoben werden
-  Bei SQL-Datenbanken sollten gefundene Sicherheitsrisiken behoben werden.
-  Bei SQL Servern auf Computern sollten gefundene Sicherheitsrisiken behoben werden.

Dank dieser Änderung können Sie die Schaltfläche **Abfrage öffnen** verwenden, um auch die Abfrage mit den Sicherheitsergebnissen zu öffnen.

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="Die Schaltfläche &quot;Abfrage öffnen&quot; bietet nun Optionen für eine tiefergehende Abfrage, bei der die Sicherheitsergebnisse für Empfehlungen im Zusammenhang mit Schwachstellenscannern angezeigt werden.":::

Die Schaltfläche **Abfrage öffnen** bietet zusätzliche Optionen für einige andere Empfehlungen, sofern relevant.

Weitere Informationen zu den Sicherheitsrisikoüberprüfungen von Security Center:

- [Der in Azure Defender integrierte Qualys-Schwachstellen-Scanner für Azure- und Hybrid-Maschinen](deploy-vulnerability-assessment-vm.md)
- [Integrierte Azure Defender-Überprüfung zur Sicherheitsrisikobewertung für SQL Server-Instanzen](defender-for-sql-on-machines-vulnerability-assessment.md)
- [Integrierte Azure Defender-Überprüfung zur Sicherheitsrisikobewertung für Containerregistrierungen](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>Schweregrad der Empfehlung zur SQL-Datenklassifizierung geändert

Der Schweregrad der Empfehlung **Vertrauliche Daten in Ihren SQL-Datenbanken sollten klassifiziert werden** wurde von **Hoch** in **Niedrig** geändert.

Dies ist Teil der unter [Verbesserungen bei der Empfehlung zur Klassifizierung vertraulicher Daten in den SQL Datenbanken](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases) angekündigten laufenden Änderungen an dieser Empfehlung.

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>Neue Empfehlungen zur Aktivierung von Funktionen für den vertrauenswürdigen Start (Vorschau)

Azure bietet den vertrauenswürdigen Start als nahtlose Möglichkeit zur Verbesserung der Sicherheit von VMs der [Generation 2](../virtual-machines/generation-2.md) an. Der vertrauenswürdige Start bietet Schutz vor komplexen und permanenten Angriffstechniken. Der vertrauenswürdige Start besteht aus mehreren koordinierten Infrastrukturtechnologien, die unabhängig voneinander aktiviert werden können. Jede Technologie bietet eine eigene Schutzschicht gegen komplexe Bedrohungen. Weitere Informationen finden Sie unter [Vertrauenswürdiger Start für Azure-VMs](../virtual-machines/trusted-launch.md).

> [!IMPORTANT]
> Der vertrauenswürdige Start erfordert die Erstellung neuer VMs. Sie können den vertrauenswürdigen Start nicht für vorhandene VMs aktivieren, die ursprünglich ohne vertrauenswürdigen Start erstellt wurden.
> 
> Der vertrauenswürdige Start befindet sich derzeit in der öffentlichen Vorschau. Die Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Mit der Empfehlung **Für unterstützte VMs muss ein virtuelles TPM-Gerät aktiviert werden** von Security Center wird sichergestellt, dass für Ihre Azure-VMs ein virtuelles TPM-Gerät genutzt wird. Diese virtualisierte Version einer Trusted Platform Module-Hardwareeinheit ermöglicht die Nachweiserbringung, indem die gesamte Startkette Ihrer VM (UEFI, Betriebssystem, System und Treiber) gemessen wird.

Wenn das virtuelle TPM-Gerät aktiviert ist, kann der sichere Start von der **Erweiterung für den Gastnachweis** per Remotezugriff überprüft werden. Mit den folgenden Empfehlungen wird sichergestellt, dass diese Erweiterung bereitgestellt wird:

- **Für unterstützte Windows-VMs muss der sichere Start aktiviert werden**
- **Für unterstützte Windows-VMs muss die Erweiterung für den Gastnachweis installiert sein**
- **Für unterstützte Windows-VM-Skalierungsgruppen muss die Erweiterung für den Gastnachweis installiert sein**
- **Für unterstützte Linux-VMs muss die Erweiterung für den Gastnachweis installiert sein**
- **Für unterstützte Linux-VM-Skalierungsgruppen muss die Erweiterung für den Gastnachweis installiert sein**

Weitere Informationen finden Sie unter [Vertrauenswürdiger Start für Azure-VMs](../virtual-machines/trusted-launch.md). 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Neue Empfehlungen für die Härtung von Kubernetes-Clustern (Vorschau)

Mit den folgenden Empfehlungen können Sie die weitere Härtung Ihrer Kubernetes-Cluster durchführen.

- **Kubernetes-Cluster dürfen nicht den Standardnamespace verwenden**: Verhindern Sie die Verwendung des Standardnamespace in Kubernetes-Clustern, um ConfigMap-, Pod-, Geheimnis-, Dienst- und Dienstkontoressourcen vor einem nicht autorisierten Zugriff zu schützen.
- **Kubernetes-Cluster müssen die automatische Bereitstellung von API-Anmeldeinformationen deaktivieren**: Deaktivieren Sie die automatische Bereitstellung von API-Anmeldeinformationen, um für eine potenziell kompromittierte Podressource die Ausführung von API-Befehlen für Kubernetes-Cluster zu verhindern.
- **Kubernetes-Cluster sollten keine CAPSYSADMIN-Sicherheitsfunktionen gewähren**

Informationen dazu, wie Sie Ihre containerisierten Umgebungen mit Security Center schützen können, finden Sie unter [Containersicherheit in Security Center](container-security.md).

### <a name="assessments-api-expanded-with-two-new-fields"></a>Die Bewertungs-API wurde um zwei neue Felder erweitert.

Wir haben die folgenden beiden Felder zur [Bewertungs-REST-API](/rest/api/securitycenter/assessments) hinzugefügt:

- **FirstEvaluationDate**: Der Zeitpunkt, zu dem die Empfehlung erstellt und zum ersten Mal ausgewertet wurde. Wird als UTC-Zeit im ISO 8601-Format zurückgegeben.
- **StatusChangeDate**: Der Zeitpunkt, zu dem sich der Status der Empfehlung zuletzt geändert hat. Wird als UTC-Zeit im ISO 8601-Format zurückgegeben.

Der anfängliche Standardwert für diese Felder ist für alle Empfehlungen `2021-03-14T00:00:00+0000000Z`.

Um auf diese Informationen zuzugreifen, können Sie eine der Methoden in der folgenden Tabelle verwenden.

| Tool                 | Details                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REST-API-Aufruf        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| Fortlaufendem Export    | Die beiden dedizierten Felder sind für die Log Analytics-Arbeitsbereichsdaten verfügbar.                                                                                            |
| [CSV-Export](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | Die beiden Felder sind in den CSV-Dateien enthalten.                                                        |
|                      |                                                                                                                                                                        |


Erfahren Sie mehr zur [Bewertungs-REST-API](/rest/api/securitycenter/assessments).


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>Cloudumgebungsfilter für Ressourcenbestand

Die Security Center-Seite für den Ressourcenbestand bietet einige Filter, mit denen die Liste mit den angezeigten Ressourcen schnell eingegrenzt werden kann. Weitere Informationen finden Sie unter [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md).

Ein neuer Filter bietet die Möglichkeit, die Liste gemäß den Cloudkonten einzugrenzen, mit denen Sie über die Multi-Cloud-Features von Security Center eine Verbindung hergestellt haben:

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="Umgebungsfilter für Bestand":::

Weitere Informationen zu den Multi-Cloud-Features:

- [Verbinden Ihrer AWS-Konten mit Azure Security Center](quickstart-onboard-aws.md)
- [Herstellen einer Verbindung zwischen Ihren GCP-Konten und Azure Security Center](quickstart-onboard-gcp.md)


## <a name="april-2021"></a>April 2021

Zu den Updates im April gehören:
- [Aktualisierte Seite „Ressourcenintegrität“ (Vorschau)](#refreshed-resource-health-page-in-preview)
- [Containerregistrierungsimages, die vor Kurzem gepullt wurden, werden jetzt wöchentlich erneut überprüft (veröffentlicht zur allgemeinen Verfügbarkeit)](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [Verwenden von Azure Defender für Kubernetes zum Schutz von Hybrid- und Multi-Cloud-Bereitstellungen von Kubernetes (Vorschau)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [Microsoft Defender für Endpunkt-Integration mit Azure Defender unterstützt jetzt Windows Server 2019 und Windows 10 Virtual Desktop (WVD) (veröffentlicht zur allgemeinen Verfügbarkeit)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [Empfehlungen zum Aktivieren von Azure Defender für DNS und Resource Manager (Vorschau)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [Drei Standards zur Einhaltung gesetzlicher Bestimmungen wurden hinzugefügt: „Azure CIS 1.3.0“, „CMMC Level 3“ und „Durch New Zealand ISM eingeschränkt“.](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Vier neue Empfehlungen im Zusammenhang mit der Gastkonfiguration (Vorschau)](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [CMK-Empfehlungen wurden in bewährte Methoden für die Sicherheitskontrolle verschoben](#cmk-recommendations-moved-to-best-practices-security-control)
- [Elf Azure Defender-Warnungen als veraltet eingestuft](#11-azure-defender-alerts-deprecated)
- [Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Kachel für Azure Defender für SQL auf Computern vom Azure Defender-Dashboard entfernt](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 Empfehlungen zwischen Sicherheitskontrollen verschoben](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>Aktualisierte Seite „Ressourcenintegrität“ (Vorschau)

Die Ressourcenintegrität von Security Center wurde erweitert und verbessert, um eine Momentaufnahmenansicht der Gesamtintegrität einer einzelnen Ressource zu erhalten. 

Sie können ausführliche Informationen zur Ressource und sich alle Empfehlungen im Zusammenhang mit der Ressource ansehen. Wenn Sie die [erweiterten Schutzpläne von Microsoft Defender](defender-for-cloud-introduction.md) verwenden, werden Ihnen außerdem sehr nützliche Sicherheitswarnungen für diese bestimmte Ressource angezeigt.

Wählen Sie auf der Seite [Ressourcenbestand](asset-inventory.md) eine beliebige Ressource aus, um die Seite „Ressourcenintegrität“ für eine Ressource zu öffnen.

Diese Vorschauseite auf Portalseiten im Security Center zeigt:

1. **Ressourceninformationen:** Zugehörige Ressourcengruppe, zugehöriges Abonnement, geografischer Standort und Ähnliches.
1. **Angewendetes Sicherheitsfeature:** Gibt an, ob Azure Defender für die Ressource aktiviert ist.
1. **Anzahl ausstehenden Empfehlungen und Warnungen:** Die Anzahl ausstehender Sicherheitsempfehlungen und Azure Defender-Warnungen.
1. **Umsetzbare Empfehlungen und handlungsrelevante Warnungen:** Auf zwei Registerkarten werden die Empfehlungen und Warnungen für die Ressource aufgeführt.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="Ressourcenintegritätsseite von Azure Security Center mit den Integritätsinformationen für einen virtuellen Computer":::

Weitere Informationen finden Sie unter [Tutorial: Untersuchen der Integrität Ihrer Ressourcen](investigate-resource-health.md).


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>Containerregistrierungsimages, die vor Kurzem gepullt wurden, werden jetzt wöchentlich erneut überprüft (veröffentlicht zur allgemeinen Verfügbarkeit)

Azure Defender für Containerregistrierungen enthält eine integrierte Überprüfung auf Sicherheitsrisiken. Bei dieser Überprüfung werden alle Images, die Sie in Ihre Registrierung pushen oder per Pullvorgang innerhalb der letzten 30 Tage abgerufen haben, sofort überprüft.

Jeden Tag werden neue Sicherheitsrisiken entdeckt. Nach diesem Update werden Containerimages, die innerhalb der letzten 30 Tage per Pullvorgang aus Ihren Registrierungen abgerufen wurden, wöchentlich **erneut überprüft**. So wird sichergestellt, dass neu ermittelte Sicherheitsrisiken in Ihren Images erkannt werden.

Da die Kosten für die Überprüfung pro Image berechnet werden, fallen für diese erneuten Überprüfungen keine zusätzlichen Kosten an.

Weitere Informationen finden Sie unter [Verwenden von Azure Defender für Containerregistrierungen zum Überprüfen Ihrer Images auf Sicherheitsrisiken](defender-for-container-registries-usage.md).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>Verwenden von Azure Defender für Kubernetes zum Schutz von Hybrid- und Multi-Cloud-Bereitstellungen von Kubernetes (Vorschau)

Azure Defender für Kubernetes erweitert seine Funktionen zum Schutz vor Bedrohungen, um Ihre Cluster unabhängig von Ihrem Bereitstellungsort zu schützen. Dies wurde durch die Integration von [Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/overview.md) und der neuen [Erweiterungsfunktionen](../azure-arc/kubernetes/extensions.md) ermöglicht. 

Wenn Sie Azure Arc in ihren Nicht-Azure Kubernetes-Clustern aktiviert haben, bietet eine neue Empfehlung von Azure Security Center an, die Azure Defender-Erweiterung mit nur wenigen Klicks für Sie bereitzustellen.

Verwenden Sie die Empfehlung (**für Kubernetes-Cluster mit Azure Arc-Unterstützung muss die Azure Defender-Erweiterung installiert sein**) und die Erweiterung, um Kubernetes-Cluster zu schützen, die bei anderen Cloudanbietern, jedoch nicht in ihren verwalteten Kubernetes-Diensten bereitgestellt werden.

Diese Integration zwischen Azure Security Center, Azure Defender und Kubernetes mit Azure Arc-Unterstützung ermöglicht:

- Einfache Bereitstellung der Azure Defender-Erweiterung für ungeschützte Kubernetes-Cluster mit Azure Arc-Unterstützung (manuell und skalierbar)
- Überwachung der Azure Defender-Erweiterung und ihres Bereitstellungsstatus über das Azure Arc-Portal
- Sicherheitsempfehlungen von Security Center werden auf der neuen Seite "Sicherheit" des Azure Arc-Portals angezeigt
- Von Azure Defender erkannte Sicherheitsbedrohungen werden auf der neuen Seite "Sicherheit" des Azure Arc-Portals angezeigt
- Kubernetes-Cluster mit Azure Arc-Unterstützung werden in die Azure Security Center-Plattform und -Benutzeroberfläche integriert.

Weitere Informationen finden Sie unter [Verwenden von Azure Defender für Kubernetes mit Ihren lokalen und Multi-Cloud Kubernetes-Clustern](defender-for-kubernetes-azure-arc.md).

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Empfehlung des Azure Security Center zur Bereitstellung der Azure Defender-Erweiterung für Azure Arc-fähige Kubernetes-Cluster" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::.


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>Microsoft Defender für Endpunkt-Integration mit Azure Defender unterstützt jetzt Windows Server 2019 und Windows 10 Virtual Desktop (WVD) (veröffentlicht zur allgemeinen Verfügbarkeit)

Microsoft Defender für den Endpunkt ist eine ganzheitliche, cloudbasierte Lösung für die Endpunktsicherheit. Sie ermöglicht die risikobasierte Verwaltung und Bewertung von Sicherheitsrisiken sowie Endpunkterkennung und -reaktion (Endpoint Detection and Response, EDR). Eine vollständige Liste mit den Vorteilen der gemeinsamen Nutzung von Defender für Endpunkt und Azure Security Center finden Sie unter [Schützen Sie Ihre Endpunkte mit der in Security Center integrierten EDR-Lösung: Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md).

Wenn Sie Azure Defender für Server auf einem Windows-Server aktivieren, ist im Plan eine Lizenz für Defender für Endpunkt enthalten. Falls Sie Azure Defender für Server bereits aktiviert haben und unter Ihrem Abonnement Windows 2019-Server nutzen, wird Defender für Endpunkt mit diesem Update darauf automatisch bereitgestellt. Es ist keine manuelle Aktion erforderlich. 

Die Unterstützung wurde nun auf Windows Server 2019 und [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) erweitert.

> [!NOTE]
> Stellen Sie beim Aktivieren von Defender für Endpunkt auf einem Windows Server 2019-Computer sicher, dass die unter [Aktivieren der Integration von Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md#enable-the-microsoft-defender-for-endpoint-integration) beschriebenen Voraussetzungen erfüllt sind.


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>Empfehlungen zum Aktivieren von Azure Defender für DNS und Resource Manager (Vorschau)

Zwei neue Empfehlungen wurden hinzugefügt, um den Prozess zum Aktivieren von [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) und [Azure Defender für DNS](defender-for-dns-introduction.md) zu vereinfachen:

- **Azure Defender für Resource Manager muss aktiviert sein**: Defender für Resource Manager überwacht automatisch die Ressourcenverwaltungsvorgänge in Ihrer Organisation. Azure Defender erkennt Bedrohungen und warnt Sie bei verdächtigen Aktivitäten.
- **Azure Defender für DNS muss aktiviert sein**: Defender für DNS bietet eine zusätzliche Schutzebene für Ihre Cloudressourcen, indem alle DNS-Abfragen aus Ihren Azure-Ressourcen fortlaufend überwacht werden. Azure Defender warnt Sie bei verdächtigen Aktivitäten auf der DNS-Ebene.

Bei Aktivierung dieser Azure Defender-Pläne fallen Gebühren an. Weitere Informationen zu den Preisen pro Region finden Sie in der Security Center-Preisübersicht unter https://aka.ms/pricing-security-center.

> [!TIP]
> Empfehlungen der Vorschau versetzen keine Ressourcen in einen fehlerhaften Zustand, und sie werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Setzen Sie sie trotzdem um, wann immer möglich, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen. Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Azure Security Center](implement-security-recommendations.md).


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Drei Standards zur Einhaltung gesetzlicher Bestimmungen wurden hinzugefügt: „Azure CIS 1.3.0“, „CMMC Level 3“ und „Durch New Zealand ISM eingeschränkt“.

Wir haben drei Standards für die Verwendung mit Azure Security Center hinzugefügt. Mithilfe des Dashboards zur Einhaltung gesetzlicher Bestimmungen können Sie jetzt Ihre Konformität mit Folgendem nachverfolgen:

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC Level 3](../governance/policy/samples/cmmc-l3.md)
- [Durch New Zealand ISM eingeschränkt](../governance/policy/samples/new-zealand-ism.md)

Sie können diese Standards Ihren Abonnements zuweisen, wie unter [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md) beschrieben.

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Drei Standards für die Verwendung mit dem Azure Security Center-Dashboard zur Einhaltung gesetzlicher Bestimmungen wurden hinzugefügt." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

Weitere Informationen finden Sie hier:
- [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md)
- [Tutorial: Verbessern der Einhaltung gesetzlicher Vorschriften](regulatory-compliance-dashboard.md)
- [Häufig gestellte Fragen: Dashboard für die Einhaltung gesetzlicher Bestimmungen](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>Vier neue Empfehlungen im Zusammenhang mit der Gastkonfiguration (Vorschau)

Die [Erweiterung für Gastkonfigurationen](../governance/policy/concepts/guest-configuration.md) von Azure sendet Meldungen an Security Center, um sicherzustellen, dass die In-Guest-Einstellungen Ihrer virtuellen Computer festgeschrieben werden. Bei Arc-fähigen Servern wird die Erweiterung nicht benötigt, da sie bereits im Arc Connected Machine-Agent enthalten ist. Die Erweiterung erfordert eine vom System verwaltete Identität auf dem Computer.

Wir haben vier neue Empfehlungen zum Security Center hinzugefügt, damit Sie diese Erweiterung optimal nutzen können.

- In zwei Empfehlungen werden Sie aufgefordert, die Erweiterung und die erforderliche vom System verwaltete Identität zu installieren:
    - **Die Erweiterung „Gastkonfiguration“ muss auf Ihren Computern installiert sein.**
    - **VM-Erweiterung „Gastkonfiguration“ muss mit einer systemseitig zugewiesenen verwalteten Identität bereitgestellt werden**

- Wenn die Erweiterung installiert ist und ausgeführt wird, beginnt Sie mit der Überprüfung ihrer Computer, und Sie werden aufgefordert, Einstellungen wie die Konfiguration der Betriebssystem- und Umgebungseinstellungen festzuschreiben. Mit diesen beiden Empfehlungen werden Sie dazu aufgefordert, Ihre Windows-und Linux-Computer wie beschrieben festzuschreiben:
    - **Windows Defender Exploit Guard muss auf Ihren Computern aktiviert sein**
    - **Für die Authentifizierung bei Linux-Computern muss ein SSH-Schlüssel erforderlich sein**

Weitere Informationen finden Sie in [Grundlegendes zur Gastkonfiguration von Azure Policy](../governance/policy/concepts/guest-configuration.md).

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK-Empfehlungen wurden in bewährte Methoden für die Sicherheitskontrolle verschoben

Das Sicherheitsprogramm jeder Organisation enthält Anforderungen an die Datenverschlüsselung. Die Daten von Azure-Kunden werden im Ruhezustand standardmäßig mit dienstseitig verwalteten Schlüsseln verschlüsselt. Kundenseitig verwaltete Schlüssel (CMK) sind aber häufig zur Einhaltung gesetzlicher Bestimmungen erforderlich. Mit CMKs können Sie Ihre Daten mit einem [Azure Key Vault](../key-vault/general/overview.md)-Schlüssel verschlüsseln, der von Ihnen erstellt wird und sich in Ihrem Besitz befindet. So haben Sie die volle Kontrolle über den Schlüssellebenszyklus, einschließlich der Rotation und Verwaltung, und sind dafür entsprechend verantwortlich.

Bei Sicherheitskontrollen von Azure Security Center handelt es sich um logische Gruppen mit verwandten Sicherheitsempfehlungen, die Ihren anfälligen Angriffsflächen entsprechen. Jede Sicherheitskontrolle verfügt über eine maximale Anzahl von Punkten, die Ihrer Sicherheitsbewertung hinzugefügt wird, wenn Sie alle in der Sicherheitskontrolle aufgeführten Empfehlungen für Ihre gesamten Ressourcen umsetzen. Die Sicherheitskontrolle **Best Practices für die Sicherheit implementieren** hat einen Wert von null Punkten. Daher wirken sich die Empfehlungen dieser Sicherheitskontrolle nicht auf Ihre Sicherheitsbewertung aus.

Die unten angegebenen Empfehlungen werden in die Sicherheitskontrolle **Best Practices für die Sicherheit implementieren** verschoben, um besser zu verdeutlichen, dass sie optional sind. Durch die Verschiebung wird sichergestellt, dass sich diese Empfehlungen in der Sicherheitskontrolle befinden, die zur Erreichung des Ziels am besten geeignet ist.

- Azure Cosmos DB-Konten sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.
- Azure Machine Learning-Arbeitsbereiche müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden
- Cognitive Services-Konten müssen eine Datenverschlüsselung mit einem kundenseitig verwalteten Schlüssel (CMK) aktivieren
- Containerregistrierungen müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden
- SQL Managed Instance-Instanzen müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden
- SQL Server-Instanzen müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden
- Speicherkonten sollten einen kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) zur Verschlüsselung verwenden.

Informationen dazu, welche Empfehlungen in den einzelnen Sicherheitssteuerungen enthalten sind, finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="11-azure-defender-alerts-deprecated"></a>Elf Azure Defender-Warnungen als veraltet eingestuft

Die 11 nachfolgend aufgeführten Azure Defender-Warnungen wurden als veraltet eingestuft.

- Neue Warnungen werden diese beiden Warnungen ersetzt und für eine bessere Abdeckung sorgen:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW – MicroBurst toolkit „Get-AzureDomainInfo“ function run detected (VORSCHAU – Ausführung der MicroBurst-Toolkitfunktion „Get-AzureDomainInfo“ erkannt) |
    | ARM_MicroBurstRunbook    | PREVIEW – MicroBurst toolkit „Get-AzurePasswords“ function run detected (VORSCHAU – Ausführung der MicroBurst-Funktion „Get-AzurePasswords“ erkannt)  |
    |                          |                                                                          |

- Die folgenden neun Warnungen beziehen sich auf einen Azure Active Directory Identity Protection-Connector (IPC), der bereits veraltet ist:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Ungewöhnliche Anmeldeeigenschaften |
    | AnonymousLogin      | Anonyme IP-Adresse          |
    | InfectedDeviceLogin | Mit Schadsoftware verknüpfte IP-Adresse     |
    | ImpossibleTravel    | Ungewöhnlicher Ortswechsel               |
    | MaliciousIP         | Schädliche IP-Adresse          |
    | LeakedCredentials   | Kompromittierte Anmeldeinformationen            |
    | PasswordSpray       | Kennwortspray                |
    | LeakedCredentials   | Azure AD Threat Intelligence  |
    | AADAI               | Azure AD-KI                   |
    |                     |                               |
 
    > [!TIP]
    > Bei diesen neun IPC-Warnungen hat es sich niemals um Security Center-Warnungen gehandelt. Sie sind Teil des Azure Active Directory (AAD) Identity Protection Connector (IPC), der Sie an Security Center gesendet hat. In den letzten zwei Jahren haben nur solche Kunden diese Warnungen gesehen, die den Export (vom Connector zu ASC) im Jahr 2019 oder früher konfiguriert haben. AAD IPC hat Sie weiterhin in den eigenen Warnsystemen angezeigt und waren weiterhin in Azure Sentinel verfügbar. Die einzige Veränderung besteht darin, dass sie nicht mehr in Security Center angezeigt werden.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft 

Die folgenden beiden Empfehlungen wurden als veraltet eingestuft und die Änderungen können geringfügige Auswirkungen auf Ihre Sicherheitsbewertung haben:

- **Ihre Computer sollten zur Anwendung von Systemupdates neu gestartet werden**
- **Der Überwachungs-Agent sollte auf Ihren Computern installiert werden.** Diese Empfehlung gilt nur für lokale Computer. Ein Teil der Logik wird in eine andere Empfehlung übertragen: **Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden**

Wir empfehlen Ihnen, Ihre Konfigurationen für den fortlaufenden Export und die Workflowautomatisierung zu überprüfen, um zu ermitteln, ob diese Empfehlungen darin enthalten sind. Darüber hinaus sollten Sie alle Dashboards oder anderen Überwachungstools, für die diese ggf. genutzt werden, entsprechend aktualisieren.

Weitere Informationen zu diesen Empfehlungen finden Sie auf der [Referenzseite zu Sicherheitsempfehlungen](recommendations-reference.md).

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Kachel für Azure Defender für SQL auf Computern vom Azure Defender-Dashboard entfernt

Der Abdeckungsbereich des Azure Defender-Dashboards enthält Kacheln für die relevanten Azure Defender-Pläne für Ihre Umgebung. Aufgrund eines Problems mit der Meldung der Anzahl geschützter und nicht geschützter Ressourcen haben wir uns entschieden, den Ressourcenabdeckungsstatus für **Azure Defender für SQL auf Computern** vorübergehend zu entfernen, bis das Problem behoben ist.


### <a name="21-recommendations-moved-between-security-controls"></a>21 Empfehlungen zwischen Sicherheitskontrollen verschoben 

Die folgenden Empfehlungen wurden in andere Sicherheitskontrollen verschoben. Bei Sicherheitskontrollen handelt es sich um logische Gruppen verwandter Sicherheitsempfehlungen, die anfällige Angriffsflächen widerspiegeln. Durch die Verschiebung wird sichergestellt, dass sich jede dieser Empfehlungen in der am besten geeigneten Kontrolle befindet, um das jeweilige Ziel zu erreichen.

Informationen dazu, welche Empfehlungen in den einzelnen Sicherheitssteuerungen enthalten sind, finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).

|Empfehlung |Änderung und Auswirkung  |
|---------|---------|
|Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.<br>Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren<br>Sicherheitsrisiken in Ihren SQL-Datenbanken müssen entschärft werden (neu)<br>Die Sicherheitsrisiken für Ihre SQL-Datenbanken in VMs müssen entschärft werden.     |Verschiebung aus „Sicherheitsrisiken entschärfen“ (Wert: sechs Punkte)<br>in „Sicherheitskonfigurationen bereinigen“ (Wert: vier Punkte).<br>Diese Empfehlungen haben je nach Umgebung eine geringere Auswirkung auf Ihre Bewertung.|
|Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.<br>Automation-Kontovariablen sollten verschlüsselt werden.<br> IoT-Geräte: Überwachter Prozess hat das Senden von Ereignissen beendet<br> IoT-Geräte: Fehler bei Validierung von Baseline von Betriebssystem<br> IoT-Geräte:Upgrade der TLS-Verschlüsselungssammlung erforderlich<br> IoT-Geräte: offene Ports auf Gerät<br> IoT-Geräte: In einer der Ketten wurde eine zu wenig einschränkende Firewallrichtlinie gefunden<br> IoT-Geräte: In der Eingabekette wurde eine zu wenig einschränkende Firewallregel gefunden<br> IoT-Geräte: In der Ausgabekette wurde eine zu wenig einschränkende Firewallregel gefunden<br>In IoT Hub sollten Diagnoseprotokolle aktiviert sein.<br> IoT-Geräte: Agent sendet Nachrichten zu Unterauslastung<br>IoT-Geräte: Die Standard-IP-Filterrichtlinie sollte auf „Verweigern“ festgelegt werden<br>IoT-Geräte: Großer IP-Adressbereich für IP-Filterregel<br>IoT-Geräte: Agent-Nachrichtenintervalle und -größe müssen angepasst werden<br>IoT-Geräte: Identische Anmeldeinformationen für die Authentifizierung<br>IoT-Geräte – Auditd-Prozess hat das Senden von Ereignissen beendet<br>IoT-Geräte: Baselinekonfiguration des Betriebssystems (OS) muss korrigiert werden|Verschiebung in **Bewährte Sicherheitsmethoden implementieren**.<br>Wenn eine Empfehlung in die Sicherheitskontrolle „Bewährte Sicherheitsmethoden implementieren“ (Wert: null Punkte) verschoben wird, wirkt sie sich nicht mehr auf Ihre Sicherheitsbewertung aus.|
|||


## <a name="march-2021"></a>März 2021

Zu den Updates im März gehören:

- [In Security Center integrierte Azure Firewall-Verwaltung](#azure-firewall-management-integrated-into-security-center)
- [SQL-Sicherheitsrisikobewertung enthält jetzt die Option „Regel deaktivieren“ (Vorschauversion)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [In Security Center integrierte Azure Monitor-Arbeitsmappen und drei verfügbare Vorlagen](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Dashboard für die Einhaltung gesetzlicher Bestimmungen enthält jetzt Azure-Überwachungsberichte (Vorschauversion)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Empfehlungsdaten können mit „In ARG untersuchen“ in Azure Resource Graph angezeigt werden](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Updates der Richtlinien für die Bereitstellung der Workflowautomatisierung](#updates-to-the-policies-for-deploying-workflow-automation)
- [Von zwei Legacyempfehlungen werden keine Daten mehr direkt in das Azure-Aktivitätsprotokoll geschrieben](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Verbesserungen der Seite „Empfehlungen“](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>In Security Center integrierte Azure Firewall-Verwaltung

Wenn Sie Azure Security Center öffnen, wird als Erstes die Übersichtsseite angezeigt. 

Dieses interaktive Dashboard ermöglicht eine einheitliche Übersicht über den Sicherheitsstatus Ihrer Hybrid Cloud-Workloads. Darüber hinaus werden darauf Sicherheitswarnungen, Informationen zur Abdeckung und andere Infos angezeigt.

Um Sie beim Anzeigen Ihres Sicherheitsstatus über eine zentrale Benutzeroberfläche zu unterstützen, haben wir u. a. Azure Firewall Manager in dieses Dashboard integriert. Sie können den Firewall-Abdeckungsstatus jetzt für alle Netzwerke überprüfen und über Security Center die Azure Firewall-Richtlinien an einem zentralen Ort verwalten.

Weitere Informationen zu diesem Dashboard finden Sie auf der [Übersichtsseite für Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Übersichtsdashboard von Security Center mit einer Kachel für Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL-Sicherheitsrisikobewertung enthält jetzt die Option „Regel deaktivieren“ (Vorschauversion)

Security Center enthält eine integrierte Überprüfung auf Sicherheitsrisiken, mit der Sie potenzielle Sicherheitsrisiken für Datenbanken ermitteln, nachverfolgen und beseitigen können. Die Ergebnisse Ihrer Überprüfungen für die Bewertung ermöglichen einen Überblick über den Sicherheitszustand Ihrer SQL-Computer und enthalten Einzelheiten zu allen Sicherheitsergebnissen.

Wenn in Ihrer Organisation eine Suche ignoriert werden muss, anstatt sie zu beheben, können Sie sie optional deaktivieren. Deaktivierte Ergebnisse haben keine Auswirkung auf Ihre Sicherheitsbewertung und erzeugen kein unerwünschtes Rauschen.

Weitere Informationen finden Sie unter [Deaktivieren bestimmter Ergebnisse](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>In Security Center integrierte Azure Monitor-Arbeitsmappen und drei verfügbare Vorlagen

Bei der Ignite im Frühjahr 2021 haben wir eine integrierte Benutzeroberfläche für Azure Monitor-Arbeitsmappen in Security Center angekündigt.

Sie können diese neue Integration verwenden, um mit der Verwendung der vordefinierten Vorlagen aus dem Security Center-Katalog zu beginnen. Mit den Arbeitsmappenvorlagen können Sie auf dynamische und grafische Berichte zugreifen bzw. diese erstellen, um den Sicherheitsstatus Ihrer Organisation nachzuverfolgen. Darüber hinaus können Sie auch neue Arbeitsmappen, die auf Security Center-Daten basieren, oder alle anderen unterstützten Datentypen erstellen und in kurzer Zeit Community-Arbeitsmappen aus der GitHub-Community für Security Center bereitstellen.

Es sind drei Vorlagenberichte verfügbar:

- **Sicherheitsbewertung im Zeitverlauf**: Nutzen Sie die Nachverfolgung der Bewertungen Ihrer Abonnements und der Änderungen von Empfehlungen für Ihre Ressourcen.
- **Systemupdates**: Zeigen Sie fehlende Systemupdates nach Ressource, Betriebssystem, Schweregrad usw. an.
- **Ergebnisse der Sicherheitsrisikobewertung**: Zeigen Sie die Ergebnisse der Sicherheitsrisikobewertungen Ihrer Azure-Ressourcen an.

Informieren Sie sich über die Nutzung dieser Berichte oder die Erstellung eigener Berichte unter [Erstellen umfassender interaktiver Berichte für Security Center-Daten](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Bericht „Sicherheitsbewertung im Zeitverlauf“":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Dashboard für die Einhaltung gesetzlicher Bestimmungen enthält jetzt Azure-Überwachungsberichte (Vorschauversion)

In der Symbolleiste im Dashboard für die Einhaltung gesetzlicher Bestimmungen können Sie jetzt Zertifizierungsberichte für Azure und Dynamics herunterladen. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Symbolleiste des Dashboards für die Einhaltung gesetzlicher Bestimmungen":::

Sie können die Registerkarte für die relevanten Berichtstypen (PCI, SOC, ISO usw.) auswählen und Filter verwenden, um nach den benötigten spezifischen Berichten zu suchen.

Informieren Sie sich über das [Verwalten der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtern der Liste mit den verfügbaren Azure-Überwachungsberichten":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Empfehlungsdaten können mit „In ARG untersuchen“ in Azure Resource Graph angezeigt werden

Auf den Empfehlungsdetailseiten steht die Schaltfläche „In ARG untersuchen“ zur Verfügung. Verwenden Sie diese Schaltfläche, um eine Azure Resource Graph-Abfrage zu öffnen und die Daten der Empfehlung zu erkunden, zu exportieren und weiterzugeben.

Azure Resource Graph (ARG) bietet mit zuverlässigen Funktionen zum Filtern, Gruppieren und Sortieren sofortigen Zugriff auf Ressourceninformationen in Ihren Cloudumgebungen. Es ist eine schnelle und effiziente Möglichkeit, Informationen über Azure-Abonnements programmgesteuert oder aus dem Azure-Portal heraus abzufragen.

Weitere Informationen zu Azure Resource Graph (ARG) finden Sie [hier](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Erkunden Sie Empfehlungsdaten in Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Updates der Richtlinien für die Bereitstellung der Workflowautomatisierung

Die Automatisierung der Prozesse Ihrer Organisation zur Überwachung und Reaktion auf Vorfälle kann die Zeit, die zum Untersuchen von Sicherheitsvorfällen und zur Durchführung entsprechender Gegenmaßnahmen benötigt wird, erheblich verkürzen.

Wir stellen drei Azure Policy-Richtlinien vom Typ „DeployIfNotExist“ bereit, mit denen Verfahren für die Workflowautomatisierung erstellt und konfiguriert werden, damit Sie Ihre Automatisierungen in Ihrer gesamten Organisation bereitstellen können:

|Zielsetzung  |Richtlinie  |Richtlinien-ID  |
|---------|---------|---------|
|Workflowautomatisierung für Sicherheitswarnungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Workflowautomatisierung für Sicherheitsempfehlungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Empfehlungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Workflowautomatisierung für Änderungen bei der Einhaltung gesetzlicher Bestimmungen|[Workflowautomatisierung für die Einhaltung gesetzlicher Bestimmungen in Azure Security Center bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Für die Features dieser Richtlinien wurden zwei Aktualisierungen durchgeführt:

- Wenn sie zugewiesen werden, wird erzwungen, dass sie aktiviert bleiben.
- Sie können diese Richtlinien jetzt anpassen und alle Parameter auch nach der Bereitstellung noch aktualisieren. Wenn ein Benutzer beispielsweise einen weiteren Bewertungsschlüssel hinzufügen oder einen vorhandenen Bewertungsschlüssel bearbeiten möchte, ist dies möglich.

Beginnen Sie mit [Vorlagen zur Workflowautomatisierung](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Informieren Sie sich über das [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Von zwei Legacyempfehlungen werden keine Daten mehr direkt in das Azure-Aktivitätsprotokoll geschrieben 

Von Security Center werden die Daten für nahezu alle Sicherheitsempfehlungen an Azure Advisor übergeben und anschließend von Azure Advisor in das [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) geschrieben.

Bei zwei Empfehlungen werden die Daten gleichzeitig direkt in das Azure-Aktivitätsprotokoll geschrieben. Diese Änderung sorgt dafür, dass Daten für diese Legacysicherheitsempfehlungen von Security Center nicht mehr direkt in das Aktivitätsprotokoll geschrieben werden. Stattdessen werden die Daten genau wie bei allen anderen Empfehlungen nach Azure Advisor exportiert.

Die beiden Legacyempfehlungen sind:
-  Integritätsprobleme in Endpoint Protection sollten auf Ihren Computern behoben werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.

Wenn Sie auf Informationen für diese beiden Empfehlungen in der Kategorie „Empfehlung vom Typ "TaskDiscovery"“ zugegriffen haben, ist dies nicht mehr möglich.


### <a name="recommendations-page-enhancements"></a>Verbesserungen der Seite „Empfehlungen“ 

Wir haben eine verbesserte Version der Empfehlungsliste veröffentlicht, um mehr Informationen auf einen Blick zu präsentieren.

Auf der Seite sehen Sie nun Folgendes:

1. Die maximale Bewertung und die aktuelle Bewertung für jede Sicherheitskontrolle
1. Symbole, die Tags ersetzen, etwa **Fix** und **Vorschau**
1. Eine neue Spalte mit der [Richtlinieninitiative](security-policy-concept.md) für jede Empfehlung (sichtbar, wenn „Nach Kontrollen gruppieren“ deaktiviert ist)

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Verbesserungen der Seite „Empfehlungen“ für Azure Security Center: März 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Verbesserungen der flachen Liste „Empfehlungen“ für Azure Security Center: März 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Weitere Informationen finden Sie unter [Sicherheitsempfehlungen in Azure Security Center](review-security-recommendations.md).

## <a name="february-2021"></a>Februar 2021

Updates im Februar:

- [Neue Seite „Sicherheitswarnungen“ im Azure-Portal nun allgemein verfügbar](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Empfehlungen zum Schutz von Kubernetes-Workloads nun allgemein verfügbar](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender für Endpunkt-Integration mit Azure Defender unterstützt jetzt Windows Server 2019 und Windows 10 Virtual Desktop (WVD) (Vorschau)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Direkter Link zur Richtlinie auf der Seite mit den Empfehlungsdetails](#direct-link-to-policy-from-recommendation-details-page)
- [Empfehlung zur SQL-Datenklassifizierung hat keine Auswirkung auf Ihre Sicherheitsbewertung mehr](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst werden (Vorschau)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Erweiterungen für die Seite „Ressourcenbestand“](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Neue Seite „Sicherheitswarnungen“ im Azure-Portal nun allgemein verfügbar

Die Seite „Sicherheitswarnungen“ von Azure Security Center wurde neu gestaltet. Sie bietet jetzt Folgendes:

- **Eine verbesserte Selektierungsoberfläche für Warnungen**: Die Liste enthält anpassbare Filter und Gruppierungsoptionen und trägt dadurch zur Reduzierung der „Warnungsmüdigkeit“ bei, sodass Sie sich einfacher auf die relevantesten Bedrohungen konzentrieren können.
- **Weitere Informationen in der Warnungsliste**, beispielsweise MITRE ATT&CK-Taktiken
- **Schaltfläche zum Erstellen von Beispielwarnungen**: Zum Evaluieren von Azure Defender-Funktionen und Testen Ihrer Warnungen. Sie können Beispielwarnungen aus allen Azure Defender-Plänen erstellen (Konfiguration für SIEM-Integration, E-Mail-Benachrichtigungen und Workflowautomatisierungen).
- **Angleichung an die Azure Sentinel-Incidentoberfläche**: Für Kunden, die beide Produkte verwenden, ist der Wechsel zwischen den Produkten jetzt unkomplizierter, und es ist einfach, das eine vom anderen zu übernehmen.
- **Bessere Leistung** für große Warnungslisten
- **Tastaturnavigation** durch die Warnungsliste
- **Warnungen aus Azure Resource Graph**: Sie können Warnungen in Azure Resource Graph abfragen, der Kusto-ähnlichen API für alle Ihre Ressourcen. Dies ist auch nützlich, wenn Sie eigene Warnungsdashboards erstellen. [Erfahren Sie mehr über Azure Resource Graph](../governance/resource-graph/index.yml).
- **Feature zum Erstellen von Beispielwarnungen**: Informationen zum Erstellen von Beispielwarnungen über die neue Oberfläche für Warnungen finden Sie unter [Generieren von Azure Defender-Beispielwarnungen](alert-validation.md#generate-sample-security-alerts).

:::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Liste der Sicherheitswarnungen von Azure Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Empfehlungen zum Schutz von Kubernetes-Workloads nun allgemein verfügbar

Wir freuen uns, ankündigen zu können, dass die Empfehlungen für den Schutz von Kubernetes-Workloads jetzt allgemein verfügbar sind.

Um sicherzustellen, dass Kubernetes-Workloads standardmäßig sicher sind, wurden über Security Center Härtungsempfehlungen auf Kubernetes-Ebene hinzugefügt, z. B. auch Erzwingungsoptionen mit Kubernetes-Zugangskontrolle.

Wenn das Azure Policy-Add-On für Kubernetes in Ihrem AKS-Cluster (Azure Kubernetes Service) installiert ist, wird jede Anforderung an den Kubernetes-API-Server anhand der vordefinierten bewährten Methoden (Anzeige in Form von 13 Sicherheitsempfehlungen) überwacht, bevor sie im Cluster gespeichert wird. Anschließend können Sie das Erzwingen der bewährten Methoden konfigurieren und auf zukünftige Workloads anwenden.

Beispielsweise können Sie vorschreiben, dass keine privilegierten Container erstellt werden sollen und dass zukünftige Anforderungen für diese Aktion blockiert werden sollen.

Weitere Informationen finden Sie unter [Bewährte Methoden zum Schutz von Workloads mithilfe der Kubernetes-Zugangssteuerung](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Während sich die Empfehlungen in der Vorschauphase befunden haben, haben diese nicht dazu geführt, dass eine AKS-Clusterressource als fehlerhaft gekennzeichnet wurde, und sind nicht in die Berechnung Ihrer Sicherheitsbewertung eingegangen. Ab dem Zeitpunkt dieser Ankündigung zur allgemeinen Verfügbarkeit wirken sie sich aber auf die Bewertungsberechnung aus. Falls Sie sie noch nicht umgesetzt haben, kann sich dies geringfügig auf Ihre Sicherheitsbewertung auswirken. Setzen Sie sie nach Möglichkeit um, wie dies unter [Umsetzen von Empfehlungen in Azure Security Center](implement-security-recommendations.md) beschrieben ist.


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Microsoft Defender für Endpunkt-Integration mit Azure Defender unterstützt jetzt Windows Server 2019 und Windows 10 Virtual Desktop (WVD) (Vorschau)

Microsoft Defender für den Endpunkt ist eine ganzheitliche, cloudbasierte Lösung für die Endpunktsicherheit. Sie ermöglicht die risikobasierte Verwaltung und Bewertung von Sicherheitsrisiken sowie Endpunkterkennung und -reaktion (Endpoint Detection and Response, EDR). Eine vollständige Liste mit den Vorteilen der gemeinsamen Nutzung von Defender für Endpunkt und Azure Security Center finden Sie unter [Schützen Sie Ihre Endpunkte mit der in Security Center integrierten EDR-Lösung: Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md).

Wenn Sie Azure Defender für Server auf einem Windows-Server aktivieren, ist im Plan eine Lizenz für Defender für Endpunkt enthalten. Falls Sie Azure Defender für Server bereits aktiviert haben und unter Ihrem Abonnement Windows 2019-Server nutzen, wird Defender für Endpunkt mit diesem Update darauf automatisch bereitgestellt. Es ist keine manuelle Aktion erforderlich. 

Die Unterstützung wurde nun auf Windows Server 2019 und [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) erweitert.

> [!NOTE]
> Stellen Sie beim Aktivieren von Defender für Endpunkt auf einem Windows Server 2019-Computer sicher, dass die unter [Aktivieren der Integration von Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md#enable-the-microsoft-defender-for-endpoint-integration) beschriebenen Voraussetzungen erfüllt sind.

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Direkter Link zur Richtlinie auf der Seite mit den Empfehlungsdetails

Wenn Sie die Details einer Empfehlung überprüfen, ist es häufig hilfreich, die zugrunde liegende Richtlinie zu kennen. Für jede Empfehlung, die von einer Richtlinie unterstützt wird, enthält die Seite mit den Empfehlungsdetails einen neuen Link:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link zur Azure Policy-Seite für eine bestimmte Richtlinie, die eine Empfehlung unterstützt":::

Verwenden Sie diesen Link, um die Richtliniendefinition anzuzeigen und die Bewertungslogik zu überprüfen. 

In der Liste mit den Empfehlungen in unserem [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) finden Sie auch Links zu den Seiten mit Richtliniendefinitionen:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Zugriff auf die Azure Policy-Seite für eine bestimmte Richtlinie direkt über die Seite mit der Azure Security Center-Empfehlungsreferenz" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Empfehlung zur SQL-Datenklassifizierung hat keine Auswirkung auf Ihre Sicherheitsbewertung mehr
Die Empfehlung **Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden** hat keine Auswirkung auf Ihre Sicherheitsbewertung mehr. Da dies die einzige Empfehlung unter der Sicherheitskontrolle **Datenklassifizierung anwenden** ist, gilt für diese Kontrolle in Bezug auf die Sicherheitsbewertung jetzt der Wert „0“.

Eine vollständige Liste mit allen Sicherheitskontrollen in Security Center und die zugehörigen Bewertungen und Empfehlungen finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst werden (Vorschau)
Wir haben den Triggeroptionen für Ihre Workflowautomatisierungen jetzt einen dritten Datentyp hinzugefügt: Änderungen an Bewertungen zur Einhaltung gesetzlicher Bestimmungen.

Informieren Sie sich unter [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md) über die Nutzung der Tools für die Workflowautomatisierung.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Verwenden von Änderungen bei Bewertungen der Einhaltung gesetzlicher Bestimmungen zum Auslösen der Workflowautomatisierung" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Erweiterungen für die Seite „Ressourcenbestand“
Die Security Center-Seite „Ressourcenbestand“ wurde wie folgt verbessert:

- Zusammenfassungen am oberen Rand der Seite enthalten jetzt **Nicht registrierte Abonnements** und geben Aufschluss über die Anzahl von Abonnements ohne Security Center-Aktivierung.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Anzahl nicht registrierter Abonnements in den Zusammenfassungen am oberen Rand der Seite „Ressourcenbestand“":::

- Filter wurden erweitert und verbessert, um Folgendes einzuschließen:
    - **Anzahl**: Für die Filter wird jeweils die Anzahl von Ressourcen angezeigt, die die Kriterien der jeweiligen Kategorie erfüllen.

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Anzahlangabe in den Filtern der Seite „Ressourcenbestand“ von Azure Security Center":::

    - **Ausnahmenfilter** (optional): Ermöglicht das Eingrenzen der Ergebnisse auf Ressourcen mit bzw. ohne Ausnahmen. Dieser Filter wird standardmäßig nicht angezeigt, ist aber über die Schaltfläche **Filter hinzufügen** verfügbar.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Hinzufügen des Ausnahmenfilters auf der Seite „Ressourcenbestand“ von Azure Security Center":::

Weitere Informationen zum Erkunden und Verwalten Ihrer Ressourcen mithilfe des Ressourcenbestands und finden Sie [hier](asset-inventory.md).



## <a name="january-2021"></a>Januar 2021

Die Updates im Januar umfassen Folgendes:

- [Der Azure-Sicherheitsvergleichstest ist jetzt die Standardrichtlinieninitiative für Azure Security Center.](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Die Sicherheitsrisikobewertung für lokale Computer und Multi-Cloud-Computer ist jetzt allgemein verfügbar.](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Die Sicherheitsbewertung für Verwaltungsgruppen ist jetzt als Vorschau verfügbar.](#secure-score-for-management-groups-is-now-available-in-preview)
- [Die Sicherheitsbewertungs-API ist jetzt allgemein verfügbar.](#secure-score-api-is-released-for-general-availability-ga)
- [Azure Defender für App Service wurde Schutz vor verwaisten DNS-Einträgen hinzugefügt.](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Multi-Cloud-Connectors sind jetzt allgemein verfügbar.](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Möglichkeit, bei Ihrer Sicherheitsbewertung für Abonnements und Verwaltungsgruppen ganze Empfehlungen auszunehmen](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Benutzer können beim globalen Administrator jetzt mandantenweite Sichtbarkeit anfordern.](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 Vorschauempfehlungen werden hinzugefügt, um die Abdeckung des Azure-Sicherheitsvergleichstests zu erhöhen](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [CSV-Export der gefilterten Liste mit Empfehlungen](#csv-export-of-filtered-list-of-recommendations)
- [Nicht anwendbare Ressourcen werden in Azure Policy-Bewertungen jetzt als „Konform“ gemeldet.](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportieren wöchentlicher Momentaufnahmen der Daten für die Sicherheitsbewertung und die Einhaltung gesetzlicher Bestimmungen per fortlaufendem Export (Vorschau)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Der Azure-Sicherheitsvergleichstest ist jetzt die Standardrichtlinieninitiative für Azure Security Center.

Beim Azure-Sicherheitsvergleichstest handelt es sich um einen von Microsoft erstellten Satz Azure-spezifischer Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.

In den letzten Monaten wurde die Liste der integrierten Sicherheitsempfehlungen von Security Center erheblich erweitert, um unsere Abdeckung dieser Benchmark auszudehnen.

Ab diesem Release bildet die Benchmark die Grundlage für Security Center-Empfehlungen und ist vollständig als Standardrichtlinieninitiative integriert. 

Die Dokumentation jedes Azure-Diensts enthält eine Seite mit der Sicherheitsbaseline. Diese Baselines basieren auf dem Vergleichstest für die Azure-Sicherheit.

Auf dem Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen werden während eines Übergangszeitraums zwei Instanzen der Benchmark angezeigt:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen mit dem Vergleichstest für die Azure-Sicherheit":::

Auf bereits vorhandene Empfehlungen hat dies keine Auswirkungen, und im Zuge des weiteren Ausbaus der Benchmark werden Änderungen automatisch in Security Center berücksichtigt. 

Weitere Informationen finden Sie auf den folgenden Seiten:

- [Weitere Informationen zum Vergleichstest für die Azure-Sicherheit](/security/benchmark/azure/introduction)
- [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Die Sicherheitsrisikobewertung für lokale Computer und Multi-Cloud-Computer ist jetzt allgemein verfügbar.

Im Oktober haben wir eine Vorschauversion für die Überprüfung von Azure Arc-fähigen Servern mit des Scanners für die Sicherheitsrisikobewertung (von Qualys) angekündigt, die in [Azure Defender für Server](defender-for-servers-introduction.md) integriert ist.

Dieses Feature ist jetzt allgemein verfügbar.

Wenn Sie Azure Arc auf Ihren Nicht-Azure-Computern aktiviert haben, bietet Security Center eine manuelle und skalierbare Bereitstellung des integrierten Sicherheitsrisikoscanners auf diesen Computern.

Mit diesem Update können Sie die Leistungsfähigkeit von **Azure Defender für Server** nutzen, um Ihr Programm zur Verwaltung von Sicherheitsrisiken auf allen Azure- und Nicht-Azure-Ressourcen zu konsolidieren.

Hauptfunktionen:

- Überwachen des Bereitstellungsstatus des Scanners für die Sicherheitsrisikobewertung auf Azure Arc-Computern
- Bereitstellen des integrierten Agents für die Sicherheitsrisikobewertung auf ungeschützten Azure Arc-Computern unter Windows und Linux (manuell und skalierbar)
- Empfangen und Analysieren ermittelter Sicherheitsrisiken von bereitgestellten Agents (manuell und skalierbar)
- Einheitliche Benutzeroberfläche für Azure-VMs und Azure Arc-Computer

[Erfahren Sie mehr über das Bereitstellen der integrierten Qualys-Überprüfung auf Sicherheitsrisiken für Ihre Hybridcomputer](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Erfahren Sie mehr über Server mit Azure Arc-Unterstützung](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Die Sicherheitsbewertung für Verwaltungsgruppen ist jetzt als Vorschau verfügbar.

Zusätzlich zur Abonnementebene werden auf der Seite „Sicherheitsbewertung“ nun auch die aggregierten Sicherheitsbewertungen für Ihre Verwaltungsgruppen angezeigt. Nun können Sie sich also die Liste der Verwaltungsgruppen in Ihrer Organisation sowie die Bewertung für die jeweilige Verwaltungsgruppe ansehen.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Anzeigen der Sicherheitsbewertungen für Ihre Verwaltungsgruppen":::

Weitere Informationen finden Sie unter [Erweiterter Secure Score (Vorschau) in Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Die Sicherheitsbewertungs-API ist jetzt allgemein verfügbar.

Sie können jetzt über die [Sicherheitsbewertungs-API](/rest/api/securitycenter/securescores/) auf Ihre Bewertung zugreifen. Die API-Methoden bieten die Flexibilität, die Daten abzufragen und im Laufe der Zeit einen eigenen Berichtsmechanismus für Ihre Sicherheitsbewertungen zu erstellen. Beispiel:

- Verwenden Sie die API für **Sicherheitsbewertungen**, um die Bewertung für ein bestimmtes Abonnement zu erhalten.
- Verwenden Sie die API für **Sicherheitsbewertungs-Steuerelemente**, um die Sicherheitssteuerelemente und die aktuelle Bewertung Ihrer Abonnements aufzulisten.

Informieren Sie sich im [Bereich zu den Sicherheitsbewertungen in unserer GitHub-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) über die externen Tools, die mit der Sicherheitsbewertungs-API verwendet werden können.

Weitere Informationen finden Sie unter [Erweiterter Secure Score (Vorschau) in Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Azure Defender für App Service wurde Schutz vor verwaisten DNS-Einträgen hinzugefügt.

Unterdomänenübernahmen sind eine weit verbreitete Bedrohung mit hohem Schweregrad für Organisationen. Eine Unterdomänenübernahme ist möglich, wenn Sie über einen DNS-Eintrag verfügen, der auf Website verweist, deren Bereitstellung aufgehoben wurde. Solche DNS-Einträge werden auch als „verwaiste DNS“-Einträge bezeichnet. CNAME-Einträge sind besonders anfällig für diese Bedrohung. 

Durch die Übernahme von Unterdomänen können Bedrohungsakteure Datenverkehr, der für die Domäne eines Unternehmens bestimmt ist, an eine Website für schädliche Aktivitäten umleiten.

Von Azure Defender für App Service werden nun verwaiste DNS-Einträge erkannt, wenn eine App Service-Website eingestellt wird. Dies ist der Zeitpunkt, zu dem der DNS-Eintrag auf eine nicht vorhandene Ressource verweist und Ihre Website für eine Unterdomänenübernahme anfällig ist. Diese Schutzmaßnahmen sind unabhängig davon verfügbar, ob Ihre Domänen mit Azure DNS oder mit einer externen Domänenregistrierungsstelle verwaltet werden, und sie gelten sowohl für App Service unter Windows als auch für App Service unter Linux.

Weitere Informationen:

- Referenztabelle [Warnungen für Azure App Service](alerts-reference.md#alerts-azureappserv): Enthält zwei neue Azure Defender-Warnungen, die ausgelöst werden, wenn ein verwaister DNS-Eintrag erkannt wird.
- [Verhindern verwaister DNS-Einträge und Vermeiden von Unterdomänenübernahmen](../security/fundamentals/subdomain-takeover.md): Hier finden Sie Informationen zur Gefahr von Unterdomänenübernahmen sowie zu verwaisten DNS-Einträgen.
- [Einführung in Azure Defender für App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Multi-Cloud-Connectors sind jetzt allgemein verfügbar.

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Azure Security Center schützt Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Wenn Sie eine Verbindung mit Ihren AWS- oder GCP-Konten herstellen, werden ihre nativen Sicherheitstools wie AWS Security Hub und GCP Security Command Center in Azure Security Center integriert.

Das bedeutet, dass Security Center Transparenz und Schutz für alle gängigen Cloudumgebungen bereitstellt. Diese Integration hat unter anderem folgende Vorteile:

- Automatische Agent-Bereitstellung: Security Center verwendet Azure Arc, um den Log Analytics-Agent für Ihre AWS-Instanzen bereitzustellen.
- Richtlinienverwaltung
- Verwaltung von Sicherheitsrisiken
- Integrierte Endpunkterkennung und -antwort (Endpoint Detection and Response, EDR)
- Erkennung von Sicherheitsfehlkonfigurationen
- Eine zentrale Ansicht mit Sicherheitsempfehlungen von allen Cloudanbietern
- Einbindung all Ihrer Ressourcen in die Berechnung von Sicherheitsbewertungen durch Security Center
- Bewertung der Einhaltung gesetzlicher Bestimmungen für Ihre AWS- und GPC-Ressourcen

Wählen Sie im Menü von Defender für Cloud die Option **Multi cloud connectors** (Connectors für mehrere Clouds) aus, um die Optionen zum Erstellen neuer Connectors anzuzeigen:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Schaltfläche zum Hinzufügen eines AWS-Kontos auf der Seite für Connectors für mehrere Clouds in Security Center":::

Weitere Informationen finden Sie hier:
- [Verbinden Ihrer AWS-Konten mit Azure Security Center](quickstart-onboard-aws.md)
- [Herstellen einer Verbindung zwischen Ihren GCP-Konten und Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Möglichkeit, bei Ihrer Sicherheitsbewertung für Abonnements und Verwaltungsgruppen ganze Empfehlungen auszunehmen

Wir erweitern die Ausnahmenfunktion um die Möglichkeit, vollständige Empfehlungen einzuschließen. Dazu stellen wir weitere Optionen für die Feinabstimmung der Sicherheitsempfehlungen zur Verfügung, die Security Center für Ihre Abonnements, Verwaltungsgruppe oder Ressourcen bereitstellt.

Gelegentlich kann es vorkommen, dass eine Ressource als fehlerhaft aufgeführt wird, obwohl Sie wissen, dass das Problem durch ein Drittanbietertool behoben und dies von Security Center nicht erkannt wurde. Auch kann es bisweilen passieren, dass eine Empfehlung in einem Bereich angezeigt wird, zu dem sie Ihrer Meinung nach nicht gehört. Möglicherweise ist die Empfehlung für ein bestimmtes Abonnement nicht geeignet. Oder vielleicht hat Ihr Unternehmen auch die Entscheidung getroffen, die Risiken im Zusammenhang mit der jeweiligen Ressource oder Empfehlung zu akzeptieren.

Mit dieser Previewfunktion können Sie jetzt eine Ausnahme für eine Empfehlung erstellen. Dabei haben Sie folgende Möglichkeiten:

- Sie können **eine Ressource ausnehmen**, um sicherzustellen, dass sie in Zukunft nicht mehr als fehlerhafte Ressourcen aufgeführt wird und keine Auswirkung auf Ihre Sicherheitsbewertung hat. Die Ressource wird als nicht anwendbar aufgeführt, und als Grund wird „Ausgenommen“ mit der spezifischen, von Ihnen ausgewählten Begründung angezeigt.

- Sie können **ein Abonnement oder eine Verwaltungsgruppe ausnehmen**, um sicherzustellen, dass die Empfehlung keine Auswirkung auf Ihre Sicherheitsbewertung hat und in Zukunft nicht mehr für das Abonnement oder die Verwaltungsgruppe angezeigt wird. Das gilt sowohl für bereits vorhandene Ressourcen als auch für alle zukünftig erstellten Ressourcen. Die Empfehlung wird mit der spezifischen Begründung gekennzeichnet, die Sie für den ausgewählten Bereich auswählen.

Weitere Informationen finden Sie unter [Ausschließen einer Ressource aus Empfehlungen und der Sicherheitsbewertung](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Benutzer können beim globalen Administrator jetzt mandantenweite Sichtbarkeit anfordern.

Wenn ein Benutzer nicht über Berechtigungen zum Anzeigen von Security Center-Daten verfügt, wird ihm jetzt ein Link angezeigt, über den er Berechtigungen vom globalen Administrator der Organisation anfordern kann. Die Anforderung enthält die gewünschte Rolle sowie eine Begründung, warum die Rolle erforderlich ist.

:::image type="content" source="media/management-groups-roles/request-tenant-permissions.png" alt-text="Banner mit der Information, dass der Benutzer mandantenweite Berechtigungen anfordern kann":::

Weitere Informationen finden Sie unter [Anfordern mandantenweiter Berechtigungen, wenn die eigenen Berechtigungen nicht ausreichen](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 Vorschauempfehlungen werden hinzugefügt, um die Abdeckung des Azure-Sicherheitsvergleichstests zu erhöhen

Der [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction) ist die Standardrichtlinieninitiative in Azure Security Center. 

Die folgenden 35 Vorschauempfehlungen wurden zu Security Center hinzugefügt, um die Abdeckung dieser Benchmark zu erhöhen.

> [!TIP]
> Empfehlungen der Vorschau versetzen keine Ressourcen in einen fehlerhaften Zustand, und sie werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Setzen Sie sie trotzdem um, wann immer möglich, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen. Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Azure Security Center](implement-security-recommendations.md).

| Sicherheitskontrolle                     | Neue Empfehlungen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivieren der Verschlüsselung ruhender Daten            | - Azure Cosmos DB-Konten sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.<br>- Azure Machine Learning-Arbeitsbereiche sollten mit einem kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) verschlüsselt werden.<br>- BYOK-Datenschutz (Bring Your Own Key) sollte für MySQL-Server aktiviert sein.<br>- BYOK-Datenschutz (Bring Your Own Key) sollte für PostgreSQL-Server aktiviert sein.<br>- Cognitive Services-Konten sollten die Datenverschlüsselung mit einem kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) aktivieren.<br>- Containerregistrierungen sollten mit einem kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) verschlüsselt werden.<br>- Verwaltete SQL-Instanzen sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.<br>- Computer mit SQL Server sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.<br>- Speicherkonten sollten einen kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) zur Verschlüsselung verwenden.                                                                                                                                                              |
| Bewährte Sicherheitsmethoden implementieren    | - In Abonnements sollte eine Kontakt-E-Mail-Adresse für Sicherheitsprobleme angegeben sein.<br> - Für Ihr Abonnement sollte die automatische Bereitstellung des Log Analytics-Agents aktiviert sein.<br> - E-Mail-Benachrichtigungen bei Warnungen mit hohem Schweregrad sollten aktiviert sein.<br> - Das Senden von E-Mail-Benachrichtigungen an den Abonnementbesitzers bei Warnungen mit hohem Schweregrad sollte aktiviert sein.<br> - Für Schlüsseltresore sollte der Löschschutz aktiviert sein.<br> - Für Schlüsseltresore sollte vorläufiges Löschen aktiviert sein. |
| Zugriff und Berechtigungen verwalten        | - Für Funktions-Apps sollte „Clientzertifikate (eingehende Clientzertifikate)“ aktiviert sein. |
| Anwendungen vor DDoS-Angriffen schützen | - Web Application Firewall (WAF) sollte für Application Gateway aktiviert sein.<br> - Web Application Firewall (WAF) sollte für Azure Front Door Service aktiviert sein. |
| Nicht autorisierten Netzwerkzugriff einschränken | - Für Key Vault sollte eine Firewall aktiviert sein.<br> - Für Key Vault sollte ein privater Endpunkt konfiguriert werden.<br> - App Configuration sollte eine private Verbindung verwenden.<br> - Azure Cache for Redis sollte sich in einem virtuellen Netzwerk befinden.<br> - Azure Event Grid-Domänen sollten eine private Verbindung verwenden.<br> - Azure Event Grid-Themen sollten eine private Verbindung verwenden.<br> - Azure Machine Learning-Arbeitsbereiche sollten eine private Verbindung verwenden.<br> - Azure SignalR Service sollte eine private Verbindung verwenden.<br> - Azure Spring Cloud sollte Netzwerkinjektion verwenden.<br> - Containerregistrierungen sollten keinen uneingeschränkten Netzwerkzugriff zulassen.<br> - Containerregistrierungen sollten eine private Verbindung verwenden.<br> - Öffentlicher Netzwerkzugriff sollte für MariaDB-Server deaktiviert sein.<br> - Öffentlicher Netzwerkzugriff sollte für MySQL-Server deaktiviert sein.<br> - Öffentlicher Netzwerkzugriff sollte für PostgreSQL-Server deaktiviert sein.<br> - Das Speicherkonto sollte eine Private Link-Verbindung verwenden.<br> - Speicherkonten sollten den Netzwerkzugriff mithilfe von VNET-Regeln einschränken.<br> - VM Image Builder-Vorlagen sollten eine private Verbindung verwenden.|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Verwandte Links:

- [Weitere Informationen zum Vergleichstest für die Azure-Sicherheit](/security/benchmark/azure/introduction)
- [Weitere Informationen zu Azure Database for MariaDB](../mariadb/overview.md)
- [Weitere Informationen zu Azure Database for MySQL](../mysql/overview.md)
- [Weitere Informationen zu Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>CSV-Export der gefilterten Liste mit Empfehlungen 

Im November 2020 haben wir der Seite mit den Empfehlungen Filter hinzugefügt ([Die Empfehlungsliste beinhaltet nun Filter](release-notes-archive.md#recommendations-list-now-includes-filters)). Im Dezember haben wir diese Filter erweitert ([Empfehlungsseite enthält neue Filter für Umgebung, Schweregrad und verfügbare Antworten](release-notes-archive.md#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Mit dieser Ankündigung wird das Verhalten der Schaltfläche für den **Download als CSV** so geändert, dass der CSV-Export nur die Empfehlungen enthält, die derzeit in der gefilterten Liste angezeigt werden. 

In der Abbildung unten ist beispielsweise erkennbar, dass die Liste nach zwei Empfehlungen gefiltert wurde. Die generierte CSV-Datei enthält die Statusdetails für jede Ressource, auf die sich diese beiden Empfehlungen auswirken.   

:::image type="content" source="media/managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportieren von gefilterten Empfehlungen als CSV-Datei":::

Weitere Informationen finden Sie unter [Sicherheitsempfehlungen in Azure Security Center](review-security-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Nicht anwendbare Ressourcen werden in Azure Policy-Bewertungen jetzt als „Konform“ gemeldet.

Bislang wurden Ressourcen, die für eine Empfehlung ausgewertet und als **nicht anwendbar** befunden wurden, in Azure Policy als „Nicht konform“ angezeigt. Ihr Zustand kann durch keine Benutzeraktion in „Konform“ geändert werden. Mit dieser Änderung werden sie zur besseren Verständlichkeit als „Konform“ gemeldet.

Die einzige Auswirkung wird in Azure Policy zu sehen sein, weil die Anzahl der konformen Ressourcen dort steigt. Ihre Sicherheitsbewertung in Azure Security Center wird dadurch nicht beeinflusst.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportieren wöchentlicher Momentaufnahmen der Daten für die Sicherheitsbewertung und die Einhaltung gesetzlicher Bestimmungen per fortlaufendem Export (Vorschau)

Wir haben den Tools für den [fortlaufenden Export](continuous-export.md) eine neue Previewfunktion hinzugefügt, mit der wöchentliche Momentaufnahmen der Daten für die Sicherheitsbewertung und die Einhaltung gesetzlicher Bestimmungen exportiert werden können.

Legen Sie beim Definieren eines Vorgangs für den fortlaufenden Export die Exporthäufigkeit fest:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Auswählen der Häufigkeit des fortlaufenden Exports":::

- **Streaming**: Bewertungen werden gesendet, wenn der Integritätszustand einer Ressource aktualisiert wird (wenn keine Updates durchgeführt werden, werden keine Daten gesendet).
- **Momentaufnahmen**: Einmal pro Woche wird eine Momentaufnahme des aktuellen Zustands aller Bewertungen der Einhaltung gesetzlicher Bestimmungen gesendet. (Dies ist eine Previewfunktion für wöchentliche Momentaufnahmen der Daten für Sicherheitsbewertungen und die Einhaltung gesetzlicher Bestimmungen.)

Erfahren Sie mehr zu allen Funktionen dieses Features unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md).

## <a name="december-2020"></a>Dezember 2020

Zu den Updates im Dezember gehören:

- [Azure Defender für SQL Server-Instanzen auf Computern ist allgemein verfügbar.](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender für SQL-Unterstützung für dedizierte SQL-Pools von Azure Synapse Analytics ist allgemein verfügbar.](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globale Administratoren können sich jetzt selbst Berechtigungen auf Mandantenebene erteilen.](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Zwei neue Azure Defender-Pläne: Azure Defender für DNS und Azure Defender für Resource Manager (in der Vorschauphase)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Neue Seite „Sicherheitswarnungen“ im Azure-Portal (Vorschau)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Überarbeitete Security Center-Benutzeroberfläche in Azure SQL-Datenbank und SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Aktualisierung von Tools und Filtern des Ressourcenbestands](#asset-inventory-tools-and-filters-updated)
- [Empfehlung zu Web-Apps, die SSL-Zertifikate anfordern, ist nicht mehr Teil der Sicherheitsbewertung](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Empfehlungsseite enthält neue Filter für Umgebung, Schweregrad und verfügbare Antworten](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Neue Datentypen und verbesserte deployifnotexist-Richtlinien für fortlaufenden Export](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender für SQL Server-Instanzen auf Computern ist allgemein verfügbar.

Azure Security Center bietet zwei Azure Defender-Pläne für SQL Server:

- **Azure Defender für Azure SQL-Datenbankserver** – schützt Ihre nativen Azure SQL Server. 
- **Azure Defender für SQL Server auf Computern** – erweitert denselben Schutz auf Ihre SQL Server-Instanzen in Hybrid-, Multicloud- und lokalen Umgebungen

Mit dieser Ankündigung schützt **Azure Defender für SQL** jetzt Ihre Datenbanken und deren Daten überall, wo sie sich befinden.

Azure Defender für SQL umfasst Funktionen zur Sicherheitsrisikobewertung. Das Tool zur Sicherheitsrisikobewertung umfasst die folgenden erweiterten Funktionen:

- **Baselinekonfiguration** (neu!), um die Ergebnisse von Sicherheitsrisikoscans auf intelligente Weise auf jene einzuschränken, die echte Sicherheitsprobleme darstellen könnten. Nach dem Festlegen des Baseline-Sicherheitsstatus meldet die das Tool zur Sicherheitsrisikobewertung nur noch Abweichungen von diesem Baselinestatus. Wenn Ergebnisse in nachfolgenden Überprüfungen mit der Baseline übereinstimmen, wird dies als Bestehen gewertet. Auf diese Weise können Sie und ihre Analysten Ihre Aufmerksamkeit darauf konzentrieren, wo sie gefordert ist.
- **Ausführliche Informationen**, die Ihnen helfen, die erkannten Ergebnisse und deren Zusammenhang mit ihren Ressourcen zu *verstehen*.
- **Wiederherstellungsskripts**  zum Verringern identifizierter Risiken.

Weitere Informationen zu [Azure Defender für SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender für SQL-Unterstützung für dedizierte SQL-Pools von Azure Synapse Analytics ist allgemein verfügbar.

Azure Synapse Analytics (früher SQL DW) ist ein Analysedienst, der Data Warehousing für Unternehmen mit Big Data-Analysen vereint. Dedizierte SQL-Pools sind die Data Warehousing-Funktionen für Unternehmen von Azure Synapse. Weitere Informationen finden Sie unter [Was ist Azure Synapse Analytics (früher SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender für SQL schützt Ihre dedizierten SQL-Pools wie folgt:

- **Erweiterter Schutz vor Bedrohungen** zur Erkennung von Bedrohungen und Angriffen 
- **Funktionen zur Sicherheitsrisikobewertung** zum Identifizieren und Beheben von Sicherheitsfehlkonfigurationen

Die Azure Defender für SQL-Unterstützung für Azure Synapse Analytics-SQL-Pools wird in Azure Security Center automatisch dem Paket „Azure SQL-Datenbanken“ hinzugefügt. Sie finden die entsprechende neue Registerkarte „Azure Defender für SQL“ im Azure-Portal auf der Seite „Synapse-Arbeitsbereich“.

Weitere Informationen zu [Azure Defender für SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Globale Administratoren können sich jetzt selbst Berechtigungen auf Mandantenebene erteilen.

Ein Benutzer mit der Azure Active Directory-Rolle **Globaler Administrator** hat möglicherweise mandantenweite Aufgaben, aber keine Azure-Berechtigungen zum Anzeigen der organisationsweiten Informationen in Azure Security Center. 

Unter [Erteilen mandantenweiter Berechtigungen für sich selbst](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself) erfahren Sie, wie Sie sich selbst Berechtigungen auf Mandantenebene erteilen.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Zwei neue Azure Defender-Pläne: Azure Defender für DNS und Azure Defender für Resource Manager (in der Vorschauphase)

Wir haben zwei neue cloudnative und breit gefächerte Bedrohungsschutzfunktionen für Ihre Azure-Umgebung hinzugefügt.

Diese neuen Schutzmaßnahmen sorgen für eine erhebliche Verbesserung der Resilienz gegenüber Angriffen von Bedrohungsakteuren sowie für eine deutliche Erhöhung der Anzahl von Azure-Ressourcen, die durch Azure Defender geschützt werden.

- **Azure Defender für Resource Manager**: Überwacht automatisch alle in Ihrer Organisation ausgeführten Ressourcenverwaltungsvorgänge. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für Resource Manager](defender-for-resource-manager-usage.md)
    - [Warnungen für Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender für DNS**: Überwacht kontinuierlich alle DNS-Abfragen Ihrer Azure-Ressourcen. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für DNS](defender-for-dns-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für DNS](defender-for-dns-usage.md)
    - [Warnungen für DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Neue Seite „Sicherheitswarnungen“ im Azure-Portal (Vorschau)

Die Seite „Sicherheitswarnungen“ von Azure Security Center wurde neu gestaltet. Sie bietet jetzt Folgendes:

- **Eine verbesserte Selektierungsoberfläche für Warnungen**: Die Liste enthält anpassbare Filter und Gruppierungsoptionen und trägt dadurch zur Reduzierung der „Warnungsmüdigkeit“ bei, sodass Sie sich einfacher auf die relevantesten Bedrohungen konzentrieren können.
- **Weitere Informationen in der Warnungsliste**, beispielsweise MITRE ATT&CK-Taktiken
- **Schaltfläche zum Erstellen von Beispielwarnungen**: Sie können Beispielwarnungen aus allen Azure Defender-Plänen erstellen, um Azure Defender-Funktionen auszuwerten und Ihre Warnungskonfiguration zu testen (SIEM-Integration, E-Mail-Benachrichtigungen und Workflowautomatisierungen).
- **Angleichung an die Azure Sentinel-Incidentoberfläche**: Für Kunden, die beide Produkte verwenden, ist der Wechsel zwischen den Produkten jetzt unkomplizierter, und es ist einfach, das eine vom anderen zu übernehmen.
- **Bessere Leistung** für große Warnungslisten
- **Tastaturnavigation** durch die Warnungsliste
- **Warnungen aus Azure Resource Graph**: Sie können Warnungen in Azure Resource Graph abfragen, der Kusto-ähnlichen API für alle Ihre Ressourcen. Dies ist auch nützlich, wenn Sie eigene Warnungsdashboards erstellen. [Erfahren Sie mehr über Azure Resource Graph](../governance/resource-graph/index.yml).

Um auf die neue Oberfläche zuzugreifen, verwenden Sie oben auf der Seite „Sicherheitswarnungen“ den Link „Jetzt ausprobieren“ im Banner.

:::image type="content" source="media/managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner mit dem Link zur neuen Benutzeroberfläche für Warnungen in der Vorschauversion.":::

Informationen zum Erstellen von Beispielwarnungen über die neue Oberfläche für Warnungen finden Sie unter [Generieren von Azure Defender-Beispielwarnungen](alert-validation.md#generate-sample-security-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Überarbeitete Security Center-Benutzeroberfläche in Azure SQL-Datenbank und SQL Managed Instance 

Die Security Center-Benutzeroberfläche in SQL ermöglicht den Zugriff auf die folgenden Security Center- und Azure Defender für SQL-Features:

- **Sicherheitsempfehlungen**: Security Center führt eine regelmäßige Analyse des Sicherheitsstatus aller verbundenen Azure-Ressourcen durch, um potenzielle Fehlkonfigurationen in Bezug auf die Sicherheit zu identifizieren. Anschließend werden Empfehlungen dazu angezeigt, wie Sie diese Sicherheitsrisiken beseitigen und den Sicherheitsstatus von Organisationen verbessern können.
- **Sicherheitswarnungen**: Bei diesem Erkennungsdienst werden Azure SQL-Aktivitäten fortlaufend auf Bedrohungen wie Einschleusung von SQL-Befehlen, Brute-Force-Angriffe und Berechtigungsmissbrauch überwacht. Dieser Dienst löst ausführliche und aktionsorientierte Sicherheitswarnungen in Security Center aus und zeigt Optionen für die weitere Untersuchung mit Azure Sentinel, der nativen SIEM-Lösung von Microsoft, an.
- **Ergebnisse**: Bei diesem Dienst für die Sicherheitsrisikobewertung werden die Azure SQL-Konfigurationen fortlaufend überwacht, und es wird Hilfestellung bei der Eindämmung von Sicherheitsrisiken geleistet. Es werden Bewertungsüberprüfungen durchgeführt, um eine Übersicht über die einzelnen Azure SQL-Sicherheitsstatus und die zugehörigen ausführlichen Ergebnisse zur Sicherheit anzeigen zu können.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Sicherheitsfunktionen von Azure Security Center für SQL sind in Azure SQL verfügbar":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Aktualisierung von Tools und Filtern des Ressourcenbestands

Die Bestandsseite in Azure Security Center wurde mit den folgenden Änderungen aktualisiert:

- Der Symbolleiste wurde eine Option für **Anleitungen und Feedback** hinzugefügt. Ein Bereich mit Links zu verwandten Informationen und Tools wurde hinzugefügt. 
- Den verfügbaren Standardfiltern für Ihre Ressourcen wurde ein **Abonnementfilter** hinzugefügt.
- Link **Abfrage öffnen** zum Öffnen der aktuellen Filteroptionen als Azure Resource Graph-Abfrage (früher als „Im Resource Graph-Explorer anzeigen“ bezeichnet).
- **Operatoroptionen** für jeden Filter. Sie können jetzt auch andere logische Operatoren als „=“ auswählen. Beispielsweise können Sie nach allen Ressourcen mit aktiven Empfehlungen suchen, deren Titel die Zeichenfolge „encrypt“ enthält. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Steuerelemente für die Operatoroption in Filtern des Ressourcenbestands":::

Weitere Informationen zum Bestand finden Sie unter [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Empfehlung zu Web-Apps, die SSL-Zertifikate anfordern, ist nicht mehr Teil der Sicherheitsbewertung

Die Empfehlung „Web-Apps sollten ein SSL-Zertifikat für alle eingehenden Anforderungen anfordern“ wurde aus der Sicherheitssteuerung **Zugriff und Berechtigungen verwalten** (mit einem Wert von maximal 4 Punkten) in **Bewährte Sicherheitsmethoden implementieren** (mit einem Wert von 0 Punkten) verschoben. 

Wenn sichergestellt wird, dass eine Web-App ein Zertifikat anfordert, erhöht dies auf jeden Fall die Sicherheit. Für öffentliche Web-Apps ist dies aber irrelevant. Wenn Sie über HTTP und nicht HTTPS auf Ihre Website zugreifen, erhalten Sie kein Clientzertifikat. Wenn Ihre Anwendung also Clientzertifikate erfordert, sollten Sie keine Anforderungen an Ihre Anwendung über HTTP zulassen. Weitere Informationen finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Mit dieser Änderung ist die Empfehlung jetzt eine empfohlene bewährte Methode, die keine Auswirkung auf die Bewertung hat. 

Informationen dazu, welche Empfehlungen in den einzelnen Sicherheitssteuerungen enthalten sind, finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Empfehlungsseite enthält neue Filter für Umgebung, Schweregrad und verfügbare Antworten

Azure Security Center überwacht alle verbundenen Ressourcen und generiert Sicherheitsempfehlungen. Verwenden Sie diese Empfehlungen, um den Sicherheitsstatus Ihrer Hybrid Cloud zu verbessern und die Konformität mit den relevanten Richtlinien und Standards für Ihre Organisation, Ihre Branche und Ihr Land nachzuverfolgen.

Während die Abdeckung und die Features von Security Center erweitert werden, werden der Liste mit den Sicherheitsempfehlungen jeden Monat neue Einträge hinzugefügt. Informieren Sie sich beispielsweise unter [29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen](release-notes-archive.md#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Da der Liste ständig neue Einträge hinzugefügt werden, ist es erforderlich, dass sie nach den für Sie interessanten Empfehlungen gefiltert werden kann. Im November haben wir der Seite mit den Empfehlungen Filter hinzugefügt (siehe [Die Empfehlungsliste beinhaltet nun Filter](release-notes-archive.md#recommendations-list-now-includes-filters)).

Die in diesem Monat hinzugefügten Filter verfügen über Optionen, mit denen Sie den Umfang der Liste mit den Empfehlungen wie folgt eingrenzen können:

- **Umgebung**: Es werden Empfehlungen für Ihre AWS-, GCP- oder Azure-Ressourcen (oder eine beliebige Kombination) angezeigt.
- **Schweregrad**: Es werden Empfehlungen gemäß der Klassifizierung nach Schweregrad angezeigt, die für Security Center festgelegt wurden.
- **Antwortaktionen**: Es werden Empfehlungen gemäß der Verfügbarkeit von Security Center-Antwortoptionen angezeigt: „Beheben“, „Verweigern“ und „Erzwingen“.

    > [!TIP]
    > Der Filter für Antwortaktionen ersetzt den Filter vom Typ **Schnelle Problembehebung verfügbar (Ja/Nein)** . 
    > 
    > Informieren Sie sich weiter über diese Antwortoptionen:
    > - [Schaltfläche „Beheben“](implement-security-recommendations.md#fix-button)
    > - [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Nach Sicherheitssteuerung gruppierte Empfehlungen." lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Neue Datentypen und verbesserte deployifnotexist-Richtlinien für fortlaufenden Export

Mit den Azure Security Center-Tools für den fortlaufenden Export können Sie die Empfehlungen und Warnungen für die Verwendung mit anderen Überwachungstools in Ihrer Umgebung exportieren.

Mit dem fortlaufenden Export können Sie umfassend anpassen, was exportiert wird und wohin. Ausführliche Informationen finden Sie unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md).

Diese Tools wurden wie folgt verbessert und erweitert:

- **Verbesserung der deployifnotexist-Richtlinien für den fortlaufenden Export:** Mit den Richtlinien wird nun Folgendes durchgeführt:

    - **Überprüfen, ob die Konfiguration aktiviert ist:** Wenn nicht, wird die Richtlinie als nicht konform angezeigt und eine konforme Ressource erstellt. Weitere Informationen zu den bereitgestellten Azure Policy-Vorlagen finden Sie unter [Einrichten eines fortlaufenden Exports](continuous-export.md#set-up-a-continuous-export) auf der Registerkarte „Bereitstellung im großen Stil mit Azure Policy“.

    - **Unterstützen des Exports von Sicherheitsergebnissen:** Bei Verwendung der Azure Policy-Vorlagen können Sie Ihren fortlaufenden Export so konfigurieren, dass er Ergebnisse enthält. Dies ist beim Exportieren von Empfehlungen relevant, die über untergeordnete Empfehlungen verfügen. Beispiele hierfür sind Ergebnisse aus Sicherheitsrisikobewertungen oder spezifische Systemupdates für die übergeordnete Empfehlung „Systemupdates sollten auf Ihren Computern installiert sein“.
    
    - **Unterstützen des Exports der Daten von Sicherheitsbewertungen**

- **Daten zur Bewertung der Einhaltung gesetzlicher Bestimmungen hinzugefügt (Vorschauphase):** Sie können Updates für Bewertungen der Einhaltung gesetzlichen Bestimmungen jetzt fortlaufend in einen Log Analytics-Arbeitsbereich oder eine Event Hub-Instanz exportieren (gilt auch für benutzerdefinierte Initiativen). Diese Funktion ist in nationalen Clouds nicht verfügbar.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Optionen zum Einbinden von Bewertungen der Einhaltung gesetzlicher Bestimmungen in Ihre Daten des fortlaufenden Exports.":::

## <a name="november-2020"></a>November 2020

Updates im November:

- [29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 in das Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen aufgenommen](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Die Empfehlungsliste beinhaltet nun Filter.](#recommendations-list-now-includes-filters)
- [Die Umgebung für die automatische Bereitstellung wurde verbessert und erweitert.](#auto-provisioning-experience-improved-and-expanded)
- [Die Sicherheitsbewertung ist jetzt im fortlaufenden Export (Vorschauversion) verfügbar.](#secure-score-is-now-available-in-continuous-export-preview)
- [Die Empfehlung „Auf Ihren Computern sollten Systemupdates installiert werden“ enthält jetzt Unterempfehlungen.](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Die Seite „Richtlinienverwaltung“ im Azure-Portal zeigt jetzt den Zuweisungsstatus von Standardrichtlinien an.](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen

Beim Azure-Sicherheitsvergleichstest handelt es sich um einen von Microsoft erstellten Satz mit Azure-spezifischen Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. [Weitere Informationen zum Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction)

Die folgenden 29 Vorschauempfehlungen wurden zu Security Center hinzugefügt, um die Abdeckung dieses Vergleichstests zu erhöhen.

Empfehlungen der Vorschau versetzen keine Ressourcen in einen fehlerhaften Zustand, und sie werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Setzen Sie sie trotzdem um, wann immer möglich, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen. Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Azure Security Center](implement-security-recommendations.md).

| Sicherheitskontrolle                     | Neue Empfehlungen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Verschlüsseln von Daten während der Übertragung              | – Erzwingen einer SSL-Verbindung sollte für PostgreSQL-Datenbankserver aktiviert sein<br>– Erzwingen einer SSL-Verbindung sollte für MySQL-Datenbankserver aktiviert sein<br>– TLS sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– TLS sollte für Ihre Funktions-App auf die aktuelle Version aktualisiert werden<br>– TLS sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– FTPS sollte in API-App erforderlich sein<br>– FTPS sollte in Funktions-App erforderlich sein<br>– FTPS sollte in Web-App erforderlich sein                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Zugriff und Berechtigungen verwalten        | – Web-Apps sollten ein SSL-Zertifikat für alle eingehenden Anforderungen anfordern<br>– API-App sollte verwaltete Identität verwenden<br>– Funktions-App sollte verwaltete Identität verwenden<br>– Web-App sollte verwaltete Identität verwenden                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Nicht autorisierten Netzwerkzugriff einschränken | – Privater Endpunkt sollte für PostgreSQL-Server aktiviert sein<br>– Privater Endpunkt sollte für MariaDB-Server aktiviert sein<br>– Privater Endpunkt sollte für MySQL-Server aktiviert sein                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Überwachung und Protokollierung aktivieren          | - In App Services müssen Diagnoseprotokolle aktiviert sein                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Bewährte Sicherheitsmethoden implementieren    | – Azure Backup sollte für virtuelle Computer aktiviert sein<br>– Georedundante Sicherung sollte für Azure Database for MariaDB aktiviert sein<br>– Georedundante Sicherung sollte für Azure Database for MySQL aktiviert sein<br>– Georedundante Sicherung sollte für Azure Database for PostgreSQL aktiviert sein<br>– PHP sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– PHP sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– Java sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– Java sollte für Ihre Funktions-App auf die aktuelle Version aktualisiert werden<br>– Java sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– Python sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– Python sollte für Ihre Funktions-App auf die aktuelle Version aktualisiert werden<br>– Python sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– Die Aufbewahrung der Überprüfung für SQL-Server sollte auf mindestens 90 Tage festgelegt sein |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Verwandte Links:

- [Weitere Informationen zum Vergleichstest für die Azure-Sicherheit](/security/benchmark/azure/introduction)
- [Weitere Informationen zu API-Apps in Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Weitere Informationen zu Funktions-Apps in Azure](../azure-functions/functions-overview.md)
- [Weitere Informationen zu Web-Apps in Azure](../app-service/overview.md)
- [Weitere Informationen zu Azure Database for MariaDB](../mariadb/overview.md)
- [Weitere Informationen zu Azure Database for MySQL](../mysql/overview.md)
- [Weitere Informationen zu Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 in das Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen aufgenommen

Der Standard „NIST SP 800-171 R2“ ist jetzt als integrierte Initiative zur Verwendung mit dem Azure Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen verfügbar. Die Zuordnungen für die Steuerelemente werden in [Details zur integrierten Initiative zur Einhaltung der gesetzlichen Bestimmungen gemäß NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md) beschrieben. 

Um den Standard auf Ihre Abonnements anzuwenden und Ihren Compliancestatus kontinuierlich zu überwachen, verwenden Sie die Anweisungen unter [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard „NIST SP 800-171 R2“ im Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen":::

Weitere Informationen zu diesem Compliancestandard finden Sie unter [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Die Empfehlungsliste beinhaltet nun Filter.

Sie können die Liste der Sicherheitsempfehlungen nun nach verschiedenen Kriterien filtern. Im folgenden Beispiel wurde die Empfehlungsliste zur Anzeige von Empfehlungen gefiltert, für die Folgendes gilt:

- Sie sind **allgemein verfügbar** (also nicht in der Vorschauphase).
- Sie gelten für **Speicherkonten**.
- Sie unterstützen eine **schnelle Problembehebung**.

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filter für die Empfehlungsliste.":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Die Umgebung für die automatische Bereitstellung wurde verbessert und erweitert.

Mit der Funktion zur automatischen Bereitstellung können Sie den Verwaltungsaufwand verringern, indem Sie die erforderlichen Erweiterungen auf neuen und vorhandenen virtuellen Azure-Computern installieren, damit diese von den Security Center-Schutzmöglichkeiten profitieren können. 

Da Azure Security Center wächst, wurden mehr Erweiterungen entwickelt, und Security Center kann eine umfangreichere Liste von Ressourcentypen überwachen. Die Tools für die automatische Bereitstellung wurden nun erweitert, um durch die Nutzung der Funktionen von Azure Policy weitere Erweiterungen und Ressourcentypen zu unterstützen.

Sie können jetzt die automatische Bereitstellung der folgenden Komponenten konfigurieren:

- Log Analytics-Agent
- (Neu) Azure Policy-Add-On für Kubernetes
- (Neu) Microsoft Dependency-Agent

Weitere Informationen finden Sie unter [Automatische Bereitstellung von Agents und Erweiterungen aus Azure Security Center](enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Die Sicherheitsbewertung ist jetzt im fortlaufenden Export (Vorschauversion) verfügbar.

Mit dem fortlaufenden Export der Sicherheitsbewertung können Sie Änderungen an Ihrer Bewertung in Echtzeit an Azure Event Hubs oder an einen Log Analytics-Arbeitsbereich streamen. Diese Funktion ermöglicht Folgendes:

- Nachverfolgen Ihrer Sicherheitsbewertung im Laufe der Zeit mit dynamischen Berichten
- Exportieren von Sicherheitsbewertungsdaten an Azure Sentinel (oder an eine beliebige andere SIEM-Lösung)
- Integrieren dieser Daten in beliebige Prozesse, die in Ihrer Organisation ggf. bereits zur Überwachung der Sicherheitsbewertung verwendet werden

Weitere Informationen zum fortlaufenden Exportieren von Security Center-Daten finden Sie [hier](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Die Empfehlung „Auf Ihren Computern sollten Systemupdates installiert werden“ enthält jetzt Unterempfehlungen.

Die Empfehlung **Auf Ihren Computern sollten Systemupdates installiert werden** wurde erweitert. Die neue Version enthält Unterempfehlungen für jedes fehlende Update und bietet folgende Verbesserungen:

- Eine neu gestaltete Umgebung auf den Azure Security Center-Seiten des Azure-Portals. Die Seite mit den Empfehlungsdetails für **Auf Ihren Computern sollten Systemupdates installiert werden** enthält die unten gezeigte Liste der Ergebnisse. Wenn Sie ein einzelnes Ergebnis auswählen, wird die Detailansicht mit einem Link zu den Informationen zur Risikominderung und einer Liste der betroffenen Ressourcen geöffnet.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Öffnen einer der Unterempfehlungen auf der Benutzeroberfläche des Portals für die aktualisierte Empfehlung.":::

- Erweiterte Daten für die Empfehlung aus Azure Resource Graph (ARG). ARG ist ein Azure-Dienst zur effizienten Untersuchung von Ressourcen. Sie können ARG verwenden, um über einen bestimmten Satz von Abonnements Abfragen im großen Stil durchzuführen und Ihre Umgebung so effektiv zu verwalten. 

    Für Azure Security Center können Sie ARG und die [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/) verwenden, um eine Vielzahl von Daten zum Sicherheitsstatus abzufragen.

    Zuvor galt: Wenn Sie diese Empfehlung in ARG abgefragt haben, bestand die einzige verfügbare Information darin, dass die Empfehlung auf einem Computer behandelt werden muss. Die folgende Abfrage der aktualisierten Version gibt die einzelnen fehlenden Systemupdates zurück (gruppiert nach Computer):

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Die Seite „Richtlinienverwaltung“ im Azure-Portal zeigt jetzt den Zuweisungsstatus von Standardrichtlinien an.

Auf der Seite **Sicherheitsrichtlinie** von Security Center im Azure-Portal sehen Sie nun, ob ihren Abonnements die standardmäßige Security Center-Richtlinie zugewiesen ist.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Seite „Richtlinienverwaltung“ im Azure Security Center, die die Standardrichtlinienzuweisung anzeigt.":::



## <a name="october-2020"></a>Oktober 2020

Updates im Oktober:
- [Sicherheitsrisikobewertung für lokale und Multi-Cloud-Computer (Vorschau)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Empfehlung für Azure Firewall hinzugefügt (Vorschau)](#azure-firewall-recommendation-added-preview)
- [Die Empfehlung „Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden“ wurde mit einer schnellen Problembehebung aktualisiert](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Das Dashboard zur Einhaltung gesetzlicher Bestimmungen enthält jetzt die Option, Standards zu entfernen](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft.Security/securityStatuses-Tabelle aus Azure Resource Graph (ARG) entfernt](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Sicherheitsrisikobewertung für lokale und Multi-Cloud-Computer (Vorschau)

Der in [Azure Defender für Server](defender-for-servers-introduction.md) integrierte Scanner für die Sicherheitsrisikobewertung (von Qualys) überprüft jetzt Server mit Azure Arc-Unterstützung.

Wenn Sie Azure Arc auf Ihren Nicht-Azure-Computern aktiviert haben, bietet Security Center eine manuelle und skalierbare Bereitstellung des integrierten Sicherheitsrisikoscanners auf diesen Computern.

Mit diesem Update können Sie die Leistungsfähigkeit von **Azure Defender für Server** nutzen, um Ihr Programm zur Verwaltung von Sicherheitsrisiken auf allen Azure- und Nicht-Azure-Ressourcen zu konsolidieren.

Hauptfunktionen:

- Überwachen des Bereitstellungsstatus des Scanners für die Sicherheitsrisikobewertung auf Azure Arc-Computern
- Bereitstellen des integrierten Agents für die Sicherheitsrisikobewertung auf ungeschützten Azure Arc-Computern unter Windows und Linux (manuell und skalierbar)
- Empfangen und Analysieren ermittelter Sicherheitsrisiken von bereitgestellten Agents (manuell und skalierbar)
- Einheitliche Benutzeroberfläche für Azure-VMs und Azure Arc-Computer

[Erfahren Sie mehr über das Bereitstellen der integrierten Qualys-Überprüfung auf Sicherheitsrisiken für Ihre Hybridcomputer](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Erfahren Sie mehr über Server mit Azure Arc-Unterstützung](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Empfehlung für Azure Firewall hinzugefügt (Vorschau)

Es wurde eine neue Empfehlung zum Schützen aller virtuellen Netzwerke mit Azure Firewall hinzugefügt.

In der Empfehlung **Virtuelle Netzwerke müssen durch Azure Firewall geschützt werden** wird Ihnen geraten, mithilfe der Azure-Firewall den Zugriff auf Ihre virtuellen Netzwerke einzuschränken und potenzielle Bedrohungen zu verhindern.

Erfahren Sie mehr über [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Die Empfehlung „Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden“ wurde mit einer schnellen Problembehebung aktualisiert

Für die Empfehlung **Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden** gibt es jetzt eine Option zur schnellen Problembehebung.

Weitere Informationen zu dieser Empfehlung und allen anderen Security Center-Empfehlungen finden Sie unter [Sicherheitsempfehlungen: Referenzhandbuch](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Die Empfehlung „Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden“ mit einer Option zur schnellen Problembehebung.":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Das Dashboard zur Einhaltung gesetzlicher Bestimmungen enthält jetzt die Option, Standards zu entfernen

Das Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen bietet Erkenntnisse zu Ihrem Compliancestatus basierend auf der Erfüllung bestimmter Compliancevorgaben und -anforderungen.

Das Dashboard enthält einen Standardsatz gesetzlicher Standards. Falls einer der angegebenen Standards für Ihre Organisation nicht relevant ist, ist es nun problemlos möglich, diesen aus der Benutzeroberfläche für ein Abonnement zu entfernen. Standards können nur auf der Ebene des *Abonnements* entfernt werden, nicht der für den Verwaltungsgruppenbereich.

Weitere Informationen finden Sie unter [Entfernen eines Standards aus Ihrem Dashboard](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft.Security/securityStatuses-Tabelle aus Azure Resource Graph (ARG) entfernt

Azure Resource Graph ist ein Dienst in Azure, der eine effiziente Ressourcenuntersuchung mit der Fähigkeit bereitstellt, übergreifend für eine bestimmte Menge von Abonnements nach Bedarf Abfragen durchzuführen, sodass Sie Ihre Umgebung effektiv beherrschen können. 

Für Azure Security Center können Sie ARG und die [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/) verwenden, um eine Vielzahl von Daten zum Sicherheitsstatus abzufragen. Zum Beispiel:

- Asset Inventory nutzt (ARG)
- Wir haben eine ARG-Beispielabfrage zum [Identifizieren von Konten ohne aktivierte mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](multi-factor-authentication-enforcement.md#identify-accounts-without-multi-factor-authentication-mfa-enabled) dokumentiert.

In ARG gibt es Datentabellen, die Sie in Ihren Abfragen verwenden können.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph-Explorer und die verfügbaren Tabellen.":::

> [!TIP]
> In der ARG-Dokumentation sind alle verfügbaren Tabellen unter [Azure Resource Graph-Tabelle und Ressourcentypreferenz](../governance/resource-graph/reference/supported-tables-resources.md) aufgelistet.

Bei diesem Update wurde die **Microsoft.Security/securityStatuses**-Tabelle entfernt. Die securityStatuses-API ist weiterhin verfügbar.

Zum Datenersatz kann die Microsoft.Security/Assessments-Tabelle verwendet werden.

Der Hauptunterschied zwischen Microsoft.Security/securityStatuses und Microsoft.Security/Assessments besteht darin, dass Erstere die Aggregation von Bewertungen zeigt, während in der Zweiten jeweils ein einzelner Datensatz enthalten ist.

Beispielsweise gibt Microsoft.Security/securityStatuses ein Ergebnis mit einem Array von zwei Richtlinienbewertungen (policyAssessments) zurück:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Microsoft.Security/Assessments enthält hingegen einen Datensatz für jede Richtlinienbewertung:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Beispiel für das Konvertieren einer vorhandenen ARG-Abfrage mit securityStatuses für die Verwendung der Assessments-Tabelle:**

Abfrage, die auf SecurityStatuses verweist:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Ersatzabfrage für die Assessments-Tabelle:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Weitere Informationen finden Sie unter den folgenden Links:
- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)
- [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>September 2020

Updates im September:
- [Security Center erhält ein neues Aussehen!](#security-center-gets-a-new-look)
- [Azure Defender veröffentlicht](#azure-defender-released)
- [Azure Defender für Key Vault ist allgemein verfügbar.](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender für Storage ist für Files und ADLS Gen2 allgemein verfügbar.](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Asset Inventory-Tools sind jetzt allgemein verfügbar.](#asset-inventory-tools-are-now-generally-available)
- [Deaktivieren der Erkennung eines bestimmten Sicherheitsrisikos bei Scans von Containerregistrierungen und virtuellen Computern](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Ausschließen einer Ressource aus einer Empfehlung](#exempt-a-resource-from-a-recommendation)
- [AWS- und GCP-Connectors in Security Center für Szenarien mit mehreren Clouds](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Empfehlungsbündel für Kubernetes-Workloadschutz](#kubernetes-workload-protection-recommendation-bundle)
- [Ergebnisse der Sicherheitsrisikobewertung sind jetzt im fortlaufenden Export verfügbar](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Verhindern von sicherheitsbezogenen Fehlkonfigurationen durch Erzwingen von Empfehlungen beim Erstellen neuer Ressourcen](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Empfehlungen für Netzwerksicherheitsgruppen verbessert](#network-security-group-recommendations-improved)
- [Veraltete AKS-Vorschauempfehlung „Für Kubernetes Service müssen Podsicherheitsrichtlinien definiert werden“](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-Mail-Benachrichtigungen von Azure Security Center verbessert](#email-notifications-from-azure-security-center-improved)
- [Sicherheitsbewertung ohne Vorschauempfehlungen](#secure-score-doesnt-include-preview-recommendations)
- [Empfehlungen jetzt mit Schweregradindikator und Aktualisierungsintervall](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center erhält ein neues Aussehen!

Wir haben eine aktualisierte Benutzeroberfläche für die Portalseiten von Security Center veröffentlicht. Die neuen Seiten enthalten eine neue Übersicht und Dashboards für Sicherheitsbewertung, Ressourcenbestand und Azure Defender.

Die neu gestaltete Übersicht verfügt jetzt über eine Kachel für den Zugriff auf die Sicherheitsbewertung, den Ressourcenbestand und Azure Defender-Dashboards. Außerdem bietet eine neue Kachel eine Verknüpfung mit dem Compliance-Dashboard.

Erfahren Sie mehr über die [Übersicht](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender veröffentlicht

**Azure Defender** ist die in Security Center integrierte Cloud Workload Protection Platform (CWPP), die einen erweiterten, intelligenten Schutz Ihrer Azure- und Hybridworkloads bietet. Sie ersetzt den Standard-Tarif von Security Center. 

Wenn Sie Azure Defender über den Bereich **Preise und Einstellungen** von Azure Security Center aktivieren, werden alle folgenden Defender-Pläne aktiviert und bieten umfassende Schutzmechanismen für die Compute-, Daten- und Dienstebenen Ihrer Umgebung:

- [Azure Defender für Server](defender-for-servers-introduction.md)
- [Azure Defender für App Service](defender-for-app-service-introduction.md)
- [Azure Defender für Storage](defender-for-storage-introduction.md)
- [Azure Defender für SQL](defender-for-sql-introduction.md)
- [Azure Defender für Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)

Jeder dieser Pläne wird in der Security Center-Dokumentation einzeln erläutert.

Über ein dediziertes Dashboard bietet Azure Defender Sicherheitswarnungen und erweiterten Bedrohungsschutz für virtuelle Computer, SQL-Datenbanken, Container, Webanwendungen, Ihr Netzwerk u. v. m.

Weitere Informationen zu [Azure Defender](azure-defender.md).

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender für Key Vault ist allgemein verfügbar

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). 

**Azure Defender für Key Vault** bietet in Azure nativen erweiterten Bedrohungsschutz für Azure Key Vault und dadurch eine zusätzliche Ebene der Sicherheitsintelligenz. Durch die Erweiterung schützt Azure Defender für Key Vault daher viele der Ressourcen, die von Ihren Key Vault-Konten abhängig sind.

Der optionale Plan ist nun allgemein verfügbar. Dieses Feature wurde in der Vorschau als „Advanced Threat Protection für Azure Key Vault“ bezeichnet.

Außerdem enthalten die Key Vault-Seiten im Azure-Portal nun eine dedizierte Seite **Sicherheit** für Empfehlungen und Warnungen von **Security Center**.

Weitere Informationen finden Sie unter [Azure Defender für Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender für Storage ist für Files und ADLS Gen2 allgemein verfügbar 

**Azure Defender für Storage** erkennt potenziell schädliche Aktivitäten in Ihren Azure Storage-Konten. Ihre Daten können geschützt werden, und zwar unabhängig davon, ob Sie als Blobcontainer, Dateifreigaben oder Data Lakes gespeichert werden.

Die Unterstützung für [Azure Files](../storage/files/storage-files-introduction.md) und [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) ist jetzt allgemein verfügbar.

Ab dem 1. Oktober 2020 beginnen wir damit, den Schutz von Ressourcen für diese Dienste abzurechnen.

Weitere Informationen finden Sie unter [Azure Defender für Storage](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Asset Inventory-Tools sind jetzt allgemein verfügbar

Auf der Seite „Ressourcenbestand“ von Azure Security Center können Sie auf einer Seite den gesamten Sicherheitsstatus der Ressourcen anzeigen, die Sie mit Azure Security Center verbunden haben.

Azure Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Sicherheitsrisiken beheben können.

Wenn eine Ressource ausstehende Empfehlungen hat, werden diese im Inventar angezeigt.

Weitere Informationen finden Sie unter [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Deaktivieren der Erkennung eines bestimmten Sicherheitsrisikos bei Scans von Containerregistrierungen und virtuellen Computern

Azure Defender enthält Sicherheitsrisikoscanner, mit denen Sie Images in Azure Container Registry und auf Ihren virtuellen Computern scannen können.

Wenn in Ihrer Organisation eine Suche ignoriert werden muss, anstatt sie zu beheben, können Sie sie optional deaktivieren. Deaktivierte Ergebnisse haben keine Auswirkung auf Ihre Sicherheitsbewertung und erzeugen kein unerwünschtes Rauschen.

Wenn eine Suche mit den in Ihren Deaktivierungsregeln definierten Kriterien übereinstimmt, wird sie nicht in der Liste der Ergebnisse angezeigt.

Diese Option ist auf den Detailseiten der Empfehlungen verfügbar:

- **Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden**
- **Sicherheitsrisiken für VMs müssen behoben werden**

Weitere Informationen finden Sie unter [Deaktivieren bestimmter Ergebnisse für Containerimages](defender-for-container-registries-usage.md#disable-specific-findings-preview) und [Deaktivieren bestimmter Ergebnisse für virtuelle Computer](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Ausschließen einer Ressource aus einer Empfehlung

Gelegentlich wird eine Ressource in Bezug auf eine bestimmte Empfehlung als fehlerhaft aufgeführt (und damit Ihre Sicherheitsbewertung gesenkt), obwohl Sie anderer Ansicht sind. Unter Umständen wurde dafür eine Lösungsmaßnahme mit einem Prozess durchgeführt, der von Security Center nicht nachverfolgt wird. Es kann auch sein, dass Ihre Organisation die Entscheidung getroffen hat, das Risiko für die entsprechende Ressource zu akzeptieren. 

In diesen Fällen können Sie eine Ausnahmeregel erstellen und sicherstellen, dass die Ressource in Zukunft nicht mehr in der Liste mit den fehlerhaften Ressourcen enthalten ist. Diese Regeln können dokumentierte Begründungen enthalten, wie unten beschrieben.

Weitere Informationen finden Sie unter [Ausschließen einer Ressource aus Empfehlungen und der Sicherheitsbewertung](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS- und GCP-Connectors in Security Center für Szenarien mit mehreren Clouds

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Azure Security Center schützt nun Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Beim Onboarding Ihrer AWS- und GCP-Konten in Security Center werden AWS Security Hub, GCP Security Command und Azure Security Center integriert. 

Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen Ihren AWS-Konten und Azure Security Center](quickstart-onboard-aws.md) und [Herstellen einer Verbindung zwischen Ihren GCP-Konten und Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Empfehlungsbündel für Kubernetes-Workloadschutz

Um sicherzustellen, dass Kubernetes-Workloads standardmäßig sicher sind, fügt Security Center Härtungsempfehlungen auf Kubernetes-Ebene hinzu, einschließlich Erzwingungsoptionen mit Kubernetes-Zugangskontrolle.

Wenn Sie das Azure Policy-Add-On für Kubernetes in Ihrem AKS-Cluster installiert haben, wird jede Anforderung an den Kubernetes-API-Server anhand der vordefinierten bewährten Methoden überwacht, bevor sie im Cluster persistent gespeichert wird. Anschließend können Sie das Erzwingen der bewährten Methoden konfigurieren und auf zukünftige Workloads anwenden.

Beispielsweise können Sie vorschreiben, dass keine privilegierten Container erstellt werden sollen und dass zukünftige Anforderungen für diese Aktion blockiert werden sollen.

Weitere Informationen finden Sie unter [Bewährte Methoden zum Schutz von Workloads mithilfe der Kubernetes-Zugangssteuerung](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Ergebnisse der Sicherheitsrisikobewertung sind jetzt im fortlaufenden Export verfügbar

Verwenden Sie den fortlaufenden Export, um Warnungen und Empfehlungen an Azure Event Hubs, Log Analytics-Arbeitsbereiche oder Azure Monitor zu streamen. Von dort aus können Sie diese Daten in SIEM-Lösungen integrieren (z. B. Azure Sentinel, Power BI, Azure Data Explorer usw.).

Die integrierten Tools für die Sicherheitsrisikobewertung von Security Center geben Ergebnisse zu Ihren Ressourcen als handlungsrelevante Empfehlungen innerhalb einer übergeordneten Empfehlung zurück, z. B. als „Sicherheitsrisiken für VMs müssen behoben werden“. 

Die sicherheitsrelevanten Ergebnisse sind jetzt über den fortlaufenden Export verfügbar, wenn Sie Empfehlungen auswählen und die Option **Sicherheitsrelevante Ergebnisse einbeziehen** aktivieren.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Umschalter „Sicherheitsrelevante Ergebnisse einbeziehen“ in der Konfiguration für den fortlaufenden Export." :::

Verwandte Seiten:

- [In Security Center integrierte Qualys-Lösung zur Sicherheitsrisikobewertung für virtuelle Computer](deploy-vulnerability-assessment-vm.md)
- [Integrierte Security Center-Lösung zur Sicherheitsrisikobewertung für Azure Container Registry-Images](defender-for-container-registries-usage.md)
- [Fortlaufendem Export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Verhindern von sicherheitsbezogenen Fehlkonfigurationen durch Erzwingen von Empfehlungen beim Erstellen neuer Ressourcen

Sicherheitsbezogene Fehlkonfigurationen sind eine häufige Ursache für Sicherheitsincidents. In Security Center ist jetzt ein Feature verfügbar, mit dem Sie Fehlkonfigurationen neuer Ressourcen dank bestimmter Empfehlungen *verhindern* können. 

Dieses Feature kann dazu beitragen, dass Ihre Workloads geschützt sind und Ihre Sicherheitsbewertung stabil bleibt.

Für die Erzwingung einer sicheren Konfiguration basierend auf einer bestimmten Empfehlung gibt es zwei Modi:

- Mit dem Effekt **Deny** (Ablehnen) von Azure Policy können Sie verhindern, dass fehlerhafte Ressourcen erstellt werden.

- Mit der Option **Erzwingen** können Sie den Effekt **DeployIfNotExist** von Azure Policy nutzen und nicht konforme Ressourcen bei der Erstellung automatisch korrigieren.
 
Diese sind für ausgewählte Sicherheitsempfehlungen oben auf der Seite mit den Ressourcendetails verfügbar.

Weitere Informationen finden Sie unter [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Empfehlungen für Netzwerksicherheitsgruppen verbessert

Die folgenden Sicherheitsempfehlungen im Zusammenhang mit Netzwerksicherheitsgruppen wurden verbessert, um einige Arten von False Positives zu verringern.

- Alle Netzwerkports sollten auf NSGs eingeschränkt werden, die ihrer VM zugeordnet sind.
- Verwaltungsports sollten auf Ihren virtuellen Computern geschlossen werden.
- Virtuelle Computer mit Internetzugang sollten über Netzwerksicherheitsgruppen geschützt werden.
- Subnetze sollten einer Netzwerksicherheitsgruppe zugeordnet werden


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Veraltete AKS-Vorschauempfehlung „Für Kubernetes Service müssen Podsicherheitsrichtlinien definiert werden“

Die Vorschauempfehlung „Für Kubernetes Service müssen Podsicherheitsrichtlinien definiert werden“ ist veraltet. Eine Beschreibung finden Sie in der Dokumentation zu [Azure Kubernetes Service](../aks/use-pod-security-policies.md).

Das Feature „Podsicherheitsrichtlinie“ (Vorschauversion) wird als veraltet eingestuft und steht nach dem 15. Oktober 2020 zugunsten von Azure Policy für AKS nicht mehr zur Verfügung.

Wenn die Podsicherheitsrichtlinie (Vorschauversion) veraltet ist, müssen Sie das Feature für alle vorhandenen Cluster deaktivieren, die das veraltete Feature verwenden, um zukünftige Clusterupgrades ausführen und weiterhin Azure-Support erhalten zu können.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-Mail-Benachrichtigungen von Azure Security Center verbessert

Die folgenden Bereiche von E-Mails zu Sicherheitswarnungen wurden verbessert: 

- Möglichkeit zum Senden von E-Mail-Benachrichtigungen zu Warnungen für alle Schweregrade hinzugefügt
- Möglichkeit zum Benachrichtigen von Benutzern mit unterschiedlichen Azure-Rollen für das Abonnement hinzugefügt
- Wir benachrichtigen standardmäßig Abonnementbesitzer bei Warnungen mit hohem Schweregrad (bei denen es sich mit hohen Wahrscheinlichkeit um echte Verstöße handelt).
- Das Feld für die Telefonnummer wurde von der Konfigurationsseite für E-Mail-Benachrichtigungen entfernt.

Weitere Informationen finden Sie unter [Einrichten von E-Mail-Benachrichtigungen für Sicherheitswarnungen](configure-email-notifications.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Sicherheitsbewertung ohne Vorschauempfehlungen 

Security Center führt eine ständige Bewertung Ihrer Ressourcen, Abonnements und Organisation in Bezug auf Sicherheitsprobleme durch. Anschließend werden alle Ergebnisse in einer einzigen Bewertung zusammengefasst, sodass Sie auf einen Blick Ihre aktuelle Sicherheitssituation erkennen können: je höher die Bewertung, desto geringer das ermittelte Risiko.

Wenn neue Bedrohungen erkannt werden, werden neue Sicherheitsempfehlungen in Security Center über neue Empfehlungen verfügbar gemacht. Um unerwartete Änderungen an Ihrer Sicherheitsbewertung zu vermeiden und eine Toleranzperiode zu gewähren, in der Sie neue Empfehlungen erkunden können, bevor diese sich auf Ihre Bewertungen auswirken, werden Empfehlungen, die als **Vorschau** gekennzeichnet sind, nicht mehr in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Sie sollten nach Möglichkeit weiterhin korrigiert werden, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen.

Außerdem führen Empfehlungen in der **Vorschauphase** nicht dazu, dass eine Ressource als „Fehlerhaft“ gekennzeichnet wird.

Beispiel für eine Vorschauempfehlung:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Empfehlung mit dem Flag „Vorschau“.":::

[Weitere Informationen zur Sicherheitsbewertung.](secure-score-security-controls.md)


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Empfehlungen jetzt mit Schweregradindikator und Aktualisierungsintervall

Die Detailseite für Empfehlungen enthält jetzt einen Indikator für das Aktualisierungsintervall (sofern relevant) und eine deutliche Anzeige des Schweregrads der Empfehlung.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Empfehlungsseite mit Aktualität und Schweregrad.":::


## <a name="august-2020"></a>August 2020

Updates im August:

- [Ressourcenbestand – leistungsstarke neue Ansicht des Sicherheitsstatus ihrer Ressourcen](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Unterstützung für Azure Active Directory-Sicherheitsstandards (für die mehrstufige Authentifizierung) hinzugefügt](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Empfehlung zu Dienstprinzipale hinzugefügt](#service-principals-recommendation-added)
- [Sicherheitsrisikobewertung bei virtuellen Computern: Empfehlungen und Richtlinien wurden konsolidiert](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Der Initiative ASC_default wurden neue AKS-Sicherheitsrichtlinien hinzugefügt – nur für Kunden der privaten Vorschau.](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Ressourcenbestand – leistungsstarke neue Ansicht des Sicherheitsstatus ihrer Ressourcen

Der Ressourcenbestand in Security Center (derzeit in der Vorschauphase) bietet eine Möglichkeit, den Sicherheitsstatus der Ressourcen anzuzeigen, die Sie mit Security Center verbunden haben.

Azure Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Sicherheitsrisiken beheben können. Wenn eine Ressource ausstehende Empfehlungen hat, werden diese im Inventar angezeigt.

Sie können die Ansicht und ihre Filter verwenden, um Ihre Daten zum Sicherheitsstatus zu untersuchen und auf der Grundlage der Ergebnisse weitere Maßnahmen zu ergreifen.

Weitere Informationen: [Ressourcenbestand](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Unterstützung für Azure Active Directory-Sicherheitsstandards (für die mehrstufige Authentifizierung) hinzugefügt

Azure Security Center bietet vollständige Unterstützung für [Sicherheitsstandards](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), den kostenlosen Schutzfunktionen von Microsoft für die Identitätssicherheit.

Die Sicherheitsstandards bieten vorkonfigurierte Identitätssicherheitseinstellungen, um Ihre Organisation vor häufigen identitätsbezogenen Angriffen zu schützen. Sicherheitsstandards schützen bereits mehr als 5 Millionen Mandanten insgesamt; 50.000 Mandanten werden zusätzlich durch Azure Security Center geschützt.

Azure Security Center stellt jetzt eine Sicherheitsempfehlung bereit, sobald ein Azure-Abonnement ohne aktivierte Sicherheitsstandards identifiziert wird. Bisher empfahl Azure Security Center die Aktivierung der mehrstufigen Authentifizierung (MFA) mittels bedingtem Zugriff, was Teil der Lizenz von Azure Active Directory (AD) Premium ist. Für Kunden, die Azure AD Free verwenden, empfehlen wir nun, die Sicherheitsstandards zu aktivieren. 

Unser Ziel ist es, mehr Kunden zu ermutigen, ihre Cloudumgebungen mit MFA abzusichern und damit eines der höchsten Risiken zu mindern, das sich auch am stärksten auf ihre [Sicherheitsbewertung](secure-score-security-controls.md) auswirkt.

Weitere Informationen: [Sicherheitsstandards](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Empfehlung zu Dienstprinzipale hinzugefügt

Es wurde eine neue Empfehlung hinzugefügt, die Azure Security Center-Kunden, die Verwaltungszertifikate zum Verwalten ihrer Abonnements verwenden, empfiehlt, zu Dienstprinzipalen zu wechseln.

Die Empfehlung, **Zum Schutz Ihrer Abonnements Dienstprinzipale anstelle von Verwaltungszertifikaten verwenden** besagt, für eine sicherere Verwaltung Ihrer Abonnements Dienstprinzipale oder Azure Resource Manager zu verwenden. 

Weitere Informationen: [Anwendungs- und Dienstprinzipalobjekten in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Sicherheitsrisikobewertung bei virtuellen Computern: Empfehlungen und Richtlinien wurden konsolidiert

Security Center untersucht Ihre virtuellen Computer, um festzustellen, ob darauf eine Lösung zur Sicherheitsrisikobewertung ausgeführt wird. Wenn keine Lösung zur Sicherheitsrisikobewertung gefunden wird, gibt Security Center eine Empfehlung zur Vereinfachung der Bereitstellung.

Werden Sicherheitsrisiken gefunden, gibt Security Center eine Empfehlung, die die Ergebnisse zusammenfasst, damit Sie die Sicherheitsrisiken untersuchen und gegebenenfalls beheben können.

Um für alle Benutzer, unabhängig vom verwendeten Scannertyp, eine einheitliche Umgebung zu gewährleisten, haben wir vier Empfehlungen zu den folgenden beiden Empfehlungen vereinheitlicht:

|Vereinheitlichte Empfehlung|Änderungsbeschreibung|
|----|:----|
|**Auf Ihren virtuellen Computern muss eine Lösung zur Sicherheitsrisikobewertung installiert werden**|Ersetzt die folgenden beiden Empfehlungen:<br> ***** Integrierte Lösung zur Sicherheitsrisikobewertung auf virtuellen Computern aktivieren (unterstützt von Qualys – jetzt veraltet) (Empfehlung wird in Standard-Tarifen angezeigt)<br> ***** Die Lösung zur Sicherheitsrisikobewertung sollte auf Ihren virtuellen Computern installiert werden (jetzt veraltet) (Empfehlung wird sowohl in Standard- als auch Free-Tarifen angezeigt)|
|**Sicherheitsrisiken für VMs müssen behoben werden**|Ersetzt die folgenden beiden Empfehlungen:<br>***** Auf Ihren virtuellen Computern gefundene Sicherheitsrisiken beheben (unterstützt von Qualys – jetzt veraltet)<br>***** Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung entschärft werden (unterstützt von Qualys – jetzt veraltet)|
|||

Nun wenden Sie dieselbe Empfehlung an, um die Security Center-Erweiterung für die Sicherheitsrisikobewertung oder eine privat lizenzierte Lösung („BYOL“) von einem Partner wie Qualys oder Rapid7 bereitzustellen.

Wurden Sicherheitsrisiken gefunden und an Security Center gemeldet, werden Sie unabhängig von der Lösung zur Sicherheitsrisikobewertung, die diese Sicherheitsrisiken identifiziert hat, mit einer einzigen Empfehlung auf die Ergebnisse aufmerksam gemacht.

#### <a name="updating-dependencies"></a>Aktualisieren von Abhängigkeiten

Wenn Sie über Skripts, Abfragen oder Automatisierungen verfügen, die sich auf die früheren Empfehlungen oder Richtlinienschlüssel/-namen beziehen, verwenden Sie die folgenden Tabellen, um die Verweise zu aktualisieren:

##### <a name="before-august-2020"></a>Vor August 2020

| Empfehlung|`Scope`|
|----|:----|
|**Integrierte Lösung zur Sicherheitsrisikobewertung (unterstützt von Qualys) auf virtuellen Computern aktivieren**<br>Schlüssel: 550e890b-e652-4d22-8274-60b3bdb24c63|Integriert|
|**Auf Ihren virtuellen Computern gefundene Sicherheitsrisiken beheben (unterstützt von Qualys)**<br>Schlüssel: 1195afff-c881-495e-9bc5-1486211ae03f|Integriert|
|**Die Lösung zur Sicherheitsrisikobewertung sollte auf Ihren virtuellen Computern installiert werden**<br>Schlüssel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden**<br>Schlüssel: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|Richtlinie|`Scope`|
|----|:----|
|**Sicherheitsrisikobewertung für VMs muss aktiviert sein**<br>Richtlinien-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Integriert|
|**Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung entschärft werden**<br>Richtlinien-ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>Ab August 2020

|Empfehlung|`Scope`|
|----|:----|
|**Auf Ihren virtuellen Computern muss eine Lösung zur Sicherheitsrisikobewertung installiert werden**<br>Schlüssel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Integriert + BYOL|
|**Sicherheitsrisiken für VMs müssen behoben werden**<br>Schlüssel: 1195afff-c881-495e-9bc5-1486211ae03f|Integriert + BYOL|
|||

|Richtlinie|`Scope`|
|----|:----|
|[**Sicherheitsrisikobewertung für virtuelle Computer muss aktiviert sein**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Richtlinien-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Integriert + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Der Initiative ASC_default wurden neue AKS-Sicherheitsrichtlinien hinzugefügt – nur für Kunden der privaten Vorschau.

Um sicherzustellen, dass Kubernetes-Workloads standardmäßig sicher sind, fügt Security Center Richtlinien auf Kubernetes-Ebene und Härtungsempfehlungen hinzu, einschließlich Erzwingungsoptionen mit Kubernetes-Zugangskontrolle.

Die frühe Phase dieses Projekts schließt eine private Vorschau und neue (standardmäßig deaktivierte) Richtlinien für die Initiative ASC_default ein.

Sie können diese Richtlinien gefahrlos und ohne Auswirkungen auf Ihre Umgebung ignorieren. Wenn Sie sie aktivieren möchten, registrieren Sie sich unter https://aka.ms/SecurityPrP für die Vorschauversion, und wählen Sie eine der folgenden Optionen aus:

1. **Einzelne Vorschau**, um nur dieser privaten Vorschau beizutreten. Nennen Sie „ASC Continuous Scan“ ausdrücklich als Vorschauversion, der Sie beitreten möchten.
1. **Fortlaufendes Programm**, um dieser und zukünftigen privaten Vorschauversionen hinzugefügt zu werden. Sie müssen ein Profil und eine Datenschutzvereinbarung ausfüllen.


## <a name="july-2020"></a>Juli 2020

Zu den Updates im Juli gehören:
- [Sicherheitsrisikobewertung für virtuelle Computer jetzt verfügbar für Nicht-Marketplace-Images](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Erweiterung des Bedrohungsschutzes für Azure Storage durch Einbeziehen von Azure Files und Azure Data Lake Storage Gen2 (Vorschau)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Acht neue Empfehlungen zum Aktivieren von Bedrohungsschutzfeatures](#eight-new-recommendations-to-enable-threat-protection-features)
- [Verbesserungen der Containersicherheit: schnellere Überprüfung der Registrierung und aktualisierte Dokumentation](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Neue Empfehlung zum Aktualisieren Ihrer Regeln für die adaptive Anwendungssteuerung](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sechs Richtlinien für SQL Advanced Data Security als veraltet markiert](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Sicherheitsrisikobewertung für virtuelle Computer jetzt verfügbar für Nicht-Marketplace-Images

Beim Bereitstellen einer Lösung zur Sicherheitsrisikobewertung führte Security Center bisher vorab eine Überprüfung durch. Die Überprüfung diente der Bestätigung einer Marketplace-SKU des virtuellen Zielcomputers. 

Ab diesem Update wurde die Überprüfung entfernt, und Sie können nun Tools zur Sicherheitsrisikobewertung auf benutzerdefinierten Windows- und Linux-Computern bereitstellen. Benutzerdefinierte Images sind solche, in denen Sie die Marketplace-Standardeinstellungen geändert haben.

Auch wenn Sie nun die integrierte Erweiterung für die Sicherheitsrisikobewertung (bereitgestellt durch Qualys) auf vielen weiteren Computern bereitstellen können, ist Support nur verfügbar, wenn Sie ein Betriebssystem verwenden, das unter [Bereitstellen der integrierten für Überprüfung auf Sicherheitsrisiken auf VMs im Standard-Tarif](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) aufgeführt ist.

Erfahren Sie mehr über die [integrierte Lösung für die Überprüfung auf Sicherheitsrisiken für virtuelle Computer (erfordert Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Erfahren Sie mehr über das Verwenden Ihrer eigenen privat lizenzierten Lösung zur Sicherheitsrisikobewertung von Qualys oder Rapid7 unter [Bereitstellen einer Partnerlösung zur Überprüfung auf Sicherheitsrisiken](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Erweiterung des Bedrohungsschutzes für Azure Storage durch Einbeziehen von Azure Files und Azure Data Lake Storage Gen2 (Vorschau)

Der Bedrohungsschutz für Azure Storage erkennt potenziell schädliche Aktivitäten in Ihren Azure Storage-Konten. Security Center zeigt Warnungen an, wenn Versuche erkannt werden, auf Ihre Speicherkonten zuzugreifen oder diese auszunutzen. 

Ihre Daten können geschützt werden, und zwar unabhängig davon, ob Sie als Blobcontainer, Dateifreigaben oder Data Lakes gespeichert werden.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Acht neue Empfehlungen zum Aktivieren von Bedrohungsschutzfeatures

Es wurden acht neue Empfehlungen hinzugefügt, um eine einfache Möglichkeit zum Aktivieren der Bedrohungsschutzfeatures von Azure Security Center für die folgenden Ressourcentypen bereitzustellen: virtuelle Computer, App Service-Pläne, Azure SQL-Datenbank-Server, SQL-Server auf Computern, Azure Storage-Konten, Azure Kubernetes Service-Cluster, Azure Container Registry-Registrierungen und Azure Key Vault-Tresore.

Die neuen Empfehlungen sind:

- **Advanced Data Security muss für Azure SQL-Datenbank-Server aktiviert sein**
- **Advanced Data Security muss für SQL Server-Instanzen auf Computern aktiviert sein.**
- **Advanced Threat Protection muss in Azure App Service-Plänen aktiviert sein.**
- **Advanced Threat Protection muss in Azure Container Registry-Instanzen aktiviert sein**
- **Advanced Threat Protection muss in Azure Key Vault-Instanzen aktiviert sein**
- **Advanced Threat Protection muss in Azure Kubernetes Service-Clustern aktiviert sein**
- **Advanced Threat Protection muss für Azure Storage-Konten aktiviert sein**
- **Advanced Threat Protection muss für virtuelle Computer aktiviert sein.**

Diese neuen Empfehlungen gehören zur Sicherheitssteuerung **Azure Defender aktivieren**.

Die Empfehlungen schließen auch die Möglichkeit schneller Korrekturen ein. 

> [!IMPORTANT]
> Wenn Sie eine dieser Empfehlungen umsetzen, fallen Gebühren für den Schutz der relevanten Ressourcen an. Diese Kosten beginnen sofort, wenn Sie über zugehörige Ressourcen im aktuellen Abonnement verfügen. Das gilt auch, wenn Sie sie zu einem späteren Zeitpunkt hinzufügen.
> 
> Wenn Sie z. B. nicht über Azure Kubernetes Service-Cluster in Ihrem Abonnement verfügen und den Bedrohungsschutz aktivieren, fallen keine Gebühren an. Wenn Sie in Zukunft einen Cluster im selben Abonnement hinzufügen, wird dieser automatisch geschützt, und die Gebühren beginnen ab diesem Zeitpunkt.

Weitere Informationen zu den einzelnen Angaben finden Sie auf der [Referenzseite zu Sicherheitsempfehlungen](recommendations-reference.md).

Erfahren Sie mehr über [Bedrohungsschutz in Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Verbesserungen bei der Containersicherheit: schnellere Überprüfung der Registrierung und aktualisierte Dokumentation

Im Rahmen der kontinuierlichen Investitionen im Bereich Containersicherheit geben wir gerne eine bedeutende Leistungsverbesserung im Security Center-Feature für dynamische Scans von Containerimages in Azure Container Registry weiter. Scans werden nun in der Regel in ungefähr zwei Minuten abgeschlossen. In einigen Fällen kann es bis zu 15 Minuten dauern.

Zur Verbesserung der Klarheit und der Beschreibungen zu den Sicherheitsfunktionen von Azure Security Center haben wir auch die Dokumentationsseiten für die Containersicherheit aktualisiert. 

Weitere Informationen zur Containersicherheit in Security Center finden Sie in den folgenden Artikeln:

- [Übersicht über die Containersicherheitsfeatures von Security Center](container-security.md)
- [Ausführliche Informationen zur Integration mit Azure Container Registry](defender-for-container-registries-introduction.md)
- [Ausführliche Informationen zur Integration mit Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Überprüfen Ihrer Registrierungen und Härten Ihrer Docker-Hosts](container-security.md)
- [Sicherheitswarnungen von den Features zum Schutz vor Bedrohungen für Azure Kubernetes Service-Cluster](alerts-reference.md#alerts-k8scluster)
- [Sicherheitswarnungen von den Features zum Schutz vor Bedrohungen für Azure Kubernetes Service-Hosts](alerts-reference.md#alerts-containerhost)
- [Sicherheitsempfehlungen für Container](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Neue Empfehlung zum Aktualisieren Ihrer Regeln für die adaptive Anwendungssteuerung

Das Feature für die adaptive Anwendungssteuerung hat zwei bedeutende Updates erhalten:

* Eine neue Empfehlung identifiziert potenziell legitimes Verhalten, das bisher noch nicht zulässig war. Die neue Empfehlung **Zulassungslistenregeln in der Richtlinie für die adaptive Anwendungssteuerung müssen aktualisiert werden** fordert Sie auf, der vorhandenen Richtlinie neue Regeln hinzuzufügen, um die Anzahl der falsch positiven Ergebnisse bei adaptiven Warnungen zu Anwendungssteuerungen zu verringern.

* Pfadregeln unterstützen jetzt Platzhalter. Mit diesem Update können Sie zulässige Pfadregeln mithilfe von Platzhaltern konfigurieren. Es werden zwei Szenarios unterstützt:

    * Verwenden eines Platzhalters am Ende eines Pfads, um alle ausführbaren Dateien in diesem Ordner und Unterordnern zuzulassen

    * Verwenden eines Platzhalters in der Mitte eines Pfads zum Aktivieren des Namens einer bekannten ausführbaren Datei mit einem sich ändernden Ordnernamen (z. B. persönliche Benutzerordner mit einer bekannten ausführbaren Datei, automatisch generierte Ordnernamen usw.)


[Weitere Informationen über Adaptive Anwendungssteuerungen](adaptive-application-controls.md)



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sechs Richtlinien für SQL Advanced Data Security als veraltet markiert

Sechs Richtlinien im Zusammenhang mit Advanced Data Security für SQL-Computer werden als veraltet markiert:

- Advanced Threat Protection-Typen müssen in den Advanced Data Security-Einstellungen von SQL Managed Instance auf „Alle“ festgelegt werden
- Advanced Threat Protection-Typen müssen in den Advanced Data Security-Einstellungen der SQL Server-Instanz auf „Alle“ festgelegt werden
- Advanced Data Security-Einstellungen für verwaltete SQL-Instanzen sollten eine E-Mail-Adresse für den Empfang von Sicherheitswarnungen enthalten.
- Advanced Data Security-Einstellungen für SQL Server sollten eine E-Mail-Adresse für den Empfang von Sicherheitswarnungen enthalten.
- In den Advanced Data Security-Einstellungen für die verwaltete SQL-Instanz müssen E-Mail-Benachrichtigungen an Administratoren und Abonnementbesitzer aktiviert sein
- E-Mail-Benachrichtigungen an Administratoren und Abonnementbesitzer sollten in den erweiterten Einstellungen für Datensicherheit in SQL Server aktiviert werden.

Weitere Informationen zu [integrierten Richtlinien](./policy-reference.md)



## <a name="june-2020"></a>Juni 2020

Zu den Updates im Juni gehören:

- [Sicherheitsbewertungs-API (Vorschau)](#secure-score-api-preview)
- [Erweiterte Datensicherheit für SQL-Computer (Azure, andere Clouds und lokal) (Vorschau)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Zwei neue Empfehlungen zum Bereitstellen des Log Analytics-Agent auf Azure Arc-Computern (Vorschau)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Neue Richtlinien zum Erstellen von Konfigurationen für fortlaufenden Export und Workflowautomatisierung im großen Stil](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Neue Empfehlung zum Verwenden von NSGs zum Schützen von virtuellen Computern ohne Internetzugriff](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Neue Richtlinien zum Aktivieren von Bedrohungsschutz und erweiterter Datensicherheit](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Sicherheitsbewertungs-API (Vorschau)

Sie können jetzt auf Ihre Bewertung über die [Sicherheitsbewertungs-API](/rest/api/securitycenter/securescores/) (derzeit in der Vorschau) zugreifen. Die API-Methoden bieten die Flexibilität, die Daten abzufragen und im Laufe der Zeit einen eigenen Berichtsmechanismus für Ihre Sicherheitsbewertungen zu erstellen. Beispielsweise können Sie die API **Sicherheitsbewertungen** verwenden, um die Bewertung für ein bestimmtes Abonnement zu erhalten. Darüber hinaus können Sie die API **Sicherheitsbewertungs-Steuerelemente** verwenden, um die Sicherheitssteuerelemente und die aktuelle Bewertung Ihrer Abonnements aufzulisten.

Beispiele für externe Tools, die mit der Sicherheitsbewertungs-API verwendet werden können, finden Sie im [Bereich zu den Sicherheitsbewertungen in unserer GitHub-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Weitere Informationen finden Sie unter [Erweiterter Secure Score (Vorschau) in Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Erweiterte Datensicherheit für SQL-Computer (Azure, andere Clouds und lokal) (Vorschau)

Advanced Data Security für SQL-Computer im Azure Security Center schützt jetzt in Azure, in anderen Cloudumgebungen und sogar auf lokalen Computern gehostete SQL-Server-Instanzen. Dies erweitert den Schutz für Ihre nativen Azure-SQL Server-Instanzen, um vollständige Unterstützung für Hybridumgebungen bereitzustellen.

Erweiterte Datensicherheit bietet eine Sicherheitsrisikobewertung und erweiterten Bedrohungsschutz für Ihre SQL-Computer, wo immer sie sich befinden.

Das Setup umfasst zwei Schritte:

1. Stellen Sie den Log Analytics-Agent auf dem Hostcomputer Ihrer SQL Server-Instanz bereit, um die Verbindung mit dem Azure-Konto herzustellen.

1. Aktivieren Sie das optionale Paket auf der Security Center-Seite „Preise und Einstellungen“.

Weitere Informationen finden Sie unter [Erweiterte Datensicherheit für SQL Server-Instanzen in Azure Virtual Machines (Vorschau)](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Zwei neue Empfehlungen zum Bereitstellen des Log Analytics-Agent auf Azure Arc-Computern (Vorschau)

Es wurden zwei neue Empfehlungen hinzugefügt, die Ihnen helfen, den [Log Analytics-Agent](../azure-monitor/agents/log-analytics-agent.md) auf Ihren Azure Arc-Computern bereitzustellen und sicherzustellen, dass sie durch Azure Security Center geschützt sind:

- **Log Analytics-Agent muss auf Ihren Windows-basierten Azure Arc-Computern installiert sein (Vorschau)**
- **Log Analytics-Agent muss auf Ihren Linux-basierten Azure Arc-Computern installiert sein (Vorschau)**

Diese neuen Empfehlungen werden in denselben vier Sicherheitssteuerelementen angezeigt wie die vorhandene (verwandte) Empfehlung, **Monitoring Agent sollte auf ihren Computern installiert werden**: Sicherheitskonfigurationen reparieren, adaptive Anwendungssteuerung anwenden, Systemupdates anwenden und Endpunktschutz aktivieren.

Die Empfehlungen umfassen auch die Schnellkorrekturfunktion, um den Bereitstellungsprozess zu beschleunigen. 

Weitere Informationen zu diesen beiden neuen Empfehlungen finden Sie in der Tabelle [Compute- und App-Empfehlungen](recommendations-reference.md#recs-compute).

In [Was ist der Log Analytics-Agent?](./faq-data-collection-agents.yml#what-is-the-log-analytics-agent-) erfahren Sie mehr darüber, wie Azure Security Center den Agent verwendet.

Erfahren Sie mehr über [Erweiterungen für Azure Arc-Computer](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Neue Richtlinien zum Erstellen von Konfigurationen für fortlaufenden Export und Workflowautomatisierung im großen Stil

Die Automatisierung der Prozesse Ihrer Organisation zur Überwachung und Reaktion auf Vorfälle kann die Zeit, die zum Untersuchen von Sicherheitsvorfällen und zur Durchführung entsprechender Gegenmaßnahmen benötigt wird, erheblich verkürzen.

Zum Bereitstellen Ihrer Automatisierungskonfigurationen in Ihrer Organisation verwenden Sie diese integrierten „DeployIfdNotExist“-Azure-Richtlinien zum Erstellen und Konfigurieren von Prozeduren für [fortlaufenden Export](continuous-export.md) und [Workflowautomatisierung](workflow-automation.md):

Die Richtliniendefinitionen finden Sie unter Azure Policy:


|Zielsetzung  |Richtlinie  |Richtlinien-ID  |
|---------|---------|---------|
|Fortlaufender Export zu einem Event Hub|[Bereitstellen eines Exports für Azure Security Center-Warnungen und -Empfehlungen in Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Fortlaufender Export in einen Log Analytics-Arbeitsbereich|[Bereitstellen eines Exports für Azure Security Center-Warnungen und -Empfehlungen in Log Analytics-Arbeitsbereichen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Workflowautomatisierung für Sicherheitswarnungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Workflowautomatisierung für Sicherheitsempfehlungen|[Bereitstellen der Workflowautomatisierung für Azure Security Center-Empfehlungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Beginnen Sie mit [Vorlagen zur Workflowautomatisierung](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Weitere Informationen über die Verwendung der beiden Exportrichtlinien finden Sie unter [Konfigurieren der Workflowautomatisierung in großem Stile mit Hilfe der bereitgestellten Richtlinien](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) und [Einrichten eines fortlaufenden Exports](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Neue Empfehlung zum Verwenden von NSGs zum Schützen von virtuellen Computern ohne Internetzugriff

Das Sicherheitssteuerelement „Bewährte Sicherheitsmethoden implementieren“ enthält jetzt die folgende neue Empfehlung:

- **Virtuelle Computer ohne Internetzugang sollten über Netzwerksicherheitsgruppen geschützt werden.**

Eine vorhandene Empfehlung **VMs mit Internetzugang sollten mithilfe von Netzwerksicherheitsgruppen geschützt werden** unterschied nicht zwischen virtuellen Computern mit und ohne Internetzugriff. Für beide wurde eine Empfehlung mit hohem Schweregrad generiert, wenn eine VM keiner Netzwerksicherheitsgruppe zugewiesen war. Diese neue Empfehlung separiert die Computer ohne Internetzugriff, um falsch positive Ergebnisse zu reduzieren und unnötige Warnungen mit hohem Schweregrad zu vermeiden.

Weitere Informationen finden Sie in der Tabelle [Netzwerkempfehlungen](recommendations-reference.md#recs-networking).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Neue Richtlinien zum Aktivieren von Bedrohungsschutz und erweiterter Datensicherheit

Die folgenden neuen Richtliniendefinitionen wurden der ASC-Standardinitiative hinzugefügt und dienen zur Unterstützung beim Aktivieren von Bedrohungsschutz oder erweiterter Datensicherheit für die relevanten Ressourcentypen.

Die Richtliniendefinitionen finden Sie unter Azure Policy:


| Richtlinie                                                                                                                                                                                                                                                                | Richtlinien-ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Für Azure SQL-Datenbank-Server muss Advanced Data Security aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Advanced Data Security muss für SQL Server-Instanzen auf Computern aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection muss für Azure Storage-Konten aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection muss in Azure Key Vault-Instanzen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection muss in Azure App Service-Plänen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Advanced Threat Protection muss in Azure Container Registry-Instanzen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Advanced Threat Protection muss in Azure Kubernetes Service-Clustern aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cdq1-3 23-492b5a539-13118b6d1e3a |
| [Advanced Threat Protection muss für Virtual Machines aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Erfahren Sie mehr über [Bedrohungsschutz in Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Mai 2020

Zu den Updates im Mai gehören:
- [Regeln zur Warnungsunterdrückung (Vorschau)](#alert-suppression-rules-preview)
- [Sicherheitsrisikobewertung für virtuelle Computer ist jetzt allgemein verfügbar](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Änderungen am JIT-VM-Zugriff (Just-In-Time)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Benutzerdefinierte Empfehlungen wurden in ein separates Sicherheitssteuerelement verschoben](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Umschalter zum Anzeigen von Empfehlungen in Steuerelementen oder als flache Liste hinzugefügt](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Erweitertes Sicherheitssteuerelement „Bewährte Sicherheitsmethoden implementieren“](#expanded-security-control-implement-security-best-practices)
- [Benutzerdefinierte Richtlinien mit benutzerdefinierten Metadaten sind nun allgemein verfügbar](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migration der Funktionen für die Absturzabbildanalyse zur Erkennung dateiloser Angriffe](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regeln zur Warnungsunterdrückung (Vorschau)

Dieses neue Feature (derzeit in der Vorschauphase) trägt zur Reduzierung der „Warnungsmüdigkeit“ bei. Verwenden Sie Regeln, damit Warnungen, die auf harmlose Fehler hinweisen oder sich auf normale Aktivitäten Ihrer Organisation beziehen, automatisch ausgeblendet werden. Auf diese Weise können Sie sich auf die relevantesten Bedrohungen konzentrieren. 

Warnungen, die den aktivierten Unterdrückungsregeln entsprechen, werden zwar weiterhin generiert, aber ihr Status lautet „Geschlossen“. Der Status wird im Azure-Portal bzw. an dem Ort angezeigt, über den Sie auf die Security Center-Sicherheitswarnungen zugreifen.

Mit Unterdrückungsregeln werden die Kriterien definiert, nach denen Warnungen automatisch geschlossen werden sollen. Normalerweise verwenden Sie in folgenden Fällen eine Unterdrückungsregel:

- Unterdrücken von Warnungen, die Sie als falsch positiv identifiziert haben

- Unterdrücken von Warnungen, die zu oft ausgelöst werden und daher nicht nützlich sind

Weitere Informationen finden Sie unter [Unterdrücken von Warnungen aus dem Threat Protection-Modul von Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Sicherheitsrisikobewertung für virtuelle Computer ist jetzt allgemein verfügbar

Der Standardtarif von Security Center enthält jetzt eine integrierte Sicherheitsrisikobewertung, für die keine zusätzlichen Gebühren anfallen. Diese Erweiterung basiert auf Qualys, aber die Ergebnisse werden direkt an Security Center gemeldet. Sie benötigen keine Qualys-Lizenz und auch kein Qualys-Konto – alles erfolgt nahtlos innerhalb von Security Center.

Mit der neuen Lösung können Ihre virtuellen Computer fortlaufend gescannt werden, um Sicherheitsrisiken zu ermitteln, und die Ergebnisse werden in Security Center bereitgestellt. 

Verwenden Sie zum Bereitstellen der Lösung die neue Sicherheitsempfehlung:

„Integrierte Lösung zur Sicherheitsrisikobewertung (unterstützt von Qualys) auf virtuellen Computern aktivieren“

Informieren Sie sich über die [integrierte Sicherheitsrisikobewertung für virtuelle Computer von Security Center](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Änderungen am JIT-VM-Zugriff (Just-In-Time)

Security Center enthält ein optionales Feature zum Schützen der Verwaltungsports Ihrer virtuellen Computer. Dies ist eine Verteidigungsmaßnahme gegen die häufigste Form von Brute-Force-Angriffen.

Mit diesem Update werden die folgenden Änderungen an diesem Feature vorgenommen:

- Die Empfehlung, in der Ihnen zur JIT-Aktivierung auf einer VM geraten wird, wurde umbenannt. „Just-In-Time-Netzwerkzugriffssteuerung auf virtuelle Computer anwenden“ wurde geändert in: „Verwaltungsports virtueller Computer müssen mit der Just-In-Time-Netzwerkzugriffssteuerung geschützt werden“.

- Die Empfehlung wird nur ausgelöst, wenn offene Verwaltungsports vorhanden sind.

Erfahren Sie mehr zum [Feature für JIT-Zugriff](just-in-time-access-usage.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Benutzerdefinierte Empfehlungen wurden in ein separates Sicherheitssteuerelement verschoben

Eine Sicherheitssteuerung, die im Rahmen der erweiterten Sicherheitsbewertung eingeführt wurde, ist „Best Practices für die Sicherheit implementieren“. Alle benutzerdefinierten Empfehlungen, die für Ihre Abonnements erstellt wurden, wurden automatisch in dieses Steuerelement eingefügt. 

Um Ihnen die Suche nach Ihren benutzerdefinierten Empfehlungen zu erleichtern, haben wir diese in das dedizierte Sicherheitssteuerelement „Benutzerdefinierte Empfehlungen“ verschoben. Dieses Steuerelement wirkt sich nicht auf Ihre Sicherheitsbewertung aus.

Weitere Informationen zu Sicherheitssteuerelementen finden Sie unter [Erweiterte Sicherheitsbewertung (Vorschau) in Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Umschalter zum Anzeigen von Empfehlungen in Steuerelementen oder als flache Liste hinzugefügt

Bei Sicherheitssteuerelementen handelt es sich um logische Gruppen mit zusammengehörigen Sicherheitsempfehlungen. Diese spiegeln Ihre anfälligen Angriffsflächen wider. Eine Sicherheitskontrolle umfasst eine Reihe von Sicherheitsempfehlungen mit Anweisungen, mit denen Sie diese Empfehlungen implementieren können.

Um sofort zu sehen, wie gut jede einzelne Angriffsfläche in Ihrer Organisation geschützt ist, sehen Sie sich die Bewertungen für die einzelnen Sicherheitskontrollen an.

Ihre Empfehlungen werden standardmäßig in den Sicherheitssteuerelementen angezeigt. Ab diesem Update können Sie sie auch als Liste anzeigen. Verwenden Sie den neuen Umschalter, mit dem Sie „Nach Steuerelementen gruppieren“ können, um sie als einfache Liste anzuzeigen, die nach dem Integritätsstatus der betroffenen Ressourcen sortiert ist. Der Umschalter befindet sich oberhalb der Liste im Portal.

Die Sicherheitssteuerelemente – und dieser Umschalter – sind Teil der neuen Benutzeroberfläche für die Sicherheitsbewertung. Denken Sie daran, uns über das Portal Ihr Feedback hierzu zu senden.

Weitere Informationen zu Sicherheitssteuerelementen finden Sie unter [Erweiterte Sicherheitsbewertung (Vorschau) in Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Umschalter „Nach Kontrollen gruppieren“ für Empfehlungen.":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Erweitertes Sicherheitssteuerelement „Bewährte Sicherheitsmethoden implementieren“ 

Eine Sicherheitssteuerung, die im Rahmen der erweiterten Sicherheitsbewertung eingeführt wurde, ist „Best Practices für die Sicherheit implementieren“. Wenn eine Empfehlung in diesem Steuerelement angeordnet ist, wirkt sie sich nicht auf die Sicherheitsbewertung aus. 

Mit diesem Update wurden drei Empfehlungen aus den Steuerelementen, in denen sie ursprünglich angeordnet waren, in dieses Steuerelement für die bewährte Vorgehensweise verschoben. Wir haben dies durchgeführt, weil wir festgestellt haben, dass das Risiko bei diesen drei Empfehlungen niedriger als anfänglich gedacht ist.

Außerdem wurden zwei neue Empfehlungen eingeführt und diesem Steuerelement hinzugefügt.

Die folgenden drei Empfehlungen wurden verschoben:

- **Für Konten mit Leseberechtigungen für Ihr Abonnement muss MFA aktiviert sein** (ursprünglich im Steuerelement „MFA aktivieren“ enthalten)
- **Externe Konten mit Leseberechtigungen müssen aus Ihrem Abonnement entfernt werden** (ursprünglich im Steuerelement „Zugriff und Berechtigungen verwalten“ enthalten)
- **Für Ihr Abonnement dürfen maximal 3 Besitzer festgelegt werden** (ursprünglich im Steuerelement „Zugriff und Berechtigungen verwalten“ enthalten)

Die beiden folgenden neuen Empfehlungen wurden dem Steuerelement hinzugefügt:

- **Die Erweiterung Guest Configuration sollte auf Windows-VMs installiert sein (Vorschau)** : [Guest Configuration von Azure Policy](../governance/policy/concepts/guest-configuration.md) ermöglicht auf virtuellen Computern einen Einblick in Server- und Anwendungseinstellungen (nur Windows).

- **Windows Defender Exploit Guard sollte auf Ihren Computern aktiviert sein (Vorschau)** : Für Windows Defender Exploit Guard wird der Guest Configuration-Agent von Azure Policy genutzt. Exploit Guard verfügt über vier Komponenten für die Absicherung von Geräten gegen viele verschiedene Angriffsvektoren und Blockierungsverhalten, mit denen bei Angriffen mit Schadsoftware häufig zu rechnen ist. Darüber hinaus ermöglichen diese Komponenten es Unternehmen, die richtige Balance zwischen Sicherheitsrisiken und Produktivitätsanforderungen zu finden (nur Windows).

Weitere Informationen zu Windows Defender Exploit Guard finden Sie unter [Erstellen und Bereitstellen einer Exploit Guard-Richtlinie](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Weitere Informationen zu Sicherheitssteuerelementen finden Sie unter [Erweiterte Sicherheitsbewertung (Vorschau)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Benutzerdefinierte Richtlinien mit benutzerdefinierten Metadaten sind nun allgemein verfügbar

Benutzerdefinierte Richtlinien sind nun Bestandteil der Security Center-Oberfläche mit den Empfehlungen, der Sicherheitsbewertung und des Dashboards mit den Standards zur Einhaltung gesetzlicher Bestimmungen. Dieses Feature ist jetzt allgemein verfügbar und ermöglicht es Ihnen, die Abdeckung in Bezug auf die Sicherheitsbewertung Ihrer Organisation in Security Center zu erweitern. 

Erstellen Sie in Azure Policy eine benutzerdefinierte Initiative, und fügen Sie ihr Richtlinien hinzu. Führen Sie anschließend das Onboarding in Azure Security Center und die Visualisierung in Form von Empfehlungen durch.

Wir haben jetzt auch die Option zum Bearbeiten der benutzerdefinierten Metadaten für die Empfehlungen hinzugefügt. Zu den Metadatenoptionen zählen Schweregrad, Problembehandlungsschritte, Bedrohungsinformationen und mehr.  

Informieren Sie sich über das [Verbessern der benutzerdefinierten Empfehlungen mit ausführlichen Informationen](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migration der Funktionen für die Absturzabbildanalyse zur Erkennung dateiloser Angriffe 

Wir integrieren die Erkennungsfunktionen der Windows-Absturzabbildanalyse mit der [Erkennung dateiloser Angriffe](defender-for-servers-introduction.md#what-are-the-benefits-of-microsoft-defender-for-servers). Die Analyse zur Erkennung dateiloser Angriffe verfügt über verbesserte Versionen der folgenden Sicherheitswarnungen für Windows-Computer: „Codeinjektion erkannt“, „Windows-Maskerademodul erkannt“, „Shellcode erkannt“ und „Verdächtiges Codesegment erkannt“.

Diese Umstellung hat u. a. folgende Vorteile:

- **Proaktive und rechtzeitige Erkennung von Schadsoftware**: Bei der Absturzabbildanalyse wurde gewartet, bis es zu einem Absturz gekommen ist, und erst dann wurde die Analyse durchgeführt, um bösartige Artefakte zu ermitteln. Bei der Erkennung von dateilosen Angriffen werden In-Memory-Bedrohungen proaktiv während der Ausführung identifiziert. 

- **Erweiterte Warnungen**: Die Sicherheitswarnungen bei der Erkennung von dateilosen Angriffen verfügen über erweiterte Elemente, die bei der Absturzabbildanalyse nicht vorhanden sind, z. B. Informationen zu aktiven Netzwerkverbindungen. 

- **Warnungsaggregation**: Wenn bei der Absturzabbildanalyse in einem Absturzabbild mehrere Angriffsmuster erkannt wurden, wurden mehrere Sicherheitswarnungen ausgelöst. Bei der Erkennung von dateilosen Angriffen werden alle identifizierten Angriffsmuster eines Prozesses in einer Warnung zusammengefasst, damit nicht mehrere Warnungen korreliert werden müssen.

- **Verringerung der Anforderungen in Ihrem Log Analytics-Arbeitsbereich**: Absturzabbilder mit potenziell vertraulichen Daten werden nicht mehr in Ihren Log Analytics-Arbeitsbereich hochgeladen.






## <a name="april-2020"></a>April 2020

Zu den Updates im April gehören:
- [Dynamische Compliancepakete sind jetzt allgemein verfügbar](#dynamic-compliance-packages-are-now-generally-available)
- [Empfehlungen zur Identität sind in Azure Security Center jetzt im Free-Tarif enthalten](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamische Compliancepakete sind jetzt allgemein verfügbar

Das Azure Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen enthält jetzt **dynamische Compliancepakete** (allgemein verfügbar), um weitere branchenbezogene und gesetzliche Standards nachverfolgen zu können.

Dynamische Compliancepakete können über die Security Center-Seite mit den Sicherheitsrichtlinien Ihrem Abonnement oder Ihrer Verwaltungsgruppe hinzugefügt werden. Nachdem Sie das Onboarding für einen Standard oder einen Benchmarkwert durchgeführt haben, wird der Standard in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen mit allen zugeordneten Compliancedaten in Form von Bewertungen angezeigt. Ein zusammenfassender Bericht für alle Standards, für die das Onboarding durchgeführt wurde, wird als Download bereitgestellt.

Sie können nun beispielsweise folgende Standards hinzufügen:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official und UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (neu)** (eine vollständigere Darstellung von Azure CIS 1.1.0)

Zusätzlich haben wir vor Kurzem den [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction) hinzugefügt. Dies sind von Microsoft erstellte Azure-spezifische Richtlinien zu den bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Compliance-Frameworks basieren. Weitere Standards werden im Dashboard unterstützt, sobald sie verfügbar sind.  
 
Erfahren Sie mehr zum [Aktualisieren auf dynamische Compliancepakete in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Empfehlungen zur Identität sind in Azure Security Center jetzt im Free-Tarif enthalten

Sicherheitsempfehlungen zur Identität und zum Zugriff sind im Free-Tarif von Azure Security Center nun allgemein verfügbar. Dies ist Teil der Maßnahme, mit der die Features für die Verwaltung des Cloudsicherheitsstatus kostenlos zugänglich gemacht werden sollen. Bisher waren diese Empfehlungen nur im Standard-Tarif verfügbar.

Beispiele für Empfehlungen zur Identität und zum Zugriff sind:

- „Für Konten mit Besitzerberechtigungen für Ihr Abonnement muss MFA aktiviert sein“
- „Für Ihr Abonnement dürfen maximal 3 Besitzer festgelegt werden“
- „Veraltete Konten müssen aus Ihrem Abonnement entfernt werden“

Bei Abonnements mit Free-Tarif wirkt sich diese Änderung auf deren Sicherheitsbewertungen aus, weil die Sicherheit in Bezug auf die Identität und den Zugriff hierfür bisher noch nicht bewertet wurde.

Weitere Informationen finden Sie in den [Empfehlungen zur Identität und zum Zugriff](recommendations-reference.md#recs-identityandaccess).

Weitere Informationen finden Sie unter [Verwalten der MFA-Erzwingung (mehrstufige Authentifizierung) für Ihre Abonnements](multi-factor-authentication-enforcement.md).



## <a name="march-2020"></a>März 2020

Zu den Updates im März gehören:

- [Workflowautomatisierung ist nun allgemein verfügbar](#workflow-automation-is-now-generally-available)
- [Integration von Azure Security Center mit Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Schutz für Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Verbesserte Just-In-Time-Umgebung](#improved-just-in-time-experience)
- [Zwei Sicherheitsempfehlungen für Webanwendungen als veraltet gekennzeichnet](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Workflowautomatisierung ist nun allgemein verfügbar

Das Feature „Workflowautomatisierung“ von Azure Security Center ist jetzt allgemein verfügbar. Sie können es verwenden, um Logic Apps bei Sicherheitswarnungen und Empfehlungen automatisch auszulösen. Darüber hinaus sind manuelle Trigger für Warnungen und alle Empfehlungen verfügbar, für die die „Schnellkorrektur“ verfügbar ist.

Jedes Sicherheitsprogramm umfasst mehrere Workflows für die Reaktion auf Vorfälle. Diese Prozesse können das Benachrichtigen relevanter Stakeholder, das Starten eines Change Management-Prozesses und das Anwenden spezifischer Korrekturschritte umfassen. Sicherheitsexperten empfehlen, möglichst viele Schritte dieser Verfahren zu automatisieren. Durch die Automatisierung wird der Aufwand reduziert. Außerdem können Sie die Sicherheit erhöhen, indem Sie sicherstellen, dass die Prozessschritte schnell, einheitlich und gemäß Ihren vordefinierten Anforderungen ausgeführt werden.

Weitere Informationen zu den automatischen und manuellen Security Center-Funktionen zum Ausführen von Workflows finden Sie unter [Workflowautomatisierung](workflow-automation.md).

Erfahren Sie mehr zum [Erstellen von Logik-Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integration von Azure Security Center mit Windows Admin Center

Es ist jetzt möglich, Ihre lokalen Windows-Server aus Windows Admin Center direkt in Azure Security Center zu verschieben. Security Center wird somit zu Ihrer zentralen Benutzeroberfläche für die Anzeige von Sicherheitsinformationen für Ihre gesamten Windows Admin Center-Ressourcen, z. B. lokale Server, virtuelle Computer und weitere PaaS-Workloads.

Nach dem Verschieben eines Servers aus Windows Admin Center in Azure Security Center haben Sie folgende Möglichkeiten:

- Anzeigen von Sicherheitswarnungen und -empfehlungen in der Security Center-Erweiterung von Windows Admin Center
- Anzeigen des Sicherheitsstatus und Abrufen weiterer detaillierter Informationen zu Ihren mit Windows Admin Center verwalteten Servern in Security Center im Azure-Portal (oder über eine API)

Erfahren Sie mehr zum [Integrieren von Azure Security Center mit Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Schutz für Azure Kubernetes Service

Für Azure Security Center werden die Containersicherheitsfeatures als Schutz für Azure Kubernetes Service (AKS) erweitert.

Die beliebte Open-Source-Plattform Kubernetes wurde so stark genutzt, dass sie zu einem Branchenstandard für die Containerorchestrierung geworden ist. Trotz dieser weit verbreiteten Implementierung gibt es immer noch Defizite, was das Schützen einer Kubernetes-Umgebung betrifft. Zum Verteidigen der Angriffsflächen einer Containeranwendung sind bestimmte Kenntnisse erforderlich, um sicherstellen zu können, dass die Infrastruktur sicher konfiguriert ist und ständig auf potenzielle Bedrohungen überwacht wird.

Die Verteidigung für Security Center umfasst Folgendes:

- **Ermittlung und Transparenz**: Kontinuierliche Ermittlung von verwalteten AKS-Instanzen in den für Security Center registrierten Abonnements.
- **Sicherheitsempfehlungen**: Direkt umsetzbare Empfehlungen, damit Sie für AKS die bewährten Methoden in Bezug auf die Sicherheit anwenden können. Diese Empfehlungen sind in Ihrer Sicherheitsbewertung enthalten, um dafür zu sorgen, dass sie als Teil des Sicherheitsstatus Ihres Unternehmens angesehen werden. Ein Beispiel für eine Empfehlung mit AKS-Bezug, die ggf. angezeigt wird, ist „Die rollenbasierte Zugriffssteuerung sollte genutzt werden, um den Zugriff auf einen Kubernetes Service-Cluster einzuschränken“.
- **Bedrohungsschutz**: Security Center analysiert Ihre AKS-Bereitstellung ständig und warnt Sie vor Bedrohungen und schädlichen Aktivitäten, die auf dem Host und auf der AKS-Clusterebene erkannt werden.

Erfahren Sie mehr zur [Integration von Security Center in Azure Kubernetes Service](defender-for-kubernetes-introduction.md).

Erfahren Sie mehr zu den [Containersicherheitsfeatures von Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Verbesserte Just-In-Time-Umgebung

Die Features, der Betrieb und die Benutzeroberfläche der Just-In-Time-Tools von Azure Security Center, mit denen Ihre Verwaltungsports geschützt werden, wurden wie folgt verbessert: 

- **Feld für Begründung**: Beim Anfordern des Zugriffs auf einen virtuellen Computer (VM) über die Just-In-Time-Seite des Azure-Portals ist ein neues optionales Feld verfügbar, in dem eine Begründung für die Anforderung eingegeben werden kann. Die in diesem Feld eingegebenen Informationen können im Aktivitätsprotokoll nachverfolgt werden. 
- **Automatische Bereinigung von redundanten Just-In-Time-Regeln (JIT)** : Bei jedem Update einer JIT-Richtlinie wird automatisch ein Bereinigungstool ausgeführt, um die Gültigkeit des gesamten Regelsatzes zu überprüfen. Das Tool sucht nach Konflikten zwischen den Regeln in Ihrer Richtlinie und den Regeln in der NSG. Wenn das Bereinigungstool einen Konflikt feststellt, ermittelt es die Ursache und entfernt integrierte Regeln, die nicht mehr benötigt werden – sofern dies auf sichere Weise möglich ist. Der Cleaner löscht niemals Regeln, die Sie erstellt haben. 

Erfahren Sie mehr zum [Feature für JIT-Zugriff](just-in-time-access-usage.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Zwei Sicherheitsempfehlungen für Webanwendungen als veraltet gekennzeichnet

Zwei Sicherheitsempfehlungen zu Webanwendungen wurden als veraltet gekennzeichnet: 

- Regeln für Webanwendungen in IaaS-NSGs müssen verstärkt werden.
    (Zugehörige Richtlinie: Für Webanwendungen in IaaS müssen die NSG-Regeln verstärkt werden)

- Zugriff auf App Services muss eingeschränkt sein.
    (Zugehörige Richtlinie: Zugriff auf App Services muss eingeschränkt sein [Vorschau])

Diese Empfehlungen werden nicht mehr in der Security Center-Liste mit den Empfehlungen angezeigt. Die zugehörigen Richtlinien sind nicht mehr in der Initiative mit dem Namen „Security Center-Standardrichtlinie“ enthalten.

Erfahren Sie mehr über [Sicherheitsempfehlungen](recommendations-reference.md).




## <a name="february-2020"></a>Februar 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Erkennung von dateilosen Angriffen für Linux (Vorschau)

Da Angreifer immer häufiger verdeckte Methoden nutzen, um einer Entdeckung zu entgehen, wird die Erkennung von dateilosen Angriffen für Azure Security Center zusätzlich zu Windows auch auf Linux erweitert. Bei dateilosen Angriffen werden Sicherheitsrisiken in der Software ausgenutzt, schädliche Nutzlasten in unkritische Systemprozesse eingeschleust und Angriffe im Arbeitsspeicher verborgen. Diese Vorgehensweise soll Folgendes bewirken:

- Verringerung oder Beseitigung der Spuren von Schadsoftware auf Datenträgern
- Erhebliche Verringerung der Wahrscheinlichkeit, dass Angreifer von datenträgerbasierten Schadsoftware-Scanlösungen erkannt werden

Als Reaktion auf diese Bedrohung wurde die Azure Security Center-Erkennung von dateilosen Angriffen für Windows im Oktober 2018 veröffentlicht und nun auch auf Linux ausgeweitet. 



## <a name="january-2020"></a>Januar 2020

### <a name="enhanced-secure-score-preview"></a>Erweiterte Sicherheitsbewertung (Vorschau)

Eine erweiterte Version der Sicherheitsbewertung von Azure Security Center ist jetzt verfügbar und befindet sich in der Vorschauphase. Bei dieser Version werden mehrere Empfehlungen zu Sicherheitssteuerelementen gruppiert, die Ihre anfälligen Angriffsflächen besser widerspiegeln (z. B. Einschränkung des Zugriffs auf Verwaltungsports).

Machen Sie sich mit den Änderungen vertraut, die während der Vorschauphase für die Sicherheitsbewertung vorgenommen werden, und ermitteln Sie andere Lösungen, mit denen Sie Ihre Umgebung noch besser schützen können.

Erfahren Sie mehr über die [erweiterte Sicherheitsbewertung (Vorschau)](secure-score-security-controls.md).



## <a name="november-2019"></a>November 2019

Updates im November:
 - [Threat Protection für Azure Key Vault in Nordamerika (Vorschau)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Threat Protection für Azure Storage mit zuverlässigem Malwarescreening](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Workflowautomatisierung mit Azure Logic Apps (Vorschau)](#workflow-automation-with-logic-apps-preview)
 - [Ressourcenübergreifende schnelle Problembehebung allgemein verfügbar](#quick-fix-for-bulk-resources-generally-available)
 - [Überprüfen von Containerimages auf Sicherheitsrisiken (Vorschau)](#scan-container-images-for-vulnerabilities-preview)
 - [Zusätzliche Standards zur Einhaltung gesetzlicher Bestimmungen (Vorschau)](#additional-regulatory-compliance-standards-preview)
 - [Threat Protection-Unterstützung für Azure Kubernetes Service (AKS) (Vorschau)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Sicherheitsrisikobewertung für virtuelle Computer (Vorschau)](#virtual-machine-vulnerability-assessment-preview)
 - [Advanced Data Security für SQL-Server auf Azure-VMs (Vorschau)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Unterstützung für benutzerdefinierte Richtlinien (Vorschau)](#support-for-custom-policies-preview)
 - [Erweitertes Azure Security Center-Angebot: Plattform für die Community und Partner](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Erweiterte Integration über den Export von Security Center-Empfehlungen und -Warnungen (Vorschau)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Onboarding lokaler Server in Security Center über Windows Admin Center (Vorschau)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Threat Protection für Azure Key Vault in Nordamerika (Vorschau)

Azure Key Vault ist ein zum Schutz von Daten und zur Verbesserung der Leistung von Cloudanwendungen unabdingbarer Dienst, der die Möglichkeit bietet, Schlüssel, Geheimnisse, Kryptografieschlüssel und Richtlinien zentral in der Cloud zu verwalten. Da in Azure Key Vault vertrauliche und geschäftskritische Daten gespeichert werden, ist für die Schlüsseltresore und die darin gespeicherten Daten höchste Sicherheit erforderlich.

Die Unterstützung des Bedrohungsschutzes für Azure Storage in Azure Security Center bietet eine zusätzliche Ebene intelligenter Sicherheitsfunktionen, die ungewöhnliche und möglicherweise schädliche Versuche erkennen, auf Schlüsseltresore zuzugreifen und diese missbräuchlich zu nutzen. Aufgrund dieser neuen Schutzebene können Kunden auch ohne Sicherheitsexpertise oder die Verwaltung von Sicherheitsüberwachungssystemen effektiv auf Bedrohungen ihrer Schlüsseltresore reagieren. Dieses Feature ist in Nordamerika als öffentliche Vorschauversion verfügbar.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Threat Protection für Azure Storage umfasst zuverlässiges Malwarescreening

Threat Protection für Azure Storage bietet mithilfe der Analyse für Hashbewertung und verdächtige Zugriffsmuster von einem aktiven Tor-Exitknoten (anonymisierender Proxy) von Microsoft Threat Intelligence neue Funktionen zum Erkennen von Malwareuploads in Azure Storage. Sie können jetzt mithilfe des Azure Security Center in Speicherkonten entdeckte Malware anzeigen lassen.


### <a name="workflow-automation-with-logic-apps-preview"></a>Workflowautomatisierung mit Azure Logic Apps (Vorschauversion)

Organisationen mit zentral verwalteten Sicherheits- und IT-Abläufen implementieren interne Workflowprozesse, um erforderliche Aktionen innerhalb der Organisation voranzutreiben, wenn Abweichungen in der Umgebung entdeckt werden. In vielen Fällen handelt es sich bei diesen Workflows um wiederholbare Prozesse, und durch die Automatisierung können Prozesse in der Organisation deutlich optimiert werden.

Wir führen heute eine neue Funktion in Security Center ein, die Kunden unter Verwendung von Azure Logic Apps das Erstellen von Automatisierungskonfigurationen und Richtlinien ermöglicht, die basierend auf bestimmten ASC-Ergebnissen wie beispielsweise Empfehlungen oder Warnungen automatisch ausgelöst werden. Azure Logic Apps kann so konfiguriert werden, dass eine beliebige von der großen Logic App-Connectors-Community unterstützte benutzerdefinierte Aktion ausgeführt oder eine der von Security Center bereitgestellten Vorlagen wie das Senden einer E-Mail oder das Öffnen eines ServiceNow&trade;-Tickets verwendet wird.

Weitere Informationen zu den automatischen und manuellen Security Center-Funktionen zum Ausführen von Workflows finden Sie unter [Workflowautomatisierung](workflow-automation.md).

Informationen zum Erstellen von Logic Apps finden Sie unter [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Ressourcenübergreifendes schnelle Problembehebung allgemein verfügbar

Die Sicherheitsbewertung kann sehr umfangreich sein, wodurch es schwierig wird, Probleme ressourcenübergreifend zu beheben.

Mithilfe der schnellen Problembehebung werden die Vorgänge zum Beheben von fehlerhaften Sicherheitskonfigurationen vereinfacht, und Sie können schnell Empfehlungen ressourcenübergreifend umsetzen und Ihre Sicherheitsbewertung verbessern.

Mit dieser Vorgehensweise können Sie die Ressourcen auswählen, auf die Sie die Wartung anwenden möchten, und dann die Wartungsaktion starten. Die Konfiguration erfolgt automatisch, ohne dass Sie eingreifen müssen.

Die schnelle Problembehebung steht Kunden ab heute über die Seite „Security Center-Empfehlungen“ zur Verfügung.

Im [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) erfahren Sie, für welche Empfehlungen die schnelle Problembehebung aktiviert ist.


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Überprüfen von Containerimages auf Sicherheitsrisiken (Vorschauversion)

Azure Security Center kann nun Containerimages in Azure Container Registry auf Sicherheitsrisiken überprüfen.

Bei der Überprüfung von Images wird die Containerimagedatei analysiert und anschließend auf bekannte Sicherheitsrisiken überprüft (unterstützt durch Qualys).

Die Überprüfung selbst wird automatisch ausgelöst, wenn neue Containerimages per Push an Azure Container Registry übertragen werden. Erkannte Sicherheitsrisiken werden als Security Center-Empfehlungen angezeigt und sind zusammen mit Informationen zum Patchen dieser Risiken zur Reduzierung der gebotenen Angriffsfläche in der Sicherheitsbewertung enthalten.


### <a name="additional-regulatory-compliance-standards-preview"></a>Zusätzliche Standards zur Einhaltung gesetzlicher Bestimmungen (Vorschauversion)

Das Dashboard „Einhaltung gesetzlicher Bestimmungen“ bietet Erkenntnisse über Ihren Konformitätsstatus, die auf Security Center-Bewertungen basieren. Das Dashboard zeigt, inwieweit Ihre Umgebung mit den Steuerelementen und Anforderungen bestimmter gesetzlicher Standards und Branchenbenchmarks übereinstimmt und bietet ausführliche Empfehlungen zum Erfüllen dieser Anforderungen.

Das Dashboard „Einhaltung gesetzlicher Bestimmungen“ verfügt daher über vier integrierte und unterstützte Standards:  Azure CIS 1.1.0, PCI-DSS, ISO 27001 und SOC-TSP. Wir künden hiermit die öffentliche Vorschauversion zusätzlicher unterstützter Standards an: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM und UK Official zusammen mit UK NHS. Außerdem veröffentlichen wir eine aktualisierte Version von Azure CIS 1.1.0, in der weitere Steuerelemente der optimierten und der Standarderweiterbarkeit verfügbar sind.

[Weitere Informationen über das Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md)


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Threat Protection-Unterstützung für Azure Kubernetes Service (AKS) (Vorschauversion)

Kubernetes entwickelt sich in Rekordzeit zum neuen Standard für die Softwarebereitstellung und -verwaltung in der Cloud. Derzeit gibt es noch wenige Benutzer, die umfassende Erfahrungen mit Kubernetes vorweisen können. Viele konzentrieren sich auf die allgemeine Entwicklung und Verwaltung und vernachlässigen dabei den Sicherheitsaspekt. Eine Kubernetes-Umgebung muss sorgfältig konfiguriert werden, damit sie wirklich sicher ist. So wird sichergestellt, dass keine Angriffsfläche für Angriffe auf Container geboten wird. Die Unterstützung für den Containerbereich in Security Center wird um einen der am schnellsten wachsenden Dienst in Azure ausgebaut: Azure Kubernetes Service (AKS).

Die öffentliche Vorschauversion umfasst folgende Funktionen:

- **Ermittlung und Sichtbarkeit:** Continuous Discovery für verwaltete AKS-Instanzen in den registrierten Security Center-Abonnements.
- **Empfehlungen zur Sicherheitsbewertung**: Nützliche Hinweise, mit denen Kunden die Best Practices für Sicherheit in AKS einhalten und ihre Sicherheitsbewertung erhöhen können. Ein Beispiel für diese Empfehlungen lautet: „Die rollenbasierte Zugriffssteuerung sollte genutzt werden, um den Zugriff auf einen Kubernetes Service-Cluster einzuschränken“.
- **Bedrohungserkennung:** host- und clusterbasierte Analysen (z. B. „Ein privilegierter Container wurde erkannt.“).


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Sicherheitsrisikobewertung für virtuelle Computer (Vorschauversion)

Auf virtuellen Computern installierte Anwendungen sind oft anfällig für Sicherheitsrisiken, die zu einer Kompromittierung des gesamten virtuellen Computers führen können. Heute geben wir bekannt, dass der Security Center-Standard-Tarif eine kostenlose integrierte Sicherheitsrisikobewertung für virtuelle Computer enthält. Die Sicherheitsrisikobewertung in der öffentlichen Vorschauversion basiert auf Qualys und ermöglicht das kontinuierliche Scannen aller installierten Anwendungen auf einem virtuellen Computer, um anfällige Anwendungen zu finden und die Ergebnisse auf der Benutzeroberfläche des Security Center-Portal anzuzeigen. Das Security Center berücksichtigt dabei alle Bereitstellungsvorgänge, sodass der Benutzer keine weiteren Maßnahmen ergreifen muss. Für die Zukunft planen wir die Bereitstellung von Sicherheitsrisikobewertungsoptionen, um die individuellen Geschäftsanforderungen unserer Kunden zu erfüllen.

[Weitere Informationen über Sicherheitsrisikobewertungen für Ihre virtuellen Azure-Computer](deploy-vulnerability-assessment-vm.md)


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Advanced Data Security für SQL-Server auf Azure-VMs (Vorschauversion)

Die Unterstützung von Azure Security Center für die Bedrohungsschutz- und Sicherheitsrisikobewertung für SQL-Datenbanken, die auf IaaS-VMs ausgeführt werden, befindet sich jetzt in der Vorschauphase.

[Sicherheitsrisikobewertung](../azure-sql/database/sql-vulnerability-assessment.md) ist ein einfach zu konfigurierender Dienst, mit dem potenzielle Schwachstellen in der Datenbank ermittelt, nachverfolgt und behoben werden können. Sie bietet Einblicke in Ihren Sicherheitsstatus als Teil der Sicherheitsbewertung, enthält die Schritte zum Beheben von Sicherheitsproblemen und verbessert Ihre Datenbanksicherheit.

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf SQL Server zuzugreifen oder diesen zu nutzen. Sie überwacht Ihre Datenbank fortlaufend auf verdächtige Aktivitäten und stellt handlungsorientierte Sicherheitswarnungen bei anomalen Datenbankzugriffsmustern bereit. Diese Warnungen enthalten Details zur verdächtigen Aktivität sowie empfohlene Maßnahmen zur Untersuchung und Abwehr der Bedrohung.


### <a name="support-for-custom-policies-preview"></a>Unterstützung für benutzerdefinierte Richtlinien (Vorschauversion)

Azure Security Center unterstützt jetzt benutzerdefinierte Richtlinien (Vorschauphase).

Unsere Kunden haben sich gewünscht, die Abdeckung ihrer aktuellen Sicherheitsbewertungen in Security Center um ihre eigenen Sicherheitsbewertungen zu erweitern, die auf Richtlinien basieren, die sie in Azure Policy erstellen. Da nun benutzerdefinierte Richtlinien unterstützt werden, ist dies jetzt möglich.

Diese neuen Richtlinien sollen Teil der Security Center-Empfehlungen, Sicherheitsbewertung und dem Dashboard für Standards zur Einhaltung gesetzlicher Bestimmungen werden. Durch die Unterstützung benutzerdefinierter Richtlinien können Sie jetzt benutzerdefinierte Initiativen in Azure Policy erstellen, diese als Richtlinien zu Security Center hinzufügen und anschließend als Empfehlungen visualisieren.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Erweitertes Azure Security Center-Angebot: Plattform für die Community und Partner

Verwenden Sie Security Center, um Empfehlungen nicht nur von Microsoft, sondern auch aus bestehenden Lösungen von Partnern wie Check Point, Tenable und CyberArk zu erhalten, wobei viele weitere Integrationen vorgesehen sind.  Der einfache Security Center-Onboardingflow ermöglicht das Verbinden Ihrer vorhandenen Lösungen mit Security Center, sodass Sie alle Sicherheitsstatusempfehlungen an einem Ort anzeigen, vereinheitlichte Berichte ausführen und alle Security Center-Funktionen sowohl für integrierte als auch Partnerempfehlungen nutzen können. Sie können Security Center-Empfehlungen auch für Partnerprodukte exportieren.

[Weitere Informationen zur Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association)



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Erweiterte Integration über den Export von Security Center-Empfehlungen und -Warnungen (Vorschauversion)

Für Szenarios auf Unternehmensebene, die auf Security Center basieren, können jetzt Warnungen und Empfehlungen von Security Center auch außerhalb des Azure-Portals oder der Azure-API verarbeitet werden. Sie können sie direkt in einen Event Hub oder in Log Analytics-Arbeitsbereiche exportieren. Hier finden Sie Beispiele für Workflows, die Sie mithilfe dieser neuen Funktionen erstellen können:

- Durch den Export in Log Analytics-Arbeitsbereiche können Sie mithilfe von Power BI benutzerdefinierte Dashboards erstellen.
- Durch den Export in einen Event Hub können Sie Warnungen und Empfehlungen für Security Center in SIEM-Produkte von Drittanbietern, in eine Drittanbieterlösung oder in Azure Data Explorer exportieren.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Onboarding lokaler Server in Security Center über Windows Admin Center (Vorschauversion)

Windows Admin Center ist ein Verwaltungsportal für Windows-Server, die nicht in Azure bereitgestellt wurden, und bietet diesen mehrere Azure-Verwaltungsfunktionen wie Sicherungen und Systemupdates. Wir haben kürzlich eine Funktion zum Onboarding dieser Nicht-Azure-Server hinzugefügt, damit diese direkt über Windows Admin Center vom Azure Security Center geschützt werden.

Durch diese neue Funktion können Benutzer für einen WAC-Server das Onboarding für Azure Security Center durchführen und die Anzeige dessen Sicherheitswarnungen und -empfehlungen direkt über Windows Admin Center aktivieren.


## <a name="september-2019"></a>September 2019

Updates im September:

 - [Verbesserte Verwaltung von Regeln mit der adaptiven Anwendungssteuerung](#managing-rules-with-adaptive-application-controls-improvements)
 - [Steuern von Empfehlungen für die Containersicherheit mit Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Verbesserte Verwaltung von Regeln mit der adaptiven Anwendungssteuerung

Die Funktion zum Verwalten von Regeln für virtuelle Computer mithilfe der adaptiven Anwendungssteuerung wurde verbessert. Die adaptive Anwendungssteuerung ist ein Feature von Azure Security Center, mit dem Sie steuern können, welche Anwendungen auf Ihren virtuellen Computern ausgeführt werden können. Neben der allgemeinen Verbesserung der Regelverwaltung können Sie dank einer neuen Funktion nun steuern, welche Dateitypen geschützt werden, wenn Sie eine neue Regel hinzufügen.

[Weitere Informationen über Adaptive Anwendungssteuerungen](adaptive-application-controls.md)


### <a name="control-container-security-recommendation-using-azure-policy"></a>Steuern von Empfehlungen für die Containersicherheit mit Azure Policy

Die Empfehlungen von Azure Security Center zum Korrigieren von Sicherheitsrisiken bei Containern können nun mit Azure Policy aktiviert und deaktiviert werden.

Öffnen Sie zum Anzeigen der von Ihnen aktivierten Sicherheitsrichtlinien in Security Center die Seite „Sicherheitsrichtlinie“.


## <a name="august-2019"></a>August 2019

Updates im August:

 - [Just-In-Time-Zugriff (JIT) auf virtuelle Computer für Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Ein-Klick-Wartung zur Verbesserung Ihres Sicherheitsstatus (Vorschau)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Mandantenübergreifende Verwaltung](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Just-In-Time-Zugriff (JIT) auf virtuelle Computer für Azure Firewall 

Just-In-Time-Zugriff (JIT) auf virtuelle Computer für Azure Firewall ist jetzt allgemein verfügbar. Schützen Sie damit Ihre durch Azure Firewall geschützten Umgebungen zusätzlich zu Ihren durch Netzwerksicherheitsgruppen (NSG) geschützten Umgebungen.

JIT-VM-Zugriff sorgt für ein geringeres Risiko durch volumetrische Netzwerkangriffe, da mit dieser Steuerung nur der wirklich erforderliche Zugriff auf Ihre VMs gewährt wird und dabei Ihre NSG- und Azure Firewall-Regeln angewandt werden.

Beim Aktivieren von JIT für Ihre VMs erstellen Sie eine Richtlinie, die festlegt, welche Ports geschützt werden sollen, wie lange die Ports geöffnet sein sollen und von welchen genehmigten IP-Adressen aus auf diese Ports zugegriffen werden kann. Mit dieser Richtlinie behalten Sie volle Kontrolle darüber, was Ihre Benutzer ausführen dürfen, wenn sie Zugriff anfordern.

Anforderungen werden im Azure-Aktivitätsprotokoll erfasst, sodass Sie den Zugriff ganz einfach überwachen und überprüfen können. Außerdem können Sie auf der JIT-Seite sehr schnell ermitteln, auf welchen vorhandenen VMs JIT aktiviert ist und auf welchen JIT empfohlen wird.

[Weitere Informationen über Azure Firewall](../firewall/overview.md)


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Ein-Klick-Wartung zur Verbesserung Ihres Sicherheitsstatus (Vorschauversion)

Secure Score ist ein Tool, mit dem Sie den Sicherheitsstatus bewerten können. Es überprüft Ihre Sicherheitsempfehlungen und priorisiert sie für Sie, damit Sie wissen, welche Empfehlungen Sie zuerst durchführen sollten. Dies hilft Ihnen, die schwerwiegendsten Sicherheitsrisiken zu finden, um die Untersuchung priorisieren zu können.

Zur Vereinfachung der Wartung von Fehlkonfigurationen bei der Sicherheit und für eine schnelle Verbesserung Ihrer Sicherheitsbewertung haben wir außerdem eine neue Funktion hinzugefügt, mit der Sie einzelne Empfehlungen mit nur einem Klick für einen ganzen Stapel von Ressourcen ausführen können.

Mit dieser Vorgehensweise können Sie die Ressourcen auswählen, auf die Sie die Wartung anwenden möchten, und dann die Wartungsaktion starten. Die Konfiguration erfolgt automatisch, ohne dass Sie eingreifen müssen.

Im [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) erfahren Sie, für welche Empfehlungen die schnelle Problembehebung aktiviert ist.


### <a name="cross-tenant-management"></a>Mandantenübergreifende Verwaltung

Security Center unterstützt jetzt Szenarien mit mandantenübergreifender Verwaltung als Teil von Azure Lighthouse. Dadurch können Sie in Security Center mehrere Mandanten einsehen und ihren Sicherheitsstatus verwalten. 

[Weitere Informationen über mandantenübergreifende Verwaltungsmöglichkeiten](cross-tenant-management.md)


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Updates für Netzwerkempfehlungen

Azure Security Center (ASC) hat neue Netzwerkempfehlungen veröffentlicht und bestehende verbessert. Damit trägt Security Center noch mehr zum Schutz Ihres Netzwerks und Ihrer Ressourcen bei. 

[Weitere Informationen über Netzwerkempfehlungen](recommendations-reference.md#recs-networking)


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Adaptive Netzwerkhärtung allgemein verfügbar

Eine der größten Angriffsflächen für Workloads, die in der öffentlichen Cloud ausgeführt werden, sind Verbindungen mit und aus dem öffentlichen Internet. Unsere Kunden finden es schwierig zu entscheiden, welche Netzwerksicherheitsgruppenregeln gelten sollten, damit Azure-Workloads auch wirklich nur für die erforderlichen Quellbereiche verfügbar sind. Mit diesem Feature werden der Netzwerkdatenverkehr und Verbindungsmuster von Azure-Workloads von Security Center analysiert und Empfehlungen für Netzwerksicherheitsgruppenregeln für VMs gemacht, die mit dem Internet verbunden sind. Dadurch können Kunden ihre Netzwerkzugriffsrichtlinien besser konfigurieren und die Anfälligkeit für Angriffe minimieren. 

[Weitere Informationen über die adaptive Netzwerkhärtung](adaptive-network-hardening.md)
