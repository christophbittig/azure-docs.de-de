---
title: 'Azure AD Connect: Automatisches Upgrade | Microsoft-Dokumentation'
description: Dieses Thema beschreibt das integrierte automatische Upgradefeature in der Azure AD Connect-Synchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/11/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a3cf21ef2493ef6a93397e6d6601e326d0ef0d3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122350972"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatische Upgrade
Das automatische Upgrade von Azure AD Connect ist ein Feature, das regelmäßig überprüft, ob neuere Versionen von Azure AD Connect vorhanden sind. Wenn Ihr Server für automatische Upgrades aktiviert ist und eine neuere Version gefunden wird, für die der Server berechtigt ist, wird ein automatisches Upgrade auf diese neuere Version durchgeführt.
Aus Sicherheitsgründen überprüft der Agent, der das automatische Upgrade ausführt, den neuen Build von Azure AD Connect anhand der digitalen Signatur der heruntergeladenen Version.

## <a name="overview"></a>Übersicht
Noch nie konnten Sie so leicht sicherstellen, dass Ihre Azure AD Connect-Installation immer auf dem neuesten Stand ist  – mit dem **automatischen Upgradefeature** . Dieses Feature ist standardmäßig für Express-Installationen und DirSync-Upgrades aktiviert. Wenn eine neue Version veröffentlicht wird, erfolgt ein automatisches Upgrade Ihrer Installation.
Das automatische Upgrade ist in folgenden Fällen standardmäßig aktiviert:

* Installationen mit Express-Einstellungen und DirSync-Upgrades.
* Verwendung von SQL Express LocalDB (wird bei Express-Einstellungen immer verwendet). DirSync mit SQL Express verwendet ebenfalls LocalDB.
* Das AD-Konto ist das MSOL_-Standardkonto, das mit den Express-Einstellungen und DirSync erstellt wird
* Es sind weniger als 100.000 Objekte im Metaverse enthalten.

Der aktuelle Zustand des automatischen Upgrades kann über das PowerShell-Cmdlet `Get-ADSyncAutoUpgrade`angezeigt werden. Folgende Zustände sind möglich:

| State | Comment |
| --- | --- |
| Aktiviert |Das automatische Upgrade ist aktiviert. |
| Ausgesetzt |Nur vom System festgelegt. Das System ist **derzeit nicht** zum Erhalten automatischer Upgrades berechtigt. |
| Disabled |Das automatische Upgrade ist deaktiviert. |

Mit `Set-ADSyncAutoUpgrade` können Sie zwischen **aktiviert** und **deaktiviert** wechseln. Nur das System sollte den Zustand **Ausgesetzt** festlegen.  Vor 1.1.750.0 hat das Cmdlet „Set-ADSyncAutoUpgrade“ das automatische Upgrade blockiert, wenn der auto-upgrade-Status auf „Angehalten“ festgelegt wurde. Diese Funktion wurde geändert, sodass das automatische Upgrade nicht blockiert wird.

Das automatische Upgrade verwendet Azure AD Connect Health als Upgrade-Infrastruktur. Damit das automatische Upgrade funktioniert, müssen die URLs im Proxy für **Azure AD Connect Health** wie unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)beschrieben geöffnet sein.


Wenn auf dem Server die **Synchronization Service Manager** -Benutzeroberfläche geöffnet ist, wird das Upgrade angehalten, bis die Benutzeroberfläche geschlossen wird.

