---
title: Erhalten von Benachrichtigungen über Probleme mithilfe von Warnungen – Azure IoT Edge
description: Verwenden von Azure Monitor-Warnungsregeln zur Überwachung im großen Stil
author: veyalla
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1de507f113f5bf457a4394be9e5fb86c37291e95
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350060"
---
# <a name="get-notified-about-issues-using-alerts-preview"></a>Erhalten von Benachrichtigungen über Probleme mithilfe von Warnungen (Vorschau)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Anhand von [Azure Monitor-Protokollwarnungen](../azure-monitor/alerts/alerts-unified-log.md) können Sie IoT Edge-Geräte im großen Stil überwachen. Wie in der [Lösungsarchitektur](how-to-collect-and-transport-metrics.md#architecture) hervorgehoben, wird Azure Monitor Log Analytics als Metrikdatenbank verwendet. Diese Integration bietet leistungsstarke und flexible Warnungsfunktionen mithilfe von ressourcenzentrierten Protokollwarnungen.

> [!IMPORTANT]
> Dieses Feature steht zurzeit nur für IoT Hub und nicht für IoT Central zur Verfügung.

## <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel

Sie können [eine Protokollwarnungsregel erstellen](../azure-monitor/alerts/alerts-log.md), um ein breites Spektrum an Bedingungen für Ihre Geräteflotte zu überwachen.

[KQL](/azure/data-explorer/kusto/query/)-Beispielwarnungsabfragen werden unter der IoT Hub-Ressource bereitgestellt. Abfragen, die Metrikdaten aus Edgegeräten verarbeiten, wird im Titel *IoT Edge:* vorangestellt. Verwenden Sie diese Beispiele unverändert, oder ändern Sie sie nach Bedarf, um eine Abfrage für genau Ihre Anforderungen zu erstellen.

Führen Sie zum Zugreifen auf die Beispielwarnungsabfragen die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.
1. Wählen Sie im Abschnitt **Überwachung** des Menüs die Option **Protokolle** aus.
1. Wählen Sie **Abfragen** aus, um den Beispielabfragebrowser zu öffnen.

:::image type="content" source="./media/how-to-create-alerts/example-alerts.png" alt-text="Zugreifen auf Beispielwarnungsabfragen" lightbox="./media/how-to-create-alerts/example-alerts.png":::

Das [Metrikensammlermodul](how-to-collect-and-transport-metrics.md#metrics-collector-module) erfasst alle Daten in der Standardtabelle [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics). Sie können Warnungsregeln basierend auf Metrikdaten aus benutzerdefinierten Modulen erstellen, indem Sie dieselbe Tabelle abfragen.

### <a name="split-by-device-dimension"></a>Aufteilen nach Gerätedimension

Alle Abfragen von Beispielwarnungsregeln aggregieren Werte nach Geräte-ID. Diese Gruppierung ist erforderlich, um zu ermitteln, welches Gerät die Warnung ausgelöst hat. Sie können bestimmte Geräte auswählen, um die Warnungsregel zu aktivieren oder auf allen Geräten zu aktivieren. Untersuchen Sie anhand des Vorschaudiagramms den Trend pro Gerät, bevor Sie die Warnungslogik festlegen.

### <a name="choose-notification-preferences"></a>Auswählen von Benachrichtigungseinstellungen

Konfigurieren Sie Ihre Benachrichtigungseinstellungen in einer [Aktionsgruppe](../azure-monitor/alerts/action-groups.md), und ordnen Sie sie beim Erstellen einer Warnungsregel einer Warnung zu.

## <a name="select-alert-rule-scope"></a>Auswählen eines Warnungsregelbereichs

Anhand der Anleitung im vorstehenden Abschnitt wird eine Warnungsregel erstellt, die für einen einzelnen IoT-Hub gilt. Möglicherweise möchten Sie dieselbe Regel jedoch für mehrere IoT-Hubs erstellen. Ändern Sie den Bereich in eine Ressourcengruppe oder ein ganzes Abonnement, um die Warnungsregel für alle Hubs innerhalb dieses Bereichs zu aktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.
1. Wählen Sie im Abschnitt **Überwachung** des Menüs die Option **Protokolle** aus.
1. Wählen Sie **Bereich auswählen** aus, um den Bereich einer Warnungsregel zu ändern.

:::image type="content" source="./media/how-to-create-alerts/change-scope.png" alt-text="Ändern des Warnungsbereichs" lightbox="./media/how-to-create-alerts/change-scope.png":::

Aggregieren Sie Werte nach dem Feld `_ResourceId`, und wählen Sie es beim Erstellen der Warnungsregel als Spalte *Ressourcen-ID* aus. Bei diesem Ansatz wird der Einfachheit halber eine Warnung der richtigen Ressource zugeordnet.

## <a name="viewing-alerts"></a>Anzeigen von Warnungen

Weitere Informationen finden Sie auf der Registerkarte **Warnungen** der Arbeitsmappe [IoT Edge-Bestandsansicht](how-to-explore-curated-visualizations.md#iot-edge-fleet-view-workbook) unter „Warnungen, die für Geräte über mehrere IoT Hubs hinweg generiert wurden“.

Klicken Sie auf den Namen der Warnungsregel, um mehr Kontext zur Warnung anzuzeigen. Wenn Sie auf den Link „Gerätename“ klicken, werden die detaillierten Metriken für das Gerät um den Zeitpunkt herum angezeigt, zu dem die Warnung ausgelöst wurde.

## <a name="next-steps"></a>Nächste Schritte

Erweitern Sie Ihre Überwachungslösung mit [Metriken aus benutzerdefinierten Modulen](how-to-add-custom-metrics.md).