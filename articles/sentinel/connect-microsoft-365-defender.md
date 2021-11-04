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
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1f55f38e126ae9aa64752b45ff449bcde321aaaf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083933"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Verknüpfen von Daten aus Microsoft 365 Defender mit Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **Microsoft 365 Defender** wurde bisher als **Microsoft Threat Protection** oder **MTP** bezeichnet.
>
> **Microsoft Defender for Endpoint** wurde bisher als **Microsoft Defender Advanced Threat Protection** oder **MDATP** bezeichnet.
>
> **Microsoft Defender für Office 365** war früher bekannt als **Office 365 Advanced Threat Protection**.
>
> Die alten Namen werden möglicherweise eine Zeit lang weiterhin verwendet.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="background"></a>Hintergrund

Der [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection)-Connector (M365D) von Azure Sentinel mit Incidentintegration ermöglicht es Ihnen, alle M365D-Incidents und -Warnungen in Azure Sentinel zu streamen. Außerdem sorgt er dafür, dass Incidents zwischen den Portalen synchronisiert werden. M365D-Incidents enthalten alle dazugehörigen Warnungen, Entitäten und andere relevante Informationen. Diese Informationen werden angereichert. Warnungen aus den Komponentendiensten von M365D, **Microsoft Defender für Endpunkt**, **Microsoft Defender for Identity**, **Microsoft Defender für Office 365** und **Microsoft Cloud App Security**, werden zusammen gruppiert.

Mit dem Connector können Sie auch **Advanced Hunting-Ereignisse** von Microsoft Defender for Endpoint und Microsoft Defender for Office 365 in Azure Sentinel streamen. So können Sie die Advanced Hunting-Abfragen dieser Defender-Komponenten in Azure Sentinel kopieren, Sentinel-Warnungen mit den Ereignisrohdaten der Defender-Komponenten anreichern, um zusätzliche Erkenntnisse zu gewinnen, und die Protokolle mit erhöhter Aufbewahrungsdauer in Log Analytics speichern.

Weitere Informationen zur Incidentintegration und zur Erfassung von Ereignissen der erweiterten Bedrohungssuche finden Sie unter [Microsoft 365 Defender-Integration mit Azure Sentinel](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection).

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

1. Wenn Sie erweiterte Suchereignisse von Microsoft Defender für Endpoint oder Microsoft Defender für Office 365 sammeln möchten, können die folgenden Ereignistypen aus den entsprechenden erweiterten Suchtabellen gesammelt werden.

    1. Aktivieren Sie die Kontrollkästchen der Tabellen mit den Ereignistypen, die Sie erfassen möchten:

       # <a name="defender-for-endpoint"></a>[Defender für Endpunkt](#tab/MDE)

       | Tabellenname | Ereignistyp |
       |-|-|
       | **[DeviceInfo](/microsoft-365/security/defender/advanced-hunting-deviceinfo-table)** | Maschineninformationen, einschließlich Betriebssysteminformationen |
       | **[DeviceNetworkInfo](/microsoft-365/security/defender/advanced-hunting-devicenetworkinfo-table)** | Netzwerkeigenschaften von Geräten, einschließlich physischer Adapter, IP- und MAC-Adressen sowie verbundener Netzwerke und Domänen |
       | **[DeviceProcessEvents](/microsoft-365/security/defender/advanced-hunting-deviceprocessevents-table)** | Prozesserstellung und zugehörige Ereignisse |
       | **[DeviceNetworkEvents](/microsoft-365/security/defender/advanced-hunting-devicenetworkevents-table)** | Netzwerkverbindung und zugehörige Ereignisse |
       | **[DeviceFileEvents](/microsoft-365/security/defender/advanced-hunting-devicefileevents-table)** | Dateierstellung, Änderung und andere Dateisystemereignisse |
       | **[DeviceRegistryEvents](/microsoft-365/security/defender/advanced-hunting-deviceregistryevents-table)** | Erstellen und Ändern von Registrierungseinträgen |
       | **[DeviceLogonEvents](/microsoft-365/security/defender/advanced-hunting-devicelogonevents-table)** | Anmeldungen und andere Authentifizierungsereignisse auf Geräten |
       | **[DeviceImageLoadEvents](/microsoft-365/security/defender/advanced-hunting-deviceimageloadevents-table)** | DLL-Ladeereignisse |
       | **[DeviceEvents](/microsoft-365/security/defender/advanced-hunting-deviceevents-table)** | Mehrere Ereignistypen, einschließlich Ereignissen, die durch Sicherheitskontrollen wie Windows Defender Antivirus und Exploit-Schutz ausgelöst werden |
       | **[DeviceFileCertificateInfo](/microsoft-365/security/defender/advanced-hunting-DeviceFileCertificateInfo-table)** | Zertifikatsinformationen von signierten Dateien, die aus Zertifikatsüberprüfungsereignissen auf Endpunkten gewonnen werden |
       |

       # <a name="defender-for-office-365"></a>[Defender für Office 365](#tab/MDO)

       | Tabellenname | Ereignistyp |
       |-|-|
       | **[EmailAttachmentInfo](/microsoft-365/security/defender/advanced-hunting-emailattachmentinfo-table)** | Informationen über an E-Mails angehängte Dateien |
       | **[EmailEvents](/microsoft-365/security/defender/advanced-hunting-emailevents-table)** | Microsoft 365 E-Mail-Ereignisse, einschließlich E-Mail-Zustellungs- und Blockierungsereignisse |
       | **[EmailPostDeliveryEvents](/microsoft-365/security/defender/advanced-hunting-emailpostdeliveryevents-table)** | Sicherheitsereignisse, die nach der Zustellung auftreten, nachdem Microsoft 365 die E-Mails an das Empfängerpostfach zugestellt hat |
       | **[EmailUrlInfo](/microsoft-365/security/defender/advanced-hunting-emailurlinfo-table)** | Informationen über URLs in E-Mails |
       |

       ---

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

In diesem Dokument erfahren Sie, wie Sie Microsoft 365 Defender-Vorfälle und erweiterte Jagd-Ereignisdaten aus Microsoft Defender für Endpoint und Defender für Office 365 mithilfe des Microsoft 365 Defender-Connectors in Azure Sentinel integrieren. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./detect-threats-built-in.md).
