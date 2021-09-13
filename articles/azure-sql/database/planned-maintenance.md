---
title: Planen von Azure-Wartungsereignissen
description: Es wird beschrieben, wie Sie Ereignisse der geplanten Wartung für Azure SQL-Datenbank und für verwaltete Azure SQL-Instanzen vorbereiten.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: mathoma
ms.date: 3/23/2021
ms.openlocfilehash: a697c0a3095963760d6a95159790c02cbab14d71
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121628"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planen von Azure-Wartungsereignissen in Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Es wird beschrieben, wie Sie Ereignisse der geplanten Wartung in Ihrer Datenbank für Azure SQL-Datenbank und für verwaltete Azure SQL-Instanzen vorbereiten.

## <a name="what-is-a-planned-maintenance-event"></a>Was ist ein geplantes Wartungsereignis?

Damit die Dienste Azure SQL-Datenbank und Azure SQL Managed Instance sicher, konform, stabil und leistungsfähig bleiben, werden nahezu fortlaufend Updates über die Dienstkomponenten ausgeführt. Dank der modernen und robusten Dienstarchitektur und innovativer Technologien wie [Hotpatching](https://aka.ms/azuresqlhotpatching) sind die meisten Updates in Bezug auf die Dienstverfügbarkeit vollständig transparent und stellen keine Beeinträchtigung dar. Trotzdem verursachen einige Arten von Updates kurze Dienstunterbrechungen und erfordern eine besondere Behandlung. 

Während der geplanten Wartung werden die Mitglieder des Datenbankquorums nacheinander mit der Absicht offline geschaltet, dass ein antwortendes primäres Replikat vorhanden ist. Für Datenbanken der Kategorien Unternehmenskritisch und Premium bleibt auch mindestens ein sekundäres Replikat online, um sicherzustellen, dass keine Clientausfälle auftreten. Wenn das primäre Replikat offline geschaltet werden muss, erfolgt ein Neukonfigurationsprozess. Für Datenbanken der Kategorien Unternehmenskritisch und Premium wird eines der sekundären Replikate zum neuen primären Replikat. Für Universell-, Standard- und Basic-Datenbanken wird das primäre Replikat auf einen anderen zustandslosen Computeknoten mit ausreichend freier Kapazität verschoben.

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Was Sie bei einem geplanten Wartungsereignis erwarten können

Das Wartungsereignis kann je nach der Konstellation der primären und sekundären Replikate zu Beginn des Wartungsereignisses eine einzelne oder mehrere Neukonfiguration(en) erzeugen. Durchschnittlich gibt es 1,7 Neukonfigurationen pro geplantem Wartungsereignis. Neukonfigurationen werden normalerweise innerhalb von 30 Sekunden abgeschlossen. Der Mittelwert beträgt acht Sekunden. Wenn sie bereits verbunden ist, muss Ihre Anwendung mit dem neuen primären Replikat Ihrer Datenbank erneut eine Verbindung herstellen. Wenn eine neue Verbindung versucht wird, während für die Datenbank eine Neukonfiguration durchgeführt wird, bevor das neue primäre Replikat online ist, erhalten Sie Fehler 40613 (Datenbank nicht verfügbar): *Die Datenbank "{Datenbankname}" auf Server "{Servername}" ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später.“* Wenn Ihre Datenbank gerade eine zeitintensive Abfrage ausführt, wird diese bei einer Neukonfiguration unterbrochen und muss neu gestartet werden.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Simulieren eines geplanten Wartungsereignisses

Wenn Sie sicherstellen, dass Ihre Clientanwendung vor der Bereitstellung in der Produktion resilient gegenüber Wartungsereignissen ist, können Sie das Risiko von Anwendungsfehlern verringern und zur Anwendungsverfügbarkeit für Ihre Endbenutzer beitragen. Sie können das Verhalten Ihrer Clientanwendung während geplanter Wartungsereignisse testen, indem Sie die [Resilienz von Anwendungsfehlern](./high-availability-sla.md#testing-application-fault-resiliency) über PowerShell, die CLI oder die REST-API testen. Weitere Informationen finden Sie auch unter [Initiieren eines manuellen Failovers](https://aka.ms/mifailover-techblog) für Verwaltete Instanz. Es wird ein identisches Verhalten wie ein Wartungsereignis erzeugen, bei dem die primäre Replik offline geschaltet wird.

## <a name="retry-logic"></a>Wiederholungslogik

Jede Clientproduktionsanwendung, die eine Verbindung mit einem Clouddatenbankdienst herstellt, sollte eine stabile [Wiederholungslogik](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) für Verbindungen implementieren. Dies wird dazu beitragen, Neukonfigurationen für die Endbenutzer transparent zu gestalten oder wenigstens negative Auswirkungen zu minimieren.

### <a name="service-health-alert"></a>Service Health-Warnung

Wenn Sie Warnungen im Zusammenhang mit Dienstproblemen oder geplanten Wartungsaktivitäten erhalten möchten, können Sie im Azure-Portal Service Health-Warnungen mit entsprechenden Ereignistypen und Aktionsgruppen verwenden. Weitere Informationen finden Sie unter [Erstellen einer Service Health-Warnung über das Azure-Portal](../../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

## <a name="resource-health"></a>Ressourcenintegrität

Wenn für Ihre Datenbank Anmeldefehler auftreten, sollten Sie im Fenster [Resource Health](../../service-health/resource-health-overview.md#get-started) im [Azure-Portal](https://portal.azure.com) den aktuellen Status überprüfen. Der Abschnitt „Integritätsverlauf“ enthält den Grund für die Ausfallzeit für jedes Ereignis (wenn verfügbar).

## <a name="maintenance-window-feature"></a>Funktion „Wartungsfenster“

Die Wartungsfensterfunktion bietet die Möglichkeit, Zeitpläne für vorhersehbare Wartungsfenster für berechtigte Azure SQL-Datenbanken und verwaltete SQL-Instanzen zu konfigurieren. Weitere Informationen finden Sie unter [Wartungsfenster](maintenance-window.md).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Resource Health](resource-health-to-troubleshoot-connectivity.md) für Azure SQL-Datenbank und Azure SQL Managed Instance.
- Weitere Informationen zur Wiederholungslogik finden Sie unter [Wiederholungslogik für vorübergehende Fehler](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- Konfigurieren von Zeitplänen für Wartungsfenster mit der Funktion [Wartungsfenster](maintenance-window.md).