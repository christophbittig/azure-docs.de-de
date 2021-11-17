---
title: Chatkonzepte in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über Chatkonzepte in Communication Services.
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: chat
ms.openlocfilehash: f9b5ab547171af33d459cd0a52b1cbb2dfc2511c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308463"
---
# <a name="chat-concepts"></a>Chatkonzepte

Azure Communication Services Chat SDKs können verwendet werden, um Ihre Anwendungen mit Echtzeittextchat auszustatten. Auf dieser Seite werden wichtige Chatkonzepte und -funktionen zusammengefasst.

Weitere Informationen zu bestimmten SDK-Sprachen und -Funktionen finden Sie in der [Übersicht über das Communication Services Chat SDK](./sdk-features.md).

## <a name="chat-overview"></a>Übersicht über den Chat

Chatkonversationen finden innerhalb von **Chatthreads** statt. Chatthreads haben folgende Eigenschaften:

- Ein Chatthread wird durch seine ID (`ChatThreadId`) eindeutig identifiziert. 
- Chatthreads verfügen über mindestens einen Benutzer als Teilnehmer, der Nachrichten an den Thread senden kann. 
- Ein Benutzer kann an einem einzelnen Chatthread oder an mehreren Chatthreads teilnehmen. 
- Nur die Threadteilnehmer haben Zugriff auf einen bestimmten Chatthread, und nur sie können Chatthreadvorgänge ausführen. Diese Vorgänge umfassen das Senden und Empfangen von Nachrichten sowie das Hinzufügen und Entfernen von Teilnehmern. 
- Der Ersteller eines Chatthreads wird ihm automatisch als Teilnehmer hinzugefügt.

### <a name="user-access"></a>Benutzerzugriff
In der Regel haben der Threadersteller und die Teilnehmer den gleichen Zugriff auf den Thread und können alle zugehörigen, im SDK verfügbaren Vorgänge ausführen (einschließlich Löschen). Teilnehmer haben keinen Schreibzugriff auf Nachrichten, die von anderen Teilnehmern gesendet wurden. Somit können Nachrichten nur vom jeweiligen Absender aktualisiert oder gelöscht werden. Wenn ein anderer Teilnehmer versucht, einen solchen Vorgang auszuführen, tritt ein Fehler auf. 

### <a name="chat-data"></a>Chatdaten 
Der Chatverlauf wird von Communication Services gespeichert, bis er explizit gelöscht wird. Chatthreadteilnehmer können `ListMessages` verwenden, um den Nachrichtenverlauf für einen bestimmten Thread anzuzeigen. Benutzer, die aus einem Chatthread entfernt wurden, können zwar weiterhin den bisherigen Nachrichtenverlauf anzeigen, aber keine neuen Nachrichten für diesen Chatthread mehr senden oder empfangen. Ein vollständig im Leerlauf befindlicher Thread ohne Teilnehmer wird nach 30 Tagen automatisch gelöscht. Weitere Informationen zu von Communication Services gespeicherten Daten finden Sie in der [Dokumentation zum Datenschutz](../privacy.md).  

### <a name="service-limits"></a>Diensteinschränkungen
- Pro Chatthread sind maximal 250 Teilnehmer zulässig.
- Die maximal zulässige Nachrichtengröße beträgt ca. 28 KB.
- Bei Chatthreads mit mehr als 20 Teilnehmern werden Lesebestätigungen und Eingabeindikator nicht unterstützt.

## <a name="chat-architecture"></a>Chatarchitektur

