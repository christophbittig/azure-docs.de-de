---
title: Sicherheitswarnungen und -incidents in Azure Security Center
description: Erfahren Sie, wie Azure Security Center Sicherheitswarnungen generiert und diese zu Incidents korreliert.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: f205f66adc2c875e271a85efc2e48763c260dfa0
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237692"
---
# <a name="security-alerts-and-incidents-in-azure-security-center"></a>Sicherheitswarnungen und -incidents in Azure Security Center

Security Center generiert Warnungen für Ressourcen, die in Ihrer Azure-Instanz, lokal und in hybriden Cloudumgebungen bereitgestellt wurden.

Sicherheitswarnungen werden durch erweiterte Erkennungen ausgelöst und sind nur in Azure Defender verfügbar. Sie können auf der Seite **Preise und Einstellungen** ein Upgrade durchführen, wie unter [Schnellstart: Aktivieren von Azure Defender](enable-azure-defender.md) beschrieben. Sie können eine kostenlose 30-Tage-Testversion nutzen. Preisdetails zu Ihrer bevorzugten Währung und für Ihre Region finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>Was sind Sicherheitswarnungen und Sicherheitsincidents? 

**Warnungen** sind die Benachrichtigungen, die Security Center generiert, wenn Bedrohungen für Ihre Ressourcen erkannt werden. Security Center priorisiert die Warnungen und listet sie zusammen mit den Informationen auf, die erforderlich sind, um das Problem schnell zu untersuchen. Security Center stellt auch detaillierte Schritte bereit, die Sie bei der Abwehr von Angriffen unterstützen. Warnungsdaten werden 90 Tage lang aufbewahrt.

