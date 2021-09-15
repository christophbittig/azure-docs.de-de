---
title: include file
description: Datei einfügen
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: b22cee560284a5a185e6b3ba560531d43d37ca67
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967934"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/add-chat).

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die folgenden Schritte aus, bevor Sie beginnen:

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installieren Sie [Python](https://www.python.org/downloads/).
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../create-communication-resource.md). Für diese Schnellstartanleitung müssen Sie den Endpunkt Ihrer Ressource aufzeichnen.
- Ein [Benutzerzugriffstoken](../../access-tokens.md). Achten Sie darauf, dass Sie den Bereich auf `chat` festlegen und sich die Zeichenfolgen für `token` und `userId` notieren.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Verwenden Sie einen Text-Editor, um im Stammverzeichnis des Projekts die Datei *start-chat.py* zu erstellen. Fügen Sie die Struktur für das Programm hinzu, einschließlich einer einfachen Ausnahmebehandlung In den folgenden Abschnitten fügen Sie der Datei den gesamten Quellcode für diese Schnellstartanleitung hinzu.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Installieren des SDK

Verwenden Sie den folgenden Befehl, um das SDK zu installieren:

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features des Chat-SDKs von Azure Communication Services für Python verwendet.

| Name                                  | BESCHREIBUNG                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `ChatClient` | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| `ChatThreadClient` | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie erhalten über `ChatClient` eine Instanz und verwenden sie zum Senden, Empfangen, Aktualisieren und Löschen von Nachrichten. Darüber hinaus können Sie sie auch verwenden, um Benutzer hinzuzufügen, zu entfernen und abzurufen sowie Eingabebenachrichtigungen und Lesebestätigungen zu senden. |

## <a name="create-a-chat-client"></a>Erstellen eines Chatclients

Zum Erstellen eines Chatclients verwenden Sie den Communication Services-Endpunkt und das Zugriffstoken, das Sie im Rahmen der Schritte zur Erfüllung der Voraussetzungen generiert haben.

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```
Die Erstellung einer Dienstebene zum Verwalten von Token für Ihre Chatanwendung wird in dieser Schnellstartanleitung nicht behandelt, aber dies ist die empfohlene Vorgehensweise. Weitere Informationen finden Sie im Artikel [Chatkonzepte](../../../concepts/chat/concepts.md) im Abschnitt „Chatarchitektur“.

## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Verwenden Sie die `create_chat_thread`-Methode, um einen Chatthread zu erstellen.

- Verwenden Sie `topic`, um für den Thread ein Thema anzugeben. Sie können das Thema aktualisieren, nachdem der Chatthread mithilfe der Funktion `update_thread` erstellt wurde.
- Verwenden Sie `thread_participants`, um den Chatteilnehmer (`ChatParticipant`) aufzulisten, der dem Chatthread hinzugefügt werden soll. `ChatParticipant` übernimmt `user` als `CommunicationUserIdentifier`-Typ.

`CreateChatThreadResult` ist das Ergebnis, das beim Erstellen eines Chatthreads zurückgegeben wird. Sie können es verwenden, um die ID (`id`) des erstellten Chatthreads abzurufen. Diese ID (`id`) kann dann zum Abrufen eines `ChatThreadClient`-Objekts mithilfe der Methode `get_chat_thread_client` verwendet werden. Mit `ChatThreadClient` können weitere Chatvorgänge für diesen Chatthread ausgeführt werden.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread

Die `get_chat_thread_client`-Methode gibt einen Threadclient für einen bereits vorhandenen Thread zurück. Sie können sie verwenden, um weitere Vorgänge für den Chatthread auszuführen. Beispielsweise können Sie Teilnehmer hinzufügen und Nachrichten senden. `thread_id` ist die eindeutige ID des vorhandenen Chatthreads.

Mit `ChatThreadClient` können weitere Chatvorgänge für diesen Chatthread ausgeführt werden.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>Auflisten aller Chatthreads

Von der Methode `list_chat_threads` wird ein Iterator vom Typ `ChatThreadItem` zurückgegeben.

- Verwenden Sie `start_time`, um den frühesten Zeitpunkt zum Abrufen von Chatthreads anzugeben.
- Verwenden Sie `results_per_page`, um die maximale Anzahl von Chatthreads anzugeben, die pro Seite zurückgegeben werden sollen.

Beim Auflisten von Threads wird als Antwort ein Iterator vom Typ `[ChatThreadItem]` zurückgegeben.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie die `send_message`-Methode zum Senden einer Nachricht an einen Chatthread, den Sie gerade erstellt haben und der anhand von `thread_id` identifiziert wird.

- Verwenden Sie `content`, um den Inhalt der Chatnachricht anzugeben.
- Verwenden Sie `chat_message_type`, um den Inhaltstyp der Nachricht anzugeben. Mögliche Werte sind `text` und `html`. Wenn Sie keinen Wert angeben, ist der Standardwert `text`.
- Verwenden Sie `sender_display_name`, um den Anzeigenamen des Absenders anzugeben.
- Verwenden Sie optional `metadata`, um alle zusätzlichen Daten einzubeziehen, die Sie zusammen mit der Nachricht senden möchten. Dieses Feld bietet Entwicklern einen Mechanismus, um die Chatnachrichtenfunktionalität zu erweitern und benutzerdefinierte Informationen für Ihren Anwendungsfall hinzuzufügen. Wenn Sie beispielsweise einen Dateilink in der Nachricht freigeben, sollten Sie „hasAttachment:true“ in den Metadaten hinzufügen, damit die Anwendung des Empfängers diese analysieren und entsprechend anzeigen kann.

`SendChatMessageResult` ist die Antwort, die beim Senden einer Nachricht zurückgegeben wird. Sie enthält eine ID, bei der es sich um die eindeutige ID der Nachricht handelt.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='Please take a look at the attachment'
sender_display_name='sender name'
metadata={
    'hasAttachment': 'true',
    'attachmentUrl': 'https://contoso.com/files/attachment.docx'
}

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT, metadata=metadata)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Sie können Chatnachrichten abrufen, indem Sie die `list_messages`-Methode jeweils nach dem angegebenen Intervallzeitraum abfragen.

- Verwenden Sie `results_per_page`, um die maximale Anzahl von Nachrichten anzugeben, die pro Seite zurückgegeben werden sollen.
- Verwenden Sie `start_time`, um den frühesten Zeitpunkt zum Abrufen von Nachrichten anzugeben.

Beim Auflisten von Nachrichten wird als Antwort ein Iterator vom Typ `[ChatMessage]` zurückgegeben.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` gibt die aktuelle Version der Nachricht zurück, einschließlich aller Bearbeitungen oder Löschungen, die für die Nachricht mit `update_message` und `delete_message` durchgeführt wurden. Für gelöschte Nachrichten wird von `ChatMessage.deleted_on` ein `datetime`-Wert zurückgegeben, mit dem der Löschzeitpunkt der Nachricht angegeben wird. Für bearbeitete Nachrichten gibt `ChatMessage.edited_on` einen `datetime`-Wert zurück, mit dem der Bearbeitungszeitpunkt der Nachricht angegeben wird. Auf den ursprünglichen Zeitpunkt der Nachrichtenerstellung kann mithilfe von `ChatMessage.created_on` zugegriffen werden. Dieses Element kann zum Sortieren der Nachrichten genutzt werden.

