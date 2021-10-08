---
title: Verwalten digitaler Zwillinge
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie einzelne Zwillinge und Beziehungen abrufen, aktualisieren und löschen.
author: baanders
ms.author: baanders
ms.date: 9/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4be8ef1085d6a940e7f2d95f43a75d1b4e7c11f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611687"
---
# <a name="manage-digital-twins"></a>Verwalten digitaler Zwillinge

Entitäten in Ihrer Umgebung werden durch [digital Zwillinge](concepts-twins-graph.md) dargestellt. Die Verwaltung digitaler Zwillinge kann das Erstellen, Ändern und Löschen umfassen.

In diesem Artikel wird die Verwaltung digitaler Zwillinge beschrieben. Informationen zum Arbeiten mit Beziehungen und dem [Zwillingsgraphen](concepts-twins-graph.md) insgesamt finden Sie unter [Verwalten des Zwillingsgraphen und der Beziehungen](how-to-manage-graph.md).

> [!TIP]
> Alle SDK-Funktionen sind in synchronen und asynchronen Versionen enthalten.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit Beispielmodellen und -zwillingen." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

## <a name="create-a-digital-twin"></a>Erstellen eines digitalen Zwillings

Wenn Sie einen Zwilling erstellen möchten, verwenden Sie die `CreateOrReplaceDigitalTwinAsync()`-Methode auf dem Serviceclient wie folgt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

