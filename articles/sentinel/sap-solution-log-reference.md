---
title: 'Microsoft Sentinel-Lösung für SAP: Referenz zu verfügbaren Protokollen | Microsoft-Dokumentation'
description: Erfahren Sie mehr über die SAP-Protokolle, die in der Microsoft Sentinel-Lösung für SAP verfügbar sind.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: 5d02b40794e226238896b7327a47d9cfb2acbe0e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517993"
---
# <a name="microsoft-sentinel-sap-solution-logs-reference-public-preview"></a>Referenz zu Protokollen der Microsoft Sentinel-Lösung für SAP (Public Preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden die SAP-Protokolle beschrieben, die über den Microsoft Sentinel-Datenconnector für SAP verfügbar sind, einschließlich der Tabellennamen in Microsoft Sentinel, der Protokollzwecke und detaillierten Protokollschemas. Die Beschreibungen der Schemafelder basieren auf den Feldbeschreibungen in der entsprechenden [SAP-Dokumentation](https://help.sap.com/).

Dieser Artikel richtet sich an fortgeschrittene SAP-Benutzer.

> [!NOTE]
> Bei Verwendung der XBP 3.0-Schnittstelle nutzt die Microsoft Sentinel-Lösung für SAP *nicht freigegebene* Dienste. Diese Dienste wirken sich nicht auf das Verhalten des Back-End-Systems oder Connectors aus.
>
> Um diese Dienste „freizugeben“, implementieren Sie den [SAP-Hinweis 2910263 – Nicht freigegebene XBP-Funktionen](https://launchpad.support.sap.com/#/notes/2910263).

> [!IMPORTANT]
> Die Microsoft Sentinel-Lösung für SAP befindet sich derzeit in der PREVIEW. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="abap-application-log"></a>ABAP-Anwendungsprotokoll

- **Name in Microsoft Sentinel**: `ABAPAppLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcc9f36611d3a6510000e835363f.html)

- **Protokollzweck:** Aufzeichnung des Status einer Anwendungsausführung, sodass Sie sie später bei Bedarf rekonstruieren können.

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst, der auf Standarddiensten der XBP-Schnittstelle basiert. Dieses Protokoll wird clientbezogen generiert.


### <a name="abapapplog_cl-log-schema"></a>Schema des Protokolls ABAPAppLog_CL

| Feld                 | BESCHREIBUNG                    |
| --------------------- | ------------------------------ |
| AppLogDateTime        | Datum/Uhrzeit des Anwendungsprotokolls      |
| CallbackProgram       | Rückrufprogramm               |
| CallbackRoutine       | Rückrufroutine               |
| CallbackType          | Rückruftyp                  |
| ClientID              | ABAP-Client-ID (MANDT)         |
| ContextDDIC           | DDIC-Struktur des Kontexts         |
| ExternalID            | Externe Protokoll-ID                |
| Host                  | Host                           |
| Instanz              | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>`              |
| InternalMessageSerial | Serielle Anwendungsprotokollmeldung |
| LevelofDetail         | Detailgrad                |
| LogHandle             | Handle für Anwendungsprotokoll         |
| LogNumber             | Protokollnummer                     |
| MessageClass          | Message-Klasse                  |
| MessageNumber         | Meldungsnummer                 |
| MessageText           | Meldungstext                   |
| MessageType           | Nachrichtentyp                   |
| Object                | Anwendungsprotokollobjekt         |
| OperationMode         | Betriebsmodus                 |
| ProblemClass          | Problemklasse                  |
| ProgramName           | Programmname                   |
| SortCriterion         | Sortierkriterium                 |
| StandardText          | Standardtext                  |
| SubObject             | Unterobjekt des Anwendungsprotokolls     |
| SystemID              | System-ID                      |
| SystemNumber          | Systemnummer                  |
| TransactionCode       | Transaktionscode               |
| Benutzer                  | Benutzer                           |
| UserChange            | Benutzeränderung                    |
| | |



## <a name="abap-change-documents-log"></a>Protokoll für ABAP-Änderungsdokumente

- **Name in Microsoft Sentinel**: `ABAPChangeDocsLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/6f51f5216c4b10149358d088a0b7029c/7.01.22/en-US/b8686150ed102f1ae10000000a44176f.html)

- **Protokollzweck**: Aufzeichnung folgender Elemente:

    - SAP NetWeaver Application Server (AS) ABAP-Protokolländerungen an Geschäftsdatenobjekten in Änderungsdokumenten

    - Andere Entitäten im SAP-System, z. B. Benutzerdaten, Rollen, Adressen

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst, der auf Standarddiensten basiert. Dieses Protokoll wird clientbezogen generiert.

### <a name="abapchangedocslog_cl-log-schema"></a>Schema des Protokolls ABAPChangeDocsLog_CL


| Feld                    | BESCHREIBUNG                 |
| ------------------------ | ---------------------------- |
| ActualChangeNum          | Tatsächliche Änderungsnummer         |
| ChangedTableKey          | Geänderter Tabellenschlüssel            |
| ChangeNumber             | Änderungsnummer                |
| ClientID                 | ABAP-Client-ID (MANDT)       |
| CreatedfromPlannedChange | Erstellt anhand geplanter Änderung in der folgenden Syntax: `(‘X’ , ‘ ‘)`|
| CurrencyKeyNew           | Währungsschlüssel: neuer Wert      |
| CurrencyKeyOld           | Währungsschlüssel: alter Wert      |
| FieldName                | Feldname                   |
| FlagText                 | Flagtext                    |
| Host                     | Host                         |
| Instanz                 | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>` |
| Sprache                 | Sprache                     |
| ObjectClass              | Objektklasse, z. B. `BELEG`, `BPAR`, `PFCG`, `IDENTITY` |
| ObjectID                 | Objekt-ID  |
| PlannedChangeNum         | Geplante Änderungsnummer  |
| SystemID                 | System-ID    |
| SystemNumber             | Systemnummer     |
| TableName                | Tabellenname        |
| TransactionCode          | Transaktionscode   |
| TypeofChange_Header      | Änderungstyp des Headers, einschließlich: <br>`U` = Ändern; `I` = Einfügen; `E` = Einzelnes Dokument löschen; `D` = Löschen; `J` = Einzelnes Dokument einfügen |
| TypeofChange_Item        | Änderungstyp des Elements, einschließlich: <br>`U` = Ändern; `I` = Einfügen; `E` = Einzelnes Dokument löschen; `D` = Löschen; `J` = Einzelnes Dokument einfügen |
| UOMNew                   | Maßeinheit: neuer Wert  |
| UOMOld                   | Maßeinheit: alter Wert |
| Benutzer                     | Benutzer  |
| ValueNew                 | Feldinhalt: neuer Wert |
| ValueOld                 | Feldinhalt: alter Wert |
| Version                  | Version          |
| | |

## <a name="abap-cr-log"></a>ABAP CR-Protokoll

- **Name in Microsoft Sentinel**: `ABAPCRLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd5f36611d3a6510000e835363f.html)

- **Protokollzweck**: enthält die CTS-Protokolle (Change & Transport System), einschließlich der Verzeichnisobjekte und Anpassungen, an denen Änderungen erfolgt sind.

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst, der auf Standardtabellen und -diensten basiert. Dieses Protokoll wird mit Daten aller Clients generiert.

> [!NOTE]
> Neben der Anwendungsprotokollierung, Änderungsdokumenten und Tabellenaufzeichnung werden alle Änderungen, die Sie mit dem Change & Transport System an Ihrem Produktivsystem vornehmen, in den CTS- und TMS-Protokollen dokumentiert.
>


### <a name="abapcrlog_cl-log-schema"></a>Schema des Protokolls ABAPCRLog_CL

| Feld        | Beschreibung                       |
| ------------ | --------------------------------- |
| Category     | Kategorie (Workbench, Anpassung) |
| ClientID     | ABAP-Client-ID (MANDT)            |
| BESCHREIBUNG  | BESCHREIBUNG                       |
| Host         | Host                              |
| Instanz     | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>` |
| ObjectName   | Objektname                       |
| ObjektType   | Objekttyp                       |
| Besitzer        | Besitzer                             |
| Anforderung      | Änderungsanforderung                    |
| Status       | Status                            |
| SystemID     | System-ID                         |
| SystemNumber | Systemnummer                     |
| TableKey     | Tabellenschlüssel                         |
| TableName    | Tabellenname                        |
| Ansichtsname     | Name der Ansicht                         |
| | |

## <a name="abap-db-table-data-log"></a>ABAP DB-Tabellendatenprotokoll

- **Name in Microsoft Sentinel**: `ABAPTableDataLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd2f36611d3a6510000e835363f.html)

- **Protokollzweck**: Bereitstellung einer Protokollierung für die Tabellen, die kritisch oder anfällig für Überwachungen sind.

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abaptabledatalog_cl-log-schema"></a>Schema des Protokolls ABAPTableDataLog_CL

| Feld            | BESCHREIBUNG                           |
| ---------------- | ------------------------------------- |
| DBLogID          | DB-Protokoll-ID                             |
| Host             | Host                                  |
| Instanz         | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>` |
| Sprache         | Sprache                              |
| LogKey           | Protokollschlüssel                               |
| NewValue         | Neuer Wert des Felds                       |
| OldValue         | Alter Wert des Felds                       |
| OperationTypeSQL | Vorgangstyp: `Insert`, `Update`, `Delete` |
| Programm          | Programmname                          |
| SystemID         | System-ID                             |
| SystemNumber     | Systemnummer                         |
| TableField       | Tabellenfeld                           |
| TableName        | Tabellenname                            |
| TransactionCode  | Transaktionscode                      |
| UserName         | Benutzer                                  |
| VersionNumber    | Versionsnummer                        |
| | |

## <a name="abap-gateway-log"></a>ABAP-Gatewayprotokoll

- **Name in Microsoft Sentinel**: `ABAPOS_GW_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/62b4de4187cb43668d15dac48fc00732/7.5.7/en-US/48b2a710ca1c3079e10000000a42189b.html)

- **Protokollzweck**: Überwachung von Gatewayaktivitäten. Verfügbar über SAP Control Web Service. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapos_gw_cl-log-schema"></a>Schema des Protokolls ABAPOS_GW_CL

| Feld        | BESCHREIBUNG      |
| ------------ | ---------------- |
| Host         | Host             |
| Instanz     | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Meldungstext     |
| severity     | Schweregrad der Meldung: `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | System-ID        |
| SystemNumber | Systemnummer    |
| | |

## <a name="abap-icm-log"></a>ABAP ICM-Protokoll

- **Name in Microsoft Sentinel**: `ABAPOS_ICM_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/683d6a1797a34730a6e005d1e8de6f22/7.52.4/en-US/a10ec40d01e740b58d0a5231736c434e.html)

- **Protokollzweck**: Aufzeichnung ein- und ausgehender Anforderungen und Erstellung von Statistiken zu HTTP-Anforderungen.

    Verfügbar über SAP Control Web Service. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapos_icm_cl-log-schema"></a>Schema des Protokolls ABAPOS_ICM_CL

| Feld        | BESCHREIBUNG      |
| ------------ | ---------------- |
| Host         | Host             |
| Instanz     | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Meldungstext     |
| severity     | Schweregrad der Meldung einschließlich: `Debug`, `Info`, `Warning`, `Error`   |
| SystemID     | System-ID        |
| SystemNumber | Systemnummer    |
| | |

## <a name="abap-job-log"></a>ABAP-Auftragsprotokoll

- **Name in Microsoft Sentinel**: `ABAPJobLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/b07e7195f03f438b8e7ed273099d74f3/7.31.19/en-US/4b2bc0974c594ba2e10000000a42189c.html)

