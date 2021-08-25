---
title: Verbinden von CyberArk EPV-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Datenconnector für CyberArk EPV (Enterprise Password Vault) die zugehörigen Protokolle in Azure Sentinel pullen. Zeigen Sie CyberArk EPV-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
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
ms.openlocfilehash: 0b8db5dfa399aaef087adb0ec5c2d67b91fceab9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694043"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Verbinden von CyberArk EPV (Enterprise Password Vault) mit Azure Sentinel

> [!IMPORTANT]
> Der Datenconnector für CyberArk EPV (Enterprise Password Vault) in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Mit dem Syslog-Connector für CyberArk können Sie alle Protokolle Ihrer CyberArk-Sicherheitslösungen einfach mit Azure Sentinel verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und Untersuchungen zu verbessern. Bei der Integration von CyberArk und Azure Sentinel wird der CEF-Datenconnector verwendet, um CyberArk-Syslog-Nachrichten ordnungsgemäß zu analysieren und anzuzeigen.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="configure-and-connect-cyberark-epv"></a>Konfigurieren und Verbinden von CyberArk EPV

CyberArk EPV-Protokolle werden vom Vault an einen Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird) gesendet. Auf diesem ist der Log Analytics-Agent installiert, der die Protokolle in Azure Sentinel exportiert. Wenn Sie nicht über einen solchen Protokollweiterleitungsserver verfügen, können Sie über [diese Anweisungen](connect-cef-agent.md) einen Server einrichten.

1. Klicken Sie im Azure Sentinel-Portal auf **Datenconnectors**, und wählen Sie **CyberArk Enterprise Password Vault (EPV) Events (Preview)** (CyberArk EPV-Ereignisse (Enterprise Password Vault, Vorschau)) und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die [Anweisungen in CyberArk EPV](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm), um das Senden von Syslog-Daten an den Protokollweiterleitungsserver zu konfigurieren.

1. Überprüfen Sie die Verbindung und die Datenerfassung mithilfe [dieser Anweisungen](troubleshooting-cef-syslog.md#validate-cef-connectivity). Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Wenn Sie die CyberArk EPV-Protokolle in Log Analytics abfragen möchten, geben Sie oben im Abfragefenster `CommonSecurityLog` ein.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie CyberArk EPV-Protokolle (Enterprise Password Vault) mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.