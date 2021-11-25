---
title: Verknüpfen von Microsoft 365 Defender-Daten mit Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Incidents, Warnungen und Ereignisrohdaten aus Microsoft 365 Defender in Microsoft Sentinel erfassen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: edef094a6d4db97208124a97732f1ec354f2106c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522059"
---
# <a name="connect-data-from-microsoft-365-defender-to-microsoft-sentinel"></a>Verknüpfen von Daten aus Microsoft 365 Defender mit Microsoft Sentinel

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

Der [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection)-Connector (M365D) von Microsoft Sentinel mit Incidentintegration ermöglicht es Ihnen, alle M365D-Incidents und -Warnungen in Microsoft Sentinel zu streamen. Außerdem sorgt er dafür, dass die Incidents zwischen den beiden Portalen synchronisiert werden. M365D-Incidents enthalten alle dazugehörigen Warnungen, Entitäten und andere relevante Informationen. Sie werden mit Warnungen aus den Komponentendiensten von M365D, **Microsoft Defender für Endpunkt**, **Microsoft Defender for Identity**, **Microsoft Defender für Office 365** und **Microsoft Defender for Cloud Apps**, angereichert und mit diesen zusammen gruppiert.

Mit dem Connector können Sie auch Ereignisse aus der **erweiterten Bedrohungssuche** von Microsoft Defender für Endpunkt und Microsoft Defender für Office 365 an Microsoft Sentinel streamen. So können Sie die Abfragen für die erweiterte Bedrohungssuche dieser Defender-Komponenten in Microsoft Sentinel kopieren, Sentinel-Warnungen mit den Ereignisrohdaten der Defender-Komponenten anreichern, um zusätzliche Erkenntnisse zu gewinnen, und die Protokolle mit einer längeren Aufbewahrungsdauer in Log Analytics speichern.

Weitere Informationen zur Incidentintegration und zur Erfassung von Ereignissen aus der erweiterten Bedrohungssuche finden Sie unter [Microsoft 365 Defender-Integration in Microsoft Sentinel](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection).

> [!IMPORTANT]
>
> Der Microsoft 365 Defender-Connector ist derzeit als **VORSCHAU** verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="prerequisites"></a>Voraussetzungen

- Wie unter [Voraussetzungen für Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites) beschrieben, benötigen Sie eine gültige Lizenz für Microsoft 365 Defender. 

- Sie müssen in Azure Active Directory ein **globaler Administrator** oder **Sicherheitsadministrator** sein.

## <a name="connect-to-microsoft-365-defender"></a>Herstellen einer Verbindung mit Microsoft 365 Defender

1. Klicken Sie in Microsoft Sentinel auf **Datenconnectors**, dann im Katalog auf **Microsoft 365 Defender (Preview)** und anschließend auf **Connectorseite öffnen**.

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

In diesem Artikel haben Sie erfahren, wie Sie Microsoft 365 Defender-Incidents und Ereignisse aus der erweiterten Bedrohungssuche von Microsoft Defender für Endpunkt und Defender für Office 365 mithilfe des Microsoft 365 Defender-Connectors in Microsoft Sentinel integrieren. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mit Microsoft Sentinel](./detect-threats-built-in.md).
