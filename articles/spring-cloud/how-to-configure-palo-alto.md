---
title: Konfigurieren von Palo Alto für Azure Spring Cloud
description: Konfigurieren von Palo Alto für Azure Spring Cloud
author: karlerickson
ms.author: vaangadi
ms.topic: how-to
ms.service: spring-cloud
ms.date: 09/17/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: f3b0aec801598faea2fd741177d34ecdc127a9a8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083382"
---
# <a name="how-to-configure-palo-alto-for-azure-spring-cloud"></a>Konfigurieren von Palo Alto für Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In diesem Artikel wird beschrieben, wie Sie Azure Spring Cloud mit einer Palo Alto-Firewall verwenden.

Die [Azure Spring Cloud-Referenzarchitektur](./reference-architecture.md) enthält z. B. eine Azure Firewall-Instanz zum Schutz Ihrer Anwendungen. Wenn Ihre aktuellen Bereitstellungen jedoch eine Palo Alto-Firewall enthalten, können Sie die Azure Firewall aus der Azure Spring Cloud-Bereitstellung entfernen und stattdessen Palo Alto wie in diesem Artikel beschrieben verwenden.

Konfigurationsinformationen wie Regeln und Adressplatzhalter sollten Sie in CSV-Dateien in einem Git-Repository aufbewahren. In diesem Artikel erfahren Sie, wie Sie diese Dateien automatisiert auf Palo Alto anwenden. Informationen zur Konfiguration, die auf Palo Alto angewandt werden soll, finden Sie unter [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNet](./vnet-customer-responsibilities.md). 

