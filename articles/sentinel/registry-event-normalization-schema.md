---
title: Referenz zum Normalisierungsschema für Registrierungsereignisse in Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird das Normalisierungsschema für Registrierungsereignisse in Microsoft Sentinel beschrieben.
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
ms.openlocfilehash: 4a22c8b2f207d8d6d2019f417bfcb025dcca9fa1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523921"
---
# <a name="microsoft-sentinel-registry-event-normalization-schema-reference-public-preview"></a>Referenz zum Normalisierungsschema für Registrierungsereignisse in Microsoft Sentinel (Public Preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das Registrierungsereignisschema wird verwendet, um die Windows-Aktivität zum Erstellen, Ändern oder Löschen von Windows-Registrierungsentitäten zu beschreiben.

Registrierungsereignisse sind spezifisch für Windows-Systeme, werden aber von verschiedenen Systemen gemeldet, die Windows überwachen, z. B. EDR-Systeme (Endpunkterkennung und -antwort), Sysmon oder Windows selbst.

Weitere Informationen zur Normalisierung in Microsoft Sentinel finden Sie unter [Normalisierung und erweitertes SIEM-Informationsmodell (ADVANCED SIEM Information Model, ASIM).](normalization.md)

> [!IMPORTANT]
> Das Schema für die Normalisierung von Registrierungsereignissen befindet sich derzeit in der VORSCHAU. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
>
> In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="parsers"></a>Parser

Microsoft Sentinel bietet die folgenden integrierten produktspezifischen Registrierungsereignisparser:

- **Registrierungsupdate für sicherheitsrelevante Ereignisse (Ereignis 4657)** , erfasst mit dem Log Analytics-Agent oder mit Azure Monitor
- **Sysmon-Registrierungsüberwachungsereignisse (Ereignisse 12, 13 und 14)** , die mit dem Log Analytics-Agent oder dem Azure Monitor-Agent erfasst wurden
- **Registrierungsereignisse aus Microsoft 365 Defender für Endpunkt**

Um den quellunabhängigen Parser zu verwenden, der alle integrierten Parser vereinheitlicht, und um sicherzustellen, dass Ihre Analyse über alle konfigurierten Quellen hinweg ausgeführt wird, verwenden Sie **imRegistry** als Tabellennamen in der Abfrage.

