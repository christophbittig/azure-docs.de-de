---
title: 'Schnellstart: Verwenden von Azure App Configuration mit Python-Apps | Microsoft-Dokumentation'
description: In dieser Schnellanleitung erstellen Sie mit Azure App Configuration eine Python-App, um die Speicherung und Verwaltung von Anwendungseinstellungen getrennt von Ihrem Code zu zentralisieren.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 36b840cb851ef544d0956ff0dec969ce251855d4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748754"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Schnellstart: Erstellen einer Python-App mit Azure App Configuration

In dieser Schnellstartanleitung verwenden Sie Azure App Configuration zum Zentralisieren der Speicherung und Verwaltung von Anwendungseinstellungen mit der [Azure App Configuration-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- Python 2.7, 3.6 oder höher: Informationen zum Einrichten von Python unter Windows finden Sie in [dieser Dokumentation]( https://docs.microsoft.com/windows/python/).

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Konfigurations-Explorer** > **Erstellen** > **Schlüssel-Wert** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

8. Wählen Sie **Übernehmen**.

## <a name="setting-up-the-python-app"></a>Einrichten der Python-App

1. In diesem Tutorial erstellen Sie ein neues Verzeichnis für das Projekt mit dem Namen *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Wechseln Sie zum neu erstellten Verzeichnis *app-configuration-quickstart*.

    ```console
    cd app-configuration-quickstart
    ```

1. Installieren Sie die Azure App Configuration-Clientbibliothek mit dem Befehl `pip install`.

    ```console
    pip install azure-appconfiguration
    ```

1. Erstellen Sie im Verzeichnis *app-configuration-quickstart* eine neue Datei mit dem Namen *app-configuration-quickstart.py*, und fügen Sie ihr den folgenden Code hinzu:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Die Codeausschnitte in dieser Schnellstartanleitung dienen Ihnen als Hilfe bei den ersten Schritten mit der App Configuration-Clientbibliothek für Python. Für Ihre Anwendung sollten Sie auch berücksichtigen, dass Ausnahmen je nach Ihren Anforderungen verarbeitet werden. Weitere Informationen zur Behandlung von Ausnahmen finden Sie in unserer [Dokumentation zum Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Konfigurieren der App Configuration-Verbindungszeichenfolge

1. Legen Sie eine Umgebungsvariable mit dem Namen **AZURE_APP_CONFIG_CONNECTION_STRING** auf den Zugriffsschlüssel für Ihren App Configuration-Speicher fest. Führen Sie an der Befehlszeile den folgenden Befehl aus:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird. Geben Sie den Wert der Umgebungsvariablen aus, um zu überprüfen, ob er richtig festgelegt wurde.

## <a name="code-samples"></a>Codebeispiele

Anhand der Beispielcodeausschnitte in diesem Abschnitt wird veranschaulicht, wie Sie häufige Vorgänge mit der App Configuration-Clientbibliothek für Python durchführen. Fügen Sie diese Codeausschnitte dem `try`-Block in der Datei *app-configuration-quickstart.py* hinzu, die Sie weiter oben erstellt haben.

> [!NOTE]
> In der App Configuration-Clientbibliothek wird auf ein Schlüssel-Wert-Objekt als `ConfigurationSetting` verwiesen. Daher werden die **Schlüsselwerte** im App Configuration-Speicher als **Konfigurationseinstellungen** bezeichnet.

* [Herstellen einer Verbindung mit einem App Configuration-Speicher](#connect-to-an-app-configuration-store)
* [Abrufen einer Konfigurationseinstellung](#get-a-configuration-setting)
* [Hinzufügen einer Konfigurationseinstellung](#add-a-configuration-setting)
* [Abrufen einer Liste mit Konfigurationseinstellungen](#get-a-list-of-configuration-settings)
* [Sperren einer Konfigurationseinstellung](#lock-a-configuration-setting)
* [Entsperren einer Konfigurationseinstellung](#unlock-a-configuration-setting)
* [Aktualisieren einer Konfigurationseinstellung](#update-a-configuration-setting)
* [Löschen einer Konfigurationseinstellung](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App Configuration-Speicher

Mit dem folgenden Codeausschnitt wird eine Instanz von **AzureAppConfigurationClient** erstellt, indem die in Ihren Umgebungsvariablen gespeicherte Verbindungszeichenfolge verwendet wird.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Abrufen einer Konfigurationseinstellung

Mit dem folgenden Codeausschnitt wird eine Konfigurationseinstellung anhand des `key`-Namens abgerufen.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Hinzufügen einer Konfigurationseinstellung

Mit dem folgenden Codeausschnitt wird ein `ConfigurationSetting`-Objekt mit den Feldern `key` und `value` erstellt und die Methode `add_configuration_setting` aufgerufen. Diese Methode löst eine Ausnahme aus, wenn Sie versuchen, eine Konfigurationseinstellung hinzuzufügen, die in Ihrem Speicher bereits vorhanden ist. Wenn Sie die Nutzung dieser Ausnahme vermeiden möchten, können Sie stattdessen die Methode [set_configuration_setting](#update-a-configuration-setting) verwenden.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Abrufen einer Liste mit Konfigurationseinstellungen

Mit dem folgenden Codeausschnitt wird eine Liste mit Konfigurationseinstellungen abgerufen. Die Argumente `key_filter` und `label_filter` können bereitgestellt werden, um Schlüsselwerte nach `key` bzw. `label` zu filtern. Weitere Informationen zum Filtern finden Sie unter [Abfragen von Konfigurationseinstellungen](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Sperren einer Konfigurationseinstellung

Der Sperrstatus eines Schlüsselwerts in App Configuration wird mit dem Attribut `read_only` des Objekts `ConfigurationSetting` angegeben. Wenn `read_only` auf `True` festgelegt ist, ist diese Einstellung gesperrt. Die Methode `set_read_only` kann mit dem Argument `read_only=True` aufgerufen werden, um die Konfigurationseinstellung zu sperren.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Entsperren einer Konfigurationseinstellung

Wenn das Attribut `read_only` für eine Konfigurationseinstellung (`ConfigurationSetting`) auf `False` festgelegt ist, ist die Einstellung entsperrt. Die Methode `set_read_only` kann mit dem Argument `read_only=False` aufgerufen werden, um die Konfigurationseinstellung zu entsperren.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Aktualisieren einer Konfigurationseinstellung

Die Methode `set_configuration_setting` kann verwendet werden, um eine vorhandene Einstellung zu aktualisieren oder eine neue zu erstellen. Mit dem folgenden Codeausschnitt wird der Wert einer vorhandenen Konfigurationseinstellung geändert.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Löschen einer Konfigurationseinstellung

Mit dem folgenden Codeausschnitt wird eine Konfigurationseinstellung anhand des `key`-Namens gelöscht.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Ausführen der App

In dieser Schnellstartanleitung haben Sie eine Python-App erstellt, bei der die Azure App Configuration-Clientbibliothek zum Abrufen einer Konfigurationseinstellung verwendet wurde, die mit dem Azure-Portal erstellt wurde. Sie haben eine neue Einstellung hinzugefügt, eine Liste mit vorhandenen Einstellungen abgerufen und eine Einstellung gesperrt, entsperrt, aktualisiert und abschließend gelöscht.

An diesem Punkt sollte Ihre Datei *app-configuration-quickstart.py* den folgenden Code enthalten:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

Navigieren Sie im Konsolenfenster zu dem Verzeichnis, das die Datei *app-configuration-quickstart.py* enthält, und führen Sie den folgenden Python-Befehl aus, um die App auszuführen:

```console
python app-configuration-quickstart.py
```

Die folgende Ausgabe wird angezeigt:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen neuen App Configuration-Speicher erstellt und gelernt, wie Sie aus einer Python-App auf Schlüsselwerte zugreifen.

Weitere Codebeispiele finden Sie unter:

> [!div class="nextstepaction"]
> [Azure App Configuration-Clientbibliothek: Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
