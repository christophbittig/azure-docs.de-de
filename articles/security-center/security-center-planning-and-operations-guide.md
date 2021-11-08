---
title: 'Defender für Cloud: Leitfaden zu Planung und Betrieb'
description: Dieses Dokument ist hilfreich bei der Planung vor der Einführung von Defender für Cloud und informiert über Aspekte des täglichen Betriebs.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 3f4f174594e7aa10369ed1d822b51df6c22e33b6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445064"
---
# <a name="planning-and-operations-guide"></a>Leitfaden zu Planung und Betrieb

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Leitfaden richtet sich an IT-Experten, IT-Architekten, Informationssicherheitsanalysten und Cloudadministratoren, die Defender für Cloud verwenden möchten.


## <a name="planning-guide"></a>Planungshandbuch
Dieser Leitfaden enthält Vorgänge, mit denen Sie die Verwendung von Defender für Cloud auf der Grundlage der Sicherheitsanforderungen und des Cloudverwaltungsmodells Ihres Unternehmens optimieren können. Für eine optimale Nutzung von Defender für Cloud ist es wichtig zu verstehen, wie der Dienst von den verschiedenen Personen oder Teams in Ihrer Organisation genutzt wird, um die Anforderungen in puncto sichere Entwicklung und sicherer Betrieb, Überwachung, Governance und Reaktionen auf Vorfälle zu erfüllen. Bei der Planung der Verwendung von Defender für Cloud sind insbesondere folgende Aspekte zu berücksichtigen:

* Sicherheitsrollen und Zugriffsteuerungen
* Sicherheitsrichtlinien und -empfehlungen
* Datensammlung und -speicherung
* Integration Azure-fremder Ressourcen
* Kontinuierliche Sicherheitsüberwachung
* Reaktion auf Vorfälle

Im nächsten Abschnitt erfahren Sie, wie Sie jeden dieser Aspekte bei Ihrer Planung berücksichtigen und die Empfehlungen auf der Grundlage Ihrer Anforderungen umsetzen.


> [!NOTE]
> In den [häufig gestellten Fragen zu Defender für Cloud](faq-general.yml) finden Sie eine Liste mit allgemeinen Fragen, die in der Entwurfs- und Planungsphase ebenfalls hilfreich sein kann.

## <a name="security-roles-and-access-controls"></a>Sicherheitsrollen und Zugriffsteuerungen
Je nach Größe und Struktur Ihrer Organisation wird Defender für Cloud unter Umständen von mehreren Einzelpersonen und Teams zur Durchführung unterschiedlicher sicherheitsbezogener Aufgaben verwendet. Das folgende Diagramm zeigt ein Beispiel für fiktive Personae und deren jeweilige Rollen und Sicherheitsaufgaben:

![Rollen.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Defender für Cloud ermöglicht es diesen Personen, die unterschiedlichen Aufgaben auszuführen. Beispiel:

**Jeff (Workloadbesitzer)**

* Verwaltet eine Cloudworkload und die dazugehörigen Ressourcen
* Verantwortlich für die Implementierung und Verwaltung von Schutzmaßnahmen in Übereinstimmung mit der Sicherheitsrichtlinie des Unternehmens

**Ellen (CISO/CIO)**

* Verantwortlich für alle Sicherheitsaspekte im Unternehmen
* Möchte die Sicherheitssituation des Unternehmens in Bezug auf Cloudworkloads verstehen
* Muss über größere Angriffe und Risiken informiert werden

**David (IT-Sicherheit)**

* Legt die Sicherheitsrichtlinien des Unternehmens fest, um für die richtigen Schutzmaßnahmen zu sorgen
* Überwacht die Einhaltung von Richtlinien
* Erstellt Berichte für die Geschäftsleitung oder Prüfer

**Judy (Security Operations)**

* Überwacht und reagiert auf Sicherheitswarnungen rund um die Uhr
* Eskaliert zu Cloudworkloadbesitzer oder IT Security Analyst

**Sam (Security Analyst)**

* Untersucht Angriffe
* Arbeitet zusammen mit dem Cloudworkloadbesitzer an der Lösung

Defender für Cloud verwendet die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Diese stellt [integrierte Rollen](../role-based-access-control/built-in-roles.md) bereit, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können. Wenn ein Benutzer Defender für Cloud öffnet, werden nur Informationen für Ressourcen angezeigt, auf die er Zugriff hat. Dies bedeutet, dass dem Benutzer die Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ für das Abonnement oder die Ressourcengruppe einer Ressource zugewiesen wird. Neben diesen Rollen gibt es zwei spezifische Rollen für Defender für Cloud:

- **Sicherheitsleseberechtigter:** Benutzer mit dieser Rolle können nur Konfigurationen für Defender für Cloud (einschließlich Empfehlungen, Warnungen, Richtlinien und Integrität) anzeigen, aber keine Änderungen vornehmen.
- **Sicherheitsadministrator:** Es gelten die gleichen Berechtigungen wie bei einem Sicherheitsleseberechtigten, zusätzlich sind jedoch auch Rechte zum Ändern der Sicherheitsrichtlinie und zum Verwerfen von Empfehlungen und Warnungen vorhanden.

Die oben beschriebenen Rollen für Defender für Cloud haben keinen Zugriff auf andere Dienstbereiche von Azure wie Speicher, Web- und Mobilbereich oder Internet der Dinge.

Für die Personen in der obigen Abbildung wären die folgenden Azure RBAC-Einstellungen erforderlich:

**Jeff (Workloadbesitzer)**

* Ressourcengruppenbesitzer/-mitwirkender

**Ellen (CISO/CIO)**

* Abonnementbesitzer/-mitwirkender oder Sicherheitsadministrator

**David (IT-Sicherheit)**

* Abonnementbesitzer/-mitwirkender oder Sicherheitsadministrator

**Judy (Security Operations)**

* Abonnementleser oder Sicherheitsleseberechtigter zum Anzeigen von Warnungen
* Abonnementbesitzer/-mitwirkender oder Sicherheitsadministrator zum Verwerfen von Warnungen

**Sam (Security Analyst)**

* Abonnementleser zum Anzeigen von Warnungen
* Abonnementbesitzer/-mitwirkender zum Verwerfen von Warnungen
* Möglicherweise ist ein Zugriff auf den Arbeitsbereich erforderlich.

Einige weitere wichtige Informationen, die es zu berücksichtigen gilt:

* Nur Abonnementbesitzer/-mitwirkende und Sicherheitsadministratoren können eine Sicherheitsrichtlinie bearbeiten.
* Nur Abonnement- und Ressourcengruppenbesitzer und Mitwirkende können Sicherheitsempfehlungen für eine Ressource anwenden.

Bei der Planung der Zugriffssteuerung mit Azure RBAC für Defender für Cloud müssen Sie zunächst ermitteln, von wem Defender für Cloud in Ihrer Organisation verwendet wird. Außerdem muss ermittelt werden, welche Aufgaben diese Personen jeweils ausführen, und dann müssen die Azure RBAC-Einstellungen entsprechend konfiguriert werden.

> [!NOTE]
> Es empfiehlt sich, den Benutzern eine Rolle zuzuweisen, die jeweils nur so viele Berechtigungen umfasst wie für die Erfüllung ihrer Aufgaben erforderlich sind. So sollte etwa Benutzern, die nur Informationen zum Sicherheitszustand von Ressourcen anzeigen, aber keine Aktionen durchführen müssen (also beispielsweise keine Empfehlungen umsetzen oder Richtlinien bearbeiten), nur die Leserrolle zugewiesen werden.
>
>

## <a name="security-policies-and-recommendations"></a>Sicherheitsrichtlinien und -empfehlungen
Eine Sicherheitsrichtlinie definiert die gewünschte Konfiguration Ihrer Workloads und trägt zur Erfüllung unternehmensbezogener oder gesetzlicher Sicherheitsanforderungen bei. In Defender für Cloud können Sie Richtlinien für Ihre Azure-Abonnements definieren und auf die Art der Workload oder auf die Vertraulichkeit der Daten abstimmen.

Richtlinien für Defender für Cloud umfassen folgende Komponenten:
- [Datensammlung:](enable-data-collection.md) Agent-Bereitstellung und Datensammlungseinstellungen.
- [Sicherheitsrichtlinie:](tutorial-security-policy.md) Eine [Azure-Richtlinie](../governance/policy/overview.md), die festlegt, welche Kontrollen von Defender für Cloud überwacht und empfohlen werden. Sie können aber auch Azure Policy verwenden, um neue Definitionen zu erstellen, zusätzliche Richtlinien zu definieren und verwaltungsgruppenübergreifende Richtlinien zuzuweisen.
- [E-Mail-Benachrichtigungen:](configure-email-notifications.md) Sicherheitskontakte und Benachrichtigungseinstellungen.
- [Tarif:](enhanced-security-features-overview.md) Es stehen Tarife mit und ohne erweiterte Sicherheitsfeatures von Defender für Cloud zur Verfügung. Hiervon hängt ab, welche Features von Defender für Cloud für Ressourcen innerhalb des Gültigkeitsbereichs verfügbar sind. (Kann für Abonnements und Arbeitsbereiche oder für Ressourcengruppen über die API angegeben werden.)

> [!NOTE]
> Die Angabe eines Sicherheitskontakts stellt sicher, dass Azure bei einem Sicherheitsvorfall die richtige Person in Ihrer Organisation erreicht. Weitere Informationen zur Aktivierung dieser Empfehlung finden Sie unter [Konfigurieren von E-Mail-Benachrichtigungen für Sicherheitswarnungen](configure-email-notifications.md).

### <a name="security-policies-definitions-and-recommendations"></a>Sicherheitsrichtliniendefinitionen und -empfehlungen
Defender für Cloud erstellt für jedes Ihrer Azure-Abonnements automatisch eine Standardsicherheitsrichtlinie. Sie können die Richtlinie in Defender für Cloud bearbeiten oder mithilfe von Azure Policy neue Definitionen erstellen, zusätzliche Richtlinien definieren, verwaltungsgruppenübergreifende Richtlinien zuweisen (die die gesamte Organisation, eine darin enthaltene Unternehmenseinheit und Ähnliches darstellen können) sowie die Einhaltung dieser Richtlinien bereichsübergreifend überwachen.

Ermitteln Sie vor dem Konfigurieren der Sicherheitsrichtlinien zunächst anhand der [Sicherheitsempfehlungen](review-security-recommendations.md), ob diese Richtlinien für Ihre verschiedenen Abonnements und Ressourcengruppen geeignet sind. Es ist auch wichtig zu verstehen, welche Aktionen aufgrund der Sicherheitsempfehlungen ausgeführt werden sollten und wer in Ihrer Organisation für die Überwachung neuer Empfehlungen und die Durchführung der erforderlichen Schritte verantwortlich ist.

## <a name="data-collection-and-storage"></a>Datensammlung und -speicherung
Defender für Cloud verwendet den Log Analytics-Agent, um sicherheitsrelevante Daten von Ihren virtuellen Computern zu sammeln. Dieser Agent wird auch vom Azure Monitor-Dienst verwendet. Die von diesem Agent [gesammelten Daten](enable-data-collection.md) werden in Ihren Log Analytics-Arbeitsbereichen gespeichert.

### <a name="agent"></a>Agent

Wenn in der Sicherheitsrichtlinie die automatische Bereitstellung aktiviert ist, wird der Log Analytics-Agent (für [Windows](../azure-monitor/agents/agent-windows.md) oder [Linux](../azure-monitor/vm/monitor-virtual-machine.md)) auf allen unterstützten Azure-VMs sowie auf allen neu erstellten VMs installiert. Wenn der Log Analytics-Agent bereits auf der VM oder dem Computer installiert wurde, nutzt Defender für Cloud den installierten Agent. Der Prozess des Agents ist so konzipiert, dass er nicht invasiv ist und nur eine minimale Auswirkung auf die VM-Leistung hat.

Der Log Analytics-Agent für Windows erfordert TCP-Port 443. Weitere Informationen finden Sie im Artikel zur [Problembehandlung](troubleshooting-guide.md).

Die Datensammlung kann bei Bedarf in der Sicherheitsrichtlinie deaktiviert werden. Da der Log Analytics-Agent allerdings unter Umständen durch andere Überwachungs- und Verwaltungsdienste von Azure verwendet wird, wird er nicht automatisch deinstalliert, wenn Sie die Datensammlung in Defender für Cloud deaktivieren. Sie können den Agent bei Bedarf manuell deinstallieren.

> [!NOTE]
> Eine Liste mit unterstützten virtuellen Computern finden Sie in den [häufig gestellten Fragen zu Defender für Cloud](faq-vms.yml).

### <a name="workspace"></a>Arbeitsbereich

Ein Arbeitsbereich ist eine Azure-Ressource, die als Container für Daten fungiert. Sie oder andere Mitglieder Ihrer Organisation können mehrere Arbeitsbereiche nutzen, um unterschiedliche Mengen von Daten zu verwalten, die in Ihrer gesamten IT-Infrastruktur oder Teilen davon erfasst werden.

Die vom Log Analytics-Agent im Auftrag von Defender für Cloud gesammelten Daten werden entweder in bereits vorhandenen Log Analytics-Arbeitsbereichen, die mit Ihrem Azure-Abonnement verknüpft sind, oder unter Berücksichtigung des geografischen Standorts des virtuellen Computers in neuen Arbeitsbereichen gespeichert.

Im Azure-Portal steht eine Liste mit Ihren Log Analytics-Arbeitsbereichen zur Verfügung. Diese enthält auch die von Defender für Cloud erstellten Arbeitsbereiche. Für neue Arbeitsbereiche wird eine zugehörige Ressourcengruppe erstellt. Für beide gilt folgende Namenskonvention:

* Arbeitsbereich: *DefaultWorkspace-[Abonnement-ID]-[Region]*
* Ressourcengruppe: *DefaultResourceGroup-[Region]*

Für Arbeitsbereiche, die von Defender für Cloud erstellt wurden, werden die Daten 30 Tage aufbewahrt. Bei vorhandenen Arbeitsbereichen basiert die Vermerkdauer auf dem Tarif des Arbeitsbereichs. Sie können auch einen bereits vorhandenen Arbeitsbereich verwenden.

> [!NOTE]
> Microsoft unternimmt große Anstrengungen, um für den Datenschutz und die Sicherheit dieser Daten zu sorgen. Microsoft hält strenge Compliance- und Sicherheitsrichtlinien ein – angefangen bei der Codierung bis hin zum Betreiben von Diensten. Weitere Informationen zur Behandlung von Daten und zum Datenschutz finden Sie unter [Datensicherheit in Microsoft Defender für die Cloud](data-security.md).
>

## <a name="onboard-non-azure-resources"></a>Integrieren Azure-fremder Ressourcen

Defender für Cloud kann den Sicherheitsstatus Ihrer Azure-fremden Computer überwachen. Dazu müssen die entsprechenden Ressourcen jedoch zunächst integriert werden. Weitere Informationen zum Integrieren Azure-fremder Ressourcen finden Sie im Artikel zum [Integrieren Azure-fremder Computer](quickstart-onboard-machines.md).

## <a name="ongoing-security-monitoring"></a>Kontinuierliche Sicherheitsüberwachung
Nach dem Konfigurieren und Anwenden der Empfehlungen von Defender für Cloud müssen Sie sich Gedanken zu den Betriebsprozessen von Defender für Cloud machen.

Die Übersicht von Defender für Cloud bietet einen einheitlichen Überblick über die Sicherheit Ihrer Azure-Ressourcen sowie der Azure-fremden Ressourcen, mit denen Sie ggf. eine Verbindung hergestellt haben. Das folgende Beispiel zeigt eine Umgebung mit zahlreichen zu behandelnden Problemen:

![Dashboard an.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Der reguläre Betrieb wird durch Defender für Cloud nicht beeinträchtigt. Die Lösung überwacht Ihre Bereitstellungen passiv und stellt Empfehlungen auf der Grundlage der aktivierten Sicherheitsrichtlinien bereit.

Wenn Sie Defender für Cloud zum ersten Mal für Ihre aktuelle Azure-Umgebung aktivieren, sollten Sie sich unbedingt alle Empfehlungen auf der Seite **Empfehlungen** ansehen.

Planen Sie den täglichen Aufruf der Option „Informationen zu Bedrohungen“ ein. Hier können Sie sich über Sicherheitsrisiken für die Umgebung informieren und so beispielsweise ermitteln, ob ein bestimmter Computer Teil eines Botnets ist.

### <a name="monitoring-for-new-or-changed-resources"></a>Überwachung auf neue oder geänderte Ressourcen

Die meisten Azure-Umgebungen sind dynamisch, wobei Ressourcen regelmäßig erstellt, hoch- oder heruntergefahren, neu konfiguriert und geändert werden. Defender für Cloud hilft Ihnen dabei, den Überblick über den Sicherheitszustand dieser neuen Ressourcen zu behalten.

Wenn Sie Ihrer Azure-Umgebung neue Ressourcen (virtuelle Computer, SQL-Datenbanken) hinzufügen, erkennt Defender für Cloud dies automatisch und beginnt mit der Überwachung ihrer Sicherheit. Hierzu gehören auch PaaS-Webrollen und -Workerrollen. Wenn in der [Sicherheitsrichtlinie](tutorial-security-policy.md)die Datensammlung aktiviert ist, werden für Ihre virtuellen Computer automatisch zusätzliche Überwachungsfunktionen aktiviert.

Außerdem sollten Sie die vorhandenen Ressourcen regelmäßig auf Konfigurationsänderungen prüfen, die Sicherheitsrisiken, Abweichungen von den empfohlenen Grundwerten und Sicherheitswarnungen verursacht haben könnten. 

### <a name="hardening-access-and-applications"></a>Härten von Zugriff und Anwendungen

Im Rahmen Ihrer Sicherheitsmaßnahmen sollten Sie auch vorbeugende Maßnahmen zur Beschränkung des Zugriffs auf virtuelle Computer ergreifen und die Anwendungen überwachen, die auf virtuellen Computern ausgeführt werden. Die Sperrung des eingehenden Datenverkehrs für Ihre virtuellen Azure-Computer trägt zur Verringerung der Angriffsfläche bei und ermöglicht gleichzeitig eine komfortable bedarfsgerechte Verbindungsherstellung mit virtuellen Computern. Verwenden Sie das Zugriffsfeature [JIT-VM-Zugriff](just-in-time-access-usage.md), um den Zugriff auf Ihre virtuellen Computer zu härten.

Sie können [adaptive Anwendungssteuerungen](adaptive-application-controls.md) verwenden, um einzugrenzen, welche Anwendungen auf Ihren in Azure befindlichen virtuellen Computern ausgeführt werden können. Neben anderen Vorteilen verbessert dies die Möglichkeiten, ihre virtuellen Computer vor Schadsoftware zu schützen. Defender für Cloud analysiert auf dem virtuellen Computer ausgeführte Prozesse unter Verwendung von maschinellem Lernen, um Sie bei der Erstellung von Regeln für Zulassungslisten zu unterstützen.


## <a name="incident-response"></a>Reaktion auf Vorfälle
Defender für Cloud erkennt Bedrohungen, wenn sie entstehen, und macht Sie darauf aufmerksam. Organisationen sollten eine Überwachung auf neue Sicherheitswarnungen implementieren und bei Bedarf geeignete Maßnahmen zur weiteren Untersuchung oder Abwehr des Angriffs ergreifen. Weitere Informationen zur Funktionsweise des Bedrohungsschutzes von Defender für Cloud finden Sie unter [Wie erkennt Defender für Cloud Bedrohungen?](alerts-overview.md#detect-threats).

Dieser Artikel soll Ihnen zwar nicht als Hilfe beim Erstellen eines eigenen Reaktionsplans für Sicherheitsvorfälle dienen, aber wir verwenden Microsoft Azure Security Response im Cloudlebenszyklus als Grundlage für die Phasen der Reaktionen auf Vorfälle. Die Phasen sind in der folgenden Abbildung dargestellt:

![Die Phasen der Reaktion auf Vorfälle im Cloudlebenszyklus.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Zu diesem Zweck können Sie den [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) des NIST (National Institute of Standards and Technology) verwenden.
>

Warnungen von Defender für Cloud können in den folgenden Phasen verwendet werden:

* **Erkennen**: Identifizieren einer verdächtigen Aktivität in Ressourcen
* **Bewerten**: Durchführen der ersten Bewertung, um weitere Informationen zur verdächtigen Aktivität zu erhalten
* **Diagnostizieren**: Verwenden der Wiederherstellungsschritte für das technische Verfahren zum Beheben des Problems

Die in jeder Sicherheitswarnung enthaltenen Informationen ermöglichen es, die Art des Angriffs zu ermitteln und mögliche Lösungen vorzuschlagen. Einige Warnungen enthalten auch Links zu weiteren Informationen oder zu anderen Informationsquellen in Azure. Sie können die bereitgestellten Informationen zur weiteren Untersuchung und zum Starten von Korrekturmaßnahmen verwenden. Außerdem können Sie die in Ihrem Arbeitsbereich gespeicherten, sicherheitsrelevanten Daten auch durchsuchen.

Das folgende Beispiel zeigt eine verdächtige RDP-Aktivität:

![Verdächtige Aktivität.](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Diese Seite enthält Details zur Angriffszeit, den Hostnamen der Quelle, den virtuellen Zielcomputer sowie empfohlene Schritte. In bestimmten Fällen sind die Quellinformationen des Angriffs möglicherweise leer. Weitere Informationen zu diesem Verhalten finden Sie im [Artikel zu fehlenden Quellinformationen in Warnungen von Defender für Cloud](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts).

Nach Ermittlung des betroffenen Systems können Sie eine zuvor erstellte [Workflowautomatisierung](workflow-automation.md) ausführen. Dabei handelt es sich um eine Sammlung von Prozeduren, die über Defender für Cloud ausgeführt werden können, sobald sie durch eine Warnung ausgelöst wurden.

Im [Video zur Nutzung von Defender für Cloud und Microsoft Operations Management Suite für eine Reaktion auf Vorfälle](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) wird anhand einiger Demos veranschaulicht, wie Defender für Cloud in den einzelnen Phasen verwendet werden kann.

> [!NOTE]
> Unter [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md) erhalten Sie weitere Informationen dazu, wie Sie Funktionen von Defender für Cloud bei der Reaktion auf Vorfälle nutzen können.
>
>

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie gelernt, wie Sie die Einführung von Defender für Cloud planen. Weitere Informationen zu Defender für Cloud finden Sie hier:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md)
* [Integrieren von Sicherheitslösungen in Microsoft Defender für Cloud](./partner-integration.md): Hier erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Häufig gestellte Fragen zu Microsoft Defender für Cloud](faq-general.yml): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](/archive/blogs/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Azure-Sicherheit und -Compliance.