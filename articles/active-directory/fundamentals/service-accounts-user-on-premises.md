---
title: Schützen benutzerbasierter Dienstkonten | Azure Active Directory
description: Leitfaden zum Schutz benutzerbasierter Dienstkonten
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
ms.openlocfilehash: 6dc3ec13715fe0c58ffbf23cf377f0ea5d29d6de
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292393"
---
# <a name="secure-user-based-service-accounts-in-active-directory"></a>Schützen benutzerbasierter Dienstkonten in Active Directory

Unter Windows ausgeführte Dienste werden traditionell mithilfe lokaler Benutzerkonten geschützt. Verwenden Sie diese Konten nur, falls von Ihrem Dienst keine gruppenverwalteten Dienstkonten (group managed service accounts, gMSAs) oder eigenständigen verwalteten Dienstkonten (standalone managed service accounts, sMSAs) unterstützt werden. Informationen zur Wahl des optimalen Kontotyps finden Sie in der [Einführung in lokale Dienstkonten](service-accounts-on-premises.md). 

Untersuchen Sie ggf. auch, ob Sie Ihren Dienst verschieben können, um ein Azure-Dienstkonto (etwa eine verwaltete Identität oder einen Dienstprinzipal) zu verwenden. 

Sie können lokale Benutzerkonten erstellen, um einen Sicherheitskontext für die Dienste und Berechtigungen bereitzustellen, die die Konten für den Zugriff auf lokale Ressourcen und Netzwerkressourcen benötigen. Kennwörter müssen bei lokalen Benutzerkonten ähnlich wie bei anderen Active Directory-Benutzerkonten lokal verwaltet werden. Zum Schutz dieser Konten müssen Dienst- und Domänenadministratoren sichere Kennwortverwaltungsprozesse verwenden.

Wenn Sie ein Benutzerkonto als Dienstkonto erstellen, verwenden Sie es nur für einen einzelnen Dienst. Geben Sie ihm einen Namen, der deutlich macht, dass es sich um ein Dienstkonto handelt und für welchen Dienst es verwendet wird. 

## <a name="benefits-and-challenges"></a>Vorteile und Einschränkungen

Lokale Benutzerkonten können erhebliche Vorteile bieten. Sie sind der vielseitigste Kontotyp für die Verwendung mit Diensten. Als Dienstkonten verwendete Benutzerkonten können über die gleichen Richtlinien gesteuert werden, über die auch normale Benutzerkonten gesteuert werden. Sie sollten jedoch nur verwendet werden, wenn Sie kein MSA verwenden können. Überprüfen Sie außerdem, ob ein Computerkonto nicht die bessere Option wäre. 

Die Herausforderungen im Zusammenhang mit der Verwendung lokaler Benutzerkonten sind in der folgenden Tabelle zusammengefasst:

| Herausforderung | Minderung |
| - | - |
| Die Kennwortverwaltung wird manuell durchgeführt. Dies kann die Sicherheit beeinträchtigen und Dienstausfallzeiten zur Folge haben.| <li>Stellen Sie sicher, dass Kennwortkomplexität und Kennwortänderungen durch einen robusten Prozess gesteuert werden, der regelmäßige Aktualisierungen mit sicheren Kennwörtern gewährleistet.<li>Koordinieren Sie Kennwortänderungen mit einer Kennwortaktualisierung für den Dienst, um Dienstausfallzeiten zu verringern. |
| Die Identifizierung von lokalen Benutzerkonten, die als Dienstkonten fungieren, kann schwierig sein. | <li>Dokumentieren und verwalten Sie Datensätze der Dienstkonten, die in Ihrer Umgebung bereitgestellt werden.<li>Verfolgen Sie den Kontonamen und die Ressourcen, denen sie zugewiesen sind.<li>Es empfiehlt sich gegebenenfalls, alle als Dienstkonten verwendeten Benutzerkonten mit dem Präfix „svc-“ zu versehen. |
| | |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Suchen von als Dienstkonten verwendeten lokalen Benutzerkonten

Lokale Benutzerkonten unterscheiden sich nicht von anderen Active Directory-Benutzerkonten. Die Suche nach solchen Benutzerkonten kann sich als schwierig erweisen, da sie durch kein spezifisches Attribut als Dienstkonto identifiziert werden. 

Daher empfiehlt es sich, für alle als Dienstkonto verwendeten Benutzerkonten eine einfach zu erkennende Namenskonvention zu verwenden. Fügen Sie beispielsweise ein Präfix wie „svc-“ hinzu, und nennen Sie den Dienst „svc-HRDataConnector“.

