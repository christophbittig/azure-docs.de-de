---
title: Referenz zu Microsoft Sentinel-UEBA-Anreicherungen | Microsoft-Dokumentation
description: In diesem Artikel werden die Entitätsanreicherungen gezeigt, die von der Entitätsverhaltensanalyse von Microsoft Sentinel generiert werden.
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 345f34d2e0db508b831dca6d7f19b27436448de0
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519817"
---
# <a name="microsoft-sentinel-ueba-enrichments-reference"></a>Microsoft Sentinel UEBA Anreicherungen Referenz

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Artikel befasst sich mit der Microsoft Sentinel-Tabelle **BehaviorAnalytics**, die sich unter **Protokolle** befindet und die auf den [Seiten mit den Entitätsdetails](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages) erwähnt wird. Außerdem werden Details zu den Feldern für Entitätsanreicherungen in dieser Tabelle beschrieben, deren Inhalte Sie verwenden können, um Ihre Untersuchungen zu Sicherheitsvorfällen zu fokussieren und zu verbessern.

Die folgenden drei dynamischen Felder aus der Tabelle „BehaviorAnalytics“ werden in den [folgenden Tabellen](#entity-enrichments-dynamic-fields) beschrieben.

Die Felder [UsersInsights](#usersinsights-field) und [DevicesInsights](#devicesinsights-field), enthalten Entitätsinformationen aus Active Directory/Azure AD- und Microsoft Threat Intelligence-Quellen.

Das Feld [ActivityInsights](#activityinsights-field) enthält Entitätsinformationen, die auf den aus der Entitätsverhaltensanalyse von Microsoft Sentinel resultierenden Verhaltensprofilen basieren. 

<a name="baseline-explained"></a>Benutzeraktivitäten werden anhand einer Baseline analysiert, die bei jeder Verwendung dynamisch kompiliert wird. Jede Aktivität verfügt über ihren eigenen definierten Rückblickzeitraum, von dem die dynamische Baseline abgeleitet ist. Dieser Rückblickzeitraum wird in der Spalte [**Baseline**](#activityinsights-field) in dieser Tabelle angegeben.

> [!NOTE]
> In der Spalte **Anreicherungsname** in allen [Entitätsanreicherungsfeld](#entity-enrichments-dynamic-fields)-Tabellen werden zwei Zeilen mit Informationen angezeigt. 
>
> - Die erste in **Fett** ist der „Anzeigename“ der Anreicherung.
> - Der zweite *(kursiv und in Klammern)* ist der Feldname der Anreicherung, der in der [**Behavior Analytics-Tabelle**](#behavioranalytics-table) gespeichert ist.

> [!IMPORTANT]
> Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>
## <a name="behavioranalytics-table"></a>Tabelle „BehaviorAnalytics“

In der folgenden Tabelle werden die Verhaltensanalysedaten beschrieben, die auf jeder [Seite mit Entitätsdetails](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages) in Microsoft Sentinel angezeigt werden.

| Feld                     | type | BESCHREIBUNG                                                  |
|---------------------------|------|--------------------------------------------------------------|
| **TenantId**              | Zeichenfolge | Die eindeutige ID des Mandanten.                             |
| **SourceRecordId**        | Zeichenfolge | Die eindeutige ID des EBA-Ereignisses.                          |
| **TimeGenerated**         | datetime | Der Zeitstempel des Auftretens der Aktivität.                   |
| **TimeProcessed**         | datetime | Der Zeitstempel der Verarbeitung der Aktivität durch die EBA-Engine. |
| **ActivityType**          | Zeichenfolge | Die allgemeine Kategorie der Aktivität.                        |
| **ActionType**            | Zeichenfolge | Der normalisierte Name der Aktivität.                            |
| **UserName**              | Zeichenfolge | Der Benutzername des Benutzers, der die Aktivität eingeleitet hat.           |
| **UserPrincipalName**     | Zeichenfolge | Der vollständiger Benutzername des Benutzers, der die Aktivität eingeleitet hat.      |
| **EventSource**           | Zeichenfolge | Die Datenquelle, die das ursprüngliche Ereignis bereitgestellt hat.               |
| **SourceIPAddress**       | Zeichenfolge | Die IP-Adresse, über die die Aktivität eingeleitet wurde.               |
| **SourceIPLocation** | Zeichenfolge | Das Land, von dem aus die Aktivität eingeleitet wurde, ergänzt durch die IP-Adresse. |
| **SourceDevice**          | Zeichenfolge | Der Hostname des Geräts, das die Aktivität eingeleitet hat.         |
| **DestinationIPAddress**  | Zeichenfolge | Die IP-Adresse des Ziels der Aktivität.                   |
| **DestinationIPLocation** | Zeichenfolge | Das Land des Ziels der Aktivität, ergänzt durch die IP-Adresse. |
| **DestinationDevice**     | Zeichenfolge | Der Name des Zielgeräts.                                  |
| **UsersInsights**         | dynamisch | Die kontextabhängigen Anreicherungen der beteiligten Benutzer ([Details siehe unten](#usersinsights-field)). |
| **DevicesInsights**       | dynamisch | Die kontextabhängigen Anreicherungen der beteiligten Geräte ([Details siehe unten](#devicesinsights-field)). |
| **ActivityInsights**      | dynamisch | Die kontextabhängige Analyse von Aktivitäten basierend auf unserer Profilerstellung ([Details siehe unten](#activityinsights-field)). |
| **InvestigationPriority** | INT | Die Anomaliebewertung, Wert zwischen 0 und 10 (0 = unschädlich, 10 = hochgradig anomal).   |



## <a name="entity-enrichments-dynamic-fields"></a>Dynamische Felder für Entitätsanreicherungen

### <a name="usersinsights-field"></a>Feld „UsersInsights“

In der folgenden Tabelle werden die Anreicherungen beschrieben, die im dynamischen Feld **UsersInsights** in der Tabelle „BehaviorAnalytics“ aufgeführt sind:

| Anreicherungsname | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| **Anzeigename des Kontos**<br>*(AccountDisplayName)* | Der Kontoanzeigename des Benutzers. | Admin, Hayden Cook |
| **Kontodomäne**<br>*(AccountDomain)* | Der Kontodomänenname des Benutzers. |  |
| **Kontoobjekt-ID**<br>*(AccountObjectID)* | Die Kontoobjektobjekt-ID des Benutzers. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Auswirkungsgrad**<br>*(BlastRadius)* | Der Auswirkungsgrad wird anhand verschiedener Faktoren berechnet: der Position des Benutzers in der Organisationsstruktur und der Azure Active Directory-Rollen und -Berechtigungen des Benutzers. | Niedrig, Mittel, Hoch |
| **Konto im Ruhezustand**<br>*(IsDormantAccount)* | Das Konto wurde für die letzten 180 Tage nicht verwendet. | TRUE, FALSE |
| **Lokaler Administrator**<br>*(IsLocalAdmin)* | Das Konto verfügt über lokale Administratorberechtigungen. | TRUE, FALSE |
| **Neues Konto**<br>*(IsNewAccount)* | Das Konto wurde innerhalb der letzten 30 Tage erstellt. | TRUE, FALSE |
| **Lokale SID**<br>*(OnPremisesSID)* | Die lokale SID des Benutzers, der mit der Aktion verknüpft ist. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

### <a name="devicesinsights-field"></a>Feld „DevicesInsights“

In der folgenden Tabelle werden die Anreicherungen beschrieben, die im dynamischen Feld **DevicesInsights** in der Tabelle „BehaviorAnalytics“ aufgeführt sind:

| Anreicherungsname | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| **Browser**<br>*(Browser)* | Der in der Aktion verwendete Browser. | Microsoft Edge, Chrome |
| **Gerätefamilie**<br>*(DeviceFamily)* | Die in der Aktion verwendete Gerätefamilie. | Windows |
| **Gerätetyp**<br>*(DeviceType)* | Der in der Aktion verwendete Clientgerätetyp. | Desktop |
| **ISP**<br>*(ISP)* | Der in der Aktion verwendete Internetdienstanbieter. |  |
| **Betriebssystem**<br>*(OperatingSystem)* | Das in der Aktion verwendete Betriebssystem. | Windows 10 |
| **Threat Intelligence-Indikatorbeschreibung**<br>*(ThreatIntelIndicatorDescription)* | Beschreibung des beobachteten Bedrohungsindikators, der aus der in der Aktion verwendeten IP-Adresse aufgelöst wurde. | Host ist Mitglied von Botnet: azorult |
| **Threat Intelligence-Indikatortyp**<br>*(ThreatIntelIndicatorType)* | Der Typ des Bedrohungsindikators, der aus der in der Aktion verwendeten IP-Adresse aufgelöst wurde. | Botnet, C2, CryptoMining, Darknet, Ddos, MaliciousUrl, Malware, Phishing, Proxy, PUA, Watchlist |
| **Benutzer-Agent**<br>*(UserAgent)* | Der in der Aktion verwendete Benutzer-Agent. | Microsoft Azure Graph Client Library 1.0,<br>Swagger-Codegen/1.4.0.0/csharp,<br>EvoSTS |
| **Benutzer-Agent-Familie**<br>*(UserAgentFamily)* | Die in der Aktion verwendete Benutzer-Agent-Familie. | Chrome, Microsoft Edge, Firefox |
|

### <a name="activityinsights-field"></a>Feld „ActivityInsights“

In der folgenden Tabelle werden die Anreicherungen beschrieben, die im dynamischen Feld **ActivityInsights** in der Tabelle „BehaviorAnalytics“ aufgeführt sind:

#### <a name="action-performed"></a>Ausgeführte Aktion

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Zum ersten Mal vom Benutzer ausgeführte Aktion**<br>*(FirstTimeUserPerformedAction)* | 180 | Die Aktion wurde zum ersten Mal vom Benutzer ausgeführt. | TRUE, FALSE |
| **Ungewöhnlicherweise vom Benutzer ausgeführte Aktion**<br>*(ActionUncommonlyPerformedByUser)* | 10 | Die Aktion wird vom Benutzer üblicherweise nicht ausgeführt. | TRUE, FALSE |
| **Ungewöhnlicherweise unter Peers ausgeführte Aktion**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | Die Aktion wird üblicherweise nicht unter Peers des Benutzers ausgeführt. | TRUE, FALSE |
| **Zum ersten Mal im Mandanten ausgeführte Aktion**<br>*(FirstTimeActionPerformedInTenant)* | 180 | Die Aktion wurde zum ersten Mal von jemand in der Organisation ausgeführt. | TRUE, FALSE |
| **Ungewöhnlicherweise im Mandanten ausgeführte Aktion**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | Die Aktion wird üblicherweise nicht in der Organisation ausgeführt. | TRUE, FALSE |
|

#### <a name="app-used"></a>Verwendete App

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Zum ersten Mal vom Benutzer verwendete App**<br>*(FirstTimeUserUsedApp)* | 180 | Die App wurde zum ersten Mal vom Benutzer verwendet. | TRUE, FALSE |
| **Üblicherweise nicht vom Benutzer verwendete App**<br>*(AppUncommonlyUsedByUser)* | 10 | Die App wird vom Benutzer üblicherweise nicht verwendet. | TRUE, FALSE |
| **Üblicherweise nicht unter Peers verwendete App**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | Die App wird in der Regel nicht unter Peers des Benutzers verwendet. | TRUE, FALSE |
| **Zum ersten Mal im Mandanten beobachtete App**<br>*(FirstTimeAppObservedInTenant)* | 180 | Die App wurde zum ersten Mal in der Organisation beobachtet. | TRUE, FALSE |
| **Üblicherweise nicht im Mandanten verwendete App**<br>*(AppUncommonlyUsedInTenant)* | 180 | Die App wird in der Organisation nicht häufig verwendet. | TRUE, FALSE |
| 

#### <a name="browser-used"></a>Verwendeter Browser

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Erstmalige Verbindung des Benutzers über den Browser**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | Der Browser wurde zum ersten Mal vom Benutzer verwendet. | TRUE, FALSE |
| **Üblicherweise nicht vom Benutzer verwendeter Browser**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Der Browser wird vom Benutzer üblicherweise nicht verwendet. | TRUE, FALSE |
| **Üblicherweise nicht unter Peers verwendeter Browser**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | Der Browser wird in der Regel nicht unter Peers des Benutzers verwendet. | TRUE, FALSE |
| **Zum ersten Mal im Mandanten beobachteter Browser**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Der Browser wurde zum ersten Mal in der Organisation beobachtet. | TRUE, FALSE |
| **Üblicherweise nicht im Mandanten verwendeter Browser**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Der Browser wird in der Organisation nicht häufig verwendet. | TRUE, FALSE |
| 

#### <a name="country-connected-from"></a>Land, von dem aus die Verbindung hergestellt wurde

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Erstmalige Verbindung des Benutzers von diesem Land aus**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | Von diesem geografischen Standort laut Auflösung der IP-Adresse aus wurde vom Benutzer zum ersten Mal eine Verbindung hergestellt. | TRUE, FALSE |
| **Land, von dem aus der Benutzer üblicherweise keine Verbindung herstellt**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | Von diesem geografischen Standort laut Auflösung der IP-Adresse aus wird vom Benutzer üblicherweise keine Verbindung hergestellt. | TRUE, FALSE |
| **Land, von dem aus üblicherweise keine Verbindung unter Peers hergestellt wird**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | Von diesem geografischen Standort laut Auflösung der IP-Adresse aus wird üblicherweise keine Verbindung unter Peers hergestellt. | TRUE, FALSE |
| **Zum ersten Mal im Mandanten beobachtete Verbindung von diesem Land aus**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Zum ersten Mal wurde von jemandem in der Organisation eine Verbindung von diesem Land aus hergestellt. | TRUE, FALSE |
| **Land, von dem aus im Mandanten üblicherweise keine Verbindung hergestellt wird**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | Von diesem geografischen Standort laut Auflösung der IP-Adresse aus wird von der Organisation üblicherweise keine Verbindung hergestellt. | TRUE, FALSE |
| 

#### <a name="device-used-to-connect"></a>Zum Herstellen der Verbindung verwendetes Gerät

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Erstmalige Verbindung des Benutzers von diesem Gerät aus**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | Vom Quellgerät aus wurde vom Benutzer zum ersten Mal eine Verbindung hergestellt. | TRUE, FALSE |
| **Üblicherweise nicht vom Benutzer verwendetes Gerät**<br>*(DeviceUncommonlyUsedByUser)* | 10 | Das Gerät wird vom Benutzer üblicherweise nicht verwendet. | TRUE, FALSE |
| **Üblicherweise nicht unter Peers verwendetes Gerät**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | Das Gerät wird in der Regel nicht unter Peers des Benutzers verwendet. | TRUE, FALSE |
| **Zum ersten Mal im Mandanten beobachtetes Gerät**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Das Gerät wurde zum ersten Mal in der Organisation beobachtet. | TRUE, FALSE |
| **Üblicherweise nicht im Mandanten verwendetes Gerät**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Das Gerät wird in der Organisation nicht häufig verwendet. | TRUE, FALSE |
| 

#### <a name="other-device-related"></a>Sonstige gerätebezogene Aspekte

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Der Benutzer hat sich zum ersten Mal beim Gerät angemeldet**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | Der Benutzer hat zum ersten Mal eine Verbindung mit dem Zielgerät hergestellt. | TRUE, FALSE |
| **Üblicherweise nicht im Mandanten verwendete Gerätefamilie**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | Die Gerätefamilie wird in der Organisation nicht häufig verwendet. | TRUE, FALSE |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Zum Herstellen der Verbindung verwendeter Internetdienstanbieter (ISP)

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Erstmalige Verbindung des Benutzers über den ISP**<br>*(FirstTimeUserConnectedViaISP)* | 30 | Der ISP wurde zum ersten Mal vom Benutzer verwendet. | TRUE, FALSE |
| **Üblicherweise nicht vom Benutzer verwendeter ISP**<br>*(ISPUncommonlyUsedByUser)* | 10 | Der ISP wird vom Benutzer üblicherweise nicht verwendet. | TRUE, FALSE |
| **Üblicherweise nicht unter Peers verwendeter ISP**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | Der ISP wird in der Regel nicht unter Peers des Benutzers verwendet. | TRUE, FALSE |
| **Erstmalige Verbindungsherstellung über den ISP im Mandanten**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | Der ISP wurde zum ersten Mal in der Organisation beobachtet. | TRUE, FALSE |
| **Üblicherweise nicht im Mandanten verwendeter ISP**<br>*(ISPUncommonlyUsedInTenant)* | 30 | Der ISP wird in der Organisation nicht häufig verwendet. | TRUE, FALSE |
| 

#### <a name="resource-accessed"></a>Aufgerufene Ressource

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Erstmaliger Zugriff des Benutzers auf die Ressource**<br>*(FirstTimeUserAccessedResource)* | 180 | Der Benutzer hat zum ersten Mal auf die Ressource zugegriffen. | TRUE, FALSE |
| **Ressource, auf die vom Benutzer nicht häufig zugegriffen wird**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | Der Benutzer greift in der Regel nicht auf die Ressource zu. | TRUE, FALSE |
| **Ressource, auf die unter Peers nicht häufig zugegriffen wird**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | Unter Peers des Benutzers wird in der Regel nicht auf die Ressource zugegriffen. | TRUE, FALSE |
| **Erstmaliger Zugriff auf Ressource im Mandanten**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | Auf die Ressource wurde zum ersten Mal von jemand in der Organisation zugegriffen. | TRUE, FALSE |
| **Ressource, auf die im Mandanten nicht häufig zugegriffen wird**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | Auf die Ressource wird in der Organisation in der Regel nicht zugegriffen. | TRUE, FALSE |
| 

#### <a name="miscellaneous"></a>Verschiedenes

| Anreicherungsname | [Baseline](#baseline-explained) (Tage) | BESCHREIBUNG | Beispielwert |
| --- | --- | --- | --- |
| **Beim letzten Mal vom Benutzer ausgeführte Aktion**<br>*(LastTimeUserPerformedAction)* | 180 | Beim letzten Mal hat der Benutzer die gleiche Aktion ausgeführt. | \<Timestamp\> |
| **Eine ähnliche Aktion wurde in der Vergangenheit nicht ausgeführt.**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | Keine Aktion wurde vom Benutzer im gleichen Ressourcenanbieter durchgeführt. | TRUE, FALSE |
| **Quell-IP-Standort**<br>*(SourceIPLocation)* | *N/V* | Das aus der Quell-IP der Aktion aufgelöste Land. | [Surrey, England] |
| **Ungewöhnlich hohe Anzahl an Vorgängen**<br>*(UncommonHighVolumeOfOperations)* | 7 | Ein Benutzer hat einen Burst ähnlicher Vorgänge innerhalb desselben Anbieters durchgeführt. | TRUE, FALSE |
| **Ungewöhnliche Anzahl von „Bedingter Zugriff“-Azure AD-Fehlern**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Eine ungewöhnliche Anzahl von Benutzern konnte sich aufgrund des bedingten Zugriffs nicht authentifizieren. | TRUE, FALSE |
| **Ungewöhnliche Anzahl von Geräten wurde hinzugefügt**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Ein Benutzer hat eine ungewöhnliche Anzahl von Geräten hinzugefügt. | TRUE, FALSE |
| **Ungewöhnliche Anzahl von Geräten wurde gelöscht**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Ein Benutzer hat eine ungewöhnliche Anzahl von Geräten gelöscht. | TRUE, FALSE |
| **Ungewöhnliche Anzahl von Benutzern wurde der Gruppe hinzugefügt**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Ein Benutzer hat eine ungewöhnliche Anzahl von Benutzern einer Gruppe hinzugefügt. | TRUE, FALSE |
|


## <a name="identityinfo-table-public-preview"></a>IdentityInfo-Tabelle (öffentliche Vorschau)

Nachdem Sie [UEBA](enable-entity-behavior-analytics.md) für Ihren Microsoft Sentinel-Arbeitsbereich aktiviert haben, werden Daten aus Ihrer Azure Active Directory-Instanz zur Verwendung in Microsoft Sentinel mit der **IdentityInfo-Tabelle** in Log Analytics synchronisiert. Sie können aus Azure AD synchronisierte Benutzerdaten in Ihre Analyseregeln einbetten, um die Analysen für Ihre Anwendungsfälle zu verbessern und falsch positive Ergebnisse zu reduzieren.

Die Erstsynchronisierung kann einige Tage dauern. Nachdem die Daten vollständig synchronisiert wurden, gilt Folgendes:

- Änderungen an Ihren Benutzerprofilen in Azure AD werden innerhalb von 15 Minuten in der **IdentityInfo**-Tabelle aktualisiert.

- Gruppen- und Rolleninformationen werden täglich zwischen der **IdentityInfo**-Tabelle und Azure AD synchronisiert.

- Microsoft Sentinel führt alle 21 Tage eine erneute Synchronisierung mit Ihrer gesamten Azure AD-Instanz durch, um sicherzustellen, dass veraltete Datensätze vollständig aktualisiert werden.

- Die Standardaufbewahrungsdauer in der **IdentityInfo**-Tabelle beträgt 30 Tage.


> [!NOTE]
> Derzeit werden nur integrierte Rollen unterstützt.
>
> Daten zu gelöschten Gruppen, bei denen ein Benutzer aus einer Gruppe entfernt wurde, werden derzeit nicht unterstützt.
>

In der folgenden Tabelle werden die Benutzeridentitätsdaten beschrieben, die in der **IdentityInfo**-Tabelle in Log Analytics enthalten sind.

| Feld                      | type     | BESCHREIBUNG                                                                                                             |
| --------------------------- | -------- | -------------------------------------------------------- |
| **AccountCloudSID**             | Zeichenfolge   | Die Azure AD-Sicherheits-ID des Kontos.       |
| **AccountCreationTime**         | datetime | Das Datum, an dem das Benutzerkonto erstellt wurde (UTC).    |
| **AccountDisplayName**          | Zeichenfolge   | Der Anzeigename des Benutzerkontos.           |
| **AccountDomain**               | Zeichenfolge   | Der Domänenname des Benutzerkontos.  |
| **AccountName**                 | Zeichenfolge   | Der Benutzername des Benutzerkontos.      |
| **AccountObjectId**             | Zeichenfolge   | Die Azure Active Directory-Objekt-ID für das Benutzerkonto.           |
| **AccountSID**                  | Zeichenfolge   | Die lokale Sicherheits-ID des Benutzerkontos.      |
| **AccountTenantId**             | Zeichenfolge   | Die Azure Active Directory-Mandanten-ID des Benutzerkontos.    |
| **AccountUPN**                  | Zeichenfolge   | Der Benutzerprinzipalname des Benutzerkontos.     |
| **AdditionalMailAddresses**     | dynamisch  | Die zusätzlichen E-Mail-Adressen des Benutzers.   |
| **AssignedRoles**               | dynamisch  | Die Azure AD-Rollen, die dem Benutzerkonto zugewiesen sind.    |
| **City (Ort)**                        | Zeichenfolge   | Der Ort für das Benutzerkonto.   |
| **Country**                     | Zeichenfolge   | Das Land für das Benutzerkonto.   |
| **DeletedDateTime**             | datetime | Das Datum und die Uhrzeit der Löschung des Benutzers.       |
| **Abteilung**                  | Zeichenfolge   | Die Abteilung für das Benutzerkonto.    |
| **GivenName**                   | Zeichenfolge   | Der Vorname für das Benutzerkonto.     |
| **GroupMembership**             | dynamisch  | Azure AD-Gruppen, in denen das Benutzerkonto Mitglied ist.      |
| **IsAccountEnabled**            | bool     | Ein Hinweis darauf, ob das Benutzerkonto in Azure AD aktiviert ist oder nicht.    |
| **JobTitle**                    | Zeichenfolge   | Die Position für das Benutzerkonto.    |
| **MailAddress**                 | Zeichenfolge   | Die primäre E-Mail-Adresse für das Benutzerkonto.    |
| **Manager**                     | Zeichenfolge   | Der Alias des Vorgesetzten für das Benutzerkonto.     |
| **OnPremisesDistinguishedName** | Zeichenfolge   | Der Azure AD-DN (Distinguished Name). Ein DN ist eine Sequenz relativer DNs (RDN), die durch Kommas verbunden sind. |
| **Telefon**                       | Zeichenfolge   | Die Telefonnummer für das Benutzerkonto.       |
| **SourceSystem**                | Zeichenfolge   | Das System, aus dem die Benutzerdaten stammen.   |
| **State**                       | Zeichenfolge   | Das Bundesland/der Kanton für das Benutzerkonto.  |
| **StreetAddress**               | Zeichenfolge   | Die Büroadresse für das Benutzerkonto.    |
| **Surname**                     | Zeichenfolge   | Nachname des Benutzers Benutzerkonto.      |
| **TenantId**                    | Zeichenfolge   |          Die Mandanten-ID des Benutzers.   |
| **TimeGenerated**               | datetime | Der Zeitpunkt, zu dem das Ereignis generiert wurde (UTC).       |
| **Type**                        | Zeichenfolge   | Der Name der Tabelle.          |
| **UserState**                   | Zeichenfolge   | Der aktuelle Zustand des Benutzerkontos in Azure AD (aktiv/deaktiviert/ruhend/Sperrung).  |
| **UserStateChangedOn**          | datetime | Das Datum der letzten Änderung des Kontozustands (UTC).     |
| **UserType**                    | Zeichenfolge   | Der Benutzertyp.         |


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde das Tabellenschema der Entitätsverhaltensanalyse in Microsoft Sentinel beschrieben.

- Erfahren Sie mehr über die [Entitätsverhaltensanalyse](identify-threats-with-entity-behavior-analytics.md).
- [Verwenden von UEBA](investigate-with-ueba.md) in Ihren Untersuchungen.