- **Protokollzweck**: Kombination aller Protokolle von Aufträgen für die Verarbeitung im Hintergrund (SM37).

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst, der auf Standarddiensten von XBP-Schnittstellen basiert. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapjoblog_cl-log-schema"></a>Schema des Protokolls ABAPJobLog_CL


| Feld               | BESCHREIBUNG                      |
| ------------------- | -------------------------------- |
| ABAPProgram         | ABAP-Programm                     |
| BgdEventParameters  | Hintergrundereignisparameter      |
| BgdProcessingEvent  | Hintergrundverarbeitungsereignis      |
| ClientID            | ABAP-Client-ID (MANDT)           |
| DynproNumber        | Dynpro-Nummer                    |
| GUIStatus           | GUI-Status                       |
| Host                | Host                             |
| Instanz            | ABAP-Instanz (HOST_SYSID_SYSNR) in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>` |
| JobClassification   | Auftragsklassifizierung               |
| JobCount            | Auftragsanzahl                        |
| JobGroup            | Auftragsgruppe                        |
| JobName             | Auftragsname                         |
| JobPriority         | Auftragspriorität                     |
| MessageClass        | Message-Klasse                    |
| MessageNumber       | Meldungsnummer                   |
| MessageText         | Meldungstext                     |
| MessageType         | Nachrichtentyp                     |
| ReleaseUser         | Auftragsfreigabebenutzer                 |
| SchedulingDateTime  | Zeitplanung für Datum und Uhrzeit             |
| StartDateTime       | Startdatum und -uhrzeit                  |
| SystemID            | System-ID                        |
| SystemNumber        | Systemnummer                    |
| TargetServer        | Zielserver                    |
| Benutzer                | Benutzer                             |
| UserReleaseInstance | ABAP-Instanz – Benutzerfreigabe     |
| WorkProcessID       | Arbeitsprozess-ID                  |
| WorkProcessNumber   | Arbeitsprozessnummer              |
| | |

## <a name="abap-security-audit-log"></a>ABAP-Sicherheitsüberwachungsprotokoll

- **Name in Microsoft Sentinel**: `ABAPAuditLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/280f016edb8049e998237fcbd80558e7/7.5.7/en-US/4d41bec4aa601c86e10000000a42189b.html)

