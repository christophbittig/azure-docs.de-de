---
title: Verbessern des optimalen Betriebs mit Advisor
description: Verwenden Sie Azure Advisor, um den optimalen Betrieb für Ihre Azure-Abonnements zu verbessern und weiterzuentwickeln.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 151edab6c389e32abaaa73fec3012fa3bdcdf75a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650418"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Sicherstellen des optimalen Betriebs mit Azure Advisor

Empfehlungen zum optimalen Betrieb in Azure Advisor können Sie bei folgenden Aspekten unterstützen: 
- Effizienz von Prozessen und Workflows.
- Verwaltbarkeit von Ressourcen.
- Bewährte Methoden für die Bereitstellung. 

Diese Empfehlungen stehen Ihnen auf der Registerkarte **Erstklassige Betriebsprozesse** des Advisor-Dashboards zur Verfügung.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Erstellen von Azure Service Health-Warnungen für Benachrichtigungen bei Azure-Problemen

Es wird empfohlen, Azure Service Health-Warnungen einzurichten, um über Azure-Dienstprobleme benachrichtigt zu werden. [Azure Service Health](https://azure.microsoft.com/features/service-health/) ist ein kostenloser Dienst, der personalisierte Anleitungen und Unterstützung bietet, wenn Sie von einem Azure-Dienstproblem betroffen sind. Advisor identifiziert Abonnements, für die keine Warnungen konfiguriert sind, und empfiehlt deren Konfiguration.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Speicherkontenentwurf zum Vermeiden der maximalen Abonnementgrenze

In einer Azure-Region können maximal 250 Speicherkonten pro Abonnement unterstützt werden. Wenn Sie dieses Limit erreichen, können Sie keine weiteren Speicherkonten mit dieser Kombination aus Abonnement und Region erstellen. Advisor überprüft Ihre Abonnements und bietet Empfehlungen, damit Sie weniger Speicherkonten für Regionen/Abonnements entwerfen, bei denen das Höchstlimit bald erreicht ist.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Sicherstellen, dass Sie bei Bedarf Zugang zu Azure-Cloudexperten haben

Bei unternehmenskritischen Workloads ist es wichtig, bei Bedarf Zugang zu technischem Support zu haben. Advisor identifiziert potenzielle geschäftskritische Abonnements, die keinen technischen Support im Supportplan enthalten. Es wird ein Upgrade auf eine Option empfohlen, die den technischen Support umfasst.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Löschen und erneutes Erstellen Ihres Pools, um eine veraltete interne Komponente zu entfernen

Wenn Ihr Pool eine veraltete interne Komponente verwendet, löschen Sie den Pool, und erstellen Sie ihn neu, um Stabilität und Leistung zu verbessern.

## <a name="repair-invalid-log-alert-rules"></a>Reparieren ungültiger Protokollwarnungsregeln

Azure Advisor erkennt Warnungsregeln mit ungültigen Abfragen im Bedingungsabschnitt. Sie können in Azure Monitor Protokollwarnungsregeln erstellen und diese zum Ausführen von Analyseabfragen in bestimmten Intervallen verwenden. Anhand der Ergebnisse der Abfrage wird ermittelt, ob eine Warnung ausgelöst werden muss. Es kann vorkommen, dass Analytics-Abfragen aufgrund von Änderungen an referenzierten Ressourcen, Tabellen oder Befehlen im Laufe der Zeit ungültig werden. Von Advisor wird empfohlen, die Abfrage in der Warnungsregel zu korrigieren, um eine automatische Deaktivierung zu verhindern und die Überwachung Ihrer Ressourcen in Azure sicherzustellen. [Weitere Informationen zur Behandlung von Problemen mit Warnungsregeln](../azure-monitor/alerts/alerts-troubleshoot-log.md).

## <a name="use-azure-policy-recommendations"></a>Verwenden von Azure Policy-Empfehlungen

Azure Policy ist ein Dienst in Azure, mit dem Sie Richtlinien erstellen, zuweisen und verwalten können. Mit diesen Richtlinien werden Regeln und Auswirkungen für Ihre Ressourcen erzwungen. Die folgenden Azure Policy-Empfehlungen sollen Sie beim Sicherstellen des optimalen Betriebs unterstützen: 

**Markierungen verwalten.** Diese Richtlinie dient zum Hinzufügen oder Ersetzen des angegebenen Tags und des zugehörigen Werts, wenn eine Ressource erstellt oder aktualisiert wird. Vorhandene Ressourcen können durch Auslösen eines Wartungstasks gewartet werden. Tags für Ressourcengruppen werden von dieser Richtlinie nicht geändert.

**Erzwingen von geografischen Konformitätsanforderungen.** Mit dieser Richtlinie können Sie die Speicherorte einschränken, die Ihre Organisation beim Bereitstellen von Ressourcen angeben kann. 

**Angeben von zulässigen VM-SKUs für Bereitstellungen.** Über diese Richtlinie können Sie einen Satz von SKUs für virtuelle Computer angeben, die Ihre Organisation bereitstellen kann.

**Erzwingen Sie *Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden*.**

**Aktivieren Sie *Tag von der Ressourcengruppe erben*.** Diese Richtlinie dient zum Hinzufügen oder Ersetzen des angegebenen Tags aus der übergeordneten Ressourcengruppe und des zugehörigen Werts, wenn eine Ressource erstellt oder aktualisiert wird. Vorhandene Ressourcen können durch Auslösen eines Wartungstasks gewartet werden.

Advisor empfiehlt ein paar einzelne Azure-Richtlinien, die Kunden dabei helfen, einen optimalen Betrieb durch Umsetzung bewährter Methoden zu erzielen. Wenn ein Kunde beschließt, eine empfohlene Richtlinie zuzuweisen, wird die Empfehlung unterdrückt. Wenn sich der Kunde entscheidet, die Richtlinie zu einem späteren Zeitpunkt zu entfernen, wird die Empfehlung weiterhin von Advisor unterdrückt, da wir die Entfernung als sicheres Signal für folgende Absichten interpretieren:

1.  Der Kunde hat die Richtlinie entfernt, weil sie, trotz der Empfehlung von Advisor, nicht auf seinen speziellen Anwendungsfall zutrifft. 
2.  Der Kunde kennt die Richtlinie und ist damit vertraut, nachdem er sie zugewiesen und entfernt hat, und kann Sie bei Bedarf ohne Anleitung erneut zuweisen oder entfernen, wenn sie später für seinen Anwendungsfall doch noch relevant werden sollte. Wenn der Kunde es für angebracht hält, dieselbe Richtlinie erneut zuzuweisen, kann er dies in Azure Policy tun, ohne dass eine Empfehlung von Advisor erforderlich ist. Beachten Sie, dass diese Logik insbesondere für die Richtlinienempfehlung in der Kategorie „Optimaler Betrieb“ (Operational Excellence) gilt. Diese Regeln gelten nicht für Sicherheitsempfehlungen.  


## <a name="no-validation-environment-enabled"></a>Keine Überprüfungsumgebung aktiviert
Azure Advisor stellt fest, dass in Ihrem aktuellen Abonnement keine Überprüfungsumgebung aktiviert wurde. Beim Erstellen Ihrer Hostpools haben Sie auf der Registerkarte „Eigenschaften“ unter \"Überprüfungsumgebung\" die Option \"Nein\" ausgewählt. Mit der Aktivierung von mindestens einem Hostpool mit einer Überprüfungsumgebung sorgen Sie für Geschäftskontinuität durch Windows Virtual Desktop-Dienstbereitstellungen mit frühzeitiger Erkennung potenzieller Probleme. [Weitere Informationen](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Sicherstellen, dass die Produktionsumgebung (keine Überprüfungsumgebung) von stabiler Funktionalität profitiert
Azure Advisor erkennt, dass bei Ihnen für zu viele Hostpools eine Überprüfungsumgebung aktiviert wurde. Damit Überprüfungsumgebungen Ihren Zweck am besten erfüllen können, sollten Sie mindestens eine davon nutzen. Die Anzahl von Überprüfungsumgebungen sollte aber niemals die Hälfte Ihrer Anzahl von Hostpools überschreiten. Indem Sie eine ausgewogene Anzahl von Hostpools mit aktivierter und mit deaktivierter Überprüfungsumgebung nutzen, können Sie von den Vorteilen der mehrstufigen Bereitstellungen am besten profitieren, die Ihnen Windows Virtual Desktop bei bestimmten Updates bietet. Öffnen Sie zum Beheben dieses Problems die Eigenschaften Ihres Hostpools, und wählen Sie neben der Einstellung \"Überprüfungsumgebung\" die Option \"Nein\" aus.

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Aktivieren von Traffic Analytics zum Anzeigen von Erkenntnissen zu Datenverkehrsmustern für Azure-Ressourcen
Traffic Analytics ist eine cloudbasierte Lösung, die Einblick in Benutzer- und Anwendungsaktivitäten in Azure bietet. Traffic Analytics analysiert Flussprotokolle von Network Watcher für Netzwerksicherheitsgruppen (NSGs), um Einblicke in den Datenfluss zu ermöglichen. Mit Traffic Analytics können Sie „Top Talkers“ für Azure-Bereitstellungen und andere Bereitstellungen anzeigen, geöffnete Ports, Protokolle und schädliche Datenflüsse in Ihrer Umgebung untersuchen und Ihre Netzwerkbereitstellung in Bezug auf die Leistung optimieren. Sie können Datenflussprotokolle mit Verarbeitungsintervallen von 10 und 60 Minuten verarbeiten, um schnellere Analyseergebnisse für Ihren Datenverkehr zu erhalten. Es empfiehlt sich, Traffic Analytics für Ihre Azure-Ressourcen zu aktivieren. 

## <a name="increase-vcpu-limits-for-your-deployments-for-pay-as-you-go-subscription-preview"></a>Erhöhen der vCPU-Grenzwerte für Ihre Bereitstellungen für ein Abonnement mit nutzungsbasierter Bezahlung (Vorschau)
Diese Erfahrung wurde erstellt, um eine einfache Möglichkeit zum Erhöhen des Kontingents zu bieten, um Sie bei ihren wachsenden Anforderungen zu unterstützen und Bereitstellungsprobleme aufgrund von Kontingenteinschränkungen zu vermeiden. Wir haben die Option „schnelle Problembehebung“ für eingeschränkte Abonnements aktiviert, um eine einfache 1-Klick-Option zum Erhöhen des Kontingents für die vCPUs von 10 auf 20 bereitzustellen. Dieser vereinfachte Ansatz ruft die [Kontingent-Rest-API](https://techcommunity.microsoft.com/t5/azure-governance-and-management/using-the-new-quota-rest-api/ba-p/2183670) im Namen des Benutzers auf, um das Kontingent zu erhöhen.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie weitere Informationen zu Empfehlungen des Advisor:
* [Einführung in Advisor](advisor-overview.md)
* [Erste Schritte](advisor-get-started.md)
* [Kostenempfehlungen von Advisor](advisor-cost-recommendations.md)
* [Verbessern der Leistung von Azure-Anwendungen mit dem Azure Advisor](advisor-performance-recommendations.md)
* [Zuverlässigkeitsempfehlungen von Advisor](advisor-high-availability-recommendations.md)
* [Sicherheitsempfehlungen von Advisor](advisor-security-recommendations.md)
* [Advisor-REST-API](/rest/api/advisor/)
