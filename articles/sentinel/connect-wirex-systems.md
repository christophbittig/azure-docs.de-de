---
title: Verbinden der WireX Network Forensics Platform (NFP) mit Azure Sentinel | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie den WireX Systems NFP-Datenconnector verwenden, um WireX NFP-Protokolle für Azure Sentinel zu pullen. Zeigen Sie WireX NFP-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: c9f6151986aea62946eb238861207c590b045d51
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695091"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>Verbinden der WireX Network Forensics Platform-Appliance (NFP) mit Azure Sentinel

> [!IMPORTANT]
> Der NFP-Connector von WireX Systems befindet sich derzeit in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

In diesem Artikel wird beschrieben, wie Sie Ihre WireX Systems NFP-Appliance (Network Forensics Platform) mit Azure Sentinel verbinden. Mit dem WireX NFP-Datenconnector lassen sich Ihre NFP-Protokolle problemlos mit Azure Sentinel verbinden. Anschließend können Sie die Daten dann in Arbeitsmappen anzeigen und als Vorlage für benutzerdefinierte Warnungen und zur Verbesserung der Untersuchung von Problemen verwenden. 

> [!NOTE] 
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>Senden von WireX NFP-Protokollen an Azure Sentinel

Um die Protokolle Ihrer WireX Systems NFP-Appliance in Azure Sentinel zu erfassen, müssen Sie die Appliance so konfigurieren, dass sie Syslog-Nachrichten im CEF-Format an einen Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird) sendet. Auf diesem Server ist der Log Analytics-Agent installiert, der die Protokolle an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Datenconnectors** aus.

1. Wählen Sie im Katalog mit den **Datenconnectors** die Option **WireX Network Forensics Platform (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die Anleitung auf der Registerkarte **Anweisungen** unter **Konfiguration**:

    1. **1. Linux-Syslog-Agent-Konfiguration**: Führen Sie diesen Schritt aus, wenn Sie noch keinen Protokollweiterleitungsserver ausführen oder einen weiteren benötigen. Detaillierte Anleitungen und Beschreibungen finden Sie unter [SCHRITT 1: Bereitstellen des Protokollweiterleitungsservers](connect-cef-agent.md) in der Azure Sentinel-Dokumentation.

    1. **2. Weiterleiten von CEF-Protokollen (Common Event Format) an den Syslog-Agent**: Wenden Sie sich an den [WireX-Support](https://wirexsystems.com/contact-us/), um die Informationen zur richtigen Konfiguration Ihrer WireX NFP-Lösung zu erhalten. Diese Konfiguration sollte die folgenden Elemente enthalten:
        - Protokollziel: Hostname und/oder IP-Adresse Ihres Protokollweiterleitungsservers
        - Protokoll und Port: TCP 514 (Falls etwas anderes empfohlen wird, sollten Sie sicherstellen, dass Sie die entsprechende Änderung parallel auch im syslog-Daemon und auf Ihrem Protokollweiterleitungsserver vornehmen.)
        - Protokollformat: CEF
        - Protokolltypen: Alle von WireX empfohlenen Typen

    1. **3. Überprüfen der Verbindung**: Überprüfen Sie die Datenerfassung, indem Sie den Befehl von der Connectorseite kopieren und auf Ihrem Protokollweiterleitungsserver ausführen. Weitere Informationen und Erläuterungen finden Sie in der in der Azure Sentinel-Dokumentation [Überprüfen der CEF-Verbindung](troubleshooting-cef-syslog.md#validate-cef-connectivity).

        Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Um WireX NFP-Daten in Log Analytics abzufragen, kopieren Sie Folgendes in das Abfragefenster, wobei Sie bei der Auswahl andere Filter anwenden:

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

Weitere hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie WireX Systems NFP mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.