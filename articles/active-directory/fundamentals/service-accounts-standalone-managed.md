---
title: Schützen von eigenständigen verwalteten Dienstkonten | Azure Active Directory
description: Eine Anleitung zum Schützen von eigenständigen verwalteten Dienstkonten.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8237e746e6f48640ed1aca8d2637eb128172a297
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292431"
---
# <a name="secure-standalone-managed-service-accounts"></a>Schützen von eigenständigen verwalteten Dienstkonten

Eigenständige verwaltete Dienstkonten (Standalone Managed Service Accounts, sMSAs) sind verwaltete Domänenkonten, die zum Schutz eines oder mehrerer Dienste verwendet werden, welche auf einem Server ausgeführt werden. Sie können nicht auf mehreren Servern wiederverwendet werden. sMSAs bieten eine automatische Kennwortverwaltung, eine einfachere Verwaltung des Dienstprinzipalnamens und die Möglichkeit, die Verwaltung an andere Administratoren zu delegieren. 

In Active Directory sind sMSAs an einen bestimmten Server gebunden, der einen Dienst ausführt. Diese Konten sind in der Microsoft Management Console im Snap-in „Active Directory-Benutzer und -Computer“ aufgeführt.

![Screenshot: Snap-In „Active Directory-Benutzer und -Computer“ mit der Organisationseinheit „Verwaltete Dienstkonten“.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Verwaltete Dienstkonten wurden mit dem Active Directory-Schema für Windows Server 2008 R2 eingeführt und erfordern mindestens Windows Server 2008 R2. 

## <a name="benefits-of-using-smsas"></a>Vorteile der Verwendung von sMSAs

sMSAs bieten bei der Verwendung als Dienstkonten mehr Sicherheit als Benutzerkonten. Außerdem tragen sie dank folgender Merkmale zur Verringerung des Verwaltungsaufwands bei:

* **Sichere Kennwörter**: Von gMSAs werden komplexe, nach dem Zufallsprinzip generierte Kennwörter mit 240 Bytes verwendet. Durch Komplexität und Länge der sMSA-Kennwörter sinkt die Wahrscheinlichkeit, dass ein Dienst durch Brute-Force- oder Wörterbuchangriffe kompromittiert wird.

* **Regelmäßige Änderung von Kennwörtern**: Das sMSA-Kennwort wird von Windows automatisch alle 30 Tage geändert. Dienst- und Domänenadministratoren brauchen keine Kennwortänderungen zu planen oder damit verbundene Ausfallzeiten zu verwalten.

* **Vereinfachte SPN-Verwaltung**: Dienstprinzipalnamen werden automatisch aktualisiert, wenn Windows Server 2008 R2 die Domänenfunktionsebene ist. Zum Beispiel wird der Dienstprinzipalname automatisch aktualisiert, wenn Sie:
   * das Hostcomputerkonto umbenennen.  
   * den DNS-Namen (Domain Name Server) des Hostcomputers ändern.  
   * mithilfe von [PowerShell](/powershell/module/activedirectory/set-adserviceaccount) andere „sam-accountname“- oder „dns-hostname“-Parameter hinzufügen oder entfernen.

## <a name="when-to-use-smsas"></a>Wann sind sMSAs zu verwenden?

sMSAs können Verwaltungs- und Sicherheitsaufgaben vereinfachen. Verwenden Sie sMSAs, wenn mindestens ein Dienst auf einem einzelnen Server bereitgestellt wurde und Sie kein gruppenverwaltetes Dienstkonto (Group Managed Service Account, gMSA) verwenden können. 

> [!NOTE] 
> Sie können sMSAs zwar für mehr als einen Dienst verwenden, zu Überwachungszwecken ist es jedoch empfehlenswert, wenn jeder Dienst eine eigene Identität hat. 

Wenn der Ersteller der Software nicht sagen kann, ob Sie ein verwaltetes Dienstkonto verwenden können, müssen Sie die Anwendung testen. Erstellen Sie hierzu eine Testumgebung, und stellen Sie sicher, dass sie auf alle erforderlichen Ressourcen zugreifen kann. Weitere Informationen finden Sie unter [Erstellen und Installieren eines sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting).

### <a name="assess-the-security-posture-of-smsas"></a>Bewerten des Sicherheitsstatus von sMSAs

sMSAs sind grundsätzlich sicherer als Standardbenutzerkonten, die eine ständige Kennwortverwaltung erfordern. Bei der Überprüfung des allgemeinen Sicherheitsstatus ist es jedoch wichtig, den Zugriffsbereich von sMSAs zu berücksichtigen.

In der folgenden Tabelle wird gezeigt, wie Sie potenzielle sMSA-bedingte Sicherheitsprobleme minimieren können:

| Sicherheitsproblem| Minderung |
| - | - |
| sMSA ist Mitglied privilegierter Gruppen. | <li>Entfernen Sie das sMSA aus Gruppen mit erhöhten Rechten (z. B. Domänenadministratoren).<li>Verwenden Sie das Modell mit den *geringsten Berechtigungen*, und erteilen Sie dem sMSA nur die Rechte und Berechtigungen, die zum Ausführen der Dienste erforderlich sind.<li>Wenn Sie nicht sicher sind, welche Berechtigungen erforderlich sind, wenden Sie sich an den Ersteller des Diensts. |
| Das sMSA verfügt über Lese-/Schreibzugriff auf vertrauliche Ressourcen. | <li>Überwachen Sie den Zugriff auf vertrauliche Ressourcen.<li>Archivieren Sie Überwachungsprotokolle zur Analyse in einem SIEM-Programm (Security Information and Event Management) wie Azure Log Analytics oder Microsoft Sentinel.<li>Korrigieren Sie Ressourcenberechtigungen, wenn eine unerwünschte Zugriffsebene erkannt wird. |
| Standardmäßig beträgt das Kennwortrolloverintervall von sMSAs 30 Tage. | Mit einer Gruppenrichtlinie können Sie die Dauer entsprechend den Sicherheitsanforderungen des Unternehmens optimieren. Zum Festlegen der Gültigkeitsdauer von Kennwörtern verwenden Sie den folgenden Pfad:<br>*Computerkonfiguration\Richtlinien\Windows-Einstellungen\Sicherheitseinstellungen\Sicherheitsoptionen*. Verwenden Sie für Domänenmitglieder **Maximalalter von Computerkontenkennwörtern**. |
| | |



### <a name="challenges-with-smsas"></a>Probleme im Zusammenhang mit sMSAs

Im Zusammenhang mit sMSAs gibt es folgende Herausforderungen:

| Herausforderung| Minderung |
| - | - |
| sMSAs können nur auf einem einzelnen Server verwendet werden. | Verwenden Sie ein gMSA, wenn Sie das Konto serverübergreifend verwenden müssen. |
| sMSAs können nicht domänenübergreifend verwendet werden. | Verwenden Sie ein gMSA, wenn Sie das Konto domänenübergreifend verwenden müssen. |
| Nicht alle Anwendungen unterstützen sMSAs. | Verwenden Sie nach Möglichkeit ein gMSA. Andernfalls verwenden Sie ein Standardbenutzerkonto oder ein Computerkonto, wie vom Anwendungsersteller empfohlen. |
| | |


## <a name="find-smsas"></a>Suchen nach sMSAs

Führen Sie auf jedem Domänencontroller „DSA.msc“ aus, und erweitern Sie den Container „Verwaltete Dienstkonten“, um alle sMSAs anzuzeigen. 

Führen Sie den folgenden PowerShell-Befehl aus, um alle sMSAs und gMSAs in der Active Directory-Domäne zurückzugeben: 

`Get-ADServiceAccount -Filter *`

Führen Sie den folgenden Befehl aus, um nur sMSAs in der Active Directory-Domäne zurückzugeben:

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Verwalten von sMSAs

Für die Verwaltung Ihrer sMSAs können Sie die folgenden Active Directory-PowerShell-Cmdlets verwenden:

`Get-ADServiceAccount`
`Install-ADServiceAccount`
`New-ADServiceAccount`
`Remove-ADServiceAccount`
`Set-ADServiceAccount`
`Test-ADServiceAccount`
`Uninstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Wechseln zu sMSAs

Wenn ein Anwendungsdienst ein sMSA, aber kein gMSA unterstützt und Sie derzeit ein Benutzerkonto oder ein Computerkonto für den Sicherheitskontext verwenden, [erstellen und installieren Sie ein sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) auf dem Server. 

Im Idealfall würden Sie Ressourcen zu Azure verschieben und verwaltete Azure-Identitäten oder Dienstprinzipale verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Schutz von Dienstkonten finden Sie in den folgenden Artikeln:

* [Einführung in lokale Dienstkonten](service-accounts-on-premises.md)  
* [Schützen von gruppenverwalteten Dienstkonten](service-accounts-group-managed.md)  
* [Schützen von Computerkonten](service-accounts-computer.md)  
* [Schützen von Benutzerkonten](service-accounts-user-on-premises.md)  
* [Steuern lokaler Dienstkonten](service-accounts-govern-on-premises.md)
