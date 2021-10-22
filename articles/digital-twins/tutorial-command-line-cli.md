---
title: 'Tutorial: Erstellen eines Graphen in Azure Digital Twins (CLI)'
titleSuffix: Azure Digital Twins
description: Tutorial zum Erstellen eines Azure Digital Twins-Szenarios unter Verwendung der Azure CLI
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 8df2c2a1eb7a16df4d35a8b6530aec82a1632722
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996283"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Tutorial: Erstellen eines Azure Digital Twins-Graphen unter Verwendung der Azure CLI

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

In diesem Tutorial wird in Azure Digital Twins ein Graph mit Modellen, Zwillingen und Beziehungen erstellt. Als Tool wird in diesem Tutorial der [Azure Digital Twins-Befehlssatz für die Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) verwendet.

Diese CLI-Befehle ermöglichen grundlegende Azure Digital Twins-Aktionen wie das Hochladen von Modellen, das Erstellen und Ändern von Zwillingen und das Erstellen von Beziehungen. Den vollständigen CLI-Befehlssatz finden Sie in der [Referenzdokumentation zum Befehlssatz „az dt“](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Modellieren einer Umgebung
> * Erstellen digitaler Zwillinge
> * Hinzufügen von Beziehungen zur Erstellung eines Graphen
> * Abfragen des Graphen zur Beantwortung von Fragen

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

### <a name="download-the-sample-models"></a>Herunterladen der Beispielmodelle

In diesem Tutorial werden zwei vorab geschriebene Modelle aus dem [End-to-End-C#-Beispielprojekt](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) für Azure Digital Twins verwendet. Die Modelldateien befinden sich hier: 
* [Room.json](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [Floor.json](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Verwenden Sie die oben bereitgestellten Navigationslinks, und kopieren Sie die Dateiinhalte auf Ihrem Computer in lokale Dateien mit dem gleichen Namen (*Room.json* und *Floor.json*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Einrichten einer Cloud Shell-Sitzung
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Vorbereiten einer Azure Digital Twins-Instanz

Für die Arbeit mit Azure Digital Twins in diesem Artikel müssen Sie zuerst **eine Azure Digital Twins-Instanz einrichten** und die für die Nutzung erforderlichen Berechtigungen gewähren. Wenn Sie zuvor bereits eine Azure Digital Twins-Instanz eingerichtet haben, können Sie diese nutzen.

Befolgen Sie andernfalls die Anleitung unter [Einrichten einer Instanz und der Authentifizierung](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance). Die Anleitung enthält auch Schritte zur Überprüfung, ob Sie jeden Schritt erfolgreich abgeschlossen haben und für die Nutzung Ihrer neuen Instanz bereit sind.

Nachdem Sie Ihre Azure Digital Twins-Instanz eingerichtet haben, notieren Sie sich die folgenden Werte, die Sie benötigen, um später eine Verbindung mit der Instanz herzustellen:
* Den **_Hostnamen_** der Instanz
* Das **Azure-Abonnement**, das Sie zum Erstellen der Instanz verwendet haben 

Diese beiden Werte für Ihre Instanz sind in der Ausgabe des folgenden Azure CLI-Befehls enthalten: 

```azurecli-interactive
az dt show --dt-name <Azure-Digital-Twins-instance-name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Screenshot: Cloud Shell-Browserfenster mit der Ausgabe des Befehls „az dt show“. Die Felder für Hostname (hostName) und Abonnement-ID sind hervorgehoben.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modellieren einer physischen Umgebung mit DTDL

Nachdem die CLI und die Azure Digital Twins-Instanz eingerichtet sind, können Sie mit der Erstellung eines Szenariographen beginnen. 

Beim Erstellen einer Azure Digital Twins-Lösung werden zunächst [Zwillingsmodelle](concepts-models.md) für Ihre Umgebung definiert. 

Modelle ähneln Klassen in objektorientierten Programmiersprachen. Sie stellen benutzerdefinierte Vorlagen für [digitale Zwillinge](concepts-twins-graph.md) bereit, die später eingesetzt und instanziiert werden. Sie sind in einer JSON-ähnlichen Sprache namens **Digital Twins Definition Language (DTDL)** geschrieben und können die *Eigenschaften*, *Telemetriedaten*, *Beziehungen* und *Komponenten* eines Zwillings festlegen.

> [!NOTE]
> DTDL ermöglicht außerdem die Definition von *Befehlen* für digitale Zwillinge. Allerdings werden Befehle im Azure Digital Twins-Dienst derzeit nicht unterstützt.

Navigieren Sie auf Ihrem Computer zur Datei *Room.js*, die Sie im Abschnitt [Voraussetzungen](#prerequisites) erstellt haben. Öffnen Sie sie in einem Code-Editor, und ändern Sie sie wie folgt:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Hochladen von Modellen in Azure Digital Twins

Nach dem Entwerfen von Modellen müssen Sie diese in Ihre Azure Digital Twins-Instanz hochladen. Dadurch wird die Azure Digital Twins-Dienstinstanz mit Ihrem eigenen benutzerdefinierten Domänenvokabular konfiguriert. Nach dem Hochladen der Modelle können Sie Zwillingsinstanzen erstellen, die diese verwenden.

1. Um Modelle mithilfe von Cloud Shell hinzufügen zu können, müssen Sie Ihre Modelldateien in den Cloud Shell-Speicher hochladen, damit sie verfügbar sind, wenn Sie den Cloud Shell-Befehl für sie ausführen. Wählen Sie hierzu das Symbol „Dateien hochladen/herunterladen“ und anschließend „Hochladen“ aus.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Screenshot: Cloud Shell-Browserfenster mit hervorgehobenem Symbol „Hochladen“":::
    
    Navigieren Sie zur Datei *Room.json* auf Ihrem Computer, und wählen Sie „Öffnen“ aus. Wiederholen Sie diesen Schritt anschließend für die Datei *Floor.js*.

1. Verwenden Sie als Nächstes den Befehl [az dt model create](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_create), wie im Anschluss gezeigt, um das aktualisierte Raummodell (Room) in Ihre Azure Digital Twins-Instanz hochzuladen. Mit dem zweiten Befehl wird ein weiteres Modell (Floor) hochgeladen. Dieses wird auch im nächsten Abschnitt verwendet, um verschiedene Arten von Zwillingen zu erstellen.

    ```azurecli-interactive
    az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Room.json
    az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Floor.json
    ```
    
    Die Ausgabe des jeweiligen Befehls enthält Informationen zum erfolgreich hochgeladenen Modell.

    >[!TIP]
    >Sie können auch alle Modelle in einem Verzeichnis auf einmal hochladen, indem Sie die Option `--from-directory` für den Befehl „model create“ verwenden. Weitere Informationen finden Sie in den [optionalen Parametern für „az dt model create“](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_create-optional-parameters).

1. Vergewissern Sie sich wie im Anschluss gezeigt mithilfe des Befehls [az dt model list](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_list), dass die Modelle erstellt wurden. Dieser Befehl gibt eine Liste mit allen Modellen, die in die Azure Digital Twins-Instanz hochgeladen wurden, sowie mit ausführlichen Informationen zu den Modellen aus. 

    ```azurecli-interactive
    az dt model list --dt-name <Azure-Digital-Twins-instance-name> --definition
    ```
    
    Suchen Sie in den Ergebnissen nach dem bearbeiteten Room-Modell:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Screenshot: Cloud Shell mit dem Ergebnis des Befehls „model list“ (mit dem aktualisierten Raummodell)" lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Errors

Von der CLI werden auch Fehler des Diensts behandelt. 

Führen Sie den Befehl `az dt model create` erneut aus, um eins des soeben hochgeladenen Modelle ein zweites Mal hochzuladen:

```azurecli-interactive
az dt model create --dt-name <Azure-Digital-Twins-instance-name> --models Room.json
```

Da Modelle nicht überschrieben werden können, wird nun der Fehlercode `ModelIdAlreadyExists` zurückgegeben.

## <a name="create-digital-twins"></a>Erstellen digitaler Zwillinge

Sie haben einige Modelle in Ihre Azure Digital Twins-Instanz hochgeladen und können nun basierend auf den Modelldefinitionen [digitale Zwillinge](concepts-twins-graph.md) erstellen. Digitale Zwillinge stellen die Entitäten in Ihrer Geschäftsumgebung dar, etwa Sensoren in einer Farm, Räume in einem Gebäude oder Beleuchtung in einem Fahrzeug. 

Verwenden Sie zum Erstellen eines digitalen Zwillings den Befehl [az dt twin create](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_create). Sie müssen auf das Modell verweisen, auf dem der Zwilling basiert, und können optional Anfangswerte für Eigenschaften im Modell definieren. Sie müssen in dieser Phase keine Beziehungsinformationen übergeben.

1. Führen Sie diesen Code in Cloud Shell aus, um mehrere Zwillinge basierend auf dem zuvor aktualisierten Modell Room (Raum) und einem anderen Modell (Floor) zu erstellen. Denken Sie daran, dass Room drei Eigenschaften hat, sodass Sie Argumente mit den Anfangswerten für diese angeben können. (Die Initialisierung von Eigenschaftswerten ist grundsätzlich optional, für dieses Tutorial werden sie allerdings benötigt.)

    ```azurecli-interactive
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":80, "HumidityLevel":60}'
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Wenn Sie Cloud Shell in der PowerShell-Umgebung verwenden, müssen die Anführungszeichen ggf. mit Escapezeichen versehen werden, damit der JSON-Wert `--properties` ordnungsgemäß analysiert wird. Mit dieser Bearbeitung sehen die Befehle zum Erstellen der Raumzwillinge wie folgt aus:
    >
    > ```azurecli-interactive
    > az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create --dt-name <Azure-Digital-Twins-instance-name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Dies wird im Screenshot weiter unten dargestellt.
    
    Die Ausgabe der einzelnen Befehle enthält Informationen zum erfolgreich erstellten Zwilling (einschließlich Eigenschaften für die Raumzwillinge, die mit ihnen initialisiert wurden).

1. Mithilfe des im Anschluss gezeigten Befehls [az dt twin query](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) können Sie sich vergewissern, dass die Zwillinge erstellt wurden. Mit der gezeigten Abfrage wird nach allen digitalen Zwillingen in Ihrer Azure Digital Twins-Instanz gesucht.
    
    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS"
    ```
    
    Suchen Sie in den Ergebnissen nach den Zwillingen room0, room1, floor0 und floor1. Der folgende Auszug zeigt einen Teil des Ergebnisses dieser Abfrage:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Screenshot: Cloud Shell mit einem Teil des Ergebnisses der Zwillingsabfrage (mit „room0“ und „room1“)" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

### <a name="modify-a-digital-twin"></a>Ändern eines digitalen Zwillings

Sie können auch die Eigenschaften eines von Ihnen erstellten Zwillings ändern. 

1. Führen Sie den Befehl [az dt twin update](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update) aus, um den RoomName-Wert für *room0* von *Room0* in *PresidentialSuite* zu ändern:

    ```azurecli-interactive
    az dt twin update --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Wenn Sie Cloud Shell in der PowerShell-Umgebung verwenden, müssen die Anführungszeichen ggf. mit Escapezeichen versehen werden, damit der JSON-Wert `--json-patch` ordnungsgemäß analysiert wird. Mit dieser Bearbeitung sieht der Befehl zum Aktualisieren des Zwillings wie folgt aus:
    >
    > ```azurecli-interactive
    > az dt twin update --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Dies wird im Screenshot weiter unten dargestellt.
    
    Die Ausgabe dieses Befehls enthält die aktuellen Informationen des Zwillings mit dem neuen Wert für `RoomName` im Ergebnis.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Screenshot: Cloud Shell mit dem Ergebnis des Aktualisierungsbefehls (mit dem RoomName-Wert „PresidentialSuite“)" lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. Sie können den Befehl [az dt twin show](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) ausführen, um die Informationen zu room0 anzuzeigen und sich zu vergewissern, dass die Aktualisierung erfolgreich war:

    ```azurecli-interactive
    az dt twin show --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0
    ```
    
    In der Ausgabe sollte der aktualisierte Name angezeigt werden.

## <a name="create-a-graph-by-adding-relationships"></a>Erstellen eines Graphen durch Hinzufügen von Beziehungen

Im nächsten Schritt können Sie einige **Beziehungen** zwischen diesen Zwillingen erstellen, um Sie in einem [Zwillingsgraphen](concepts-twins-graph.md) zu verbinden. Zwillingsgraphen werden verwendet, um eine gesamte Umgebung darzustellen. 

Die Arten von Beziehungen, die Sie zwischen Zwillingen erstellen können, werden in den zuvor hochgeladenen [Modellen](#model-a-physical-environment-with-dtdl) definiert. In der [Modelldefinition für „Floor“](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) ist angegeben, dass Etagen eine Beziehung vom Typ *contains* (enthält) haben können. Dies ermöglicht die Erstellung einer Beziehung vom Typ *enthält* zwischen jedem Etagenzwilling (Floor) und dem entsprechenden enthaltenen Raum.

Verwenden Sie zum Hinzufügen einer Beziehung den Befehl [az dt twin relationship create](/cli/azure/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#az_dt_twin_relationship_create). Geben Sie den Zwilling, von dem die Beziehung ausgeht, die Art der Beziehung sowie den Zwilling an, mit dem die Beziehung hergestellt wird. Geben Sie abschließend eine eindeutige ID für die Beziehung an. Wenn eine Beziehung mit Eigenschaften definiert wurde, können Sie in diesem Befehl auch die Beziehungseigenschaften initialisieren.

1. Führen Sie den folgenden Code aus, um eine Beziehung vom Typ *contains* (enthält) zwischen jedem der zuvor erstellten Etagenzwillinge (Floor) um dem entsprechenden Raumzwilling (Room) hinzuzufügen. Die Beziehungen heißen relationship0 und relationship1.

    ```azurecli-interactive
    az dt twin relationship create --dt-name <Azure-Digital-Twins-instance-name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create --dt-name <Azure-Digital-Twins-instance-name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >Da die Beziehung vom Typ *contains* (enthält) im Modell [Floor (Etage)](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) mit zwei Eigenschaften (`ownershipUser` und `ownershipDepartment`) definiert wurde, können Sie beim Erstellen der Beziehungen auch Argumente mit den Anfangswerten für diese Eigenschaften angeben.
    > Wenn Sie eine Beziehung mit Initialisierung dieser Eigenschaften erstellen möchten, müssen Sie einem der obigen Befehle die Option `--properties` hinzufügen:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Wenn Sie Cloud Shell in der PowerShell-Umgebung verwenden, müssen die Anführungszeichen ggf. mit Escapezeichen versehen werden, damit der JSON-Wert `--properties` ordnungsgemäß analysiert wird.
    
    Die Ausgabe des jeweiligen Befehls enthält Informationen zur erfolgreich erstellten Beziehung.

1. Sie können die Beziehungen mithilfe einer der folgenden Befehle zum Ausgeben der Beziehungen in der Azure Digital Twins-Instanz überprüfen.
    * So zeigen Sie alle Beziehungen an, die von den einzelnen Etagen ausgehen (Anzeige der Beziehungen von einer Seite):
        ```azurecli-interactive
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor0
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor1
        ```
    * So zeigen Sie alle Beziehungen an, die bei jedem Raum ankommen (Anzeige der Beziehungen von der anderen Seite):
        ```azurecli-interactive
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id room0 --incoming
        az dt twin relationship list --dt-name <Azure-Digital-Twins-instance-name> --twin-id room1 --incoming
        ```
    * So können Sie sich diese Beziehungen einzeln (nach ID) ansehen:
        ```azurecli-interactive
        az dt twin relationship show --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show --dt-name <Azure-Digital-Twins-instance-name> --twin-id floor1 --relationship-id relationship1
        ```

Die Zwillinge und Beziehungen, die Sie in diesem Tutorial eingerichtet haben, bilden den folgenden konzeptionellen Graphen:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagramm mit einem Konzeptgraphen. „floor0“ ist über „relationship0“ mit „room0“ verbunden und „floor1“ über „relationship1“ mit „room1“." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Abfragen des Zwillingsgraphen zum Beantworten von Umgebungsfragen

Eine Hauptfunktion von Azure Digital Twins ist das einfache [Abfragen](concepts-query-language.md) des Zwillingsgraphen und das effiziente Beantworten von Fragen zur Umgebung. In der Azure CLI wird dazu der Befehl [az dt twin query](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) verwendet.

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

Führen Sie in Cloud Shell die folgenden Abfragen aus, um einige Fragen zur Beispielumgebung zu beantworten.

1. **Welche Entitäten aus meiner Umgebung werden in Azure Digital Twins dargestellt?** (Abfrage aller Elemente)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS"
    ```

    Auf diese Weise können Sie schnell eine Bestandsaufnahme Ihrer Umgebung machen und sicherstellen, dass in Azure Digital Twins alles nach Ihren Wünschen dargestellt wird. Das Ergebnis ist eine Ausgabe, die jeden digitalen Zwilling mit seinen Details enthält. Hier sehen Sie einen Auszug:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Screenshot: Cloud Shell mit einem Teil des Ergebnisses der Zwillingsabfrage (mit „room0“ und „room1“)" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Wie Sie sehen, handelt es sich hierbei um den gleichen Befehl, den Sie bereits im Abschnitt [Erstellen digitaler Zwillinge](#create-digital-twins) verwendet haben, um alle Azure Digital Twins-Instanzen in der Instanz zu finden.

1. **Welche Räume sind in meiner Umgebung vorhanden?** (Abfrage nach Modell)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Sie können Ihre Abfrage auf Zwillinge eines bestimmten Typs beschränken, um spezifischere Informationen zu den dargestellten Elementen zu erhalten. Im Ergebnis werden room0 und room1, aber **nicht** floor0 oder floor1 angezeigt (da es sich dabei um Etagen und nicht um Räume handelt).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Screenshot: Cloud Shell mit dem Ergebnis der Modellabfrage (nur mit „room0“ und „room1“)" lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Welche Räume befinden sich auf floor0?** (Abfrage nach Beziehung)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    Sie können Abfragen basierend auf den Beziehungen in Ihrem Graphen durchführen, um Informationen zu den Beziehungen zwischen Zwillingen zu erhalten oder um die Abfrage auf einen bestimmten Bereich zu beschränken. Auf floor0 befindet sich nur room0, daher ist dies der einzige Raum im Ergebnis.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Screenshot: Cloud Shell mit dem Ergebnis der Beziehungsabfrage (mit „room0“)" lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Die ID eines Zwillings (wie „floor0“ in der obigen Abfrage) wird über das Metadatenfeld `$dtId` abgefragt. 
    >
    >Wenn Sie Cloud Shell verwenden, um eine Abfrage mit Metadatenfeldern wie diesem auszuführen, die mit `$` beginnen, müssen Sie `$` mit einem Graviszeichen als Escapezeichen versehen, um Cloud Shell mitzuteilen, dass es sich dabei nicht um eine Variable handelt und es im Abfragetext als Literal behandelt werden soll. Dies ist im obigen Screenshot dargestellt.

1. **Welche Zwillinge in meiner Umgebung haben eine Temperatur über 75?** (Abfrage nach Eigenschaft)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Sie können den Graphen auf der Grundlage von Eigenschaften abfragen, um eine Vielzahl von Fragen zu beantworten. Dazu zählt auch das Auffinden von Ausreißern in Ihrer Umgebung, die möglicherweise Ihre Aufmerksamkeit erfordern. Andere Vergleichsoperatoren ( *<* , *>* , *=* oder *!=* ) werden ebenfalls unterstützt. room1 wird hier in den Ergebnissen angezeigt, da die Temperatur 80 beträgt.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Screenshot: Cloud Shell mit dem Ergebnis der Eigenschaftenabfrage (nur mit „room1“)" lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Welche Räume auf *floor0* haben eine Temperatur über 75?** (Verbundabfrage)

    ```azurecli-interactive
    az dt twin query --dt-name <Azure-Digital-Twins-instance-name> --query-command "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Sie können die Abfragen oben auch wie in SQL mithilfe von Kombinationsoperatoren (etwa `AND`, `OR`, `NOT`) kombinieren. Diese Abfrage verwendet `AND`, um die vorherige Abfrage der Zwillingstemperatur spezifischer zu gestalten. Das Ergebnis enthält jetzt nur noch Räume mit einer Temperatur von über 75, die sich auf floor0 befinden – in diesem Fall kein Raum. Das Resultset ist leer.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Screenshot: Cloud Shell mit dem Ergebnis der Verbundabfrage (ganz ohne Elemente)" lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss dieses Tutorials können Sie die zu entfernenden Ressourcen auswählen, abhängig davon, wie Sie weiter vorgehen möchten.

* **Wenn Sie mit dem nächsten Tutorial fortfahren möchten**, können Sie die hier eingerichteten Ressourcen behalten und die Azure Digital Twins-Instanz wiederverwenden, ohne dazwischen etwas zu löschen.

* **Wenn Sie die Azure Digital Twins-Instanz weiterverwenden wollen, aber alle zugehörigen Modelle, Zwillinge und Beziehungen entfernen möchten**, können Sie die Befehle [az dt twin relationship delete](/cli/azure/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#az_dt_twin_relationship_delete), [az dt twin delete](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_delete) und [az dt model delete](/cli/azure/dt/model?view=azure-cli-latest&preserve-view=true#az_dt_model_delete) verwenden, um die Beziehungen, Zwillinge und Modelle in Ihrer Instanz zu löschen.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Löschen Sie ggf. auch die Modelldateien, die Sie auf Ihrem lokalen Computer erstellt haben.

## <a name="next-steps"></a>Nächste Schritte 

In diesem Tutorial haben Sie die ersten Schritte mit Azure Digital Twins durchgeführt, indem Sie unter Verwendung der Azure CLI einen Graphen in Ihrer Instanz erstellt haben. Sie haben Modelle, digitale Zwillinge und Beziehungen erstellt, um einen Graphen zu erstellen. Darüber hinaus haben Sie einige Abfragen für den Graphen ausgeführt, um eine Vorstellung davon zu erhalten, welche Arten von Fragen Azure Digital Twins zu einer Umgebung beantworten kann.

Im nächsten Tutorial wird eine Kombination aus Azure Digital Twins und anderen Azure-Diensten in einem datengesteuerten End-to-End-Szenario verwendet:
> [!div class="nextstepaction"]
> [Verbinden einer End-to-End-Lösung](tutorial-end-to-end.md)