Die Chatarchitektur besteht aus zwei zentralen Teilen: 1) einem vertrauenswürdigen Dienst und 2) einer Clientanwendung.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagramm, das die Chatarchitektur von Communication Services zeigt":::

 - **Vertrauenswürdiger Dienst:** Damit Sie eine Chatsitzung ordnungsgemäß verwalten können, benötigen Sie einen Dienst, der Ihnen hilft, mithilfe ihrer Ressourcen-Verbindungszeichenfolge eine Verbindung mit Communication Services herzustellen. Dieser Dienst ist für das Erstellen von Chatthreads sowie das Hinzufügen und Entfernen von Teilnehmern und das Ausstellen von Zugriffstoken für Benutzer zuständig. Weitere Informationen zu Zugriffstoken finden Sie in unserem Schnellstart zu [Zugriffstoken](../../quickstarts/access-tokens.md).
 - **Client-App:** Die Clientanwendung stellt eine Verbindung mit Ihrem vertrauenswürdigen Dienst her und empfängt die Zugriffstoken, die von Benutzern zum Herstellen einer direkten Verbindung mit Communication Services verwendet werden. Nachdem Ihr vertrauenswürdiger Dienst den Chatthread erstellt und Benutzer als Teilnehmer hinzugefügt hat, können diese die Client-App verwenden, um eine Verbindung mit dem Chatthread herzustellen und Nachrichten zu senden. Verwenden Sie das Feature für Echtzeitbenachrichtigungen (siehe Erläuterung weiter unten) in Ihrer Client-App, um Nachrichten- und Threadaktualisierungen von anderen Teilnehmern zu abonnieren.


## <a name="message-types"></a>Nachrichtentypen

Im Rahmen des Nachrichtenverlaufs werden benutzerseitig generierte Nachrichten sowie systemseitig generierte Nachrichten weitergegeben. Systemnachrichten werden generiert, wenn ein Chatthread aktualisiert wird. Mit ihrer Hilfe kann ermittelt werden, wann ein Teilnehmer hinzugefügt oder entfernt wurde oder wann das Chatthreadthema aktualisiert wurde. Wenn Sie `List Messages` oder `Get Messages` in einem Chatthread aufrufen, enthält das Ergebnis beide Arten von Nachrichten in chronologischer Reihenfolge.

Bei benutzerseitig generierten Nachrichten kann der Nachrichtentyp in `SendMessageOptions` festgelegt werden, wenn eine Nachricht an den Chatthread gesendet wird. Ohne Angabe eines Werts wird von Communication Services standardmäßig der Typ `text` verwendet. Das Festlegen dieses Werts ist beim Senden von HTML wichtig. Bei Angabe von `html` wird der Inhalte durch Communication Services bereinigt, um sicherzustellen, dass er auf Clientgeräten gefahrlos gerendert wird.
 - `text`: Eine von einem Benutzer im Rahmen eines Chatthreads verfasste und gesendete Nur-Text-Nachricht. 
 - `html`: Eine formatierte Nachricht mit HTML, die von einem Benutzer im Rahmen eines Chatthreads verfasst und gesendet wurde. 

Arten von Systemnachrichten: 
 - `participantAdded`: Systemnachricht mit dem Hinweis, dass dem Chatthread mindestens ein Teilnehmer hinzugefügt wurde
 - `participantRemoved`: Systemnachricht mit dem Hinweis, dass ein Teilnehmer aus dem Chatthread entfernt wurde
 - `topicUpdated`: Systemnachricht mit dem Hinweis, dass das Threadthema aktualisiert wurde.

## <a name="real-time-notifications"></a>Echtzeitbenachrichtigungen

