---
title: Datenconnectors für Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Datenquellen wie Microsoft 365 Defender (vormals Microsoft Threat Protection), Microsoft 365 und Office 365, Azure AD, ATP und Defender für Cloud-Apps mit Microsoft Sentinel verbinden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8e8ac4ba23a548138eaffc7a0efeb89082af3475
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517005"
---
# <a name="microsoft-sentinel-data-connectors"></a>Microsoft Sentinel-Datenconnectors

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Nachdem Sie Microsoft Sentinel in Ihren Arbeitsbereich integriert haben, verbinden Sie Datenquellen, um mit der Erfassung Ihrer Daten in Microsoft Sentinel zu beginnen. Microsoft Sentinel verfügt über viele Connectors für Microsoft-Produkte, die sofort verfügbar sind und Echtzeitintegration bieten. Beispielsweise umfassen Dienst-zu-Dienst-Connectors Microsoft 365 Defender-Connectors und Microsoft 365-Quellen wie Office 365, Azure Active Directory (Azure AD), Microsoft Defender for Identity und Microsoft Defender für Cloud-Apps.

Sie können auch Out-of-the-Box-Connectors für das breitere Sicherheitsökosystem für Nicht-Microsoft-Produkte aktivieren. Beispielsweise können Sie [Syslog](#syslog), [Common Event Format (CEF)](#common-event-format-cef) oder [REST-APIs](#rest-api-integration) verwenden, um Ihre Datenquellen mit Microsoft Sentinel zu verbinden.

Die Seite **Datenconnectors** ist über das Microsoft Sentinel-Navigationsmenü zugänglich und zeigt die vollständige Liste der von Microsoft Sentinel bereitgestellten Connectors und deren Status in Ihrem Arbeitsbereich an. Wählen Sie den gewünschten Connector für die Verbindungsherstellung und anschließend **Connectorseite öffnen** aus.

![Datenconnectors-Katalog](./media/collect-data/collect-data-page.png)

In diesem Artikel werden unterstützte Datenverbindungsmethoden beschrieben. Weitere Informationen finden Sie in der [Referenz zu Microsoft Sentinel-Datenconnectors](data-connectors-reference.md) sowie im [Microsoft Sentinel-Lösungskatalog](sentinel-solutions-catalog.md).

<a name="agent-options"></a>
<a name="data-connection-methods"></a>
<a name="map-data-types-with-microsoft-sentinel-connection-options"></a>

## <a name="enable-a-data-connector"></a>Aktualisieren eines Datenconnectors

Die Seite **Datenconnectors** ist über das Microsoft Sentinel-Navigationsmenü zugänglich und zeigt die vollständige Liste der von Microsoft Sentinel bereitgestellten Connectors und deren Status an. Wählen Sie den gewünschten Connector für die Verbindungsherstellung und anschließend **Connectorseite öffnen** aus.

![Datenconnectors-Katalog](./media/collect-data/collect-data-page.png)

Sie müssen alle Voraussetzungen erfüllen. Vollständige Anweisungen zum Erfassen von Daten in Microsoft Sentinel werden Ihnen auf der Connectorseite angezeigt. Es kann einige Zeit dauern, bis die Daten eintreffen. Nach erfolgreicher Verbindungsherstellung werden eine Zusammenfassung der Daten im Diagramm **Empfangene Daten** sowie der Konnektivitätsstatus der Datentypen angezeigt.

![Konfigurieren von Datenconnectors](./media/collect-data/opened-connector-page.png)

Auf der Registerkarte **Nächste Schritte** werden zusätzliche Inhalte angezeigt, die Microsoft Sentinel für den jeweiligen Datentyp bereitstellt: Beispielabfragen, Visualisierungsarbeitsmappen und Analyseregelvorlagen, die Sie bei der Erkennung und Untersuchung von Bedrohungen unterstützen.

![Nächste Schritte für Connectors](./media/collect-data/data-insights.png)

Weitere Informationen finden Sie im entsprechenden Abschnitt für Ihren Datenconnector in der [Referenz zu Datenconnectors](data-connectors-reference.md).

## <a name="rest-api-integration"></a>REST-API-Integration

Viele Sicherheitstechnologien stellen eine Reihe von APIs zum Abrufen von Protokolldateien zur Verfügung, und einige Datenquellen können diese APIs verwenden, um eine Verbindung mit Microsoft Sentinel herzustellen.

Datenconnectors, die APIs verwenden, können entweder auf Anbieterseite oder mit Azure Functions integriert werden, wie in den folgenden Abschnitten beschrieben.

Eine vollständige Liste und Informationen zu diesen Connectors finden Sie in der [Referenz zu Datenconnectors](data-connectors-reference.md).

### <a name="rest-api-integration-on-the-provider-side"></a>REST-API-Integration auf Anbieterseite

Eine API-Integration, die vom Anbieter erstellt wird, stellt eine Verbindung mit den Anbieterdatenquellen her und pusht Daten mithilfe der [Azure Monitor-Datensammler-API](../azure-monitor/logs/data-collector-api.md) in benutzerdefinierte Protokolltabellen von Microsoft Sentinel.

Weitere Informationen finden Sie in der Dokumentation Ihres Anbieters und unter [Verbinden Ihrer Datenquelle mit der REST-API von Microsoft Sentinel zur Erfassung von Daten](connect-rest-api-template.md).

### <a name="rest-api-integration-using-azure-functions"></a>REST-API-Integration mit Azure Functions

Integrationen, die [Azure Functions](../azure-functions/index.yml) verwenden, um eine Verbindung mit einer Anbieter-API herzustellen, formatieren zuerst die Daten und senden sie dann mithilfe der [Azure Monitor-Datensammler-API](../azure-monitor/logs/data-collector-api.md) an benutzerdefinierte Protokolltabellen von Microsoft Sentinel.

Um diese Datenconnectors für das Herstellen einer Verbindung mit der Anbieter-API und für das Sammeln von Protokollen in Microsoft Sentinel zu konfigurieren, befolgen Sie die für den jeweiligen Datenconnector in Microsoft Sentinel angezeigten Schritte.

Weitere Informationen finden Sie unter [Verbinden Ihrer Datenquelle mit Microsoft Sentinel mithilfe von Azure Functions](connect-azure-functions-template.md).

> [!IMPORTANT]
> Integrationen, die Azure Functions verwenden, können zusätzliche Datenerfassungskosten verursachen, da Sie Azure Functions auf Ihrem Azure-Mandanten hosten. Weitere Informationen finden Sie unter [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

## <a name="agent-based-integration"></a>Agent-basierte Integration

Microsoft Sentinel kann das Syslog-Protokoll verwenden, um über einen Agent eine Verbindung mit einer beliebigen Datenquelle herzustellen, die Protokollstreaming in Echtzeit durchführen kann. Beispielsweise stellen die meisten lokalen Datenquellen eine Verbindung über Agent-basierte Integration her.

In den folgenden Abschnitten werden die verschiedenen Typen von Agent-basierten Microsoft Sentinel-Datenconnectors beschrieben. Führen Sie die Schritte auf jeder Microsoft Sentinel-Datenconnectorseite aus, um Verbindungen mit Agent-basierten Mechanismen zu konfigurieren.

Eine vollständige Liste der Firewalls, Proxys und Endpunkte, die über CEF oder Syslog eine Verbindung mit Microsoft Sentinel herstellen, finden Sie in der [Referenz zu Datenconnectors](data-connectors-reference.md).

### <a name="syslog"></a>syslog

Sie können Ereignisse von Linux-basierten Geräten aus, die Syslog unterstützen, in Microsoft Sentinel streamen, indem Sie den Log Analytics-Agent für Linux verwenden, ehemals als OMS-Agent bezeichnet. Der Log Analytics-Agent wird für jedes Gerät unterstützt, dass Ihnen die Installation des Log Analytics-Agents direkt auf dem Gerät gestattet.

Der integrierte Syslog-Daemon des Geräts sammelt lokale Ereignisse der angegebenen Typen und leitet diese lokal an den Agent weiter, der sie dann in Ihren Log Analytics-Arbeitsbereich streamt. Nach erfolgreicher Konfiguration werden die Daten in der Log Analytics-Syslog-Tabelle angezeigt.

Je nach Gerätetyp wird der Agent entweder direkt auf dem Gerät oder auf einem dedizierten Linux-basierten Server für die Protokollweiterleitung installiert. Der Log Analytics-Agent empfängt Ereignisse vom Syslog-Daemon über UDP. Falls für einen Linux-Computer aber die Erfassung einer großen Menge von Syslog-Ereignissen erwartet wird, sendet er Ereignisse über TCP vom Syslog-Daemon an den Agent und von dort an Log Analytics.

Weitere Informationen finden Sie unter [Verbinden Syslog-basierter Appliances mit Microsoft Sentinel](connect-syslog.md).

### <a name="common-event-format-cef"></a>Common Event Format (CEF)

Protokollformate variieren, aber viele Quellen unterstützen CEF-basierte Formatierung. Der Microsoft Sentinel-Agent, bei dem es sich tatsächlich um den Log Analytics-Agent handelt, konvertiert CEF-formatierte Protokolle in ein Format, das Log Analytics erfassen kann.

Richten Sie für Datenquellen, die Daten in CEF ausgeben, den Syslog-Agent ein, und konfigurieren Sie dann den CEF-Datenfluss. Nach erfolgreicher Konfiguration werden die Daten in der Tabelle **CommonSecurityLog** angezeigt.

Weitere Informationen finden Sie unter [Verbinden CEF-basierter Appliances mit Microsoft Sentinel](connect-common-event-format.md).

### <a name="custom-logs"></a>Benutzerdefinierte Protokolle

Einige Datenquellen verfügen über Protokolle, die als Dateien unter Windows oder Linux erfasst werden können. Sie können diese Protokolle mithilfe des benutzerdefinierten Log Analytics-Protokollsammlungs-Agents erfassen.

Führen Sie die Schritte auf jeder Microsoft Sentinel-Datenconnectorseite aus, um mithilfe des benutzerdefinierten Log Analytics-Protokollsammlungs-Agents eine Verbindung herzustellen. Nach erfolgreicher Konfiguration werden die Daten in benutzerdefinierten Tabellen angezeigt.

Weitere Informationen finden Sie unter [Sammeln von Daten in benutzerdefinierten Protokollformaten für Microsoft Sentinel mit dem Log Analytics-Agent](connect-custom-logs.md).

## <a name="service-to-service-integration"></a>Dienst-zu-Dienst-Integration

Microsoft Sentinel verwendet die Azure Foundation, um sofort einsatzbereiten Service-to-Service-Support für Microsoft-Dienste und Amazon Web Services bereitzustellen.

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure-, Windows-, Microsoft- und Amazon-Diensten](connect-azure-windows-microsoft-services.md) sowie in der [Referenz zu Datenconnectors](data-connectors-reference.md).

## <a name="deploy-as-part-of-a-solution"></a>Bereitstellen im Rahmen einer Lösung

[Microsoft Sentinel-Lösungen](sentinel-solutions.md) stellen Pakete mit Sicherheitsinhalten bereit, darunter beispielsweise Datenconnectors, Arbeitsmappen, Analyseregeln oder Playbooks. Wenn Sie eine Lösung mit einem Datenconnector bereitstellen, erhalten Sie den Datenconnector zusammen mit den zugehörigen Inhalten in derselben Bereitstellung.

Weitere Informationen finden Sie unter [Out-of-the-Box-Inhalte und Lösungen von Microsoft Sentinel zentral erkennen und bereitstellen](sentinel-solutions-deploy.md) und den [Lösungskatalog von Microsoft Sentinel](sentinel-solutions-catalog.md).

## <a name="data-connector-support"></a>Datenconnectorsupport

Sowohl Microsoft als auch andere Organisationen erstellen Microsoft Sentinel-Datenconnectors. Jeder Datenconnector verfügt über einen der folgenden Unterstützungstypen:

| Supporttyp| BESCHREIBUNG|
|-------------|------------|
|**Von Microsoft unterstützt**|Gilt für:<ul><li>Datenconnectors für Datenquellen, bei denen Microsoft der Datenanbieter und Autor ist.</li><li>Ein paar von Microsoft erstellte Datenconnectors für Nicht-Microsoft-Datenquellen.</li></ul>Microsoft unterstützt und verwaltet Datenconnectors in dieser Kategorie in Übereinstimmung mit [Microsoft Azure-Supportplänen](https://azure.microsoft.com/support/options/#overview).<br><br>Partner oder die Community unterstützen Datenconnectors, die von einer anderen Partei als Microsoft erstellt werden.|
|**Von Partnern unterstützt**|Gilt für Datenconnectors, die von anderen Parteien als Microsoft erstellt wurden.<br><br>Das Partnerunternehmen bietet Support oder Wartung für diese Datenconnectors. Das Partnerunternehmen kann ein unabhängiger Softwarehersteller (ISV), ein Anbieter verwalteter Dienste (Managed Service Provider, MSP/MSSP), ein Systemintegrator (SI) oder eine beliebige Organisation sein, deren Kontaktinformationen auf der Microsoft Sentinel-Seite für diesen Datenconnector bereitgestellt werden.<br><br>Wenden Sie sich bei Problemen mit einem vom Partner unterstützten Datenconnector an den angegebenen Supportkontakt für den Datenconnector.|
|**Von der Community unterstützt**|Gilt für Datenconnectors, die von Microsoft oder Partnerentwicklern erstellt wurden und für die auf der angegebenen Datenconnectorseite in Microsoft Sentinel keine Kontakte für den Support und die Verwaltung des Datenconnectors aufgeführt sind.<br><br>Bei Fragen oder Problemen mit diesen Datenconnectors können Sie in der [Microsoft Sentinel GitHub-Community](https://aka.ms/threathunters) [ein Problem erstellen](https://github.com/Azure/Azure-Sentinel/issues/new/choose).|

### <a name="find-the-support-contact-for-a-data-connector"></a>Auffinden des Supportkontakts für einen Datenconnector

So finden Sie die Kontaktinformationen für den Support eines Datenconnectors

1. Wählen Sie im linken Navigationsmenü von Microsoft Sentinel **Datenconnectors** aus.

1. Wählen Sie den Connector aus, für den Sie Supportinformationen suchen möchten.

1. Zeigen Sie das Feld **Unterstützt von** im Seitenbereich für den Datenconnector an.

   ![Screenshot des Felds „Unterstützt von“ für einen Datenconnector in Microsoft Sentinel.](./media/collect-data/connectors.png)

   Das Feld **Unterstützt von** verfügt über einen Supportkontaktlink, über den Sie auf Support und Wartung für den ausgewählten Datenconnector zugreifen können.

## <a name="next-steps"></a>Nächste Schritte

- Für den Einstieg in Microsoft Sentinel benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie über kein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/free/) registrieren.
- Hier erfahren Sie, wie Sie [ein Onboarding Ihrer Daten in Microsoft Sentinel durchführen](quickstart-onboard.md) und [Einblicke in Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
