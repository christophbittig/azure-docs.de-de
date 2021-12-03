---
title: Verstehen der erweiterten Sicherheitsfunktionen von Microsoft Defender für Cloud
description: Erfahren Sie mehr über die Vorteile der Aktivierung erweiterter Sicherheit in Microsoft Defender für Cloud
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: overview
ms.date: 11/14/2021
ms.openlocfilehash: 4f17d5228bdac13797aaef0fe4441f61d474042a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557476"
---
# <a name="microsoft-defender-for-clouds-enhanced-security-features"></a>Die erweiterten Sicherheitsfunktionen von Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Die erweiterten Sicherheitsfunktionen sind in den ersten 30 Tagen kostenlos. Wenn Sie sich nach 30 Tagen entscheiden, den Dienst weiter zu nutzen, wird die Nutzung automatisch in Rechnung gestellt.

Sie können das Upgrade von der Seite **Umgebungseinstellungen** aus durchführen, wie in [Schnellstart beschrieben: Erweiterte Sicherheitsfunktionen aktivieren](enable-enhanced-security.md). Einzelheiten zu den Preisen in der von Ihnen gewählten Währung und je nach Region finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/).

:::image type="content" source="media/enhanced-security-features-overview/defender-plans-top.png" alt-text="Aktivierung der erweiterten Sicherheitsfunktionen von Microsoft Defender für Cloud.":::

## <a name="what-are-the-benefits-of-enabling-enhanced-security-features"></a>Was sind die Vorteile der Aktivierung erweiterter Sicherheitsfunktionen?

Defender für Cloud wird in zwei Modi angeboten:

- **Ohne erweiterte Sicherheitsfunktionen** (Kostenlos): Defender für Cloud wird kostenlos für alle Ihre Azure-Abonnements aktiviert, wenn Sie das Dashboard für den Workloadschutz im Azure-Portal zum ersten Mal besuchen oder wenn die Aktivierung programmatisch über die API erfolgt. Mit diesem kostenlosen Modus erhalten Sie die Sicherheitsbewertung und die damit verbundenen Funktionen: Sicherheitsrichtlinien, kontinuierliche Sicherheitsbewertung und umsetzbare Sicherheitsempfehlungen, die Sie beim Schutz Ihrer Azure-Ressourcen unterstützen.