- **Protokollzweck**: Aufzeichnung der folgenden Daten:

    - Sicherheitsbezogene Änderungen an der SAP-Systemumgebung, z. B. Änderungen an Hauptbenutzerdatensätzen
    - Informationen, die eine höhere Datenebene bieten, z. B. erfolgreiche und nicht erfolgreiche Anmeldeversuche
    - Informationen, die die Wiederherstellung einer Reihe von Ereignissen ermöglichen, z. B. erfolgreiche oder nicht erfolgreiche Transaktionsstarts

    Verfügbar über XAL-/SAL-Schnittstellen von RFC. SAL ist ab Version Basis 7.50 verfügbar. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapauditlog_cl-log-schema"></a>Schema des Protokolls ABAPAuditLog_CL

| Feld                      | BESCHREIBUNG                     |
| -------------------------- | ------------------------------- |
| ABAPProgramName            | Programmname, nur SAL                    |
| AlertSeverity              | Schweregrad der Warnung                  |
| AlertSeverityText          | Text mit Warnungsschweregrad, nur SAL             |
| AlertValue                 | Warnungswert                     |
| AuditClassID               | Überwachungsklassen-ID, nur SAL                 |
| ClientID                   | ABAP-Client-ID (MANDT)          |
| Computer                   | Benutzercomputer, nur SAL                   |
| E-Mail                      | User email                      |
| Host                       | Host                                                   |
| Instanz                   | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>`                  |
| MessageClass               | Message-Klasse                   |
| MessageContainerID         | Nachrichtencontainer-ID, nur XAL            |
| Meldungs-ID                  | Meldungs-ID, z. B. `‘AU1’,’AU2’…`                     |
| MessageText                | Meldungstext                    |
| MonitoringObjectName       | Objektname des MTE-Monitors, nur XAL        |
| MonitorShortName           | Kurzname des MTE-Monitors, nur XAL          |
| SAPProcesType              | Systemprotokoll: SAP-Prozesstyp, nur SAL    |
| B* – Hintergrundverarbeitung |                                 |
| D* – Dialogverarbeitung     |                                 |
| U* – Aktualisierungsaufgaben         |                                 |
| SAPWPName                  | Systemprotokoll: Arbeitsprozessnummer, nur SAL |
| SystemID                   | System-ID                       |
| SystemNumber               | Systemnummer                   |
| TerminalIPv6               | IP-Adresse des Benutzercomputers, nur SAL |
| TransactionCode            | Transaktionscode, nur SAL |
| Benutzer                       | Benutzer                            |
| Variable1                  | Meldungsvariable 1              |
| Variable2                  | Meldungsvariable 2              |
| Variable3                  | Meldungsvariable 3              |
| Variable4                  | Meldungsvariable 4              |
| | |

## <a name="abap-spool-log"></a>ABAP-Spoolprotokoll

- **Name in Microsoft Sentinel**: `ABAPSpoolLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae791c40f72045e10000000a421937.html)

