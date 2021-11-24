---
title: Verfügbarmachen von Anwendungen für das Internet mithilfe von Application Gateway
titleSuffix: Azure Spring Cloud
description: Verfügbarmachen von Anwendungen für das Internet mithilfe von Application Gateway
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: e23503c7e38009a8ac6b5da82dd17e26c7aeeaee
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404335"
---
# <a name="expose-applications-to-the-internet-using-application-gateway"></a>Verfügbarmachen von Anwendungen für das Internet mithilfe von Application Gateway

In diesem Artikel wird die Vorgehensweise beim Verfügbarmachen von Anwendungen für das Internet mithilfe von Application Gateway erläutert. Wenn eine Azure Spring Cloud-Dienstinstanz in Ihrem virtuellen Netzwerk bereitgestellt wird, kann auf Anwendungen in der Dienstinstanz nur im privaten Netzwerk zugegriffen werden. Um die Anwendungen über das Internet verfügbar zu machen, müssen Sie eine Integration in Azure Application Gateway durchführen.

## <a name="prerequisites"></a>Voraussetzungen

- [Azure CLI, Version 2.0.4 oder höher](/cli/azure/install-azure-cli).
- Eine Instanz des Azure Spring Cloud-Diensts, die in einem virtuellen Netzwerk bereitgestellt wird und auf deren Anwendung über das private Netzwerk mithilfe des `.private.azuremicroservices.io`-Standarddomänensuffixes zugegriffen werden kann. Weitere Informationen finden Sie unter [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](./how-to-deploy-in-azure-virtual-network.md).
- Eine benutzerdefinierte Domäne für den Zugriff auf die Anwendung.
- Ein in Key Vault gespeichertes Zertifikat, das mit der benutzerdefinierten Domäne übereinstimmt, die zur Einrichtung des HTTPS-Listeners verwendet werden soll. Weitere Informationen finden Sie unter [Tutorial: Importieren eines Zertifikats in Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md).

## <a name="configure-application-gateway-for-azure-spring-cloud"></a>Konfigurieren von Application Gateway für Azure Spring Cloud

Es wird empfohlen, dass der Domänenname, wie er vom Browser angezeigt wird, mit dem Hostnamen identisch ist, den Application Gateway verwendet, um Datenverkehr an das Azure Spring Cloud-Back-End weiterzuleiten. Diese Empfehlung bietet die beste Erfahrung bei der Verwendung von Application Gateway, um Anwendungen verfügbar zu machen, die in Azure Spring Cloud gehostet werden und sich in einem virtuellen Netzwerk befinden. Wenn sich die von Application Gateway verfügbar gemachte Domäne von der Domäne unterscheidet, die von Azure Spring Cloud akzeptiert wird, können Cookies und generierte Umleitungs-URLs (als Beispiel) beschädigt werden.

Verwenden Sie die folgenden Schritte, um Application Gateway vor Azure Spring Cloud zu konfigurieren.

1. Befolgen Sie die Anweisungen unter [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](./how-to-deploy-in-azure-virtual-network.md).
1. Rufen Sie ein Zertifikat für Ihre gewünschte Domäne ab, und speichern Sie es in Key Vault. Weitere Informationen finden Sie unter [Tutorial: Importieren eines Zertifikats in Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md).
1. Konfigurieren Sie eine benutzerdefinierte Domäne und ein entsprechendes Zertifikat aus Key Vault in einer App, die unter Azure Spring Cloud bereitgestellt wird. Weitere Informationen finden Sie unter [Tutorial: Zuordnen einer bereits vorhandenen benutzerdefinierten Domäne zu Azure Spring Cloud](./tutorial-custom-domain.md).
1. Stellen Sie Application Gateway in einem virtuellen Netzwerk bereit, das gemäß der folgenden Liste konfiguriert ist:
   - Verwenden Sie Azure Spring Cloud im Back-End-Pool, auf den die Domäne mit dem Suffix `private.azuremicroservices.io` verweist.
   - Schließen Sie einen HTTPS-Listener ein, der dasselbe Zertifikat von Key Vault verwendet.
   - Konfigurieren Sie das virtuelle Netzwerk mit HTTP-Einstellungen, die den auf Azure Spring Cloud konfigurierten benutzerdefinierten Domänennamen anstelle der Domäne mit dem Suffix `private.azuremicroservices.io` verwenden.
1. Konfigurieren Sie Ihr öffentliches DNS so, dass es auf Application Gateway verweist.

## <a name="define-variables"></a>Definieren von Variablen

