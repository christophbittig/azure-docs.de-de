---
title: Diagnostizieren von Problemen mit Azure Virtual Desktop (klassisch) – Azure
description: Verwendung der Diagnosefunktion von Azure Virtual Desktop (klassisch) zum Diagnostizieren von Problemen
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 3a9d7f4f6c3413fe82cd1dde52ebde866d77eedf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262905"
---
# <a name="identify-and-diagnose-issues-in-azure-virtual-desktop-classic"></a>Identifizieren und Diagnostizieren von Problemen in Azure Virtual Desktop (klassisch)

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop (klassisch). Der Dienst unterstützt keine Azure Virtual Desktop-Objekte in Azure Resource Manager. Wenn Sie Azure Virtual Desktop-Objekte in Azure Resource Manager verwalten möchten, helfen Ihnen die Informationen in [diesem Artikel](../troubleshoot-set-up-overview.md) weiter.

Azure Virtual Desktop verfügt über eine Diagnosefunktion, mit der der Administrator Probleme über eine zentrale Benutzeroberfläche identifizieren kann. Die Azure Virtual Desktop-Rollen protokollieren jeweils eine Diagnoseaktivität, wenn ein Benutzer mit dem System interagiert. Jedes Protokoll enthält relevante Informationen, z. B. die an der Transaktion beteiligten Azure Virtual Desktop-Rollen, Fehlermeldungen, Mandanteninformationen und Benutzerinformationen. Diagnoseaktivitäten werden sowohl bei Endbenutzer- als auch bei Administratoraktionen erstellt und können in drei Buckets kategorisiert werden:

* Feedabonnementaktivitäten: Der Endbenutzer löst diese Aktivitäten jeweils aus, wenn er versucht, über Microsoft-Remotedesktopanwendungen eine Verbindung mit seinem Feed herzustellen.
* Verbindungsaktivitäten: Der Endbenutzer löst diese Aktivitäten jeweils aus, wenn er versucht, über Microsoft-Remotedesktopanwendungen eine Verbindung mit einem Desktop oder einer RemoteApp herzustellen.
* Verwaltungsaktivitäten: Der Administrator löst diese Aktivitäten jeweils aus, wenn er im System Verwaltungsvorgänge durchführt, z. B. das Erstellen von Hostpools, Zuweisen von Benutzern zu App-Gruppen und Erstellen von Rollenzuweisungen.

Verbindungen, die Azure Virtual Desktop nicht erreichen, werden nicht in den Diagnoseergebnissen angezeigt, da der Diagnoserollendienst selbst Teil von Azure Virtual Desktop ist. Azure Virtual Desktop-Verbindungsprobleme können auftreten, wenn es für den Endbenutzer zu Problemen mit der Netzwerkkonnektivität kommt.

Zunächst müssen Sie das [Azure Virtual Desktop-PowerShell-Modul herunterladen und importieren](/powershell/windows-virtual-desktop/overview/), um es in Ihrer PowerShell-Sitzung verwenden zu können. Führen Sie anschließend das folgende Cmdlet aus, um sich bei Ihrem Konto anzumelden:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnostizieren von Problemen mit PowerShell

Für die Azure Virtual Desktop-Diagnose wird nur ein PowerShell-Cmdlet verwendet, aber es sind viele optionale Parameter verfügbar, mit denen Probleme eingegrenzt und isoliert werden können. In den folgenden Abschnitten sind die Cmdlets aufgeführt, die Sie zum Diagnostizieren von Problemen ausführen können. Die meisten Filter können zusammen angewendet werden. Die Werte in Klammern, z. B. `<tenantName>`, sollten durch die Werte ersetzt werden, die für Ihre Situation gelten.

>[!IMPORTANT]
>Die Diagnosefunktion ist für die Problembehandlung von einzelnen Benutzern vorgesehen. Alle Abfragen mit PowerShell müssen entweder den Parameter *-UserName* oder *-ActivityID* enthalten. Verwenden Sie zur Nutzung von Überwachungsfunktionen Log Analytics. Weitere Informationen zum Senden von Diagnosedaten an Ihren Arbeitsbereich finden Sie unter [Verwenden von Log Analytics für die Diagnosefunktion](diagnostics-log-analytics-2019.md).

