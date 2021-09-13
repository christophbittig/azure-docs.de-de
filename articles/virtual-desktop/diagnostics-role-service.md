---
title: Diagnostizieren von Problemen mit Azure Virtual Desktop – Azure
description: So verwenden Sie die Diagnosefunktion von Azure Virtual Desktop zum Diagnostizieren von Problemen.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0154bb61dcfbcf8024f09052d7a199ac63f71d8e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412539"
---
# <a name="identify-and-diagnose-azure-virtual-desktop-issues"></a>Identifizieren und Diagnostizieren von Problemen mit Azure Virtual Desktop

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Azure Virtual Desktop verfügt über eine Diagnosefunktion, mit der der Administrator Probleme über eine zentrale Benutzeroberfläche identifizieren kann. Weitere Informationen zu den Diagnosefunktionen von Azure Virtual Desktop finden Sie unter [Verwenden von Log Analytics für die Diagnosefunktion](diagnostics-log-analytics.md).

Verbindungen, die Azure Virtual Desktop nicht erreichen, werden nicht in den Diagnoseergebnissen angezeigt, da der Diagnoserollendienst selbst Teil von Azure Virtual Desktop ist. Azure Virtual Desktop-Verbindungsprobleme können auftreten, wenn es für den Endbenutzer zu Problemen mit der Netzwerkkonnektivität kommt.

## <a name="common-error-scenarios"></a>Allgemeine Fehlerszenarien

In der Tabelle „WVDErrors“ werden Fehler für alle Aktivitätstypen nachverfolgt. Die Spalte „ServiceError“ enthält ein zusätzliches Flag („True“ oder „False“). Dieses Flag gibt Aufschluss darüber, ob der Fehler mit dem Dienst zusammenhängt.

* Lautet der Wert „True“, hat das Dienstteam dieses Problem möglicherweise bereits untersucht. Falls sich das Problem auf die Benutzerfreundlichkeit auswirkt und häufig auftritt, empfiehlt es sich, ein Supportticket für Azure Virtual Desktop zu erstellen.
* Lautet der Wert „False“, handelt es sich möglicherweise um eine Fehlkonfiguration, die Sie selbst beheben können. Die Fehlermeldung kann Aufschluss über einen möglichen Ansatzpunkt geben.

In der folgenden Tabelle sind häufige Fehler aufgeführt, die für Ihre Administratoren ggf. auftreten können.

>[!NOTE]
>Diese Liste enthält die häufigsten Fehler und wird in regelmäßigen Abständen aktualisiert. Damit Sie immer über die aktuellen Informationen verfügen, ist es ratsam, diesen Artikel mindestens einmal pro Monat aufzurufen.

### <a name="connection-error-codes"></a>Verbindungsfehlercodes

|Numerischer Code|Fehlercode|Vorgeschlagene Lösung|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Der Sitzungshost ist nicht richtig in Active Directory eingebunden.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Bei der Verbindungsherstellung ist ein Fehler aufgetreten, weil der Sitzungshost nicht verfügbar ist. Überprüfen Sie die Integrität des Sitzungshosts.|
|-2146233088|ConnectionFailedClientDisconnect|Wenn dieser Fehler häufig angezeigt wird, sollten Sie sicherstellen, dass der Computer des Benutzers mit dem Netzwerk verbunden ist.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Die Sitzung, mit der der Hostbenutzer eine Verbindung herstellen möchte, ist nicht fehlerfrei. Debuggen Sie den virtuellen Computer.|
|-2146233088|ConnectionFailedUserNotAuthorized|Der Benutzer verfügt nicht über die Berechtigung zum Zugreifen auf die veröffentlichte App oder den Desktop. Der Fehler wird unter Umständen angezeigt, nachdem der Administrator die veröffentlichten Ressourcen entfernt hat. Bitten Sie den Benutzer, den Feed in der Remotedesktopanwendung zu aktualisieren.|
|2|FileNotFound|Die Anwendung, auf die der Benutzer zugreifen möchte, ist entweder falsch installiert oder auf einen falschen Pfad festgelegt.<br><br>Wenn neue Apps veröffentlicht werden, während der Benutzer eine aktive Sitzung ausführt, kann der Benutzer nicht auf diese App zugreifen. Die Sitzung muss beendet und neu gestartet werden, bevor der Benutzer auf die App zugreifen kann. |
|3|InvalidCredentials|Der vom Benutzer eingegebene Benutzername oder das Kennwort stimmt nicht mit vorhandenen Benutzernamen bzw. Kennwörtern überein. Überprüfen Sie die Anmeldeinformationen auf Tippfehler, und wiederholen Sie den Vorgang.|
|8|ConnectionBroken|Die Verbindung zwischen dem Client und dem Gateway oder Server wurde getrennt. Sofern dieser Vorgang nicht unerwartet auftritt, ist keine Aktion erforderlich.|
|14|UnexpectedNetworkDisconnect|Die Verbindung mit dem Netzwerk wurde getrennt. Bitten Sie den Benutzer, die Verbindung wiederherzustellen.|
|24|ReverseConnectFailed|Der virtuelle Hostcomputer verfügt nicht über eine direkte Sichtlinie zum RD-Gateway. Stellen Sie sicher, dass die IP-Adresse des Gateways aufgelöst werden kann.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Rollen in Azure Virtual Desktop finden Sie unter [Azure Virtual Desktop-Umgebung](environment-setup.md).

Eine Liste mit verfügbaren PowerShell-Cmdlets für Azure Virtual Desktop finden Sie in der [PowerShell-Referenz](/powershell/windows-virtual-desktop/overview).