Verwenden Sie als nächstes die folgenden Befehle, um die Variablen für die Ressourcengruppe und das virtuelle Netzwerk zu definieren, die Sie gemäß der Beschreibung unter [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](./how-to-deploy-in-azure-virtual-network.md) erstellt haben. Passen Sie die Werte basierend auf Ihrer realen Umgebung an. Wenn Sie `SPRING_APP_PRIVATE_FQDN` definieren, entfernen Sie `https://` aus dem URI.

```bash
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_CLOUD_NAME='name-of-spring-cloud-instance'
APPNAME='name-of-app-in-azure-spring-cloud'
SPRING_APP_PRIVATE_FQDN='$APPNAME.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Verwenden Sie den folgenden Befehl, um sich bei der Azure CLI anzumelden und Ihr aktives Abonnement auszuwählen.

```azurecli
az login
az account set --subscription $SUBSCRIPTION
```

## <a name="acquire-a-certificate"></a>Abrufen eines Zertifikats

### <a name="use-a-publicly-signed-certificate"></a>[Verwenden eines öffentlich signierten Zertifikats](#tab/public-cert)

Für Produktionsbereitstellungen verwenden Sie höchstwahrscheinlich ein öffentlich signiertes Zertifikat. Importieren Sie in diesem Fall das Zertifikat in Azure Key Vault. Weitere Informationen finden Sie unter [Tutorial: Importieren eines Zertifikats in Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md). Stellen Sie sicher, dass das Zertifikat die gesamte Zertifikatkette enthält.

### <a name="use-a-self-signed-certificate"></a>[Verwenden eines selbstsignierten Zertifikats](#tab/self-signed-cert)

Wenn Sie zu Test- oder Entwicklungszwecken ein selbstsigniertes Zertifikat benötigen, müssen Sie es erstellen. Sie müssen auch sicherstellen, dass die Liste der alternativen Antragstellernamen im Zertifikat den Domänennamen enthält, für den Sie die Anwendung verfügbar machen. Wenn Sie ein selbstsigniertes Zertifikat über Azure Key Vault erstellen, können Sie dies über das Azure-Portal vornehmen. Wenn Sie die Azure CLI verwenden, benötigen Sie alternativ eine JSON-Richtliniendatei.

Verwenden Sie den folgenden Befehl, um die Standardrichtlinie anzufordern:

```azurecli
az keyvault certificate get-default-policy
```

Passen Sie als nächstes den JSON-Code der Richtlinie an, wie im folgenden Beispiel gezeigt, und geben Sie dabei `subject` und `SubjectAlternativeNames` an:

```json
{
  // ...
    "subject": "C=US, ST=WA, L=Redmond, O=Contoso, OU=Contoso HR, CN=myapp.mydomain.com",
    "subjectAlternativeNames": {
        "dnsNames": [
            "myapp.mydomain.com",
            "*.myapp.mydomain.com"
        ],
        "emails": [
            "hello@contoso.com"
        ],
          "upns": []
      }
  // ...
}
```

Nachdem Sie die JSON-Richtliniendatei aktualisiert haben (siehe [Aktualisieren der Zertifikatrichtlinie](/rest/api/keyvault/update-certificate-policy/update-certificate-policy)), können Sie ein selbstsigniertes Zertifikat in Key Vault erstellen, indem Sie die folgenden Befehle verwenden:

```azurecli
KV_NAME='name-of-key-vault'
CERT_NAME_IN_KV='name-of-certificate-in-key-vault'

az keyvault certificate create \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --policy "$KV_CERT_POLICY"
```

---

## <a name="configure-the-public-domain-name-on-azure-spring-cloud"></a>Konfigurieren des öffentlichen Domänennamens unter Azure Spring Cloud

Der Datenverkehr wird mithilfe des öffentlichen Domänennamens in die unter Azure Spring Cloud bereitgestellte Anwendung gelangen. Um Ihre Anwendung so zu konfigurieren, dass sie auf diesen Hostnamen über HTTPS lauscht, verwenden Sie die folgenden Befehle, um Ihrer App eine benutzerdefinierte Domäne hinzuzufügen:

```azurecli
KV_NAME='name-of-key-vault'
KV_RG='resource-group-name-of-key-vault'
CERT_NAME_IN_ASC='name-of-certificate-in-Azure-Spring-Cloud'
CERT_NAME_IN_KV='name-of-certificate-with-intermediaries-in-key-vault'
DOMAIN_NAME=myapp.mydomain.com

# provide permissions to ASC to read the certificate from Key Vault:
VAULTURI=$(az keyvault show -n $KV_NAME -g $KV_RG --query properties.vaultUri -o tsv)