- **Protokollzweck**: Hauptprotokoll für SAP-Druck mit Verlauf der Spoolanforderungen. (SP01).

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst, der auf Standardtabellen basiert. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapspoollog_cl-log-schema"></a>Schema des Protokolls ABAPSpoolLog_CL

| Feld                               | BESCHREIBUNG                                |
| ----------------------------------- | ------------------------------------------ |
| ArchiveStatus                       | Archivstatus                             |
| ArchiveType                         | Archivtyp                               |
| ArchivingDevice                     | Archivierungsgerät                           |
| AutoRereoute                        | Automatisches Umleiten                              |
| ClientID                            | ABAP-Client-ID (MANDT)                     |
| CountryKey                          | Länderschlüssel                                |
| DeleteSpoolRequestAuto              | Automatisches Löschen der Spoolanforderung                  |
| DelFlag                             | Löschflag                              |
| Department                          | Department                                 |
| DocumentType                        | Dokumenttyp                              |
| ExternalMode                        | Externer Modus                              |
| FormatType                          | Formattyp                                |
| Host                                | Host                                       |
| Instanz                            | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>` |
| NumofCopies                         | Anzahl                           |
| OutputDevice                        | Ausgabegerät                              |
| PrinterLongName                     | Langname des Druckers                          |
| PrintImmediately                    | Sofort drucken                          |
| PrintOSCoverPage                    | OSCover-Seite drucken                         |
| PrintOSCoverPage                   | SAPCover-Seite drucken                        |
| Priority                            | Priority                                   |
| RecipientofSpoolRequest             | Empfänger der Spoolanforderung                 |
| SpoolErrorStatus                    | Spoolfehlerstatus                         |
| SpoolRequestCompleted               | Spoolanforderung abgeschlossen                    |
| SpoolRequestisALogForAnotherRequest | Spoolanforderung ist ein Protokoll für eine andere Anforderung |
| SpoolRequestName                    | Name der Spoolanforderung                         |
| SpoolRequestNumber                  | Nummer der Spoolanforderung                       |
| SpoolRequestSuffix1                 | Spoolanforderungssuffix 1                      |
| SpoolRequestSuffix2                 | Spoolanforderungssuffix 2                      |
| SpoolRequestTitle                   | Titel der Spoolanforderung                        |
| SystemID                            | System-ID                                  |
| SystemNumber                        | Systemnummer                              |
| TelecommunicationsPartner           | Telekommunikationspartner                 |
| TelecommunicationsPartnerE          | Telekommunikationspartner E               |
| TemSeGeneralcounter                 | TemSe-Zähler                              |
| TemseNumAddProtectionRule           | TemSe-Nummer: Schutzregel hinzufügen              |
| TemseNumChangeProtectionRule        | TemSe-Nummer: Schutzregel ändern           |
| TemseNumDeleteProtectionRule        | TemSe-Nummer: Schutzregel löschen           |
| TemSeObjectName                     | TemSe-Objektname                          |
| TemSeObjectPart                     | TemSe-Objektteil                          |
| TemseReadProtectionRule             | TemSe-Leseschutzregel                 |
| Benutzer                                | Benutzer                                       |
| ValueAuthCheck                      | Überprüfung der Wertauthentifizierung                           |
| | |

## <a name="apab-spool-output-log"></a>APAB-Spoolausgabeprotokoll

- **Name in Microsoft Sentinel**: `ABAPSpoolOutputLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae779e40f72045e10000000a421937.html)

