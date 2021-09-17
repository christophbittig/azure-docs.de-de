---
title: Verknüpfen von Cisco-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Cisco ASA-Appliance mit Azure Sentinel verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: db10c9814c04f6e29a017b9a473cd117a0cbf2ae
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695199"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Verbinden von Cisco ASA mit Azure Sentinel



In diesem Artikel wird erläutert, wie Sie die Cisco ASA-Appliance mit Azure Sentinel verbinden. Der Cisco ASA-Datenconnektor ermöglicht es Ihnen, Ihre Cisco ASA-Protokolle auf einfache Weise mit Azure Sentinel zu verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern. Die Verwendung von Cisco ASA für Azure Sentinel bietet Ihnen mehr Einblicke in die Internetnutzung Ihrer Organisation und erweitert die Funktionen für Sicherheitsvorgänge. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Weiterleiten von Cisco ASA-Protokollen an den Syslog-Agent

Cisco ASA unterstützt kein CEF, weshalb die Protokolle als Syslog gesendet werden, sodass der Azure Sentinel-Agent weiß, wie sie zu analysieren sind, als ob sie CEF-Protokolle wären. Konfigurieren Sie Cisco ASA für das Weiterleiten von Syslog-Nachrichten an Ihren Azure-Arbeitsbereich über den Syslog-Agent:

1. Navigieren Sie zu [Sending Syslog Messages to an External Syslog Server](https://aka.ms/asi-syslog-cisco-forwarding) (Senden von Syslog-Nachrichten an einen externen Syslog-Server), und befolgen Sie die Anleitung zum Einrichten der Verbindung. Verwenden Sie bei entsprechender Aufforderung diese Parameter:
    - Legen Sie **port** auf 514 oder den Port fest, den Sie im Agent angegeben haben.
    - Legen Sie **syslog_ip** auf die IP-Adresse des Agents fest.

1. Um das relevante Schema in Log Analytics für die Cisco-Ereignisse zu verwenden, suchen Sie nach `CommonSecurityLog`.

1. Fahren Sie mit [Überprüfen der Konnektivität](troubleshooting-cef-syslog.md#validate-cef-connectivity) fort.




## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Cisco ASA-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.