---
title: Inhalte des erweiterten SIEM-Informationsmodells (ASIM) | Microsoft-Dokumentation
description: In diesem Artikel werden die Microsoft Sentinel-Inhalte beschrieben, die das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) verwendet haben.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 78b8615a959a5913b171b8f52b5f6ee0caa85578
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517480"
---
# <a name="advanced-siem-information-model-asim-security-content--public-preview"></a>Sicherheitsinhalte des erweiterten SIEM-Informationsmodells (ASIM) (Public Preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Zu den normalisierten Sicherheitsinhalten in Microsoft Sentinel gehören Analyseregeln, Hunting-Abfragen und Arbeitsmappen, die mit quellenunabhängigen Normalisierungsparsern arbeiten.

<a name="builtin"></a>Sie finden normalisierte, integrierte Inhalte in Microsoft Sentinel-Katalogen und [Lösungen](sentinel-solutions-catalog.md), erstellen Sie Ihre eigenen normalisierten Inhalte, oder ändern Sie bestehende Inhalte, um normalisierte Daten zu verwenden.

In diesem Artikel werden integrierte Microsoft Sentinel-Inhalte aufgeführt, die für die Unterstützung des erweiterten SIEM-Informationsmodells (Advanced SIEM Information Model, ASIM) konfiguriert wurden.  Im Folgenden finden Sie Links zum GitHub-Repository für Microsoft Sentinel, aber Sie können auch im [Microsoft Sentinel-Katalog für Analyseregeln](detect-threats-built-in.md) nach diesen Regeln suchen. Verwenden Sie die verlinkten GitHub-Seiten, um alle relevanten Hunting-Abfragen zu kopieren.