- **Protokollzweck**: Hauptprotokoll für SAP-Druck mit Verlauf der Spoolausgabeanforderungen. (SP02).

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst, der auf Standardtabellen basiert. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapspooloutputlog_cl-log-schema"></a>Schema des Protokolls ABAPSpoolOutputLog_CL

| Feld                              | BESCHREIBUNG                               |
| ---------------------------------- | ----------------------------------------- |
| AppServer                          | Anwendungsserver                        |
| ClientID                           | ABAP-Client-ID (MANDT)                    |
| Kommentar                            | Kommentar                                   |
| CopyCount                          | Anzahl der Exemplare                                |
| CopyCounter                        | Zähler für Exemplare                              |
| Department                         | Department                                |
| ErrorSpoolRequestNumber            | Nummer der fehlerhaften Anforderung                      |
| FormatType                         | Formattyp                               |
| Host                               | Host                                      |
| HostName                           | Hostname                                 |
| HostSpoolerID                      | Hostspooler-ID                          |
| Instanz                           | ABAP-Instanz                             |
| LastPage                           | Letzte Seite                                 |
| NumofCopies                        | Anzahl                              |
| OutputDevice                       | Ausgabegerät                             |
| OutputRequestNumber                | Ausgabeanforderungsnummer                     |
| OutputRequestStatus                | Ausgabeanforderungsstatus                     |
| PhysicalFormatType                 | Physischer Formattyp                      |
| PrinterLongName                    | Langname des Druckers                         |
| PrintRequestSize                   | Druckanforderungsgröße                        |
| Priority                           | Priority                                  |
| ReasonforOutputRequest             | Grund für Ausgabeanforderung                 |
| RecipientofSpoolRequest            | Empfänger der Spoolanforderung                 |
| SpoolNumberofOutputReqProcessed    | Anzahl der Ausgabeanforderungen – verarbeitet     |
| SpoolNumberofOutputReqWithErrors   | Anzahl der Ausgabeanforderungen – mit Fehlern   |
| SpoolNumberofOutputReqWithProblems | Anzahl der Ausgabeanforderungen – mit Problemen |
| SpoolRequestNumber                 | Nummer der Spoolanforderung                      |
| StartPage                          | Startseite                                |
| SystemID                           | System-ID                                 |
| SystemNumber                       | Systemnummer                             |
| TelecommunicationsPartner          | Telekommunikationspartner                |
| TemSeGeneralcounter                | TemSe-Zähler                             |
| Titel                              | Titel                                     |
| Benutzer                               | Benutzer                                      |
| | |


