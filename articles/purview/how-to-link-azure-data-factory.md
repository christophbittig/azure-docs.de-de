---
title: Herstellen einer Verbindung mit Azure Data Factory
description: In diesem Artikel wird beschrieben, wie Sie Azure Data Factory und Azure Purview verbinden, um die Datenherkunft nachzuverfolgen.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/01/2021
ms.openlocfilehash: 2ba2c135a0b07df6c6a4802c241b064b2ef94335
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851483"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Verbinden von Azure Data Factory und Azure Purview

In diesem Dokument werden die Schritte erläutert, die zum Verbinden eines Azure Data Factory-Kontos mit einem Azure Purview-Konto erforderlich sind, um die Datenherkunft nachzuverfolgen. Das Dokument enthält zudem Details zum Abdeckungsbereich und zu den unterstützten Herkunftsmustern.

## <a name="view-existing-data-factory-connections"></a>Anzeigen vorhandener Data Factory-Verbindungen

Mehrere Azure Data Factory-Instanzen können eine Verbindung mit einer einzelnen Azure Purview-Instanz herstellen, um Herkunftsinformationen per Push zu übertragen. Gemäß den aktuellen Grenzwerten können Sie vom Purview-Verwaltungscenter eine Verbindung mit bis zu zehn Data Factory-Konten gleichzeitig herstellen. Um die Liste der mit Ihrem Purview-Konto verbundenen Data Factory-Konten anzuzeigen, gehen Sie folgendermaßen vor:

1. Wählen Sie im linken Navigationsbereich **Verwaltung** aus.
2. Wählen Sie unter **Herkunftsverbindungen** **Data Factory** aus.
3. Die Liste mit Data Factory-Verbindungen wird angezeigt.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Screenshot einer Data Factory-Verbindungsliste" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Beachten Sie die verschiedenen Werte für den **Status** einer Verbindung:

    - **Verbunden**: Die Data Factory ist mit dem Purview-Konto verbunden.
    - **Getrennt**: Die Data Factory hat Zugriff auf den Katalog, ist aber mit einem anderen Katalog verbunden. Daher wird die Datenherkunft nicht automatisch an den Katalog gemeldet.
    - **CannotAccess**: Der aktuelle Benutzer hat keine Zugriff auf die Data Factory, daher ist der Verbindungsstatus unbekannt.

>[!Note]
>Damit Sie die Data Factory-Verbindungen anzeigen können, muss Ihnen die folgende Rolle zugewiesen sein. Die Vererbung von Rollen aus der Verwaltungsgruppe wird nicht unterstützt.
>Rolle **Sammlungsadministratoren** in der Stammsammlung.

## <a name="create-new-data-factory-connection"></a>Erstellen einer neuen Data Factory-Verbindung

>[!Note]
>Um die Data Factory-Verbindungen hinzufügen oder entfernen zu können, muss Ihnen die folgende Rolle zugewiesen sein. Die Vererbung von Rollen aus der Verwaltungsgruppe wird nicht unterstützt.
>Rolle **Sammlungsadministratoren** in der Stammsammlung.
>
> Darüber hinaus müssen die Benutzer die Rolle „Besitzer“ oder „Mitwirkender“ für die Data Factory aufweisen.

Führen Sie die folgenden Schritte aus, um eine vorhandene Data Factory mit Ihrem Purview-Konto zu verbinden. Sie können auch [über ADF eine Verbindung zwischen der Data Factory und dem Purview-Konto herstellen](../data-factory/connect-data-factory-to-azure-purview.md).

1. Wählen Sie im linken Navigationsbereich **Verwaltung** aus.
2. Wählen Sie unter **Herkunftsverbindungen** **Data Factory** aus.
3. Wählen Sie auf der Seite **Data Factory-Verbindung** die Option **neu** aus.

4. Wählen Sie Ihr Data Factory-Konto aus der Liste aus, und klicken Sie auf **OK**. Sie können die Liste auch nach Abonnementnamen filtern, um sie einzuschränken.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Screenshot: Verbinden von Azure Data Factory" lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Einige Data Factory-Instanzen sind möglicherweise deaktiviert, wenn die Data Factory bereits mit dem aktuellen Purview-Konto verbunden ist oder nicht über eine verwaltete Identität verfügt.

    Es wird eine Warnmeldung angezeigt, wenn eine der ausgewählten Data Factorys bereits mit einem anderen Purview-Konto verbunden ist. Durch Klicken auf „OK“ wird die Data Factory-Verbindung mit dem anderen Purview-Konto getrennt. Es sind keine weiteren Bestätigungen erforderlich.

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Screenshot: Warnung vor dem Trennen einer Azure Data Factory-Verbindung":::