# get the object id for the Azure Spring Cloud Domain-Management Service Principal:
ASCDM_OID=$(az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId --output tsv)

# allow this Service Principal to read and list certificates and secrets from Key Vault:
az keyvault set-policy -g $KV_RG -n $KV_NAME  --object-id $ASCDM_OID --certificate-permissions get list --secret-permissions get list

# add custom domain name and configure TLS using the certificate:
az spring-cloud certificate add \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --name $CERT_NAME_IN_ASC \
    --vault-certificate-name $CERT_NAME_IN_KV \
    --vault-uri $VAULTURI
az spring-cloud app custom-domain bind \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --domain-name $DOMAIN_NAME \
    --certificate $CERT_NAME_IN_ASC \
    --app $APPNAME
```

> [!NOTE]
> Für Entwicklungs- und Testzwecke, wenn Sie den TLS-Abschluss am Application Gateway durchführen möchten, ist es nicht erforderlich, ein Zertifikat für Azure Spring Cloud zu konfigurieren oder Berechtigungen für Azure Spring Cloud zum Lesen von Key Vault bereitzustellen. Sie müssen lediglich den Domänennamen binden, wozu Sie den folgenden Befehl verwenden können:
>
> ```azurecli
> az spring-cloud app custom-domain bind \
>     --resource-group $RESOURCE_GROUP \
>     --service $SPRING_CLOUD_NAME \
>     --domain-name $DOMAIN_NAME \
>     --app $APPNAME
> ```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Das zu erstellende Azure Application Gateway tritt dem gleichen virtuellen Netzwerk wie die Azure Spring Cloud-Dienstinstanz bei, oder es erfolgt ein Peering. Erstellen Sie zunächst mithilfe von `az network vnet subnet create` ein neues Subnetz für das Application Gateway im virtuellen Netzwerk, und erstellen Sie außerdem mithilfe von `az network public-ip create` eine öffentliche IP-Adresse als Front-End der Application Gateway-Instanz.

```azurecli
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name $APPLICATION_GATEWAY_SUBNET_NAME \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --address-prefix $APPLICATION_GATEWAY_SUBNET_CIDR
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-a-managed-identity-for-application-gateway"></a>Erstellen einer verwalteten Identität für Application Gateway

Application Gateway muss auf Key Vault zugreifen können, um das Zertifikat zu lesen. Dazu verwendet es eine benutzerseitig zugewiesene [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md). Erstellen Sie die verwaltete Identität mit dem folgenden Befehl:

```azurecli
APPGW_IDENTITY_NAME='name-for-appgw-managed-identity'
az identity create \
    --resource-group $RESOURCE_GROUP \
    --name $APPGW_IDENTITY_NAME
```

Rufen Sie dann die „objectId“ für die verwaltete Identität ab, da diese später verwendet wird, um Rechte für den Zugriff auf das Zertifikat in Key Vault zu erteilen:

```azurecli
APPGW_IDENTITY_CLIENTID=$(az identity show --resource-group $RESOURCE_GROUP --name $APPGW_IDENTITY_NAME --query clientId --output tsv)
APPGW_IDENTITY_OID=$(az ad sp show --id $APPGW_IDENTITY_CLIENTID --query objectId --output tsv)
```

## <a name="set-policy-on-key-vault"></a>Festlegen der Richtlinie für Key Vault

Konfigurieren Sie Key Vault mit dem folgenden Befehl, damit die verwaltete Identität für Application Gateway auf das in Key Vault gespeicherte Zertifikat zugreifen kann:

```azurecli
az keyvault set-policy \
    --name $KV_NAME \
    --resource-group $KV_RG \
    --object-id $APPGW_IDENTITY_OID \
    --secret-permissions get list \
    --certificate-permissions get list
```

## <a name="create-application-gateway"></a>Erstellen eines Application Gateways

Erstellen Sie ein Anwendungsgateway mit `az network application-gateway create`, und geben Sie den privaten vollqualifizierten Domänennamen (FQDN) Ihrer Anwendung als Server im Back-End-Pool an. Stellen Sie sicher, dass Sie die benutzerseitig zugewiesene verwaltete Identität verwenden und auf das Zertifikat in Key Vault verweisen, indem Sie die Geheimnis-ID des Zertifikats verwenden. Aktualisieren Sie dann die HTTP-Einstellung mit `az network application-gateway http-settings update`, um den öffentlichen Hostnamen zu verwenden.