Bei der Suche nach diesen Dienstkonten können einige der folgenden Kriterien verwendet werden. Damit werden jedoch möglicherweise nicht alle Konten gefunden. Hierzu zählen beispielweise:

* Konten, die für die Delegierung als vertrauenswürdig gelten  
* Konten mit Dienstprinzipalnamen  
* Konten, deren Kennwort nie abläuft

Sie können die folgenden PowerShell-Befehle ausführen, um die für Dienste erstellten lokalen Benutzerkonten zu suchen.

So suchen Sie nach Konten, die für die Delegierung als vertrauenswürdig gelten:

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

So suchen Sie nach Konten mit Dienstprinzipalnamen:

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

So suchen Sie nach Konten, deren Kennwort nie abläuft:

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```

Sie können außerdem den Zugriff auf vertrauliche Ressourcen überwachen und Überwachungsprotokolle in einem SIEM-System (Security Information & Event Management) archivieren. Mit Systemen wie Azure Log Analytics oder Microsoft Sentinel können Sie nach Dienstkonten suchen und sie analysieren.

## <a name="assess-the-security-of-on-premises-user-accounts"></a>Bewerten der Sicherheit lokaler Benutzerkonten

Sie können die Sicherheit lokaler, als Dienstkonten verwendeter Benutzerkonten anhand der folgenden Kriterien bewerten:

* Welche Richtlinie wird für die Kennwortverwaltung verwendet?  
* Gehört das Konto privilegierten Gruppen an?  
* Verfügt das Konto über Lese-/Schreibberechtigungen für wichtige Ressourcen?

### <a name="mitigate-potential-security-issues"></a>Beheben potenzieller Sicherheitsprobleme

In der folgenden Tabelle sind potenzielle Sicherheitsprobleme und entsprechende Maßnahmen für lokale Benutzerkonten zusammengefasst:

| Sicherheitsproblem | Minderung |
| - | - |
| Kennwortverwaltung| <li>Stellen Sie sicher, dass Kennwortkomplexität und Kennwortänderungen durch einen robusten Prozess gesteuert werden, der regelmäßige Aktualisierungen und Anforderungen für sichere Kennwörter beinhaltet.<li>Koordinieren Sie die Kennwortänderung mit einer Kennwortaktualisierung, um Dienstausfallzeiten zu minimieren. |
| Das Konto gehört privilegierten Gruppen an.| <li>Überprüfen Sie die Gruppenmitgliedschaften.<li>Entfernen Sie das Konto aus privilegierten Gruppen.<li>Weisen Sie dem Konto nur die Rechte und Berechtigungen zu, die zum Ausführen des zugehörigen Diensts erforderlich sind (wenden Sie sich an den Dienstanbieter). Beispielsweise können Sie unter Umständen die lokale oder die interaktive Anmeldung verweigern. |
| Das Konto verfügt über Lese-/Schreibberechtigungen für vertrauliche Ressourcen.| <li>Überwachen Sie den Zugriff auf vertrauliche Ressourcen.<li>Archivieren Sie zur Analyse Überwachungsprotokolle in einem SIEM-System (Azure Log Analytics oder Microsoft Sentinel).<li>Korrigieren Sie Ressourcenberechtigungen, wenn eine unerwünschte Zugriffsebene erkannt wird. |
| | |


## <a name="move-to-more-secure-account-types"></a>Wechseln zu sichereren Kontotypen

Microsoft rät davon ab, lokale Benutzerkonten als Dienstkonten zu verwenden. Untersuchen Sie bei jedem Dienst, von dem dieser Kontotyp verwendet wird, ob er nicht auch für die Verwendung eines gMSA oder sMSA konfiguriert werden kann.

Prüfen Sie außerdem, ob der Dienst zu Azure migriert werden kann, um die Verwendung sichererer Dienstkontotypen zu ermöglichen. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Schutz von Dienstkonten finden Sie in den folgenden Artikeln:

* [Einführung in lokale Dienstkonten](service-accounts-on-premises.md)  
* [Schützen von gruppenverwalteten Dienstkonten](service-accounts-group-managed.md)  
* [Schützen von eigenständigen verwalteten Dienstkonten](service-accounts-standalone-managed.md)  
* [Schützen von Computerkonten](service-accounts-computer.md)  
* [Steuern lokaler Dienstkonten](service-accounts-govern-on-premises.md)

 
