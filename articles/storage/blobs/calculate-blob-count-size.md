---
title: Berechnen der Anzahl und Größe von Blobs mit dem Azure Storage-Bestand
description: Erfahren Sie, wie Sie die Anzahl und Gesamtgröße von Blobs pro Container berechnen.
services: storage
author: normesta
ms.author: normesta
ms.date: 08/16/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: subject-rbac-steps
ms.openlocfilehash: f4a03a73a85fa265517b421c2179a077d73a75be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600340"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Berechnen der Anzahl und Gesamtgröße von Blobs pro Container mit dem Azure Storage-Bestand

In diesem Artikel werden der Azure Blob Storage-Bestand und Azure Synapse verwendet, um die Blobanzahl und die Gesamtgröße von Blobs pro Container zu berechnen. Diese Werte sind bei der Optimierung der Blobverwendung pro Container nützlich.

Die Blobmetadaten werden bei dieser Methode nicht einbezogen. Bei der Funktion für den Azure Blob Storage-Bestand wird die REST-API [List Blobs](/rest/api/storageservices/list-blobs) mit Standardparametern verwendet. Daher werden in diesem Beispiel keine Momentaufnahmen, „$“-Container usw. unterstützt.

## <a name="enable-inventory-reports"></a>Aktivieren von Bestandsberichten

Im ersten Schritt dieser Methode werden in Ihrem Speicherkonto [Bestandsberichte aktiviert](blob-inventory.md#enabling-inventory-reports). Möglicherweise müssen Sie nach dem Aktivieren von Bestandsberichten bis zu 24 Stunden warten, bis der erste Bericht generiert werden kann.

Wenn Sie einen Bestandsbericht analysieren möchten, weisen Sie sich für den Container, in dem sich die CSV-Datei des Berichts befindet, die Rolle **Storage-Blobdatenleser** zu. Verwenden Sie die E-Mail-Adresse des Kontos, mit dem Sie den Bericht ausführen. Weitere Informationen zum Zuweisen einer Azure-Rolle zu einem Benutzer mit rollenbasierter Zugriffssteuerung in Azure (Azure RBAC) finden Sie in den Anweisungen unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="create-an-azure-synapse-workspace"></a>Erstellen eines Azure Synapse-Arbeitsbereichs

[Erstellen Sie einen Azure Synapse-Arbeitsbereich](../../synapse-analytics/get-started-create-workspace.md), in dem Sie eine SQL-Abfrage ausführen, um die Bestandsergebnisse zu protokollieren.

## <a name="create-the-sql-query"></a>Erstellen der SQL-Abfrage

Führen Sie nach dem Erstellen des Azure Synapse-Arbeitsbereichs die folgenden Schritte aus.

1. Navigieren Sie zu [https://web.azuresynapse.net](https://web.azuresynapse.net).
1. Wählen Sie die Registerkarte **Entwickeln** am linken Rand aus.
1. Wählen Sie das große Pluszeichen (+) aus, um ein Element hinzuzufügen.
1. Wählen Sie **SQL-Skript** aus.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Auswählen von „SQL-Skript“ zum Erstellen einer neuen Abfrage":::

## <a name="run-the-sql-query"></a>Ausführen der SQL-Abfrage

1. Fügen Sie die folgende SQL-Abfrage in Ihrem Azure Synapse-Arbeitsbereich hinzu, um [die CSV-Bestandsdatei zu lesen](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    Verwenden Sie für den `bulk`-Parameter die URL der CSV-Datei für den Bestandsbericht, die analysiert werden soll.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container,
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Geben Sie im Eigenschaftenbereich rechts einen Namen für die SQL-Abfrage ein.

1. Veröffentlichen Sie die SQL-Abfrage durch Drücken von STRG+S oder durch Auswählen der Schaltfläche **Alle veröffentlichen**.

1. Wählen Sie die Schaltfläche **Ausführen** aus, um die SQL-Abfrage auszuführen. Die Anzahl und Gesamtgröße der Blobs pro Container werden im **Ergebnisbereich** angezeigt.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Ausgabe nach Ausführung des Skripts zum Berechnen der Anzahl und Gesamtgröße der Blobs":::

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Blobdaten mit dem Azure Storage-Blobbestand](blob-inventory.md)
- [Berechnen der Gesamtabrechnungsgröße eines Blobcontainers](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
