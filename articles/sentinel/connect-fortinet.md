---
title: Verknüpfen von Fortinet-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Verbinden Sie Ihre Fortinet-Appliance mit Azure Sentinel, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 9fb3de09195c8dd88110a7723cbe7b4d89e89309
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695115"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Verbinden von Fortinet mit Azure Sentinel



In diesem Artikel wird erläutert, wie Sie die Fortinet-Appliance mit Azure Sentinel verbinden. Der Fortinet-Datenconnektor ermöglicht es Ihnen, Ihre Fortinet-Protokolle auf einfache Weise mit Azure Sentinel zu verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern. Die Verwendung von Fortinet für Azure Sentinel bietet Ihnen mehr Einblicke in die Internetnutzung Ihrer Organisation und erweitert die Funktionen für Sicherheitsvorgänge. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Weiterleiten von Fortinet-Protokollen an den Syslog-Agent

Konfigurieren Sie Fortinet, um Syslog-Nachrichten im CEF-Format über den Syslog-Agent an Ihren Azure-Arbeitsbereich weiterzuleiten.

1. Öffnen Sie die Befehlszeilenschnittstelle für Ihre Fortinet-Appliance, und führen Sie die folgenden Befehle aus:

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - Ersetzen Sie die **IP-Adresse** des Servers durch die IP-Adresse des Agents.
    - Legen Sie den **Syslog-Port** auf **514** fest (oder auf den Port, den Sie für den Agent festgelegt haben).
    - In frühen Versionen von FortiOS muss zum Aktivieren des CEF-Formats ggf. der Befehl **set csv disable** ausgeführt werden.
 
   > [!NOTE] 
   > Weitere Informationen finden Sie in der [Fortinet-Dokumentbibliothek](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Wählen Sie Ihre Version aus, und verwenden Sie das **Handbuch** und die **Referenz für Protokollmeldungen**.

1. Suchen Sie nach `CommonSecurityLog`, um das relevante Schema in Azure Monitor Log Analytics für die Fortinet-Ereignisse zu verwenden.

1. Fahren Sie mit dem [Überprüfen der Konnektivität](troubleshooting-cef-syslog.md#validate-cef-connectivity) fort.


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Fortinet-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.