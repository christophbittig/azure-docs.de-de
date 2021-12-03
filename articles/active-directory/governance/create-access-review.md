---
title: Erstellen einer Zugriffsüberprüfung von Gruppen und Anwendungen – Azure AD
description: Erfahren Sie, wie Sie eine Gruppenmitglieder oder den Anwendungszugriff betreffende Zugriffsüberprüfung in Azure Active Directory erstellen.
services: active-directory
author: ajburnle
manager: karenhoran
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 224c81d1f5e827bb53fce1cfdeae22028ec1b598
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517670"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad"></a>Erstellen einer Zugriffsüberprüfung von Gruppen und Anwendungen in Azure AD

Der Zugriff auf Gruppen und Anwendungen für Mitarbeiter und Gäste ändert sich im Laufe der Zeit. Zur Senkung der Risiken im Zusammenhang mit veralteten Zugriffszuweisungen können Administratoren mithilfe von Azure Active Directory (Azure AD) Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff erstellen.

Auch Besitzer von Microsoft 365-Gruppen und Sicherheitsgruppen können mit Azure AD Zugriffsüberprüfungen für Gruppenmitglieder erstellen, wenn der globale Administrator oder Benutzeradministrator die Einstellung über den Bereich **Einstellungen für Zugriffsüberprüfungen** aktiviert (Vorschau). Weitere Informationen zu diesen Szenarien finden Sie unter [Verwaltung von Benutzer- und Gastbenutzerzugängen mit Zugriffsüberprüfungen](manage-access-review.md).

Sehen Sie sich ein kurzes Video zur Aktivierung von Zugriffsüberprüfungen an.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

In diesem Artikel wird die Erstellung einer oder mehrerer Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P2.
- Die Rolle „Globaler Administrator“, „Benutzeradministrator“ oder „Identity Governance-Administrator“ zum Erstellen von Überprüfungen für Gruppen und Anwendungen.
- Globale Administratoren und Administratoren für privilegierte Rollen können Überprüfungen von Gruppen erstellen, denen Rollen zugewiesen werden können. Weitere Informationen finden Sie unter [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](../roles/groups-concept.md).
- (Vorschau) Besitzer von Microsoft 365-Gruppen und Sicherheitsgruppen.

