---
title: Schützen gruppenverwalteter Dienstkonten | Azure Active Directory
description: Leitfaden zum Schutz von Computerkonten vom Typ „gruppenverwaltetes Dienstkonto“ (group managed service account, gMSA).
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
ms.openlocfilehash: f934e016f23e988cbe8673f80e96c4583b6aaec5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292507"
---
# <a name="secure-group-managed-service-accounts"></a>Schützen von gruppenverwalteten Dienstkonten

Gruppenverwaltete Dienstkonten (Group Managed Service Accounts, gMSAs) sind verwaltete Domänenkonten, die zum Schutz von Diensten verwendet werden. gMSAs können auf einem einzelnen Server oder in einer Serverfarm ausgeführt werden – beispielsweise in Systemen hinter einem Netzwerklastenausgleich oder auf einem IIS-Server (Internet Information Services). Nachdem Sie Ihre Dienste für die Verwendung eines gMSA-Prinzipals konfiguriert haben, wird die Kennwortverwaltung für dieses Konto vom Windows-Betriebssystem abgewickelt.

## <a name="benefits-of-using-gmsas"></a>Vorteile der Verwendung von gMSAs

gMSAs bieten eine Einzelidentitätslösung mit höherer Sicherheit. Außerdem tragen sie dank folgender Merkmale zur Verringerung des Verwaltungsaufwands bei:

* **Sichere Kennwörter:** Von gMSAs werden komplexe, nach dem Zufallsprinzip generierte Kennwörter mit 240 Bytes verwendet. Die Komplexität und Länge von gMSA-Kennwörtern minimiert die Wahrscheinlichkeit, dass ein Dienst durch Brute-Force- oder Wörterbuchangriffe kompromittiert wird.

* **Regelmäßige Kennwortrotation:** Bei gMSAs wird die Kennwortverwaltung in das Windows-Betriebssystem verlagert, wodurch das Kennwort alle 30 Tage geändert wird. Dienst- und Domänenadministratoren müssen keine Kennwortänderungen mehr planen oder mit Dienstausfällen zurechtkommen, um Dienstkonten zu schützen. 

* **Unterstützung der Bereitstellung in Serverfarmen:** Die Möglichkeit, gMSAs auf mehreren Servern bereitzustellen, ermöglicht die Unterstützung von Lösungen mit Lastenausgleich, bei denen der gleiche Dienst von mehreren Hosts ausgeführt wird. 

* **Unterstützung der vereinfachten SPN-Verwaltung (Service Principal Name, Dienstprinzipalname):** Sie können bei der Kontoerstellung mithilfe von PowerShell einen SPN einrichten. Außerdem kann von Diensten, die automatische SPN-Registrierungen unterstützen, eine solche Registrierung ggf. über das gMSA durchgeführt werden, sofern die gMSA-Berechtigungen ordnungsgemäß festgelegt sind. 

## <a name="when-to-use-gmsas"></a>Einsatzgebiete für gMSAs

Verwenden Sie gMSAs als bevorzugten Kontotyp für lokale Dienste, es sei denn, dies wird in einem Dienst (z. B. Failoverclustering) nicht unterstützt.

> [!IMPORTANT]
> Sie müssen Ihren Dienst mit gMSAs testen, bevor Sie ihn in der Produktionsumgebung bereitstellen. Richten Sie hierzu eine Testumgebung ein, und vergewissern Sie sich, dass die Anwendung das gMSA verwenden und auf die erforderlichen Ressourcen zugreifen kann. Weitere Informationen finden Sie unter [Unterstützung für gruppenverwaltete Dienstkonten](/system-center/scom/support-group-managed-service-accounts).


Wenn ein Dienst die Verwendung von gMSAs nicht unterstützt, kann als nächstbeste Alternative ein eigenständiges verwaltetes Dienstkonto (standalone managed service account, sMSA) verwendet werden. Ein sMSA bietet die gleichen Funktionen wie ein gMSA, ist jedoch nur für die Bereitstellung auf einem einzelnen Server vorgesehen.

Wenn Sie kein von Ihrem Dienst unterstütztes gMSA oder sMSA verwenden können, muss der Dienst für die Ausführung als Standardbenutzerkonto konfiguriert werden. Zum Schutz des Kontos müssen Dienst- und Domänenadministratoren sichere Kennwortverwaltungsprozesse verwenden.

## <a name="assess-the-security-posture-of-gmsas"></a>Bewerten des Sicherheitsstatus von gMSAs

gMSAs sind grundsätzlich sicherer als Standardbenutzerkonten, die eine kontinuierliche Kennwortverwaltung erfordern. Bei der Überprüfung des allgemeinen Sicherheitsstatus ist es jedoch wichtig, den Zugriffsbereich von gMSAs zu berücksichtigen.

