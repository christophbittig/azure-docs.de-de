---
title: Verknüpfen von Check Point-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Konfigurieren Sie Ihre Check Point-Appliance so, dass sie Syslog-Nachrichten im CEF-Format über den Syslog-Agent an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 72aa2efe611a500a8a7c4a56725bb5bd02b247da
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694081"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Verbinden von Check Point mit Azure Sentinel



In diesem Artikel wird erläutert, wie Sie die Check Point-Appliance mit Azure Sentinel verbinden. Der Check Point-Datenconnektor ermöglicht es Ihnen, Ihre Check Point-Protokolle auf einfache Weise mit Azure Sentinel zu verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern. Die Verwendung von Check Point für Azure Sentinel bietet Ihnen mehr Einblicke in die Internetnutzung Ihrer Organisation und erweitert die Funktionen für Sicherheitsvorgänge. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Weiterleiten der Check Point-Protokolle an den Syslog-Agent

Konfigurieren Sie Ihre Check Point-Appliance, um Syslog-Nachrichten im CEF-Format über den Syslog-Agent an Ihren Azure-Arbeitsbereich weiterzuleiten.

1. Öffnen Sie die Seite [Check Point Log Export](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Scrollen Sie zum Abschnitt **Basic Deployment** (Grundlegende Bereitstellung), und führen Sie die Anweisungen zum Einrichten der Verbindung gemäß der folgenden Richtlinien durch:
   - Legen Sie den **Syslog-Port** auf **514** oder den Port fest, den Sie für den Agent festgelegt haben.
     - Ersetzen Sie den **Namen** und die **IP-Adresse des Zielservers** in der CLI durch den Namen und die IP-Adresse des Syslog-Agents.
     - Legen Sie das Format auf **CEF** fest.
1. Wenn Sie Version R77.30 oder R80.10 verwenden, scrollen Sie nach oben zum Abschnitt **Installation**, und führen Sie die Anweisungen aus, um das Protokollexportprogramm für Ihre Version zu installieren.
1. Fahren Sie mit dem [Überprüfen der CEF-Konnektivität](troubleshooting-cef-syslog.md#validate-cef-connectivity) fort.
 

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Check Point-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- [Überprüfen der Konnektivität](troubleshooting-cef-syslog.md#validate-cef-connectivity).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.