- **Defender für Cloud mit allen erweiterten Sicherheitsfunktionen** - Die Aktivierung der erweiterten Sicherheitsfunktionen erweitert die Funktionen des kostenlosen Modus auf Workloads, die in privaten und anderen öffentlichen Clouds ausgeführt werden, und bietet ein einheitliches Sicherheitsmanagement und Schutz vor Bedrohungen für Ihre Hybrid-Cloud-Workloads. Einige der wichtigsten Vorteile sind:

    - **Microsoft Defender für Endpunkt** - Microsoft Defender für Server enthält [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender) für umfassende Endpunkt-Erkennung und -Reaktion (EDR). Erfahren Sie mehr über die Vorteile der Verwendung von Microsoft Defender für Endpoint zusammen mit Defender für Cloud in [Nutzen Sie die integrierte EDR-Lösung von Defender für Cloud](integration-defender-for-endpoint.md).
    - **Risikosicherheitsbewertung für virtuelle Computer, Containerregistrierungen und SQL-Ressourcen:** Aktivieren Sie einfach Lösungen zur Sicherheitsrisikobewertung, um Sicherheitsrisiken zu ermitteln, zu verwalten und zu beheben. Zeigen Sie die Ergebnisse direkt in Defender für Cloud an, untersuchen Sie sie, und beheben Sie sie.
    - **Multi-Cloud-Sicherheit** - Verbinden Sie Ihre Konten von Amazon Web Services (AWS) und Google Cloud Platform (GCP), um Ressourcen und Arbeitslasten auf diesen Plattformen mit einer Reihe von Microsoft Defender für Cloud-Sicherheitsfunktionen zu schützen.
    - **Hybridsicherheit:** Verschaffen Sie sich einen einheitlichen Überblick über die Sicherheit sämtlicher lokaler und cloudbasierter Workloads. Wenden Sie Sicherheitsrichtlinien an, und bewerten Sie kontinuierlich die Sicherheit Ihrer Hybridcloud-Workloads, um die Einhaltung von Sicherheitsstandards zu gewährleisten. Sammeln, durchsuchen und analysieren Sie Sicherheitsdaten aus zahlreichen Quellen (einschließlich Firewalls und Partnerlösungen).
    - **Bedrohungsschutzwarnungen:** Mit erweiterten Verhaltensanalysen und Microsoft Intelligent Security Graph sind Sie neuen Arten von Cyberangriffen immer einen Schritt voraus. Mit integrierten Verhaltensanalysen und Machine Learning können Angriffe und Zero-Day-Exploits erkannt werden. Überwachen Sie Netzwerke, Computer, Datenspeicher (SQL Server-Instanzen, die innerhalb und außerhalb von Azure gehostet werden, Azure SQL-Datenbanken, Azure SQL Managed Instance und Azure Storage) sowie Clouddienste auf eingehende Angriffe und Aktivitäten nach Sicherheitsverletzungen. Optimieren Sie die Untersuchung mit interaktiven Tools und kontextbezogenen Informationen zu Bedrohungen.
    - **Überwachung der Einhaltung einer Reihe von Standards** - Defender für Cloud bewertet kontinuierlich Ihre Hybrid-Cloud-Umgebung, um die Risikofaktoren gemäß den Kontrollen und Best Practices in [Azure Security Benchmark](/security/benchmark/azure/introduction) zu analysieren. Wenn Sie die erweiterten Sicherheitsfunktionen aktivieren, können Sie je nach den Anforderungen Ihres Unternehmens eine Reihe anderer Industriestandards, gesetzlicher Normen und Benchmarks anwenden. Über das [Dashboard zur Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md) können Sie Standards hinzufügen und die Konformität nachverfolgen.
    - **Zugriffs- und Anwendungskontrollen:** Blockieren Sie Schadsoftware und andere unerwünschte Anwendungen, indem Sie durch Machine Learning unterstützte Empfehlungen anwenden, die auf Ihre spezifischen Workloads abgestimmt sind, um Positiv- und Sperrlisten zu erstellen. Verringern Sie die Angriffsfläche im Netzwerk mit kontrolliertem Just-in-Time-Zugriff auf Verwaltungsports auf Azure Virtual Machines. Zugriffs- und Anwendungskontrollen reduzieren die Anfälligkeit für Brute-Force- und andere Netzwerkangriffe drastisch.
    - **Containersicherheitsfeatures**: Profitieren Sie in Ihren Containerumgebungen von der Handhabung von Sicherheitsrisiken und des Schutzes vor Bedrohungen in Echtzeit. Die Gebühren basieren auf der Anzahl der eindeutigen Containerimages, die in Ihre verbundene Registrierung gepusht werden. Nachdem ein Image ein Mal gescannt wurde, wird es nicht mehr in Rechnung gestellt, es sei denn, es wird geändert und noch einmal gepusht.
    - **Umfassender Schutz vor Bedrohungen für Ressourcen, die mit Azure verbunden sind:** Cloudnativer Bedrohungsschutz für die Azure-Dienste, die für alle Ihre Ressourcen gelten: Azure Resource Manager, Azure DNS, Azure Network Layer und Azure Key Vault. Defender für Cloud hat einen einzigartigen Einblick in die Azure-Verwaltungsebene und die Azure-DNS-Ebene und kann daher Cloud-Ressourcen schützen, die mit diesen Ebenen verbunden sind.


## <a name="faq---pricing-and-billing"></a>Häufig gestellte Fragen: Preise und Abrechnung 

