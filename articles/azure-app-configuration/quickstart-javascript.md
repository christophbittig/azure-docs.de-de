---
title: 'Schnellstart: Verwenden von Azure App Configuration mit JavaScript-Apps | Microsoft-Dokumentation'
description: In dieser Schnellanleitung erstellen Sie mit Azure App Configuration eine Node.js-App, um die Speicherung und Verwaltung von Anwendungseinstellungen getrennt von Ihrem Code zu zentralisieren.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: quickstart
ms.date: 07/12/2021
ms.author: drewbat
ms.openlocfilehash: 850771db454f854243ac9ba242d02ed1911f255c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785187"
---
# <a name="quickstart-create-a-javascript-app-with-azure-app-configuration"></a>Schnellstart: Erstellen einer JavaScript-App mit Azure App Configuration

In dieser Schnellstartanleitung verwenden Sie Azure App Configuration zum Zentralisieren der Speicherung und Verwaltung von Anwendungseinstellungen mit der [App Configuration-Clientbibliothek für JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/appconfiguration/app-configuration/README.md).

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [LTS-Versionen von Node.js.](https://nodejs.org/en/about/releases/) Informationen zum Installieren von Node.js direkt unter Windows oder mithilfe des Windows-Subsystems für Linux (WSL) finden Sie unter [Was ist NodeJS?](/windows/dev-environment/javascript/nodejs-overview).

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Konfigurations-Explorer** > **Erstellen** > **Schlüssel-Wert** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

8. Wählen Sie **Übernehmen**.

## <a name="setting-up-the-nodejs-app"></a>Einrichten der Node.js-App

1. In diesem Tutorial erstellen Sie ein neues Verzeichnis für das Projekt mit dem Namen *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Wechseln Sie zum neu erstellten Verzeichnis *app-configuration-quickstart*.

    ```console
    cd app-configuration-quickstart
    ```

1. Installieren Sie die Azure App Configuration-Clientbibliothek mit dem Befehl `npm install`.

    ```console
    npm install @azure/app-configuration
    ```

1. Erstellen Sie im Verzeichnis *app-configuration-quickstart* eine neue Datei mit dem Namen *app.js*, und fügen Sie ihr den folgenden Code hinzu:

   ```javascript
   const appConfig = require("@azure/app-configuration");
   ```

## <a name="configure-your-connection-string"></a>Konfigurieren der Verbindungszeichenfolge

1. Legen Sie eine Umgebungsvariable mit dem Namen **AZURE_APP_CONFIG_CONNECTION_STRING** auf den Zugriffsschlüssel für Ihren App Configuration-Speicher fest. Führen Sie an der Befehlszeile den folgenden Befehl aus:

    ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="command-line"></a>[Befehlszeile](#tab/command-line)

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="macos"></a>[macOS](#tab/macOS)
    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

    ---

2. Starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird. Geben Sie den Wert der Umgebungsvariablen aus, um zu überprüfen, ob er richtig festgelegt wurde.

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App Configuration-Speicher

Mit dem folgenden Codeausschnitt wird eine Instanz von **AppConfigurationClient** erstellt, indem die in Ihren Umgebungsvariablen gespeicherte Verbindungszeichenfolge verwendet wird.

```javascript
const connection_string = process.env.AZURE_APP_CONFIG_CONNECTION_STRING;
const client = new appConfig.AppConfigurationClient(connection_string);
```

## <a name="get-a-configuration-setting"></a>Abrufen einer Konfigurationseinstellung

Mit dem folgenden Codeausschnitt wird eine Konfigurationseinstellung anhand des `key`-Namens abgerufen. Der in diesem Beispiel gezeigte Schlüssel wurde in den vorherigen Schritten dieses Artikels erstellt.

```javascript
async function run() {
  
  let retrievedSetting = await client.getConfigurationSetting({
    key: "TestApp:Settings:Message"
  });

  console.log("Retrieved value:", retrievedSetting.value);
}

run().catch((err) => console.log("ERROR:", err));
```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Führen Sie den folgenden Befehl aus, um die Node.js-App auszuführen:

   ```powershell
   node app.js
   ```
1. An der Eingabeaufforderung sollte die folgende Ausgabe angezeigt werden:

   ```powershell
   Retrieved value: Data from Azure App Configuration
   ```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen neuen App Configuration-Speicher erstellt und gelernt, wie Sie aus einer Node.js-App auf Schlüsselwerte zugreifen.

Weitere Codebeispiele finden Sie unter:

> [!div class="nextstepaction"]
> [Azure App Configuration-Clientbibliothek: Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
