---
title: Laden von Daten aus SAP Business Warehouse
titleSuffix: Azure Data Factory & Azure Synapse
description: Kopieren von Daten aus SAP Business Warehouse (BW) mithilfe von Azure Data Factory oder Synapse Analytics
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/09/2021
ms.openlocfilehash: 70db6de930ad60da87180fdf28555e542a458ecf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824621"
---
# <a name="copy-data-from-sap-business-warehouse-with-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten aus SAP Business Warehouse mithilfe von Azure Data Factory oder Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel beschreibt, wie Sie Azure Data Factory- oder Synapse Analytics-Pipelines zum Kopieren von Daten aus SAP Business Warehouse (BW) über Open Hub nach Azure Data Lake Storage Gen2 verwenden. Mit einer ähnlichen Vorgehensweise können Sie Daten in andere [unterstützte Senkendatenspeicher](copy-activity-overview.md#supported-data-stores-and-formats) kopieren.

> [!TIP]
> Allgemeine Informationen zum Kopieren von Daten aus SAP BW, einschließlich SAP BW Open Hub-Integration und Ablauf der Deltaextraktion, finden Sie unter [Kopieren von Daten aus SAP Business Warehouse über Open Hub mithilfe von Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Voraussetzungen

- **Azure Data Factory- oder Synapse-Arbeitsbereich**: Befolgen Sie die Schritte zum [Erstellen einer Data Factory](quickstart-create-data-factory-portal.md#create-a-data-factory) oder zum [Erstellen eines Synapse-Arbeitsbereichs](../synapse-analytics/quickstart-create-workspace.md), wenn Sie über keinen Arbeitsbereich verfügen.

- **SAP BW Open Hub Destination (OHD) mit dem Zieltyp „Datenbanktabelle“** : Informationen zum Erstellen eines OHD oder zum Überprüfen, ob Ihr OHD ordnungsgemäß für die Integration in den Dienst konfiguriert ist, finden Sie im Abschnitt [SAP BW Open Hub Destination-Konfigurationen](#sap-bw-open-hub-destination-configurations) dieses Artikels.

- **Der SAP BW-Benutzer benötigt die folgenden Berechtigungen**:

  - Autorisierung für Remotefunktionsaufrufe (RFC, Remote Function Calls) und SAP BW
  - Berechtigungen für die Aktivität „Execute“ des Autorisierungsobjekts **S_SDSAUTH**

- **Eine [selbstgehostete Integration Runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) mit SAP .NET Connector 3.0**. Führen Sie die folgenden Einrichtungsschritte aus:

  1. Installieren und registrieren Sie die selbstgehostete Integration Runtime, Version 3.13 oder höher. (Dieser Vorgang wird weiter unten in diesem Artikel beschrieben.)

  2. Laden Sie den [SAP Connector für Microsoft .NET 3.0 (64 Bit)](https://support.sap.com/en/product/connectors/msnet.html) von der SAP-Website herunter, und installieren Sie ihn auf demselben Computer wie die selbstgehostete IR. Vergewissern Sie sich bei der Installation, dass Sie im Dialogfeld **Optionale Einrichtungsschritte** die Option **Assemblys in GAC installieren** wie in der folgenden Abbildung gezeigt auswählen:

     :::image type="content" source="media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png" alt-text="Dialogfeld zum Einrichten von SAP .NET Connector":::

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Vollständiges Kopieren aus SAP BW Open Hub

Navigieren Sie im Azure-Portal zu Ihrem Dienst. Wählen Sie **Öffnen** auf der Kachel **Azure Data Factory Studio öffnen** oder **Synapse Studio öffnen** aus, um die Benutzeroberfläche des Diensts in einer separaten Registerkarte zu öffnen.

1. Wählen Sie auf der Homepage **Erfassen** aus, um das Tool zum Daten kopieren zu starten.

2. Wählen Sie auf der Seite **Eigenschaften** die Option **Integrierte Kopieraufgabe** unter dem **Aufgabentyp** aus. Wählen Sie **Jetzt einmal ausführen** unter **Aufgabenintervall oder Aufgabenzeitplan** und dann **Weiter** aus.

3. Auf der Seite **Quelldatenspeicher** wählen Sie **+ Neue Verbindung** aus. Wählen Sie im Connectorkatalog **SAP BW Open Hub** aus, und klicken Sie dann auf **Weiter**. Zum Filtern der Connectors können Sie **SAP** in das Suchfeld eingeben.

4. Führen Sie auf der Seite **Neue Verbindung (SAP BW Open Hub)** die folgenden Schritte aus, um eine neue Verbindung zu erstellen.

   1. Wählen Sie in der Liste **Über Integration Runtime verbinden** eine vorhandene selbstgehostete IR aus. Sie können auch eine IR erstellen, wenn noch keine vorhanden ist.

      Zum Erstellen einer neuen selbstgehosteten IR wählen Sie **+Neu** und dann **Selbstgehostet** aus. Geben Sie im Feld **Name** einen Namen ein, und klicken Sie dann auf **Weiter**. Wählen Sie für die Installation auf dem aktuellen Computer die Option **Express-Setup** aus, oder führen Sie die bereitgestellten Schritte für **Manuelles Setup** aus.

      Vergewissern Sie sich wie unter [Voraussetzungen](#prerequisites) erwähnt, dass SAP Connector für Microsoft .NET 3.0 auf demselben Computer installiert ist, auf dem auch die selbstgehostete IR ausgeführt wird.

   2. Tragen Sie für SAP BW den **Servernamen**, die **Systemnummer**, die **Client-ID**, die **Sprache** (sofern nicht **EN**), den **Benutzernamen** und das **Kennwort** ein.

   3. Wählen Sie **Verbindung testen** aus, um die Einstellungen zu überprüfen, und wählen Sie dann **Erstellen** aus.

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png" alt-text="Seite zum Erstellen eines verknüpften SAP BW Open Hub-Diensts":::

   4. Wählen Sie auf der Seite **Quelldatenspeicher** die neu erstellte Verbindung in dem Block **Verbindung** aus.

   5. Durchsuchen Sie auf der Seite Open Hub-Ziele auswählen die in Ihrem SAP BW verfügbaren Open Hub-Ziele. Sie können eine Vorschau der Daten in jedem Ziel anzeigen, indem Sie die Vorschauschaltfläche am Ende jeder Zeile auswählen. Wählen Sie das OHD aus, aus dem Daten kopiert werden sollen, und klicken Sie dann auf **Weiter**.
   
   :::image type="content" source="./media/load-sap-bw-data/source-data-store-page.png" alt-text="Screenshot der Seite „Quelldatenspeicher“.":::

5. Geben Sie bei Bedarf einen Filter an. Wenn Ihr OHD nur Daten aus einer einzelnen Ausführung eines Datenübertragungsprozesses (Data Transfer Process, DTP) mit einer einzelnen Anforderungs-ID enthält, oder wenn Sie sicher sind, dass Ihr DTP fertig gestellt ist und Sie die Daten kopieren möchten, deaktivieren Sie das Dialogfeld **Letzte Anforderung ausschließen** im Abschnitt **Advanced**. Über die **Datenvorschau** Schaltfläche können Sie die Daten vorab ansehen.

   Weitere Informationen zu diesen Einstellungen finden Sie im Abschnitt [SAP BW Open Hub Destination-Konfigurationen](#sap-bw-open-hub-destination-configurations) dieses Artikels. Wählen Sie **Weiter** aus.

   :::image type="content" source="media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png" alt-text="Konfigurieren des SAP BW Open Hub-Filters":::

6. Wählen Sie auf der Seite **Zieldatenspeicher** die Optionen **+ Neue Verbindung** > **Azure Data Lake Storage Gen2** > **Weiter** aus.

7. Führen Sie auf der Seite **Neue Verbindung (Azure Data Lake Storage Gen2)** die folgenden Schritte aus, um eine Verbindung zu erstellen.
   1. Wählen Sie in der Dropdownliste **Name** das Data Lake Storage Gen2-fähige Konto aus.
   2. Wählen Sie **Erstellen** aus, um die Verbindung zu erstellen.

   :::image type="content" source="media/load-sap-bw-data/create-adls-gen2-linked-service.png" alt-text="Seite zum Erstellen eines verknüpften ADLS Gen2-Diensts":::

8. Wählen Sie auf der Seite **Zieldatenspeicher** die neu erstellte Verbindung in dem Abschnitt **Verbindung** und geben Sie **copyfromopenhub** als den Namen für den Ausgabeordner ein. Wählen Sie **Weiter** aus.

   :::image type="content" source="./media/load-sap-bw-data/destination-data-store-page.png" alt-text="Screenshot der Seite „Zieldatenspeicher“.":::

9. Klicken Sie auf der Seite **Dateiformateinstellung** auf **Weiter**, um die Standardeinstellungen zu verwenden.

    :::image type="content" source="media/load-sap-bw-data/specify-sink-format.png" alt-text="Seite zum Angeben des Senkenformats":::

10. Spezifizieren Sie auf der Seite **Einstellungen** einen **Aufgabenamen** und klappen Sie **Erweitern** auf. Geben Sie einen Wert für **Parallelitätsgrad des Kopierens** ein, z.B. „5“, zum parallelen Laden aus SAP BW. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/load-sap-bw-data/configure-copy-settings.png" alt-text="Konfigurieren von Kopiereinstellungen":::

11. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen. Wählen Sie **Weiter** aus.

    :::image type="content" source="./media/load-sap-bw-data/summary-page.png" alt-text="Screenshot der Seite „Zusammenfassung“.":::

12. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline zu überwachen.

13. Beachten Sie, dass die Registerkarte **Überwachen** links auf der Seite automatisch ausgewählt ist. Über die Links in der Spalte **Name der Pipeline** auf der Seite **Pipelineausführungen** können Sie die Aktivitätsdetails anzeigen und die Pipeline erneut ausführen.

14. Um die der Pipelineausführung zugeordneten Aktivitätsausführungen anzuzeigen, wählen Sie den Link in der Spalte **Pipelinename** aus. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Wählen Sie oben den Link **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**.

    :::image type="content" source="media/load-sap-bw-data/activity-monitoring.png" alt-text="Bildschirm zur Aktivitätsüberwachung":::

15. Um die Ausführungsdetails für jede Kopieraktivität zu überwachen, wählen Sie den Link **Details**, der sich als Brillensymbol in derselben Zeile jeder Kopieraktivität in der Aktivitätsüberwachungsansicht befindet. Verfügbare Details umfassen das aus der Quelle in die Senke kopierte Datenvolumen, den Datendurchsatz, die Ausführungsschritte und die Dauer sowie die verwendeten Konfigurationen.

    :::image type="content" source="media/load-sap-bw-data/activity-monitoring-details.png" alt-text="Details der Aktivitätsüberwachung":::

16. Um die maximale **Anforderungs-ID** jeder Kopieraktivität anzuzeigen, kehren Sie zur Aktivitätsüberwachungsansicht zurück und wählen in derselben Zeile jeder Kopieraktivität die Option **Ausgabe**.

    :::image type="content" source="media/load-sap-bw-data/activity-output.png" alt-text="Bildschirm zur Aktivitätsausgabe":::

    :::image type="content" source="media/load-sap-bw-data/activity-output-details.png" alt-text="Detailansicht der Aktivitätsausgabe":::

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Inkrementelles Kopieren aus SAP BW Open Hub

> [!TIP]
> Informationen dazu, wie der SAP BW Open Hub-Connector inkrementelle Daten aus SAP BW kopiert, finden Sie unter [Ablauf der Deltaextraktion für den SAP BW Open Hub-Connector](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow). In diesem Artikel wird auch die grundlegende Connectorkonfiguration erläutert.

Lassen Sie uns nun das inkrementelle Kopieren aus SAP BW Open Hub konfigurieren.

Beim inkrementellen Kopieren wird ein Mechanismus des Typs „hoher Grenzwert“ verwendet, der auf der **Anforderungs-ID** basiert. Diese ID wird durch den Datenübertragungsprozess automatisch in SAP BW Open Hub Destination generiert. Dieser Workflow ist im folgenden Diagramm dargestellt:

:::image type="content" source="media/load-sap-bw-data/incremental-copy-workflow.png" alt-text="Flussdiagramm zum Workflow für inkrementelles Kopieren":::

Wählen Sie auf der Startseite im Abschnitt **Mehr entdecken** die Option **Pipelinevorlagen** aus, um die integrierte Vorlage zu verwenden.

1. Suchen Sie nach **SAP BW**, um die Vorlage **Inkrementelles Kopieren aus SAP BW in Azure Data Lake Storage Gen2** zu finden und auszuwählen. Mit dieser Vorlage werden Daten in Azure Data Lake Storage Gen2 kopiert. Zum Kopieren in andere Senkentypen können Sie einen ähnlichen Workflow verwenden.

2. Wählen Sie auf der Hauptseite der Vorlage die folgenden drei Verbindungen aus, oder erstellen Sie sie, und wählen Sie dann unten rechts im Fenster die Option **Diese Vorlage verwenden** aus.

   - **Azure Blob Storage**: In dieser exemplarischen Vorgehensweise verwenden wir Azure Blob Storage, um den hohen Grenzwert zu speichern, der die *maximale Anzahl der kopierten Anforderungs-IDs* ist.
   - **SAP BW Open Hub**: Dies ist die Quelle, aus der Daten kopiert werden. Die detaillierte Konfiguration finden Sie in der vorherigen exemplarischen Vorgehensweise zum vollständigen Kopieren.
   - **Azure Data Lake Storage Gen2**: Dies ist die Senke, in die Daten kopiert werden. Die detaillierte Konfiguration finden Sie in der vorherigen exemplarischen Vorgehensweise zum vollständigen Kopieren.

   :::image type="content" source="media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png" alt-text="Inkrementelles Kopieren aus SAP BW-Vorlage":::

3. Diese Vorlage generiert eine Pipeline mit den folgenden drei Aktivitäten und verkettet sie bei Erfolg: *Suchen*, *Daten kopieren* und *Web*.

   Wechseln Sie zur Registerkarte **Parameter** der Pipeline. Sie sehen alle Konfigurationen, die Sie bereitstellen müssen.

   :::image type="content" source="media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png" alt-text="Konfiguration für inkrementelles Kopieren aus SAP BW":::

   - **SAPOpenHubDestinationName**: Geben Sie den Namen der Open Hub-Tabelle an, aus der Daten kopiert werden sollen.

   - **Data_Destination_Container:** Geben Sie den Zielcontainer von Azure Data Lake Storage Gen2 an, in den Daten kopiert werden sollen. Wenn der Container nicht vorhanden ist, erstellt die Copy-Aktivität während der Ausführung einen Container.
  
   - **Data_Destination_Directory:** Geben Sie den Ordnerpfad des Containers von Azure Data Lake Storage Gen2 an, in den Daten kopiert werden sollen. Wenn der Pfad nicht vorhanden ist, erstellt die Copy-Aktivität während der Ausführung einen Pfad.
  
   - **HighWatermarkBlobContainer:** Geben Sie den Container zum Speichern des hohen Grenzwerts an.

   - **HighWatermarkBlobDirectory:** Geben Sie den Ordnerpfad zum Speichern des hohen Grenzwerts an.

   - **HighWatermarkBlobName**: Geben Sie den Namen des Blobs zum Speichern des hohen Grenzwerts an, z.B. `requestIdCache.txt`. Wechseln Sie in Blob Storage zum entsprechenden Pfad von „HighWatermarkBlobPath+HighWatermarkBlobName“, z.B. *container/path/requestIdCache.txt*. Erstellen Sie einen Blob mit Inhalt 0.

      :::image type="content" source="media/load-sap-bw-data/blob.png" alt-text="Blobinhalt":::

   - **LogicAppURL**: In dieser Vorlage verwenden wir eine Webaktivität zum Aufrufen von Azure Logic Apps, um den hohen Grenzwert im Blob-Speicher festzulegen. Sie können auch Azure SQL-Datenbank zum Speichern verwenden. Verwenden Sie eine Aktivität für gespeicherte Prozeduren, um den Wert zu aktualisieren.

      Sie müssen zuerst eine Logik-App erstellen, wie es im folgenden Bild gezeigt wird. Fügen Sie dann die **HTTP-POST-URL** ein.

      :::image type="content" source="media/load-sap-bw-data/logic-app-config.png" alt-text="Logik-App-Konfiguration":::

      1. Öffnen Sie das Azure-Portal. Wählen Sie einen neuen **Logic Apps**-Dienst aus. Wählen Sie **+Leere Logik-App** aus, um zum **Designer für Logik-Apps** zu wechseln.

      2. Erstellen Sie einen Trigger **Beim Empfang einer HTTP-Anforderung**. Geben Sie den HTTP-Anforderungstext wie folgt an:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Fügen Sie eine Aktion **Blob erstellen** hinzu. Verwenden Sie für **Ordnerpfad** und **Blob-Name** dieselben Werte, die Sie zuvor in *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* und *HighWatermarkBlobName* konfiguriert haben.

      4. Wählen Sie **Speichern** aus. Kopieren Sie dann den Wert von **HTTP-POST-URL**, um ihn in der Pipeline zu verwenden.

4. Nachdem Sie die Pipelineparameter angegeben haben, wählen Sie **Debuggen** > **Fertig stellen** aus, um eine Ausführung zum Überprüfen der Konfiguration aufzurufen. Sie können auch **Veröffentlichen** auswählen, um alle Änderungen zu veröffentlichen, und dann **Trigger hinzufügen** für eine Ausführung auswählen.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub Destination-Konfigurationen

In diesem Abschnitt wird erläutert, wie Sie die SAP BW-Seite so konfigurieren, dass der SAP BW Open Hub-Connector zum Kopieren von Daten verwendet wird.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurieren der Deltaextraktion in SAP BW

Wenn Sie sowohl historisches Kopieren als auch inkrementelles Kopieren oder nur inkrementelles Kopieren benötigen, konfigurieren Sie die Deltaextraktion in SAP BW.

1. Erstellen Sie das OHD (Open Hub Destination). Sie können das OHD in SAP Transaction RSA1 erstellen, das automatisch die erforderliche Transformation und den Datenübertragungsprozess erstellt. Verwenden Sie folgende Einstellungen:

   - **ObjectType**: Sie können einen beliebigen Objekttyp verwenden. Hier verwenden wir **InfoCube** als Beispiel.
   - **Zieltyp**: Wählen Sie **Datenbanktabelle** aus.
   - **Schlüssel der Tabelle**: Wählen Sie **Technischer Schlüssel** aus.
   - **Extraktion**: Wählen Sie **Daten beibehalten und Datensätze in Tabelle einfügen** aus.

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta.png" alt-text="Dialogfeld zum Erstellen der SAP BW OHD-Deltaextraktion":::

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta2.png" alt-text="Dialogfeld zum Erstellen der SAP BW OHD-Deltaextraktion 2":::

   Sie können die Anzahl parallel ausgeführter SAP-Arbeitsprozesse für die DTP erhöhen:

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta3.png" alt-text="Screenshot: Einstellungen für die Parallelverarbeitung. Dort können Sie die Anzahl paralleler Prozesse für die DTP auswählen.":::

2. Planen Sie den Datenübertragungsprozess (DTP) in Prozessketten.

   Ein Delta-DTP für einen Cube funktioniert nur, wenn die erforderlichen Zeilen nicht komprimiert wurden. Stellen Sie sicher, dass die BW-Cube-Komprimierung nicht vor dem DTP zur Open Hub-Tabelle ausgeführt wird. Die einfachste Möglichkeit hierfür ist das Integrieren des DTP in Ihre vorhandenen Prozessketten. Im folgenden Beispiel wird der DTP (zum OHD) in die Prozesskette zwischen den Schritten *Anpassen* (Rollup aggregieren) und *Reduzieren* (Cube-Komprimierung) eingefügt.

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-process-chain.png" alt-text="Flussdiagramm zum Erstellen der SAP BW-Prozesskette":::

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurieren der vollständigen Extraktion in SAP BW

Zusätzlich zur Deltaextraktion können Sie auch eine vollständige Extraktion desselben SAP BW InfoProvider einrichten. Dies trifft in der Regel zu, wenn Sie vollständig und nicht inkrementell kopieren oder die [Deltaextraktion neu synchronisieren](#resync-delta-extraction) möchten.

Sie können nicht mehr als einen DTP für dasselbe OHD haben. Daher müssen Sie vor der Deltaextraktion ein zusätzliches OHD erstellen.

:::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-full.png" alt-text="Erstellen eines SAP BW OHD für vollständigen Vorgang":::

Wählen Sie für ein OHD mit vollständigem Ladevorgang andere Optionen aus als für die Deltaextraktion:

- Im OHD: Legen Sie die Option **Extraktion** auf **Daten löschen und Datensätze einfügen** fest. Andernfalls werden die Daten viele Male extrahiert, wenn Sie den DTP in einer BW-Prozesskette wiederholen.

- Im DTP: Setzen Sie den **Extraktionsmodus** auf **Vollständig**. Sie müssen den automatisch erstellten DTP unmittelbar nach dem Erstellen des OHD von **Delta** in **Vollständig** ändern, wie es im folgenden Bild gezeigt ist:

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-full2.png" alt-text="Dialogfeld zum Erstellen eines SAP BW OHD mit Konfiguration für &quot;vollständige&quot; Extraktion":::

- Im BW Open Hub-Connector: Deaktivieren Sie **Letzte Anforderung ausschließen**. Andernfalls wird nichts extrahiert.

Den vollständigen DTP führen Sie in der Regel manuell aus. Alternativ können Sie eine Prozesskette für den vollständigen DTP erstellen. Dies ist in der Regel eine separate Kette, die von Ihren vorhandenen Prozessketten unabhängig ist. In beiden Fällen müssen Sie *sicherstellen, dass der DTP abgeschlossen ist, bevor Sie die Extraktion mit Copy beginnen*. Andernfalls werden nur partielle Daten kopiert.

### <a name="run-delta-extraction-the-first-time"></a>Erstmalige Ausführung der Deltaextraktion

Die erste Deltaextraktion ist technisch gesehen eine *vollständige Extraktion*. Der SAP BW Open Hub-Connector schließt beim Kopieren der Daten standardmäßige die letzte Anforderung aus. Im Fall der ersten Deltaextraktion werden von der Copy-Aktivität erst dann Daten extrahiert, wenn ein nachfolgender DTP Deltadaten mit einer gesonderten Anforderungs-ID in der Tabelle generiert hat. Dieses Szenario kann auf zwei Arten vermieden werden:

- Deaktivieren Sie die Option **Letzte Anforderung ausschließen** für die erste Deltaextraktion. Stellen Sie sicher, dass der erste Delta-DTP abgeschlossen ist, bevor Sie die Deltaextraktion zum ersten Mal starten.
-  Verwenden Sie das Verfahren zum erneuten Synchronisieren der Deltaextraktion, wie es im folgenden Abschnitt beschrieben ist.

### <a name="resync-delta-extraction"></a>Erneutes Synchronisieren der Deltaextraktion

In den folgenden Szenarien werden die Daten in SAP BW-Cubes geändert, vom Delta-DTP aber nicht berücksichtigt:

- SAP BW – selektives Löschen (von Zeilen mithilfe einer beliebigen Filterbedingung)
- SAP BW – Anforderung löschen (von fehlerhaften Anforderungen)

Ein SAP Open Hub Destination ist kein Data Mart-kontrolliertes Datenziel (in allen SAP BW-Supportpaketen seit 2015). Deshalb können Sie Daten aus einem Cube löschen, ohne die Daten im OHD zu ändern. Anschließend müssen Sie die Daten des Cubes mit dem Dienst erneut synchronisieren:

1. Ausführen einer vollständige Extraktion im Dienst (mithilfe eines vollständigen DTP in SAP)
2. Löschen Sie alle Zeilen in der Open Hub-Tabelle für den Delta-DTP.
3. Legen Sie den Status des Delta-DTP auf **Abgerufen** fest.

Anschließend funktionieren alle nachfolgenden Delta-DTPs und Deltaextraktionen wie erwartet.

Mit der folgenden Option können Sie den Delta-DTP manuell ausführen und seinen Status auf **Abgerufen** setzen:

*No Data Transfer; Delta Status in Source: Fetched* (Keine Datenübertragung; Deltastatus in Quelle: Abgerufen).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Unterstützung von SAP BW Open Hub-Connector:

> [!div class="nextstepaction"]
>[SAP Business Warehouse Open Hub-Connector](connector-sap-business-warehouse-open-hub.md)
