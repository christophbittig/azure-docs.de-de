---
title: Überblick über Sicherheit in Azure Australien
description: Die am häufigsten gefragten Informationen zu den australischen Regionen und den spezifischen Anforderungen zum Erfüllen der Richtlinien, Bestimmungen und Gesetze der australischen Regierung.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: ccc71707fa15fd1b09fcdc46f887d9ca343a3d04
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "122345755"
---
# <a name="azure-australia-security-explained"></a>Überblick über Sicherheit in Azure Australien

In diesem Artikel werden einige der häufig gestellten Fragen und Interessensbereiche für australische Regierungsbehörden erläutert, die Microsoft Azure in Australien untersuchen, entwerfen und bereitstellen.

## <a name="irap-and-certified-cloud-services-list-documents"></a>Dokumente des IRAP (Information Security Registered Assessors Program) und der Liste der zertifizierten Clouddienste (Certified Cloud Services List, CCSL)

Das Australian Cyber Security Centre (ACSC) stellt ein Zertifizierungsschreiben, einen Zertifizierungsbericht und einen Verbraucherleitfaden für den Dienst bereit, wenn er der CCSL hinzugefügt wird.

Microsoft ist zurzeit in der CCSL für Azure, Office 365 und Dynamics 365 CRM aufgeführt.

