---
title: include file
description: include file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/07/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ec263b7e6c5c5f2191f207cf40ddc43b69fa6ad
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096955"
---
In der folgenden Tabelle sind die Kontingentinformationen für das Azure Service Bus Messaging aufgeführt. Informationen zu Preisen und anderen Kontingenten für Service Bus finden Sie unter [Service Bus – Preise](https://azure.microsoft.com/pricing/details/service-bus/).

| Namen des Kontingents | `Scope` | Wert | Notizen | 
| --- | --- | --- | --- |
| Maximale Anzahl von Namespaces pro Azure-Abonnement |Namespace |  1000 (Standard und Maximum) |Nachfolgende Anforderungen weiterer Namespaces werden abgelehnt. |
| Größe von Warteschlangen oder Themen |Entität | <p>1, 2, 3, 4 GB oder 5 GB</p><p>In der Premium-SKU und in der Standard-SKU mit aktivierter [Partitionierung](../articles/service-bus-messaging/service-bus-partitioning.md) beträgt die maximale Größe von Warteschlangen bzw. Themen 80 GB.</p><p>Der Grenzwert für die Gesamtgröße eines Premium-Namespace beträgt 1 TB pro [Messagingeinheit](../articles/service-bus-messaging/service-bus-premium-messaging.md). Die Gesamtgröße aller Entitäten im Namespace kann diesen Grenzwert nicht überschreiten.</p> | Wird beim Erstellen/Aktualisieren der Warteschlange oder des Themas definiert. <br/><br/> Nachfolgende eingehende Nachrichten werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. |
| Maximale Anzahl gleichzeitiger Verbindungen in einem Namespace |Namespace |.NET-Messaging: 1.000.<br /><br />AMQP: 5.000 | Nachfolgende Anforderungen für zusätzliche Verbindungen werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. REST-Vorgänge werden nicht zu den gleichzeitigen TCP-Verbindungen hinzugezählt. |
| Anzahl der gleichzeitigen Empfangsanforderungen für eine Warteschlangen-, Themen- oder Abonnemententität |Entität | 5\.000 |Nachfolgende Empfangsanforderungen werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Dieses Kontingent gilt für alle gleichzeitigen Empfangsvorgänge über alle Abonnements eines Themas hinweg. |
| Anzahl von Themen oder Warteschlangen pro Namespace |Namespace | 10.000 für den Basic- oder Standard-Tarif. Die Gesamtzahl von Themen und Warteschlangen in einem Namespace muss kleiner oder gleich 10.000 sein. <br/><br/>Im Premium-Tarif ist ein Wert von 1.000 pro Messagingeinheit (Messaging Unit, MU) zulässig. | Nachfolgende Anforderungen zum Erstellen eines neuen Themas bzw. einer neuen Warteschlange im Namespace werden abgelehnt. Als Ergebnis wird bei der Konfiguration über das [Azure-Portal][Azure portal] eine Fehlermeldung generiert. Bei einem Aufruf über die Verwaltungs-API wird vom aufrufenden Code eine Ausnahme empfangen. |
| Anzahl von [partitionierten Themen oder Warteschlangen](../articles/service-bus-messaging/service-bus-partitioning.md) pro Namespace |Namespace | Basic- und Standard-Tarif: 100.<br/><br/>Partitionierte Entitäten werden im [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)-Tarif nicht unterstützt.<br/><br />Jede partitionierte Warteschlange bzw. jedes partitionierte Thema wird in das zulässige Kontingent von 1.000 Entitäten pro Namespace eingerechnet. | Nachfolgende Anforderungen zum Erstellen eines neuen partitionierten Themas bzw. einer neuen Warteschlange im Namespace werden abgelehnt. Als Ergebnis wird bei der Konfiguration über das [Azure-Portal][Azure portal] eine Fehlermeldung generiert. Bei einem Aufruf über die Verwaltungs-API wird vom aufrufenden Code die Ausnahme **QuotaExceededException** empfangen. <p>Wenn Sie mehr partitionierte Entitäten in einem Namespace mit Basic- oder Standard-Tarif haben möchten, erstellen Sie zusätzliche Namespaces. </p>|
| Maximale Größe eines Messagingentitätspfads: Warteschlange oder Thema |Entität |- |260 Zeichen |
| Maximale Größe eines Messagingentitätsnamens: Namespace, Abonnement oder Abonnementregel |Entität |- |50 Zeichen |
| Maximale Größe der message ID | Entität |- | 128 |
| Maximale Größe der Sitzungs-ID einer Nachricht | Entität |- | 128 |
| Nachrichtengröße für eine Warteschlangen-, Themen- oder Abonnemententität |Entität |Eingehende Nachrichten, die diese Kontingente überschreiten, werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. | 256 KB für [Standard-Tarif](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/> 100 MB für [Premium-Tarif](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Die Nachrichtengröße umfasst die Größe der Eigenschaften (System- und Benutzereigenschaften) und die Größe der Nutzdaten. Die Größe der Systemeigenschaften variiert je nach Szenario. |
| Nachrichteneigenschaftsgröße für eine Warteschlangen-, Themen- oder Abonnemententität |Entität | Die Ausnahme `SerializationException` wird generiert. | <p>Die maximale Nachrichteneigenschaftsgröße für jede Eigenschaft beträgt 32 KB.</p><p>Die kumulierte Größe aller Eigenschaften darf 64 KB nicht überschreiten. Dieser Grenzwert gilt für den gesamten Header der im Broker gespeicherten Nachricht, die sowohl über Benutzereigenschaften als auch Systemeigenschaften verfügt (z. B. Sequenznummer, Bezeichnung und Nachrichten-ID).</p><p>Maximale Anzahl der Headereigenschaften im Eigenschaftenbehälter: **byte/int.MaxValue**</p> |
| Anzahl von Abonnements pro Thema |Entität |Nachfolgende Anforderungen zum Erstellen weiterer Abonnements für das Thema werden abgelehnt. Als Ergebnis wird bei der Konfiguration über das Portal eine Fehlermeldung angezeigt. Bei einem Aufruf über die Verwaltungs-API wird vom aufrufenden Code eine Ausnahme empfangen. |2\.000 pro Thema für die Ebenen „Standard“ und „Premium“ |
| Anzahl von SQL-Filtern pro Thema |Entität |Nachfolgende Anforderungen für die Erstellung weiterer Filter für das Thema werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |2\.000 |
| Anzahl von Korrelationsfiltern pro Thema |Entität |Nachfolgende Anforderungen für die Erstellung weiterer Filter für das Thema werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |100.000 |
| Größe der SQL-Filter oder -Aktionen |Namespace |Nachfolgende Anforderungen für die Erstellung weiterer Filter werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |Maximale Länge der Filterbedingungszeichenfolge: 1.024 (1 K)<br /><br />Maximale Länge der Regelaktionszeichenfolge: 1.024 (1 K)<br /><br />Maximale Anzahl von Ausdrücken pro Regelaktion: 32. |
| Anzahl der Autorisierungsregeln für gemeinsamen Zugriff pro Namespace, Warteschlange oder Thema |Entität, Namespace |Nachfolgende Anforderungen für die Erstellung weiterer Regeln werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |Maximale Anzahl von Regeln pro Entitätstyp: 12. <br /><br /> Regeln, die für einen Service Bus-Namespace konfiguriert werden, gelten für alle Typen: Warteschlangen, Themen. |
| Anzahl der Nachrichten pro Transaktion | Transaktion | Zusätzliche eingehende Nachrichten werden abgelehnt, und eine Ausnahme („Mehr als 100 Nachrichten dürfen nicht in einer einzelnen Transaktion gesendet werden“) wird durch den aufrufenden Code empfangen. | 100 <br /><br /> Für **Send()** - und **SendAsync()** -Vorgänge. |
| Anzahl von Regeln für virtuelle Netzwerke und IP-Filter | Namespace | &nbsp; | 128 |

[Azure portal]: https://portal.azure.com
