---
title: Node.js – Erste Schritte
description: Erfahren Sie, wie Sie eine einfache Node.js-Webanwendung erstellen und in einem Azure-Clouddienst bereitstellen können.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 723dc4468e2b5ff49b82eaf91ac46f62aa961236
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823669"
---
# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service-classic"></a>Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Dieses Lernprogramm veranschaulicht, wie Sie eine einfache Node.js-Anwendung erstellen können, die in einem Azure-Clouddienst ausgeführt wird. Clouddienste sind die Bausteine skalierbarer Cloudanwendungen in Azure. Sie ermöglichen die Trennung und unabhängige Verwaltung und Skalierung von Front-End- und Back-End-Komponenten von Anwendungen.  Clouddienste bieten stabile und dedizierte virtuelle Computer, um jede Rolle zuverlässig zu hosten.

Weitere Informationen zu Cloud Services und einen Vergleich mit Azure-Websites und Virtual Machines finden Sie unter [Azure Websites, Cloud Services and Virtual Machines comparison].

> [!TIP]
> Möchten Sie eine einfache Website erstellen? Wenn Ihr Szenario nur ein einfaches Website-Front-End umfasst, sollten Sie die [Verwendung einer einfachen Web-App] in Betracht ziehen. Sie können einen Clouddienst mühelos aktualisieren, wenn die Web-App größer wird und sich Ihre Anforderungen ändern.

In diesem Lernprogramm werden Sie eine einfache Webanwendung erstellen, die in einer Webrolle gehostet wird. Sie werden den Serveremulator verwenden, um die Anwendung lokal zu testen. Anschließend stellen Sie sie mithilfe der PowerShell-Befehlszeilentools bereit.

Die Anwendung ist eine einfache "Hello World"-Anwendung:

![Ein Webbrowser, der die Hallo Welt-Webseite anzeigt][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Voraussetzungen
> [!NOTE]
> In diesem Lernprogramm wird Azure PowerShell verwendet, für die Windows installiert sein muss.

* Installieren und Konfigurieren von [Azure PowerShell].
* Herunterladen und Installieren des [Azure SDK für .NET 2.7]. Wählen Sie während der Installationseinrichtung Folgendes:
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## <a name="create-an-azure-cloud-service-project"></a>Erstellen eines Azure-Clouddienstprojekts
Führen Sie folgende Aufgaben durch, um ein neues Azure-Clouddienstprojekt sowie ein einfaches Node.js-Gerüst zu erstellen:

1. Führen Sie **Windows PowerShell** als Administrator aus. Suchen Sie im **Startmenü** oder auf dem **Startbildschirm** nach **Windows PowerShell**.
2. [Stellen Sie eine PowerShell-Verbindung mit Ihrem Abonnement her.]
3. Geben Sie das folgende PowerShell-Cmdlet ein, um das Projekt zu erstellen:

   ```powershell
   New-AzureServiceProject helloworld
   ```

   ![Ergebnis des New-AzureService-Hallowelt-Befehls][The result of the New-AzureService helloworld command]

   Das **New-AzureServiceProject** -Cmdlet generiert eine einfache Struktur für die Veröffentlichung einer Node.js-Anwendung in einem Clouddienst. Darin sind Konfigurationsdateien enthalten, die für die Veröffentlichung in Azure erforderlich sind. Das Cmdlet ändert zudem das Arbeitsverzeichnis in das Verzeichnis des Diensts.

   Das Cmdlet erstellt die folgenden Dateien:

   * **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** und **ServiceDefinition.csdef**: Dies sind Azure-spezifische Dateien, die für die Veröffentlichung der Anwendung erforderlich sind. Weitere Informationen erhalten Sie unter [Übersicht zum Erstellen eines gehosteten Diensts für Azure].
   * **deploymentSettings.json** speichert lokale Einstellungen, die von den Azure PowerShell-Bereitstellungs-Cmdlets verwendet werden.

4. Geben Sie den folgenden Befehl zum Hinzufügen einer neuen Webrolle ein:

   ```powershell
   Add-AzureNodeWebRole
   ```

   ![Das Ergebnis des Add-AzureNodeWebRole-Befehls][The output of the Add-AzureNodeWebRole command]

   Das **Add-AzureNodeWebRole** -Cmdlet erstellt eine einfache Node.js-Anwendung. Zudem ändert es die **CSFG**- und **CSDEF**-Dateien, um Konfigurationseinträge für die neue Rolle hinzuzufügen.

   > [!NOTE]
   > Wenn Sie keinen Rollennamen angeben, wird ein Standardname verwendet. Sie können einen Namen als ersten Cmdlet-Parameter angeben: `Add-AzureNodeWebRole MyRole`

Die Node.js-App wird in der Datei **server.js** im Verzeichnis für die Webrolle (Standardeinstellung: **WebRole1**) definiert. Hier folgt der Code:

```js
var http = require('http');
var port = process.env.port || 1337;
http.createServer(function (req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello World\n');
}).listen(port);
```

Dieser Code ist im Wesentlichen identisch mit dem Beispiel "Hello World" auf der [nodejs.org] -Website, mit der Ausnahme, dass die von der Cloudumgebung zugewiesene Portnummer verwendet wird.

## <a name="deploy-the-application-to-azure"></a>Bereitstellen der Anwendung für Azure

> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenleistungen aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Testkonto registrieren](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="download-the-azure-publishing-settings"></a>Herunterladen der Azure-Veröffentlichungseinstellungen
Um die Anwendung in Azure bereitzustellen, müssen Sie zuerst die Veröffentlichungseinstellungen für Ihr Azure-Abonnement herunterladen.

1. Führen Sie das folgende Azure PowerShell-Cmdlet aus:

    ```powershell
    Get-AzurePublishSettingsFile
    ```

   Dadurch wird Ihr Browser zur Downloadseite mit den Veröffentlichungseinstellungen navigieren. Sie werden möglicherweise aufgefordert, sich mit einem Microsoft-Konto anzumelden. Verwenden Sie in diesem Fall das mit Ihrem Azure-Abonnement verknüpfte Konto.

   Speichern Sie das heruntergeladene Profil an einem Datenspeicherort, auf den Sie leicht zugreifen können.
2. Führen Sie das folgende Cmdlet aus, um das heruntergeladene Veröffentlichungsprofil zu importieren:

    ```powershell
    Import-AzurePublishSettingsFile [path to file]
    ```

    > [!NOTE]
    > Nach dem Importieren der Veröffentlichungseinstellungen empfiehlt es sich, die heruntergeladene .publishSettings-Datei zu löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können.

### <a name="publish-the-application"></a>Veröffentlichen der Anwendung
Führen Sie zum Veröffentlichen die folgenden Befehle aus:

```powershell
$ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))
Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch
```

* **-ServiceName** gibt den Namen für die Bereitstellung an. Dieser Name muss eindeutig sein, andernfalls schlägt der Veröffentlichungsvorgang fehl. Der Befehl **Get-Date** fügt eine Datum-/Uhrzeit-Zeichenfolge an, die den Namen eindeutig macht.
* **-Location** gibt das Datencenter an, in dem die Anwendung gehostet wird. Um eine Liste der verfügbaren Datencenter anzuzeigen, verwenden Sie das **Get-AzureLocation** -Cmdlet.
* **-Launch** öffnet nach Abschluss der Bereitstellung ein Browserfenster und navigiert zum gehosteten Dienst.

Nach der erfolgreichen Veröffentlichung wird eine Antwort dieser Art angezeigt:

![Das Ergebnis des Publish-AzureService-Befehls][The output of the Publish-AzureService command]

> [!NOTE]
> Es kann nach der ersten Veröffentlichung fünf bis sieben Minuten dauern, bis die Anwendung bereitgestellt wird und verfügbar ist.

Nachdem die Bereitstellung abgeschlossen ist, wird ein Browserfenster geöffnet und der Clouddienst angezeigt.

![Ein Browserfenster mit der Seite „Hello World“. Die URL weist darauf hin, dass die Seite in Azure gehostet wird.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Ihre Anwendung wird jetzt in Azure ausgeführt.

Das Cmdlet **Publish-AzureServiceProject** führt folgende Schritte aus:

1. Erstellt ein Paket zum Bereitstellen. Das Paket enthält alle Dateien im Anwendungsordner.
2. Erstellt ein neues **Speicherkonto** , falls noch keines vorhanden ist. Das Azure-Speicherkonto wird verwendet, um das Anwendungspaket während der Bereitstellung zu speichern. Sie können das Speicherkonto problemlos löschen, wenn die Bereitstellung erfolgt ist.
3. Erstellt einen neuen **Clouddienst** , falls noch keiner vorhanden ist. Ein **Clouddienst** ist der Container, in dem die Anwendung gehostet wird, wenn diese in Azure bereitgestellt wird. Weitere Informationen erhalten Sie unter [Übersicht zum Erstellen eines gehosteten Diensts für Azure].
4. Veröffentlicht das Bereitstellungspaket in Azure.

## <a name="stopping-and-deleting-your-application"></a>Beenden und Löschen Ihrer Anwendung
Nachdem Sie Ihre Anwendung bereitgestellt haben, möchten Sie diese möglicherweise deaktivieren, um Extrakosten zu vermeiden. Azure berechnet Webrolleninstanzen pro Stunde verbrauchter Serverzeit. Serverzeit wird genutzt, sobald die Anwendung bereitgestellt wird, selbst wenn die Instanzen nicht ausgeführt werden und sich im Status Beendet befinden.

1. Stoppen Sie im Windows PowerShell-Fenster die im vorherigen Abschnitt erstellte Dienstbereitstellung mit folgendem Cmdlet:

    ```powershell
    Stop-AzureService
    ```

   Das Stoppen des Diensts kann einige Minuten dauern. Wenn der Dienst gestoppt wurde, erhalten Sie eine entsprechende Nachricht.

   ![Der Status des Stop-AzureService-Befehls][The status of the Stop-AzureService command]
2. Um den Dienst zu löschen, rufen Sie folgendes Cmdlet auf:

    ```powershell
    Remove-AzureService
    ```

   Geben Sie nach entsprechender Aufforderung **Y** ein, um den Dienst zu löschen.

   Das Löschen des Diensts kann einige Minuten dauern. Nachdem der Dienst gelöscht wurde, erhalten Sie eine entsprechende Nachricht.

   ![Der Status des Remove-AzureService-Befehls][The status of the Remove-AzureService command]

   > [!NOTE]
   > Durch das Löschen dieses Diensts wird das Speicherkonto, das beim erstmaligen Veröffentlichen des Diensts erstellt wurde, nicht gelöscht, und Sie erhalten weiterhin eine Rechnung über den verwendeten Speicherplatz. Wird der Speicher nicht anderweitig verwendet, empfiehlt es sich unter Umständen, ihn zu löschen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie im [Node.js Developer Center (in englischer Sprache)].

<!-- URL List -->

[Azure Websites, Cloud Services and Virtual Machines comparison]: /azure/architecture/guide/technology-choices/compute-decision-tree
[Verwendung einer einfachen Web-App]: ../app-service/quickstart-nodejs.md
[Azure PowerShell]: /powershell/azure/
[Azure SDK for .NET 3.0]: https://www.microsoft.com/download/details.aspx?id=54917
[Stellen Sie eine PowerShell-Verbindung mit Ihrem Abonnement her.]: /powershell/azure/
[nodejs.org]: https://nodejs.org/
[Übersicht zum Erstellen eines gehosteten Diensts für Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js Developer Center (in englischer Sprache)]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png



