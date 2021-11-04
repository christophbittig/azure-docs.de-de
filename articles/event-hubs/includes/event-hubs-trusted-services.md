---
title: Datei einfügen
description: include file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/20/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1e9d61eca35cc9ea9fe20731fa093354c0eac5f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253701"
---
## <a name="trusted-microsoft-services"></a>Vertrauenswürdige Microsoft-Dienste
Wenn Sie die Einstellung **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben** aktivieren, wird den folgenden Diensten Zugriff auf Ihre Event Hubs-Ressourcen gewährt.

| Vertrauenswürdiger Dienst | Unterstützte Verwendungsszenarien | 
| --------------- | ------------------------- | 
| Azure Event Grid | Ermöglicht Azure Event Grid das Senden von Ereignissen an Event Hubs in Ihrem Event Hubs-Namespace. Sie müssen außerdem die folgenden Schritte ausführen: <ul><li>Aktivieren der vom System zugewiesenen Identität für ein Thema oder eine Domäne</li><li>Hinzufügen der Identität der Rolle „Azure Event Hubs-Datenabsender“ für den Event Hubs-Namespace</li><li>Konfigurieren Sie dann das Ereignisabonnement, das einen Event Hub als Endpunkt verwendet, um die vom System zugewiesene Identität zu verwenden.</li></ul> <p>Weitere Informationen finden Sie unter [Ereignisübermittlung mit einer verwalteten Identität](../../event-grid/managed-service-identity.md).</p>|
| Azure Monitor (Diagnoseeinstellungen und Aktionsgruppen) | Ermöglicht Azure Monitor das Senden von Diagnoseinformationen und Warnungsbenachrichtigungen an Event Hubs in Ihrem Event Hubs-Namespace. Azure Monitor kann Daten aus dem Event Hub lesen und in den Event Hub schreiben. |
| Azure Stream Analytics | Ermöglicht einem Azure Stream Analytics-Auftrag das Lesen von Daten von Event Hubs für [Eingaben](../../stream-analytics/stream-analytics-add-inputs.md) bzw. das Schreiben von Daten in Event Hubs für [Ausgaben](../../stream-analytics/event-hubs-output.md) in Ihrem Event Hubs-Namespace. <p>**Wichtig**: Der Stream Analytics-Auftrag sollte so konfiguriert werden, dass er eine **verwaltete Identität** für den Zugriff auf den Event Hub verwendet. Weitere Informationen finden Sie unter [Verwenden von verwalteten Identitäten zum Zugriff auf Event Hubs aus einem Azure Stream Analytics-Auftrag (Vorschau)](../../stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Ermöglicht Azure IoT Hub das Senden von Nachrichten an Event Hubs in Ihrem Event Hubs-Namespace. Sie müssen außerdem die folgenden Schritte ausführen: <ul><li>Aktivieren der systemseitig zugewiesenen Identität für Ihren IoT-Hub</li><li>Fügen Sie die Identität der Rolle „Azure Event Hubs-Datensender“ im Event Hubs-Namespace hinzu.</li><li>Konfigurieren Sie dann die IoT Hub-Instanz, die einen Event Hub als benutzerdefinierten Endpunkt nur Nutzung der identitätsbasierten Authentifizierung verwendet.</li></ul>
| Azure API Management | <p>Mit dem API Management-Dienst können Sie Ereignisse an einen Event Hub im Event Hubs-Namespace senden.</p> <ul><li>Sie können benutzerdefinierte Workflows auslösen, indem Sie Ereignisse an Ihren Event Hub senden, wenn eine API mithilfe der [send-request-Richtlinie](../../api-management/api-management-sample-send-request.md) aufgerufen wird.</li><li>Sie können auch einen Event Hub auch als Back-End in einer API behandeln. Eine Beispielrichtlinie finden Sie unter [Authentifizieren mithilfe einer verwalteten Identität für den Zugriff auf einen Event Hub](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). Sie müssen außerdem die folgenden Schritte ausführen:<ol><li>Aktivieren einer vom System zugewiesenen Identität für die API Management-Instanz. Anleitungen dazu finden Sie unter [Verwenden von verwalteten Identitäten in Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md).</li><li>Hinzufügen der Identität zur Rolle **Azure Event Hubs-Datensender** im Event Hubs-Namespace.</li></ol></li></ul> | 
| Azure IoT Central | <p>Ermöglicht IoT Central das Exportieren von Daten an Event Hubs in Ihrem Event Hubs-Namespace. Sie müssen außerdem die folgenden Schritte ausführen:</p><ul><li>Aktivieren Sie die systemseitig zugewiesene Identität für Ihre IoT Central-Anwendung.</li><li>Fügen Sie die Identität der Rolle **Azure Event Hubs-Datensender** im Event Hubs-Namespace hinzu.</li><li>Konfigurieren Sie dann das Event Hubs-[Exportziel in Ihrer IoT Central-Anwendung](../../iot-central/core/howto-export-data.md), um die identitätsbasierte Authentifizierung zu verwenden.</li>
