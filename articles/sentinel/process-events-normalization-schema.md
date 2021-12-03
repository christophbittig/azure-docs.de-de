---
title: 'Microsoft Sentinel: Referenz zum Prozessereignis-Normalisierungsschema | Microsoft-Dokumentation'
description: In diesem Artikel wird das Prozessereignis-Normalisierungsschema von Microsoft Sentinel beschrieben.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: cff85330635a5c6f9501f6064d36fa623159df22
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522819"
---
# <a name="microsoft-sentinel-process-event-normalization-schema-reference-public-preview"></a>Microsoft Sentinel: Referenz zum Prozessereignis-Normalisierungsschema (Öffentliche Vorschau)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das Prozessereignis-Normalisierungsschema wird verwendet, um die Betriebssystemaktivität zum Ausführen und Beenden eines Prozesses zu beschreiben. Solche Ereignisse werden von Betriebssystemen und Sicherheitssystemen wie EDR (End Point Detection and Response) gemeldet.

Ein Prozess, wie von OSSEM definiert, ist ein Eigenständigkeits- und Verwaltungsobjekt, das eine aktuell ausgeführte Instanz eines Programms darstellt. Prozesse selbst werden zwar nicht ausgeführt, verwalten aber Threads, die Code ausführen.

Weitere Informationen zu Normalisierung in Microsoft Sentinel finden Sie unter [Normalisierung und das Advanced SIEM Information Model (ASIM)](normalization.md).

> [!IMPORTANT]
> Das Prozessereignis-Normalisierungsschema befindet sich derzeit in der VORSCHAU. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
>
> In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="parsers"></a>Parser

Microsoft Sentinel bietet die folgenden integrierten, produktspezifischen Prozessereignisparser:

- **Erstellung des Prozesses für sicherheitsrelevante Ereignisse (Ereignis 4688)** , erfasst mit dem Log Analytics-Agent oder mit Azure Monitor
- **Beendigung des Prozesses für sicherheitsrelevante Ereignisse (Ereignis 4689)** , erfasst mit dem Log Analytics-Agent oder mit Azure Monitor
- **Sysmon-Prozesserstellung (Ereignis 1)** , erfasst mit dem Log Analytics-Agent oder mit Azure Monitor
- **Sysmon-Prozessbeendigung (Ereignis 5)** , erfasst mit dem Log Analytics-Agent oder mit Azure Monitor
- **Microsoft 365 Defender für die Erstellung von Endpunktprozessen**

Um die quellunabhängigen Parser zu verwenden, die alle aufgelisteten Parser vereinheitlichen und sicherstellen, dass Sie alle konfigurierten Quellen analysieren, verwenden Sie die folgenden Tabellennamen in Ihren Abfragen:

- **imProcessCreate** für Abfragen, die Informationen zur Prozesserstellung erfordern. Diese Abfragen sind am häufigsten.
- **imProcessTerminate** für Abfragen, die Informationen zur Prozessbeendigung erfordern.
- **imProcessEvents** für Abfragen, die Informationen zur Prozesserstellung und zur Prozessbeendigung erfordern. In solchen Fällen ermöglicht das Feld `EventType` die Unterscheidung zwischen den Ereignissen und wird auf `ProcessCreate` bzw. `ProcessTerminate` festgelegt. Ereignisse zur Prozessbeendigung enthalten im Allgemeinen viel weniger Informationen als Ereignisse zur Prozesserstellung.

