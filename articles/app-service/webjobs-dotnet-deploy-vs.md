---
title: Entwickeln und Bereitstellen von WebJobs mit Visual Studio
description: Erfahren Sie, wie Sie Azure WebJobs in Visual Studio entwickeln und in Azure App Service bereitstellen, einschließlich der Erstellung einer geplanten Aufgabe.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 06/24/2021
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 2c0fb760a5a20167da4a13fb483274908b98548b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558505"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Entwickeln und Bereitstellen von WebJobs mit Visual Studio

In diesem Artikel wird erläutert, wie Sie Visual Studio zum Bereitstellen eines Konsolen-App-Projekts in einer [Azure App Service](overview.md)-Web-App als [Azure-WebJob](https://go.microsoft.com/fwlink/?LinkId=390226) verwenden. Weitere Informationen zum Bereitstellen von WebJobs über das [Azure-Portal](https://portal.azure.com) finden Sie unter [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](webjobs-create.md).

Sie können einen WebJob entwickeln, der entweder als [.NET Core-App](#webjobs-as-net-core-console-apps) oder als [.NET Framework-App](#webjobs-as-net-framework-console-apps) ausgeführt wird. Mit Version 3.x des [Azure WebJobs SDK](webjobs-sdk-how-to.md) können Sie Webaufträge entwickeln, die entweder als .NET Core- oder .NET Framework-Apps ausgeführt werden, während Version 2.x lediglich das .NET Framework unterstützt. Die Bereitstellung eines WebJobs-Projekts ist bei .NET Core- und .NET Framework-Projekten unterschiedlich.

Sie können mehrere WebJobs für eine einzelne Web-App veröffentlichen, sofern jeder WebJob in einer Web-App über einen eindeutigen Namen verfügt.

## <a name="webjobs-as-net-core-console-apps"></a>Webaufträge als .NET Core-Konsolenanwendung

Mit Version 3.x des Azure WebJobs SDKs können Sie WebJobs als .NET Core-Konsolen-Apps erstellen und veröffentlichen. Exemplarische Vorgehensweisen zum Erstellen und Veröffentlichen einer .NET Core-Konsolen-App in Azure als WebJob finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK für die ereignisgesteuerte Hintergrundverarbeitung](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET Core-Web-Apps und/oder .NET Core-WebJobs können nicht mit Webprojekten verknüpft werden. Wenn Sie Ihren Webauftrag mit einer Web-App bereitstellen müssen, [erstellen Sie Ihre WebJobs als .NET Framework-Konsolen-App](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Bereitstellung in Azure App Service

Beim Veröffentlichen eines .NET Core-Webauftrags in Azure App Service über Visual Studio werden die gleichen Tools wie beim Veröffentlichen einer ASP.NET Core-App verwendet.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Webaufträge als .NET Framework-Konsolenanwendungen  

Wenn Sie Visual Studio verwenden, um ein WebJobs-fähiges .NET Framework-Konsolen-App-Projekt bereitzustellen, werden Runtimedateien in den entsprechenden Ordner der Web-App kopiert (*App_Data/jobs/continuous* bei fortlaufenden WebJobs und *App_Data/jobs/triggered* bei geplanten oder bei Bedarf ausgeführten WebJobs).

Visual Studio fügt die folgenden Elemente zu einem WebJobs-fähigen Projekt hinzu:

* Das NuGet-Paket [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/)
* Die Datei [webjob-publish-settings.json](#publishsettings) mit Bereitstellungs- und Zeitplanungseinstellungen 

![Abbildung: Zur Konsolen-App hinzugefügte Elemente zum Ermöglichen der Bereitstellung als WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Sie können diese Elemente zu einem vorhandenen Konsolen-App-Projekt hinzufügen oder eine Vorlage nutzen, um ein neues WebJobs-fähiges Konsolen-App-Projekt zu erstellen. 

Stellen Sie ein Projekt als eigenständigen WebJob bereit, oder verknüpfen Sie es mit einem Webprojekt, damit es automatisch bereitgestellt wird, wenn Sie das Webprojekt bereitstellen. Zum Verknüpfen von Projekten fügt Visual Studio den Namen des webauftragsfähigen Projekts der Datei [webjobs-list.json](#webjobslist) im Webprojekt hinzu.

![Abbildung der Verknüpfung eines Webauftragsprojekts mit einem Webprojekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Voraussetzungen

Installation von Visual Studio 2017 oder Visual Studio 2019 mit der [Azure-Entwicklungsworkload](/visualstudio/install/install-visual-studio#step-4---choose-workloads)

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Aktivieren der WebJobs-Bereitstellung für ein vorhandenes Konsolen-App-Projekt

Sie haben zwei Möglichkeiten:

* [Aktivieren der automatischen Bereitstellung mit einem Webprojekt](#convertlink).

  Konfigurieren Sie ein vorhandenes Konsolen-App-Projekt so, dass es automatisch als Webauftrag bereitgestellt wird, wenn Sie ein Webprojekt bereitstellen. Wählen Sie diese Option, wenn Sie Ihren Webauftrag in derselben Web-App ausführen möchten, in der die dazugehörige Webanwendung ausgeführt wird.

* [Ermöglichen der Bereitstellung ohne Webprojekt](#convertnolink).

  Konfigurieren Sie ein vorhandenes Konsolen-App-Projekt, um es ohne Verknüpfung mit einem Webprojekt als eigenständigen WebJob bereitzustellen. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung in der Web-App ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Aktivieren der automatischen Bereitstellung von Webaufträgen mit einem Webprojekt

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt. Klicken Sie dann auf **Hinzufügen** > **Vorhandenes Projekt als Azure WebJob**.
   
    ![Vorhandenes Projekt als Azure-Webauftrag](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.
2. Wählen Sie in der Dropdownliste **Projektname** das Konsolen-App-Projekt aus, das als WebJob hinzugefügt werden soll.
   
    ![Auswählen des Projekts im Dialogfeld „Azure-WebJob hinzufügen“](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Vervollständigen Sie das Dialogfeld [Azure-WebJob hinzufügen](#configure), und klicken Sie dann auf **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Aktivieren der Bereitstellung von Webaufträgen ohne Webprojekt
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Konsolen-App-Projekt, und wählen Sie dann **Als Azure-WebJob veröffentlichen...** aus. 
   
    ![Als Azure-Webauftrag veröffentlichen](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird mit dem im Feld **Projektname** ausgewählten Projekt angezeigt.
2. Vervollständigen Sie das Dialogfeld [Azure-WebJob hinzufügen](#configure), und klicken Sie dann auf **OK**.
   
   Der Assistent **Web veröffentlichen** wird geöffnet. Wenn Sie keine sofortige Veröffentlichung wünschen, schließen Sie den Assistenten. Die eingegebenen Einstellungen werden für den Zeitpunkt der gewünschten [Bereitstellung des Projekts](#deploy)gespeichert.

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Erstellen eines neuen webauftragsfähigen Projekts
Verwenden Sie die Konsolen-App-Projektvorlage, und aktivieren Sie die WebJobs-Bereitstellung mithilfe der Anweisungen im [vorherigen Abschnitt](#convert), um ein neues WebJobs-fähiges Projekt zu erstellen. Alternativ können Sie die Webauftragsvorlage "new-project" nutzen:

* [Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag](#createnolink)
  
    Erstellen Sie ein Projekt, und konfigurieren Sie es für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung in der Web-App ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.
* [Verwenden der Webauftragsvorlage "new-project" für einen mit einem Webprojekt verknüpften Webauftrag](#createlink)
  
    Erstellen Sie ein Projekt, das für die automatische Bereitstellung als WebJob konfiguriert ist, wenn Sie ein Webprojekt in derselben Projektmappe bereitstellen. Wählen Sie diese Option, wenn Sie Ihren Webauftrag in derselben Web-App ausführen möchten, in der die dazugehörige Webanwendung ausgeführt wird.

> [!NOTE]
> Die WebJobs-Vorlage „new-project“ installiert automatisch NuGet-Pakete und enthält in *Program.cs* Code für das [WebJobs-SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Wenn Sie das WebJobs-SDK nicht verwenden möchten, entfernen oder ändern Sie die `host.RunAndBlock`-Anweisung in *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag
1. Klicken Sie auf **Datei** > **Neu** > **Projekt**. Suchen Sie im Dialogfeld **Neues Projekt erstellen** nach **Azure WebJob (.NET Framework)** für C#, und wählen Sie diese Option aus.
   
2. Führen Sie die zuvor gezeigten Anweisungen aus, um [das Konsolen-App-Projekt als unabhängiges WebJobs-Projekt zu erstellen](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Verwenden der Webauftragsvorlage "new-project" für einen mit einem Webprojekt verknüpften Webauftrag
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt, und klicken Sie dann auf **Hinzufügen** > **Neues Azure WebJob-Projekt**.
   
    ![Menüeintrag "Neues Azure-Webauftragsprojekt"](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.
2. Vervollständigen Sie das Dialogfeld [Azure-WebJob hinzufügen](#configure), und klicken Sie dann auf **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>Die Datei „webjob-publish-settings.json“
Wenn Sie eine Konsolen.App für die WebJob-Bereitstellung konfigurieren, installiert Visual Studio das NuGet-Paket [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) und speichert Zeitplanungsinformationen in der Datei *webjob-publish-settings.json* im Ordner *Eigenschaften* des WebJob-Projekts. Hier ist ein Beispiel dieser Datei:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Sie können diese Datei direkt bearbeiten, und Visual Studio stellt IntelliSense zur Verfügung. Das Dateischema wird unter [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) gespeichert und kann dort angezeigt werden.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>Die Datei „webjobs-list.json“
Wenn Sie ein WebJob-fähiges Projekt mit einem Webprojekt verknüpfen, speichert Visual Studio den Namen des WebJob-Projekts in der Datei *webjobs-list.json* im Ordner *Eigenschaften* des Webprojekts. Die Liste kann mehrere WebJobs-Projekte umfassen, wie im folgenden Beispiel gezeigt wird:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

Diese Datei können Sie direkt in Visual Studio mit IntelliSense bearbeiten. Das Dateischema wird unter [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) gespeichert.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Bereitstellen eines Webauftragsprojekts
Ein WebJobs-Projekt, das Sie mit einem Webprojekt verknüpft haben, wird automatisch mit dem Webprojekt bereitstellt. Informationen zur Bereitstellung von Webprojekten finden Sie unter **Anleitungen** > **Bereitstellen der App** im linken Navigationsbereich.

Klicken Sie zum Bereitstellen eines WebJobs-Projekts im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie dann auf **Als Azure-WebJob veröffentlichen**. 

![Als Azure-Webauftrag veröffentlichen](./media/webjobs-dotnet-deploy-vs/paw.png)

Bei einem unabhängigen Webauftrag wird derselbe Assistent **Web veröffentlichen** wie bei Webprojekten angezeigt, wobei allerdings weniger Einstellungen geändert werden können.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Dialogfeld „Azure-WebJob hinzufügen“
Im Dialogfeld **Azure-WebJob hinzufügen** können Sie den WebJob-Namen und die Einstellung des Ausführungsmodus für Ihren WebJob eingeben. 

![Dialogfeld „Azure-WebJob hinzufügen“](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Einige der Felder in diesem Dialogfeld entsprechen Feldern im Dialogfeld **WebJob hinzufügen** des Azure-Portals. Weitere Informationen finden Sie unter [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](webjobs-create.md).

WebJob-Bereitstellungsinformationen:

* Informationen zur Befehlszeilenbereitstellung finden Sie unter [Aktivieren der befehlszeilengesteuerten oder kontinuierlichen Bereitstellung von Azure-Webaufträgen](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* Wenn Sie einen WebJob bereitstellen und dann entscheiden, den Typ des WebJobs zu ändern und diesen erneut bereitzustellen, müssen Sie die Datei *webjobs-publish-settings.json* löschen. Dies führt dazu, dass Visual Studio die Veröffentlichungsoptionen erneut anzeigt, sodass Sie den Typ des WebJobs ändern können.

* Wenn Sie einen Webauftrag bereitstellen und den Ausführungsmodus später von fortlaufend in nicht fortlaufend ändern oder umgekehrt, erstellt Visual Studio bei der erneuten Bereitstellung einen Webauftrag in Azure. Wenn Sie andere Planungseinstellungen ändern, aber den gleichen Ausführungsmodus verwenden oder zwischen einem geplanten und bedarfsgesteuerten Modus wechseln, aktualisiert Visual Studio den vorhandenen WebJob, anstatt einen neuen zu erstellen.

## <a name="webjob-types"></a>WebJob-Typen

Der Typ eines WebJobs kann entweder *Ausgelöst* oder *Fortlaufend* sein:

- Ausgelöst (Standardtyp): Ein ausgelöster WebJob wird basierend auf einem Bindungsereignis, nach einem [Zeitplan](#scheduling-a-triggered-webjob) oder bei manuellem Auslösen (bedarfsgesteuert) gestartet. Er wird in einer einzelnen Instanz ausgeführt, in der die Web-App ausgeführt wird.

- Fortlaufend: Ein [fortlaufender](#continuous-execution) WebJob wird sofort nach Erstellung des WebJobs gestartet. Er wird standardmäßig für alle skalierten Web-App-Instanzen ausgeführt, kann jedoch über *settings.job* so konfiguriert werden, dass er als einzelne Instanz ausgeführt wird.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Planen eines ausgelösten Webauftrags

Wenn Sie eine Konsolen-App in Azure veröffentlichen, legt Visual Studio den Typ des WebJobs standardmäßig auf **Ausgelöst** fest und fügt die neue Datei *settings.job* zum Projekt hinzu. Sie können diese Datei für ausgelöste WebJobs verwenden, um einen Ausführungszeitplan für Ihren WebJob festzulegen.

Verwenden Sie die Datei *settings.job*, um einen Ausführungszeitplan für Ihren WebJob festzulegen. Beim folgenden Beispiel erfolgt die Ausführung stündlich von 9:00 Uhr bis 17:00 Uhr:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Diese Datei befindet sich im Stammverzeichnis des WebJobs-Ordners, der das Skript Ihres WebJobs enthält, z. B. `wwwroot\app_data\jobs\triggered\{job name}` oder `wwwroot\app_data\jobs\continuous\{job name}`. Wenn Sie einen WebJob aus Visual Studio bereitstellen, kennzeichnen Sie die Eigenschaften der Datei *settings.job* in Visual Studio mit **Kopieren, falls aktueller**.

Wenn Sie einen [Webauftrag über das Azure-Portal erstellen](webjobs-create.md), wird die Datei *settings.job* für Sie erstellt.

#### <a name="cron-expressions"></a>CRON-Ausdrücke

Azure WebJobs verwendet die gleichen CRON-Ausdrücke für Zeitpläne wie der Zeitgebertrigger in Azure Functions. Weitere Informationen zum CRON-Support finden Sie unter [Trigger mit Timer für Azure Functions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>Referenz zu „settings.job“

Webaufträge unterstützen die folgenden Einstellungen:

| **Einstellung** | **Typ**  | **Beschreibung** |
| ----------- | --------- | --------------- |
| `is_in_place` | All | Diese Einstellung ermöglicht dem WebJob die direkte Ausführung, ohne dass er zunächst in einen temporären Ordner kopiert werden muss. Weitere Informationen finden Sie unter [WebJob-Arbeitsverzeichnis](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Fortlaufend | Mit dieser Einstellung wird der WebJob nur in einer einzelnen Instanz ausgeführt, wenn aufskaliert wurde. Weitere Informationen finden Sie unter [Festlegen eines fortlaufenden WebJobs als Singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Ausgelöst | Der Webauftrag wird gemäß eines CRON-basierten Zeitplans ausgeführt. Weitere Informationen finden Sie unter [NCRONTAB-Ausdrücke](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| All | Ermöglicht das Steuern des Verhaltens des Herunterfahrens. Weitere Informationen finden Sie unter [Ordnungsgemäßes Herunterfahren](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Fortlaufende Ausführung

Wenn Sie **Always On** in Azure aktivieren, können Sie Visual Studio verwenden, um den WebJob so zu ändern, dass er fortlaufend ausgeführt wird:

1. Wenn dies noch nicht erfolgt ist, [veröffentlichen Sie das Projekt in Azure](#deploy-to-azure-app-service).

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Klicken Sie auf der Registerkarte **Veröffentlichen** auf **Bearbeiten**. 

1. Wählen Sie im Dialogfeld **Profileinstellungen** die Option **Fortlaufend** für **WebJob-Typ** aus, und klicken Sie dann auf **Speichern**.

    ![Dialogfeld „Veröffentlichungseinstellungen“ für einen WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Klicken Sie auf der Registerkarte **Veröffentlichen** auf **Veröffentlichen**, um den WebJob mit den aktualisierten Einstellungen erneut zu veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zum WebJobs SDK](webjobs-sdk-how-to.md)
