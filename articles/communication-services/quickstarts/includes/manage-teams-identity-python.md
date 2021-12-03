---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 60c534a64bae703de30cd7aaddd1da31d27e750b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461152"
---
## <a name="set-up-prerequisites"></a>Einrichten erforderlicher Komponenten

- [Python](https://www.python.org/downloads/) 2.7 bzw. 3.6 oder höher.

## <a name="set-up"></a>Einrichtung

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

   ```console
   mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
   ```

1. Erstellen Sie mithilfe eines Text-Editors eine Datei mit dem Namen `exchange-communication-access-tokens.py` im Stammverzeichnis des Projekts, und fügen Sie die Struktur für das Programm hinzu (einschließlich einer einfachen Ausnahmebehandlung). In den folgenden Abschnitten wird dieser Datei der gesamte Quellcode für diese Schnellstartanleitung hinzugefügt.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `pip install` das Python-Paket des SDK für Identitäten von Azure Communication Services.

```console
pip install azure-communication-identity
pip install msal
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Schritt 1: Empfangen des Azure AD-Benutzertokens über die MSAL-Bibliothek

Der erste Schritt im Flow für den Tokenaustausch besteht darin, mithilfe von [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md) ein Token für Ihre Teams-Benutzer abzurufen.

```python
from msal.application import PublicClientApplication

client_id = "<contoso_application_id>"
tenant_id = "<contoso_tenant_id>"
authority = "https://login.microsoftonline.com/%s" % tenant_id

app = PublicClientApplication(client_id, authority=authority)

scope = [ "https://auth.msft.communication.azure.com/VoIP" ]
teams_token_result = app.acquire_token_interactive(scope)
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Schritt 2: Initialisieren von CommunicationIdentityClient

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string).

Fügen Sie diesen Code im `try`-Block hinzu:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Schritt 3: Austauschen des Azure AD-Benutzertokens gegen das Teams-Zugriffstoken

Verwenden Sie die Methode `get_token_for_teams_user`, um ein Zugriffstoken für den Teams-Benutzer auszustellen, das mit den Azure Communication Services SDKs verwendet werden kann.

```python
token_result = client.get_token_for_teams_user(teams_token_result['access_token'])
print("Token: " + token_result.token)
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie in einer Konsoleneingabeaufforderung zum Verzeichnis mit der Datei *exchange-teams-access-tokens.py*, und führen Sie anschließend den folgenden Befehl `python` aus, um die App auszuführen.

```console
python ./exchange-communication-access-tokens.py
```