Weitere Informationen finden Sie unter [Lizenzanforderungen](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Erstellen einer oder mehrerer Zugriffsüberprüfungen

1. Melden Sie sich beim Azure-Portal an, und öffnen Sie die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Wählen Sie im linken Menü **Zugriffsüberprüfungen** aus.

1. Wählen Sie **Neue Zugriffsüberprüfung** aus, um eine neue Zugriffsüberprüfung zu erstellen.

    ![Screenshot: Bereich „Zugriffsüberprüfungen“ in Identity Governance.](./media/create-access-review/access-reviews.png)

1. Wählen Sie im Feld **Auswählen, was überprüft werden soll** aus, welche Ressource Sie überprüfen möchten.

    ![Screenshot: Erstellen einer Zugriffsüberprüfung.](./media/create-access-review/select-what-review.png)

1. Wenn Sie **Teams und Gruppen** ausgewählt haben, stehen Ihnen zwei Optionen zur Auswahl.

   - **Alle Microsoft 365-Gruppen mit Gastbenutzern**: Wählen Sie diese Option aus, wenn Sie wiederkehrende Überprüfungen für alle Gastbenutzer in all Ihren Microsoft Teams- und Microsoft 365-Gruppen in Ihrer Organisation erstellen möchten. Dynamische Gruppen und Gruppen, denen Rollen zugewiesen werden können, sind nicht enthalten. Sie können auch einzelne Gruppen ausschließen, indem Sie **Auszuschließende Gruppen auswählen** auswählen.
   - **Teams und Gruppen auswählen**: Wählen Sie diese Option aus, wenn Sie eine Gruppe von Teams oder Gruppen für die Überprüfung angeben möchten. Auf der rechten Seite wird eine Liste der Gruppen angezeigt, aus der Sie auswählen können.

     ![Screenshot: Auswählen von Teams und Gruppen.](./media/create-access-review/teams-groups.png)

1. Wenn Sie **Anwendungen** ausgewählt haben, können Sie eine oder mehrere Anwendungen auswählen.

   ![Screenshot: Benutzeroberfläche, die angezeigt wird, wenn Sie Anwendungen anstelle von Gruppen ausgewählt haben.](./media/create-access-review/select-application-detailed.png)

    > [!NOTE]
    > Wenn Sie mehrere Gruppen oder Anwendungen auswählen, werden mehrere Zugriffsüberprüfungen erstellt. Wenn Sie z. B. fünf zu überprüfende Gruppen auswählen, werden fünf separate Zugriffsüberprüfungen erstellt.

1. Jetzt können Sie einen Bereich für die Überprüfung auswählen. Folgende Optionen sind verfügbar:

    - **Nur Gastbenutzer**: Diese Option beschränkt die Zugriffsüberprüfung ausschließlich auf die Azure AD B2B-Gastbenutzer in Ihrem Verzeichnis.
    - **Jeder**: Mit dieser Option wird die Zugriffsüberprüfung auf alle Benutzerobjekte angewendet, die der Ressource zugeordnet sind.

    > [!NOTE]  
    > Wenn Sie **Alle Microsoft 365-Gruppen mit Gastbenutzern** ausgewählt haben, steht Ihnen als einzige Option für die Überprüfung **Nur Gastbenutzer** zur Verfügung.

1. Wählen Sie **Weiter: Überprüfungen** aus.

1. Wählen Sie im Abschnitt **Prüfer festlegen** im Feld **Prüfer auswählen** mindestens eine Person aus, die die Zugriffsüberprüfungen ausführen soll. Es gibt folgende Auswahlmöglichkeiten:

    - **Gruppenbesitzer**: Diese Option ist nur verfügbar, wenn Sie eine Überprüfung für ein Team oder eine Gruppe durchführen.
    - **Ausgewählte Benutzer oder Gruppen**
    - **Benutzer überprüfen ihren eigenen Zugriff**
    - **Vorgesetzte von Benutzern**

   Wenn Sie **Vorgesetzte von Benutzern** oder **Gruppenbesitzer** auswählen, können Sie auch einen Fallbackprüfer angeben. Fallbackprüfer werden aufgefordert, eine Überprüfung durchzuführen, wenn für den Benutzer im Verzeichnis kein Vorgesetzter angegeben ist oder wenn die Gruppe keinen Besitzer hat.

      ![Screenshot: Neue Zugriffsüberprüfung.](./media/create-access-review/new-access-review.png)

1. Wählen Sie im Abschnitt **Wiederholung der Überprüfung angeben** die folgenden Optionen aus:

   - **Dauer (in Tagen)** : Gibt an, wie lange Prüfer etwas für eine Überprüfung eingeben kann.
   - **Startdatum**: Gibt an, wann die Überprüfungsreihe beginnt.
   - **Enddatum**: Gibt an, wann die Überprüfungsreihe endet. Mögliche Optionen sind **Nie**, **An bestimmtem Datum beenden** oder **Nach Anzahl von Vorkommen beenden**.

     ![Screenshot, der zeigt, wie oft die Überprüfung durchgeführt werden soll.](./media/create-access-review/frequency.png)

1. Wählen Sie **Weiter: Einstellungen** aus.

1. Im Abschnitt **Einstellungen nach Abschluss** können Sie angeben, was nach Abschluss der Überprüfung geschehen soll.

    ![Screenshot: Einstellungen nach Abschluss.](./media/create-access-review/upon-completion-settings-new.png)

    - **Ergebnisse automatisch auf Ressource anwenden**: Aktivieren Sie dieses Kontrollkästchen, wenn der Zugriff abgelehnter Benutzer am Ende der Überprüfungsdauer automatisch entfernt werden soll. Wenn die Option deaktiviert ist, müssen Sie die Ergebnisse nach Abschluss der Überprüfung manuell anwenden. Weitere Informationen zum Anwenden der Überprüfungsergebnisse finden Sie unter [Verwaltung von Benutzer- und Gastbenutzerzugängen mit Zugriffsüberprüfungen](manage-access-review.md).

    - **Wenn Prüfer nicht reagieren**: Mit dieser Option geben Sie an, wie mit Benutzern verfahren werden soll, die innerhalb des Überprüfungszeitraums von keinem Prüfer überprüft wurden. Diese Einstellung wirkt sich nicht auf Benutzer aus, die von einem Prüfer überprüft wurden. Die Dropdownliste enthält die folgenden Optionen:

       - **Keine Änderung**: Der Zugriff des Benutzers bleibt unverändert.
       - **Zugriff entfernen**: Dem Benutzer wird der Zugriff entzogen.
       - **Zugriff genehmigen**: Der Zugriff des Benutzers wird genehmigt.
       - **Empfehlungen annehmen**: Die Systemempfehlungen hinsichtlich der Ablehnung oder Gewährung des weiteren Benutzerzugriffs werden verwendet.

    - **Auf abgelehnte Gastbenutzer anzuwendende Aktion**: Diese Option ist nur verfügbar, wenn die Zugriffsüberprüfung so konfiguriert wurde, dass sie nur Gastbenutzer umfasst. Mithilfe dieser Option können Sie angeben, wie mit Gastbenutzern verfahren werden soll, die entweder durch einen Prüfer oder aufgrund der Einstellung **Wenn Prüfer nicht reagieren** abgelehnt wurden.

       - **Mitgliedschaft des Benutzers aus der Ressource entfernen**: Bei Verwendung dieser Option wird der Zugriff eines abgelehnten Gastbenutzers auf die überprüfte Gruppe oder Anwendung deaktiviert. Sie können sich weiterhin beim Mandanten anmelden und verlieren keinen anderen Zugriff.
       - **Anmeldung des Benutzers für 30 Tage blockieren und den Benutzer anschließend vom Mandanten entfernen**: Bei Verwendung dieser Option können sich abgelehnte Gastbenutzer nicht mehr beim Mandanten anmelden – unabhängig vom Zugriff auf andere Ressourcen. Wurde diese Maßnahme fälschlicherweise ergriffen, können Administratoren den Zugriff des Gastbenutzers innerhalb von 30 Tagen nach der Deaktivierung wieder aktivieren. Wird für den deaktivierten Gastbenutzer keine Aktion durchgeführt, wird er nach 30 Tagen aus dem Mandanten gelöscht.

    Weitere Informationen zu bewährten Methoden im Zusammenhang mit dem Entfernen von Gastbenutzern, die keinen Zugriff mehr auf Ressourcen in Ihrer Organisation haben, finden Sie unter [Verwenden von Azure AD Identity Governance zum Überprüfen und Entfernen externer Benutzer, die keinen Zugriff mehr auf Ressourcen haben](access-reviews-external-users.md).

    > [!NOTE]
    > Die **auf abgelehnte Gastbenutzer anzuwendende Aktion** kann nicht für Überprüfungen konfiguriert werden, die sich neben Gastbenutzern auf einen größeren Bereich beziehen. Sie kann auch nicht für Überprüfungen von **allen Microsoft 365-Gruppen mit Gastbenutzern** konfiguriert werden. Wenn diese Option nicht konfiguriert werden kann, wird für abgelehnte Benutzer die Standardoption (Entfernen der Mitgliedschaft eines Benutzers aus der Ressource) verwendet.

1. Verwenden Sie die Option **Bei Abschluss der Überprüfung Benachrichtigung senden an:** , um Benachrichtigungen mit Abschlussupdates an andere Benutzer oder Gruppen zu senden. Mit diesem Feature können andere Beteiligte als der Ersteller der Überprüfung über den Fortschritt der Überprüfung informiert werden. Um dieses Feature zu verwenden, wählen Sie **Benutzer oder Gruppen auswählen** aus, und fügen Sie einen weiteren Benutzer oder eine weitere Gruppe hinzu, für den bzw. die Sie Statusinformationen zur Überprüfung erhalten möchten.

1. Wählen Sie im Abschnitt **Entscheidungshilfen für Überprüfungen aktivieren** aus, ob Ihre Prüfer während des Überprüfungsvorgangs Empfehlungen erhalten sollen. Wenn diese Option aktiviert ist, werden Benutzer, die sich während der letzten 30 Tage angemeldet haben, zur Genehmigung empfohlen. Für Benutzer, die sich während der letzten 30 Tage nicht angemeldet haben, wird die Ablehnung empfohlen.

   > [!NOTE]
   > Wenn Sie eine anwendungsbasierte Zugriffsüberprüfung erstellen, liegt Ihren Empfehlungen der 30-tägige Intervallzeitraum zugrunde, der auf der letzten Anmeldung des Benutzers bei der Anwendung basiert (nicht auf der letzten Anmeldung beim Mandanten).

   ![Screenshot: Option „Entscheidungshilfen für Prüfer aktivieren“.](./media/create-access-review/helpers.png)

1. Sie können im Abschnitt **Erweiterte Einstellungen** Folgendes auswählen:

    - **Begründung erforderlich**: Wenn Sie dieses Kontrollkästchen aktivieren, müssen Prüfer einen Grund für die Genehmigung oder Ablehnung angeben.
    - **E-Mail-Benachrichtigungen**: Aktivieren Sie dieses Kontrollkästchen, damit Azure AD beim Start einer Zugriffsüberprüfung E-Mail-Benachrichtigungen an die Prüfer und beim Abschluss einer Überprüfung Benachrichtigungen an Administratoren sendet.
    - L **Erinnerungen**: Aktivieren Sie dieses Kontrollkästchen, damit Azure AD Erinnerungen zu laufenden Zugriffsüberprüfungen an alle Prüfer sendet. Prüfer erhalten die Erinnerungen nach der Hälfte der Überprüfung, unabhängig davon, ob sie ihre Überprüfung abgeschlossen haben oder nicht.
    - **Zusätzlicher Inhalt für E-Mail an Prüfer**: Der Inhalt der an Prüfer gesendeten E-Mail wird automatisch basierend auf den Überprüfungsdetails generiert, z. B. Name der Überprüfung, Name der Ressource, Fälligkeitsdatum usw. Wenn Sie weitere Informationen übermitteln müssen, können Sie in dem Feld Details wie Anweisungen oder Kontaktinformationen angeben. Die eingegebenen Informationen werden in die Einladung eingefügt, und Erinnerungs-E-Mails werden an die zugewiesenen Prüfer gesendet. Der in der folgenden Abbildung hervorgehobene Abschnitt zeigt, wo diese Informationen angezeigt werden.

      ![Screenshot: Zusätzliche Inhalte für Prüfer.](./media/create-access-review/additional-content-reviewer.png)

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

   ![Screenshot: Registerkarte „Überprüfen und erstellen“.](./media/create-access-review/create-review.png)

1. Benennen Sie die Zugriffsüberprüfung. Wahlweise können Sie jeder Überprüfung eine Beschreibung hinzufügen. Den Prüfern werden Name und Beschreibung angezeigt.

1. Überprüfen Sie die Informationen, und wählen Sie **Erstellen** aus.

## <a name="allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview"></a>Ermöglichen der Erstellung und Verwaltung von Zugriffsüberprüfungen ihrer Gruppen für Gruppenbesitzer (Vorschau)

Die Rolle muss „Globaler Administrator“ oder „Benutzeradministrator“ lauten.

1. Melden Sie sich beim Azure-Portal an, und öffnen Sie die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Wählen Sie im Menü auf der linken Seite unter **Zugriffsüberprüfungen** die Option **Einstellungen** aus.

1. Legen Sie auf der Seite **Delegieren, wer Zugriffsüberprüfungen erstellen und verwalten kann** die Einstellung **(Vorschau) Gruppenbesitzer können Zugriffsüberprüfungen für Gruppen, deren Besitzer sie sind, erstellen und verwalten** auf **Ja** fest.

    ![Screenshot: Aktivieren der Überprüfung durch Gruppenbesitzer.](./media/create-access-review/group-owners-review-access.png)

    > [!NOTE]
    > Standardmäßig ist die Einstellung auf den Wert **Nein** festgelegt. Um Gruppenbesitzern das Erstellen und Verwalten von Zugriffsüberprüfungen zu erlauben, legen Sie die Einstellung auf **Ja** fest.

## <a name="start-the-access-review"></a>Starten der Zugriffsüberprüfung

Wählen Sie nach dem Festlegen der Einstellungen für eine Zugriffsüberprüfung die Option **Starten** aus. Die Zugriffsüberprüfung wird in der Liste mit einer Angabe des Status angezeigt.

![Screenshot: Liste der Zugriffsüberprüfungen und deren Status.](./media/create-access-review/access-reviews-list.png)

Standardmäßig sendet Azure AD kurz nach dem Start der Überprüfung eine E-Mail an die Prüfer. Wenn Sie nicht möchten, dass Azure AD die E-Mail sendet, stellen Sie sicher, dass die Prüfer darüber in Kenntnis gesetzt werden, dass sie eine ausstehende Zugriffsüberprüfung abschließen müssen. Sie können ihnen die Anweisungen zum [Überprüfen des Zugriffs auf Gruppen oder Anwendungen](perform-access-review.md) anzeigen. Wenn Ihre Überprüfung für Gäste gedacht ist, die ihren eigenen Zugriff überprüfen sollen, können Sie ihnen die Anweisungen zum [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen ](review-your-access.md) anzeigen.

Wenn Sie Gäste als Prüfer zugewiesen haben und diese ihre Einladung für den Mandanten nicht akzeptiert haben, erhalten sie keine E-Mail von Zugriffsüberprüfungen. Sie müssen zunächst die Einladung annehmen, bevor sie mit der Überprüfung beginnen können.

## <a name="update-the-access-review"></a>Zugriffsüberprüfung aktualisieren

Nachdem eine oder mehrere Zugriffsüberprüfungen gestartet wurden, möchten Sie die Einstellungen Ihrer vorhandenen Zugriffsüberprüfungen möglicherweise ändern oder aktualisieren. Im Folgenden finden Sie einige häufige Szenarien, die sie berücksichtigen sollten:

- **Einstellungen oder Prüfern aktualisieren**: Wenn eine Zugriffsüberprüfung wiederholt wird, gibt es getrennte Einstellungen für **Aktuell** und **Serie**. Wenn Sie die Einstellungen oder Prüfer unter **Aktuell** aktualisieren, werden die Änderungen nur auf die aktuelle Zugriffsüberprüfung angewendet. Wenn Sie die Einstellungen oder Prüfer unter **Serie** aktualisieren, werden die Einstellungen für alle zukünftigen Wiederholungen aktualisiert.

   ![Screenshot: Aktualisieren der Einstellungen für die Zugriffsüberprüfung.](./media/create-access-review/current-v-series-setting.png)

- **Prüfer hinzufügen und entfernen**: Beim Aktualisieren von Zugriffsüberprüfungen können Sie zusätzlich zum primären Prüfer einen Fallbackprüfer hinzufügen. Primäre Prüfer können entfernt werden, wenn Sie eine Zugriffsüberprüfung aktualisieren. Fallbackprüfer können standardmäßig nicht entfernt werden.

    > [!Note]
    > Fallbackprüfer können nur hinzugefügt werden, wenn der Prüfertyp „Vorgesetzter“ oder „Gruppenbesitzer“ lautet. Primäre Prüfer können hinzugefügt werden, wenn der Prüfertyp der ausgewählte Benutzer ist.

- **Prüfer erinnern**: Beim Aktualisieren von Zugriffsüberprüfungen können Sie die Option **Erinnerungen** unter **Erweiterte Einstellungen** aktivieren. Benutzer erhalten dann nach der Hälfte des Überprüfungszeitraums eine E-Mail-Benachrichtigung, unabhängig davon, ob sie ihre Überprüfung abgeschlossen haben oder nicht.

   ![Screenshot: Erinnerungen für Prüfer.](./media/create-access-review/reminder-setting.png)

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen des Zugriffs auf Gruppen oder Anwendungen](perform-access-review.md)
- [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen](review-your-access.md)
- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)


