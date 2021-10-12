---
title: Azure Security Center Free und Azure Defender-Aktivierung
description: Hier erfahren Sie mehr über die Vorteile der Aktivierung von Azure Defender für den Cloudworkloadschutz in Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 09/19/2021
ms.openlocfilehash: 3e76d298d30d2e191edf218e85a8fc64758f8359
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560786"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center Free und Azure Defender-Aktivierung
Azure Defender ist in den ersten 30 Tagen kostenlos. Nach Ablauf der 30 Tage wird Ihnen die Nutzung automatisch in Rechnung gestellt, sofern Sie sich dafür entscheiden, den Dienst weiterhin zu nutzen.

Sie können auf der Seite **Preise und Einstellungen** ein Upgrade durchführen, wie unter [Schnellstart: Aktivieren von Azure Defender](enable-azure-defender.md) beschrieben. Preisdetails zu Ihrer bevorzugten Währung und für Ihre Region finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Welche Vorteile bietet die Aktivierung von Azure Defender?

Security Center wird in zwei Modi angeboten:

- **Azure Defender AUS** (Kostenlos): Security Center ohne Azure Defender ist kostenlos für alle Ihre Azure-Abonnements aktiviert, sobald Sie das Azure Security Center-Dashboard zum ersten Mal im Azure-Portal aufrufen, oder wenn Sie es programmgesteuert über die API aktiviert haben. Die Verwendung dieses kostenlosen Tarifs umfasst eine Sicherheitsrichtlinie, eine kontinuierliche Bewertung der Sicherheit und direkt umsetzbare Sicherheitsempfehlungen für den Schutz Ihrer Azure-Ressourcen.

- **Azure Defender EIN**: Die Aktivierung von Azure Defender erweitert die Funktionen des kostenlosen Modus auf Workloads, die in privaten und anderen öffentlichen Clouds ausgeführt werden. Sie bietet eine einheitliche Sicherheitsverwaltung und Schutz vor Bedrohungen für Ihre Hybrid Cloud-Workloads. Einige der wichtigsten Features von Azure Defender:

    - **Microsoft Defender für Endpunkt**: Azure Defender für Server enthält [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender), um die umfassende Endpunkterkennung und -reaktion (Endpoint Detection and Response, EDR) zu ermöglichen. Informieren Sie sich über die Vorteile der Nutzung von Microsoft Defender für Endpunkt mit Azure Defender, indem Sie den [Artikel zur integrierten EDR-Lösung von Security Center](security-center-wdatp.md) lesen.
    - **Überprüfung auf Sicherheitsrisiken für virtuelle Computer und Containerregistrierungen:** Stellen Sie problemlos einen Scanner auf allen virtuellen Computern bereit, der die branchenweit fortgeschrittenste Lösung zum Umgang mit Sicherheitsrisiken bietet. Sie können die Ergebnisse direkt in Security Center anzeigen, untersuchen und behandeln. 
    - **Hybridsicherheit:** Verschaffen Sie sich einen einheitlichen Überblick über die Sicherheit sämtlicher lokaler und cloudbasierter Workloads. Wenden Sie Sicherheitsrichtlinien an, und bewerten Sie kontinuierlich die Sicherheit Ihrer Hybridcloud-Workloads, um die Einhaltung von Sicherheitsstandards zu gewährleisten. Sammeln, durchsuchen und analysieren Sie Sicherheitsdaten aus zahlreichen Quellen (einschließlich Firewalls und Partnerlösungen).
    - **Bedrohungsschutzwarnungen:** Mit erweiterten Verhaltensanalysen und Microsoft Intelligent Security Graph sind Sie neuen Arten von Cyberangriffen immer einen Schritt voraus. Mit integrierten Verhaltensanalysen und Machine Learning können Angriffe und Zero-Day-Exploits erkannt werden. Überwachen Sie Netzwerke, Computer und Clouddienste auf neue Angriffe und auf Aktivitäten nach einer Sicherheitsverletzung. Optimieren Sie die Untersuchung mit interaktiven Tools und kontextbezogenen Informationen zu Bedrohungen.
    - **Nachverfolgen der Konformität mit verschiedenen Standards:** Von Security Center wird Ihre Hybrid Cloud-Umgebung kontinuierlich bewertet, um die Risikofaktoren gemäß den Kontrollen und bewährten Methoden im [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction) zu analysieren. Wenn Sie Azure Defender aktivieren, können Sie verschiedene andere Branchenstandards, gesetzliche Standards und Benchmarks gemäß den Anforderungen Ihrer Organisation anwenden. Über das [Dashboard zur Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md) können Sie Standards hinzufügen und die Konformität nachverfolgen.
    - **Zugriffs- und Anwendungskontrollen (Access and Application Controls, AAC):** Blockieren Sie Schadsoftware und andere unerwünschte Anwendungen, indem Sie durch Machine Learning unterstützte Empfehlungen anwenden, die auf Ihre spezifischen Workloads abgestimmt sind, um Zulassungs- und Verweigerungslisten zu erstellen. Verringern Sie die Angriffsfläche im Netzwerk mit kontrolliertem Just-in-Time-Zugriff auf Verwaltungsports auf Azure Virtual Machines. Mit den Zugriffs- und Anwendungskontrollen wird das Risiko von Brute-Force- und anderen Netzwerkangriffen drastisch reduziert.
    - **Containersicherheitsfeatures**: Profitieren Sie in Ihren Containerumgebungen von der Handhabung von Sicherheitsrisiken und des Schutzes vor Bedrohungen in Echtzeit. Beim Aktivieren von **Azure Defender für Containerregistrierungen** kann es bis zu zwölf Stunden dauern, bis alle Features aktiviert wurden. Die Gebühren basieren auf der Anzahl der eindeutigen Containerimages, die in Ihre verbundene Registrierung gepusht werden. Nachdem ein Image ein Mal gescannt wurde, wird es nicht mehr in Rechnung gestellt, es sei denn, es wird geändert und noch einmal gepusht.
    - **Breit gefächerter Bedrohungsschutz für Ressourcen, die mit der Azure-Umgebung verbunden sind**: Azure Defender beinhaltet Azure-nativen, breit gefächerten Bedrohungsschutz für die Azure-Dienste, die von allen Ihren Ressourcen genutzt werden: Azure Resource Manager, Azure DNS, Azure-Netzwerkebene und Azure Key Vault. Azure Defender verfügt über einen einzigartigen Einblick in die Azure-Verwaltungsebene und in die Azure DNS-Ebene, was den Schutz von Cloudressourcen ermöglicht, die mit diesen Ebenen verbunden sind.


