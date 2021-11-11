---
title: Versionshinweise zu Microsoft Defender für Cloud
description: Eine Beschreibung der Neuerungen und Änderungen in Microsoft Defender für Cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 11/04/2021
ms.author: memildin
ms.openlocfilehash: 0e93c254a28aa62ce84f60daa02f91b247893a5b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577154"
---
# <a name="whats-new-in-microsoft-defender-for-cloud"></a>Neuerungen in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender für Cloud befindet sich in der aktiven Entwicklung und wird ständig verbessert. Damit Sie bezüglich der aktuellen Entwicklungen immer auf dem neuesten Stand sind, enthält diese Seite Informationen zu neuen Features, Fehlerbehebungen und veralteten Funktionen.

Es ist ratsam, diese Seite regelmäßig zu besuchen, da sie immer wieder aktualisiert wird. 

Informationen zu *geplanten* Änderungen, die in Kürze an Defender für Cloud vorgenommen werden, finden Sie unter [Wichtige bevorstehende Änderungen an Microsoft Defender für Cloud](upcoming-changes.md). 

> [!TIP]
> Mehr als sechs Monate zurückliegende Einträge finden Sie im [Archiv zu den Neuerungen in Microsoft Defender für Cloud](release-notes-archive.md).


## <a name="november-2021"></a>November 2021

Unser Ignite-Release umfasst Folgendes:

