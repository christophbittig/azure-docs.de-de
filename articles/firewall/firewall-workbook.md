---
title: Überwachen von Protokollen mit Azure Firewall Workbook
description: Azure Firewall-Workbooks bieten einen flexiblen Bereich für die Azure Firewall-Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Azure-Portals.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 11/01/2021
ms.author: victorh
ms.openlocfilehash: 207097258a78d79d77e56052fc254065ca8a293c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457613"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Überwachen von Protokollen mit Azure Firewall Workbook

Azure Firewall Workbook bietet einen flexiblen Bereich für die Azure Firewall-Datenanalyse. Sie können damit umfangreiche visuelle Berichte innerhalb des Azure-Portals erstellen. Sie können mehrere in Azure bereitgestellte Firewalls nutzen und sie zu vereinheitlichten interaktiven Oberflächen kombinieren.

Sie gewinnen Einblicke in Azure Firewall-Ereignisse, erhalten Informationen zu Ihren Anwendungs- und Netzwerkregeln und sehen Statistiken zu Firewallaktivitäten für URLs, Ports und Adressen. Mit Azure Firewall Workbook können Sie Ihre Firewalls und Ressourcengruppen filtern und mit leicht lesbaren Datasets dynamisch nach Kategorien filtern, wenn Sie ein Problem in Ihren Protokollen untersuchen. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, sollten Sie die [Diagnoseprotokollierung](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) über das Azure-Portal aktivieren. Lesen Sie auch den Artikel [Azure Firewall-Protokolle und -Metriken](logs-and-metrics.md), um einen Überblick über die für Azure Firewall verfügbaren Diagnoseprotokolle und Metriken zu erhalten.

## <a name="get-started"></a>Erste Schritte

Wechseln Sie zum Bereitstellen der Arbeitsmappe zu [Azure Monitor Workbook for Azure Firewall](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) (Azure Monitor-Arbeitsmappe für Azure Firewall) und befolgen Sie die Anweisungen auf der Seite. Azure Firewall Workbook ist für das Arbeiten mit mehreren Mandanten und Abonnements konzipiert und für mehrere Firewalls filterbar.

## <a name="overview-page"></a>Seite „Übersicht“

Die Übersichtsseite ermöglicht, Arbeitsbereiche, Zeit und Firewalls zu filtern. Sie zeigt Ereignisse chronologisch Firewalls und Protokolltypen (Anwendung, Netzwerke, Threat Intelligence, DNS-Proxy) übergreifend an.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Übersicht zu Azure Firewall Workbook":::

## <a name="application-rule-log-statistics"></a>Anwendungsregelprotokoll-Statistik

Auf dieser Seite werden die eindeutigen Quellen der IP-Adresse im Zeitverlauf, die Nutzungshäufigkeit der Anwendungsregeln, der verweigerte/zulässige FQDN im Zeitverlauf und gefilterte Daten angezeigt. Sie können Daten auf der IP-Adresse basierend filtern. 

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Anwendungsregelprotokoll von Azure Firewall Workbook":::

In der Ansicht Webkategorien werden alle Aktionen zum Zulassen und Verweigern des Zugriffsprotokolls basierend auf dem Schweregrad zusammengefasst, der vom Firewalladministrator konfiguriert wurde.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-webcategory.png" alt-text="Azure Firewall Web-Kategorie Zusammenfassung":::

## <a name="network-rule-log-statistics"></a>Netzwerkregelprotokoll-Statistik

Diese Seite bietet eine Ansicht nach Regelaktion – zulassen/verweigern, Zielport nach IP und DNAT im Zeitverlauf. Sie können auch nach Aktion, Port und Zieltyp filtern.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Netzwerkregelprotokoll von Azure Firewall Workbook":::

Sie können Protokolle auch auf Zeitfensterbasis filtern:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Netzwerkregelprotokoll-Zeitfenster von Azure Firewall Workbook":::

## <a name="idps-log-statistics"></a>Abfrageprotokollstatistik

Diese Seite bietet einen Überblick über die Anzahl der IDPS-Aktionen für den gesamten Verkehr, der den IDPS-Regeln entspricht: Protokoll, Signatur-ID, Quell-IP.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-idps.png" alt-text="Azure Firewall Workbook idps Protokoll":::

## <a name="investigations"></a>Untersuchungen

Sie können die Protokolle ansehen und auf Basis der Quell-IP-Adresse mehr über die Ressource erfahren. Sie können Informationen wie den Namen des virtuellen Computers und der Netzwerkschnittstelle erhalten. Es ist einfach, in den Protokollen nach der Ressource zu filtern.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure Firewall Workbook-Untersuchung":::


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Firewall-Diagnose](firewall-diagnostics.md).
