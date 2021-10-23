---
title: 'Tutorial: Konfigurieren von Rollen in Azure Analysis Services | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Administrator- und Benutzerrollen von Azure Analysis Services über das Azure-Portal oder das SQL Server Management Studio konfigurieren.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 21742dbc4fa41cca4dadf2871d7851a526807e48
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993357"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Tutorial: Konfigurieren von Serveradministrator- und Benutzerrollen

 In diesem Tutorial stellen Sie mithilfe von SQL Server Management Studio (SSMS) eine Verbindung mit Ihrem Server in Azure her, um Serveradministrator- und Modelldatenbankrollen zu konfigurieren. Darüber hinaus finden Sie hier eine Einführung in [TMSL (Tabular Model Scripting Language)](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). TMSL ist eine auf JSON basierende Skriptsprache für Tabellenmodelle mit einem Kompatibilitätsgrad von mindestens 1200. Sie kann zum Automatisieren zahlreicher Tabellenmodellierungsaufgaben verwendet werden. TMSL wird häufig mit PowerShell verwendet. In diesem Tutorial verwenden Sie jedoch den XMLA-Abfrage-Editor in SSMS. Sie führen im Rahmen dieses Tutorials folgende Aufgaben aus: 
  
> [!div class="checklist"]
> * Abrufen Ihres Servernamens über das Portal
> * Herstellen einer Verbindung mit Ihrem Server mithilfe von SSMS
> * Hinzufügen eines Benutzers oder einer Gruppe zur Serveradministratorrolle 
> * Hinzufügen eines Benutzers oder einer Gruppe zur Modelldatenbank-Administratorrolle
> * Hinzufügen einer neuen Modelldatenbankrolle sowie eines Benutzers oder einer Gruppe

