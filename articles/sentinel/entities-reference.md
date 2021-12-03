---
title: Referenz zu Microsoft Sentinel-Entitätstypen | Microsoft-Dokumentation
description: In diesem Artikel werden die Microsoft Sentinel-Entitätstypen und ihre erforderlichen Bezeichner vorgestellt.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c5917011bd937e88811c9f0cffaed7f8c1c61f00
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521204"
---
# <a name="microsoft-sentinel-entity-types-reference"></a>Referenz zu Microsoft Sentinel-Entitätstypen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="entity-types-and-identifiers"></a>Entitätstypen und Bezeichner

Die folgende Tabelle zeigt die **Entitätstypen**, die derzeit für die Zuordnung in Microsoft Sentinel verfügbar sind, und die **Attribute**, die als **Bezeichner** für jeden Entitätstyp verfügbar sind. Diese werden in der Dropdownliste **Bezeichner** im Abschnitt [Entitätszuordnung](map-data-fields-to-entities.md) des [Analyseregel-Assistenten](detect-threats-custom.md) angezeigt.

Jeder der Bezeichner in der Spalte **erforderliche Bezeichner** ist mindestens erforderlich, um die zugehörige Entität zu identifizieren. Ein erforderlicher Bezeichner kann allein jedoch nicht ausreichen, um eine *eindeutige* Identifikation bereitzustellen. Umso mehr Bezeichner verwendet werden, desto größer ist die Wahrscheinlichkeit der eindeutigen Identifizierung. Sie können bis zu drei Bezeichner für eine einzelne Entitätszuordnung verwenden.

Um optimale Ergebnisse (garantiert eindeutige Identifizierung) zu gewährleisten, sollten Sie nach Möglichkeit Bezeichner aus der Spalte mit den **stärksten Bezeichnern** verwenden. Durch die Verwendung mehrerer starker Bezeichner wird die Korrelation zwischen starken Bezeichnern aus unterschiedlichen Datenquellen und Schemas ermöglicht. Dies ermöglicht es Microsoft Sentinel wiederum, umfassendere Einblicke für eine bestimmte Entität bereitzustellen.

