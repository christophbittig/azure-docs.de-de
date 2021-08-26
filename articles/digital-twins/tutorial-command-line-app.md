---
title: 'Tutorial: Erstellen eines Graphen in Azure Digital Twins (Client-App)'
titleSuffix: Azure Digital Twins
description: Tutorial zum Erstellen eines Azure Digital Twins-Szenarios unter Verwendung einer Beispielbefehlszeilenanwendung
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: e795b8d34b46fc3df0e31e12a1de0bccdab74e6a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252706"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Tutorial: Erstellen eines Azure Digital Twins-Graphen unter Verwendung Beispielclient-App

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

In diesem Tutorial wird in Azure Digital Twins ein Graph mit Modellen, Zwillingen und Beziehungen erstellt. Als Tool wird in diesem Tutorial eine **exemplarische Befehlszeilenclientanwendung** für die Interaktion mit einer Azure Digital Twins-Instanz verwendet. Die Client-App ähnelt der App die in [Kodieren einer Client-App](tutorial-code.md)geschrieben wurde.

Sie können dieses Beispiel verwenden, um grundlegende Azure Digital Twins-Aktionen auszuführen, etwa Hochladen von Modellen, Erstellen und Ändern von Zwillingen und Erstellen von Beziehungen. Sie können sich auch den [Code des Beispiels](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) ansehen, um mehr über die Azure Digital Twins-APIs zu erfahren, und die Implementierung eigener Befehle üben, indem Sie das Beispielprojekt wunschgemäß anpassen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Modellieren einer Umgebung
> * Erstellen digitaler Zwillinge
> * Hinzufügen von Beziehungen zur Erstellung eines Graphen
> * Abfragen des Graphen zur Beantwortung von Fragen

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Ausführen des Beispielprojekts

Nach Einrichtung der App und der Authentifizierung kann das Projekt über die folgende Schaltfläche auf der Symbolleiste ausgeführt werden:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Screenshot: Visual Studio-Startschaltfläche (Projekt „SampleClientApp“)" lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Ein Konsolenfenster wird geöffnet, führt die Authentifizierung durch und wartet auf einen Befehl. 

Hier sehen Sie einen Screenshot der Projektkonsole:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Screenshot: Willkommensnachricht der Befehlszeilen-App" lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Geben Sie zum Abrufen einer Liste mit allen möglichen Befehlen, die Sie mit diesem Projekt verwenden können, `help` in der Projektkonsole ein, und drücken Sie die EINGABETASTE.

Nachdem Sie sich vergewissert haben, dass die App erfolgreich ausgeführt wird, schließen Sie das Konsolenfenster, um die Ausführung der App vorerst zu beenden. Sie werden die App später in diesem Artikel erneut ausführen.

## <a name="model-a-physical-environment-with-dtdl"></a>Modellieren einer physischen Umgebung mit DTDL

Nachdem die Azure Digital Twins-Instanz und die Beispiel-App eingerichtet sind, können Sie mit der Erstellung eines Szenariographen beginnen. 

Beim Erstellen einer Azure Digital Twins-Lösung werden zunächst [Zwillingsmodelle](concepts-models.md) für Ihre Umgebung definiert. 

Modelle ähneln Klassen in objektorientierten Programmiersprachen. Sie stellen benutzerdefinierte Vorlagen für [digitale Zwillinge](concepts-twins-graph.md) bereit, die später eingesetzt und instanziiert werden. Sie sind in einer JSON-ähnlichen Sprache namens **Digital Twins Definition Language (DTDL)** geschrieben und können die *Eigenschaften*, *Telemetriedaten*, *Beziehungen* und *Komponenten* eines Zwillings festlegen.

> [!NOTE]
> DTDL ermöglicht außerdem die Definition von *Befehlen* für digitale Zwillinge. Allerdings werden Befehle im Azure Digital Twins-Dienst derzeit nicht unterstützt.

Navigieren Sie im Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, im Bereich *Projektmappen-Explorer* zum Ordner *AdtSampleApp\SampleClientApp\Models*. Dieser Ordner enthält Beispielmodelle.

Wählen Sie die Datei *Room.json* aus, um sie im Bearbeitungsfenster zu öffnen, und nehmen Sie die folgenden Änderungen vor:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Hochladen von Modellen in Azure Digital Twins

Nach dem Entwerfen von Modellen müssen Sie diese in Ihre Azure Digital Twins-Instanz hochladen. Dadurch wird die Azure Digital Twins-Dienstinstanz mit Ihrem eigenen benutzerdefinierten Domänenvokabular konfiguriert. Nach dem Hochladen der Modelle können Sie Zwillingsinstanzen erstellen, die diese verwenden.

