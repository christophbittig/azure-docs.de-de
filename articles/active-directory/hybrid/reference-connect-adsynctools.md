---
title: 'Azure AD Connect: PowerShell-Referenz zu ADSyncTools | Microsoft-Dokumentation'
description: Dieses Dokument enthält Referenzinformationen für das PowerShell-Modul „ADSyncTools.psm1“.
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
ms.openlocfilehash: 768b037573d1114141ca9722feb6a9ecd510dd5a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339435"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:  PowerShell-Referenz zu ADSyncTools
Die folgende Dokumentation enthält Referenzinformationen für das PowerShell-Modul „ADSyncTools.psm1“, das in Azure AD Connect enthalten ist.

## <a name="install-the-adsynctools-powershell-module"></a>Installieren des PowerShell-Moduls „ADSyncTools“
Gehen Sie folgendermaßen vor, um das PowerShell-Modul ADSyncTools zu installieren:

1.  Öffnen Sie Windows PowerShell mit Administratorrechten.
2.  Geben Sie den folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Drücken Sie die EINGABETASTE.
4.  Um zu überprüfen, ob das Modul installiert wurde, geben Sie folgenden Befehl ein (Sie können ihn auch kopieren und einfügen):
    ```powershell
    Get-module AdSyncTools
    ```
5.  Jetzt sollten Informationen zum Modul angezeigt werden.