```azurecli
APPGW_NAME='name-for-application-gateway'

KEYVAULT_SECRET_ID_FOR_CERT=$(az keyvault certificate show --name $CERT_NAME_IN_KV --vault-name $KV_NAME --query sid --output tsv)

az network application-gateway create \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --capacity 2 \
    --sku Standard_v2 \
    --frontend-port 443 \
    --http-settings-cookie-based-affinity Disabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --subnet $APPLICATION_GATEWAY_SUBNET_NAME \
    --servers $SPRING_APP_PRIVATE_FQDN \
    --key-vault-secret-id $KEYVAULT_SECRET_ID_FOR_CERT \
    --identity $APPGW_IDENTITY_NAME
```

Die Erstellung des Anwendungsgateways in Azure kann bis zu 30 Minuten in Anspruch nehmen.

>[!NOTE]
> Für Entwicklungs- und Testzwecke, wenn der TLS-Abschluss bei Application Gateway erfolgen soll, ändern Sie `http-settings-port` in `80` und `http-settings-protocol` in `Https`, und folgen Sie den nachfolgenden Anweisungen für „Verwenden eines öffentlich signierten Zertifikats“.

### <a name="update-http-settings-to-use-the-domain-name-towards-the-backend"></a>Aktualisieren der HTTP-Einstellungen zur Verwendung des Domänennamens für das Back-End

#### <a name="use-a-publicly-signed-certificate"></a>[Verwenden eines öffentlich signierten Zertifikats](#tab/public-cert-2)

Aktualisieren Sie die HTTP-Einstellungen so, dass der öffentliche Domänenname als Hostname anstelle der Domäne mit dem Suffix „.private.azuremicroservices.io“ verwendet wird, um den Datenverkehr an Azure Spring Cloud zu senden.

```azurecli
az network application-gateway http-settings update \
    --resource-group $RESOURCE_GROUP \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings
```

#### <a name="use-a-self-signed-certificate"></a>[Verwenden eines selbstsignierten Zertifikats](#tab/self-signed-cert-2)

Aktualisieren Sie die HTTP-Einstellungen so, dass der öffentliche Domänenname als Hostname anstelle der Domäne mit dem Suffix „.private.azuremicroservices.io“ verwendet wird, um den Datenverkehr an Azure Spring Cloud zu senden. Da ein selbstsigniertes Zertifikat verwendet wird, muss es in den HTTP-Einstellungen von Application Gateway in die Positivliste aufgenommen werden.

Um das Zertifikat in die Positivliste aufzunehmen, rufen Sie zunächst den öffentlichen Teil des Zertifikats aus Key Vault mithilfe des folgenden Befehls ab:

```azurecli
az keyvault certificate download \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --file ./selfsignedcert.crt \
    --encoding DER
```

Laden Sie es dann mit dem folgenden Befehl in Application Gateway hoch:

```azurecli
az network application-gateway root-cert create \
    --resource-group $RG \
    --cert-file ./selfsignedcert.crt \
    --gateway-name $APPGW_NAME \
    --name MySelfSignedTrustedRootCert
```

Nun können Sie die HTTP-Einstellungen aktualisieren, um dem neuen (selbstsignierten) Stammzertifikat zu vertrauen, indem Sie den folgenden Befehl verwenden:

```azurecli
az network application-gateway http-settings update \
    --resource-group $RG \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings \
    --root-certs MySelfSignedTrustedRootCert
```

---

### <a name="check-the-deployment-of-application-gateway"></a>Überprüfen der Bereitstellung von Application Gateway

Überprüfen Sie nach der Erstellung die Back-End-Integrität mit dem folgenden Befehl. Mit der Ausgabe dieses Befehls können Sie bestimmen, ob das Anwendungsgateway Ihre Anwendung über seinen privaten FQDN erreicht.

```azurecli
az network application-gateway show-backend-health \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP
```

Die Ausgabe gibt den fehlerfreien Status des Back-End-Pools an, wie im folgenden Beispiel gezeigt:

```output
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="configure-dns-and-access-the-application"></a>Konfigurieren von DNS und Zugreifen auf die Anwendung

Konfigurieren Sie nun das öffentliche DNS so, dass es mithilfe eines CNAME- oder A-Eintrags auf Application Gateway verweist. Sie können die öffentliche Adresse für Application Gateway mithilfe des folgenden Befehls ermitteln:

```azurecli
az network public-ip show \
    --resource-group $RESOURCE_GROUP \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --query [ipAddress] \
    --output tsv
```

Sie können jetzt mithilfe des öffentlichen Domänennamens auf die Anwendung zugreifen.

## <a name="see-also"></a>Weitere Informationen

- [Problembehandlung für Azure Spring Cloud im VNET](./troubleshooting-vnet.md)
- [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET](./vnet-customer-responsibilities.md)
