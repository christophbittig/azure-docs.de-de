---
title: Verbinden von Thycotic Secret Server mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Thycotic Secret Server-Datenconnector die Thycotic Secret Server-Protokolle in Azure Sentinel pullen. Zeigen Sie Thycotic Secret Server-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 79ab5ccccc3a81f6a897c41c8218432553d4ba7e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693761"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Verbinden Ihres Thycotic Secret Server mit Azure Sentinel

> [!IMPORTANT]
> Der Thycotic Secret Server-Connector befindet sich derzeit in der **VORSCHAUVERSION**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

In diesem Artikel wird erläutert, wie Sie Ihre Thycotic Secret Server-Appliance mit Azure Sentinel verbinden. Mit dem Thycotic Secret Server-Datenconnector lassen sich Ihre Thycotic Secret Server-Protokolle problemlos mit Azure Sentinel verbinden, sodass Sie die Daten dann in Arbeitsmappen anzeigen, als Vorlage für benutzerdefinierte Warnungen und zur Verbesserung der Untersuchung von Problemen verwenden können. Bei der Integration von Thycotic Secret Server und Azure Sentinel wird der CEF-Datenconnector verwendet, um Secret Server-Syslog-Nachrichten ordnungsgemäß zu analysieren und anzuzeigen.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen.

- Ihr Thycotic Secret Server muss so konfiguriert sein, dass Protokolle über Syslog exportiert werden.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Senden Ihrer Thycotic Secret Server-Protokolle an Azure Sentinel

Um die Protokolle Ihres Thycotic Secret Server in Azure Sentinel zu erfassen, konfigurieren Sie ihn so, dass er Syslog-Nachrichten im CEF-Format an Ihren Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird) sendet. Auf diesem Server ist der Log Analytics-Agent installiert, der die Protokolle an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.

1. Wählen Sie im Azure Sentinel-Navigationsmenü **Datenconnectors** aus.

1. Wählen Sie im **Datenconnectors**-Katalog den Eintrag **Thycotic Secret Server (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die Anleitungen auf der Registerkarte **Anweisungen** unter **Konfiguration**:

    1. Unter **1. Konfiguration des Linux-Syslog-Agents**: Führen Sie diesen Schritt aus, wenn Sie noch keinen Protokollweiterleitungsserver ausführen oder einen anderen benötigen. Detaillierte Anleitungen und Erläuterungen finden Sie unter [SCHRITT 1: Bereitstellen des Protokollweiterleitungsservers](connect-cef-agent.md) in der Azure Sentinel-Dokumentation.

    1. Wählen Sie unter **2. Weiterleiten von CEF-Protokollen (Common Event Format) an den Syslog-Agent**: Befolgen Sie die Anweisungen von Thycotic zum [Konfigurieren des Secret Server](https://docs.thycotic.com/ssi/1.0.0/splunk/splunk-on-prem/config/syslog-events.md). Diese Konfiguration sollte die folgenden Elemente enthalten:
        - Protokollziel: Hostname und/oder IP-Adresse Ihres Protokollweiterleitungsservers.
        - Protokoll und Port: **TCP 514** (falls etwas anderes empfohlen wird, stellen Sie sicher, dass Sie die entsprechende Änderung parallel im syslog-Daemon und auf Ihrem Protokollweiterleitungsserver vornehmen).
        - Protokollformat: CEF.
        - Protokolltypen: Alle verfügbaren.

    1. Klicken Sie unter **3. Überprüfen der Verbindung**: Überprüfen Sie die Datenerfassung, indem Sie den Befehl von der Connectorseite kopieren und auf Ihrem Protokollweiterleitungsserver ausführen. Weitere Informationen und Erklärungen finden Sie in der in der Azure Sentinel-Dokumentation [Überprüfen der CEF-Verbindung](troubleshooting-cef-syslog.md#validate-cef-connectivity).

        Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Um Thycotic Secret Server-Daten in Log Analytics abzufragen, kopieren Sie Folgendes in das Abfragefenster, wobei Sie bei der Auswahl andere Filter anwenden:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Einige hilfreiche Arbeitsmappenbeispiele und Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Thycotic Secret Server mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
