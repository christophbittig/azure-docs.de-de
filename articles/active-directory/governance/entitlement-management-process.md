---
title: Anforderungsprozess und Benachrichtigungen – Azure AD-Berechtigungsverwaltung
description: Hier finden Sie Informationen zum Anforderungsprozess für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung und darüber, wann entsprechende E-Mail-Benachrichtigungen gesendet werden.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 5/17/2021
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4a2e346983e3d258fde4a5deaa6dc51601cff13
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2021
ms.locfileid: "112376358"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Anforderungsprozess und E-Mail-Benachrichtigungen in der Azure AD-Berechtigungsverwaltung

Wenn ein Benutzer eine Anforderung für ein Zugriffspaket sendet, wird ein Prozess gestartet, um diese Zugriffsanforderung zu übermitteln. Die Azure AD-Berechtigungsverwaltung sendet E-Mail-Benachrichtigungen an genehmigende Personen und Anforderer, wenn wichtige Ereignisse während des Prozesses auftreten. In diesem Artikel finden Sie eine Beschreibung des Anforderungsprozesses und der E-Mail-Benachrichtigungen, die gesendet werden.

## <a name="request-process"></a>Anforderungsprozess

Ein Benutzer, der Zugriff auf ein Zugriffspaket benötigt, kann eine Zugriffsanforderung senden. Abhängig von der Konfiguration der Richtlinie muss die Anforderung möglicherweise genehmigt werden. Wenn eine Anforderung genehmigt wird, startet ein Prozess, um dem Benutzer Zugriff auf jede Ressource im Zugriffspaket zuzuweisen. Das folgende Diagramm zeigt eine Übersicht über den Prozess und die unterschiedlichen Statuswerte:

![Diagramm des Genehmigungsprozesses](./media/entitlement-management-process/request-process.png)

| State | BESCHREIBUNG |
| --- | --- |
| Gesendet | Ein Benutzer sendet eine Anforderung. |
| Ausstehende Genehmigung | Wenn die Richtlinie für ein Zugriffspaket eine Genehmigung erfordert, ändert sich der Status der Anforderung in „Genehmigung ausstehend“. |
| Abgelaufen | Wenn keine der genehmigenden Personen die Anforderung innerhalb des Zeitlimits für die Genehmigungsanforderung genehmigt, läuft die Anforderung ab. In dem Fall muss der Benutzer seine Anforderung erneut senden. |
| Verweigert | Eine genehmigende Person lehnt eine Anforderung ab. |
| Genehmigt | Eine genehmigende Person genehmigt eine Anforderung. |
| Übermitteln | Dem Benutzer wurde **noch kein** Zugriff auf alle Ressourcen im Zugriffspaket zugewiesen. Wenn es sich um einen externen Benutzer handelt, hat der Benutzer möglicherweise noch nicht auf das Ressourcenverzeichnis zugegriffen. Außerdem hat er möglicherweise die Zustimmungsaufforderung noch nicht akzeptiert. |
| Geliefert | Dem Benutzer wurde Zugriff auf alle Ressourcen im Zugriffspaket zugewiesen. |
| Zugriff verlängern | Wenn in der Richtlinie Verlängerungen zulässig sind, hat der Benutzer die Zuweisung verlängert. |
| Zugriff abgelaufen | Der Benutzerzugriff auf das Zugriffspaket ist abgelaufen. Um wieder Zugriff zu erhalten, muss der Benutzer eine Anforderung senden. |

## <a name="email-notifications"></a>E-Mail-Benachrichtigungen

Wenn Sie eine genehmigende Person sind, erhalten Sie E-Mail-Benachrichtigungen, wenn Sie eine Zugriffsanforderung genehmigen müssen. Außerdem erhalten Sie Benachrichtigungen, wenn eine Zugriffsanforderung abgeschlossen wurde. Wenn Sie ein Anforderer sind, erhalten Sie ebenfalls E-Mail-Benachrichtigungen, die den Status Ihrer Anforderung angeben.

