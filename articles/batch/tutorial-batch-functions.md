---
title: Tutorial – Auslösen eines Batch-Auftrags mithilfe von Azure Functions
description: 'Tutorial: Anwenden von OCR auf gescannte Dokumente beim Hinzufügen zu einem Storage-Blob'
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: peshultz
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 02379ee6872564b73441f6756479965912f3925f
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449097"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Tutorial: Auslösen eines Batch-Auftrags mithilfe von Azure Functions

In diesem Tutorial erfahren Sie, wie Sie mithilfe von [Azure Functions](../azure-functions/functions-overview.md) einen Batch-Auftrag auslösen. Wir sehen uns ein Beispiel an, bei dem die optische Zeichenerkennung (OCR) über Azure Batch auf Dokumente angewandt wird, die einem Azure Storage-Blobcontainer hinzugefügt werden. Zur Optimierung der OCR-Verarbeitung wird eine Azure-Funktion konfiguriert, die bei jedem Hinzufügen einer Datei zum Blobcontainer einen Batch-OCR-Auftrag ausführt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen von Pools und Aufträgen mithilfe von Batch Explorer
> * Erstellen von Blobcontainern und einer Shared Access Signature (SAS) mithilfe von Storage-Explorer
> * Erstellen einer per Blob ausgelösten Azure-Funktion
> * Hochladen von Eingabedateien in Storage
> * Überwachen der Aufgabenausführung
> * Abrufen von Ausgabedateien

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ein Azure Batch-Konto und ein verknüpftes Azure Storage-Konto. Weitere Informationen zum Erstellen und Verknüpfen von Konten finden Sie unter [Erstellen eines Batch-Kontos](quick-create-portal.md#create-a-batch-account).
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage-Explorer:](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Erstellen eines Batch-Pools und eines Batch-Auftrags mit Batch Explorer

In diesem Abschnitt erstellen Sie mit Batch Explorer den Batch-Pool und den Batch-Auftrag, über den OCR-Tasks ausgeführt werden.

### <a name="create-a-pool"></a>Erstellen eines Pools

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen bei Batch Explorer an.
1. Erstellen Sie einen Pool, indem Sie auf der linken Seitenleiste die Option **Pools** und anschließend die Schaltfläche **Hinzufügen** über dem Suchformular auswählen. 
    1. Wählen Sie eine ID und einen Anzeigenamen aus. In diesem Beispiel wird `ocr-pool` verwendet.
    1. Legen Sie den Skalierungstyp auf **Feste Größe** und die Anzahl der dedizierten Knoten auf 3 fest.
    1. Wählen Sie als Betriebssystem **Ubuntuserver** > **18.04-lts** aus.
    1. Wählen Sie `Standard_f2s_v2` als Größe des virtuellen Computers aus.
    1. Aktivieren Sie den Starttask, und fügen Sie den Befehl `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"` hinzu. Legen Sie die Benutzeridentität als **Task default user (Admin)** (Standardbenutzer für Tasks [Administrator]) fest, damit Starttasks Befehle mit `sudo` enthalten können.
    1. Klicken Sie auf **OK**.
  
### <a name="create-a-job"></a>Erstellen eines Auftrags

1. Erstellen Sie einen Auftrag im Pool, indem Sie **Aufträge** auf der linken Seitenleiste und anschließend die Schaltfläche **Hinzufügen** über dem Suchformular auswählen.
   1. Wählen Sie eine ID und einen Anzeigenamen aus. In diesem Beispiel wird `ocr-job` verwendet.
   1. Legen Sie den Pool auf `ocr-pool` oder den ausgewählten Namen des Pools fest.
   1. Klicken Sie auf **OK**.

## <a name="create-blob-containers"></a>Erstellen von Blobcontainern

Hier erstellen Sie Blobcontainer, in denen die Eingabe- und Ausgabedateien für den OCR-Batch-Auftrag gespeichert werden. In diesem Beispiel hat der Eingabecontainer den Namen `input` und ist der Speicherort, in den alle Dokumente ohne OCR zunächst zur Verarbeitung hochgeladen werden. In den Ausgabecontainer mit dem Namen `output` schreibt der Batch-Auftrag die mit OCR verarbeiteten Dokumente.

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen bei [Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) an.
1. Erstellen Sie unter Verwendung des mit Ihrem Batch-Konto verknüpften Speicherkontos entsprechend den unter [Erstellen eines Blobcontainers](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container) beschriebenen Schritten zwei Blobcontainer (einen für Eingabedateien und einen für Ausgabedateien).
1. Erstellen Sie in Storage-Explorer eine Shared Access Signature für Ihren Ausgabecontainer, indem Sie mit der rechten Maustaste auf den Ausgabecontainer klicken und **Shared Access Signature abrufen** auswählen. Wählen Sie unter **Berechtigungen**  die Option **Schreiben** aus. Es sind keine weiteren Berechtigungen erforderlich.  

## <a name="create-an-azure-function"></a>Erstellen einer Azure Function

In diesem Abschnitt erstellen Sie die Azure-Funktion, die den OCR-Batch-Auftrag auslöst, wenn eine Datei in den Eingabecontainer hochgeladen wird.

1. Führen Sie zum Erstellen der Funktion die Schritte unter [Erstellen einer Funktion, die durch Azure Blob Storage ausgelöst wird](../azure-functions/functions-create-storage-blob-triggered-function.md) aus.
    1. Wählen Sie „.NET“ für **Runtimestapel** aus. Wir schreiben die Funktion in C#, um das Batch .NET SDK zu nutzen.
    1. Wenn Sie unter **Hosting** zur Eingabe eines Speicherkontos aufgefordert werden, geben Sie das Speicherkonto an, das Sie mit Ihrem Batch-Konto verknüpft haben.
    1. Achten Sie beim Erstellen des Azure Blob Storage-Kontotriggers darauf, den Pfad auf `input/{name}` (entsprechend dem Namen Ihres Eingabecontainers) festzulegen.
1. Wählen Sie nach dem Erstellen der per Blob ausgelösten Funktion **Programmieren und testen** aus. Verwenden Sie in der Funktion [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) und [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) von GitHub. `function.proj` ist standardmäßig nicht vorhanden. Wählen Sie daher die Schaltfläche **Hochladen** aus, um sie in Ihren Entwicklungsarbeitsbereich hochzuladen.
    * `run.csx` wird ausgeführt, wenn ein neues Blob im Eingabeblobcontainer hinzugefügt wird.
    * `function.proj` enthält die externen Bibliotheken im Funktionscode, z. B. das Batch .NET SDK.
1. Ändern Sie die Platzhalterwerte der Variablen in der `Run()`-Funktion in der Datei `run.csx` entsprechend Ihren Batch- und Storage-Anmeldeinformationen. Die Anmeldeinformationen für Ihr Batch- und Storage-Konto finden Sie im Azure-Portal im Abschnitt **Schlüssel** des Batch-Kontos.
    * Rufen Sie die Anmeldeinformationen für Ihr Batch- und Storage-Konto im Azure-Portal im Abschnitt **Schlüssel** des Batch-Kontos ab. 


## <a name="trigger-the-function-and-retrieve-results"></a>Auslösen der Funktion und Abrufen der Ergebnisse

Laden Sie beliebige gescannte Dateien aus dem Verzeichnis [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) in GitHub in Ihren Eingabecontainer hoch. Überprüfen Sie in Batch Explorer, ob für jede Datei ein Task in `ocr-pool` hinzugefügt wird. Nach wenigen Sekunden wird die Datei mit angewandter OCR im Ausgabecontainer hinzugefügt. Die Datei ist dann sichtbar und kann über Storage-Explorer abgerufen werden.

Zusätzlich können Sie die Protokolldateien unten im Azure Functions-Web-Editorfenster überprüfen. Hier werden Nachrichten wie die folgende für jede Datei angezeigt, die in den Eingabecontainer hochgeladen wird:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Um die Ausgabedateien von Storage-Explorer auf den lokalen Computer herunterzuladen, wählen Sie zunächst die gewünschten Dateien und dann im oberen Menüband die Option **Herunterladen** aus.

> [!TIP]
> Die heruntergeladenen Dateien können durchsucht werden, wenn sie in einem PDF-Reader geöffnet werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ihnen werden während der Ausführung der Knoten auch dann Gebühren für den Pool berechnet, wenn keine Aufträge geplant sind. Wenn Sie den Pool nicht mehr benötigen, löschen Sie ihn, indem Sie die folgenden Schritte ausführen:

1. Klicken Sie in der Kontoansicht auf **Pools** und auf den Namen des Pools.
1. Klicken Sie auf **Löschen**.

Beim Löschen des Pools werden alle Aufgabenausgaben auf den Knoten gelöscht. Die Ausgabedateien verbleiben aber im Speicherkonto. Wenn Sie nicht mehr benötigt werden, können Sie auch das Batch-Konto und das Speicherkonto löschen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele zur Verwendung der .NET-API zum Planen und Verarbeiten von Batch-Workloads finden Sie in den Beispielen auf GitHub.

> [!div class="nextstepaction"]
> [C#-Beispiele für Batch](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