Stellen Sie die [quellunabhängigen und quellspezifischen Parser](normalization-about-parsers.md) aus dem [Microsoft Sentinel GitHub-Repository](https://aka.ms/AzSentinelProcessEvents) bereit.

## <a name="add-your-own-normalized-parsers"></a>Hinzufügen eigener normalisierter Parser

Wenn Sie benutzerdefinierte Parser für das [Prozessereignis](normalization-about-schemas.md#the-process-entity)-Informationsmodell implementieren, benennen Sie Ihre KQL-Funktionen mit der folgenden Syntax: `imProcess<Type><vendor><Product>`, wobei `Type` entweder `Create`, `Terminate` oder `Event` ist, wenn der Parser sowohl Erstellungs- als auch Beendigungsereignisse implementiert.

Fügen Sie Ihre KQL-Funktion den quellunabhängigen Parsern `imProcess<Type>` und `imProcess` hinzu, um sicherzustellen, dass alle Inhalte, die das Modell [Prozessereignis](normalization-about-schemas.md#the-process-entity) verwenden, auch Ihren neuen Parser verwenden.

## <a name="normalized-content-for-process-activity-data"></a>Normalisierter Inhalt für Prozessaktivitätsdaten

Der folgende Microsoft Sentinel-Inhalt funktioniert mit jeder Prozessaktivität, die mithilfe des Informationsmodells von Advanced SIEM normalisiert wird:

- **Analyseregeln**:

   - [Wahrscheinliche Verwendung des AdFind Recon-Tools (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
   - [Base64-codierte Windows-Prozessbefehlszeilen (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
   - [Schadsoftware im Papierkorb (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
   - [NOBELIUM – verdächtige rundll32.exe-Ausführung von vbscript (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
   - [SUNBURST – verdächtige untergeordnete SolarWinds-Prozesse (normalisierte Prozessereignisse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

   Weitere Informationen finden Sie unter [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md).

- **Hunting-Abfragen**:
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


    Weitere Informationen finden Sie unter [Suchen nach Bedrohungen mit Microsoft Sentinel](hunting.md).

## <a name="schema-details"></a>Schemadetails

Das Prozessereignis-Informationsmodell ist auf das [OSSEM-Prozessentitätsschema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/process.md) ausgerichtet.

### <a name="log-analytics-fields"></a>Log Analytics-Felder

Die folgenden Felder werden von Log Analytics für jeden Datensatz generiert und können beim Erstellen eines benutzerdefinierten Connectors überschrieben werden.

| Feld         | type     | Diskussion (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde.|
| **_ResourceId**   | guid     | Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden. |
| **Type** | String | Die ursprüngliche Tabelle, aus der der Datensatz abgerufen wurde. Dieses Feld ist nützlich, wenn ein und dasselbe Ereignis über mehrere Kanäle in verschiedenen Tabellen empfangen werden kann und die gleichen EventVendor- und EventProduct-Werte hat.<br><br>Ein Sysmon-Ereignis kann z. B. entweder in der Tabelle Event oder in der Tabelle WindowsEvent gesammelt werden. |
| | | |

> [!NOTE]
> Log Analytics fügt auch andere Felder hinzu, die für Sicherheitsanwendungsfälle weniger relevant sind. Weitere Informationen finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../azure-monitor/logs/log-standard-columns.md).
>

## <a name="event-fields"></a>Ereignisfelder

Ereignisfelder sind allen Schemas gemeinsam und beschreiben die Aktivität selbst und das meldende Gerät.

| Feld               | Klasse       | type       |  BESCHREIBUNG        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Optional    | String     |     Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert.   |
| **EventCount**          | Obligatorisch.   | Integer    |     Die Anzahl der Ereignisse, die vom Datensatz beschrieben werden. <br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen. <br><br>Legen Sie den Wert für andere Quellen auf `1` fest.   |
| **EventStartTime**      | Obligatorisch.   | Datum/Uhrzeit  |      Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, gibt dieses Feld die Zeit an, zu der das erste Ereignis generiert wurde. Andernfalls wird in diesem Feld ein Alias für das Feld [TimeGenerated](#timegenerated) verwendet. |
| **EventEndTime**        | Obligatorisch.   | Alias      |      Alias für das Feld [TimeGenerated](#timegenerated).    |
| **EventType**           | Obligatorisch.   | Enumerated |    Beschreibt den vom Datensatz gemeldeten Vorgang. <br><br>Für Prozessdatensätze werden folgende Werte unterstützt: <br>- `ProcessCreated` <br>- `ProcessTerminated` |
| **EventResult**         | Obligatorisch.   | Enumerated |  Beschreibt das Ergebnis des Ereignisses, das auf einen der folgenden unterstützten Werte normalisiert wird: <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (nicht zutreffend) <br><br>Die Quelle stellt möglicherweise nur einen Wert für das Feld **EventResultDetails** bereit, das analysiert werden muss, um den Wert **EventResult** zu erhalten.<br><br>**Beachten** Sie, dass Prozessereignisse in der Regel nur Erfolg melden. |
| **EventOriginalUid**    | Optional    | String     |   Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Optional    | String     |   Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `4688`|
| <a name ="eventproduct"></a>**EventProduct**        | Obligatorisch.   | String     |             Das Produkt, das das Ereignis erzeugt. <br><br>Beispiel: `Sysmon`<br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.           |
| **EventProductVersion** | Optional    | String     | Die Version des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `12.1`      |
| **EventVendor**         | Obligatorisch.   | String     |           Der Hersteller des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `Microsoft`  <br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.  |
| **EventSchemaVersion**  | Obligatorisch.   | String     |    Die Version des Schemas. Die hier dokumentierte Version des Schemas ist `0.1`.         |
| **EventReportUrl**      | Optional    | String     | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die zusätzliche Informationen zum Ereignis enthält.|
| **Dvc** | Obligatorisch.       | String     |                Ein eindeutiger Bezeichner des Geräts, auf dem das Ereignis aufgetreten ist. <br><br>Dieses Feld kann ein Alias für die Felder [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die es kein offensichtliches Gerät gibt, den gleichen Wert wie das Feld [Ereignisprodukt](#eventproduct).            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Empfohlen | IP-Adresse |         Die IP-Adresse des Geräts, auf dem das Prozessereignis aufgetreten ist.  <br><br>Beispiel: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Empfohlen | Hostname   |               Der Hostname des Geräts, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Optional    | String     |  Die eindeutige ID des Geräts, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Optional    | MAC        |   Die MAC-Adresse des Geräts, auf dem das Prozessereignis aufgetreten ist.  <br><br>Beispiel: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Optional    | String     |         Das Betriebssystem, das auf dem Gerät ausgeführt wird, auf dem das Prozessereignis aufgetreten ist.    <br><br>Beispiel: `Windows`    |
| **DvcOsVersion**        | Optional    | String     |   Die Version des Betriebssystems auf dem Gerät, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `10` |
| **AdditionalFields**    | Optional    | Dynamisch    | Wenn Ihre Quelle zusätzliche Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie ihm die zusätzlichen Informationen als Schlüssel-Wert-Paare hinzu.    |
| | | | |

### <a name="process-event-specific-fields"></a>Prozessereignisspezifische Felder

Die in der folgenden Tabelle aufgeführten Felder sind spezifisch für Prozessereignisse. Sie ähneln jedoch Feldern in anderen Schemas und folgen ähnlichen Benennungskonventionen.

Das Prozessereignisschema verweist auf die folgenden Entitäten, die für die Verarbeitung der Erstellungs- und Beendigungsaktivität von zentraler Bedeutung sind:

- **Actor**. Der Benutzer, der die Prozesserstellung oder -beendigung initiiert hat.
- **ActingProcess**. Der vom Akteur verwendete Prozess, der die Prozesserstellung oder -beendigung initiiert hat.
- **TargetProcess**. Der neue Prozess.
- **TargetUser**. Der Benutzer, dessen Anmeldeinformationen zum Erstellen des neuen Prozesses verwendet werden.
- **ParentProcess**. Der Prozess, der den Actor-Prozess initiiert hat.

| Feld          | Klasse        | type       | BESCHREIBUNG   |
|---------------|--------------|------------|-----------------|
| **Benutzer**           | Alias        |            | Alias für [TargetUsername](#targetusername). <br><br>Beispiel: `CONTOSO\dadmin`     |
| **Process**        | Alias        |            | Alias für [TargetProcessName](#targetprocessname) <br><br>Beispiel: `C:\Windows\System32\rundll32.exe`|
| **CommandLine**    | Alias        |            |     Alias für [TargetProcessCommandLine](#targetprocesscommandline)  |
| **Hash**           | Alias        |            |       Alias für den besten verfügbaren Hash. |
| <a name="actorusername"></a>**ActorUsername**  | Obligatorisch.    | String     | Der Benutzername des Benutzers, der das Ereignis initiiert hat. <br><br>Beispiel: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Obligatorisch.    | Enumerated |   Gibt den Typ des Benutzernamens an, der im Feld [ActorUsername](#actorusername) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity). <br><br>Beispiel: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | Empfohlen  | String     |   Eine eindeutige ID des Akteurs. Die jeweilige ID hängt vom System ab, das das Ereignis generiert. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).  <br><br>Beispiel: `S-1-5-18`    |
| **ActorUserIdType**| Empfohlen  | String     |  Der Typ der ID, die im Feld [ActorUserId](#actoruserid) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity). <br><br>Beispiel: `SID`         |
| **ActorSessionId** | Optional     | String     |   Die eindeutige ID der Anmeldesitzung des Akteurs.  <br><br>Beispiel: `999`<br><br>**Hinweis**: Der Typ ist als *Zeichenfolge* definiert, um unterschiedliche Systeme zu unterstützen, aber unter Windows muss dieser Wert numerisch sein. <br><br>Wenn Sie einen Windows-Computer verwenden und einen anderen Typ verwendet haben, stellen Sie sicher, dass Sie die Werte konvertieren. Wenn Sie beispielsweise einen Hexadezimalwert verwendet haben, konvertieren Sie diesen in einen Dezimalwert.   |
| **ActingProcessCommandLine**       | Optional     | String     |   Die Befehlszeile zum Ausführen des Programms. <br><br>Beispiel: `"choco.exe" -v`    |
| **ActingProcessName**              | Optional     | Zeichenfolge     |   Der Name des agierenden Prozesses. Dieser Name wird häufig vom Image oder von der ausführbaren Datei abgeleitet, die zum Definieren des ursprünglichen Codes und der Daten verwendet wird, die dem virtuellen Adressraum des Prozesses zugeordnet sind.<br><br>Beispiel: `C:\Windows\explorer.exe`  |
| **ActingProcessFileCompany**       | Optional     | String     |           Das Unternehmen, das die Imagedatei des agierenden Prozesses erstellt hat.  <br><br> Beispiel: `Microsoft`    |
| **ActingProcessFileDescription**   | Optional     | String     |  Die Beschreibung, die in die Versionsinformationen der Imagedatei des agierenden Prozesses eingebettet ist. <br><br>Beispiel: `Notepad++ : a free (GPL) source code editor` |
| **ActingProcessFileProduct**       | Optional     | String     |Der Produktname aus den Versionsinformationen in der Imagedatei des agierenden Prozesses. <br><br> Beispiel: `Notepad++`           |
| **ActingProcessFileVersion**       | Optional     | String     |               Die Produktversion aus den Versionsinformationen der Imagedatei des agierenden Prozesses. <br><br>Beispiel: `7.9.5.0`   |
| **ActingProcessFileInternalName**  | Optional     | String     |      Der produktinterne Dateiname aus den Versionsinformationen in der Imagedatei des agierenden Prozesses. |
| **ActingProcessFileOriginalName** | Optional     | String     |Der ursprüngliche Produkdateiname aus den Versionsinformationen der Imagedatei des agierenden Prozesses.       <br><br> Beispiel: `Notepad++.exe` |
| **ActingProcessIsHidden**          | Optional     | Boolean    |      Ein Hinweis darauf, ob sich der agierende Prozess im ausgeblendeten Modus befindet.  |
| **ActingProcessInjectedAddress**   | Optional     | String     |      Die Speicheradresse, an der der verantwortliche agierende Prozess gespeichert ist.           |
| **ActingProcessId**| Obligatorisch.    | String        | Die Prozess-ID (PID) des handelnden Prozesses.<br><br>Beispiel: `48610176`           <br><br>**Hinweis**: Der Typ ist als *Zeichenfolge* definiert, um unterschiedliche Systeme zu unterstützen, aber unter Windows und Linux muss dieser Wert numerisch sein. <br><br>Wenn Sie einen Windows- oder Linux-Computer verwenden und einen anderen Typ verwendet haben, stellen Sie sicher, dass Sie die Werte konvertieren. Wenn Sie beispielsweise einen Hexadezimalwert verwendet haben, konvertieren Sie diesen in einen Dezimalwert.    |
| **ActingProcessGuid**              | Optional     | Zeichenfolge     |  Ein generierter eindeutiger Bezeichner (GUID) des agierenden Prozesses. Ermöglicht die systemübergreifende Identifizierung des Prozesses.  <br><br> Beispiel: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessIntegrityLevel**    | Optional     | String     |       Jeder Prozess verfügt über eine Integritätsebene, die in seinem Token dargestellt wird. Integritätsebenen bestimmen die Prozessebene des Schutzes oder Zugriffs. <br><br> Windows definiert die folgenden Integritätsebenen: **Niedrig**, **Mittel**, **Hoch** und **System**. Standardbenutzer erhalten eine **mittlere** Integritätsebene und erweiterte Benutzer eine **hohe** Integritätsebene. <br><br> Weitere Informationen finden Sie unter [OBligatorische Integritätskontrolle – Win32-Apps](/windows/win32/secauthz/mandatory-integrity-control). |
| **ActingProcessMD5**               | Optional     | String     |Der MD5-Hash der Imagedatei des agierenden Prozesses.  <br><br>Beispiel: `75a599802f1fa166cdadb360960b1dd0`|
| **ActingProcessSHA1**              | Optional     | SHA1       | Der SHA-1-Hash der Imagedatei des agierenden Prozesses.             <br><br>  Beispiel: `d55c5a4df19b46db8c54c801c4665d3338acdab0`  |
| **ActingProcessSHA256**            | Optional     | SHA256     | Der SHA-256--Hash der Imagedatei des agierenden Prozesses.    <br><br> Beispiel: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`   |
| **ActingProcessSHA512**            | Optional     | SHA521     |       Der SHA-512-Hash der Imagedatei des agierenden Prozesses.       |
| **ActingProcessIMPHASH**           | Optional     | String     |       Der Importhash aller Bibliotheks-DLLs, die vom agierenden Prozess verwendet werden.    |
| **ActingProcessCreationTime**      | Optional     | Datetime   |       Datum und Uhrzeit des Starts des agierenden Prozesses. |
| **ActingProcessTokenElevation**    | Optional     | String     | Ein Token, das das Vorhandensein oder Fehlen von UAC-Rechteerweiterung (User Access Control) angibt, die auf den agierenden Prozess angewendet wird.   <br><br>Beispiel: `None`|
| **ActingProcessFileSize**          | Optional     | Long       |      Die Größe der Datei, die den agierenden Prozess ausgeführt hat.   |
| **ParentProcessName**              | Optional     | Zeichenfolge     | Der Name des übergeordneten Prozesses. Dieser Name wird häufig vom Image oder von der ausführbaren Datei abgeleitet, die zum Definieren des ursprünglichen Codes und der Daten verwendet wird, die dem virtuellen Adressraum des Prozesses zugeordnet sind.<br><br>Beispiel: `C:\Windows\explorer.exe` |
| **ParentProcessFileCompany**       | Optional     | String     |Der Name des Unternehmens, das die Imagedatei des übergeordneten Prozesses erstellt hat.            <br><br>    Beispiel: `Microsoft`   |
| **ParentProcessFileDescription**   | Optional     | String     |  Die Beschreibung aus den Versionsinformationen in der Imagedatei des übergeordneten Prozesses.    <br><br>Beispiel: `Notepad++ : a free (GPL) source code editor`|
| **ParentProcessFileProduct**       | Optional     | String     |Der Produktname aus den Versionsinformationen in der Imagedatei des übergeordneten Prozesses.    <br><br>  Beispiel: `Notepad++`  |
| **ParentProcessFileVersion**       | Optional     | String     | Die Produktversion aus den Versionsinformationen in der Imagedatei des übergeordneten Prozesses.    <br><br> Beispiel: `7.9.5.0` |
| **ParentProcessIsHidden**          | Optional     | Boolean    |   Ein Hinweis darauf, ob sich der übergeordnete Prozess im ausgeblendeten Modus befindet.  |
| **ParentProcessInjectedAddress**   | Optional     | String     |    Die Speicheradresse, an der der verantwortliche übergeordnete Prozess gespeichert ist.           |
| **ParentProcessId**| Obligatorisch.    | String    | Die Prozess-ID (PID) des übergeordneten Prozesses.   <br><br>     Beispiel: `48610176`    |
| **ParentProcessGuid**              | Optional     | String     |  Ein generierter eindeutiger Bezeichner (GUID) des übergeordneten Prozesses.  Ermöglicht die systemübergreifende Identifizierung des Prozesses.    <br><br> Beispiel: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |
| **ParentProcessIntegrityLevel**    | Optional     | String     |   Jeder Prozess verfügt über eine Integritätsebene, die in seinem Token dargestellt wird. Integritätsebenen bestimmen die Prozessebene des Schutzes oder Zugriffs. <br><br> Windows definiert die folgenden Integritätsebenen: **Niedrig**, **Mittel**, **Hoch** und **System**. Standardbenutzer erhalten eine **mittlere** Integritätsebene und erweiterte Benutzer eine **hohe** Integritätsebene. <br><br> Weitere Informationen finden Sie unter [OBligatorische Integritätskontrolle – Win32-Apps](/windows/win32/secauthz/mandatory-integrity-control). |
| **ParentProcessMD5**               | Optional     | MD5        | Der MD5-Hash der Imagedatei des übergeordneten Prozesses.  <br><br>Beispiel: `75a599802f1fa166cdadb360960b1dd0`|
| **ParentProcessSHA1**              | Optional     | SHA1       | Der SHA-1-Hash der Imagedatei des übergeordneten Prozesses.       <br><br> Beispiel: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **ParentProcessSHA256**            | Optional     | SHA256     |Der SHA-256-Hash der Imagedatei des übergeordneten Prozesses.      <br><br>  Beispiel: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **ParentProcessSHA512**            | Optional     | SHA512     |    Der SHA-512-Hash der Imagedatei des übergeordneten Prozesses.       |
| **ParentProcessIMPHASH**           | Optional     | String     |    Der Importhash aller Bibliotheks-DLLs, die vom übergeordneten Prozess verwendet werden.    |
| **ParentProcessTokenElevation**    | Optional     | String     |Ein Token, das das Vorhandensein oder Fehlen von UAC-Rechteerweiterung (User Access Control) angibt, die auf den übergeordneten Prozess angewendet wird.     <br><br>  Beispiel: `None` |
| **ParentProcessCreationTime**      | Optional    | Datetime   |    Datum und Uhrzeit des Starts des übergeordneten Prozesses. |
| <a name="targetusername"></a>**TargetUsername** | Obligatorisch für Prozesserstellungsereignisse. | String     | Der Benutzername des Zielbenutzers.  <br><br>Beispiel: `CONTOSO\WIN-GG82ULGC9GO$`      |
| **TargetUsernameType**             | Obligatorisch für Prozesserstellungsereignisse.   | Enumerated | Gibt den Typ des Benutzernamens an, der im Feld [TargetUsername](#targetusername) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).          <br><br>  Beispiel: `Windows`        |
|<a name="targetuserid"></a> **TargetUserId**   | Empfohlen | String     | Eine eindeutige ID des Zielbenutzers. Die jeweilige ID hängt vom System ab, das das Ereignis generiert. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).            <br><br> Beispiel: `S-1-5-18`    |
| **TargetUserIdType**               | Empfohlen | String     | Der Typ der Benutzer-ID, die im Feld [TargetUserId](#targetuserid) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).            <br><br> Beispiel: `SID`  |
| **TargetUserSessionId**            | Optional     | String     |Die eindeutige ID der Anmeldesitzung des Zielbenutzers. <br><br>Beispiel: `999`          <br><br>**Hinweis**: Der Typ ist als *Zeichenfolge* definiert, um unterschiedliche Systeme zu unterstützen, aber unter Windows muss dieser Wert numerisch sein. <br><br>Wenn Sie einen Windows- oder Linux-Computer verwenden und einen anderen Typ verwendet haben, stellen Sie sicher, dass Sie die Werte konvertieren. Wenn Sie beispielsweise einen Hexadezimalwert verwendet haben, konvertieren Sie diesen in einen Dezimalwert.     |
| <a name="targetprocessname"></a>**TargetProcessName**              | Obligatorisch.    | Zeichenfolge     |Der Name des Zielprozesses. Dieser Name wird häufig vom Image oder von der ausführbaren Datei abgeleitet, die zum Definieren des ursprünglichen Codes und der Daten verwendet wird, die dem virtuellen Adressraum des Prozesses zugeordnet sind.   <br><br>     Beispiel: `C:\Windows\explorer.exe`     |
| **TargetProcessFileCompany**       | Optional     | String     |Der Name des Unternehmens, das die Imagedatei des Zielprozesses erstellt hat.   <br><br>   Beispiel: `Microsoft` |
| **TargetProcessFileDescription**   | Optional     | String     | Die Beschreibung aus den Versionsinformationen in der Imagedatei des Zielprozesses.   <br><br>Beispiel: `Notepad++ : a free (GPL) source code editor` |
| **TargetProcessFileProduct**       | Optional     | String     |Der Produktname aus den Versionsinformationen in der Imagedatei des Zielprozesses.  <br><br>  Beispiel: `Notepad++`  |
| **TargetProcessFileSize**          | Optional     | String     |    Größe der Datei, die den für das Ereignis verantwortlichen Prozess ausgeführt hat. |
| **TargetProcessFileVersion**       | Optional     | String     |Die Produktversion aus den Versionsinformationen in der Imagedatei des Zielprozesses.   <br><br>  Beispiel: `7.9.5.0` |
| **TargetProcessFileInternalName**  |    Optional          | String  |   Der produktinterne Dateiname aus den Versionsinformationen in der Imagedatei des Zielprozesses. |
| **TargetProcessFileOriginalName** |       Optional       | String   |   Der ursprüngliche Produkdateiname aus den Versionsinformationen der Imagedatei des Zielprozesses. |
| **TargetProcessIsHidden**          | Optional     | Boolean    |   Ein Hinweis darauf, ob sich der Zielprozess im ausgeblendeten Modus befindet.  |
| **TargetProcessInjectedAddress**   | Optional     | String     |    Die Speicheradresse, an der der verantwortliche Zielprozess gespeichert ist.           |
| **TargetProcessMD5**               | Optional     | MD5        | Der MD5-Hash der Imagedatei des Zielprozesses.   <br><br> Beispiel: `75a599802f1fa166cdadb360960b1dd0`|
| **TargetProcessSHA1**              | Optional     | SHA1       | Der SHA-1-Hash der Imagedatei des Zielprozesses.       <br><br>  Beispiel: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **TargetProcessSHA256**            | Optional     | SHA256     | Der SHA-256-Hash der Imagedatei des Zielprozesses.      <br><br>  Beispiel: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **TargetProcessSHA512**            | Optional     | SHA512     |   Der SHA-512-Hash der Imagedatei des Zielprozesses.       |
| **TargetProcessIMPHASH**           | Optional     | String     |    Der Importhash aller Bibliotheks-DLLs, die vom Zielprozess verwendet werden.    |
| <a name="targetprocesscommandline"></a> **TargetProcessCommandLine**       | Obligatorisch.    | String     | Die Befehlszeile zum Ausführen des Zielprozesses.   <br><br> Beispiel: `"choco.exe" -v`  |
| <a name="targetprocesscurrentdirectory"></a> **TargetProcessCurrentDirectory**       | Optional    | String     | Das aktuelle Verzeichnis, in dem der Zielprozess ausgeführt wird.  <br><br> Beispiel: `c:\windows\system32`  |
| **TargetProcessCreationTime**      | Obligatorisch.    | Datetime   |    Die Produktversion aus den Versionsinformationen der Imagedatei des Zielprozesses.   |
| **TargetProcessId**| Obligatorisch.    | String     |  Die Prozess-ID (PID) des Zielprozesses.     <br><br>Beispiel: `48610176`<br><br>**Hinweis**: Der Typ ist als *Zeichenfolge* definiert, um unterschiedliche Systeme zu unterstützen, aber unter Windows und Linux muss dieser Wert numerisch sein. <br><br>Wenn Sie einen Windows- oder Linux-Computer verwenden und einen anderen Typ verwendet haben, stellen Sie sicher, dass Sie die Werte konvertieren. Wenn Sie beispielsweise einen Hexadezimalwert verwendet haben, konvertieren Sie diesen in einen Dezimalwert.         |
| **TargetProcessGuid**              | Optional    | String     |Ein generierter eindeutiger Bezeichner (GUID) des Zielprozesses. Ermöglicht die systemübergreifende Identifizierung des Prozesses.   <br><br>  Beispiel: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`  |
| **TargetProcessIntegrityLevel**    | Optional    | String     |   Jeder Prozess verfügt über eine Integritätsebene, die in seinem Token dargestellt wird. Integritätsebenen bestimmen die Prozessebene des Schutzes oder Zugriffs. <br><br> Windows definiert die folgenden Integritätsebenen: **Niedrig**, **Mittel**, **Hoch** und **System**. Standardbenutzer erhalten eine **mittlere** Integritätsebene und erweiterte Benutzer eine **hohe** Integritätsebene. <br><br> Weitere Informationen finden Sie unter [OBligatorische Integritätskontrolle – Win32-Apps](/windows/win32/secauthz/mandatory-integrity-control). |
| **TargetProcessTokenElevation**    | Optional    | String     |Tokentyp, der das Vorhandensein oder Fehlen von UAC-Rechteerweiterungen (User Access Control) angibt, die auf den Prozess angewendet wurden, der erstellt oder beendet wurde.   <br><br>    Beispiel: `None`     |
| | | | |



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Normalisierung in Microsoft Sentinel](normalization.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung der Authentifizierung (Public Preview)](authentication-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum DNS-Normalisierungsschema](dns-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Dateiereignissen (Public Preview)](file-event-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Netzwerknormalisierungsschema](./network-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Registrierungsereignissen (Public Preview)](registry-event-normalization-schema.md)
