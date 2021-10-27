---
title: Unterstützung für Azure Active Directory B2C
description: Senden von Supportanfragen für Azure Active Directory B2C
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: a921ce8b4f8a2693cba1289dcf24107c1c03f6a9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043109"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Senden von Supportanfragen
Sie können im Azure-Portal Supportanfragen für Azure Active Directory B2C (Azure AD B2C) senden, indem Sie die folgenden Schritte ausführen:

1. Wechseln Sie aus Ihrem B2C-Mandanten in einen anderen Mandanten, dem ein Azure-Abonnement zugeordnet ist. Dies ist normalerweise Ihr Mitarbeitermandant oder der Standardmandant, der bei der Registrierung für ein Azure-Abonnement erstellt wurde. Weitere Informationen finden Sie unter [Beziehung zwischen einem Azure-Abonnement und Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Azure-Portal mit hervorgehobener Mandantenauswahl](./media/support-options/support-switch-dir.png)

1. Klicken Sie nach dem Wechsel von Mandanten auf **Hilfe und Support**.

    ![Die Kachel „Hilfe und Support“, hervorgehoben im Azure-Portal](./media/support-options/support-support.png)

1. Klicken Sie auf **Neue Supportanfrage**.

    ![Neue Kachel „Supportanfrage“, hervorgehoben im Azure-Portal](./media/support-options/support-new.png)

1. Verwenden Sie diese Informationen auf dem Blatt **Grundlagen**, und klicken Sie auf **Weiter**.

    * Wählen Sie für **Problemtyp** den Eintrag **Technisch** aus.
    * Wählen Sie das entsprechende **Abonnement**.
    * **Dienst** ist **Active Directory**.
    * Wählen Sie den entsprechenden **Supportplan**. Wenn Sie keinen besitzen, können Sie sich [hier](https://azure.microsoft.com/support/plans/)für einen Supportplan registrieren.

     ![Seite mit grundlegenden Angaben mit hervorgehobener Schaltfläche „Weiter“ im Azure-Portal](./media/support-options/support-basics.png)

1. Verwenden Sie diese Informationen auf dem Blatt **Problem**, und klicken Sie auf **Weiter**.

    * Wählen Sie den entsprechenden **Schweregrad** .
    * Der **Problemtyp** lautet **B2C**.
    * Wählen Sie die entsprechende **Kategorie**.
    * Beschreiben Sie Ihr Problem im Feld **Details**. Geben Sie Details wie den Namen des B2C-Mandanten, eine Beschreibung des Problems, Fehlermeldungen, Korrelations-IDs (falls verfügbar) usw. an.
    * Geben Sie im Feld **Zeitrahmen** durch Datum und Uhrzeit (einschließlich Zeitzone) an, wann das Problem aufgetreten ist.
    * Laden Sie unter **Dateiupload** alle Screenshots und Dateien hoch, die Ihrer Meinung nach bei der Behebung des Problems hilfreich sein könnten.

     ![Problemseite mit hervorgehobener Schaltfläche „Weiter“ im Azure-Portal](./media/support-options/support-problem.png)

1. Fügen Sie auf dem Blatt **Kontaktinformationen** Ihre Kontaktinformationen hinzu. Klicken Sie auf **Erstellen**.

    ![Seite mit den Kontaktinformationen mit hervorgehobener Schaltfläche „Erstellen“ im Azure-Portal](./media/support-options/support-contact.png)

1. Nachdem Ihre Supportanfrage übermittelt wurde, können Sie sie überwachen, indem Sie im Startmenü auf **Hilfe und Support** und dann auf **Supportanfragen verwalten** klicken.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Bekanntes Problem: Senden von Supportanfragen im Kontext eines B2C-Mandanten

Wenn Sie den oben beschriebenen Schritt 2 nicht durchführen und versuchen, eine Supportanfrage im Kontext Ihres B2C-Mandanten zu erstellen, wird der folgende Fehler angezeigt.

> [!IMPORTANT]
> Versuchen Sie nicht, sich in Ihrem B2C-Mandanten für ein neues Azure-Abonnement zu registrieren.

![Im Azure-Portal wird kein Abonnementfehler angezeigt.](./media/support-options/support-no-sub.png)
