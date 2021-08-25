---
title: Verbinden von Citrix WAF-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Citrix WAF-Datenconnector die zugehörigen Protokolle in Azure Sentinel pullen. Zeigen Sie Citrix WAF-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 309d2cfb47c61d516be19d110cb7ade1f3c63e18
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695172"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Verbinden Ihrer Citrix WAF-Instanz mit Azure Sentinel

> [!IMPORTANT]
> Der Datenconnector für Citrix Web Application Firewall (WAF) in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

In diesem Artikel wird erläutert, wie Sie Ihre Citrix Web Application Firewall-Appliance (WAF) mit Azure Sentinel verbinden. Der Citrix WAF-Datenconnector ermöglicht Ihnen, Ihre Citrix WAF-Protokolle auf einfache Weise mit Azure Sentinel zu verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern. Indem Sie Ihre Citrix WAF-CEF-Protokolle mit Azure Sentinel verbinden, können Sie Such- und Korrelationsfunktionen, Warnungen sowie die Threat Intelligence-Anreicherung für jedes Protokoll nutzen.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Weiterleiten von Citrix WAF-Protokollen an den Syslog-Agent  

Citrix WAF sendet Syslog-Nachrichten im CEF-Format an einen Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird). Auf diesem ist der Log Analytics-Agent installiert, der die Protokolle an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.

1. Wenn Sie nicht über einen solchen Protokollweiterleitungsserver verfügen, können Sie über [diese Anweisungen](connect-cef-agent.md) einen Server einrichten.

1. Befolgen Sie die Anweisungen von Citrix zum Konfigurieren der [WAF](https://support.citrix.com/article/CTX234174), der [CEF-Protokollierung](https://support.citrix.com/article/CTX136146) und der [Übermittlung der Protokolle an den Protokollweiterleitungsserver](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Die Protokolle müssen über TCP-Port 514 an die IP-Adresse des Protokollweiterleitungscomputers gesendet werden.

1. Überprüfen Sie die Verbindung und die Datenerfassung mithilfe [dieser Anweisungen](troubleshooting-cef-syslog.md#validate-cef-connectivity). Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Wenn Sie die Citrix WAF-Protokolle in Log Analytics abfragen möchten, geben Sie oben im Abfragefenster `CommonSecurityLog` ein.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie die Citrix WAF mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.