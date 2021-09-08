---
title: SMB-Dateifreigaben in Azure Files
description: Informationen zu Dateifreigaben, die unter Verwendung des SMB-Protokolls (Server Message Block) in Azure Files gehostet werden.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 15a3064ab02a9c83a489219210be32ee99134e47
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338924"
---
# <a name="smb-file-shares-in-azure-files"></a>SMB-Dateifreigaben in Azure Files
Azure Files unterstützt zwei Branchenstandardprotokolle für die Einbindung von Azure-Dateifreigaben: das [SMB-Protokoll (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) und das [NFS-Protokoll (Network File System)](https://en.wikipedia.org/wiki/Network_File_System) (Vorschau). In Azure Files können Sie das Dateisystemprotokoll auswählen, das sich für Ihre Workload am besten eignet. Bei Azure-Dateifreigaben ist der Zugriff auf eine einzelne Azure-Dateifreigabe über das SMB- oder das NFS-Protokoll nicht möglich. Sie können jedoch SMB- und NFS-Dateifreigaben innerhalb desselben Speicherkontos erstellen. Für alle Dateifreigaben ermöglicht Azure Files Dateifreigaben in Unternehmensqualität, die entsprechend Ihren Speicheranforderungen hochskaliert werden können und auf die Tausende von Clients gleichzeitig zugreifen können.

In diesem Artikel werden SMB-Azure-Dateifreigaben behandelt. Informationen zu NFS-Azure-Dateifreigaben finden Sie unter [NFS-Dateifreigaben in Azure Files](files-nfs-protocol.md).

## <a name="common-scenarios"></a>Häufige Szenarien
SMB-Dateifreigaben werden für eine Vielzahl von Anwendungen verwendet, darunter Dateifreigaben für Endbenutzer und Dateifreigaben, die Datenbanken und Anwendungen unterstützen. SMB-Dateifreigaben werden häufig in den folgenden Szenarien verwendet:

- Dateifreigaben für Endbenutzer, z. B. Teamfreigaben, Basisverzeichnisse usw.
- Hintergrundspeicher für Windows-basierte Anwendungen, z. B. SQL Server-Datenbanken oder branchenspezifische Anwendungen, die für lokale Win32- oder .NET-Dateisystem-APIs geschrieben wurden 
- Entwicklung neuer Anwendungen und Dienste, insbesondere wenn die Anwendungen oder Dienste zufällige E/A und hierarchische Speicherung erfordern

## <a name="features"></a>Features
Azure Files unterstützt die wichtigsten Funktionen von SMB und Azure, die für Produktionsbereitstellungen von SMB-Dateifreigaben erforderlich sind:

- AD-Domänenbeitritt und besitzverwaltete Zugriffssteuerungslisten (Discretionary Access Control Lists, DACLs)
- Integrierte serverlose Sicherung mit Azure Backup
- Netzwerkisolation mit privaten Azure-Endpunkten
- Hoher Netzwerkdurchsatz mit SMB Multichannel (nur Premium-Dateifreigaben)
- SMB-Kanalverschlüsselung einschließlich AES-256-GCM, AES-128-GCM und AES-128-CCM
- Unterstützung früherer Versionen durch integrierte VSS-Freigabemomentaufnahmen
- Automatisches vorläufiges Löschen für Azure-Dateifreigaben, um versehentliche Löschvorgänge zu verhindern
- Optional über das Internet zugängliche Dateifreigaben mit internetsicherem SMB 3.0 und höher

SMB-Dateifreigaben können direkt lokal eingebunden oder auch [lokal mit der Azure-Dateisynchronisierung zwischengespeichert](../file-sync/file-sync-introduction.md) werden.  

## <a name="security"></a>Sicherheit
Alle in Azure Files gespeicherten Daten werden im Ruhezustand mithilfe der Azure-Speicherdienstverschlüsselung (Storage Service Encryption, SSE) verschlüsselt. Die Speicherdienstverschlüsselung funktioniert ähnlich wie BitLocker unter Windows: Daten werden unterhalb der Dateisystemebene verschlüsselt. Da Daten durch die Codierung auf dem Datenträger unterhalb des Dateisystems der Azure-Dateifreigabe verschlüsselt werden, benötigen Sie keinen Zugriff auf den zugrunde liegenden Schlüssel auf dem Client, um aus der Azure-Dateifreigabe zu lesen oder in diese zu schreiben. Die Verschlüsselung ruhender Daten wird auf SMB- und NFS-Protokolle angewendet.

Standardmäßig ist in allen Azure-Speicherkonten die Verschlüsselung während der Übertragung aktiviert. Das bedeutet Folgendes: Wenn Sie eine Dateifreigabe über SMB einbinden (oder über das FileREST-Protokoll darauf zugreifen), lässt Azure Files die Verbindung nur dann zu, wenn sie über SMB 3.x mit Verschlüsselung oder über HTTPS hergestellt wird. Clients, die SMB 3.x mit SMB-Kanalverschlüsselung nicht unterstützen, können die Azure-Dateifreigabe nicht einbinden, wenn die Verschlüsselung während der Übertragung aktiviert ist. 

Azure Files unterstützt die branchenführende Verschlüsselung AES-256-GCM mit SMB 3.1.1 bei Verwendung unter Windows 10 Version 21H1. SMB 3.1.1 unterstützt außerdem AES-128-GCM. SMB 3.0 unterstützt AES-128-CCM. AES-128-GCM wird aus Leistungsgründen standardmäßig unter Windows 10 Version 21H1 ausgehandelt.

Sie können die Verschlüsselung während der Übertragung für ein Azure-Speicherkonto deaktivieren. Wenn die Verschlüsselung deaktiviert ist, lässt Azure Files auch SMB 2.1 und SMB 3.x ohne Verschlüsselung zu. Der Hauptgrund für die Deaktivierung der Verschlüsselung während der Übertragung ist die Unterstützung einer älteren Anwendung, die unter einem älteren Betriebssystem wie z. B. Windows Server 2008 R2 oder einer älteren Linux-Distribution ausgeführt werden muss. Azure Files lässt nur SMB 2.1-Verbindungen innerhalb der gleichen Region zu, in der sich auch die Azure-Dateifreigabe befindet. Ein SMB 2.1-Client außerhalb der Azure-Region der Azure-Dateifreigabe – z. B. ein lokales System oder eine andere Azure-Region – kann nicht auf die Dateifreigabe zugreifen.

## <a name="smb-protocol-settings"></a>SMB-Protokolleinstellungen
Azure Files umfasst mehrere Einstellungen für das SMB-Protokoll.

- **SMB Multichannel:** Aktivieren und Deaktivieren von SMB Multichannel (nur Premium-Dateifreigaben). Informationen zum Aktivieren von SMB Multichannel finden Sie unter [Aktivieren von SMB Multichannel in einem FileStorage-Speicherkonto](storage-files-enable-smb-multichannel.md). SMB Multichannel ist standardmäßig deaktiviert.
- **SMB-Versionen:** Zulässige Versionen von SMB. Unterstützt werden die Protokollversionen SMB 3.1.1, SMB 3.0 und SMB 2.1. Standardmäßig sind alle SMB-Versionen zulässig, obwohl SMB 2.1 nicht zulässig ist, wenn „Sichere Übertragung erforderlich“ aktiviert ist, da SMB 2.1 die Verschlüsselung während der Übertragung nicht unterstützt.
- **Authentifizierungsmethoden:** Zulässige SMB-Authentifizierungsmethoden. Unterstützt werden die Authentifizierungsmethoden NTLMv2 und Kerberos. Standardmäßig sind alle Authentifizierungsmethoden zulässig. Durch Entfernen von NTLMv2 wird die Verwendung des Speicherkontoschlüssels zum Einbinden der Azure-Dateifreigabe verhindert.
- **Kerberos-Ticketverschlüsselung:** Zulässige Verschlüsselungsalgorithmen. Unterstützt werden die Verschlüsselungsalgorithmen RC4-HMAC und AES-256.
- **SMB-Kanalverschlüsselung:** Zulässige SMB-Kanalverschlüsselungsalgorithmen. Unterstützt werden die Verschlüsselungsalgorithmen AES-256-GCM, AES-128-GCM und AES-128-CCM.

Die SMB-Protokolleinstellungen können über das Azure PowerShell-Modul geändert werden.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Die SMB-Protokolleinstellungen können über PowerShell oder die Befehlszeilenschnittstelle angezeigt und geändert werden. Wählen Sie die gewünschte Registerkarte aus, um die Schritte zum Abrufen und Festlegen der SMB-Protokolleinstellungen anzuzeigen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Um die SMB-Protokolleinstellungen abzurufen oder festzulegen, müssen Sie einen Verweis auf ein Speicherkonto angeben, indem Sie entweder direkt den Namen der Ressourcengruppe und des Speicherkontos oder ein Speicherkontoobjekt angeben, das Sie über das Cmdlet `Get-AzStorageAccount` abgerufen haben. In den folgenden Beispielen wird die letztere Methode verwendet, um die SMB-Protokolleinstellungen abzurufen und festzulegen.

Denken Sie daran, `<resource-group>` und `<storage-account>` durch die entsprechenden Werte für Ihre Umgebung zu ersetzen, bevor Sie diese PowerShell-Befehle ausführen.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

Verwenden Sie das Cmdlet `Get-AzStorageFileServiceProperty`, um die SMB-Protokolleinstellungen abzurufen. Wenn Sie die SMB-Protokolleinstellungen noch nie geändert haben, werden vom Cmdlet NULL-Werte zurückgegeben. Zurückgegebene NULL-Werte können als „Standardeinstellungen sind aktiv“ interpretiert werden. Zur Verbesserung der Benutzerfreundlichkeit werden mit den folgenden PowerShell-Befehlen NULL-Werte durch die lesbaren Standardwerte ersetzt. 

```PowerShell
# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
$smbMultichannelEnabled = $false
$smbProtocolVersions = "SMB2.1", "SMB3.0", "SMB3.1.1"
$smbAuthenticationMethods = "NTLMv2", "Kerberos"
$smbKerberosTicketEncryption = "RC4-HMAC", "AES-256"
$smbChannelEncryption = "AES-128-CCM", "AES-128-GCM", "AES-256-GCM"

Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbMultichannelEnabled"; 
            Expression = { 
                if ($null -eq $_.ProtocolSettings.Smb.Multichannel.Enabled) { 
                    $smbMultichannelEnabled 
                } else { 
                    $_.ProtocolSettings.Smb.Multichannel.Enabled 
                } 
            } 
        }, 
        @{ 
            Name = "SmbProtocolVersions";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.Versions) {
                    [String]::Join(", ", $smbProtocolVersions)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.Versions)
                }
            }
        },
        @{
            Name = "SmbChannelEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.ChannelEncryption) {
                    [String]::Join(", ", $smbChannelEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.ChannelEncryption)
                }
            }
        },
        @{
            Name = "SmbAuthenticationMethods";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.AuthenticationMethods) {
                    [String]::Join(", ", $smbAuthenticationMethods)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.AuthenticationMethods)
                }
            }
        },
        @{
            Name = "SmbKerberosTicketEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.KerberosTicketEncryption) {
                    [String]::Join(", ", $smbKerberosTicketEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.KerberosTicketEncryption)
                }
            }
        }
```

Sie können die SMB-Protokolleinstellungen je nach den Sicherheits-, Leistungs- und Kompatibilitätsanforderungen Ihrer Organisation ändern. Mit dem folgenden PowerShell-Befehl werden Ihre SMB-Dateifreigaben auf die sichersten Optionen eingeschränkt.

> [!Important]  
> Die Einschränkung der SMB-Azure-Dateifreigaben auf die sichersten Optionen kann dazu führen, dass einige Clients keine Verbindung herstellen können, wenn sie die Anforderungen nicht erfüllen. Beispielsweise wurde AES-256-GCM ab Windows 10, Version 21H1 als Option für die SMB-Kanalverschlüsselung eingeführt. Das bedeutet, dass ältere Clients, die AES-256-GCM nicht unterstützen, keine Verbindung herstellen können.

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -SmbAuthenticationMethod "Kerberos" `
    -SmbChannelEncryption "AES-256-GCM" `
    -SmbKerberosTicketEncryption "AES-256" `
    -SmbProtocolVersion "SMB3.1.1"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Verwenden Sie den Befehl `az storage account file-service-properties show`, um die SMB-Protokolleinstellungen abzurufen. Wenn Sie die SMB-Protokolleinstellungen noch nie geändert haben, werden über den Befehl NULL-Werte zurückgegeben. Zurückgegebene NULL-Werte können als „Standardeinstellungen sind aktiv“ interpretiert werden. Zur Verbesserung der Benutzerfreundlichkeit werden mit den folgenden Bash-Befehlen NULL-Werte durch die lesbaren Standardwerte ersetzt. 

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# Values to be replaced
replaceSmbMultichannel="\"smbMultichannelEnabled\": null"
replaceSmbProtocolVersion="\"smbProtocolVersions\": null"
replaceSmbChannelEncryption="\"smbChannelEncryption\": null"
replaceSmbAuthenticationMethods="\"smbAuthenticationMethods\": null"
replaceSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": null"

# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
defaultSmbMultichannelEnabled="\"smbMultichannelEnabled\": false"
defaultSmbProtocolVersions="\"smbProtocolVersions\": \"SMB2.1;SMB3.0;SMB3.1.1\""
defaultSmbChannelEncryption="\"smbChannelEncryption\": \"AES-128-CCM;AES-128-GCM;AES-256-GCM\""
defaultSmbAuthenticationMethods="\"smbAuthenticationMethods\": \"NTLMv2;Kerberos\""
defaultSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": \"RC4-HMAC;AES-256\""

# Build JMESPath query string
query="{" 
query="${query}smbMultichannelEnabled: protocolSettings.smb.multichannel.enabled,"
query="${query}smbProtocolVersions: protocolSettings.smb.versions,"
query="${query}smbChannelEncryption: protocolSettings.smb.channelEncryption,"
query="${query}smbAuthenticationMethods: protocolSettings.smb.authenticationMethods,"
query="${query}smbKerberosTicketEncryption: protocolSettings.smb.kerberosTicketEncryption"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbMultichannel/$defaultSmbMultichannelEnabled}"
protocolSettings="${protocolSettings/$replaceSmbProtocolVersion/$defaultSmbProtocolVersion}"
protocolSettings="${protocolSettings/$replaceSmbChannelEncryption/$defaultSmbChannelEncryption}"
protocolSettings="${protocolSettings/$replaceSmbAuthenticationMethods/$defaultSmbAuthenticationMethods}"
protocolSettings="${protocolSettings/$replaceSmbKerberosTicketEncryption/$defaultSmbKerberosTicketEncryption}"

# Print returned settings
echo $protocolSettings
```

Sie können die SMB-Protokolleinstellungen je nach den Sicherheits-, Leistungs- und Kompatibilitätsanforderungen Ihrer Organisation ändern. Mit dem folgenden Azure CLI-Befehl werden Ihre SMB-Dateifreigaben auf die sichersten Optionen eingeschränkt.

> [!Important]  
> Die Einschränkung der SMB-Azure-Dateifreigaben auf die sichersten Optionen kann dazu führen, dass einige Clients keine Verbindung herstellen können, wenn sie die Anforderungen nicht erfüllen. Beispielsweise wurde AES-256-GCM ab Windows 10, Version 21H1 als Option für die SMB-Kanalverschlüsselung eingeführt. Das bedeutet, dass ältere Clients, die AES-256-GCM nicht unterstützen, keine Verbindung herstellen können.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --versions "SMB3.1.1" \
    --channel-encryption "AES-256-GCM" \
    --auth-methods "Kerberos" \
    --kerb-ticket-encryption "AES-256"
```
---

## <a name="limitations"></a>Einschränkungen
SMB-Dateifreigaben in Azure Files unterstützen eine Teilmenge der über das SMB-Protokoll und das NTFS-Dateisystem unterstützten Funktionen. Auch wenn diese Funktionen in den meisten Anwendungsfällen und Anwendungen nicht erforderlich sind, werden einige Anwendungen möglicherweise nicht ordnungsgemäß mit Azure Files ausgeführt, wenn sie auf nicht unterstützten Funktionen basieren. Folgende Funktionen werden nicht unterstützt:

- [SMB Direct](/windows-server/storage/file-server/smb-direct)  
- SMB Directory Leasing
- [VSS für SMB-Dateifreigaben](/archive/blogs/clausjor/vss-for-smb-file-shares) (damit können VSS-Anbieter Daten in die SMB-Dateifreigabe leeren, bevor eine Momentaufnahme erstellt wird)
- Alternative Datenströme
- Erweiterte Attribute
- Objektkennungen
- [Harte Links](/windows/win32/fileio/hard-links-and-junctions)
- [Weiche Links](/windows/win32/fileio/creating-symbolic-links)  
- [Analysepunkte](/windows/win32/fileio/reparse-points)  
- [Sparsedateien](/windows/win32/fileio/sparse-files)
- [Kurze Dateinamen (8.3-Alias)](/windows/win32/fileio/naming-a-file#short-vs-long-names)  
- [Komprimierung](https://techcommunity.microsoft.com/t5/itops-talk-blog/smb-compression-deflate-your-io/ba-p/1183552)

## <a name="regional-availability"></a>Regionale Verfügbarkeit
SMB-Azure-Dateifreigaben sind in jeder Azure-Region verfügbar, einschließlich aller öffentlichen und unabhängigen Regionen. SMB-Premium-Dateifreigaben sind in [einigen Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=storage) verfügbar.

## <a name="next-steps"></a>Nächste Schritte
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Erstellen einer Azure-Dateifreigabe](storage-how-to-create-file-share.md)
- Einbinden von SMB-Dateifreigaben in Ihrem bevorzugten Betriebssystem:
    - [Einbinden von SMB-Dateifreigaben unter Windows](storage-how-to-use-files-windows.md)
    - [Einbinden von SMB-Dateifreigaben unter Linux](storage-how-to-use-files-linux.md)
    - [Einbinden von SMB-Dateifreigaben unter macOS](storage-how-to-use-files-mac.md)