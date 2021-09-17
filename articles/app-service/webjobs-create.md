---
title: Ausführen von Hintergrundaufgaben mit WebJobs
description: Erfahren Sie, wie Sie mithilfe von WebJobs Hintergrundaufgaben in Azure App Service ausführen. Wählen Sie aus einer Vielzahl von Skriptformaten aus, und führen Sie sie mit Cron-Ausdrücken aus.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 6/25/2021
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 3ecb31f6d008fda51d03c3e0d2d44b881397b466
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "122639779"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service

Bereitstellen von WebJobs mit dem [Azure-Portal](https://portal.azure.com), um eine ausführbare Datei oder ein Skript hochzuladen. Sie können Hintergrundaufgaben in Azure App Service ausführen.

Wenn Sie statt des Azure App Service Visual Studio 2019 zum Entwickeln und Bereitstellen von WebJobs verwenden, lesen Sie den Abschnitt [Bereitstellen von WebJobs mit Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Übersicht
WebJobs ist ein Feature von [Azure App Service](index.yml), mit dem Sie ein Programm oder Skript in derselben Instanz wie eine Web-App, API-App oder mobile App ausführen können. Für die Nutzung von WebJobs fallen keine zusätzlichen Kosten an.

Sie können das Azure WebJobs SDK mit WebJobs verwenden, um zahlreiche Programmieraufgaben vereinfachen. WebJobs wird für Azure App Service für Linux zurzeit noch nicht unterstützt. Weitere Informationen finden Sie unter [Was ist das WebJobs-SDK?](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions bietet eine weitere Möglichkeit zum Ausführen von Programmen und Skripts. Einen Vergleich zwischen WebJobs und Functions finden Sie unter [Auswählen zwischen Flow, Logic Apps, Functions und WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>WebJob-Typen

Die folgende Tabelle beschreibt die Unterschiede zwischen *fortlaufenden* und *ausgelösten* WebJobs.


|Fortlaufend  |Ausgelöst  |
|---------|---------|
| Werden sofort nach Erstellung des WebJobs gestartet. Damit der Auftrag nicht beendet wird, wird das Programm oder Skript in der Regel in einer Endlosschleife ausgeführt. Wenn der Auftrag beendet ist, können Sie ihn neu starten. Wird in der Regel mit dem WebJobs SDK verwendet. | Werden nur gestartet, wenn sie manuell oder nach einem Zeitplan ausgelöst werden. |
| Werden in allen Instanzen ausgeführt, in denen die Web-App ausgeführt wird. Sie können den WebJob wahlweise auf eine einzelne Instanz beschränken. |Werden in einer einzelnen Instanz ausgeführt, die Azure zum Lastenausgleich auswählt.|
| Unterstützen Remotedebuggen. | Bieten keine Unterstützung für Remotedebuggen.|
| Code wird unter `\site\wwwroot\app_data\Jobs\Continuous` bereitgestellt. | Code wird unter `\site\wwwroot\app_data\Jobs\Triggered` bereitgestellt. |

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Unterstützte Dateitypen für Skripts oder Programme

Die folgenden Dateitypen werden unterstützt:

* CMD, BAT, EXE (bei Verwendung von Windows-CMD)
* PS1 (bei Verwendung von PowerShell)
* SH (bei Verwendung von Bash)
* PHP (bei Verwendung von PHP)
* PY (bei Verwendung von Python)
* JS (bei Verwendung von Node.js)
* JAR (bei Verwendung von Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Erstellen eines fortlaufenden WebJobs

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Wenn Sie die Quellcodeverwaltung mit Ihrer Anwendung konfiguriert haben, sollten die Webaufträge im Rahmen der Integration der Quellcodeverwaltung bereitgestellt werden. Nachdem die Quellcodeverwaltung für Ihre Anwendung konfiguriert wurde, kann ein Webauftrag nicht über das Azure-Portal hinzugefügt werden.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Seite **App Service** Ihrer Web-App, API-App oder mobilen App von App Service.

1. Suchen Sie im linken Bereich der **App-Service**-Seite Ihrer Anwendung nach **WebJobs** und wählen Sie diese aus.

   ![Auswählen von WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

1. Auf der Seite **WebJobs** wählen Sie **Hinzufügen**.

    ![Seite „WebJob“](./media/web-sites-create-web-jobs/wjblade.png)

1. Geben Sie die in der Tabelle angegebenen Einstellungen **WebJob hinzufügen** ein.

   ![Screenshot, der die Einstellungen von „WebJob hinzufügen“ zeigt, die Sie konfigurieren müssen.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Einstellung      | Beispielwert   | BESCHREIBUNG  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myContinuousWebJob | Ein Name, der innerhalb einer App Service-App eindeutig ist. Muss mit einem Buchstaben oder einer Ziffer beginnen und darf nur die Sonderzeichen „-“ und „_“ enthalten. |
   | **Dateiupload** | ConsoleApp.zip | Eine *ZIP*-Datei, die die ausführbare Datei oder Skriptdatei sowie für die Ausführung des Programms oder Skripts erforderliche Hilfsdateien enthält. Die unterstützten Typen von ausführbarer Datei oder Skriptdatei werden im Abschnitt [Unterstützte Dateitypen](#acceptablefiles) aufgeführt. |
   | **Typ** | Fortlaufend | Die [WebJob-Typen](#webjob-types) werden weiter oben in diesem Artikel beschrieben. |
   | **Skalieren** | Mehrere Instanzen | Diese Option ist nur für fortlaufende WebJobs verfügbar. Legt fest, ob das Programm oder Skript auf allen Instanzen oder nur einer Instanz ausgeführt wird. Die Option zum Ausführen auf mehreren Instanzen gilt nicht für die [Tarife](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) „Free“ oder „Shared“. | 

1. Klicken Sie auf **OK**. 

   Der neue WebJob wird auf der Seite **WebJobs** angezeigt. Wenn Sie eine Meldung sehen, die besagt, dass der WebJob hinzugefügt wurde, Sie sie aber nicht sehen, wählen Sie **Aktualisieren**. 

   ![Liste von WebJobs](./media/web-sites-create-web-jobs/list-continuous-webjob.png)

1. Um einen fortlaufenden WebJob zu stoppen oder neu zu starten, klicken Sie mit der rechten Maustaste auf den WebJob in der Liste und wählen Sie **Stopp** oder **Start**.

    ![Beenden eines fortlaufenden WebJobs](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Erstellen eines manuell ausgelösten WebJobs

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie den Eintrag aus. 

1. Wählen Sie Ihre Webanwendung, API-App oder mobile App aus der Liste aus. 

1. Wählen Sie im linken Bereich der **App Service**-Seite Ihrer Anwendung **WebJobs** aus.

   ![Auswählen von WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Auf der Seite **WebJobs** wählen Sie **Hinzufügen** aus.

    ![Seite „WebJob“](./media/web-sites-create-web-jobs/wjblade.png)

1. Geben Sie die in der Tabelle angegebenen Einstellungen **WebJob hinzufügen** ein. 

   ![Screenshot, der die Einstellungen zeigt, die für das Erstellen eines manuell ausgelösten WebJobs konfiguriert werden müssen.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Einstellung      | Beispielwert   | BESCHREIBUNG  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myTriggeredWebJob | Ein Name, der innerhalb einer App Service-App eindeutig ist. Muss mit einem Buchstaben oder einer Ziffer beginnen und darf nur die Sonderzeichen „-“ und „_“ enthalten.|
   | **Dateiupload** | ConsoleApp.zip | Eine *ZIP*-Datei, die die ausführbare Datei oder Skriptdatei sowie für die Ausführung des Programms oder Skripts erforderliche Hilfsdateien enthält. Die unterstützten Typen von ausführbarer Datei oder Skriptdatei werden im Abschnitt [Unterstützte Dateitypen](#acceptablefiles) aufgeführt. |
   | **Typ** | Ausgelöst | Die [WebJob-Typen](#webjob-types) sind vorher in diesem Artikel beschrieben. |
   | **Trigger** | Manuell | |

4. Klicken Sie auf **OK**.

   Der neue WebJob wird auf der Seite **WebJobs** angezeigt. Wenn Sie eine Meldung sehen, die besagt, dass der WebJob hinzugefügt wurde, Sie sie aber nicht sehen, wählen Sie **Aktualisieren**.  

   ![Liste von ausgelösten WebJobs](./media/web-sites-create-web-jobs/list-triggered-webjob.png)

7. Zum Ausführen von WebJobs klicken Sie mit der rechten Maustaste in die Liste und wählen Sie **Ausführen**.
   
    ![Webauftrag ausführen](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Erstellen eines geplanten WebJobs

Ein geplanter WebJob wird ebenfalls ausgelöst. Sie können die automatische Auslösung des Triggers nach dem von Ihnen angegebenen Zeitplan planen.
 
<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie den Eintrag aus. 

1. Wählen Sie Ihre Webanwendung, API-App oder mobile App aus der Liste aus. 

1. Wählen Sie im linken Bereich der **App Service**-Seite Ihrer Anwendung **WebJobs** aus.

   ![Auswählen von WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

1. Auf der Seite **WebJobs** wählen Sie **Hinzufügen** aus.

   ![Seite „WebJob“](./media/web-sites-create-web-jobs/wjblade.png)

3. Geben Sie die in der Tabelle angegebenen Einstellungen **WebJob hinzufügen** ein.

   ![Seite „WebJob hinzufügen“](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Einstellung      | Beispielwert   | BESCHREIBUNG  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myScheduledWebJob | Ein Name, der innerhalb einer App Service-App eindeutig ist. Muss mit einem Buchstaben oder einer Ziffer beginnen und darf nur die Sonderzeichen „-“ und „_“ enthalten. |
   | **Dateiupload** | ConsoleApp.zip | Eine *ZIP*-Datei, die die ausführbare Datei oder Skriptdatei sowie für die Ausführung des Programms oder Skripts erforderliche Hilfsdateien enthält. Die unterstützten Typen von ausführbarer Datei oder Skriptdatei werden im Abschnitt [Unterstützte Dateitypen](#acceptablefiles) aufgeführt. |
   | **Typ** | Ausgelöst | Die [WebJob-Typen](#webjob-types) werden weiter oben in diesem Artikel beschrieben. |
   | **Trigger** | Geplant | Damit die Planung zuverlässig funktioniert, aktivieren Sie das Feature „Always On“. Dieses Feature steht nur in den Tarifen „Basic“, „Standard“ und „Premium“ zur Verfügung.|
   | **CRON-Ausdruck** | 0 0/20 * * * * | [CRON-Ausdrücke](#ncrontab-expressions) werden im folgenden Abschnitt beschrieben. |

4. Klicken Sie auf **OK**.

   Der neue WebJob wird auf der Seite **WebJobs** angezeigt. Wenn Sie eine Meldung sehen, die besagt, dass der WebJob hinzugefügt wurde, Sie sie aber nicht sehen, wählen Sie **Aktualisieren**.  

   ![Liste von geplanten WebJobs](./media/web-sites-create-web-jobs/list-scheduled-webjob.png)

## <a name="ncrontab-expressions"></a>NCRONTAB-Ausdrücke

Sie können einen [NCRONTAB-Ausdruck](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) im Portal eingeben oder eine `settings.job`-Datei dem Stammverzeichnis Ihrer WebJob-*ZIP*-Datei hinzufügen, wie im folgenden Beispiel gezeigt:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Weitere Informationen finden Sie unter [Planen eines ausgelösten WebJobs](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="manage-webjobs"></a>Verwalten von WebJobs

Sie können den Ausführungsstatus einzelner WebJobs verwalten, die auf Ihrer Website im [Azure-Portal](https://portal.azure.com) ausgeführt werden. Wechseln Sie einfach zu **Einstellungen** > **WebJobs,** wählen Sie den WebJob aus, und dann können Sie den WebJob starten und beenden. Sie können auch das Kennwort des Webhooks anzeigen und ändern, der den WebJob ausführt.  

Sie können auch [eine Anwendungseinstellung hinzufügen](configure-common.md#configure-app-settings)mit dem Namen`WEBJOB_STOPPED` von dem Wert `1`, um alle WebJobs zu beenden, die auf Ihrer Website ausgeführt werden. Dies kann hilfreich sein, um zu verhindern, dass in Konflikt stehende WebJobs sowohl in Staging- als auch in Produktionsslots ausgeführt werden. Sie können auf ähnliche Weise den Wert `1` für die `WEBJOBS_DISABLE_SCHEDULE` Einstellung verwenden, um ausgelöste WebJobs auf der Website oder in einem Stagingslot zu deaktivieren. Denken Sie bei Slots daran, die **Einstellungsoption Bereitstellungsslot** zu aktivieren, damit die Einstellung selbst nicht ausgetauscht wird.    

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Anzeigen des Auftragsverlaufs

1. Wählen Sie den WebJob aus, und wählen Sie dann **Protokolle** aus, um den Verlauf anzuzeigen.
   
   ![Schaltfläche „Protokolle“](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Wählen Sie auf der Seite **Details zum WebJob** einen Zeitpunkt aus, um die Details für eine Ausführung anzuzeigen.
   
   ![Details zum WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Wählen Sie auf der Seite **Details zur WebJob-Ausführung** die Option **Ausgabe umschalten**, um den Text der Protokollinhalte anzuzeigen.
   
    ![Ausführungsdetails zu WebJobs](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Um den Ausgabetext in einem separaten Browserfenster anzuzeigen, klicken Sie auf **Herunterladen**. Um den Text selbst herunterzuladen, klicken Sie mit der rechten Maustaste auf **Herunterladen**, und speichern Sie die Dateiinhalte mithilfe der Browseroptionen.
   
5. Wählen Sie am oberen Rand der Seite den Breadcrumb-Link **WebJobs** aus, um eine Liste von WebJobs aufzurufen.

    ![WebJob-Breadcrumb](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Liste der Webaufträge im Verlaufsdashboard](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Nächste Schritte

Das Azure WebJobs SDK kann in Verbindung mit WebJobs zahlreiche Programmieraufgaben vereinfachen. Weitere Informationen finden Sie unter [Was ist das WebJobs-SDK?](https://github.com/Azure/azure-webjobs-sdk/wiki).
