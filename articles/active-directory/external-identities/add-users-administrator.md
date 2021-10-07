---
title: 'Azure AD: Hinzufügen von B2B-Zusammenarbeitsbenutzern im Azure-Portal'
description: Hier wird gezeigt, wie ein Administrator seinem Verzeichnis unter Verwendung von Azure AD B2B-Zusammenarbeit (Azure Active Directory) Gastbenutzer aus einer Partnerorganisation hinzufügen kann.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/10/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa19fffae56825b6ed9a5ca7f39abbcf2999d6e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780735"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal

Als Benutzer mit einer der eingeschränkten Administratorverzeichnisrollen können Sie B2B-Zusammenarbeitsbenutzer über das Azure-Portal einladen. Gastbenutzer können zu einem Verzeichnis, zu einer Gruppe oder zu einer Anwendung eingeladen werden. Nachdem Sie einen Benutzer mithilfe einer der verfügbaren Methoden eingeladen haben, wird das Konto des eingeladenen Benutzers in Azure Active Directory (Azure AD) mit dem Benutzertyp *Gast* hinzugefügt. Der Gastbenutzer muss daraufhin seine Einladung annehmen, um auf Ressourcen zugreifen zu können. Die Einladung eines Benutzers läuft nicht ab.

Nachdem Sie dem Verzeichnis einen Gastbenutzer hinzugefügt haben, können Sie dem Gastbenutzer einen direkten Link zu einer freigegebenen App senden, oder der Gastbenutzer kann auf die Einlösungs-URL in der Einladungs-E-Mail klicken. Weitere Informationen zum Annahmevorgang finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md).