### <a name="filter-diagnostic-activities-by-user"></a>Filtern von Diagnoseaktivitäten nach Benutzer

Mit dem Parameter **-UserName** wird eine Liste mit Diagnoseaktivitäten zurückgegeben, die vom angegebenen Benutzer initiiert werden. Dies ist im folgenden Beispiel-Cmdlet dargestellt.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Der Parameter **-UserName** kann auch mit anderen optionalen Filterparametern kombiniert werden.

### <a name="filter-diagnostic-activities-by-time"></a>Filtern von Diagnoseaktivitäten nach Zeit

Sie können die Liste mit den zurückgegebenen Diagnoseaktivitäten mit den Parametern **-StartTime** und **-EndTime** filtern. Mit dem Parameter **-StartTime** wird eine Liste mit Diagnoseaktivitäten zurückgegeben, die ab einem bestimmten Datum beginnt. Dies ist im folgenden Beispiel dargestellt.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Der Parameter **-EndTime** kann einem Cmdlet mit dem Parameter **-StartTime** hinzugefügt werden, um einen bestimmten Zeitraum anzugeben, für den Sie Ergebnisse abrufen möchten. Mit dem folgenden Beispiel-Cmdlet wird eine Liste mit den Diagnoseaktivitäten für den Zeitraum zwischen dem 1. August und dem 10. August zurückgegeben.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Die Parameter **-StartTime** und **-EndTime** können auch mit anderen optionalen Filterparametern kombiniert werden.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtern von Diagnoseaktivitäten nach Aktivitätstyp

Sie können auch den Parameter **-ActivityType** verwenden, um Diagnoseaktivitäten nach Aktivitätstyp zu filtern. Mit dem folgenden Cmdlet wird eine Liste mit Endbenutzerverbindungen zurückgegeben:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Mit dem folgenden Cmdlet wird eine Liste mit Administratorverwaltungsaufgaben zurückgegeben:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Für das Cmdlet **Get-RdsDiagnosticActivities** wird das Angeben des Feeds als ActivityType derzeit nicht unterstützt.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtern von Diagnoseaktivitäten nach Ergebnis

Sie können die Liste mit den zurückgegebenen Diagnoseaktivitäten mit dem Parameter **-Outcome** nach dem Ergebnis filtern. Mit dem folgenden Beispiel-Cmdlet wird eine Liste mit erfolgreichen Diagnoseaktivitäten zurückgegeben.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Mit dem folgenden Beispiel-Cmdlet wird eine Liste mit nicht erfolgreichen Diagnoseaktivitäten zurückgegeben.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Der Parameter **-Outcome** kann auch mit anderen optionalen Filterparametern kombiniert werden.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Abrufen einer bestimmten Diagnoseaktivität nach Aktivitäts-ID

Mit dem Parameter **-ActivityId** wird eine bestimmte Diagnoseaktivität zurückgegeben, sofern sie vorhanden ist. Dies ist im folgenden Beispiel-Cmdlet dargestellt.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Anzeigen von Fehlermeldungen für eine fehlerhafte Aktivität nach Aktivitäts-ID

Zum Anzeigen der Fehlermeldungen für eine fehlerhafte Aktivität müssen Sie das Cmdlet mit dem Parameter **-Detailed** ausführen. Sie können die Liste der Fehler anzeigen, indem Sie das Cmdlet **Select-Object** ausführen.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Abrufen von ausführlichen Diagnoseaktivitäten

Mit dem Parameter **-Detailed** werden zusätzliche Details für jede zurückgegebene Diagnoseaktivität bereitgestellt. Das Format für jede Aktivität variiert je nach Aktivitätstyp. Der Parameter **-Detailed** kann allen **Get-RdsDiagnosticActivities**-Abfragen hinzugefügt werden. Dies ist im folgenden Beispiel veranschaulicht.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Allgemeine Fehlerszenarien

