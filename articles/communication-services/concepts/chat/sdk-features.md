---
title: Übersicht über das Chat SDK für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie Informationen zum Chat SDK von Azure Communication Services.
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: chat
ms.openlocfilehash: 64d88922d849fc29ab5d33451d8d90bed6ed4028
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308406"
---
# <a name="chat-sdk-overview"></a>Übersicht über das Chat SDK

Die Chat SDKs von Azure Communication Services können verwendet werden, um Ihren Anwendungen umfassende Echtzeitchatfunktionen hinzuzufügen.

## <a name="chat-sdk-capabilities"></a>Funktionen der Chat SDKs

Die folgende Liste enthält die Features, die aktuell in den Chat SDKs von Communication Services verfügbar sind:

| Featuregruppe | Funktion | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Grundlegende Funktionen | Erstellen eines Chatthreads zwischen zwei oder mehr Benutzern                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | Aktualisieren des Themas eines Chatthreads                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | Hinzufügen oder Entfernen von Teilnehmern zu bzw. aus einem Chatthread                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Auswählen, ob der Chatnachrichtenverlauf mit dem hinzugefügten Teilnehmer geteilt werden soll                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Abrufen einer Liste der Teilnehmer eines Chatthreads                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Löschen eines Chatthreads                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Abrufen der Liste der Chatthreads, denen der Benutzer angehört (bei Kommunikationsbenutzern)                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Abrufen von Informationen für einen bestimmten Chatthread                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Senden und Empfangen von Nachrichten in einem Chatthread                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Aktualisieren des Inhalts der gesendeten Nachricht                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Löschen einer zuvor von Ihnen gesendeten Nachricht                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | Lesebestätigungen für Nachrichten, die von anderen Teilnehmern in einem Chat gelesen wurden                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | Erhalten einer Benachrichtigung, wenn Teilnehmer aktiv eine Nachricht in einem Chatthread eingeben                                         | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |
|                   | Abrufen aller Nachrichten in einem Chatthread                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Senden von Unicode-Emojis im Nachrichteninhalt                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Hinzufügen von Metadaten zu Chatnachrichten                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |
|                   | Hinzufügen eines Anzeigenamens zur Benachrichtigung eines Eingabeindikators                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |
|Echtzeitbenachrichtigungen (durch proprietäres Signalisierungspaket**)|  Chatclients können Echtzeitaktualisierungen für eingehende Nachrichten und andere Vorgänge abonnieren, die in einem Chatthread stattfinden. Eine Liste der unterstützten Aktualisierungen für Echtzeitbenachrichtigungen finden Sie unter [Chatkonzepte](concepts.md#real-time-notifications).                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |
| Integration in Azure Event Grid             | Verwenden Sie die in Azure Event Grid verfügbaren Chatereignisse, um benutzerdefinierte Benachrichtigungsdienste einzubinden oder dieses Ereignis in einem Webhook zu posten und um Geschäftslogik (etwa Aktualisieren von CRM-Datensätzen nach Abschluss eines Chats) auszuführen.   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
| Berichterstellung </br>(Diese Informationen finden Sie auf der Registerkarte „Überwachung“ für Ihre Communication Services-Ressource im Azure-Portal.)      | Vollziehen Sie den API-Datenverkehr aus Ihrer Chat-App nach, indem Sie die veröffentlichten Metriken im Azure-Metrik-Explorer überwachen und Warnungen zum Erkennen von Anomalien festlegen.     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | Überwachen und Debuggen der Communication Services-Lösung durch Aktivieren der Diagnoseprotokollierung für Ihre Ressource    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |


**Das proprietäre Signalisierungspaket wird mithilfe von Websockets implementiert. Es wird ein Fallback auf ein langes Abrufintervall durchgeführt, falls Websockets nicht unterstützt werden.

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>JavaScript Chat SDK: Unterstützung nach Betriebssystem und Browser

Die folgende Tabelle enthält die unterstützten Browser und Versionen, die derzeit verfügbar sind:

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad-Betriebssystem|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat SDK** | Firefox *, Chrome*, Microsoft Edge (neu) | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

\* Hinweis: Neben den beiden vorherigen Releases wird auch die neueste Version unterstützt.<br/>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Chat](../../quickstarts/chat/get-started.md)

Die folgenden Dokumente könnten Sie auch interessieren:
- Machen Sie sich mit [Chatkonzepten](../chat/concepts.md) vertraut.
- Grundlegendes zu den [Preisen](../pricing.md#chat) für die Chatfunktion
