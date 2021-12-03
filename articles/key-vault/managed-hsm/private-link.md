---
title: Konfigurieren des verwalteten HSM von Azure Key Vault mit privaten Endpunkten
description: Hier erfahren Sie, wie Sie das verwaltete HSM von Azure Key Vault in den Azure Private Link-Dienst integrieren.
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/21/2021
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e7ccb16efe21503087dcac035e2ca8f832ef7b7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370345"
---
# <a name="integrate-managed-hsm-with-azure-private-link"></a>Integrieren des verwalteten HSM in Azure Private Link

Mit dem Azure Private Link-Dienst können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-Dienste wie verwaltetes HSM, Azure Storage und Azure Cosmos DB sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

Weitere Informationen finden Sie unter [Was ist Azure Private Link?](../../private-link/private-link-overview.md).

> [!NOTE]
> Verwaltetes HSM unterstützt derzeit keine IP-Regeln oder [VNET-Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md). 
> 
## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie ein verwaltetes HSM in Azure Private Link integrieren möchten, benötigen Sie Folgendes:

- Ein verwaltetes HSM. Ausführlichere Informationen finden Sie unter [Bereitstellen und Aktivieren eines verwalteten HSM mithilfe der Azure-Befehlszeilenschnittstelle](quick-create-cli.md).
- Ein virtuelles Azure-Netzwerk
- Ein Subnetz in dem virtuellen Netzwerk
- Berechtigungen vom Typ „Besitzer“ oder „Mitwirkender“ für das verwaltete HSM und für das virtuelle Netzwerk.
- Azure-Befehlszeilenschnittstelle ab Version 2.25.0 Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

Der private Endpunkt und das virtuelle Netzwerk müssen sich in der gleichen Region befinden. Wenn Sie über das Portal eine Region für den privaten Endpunkt auswählen, wird automatisch nach virtuellen Netzwerken in dieser Region gefiltert. Ihr HSM kann sich in einer anderen Region befinden.

Der private Endpunkt verwendet eine private IP-Adresse in Ihrem virtuellen Netzwerk.


## <a name="establish-a-private-link-connection-to-managed-hsm-using-cli-initial-setup"></a>Einrichten einer Private Link-Verbindung mit dem verwalteten HSM über die Befehlszeilenschnittstelle (Ersteinrichtung)

```azurecli
az login                                                                   # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                            # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                            # Create a new Resource Group
az provider register -n Microsoft.KeyVault                                 # Register KeyVault as a provider
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny # Turn on firewall

az network vnet create -g {RG} -n {vNet NAME} --location {REGION}           # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.managedhsm.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.managedhsm.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="allow-trusted-services-to-access-managed-hsm"></a>Zulassen des Zugriffs auf das verwaltete HSM durch vertrauenswürdige Dienste

Bei aktivierter Firewall wird sämtlicher Zugriff auf das HSM von jedem Ort aus verweigert, der keine Verbindung mit privaten Endpunkten verwendet. Das gilt auch für das öffentliche Internet und für Azure-Dienste. Verwenden Sie die Option `--bypass AzureServices`, wenn Sie Microsoft-Diensten den Zugriff auf Ihre Schlüssel in Ihrem verwalteten HSM gestatten möchten. Die einzelnen Entitäten (beispielsweise ein Azure Storage-Konto oder eine Azure SQL Server-Instanz) müssen allerdings trotzdem über bestimmte Rollenzuweisungen verfügen, um auf einen Schlüssel zugreifen zu können. 

> [!NOTE]
> Es werden nur bestimmte Verwendungsszenarien für vertrauenswürdige Dienste unterstützt. Ausführlichere Informationen finden Sie in der Liste unter [Vertrauenswürdige Dienste](../general/overview-vnet-service-endpoints.md#trusted-services).

```azurecli
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny --bypass AzureServices
```

### <a name="create-a-private-endpoint-automatically-approve"></a>Erstellen eines privaten Endpunkts (automatische Genehmigung) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

> [!NOTE]
> Wenn Sie dieses HSM löschen, funktioniert der private Endpunkt nicht mehr. Wenn Sie das HSM später wiederherstellen (also das Löschen rückgängig machen), müssen Sie einen neuen privaten Endpunkt erstellen.

### <a name="create-a-private-endpoint-manually-request-approval"></a>Erstellen eines privaten Endpunkts (manuelle Anforderung der Genehmigung) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Verwalten von Private Link-Verbindungen

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --hsm-name {HSM NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Hinzufügen privater DNS-Datensätze
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.managedhsm.azure.net" -n {HSM NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.managedhsm.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {HSM NAME}.managedhsm.azure.net
nslookup {HSM NAME}.privatelink.managedhsm.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Überprüfen, ob die Private Link-Verbindung funktioniert

Vergewissern Sie sich, dass die Ressourcen innerhalb des Subnetzes, in dem sich auch die private Endpunktressource befindet, mit Ihrem HSM eine Verbindung über eine private IP-Adresse herstellen, und dass die Integration in die private DNS-Zone korrekt ist.

Erstellen Sie zunächst einen virtuellen Computer. Eine entsprechende Anleitung finden Sie unter [Schnellstart: Erstellen eines virtuellen Windows-Computers im Azure-Portal](../../virtual-machines/windows/quick-create-portal.md).

Gehen Sie auf der Registerkarte „Netzwerk“ wie folgt vor:

1. Geben Sie ein virtuelles Netzwerk und ein Subnetz an. Sie können ein neues virtuelles Netzwerk erstellen oder ein bereits vorhandenes virtuelles Netzwerk auswählen. Vergewissern Sie sich bei Verwendung eines bereits vorhandenen Netzwerks, dass die Region übereinstimmt.
1. Geben Sie eine öffentliche IP-Ressource an.
1. Wählen Sie für „NIC-Netzwerksicherheitsgruppe“ die Option „Keine“ aus.
1. Wählen Sie für den Lastenausgleich „Nein“ aus.

Öffnen Sie die Befehlszeile, und führen Sie den folgenden Befehl aus:

```console
nslookup <your-HSM-name>.managedhsm.azure.net
```

Wenn Sie den Befehl „ns lookup“ ausführen, um die IP-Adresse eines verwalteten HSM über einen öffentlichen Endpunkt aufzulösen, sieht das Ergebnis wie folgt aus:

```console
c:\ >nslookup <your-hsm-name>.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-hsm-name>.managedhsm.azure.net
```

Wenn Sie den Befehl „ns lookup“ ausführen, um die IP-Adresse eines verwalteten HSM über einen privaten Endpunkt aufzulösen, sieht das Ergebnis wie folgt aus:

```console
c:\ >nslookup your_hsm_name.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-hsm-name>.managed.azure.net
          <your-hsm-name>.privatelink.managedhsm.azure.net
