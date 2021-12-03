---
title: Einrichten eines privaten Endpunkts mit privater Verbindung
description: Richten Sie einen privaten Endpunkt in einer Containerregistrierung ein, und ermöglichen Sie den Zugriff auf ein virtuelles Netzwerk über eine private Verbindung. Der Zugriff auf private Links ist ein Feature der Dienstebene Premium.
ms.topic: article
ms.date: 10/26/2021
ms.openlocfilehash: ef3f6cd30852f6c2bac5f5e9dce383d8f4501023
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523408"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link

Beschränken Sie den Zugriff auf eine Registrierung, indem Sie den Registrierungsendpunkten private IP-Adressen eines virtuellen Netzwerks zuweisen und [Azure Private Link](../private-link/private-link-overview.md) verwenden. Der Netzwerkdatenverkehr zwischen den Clients im virtuellen Netzwerk und den privaten Endpunkten der Registrierung wird über das virtuelle Netzwerk und einen privaten Link im Microsoft-Backbonenetzwerk geleitet, und somit nicht im öffentlichen Internet verfügbar gemacht. Private Link ermöglicht auch den Zugriff auf die private Registrierung aus einem lokalen System über privates Peering mit [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) oder ein [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

Sie können für den privaten Endpunkt der Registrierung [DNS-Einstellungen konfigurieren](../private-link/private-endpoint-overview.md#dns-configuration), sodass die Einstellungen zur zugeordneten privaten IP-Adresse der Registrierung aufgelöst werden. Dank DNS-Konfiguration können Clients und Dienste im Netzwerk weiterhin unter dem vollqualifizierten Domänennamen der Registrierung (beispielsweise *myregistry.azurecr.io*) auf die Registrierung zugreifen. 

Dieser Artikel zeigt, wie Sie einen privaten Endpunkt für Ihre Registrierung mithilfe des Azure-Portals (empfohlen) oder der Azure CLI konfigurieren. Diese Funktion ist auf der Dienstebene **Premium** der Containerregistrierung verfügbar. Weitere Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

> [!NOTE]
> Ab Oktober 2021 lassen neue Containerregistrierungen maximal 200 private Endpunkte zu. Früher erstellte Registrierungen lassen maximal 10 private Endpunkte zu. Verwenden Sie den Befehl [az acr show-usage](/cli/azure/acr#az_acr_show_usage), um den Grenzwert für Ihre Registrierung anzuzeigen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein virtuelles Netz und Subnetz, in dem der private Endpunkt eingerichtet werden soll. Sie können bei Bedarf auch [ein neues virtuelles Netzwerk und Subnetz erstellen](../virtual-network/quick-create-portal.md).
* Zu Testzwecken wird empfohlen, eine VM im virtuellen Netzwerk einzurichten. Schritte zum Erstellen einer Test-VM für den Zugriff auf Ihre Registrierung finden Sie unter [Erstellen eines Docker-fähigen virtuellen Computers](container-registry-vnet.md#create-a-docker-enabled-virtual-machine). 
* Wenn Sie die Azure CLI-Schritte in diesem Artikel verwenden möchten, wird mindestens die Azure CLI-Version 2.6.0 empfohlen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli]. Alternativ können Sie [Azure Cloud Shell](../cloud-shell/quickstart.md) verwenden.
* Sollten Sie noch nicht über eine Containerregistrierung verfügen, können Sie eine Registrierung erstellen (Premium-Tarif erforderlich) und ein öffentliches Beispielimage (etwa `mcr.microsoft.com/hello-world`) aus Microsoft Container Registry [importieren](container-registry-import-images.md). Zum Erstellen einer Registrierung können Sie das [Azure-Portal][quickstart-portal] oder die [Azure-Befehlszeilenschnittstelle][quickstart-cli] verwenden.

### <a name="register-container-registry-resource-provider"></a>Registrieren des Ressourcenanbieters für die Containerregistrierung

Um den Registrierungszugriff mithilfe einer privaten Verbindung in einem anderen Azure-Abonnement oder -Mandanten zu konfigurieren, müssen Sie [den Ressourcenanbieter für Azure Container Registry in diesem Abonnement registrieren](../azure-resource-manager/management/resource-providers-and-types.md). Hierfür können Sie das Azure-Portal, die Azure CLI oder andere Tools verwenden.

Beispiel:

```azurecli
az account set --subscription <Name or ID of subscription of private link>

az provider register --namespace Microsoft.ContainerRegistry
``` 

## <a name="set-up-private-endpoint---portal-recommended"></a>Einrichten eines privaten Endpunkts im Portal (empfohlen)

Richten Sie einen privaten Endpunkt ein, wenn Sie eine Registrierung erstellen, oder fügen Sie einen privaten Endpunkt zu einer bestehenden Registrierung hinzu. 

### <a name="create-a-private-endpoint---new-registry"></a>Erstellen eines privaten Endpunkts: neue Registrierung

1. Wenn Sie eine Registrierung im Portal erstellen, wählen Sie auf der Registerkarte **Grundlagen** in **SKU** den Tarif **Premium** aus.
1. Wählen Sie die Registerkarte **Netzwerk** aus.
1. Wählen Sie in **Netzwerkverbindung** die Optionen **Privater Endpunkt** >  **+ Hinzufügen** aus.
1. Geben Sie die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Geben Sie den Namen einer vorhandenen Gruppe ein, oder erstellen Sie eine neue.|
    | Name | Geben Sie einen eindeutigen Namen ein. |
    | Registrierungsunterressource |Wählen Sie **Registrierung** aus.|
    | **Netzwerk** | |
    | Virtuelles Netzwerk| Wählen Sie das virtuelle Netz für den privaten Endpunkt aus. Beispiel: *myDockerVMVNET*. |
    | Subnet | Wählen Sie das Subnetz für den privaten Endpunkt aus. Beispiel: *myDockerVMSubnet*. |
    |**Private DNS-Integration**||
    |Integration in eine private DNS-Zone |Wählen Sie **Ja** aus. |
    |Private DNS-Zone |Wählen Sie *(Neu) privatelink.azurecr.io* aus. |
    |||
1. Konfigurieren Sie die restlichen Registrierungseinstellungen, und wählen Sie dann **Überprüfen und erstellen** aus.
  
:::image type="content" source="media/container-registry-private-link/private-link-create-portal.png" alt-text="Erstellen einer Registrierung mit einem privaten Endpunkt":::



Ihre private Verbindung ist nun konfiguriert und einsatzbereit.

### <a name="create-a-private-endpoint---existing-registry"></a>Erstellen eines privaten Endpunkts: vorhandene Registrierung

1. Navigieren Sie im Azure-Portal zu Ihrer Containerregistrierung.
1. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Private Endpunkte** die Option **+ Privater Endpunkt** aus.
    :::image type="content" source="media/container-registry-private-link/private-endpoint-existing-registry.png" alt-text="Hinzufügen eines privaten Endpunkts zur Registrierung":::

1. Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Geben Sie den Namen einer vorhandenen Gruppe ein, oder erstellen Sie eine neue.|
    | **Instanzendetails** |  |
    | Name | Geben Sie einen Namen ein. |
    |Region|Wählen Sie eine Region aus.|
    |||
1. Klicken Sie auf **Weiter: Ressource** aus.
1. Geben Sie die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    |Verbindungsmethode  | Wählen Sie für dieses Beispiel **Verbindung mit einer Azure-Ressource in meinem Verzeichnis herstellen** aus.|
    | Subscription| Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.ContainerRegistry/registries** aus. |
    | Resource |Wählen Sie den Namen Ihrer Registrierung aus.|
    |Unterressource des Ziels |Wählen Sie **Registrierung** aus.|
    |||
1. Klicken Sie auf **Weiter: Konfiguration** aus.
1. Geben Sie die folgenden Informationen an:

    | Einstellung | Wert |
    | ------- | ----- |
    |**Netzwerk**| |
    | Virtuelles Netzwerk| Wählen Sie das virtuelle Netz für den privaten Endpunkt aus. |
    | Subnet | Wählen Sie das Subnetz für den privaten Endpunkt aus. |
    |**Private DNS-Integration**||
    |Integration in eine private DNS-Zone |Wählen Sie **Ja** aus. |
    |Private DNS-Zone |Wählen Sie *(Neu) privatelink.azurecr.io* aus. |
    |||

1. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft. 
1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

### <a name="confirm-endpoint-configuration"></a>Bestätigen der Konfiguration des privaten Endpunkts

Nachdem der private Endpunkt erstellt wurde, werden DNS-Einstellungen in der privaten Zone in den Einstellungen zu **Private Endpunkte** im Portal angezeigt:

1. Navigieren Sie im Portal zu Ihrer Containerregistrierung, und wählen Sie **Einstellungen > Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Private Endpunkte** den privaten Endpunkt aus, den Sie erstellt haben. 
1. Wählen Sie **DNS-Konfiguration** aus.
1. Überprüfen Sie die Verbindungs- und benutzerdefinierten DNS-Einstellungen.

:::image type="content" source="media/container-registry-private-link/private-endpoint-overview.png" alt-text="DNS-Einstellungen für den Endpunkt im Portal":::
## <a name="set-up-private-endpoint---cli"></a>Einrichten eines privaten Endpunkts über die CLI

In den Azure CLI-Beispielen dieses Artikels werden die folgenden Umgebungsvariablen verwendet. Sie benötigen die Namen einer vorhandenen Containerregistrierung, eines virtuellen Netzwerks und eines Subnetzes, um einen privaten Endpunkt einzurichten. Ersetzen Sie die Werte durch entsprechende Werte für Ihre Umgebung. Alle Beispiele sind für die Bash-Shell formatiert:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name> # Resource group for your existing virtual network and subnet
NETWORK_NAME=<virtual-network-name>
SUBNET_NAME=<subnet-name>
```
### <a name="disable-network-policies-in-subnet"></a>Deaktivieren von Netzwerkrichtlinien im Subnetz

[Deaktivieren Sie Netzwerkrichtlinien](../private-link/disable-private-endpoint-network-policy.md) (beispielsweise Netzwerksicherheitsgruppen) im Subnetz für den privaten Endpunkt. Aktualisieren Sie Ihre Subnetzkonfiguration mithilfe des Befehls [az network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone

Erstellen Sie eine [private Azure DNS-Zone](../dns/private-dns-privatednszone.md) für die private Domäne der Azure-Containerregistrierung. Später erstellen Sie dann DNS-Einträge für Ihre Registrierungsdomäne innerhalb dieser DNS-Zone. Weitere Informationen finden Sie weiter unten in diesem Artikel unter [DNS-Konfigurationsoptionen](#dns-configuration-options).

Wenn Sie die DNS-Standardauflösung für Ihre Containerregistrierung mithilfe einer privaten Zone überschreiben möchten, muss die Zone den Namen **privatelink.azurecr.io** haben. Führen Sie den Befehl [az network private-dns zone create][az-network-private-dns-zone-create] wie folgt aus, um die private Zone zu erstellen:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Erstellen eines Zuordnungslinks

Führen Sie [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] aus, um Ihre private Zone dem virtuellen Netzwerk zuzuordnen. In diesem Beispiel wird ein Link namens *myDNSLink* erstellt.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Erstellen eines privaten Registrierungsendpunkts

In diesem Abschnitt wird der private Endpunkt der Registrierung im virtuellen Netzwerk erstellt. Rufen Sie zunächst die Ressourcen-ID Ihrer Registrierung ab:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Führen Sie den Befehl [az network private-endpoint create][az-network-private-endpoint-create] aus, um den privaten Endpunkt der Registrierung zu erstellen.

Im folgenden Beispiel werden der Endpunkt *myPrivateEndpoint* und die Dienstverbindung *myConnection* erstellt. Übergeben Sie `--group-ids registry`, um eine Containerregistrierungsressource für den Endpunkt anzugeben:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>Abrufen der Endpunkt-IP-Konfiguration

Rufen Sie zum Konfigurieren von DNS-Einträgen die IP-Konfiguration des privaten Endpunkts ab. Mit der Netzwerkschnittstelle des privaten Endpunkts sind in diesem Beispiel zwei private IP-Adressen für die Containerregistrierung verknüpft: eine für die eigentliche Registrierung und eine für den Datenendpunkt der Registrierung. Wenn Ihre Registrierung georepliziert ist, wird jedem Replikat eine zusätzliche IP-Adresse zugeordnet.

Führen Sie zunächst [az network private-endpoint show][az-network-private-endpoint-show] aus, um die Netzwerkschnittstellen-ID vom privaten Endpunkt abzufragen:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Die folgenden [az network nic show][az-network-nic-show]-Befehle rufen die privaten IP-Adressen und FQDNs für die Containerregistrierung und den Datenendpunkt der Registrierung ab:

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

#### <a name="additional-endpoints-for-geo-replicas"></a>Zusätzliche Endpunkte für Georeplikate

Wenn Ihre Registrierung [georepliziert](container-registry-geo-replication.md) ist, fragen Sie den zusätzlichen Datenendpunkt für jedes Registrierungsreplikat ab. Beispiel: in der Region *eastus*: 

```azurecli
REPLICA_LOCATION=eastus
GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateIpAddress" \
  --output tsv) 

GEO_REPLICA_DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```
### <a name="create-dns-records-in-the-private-zone"></a>Erstellen von DNS-Einträgen in der privaten Zone

Mit den folgenden Befehlen werden DNS-Einträge in der privaten Zone für den Registrierungsendpunkt und den zugehörigen Datenendpunkt erstellt. Wenn Sie also beispielsweise über eine Registrierung namens *myregistry* in der Region *westeurope* verfügen, lauten die Endpunktnamen `myregistry.azurecr.io` und `myregistry.westeurope.data.azurecr.io`. 

Führen Sie zuerst [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] aus, um leere A-Ressourceneintragssätze für den Registrierungsendpunkt und Datenendpunkt zu erstellen:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Führen Sie den Befehl [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] aus, um die A-Einträge für den Registrierungsendpunkt und Datenendpunkt zu erstellen:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

#### <a name="additional-records-for-geo-replicas"></a>Zusätzliche Datensätze für Georeplikate

Wenn Ihre Registrierung georepliziert ist, erstellen Sie zusätzliche DNS-Einstellungen für jedes Replikat. Fortsetzung des Beispiels in der Region *eastus*:

```azurecli
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP
```

Die private Verbindung ist nun konfiguriert und einsatzbereit.

## <a name="disable-public-access"></a>Deaktivieren des öffentlichen Zugriffs

Deaktivieren Sie für viele Szenarien den Registrierungszugriff aus öffentlichen Netzwerken. Mit dieser Konfiguration wird verhindert, dass Clients von außerhalb des virtuellen Netzwerks die Registrierungsendpunkte erreichen können. 

### <a name="disable-public-access---portal"></a>Deaktivieren des öffentlichen Zugriffs – Portal

1. Navigieren Sie im Portal zu Ihrer Containerregistrierung, und wählen Sie **Einstellungen > Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Öffentlicher Zugriff** in **Öffentlichen Netzwerkzugriff zulassen** die Option **Deaktiviert** aus. Klicken Sie dann auf **Speichern**.

### <a name="disable-public-access---cli"></a>Deaktivieren des öffentlichen Zugriffs – CLI

Um den öffentlichen Zugriff mithilfe der Azure CLI zu deaktivieren, führen Sie [az acr update][az-acr-update] aus, und legen Sie `--public-network-enabled` auf `false` fest. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```

## <a name="validate-private-link-connection"></a>Überprüfen der Private Link-Verbindung

Vergewissern Sie sich, dass die Ressourcen im Subnetz des privaten Endpunkts eine Verbindung mit Ihrer Registrierung über eine private IP-Adresse herstellen und dass die Integration in die private DNS-Zone korrekt ist.

Stellen Sie zum Überprüfen der Private Link-Verbindung eine Verbindung mit dem virtuellen Computer her, den Sie im virtuellen Netzwerk eingerichtet haben.

Führen Sie ein Hilfsprogramm wie `nslookup` oder `dig` aus, um die IP-Adresse Ihrer Registrierung über die private Verbindung nachzuschlagen. Beispiel:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Die Beispielausgabe zeigt die IP-Adresse der Registrierung im Adressraum des Subnetzes:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Vergleichen Sie dieses Ergebnis mit der öffentlichen IP-Adresse in der Ausgabe von `dig` für die gleiche Registrierung über einen öffentlichen Endpunkt:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Registrierungsvorgänge über die private Verbindung

Vergewissern Sie sich außerdem, dass Sie Registrierungsvorgänge über den virtuellen Computer im Netzwerk ausführen können. Stellen Sie eine SSH-Verbindung mit Ihrem virtuellen Computer her, und führen Sie [az acr login][az-acr-login] aus, um sich bei Ihrer Registrierung anzumelden. Je nach VM-Konfiguration müssen die folgenden Befehle unter Umständen mit dem Präfix `sudo` versehen werden.

```bash
az acr login --name $REGISTRY_NAME
```

Führen Sie Registrierungsvorgänge aus (beispielsweise `docker pull`, um ein Beispielimage aus der Registrierung zu pullen). Ersetzen Sie `hello-world:v1` durch ein geeignetes Image und Tag für Ihre Registrierung, und geben Sie den Namen des Anmeldeservers für die Registrierung als Präfix an (nur Kleinbuchstaben):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker ruft erfolgreich mittels Pullvorgang das Image auf den virtuellen Computer ab.

## <a name="manage-private-endpoint-connections"></a>Verwalten von Verbindungen mit dem privaten Endpunkt

Die Verbindungen mit dem privaten Endpunkt einer Registrierung können über das Azure-Portal oder mithilfe von Befehlen aus der Befehlsgruppe [az acr private-endpoint-connection][az-acr-private-endpoint-connection] verwaltet werden. Die Vorgänge umfassen das Genehmigen, Löschen, Auflisten oder Ablehnen von Verbindungen mit dem privaten Endpunkt einer Registrierung sowie das Anzeigen von Details.

Führen Sie also beispielsweise den Befehl [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] aus, um die Verbindungen mit dem privaten Endpunkt einer Registrierung aufzulisten. Beispiel:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Wenn Sie mithilfe der in diesem Artikel beschriebenen Schritte eine Verbindung mit einem privaten Endpunkt einrichten, akzeptiert die Registrierung automatisch Verbindungen von Clients und Diensten, die über Azure RBAC-Berechtigungen für die Registrierung verfügen. Sie können den Endpunkt so einrichten, dass Verbindungen manuell genehmigt werden müssen. Informationen zum Genehmigen und Ablehnen von Verbindungen mit einem privaten Endpunkt finden Sie unter [Verwalten einer Verbindung mit einem privaten Endpunkt](../private-link/manage-private-endpoint.md).

> [!IMPORTANT]
> Wenn Sie derzeit einen privaten Endpunkt aus einer Registrierung löschen, müssen Sie möglicherweise auch die Verknüpfung des virtuellen Netzwerks mit der privaten Zone löschen. Wird die Verknüpfung nicht gelöscht, wird unter Umständen ein Fehler wie `unresolvable host` angezeigt.

## <a name="dns-configuration-options"></a>DNS-Konfigurationsoptionen

Der private Endpunkt in diesem Beispiel integriert sich in eine private DNS-Zone, die einem virtuellen Basisnetzwerk zugeordnet ist. Diese Einrichtung verwendet den von Azure bereitgestellten DNS-Dienst direkt, um den öffentlichen FQDN der Registrierung in seine privaten IP-Adressen im virtuellen Netzwerk aufzulösen. 

Die private Verbindung unterstützt zusätzliche DNS-Konfigurationsszenarien, in denen die private Zone verwendet wird, einschließlich benutzerdefinierter DNS-Lösungen. Angenommen, Sie verfügen z. B. über eine benutzerdefinierte DNS-Lösung, die im virtuellen Netzwerk oder lokal in einem Netzwerk bereitgestellt ist, das über ein VPN-Gateway oder über Azure ExpressRoute mit dem virtuellen Netzwerk verbunden wird. 

Um den öffentlichen FQDN der Registrierung in die private IP-Adresse in diesen Szenarien aufzulösen, müssen Sie eine Weiterleitung auf Serverebene für den Azure DNS-Dienst (168.63.129.16) konfigurieren. Genaue Konfigurationsoptionen und -schritte sind von Ihren vorhandenen Netzwerken und dem DNS abhängig. Beispiele finden Sie unter [DNS-Konfiguration von privaten Azure-Endpunkten](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Falls Sie für Hochverfügbarkeit private Endpunkte in mehreren Regionen erstellt haben, sollten Sie in jeder Region eine separate Ressourcengruppe verwenden und darin das virtuelle Netzwerk und die zugehörige private DNS-Zone platzieren. Diese Konfiguration verhindert auch eine unvorhersehbare DNS-Auflösung, die durch die gemeinsame Nutzung derselben privaten DNS-Zone verursacht wird.

### <a name="manually-configure-dns-records"></a>Manuelles Konfigurieren von DNS-Einträgen

In einigen Szenarien müssen Sie DNS-Einträge in einer privaten Zone möglicherweise manuell konfigurieren, anstatt die von Azure bereitgestellte private Zone zu verwenden. Erstellen Sie unbedingt Datensätze für jeden der folgenden Endpunkte: Registrierungsendpunkt, Datenendpunkt der Registrierung und Datenendpunkt für jedes zusätzliche regionale Replikat. Sind nicht alle Datensätze konfiguriert, ist die Registrierung möglicherweise nicht erreichbar.

> [!IMPORTANT]
> Wenn Sie später ein neues Replikat hinzufügen, müssen Sie für den Datenendpunkt in dieser Region manuell einen neuen DNS-Eintrag hinzufügen. Wenn Sie beispielsweise ein Replikat von *myregistry* am Standort „northeurope“ erstellen, fügen Sie einen Eintrag für `myregistry.northeurope.data.azurecr.io` hinzu.

Die FQDNs und privaten IP-Adressen, die Sie zum Erstellen von DNS-Einträgen benötigen, sind der Netzwerkschnittstelle des privaten Endpunkts zugeordnet. Sie können diese Informationen über das Azure-Portal oder die Azure CLI abrufen.

* Navigieren Sie im Portal zu Ihrem privaten Endpunkt, und wählen Sie **DNS-Konfiguration** aus. 
* Führen Sie mithilfe der Azure CLI den Befehl [az network nic show][az-network-nic-show] aus. Beispielbefehle finden Sie weiter oben in diesem Artikel unter [Abrufen der Endpunkt-IP-Konfiguration](#get-endpoint-ip-configuration).

Stellen Sie nach dem Erstellen von DNS-Einträgen sicher, dass die Registrierungs-FQDNs ordnungsgemäß in ihre jeweiligen privaten IP-Adressen aufgelöst werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihre Ressourcen über das Portal bereinigen möchten, navigieren Sie zu Ihrer Ressourcengruppe. Klicken Sie nach dem Laden der Ressourcengruppe auf **Ressourcengruppe löschen**, um die Ressourcengruppe und die dort gespeicherten Ressourcen zu entfernen.

Wenn Sie alle Azure-Ressourcen in der gleichen Ressourcengruppe erstellt haben und sie nicht mehr benötigen, können Sie die Ressourcen optional mit einem einzigen Befehl [az group delete](/cli/azure/group) löschen:

```azurecli
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Private Link finden Sie in der Dokumentation zu [Azure Private Link](../private-link/private-link-overview.md).

* Um DNS-Einstellungen im virtuellen Netzwerk zu überprüfen, die zu einem privaten Endpunkt führen, führen Sie den Befehl [az acr check-health](/cli/azure/acr#az_acr_check_health) mit dem Parameter `--vnet` aus. Weitere Informationen finden Sie unter [Überprüfen der Integrität einer Azure-Containerregistrierung](container-registry-check-health.md). 

* Wenn Sie Zugriffsregeln für die Registrierung hinter einer Clientfirewall einrichten müssen, finden Sie Informationen hierzu unter [Konfigurieren von Regeln für den Zugriff auf eine Azure-Containerregistrierung hinter einer Firewall](container-registry-firewall-access-rules.md).

* [Behandeln von Problemen mit der Konnektivität privater Azure-Endpunkte](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