Stellen Sie die [quellenunabhängigen und quellenspezifischen Parser](normalization-about-parsers.md) aus dem [Microsoft Sentinel GitHub-Repository](https://aka.ms/AzSentinelRegistry) bereit.

### <a name="add-your-own-normalized-parsers"></a>Hinzufügen eigener normalisierter Parser

Wenn Sie benutzerdefinierte Parser für das Registrierungsereignis-Informationsmodell implementieren, benennen Sie Ihre KQL-Funktionen mit der folgenden Syntax: `imRegistry<vendor><Product>`.

Fügen Sie Ihre KQL-Funktion den quellunabhängigen `imRegistry`-Parsern hinzu, um sicherzustellen, dass alle Inhalte, die das Registrierungsereignismodell verwenden, auch Ihren neuen Parser verwenden.

## <a name="normalized-content"></a>Normalisierter Inhalt

Microsoft Sentinel stellt die Huntingabfrage [Persisting Via IFEO Registry Key](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml) (Beibehalten über IFEO-Registrierungsschlüssel) bereit. Diese Abfrage funktioniert mit allen Registrierungsaktivitätsdaten, die mit dem erweiterten SIEM-Informationsmodell normalisiert wurden.

Weitere Informationen finden Sie unter [Suchen nach Bedrohungen mit Microsoft Sentinel](hunting.md).

## <a name="schema-details"></a>Schemadetails

Das Registrierungsereignis-Informationsmodell ist am [OSSEM-Registrierungsentitätsschema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/registry.md) ausgerichtet.

### <a name="log-analytics-fields"></a>Log Analytics-Felder


Die folgenden Felder werden von Log Analytics für jeden Datensatz generiert und können beim Erstellen eines benutzerdefinierten Connectors überschrieben werden.

| Feld         | type     | Diskussion (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde.|
| **_ResourceId**   | guid     | Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden. |
| **Type** | String | Die ursprüngliche Tabelle, aus der der Datensatz abgerufen wurde. Dieses Feld ist nützlich, wenn ein und dasselbe Ereignis über mehrere Kanäle in verschiedenen Tabellen empfangen werden kann und die gleichen EventVendor- und EventProduct-Werte hat.<br><br>Ein Sysmon-Ereignis kann z. B. entweder in der Tabelle Event oder in der Tabelle WindowsEvent gesammelt werden. |


> [!NOTE]
> Log Analytics fügt auch andere Felder hinzu, die für Sicherheitsanwendungsfälle weniger relevant sind. Weitere Informationen finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Ereignisfelder


Ereignisfelder sind allen Schemas gemeinsam und beschreiben die Aktivität selbst und das meldende Gerät.

| Feld               | Klasse       | type       |  BESCHREIBUNG        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Optional    | String     |     Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert.   |
| **EventCount**          | Obligatorisch.   | Integer    |     Die Anzahl der Ereignisse, die vom Datensatz beschrieben werden. <br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen. <br><br>Legen Sie den Wert für andere Quellen auf `1` fest.   |
| **EventStartTime**      | Obligatorisch.   | Datum/Uhrzeit  |      Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, gibt dieses Feld die Zeit an, zu der das erste Ereignis generiert wurde. <br><br>Andernfalls wird in diesem Feld ein Alias für das Feld [TimeGenerated](#timegenerated) verwendet. |
| **EventEndTime**        | Obligatorisch.   | Alias      |      Alias für das Feld [TimeGenerated](#timegenerated).    |
| **EventType**           | Obligatorisch.   | Enumerated |    Beschreibt den vom Datensatz gemeldeten Vorgang. <br><br>Für Registrierungsdatensätze werden folgende Werte unterstützt: <br>- `RegistryKeyCreated` <br>- `RegistryKeyDeleted`<br>- `RegistryKeyRenamed` <br>- `RegistryValueDeleted` <br>- `RegistryValueSet`|
| **EventOriginalUid**    | Optional    | String     |   Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Optional    | String     |   Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `4657`|
| <a name ="eventproduct"></a>**EventProduct**        | Obligatorisch.   | String     |             Das Produkt, das das Ereignis erzeugt. <br><br>Beispiel: `Sysmon`<br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.           |
| **EventProductVersion** | Optional    | String     | Die Version des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `12.1`      |
| **EventVendor**         | Obligatorisch.   | String     |           Der Hersteller des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `Microsoft`  <br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.  |
| **EventSchemaVersion**  | Obligatorisch.   | String     |    Die Version des Schemas. Die hier dokumentierte Version des Schemas ist `0.1`.         |
| **EventReportUrl**      | Optional    | String     | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die zusätzliche Informationen zum Ereignis enthält.|
| **Dvc** | Obligatorisch.       | String     |               Ein eindeutiger Bezeichner des Geräts, auf dem das Ereignis aufgetreten ist. <br><br>Dieses Feld kann ein Alias für die Felder [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die es kein offensichtliches Gerät gibt, denselben Wert wie das Feld [EventProduct](#eventproduct).         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Empfohlen | IP-Adresse |         Die IP-Adresse des Geräts, auf dem das Registrierungsereignis aufgetreten ist.  <br><br>Beispiel: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Empfohlen | Hostname   |               Der Hostname des Geräts, auf dem das Registrierungsereignis aufgetreten ist. <br><br>Beispiel: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Optional    | String     |  Die eindeutige ID des Geräts, auf dem das Registrierungsereignis aufgetreten ist. <br><br>Beispiel: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Optional    | MAC        |   Die MAC-Adresse des Geräts, auf dem das Registrierungsereignis aufgetreten ist.  <br><br>Beispiel: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Optional    | String     |         Das Betriebssystem, das auf dem Gerät ausgeführt wird, auf dem das Registrierungsereignis aufgetreten ist.    <br><br>Beispiel: `Windows`    |
| **DvcOsVersion**        | Optional    | String     |   Die Version des Betriebssystems auf dem Gerät, auf dem das Registrierungsereignis aufgetreten ist. <br><br>Beispiel: `10` |
| **AdditionalFields**    | Optional    | Dynamisch    | Wenn Ihre Quelle zusätzliche Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie die zusätzlichen Informationen als Schlüssel-Wert-Paare hinzu.    |


### <a name="registry-event-specific-fields"></a>Registrierungsereignisspezifische Felder

Die in der folgenden Tabelle aufgeführten Felder sind spezifisch für Registrierungsereignisse. Sie ähneln jedoch Feldern in anderen Schemas und folgen ähnlichen Benennungskonventionen.

Weitere Informationen finden Sie unter [Struktur der Registrierung](/windows/win32/sysinfo/structure-of-the-registry) in der Windows-Dokumentation.

| Feld          | Klasse        | type       | BESCHREIBUNG   |
|---------------|--------------|------------|-----------------|
|<a name="registrykey"></a>**RegistryKey**     |     Obligatorisch.    |   String      |Der Registrierungsschlüssel, der dem Vorgang zugeordnet und auf Standardnamenskonventionen für Stammschlüssel normalisiert ist. Weitere Informationen finden Sie unter [Stammschlüssel](#root-keys).<br><br>Registrierungsschlüssel ähneln Ordnern in Dateisystemen. <br><br>Beispiel: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`        |
|**RegistryValue**     |    Empfohlen     |  String       |Der dem Vorgang zugeordnete Registrierungswert. Registrierungswerte ähneln Dateien in Dateisystemen. <br><br>Beispiel: `Path`        |
|<a name="registryvaluetype"></a>**RegistryValueType**     |    Empfohlen     |    String     | Der Typ des Registrierungswerts, normalisiert in Standardform. Weitere Informationen finden Sie unter [Werttypen](#value-types).<br><br>Beispiel: `Reg_Expand_Sz`        |
|**RegistryValueData**     | Empfohlen       |      String   |  Die im Registrierungswert gespeicherten Daten. <br><br>Beispiel: `C:\Windows\system32;C:\Windows;`       |
|<a name="registrypreviouskey"></a>**RegistryPreviousKey**     |  Empfohlen       |   String      |  Bei Vorgängen, die die Registrierung ändern, der ursprüngliche Registrierungsschlüssel, normalisiert in die Standardbenennung für Stammschlüssel. Weitere Informationen finden Sie unter [Stammschlüssel](#root-keys). <br><br>**Hinweis:** Wenn der Vorgang andere Felder geändert hat, z. B. den Wert, der Schlüssel aber gleich bleibt, hat [RegistryPreviousKey](#registrypreviouskey) den gleichen Wert wie [RegistryKey](#registrykey).<br><br>Beispiel: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`       |
|<a name="registrypreviousvalue"></a>**RegistryPreviousValue**     | Empfohlen        | String        | Bei Vorgängen, die die Registrierung ändern, der ursprüngliche Werttyp, normalisiert in die Standardform. Weitere Informationen finden Sie unter [Werttypen](#value-types). <br><br>Wenn der Typ nicht geändert wurde, weist dieses Feld den gleichen Wert wie das Feld [RegistryValueType](#registryvaluetype) auf.  <br><br>Beispiel: `Path`       |
|**RegistryPreviousValueType**     | Empfohlen        |   String      |Bei Vorgängen, die die Registrierung ändern, der ursprüngliche Werttyp. <br><br>Wenn der Typ nicht geändert wurde, weist dieses Feld den gleichen Wert wie das Feld [RegistryValueType](#registryvaluetype) auf, normalisiert in die Standardform. Weitere Informationen finden Sie unter [Werttypen](#value-types).<br><br>Beispiel: `Reg_Expand_Sz`         |
|**RegistryPreviousValueData**     | Empfohlen        |   String      |Bei Vorgängen, die die Registrierung ändern, die ursprünglichen Registrierungsdaten. <br><br>Beispiel: `C:\Windows\system32;C:\Windows;`         |
|**Benutzer** | Alias | |Alias für das Feld [ActorUsername](#actorusername). <br><br>Beispiel: `CONTOSO\ dadmin` |
|**Process**     |  Alias       |         |  Alias für das [Feld ActingProcessName](#actingprocessname).<br><br>Beispiel: `C:\Windows\System32\rundll32.exe`       |
| <a name="actorusername"></a>**ActorUsername**  | Obligatorisch.    | String     | Der Benutzername des Benutzers, der das Ereignis initiiert hat. <br><br>Beispiel: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Obligatorisch.    | Enumerated |   Gibt den Typ des Benutzernamens an, der im Feld [ActorUsername](#actorusername) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity). <br><br>Beispiel: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | Empfohlen  | String     |   Eine eindeutige ID des Akteurs. Die jeweilige ID hängt vom System ab, das das Ereignis generiert. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).  <br><br>Beispiel: `S-1-5-18`    |
| **ActorUserIdType**| Empfohlen  | String     |  Der Typ der ID, die im Feld [ActorUserId](#actoruserid) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity). <br><br>Beispiel: `SID`         |
| **ActorSessionId** | Optional     | String     |   Die eindeutige ID der Anmeldesitzung des Akteurs.  <br><br>Beispiel: `999`<br><br>**Hinweis**: Der Typ ist als *Zeichenfolge* definiert, um unterschiedliche Systeme zu unterstützen, aber unter Windows muss dieser Wert numerisch sein. Wenn Sie einen Windows-Computer verwenden und die Quelle einen anderen Typ sendet, stellen Sie sicher, dass Sie den Wert konvertieren. Wenn die Quelle beispielsweise einen Hexadezimalwert verwendet haben, konvertieren Sie diesen in einen Dezimalwert.   |
| <a name="actingprocessname"></a>**ActingProcessName**              | Optional     | String     |   Der Dateiname der Imagedatei des agierenden Prozesses. Dieser Name wird in der Regel als Prozessname betrachtet.  <br><br>Beispiel: `C:\Windows\explorer.exe`  |
| **ActingProcessId**| Obligatorisch.    | String        | Die Prozess-ID (PID) des handelnden Prozesses.<br><br>Beispiel: `48610176`           <br><br>**Hinweis**: Der Typ ist als *Zeichenfolge* definiert, um unterschiedliche Systeme zu unterstützen, aber unter Windows und Linux muss dieser Wert numerisch sein. <br><br>Wenn Sie einen Windows- oder Linux-Computer verwenden und einen anderen Typ verwendet haben, stellen Sie sicher, dass Sie die Werte konvertieren. Wenn Sie beispielsweise einen Hexadezimalwert verwendet haben, konvertieren Sie diesen in einen Dezimalwert.    |
| **ActingProcessGuid**              | Optional     | String     |  Ein generierter eindeutiger Bezeichner (GUID) des agierenden Prozesses.   <br><br> Beispiel: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ParentProcessName**              | Optional     | String     |  Der Dateiname der Imagedatei des übergeordneten Prozesses. Dieser Wert wird in der Regel als Prozessname betrachtet.    <br><br>Beispiel: `C:\Windows\explorer.exe` |
| **ParentProcessId**| Obligatorisch.    | String    | Die Prozess-ID (PID) des übergeordneten Prozesses.   <br><br>     Beispiel: `48610176`    |
| **ParentProcessGuid**              | Optional     | String     |  Ein generierter eindeutiger Bezeichner (GUID) des übergeordneten Prozesses.     <br><br> Beispiel: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |



### <a name="root-keys"></a>Stammschlüssel

Verschiedene Quellen stellen Registrierungsschlüsselpräfixe mit unterschiedlichen Darstellungen dar. Verwenden Sie für die Felder [RegistryKey](#registrykey) und [RegistryPreviousKey](#registrypreviouskey) die folgenden normalisierten Präfixe:

|Normalisiertes Schlüsselpräfix  |Andere gängige Darstellungen  |
|---------|---------|
|**HKEY_LOCAL_MACHINE**     | `HKLM`, `\REGISTRY\MACHINE`        |
|**HKEY_USERS**     | `HKU`, `\REGISTRY\USER`        |


### <a name="value-types"></a>Werttypen

Verschiedene Quellen stellen Registrierungswerttypen mit unterschiedlichen Darstellungen dar. Verwenden Sie für die Felder [RegistryValueType](#registryvaluetype) und [RegistryPreviousValueType](#registrypreviousvalue) die folgenden normalisierten Typen:


|Normalisiertes Schlüsselpräfix  |Andere gängige Darstellungen  |
|---------|---------|
|  **Reg_None**   |    `None`, `%%1872`     |
|  **Reg_Sz**   |     `String`, `%%1873`    |
| **Reg_Expand_Sz**    | `ExpandString`, `%%1874`        |
|  **Reg_Binary**   |   `Binary`, `%%1875`      |
| **Reg_DWord**    |    `Dword`, `%%1876`     |
|  **Reg_Multi_Sz**   |  `MultiString`, `%%1879`       |
|  **Reg_QWord**   |    `Qword`, `%%1883`     |



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Normalisierung in Microsoft Sentinel](normalization.md)
- [Referenz zum Normalisierungsschema für die Authentifizierung in Microsoft Sentinel (Public Preview)](authentication-normalization-schema.md)
- [Referenz zum DNS-Normalisierungsschema von Microsoft Sentinel](dns-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Dateiereignissen (Public Preview)](file-event-normalization-schema.md)
- [Referenz zum Microsoft Sentinel-Netzwerk-Normalisierungsschema](./network-normalization-schema.md)