Microsoft macht seine Überwachungs-, Bewertungs- und ACSC-Zertifizierungsdokumente Verbrauchern und Partnern auf einer spezifischen Australien-Seite des [Microsoft Service Trust Portal](https://aka.ms/au-irap) verfügbar.

## <a name="dissemination-limiting-markers-and-protected-certification"></a>Dissemination Limiting Markers (DLM) und PROTECTED-Zertifizierung

Der Prozess, Systeme, z. B. Clouddienste, für die Verwendung durch Regierungsorganisationen zu genehmigen, wird im [Information Security Manual (ISM)](https://www.cyber.gov.au/acsc/view-all-content/ism) definiert, das vom Australian Cyber Security Centre (ACSC) erstellt und veröffentlicht wird. Das ACSC ist die Stelle innerhalb des Australian Signals Directorate (ASD), die für Cybersicherheit und Cloudzertifizierung zuständig ist.

Der Genehmigungsprozess umfasst zwei Schritte:

1. **Sicherheitsbewertung (IRAP)** : Ein Prozess, mit dem registrierte Fachleute Systeme, Dienste und Lösungen anhand der technischen Kontrollen im ISM bewerten und auswerten, ob die Kontrollen effektiv implementiert wurden. Außerdem werden durch die Bewertung alle spezifischen Risiken identifiziert, die die Genehmigungsbehörde vor der Ausstellung einer Betriebsgenehmigung (Approval to Operate, ATO) berücksichtigen muss.

1. **Betriebsgenehmigung (Approval to Operate, ATO)** : Der Prozess, bei dem ein leitender Mitarbeiter einer Regierungsbehörde das Restrisiko eines Systems für die von diesem verarbeiteten, gespeicherten und übertragenen Daten formal anerkennt und akzeptiert. Eine Eingabe für diesen Prozess ist die Sicherheitsbewertung.

Die Bewertungsstufe PROTECTED für die Azure-Dienste gibt an, dass das Vorhandensein und die Funktionalität der Implementierung der Sicherheitskontrollen bestätigt wurde, die für die Speicherung und Verarbeitung von als PROTECTED und niedriger eingestuften Daten erforderlich sind.

## <a name="australian-data-classification-changes"></a>Änderungen an der australischen Datenklassifizierung

Am 1. Oktober 2018 hat die australische Generalstaatsanwaltschaft Änderungen am Protective Security Policy Framework (PSPF) angekündigt, insbesondere ein neues [sensibles und klassifiziertes Informationssystem](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Überarbeitete PSPF-Klassifizierungen](media/pspf-classifications.png)

Alle australischen Behörden und Organisationen, die unter dem PSPF arbeiten, sind von diesen Änderungen betroffen. Die wichtigste Änderung, die sich auf unsere aktuellen IRAP/CCSL-Zertifizierungen auswirkt, besteht darin, dass die aktuellen Dissemination Limiting Markers (DLM) eingestellt wurden. Die Markierung „OFFICIAL:Sensitive“ ersetzt die verschiedenen DLMs, die für den Schutz vertraulicher Informationen verwendet werden. Die Änderung hat auch drei Markierungen zur Verwaltung von Informationen eingeführt, die auf alle offiziellen Informationen in allen Vertraulichkeits- und Klassifizierungsstufen angewendet werden können. Die Klassifizierung PROTECTED bleibt unverändert.

Der Begriff „Unclassified“ wird aus dem neuen System entfernt, und der Begriff „Unofficial“ wird auf Nicht-Regierungsinformationen angewendet, obwohl diese keine formale Markierung erfordern.

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>Auswählen einer Azure-Region für als „OFFICIAL:Sensitive“ und „PROTECTED“ eingestufte Workloads

Die für „OFFICIAL:Sensitive“ und „PROTECTED“ zertifizierten Azure-Dienste werden in allen vier australischen Rechenzentrumsregionen bereitgestellt: „Australien, Osten“, „Australien, Südosten“, „Australien, Mitte“ und „Australien, Mitte 2“. Der vom ACSC ausgegebene Zertifizierungsbericht empfiehlt, dass PROTECTED Daten in den Azure Government-Regionen in Canberra bereitgestellt werden, wenn dort ein Dienst verfügbar ist. Weitere Informationen zu den als PROTECTED zertifizierten Azure-Diensten finden Sie auf der [Australien-Seite im Service Trust Portal (STP)](https://aka.ms/au-irap).

>[!NOTE]
>Microsoft empfiehlt, Daten von Regierungsbehörden aller Vertraulichkeitsstufen und Klassifizierungen in den Regionen „Australien, Mitte“ und „Australien, Mitte 2“ bereitzustellen, da sie speziell für die Bedürfnisse von Regierungsbehörden entworfen wurden und gemäß diesen betrieben werden.

Weitere Informationen zur besonderen Natur der Azure Australien-Regionen finden Sie unter [Azure-Regionen in „Australien, Mitte“](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Wie Microsoft klassifizierte und offizielle Daten trennt

Microsoft führt Azure und Office 365 in Australien so aus, als ob alle Daten vertraulich oder klassifiziert wären, wodurch die Sicherheitskontrollen auf diese hohe Stufe angehoben werden.

Die Infrastruktur, die Azure unterstützt, bedient potenziell Daten mehrerer Klassifizierungen. Da wir davon ausgehen, dass die Kundendaten klassifiziert sind, stehen die entsprechenden Kontrollen bereit. Microsoft hat für seine Dienste einen Baseline-Sicherheitsstatus implementiert, der den PSPF-Anforderungen zum Speichern und Verarbeiten von als PROTECTED eingestuften Informationen entspricht.

Um unseren Kunden gegenüber zu gewährleisten, dass ein Mandant in Azure nicht durch andere Mandanten gefährdet ist, implementiert Microsoft umfassende Defense-in-Depth-Kontrollen.

Neben den in der Microsoft Azure-Plattform implementierten Funktionen können zusätzliche, vom Kunden konfigurierbare Kontrollen, wie z.B. Verschlüsselung mit vom Kunden verwalteten Schlüsseln, geschachtelte Virtualisierung und Just-in-Time-Administratorzugriff, das Risiko weiter verringern. Innerhalb der australischen Azure Government-Regionen in Canberra ist ein Prozess für eine formale Genehmigung nur für australische und neuseeländische Regierungsbehörden und wichtige nationale Organisationen im Infrastrukturbereich vorhanden. Diese Communitycloud bietet Organisationen, die für potenzielle Co-Mandanten-Risiken anfällig sind, zusätzliche Sicherheit.

Der Microsoft Azure-Bericht für PROTECTED-Zertifizierung bestätigt, dass diese Kontrollen für die Speicherung und Verarbeitung von als PROTECTED klassifizierten Daten und deren Isolation wirksam sind.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevanz von IRAP/CCSL für Landesbehörden und wichtige Infrastrukturanbieter

Viele Landesbehörden und wichtigen Infrastrukturanbieter integrieren die Anforderungen der Bundesbehörden in ihre Sicherheitsrichtlinien und ihr Sicherungsframework. Diese Organisationen verarbeiten ebenfalls als OFFICIAL, „OFFICIAL:Sensitive“ und in gewissem Umfang als PROTECTED klassifizierte Daten, entweder im Rahmen ihrer Interaktion mit der Bundesbehörde oder in Bezug auf eigene Daten.

Die australische Regierung konzentriert sich immer stärker auf Richtlinien und Gesetze zum Schutz von Nicht-Regierungsdaten, die sich fundamental auf die Sicherheit und den wirtschaftlichen Wohlstand von Australien auswirken. Als solche sind die Azure Australien-Regionen und die CCSL-Zertifizierung für alle diese Branchen relevant.

![Kritische Infrastruktursektoren](media/nci-sectors.png)

Die Microsoft-Zertifizierungen veranschaulichen, dass Azure-Dienste einer umfassenden, strengen und formalen Bewertung der implementierten Sicherheitsmaßnahmen unterzogen und für die Verarbeitung solch hochsensibler Daten zugelassen wurden.

## <a name="location-and-control-of-microsoft-data-centres"></a>Speicherort und Kontrolle von Microsoft-Rechenzentren

Es ist eine obligatorische Voraussetzung für Regierungsbehörden und kritische Infrastrukturen, den Speicherort des Rechenzentrums und den Besitz der Clouddienste, die die Daten verarbeiten, explizit zu kennen. Microsoft ist als Hyperscale-Cloudanbieter einzigartig in der Bereitstellung umfassender Informationen zu Speicherorten und Besitzern.

Die Azure Australien-Regionen („Australien, Mitte“ und „Australien, Mitte 2“) werden innerhalb der Räumlichkeiten der CDC-Rechenzentren betrieben. Die CDC-Rechenzentren werden von Australien kontrolliert und sind zu 48 % im Besitz von Commonwealth Superannuation Corporation, zu 48 % im Besitz von Infratil (einem sowohl an der australischen als auch neuseeländischen Börse gelisteten Investmentunternehmen mit Sitz in Neuseeland, mit langfristigen Investitionen in Unternehmungen der Infrastrukturbranche) und zu 4 % in Verwaltung durch Australien. 

Für die Verwaltung der CDC-Rechenzentren bestehen vertragliche Zusicherungen, und die australische Regierung hat eine zukünftige Übertragung von Besitz und Kontrolle eingeschränkt. Diese Transparenz der Lieferkette und des Besitzes über die Microsoft-Partnerschaft mit CDC-Rechenzentren steht in Einklang mit den Prinzipien der [Whole-of-Government Hosting Strategy](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) und der Definition eines zertifizierten souveränen Rechenzentrums.

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>In der aktuellen CCSL-Zertifizierung enthaltene Azure-Dienste

Im Juni 2017 hat das ACSC 41 Azure-Dienste für die Speicherung und Verarbeitung von Daten der Stufe „Unclassified:DLM“ zertifiziert. Im April 2018 wurden 24 dieser Dienste für als PROTECTED klassifizierte Daten zertifiziert.

Die Verfügbarkeit von ACSC-zertifizierten Azure-Diensten in den Azure-Regionen in Australien sieht wie folgt aus (fett formatiert angezeigte Dienste sind auf PROTECTED-Ebene zertifiziert).

|Azure-Regionen in „Australien, Mitte“|Nicht regionale Dienste und andere Regionen|
|---|---|
|API Management, App Gateway, Anwendungsdienste, **Automation**, **Azure portal**, **Backup**, **Batch**, **Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, Log Analytics, **Multi-factor Authentication**, Redis Cache, **Resource Manager**, **Service Bus**, **Service Fabric**, **Site Recovery**, **SQL Database**, **Storage**, Traffic Manager, **Virtual Machines**, **Virtual Network**, **VPN Gateway**|**Azure Active Directory**, CDN, Data Catalog, **Import Export**, **Information Protection**, **IOT Hub**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **Scheduler**, **Security Centre**, Search, Stream Analytics|
|

Microsoft veröffentlicht die [Übersicht über die Compliance von Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf), in der alle zum Umfang gehörigen Dienste für alle globalen, behördlichen, branchenspezifischen und regionalen Compliance- und Bewertungsprozesse aufgeführt sind, die Azure durchläuft, einschließlich IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>Azure-Dienst ist nicht aufgeführt oder für eine niedrigere Stufe bewertet, als benötigt

Dienste, die nicht zertifiziert sind oder auf der Ebene „OFFICIAL:Sensitive“, aber nicht PROTECTED zertifiziert sind, können zusammen mit oder als Teil einer Lösung verwendet werden, die als PROTECTED eingestufte Daten hostet, vorausgesetzt für die Dienste gilt Folgendes:

- Als PROTECTED eingestufte Daten werden nicht unverschlüsselt gespeichert oder verarbeitet. Oder:
- Sie haben eine Risikobewertung durchgeführt, und den Dienst zum Speichern von als PROTECTED eingestuften Daten selbst genehmigt.

Sie können einen Dienst, der nicht in der CCSL enthalten ist, verwenden, um als OFFICIAL eingestufte Daten zu speichern und zu verarbeiten, können Sie dies tun, aber das ISM erfordert, dass Sie das ACSC schriftlich darüber benachrichtigen, bevor Sie einen Vertrag mit einem Clouddienstanbieter eingeben oder verlängern.

Für alle von einer Behörde für PROTECTED-Workloads verwendete Dienste muss die Sicherheit gemäß den im ISM beschriebenen Prozessen und dem von der Behörde verwalteten IRAP-Bewertungsprozess in der [DTA Secure Cloud Strategy](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf) bewertet und genehmigt werden.

![DTA Secure Cloud Strategy-Zertifizierungsprozess](media/certification.png)

Microsoft führt kontinuierlich Bewertungen seiner Dienste durch, um sicherzustellen, dass die Plattform sicher ist und für die Verwendung durch australische Regierungsbehörden geeignet ist. Wenden Sie sich daher an Microsoft, wenn Sie Unterstützung bei einem Dienst benötigen, der derzeit in der CCSL nicht auf der Stufe PROTECTED ist.

Da Microsoft über eine Reihe von Diensten verfügt, die in der CCSL sowohl für die Klassifizierungen „Unclassified DLM“ als auch PROTECTED zertifiziert sind, ist es nach dem ISM erforderlich, mindestens alle zwei Jahre eine IRAP-Bewertung der Dienste durchzuführen. Microsoft nimmt eine jährliche Bewertung vor, die auch die Möglichkeit bietet, zusätzliche Dienste zu einzubeziehen.

## <a name="certified-protected-gateway-in-azure"></a>Zertifiziertes PROTECTED-Gateway in Azure

Aufgrund von Einschränkungen hinsichtlich der Anzahl von SIGs, die im Rahmen des Gateway Consolidation-Programms zulässig sind, betreibt Microsoft kein für Regierungsorganisationen zertifiziertes Secure Internet Gateway (SIG). Die erwarteten und notwendigen Funktionen eines SIG können jedoch in Microsoft Azure konfiguriert werden.

Durch die PROTECTED-Zertifizierung von Azure-Diensten gibt das ACSC bestimmte Empfehlungen für Behörden zum Herstellen einer Verbindung mit Azure und zur Implementierung der Netzwerksegmentierung zwischen Sicherheitsdomänen, z.B. zwischen PROTECTED und dem Internet. Diese Empfehlungen umfassen die Verwendung von Netzwerksicherheitsgruppen, Firewalls und virtuellen privaten Netzwerken. Das ACSC empfiehlt die Verwendung einer virtuellen Gatewayappliance. In Azure sind mehrere virtuelle Appliances verfügbar, für die es in der Liste der vom ASD ausgewerteten Produkte eine physische Entsprechung gibt oder die nach dem Common Criteria-Schutzprofil ausgewertet wurden und im Common Criteria-Portal aufgeführt sind. Diese Produkte werden vom ASD als Unterzeichner des Common Criteria Recognition Arrangement (CCRA) anerkannt.

Microsoft hat eine Anleitung zur Implementierung von Azure-basierten Funktionen erstellt, die die zum Schutz der Grenze zwischen verschiedenen Sicherheitsdomänen erforderlichen Sicherheitsfunktionen bereitstellen, die in Kombination das Gegenstück zu einem zertifizierten SIG darstellen. Eine Reihe von Partnern kann Sie bei Entwurf und Implementierung dieser Funktionen unterstütze, und eine Reihe von verfügbaren Partnerlösungen ist zum gleichen Zweck verfügbar.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Sicherheitsfreigaben und die Staatsbürgerschaft von Microsoft-Supportmitarbeitern

Microsoft betreibt seine Dienste weltweit mit geprüftem und geschultem Sicherheitspersonal. Mitarbeiter, die unbegleiteten physischen Zugriff auf Einrichtungen in Sydney und Melbourne haben, verfügen über Baseline-Sicherheitsfreigaben der australischen Regierung. Mitarbeiter in den Regionen „Australien, Mitte“ und „Australien, Mitte 2“ verfügen mindestens über Negative Vetting 1 (NV1)-Freigaben (geeignet für Daten, die als SECRET eingestuft sind). Diese Freigabeanforderungen bieten Kunden die zusätzliche Sicherheit, dass die Mitarbeiter in Rechenzentren, die Azure betreiben, eine hohe Vertrauenswürdigkeit aufweisen.

Microsoft verfügt über eine Zugriffsrichtlinie ohne feste Berechtigungen, bei der der Zugriff durch ein System aus Just-in-Time- und Just-Enough-Administration basierend auf rollenbasierten Zugriffssteuerungen von Azure (Azure RBAC) gewährt wird. In den meisten Fällen benötigen unsere Administratoren keinen Zugriff auf und keine Berechtigungen für Kundendaten, um Probleme zu beheben und den Dienst zu verwalten. Durch ein hohes Maß an Automatisierung und Skripting bei Aufgaben für die Remoteausführung ist kein direkter Zugriff auf Kundendaten erforderlich.

Die Generalstaatsanwaltschaft hat bestätigt, dass die Personalsicherheitsrichtlinien und -verfahren von Microsoft innerhalb von Azure die Absicht der „PSPF Access to Information“-Bestimmungen in INFOSEC-9 erfüllen.

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Speichern von ITAR-Daten (International Traffic in Arms, US-amerikanische Vorschriften für den internationalen Waffenhandel) oder EAR-Daten (US Export Administration Regulations, Ausführungsverordnung des amerikanischen Exportkontrollrechts)

Die technischen Kontrollen in Azure, die Kunden bei der Erfüllung ihrer Verpflichtungen in Bezug auf Daten, die einer Ausfuhrkontrolle unterliegen, unterstützen, sind in Azure weltweit gleich. Wichtig ist, dass es kein formales Bewertungs- und Zertifizierungsframework für Daten gibt, die Ausfuhrkontrollen unterliegen.

Für Azure Government und Office 365 US Government for Defense haben wir zusätzliche Vertrags- und Prozessmeasures eingerichtet, um Kunden zu unterstützen, die Ausfuhrkontrollen unterliegen. Diese zusätzlichen Vertragsklauseln und die garantierte US-amerikanische Unterstützung und Verwaltung der Azure-Regionen sind für Australien nicht vorhanden.

Das bedeutet nicht, dass Azure in Australien nicht für ITAR/EAR verwendet werden kann, aber Sie müssen die Einschränkungen, denen Sie durch Ihre Exportlizenz unterliegen, klar verstehen. Sie müssen auch zusätzliche Schutzmaßnahmen implementieren, um diese Verpflichtungen zu erfüllen, bevor Sie Azure zum Speichern dieser Daten verwenden. Beispielsweise müssen Sie eventuell:

- Nationalität als Attribut in Azure Active Directory integrieren.
- Azure Information Protection verwenden, um Verschlüsselungsregeln für die Daten zu erzwingen und diese auf ausschließlich die USA zu beschränken sowie alle anderen Nationalitäten, die in der Exportlizenz enthalten sind.
- Alle lokalen Daten verschlüsseln, bevor Sie sie in Azure speichern, indem Sie einen Kundenschlüssel oder „Eigenen Schlüssel speichern“ (Hold Your Own Key) für ITAR-Daten verwenden.

Da es sich bei ITAR nicht um eine formale Zertifizierung handelt, müssen Sie verstehen, welche Begrenzungen und Einschränkungen mit der Exportlizenz verbunden sind. Dann können Sie überprüfen, ob in Azure ausreichende Kontrollen vorhanden sind, um diese Anforderungen zu erfüllen. In diesem Fall ist eines der genauestens zu betrachtenden Probleme der Zugriff durch unsere Techniker, die möglicherweise nicht über eine für die Exportlizenz zulässige Nationalität verfügen.

## <a name="next-steps"></a>Nächste Schritte

 Informationen zur ISM-kompatiblen Konfiguration und Implementierung der VPN-Konnektivität in Azure Australien finden Sie unter [Azure VPN Gateway](vpn-gateway.md).
