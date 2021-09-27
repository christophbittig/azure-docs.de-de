---
title: Verwenden von API-Analysen in Azure API Management | Microsoft-Dokumentation
description: Verwenden Sie Analysen in Azure API Management, um die Nutzung ihrer APIs und deren Leistung besser zu verstehen und zu kategorisieren.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: danlep
ms.openlocfilehash: 07ac5abfb3357d5d36496d12f34853257bc76a4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585069"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Abrufen von API-Analysen in Azure API Management

Azure API Management bietet integrierte Analysen für Ihre APIs. Analysieren Sie die Nutzung und Leistung der APIs in Ihrer API Management-Instanz über mehrere Dimensionen hinweg, einschließlich:

* Time
* Geografie
* APIs
* API-Vorgänge
* Produkte
* Abonnements
* Benutzer
* Requests

> [!NOTE]
> Geografiewerte basieren ungefähr auf der IP-Adresszuordnung.

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Zeitachsenanalysen im Portal":::

Verwenden Sie Analysen für die allgemeine Überwachung und Problembehandlung Ihrer APIs. Weitere Überwachungsfunktionen, einschließlich Metriken und Ressourcenprotokollen in nahezu Echtzeit für die Diagnose und Überwachung, finden Sie im [Tutorial: Überwachen von veröffentlichten APIs](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analysen – Portal

Verwenden Sie das Azure-Portal, um die Analysedaten auf einen Blick für Ihre API Management-Instanz zu überprüfen.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz. 
1. Wählen Sie im Menü auf der linken Seite unter **Überwachung** die Option **Analysen** aus.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Auswählen von „Analysen“ für die API Management-Instanz im Portal":::  
1. Wählen Sie einen Zeitbereich für die Daten aus, oder geben Sie einen benutzerdefinierten Zeitbereich ein.
1. Wählen Sie eine Berichtskategorie für Analysedaten aus, z. B. **Zeitachse**, **Geografie** usw.
1. Filtern Sie den Bericht optional nach mindestens einer zusätzlichen Kategorie.

## <a name="analytics---rest-api"></a>Analysen – REST-API

Verwenden Sie [Berichte](/rest/api/apimanagement/2020-12-01/reports)vorgänge in der API Management REST-API, um Analysedaten für Ihre API Management-Instanz abzurufen und zu filtern.

Verfügbare Vorgänge geben Berichtsdatensätze nach API, Geografie, API-Vorgängen, Produkt, Anforderung, Abonnement, Uhrzeit oder Benutzer zurück.

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in Azure Monitor-Funktionen in API Management finden Sie im [Tutorial: Überwachen von veröffentlichten APIs](api-management-howto-use-azure-monitor.md).
* Detaillierte Informationen zur HTTP-Protokollierung und -Überwachung finden Sie unter [Überwachen Ihrer APIs mit Azure API Management, Event Hubs und Moesif](api-management-log-to-eventhub-sample.md).
* Informationen zum Integrieren von [Azure API Management in Azure Application Insights](api-management-howto-app-insights.md).