> [!Note]
> Zur Beschreibung der Verwendung von REST-APIs werden in diesem Artikel Namen und Werte in der Syntax für PowerShell-Variablen angegeben. Für die Werte sind Sie selbst verantwortlich. Achten Sie darauf, bei allen Schritten dieselben Werte zu verwenden.
>
> Nachdem Sie das TLS/SSL-Zertifikat in Palo Alto konfiguriert haben, entfernen Sie das Argument `-SkipCertificateCheck` aus allen Palo Alto-REST-API-Aufrufen in den folgenden Beispielen.
>
> Sie sollten diesen Artikel nicht als Referenz für die Palo Alto-REST-APIs verwenden. Sämtliche Beispiele dienen ausschließlich zu Demonstrationszwecken. Verbindliche API-Details finden Sie in der Palo Alto-Dokumentation unter [PAN-OS-REST-API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/pan-os-rest-api.html).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* Eine Palo Alto-Bereitstellung. Falls Sie nicht über eine Bereitstellung verfügen, können Sie [Palo Alto über den Azure Marketplace bereitstellen](https://ms.portal.azure.com/#create/paloaltonetworks.vmseries-ngfwbundle2).
* [PowerShell](/powershell/scripting/install/installing-powershell)
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

## <a name="configure-palo-alto"></a>Konfigurieren von Palo Alto

Konfigurieren Sie zunächst die VM-Series-Firewall von Palo Alto. Ausführliche Anweisungen finden Sie unter [Deploy the VM-Series Firewall from the Azure Marketplace (Solution Template)](https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewall-on-azure-solution-template.html) (Bereitstellen der VM-Series-Firewall über den Azure Marketplace (Lösungsvorlage)). Mit diesen Anweisungen können Sie eine VM-Series-Firewall bereitstellen und sowohl die Subnetze `Trust` und `UnTrust` als auch die zugehörigen Netzwerkschnittstellenkarten konfigurieren. Aus Konsistenzgründen sollten Sie diese Firewall im Adressraum des virtuellen `Hub`-Netzwerks in der Referenzarchitektur erstellen.

Im [Leitfaden zur Referenzarchitektur für Azure](https://www.paloaltonetworks.com/resources/guides/azure-architecture-guide) werden verschiedene technische Entwurfsmodelle für die Bereitstellung der Firewall in Azure beschrieben.

Im weiteren Verlauf dieses Artikels wird davon ausgegangen, dass Sie über die folgenden beiden vorkonfigurierten Netzwerkzonen verfügen:

* `Trust` enthält die Schnittstelle, die mit einem virtuellen Netzwerk verbunden ist, das mittels Peering mit dem virtuellen Azure Spring Cloud-Netzwerk verknüpft ist.
* `UnTrust` enthält die Schnittstelle zum öffentlichen Internet, die zuvor im Bereitstellungsleitfaden für die VM-Series erstellt wurde.

## <a name="prepare-csv-files"></a>Vorbereiten der CSV-Dateien

Erstellen Sie als Nächstes drei CSV-Dateien.

Nennen Sie die erste Datei *AzureSpringCloudServices.csv*. Diese Datei soll die Eingangsports für Azure Spring Cloud enthalten. Die Werte im folgenden Beispiel dienen nur zu Demonstrationszwecken. Alle erforderlichen Werte finden Sie im Abschnitt [Azure Spring Cloud-Netzwerkanforderungen](./vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements) unter [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNet](./vnet-customer-responsibilities.md).

```CSV
name,protocol,port,tag
ASC_1194,udp,1194,AzureSpringCloud
ASC_443,tcp,443,AzureSpringCloud
ASC_9000,tcp,9000,AzureSpringCloud
ASC_445,tcp,445,AzureSpringCloud
ASC_123,udp,123,AzureSpringCloud
```

Nennen Sie die zweite Datei *AzureSpringCloudUrlCategories.csv*. Diese Datei ist für die Ausgangsadressen (mit Platzhaltern) vorgesehen, die für Azure Spring Cloud verfügbar sein sollen. Die Werte im folgenden Beispiel dienen nur zu Demonstrationszwecken. Die aktuellen Werte finden Sie unter [FQDN-Anforderungen/Anwendungsregeln für Azure Spring Cloud](./vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirementsapplication-rules).

```CSV
name,description
*.azmk8s.io,
mcr.microsoft.com,
*.cdn.mscr.io,
*.data.mcr.microsoft.com,
management.azure.com,
*.microsoftonline.com,
*.microsoft.com,
packages.microsoft.com,
acs-mirror.azureedge.net,
mscrl.microsoft.com,
crl.microsoft.com,
crl3.digicert.com
```

Nennen Sie die dritte Datei *AzureMonitorAddresses.csv*. Diese Datei soll alle Adressen und IP-Adressbereiche enthalten, die für Metriken und die Überwachung über Azure Monitor zur Verfügung gestellt werden sollen, sofern Sie Azure Monitor verwenden. Die Werte im folgenden Beispiel dienen nur zu Demonstrationszwecken. Aktuelle Werte finden Sie unter [Von Azure Monitor verwendete IP-Adressen](../azure-monitor/app/ip-addresses.md).

```CSV
name,type,address,tag
40.114.241.141,ip-netmask,40.114.241.141/32,AzureMonitor
104.45.136.42,ip-netmask,104.45.136.42/32,AzureMonitor
40.84.189.107,ip-netmask,40.84.189.107/32,AzureMonitor
168.63.242.221,ip-netmask,168.63.242.221/32,AzureMonitor
52.167.221.184,ip-netmask,52.167.221.184/32,AzureMonitor
live.applicationinsights.azure.com,fqdn,live.applicationinsights.azure.com,AzureMonitor
rt.applicationinsights.microsoft.com,fqdn,rt.applicationinsights.microsoft.com,AzureMonitor
rt.services.visualstudio.com,fqdn,rt.services.visualstudio.com,AzureMonitor

```

## <a name="authenticate-into-palo-alto"></a>Authentifizieren bei Palo Alto

Als Nächstes müssen Sie sich bei Palo Alto authentifizieren und einen API-Schlüssel abrufen. Weitere Informationen finden Sie unter [Get Your API Key](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-xml-api/get-your-api-key.html) (Abrufen Ihres API-Schlüssels) in der Palo Alto-Dokumentation.

Im folgenden Beispiel wird PowerShell verwendet, um Anforderungsheader zu authentifizieren und zu generieren, die später in diesem Artikel verwendet werden:

```powershell
$username=<username for PaloAlto>
$password=<password for PaloAlto>
$authResponse = irm "https://${PaloAltoIpAddress}/api/?type=keygen&user=${username}&password=${password}" -SkipCertificateCheck
$paloAltoHeaders = @{'X-PAN-KEY' = $authResponse.response.result.key; 'Content-Type' = 'application/json' }
```

## <a name="delete-existing-service-group"></a>Löschen einer vorhandenen Dienstgruppe

Wenn Sie bereits Konfigurationsversuche unternommen haben, sollten Sie diese Konfigurationen zurücksetzen und alle Sicherheitsregeln und Dienstgruppen löschen.

Sie löschen die Sicherheitsregel mithilfe der [REST-API für Sicherheitsregeln](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/create-security-policy-rule-rest-api.html), wie im folgenden Beispiel gezeigt:

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=${paloAltoSecurityPolicyName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

Löschen Sie die Dienstgruppe, wie im folgenden Beispiel gezeigt:

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${paloAltoServiceGroupName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

Löschen Sie jeden Palo Alto-Dienst (gemäß Definition in *AzureSpringCloudServices.csv*), wie im folgenden Beispiel gezeigt:

```powershell
Get-Content .\AzureSpringCloudServices.csv | ConvertFrom-Csv | select name | ForEach-Object {
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${_}"
    Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
}
```

## <a name="create-a-service-and-service-group"></a>Erstellen eines Diensts und einer Dienstgruppe

Verwenden Sie das folgende Beispiel, um die Erstellung von Diensten basierend auf der zuvor erstellten Datei *AzureSpringCloudServices.csv* zu automatisieren.

```powershell
# Define a function to create and submit a Palo Alto service creation request
function New-PaloAltoService {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject]
        $ServiceObject
    )
    PROCESS {
        $requestBody = @{
            'entry' = @{
                '@name'    = $ServiceObject.name
                'protocol' = @{
                    $ServiceObject.protocol = @{
                        'port'     = $ServiceObject.port
                        'override' = @{
                            'no' = @{}
                        }

                    }
                }
                'tag'      = @{
                    'member' = @($ServiceObject.tag)
                }
            }
        }

        # Some rules in the CSV may need to conain source ports or descriptions. If these are present, populate them in the request
        if ($ServiceObject.description) {
            $requestBody.entry.description = $ServiceObject.description
        }
        if ($ServiceObject.'source-port') {
            $requestBody.entry.protocol."$($ServiceObject.protocol)".'source-port' = $ServiceObject.'source-port'
        }

        # Send the request
        $name = $requestBody.entry.'@name'
        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${name}"
         Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 9) -Verbose
    }
}

# Now invoke that function for every row in AzureSpringCloudServices.csv
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoService
```

Erstellen Sie als Nächstes eine Dienstgruppe für diese Dienste, wie im folgenden Beispiel gezeigt:

```powershell
# Create a function to consume service definitions and submit a service group creation request
function New-PaloAltoServiceGroup {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject[]]
        $RuleData,

        [Parameter(Mandatory = $true)]
        [string]
        $ServiceGroupName
    )
    begin {
        [array] $names = @()
    }

    process {
        $names += $RuleData.name
    }

    end {
        $requestBody = @{ 'entry' = [ordered] @{
                '@name'   = $ServiceGroupName
                'members' = @{ 'member' = $names }
                'tag'     = @{ 'member' = 'AzureSpringCloud' }
            }
        }

        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${ServiceGroupName}"

        Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json $requestBody) -Verbose
    }
}

# Run that function for all services in AzureSpringCloudServices.csv.
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoServiceGroup -ServiceGroupName 'AzureSpringCloud_SG'
```

## <a name="create-custom-url-categories"></a>Erstellen benutzerdefinierter URL-Kategorien

Definieren Sie als Nächstes benutzerdefinierte URL-Kategorien für die Dienstgruppe, um ausgehenden Datenverkehr von Azure Spring Cloud zu ermöglichen. Gehen Sie dabei wie im folgenden Beispiel gezeigt vor.

```powershell
# Read Service entries from CSV to enter into Palo Alto
$csvImport = Get-Content ${PSScriptRoot}/AzureSpringCloudUrls.csv | ConvertFrom-Csv

# Convert name column of CSV to add to the Custom URL Group in Palo Alto
$requestBody = @{ 'entry' = [ordered] @{
        '@name' = 'AzureSpringCloud_SG'
        'list'  = @{ 'member' = $csvImport.name }
        'type'  = 'URL List'
    }
} | ConvertTo-Json -Depth 9

$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/CustomURLCategories?location=vsys&vsys=vsys1&name=AzureSpringCloud_SG"

try {
    $existingObject = Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
}
catch {
}

Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body $requestBody -Verbose
```

## <a name="create-a-security-rule"></a>Erstellen einer Sicherheitsregel

Erstellen Sie nun eine JSON-Datei für eine Sicherheitsregel. Nennen Sie die Datei *SecurityRule.json*, und fügen Sie folgenden Inhalt ein. Die Namen der beiden Zonen `Trust` und `UnTrust` stimmen mit den Zonennamen überein, die weiter oben im Abschnitt [Konfigurieren von Palo Alto](#configure-palo-alto) beschrieben wurden. Der Eintrag `service/member` enthält den Namen der in den vorherigen Schritten erstellten Dienstgruppe.

```json
{
    "entry": [
        {
            "@name": "azureSpringCloudRule",
            "@location": "vsys",
            "@vsys": "vsys1",
            "to": {
                "member": [
                    "UnTrust"
                ]
            },
            "from": {
                "member": [
                    "Trust"
                ]
            },
            "source-user": {
                "member": [
                    "any"
                ]
            },
            "application": {
                "member": [
                    "any"
                ]
            },
            "service": {
                "member": [
                    "AzureSpringCloud_SG"
                ]
            },
            "hip-profiles": {
                "member": [
                    "any"
                ]
            },
            "action": "allow",
            "category": {
                "member": [
                    "any"
                ]
            },
            "source": {
                "member": [
                    "any"
                ]
            },
            "destination": {
                "member": [
                    "any"
                ]
            }
        }
    ]
}
```

Wenden Sie diese Regel nun auf Palo Alto an, wie im folgenden Beispiel gezeigt.

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=azureSpringCloudRule"

# Delete the rule if it already exists
try {
    $getResult = Invoke-RestMethod -Headers $paloAltoHeaders -Method Get -SkipCertificateCheck -Uri $url -Verbose
    if ($getResult.'@status' -eq 'success') {
        Invoke-RestMethod -Method Delete  -Headers $paloAltoHeaders -SkipCertificateCheck -Uri $url
    }
}
catch {}

# Create the rule from the JSON file
Invoke-WebRequest -Uri $url -Method Post -Headers $paloAltoHeaders -Body (Get-Content SecurityRule.json) -SkipCertificateCheck
```

## <a name="create-azure-monitor-addresses"></a>Erstellen von Azure Monitor-Adressen

Verwenden Sie als Nächstes die Datei *AzureMonitorAddresses.csv*, um Adressobjekte in Palo Alto zu definieren. Der folgende Beispielcode zeigt, wie dieser Task automatisiert werden kann.

```powershell
Get-Content ./AzureMonitorAddresses.csv | ConvertFrom-Csv | ForEach-Object {
    $requestBody = @{ 'entry' = [ordered]@{
            '@name' = $_.name
            $_.type = $_.address
            'tag'   = @{ 'member' = @($_.tag) }
        }
    }

    $name = $requestBody.entry.'@name'
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Addresses?location=vsys&vsys=vsys1&name=${name}"

    # Delete the address if it already exists
    try {
        Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    }
    catch {
    }

    # Create the address
    Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 3) -Verbose
}
```

## <a name="commit-changes-to-palo-alto"></a>Committen von Änderungen in Palo Alto

Sie müssen einige der oben genannten Änderungen committen, damit sie angewandt werden. Dafür können Sie den folgenden REST-API-Aufruf verwenden.

```powershell
$url = "https://${PaloAltoIpAddress}/api/?type=commit&cmd=<commit></commit>"
Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
```

## <a name="configure-the-security-rules-for-azure-spring-cloud-subnets"></a>Konfigurieren der Sicherheitsregeln für Azure Spring Cloud-Subnetze

Fügen Sie als Nächstes Netzwerksicherheitsregeln hinzu, um Datenverkehr von Palo Alto den Zugriff auf Azure Spring Cloud zu ermöglichen. In den folgenden Beispielen wird auf die Spoke-Netzwerksicherheitsgruppen (NSGs) `nsg-spokeapp` und `nsg-spokeruntime` verwiesen, die von der Referenzarchitektur erstellt wurden.

Führen Sie die folgenden Azure CLI-Befehle in einem PowerShell-Fenster aus, um die erforderliche Netzwerksicherheitsregel für die einzelnen NSGs zu erstellen. Dabei ist `$PaloAltoAddressPrefix` die CIDR-Adresse (Classless Inter-Domain Routing) der privaten IP-Adressen von Palo Alto.

```azurecli
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeapp' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeruntime' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
```

## <a name="configure-the-next-hop"></a>Konfigurieren des nächsten Hops

Nachdem Sie Palo Alto konfiguriert haben, konfigurieren Sie Azure Spring Cloud so, dass Palo Alto als nächster Hop für ausgehenden Internetzugriff verwendet wird. Sie können für diese Konfiguration die folgenden Azure CLI-Befehle in einem PowerShell-Fenster verwenden. Stellen Sie sicher, dass Sie Werte für die folgenden Variablen angeben:

* `$AppResourceGroupName`: Name der Ressourcengruppe mit Ihrer Azure Spring Cloud-Instanz
* `$AzureSpringCloudServiceSubnetRouteTableName`: Name der Routingtabelle für den Azure Spring Cloud Dienst/das Runtimesubnetz. In der Referenzarchitektur ist dieser Wert auf `rt-spokeruntime` festgelegt.
* `$AzureSpringCloudAppSubnetRouteTableName`: Name der Routingtabelle für das Subnetz der Azure Spring Cloud-App. In der Referenzarchitektur ist dieser Wert auf `rt-spokeapp` festgelegt.

```azurecli
az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudServiceSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose

az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudAppSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose
```

Damit ist die Konfiguration abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

* [Streamen von Azure Spring Cloud-App-Protokollen in Echtzeit](./how-to-log-streaming.md)
* [Application Insights: Java-Prozess-Agent in Azure Spring Cloud](./how-to-application-insights.md)
* [Automatisieren von Anwendungsbereitstellungen für Azure Spring Cloud](./how-to-cicd.md)