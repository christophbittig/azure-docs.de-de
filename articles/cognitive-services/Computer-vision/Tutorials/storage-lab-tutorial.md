---
title: 'Tutorial: Generieren von Metadaten für Azure-Images'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial wird beschrieben, wie Sie den Azure-Dienst „Maschinelles Sehen“ in eine Web-App integrieren, um Metadaten für Bilder zu generieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 07/06/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: f97f01ecb1b11af6ca4292c8b2bfd3ebdb50e943
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360254"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Verwenden von „Maschinelles Sehen“ zum Generieren von Bildmetadaten in Azure Storage

In diesem Tutorial wird beschrieben, wie Sie den Azure-Dienst „Maschinelles Sehen“ in eine Web-App integrieren, um Metadaten für hochgeladene Bilder zu generieren. Dies ist für [Digital Asset Management](../overview.md#computer-vision-for-digital-asset-management)-Szenarien (DAM) nützlich, wenn ein Unternehmen z. B. beschreibende Titel oder durchsuchbare Schlüsselwörter für alle seine Bilder schnell generieren möchte.

Sie verwenden Visual Studio, um eine MVC-Web-App zu schreiben, die von Benutzern hochgeladene Bilder akzeptiert und die Bilder in Azure Blob Storage speichert. Sie erfahren, wie Sie Blobs in C# lesen und schreiben und Blobmetadaten verwenden, um zusätzliche Informationen an die von Ihnen erstellten Blobs anfügen. Anschließend übermitteln Sie jedes vom Benutzer hochgeladene Bild an die Maschinelles Sehen-API, um eine Beschriftung und Suchmetadaten für das Bild zu generieren. Abschließend können Sie die App mithilfe von Visual Studio in der Cloud bereitstellen.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Erstellen eines Speicherkontos und Speichercontainers mit Azure-Portal
> * Erstellen einer Web-App in Visual Studio und Bereitstellen in Azure
> * Verwendung der Maschinelles Sehen-API zur Extraktion von Informationen aus Bildern
> * Anfügen von Metadaten an Azure Storage-Bilder
> * Überprüfen von Bildmetadaten mit [Azure Storage-Explorer](http://storageexplorer.com/)

> [!TIP]
> Der Abschnitt [Verwenden von maschinellem Sehen zur Erstellung von Metadaten](#Exercise5) ist für die Bildanalyse am relevantesten. Gehen Sie direkt zu diesem Abschnitt, wenn Sie nur sehen möchten, wie die Bildanalyse in eine eingerichtete Anwendung integriert ist.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) oder höher mit Installation der Workloads „ASP.NET und Webentwicklung“ und „Azure-Entwicklung“.
- Das Tool [Azure Storage-Explorer](http://storageexplorer.com/) ist installiert.

<a name="Exercise1"></a>
## <a name="create-a-storage-account"></a>Speicherkonto erstellen

In diesem Abschnitt nutzen Sie [Azure-Portal](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa) zur Erstellung eines Speicherkontos. Anschließend erstellen Sie ein Containerpaar: einen zum Speichern von Bildern, die vom Benutzer hochgeladen wurden, und einen anderen zum Speichern von Bildminiaturansichten, die aus den hochgeladenen Bildern generiert wurden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa) in Ihrem Browser. Wenn Sie aufgefordert werden, sich anzumelden, verwenden Sie dafür Ihr Microsoft-Konto.
1. Zum Erstellen eines Speicherkontos klicken Sie im Menüband auf der linken Seite auf **+ Ressource erstellen**. Klicken Sie auf **Speicher** und anschließend auf **Speicherkonto**.

    ![Erstellen eines Speicherkontos](Images/new-storage-account.png)
1. Geben Sie im Feld **Name** einen eindeutigen Namen für das Speicherkonto ein und stellen Sie sicher, dass daneben ein grünes Häkchen angezeigt wird. Der Name ist wichtig, da er einen Teil der URL bildet, über die auf Blobs zugegriffen wird, die unter diesem Konto erstellt wurden. Platzieren Sie das Speicherkonto in einer neuen Ressourcengruppe namens „IntellipixResources“ und wählen Sie die nächstgelegene Region aus. Klicken Sie anschließend unten im Bildschirm auf die Schaltfläche **Überprüfen + erstellen**, um das neue Speicherkonto zu erstellen.
    > [!NOTE]
    > Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Darüber hinaus muss der von Ihnen eingeben Name innerhalb von Azure eindeutig sein. Wenn eine andere Person denselben Namen gewählt hat, werden Sie benachrichtigt, dass der Name nicht mit einem roten Ausrufezeichen im Feld **Name verfügbar** ist.
   
    ![Angeben von Parametern für ein neues Speicherkonto](Images/create-storage-account.png)
1. Klicken Sie im Menüband auf der linken Seite auf **Ressourcengruppen**. Dann klicken Sie auf die Ressourcengruppe „IntellipixResources“.

    ![Öffnen der Ressourcengruppe](Images/open-resource-group.png)
1. Klicken Sie auf der Registerkarte, die für die Ressourcengruppe geöffnet wird, auf das Speicherkonto, das Sie erstellt haben. Wenn das Speicherkonto dort noch nicht verfügbar ist, können Sie oben in Registerkarte auf **Aktualisieren** klicken, bis das Speicherkonto angezeigt wird.

    ![Öffnen des neuen Speicherkontos](Images/open-storage-account.png)
1. Klicken Sie in Registerkarte für das Speicherkonto auf **Blobs**, um eine Liste der Container anzuzeigen, die diesem Konto zugeordnet sind.

    ![Schaltfläche zum Anzeigen von Blobs](Images/view-containers.png)

1. Das Speicherkonto verfügt derzeit über keine Container. Bevor Sie ein Blob hochladen können, müssen Sie einen Container erstellen, in dem der Blob gespeichert werden kann. Klicken Sie auf **+ Container**, um einen neuen Container zu erstellen. Geben `photos` Sie in das Feld **Name** ein und wählen Sie **Blob** als **öffentliche Zugriffsebene** aus. Klicken Sie dann auf **OK,** um einen Container namens „Fotos“ zu erstellen.

    > Standardmäßig sind Container und ihre Inhalte privat. Wenn Sie **Blob** als Zugriffsebene auswählen, werden die Blobs im Container „Fotos“ öffentlich zugänglich, der Container selbst wird jedoch nicht öffentlich gemacht. Dies ist das, was Sie erreichen möchten, da die im Container „Fotos“ gespeicherten Bilder aus einer Web-App mit verknüpft werden. 

    ![Erstellen eines „Fotos“-Containers](Images/create-photos-container.png)

1. Wiederholen Sie den vorherigen Schritt, um einen Container mit dem Namen „Miniaturansichten“ zu erstellen, und stellen Sie dabei sicher, dass die **öffentliche Zugriffsebene** des Containers auf **Blob** eingestellt ist.
1. Vergewissern Sie sich, dass beide Container in der Liste der Container für dieses Speicherkonto angezeigt werden und dass die Namen richtig geschrieben sind.

    ![Die neuen Container](Images/new-containers.png)

1. Schließen Sie den Bildschirm „Blob-Dienst“. Klicken Sie im Menü auf der linken Seite des Speicherkontobildschirms auf **Zugriffsschlüssel**, und dann auf die Schaltfläche **Kopieren** neben **SCHLÜSSEL** für **Schlüssel1**. Fügen Sie diesen Zugriffsschlüssel zur späteren Verwendung in Ihren bevorzugten Text-Editor ein.

    ![Kopieren des Zugriffsschlüssels](Images/copy-storage-account-access-key.png)

Sie haben nun sowohl ein Speicherkonto zum Speichern von Bildern erstellt, die in die App hochgeladen wurden, die Sie erstellen möchten, als auch Container zum Speichern der Bilder. 

<a name="Exercise2"></a>
## <a name="run-azure-storage-explorer"></a>Ausführen von Azure Storage-Explorer

[Azure Storage-Explorer](http://storageexplorer.com/) ist ein kostenloses Tool, das eine grafische Benutzeroberfläche für die Arbeit mit Azure Storage auf PCs mit Windows, macOS und Linux bietet. Es bietet die meisten Funktionen wie Azure-Portal und darüber hinaus weitere Features, z. B. die Möglichkeit zum Anzeigen von Blobmetadaten. In diesem Abschnitt verwenden Sie Microsoft Azure Storage-Explorer, um die Container anzuzeigen, die Sie im vorherigen Abschnitt erstellt haben.

1. Wenn Sie Storage-Explorer installiert haben oder sicherstellen möchten, dass Sie die neueste Version ausführen, wechseln Sie zu http://storageexplorer.com/, laden Sie das Programm herunter und installieren Sie es.
1. Starten Sie den Storage-Explorer. Wenn Sie aufgefordert werden, sich zu anmelden, verwenden Sie hierzu das gleiche Microsoft-Konto&mdash;, das Sie für die Anmeldung beim Azure-Portal genutzt haben. Wenn das Speicherkonto im linken Bereich von Storage-Explorer nicht angezeigt wird, klicken Sie unten auf die Schaltfläche **Konten verwalten**, und stellen Sie sicher, dass sowohl Ihr Microsoft-Konto als auch das Abonnement, das zum Erstellen des Speicherkontos verwendet wurde, zu Storage-Explorer hinzugefügt wurden.

    ![Verwalten von Konten in Storage-Explorer](Images/add-account.png)

1. Klicken Sie auf den kleinen Pfeil neben dem Speicherkonto, um dessen Inhalt anzuzeigen, und klicken Sie dann auf den Pfeil neben **Blobcontainer**. Vergewissern Sie sich, dass die von Ihnen erstellten Container in der Liste angezeigt werden.

    ![Anzeigen von Blobcontainern](Images/storage-explorer.png)

Die Container sind derzeit leer, aber dies ändert sich, nachdem Ihre App bereitgestellt wurde und Sie mit dem Hochladen von Fotos beginnen. Wenn Storage-Explorer installiert ist, können Sie leicht sehen, was Ihre App in den Blobspeicher schreibt.

<a name="Exercise3"></a>
## <a name="create-a-new-web-app-in-visual-studio"></a>Erstellen einer neuen Web-App in Visual Studio

In diesem Abschnitt erstellen Sie eine neue Web-App in Visual Studio und fügen Code hinzu, um die grundlegende Funktionalität zu implementieren, die zum Hochladen von Bildern, zum Schreiben dieser in den Blobspeicher und zum Anzeigen auf einer Webseite erforderlich ist.

1. Starten Sie Visual Studio und verwenden Sie den Befehl **Datei -> Neu -> Projekt**, um ein neues Visual C# **ASP.NET-Webanwendungsprojekt** mit dem Namen „Intellipix“ (kurz für „Intelligent Pictures“) zu erstellen.

    ![Erstellen eines neuen Projekts vom Typ Web-Anwendung](Images/new-web-app.png)

1. Klicken Sie im Dialogfeld „Neue ASP.NET-Webanwendung“ auf **MVC**. Klicken Sie dann auf **OK**.

    ![Neues ASP.NET MVC-Projekt erstellen](Images/new-mvc-project.png)

1. Nehmen Sie sich einen Moment Zeit, um die Projektstruktur im Projektmappen-Explorer zu bewerten. Es gibt unter anderem einen Ordner namens **Controller**, der die MVC-Controller des Projekts enthält, und einen Ordner namens **Ansichten**, der die Ansichten des Projekts enthält. Während Sie die Anwendung implementieren, arbeiten Sie mit Ressourcen in diesen und anderen Ordnern.

    ![Das Projekt, das im Projektmappen-Explorer angezeigt wird.](Images/project-structure.png)

1. Verwenden Visual Studio Befehl **Debuggen -> Starten ohne Debuggen** (oder drücken Sie **STRG+F5**), um die Anwendung in Ihrem Browser zu starten. So sieht die Anwendung im aktuellen Zustand aus:

    ![Die erste Anwendung](Images/initial-application.png)

1. Schließen Sie den Browser und kehren Sie zu Visual Studio zurück. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **Intellipix** und wählen Sie **NuGet-Pakete verwalten...** aus. Klicken Sie nun auf **Browsen**. Geben Sie dann `imageresizer` in das Suchfeld ein und wählen Sie das NuGet-Paket mit dem Namen **ImageResizer** aus. Wählen Sie nun **Installieren**, um die neueste stabile Version des Pakets zu installieren. ImageResizer enthält APIs, mit denen Sie Miniaturansichten von Bildern aus den Bilder erstellen können, die in die App hochgeladen wurden. Bestätigen Sie alle Änderungen und akzeptieren Sie alle Ihnen präsentierten Lizenzen.

    ![Installieren von ImageResizer](Images/install-image-resizer.png)

1. Wiederholen Sie diesen Vorgang, um das NuGet-Paket **WindowsAzure.Storage** zum Projekt hinzuzufügen. Dieses Paket enthält APIs für den Zugriff auf Azure Storage aus .NET-Anwendungen. Bestätigen Sie alle Änderungen und akzeptieren Sie alle Ihnen präsentierten Lizenzen.

    ![Installieren von WindowsAzure.Storage](Images/install-storage-package.png)

1. Öffnen _Web.config_ und fügen Sie dem Abschnitt ```<appSettings>``` die folgende Anweisung hinzu. Ersetzen Sie dabei ACCOUNT_NAME durch den Namen des Speicherkontos, das Sie im ersten Abschnitt erstellt haben, und ACCOUNT_KEY durch den von Ihnen gespeicherten Zugriffsschlüssel.

    ```xml
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=ACCOUNT_NAME;AccountKey=ACCOUNT_KEY" />
    ```

1. Öffnen Sie die Datei *_Layout.cshtml* im Projektordner **Ansichten/Shared**. Ändern Sie in Zeile 19 „Anwendungsname“ in „Intellipix“. Die Zeile sollte wie folgt aussehen:

    ```C#
    @Html.ActionLink("Intellipix", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    > [!NOTE]
    > In einem ASP.NET MVC-Projekt ist *_Layout.cshtml* eine spezielle Ansicht, die als Vorlage für andere Ansichten dient. In der Regel definieren Sie Kopf- und Fußzeileninhalte, die allen Ansichten in dieser Datei gemeinsam sind.

1. Klicken Sie mit der rechten Maustaste auf den Projektordner **Modelle** und verwenden Sie den Befehl **Hinzuzufügen -> Klasse...** , um dem Ordner eine Klassendatei mit dem Namen *BlobInfo.cs* hinzuzufügen. Ersetzen Sie dann die leere **BlobInfo**-Klasse durch die folgende Klassendefinition:

    ```C#
    public class BlobInfo
    {
        public string ImageUri { get; set; }
        public string ThumbnailUri { get; set; }
        public string Caption { get; set; }
    }
    ```

1. Öffnen Sie die *HomeController.cs* im Projektordner **Controllers** und fügen Sie oben in der Datei die folgenden `using`-Anweisungen hinzu:

    ```C#
    using ImageResizer;
    using Intellipix.Models;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Configuration;
    using System.Threading.Tasks;
    using System.IO;
    ```

1. Ersetzen Sie die **Index**-Methode in *HomeController.cs* durch die folgende Implementierung:

    ```C#
    public ActionResult Index()
    {
        // Pass a list of blob URIs in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();
    
        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;
    
            if (blob != null)
            {
                blobs.Add(new BlobInfo()
                {
                    ImageUri = blob.Uri.ToString(),
                    ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/")
                });
            }
        }
    
        ViewBag.Blobs = blobs.ToArray();
        return View();
    }
    ```

    Die neue **Index**-Methode führt die Blobs im `"photos"`Container auf und übergibt ein Array von **BlobInfo**-Objekten, die diese Blobs darstellen, über die **ViewBag**-Eigenschaft von ASP.NET MVC an die Ansicht. Später ändern Sie die Ansicht, um diese Objekte aufzuführen und eine Auflistung von Fotominiaturansichten anzuzeigen. Die Klassen, die Sie verwenden, um auf Ihr Speicherkonto zuzugreifen und die Blobs&mdash; **[CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet&preserve-view=true)** , **[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient?view=azure-dotnet-legacy&preserve-view=true)** und **[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer?view=azure-dotnet-legacy&preserve-view=true)** aufzuführen, stammen aus dem &mdash;**WindowsAzure.Storage**-Paket, das Sie über NuGet installiert haben.

1. Fügen Sie der **HomeController**-Klasse in *HomeController.cs* die folgende Methode hinzu:

    ```C#
    [HttpPost]
    public async Task<ActionResult> Upload(HttpPostedFileBase file)
    {
        if (file != null && file.ContentLength > 0)
        {
            // Make sure the user selected an image file
            if (!file.ContentType.StartsWith("image"))
            {
                TempData["Message"] = "Only image files may be uploaded";
            }
            else
            {
                try
                {
                    // Save the original image in the "photos" container
                    CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
                    CloudBlobClient client = account.CreateCloudBlobClient();
                    CloudBlobContainer container = client.GetContainerReference("photos");
                    CloudBlockBlob photo = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                    await photo.UploadFromStreamAsync(file.InputStream);
    
                    // Generate a thumbnail and save it in the "thumbnails" container
                    using (var outputStream = new MemoryStream())
                    {
                        file.InputStream.Seek(0L, SeekOrigin.Begin);
                        var settings = new ResizeSettings { MaxWidth = 192 };
                        ImageBuilder.Current.Build(file.InputStream, outputStream, settings);
                        outputStream.Seek(0L, SeekOrigin.Begin);
                        container = client.GetContainerReference("thumbnails");
                        CloudBlockBlob thumbnail = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                        await thumbnail.UploadFromStreamAsync(outputStream);
                    }
                }
                catch (Exception ex)
                {
                    // In case something goes wrong
                    TempData["Message"] = ex.Message;
                }
            }
        }
    
        return RedirectToAction("Index");
    }
    ```

    Dies ist die Methode, die aufgerufen wird, wenn Sie ein Foto hochladen. Es speichert jedes hochgeladene Bild als Blob im `"photos"` Container, erstellt mithilfe des `ImageResizer` Pakets ein Miniaturbild aus dem originalen Bild und speichert das Miniaturbild als Blob im `"thumbnails"` Container.

1. Öffnen Sie *Index.cshmtl* im Projektordner **Ansichten/Home** und ersetzen Sie dessen Inhalt durch folgenden Code und Markup:

    ```HTML
    @{
        ViewBag.Title = "Intellipix Home Page";
    }
    
    @using Intellipix.Models
    
    <div class="container" style="padding-top: 24px">
        <div class="row">
            <div class="col-sm-8">
                @using (Html.BeginForm("Upload", "Home", FormMethod.Post, new { enctype = "multipart/form-data" }))
                {
                    <input type="file" name="file" id="upload" style="display: none" onchange="$('#submit').click();" />
                    <input type="button" value="Upload a Photo" class="btn btn-primary btn-lg" onclick="$('#upload').click();" />
                    <input type="submit" id="submit" style="display: none" />
                }
            </div>
            <div class="col-sm-4 pull-right">
            </div>
        </div>
    
        <hr />
    
        <div class="row">
            <div class="col-sm-12">
                @foreach (BlobInfo blob in ViewBag.Blobs)
                {
                    <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
                }
            </div>
        </div>
    </div>
    
    @section scripts
    {
        <script type="text/javascript" language="javascript">
            if ("@TempData["Message"]" !== "") {
                alert("@TempData["Message"]");
            }
        </script>
    }
    ```

    Die hier verwendete Sprache ist [Razor](http://www.asp.net/web-pages/overview/getting-started/introducing-razor-syntax-c), mit der Sie ausführbaren Code in HTML-Markup einbetten können. Die ```@foreach```-Anweisung in der Mitte der Datei führt die **BlobInfo**-Objekte auf, die vom Controller in **ViewBag** übergeben werden und erstellt aus ihnen HTML-```<img>```-Elemente. Die ```src```-Eigenschaft jedes Elements wird mit dem URI des Blobs initialisiert, das die Miniaturansicht des Bilds enthält.

1. Laden Sie die Datei _photos.zip_ aus [GitHub-Beispieldaten-Repository](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial) herunter und entzippen Sie sie. Es handelt sich hierbei um eine Reihe verschiedener Fotos, die Sie zum Testen der App verwenden können.

1. Speichern Sie Ihre Änderungen und drücken Sie **STRG+F5**, um die Anwendung in Ihrem Browser zu starten. Klicken Sie dann **Ein Foto hochladen** und laden Sie eines der heruntergeladenen Bilder hoch. Vergewissern Sie sich, dass auf der Seite eine Miniaturansicht des Fotos angezeigt wird.

    ![Intellipix mit einem hochgeladenen Foto](Images/one-photo-uploaded.png)

1. Laden Sie einige weitere Bilder aus Ihrem **Fotos**-Ordner hoch. Vergewissern Sie sich, dass sie auch auf der Seite angezeigt werden:

    ![Intellipix mit drei hochgeladenen Fotos](Images/three-photos-uploaded.png)

1. Klicken Sie mit der rechten Maustaste in Ihren Browser und wählen Sie **Seitenquelle anzeigen** aus, um den Quellcode für die Seite anzuzeigen. Suchen Sie die ```<img>``` Elemente, die die Miniaturansichten des Bilds darstellen. Beachten Sie, dass die den Bildern zugewiesenen URLs direkt auf Blobs im Blobspeicher verweisen. Dies liegt daran, dass Sie die **öffentliche Zugriffsebene** der Container auf **Blob** festgelegt haben, wodurch die darin gespeicherten Blobs öffentlich zugänglich werden.

1. Kehren Sie zum Azure Storage-Explorer zurück (oder starten Sie ihn neu, wenn Sie ihn nicht ausgeführt haben), und wählen Sie den Container `"photos"` unter Ihrem Speicherkonto aus. Die Anzahl der Blobs im Container sollte der Anzahl der hochgeladenen Fotos entsprechen. Klicken Sie doppelt auf einen der Blobs, um diesen herunterzuladen und das im Blob gespeicherte Bild anzuzeigen.

    ![Inhalt des „Fotos“-Containers](Images/photos-container.png)

1. Öffnen Sie den Container `"thumbnails"` in Storage-Explorer. Öffnen Sie einen der Blobs, um die Miniaturbilder anzuzeigen, die aus den Bilduploads generiert wurden.

Die App bietet noch keine Möglichkeit zum Anzeigen der Originalbilder, die Sie hochgeladen haben. Im Idealfall sollte das Originalbild angezeigt werden, wenn Sie auf eine Miniaturansicht klicken. Dieses Feature fügen Sie als nächstes hinzu.

<a name="Exercise4"></a>
## <a name="add-a-lightbox-for-viewing-photos"></a>Hinzufügen einer Lightbox zum Anzeigen von Fotos

In diesem Abschnitt verwenden Sie eine kostenlose Open-Source-JavaScript-Bibliothek, um einen Lightbox-Viewer hinzuzufügen, der es Benutzern ermöglicht, die hochgeladenen Originalbilder (nicht nur die Miniaturansichten) anzuzeigen. Die Dateien werden für Sie bereitgestellt. Sie müssen sie nur in das Projekt integrieren und eine geringfügige Änderung an *Index.cshtml* vornehmen.

1. Laden Sie die Dateien _lightbox.css_ und _lightbox.js_ aus dem [GitHub-Code-Repository](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/storage-lab-tutorial) herunter.
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektordner **Skripts** und verwenden Sie den Befehl **Hinzufügen -> Neues Element...** , um eine *lightbox.js*-Datei zu erstellen. Fügen Sie den Inhalt aus der Beispieldatei in das [GitHub-Code-Repository](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/scripts/lightbox.js) ein.

1. Klicken Sie mit der rechten Maustaste auf den Projektordner „Inhalt“ und erstellen Sie mit dem Befehl **Hinzufügen -> Neues Element...** eine *lightbox.css*-Datei. Fügen Sie den Inhalt aus der Beispieldatei in das [GitHub-Code-Repository](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/css/lightbox.css) ein.
1. Laden Sie die Datei _photos.zip_ aus dem folgenden GitHub-Datendatei-Repository herunter und entzippen Sie sie: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial. Sie sollten über vier Schaltflächenbilder verfügen.

1. Klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf das Intellipix-Projekt und verwenden Sie den Befehl **Hinzufügen -> Neuer Ordner**, um dem Projekt einen Ordner mit dem Namen „Bilder“ hinzuzufügen.

1. Klicken Sie mit der rechten Maustaste auf den Ordner **Bilder** und verwenden Sie den Befehl **Hinzufügen -> Vorhandenes Element...** , um die vier heruntergeladenen Bilder zu importieren.

1. Öffnen Sie im Projektordner „App_Start“ die Datei *BundleConfig.cs*. Fügen Sie der ```RegisterBundles```-Methode in **BundleConfig.cs** folgende Anweisung zu hinzu:

    ```C#
    bundles.Add(new ScriptBundle("~/bundles/lightbox").Include(
              "~/Scripts/lightbox.js"));
    ```

1. Suchen Sie in derselben Methode die Anweisung, die ein ```StyleBundle``` aus „~/Content/css“ erstellt und fügen Sie *lightbox.css* zur Liste der Stylesheets im Paket hinzu. Dies ist die geänderte Anweisung:

    ```C#
    bundles.Add(new StyleBundle("~/Content/css").Include(
              "~/Content/bootstrap.css",
              "~/Content/site.css",
              "~/Content/lightbox.css"));
    ```

1. Öffnen *_Layout.cshtml* im Projektordner **Ansichten/Shared** und fügen Sie die folgende Anweisung direkt vor der ```@RenderSection```-Anweisung in der Nähe der Unterseite hinzu:

    ```C#
    @Scripts.Render("~/bundles/lightbox")
    ```

1. Die letzte Aufgabe besteht darin, den Lightbox-Viewer in die Homepage zu integrieren. Öffnen Sie hierzu *Index.cshtml* (im Projektordner **Ansichten/Home**) und ersetzen Sie die ```@foreach```-Schleife durch folgende:

    ```HTML
    @foreach (BlobInfo blob in ViewBag.Blobs)
    {
        <a href="@blob.ImageUri" rel="lightbox" title="@blob.Caption">
            <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
        </a>
    }
    ```

1. Speichern Sie Ihre Änderungen und drücken Sie **STRG+F5**, um die Anwendung in Ihrem Browser zu starten. Klicken Sie dann auf eines der Bilder, die Sie zuvor hochgeladen haben. Vergewissern Sie sich, dass eine Lightbox und eine vergrößerte Ansicht des Bilds angezeigt wird.

    ![Vergrößertes Bild](Images/lightbox-image.png)

1. Klicken Sie auf das **X** in der unteren rechten Ecke der Lightbox, um die zu schließen.

Nun können Sie die hochgeladenen Bilder anzeigen. Der nächste Schritt besteht in der weiteren Verarbeitung dieser Bilder.

<a name="Exercise5"></a>
## <a name="use-computer-vision-to-generate-metadata"></a>Verwenden von maschinellem Sehen zur Erstellung von Metadaten

### <a name="create-a-computer-vision-resource"></a>Erstellen einer Maschinelles Sehen-Ressource

Sie müssen für Ihr Azure-Konto eine Maschinelles Sehen-Ressource erstellen. Mit dieser Ressource wird der Zugriff auf den Azure-Dienst „Maschinelles Sehen“ verwaltet. 

1. Befolgen Sie die Anweisungen in [Erstellen einer Azure Cognitive Services-Ressource](../../cognitive-services-apis-create-account.md), um eine Ressource für maschinelles Sehen zu erstellen.

1. Kehren Sie dann zum Menü für Ihre Ressourcengruppe zurück, und klicken Sie auf das Abonnement der Maschinelles Sehen-API, das Sie erstellt haben. Kopieren Sie die URL unter **Endpunkt** an einen Ort, an dem Sie gleich schnell darauf zugreifen können. Klicken Sie anschließend auf **Zugriffsschlüssel anzeigen**.

    ![Azure-Portalseite mit Hervorhebung der Endpunkt-URL und des Links für Zugriffsschlüssel](Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Kopieren Sie im nächsten Fenster den Wert von **SCHLÜSSEL 1** in die Zwischenablage.

    ![Dialogfeld „Schlüssel verwalten“ mit Hervorhebung der Schaltfläche „Kopieren“](Images/copy-vision-key.png)

### <a name="add-computer-vision-credentials"></a>Hinzufügen von Anmeldeinformationen für Maschinelles Sehen

Als Nächstes fügen Sie Ihrer App die erforderlichen Anmeldeinformationen hinzu, damit der Zugriff auf Maschinelles Sehen-Ressourcen möglich ist.

Navigieren Sie zur Datei *Web.config* im Stamm des Projekts. Fügen Sie dem Abschnitt `<appSettings>` der Datei die folgenden Anweisungen hinzu, und ersetzen Sie dabei `VISION_KEY` durch den Schlüssel, den Sie im vorherigen Schritt kopiert haben, und `VISION_ENDPOINT` durch die URL, die Sie im Schritt davor gespeichert haben.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Klicken Sie im Projektmappen-Explorer dann mit der rechten Maustaste auf das Projekt, und verwenden Sie den Befehl **NuGet-Pakete verwalten**, um das Paket **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** zu installieren. Dieses Paket enthält die Typen, die zum Aufrufen der Maschinelles Sehen-API benötigt werden.

### <a name="add-metadata-generation-code"></a>Hinzufügen des Codes zum Generieren von Metadaten

Als Nächstes fügen Sie den Code hinzu, der den Dienst „Maschinelles Sehen“ nutzt, um Metadaten für Bilder zu erstellen.

1. Öffnen Sie die Datei *HomeController.cs* im Ordner **Controllers** des Projekts, und fügen Sie oben in der Datei die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Fahren Sie dann mit der **Upload**-Methode fort. Hierbei werden Bilder konvertiert und in den Blobspeicher hochgeladen. Fügen Sie den folgenden Code direkt nach dem Block hinzu, der mit `// Generate a thumbnail` beginnt (oder am Ende Ihres Prozesses für die Erstellung von Bildblobs). In diesem Code wird für das Blob, in dem das Bild (`photo`) enthalten ist, Maschinelles Sehen verwendet, um eine Beschreibung für das Bild zu generieren. Mit der Maschinelles Sehen-API wird auch eine Liste mit Schlüsselwörtern generiert, die auf das Bild angewendet werden. Die generierte Beschreibung und die Schlüsselwörter werden in den Metadaten des Blobs gespeichert, damit sie später abgerufen werden können.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>() { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Navigieren Sie als Nächstes in derselben Datei zur **Index**-Methode. Mit dieser Methode werden die gespeicherten Bildblobs im betreffenden Blobcontainer enumeriert (als **IListBlobItem**-Instanzen) und an die Anwendungsansicht übergeben. Ersetzen Sie den `foreach`-Block in dieser Methode durch den folgenden Code. Mit diesem Code wird **CloudBlockBlob.FetchAttributes** aufgerufen, um die angefügten Metadaten für jedes Blob abzurufen. Die per Computer generierte Beschreibung (`caption`) wird aus den Metadaten extrahiert und dem **BlobInfo**-Objekt hinzugefügt, das dann an die Ansicht übergeben wird.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

### <a name="test-the-app"></a>Testen der App

Speichern Sie Ihre Änderungen in Visual Studio, und drücken Sie **STRG+F5**, um die Anwendung in Ihrem Browser zu starten. Verwenden Sie die App, um einige weitere Bilder hochzuladen, entweder aus dem heruntergeladenen Fotosatz oder aus Ihrem eigenen Ordner. Wenn Sie den Mauszeiger in der Ansicht auf eines der neuen Bilder bewegen, sollte ein QuickInfo-Fenster mit dem per Computer generierten Beschriftungstext für das Bild angezeigt werden.

![Per Computer generierter Beschriftungstext](Images/thumbnail-with-tooltip.png)

Verwenden Sie zum Anzeigen aller angefügten Metadaten Azure Storage-Explorer, um den Speichercontainer einzublenden, den Sie für Bilder nutzen. Klicken Sie mit der rechten Maustaste auf die Blobs im Container, und wählen Sie **Eigenschaften**. Im Dialogfeld wird eine Liste mit Schlüssel-Wert-Paaren angezeigt. Die per Computer generierte Bildbeschreibung wird unter dem Element `Caption` gespeichert, und die Schlüsselwörter für die Suche unter `Tag0`, `Tag1` usw. Klicken Sie auf **Abbrechen**, wenn Sie fertig sind, um das Dialogfeld zu schließen.

![Dialogfenster mit Image-Eigenschaften und aufgeführten Metadatentags](Images/blob-metadata.png)

<a name="Exercise6"></a>
## <a name="add-search-to-the-app"></a>Hinzufügen der Suchfunktion zu Ihrer App

In diesem Abschnitt fügen Sie der Homepage ein Suchfeld hinzu, mit dem Benutzer Schlüsselwortsuchen für die hochgeladenen Bilder durchführen können. Die Schlüsselwörter werden von der maschinelles Sehen-API generiert und in Blobmetadaten gespeichert.

1. Öffnen Sie *Index.cshtml* im Projektordner **Ansichten/Home** und fügen Sie dem leeren ```<div>```-Element mit dem ```class="col-sm-4 pull-right"```-Attribut die folgenden Anweisungen hinzu:

    ```HTML
    @using (Html.BeginForm("Search", "Home", FormMethod.Post, new { enctype = "multipart/form-data", @class = "navbar-form" }))
    {
        <div class="input-group">
            <input type="text" class="form-control" placeholder="Search photos" name="term" value="@ViewBag.Search" style="max-width: 800px">
            <span class="input-group-btn">
                <button class="btn btn-primary" type="submit">
                    <i class="glyphicon glyphicon-search"></i>
                </button>
            </span>
        </div>
    }
    ```

    Dieser Code und das Markup fügt der Homepage ein Suchfeld und eine Schaltfläche **Suchen** hinzu.

1. Öffnen Sie die *HomeController.cs* im Projektordner **Controllers** und fügen Sie der **HomeController**-Klasse die folgende Methode hinzu:

    ```C#
    [HttpPost]
    public ActionResult Search(string term)
    {
        return RedirectToAction("Index", new { id = term });
    }
    ```

    Dies ist die Methode, die aufgerufen wird, wenn der Benutzer auf die Schaltfläche **Suchen** klickt, die im vorherigen Schritt hinzugefügt wurde. Damit wird die Seite aktualisiert, sodass diese nun einen Suchparameter in der URL enthält.

1. Ersetzen Sie die **Index**-Methode durch die folgende Implementierung:

    ```C#
    public ActionResult Index(string id)
    {
        // Pass a list of blob URIs and captions in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();

        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;

            if (blob != null)
            {
                blob.FetchAttributes(); // Get blob metadata

                if (String.IsNullOrEmpty(id) || HasMatchingMetadata(blob, id))
                {
                    var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;

                    blobs.Add(new BlobInfo()
                    {
                        ImageUri = blob.Uri.ToString(),
                        ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                        Caption = caption
                    });
                }
            }
        }

        ViewBag.Blobs = blobs.ToArray();
        ViewBag.Search = id; // Prevent search box from losing its content
        return View();
    }
    ```

    Beachten Sie, dass die **Index**-Methode jetzt eine Parameter-_ID_ akzeptiert, die den vom Benutzer in das Suchfeld eingegebenen Wert enthält. Ein leerer oder fehlender _ID_-Parameter bedeutet, dass alle Fotos angezeigt werden sollen.

1. Fügen Sie der **HomeController**-Klasse die folgende Methode hinzu:

    ```C#
    private bool HasMatchingMetadata(CloudBlockBlob blob, string term)
    {
        foreach (var item in blob.Metadata)
        {
            if (item.Key.StartsWith("Tag") && item.Value.Equals(term, StringComparison.InvariantCultureIgnoreCase))
                return true;
        }

        return false;
    }
    ```

    Diese Methode wird von der **Index**-Methode aufgerufen, um zu bestimmen, ob die Metadatenschlüsselwörter, die an ein bestimmtes Bildblob angefügt sind, den vom Benutzer eingegebenen Suchbegriff enthalten.

1. Starten Sie die Anwendung erneut und laden Sie mehrere Fotos hoch. Sie können ihre eigenen Fotos verwenden, nicht nur diejenigen, die im Tutorial bereitgestellt werden.

1. Geben Sie ein Schlüsselwort wie „Fluss“ in das Suchfeld ein. Klicken Sie dann auf die Schaltfläche **Suchen**.

    ![Durchführen einer Suche](Images/enter-search-term.png)

1. Die Suchergebnisse hängen davon ab, was Sie eingeben und welche Bilder Sie hochgeladen haben. Das Ergebnis sollte jedoch eine gefilterte Liste von Bildern sein, deren Metadatenschlüsselwörter das eingegebene Schlüsselwort ganz oder teilweise enthalten.

    ![Suchergebnisse](Images/search-results.png)

1. Klicken Sie auf die Zurück-Schaltfläche des Browsers, um alle Bilder erneut anzuzeigen.

Sie haben es fast geschafft. Es ist an der Zeit, die App in der Cloud bereitzustellen.

<a name="Exercise7"></a>
## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

In diesem Abschnitt stellen Sie die App in Azure aus Visual Studio bereit. Sie erlauben Visual Studio, eine Azure-Web-App für Sie zu erstellen, um zu verhindern, dass Sie in das Azure-Portal gehen und diese dort selbst erstellen müssen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt und wählen Sie im Kontextmenü **Veröffentlichen...** aus. Stellen Sie sicher, dass **Microsoft Azure App Service** und **Neu erstellen** ausgewählt sind und klicken Sie dann auf die Schaltfläche **Veröffentlichen**.

    ![Veröffentlichen der App](Images/publish-1.png)

1. Wählen Sie im nächsten Dialogfeld unter **Ressourcengruppe** die Ressourcengruppe „IntellipixResources“ aus. Klicken Sie auf die Schaltfläche **Neu...** neben „App Service-Plan“ und erstellen Sie einen neuen App Service-Plan an dem Speicherort, den Sie unter [Erstellen eines Speicherkontos](#Exercise1) für das Speicherkonto ausgewählt haben, und übernehmen Sie die Standardwerte überall sonst. Beenden Sie den Vorgang durch Klicken auf die Schaltfläche **Erstellen**.

    ![Erstellen einer Azure-Web-App](Images/publish-2.png)

1. Nach einigen Augenblicken wird die App in einem Browserfenster angezeigt. Notieren Sie die URL in der Adressleiste. Die App wird nicht mehr lokal ausgeführt. Sie ist im Web verfügbar, wo sie öffentlich zugänglich ist.

    ![Das fertige Produkt!](Images/vs-intellipix.png)

Wenn Sie Änderungen an der App vornehmen und die Änderungen in das Web pushen möchten, können Sie den Veröffentlichungsprozess erneut durchlaufen. Sie können Ihre Änderungen weiterhin lokal testen, bevor Sie sie im Web veröffentlichen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit Ihrer Web-App weiterarbeiten möchten, helfen Ihnen die Informationen im Abschnitt [Nächste Schritte](#next-steps) weiter. Wenn Sie nicht planen, diese Anwendung weiter zu nutzen, sollten Sie alle App-spezifischen Ressourcen löschen. Zum Löschen von Ressourcen können Sie die Ressourcengruppe löschen, die Ihr Azure Storage-Abonnement und die Maschinelles Sehen-Ressource enthält. Das Speicherkonto, die dafür hochgeladenen Blobs und die App Service-Ressource, die zum Herstellen der Verbindung mit der ASP.NET-Web-App benötigt werden, werden entfernt. 

Öffnen Sie zum Löschen der Ressourcengruppe im Portal die Registerkarte **Ressourcengruppe**, navigieren Sie zu der Ressourcengruppe, die Sie für dieses Projekt verwendet haben, und klicken Sie oben in der Ansicht auf **Ressourcengruppe löschen**. Sie werden gebeten, den Namen der Ressourcengruppe einzugeben, um zu bestätigen, dass Sie die Gruppe löschen möchten. Nach dem Löschen kann eine Ressourcengruppe nicht wiederhergestellt werden.

## <a name="next-steps"></a>Nächste Schritte

Es gibt noch viel mehr, was Sie tun können, um Azure zu verwenden und Ihre Intellipix-App noch weiter zu entwickeln. Beispielsweise können Sie Unterstützung für die Authentifizierung von Benutzern und das Löschen von Fotos hinzufügen. Anstatt zu erzwingen, dass der Benutzer nach einem Upload auf die Verarbeitung eines Fotos durch Cognitive Services wartet, können Sie [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=academiccontent-github-cxa) verwenden, um die maschinelles Sehen-API bei jedem Hinzufügen eines Bilds zum Blobspeicher asynchron aufzurufen. Sie können auch eine beliebige Anzahl anderer Bildanalysevorgänge für das Bild durchführen, wie in der Übersicht beschrieben.

> [!div class="nextstepaction"]
> [Übersicht über die Bildanalyse](../overview-image-analysis.md)
