---
title: Bereitstellen von OpenShift Container Platform 3.11 in Azure
description: Bereitstellen von OpenShift Container Platform 3.11 in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: fd77fbdc7e8a3a417c886280a52bdf3f27e559f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074401"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Bereitstellen von OpenShift Container Platform 3.11 in Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Zum Bereitstellen von OpenShift Container Platform 3.11 in Azure können Sie verschiedene Methoden verwenden:

- Sie können die erforderlichen Azure-Infrastrukturkomponenten manuell bereitstellen und sich dann an der [Dokumentation von OpenShift Container Platform](https://docs.openshift.com/container-platform) orientieren.
- Sie können auch eine vorhandene [Ressourcen-Manager-Vorlage](https://github.com/Microsoft/openshift-container-platform/) verwenden, die die Bereitstellung des OpenShift Container Platform-Clusters vereinfacht.
- Eine weitere Möglichkeit besteht darin, das Azure Marketplace-Angebot zu nutzen.

Für alle Optionen ist ein Red Hat-Abonnement erforderlich. Während der Bereitstellung wird die Red Hat Enterprise Linux-Instanz beim Red Hat-Abonnement registriert und an die Pool-ID, die die Berechtigungen für OpenShift Container Platform enthält, angefügt.
Stellen Sie sicher, dass Sie über einen gültigen Benutzernamen, ein gültiges Kennwort und eine gültige Pool-ID für Red Hat Subscription Manager (RHSM) verfügen. Sie können einen Aktivierungsschlüssel, eine Organisations-ID und eine Pool-ID verwenden. Sie können diese Informationen überprüfen, indem Sie sich hier anmelden: https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Bereitstellen mithilfe der Resource Manager-Vorlage für OpenShift Container Platform 3.11

### <a name="private-clusters"></a>Private Cluster

Das Bereitstellen privater OpenShift-Cluster setzt mehr voraus, als das Nichtvorhandensein einer öffentlichen IP-Adresse, die dem Master-Lastenausgleich (Webkonsole) oder dem Infra-Lastenausgleich (Router) zugeordnet ist.  Ein privater Cluster verwendet im Allgemeinen einen benutzerdefinierten DNS-Server (nicht das standardmäßige Azure DNS), einen benutzerdefinierten Domänennamen (wie „contoso.com“) und ein oder mehrere vordefinierte virtuelle Netzwerke.  Für private Cluster müssen Sie Ihr virtuelles Netzwerk vorab mit allen entsprechenden Subnetzen und DNS-Servereinstellungen konfigurieren.  Geben Sie anschließend mit **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference** und **existingNodeSubnetReference** das vorhandene Subnetz an, das vom Cluster verwendet werden soll.

Ist „private master“ (**masterClusterType**=private) ausgewählt, muss eine statische private IP-Adresse für **masterPrivateClusterIp** angegeben werden.  Diese IP-Adresse wird dem Front-End des Master-Lastenausgleichs zugewiesen.  Die IP-Adresse muss innerhalb des CIDR für das Master-Subnetz liegen und darf nicht in Verwendung sein.  **masterClusterDnsType** muss auf „custom“ festgelegt werden, und der Master-DNS-Name muss für **masterClusterDns** angegeben werden.  Der DNS-Name muss der statischen privaten IP-Adresse zugeordnet sein; damit wird auf die Konsole auf den Master-Knoten zugegriffen.

Ist „private router“ (**routerClusterType**=private) ausgewählt, muss eine statische private IP-Adresse für **routerPrivateClusterIp** angegeben werden.  Diese IP-Adresse wird dem Front-End des Infra-Lastenausgleichs zugewiesen.  Die IP-Adresse muss innerhalb des CIDR für das Infra-Subnetz liegen und darf nicht in Verwendung sein.  **routingSubDomainType** muss auf „custom“ festgelegt sein, und der Platzhalter-DNS-Name für das Routing muss für **routingSubDomain** angegeben werden.  

Wenn „private master“ und „private router“ ausgewählt sind, muss auch der benutzerdefinierte Domänenname für **domainName** angegeben werden.

Nach erfolgreicher Bereitstellung ist der Bastion-Knoten der einzige Knoten mit einer öffentlichen IP-Adresse, mit der eine SSH-Verbindung hergestellt werden kann.  Selbst wenn die Master-Knoten für den öffentlichen Zugriff konfiguriert sind, sind sie nicht verfügbar für den Zugriff über eine SSH-Verbindung.

Bei der Bereitstellung mithilfe der Resource Manager-Vorlage werden die Eingabeparameter über eine Parameterdatei angegeben. Forken Sie zur weiteren Anpassung der Bereitstellung das GitHub-Repository, und ändern Sie die entsprechenden Elemente.

Im Folgenden finden Sie eine Auswahl allgemeiner Optionen, die Sie anpassen können:

- Größe der geschützten VM (Variable in „azuredeploy.json“)
- Namenskonvention (Variable in „azuredeploy.json“)
- OpenShift-Clusterbesonderheiten, angepasst über „hosts“-Datei („deployOpenShift.sh“)

### <a name="configure-the-parameters-file"></a>Anpassen der Parameterdatei

Für die [Vorlage für OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) stehen mehrere Verzweigungen für verschiedene Versionen von OpenShift Container Platform zur Verfügung.  Die Bereitstellung kann direkt über das Repository erfolgen. Bei Bedarf können Sie aber auch das Repository forken und vor der Bereitstellung Änderungen an den Vorlagen oder Skripts vornehmen.

Verwenden Sie den `appId` Wert des zuvor erstellten Dienstprinzipals für den `aadClientId`-Parameter.

Das folgende Beispiel zeigt die Parameterdatei „azuredeploy.parameters.json“ mit allen erforderlichen Eingaben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Ersetzen Sie die Parameter durch Ihre eigenen Informationen.

Die Parameter können sich je nach Release unterschieden. Überprüfen Sie daher die erforderlichen Parameter für den verwendeten Branch.

### <a name="azuredeployparametersjson-file-explained"></a>Erläuterung zur Datei „azuredeploy.Parameters.json“

| Eigenschaft | BESCHREIBUNG | Gültige Optionen | Standardwert |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL für Artefakte (JSON, Skripts usw.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Azure-Region, in der Ressourcen bereitgestellt werden |  |  |
| `masterVmSize` | Größe der Master-VM. Wählen Sie eine der in der Datei „azuredeploy.json“ aufgelisteten VM-Größen aus. |  | Standard_E2s_v3 |
| `infraVmSize` | Größe der Infra-VM. Wählen Sie eine der in der Datei „azuredeploy.json“ aufgelisteten VM-Größen aus. |  | Standard_D4s_v3 |
| `nodeVmSize` | Größe der App-Knoten-VM. Wählen Sie eine der in der Datei „azuredeploy.json“ aufgelisteten VM-Größen aus. |  | Standard_D4s_v3 |
| `cnsVmSize` | Größe der CNS (Container Native Storage)-Knoten-VM. Wählen Sie eine der in der Datei „azuredeploy.json“ aufgelisteten VM-Größen aus. |  | Standard_E4s_v3 |
| `osImageType` | Das zu verwendende RHEL-Image. defaultgallery: Bei Bedarf; Marketplace: Drittanbieter-Image | defaultgallery <br> Marketplace | defaultgallery |
| `marketplaceOsImage` | Wenn `osImageType` gleich „marketplace“ ist, geben Sie die entsprechenden Werte für „Herausgeber“, „Angebot“, „SKU“ und „Version“ des Marketplace-Angebots ein. Dieser Parameter ist ein Objekttyp. |  |  |
| `storageKind` | Der Typ des zu verwendenden Speichers  | verwaltet<br> nicht verwaltet | verwaltet |
| `openshiftClusterPrefix` | Cluster-Präfix, mit dem Hostnamen für alle Knoten konfiguriert werden.  Zwischen 1 und 20 Zeichen |  | mycluster |
| `minoVersion` | Die Nebenversion von OpenShift Container Platform 3.11, die bereitgestellt werden soll |  | 69 |
| `masterInstanceCount` | Anzahl der bereitzustellenden Master-Knoten | 1, 3, 5 | 3 |
| `infraInstanceCount` | Anzahl der bereitzustellenden Infra-Knoten | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Anzahl der bereitzustellenden Knoten | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Anzahl der bereitzustellenden CNS-Knoten | 3, 4 | 3 |
| `osDiskSize` | Größe des Betriebssystemdatenträgers für die VM (in GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Größe des Datenträgers, der an Knoten für Docker-Volume angefügt werden soll (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Größe des Datenträgers, der an CNS-Knoten für glusterfs angefügt werden soll (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Administratorbenutzername für Anmeldung beim Betriebssystem (VM) und anfänglichen OpenShift-Benutzer |  | ocpadmin |
| `enableMetrics` | Aktivieren von Metriken. Metriken erfordern, dass mehr Ressourcen benötigt werden, daher ist die richtige Größe für die Infra-VM auszuwählen | true <br> false | false |
| `enableLogging` | Aktivieren der Protokollierung. „elasticsearch pod“ benötigt 8 GB RAM, daher ist die richtige Größe für die Infra-VM auszuwählen | true <br> false | false |
| `enableCNS` | Aktivieren des nativen Container-Speichers | true <br> false | false |
| `rhsmUsernameOrOrgId` | Benutzername oder Organisations-ID des Red Hat-Abonnement-Managers |  |  |
| `rhsmPoolId` | Die Red Hat-Abonnement-Manager-Pool-ID, die Ihre OpenShift-Berechtigungen für Computeknoten enthält |  |  |
| `rhsmBrokerPoolId` | Die Red Hat-Abonnement-Manager-Pool-ID, die Ihre OpenShift-Berechtigungen für Master- und Infra-Knoten enthält. Wenn Sie keine unterschiedlichen Pool-IDs haben, geben Sie dieselbe Pool-ID als „rhsmPoolId“ ein. |  |
| `sshPublicKey` | Kopieren Sie Ihren öffentlichen SSH-Schlüssel hierher. |  |  |
| `keyVaultSubscriptionId` | Die Abonnement-ID des Abonnements, das den Schlüsseltresor enthält |  |  |
| `keyVaultResourceGroup` | Der Name der Ressourcengruppe, die den Schlüsseltresor enthält |  |  |
| `keyVaultName` | Der Name des Schlüsseltresors, den Sie erstellt haben |  |  |
| `enableAzure` | Azure Cloudanbieter aktivieren | true <br> false | true |
| `aadClientId` | Azure Active Directory-Client-ID (auch als Anwendungs-ID für den Dienstprinzipal bezeichnet) |  |  |
| `domainName` | Name des zu verwendenden benutzerdefinierten Domänennamens (falls zutreffend). Auf „none“ festlegen, wenn kein vollständig privater Cluster bereitgestellt wird |  | none |
| `masterClusterDnsType` | Domänentyp für die OpenShift-Webkonsole. Bei „default“ wird die DNS-Bezeichnung der öffentlichen Master/Infra-IP-Adresse verwendet. Bei „custom“ können Sie einen eigenen Namen definieren | default <br> custom | default |
| `masterClusterDns` | Der benutzerdefinierte DNS-Name für den Zugriff auf die OpenShift-Webkonsole, wenn für `masterClusterDnsType` „custom“ ausgewählt wurde |  | console.contoso.com |
| `routingSubDomainType` | Ist der Wert auf `nipio` festgelegt, verwendet `routingSubDomain` `nip.io`.  Geben Sie „custom“ an, wenn Sie über eine eigene Domäne für das Routing verfügen | `nipio` <br> custom | `nipio` |
| `routingSubDomain` | Der für das Routing zu verwendende Platzhalter-DNS-Name, wenn Sie für `routingSubDomainType` „custom“ ausgewählt haben |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Wählen Sie aus, ob ein vorhandenes virtuelles Netzwerk verwendet oder ein neues virtuelles Netzwerk erstellt werden soll | existing <br> new | new |
| `virtualNetworkResourceGroupName` | Name der Ressourcengruppe für das neue virtuelle Netzwerk, wenn Sie für `virtualNetworkNewOrExisting` „new“ ausgewählt haben |  | resourceGroup().name |
| `virtualNetworkName` | Name des neuen virtuellen Netzwerks, wenn Sie für `virtualNetworkNewOrExisting` „new“ ausgewählt haben |  | openshiftvnet |
| `addressPrefixes` | Adresspräfix des neuen virtuellen Netzwerks |  | 10.0.0.0/14 |
| `masterSubnetName` | Name des Master-Subnetzes |  | mastersubnet |
| `masterSubnetPrefix` | Für das Master-Subnetz verwendeter CIDR – muss eine Teilmenge des Adresspräfixes sein |  | 10.1.0.0/16 |
| `infraSubnetName` | Name des Infra-Subnetzes |  | infrasubnet |
| `infraSubnetPrefix` | Für das Infra-Subnetz verwendeter CIDR – muss eine Teilmenge des Adresspräfixes sein |  | 10.2.0.0/16 |
| `nodeSubnetName` | Name des Knoten-Subnetzes |  | nodesubnet |
| `nodeSubnetPrefix` | Für das Knoten-Subnetz verwendeter CIDR – muss eine Teilmenge des Adresspräfixes sein |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Vollständige Referenz zum vorhandenen Subnetz für Master-Knoten. Nicht erforderlich, wenn ein neues VNET/Subnetz erstellt wird |  |  |
| `existingInfraSubnetReference` | Vollständige Referenz zum vorhandenen Subnetz für Infra-Knoten. Nicht erforderlich, wenn ein neues VNET/Subnetz erstellt wird |  |  |
| `existingCnsSubnetReference` | Vollständige Referenz zum vorhandenen Subnetz für CNS-Knoten. Nicht erforderlich, wenn ein neues VNET/Subnetz erstellt wird |  |  |
| `existingNodeSubnetReference` | Vollständige Referenz zum vorhandenen Subnetz für Computeknoten. Nicht erforderlich, wenn ein neues VNET/Subnetz erstellt wird |  |  |
| `masterClusterType` | Geben Sie an, ob der Cluster private oder öffentliche Master-Knoten verwendet. Bei Auswahl von „private“ werden die Master-Knoten nicht über eine öffentliche IP-Adresse im Internet offengelegt. Stattdessen wird die in `masterPrivateClusterIp` angegebene private IP-Adresse verwendet | öffentlich <br> Privat | öffentlich |
| `masterPrivateClusterIp` | Wenn private Master-Knoten ausgewählt sind, muss eine private IP-Adresse für die Verwendung durch den internen Lastenausgleich für Master-Knoten angegeben werden. Diese statische IP-Adresse muss innerhalb des CIDR-Blocks für das Master-Subnetz liegen und darf nicht bereits verwendet werden. Wenn öffentliche Master-Knoten ausgewählt sind, wird dieser Wert nicht verwendet, er muss jedoch angegeben sein |  | 10.1.0.200 |
| `routerClusterType` | Geben Sie an, ob der Cluster private oder öffentliche Infra-Knoten verwendet. Bei Auswahl von „private“ werden die Infra-Knoten nicht über eine öffentliche IP-Adresse im Internet offengelegt. Stattdessen wird die in `routerPrivateClusterIp` angegebene private IP-Adresse verwendet | öffentlich <br> Privat | öffentlich |
| `routerPrivateClusterIp` | Wenn private Infra-Knoten ausgewählt sind, muss eine private IP-Adresse für die Verwendung durch den internen Lastenausgleich für Infra-Knoten angegeben werden. Diese statische IP-Adresse muss innerhalb des CIDR-Blocks für das Infrasubnetz liegen und darf nicht bereits verwendet werden. Wenn öffentliche Infra-Knoten ausgewählt sind, wird dieser Wert nicht verwendet, er muss jedoch angegeben sein |  | 10.2.0.200 |
| `routingCertType` | Verwenden Sie ein benutzerdefiniertes Zertifikat für die Routingdomäne oder das standardmäßige selbstsignierte Zertifikat – befolgen Sie die Anweisungen im Abschnitt **Benutzerdefinierte Zertifikate** | selfsigned <br> custom | selfsigned |
| `masterCertType` | Verwenden Sie ein benutzerdefiniertes Zertifikat für die Masterdomäne oder das standardmäßige selbstsignierte Zertifikat – befolgen Sie die Anweisungen im Abschnitt **Benutzerdefinierte Zertifikate** | selfsigned <br> custom | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

> [!NOTE] 
> Der folgende Befehl erfordert Azure CLI 2.0.8 oder höher. Sie können die CLI-Version mit dem `az --version`-Befehl überprüfen. Informationen zum Aktualisieren der CLI-Version finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Im folgenden Beispiel werden der OpenShift-Cluster und alle dazugehörigen Ressourcen in der Ressourcengruppe „openshiftrg“ mit dem Bereitstellungsnamen „myOpenShiftCluster“ bereitgestellt. Auf die Vorlage wird direkt aus dem GitHub-Repository verwiesen. Außerdem wird die lokale Parameterdatei „azuredeploy.parameters.json“ verwendet.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Der Bereitstellungsvorgang dauert mindestens 60 Minuten. Die Dauer hängt von der Gesamtzahl der bereitgestellten Knoten sowie von den konfigurierten Optionen ab. Nach Abschluss der Bereitstellung werden der Bastion-DNS-FQDN und die URL der OpenShift-Konsole im Terminal ausgegeben.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Wenn Sie die Befehlszeile nicht blockieren möchten, bis die Bereitstellung abgeschlossen ist, fügen Sie `--no-wait` als eine der Optionen für die Gruppenbereitstellung hinzu. Die Ausgabe der Bereitstellung kann über das Azure-Portal im Bereitstellungsabschnitt für die Ressourcengruppe abgerufen werden.

## <a name="connect-to-the-openshift-cluster"></a>Herstellen einer Verbindung mit dem OpenShift-Cluster

Rufen Sie nach Abschluss der Bereitstellung die Verbindung aus dem Ausgabeabschnitt der Bereitstellung ab. Stellen Sie über Ihren Browser mithilfe der **URL für die OpenShift-Konsole** eine Verbindung mit der OpenShift-Konsole her. Sie können auch eine SSH-Verbindung mit dem Bastion-Host herstellen. Im folgenden Beispiel lautet der Administratorbenutzername „clusteradmin“, und der DNS-FQDN der öffentlichen Bastion-IP-Adresse lautet „bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com“:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, OpenShift-Cluster und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) entfernen.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nächste Schritte

- [Aufgaben nach der Bereitstellung](./openshift-container-platform-3x-post-deployment.md)
- [Behandeln von Problemen beim Bereitstellen von OpenShift in Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Getting Started with OpenShift Container Platform](https://docs.openshift.com) (Erste Schritte mit OpenShift Container Platform)