Die folgende Tabelle enthält Informationen zu potenziellen Sicherheitsproblemen und entsprechenden Maßnahmen für gMSAs:

| Sicherheitsproblem| Minderung |
| - | - |
| Das gMSA ist Mitglied von privilegierten Gruppen. | <li>Überprüfen Sie die Gruppenmitgliedschaften. Dazu können Sie ein PowerShell-Skript erstellen, um alle Gruppenmitgliedschaften aufzuzählen. Anschließend können Sie eine resultierende CSV-Datei nach den Namen Ihrer gMSA-Dateien filtern.<li>Entfernen Sie das gMSA aus privilegierten Gruppen.<li>Weisen Sie dem gMSA nur die Rechte und Berechtigungen zu, die zum Ausführen des zugehörigen Diensts erforderlich sind (wenden Sie sich an den Dienstanbieter). 
| Das gMSA verfügt über Lese-/Schreibzugriff auf vertrauliche Ressourcen. | <li>Überwachen Sie den Zugriff auf vertrauliche Ressourcen.<li>Archivieren Sie zur Analyse Überwachungsprotokolle in einem SIEM-System wie Azure Log Analytics oder Microsoft Sentinel.<li>Entfernen Sie unnötige Ressourcenberechtigungen, falls Sie eine unerwünschte Zugriffsebene finden. |
| | |


## <a name="find-gmsas"></a>Suchen von gMSAs

Von Ihrer Organisation wurden möglicherweise bereits gMSAs erstellt. Führen Sie zum Abrufen dieser Konten das folgende PowerShell-Cmdlet aus:

```powershell
Get-ADServiceAccount 
Install-ADServiceAccount 
New-ADServiceAccount 
Remove-ADServiceAccount 
Set-ADServiceAccount 
Test-ADServiceAccount 
Uninstall-ADServiceAccount
```


Für eine effektive Verwendung müssen sich gMSAs in der Organisationseinheit (OE) des verwalteten Dienstkontos befinden.

  
![Screenshot: gMSA in der Organisationseinheit des verwalteten Dienstkontos](./media/securing-service-accounts/secure-gmsa-image-1.png)

Führen Sie die folgenden Befehle aus, um nach Dienst-MSAs zu suchen, die möglicherweise nicht in der Liste enthalten sind:

```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all managed service accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where-object {$_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>Verwalten von gMSAs

Für die Verwaltung von gMSAs können die folgenden Active Directory-PowerShell-Cmdlets verwendet werden:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Ab Windows Server 2012 funktionieren die „*-ADServiceAccount“-Cmdlets standardmäßig mit gMSAs. Weitere Informationen zur Verwendung der obigen Cmdlets finden Sie unter [Erste Schritte mit gruppenverwalteten Dienstkonten](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Wechseln zu einem gMSA
gMSAs sind die sicherste Art von Dienstkonten für lokale Anforderungen. Wenn der Wechsel zu einem gMSA möglich ist, sollten Sie dies tun. Außerdem können Sie Ihre Dienste zu Azure und Ihre Dienstkonten zu Azure Active Directory migrieren. Gehen Sie wie folgt vor, um zu einem gMSA-Konto zu wechseln:

1. Stellen Sie sicher, dass der [Stammschlüssel des Schlüsselverteilungsdiensts (Key Distribution Service, KDS)](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key) in der Gesamtstruktur bereitgestellt wird. Dies ist ein einmaliger Vorgang.

1. [Erstellen Sie ein neues gMSA.](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

1. Installieren Sie das neue gMSA auf jedem Host, von dem der Dienst ausgeführt wird.
   > [!NOTE] 
   > Weitere Informationen zum Erstellen und Installieren eines gMSA auf einem Host, bevor Sie Ihren Dienst für die Verwendung des gMSA konfigurieren, finden Sie unter [Erste Schritte mit gruppenverwalteten Dienstkonten](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)).

1. Ändern Sie Ihre Dienstidentität in gMSA, und geben Sie ein leeres Kennwort an.

1. Überprüfen Sie, ob der Dienst unter der neuen gMSA-Identität ausgeführt wird.

1. Löschen Sie die alte Identität des Dienstkontos.

 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Schutz von Dienstkonten finden Sie in den folgenden Artikeln:

* [Einführung in lokale Dienstkonten](service-accounts-on-premises.md)  
* [Schützen von eigenständigen verwalteten Dienstkonten](service-accounts-standalone-managed.md)  
* [Schützen von Computerkonten](service-accounts-computer.md)  
* [Schützen von Benutzerkonten](service-accounts-user-on-premises.md)  
* [Steuern lokaler Dienstkonten](service-accounts-govern-on-premises.md)
