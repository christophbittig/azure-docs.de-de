---
title: Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen  | Micosoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Resource Manager Vorlagen verwenden, um ein Upgrade von Azure Blob Storage auf Data Lake Storage durchzuführen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: b1f612ee189d47529033fdd9e6c7e7df0f523df9
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129459051"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Ein Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen

Dieser Artikel hilft Ihnen, Funktionen wie Sicherheit auf Datei- und Verzeichnisebene und schnellere Vorgänge aufzuschließen. Diese Funktionen werden häufig von großen Datenanalyse-Workloads verwendet und werden als Azure Data Lake Storage Gen2 bezeichnet. 

Weitere Informationen zu diesen Funktionen und zur Bewertung der Auswirkungen dieses Upgrades auf die Workloads, Anwendungen, Kosten, Dienstintegrationen, Tools, Funktonen und Dokumentation finden Sie unter [Upgraden von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen](upgrade-to-data-lake-storage-gen2.md).

> [!IMPORTANT]
> Ein Upgrade ist eine unidirektionale Aktion. Nachdem Sie das Upgrade durchgeführt haben, gibt es keine Möglichkeit, Ihr Konto wiederherzustellen. Wir empfehlen Ihnen, das Upgrade in einer Nichtproduktionsumgebung zu testen.

## <a name="perform-the-upgrade"></a>Durchführen des Upgrades

1. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie nach Ihrem Speicherkonto, und zeigen Sie die Kontoübersicht an.

3. Wählen Sie **Data Lake Gen2-Migration** aus.

   Die Konfigurationsseite **Upgrade auf ein Speicherkonto mit Azure Data Lake Gen2-Funktionen** wird angezeigt.

   > [!div class="mx-imgBorder"]
   > ![Seite „Konfiguration“](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. Erweitern Sie den Abschnitt **Schritt 1: Überprüfen von Kontoänderungen vor dem Upgrade** und klicken Sie auf **Änderungen überprüfen und zustimmen**.

5. Aktivieren Sie auf der Seite **Kontoänderungen überprüfen** das Kontrollkästchen, und klicken Sie dann auf **Änderungen zustimmen**.

6. Erweitern Sie den Abschnitt **Schritt 2: Überprüfen des Kontos vor dem Upgrade** und klicken Sie dann auf **Überprüfung starten**.

   Wenn bei der Überprüfung ein Fehler auftritt, wählen Sie den Link **Fehler anzeigen** aus

   > [!div class="mx-imgBorder"]
   > ![Link Fehler anzeigen](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   Wählen Sie dann im Kontextmenü der Datei **error.json** die Option **Herunterladen** aus.

   > [!div class="mx-imgBorder"]
   > ![Seite: json-Fehler](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   Öffnen Sie die heruntergeladene Datei, um zu ermitteln, warum der Überprüfungsschritt für das Konto nicht erfolgreich war. 

   Der folgende JSON-Code gibt an, dass eine inkompatibles Funktion für das Konto aktiviert ist. In diesem Fall würden Sie die Funktion deaktivieren und dann den Überprüfungsprozess erneut starten.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. Nachdem Ihr Konto erfolgreich überprüft wurde, erweitern Sie den Abschnitt **Schritt 3: Upgradekonto** und klicken Sie dann auf **Upgrade starten**.

   > [!IMPORTANT]
   > Schreibvorgänge sind während des Upgrades Ihres Kontos deaktiviert. Lesevorgänge sind nicht deaktiviert, aber wir empfehlen dringend, dass Sie Lesevorgänge anhalten, da diese den Upgrade-Prozess destabilisieren könnten.

   Wenn die Migration erfolgreich abgeschlossen wurde, wird eine Meldung ähnlich der folgenden angezeigt. 

   > [!div class="mx-imgBorder"]
   > ![Seite: Migration abgeschlossen](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

## <a name="migrate-data-workloads-and-applications"></a>Migrieren von Daten, Workloads und Anwendungen 

1. Konfigurieren Sie die [Dienste in Ihren Workloads](data-lake-storage-integrate-with-azure-services.md) so, dass sie entweder auf den **Blob-Dienstendpunkt** oder den **Data Lake Storage-Endpunkt** verweisen.

   > [!div class="mx-imgBorder"]
   > ![Kontoendpunkte](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Stellen Sie sicher, das Hadoop-Workloads, die den Windows Azure Storage Blob-Treiber oder den [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)-Treiber verwenden, so geändert werden, dass sie den [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)-Treiber verwenden. Anders als der WASB-Treiber, der Anforderungen an den Endpunkt des **Blob-Dienstes** stellt, stellt der ABFS-Treiber Anforderungen an den **Data Lake Storage**-Endpunkt Ihres Kontos.

2. Testen Sie die benutzerdefinierten Anwendungen, um sicherzustellen, dass sie wie erwartet mit Ihrem aktualisierten Konto funktionieren. 

   [Der Zugriff mit mehreren Protokollen auf Data Lake Storage](data-lake-storage-multi-protocol-access.md) ermöglicht es den meisten Anwendungen, Blob-APIs weiterhin ohne Änderungen zu verwenden. Wenn Probleme auftreten oder Sie APIs verwenden möchten, um mit Verzeichnisvorgängen und ACLs zu arbeiten, sollten Sie einen Teil Ihres Codes verschieben, um Data Lake Storage Gen2-APIs zu verwenden. Weitere Informationen finden Sie in den Anleitungen für [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [Node.js](data-lake-storage-acl-javascript.md) und [REST](/rest/api/storageservices/data-lake-storage-gen2). 

3. Testen Sie alle benutzerdefinierten Skripts, um sicherzustellen, dass sie wie erwartet mit Ihrem aktualisierten Konto funktionieren. 

   Wie bei Blob-APIs funktionieren viele Ihrer Skripts wahrscheinlich, ohne dass Sie sie ändern müssen. Aktualisieren Sie Skripts aber bei Bedarf für die Verwendung der [PowerShell-Cmdlets](data-lake-storage-directory-file-acl-powershell.md) und [Azure CLI-Befehle](data-lake-storage-directory-file-acl-cli.md) für Data Lake Storage Gen2.
 

## <a name="see-also"></a>Siehe auch

[Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)