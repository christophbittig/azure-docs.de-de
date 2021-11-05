---
title: Microsoft Defender für Cloud – eine Einführung
description: Verwenden Sie Microsoft Defender für Cloud, um Ihre Azure-, Hybrid- und Multi-Cloud-Ressourcen und -Workloads zu schützen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.custom: mvc, ignite-fall-2021
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: 0d75d3c3a7276e15f114f7356941f38ac2ac7c64
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100994"
---
# <a name="what-is-microsoft-defender-for-cloud"></a>Was ist Microsoft Defender für Cloud?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender für Cloud ist ein Tool für die Verwaltung des Sicherheitsstatus und den Schutz vor Bedrohungen. Es verbessert den Sicherheitsstatus Ihrer Cloudressourcen, und mithilfe der integrierten Microsoft Defender-Pläne schützt Defender für Cloud Workloads, die in Azure-, Hybrid- und anderen Cloudplattformen ausgeführt werden.

Defender für Cloud bietet die Tools, die Sie benötigen, um Ihre Ressourcen zu schützen, Ihren Sicherheitsstatus zu verfolgen, sich vor Cyberangriffen zu schützen und die Sicherheitsverwaltung zu optimieren. Defender für Cloud ist nativ integriert und kann auf einfache Weise über eine automatische Einrichtung bereitgestellt werden, um Ihre Ressourcen standardmäßig zu schützen.

Defender für Cloud erfüllt drei wichtige Anforderungen, wenn Sie die Sicherheit Ihrer Ressourcen und Workloads in der Cloud und lokal verwalten:

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-synopsis.png" alt-text="Grundlegendes zur Kernfunktionalität von Microsoft Defender für Cloud":::


|Sicherheitsanforderung  | Defender für Cloud-Lösung|
|---------|---------|
|**Fortlaufende Bewertung**: Informieren Sie sich über Ihren aktuellen Sicherheitsstatus.   | **Sicherheitsbewertung**: Anhand einer einzigen Bewertung können Sie auf einen Blick Ihre aktuelle Sicherheitssituation einschätzen – je höher die Bewertung, desto geringer das ermittelte Risiko.       |
|**Absicherung**: Härten Sie alle verbundenen Ressourcen und Dienste. | **Sicherheitsempfehlungen**: Angepasste und priorisierte Aufgaben zur Verbesserung Ihres Sicherheitsstatus. Sie implementieren eine Empfehlung, indem Sie die in der Empfehlung beschriebenen Schritte zur Bereinigung ausführen. Für viele Empfehlungen stellt Defender für Cloud eine Schaltfläche „Korrigieren“ für die automatisierte Implementierung bereit.|
|**Verteidigung**: Erkennen und beheben Sie Bedrohungen für diese Ressourcen und Dienste. | **Sicherheitswarnungen**: Bei Aktivierung der erweiterten Sicherheitsfeatures erkennt Defender für Cloud Bedrohungen für Ihre Ressourcen und Workloads. Diese Warnungen werden im Azure-Portal angezeigt, und Defender für Cloud kann sie auch per E-Mail an die zuständigen Mitarbeiter in Ihrer Organisation senden. Warnungen können bei Bedarf außerdem an SIEM-, SOAR- oder IT-Service-Management-Lösungen gestreamt werden. |

## <a name="posture-management-and-workload-protection"></a>Verwalten des Sicherheitsstatus und Schutz von Workloads

Die Features von Microsoft Defenders für Cloud decken die zwei Grundpfeiler der Cloudsicherheit ab: Verwaltung des Cloudsicherheitsstatus und Schutz von Cloudworkloads.

### <a name="cloud-security-posture-management-cspm"></a>Cloud Security Posture Management (CSPM)

Defender für Cloud bietet folgende Funktionen für die Verwaltung des Sicherheitsstatus:

- **Einblick** – zum Verständnis Ihres aktuellen Sicherheitsstatus.
- **Leitfaden zur Härtung** – für eine effiziente und effektive Verbesserung Ihrer Sicherheit.

