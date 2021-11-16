---
title: include file
description: Datei einfügen
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/30/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 35319f125578143abc2518d8037e0050dd9a6041
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893220"
---
> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/relay-token-quickstart).

## <a name="prerequisites-for-python"></a>Voraussetzungen für Python

 Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 bzw. 3.6 oder höher.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../create-communication-resource.md)

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

   ```console
   mkdir access-tokens-quickstart && cd relay-tokens-quickstart
   ```

2. Erstellen Sie mithilfe eines Text-Editors eine Datei mit dem Namen **issue-relay-tokens.py** im Stammverzeichnis des Projekts, und fügen Sie die Struktur für das Programm hinzu (einschließlich einer einfachen Ausnahmebehandlung). In den folgenden Abschnitten wird dieser Datei der gesamte Quellcode für diese Schnellstartanleitung hinzugefügt.

   ```python
    import os
    from azure.communication.networktraversal import CommunicationRelayClient
    from azure.identity import DefaultAzureCredential
    from azure.communication.identity import CommunicationIdentityClient
    
    try:
      print("Azure Communication Services - Access Relay Configuration  Quickstart")
      # Quickstart code goes here
    except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `pip install` das Python-Paket des SDK für Identitäten von Azure Communication Services.

```console
pip install azure-communication-identity
pip install azure.communication.networktraversal
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit dem Zugriffsschlüssel und dem Endpunkt Ihrer Ressource. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string). 

Fügen Sie diesen Code im `try`-Block hinzu:

```python

# You can find your endpoint and access token from your resource in the Azure Portal
connection_str = "endpoint=ENDPOINT;accessKey=KEY"
endpoint = "https://<RESOURCE_NAME>.communication.azure.com"

# To use Azure Active Directory Authentication (DefaultAzureCredential) make sure to have
# AZURE_TENANT_ID, AZURE_CLIENT_ID and AZURE_CLIENT_SECRET as env variables.
# We also need Identity client to get a User Identifier
identity_client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
relay_client = CommunicationRelayClient(endpoint, DefaultAzureCredential())

#You can also authenticate using your connection string
identity_client = CommunicationIdentityClient.from_connection_string(self.connection_string)
relay_client = CommunicationRelayClient.from_connection_string(self.connection_string)
```

## <a name="create-a-user-from-identity"></a>Erstellen eines Benutzers auf der Grundlage einer Identität 

Von Azure Communication Services wird ein einfaches Identitätsverzeichnis gepflegt. Verwenden Sie die Methode `create_user`, um in dem Verzeichnis einen neuen Eintrag mit einer eindeutigen `Id` zu erstellen. Speichern Sie die empfangene Identität mit einer Zuordnung zu den Benutzern Ihrer Anwendung. Beispielsweise, indem Sie sie in der Datenbank des Anwendungsservers speichern. Die Identität ist später erforderlich, um Zugriffstoken auszustellen.

```python
user = identity_client.create_user()
```

## <a name="getting-the-relay-configuration"></a>Abrufen der Relaykonfiguration
Rufen Sie den Azure Communication-Tokendienst auf, um das Benutzerzugriffstoken gegen ein TURN-Diensttoken zu tauschen

```python
relay_configuration = relay_client.get_relay_configuration(user)

for iceServer in config.ice_servers:
    assert iceServer.username is not None
    print('Username: ' + iceServer.username)

    assert iceServer.credential is not None
    print('Credential: ' + iceServer.credential)
    
    assert iceServer.urls is not None
    for url in iceServer.urls:
        print('Url:' + url)
```     

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie in einer Konsoleneingabeaufforderung zum Verzeichnis mit der Datei *issue-relay-tokens.py*, und führen Sie anschließend den folgenden Befehl vom Typ `python` aus, um die App auszuführen.

```console
python ./issue-relay-tokens.py
```