Die folgenden Diagramme zeigen, wann diese E-Mail-Benachrichtigungen entweder an die genehmigenden Personen oder den Anforderer gesendet werden. Die entsprechende Nummer für die in den Diagrammen angezeigten E-Mail-Benachrichtigungen finden Sie in der [Tabelle der E-Mail-Benachrichtigungen](entitlement-management-process.md#email-notifications-table).

### <a name="first-approvers-and-alternate-approvers"></a>Erste genehmigende Personen und alternative genehmigende Personen
Das folgende Diagramm zeigt den Ablauf für erste und alternative genehmigende Personen sowie die E-Mail-Benachrichtigungen, die sie während des Anforderungsprozesses erhalten:

![Prozessablauf für erste und alternative genehmigende Personen](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Anforderer
Das folgende Diagramm zeigt den Ablauf für die Anforderer sowie die E-Mail-Benachrichtigungen, die sie während des Anforderungsprozesses erhalten:

![Ablauf für Anforderer](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="multi-stage-approval"></a>Mehrstufige Genehmigung
Das folgende Diagramm zeigt den Ablauf für die genehmigenden Personen auf Stufe 1 und 2 sowie die E-Mail-Benachrichtigungen, die sie während des Anforderungsprozesses erhalten:

![Ablauf des zweistufigen Genehmigungsprozesses](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabelle der E-Mail-Benachrichtigungen
Die folgende Tabelle enthält weitere Details zu den einzelnen E-Mail-Benachrichtigungen. Sie können zum Verwalten dieser E-Mails Regeln verwenden. Beispielsweise können Sie in Outlook Regeln erstellen, um die E-Mails in einen Ordner zu verschieben, wenn die Betreffzeile Wörter aus dieser Tabelle enthält.  Beachten Sie, dass die Wörter auf den Standardspracheinstellungen des Mandanten basieren, in dem der Benutzer Zugriff anfordert.

| # | E-Mail-Betreff | Sendeszenario | Gesendet an |
| --- | --- | --- | --- |
| 1 | Aktion erforderlich: Weitergeleitete Anforderung bis zum *[Datum]* genehmigen oder verweigern | Diese E-Mail wird an alternative genehmigende Personen auf Stufe 1 gesendet (nach Eskalieren der Anforderung), um Maßnahmen zu ergreifen. | Alternative genehmigende Personen auf Stufe 1 |
| 2 | Aktion erforderlich: Anforderung bis zum *[Datum]* genehmigen oder verweigern | Diese E-Mail wird an die erste genehmigende Person gesendet (bei deaktivierter Eskalation), um Maßnahmen zu ergreifen. | Erste genehmigende Person |
| 3 | Erinnerung: Anforderung von *[Anforderer]* bis *[Datum]* genehmigen oder ablehnen | Diese Erinnerungs-E-Mail wird an die erste genehmigende Personen gesendet (bei deaktivierter Eskalation). In der E-Mail wird sie aufgefordert, Maßnahmen zu ergreifen, sofern das noch nicht geschehen ist. | Erste genehmigende Person |
| 4 | Anforderung bis zum *[Datum]* um *[Uhrzeit]* genehmigen oder ablehnen | Diese E-Mail wird an die erste genehmigende Person gesendet (bei aktivierter Eskalation), um Maßnahmen zu ergreifen. | Erste genehmigende Person |
| 5 | Erinnerung an erforderliche Aktion: Anforderung von *[Anforderer]* bis *[Datum]* genehmigen oder ablehnen | Diese Erinnerungs-E-Mail wird an die erste genehmigende Person gesendet (bei aktivierter Eskalation). In der E-Mail wird sie aufgefordert, Maßnahmen zu ergreifen, sofern das noch nicht geschehen ist. | Erste genehmigende Person |
| 6 | Anforderung für *[Zugriffspaket]* ist abgelaufen | Diese E-Mail wird an die erste genehmigende Person und an die alternativen genehmigenden Personen auf Stufe 1 gesendet, nachdem die Anforderung abgelaufen ist. | Erste genehmigende Person, alternative genehmigende Personen auf Stufe 1 |
| 7 | Anforderung von *[Anforderer]* für *[Zugriffspaket]* genehmigt | Diese E-Mail wird an die erste genehmigende Person und an die alternativen genehmigenden Personen auf Stufe 1 gesendet, wenn die Anforderung abgeschlossen ist. | Erste genehmigende Person, alternative genehmigende Personen auf Stufe 1 |
| 8 | Anforderung von *[Anforderer]* für *[Zugriffspaket]* genehmigt | Diese E-Mail wird an die erste genehmigende Person und an alternative genehmigende Personen auf Stufe 1 einer mehrstufigen Anforderung gesendet, wenn die Anforderung auf Stufe 1 genehmigt wurde. | Erste genehmigende Person, alternative genehmigende Personen auf Stufe 1 |
| 9 | Anforderung für *[Zugriffspaket]* abgelehnt | Diese E-Mail wird an den Anforderer gesendet, wenn seine Anforderung abgelehnt wurde. | Anforderer |
| 10 | Ihre Anforderung für *[Zugriffspaket]* ist abgelaufen | Diese E-Mail wird am Ende einer Anforderung mit ein- oder mehrstufiger Genehmigung an den Anforderer gesendet. In der E-Mail wird der Anforderer informiert, dass die Anforderung abgelaufen ist. | Anforderer |
| 11 | Aktion erforderlich: Anforderung bis zum *[Datum]* genehmigen oder verweigern | Diese E-Mail wird an die zweite genehmigende Personen gesendet (bei deaktivierter Eskalation), um Maßnahmen zu ergreifen. | Zweite genehmigende Person |
| 12 | Erinnerung an erforderliche Aktion: Anforderung bis *[Datum]* genehmigen oder ablehnen | Diese Erinnerungs-E-Mail wird an die zweite genehmigende Person gesendet (bei deaktivierter Eskalation). In der Benachrichtigung wird sie aufgefordert, Maßnahmen zu ergreifen, sofern das noch nicht geschehen ist. | Zweite genehmigende Person |
| 13 | Aktion erforderlich: Anforderung von *[Anforderer]* bis *[Datum]* genehmigen oder ablehnen | Diese E-Mail wird an die zweite genehmigende Person gesendet (bei aktivierter Eskalation), um Maßnahmen zu ergreifen. | Zweite genehmigende Person |
| 14 | Erinnerung an erforderliche Aktion: Anforderung von *[Anforderer]* bis *[Datum]* genehmigen oder ablehnen | Diese Erinnerungs-E-Mail wird an die zweite genehmigende Personen gesendet (bei aktivierter Eskalation). In der Benachrichtigung wird sie aufgefordert, Maßnahmen zu ergreifen, sofern das noch nicht geschehen ist. | Zweite genehmigende Person |
| 15 | Aktion erforderlich: Weitergeleitete Anforderung bis zum *[Datum]* genehmigen oder verweigern | Diese E-Mail wird an die alternativen genehmigenden Personen auf Stufe 2 gesendet (bei aktivierter Eskalation), um Maßnahmen zu ergreifen. | Alternative genehmigende Personen auf Stufe 2 |
| 16 | Anforderung von *[Anforderer]* für *[Zugriffspaket]* genehmigt | Diese E-Mail wird an die zweite genehmigende Person und an die alternativen genehmigenden Personen auf Stufe 2 gesendet, wenn die Anforderung genehmigt wird. | Zweite genehmigende Person, alternative genehmigende Personen auf Stufe 2 |
| 17 | Anforderung für *[Zugriffspaket]* ist abgelaufen | Diese E-Mail wird an die zweite genehmigende Person oder an alternative genehmigende Personen gesendet, nachdem die Anforderung abgelaufen ist. | Zweite genehmigende Person, alternative genehmigende Personen auf Stufe 2 |
| 18 | Sie haben jetzt Zugriff auf *[Zugriffspaket]* | Diese E-Mail wird an die Endbenutzer gesendet, damit diese den Zugriff nutzen können. | Anforderer |
| 19 | Zugriff für *[Zugriffspaket]* bis *[Datum]* verlängern | Diese E-Mail wird an die Endbenutzer gesendet, bevor deren Zugriff abläuft. | Anforderer |
| 20 | Zugriff auf *[Zugriffspaket]* beendet | Diese E-Mail wird an die Endbenutzer gesendet, nachdem ihr Zugriff abgelaufen ist. | Anforderer |

### <a name="access-request-emails"></a>E-Mails zu Zugriffsanforderungen

Wenn ein Anforderer eine Zugriffsanforderung für ein Zugriffspaket sendet, das eine Genehmigung erfordert, erhalten alle in der Richtlinie festgelegten genehmigenden Personen eine E-Mail-Benachrichtigung mit Details zur Anforderung. Die E-Mail enthält folgende Details: Name der Organisation des Anforderers und geschäftliche Begründung sowie Start- und Enddatum (sofern angegeben) des angeforderten Zugriffs. Zu den Details zählen auch Übermittlungs- und Ablaufzeitpunkt der Anforderung.

Die E-Mail enthält einen Link, über den die genehmigenden Personen zu „Mein Zugriff“ wechseln können, um die Zugriffsanforderung zu genehmigen oder abzulehnen. Hier sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die an eine genehmigende Person gesendet wird, um eine Zugriffsanforderung abzuschließen:

![E-Mail für die Genehmigung der Anforderung zum Zugreifen auf ein Paket](./media/entitlement-management-shared/approver-request-email.png)

Genehmigende Personen können auch eine Erinnerungs-E-Mail erhalten. In der E-Mail wird die genehmigende Person aufgefordert, eine Entscheidung bezüglich der Anforderung zu treffen. Hier sehen Sie als Beispiel eine E-Mail-Benachrichtigung, welche die genehmigende Person als Erinnerung an das Ergreifen von Maßnahmen erhält:

![Erinnerungs-E-Mail für Zugriffsanforderung](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>E-Mails an alternative genehmigende Personen für Anforderungen

Wenn die Einstellung für alternative genehmigende Personen aktiviert ist und die Anforderung noch aussteht, wird sie weitergeleitet. Alternative genehmigende Personen erhalten eine E-Mail, um die Anforderung zu genehmigen oder abzulehnen. Alternative genehmigende Personen können auf Stufe 1 und 2 aktiviert werden. Hier sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die die alternative genehmigende Person erhält:

![E-Mail an alternative genehmigende Personen für die Anforderung](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Sowohl die ersten genehmigenden Personen als auch die alternativen genehmigenden Personen können die Anforderung genehmigen oder ablehnen.

### <a name="approved-or-denied-emails"></a>E-Mails über Genehmigung oder Ablehnung

 Wenn eine genehmigende Person eine von einem Anforderer gesendete Zugriffsanforderung empfängt, kann sie die Zugriffsanforderung genehmigen oder ablehnen. Die genehmigende Person muss eine geschäftliche Begründung der Entscheidung hinzufügen. Hier sehen Sie als Beispiel eine E-Mail, die nach dem Genehmigen einer Anforderung an erste oder alternative genehmigende Personen gesendet wird:

![E-Mail bei genehmigter Anforderung des Paketzugriffs](./media/entitlement-management-process/approver-request-email-approved.png)

Wenn eine Zugriffsanforderung genehmigt und der Zugriff erteilt wurde, wird der Anforderer per E-Mail benachrichtigt, dass er nun Zugriff auf das Zugriffspaket hat. Hier sehen Sie als Beispiel eine E-Mail-Benachrichtigung, die an einen Anforderer gesendet wird, wenn ihm Zugriff auf ein Zugriffspaket gewährt wurde:

![E-Mail an Anforderer bei genehmigter Zugriffsanforderung](./media/entitlement-management-process/requestor-email-approved.png)

Wenn eine Zugriffsanforderung abgelehnt wird, erhält der Anforderer eine E-Mail-Benachrichtigung. Hier sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die an einen Anforderer gesendet wird, wenn die Zugriffsanforderung abgelehnt wurde:

![E-Mail an Anforderer bei abgelehnter Anforderung](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="multi-stage-approval-access-request-emails"></a>E-Mails bei Zugriffsanforderung mit mehrstufiger Genehmigung

Wenn die mehrstufige Genehmigung aktiviert ist, muss die Anforderung durch mindestens eine genehmigende Person jeder Stufe genehmigt werden, bevor der Anforderer Zugriff erhalten kann.

Auf Stufe 1 erhält die erste genehmigende Person die E-Mail mit der Zugriffsanforderung und trifft eine Entscheidung.

Nachdem die erste oder alternative genehmigende Person die Anforderung auf Stufe 1 genehmigt hat, beginnt Stufe 2. Auf Stufe 2 erhält die zweite genehmigende Person eine E-Mail-Benachrichtigung für die Zugriffsanforderung. Wenn sich die zweite genehmigende Person oder alternative genehmigende Personen auf Stufe 2 (bei aktivierter Eskalation) entscheidet, die Anforderung zu genehmigen oder abzulehnen, werden E-Mail-Benachrichtigungen an die erste und zweite genehmigende Person und alle alternativen genehmigenden Personen auf Stufe 1 und 2 sowie an den Anforderer gesendet.

### <a name="expired-access-request-emails"></a>E-Mails über abgelaufene Zugriffsanforderungen

Zugriffsanforderungen können ablaufen, wenn keiner der genehmigenden Personen die Anforderung genehmigt oder abgelehnt hat. 

Wenn die Anforderung das konfigurierte Ablaufdatum erreicht und abläuft, kann sie von den genehmigenden Personen nicht mehr genehmigt oder abgelehnt werden.

Der Anforderer erhält eine E-Mail-Benachrichtigung mit dem Hinweis, dass die Zugriffsanforderung abgelaufen ist und erneut übermittelt werden muss. Das folgende Diagramm zeigt den Ablauf für den Anforderer sowie die E-Mail-Benachrichtigungen, die er bei der Anforderung des erweiterten Zugriffs erhält:

![Prozessablauf für den Anforderer des erweiterten Zugriffs](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Hier sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die ein Anforderer erhält, wenn die Zugriffsanforderung abgelaufen ist:

![E-Mail über abgelaufene Zugriffsanforderung an Anforderer](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Nächste Schritte

- [Anfordern des Zugriffs auf ein Zugriffspaket](entitlement-management-request-access.md)
- [Genehmigen oder Ablehnen von Zugriffsanforderungen](entitlement-management-request-approve.md)
