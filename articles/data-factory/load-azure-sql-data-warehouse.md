---
title: Laden von Daten in Azure Synapse Analytics
titleSuffix: Azure Data Factory & Azure Synapse
description: Verwenden Sie Azure Data Factory oder eine Azure Synapse-Pipeline, um Daten in Azure Synapse Analytics zu kopieren.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e91d2dbae4f73e99a5e341330c0841e8173cedc8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819923"
---
# <a name="load-data-into-azure-synapse-analytics-using-azure-data-factory-or-a-synapse-pipeline"></a>Laden von Daten in Azure Synapse Analytics mithilfe einer Azure Data Factory- oder Synapse-Pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ist eine cloudbasierte Datenbank mit horizontaler Skalierung, mit der sehr große Datenvolumen verarbeitet werden können, und zwar sowohl relational als auch nicht relational. Azure Synapse Analytics basiert auf der MPP-Architektur (Massively Parallel Processing), die für Data-Warehouse-Workloads auf Unternehmensniveau optimiert ist. Es bietet Cloudelastizität mit der Flexibilität, Speicher zu skalieren und unabhängig zu berechnen.

Das Ausführen der ersten Schritte mit Azure Synapse Analytics ist jetzt einfacher als je zuvor. Azure Data Factory und die entsprechenden Pipelinefunktionen in Azure Synapse bieten einen vollständig verwalteten cloudbasierten Datenintegrationsdienst. Mithilfe dieses Diensts können Sie eine Azure Synapse Analytics-Instanz mit Daten aus dem vorhandenen System auffüllen und so Zeit beim Erstellen von Analyselösungen sparen.

Azure Data Factory- und Synapse-Pipelines bieten die folgenden Vorteile beim Laden von Daten in Azure Synapse Analytics:

* **Mühelose Einrichtung**: Intuitiver Assistent mit 5 Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher. Eine ausführliche Liste finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicher und kompatibel**: Daten werden über HTTPS oder ExpressRoute übertragen. Globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Beispiellose Leistung mithilfe von PolyBase**: PolyBase ist die effizienteste Methode zum Verschieben von Daten in Azure Synapse Analytics. Mit der Funktion „Stagingblob“ werden schnelle Ladezeiten für alle Arten von Datenspeicher erreicht. Dies gilt auch für Azure Blob Storage und Data Lake Store. (Azure Blob Storage und Azure Data Lake Store werden standardmäßig von Polybase unterstützt.) Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool „Kopieren von Daten“ zum _Laden von Daten aus der Azure SQL-Datenbank in Azure Synapse Analytics_ verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

