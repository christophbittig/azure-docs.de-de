---
title: Referenz - Python-Server-SDK für Azure Web PubSub
description: Diese Referenz beschreibt das Python-Server-SDK für den Azure Web PubSub-Dienst.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f28ab302aa6899e35c080821ccddf01d5e7cb76e
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578328"
---
# <a name="python-server-sdk-for-azure-web-pubsub"></a>Python-Server-SDK für Azure Web PubSub

Sie können diese Bibliothek verwenden, um:

- Das Versenden von Nachrichten an Hubs und Gruppen. 
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
- Das Organisieren von Benutzern und Verbindungen in Gruppen.
- Enge Verbindungen.
- Erteilen, Entziehen und Überprüfen von Berechtigungen für eine bestehende Verbindung.

[Quellcode](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Paket (Pypi)][package] | [API-Referenzdokumentation](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Produktdokumentation][webpubsubservice_docs] |
[Beispiele][samples_ref]

## <a name="get-started"></a>Erste Schritte

Installieren Sie das Paket wie folgt:

```bash
python -m pip install azure-messaging-webpubsubservice==1.0.0b1
```

### <a name="prerequisites"></a>Voraussetzungen

- Python 2.7 oder 3.6 oder höher ist erforderlich, um dieses Paket zu verwenden.
- Ein [Azure-Abonnement][azure_sub].
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

### <a name="authenticate-the-client"></a>Authentifizieren des Clients

Um mit dem Azure WebPubSub-Dienst zu interagieren, müssen Sie eine Instanz der Klasse [`WebPubSubServiceClient`][webpubsubservice_client_class] erstellen. Um sich gegenüber dem Dienst zu authentifizieren, müssen Sie eine "AzureKeyCredential"-Instanz mit Endpunkt und Zugangsschlüssel übergeben. Sie finden den Endpunkt und den Zugangsschlüssel im Azure-Portal.

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> client
<WebPubSubServiceClient endpoint:'<endpoint>'>
```

## <a name="examples"></a>Beispiele

### <a name="sending-a-request"></a>Senden einer Anforderung

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> from azure.messaging.webpubsubservice.rest import build_send_to_all_request
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> request = build_send_to_all_request('default', json={ 'Hello':  'webpubsub!' })
>>> request
<HttpRequest [POST], url: '/api/hubs/default/:send?api-version=2020-10-01'>
>>> response = client.send_request(request)
>>> response
<RequestsTransportResponse: 202 Accepted>
>>> response.status_code 
202
>>> with open('file.json', 'r') as f:
>>>    request = build_send_to_all_request('ahub', content=f, content_type='application/json')
>>>    response = client.send_request(request)
>>> print(response)
<RequestsTransportResponse: 202 Accepted>
```

## <a name="key-concepts"></a>Wichtige Begriffe

[!INCLUDE [Terms](includes/terms.md)]

## <a name="troubleshooting"></a>Problembehandlung

Dieses SDK verwendet die Standard-Protokollierungsbibliothek von Python. Sie können die Protokollierung so konfigurieren, dass Debugging-Informationen in den `stdout` oder an eine beliebige Stelle gedruckt werden.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Die Details der HTTP-Anforderung und -Antwort werden bei dieser Protokollierungskonfiguration nach `stdout` gedruckt.

[webpubsubservice_docs]: ./index.yml
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]