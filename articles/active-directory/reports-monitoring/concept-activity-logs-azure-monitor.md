---
title: Azure Active Directory-Aktivitätsprotokolle in Azure Monitor | Microsoft-Dokumentation
description: Einführung in Azure Active Directory-Aktivitätsprotokolle in Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 110170868ba477060c5cd8ba1fbf28428160e29e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305577"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure AD-Aktivitätsprotokolle in Azure Monitor

Sie können Azure AD-Aktivitätsprotokolle (Azure Active Directory) zur langfristigen Aufbewahrung und Gewinnung von Erkenntnissen zu Daten an mehrere Endpunkte weiterleiten. Dieses Feature ermöglicht Folgendes:

* Archivieren von Azure AD-Aktivitätsprotokollen in einem Azure-Speicherkonto, um die Daten für längere Zeit aufzubewahren
* Streamen Sie Aktivitätsprotokolle von Azure AD zum Analysieren an einen Azure Event Hub mithilfe der beliebten SIEM-Tools (Security Information & Event Management) wie Splunk, QRadar und Microsoft .
* Integrieren von Azure AD-Aktivitätsprotokollen in Ihre eigenen benutzerdefinierten Protokolllösungen, indem Sie sie an einen Event Hub streamen
* Senden von Azure AD-Aktivitätsprotokollen an Azure Monitor-Protokolle, um funktionsreiche Visualisierungen, Überwachung und Benachrichtigungen für die verbundenen Daten zu aktivieren

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Unterstützte Berichte

Mit dieser Funktion können Sie Azure AD-Überwachungsprotokolle und -Anmeldeprotokolle an Ihr Azure-Speicherkonto, einen Event Hub, an Azure Monitor-Protokolle oder eine benutzerdefinierte Lösung weiterleiten.

* **Überwachungsprotokolle**: Der [Aktivitätsbericht der Überwachungsprotokolle](concept-audit-logs.md) bietet Ihnen Zugriff auf Informationen über Änderungen, die auf Ihren Mandanten angewendet wurden, z. B. die Verwaltung von Benutzern und Gruppen oder Aktualisierungen, die auf die Ressourcen Ihres Mandanten angewendet wurden.
* **Anmeldeprotokolle**: Mit dem [Aktivitätsbericht zu Anmeldungen](concept-sign-ins.md) können Sie ermitteln, von wem die Aufgaben durchgeführt wurden, die in den Überwachungsprotokollen aufgeführt sind.

> [!NOTE]
> B2C-bezogene Aktivitätsprotokolle für Überwachungen und Anmeldungen werden derzeit nicht unterstützt.
>

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um dieses Feature verwenden zu können:

* Ein Azure-Abonnement. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für eine kostenlose Testversion registrieren](https://azure.microsoft.com/free/).
* Eine Azure AD-[Lizenz](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) vom Typ „Free“, „Basic“, „Premium 1“ oder „Premium 2“, um auf die Azure AD-Überwachungsprotokolle im Azure-Portal zuzugreifen. 
* Einen Azure AD-Mandanten.
* Einen Benutzer, der als **globaler Administrator** oder **Sicherheitsadministrator** für den Azure AD-Mandanten fungiert.
* Eine Azure AD-[Lizenz](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) vom Typ „Premium 1“ oder „Premium 2“, um auf die Azure AD-Anmeldeprotokolle im Azure-Portal zuzugreifen. 

Je nachdem, wohin die Überwachungsprotokolldaten weitergeleitet werden, benötigen Sie Folgendes:

* Ein Azure-Speicherkonto, für das Sie über *ListKeys*-Berechtigungen verfügen. Wir empfehlen Ihnen, anstelle eines Blobspeicherkontos ein allgemeines Speicherkonto zu verwenden. Preisinformationen zur Speicherung erhalten Sie über den [Azure Storage-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Einen Azure Event Hubs-Namespace, um die Integration in Drittanbieterlösungen zu ermöglichen.
* Einen Azure Log Analytics-Arbeitsbereich, um Protokolle an Azure Monitor-Protokolle zu senden.

## <a name="cost-considerations"></a>Kostenbetrachtung

Falls Sie bereits über eine Azure AD-Lizenz verfügen, benötigen Sie ein Azure-Abonnement, um das Speicherkonto und den Event Hub einzurichten. Für das Azure-Abonnement fallen keine Kosten an, doch die Nutzung der Azure-Ressourcen (z. B. das Speicherkonto, das Sie für die Archivierung verwenden, und der Event Hub, den Sie zum Streamen nutzen) ist kostenpflichtig. Die Datenmenge und somit auch die anfallenden Kosten variieren je nach Mandantengröße erheblich. 

### <a name="storage-size-for-activity-logs"></a>Speichergröße für Aktivitätsprotokolle

Für jedes Überwachungsprotokollereignis werden ca. 2 KB an Datenspeicher verbraucht. Die Anmeldeprotokolle umfassen ca. 4 KB Datenspeicher. Für einen Mandanten mit 100.000 Benutzern, für den pro Tag ca. 1,5 Millionen Ereignisse anfallen, benötigen Sie also ungefähr 3 GB an Datenspeicher pro Tag. Da Schreibvorgänge in etwa 5-Minuten-Batches erfolgen, können Sie mit ca. 9.000 Schreibvorgängen pro Monat rechnen. 


Die folgende Tabelle enthält eine Schätzung der Kosten in Abhängigkeit von der Mandantengröße für ein universelles Speicherkonto (v2) in der Region „USA, Westen“ und einer Aufbewahrungsdauer von mindestens einem Jahr. Verwenden Sie den [Azure Storage-Preisrechner](https://azure.microsoft.com/pricing/details/storage/blobs/), um eine genauere Schätzung für die Datenmenge zu erstellen, mit der Sie für Ihre Anwendung rechnen.


| Protokollkategorie | Anzahl an Benutzern | Ereignisse pro Tag | Datenmenge pro Monat (Schätzung) | Kosten pro Monat (Schätzung) | Kosten pro Jahr (Schätzung) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100.000 | 1,5&nbsp;Millionen | 90 GB | 1,93 $ | 23,12 $ |
| Audit | 1\.000 | 15.000 | 900 MB | 0,02 $ | 0,24 $ |
| Anmeldungen | 1\.000 | 34.800 | 4 GB | 0,13 $ | 1,56 $ |
| Anmeldungen | 100.000 | 15&nbsp;Millionen | 1,7 TB | 35,41 $ | 424,92 $ |
 









### <a name="event-hub-messages-for-activity-logs"></a>Event Hub-Nachrichten für Aktivitätsprotokolle

Ereignisse werden zu Batchintervallen mit einer Länge von ca. fünf Minuten zusammengefasst und als einzelne Nachricht gesendet, die alle Ereignisse innerhalb dieses Zeitraums enthält. Eine Nachricht im Event Hub hat eine maximale Größe von 256 KB. Wenn die Gesamtgröße aller Nachrichten innerhalb des Zeitrahmens diese Menge übersteigt, werden mehrere Nachrichten gesendet. 

Für einen großen Mandanten mit mehr als 100.000 Benutzern treten pro Sekunde beispielsweise im Schnitt 18 Ereignisse auf. Dies entspricht einer Rate von 5.400 Ereignissen alle fünf Minuten. Da Überwachungsprotokolle ca. 2 KB pro Ereignis umfassen, entspricht dies 10,8 MB an Daten. Daher werden im Intervall von fünf Minuten 43 Nachrichten an den Event Hub gesendet. 

Die folgende Tabelle enthält die geschätzten Kosten pro Monat für einen einfachen Event Hub in der Region „USA, Westen“. Sie hängen von der Menge der Ereignisdaten ab, die von Mandant zu Mandant und abhängig von vielen Faktoren (wie benutzerspezifisches Anmeldeverhalten usw.) variieren können. Um eine genaue Schätzung des Datenvolumens zu berechnen, das Sie für Ihre Anwendung erwarten, verwenden Sie den [Event Hubs-Preisrechner](https://azure.microsoft.com/pricing/details/event-hubs/).

| Protokollkategorie | Anzahl an Benutzern | Ereignisse pro Sekunde | Ereignisse pro Fünf-Minuten-Intervall | Menge pro Intervall | Nachrichten pro Intervall | Nachrichten pro Monat | Kosten pro Monat (Schätzung) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100.000 | 18 | 5\.400 | 10,8 MB | 43 | 371.520 | 10,83 $ |
| Audit | 1\.000 | 0,1 | 52 | 104 KB | 1 | 8\.640 | 10,80 $ |
| Anmeldungen | 100.000 | 18000 | 5\.400.000 | 10,8 GB | 42.188 | 364.504.320 | 23,9 USD |  
| Anmeldungen | 1\.000 | 178 | 53.400 | 106,8&nbsp;MB | 418 | 3\.611.520 | 11,06 $ |  

### <a name="azure-monitor-logs-cost-considerations"></a>Kostenüberlegungen zu Azure Monitor-Protokollen



| Protokollkategorie | Anzahl an Benutzern | Ereignisse pro Tag | Ereignisse pro Monat (30 Tage) | Kosten pro Monat in US-Dollar (geschätzt) |
|:-|--|--|--|-:|
| Überwachung und Anmeldungen | 100.000 | 16.500.000 | 495.000.000 | 1093,00 US-Dollar |
| Audit | 100.000 | 1\.500.000 | 45.000.000 | 246,66 US-Dollar |
| Anmeldungen | 100.000 | 15.000.000 | 450.000.000 | 847,28 US-Dollar |










Informationen zum Überprüfen der Kosten im Zusammenhang mit der Verwaltung von Azure Monitor-Protokollen finden Sie unter [Verwalten der Kosten durch Steuerung der Datenmenge und -aufbewahrung in Azure Monitor-Protokollen](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Dieser Abschnitt beantwortet häufig gestellte Fragen und bespricht bekannte Probleme zu Azure AD-Protokollen in Azure Monitor.

**F: Welche Protokolle sind enthalten?**

**A:** Sowohl Anmeldeaktivitäts- als auch Überwachungsprotokolle können über dieses Feature weitergeleitet werden, B2C-bezogene Überwachungsereignisse sind derzeit noch nicht enthalten. Lesen Sie die Informationen zum [Überwachungsprotokollschema](./overview-reports.md) und [Anmeldeprotokollschema](reference-azure-monitor-sign-ins-log-schema.md), um zu ermitteln, welche Arten von Protokollen und featurebasierten Protokolle derzeit unterstützt werden. 

---

**F: Wie schnell werden die entsprechenden Protokolle nach einer Aktion in meinem Event Hub angezeigt?**

**A:** Die Protokolle werden innerhalb von zwei bis fünf Minuten nach dem Ausführen der Aktion in Ihrem Event Hub angezeigt. Weitere Informationen zu Event Hubs finden Sie unter [Was ist Azure Event Hubs?](../../event-hubs/event-hubs-about.md).

---

**F: Wie schnell werden die entsprechenden Protokolle nach einer Aktion in meinem Speicherkonto angezeigt?**

**A:** Bei Azure-Speicherkonten beträgt die Wartezeit zwischen 5 und 15 Minuten nach Durchführung der Aktion.

---

**F: Was geschieht, wenn ein Administrator die Aufbewahrungsdauer für eine Diagnoseeinstellung ändert?**

**A:** Die neue Aufbewahrungsrichtlinie wird auf nach der Änderung erfasste Protokolle angewendet. Vor der Richtlinienänderung gesammelte Protokolle sind nicht betroffen.

---

**F: Was kostet das Speichern meiner Daten?**

**A:** Die Speicherkosten richten sich nach der Größe Ihrer Protokolle und der gewählten Aufbewahrungsdauer. Eine Liste mit geschätzten Kosten pro Mandant, die von der Menge der erstellten Protokolle abhängen, finden Sie im Abschnitt [Speichergröße für Aktivitätsprotokolle](#storage-size-for-activity-logs).

---

**F: Wie viel kostet das Streamen meiner Daten an einen Event Hub?**

**A:** Die Streamingkosten richten sich nach der Anzahl von Nachrichten, die Sie pro Minute empfangen. In diesem Artikel wird erläutert, wie die Kosten berechnet werden. Außerdem umfasst er Kostenschätzungen ausgehend von der Anzahl von Nachrichten. 

---

**F: Wie integriere ich Azure AD-Aktivitätsprotokolle in mein SIEM-System?**

**A:** Hierzu stehen zwei Möglichkeiten zur Verfügung:

- Verwenden Sie Azure Monitor mit Event Hubs zum Streamen von Protokollen an Ihr SIEM-System. [Streamen Sie die Protokolle zunächst an einen Event Hub](tutorial-azure-monitor-stream-logs-to-event-hub.md), und [richten Sie anschließend Ihr SIEM-Tool mit dem konfigurierten Event Hub ein](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub). 

- Greifen Sie mit der [Reporting-Graph-API](concept-reporting-api.md) auf die Daten zu, und übertragen Sie diese dann mit Ihren eigenen Skripts in Ihr SIEM-System.

---

**F: Welche SIEM-Tools werden derzeit unterstützt?** 

**A:** **A:** Derzeit wird Azure Monitor von [Splunk](./howto-integrate-activity-logs-with-splunk.md), IBM QRadar, [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), [ArcSight](./howto-integrate-activity-logs-with-arcsight.md), LogRhythm und Logz.io unterstützt. Weitere Informationen zur Funktionsweise von Connectors finden Sie unter [Streamen von Azure-Überwachungsdaten an einen Event Hub für die Verwendung durch ein externes Tool](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

---

**F: Wie integriere ich Azure AD-Aktivitätsprotokolle in meine Splunk-Instanz?**

**A:** [Leiten Sie die Azure AD-Aktivitätsprotokolle zunächst an einen Event Hub weiter](./tutorial-azure-monitor-stream-logs-to-event-hub.md), und führen Sie dann die Schritte zum [Integrieren von Aktivitätsprotokollen in Splunk](./howto-integrate-activity-logs-with-splunk.md) aus.

---

**F: Wie integriere ich Azure AD-Aktivitätsprotokolle in Sumo Logic?** 

**A:** [Leiten Sie die Azure AD-Aktivitätsprotokolle zunächst an einen Event Hub weiter](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), und führen Sie dann die Schritte zum [Installieren der Azure AD-Anwendung und Anzeigen der Dashboards in SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) aus.

---

**F: Kann ich über einen Event Hub auf die Daten zugreifen, ohne ein externes SIEM-Tool zu verwenden?** 

**A:** Ja. Sie können die [Event Hubs-API](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) nutzen, um über Ihre benutzerdefinierte Anwendung auf die Protokolle zuzugreifen. 

---


## <a name="next-steps"></a>Nächste Schritte

* [Archivieren von Aktivitätsprotokollen in einem Speicherkonto](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Weiterleiten von Aktivitätsprotokollen an Event Hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Integrieren von Aktivitätsprotokollen in Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)
