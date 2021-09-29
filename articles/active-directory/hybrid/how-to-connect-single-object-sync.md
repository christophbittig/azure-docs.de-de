---
title: 'Azure AD Connect: Single Object Sync '
description: Erfahren Sie, wie Sie ein einzelnes Objekt aus Active Directory zur Problembehandlung mit Azure AD synchronisieren können.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/24/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34d0a6c4940032bc1957c787e05722586c792417
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814391"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect: Single Object Sync 

Das Tool Azure AD Connect Single Object Sync ist ein PowerShell-Cmdlet, mit dem ein einzelnes Objekt aus Active Directory mit Azure Active Directory synchronisiert werden kann. Der generierte Bericht kann verwendet werden, um objektspezifische Synchronisierungsprobleme zu untersuchen und zu beheben. 

> [!NOTE]
> Das Tool unterstützt die Synchronisierung von Active Directory mit Azure Active Directory. Die Synchronisierung von Azure Active Directory mit Active Directory wird nicht unterstützt. 
>
> Das Tool unterstützt das Synchronisieren von „Objektänderung: Hinzufügen und Aktualisieren“. Es unterstützt nicht das Synchronisieren von „Objektänderung: Löschen“. 

## <a name="how-it-works"></a>Funktionsweise
Das Tool Single Object Sync erfordert einen Active Directory-Distinguished Name als Eingabe, um den Quellconnector und die Partition für den Import zu suchen. Die Änderungen werden in Azure Active Directory exportiert. Das Tool generiert eine JSON-Ausgabe ähnlich dem Ressourcentyp **provisioningObjectSummary**. 

Das Tool Single Object Sync führt die folgenden Schritte aus: 

 1. Ermitteln, ob die (Quell-) Domäne des Objekts (Active Directory Connector und Partition) im Synchronisierungsbereich liegt. 
 2. Ermitteln, ob die (Ziel-) Domäne des Objekts (Active Directory Connector und Partition) im Synchronisierungsbereich liegt. 
 3. Ermitteln, ob die Organisationseinheit des Objekts im Synchronisierungsbereich liegt. 
 4. Ermitteln, ob über die Anmeldeinformationen des Connectorkontos auf das Objekt zugegriffen werden kann. 
 5. Ermitteln, ob der Typ des Objekts im Synchronisierungsbereich liegt. 
 6. Ermitteln, ob das Objekt im Synchronisierungsbereich liegt, wenn die Gruppenfilterung aktiviert ist. 
 7. Importieren des Objekts aus Active Directory in den Active Directory-Connectorbereich. 
 8. Importieren des Objekts aus Azure Active Directory in den Azure Active Directory-Connectorbereich. 
 9. Synchronisieren des Objekts aus dem Active Directory Connectorbereich. 
 10. Exportieren des Objekts aus dem Azure Active Directory-Connectorbereich in Azure Active Directory. 

Zusätzlich zur JSON-Ausgabe generiert das Tool einen HTML-Bericht, der alle Details des Synchronisierungsvorgangs enthält. Der HTML-Bericht befindet sich in **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ADSyncSingleObjectSyncResult-\<date\>.htm**. Dieser HTML-Bericht kann bei Bedarf zur weiteren Problembehandlung für das Supportteam freigegeben werden. 

Der HTML-Bericht enthält Folgendes: 

|Registerkarte|BESCHREIBUNG|
|-----|-----|
|Schritte|Hier sind die zum Synchronisieren eines Objekts ausgeführten Schritte aufgeführt. Jeder Schritt enthält Details zur Problembehandlung. Die Schritte zum Importieren, Synchronisieren und Exportieren enthalten zusätzliche Attributinformationen wie Name, Mehrwertigkeit, Typ, Wert, Hinzufügen eines Werts, Löschen eines Werts, Vorgang, Synchronisierungsregel, Zuordnungstyp und Datenquelle.| 
|Problembehandlung und Empfehlung|Enthält den Fehlercode und den Grund für den Fehler. Die Fehlerinformationen sind nur verfügbar, wenn ein Fehler auftritt.| 
|geänderten Eigenschaften|Enthält den alten und den neuen Wert. Wenn kein alter Wert vorhanden ist oder der neue Wert gelöscht wird, ist die Zelle leer. Bei mehrwertigen Attributen wird die Anzahl angezeigt. Der Attributname ist ein Link zur Registerkarte „Schritte“: Exportieren des Objekts aus dem Azure Active Directory-Connectorbereich in Azure Active Directory: Attributinformationen, die zusätzliche Details des Attributs enthalten, z. B. Name, Mehrwertigkeit, Typ, Wert, Hinzufügen eines Werts, Löschen eines Werts, Vorgang, Synchronisierungsregel, Zuordnungstyp und Datenquelle.| 
|Zusammenfassung|Enthält eine Übersicht über die Vorgänge und die Bezeichner für das Objekt im Quell- und Zielsystem.| 