## <a name="abap-syslog"></a>ABAP SysLog

- **Name in Microsoft Sentinel**: `ABAPOS_Syslog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcbaf36611d3a6510000e835363f.html)

- **Protokollzweck**: Aufzeichnung aller ABAP-Systemfehler von SAP NetWeaver Application Server (SAP NetWeaver AS), Warnungen, Benutzersperren aufgrund fehlgeschlagener Anmeldeversuche bekannter Benutzer und Prozessmeldungen.

    Verfügbar über SAP Control Web Service. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapos_syslog_cl-log-schema"></a>Schema des Protokolls ABAPOS_Syslog_CL


| Feld            | BESCHREIBUNG            |
| ---------------- | ---------------------- |
| ClientID         | ABAP-Client-ID (MANDT) |
| Host             | Host                   |
| Instanz         | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR> ` |
| MessageNumber    | Meldungsnummer         |
| MessageText      | Meldungstext           |
| severity         | Meldungsschweregrad, einer der folgenden Werte: `Debug`, `Info`, `Warning`, `Error`        |
| SystemID         | System-ID              |
| SystemNumber     | Systemnummer          |
| TransacationCode | Transaktionscode       |
| Typ             | SAP-Prozesstyp       |
| Benutzer             | Benutzer                   |
| | |


## <a name="abap-workflow-log"></a>ABAP-Workflowprotokoll

