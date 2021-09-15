---
title: 'Referenz: Python-Server-SDK für den Azure Web PubSub-Dienst'
description: In dieser Referenz wird das Python-Server-SDK für den Azure Web PubSub-Dienst beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: c394258a49e73055f6848eed87dd15e75bd1c069
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115412"
---
# <a name="python-server-sdk-for-azure-web-pubsub-service"></a>Python-Server-SDK für den Azure Web PubSub-Dienst

Verwenden Sie die Bibliothek für:

- Das Versenden von Nachrichten an Hubs und Gruppen
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen
- Das Organisieren von Benutzern und Verbindungen in Gruppen
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

[Quellcode](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Paket (Pypi)][package] | [API-Referenzdokumentation](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Produktdokumentation][webpubsubservice_docs] |
[Beispiele][samples_ref]

## <a name="getting-started"></a>Erste Schritte

### <a name="installations-the-package"></a>Installieren des Pakets

```bash
python -m pip install azure-messaging-webpubsubservice
```

#### <a name="prerequisites"></a>Voraussetzungen

- Für die Verwendung dieses Pakets ist Python 2.7 bzw. 3.6 oder höher erforderlich.
- Sie benötigen ein [Azure-Abonnement][azure_sub] und eine [Azure Web PubSub-Dienstinstanz][webpubsubservice_docs], um dieses Paket zu verwenden.
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

### <a name="authenticating-the-client"></a>Authentifizieren des Clients

Für die Interaktion mit dem Azure Web PubSub-Dienst müssen Sie eine Instanz der [WebPubSubServiceClient][webpubsubservice_client_class]-Klasse erstellen. Für die Authentifizierung beim Dienst müssen Sie eine AzureKeyCredential-Instanz mit Endpunkt und Zugriffsschlüssel übergeben. Den Endpunkt und den Zugriffsschlüssel finden Sie im Azure-Portal.

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

### <a name="logging"></a>Protokollierung

Dieses SDK verwendet die Python-Standardprotokollierungsbibliothek.
Sie können die Protokollierung von Debuginformationen zur Ausgabe in stdout oder an einer beliebigen anderen Stelle konfigurieren.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

HTTP-Anforderungs- und Antwortdetails werden bei dieser Protokollierungskonfiguration in stdout gedruckt.

[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
