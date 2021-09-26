---
title: Erstellen von benutzerdefinierten .NET-Deserialisierern für Azure Stream Analytics-Cloudaufträge mithilfe von Visual Studio Code
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Visual Studio Code einen benutzerdefinierten .NET-Deserialisierer für einen Azure Stream Analytics-Cloudauftrag erstellen.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 6d5787268cd5f7d25b54f8c7bc63f59c20a8495b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606986"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Erstellen von benutzerdefinierten .NET-Deserialisierern für Azure Stream Analytics in Visual Studio Code

Azure Stream Analytics verfügt über [integrierte Unterstützung von drei Datenformaten](stream-analytics-parsing-json.md): JSON, CSV und Avro. Mit benutzerdefinierten .NET-Deserialisierern können Sie Daten aus anderen Formaten lesen, z. B. [Protokollpuffer](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) und andere benutzerdefinierte Formate für Cloudaufträge.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Benutzerdefinierte .NET-Deserialisierer in Visual Studio Code

Sie können einen benutzerdefinierten .NET-Deserialisierer für einen Azure Stream Analytics-Cloudauftrag mithilfe von Visual Studio Code erstellen, testen und debuggen.

### <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie [.NET Core SDK](https://dotnet.microsoft.com/download), und starten Sie Visual Studio Code neu.

* In diesem [Schnellstarttutorial](quick-create-visual-studio-code.md) erfahren Sie, wie ein Stream Analytics-Auftrags mithilfe von Visual Studio Code erstellt wird.

### <a name="create-a-custom-deserializer"></a>Erstellen eines benutzerdefinierten Deserialisierers

1. Öffnen Sie ein Terminal, und führen Sie den folgenden Befehl aus, um eine .NET-Klassenbibliothek in Visual Studio Code für Ihren benutzerdefinierten Deserialisierer namens **ProtobufDeserializer** zu erstellen.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Wechseln Sie zum Projektverzeichnis **ProtobufDeserializer**, und installieren Sie die NuGet-Pakete [Microsoft.Azure.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) und [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/).

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Fügen Sie Ihrem Projekt die Klassen [MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) und [MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) hinzu.

4. Kompilieren Sie das Projekt **Protobuf Deserializer**.

### <a name="add-an-azure-stream-analytics-project"></a>Hinzufügen eines Azure Stream Analytics-Projekts

1. Öffnen Sie Visual Studio Code, und drücken Sie **STRG+UMSCHALT+P**, um die Befehlspalette zu öffnen. Geben Sie dann ASA ein, und wählen Sie **ASA: Neues Projekt erstellen** aus. Geben Sie den Namen **ProtobufCloudDeserializer** ein.

### <a name="configure-a-stream-analytics-job"></a>Konfigurieren eines Stream Analytics-Auftrags

1. Doppelklicken Sie auf **JobConfig.json**. Verwenden Sie die folgenden Einstellungen, und behalten Sie ansonsten die Standardkonfiguration bei:

   |Einstellung|Empfohlener Wert|
   |-------|---------------|
   |Global Storage Settings Resource (Ressource für globale Speichereinstellungen)|Datenquelle aus aktuellem Konto wählen|
   |Global Storage Settings Subscription (Abonnement für globale Speichereinstellungen)| <Ihr Abonnement>|
   |Global Storage Settings Storage Account (Speicherkonto für globale Speichereinstellungen)| <Ihr Speicherkonto>|
   |CustomCodeStorage Settings Storage Account (Speicherkonto für Einstellungen für benutzerdefinierten Codespeicher)|<Ihr Speicherkonto>|
   |CustomCodeStorage Settings Container (Container für Einstellungen für benutzerdefinierten Codespeicher)|<Ihr Speichercontainer>|

2. Öffnen Sie im Ordner **Eingaben** die Datei **input.json**. Wählen Sie **Liveeingabe hinzufügen** aus, und fügen Sie eine Eingabe aus Azure Data Lake Storage Gen2 bzw. dem Blobspeicher hinzu. Klicken Sie dann auf **Aus Azure-Abonnement auswählen**. Verwenden Sie die folgenden Einstellungen, und behalten Sie ansonsten die Standardkonfiguration bei:

   |Einstellung|Empfohlener Wert|
   |-------|---------------|
   |Name|Eingabe|
   |Subscription|<Ihr Abonnement>|
   |Speicherkonto|<Ihr Speicherkonto>|
   |Container|<Ihr Speichercontainer>|
   |Serialisierungstyp|Wählen Sie **Benutzerdefiniert** aus.|
   |SerializationProjectPath|Wählen Sie in CodeLens die Option **Bibliotheksprojektpfad auswählen** und dann das Bibliotheksprojekt **ProtobufDeserializer**, das im letzten Abschnitt erstellt wurde. Wählen Sie **Projekt kompilieren** aus, um das Projekt zu kompilieren.|
   |SerializationClassName|Wählen Sie in CodeLens **Deserialisierungsklasse auswählen**, um den Klassenname und den DLL-Pfad automatisch aufzufüllen.|
   |Klassenname|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Hinzufügen einer benutzerdefinierten Deserialisierereingabe":::