>[!Note]
>Zurzeit wird das gleichzeitige Hinzufügen von höchstens zehn Data Factorys unterstützt. Wenn Sie mehr als zehn Data Factorys gleichzeitig hinzufügen möchten, erstellen Sie ein Supportticket.

### <a name="how-authentication-works"></a>Funktionsweise der Authentifizierung

Die verwaltete Identität für die Data Factory wird verwendet, um Pushvorgänge für Herkunftsdaten von der Data Factory zu Purview zu authentifizieren. Wenn Sie die Data Factory über die Benutzeroberfläche mit Purview verbinden, wird die Rollenzuweisung automatisch hinzugefügt.

Erteilen Sie der verwalteten Identität der Data Factory die Rolle **Datenkurator** für die Purview-**Stammsammlung**. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Purview](../purview/catalog-permissions.md) und unter [Hinzufügen von Rollen und Einschränken des Zugriffs über Sammlungen](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

### <a name="remove-data-factory-connections"></a>Entfernen von Data Factory-Verbindungen

Um eine Data Factory-Verbindung zu entfernen, gehen Sie folgendermaßen vor:

1. Klicken Sie auf der Seite **Data Factory-Verbindung** auf die Schaltfläche **Entfernen** neben mindestens einer Data Factory-Verbindung.
2. Klicken Sie in der angezeigten Popupmeldung auf **Bestätigen**, um die ausgewählten Data Factory-Verbindungen zu löschen.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Screenshot: Auswählen der Data Factorys, deren Verbindung entfernt werden soll" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="supported-azure-data-factory-activities"></a>Unterstützte Azure Data Factory-Aktivitäten

Azure Purview erfasst die Runtimeherkunft aus den folgenden Azure Data Factory-Aktivitäten:

- [Daten kopieren](../data-factory/copy-activity-overview.md)
- [Datenfluss](../data-factory/concepts-data-flow-overview.md)
- [Ausführen des SSIS-Pakets](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> Azure Purview verwirft die Herkunft, wenn die Quelle oder das Ziel ein nicht unterstütztes Datenspeichersystem verwendet.

Die Integration zwischen Data Factory und Purview unterstützt nur eine Teilmenge der Datensysteme, die von Data Factory unterstützt werden. Dies wird in den folgenden Abschnitten beschrieben.

[!INCLUDE[data-factory-supported-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

### <a name="execute-ssis-package-support"></a>Unterstützung der Aktivität „SSIS-Paket ausführen“

Weitere Informationen finden Sie in der [Liste mit unterstützten Datenspeichern](how-to-lineage-sql-server-integration-services.md#supported-data-stores).

## <a name="access-secured-azure-purview-account"></a>Zugreifen auf ein geschütztes Azure Purview-Konto
      
Wenn Ihr Purview-Konto durch eine Firewall geschützt ist, erfahren Sie, wie Sie Data Factory über private Purview-Endpunkte den [Zugriff auf ein geschütztes Purview-Konto](../data-factory/how-to-access-secured-purview-account.md) ermöglichen können.

## <a name="bring-data-factory-lineage-into-purview"></a>Einbringen von Data Factory-Herkunftsdaten in Purview

Eine exemplarische End-to-End-Vorgehensweise finden Sie im [Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](../data-factory/turorial-push-lineage-to-purview.md).

## <a name="supported-lineage-patterns"></a>Unterstützte Herkunftsmuster

Azure Purview unterstützt verschiedene Herkunftsmuster. Die generierten Herkunftsdaten basieren auf der Art der Quelle und der Senke, die in den Data Factory-Aktivitäten verwendet wird. Data Factory bietet zwar Unterstützung für über 80 Quellen und Senken, allerdings wird nur ein Teil davon auch in Azure Purview unterstützt. Eine Liste finden Sie unter [Unterstützte Azure Data Factory-Aktivitäten](#supported-azure-data-factory-activities).

Informationen dazu, wie Sie Data Factory für das Senden von Herkunftsdaten konfigurieren, finden Sie unter [Erste Schritte mit der Datenherkunft](catalog-lineage-user-guide.md#get-started-with-lineage).

Es gibt noch andere Möglichkeiten, Informationen in der Herkunftsansicht zu suchen. Hierzu gehören u. a. die folgenden:

- Zeigen Sie auf der Registerkarte **Herkunft** auf Formen, um in der QuickInfo eine Vorschau zusätzlicher Informationen zur Ressource anzuzeigen.
- Wählen Sie den Knoten oder Edge aus, um den zugehörigen Ressourcentyp anzuzeigen oder Ressourcen zu wechseln.
- Auf der linken Seite der Registerkarte **Herkunft** werden die Spalten eines Datasets angezeigt. Weitere Informationen zur Herkunft auf Spaltenebene finden Sie unter [Spaltenherkunft für Dataset](catalog-lineage-user-guide.md#dataset-column-lineage).

### <a name="data-lineage-for-11-operations"></a>Datenherkunft für 1:1-Vorgänge

Das gängigste Muster für die Erfassung der Datenherkunft ist das Verschieben von Daten von einem einzelnen Eingabedataset zu einem einzelnen Ausgabedataset mit einem Prozess dazwischen.

Im Folgenden sehen Sie ein Beispiel für dieses Muster:

- 1 Quelle/Eingabe: *Customer* (SQL-Tabelle)
- 1 Senke/Ausgabe: *Customer1.csv* (Azure-Blob)
- 1 Prozess: *CopyCustomerInfo1\#Customer1.csv* (Data Factory-Kopieraktivität)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Screenshot: Herkunft für einen Eins-zu-eins-Kopiervorgang von Data Factory":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Datenverschiebung mit 1:1-Herkunft und Unterstützung für Platzhalter

Ein weiteres gängiges Szenario für die Erfassung der Herkunft ist die Verwendung eines Platzhalters zum Kopieren von Dateien aus einem einzelnen Eingabedataset in ein einzelnes Ausgabedataset. Mit dem Platzhalter kann die Kopieraktivität anhand eines gemeinsamen Dateinamensteils mehrere Dateien für den Kopiervorgang abgleichen. Azure Purview erfasst die Herkunft auf Dateiebene für jede einzelne Datei, die von der entsprechenden Kopieraktivität kopiert wurde.

Im Folgenden sehen Sie ein Beispiel für dieses Muster:

* Quelle/Eingabe: *CustomerCall\*.csv* (ADLS Gen2-Pfad)
* Senke/Ausgabe: *CustomerCall\*.csv* (Azure-Blobdatei)
* 1 Prozess: *CopyGen2ToBlob\#CustomerCall.csv* (Data Factory-Kopieraktivität)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Screenshot: Herkunft für einen Eins-zu-eins-Kopiervorgang mit Unterstützung für Platzhalter" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Datenverschiebung mit n:1-Herkunft

Sie können Datenflussaktivitäten verwenden, um Datenvorgänge wie Merges, Joins usw. auszuführen. Um ein Zieldataset zu erzeugen, kann mehr als ein Quelldataset verwendet werden. In diesem Beispiel erfasst Azure Purview die Herkunft auf Dateiebene für einzelne Eingabedateien einer SQL-Tabelle, die zu einer Datenflussaktivität gehört.

Im Folgenden sehen Sie ein Beispiel für dieses Muster:

* 2 Quellen/Eingaben: *Customer.csv*, *Sales.parquet* (ADLS Gen2-Pfad)
* 1 Senke/Ausgabe: *companydata* (Azure SQL-Tabelle)
* 1 Prozess: *DataFlowBlobsToSQL* (Data Factory-Datenflussaktivität)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Screenshot: Herkunft eines N-zu-eins-ADF-Datenflussvorgangs" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Herkunft für Ressourcensätze

Ein Ressourcensatz ist ein logisches Objekt im Katalog, das viele Partitionsdateien im zugrunde liegenden Speicher repräsentiert. Weitere Informationen finden Sie unter [Grundlegendes zu Ressourcensätzen](concept-resource-sets.md). Wenn Azure Purview die Herkunft aus Azure Data Factory erfasst, werden die Regeln zur Normalisierung der einzelnen Partitionsdateien angewendet, und es wird ein einzelnes logisches Objekt erstellt.

Im folgenden Beispiel wird aus einem Azure-Blob ein Azure Data Lake Gen2-Ressourcensatz erzeugt:

* 1 Quelle/Eingabe: *Employee\_management.csv* (Azure-Blob)
* 1 Senke/Ausgabe: *Employee\_management.csv* (Azure Data Lake Gen2)
* 1 Prozess: *CopyBlobToAdlsGen2\_RS* (Data Factory-Kopieraktivität)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Screenshot: Herkunft für einen Ressourcensatz" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](../data-factory/turorial-push-lineage-to-purview.md)

[Benutzerhandbuch zur Katalogherkunft](catalog-lineage-user-guide.md)

[Azure Data Share-Datenherkunft](how-to-link-azure-data-share.md)