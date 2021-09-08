---
title: 'Azure AD Connect: PowerShell-Referenz zu ADSync | Microsoft-Dokumentation'
description: Dieses Dokument enthält Referenzinformationen für das PowerShell-Modul „ADSync.psm1“.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19d3b2dc72e31cef290bd5253e7b173d624488af
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228737"
---
# <a name="azure-ad-connect--adsync-powershell-reference"></a>Azure AD Connect: PowerShell-Referenz zu ADSync
Die folgende Dokumentation enthält Referenzinformationen für das PowerShell-Modul „ADSync.psm1“, das in Azure AD Connect enthalten ist.


## <a name="add-adsyncaddsconnectoraccount"></a>Add-ADSyncADDSConnectorAccount

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Dieses Cmdlet setzt das Kennwort für das Dienstkonto zurück und aktualisiert es sowohl in Azure AD als auch im Synchronisierungsmodul.

 ### <a name="syntax"></a>SYNTAX
  #### <a name="byidentifier"></a>byIdentifier
   ```
     Add-ADSyncADDSConnectorAccount [-Identifier] <Guid> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 #### <a name="byname"></a>byName
   ```
     Add-ADSyncADDSConnectorAccount [-Name] <String> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 ### <a name="description"></a>DESCRIPTION
 Dieses Cmdlet setzt das Kennwort für das Dienstkonto zurück und aktualisiert es sowohl in Azure AD als auch im Synchronisierungsmodul.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
   ```powershell
     PS C:\> Add-ADSyncADDSConnectorAccount -Name contoso.com -EACredential $EAcredentials
   ```

 Setzt das Kennwort des Dienstkontos zurück, das mit contoso.com verbunden ist.

 ### <a name="parameters"></a>PARAMETERS

   #### <a name="-eacredential"></a>-EACredential
   Anmeldeinformationen für ein Unternehmensadministratorkonto in Active Directory.

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
   ```

  #### <a name="-identifier"></a>-Identifier
  Bezeichner des Connectors, dessen Dienstkontokennwort zurückgesetzt werden muss.

   ```yaml
     Type: Guid
     Parameter Sets: byIdentifier
     Aliases:

     Required: True
     Position: 0
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="-name"></a>-Name
  Name des Connectors.

   ```yaml
     Type: String
     Parameter Sets: byName
     Aliases:
 
     Required: True
     Position: 1
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="commonparameters"></a>CommonParameters
  Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

  #### <a name="systemguid"></a>System.Guid

  #### <a name="systemstring"></a>System.String

 ### <a name="outputs"></a>AUSGABEN

  #### <a name="systemobject"></a>System.Object



## <a name="disable-adsyncexportdeletionthreshold"></a>Disable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Deaktiviert das Feature für den Löschschwellenwert in der Exportphase.

 ### <a name="syntax"></a>SYNTAX
   
   ```
     Disable-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm]
     [<CommonParameters>]
   ```

 ### <a name="description"></a>DESCRIPTION
 Deaktiviert das Feature für den Löschschwellenwert in der Exportphase.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
   ```powershell
     PS C:\> Disable-ADSyncExportDeletionThreshold -AADCredential $aadCreds
   ```

 Verwendet die bereitgestellten AAD-Anmeldeinformationen, um das Feature für den Löschschwellenwert in der Exportphase zu deaktivieren.

 ### <a name="parameters"></a>PARAMETERS

  #### <a name="-aadcredential"></a>-AADCredential
  Die AAD-Anmeldeinformationen.

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: 1
     Default value: None
     Accept pipeline input: True (ByPropertyName)
     Accept wildcard characters: False
  ```

  #### <a name="-confirm"></a>-Confirm
  Parameterschalter für die Aufforderung zur Bestätigung.

 ```yaml
     Type: SwitchParameter
     Parameter Sets: (All)
     Aliases: cf

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
 ```

  #### <a name="-whatif"></a>-WhatIf
  Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

 ```yaml
     Type: SwitchParameter
     Parameter Sets: (All)
     Aliases: wi

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

  #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>AUSGABEN

  #### <a name="systemobject"></a>System.Object

## <a name="enable-adsyncexportdeletionthreshold"></a>Enable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Aktiviert das Schwellenwertfeature „Exportlöschung“ und legt einen Wert für den Schwellenwert fest.

 ### <a name="syntax"></a>SYNTAX

 ```
 Enable-ADSyncExportDeletionThreshold [-DeletionThreshold] <UInt32> [[-AADCredential] <PSCredential>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Aktiviert das Schwellenwertfeature „Exportlöschung“ und legt einen Wert für den Schwellenwert fest.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Enable-ADSyncExportDeletionThreshold -DeletionThreshold 777 -AADCredential $aadCreds
 ```

 Aktiviert das Schwellenwertfeature „Exportlöschung“ und legt den Schwellenwert für den Löschvorgang auf 777 fest.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 Die AAD-Anmeldeinformationen.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 Hiermit werden Sie vor der Ausführung des Cmdlets zur Bestätigung aufgefordert.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-deletionthreshold"></a>-DeletionThreshold
 Der Schwellenwert für den Löschvorgang.

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemuint32"></a>System.UInt32
 
 #### <a name="sytemmanagementautomationpscredential"></a>Sytem.Management.Automation.PSCredential

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncautoupgrade"></a>Get-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft den Status von AutoUpgrade für Ihre Installation ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncAutoUpgrade [-Detail] [<CommonParameters>]
 ``` 

 ### <a name="description"></a>DESCRIPTION
 Ruft den Status von AutoUpgrade für Ihre Installation ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncAutoUpgrade -Detail
 ```

 Gibt den AutoUpgrade-Status der Installation zurück und zeigt den Grund für das Anhalten an, wenn AutoUpgrade angehalten wird.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-detail"></a>-Detail
 Wenn der AutoUpgrade-Zustand „Angehalten“ ist, zeigt dieser Parameter den Grund für das Anhalten.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobject"></a>Get-ADSyncCSObject

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft das angegebene Connectorbereichsobjekt ab.

 ### <a name="syntax"></a>SYNTAX
 
 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Get-ADSyncCSObject [-Identifier] <Guid> [<CommonParameters>]
 ```
 
 #### <a name="searchbyconnectoridentifierdistinguishedname"></a>SearchByConnectorIdentifierDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectoridentifier"></a>SearchByConnectorIdentifier
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectornamedistinguishedname"></a>SearchByConnectorNameDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectorname"></a>SearchByConnectorName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ruft das angegebene Connectorbereichsobjekt ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 Ruft das CS-Objekt für den Benutzer „fabrikam“ in der Domäne contoso.com ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Der Bezeichner des Connectors.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier 
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Der Name des Connectors.

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 Der Distinguished Name des Connectorbereichsobjekts.

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Der Bezeichner des Connectorbereichsobjekts.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-maxresultcount"></a>-MaxResultCount
 Die maximale Anzahl des Resultsets.

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-skipdnvalidation"></a>-SkipDNValidation
 Parameterschalter zum Überspringen der DN-Überprüfung.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-startindex"></a>-StartIndex
 Der Startindex, aus dem die Anzahl zurückgeben werden soll.

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-transient"></a>-Transient
 Parameterschalter zum Abrufen vorübergehender CS-Objekte.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier, SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobjectlog"></a>Get-ADSyncCSObjectLog

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft Protokolleinträge des Connectorbereichsobjekts ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncCSObjectLog [-Identifier] <Guid> [-Count] <UInt32> [<CommonParameters>]
 ```
 
 ### <a name="description"></a>DESCRIPTION
 Ruft Protokolleinträge des Connectorbereichsobjekts ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncCSObjectLog -Identifier "00000000-0000-0000-0000-000000000000" -Count 1
 ```

 Gibt ein einzelnes Objekt mit dem angegebenen Bezeichner zurück.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-count"></a>-Count
 Erwartete abzurufende maximale Anzahl der Protokolleinträge des Connectorbereichsobjekts.

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Der Objektbezeichner des Connectorbereichs.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncdatabaseconfiguration"></a>Get-ADSyncDatabaseConfiguration

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft die Konfiguration der ADSync-Datenbank ab.
 
 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncDatabaseConfiguration [<CommonParameters>]
 ```
 
 ### <a name="description"></a>DESCRIPTION
 Ruft die Konfiguration der ADSync-Datenbank ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncDatabaseConfiguration
 ```

 Ruft die Konfiguration der ADSync-Datenbank ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncexportdeletionthreshold"></a>Get-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft den Schwellenwert für das Löschen des Exports aus AAD ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ruft den Schwellenwert für das Löschen des Exports aus AAD ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncExportDeletionThreshold -AADCredential $aadCreds
 ```

 Ruft den Schwellenwert für das Löschen des Exports aus AAD mithilfe der angegebenen AAD-Anmeldeinformationen ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 Die AAD-Anmeldeinformationen.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 Hiermit werden Sie vor der Ausführung des Cmdlets zur Bestätigung aufgefordert.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncmvobject"></a>Get-ADSyncMVObject

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft ein Metaverse-Objekt ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncMVObject -Identifier <Guid> [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ruft ein Metaverse-Objekt ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncMVObject -Identifier "00000000-0000-0000-0000-000000000000"
 ```

 Ruft das Metaverse-Objekt mit dem angegebenen Bezeichner ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-identifier"></a>-Identifier
 Der Bezeichner des Metaverse-Objekts.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncrunprofileresult"></a>Get-ADSyncRunProfileResult

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Verarbeitet die Eingaben des Clients und ruft die Ergebnisse des Ausführungsprofils ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunProfileResult [-RunHistoryId <Guid>] [-ConnectorId <Guid>] [-RunProfileId <Guid>]
 [-RunNumber <Int32>] [-NumberRequested <Int32>] [-RunStepDetails] [-StepNumber <Int32>] [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Verarbeitet die Eingaben des Clients und ruft die Ergebnisse des Ausführungsprofils ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncRunProfileResult -ConnectorId "00000000-0000-0000-0000-000000000000"
 ```

 Ruft alle Ergebnisse des Synchronisierungsausführungsprofils für den angegebenen Connector ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-confirm"></a>-Confirm
 Hiermit werden Sie vor der Ausführung des Cmdlets zur Bestätigung aufgefordert.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorid"></a>-ConnectorId
 Der Connectorbezeichner.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-numberrequested"></a>-NumberRequested
 Die maximale Anzahl von Rückgaben.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 Der Bezeichner einer bestimmten Ausführung.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runnumber"></a>-RunNumber
 Die Ausführungsnummer einer bestimmten Ausführung.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofileid"></a>-RunProfileId
 Der Bezeichner des Ausführungsprofils einer bestimmten Ausführung.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runstepdetails"></a>-RunStepDetails
 Parameterschalter für Details zum Ausführungsschritt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 Filtert nach Schrittnummer.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncrunstepresult"></a>Get-ADSyncRunStepResult

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft das Ergebnis des ADSync-Schritts ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunStepResult [-RunHistoryId <Guid>] [-StepHistoryId <Guid>] [-First] [-StepNumber <Int32>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ruft das Ergebnis des ADSync-Schritts ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncRunStepResult -RunHistoryId "00000000-0000-0000-0000-000000000000"
 ```

 Ruft das Ergebnis des ADSync-Schritts der angegebenen Ausführung ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-confirm"></a>-Confirm
 Hiermit werden Sie vor der Ausführung des Cmdlets zur Bestätigung aufgefordert.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-first"></a>-First
 Parameterschalter zum Abrufen nur des ersten Objekts.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 Die ID einer bestimmten Ausführung.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stephistoryid"></a>-StepHistoryId
 Die ID einer bestimmten Ausführung.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 Die Schrittnummer.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object



## <a name="get-adsyncscheduler"></a>Get-ADSyncScheduler

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft die aktuellen Synchronisierungszykluseinstellungen für den Synchronisierungsplaner ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncScheduler [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ruft die aktuellen Synchronisierungszykluseinstellungen für den Synchronisierungsplaner ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Get-ADSyncScheduler
 ```

 Ruft die aktuellen Synchronisierungszykluseinstellungen für den Synchronisierungsplaner ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncschedulerconnectoroverride"></a>Get-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft die Überschreibungswerte des AD-Synchronisierungsplaners für die angegebenen Connectors ab.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncSchedulerConnectorOverride [-ConnectorIdentifier <Guid>] [-ConnectorName <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ruft die Überschreibungswerte des AD-Synchronisierungsplaners für die angegebenen Connectors ab.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1 
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride -ConnectorName "contoso.com"
 ```

 Ruft die Überschreibungswerte für den ADSync-Synchronisierungsplaner für den Connector contoso.com ab.

 #### <a name="example-2"></a>Beispiel 2
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride
 ```

 Ruft alle Überschreibungswerte für den ADSync-Synchronisierungsplaner ab.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Der Connectorbezeichner.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Der Connectorname.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsynccsobjectpasswordhashsync"></a>Invoke-ADSyncCSObjectPasswordHashSync

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Synchronisiert den Kennworthash für das angegebene AD-Connectorbereichsobjekt.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="searchbydistinguishedname"></a>SearchByDistinguishedName
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-ConnectorName] <String> [-DistinguishedName] <String>
 [<CommonParameters>]
 ```

 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-Identifier] <Guid> [<CommonParameters>]
 ```

 #### <a name="csobject"></a>CSObject
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-CsObject] <CsObject> [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Synchronisiert den Kennworthash für das angegebene AD-Connectorbereichsobjekt.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Invoke-ADSyncCSObjectPasswordHashSync -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DN=contoso,DN=com"
 ```

 Synchronisiert den Kennworthash für das angegebene Objekt.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 Der Name des Connectors.

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-csobject"></a>-CsObject
 Connectorbereichsobjekt.

 ```yaml
 Type: CsObject
 Parameter Sets: CSObject
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 Distinguished Name des Connectorbereichsobjekts.

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Der Bezeichner des Connectorbereichsobjekts.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Ruft ein bestimmtes Ausführungsprofil auf.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname"></a>ConnectorName
 ```
 Invoke-ADSyncRunProfile -ConnectorName <String> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Invoke-ADSyncRunProfile -ConnectorIdentifier <Guid> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ruft ein bestimmtes Ausführungsprofil auf.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Invoke-ADSyncRunProfile -ConnectorName "contoso.com" -RunProfileName Export
 ```

 Ruft einen Export für den Connector contoso.com auf.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Der Bezeichner des Connectors.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Name des Connectors.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-resume"></a>-Resume
 Parameterschalter, um zu versuchen, ein zuvor angehaltenes/halbfertiges Ausführungsprofil fortzusetzen.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofilename"></a>-RunProfileName
 Name des Ausführungsprofils, das für den ausgewählten Connector aufgerufen werden soll.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="remove-adsyncaadserviceaccount"></a>Remove-ADSyncAADServiceAccount

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Löscht ein/alle vorhandenen AAD-Dienstkonten im AAD-Mandanten (der mit den angegebenen Anmeldeinformationen verknüpft ist).

 ### <a name="syntax"></a>SYNTAX

 #### <a name="serviceaccount"></a>ServiceAccount
 ```
 Remove-ADSyncAADServiceAccount [-AADCredential] <PSCredential> [-Name] <String> [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 #### <a name="serviceprincipal"></a>ServicePrincipal
 ```
 Remove-ADSyncAADServiceAccount [-ServicePrincipal] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Löscht ein/alle vorhandenen AAD-Dienstkonten im AAD-Mandanten (der mit den angegebenen Anmeldeinformationen verknüpft ist).

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Remove-ADSyncAADServiceAccount -AADCredential $aadcreds -Name contoso.com
 ```

 Löscht alle vorhandenen AAD-Dienstkonten in contoso.com.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 Die AAD-Anmeldeinformationen.

 ```yaml
 Type: PSCredential
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 Hiermit werden Sie vor der Ausführung des Cmdlets zur Bestätigung aufgefordert.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-name"></a>-Name
 Der Kontoname.

 ```yaml
 Type: String
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-serviceprincipal"></a>-ServicePrincipal
 Der Dienstprinzipal des Kontos.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: ServicePrincipal
 Aliases:

 Required: True
 Position: 3
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 #### <a name="systemstring"></a>System.String

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncautoupgrade"></a>Set-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Stellt den AutoUpgrade-Status Ihrer Installation zwischen „Aktiviert“ und „Deaktiviert“ um.

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncAutoUpgrade [-AutoUpgradeState] <AutoUpgradeConfigurationState> [[-SuspensionReason] <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Legt den AutoUpgrade-Status für Ihre Installation fest. Dieses Cmdlet sollte nur verwendet werden, um den AutoUpgrade-Status zwischen „Aktiviert“ und „Deaktiviert“ umzustellen. Nur das System darf den Status auf „Angehalten“ festlegen.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Set-ADSyncAutoUpgrade -AutoUpgradeState Enabled
 ```

 Legt den AutoUpgrade-Status auf „Aktiviert“ fest.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-autoupgradestate"></a>-AutoUpgradeState
 Der AutoUpgrade-Status. Zulässige Werte: Suspended, Enabled, Disabled (Angehalten, Aktiviert, Deaktiviert).

 ```yaml
 Type: AutoUpgradeConfigurationState
 Parameter Sets: (All)
 Aliases:
 Accepted values: Suspended, Enabled, Disabled

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-suspensionreason"></a>-SuspensionReason
 Der Grund für das Anhalten. Nur das System sollte den AutoUpgrade-Status auf „Angehalten“ festlegen.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncscheduler"></a>Set-ADSyncScheduler

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Legt die aktuellen Synchronisierungszykluseinstellungen für den Synchronisierungsplaner fest.

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncScheduler [[-CustomizedSyncCycleInterval] <TimeSpan>] [[-SyncCycleEnabled] <Boolean>]
 [[-NextSyncCyclePolicyType] <SynchronizationPolicyType>] [[-PurgeRunHistoryInterval] <TimeSpan>]
 [[-MaintenanceEnabled] <Boolean>] [[-SchedulerSuspended] <Boolean>] [-Force] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Legt die aktuellen Synchronisierungszykluseinstellungen für den Synchronisierungsplaner fest.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Set-ADSyncScheduler -SyncCycleEnabled $true
 ```

 Legt die aktuelle Einstellung für den Synchronisierungszyklus für SyncCycleEnabled auf TRUE fest.
 
 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-customizedsynccycleinterval"></a>-CustomizedSyncCycleInterval
 Geben Sie den Zeitspannenwert für das benutzerdefinierte Synchronisierungsintervall an, das Sie festlegen möchten.
Wenn Sie mit der niedrigsten zulässigen Einstellung arbeiten möchten, legen Sie diesen Parameter auf NULL fest.

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-force"></a>-Force
 Parameterschalter zum Erzwingen der Einstellung eines Werts.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 6
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-maintenanceenabled"></a>-MaintenanceEnabled
 Parameter zum Festlegen von MaintenanceEnabled.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 4
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-nextsynccyclepolicytype"></a>-NextSyncCyclePolicyType
 Parameter zum Festlegen von NextSyncCyclePolicyType. Zulässige Werte: Unspecified, Delta, Initial (Nicht angegeben, Delta, Anfänglich).

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 Parameter zum Festlegen von PurgeRunHistoryInterval.

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-schedulersuspended"></a>-SchedulerSuspended
 Parameter zum Festlegen von SchedulerSuspended.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 5
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-synccycleenabled"></a>-SyncCycleEnabled
 Parameter zum Festlegen von SyncCycleEnabled.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemnullable1systemtimespan-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.TimeSpan, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncschedulerconnectoroverride"></a>Set-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Legt die aktuellen Synchronisierungszykluseinstellungen für den Synchronisierungsplaner fest.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 #### <a name="connectorname"></a>ConnectorName
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorName <String> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Legt die aktuellen Synchronisierungszykluseinstellungen für den Synchronisierungsplaner fest.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Set-ADSyncSchedulerConnectorOverride -Connectorname "contoso.com" -FullImportRequired $true
 -FullSyncRequired $false
 ```

 Legt die Einstellungen für den Synchronisierungszyklus für den Connector „contoso.com“ so fest, dass ein vollständiger Import und keine vollständige Synchronisierung erforderlich ist.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Der Connectorbezeichner.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Der Connectorname.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullimportrequired"></a>-FullImportRequired
 Auf TRUE festlegen, um den vollständigen Import im nächsten Zyklus anzufordern.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullsyncrequired"></a>-FullSyncRequired
 Auf TRUE festlegen, um eine vollständige Synchronisierung im nächsten Zyklus anzufordern.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemguid"></a>System.Guid

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="start-adsyncpurgerunhistory"></a>Start-ADSyncPurgeRunHistory

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Cmdlet zum Löschen des Ausführungsverlaufs, der älter als die angegebene Zeitspanne ist.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="online"></a>online
 ```
 Start-ADSyncPurgeRunHistory [[-PurgeRunHistoryInterval]  <TimeSpan>] [<CommonParameters>]
 ```

 #### <a name="offline"></a>Offline
 ```
 Start-ADSyncPurgeRunHistory [-Offline] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Cmdlet zum Löschen des Ausführungsverlaufs, der älter als die angegebene Zeitspanne ist.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Start-ADSyncPurgeRunHistory -PurgeRunHistoryInterval (New-Timespan -Hours 5)
 ```

 Löscht den gesamten Ausführungsverlauf, der älter als 5 Stunden ist.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-offline"></a>-Offline
 Löscht den gesamten Ausführungsverlauf aus der Datenbank, während der Dienst offline ist.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: offline
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 Intervall, für das der Verlauf beibehalten werden soll.

 ```yaml
 Type: TimeSpan
 Parameter Sets: online
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemtimespan"></a>System.TimeSpan

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object

## <a name="start-adsyncsynccycle"></a>Start-ADSyncSyncCycle

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Löst einen Synchronisierungszyklus aus.

 ### <a name="syntax"></a>SYNTAX

 ```
 Start-ADSyncSyncCycle [[-PolicyType] <SynchronizationPolicyType>] [[-InteractiveMode] <Boolean>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Löst einen Synchronisierungszyklus aus.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Start-ADSyncSyncCycle -PolicyType Initial
 ```

 Löst einen Synchronisierungszyklus mit dem Richtlinientyp „Initial“ (Anfänglich) aus.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-interactivemode"></a>-InteractiveMode
 Unterscheidet zwischen interaktivem Modus (Befehlszeile) und Skript-/Codemodus (Aufrufe aus anderem Code).

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-policytype"></a>-PolicyType
 Der Typ der auszuführenden Richtlinie. Zulässige Werte: Unspecified, Delta, Initial (Nicht angegeben, Delta, Anfänglich).

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemboolean"></a>System.Boolean

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncrunprofile"></a>Stop-ADSyncRunProfile

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Sucht und beendet alle oder angegebene ausgelastete Connectors.

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncRunProfile [[-ConnectorName] <String>] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Sucht und beendet alle oder angegebene ausgelastete Connectors.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Stop-ADSyncRunProfile -ConnectorName "contoso.com"
 ```

 Beendet alle ausgeführten Synchronisierungen für contoso.com.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 Name des Connectors.
Falls nicht angegeben, werden alle ausgelasteten Connectors beendet.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncsynccycle"></a>Stop-ADSyncSyncCycle

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Signalisiert dem Server, den aktuell ausgeführten Synchronisierungszyklus zu beenden.

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncSyncCycle [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Signalisiert dem Server, den aktuell ausgeführten Synchronisierungszyklus zu beenden.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Stop-ADSyncSyncCycle
 ```

 Signalisiert dem Server, den aktuell ausgeführten Synchronisierungszyklus zu beenden.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="sync-adsynccsobject"></a>Sync-ADSyncCSObject

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Führt die Synchronisierungsvorschau für das Connectorbereichsobjekt aus.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname_objectdn"></a>ConnectorName_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorName <String> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier_objectdn"></a>ConnectorIdentifier_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorIdentifier <Guid> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="objectidentifier"></a>ObjectIdentifier
 ```
 Sync-ADSyncCSObject -Identifier <Guid> [-Commit] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Führt die Synchronisierungsvorschau für das Connectorbereichsobjekt aus.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Sync-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 Gibt eine Synchronisierungsvorschau für das angegebene Objekt zurück.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-commit"></a>-Commit
 Parameterschalter für Commit.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Der Bezeichner des Connectors.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Der Name des Connectors.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 Distinguished Name des Connectorbereichsobjekts.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN, ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Der Bezeichner des Connectorbereichsobjekts.

 ```yaml
 Type: Guid
 Parameter Sets: ObjectIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="none"></a>Keine

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncazureserviceconnectivity"></a>Test-AdSyncAzureServiceConnectivity

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Untersucht und identifiziert Konnektivitätsprobleme in Azure AD.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="byenvironment"></a>ByEnvironment
 ```
 Test-AdSyncAzureServiceConnectivity [-AzureEnvironment] <Identifier> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 #### <a name="bytenantname"></a>ByTenantName
 ```
 Test-AdSyncAzureServiceConnectivity [-Domain] <String> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Untersucht und identifiziert Konnektivitätsprobleme in Azure AD.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Test-AdSyncAzureServiceConnectivity -AzureEnvironment Worldwide -Service SecurityTokenService -CurrentUser
 ```

 Gibt TRUE zurück, wenn keine Konnektivitätsprobleme vorliegen.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-azureenvironment"></a>-AzureEnvironment
 Zu testende Azure-Umgebung. Zulässige Werte: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default.

 ```yaml
 Type: Identifier
 Parameter Sets: ByEnvironment
 Aliases:
 Accepted values: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-currentuser"></a>-CurrentUser
 Der Benutzer, der das Cmdlet ausführt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-domain"></a>-Domain
 Die Domäne, deren Konnektivität getestet wird.

 ```yaml
 Type: String
 Parameter Sets: ByTenantName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-service"></a>-Service
 Der Dienst, dessen Konnektivität getestet wird.

 ```yaml
 Type: AzureService
 Parameter Sets: (All)
 Aliases:
 Accepted values: SecurityTokenService, AdminWebService

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="microsoftonlinedeploymentclientframeworkmicrosoftonlineinstanceidentifier"></a>Microsoft.Online.Deployment.Client.Framework.MicrosoftOnlineInstance+Bezeichner

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1microsoftonlinedeploymentclientframeworkazureservice-microsoftonlinedeploymentclientframework-version1600-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.Online.Deployment.Client.Framework.AzureService, Microsoft.Online.Deployment.Client.Framework, Version=1.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncuserhaspermissions"></a>Test-AdSyncUserHasPermissions

 ### <a name="synopsis"></a>ZUSAMMENFASSUNG
 Cmdlet, um zu überprüfen, ob der ADMA-Benutzer die erforderlichen Berechtigungen hat.

 ### <a name="syntax"></a>SYNTAX

 ```
 Test-AdSyncUserHasPermissions [-ForestFqdn] <String> [-AdConnectorId] <Guid>
 [-AdConnectorCredential] <PSCredential> [-BaseDn] <String> [-PropertyType] <String> [-PropertyValue] <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Cmdlet, um zu überprüfen, ob der ADMA-Benutzer die erforderlichen Berechtigungen hat.

 ### <a name="examples"></a>BEISPIELE

 #### <a name="example-1"></a>Beispiel 1
 ```powershell
 PS C:\> Test-AdSyncUserHasPermissions -ForestFqdn "contoso.com" -AdConnectorId "00000000-0000-0000-000000000000"
 -AdConnectorCredential $connectorAcctCreds -BaseDn "CN=fabrikam,CN=Users,DC=contoso,DC=com" -PropertyType "Allowed-Attributes" -PropertyValue "name"
 ```

 Überprüft, ob der ADMA-Benutzer Berechtigungen für den Zugriff auf die Eigenschaft „name“ des Benutzers „fabrikam“ hat.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-adconnectorcredential"></a>-AdConnectorCredential
 Anmeldeinformationen für das Konto des AD-Connectors.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-adconnectorid"></a>-AdConnectorId
 AD-Connector-ID.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-basedn"></a>-BaseDn
 Basis-DN des zu überprüfende Objekts.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 3
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 Hiermit werden Sie vor der Ausführung des Cmdlets zur Bestätigung aufgefordert.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-forestfqdn"></a>-ForestFqdn
 Name der Gesamtstruktur.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-propertytype"></a>-PropertyType
 Der gesuchte Berechtigungstyp. Zulässige Werte: Allowed-Attributes, Allowed-Attributes-Effective, Allowed-Child-Classes, Allowed-Child-Classes-Effective.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 4
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-propertyvalue"></a>-PropertyValue
 Der Wert, den Sie im PropertyType-Attribut suchen.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 5
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>EINGABEN

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>AUSGABEN

 #### <a name="systemobject"></a>System.Object

## <a name="next-steps"></a>Nächste Schritte

- [Was ist eine Hybrididentität?](./whatis-hybrid-identity.md)
- [Worum handelt es sich bei Azure AD Connect und Connect Health?](whatis-azure-ad-connect.md)