- **Name in Microsoft Sentinel**: `ABAPWorkflowLog_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcccf36611d3a6510000e835363f.html)

- **Protokollzweck**: Mit dem SAP Business Workflow (WebFlow-Engine) können Sie Geschäftsprozesse definieren, die noch nicht im SAP-System abgebildet sind.

    Bei nicht abgebildeten Geschäftsprozessen kann es sich z. B. um einfache Freigabe- oder Genehmigungsprozesse handeln oder um komplexere Geschäftsprozesse wie die Erstellung von Basismaterial und die anschließende Koordination der zugehörigen Abteilungen.

    Verfügbar unter Verwendung von RFC mit einem benutzerdefinierten Dienst, der auf Standardtabellen und -diensten basiert. Dieses Protokoll wird clientbezogen generiert.

### <a name="abapworkflowlog_cl-log-schema"></a>Schema des Protokolls ABAPWorkflowLog_CL


| Feld               | BESCHREIBUNG                      |
| ------------------- | -------------------------------- |
| ActualAgent         | Tatsächlicher Agent                     |
| Adresse             | Adresse                          |
| ApplicationArea     | Anwendungsbereich                 |
| CallbackFunction    | Rückruffunktion                |
| ClientID            | ABAP-Client-ID (MANDT)           |
| CreationDateTime    | Erstellungsdatum/-uhrzeit               |
| Creator             | Creator                          |
| CreatorAddress      | Adresse des Erstellers                  |
| ErrorType           | Fehlertyp                       |
| ExceptionforMethod  | Ausnahme für Methode             |
| Host                | Host                             |
| Instanz            | ABAP-Instanz (HOST_SYSID_SYSNR) in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>` |
| Sprache            | Sprache                         |
| LogCounter          | Protokollzähler                      |
| MessageNumber       | Meldungsnummer                   |
| MessageType         | Nachrichtentyp                     |
| MethodUser          | Methodenbenutzer                      |
| Priority            | Priority                         |
| SimpleContainer     | Einfacher Container, gepackt als Liste mit Schlüssel-Wert-Entitäten für das Arbeitselement |
| Status              | Status                           |
| SuperWI             | Superarbeitselement                         |
| SystemID            | System-ID                        |
| SystemNumber        | Systemnummer                    |
| TaskID              | Aufgaben-ID                          |
| TasksClassification | Aufgabenklassifizierungen            |
| TaskText            | Aufgabentext                        |
| TopTaskID           | ID der wichtigsten Aufgabe                      |
| UserCreated         | Vom Benutzer erstellt                     |
| WIText              | Arbeitselementtext                   |
| WIType              | Arbeitsaufgabentyp                   |
| WorkflowAction      | Workflow-Aktion                  |
| WorkItemID          | ID des Arbeitselements                     |
| | |





## <a name="abap-workprocess-log"></a>ABAP WorkProcess-Protokoll

- **Name in Microsoft Sentinel**: `ABAPOS_WP_CL`