Der zentrale Aspekt von Defender für Cloud zum Erreichen dieser Ziele ist die **Sicherheitsbewertung**. Defender für Cloud führt eine fortlaufende Bewertung Ihrer Ressourcen, Abonnements und Organisation in Bezug auf Sicherheitsprobleme durch. Anschließend werden alle Ergebnisse in einer einzigen Bewertung zusammengefasst, sodass Sie auf einen Blick Ihre aktuelle Sicherheitssituation erkennen können: je höher die Bewertung, desto geringer das ermittelte Risiko. 

Beim ersten Öffnen von Defender für Cloud werden die Ziele in Bezug auf Einblick und Härtung wie folgt erfüllt:

1. **Sicherheitsbewertung**: Basierend auf einer Bewertung der verbundenen Ressourcen im Vergleich zum [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/overview) wird eine Sicherheitsbewertung generiert. Anhand des Ergebnisses können Sie sich ein Bild von Ihrem Sicherheitsstatus machen, und über das Compliancedashboard können Sie Ihre Konformität mit dem integrierten Benchmark überprüfen. Wenn Sie die erweiterten Sicherheitsfeatures aktiviert haben, können Sie die Standards anpassen, die zur Bewertung Ihrer Konformität verwendet werden, und weitere Vorschriften (z. B. NIST und Azure CIS) oder organisationsspezifische Sicherheitsanforderungen hinzufügen.

1. **Empfehlungen zur Härtung**: Basierend auf erkannten Fehlern in Sicherheitskonfigurationen und Schwachstellen werden Empfehlungen zur Härtung bereitgestellt. Verwenden Sie diese Sicherheitsempfehlungen, um den Sicherheitsstatus der Azure-, Hybrid- und Multi-Cloud-Ressourcen Ihrer Organisation zu verbessern.

[Weitere Informationen zur Sicherheitsbewertung.](secure-score-security-controls.md)

### <a name="cloud-workload-protection-cwp"></a>Cloudworkloadschutz (Cloud Workload Protection, CWP)

Defender für Cloud stellt Sicherheitswarnungen auf der Grundlage von [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) bereit. Darüber hinaus bietet es eine Reihe von fortschrittlichen, intelligenten Schutzmaßnahmen für Ihre Workloads. Der Workloadschutz wird über Microsoft Defender-Pläne bereitgestellt, die auf die Ressourcentypen in Ihren Abonnements abgestimmt sind. Beispielsweise können Sie **Microsoft Defender für Storage** aktivieren, um über verdächtige Aktivitäten im Zusammenhang mit Ihren Azure Storage-Konten benachrichtigt zu werden. 


## <a name="azure-hybrid-and-multi-cloud-protections"></a>Azure-, Hybrid- und Multi-Cloud-Schutz

Da Defender für Cloud ein nativer Azure-Dienst ist, werden viele Azure-Dienste überwacht und geschützt, ohne dass eine Bereitstellung erforderlich ist.

