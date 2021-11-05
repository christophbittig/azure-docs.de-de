---
title: Erstellen einer Zugriffsüberprüfung von Gruppen und Anwendungen – Azure AD
description: Erfahren Sie, wie Sie eine Gruppenmitglieder oder den Anwendungszugriff betreffende Zugriffsüberprüfung in Azure Active Directory-Zugriffsüberprüfungen erstellen.
services: active-directory
author: ajburnle
manager: daveba
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
ms.openlocfilehash: d97efdafdaca73a65dfe0013d36d83d3b681809c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052100"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Erstellen einer Zugriffsüberprüfung für Gruppen und Anwendungen in Azure AD-Zugriffsüberprüfungen
 
Der Zugriff auf Gruppen und Anwendungen für Mitarbeiter und Gäste ändert sich im Laufe der Zeit. Zur Senkung der Risiken im Zusammenhang mit veralteten Zugriffszuweisungen können Administratoren mithilfe von Azure Active Directory (Azure AD) Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff erstellen. 

Auch Besitzer von Microsoft 365-Gruppen und Sicherheitsgruppen können mit Azure AD Zugriffsüberprüfungen für Gruppenmitglieder erstellen, wenn der globale Administrator oder Benutzeradministrator die Einstellung über das Blatt „Einstellungen für Zugriffsüberprüfungen“ aktiviert (Vorschauversion). Weitere Informationen zu diesen Szenarien finden Sie unter [Verwaltung von Benutzer- und Gastbenutzerzugängen mit Zugriffsüberprüfungen](manage-access-review.md). 
 
Sehen Sie sich ein kurzes Video zum Aktivieren von Zugriffsüberprüfungen an:
 
>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]
 
In diesem Artikel wird die Erstellung einer oder mehrerer Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff beschrieben.
 
## <a name="prerequisites"></a>Voraussetzungen
 
- Azure AD Premium P2
-  Die Rolle „Globalen Administrator“, „Benutzeradministrator“ oder „Identity Governance-Administrator“ zum Erstellen von Überprüfungen für Gruppen und Anwendungen. 
-  Globale Administratoren und Administratoren für privilegierte Rollen können Überprüfungen für Gruppen erstellen, denen Rollen zugewiesen werden können. Weitere Informationen finden Sie unter [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](../roles/groups-concept.md).
- (Vorschauversion) Besitzer von Microsoft 365-Gruppen und Sicherheitsgruppen
 
