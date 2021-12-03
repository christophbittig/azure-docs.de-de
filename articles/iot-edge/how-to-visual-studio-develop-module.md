---
title: Entwickeln und Debuggen von Modulen in Visual Studio –Azure IoT Edge
description: Verwenden Sie Visual Studio mit Azure IoT-Tools, um ein C- oder C# IoT Edge-Modul zu entwickeln und es – entsprechend der Konfiguration durch ein Bereitstellungsmanifest – von Ihrem IoT Hub auf ein IoT-Gerät zu pushen.
services: iot-edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 5f546acb48a84ddddeb822601d9284818d2211fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423069"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Entwickeln und Debuggen von Modulen für Azure IoT Edge mithilfe von Visual Studio 2019

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

In diesem Artikel erfahren Sie, wie Sie Visual Studio 2019 zum Entwickeln und Debuggen von Azure IoT Edge-Modulen verwenden.

Die Azure IoT Edge-Toolserweiterung für Visual Studio bietet die folgenden Vorteile:

* Sie können IoT Edge-Lösungen und -Module auf Ihrem lokalen Entwicklungscomputer erstellen, bearbeiten, kompilieren, ausführen und debuggen.
* Sie können Ihre IoT Edge-Lösung über den Azure IoT Hub auf einem IoT Edge-Gerät bereitstellen.
* Sie können Ihre Azure IoT-Module in C oder C# programmieren und dabei von sämtlichen Vorteilen der Entwicklung mit Visual Studio profitieren.
* Sie können Ihre IoT Edge-Geräte und -Module in der Benutzeroberfläche verwalten.

In diesem Artikel erfahren Sie, wie Sie Ihre IoT Edge-Module mithilfe der Azure IoT Edge-Tools für Visual Studio 2019 entwickeln. Außerdem erfahren Sie, wie Sie Ihr Projekt auf Ihrem IoT Edge-Gerät bereitstellen. Derzeit bietet Visual Studio 2019 Unterstützung für Module, die in C und C# geschrieben wurden. Die unterstützten Gerätearchitekturen sind Windows X64 und Linux X64 oder ARM32. Weitere Informationen zu unterstützten Betriebssystemen, Sprachen und Architekturen finden Sie unter [Sprach- und Architekturunterstützung](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie einen Windows-Computer als Entwicklungscomputer verwenden. Auf Windows-Computern können Sie entweder Windows- oder Linux-Module entwickeln.

* Verwenden Sie zum Entwickeln von Modulen mit **Windows-Containern** einen Windows-Computer, auf dem die Version 1809/Build 17763 oder höher ausgeführt wird.
* Verwenden Sie zum Entwickeln von Modulen mit **Linux-Containern** einen Windows-Computer, der die [Anforderungen für Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) erfüllt.

Installieren Sie Visual Studio auf Ihrem Entwicklungscomputer. Schließen Sie die Workloads **Azure-Entwicklung** und **Desktopentwicklung mit C++** in Ihre Visual Studio 2019-Installation ein. Sie können [Visual Studio 2019 ändern](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true), um die erforderlichen Workloads hinzuzufügen.

Wenn Visual Studio 2019 bereit ist, benötigen Sie auch die folgenden Tools und Komponenten:

* Laden Sie die [Azure IoT Edge-Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) aus dem Visual Studio Marketplace herunter, und installieren Sie sie, um ein IoT Edge-Projekt in Visual Studio 2019 zu erstellen.

  > [!TIP]
  > Wenn Sie Visual Studio 2017 verwenden, laden Sie die [Azure IoT Edge-Tools für VS 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) aus dem Visual Studio Marketplace herunter, und installieren Sie sie.