Informationen zur Benutzersicherheit in Azure Analysis Services finden Sie unter [Authentifizierung und Benutzerberechtigungen](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Voraussetzungen

- Eine Azure Active Directory-Instanz in Ihrem Abonnement
- Erstellung eines [Azure Analysis Services-Servers](../analysis-services-create-server.md) in Ihrem Abonnement
- [Serveradministratorberechtigungen](../analysis-services-server-admins.md)
- [Hinzufügen des Beispielmodells „adventureworks“](../analysis-services-create-sample-model.md) zu Ihrem Server
- [Installieren der aktuellen Version von SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Portal](https://portal.azure.com/) an.

## <a name="get-server-name"></a>Abrufen des Servernamens
Sie benötigen den Servernamen, um über SSMS eine Verbindung mit Ihrem Server herstellen zu können. Den Servernamen können Sie über das Portal abrufen.

Kopieren Sie den Servernamen über **Azure-Portal** > Server > **Übersicht** > **Servername**.
   
   ![Abrufen von Servernamen in Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Herstellen der Verbindung in SSMS

In den übrigen Aufgaben verwenden Sie SSMS, um eine Verbindung mit Ihrem Server herzustellen und den Server zu verwalten.

1. Klicken Sie in SSMS im **Objekt-Explorer** auf **Verbinden** > **Analysis Services**.

    ![Verbinden](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. Fügen Sie im Dialogfeld **Mit Server verbinden** unter **Servername** den aus dem Portal kopierten Servernamen ein. Wählen Sie unter **Authentifizierung** die Option **Active Directory: universell mit MFA-Unterstützung** aus, geben Sie Ihr Benutzerkonto ein, und klicken Sie dann auf **Verbinden**.
   
    ![Herstellen der Verbindung in SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > Die Auswahl von „Active Directory: universell mit MFA-Unterstützung“ wird empfohlen. Dieser Authentifizierungstyp unterstützt eine [nicht interaktive mehrstufige Authentifizierung](../../azure-sql/database/authentication-mfa-ssms-overview.md). 

3. Erweitern Sie zum Anzeigen von Serverobjekten den **Objekt-Explorer**. Klicken Sie mit der rechten Maustaste auf ein Objekt, um Servereigenschaften anzuzeigen.
   
    ![Objekt-Explorer in SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Hinzufügen eines Benutzerkontos zur Serveradministratorrolle

In dieser Aufgabe fügen Sie ein Benutzer- oder Gruppenkonto aus Azure AD zur Serveradministratorrolle hinzu. Verwenden Sie beim Angeben einer Sicherheitsgruppe `obj:groupid@tenantid`.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Servernamen, und klicken Sie dann auf **Eigenschaften**. 
2. Klicken Sie im Fenster **Eigenschaften für Analysis-Server** auf **Sicherheit** > **Hinzufügen**.
3. Geben Sie im Fenster **Benutzer oder Gruppe auswählen** ein Benutzer- oder Gruppenkonto Ihrer Azure AD-Instanz ein, und klicken Sie auf **Hinzufügen**. 
   
     ![Hinzufügen eines Serveradministrators](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Klicken Sie auf **OK**, um **Eigenschaften für Analysis-Server** zu schließen.

    > [!TIP]
    > Sie können mit **Analysis Services-Administratoren** im Portal auch Serveradministratoren hinzufügen. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Hinzufügen eines Benutzers zur Modelldatenbank-Administratorrolle

In dieser Aufgabe fügen Sie ein Benutzer- oder Gruppenkonto zur Rolle „Internet Sales Administrator“ (Administrator für Internetverkäufe) hinzu, die bereits im Modell vorhanden ist. Diese Rolle verfügt über Vollzugriffsberechtigungen (Administrator) für die Beispielmodelldatenbank „adventureworks“. In dieser Aufgabe wird der TMSL-Befehl [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) in einem für Sie erstellten Skript verwendet.

1. Erweitern Sie im **Objekt-Explorer** die Option **Datenbanken** > **adventureworks** > **Rollen**. 
2. Klicken Sie mit der rechten Maustaste auf **Internet Sales Administrator** (Administrator für Internetverkäufe), und klicken Sie dann auf **Script Role as** (Rolle skripten als) > **CREATE OR REPLACE To** (ERSTELLEN ODER ERSETZEN:) > **Neues Abfrage-Editor-Fenster**.

    ![Neues Abfrage-Editor-Fenster](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. Ändern Sie in **XMLAQuery** den Wert für **"memberName":** in ein Benutzer- oder Gruppenkonto in Ihrer Azure AD-Instanz. Das Konto, mit dem Sie angemeldet sind, ist standardmäßig enthalten. Sie müssen Ihr eigenes Konto jedoch nicht hinzufügen, da Sie bereits Serveradministrator sind.

    ![TMSL-Skript in XMLA-Abfrage](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Drücken Sie **F5**, um das Skript auszuführen.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Hinzufügen einer neuen Modelldatenbankrolle sowie eines Benutzers oder einer Gruppe

In dieser Aufgabe verwenden Sie den [Create](/analysis-services/tmsl/create-command-tmsl)-Befehl in einem TMSL-Skript, um die neue Rolle „Internet Sales Global“ (Internetverkäufe global) zu erstellen, Leseberechtigungen (*read*) für die Rolle anzugeben und ein Benutzer- oder Gruppenkonto aus Ihrer Azure AD-Instanz hinzuzufügen.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf **adventureworks** und dann auf **Neue Abfrage** > **XMLA**. 
2. Kopieren Sie das folgende TMSL-Skript, und fügen Sie es im Abfrage-Editor ein:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Ändern Sie den Wert des Objekts `"memberName": "globalsales@adventureworks.com"` in ein Benutzer- oder Gruppenkonto in Ihrer Azure AD-Instanz.
4. Drücken Sie **F5**, um das Skript auszuführen.

## <a name="verify-your-changes"></a>Überprüfen Ihrer Änderungen

1. Klicken Sie im **Objekt-Explorer** auf den Servernamen und anschließend auf **Aktualisieren**. Drücken Sie alternativ **F5**.
2. Erweitern Sie **Datenbanken** > **adventureworks** > **Rollen**. Überprüfen Sie, ob die in der vorherigen Aufgabe hinzugefügten Änderungen für Benutzerkonto und neue Rolle angezeigt werden.   

    ![Überprüfen im Objekt-Explorer](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Benutzer- oder Gruppenkonten bzw. Benutzer- oder Gruppenrollen nicht mehr benötigt wird, löschen Sie sie. Klicken Sie hierzu auf **Rolleneigenschaften** > **Mitgliedschaft**, um Benutzerkonten zu entfernen, oder klicken Sie mit der rechten Maustaste auf eine Rolle, und klicken Sie auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie Sie eine Verbindung mit Ihrem Azure AS-Server herstellen und die Datenbanken und Eigenschaften der Modelldatenbank „adventureworks“ in SSMS untersuchen. Außerdem haben Sie erfahren, wie Sie mit SSMS und TMSL-Skripts Benutzer oder Gruppen zu vorhandenen und neuen Rollen hinzufügen. Sie haben Benutzerberechtigungen für Ihren Server und die Beispielmodelldatenbank konfiguriert. Nun können Sie und andere Benutzer mithilfe von Clientanwendungen wie Power BI eine Verbindung damit herstellen. Weitere Informationen finden Sie im nächsten Tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Herstellen einer Verbindung mit Power BI Desktop](analysis-services-tutorial-pbid.md)