## <a name="prerequisites"></a>Voraussetzungen 

Verwenden Sie Folgendes, um das Single Object Sync-Tool zu nutzen:  
 - Azure AD Connect-Release von März 2021 ([1.6.4.0](reference-connect-version-history.md#1640)) oder höher
 -  [PowerShell 5.0](/powershell/scripting/windows-powershell/whats-new/what-s-new-in-windows-powershell-50)

### <a name="run-the-single-object-sync-tool"></a>Ausführen des Tools Single Object Sync 

Führen Sie zum Ausführen des Tools Single Object Sync folgende Schritte aus: 

 1. Öffnen Sie mit der Option „Als Administrator ausführen“ eine neue Windows PowerShell-Sitzung auf Ihrem Azure AD Connect-Server. 

 2. Setzen Sie die [Ausführungsrichtlinie](/powershell/module/microsoft.powershell.security/set-executionpolicy) auf „RemoteSigned“ oder „Unrestricted“. 

 3. Deaktivieren Sie den Synchronisierungsplaner, nachdem Sie bestätigt haben, dass keine Synchronisierungsvorgänge ausgeführt werden. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Importieren Sie das AdSync-Diagnosemodul. 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Rufen Sie das Single Object-Synchronisierungs-Cmdlet auf. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Aktivieren Sie den Synchronisierungsplaner erneut. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Eingabeparameter für Single Object Sync|Beschreibung| 
|-----|----|
|DistinguishedName|Dies ist ein erforderlicher Zeichenfolgenparameter. </br></br>Dies ist der Distinguished Name des Active Directory-Objekts, das Synchronisierung und Problembehandlung erfordert.| 
|StagingMode|Dies ist ein optionaler Switch-Parameter.</br></br>Dieser Parameter kann verwendet werden, um zu verhindern, dass die Änderungen in Azure Active Directory exportiert werden.</br></br>**Hinweis**: Das Cmdlet committet den Synchronisierungsvorgang. </br></br>**Hinweis**: Der Azure AD Connect-Stagingserver exportiert die Änderungen nicht in Azure Active Directory.|
|NoHtmlReport|Dies ist ein optionaler Switch-Parameter.</br></br>Dieser Parameter kann verwendet werden, um zu verhindern, dass der HTML-Bericht generiert wird. 

## <a name="single-object-sync-throttling"></a>Drosselung von Single Object Sync 

Das Tool Single Object Sync **ist** für die Untersuchung von Problemen bei der Objektsynchronisierung und deren Behandlung vorgesehen. Es ist **nicht** dafür vorgesehen, den vom Planer ausgeführten Synchronisierungszyklus zu ersetzen. Der Import aus Azure Active Directory und Export in Azure Active Directory unterliegen Drosselungslimits. Bitte unternehmen Sie nach fünf Minuten einen erneuten Versuch, wenn Sie das Drosselungslimit erreichen. 

## <a name="next-steps"></a>Nächste Schritte
- [Beheben von Problemen bei der Objektsynchronisierung mit der Azure AD Connect-Synchronisierung](tshoot-connect-objectsync.md)
- [Beheben von Problemen mit einem Objekt, das nicht mit Azure Active Directory synchronisiert wird](tshoot-connect-object-not-syncing.md)
- [End-to-End-Problembehandlung bei Azure AD Connect-Objekten und -Attributen](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)