>[!NOTE]
> Nicht für alle Releases von Azure AD Connect wird das automatische Upgrade zur Verfügung gestellt. Aus dem Releasestatus geht hervor, ob für ein Release das automatische Upgrade oder nur der Download verfügbar ist. Wenn das automatische Upgrade auf Ihrem Azure AD Connect-Server aktiviert ist und **Ihre Konfiguration für das automatische Upgrade [berechtigt](#auto-upgrade-eligibility) ist**, wird dieser Server automatisch auf die neueste Version von Azure AD Connect aktualisiert, die für das automatische Upgrade veröffentlicht wird. Weitere Informationen zu diesem Update finden Sie unter [Azure AD Connect: Verlauf der Versionsveröffentlichungen](reference-connect-version-history.md).

## <a name="auto-upgrade-eligibility"></a>Berechtigung für das automatische Upgrade
Um für das automatisches Upgrade berechtigt zu sein, dürfen Sie keine der folgenden Bedingungen erfüllen:

| Ergebnismeldung | BESCHREIBUNG |
| --- | --- |
|UpgradeNotSupportedCustomizedSyncRules|Sie haben der Konfiguration eigene benutzerdefinierte Regeln hinzugefügt.|
|UpgradeNotSupportedInvalidPersistedState|Die Installation ist keine Express-Einstellung und kein DirSync-Upgrade.|
|UpgradeNotSupportedNonLocalDbInstall|Sie verwenden keine SQL Server Express LocalDB-Datenbank.|
|UpgradeNotSupportedLocalDbSizeExceeded|Lokale Datenbank ist größer als oder gleich 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|Uploads von Integritätsdaten wurden im Portal deaktiviert|



## <a name="troubleshooting"></a>Problembehandlung
Wenn das automatische Upgrade Ihrer Connect-Installation nicht wie erwartet funktioniert, befolgen Sie diese Schritte, um herauszufinden, wo der Fehler liegen könnte.

Ein automatisches Upgrade wird voraussichtlich nicht direkt am Erscheinungstag einer neuen Version versucht. Upgrades unterliegen einer beabsichtigten Zufälligkeit – Sie müssen sich also keine Sorgen machen, wenn Ihre Installation nicht sofort aktualisiert wird.

Wenn Sie der Meinung sind, dass ein Fehler vorliegt, führen Sie zuerst `Get-ADSyncAutoUpgrade` aus, um sicherzustellen, dass das automatische Upgrade aktiviert ist.

Wenn der Status „Angehalten“ lautet, können Sie den Grund mit dem Befehl `Get-ADSyncAutoUpgrade -Detail` anzeigen.  Der Grund für die Unterbrechung kann einen beliebigen Zeichenfolgenwert enthalten, enthält in der Regel aber den Zeichenfolgenwert von UpgradeResult, d. h. `UpgradeNotSupportedNonLocalDbInstall` oder `UpgradeAbortedAdSyncExeInUse`.  Es kann auch ein zusammengesetzter Wert zurückgegeben werden, z. B. `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed`.

Möglicherweise erhalten Sie auch ein Ergebnis, das kein UpgradeResult ist, d. h. „AADHealthEndpointNotDefined“ oder „DirSyncInPlaceUpgradeNonLocalDb“.

Stellen Sie dann sicher, dass Sie die erforderlichen URLs in Ihrem Proxy oder in der Firewall geöffnet haben. Die automatische Aktualisierung verwendet Azure AD Connect Health wie in der [Übersicht](#overview)beschrieben. Wenn Sie einen Proxy verwenden, muss Health für die Verwendung eines [Proxyservers](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)konfiguriert sein. Prüfen Sie auch die [Verbindung von Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) zu Azure AD.

Wenn Sie die Verbindung mit Azure AD überprüft haben, prüfen Sie die Ereignisprotokolle. Starten Sie die Ereignisanzeige, und sehen Sie sich das Ereignisprotokoll für die **Anwendung** an. Fügen Sie einen Ereignisprotokollfilter für die Quelle **Azure AD Connect Upgrade** und den Ereignis-ID-Bereich **300-399** hinzu.  
![Screenshot des Fensters „Aktuelles Protokoll filtern“, auf dem das Feld „Ereignisquellen“ sowie das Feld „Einschließen/ausschließen“ für die Event-ID hervorgehobenen werden](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

So können Sie alle Ereignisprotokolle anzeigen, die dem Status für das automatische Upgrade zugeordnet sind.  
![Ereignisprotokollfilter für das automatische Upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Der Ergebniscode hat ein Präfix mit einer Übersicht über den Status.

| Ergebniscodepräfix | BESCHREIBUNG |
| --- | --- |
| Erfolg |Das Upgrade der Installation wurde erfolgreich durchgeführt. |
| UpgradeAborted |Das Upgrade wurde durch ein temporäres Problem angehalten. Das Upgrade wird erneut ausgeführt, und es wird davon ausgegangen, dass es später erfolgreich durchgeführt werden kann. |
| UpgradeNotSupported |Die Konfiguration des Systems blockiert das automatische Upgrade. Das Upgrade wird erneut versucht, um zu ermitteln, ob sich der Status ändert. Es ist jedoch damit zu rechnen, dass das Upgrade für das System manuell durchgeführt werden muss. |

Im Folgenden finden Sie eine Liste der Meldungen, die am häufigsten angezeigt werden. Es werden nicht alle Meldungen aufgeführt, aber aus den Ergebnismeldungen wird das jeweilige Problem klar.

| Ergebnismeldung | BESCHREIBUNG |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Es konnte nicht in die Registrierung geschrieben werden. |
| UpgradeAbortedInsufficientDatabasePermissions |Die integrierte Administratorengruppe verfügt nicht über die erforderlichen Berechtigungen für die Datenbank. Führen Sie ein manuelles Upgrade auf die aktuelle Version von Azure AD Connect aus, um dieses Problem zu beheben. |
| UpgradeAbortedInsufficientDiskSpace |Es ist nicht genügend Speicherplatz vorhanden, um ein Upgrade zu unterstützen. |
| UpgradeAbortedSecurityGroupsNotPresent |Es konnten nicht alle Sicherheitsgruppen gefunden und aufgelöst werden, die vom Synchronisierungsmodul verwendet werden. |
| UpgradeAbortedServiceCanNotBeStarted |Der NT-Dienst **Microsoft Azure AD Sync** konnte nicht gestartet werden. |
| UpgradeAbortedServiceCanNotBeStopped |Der NT-Dienst **Microsoft Azure AD Sync** konnte nicht angehalten werden. |
| UpgradeAbortedServiceIsNotRunning |Der NT-Dienst **Microsoft Azure AD Sync** wird nicht ausgeführt. |
| UpgradeAbortedSyncCycleDisabled |Die SyncCycle-Option im [Scheduler](how-to-connect-sync-feature-scheduler.md) wurde deaktiviert. |
| UpgradeAbortedSyncExeInUse |Die [Synchronization Service Manager-Benutzeroberfläche](how-to-connect-sync-service-manager-ui.md) ist auf dem Server geöffnet. |
| UpgradeAbortedSyncOrConfigurationInProgress |Der Installations-Assistent wird ausgeführt, oder es wurde eine Synchronisierung außerhalb des Schedulers geplant. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |Sie haben der Konfiguration eigene benutzerdefinierte Regeln hinzugefügt. |
| UpgradeNotSupportedInvalidPersistedState |Die Installation ist keine Express-Einstellung und kein DirSync-Upgrade. |
| UpgradeNotSupportedNonLocalDbInstall |Sie verwenden keine SQL Server Express LocalDB-Datenbank. |
|UpgradeNotSupportedLocalDbSizeExceeded|Lokale Datenbank ist größer als oder gleich 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|Uploads von Integritätsdaten wurden im Portal deaktiviert|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