| Entitätstyp | Bezeichner | Erforderliche Bezeichner | Stärkste Bezeichner |
| - | - | - | - |
| [**Benutzerkonto**](#user-account)<br>*(Konto)* | Name<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>Name<br>AadUserId<br>PUID<br>ObjectGuid | Name und NTDomain<br>Name und UPNSuffix<br>AADUserId<br>Sid |
| [**Host**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | HostName und NTDomain<br>HostName und DnsDomain<br>NetBiosName und NTDomain<br>NetBiosName und DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP address (IP-Adresse)**](#ip-address)<br>*(IP)* | Adresse | Adresse | |
| [**Malware**](#malware) | Name<br>Category | Name | |
| [**Datei**](#file) | Verzeichnis<br>Name | Name | |
| [**Prozess**](#process) | ProcessId<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>ProcessId | |
| [**Cloudanwendung**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**Domänenname**](#domain-name)<br>*(DNS)* | DomainName | DomainName | |
| [**Azure-Ressource**](#azure-resource) | ResourceId | ResourceId | |
| [**Dateihash**](#file-hash)<br>*(FileHash)* | Algorithmus<br>Wert | Algorithmus und Wert | |
| [**Registrierungsschlüssel**](#registry-key) | Hive<br>Schlüssel | Hive<br>Schlüssel | Hive und Schlüssel |
| [**Registrierungswert**](#registry-value) | Name<br>Wert<br>ValueType | Name | |
| [**Sicherheitsgruppe**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | url | url | |
| [**IoT-Gerät**](#iot-device) | IoTHub<br>deviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>`Source`<br>SourceRef<br>Hersteller<br>Modellieren<br>OperatingSystem<br>IpAddress<br>MacAddress<br>Protokolle<br>SerialNumber | IoTHub<br>deviceId | IoTHub und DeviceId |
| [**Mailbox**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Upn<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**E-Mail-Cluster**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Bedrohungen<br>Abfrage<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>`Source`<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Abfrage<br>`Source` | Abfrage und Quelle |
| [**E-Mail**](#mail-message) | Recipient<br>URLs<br>Bedrohungen<br>Sender<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>Subject<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Sprache<br>ThreatDetectionMethods | NetworkMessageId<br>Recipient | NetworkMessageId und Empfänger |
| [**Übermittlungs-E-Mail**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Absender<br>NetworkMessageId<br>Timestamp<br>Recipient<br>Sender<br>SenderIp<br>Subject<br>ReportType | SubmissionId<br>NetworkMessageId<br>Recipient<br>Absender |  |
|

## <a name="entity-type-schemas"></a>Entitätstypschemas

Im Folgenden finden Sie eine ausführlichere Beschreibung der vollständigen Schemas für jeden Entitätstyp. Sie werden feststellen, dass viele dieser Schemas Links zu anderen Entitätstypen enthalten, z. B. enthält das Benutzerkontoschema einen Link zum Host-Entitätstyp, da ein Attribut eines Benutzerkontos der Host ist, für den es definiert ist. Diese extern verknüpften Entitäten können nicht als Bezeichner für die Entitätszuordnung verwendet werden, sie sind jedoch sehr nützlich, um ein umfassendes Bild von Entitäten auf Entitätsseiten und im Untersuchungsdiagramm zu erhalten.

> [!NOTE]
> Ein Fragezeichen, das auf den Wert in der Spalte **Type** folgt, gibt an, dass das Feld NULL-Werte zulässt.

## <a name="user-account"></a>Benutzerkonto

*Entitätsname: Account*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘account’ |
| Name | String | Der Kontoname. Dieses Feld sollte nur den Namen enthalten, ohne dass eine Domäne hinzugefügt wird. |
| *FullName* | *N/V* | *Kein Teil des Schemas, aus Gründen der Abwärtskompatibilität mit der alten Version der Entitätszuordnung enthalten.*
| NTDomain | Zeichenfolge | Der NETBIOS-Domänenname, wie er im Warnungsformat angezeigt wird (domain\username). Beispiele: Finance, NT AUTHORITY |
| DnsDomain | Zeichenfolge | Der vollqualifizierte DNS-Domänenname. Beispiele: finance.contoso.com |
| UPNSuffix | Zeichenfolge | Das Suffix des Benutzerprinzipalnamens für das Konto. In einigen Fällen handelt es sich hierbei auch um den Domänennamen. Beispiele: contoso.com |
| Host | Entität | Der Host, der das Konto enthält, wenn es sich um ein lokales Konto handelt. |
| Sid | Zeichenfolge | Die Kontosicherheitsbezeichner, z. B. S-1-5-18. |
| AadTenantId | Guid? | Die Azure AD-Mandanten-ID, wenn bekannt. |
| AadUserId | Guid? | Die Azure AD-Kontoobjekt-ID, wenn bekannt. |
| PUID | Guid? | Die Azure AD-Passport-Benutzer-ID, wenn bekannt. |
| IsDomainJoined | Bool? | Bestimmt, ob es sich um ein Domänenkonto handelt. |
| DisplayName | Zeichenfolge | Der Anzeigename des Kontos. |
| ObjectGuid | Guid? | Das objectGUID-Attribut ist ein Einzelwertattribut, bei dem es sich um den eindeutigen Bezeichner für das Objekt handelt, der durch Active Directory zugewiesen wird. |
|

Starke Bezeichner einer Kontoentität:

- Name und UPNSuffix
- AadUserId
- Sid und Host (erforderlich für SIDs von integrierten Konten)
- Sid (ausgenommen SIDs von integrierten Konten)
- Name und NTDomäne (außer wenn NTDomain eine integrierte Domäne ist, z. B. „Workgroup“)
- Name und Host (wenn NTDomain eine integrierte Domäne ist, z. B. „Workgroup“)
- Name und DnsDomain
- PUID
- ObjectGuid

Schwache Bezeichner einer Kontoentität:

- Name

## <a name="host"></a>Host

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘host’ |
| DnsDomain | Zeichenfolge | Die DNS-Domäne, zu der dieser Host gehört. Sollte das vollständige DNS-Suffix für die Domäne enthalten, sofern bekannt. |
| NTDomain | Zeichenfolge | Die DNS-Domäne, zu der dieser Host gehört. |
| HostName | Zeichenfolge | Der Hostname ohne das Domänensuffix. |
| *FullName* | *N/V* | *Kein Teil des Schemas, aus Gründen der Abwärtskompatibilität mit der alten Version der Entitätszuordnung enthalten.*
| NetBiosName | Zeichenfolge | Der Hostname (vor Windows 2000). |
| IoTDevice | Entität | Die IoT-Geräteentität (wenn dieser Host ein IoT-Gerät darstellt). |
| AzureID | Zeichenfolge | Die Azure-Ressourcen-ID der VM, falls bekannt. |
| OMSAgentID | Zeichenfolge | Die OMS-Agent-ID, wenn der OMS-Agent auf dem Host installiert ist. |
| OSFamily | Enum? | Einer der folgenden Werte: <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | Zeichenfolge | Eine Freitextdarstellung des Betriebssystems.<br>Dieses Feld soll bestimmte Versionen enthalten, die präziser als OSFamily sind, oder zukünftige Werte, die von der OSFamily-Enumeration nicht unterstützt werden. |
| IsDomainJoined | Bool | Bestimmt, ob dieser Host zu einer Domäne gehört. |
|

Starke Bezeichner einer Hostentität:
- HostName und NTDomain
- HostName und DnsDomain
- NetBiosName und NTDomain
- NetBiosName und DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (wird für Entitätszuordnung nicht unterstützt)

Schwache Bezeichner einer Hostentität:
- HostName
- NetBiosName

## <a name="ip-address"></a>IP address (IP-Adresse)

*Entitätsname: IP*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘ip’ |
| Adresse | Zeichenfolge | Die IP-Adresse als Zeichenfolge, z. B. 127.0.0.1 (in IPv4 oder IPv6). |
| Location | GeoLocation | Der an die IP-Entität angefügte Geostandortkontext. <br><br>Weitere Informationen finden Sie unter [Anreichern von Entitäten in Microsoft Sentinel mit Geolocationdaten über die REST-API (öffentliche Vorschau)](geolocation-data-api.md). |
|

Starke Bezeichner einer IP-Entität:
- Adresse

## <a name="malware"></a>Malware

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘malware’ |
| Name | String | Der Name der Schadsoftware vom Hersteller, z. B `Win32/Toga!rfn`. |
| Category | String | Die Schadsoftwarekategorie vom Hersteller, z. B. Trojaner. |
| Dateien | List\<Entity> | Liste der verknüpften Dateientitäten, für die die Schadsoftware gefunden wurde. Kann die Dateientitäten inline oder als Verweis enthalten.<br>Weitere Details zur Struktur finden Sie in der File-Entität. |
| Prozesse | List\<Entity> | Liste der verknüpften Prozessentitäten, für die die Schadsoftware gefunden wurde. Dies wird häufig verwendet, wenn die Warnung bei einer dateilosen Aktivität ausgelöst wird.<br>Weitere Details zur Struktur finden Sie in der [Process](#process)-Entität. |
|

Starke Bezeichner einer Malwareentität:

- Name und Kategorie

## <a name="file"></a>Datei

| Feld | type | BESCHREIBUNG |
| ----- | ---- | ----------- |
| type | String | ‘file’ |
| Verzeichnis | Zeichenfolge | Der vollständige Pfad zur Datei. |
| Name | String | Der Dateiname ohne den Pfad (einige Warnungen enthalten möglicherweise keinen Pfad). |
| Host | Entität | Der Host, auf dem die Datei gespeichert wurde. |
| FileHashes | List&lt;Entity&gt; | Die Dateihashes, die dieser Datei zugeordnet sind. |
|

Starke Bezeichner einer Dateientität:
- Name und Verzeichnis
- Name und FileHash
- Name und Verzeichnis und FileHash

## <a name="process"></a>Prozess

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘process’ |
| ProcessId | Zeichenfolge | Die Prozess-ID. |
| CommandLine | Zeichenfolge | Die Befehlszeile, die zum Erstellen des Prozesses verwendet wird. |
| ElevationToken | Enum? | Das dem Prozess zugeordnete Erhöhungstoken.<br>Mögliche Werte:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | Die Zeit, zu der die Ausführung des Prozesses gestartet wurde. |
| ImageFile | Entität (Datei) | Kann die Dateientität inline oder als Verweis enthalten.<br>Weitere Details zur Struktur finden Sie in der File-Entität. |
| Konto | Entität | Das Konto, mit dem die Prozesse ausgeführt werden.<br>Kann die [Account](#user-account)-Entität inline oder als Verweis enthalten.<br>Weitere Details zur Struktur finden Sie in der [Account](#user-account)-Entität. |
| ParentProcess | Entität (Prozess) | Die übergeordnete Prozessentität. <br>Kann partielle Daten enthalten, d. h. nur die PID. |
| Host | Entität | Der Host, auf dem der Prozess ausgeführt wurde. |
| LogonSession | Entität (HostLogonSession) | Die Sitzung, in der der Prozess ausgeführt wurde. |
|

Starke Bezeichner einer Prozessentität:

- Host und ProcessID und CreationTimeUtc
- Host und ParentProcessId und CreationTimeUtc und CommandLine
- Host und ProcessId und CreationTimeUtc und ImageFile
- Host und ProcessId und CreationTimeUtc und ImageFile.FileHash

Schwache Bezeichner einer Prozessentität:

- ProcessID und CreationTimeUtc und CommandLine (und kein Host)
- ProcessID und CreationTimeUtc und ImageFile (und kein Host)

## <a name="cloud-application"></a>Cloudanwendung

*Entitätsname: CloudApplication*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘cloud-application’ |
| AppId | Int | Der technische Bezeichner der Anwendung. Dies sollte einer der Werte sein, die in der Liste der [Cloudanwendungsbezeichner](#cloud-application-identifiers) definiert sind. Der Wert für das AppId-Feld ist optional. |
| Name | String | Der Name der verwandten Cloudanwendung. Der Wert für den Anwendungsnamen ist optional. |
| InstanceName | Zeichenfolge | Der benutzerdefinierte Instanzname der Cloudanwendung. Er wird häufig verwendet, um zwischen verschiedenen Anwendungen desselben Typs zu unterscheiden, die ein Kunde verwendet. |
|

Starke Bezeichner einer Cloudanwendungsentität:
 - AppId (ohne InstanceName)
 - Name (ohne InstanceName)
 - AppId und InstanceName
 - Name und InstanceName

## <a name="domain-name"></a>Domänenname

*Entitätsname: DNS*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘dns’ |
| DomainName | Zeichenfolge | Der Name des DNS-Eintrags, der der Warnung zugeordnet ist. |
| IpAddress | List&lt;Entity (IP)&gt; | Entitäten, die den aufgelösten IP-Adressen entsprechen. |
| DnsServerIp | Entity (IP) | Eine Entität, die den DNS-Server darstellt, der die Anforderung auflöst. |
| HostIpAddress | Entity (IP) | Eine Entität, die den DNS-Anforderungsclient darstellt. |
|

Starke Bezeichner einer DNS-Entität:
- DomainName und DnsServerIp und HostIpAddress

Schwache Bezeichner einer DNS-Entität:
- DomainName und HostIpAddress

## <a name="azure-resource"></a>Azure-Ressource

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | 'azure-resource' |
| resourceId | String | Die Azure-Ressourcen-ID der Ressource. |
| SubscriptionId | Zeichenfolge | Die Abonnement-ID der Ressource. |
| TryGetResourceGroup | Bool | Der Ressourcengruppenwert, falls vorhanden. |
| TryGetProvider | Bool | Der Anbieterwert, falls vorhanden. |
| TryGetName | Bool | Der Namenwert, falls vorhanden. |
|

Starke Bezeichner einer Azure-Ressourcenentität:
- ResourceId

## <a name="file-hash"></a>Dateihash

*Entitätsname: FileHash*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | 'filehash' |
| Algorithmus | Enumeration | Der Hashalgorithmustyp. Mögliche Werte:<li>Unbekannt<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Wert | Zeichenfolge | Der Hashwert. |
|

Starke Bezeichner einer Dateihashentität:
- Algorithmus und Wert

## <a name="registry-key"></a>Registrierungsschlüssel

*Entitätsname: RegistryKey*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘registry-key’ |
| Hive | Enum? | Einer der folgenden Werte:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Schlüssel | Zeichenfolge | Der Registrierungsschlüsselpfad. |
|

Starke Bezeichner einer Registrierungsschlüsselentität:
- Hive und Schlüssel

## <a name="registry-value"></a>Registrierungswert

*Entitätsname: RegistryValue*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | ‘registry-value’ |
| Schlüssel | Entität (RegistryKey) | Die Registrierungsschlüsselentität. |
| Name | String | Der Name des Registrierungswerts. |
| Wert | Zeichenfolge | Als Zeichenfolge formatierte Darstellung der Wertdaten. |
| ValueType | Enum? | Einer der folgenden Werte:<li>String<li>Binary<li>DWord<li>Qword<li>MultiString<li>ExpandString<li>Keine<li>Unbekannt<br>Werte sollten der Microsoft. Win32.RegistryValueKind-Enumeration entsprechen. |
|

Starke Bezeichner einer Registrierungswertentität:
- Schlüssel und Name

Schwache Bezeichner einer Registrierungswertentität:
- Name (ohne Schlüssel)

## <a name="security-group"></a>Sicherheitsgruppe

*Entitätsname: SecurityGroup*

| Feld | type | BESCHREIBUNG |
| ----- | ---- | ----------- |
| type | String | 'security-group' |
| DistinguishedName | Zeichenfolge | Der Distinguished Name (DN) der Gruppe. |
| SID | Zeichenfolge | Das SID-Attribut ist ein Einzelwertattribut, das die Sicherheits-ID (SID) der Gruppe angibt. |
| ObjectGuid | Guid? | Das objectGUID-Attribut ist ein Einzelwertattribut, bei dem es sich um den eindeutigen Bezeichner für das Objekt handelt, der durch Active Directory zugewiesen wird. |
|

Starke Bezeichner einer Sicherheitsgruppenentität:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| Feld | type | BESCHREIBUNG |
| ----- | ---- | ----------- |
| type | String | 'url' |
| url | Uri | Eine vollständige URL, auf die die Entität verweist. |
|

Starke Bezeichner einer URL-Entität:
- URL (wenn absolute URL)

Schwache Bezeichner einer URL-Entität:
- URL (wenn relative URL)

## <a name="iot-device"></a>IoT-Gerät

*Entitätsname: IoTDevice*

| Feld | type | BESCHREIBUNG |
| ----- | ---- | ----------- |
| type | String | 'iotdevice' |
| IoTHub | Entität (AzureResource) | Die AzureResource-Entität, die den IoT Hub darstellt, zu dem das Gerät gehört. |
| deviceId | Zeichenfolge | Die ID des Geräts im Kontext des IoT Hub. |
| DeviceName | Zeichenfolge | Der Anzeigename des Geräts. |
| IoTSecurityAgentId | Guid? | Die ID des *Defender für IoT*-Agents, der auf dem Gerät ausgeführt wird. |
| DeviceType | Zeichenfolge | Der Typ des Geräts („Temperatursensor“, „Kühlung“, „Windrad“ usw.). |
| `Source` | String | Die Quelle (Microsoft/Hersteller) der Geräteentität. |
| SourceRef | Entität (URL) | Ein URL-Verweis auf das Quellelement, in dem das Gerät verwaltet wird. |
| Hersteller | Zeichenfolge | Der Hersteller des Geräts. |
| Modellieren | Zeichenfolge | Das Gerätemodell. |
| OperatingSystem | Zeichenfolge | Das Betriebssystem, das das Gerät ausführt. |
| IpAddress | Entity (IP) | Die aktuelle IP-Adresse des Geräts. |
| MacAddress | Zeichenfolge | Die MAC-Adresse des Geräts. |
| Protokolle | List&lt;String&gt; | Eine Liste der Protokolle, die vom Gerät unterstützt werden. |
| SerialNumber | Zeichenfolge | Die Seriennummer des Geräts. |
|

Starke Bezeichner einer IoT-Geräteentität:
- IoTHub und DeviceId

Schwache Bezeichner einer IoT-Geräteentität:
- DeviceId -ID (ohne IoTHub)

## <a name="mailbox"></a>Mailbox

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | 'mailbox' |
| MailboxPrimaryAddress | Zeichenfolge | Die primäre Adresse des Postfachs. |
| DisplayName | Zeichenfolge | Der Anzeigename des Postfachs. |
| Upn | Zeichenfolge | Der UPN des Postfachs. |
| RiskLevel | Enum? | Die Risikostufe dieses Postfachs. Mögliche Werte:<li>Keine<li>Niedrig<li>Medium<li>High |
| ExternalDirectoryObjectId | Guid? | Der AzureAD-Bezeichner des Postfachs. Ähnlich wie AadUserId in der Account-Entität. Diese Eigenschaft ist jedoch für das Postfachobjekt auf der Office-Seite spezifisch. |
|

Starke Bezeichner einer Postfachentität:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>E-Mail-Cluster

*Entitätsname: MailCluster*

> [!NOTE]
> **Microsoft Defender für Office 365** wurde früher als Office 365 Advanced Threat Protection (O365 ATP) bezeichnet.

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | 'mail-cluster' |
| NetworkMessageIds | IList&lt;String&gt; | Die E-Mail-Nachrichten-IDs, die Teil des Nachrichtenclusters sind. |
| CountByDeliveryStatus | IDictionary&lt;String,Int&gt; | Anzahl von E-Mail-Nachrichten nach DeliveryStatus-Zeichenfolgendarstellung. |
| CountByThreatType | IDictionary&lt;String,Int&gt; | Anzahl von E-Mail-Nachrichten nach ThreatType-Zeichenfolgendarstellung. |
| CountByProtectionStatus | IDictionary&lt;String,long&gt; | Anzahl von E-Mail-Nachrichten nach Bedrohungsschutzstatus. |
| Bedrohungen | IList&lt;String&gt; | Die Bedrohungen von E-Mail-Nachrichten, die Teil des Nachrichtenclusters sind. |
| Abfrage | Zeichenfolge | Die Abfrage, mit der die Nachrichten des Nachrichtenclusters identifiziert wurden. |
| QueryTime | DateTime? | Die Abfragezeit. |
| MailCount | Int? | Die Anzahl der E-Mail-Nachrichten, die Teil des Nachrichtenclusters sind. |
| IsVolumeAnomaly | Bool? | Bestimmt, ob es sich um einen Volumenanomalie-Nachrichtencluster handelt. |
| `Source` | String | Die Quelle des Nachrichtenclusters (Standardwert ist „O365 ATP“). |
| ClusterSourceIdentifier | Zeichenfolge | Die Netzwerknachrichten-ID der E-Mail, die die Quelle dieses Nachrichtenclusters ist. |
| ClusterSourceType | Zeichenfolge | Der Quelltyp des Nachrichtenclusters. Dieser wird der MailClusterSourceType-Einstellung von Microsoft Defender für Office 365 zugeordnet (siehe Hinweis oben). |
| ClusterQueryStartTime | DateTime? | Startzeit des Clusters: wird als Startzeit für die Abfrage der Clusteranzahl verwendet. Datiert in der Regel auf der Endzeit minus der DaysToLookBack-Einstellung aus Microsoft Defender für Office 365 (siehe Hinweis oben). |
| ClusterQueryEndTime | DateTime? | Endzeit des Clusters: wird als Endzeit für die Abfrage der Clusteranzahl verwendet. Normalerweise die Empfangszeit der Daten der E-Mail. |
| ClusterGroup | Zeichenfolge | Entspricht dem Kusto-Abfrageschlüssel, der von Microsoft Defender für Office 365 verwendet wird (siehe Hinweis oben). |
|

Starke Bezeichner einer Nachrichtencluster Entität:
- Abfrage und Quelle

## <a name="mail-message"></a>E-Mail

*Entitätsname: MailMessage*

| Feld | type | Beschreibung |
| ----- | ---- | ----------- |
| type | String | 'mail-message' |
| Dateien | IList&lt;File&gt; | Die Dateientitäten der Anlagen dieser E-Mail-Nachricht. |
| Recipient | Zeichenfolge | Der Empfänger dieser E-Mail-Nachricht. Im Fall mehrerer Empfänger wird die E-Mail-Nachricht kopiert, und jede Kopie weist einen Empfänger auf. |
| URLs | IList&lt;String&gt; | Die in dieser E-Mail-Nachricht enthaltenen URLs. |
| Bedrohungen | IList&lt;String&gt; | Die in dieser E-Mail-Nachricht enthaltenen Bedrohungen. |
| Sender | Zeichenfolge | Die E-Mail-Adresse des Absenders. |
| P1Sender | Zeichenfolge | E-Mail-ID des (delegierten) Benutzers, der diese E-Mail „im Auftrag von P2 (primärer Benutzer)“ gesendet hat. Wenn die E-Mail nicht vom Delegaten gesendet wird, entspricht dieser Wert P2Sender. |
| P1SenderDisplayName | Zeichenfolge | Anzeigename des (delegierten) Benutzers, der diese E-Mail „im Auftrag von P2 (primärer Benutzer)“ gesendet hat. Wird im E-Mail-Header durch die OnbehalfofSenderDisplayName-Eigenschaft dargestellt. |
| P1SenderDomain | Zeichenfolge | E-Mail-Domäne des (delegierten) Benutzers, der diese E-Mail „im Auftrag von P2 (primärer Benutzer)“ gesendet hat. Wenn die E-Mail nicht vom Delegaten gesendet wird, entspricht dieser Wert P2SenderDomain. |
| P2Sender | Zeichenfolge | E-Mail des (primären) Benutzers, in dessen Auftrag diese E-Mail gesendet wurde. |
| P2SenderDisplayName | Zeichenfolge | Anzeigename des (primären) Benutzers, in dessen Auftrag diese E-Mail gesendet wurde. Wenn die E-Mail nicht vom Delegaten gesendet wird, stellt dies den Anzeigenamen des Absenders dar. |
| P2SenderDomain | Zeichenfolge | E-Mail-Domäne des (primären) Benutzers, in dessen Auftrag diese E-Mail gesendet wurde. Wenn die E-Mail nicht vom Delegaten gesendet wird, stellt dies die Domäne des Absenders dar. |
| SenderIP | Zeichenfolge | Die IP-Adresse des Absenders. |
| ReceivedDate | Datetime | Das Empfangsdatum dieser Nachricht. |
| NetworkMessageId | Guid? | Die Netzwerknachrichten-ID dieser E-Mail-Nachricht. |
| InternetMessageId | Zeichenfolge | Die Internetnachrichten-ID dieser E-Mail-Nachricht. |
| Subject | Zeichenfolge | Der Betreff dieser E-Mail-Nachricht. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Wird von Microsoft Defender für Office 365 verwendet, um übereinstimmende oder ähnliche E-Mail-Nachrichten zu ermitteln. |
| AntispamDirection | Enum? | Die Direktionalität dieser E-Mail-Nachricht. Mögliche Werte:<li>Unbekannt<li>Eingehend<li>Ausgehend<li>Innerhalb der Organisation (intern) |
| DeliveryAction | Enum? | Die Übermittlungsaktion dieser E-Mail-Nachricht. Mögliche Werte:<li>Unbekannt<li>DeliveredAsSpam<li>Geliefert<li>Gesperrt<li>Ersetzt |
| DeliveryLocation | Enum? | Die Übermittlungsort dieser E-Mail-Nachricht. Mögliche Werte:<li>Unbekannt<li>Posteingang<li>JunkFolder<li>Deletedfolder<li>Quarantäne<li>Extern<li>Fehler<li>Dropped<li>Weitergeleitet |
| Sprache | String | Die Sprache, in der der Inhalt der E-Mail-Nachricht geschrieben wird. |
| ThreatDetectionMethods | IList&lt;String&gt; | Die Liste der auf diese E-Mail angewendeten Methoden zur Bedrohungserkennung. |
|

Starke Bezeichner einer E-Mail-Nachrichtenentität:
- NetworkMessageId und Empfänger

## <a name="submission-mail"></a>Übermittlungs-E-Mail

*Entitätsname: SubmissionMail*

| Feld | type | BESCHREIBUNG |
| ----- | ---- | ----------- |
| type | String | 'SubmissionMail' |
| SubmissionId | Guid? | Die Übermittlungs-ID. |
| SubmissionDate | DateTime? | Gemeldetes Datum und die Uhrzeit dieser Übermittlung. |
| Absender | Zeichenfolge | Die E-Mail-Adresse des Übermittlers. |
| NetworkMessageId | Guid? | Die Netzwerknachrichten-ID der E-Mail, zu der die Übermittlung gehört. |
| Timestamp | DateTime? | Der Zeitstempel für den Nachrichtempfang (E-Mail). |
| Recipient | Zeichenfolge | Der Empfänger der E-Mail. |
| Sender | Zeichenfolge | Der Absender der E-Mail. |
| SenderIp | Zeichenfolge | Die IP-Adresse des Absenders. |
| Subject | Zeichenfolge | Der Betreff der Übermittlungs-E-Mail. |
| ReportType | Zeichenfolge | Der Übermittlungstyp für die angegebene Instanz. Dies wird Junk, Phish, Malware oder NotJunk zugeordnet. |
|

Starke Bezeichner einer SubmissionMail-Entität:
- SubmissionId, Submitter, NetworkMessageId, Recipient

## <a name="cloud-application-identifiers"></a>Cloudanwendungsbezeichner

In der folgenden Liste werden Bezeichner für bekannte Cloudanwendungen definiert. Der App-ID-Wert wird als Entitätsbezeichner für die [Cloudanwendung](#cloud-application) verwendet.

|App-ID|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Feld|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|cornerstone ondemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive Software|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive for Business|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Defender für Cloud-Apps|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk Fusion Lifecycle|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype for Business|
|25988|Google Docs|
|26055|Microsoft Office 365 Admin Center|
|26060|OPSWAT Gears|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics 365|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS Proxy Emulator|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie die Entitätsstruktur, Bezeichner und das Schema in Microsoft Sentinel kennengelernt.

Informieren Sie sich über [Entitäten](entities-in-azure-sentinel.md) und die [Entitätszuordnung](map-data-fields-to-entities.md). 
