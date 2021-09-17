---
title: Verbinden von Broadcom Symantec Data Loss Prevention-Daten (DLP) mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Broadcom Symantec DLP-Datenconnector entsprechende Symantec DLP-Protokolle in Azure Sentinel pullen. Zeigen Sie Symantec DLP-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: be7decff244bffa329f46109a5ab2fca7772bee1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695208"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Verbinden von Broadcom Symantec Data Loss Prevention (DLP) mit Azure Sentinel

> [!IMPORTANT]
> Der Broadcom Symantec DLP-Connector befindet sich derzeit in der **VORSCHAUVERSION**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

In diesem Artikel wird erläutert, wie Sie Ihre Broadcom Symantec DLP-Appliance mit Azure Sentinel verbinden. Der Broadcom Symantec DLP-Datenconnector ermöglicht Ihnen, Ihre Symantec DLP-Protokolle auf einfache Weise mit Azure Sentinel zu verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern. Diese Funktion gibt Ihnen einen besseren Einblick in die Informationen und deren Verlauf in Ihrem Unternehmen und verbessert Ihre Möglichkeiten für Sicherheitsvorgänge. Die Integration von Broadcom Symantec DLP und Azure Sentinel basiert auf CEF-formatierten Syslog-Nachrichten, einem Linux-basierten Server für die Protokollweiterleitung und dem Log Analytics-Agent. Außerdem wird ein benutzerdefinierter Protokollparser verwendet, der auf einer Kusto-Funktion basiert.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Senden von Broadcom Symantec DLP-Protokollen an Azure Sentinel

Um die Protokolle Ihrer Broadcom Symantec DLP-Appliance in Azure Sentinel zu erfassen, müssen Sie die Appliance so konfigurieren, dass sie Syslog-Nachrichten im CEF-Format an einen Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird) sendet. Auf diesem Server ist der Log Analytics-Agent installiert, der die Protokolle an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.

1. Wählen Sie im Azure Sentinel-Navigationsmenü **Datenconnectors** aus.

1. Wählen Sie im **Datenconnectors**-Katalog den Connector **Broadcom Symantec DLP (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die Anleitungen auf der Registerkarte **Anweisungen** unter **Konfiguration**:

    1. Unter **1. Konfiguration des Linux-Syslog-Agents**: Führen Sie diesen Schritt aus, wenn Sie noch keinen Protokollweiterleitungsserver ausführen oder einen anderen benötigen. Detaillierte Anleitungen und Erläuterungen finden Sie unter [SCHRITT 1: Bereitstellen des Protokollweiterleitungsservers](connect-cef-agent.md) in der Azure Sentinel-Dokumentation.

    1. Wählen Sie unter **2. Weiterleiten von Symantec DLP-Protokollen an einen Syslog-Agent** – Befolgen der Anweisungen von Broadcom zum [Konfigurieren von Symantec DLP](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Diese Konfiguration sollte die folgenden Elemente enthalten:
        - Protokollziel: Hostname und/oder IP-Adresse Ihres Protokollweiterleitungsservers
        - Protokoll und Port: TCP 514 (Falls etwas anderes empfohlen wird, sollten Sie sicherstellen, dass Sie die entsprechende Änderung parallel auch im syslog-Daemon und auf Ihrem Protokollweiterleitungsserver vornehmen.)
        - Protokollformat: CEF
        - Protokolltypen: „Alle verfügbar“ oder „Alle geeignet“

    1. Klicken Sie unter **3. Überprüfen der Verbindung**: Überprüfen Sie die Datenerfassung, indem Sie den Befehl von der Connectorseite kopieren und auf Ihrem Protokollweiterleitungsserver ausführen. Weitere Informationen und Erklärungen finden Sie in der in der Azure Sentinel-Dokumentation [Überprüfen der CEF-Verbindung](troubleshooting-cef-syslog.md#validate-cef-connectivity).

        Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Dieser Datenconnector ist von einem Parser abhängig, der auf einer Kusto-Funktion basiert und erwartungsgemäß funktionieren muss. [Befolgen Sie diese Schritte](https://aka.ms/sentinel-symantecdlp-parser), um den **SymantecDLP** Kusto-Funktionsalias zu erstellen.

Um dann Broadcom Symantec DLP-Daten in Log Analytics abzufragen, geben Sie `SymantecDLP` oben im Abfragefenster ein.

Einige hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Symantec DLP mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.