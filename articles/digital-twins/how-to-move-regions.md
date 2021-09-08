---
title: Verschieben einer Instanz in eine andere Azure-Region
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie eine Instanz von Azure Digital Twins aus einer Azure-Region in eine andere verschieben.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 9617fc3cc7668d8449a9ec3b46e6c82228783e9d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447147"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Verschieben einer Azure Digital Twins-Instanz in eine andere Azure-Region

Wenn Sie Ihre Azure Digital Twins-Instanz in eine andere Region verschieben müssen, erstellen Sie Ihre Ressourcen in der neuen Region neu, und löschen Sie dann die ursprünglichen Ressourcen. Am Ende dieses Vorgangs werden Sie mit einer neuen Azure Digital Twins-Instanz arbeiten, die bis auf den aktualisierten Speicherort mit der ersten identisch ist.

Dieser Artikel bietet einen Leitfaden, wie Sie eine vollständige Verschiebung durchführen, indem Sie alles kopieren, was Sie benötigen, damit die neue Instanz mit dem Original übereinstimmt.

Dieser Vorgang umfasst die folgenden Schritte:

1. Vorbereiten: Laden Sie Ihre ursprünglichen Modelle, Zwillinge und Graphen herunter.
1. Verschieben: Erstellen Sie eine neue Azure Digital Twins-Instanz in einer neuen Region.
1. Verschieben: Füllen Sie die neue Azure Digital Twins-Instanz neu auf.
    - Laden Sie die ursprünglichen Modelle, Zwillinge und Graphen hoch.
    - Erstellen Sie Endpunkte und Routen neu.
    - Verknüpfen Sie verbundene Ressourcen erneut.
1. Bereinigen der Quellressourcen: Löschen Sie die ursprüngliche Instanz.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie versuchen, Ihre Azure Digital Twins-Instanz neu zu erstellen, gehen Sie die Komponenten Ihrer ursprünglichen Instanz durch, um sich eine klare Vorstellung von allen Komponenten zu machen, die erneut erstellt werden müssen.

Dabei stellen sich unter anderem folgende Fragen:

* Welche *Modelle* werden in meine Instanz hochgeladen? Wie viele sind es?
* Welche sind in meiner Instanz die *Zwillinge*? Wie viele sind es?
* Wie ist die allgemeine Form des *Graphen* in meiner Instanz? Wie viele Beziehungen gibt es?
* Über welche *Endpunkte* verfüge ich in meiner Instanz?
* Über welche *Routen* verfüge ich in meiner Instanz? Besitzen sie Filter?
* Wo wird meine Instanz *mit anderen Azure-Diensten* verbunden? Einige allgemeine Integrationspunkte sind:

    - Azure Event Grid, Azure Event Hubs und Azure Service Bus
    - Azure-Funktionen
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning-Dienst
* Welche anderen *persönlichen oder Unternehmens-Apps* besitze ich, die eine Verbindung mit meiner Instanz herstellen?

