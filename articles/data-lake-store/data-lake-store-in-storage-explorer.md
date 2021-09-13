---
title: Verwalten von Data Lake Storage Gen1-Ressourcen – Azure Storage-Explorer
description: Es wird beschrieben, wie Sie auf Ihre Azure Data Lake Storage Gen1-Daten und Ressourcen in Azure Storage-Explorer zugreifen und diese verwalten.
author: jejiang
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/04/2021
ms.author: jejiang
ms.openlocfilehash: 9ef06e1b13141e3b5c342842a63e8e520e3e03e9
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2021
ms.locfileid: "111589865"
---
# <a name="manage-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Verwalten von Data Lake Storage Gen1-Ressourcen mit Storage-Explorer

[Azure Data Lake Storage Gen1](./data-lake-store-overview.md) ist ein Dienst zum Speichern großer Mengen von unstrukturierten Daten, z.B. als Text- oder Binärdaten. Sie können per HTTP oder HTTPS von jedem Ort aus auf die Daten zugreifen. Mit Data Lake Storage Gen1 in Azure Storage-Explorer können Sie auf Data Lake Storage Gen1-Daten und -Ressourcen sowie andere Azure-Entitäten wie Blobs und Warteschlangen zugreifen und diese verwalten. Nun können Sie mit demselben Tool Ihre verschiedenen Azure-Entitäten an einem Ort verwalten.

Ein weiterer Vorteil besteht darin, dass Sie keine Abonnementberechtigung zum Verwalten von Data Lake Storage Gen1-Daten benötigen. In Storage-Explorer können Sie den Data Lake Storage Gen1-Pfad an den Knoten **Lokal & angefügt** anfügen, sofern Ihnen die Berechtigung erteilt wurde.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial).
* Ein Data Lake Storage Gen1-Konto. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](./data-lake-store-get-started-portal.md).

## <a name="install-storage-explorer"></a>Installieren des Storage-Explorers