Fehlerszenarien werden in die Bereiche „Dienstintern“ und „Außerhalb von Azure Virtual Desktop“ (extern) kategorisiert.

* Internes Problem: Betrifft Szenarien, die nicht vom Mandantenadministrator gelöst werden können und als ein Supportproblem behandelt werden müssen. Wenn Sie Feedback über die [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) bereitstellen, geben Sie die Aktivitäts-ID sowie den ungefähren Zeitrahmen an, in dem das Problem aufgetreten ist.
* Externes Problem: Betrifft Szenarien, die vom Systemadministrator gelöst werden können. Dies erfolgt außerhalb von Azure Virtual Desktop.

In der folgenden Tabelle sind häufige Fehler aufgeführt, die für Ihre Administratoren ggf. auftreten können.

>[!NOTE]
>Diese Liste enthält die häufigsten Fehler und wird in regelmäßigen Abständen aktualisiert. Damit Sie immer über die aktuellen Informationen verfügen, ist es ratsam, diesen Artikel mindestens einmal pro Monat aufzurufen.

### <a name="external-management-error-codes"></a>Externe Verwaltung: Fehlercodes

|Numerischer Code|Fehlercode|Vorgeschlagene Lösung|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|Der Benutzer ist kein Mitglied von Azure Active Directory. Befolgen Sie die Anweisungen unter [Active Directory-Verwaltungscenter](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center), um ihn hinzuzufügen.|
|3|UnauthorizedAccess|Der Benutzer, der das administrative PowerShell-Cmdlet ausführen möchte, verfügt entweder nicht über die erforderlichen Berechtigungen oder hat seinen Benutzernamen falsch eingegeben.|
|1000|TenantNotFound|Der von Ihnen eingegebene Mandantenname stimmt nicht mit vorhandenen Mandanten überein. Überprüfen Sie den Mandantennamen auf Tippfehler, und wiederholen Sie den Vorgang.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Sie können einen Mandanten nicht löschen, solange er Objekte enthält. Löschen Sie zuerst die Sitzungshostpools, und wiederholen Sie dann den Vorgang.|
|2000|HostPoolNotFound|Der von Ihnen eingegebene Hostpoolname stimmt nicht mit vorhandenen Hostpools überein. Überprüfen Sie den Hostpoolnamen auf Tippfehler, und wiederholen Sie den Vorgang.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Sie können einen Hostpool nicht löschen, solange er Objekte enthält. Entfernen Sie zuerst alle App-Gruppen im Hostpool.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Entfernen Sie zuerst alle Sitzungen, bevor Sie den Sitzungshostpool löschen.|
|5001|SessionHostNotFound|Der von Ihnen abgefragte Sitzungshost ist unter Umständen offline. Überprüfen Sie den Status des Hostpools.|
|5008|SessionHostUserSessionsExist |Sie müssen alle Benutzer vom Sitzungshost abmelden, bevor Sie Ihre gewünschte Verwaltungsaktivität ausführen.|
|6000|AppGroupNotFound|Der von Ihnen eingegebene App-Gruppenname stimmt nicht mit vorhandenen App-Gruppen überein. Überprüfen Sie den App-Gruppennamen auf Tippfehler, und wiederholen Sie den Vorgang.|
|6022|RemoteAppNotFound|Der von Ihnen eingegebene RemoteApp-Name stimmt nicht mit vorhandenen RemoteApps überein. Überprüfen Sie den RemoteApp-Namen auf Tippfehler, und wiederholen Sie den Vorgang.|
|6010|PublishedItemsExist|Der Name der Ressource, die Sie veröffentlichen möchten, ist mit dem Namen einer bereits vorhandenen Ressource identisch. Ändern Sie den Namen der Ressource, und wiederholen Sie den Vorgang.|
|7002|NameNotValidWhiteSpace|Verwenden Sie keine Leerzeichen im Namen.|
|8.000|InvalidAuthorizationRoleScope|Der von Ihnen eingegebene Rollenname stimmt nicht mit vorhandenen Rollennamen überein. Überprüfen Sie den Rollennamen auf Tippfehler, und wiederholen Sie den Vorgang. |
|8001|UserNotFound |Der von Ihnen eingegebene Benutzername stimmt nicht mit vorhandenen Benutzernamen überein. Überprüfen Sie den Namen auf Tippfehler, und wiederholen Sie den Vorgang.|
|8005|UserNotFoundInAAD |Der von Ihnen eingegebene Benutzername stimmt nicht mit vorhandenen Benutzernamen überein. Überprüfen Sie den Namen auf Tippfehler, und wiederholen Sie den Vorgang.|
|8008|TenantConsentRequired|Befolgen Sie [diese Anleitung](tenant-setup-azure-active-directory.md#grant-permissions-to-azure-virtual-desktop), um die Einwilligung für Ihren Mandanten zu erteilen.|

### <a name="external-connection-error-codes"></a>Externe Verbindung: Fehlercodes

|Numerischer Code|Fehlercode|Vorgeschlagene Lösung|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|Der Benutzer ist kein Mitglied von Active Directory. Befolgen Sie die Anweisungen unter [Active Directory-Verwaltungscenter](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center), um ihn hinzuzufügen.|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Der Sitzungshost ist nicht richtig in Active Directory eingebunden.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Bei der Verbindungsherstellung ist ein Fehler aufgetreten, weil der Sitzungshost nicht verfügbar ist. Überprüfen Sie die Integrität des Sitzungshosts.|
|-2146233088|ConnectionFailedClientDisconnect|Wenn dieser Fehler häufig angezeigt wird, sollten Sie sicherstellen, dass der Computer des Benutzers mit dem Netzwerk verbunden ist.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Die Sitzung, mit der der Hostbenutzer eine Verbindung herstellen möchte, ist nicht fehlerfrei. Debuggen Sie den virtuellen Computer.|
|-2146233088|ConnectionFailedUserNotAuthorized|Der Benutzer verfügt nicht über die Berechtigung zum Zugreifen auf die veröffentlichte App oder den Desktop. Der Fehler wird unter Umständen angezeigt, nachdem der Administrator die veröffentlichten Ressourcen entfernt hat. Bitten Sie den Benutzer, den Feed in der Remotedesktopanwendung zu aktualisieren.|
|2|FileNotFound|Die Anwendung, auf die der Benutzer zugreifen möchte, ist entweder falsch installiert oder auf einen falschen Pfad festgelegt.|
|3|InvalidCredentials|Der vom Benutzer eingegebene Benutzername oder das Kennwort stimmt nicht mit vorhandenen Benutzernamen bzw. Kennwörtern überein. Überprüfen Sie die Anmeldeinformationen auf Tippfehler, und wiederholen Sie den Vorgang.|
|8|ConnectionBroken|Die Verbindung zwischen dem Client und dem Gateway oder Server wurde getrennt. Sofern dieser Vorgang nicht unerwartet auftritt, ist keine Aktion erforderlich.|
|14|UnexpectedNetworkDisconnect|Die Verbindung mit dem Netzwerk wurde getrennt. Bitten Sie den Benutzer, die Verbindung wiederherzustellen.|
|24|ReverseConnectFailed|Der virtuelle Hostcomputer verfügt nicht über eine direkte Sichtlinie zum RD-Gateway. Stellen Sie sicher, dass die IP-Adresse des Gateways aufgelöst werden kann.|
|1322|ConnectionFailedNoMappingOfSIDinAD|Der Benutzer ist kein Mitglied von Active Directory. Befolgen Sie die Anweisungen unter [Active Directory-Verwaltungscenter](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center), um ihn hinzuzufügen.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Rollen in Azure Virtual Desktop finden Sie unter [Azure Virtual Desktop-Umgebung](environment-setup-2019.md).

Eine Liste mit verfügbaren PowerShell-Cmdlets für Azure Virtual Desktop finden Sie in der [PowerShell-Referenz](/powershell/windows-virtual-desktop/overview).