---
title: Verbinden von Aruba ClearPass-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Aruba ClearPass-Connector entsprechende Aruba ClearPass-Protokolle in Azure Sentinel pullen. Zeigen Sie Aruba ClearPass-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 6f81fc5d131ae4d9fa9b76986cc0a6aaed646f32
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689981"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Verbinden von Aruba ClearPass mit Azure Sentinel

> [!IMPORTANT]
> Der Aruba ClearPass-Connector befindet sich derzeit in der **VORSCHAUVERSION**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

In diesem Artikel wird erläutert, wie Sie die Aruba ClearPass-Appliance mit Azure Sentinel verbinden. Mit dem Aruba ClearPass-Datenconnector lassen sich Ihre Aruba ClearPass-Protokolle problemlos mit Azure Sentinel verbinden, sodass Sie die Daten dann in Arbeitsmappen anzeigen, zum Erstellen benutzerdefinierter Warnungen abfragen und zur Verbesserung der Untersuchung von Problemen verwenden können. Die Integration von Aruba ClearPass und Azure Sentinel basiert auf CEF-formatierten Syslog-Nachrichten, einem Linux-basierten Server für die Protokollweiterleitung und dem Log Analytics-Agent. Außerdem wird ein benutzerdefinierter Protokollparser verwendet, der auf einer Kusto-Funktion basiert.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Senden von Aruba ClearPass-Protokollen an Azure Sentinel

Um die Protokolle Ihrer Aruba ClearPass-Appliance in Azure Sentinel zu erfassen, müssen Sie die Appliance so konfigurieren, dass sie Syslog-Nachrichten im CEF-Format an einen Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird) sendet. Auf diesem Server ist der Log Analytics-Agent installiert, der die Protokolle an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.

1. Wählen Sie im Azure Sentinel-Navigationsmenü **Datenconnectors** aus.

1. Wählen Sie im Katalog **Datenconnectors** den Eintrag **Aruba ClearPass (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die Anleitungen auf der Registerkarte **Anweisungen** unter **Konfiguration**:

    1. Unter **1. Konfiguration des Linux-Syslog-Agents**: Führen Sie diesen Schritt aus, wenn Sie noch keinen Protokollweiterleitungsserver ausführen oder einen anderen benötigen. Detaillierte Anleitungen und Erläuterungen finden Sie unter [SCHRITT 1: Bereitstellen des Protokollweiterleitungsservers](connect-cef-agent.md) in der Azure Sentinel-Dokumentation.

    1. Wählen Sie unter **2. Weiterleiten von Aruba ClearPass-Protokollen an einen Syslog-Agent** – Befolgen der Anweisungen von Aruba zum [Konfigurieren von ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Diese Konfiguration sollte die folgenden Elemente enthalten:
        - Protokollziel: Hostname und/oder IP-Adresse Ihres Protokollweiterleitungsservers
        - Protokoll und Port: TCP 514 (Falls etwas anderes empfohlen wird, sollten Sie sicherstellen, dass Sie die entsprechende Änderung parallel auch im syslog-Daemon und auf Ihrem Protokollweiterleitungsserver vornehmen.)
        - Protokollformat: CEF
        - Protokolltypen: „Alle verfügbar“ oder „Alle geeignet“

    1. Klicken Sie unter **3. Überprüfen der Verbindung**: Überprüfen Sie die Datenerfassung, indem Sie den Befehl von der Connectorseite kopieren und auf Ihrem Protokollweiterleitungsserver ausführen. Weitere Informationen und Erklärungen finden Sie in der in der Azure Sentinel-Dokumentation [Überprüfen der CEF-Verbindung](troubleshooting-cef-syslog.md#validate-cef-connectivity).

        Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Dieser Datenconnector ist von einem Parser abhängig, der auf einer Kusto-Funktion basiert und erwartungsgemäß funktionieren muss. [Befolgen Sie diese Schritte](https://aka.ms/sentinel-arubaclearpass-parser), um den **ArubaClearPass** Kusto-Funktionsalias zu erstellen.

Um dann Aruba ClearPass-Daten in Log Analytics abzufragen, geben Sie `ArubaClearPass` oben im Abfragefenster ein.

Einige hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Aruba ClearPass mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.