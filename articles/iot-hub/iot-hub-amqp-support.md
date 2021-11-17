---
title: Grundlegendes zur Azure IoT Hub AMQP-Unterstützung | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Unterstützung für Geräte, die unter Verwendung des Protokolls AMQP eine Verbindung mit einem geräte- und dienstseitigen IoT Hub-Endpunkt herstellen. Enthält Informationen zur integrierten AMQP-Unterstützung der Azure IoT-Geräte-SDKs.'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: lizross
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ccdf206c9ccd9d91b125af658bf03d3ec40c4b02
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549981"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Kommunikation mit Ihrem IoT-Hub mithilfe des Protokolls AMQP

IoT Hub unterstützt die [OASIS AMQP-Version 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) (Advanced Message Queuing Protocol), um eine Vielzahl von Funktionen über geräte- und dienstseitige Endpunkte bereitzustellen. Dieses Dokument beschreibt die Verwendung des AMQP-Clients, um eine Verbindung mit einem IoT-Hub zur Verwendung der IoT Hub-Funktionalität herzustellen.

## <a name="service-client"></a>Dienstclient

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Herstellen der Verbindung mit und Authentifizierung bei einem IoT-Hub (Dienstclient)

Ein Client kann zum Herstellen der Verbindung mit einem IoT-Hub mithilfe von AMQP die Authentifizierung mit [Claims Based Security (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) – auf Ansprüchen basierende Sicherheit – oder [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) verwenden.

Für den Dienstclient sind die folgenden Informationen erforderlich:

| Information | Wert |
|-------------|--------------|
| Hostname des IoT-Hubs | `<iot-hub-name>.azure-devices.net` |
| Schlüsselname | `service` |
| Zugriffsschüssel | Ein primärer oder sekundärer dem Dienst zugeordneter Schlüssel |
| Shared Access Signature (SAS) | Eine kurzlebige Shared Access Signature (SAS) im folgenden Format: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Informationen zum Abrufen des Codes zum Generieren dieser Signatur finden Sie unter [Verwalten des Zugriffs auf IoT Hub](./iot-hub-dev-guide-sas.md#security-token-structure).

Der folgende Codeausschnitt verwendet die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) zum Herstellen der Verbindung mit IoT Hub über einen Senderlink.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Aufrufen von Cloud-zu-Gerät-Nachrichten (Dienstclient)

Weitere Informationen zum Austausch von Cloud-zu-Gerät-Nachrichten zwischen dem Dienst und dem IoT-Hub sowie zwischen dem Gerät und dem IoT-Hub finden Sie unter [Senden von Cloud-zu-Gerät-Nachrichten von Ihrem IoT-Hub](iot-hub-devguide-messages-c2d.md). Der Dienstclient verwendet die beiden Links, um Nachrichten zu senden und Feedback für zuvor von Geräten gesendete Nachrichten zu empfangen (siehe die folgende Tabelle):

| Erstellt von | Verknüpfungstyp | Verknüpfungspfad | BESCHREIBUNG |
|------------|-----------|-----------|-------------|
| Dienst | Senderlink | `/messages/devicebound` | Cloud-zu-Gerät-Nachrichten mit Geräten als Ziel werden vom Dienst an diesen Link gesendet. Die `To`-Eigenschaft von Nachrichten, die über diesen Link gesendet werden, wird auf den Linkpfad des Empfängers des Zielgeräts festgelegt: `/devices/<deviceID>/messages/devicebound`. |
| Dienst | Empfängerlink | `/messages/serviceBound/feedback` | Feedbacknachrichten zu Abschluss, Ablehnung und Abbruch von Geräten, die über diesen Link vom Dienst empfangen werden. Weitere Informationen zu Feedbacknachrichten finden Sie unter [Senden von Cloud-zu-Gerät-Nachrichten von einem IoT-Hub](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Der folgende Codeausschnitt veranschaulicht, wie eine Cloud-zu-Gerät-Nachricht erstellt und mithilfe der [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) an ein Gerät gesendet wird.

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Um Feedback zu erhalten, erstellt der Dienstclient einen Empfängerlink. Der folgende Codeausschnitt veranschaulicht, wie mit der [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) ein Link erstellt wird:

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Wie im vorherigen Code gezeigt, hat eine Cloud-zu-Gerät-Feedbacknachricht den Inhaltstyp *application/vnd.microsoft.iothub.feedback.json*. Sie können die Eigenschaften im JSON-Textkörper der Nachricht verwenden, um den Zustellungsstatus der ursprünglichen Nachricht herzuleiten:

* Der Schlüssel `statusCode` im Feedbacktext verfügt einen der folgenden Werte: *Success* (Erfolg), *Expired* (Abgelaufen), *DeliveryCountExceeded* (LieferanzahlÜberschritten), *Rejected* (Abgelehnt) oder *Purged* (Gelöscht).

* Der Schlüssel `deviceId` im Feedbacktext enthält die ID des Zielgeräts.

* Der Schlüssel `originalMessageId` im Feedbacktext enthält die ID der ursprünglichen vom Dienst gesendeten Cloud-zu-Gerät-Nachricht. Anhand dieses Zustellungsstatus können Sie Feedback mit Cloud-zu-Gerät-Nachrichten korrelieren.

### <a name="receive-telemetry-messages-service-client"></a>Empfangen von Telemetrienachrichten (Dienstclient)

Standardmäßig speichert Ihr IoT-Hub erfasste Telemetrienachrichten von Geräten in einem integrierten Event Hub. Ihr Dienstclient kann das Protokoll AMQP nutzen, um die gespeicherten Ereignisse zu empfangen.

Zu diesem Zweck muss der Dienstclient zuerst eine Verbindung mit dem IoT-Hub-Endpunkt herstellen und eine Adresse für die Umleitung zu den integrierten Event Hubs erhalten. Der Dienstclient nutzt die bereitgestellte Adresse dann, um eine Verbindung mit dem integrierten Event Hub herzustellen.

In jedem Schritt muss der Client die folgenden Informationen angeben:

* Gültige Dienstanmeldeinformationen (SAS-Token des Diensts).

* Einen ordnungsgemäß formatierten Pfad zur Consumergruppenpartition, aus der Nachrichten abgerufen werden sollen. Für eine Consumergruppe und Partitions-ID hat der Pfad das folgende Format: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (die Standardconsumergruppe ist `$Default`).

* Ein optionales Filterprädikat zum Bestimmen eines Startpunkts in der Partition. Dieses Prädikat kann in Form einer Sequenznummer, eines Offsets oder eines in die Warteschlange eingereihten Zeitstempels vorliegen.

Im folgenden Codeausschnitt wird die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) verwendet, um die vorherigen Schritte zu veranschaulichen:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Für eine bestimmte Geräte-ID wird vom IoT-Hub ein Hash der Geräte-ID verwendet, um zu ermitteln, in welcher Partition die Nachrichten gespeichert werden sollen. Im vorherigen Codeausschnitt wird veranschaulicht, wie Ereignisse aus einer einzigen solchen Partition empfangen werden. Beachten Sie jedoch, dass eine typische Anwendung häufig Ereignisse abrufen muss, die auf allen Event Hub-Partitionen gespeichert sind.

## <a name="device-client"></a>Geräteclient

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Herstellen der Verbindung mit und Authentifizierung bei einem IoT-Hub (Geräteclient)

Ein Gerät kann zum Herstellen der Verbindung mit einem IoT-Hub mithilfe von AMQP die Authentifizierung mit [Claims Based Security (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) (auf Ansprüchen basierende Sicherheit) oder [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) verwenden.

Für den Geräteclient sind die folgenden Informationen erforderlich:

| Information | Wert |
|-------------|--------------|
| Hostname des IoT-Hubs | `<iot-hub-name>.azure-devices.net` |
| Zugriffsschüssel | Ein primärer oder sekundärer dem Gerät zugeordneter Schlüssel |
| Shared Access Signature (SAS) | Eine kurzlebige Shared Access Signature (SAS) im folgenden Format: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Informationen zum Abrufen des Codes zum Generieren dieser Signatur finden Sie unter [Verwalten des Zugriffs auf IoT Hub](./iot-hub-dev-guide-sas.md#security-token-structure).

Der folgende Codeausschnitt verwendet die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) zum Herstellen der Verbindung mit IoT Hub über einen Senderlink.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Die folgenden Linkpfade werden als Gerätevorgänge unterstützt:

| Erstellt von | Verknüpfungstyp | Verknüpfungspfad | BESCHREIBUNG |
|------------|-----------|-----------|-------------|
| Geräte | Empfängerlink | `/devices/<deviceID>/messages/devicebound` | An Geräte gerichtete Cloud-zu-Gerät-Nachrichten werden von jedem Zielgerät über diesen Link empfangen. |
| Geräte | Senderlink | `/devices/<deviceID>/messages/events` | Von einem Gerät gesendete Gerät-zu-Cloud-Nachrichten werden über diesen Link gesendet. |
| Geräte | Senderlink | `/messages/serviceBound/feedback` | Von Geräten über diesen Link an den Dienst gesendetes Cloud-zu-Gerät-Nachrichtenfeedback. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Empfangen von Cloud-zu-Gerät-Befehlen (Geräteclient)

An Geräte gesendete Cloud-zu-Gerät-Befehle gehen an einem `/devices/<deviceID>/messages/devicebound`-Link ein. Geräte können diese Nachrichten in Batches empfangen und bei Bedarf die Nachrichtendatennutzlast, Nachrichteneigenschaften, Anmerkungen oder Anwendungseigenschaften in der Nachricht verwenden.

Im folgenden Codeausschnitt wird die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) zum Empfangen von Cloud-zu-Gerät-Nachrichten von einem Gerät verwendet.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Senden von Telemetrienachrichten (Geräteclient)

Sie können auch mithilfe von AMQP Telemetrienachrichten von einem Gerät senden. Das Gerät kann optional ein Wörterbuch der Anwendungseigenschaften oder verschiedene Nachrichteneigenschaften wie die Nachrichten-ID bereitstellen.

Im folgenden Codeausschnitt wird die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) zum Senden von Gerät-zu-Cloud-Nachrichten von einem Gerät verwendet.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Die AMQP-Verbindungen können aufgrund von Netzwerkstörungen oder Ablauf des (im Code generierten) Authentifizierungstokens unterbrochen werden. Der Dienstclient muss diese Situationen bewältigen und die Verbindung und Links bei Bedarf wiederherstellen. Bei Ablauf des Authentifizierungstokens kann der Client das Token auch proaktiv vor dessen Ablauf verlängern, um einen Verbindungsausfall zu vermeiden.

* In einigen Fällen muss Ihr Client Linkumleitungen ordnungsgemäß verarbeiten können. Informationen dazu finden Sie in der AMQP-Clientdokumentation.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Protokoll AMQP finden Sie in der [AMQP 1.0-Spezifikation](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Weitere Informationen zum IoT Hub-Messaging finden Sie unter:

* [C2D-Nachrichten](./iot-hub-devguide-messages-c2d.md)
* [Unterstützung zusätzlicher Protokolle](iot-hub-protocol-gateway.md)
* [Unterstützung des Protokolls MQTT (Message Queuing Telemetry Transport)](./iot-hub-mqtt-support.md)