Zum Erstellen eines digitalen Zwillings müssen Sie Folgendes angeben:
* Ein ID-Wert, den Sie dem digitalen Zwilling zuweisen möchten (Sie definieren diese ID, wenn der Zwilling erstellt wird)
* Das zu verwendende [Modell](concepts-models.md)
* Jede gewünschte Initialisierung von Zwillingsdaten, einschließlich...
    - Eigenschaften (optional zum Initialisieren): Sie können bei Bedarf Anfangswerte für Eigenschaften des digitalen Zwillings festlegen. Eigenschaften werden als optional behandelt und können später festgelegt werden. Beachten Sie aber, dass **sie erst als Teil eines Zwillings angezeigt werden, nachdem sie festgelegt wurden**.
    - Telemetrie (zu initialisieren empfohlen): Sie können auch Anfangswerte für Telemetriefelder auf dem Zwilling festlegen. Obwohl die Initialisierung von Telemetriedaten nicht erforderlich ist, werden auch Telemetriefelder erst dann als Teil eines Zwillings angezeigt, wenn sie festgelegt wurden. Das bedeutet, dass **Sie Telemetriewerte für einen Zwilling nur dann bearbeiten können, wenn diese erst initialisiert wurden**.
    - Komponenten (für die Initialisierung erforderlich, wenn sie auf dem Zwilling vorhanden sind): Wenn Ihr Zwilling [Komponenten](concepts-models.md#elements-of-a-model) enthält, müssen diese beim Erstellen des Zwillings initialisiert werden. Es kann sich um leere Objekte handeln, aber die Komponenten selbst müssen vorhanden sein.
    
Die Modell- und alle ursprünglichen Eigenschaftswerte werden über den `initData`-Parameter bereitgestellt. Dabei handelt es sich um eine JSON-Zeichenfolge, die die relevanten Daten enthält. Weitere Informationen zum Strukturieren dieses Objekts finden Sie im nächsten Abschnitt.

> [!TIP]
> Nachdem Sie einen Zwilling erstellt oder aktualisiert haben, kann es bis zu 10 Sekunden dauern, bis sich die Änderungen in [Abfragen](how-to-query-graph.md) widerspiegeln. Bei der `GetDigitalTwin`-API (siehe [weiter unten in diesem Artikel](#get-data-for-a-digital-twin)) tritt diese Verzögerung nicht auf. Wenn Sie eine sofortige Antwort benötigen, verwenden Sie daher den API-Befehl anstelle von Abfragen, um die neu erstellten Zwillinge anzuzeigen. 

### <a name="initialize-model-and-properties"></a>Initialisieren des Modells und der Eigenschaften

Sie können die Eigenschaften eines Zwillings zum Zeitpunkt der Erstellung des Zwillings initialisieren. 

Die Zwillingserstellungs-API akzeptiert ein Objekt, das in eine gültige JSON-Beschreibung der Zwillingseigenschaften serialisiert wird. Unter [Digitale Zwillinge und der Zwillingsgraph](concepts-twins-graph.md) finden Sie eine Beschreibung des JSON-Formats für einen Zwilling. 

Zunächst können Sie ein Datenobjekt erstellen, das den Zwilling und seine Eigenschaftsdaten darstellt. Sie können ein Parameterobjekt entweder manuell oder mithilfe einer bereitgestellten Hilfsklasse erstellen. Im Folgenden finden Sie Beispiele für beides.

#### <a name="create-twins-using-manually-created-data"></a>Erstellen von Zwillingen mithilfe manuell erstellter Daten

Sie können die Eigenschaften eines Zwillings ohne benutzerdefinierte Hilfsklassen in `Dictionary<string, object>` darstellen, wobei `string` der Name der Eigenschaft ist und `object` ein Objekt, das die Eigenschaft und ihren Wert darstellt.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Erstellen von Zwillingen mithilfe der Hilfsklasse

Die Hilfsklasse `BasicDigitalTwin` ermöglicht es Ihnen, Eigenschaftsfelder in einem Zwillingsobjekt direkt zu speichern. Sie sollten dennoch die Liste der Eigenschaften mit `Dictionary<string, object>` erstellen, was dann dem Zwillingsobjekt direkt als `CustomProperties` hinzugefügt werden kann.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin`-Objekte verfügen über ein `Id`-Feld. Sie können dieses Feld leer lassen. Wenn Sie einen ID-Wert hinzufügen, muss dieser jedoch dem an den `CreateOrReplaceDigitalTwinAsync()`-Aufruf übergebenen ID-Parameter entsprechen. Zum Beispiel:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Abrufen von Daten für einen digitalen Zwilling

Sie können auf die Details jedes digitalen Zwillings zugreifen, indem Sie die Methode `GetDigitalTwin()` wie folgt aufrufen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Dieser Aufruf gibt Zwillingsdaten als einen stark typisierten Objekttyp (z. B. `BasicDigitalTwin`) zurück. `BasicDigitalTwin` ist eine Serialisierungshilfsklasse, die im SDK enthalten ist und die die zentralen Zwillingsmetadaten und -eigenschaften in einem vorab analysierten Format zurückgibt. Sie können jederzeit Zwillingsdaten deserialisieren, indem Sie die JSON-Bibliothek Ihrer Wahl wie `System.Text.Json` oder `Newtonsoft.Json` verwenden. Den grundlegenden Zugriff auf einen Zwilling können jedoch die Hilfsklassen komfortabler machen.

> [!NOTE]
> Von `BasicDigitalTwin` werden Attribute vom Typ `System.Text.Json` verwendet. Um `BasicDigitalTwin` mit Ihrer Instanz von [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true) verwenden zu können, müssen Sie entweder den Client mit dem Standardkonstruktor initialisieren oder den Serialisierer [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true), wenn Sie die Serialisierungsoption anpassen möchten.

Die Hilfsklasse `BasicDigitalTwin` bietet Ihnen auch Zugriff auf Eigenschaften, die auf dem Zwilling definiert sind, über ein `Dictionary<string, object>`. Sie können Folgendes verwenden, um die Eigenschaften eines Zwillings aufzulisten:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Beim Abrufen eines Zwillings mit der Methode `GetDigitalTwin()` werden nur Eigenschaften zurückgegeben, die mindestens einmal festgelegt wurden.

>[!TIP]
>Der `displayName` für einen Zwilling ist ein Teil seiner Modellmetadaten und wird daher nicht angezeigt, wenn Daten für die Zwillingsinstanz abgerufen werden. Um diesen Wert zu sehen, können Sie [ihn aus dem Modell abrufen](how-to-manage-model.md#retrieve-models).

Informationen zum Abrufen mehrerer Zwillinge mithilfe eines einzelnen API-Aufrufs finden Sie in den Beispielen zur Abfrage-API unter [Abfragen des Zwillingsgraphen](how-to-query-graph.md).

Beachten Sie das folgende Modell (geschrieben in [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)), das einen Zwilling vom Typ Moon definiert:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

Das Ergebnis des Aufrufs von `object result = await client.GetDigitalTwinAsync("my-moon");` für einen Zwilling vom Typ Moon könnte wie folgt aussehen:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Die definierten Eigenschaften des digitalen Zwillings werden als Eigenschaften der obersten Ebene für den digitalen Zwilling zurückgegeben. Metadaten oder Systeminformationen, die nicht Teil der DTDL-Definition sind, werden mit einem `$`-Präfix zurückgegeben. Metadateneigenschaften umfassen die folgenden Werte:
* `$dtId`: ID des digitalen Zwillings in dieser Azure Digital Twins-Instanz
* `$etag`: ein vom Webserver zugewiesenes Standard-HTTP-Feld. Dieser Wert wird bei jeder Aktualisierung des Zwillings geändert. Er kann damit hilfreich sein, um zu ermitteln, ob die Daten des Zwillings seit einer früheren Überprüfung auf dem Server aktualisiert wurden. Sie können `If-Match` verwenden, um Aktualisierungen und Löschungen durchführen, die nur dann abgeschlossen werden, wenn der Etag mit dem bereitgestellten Etag übereinstimmt. Weitere Informationen zu diesen Vorgängen finden Sie in der Dokumentation zu [DigitalTwins Update](/rest/api/digital-twins/dataplane/twins/digitaltwins_update) und [DigitalTwins Delete](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete).
* `$metadata`: verschiedene andere Eigenschaften, einschließlich:
  - Der DTMI des Modells für den digitalen Zwilling
  - Der Synchronisierungsstatus für jede schreibbare Eigenschaft. Dies ist besonders hilfreich für Geräte, bei denen es möglich ist, dass der Dienst und das Gerät abweichende Status aufweisen (z. B. wenn ein Gerät offline ist). Derzeit gilt diese Eigenschaft nur für physische Geräte, die mit IoT Hub verbunden sind. Mit den Daten im Metadatenabschnitt erfahren Sie den vollständigen Status einer Eigenschaft sowie die Zeitstempel der letzten Änderung. Weitere Informationen zum Synchronisierungsstatus erhalten Sie in [diesem IoT Hub-Tutorial](../iot-hub/tutorial-device-twins.md) zum Synchronisieren des Gerätestatus.
  - Dienstspezifische Metadaten, wie z. B. aus IoT Hub oder Azure Digital Twins 

Weitere Informationen zu den Serialisierungshilfsklassen wie `BasicDigitalTwin` finden Sie unter [Azure Digital Twins-APIs und SDKs](concepts-apis-sdks.md#serialization-helpers).

## <a name="view-all-digital-twins"></a>Anzeigen aller digitalen Zwillinge

Verwenden Sie zum Anzeigen aller digitalen Zwillinge in Ihrer Instanz eine [Abfrage](how-to-query-graph.md). Sie können eine Abfrage mit den [Abfrage-APIs](/rest/api/digital-twins/dataplane/query) oder den [CLI-Befehlen](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) ausführen.

Im Folgenden finden Sie den Text der grundlegenden Abfrage, die eine Liste aller digitalen Zwillinge in der Instanz zurückgibt:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Aktualisieren eines digitalen Zwillings

Um die Eigenschaften eines digitalen Zwillings zu aktualisieren, schreiben Sie die Informationen, die Sie ersetzen möchten, im [JSON Patch](http://jsonpatch.com/)-Format. So können Sie mehrere Eigenschaften gleichzeitig ersetzen. Anschließend übergeben Sie das JSON Patch-Dokument an eine `UpdateDigitalTwin()`-Methode:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Ein Patchaufruf kann beliebig viele Eigenschaften auf einem einzelnen Zwilling aktualisieren (sogar alle). Wenn Sie Eigenschaften für mehrere Zwillinge aktualisieren müssen, benötigen Sie für jeden Zwilling einen separaten Updateaufruf.

> [!TIP]
> Nachdem Sie einen Zwilling erstellt oder aktualisiert haben, kann es bis zu 10 Sekunden dauern, bis sich die Änderungen in [Abfragen](how-to-query-graph.md) widerspiegeln. Bei der `GetDigitalTwin`-API (siehe [weiter oben in diesem Artikel](#get-data-for-a-digital-twin)) tritt diese Verzögerung nicht auf. Verwenden Sie daher den API-Befehl anstelle von Abfragen, um die neu aktualisierten Zwillinge anzuzeigen, wenn Sie eine sofortige Antwort benötigen. 

Im Folgenden finden Sie ein Beispiel für JSON Patch-Code. Dieses Dokument ersetzt die Eigenschaftswerte *mass* und *radius* des digitalen Zwillings, auf den es angewendet wird.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

>[!NOTE]
> Dieses Beispiel zeigt den JSON Patch-Vorgang `replace`, der den Wert einer vorhandenen Eigenschaft ersetzt. Eine vollständige Liste der JSON Patch-Vorgänge, die verwendet werden können, einschließlich `add` und `remove`, finden Sie im Abschnitt [Vorgänge für JSON Patch](http://jsonpatch.com/#operations). 

Wenn Sie einen Zwilling aus einem Codeprojekt mit dem .NET SDK aktualisieren, können Sie JSON-Patches mit [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument?view=azure-dotnet&preserve-view=true) aus dem Azure .NET SDK erstellen. Beispiel:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-sub-properties-in-digital-twin-components"></a>Aktualisieren von Untereigenschaften in Komponenten digitaler Zwillinge

Denken Sie daran, dass ein Modell Komponenten enthalten kann, sodass es aus anderen Modellen besteht. 

Zum Patchen von Eigenschaften in den Komponenten eines digitalen Zwillings können Sie die Pfadsyntax im JSON-Patch verwenden:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-sub-properties-in-object-type-properties"></a>Aktualisieren von Untereigenschaften in Objekttypeigenschaften

Modelle können Eigenschaften mit einem Objekttyp enthalten. Diese Objekte verfügen möglicherweise über eigene Eigenschaften, und Sie möchten möglicherweise eine dieser Untereigenschaften aktualisieren, die zur Objekttypeigenschaft gehört. Dieser Prozess ähnelt dem Prozess zum [Aktualisieren von Untereigenschaften in Komponenten](#update-sub-properties-in-digital-twin-components), erfordert jedoch möglicherweise einige zusätzliche Schritte. 

Stellen Sie sich ein Modell mit der Objekttypeigenschaft `ObjectProperty` vor. `ObjectProperty` verfügt über eine Zeichenfolgeneigenschaft namens `StringSubProperty`.

Wenn ein Zwilling mit diesem Modell erstellt wird, ist es nicht erforderlich, die `ObjectProperty` zu diesem Zeitpunkt zu instanziieren. Wenn die Objekteigenschaft während der Erstellung des Zwillings nicht instanziiert wird, wird kein Standardpfad für den Zugriff auf `ObjectProperty` und die zugehörige `StringSubProperty` für einen Patchvorgang erstellt. Sie müssen den Pfad zu `ObjectProperty` selbst hinzufügen, bevor Sie die Eigenschaften aktualisieren können.

Dies kann mit einem JSON-Patchvorgang vom Typ `add` wie folgt geschehen:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-object-sub-property-1.json":::

>[!NOTE]
> Wenn `ObjectProperty` über mehrere Eigenschaften verfügt, sollten Sie alle Eigenschaften in das `value`-Feld dieses Vorgangs einbeziehen, auch wenn Sie nur Eigenschaft eine aktualisieren:
> ```json
>... "value": {"StringSubProperty":"<string-value>", "Property2":"<property2-value>", ...}
>```


Nachdem dies einmal erfolgt ist, ist ein Pfad zu `StringSubProperty` vorhanden, und sie kann von nun an direkt mit einem typischen `replace`-Vorgang aktualisiert werden:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-object-sub-property-2.json":::

Obwohl der erste Schritt in Fällen, in denen `ObjectProperty` beim Erstellen des Zwillings instanziiert wurde, nicht notwendig ist, wird empfohlen, ihn jedes Mal zu verwenden, wenn Sie eine Untereigenschaft zum ersten Mal aktualisieren, da Sie nicht immer sicher wissen, ob die Objekteigenschaft ursprünglich instanziiert wurde oder nicht.

### <a name="update-a-digital-twins-model"></a>Aktualisieren eines Modells eines digitalen Zwillings

Die `UpdateDigitalTwin()`-Funktion kann auch verwendet werden, um einen digitalen Zwilling zu einem anderen Modell zu migrieren. 

Sehen Sie sich beispielsweise das folgende JSON Patch-Dokument an, das das `$model`-Metadatenfeld des digitalen Zwillings ersetzt:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Dieser Vorgang ist nur erfolgreich, wenn der vom Patch geänderte digitale Zwilling dem neuen Modell entspricht. 

Betrachten Sie das folgenden Beispiel:
1. Stellen Sie sich einen digitalen Zwilling mit dem Modell foo_old vor. foo_old definiert die erforderliche *mass*-Eigenschaft.
2. Das neue Modell foo_new definiert die Eigenschaften „mass“ und fügt die neue erforderliche Eigenschaft *temperature* hinzu.
3. Nach dem Patch muss der digitale Zwilling die beiden Eigenschaften „mass“ und „temperature“ aufweisen. 

Der Patch für diese Situation muss sowohl das Modell als auch die Eigenschaft „temperature“ des Zwillings wie folgt aktualisieren:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Verarbeitung von in Konflikt stehenden Updateaufrufen

Azure Digital Twins stellt sicher, dass alle eingehenden Anforderungen nacheinander verarbeitet werden. Dies bedeutet, dass Sie selbst dann, wenn mehrere Funktionen versuchen, dieselbe Eigenschaft für einen Zwilling gleichzeitig zu aktualisieren, **keinen** expliziten Sperrcode schreiben müssen, um den Konflikt zu beheben.

Dieses Verhalten erfolgt für jeden Zwilling einzeln. 

Stellen Sie sich beispielsweise ein Szenario vor, in dem die folgenden drei Aufrufe gleichzeitig eingehen: 
*   Schreiben von Eigenschaft A auf Zwilling1
*   Schreiben von Eigenschaft B auf Zwilling1
*   Schreiben von Eigenschaft A auf Zwilling2

Die beiden Aufrufe, die Zwilling1 ändern, werden nacheinander ausgeführt, und für jede Änderung werden Änderungsnachrichten generiert. Der Aufruf zum Ändern von Zwilling2 kann ohne Konflikt zeitgleich ausgeführt werden, sobald er eingeht.

## <a name="delete-a-digital-twin"></a>Löschen eines digitalen Zwillings

Sie können Zwillinge mithilfe der `DeleteDigitalTwin()`-Methode löschen. Sie können einen Zwilling jedoch nur löschen, wenn er keine weiteren Beziehungen aufweist. Daher löschen Sie zuerst die ein- und ausgehenden Beziehungen des Zwillings.

Hier folgt ein Beispiel für den Code zum Löschen von Zwillingen und ihren Beziehungen. Der `DeleteDigitalTwin`-SDK-Aufruf ist hervorgehoben, um zu verdeutlichen, inwiefern er Teil des breiteren Beispielkontexts ist.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Löschen aller digitalen Zwillinge

Ein Beispiel für das gleichzeitige Löschen aller Zwillinge finden Sie in der Beispiel-App unter [Erkunden von Azure Digital Twins mit einer Beispielclient-App](tutorial-command-line-app.md). In der Datei *CommandLoop.cs* wird dies in einer `CommandDeleteAllTwins()`-Funktion durchgeführt.

## <a name="runnable-digital-twin-code-sample"></a>Ausführbares Codebeispiel für digitale Zwillinge

Sie können das folgende ausführbare Codebeispiel verwenden, um einen Zwilling zu erstellen, seine Details zu aktualisieren und den Zwilling zu löschen. 

### <a name="set-up-sample-project-files"></a>Einrichten von Beispielprojektdateien

Der Codeausschnitt verwendet eine Beispielmodelldefinition: [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Room.json). Um die **Modelldatei herunterzuladen**, damit Sie sie in Ihrem Code verwenden können, verwenden Sie diesen Link, um direkt zur Datei in GitHub zu navigieren. Klicken Sie dann mit der rechten Maustaste auf eine beliebige Stelle auf dem Bildschirm, wählen Sie im Kontextmenü Ihres Browsers **Speichern unter** aus, und verwenden Sie das Fenster „Speichern unter“, um die Datei als **Room.json** zu speichern.

Erstellen Sie als dann **ein neues Konsolen-App-Projekt** in Visual Studio oder einem Editor Ihrer Wahl.

Kopieren Sie dann den **folgenden Code** des ausführbaren Beispiels in Ihr Projekt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

### <a name="configure-project"></a>Projekt konfigurieren

Führen Sie als dann die folgenden Schritte aus, um den Projektcode zu konfigurieren:
1. Fügen Sie Ihrem Projekt die Datei **Room.json** hinzu, die Sie zuvor heruntergeladen haben, und ersetzen Sie den Platzhalter `<path-to>` im Code, um dem Programm den Pfad mitzuteilen.
2. Ersetzen Sie den Platzhalter `<your-instance-hostname>` durch den Hostnamen Ihrer Azure Digital Twins-Instanz.
3. Als Nächstes fügen Sie Ihrem Projekt zwei Abhängigkeiten hinzu, die erforderlich sind, um Azure Digital Twins verwenden zu können. Bei der ersten handelt es sich um das Paket für das [Azure Digital Twins SDK für .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), und die zweite stellt Tools zur Unterstützung der Authentifizierung bei Azure bereit.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Sie müssen auch lokale Anmeldeinformationen einrichten, wenn Sie das Beispiel direkt ausführen möchten. Im nächsten Abschnitt werden diese Schritte erläutert.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Ausführen des Beispiels

Nachdem Sie das Setup abgeschlossen haben, können Sie das Beispielcodeprojekt ausführen.

Hier ist die Konsolenausgabe des obigen Programms: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Screenshot: Konsolenausgabe, die zeigt, dass der Zwilling erstellt, aktualisiert und gelöscht wurde." lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Beziehungen zwischen Ihren digitalen Zwillingen erstellen und verwalten können:
* [Verwalten des Zwillingsgraphen und der Beziehungen](how-to-manage-graph.md)