Installieren Sie die neuesten Azure Storage-Explorer-Komponenten von der [Produktwebseite](https://azure.microsoft.com/features/storage-explorer/). Die Installation unterstützt Windows-, Linux- und Macintosh-Versionen.

## <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

1. Wählen Sie in Storage-Explorer links das Plug-In-Symbol aus.

   ![Screenshot: Position des Plug-In-Symbols auf der Benutzeroberfläche](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

   Das Dialogfeld **Verbindung mit Azure Storage herstellen** wird dadurch geöffnet.
1. Wählen Sie auf der Seite **Ressource auswählen** die Option **Abonnement** aus.
1. Wählen Sie auf der Seite **Azure-Umgebung auswählen** die Azure-Umgebung aus, bei der Sie sich anmelden möchten, und wählen Sie dann **Weiter** aus.
1. Geben Sie im Dialogfeld **Anmelden** Ihre Azure-Anmeldeinformationen ein, und wählen Sie dann **Weiter** aus.

1. Wählen Sie im Storage-Explorer im Bereich **KONTOVERWALTUNG** das Abonnement aus, das das zu verwaltende Data Lake Storage Gen1-Konto enthält, und wählen Sie dann **Explorer öffnen** aus.
1. Erweitern Sie im Bereich **EXPLORER** Ihr Abonnement. Der Bereich wird aktualisiert und zeigt die Konten im ausgewählten Abonnement an. Dies schließt alle Data Lake Storage Gen1-Konten ein, z. B.:

     ![Screenshot: Beispielkonto im Data Lake Storage Gen1-Knoten](./media/data-lake-store-in-storage-explorer/account-list.png)

## <a name="connect-to-data-lake-storage-gen1"></a>Herstellen der Verbindung mit Data Lake Storage Gen1

Sie können auf Ressourcen zugreifen, die in Ihrem Abonnement nicht vorhanden sind, wenn Sie den URI für die Ressourcen erhalten. Anschließend können Sie eine Verbindung mit Data Lake Storage Gen1 herstellen, indem Sie nach der Anmeldung den URI verwenden.

1. Öffnen Sie den Storage-Explorer.
1. Erweitern Sie **Lokal & angefügt**.
1. Klicken Sie mit der rechten Maustaste auf **Data Lake Storage Gen1 (Vorschau)** , und wählen Sie **Connect to Data Lake Storage Gen1** (Mit Data Lake Storage Gen1 verbinden) aus.
1. Geben Sie den URI ein, z. B.:

      ![Screenshot: Dialogfeld „Connect to Data Lake Storage“ (Verbindung mit Data Lake Storage herstellen) mit dem Textfeld für die URI-Eingabe](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

   Das Tool greift auf den URL-Speicherort zu, den Sie gerade eingegeben haben.

      ![Data Lake Storage Gen1-Konto unter dem Knoten „Data Lake Storage Gen1 (Vorschau)“ auf der Benutzeroberfläche](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-the-contents-of-a-data-lake-storage-gen1-account"></a>Anzeigen der Inhalte eines Data Lake Storage Gen1-Kontos

Zu den Ressourcen eines Data Lake Storage Gen1-Kontos gehören Ordner und Dateien. Die folgenden Schritte veranschaulichen, wie Sie den Inhalt eines Data Lake Storage Gen1-Kontos in Storage-Explorer anzeigen.

1. Öffnen Sie den Storage-Explorer.
1. Erweitern Sie das Abonnement mit dem Data Lake Storage Gen1-Konto, das Sie anzeigen möchten.
1. Erweitern Sie **Data Lake Storage Gen1 (Vorschau)** .
1. Wählen Sie das Data Lake Storage Gen1-Konto aus, das Sie anzeigen möchten.

   Im Hauptbereich werden die Inhalte des Data Lake Storage Gen1-Kontos angezeigt.

   ![Hauptbereich mit ausgewähltem Data Lake Storage Gen1-Konto und Liste der Ordner im Konto](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Verwalten von Ressourcen in Data Lake Storage Gen1

Sie können Data Lake Storage Gen1-Ressourcen verwalten, indem Sie die folgenden Vorgänge durchführen:

* Durchsuchen Sie die Data Lake Storage Gen1-Ressourcen mehrerer Data Lake Storage Gen1-Konten.  
* Verwenden Sie eine Verbindungszeichenfolge, um eine direkte Verbindung mit Data Lake Storage Gen1 herzustellen und die Daten zu verwalten.
* Zeigen Sie Data Lake Storage Gen1-Ressourcen, die von anderen Benutzern per ACL freigegeben wurden, unter **Lokal & angefügt** an.
* Führen Sie CRUD-Vorgänge für Dateien und Ordner durch: Unterstützung von rekursiven Ordnern und Mehrfachauswahl von Dateien.
* Sie können Ordner per Drag & Drop verschieben und hinzufügen, um schnell auf die letzten Speicherorte zuzugreifen. Dieser Vorgang ähnelt der Verwendung des Datei-Explorers auf dem Desktop.
* Kopieren und öffnen Sie einen Data Lake Storage Gen1-Hyperlink in Storage-Explorer mit nur einem Klick.
* Öffnen Sie das **Aktivitätsprotokoll** im unteren Bereich, um den Aktivitätsstatus anzuzeigen.
* Zeigen Sie die Ordnerstatistiken und Dateieigenschaften an.

## <a name="manage-resources-in-azure-storage-explorer"></a>Verwalten von Ressourcen in Azure Storage-Explorer

Wenn Sie ein Data Lake Storage Gen1-Konto erstellt haben, haben Sie folgende Möglichkeiten:

* Laden Sie Ordner und Dateien hoch und herunter, und öffnen Sie Ressourcen auf Ihrem lokalen Computer.
* Führen Sie das Anheften an den **Schnellzugriff** durch, erstellen Sie einen neuen Ordner, kopieren Sie eine URL, und wählen Sie alles aus.
* Sie können kopieren und einfügen, umbenennen, löschen, Ordnerstatistiken abrufen und aktualisieren.

Im Folgenden wird veranschaulicht, wie Sie Ressourcen in einem Data Lake Storage Gen1-Konto verwalten. Führen Sie die Schritte für den gewünschten Task durch.

### <a name="upload-files"></a>Hochladen von Dateien

1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Hochladen** und dann **Dateien hochladen** aus.
1. Wählen Sie im Dialogfeld **Select files to upload** (Dateien zum Hochladen auswählen) die Dateien aus, die Sie hochladen möchten.
1. Wählen Sie **Öffnen**, um den Upload zu starten.

> [!NOTE]
> Sie können Dateien von einem lokalen Computer auch direkt dorthin ziehen, um den Uploadvorgang zu starten.

### <a name="upload-a-folder"></a>Hochladen eines Ordners

1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Hochladen** und dann **Ordner hochladen** aus.
1. Wählen Sie im Dialogfeld **Ordner zum Hochladen auswählen** einen Ordner aus, den Sie hochladen möchten.
1. Wählen Sie die Option **Ordner auswählen** aus, um den Upload zu starten.

> [!NOTE]
> Sie können Ordner von einem lokalen Computer auch direkt dorthin ziehen, um den Uploadvorgang zu starten.

### <a name="download-folders-or-files-to-your-local-computer"></a>Herunterladen von Ordnern oder Dateien auf Ihren lokalen Computer

1. Wählen Sie die Ordner oder Dateien aus, die Sie herunterladen möchten.
1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Herunterladen**.
1. Geben Sie im Dialogfeld **Select a folder to save the downloaded files into** (Ordner zum Speichern heruntergeladener Dateien auswählen) den Speicherort und den Namen an.
1. Wählen Sie **Speichern** aus.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Öffnen eines Ordners oder einer Datei von Ihrem lokalen Computer

1. Wählen Sie den Ordner oder die Datei aus, der bzw. die geöffnet werden soll.
1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Öffnen**. Klicken Sie alternativ mit der rechten Maustaste auf den ausgewählten Ordner bzw. die Datei, und wählen Sie im Kontextmenü dann die Option **Öffnen** aus.

Die Datei wird heruntergeladen und mit der Anwendung geöffnet, die dem zugrunde liegenden Dateityp zugeordnet ist. Alternativ wird ein Ordner im Hauptbereich geöffnet.

### <a name="copy-folders-or-files-to-the-clipboard"></a>Kopieren von Ordnern oder Dateien in die Zwischenablage

Sie können Data Lake Storage Gen1-Ordner oder -Dateien kopieren und in ein anderes Data Lake Storage Gen1-Konto einfügen. Kopier- und Einfügevorgänge für mehrere Speichertypen werden nicht unterstützt. Es ist beispielsweise nicht möglich, Data Lake Storage Gen1-Ordner oder -Dateien zu kopieren und in Azure Blob Storage einzufügen (oder umgekehrt).

1. Wählen Sie die Ordner oder Dateien aus, die Sie kopieren möchten.
1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Kopieren**. Klicken Sie alternativ mit der rechten Maustaste auf die ausgewählten Ordner oder Dateien, und wählen Sie im Kontextmenü dann die Option **Kopieren** aus.
1. Navigieren Sie im linken Navigationsbereich zu einem anderen Data Lake Storage Gen1-Konto, und wählen Sie es aus, um es im Hauptbereich anzuzeigen.
1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Einfügen**, um eine Kopie zu erstellen. Wählen Sie alternativ im Kontextmenü des Ziels die Option **Einfügen** aus.

> [!NOTE]
> Der Kopier- bzw. Einfügevorgang funktioniert, indem die Ordner oder Dateien auf den lokalen Computer heruntergeladen und dann auf das Ziel hochgeladen werden. Mit dem Tool wird die Aktion nicht auf dem Back-End durchgeführt. Der Kopier- bzw. Einfügevorgang für große Dateien ist langsam.

### <a name="delete-folders-or-files"></a>Löschen von Ordnern oder Dateien

1. Wählen Sie die Ordner oder Dateien aus, die Sie löschen möchten.
1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Löschen**. Klicken Sie alternativ mit der rechten Maustaste auf die ausgewählten Ordner oder Dateien, und wählen Sie im Kontextmenü dann die Option **Löschen** aus.
1. Wählen Sie im Bestätigungsdialogfeld die Option **Ja**.

### <a name="pin-to-quick-access"></a>Anheften an Schnellzugriff

1. Wählen Sie den Ordner aus, den Sie anheften möchten, damit Sie problemlos auf die Ressourcen zugreifen können.
1. Wählen Sie in der Symbolleiste im Hauptbereich die Option **An Schnellzugriff anheften**.

   Im linken Navigationsbereich wird der ausgewählte Ordner dem Knoten **Schnellzugriff** hinzugefügt.

   ![Ordnerliste unter dem Knoten „Schnellzugriff“ auf der Benutzeroberfläche](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

### <a name="use-deep-links"></a>Verwenden von Deep-Links

Wenn Sie über eine URL verfügen, können Sie sie im Datei-Explorer oder Browser in den Adresspfad eingeben. Der Storage-Explorer wird dann automatisch geöffnet und navigiert zur URL.

![URL eines Ordners in einem Data Lake Storage Gen1-Konto, die in das Fenster „Datei-Explorer“ kopiert wurde](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [neuesten Versionsanmerkungen und Videos zum Storage-Explorer](https://www.storageexplorer.com) an.
* [Erste Schritte mit dem Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Erste Schritte mit Azure Data Lake Storage Gen1](./data-lake-store-overview.md)
