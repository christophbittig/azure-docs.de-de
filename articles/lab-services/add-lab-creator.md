---
title: Hinzufügen von Benutzern als Lab-Ersteller in Azure Lab Services
description: In diesem Artikel erfahren Sie, wie Sie einen Benutzer der Rolle „Lab-Ersteller“ für ein Labkonto in Azure Lab Services hinzufügen. Die Lab-Ersteller können Labs in diesem Labkonto erstellen.
ms.topic: article
ms.date: 07/26/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: a2135ab6580d39d6c63f7e948a29f0f0ede4efd6
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634193"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Hinzufügen von Lab-Erstellern zu einem Labkonto in Azure Lab Services
In diesem Artikel erfahren Sie, wie Sie Benutzer als Lab-Ersteller zu einem Labkonto in Azure Lab Services hinzufügen. Diese Benutzer können dann Labs im Labkonto erstellen. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Hinzufügen eines Microsoft-Benutzerkontos zur Rolle „Lab-Ersteller“
Zum Einrichten eines Classroom-Labs in einem Labkonto muss der Benutzer Mitglied der Rolle **Ersteller des Labs** für das Labkonto sein. Das zum Erstellen des Labkontos verwendete Konto wird dieser Rolle automatisch hinzugefügt. Wenn Sie zum Erstellen eines Classroom-Labs das gleiche Benutzerkonto verwenden möchten, können Sie diesen Schritt überspringen. Führen Sie die folgenden Schritte aus, um zum Erstellen eines Classroom-Labs ein anderes Benutzerkonto zu verwenden: 

Um Lehrkräften die Berechtigungzu geben, Labs für ihre Klassen zu erstellen, fügen Sie ihnen die Rolle **Lab Autor** hinzu: Detaillierte Schritte finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).


1. Wählen Sie auf der Seite **Lab Konto** **Zugriffssteuerung (IAM)**

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** .

    ![Seite „Zugriffssteuerung (IAM)“ mit geöffnetem Menü „Rollenzuweisung hinzufügen“.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Wählen Sie auf der Registerkarte **Rolle** die Rolle **Lab Autor**.

    ![Seite „Rollenzuweisung hinzufügen“ mit ausgewählter Registerkarte „Rolle“.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Wählen Sie auf der Registerkarte **Members** den Benutzer, den Sie der Rolle Lab Autors hinzufügen möchten

1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.




    > [!NOTE]
    > Wenn Sie einen Benutzer ohne Microsoft-Konto als Lab-Ersteller hinzufügen, finden Sie weitere Informationen im Abschnitt [Hinzufügen eines Benutzers ohne Microsoft-Konto als Lab-Ersteller](#add-a-non-microsoft-account-user-as-a-lab-creator). 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Hinzufügen eines Benutzers ohne Microsoft-Konto als Lab-Ersteller
Wenn Sie einen Benutzer als Lab-Ersteller hinzufügen möchten, verwenden Sie sein E-Mail-Konto. Die folgenden Arten von E-Mail-Konten können verwendet werden:

- Ein E-Mail-Konto, das von der AAD-Instanz (Azure Active Directory) Ihrer Universität bereitgestellt wird
- Ein Microsoft-E-Mail-Konto, z. B. `@outlook.com`, `@hotmail.com`, `@msn.com` oder `@live.com`
- Ein Microsoft-fremdes E-Mail-Konto, z. B. ein von Yahoo oder Google bereitgestelltes. Diese Kontotypen müssen jedoch mit einem Microsoft-Konto verknüpft werden.
- Ein GitHub-Konto. Dieses Konto muss mit einem Microsoft-Konto verknüpft werden.

### <a name="using-a-non-microsoft-email-account"></a>Verwenden eines Microsoft-fremden E-Mail-Kontos
Lab-Ersteller/Kursleiter können Microsoft-fremde E-Mail-Konten verwenden, um sich für ein Classroom-Lab zu registrieren und sich bei diesem anzumelden.  Die Anmeldung beim Lab-Dienst-Portal erfordert jedoch, dass die Kursleiter zunächst ein Microsoft-Konto erstellen, das mit ihrer Microsoft-fremden E-Mail-Adresse verknüpft ist.

Viele Kursleiter verfügen möglicherweise bereits über ein Microsoft-Konto, das mit ihren Microsoft-fremden E-Mail-Adressen verknüpft ist. Beispielsweise verfügen Kursleiter bereits über ein Microsoft-Konto, wenn sie ihre E-Mail-Adresse mit anderen Microsoft-Produkten oder -Diensten wie Office, Skype, OneDrive oder Windows verwendet haben.  

Wenn sich ein Kursleiter beim Lab-Dienst-Portal anmeldet, wird er aufgefordert, seine E-Mail-Adresse und sein Kennwort einzugeben. Wenn der Kursleiter versucht, sich mit einem Microsoft-fremden Konto anzumelden, das nicht mit einem Microsoft-Konto verknüpft ist, erhält er die folgende Fehlermeldung: 

![Fehlermeldung](./media/how-to-configure-student-usage/cant-find-account.png)

Um sich für ein Microsoft-Konto anzumelden, sollten Kursleiter [http://signup.live.com](http://signup.live.com) besuchen.  


### <a name="using-a-github-account"></a>Verwenden eines GitHub-Kontos
Kursleiter können auch ein vorhandenes GitHub-Konto verwenden, um sich für ein Classroom-Lab zu registrieren und bei diesem anzumelden. Wenn der Kursleiter bereits über ein Microsoft-Konto verfügt, das mit seinem GitHub-Konto verknüpft ist, kann er sich anmelden und sein Kennwort angeben, wie im vorherigen Abschnitt gezeigt. Wenn er sein GitHub-Konto noch nicht mit einem Microsoft-Konto verknüpft hat, sollte er **Anmeldeoptionen** auswählen:

![Link „Anmeldeoptionen“](./media/how-to-configure-student-usage/signin-options.png)

Auf der Seite **Anmeldeoptionen** wählt er die Option **Mit GitHub anmelden** aus.

![Link „Mit GitHub anmelden“](./media/how-to-configure-student-usage/signin-github.png)

Schließlich wird er aufgefordert, ein Microsoft-Konto zu erstellen, das mit seinem GitHub-Konto verknüpft ist. Dies geschieht automatisch, wenn der Kursleiter **Weiter** auswählt.  Anschließend wird der Kursleiter sofort angemeldet und mit dem Classroom-Lab verbunden.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Einrichten und Veröffentlichen von Vorlagen als Labbesitzer](how-to-create-manage-template.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
- [Zugreifen auf Labs als Labbenutzer](how-to-use-classroom-lab.md)
