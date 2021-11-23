---
title: Datei einfügen
description: include file
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: c6fcc4c63b786af08c48450791a58b4f6f13a6ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529475"
---
Steigen Sie ein in Azure Communication Services, indem Sie das Python-SDK für SMS von Communication Services nutzen, um SMS-Nachrichten zu senden.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/send-sms-quickstart).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 bzw. 3.6 oder höher.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../../create-communication-resource.md)
- Eine für SMS-Nachrichten geeignete Telefonnummer. [Beziehen Sie eine Telefonnummer.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

- Führen Sie in einem Terminal- oder Befehlsfenster den Befehl `python --version` aus, um sich zu vergewissern, dass Python installiert ist.
- Melden Sie sich zum Anzeigen der Telefonnummern für Ihre Communication Services-Ressource beim [Azure-Portal](https://portal.azure.com/) an, suchen Sie nach Ihrer Communication Services-Ressource, und öffnen Sie im linken Navigationsbereich die Registerkarte **Telefonnummern**.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Erstellen Sie mithilfe eines Text-Editors eine Datei mit dem Namen **send-sms.py** im Stammverzeichnis des Projekts, und fügen Sie die Struktur für das Programm hinzu (einschließlich einer einfachen Ausnahmebehandlung). In den folgenden Abschnitten wird dieser Datei der gesamte Quellcode für diese Schnellstartanleitung hinzugefügt.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `pip install` das Python-Paket des SMS SDK von Azure Communication Services.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen dienen zur Behandlung einiger der wichtigsten Features des SMS SDK für Python von Azure Communication Services.

| name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Diese Klasse ist für alle SMS-Funktionen erforderlich. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Senden von SMS-Nachrichten.                                                                                                                 |
| SmsSendResult               | Diese Klasse enthält das Ergebnis des SMS-Diensts.                                          |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen SMS-Client (**SmsClient**) mit Ihrer Verbindungszeichenfolge. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../../create-communication-resource.md#store-your-connection-string).

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Der Einfachheit halber verwenden wir in dieser Schnellstartanleitung Verbindungszeichenfolgen, aber in Produktionsumgebungen sollten [Dienstprinzipale](../../../quickstarts/identity/service-principal.md) verwendet werden.

## <a name="send-a-11-sms-message"></a>Senden einer SMS: 1:1

Wenn Sie eine SMS an einen einzelnen Empfänger senden möchten, rufen Sie über **SmsClient** die Methode ```send``` mit der Telefonnummer eines einzelnen Empfängers auf. Sie können auch optionale Parameter übergeben, um anzugeben, ob der Zustellbericht aktiviert werden soll, und um benutzerdefinierte Tags festzulegen. Fügen Sie in **send-sms.py** am Ende des `try`-Blocks den folgenden Code hinzu:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Ersetzen Sie `<from-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Instanz zugeordnet ist, und ersetzen Sie `<to-phone-number>` durch die Telefonnummer, an die Sie eine Nachricht senden möchten.

> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten (z. B. +14255550123). Die Telefonnummer vom Typ **From** (Anrufertelefonnummer) kann auch ein kurzer Code sein (z. B. 23456).

## <a name="send-a-1n-sms-message"></a>Senden einer SMS: 1:N

Wenn Sie eine SMS an eine Empfängerliste senden möchten, rufen Sie über **SmsClient** die Methode ```send``` mit einer Listeder Telefonnummern der Empfänger auf. Sie können auch optionale Parameter übergeben, um anzugeben, ob der Zustellbericht aktiviert werden soll, und um benutzerdefinierte Tags festzulegen. Fügen Sie in **send-sms.py** am Ende des `try`-Blocks den folgenden Code hinzu:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Ersetzen Sie `<from-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Instanz zugeordnet ist, und ersetzen Sie `<to-phone-number-1>` `<to-phone-number-2>` durch Telefonnummern, an die Sie eine Nachricht senden möchten.

> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten (z. B. +14255550123). Die Telefonnummer vom Typ **From** (Anrufertelefonnummer) kann auch ein kurzer Code sein (z. B. 23456).

## <a name="optional-parameters"></a>Optionale Parameter

Der Parameter `enable_delivery_report` ist ein optionaler Parameter zum Konfigurieren von Zustellberichten. Dies ist in Szenarien hilfreich, in denen Ereignisse ausgegeben werden sollen, wenn SMS-Nachrichten zugestellt wurden. Informationen zum Konfigurieren von Zustellberichten für SMS-Nachrichten finden Sie in der Schnellstartanleitung [Behandeln von SMS-Ereignissen](../handle-sms-events.md).

Der Parameter `tag` ist ein optionaler Parameter zum Anwenden eines Tags auf den Zustellbericht.

## <a name="run-the-code"></a>Ausführen des Codes
Führen Sie die Anwendung mit dem Befehl `python` aus dem Anwendungsverzeichnis aus.

```console
python send-sms.py
```

Das komplette Python-Skript sollte in etwa wie folgt aussehen:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