> [!NOTE]
> Weitere Informationen finden Sie unter [Kopieren von Daten nach und aus Azure Synapse Analytics](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Azure Synapse Analytics: Dieses Data Warehouse enthält die Daten, die aus der SQL-Datenbank kopiert werden. Wenn Sie keine Azure Synapse Analytics-Instanz besitzen, finden Sie unter [Erstellen einer Azure Synapse Analytics-Instanz](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md) die entsprechenden Anweisungen.
* Azure SQL-Datenbank: In diesem Tutorial werden Daten aus einem Beispieldataset von Adventure Works LT nach Azure SQL-Datenbank kopiert. Sie können diese Beispieldatenbank in SQL-Datenbank erstellen, indem Sie den Anweisungen unter [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](../azure-sql/database/single-database-create-quickstart.md) folgen.
* Azure-Speicherkonto: Azure Storage wird im Massenkopiervorgang als _Staging_ blob verwendet. Falls Sie noch nicht über ein Azure-Speicherkonto verfügen, finden Sie Anweisungen dazu unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

> [!NOTE]
> Sie können die Erstellung einer neuen Data Factory überspringen, wenn Sie die Pipelinefunktion in Ihrem vorhandenen Synapse-Arbeitsbereich verwenden möchten, um die Daten zu laden.  Azure Synapse bettet die Funktionalität von Azure Data Factory in die Pipelinefunkton ein.

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus:

2. Geben Sie auf der Seite **Neue Data Factory** Werte für folgende Elemente an:

    * **Name**: Geben Sie als Namen *LoadSQLDWDemo* ein. Der Name der Data Factory muss global eindeutig sein. Wenn die Fehlermeldung „Data Factory mit dem Namen ‚LoadSQLDWDemo‘ ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_**ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. Diese Datenspeicher umfassen Azure Data Lake Store, Azure Storage, Azure SQL-Datenbank usw.

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Startseite für Azure Data Factory mit der Kachel „Open Azure Data Factory Studio“":::

   Klicken Sie auf der Kachel **Open Azure Data Factory Studio** auf **Öffnen**, um die Datenintegration-Anwendung in einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-synapse-analytics"></a>Laden von Daten in Azure Synapse Analytics

1. Wählen Sie auf der Startseite von dem Azure Data Factory- oder Azure Synapse-Arbeitsbereich die Kachel **Erfassen** aus, um das Tool „Daten kopieren“ zu starten.  Sie können dann die **Integrierte Kopieraufgabe** verwenden.

2. Wählen Sie auf der Seite **Eigenschaften** unter **Aufgabentyp** den Typ **Integrierte Kopieraufgabe** aus, und wählen Sie dann **Weiter** aus.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png" alt-text="Seite „Eigenschaften“":::

3. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:
    >[!TIP]
    >In diesem Tutorial verwenden Sie die *SQL-Authentifizierung* als Authentifizierungstyp für Ihren Quelldatenspeicher, Sie können bei Bedarf aber auch andere unterstützte Authentifizierungsmethoden auswählen: *Dienstprinzipal* oder *Verwaltete Identität*. Ausführlichere Informationen finden Sie in den entsprechenden Abschnitten [dieses Artikels](./connector-azure-sql-database.md#linked-service-properties).
    >Zum sicheren Speichern von Geheimnissen für Datenspeicher empfiehlt sich darüber hinaus die Verwendung einer Azure Key Vault-Instanz. Eine ausführliche Erläuterung finden Sie in [diesem Artikel](./store-credentials-in-key-vault.md).

    1. Wählen Sie **+ Neue Verbindung** aus.

    1. Wählen Sie im Katalog **Azure SQL-Datenbank** und dann **Weiter** aus. Sie können in das Suchfeld zum Filtern der Connectors „SQL“ eingeben.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png" alt-text="Auswählen der Azure SQL-Datenbank":::
    
    1. Wählen Sie auf der Seite **Neue Verbindung (Azure SQL Database)** in der Dropdownliste Ihren Server- und Datenbanknamen aus und geben Sie den Benutzernamen und das Kennwort an. Wählen Sie **Verbindung testen** aus, um die Einstellungen zu überprüfen. Wählen Sie dann **Erstellen** aus.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png" alt-text="Konfigurieren von Azure SQL-Datenbank":::
    
    1. Wählen Sie auf der Seite **Zieldatenspeicher** die neu erstellte Verbindung als Quelle in dem Bereich **Verbindung** aus.

    1. Geben Sie **SalesLT** in dem Abschnitt **Quelltabellen** ein, um die Tabellen zu filtern. Aktivieren Sie das Kontrollkästchen **(Alles auswählen)** , um alle Tabellen für den Kopiervorgang zu verwenden, und wählen Sie dann **Weiter** aus.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/source-data-store-page.png" alt-text="Ein Screenshot, der die Konfiguration der Seite „Quelldatenspeicher“ zeigt.":::

4. Geben Sie auf der Seite **Filter anwenden** die Einstellungen an, oder wählen Sie **Weiter** aus. Sie können eine Datenvorschau anzeigen und das Schema der Eingabedaten einsehen, indem Sie die Schaltfläche **Datenvorschau** auswählen. 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/apply-filter.png" alt-text="Ein Screenshot, der die Seite „Filter anwenden“ zeigt":::.

5. Führen Sie auf der Seite **Zieldatenspeicher** die folgenden Schritte aus:
    >[!TIP]
    >In diesem Tutorial verwenden Sie die *SQL-Authentifizierung* als Authentifizierungstyp für Ihren Zieldatenspeicher, Sie können bei Bedarf aber auch andere unterstützte Authentifizierungsmethoden auswählen: *Dienstprinzipal* oder *Verwaltete Identität*. Ausführlichere Informationen finden Sie in den entsprechenden Abschnitten [dieses Artikels](./connector-azure-sql-data-warehouse.md#linked-service-properties).
    >Zum sicheren Speichern von Geheimnissen für Datenspeicher empfiehlt sich darüber hinaus die Verwendung einer Azure Key Vault-Instanz. Eine ausführliche Erläuterung finden Sie in [diesem Artikel](./store-credentials-in-key-vault.md).

    1. Wählen Sie **+ Neue Verbindung** aus, um eine Verbindung hinzuzufügen.

    1. Wählen Sie im Katalog **Azure Synapse Analytics** aus, und klicken Sie auf **Weiter**.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png" alt-text="Auswählen von Azure Synapse Analytics":::

    1. Wählen Sie auf der Seite **Neue Verbindung (Azure Synapse Analytics)** in der Dropdownliste Ihren Server- und Datenbanknamen aus und geben Sie den Benutzernamen und das Kennwort an. Wählen Sie **Verbindung testen** aus, um die Einstellungen zu überprüfen. Wählen Sie dann **Erstellen** aus.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png" alt-text="Konfigurieren von Azure Synapse Analytics":::

    1. Wählen Sie auf der Seite **Zieldatenspeicher** die neu erstellte Verbindung als Senke in dem Bereich **Verbindung** aus.

6. Überprüfen Sie den Inhalt der Seite **Tabellenzuordnung** und klicken Sie dann auf **Weiter**. Eine intelligente Tabellenzuordnung wird angezeigt. Die Quelltabellen werden den Zieltabellen auf Grundlage der Tabellennamen zugeordnet. Wenn eine Quelltabelle im Ziel nicht vorhanden ist, wird von dem Service standardmäßig eine Zieltabelle mit dem gleichen Namen erstellt. Sie können eine Quelltabelle auch einer vorhandenen Zieltabelle zuordnen.

   :::image type="content" source="./media/load-azure-sql-data-warehouse/destination-data-store-page.png" alt-text="Ein Screenshot, der die Konfiguration der Seite „Zieldatenspeicher“ zeigt.":::

1. Überprüfen Sie den Inhalt der Seite **Spaltenzuordnung**, und wählen Sie dann **Weiter** aus. Die intelligente Tabellenzuordnung basiert auf dem Spaltennamen. Wenn Sie den Dienst die Tabellen automatisch erstellen lassen, kann eine Datentypkonvertierung erfolgen, wenn zwischen den Quell- und Zielspeichern Inkompatibilitäten vorliegen. Wenn es eine nicht unterstützte Datentypkonvertierung zwischen der Quell- und Zielspalte gibt, wird eine Fehlermeldung neben der entsprechenden Tabelle angezeigt.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/schema-mapping.png" alt-text="Seite „Spaltenzuordnung“":::

1. Führen Sie auf der Seite **Einstellungen** die folgenden Schritte aus:

    1. Geben Sie **CopyFromSQLToSQLDW** in das Feld **Aufgabenname** ein.
    1. Klicken Sie im Abschnitt **Stagingeinstellungen** auf **+ Neu**, um einen neuen Stagingspeicher zu erstellen. Der Speicher wird für das Staging der Daten verwendet, bevor diese mit PolyBase in Azure Synapse Analytics geladen werden. Nach Abschluss des Kopiervorgangs werden die vorläufigen Daten in Azure Blob Storage automatisch bereinigt.

    1. Wählen Sie auf der Seite **Neuer verknüpfter Dienst** Ihr Speicherkonto und dann **Erstellen** aus, um den verknüpften Dienst bereitzustellen.

    1. Deaktivieren Sie die Option **Typstandard verwenden**, und wählen Sie dann **Weiter** aus.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-polybase.png" alt-text="Konfigurieren von PolyBase":::

8. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**.

9. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen. 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/deployment-complete-page.png" alt-text="Ein Screenshot, der die Seite „Bereitstellung“ zeigt":::.
 
10. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. Wenn die Pipelineausführung erfolgreich abgeschlossen wurde, wählen Sie den Link **CopyFromSQLToSQLDW** unter der Spalte **Pipelinename** aus, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring.png" alt-text="Überwachen der Pipelineausführungen":::    

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring-synapse.png" alt-text="Überwachen der Pipelineausführungen":::   

--- 

12. Wählen Sie oben den Link **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/activity-monitoring.png" alt-text="Überwachung der Aktivitätsausführungen":::

1. Zum Überwachen der Ausführungsdetails jeder Kopieraktivität wählen Sie in der Ansicht der Aktivitätsausführungen unter **Aktivitätsname** den Link **Details** (Brillensymbol) aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png" alt-text="Überwachen von Details zur Aktivitätsausführung 1":::

    :::image type="content" source="./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png" alt-text="Überwachen von Details zur Aktivitätsausführung 2":::

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den folgenden Artikel, um mehr über die Unterstützung von Azure Synapse Analytics zu erfahren:

> [!div class="nextstepaction"]
>[Azure Synapse Analytics-Connector](connector-azure-sql-data-warehouse.md)
