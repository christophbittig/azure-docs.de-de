---
title: Azure Service Bus-Nachrichtensitzungen | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Nachrichtensitzungen zum Ermöglichen der gemeinsamen und geordneten Verarbeitung unbegrenzter Sequenzen verwandter Nachrichten verwendet werden.
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: 55a5d432505d6c0aec7c2fc9b030eb9763413bee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063287"
---
# <a name="message-sessions"></a>Nachrichtensitzungen
Azure Service Bus-Sitzungen ermöglichen die gemeinsame und geordnete Verarbeitung unbegrenzter Sequenzen verwandter Nachrichten. Sitzungen können mit **FIFO**-Mustern (First in, First Out) und **Anforderung/Antwort**-Mustern verwendet werden. In diesem Artikel wird gezeigt, wie Sie diese Muster mithilfe von Sitzungen implementieren, wenn Sie Service Bus verwenden. 

> [!NOTE]
> Der Basic-Tarif von Service Bus unterstützt keine Sitzungen. Die Standard- und Premium-Tarife unterstützen Sitzungen. Informationen zu den Unterschieden zwischen diesen Tarifen finden Sie unter [Service Bus-Preise](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>FIFO-Muster (First in, First Out)
Um eine FIFO-Garantie in Service Bus umzusetzen, müssen Sie Sitzungen verwenden. Service Bus macht keine Vorgaben zur Art der Beziehung zwischen Nachrichten und definiert auch kein bestimmtes Modell zur Bestimmung, wo eine Nachrichtensequenz beginnt oder endet.

Jeder Absender kann eine Sitzung erstellen, wenn er Nachrichten an ein Thema oder eine Warteschlange sendet, indem er die Eigenschaft **Sitzungs-ID** auf einen von der Anwendung definierten Bezeichner festlegt, der für die Sitzung eindeutig ist. Auf der **AMQP 1.0**-Protokollebene ist dieser Wert der **group-id**-Eigenschaft zugeordnet.

Bei sitzungsabhängigen Warteschlangen oder Abonnements entstehen Sitzungen, wenn mindestens eine Nachricht mit der Sitzungs-ID vorhanden ist. Sobald eine Sitzung vorhanden ist, gibt es keine definierte Uhrzeit oder API für den Zeitpunkt, an dem die Sitzung abläuft oder erlischt. Theoretisch kann eine Nachricht für eine Sitzung heute und die nächste Nachricht in einem Jahr empfangen werden. Wenn die Sitzungs-ID übereinstimmt, ist die Sitzung aus Service Bus-Sicht identisch.

Typischerweise verfügt eine Anwendung jedoch über klare Informationen dazu, wo eine Reihe zusammengehöriger Nachrichten beginnt und endet. Service Bus legt allerdings keine spezifischen Regeln fest. Ihre Anwendung kann die **Label**-Eigenschaft beispielsweise für die erste Nachricht auf **start** festlegen, für die Zwischennachrichten auf **content** und für die letzte Nachricht auf **end**. Die relative Position der content-Nachrichten kann als *SequenceNumber*-Delta der aktuellen Nachricht von der *SequenceNumber* der **Start**-Nachricht berechnet werden.

> [!IMPORTANT]
> Wenn Sitzungen für eine Warteschlange oder ein Abonnement aktiviert sind, können die Clientanwendungen ***keine*** regulären Nachrichten mehr senden oder empfangen. Alle Nachrichten müssen im Rahmen einer Sitzung (durch Festlegen der Sitzungs-ID) gesendet und durch Akzeptieren der Sitzung empfangen werden.

Die APIs für Sitzungen sind für Warteschlangen- und Abonnementclients vorhanden. Es gibt ein verbindliches Modell, das steuert, wann Sitzungen und Nachrichten empfangen werden, und ein auf einem Handler basierendes Modell, das die Komplexität bei der Verwaltung der Empfangsschleife verbirgt. 

Beispiele finden Sie unter den Links im [Abschnitt Nächste Schritte](#next-steps). 

### <a name="session-features"></a>Sitzungsfeatures

Sitzungen ermöglichen das gleichzeitige Demultiplexing von verschachtelten Nachrichtenströmen unter Beibehaltung und Gewährleistung der geordneten Zustellung.

![Ein Diagramm, das zeigt, wie das Sitzungsfeature die geordnete Zustellung beibehält][1]

Ein Sitzungsempfänger wird von einem Client erstellt, der eine Sitzung akzeptiert. Wenn die Sitzung von einem Client akzeptiert und aufrechterhalten wird, hält der Client eine exklusive Sperre für alle Nachrichten mit der **Sitzungs-ID** dieser Sitzung, die in der Warteschlange oder im Abonnement vorhanden sind. Er hält auch exklusive Sperren für alle Nachrichten mit der **Sitzungs-ID**, die später eingehen.

Die Sperre wird freigegeben, wenn Methoden für Schließvorgänge auf der Empfängerseite aufgerufen werden oder die Sperre abläuft. Auf der Empfängerseite sind auch Methoden zum Verlängern der Sperren verfügbar. Sie können stattdessen das Feature für die automatische Verlängerung von Sperren verwenden und dort den Zeitraum angeben, für den die Sperre verlängert werden soll. Die Sitzungssperre sollte wie eine exklusive Sperre für eine Datei behandelt werden, d. h. die Anwendung muss die Sitzung schließen, sobald sie diese nicht mehr benötigt und/oder keine weiteren Nachrichten erwartet.

Wenn mehrere gleichzeitige Empfänger auf die Warteschlange zugreifen, werden die zu einer bestimmten Sitzung gehörenden Nachrichten an den spezifischen Empfänger gesendet, der gerade die Sperre für diese Sitzung hält. Mit diesem Vorgang erfolgt für einen geschachtelten Nachrichtendatenstrom, der sich in einer Warteschlange oder einem Abonnement befindet, ein ordnungsgemäßes Demultiplexing an verschiedene Empfänger. Diese Empfänger können sich auch auf verschiedenen Clientcomputern befinden, da die Sperrverwaltung in Service Bus auf Dienstseite erfolgt.

Die vorherige Abbildung zeigt drei gleichzeitige Sitzungsempfänger. Eine Sitzung mit `SessionId` = 4 verfügt über keinen aktiven, besitzenden Client, was bedeutet, dass von dieser bestimmten Sitzung keine Nachrichten übermittelt werden. Eine Sitzung fungiert in vielerlei Hinsicht wie eine untergeordnete Warteschlange.

Die vom Sitzungsempfänger gehaltene Sitzungssperre ist ein Schirm für die Nachrichtensperren, die vom *peek-lock*-Abstimmungsmodus verwendet werden. Nur ein Empfänger kann eine Sperre für eine Sitzung haben. Ein Empfänger kann viele übermittelte Nachrichten haben, aber die Nachrichten werden in Reihenfolge empfangen. Das Abbrechen einer Nachricht bewirkt, dass dieselbe Nachricht beim nächsten Empfangsvorgang erneut abgearbeitet wird.

### <a name="message-session-state"></a>Nachrichtensitzungszustand

Wenn Workflows in umfangreichen, hochverfügbaren Cloudsystemen verarbeitet werden, muss der zu einer bestimmten Sitzung gehörende Workflowhandler in der Lage sein, unerwartete Fehler zu beheben. Er kann teilweise abgeschlossene Aufgaben in einem anderen Prozess oder auf einem anderen Computer, auf dem die Aufgabe begonnen hat, fortsetzen.

Der Sitzungszustand ermöglicht eine von der Anwendung definierte Anmerkung einer Nachrichtensitzung innerhalb des Brokers, sodass der aufgezeichnete Verarbeitungszustand in Bezug auf diese Sitzung sofort verfügbar wird, sobald die Sitzung von einem neuen Prozessor übernommen wird.

Aus Service Bus-Sicht ist der Nachrichtensitzungszustand ein undurchsichtiges binäres Objekt, das Daten von der Größe einer Nachricht aufnehmen kann, die bei Service Bus Standard 256 KB und bei Service Bus Premium 100 MB beträgt. Der Verarbeitungszustand relativ zu einer Sitzung kann innerhalb des Sitzungszustands gespeichert werden, oder der Sitzungszustand kann auf einen Speicherort oder Datenbankdatensatz verweisen, der solche Informationen enthält.

Die Methoden zum Verwalten des Sitzungszustands – „SetState“ und „GetState“ – befinden sich im Sitzungsempfängerobjekt. Eine Sitzung, für die zuvor kein Sitzungszustand festgelegt wurde, gibt für „GetState“ einen Nullverweis zurück. Der zuvor festgelegte Sitzungszustand kann durch Übergeben von NULL an die SetState-Methode auf der Empfängerseite gelöscht werden.

Der Sitzungszustand bleibt erhalten, bis er gelöscht wird (Rückgabe: **NULL**), auch wenn alle Nachrichten in einer Sitzung verarbeitet werden.

Der Sitzungszustand, der in einer Warteschlange oder in einem Abonnement gespeichert ist, wird auf das Speicherkontingent dieser Entität angerechnet. Wenn die Anwendung mit einer Sitzung fertig ist, empfiehlt es sich daher für die Anwendung, ihren beibehaltenen Zustand zu bereinigen, um externe Verwaltungskosten zu vermeiden.

### <a name="impact-of-delivery-count"></a>Auswirkungen der Übermittlungsanzahl

Die Definition der Übermittlungsanzahl pro Nachricht im Kontext von Sitzungen weicht geringfügig von der Definition bei fehlenden Sitzungen ab. Hier ist eine Tabelle mit einer Zusammenfassung der Umstände, unter denen die Übermittlungsanzahl erhöht wird.

| Szenario | Übermittlungsanzahl der Nachricht inkrementiert? |
|----------|---------------------------------------------|
| Die Sitzung wird akzeptiert, aber die Sitzungssperre läuft ab (aufgrund eines Timeouts). | Ja |
| Die Sitzung wird akzeptiert, die Nachrichten innerhalb der Sitzung werden nicht abgeschlossen (selbst wenn sie gesperrt sind), und die Sitzung wird geschlossen. | Nein |
| Die Sitzung wird akzeptiert, Nachrichten werden abgeschlossen, und die Sitzung wird explizit geschlossen. | Nicht zutreffend (Standardflow. Hier werden die Nachrichten aus der Sitzung entfernt.) |

## <a name="request-response-pattern"></a>Anforderung/Antwort-Muster
Das [Anforderung/Antwort-Muster](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) ist ein etabliertes Integrationsmuster, das es der sendenden Anwendung ermöglicht, eine Anforderung zu senden, und dem Empfänger die Möglichkeit bietet, eine Antwort ordnungsgemäß an die Absenderanwendung zurückzusenden. Dieses Muster benötigt in der Regel eine kurzlebige Warteschlange oder ein kurzlebiges Thema, an die bzw. das die Anwendung Antworten senden soll. In diesem Szenario stellen Sitzungen eine einfache alternative Lösung mit vergleichbarer Semantik dar. 

Mehrere Anwendungen können ihre Anforderungen an eine einzelne Anforderungswarteschlange senden, wobei ein spezifischer Headerparameter zur eindeutigen Identifizierung der Absenderanwendung festgelegt wird. Die Empfängeranwendung kann die in der Warteschlange eingehenden Anforderungen verarbeiten und Antworten in der sitzungsfähigen Warteschlange senden. Dabei wird die Sitzungs-ID auf den eindeutigen Bezeichner festgelegt, den der Absender in der Anforderungsnachricht gesendet hatte. Die Anwendung, die die Anforderung gesendet hat, kann dann Nachrichten mit der bestimmten Sitzungs-ID empfangen und die Antworten ordnungsgemäß verarbeiten.

> [!NOTE]
> Die Anwendung, die die ersten Anforderungen sendet, muss die Sitzungs-ID kennen und diese verwenden, um die Sitzung zu akzeptieren, damit die Sitzung, in der sie die Antwort erwartet, gesperrt wird. Es empfiehlt sich, eine GUID zu verwenden, die die Instanz der Anwendung eindeutig als Sitzungs-ID identifiziert. Um sicherzustellen, dass die Antworten durch bestimmte Empfänger gesperrt und verarbeitet werden können, darf kein Sitzungshandler oder -timeout auf der Seite des Sitzungsempfängers für die Warteschlange festgelegt sein.

## <a name="next-steps"></a>Nächste Schritte
Sie können Nachrichtensitzungen im Zuge der Erstellung einer Warteschlange per Azure-Portal, PowerShell, CLI, Resource Manager-Vorlage, .NET, Java, Python und JavaScript aktivieren. Weitere Informationen finden Sie unter [Aktivieren von Nachrichtensitzungen für eine Azure Service Bus-Warteschlange oder ein Abonnement](enable-message-sessions.md). 

Sehen Sie sich die Beispiele in der Sprache Ihrer Wahl an, um sich mit Azure Service Bus-Features vertraut zu machen: 

- [Azure Service Bus-Clientbibliothekbeispiele für .NET (neueste Version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für Java (neueste Version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus-Clientbibliothekbeispiele für TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Hier finden Sie Beispiele für die älteren .NET- und Java-Clientbibliotheken:
- [Azure Service Bus-Clientbibliothekbeispiele für .NET (Legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Azure Service Bus-Clientbibliothekbeispiele für Java (Legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

