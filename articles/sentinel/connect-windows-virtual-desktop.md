---
title: Verknüpfen von Windows Virtual Desktop mit Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Windows Virtual Desktop-Daten mit Microsoft Sentinel verbinden.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3a1aa86db8b7a969b0c526ff4eaf9ff0d3351865
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524491"
---
# <a name="connect-windows-virtual-desktop-data-to-microsoft-sentinel"></a>Verbinden von Windows Virtual Desktop-Daten mit Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel wird beschrieben, wie Sie Ihre Windows Virtual Desktop-Umgebungen (WVD) mithilfe von Microsoft Sentinel überwachen können.

Durch die Überwachung Ihrer Windows Virtual Desktop-Umgebungen können Sie beispielsweise mehr Remotearbeit mit virtualisierten Desktops ermöglichen, ohne den Sicherheitsstatus Ihrer Organisation zu gefährden.

## <a name="windows-virtual-desktop-data-in-microsoft-sentinel"></a>Windows Virtual Desktop-Daten in Microsoft Sentinel

Windows Virtual Desktop-Daten in Microsoft Sentinel umfassen die folgenden Typen:


|Daten  |BESCHREIBUNG  |
|---------|---------|
|**Windows-Ereignisprotokolle**     |  Windows-Ereignisprotokolle aus der WVD-Umgebung werden auf die gleiche Weise in einen Microsoft Sentinel-fähigen Log Analytics-Arbeitsbereich gestreamt wie Windows-Ereignisprotokolle von anderen Windows-Computern außerhalb der WVD-Umgebung. <br><br>Installieren Sie den Log Analytics-Agent auf Ihrem Windows-Computern, und konfigurieren Sie die Windows-Ereignisprotokolle so, dass sie an den Log Analytics-Arbeitsbereich gesendet werden.<br><br>Weitere Informationen finden Sie unter<br>- [Installieren des Log Analytics-Agents auf Windows-Computern](../azure-monitor/agents/agent-windows.md)<br>- [Datenquellen für das Sammeln von Windows-Ereignisprotokolldaten mit dem Log Analytics-Agent](../azure-monitor/agents/data-sources-windows-events.md)<br>- [Herstellen einer Verbindung mit Windows-Sicherheitsereignissen](connect-windows-security-events.md)       |
|**Microsoft Defender für Endpunkt-Warnungen**     |  Um Defender für Endpunkt für Windows Virtual Desktop zu konfigurieren, verwenden Sie das gleiche Verfahren wie für jeden anderen Windows-Endpunkt. <br><br>Weitere Informationen finden Sie unter <br>- [Einrichten der Bereitstellung von Microsoft Defender für Endpunkt](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Verknüpfen von Daten aus Microsoft 365 Defender mit Microsoft Sentinel](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop-Diagnose**     | Windows Virtual Desktop-Diagnose ist ein Feature des PaaS-Diensts von Windows Virtual Desktop, das Informationen protokolliert, sobald jemand, dem die Windows Virtual Desktop-Rolle zugewiesen ist, den Dienst verwendet. <br><br>Neben Mandanten- und Benutzerinformationen enthält jedes Protokoll Informationen darüber, welche Windows Virtual Desktop-Rolle an der Aktivität beteiligt war und welche Fehlermeldungen ggf. während der Sitzung angezeigt wurden. <br><br>Die Diagnosefunktion erstellt Aktivitätsprotokolle für Benutzer- und Administratoraktionen. <br><br>Weitere Informationen finden Sie unter [Verwenden von Log Analytics für das Diagnosefeature in Windows Virtual Desktop](../virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Verbinden von Windows Virtual Desktop-Daten

Um mit dem Erfassen von Windows Virtual Desktop-Daten in Microsoft Sentinel zu beginnen, verwenden Sie die Anweisungen aus der Windows Virtual Desktop-Dokumentation.

Weitere Informationen finden Sie unter [Pushen von Windows Virtual Desktop-Daten in Ihren Log Analytics-Arbeitsbereich](../virtual-desktop/diagnostics-log-analytics.md).

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine erfolgreiche Verbindung hergestellt wurde, führen Sie Abfragen Ihrer Log Analytics-Daten in Microsoft Sentinel aus.

Beispielabfragen finden Sie in der [Windows Virtual Desktop-Dokumentation](../virtual-desktop/diagnostics-log-analytics.md).


Microsoft Sentinel bietet auch integrierte Abfragen im Bereich **Allgemein** > **Protokolle** > **WINDOWS VIRTUAL DESKTOP**:

[![Integrierte Windows Virtual Desktop-Abfragen in Microsoft Sentinel.](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png) ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte


Weitere Informationen finden Sie im [Glossar zu Azure Monitor für Windows Virtual Desktop](../virtual-desktop/azure-monitor-glossary.md).