> [!IMPORTANT]
> Sie sollten die Schritte in [ Hinzufügen der Datenschutzinformationen Ihrer Organisation in Azure Active Directory](../fundamentals/active-directory-properties-area.md) ausführen. Im Rahmen der erstmaligen Annahme einer Einladung muss ein eingeladener Benutzer Ihren Datenschutzbestimmungen zustimmen, um fortfahren zu können. 

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie sicher, dass die Einstellungen für externe Zusammenarbeit Ihrer Organisation so konfiguriert sind, dass Sie Gäste einladen dürfen. Standardmäßig können alle Benutzer und Administratoren Gäste einladen. Die Richtlinien für externe Zusammenarbeit Ihrer Organisation können jedoch so konfiguriert sein, dass bestimmte Benutzer oder Administratoren keine Gäste einladen dürfen. Informationen dazu, wie Sie diese Richtlinien anzeigen und festlegen, finden Sie unter [Delegieren von Einladungen zur Azure Active Directory B2B-Zusammenarbeit](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Hinzufügen von Gastbenutzern zum Verzeichnis

Gehen Sie wie folgt vor, um B2B-Zusammenarbeitsbenutzer dem Verzeichnis hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Benutzer an, dem eine eingeschränkte Azure AD-Administratorrolle oder die Rolle „Gasteinladender“ zugewiesen ist.
2. Suchen Sie auf einer beliebigen Seite nach **Azure Active Directory**, und wählen Sie es aus.
3. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
4. Klicken Sie auf **Neuer Gastbenutzer**.
5. Wählen Sie auf der Seite **Neuer Benutzer** die Option **Benutzer einladen** aus, und fügen Sie dann die Informationen des Gastbenutzers hinzu.

   ![Abbildung des Gastbenutzertyps](media/add-users-administrator/invite-user.png)

      - **Name**. Der Vor- und Nachname des Gastbenutzers.
   - **E-Mail-Adresse (erforderlich)** . Die E-Mail-Adresse des Gastbenutzers.
   - **Persönliche Nachricht (optional)** : Geben Sie eine persönliche Willkommensnachricht an den Gastbenutzer ein.
   - **Gruppen**: Sie können den Gastbenutzer jetzt oder später einer oder mehreren vorhandenen Gruppen hinzufügen.
   -  **Rollen:** Wenn Sie Azure AD-Administratorberechtigungen für den Benutzer benötigen, können Sie diese einer Azure AD-Rolle hinzufügen, indem Sie neben **Rollen** auf **Benutzer** klicken.  [Erfahren Sie mehr](../../role-based-access-control/role-assignments-external-users.md) über Azure-Rollen für externe Gastbenutzer.

   > [!NOTE]
   > Gruppen-E-Mail-Adressen werden nicht unterstützt. Geben Sie die E-Mail-Adresse einer Einzelperson ein. Einige E-Mail-Anbieter erlauben Benutzern auch, ihren E-Mail-Adressen ein Pluszeichen (+) und zusätzlichen Text hinzuzufügen. Dies kann beispielsweise beim Filtern im Posteingang nützlich sein. Allerdings unterstützt Azure AD derzeit keine Pluszeichen in E-Mail-Adressen. Um Probleme bei der Übermittlung zu vermeiden, lassen Sie das Pluszeichen und alle nachfolgenden Zeichen bis zum @-Symbol weg.
6. Klicken Sie auf **Einladen**. Die Einladung wird daraufhin automatisch an den Gastbenutzer gesendet. 
 
Nach dem Senden der Einladung wird das Benutzerkonto dem Verzeichnis automatisch als Gast hinzugefügt.

 ![Abbildung zur Nutzung als Gastbenutzer](media/add-users-administrator//guest-user-type.png)

## <a name="add-guest-users-to-a-group"></a>Hinzufügen von Gastbenutzern zu einer Gruppe
Gehen Sie wie folgt vor, um einer Gruppe Benutzer für die B2B-Zusammenarbeit manuell hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Suchen Sie auf einer beliebigen Seite nach **Azure Active Directory**, und wählen Sie es aus.
3. Wählen Sie unter **Verwalten** die Option **Gruppen** aus.
4. Wählen Sie eine Gruppe aus, oder klicken Sie auf **Neue Gruppe**, um eine neue Gruppe zu erstellen. Es empfiehlt sich, in der Gruppenbeschreibung anzugeben, dass die Gruppe B2B-Gastbenutzer enthält.
5. Klicken Sie unter **Verwalten** auf **Mitglieder**.
6. Wählen Sie **Mitglieder hinzufügen** aus.
7. Führen Sie eines der folgenden Verfahren aus:

   - *Für den Fall, dass Gastbenutzer*innen bereits im Verzeichnis vorhanden sind:*
      
       a. Beginnen Sie auf der Seite **Mitglieder hinzufügen** mit der Eingabe des Namens oder der E-Mail-Adresse von Gastbenutzer*innen.
       
       b. Wählen Sie die Benutzerin bzw. den Benutzer in den Suchergebnissen aus, und klicken Sie dann auf **Auswählen**.

   - *Für den Fall, dass Gastbenutzer*innen noch nicht im Verzeichnis vorhanden sind:*
      
       a. Geben Sie im Suchfeld auf der Seite **Mitglieder hinzufügen** die vollständige E-Mail-Adresse von Benutzer*innen ein, formulieren Sie eine **persönliche Nachricht** (optional), und klicken Sie dann auf **Einladen**, um eine Einladung zu senden.
       
       b. Klicken Sie auf **Auswählen**.
       
       c. Fügen Sie der Gruppe dann Benutzer*innen hinzu: Klicken Sie auf der Seite **Mitglieder** auf **Mitglieder hinzufügen**. Geben Sie im Suchfeld den Namen oder die E-Mail-Adresse der Benutzer*innen ein, wählen Sie diese in den Ergebnissen aus, und klicken Sie dann auf **Auswählen**.

Für Azure AD B2B-Zusammenarbeit können auch dynamische Gruppen verwendet werden. Weitere Informationen finden Sie unter [Dynamische Gruppen und Azure Active Directory B2B-Zusammenarbeit](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Hinzufügen von Gastbenutzern zu einer Anwendung

Gehen Sie wie folgt vor, um einer Anwendung Benutzer für die B2B-Zusammenarbeit hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Suchen Sie auf einer beliebigen Seite nach **Azure Active Directory**, und wählen Sie es aus.
3. Wählen Sie unter **Verwalten****Unternehmensanwendungen** aus.
4. Wählen Sie auf der Seite **Alle Anwendungen** die Anwendung aus, der Sie Gastbenutzer hinzufügen möchten.
5. Klicken Sie unter **Verwalten** auf **Benutzer und Gruppen**.
6. Wählen Sie **Benutzer/Gruppe hinzufügen** aus.
7. Klicken Sie auf der Seite **Zuweisung hinzufügen** auf den Link unter **Benutzer**.
8. Führen Sie eines der folgenden Verfahren aus:

   - *Für den Fall, dass Gastbenutzer*innen bereits im Verzeichnis vorhanden sind:*
      
       a. Beginnen Sie auf der Seite **Benutzer** mit der Eingabe des Namens oder der E-Mail-Adresse von Gastbenutzer*innen.
      
       b. Wählen Sie die Benutzerin bzw. den Benutzer in den Suchergebnissen aus, und klicken Sie dann auf **Auswählen**.
       
       c. Klicken Sie auf der Seite **Zuweisung hinzufügen** auf **Zuweisen**, um der App Benutzer*innen hinzuzufügen.

   - *Für den Fall, dass Gastbenutzer*innen noch nicht im Verzeichnis vorhanden sind:*
      
       a. Geben Sie im Suchfeld auf der Seite **Benutzer** die vollständige E-Mail-Adresse von Benutzer*innen ein, formulieren Sie eine **persönliche Nachricht** (optional), und klicken Sie dann auf **Einladen**.
       
       b. Klicken Sie auf **Auswählen**.
       
       c. Fügen Sie der Anwendung nun eingeladene Benutzer*innen hinzu: Klicken Sie auf der Seite **Zuweisung hinzufügen** auf den Link unter **Benutzer**. Geben Sie im Suchfeld den Namen oder die E-Mail-Adresse der eingeladenen Benutzer*innen ein, wählen Sie diese in den Ergebnissen aus, und klicken Sie dann auf **Auswählen**.
       
       d. Klicken Sie auf der Seite **Zuweisung hinzufügen** auf **Zuweisen**.

9. Der Gastbenutzer wird in der Anwendungsliste **Benutzer und Gruppen** mit der zugewiesenen Rolle **Standardzugriff** angezeigt. Wenn die Anwendung unterschiedliche Rollen bietet und Sie die Rolle des Benutzers ändern möchten, gehen Sie wie folgt vor:

    a. Aktivieren Sie das Kontrollkästchen neben dem Gastbenutzer, und klicken Sie dann auf die Schaltfläche **Bearbeiten**. 
    
    b. Klicken Sie auf der Seite **Zuweisung bearbeiten** auf den Link unter **Rolle auswählen**, und wählen Sie die Rolle aus, die Sie dem Benutzer zuweisen möchten.
    
    c. Klicken Sie auf **Auswählen**.
    
    d. Wählen Sie **Zuweisen** aus.

## <a name="resend-invitations-to-guest-users"></a>Erneutes Senden von Einladungen an Gastbenutzer

Wenn ein Gastbenutzer seine Einladung noch nicht angenommen hat, können Sie die Einladungs-E-Mail erneut senden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Suchen Sie auf einer beliebigen Seite nach **Azure Active Directory**, und wählen Sie es aus.
3. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
4. Wählen Sie das Benutzerkonto aus.
5. Klicken Sie im Abschnitt **Identität** unter **Einladung akzeptiert** auf den Link **(Verwalten)** . 
6. Wenn der Benutzer die Einladung noch nicht angenommen hat, klicken Sie auf die Option **Ja**, um die Einladung erneut zu senden.

    ![Erneutes Senden einer Einladung](./media/add-users-administrator/resend-invitation.png)

> [!NOTE]
> Eine Einladungs-URL wird generiert.

## <a name="next-steps"></a>Nächste Schritte

- Unter [Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer zu Azure Active Directory hinzu?](add-users-information-worker.md) erfahren Sie, wie Benutzer ohne Azure AD-Administratorrolle B2B-Gastbenutzer hinzufügen können.
- Informationen zur Einladungs-E-Mail finden Sie unter [Die Elemente der Einladungs-E-Mail für die B2B-Zusammenarbeit – Azure Active Directory](invitation-email-elements.md).