Sie können diese Informationen über das [Azure-Portal](https://portal.azure.com), mithilfe der [Azure Digital Twins-APIs und -SDKs](concepts-apis-sdks.md), den [Befehlen der Azure Digital Twins-CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) oder den [Azure Digital Twins-Explorer](concepts-azure-digital-twins-explorer.md) erfassen.

## <a name="prepare"></a>Vorbereiten

In diesem Abschnitt bereiten Sie die Neuerstellung Ihrer Instanz vor, indem Sie Ihre Originalmodelle, Zwillinge und den Graphen aus Ihrer ursprünglichen Instanz herunterladen. In diesem Artikel wird der [Azure Digital Twins-Explorer](concepts-azure-digital-twins-explorer.md) für diese Aufgabe verwendet.

>[!NOTE]
>Möglicherweise verfügen Sie bereits über Dateien, die die Modelle oder den Graphen in Ihrer Instanz enthalten. Sofern dies der Fall ist, müssen Sie nicht alles erneut herunterzuladen – nur die fehlenden Teile oder Komponenten, die sich seit dem ursprünglichen Hochladen dieser Dateien geändert haben könnten. Beispielsweise können Zwillinge vorhanden sein, die mit neuen Daten aktualisiert wurden.

### <a name="download-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Herunterladen von Modellen, Zwillingen und eines Graphen mit dem Azure Digital Twins-Explorer

Öffnen Sie als zunächst [Azure-Portal](https://portal.azure.com) den **Azure Digital Twins-Explorer** für Ihre Instanz. Navigieren Sie dazu im Portal zu Ihrer neuen Azure Digital Twins-Instanz, indem Sie in der Suchleiste des Portals nach ihrem Namen suchen. Wählen Sie dann die Schaltfläche **Explorer (Vorschau) öffnen** aus. 

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Screenshot des Azure-Portal mit der Seite „Übersicht“ für eine Azure Digital Twins-Instanz. Die Schaltfläche „Azure Digital Twins-Explorer (Vorschau) öffnen“ ist hervorgehoben." lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

Dadurch wird ein Azure Digital Twins-Explorer-Fenster geöffnet, das mit dieser Instanz verbunden ist.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Screenshot: Azure-Portal in einem Internetbrowser Im Portal wird der Azure Digital Twins-Explorer angezeigt, der keine Daten enthält." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

Befolgen Sie die Anweisungen des Azure Digital Twins-Explorers zum [Exportieren von Graphen und Modellen](how-to-use-azure-digital-twins-explorer.md#export-graph-and-models). Dadurch wird eine JSON-Datei auf Ihren Computer heruntergeladen, die den Code für Ihre Modelle, Zwillinge und Beziehungen enthält (auch für Modelle, die derzeit nicht im Graphen verwendet werden).

## <a name="move"></a>Move

Nun vervollständigen Sie das „Verschieben“ Ihrer Instanz, indem Sie eine neue Instanz in der Zielregion erstellen. Anschließend füllen Sie diese mit den Daten und Komponenten Ihrer ursprünglichen Instanz auf.

### <a name="create-a-new-instance"></a>Erstellen einer neuen Instanz

Erstellen Sie zunächst eine neue Azure Digital Twins-Instanz in Ihrer Zielregion. Führen Sie die Schritte unter [Einrichten einer Instanz und der Authentifizierung](how-to-set-up-instance-portal.md) aus. Beachten Sie Folgendes:

* Sie können denselben Namen für die neue Instanz beibehalten, *wenn* sie sich in einer anderen Ressourcengruppe befindet. Wenn Sie dieselbe Ressourcengruppe verwenden müssen, die Ihre ursprüngliche Instanz enthält, benötigt Ihre neue Instanz einen eigenen, eindeutigen Namen.
* Geben Sie die neue Zielregion ein, wenn Sie zur Eingabe eines Standorts aufgefordert werden.

Sobald dieser Schritt abgeschlossen wurde, benötigen Sie den Hostnamen Ihrer neuen Instanz, um die Einrichtung mit Ihren Daten fortzusetzen. Wenn Sie sich den Hostnamen bei der Einrichtung nicht notiert haben, können Sie [diese Anweisungen](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) befolgen, um ihn jetzt aus dem Azure-Portal abzurufen.

Nun richten Sie die Daten der neuen Instanz so ein, dass sie eine Kopie der ursprünglichen Instanz ist.

#### <a name="upload-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Hochladen von Modellen, Zwillingen und eines Graphen mit dem Azure Digital Twins-Explorer

In diesem Abschnitt können Sie die Modelle, Zwillinge und Graphen erneut in die neue Instanz hochladen. Wenn Sie in Ihrer ursprünglichen Instanz keine Modelle, Zwillinge oder Graphen besitzen oder Sie diese nicht in die neue Instanz verschieben möchten, können Sie mit dem [nächsten Abschnitt](#re-create-endpoints-and-routes) fortfahren.

Navigieren Sie zunächst im [Azure-Portal](https://portal.azure.com) zum **Azure Digital Twins-Explorer** für die neue Instanz. 

Importieren Sie die zuvor in diesem Artikel [heruntergeladene JSON-Datei](#download-models-twins-and-graph-with-azure-digital-twins-explorer) in Ihre neue Instanz, indem Sie die Schritte in den Anweisungen des Azure Digital Twins-Explorers zum [Importieren einer Datei in Azure Digital Twins-Explorer](how-to-use-azure-digital-twins-explorer.md#import-file-to-azure-digital-twins-explorer) ausführen. Dadurch werden alle Modelle, Zwillinge und Beziehungen aus Ihrer ursprünglichen Instanz in die neue Instanz hochgeladen.

Um zu überprüfen, ob alles erfolgreich hochgeladen wurde, navigieren Sie zur Registerkarte **Twin Graph** (Zwillingsgraph) zurück und wählen die Schaltfläche **Abfrage ausführen** im Bereich **Abfrage-Explorer** aus. Damit führen Sie die Standardabfrage aus, die alle Zwillinge und Beziehungen im Graphen anzeigt. Durch diese Aktion wird auch die Liste der Modelle im Bereich **Modelle** aktualisiert.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit hervorgehobenen Schaltfläche „Abfrage ausführen“ rechts oben im Fenster." lightbox="media/how-to-move-regions/run-query.png":::

Der Graph sollte mit all seinen Zwillingen und Beziehungen im Bereich **Twin Graph** (Zwillingsgraph) angezeigt werden. Ihre Modelle sollten auch im Bereich **Modelle** aufgelistet werden.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit zwei Modellen, die im Feld „Modelle“ hervorgehoben sind, und ein Graph, der im Feld „Twin Graph“ (Zwillingsgraph) hervorgehoben ist." lightbox="media/how-to-move-regions/post-upload.png":::

Diese Ansichten bestätigen, dass Ihre Modelle, Zwillinge und Graphen erneut in die neue Instanz in der Zielregion hochgeladen wurden.

#### <a name="re-create-endpoints-and-routes"></a>Erneutes Erstellen von Endpunkten und Routen

Wenn Sie Endpunkte oder Routen in Ihrer ursprünglichen Instanz besitzen, müssen Sie diese in Ihrer neuen Instanz neu erstellen. Wenn Sie in Ihrer ursprünglichen Instanz keine Endpunkte oder Routen besitzen oder Sie diese nicht in die neue Instanz verschieben möchten, können Sie zum [nächsten Abschnitt](#relink-connected-resources) springen.

Führen Sie andernfalls die Schritte unter [Verwalten von Endpunkten und Routen](how-to-manage-routes.md) aus, und verwenden Sie dabei die neue Instanz. Beachten Sie Folgendes:

* Sie müssen *nicht* die Event Grid-, Event Hub- oder Service Bus-Ressource neu erstellen, die Sie für den Endpunkt verwenden. Weitere Informationen finden Sie im Abschnitt „Voraussetzungen“ in den Endpunktanleitungen. Sie müssen den Endpunkt nur auf der Azure Digital Twins-Instanz neu erstellen.
* Sie können Endpunkt- und Routennamen wiederverwenden, da ihr Gültigkeitsbereich eine andere Instanz ist.
* Denken Sie daran, alle erforderlichen Filter zu den von Ihnen erstellten Routen hinzuzufügen.

#### <a name="relink-connected-resources"></a>Erneutes Verknüpfen verbundener Ressourcen

Wenn Sie andere Apps oder Azure-Ressourcen besitzen, die mit Ihrer ursprünglichen Azure Digital Twins-Instanz verbunden sind, müssen Sie die Verbindung bearbeiten, sodass sie stattdessen Ihre neue Instanz erreichen. Diese Ressourcen können andere Azure-Dienste oder persönliche bzw. Unternehmens-Apps umfassen, die Sie für die Zusammenarbeit mit Azure Digital Twins eingerichtet haben.

Wenn mit Ihrer ursprünglichen Instanz keine anderen Ressourcen verbunden sind oder Sie diese nicht in die neue Instanz verschieben möchten, können Sie zum [nächsten Abschnitt](#verify) springen.

Andernfalls sollten Sie die verbundenen Ressourcen in Ihrem Szenario berücksichtigen. Sie müssen keine verbundenen Ressourcen löschen und neu erstellen. Stattdessen müssen Sie nur die Punkte bearbeiten, an denen Sie über ihren Hostnamen eine Verbindung mit einer Azure Digital Twins-Instanz herstellen. Anschließend aktualisieren Sie diese Punkte so, dass der Hostname der neuen Instanz anstelle des Namens der ursprünglichen Instanz verwendet wird.

Die genauen Ressourcen, die Sie bearbeiten müssen, hängen von Ihrem Szenario ab, aber hier folgen einige allgemeine Integrationspunkte:

* Azure Functions. Wenn Sie über eine Azure-Funktion verfügen, deren Code den Hostnamen der ursprünglichen Instanz enthält, sollten Sie diesen Wert auf den Hostnamen der neuen Instanz aktualisieren und die Funktion erneut veröffentlichen.
* Event Grid, Event Hubs oder Service Bus.
* Logik-Apps
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Persönliche oder Unternehmens-Apps außerhalb von Azure, z. B. die Client-App, die unter [Codieren einer Client-App](tutorial-code.md) erstellt wurde, die eine Verbindung mit der Instanz herstellt und Azure Digital Twins-APIs aufruft.
* Azure AD-App-Registrierungen müssen *nicht* neu erstellt werden. Wenn Sie eine [App-Registrierung](./how-to-create-app-registration-portal.md) verwenden, um eine Verbindung mit den Azure Digital Twins-APIs herzustellen, können Sie die gleiche App-Registrierung mit Ihrer neuen Instanz wiederverwenden.

Nachdem Sie diesen Schritt abgeschlossen haben, sollte Ihre neue Instanz in der Zielregion eine Kopie der ursprünglichen Instanz sein.

## <a name="verify"></a>Überprüfung

Zum Überprüfen, ob Ihre neue Instanz ordnungsgemäß eingerichtet wurde, verwenden Sie die folgenden Tools:

* [Azure-Portal](https://portal.azure.com). Das Portal eignet sich gut, um zu überprüfen, ob Ihre neue Instanz vorhanden ist und sich in der richtigen Zielregion befindet. Außerdem eignet es sich gut zum Überprüfen von Endpunkten und Routen sowie Verbindungen mit anderen Azure-Diensten.
* [Befehle der Azure Digital Twins-Cli](/cli/azure/dt?view=azure-cli-latest&preserve-view=true). Diese Befehle eignen sich gut, um zu überprüfen, ob Ihre neue Instanz vorhanden ist und sich in der richtigen Zielregion befindet. Sie können auch zum Überprüfen von Instanzdaten verwendet werden.
* [Azure Digital Twins-Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twins-Explorer eignet sich gut für die Überprüfung von Instanzdaten wie Modellen, Zwillingen und Graphen.
* [Azure Digital Twins-APIs und -SDKs](concepts-apis-sdks.md). Diese Ressourcen eignen sich gut für die Überprüfung von Instanzdaten wie Modellen, Zwillingen und Graphen. Sie sind auch für die Überprüfung von Endpunkten und Routen geeignet.

Sie können auch versuchen, alle benutzerdefinierten Apps oder End-to-End-Flows, die Sie mit Ihrer ursprünglichen Instanz ausgeführt haben, auszuführen, um zu überprüfen, ob sie mit der neuen Instanz ordnungsgemäß funktionieren.

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen

Nachdem Ihre neue Instanz jetzt in der Zielregion mit einer Kopie der Daten und Verbindungen der ursprünglichen Instanz eingerichtet ist, können Sie die ursprüngliche Instanz löschen.

Sie können das [Azure-Portal](https://portal.azure.com), die [Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) oder die [APIs der Steuerungsebene](concepts-apis-sdks.md#overview-control-plane-apis) verwenden.

Um die Instanz über das Azure-Portal zu löschen, [öffnen Sie das Portal](https://portal.azure.com) in einem Browserfenster, und navigieren Sie zu Ihrer ursprünglichen Azure Digital Twins-Instanz, indem Sie in der Portalsuchleiste nach dem Namen suchen.

Wählen Sie die Schaltfläche **Löschen** aus, und folgen Sie den Aufforderungen, um den Löschvorgang abzuschließen.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Screenshot: Details der Azure Digital Twins-Instanz im Azure-Portal auf der Registerkarte „Übersicht“. Die Schaltfläche „Löschen“ ist hervorgehoben.":::