```

## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

* Stellen Sie sicher, dass sich der private Endpunkt im Zustand „Genehmigt“ befindet. 
    1. Verwenden Sie den Unterbefehl ```az keyvault private-endpoint-connections show```, um den Status einer Verbindung mit einem privaten Endpunkt anzuzeigen.
    2. Vergewissern Sie sich, dass der Verbindungsstatus „Genehmigt“ und der Bereitstellungsstatus „Erfolgreich“ lautet. 
    3. Vergewissern Sie sich, dass das virtuelle Netzwerk dem von Ihnen verwendeten virtuellen Netzwerk entspricht.

* Vergewissern Sie sich, dass Sie über eine Ressource vom Typ „Private DNS-Zone“ verfügen. 
    1. Sie müssen über eine Ressource vom Typ „Private DNS-Zone“ mit exakt dem folgenden Namen verfügen: „privatelink.managedhsm.azure.net“. 
    2. Entsprechende Einrichtungsinformationen finden Sie unter folgendem Link: [Was ist eine private Azure DNS-Zone?](../../dns/private-dns-privatednszone.md)
    
* Vergewissern Sie sich, dass die private DNS-Zone mit dem virtuellen Netzwerk verknüpft ist. Dieses Problem kann vorliegen, wenn weiterhin die öffentliche IP-Adresse zurückgegeben wird. 
    1. Ist die private DNS-Zone nicht mit dem virtuellen Netzwerk verknüpft, wird bei der DNS-Abfrage aus dem virtuellen Netzwerk die öffentliche IP-Adresse des HSM zurückgegeben. 
    2. Navigieren Sie im Azure-Portal zur Ressource „Private DNS-Zone“, und klicken Sie auf die Option „Verknüpfungen virtueller Netzwerke“. 
    4. Das virtuelle Netzwerk, von dem Aufrufe an das HSM gesendet werden, muss aufgeführt werden. 
    5. Ist dies nicht der Fall, fügen Sie es hinzu. 
    6. Eine ausführliche Anleitung finden Sie unter [Verknüpfen des virtuellen Networks](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

* Vergewissern Sie sich, dass in der privaten DNS-Zone kein A-Eintrag für das HSM fehlt. 
    1. Navigieren Sie zur Seite „Private DNS-Zone“. 
    2. Klicken Sie auf „Übersicht“, und vergewissern Sie sich, dass ein A-Eintrag mit dem einfachen Namen Ihres HSM vorhanden ist. Geben Sie kein Suffix an.
    3. Überprüfen Sie die Schreibweise, und erstellen oder korrigieren Sie den A-Eintrag. Die Gültigkeitsdauer kann auf „3600“ (eine Stunde) festgelegt werden. 
    4. Achten Sie darauf, die richtige private IP-Adresse anzugeben. 
    
* Vergewissern Sie sich, dass der A-Eintrag über die richtige IP-Adresse verfügt. 
    1. Zur Bestätigung der IP-Adresse können Sie im Azure-Portal die private Endpunktressource öffnen.
    2. Navigieren Sie im Azure-Portal zur Ressource „Microsoft.Network/privateEndpoints“.
    3. Suchen Sie auf der Übersichtsseite nach „Netzwerkschnittstelle“, und klicken Sie auf den entsprechenden Link. 
    4. Daraufhin wird die Übersicht über die NIC-Ressource mit der Eigenschaft „Private IP-Adresse“ angezeigt. 
    5. Vergewissern Sie sich, dass es sich bei der IP-Adresse um die richtige Adresse aus dem A-Eintrag handelt.

## <a name="limitations-and-design-considerations"></a>Einschränkungen und Entwurfsaspekte

> [!NOTE]
> Die Anzahl verwalteter HSMs mit aktivierten privaten Endpunkten pro Abonnement ist ein anpassbarer Grenzwert. Der gezeigte Grenzwert ist der Standardwert. Wenn Sie eine Erhöhung des Grenzwerts für Ihr Abonnement anfordern möchten, erstellen Sie ein Azure-Supportticket. Wir genehmigen diese Anträge je nach Fall.

**Preise:** Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link/).

**Maximal zulässige Anzahl privater Endpunkte pro verwaltetem HSM:** 64.

**Standardanzahl verwalteter HSMs mit privaten Endpunkten pro Abonnement:** 400.

Weitere Informationen finden Sie unter [Was ist der Azure Private Link-Dienst? – Einschränkungen](../../private-link/private-link-service-overview.md#limitations).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure Private Link](../../private-link/private-link-service-overview.md).
- Weitere Informationen zu verwalteten HSMs finden Sie [hier](overview.md).