## <a name="clear-adsynctoolsmsdsconsistencyguid"></a>Clear-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Löschen der mS-DS-ConsistencyGuid für ein Active Directory-Objekt
### SYNTAX
```
Clear-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Löscht den Wert in der mS-DS-ConsistencyGuid für das Active Directory-Zielobjekt.
Unterstützt Active Directory-Objekte in Gesamtstrukturen mit mehreren Domänen.

### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>BEISPIEL 3
```
'User1@Contoso.com' | Clear-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Zielobjekt in AD zum Löschen von mS-DS-ConsistencyGuid
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="connect-adsynctoolssqldatabase"></a>Connect-ADSyncToolsSqlDatabase
### SYNOPSIS
Verbindungsherstellung mit einer SQL-Datenbank zu Testzwecken
### SYNTAX
```
Connect-ADSyncToolsSqlDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>]
 [[-Port] <String>] [[-UserName] <String>] [[-Password] <String>] [<CommonParameters>]
```
### DESCRIPTION
Funktionen und Hilfsprogramme für die SQL-Diagnose
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Database 'ADSync'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Instance 'INTANCE01' -Database 'ADSync'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server Name
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-instance"></a>-Instance
SQL Server-Instanzname
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-database"></a>-Database
Name der SQL Server-Datenbank
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
SQL Server-Port (z. B.
49823)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-username"></a>-UserName
Benutzername für die SQL Server-Anmeldung
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-password"></a>-Password
Kennwort für die SQL Server-Anmeldung
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertfrom-adsynctoolsaaddistinguishedname"></a>ConvertFrom-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Konvertieren des DistinguishedName für einen Azure AD-Connector in eine ImmutableId
### SYNTAX
```
ConvertFrom-ADSyncToolsAadDistinguishedName [-DistinguishedName] <String> [<CommonParameters>]
```
### DESCRIPTION
Konvertiert den DistinguishedName für einen Azure AD-Connector, beispielsweise CN={514635484D4B376E38307176645973555049486139513D3D}, in den entsprechenden Base64-ImmutableID-Wert konvertiert, z. B. QF5HMK7n80qvdYsUPIHa9Q==.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
ConvertFrom-ADSyncToolsAadDistinguishedName 'CN={514635484D4B376E38307176645973555049486139513D3D}'
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName für den Azure AD-Connectorbereich
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertfrom-adsynctoolsimmutableid"></a>ConvertFrom-ADSyncToolsImmutableID
### SYNOPSIS
Konvertieren einer Base64-ImmutableId (SourceAnchor) in einen GUID-Wert
### SYNTAX
```
ConvertFrom-ADSyncToolsImmutableID [-Value] <String> [<CommonParameters>]
```
### DESCRIPTION
Konvertiert den Wert der ImmutableID aus einer Base64-Zeichenfolge in einen GUID-Wert und gibt ihn zurück. Falls die Base64-Zeichenfolge nicht in eine GUID konvertiert werden kann, wird ein Bytearray zurückgegeben.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
ConvertFrom-ADSyncToolsImmutableID 'iGhmiAEBERG7uxI0VniQqw=='
```
#### <a name="example-2"></a>BEISPIEL 2
```
'iGhmiAEBERG7uxI0VniQqw==' | ConvertFrom-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
ImmutableId im Base64-Format
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolsaaddistinguishedname"></a>ConvertTo-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Konvertieren einer ImmutableId in den DistinguishedName für einen Azure AD-Connector
### SYNTAX
```
ConvertTo-ADSyncToolsAadDistinguishedName [-ImmutableId] <String> [<CommonParameters>]
```
### DESCRIPTION
Konvertiert eine ImmutableId (SourceAnchor), beispielsweise QF5HMK7n80qvdYsUPIHa9Q==, in den entsprechenden DistinguishedName-Wert für einen Azure AD-Connector, z. B. CN={514635484D4B376E38307176645973555049486139513D3D}.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
ConvertTo-ADSyncToolsAadDistinguishedName 'QF5HMK7n80qvdYsUPIHa9Q=='
```
### PARAMETERS
#### <a name="-immutableid"></a>-ImmutableId
ImmutableId (SourceAnchor)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolscloudanchor"></a>ConvertTo-ADSyncToolsCloudAnchor
### SYNOPSIS
Konvertieren eines Base64-Ankers in einen CloudAnchor
### SYNTAX
```
ConvertTo-ADSyncToolsCloudAnchor [-Anchor] <String> [<CommonParameters>]
```
### DESCRIPTION
Konvertiert einen Base64-Anker wie VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA== in den entsprechenden CloudAnchor-Wert, z. B. User_abc12345-1234-abcd-9876-ab0123456789.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
ConvertTo-ADSyncToolsCloudAnchor "VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA=="
```
#### <a name="example-2"></a>BEISPIEL 2
```
"VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA==" | ConvertTo-ADSyncToolsCloudAnchor
```
### PARAMETERS
#### <a name="-anchor"></a>-Anchor
Base64-Anker
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolsimmutableid"></a>ConvertTo-ADSyncToolsImmutableID
### SYNOPSIS
Konvertieren einer GUID (ObjectGUID/ms-Ds-Consistency-Guid) in eine Base64-Zeichenfolge
### SYNTAX
```
ConvertTo-ADSyncToolsImmutableID [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
Konvertiert einen Wert im GUID-, GUID-Zeichenfolgen- oder Bytearrayformat in eine Base64-Zeichenfolge.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
ConvertTo-ADSyncToolsImmutableID '88888888-0101-3333-cccc-1234567890cd'
```
#### <a name="example-2"></a>BEISPIEL 2
```
'88888888-0101-3333-cccc-1234567890cd' | ConvertTo-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
GUID, GUID-Zeichenfolge oder Bytearray
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolsaaddisconnectors"></a>Export-ADSyncToolsAadDisconnectors
### SYNOPSIS
Exportieren von Azure AD-Disconnector-Objekten
### SYNTAX
```
Export-ADSyncToolsAadDisconnectors [[-SyncObjectType] <Object>] [<CommonParameters>]
```
### DESCRIPTION
Führt das CSExport-Tool aus, um alle Disconnector-Objekte nach XML zu exportieren. Anschließend wird diese XML-Ausgabe in eine CSV-Datei mit folgendem Inhalt konvertiert: UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId, CloudAnchor
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Export-ADSyncToolsDisconnectors -SyncObjectType 'PublicFolder'
```
Exportiert alle PublicFolder-Disconnector-Objekte in eine CSV.
#### <a name="example-2"></a>BEISPIEL 2
```
Export-ADSyncToolsDisconnectors
```
Exportiert alle Disconnector-Objekte in eine CSV.
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
In die Ausgabe einzuschließender ObjectType
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
Verwenden Sie das ObjectType-Argument in Fällen, in denen Sie Disconnector-Objekte nur für einen bestimmten Objekttyp exportieren möchten.
### OUTPUTS
Exportiert eine CSV-Datei mit Disconnector-Objekten, die Folgendes enthält: 

UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId, CloudAnchor

