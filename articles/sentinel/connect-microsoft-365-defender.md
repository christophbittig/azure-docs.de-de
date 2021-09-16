---
title: Verknüpfen von Microsoft 365 Defender-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Incidents, Warnungen und Ereignisrohdaten aus Microsoft 365 Defender in Azure Sentinel erfassen.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: c31ed32e13e8b71b5ad6253d5e157b5adc2096bf
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260022"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Verknüpfen von Daten aus Microsoft 365 Defender mit Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** wurde bisher als **Microsoft Threat Protection** oder **MTP** bezeichnet.
>
> **Microsoft Defender for Endpoint** wurde bisher als **Microsoft Defender Advanced Threat Protection** oder **MDATP** bezeichnet.
>
> Die alten Namen werden möglicherweise eine Zeit lang weiterhin verwendet.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="background"></a>Hintergrund

Der [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection)-Connector (M365D) von Azure Sentinel mit Incidentintegration ermöglicht es Ihnen, alle M365D-Incidents und -Warnungen in Azure Sentinel zu streamen. Außerdem sorgt er dafür, dass Incidents zwischen den Portalen synchronisiert werden. M365D-Incidents enthalten alle dazugehörigen Warnungen, Entitäten und andere relevante Informationen. Diese Informationen werden angereichert. Warnungen aus den Komponentendiensten von M365D, **Microsoft Defender für Endpunkt**, **Microsoft Defender for Identity**, **Microsoft Defender für Office 365** und **Microsoft Cloud App Security**, werden zusammen gruppiert.

Der Connector ermöglicht Ihnen auch das Streamen von Ereignissen der **erweiterten Bedrohungssuche** aus Microsoft Defender für Endpunkt zu Azure Sentinel. So können Sie erweiterte Abfragen der erweiterten Bedrohungssuche in Defender für Endpunkt in Azure Sentinel kopieren, Sentinel-Warnungen mit Rohereignisdaten aus Defender für Endpunkt anreichen, um zusätzliche Erkenntnisse zu ermöglichen, und die Protokolle mit erhöhter Aufbewahrungsdauer in Log Analytics speichern.

Weitere Informationen zur Incidentintegration und zur Erfassung von Ereignissen der erweiterten Bedrohungssuche finden Sie unter [Microsoft 365 Defender-Integration mit Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> Der Microsoft 365 Defender-Connector ist derzeit als **VORSCHAU** verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="prerequisites"></a>Voraussetzungen

- Wie unter [Voraussetzungen für Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites) beschrieben, benötigen Sie eine gültige Lizenz für Microsoft 365 Defender. 

- Sie müssen in Azure Active Directory ein **globaler Administrator** oder **Sicherheitsadministrator** sein.

## <a name="connect-to-microsoft-365-defender"></a>Herstellen einer Verbindung mit Microsoft 365 Defender

1. Wählen Sie in Azure Sentinel **Datenconnectors**, dann im Katalog **Microsoft 365 Defender (Vorschau)** und anschließend **Connectorseite öffnen** aus.

1. Klicken Sie unter **Configuration** (Konfiguration) im Abschnitt **Connect incidents & alerts** (Incidents und Warnungen verknüpfen) auf die Schaltfläche **Connect incidents & alerts** (Incidents und Warnungen verknüpfen).

1. Damit Incidents nicht dupliziert werden, sollten Sie das Kontrollkästchen mit der Bezeichnung **Turn off all Microsoft incident creation rules for these products** (Alle Incidenterstellungsregeln von Microsoft für diese Produkte deaktivieren) aktivieren.

    > [!NOTE]
    > Wenn Sie den Microsoft 365 Defender-Connector aktivieren, werden alle Connectors der M365D-Komponenten (wie zu Beginn dieses Artikels erwähnt) automatisch im Hintergrund verbunden. Damit die Verbindung eines der Connectors der Komponenten getrennt wird, müssen Sie zunächst die Verbindung des Microsoft 365 Defender-Connectors aufheben.

1. Wenn Sie Microsoft 365 Defender-Incidentdaten abfragen möchten, verwenden Sie die folgende Anweisung im Abfragefenster:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Wenn Sie Ereignisse der erweiterten Bedrohungssuche aus Microsoft Defender für Endpunkt erfassen möchten, können die folgenden Ereignistypen aus den entsprechenden Tabellen für die erweiterte Bedrohungssuche erfasst werden.

    1. Aktivieren Sie die Kontrollkästchen der Tabellen mit den Ereignistypen, die Sie erfassen möchten:

       | Tabellenname | Ereignistyp |
       |-|-|
       | DeviceInfo | Computerinformationen (einschließlich Betriebssysteminformationen) |
       | DeviceNetworkInfo | Netzwerkeigenschaften von Computern |
       | DeviceProcessEvents | Prozesserstellung und zugehörige Ereignisse |
       | DeviceNetworkEvents | Netzwerkverbindung und zugehörige Ereignisse |
       | DeviceFileEvents | Dateierstellung, Änderung und andere Dateisystemereignisse |
       | DeviceRegistryEvents | Erstellen und Ändern von Registrierungseinträgen |
       | DeviceLogonEvents | Anmeldungen und andere Authentifizierungsereignisse |
       | DeviceImageLoadEvents | DLL-Ladeereignisse |
       | DeviceEvents | Weitere Ereignistypen |
       | DeviceFileCertificateInfo | Zertifikatinformationen der signierten Dateien |
       |

    1. Klicken Sie auf **Apply Changes**.

    1. Zum Abfragen der erweiterten Huntingtabellen in Log Analytics geben Sie den Tabellennamen aus der Liste oben im Abfragefenster ein.

## <a name="verify-data-ingestion"></a>Überprüfen der Datenerfassung

Das Datendiagramm auf der Connectorseite weist darauf hin, dass Sie Daten erfassen. Wie Sie sehen können wird für Incidents, Warnungen und Ereignisse jeweils eine Zeile angezeigt. Bei der Ereigniszeile handelt es sich um eine Aggregation des Ereignisvolumens für alle aktivierten Tabellen. Sobald Sie den Connector aktiviert haben, können Sie die folgenden KQL-Abfragen verwenden, um spezifischere Graphen zu generieren.

Verwenden Sie die folgende KQL-Abfrage für einen Graph der eingehenden Microsoft 365 Defender-Incidents:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Verwenden Sie die folgenden KQL-Abfrage, um einen Graph des Ereignisvolumens für eine einzelne Tabelle zu erzeugen. Ändern Sie die Tabelle *DeviceEvents* in die erforderliche Tabelle Ihrer Wahl:

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Auf der Registerkarte **Next steps** (Weitere Schritte) finden Sie hilfreiche Arbeitsmappen, Beispielabfragen und Vorlagen für Analyseregeln, die enthalten sind. Sie können sie sofort ausführen oder sie ändern und speichern.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Microsoft 365 Defender-Incidents sowie Ereignisdaten der erweiterten Bedrohungssuche aus Microsoft Defender für Endpunkt in Azure Sentinel integrieren. Dafür haben Sie den Microsoft 365 Defender-Connector verwendet. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./detect-threats-built-in.md).