- **Zugehörige SAP-Dokumentation:** [SAP-Hilfeportal](https://help.sap.com/viewer/d0739d980ecf42ae9f3b4c19e21a4b6e/7.3.15/en-US/46fb763b6d4c5515e10000000a1553f6.html)

- **Protokollzweck**: Kombination aller Arbeitsprozessprotokolle. (Standard: `dev_*`).

    Verfügbar über SAP Control Web Service. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="abapos_wp_cl-log-schema"></a>Schema des Protokolls ABAPOS_WP_CL


| Feld        | BESCHREIBUNG         |
| ------------ | ------------------- |
| Host         | Host                |
| Instanz     | ABAP-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | Meldungstext     |
| severity     | Schweregrad der Meldung: `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | System-ID           |
| SystemNumber | Systemnummer       |
| WPNumber     | Arbeitsprozessnummer |
| | |


## <a name="hana-db-audit-trail"></a>HANA DB-Überwachungsprotokoll

- **Name in Microsoft Sentinel**: `Syslog`

- **Zugehörige SAP-Dokumentation**: [Allgemein](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/48fd6586304c4f859bf92d64d0cd8b08.html) |  [Überwachungsprotokoll](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.03/en-US/0a57444d217649bf94a19c0b68b470cc.html)

- **Protokollzweck** Aufzeichnung von Benutzer- oder versuchten Aktionen in der SAP HANA-Datenbank. Ermöglicht Ihnen beispielsweise, den Lesezugriff auf vertrauliche Daten zu protokollieren und zu überwachen.

    Verfügbar über den Sentinel Linux-Agent für Syslog. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="syslog-log-schema"></a>Schema des Protokolls Syslog

| Feld         | BESCHREIBUNG  |
| ------------- | ------------ |
| Computer      | Hostname    |
| HostIP        | Host-IP      |
| HostName      | Hostname    |
| ProcessID     | Prozess-ID   |
| ProcessName   | Prozessname: `HDB*` |
| SeverityLevel | Warnung        |
| SourceSystem  |   Betriebssystem des Quellsystems: `Linux`           |
| SyslogMessage | Meldung, eine nicht analysierte Überwachungsprotokollmeldung      |
| | |

## <a name="java-files"></a>JAVA-Dateien

- **Name in Microsoft Sentinel**: `JavaFilesLogsCL`

- **Zugehörige SAP-Dokumentation**: [Allgemein](https://help.sap.com/viewer/2f8b1599655d4544a3d9c6d1a9b6546b/7.5.9/en-US/485059dfe31672d4e10000000a42189c.html) | [Java-Sicherheitsüberwachungsprotokoll](https://help.sap.com/viewer/1531c8a1792f45ab95a4c49ba16dc50b/7.5.9/en-US/4b6013583840584ae10000000a42189c.html)

- **Protokollzweck**: Kombination aller auf Java-Dateien basierenden Protokolle, einschließlich des Sicherheitsüberwachungsprotokolls und der System- (Cluster- und Serverprozess), Leistungs- und Gatewayprotokolle. Enthält auch Entwicklerablaufverfolgungs- und Standardablaufverfolgungs-Protokolle.

    Verfügbar über SAP Control Web Service. Dieses Protokoll wird mit Daten aller Clients generiert.

### <a name="javafileslogscl-log-schema"></a>Schema des Protokolls JavaFilesLogsCL


| Feld            | Beschreibung          |
| ---------------- | -------------------- |
| Anwendung      | Java-Anwendung     |
| ClientID         | Client-ID           |
| CSNComponent     | CSN-Komponente, z. B. `BC-XI-IBD` |
| DCComponent      | DC-Komponente, z. B. `com.sap.xi.util.misc` |
| DSRCounter       | DSR-Zähler          |
| DSRRootContentID | DSR-Kontext-GUID     |
| DSRTransaction   | DSR-Transaktions-GUID |
| Host             | Host                 |
| Instanz         | Java-Instanz in der folgenden Syntax: `<HOST>_<SYSID>_<SYSNR>` |
| Standort         | Java-Klasse           |
| LogName          | Java-Protokollname, z. B.: `Available`, `defaulttrace`, `dev*`, `security` usw.
| MessageText      | Meldungstext         |
| MNo              | Meldungsnummer       |
| Pid              | Prozess-ID           |
| Programm          | Programmname         |
| Sitzung          | Sitzung              |
| severity         | Schweregrad der Meldung einschließlich: `Debug`, `Info`, `Warning`, `Error`     |
| Lösung         | Lösung             |
| SystemID         | System-ID            |
| SystemNumber     | Systemnummer        |
| ThreadName       | Threadname          |
| Thrown           | Ausgelöste Ausnahme     |
| TimeZone         | Zeitzone             |
| Benutzer             | Benutzer                 |
| | |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Bereitstellen der Microsoft Sentinel-Lösung für SAP](sap-deploy-solution.md)
- [Detaillierte SAP-Anforderungen für die Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-detailed-requirements.md)
- [Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md)
- [Konfigurationsoptionen für Experten, lokale Bereitstellung und SAPControl-Protokollquellen](sap-solution-deploy-alternate.md)
- [Microsoft Sentinel-Lösung für SAP: integrierte Sicherheitsinhalte](sap-solution-security-content.md)
- [Problembehandlung bei der Bereitstellung der Microsoft Sentinel-Lösung für SAP](sap-deploy-troubleshoot.md)
