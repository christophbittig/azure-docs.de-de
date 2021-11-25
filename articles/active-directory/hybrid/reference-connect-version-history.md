---
title: 'Azure AD Connect: Versionsverlauf | Microsoft-Dokumentation'
description: In diesem Artikel sind alle Versionen von Azure AD Connect und Azure AD Sync aufgeführt.
author: billmath
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/21/2021
ms.subservice: hybrid
ms.author: rodejo
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: de2b1f3a6eb44802d074c813881702072e61339f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397640"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Verlauf der Versionsveröffentlichungen
Das Azure Active Directory-Team (Azure AD) aktualisiert Azure AD Connect regelmäßig mit neuen Features und Funktionen. Nicht alle Erweiterungen gelten für alle Benutzergruppen.

Dieser Artikel soll Ihnen helfen, die Versionen zu verfolgen, die veröffentlicht wurden, und zu verstehen, welche Änderungen bei der aktuellen Version vorgenommen wurden.

## <a name="looking-for-the-latest-versions"></a>Suchen Sie die neuesten Versionen?

Sie können Ihren AADConnect Server von allen unterstützten Versionen mit den neuesten Versionen aktualisieren:

 - Wenn Sie **Windows Server 2016 oder neuer** verwenden, sollten Sie **Azure AD Connect V2.0** verwenden. Sie können die neueste Version von Azure AD Connect 2.0 über [diesen Link](https://www.microsoft.com/en-us/download/details.aspx?id=47594) herunterladen. Die Versionshinweise für die neueste Version V2.0 finden Sie [hier](reference-connect-version-history.md#20280)
 - Wenn Sie noch eine **ältere Version von Windows Server** verwenden, sollten Sie **Azure AD Connect V1.6** verwenden. Sie können die neueste Version von Azure AD Connect 1.6 über [diesen Link](https://www.microsoft.com/download/details.aspx?id=103336) herunterladen. Die Versionshinweise für die neueste Version 1.6 finden Sie [hier](reference-connect-version-history.md#16160)
 - Wir werden in Zukunft nur noch kritische Änderungen an den V1.x-Versionen vornehmen, und einige der Funktionen und Korrekturen für V2.0 werden Sie in den V1.x-Versionen nicht finden. Insbesondere gibt es ein Problem mit dem Build 1.16.4.2, bei dem das Upgrade auf diesen v1.6-Build oder neuere Builds das Gruppenlimit auf 50.000 zurücksetzt. Wenn ein Server auf diesen Build oder neuere 1.6-Builds aktualisiert wird, sollte der Kunde die Regeländerungen erneut anwenden, die beim anfänglichen Erhöhen des Gruppenmitgliedschaftslimits auf 250.000 angewendet wurden, bevor er die Synchronisierung für den Server aktiviert. 

Dieser Tabelle enthält eine Liste der verwandten Themen:

Thema |  Details
--------- | --------- |
Schritte zum Upgrade von Azure AD Connect | Verschiedene Methoden zum [Aktualisieren von einer früheren Version auf die aktuelle Version](how-to-upgrade-previous-version.md) von Azure AD Connect.
Erforderliche Berechtigungen | Informationen zu den zum Anwenden eines Updates erforderlichen Berechtigungen finden Sie unter [Konten und Berechtigungen](reference-connect-accounts-permissions.md#upgrade).


>[!IMPORTANT]
> **Am 31. August 2022 werden alle 1.x-Versionen von Azure Active Directory (Azure AD) Connect eingestellt, weil sie SQL Server 2012-Komponenten enthalten, die nicht mehr unterstützt werden.** Führen Sie bis zu diesem Datum ein Upgrade auf die neueste Version von Azure AD Connect (2.x-Version) durch, oder [testen und wechseln Sie zur Azure AD-Cloudsynchronisierung](../cloud-sync/what-is-cloud-sync.md).
> 
> Sie müssen sicherstellen, dass Sie eine aktuelle Version von Azure AD Connect ausführen, um einen optimalen Support zu erhalten. 
> 
> Wenn Sie eine außer Betrieb gesetzte Version von Azure AD Connect ausführen, kann diese unerwartet ihre Funktion einstellen, und Sie profitieren nicht von den neuesten Sicherheitsfixes, Leistungsverbesserungen, Tools für Problembehandlung und Diagnose sowie Dienstverbesserungen. Wenn Sie zudem Unterstützung benötigen, können wir Ihnen möglicherweise nicht das für Ihre Organisation erforderliche Serviceniveau bieten.
> 
> In diesem Artikel erfahren Sie mehr über [Azure Active Directory Connect V2.0](whatis-azure-ad-connect-v2.md), über die Änderungen in V2.0 und deren Auswirkungen für Sie.
>
> In [diesem Artikel](./how-to-upgrade-previous-version.md) erfahren Sie mehr über das Upgrade von Azure AD Connect auf die aktuelle Version.
>
> Informationen zum Versionsverlauf für eingestellte Versionen finden Sie unter [Azure AD Connect: Archiv des Verlaufs der Versionsveröffentlichungen](reference-connect-version-history-archive.md).

>[!NOTE]
>Das Veröffentlichen einer neuen Version von Azure AD Connect ist ein Prozess, der mehrere Qualitätskontrollschritte erfordert, um die Funktionalität des Diensts sicherzustellen. Im Laufe dieses Prozesses wird die Versionsnummer und der Status des Releases aktualisiert, um den aktuellen Zustand wiederzugeben.
Nicht für alle Releases von Azure AD Connect wird das automatische Upgrade zur Verfügung gestellt. Aus dem Releasestatus geht hervor, ob für ein Release das automatische Upgrade oder nur der Download verfügbar gemacht wird. Wenn das automatische Upgrade auf Ihrem Azure AD Connect-Server aktiviert ist, wird dieser Server automatisch auf die neueste Version von Azure AD Connect aktualisiert, die für das automatische Upgrade veröffentlicht wird. Beachten Sie, dass nicht alle Azure AD Connect-Konfigurationen für ein automatisches Upgrade berechtigt sind. 

>Um die Verwendung des automatischen Upgrades klarzustellen, sollten alle wichtigen Updates und Korrekturen an Sie gepusht werden. Dies ist nicht unbedingt die neueste Version, da nicht alle Versionen eine Behebung eines kritischen Sicherheitsproblems erfordern/einschließen (nur ein Beispiel für viele). Kritische Probleme würden in der Regel durch eine neue Version behoben, die über das automatische Upgrade bereitgestellt wird. Wenn solche Probleme nicht vorliegen, werden keine Updates mithilfe des automatischen Upgrades gepusht, und im Allgemeinen sollten Sie auf der sicheren Seite sein, wenn Sie die neueste Version des automatischen Upgrades verwenden.
Wenn Sie jedoch alle neuesten Features und Updates wünschen, überprüfen Sie am besten anhand dieser Seite, welche vorliegen, und installieren sie nach Wunsch. 

>Unter dem Link [Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md) erhalten Sie weitere Informationen zu diesem Thema.


## <a name="16160"></a>1.6.16.0
>[!NOTE] 
>Dies ist ein Hotfix-Updaterelease von Azure AD Connect. Diese Version soll von Kunden verwendet werden, die eine ältere Windows Server-Version ausführen und ihren Server zurzeit nicht auf Windows Server 2016 oder höher aktualisieren können. Sie können mit dieser Version keinen Azure AD Connect V2.0-Server aktualisieren. Dieses Release wird auf Windows Server 2016 oder neueren Versionen nicht unterstützt. Dieses Release beinhaltet SQL Server 2012-Komponenten und wird am 31. August 2022 eingestellt. Sie müssen Ihr Serverbetriebssystem und AADConnect-Version vor diesem Datum upgraden.
>Es gibt ein Problem, bei dem ein Upgrade auf diesen v1.6-Build oder neuere Builds das Gruppenmitgliedschaftslimit auf 50.000 zurücksetzt. Wenn ein Server auf diesen Build oder neuere 1.6-Builds aktualisiert wird, sollte der Kunde die Regeländerungen erneut anwenden, die beim anfänglichen Erhöhen des Gruppenmitgliedschaftslimits auf 250.000 angewendet wurden, bevor er die Synchronisierung für den Server aktiviert. 

### <a name="release-status"></a>Releasestatus
13.10.2021: Als Download bereitgestellt und automatisches Upgrade durchgeführt.

### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Es wurde ein Fehler behoben, bei dem der Autoupgrade-Prozess versucht hat, ein Upgrade für AADConnect-Server durchzuführen, auf denen ältere Windows-Betriebssystemversionen wie Windows 2008 oder Windows 2008 R2 ausgeführt wurden und ein Fehler aufgetreten ist. Diese Versionen von Windows Server werden nicht mehr unterstützt. In diesem Release wird nur versucht, das automatische Upgrade auf Computern durchzuführen, auf denen Windows Server 2012 oder neuer ausgeführt wird.
- Es wurde ein Problem behoben, bei dem MIIS-Server unter bestimmten Bedingungen aufgrund einer Zugriffsverletzungsausnahme abstürzte.

### <a name="known-issues"></a>Bekannte Probleme
 - Es gibt ein Problem, bei dem ein Upgrade auf diesen v1.6-Build oder neuere Builds das Gruppenmitgliedschaftslimit auf 50.000 zurücksetzt. Wenn ein Server auf diesen Build oder neuere 1.6-Builds aktualisiert wird, sollte der Kunde die Regeländerungen erneut anwenden, die beim anfänglichen Erhöhen des Gruppenmitgliedschaftslimits auf 250.000 angewendet wurden, bevor er die Synchronisierung für den Server aktiviert. 

## <a name="20280"></a>2.0.28.0

>[!NOTE] 
> Dies ist ein Wartungsupdaterelease von Azure AD Connect. Für dieses Release ist Windows Server 2016 oder höher erforderlich.

### <a name="release-status"></a>Releasestatus
30.09.2021: Nur zum Download freigegeben, nicht für die automatische Aktualisierung verfügbar.

### <a name="bug-fixes"></a>Behebung von Programmfehlern
 - Auf der Seite „Berechtigungen“ für das Gruppenrückschreiben im Assistenten wurde eine Downloadschaltfläche für ein PowerShell-Skript entfernt. Außerdem wurde der Text auf der Assistentenseite so geändert, dass er einen Link zu weiteren Informationen in einem Onlineartikel enthält, in dem auch das PowerShell-Skript bereitgestellt wird.

 - Es wurde ein Fehler behoben, bei dem der Assistent die Installation fälschlicherweise blockierte, wenn die .NET-Version auf dem Server aufgrund fehlender Registrierungsschlüssel größer als 4.6 war. Diese Registrierungsschlüssel sind nicht erforderlich und sollten die Installation nur blockieren, wenn sie absichtlich auf FALSE festgelegt werden.

- Es wurde ein Fehler behoben, durch den ein Fehler ausgelöst wurde, wenn während der Initialisierung eines Synchronisierungsschritts Phantomobjekte gefunden wurden. Dadurch wurde der Synchronisierungsschritt blockiert, oder es wurden vorübergehende Objekte entfernt. Die Phantomobjekte werden nun ignoriert.
Hinweis: Ein Phantomobjekt ist ein Platzhalter für ein Objekt, das noch nicht vorhanden ist oder noch nicht ermittelt wurde. Wenn z. B. ein Quellobjekt über einen Verweis auf ein Zielobjekt verfügt, das nicht vorhanden ist, wird das Zielobjekt als Phantom erstellt.

### <a name="functional-changes"></a>Funktionale Änderungen

 - Es wurde eine Änderung vorgenommen, die Benutzer*innen ermöglicht, die Auswahl von Objekten und Attributen aus der Aufnahmeliste aufzuheben, auch wenn sie verwendet werden. Anstelle der Blockierung wird jetzt eine Warnung angezeigt. 

## <a name="16142"></a>1.6.14.2
>[!NOTE] 
>Dies ist ein Hotfix-Updaterelease von Azure AD Connect. Diese Version soll von Kunden verwendet werden, die eine ältere Windows Server-Version ausführen und ihren Server zurzeit nicht auf Windows Server 2016 oder höher aktualisieren können. Sie können mit dieser Version keinen Azure AD Connect V2.0-Server aktualisieren.
>Wir beginnen mit dem automatischen Upgrade berechtigter Mandanten, wenn diese Version zum Download verfügbar ist. Das automatische Upgrade dauert einige Wochen.
>Es gibt ein Problem, bei dem ein Upgrade auf diesen v1.6-Build oder neuere Builds das Gruppenmitgliedschaftslimit auf 50.000 zurücksetzt. Wenn ein Server auf diesen Build oder neuere 1.6-Builds aktualisiert wird, sollte der Kunde die Regeländerungen erneut anwenden, die beim anfänglichen Erhöhen des Gruppenmitgliedschaftslimits auf 250.000 angewendet wurden, bevor er die Synchronisierung für den Server aktiviert. 


### <a name="release-status"></a>Releasestatus
21.09.2021: Als Download bereitgestellt und automatisches Upgrade durchgeführt.

### <a name="functional-changes"></a>Funktionale Änderungen
 - Wir haben die neuesten Versionen von MIM Connectors (1.1.1610.0) hinzugefügt. Weitere Informationen finden Sie auf der Seite [Versionsgeschichte der MiM-Connectors](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)
 - Wir haben eine Konfigurationsoption hinzugefügt, um das Feature „Soft Matching“ in Azure AD Connect zu deaktivieren. Wir empfehlen Kunden, das Soft Matching zu deaktivieren, es sei denn, sie benötigen es, um reine Cloudkonten zu übernehmen. In diesem [Artikel](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant) wird gezeigt, wie Sie das Soft Matching deaktivieren.

### <a name="bug-fixes"></a>Behebung von Programmfehlern
 - Es wurde ein Fehler behoben, bei dem die DesktopSSO-Einstellungen nach dem Upgrade von einer früheren Version nicht beibehalten wurden.
 - Es wurde ein Fehler behoben, der dazu führte, dass die Cmdlets "Set-ADSync\*Permission“ versagten.

## <a name="20251"></a>2.0.25.1

>[!NOTE] 
> Dies ist ein Hotfix-Updaterelease von Azure AD Connect. Diese Version erfordert Windows Server 2016 oder höher und behebt ein Sicherheitsproblem, das in Version 2.0 von Azure AD Connect vorhanden ist, sowie einige andere Fehlerbehebungen.

### <a name="release-status"></a>Releasestatus
14.09.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar.

### <a name="bug-fixes"></a>Behebung von Programmfehlern

 - Es wurde ein Sicherheitsproblem behoben, bei dem ein Pfad ohne Anquote verwendet wurde, um auf den Azure AD Connect-Dienst zu verweisen. Dieser Pfad ist jetzt ein Pfad in Anführungszeichen.
 - Beheben eines Importkonfigurationsproblems, bei dem das Rückschreiben aktiviert ist, wenn das vorhandene AD Connector-Konto verwendet wird.
 - Wir haben ein Problem in Set-ADSyncExchangeHybridPermissions und anderen verwandten Cmdlets behoben, die aufgrund eines ungültigen Vererbungstyps von 1.6 an fehlerhaft waren.
 - Das Cmdlet, das wir in einer früheren Version veröffentlicht haben, um die TLS-Version festzulegen, hatte ein Problem, bei dem es die Schlüssel überschreibt und alle darin enthaltenen Werte zerstörte. Wir haben dies behoben, indem nur dann ein neuer Schlüssel erstellt wird, wenn noch keiner vorhanden ist. Außerdem wird eine Warnung hinzugefügt, um Benutzer darüber zu informieren, dass Änderungen an der TLS-Registrierung nicht ausschließlich für Azure AD Connect gelten und sich auch auf andere Anwendungen auf demselben Server auswirken können.
 - Wir haben eine Überprüfung hinzugefügt, um ein automatisches Upgrade für V2.0 zu erzwingen, damit Windows Server 2016 oder höher erforderlich ist.
 - Im Cmdlet „Set-ADSyncBasicReadPermissions“ wurde die Berechtigung „Replicating Directory Changes“ (Verzeichnisänderungen replizieren) hinzugefügt.
 - Wir haben eine Änderung vorgenommen, um zu verhindern, dass UseExistingDatabase und die Importkonfiguration zusammen verwendet werden, da diese möglicherweise in Konflikt stehende Konfigurationseinstellungen enthalten.
 - Wir haben eine Änderung vorgenommen, damit ein Benutzer mit der Rolle Anwendungsadministrator die Konfiguration des App-Proxydiensts ändern kann.
 - Wir haben das Etikett „(Preview)“ aus den Etiketten der Import/Export-Einstellungen entfernt. Diese Funktion ist seit einiger Zeit allgemein verfügbar.
 - Wir haben einige Etikettem geändert, die immer noch auf „Unternehmensadministrator“ verwiesen. Wir verwenden jetzt den Rollennamen "Globaler Administrator".
 - Wir haben die neuen AAD Kerberos PowerShell-Cmdlets „\*-AADKerberosServer“ erstellt, um dem AAD-Dienstprinzipal eine Anspruchstransformationsregel hinzuzufügen.

### <a name="functional-changes"></a>Funktionale Änderungen
 - Wir haben die neuesten Versionen von MIM Connectors (1.1.1610.0) hinzugefügt. Weitere Informationen finden Sie auf der Seite [Versionsgeschichte der MiM-Connectors](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)
 - Wir haben eine Konfigurationsoption hinzugefügt, um das Feature „Soft Matching“ in Azure AD Connect zu deaktivieren. Wir empfehlen Kunden, das Soft Matching zu deaktivieren, es sei denn, sie benötigen es, um reine Cloudkonten zu übernehmen. In diesem [Artikel](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant) wird gezeigt, wie Sie das Soft Matching deaktivieren.

## <a name="20100"></a>2.0.10.0

### <a name="release-status"></a>Releasestatus
19.08.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar.

>[!NOTE] 
>Dies ist ein Hotfix-Updaterelease von Azure AD Connect. Für dieses Release ist Windows Server 2016 oder höher erforderlich. Dieser Hotfix behebt ein Problem, das es sowohl in Version 2.0 als auch in Azure AD Connect, Version  1.6, gibt. Wenn Sie Azure AD Connect auf einer älteren Windows Server-Version ausführen, sollten Sie stattdessen Build [1.6.13.0](#16130) installieren.

### <a name="release-status"></a>Releasestatus
19.08.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar.

### <a name="known-issues"></a>Bekannte Probleme
 - Unter bestimmten Umständen zeigt das Installationsprogramm für diese Version einen Fehler an, der darauf hinweist, dass TLS 1.2 nicht aktiviert ist, und beendet die Installation. Dies ist auf einen Fehler im Code zurückzuführen, der die Registrierungseinstellung für TLS 1.2 überprüft. Diesen Feher werden wir in einem zukünftigen Release beheben. Kunden, denen dieses Problem angezeigt wird, sollten die Anweisungen zum Aktivieren von TLS 1.2 befolgen, die sie im Artikel „[Erzwingung von TLS 1.2 für Azure AD Connect](reference-connect-tls-enforcement.md)“ finden.

### <a name="bug-fixes"></a>Behebung von Programmfehlern

 - Wir haben einen Fehler behoben, bei dem die Kennworthashsynchronisierung beim Umbenennen einer Domäne fehlschlägt und im Ereignisprotokoll die Meldung „Eine angegebene Umwandlung ist ungültig“ steht. Dies ist eine Regression aus früheren Builds.

## <a name="16130"></a>1.6.13.0
>[!NOTE] 
>Dies ist ein Hotfix-Updaterelease von Azure AD Connect. Dieses Release ist für Kunden vorgesehen, die Azure AD Connect auf einem Server mit Windows Server 2012 oder 2012 R2 ausführen.

19.08.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar.

### <a name="bug-fixes"></a>Behebung von Programmfehlern

 - Wir haben einen Fehler behoben, bei dem die Kennworthashsynchronisierung beim Umbenennen einer Domäne fehlschlägt und im Ereignisprotokoll die Meldung „Eine angegebene Umwandlung ist ungültig“ steht. Dies ist eine Regression aus früheren Builds.

### <a name="functional-changes"></a>Funktionale Änderungen
Dieses Release enthält keine funktionalen Änderungen.

## <a name="2090"></a>2.0.9.0

### <a name="release-status"></a>Releasestatus
17.08.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar.

### <a name="bug-fixes"></a>Behebung von Programmfehlern
>[!NOTE] 
>Dies ist ein Hotfix-Updaterelease von Azure AD Connect. Für dieses Release ist Windows Server 2016 oder höher erforderlich. Dieses Release behebt ein Problem, das es in Version 2.0.8.0 gibt. In Azure AD Connect, Version 1.6, gibt es dieses Problem nicht.

 - Wir haben einen Fehler behoben, bei dem beim Synchronisieren einer großen Anzahl von Kennworthashsynchronisierungs-Transaktionen die Länge des Ereignisprotokolleintrags die maximal zulässige Länge für einen Ereigniseintrag zur Kennworthashsynchronisierung überschreiten würde. Der lange Protokolleintrag wird jetzt in mehrere Einträge aufgeteilt.

## <a name="2080"></a>2.0.8.0
>[!NOTE] 
>Dies ist ein Sicherheitsupdaterelease von Azure AD Connect. Für dieses Release ist Windows Server 2016 oder höher erforderlich. Wenn Sie mit einer älteren Windows Server-Version arbeiten, verwenden Sie bitte [Version 1.6.11.3](#16113).
>Mit diesem Release soll – wie in [diesem CVE-Bericht](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) dokumentiert – ein Sicherheitsrisiko behoben werden. Weitere Informationen zu diesem Sicherheitsrisiko finden Sie im CVE-Bericht.
>Sie können die neueste Version von Azure AD Connect 2.0 über [diesen Link](https://www.microsoft.com/en-us/download/details.aspx?id=47594) herunterladen.

### <a name="release-status"></a>Releasestatus
10.08.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar. 

### <a name="functional-changes"></a>Funktionale Änderungen
Dieses Release enthält keine funktionalen Änderungen.

## <a name="16113"></a>1.6.11.3 
>[!NOTE] 
>Dies ist ein Sicherheitsupdaterelease von Azure AD Connect. Diese Version soll von Kunden verwendet werden, die eine ältere Windows Server-Version ausführen und ihren Server zurzeit nicht auf Windows Server 2016 oder höher aktualisieren können. Sie können mit dieser Version keinen Azure AD Connect V2.0-Server aktualisieren.
>Mit diesem Release soll – wie in [diesem CVE-Bericht](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) dokumentiert – ein Sicherheitsrisiko behoben werden. Weitere Informationen zu diesem Sicherheitsrisiko finden Sie im CVE-Bericht.
>Sie können die neueste Version von Azure AD Connect 1.6 über [diesen Link](https://www.microsoft.com/download/details.aspx?id=103336) herunterladen.

### <a name="release-status"></a>Releasestatus
10.08.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar.

### <a name="functional-changes"></a>Funktionale Änderungen
Dieses Release enthält keine funktionalen Änderungen.

## <a name="2030"></a>2.0.3.0
>[!NOTE] 
>Dies ist ein Hauptrelease von Azure AD Connect. Weitere Details finden Sie im Artikel zu [Azure Active Directory V2.0](whatis-azure-ad-connect-v2.md).

### <a name="release-status"></a>Releasestatus
20.07.2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar
### <a name="functional-changes"></a>Funktionale Änderungen
 - Wir haben die LocalDB-Komponenten von SQL Server auf SQL 2019 aktualisiert. 
 - Aufgrund der Anforderungen von SQL Server 2019 erfordert diese Version Windows Server 2016 oder höher. Beachten Sie: Weil ein direktes Upgrade von Windows Server auf einem Azure AD Connect-Server nicht unterstützt wird, müssen Sie möglicherweise eine [Swing-Migration](how-to-upgrade-previous-version.md#swing-migration) durchführen.
 -    In dieser Version wird die Verwendung von TLS 1.2 erzwungen. Wenn Sie Ihren Windows-Server für TLS 1.2 aktiviert haben, verwendet AADConnect dieses Protokoll. Wenn TLS 1.2 auf dem Server nicht aktiviert wurde, wird beim Versuch einer AADConnect-Installation eine Fehlermeldung angezeigt, und die Installation wird erst dann fortgesetzt, wenn Sie TLS 1.2 aktiviert haben. Beachten Sie, dass Sie TLS 1.2 mithilfe der neuen Cmdlets „Set-ADSyncToolsTls12“ auf Ihrem Server aktivieren können.
 -    Mit diesem Release können Sie sich bei der Installation von Azure AD Connect als Benutzer*in mit der Benutzerrolle „Hybrididentitätsadministrator“ authentifizieren. Sie benötigen dafür nicht mehr die Rolle „Globaler Administrator“.
 - Als Voraussetzung für SQL Server 2019 haben wir die Visual C++-Laufzeitbibliothek auf Version 14 aktualisiert.    
 -    In diesem Release wird die MSAL-Bibliothek zur Authentifizierung verwendet, und wir haben die ältere ADAL-Bibliothek entfernt, die 2022 eingestellt wird.    
 -    Entsprechend dem Windows-Sicherheitsleitfaden wenden wir für die AdminSDHolders keine Berechtigungen mehr an. Wir haben im Modul „ADSyncConfig.psm1“ den Parameter „SkipAdminSdHolders“ in „IncludeAdminSdHolders“ geändert.
 -    Kennwörter werden jetzt neu ausgewertet, wenn ein abgelaufenes Kennwort „nicht abgelaufen“ ist. Dies geschieht unabhängig davon, ob das Kennwort selbst geändert wird. Wenn für Benutzer*innen das Kennwort auf „Muss Kennwort bei der nächsten Anmeldung ändern“ festgelegt wurde und dieses Flag gelöscht wird (wodurch das abgelaufene Kennwort „wiederhergestellt“ wird), werden der Status „Nicht abgelaufen“ und der Kennworthash mit Azure AD synchronisiert. Versuchen die Benutzer*innen dann, sich bei Azure AD anzumelden, können sie das nicht abgelaufene Kennwort verwenden.
Verwenden Sie zum Synchronisieren eines abgelaufenen Kennworts von Active Directory mit Azure Active Directory das Feature [Synchronisieren von temporären Kennwörtern](how-to-connect-password-hash-synchronization.md#synchronizing-temporary-passwords-and-force-password-change-on-next-logon) in Azure AD Connect. Beachten Sie: Sie müssen das Kennwortrückschreiben aktivieren, um dieses Feature nutzen zu können, damit das erforderliche Kennwort für Updates ebenfalls in Active Directory zurückgeschrieben wird.
 - Wir haben dem Modul „ADSyncTools“ zwei neue Cmdlets hinzugefügt, um TLS 1.2-Einstellungen zu aktivieren oder vom Windows-Server abzurufen. 
   - Get-ADSyncToolsTls12
   - Set-ADSyncToolsTls12
   
Mithilfe dieser Cmdlets können Sie den TLS 1.2-Aktivierungsstatus abrufen oder nach Bedarf festlegen. Beachten Sie, dass TLS 1.2 auf dem Server aktiviert sein muss, damit die Installation oder AADConnect erfolgreich ist.

 -    Wir haben ADSyncTools mit mehreren neuen und verbesserten Cmdlets überarbeitet. Im Artikel [ADSyncTools](reference-connect-adsynctools.md) finden Sie weitere Informationen zu diesen Cmdlets. 
  Die folgenden Cmdlets wurden hinzugefügt oder aktualisiert: 
    - Clear-ADSyncToolsMsDsConsistencyGuid    
    - ConvertFrom-ADSyncToolsAadDistinguishedName    
    - ConvertFrom-ADSyncToolsImmutableID    
    - ConvertTo-ADSyncToolsAadDistinguishedName    
    - ConvertTo-ADSyncToolsCloudAnchor    
    - ConvertTo-ADSyncToolsImmutableID    
    - Export-ADSyncToolsAadDisconnectors    
    - Export-ADSyncToolsObjects    
    - Export-ADSyncToolsRunHistory    
    - Get-ADSyncToolsAadObject    
    - Get-ADSyncToolsMsDsConsistencyGuid    
    - Import-ADSyncToolsObjects    
    - Import-ADSyncToolsRunHistory    
    - Remove-ADSyncToolsAadObject    
    - Search-ADSyncToolsADobject    
    - Set-ADSyncToolsMsDsConsistencyGuid    
    - Trace-ADSyncToolsADImport    
    - Trace-ADSyncToolsLdapQuery    
-    Wir verwenden jetzt den V2-Endpunkt für Import und Export und haben das Problem im Cmdlet „Get-ADSyncAADConnectorExportApiVersion“ behoben. Weitere Informationen zum V2-Endpunkt finden Sie im Artikel [Azure AD Connect-Synchronisierungsendpunkt V2](how-to-connect-sync-endpoint-api-v2.md).
-    Wir haben die folgenden neuen Benutzereigenschaften für eine Synchronisierung vom lokalen AD mit Azure AD hinzugefügt:    
    - employeeType    
    - employeeHireDate    
-    Für diese Version muss PowerShell, Version 5.0 oder höher, auf dem Windows-Server installiert sein. Beachten Sie, dass diese Version Teil von Windows Server 2016 und höher ist.    
-    Wir haben die Limits für die Gruppensynchronisierungsmitgliedschaft beim neuen V2-Endpunkt auf 250.000 erhöht.
-    Wir haben den generischen LDAP-Connector und den generischen SQL-Connector auf die neuesten Versionen aktualisiert. Hier finden Sie weitere Informationen zu diesen Connectors:
    - [Generischer LDAP-Connector – Referenzdokumentation](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap)
    - [Generischer SQL-Connector – Referenzdokumentation](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)
-    Im M365 Admin Center melden wir jetzt die AADConnect-Clientversion, wenn es Exportaktivitäten an Azure AD gibt. Dadurch wird sichergestellt, dass das M365 Admin Center immer über die aktuellste AADConnect-Clientversion verfügt und erkennen kann, wenn Sie eine veraltete Version verwenden.

### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Wir haben einen Barrierefreiheitsfehler behoben, bei dem die Sprachausgabe eine falsche Rolle des Links ‚Weitere Informationen‘ ankündigt.
-    Wir haben einen Fehler behoben, bei dem Synchronisierungsregeln mit großen Rangfolgenwerten (d. h. 387163089) zum Fehlschlagen eines Updates führten. Wir haben die gespeicherte Prozedur „mms_UpdateSyncRulePrecedence“ aktualisiert, um die Rangfolgenummer vor dem Erhöhen des Werts in eine ganze Zahl zu umzuwandeln.
-    Wir haben einen Fehler behoben, bei dem Berechtigungen für Gruppenrückschreiben im Synchronisierungskonto nicht festgelegt werden, wenn eine Konfiguration für das Gruppenrückschreiben importiert wird. Wir legen die Berechtigungen für Gruppenrückschreiben jetzt fest, wenn das Gruppenrückschreiben für die importierte Konfiguration aktiviert wird.
-    Wir haben die Agent-Version von Azure AD Connect Health auf 3.1.110.0 aktualisiert, um einen Installationsfehler zu beheben.
-    Es tritt ein Problem bei nicht standardmäßigen Attributen aus exportierten Konfigurationen auf, bei denen Verzeichniserweiterungsattribute konfiguriert werden. Beim Importieren dieser Konfigurationen auf einen neuen Server bzw. in eine neue Installation wird die Attributaufnahmeliste durch den Konfigurationsschritt der Verzeichniserweiterung außer Kraft gesetzt. Nach dem Import werden also nur Standard- und Verzeichniserweiterungsattribute im Synchronisierungsdienst-Manager ausgewählt (nicht standardmäßige Attribute werden in die Installation nicht einbezogen, sodass der Benutzer sie aus dem Synchronisierungsdienst-Manager manuell erneut aktivieren muss, wenn die importierten Synchronisierungsregeln funktionieren sollen). Wir aktualisieren jetzt den AAD Connector, bevor wir die Verzeichniserweiterung konfigurieren, um vorhandene Attribute aus der Aufnahmeliste für Attribute beizubehalten.
-    Wir haben ein Barrierefreiheitsproblem behoben, bei dem die Schriftbreite des Seitenkopfs auf „Light“ (Schmal) festgelegt wurde. Die Schriftbreite für den Seitentitel ist jetzt auf „Fett“ festgelegt, und dies gilt für die Kopfzeile aller Seiten.
-    Die Funktion „Get-AdObject“ in „ADSyncSingleObjectSync.ps1“ wurde in „Get-AdDirectoryObject“ umbenannt, um eine Mehrdeutigkeit mit dem AD-Cmdlet zu verhindern.
-    Die SQL-Funktion „mms_CheckSynchronizationRuleHasUniquePrecedence“ lässt eine Rangfolge von Duplikaten bei ausgehenden Synchronisierungsregeln auf verschiedenen Connectors zu. Wir haben die Bedingung entfernt, die eine Rangfolge doppelter Regeln zulässt.
-    Wir haben einen Fehler behoben, bei dem das Cmdlet „Single Object Sync“ fehlschlägt, wenn die Attributflussdaten „null“ sind, d. h. beim Exportieren des Löschvorgangs.    
-    Es wurde ein Fehler behoben, bei dem die Installation fehlschlägt, weil der ADSync-Bootstrapdienst nicht gestartet werden kann. Wir fügen jetzt das Synchronisierungsdienstkonto der lokalen integrierten Benutzergruppe hinzu, bevor der den Bootstrapdienst gestartet wird.
-    Wir haben ein Barrierefreiheitsproblem behoben, bei dem die aktive Registerkarte im AAD Connect-Assistenten nicht die richtige Farbe für das Design „Hoher Kontrast“ anzeigt. Aufgrund einer fehlenden Bedingung in der normalen Farbcodekonfiguration wurde der ausgewählte Farbcode außer Kraft gesetzt.
-    Wir haben ein Problem behoben, bei dem Benutzer die Auswahl von Objekten und Attributen, die in Synchronisierungsregeln verwendet werden, über die Benutzeroberfläche und mithilfe von PowerShell aufheben durften. Wir zeigen jetzt eine kurze Fehlermeldung an, wenn Sie versuchen, die Auswahl eines Attributs oder Objekts aufzuheben, das in Synchronisierungsregeln verwendet wird.
-    Es wurden einige Updates am „Migrationseinstellungscode“ vorgenommen, um Probleme bei der Abwärtskompatibilität zu überprüfen und zu beheben, wenn das Skript in einer älteren Version von Azure AD Connect ausgeführt wird.    
-    Es wurde folgender Fehler behoben: Wenn die Kennworthashsynchronisierung (Password Hash Sync, PHS) versucht, nach einem unvollständigen Objekt zu suchen, verwendet sie zum Auflösen des Domänencontrollers nicht denselben Algorithmus wie den ursprünglich zum Abrufen der Kennwörter verwendeten. Insbesondere werden zugeordnete Informationen zum Domänencontroller ignoriert. Die „Unvollständige Objektsuche“ sollte dieselbe Logik zum Suchen des Domänencontrollers in beiden Instanzen verwenden.
-    Wir haben einen Fehler behoben, bei dem AADConnect Anwendungsproxyelemente mithilfe von Microsoft Graph nicht lesen kann. Der Grund dafür ist ein Berechtigungsproblem beim Aufrufen von Microsoft Graph direkt basierend auf der AAD Connect-Client-ID. Zur Behebung dieses Problems haben wir die Abhängigkeit von Microsoft Graph entfernt. Verwenden Sie stattdessen AAD PowerShell zum Arbeiten mit den Proxyanwendungsobjekten.
-    Wir haben das Rückschreibelimit für Mitglieder aus der Synchronisierungsregel „Ausgehend an AD – Gruppe SOAInAAD: Exchange“ entfernt.    
-    Wir haben folgenden Fehler behoben: Wenn es beim Ändern von Connector-Kontoberechtigungen um ein Objekt geht, das seit dem letzten Deltaimport nicht geändert wurde, wird es mit einem Deltaimport nicht importiert. Jetzt wird eine Warnung angezeigt, die den Benutzer auf das Problem aufmerksam macht.
-    Wir haben ein Barrierefreiheitsproblem behoben, bei dem die Sprachausgabe die Position des Optionsfelds nicht vorliest. Wir haben im Textfeld zum Optionsfeld „Barrierefreiheit“ Positionstext hinzugefügt.
-    Wir haben das Paket „Pass-Through-Authentifizierungs-Agent“ aktualisiert. Das ältere Paket enthielt nicht die richtige Antwort-URL für die Erstanbieteranwendung von HIP in „US Gov“.    
-    Folgende Fehler wurden behoben: Nach einer Neuinstallation von AADConnect, Version 1.6.X.X, gibt es beim AAD-Connectorexport eine Ausnahme vom Typ „stopped-extension-dll-exception“. Dadurch wird standardmäßig – unter Verwendung einer vorhandenen Datenbank – DirSyncWebServices API V2 verwendet.    Zuvor erfolgte das Festlegen der Exportversion auf v2 nur beim Upgrade. Wir haben dies so geändert, dass sie auch bei einer Neuinstallation festgelegt wird.
-    Das Modul „ADSyncPrep.psm1“ wird nicht mehr verwendet und wurde aus der Installation entfernt.

### <a name="known-issues"></a>Bekannte Probleme
-    Der AADConnect-Assistent zeigt die Option „Synchronisierungseinstellungen importieren“ als „Vorschau“ an, obwohl dieses Feature allgemein verfügbar ist.
-    Einige Active Directory-Connectors werden möglicherweise in einer anderen Reihenfolge installiert, wenn die Ausgabe des Migrationseinstellungsskripts zum Installieren des Produkts verwendet wird. 
-    Im Azure AD Connect-Assistenten wird auf der Seite „Optionen für die Benutzeranmeldung“ der Begriff „Unternehmensadministrator“ erwähnt. Dieser Begriff wird nicht mehr verwendet und muss durch „Globaler Administrator“ ersetzt werden.
-    Die Option „Exporteinstellungen“ ist fehlerhaft, wenn die Anmeldeoption zur Verwendung von PingFederate konfiguriert wurde.
-    Obwohl Azure AD Connect jetzt mithilfe der Rolle „Hybrididentitätsadministrator“ bereitgestellt werden kann, erfordert die Konfiguration der Self-Service-Kennwortrücksetzung weiterhin einen Benutzer mit der Rolle „Globaler Administrator“.
-    Beim Importieren der AADConnect-Konfiguration während der Bereitstellung, um eine Verbindung mit einem anderen Mandanten als der ursprünglichen AADConnect-Konfiguration herzustellen, werden die Verzeichniserweiterungsattribute nicht richtig konfiguriert.

## <a name="1640"></a>`1.6.4.0

>[!NOTE]
> Die V2-Endpunkt-API für die Azure AD Connect-Synchronisierung ist jetzt in den folgenden Azure-Umgebungen verfügbar:
> - Azure Commercial
> - Azure China
> - (Azure-Cloud für US-Behörden)
> - Dieses Release wird in der Azure German Cloud nicht zur Verfügung gestellt.

### <a name="release-status"></a>Releasestatus
3/31/2021: Nur zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar

### <a name="bug-fixes"></a>Behebung von Programmfehlern
- Diese Version behebt einen Fehler in Version 1.6.2.4, bei dem nach einem Upgrade auf diese Version die Azure AD Connect Health-Funktion nicht korrekt registriert wurde und nicht funktionierte. Kunden, die Build 1.6.2.4 bereitgestellt haben, werden gebeten, ihren Azure AD Connect-Server mit diesem Build zu aktualisieren, wobei die Health-Funktion korrekt registriert wird. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Aktualisierung gemäß dem 30. März 2021: Es wurde ein Problem in diesem Build festgestellt. Nach der Installation dieses Builds sind die Integritäts-Dienste nicht registriert. Wir empfehlen, diesen Build nicht zu installieren. Wir werden einen Hotfix in Kürze veröffentlichen.
> Wenn Sie dieses Build bereits installiert haben, können Sie die Health-Dienste manuell registrieren, indem Sie das Cmdlet wie in [diesem Artikel](./how-to-connect-health-agent-install.md#manually-register-azure-ad-connect-health-for-sync) gezeigt verwenden

>[!NOTE]
> - Dieses Release wird nur zum Download zur Verfügung gestellt.
> - Das Upgrade auf dieses Release erfordert aufgrund von Änderungen der Synchronisierungsregeln eine vollständige Synchronisierung.
> - Dieses Release verwendet standardmäßig den AADConnect -Server zum neuen V2-Endpunkt.

### <a name="release-status"></a>Releasestatus
3/19/2021: Zum Download freigegeben, nicht für die Auto-Aktualisierung verfügbar

### <a name="functional-changes"></a>Funktionale Änderungen

 - Die standardmäßigen Synchronisierungsregeln wurden aktualisiert, um die Mitgliedschaft in zurückgeschriebenen Gruppen auf 50.000 Mitglieder einzuschränken.
   - Es wurden neue Standardsynchronisierungsregeln zum Begrenzen der Anzahl der Mitgliedschaften beim Gruppenrückschreiben (Ausgehend an AD – Mitgliederbeschränkung für Gruppenrückschreiben) und Gruppensynchronisierung mit Azure Active Directory (Ausgehend an AAD – Mitgliederbeschränkung für Gruppenhochschreiben) hinzugefügt.
   - Ein Mitgliedsattribut wurde der Regel „Ausgehend an AD: Gruppe SOAInAAD – Exchange“ hinzugefügt, um die Mitglieder in zurückgeschriebenen Gruppen auf 50.000 einzuschränken.
 - Die Synchronisierungsregeln wurden aktualisiert, um das Gruppenrückschreiben V2 zu unterstützen. - Wenn die Regel „Eingehend aus AAD – Gruppe SOAInAAD“ geklont und AADConnect aktualisiert wird.
     - Die aktualisierte Regel wird standardmäßig deaktiviert, sodass targetWritebackType NULL ist.
     - AADConnect schreibt alle Cloudgruppen (einschließlich für das Rückschreiben aktivierter Azure Active Directory-Sicherheitsgruppen) als Verteilergruppen zurück.
   \- Wenn die Regel „Ausgehend an AD – Gruppe SOAInAAD“ geklont und AADConnect aktualisiert wird.
     - Die aktualisierte Regel wird standardmäßig deaktiviert. Allerdings wird eine Synchronisierungsregel aktiviert, die neu hinzugefügt wird: „Ausgehend an AD – Gruppe SOAInAAD: Exchange“.
     - Abhängig von der Rangfolge der geklonten benutzerdefinierten Synchronisierungsregel leitet AADConnect die Mail- und Exchange-Attribute weiter.
     - Wenn die geklonte benutzerdefinierte Synchronisierungsregel einige Mail- und Exchange-Attribute nicht weiterleitet, werden diese Attribute von der neuen Exchange-Synchronisierungsregel hinzugefügt.
     - Beachten Sie, dass sich Group Writeback V2 derzeit in der privaten Vorschau befindet und nicht öffentlich verfügbar ist.
 - Unterstützung für das [Konfigurieren der selektiven Kennworthashsynchronisierung für Azure AD Connect](./how-to-connect-selective-password-hash-synchronization.md) wurde hinzugefügt.
 - Das neue [Cmdlet Single Object Sync](./how-to-connect-single-object-sync.md) wurde hinzugefügt. Behandeln Sie mit diesem Cmdlet Probleme mit der Konfiguration der Azure AD Connect-Synchronisierung. 
 -  Azure AD Connect unterstützt jetzt die Rolle „Hybrididentitätsadministrator“ zum Konfigurieren des Diensts.
 - AADConnectHealth-Agent wurde auf 3.1.83.0 aktualisiert.
 - Neue Version des [ADSyncTools-PowerShell-Moduls](./reference-connect-adsynctools.md) mit mehreren neuen oder verbesserten Cmdlets. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Fehlerprotokollierung für Tokenerwerbsfehler wurde aktualisiert.
 - Links zu „Weiteren Informationen“ auf der Konfigurationsseite wurden aktualisiert, um weitere Details zu den verknüpften Informationen zu liefern.
 - Die Explicit-Spalte wurde in der alten Synchronisierungsbenutzeroberfläche von der CS-Suchseite entfernt.
 - Dem Gruppenrückschreiben-Flow wurde eine zusätzliche Benutzeroberfläche hinzugefügt, um den Benutzer zur Eingabe von Anmeldeinformationen oder zum Konfigurieren seiner eigenen Berechtigungen mithilfe des ADSyncConfig-Moduls aufzufordern, wenn in einem früheren Schritt noch keine Anmeldeinformationen bereitgestellt wurden.
 - MSA für das ADSync-Dienstkonto wird auf einem Domänencontroller automatisch erstellt. 
 - Es gibt jetzt die Möglichkeit, das Azure Active Directory DirSync-Feature Gruppenrückschreiben V2 in den vorhandenen Cmdlets festzulegen und abzurufen:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - 2 Cmdlets zum Lesen der AWS-API-Version wurden hinzugefügt.
    - Get-ADSyncAADConnectorImportApiVersion zum Abrufen der Import-AWS-API-Version
    - Get-ADSyncAADConnectorExportApiVersion zum Abrufen der Export-AWS-API-Version

 - Änderungen an Synchronisierungsregeln werden nun nachverfolgt, um die Problembehandlung bei Änderungen im Dienst zu unterstützen. Mit dem Cmdlet „Get-ADSyncRuleAudit“ werden nachverfolgte Änderungen abgerufen.
 - Das Cmdlet „Add-ADSyncADDSConnectorAccount“ im [Modul „ADSyncConfig PowerShell“](./how-to-connect-configure-ad-ds-connector-account.md#using-the-adsyncconfig-powershell-module) wurde aktualisiert, damit ein Benutzer in der Gruppe „ADSyncAdmin“ das AD DS Connector-Konto ändern kann. 

### <a name="bug-fixes"></a>Behebung von Programmfehlern
 - Deaktivierte Vordergrundfarbe wurde aktualisiert, um die Anforderungen an die Helligkeit auf weißem Hintergrund zu erfüllen. Zusätzliche Bedingungen für die Navigationsstruktur wurden hinzugefügt, um die Vordergrundtextfarbe auf Weiß festzulegen, wenn eine deaktivierte Seite ausgewählt wird, um die Helligkeitsanforderungen zu erfüllen.
 - Die Granularität für das Cmdlet „Set-ADSyncPasswordHashSyncPermissions“ wurde erhöht. – Das PHS-Berechtigungsskript (Set-ADSyncPasswordHashSyncPermissions) wurde aktualisiert, um einen optionalen Parameter „ADobjectDN“ einzuschließen. 
 - Fehlerbehebung für Barrierefreiheit. Die Sprachausgabe beschreibt nun das Benutzeroberflächenelement, das die Liste der Gesamtstrukturen enthält, als **Gesamtstrukturenliste** anstelle von **Liste Gesamtstrukturenliste**.
 - Die Sprachausgabe wurde für einige Elemente im Assistenten für Azure AD Connect aktualisiert. Die Farbe für das Zeigen auf eine Schaltfläche wurde aktualisiert, um Kontrastanforderungen zu erfüllen. Die Titelfarbe des Synchronisierungs-Service Manager wurde aktualisiert, um Kontrastanforderungen zu erfüllen.
 - Es wurde ein Problem bei der Installation von AADConnect aus einer exportierten Konfiguration mit benutzerdefinierten Erweiterungsattributen behoben. – Eine Bedingung wurde hinzugefügt, um die Überprüfung auf Erweiterungsattribute im Zielschema beim Anwenden der Synchronisierungsregel zu überspringen.
 - Die entsprechenden Berechtigungen werden bei der Installation hinzugefügt, wenn das Gruppenrückschreiben-Feature aktiviert ist.
 - Doppelte standardmäßige Synchronisierungsregel-Rangfolge beim Importieren wurde behoben.
 - Es wurde ein Problem behoben, das einen Stagingfehler beim Deltaimport der V2-API für ein in Konflikt stehendes Objekt verursacht hat, das über das Integritätsportal repariert wurde.
 - Es wurde ein Problem im Synchronisierungsmodul behoben, das dazu geführt hat, dass CS-Objekte einen inkonsistenten Linkstatus haben.
 - Importzähler wurden der Get-ADSyncConnectorStatistics-Ausgabe hinzugefügt.
 - Es wurde das Problem behoben, dass in Ausnahmefällen während Ausführung des pass2-Assistenten die vorherige Auswahl einer nicht erreichbaren Domäne aufgehoben wurde.
 - Dass bei Import und Export einer Richtlinie Fehler auftraten, wenn die benutzerdefinierte Regel eine doppelte Rangfolge aufwies, wurde geändert. 
 - Ein Fehler in der Logik der Domänenauswahl wurde behoben.
 - Es wurde ein Problem mit Build 1.5.18.0 behoben, das bei Verwendung von mS-DS-ConsistencyGuid als Quellanker und Klonen der Regel „Eingehend aus AD – Gruppenverknüpfung“ auftrat.
 - Bei einer Neuinstallation von AADConnect wird der in der Cloud gespeicherte Exportlöschschwellenwert verwendet, sofern er verfügbar ist und kein anderer übergeben wird.
 - Es wurde das Problem behoben, das AADConnect keine AD-displayName-Änderungen von eingebundenen Hybridgeräten las.

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Releasestatus
29.07.2020: Für den Download veröffentlicht

### <a name="functional-changes"></a>Funktionale Änderungen
Dies ist ein Release zur Fehlerbehebung. Dieses Release enthält keine funktionalen Änderungen.

### <a name="fixed-issues"></a>Behobene Probleme

- Es wurde ein Problem behoben, durch das Administrator*innen „Nahtloses einmaliges Anmelden“ nicht aktivieren konnten, wenn das Computerkonto AZUREADSSOACC bereits in „Active Directory“ vorhanden war.
- Es wurde ein Problem behoben, das einen Stagingfehler beim Deltaimport der V2-API für ein in Konflikt stehendes Objekt verursacht hat, das über das Integritätsportal repariert wurde.
- Es wurde ein Problem in der Import-/Exportkonfiguration behoben, durch das die deaktivierte benutzerdefinierte Regel als aktiviert importiert wurde.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
