---
title: Verbinden von Datenquellen mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Datenquellen wie Microsoft 365 Defender (vormals Microsoft Threat Protection), Microsoft 365 und Office 365, Azure AD, ATP und Cloud App Security mit Azure Sentinel verbinden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: c9bce99f26d7d23d2bf655719373f43e78b5a8cb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355936"
---
# <a name="connect-data-sources"></a>Herstellen einer Verbindung mit Datenquellen

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Nachdem Sie Azure Sentinel aktiviert haben, müssen Sie als Erstes eine Verbindung mit Ihren Datenquellen herstellen. Azure Sentinel verfügt über viele Connectors für Microsoft-Produkte, die sofort verfügbar sind und Echtzeitintegration bieten. Beispielsweise umfassen Dienst-zu-Dienst-Connectors Microsoft 365 Defender-Connectors und Microsoft 365-Quellen wie Office 365, Azure Active Directory (Azure AD), Microsoft Defender for Identity und Microsoft Cloud App Security.

Sie können auch integrierte Connectors für Produkte des Sicherheitsökosystems von anderen Anbietern als Microsoft aktivieren. Beispielsweise können Sie [Syslog](#syslog), [Common Event Format (CEF)](#common-event-format-cef) oder [REST-APIs](#rest-api-integration) verwenden, um Ihre Datenquellen mit Azure Sentinel zu verbinden.

Weitere Informationen finden Sie unter [Azure Sentinel-Partnerdatenconnectors](partner-data-connectors.md) sowie im [Azure Sentinel-Lösungskatalog](sentinel-solutions-catalog.md).

## <a name="enable-a-data-connector"></a>Aktualisieren eines Datenconnectors

So aktivieren Sie einen Datenconnector in Azure Sentinel

1. Wählen Sie im linken Navigationsmenü von Azure Sentinel **Datenconnectors** aus. Auf der Seite **Datenconnectors** wird die gesamte Liste der von Azure Sentinel bereitgestellten Connectors sowie deren Status angezeigt. Wählen Sie den gewünschten Connector für die Verbindungsherstellung und anschließend **Connectorseite öffnen** aus. 

   ![Datenconnectors-Katalog](./media/collect-data/collect-data-page.png)
   
   Weitere Informationen finden Sie unter [Dienst-zu-Dienst-Integration](#service-to-service-integration) und [Azure Sentinel-Partnerdatenconnectors](partner-data-connectors.md).
   

1. Vergewissern Sie sich auf der Seite des Connectors, dass alle Voraussetzungen erfüllt sind, und folgen Sie anschließend der Anleitung, um die Daten mit Azure Sentinel zu verbinden. Möglicherweise dauert es etwas, bis die Protokollsynchronisierung mit Azure Sentinel beginnt. Nach erfolgreicher Verbindungsherstellung werden eine Zusammenfassung der Daten im Diagramm **Empfangene Daten** sowie der Konnektivitätsstatus der Datentypen angezeigt.

   ![Konfigurieren von Datenconnectors](./media/collect-data/opened-connector-page.png)
  
1. Wählen Sie die Registerkarte **Nächste Schritte** aus, um Inhalte anzuzeigen, die von Azure Sentinel für den spezifischen Datentyp bereitgestellt werden.

   ![Nächste Schritte für Connectors](./media/collect-data/data-insights.png)

<a name="agent-options"></a>
## <a name="data-connection-methods"></a>Datenverbindungsmethoden
<a name="map-data-types-with-azure-sentinel-connection-options"></a>

Azure Sentinel unterstützt die folgenden Datenverbindungsmethoden:

### <a name="service-to-service-integration"></a>Dienst-zu-Dienst-Integration

Azure Sentinel verwendet die Azure-Grundlage, um integrierte Dienst-zu-Dienst-Unterstützung für die folgenden Microsoft-Dienste und Amazon Web Services bereitzustellen:

- [Amazon Web Services – CloudTrail](connect-aws.md)
- [Azure Active Directory](connect-azure-active-directory.md), einschließlich Überwachungs- und Anmeldeprotokollen
- [Azure Active Directory Identity Protection](connect-azure-ad-identity-protection.md)
- [Azure-Aktivität](connect-azure-activity.md)
- [Azure DDoS Protection](connect-azure-ddos-protection.md)
- [Azure Defender](connect-azure-security-center.md)-Benachrichtigungen aus Azure Security Center
- [Azure Defender für IoT](connect-asc-iot.md) (früher: Azure Security Center für IoT)
- [Azure Firewall](connect-azure-firewall.md)
- [Azure Information Protection](connect-azure-information-protection.md). Weitere Informationen finden Sie unter [Ändern der Berichte und Erstellen von benutzerdefinierten Abfragen](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries).
- [Azure Key Vault](connect-azure-key-vault.md)
- [Azure Kubernetes Service (AKS)](connect-azure-kubernetes-service.md)
- [Azure SQL-Datenbank-Instanzen](connect-azure-sql-logs.md)
- [Azure Storage-Konto](connect-azure-storage-account.md)
- [Microsoft Web Application Firewall (WAF)](connect-azure-waf.md) (früher: Microsoft WAF)
- [Domänennamenserver](connect-dns.md)
- [Dynamics 365](connect-dynamics-365.md)
- [Microsoft 365 Defender](connect-microsoft-365-defender.md), einschließlich Microsoft 365 Defender Incidents und Rohdaten von Microsoft 365 Defender für Endpunkt
- [Microsoft Cloud App Security (MCAS)](connect-cloud-app-security.md)
- [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md) (vormals Microsoft Defender Advanced Threat Protection)
- [Microsoft Defender for Identity](connect-azure-atp.md) (vormals Azure Advanced Threat Protection)
- [Microsoft Defender für Office 365](connect-office-365-advanced-threat-protection.md) (früher: Office 365 Advanced Threat Protection)
- [Office 365](connect-office-365.md), einschließlich Microsoft Teams
- [Sicherheitsereignisse (Windows)](connect-windows-security-events.md). Weitere Informationen finden Sie unter [Einrichtung der Arbeitsmappe für unsichere Protokolle](./get-visibility.md#use-built-in-workbooks).
- [Windows-Firewall](connect-windows-firewall.md)

### <a name="rest-api-integration"></a>REST-API-Integration

Viele Sicherheitstechnologien stellen eine Reihe von APIs zum Abrufen von Protokolldateien zur Verfügung, und einige Datenquellen können diese APIs verwenden, um eine Verbindung mit Azure Sentinel herzustellen.

Datenconnectors, die APIs verwenden, können entweder auf Anbieterseite oder mit Azure Functions integriert werden, wie in den folgenden Abschnitten beschrieben.

Eine vollständige Liste und Informationen zu diesen Connectors finden Sie unter [Azure Sentinel-Partnerdatenconnectors](partner-data-connectors.md).

#### <a name="rest-api-integration-on-the-provider-side"></a>REST-API-Integration auf Anbieterseite

Eine API-Integration, die vom Anbieter erstellt wird, stellt eine Verbindung mit den Anbieterdatenquellen her und pusht Daten mithilfe der [Azure Monitor-Datensammler-API](../azure-monitor/logs/data-collector-api.md) in benutzerdefinierte Protokolltabellen von Azure Sentinel.

Weitere Informationen zum Konfigurieren dieser Datenconnectors zum Senden von Protokollen an Azure Sentinel finden Sie in der relevanten Anbieterdokumentation. 

#### <a name="rest-api-integration-using-azure-functions"></a>REST-API-Integration mit Azure Functions

Integrationen, die [Azure Functions](../azure-functions/index.yml) verwenden, um eine Verbindung mit einer Anbieter-API herzustellen, formatieren zuerst die Daten und senden sie dann mithilfe der [Azure Monitor-Datensammler-API](../azure-monitor/logs/data-collector-api.md) an benutzerdefinierte Protokolltabellen von Azure Sentinel.

Weitere Informationen zum Konfigurieren dieser Datenconnectors zum Herstellen einer Verbindung mit der Anbieter-API und zum Sammeln von Protokollen in Azure Sentinel finden Sie in den für jeden Datenconnector in Azure Sentinel angezeigten Schritten.

Integrationen, die Azure Functions verwenden, verfügen auch über die Schaltfläche **In Azure bereitstellen** auf ihrer Azure Sentinel-Datenconnectorseite. Gehen Sie für diese Integrationen wie folgt vor, um die Konfiguration zu vereinfachen:

1. Wählen Sie **In Azure bereitstellen** aus.
1. Geben Sie die Parameterwerte ein, um eine Verbindung mit der Anbieter-API herzustellen.
1. Geben Sie Ihre Azure Sentinel-Arbeitsbereichs-ID und den Schlüssel ein, um eine Verbindung mit Log Analytics herzustellen und die Daten an Azure Sentinel zu senden.

> [!IMPORTANT]
> Integrationen, die Azure Functions verwenden, können zusätzliche Datenerfassungskosten verursachen, da Sie Azure Functions auf Ihrem Azure-Mandanten hosten. Weitere Informationen finden Sie unter [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

### <a name="agent-based-integration"></a>Agent-basierte Integration

Azure Sentinel kann das Syslog-Protokoll verwenden, um über einen Agent eine Verbindung mit einer beliebigen Datenquelle herzustellen, die Protokollstreaming in Echtzeit durchführen kann. Beispielsweise stellen die meisten lokalen Datenquellen eine Verbindung über Agent-basierte Integration her.
In den folgenden Abschnitten werden die verschiedenen Typen von Agent-basierten Azure Sentinel-Datenconnectors beschrieben. Führen Sie die Schritte auf jeder Azure Sentinel-Datenconnectorseite aus, um Verbindungen mit Agent-basierten Mechanismen zu konfigurieren.

Eine vollständige Liste der Firewalls, Proxys und Endpunkte, die über CEF oder Syslog eine Verbindung mit Azure Sentinel herstellen, finden Sie unter [Azure Sentinel-Partnerdatenconnectors](partner-data-connectors.md).

#### <a name="syslog"></a>Syslog

Sie können Ereignisse von Linux-basierten Geräten aus, die Syslog unterstützen, in Azure Sentinel streamen, indem Sie den Log Analytics-Agent für Linux verwenden, ehemals als OMS-Agent bezeichnet. Der Log Analytics-Agent wird für jedes Gerät unterstützt, dass Ihnen die Installation des Log Analytics-Agents direkt auf dem Gerät gestattet.

Der integrierte Syslog-Daemon des Geräts sammelt lokale Ereignisse der angegebenen Typen und leitet diese lokal an den Agent weiter, der sie dann in Ihren Log Analytics-Arbeitsbereich streamt. Nach erfolgreicher Konfiguration werden die Daten in der Log Analytics-Syslog-Tabelle angezeigt.

Je nach Gerätetyp wird der Agent entweder direkt auf dem Gerät oder auf einem dedizierten Linux-basierten Server für die Protokollweiterleitung installiert. Der Log Analytics-Agent empfängt Ereignisse vom Syslog-Daemon über UDP. Falls für einen Linux-Computer aber die Erfassung einer großen Menge von Syslog-Ereignissen erwartet wird, sendet er Ereignisse über TCP vom Syslog-Daemon an den Agent und von dort an Log Analytics.

Weitere Informationen finden Sie unter [Verbinden Syslog-basierter Appliances mit Azure Sentinel](connect-syslog.md). 

#### <a name="common-event-format-cef"></a>Common Event Format (CEF)

Protokollformate variieren, aber viele Quellen unterstützen CEF-basierte Formatierung. Der Azure Sentinel-Agent, bei dem es sich tatsächlich um den Log Analytics-Agent handelt, konvertiert CEF-formatierte Protokolle in ein Format, das Log Analytics erfassen kann.

Richten Sie für Datenquellen, die Daten in CEF ausgeben, den Syslog-Agent ein, und konfigurieren Sie dann den CEF-Datenfluss. Nach erfolgreicher Konfiguration werden die Daten in der Tabelle **CommonSecurityLog** angezeigt.

Weitere Informationen finden Sie unter [Verbinden CEF-basierter Appliances mit Azure Sentinel](connect-common-event-format.md).

#### <a name="custom-logs"></a>Benutzerdefinierte Protokolle

Einige Datenquellen verfügen über Protokolle, die als Dateien unter Windows oder Linux erfasst werden können. Sie können diese Protokolle mithilfe des benutzerdefinierten Log Analytics-Protokollsammlungs-Agents erfassen.

Führen Sie die Schritte auf jeder Azure Sentinel-Datenconnectorseite aus, um mithilfe des benutzerdefinierten Log Analytics-Protokollsammlungs-Agents eine Verbindung herzustellen. Nach erfolgreicher Konfiguration werden die Daten in benutzerdefinierten Tabellen angezeigt.

Weitere Informationen finden Sie unter [Sammeln von benutzerdefinierten Protokollen mit dem Log Analytics-Agent](../azure-monitor/agents/data-sources-custom-logs.md).

## <a name="data-connector-support"></a>Datenconnectorsupport

Sowohl Microsoft als auch andere Organisationen erstellen Azure Sentinel-Datenconnectors. Jeder Datenconnector verfügt über einen der folgenden Unterstützungstypen:

| Supporttyp| BESCHREIBUNG|
|-------------|------------|
|**Von Microsoft unterstützt**|Gilt für:<ul><li>Datenconnectors für Datenquellen, bei denen Microsoft der Datenanbieter und Autor ist.</li><li>Ein paar von Microsoft erstellte Datenconnectors für Nicht-Microsoft-Datenquellen.</li></ul>Microsoft unterstützt und verwaltet Datenconnectors in dieser Kategorie in Übereinstimmung mit [Microsoft Azure-Supportplänen](https://azure.microsoft.com/support/options/#overview).<br><br>Partner oder die Community unterstützen Datenconnectors, die von einer anderen Partei als Microsoft erstellt werden.|
|**Von Partnern unterstützt**|Gilt für Datenconnectors, die von anderen Parteien als Microsoft erstellt wurden.<br><br>Das Partnerunternehmen bietet Support oder Wartung für diese Datenconnectors. Das Partnerunternehmen kann ein unabhängiger Softwarehersteller (ISV), ein Anbieter verwalteter Dienste (Managed Service Provider, MSP/MSSP), ein Systemintegrator (SI) oder eine beliebige Organisation sein, deren Kontaktinformationen auf der Azure Sentinel-Seite für diesen Datenconnector bereitgestellt werden.<br><br>Wenden Sie sich bei Problemen mit einem vom Partner unterstützten Datenconnector an den angegebenen Supportkontakt für den Datenconnector.|
|**Von der Community unterstützt**|Gilt für Datenconnectors, die von Microsoft oder Partnerentwicklern erstellt wurden und für die auf der angegebenen Datenconnectorseite in Azure Sentinel keine Kontakte für den Support und die Verwaltung des Datenconnectors aufgeführt sind.<br><br>Bei Fragen oder Problemen mit diesen Datenconnectors können Sie in der [Azure Sentinel GitHub-Community](https://aka.ms/threathunters) [ein Problem erstellen](https://github.com/Azure/Azure-Sentinel/issues/new/choose).|

### <a name="find-the-support-contact-for-a-data-connector"></a>Auffinden des Supportkontakts für einen Datenconnector

So finden Sie die Kontaktinformationen für den Support eines Datenconnectors

1. Wählen Sie im linken Navigationsmenü von Azure Sentinel **Datenconnectors** aus.
   
1. Wählen Sie den Connector aus, für den Sie Supportinformationen suchen möchten.
   
1. Zeigen Sie das Feld **Unterstützt von** im Seitenbereich für den Datenconnector an.
   
   ![Screenshot des Felds „Unterstützt von“ für einen Datenconnector in Azure Sentinel.](./media/collect-data/connectors.png)
   
   Das Feld **Unterstützt von** verfügt über einen Supportkontaktlink, über den Sie auf Support und Wartung für den ausgewählten Datenconnector zugreifen können.



## <a name="next-steps"></a>Nächste Schritte

- Für den Einstieg in Azure Sentinel benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie über kein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/free/) registrieren.
- Hier erfahren Sie, wie Sie [ein Onboarding Ihrer Daten in Azure Sentinel durchführen](quickstart-onboard.md) und [Einblicke in Daten und potenzielle Bedrohungen erhalten](get-visibility.md).