* Laden Sie die [Docker Community Edition](https://docs.docker.com/install/) auf Ihren Entwicklungscomputer herunter, und installieren Sie sie, um Ihre Modulimages erstellen und ausführen zu können. Sie müssen Docker CE je nach Typ der Module, die Sie entwickeln, für die Ausführung im Linux- oder Windows-Containermodus festlegen.

* Richten Sie Ihre lokale Entwicklungsumgebung zum Debuggen, Ausführen und Testen Ihrer IoT Edge-Lösung ein, indem Sie das [Entwicklertool für Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) installieren. Installieren Sie [Python (3.5/3.6/3.7/3.8) und Pip](https://www.python.org/) und anschließend das Paket **iotedgehubdev**, indem Sie in Ihrem Terminal den folgenden Befehl ausführen. Stellen Sie sicher, dass Sie eine Version des Azure IoT EdgeHub-Entwicklertools von höher als 0.3.0 verwenden.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

* Installieren Sie den Vcpkg-Bibliotheks-Manager und anschließend das **azure-iot-sdk-c-Paket** für Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

* Erstellen Sie eine Instanz von [Azure Container Registry](../container-registry/index.yml) oder [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags), um Ihre Modulimages zu speichern.

  > [!TIP]
  > Sie können anstelle einer Cloudregistrierung auch eine lokale Docker-Registrierung für Prototyp- bzw. Testzwecke verwenden.

* Zum Testen Ihres Moduls auf einem Gerät benötigen Sie einen aktiven IoT Hub mit mindestens einem IoT Edge-Gerät. Um schnell ein IoT Edge-Gerät zum Testen zu erstellen, führen Sie die Schritte in der Schnellstartanleitung für [Linux](quickstart-linux.md) oder [Windows](quickstart.md) aus. Wenn Sie den IoT Edge-Daemon auf Ihrem Entwicklungscomputer ausführen, müssen Sie möglicherweise EdgeHub und EdgeAgent beenden, bevor Sie mit der Entwicklung in Visual Studio beginnen.

### <a name="check-your-tools-version"></a>Überprüfen Ihrer Tools-Version

1. Wählen Sie im Menü **Erweiterungen** die Option **Erweiterungen verwalten** aus. Erweitern Sie **Installiert > Tools**. Dort finden Sie **Azure IoT Edge-Tools für Visual Studio** und **Cloud-Explorer für Visual Studio**.

1. Notieren Sie sich die installierte Version. Sie können diese Version mit der neuesten Version in Visual Studio Marketplace vergleichen ([Cloud-Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)).

1. Wenn Ihre Version älter ist als die Versionen, die auf dem Visual Studio Marketplace zur Verfügung stehen, aktualisieren Sie Ihre Tools in Visual Studio, wie im folgenden Abschnitt gezeigt wird.

### <a name="update-your-tools"></a>Aktualisieren Ihrer Tools

1. Erweitern Sie im Fenster **Erweiterungen verwalten** die Option **Updates > Visual Studio Marketplace**, wählen Sie **Azure IoT Edge-Tools** oder **Cloud-Explorer für Visual Studio** und dann **Aktualisieren** aus.

1. Nachdem das Toolupdate heruntergeladen wurde, schließen Sie Visual Studio, damit die Tools mit dem VSIX-Installationsprogramm aktualisiert werden.

1. Wählen Sie im Installationsprogramm **OK** aus, um den Vorgang zu starten, und dann **Ändern**, um die Tools zu aktualisieren.

1. Nachdem das Update abgeschlossen ist, wählen Sie **Schließen** aus, und starten Sie Visual Studio neu.

## <a name="create-an-azure-iot-edge-project"></a>Erstellen eines Azure IoT Edge-Projekts

Die IoT Edge-Projektvorlage in Visual Studio erstellt eine Projektmappe, die auf IoT Edge-Geräten bereitgestellt werden kann. Sie erstellen zunächst eine Azure IoT Edge-Lösung und generieren anschließend das erste Modul in dieser Lösung. Jede IoT Edge-Lösung kann mehrere Module enthalten.

> [!TIP]
> Die von Visual Studio erstellte Struktur eines IoT Edge-Projekts ist nicht die gleiche wie in Visual Studio Code.

1. Erstellen Sie in Visual Studio ein neues Projekt.

1. Suchen Sie auf der Seite **Neues Projekt erstellen** nach **Azure IoT Edge**. Wählen Sie das Projekt aus, das der Plattform und Architektur für Ihr IoT Edge-Gerät entspricht, und klicken Sie auf **Weiter**.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/create-new-project.png" alt-text="Erstellen eines neuen Projekts":::

1. Geben Sie auf der Seite **Konfigurieren Ihres neuen Projekts** einen Namen und den Speicherort für Ihr Projekt ein, und wählen Sie dann **Erstellen** aus.

1. Wählen Sie im Fenster **Modul hinzufügen** den Typ des Moduls aus, das Sie entwickeln möchten. Sie können auch **Vorhandenes Modul** auswählen, um Ihrer Bereitstellung ein vorhandenes IoT Edge-Modul hinzuzufügen. Geben Sie den Modulnamen und das Modulimagerepository an.

   Visual Studio füllt die Repository-URL automatisch mit **localhost:5000/<Name Ihres Moduls\>** aus. Wenn Sie eine lokale Docker-Registrierung zum Testen verwenden, können Sie **localhost** nutzen. Ersetzen Sie **localhost:5000** durch den Anmeldeserver aus Ihren Registrierungseinstellungen, wenn Sie Azure Container Registry verwenden. Der Anmeldeserver sieht wie **_\<registry name\>_ .azurecr.io** aus. Das Endergebnis sollte wie **\<*registry name*\>.azurecr.io/ _\<module name\>_** aussehen.

   Wählen Sie **Hinzufügen** aus, um Ihr Modul dem Projekt hinzuzufügen.

   ![Anwendung und Modul hinzufügen](./media/how-to-visual-studio-develop-csharp-module/add-module.png)

Nun enthält Ihre Visual Studio-Projektmappe ein IoT Edge-Projekt und ein IoT Edge-Modul.

Der Modulordner enthält abhängig von der Sprache, die Sie ausgewählt haben, eine Datei für Ihren Modulcode mit dem Namen `program.cs` oder `main.c`. Dieser Ordner enthält auch eine Datei mit dem Namen `module.json`, die die Metadaten Ihres Moduls beschreibt. Verschiedene Docker-Dateien stellen die Informationen bereit, die zum Erstellen Ihres Moduls als Windows- oder Linux-Container erforderlich sind.

Der Projektordner enthält eine Liste aller Module, die in diesem Projekt enthalten sind. Derzeit sollte nur ein Modul angezeigt werden, aber Sie können weitere hinzufügen. Weitere Informationen zum Hinzufügen von Modulen zu einem Projekt finden Sie weiter unten in diesem Artikel im Abschnitt [Erstellen und Debuggen mehrerer Module](#build-and-debug-multiple-modules).

Der Projektordner enthält auch eine Datei mit dem Namen `deployment.template.json`. Diese Datei ist eine Vorlage eines IoT Edge-Bereitstellungsmanifests, das alle Module definiert, die auf einem Gerät ausgeführt werden, sowie die Art und Weise, wie sie miteinander kommunizieren. Weitere Informationen zu Bereitstellungsmanifesten finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md). Wenn Sie diese Bereitstellungsvorlage öffnen, sehen Sie, dass die beiden Runtimemodule **edgeAgent** und **edgeHub** zusammen mit dem benutzerdefinierten Modul enthalten sind, das Sie in diesem Visual Studio-Projekt erstellt haben. Ein viertes Modul mit dem Namen **SimulatedTemperatureSensor** ist ebenfalls enthalten. Dieses Standardmodul generiert simulierte Daten, mit denen Sie Ihre Module testen können, oder löschen, wenn dies nicht erforderlich ist. Der [Quellcode „SimulatedTemperatureSensor.csproj“](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor) veranschaulicht die Funktionsweise des simulierten Temperatursensors.

### <a name="set-iot-edge-runtime-version"></a>Festlegen der IoT Edge-Runtimeversion

Die IoT Edge-Erweiterung verwendet beim Erstellen Ihrer Bereitstellungsressourcen standardmäßig die neueste stabile Version der IoT Edge-Runtime. Zurzeit ist Version 1.2 die neueste stabile Version. Wenn Sie Module für Geräte entwickeln, auf denen die Version 1.1 mit langfristigem Support (Long-Term Support, LTS) oder die frühere Version 1.0 ausgeführt wird, aktualisieren Sie die IoT Edge-Runtimeversion in Visual Studio entsprechend.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Namen Ihres Projekts, und wählen Sie **IoT Edge-Runtimeversion festlegen** aus.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/set-iot-edge-runtime-version.png" alt-text="Klicken Sie mit der rechten Maustaste auf Ihren Projektnamen, und wählen Sie „IoT Edge-Runtimeversion festlegen“ aus.":::

1. Wählen Sie mithilfe des Dropdownmenüs die auf Ihren IoT Edge-Geräten ausgeführte Runtimeversion und dann **OK** aus, um Ihre Änderungen zu speichern.

1. Generieren Sie Ihr Bereitstellungsmanifest mit der neuen Runtimeversion neu. Klicken Sie mit der rechten Maustaste auf den Namen Ihres Projekts, und wählen Sie **Bereitstellung für IoT Edge generieren** aus.

## <a name="develop-your-module"></a>Entwickeln Ihres Moduls

Wenn Sie ein neues Modul hinzufügen, enthält es Standardcode, der sofort genutzt und auf einem Gerät bereitgestellt werden kann, sodass Sie mit dem Testen beginnen können, ohne Code zu berühren. Der Modulcode befindet sich im Modulordner in einer Datei mit dem Namen `Program.cs` (für C#) oder `main.c` (für C).

Die Standardlösung ist so aufgebaut, dass die simulierten Daten aus dem **SimulatedTemperatureSensor**-Modul an Ihr Modul weitergeleitet werden, das die Eingabe entgegennimmt und sie dann an IoT Hub sendet.

Wenn Sie bereit sind, die Modulvorlage mit Ihrem eigenen Code anzupassen, erstellen Sie mit den [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) Module, die die wesentlichen Anforderungen für IoT-Lösungen wie Sicherheit, Geräteverwaltung und Zuverlässigkeit berücksichtigen.

## <a name="set-up-the-iotedgehubdev-testing-tool"></a>Einrichten des iotedgehubdev-Testtools

Das IoT EdgeHub-Entwicklungstool stellt eine lokale Entwicklungs- und Debugumgebung bereit. Das Tool ermöglicht das Starten von IoT Edge-Modulen ohne die IoT Edge-Runtime, so dass Sie IoT Edge-Module und -Projektmappen lokal erstellen, entwickeln, testen und debuggen können. Sie brauchen keine Images per Push in eine Containerregistrierung hochzuladen und auf einem Gerät bereitzustellen, um sie zu testen.

Weitere Informationen finden Sie unter [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) (Azure IoT EdgeHub-Entwicklungstool).

Um das Tool zu initialisieren, geben Sie eine IoT Edge-Geräteverbindungszeichenfolge aus IoT Hub an.

1. Rufen Sie die Verbindungszeichenfolge eines IoT Edge-Geräts aus dem Azure-Portal, der Azure CLI oder dem Visual Studio Cloud-Explorer ab. 

1. Wählen Sie im Menü **Tools** die Option **Azure IoT Edge-Tools** > **IoT Edge Simulator einrichten** aus.

1. Fügen Sie die Verbindungszeichenfolge ein, und klicken Sie auf **OK**.

> [!NOTE]
> Sie müssen diese Schritte auf Ihrem Entwicklungscomputer nur einmal ausführen, weil die Ergebnisse auf alle nachfolgenden Azure IoT Edge-Lösungen automatisch angewendet werden. Diese Schritte können erneut ausgeführt werden, wenn Sie in eine andere Verbindungszeichenfolge ändern müssen.

## <a name="build-and-debug-a-single-module"></a>Erstellen und Debuggen eines einzelnen Moduls

In der Regel möchten Sie jedes Modul testen und debuggen, bevor Sie es in einer Gesamtlösung mit mehreren Modulen ausführen.

>[!TIP]
>Stellen Sie sicher, dass Sie in den richtigen Docker-Containermodus gewechselt sind. Je nach dem, welchen Typ von IoT Edge-Modul Sie entwickeln, ist dies entweder der Linux-Containermodus oder der Windows-Containermodus. Im Docker-Desktopmenü können Sie zwischen den zwei Typen von Modi wechseln. Wählen Sie **Switch to Windows containers** (Zu Windows-Containern wechseln) aus, um Windows-Container zu verwenden, oder wählen Sie **Switch to Linux containers** aus, um Linux-Container zu verwenden. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Modulordner, und wählen Sie im Menü **Als Startprojekt festlegen** aus.

   ![Startprojekt festlegen](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Drücken Sie **F5,** oder klicken Sie auf der Symbolleiste auf die Schaltfläche „Ausführen“, um das Modul auszuführen. Es kann 10&ndash;20 Sekunden dauern, wenn Sie dies zum ersten Mal durchführen.

   ![Modul ausführen](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Wenn das Modul erfolgreich initialisiert wurde, sollte eine .NET Core-Konsolenanwendung gestartet werden.

1. Legen Sie einen Haltepunkt fest, um das Modul zu überprüfen.

   * Legen Sie bei der Entwicklung in C# in **Program.cs** einen Haltepunkt in der `PipeMessage()`-Funktion fest.
   * Wenn Sie C verwenden, legen Sie in **main.c** einen Haltepunkt in der `InputQueue1Callback()`-Funktion fest.

1. Testen Sie das Modul, indem Sie durch Ausführen des folgenden Befehls in einer **Git Bash**- oder **WSL Bash**-Shell eine Nachricht senden. (Den `curl`-Befehl können Sie nicht aus einer PowerShell oder Eingabeaufforderung ausführen.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Debuggen eines Einzelmoduls](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

   Es sollte ein Breakpoint ausgelöst werden. Sie können Variablen in Visual Studio im Fenster **Lokal** ansehen.

   > [!TIP]
   > Sie können auch [PostMan](https://www.getpostman.com/) oder andere API-Tools (anstelle von `curl`) zum Senden von Nachrichten verwenden.

1. Drücken Sie zum Beenden des Debuggen auf **STRG + F5** oder klicken Sie auf die Schaltfläche „Beenden“.

## <a name="build-and-debug-multiple-modules"></a>Erstellen und Debuggen mehrerer Module

Nachdem Sie ein Einzelmodul entwickelt haben, möchten Sie jetzt vielleicht eine Gesamtlösung mit mehreren Modulen ausführen und debuggen.

1. Fügen Sie im **Projektmappen-Explorer** der Projektmappe ein zweites Modul hinzu, indem Sie mit der rechten Maustaste auf den Projektordner klicken. Wählen Sie im Menü **Hinzufügen** > **Neues IoT Edge-Modul** aus.

   ![Hinzufügen eines neuen Moduls zu einem vorhandenen IoT Edge-Projekt](./media/how-to-visual-studio-develop-csharp-module/add-new-module.png)

1. Öffnen Sie die Datei `deployment.template.json`. Dann sehen Sie, dass das neue Modul im Abschnitt **modules** hinzugefügt wurde. Dem Abschnitt **routes** wurde auch eine neue Route hinzugefügt, um Nachrichten aus dem neuen Modul an IoT Hub zu senden. Wenn Sie Daten vom simulierten Temperatursensor an das neue Modul senden möchten, fügen Sie eine weitere Route wie im folgenden Beispiel hinzu: 

    ```json
   "sensorTo<NewModuleName>": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/<NewModuleName>/inputs/input1\")"
    ```

1. Klicken Sie mit der rechten Maustaste auf den Projektordner, und wählen Sie im Kontextmenü **Als Startprojekt festlegen** aus.

1. Erstellen Sie Ihre Breakpoints, und drücken Sie **F5**, um mehrere Module gleichzeitig auszuführen und zu debuggen. Daraufhin sollten mehrere Fenster der .NET Core-Konsolen-App angezeigt werden, wobei jedes Fenster ein anderes Modul darstellt.

   ![Debuggen mehrerer Module](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Drücken Sie zum Beenden des Debuggens **STRG+F5**, oder wählen Sie die Schaltfläche „Beenden“ aus.

## <a name="build-and-push-images"></a>Erstellen und Übermitteln des Images mithilfe von Push

1. Stellen Sie sicher, dass das IoT Edge-Projekt und nicht eines der einzelnen Module das Startprojekt ist. Wählen Sie entweder **Debuggen** oder **Version** als Konfiguration zum Kompilieren Ihrer Modulimages aus.

    > [!NOTE]
    > Bei der Auswahl von **Debuggen** verwendet Visual Studio `Dockerfile.(amd64|windows-amd64).debug` zum Erstellen von Docker-Images. Dies bindet den .NET Core-Befehlszeilendebugger VSDBG beim Erstellen in Ihr Containerimage ein. Wir empfehlen Ihnen, für produktionsbereite IoT Edge-Module die Konfiguration **Version** zu verwenden, die `Dockerfile.(amd64|windows-amd64)` ohne VSDBG verwendet.

1. Wenn Sie eine private Registrierung wie Azure Container Registry (ACR) verwenden, melden Sie sich dort mit dem folgenden Docker-Befehl an.  Sie erhalten den Benutzernamen und das Kennwort über die Seite **Zugriffsschlüssel** der Registrierung im Azure-Portal. Wenn Sie eine lokale Registrierung verwenden, können Sie [eine lokale Registrierung ausführen](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Wenn Sie eine private Registrierung wie Azure Container Registry verwenden, müssen Sie Ihre Anmeldeinformationen für die Registrierung den Laufzeiteinstellungen hinzufügen, die in der Datei `deployment.template.json` enthalten sind. Ersetzen Sie die Platzhalter durch Ihren tatsächlichen Benutzernamen, das Kennwort und den Registrierungsnamen des ACR-Administrators.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

   >[!NOTE]
   >In diesem Artikel werden die Administratoranmeldeinformationen für die Azure Container Registry verwendet, die für Entwicklungs- und Testszenarien geeignet sind. Wenn Sie für die Produktionsszenarien bereit sind, empfehlen wir Ihnen, eine Authentifizierungsoption mit den geringstmöglichen Rechten, z. B. Dienstprinzipale, auszuwählen. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Ihre Containerregistrierung](production-checklist.md#manage-access-to-your-container-registry).

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektordner, und wählen Sie **IoT Edge-Module erstellen und pushen** aus, um das Docker-Image für jedes Modul zu kompilieren und zu pushen.

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

Im Schnellstartartikel, anhand dessen Sie Ihr IoT Edge-Gerät eingerichtet haben, haben Sie ein Modul über das Azure-Portal bereitgestellt. Sie können Module auch mit dem Cloud-Explorer für Visual Studio bereitstellen. Weil Sie ein Bereitstellungsmanifest für Ihr Szenario und die Datei `deployment.json` schon vorbereitet haben, müssen Sie lediglich ein Gerät auswählen, um die Bereitstellung zu empfangen.

1. Öffnen Sie den **Cloud-Explorer** durch Klicken auf **Ansicht** > **Cloud-Explorer**. Vergewissern Sie sich, dass Sie bei Visual Studio 2019 angemeldet sind.

1. Erweitern Sie in **Cloud-Explorer** Ihr Abonnement, suchen Sie Ihren Azure IoT Hub und das Azure IoT Edge-Gerät, das Sie bereitstellen möchten.

1. Klicken Sie mit der rechten Maustaste auf das IoT Edge Gerät, um eine Bereitstellung dafür zu erstellen. Navigieren Sie zu dem für Ihre Plattform konfigurierten Bereitstellungsmanifest im Ordner **config** der Visual Studio-Projektmappe, z. B. `deployment.arm32v7.json`.

1. Klicken Sie auf die Schaltfläche „Aktualisieren“, um sich anzusehen, wie die neuen Module zusammen mit dem **SimulatedTemperatureSensor**-Modul sowie **$edgeAgent** und **$edgeHub** ausgeführt werden.

## <a name="view-generated-data"></a>Anzeigen generierter Daten

1. Um die D2C-Nachricht für ein bestimmtes IoT Edge-Gerät zu überwachen, wählen Sie dieses im IoT-Hub im **Cloud-Explorer** aus, und klicken Sie dann im Fenster **Aktion** auf **Überwachen des integrierten Ereignisendpunkts starten**.

1. Um die Überwachung von Daten zu beenden, wählen Sie im Fenster **Aktion** die Option **Überwachen des integrierten Ereignisendpunkts beenden** aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwickeln von benutzerdefinierten Modulen für Ihre IoT Edge-Geräte finden Sie unter [Verstehen und Verwenden von Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
