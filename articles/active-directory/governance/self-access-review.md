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
ms.openlocfilehash: ff7c9d0e24eefb1c615f45f0aa33f5a1fe0af760
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095494"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Selbstüberprüfung von Zugriffspaketen und Ressourcen mit der Azure AD-Berechtigungsverwaltung

Die Azure AD-Berechtigungsverwaltung erleichtert Unternehmen die Verwaltung des Zugriffs auf Gruppen, Anwendungen und SharePoint-Websites. In diesem Artikel wird beschrieben, wie ein Benutzer eine Selbstüberprüfung der ihm zugewiesenen Zugriffspakete durchführt.

## <a name="review-your-own-access-using-my-access"></a>Überprüfen des eigenen Zugriffs mithilfe von „Mein Zugriff“

Sie können Ihren eigenen Zugriff auf eine Gruppe, eine Anwendung oder ein Zugriffspaket auf die nachfolgend beschriebenen beiden Arten überprüfen:

### <a name="email"></a>Email

>[!IMPORTANT]
> Beim Empfang von E-Mails kann es Verzögerungen geben, und in einigen Fällen kann es bis zu 24 Stunden dauern. Fügen Sie Ihrer Liste der sicheren Empfänger azure-noreply@microsoft.com hinzu, um sicherzustellen, dass Sie alle E-Mails empfangen.

1. Achten Sie auf eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Nachstehend finden Sie ein Beispiel für eine E-Mail-Nachricht:

 ![Beispiel-E-Mail von Microsoft zum Überprüfen des Zugriffs auf eine Gruppe](./media/self-access-review/access-review-email-preview.png)

2. Klicken Sie auf den Link **Zugriff überprüfen**, um die Zugriffsüberprüfung zu öffnen.

3. Weiter mit dem Abschnitt **Durchführen der Zugriffsüberprüfung**

### <a name="directly-at-my-access"></a>Direkt über „Mein Zugriff“

Sie können Ihre ausstehenden Zugriffsüberprüfungen auch anzeigen, indem Sie Ihren Browser zum Öffnen von „Mein Zugriff“ verwenden.

1. Anmelden auf https://myaccess.microsoft.com/ bei „Mein Zugriff“

2. Wählen Sie im Menü auf der linken Navigationsleiste **Zugriffsüberprüfungen** aus, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen, die Ihnen zugewiesen sind.

   ![Zugriffsüberprüfungen im Menü](./media/self-access-review/access-review-menu.png)

## <a name="perform-the-access-review"></a>Durchführen der Zugriffsüberprüfung

1. Unter „Gruppen und Apps“ wird Folgendes angezeigt:
    
    - **Name**: Der Name der Zugriffsüberprüfung.
    - **Fällig**: Das Fälligkeitsdatum der Überprüfung. Nach diesem Datum können abgelehnte Benutzer aus der zu überprüfenden Gruppe oder App entfernt werden.
    - **Ressource**: Der Name der zu überprüfenden Ressource.
    - **Fortschritt**: Die Anzahl von überprüften Benutzern in Relation zur Gesamtzahl der Benutzer im Rahmen dieser Zugriffsüberprüfung.
    
2. Klicken Sie zum Starten des Vorgangs auf den Namen einer Zugriffsüberprüfung.

   ![Liste mit ausstehenden Zugriffsüberprüfungen für Apps und Gruppen](./media/self-access-review/access-reviews-list-preview.png)

3. Überprüfen Sie Ihren Zugriff, und entscheiden Sie, ob Sie weiterhin Zugriff benötigen.

    Wenn Sie aufgefordert werden, den Zugriff für andere Benutzer zu überprüfen, sieht die Seite anders aus. Weitere Informationen finden Sie unter [Zugriffsüberprüfung von Gruppen oder Anwendungen](perform-access-review.md).

    ![Offene Zugriffsüberprüfung, in der Sie gefragt werden, ob Sie weiterhin Zugriff auf eine Gruppe benötigen](./media/self-access-review/review-access-preview.png)

1. Wählen Sie **Ja** aus, um Ihren Zugriff beizubehalten, oder wählen Sie **Nein** aus, um Ihren Zugriff zu entfernen.

1. Wenn Sie auf **Ja** klicken, müssen Sie möglicherweise im Feld **Grund** eine Begründung angeben.

    ![Abgeschlossene Zugriffsüberprüfung, in der Sie gefragt werden, ob Sie weiterhin Zugriff auf eine Gruppe benötigen](./media/self-access-review/review-access-yes-preview.png)

1. Klicken Sie auf **Submit**(Senden).

    Ihre Auswahl wird übermittelt, und Sie gelangen wieder zur Seite „Mein Zugriff“.

    Wenn Sie Ihre Antwort ändern möchten, öffnen Sie erneut die Seite „Zugriffsüberprüfungen“, und aktualisieren Sie Ihre Antwort. Bis zum Abschluss der Zugriffsüberprüfung können Sie Ihre Entscheidung jederzeit ändern.

    > [!NOTE]
    > Wenn Sie angegeben haben, dass Sie keinen Zugriff mehr benötigen, werden Sie nicht sofort entfernt. Sie werden nach Ende der Überprüfung (oder wenn ein Administrator die Überprüfung beendet) entfernt.

## <a name="next-steps"></a>Nächste Schritte

- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)


