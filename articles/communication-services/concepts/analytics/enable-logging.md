---
title: Azure Communication Services - Azure Monitor aktivieren
titleSuffix: An Azure Communication Services concept document
description: Konfigurieren von Kommunikationsdienstprotokollen und -metriken mit Diagnoseeinstellungen
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: f9de0567c3c07a10b780c4932a09e155f0e2ddea
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138834"
---
# <a name="enable-logs-via-diagnostic-settings-in-azure-monitor"></a>Aktivieren von Protokollen über die Diagnoseeinstellungen in Azure Monitor

## <a name="overview"></a>Übersicht

Communications Services bietet Überwachungs- und Analysefunktionen über [Azure Monitor Logs overview](/azure/azure-monitor/logs/data-platform-logs) und [Azure Monitor Metrics](/azure/azure-monitor/essentials/data-platform-metrics). Jede Azure-Ressource erfordert eine eigene Diagnoseeinstellung, die folgende Kriterien definiert:

- Kategorien der Protokolle und Metrikdaten, die an die in der Einstellung definierten Ziele gesendet werden. Die verfügbaren Kategorien sind je nach Ressourcentyp verschieden.
- Ein oder mehrere Ziele zum Senden der Protokolle. Zu den aktuellen Zielen gehören der Log Analytics-Arbeitsbereich, Event Hubs und Azure Storage.
- Mit einer einzelnen Diagnoseeinstellung kann maximal eines der Ziele definiert werden. Wenn Sie Daten an mehrere Ziele eines bestimmten Typs senden möchten (z.B. zwei verschiedene Log Analytics-Arbeitsbereiche), dann erstellen Sie mehrere Einstellungen. Jede Ressource kann bis zu fünf Diagnoseeinstellungen haben.

Im Folgenden finden Sie Anweisungen zur Konfiguration Ihrer Azure Monitor-Ressource, damit Sie Protokolle und Metriken für Ihre Kommunikationsdienste erstellen können. Eine ausführliche Dokumentation zur Verwendung von Diagnoseeinstellungen für alle Azure-Ressourcen finden Sie unter: [Diagnoseeinstellungen](/azure/azure-monitor/essentials/diagnostic-settings).

Diese Anweisungen gelten für die folgenden Protokolle der Kommunikationsdienste:

- [Anrufübersicht und Anrufdiagnoseprotokolle](call-logs-azure-monitor.md) Vorschau

## <a name="access-diagnostic-settings"></a>Zugriff auf Diagnoseeinstellungen

Um auf die Diagnoseeinstellungen für Ihre Kommunikationsdienste zuzugreifen, navigieren Sie zunächst zur Startseite Ihrer Kommunikationsdienste im Azure-Portal:

:::image type="content" source="media\enable-logging\portal-home-go-to-acs-resource.png" alt-text="Kommunikationsdienste Ressource":::

Klicken Sie dort auf "Diagnoseeinstellungen" im Abschnitt "Überwachung" des linken Navigationsbereichs:

:::image type="content" source="media\enable-logging\resource-diagnostic-settings-nav.png" alt-text="Diagnoseeinstellungen in der Navigation":::

Klicken Sie auf den Link "Diagnoseeinstellung hinzufügen" (beachten Sie die verschiedenen für Kommunikationsdienste verfügbaren Protokolle und Metrikquellen):

:::image type="content" source="media\enable-logging\diagnostic-setting-add.png" alt-text="Diagnoseeinstellungen Kategorie Details":::

## <a name="adding-a-diagnostic-setting"></a>Hinzufügen einer Diagnoseeinstellung

Sie werden dann aufgefordert, einen Namen für Ihre Diagnoseeinstellung zu wählen, was nützlich ist, wenn Sie viele Azure-Ressourcen überwachen möchten. Sie werden auch aufgefordert, die Protokoll- und Metrikdatenquellen auszuwählen, die Sie entweder als Protokolle oder als Metriken überwachen möchten. Siehe [Azure Monitor Datenplattform](/azure/azure-monitor/data-platform) für weitere Einzelheiten zu diesem Unterschied.