Ein **Sicherheitsincident** ist eine Sammlung verwandter Warnungen (anstelle einer Auflistung der einzelnen Warnungen). In Security Center werden verschiedene Warnungen und Signale mit geringer Genauigkeit mittels [Korrelation von intelligenten Cloudwarnungen](#cloud-smart-alert-correlation-in-azure-security-center-incidents) zu Sicherheitsincidents korreliert.

Dank Incidents bietet Ihnen Security Center eine zentrale Ansicht für einen Angriffsversuch und alle zugehörigen Warnungen. In dieser Ansicht können Sie schnell nachvollziehen, welche Aktionen der Angreifer ausgeführt hat und welche Ressourcen betroffen sind.

## <a name="respond-to-todays-threats"></a>Reagieren auf heutige Bedrohungen 

In den letzten 20 Jahren hat sich die Bedrohungslandschaft stark verändert. In der Vergangenheit mussten sich Unternehmen in der Regel nur Sorgen um eine mögliche Verunstaltung Ihrer Websites durch einzelne Angreifer machen, die häufig nur ihre Fähigkeiten austesten wollten. Die Angreifer von heute sind dagegen viel besser vorbereitet und ausgerüstet. Sie verfolgen häufig bestimmte finanzielle oder strategische Ziele. Außerdem stehen ihnen mehr Ressourcen zur Verfügung, weil sie möglicherweise von Staaten oder der organisierten Kriminalität finanziert werden.

Diese Veränderungen haben dazu geführt, dass Angreifer heutzutage sehr professionell vorgehen können. Sie sind nicht mehr daran interessiert, nur Websites zu verunstalten. Es geht vielmehr um das Stehlen von Informationen, Finanzkonten und privaten Daten. Diese Daten werden dann auf dem freien Markt zu Geld gemacht oder für bestimmte geschäftliche, politische oder militärische Zwecke genutzt. Noch gefährlicher als Angreifer mit finanziellen Motiven sind Angreifer, die in Netzwerke eindringen, um die Infrastruktur oder Personen zu schädigen.

Als Reaktion stellen Organisationen häufig verschiedene Punktlösungen bereit, die nach bekannten Angriffssignaturen suchen, um die Grenzen oder Endpunkte des Unternehmens abzusichern. Diese Lösungen neigen dazu, eine große Menge von so genannten Low-Fidelity-Warnungen zu generieren, die von einem Sicherheitsexperten selektiert und untersucht werden müssen. Die meisten Organisationen verfügen nicht über die Zeit und das Wissen, um auf diese Warnungen zu reagieren, sodass diese häufig nicht untersucht werden können.  

Darüber hinaus haben die Angreifer ihre Methoden weiterentwickelt und können viele signaturbasierte Verteidigungen umgehen und sich [an Cloudumgebungen anpassen](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Neue Ansätze sind erforderlich, um neue Bedrohungen schnell identifizieren und die Erkennung und Reaktion beschleunigen zu können.


## <a name="continuous-monitoring-and-assessments"></a>Kontinuierliche Überwachung und Bewertungen

Azure Security Center profitiert von Sicherheitsforschungs- und Data Science-Teams bei Microsoft, die ständig Änderungen der Bedrohungslandschaft überwachen. Dies umfasst Folgendes:

* **Threat Intelligence-Überwachung**: Informationen zu Bedrohungen (Threat Intelligence) umfassen Mechanismen, Indikatoren, Auswirkungen und nützliche Hinweise zu vorhandenen oder neuen Bedrohungen. Diese Informationen werden in der Sicherheitscommunity bereitgestellt, und Microsoft überwacht fortlaufend Threat Intelligence-Feeds von internen und externen Quellen.
* **Signalaustausch**: Die Erkenntnisse der Sicherheitsteams aus dem großen Microsoft-Portfolio mit Clouddiensten und lokalen Diensten, Servern und Clientendpunkt-Geräten werden ausgetauscht und analysiert.
* **Microsoft-Sicherheitsexperten**: Ständiger Austausch mit Teams von Microsoft, die sich mit speziellen Sicherheitsfeldern beschäftigen, z.B. Forensik und Erkennung von Webangriffen.
* **Erkennungsoptimierung**: Algorithmen werden für echte Kundendatasets ausgeführt, und Sicherheitsexperten arbeiten mit Kunden zusammen, um die Ergebnisse auszuwerten. Richtige und falsche Positivmeldungen werden verwendet, um Machine Learning-Algorithmen zu verfeinern.

Diese kombinierten Verfahren führen zu neuen und verbesserten Erkennungsergebnissen, von denen Sie sofort profitieren können. Sie müssen dabei nichts unternehmen.

## <a name="how-does-security-center-detect-threats"></a>Wie funktioniert die Bedrohungserkennung von Security Center? <a name="detect-threats"> </a>

Microsoft-Sicherheitsexperten suchen ständig nach neuen Bedrohungen. Aufgrund der globalen Präsenz in der Cloud und in lokalen Umgebungen haben wir Zugriff auf umfassende Telemetriedaten. Mithilfe dieser weit reichenden und verschiedenartigen Sammlung von Datasets können wir neue Angriffsmuster und Trends für unsere lokalen Privatkunden- und Unternehmensprodukte sowie für unsere Onlinedienste erkennen. Auf diese Weise können die Erkennungsalgorithmen von Security Center schnell aktualisiert werden, wenn Angreifer neue und immer anspruchsvollere Exploit-Verfahren nutzen. So können Sie mit der rasanten Entwicklung von Bedrohungen Schritt halten.

Security Center sammelt, analysiert und integriert Protokolldaten Ihrer Azure-Ressourcen und aus dem Netzwerk, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Dies funktioniert auch mit verbundenen Partnerlösungen wie Firewalls und Lösungen zum Schutz von Endpunkten. Security Center analysiert diese Informationen und korreliert sie häufig mit Informationen aus mehreren Quellen, um Bedrohungen zu identifizieren.

![Security Center – Datensammlung und -darstellung](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Für Security Center werden professionelle Sicherheitsanalysen genutzt, die weit über signaturbasierte Ansätze hinausgehen. Bahnbrechende Weiterentwicklungen der Big Data- und [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)-Technologie werden genutzt, um Ereignisse im gesamten Cloudfabric auszuwerten. So können Bedrohungen erkannt werden, die bei Verwendung von manuellen Konzepten nicht identifiziert werden können, und die Entwicklung von Angriffen kann vorhergesagt werden. Zu diesen Sicherheitsanalysen gehört Folgendes:

- **Integrierte Informationen zu Bedrohungen:** Microsoft verfügt über eine große Menge von Informationen zu globalen Bedrohungen. Die Telemetriedaten stammen aus mehreren Quellen, z. B. Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) und Microsoft Security Response Center (MSRC). Sicherheitsexperten erhalten außerdem Informationen zu Bedrohungen, die zwischen großen Cloudanbietern ausgetauscht werden, und Feeds von Drittanbietern. Azure Security Center kann diese Informationen verwenden, um Sie vor Bedrohungen durch bekannte Angreifer zu warnen.

- **Verhaltensanalyse:** Die Verhaltensanalyse ist ein Verfahren, bei dem Daten mit einer Sammlung bekannter Muster analysiert und verglichen werden. Bei diesen Mustern handelt es sich aber nicht nur um einfache Signaturen. Sie werden anhand von komplexen Machine Learning-Algorithmen bestimmt, die auf große Datasets angewendet werden. Außerdem werden sie anhand einer sorgfältigen Analyse von schädlichem Verhalten durch erfahrene Analysten bestimmt. Azure Security Center kann die Verhaltensanalyse verwenden, um kompromittierte Ressourcen basierend auf der Analyse der Protokolle von virtuellen Computern und virtuellen Netzwerkgeräten, Fabric-Protokolle und von anderen Quellen zu identifizieren.

- **Anomalieerkennung:** Azure Security Center nutzt auch die Anomalieerkennung, um Bedrohungen zu identifizieren. Im Gegensatz zur Verhaltensanalyse (die auf bekannten Mustern basiert, die aus großen Datasets abgeleitet werden) ist die Anomalieerkennung „personalisierter“ und nutzt Baselines, die speziell für Ihre Bereitstellungen gelten. Machine Learning wird angewendet, um die normale Aktivität für Ihre Bereitstellungen zu ermitteln. Anschließend werden Regeln generiert, um Ausreißerbedingungen zu definieren, die ein Sicherheitsereignis darstellen können.

## <a name="how-are-alerts-classified"></a>Wie werden Warnungen klassifiziert?

Security Center weist Warnungen einen Schweregrad zu, um Ihnen zu helfen, die Reihenfolge zu priorisieren, in der Sie sich um Warnungen kümmern. So können Sie, sobald eine Ressource gefährdet ist, sofort zu ihr gelangen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

> [!NOTE]
> Der Schweregrad der Warnung wird im Portal und in Versionen der REST-API vor dem 1.1.2019 unterschiedlich angezeigt. Wenn Sie eine ältere Version der API verwenden, führen Sie ein Upgrade aus, um die unten beschriebene konsistente Erfahrung zu erhalten.

| severity          | Empfohlene Antwort                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Hoch**          | Ihre Ressource wurde mit hoher Wahrscheinlichkeit kompromittiert. Sie sollten dies sofort überprüfen. Von Security Center werden sowohl die böswillige Absicht als auch die ermittelten Ergebnisse zur Ausgabe der Warnung als hoch eingestuft. Ein Beispiel hierfür ist eine Warnung, bei der die Ausführung eines bekannten schädlichen Tools wie Mimikatz erkannt wird, das häufig für den Diebstahl von Anmeldeinformationen verwendet wird.                                                                                                           |
| **Mittel**        | Hiermit wird eine potenziell verdächtige Aktivität angegeben, die auf die Kompromittierung einer Ressource hinweisen kann. Security Center stuft die Analyse oder die ermittelten Ergebnisse als „Mittel“ und die schädliche Absicht als „Mittel“ bis „Hoch“ ein. Hierbei handelt es sich normalerweise um Erkennungen, die auf maschinellem Lernen oder Anomalien basieren. Ein Beispiel hierfür ist ein Anmeldeversuch, der von einem ungewöhnlichen Standort aus durchgeführt wird.                                                                                                            |
| **Niedrig**           | Hierbei kann es sich um ein unschädliches positives Ergebnis oder um einen blockierten Angriff handeln. Security Center kann die Absicht nicht mit Sicherheit als schädlich einstufen, und die Aktivität ist möglicherweise harmlos. Das Löschen eines Protokolls ist beispielsweise eine Aktion, die von einem Angreifer ausgeführt werden kann, um Spuren zu verwischen. Aber in vielen Fällen handelt es sich um einen von Administratoren ausgeführten Routinevorgang. Security Center teilt Ihnen normalerweise nicht mit, wenn Angriffe blockiert wurden. Eine Ausnahme sind interessante Fälle, bei denen wir Ihnen raten, dass Sie sich diese ansehen. |
| **Informational** (Nur zu Informationszwecken) | Ein Incident besteht in der Regel aus einer Reihe von Warnungen, von denen einige für sich allein genommen nur informativen Charakter haben können, aber im Zusammenhang mit den anderen Warnungen durchaus eine genauere Untersuchung verdienen.                                                                                                                                                                                                                                                               |
|                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

## <a name="export-alerts"></a>Exportieren von Warnungen

Sie haben mehrere Optionen zum Anzeigen der Warnungen außerhalb von Security Center:

- Der **Download des CSV-Berichts** auf dem Dashboard „Warnungen“ bietet einen einmaligen Export in CSV.
- Mit dem **fortlaufenden Export** von der Seite „Preise und Einstellungen“ können Sie Streams von Sicherheitswarnungen und -empfehlungen im Log Analytics-Arbeitsbereich und in Event Hubs konfigurieren. [Weitere Informationen zum fortlaufenden Export](continuous-export.md).
- Der **Azure Sentinel-Connector** streamt Sicherheitswarnungen von Azure Security Center in Azure Sentinel. [Weitere Informationen zum Verbinden von Azure Security Center mit Azure Sentinel](../sentinel/connect-azure-security-center.md).

Erfahren Sie mehr über alle Exportoptionen unter [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Dienstverwaltungslösung](export-to-siem.md) und [Fortlaufender Export von Security Center-Daten](continuous-export.md).

## <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korrelation von intelligenten Cloudwarnungen in Azure Security Center (Incidents)

Azure Security Center analysiert kontinuierlich die Hybrid Cloud-Workloads unter Verwendung von erweiterten Analysen und Threat Intelligence, um Sie auf schädliche Aktivitäten aufmerksam zu machen.

Die Bedrohungsabdeckung wird ständig erweitert. Es ist wichtig, selbst kleinste Kompromittierungen zu erkennen, und für Sicherheitsanalysten kann es schwierig sein, die verschiedenen Warnungen zu sichten und einen tatsächlichen Angriff zu erkennen. Security Center unterstützt Analysten bei der Bewältigung dieser Warnungen. Die Lösung ermöglicht eine zeitnahe Angriffsdiagnose und fasst zu diesem Zweck verschiedene Warnungen und Signale mit geringer Genauigkeit zu Sicherheitsincidents zusammen.

Fusion-Analysen sind die Technologie und das Analyse-Back-End, die Security Center-Incidents zugrunde liegen, und ermöglichen die Korrelation verschiedener Warnungen und kontextbezogener Signale. Fusion analysiert ressourcenübergreifend die verschiedenen, für ein Abonnement gemeldeten Signale. Fusion ermittelt Muster, die Aufschluss über den Status eines Angriffs geben, oder Signale mit gemeinsamen kontextbezogen Informationen, die darauf hindeuten, dass eine einheitliche Gegenmaßnahme ergriffen werden sollte.

Bei Fusion-Analysen werden Sicherheitsdomänenkenntnisse mit künstlicher Intelligenz kombiniert, um Warnungen zu analysieren und neue Angriffsmuster zu erkennen, sobald diese auftreten. 

Security Center nutzt die Angriffsmatrix von Mitre, um einen Zusammenhang zwischen Warnungen und deren vermeintlicher Absicht herzustellen und so zur Formalisierung von Sicherheitsdomänenkenntnissen beizutragen. Auf der Grundlage der Informationen, die für die einzelnen Schritte eines Angriffs gesammelt wurden, kann Security Center außerdem Aktivitäten ausschließen, die lediglich wie Angriffsschritte aussehen, aber gar keine sind.

Da Angriffe häufig mandantenübergreifend auftreten, kann Security Center KI-Algorithmen für die Analyse von Angriffssequenzen kombinieren, die für die einzelnen Abonnements gemeldet werden. Dadurch lässt sich erkennen, ob es sich um verbreitete Warnungsmuster handelt (und nicht nur um Warnungen, die zufällig zusammenhängen).

Bei der Untersuchung eines Incidents benötigen Analysten häufig zusätzlichen Kontext, um die Art der Bedrohung und mögliche Gegenmaßnahmen einschätzen zu können. Selbst wenn also beispielsweise eine Netzwerkanomalie erkannt wurde, ist es schwer, angemessen darauf zu reagieren, wenn die weiteren Vorgänge in Bezug auf das Netzwerk oder die betroffenen Ressourcen nicht klar sind. Ein Sicherheitsincident kann hier mit Artefakten, verwandten Ereignissen und Informationen weiterhelfen. Welche zusätzlichen Informationen für Sicherheitsincidents verfügbar sind, hängt von der Art der erkannten Bedrohung und der Konfiguration Ihrer Umgebung ab. 

> [!TIP]
> Eine Liste der Warnungen für Sicherheitsvorfälle, die von Fusion-Analysen erzeugt werden, finden Sie in der [Verweistabelle zu Warnungen](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Screenshot: Bericht zu erkanntem Sicherheitsvorfall":::

Informationen zum Verwalten Ihrer Sicherheitsincidents finden Sie unter [Verwalten von Sicherheitsincidents in Azure Security Center](security-center-incident.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die unterschiedlichen Arten von Sicherheitswarnungen in Security Center beschrieben. Weitere Informationen finden Sie unter

- [Sicherheitswarnungen im Azure-Aktivitätsprotokoll](https://go.microsoft.com/fwlink/?linkid=2114113): Sicherheitswarnungen und -incidents sind nicht nur im Azure-Portal oder programmgesteuert verfügbar, sondern werden auch im Azure-Aktivitätsprotokoll als Ereignisse überwacht.
- [Referenztabelle der Azure Defender-Warnungen](alerts-reference.md)
- [Reagieren auf Sicherheitswarnungen](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)