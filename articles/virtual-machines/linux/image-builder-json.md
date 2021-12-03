---
title: Erstellen einer Azure Image Builder-Vorlage
description: Erfahren Sie, wie Sie eine Vorlage für die Verwendung mit Azure Image Builder erstellen.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 05/24/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4e8832222cb1fc0a4ec431f1eeedcdcda0c5a11
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400963"
---
# <a name="create-an-azure-image-builder-template"></a>Erstellen einer Azure Image Builder-Vorlage 

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Azure Image Builder verwendet eine JSON-Datei, um Informationen an den Image Builder-Dienst zu übermitteln. In diesem Artikel werden die einzelnen Abschnitte der JSON-Datei erläutert, sodass Sie Ihre eigene erstellen können. Vollständige JSON-Beispieldateien finden Sie im [GitHub-Repository für Azure Image Builder](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Das grundlegende Format der Vorlage:

```json
  { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
      "<name>": "<value>",
      "<name>": "<value>"
    },
    "identity": {},          
    "properties": { 
      "buildTimeoutInMinutes": <minutes>, 
      "vmProfile": {
        "vmSize": "<vmSize>",
        "proxyVmSize": "<vmSize>",
        "osDiskSizeGB": <sizeInGB>,
        "vnetConfig": {
          "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
      },
      "source": {}, 
      "customize": {}, 
      "distribute": {} 
    } 
  } 
```



## <a name="type-and-api-version"></a>Typ und API-Version

`type` ist der Ressourcentyp, der `"Microsoft.VirtualMachineImages/imageTemplates"` entsprechen muss. Die `apiVersion` ändert sich im Laufe der Zeit, wenn die API geändert wird, sollte aber momentan `"2020-02-14"` sein.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Standort

„Location“ entspricht der Region, in der das benutzerdefinierte Image erstellt wird. Die folgenden Regionen werden unterstützt:

- East US
- USA (Ost) 2
- USA, Westen-Mitte
- USA (Westen)
- USA, Westen 2
- USA Süd Mitte
- Nordeuropa
- Europa, Westen
- Südostasien
- Australien, Südosten
- Australien (Osten)
- UK, Süden
- UK, Westen

```json
    "location": "<region>",
```

### <a name="data-residency"></a>Datenresidenz
Der Azure VM Image Builder-Dienst speichert/verarbeitet Kundendaten nicht außerhalb von Regionen, die strenge Anforderungen an die Datenresidenz in einer Region haben, wenn ein Kunde einen Build in dieser Region an fordert. Bei einem Dienstausfall für Regionen mit Anforderungen an die Datenresidenz müssen Sie Vorlagen in einer anderen Region und Geografie erstellen.

### <a name="zone-redundancy"></a>Zonenredundanz
Die Verteilung unterstützt Zonenredundanz, VHDs werden standardmäßig auf ein Konto für zonenredundanten Speicher verteilt, und die Version der Azure Compute Gallery (ehemals Shared Image Gallery) unterstützt einen [ZRS-Speichertyp](../disks-redundancy.md#zone-redundant-storage-for-managed-disks), falls angegeben.
 
## <a name="vmprofile"></a>vmProfile
## <a name="buildvm"></a>buildVM
Standardmäßig verwendet Image Builder eine "Standard_D1_v2"-Build-VM für Gen1-Images und eine "Standard_D2ds_v4"-Build-VM für Gen2-Images. Diese wird aus dem Image erstellt, das Sie in `source` angeben. Sie können dies außer Kraft setzen und dies aus folgenden Gründen tun:
1. Durchführen von Anpassungen, die mehr Arbeitsspeicher, CPU und Verarbeitung großer Dateien (GBs) erfordern.
2. Wenn Sie Windows-Builds ausführen, sollten Sie „Standard_D2_v2“ oder eine gleichmäßige VM-Größe verwenden.
3. Forderung nach [VM-Isolation](../isolation.md).
4. Passen Sie ein Image an, das bestimmte Hardware erfordert, z. B. für eine GPU-VM benötigen Sie eine GPU-VM-Größe. 
5. Fordern Sie End-to-end-Verschlüsselung im Ruhespeicher des virtuellen Buildcomputers an. Sie müssen die [Größe des virtuellen Buildcomputers](../azure-vms-no-temp-disk.yml) angeben, für den keine lokalen temporären Datenträger verwendet werden.
 
Diese Eingabe ist optional.

## <a name="osdisksizegb"></a>osDiskSizeGB

Standardmäßig ändert Image Builder die Größe des Images nicht. Es wird die Größe des Quellimages verwendet. Sie können **nur** die Größe des Betriebssystemdatenträgers erhöhen (Windows und Linux). Dies ist optional, und der Wert „0“ bedeutet, dass die Größe der Größe des Quellimages entspricht. Die Größe des Betriebssystemdatenträgers kann nicht auf einen Wert reduziert werden, der niedriger ist als die Größe des Quellimages.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Wenn Sie keine VNET-Eigenschaften angeben, erstellt Image Builder ein eigenes VNet, eine öffentliche IP-Adresse und eine NSG. Die öffentliche IP-Adresse wird für die Kommunikation des Diensts mit der Build-VM verwendet. Wenn Sie jedoch nicht möchten, dass eine öffentliche IP-Adresse oder Image Builder Zugriff auf Ihre vorhandenen VNET-Ressourcen wie Konfigurationsserver (DSC, Chef, Puppet, Ansible), Dateifreigaben usw. hat, können Sie ein VNet angeben. Lesen Sie die [Netzwerkdokumentation](image-builder-networking.md), um weitere Informationen zu erhalten. Dieser Schritt ist optional.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>`Tags`

Dabei handelt es sich um Schlüssel-Wert-Paare, die Sie für das generierte Image angeben können.

## <a name="identity"></a>Identity

Erforderlich: Damit Image Builder die Berechtigung zum Lesen/Schreiben von Images hat, die in Skripts von Azure Storage eingelesen werden, müssen Sie eine benutzerseitig zugewiesene Azure-Identität erstellen, die über Berechtigungen für die einzelnen Azure-Ressourcen verfügt. Details zur Funktionsweise der Image Builder-Berechtigungen und zu den relevanten Schritten finden Sie in der [Dokumentation](image-builder-user-assigned-identity.md).


```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
    },
```


Image Builder-Unterstützung für eine benutzerseitig zugewiesene Identität:
* Unterstützt nur eine einzelne Identität
* Unterstützt keine benutzerdefinierten Domänennamen

Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).
Weitere Informationen zur Bereitstellung dieses Features finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Eigenschaften: source

Der Abschnitt `source` enthält Informationen über das Quellimage, das von Image Builder verwendet wird. Image Builder unterstützt derzeit nativ nur die Erstellung von Images der Hyper-V-Generation 1 (Gen1) für Azure Compute Gallery oder Managed Image. Wenn Sie Gen2-Images erstellen möchten, müssen Sie ein Gen2-Quellimage verwenden und an VHD verteilen. Danach müssen Sie ein verwaltetes Image von der VHD erstellen und es als Gen2-Image in SIG einfügen.

Die API erfordert eine SourceType-Eigenschaft, die die Quelle für die Imageerstellung definiert. Derzeit stehen die folgenden drei Typen zur Verfügung:
- PlatformImage: gibt an, dass es sich beim Quellimage um ein Marketplace-Image handelt.
- ManagedImage: Verwenden Sie diesen Typ, wenn Sie mit einem normal verwalteten Image beginnen.
- SharedImageVersion: Dieser Typ wird verwendet, wenn Sie eine Imageversion aus einer Azure Compute Gallery als Quellimage verwenden.


> [!NOTE]
> Wenn Sie vorhandene benutzerdefinierte Windows-Images verwenden, können Sie den Sysprep-Befehl bis zu dreimal für ein einzelnes Windows 7- oder Windows Server 2008 R2-Image oder 1001-mal für ein einzelnes Windows-Image für spätere Versionen ausführen. Weitere Informationen finden Sie in der [sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)-Dokumentation.

### <a name="platformimage-source"></a>PlatformImage-Quelle 
Azure Image Builder unterstützt Windows Server- und -Client- sowie Azure Marketplace Linux-Images. Die vollständige Liste finden Sie unter [Weitere Informationen zu Azure Image Builder](../image-builder-overview.md#os-support). 

```json
        "source": {
            "type": "PlatformImage",
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS",
            "version": "latest"
        },  
```


Die Eigenschaften hier entsprechen denen, die zum Erstellen der VM verwendet wurden. Führen Sie den folgenden Befehl in der Azure CLI aus, um die Eigenschaften abzurufen: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Sie können „latest“ in der Version verwenden. Die Version wird bei der Imageerstellung und nicht beim Übermitteln der Vorlage ausgewertet. Wenn Sie diese Funktion mit dem Azure Compute Gallery-Ziel verwenden, können Sie die erneute Übermittlung der Vorlage vermeiden und die Imageerstellung in Intervallen erneut ausführen, sodass Ihre Images aus den neuesten Images neu erstellt werden.

#### <a name="support-for-market-place-plan-information"></a>Unterstützung für Informationen zum Marketplaceplan
Sie können auch Planinformationen angeben. Beispiel:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage-Quelle

Hiermit wird ein vorhandenes verwaltetes Image einer generalisierten VHD oder VM als Quellimage festgelegt.

> [!NOTE]
> Das verwaltete Quellimage muss ein Image eines unterstützten Betriebssystems sein und sich in derselben Region wie Ihre Azure Image Builder-Vorlage befinden. 

```json
        "source": { 
            "type": "ManagedImage", 
            "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` sollte dem ResourceId-Wert des verwalteten Images entsprechen. Verwenden Sie den Befehl `az image list`, um die verfügbaren Images aufzulisten.


### <a name="sharedimageversion-source"></a>SharedImageVersion-Quelle
Hiermit wird eine vorhandene Imageversion aus einer Azure Compute Gallery als Quellimage festgelegt.

> [!NOTE]
> Das verwaltete Quellimage muss ein Image eines unterstützten Betriebssystems sein und sich in derselben Region wie Ihre Azure Image Builder-Vorlage befinden. Wenn dies nicht der Fall ist, replizieren Sie die Imageversion in die Region der Image Builder-Vorlage.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
        } 
```

`imageVersionId` sollte dem ResourceId-Wert der Imageversion entsprechen. Verwenden Sie den Befehl [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list), um die Imageversionen aufzulisten.


## <a name="properties-buildtimeoutinminutes"></a>Eigenschaften: buildTimeoutInMinutes

Image Builder wird standardmäßig für 240 Minuten ausgeführt. Anschließend kommt es zu einem Timeout, und die Ausführung wird beendet – unabhängig davon, ob das Image erstellt wurde oder nicht. Bei Auftreten des Timeouts wird ein Fehler ähnlich dem folgenden angezeigt:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Wenn Sie keinen buildTimeoutInMinutes-Wert angeben oder diesen Wert auf „0“ festlegen, wird der Standardwert verwendet. Sie können den Wert verringern oder bis auf den Höchstwert von 960 Minuten (16 Stunden) erhöhen. Für Windows wird empfohlen, diese Einstellung nicht unter 60 Minuten festzulegen. Wenn es zu einem Timeout kommt, überprüfen Sie in den [Protokollen](image-builder-troubleshoot.md#customization-log), ob der Anpassungsschritt auf etwas wartet, z.B. auf eine Benutzereingabe. 

Wenn Sie mehr Zeit zum Abschließen der Anpassung benötigen, legen Sie die Einstellung auf die geschätzte benötigte Zeit plus einen kleinen Puffer fest. Wählen Sie aber keinen zu hohen Wert, da ein Fehler erst bei einem Timeout angezeigt wird. 

> [!NOTE]
> Wenn Sie den Wert nicht auf 0 festlegen, beträgt der unterstützte Mindestwert sechs Minuten. Die Verwendung der Werte 1 bis 5 führt zu einem Fehler.

## <a name="properties-customize"></a>Eigenschaften: customize

Image Builder unterstützt mehrere „Anpassungen“. Anpassungen sind Funktionen, die zum Anpassen Ihres Images verwendet werden. Dies umfasst beispielsweise das Ausführen von Skripts oder Neustarten von Servern. 

Folgendes ist bei Verwendung von `customize` zu beachten: 
- Sie können mehrere Anpassungen verwenden, die jedoch eine eindeutige `name`-Eigenschaft aufweisen müssen.
- Anpassungen werden nach der in der Vorlage festgelegten Reihenfolge ausgeführt.
- Wenn eine Anpassung fehlschlägt, schlägt die gesamte Anpassungskomponente fehl, und ein Fehler wird zurückgegeben.
- Es wird dringend empfohlen, dass Sie das Skript gründlich testen, bevor Sie es in einer Vorlage verwenden. Das Debuggen des Skripts ist in Ihrer eigenen VM einfacher.
- Fügen Sie keine sensiblen Daten in die Skripts ein. 
- Die Speicherorte für die Skripts müssen öffentlich zugänglich sein, es sei denn, Sie verwenden die [verwaltete Dienstidentität](./image-builder-user-assigned-identity.md).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Der Abschnitt „customize“ ist ein Array. Azure Image Builder durchläuft die Anpassungen in sequenzieller Reihenfolge. Wenn ein Fehler bei einer beliebigen Anpassung auftritt, schlägt der gesamte Buildprozess fehl. 

> [!NOTE]
> Inlinebefehle können in der Imagevorlagendefinition angezeigt werden. Wenn Sie über sensible Informationen wie Kennwörter, SAS-Token oder Authentifizierungstoken verfügen, sollten diese in Skripts in Azure Storage verschoben werden, weil dort für den Zugriff eine Authentifizierung erforderlich ist.
 
### <a name="shell-customizer"></a>Shellanpassung

Die Shellanpassung unterstützt die Ausführung von Shellskripts. Die Shellskripts müssen öffentlich zugänglich sein, oder Sie müssen eine [MSI](./image-builder-user-assigned-identity.md) konfiguriert haben, damit Image Builder auf sie zugreifen kann.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
    }, 
    ],
```

Betriebssystemunterstützung: Linux 
 
Anpassungseigenschaften:

- **type:** Shell 
- **name:** Name für die Nachverfolgung der Anpassung 
- **scriptUri:** URI für den Speicherort der Datei 
- **inline:** Array von Shellbefehlen, die durch Kommas getrennt sind
- **sha256Checksum**: Der Wert der SHA256-Prüfsumme der Datei, den Sie lokal generieren. Anschließend überprüft Image Builder die Prüfsumme und validiert sie.
    * Um sha256Checksum mithilfe eines Terminals unter Mac/Linux zu generieren, führen Sie Folgendes aus: `sha256sum <fileName>`

> [!NOTE]
> Inlinebefehle werden als Bestandteil der Imagevorlagendefinition gespeichert. Sie können sie anzeigen, indem Sie die Imagedefinition ausgeben. Wenn Sie vertrauliche Befehle oder Werte wie Kennwörter, SAS-Token oder Authentifizierungstoken verwenden, wird dringend empfohlen, diese in Skripts zu verschieben und eine Benutzeridentität zur Authentifizierung bei Azure Storage zu verwenden.

#### <a name="super-user-privileges"></a>Administratorrechte
Damit Befehle mit Administratorrechten ausgeführt werden, muss ihnen das Präfix `sudo` vorangestellt werden. Dieses Präfix können Sie in Skripts einfügen oder als Inlinebefehle verwenden, beispielsweise:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Beispiel eines Skripts mit dem Präfix „sudo“, auf das mithilfe von „scriptUri“ verwiesen werden kann:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows-Neustartanpassung 
Mithilfe der Neustartanpassung können Sie eine Windows-VMM neu starten und darauf warten, dass sie wieder online geschaltet wird. Dies ermöglicht die Installation von Software, für die ein Neustart erforderlich ist.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Betriebssystemunterstützung: Windows
 
Anpassungseigenschaften:
- **Typ:** WindowsRestart
- **restartCommand:** Hiermit wird der Befehl zum Ausführen des Neustarts festgelegt (Optional). Der Standardwert lautet `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand:** Befehl zur Überprüfung, ob der Neustart erfolgreich war (Optional). 
- **restartTimeout:** Zeichenfolge von Größe und Einheit des Timeouts für den Neustart, z. B. `5m` (5 Minuten) oder `2h` (2 Stunden). Der Standardwert lautet: „5m“

### <a name="linux-restart"></a>Linux-Neustart  
Es gibt keine Anpassung für den Linux-Neustart, aber wenn Sie Treiber oder Komponenten installieren, die einen Neustart erfordern, können Sie diese installieren und einen Neustart mit der Shellanpassung aufrufen. Es gibt ein 20-minütiges SSH-Zeitlimit für die Build-VM.

### <a name="powershell-customizer"></a>PowerShell-Anpassung 
Die Shellanpassung unterstützt das Ausführen von PowerShell-Skripts und Inlinebefehlen. Diese Skripts müssen öffentlich zugänglich sein, damit Image Builder auf diese zugreifen kann.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
     ], 
```

Betriebssystemunterstützung: Windows

Anpassungseigenschaften:

- **type:** PowerShell
- **scriptUri:** URI für den Speicherort der PowerShell-Skriptdatei 
- **inline:** Durch Kommas getrennte Inlinebefehle, die ausgeführt werden sollen
- **validExitCodes**: Optional, gültige Codes, die vom Skript/Inlinebefehl zurückgegeben werden können, um die Meldung eines Fehlers im Skript/Inlinebefehl zu vermeiden.
- **runElevated**: Optionaler boolescher Wert, Unterstützung für das Ausführen von Befehlen und Skripts mit erhöhten Berechtigungen.
- **sha256Checksum**: Der Wert der SHA256-Prüfsumme der Datei, den Sie lokal generieren. Anschließend überprüft Image Builder die Prüfsumme und validiert sie.
    * So generieren Sie sha256Checksum mithilfe des PowerShell-Befehls [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash) unter Windows


### <a name="file-customizer"></a>Dateianpassung

Mithilfe der Dateianpassung kann Image Builder eine Datei aus einem GitHub-Repository oder Azure Storage herunterladen. Wenn Sie über eine Buildpipeline für Images verfügen, die Buildartefakte verwendet, können Sie die Dateianpassung für den Download aus der Buildfreigabe konfigurieren und die Artefakte in das Image verschieben.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Betriebssystemunterstützung: Linux und Windows 

Dateianpassungseigenschaften:

- **sourceUri:** ein Speicherendpunkt, auf den zugegriffen werden kann; hier kann GitHub oder Azure Storage verwendet werden. Sie können nur eine Datei herunterladen, kein ganzes Verzeichnis. Wenn Sie ein Verzeichnis herunterladen müssen, verwenden Sie eine komprimierte Datei, und dekomprimieren Sie diese dann mithilfe der Shell- oder PowerShell-Anpassungen. 

> [!NOTE]
> Wenn „sourceUri“ ein Azure Storage-Konto ist, müssen Sie unabhängig davon, ob das Blob als öffentlich markiert ist, der verwalteten Benutzeridentität Berechtigungen für den Schreibzugriff auf das Blob erteilen. Informationen zum Festlegen der Speicherberechtigungen finden Sie in [diesem Beispiel](./image-builder-user-assigned-identity.md#create-a-resource-group).

- **destination:** dies ist der vollständige Zielpfad mit dem Dateinamen. Alle referenzierten Pfade und Unterverzeichnisse müssen vorhanden sein. Verwenden Sie die Shell- oder PowerShell-Anpassungen, um diese im Voraus einzurichten. Sie können die Skriptanpassungen zum Erstellen des Pfads verwenden. 

Diese Vorgehensweise wird von Windows-Verzeichnissen und Linux-Pfaden unterstützt, jedoch gibt es einige Unterschiede: 
- Linux-Betriebssysteme: Image Builder kann nur in den Pfad „/tmp“ schreiben.
- Windows: Es besteht keine Pfadeinschränkung, jedoch muss der Pfad vorhanden sein.
 

Wenn beim Herunterladen der Datei oder beim Platzieren der Datei im festgelegten Verzeichnis ein Fehler auftritt, schlägt der Anpassungsschritt fehl. Dies wird im Protokoll „customization.log“ dokumentiert.

> [!NOTE]
> Die Dateianpassung ist nur für kleine Dateidownloads geeignet, < 20 MB. Für größere Dateidownloads verwenden Sie einen Skript- oder Inline-Befehl und dann den Code zum Herunterladen von Dateien, z. B. Linux `wget` oder `curl`, Windows, `Invoke-WebRequest`.

### <a name="windows-update-customizer"></a>Windows Update-Anpassung
Diese Anpassung basiert auf dem [Community Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) für Packer. Dabei handelt es sich um ein von der Packer-Community verwaltetes Open-Source-Projekt. Microsoft testet und überprüft den Provisioner mit dem Image Builder-Dienst und unterstützt das Untersuchen von Problemen mit dem Dienst sowie das Beheben von Problemen. Das Open-Source-Projekt wird jedoch nicht offiziell von Microsoft unterstützt. Eine ausführliche Dokumentation und Hilfe zu Windows Update Provisioner finden Sie im Projektrepository.

```json
     "customize": [
          {
               "type": "WindowsUpdate",
               "searchCriteria": "IsInstalled=0",
               "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
               ],
               "updateLimit": 20
          }
     ], 
```

Betriebssystemunterstützung: Windows

Anpassungseigenschaften:
- **type**: WindowsUpdate.
- **searchCriteria**: Optional, definiert, welche Art von Updates installiert werden (empfohlene Updates, wichtige Updates usw.), die Standardeinstellung ist „BrowseOnly=0 and „IsInstalled=0“ (Recommended).
- **filters**: Optional können Sie einen Filter zum Ein- oder Ausschließen von Updates angeben.
- **updateLimit**: Optional, definiert, wie viele Updates installiert werden können. Der Standardwert ist „1000“.
 
> [!NOTE]
> Die Windows Update-Anpassung kann einen Fehler verursachen, wenn Windows-Neustarts ausstehen oder Anwendungsinstallationen noch ausgeführt werden. in der Regel wird dieser Fehler in der Datei „customization.log“ angezeigt: `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016`. Es wird dringend empfohlen, einen Windows-Neustart hinzuzufügen und/oder in Anwendungen mithilfe von [sleep](/powershell/module/microsoft.powershell.utility/start-sleep)- oder wait-Befehlen in den Inlinebefehlen oder Skripts genug Zeit zum Abschluss der Installation festzulegen, bevor Sie Windows Update ausführen.

### <a name="generalize"></a>Generalize 
Azure Image Builder führt außerdem standardmäßig Code zum „Aufheben der Bereitstellung“ nach jeder Imageanpassungsphase aus, um das Image zu „generalisieren“. Das Generalisieren ist ein Prozess, bei dem das Image so eingerichtet wird, dass es für die Erstellung mehrerer VMs wiederverwendet werden kann. Azure Image Builder verwendet Sysprep für Windows-VMs. Für Linux führt Azure Image Builder „waagent -deprovision“ aus. 

Die Befehle, die Image Builder für die Generalisierung verwendet, eignen sich möglicherweise nicht für jede Situation, weshalb Azure Image Builder die Anpassung der Befehle bei Bedarf zulässt. 

Wenn Sie vorhandene Anpassungen migrieren und verschiedene Sysprep- oder waagent-Befehle verwenden, können Sie die allgemeinen Image Builder-Befehle nutzen. Und wenn die VM-Erstellung fehlschlägt, können Sie Ihre eigenen Sysprep- oder waagent-Befehle verwenden.

Wenn Azure Image Builder erfolgreich ein benutzerdefiniertes Windows-Image erstellt, Sie mit dem Image eine VM erstellen und die VM-Erstellung dann fehlschlägt bzw. nicht erfolgreich durchgeführt wird, müssen Sie die Dokumentation zu Windows Server-Sysprep zu Rate ziehen oder eine Supportanfrage beim Supportteam für den Kundendienst für Windows Server-Sysprep eröffnen, um Unterstützung bei der Problembehandlung und Tipps zur ordnungsgemäßen Nutzung von Sysprep zu erhalten.


#### <a name="default-sysprep-command"></a>Sysprep-Standardbefehl
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>deprovision-Standardbefehl für Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Überschreiben der Befehle
Verwenden Sie zum Überschreiben der Befehle die Shell- oder PowerShell-Skriptbereitstellungen, um die Befehlsdateien mit dem gleichen Dateinamen zu erstellen, und fügen Sie sie in die richtigen Verzeichnisse ein:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Diese Befehle werden von Image Builder gelesen und in den AIB-Protokollen („customization.log“) dokumentiert. Auf der Seite zur [Problembehandlung](image-builder-troubleshoot.md#customization-log) erfahren Sie, wie Sie Protokolle erfassen.
 
## <a name="properties-distribute"></a>Eigenschaften: distribute

Azure Image Builder unterstützt drei Verteilungsziele: 

- **managedImage:** ein verwaltetes Image
- **sharedImage** - Azure Compute Gallery.
- **VHD:** VHD in einem Speicherkonto

Sie können ein Image in ein und derselben Konfiguration an beide Zieltypen verteilen.

> [!NOTE]
> Der standardmäßige AIB-Sysprep-Befehl enthält „/mode:vm“ nicht. Dies ist jedoch möglicherweise erforderlich, wenn Sie Images erstellen, in denen die HyperV-Rolle installiert ist. Wenn Sie dieses Befehlsargument hinzufügen müssen, müssen Sie den Sysprep-Befehl überschreiben.

Da Sie mehrere Verteilungsziele verwenden können, verwaltet Image Builder für jedes Verteilungsziel einen Zustand, auf den durch Abfragen von `runOutputName` zugegriffen werden kann.  `runOutputName` ist ein Objekt, das Sie nach der Verteilung abfragen können, um Informationen über die Verteilung abzurufen. Sie können beispielsweise den Speicherort der VHD oder die Regionen abfragen, in denen die Imageversion repliziert wurde. Dies ist eine Eigenschaft aller Verteilungsziele. `runOutputName` muss für jedes Verteilungsziel eindeutig sein. Das folgende Beispiel fragt eine Azure Compute Gallery-Distribution ab:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Ausgabe:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribute: managedImage

Die Imageausgabe ist eine verwaltete Imageressource.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name>": "<value>",
            "<name>": "<value>"
        }
}
```
 
Verteilungseigenschaften:
- **type:** managedImage 
- **imageId:** die Ressourcen-ID des Zielimages; das folgende Format wird erwartet: /subscriptions/\<subscriptionId>/resourceGroups/\<destinationResourceGroupName>/providers/Microsoft.Compute/images/\<imageName>
- **location:** der Speicherort des verwalteten Images.  
- **runOutputName:** eindeutiger Name zur Identifikation der Verteilung.  
- **artifactTags:** optionale vom Benutzer angegebene Schlüssel-Wert-Paar-Tags.
 
 
> [!NOTE]
> Die Zielressourcengruppe muss vorhanden sein.
> Wenn Sie ein Image in einer anderen Region verteilt werden soll, steigt auch die erforderliche Bereitstellungszeit. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
Die Azure Compute Gallery ist ein neuer Dienst für die Verwaltung von Images, mit dem die Imagereplikation in Regionen, die Versionskontrolle und die Freigabe benutzerdefinierter Images verwaltet werden können. Azure Image Builder unterstützt die Verteilung mit diesem Dienst. Sie können Images also in Regionen verteilen, die von der Azure Compute Gallery unterstützt werden. 
 
Eine Azure Compute Gallery besteht aus: 
 
- Katalog: Container für mehrere Images. Ein Katalog wird in einer Region bereitgestellt.
- Imagedefinitionen: eine konzeptionelle Gruppierung von Images. 
- Imageversionen: ein Imagetyp für die Bereitstellung einer VM oder Skalierungsgruppe. Imageversionen können in andere Regionen repliziert werden, in denen VMs bereitgestellt werden müssen.
 
Bevor Sie ein Image an den Katalog verteilen können, müssen Sie einen Katalog und eine Imagedefinition erstellen. Informationen dazu finden Sie unter [Erstellen eines Katalogs](../create-gallery.md). 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Verteilen von Eigenschaften für Kataloge:

- **type:** sharedImage  
- **galleryImageId**: ID der Azure Compute Gallery, die in zwei Formaten angegeben werden kann:
    * Automatische Versionsverwaltung: Image Builder generiert eine monotone Versionsnummer für Sie. Dies ist nützlich, wenn Sie weiterhin Images aus derselben Vorlage neu erstellen möchten: Das Format ist `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>`.
    * Explizite Versionsverwaltung: Sie können die Versionsnummer übergeben, die von Image Builder verwendet werden soll. Das Format ist `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName:** eindeutiger Name zur Identifikation der Verteilung.  
- **artifactTags:** optionale vom Benutzer angegebene Schlüssel-Wert-Paar-Tags.
- **replicationRegions:** Array von Regionen für die Replikation. In einer der hier enthaltenen Regionen muss der Katalog bereitgestellt werden. Das Hinzufügen von Regionen bedeutet eine Erhöhung der Buildzeit, da der Build erst abgeschlossen wird, wenn die Replikation fertig ist.
- **excludeFromLatest** (optional): Hiermit können Sie die von Ihnen erstellte Imageversion markieren, die nicht als neueste Version in der Katalogdefinition verwendet wird. Der Standardwert ist „false“.
- **storageAccountType** (optional): AIB unterstützt die Angabe dieser Speichertypen für die Imageversion, die erstellt werden soll:
    * „Standard_LRS“
    * „Standard_ZRS“


> [!NOTE]
> Wenn sich die Imagevorlage und die referenzierten `image definition` nicht am gleichen Standort befinden, wird zusätzliche Zeit zum Erstellen von Images angezeigt. Image Builder verfügt derzeit nicht über einen `location`-Parameter für die Imageversionsressource, daher nehmen wir ihn von seiner übergeordneten `image definition`. Wenn sich z. B. eine Imagedefinition in „westus“ (USA, Westen) befindet und Sie möchten, dass die Imageversion nach „eastus“ (USA, Osten) repliziert wird, wird ein Blob nach „westus“ kopiert. Daraus wird eine Imageversionsressource in „westus“ erstellt und dann nach „eastus“ repliziert. Stellen Sie zur Vermeidung der zusätzlichen Replikationszeit sicher, dass sich `image definition` und die Imagevorlage am selben Standort befinden.


### <a name="distribute-vhd"></a>Distribute: VHD  
Sie können die Ausgabe in einer VHD erstellen. Anschließend können Sie die VHD kopieren und für die Veröffentlichung im Azure Marketplace oder mit Azure Stack verwenden.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    }
}
```
 
Betriebssystemunterstützung: Windows und Linux

VHD-Verteilungsparameter:

- **type:** VHD
- **runOutputName:** eindeutiger Name zur Identifikation der Verteilung.  
- **tags:** optionale vom Benutzer angegebene Schlüssel-Wert-Paar-Tags.
 
Azure Image Builder ermöglicht das Festlegen von Speicherkontostandorten durch Benutzer nicht, Sie können jedoch den Status von `runOutputs` abfragen, um den Standort abzurufen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Kopieren Sie die VHD so bald wie möglich an einen anderen Speicherort, sobald diese erstellt wurde. Die VHD wird in einem Speicherkonto in der temporären Ressourcengruppe gespeichert, die erstellt wird, wenn die Imagevorlage an den Azure Image Builder-Dienst übermittelt wird. Wenn Sie die Imagevorlage löschen, wird auch die VHD gelöscht. 

## <a name="image-template-operations"></a>Vorgänge für Imagevorlagen

### <a name="starting-an-image-build"></a>Starten der Imageerstellung
Um einen Build zu starten, müssen Sie „Ausführen“ für die Imagevorlagenressource aufrufen. Beispiele für `run`-Befehle:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Abbrechen einer Imageerstellung
Wenn Sie eine Imageerstellung ausführen, die Ihrer Meinung nach fehlerhaft ist, auf Benutzereingaben wartet oder von der Sie glauben, dass sie nie erfolgreich abgeschlossen werden kann, können Sie die Erstellung abbrechen.

Der Build kann jederzeit abgebrochen werden. Wenn die Verteilungsphase begonnen hat, können Sie immer noch abbrechen, aber Sie müssen alle Images bereinigen, die möglicherweise nicht abgeschlossen sind. Der Abbruchbefehl wartet nicht auf den Abschluss des Abbruchs. Überwachen Sie `lastrunstatus.runstate` auf den Fortschritt des Abbruchs, indem Sie diese [Statusbefehle](image-builder-troubleshoot.md#customization-log) verwenden.


Beispiele für `cancel`-Befehle:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Nächste Schritte

JSON-Beispieldateien für verschiedene Szenarios finden Sie im [GitHub-Repository für Azure Image Builder](https://github.com/azure/azvmimagebuilder).