Mit `list_messages` werden unterschiedliche Nachrichtentypen zurückgegeben, die mit `ChatMessage.type` identifiziert werden können. 

Weitere Informationen finden Sie unter [Nachrichtentypen](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Senden einer Lesebestätigung

Sie können die Methode `send_read_receipt` verwenden, um im Auftrag eines Benutzers ein Lesebestätigungsereignis in einem Thread zu veröffentlichen.

- Verwenden Sie `message_id`, um die ID der letzten vom aktuellen Benutzer gelesenen Nachricht anzugeben.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Teilnehmer des Chatthreads

Beim Erstellen eines Chatthreads können Sie dafür Benutzer hinzufügen und entfernen. Durch Hinzufügen von Benutzern gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Chatthread und zum Hinzufügen oder Entfernen anderer Teilnehmer. Vergewissern Sie sich vor dem Aufrufen der Methode `add_participants`, dass Sie über ein neues Zugriffstoken und über eine Identität für den Benutzer verfügen. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

Mithilfe der Methode `add_participants` können Sie dem Chatthread einen oder mehrere Benutzer hinzufügen, sofern für alle Benutzer ein neues Zugriffstoken und eine neue Identität verfügbar sind.

Ein `list(tuple(ChatParticipant, CommunicationError))` wird zurückgegeben. Wenn der Teilnehmer erfolgreich hinzugefügt wurde, wird eine leere Liste erwartet. Tritt beim Hinzufügen eines Teilnehmers ein Fehler auf, wird die Liste mit den Teilnehmern, bei denen ein Fehler auftrat, sowie den aufgetretenen Fehler aufgefüllt.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     identifier=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    identifier=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Liste der Threadteilnehmer eines Chatthreads

Threadteilnehmer können aufgelistet werden. Die Vorgehensweise ist dabei ähnlich wie beim Hinzufügen.

Verwenden Sie `list_participants`, um die Teilnehmer des Threads abzurufen. Die beiden folgenden Befehle sind optional:

- Verwenden Sie `results_per_page`, um die maximale Anzahl von Teilnehmern anzugeben, die pro Seite zurückgegeben werden sollen.
- Verwenden Sie `skip`, um die Teilnehmer bis zur angegebenen Position in der Antwort zu überspringen.

Beim Auflisten von Teilnehmern wird als Antwort ein Iterator vom Typ `[ChatParticipant]` zurückgegeben.

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `python` aus dem Anwendungsverzeichnis aus.

```console
python start-chat.py
```