## <a name="export-adsynctoolshybridaadjoinreport"></a>Export-ADSyncToolsHybridAadJoinReport
### SYNOPSIS
Generiert einen Bericht zu den Zertifikaten, die in Active Directory-Computerobjekten gespeichert sind, insbesondere zu Zertifikaten, die über das Azure AD Hybrid Join-Feature ausgestellt wurden.
### SYNTAX
#### <a name="singleobject"></a>SingleObject
```
Export-ADSyncToolsHybridAadJoinReport [-DN] <String> [[-Filename] <String>] [<CommonParameters>]
```
#### <a name="multipleobjects"></a>MultipleObjects
```
Export-ADSyncToolsHybridAadJoinReport [-OU] <String> [[-Filename] <String>] [<CommonParameters>]
```
### DESCRIPTION
Dieses Tool sucht nach allen Zertifikaten, die in der Eigenschaft „UserCertificate“ eines Computer-Objekts in AD vorhanden sind, und überprüft für jedes vorhandene, nicht abgelaufene Zertifikat, ob es für das Azure AD Hybrid Join-Feature ausgestellt wurde (in diesem Fall lautet der Name des Antragstellers „CN={ObjectGUID}“).
Vor Version 1.4 hat Azure AD Connect jeden Computer mit mindestens einem gültigen Zertifikat mit Azure AD synchronisiert. Ab Azure AD Connect-Version 1.4 kann die ADSync-Engine jedoch Azure AD Hybrid Join-Zertifikate identifizieren und das Computerobjekt mittels „Cloudfilter“ von der Synchronisierung mit Azure AD ausschließen, sofern kein gültiges Azure AD Hybrid Join-Zertifikat vorhanden ist.
Azure AD-Geräteobjekte, die bereits mit AD synchronisiert wurden, jedoch über kein gültiges Azure AD Hybrid Join-Zertifikat verfügen, werden von Azure AD Connect aus Azure AD gelöscht (CloudFiltered=TRUE).
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -DN 'CN=Computer1,OU=SYNC,DC=Fabrikam,DC=com'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -OU 'OU=SYNC,DC=Fabrikam,DC=com' -Filename "MyHybridAzureADjoinReport.csv" -Verbose
```
### PARAMETERS
#### <a name="-dn"></a>-DN
DistinguishedName des Computers
```yaml
Type: String
Parameter Sets: SingleObject
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-ou"></a>-OU
AD-OrganizationalUnit
```yaml
Type: String
Parameter Sets: MultipleObjects
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filename"></a>-Filename
Dateiname der CSV-Ausgabe (optional)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Weitere Informationen: [Grundlegendes zu Azure AD Connect 1.4.xx.x und nicht mehr angezeigten Geräten](/troubleshoot/azure/active-directory/reference-connect-device-disappearance).

## <a name="export-adsynctoolsobjects"></a>Export-ADSyncToolsObjects
### SYNOPSIS
Exportieren Azure AD Connect-Objekten in XML-Dateien
### SYNTAX
#### <a name="objectid"></a>ObjectID
```
Export-ADSyncToolsObjects [-ObjectId] <Object> [-Source] <Object> [-ExportSerialized] [<CommonParameters>]
```
#### <a name="distinguishedname"></a>DistinguishedName
```
Export-ADSyncToolsObjects [-DistinguishedName] <Object> [-ConnectorName] <Object> [-ExportSerialized]
 [<CommonParameters>]
