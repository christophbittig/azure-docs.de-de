---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/13/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: ea706f98e7da8295305fd0a7cdccfd841e1ce3f3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520867"
---
> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/access-tokens-quickstart).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 bzw. 3.6 oder höher.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../create-communication-resource.md)

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Erstellen Sie mithilfe eines Text-Editors eine Datei mit dem Namen **issue-access-tokens.py** im Stammverzeichnis des Projekts, und fügen Sie die Struktur für das Programm hinzu (einschließlich einer einfachen Ausnahmebehandlung). In den folgenden Abschnitten wird dieser Datei der gesamte Quellcode für diese Schnellstartanleitung hinzugefügt.

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
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string).

Fügen Sie diesen Code im `try`-Block hinzu:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Wenn Sie alternativ eine Azure Active Directory(AD)-Anwendung eingerichtet haben, finden Sie weitere Informationen unter [Verwenden von Dienstprinzipals](../identity/service-principal.md). Sie können sich auch bei AD authentifizieren.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Von Azure Communication Services wird ein einfaches Identitätsverzeichnis gepflegt. Verwenden Sie die Methode `create_user`, um in dem Verzeichnis einen neuen Eintrag mit einer eindeutigen `Id` zu erstellen. Speichern Sie die empfangene Identität mit einer Zuordnung zu den Benutzern Ihrer Anwendung. Beispielsweise, indem Sie sie in der Datenbank des Anwendungsservers speichern. Die Identität ist später erforderlich, um Zugriffstoken auszustellen.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.properties['id'])
```

## <a name="issue-access-tokens"></a>Ausstellen von Zugriffstoken

Verwenden Sie die Methode `get_token`, um ein Zugriffstoken für eine bereits vorhandene Communication Services-Identität auszustellen. Der Parameter `scopes` definiert einen Satz primitiver Elemente, die dieses Zugriffstoken autorisieren. Weitere Informationen finden Sie in der [Liste der unterstützten Aktionen](../../concepts/authentication.md). Eine neue Instanz des Parameters `CommunicationUserIdentifier` kann basierend auf der Zeichenfolgendarstellung der Azure Communication Service-Identität generiert werden.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Zugriffstoken sind kurzlebige Anmeldeinformationen, die erneut ausgestellt werden müssen. Wenn dies nicht der Fall ist, kann die Benutzerumgebung Ihrer Anwendung unterbrochen werden. Die Antworteigenschaft `expires_on` gibt die Lebensdauer des Zugriffstokens an.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Erstellen einer Identität und Ausstellen eines Zugriffstokens innerhalb derselben Anforderung

Verwenden Sie die `create_user_and_token`-Methode, um eine Communication Services-Identität zu erstellen und ein Zugriffstoken dafür auszustellen. Der Parameter `scopes` definiert einen Satz primitiver Elemente, die dieses Zugriffstoken autorisieren. Weitere Informationen finden Sie in der [Liste der unterstützten Aktionen](../../concepts/authentication.md).

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0]
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity.properties['id'])
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Zugriffstoken für die Aktualisierung

Verwenden Sie zum Aktualisieren eines Zugriffstokens das `CommunicationUserIdentifier`-Objekt für die erneute Ausstellung:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token(identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Widerrufen von Zugriffstoken

In einigen Fällen können Sie Zugriffstoken explizit widerrufen. Beispielsweise dann, wenn der Benutzer einer Anwendung das Kennwort ändert, das für die Authentifizierung beim Dienst verwendet wird. Die Methode `revoke_tokens` erklärt alle aktiven Zugriffstoken für ungültig, die für die Identität ausgestellt wurden.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.properties['id'])
```

## <a name="delete-an-identity"></a>Löschen einer Identität

Wird eine Identität gelöscht, werden alle aktiven Zugriffstoken widerrufen, und für die Identität können keine Zugriffstoken mehr ausgestellt werden. Außerdem werden alle gespeicherten Inhalte entfernt, die der Identität zugeordnet sind.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.properties['id'])
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie in einer Konsoleneingabeaufforderung zum Verzeichnis mit der Datei *issue-access-tokens.py*, und führen Sie anschließend den folgenden Befehl vom Typ `python` aus, um die App auszuführen.

```console
python ./issue-access-tokens.py
```