- [Wie kann ich nachverfolgen, wer in meiner Organisation einen Microsoft Defender-Plan in Defender für Cloud aktiviert hat?](#how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud)
- [Welche Pläne bietet Defender für Cloud?](#what-are-the-plans-offered-by-defender-for-cloud)
- [Wie aktiviere ich die erweiterte Sicherheit von Defender für Cloud für mein Abonnement?](#how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription)
- [Kann ich Microsoft Defender für Server auf einer Untergruppe von Servern in meinem Abonnement aktivieren?](#can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Wenn ich bereits eine Lizenz für Microsoft Defender für Endpoint besitze, kann ich dann einen Rabatt für Defender für servers erhalten?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers)
- [Mein Abonnement hat Microsoft Defender für Server aktiviert, muss ich für nicht laufende Server bezahlen?](#my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Werden mir für Computer, auf denen der Log Analytics-Agent nicht installiert ist, Kosten berechnet?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, werden mir dann zweimal Kosten berechnet?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, sind die kostenlosen 500 MB für die Datenerfassung dann für alle Arbeitsbereiche verfügbar?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Wird die kostenlose Datenerfassung von 500 MB für einen gesamten Arbeitsbereich oder ausschließlich pro Computer berechnet?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Welche Datentypen sind im täglichen Datenkontingent von 500 MB enthalten?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud"></a>Wie kann ich nachverfolgen, wer in meiner Organisation einen Microsoft Defender-Plan in Defender für Cloud aktiviert hat?
In einem Azure-Abonnement gibt es möglicherweise mehrere Administratoren mit Berechtigungen zum Ändern der Tarifeinstellungen. Verwenden Sie das Azure-Aktivitätsprotokoll, um herauszufinden, welcher Benutzer eine Änderung vorgenommen hat.

:::image type="content" source="media/enhanced-security-features-overview/logged-change-to-pricing.png" alt-text="Azure-Aktivitätsprotokoll mit einem Preisänderungsereignis":::

Wenn die Informationen des Benutzers nicht in der Spalte **Ereignis initiiert von** aufgeführt sind, sollten Sie sich in den JSON-Daten des Ereignisses die relevanten Details ansehen.

:::image type="content" source="media/enhanced-security-features-overview/tracking-pricing-changes-in-activity-log.png" alt-text="Azure-Aktivitätsprotokoll: JSON-Explorer":::


### <a name="what-are-the-plans-offered-by-defender-for-cloud"></a>Welche Pläne werden von Defender für Cloud angeboten? 
Das kostenlose Angebot von Microsoft Defender für Cloud bietet die Sicherheitsbewertung und die dazugehörigen Tools. Durch die Aktivierung der erweiterten Sicherheit werden alle Microsoft Defender-Pläne aktiviert, um eine Reihe von Sicherheitsvorteilen für alle Ihre Ressourcen in Azure-, Hybrid- und Multi-Cloud-Umgebungen zu bieten.  

### <a name="how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription"></a>Wie aktiviere ich die erweiterte Sicherheit von Defender für Cloud für mein Abonnement? 
Sie können eine der folgenden Möglichkeiten nutzen, um erweiterte Sicherheit für Ihr Abonnement zu aktivieren: 

| Methode                                          | Anweisungen                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Defender für Cloud-Seiten des Azure-Portals    | [Erweiterte Schutzmaßnahmen aktivieren](enable-enhanced-security.md)                                                                                         |
| REST-API                                        | [Pricings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Bundle-Preise](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Kann ich Microsoft Defender für Server für eine Untergruppe von Servern in meinem Abonnement aktivieren?
Nein. Wenn Sie [Microsoft Defender für Server](defender-for-servers-introduction.md) für ein Abonnement aktivieren, werden alle Computer im Abonnement durch Defender für Server geschützt.

Eine Alternative ist die Aktivierung von Microsoft Defender für Server auf der Ebene des Log Analytics-Arbeitsbereichs. Wenn Sie dies tun, werden nur Server, die Daten an diesen Arbeitsbereich melden, geschützt und in Rechnung gestellt. Mehrere Funktionen sind aber nicht verfügbar. Hierzu zählen Just-In-Time-VM-Zugriff, Netzwerkerkennungen, Einhaltung gesetzlicher Bestimmungen, adaptive Netzwerkhärtung, adaptive Anwendungssteuerung und mehr. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers"></a>Wenn ich bereits eine Lizenz für Microsoft Defender für Endpunkte habe, kann ich dann einen Rabatt für Defender für Server erhalten?
Wenn Sie bereits über eine Lizenz für Microsoft Defender für Endpunkte verfügen, müssen Sie für diesen Teil Ihrer Lizenz für Defender für Server nicht bezahlen.

Um Ihren Rabatt anzufordern, wenden Sie sich an das Support-Team von Defender für Cloud und geben Sie die entsprechende Arbeitsbereichs-ID, die Region und die Anzahl der Microsoft Defender für Endpunkte-Lizenzen an, die für die Rechner in dem jeweiligen Arbeitsbereich gelten.

Der Rabatt gilt ab dem Genehmigungsdatum und wird nicht rückwirkend wirksam.

### <a name="my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>In meinem Abonnement ist Microsoft Defender für Server aktiviert. Muss ich auch für nicht ausgeführte Server bezahlen? 
Nein. Wenn Sie [Microsoft Defender für Server](defender-for-servers-introduction.md) im Rahmen eines Abonnements aktivieren, werden Ihnen keine Kosten für Rechner in Rechnung gestellt, die sich in diesem Zustand befinden, solange sie keinen Strom verbrauchen. Computer werden gemäß ihrem Energiezustand abgerechnet, wie in der folgenden Tabelle zu sehen:

| State        | BESCHREIBUNG                                                                                                                                      | Abgerechnete Instanznutzung |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Wird gestartet     | Die VM wird gestartet.                                                                                                                               | Nicht in Rechnung gestellt            |
| Wird ausgeführt      | Dies ist der normale Ausführungszustand einer VM.                                                                                                                    | In Rechnung gestellt                |
| Wird beendet     | Dies ist ein Übergangszustand. Nach Abschluss des Vorgangs wird für die VM Beendet angezeigt.                                                                           | In Rechnung gestellt                |
| Beendet      | Die VM wurde über das Gastbetriebssystem oder mithilfe der PowerOff-APIs heruntergefahren. Der VM ist weiterhin Hardware zugeordnet, und sie verbleibt auf dem Host. | In Rechnung gestellt                |
| Zuordnung wird aufgehoben | Dies ist ein Übergangszustand. Nach Abschluss des Vorgangs wird für die VM Zuordnung aufgehoben angezeigt.                                                                             | Nicht in Rechnung gestellt            |
| Zuordnung aufgehoben  | Die VM wurde erfolgreich beendet und vom Host entfernt.                                                                                  | Nicht in Rechnung gestellt            |

:::image type="content" source="media/enhanced-security-features-overview/deallocated-virtual-machines.png" alt-text="Computer mit aufgehobener Zuordnung in Azure Virtual Machines":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Werden mir für Computer, auf denen der Log Analytics-Agent nicht installiert ist, Kosten berechnet?
Ja. Wenn Sie [Microsoft Defender für Server](defender-for-servers-introduction.md) für ein Abonnement aktivieren, erhalten die Rechner in diesem Abonnement eine Reihe von Schutzfunktionen, auch wenn Sie den Log Analytics-Agent nicht installiert haben. Dies gilt für virtuelle Azure-Computer, Azure-VM-Skalierungsgruppeninstanzen und Server mit Azure Arc-Unterstützung.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, werden mir dann zweimal Kosten berechnet? 
Ja. Wenn Sie Ihren Log Analytics-Agent so konfiguriert haben, dass Daten an zwei oder mehr unterschiedliche Log Analytics-Arbeitsbereiche gesendet werden (Multi-Homing), werden Ihnen für jeden Arbeitsbereich Kosten in Rechnung gestellt, für den Lösungen vom Typ „Sicherheit“ oder „Antischadsoftware“ installiert sind. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, sind die kostenlosen 500 MB für die Datenerfassung dann für alle Arbeitsbereiche verfügbar?
Ja. Wenn Sie Ihren Log Analytics-Agent so konfiguriert haben, dass Daten an zwei oder mehr unterschiedliche Log Analytics-Arbeitsbereiche (Multi-Homing) gesendet werden, können Sie 500 MB für die Datenerfassung kostenlos nutzen. Dieser Wert wird pro Knoten, pro Meldungsarbeitsbereich und pro Tag berechnet und ist für jeden Arbeitsbereich verfügbar, für den Lösungen vom Typ „Sicherheit“ oder „Antischadsoftware“ installiert sind. Ihnen werden Kosten für alle Daten berechnet, die über die maximal 500 MB hinaus erfasst werden.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Wird die kostenlose Datenerfassung von 500 MB für einen gesamten Arbeitsbereich oder ausschließlich pro Computer berechnet?
Für jeden Windows-Computer, der mit dem Arbeitsbereich verbunden ist, stehen Ihnen pro Tag 500 MB für die Datenerfassung kostenlos zur Verfügung. Speziell für Sicherheitsdatentypen, die von Defender für Cloud direkt erfasst werden. 

Bei diesen Daten handelt es sich um eine tägliche Durchschnittsrate für alle Knoten. Selbst wenn also einige Computer 100 MB und andere 800 MB senden, werden Ihnen keine zusätzlichen Kosten in Rechnung gestellt, wenn die Summe die kostenlose Obergrenze von **[Anzahl der Computer] x 500 MB** nicht überschreitet.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Welche Datentypen sind im täglichen Datenkontingent von 500 MB enthalten?
Die Abrechnung von Defender für Cloud ist eng mit der Abrechnung von Log Analytics verbunden. [Microsoft Defender für Server](defender-for-servers-introduction.md) bietet eine Zuweisung von 500 MB/Knoten/Tag für Windows-Computer für die folgende Untergruppe von [Sicherheitsdatentypen](/azure/azure-monitor/reference/tables/tables-category#security):
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- Datentypen „Update“ und „UpdateSummary“, wenn im Arbeitsbereich die Lösung für die Updateverwaltung nicht ausgeführt wird oder die Zielgruppenadressierung aktiviert ist

Wenn sich der Arbeitsbereich in der alten Preisstufe "Pro Knoten" befindet, werden die Zuweisungen für Defender für Cloud und Log Analytics kombiniert und gemeinsam auf alle abrechenbaren erfassten Daten angewendet.

## <a name="next-steps"></a>Nächste Schritte
Dieser Artikel erläutert die Preisoptionen von Defender für Cloud. Verwandte Informationen finden Sie hier:

- [Optimieren der Kosten für Azure-Workloads](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Preisdetails in der von Ihnen gewählten Währung für Ihre Region](https://azure.microsoft.com/pricing/details/security-center/)
- Möglicherweise möchten Sie Ihre Kosten verwalten und den Umfang der für eine Lösung gesammelten Daten begrenzen, indem Sie sie auf einen bestimmten Satz von Agents beschränken. Mit der [Zielgruppenadressierung für Lösungen](../azure-monitor/insights/solution-targeting.md) können Sie einen Bereich auf die Lösung anwenden und eine Teilmenge von Computern im Arbeitsbereich als Ziel angeben. Wenn Sie Solution Targeting verwenden, listet Defender für Cloud den Arbeitsbereich als nicht lösungsorientiert auf.
