---
title: Laden von Daten in Azure Data Lake Storage Gen1
description: Kopieren von Daten in Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 5a82f60bfbf97ebc0de6f1ff9f8214c995775bf2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819940"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Laden von Daten in Azure Data Lake Storage Gen1 mit Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (bisher als Azure Data Lake Store bezeichnet) ist ein unternehmensweites Repository mit Hyperskalierung für Big Data-Analyseworkloads. Mit Data Lake Storage Gen1 können Sie Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit erfassen. Die Daten werden an einer einzelnen Stelle zur Durchführung operativer und explorativer Analysen erfasst.

Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie den Lake mit Daten aus dem vorhandenen System füllen und Zeit beim Erstellen von Analyselösungen sparen.

Azure Data Factory bietet die folgenden Vorteile beim Laden von Daten in Data Lake Storage Gen1:

* **Mühelose Einrichtung**: Intuitiver Assistent mit 5 Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher. Eine ausführliche Liste finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicher und kompatibel**: Daten werden über HTTPS oder ExpressRoute übertragen. Globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Hohe Leistung**: Bis zu 1GB/s Datenladegeschwindigkeit in Data Lake Storage Gen1. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum _Laden von Daten aus Amazon S3 in Data Lake Storage Gen1_ verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

> [!NOTE]
> Weitere Informationen finden Sie unter [Kopieren von Daten nach und aus Data Lake Storage Gen1 mithilfe von Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Data Lake Storage Gen1-Konto: Wenn Sie nicht über ein Data Lake Storage Gen1-Konto verfügen, lesen Sie die Anweisungen unter [Erstellen eines Data Lake Storage Gen1-Kontos](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Dieser Artikel zeigt, wie Daten aus Amazon S3 kopiert werden. Sie können andere Datenspeicher verwenden, indem Sie ähnliche Schritte ausführen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Klicken Sie im Menü auf der linken Seite auf **Ressource erstellen** > **Analytics** > **Data Factory**:
   
   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="Auswahl von Data Factory im Bereich &quot;Neu&quot;":::

2. Geben Sie auf der Seite **Neue Data Factory** die in der folgenden Abbildung gezeigten Werte für die Felder an: 
      
   :::image type="content" source="./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png" alt-text="Seite „Neue Data Factory“":::
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen \"LoadADLSG1Demo\" ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_**ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. Diese Datenspeicher umfassen Azure Data Lake Storage Gen1, Azure Storage, Azure SQL-Datenbank usw.

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Startseite für Azure Data Factory mit der Kachel „Open Azure Data Factory Studio“":::

   Klicken Sie auf der Kachel **Open Azure Data Factory Studio** auf **Öffnen**, um die Datenintegrations-Anwendung in einer separaten Registerkarte zu starten.

## <a name="load-data-into-data-lake-storage-gen1"></a>Laden von Daten in Data Lake Storage Gen1

1. Wählen Sie auf der Homepage die Kachel **Erfassen** aus, um das Tool zum Kopieren von Daten zu starten: 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Screenshot, der die ADF-Startseite zeigt.":::
2. Geben Sie auf der Seite **Eigenschaften** im Feld **Aufgabenname** den Namen **CopyFromAmazonS3ToADLS** ein, und klicken Sie dann auf **Weiter**:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png" alt-text="Seite „Eigenschaften“":::
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf **+ Neue Verbindung erstellen**:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/source-data-store-page.png" alt-text="Seite „Quelldatenspeicher“":::
    
    Wählen Sie **Amazon S3** und dann **Weiter** aus.
    
    :::image type="content" source="./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png" alt-text="Seite „Quelldatenspeicher“ für S3":::
    
4. Führen Sie auf der Seite **Amazon S3-Verbindung angeben** die folgenden Schritte aus: 
   1. Geben Sie den Wert für die **Zugriffsschlüssel-ID** an.
   2. Geben Sie den Wert für den **geheimen Zugriffsschlüssel** an.
   3. Wählen Sie **Fertig stellen** aus.
   
      :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png" alt-text="Screenshot des Bereichs „Neuer verknüpfter Dienst“ zum Eingeben von Werten":::
   
   4. Es wird eine neue Verbindung angezeigt. Wählen Sie **Weiter** aus.
   
   :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png" alt-text="Screenshot der neuen Verbindung":::
   
5. Navigieren Sie auf der Seite **Eingabedatei oder -ordner auswählen** zu dem Ordner und der Datei, die Sie kopieren möchten. Wählen Sie den Ordner/die Datei aus, klicken Sie auf **Auswählen**, und klicken Sie dann auf **Weiter**:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/choose-input-folder.png" alt-text="Auswählen der Eingabedatei bzw. des Eingabeordners":::

6. Wählen Sie das Kopierverhalten aus, indem Sie die Optionen **Dateien rekursiv kopieren** und **Binärkopie** (unverändertes Kopieren von Dateien) aktivieren. Klicken Sie auf **Weiter**:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-binary-copy.png" alt-text="Screenshot der Seite „Eingabedatei oder -ordner auswählen“ mit den Optionen „Dateien rekursiv kopieren“ und „Binärkopie“":::
    
7. Klicken Sie auf der Seite **Zieldatenspeicher** auf **+ Neue Verbindung erstellen**, und wählen Sie anschließend **Azure Data Lake Storage Gen1** und dann **Weiter** aus:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png" alt-text="Seite „Zieldatenspeicher“":::

8. Führen Sie auf der Seite **Neuer verknüpfter Dienst (Azure Data Lake Storage Gen1)** die folgenden Schritte aus: 

   1. Wählen Sie Ihr Data Lake Storage Gen1-Konto für den **Data Lake Store-Kontonamen** aus.
   2. Geben Sie den **Mandanten** an, und wählen Sie „Fertig stellen“ aus.
   3. Wählen Sie **Weiter** aus.
   
   > [!IMPORTANT]
   > In dieser exemplarischen Vorgehensweise verwenden Sie eine verwaltete Identität für Azure-Ressourcen, um Ihr Data Lake Storage Gen1-Konto zu authentifizieren. Achten Sie darauf, dass Sie der verwalteten Dienstidentität (Managed Service Identity, MSI) die entsprechenden Berechtigungen in Data Lake Storage Gen1 erteilen. Befolgen Sie dazu [diese Anweisungen](connector-azure-data-lake-store.md#managed-identity).
   
   :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-adls.png" alt-text="Angeben des Data Lake Storage Gen1-Kontos":::
9. Geben Sie auf der Seite **Ausgabedatei oder -ordner auswählen** die Zeichenfolge **copyfroms3** als Name für den Ausgabeordner ein, und klicken Sie dann auf **Weiter**: 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-adls-path.png" alt-text="Screenshot des eingegebenen Ordnerpfads":::

10. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-settings.png" alt-text="Einstellungsseite":::
11. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-summary.png" alt-text="Seite „Zusammenfassung“":::
12. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/deployment-page.png" alt-text="Bereitstellungsseite":::
13. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. In der Spalte **Aktionen** werden Links zum Anzeigen von Aktivitätsausführungsdetails und zum erneuten Ausführen der Pipeline angezeigt:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png" alt-text="Überwachen der Pipelineausführungen":::
14. Klicken Sie in der Spalte **Aktionen** auf den Link **Aktivitätsausführungen anzeigen**, um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Klicken Sie oben auf den Link **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png" alt-text="Überwachung der Aktivitätsausführungen":::

15. Zum Überwachen der Ausführungsdetails jeder Kopieraktivität wählen Sie in der Aktivitätsüberwachungsansicht unter **Aktionen** den Link **Details** aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen:

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png" alt-text="Überwachen der Details zur Aktivitätsausführung":::

16. Stellen Sie sicher, dass die Daten in Ihr Data Lake Storage Gen1-Konto kopiert werden: 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/adls-copy-result.png" alt-text="Überprüfen der Data Lake Storage Gen1-Ausgabe":::

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den folgenden Artikel, um mehr über die Unterstützung von Data Lake Storage Gen1 zu erfahren: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-Connector](connector-azure-data-lake-store.md)
