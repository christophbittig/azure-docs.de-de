---
title: Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) erstellen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c44c27c0d99c67c4102167f924a1dcb6f87e871
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669699"
---
# <a name="create-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM aufbauen

Die Notwendigkeit, auf privilegierte Azure-Ressourcenrollen und Azure AD-Rollen zugreifen zu müssen, kann sich für Mitarbeiter im Laufe der Zeit ändern. Daher sollten Sie den Zugriff in regelmäßigen Abständen überprüfen, um das mit veralteten Rollenzuweisungen verbundene Risiko zu verringern. Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Zugriffsüberprüfungen für den privilegierten Zugriff auf Azure-Ressourcenrollen und Azure AD-Rollen erstellen. Sie können auch wiederholte Zugriffsüberprüfungen konfigurieren, die automatisch ausgeführt werden. In diesem Artikel wird beschrieben, wie Sie eine oder mehrere Zugriffsüberprüfungen erstellen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] Weitere Informationen zu Lizenzen für PIM finden Sie unter [Lizenzanforderungen für die Verwendung von Privileged Identity Management](subscription-requirements.md).

 Zum Erstellen von Zugriffsüberprüfungen für Azure-Ressourcen müssen Sie der Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) für die Azure-Ressourcen zugewiesen sein. Um Zugriffsüberprüfungen für Azure AD-Rollen erstellen zu können, müssen Sie der Rolle [Globaler Administrator](../roles/permissions-reference.md#global-administrator) oder der Rolle[“Administrator für privilegierte Rollen"](../roles/permissions-reference.md#privileged-role-administrator) zugewiesen sein.

## <a name="create-access-reviews"></a>Erstellen von Zugriffsüberprüfungen

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer an, dem einer der erforderlichen Rollen zugewiesen ist.

2. Wählen Sie **Identity Governance** aus.
 
3. Für **Azure AD-Rollen** wählen Sie **Azure AD-Rollen** unter **Privileged Identity Management**. Für **Azure-Ressourcen** wählen Sie **Azure-Ressourcen** unter **Privileged Identity Management**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png" alt-text="Wählen Sie Identity Governance im Azure Portal Screenshot." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png"::: 
 
4. Für **Azure AD-Rollen** wählen Sie **Azure AD-Rollen** nochmals unter **Verwalten**. Für **Azure-Ressourcen** wählen Sie die Ressource aus, die Sie verwalten möchten, z. B. ein Abonnement.


5. Wählen Sie unter „Verwalten“ die Option **Zugriffsüberprüfungen** und anschließend **Neu** aus, um eine neue Zugriffsüberprüfung zu erstellen.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-reviews.png" alt-text="Azure AD-Rollen: Liste der Zugriffsüberprüfungen mit dem Status aller Überprüfungen.":::
 
6. Benennen Sie die Zugriffsüberprüfung. Wahlweise können Sie jeder Überprüfung eine Beschreibung hinzufügen. Den Prüfern werden Name und Beschreibung angezeigt.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/name-description.png" alt-text="Erstellen einer Zugriffsüberprüfung: Name und Screenshot der Beschreibung.":::

7. Legen Sie das **Startdatum** fest. Standardmäßig findet eine Zugriffsüberprüfung einmalig statt. Sie beginnt an dem Tag, an dem sie erstellt wird, und endet nach einem Monat. Sie können Start- und Enddatum so ändern, dass der Start der Zugriffsüberprüfung in der Zukunft liegt und sie so lange dauert, wie Sie wünschen.

   :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/start-end-dates.png" alt-text=" Screenshot von Startdatum, Häufigkeit, Dauer, Ende, Anzahl und Enddatum.":::

8. Wenn die Zugriffsüberprüfung wiederholt ausgeführt werden soll, ändern Sie die Einstellung **Häufigkeit** von **Einmal** in **Wöchentlich**, **Monatlich**,  **Vierteljährlich**, **Jährlich** oder **Halbjährlich**. Verwenden Sie den Schieberegler oder das Textfeld **Dauer**, um festzulegen, wie viele Tage Prüfer Eingaben für jede Überprüfung der Serie vornehmen können. Für eine monatliche Überprüfung kann beispielsweise eine maximale Dauer von 27 Tagen angegeben werden, um Überschneidungen zu vermeiden.

9. Geben Sie mithilfe der Einstellung **Ende** an, wie die wiederkehrende Zugriffsüberprüfungsreihe beendet werden soll. Die Reihe kann auf drei Arten enden: Die Serie wird unendlich ausgeführt, um Überprüfungen ohne zeitliche Beschränkung zu starten, die Serie wird bis zu einem bestimmten Datum ausgeführt, oder sie wird nach einer bestimmten Anzahl von Vorkommen beendet. Sie oder ein anderer globaler Administrator, der Überprüfungen verwalten darf, können die Serie nach ihrer Erstellung beenden. Dazu in **Einstellungen** das Datum ändern, sodass die Serie an diesem Datum beendet wird.


10. Wählen Sie im **Benutzerbereich** den Bereich der Bewertung aus. Für **Azure AD-Rollen** ist die erste Option für Bereich „Benutzer und Gruppen“. Direkt zugewiesene Benutzer und [Gruppen, denen Rollen zugewiesen werden können](../roles/groups-concept.md), werden in diese Auswahl einbezogen. Für **Azure-Ressourcenrollen** ist der erste Bereich Benutzer. Gruppen, die Azure-Ressourcenrollen zugewiesen sind, werden erweitert, um transitive Benutzerzuweisungen in der Überprüfung mit dieser Auswahl anzuzeigen. Sie können auch **Dienstprinzipale** auswählen, um die Computerkonten mit direktem Zugriff auf die Azure-Ressource oder die Azure AD zu überprüfen.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/users.png" alt-text=" Benutzerbereich zum Überprüfen der Rollenmitgliedschaft von Screenshot.":::

11. Wählen Sie unter **Überprüfen der Rollenmitgliedschaft** die privilegierten Azure AD-Rollen aus, die zu überprüfen sind.

    > [!NOTE]
    > Bei der Auswahl mehrerer Rollen werden mehrere Zugriffsüberprüfungen erstellt. Bei der Auswahl von fünf Rollen werden z. B. fünf separate Zugriffsüberprüfungen erstellt.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/review-role-membership.png" alt-text="Screenshot: Rollenmitgliedschaften überprüfen.":::

12. Schränken Sie unter **Zuweisungstyp** den Bereich für die Überprüfung nach Art der Zuweisung des Prinzipals zur Rolle ein. Wählen Sie **Nur berechtigte Zuweisungen** aus, um berechtigte Zuweisungen (unabhängig vom Aktivierungsstatus beim Erstellen der Überprüfung) zu überprüfen, oder wählen Sie **Nur aktive Zuweisungen** aus, um aktive Zuweisungen zu überprüfen. Wählen Sie **alle aktiven und berechtigten Zuweisungen** aus, um alle Zuweisungen unabhängig vom Typ zu überprüfen.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/assignment-type-select.png" alt-text="Screenshot: Liste der Prüfer für Zuweisungstypen.":::

13. Wählen Sie im Abschnitt **Prüfer** mindestens eine Person für die Überprüfung aller Benutzer aus. Alternativ können Sie auswählen, dass die Mitglieder ihren eigenen Zugriff überprüfen.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reviewers.png" alt-text="Liste der Prüfer mit ausgewählten Benutzer oder Mitgliedern (selbst)":::

    - **Ausgewählte Benutzer** - Verwenden Sie diese Option, um einen bestimmten Benutzer zu bestimmen, der die Überprüfung durchführen soll. Diese Option ist unabhängig vom Umfang der Überprüfung verfügbar, und die ausgewählten Prüfer können Benutzer, Gruppen und Dienstprinzipale überprüfen.
    - **Mitglieder (selbst)** : Mit dieser Option können Benutzer ihre Rollenzuweisungen selbst überprüfen. Diese Option ist nur verfügbar, wenn die Überprüfung auf **Benutzer und Gruppen** oder **Nutzer** beschränkt ist. Für **Azure AD-Rollen** sind Gruppen, die von Rollen zugewiesen werden können, nicht Teil der Prüfung, wenn diese Option gewählt ist. 
    - **Manager**: Verwenden Sie diese Option, wenn der Vorgesetzte des Benutzers seine Rollenzuweisung überprüfen soll. Diese Option ist nur verfügbar, wenn die Überprüfung auf **Benutzer und Gruppen** oder **Nutzer** beschränkt ist. Wenn Sie „Manager“ auswählen, haben Sie auch die Möglichkeit, einen Fallbackprüfer anzugeben. Fallbackprüfer werden aufgefordert, eine Überprüfung für einen Benutzer durchzuführen, wenn für diesen kein Vorgesetzter (Manager) im Verzeichnis angegeben ist. Für **Azure AD-Rollen** werden Gruppen, die von Rollen zugewiesen werden können, vom Fallbackprüfer überprüft, sofern ein solcher ausgewählt ist. 

### <a name="upon-completion-settings"></a>Einstellungen nach Abschluss

1. Erweitern Sie den Abschnitt **Einstellungen nach Abschluss**, um anzugeben, was nach Abschluss der Überprüfung geschehen soll.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings.png" alt-text="Automatisch anzuwendende Einstellungen nach Abschluss und falls die Überprüfung nicht reagieren sollte: Screenshot.":::

2. Soll abgelehnten Benutzern automatisch der Zugriff entzogen werden, legen Sie **Ergebnisse automatisch auf Ressource anwenden** auf **Aktivieren** fest. Falls Sie die Ergebnisse nach Abschluss der Überprüfung manuell anwenden möchten, legen Sie die Einstellung auf **Deaktivieren** fest.

3. Geben Sie mithilfe der Liste **Wenn die Prüfer nicht reagieren** an, was bei Benutzern geschehen soll, die vom Prüfer nicht innerhalb des vorgesehenen Zeitraums überprüft werden. Diese Einstellung hat keine Auswirkungen auf Benutzer, die von den Prüfern überprüft wurden.

    - **Keine Änderung**: Der Zugriff des Benutzers bleibt unverändert.
    - **Zugriff entfernen**: Dem Benutzer wird der Zugriff entzogen.
    - **Zugriff genehmigen**: Der Zugriff des Benutzers wird genehmigt.
    - **Empfehlungen annehmen**: Die Systemempfehlungen hinsichtlich der Ablehnung oder Gewährung des weiteren Benutzerzugriffs werden verwendet.
    
4. Verwenden Sie **Auf verweigerte Gastbenutzer anzuwendende Aktion**, um festzulegen, was mit Gastbenutzern geschieht, die abgelehnt werden. Diese Einstellung kann derzeit für die Überprüfung von Azure AD- und Azure-Ressourcenrollen nicht bearbeitet werden. Gastbenutzer verlieren wie alle Benutzer immer den Zugriff auf die Ressource, wenn sie abgelehnt werden.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/action-to-apply-on-denied-guest-users.png" alt-text="Einstellungen nach Abschluss – Aktion, die auf abgelehnte Gastbenutzer angewendet werden soll: Screenshot.":::

5. Sie können Benachrichtigungen an weitere Benutzer oder Gruppen senden, um Updates zum Überprüfungsabschluss zu erhalten. Mit diesem Feature können andere Beteiligte als der Ersteller der Überprüfung über den Fortschritt der Überprüfung informiert werden. Um dieses Feature zu verwenden, wählen Sie **Benutzer oder Gruppen auswählen** aus, und fügen Sie einen weiteren Benutzer oder eine weitere Gruppe hinzu, wenn Sie Statusinformationen zur Überprüfung erhalten möchten.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings-additional-receivers.png" alt-text="Einstellungen nach Abschluss – Hinzufügen zusätzlicher Benutzer zum Empfangen von Benachrichtigungen: Screenshot.":::

### <a name="advanced-settings"></a>Erweiterte Einstellungen

1. Erweitern Sie den Abschnitt **Erweiterte Einstellungen**, um weitere Einstellungen anzugeben.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/advanced-settings.png" alt-text="Erweiterte Einstellungen, um Empfehlungen anzuzeigen, einen Grund für Genehmigung zu verlangen, Mail-Nachrichten und Erinnerungen: Screenshot.":::

1. Legen Sie **Empfehlungen anzeigen** auf **Aktivieren** fest, damit den Prüfern die Systemempfehlungen auf der Grundlage der Zugriffsinformationen des Benutzers angezeigt werden.

1. Legen Sie **Bei Genehmigung Grund anfordern** auf **Aktivieren** fest, damit der Prüfer einen Grund für die Genehmigung angeben muss.

1. Legen Sie **E-Mail-Benachrichtigungen** auf **Aktivieren** fest, damit Azure AD beim Start einer Zugriffsüberprüfung E-Mail-Benachrichtigungen an die Prüfer und beim Abschluss einer Überprüfung Benachrichtigungen an Administratoren sendet.

1. Legen Sie **Erinnerungen** auf **Aktivieren** fest, damit Azure AD Erinnerungen zu laufenden Zugriffsüberprüfungen an Prüfer sendet, die ihre Überprüfung noch nicht abgeschlossen haben.
1. Der Inhalt der an Prüfer gesendeten E-Mail wird automatisch basierend auf den Überprüfungsdetails generiert, z. B. Name der Überprüfung, Name der Ressource, Fälligkeitsdatum usw. Wenn Sie eine Möglichkeit benötigen, zusätzliche Informationen wie etwa weitere Anweisungen oder Kontaktinformationen mitzuteilen, können Sie diese Informationen in die **E-Mail mit zusätzlichen Inhalten für Prüfer** einfügen. Diese E-Mail wird in die Einladung sowie in Erinnerungs-E-Mails an die zugewiesenen Prüfer einbezogen. Diese Informationen werden in der folgenden Abbildung im hervorgehobenen Abschnitt angezeigt.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/email-info.png" alt-text="Inhalt der an Prüfer gesendeten E-Mail mit Hervorhebungen"::: 

## <a name="manage-the-access-review"></a>Verwalten der Zugriffsüberprüfung

Sie können den Fortschritt der Überprüfungen durch die Prüfer auf der Seite **Übersicht** der Zugriffsüberprüfung nachverfolgen. Zugriffsrechte werden im Verzeichnis erst geändert, wenn die Überprüfung abgeschlossen ist. Unten sehen Sie einen Screenshot, der die Übersichtsseite für Zugriffsüberprüfungen von **Azure-Ressourcen** und **Azure AD- Rollen** zeigt.

:::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png" alt-text="Screenshot: Übersichtsseite der Zugriffsüberprüfungen, die die Einzelheiten der Zugriffsüberprüfung für Azure AD-Rollen zeigt." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png":::

Wenn dies eine einmalige Überprüfung ist, dann befolgen Sie, nachdem der Überprüfungszeitraum vorbei ist oder der Administrator die Überprüfung beendet, die Schritte in [Eine Zugriffsüberprüfung für Azure Ressourcen und Azure AD-Rollen abschließen](pim-complete-azure-ad-roles-and-resource-roles-review.md), um die Ergebnisse zu sehen und anzuwenden.  

Um eine Serie von Zugriffsüberprüfungen zu verwalten, navigieren Sie zur Zugriffsüberprüfung. Dort finden Sie unter den geplanten Überprüfungen die anstehenden Überprüfungen, und Sie können das Enddatum bearbeiten oder Prüfer entsprechend hinzufügen/entfernen.

Basierend auf Ihrer Auswahl unter **Einstellungen nach Abschluss** wird nach dem Enddatum der Überprüfung oder bei manueller Beendigung der Überprüfung die automatische Anwendung ausgeführt. Der Status der Überprüfung ändert sich von **Abgeschlossen** über Zwischenzustände wie **Wird angewandt** schließlich in den Status **Angewandt**. Erwartungsgemäß sollten abgelehnte Benutzer (sofern vorhanden) innerhalb weniger Minuten aus den Rollen entfernt werden.

> [!IMPORTANT]
> Wenn eine Gruppe **Azure-Ressource-Rollen** zugewiesen ist, wird dem Prüfer der Azure-Ressourcenrolle die erweiterte Liste der indirekten Benutzer angezeigt, deren Zugriff über eine geschachtelte Gruppe zugewiesen wurde. Wenn ein Prüfer ein Mitglied einer geschachtelten Gruppe ablehnt, wird dieses Verweigerungsergebnis nicht erfolgreich auf die Rolle angewendet, da der Benutzer nicht aus der geschachtelten Gruppe entfernt wird. Für **Azure AD-Rollen** werden [rollenzugewiesene Gruppen](../roles/groups-concept.md)in der Überprüfung angezeigt, anstatt die Mitglieder der Gruppe zu erweitern, und ein Prüfer genehmigt oder verweigert den Zugriff auf die gesamte Gruppe.

## <a name="update-the-access-review"></a>Zugriffsüberprüfung aktualisieren

Nachdem eine oder mehrere Zugriffsüberprüfungen gestartet wurden, könnten Sie es sinnvoll finden, die Einstellungen Ihrer vorhandenen Zugriffsüberprüfungen zu ändern oder zu aktualisieren. Hier einige häufige Szenarien, die Sie in Betracht ziehen sollten:

- **Hinzufügen und Entfernen von Prüfern**: Beim Aktualisieren von Zugriffsüberprüfungen können Sie zusätzlich zum primären Prüfer einen Fallbackprüfer hinzufügen. Primäre Prüfer können beim Aktualisieren einer Zugriffsüberprüfung entfernt werden. Fallbackprüfer können jedoch nicht mit Absicht entfernt werden.

    > [!Note]
    > Fallbackprüfer können nur hinzugefügt werden, wenn der Prüfertyp „Manager“ ist. Primäre Prüfer können hinzugefügt werden, wenn der Prüfertyp ausgewählt ist.

- **Die Prüfer erinnern**: Beim Aktualisieren von Zugriffsüberprüfungen können Sie die Erinnerungsoption unter Erweiterte Einstellungen aktivieren. Nach der Aktivierung erhalten Benutzer zur Mitte des Überprüfungszeitraums eine E-Mail-Benachrichtigung, unabhängig davon, ob sie die Überprüfung abgeschlossen haben oder nicht. 

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reminder-setting.png" alt-text="Screenshot: Erinnerungsoption unter den Einstellungen für Zugriffsüberprüfungen.":::

- **Aktualisieren der Einstellungen**: Wenn eine Zugriffsüberprüfung wiederholt wird, gibt es unterschiedliche Einstellungen unter „Aktuell“ und unter „Serie“. Durch das Aktualisieren der Einstellungen unter "Aktuell" ändern Sie nur die aktuelle Zugriffsüberprüfung. Wenn Sie Einstellungen unter "Serie" aktualisieren, wirkt sich das auf alle zukünftigen Wiederholungen aus.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png" alt-text="Screenshot: Seite „Einstellungen“ unter Zugriffsüberprüfungen." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png":::
    
## <a name="next-steps"></a>Nächste Schritte

- [Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM](pim-perform-azure-ad-roles-and-resource-roles-review.md)
- [Abschließen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM](pim-complete-azure-ad-roles-and-resource-roles-review.md)