Von bestimmten SDKs werden Echtzeitbenachrichtigungen unterstützt. (Hierzu zählt beispielsweise das JavaScript Chat SDK.) Dieses Feature ermöglicht es Clients, bei Communication Services auf Echtzeitaktualisierungen und eingehende Nachrichten in einem Chatthread zu lauschen, ohne die APIs abfragen zu müssen. Von der Client-App können folgende Ereignisse abonniert werden:
 - `chatMessageReceived`: Wenn von einem Teilnehmer eine neue Nachricht an einen Chatthread gesendet wird.
 - `chatMessageEdited`: Wenn eine Nachricht in einem Chatthread bearbeitet wird.
 - `chatMessageDeleted`: Wenn eine Nachricht in einem Chatthread gelöscht wird.
 - `typingIndicatorReceived`: Wenn ein anderer Teilnehmer einen Eingabeindikator an den Chatthread sendet.
 - `readReceiptReceived`: Wenn ein anderer Teilnehmer eine Lesebestätigung für eine gelesene Nachricht sendet.
 - `chatThreadCreated`: Wenn ein Communication Services-Benutzer einen Chatthread erstellt.
 - `chatThreadDeleted`: Wenn ein Communication Services-Benutzer einen Chatthread löscht.
 - `chatThreadPropertiesUpdated`: Wenn die Eigenschaften eines Chatthread aktualisiert werden. Aktuell wird nur das Aktualisieren des Threadthemas unterstützt.
 - `participantsAdded`: Wenn ein Benutzer als Chatthreadteilnehmer hinzugefügt wird.
 - `participantsRemoved`: Wenn ein vorhandener Teilnehmer aus dem Chatthread entfernt wird.

Echtzeitbenachrichtigungen können verwendet werden, um eine Echtzeitchat-Umgebung für Ihre Benutzer bereitzustellen. Um Pushbenachrichtigungen für Nachrichten zu senden, die Benutzer während ihrer Abwesenheit verpasst haben, arbeitet Communication Services mit Azure Event Grid zusammen, um chatbezogene Ereignisse (POST-Vorgang) zu veröffentlichen, die in Ihren benutzerdefinierten App-Benachrichtigungsdienst integriert werden können. Ausführlichere Informationen finden Sie unter [Ereignisbehandlung in Azure Communication Services](../../../event-grid/event-schema-communication-services.md?bc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fbread%2ftoc.json&toc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fcommunication-services%2ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Erstellen intelligenter, KI-gestützter Chatumgebungen

Sie können [Azure Cognitive-APIs](../../../cognitive-services/index.yml) mit dem Chat SDK verwenden, um Anwendungsfälle wie die folgenden zu erstellen:

- Ermöglichen Sie es Benutzern, miteinander in verschiedenen Sprachen zu chatten.
- Helfen Sie einem Support-Agent bei der Priorisierung von Tickets, indem Sie eine negative Stimmung in einer eingehenden Nachricht eines Kunden erkennen.
- Analysieren Sie die eingehenden Nachrichten zur Schlüssel- und Entitätserkennung, und fordern Sie von Ihrem Benutzer in Ihrer App basierend auf dem Nachrichteninhalt relevante Informationen an.

Dies kann unter anderem erreicht werden, indem Ihr vertrauenswürdiger Dienst als Teilnehmer eines Chatthreads fungiert. Angenommen, Sie möchten die Sprachübersetzung aktivieren. Dieser Dienst ist dafür zuständig, auf Nachrichten zu lauschen, die von anderen Teilnehmern ausgetauscht werden[1], Cognitive-APIs aufzurufen, um den Inhalt in die gewünschte Sprache zu übersetzen[2, 3] und das übersetzte Ergebnis als Nachricht im Chatthread zu senden[4].

So enthält der Nachrichtenverlauf sowohl die ursprünglichen als auch übersetzte Nachrichten. In der Clientanwendung können Sie Logik hinzufügen, um die ursprüngliche oder die übersetzte Nachricht anzuzeigen. Informationen zur Verwendung von Cognitive APIs zum Übersetzen von Text in verschiedene Sprachen finden Sie in [diesem Schnellstart](../../../cognitive-services/translator/quickstart-translator.md). 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagramm, das zeigt, wie Cognitive Services mit Communication Services interagieren":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Chat](../../quickstarts/chat/get-started.md)

Die folgenden Dokumente könnten Sie auch interessieren:
- Machen Sie sich mit dem [Chat SDK](sdk-features.md) vertraut.
