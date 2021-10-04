---
title: Laden von Daten in Azure Data Lake Storage Gen2
description: Kopieren von Daten in Azure Data Lake Storage Gen2 mithilfe von Azure Data Factory
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/22/2021
ms.openlocfilehash: 4ed42f5ea6aa5acda8234aab31b26c99057ed6b9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819986"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 baut auf [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) auf und bietet eine Reihe von Funktionen für die Big Data-Analyse. Er ermöglicht Ihnen die Arbeit mit Ihren Daten über das Dateisystem und den Objektspeicher.

Azure Data Factory (ADF) ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie den Lake mit Daten aus zahlreichen lokalen und cloudbasierten Datenspeichern füllen und Zeit beim Erstellen von Analyselösungen sparen. Eine ausführliche Liste der unterstützten Connectors finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory bietet eine Lösung zur horizontalen Skalierung und Verschiebung verwalteter Daten. Aufgrund der horizontal skalierbaren Architektur von ADF können Daten mit hohem Durchsatz erfasst werden. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum Laden von Daten aus _Amazon Web Services S3_ in _Azure Data Lake Storage Gen2_ verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

>[!TIP]
>Informationen zum Kopieren von Daten aus Azure Data Lake Storage Gen1 nach Gen2 finden Sie in [dieser exemplarischen Vorgehensweise](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Azure Storage-Konto mit aktiviertem Data Lake Storage Gen2: [Erstellen Sie ein Speicherkonto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM), wenn Sie noch keines besitzen.
* AWS-Konto mit einem S3-Bereich, der Daten enthält: Dieser Artikel zeigt, wie Daten aus Amazon S3 kopiert werden. Sie können andere Datenspeicher verwenden, indem Sie ähnliche Schritte ausführen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Auswahl von Data Factory im Bereich &quot;Neu&quot;":::

2. Geben Sie auf der Seite **Neue Data Factory** Werte für folgende Felder an:
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen *YourDataFactoryName* ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_**ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. 

3. Klicken Sie auf **Erstellen**.

4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Startseite für Azure Data Factory mit der Kachel „Open Azure Data Factory Studio“":::

   Klicken Sie auf der Kachel **Open Azure Data Factory Studio** auf **Öffnen**, um die Datenintegration-Anwendung in einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Laden von Daten in Azure Data Lake Storage Gen2

1. Wählen Sie auf der Homepage von Azure Data Factory die Kachel **Erfassung** aus, um das Tool „Daten kopieren“ zu starten.

2. Navigieren Sie zu der Seite **Eigenschaften** und wählen Sie die Option **Integrierte Kopieraufgabe** unter dem **Aufgabentyp** aus. Wählen Sie nun unter **Aufgabenintervall oder Aufgabenzeitplan** die Option **Jetzt einmal ausführen** und klicken Sie dann auf **Weiter**.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png" alt-text="Seite „Eigenschaften“":::
3. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:
    1. Wählen Sie **+ Neue Verbindung** aus. Wählen Sie im Connectorkatalog **Amazon S3** aus, und klicken Sie auf **Weiter**.
    
        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png" alt-text="Seite „Quelldatenspeicher“ für S3":::
    
    1. Führen Sie auf der Seite **Neue Verbindung (Amazon S3)** die folgenden Schritte aus:

        1. Geben Sie den Wert für die **Zugriffsschlüssel-ID** an.
        1. Geben Sie den Wert für den **geheimen Zugriffsschlüssel** an.
        1. Wählen Sie **Verbindung testen** aus, um die Einstellungen zu überprüfen. Wählen Sie dann **Erstellen** aus.
    
          :::image type="content" source="./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png" alt-text="Angeben des Amazon S3-Kontos":::

    1. Stellen Sie auf der Seite **Quelldatenspeicher** sicher, dass die neu erstellte Amazon 3-Verbindung in dem **Verbindungsblock** ausgewählt ist. 
    1. Navigieren Sie auf der Seite **Datei oder Ordner** zu dem Ordner und der Datei, die Sie kopieren möchten. Wählen Sie den Ordner / die Datei und dann **OK** aus.
    1. Geben Sie das Kopierverhalten an, indem Sie die Optionen **Rekursiv** und **Binärkopie** aktivieren. Wählen Sie **Weiter** aus.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/source-data-store.png" alt-text="Ein Screenshot, der die Seite „Quelldatenspeicher“ zeigt":::.
    
4. Führen Sie auf der Seite **Zieldatenspeicher** die folgenden Schritte aus.
    1. Wählen Sie **+ Neue Verbindung** und dann **Azure Data Lake Storage Gen2** aus. Klicken Sie anschließend auf **Weiter**.

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png" alt-text="Seite „Zieldatenspeicher“":::
    
    1. Wählen Sie auf der Seite **Neue Verbindung (Azure Data Lake Storage Gen2)** Ihr Data Lake Storage Gen2-fähiges Konto aus der Dropdown-Liste „Name des Speicherkontos“ aus und wählen Sie dann **Erstellen**, um die Verbindung zu erstellen. 

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png" alt-text="Angeben eines Azure Data Lake Storage Gen2-Kontos":::

    1. Wählen Sie auf der Seite **Zieldatenspeicher** die neu erstellte Verbindung in dem Block **Verbindung** aus. Geben Sie dann unter **Ordnerpfad** die Zeichenfolge **copyfroms3** als Name für den Ausgabeordner ein, und klicken Sie dann auf **Weiter**. Die ADF erstellt das entsprechende ADLS Gen2-Dateisystem und die Unterordner während des Kopierens, wenn diese noch nicht existieren.

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/destiantion-data-store.png" alt-text="Ein Screenshot, der die Seite „Zieldatenspeicher“ zeigt":::.   
    
5. Geben Sie auf der Seite **Einstellungen** im Feld **Aufgabenname** den Namen **CopyFromAmazonS3ToADLS** ein und klicken Sie dann auf **Weiter**.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-settings.png" alt-text="Einstellungsseite":::

6. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-summary.png" alt-text="Seite „Zusammenfassung“":::

7. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen. 
 
8. Wenn die Pipelineausführung erfolgreich abgeschlossen ist, sehen Sie eine Pipelineausführung, die durch einen manuellen Trigger ausgelöst wird. Über die Links in der Spalte **Name der Pipeline** können Sie die Aktivitätsdetails anzeigen und die Pipeline erneut ausführen.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png" alt-text="Überwachen der Pipelineausführungen":::

9. Um die der Pipelineausführung zugeordneten Aktivitätsausführungen anzuzeigen, wählen Sie in der Spalte **Name der Pipeline** den Link **CopyFromAmazonS3ToADLS** aus. Wenn Sie Details zum Kopiervorgang anzeigen möchten, wählen Sie unter der Spalte **Activity name** (Aktivitätsname) den Link **Details** (das Brillensymbol) aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendete Konfiguration überwachen.
 
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png" alt-text="Überwachung der Aktivitätsausführungen":::
    
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png" alt-text="Überwachen der Details zur Aktivitätsausführung":::

10. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**. Wählen Sie oben **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den „Pipelineausführungen“ zu wechseln.

11. Stellen Sie sicher, dass die Daten in Ihr Data Lake Storage Gen2-Konto kopiert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Kopieraktivität – Übersicht](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-Connector](connector-azure-data-lake-storage.md)