```
### DESCRIPTION
Exportiert interne ADSync-Objekte aus Metaverse und zugeordnete verbundene Objekte aus Connectorbereichen.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Export-ADSyncToolsObjects -ObjectId '9D220D58-0700-E911-80C8-000D3A3614C0' -Source Metaverse
```
#### <a name="example-2"></a>BEISPIEL 2
```
Export-ADSyncToolsObjects -ObjectId '9e220d58-0700-e911-80c8-000d3a3614c0' -Source ConnectorSpace
```
#### <a name="example-3"></a>BEISPIEL 3
```
Export-ADSyncToolsObjects -DistinguishedName 'CN=User1,OU=ADSync,DC=Contoso,DC=com' -ConnectorName 'Contoso.com'
```
### PARAMETERS
#### <a name="-objectid"></a>-ObjectId
Die ObjectId ist der eindeutige Bezeichner des Objekts im jeweiligen Connectorbereich oder Metaverse.
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-source"></a>-Source
Der Source-Wert gibt die Tabelle an, in der sich das Objekt befindet (entweder ConnectorSpace oder Metaverse).
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-distinguishedname"></a>-DistinguishedName
Der DistinguishedName ist der Bezeichner des Objekts im jeweiligen Connectorbereich.
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-connectorname"></a>-ConnectorName
Der ConnectorName ist der Name des Connectorbereichs, in dem sich das Objekt befindet.
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-exportserialized"></a>-ExportSerialized
ExportSerialized exportiert zusätzliche XML-Dateien.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolsrunhistory"></a>Export-ADSyncToolsRunHistory
### SYNOPSIS
Exportieren des Azure AD Connect-Ausführungsverlaufs
### SYNTAX
```
Export-ADSyncToolsRunHistory [-TargetName] <String> [<CommonParameters>]
```
### DESCRIPTION
Funktion zum Exportieren des Azure AD Connect-Ausführungsprofils und der Ergebnisse des Ausführungsschritts in das CSV- bzw. XML-Format.
Die resultierende CSV-Datei für das Ausführungsprofil kann in ein Arbeitsblatt importiert werden, die XML-Datei zum Ausführungsschritt kann mithilfe von „Import-Clixml“ importiert werden.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Export-ADSyncToolsRunHistory -TargetName MyADSyncHistory
```
### PARAMETERS
#### <a name="-targetname"></a>-TargetName
Name der Ausgabedatei
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolssourceanchorreport"></a>Export-ADSyncToolsSourceAnchorReport
### SYNOPSIS
Exportieren eines ms-ds-Consistency-Guid-Berichts
### SYNTAX
```
Export-ADSyncToolsSourceAnchorReport [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```
### DESCRIPTION
Generiert basierend auf einer CSV-Importdatei von Import-ADSyncToolsSourceAnchor einen ms-ds-Consistency-Guid-Bericht.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsSourceAnchorReport -Output ".\AllSyncUsers-Report"
```
#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Alternative Anmelde-ID (E-Mail) verwenden.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
Ausgabedateiname für CSV- und LOG-Dateien.
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
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsaadobject"></a>Get-ADSyncToolsAadObject
### SYNOPSIS
Abrufen der synchronisierten Objekte für einen vorgegebenen SyncObjectType
### SYNTAX
```
Get-ADSyncToolsAadObject [-SyncObjectType] <Object> [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
Ruft alle synchronisierten Objekte für eine bestimmte Objektklasse (SyncObjectType) aus Azure AD ab.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsAadObject -SyncObjectType 'publicFolder' -Credential $(Get-Credential)
```
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
Objekttyp
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Anmeldeinformationen des globalen Azure AD-Administrators
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

### OUTPUTS

Dieses Cmdlet gibt die „Schatteneigenschaften“ zurück, die vom Synchronisierungsclient synchronisiert werden. Diese können sich vom tatsächlichen Wert unterscheiden, der in der jeweiligen Eigenschaft von Azure AD gespeichert ist. Beispielsweise wird für den UPN eines Benutzers, der mit einem nicht überprüften Domänensuffix user@nonverified.domain synchronisiert wird, das UPN-Suffix in Azure AD in die Standarddomäne des Mandanten konvertiert: user@tenantname.onmicrosoft.com. In diesem Fall gibt Get-ADSyncToolsAadObject anstelle des tatsächlichen Wert in Azure AD (user@tenantname.onmicrosoft.com) den „Schattenwert“ user@nonverified.domain zurück.

## <a name="get-adsynctoolsmsdsconsistencyguid"></a>Get-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Abrufen der ms-ds-ConsistencyGuid für ein Active Directory-Objekt
### SYNTAX
```
Get-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Gibt den Wert im Attribut „mS-DS-ConsistencyGuid“ des Active Directory-Zielobjekts im GUID-Format zurück. Unterstützt Active Directory-Objekte in Gesamtstrukturen mit mehreren Domänen.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>BEISPIEL 3
```
'User1@Contoso.com' | Get-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Abzurufendes Zielobjekt in AD
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory
### SYNOPSIS
Abrufen des Azure AD Connect-Ausführungsverlaufs
### SYNTAX
```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Funktion, die den Ausführungsverlauf von Azure AD Connect im XML-Format zurückgibt
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>BEISPIEL 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
Zurückliegender Zeitraum in Tagen zum Erfassen des Verlaufs (Standardwert = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsrunhistorylegacywmi"></a>Get-ADSyncToolsRunHistoryLegacyWmi
### SYNOPSIS
Abrufen des Azure AD Connect-Ausführungsverlaufs für ältere Versionen von Azure AD Connect (WMI)
### SYNTAX
```
Get-ADSyncToolsRunHistoryLegacyWmi [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Funktion, die den Ausführungsverlauf von Azure AD Connect im XML-Format zurückgibt
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>BEISPIEL 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
Zurückliegender Zeitraum in Tagen zum Erfassen des Verlaufs (Standardwert = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolssqlbrowserinstances"></a>Get-ADSyncToolsSqlBrowserInstances
### SYNOPSIS
Abrufen von SQL Server-Instanzen aus dem SQL-Browserdienst
### SYNTAX
```
Get-ADSyncToolsSqlBrowserInstances [[-Server] <String>]
```
### DESCRIPTION
Funktionen und Hilfsprogramme für die SQL-Diagnose
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsSqlBrowserInstances -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server Name
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
## <a name="get-adsynctoolstenantazureenvironment"></a>Get-ADSyncToolsTenantAzureEnvironment
### SYNOPSIS
Hilfsfunktion zum Abrufen der Azure-Umgebung, der der Benutzer angehört
### SYNTAX
```
Get-ADSyncToolsTenantAzureEnvironment [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
Diese Funktion ruft den Oauth-Ermittlungsendpunkt auf, um durch das Abrufen von „CloudInstance“ und „tenant_region_scope“ die Azure-Umgebung zu bestimmen.
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration

### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsTenantAzureEnvironment -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Das PowerShell-Anmeldeinformationsobjekt des Benutzers:

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
PowerShell-Anmeldeinformationsobjekt des Benutzers

### OUTPUTS
Azure-Umgebung (Zeichenfolge)
## <a name="get-adsynctoolstls12"></a>Get-ADSyncToolsTls12
### SYNOPSIS
Abrufen von Client\Server-TLS 1.2-Einstellungen für .NET Framework
### SYNTAX
```
Get-ADSyncToolsTls12 [<CommonParameters>]
```
### DESCRIPTION
Ruft Informationen zu TLS 1.2 für .NET Framework aus der Registrierung ab:

| Pfad                                                         | Name                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | Aktiviert                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | Aktiviert                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsTls12
```
### PARAMETERS
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Weitere Informationen: [Erzwingen von TLS 1.2 für Azure AD Connect](reference-connect-tls-enforcement.md)

## <a name="import-adsynctoolsobjects"></a>Import-ADSyncToolsObjects
### SYNOPSIS
Importieren eines Azure AD Connect-Objekts aus einer XML-Datei
### SYNTAX
```
Import-ADSyncToolsObjects [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Importiert ein internes ADSync-Objekt aus einer XML-Datei, die mit Export-ADSyncToolsObjects exportiert wurde.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Import-ADSyncToolsObjects -Path .\20210224-003104_81275a23-0168-eb11-80de-00155d188c11_MV.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
Pfad für die zu importierende XML-Datei
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="import-adsynctoolsrunhistory"></a>Import-ADSyncToolsRunHistory
### SYNOPSIS
Importieren des Azure AD Connect-Ausführungsverlaufs
### SYNTAX
```
Import-ADSyncToolsRunHistory [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Funktion zum Importieren der Ergebnisse des Azure AD Connect-Ausführungsschritts aus einer XML, die mit Export-ADSyncToolsRunHistory erstellt wurde.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Export-ADSyncToolsRunHistory -Path .\RunHistory-RunStep.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
Pfad für die zu importierende XML-Datei
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="import-adsynctoolssourceanchor"></a>Import-ADSyncToolsSourceAnchor
### SYNOPSIS
Importieren einer ImmutableID aus Azure AD
### SYNTAX
```
Import-ADSyncToolsSourceAnchor [-Output] <String> [-IncludeSyncUsersFromRecycleBin] [<CommonParameters>]
```
### DESCRIPTION
Generiert eine Datei mit allen in Azure AD synchronisierten Benutzern, die den Wert „ImmutableID“ im GUID-Format enthalten. Voraussetzungen: PowerShell-Modul „MSOnline“
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Import-ADSyncToolsSourceAnchor -OutputFile '.\AllSyncUsers.csv'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-output"></a>-Output
CSV-Ausgabedatei
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Ruft synchronisierte Benutzer aus dem Azure AD-Papierkorb ab.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="invoke-adsynctoolssqlquery"></a>Invoke-ADSyncToolsSqlQuery
### SYNOPSIS
Aufrufen einer SQL-Abfrage für eine Datenbank zu Testzwecken
### SYNTAX
```
Invoke-ADSyncToolsSqlQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```
### DESCRIPTION
Funktionen und Hilfsprogramme für die SQL-Diagnose
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823 | Invoke-ADSyncToolsSqlQuery
```
#### <a name="example-2"></a>BEISPIEL 2
```
$sqlConn = New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823
```
Invoke-ADSyncToolsSqlQuery -SqlConnection $sqlConn -Query 'SELECT *, database_id FROM sys.databases'
### PARAMETERS
#### <a name="-sqlconnection"></a>-SqlConnection
SQL-Verbindung
```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-query"></a>-Query
SQL-Abfrage
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: SELECT name, database_id FROM sys.databases
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

## <a name="remove-adsynctoolsaadobject"></a>Remove-ADSyncToolsAadObject
### SYNOPSIS
Entfernen verwaister synchronisierter Objekte aus Azure AD
### SYNTAX
#### <a name="csvinput"></a>CsvInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-InputCsvFilename] <Object> [-WhatIf] [-Confirm]
 [<CommonParameters>]
```
#### <a name="objectinput"></a>ObjectInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-SourceAnchor] <Object> [-SyncObjectType] <Object>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
Löscht synchronisierte Objekte basierend auf SourceAnchor und ObjecType in Batches von je 10 Objekten aus Azure AD. Die CSV-Datei kann mit Export-ADSyncToolsAadDisconnectors generiert werden.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Remove-ADSyncToolsAadObject -InputCsvFilename .\DeleteObjects.csv -Credential (Get-Credential)
```
#### <a name="example-2"></a>BEISPIEL 2
```
Remove-ADSyncToolsAadObject -SourceAnchor '2epFRNMCPUqhysJL3SWL1A==' -SyncObjectType 'publicFolder' -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Anmeldeinformationen des globalen Azure AD-Administrators
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-inputcsvfilename"></a>-InputCsvFilename
Name der CSV-Eingabedatei
```yaml
Type: Object
Parameter Sets: CsvInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-sourceanchor"></a>-SourceAnchor
SourceAnchor des Objekts
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-syncobjecttype"></a>-SyncObjectType
Objekttyp
```yaml
Type: Object
Parameter Sets: ObjectInput
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
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
„InputCsvFilename“ muss auf eine CSV-Datei mit mindestens zwei Spalten verweisen: SourceAnchor, SyncObjectType
### OUTPUTS
Zeigt Ergebnisse des ExportDeletions-Vorgangs an. HAFTUNGSAUSSCHLUSS: Mit Ausnahme von Benutzerobjekten, die über einen Papierkorb verfügen, können alle anderen Objekttypen, die mit dieser Funktion GELÖSCHT wurden, NICHT WIEDERHERGESTELLT werden!
## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates
### SYNOPSIS
Skript zum Entfernen abgelaufener Zertifikate aus dem Attribut „UserCertificate“.
### SYNTAX
```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```
### DESCRIPTION
Dieses Skript akzeptiert alle Objekte von einer Zielorganisationseinheit in Ihrer Active Directory-Domäne – gefiltert nach Objektklasse (Benutzer/Computer), und löscht alle abgelaufene Zertifikate, die im Attribut „UserCertificate“ vorhanden sind.
Standardmäßig (BackupOnly-Modus) werden nur abgelaufene Zertifikate in einer Datei gesichert und keine Änderungen in Active Directory vorgenommen.
Wenn Sie „-BackupOnly $false“ verwenden, werden alle für diese Objekte im Attribut „UserCertificate“ vorhandenen abgelaufenen Zertifikate aus Active Directory entfernt, nachdem sie in eine Datei kopiert wurden.
Jedes Zertifikat wird unter einem gesonderten Dateinamen gesichert: ObjectClass_ObjectGUID_CertThumprint.cer. Das Skript erstellt außerdem eine Protokolldatei im CSV-Format mit allen Benutzern, die Zertifikate besitzen, die entweder gültig oder abgelaufen sind, inklusive der tatsächlich ausgeführten Aktion (Übersprungen/Exportiert/Gelöscht).
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user
#### <a name="example-2"></a>BEISPIEL 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false
### PARAMETERS
#### <a name="-targetou"></a>-TargetOU
Ziel-OE, in der nach AD-Objekten gesucht werden soll.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-backuponly"></a>-BackupOnly
Bei „BackupOnly“ werden keine Zertifikate aus AD gelöscht.
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-objectclass"></a>-ObjectClass
Objektklassenfilter
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
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState
### SYNOPSIS
Korrigieren des Azure AD-Connect-AutoUpgrade-Status
### SYNTAX
```
Repair-ADSyncToolsAutoUpgradeState
```
### DESCRIPTION
Behebt ein Problem mit AutoUpgrade, das in Build 1.1.524 (Mai 2017) eingeführt wurde und die Onlineüberprüfung neuer Versionen deaktiviert, während AutoUpgrade aktiviert ist.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Repair-ADSyncToolsAutoUpgradeState
```
## <a name="resolve-adsynctoolssqlhostaddress"></a>Resolve-ADSyncToolsSqlHostAddress
### SYNOPSIS
Auflösen eines SQL Server-Namens
### SYNTAX
```
Resolve-ADSyncToolsSqlHostAddress [-Server] <String> [<CommonParameters>]
```
### DESCRIPTION
Funktionen und Hilfsprogramme für die SQL-Diagnose
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Resolve-ADSyncToolsSqlHostAddress -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server Name
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="search-adsynctoolsadobject"></a>Search-ADSyncToolsADobject
### SYNOPSIS
Suchen eines Active Directory-Objekts in der Active Directory-Gesamtstruktur nach UserPrincipalName, sAMAccountName oder DistinguishedName
### SYNTAX
```
Search-ADSyncToolsADobject [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Unterstützt Abfragen für mehreren Domänen und gibt alle erforderlichen Eigenschaften zurück, einschließlich mS-DS-ConsistencyGuid.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Search-ADSyncToolsADobject 'CN=user1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Search-ADSyncToolsADobject -Identity "user1@Contoso.com"
```
#### <a name="example-3"></a>BEISPIEL 3
```
Get-ADUser 'CN=user1,OU=Sync,DC=Contoso,DC=com' | Search-ADSyncToolsADobject
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Zielbenutzer in AD zum Festlegen von ConsistencyGuid.
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="set-adsynctoolsmsdsconsistencyguid"></a>Set-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Festlegen der ms-ds-ConsistencyGuid für ein Active Directory-Objekt
### SYNTAX
```
Set-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
Legt einen Wert im Attribut „mS-DS-ConsistencyGuid“ für den Active Directory-Zielbenutzer fest.
Unterstützt Active Directory-Objekte in Gesamtstrukturen mit mehreren Domänen.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value '88666888-0101-1111-bbbb-1234567890ab'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-3"></a>BEISPIEL 3
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' '8d6c6818-018c-4f11-9bb8-1b04e2caa1b6'
```
#### <a name="example-4"></a>BEISPIEL 4
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-5"></a>BEISPIEL 5
```
'88666888-0101-1111-bbbb-1234567890ab' | Set-ADSyncToolsMsDsConsistencyGuid -Identity User1
```
#### <a name="example-6"></a>BEISPIEL 6
```
'GGhsjYwBEU+buBsE4sqhtg==' | Set-ADSyncToolsMsDsConsistencyGuid User1
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Zielobjekt in AD zum Festlegen von mS-DS-ConsistencyGuid
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-value"></a>-Value
Festzulegender Wert (ImmutableId, Bytearray, GUID, GUID-Zeichenfolge oder Base64-Zeichenfolge)
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="set-adsynctoolstls12"></a>Set-ADSyncToolsTls12
### SYNOPSIS
Festlegen von Client\Server-TLS 1.2-Einstellungen für .NET Framework
### SYNTAX
```
Set-ADSyncToolsTls12 [[-Enabled] <Boolean>] [<CommonParameters>]
```
### DESCRIPTION
Legt die Registrierungseinträge zum Aktivieren/Deaktivieren von TLS 1.2 für .NET Framework fest:

| Pfad                                                         | Name                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | Aktiviert                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | Aktiviert                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

Durch eine Ausführung des Cmdlets ohne Parameter wird TLS 1.2 für .NET Framework aktiviert.

### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncToolsTls12
```
#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncToolsTls12 -Enabled $true
```
### PARAMETERS
#### <a name="-enabled"></a>-Enabled
TLS 1.2 aktiviert
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: True
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Weitere Informationen: [Erzwingen von TLS 1.2 für Azure AD Connect](reference-connect-tls-enforcement.md)

## <a name="test-adsynctoolssqlnetworkport"></a>Test-ADSyncToolsSqlNetworkPort
### SYNOPSIS
Testen des SQL Server-Netzwerkports
### SYNTAX
```
Test-ADSyncToolsSqlNetworkPort [[-Server] <String>] [[-Port] <String>]
```
### DESCRIPTION
Funktionen und Hilfsprogramme für die SQL-Diagnose
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01'
```
#### <a name="example-2"></a>BEISPIEL 2
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01' -Port 1433
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server Name
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
#### <a name="-port"></a>-Port
Port des SQL-Servers
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport
### SYNOPSIS
Erstellt eine Ablaufverfolgungsdatei aus einem Active Directory-Importschritt.
### SYNTAX
#### <a name="adconnectorxml"></a>ADConnectorXML
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADConnectorXML] <String> [<CommonParameters>]
```
#### <a name="adwatermarkinput"></a>ADwatermarkInput
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADwatermark] <String> [<CommonParameters>]
```
### DESCRIPTION
Verfolgt alle LDAP-Abfragen eines Active Directory-Importvorgangs nach, die von einem angegebenen Active Directory-Grenzwertprüfpunkt (auch als Partitionscookie bezeichnet) ausgeführt werden. Erstellt eine Ablaufverfolgungsdatei „.\ADimportTrace_yyyyMMddHHmmss.log“ im aktuellen Ordner.
Wechseln Sie zur Verwendung von -ADConnectorXML zum Synchronisierungsdienst-Manager, klicken Sie mit der rechten Maustaste auf Ihren AD-Connector, und wählen Sie „Connector exportieren“ aus.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Trace Active Directory Import for user objects by providing an AD Connector XML file
```
Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Filter '(&(objectClass=user))' -ADConnectorXML .\ADConnector.xml
#### <a name="example-2"></a>BEISPIEL 2
```
Trace Active Directory Import for all objects by providing the Active Directory watermark (cookie) and AD Connector credential
```
$creds = Get-Credential Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Credential $creds -ADwatermark "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
### PARAMETERS
#### <a name="-dc"></a>-DC
Zieldomänencontroller
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-rootdn"></a>-RootDN
Gesamtstrukturstamm-DN
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
AD-Objekttypen, die nachverfolgt werden sollen.
Verwenden Sie für alle Objekttypen „(&(objectClass=*))“.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Angeben der Anmeldeinformationen zur Ausführung der LDAP-Abfrage in AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-ssl"></a>-SSL
SSL-Verbindung
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adconnectorxml"></a>-ADConnectorXML
XML-Datei für den AD-Connector-Export. Klicken Sie mit der rechten Maustaste auf den AD-Connector, und wählen Sie „Connector exportieren“ aus.
```yaml
Type: String
Parameter Sets: ADConnectorXML
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adwatermark"></a>-ADwatermark
Manuelle Eingabe des Wasserzeichens anstelle einer XML-Datei. Beispiel: $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
```yaml
Type: String
Parameter Sets: ADwatermarkInput
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery
### SYNOPSIS
Ablaufverfolgung für LDAP-Abfragen
### SYNTAX
```
Trace-ADSyncToolsLdapQuery [-RootDN] <String> [-Credential] <PSCredential> [[-Server] <String>]
 [[-Port] <Int32>] [-Filter <String>] [<CommonParameters>]
```
### DESCRIPTION
{{ Beschreibung einfügen}}
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Trace-ADSyncToolsLdapQuery -RootDN "DC=Contoso,DC=com" -Credential $Credential
```
### PARAMETERS
#### <a name="-rootdn"></a>-RootDN
DistinguishedName für Gesamtstruktur/Domäne
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
AD-Anmeldeinformationen
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-server"></a>-Server
Name des Domänencontrollers (optional)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
Port des Domänencontrollers (Standardwert: 389)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: 389
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
LDAP-Filter (Standardwert: objectClass=*)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="update-adsynctoolssourceanchor"></a>Update-ADSyncToolsSourceAnchor
### SYNOPSIS
Aktualisiert Benutzer mit der neuen „ConsistencyGuid“ (ImmutableId).
### SYNTAX
```
Update-ADSyncToolsSourceAnchor [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String> [-Action] <String>
 [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
Aktualisiert Benutzer mit dem neuen Wert der ConsistencyGuid (ImmutableId), der dem ConsistencyGuid-Bericht entnommen wurde. Diese Funktion unterstützt den WhatIf-Schalter. Hinweis: Der ConsistencyGuid-Bericht muss mit Tabstopps als Trennzeichen importiert werden.
### EXAMPLES
#### <a name="example-1"></a>BEISPIEL 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2 -WhatIf
```
#### <a name="example-2"></a>BEISPIEL 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-action"></a>-Action
Aktion
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
Ausgabedateiname für LOG-Dateien.
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
#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable. Weitere Informationen findest du unter [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).