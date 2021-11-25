---
title: Überprüfen Ihres Zugriffs auf Ressourcen mit Zugriffsüberprüfungen – Azure AD
description: Hier erfahren Sie, wie Sie Ihren eigenen Zugriff auf Ressourcen mit Azure Active Directory-Zugriffsüberprüfungen überprüfen.
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
ms.date: 08/27/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a997e4f92e1336d75f1c9c690b4c84ae0bddcf6f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524149"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Selbstüberprüfung von Zugriffspaketen und Ressourcen mit der Azure AD-Berechtigungsverwaltung

Die Azure Active Directory-Berechtigungsverwaltung erleichtert Unternehmen die Verwaltung des Zugriffs auf Gruppen, Anwendungen und SharePoint-Websites. In diesem Artikel wird beschrieben, wie Sie Ihre zugewiesenen Zugriffspakete selbst überprüfen können.

## <a name="review-your-own-access-by-using-my-access"></a>Überprüfen des eigenen Zugriffs mithilfe von „Mein Zugriff“

Sie können Ihren eigenen Zugriff auf eine Gruppe, eine Anwendung oder ein Zugriffspaket auf zwei Arten überprüfen.

### <a name="use-email"></a>Mit einer E-Mail

>[!IMPORTANT]
> Beim Empfang von E-Mails kann es Verzögerungen geben, und in einigen Fällen kann es bis zu 24 Stunden dauern, bis E-Mails ankommen. Fügen Sie Ihrer Liste der sicheren Empfänger azure-noreply@microsoft.com hinzu, um sicherzustellen, dass Sie alle E-Mails empfangen.

1. Achten Sie auf eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Hier sehen Sie ein Beispiel für eine E-Mail.

   ![Screenshot mit einer Beispiel-E-Mail von Microsoft, in der Sie aufgefordert werden, den Zugriff auf eine Gruppe zu überprüfen.](./media/self-access-review/access-review-email-preview.png)

1. Klicken Sie auf **Zugriff überprüfen**, um die Zugriffsüberprüfung zu öffnen.

1. Fahren Sie dann mit dem Abschnitt **Durchführen der Zugriffsüberprüfung** fort.

### <a name="use-my-access"></a>Mit „Mein Zugriff“

Sie können Ihre ausstehenden Zugriffsüberprüfungen auch anzeigen, indem Sie **Mein Zugriff** in Ihrem Browser öffnen.

1. Melden Sie sich unter [Mein Zugriff](https://myaccess.microsoft.com/) an.

1. Wählen Sie im Menü auf der linken Navigationsleiste **Zugriffsüberprüfungen** aus, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen, die Ihnen zugewiesen sind.

   ![Screenshot: „Zugriffsüberprüfungen“ im Menü.](./media/self-access-review/access-review-menu.png)

## <a name="do-the-access-review"></a>Überprüfen des Zugriffs

1. Unter **Gruppen und Apps** wird Folgendes angezeigt:

    - **Name**: Der Name der Zugriffsüberprüfung.
    - **Fällig**: Das Fälligkeitsdatum der Überprüfung. Nach diesem Datum können abgelehnte Benutzer aus der zu überprüfenden Gruppe oder App entfernt werden.
    - **Ressource**: Der Name der zu überprüfenden Ressource.
    - **Fortschritt**: Die Anzahl der überprüften Benutzer in Relation zur Gesamtzahl der Benutzer in dieser Zugriffsüberprüfung.

1. Klicken Sie zum Starten des Vorgangs auf den Namen einer Zugriffsüberprüfung.

   ![Screenshot mit einer Liste der ausstehenden Zugriffsüberprüfungen für Apps und Gruppen.](./media/self-access-review/access-reviews-list-preview.png)

1. Überprüfen Sie Ihren Zugriff, und entscheiden Sie, ob Sie weiterhin Zugriff benötigen.

    Wenn Sie aufgefordert werden, den Zugriff für andere Benutzer zu überprüfen, sieht die Seite anders aus. Weitere Informationen finden Sie unter [Zugriffsüberprüfung von Gruppen oder Anwendungen](perform-access-review.md).

    ![Screenshot: Offene Zugriffsüberprüfung, in der Sie gefragt werden, ob Sie weiterhin Zugriff auf eine Gruppe benötigen.](./media/self-access-review/review-access-preview.png)

1. Wählen Sie **Ja** aus, um Ihren Zugriff zu behalten, oder wählen Sie **Nein** aus, um Ihren Zugriff zu entfernen.

1. Wenn Sie **Ja** auswählen, müssen Sie möglicherweise im Feld **Grund** eine Begründung angeben.

    ![Screenshot, auf dem „Ja“ ausgewählt wurde, um den Zugriff auf eine Gruppe zu behalten.](./media/self-access-review/review-access-yes-preview.png)

1. Klicken Sie auf **Submit** (Senden).

    Ihre Auswahl wird übermittelt, und Sie gelangen wieder zur Seite **Mein Zugriff**.

    Wenn Sie Ihre Antwort ändern möchten, öffnen Sie erneut die Seite **Zugriffsüberprüfungen**, und aktualisieren Sie Ihre Antwort. Bis zum Abschluss der Zugriffsüberprüfung können Sie Ihre Entscheidung jederzeit ändern.

    > [!NOTE]
    > Wenn Sie angegeben haben, dass Sie keinen Zugriff mehr benötigen, werden Sie nicht sofort entfernt. Sie werden nach Ende der Überprüfung (oder wenn ein Administrator die Überprüfung beendet) entfernt.

## <a name="next-steps"></a>Nächste Schritte

[Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)