1. Nachdem Sie die Room.js Datei im vorherigen Abschnitt bearbeitet haben, starten Sie jetzt die Ausführung der Konsolen-App erneut.

1. Führen Sie im Projektkonsolenfenster den folgenden Befehl aus, um das aktualisierte Room-Modell sowie ein Floor-Modell hochzuladen, das Sie auch im nächsten Abschnitt verwenden, um unterschiedliche Arten von Zwillingen zu erstellen.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    In der Ausgabe sollte die erfolgreiche Erstellung der Modelle angegeben sein.

1. Vergewissern Sie sich, dass die Modelle erstellt wurden, indem Sie den Befehl `GetModels true` ausführen. Dadurch werden alle hochgeladenen Modelle aus der Azure Digital Twins-Instanz abgefragt und ausführliche Informationen ausgegeben. Suchen Sie in den Ergebnissen nach dem bearbeiteten Room-Modell:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Screenshot: Ergebnis von „GetModels“ mit dem aktualisierten Raummodell" lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Errors

Die Beispielanwendung verarbeitet auch Fehler des Diensts. 

Führen Sie den Befehl `CreateModels` erneut aus, um eins des soeben hochgeladenen Modelle ein zweites Mal hochzuladen:

```cmd/sh
CreateModels Room
```

Da Modelle nicht überschrieben werden können, wird jetzt ein Dienstfehler zurückgegeben.
Ausführliche Informationen zum Löschen von vorhandenen Modellen finden Sie unter [Verwalten von DTDL-Modellen](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Erstellen digitaler Zwillinge

Sie haben einige Modelle in Ihre Azure Digital Twins-Instanz hochgeladen und können nun basierend auf den Modelldefinitionen [digitale Zwillinge](concepts-twins-graph.md) erstellen. Digitale Zwillinge stellen die Entitäten in Ihrer Geschäftsumgebung dar, etwa Sensoren in einer Farm, Räume in einem Gebäude oder Beleuchtung in einem Fahrzeug. 

Zum Erstellen eines digitalen Zwillings verwenden Sie den Befehl `CreateDigitalTwin`. Sie müssen auf das Modell verweisen, auf dem der Zwilling basiert, und können optional Anfangswerte für Eigenschaften im Modell definieren. Sie müssen in dieser Phase keine Beziehungsinformationen übergeben.

1. Führen Sie diesen Code in der ausgeführten Projektkonsole aus, um mehrere Zwillinge basierend auf dem zuvor aktualisierten Room-Modell und dem Floor-Modell zu erstellen. Denken Sie daran, dass Room drei Eigenschaften hat, sodass Sie Argumente mit den Anfangswerten für diese angeben können. (Die Initialisierung von Eigenschaftswerten ist grundsätzlich optional, für dieses Tutorial werden sie allerdings benötigt.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    In der Ausgabe dieser Befehle sollte die erfolgreiche Erstellung der Zwillinge angegeben sein. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Screenshot: Auszug aus dem Ergebnis der Befehle vom Typ „CreateDigitalTwin“ mit „floor0“, „floor1“, „room0“ und „room1“" lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Führen Sie den Befehl `Query` aus, um sich zu vergewissern, dass die Zwillinge erstellt wurden. Mit diesem Befehl werden alle digitalen Zwillinge der Azure Digital Twins-Instanz abgefragt. Suchen Sie in den Ergebnissen nach den Zwillingen room0, room1, floor0 und floor1.

### <a name="modify-a-digital-twin"></a>Ändern eines digitalen Zwillings

Sie können auch die Eigenschaften eines von Ihnen erstellten Zwillings ändern. 

> [!NOTE]
> Die zugrunde liegende REST-API verwendet das Format [JSON Patch](http://jsonpatch.com/), um Aktualisierungen für einen Zwilling zu definieren. Die Befehlszeilen-App verwendet dieses Format ebenfalls, um besser widerzuspiegeln, was von den zugrunde liegenden APIs erwartet wird.

1. Führen Sie den folgenden Befehl aus, um den RoomName-Wert für „room0“ von „Room0“ in „PresidentialSuite“ zu ändern:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    In der Ausgabe sollte die erfolgreiche Aktualisierung des Zwillings angegeben sein.

1. Sie können den folgenden Befehl ausführen, um die Informationen zu *room0* anzuzeigen und sich zu vergewissern, dass die Aktualisierung erfolgreich war:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    In der Ausgabe sollte der aktualisierte Name angezeigt werden.


## <a name="create-a-graph-by-adding-relationships"></a>Erstellen eines Graphen durch Hinzufügen von Beziehungen

Im nächsten Schritt können Sie einige **Beziehungen** zwischen diesen Zwillingen erstellen, um Sie in einem [Zwillingsgraphen](concepts-twins-graph.md) zu verbinden. Zwillingsgraphen werden verwendet, um eine gesamte Umgebung darzustellen. 

Die Arten von Beziehungen, die Sie zwischen Zwillingen erstellen können, werden in den zuvor hochgeladenen [Modellen](#model-a-physical-environment-with-dtdl) definiert. In der [Modelldefinition für „Floor“](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) ist angegeben, dass Etagen eine Beziehung vom Typ *contains* (enthält) haben können. Dies ermöglicht die Erstellung einer Beziehung vom Typ *enthält* zwischen jedem Etagenzwilling (Floor) und dem entsprechenden enthaltenen Raum.

Verwenden Sie zum Hinzufügen einer Beziehung den Befehl `CreateRelationship`. Geben Sie den Zwilling, von dem die Beziehung ausgeht, die Art der Beziehung sowie den Zwilling an, mit dem die Beziehung hergestellt wird. Geben Sie abschließend eine eindeutige ID für die Beziehung an.

1. Führen Sie den folgenden Code aus, um eine Beziehung vom Typ „contains“ von jedem der zuvor erstellten Floor-Zwillinge zu einem entsprechenden Room-Zwilling hinzuzufügen. Die Beziehungen heißen relationship0 und relationship1.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >Da die Beziehung vom Typ *contains* (enthält) im Modell [Floor (Etage)](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) mit zwei Zeichenfolgeneigenschaften (`ownershipUser` und `ownershipDepartment`) definiert wurde, können Sie beim Erstellen der Beziehungen auch Argumente mit den Anfangswerten für diese Eigenschaften angeben.
    > Hier sehen Sie eine alternative Version des obigen Befehls zum Erstellen von relationship0, bei der auch Anfangswerte für diese Eigenschaften angegeben werden:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    Die Ausgabe dieser Befehle bestätigt, dass die Beziehungen erfolgreich erstellt wurden:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Screenshot: Auszug aus dem Ergebnis der Befehle vom Typ „CreateRelationship“ mit „relationship0“ und „relationship1“" lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Sie können die Beziehungen mithilfe eines der folgenden Befehle zum Abfragen der Beziehungen in der Azure Digital Twins-Instanz überprüfen.
    * So zeigen Sie alle Beziehungen an, die von den einzelnen Etagen ausgehen (Anzeige der Beziehungen von einer Seite):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * So zeigen Sie alle Beziehungen an, die bei jedem Raum ankommen (Anzeige der Beziehungen von der anderen Seite):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * So können Sie sich diese Beziehungen einzeln (nach ID) ansehen:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

Die Zwillinge und Beziehungen, die Sie in diesem Tutorial eingerichtet haben, bilden den folgenden konzeptionellen Graphen:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagramm mit einem Konzeptgraphen. „floor0“ ist über „relationship0“ mit „room0“ verbunden und „floor1“ über „relationship1“ mit „room1“." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Abfragen des Zwillingsgraphen zum Beantworten von Umgebungsfragen

Eine Hauptfunktion von Azure Digital Twins ist das einfache [Abfragen](concepts-query-language.md) des Zwillingsgraphen und das effiziente Beantworten von Fragen zur Umgebung. 

Führen Sie in der ausgeführten Projektkonsole die folgenden Befehle aus, um einige Fragen zur Beispielumgebung zu beantworten.

1. **Welche Entitäten aus meiner Umgebung werden in Azure Digital Twins dargestellt?** (Abfrage aller Elemente)

    ```cmd/sh
    Query
    ```

    Auf diese Weise können Sie schnell eine Bestandsaufnahme Ihrer Umgebung machen und sicherstellen, dass in Azure Digital Twins alles nach Ihren Wünschen dargestellt wird. Das Ergebnis ist eine Ausgabe, die jeden digitalen Zwilling mit seinen Details enthält. Hier sehen Sie einen Auszug:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Screenshot: Teilergebnis der Zwillingsabfrage mit „room0“ und „floor1“":::

    >[!NOTE]
    >Im Beispielprojekt entspricht der Befehl `Query` ohne zusätzliche Argumente `Query SELECT * FROM DIGITALTWINS`. Wenn Sie alle Zwillinge in Ihrer Instanz mithilfe der [Abfrage-APIs](/rest/api/digital-twins/dataplane/query) oder der [CLI-Befehle](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) abfragen möchten, verwenden Sie die längere (vollständige) Abfrage.

1. **Welche Räume sind in meiner Umgebung vorhanden?** (Abfrage nach Modell)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Sie können Ihre Abfrage auf Zwillinge eines bestimmten Typs beschränken, um spezifischere Informationen zu den dargestellten Elementen zu erhalten. Im Ergebnis werden „room0“ und „room1“, aber **nicht** „floor0“ oder „floor1“ angezeigt (da es sich dabei um Etagen und nicht um Räume handelt).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Screenshot: Ergebnis der Modellabfrage, nur mit „room0“ und „room1“":::

1. **Welche Räume befinden sich auf *floor0*?** (Abfrage nach Beziehung)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Sie können Abfragen basierend auf den Beziehungen in Ihrem Graphen durchführen, um Informationen zu den Beziehungen zwischen Zwillingen zu erhalten oder um die Abfrage auf einen bestimmten Bereich zu beschränken. Auf floor0 befindet sich nur room0, daher ist dies der einzige Raum im Ergebnis.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Screenshot: Ergebnis der Beziehungsabfrage mit „room0“":::

1. **Welche Zwillinge in meiner Umgebung haben eine Temperatur über 75?** (Abfrage nach Eigenschaft)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Sie können den Graphen auf der Grundlage von Eigenschaften abfragen, um eine Vielzahl von Fragen zu beantworten. Dazu zählt auch das Auffinden von Ausreißern in Ihrer Umgebung, die möglicherweise Ihre Aufmerksamkeit erfordern. Andere Vergleichsoperatoren ( *<* , *>* , *=* oder *!=* ) werden ebenfalls unterstützt. room1 wird hier in den Ergebnissen angezeigt, da die Temperatur 80 beträgt.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Screenshot: Ergebnis der Eigenschaftenabfrage, nur mit „room1“":::

1. **Welche Räume auf *floor0* haben eine Temperatur über 75?** (Verbundabfrage)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Sie können die Abfragen oben auch wie in SQL mithilfe von Kombinationsoperatoren (etwa `AND`, `OR`, `NOT`) kombinieren. Diese Abfrage verwendet `AND`, um die vorherige Abfrage der Zwillingstemperatur spezifischer zu gestalten. Das Ergebnis enthält jetzt nur noch Räume mit einer Temperatur von über 75, die sich auf floor0 befinden – in diesem Fall kein Raum. Das Resultset ist leer.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Screenshot: Ergebnis der Verbundabfrage (keine Ergebnisse)" lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss dieses Tutorials können Sie die zu entfernenden Ressourcen auswählen, abhängig davon, wie Sie weiter vorgehen möchten.

* **Wenn Sie mit dem nächsten Tutorial fortfahren möchten**, können Sie die hier eingerichteten Ressourcen behalten, um diese Azure Digital Twins-Instanz und die konfigurierte Beispiel-App für das nächste Tutorial zu verwenden.

* **Wenn Sie die Azure Digital Twins-Instanz weiterhin verwenden, aber alle ihre Modelle, Zwillinge und Beziehungen entfernen möchten**, können Sie die Befehle `DeleteAllTwins` und `DeleteAllModels` der Beispiel-App verwenden, um die Zwillinge und Modelle in Ihrer Instanz zu löschen.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Löschen Sie ggf. auch den Projektordner von Ihrem lokalen Computer.

## <a name="next-steps"></a>Nächste Schritte 

In diesem Tutorial haben Sie die ersten Schritte mit Azure Digital Twins durchgeführt, indem Sie unter Verwendung einer Beispielclientanwendung einen Graphen in Ihrer Instanz erstellt haben. Sie haben Modelle, digitale Zwillinge und Beziehungen erstellt, um einen Graphen zu erstellen. Darüber hinaus haben Sie einige Abfragen für den Graphen ausgeführt, um eine Vorstellung davon zu erhalten, welche Arten von Fragen Azure Digital Twins zu einer Umgebung beantworten kann.

Im nächsten Tutorial wird eine Kombination aus Azure Digital Twins und anderen Azure-Diensten in einem datengesteuerten End-to-End-Szenario verwendet:
> [!div class="nextstepaction"]
> [Verbinden einer End-to-End-Lösung](tutorial-end-to-end.md)