:::image type="content" source="media\enable-logging\diagnostic-setting-categories-details-acs.png" alt-text=" Hinzufügen einer Diagnoseeinstellung":::

## <a name="choose-destinations"></a>Reiseziele auswählen

Sie werden auch aufgefordert, einen Speicherort für die Protokolle auszuwählen. Plattformprotokolle und Metriken können an die in der folgenden Tabelle aufgeführten Ziele gesendet werden, die auch in der Azure Monitor-Dokumentation detaillierter beschrieben sind: [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD)

| Destination | BESCHREIBUNG |
|:------------|:------------|
| [Log Analytics-Arbeitsbereich](/azure/azure-monitor/logs/design-logs-deployment) | Das Senden von Protokollen und Metriken an einen Log Analytics-Arbeitsbereich ermöglicht es Ihnen, diese zusammen mit anderen von Azure Monitor gesammelten Überwachungsdaten mithilfe von leistungsstarken Protokollabfragen zu analysieren und auch andere Azure Monitor-Funktionen wie Warnungen und Visualisierungen zu nutzen. |
| [Event Hubs](/azure/event-hubs/) | Das Senden von Protokollen und Metriken an Event Hubs ermöglicht Ihnen das Streamen von Daten an externe Systeme, z. B. SIEMs und andere Protokollanalyselösungen von Drittanbietern. |
| [Azure-Speicherkonto](/azure/storage/blobs/) | Das Archivieren von Protokollen und Metriken in einem Azure Storage-Konto ist für die Überwachung, statische Analyse oder Sicherung nützlich. Im Vergleich zu Azure Monitor-Protokollen und einem Log Analytics Arbeitsbereich ist Azure Storage kostengünstiger, und die Protokolle können unbegrenzt aufbewahrt werden. |

Die folgenden Einstellungen sind ein Beispiel dafür, was Sie in Ihrer Ressource Kommunikationsdienste sehen würden:

:::image type="content" source="media\enable-logging\diagnostic-setting-destination-acs.png" alt-text="Diagnoseeinstellungen Ziel Details":::

Dies sind alles praktikable und flexible Optionen, die sich an Ihre spezifischen Speicheranforderungen anpassen lassen. Wir bieten jedoch weitere Funktionen und integrierte analytische Einblicke, wenn die Option Log Analytics Arbeitsbereich gewählt wird.

## <a name="log-analytics-workspace-for-additional-analytics-features"></a>Log Analytics Arbeitsbereich für zusätzliche Analysefunktionen

Wenn Sie sich dafür entscheiden, Ihre Protokolle an einen [Log Analytics-Arbeitsbereich](/azure/azure-monitor/logs/log-analytics-overview) zu senden, aktivieren Sie mehr Funktionen in Azure Monitor im Allgemeinen und für Ihre Kommunikationsdienste. Log Analytics ist ein Tool im Azure-Portal, mit dem Sie [Abfragen](/azure/azure-monitor/logs/queries) mit Daten in Ihren Azure Monitor-Protokollen und -Metriken sowie [Arbeitsmappen](/azure/azure-monitor/visualize/workbooks-overview), [Benachrichtigungen](/azure/azure-monitor/alerts/alerts-log), [Benachrichtigungsaktionen](/azure/azure-monitor/alerts/action-groups), [REST-API-Zugang](https://dev.loganalytics.io/) und viele andere erstellen, bearbeiten und ausführen können.

Für Ihre Kommunikationsdienstprotokolle haben wir ein nützliches [Standardabfragepaket](/azure/azure-monitor/logs/query-packs#default-query-pack) bereitgestellt, das erste Einblicke zur schnellen Analyse und zum Verständnis Ihrer Daten bietet. Diese Abfragepakete werden hier beschrieben: [Log Analytics für Kommunikationsdienste](log-analytics.md). Wir haben auch viele Einblicke und Visualisierungen mithilfe von Arbeitsmappen erstellt, die in beschrieben werden: [Arbeitsmappen für Kommunikationsdienstprotokolle](insights.md).