> [!TIP]
> Sehen Sie sich auch das [ausführliche Webinar zu normalisierten Parsern und Inhalten in Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) oder die [Folien](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM) an. Weitere Informationen finden Sie in den [nächsten Schritten](#next-steps).
>

> [!IMPORTANT]
> ASIM befindet sich derzeit in der Vorschauphase. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="authentication-security-content"></a>Inhalt der Authentifizierungssicherheit

Der folgende integrierte Authentifizierungsinhalt wird für die ASIM-Normalisierung unterstützt.

### <a name="analytics-rules"></a>Analyseregeln

 - [Potenzieller Kennwortspray-Angriff (Verwendung der Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [Brute-Force-Angriff auf Anmeldeinformationen von Benutzern (Verwendung der Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [Benutzeranmeldung aus verschiedenen Ländern innerhalb von 3 Stunden (Verwendung der Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [Anmeldungen von IPs, die versuchen, sich bei deaktivierten Konten anzumelden (verwendet die Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


## <a name="dns-query-security-content"></a>Sicherheitsinhalt von DNS-Abfragen

Der folgende integrierte DNS-Abfrageinhalt wird für die ASIM-Normalisierung unterstützt.

### <a name="analytics-rules"></a>Analyseregeln

 - (Preview) TI map Domain entity to DNS Events (Normalized DNS)
 - (Preview) TI map IP entity to DNS Events (Normalized DNS)
 - [Potenzielle DGA erkannt (ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
  - [Übermäßige NXDOMAIN-DNS-Abfragen (normalisiertes DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [DNS-Ereignisse im Zusammenhang mit Miningpools (normalisiertes DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [DNS-Ereignisse im Zusammenhang mit ToR-Proxys (normalisiertes DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Bekannte Barium-Domänen](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Bekannte Barium-IP-Adressen](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Exchange Server: im März 2021 offengelegte Sicherheitsrisiken – IoC-Übereinstimmung](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [Known GALLIUM domains and hashes (Bekannte GALLIUM-Domänen und -Hashes)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (Bekannte IRIDIUM-IP-Adresse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - Domain and IP IOCs - March 2021 (NOBELIUM – Domänen- und IP-IOCs – März 2021)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [Known Phosphorus group domains/IP (Bekannte Phosphorus-Gruppendomänen/IP-Adressen)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Bekannte STRONTIUM-Gruppendomänen – Juli 2019](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Solorigate Network Beacon (Solorigate-Netzwerkbeacon)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [THALLIUM domains included in DCU takedown (Im DCU-Takedown enthaltene THALLIUM-Domänen)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Bekannte ZINC Comebacker- und Klackring-Schadsoftwarehashes](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="file-activity-security-content"></a>Sicherheitsinhalt der Dateiaktivität

Der folgende integrierte Dateiaktivitätsinhalt wird für die ASIM-Normalisierung unterstützt.

### <a name="analytic-rules"></a>Analyseregeln

- [SUNBURST- und SUPERNOVA-Hintertürhashes (normalisierte Dateiereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Exchange Server: im März 2021 offengelegte Sicherheitsrisiken – IoC-Übereinstimmung](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [Schreiben verdächtiger Dateien durch den HAFNIUM UM-Dienst](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM – Domänen-, Hash- und IP-IOCs – März 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [SUNSPOT-Protokolldateierstellung](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [Bekannte ZINC Comebacker- und Klackring-Schadsoftwarehashes](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="process-activity-security-content"></a>Prozessaktivität Sicherheitsinhalt

Der folgende integrierte Prozessaktivitätsinhalt wird für die ASIM-Normalisierung unterstützt.

### <a name="analytics-rules"></a>Analyseregeln

 - [Wahrscheinliche Verwendung des AdFind Recon-Tools (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
 - [Base64-codierte Windows-Prozessbefehlszeilen (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
 - [Schadsoftware im Papierkorb (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
 - [NOBELIUM – verdächtige rundll32.exe-Ausführung von vbscript (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
 - [SUNBURST – verdächtige untergeordnete SolarWinds-Prozesse (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

### <a name="hunting-queries"></a>Hunting-Abfragen

 - [Aufschlüsselung der täglichen Zusammenfassung des CScript-Skripts (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
 - [Enumeration von Benutzern und Gruppen (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
 - [Exchange PowerShell-Snap-In hinzugefügt (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
 - [Host exportiert Postfach und Entfernen des Exports (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
 - [Invoke-PowerShellTcpOneLine-Syntax (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
 - [Nishang Reverse TCP Shell in Base64 (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
 - [Zusammenfassung der Benutzer, die mit ungewöhnlichen/nicht dokumentierten Befehlszeilenschaltern erstellt wurden (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
 - [Powercat-Download (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
 - [PowerShell-Downloads (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
 - [Entropie für Prozesse für einen bestimmten Host (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
 - [SolarWinds-Inventar (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
 - [Verdächtige Enumeration mithilfe des Adfind-Tools (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
 - [Herunterfahren/Neustarten des Windows-Systems (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
 - [Certutil (LOLBins und LOLScripts, normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
 - [Rundll32 (LOLBins und LOLScripts, normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
 - [Ungewöhnliche Prozesse – unter 5 % (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
 - [Unicode Obfuscation in der Befehlszeile](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)

## <a name="registry-activity-security-content"></a>Registrierungsaktivitätsinhalt

Der folgende integrierte Registrierungsaktivitätsinhalt wird für die ASIM-Normalisierung unterstützt.

### <a name="hunting-queries"></a>Hunting-Abfragen

- [Persistieren über IFEO-Registrierungsschlüssel](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml)

## <a name="modify-your-content-to-use-normalized-data"></a><a name="modify"></a>Ändern Sie Ihren Inhalt, um normalisierte Daten zu verwenden

So aktivieren Sie benutzerdefinierte Inhalte zur Verwendung der Normalisierung

- Ändern Sie Ihre Abfragen so, dass die für die Abfrage relevanten quellenunabhängigen Parser verwendet werden.
- Ändern Sie die Feldnamen in der Abfrage so, dass die Namen der normalisierten Schemafelder verwendet werden.
- Ändern Sie ggf. die Bedingungen so, dass die normalisierten Werte der Felder in der Abfrage verwendet werden.

Als Beispiel dient die DNS-Analyseregel **Rare client observed with high reverse DNS lookup count**, die auf DNS-Ereignisse angewandt wird, die von Infoblox-DNS-Servern gesendet werden:

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

Der folgende Code ist die quellenunabhängige Version, die die Normalisierung nutzt, um dieselbe Erkennung für jede Quelle, die DNS-Abfrageereignisse liefert, bereitzustellen:

```kusto
imDns(responsecodename='NXDOMAIN')
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns(responsecodename='NXDOMAIN')) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr```
```

Die normalisierte, quellenunabhängige Version weist die folgenden Unterschiede auf:

- Anstelle des Infoblox-Parsers wird der normalisierte Parser `imDns` verwendet.

- `imDns` ruft nur DNS-Abfrageereignisse ab, sodass eine Überprüfung des Ereignistyps, wie sie in der Infoblox-Version über `where ProcessName =~ "named" and Log_Type =~ "client"` erfolgt, nicht erforderlich ist.

- Dieses Feld `SrcIpAddr` wird anstelle von `Client_IP` verwendet.
 
- Die Parserparameterfilterung wird für ResponseCodeName verwendet, damit keine expliziten where-Klauseln erforderlich sind.


Abgesehen von der Unterstützung normalisierter DNS-Quellen ist die normalisierte Version kürzer und einfacher zu verstehen. 

Wenn das Schema oder die Parser das Filtern von Parametern nicht unterstützen, sind die Änderungen mit Ausnahme der letzten ähnlich. Stattdessen werden die Filterbedingungen aus der ursprünglichen Abfrage beibehalten, wie unten dargestellt:

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

## <a name="next-steps"></a><a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden die Inhalte für das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) erläutert.

Weitere Informationen finden Sie unter

- Sehen Sie sich das [ausführliche Webinar zu normalisierten Parsern und Inhalten in Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) oder die [Folien](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM) an.
- [Übersicht über das erweiterte SIEM-Informationsmodell](normalization.md)
- [Schemas des erweiterten SIEM-Informationsmodells](normalization-about-schemas.md)
- [Parser des erweiterten SIEM-Informationsmodells](normalization-about-parsers.md)