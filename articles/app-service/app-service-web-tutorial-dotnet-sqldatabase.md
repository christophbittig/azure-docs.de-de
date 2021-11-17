---
title: 'Tutorial: ASP.NET-App mit Azure SQL-Datenbank'
description: Es wird beschrieben, wie Sie eine C#-ASP.NET-App in Azure mit Azure SQL-Datenbank bereitstellen.
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/08/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 72ea37f483055ae3bcb97520efa7f2a4fb704037
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058861"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Tutorial: Bereitstellen einer ASP.NET-App in Azure mit Azure SQL-Datenbank

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. In diesem Tutorial wird veranschaulicht, wie Sie eine datengesteuerte ASP.NET-App in App Service bereitstellen und dafür eine Verbindung mit [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md) herstellen. Nach Abschluss verfügen Sie über eine ASP.NET-App, die in Azure ausgeführt wird und mit SQL-Datenbank verbunden ist.

![Veröffentlichte ASP.NET-Anwendung in Azure App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen einer Datenbank in Azure SQL-Datenbank
> * Herstellen einer Verbindung mit SQL-Datenbank für eine ASP.NET-App
> * Bereitstellen der Anwendung in Azure
> * Aktualisieren des Datenmodells und erneutes Bereitstellen der App
> * Streamen von Protokollen von Azure auf Ihr Terminal
> * Verwalten der App im Azure-Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Abschließen dieses Tutorials benötigen Sie Folgendes:

Installieren Sie <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2022</a> mit der Workload **ASP.NET und Webentwicklung**.

Falls Sie Visual Studio bereits installiert haben, können Sie die Workloads in Visual Studio hinzufügen, indem Sie auf **Extras** > **Get Tools and Features** (Tools und Features abrufen) klicken.

## <a name="download-the-sample"></a>Herunterladen des Beispiels

1. [Laden Sie das Beispielprojekt](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip) herunter.

1. Extrahieren (entzippen) Sie die Datei *dotnet-sqldb-tutorial-master.zip*.

Das Beispielprojekt enthält eine einfache [ASP.NET MVC](https://www.asp.net/mvc)-CRUD-App (Create-Read-Update-Delete, Erstellen-Lesen-Aktualisieren-Löschen), die auf [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) basiert.

### <a name="run-the-app"></a>Ausführen der App

1. Öffnen Sie die Datei *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* in Visual Studio.

1. Geben Sie `Ctrl+F5` ein, um die App ohne Debuggen auszuführen. Die App wird im Standardbrowser angezeigt.

1. Wählen Sie den Link **Neu erstellen**, und erstellen Sie einige *Aufgaben*-Elemente.

    ![Dialogfeld "Neues ASP.NET-Projekt"](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. Testen Sie die Links **Bearbeiten**, **Details** und **Löschen**.

Die App nutzt einen Datenbankkontext, um die Verbindung mit der Datenbank herzustellen. In diesem Beispiel wird im Datenbankkontext eine Verbindungszeichenfolge mit dem Namen `MyDbConnection` verwendet. Die Verbindungszeichenfolge wird in der Datei *Web.config* festgelegt, und die Datei *Models/MyDatabaseContext.cs* enthält einen Verweis darauf. Der Name der Verbindungszeichenfolge wird später im Tutorial verwendet, um die Azure-App mit einer Azure SQL-Datenbank zu verbinden.

## <a name="publish-aspnet-application-to-azure"></a>Veröffentlichen einer ASP.NET-Anwendung in Azure

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **DotNetAppSqlDb**, und wählen Sie **Veröffentlichen** aus.

    ![Veröffentlichen über den Projektmappen-Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Wählen Sie als Ziel **Azure** aus, und klicken Sie auf **Weiter**.

1. Vergewissern Sie sich, dass **Azure App Service (Windows)** ausgewählt ist, und klicken Sie auf **Veröffentlichen**.

#### <a name="sign-in-and-add-an-app"></a>Anmelden und Hinzufügen einer App

1. Klicken Sie im Dialogfeld **Veröffentlichen** auf **Anmelden**.

1. Melden Sie sich bei Ihrem Azure-Abonnement an. Falls Sie bereits bei einem Microsoft-Konto angemeldet sind, vergewissern Sie sich, dass dieses Konto Ihr Azure-Abonnement enthält. Wenn das Microsoft-Konto, bei dem Sie angemeldet sind, nicht Ihr Azure-Abonnement enthält, klicken Sie darauf, um das korrekte Konto hinzuzufügen.

1. Klicken Sie im Bereich **App Service-Instanzen** auf **+** .

    ![Anmelden bei Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Konfigurieren des Web-App-Namens

Sie können den generierten Web-App-Namen beibehalten oder in einen anderen eindeutigen Namen ändern (gültige Zeichen `a-z`, `0-9` und `-`). Der Web-App-Name wird als Teil der Standard-URL für Ihre App verwendet (`<app_name>.azurewebsites.net`, wobei `<app_name>` Ihr Web-App-Name ist). Der Web-App-Name muss in Azure über alle Apps hinweg eindeutig sein.

> [!NOTE]
> Wählen Sie noch nicht **Erstellen** aus.

![Dialogfeld „App Service erstellen“](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. Klicken Sie neben **Ressourcengruppe** auf **Neu**.

   ![Klicken Sie neben „Ressourcengruppe“ auf „Neu“.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Geben Sie der Ressourcengruppe den Namen **myResourceGroup**.

#### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Klicken Sie neben **Hostingplan** auf **Neu**.

1. Konfigurieren Sie den neuen App Service-Plan im Dialogfeld **App Service-Plan konfigurieren** mit den folgenden Einstellungen, und klicken Sie auf **OK**:

   | Einstellung  | Vorgeschlagener Wert | Weitere Informationen finden Sie unter |
   | ----------------- | ------------ | ----|
   |**App Service-Plan**| myAppServicePlan | [App Service-Pläne](../app-service/overview-hosting-plans.md) |
   |**Location**| Europa, Westen | [Azure-Regionen](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Größe**| Kostenlos | [Tarife](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![App Service-Plan erstellen](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. Klicken Sie auf **Erstellen**, und warten Sie, bis die Azure-Ressourcen erstellt werden.

1. Im Dialogfeld **Veröffentlichen** werden die von Ihnen konfigurierten Ressourcen angezeigt. Klicken Sie auf **Fertig stellen**.

   ![Erstellte Ressourcen](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Erstellen eines Servers und einer Datenbank

Bevor Sie eine Datenbank erstellen, benötigen Sie einen [logischen SQL-Server](../azure-sql/database/logical-servers.md). Ein logischer Server ist ein logisches Konstrukt, das eine Gruppe von Datenbanken enthält, die als Gruppe verwaltet werden.

1. Scrollen Sie im Dialogfeld **Veröffentlichen** nach unten zum Abschnitt **Dienstabhängigkeiten**. Klicken Sie neben **SQL Server-Datenbank** auf **Konfigurieren**.

    > [!NOTE]
    > Stellen Sie sicher, dass Sie die SQL-Datenbank auf der Seite **Veröffentlichen** und nicht auf der Seite **Verbundene Dienste** konfigurieren.

   ![Konfigurieren der SQL-Datenbank-Abhängigkeit](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. Wählen Sie **Azure SQL-Datenbank** aus, und klicken Sie auf **Weiter**.

1. Klicken Sie im Dialogfeld **Azure SQL-Datenbank konfigurieren** auf **+** .

1. Klicken Sie neben **Datenbankserver** auf **Neu**.

   Dieser Servername wird als Teil der Standard-URL für Ihren Server `<server_name>.database.windows.net` verwendet. Er muss in Azure SQL über alle Server hinweg eindeutig sein. Ändern Sie den Servernamen bei Bedarf.

1. Fügen Sie den Benutzernamen und das Kennwort eines Administrators hinzu. Informationen zu den Anforderungen an die Komplexität von Kennwörtern finden Sie unter [Kennwortrichtlinie](/sql/relational-databases/security/password-policy).

   Merken Sie sich diesen Benutzernamen und das Kennwort. Sie benötigen diese Angaben später zum Verwalten des Servers.

   ![Server erstellen](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Ihr Kennwort ist in den Verbindungszeichenfolgen zwar maskiert (sowohl in Visual Studio als auch in App Service), aber allein die Tatsache, dass es irgendwo gespeichert ist, macht die App bereits anfälliger für Angriffe. App Service kann dieses Risiko durch die Verwendung [verwalteter Dienstidentitäten](overview-managed-identity.md) beseitigen, da Geheimnisse dann nicht mehr im Code oder in der App-Konfiguration gespeichert werden müssen. Weitere Informationen finden Sie in den [nächsten Schritten](#next-steps).

1. Klicken Sie auf **OK**.

1. Behalten Sie im Dialogfeld **Azure SQL-Datenbank** den standardmäßig generierten **Datenbanknamen** bei. Wählen Sie **Erstellen** aus, und warten Sie, bis die Datenbankressourcen erstellt werden.

    ![Konfigurieren der Datenbank](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Konfigurieren der Datenbankverbindung

1. Wenn der Assistent die Erstellung der Datenbankressourcen abgeschlossen hat, klicken Sie auf **Weiter**.

1. Geben Sie unter **Name der Datenbankverbindungszeichenfolge** die Zeichenfolge _MyDbConnection_ ein. Dieser Name muss mit der Verbindungszeichenfolge übereinstimmen, auf die in _Models/MyDatabaseContext.cs_ verwiesen wird.

1. Geben Sie unter **Benutzername für Datenbankverbindung** und **Kennwort für Datenbankverbindung** den Benutzernamen und das Kennwort des Administrators ein, die Sie unter [Erstellen eines Servers](#create-a-server-and-database) verwendet haben.

1. Vergewissern Sie sich, dass **Azure-App-Einstellungen** ausgewählt ist, und klicken Sie auf **Fertig stellen**.

    > [!NOTE]
    > Wenn stattdessen **Local user secrets files** (Dateien mit lokalen Benutzergeheimnissen) angezeigt wird, haben Sie SQL-Datenbank über die Seite **Verbundene Dienste** und nicht über die Seite **Veröffentlichen** konfiguriert.

    ![Konfigurieren der Datenbankverbindungszeichenfolge](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Warten Sie, bis der Konfigurations-Assistent abgeschlossen ist, und klicken Sie auf **Schließen**.

#### <a name="deploy-your-aspnet-app"></a>Bereitstellen der ASP.NET-App

1. Scrollen Sie auf der Registerkarte **Veröffentlichen** nach oben, und klicken Sie auf **Veröffentlichen**. Nach der Bereitstellung Ihrer ASP.NET-App in Azure Ihr Standardbrowser wird mit der URL für die bereitgestellte App gestartet.

1. Fügen Sie einige Aufgaben hinzu.

    ![Veröffentlichte ASP.NET-Anwendung in Azure-App](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Glückwunsch! Ihre datengesteuerte ASP.NET-Anwendung wird live in Azure App Service ausgeführt.

## <a name="access-the-database-locally"></a>Lokaler Zugriff auf die Datenbank

Mit Visual Studio können Sie Ihre neue Datenbank in Azure bequem im **SQL Server-Objekt-Explorer** überprüfen und verwalten. Bei der neuen Datenbank ist bereits die Firewall für die von Ihnen erstellte App Service-App geöffnet. Um darauf von Ihrem lokalen Computer aus (etwa über Visual Studio) zugreifen zu können, müssen Sie eine Firewall für die öffentliche IP-Adresse Ihres lokalen Computers öffnen. Wenn Ihre öffentliche IP-Adresse von Ihrem Internetdienstanbieter geändert wird, müssen Sie die Firewall neu konfigurieren, um auf die Azure-Datenbank zugreifen zu können.

#### <a name="create-a-database-connection"></a>Erstellen einer Datenbankverbindung

1. Wählen Sie im Menü **Ansicht** die Option **SQL Server-Objekt-Explorer**.

1. Klicken Sie oben im **SQL Server-Objekt-Explorer** auf die Schaltfläche **SQL Server hinzufügen**.

#### <a name="configure-the-database-connection"></a>Konfigurieren der Datenbankverbindung

1. Erweitern Sie im Dialogfeld **Verbinden** den Knoten **Azure**. Hier sind alle Ihre Instanzen von SQL-Datenbank in Azure aufgeführt.

1. Wählen Sie die Datenbank aus, die Sie zuvor erstellt haben. Die Verbindung, die Sie zuvor erstellt haben, wird automatisch im unteren Bereich angezeigt.

1. Geben Sie das Administratorkennwort für die Datenbank ein, das Sie zuvor erstellt haben, und klicken Sie auf **Verbinden**.

    ![Datenbankverbindung in Visual Studio konfigurieren](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Zulassen der Clientverbindung auf dem Computer

Das Dialogfeld zum Erstellen einer neuen **Firewallregel** wird angezeigt. Standardmäßig lässt ein Server nur Verbindungen von Azure-Diensten wie Ihrer Azure-App mit seiner Datenbank zu. Erstellen Sie zum Herstellen einer Verbindung mit Ihrer Datenbank von außerhalb von Azure aus eine Firewallregel auf Serverebene. Mit der Firewallregel wird die öffentliche IP-Adresse Ihres lokalen Computers zugelassen.

Das Dialogfeld ist bereits mit der öffentlichen IP-Adresse des Computers ausgefüllt.

1. Stellen Sie sicher, dass **Meine IP-Clientadresse hinzufügen** ausgewählt ist, und klicken Sie auf **OK**.

    ![Erstellen einer Firewallregel](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Sobald das Erstellen der Firewalleinstellung in Visual Studio für die SQL-Datenbankinstanz abgeschlossen wurde, wird die Verbindung im **SQL Server-Objekt-Explorer** angezeigt.

    Hier können Sie häufige Datenbankvorgänge ausführen, und z.B. Abfragen ausführen, Ansichten und gespeicherte Prozeduren erstellen usw.

1. Erweitern Sie Ihre Verbindung unter **Datenbanken** >  **&lt;Ihre Datenbank>**  > **Tabellen**. Klicken Sie mit der rechten Maustaste auf die Tabelle `Todoes`, und wählen Sie **Daten anzeigen**.

    ![SQL-Datenbank-Objekte ansehen](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Aktualisieren der App mit Code First-Migrationen

Sie können die vertrauten Tools in Visual Studio verwenden, um Ihre Datenbank und App in Azure zu aktualisieren. In diesem Schritt verwenden Sie Code First-Migrationen in Entity Framework, um eine Änderung am Datenbankschema vorzunehmen und sie in Azure zu veröffentlichen.

Weitere Informationen zur Verwendung von Entity Framework Code First-Migrationen finden Sie unter [Getting Started with Entity Framework 6 Code First using MVC 5](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (Erste Schritte mit Entity Framework 6 Code First per MVC 5).

#### <a name="update-your-data-model"></a>Aktualisieren Sie des Datenmodells

Öffnen Sie _Models\Todo.cs_ im Code-Editor. Fügen Sie der `ToDo`-Klasse die folgende Eigenschaft hinzu:

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Lokales Ausführen von Code First-Migrationen

Führen Sie einige Befehle aus, um Aktualisierungen an Ihrer lokalen Datenbank vorzunehmen.

1. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.

1. Aktivieren Sie im Fenster „Paket-Manager-Konsole“ Code First-Migrationen:

    ```powershell
    Enable-Migrations
    ```
    
1. Fügen Sie eine Migration hinzu:

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Aktualisieren Sie die lokale Datenbank:

    ```powershell
    Update-Database
    ```
    
1. Drücken Sie `Ctrl+F5`, um die App auszuführen. Testen Sie die Links „Bearbeiten“, „Details“ und „Erstellen“.

Wenn die Anwendung ohne Fehler geladen wird, waren die Code First-Migrationen erfolgreich. Allerdings wird die Seite noch genauso angezeigt, weil die Anwendungslogik noch nicht die neue Eigenschaft verwendet.

#### <a name="use-the-new-property"></a>Verwenden der neuen Eigenschaft

Nehmen Sie einige Änderungen an Ihrem Code vor, um die `Done`-Eigenschaft zu verwenden. Der Einfachheit halber ändern Sie in diesem Lernprogramm nur Ansichten `Index` und `Create`, um die Eigenschaft in Aktion zu sehen.

1. Öffnen Sie _Controllers\TodosController.cs_.

1. Suchen Sie die `Create()`-Methode in Zeile 52, und fügen Sie `Done` zur Liste der Eigenschaften im `Bind`-Attribut hinzu. Anschließend sieht Ihre `Create()`-Methodensignatur wie im folgenden Code aus:

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. Öffnen Sie _Views\Todos\Create.cshtml_.

1. Im Razor-Code sollten Sie ein `<div class="form-group">`-Element sehen, das `model.Description` verwendet, sowie ein weiteres `<div class="form-group">`-Element, das `model.CreatedDate` verwendet. Fügen Sie direkt nach diesen beiden Elementen ein weiteres `<div class="form-group">`-Element hinzu, für das `model.Done` genutzt wird:

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. Öffnen Sie _Views\Todos\Index.cshtml_.

1. Suchen Sie nach dem leeren `<th></th>`-Element. Fügen Sie direkt oberhalb dieses Elements den folgenden Razor-Code hinzu:

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. Suchen Sie nach dem `<td>`-Element, das die `Html.ActionLink()`-Hilfsmethoden enthält. Fügen Sie _über_ diesem `<td>` ein weiteres `<td>`-Element mit folgendem Razor-Code hinzu:

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    Mehr ist nicht erforderlich, um die Änderungen in den Ansichten `Index` und `Create` anzuzeigen.

1. Drücken Sie `Ctrl+F5`, um die App auszuführen.

Sie können jetzt eine Aufgabe hinzufügen und die Option **Fertig** aktivieren. Die Aufgabe sollte dann auf der Startseite als erledigt angezeigt werden. Beachten Sie, dass in der Ansicht `Edit` das Feld `Done` nicht angezeigt wird, da Sie die Ansicht `Edit` nicht geändert haben.

#### <a name="enable-code-first-migrations-in-azure"></a>Aktivieren von Code First-Migrationen in Azure

Da die Codeänderung, einschließlich der Datenbankmigration, funktioniert, können Sie sie in Ihrer Azure-App veröffentlichen und die SQL-Datenbank ebenfalls mit Code First-Migrationen aktualisieren.

1. Klicken Sie wie zuvor mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Veröffentlichen** aus.

1. Klicken Sie auf **Weitere Aktionen** > **Bearbeiten**, um die Veröffentlichungseinstellungen zu öffnen.

    ![Veröffentlichungseinstellungen öffnen](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. Wählen Sie in der Dropdownliste **MyDatabaseContext** die Datenbankverbindung für Ihre Azure SQL-Datenbank-Instanz aus.

1. Wählen Sie **Code First-Migrationen ausführen (wird beim Anwendungsstart ausgeführt)** aus, und klicken Sie auf **Speichern**.

    ![Aktivieren von Code First-Migrationen in einer Azure-App](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>Veröffentlichen der Änderungen

Nachdem Sie Code First-Migrationen in der Azure-App aktiviert haben, können Sie die Änderungen am Code veröffentlichen.

1. Klicken Sie auf der Veröffentlichungsseite auf **Veröffentlichen**.

1. Versuchen Sie erneut, Aufgaben hinzuzufügen, und wählen Sie **Fertig** aus. Sie sollten auf der Startseite als abgeschlossene Aufgaben angezeigt werden.

    ![Azure-App nach Code First-Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Ihre gesamten vorhandenen Aufgaben werden weiterhin angezeigt. Wenn Sie die ASP.NET-Anwendung erneut veröffentlichen, gehen in der SQL-Datenbank vorhandene Daten nicht verloren. Außerdem wird durch Code First-Migrationen nur das Datenschema geändert, die vorhandenen Daten bleiben unverändert.

## <a name="stream-application-logs"></a>Streamen von Anwendungsprotokollen

Sie können Ablaufverfolgungsmeldungen direkt aus der Azure-App in Visual Studio streamen.

Öffnen Sie _Controllers\TodosController.cs_.

Jede Aktion beginnt mit einer `Trace.WriteLine()`-Methode. Dieser Code wird hinzugefügt, um zu zeigen, wie Sie Ihrer Azure-App Ablaufverfolgungsnachrichten hinzufügen können.

#### <a name="enable-log-streaming"></a>Aktivieren des Protokollstreamings

1. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

1. Erweitern Sie im **Cloud-Explorer** das Azure-Abonnement mit Ihrer App und anschließend **App Service**.

1. Klicken Sie mit der rechten Maustaste auf Ihre Azure-App, und wählen Sie **Streamingprotokolle anzeigen** aus.

    ![Aktivieren des Protokollstreamings](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    Die Protokolle werden jetzt in das **Ausgabefenster** gestreamt.

    ![Protokollstreaming im Ausgabefenster](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    Es werden allerdings noch keine Ablaufverfolgungsmeldungen angezeigt. Das liegt daran, dass beim ersten Auswählen von **Streamingprotokolle anzeigen** die Azure-App die Ablaufverfolgungsebene auf `Error` festlegt, womit nur Fehlerereignisse protokolliert werden (mit der `Trace.TraceError()`-Methode).

#### <a name="change-trace-levels"></a>Ändern der Ablaufverfolgungsebenen

1. Wenn Sie die Ablaufverfolgungsebenen ändern möchten, um andere Ablaufverfolgungsmeldungen auszugeben, wechseln Sie zurück zum **Cloud-Explorer**.

1. Klicken Sie mit der rechten Maustaste erneut auf Ihre App, und wählen Sie **Im Portal öffnen** aus.

1. Wählen Sie auf der Portalverwaltungsseite für Ihre App im Menü auf der linken Seite **App Service-Protokolle** aus.

1. Wählen Sie unter **Anwendungsprotokollierung (Dateisystem)** unter **Ebene** die Option **Ausführlich** aus. Klicken Sie auf **Speichern**.

    ![Ablaufverfolgungsebene in „Ausführlich“ ändern](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > Sie können mit verschiedenen Ablaufverfolgungsebenen experimentieren, um zu sehen, welche Nachrichten für die jeweiligen Ebenen angezeigt werden. Bei der Ebene **Informationen** werden alle von `Trace.TraceInformation()`, `Trace.TraceWarning()` und `Trace.TraceError()` erstellten Protokolle eingeschlossen, aber keine Protokolle, die von `Trace.WriteLine()` erstellt wurden.

1. Navigieren Sie in Ihrem Browser erneut zu Ihrer App unter *http://&lt;Ihr App-Name>.azurewebsites.net*, und versuchen Sie, auf die Anwendung „To-Do-Liste“ in Azure zu klicken. Die Ablaufverfolgungsmeldungen werden jetzt in das **Ausgabefenster** in Visual Studio gestreamt.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Beenden des Protokollstreamings

Klicken Sie zum Beenden des Diensts für das Protokollstreaming auf die Schaltfläche **Überwachung beenden** im **Ausgabefenster**.

![Beenden des Protokollstreamings](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Verwalten der Azure-App

Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um die Web-App zu verwalten. Suchen Sie nach **App Services**, und wählen Sie diese Option aus.

![Suchen nach „Azure App Services“](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Wählen Sie den Namen Ihrer Azure-App aus.

![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Sie befinden sich auf der Seite Ihrer App.

Standardmäßig wird im Portal die Seite **Übersicht** angezeigt. Diese Seite bietet einen Überblick über den Status Ihrer App. Hier können Sie auch einfache Verwaltungsaufgaben wie Durchsuchen, Beenden, Neustarten und Löschen durchführen. Die Registerkarten links auf der Seite zeigen die verschiedenen Konfigurationsseiten, die Sie öffnen können.

![App Service-Seite im Azure-Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
>
> * Erstellen einer Datenbank in Azure SQL-Datenbank
> * Herstellen einer Verbindung mit SQL-Datenbank für eine ASP.NET-App
> * Bereitstellen der Anwendung in Azure
> * Aktualisieren des Datenmodells und erneutes Bereitstellen der App
> * Streamen von Protokollen von Azure auf Ihr Terminal
> * Verwalten der App im Azure-Portal

Im nächsten Tutorial erfahren Sie, wie Sie ganz einfach die Sicherheit Ihrer Verbindung mit Azure SQL-Datenbank verbessern.

> [!div class="nextstepaction"]
> [Access SQL Database securely using managed identities for Azure resources](tutorial-connect-msi-sql-database.md) (Sicheres Zugreifen auf SQL-Datenbank mithilfe verwalteter Identitäten für Azure-Ressourcen)

Weitere Ressourcen:

> [!div class="nextstepaction"]
> [Konfigurieren einer ASP.NET-App](configure-language-dotnet-framework.md)

Möchten Sie Ihre Cloudausgaben optimieren und dabei sparen?

> [!div class="nextstepaction"]
> [Beginnen mit der Kostenanalyse mit Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)