Bei Bedarf kann Defender für Cloud automatisch einen Log Analytics-Agent bereitstellen, um sicherheitsbezogene Daten zu sammeln. Bei Azure-Computern erfolgt die Bereitstellung direkt. Für Hybrid- und Multi-Cloud-Umgebungen erfolgt die Bereitstellung mithilfe von [Azure Arc](https://azure.microsoft.com/services/azure-arc/).


### <a name="azure-native-protections"></a>Nativer Azure-Schutz

Defender für Cloud unterstützt Sie bei der Erkennung von Bedrohungen für:

- **Azure-PaaS-Dienste**: Erkennen Sie Bedrohungen, die auf Azure-Dienste wie Azure App Service, Azure SQL, Azure Storage-Konten und weitere Datendienste abzielen. Sie können Ihre Azure-Aktivitätsprotokolle auch mithilfe der nativen Integration in Microsoft Defender für Cloud Apps (vormals bekannt als Microsoft Cloud App Security) auf Anomalien untersuchen.

- **Azure-Datendienste**: Defender für Cloud umfasst Funktionen, mit denen Sie Ihre Daten automatisch in Azure SQL klassifizieren können. Sie können auch Bewertungen für potenzielle Sicherheitsrisiken für Azure SQL- und Storage-Dienste sowie Empfehlungen zu ihrer Entschärfung erhalten.

- **Netzwerke**: Mit Defender für Cloud können Sie die Anfälligkeit für Brute-Force-Angriffe verringern. Wenn Sie den Zugriff auf VM-Ports einschränken, indem Sie den Just-In-Time-Zugriff auf VMs nutzen, können Sie die Sicherheit für Ihr Netzwerk erhöhen, weil unnötige Zugriffsvorgänge verhindert werden. Sie können für ausgewählte Ports Richtlinien für den sicheren Zugriff festlegen, damit der Zugriff nur für autorisierte Benutzer, zulässige IP-Quelladressen oder IP-Adressbereiche und einen begrenzten Zeitraum möglich ist. 


### <a name="defend-your-hybrid-resources"></a>Schutz Ihrer Hybridressourcen
Zusätzlich zum Schutz Ihrer Azure-Umgebung können Sie Ihrer Hybrid Cloud-Umgebung Defender für Cloud-Funktionen hinzufügen, um Ihre Nicht-Azure-Server zu schützen. Damit Sie sich auf das Wesentliche konzentrieren können, erhalten Sie maßgeschneiderte Bedrohungsdaten und priorisierte Warnmeldungen basierend auf Ihrer spezifischen Umgebung.

Um den Schutz auf lokale Computer auszuweiten, stellen Sie [Azure Arc](https://azure.microsoft.com/services/azure-arc/) bereit und aktivieren die erweiterten Sicherheitsfunktionen von Defender für Cloud. Weitere Informationen finden Sie unter [Hinzufügen eines Azure-fremden Computers mit Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).


### <a name="defend-resources-running-on-other-clouds"></a>Schutz von Ressourcen, die in anderen Clouds ausgeführt werden 

Defender für Cloud kann Ressourcen in anderen Clouds (z. B. AWS und GCP) schützen. 

Folgende Multi-Cloud-Schutzpläne sind verfügbar:

- **CSPM**: Bereitstellung von Sicherheitsbewertungen und Härtungsempfehlungen für Ihre Ressourcen bei anderen Cloudanbietern.
- **Microsoft Defender für Kubernetes**: Bietet eine optimale Umgebungshärtung sowie Workload- und Runtimeschutz für Ihre Kubernetes-Cluster.
- **Microsoft Defender für Server**: Stattet Ihre Windows- und Linux-Computer mit Bedrohungserkennung und erweiterten Schutzmaßnahmen aus.


## <a name="vulnerability-assessment-and-management"></a>Sicherheitsrisikobewertung und -management

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-assess.png" alt-text="Hervorhebung der Bewertungsfunktionen von Microsoft Defender für Cloud":::

Defender für Cloud umfasst als Bestandteil der erweiterten Sicherheitsfunktionen Lösungen zur Sicherheitsrisikobewertung für Ihre VMs, Containerregistrierungen und SQL Server-Instanzen. Einige der Scanner werden über Qualys betrieben. Sie benötigen weder eine Qualys-Lizenz noch ein Qualys-Konto – die Abwicklung erfolgt nahtlos in Defender für Cloud.

Microsoft Defender für Server umfasst eine automatische, native Integration in Microsoft Defender für Endpunkt. Weitere Informationen erhalten Sie unter [Schützen Sie Ihre Endpunkte mithilfe der in Defender für Cloud integrierten EDR-Lösung: Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md). Wenn diese Integration aktiviert ist, haben Sie Zugriff auf die Sicherheitsrisikobewertung durch das **Bedrohungs- und Sicherheitsrisikomanagement von Microsoft**. Weitere Informationen finden Sie unter [Untersuchen Sie Schwachstellen mit dem Bedrohungs- und Schwachstellenmanagement von Microsoft Defender for Endpoint](deploy-vulnerability-assessment-tvm.md).

Überprüfen Sie die Ergebnisse dieser Sicherheitsrisikoüberprüfungen, und ergreifen Sie direkt aus Defender für Cloud entsprechende Maßnahmen. Mit diesem umfassenden Ansatz wird Defender für Cloud zu einer zentralen Schnittstelle für sämtliche Maßnahmen zum Schutz Ihrer Cloud.

Weitere Informationen finden Sie auf den folgenden Seiten:

- [In Defender für Cloud integrierte Qualys-Überprüfung auf Sicherheitsrisiken für Azure- und Hybridcomputer](deploy-vulnerability-assessment-vm.md)
- [Identifizieren von Sicherheitsrisiken in Images in Azure-Containerregistrierungen](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)


## <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimieren und Verbessern der Sicherheit durch die Konfiguration von empfohlenen Kontrollen

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-secure.png" alt-text="Hervorhebung der Sicherheitsfunktionen von Microsoft Defender für Cloud":::

Es ist eine grundlegende Sicherheitsanforderung, dass Sie Ihre Workloads kennen und für deren Schutz sorgen. Der erste Schritt hierbei ist, maßgeschneiderte Sicherheitsrichtlinien aufzustellen. Da die Richtlinien in Defender für Cloud auf Azure Policy-Kontrollen basieren, können Sie vom vollen Funktionsumfang und der Flexibilität einer **erstklassigen Richtlinienlösung** profitieren. In Defender für Cloud können Sie Ihre Richtlinien so festlegen, dass sie für Verwaltungsgruppen, abonnementübergreifend und sogar für einen gesamten Mandanten ausgeführt werden.

Defender für Cloud erkennt fortlaufend neue Ressourcen, die in Ihren Workloads bereitgestellt werden, und prüft, ob sie in Übereinstimmung mit den Best Practices für die Sicherheit konfiguriert sind. Falls nicht, werden sie markiert, und Sie erhalten eine nach Prioritäten geordnete Liste mit Empfehlungen zur Bereinigung. Mithilfe der Empfehlungen können Sie die Angriffsfläche jeder einzelnen Ressource verringern.

Die Liste der Empfehlungen wird durch den Azure-Sicherheitsvergleichstest aktiviert und unterstützt. Bei diesem Vergleichstest handelt es sich um einen von Microsoft erstellten Satz mit Azure-spezifischen Richtlinien zu Best Practices in Bezug auf Sicherheit und Compliance, die auf allgemeinen Complianceframeworks beruhen. Weitere Informationen finden Sie in der [Einführung zum Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction).

Dadurch können Sie mithilfe von Defender für Cloud nicht lediglich Sicherheitsrichtlinien festlegen, sondern außerdem *Standards für die sichere Konfiguration Ihrer gesamten Ressourcen anwenden*.

:::image type="content" source="./media/defender-for-cloud-introduction/recommendation-example.png" alt-text="Beispiel für eine Defender für Cloud-Empfehlung":::

Damit Sie besser nachvollziehen können, wie wichtig die einzelnen Empfehlungen für den gesamten Sicherheitsstatus sind, werden die Empfehlungen von Defender für Cloud in Sicherheitskontrollen gruppiert, und jedem Sicherheitskontrollelement wird eine **Sicherheitsbewertung** hinzugefügt. Dies ist ein entscheidender Faktor beim **Priorisieren Ihrer Sicherheitsmaßnahmen**.

:::image type="content" source="./media/defender-for-cloud-introduction/sc-secure-score.png" alt-text="Defender für Cloud-Sicherheitsbewertung":::

## <a name="defend-against-threats"></a>Schutz vor Bedrohungen

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-defend.png" alt-text="Hervorhebung der Verteidigungsfunktionen von Microsoft Defender für Cloud":::

Defender für Cloud bietet Folgendes:

- **Sicherheitswarnungen:** Wenn Defender für Cloud in einem der Bereiche Ihrer Umgebung eine Bedrohung erkennt, wird eine Sicherheitswarnung generiert. Diese Warnungen beschreiben Details zu den betroffenen Ressourcen, empfohlene Problembehandlungsschritte sowie in einigen Fällen eine Option, mit der eine Logik-App als Reaktion ausgelöst werden kann. Sie können Warnungen exportieren – unabhängig davon, ob sie von Defender für Cloud generiert oder über ein integriertes Sicherheitsprodukt in Defender für Cloud empfangen wurden. Befolgen Sie die Anweisungen unter [Streamen von Warnungen an eine SIEM-, SOAR- oder IT-Dienstverwaltungslösung](export-to-siem.md), um die Warnungen nach Microsoft Sentinel (oder ein Drittanbieter-SIEM) bzw. ein beliebiges anderes externes Tool zu exportieren. Der Bedrohungsschutz von Defender für Cloud umfasst eine Fusion-Schrittfolgenanalyse, bei der Warnungen in Ihrer Umgebung basierend auf einer Cyber-Kill-Chain-Analyse automatisch korreliert werden. Dadurch können Sie den gesamten Verlauf eines Angriffs, seinen Ausgangspunkt und die Auswirkungen auf Ihre Ressourcen besser nachvollziehen. [Die von Defender für Cloud unterstützten Kill-Chain-Absichten basieren auf Version 7 der MITRE ATT&CK-Matrix.](alerts-reference.md#intentions)

- **Erweiterte Funktionen zum Schutz vor Bedrohungen** für VMs, SQL-Datenbanken, Container, Webanwendungen, Ihr Netzwerk und vieles mehr: Zu den Schutzfunktionen gehören die Sicherung der Verwaltungsports Ihrer VMs mit [Just-in-Time-Zugriff](just-in-time-access-overview.md) und [adaptiver Anwendungssteuerung](adaptive-application-controls.md) zur Erstellung von Positivlisten für Apps, die auf Ihren Rechnern ausgeführt bzw. nicht ausgeführt werden dürfen.

Die Seite **Defender-Pläne** von Microsoft Defender für Cloud stellt die folgenden Pläne für einen umfassenden Schutz der Compute-, Daten- und Dienstebenen Ihrer Umgebung bereit:

- [Microsoft Defender für Server](defender-for-servers-introduction.md)
- [Microsoft Defender für App Service](defender-for-app-service-introduction.md)
- [Microsoft Defender für Speicher](defender-for-storage-introduction.md)
- [Microsoft Defender für SQL](defender-for-sql-introduction.md)
- [Microsoft Defender für Kubernetes](defender-for-kubernetes-introduction.md)
- [Microsoft Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)
- [Microsoft Defender für Key Vault](defender-for-key-vault-introduction.md)
- [Microsoft Defender für Resource Manager](defender-for-resource-manager-introduction.md)
- [Microsoft Defender für DNS](defender-for-dns-introduction.md)
- [Microsoft Defender für relationale Open-Source-Datenbanken](defender-for-databases-introduction.md)

Verwenden Sie die Kacheln für erweiterte Funktionen im [Dashboard für den Workloadschutz](workload-protections-dashboard.md), um die einzelnen Schutzmaßnahmen zu überwachen und zu konfigurieren. 

> [!TIP]
> Azure Defender für IoT (Vorschauversion) ist ein separates Produkt. Alle Details dazu finden Sie in der [Einführung zu Azure Defender für IoT (Vorschauversion)](../defender-for-iot/overview.md). 


## <a name="next-steps"></a>Nächste Schritte

- Zum Einstieg in Defender für Cloud benötigen Sie ein Microsoft Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/) registrieren.

- Der kostenlose Plan von Defender für Cloud wird für alle vorhandenen Azure-Abonnements aktiviert, wenn Sie das Dashboard von Defender für Cloud im Azure-Portal zum ersten Mal aufrufen, oder wenn die Aktivierung programmgesteuert über die REST-API erfolgt. Sie müssen die erweiterten Sicherheitsfeatures aktivieren, um erweiterte Funktionen für die Sicherheitsverwaltung und Bedrohungserkennung nutzen zu können. Diese Features sind in den ersten 30 Tagen kostenlos. [Weitere Informationen erhalten Sie in der Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/).

- Wenn Sie bereit sind, die erweiterten Sicherheitsfunktionen jetzt zu aktivieren, führt Sie der [Schnellstart: Aktivieren der erweiterten Sicherheitsfeatures](enable-enhanced-security.md) durch die einzelnen Schritte.

> [!div class="nextstepaction"]
> [Microsoft Defender-Pläne aktivieren](enable-enhanced-security.md)
