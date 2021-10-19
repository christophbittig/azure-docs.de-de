---
title: Azure Compute – Linux Diagnostic Extension 3.0
description: Hier erfahren Sie, wie Sie die Linux Diagnostic Extension 3.0 (LAD 4.0) von Azure zum Erfassen von Metriken und Protokollieren von Ereignissen von in Azure ausgeführten virtuellen Linux-Computern konfigurieren.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04264276b3419e32aa420792027502ce3d1acbb2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669680"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Verwenden der Linux Diagnostic Extension 3.0 zum Überwachen von Metriken und Protokollen

Dieses Dokument beschreibt Version 3.0 und höher der Linux Diagnostic Extension (LAD).

> [!IMPORTANT]
> Informationen zu Version 2.3 und früher finden Sie unter [Überwachen der Leistungs- und Diagnosedaten eines virtuellen Linux-Computers](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Einführung

Mit der Linux-Diagnoseerweiterung können Benutzer die Integrität von Linux-VMs überwachen, die in Microsoft Azure ausgeführt werden. Sie verfügt über die folgenden Funktionen:

* Erfassen von Leistungsmetriken des Systems vom virtuellen Computer und Speichern dieser in einer bestimmten Tabelle in einem angegebenen Speicherkonto
* Abrufen von Protokollereignissen aus Syslog und Speichern dieser in einer bestimmten Tabelle im angegebenen Speicherkonto
* Anpassen der zu sammelnden und hochzuladenden Datenmetriken durch Benutzer
* Anpassen der Syslog-Funktionen und der Schweregrade von Ereignissen, die gesammelt und hochgeladen werden
* Hochladen angegebener Protokolldateien in eine festgelegte Speichertabelle durch Benutzer
* Senden von Metriken und Protokollereignissen an beliebige Azure Event Hub-Endpunkte und JSON-formatierte Blobs im angegebenen Speicherkonto.

Diese Erweiterung funktioniert mit beiden Azure-Bereitstellungsmodellen.

## <a name="install-the-extension-on-a-vm"></a>Installieren Sie die Erweiterung auf eine VM

Sie können die Erweiterung mithilfe von Azure PowerShell cmdlets, Azure CLI Skripts, Azure Ressourcemonitor-Vorlagen (ARM-Vorlagen) oder dem Azure-Portal aktivieren. Weitere Informationen finden Sie unter [Erweiterungsfeatures](features-linux.md).

>[!NOTE]
>Einige Komponenten der LAD-VM-Erweiterung werden auch mit der [Protkollanalyse-Erweiterung für Diagnosen](./oms-linux.md) geliefert. Wegen dieser Architektur können Konflikte auftreten, wenn beide Erweiterungen in derselben ARM-Vorlage instanziiert werden. 
>
>Um Konflikte zur Installationszeit zu vermeiden, verwenden Sie die [`dependsOn`-Anweisung](../../azure-resource-manager/templates/resource-dependency.md#dependson), um sicherzustellen, dass die Erweiterungen nacheinander installiert werden. Die Erweiterungen können in beliebiger Reihenfolge installiert werden.

Diese Installationsanweisungen konfigurieren LAD 3.0 mithilfe einer [herunterladbaren Beispielkonfiguration](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) für Folgendes:

* Erfassen und Speichern derselben Metriken wie in LAD 2.3.
* Erfassen eines nützlichen Satzes von Dateisystemmetriken. Diese Funktionalität ist bei LAD 3.0 neu.
* Erfassen der Syslog-Standardsammlung, die durch LAD 2.3 aktiviert wurde.
* Aktivieren von Diagrammen und Warnungen zu VM-Metriken in der Azure-Portal-Umgebung.

Die herunterladbare Konfiguration ist nur ein Beispiel. Passen Sie es Ihren Anforderungen entsprechend an.

### <a name="supported-linux-distributions"></a>Unterstützte Linux-Distributionen

LAD unterstützt die folgenden Distributionen und Versionen. Die Liste der Distributionen und Versionen gilt nur für von Azure unterstützte Images von Linux-Anbietern. Die Erweiterung unterstützt in der Regel keine BYOL- und BYOS-Images von Drittanbietern wie Appliances.

Eine Distribution, die nur Hauptversionen auflistet, z. B. Debian 7, wird auch für alle Nebenversionen unterstützt. Wenn eine Nebenversion angegeben wird, wird nur diese Version unterstützt. Wenn ein Pluszeichen (+) angefügt wird, werden Nebenversionen unterstützt, die der angegebenen Version oder höher sind.

Unterstützte Distributionen und Versionen:

- Ubuntu 20.04, 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Voraussetzungen

* **Azure Linux Agent ab Version 2.2.0**. Die meisten Images des Azure-Katalogs für virtuelle Linux-Computer enthalten Version 2.2.7 oder höher. Führen Sie `/usr/sbin/waagent -version` aus, um die auf dem virtuellen Computer installierte Version zu überprüfen. Wenn die VM mit einer älteren Version ausgeführt wird, müssen Sie den [Gast-Agent aktualisieren](./update-linux-agent.md).
* Die **Azure CLI** Falls nötig, [richten Sie Azure CLI](/cli/azure/install-azure-cli) auf dem Computer ein.
* Der **wget-Befehl**. Wenn Sie noch nicht über eine App verfügen, führen Sie `sudo apt-get install wget` aus.
* Ein bestehendes **Azure-Abonnement**. 
* Ein vorhandenes **universelles Speicherkonto** zum Speichern der Daten. Universelle Speicherkonten müssen Tabellenspeicher unterstützen. Ein Blobspeicherkonto funktioniert nicht.
* **Python 2**.

### <a name="python-requirement"></a>Python-Anforderung

Die Linux-Diagnoseerweiterung erfordert Python 2. Wenn Ihr virtueller Computer eine Distribution verwendet, in der Python 2 nicht standardmäßig enthalten ist, müssen Sie die Sprache installieren. Mithilfe der folgenden Beispielbefehle wird Python 2 auf verschiedenen Distributionen installiert:   

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

Die ausführbare `python2`-Datei muss dem Alias *python* zugewiesen werden. Hier folgt eine Methode zum Festlegen dieses Alias:

1. Führen Sie den folgenden Befehl aus, um eventuell vorhandene Aliase zu entfernen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Führen Sie den folgenden Befehl aus, um den Alias zu erstellen.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Beispielinstallation

Die in den folgenden Beispielen heruntergeladene Beispielkonfiguration sammelt eine Reihe von Standarddaten und sendet diese an den Tabellenspeicher. Die URL für die Beispielkonfiguration und ihr Inhalt können sich ändern. 

In den meisten Fällen sollten Sie eine Kopie der JSON-Datei mit den Portaleinstellungen herunterladen und an Ihre Anforderungen anpassen. Verwenden Sie dann Vorlagen oder Ihre eigene Automatisierung, um eine benutzerdefinierte Version der Konfigurationsdatei zu verwenden, anstatt sie jedes Mal von der URL herunterzuladen.

> [!NOTE]
> Geben Sie vor der Ausführung des Codes bei beiden folgenden Beispielen im ersten Abschnitt die richtigen Werte für die Variablen ein. 
#### <a name="azure-cli-sample"></a>Azure CLI-Beispiel

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Azure CLI-Beispiel für die Installation von LAD 3.0 in der VMSS-Instanz

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell-Beispiel

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Aktualisieren der Erweiterungseinstellungen

Nachdem Sie Ihre geschützten oder öffentlichen Einstellungen geändert haben, stellen Sie sie auf dem virtuellen Computer bereit, indem Sie denselben Befehl ausführen. Wenn die Einstellungen geändert wurden, werden die Updates an die Erweiterung gesendet. LAD lädt die Konfiguration erneut und startet sich selbst neu.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrieren von früheren Versionen der Erweiterung

Die neueste Version der Erweiterung ist *4.0*. 

> [!IMPORTANT]
> Diese Erweiterung enthält grundlegende Änderungen an der Konfiguration. Eine solche Änderung hat die Sicherheit der Erweiterung verbessert, sodass die Abwärtskompatibilität mit 2.x nicht aufrechterhalten werden konnte. Darüber hinaus unterscheidet sich der Herausgeber dieser Erweiterung von dem für die Versionen 2.x.
>
> Um von 2.x zur neuen Version zu migrieren, deinstallieren Sie zuerst die alte Erweiterung (unter dem alten Herausgebernamen). Installieren Sie dann Version 3.

Empfehlungen:

* Installieren Sie die Erweiterung mit aktivierten automatischen Nebenversionsupgrades.
  * Auf virtuellen Computern im klassischen Bereitstellungsmodus geben Sie dazu als Version `3.*` an, wenn Sie die Erweiterung über die plattformübergreifende Azure-Befehlszeilenschnittstelle oder über PowerShell installieren.
  * Bei virtuellen Computern im Azure Resource Manager-Bereitstellungsmodell fügen Sie `"autoUpgradeMinorVersion": true` in die VM-Bereitstellungsvorlage ein.
* Verwenden Sie für LAD 3.0 ein neues oder ein anderes Speicherkonto. Es gibt mehrere kleine Inkompatibilitäten zwischen LAD 2.3 und LAD 3.0, die bei einer gemeinsamen Nutzung eines Kontos zu Konflikten führen können:
  * LAD 3.0 speichert Syslog-Ereignisse in einer Tabelle mit einem anderen Namen.
  * Die `counterSpecifier`-Zeichenfolgen für `builtin`-Metriken unterscheiden sich in LAD 3.0.

## <a name="protected-settings"></a>Geschützte Einstellungen

Dieser Satz von Konfigurationsinformationen enthält vertrauliche Informationen, die nicht öffentlich einsehbar sein dürfen. Sie enthält z. B. Speicheranmeldeinformationen. Diese Einstellungen werden von der Erweiterung in verschlüsselter Form übertragen und gespeichert.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Wert
---- | -----
storageAccountName | Der Name des Speicherkontos, in dem von der Erweiterung Daten geschrieben werden
storageAccountEndPoint | (Optional) Der Endpunkt, der die Cloud mit dem Speicherkonto angibt. Wenn diese Einstellung fehlt, verwendet LAD standardmäßig die öffentliche Azure-Cloud, `https://core.windows.net`. Um ein Speicherkonto in Azure Deutschland, Azure Government oder Azure China 21 Vianet zu verwenden, legen Sie diesen Wert den Vorgaben entsprechend fest.
storageAccountSasToken | Ein [Konto-SAS-Token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) für Blob- und Tabellendienste (`ss='bt'`). Es gilt für Container und Objekte (`srt='co'`). Es gewährt Berechtigungen zum Hinzufügen, Erstellen, Auflisten, Aktualisieren und Schreiben (`sp='acluw'`). Fügen Sie *nicht* das vorangestellte Fragezeichen (?) ein.
mdsdHttpProxy | (Optional) HTTP-Proxyinformationen, die für die Erweiterung erforderlich sind, damit sie Verbindungen mit dem angegebenen Speicherkonto und dem Endpunkt herstellen kann.
sinksConfig | (Optional) Details zu alternativen Zielen, an die Metriken und Ereignisse übermittelt werden können. In den folgenden Abschnitten werden Details zu den einzelnen Datensenken erläutert, die von der Erweiterung unterstützt werden.

Verwenden Sie die `listAccountSas`-Funktion, um ein SAS-Token innerhalb einer ARM-Vorlage zu erhalten. Eine Beispielvorlage finden Sie unter [Beispiel für eine Listenfunktion](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Sie können das erforderliche SAS-Token über das Azure-Portal erstellen:

1. Wählen Sie das universelle Speicherkonto aus, in das die Erweiterung schreiben soll.
1. Wählen Sie im Menü auf der linken Seite unter **Einstellungen** die Option **Shared Access Signature** aus.
1. Treffen Sie die Auswahl wie zuvor beschrieben.
1. Wählen Sie **SAS generieren** aus.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Screenshot der Shared Access Signature-Seite mit der Schaltfläche „SAS generieren“.":::

Kopieren Sie die generierte SAS in das `storageAccountSasToken`-Feld. Entfernen Sie das führende Fragezeichen (?).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

In diesem optionalen Abschnitt `sinksConfig` werden weitere Ziele definiert, an die die Erweiterung die gesammelten Informationen sendet. Das Array `sink` enthält ein Objekt für jede zusätzliche Datensenke. Das Attribut `type` bestimmt die anderen Attribute im Objekt.

Element | Wert
------- | -----
name | Eine Zeichenfolge, die auf diese Senke an anderer Stelle in der Konfiguration der Erweiterung verweist.
type | Der Typ der Senke, die definiert wird. Bestimmt die anderen Werte in Instanzen dieses Typs (sofern vorhanden).

LAD Version 3.0 unterstützt zwei Senkentypen: `EventHub` und `JsonBlob`.

#### <a name="eventhub-sink"></a>EventHub-Senke

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

Der Eintrag `"sasURL"` enthält die vollständige URL, einschließlich des SAS-Tokens für den Event Hub, an den die Daten veröffentlicht werden sollen. LAD erfordert eine SAS-Benennungsrichtlinie, die den Anspruch „Senden“ aktiviert. 

Beispiel:

* Erstellen eines Azure Event Hubs-Namespace `contosohub`.
* Erstellen Sie einen Event Hub im Namespace `syslogmsgs`.
* Erstellen Sie auf dem Event Hub die SAS-Richtlinie, die den Anspruch „Senden“ aktiviert. Benennen Sie die Richtline `writer`.

Wenn Ihre SAS bis Mitternacht UTC am 1. Januar 2018 gültig ist, könnte der sasURL-Wert wie im folgenden Beispiel aussehen:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Weitere Informationen zum Generieren von SAS-Token und Abrufen von Informationen für SAS-Token für Event Hubs finden Sie auf [ein SAS-Token generieren](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="jsonblob-sink"></a>JsonBlob-Senke

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Die an die `JsonBlob`-Senke weitergeleiteten Daten werden in Blobs in Azure Storage gespeichert. Jede LAD-Instanz erstellt stündlich ein Blob für jeden Senkennamen. Jedes Blob enthält immer ein syntaktisch gültiges JSON-Array von Objekten. Neue Einträge werden dem Array automatisch hinzugefügt. 

Blobs werden in einem Container mit dem gleichen Namen wie die Senke gespeichert. Die Azure Storage-Regeln für Blob-Containernamen gelten auch für die Namen von `JsonBlob`-Senken. Der Name muss zwischen 3 und 63 alphanumerische ASCII-Zeichen in Kleinbuchstaben oder Bindestriche enthalten.

## <a name="public-settings"></a>Öffentliche Einstellungen

Diese Struktur der öffentlichen Einstellungen enthält verschiedene Blöcke von Einstellungen zur Steuerung der von der Erweiterung gesammelten Informationen. Diese Einstellungen sind optional. Bei Angabe von `ladCfg` müssen Sie auch `StorageAccount` angeben.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Wert
------- | -----
StorageAccount | Der Name des Speicherkontos, in dem von der Erweiterung Daten geschrieben werden Er muss mit dem Namen übereinstimmen, der in den [geschützten Einstellungen](#protected-settings) festgelegt wurde.
mdsdHttpProxy | (Optional) Wie bei den [geschützten Einstellungen](#protected-settings). Der öffentliche Wert wird durch den geschützten Wert überschrieben, sofern dieser festgelegt wurde. Nehmen Sie Proxyeinstellungen, die ein Geheimnis (z.B. ein Kennwort) enthalten, in den [geschützten Einstellungen](#protected-settings) vor.

Die folgenden Abschnitte enthalten weitere Informationen zu den verbleibenden Elementen.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Die `ladCfg`-Struktur ist optional. Sie steuert das Sammeln von Metriken und Protokollen für die Übermittlung an den Azure Monitor-Metrikdienst und andere Datensenken. Dabei müssen Sie festlegen:

* Entweder `performanceCounters` oder `syslogEvents` oder beides. 
* Die `metrics`-Struktur.

Element | Wert
------- | -----
eventVolume | (Optional) Steuert die Anzahl der Partitionen, die innerhalb der Speichertabelle erstellt werden. Muss `"Large"`, `"Medium"`, oder `"Small"` sein. Wenn kein Wert angegeben ist, lautet der Standardwert `"Medium"`.
sampleRateInSeconds | (Optional) Das Standardintervall zwischen der Erfassung von unformatierten (nicht aggregierten) Metriken. Die kleinste unterstützte Erfassungsrate beträgt 15 Sekunden. Wenn der Wert nicht angegeben ist, lautet der Standardwert `15`.

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Wert
------- | -----
resourceId | Die Azure Resource Manager-Ressourcen-ID des virtuellen Computers oder der Skalierungsgruppe, zu der der virtuelle Computer gehört. Diese Einstellung muss ebenfalls angegeben werden, wenn in der Konfiguration eine `JsonBlob`-Senke verwendet wird.
scheduledTransferPeriod | Die Häufigkeit, mit der aggregierte Metriken berechnet und an den Azure Monitor-Metrikendienst übertragen werden. Die Häufigkeit wird als IS 8601-Zeitintervall ausgedrückt. Das kleinste Übertragungsintervall ist 60 Sekunden, d.h. PT1M. Geben Sie mindestens ein `scheduledTransferPeriod` an.

Die Werte für die Metriken, die im `performanceCounters`-Abschnitt angegeben wurden, werden alle 15 Sekunden oder in der explizit für den Leistungsindikator festgelegten Häufigkeit erfasst. Wenn mehrere `scheduledTransferPeriod`-Werte (wie im Beispiel) angegeben wurden, wird jede Aggregation unabhängig von den anderen berechnet.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Mit dem optionalen `performanceCounters`-Abschnitt wird die Erfassung von Metriken gesteuert. Die unformatierten Daten werden für jede [`scheduledTransferPeriod`](#metrics) aggregiert, um diese Werte zu generieren:

* Mittelwert
* Minimum
* Maximum
* Letzter erfasster Wert
* Anzahl der unformatierten Daten für die Aggregatberechnung

Element | Wert
------- | -----
sinks | (Optional) Eine durch Trennzeichen getrennte Liste der Namen der Senken, an die LAD die aggregierten Metrikergebnisse übermittelt. Alle aggregierten Metriken werden an jede aufgeführte Senke veröffentlicht. Beispiel: `"EHsink1, myjsonsink"`. Weitere Informationen finden Sie unter [`sinksConfig`](#sinksconfig).
type | Gibt den tatsächlichen Anbieter der Metrik an.
class | Gibt zusammen mit `"counter"` die jeweilige Metrik im Namespace des Anbieters an.
Zähler | Gibt zusammen mit `"class"` die jeweilige Metrik im Namespace des Anbieters an.
counterSpecifier | Gibt die jeweilige Metrik im Azure Monitor-Metrikennamespace an.
condition | (Optional) Wählt eine bestimmte Instanz des Objekts aus, für die die Metrik gilt. Oder sie wählt die Aggregation für alle Instanzen dieses Objekts aus. 
sampleRate | Das ISO-8601-Intervall, das die Häufigkeit festlegt, mit der unformatierte Daten für diese Metrik gesammelt werden. Wenn der Wert nicht festgelegt ist, wird das Erfassungsintervall durch den Wert von [`sampleRateInSeconds`](#ladcfg) festgelegt. Die kleinste unterstützte Erfassungsrate beträgt 15 Sekunden (PT15S).
unit | Definiert die Einheit für die Metrik. Sollte eine der folgenden Zeichenfolgen sein: `"Count"`, `"Bytes"`, `"Seconds"`, `"Percent"`, `"CountPerSecond"`, `"BytesPerSecond"`, `"Millisecond"`. Consumer der gesammelten Daten erwarten, dass die gesammelten Datenwerte diesen Einheiten entsprechen. LAD ignoriert dieses Feld.
displayName | Die Bezeichnung, die an die Daten in den Metriken von Azure Monitor angefügt werden soll. Diese Bezeichnung ist in der Sprache, die durch die zugeordnete Locale-Einstellung angegeben wird. LAD ignoriert dieses Feld.

`counterSpecifier` ist ein beliebiger Bezeichner. Consumer von Metriken, wie das Azure-Portal-Feature für Diagramme und Warnungen, verwenden `counterSpecifier` als „Schlüssel“, der eine Metrik oder eine Instanz einer Metrik identifiziert. 

Für `builtin`-Metriken empfehlen wir `counterSpecifier`-Werte, die mit `/builtin/` beginnen. Wenn Sie eine bestimmte Instanz einer Metrik erfassen, sollten Sie den Bezeichner der Instanz an den `counterSpecifier`-Wert anfügen. 

Hier einige Beispiele:

* `/builtin/Processor/PercentIdleTime` – Leerlaufzeit (Durchschnitt für alle vCPUs)
* `/builtin/Disk/FreeSpace(/mnt)` – Freier Speicherplatz für `/mnt` das Dateisystem
* `/builtin/Disk/FreeSpace` – freier Speicherplatz (Durchschnitt für alle bereitgestellten Dateisysteme)

LAD und das Azure-Portal erwarten nicht, dass der `counterSpecifier`-Wert irgendeinem Muster entspricht. Seien Sie beim Erstellen von `counterSpecifier`-Werten konsistent.

Bei Angabe von `performanceCounters` schreibt LAD Daten immer in eine Tabelle in Azure Storage. Die gleichen Daten können in JSON-Blobs geschrieben oder Event Hubs oder beides geschrieben werden. Sie können das Speichern von Daten in einer Tabelle jedoch nicht deaktivieren. 

Alle LAD-Instanzen, die denselben Speicherkontonamen und Endpunkts verwenden, fügen ihre Metriken und Protokolle derselben Tabelle hinzu. Wenn zu viele virtuelle Computer in dieselbe Tabellenpartition schreiben, kann Azure Schreibvorgänge in dieser Partition drosseln. 

Die `eventVolume`-Einstellung bewirkt, dass Einträge auf 1 (Small), 10 (Medium) oder 100 (Large) Partitionen aufgeteilt werden. In der Regel reichen mittlere Partitionen aus, um eine Drosselung des Datenverkehrs zu vermeiden. 

Das Azure Monitor-Metrikenfeature im Azure-Portal verwendet die Daten in dieser Tabelle, um Graphen zu erstellen oder Warnungen auszulösen. Der Tabellenname ist eine Verkettung dieser Zeichenfolgen:

* `WADMetrics`
* Der `"scheduledTransferPeriod"` für die aggregierten Werte, die in der Tabelle gespeichert sind
* `P10DV2S`
* Ein Datum im Format „JJJJMMTT“, das sich alle 10 Tage ändert

Beispiele sind `WADMetricsPT1HP10DV2S20170410` und `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Mit dem optionalen `syslogEvents`-Abschnitt wird die Erfassung von Protokollereignissen von Syslog gesteuert. Wenn dieser Abschnitt ausgelassen wird, werden Syslog-Ereignisse überhaupt nicht erfasst.

Die `syslogEventConfiguration`-Sammlung enthält einen Eintrag für jede gewünschte Syslog-Funktion. Wenn `minSeverity` für eine bestimmte Funktion `"NONE"` lautet oder wenn diese Funktion im Element nicht enthalten ist, werden keine Ereignisse von dieser Funktion erfasst.

Element | Wert
------- | -----
sinks | Eine durch Trennzeichen getrennte Liste der Namen von Senken, an die einzelne Protokollereignisse veröffentlicht werden. Alle Protokollereignisse, die den Einschränkungen in `syslogEventConfiguration` entsprechen, werden an alle aufgeführten Senken veröffentlicht. Beispiel: `"EHforsyslog"`
facilityName | Ein Syslog-Facility-Name, z. B. `"LOG_USER"` oder `"LOG\LOCAL0"`. Weitere Informationen finden Sie im Abschnitt „Facility“ der [Syslog-Manpage](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Ein Syslog-Schweregrad, z. B. `"LOG_ERR"` oder `"LOG_INFO"`. Weitere Informationen finden Sie im Abschnitt „Schweregrad“ der [Syslog-Manpage](http://man7.org/linux/man-pages/man3/syslog.3.html). Die Erweiterung erfasst Ereignisse ab dem angegebenen Grad, die an die Funktion gesendet werden.

Bei Angabe von `syslogEvents` schreibt LAD Daten immer in eine Tabelle in Azure Storage. Die gleichen Daten können in JSON-Blobs geschrieben oder Event Hubs oder beides geschrieben werden. Sie können das Speichern von Daten in einer Tabelle jedoch nicht deaktivieren. 

Die Partitionierung für die Tabelle entspricht der Beschreibung für `performanceCounters`. Der Tabellenname ist eine Verkettung dieser Zeichenfolgen:

* `LinuxSyslog`
* Ein Datum im Format „JJJJMMTT“, das sich alle 10 Tage ändert

Beispiele sind `LinuxSyslog20170410` und `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Der `perfCfg`-Abschnitt ist optional. Sie steuert die Ausführung beliebiger [OMI (Open Management Infrastructure)](https://github.com/Microsoft/omi)-Abfragen.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Wert
------- | -----
Namespace | (Optional) Der OMI-Namespace, in dem die Abfrage ausgeführt werden soll. Wenn nichts angegeben wird, beträgt der Standardwert `"root/scx"`. Er wird von [System Center plattformübergreifenden Anbietern](https://github.com/Microsoft/SCXcore) implementiert.
Abfrage | Die auszuführende OMI-Abfrage.
table | (Optional) Die Azure Storage-Tabelle im angegebenen Speicherkonto. Weitere Informationen finden Sie unter [Geschützte Einstellungen](#protected-settings).
frequency | (Optional) Die Anzahl von Sekunden zwischen Abfrageausführungen. Der Standardwert ist 300 (5 Minuten). Der Wert muss mindestens 15 Sekunden betragen.
sinks | (Optional) Eine durch Trennzeichen getrennte Liste der Namen von weiteren Senken, an die unformatierte Metrikergebnisse veröffentlicht werden sollen. Es erfolgt keine Aggregation dieser unformatierten Daten durch die Erweiterung oder den Azure Monitor-Metrikendienst.

Es müssen entweder `"table"` oder `"sinks"` oder beide angegeben werden.

### <a name="filelogs"></a>fileLogs

Der `fileLogs`-Abschnitt steuert die Erfassung von Protokolldateien. LAD erfasst neue Textzeilen, wenn sie in die Datei geschrieben werden. Sie werden in Tabellenzeilen und/oder angegebene Senken (`JsonBlob` oder `EventHub`) geschrieben.

> [!NOTE]
> `fileLogs` werden durch eine Unterkomponente von LAD namens `omsagent` erfasst. Stellen Sie zum Erfassen `fileLogs` von sicher, dass der `omsagent`-Benutzer über Leseberechtigungen für die von Ihnen angegebenen Dateien verfügt. Der Benutzer muss außerdem über Ausführungsberechtigungen für alle Verzeichnisse im Pfad zu dieser Datei verfügen. Nach der Installation von LAD können Sie die Berechtigungen überprüfen, indem Sie `sudo su omsagent -c 'cat /path/to/file'` ausführen.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Wert
------- | -----
file | Der vollständige Pfadname der Protokolldatei, die überwacht und erfasst werden soll. Der Pfadname darf nur eine einzelne Datei benennen. Er darf kein Verzeichnis benennen oder Platzhalterzeichen enthalten. Das `omsagent`-Benutzerkonto muss über Lesezugriff auf den Dateipfad verfügen.
table | (Optional) Die Azure Storage-Tabelle, in die neue Zeilen aus dem „Anhang“ der Datei geschrieben werden. Die Tabelle muss im angegebenen Speicherkonto wie in der geschützten Konfiguration angegeben werden. 
sinks | (Optional) Eine durch Trennzeichen getrennte Liste der Namen weiterer Senken, an die Protokollzeilen gesendet werden.

Es müssen entweder `"table"` oder `"sinks"` oder beide angegeben werden.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriken, die vom integrierten Anbieter unterstützt werden

Der `builtin`-Metrikanbieter ist eine Quelle von Metriken, die für eine Vielzahl von Benutzern von Interesse sind. Diese Metriken werden in fünf Klassen unterteilt:

* Prozessor
* Arbeitsspeicher
* Netzwerk
* Dateisystem
* Datenträger

### <a name="builtin-metrics-for-the-processor-class"></a>Integrierte Metriken der Prozessorklasse

Die Prozessorklasse von Metriken bietet Informationen zur Prozessorauslastung auf dem virtuellen Computer. Beim Aggregieren von Prozentsätzen ist das Ergebnis der Durchschnitt für alle CPUs. 

Wenn eine vCPU auf einem virtuellen Computer mit zwei vCPU zu 100 Prozent ausgelastet ist und die andere zu 100 Prozent im Leerlauf ist, beträgt die gemeldete `PercentIdleTime` 50. Wenn jede vCPU im gleichen Zeitraum zu 50 % ausgelastet ist, lautet das gemeldete Ergebnis ebenfalls 50. Wenn auf einem virtuellen Computer mit vier vCPUs eine vCPU zu 100 Prozent ausgelastet ist und die anderen sich im Leerlauf befinden, wird `PercentIdleTime` gleich 75 gemeldet.

Leistungsindikator | Bedeutung
------- | -------
PercentIdleTime | Prozentsatz der Zeit während des Aggregationszeitraums, in der die Prozessoren die Kernel-Leerlaufschleife ausgeführt haben
PercentProcessorTime | Prozentsatz der Zeit für die Ausführung von anderen Threads als dem Leerlaufthread
PercentIOWaitTime | Prozentsatz der Zeit für das Warten auf den Abschluss von E/A-Vorgängen
PercentInterruptTime | Prozentsatz der Zeit für die Ausführung von Hardware- und/oder Softwareinterrupts und DPCs (zurückgestellte Prozeduraufrufe)
PercentUserTime | Prozentsatz der Zeit, die während des Aggregationszeitraums nicht im Leerlauf verbracht wurde, sondern durch Benutzer stärker bei normaler Priorität
PercentNiceTime | Prozentsatz der Zeit, die nicht im Leerlauf aufgewendet wurde, mit geringerer Priorität (Nice)
PercentPrivilegedTime | Prozentsatz der Zeit, die nicht im Leerlauf aufgewendet wurde, im privilegierten (Kernel-)Modus

Die ersten vier Leistungsindikatoren sollten in der Summe 100 Prozent ergeben. Die letzten drei Leistungsindikatoren sollten sich ebenfalls auf 100 Prozent summieren. Diese drei Leistungsindikatoren unterteilen die Summe von `PercentProcessorTime`, `PercentIOWaitTime` und `PercentInterruptTime`.

Um eine einzelne Metrik über alle Prozessoren hinweg zu aggregieren, legen Sie `"condition": "IsAggregate=TRUE"` fest. Um eine Metrik für einen bestimmten Prozessor zu erhalten, z.B. den zweiten logischen Prozessor eines virtuellen Computers mit vier vCPUs, legen Sie `"condition": "Name=\\"1\\""` fest. Die Nummern der logischen Prozessoren liegen im Bereich `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>Integrierte Metriken der Arbeitsspeicherklasse

Die Arbeitsspeicherklasse der Metriken bietet Informationen zur Speicherauslastung, zum Paging und zum Swapping.

Leistungsindikator | Bedeutung
------- | -------
AvailableMemory | Verfügbarer physischer Speicher in MB
PercentAvailableMemory | Verfügbarer physischer Speicher als Prozentsatz des gesamten Arbeitsspeichers
UsedMemory | Verwendeter physischer Speicher (MB)
PercentUsedMemory | Verwendeter physischer Speicher als Prozentsatz des gesamten Arbeitsspeichers
PagesPerSec | Paging insgesamt (Lesen/Schreiben)
PagesReadPerSec | Aus dem Hintergrundspeicher gelesene Seiten, z. B. Auslagerungsdatei, Programmdatei und im Speicher abgebildete Datei
PagesWrittenPerSec | Seiten, die in den Hintergrundspeicher geschrieben werden, z. B. Auslagerungsdatei und im Speicher abgebildete Datei
AvailableSwap | Nicht verwendeter Auslagerungsbereich (MB)
PercentAvailableSwap | Nicht verwendeter Auslagerungsbereich als Prozentsatz des gesamten Auslagerungsbereichs
UsedSwap | Verwendeter Auslagerungsbereich (MB)
PercentUsedSwap | Verwendeter Auslagerungsbereich als Prozentsatz des gesamten Auslagerungsbereichs

Diese Klasse von Metriken hat nur eine Instanz. Das `"condition"`-Attribut weist keine nützlichen Einstellungen auf und sollte ausgelassen werden.

### <a name="builtin-metrics-for-the-network-class"></a>Integrierte Metriken der Netzwerkklasse

Die Netzwerkklasse von Metriken liefert Informationen zur Netzwerkaktivität für eine einzelne Netzwerkschnittstelle seit dem Startup. 

LAD macht keine Bandbreitenmetriken verfügbar. Sie können diese Metriken aus Hostmetriken abrufen.

Leistungsindikator | Bedeutung
------- | -------
BytesTransmitted | Gesamtanzahl der seit dem Startup gesendeten Bytes
BytesReceived | Gesamtanzahl der seit dem Startup empfangenen Bytes
BytesTotal | Gesamtanzahl der seit dem Startup gesendeten und empfangenen Bytes
PacketsTransmitted | Gesamtanzahl der seit dem Startup gesendeten Pakete
PacketsReceived | Gesamtanzahl der seit dem Startup empfangenen Pakete
TotalRxErrors | Anzahl der Empfangsfehler seit dem Startup
TotalTxErrors | Anzahl der Übertragungsfehler seit dem Startup
TotalCollisions | Anzahl der Konflikte, die seit dem Startup von Netzwerkports gemeldet wurden

Obwohl diese Netzwerkklasse instanziiert wird, unterstützt LAD nicht das Erfassen von aggregierten Netzwerkmetriken für alle Netzwerkgeräte. Legen Sie `"condition": "InstanceID=\\"eth0\\""` fest, um Metriken für eine bestimmte Schnittstelle abzurufen, z.B. eth0.

### <a name="builtin-metrics-for-the-file-system-class"></a>Integrierte Metriken der Dateisystemklasse

Die Dateisystemklasse von Metriken liefert Informationen zur Nutzung des Dateisystems. Absolute und prozentuale Werte werden so erfasst, wie sie einem normalen Benutzer (nicht root) angezeigt werden würden.

Leistungsindikator | Bedeutung
------- | -------
FreeSpace | Verfügbarer Speicherplatz in Byte
UsedSpace | Verwendeter Speicherplatz in Byte
PercentFreeSpace | Prozentsatz des freien Speicherplatzes
PercentUsedSpace | Prozentsatz des belegten Speicherplatzes
PercentFreeInodes | Prozentwert der nicht verwendeten Indexknoten (I-Knoten)
PercentUsedInodes | Prozentwert der zugeordneten (verwendeten) I-Knoten als Summe aus sämtlichen Dateisystemen
BytesReadPerSecond | Pro Sekunde gelesene Bytes
BytesWrittenPerSecond | Pro Sekunde geschriebene Bytes
Bytes pro Sekunde | Pro Sekunde gelesene und geschriebene Bytes
ReadsPerSecond | Lesevorgänge pro Sekunde
WritesPerSecond | Schreibvorgänge pro Sekunde
TransfersPerSecond | Lese- oder Schreibvorgänge pro Sekunde

Sie können aggregierte Werte für alle Dateisysteme erhalten, indem Sie `"condition": "IsAggregate=True"` festlegen. Werte für ein bestimmtes eingebundenes Dateisystem, wie z. B. `"/mnt"`, erhalten Sie, indem Sie `"condition": 'Name="/mnt"'` angeben. 

> [!NOTE]
> Wenn Sie nicht mit JSON, sondern mit dem Azure-Portal arbeiten, ist die Form des Bedienungsfeldes `Name='/mnt'`.

### <a name="builtin-metrics-for-the-disk-class"></a>Integrierte Metriken der Datenträgerklasse

Die Datenträgerklasse von Metriken liefert Informationen zur Nutzung von Datenträgergeräten. Diese Statistiken gelten für das gesamte Laufwerk. 

Wenn ein Gerät mehrere Dateisysteme enthält, sind die Leistungsindikatoren für das Gerät effektiv über alle Dateisysteme aggregiert.

Leistungsindikator | Bedeutung
------- | -------
ReadsPerSecond | Lesevorgänge pro Sekunde
WritesPerSecond | Schreibvorgänge pro Sekunde
TransfersPerSecond | Vorgänge gesamt pro Sekunde
AverageReadTime | Durchschnittliche Anzahl von Sekunden pro Lesevorgang
AverageWriteTime | Durchschnittliche Anzahl von Sekunden pro Schreibvorgang
AverageTransferTime | Durchschnittliche Anzahl von Sekunden pro Vorgang
AverageDiskQueueLength | Durchschnittliche Anzahl von Datenträgervorgängen in der Warteschlange
ReadBytesPerSecond | Anzahl von pro Sekunde gelesenen Bytes
WriteBytesPerSecond | Anzahl von pro Sekunde geschriebenen Bytes
Bytes pro Sekunde | Anzahl von pro Sekunde gelesenen und geschriebenen Bytes

Sie können aggregierte Werte für alle Datenträger erhalten, indem Sie `"condition": "IsAggregate=True"` festlegen. Legen Sie zum Abrufen von Informationen für ein bestimmtes Gerät (z. B. `/dev/sdf1`) `"condition": "Name=\\"/dev/sdf1\\""` fest.

## <a name="install-and-configure-lad-30"></a>Installieren und Konfigurieren von LAD 3.0

### <a name="azure-cli"></a>Azure CLI

Wenn Ihre geschützten Einstellungen in der Datei *ProtectedSettings.json* und Ihre öffentlichen Konfigurationsinformationen in *PublicSettings.json* gespeichert sind, führen Sie den folgenden Befehl aus.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Bei diesem Befehl wird davon ausgegangen, dass Sie den Azure Resource Manager-Modus der Azure CLI verwenden. Wenn Sie LAD für virtuelle Computer im klassischen Bereitstellungsmodell konfigurieren möchten, wechseln Sie in den ASM-Modus (`azure config mode asm`) und lassen den Namen der Ressourcengruppe im Befehl aus. 

Weitere Informationen finden Sie in der [Dokumentation zur plattformübergreifenden Azure-Befehlszeilenschnittstelle](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Wenn sich Ihre geschützten Einstellungen in der Variablen `$protectedSettings` und Ihre öffentlichen Konfigurationsinformationen in der Variablen `$publicSettings` befinden, führen Sie den folgenden Befehl aus:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Beispielkonfiguration für LAD 3.0

Die folgende Beispielkonfiguration für LAD 3.0 mit einigen Erläuterungen in diesem Abschnitt basiert auf den oben angegebenen Definitionen. Verwenden Sie Ihren eigenen Speicherkontonamen, SAS-Kontotoken und Event Hubs-SAS-Token, um dieses Beispiel in Ihrem Szenario anzuwenden.

> [!NOTE]
> Die Methode zum Bereitstellen von öffentlichen und geschützten Einstellungen unterscheidet sich abhängig davon, ob Sie LAD über die Azure-Befehlszeilenschnittstelle oder mithilfe von PowerShell installieren: 
>
> * Speichern Sie bei Verwendung der Azure-Befehlszeilenschnittstelle die folgenden Einstellungen in *ProtectedSettings.json* und *PublicSettings.json* für die Verwendung mit dem genannten Beispielbefehl. 
> * Wenn Sie PowerShell verwenden, führen Sie `$protectedSettings = '{ ... }'` aus, um die folgenden Einstellungen in `$protectedSettings` und `$publicSettings` zu speichern.

### <a name="protected-settings"></a>Geschützte Einstellungen

Durch die geschützten Einstellungen wird Folgendes konfiguriert:

* Ein Speicherkonto.
* Ein zugehöriges SAS-Kontotoken.
* Mehrere Senken ( `JsonBlob` oder `EventHub` mit SAS-Token).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Öffentliche Einstellungen

Die öffentlichen Einstellungen weisen LAD zu Folgendem an:

* Hochladen von Metriken zur prozentualen Prozessorzeit und zum verwendeten Speicherplatz in die Tabelle `WADMetrics*`.
* Hochladen von Benachrichtigungen der Syslog-Funktion `"user"` mit dem Schweregrad `"info"` in die Tabelle `LinuxSyslog*`.
* Hochladen von rohen OMI-Abfrageergebnissen (`PercentProcessorTime` und `PercentIdleTime`) in die genannte Tabelle `LinuxCPU`.
* Hochladen von angefügten Zeilen in der Datei `/var/log/myladtestlog` in die Tabelle `MyLadTestLog`.

In jedem Fall werden außerdem Daten hochgeladen nach:

* „Azure Blob Storage“. Der Containername entspricht der Definition in der `JsonBlob`-Senke.
* Der Event Hubs-Endpunkt, wie in der `EventHub`-Senke angegeben.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Die `resourceId` in der Konfiguration muss mit der des virtuellen Computers oder der VM-Skalierungsgruppe übereinstimmen.

* Die Funktion für Diagramme und Warnungen zu Azure-Plattformmetriken kennt die `resourceId` des virtuellen Computers, mit dem Sie arbeiten. Sie geht davon aus, die Daten zu Ihrem virtuellen Computer mithilfe der `resourceId` als Lookup-Schlüssel zu finden.
* Bei Verwendung der automatischen Skalierung von Azure muss die `resourceId` in der Konfiguration für die automatische Skalierung mit der von LAD verwendeten `resourceId` übereinstimmen.
* Die `resourceId` ist in die Namen von JSON-Blobs integriert, die von LAD geschrieben wurden.

## <a name="view-your-data"></a>Anzeigen Ihrer Daten

Verwenden Sie das Azure-Portal, um die Leistungsdaten anzuzeigen oder Warnungen festzulegen:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Screenshot des Azure-Portals. Der verwendete Speicherplatz auf dem Datenträger ist als Metrik ausgewählt. Das resultierende Diagramm wird angezeigt.":::

Die `performanceCounters`-Daten werden immer in einer Azure Storage-Tabelle gespeichert. Azure Storage-APIs sind für viele Sprachen und Plattformen verfügbar.

Die an `JsonBlob`-Senken gesendeten Daten werden in Blobs in dem Speicherkonto gespeichert, das in den [geschützten Einstellungen](#protected-settings) angegeben wurde. Sie können die Blobdaten mithilfe von Azure Blob Storage-APIs nutzen.

Sie können auch folgende Tools mit grafischer Benutzeroberfläche für den Zugriff auf Daten in Azure Storage verwenden:

* Server-Explorer von Visual Studio
* [Azure Storage-Explorer](https://azurestorageexplorer.codeplex.com/)

Der folgende Screenshot einer Sitzung im Microsoft Azure Storage-Explorer zeigt die generierten Azure Storage-Tabellen und -Container einer ordnungsgemäß konfigurierten LAD 3.0-Erweiterung auf einem virtuellen Testcomputer. Das Bild stimmt nicht genau mit der [LAD 3.0-Beispielkonfiguration](#example-lad-30-configuration) überein.

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Screenshot des Azure Storage-Explorer.":::


Weitere Informationen zum Nutzen von Nachrichten, die an einem Event Hubs-Endpunkt veröffentlicht werden, finden Sie in der entsprechenden [Event Hubs-Dokumentation](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Nächste Schritte

* Erstellen Sie in [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md) Warnungen für die von Ihnen gesammtelten Metriken.
* Erstellen Sie [Überwachungsdiagramme](../../azure-monitor/data-platform.md) für Ihre Metriken.
* Erfahren Sie, wie Sie mithilfe Ihrer Metriken [eine VM-Skalierungsgruppe erstellen](../linux/tutorial-create-vmss.md), um die automatische Skalierung zu steuern.