Weitere Informationen finden Sie unter [Lizenzanforderungen](access-reviews-overview.md#license-requirements).
 
## <a name="create-one-or-more-access-reviews"></a>Erstellen einer oder mehrerer Zugriffsüberprüfungen
 
1. Melden Sie sich beim Azure-Portal an, und öffnen Sie die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
2. Klicken Sie im linken Menü auf **Zugriffsüberprüfungen**.
 
3. Klicken Sie auf **Neue Zugriffsüberprüfung**, um eine neue Zugriffsüberprüfung zu erstellen.
 
    ![Bereich „Zugriffsüberprüfungen“ in Identity Governance](./media/create-access-review/access-reviews.png)
 
4. Wählen Sie in **Schritt 1: Auswählen, was überprüft werden soll** aus, welche Ressource Sie überprüfen möchten.
 
    ![Erstellen einer Zugriffsüberprüfung – Name und Beschreibung der Überprüfung](./media/create-access-review/select-what-review.png)
 
5. Wenn Sie in Schritt 1 **Teams und Gruppen** ausgewählt haben, stehen Ihnen in Schritt 2 zwei Optionen zur Auswahl.
   - **All Microsoft 365 groups with guest users** (Alle Microsoft 365-Gruppen mit Gastbenutzern): Wählen Sie diese Option aus, wenn Sie wiederkehrende Überprüfungen für alle Gastbenutzer in all Ihren Microsoft Teams- und Microsoft 365-Gruppen in Ihrer Organisation erstellen möchten. Beachten Sie, dass dynamische Gruppen und Gruppen, denen Rollen zugewiesen werden können, nicht enthalten sind. Sie können auch einzelne Gruppen ausschließen, indem Sie auf „Auszuschließende Gruppen auswählen“ klicken. 
   - **Select teams + groups** (Teams und Gruppen auswählen): Wählen Sie diese Option aus, wenn Sie feste Teams und Gruppen für die Überprüfung angeben möchten. Nach dem Klicken auf diese Option wird auf der rechten Seite eine Liste mit Gruppen zur Auswahl angezeigt.
 
     ![Teams und Gruppen](./media/create-access-review/teams-groups.png)
 
 
6. Wenn Sie in Schritt 1 die Option **Anwendungen** ausgewählt haben, können Sie in Schritt 2 eine oder mehrere Anwendungen auswählen.
 
    >[!NOTE]
    > Wenn Sie mehrere Gruppen oder Anwendungen auswählen, werden mehrere Zugriffsüberprüfungen erstellt. Wenn Sie z. B. fünf zu überprüfende Gruppen auswählen, werden auch fünf separate Zugriffsüberprüfungen erstellt.
 
   ![Benutzeroberfläche bei Auswahl von „Anwendungen“ anstelle von Gruppen](./media/create-access-review/select-application-detailed.png)
 
7. Als Nächstes können Sie in Schritt 3 einen Bereich für die Überprüfung auswählen. Folgende Optionen sind verfügbar:
   - **Nur Gastbenutzer:** Wenn Sie diese Option auswählen, wird die Zugriffsüberprüfung ausschließlich auf die Azure AD B2B-Gastbenutzer in Ihrem Verzeichnis beschränkt.
   - **Jeder:** Wenn Sie diese Option auswählen, wird die Zugriffsüberprüfung auf alle Benutzerobjekte angewandt, die der Ressource zugeordnet sind.
 
    >[!NOTE]
    > Wenn Sie in Schritt 2 „All Microsoft 365 groups with guest users“ (Alle Microsoft 365-Gruppen mit Gastbenutzern) ausgewählt haben, steht Ihnen in Schritt 3 als einzige Option die Überprüfung von Gastbenutzern zur Verfügung.
 
8. Klicken Sie auf **Weiter: Überprüfungen**.
 
9. Wählen Sie im Abschnitt **Prüfer auswählen** mindestens eine Person für die Zugriffsüberprüfungen aus. Es gibt folgende Auswahlmöglichkeiten:
    - **Gruppenbesitzer** (nur verfügbar, wenn eine Überprüfung für ein Team oder eine Gruppe durchgeführt wird)
    - **Ausgewählte Benutzer oder Gruppen**
    - **Users review own access** (Benutzer überprüfen eigenen Zugriff)
    - **Vorgesetzte von Benutzern**
    Wenn Sie **Managers of users** (Benutzermanager) oder **Gruppenbesitzer** auswählen, haben Sie auch die Möglichkeit, einen Fallbackprüfer anzugeben. Fallbackprüfer werden aufgefordert, eine Überprüfung durchzuführen, wenn für den Benutzer kein Manager im Verzeichnis angegeben ist oder die Gruppe keinen Besitzer hat.
 
    ![Neue Zugriffsüberprüfung](./media/create-access-review/new-access-review.png)
 
10. Im Abschnitt **Wiederholung der Überprüfung angeben** sind vier Angaben erforderlich:
- **Dauer**: Gibt an, wie lange Prüfer etwas für eine Überprüfung eingeben können. 
- **Wiederholung der Überprüfung**: Gibt an, wie oft eine Überprüfung erfolgt – beispielsweise **einmalig, wöchentlich, monatlich, vierteljährlich, halbjährlich oder jährlich**.
- **Startdatum**: Gibt an, wann die Überprüfungsreihe beginnt.
-  **Enddatum**: Gibt an, wann die Überprüfungsreihe endet. Mögliche Optionen sind **Nie**, **An bestimmtem Datum beenden** und **Nach Anzahl von Vorkommen beenden**.

 
    ![Auswählen der Häufigkeit von Überprüfungen](./media/create-access-review/frequency.png)
 
11. Klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Einstellungen**.
 
12. Unter **Einstellungen nach Abschluss** können Sie angeben, was nach Abschluss der Überprüfung geschehen soll.
 
    ![Erstellen einer Zugriffsüberprüfung: „Einstellungen nach Abschluss“](./media/create-access-review/upon-completion-settings-new.png)
 
    Wenn der Zugriff abgelehnter Benutzer am Ende der Überprüfungsdauer automatisch entfernt werden soll, aktivieren Sie das Kontrollkästchen **Ergebnisse automatisch auf Ressource anwenden**. Ist diese Option deaktiviert, müssen die Ergebnisse nach Abschluss der Überprüfung manuell angewendet werden. Weitere Informationen zum Anwenden der Überprüfungsergebnisse finden Sie unter [Verwaltung von Benutzer- und Gastbenutzerzugängen mit Zugriffsüberprüfungen](manage-access-review.md).
    
     Geben Sie unter **Wenn Prüfer nicht reagieren** an, wie mit Benutzern verfahren werden soll, die innerhalb des Überprüfungszeitraums von keinem Prüfer überprüft wurden. Diese Einstellung hat keine Auswirkungen auf Benutzer, die von einem Prüfer überprüft wurden.
 
    - **Keine Änderung**: Der Zugriff des Benutzers bleibt unverändert.
    - **Zugriff entfernen**: Dem Benutzer wird der Zugriff entzogen.
    - **Zugriff genehmigen**: Der Zugriff des Benutzers wird genehmigt.
    - **Empfehlungen annehmen**: Die Systemempfehlungen hinsichtlich der Ablehnung oder Gewährung des weiteren Benutzerzugriffs werden verwendet.
 
     Die Option **Auf abgelehnte Gastbenutzer anzuwendende Aktion** ist nur verfügbar, wenn die Zugriffsüberprüfung so konfiguriert wurde, dass sie nur Gastbenutzer umfasst. Mithilfe dieser Option können Sie angeben, wie mit Gastbenutzern verfahren werden soll, die entweder durch einen Prüfer oder aufgrund der Einstellung **Wenn Prüfer nicht reagieren** abgelehnt wurden.
    - Bei Verwendung von **Mitgliedschaft des Benutzers aus der Ressource entfernen** wird der Zugriff eines abgelehnten Gastbenutzers auf die überprüfte Gruppe oder Anwendung deaktiviert. Der betroffene Benutzer kann sich allerdings weiterhin beim Mandanten anmelden, und alle anderen Zugriffsrechte bleiben erhalten.
    - Bei Verwendung von **Anmeldung des Benutzers für 30 Tage blockieren und den Benutzer anschließend vom Mandanten entfernen** können sich abgelehnte Gastbenutzer nicht mehr beim Mandanten anmelden – unabhängig vom Zugriff auf andere Ressourcen. Sollte diese Maßnahme fälschlicherweise ergriffen worden sein, können Administratoren den Zugriff des Gastbenutzers innerhalb von 30 Tagen nach der Deaktivierung wieder aktivieren. Wird für den deaktivierten Benutzer keine Aktion durchgeführt, wird er nach 30 Tagen aus dem Mandanten gelöscht.
 
    Weitere Informationen zu bewährten Methoden im Zusammenhang mit dem Entfernen von Gastbenutzern, die keinen Zugriff mehr auf Ressourcen in Ihrer Organisation haben, finden Sie unter [Verwenden von Azure AD Identity Governance zum Überprüfen und Entfernen externer Benutzer, die keinen Zugriff mehr auf Ressourcen haben](access-reviews-external-users.md).
 
    > [!NOTE]
    > Die auf abgelehnte Gastbenutzer anzuwendende Aktion kann nicht für Überprüfungen konfiguriert werden, die sich neben Gastbenutzern auf einen größeren Bereich beziehen. Sie kann auch nicht für Überprüfungen von **allen Microsoft 365-Gruppen mit Gastbenutzern** konfiguriert werden. Wenn diese Option nicht konfiguriert werden kann, wird für abgelehnte Benutzer die Standardoption (Entfernen der Benutzermitgliedschaft aus der Ressource) verwendet.
 
13. Verwenden Sie **(Vorschau) Bei Abschluss der Überprüfung Benachrichtigung senden an:** , um Benachrichtigungen mit Abschlussupdates an andere Benutzer oder Gruppen zu senden. Mit diesem Feature können andere Beteiligte als der Ersteller der Überprüfung über den Fortschritt der Überprüfung informiert werden. Um dieses Feature zu verwenden, wählen Sie **Benutzer oder Gruppen auswählen** aus, und fügen Sie einen weiteren Benutzer oder eine weitere Gruppe hinzu, wenn Sie Statusinformationen zur Überprüfung erhalten möchten.
 
14. Wählen Sie unter **Enable review decision helpers** (Entscheidungshilfen für Überprüfungen aktivieren) aus, ob Ihre Prüfer während des Überprüfungsvorgangs Empfehlungen erhalten sollen. Ist diese Option aktiviert, wird für Benutzer, die sich innerhalb der vorangegangenen 30 Tagen angemeldet haben, empfohlen, eine Genehmigung zu erteilen. Bei Benutzern, die sich in den letzten 30 Tagen nicht angemeldet haben, wird dagegen zur Ablehnung geraten.

> [!NOTE]
> Wenn Sie eine anwendungsbasierte Zugriffsüberprüfung erstellen, liegt Ihren Empfehlungen der 30-tägige Intervallzeitraum zugrunde, der auf der letzten Anmeldung des Benutzers bei der Anwendung basiert (nicht auf der letzten Anmeldung beim Mandanten).

![Optionen für das Aktivieren von Entscheidungshilfen](./media/create-access-review/helpers.png)

15. Sie können im Abschnitt **Erweiterte Einstellungen** Folgendes auswählen:
    - Wenn Sie **Begründung erforderlich** auf **Aktivieren** festlegen, müssen Prüfer einen Grund für die Genehmigung oder Ablehnung angeben.
    - Legen Sie **E-Mail-Benachrichtigungen** auf **Aktivieren** fest, damit Azure AD beim Start einer Zugriffsüberprüfung E-Mail-Benachrichtigungen an die Prüfer und beim Abschluss einer Überprüfung Benachrichtigungen an Administratoren sendet.
    - Legen Sie **Erinnerungen** auf **Aktivieren** fest, damit Azure AD Erinnerungen zu laufenden Zugriffsüberprüfungen an alle Prüfer sendet. Prüfer erhalten die Erinnerungen unabhängig davon, ob die Überprüfung zu diesem Zeitpunkt bereits abgeschlossen wurde, in der Mitte des Überprüfungszeitraums.
    - Der Inhalt der an Prüfer gesendeten E-Mail wird automatisch basierend auf den Überprüfungsdetails generiert, z. B. Name der Überprüfung, Name der Ressource, Fälligkeitsdatum usw. Wenn Sie eine Möglichkeit benötigen, zusätzliche Informationen wie etwa weitere Anweisungen oder Kontaktinformationen mitzuteilen, können Sie diese Informationen im Abschnitt **E-Mail mit zusätzlichen Inhalten für Prüfer** angeben. Die eingegebenen Informationen werden in die Einladungs- und Erinnerungs-E-Mails an die zugewiesenen Prüfer eingefügt. Der in der Abbildung unten hervorgehobene Abschnitt zeigt, wo diese Informationen angezeigt werden.
 
      ![Zusätzliche Inhalte für Prüfer](./media/create-access-review/additional-content-reviewer.png)
 
16. Klicken Sie auf **Weiter: Überprüfen und erstellen**, um zur nächsten Seite zu wechseln.
 
  ![Bildschirm „Erstellen“ für Überprüfungen](./media/create-access-review/create-review.png)
17. Benennen Sie die Zugriffsüberprüfung. Wahlweise können Sie jeder Überprüfung eine Beschreibung hinzufügen. Den Prüfern werden Name und Beschreibung angezeigt.
 
18. Überprüfen Sie die Informationen, und wählen Sie **Erstellen** aus.
 
    
 
## <a name="allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview"></a>Ermöglichen der Erstellung und Verwaltung von Zugriffsüberprüfungen ihrer Gruppen für Gruppenbesitzer (Vorschauversion)
 
Erforderliche Rolle: Globaler Administrator oder Benutzeradministrator
 
1. Melden Sie sich beim Azure-Portal an, und öffnen Sie die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
1. Navigieren Sie im linken Menü unter **Zugriffsüberprüfungen** zu **Einstellungen**.
 
1. Legen Sie auf der Seite **Delegieren, wer Zugriffsüberprüfungen erstellen und verwalten kann** die Einstellung **(Vorschau) Gruppenbesitzer können Zugriffsüberprüfungen für eigene Gruppen erstellen und verwalten** auf **Ja** fest.
 
    ![Erstellen von Überprüfungen: Aktivieren von Überprüfungen durch Gruppenbesitzer](./media/create-access-review/group-owners-review-access.png)
 
    > [!NOTE]
    > Standardmäßig ist die Einstellung auf **Nein** festgelegt. Daher muss sie aktualisiert werden, damit Gruppenbesitzer Zugriffsüberprüfungen erstellen und verwalten können.
 
## <a name="start-the-access-review"></a>Starten der Zugriffsüberprüfung
 
Klicken Sie nach dem Festlegen der Einstellungen für eine Zugriffsüberprüfung auf **Starten**. Die Zugriffsüberprüfung wird in der Liste mit einer Angabe des Status angezeigt.
 
![Liste der Zugriffsüberprüfungen mit jeweiligem Status](./media/create-access-review/access-reviews-list.png)
 
Standardmäßig sendet Azure AD kurz nach dem Start der Überprüfung eine E-Mail an die Prüfer. Wenn Sie nicht möchten, dass Azure AD die E-Mail sendet, stellen Sie sicher, dass die Prüfer darüber in Kenntnis gesetzt werden, dass sie eine ausstehende Zugriffsüberprüfung abschließen müssen. Sie können ihnen die Anweisungen zum [Überprüfen des Zugriffs auf Gruppen oder Anwendungen](perform-access-review.md) anzeigen. Wenn Ihre Überprüfung für Gäste gedacht ist, die ihren eigenen Zugriff überprüfen sollen, können Sie ihnen die Anweisungen zum [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen ](review-your-access.md) anzeigen.
 
Wenn Sie Gäste als Prüfer zugewiesen haben, diese die Einladung für den Mandanten aber nicht angenommen haben, erhalten sie keine E-Mail zu Zugriffsüberprüfungen, da die Einladung zuerst akzeptiert werden muss, bevor Überprüfungen vorgenommen werden können.

## <a name="update-the-access-review"></a>Zugriffsüberprüfung aktualisieren

Nachdem eine oder mehrere Zugriffsüberprüfungen gestartet wurden, könnten Sie es sinnvoll finden, die Einstellungen Ihrer vorhandenen Zugriffsüberprüfungen zu ändern oder zu aktualisieren. Hier einige häufige Szenarien, die Sie in Betracht ziehen sollten:

- **Aktualisieren von Einstellungen oder Prüfern**: Wenn eine Zugriffsüberprüfung wiederholt wird, gibt es separate Einstellungen unter „Aktuell“ und „Serie“. Wenn Sie die Einstellungen oder Prüfer unter „Aktuell“ ändern, werden die Änderungen nur auf die aktuelle Zugriffsüberprüfung angewendet. Wenn Sie die Einstellungen unter „Serie“ aktualisieren, wirkt sich das auf alle zukünftigen Wiederholungen aus.

![Aktualisieren der Einstellungen für Zugriffsüberprüfungen](./media/create-access-review/current-v-series-setting.png)

- **Hinzufügen und Entfernen von Prüfern**: Beim Aktualisieren von Zugriffsüberprüfungen können Sie zusätzlich zum primären Prüfer einen Fallbackprüfer hinzufügen. Primäre Prüfer können beim Aktualisieren einer Zugriffsüberprüfung entfernt werden. Fallbackprüfer können jedoch nicht mit Absicht entfernt werden.

    > [!Note]
    > Fallbackprüfer können nur hinzugefügt werden, wenn der Prüfertyp „Manager“ oder „Gruppenbesitzer“ lautet. Primäre Prüfer können hinzugefügt werden, wenn der Prüfertyp ausgewählt ist.

- **Die Prüfer erinnern**: Beim Aktualisieren von Zugriffsüberprüfungen können Sie die Erinnerungsoption unter Erweiterte Einstellungen aktivieren. Nach der Aktivierung erhalten Benutzer zur Mitte des Überprüfungszeitraums eine E-Mail-Benachrichtigung, unabhängig davon, ob sie die Überprüfung abgeschlossen haben oder nicht. 

![Erinnern von Prüfern](./media/create-access-review/reminder-setting.png)



 
## <a name="next-steps"></a>Nächste Schritte
 
- [Überprüfen des Zugriffs auf Gruppen oder Anwendungen](perform-access-review.md)
- [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen](review-your-access.md)
- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)