## <a name="faq---pricing-and-billing"></a>Häufig gestellte Fragen: Preise und Abrechnung 

- [Wie kann ich nachverfolgen, wer in meiner Organisation Änderungen an Azure Defender in Security Center aktiviert hat?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Welche Pläne werden für Security Center angeboten?](#what-are-the-plans-offered-by-security-center)
- [Wie kann ich Azure Defender für mein Abonnement aktivieren?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Kann ich Azure Defender für Server nur für einen Teil der Server meines Abonnements aktivieren?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Kann ich für Azure Defender einen Rabatt erhalten, wenn ich bereits über eine Lizenz für Microsoft Defender für Endpunkt verfüge?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Für mein Abonnement ist Azure Defender für Server aktiviert. Muss ich für Server bezahlen, die nicht ausgeführt werden?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Werden mir für Computer, auf denen der Log Analytics-Agent nicht installiert ist, Kosten berechnet?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, werden mir dann zweimal Kosten berechnet?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, sind die kostenlosen 500 MB für die Datenerfassung dann für alle Arbeitsbereiche verfügbar?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Wird die kostenlose Datenerfassung von 500 MB für einen gesamten Arbeitsbereich oder ausschließlich pro Computer berechnet?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Welche Datentypen sind im täglichen Datenkontingent von 500 MB enthalten?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Wie kann ich nachverfolgen, wer in meiner Organisation Änderungen an Azure Defender in Security Center aktiviert hat?
In einem Azure-Abonnement gibt es möglicherweise mehrere Administratoren mit Berechtigungen zum Ändern der Tarifeinstellungen. Verwenden Sie das Azure-Aktivitätsprotokoll, um herauszufinden, welcher Benutzer eine Änderung vorgenommen hat.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Azure-Aktivitätsprotokoll mit einem Preisänderungsereignis":::

Wenn die Informationen des Benutzers nicht in der Spalte **Ereignis initiiert von** aufgeführt sind, sollten Sie sich in den JSON-Daten des Ereignisses die relevanten Details ansehen.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure-Aktivitätsprotokoll: JSON-Explorer":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Welche Pläne werden für Security Center angeboten? 
Für Security Center gibt es zwei Angebote: 

- Free-Tarif für Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Wie kann ich Azure Defender für mein Abonnement aktivieren? 
Sie können die folgenden Methoden verwenden, um Azure Defender für Ihr Abonnement zu aktivieren: 

| Methode                                          | Anweisungen                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center-Seiten im Azure-Portal | [Aktivieren von Azure Defender](enable-azure-defender.md)                                                                                                  |
| REST-API                                        | [Pricings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Bundle-Preise](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Kann ich Azure Defender für Server nur für einen Teil der Server meines Abonnements aktivieren?
Nein. Wenn Sie [Azure Defender für Server](defender-for-servers-introduction.md) in einem Abonnement aktivieren, sind alle Server des Abonnements mit Azure Defender geschützt. 

Eine Alternative besteht darin, Azure Defender für Server auf der Ebene des Log Analytics-Arbeitsbereichs zu aktivieren. Wenn Sie dies tun, werden nur Server, die Daten an diesen Arbeitsbereich melden, geschützt und in Rechnung gestellt. Mehrere Funktionen sind aber nicht verfügbar. Hierzu zählen Just-In-Time-VM-Zugriff, Netzwerkerkennungen, Einhaltung gesetzlicher Bestimmungen, adaptive Netzwerkhärtung, adaptive Anwendungssteuerung und mehr. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Kann ich für Azure Defender einen Rabatt erhalten, wenn ich bereits über eine Lizenz für Microsoft Defender für Endpunkt verfüge?
Wenn Sie bereits eine Lizenz für Microsoft Defender für Endpunkt besitzen, müssen Sie für diesen Teil Ihrer Azure Defender-Lizenz nicht bezahlen.

Fordern Sie Ihren Rabatt beim Supportteam von Security Center an, und geben Sie die entsprechende Arbeitsbereichs-ID, die Region und die Anzahl der Microsoft Defender for Endpoint-Lizenzen an, die für Computer im angegebenen Arbeitsbereich angewendet wurden.

Der Rabatt gilt ab dem Genehmigungsdatum und wird nicht rückwirkend wirksam.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Für mein Abonnement ist Azure Defender für Server aktiviert. Muss ich für Server bezahlen, die nicht ausgeführt werden? 
Nein. Wenn Sie für ein Abonnement [Azure Defender für Server](defender-for-servers-introduction.md) aktivieren, fallen für Computer im Zustand „Zuordnung aufgehoben“ keine Gebühren an, solange sie sich in diesem Zustand befinden. Computer werden gemäß ihrem Energiezustand abgerechnet, wie in der folgenden Tabelle zu sehen:

| State        | BESCHREIBUNG                                                                                                                                      | Abgerechnete Instanznutzung |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Wird gestartet     | Die VM wird gestartet.                                                                                                                               | Nicht in Rechnung gestellt            |
| Wird ausgeführt      | Dies ist der normale Ausführungszustand einer VM.                                                                                                                    | In Rechnung gestellt                |
| Wird beendet     | Dies ist ein Übergangszustand. Nach Abschluss des Vorgangs wird für die VM Beendet angezeigt.                                                                           | In Rechnung gestellt                |
| Beendet      | Die VM wurde über das Gastbetriebssystem oder mithilfe der PowerOff-APIs heruntergefahren. Der VM ist weiterhin Hardware zugeordnet, und sie verbleibt auf dem Host. | In Rechnung gestellt                |
| Zuordnung wird aufgehoben | Dies ist ein Übergangszustand. Nach Abschluss des Vorgangs wird für die VM Zuordnung aufgehoben angezeigt.                                                                             | Nicht in Rechnung gestellt            |
| Zuordnung aufgehoben  | Die VM wurde erfolgreich beendet und vom Host entfernt.                                                                                  | Nicht in Rechnung gestellt            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Computer mit aufgehobener Zuordnung in Azure Virtual Machines":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Werden mir für Computer, auf denen der Log Analytics-Agent nicht installiert ist, Kosten berechnet?
Ja. Wenn Sie für ein Abonnement [Azure Defender für Server](defender-for-servers-introduction.md) aktivieren, werden für die Computer dieses Abonnements auch dann einige Schutzmaßnahmen getroffen, wenn Sie den Log Analytics-Agent nicht installiert haben. Dies gilt für virtuelle Azure-Computer, Azure-VM-Skalierungsgruppeninstanzen und Server mit Azure Arc-Unterstützung.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, werden mir dann zweimal Kosten berechnet? 
Ja. Wenn Sie Ihren Log Analytics-Agent so konfiguriert haben, dass Daten an zwei oder mehr unterschiedliche Log Analytics-Arbeitsbereiche gesendet werden (Multi-Homing), werden Ihnen für jeden Arbeitsbereich Kosten in Rechnung gestellt, für den Lösungen vom Typ „Sicherheit“ oder „Antischadsoftware“ installiert sind. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Wenn ein Log Analytics-Agent Daten an mehrere Arbeitsbereiche meldet, sind die kostenlosen 500 MB für die Datenerfassung dann für alle Arbeitsbereiche verfügbar?
Ja. Wenn Sie Ihren Log Analytics-Agent so konfiguriert haben, dass Daten an zwei oder mehr unterschiedliche Log Analytics-Arbeitsbereiche (Multi-Homing) gesendet werden, können Sie 500 MB für die Datenerfassung kostenlos nutzen. Dieser Wert wird pro Knoten, pro Meldungsarbeitsbereich und pro Tag berechnet und ist für jeden Arbeitsbereich verfügbar, für den Lösungen vom Typ „Sicherheit“ oder „Antischadsoftware“ installiert sind. Ihnen werden Kosten für alle Daten berechnet, die über die maximal 500 MB hinaus erfasst werden.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Wird die kostenlose Datenerfassung von 500 MB für einen gesamten Arbeitsbereich oder ausschließlich pro Computer berechnet?
Für jeden Computer, der mit dem Arbeitsbereich verbunden ist, stehen Ihnen pro Tag 500 MB für die Datenerfassung kostenlos zur Verfügung. Dies gilt speziell für Sicherheitsdatentypen, die direkt von Azure Security Center erfasst werden.

Bei diesen Daten handelt es sich um eine tägliche Durchschnittsrate für alle Knoten. Selbst wenn also einige Computer 100 MB und andere 800 MB senden, werden Ihnen keine zusätzlichen Kosten in Rechnung gestellt, wenn die Summe die kostenlose Obergrenze von **[Anzahl der Computer] x 500 MB** nicht überschreitet.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Welche Datentypen sind im täglichen Datenkontingent von 500 MB enthalten?

Die Security Center-Abrechnung ist eng mit der Log Analytics-Abrechnung verbunden. Security Center bietet ein Kontingent von 500 MB pro Knoten und Tag für die folgende Teilmenge von [Sicherheitsdatentypen](/azure/azure-monitor/reference/tables/tables-category#security):
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

Wenn der Arbeitsbereich den Legacytarif „Pro Knoten“ aufweist, werden die Security Center- und Log Analytics-Zuordnungen kombiniert und gemeinsam auf alle abrechenbaren erfassten Daten angewendet.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden die Preisoptionen von Security Center beschrieben. Verwandte Informationen finden Sie hier:

- [Optimieren der Kosten für Azure-Workloads](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Preisdetails in der von Ihnen gewählten Währung für Ihre Region](https://azure.microsoft.com/pricing/details/security-center/)
- Möglicherweise möchten Sie Ihre Kosten verwalten und den Umfang der für eine Lösung gesammelten Daten begrenzen, indem Sie sie auf einen bestimmten Satz von Agents beschränken. Mit der [Zielgruppenadressierung für Lösungen](../azure-monitor/insights/solution-targeting.md) können Sie einen Bereich auf die Lösung anwenden und eine Teilmenge von Computern im Arbeitsbereich als Ziel angeben. Bei Verwendung der Zielgruppenadressierung für Lösungen führt Security Center den Arbeitsbereich als Arbeitsbereich ohne Lösung auf.