- [Azure Security Center und Azure Defender werden zu Microsoft Defender für Cloud](#azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud)
- [Native CSPM für AWS und Bedrohungsschutz für Amazon EKS und AWS EC2](#native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2)
- [Priorisieren von Sicherheitsaktionen nach Datensensitivität (unterstützt durch Azure Purview) (in der Vorschau)](#prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview)
- [Erweiterte Sicherheitskontrollbewertungen mit dem Azure-Sicherheitsvergleichstest v3](#expanded-security-control-assessments-with-azure-security-benchmark-v3)
- [Optionale bidirektionale Warnungssynchronisierung des Microsoft Sentinel-Connectors – jetzt allgemein verfügbar](#microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga)
- [Neue Empfehlung zum Pushen von Azure Kubernetes Service-Protokollen (AKS) an Sentinel](#new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel)

Weitere Änderungen im November sind:

- [Bedrohungs- und Sicherheitsrisikomanagement von Microsoft als Lösung zur Sicherheitsrisikobewertung hinzugefügt (in der Vorschau) – jetzt allgemein verfügbar](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga)
- [Microsoft Defender für Endpunkt für Linux wird jetzt von Microsoft Defender für Server unterstützt – jetzt allgemein verfügbar](#microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga)
- [Momentaufnahmeexport für Empfehlungen und Sicherheitsergebnisse (in der Vorschau)](#snapshot-export-for-recommendations-and-security-findings-in-preview)
- [Automatische Bereitstellung von Lösungen zur Sicherheitsrisikobewertung – jetzt allgemein verfügbar](#auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga)
- [Softwareinventurfilter im Bestandsverzeichnis – jetzt allgemein verfügbar](#software-inventory-filters-in-asset-inventory-released-for-general-availability-ga)

### <a name="azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud"></a>Azure Security Center und Azure Defender werden zu Microsoft Defender für Cloud

Gemäß dem Bericht [2021 State of the Cloud](https://info.flexera.com/CM-REPORT-State-of-the-Cloud#download) verfügen 92 % der Organisationen jetzt über eine Multi-Cloud-Strategie. Ziel von Microsoft ist es, die Sicherheit in diesen Umgebungen zu zentralisieren und Sicherheitsteams dabei zu unterstützen, effektiver zu arbeiten.

**Microsoft Defender für Cloud** (früher als Azure Security Center und Azure Defender bezeichnet) ist eine Lösung für Cloud Security Posture Management (CSPM) und Cloudworkloadschutz (Cloud Workload Protection, CWP), die Schwachstellen in Ihrer Cloudkonfiguration entdeckt, den allgemeinen Sicherheitsstatus Ihrer Umgebung stärkt und Workloads in Umgebungen mit mehreren Clouds und Hybridumgebungen schützt.

Auf der Ignite 2019 haben wir unsere Vision vorgestellt, den umfassendsten Ansatz für die Absicherung Ihrer digitalen Ressourcen zu entwickeln und XDR-Technologien unter der Marke Microsoft Defender zu integrieren. Die Zusammenführung von Azure Security Center und Azure Defender unter dem neuen Namen **Microsoft Defender für Cloud** spiegelt die integrierten Fähigkeiten unseres Sicherheitsangebots sowie unsere Fähigkeiten zur Unterstützung jeder Cloudplattform wider.


### <a name="native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2"></a>Native CSPM für AWS und Bedrohungsschutz für Amazon EKS und AWS EC2

Eine neue Seite mit **Umgebungseinstellungen** bietet mehr Transparenz und Kontrolle über Ihre Verwaltungsgruppen, Abonnements und AWS-Konten. Die Seite ist für das Onboarding von AWS-Konten im großen Stil konzipiert: Durch das Verbinden Ihres AWS-**Verwaltungskontos** werden automatisch vorhandene und zukünftige Konten integriert. 

:::image type="content" source="media/release-notes/add-aws-account.png" alt-text="Verwenden Sie die neue Seite mit den Umgebungseinstellungen, um eine Verbindung mit Ihren AWS-Konten herzustellen.":::

Wenn Sie Ihre AWS-Konten hinzugefügt haben, schützt Defender für Cloud Ihre AWS-Ressourcen mit einem oder allen der folgenden Pläne:

- Die **CSPM-Features von Defender für Cloud** werden auf Ihre AWS-Ressourcen ausgeweitet. Dieser Plan ohne Agent bewertet Ihre AWS-Ressourcen gemäß AWS-spezifischen Sicherheitsempfehlungen und ist in Ihrer Sicherheitsbewertung enthalten. Die Ressourcen werden auch auf Einhaltung integrierter AWS-spezifischer Standards (AWS CIS, AWS PCI-DSS und AWS Foundational Security Best Practices) bewertet. Die Seite [Bestandsverzeichnis](asset-inventory.md) von Defender für Cloud ist eine Multi-Cloud-Funktion, die Ihnen hilft, Ihre AWS-Ressourcen zusammen mit Ihren Azure-Ressourcen zu verwalten.
- Mit **Microsoft Defender für Kubernetes** werden die Containerbedrohungserkennung und erweiterten Schutzmaßnahmen auf Ihre **Amazon EKS Linux-Cluster** ausgeweitet.
- **Microsoft Defender für Server** stattet Ihre Windows- und Linux-EC2-Instanzen mit Bedrohungserkennung und erweiterten Schutzmaßnahmen aus. Dieser Plan umfasst die integrierte Lizenz für Microsoft Defender für Endpunkt, Sicherheitsbaselines und Bewertungen auf Betriebssystemebene, Überprüfungen zur Sicherheitsrisikobewertung, adaptive Anwendungssteuerung (AAC), Überwachung der Dateiintegrität (FIM) und mehr.

Weitere Informationen zum [Verbinden Ihrer AWS-Konten mit Microsoft Defender für Cloud](quickstart-onboard-aws.md).


### <a name="prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview"></a>Priorisieren von Sicherheitsaktionen nach Datensensitivität (unterstützt durch Azure Purview) (in der Vorschau)
Datenressourcen bleiben ein beliebtes Ziel für Bedrohungsakteure. Daher ist es wichtig, dass Sicherheitsteams vertrauliche Datenressourcen in ihren Cloudumgebungen identifizieren, priorisieren und schützen.

Um diese Herausforderung zu bewältigen, integriert Microsoft Defender für Cloud jetzt Vertraulichkeitsinformationen aus [Azure Purview](../purview/overview.md). Azure Purview ist ein einheitlicher Datengovernance-Dienst, der umfassende Einblicke in die Vertraulichkeit Ihrer Daten innerhalb von Multi-Cloud- und lokalen Workloads bietet.

Die Integration in Azure Purview weitet Ihre Sicherheitstransparenz in Defender für Cloud von der Infrastrukturebene bis hinunter zu den Daten aus und ermöglicht eine völlig neue Möglichkeit, Ressourcen und Sicherheitsaktivitäten für Ihre Sicherheitsteams zu priorisieren.

Weitere Informationen finden Sie unter [Priorisieren von Sicherheitsaktionen nach Datenvertraulichkeit](information-protection.md).


### <a name="expanded-security-control-assessments-with-azure-security-benchmark-v3"></a>Erweiterte Sicherheitskontrollbewertungen mit dem Azure-Sicherheitsvergleichstest v3
Die Sicherheitsempfehlungen von Microsoft Defender für Cloud werden vom Azure-Sicherheitsvergleichstest aktiviert und unterstützt. 

Beim [Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md) handelt es sich um einen von Microsoft erstellten Satz mit Azure-spezifischen Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.

Ab der Ignite 2021 ist **v3** des Azure-Sicherheitsvergleichstests im [Dashboard für die Einhaltung gesetzlicher Bestimmungen von Defender für Cloud](update-regulatory-compliance-packages.md) verfügbar und als neue Standardinitiative für alle Azure-Abonnements aktiviert, die mit Microsoft Defender für Cloud geschützt werden. 

Zu den Verbesserungen für v3 gehören: 

- Zusätzliche Zuordnungen zu den Branchenframeworks [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf) und [CIS Controls v8](https://www.cisecurity.org/controls/v8/).
- Detailliertere und umsetzbare Anleitungen für Steuerungen mit der Einführung:
    - **Sicherheitsprinzipien**: Einblicke in die allgemeinen Sicherheitsziele, die die Grundlage für unsere Empfehlungen bilden.
    - **Azure-Leitfaden**: Technische „Anleitung“ zum Erreichen dieser Ziele.

Neue Steuerungen umfassen DevOps-Sicherheit für Probleme wie Bedrohungsmodellierung und Sicherheit der Softwarelieferkette sowie Schlüssel- und Zertifikatverwaltung für bewährte Methoden in Azure.

Weitere Informationen finden Sie in der [Einführung zum Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction).


### <a name="microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga"></a>Optionale bidirektionale Warnungssynchronisierung des Microsoft Sentinel-Connectors – jetzt allgemein verfügbar

Im Juli wurde eine Previewfunktion [angekündigt](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview), die **bidirektionale Warnungssynchronisierung** für den integrierten Connector in [Microsoft Sentinel](../sentinel/index.yml) (die cloudnative SIEM- und SOAR-Lösung von Microsoft). Dieses Feature ist jetzt allgemein verfügbar.

Wenn Sie Microsoft Defender für Cloud mit Microsoft Sentinel verbinden, wird der Status von Sicherheitswarnungen zwischen den beiden Diensten synchronisiert. Wenn also beispielsweise eine Warnung in Defender for Cloud geschlossen wird, wird diese Warnung auch in Microsoft Sentinel als geschlossen angezeigt. Die Änderung des Status einer Warnung in Defender für Cloud wirkt sich nicht auf den Status aller Microsoft Sentinel **Vorfälle** aus, die die synchronisierte Microsoft Sentinel-Warnung enthalten, sondern nur auf den der synchronisierten Warnung selbst.

Durch die Aktivierung der **bidirektionalen Warnungssynchronisierung** wird der Status der ursprünglichen Defender für Cloud-Warnungen automatisch mit Microsoft Sentinel-Vorfällen synchronisiert, die Kopien dieser Defender für Cloud-Warnungen enthalten. Wenn zum Beispiel ein Microsoft Sentinel-Vorfall, der eine Defender für Cloud-Warnung enthält, geschlossen wird, schließt Defender für Cloud automatisch die entsprechende ursprüngliche Warnung.

Weitere Informationen finden Sie unter [Verbinden von Azure Defender-Benachrichtigungen aus Azure Security Center](../sentinel/connect-azure-security-center.md) und [Streamen von Warnungen in Azure Sentinel](export-to-siem.md#stream-alerts-to-microsoft-sentinel).


### <a name="new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel"></a>Neue Empfehlung zum Pushen von Azure Kubernetes Service-Protokollen (AKS) an Sentinel

In einer weiteren Verbesserung des kombinierten Werts von Defender für Cloud und Microsoft Sentinel werden jetzt Azure Kubernetes Service-Instanzen hervorgehoben, die keine Protokolldaten an Microsoft Sentinel senden.

SecOps-Teams können den entsprechenden Microsoft Sentinel-Arbeitsbereich direkt auf der Seite mit den Empfehlungsdetails auswählen und sofort das Streaming von Rohprotokollen aktivieren. Diese nahtlose Verbindung zwischen den beiden Produkten erleichtert es Sicherheitsteams, eine vollständige Protokollierungsabdeckung für ihre Workloads sicherzustellen, damit sie über die gesamte Umgebung auf dem Laufenden bleiben.

Die neue Empfehlung „Diagnoseprotokolle in Kubernetes-Diensten sollten aktiviert werden“ enthält die Option „Korrigieren“ für eine schnellere Korrektur.

Wir haben auch die Empfehlung „Überwachung auf SQL Server sollte aktiviert sein“ um die gleichen Sentinel-Streamingfunktionen erweitert. 


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga"></a>Bedrohungs- und Sicherheitsrisikomanagement von Microsoft als Lösung zur Sicherheitsrisikobewertung hinzugefügt (in der Vorschau) – jetzt allgemein verfügbar

Im Oktober wurde eine Erweiterung der Integration zwischen [Microsoft Defender für Server](defender-for-servers-introduction.md) und Microsoft Defender für Endpunkt [angekündigt](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview), um einen neuen Anbieter von Sicherheitsrisikobewertungen für Ihre Computer zu unterstützen: [Bedrohungs- und Sicherheitsrisikomanagement von Microsoft](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). Dieses Feature ist jetzt allgemein verfügbar.

Verwenden Sie **Bedrohungs- und Schwachstellenmanagement**, um Schwachstellen und Fehlkonfigurationen nahezu in Echtzeit zu erkennen, wenn die [Integration mit Microsoft Defender für Endpoint](integration-defender-for-endpoint.md) aktiviert ist, ohne dass zusätzliche Agenten oder regelmäßige Scans erforderlich sind. Das Bedrohungs- und Schwachstellenmanagement priorisiert Schwachstellen auf der Grundlage der Bedrohungslandschaft und der Entdeckungen in Ihrem Unternehmen.

Verwenden Sie die Sicherheitsempfehlung "[Eine Lösung zur Bewertung von Schwachstellen sollte auf Ihren virtuellen Maschinen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)", um die von der Bedrohungs- und Schwachstellenverwaltung erkannten Schwachstellen für Ihre [unterstützten Maschinen](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true) anzuzeigen. 

Um die Schwachstellen auf bestehenden und neuen Maschinen automatisch zu erkennen, ohne die Empfehlung manuell korrigieren zu müssen, siehe [Schwachstellenbewertungslösungen können jetzt automatisch aktiviert werden (in der Vorschau)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

Weitere Informationen finden Sie unter [Untersuchen Sie Schwachstellen mit dem Bedrohungs- und Schwachstellenmanagement von Microsoft Defender for Endpoint](deploy-vulnerability-assessment-tvm.md).

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga"></a>Microsoft Defender für Endpunkt für Linux wird jetzt von Microsoft Defender für Server unterstützt – jetzt allgemein verfügbar

Im August wurde die Vorschauunterstützung für die Bereitstellung des [Defender für Endpunkt für Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux)-Sensors auf unterstützten Linux-Computern [angekündigt](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview). Dieses Feature ist jetzt allgemein verfügbar.

[Microsoft Defender für Server](defender-for-servers-introduction.md) beinhaltet eine integrierte Lizenz für [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Dadurch stehen umfassende EDR-Funktionen (Endpoint Detection and Response; Endpunkterkennung und -reaktion) zur Verfügung.

Wenn Defender für Endpunkt eine Bedrohung erkennt, wird eine Warnung ausgelöst. Die Warnung wird in Defender für Cloud angezeigt. Über Defender für Cloud können Sie auch zur Defender für Endpunkt-Konsole wechseln und eine detaillierte Untersuchung durchführen, um das Ausmaß des Angriffs zu ermitteln.

Weitere Informationen finden Sie unter [Schützen Sie Ihre Endpunkte mit der in Security Center integrierten EDR-Lösung: Microsoft Defender für den Endpunkt](integration-defender-for-endpoint.md).


### <a name="snapshot-export-for-recommendations-and-security-findings-in-preview"></a>Momentaufnahmeexport für Empfehlungen und Sicherheitsergebnisse (in der Vorschau)

Defender für Cloud generiert detaillierte Sicherheitswarnungen und -empfehlungen. Sie können diese im Portal oder über programmgesteuerte Tools anzeigen. Möglicherweise müssen Sie diese Informationen auch ganz oder teilweise für die Nachverfolgung mit anderen Überwachungstools in Ihrer Umgebung exportieren.

Mit dem **fortlaufenden Export**-Feature von Defender für Cloud können Sie umfassend anpassen, *was* exportiert wird und *wohin*. Weitere Informationen finden Sie unter [Fortlaufendes Exportieren von Microsoft Defender für Cloud-Daten](continuous-export.md).

Obwohl das Feature als *fortlaufend* bezeichnet wird, gibt es auch eine Option zum Exportieren von wöchentlichen Momentaufnahmen. Bisher waren diese wöchentlichen Momentaufnahmen auf Sicherheitsbewertung und Daten zur Einhaltung gesetzlicher Bestimmungen beschränkt. Wir haben die Funktion zum Exportieren von Empfehlungen und Sicherheitsergebnissen hinzugefügt.

### <a name="auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga"></a>Automatische Bereitstellung von Lösungen zur Sicherheitsrisikobewertung – jetzt allgemein verfügbar

Im Oktober wurde [angekündigt](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview), dass Lösungen zur Sicherheitsrisikobewertung zur Seite für die automatische Bereitstellung von Defender für Cloud hinzugefügt werden. Dies ist für virtuelle Azure-Computer und Azure Arc-Computer in Abonnements relevant, die durch [Azure Defender für Server](defender-for-servers-introduction.md) geschützt sind. Dieses Feature ist jetzt allgemein verfügbar.

Wenn die [Integration mit Microsoft Defender for Endpunkt](integration-defender-for-endpoint.md) aktiviert ist, zeigt Defender für Cloud außerdem eine Auswahl an Lösungen zur Sicherheitsrisikobewertung an:

- (**NEU**) Das Microsoft Bedrohungs- und Schwachstellenmanagement-Modul von Microsoft Defender für Endpoint (siehe [die Release Note](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga))
- Der integrierte Qualys-Agent

Die von Ihnen gewählte Lösung wird automatisch auf unterstützten Maschinen aktiviert.

Erfahren Sie mehr unter [Automatische Konfiguration der Schwachstellenbewertung für Ihre Maschinen](auto-deploy-vulnerability-assessment.md).

### <a name="software-inventory-filters-in-asset-inventory-released-for-general-availability-ga"></a>Softwareinventurfilter im Bestandsverzeichnis – jetzt allgemein verfügbar

Im Oktober wurden neue Filter für die Seite [Bestandsverzeichnis](asset-inventory.md) [angekündigt](#software-inventory-filters-added-to-asset-inventory-in-preview), mit denen Sie Computer, auf denen bestimmte Software ausgeführt wird, auswählen und dabei sogar die gewünschten Versionen angeben können. Dieses Feature ist jetzt allgemein verfügbar.

Sie können die Softwareinventurdaten im **Azure Resource Graph Explorer** abfragen.

Um diese Features nutzen zu können, müssen Sie die [Integration mit Microsoft Defender for Endpoint](integration-defender-for-endpoint.md) aktivieren. 

Ausführliche Informationen, einschließlich Beispielabfragen von Kusto für Azure Resource Graph, finden Sie unter [Zugriff auf ein Softwareinventar](asset-inventory.md#access-a-software-inventory).

### <a name="new-aks-security-policy-added-to-default-initiative--for-use-by-private-preview-customers-only"></a>Der Standardinitiative wurde eine neue AKS-Sicherheitsrichtlinie hinzugefügt – nur für Kunden der privaten Vorschau.

Um sicherzustellen, dass Kubernetes-Workloads standardmäßig sicher sind, fügt Defender für Cloud Richtlinien auf Kubernetes-Ebene und Härtungsempfehlungen hinzu, einschließlich Erzwingungsoptionen mit Kubernetes-Zugangskontrolle.

Im Rahmen dieses Projekts haben wir eine Richtlinie und Empfehlung (standardmäßig deaktiviert) für die Verhinderung der Bereitstellung in Kubernetes-Clustern hinzugefügt. Die Richtlinie befindet sich in der Standardinitiative, ist aber nur für Organisationen relevant, die sich für die zugehörige private Vorschau registrieren.

Sie können die Richtlinien und Empfehlung („Kubernetes-Cluster sollten die Bereitstellung anfälliger Images verhindern“) problemlos ignorieren. Dies hat keine Auswirkungen auf Ihre Umgebung. 

Wenn Sie an der privaten Vorschau teilnehmen möchten, müssen Sie Mitglied des Rings „Private Vorschau“ sein. Wenn Sie noch kein Mitglied sind, senden Sie [hier](https://aka.ms/atscale) eine Anforderung. Mitglieder werden benachrichtigt, wenn die Vorschau beginnt.

## <a name="october-2021"></a>Oktober 2021

Updates im Oktober:

- [ Microsoft Threat and Vulnerability Management als Lösung zur Schwachstellenbewertung hinzugefügt (in der Vorschau)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)
- [Schwachstellenbewertungslösungen können jetzt automatisch aktiviert werden (in der Vorschau)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)
- [Software-Inventarisierungsfilter zum Bestandsverzeichnis hinzugefügt (in Vorschau)](#software-inventory-filters-added-to-asset-inventory-in-preview)
- [Ändern des Präfix einiger Warnungstypen von „ARM_“ in „VM_“](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)
- [Änderungen an der Logik einer Sicherheitsempfehlung für Kubernetes-Cluster](#changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters)
- [Empfehlungen Detailseiten zeigen nun verwandte Empfehlungen an](#recommendations-details-pages-now-show-related-recommendations)
- [Neue Warnungen für Azure Defender für Kubernetes (in der Vorschau)](#new-alerts-for-azure-defender-for-kubernetes-in-preview)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>Microsoft Threat and Vulnerability Management als Lösung zur Schwachstellenbewertung hinzugefügt (in der Vorschau)

Wir haben die Integration zwischen [Azure Defender für Server](defender-for-servers-introduction.md) und Microsoft Defender für Endpoint erweitert, um eine neue Sicherheitsrisikobewertung für Ihre Computer zu unterstützen: [Bedrohungs- und Sicherheitsrisikomanagement von Microsoft](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt). 

Verwenden Sie **Bedrohungs- und Schwachstellenmanagement**, um Schwachstellen und Fehlkonfigurationen nahezu in Echtzeit zu erkennen, wenn die [Integration mit Microsoft Defender für Endpoint](integration-defender-for-endpoint.md) aktiviert ist, ohne dass zusätzliche Agenten oder regelmäßige Scans erforderlich sind. Das Bedrohungs- und Schwachstellenmanagement priorisiert Schwachstellen auf der Grundlage der Bedrohungslandschaft und der Entdeckungen in Ihrem Unternehmen.

Verwenden Sie die Sicherheitsempfehlung "[Eine Lösung zur Bewertung von Schwachstellen sollte auf Ihren virtuellen Maschinen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)", um die von der Bedrohungs- und Schwachstellenverwaltung erkannten Schwachstellen für Ihre [unterstützten Maschinen](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true) anzuzeigen. 

Um die Schwachstellen auf bestehenden und neuen Maschinen automatisch zu erkennen, ohne die Empfehlung manuell korrigieren zu müssen, siehe [Schwachstellenbewertungslösungen können jetzt automatisch aktiviert werden (in der Vorschau)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview).

Weitere Informationen finden Sie unter [Untersuchen Sie Schwachstellen mit dem Bedrohungs- und Schwachstellenmanagement von Microsoft Defender for Endpoint](deploy-vulnerability-assessment-tvm.md).

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>Lösungen zur Schwachstellenanalyse können jetzt automatisch aktiviert werden (in der Vorschau)

Die Seite für die automatische Bereitstellung im Security Center enthält jetzt die Option zur automatischen Aktivierung einer Lösung zur Schwachstellenanalyse für virtuelle Azure-Maschinen und Azure Arc-Maschinen in Abonnements, die durch [Azure Defender für Server](defender-for-servers-introduction.md) geschützt sind.

Wenn die [Integration mit Microsoft Defender for Endpunkt](integration-defender-for-endpoint.md) aktiviert ist, zeigt Defender für Cloud außerdem eine Auswahl an Lösungen zur Sicherheitsrisikobewertung an:

- (**NEU**) Das Microsoft Bedrohungs- und Schwachstellenmanagement-Modul von Microsoft Defender für Endpoint (siehe [die Release Note](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview))
- Der integrierte Qualys-Agent

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/auto-provision-vulnerability-assessment-agent.png" alt-text="Konfigurieren Sie die automatische Bereitstellung von Microsofts Bedrohungs- und Schwachstellenmanagement über das Azure Security Center.":::

Die von Ihnen gewählte Lösung wird automatisch auf unterstützten Maschinen aktiviert.

Erfahren Sie mehr unter [Automatische Konfiguration der Schwachstellenbewertung für Ihre Maschinen](auto-deploy-vulnerability-assessment.md).

### <a name="software-inventory-filters-added-to-asset-inventory-in-preview"></a>Software-Inventarisierungsfilter zum Anlageninventar hinzugefügt (in Vorschau)

Die Seite [Anlageninventar](asset-inventory.md) enthält jetzt einen Filter zur Auswahl von Rechnern, auf denen bestimmte Software läuft - und sogar zur Angabe der gewünschten Versionen. 

Außerdem können Sie die Softwareinventardaten im **Azure Resource Graph Explorer** abfragen.

Um diese neuen Funktionen nutzen zu können, müssen Sie die [Integration mit Microsoft Defender for Endpoint](integration-defender-for-endpoint.md) aktivieren. 

Ausführliche Informationen, einschließlich Beispielabfragen von Kusto für Azure Resource Graph, finden Sie unter [Zugriff auf ein Softwareinventar](asset-inventory.md#access-a-software-inventory).

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/software-inventory.png" alt-text="Wenn Sie die Lösung für Bedrohungen und Schwachstellen aktiviert haben, bietet das Bestandsverzeichnis des Security Centers einen Filter zur Auswahl von Ressourcen anhand ihrer installierten Software.":::

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>Ändern des Präfix einiger Warnungstypen von „ARM_“ in „VM_“ 

Im Juli 2021 haben wir eine [logische Neuorganisation des Azure Defender für Resource Manager-Warnungen](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) angekündigt. 

Im Rahmen einer logischen Umstrukturierung einiger Azure Defender-Pläne haben wir einundzwanzig Alarme von [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) nach [Azure Defender für Server](defender-for-servers-introduction.md) verschoben.

Mit diesem Update haben wir die Präfixe dieser Warnungen so geändert, dass sie mit dieser Neuzuordnung übereinstimmen, und „ARM_“ durch „VM_“ ersetzt, wie in der folgenden Tabelle gezeigt:

| Ursprünglicher Name                                  | Aus dieser Änderung                              |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |
|||

Hier finden Sie weitere Informationen zu den Plänen [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) und [Azure Defender für Server](defender-for-servers-introduction.md).

### <a name="changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters"></a>Änderungen an der Logik einer Sicherheitsempfehlung für Kubernetes-Cluster

Die Empfehlung „Kubernetes-Cluster dürfen nicht den Standardnamespace verwenden“ verhindert bei einer Reihe von Ressourcentypen die Verwendung des Standardnamespace. Zwei der Ressourcentypen, die in dieser Empfehlung enthalten waren, wurden entfernt: ConfigMap und Secret. 

Weitere Informationen zu dieser Empfehlung und zum Härten Ihrer Kubernetes-Cluster finden Sie unter [Grundlegendes zu Azure Policy für Kubernetes-Cluster](../governance/policy/concepts/policy-for-kubernetes.md).

### <a name="recommendations-details-pages-now-show-related-recommendations"></a>Empfehlungen Detailseiten zeigen nun verwandte Empfehlungen an

Um die Beziehungen zwischen verschiedenen Empfehlungen zu verdeutlichen, haben wir den Detailseiten vieler Empfehlungen den Bereich **Verwandte Empfehlungen** hinzugefügt. 

Die drei Beziehungstypen, die auf diesen Seiten angezeigt werden, sind:

- **Voraussetzung**: Eine Empfehlung, die vor der ausgewählten Empfehlung abgeschlossen werden muss.
- **Alternative**: Eine andere Empfehlung, die eine weitere Möglichkeit bietet, die Ziele der ausgewählten Empfehlung zu erreichen.
- **Abhängig**: Eine Empfehlung, für die die ausgewählte Empfehlung eine Voraussetzung ist.

Für jede verwandte Empfehlung wird in der Spalte „Betroffene Ressourcen“ die Anzahl fehlerhafter Ressourcen angezeigt.

> [!TIP]
> Wenn eine verwandte Empfehlung ausgegraut ist, ist ihre Abhängigkeit noch nicht abgeschlossen und die Empfehlung ist daher nicht verfügbar.

Ein Beispiel für verwandte Empfehlungen:

1. Security Center überprüft Ihre Computer auf unterstützte Lösungen zur Sicherheitsrisikobewertung:<br>
    **Auf Ihren virtuellen Computern muss eine Lösung zur Sicherheitsrisikobewertung installiert werden**

1. Wenn eine gefunden wird, werden Sie über erkannte Sicherheitsrisiken benachrichtigt:<br>
    **Sicherheitsrisiken für VMs müssen behoben werden**

Natürlich kann Security Center Sie nicht über erkannte Sicherheitsrisiken benachrichtigen, solange keine unterstützte Lösung zur Sicherheitsrisikobewertung gefunden wird.

Deshalb gilt Folgendes:

 - Empfehlung 1 ist eine Voraussetzung für Empfehlung 2
 - Empfehlung 2 hängt von Empfehlung 1 ab

:::image type="content" source="media/release-notes/related-recommendations-solution-not-found.png" alt-text="Screenshot der Empfehlung zum Bereitstellen einer Lösung zur Sicherheitsrisikobewertung.":::

:::image type="content" source="media/release-notes/related-recommendations-vulnerabilities-found.png" alt-text="Screenshot der Empfehlung zum Beheben von entdeckten Sicherheitsrisiken.":::



### <a name="new-alerts-for-azure-defender-for-kubernetes-in-preview"></a>Neue Warnungen für Azure Defender für Kubernetes (in der Vorschau)

Wir haben zwei Vorschauwarnungen hinzugefügt, um den Bedrohungsschutz von Azure Defender für Kubernetes zu erweitern.

Diese Warnungen werden basierend auf einem neuen Machine Learning-Modell und erweiterten Kubernetes-Analysen generiert, wobei mehrere Bereitstellungs- und Rollenzuweisungsattribute anhand vorheriger Aktivitäten im Cluster und in allen von Azure Defender überwachten Clustern gemessen werden.

| Warnung (Warnungstyp)                                                                 | Beschreibung                                                                                                                                                                                                                                                                                                                                                      | MITRE-Taktik | Schweregrad |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| **Anomale Podbereitstellung (Vorschau)**<br>(K8S_AnomalousPodDeployment)             | Bei der Kubernetes-Überwachungsprotokollanalyse wurde eine Podbereitstellung erkannt, die basierend auf der vorherigen Podbereitstellungsaktivität anomal Ist. Diese Aktivität wird als Anomalie betrachtet, wenn berücksichtigt wird, in welcher Beziehung die verschiedenen Features im Bereitstellungsvorgang zueinander stehen. Die von dieser Analyse überwachten Features umfassen die verwendete Containerimageregistrierung, das Konto, das die Bereitstellung durchführt, den Wochentag, die Häufigkeit der Ausführung von Podbereitstellungen durch dieses Konto, den im Vorgang verwendeten Benutzer-Agent (dabei handelt es sich um einen Namespace, für den die Podbereitstellung zu häufig ausgeführt wird, oder ein anderes Feature). Die wichtigsten Gründe für das Auslösen dieser Warnung als anomale Aktivität werden in den erweiterten Eigenschaften der Warnung ausführlich beschrieben. | Ausführung | Medium |
| **Im Kubernetes-Cluster zugewiesene übermäßige Rollenberechtigungen (Vorschau)**<br>(K8S_ServiceAcountPermissionAnomaly) | Bei der Analyse der Kubernetes-Überwachungsprotokolle wurde eine übermäßige Zuweisung von Berechtigungen zu Ihrem Cluster erkannt. Bei der Untersuchung von Rollenzuweisungen sind die aufgeführten Berechtigungen für das jeweilige Dienstkonto ungewöhnlich. Bei dieser Erkennung werden vorherige Rollenzuweisungen für dasselbe Dienstkonto über von Azure überwachte Cluster hinweg, das Volume pro Berechtigung und die Auswirkungen der jeweiligen Berechtigung berücksichtigt. Das für diese Warnung verwendete Anomalieerkennungsmodell berücksichtigt, wie diese Berechtigung über alle von Azure Defender überwachte Cluster hinweg verwendet wird. | Berechtigungsausweitung | Niedrig |
|||

Eine vollständige Liste der Kubernetes-Warnungen finden Sie unter [Warnungen für Container: Kubernetes-Cluster](alerts-reference.md#alerts-k8scluster).

## <a name="september-2021"></a>September 2021

Im September wurde das folgende Update veröffentlicht:

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance-in-preview"></a>Zwei neue Empfehlungen zur Prüfung von Betriebssystemkonfigurationen für die Einhaltung der Azure-Sicherheitsgrundlagen (in der Vorschau)

Die folgenden beiden Empfehlungen wurden veröffentlicht, um die Konformität Ihrer Computer mit der [Windows-Sicherheitsbaseline](../governance/policy/samples/guest-configuration-baseline-windows.md) und der [Linux-Sicherheitsbaseline](../governance/policy/samples/guest-configuration-baseline-linux.md) zu bewerten:

- Für Windows-Rechner sollten [Schwachstellen in der Sicherheitskonfiguration auf Ihren Windows-Rechnern behoben werden (mit Hilfe der Gastkonfiguration).](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
- Für Linux-Rechner sollten [Schwachstellen in der Sicherheitskonfiguration auf Ihren Linux-Rechnern behoben werden (mit Hilfe der Gastkonfiguration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda).

Diese Empfehlungen verwenden die Gastkonfigurationsfeature von Azure Policy, um die Betriebssystemkonfiguration einer Maschine mit der im [Azure Security Benchmark](/security/benchmark/azure/overview) definierten Baseline zu vergleichen.

Weitere Informationen zur Verwendung dieser Empfehlungen finden Sie unter [Härten der Betriebssystemkonfiguration eines Computers mithilfe der Gastkonfiguration](apply-security-baseline.md).

## <a name="august-2021"></a>August 2021

Updates im August:

- [Microsoft Defender für Endpunkt für Linux wird jetzt von Azure Defender für Server (Vorschauversion) unterstützt](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [Zwei neue Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen (Vorschauversion)](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [Integrierte Problembehandlung und Anleitungen zum Beheben häufiger Probleme](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [Azure Audit-Berichte des Dashboards für die Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- [Empfehlung „Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden“ veraltet](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [Azure Defender für Containerregistrierungen sucht jetzt mit Azure Private Link nach Sicherheitsrisiken in Registrierungen](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [Security Center kann jetzt die Azure Policy-Erweiterung für Gastkonfigurationen (Vorschauversion) automatisch bereitstellen](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- [Empfehlungen zum Aktivieren von Azure Defender-Plänen unterstützen jetzt die Option „Erzwingen“](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [CSV-Exporte von Empfehlungsdaten jetzt auf 20 MB beschränkt](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [Die Seite „Empfehlungen“ enthält jetzt mehrere Ansichten](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>Microsoft Defender für Endpunkt für Linux wird jetzt von Azure Defender für Server (Vorschauversion) unterstützt

[Azure Defender für Server](defender-for-servers-introduction.md) beinhaltet eine integrierte Lizenz für [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Dadurch stehen umfassende EDR-Funktionen (Endpoint Detection and Response; Endpunkterkennung und -reaktion) zur Verfügung.

Wenn Defender für Endpunkt eine Bedrohung erkennt, wird eine Warnung ausgelöst. Die Warnung wird in Security Center angezeigt. Über Security Center können Sie auch zur Defender für Endpunkt-Konsole wechseln und eine ausführliche Untersuchung durchführen, um das Ausmaß des Angriffs zu ermitteln.

Während des Vorschauzeitraums stellen Sie den [Defender für Endpunkt für Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux)-Sensor auf unterstützten Linux-Computern auf eine von zwei Arten zur Verfügung, je nachdem, ob Sie ihn bereits auf Ihren Windows-Computern bereitgestellt haben:

- [Bestehende Benutzer mit aktivierten erweiterten Sicherheitsfeatures von Defender für Cloud und Microsoft Defender für Endpunkt für Windows](integration-defender-for-endpoint.md#existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows)
- [Neue Benutzer, die die Integration in Microsoft Defender für Endpunkt für Windows noch nie aktiviert haben](integration-defender-for-endpoint.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

Weitere Informationen finden Sie unter [Schützen Sie Ihre Endpunkte mit der in Security Center integrierten EDR-Lösung: Microsoft Defender für den Endpunkt](integration-defender-for-endpoint.md).

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>Zwei neue Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen (Vorschauversion)

Er wurden zwei **Vorschau**-Empfehlungen für die Bereitstellung und Wartung der Endpoint Protection-Lösungen auf Ihren Computern hinzugefügt. Beide Empfehlungen beinhalten Unterstützung für virtuelle Azure-Maschinen und Maschinen, die mit Azure Arc-aktivierten Servern verbunden sind.

|Empfehlung |BESCHREIBUNG |severity |
|---|---|---|
|[Endpoint Protection sollte auf Ihren Computern installiert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |Installieren Sie eine unterstützte Endpoint Protection-Lösung, um Ihre Computer vor Bedrohungen und Sicherheitsrisiken zu schützen.  <br> <a href="/azure/security-center/endpoint-protection-recommendations-technical">Informieren Sie sich über die Endpoint Protection-Evaluierung für Computer.</a><br />(Zugehörige Richtlinie: [Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Hoch |
|[Endpoint Protection-Integritätsprobleme sollten auf Ihren Computern gelöst werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |Beheben Sie Endpoint Protection-Integritätsprobleme auf Ihren virtuellen Computern, um diese vor den neuesten Bedrohungen und Sicherheitsrisiken zu schützen. Von Azure Security Center unterstützte Endpoint Protection-Lösungen sind [hier](./supported-machines-endpoint-solutions-clouds.md?tabs=features-windows) dokumentiert. Die Endpoint Protection-Bewertung ist <a href='/azure/security-center/endpoint-protection-recommendations-technical'>hier</a> dokumentiert.<br />(Zugehörige Richtlinie: [Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |Medium |
|||

> [!NOTE]
> Die Empfehlungen zeigen als Aktualisierungsintervall 8 Stunden an, aber es gibt einige Szenarien, in denen dies erheblich länger dauern kann. Wenn beispielsweise ein lokaler Computer gelöscht wird, dauert es 24 Stunden, bis Security Center die Löschung erkannt hat. Danach dauert es bis zu 8 Stunden, bis die Bewertungsinformationen zurückgegeben werden. In dieser speziellen Situation kann es daher 32 Stunden dauern, bis der Computer aus der Liste der betroffenen Ressourcen entfernt wird.
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="Indikator für das Aktualisierungsintervall für diese beiden neuen Security Center Empfehlungen":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>Integrierte Problembehandlung und Anleitungen zum Beheben häufiger Probleme

Ein neuer, dedizierter Bereich der Security Center-Seiten im Azure-Portal bietet einen sortierten, ständig wachsenden Satz von Selbsthilfematerialien zur Lösung gängiger Herausforderungen mit Security Center Azure Defender.

Wenn Sie ein Problem haben oder von unserem Supportteam Hilfe anfordern möchten, ist **Diagnose und Problemlösung** ein weiteres Tool, mit dem Sie die Lösung finden können:

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="Security Center-Seite „Diagnose und Problembehandlung“":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>Azure Audit-Berichte des Dashboards für die Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit

Die Symbolleiste des Dashboards für die Einhaltung gesetzlicher Bestimmungen bietet Azure- und Dynamics-Zertifizierungsberichte für die auf Ihre Abonnements angewendeten Standards. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Symbolleiste des Dashboards für die Einhaltung gesetzlicher Bestimmungen mit der Schaltfläche zum Generieren von Überwachungsberichten.":::

Sie können die Registerkarte für die relevanten Berichtstypen (PCI, SOC, ISO usw.) auswählen und Filter verwenden, um nach den benötigten spezifischen Berichten zu suchen.

Weitere Informationen finden Sie unter [Generieren von Konformitätsstatusberichten und -zertifikaten](regulatory-compliance-dashboard.md#generate-compliance-status-reports-and-certificates).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="Listen mit Registerkarten der verfügbaren Azure Audit -Berichte. Es werden Registerkarten für ISO-Berichte, SOC-Berichte, PCI und mehr angezeigt.":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>Empfehlung „Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden“ veraltet

Wir haben festgestellt, dass die Empfehlung **Log Analytics-Agent-Integritätsprobleme müssen auf Computern gelöst werden** Auswirkungen auf die Sicherheitsbewertungen haben, die dem Schwerpunkt von Security Center Cloud Security Posture Management (CSPM) nicht entsprechen. In der Regel bezieht sich CSPM auf die Ermittlung von Fehlkonfigurationen bei der Sicherheit. Probleme mit der Agent-Integrität gehören nicht in diese Problemkategorie.

Darüber hinaus handelt es sich bei der Empfehlung im Gegensatz zu den anderen Agents im Zusammenhang mit Security Center um eine Anomalie: Dies ist der einzige Agent mit einer Empfehlung im Zusammenhang mit Integritätsproblemen.

Die Empfehlung ist veraltet.

Daher haben wir auch geringfügige Änderungen an den Empfehlungen für die Installation des Log Analytics-Agents vorgenommen (**Der Log Analytics-Agent muss auf ... installiert sein**).

Diese Änderung wirkt sich mit hoher Wahrscheinlichkeit auf Ihre Sicherheitsbewertungen aus. Bei den meisten Abonnements erwarten wir, dass die Änderung zu einer höheren Bewertung führt. Es ist aber möglich, dass die Updates der Installationsempfehlung in einigen Fällen zu niedrigeren Bewertungen führen können.

> [!TIP]
> Diese Änderung wirkte sich auch auf die Seite [Ressourcenbestand](asset-inventory.md) aus, da diese den Überwachungsstatus eines Computers anzeigt (überwacht, nicht überwacht oder teilweise überwacht) – ein Zustand, der einen Agent mit Integritätsproblemen anzeigt.


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>Azure Defender für Containerregistrierungen sucht jetzt mit Azure Private Link nach Sicherheitsrisiken in Registrierungen
Azure Defender für Containerregistrierungen enthält eine Überprüfungen auf Sicherheitsrisiken für Images in Ihren Azure Container Registry-Registrierungen. Informationen zum Überprüfen Ihrer Registrierungen und zum Beheben der Ergebnissen finden Sie unter [Verwenden von Azure Defender für Containerregistrierungen zum Überprüfen Ihrer Images auf Sicherheitsrisiken](defender-for-container-registries-usage.md).

Um den Zugriff auf eine in Azure Container Registry gehostete Registrierung zu beschränken, weisen Sie den Registrierungsendpunkten private IP-Adressen des virtuellen Netzwerks zu, und verwenden Sie Azure Private Link, wie in [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](../container-registry/container-registry-private-link.md) beschrieben.

Im Rahmen unserer kontinuierlichen Bemühungen zur Unterstützung zusätzlicher Umgebungen und Anwendungsfälle überprüft Azure Defender jetzt auch Containerregistrierungen, die mit [Azure Private Link](../private-link/private-link-overview.md) geschützt werden.


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>Security Center kann jetzt die Azure Policy-Erweiterung für Gastkonfigurationen (Vorschauversion) automatisch bereitstellen
Mit Azure Policy können Einstellungen innerhalb eines Computers überwacht werden. Dies gilt für in Azure ausgeführte Computer sowie für über Arc verbundene Computer. Für die Überprüfung verwenden Sie die Erweiterung und den Client Guest Configuration. Weitere Informationen finden Sie in [Grundlegendes zur Gastkonfiguration von Azure Policy](../governance/policy/concepts/guest-configuration.md).

Mit diesem Update können Sie jetzt festlegen, dass Security Center diese Erweiterung automatisch für alle unterstützten Computer bereitstellt. 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="Aktivieren Sie die automatische Bereitstellung der Erweiterung für Gastkonfigurationen.":::

Weitere Informationen zur Funktionsweise der automatischen Bereitstellung finden Sie unter [Konfigurieren der automatischen Bereitstellung für Agents und Erweiterungen](enable-data-collection.md).

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>Empfehlungen zum Aktivieren von Azure Defender-Plänen unterstützen jetzt die Option „Erzwingen“
Security Center enthält zwei Features, die sicherstellen, dass neu erstellte Ressourcen auf sichere Weise bereitgestellt werden: **Erzwingen** und **Verweigern**. Wenn eine Empfehlung diese Optionen bietet, können Sie sicherstellen, dass Ihre Sicherheitsanforderungen erfüllt werden, sobald jemand versucht, eine Ressource zu erstellen:

- **Verweigern** verhindert, dass fehlerhafte Ressourcen erstellt werden.
- **Erzwingen** sorgt automatisch dafür, dass nicht konforme Ressourcen bei ihrer Erstellung korrigiert werden.

Mit diesem Update ist nun die Option „Erzwingen“ in den Empfehlungen zur Aktivierung von Azure Defender-Plänen verfügbar (z. B. **Azure Defender für App Service muss aktiviert sein**, **Azure Defender für Key Vault muss aktiviert sein**, **Azure Defender für Speicher muss aktiviert sein**).

Weitere Informationen zu diesen Optionen finden Sie unter [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>CSV-Exporte von Empfehlungsdaten jetzt auf 20 MB beschränkt

Es wurde ein Grenzwert von 20 MB für das Exportieren von Security Center-Empfehlungsdaten eingerichtet.

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="Schaltfläche „CSV-Bericht herunterladen“ zum Exportieren von Empfehlungsdaten in Security Center":::

Wenn Sie größere Datenmengen exportieren müssen, verwenden Sie vor der Auswahl die verfügbaren Filter, oder wählen Sie Teilmengen Ihrer Abonnements aus, und laden Sie die Daten in Batches herunter.

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Filtern von Abonnements im Azure-Portal":::

Erfahren Sie mehr über das [Ausführen eines CSV-Exports Ihrer Sicherheitsempfehlungen](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations).



### <a name="recommendations-page-now-includes-multiple-views"></a>Die Seite „Empfehlungen“ enthält jetzt mehrere Ansichten

Die Seite „Empfehlungen“ verfügt jetzt über zwei Registerkarten, um alternative Möglichkeiten zum Anzeigen der Empfehlungen zu bieten, die für Ihre Ressourcen relevant sind:

- **Empfehlungen zur Sicherheitsbewertung**: Auf dieser Registerkarte können Sie die Liste der Empfehlungen, gruppiert nach Sicherheitskontrollen, anzeigen. Weitere Informationen zu diesen Steuerelementen finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).
- **Alle Empfehlungen**: Auf dieser Registerkarte können Sie die Liste der Empfehlungen als einfache Liste anzeigen. Diese Registerkarte verdeutlicht zudem, welche Initiative (einschließlich Standards zur Einhaltung gesetzlicher Bestimmungen) die Empfehlung generiert hat. Weitere Informationen zu Initiativen und deren Beziehung zu Empfehlungen finden Sie unter [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md)

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="Registerkarten zum Ändern der Ansicht der Empfehlungsliste in Azure Security Center.":::

## <a name="july-2021"></a>Juli 2021

Zu den Updates im Juli gehören:

- [Der Azure Sentinel-Connector enthält jetzt optionale bidirektionale Warnungssynchronisierung (Vorschauversion)](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [Logische Neuorganisation des Azure Defender für Resource Manager-Warnungen](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [Erweiterungen der Empfehlung zum Aktivieren Azure Disk Encryption (ADE)](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [Fortlaufender Export von Daten zur Sicherheitsbewertung und zur Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst wurden (GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [Bewertungs-API-Felder „FirstEvaluationDate“ und „StatusChangeDate“ jetzt in Arbeitsbereichsschemas und Logik-Apps verfügbar](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- [Arbeitsmappenvorlage „Konformität im Zeitverlauf“ zum Azure Monitor Workbooks-Katalog hinzugefügt](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>Der Azure Sentinel-Connector enthält jetzt optionale bidirektionale Warnungssynchronisierung (Vorschauversion)

Security Center ist nativ in [Azure Sentinel](../sentinel/index.yml), der cloudnativen SIEM- und SOAR-Lösung von Azure, integriert. 

Azure Sentinel umfasst integrierte Connectors für Azure Security Center auf Abonnement- und Mandantenebene. Weitere Informationen finden Sie unter [Streamen von Warnungen in Azure Sentinel](export-to-siem.md#stream-alerts-to-microsoft-sentinel).

Wenn Sie Azure Defender mit Azure Sentinel verbinden, wird der Status der in den Azure Sentinel-Dienst übernommenen Azure Defender-Warnungen zwischen den beiden Diensten synchronisiert. Wenn also beispielsweise eine Warnung in einem Azure Defender geschlossen wird, wird diese Warnung auch in Azure Sentinel als geschlossen angezeigt. Das Ändern des Status einer Warnung in Azure Defender wirkt sich „nicht“ auf den Status von Azure Sentinel-**Incidents** aus, die die synchronisierte Azure Sentinel-Warnung enthalten, sondern nur auf den Status der synchronisierten Warnung selbst.

Wenn Sie die Previewfunktion **bidirektionale Warnungssynchronisierung** aktivieren, wird der Status der ursprünglichen Azure Defender-Warnungen automatisch mit Azure Sentinel-Incidents synchronisiert, die die Kopien dieser Azure Defender-Warnungen enthalten. Wenn also beispielsweise ein Azure Sentinel-Incident, der eine Azure Defender-Warnung enthält, geschlossen wird, schließt Azure Defender automatisch die entsprechende ursprüngliche Warnung.

Weitere Informationen finden Sie unter [Verbinden von Azure Defender-Benachrichtigungen aus Azure Security Center](../sentinel/connect-azure-security-center.md).

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Logische Neuorganisation des Azure Defender für Resource Manager-Warnungen

Die unten aufgeführten Warnungen wurden im Rahmen des [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)-Plans bereitgestellt.

Im Rahmen einer logischen Neuorganisation für einige der Azure Defender-Pläne wurden einzelne Warnungen vom **Azure Defender für Resource Manager** in den **Azure Defender für Server** verschoben.

Die Warnungen sind nach zwei Hauptprinzipien organisiert:

- Warnungen, die Schutz auf der Steuerungsebene für viele Azure-Ressourcentypen bieten, werden ein Teil des Azure Defender für Resource Manager
- Warnungen, die bestimmte Workloads schützen, befinden sich im Azure Defender-Plan, der sich auf diese Workload bezieht

Dies sind die Warnungen, die Teil des Azure Defender für Resource Manager waren und aufgrund dieser Änderung jetzt Teil von Azure Defender für Server sind:

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

Hier finden Sie weitere Informationen zu den Plänen [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) und [Azure Defender für Server](defender-for-servers-introduction.md).


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>Erweiterungen der Empfehlung zum Aktivieren Azure Disk Encryption (ADE)

Aufgrund von Benutzerfeedback haben wir die Empfehlung **Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden** umbenannt.

Die neue Empfehlung verwendet die gleiche Bewertungs-ID und heißt **Virtuelle Computer sollten temporäre Datenträger, Caches und Datenflüsse zwischen Compute- und Speicherressourcen verschlüsseln**.

Die Beschreibung wurde ebenfalls aktualisiert, um den Zweck dieser Empfehlung zur Härtung verständlicher zu machen:

| Empfehlung                                                                                               | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | severity |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **Virtuelle Computer sollten temporäre Datenträger, Caches und Datenflüsse zwischen Compute- und Speicherressourcen verschlüsseln** | Standardmäßig werden die Datenträger für das Betriebssystem und für die Daten eines virtuellen Computers im Ruhespeicher mithilfe von plattformseitig verwalteten Schlüsseln verschlüsselt. Temporäre Datenträger und Datencaches werden nicht verschlüsselt, und auch während der Übertragung zwischen Compute- und Speicherressourcen werden Daten nicht verschlüsselt. Einen Vergleich der verschiedenen Datenträgerverschlüsselungstechnologien in Azure finden Sie unter https://aka.ms/diskencryptioncomparison.<br>Verwenden Sie Azure Disk Encryption, um sämtliche dieser Daten zu verschlüsseln. Ignorieren Sie diese Empfehlung, wenn (1) Sie die Verschlüsselung auf dem Host verwenden oder wenn die (2) serverseitige Verschlüsselung auf Managed Disks Ihre Sicherheitsanforderungen erfüllt. Weitere Informationen erhalten Sie unter „Serverseitige Verschlüsselung von Azure Disk Storage“. | Hoch     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>Fortlaufender Export von Daten zur Sicherheitsbewertung und zur Einhaltung gesetzlicher Bestimmungen, veröffentlicht zur allgemeinen Verfügbarkeit

Der [fortlaufende Export](continuous-export.md) stellt den Mechanismus zum Exportieren Ihrer Sicherheitswarnungen und Empfehlungen für die Nachverfolgung mit anderen Überwachungstools in Ihrer Umgebung bereit.

Wenn Sie den fortlaufenden Export einrichten, konfigurieren Sie, was exportiert wird und wohin die Daten übertragen werden. Weitere Informationen finden Sie unter [Übersicht über den fortlaufende Export](continuous-export.md).

Wir haben dieses Feature im Laufe der Zeit verbessert und erweitert:

- Im November 2020 haben wir die **Vorschauoption** hinzugefügt, um Änderungen an Ihre **Sicherheitsbewertung** zu streamen.<br/>Vollständige Informationen finden Sie unter [Die Sicherheitsbewertung ist jetzt im fortlaufenden Export (Vorschauversion) verfügbar](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview).

- Im Dezember 2020 haben wir die **Vorschaufunktion** hinzugefügt, mit der Sie Änderungen an Ihren **Daten zur Bewertung der Einhaltung gesetzlicher Bestimmungen** streamen können.<br/>Ausführliche Informationen finden Sie unter [Fortlaufender Export ruft neue Datentypen ab (Vorschau)](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies).

Mit diesem Update werden diese beiden Optionen zur allgemeinen Verfügbarkeit veröffentlicht. 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst wurden (GA)

Im Februar 2021 haben wir die **Vorschau** eines dritten Datentyps zu den Triggeroptionen für Ihre Workflowautomatisierungen hinzugefügt: Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen. Weitere Informationen finden Sie unter [Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst werden](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview).

Mit diesem Update wird diese Triggeroption zur allgemeinen Verfügbarkeit veröffentlicht.

Informieren Sie sich unter [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md) über die Nutzung der Tools für die Workflowautomatisierung.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Verwenden von Änderungen bei Bewertungen der Einhaltung gesetzlicher Bestimmungen zum Auslösen der Workflowautomatisierung" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>Bewertungs-API-Felder „FirstEvaluationDate“ und „StatusChangeDate“ jetzt in Arbeitsbereichsschemas und Logik-Apps verfügbar

Im Mai 2021 haben wir der Bewertungs-API zwei neue Felder hinzugefügt: **FirstEvaluationDate** und **StatusChangeDate**. Vollständige Informationen finden Sie unter [Die Bewertungs-API wurde um zwei neue Felder erweitert](release-notes-archive.md#assessments-api-expanded-with-two-new-fields).

Auf diese Felder kann über die REST-API, Azure Resource Graph, den fortlaufenden Export und in CSV-Exporten zugegriffen werden.

Mit dieser Änderung stellen wir die Informationen im Log Analytics-Arbeitsbereichsschema und in Logik-Apps zur Verfügung.


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>Arbeitsmappenvorlage „Konformität im Zeitverlauf“ zum Azure Monitor Workbooks-Katalog hinzugefügt

Im März haben wir die integrierte Azure Monitor-Arbeitsmappenerfahrung in Security Center angekündigt (siehe [Integration von Azure Monitor-Arbeitsmappen in Security Center und Bereitstellung von drei Vorlagen](release-notes-archive.md#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)).

Das erste Release enthielt drei Vorlagen zum Erstellen dynamischer und visueller Berichte über den Sicherheitsstatus Ihrer Organisation.

Wir haben nun eine Arbeitsmappe hinzugefügt, die speziell für die Nachverfolgung der Konformität eines Abonnements mit den dafür geltenden gesetzlichen Vorschriften oder Branchenstandards bestimmt ist. 

Informieren Sie sich über die Nutzung dieser Berichte oder die Erstellung eigener Berichte unter [Erstellen umfassender interaktiver Berichte für Security Center-Daten](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Azure Security Center-Arbeitsmappe „Konformität im Zeitverlauf“":::


## <a name="june-2021"></a>Juni 2021

Zu den Updates im Juni gehören:

- [Neue Warnung für Azure Defender für Key Vault](#new-alert-for-azure-defender-for-key-vault)
- [Empfehlungen zur Verschlüsselung mit standardmäßig deaktivierten kundenseitig verwalteten Schlüsseln](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Änderung des Präfix für Kubernetes-Warnungen von „AKS_“ in „K8S_“](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- [Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Neue Warnung für Azure Defender für Key Vault

Wir haben die folgende Warnung hinzugefügt, um den Bedrohungsschutz von Azure Defender für Key Vault zu erweitern:

| Warnung (Warnungstyp)                                                                 | Beschreibung                                                                                                                                                                                                                                                                                                                                                      | MITRE-Taktik | Schweregrad |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Zugriff auf einen Schlüsseltresor von einer verdächtigen IP-Adresse<br>(KV_SuspiciousIPAccess)  | Auf einen Schlüsseltresor ist ein erfolgreicher Zugriff von einer IP-Adresse erfolgt, die von Microsoft Threat Intelligence als verdächtige IP-Adresse identifiziert wurde. Dies kann ggf. ein Hinweis darauf sein, dass Ihre Infrastruktur kompromittiert wurde. Wir empfehlen Ihnen, dies eingehender zu untersuchen. Weitere Informationen finden Sie unter [Threat Intelligence-Funktionen von Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). | Zugriff auf Anmeldeinformationen                            | Medium   |
|||

Weitere Informationen finden Sie unter
- [Einführung in Azure Defender für Key Vault](defender-for-resource-manager-introduction.md)
- [Reagieren auf Warnungen zu Azure Defender für Key Vault](defender-for-key-vault-usage.md)
- [Liste mit Warnungen von Azure Defender für Key Vault](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>Empfehlungen zur Verschlüsselung mit standardmäßig deaktivierten kundenseitig verwalteten Schlüsseln

Security Center enthält mehrere Empfehlungen zur Verschlüsselung von ruhenden Daten mit kundenseitig verwalteten Schlüsseln, z. B.:

- Containerregistrierungen müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden
- Azure Cosmos DB-Konten sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.
- Azure Machine Learning-Arbeitsbereiche müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden

Daten werden in Azure automatisch mit plattformseitig verwalteten Schlüsseln verschlüsselt. Daher sollten kundenseitig verwaltete Schlüssel nur angewendet werden, wenn dies zur Einhaltung einer bestimmten Richtlinie erforderlich ist, die in Ihrer Organisation durchgesetzt werden soll.

Aufgrund dieser Änderung sind die Empfehlungen zur Nutzung von kundenseitig verwalteten Schlüsseln jetzt **standardmäßig deaktiviert**. Sie können sie wieder aktivieren, falls dies für Ihre Organisation relevant ist. Ändern Sie hierfür den Parameter *Effect* für die entsprechende Sicherheitsrichtlinie in **AuditIfNotExists** oder **Enforce**. Weitere Informationen finden Sie auf der Seite mit den Informationen zum [Aktivieren einer Sicherheitsrichtlinie](tutorial-security-policy.md#enable-a-security-policy).

Diese Änderung wird in den Namen der Empfehlung durch das neue Präfix **[Bei Bedarf aktivieren]** widergespiegelt. Dies wird in den folgenden Beispielen veranschaulicht:

- [Bei Bedarf aktivieren] Speicherkonten müssen für die Verschlüsselung von ruhenden Daten einen kundenseitig verwalteten Schlüssel verwenden
- [Bei Bedarf aktivieren] Containerregistrierungen müssen mit einem kundenseitig verwalteten Schlüssel verschlüsselt werden
- [Bei Bedarf aktivieren] Azure Cosmos DB-Konten müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="CMK-Empfehlungen von Security Center sind standardmäßig deaktiviert" lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Änderung des Präfix für Kubernetes-Warnungen von „AKS_“ in „K8S_“

Azure Defender für Kubernetes wurde kürzlich so erweitert, dass lokal und in Umgebungen mit mehreren Clouds gehostete Kubernetes-Cluster geschützt werden. Weitere Informationen finden Sie unter [Verwenden von Azure Defender für Kubernetes zum Schutz von Hybrid- und Multi-Cloud-Bereitstellungen von Kubernetes (Vorschau)](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

Um deutlich zu machen, dass die von Azure Defender für Kubernetes bereitgestellten Sicherheitswarnungen nicht mehr auf Cluster in Azure Kubernetes Service beschränkt sind, haben wir das Präfix für die Warnungstypen von „AKS_“ in „K8S_“ geändert. Bei Bedarf wurden auch die Namen und Beschreibungen aktualisiert. Ein Beispiel:

|Warnung (Warnungstyp)|Beschreibung|
|----|----|
|Erkannte Tools für Kubernetes-Penetrationstests<br>(**AKS** _PenTestToolsKubeHunter)|Die Analyse des Kubernetes-Überwachungsprotokolls hat die Verwendung von Kubernetes-Penetrationstesttools im **AKS**-Cluster erkannt. Dieses Verhalten kann zwar legitim sein, aber Angreifer können solche öffentlichen Tools für böswillige Zwecke nutzen.
|||

wurde geändert in:

|Warnung (Warnungstyp)|Beschreibung|
|----|----|
|Erkannte Tools für Kubernetes-Penetrationstests<br>(**K8S** _PenTestToolsKubeHunter)|Die Analyse des Kubernetes-Überwachungsprotokolls hat die Verwendung von Kubernetes-Penetrationstesttools im **Kubernetes**-Cluster erkannt. Dieses Verhalten kann zwar legitim sein, aber Angreifer können solche öffentlichen Tools für böswillige Zwecke nutzen.|
|||

Unterdrückungsregeln, in denen auf mit „AKS_“ beginnende Warnungen verwiesen wird, wurden automatisch konvertiert. Wenn Sie SIEM-Exporte oder benutzerdefinierte Automatisierungsskripts eingerichtet haben, in denen anhand des Warnungstyps auf Kubernetes-Warnungen verwiesen wird, müssen diese mit den neuen Warnungstypen aktualisiert werden.

Eine vollständige Liste der Kubernetes-Warnungen finden Sie unter [Warnungen für Container: Kubernetes-Cluster](alerts-reference.md#alerts-k8scluster).

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>Zwei Empfehlungen der Sicherheitskontrolle „Systemupdates anwenden“ wurden als veraltet eingestuft

Die beiden folgenden Empfehlungen wurden als veraltet eingestuft:

- **Die Betriebssystemversion sollte für Ihre Clouddienstrollen aktualisiert werden**: Azure aktualisiert Ihr Gastbetriebssystem standardmäßig in regelmäßigen Abständen auf das neueste Image innerhalb der BS-Familie, die Sie in der Dienstkonfiguration (CSCFG-Datei) angegeben haben (z B. Windows Server 2016).
- **Für Kubernetes Service muss ein Upgrade auf eine Kubernetes-Version ohne Sicherheitsrisiko durchgeführt werden**: Die Evaluierungen dieser Empfehlung sind uns nicht weitreichend genug. Wir planen, die Empfehlung durch eine erweiterte Version zu ersetzen, die besser auf Ihre Sicherheitsanforderungen abgestimmt ist.