3. Fügen Sie der Datei **ProtobufCloudDeserializer.asaql** die folgende Abfrage hinzu.

   ```sql
   SELECT * FROM Input
   ```

4. Laden Sie die [exemplarische Protobuf-Eingabedatei](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf) herunter. Klicken Sie im Ordner **Eingaben** mit der rechten Maustaste auf **input.json**, und wählen Sie **Lokale Eingabe hinzufügen** aus. Doppelklicken Sie auf **local_input1.json**, und verwenden Sie mit Ausnahme der folgenden Einstellungen die Standardkonfigurationswerte.

   |Einstellung|Empfohlener Wert|
   |-------|---------------|
   |Lokalen Dateipfad auswählen|Klicken Sie auf CodeLens, um den <Dateipfad für die heruntergeladene exemplarische Protobuf-Eingabedatei> auszuwählen.|

### <a name="execute-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

1. Öffnen Sie **ProtobufCloudDeserializer.asaql**, und wählen Sie **Lokal ausführen** aus CodeLens aus. Wählen Sie dann aus der Dropdownliste den Eintrag **Lokale Eingabe verwenden** aus.

2. Beobachten Sie die Ergebnisse auf der Registerkarte **Ergebnisse** in der Diagrammansicht für den Auftrag auf der rechten Seite. Sie können auch auf die Schritte im Auftragsdiagramm klicken, um ein Zwischenergebnis anzuzeigen. Weitere Details finden Sie unter [Lokales Debuggen mithilfe eines Auftragsdiagramms](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Überprüfen des Ergebnisses der lokalen Ausführung":::

Sie haben erfolgreich einen benutzerdefinierten Deserialisierer für Ihren Stream Analytics-Auftrag implementiert. In diesem Tutorial haben Sie den benutzerdefinierten Deserialisierer lokal mit lokalen Eingabedaten getestet. Sie können den Deserialisierer auch mit [Livedateneingaben in der Cloud](visual-studio-code-local-run-live-input.md) testen. Für die Ausführung des Auftrags in der Cloud müssen Sie die Ein- und Ausgabe ordnungsgemäß konfigurieren. Anschließend übermitteln Sie den Auftrag von Visual Studio Code aus an Azure, um ihn unter Verwendung des soeben implementierten benutzerdefinierten Deserialisierers in der Cloud auszuführen.

### <a name="debug-your-deserializer"></a>Debuggen Ihres Deserialisierers

Sie können Ihren benutzerdefinierten .NET-Deserialisierer genau wie .NET-Standardcode lokal debuggen. 

1. Fügen Sie Ihrer .NET-Funktion Breakpoints hinzu.

2. Klicken Sie in der Aktivitätsleiste von Visual Studio Code auf **Ausführen**, und wählen Sie **launch.json-Datei erstellen** aus.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Erstellen einer Startdatei":::

   Wählen Sie **ProtobufCloudDeserializer** und dann aus der Dropdownliste **Azure Stream Analytics** aus.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Erstellen einer Startdatei, Teil 2":::

   Bearbeiten Sie die Datei **launch.json**, und ersetzen Sie „\<ASAScript\>.asaql“ durch „ProtobufCloudDeserializer.asaql“.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Konfigurieren der Startdatei":::

3. Drücken Sie **F5**, um das Debuggen zu starten. Das Programm hält wie erwartet an den von Ihnen festgelegten Breakpoints an. Dies funktioniert sowohl für lokale Eingaben als auch für Liveeingabedaten.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Debuggen des benutzerdefinierten Deserialisierers":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Streamingauftrag und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Durch das Löschen des Auftrags verhindern Sie, dass Kosten für die vom Auftrag verbrauchten Streamingeinheiten anfallen. Wenn Sie den Auftrag in Zukunft verwenden möchten, können Sie ihn beenden und später bei Bedarf neu starten. Wenn Sie diesen Auftrag nicht mehr verwenden möchten, löschen Sie alle Ressourcen, die im Rahmen dieses Tutorials erstellt wurden, mithilfe der folgenden Schritte:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen** und dann auf den Namen der erstellten Ressource.  

2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld den Namen der zu löschenden Ressource ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie einen benutzerdefinierten .NET-Deserialisierer für die Protokollpuffer-Eingabeserialisierung implementieren. Weitere Informationen zur Erstellung benutzerdefinierter Deserialisierer finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> * [Verwenden von .NET-Deserialisierern für Azure Stream Analytics-Aufträge](custom-deserializer-examples.md)
> * [Lokales Testen von Azure Stream Analytics-Aufträgen mit Liveeingabe unter Verwendung von Visual Studio Code](visual-studio-code-local-run-live-input.md)