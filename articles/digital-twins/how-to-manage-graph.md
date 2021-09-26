---
title: Verwalten des Zwillingsgraphen und der Beziehungen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie einen Graphen von digitalen Zwillingen verwalten, indem Sie sie über Beziehungen verbinden.
author: baanders
ms.author: baanders
ms.date: 9/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5c02ad72ace0568f7be1062993adaf154bb31cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624309"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Verwalten eines Graphen von digitalen Zwillingen mithilfe von Beziehungen

Das Herzstück von Azure Digital Twins ist der [Zwillingsgraph](concepts-twins-graph.md), der Ihre gesamte Umgebung darstellt. Der Zwillingsgraph setzt sich aus einzelnen digitalen Zwillingen zusammen, die über **Beziehungen** verbunden sind. 

Wenn Sie über eine funktionierende [Azure Digital Twins-Instanz](how-to-set-up-instance-portal.md) verfügen und Code zur [Authentifizierung](how-to-authenticate-client.md) in Ihrer Client-App eingerichtet haben, können Sie digitale Zwillinge und ihre Beziehungen in einer Azure Digital Twins-Instanz erstellen, ändern und löschen.

Dieser Artikel konzentriert sich auf die Verwaltung von Beziehungen und den Graphen als Ganzes. Für die Arbeit mit einzelnen digitalen Zwillingen finden Sie weitere Informationen unter [Verwalten digitaler Zwillinge](how-to-manage-twin.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Screenshot von Azure Digital Twins-Explorer mit Beispielmodellen und -zwillingen." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::


## <a name="create-relationships"></a>Erstellen von Beziehungen

Beziehungen beschreiben, wie verschiedene digitale Zwillinge miteinander verbunden sind, was die Grundlage des Zwillingsgraphen bildet.

Beziehungen werden mit dem `CreateOrReplaceRelationshipAsync()`-Aufruf erstellt. 

Sie müssen Folgendes angeben, um eine Beziehung zu erstellen:
* Die ID des Quellzwillings (`srcId` im nachfolgenden Codebeispiel): Die ID des Zwillings, von dem die Beziehung ausgeht.
* Die ID des Zielzwillings (`targetId` im nachfolgenden Codebeispiel): Die ID des Zwillings, an den die Beziehung gerichtet ist.
* Ein Beziehungsname (`relName` im nachfolgenden Codebeispiel): Der generische Typ der Beziehung, z. B. _Enthält_.
* Eine Beziehungs-ID (`relId` im nachfolgenden Codebeispiel): Der spezifische Name für diese Beziehung, z. B. _Beziehung1_.

Die Beziehungs-ID muss innerhalb des angegebenen Quellzwillings eindeutig sein. Sie muss nicht global eindeutig sein.
Für den Zwilling „Foo“ muss z. B. jede bestimmte Beziehungs-ID eindeutig sein. Ein weiterer Zwilling „Bar“ kann jedoch eine ausgehende Beziehung aufweisen, die derselben ID einer Beziehung mit „Foo“ entspricht.

Im folgenden Codebeispiel wird veranschaulicht, wie Sie in Ihrer Azure Digital Twins-Instanz eine Beziehung erstellen. Der SDK-Aufruf (hervorgehoben) innerhalb einer benutzerdefinierten Methode wird verwendet, die im Kontext eines größeren Programms angezeigt werden kann.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Diese benutzerdefinierte Funktion kann nun aufgerufen werden, um folgendermaßen eine _contains_-Beziehung zu erstellen: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Wenn Sie mehrere Beziehungen erstellen möchten, können Sie Aufrufe derselben Methode wiederholen und dabei verschiedene Beziehungstypen an das Argument übergeben. 

Weitere Informationen zur Hilfsklasse `BasicRelationship` finden Sie unter [Azure Digital Twins-APIs und -SDKs](concepts-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Erstellen mehrerer Beziehungen zwischen Zwillingen

Beziehungen können wie folgt klassifiziert werden: 

* Ausgehende Beziehungen: Beziehungen, die zu diesem Zwilling gehören, die nach außen gerichtet sind, um eine Verbindung mit anderen Zwillingen herzustellen. Die Methode `GetRelationshipsAsync()` wird verwendet, um ausgehende Beziehungen eines Zwillings abzurufen.
* Eingehende Beziehungen: Beziehungen, die zu anderen Zwillingen gehören, die auf diesen Zwilling verweisen, um eine „eingehende“ Verbindung zu erstellen. Die Methode `GetIncomingRelationshipsAsync()` wird verwendet, um eingehende Beziehungen eines Zwillings abzurufen.

Zwischen Zwillingen können beliebig viele Beziehungen eingerichtet sein. 

Das bedeutet, Sie können gleichzeitig verschiedene Arten von Beziehungen zwischen zwei Zwillingen ausdrücken. So kann z. B. Zwilling A sowohl eine *gespeicherte* als auch eine *erstellte* Beziehung mit Zwilling B haben.

Sie können bei Bedarf sogar mehrere Instanzen desselben Beziehungstyps zwischen zwei Zwillingen erstellen. In diesem Beispiel könnte Zwilling A zwei verschiedene *gespeicherte* Beziehungen mit Zwilling B aufweisen, solange die Beziehungen unterschiedliche Beziehungs-IDs verwenden.

## <a name="list-relationships"></a>Auflisten von Beziehungen

### <a name="list-properties-of-a-single-relationship"></a>Auflisten der Eigenschaften einer einzelnen Beziehung

Beziehungsdaten können jederzeit in einen Typ Ihrer Wahl deserialisiert werden. Verwenden Sie für grundlegenden Zugriff auf eine Beziehung den Typ `BasicRelationship`. Die Hilfsklasse `BasicRelationship` bietet Ihnen über ein `IDictionary<string, object>`-Element auch Zugriff auf Eigenschaften, die für die Beziehung definiert sind. Zum Auflisten von Eigenschaften können Sie Folgendes verwenden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

### <a name="list-outgoing-relationships-from-a-digital-twin"></a>Auflisten der ausgehenden Beziehungen für einen digitalen Zwilling

Sie können die Methode `GetRelationships()` wie folgt verwenden, um auf die Liste der **ausgehenden** Beziehungen für einen bestimmten Zwilling im Graphen zuzugreifen: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Diese Methode gibt `Azure.Pageable<T>` oder `Azure.AsyncPageable<T>` zurück, je nachdem, ob Sie die synchrone oder asynchrone Version des Aufrufs verwenden.

Nachstehend finden Sie ein Beispiel, das eine Liste von Beziehungen abruft. Der SDK-Aufruf (hervorgehoben) innerhalb einer benutzerdefinierten Methode wird verwendet, die im Kontext eines größeren Programms angezeigt werden kann.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Sie können diese benutzerdefinierte Methode jetzt aufrufen, um die ausgehenden Beziehungen der Zwillinge wie folgt anzuzeigen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Sie können anhand der abgerufenen Beziehungen zu anderen Zwillingen in Ihrem Diagramm navigieren, indem Sie die Informationen im Feld `target` der zurückgegebenen Beziehung auslesen und als ID für den nächsten Aufruf von `GetDigitalTwin()` verwenden.

### <a name="list-incoming-relationships-to-a-digital-twin"></a>Auflisten von eingehenden Beziehungen für einen digitalen Zwilling

Azure Digital Twins verfügt außerdem über ein SDK, mit dem alle **eingehenden** Beziehungen für einen bestimmten Zwilling ermittelt werden können. Dieses SDK ist oft nützlich bei der umgekehrten Navigation oder beim Löschen eines Zwillings.

>[!NOTE]
> `IncomingRelationship`-Aufrufe geben nicht den vollständigen Text der Beziehung zurück. Weitere Informationen finden Sie in der [Referenzdokumentation](/dotnet/api/azure.digitaltwins.core.incomingrelationship?view=azure-dotnet&preserve-view=true) zur `IncomingRelationship`-Klasse.

Das Codebeispiel oben hat sich auf die Suche nach ausgehenden Beziehungen eines Zwillings konzentriert. Das folgende Beispiel ist ähnlich strukturiert, findet aber stattdessen *eingehende* Beziehungen vom Zwilling. Für dieses Beispiel wird auch der SDK-Aufruf (hervorgehoben) innerhalb einer benutzerdefinierten Methode verwendet, die im Kontext eines größeren Programms angezeigt werden kann.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Sie können diese benutzerdefinierte Methode jetzt aufrufen, um die eingehenden Beziehungen der Zwillinge wie folgt anzuzeigen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Auflisten aller Zwillingseigenschaften und -beziehungen

Mithilfe der oben genannten Methoden zur Auflistung der aus- und eingehenden Beziehungen zu einem Zwilling können Sie eine Methode erstellen, die vollständige Zwillingsinformationen ausgibt, einschließlich der Eigenschaften des Zwillings und beider Arten seiner Beziehungen. Hier finden Sie eine benutzerdefinierte Beispielmethode, die zeigt, wie Sie die obigen Methoden zu diesem Zweck kombinieren.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Sie können nun diese benutzerdefinierte Funktion wie folgt aufrufen: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Aktualisieren von Beziehungen

Beziehungen werden mit der `UpdateRelationship`-Methode aktualisiert. 

>[!NOTE]
>Diese Methode dient zur Aktualisierung der **Eigenschaften** einer Beziehung. Wenn Sie den Quellzwilling oder den Zielzwilling der Beziehung ändern müssen, müssen Sie die [Beziehung löschen](#delete-relationships) und mithilfe der neuen Zwillinge [eine neue erstellen](#create-relationships).

Die erforderlichen Parameter für den Clientaufruf lauten folgendermaßen:
- Die ID des Quellzwillings (der Zwilling, von dem die Beziehung ausgeht).
- Die ID des Beziehungsobjekts, das aktualisiert werden soll.
- Ein [JSON-Patch](http://jsonpatch.com/)-Dokument, das die Eigenschaften und neuen Werte enthält, die Sie aktualisieren möchten.

Hier sehen Sie einen beispielhaften Codeausschnitt, der die Verwendung dieser Methode zeigt. Für dieses Beispiel wird der SDK-Aufruf (hervorgehoben) innerhalb einer benutzerdefinierten Methode verwendet, die im Kontext eines größeren Programms angezeigt werden kann.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Nachfolgend sehen Sie einen Beispielaufruf dieser benutzerdefinierten Methode. Darin wird ein JSON-Patch-Dokument mit den Informationen zur Aktualisierung einer Eigenschaft übergeben.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Löschen von Beziehungen

Der erste Parameter gibt den Quellzwilling an (der Zwilling, von dem die Beziehung ausgeht). Der andere Parameter ist die Beziehungs-ID. Sie benötigen sowohl die Zwillings-ID als auch die Beziehungs-ID, da Beziehungs-IDs nur innerhalb des Bereichs eines Zwillings eindeutig sind.

Hier sehen Sie Beispielcode, der die Verwendung dieser Methode zeigt. Für dieses Beispiel wird der SDK-Aufruf (hervorgehoben) innerhalb einer benutzerdefinierten Methode verwendet, die im Kontext eines größeren Programms angezeigt werden kann.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Sie können jetzt diese benutzerdefinierte Methode aufrufen, um eine Beziehung wie folgt zu löschen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="create-graph-from-a-csv-file"></a>Erstellen eines Graphen aus einer CSV-Datei

In praktischen Anwendungsfällen werden Zwillingshierarchien oft aus Daten erstellt, die in einer anderen Datenbank oder vielleicht in einem Arbeitsblatt oder einer CSV-Datei gespeichert sind. In diesem Abschnitt wird gezeigt, wie Sie Daten aus einer CSV-Datei lesen und einen Zwillingsgraphen daraus erstellen.

Betrachten Sie die folgende Datentabelle, in der eine Reihe von digitalen Zwillingen und Beziehungen beschrieben werden.

|  Modell-ID    | Zwillings-ID (muss eindeutig sein) | Beziehungsname  | Zielzwillings-ID  | Initialisierungsdaten des Zwillings |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | Floor1 (Etage1) | contains | Room1 | |
| dtmi:example:Floor;1    | Floor0 (Etage0) | contains | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature": 80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature": 70} |

Eine Möglichkeit zum Übernehmen dieser Daten in Azure Digital Twins besteht darin, zunächst die Tabelle in eine CSV-Datei zu konvertieren. Nach dem Konvertieren der Tabelle können Sie Code schreiben, um die Datei in Befehle zum Erstellen von Zwillingen und Beziehungen zu übersetzen. Das folgende Codebeispiel veranschaulicht das Lesen der Daten aus der CSV-Datei und das Erstellen eines Zwillingsgraphen in Azure Digital Twins.

Im folgenden Code weist die CSV-Datei den Namen *data.csv* auf, und es gibt einen Platzhalter, der für den **Hostnamen** Ihrer Azure Digital Twins-Instanz steht. Das Beispiel nutzt auch mehrere Pakete, die Sie dem Projekt zur Unterstützung dieses Prozesses hinzufügen können.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="runnable-twin-graph-sample"></a>Ausführbares Zwillingsgraphenbeispiel

Der folgende ausführbare Codeausschnitt verwendet die Beziehungsvorgänge aus diesem Artikel, um einen Zwillingsgraphen aus digitalen Zwillingen und Beziehungen zu erstellen.

### <a name="set-up-sample-project-files"></a>Einrichten von Beispielprojektdateien

Der Codeausschnitt verwendet zwei Beispielmodelldefinitionen: [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Room.json) und [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Floor.json). Um **die Modelldateien herunterladen** und in Ihrem Code verwenden zu können, verwenden Sie diese Links für die direkte Navigation zu den Dateien in GitHub. Klicken Sie dann mit der rechten Maustaste auf eine beliebige Stelle auf dem Bildschirm, wählen Sie im Kontextmenü Ihres Browsers **Speichern unter** aus, und verwenden Sie das Fenster „Speichern unter“, um die Dateien als **Room.json** und **Floor.json** zu speichern.

Erstellen Sie als Nächstes **ein neues Konsolen-App-Projekt** in Visual Studio oder einem Editor Ihrer Wahl.

Kopieren Sie dann den **folgenden Code** des ausführbaren Beispiels in Ihr Projekt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

### <a name="configure-project"></a>Projekt konfigurieren

Führen Sie als Nächstes die folgenden Schritte aus, um den Projektcode zu konfigurieren:
1. Fügen Sie Ihrem Projekt die Dateien **Room.json** und **Floor.json** hinzu, die Sie zuvor heruntergeladen haben, und ersetzen Sie die Platzhalter `<path-to>` im Code, um dem Programm mitzuteilen, wo sie zu finden sind.
1. Ersetzen Sie den Platzhalter `<your-instance-hostname>` durch den Hostnamen Ihrer Azure Digital Twins-Instanz.
1. Als Nächstes fügen Sie Ihrem Projekt zwei Abhängigkeiten hinzu, die erforderlich sind, um Azure Digital Twins verwenden zu können. Bei der ersten handelt es sich um das Paket für das [Azure Digital Twins SDK für .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), und die zweite stellt Tools zur Unterstützung der Authentifizierung bei Azure bereit.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Sie müssen auch lokale Anmeldeinformationen einrichten, wenn Sie das Beispiel direkt ausführen möchten. Im nächsten Abschnitt wird dieser Prozess erläutert.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Ausführen des Beispiels

Nachdem Sie das Setup abgeschlossen haben, können Sie das Beispielcodeprojekt ausführen.

Dies ist die Konsolenausgabe des Programms: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Screenshot der Konsolenausgabe, die die Zwillingsdetails mit den ein- und ausgehenden Beziehungen der Zwillinge anzeigt." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Der Zwillingsgraph ist ein Konzept zur Erstellung von Beziehungen zwischen Zwillingen. Wenn Sie sich die visuelle Darstellung des Zwillingsgraphen ansehen möchten, lesen Sie den Abschnitt [Visualisierung](how-to-manage-graph.md#visualization) dieses Artikels. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Abfrage eines Azure Digital Twins-Zwillingsgraphen:
* [Abfragesprache](concepts-query-language.md)
* [Abfragen des Zwillingsgraphen](how-to-query-graph.md)