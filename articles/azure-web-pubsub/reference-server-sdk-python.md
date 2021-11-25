---
title: Referenz - Python-Server-SDK für Azure Web PubSub
description: Diese Referenz beschreibt das Python-Server-SDK für den Azure Web PubSub-Dienst.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 256dbd5e988f95f8e4df7ea6fd1c83b88bc45d46
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487430"
---
# <a name="azure-web-pubsub-service-client-library-for-python"></a>Azure Web PubSub Service Client-Bibliothek für Python

[Azure Web PubSub Service](https://aka.ms/awps/doc) ist ein von Azure verwalteter Dienst, der Entwicklern hilft, Webanwendungen mit Echtzeitfunktionen und Publish-Subscribe-Muster zu erstellen. Jedes Szenario, das Echtzeit-Publish-Subscribe-Messaging zwischen Server und Clients oder zwischen Clients erfordert, kann den Azure Web PubSub-Dienst nutzen. Herkömmliche Echtzeitfunktionen, die häufig ein Polling vom Server oder das Senden von HTTP-Anfragen erfordern, können ebenfalls den Azure Web PubSub-Dienst nutzen.

Sie können diese Bibliothek in Ihrer App serverseitig verwenden, um die WebSocket-Client-Verbindungen zu verwalten, wie im folgenden Diagramm dargestellt:

![Das Überlaufdiagramm zeigt den Überlauf bei Verwendung der Service-Client-Bibliothek.](media/sdk-reference/service-client-overflow.png)

Verwenden Sie diese Bibliothek, um:
- Das Versenden von Nachrichten an Hubs und Gruppen.
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
- Das Organisieren von Benutzern und Verbindungen in Gruppen.
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

[Quellcode](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [Paket (Pypi)][package] | [API-Referenzdokumentation](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [Produktdokumentation][webpubsubservice_docs]

## <a name="_disclaimer_"></a>_Haftungsausschluss_

_Die Unterstützung der Azure SDK Python-Pakete für Python 2.7 endet am 01. Januar 2022. Weitere Informationen und Fragen finden Sie unter https://github.com/Azure/azure-sdk-for-python/issues/20691_

## <a name="getting-started"></a>Erste Schritte

### <a name="prerequisites"></a>Voraussetzungen

- Für die Verwendung dieses Pakets ist Python 2.7 bzw. 3.6 oder höher erforderlich.
- Sie benötigen ein [Azure-Abonnement][azure_sub] und eine [Azure WebPubSub-Service-Instanz][webpubsubservice_docs], um dieses Paket zu nutzen.
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

### <a name="1-install-the-package"></a>1. Installieren Sie das Paket

```bash
python -m pip install azure-messaging-webpubsubservice
```

### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Erstellen und Authentifizieren Sie einen WebPubSubServiceClient

Sie können die `WebPubSubServiceClient` mittels [Verbindungszeichenfolge][connection_string] authentifizieren:

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string(connection_string='<connection_string>', hub='hub')
```

Oder Sie verwenden den Endpunkt des Dienstes und den Zugangsschlüssel:

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential

>>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=AzureKeyCredential("<access_key>"))
```

Oder Sie verwenden [Azure Active Directory][aad_doc]:
1. [pip][pip] installieren [`azure-identity`][azure_identity_pip]
2. Folgen Sie dem Dokument, um [die AAD-Authentifizierung für Ihre Webpubsub-Ressource zu aktivieren][aad_doc]
3. Code aktualisieren, um [DefaultAzureCredential][default_azure_credential] zu verwenden

    ```python
    >>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
    >>> from azure.identity import DefaultAzureCredential
    >>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=DefaultAzureCredential())
    ```

## <a name="key-concepts"></a>Wichtige Begriffe

### <a name="connection"></a>Verbindung

Eine Verbindung, auch als Client oder Client-Verbindung bezeichnet, stellt eine einzelne WebSocket-Verbindung dar, die mit dem Web-PubSub-Dienst verbunden ist. Wenn die Verbindung erfolgreich hergestellt wurde, wird ihr vom Web PubSub-Dienst eine eindeutige Verbindungs-ID zugewiesen.

### <a name="hub"></a>Hub

Ein Hub ist ein logisches Konzept für eine Reihe von Client-Verbindungen. In der Regel wird jeweils ein einzelner Hub für einen einzelnen Zweck verwendet – beispielsweise ein Chathub oder ein Benachrichtigungshub. Wenn eine Client-Verbindung erstellt wird, stellt sie eine Verbindung zu einem Hub her, und während ihrer Lebensdauer gehört sie zu diesem Hub. Von verschiedenen Anwendungen können unterschiedliche Hubnamen verwendet werden, um gemeinsam einen einzelnen Azure Web PubSub-Dienst zu nutzen.

### <a name="group"></a>Group

Eine Gruppe ist eine Teilmenge von Verbindungen zum Hub. Sie können einer Gruppe eine Clientverbindung hinzufügen und sie jederzeit wieder aus der Gruppe entfernen. Beispiel: Wenn ein Client einem Chatroom beitritt oder wenn ein Client den Chatroom verlässt, kann dieser Chatroom als Gruppe betrachtet werden. Ein Client kann mehreren Gruppen beitreten, und eine Gruppe kann mehrere Clients enthalten.

### <a name="user"></a>Benutzer

Die Verbindungen zu Web PubSub können einem einzigen Benutzer gehören. Ein Benutzer kann über mehrere Verbindungen verfügen, etwa, wenn ein einzelner Benutzer über mehrere Geräte oder mehrere Browsertabs verbunden ist.

### <a name="message"></a>`Message`

Wenn der Client verbunden ist, kann er über die WebSocket-Verbindung Nachrichten an die vorgelagerte Anwendung senden oder Nachrichten von der vorgelagerten Anwendung empfangen.

## <a name="examples"></a>Beispiele

### <a name="broadcast-messages-in-json-format"></a>Senden von Nachrichten im JSON-Format

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = {
        'from': 'user1',
        'data': 'Hello world'
    })
```

Der WebSocket-Client empfängt JSON serialisierten Text: `{"from": "user1", "data": "Hello world"}`.

### <a name="broadcast-messages-in-plain-text-format"></a>Senden von Nachrichten im Klartextformat

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = 'Hello world', content_type='text/plain')
```

Der WebSocket-Client empfängt Text: `Hello world`.

### <a name="broadcast-messages-in-binary-format"></a>Senden von Nachrichten im Binärformat

```python
>>> import io
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub')
>>> service.send_to_all(message=io.StringIO('Hello World'), content_type='application/octet-stream')
```
Der WebSocket-Client empfängt binären Text: `b'Hello world'`.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="logging"></a>Protokollierung

Dieses SDK verwendet die Python-Standardprotokollierungsbibliothek.
Sie können die Protokollierung so konfigurieren, dass Debugging-Informationen auf stdout oder an einem beliebigen Ort ausgegeben werden.

```python
import sys
import logging
from azure.identity import DefaultAzureCredential
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "<endpoint>"
credential = DefaultAzureCredential()

# This WebPubSubServiceClient will log detailed information about its HTTP sessions, at DEBUG level
service = WebPubSubServiceClient(endpoint=endpoint, hub='hub', credential=credential, logging_enable=True)
```

In ähnlicher Weise kann `logging_enable` die detaillierte Protokollierung für einen einzelnen Aufruf aktivieren, auch wenn sie für den WebPubSubServiceClient nicht aktiviert ist:

```python
result = service.send_to_all(..., logging_enable=True)
```

HTTP-Anforderungs- und Antwortdetails werden bei dieser Protokollierungskonfiguration in stdout gedruckt.

## <a name="next-steps"></a>Nächste Schritte

Prüfen Sie [mehr Beispiele hier][samples].

## <a name="contributing"></a>Mitwirken

Beiträge und Vorschläge für dieses Projekt sind willkommen. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution.
Ausführliche Informationen finden Sie unter https://cla.microsoft.com.

Wenn Sie einen Pull Request (PR) übermitteln, überprüft ein CLA-Bot automatisch, ob Sie eine Lizenzvereinbarung bereitstellen und den PR entsprechend ergänzen müssen (z.B. mit einer Bezeichnung oder einem Kommentar). Führen Sie einfach die Anweisungen des Bots aus. Sie müssen dies nur einmal für alle Repositorys ausführen, die unsere CLA verwenden.

Für dieses Projekt gelten die Microsoft-Verhaltensregeln für Open Source ([Microsoft Open Source Code of Conduct][code_of_conduct]). Weitere Informationen finden Sie in den FAQ zum Verhaltenskodex, oder wenden Sie sich mit weiteren Fragen oder Kommentaren an opencode@microsoft.com.

<!-- LINKS -->
[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[cla]: https://cla.microsoft.com
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[azure_identity_credentials]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#credentials
[azure_identity_pip]: https://pypi.org/project/azure-identity/
[default_azure_credential]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#defaultazurecredential
[pip]: https://pypi.org/project/pip/
[enable_aad]: howto-develop-create-instance.md
[api_key]: howto-websocket-connect.md#authorization
[connection_string]: howto-websocket-connect.md#authorization
[azure_portal]: howto-develop-create-instance.md
[azure-key-credential]: https://aka.ms/azsdk-python-core-azurekeycredential
[aad_doc]: howto-authorize-from-application.md
[samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice/samples
