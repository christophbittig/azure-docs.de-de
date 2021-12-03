---
title: Konfigurieren von AKS-Knoten (Azure Kubernetes Service) mit einem HTTP-Proxy
description: Verwenden des HTTP-Proxykonfigurationsfeatures für AKS-Knoten (Azure Kubernetes Service).
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: 81631bfea3cc55b52dc95a81cb17c3420cf9638d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346109"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>HTTP-Proxyunterstützung in Azure Kubernetes Service (Vorschau)

AKS-Cluster (Azure Kubernetes Service) verfügen unabhängig davon, ob sie in einem verwalteten oder benutzerdefinierten virtuellen Netzwerk bereitgestellt werden, über bestimmte ausgehende Abhängigkeiten, die für eine ordnungsgemäße Funktion erforderlich sind. In Umgebungen, in denen Internetzugriff über HTTP-Proxys erfolgen musste, war dies bisher ein Problem. Knoten hatten keine Möglichkeit, Bootstrapping für die Konfiguration, Umgebungsvariablen und Zertifikate auszuführen, die für den Zugriff auf Internetdienste erforderlich sind.

Dieses Feature fügt AKS-Clustern HTTP-Proxyunterstützung hinzu, wodurch eine einfache Schnittstelle verfügbar wird, die Clusteroperatoren verwenden können, um für AKS benötigten Netzwerkdatenverkehr in von Proxys abhängigen Umgebungen zu schützen.

Einige komplexere Lösungen erfordern möglicherweise die Erstellung einer Vertrauenskette, um sichere Kommunikation über das Netzwerk einzurichten. Das Feature ermöglicht auch die Installation einer vertrauenswürdigen Zertifizierungsstelle auf den Knoten als Teil des Bootstrappingvorgangs eines Clusters.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>Einschränkungen und andere Details

Folgende Szenarios werden **nicht** unterstützt:
- Überwachungs-Add-On
- Verschiedene Proxykonfigurationen pro Knotenpool
- Aktualisieren von Proxyeinstellungen nach der Clustererstellung
- Benutzer-/Kennwortauthentifizierung
- Benutzerdefinierte Zertifizierungsstellen für die API-Serverkommunikation
- Windows-basierte Cluster
- Knotenpools, die VM-Verfügbarkeitsgruppen (VMAS) verwenden

*HttpProxy*, *httpsProxy* und *trustedCa* weisen standardmäßig keinen Wert auf.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Die [Azure CLI](/cli/azure/install-azure-cli) muss installiert sein.

### <a name="install-the-aks-preview-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle `aks-preview`

Sie benötigen außerdem die *aks-preview* Azure CLI-Erweiterung Version 0.5.25 oder höher. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-httpproxyconfigpreview-preview-feature"></a>Registrieren der Previewfunktion `HTTPProxyConfigPreview`

Sie müssen darüber hinaus das Featureflag `HTTPProxyConfigPreview` in Ihrem Abonnement aktivieren, um dieses Feature zu verwenden.

Registrieren Sie das Featureflag `HTTPProxyConfigPreview` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "HTTPProxyConfigPreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/HTTPProxyConfigPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configuring-an-http-proxy-using-azure-cli"></a>Konfigurieren eines HTTP-Proxys mit der Azure CLI 

Die Verwendung von AKS mit einem HTTP-Proxy erfolgt bei der Erstellung des Clusters mit dem Befehl [az aks create][az-aks-create] und der Übergabe der Konfiguration als JSON-Datei.

Das Schema für die Konfigurationsdatei sieht wie folgt aus:

```json
{
  "httpProxy": "string",
  "httpsProxy": "string",
  "noProxy": [
    "string"
  ],
  "trustedCa": "string"
}
```

`httpProxy`: Eine Proxy-URL, die zum Erstellen von HTTP-Verbindungen außerhalb des Clusters verwendet wird. Das URL-Schema muss `http` sein.
`httpsProxy`: Eine Proxy-URL, die zum Erstellen von HTTPS-Verbindungen außerhalb des Clusters verwendet wird. Wenn dieses nicht angegeben ist, wird `httpProxy` sowohl für HTTP- als auch für HTTPS-Verbindungen verwendet.
`noProxy`: Eine Liste von Zieldomänennamen, Domänen, IP-Adressen oder anderen Netzwerk-CIDRs, um Proxying auszuschließen.
`trustedCa`: Eine Zeichenfolge, die den `base64 encoded` alternativen Inhalt des Zertifizierungsstellenzertifikats enthält. Vorerst wird nur das `PEM`-Format unterstützt. Außerdem bemerkenswert ist, dass das Zertifikat aus Gründen der Kompatibilität mit Go-basierten Komponenten, die Teil des k8s-Systems sind, anstelle der veralteten allgemeinen Namenszertifikaten `Subject Alternative Names(SANs)` unterstützen MUSS.

Beispieleingabe: Beachten Sie, dass das Zertifizierungsstellenzertifikat die Base64-codierte Zeichenfolge des Zertifikatinhalts im PEM-Format sein sollte.

```json
{
  "httpProxy": "http://myproxy.server.com:8080/", 
  "httpsProxy": "https://myproxy.server.com:8080/", 
  "noProxy": [
    "localhost",
    "127.0.0.1"
  ],
  "trustedCA": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUgvVENDQmVXZ0F3SUJB...b3Rpbk15RGszaWFyCkYxMFlscWNPbWVYMXVGbUtiZGkvWG9yR2xrQ29NRjNURHg4cm1wOURCaUIvCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0="
}
```

Erstellen Sie eine Datei, und geben Sie Werte für *httpProxy*, *httpsProxy* und *noProxy* an. Wenn ihre Umgebung dies erfordert, geben Sie auch einen Wert für *trustedCa* an. Stellen Sie als Nächstes einen Cluster bereit, und übergeben Sie Ihren Dateinamen über das `http-proxy-config`-Flag.

```azurecli
az aks create -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config.json
```

Ihr Cluster wird mit dem HTTP-Proxy initialisiert, der auf den Knoten konfiguriert ist.

## <a name="configuring-an-http-proxy-using-azure-resource-manager-arm-templates"></a>Konfigurieren eines HTTP-Proxys mithilfe von ARM-Vorlagen (Azure Resource Manager)

Die Bereitstellung eines AKS-Clusters mit einem HTTP-Proxy, der über eine ARM-Vorlage konfiguriert ist, ist einfach. Das gleiche Schema, das für die CLI-Bereitstellung verwendet wird, ist in der `Microsoft.ContainerService/managedClusters`-Definition unter den Eigenschaften vorhanden:

```json
"properties": {
    ...,
    "httpProxyConfig": {
        "httpProxy": "string",
        "httpsProxy": "string",
        "noProxy": [
            "string"
        ],
        "trustedCa": "string"
    }
}
```

Geben Sie in Ihrer Vorlage Werte für *httpProxy*, *httpsProxy* und *noProxy* an. Geben Sie bei Bedarf auch einen Wert für *trustedCa* an. Stellen Sie die Vorlage bereit, und Ihr Cluster sollte mit dem HTTP-Proxy initialisiert werden, der auf den Knoten konfiguriert ist.

## <a name="handling-ca-rollover"></a>Behandeln des Zertifizierungsstellenrollovers

Die Werte für *httpProxy*, *httpsProxy* und *noProxy* können nach der Clustererstellung nicht mehr geändert werden. Zur Unterstützung paralleler Zertifizierungsstellenzertifikate kann der Wert für *trustedCa* jedoch geändert und mit dem Befehl [az aks update][az-aks-update] auf den Cluster angewendet werden.

Angenommen, es wurde beispielsweise eine neue Datei mit der base64-codierten Zeichenfolge des neuen Zertifizierungsstellenzertifikats mit dem Namen *aks-proxy-config-2.json* erstellt. Dann wird der Cluster mit der folgenden Aktion aktualisiert:

```azurecli
az aks update -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config-2.json
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu den Netzwerkanforderungen von AKS-Clustern finden Sie unter [Steuern des ausgehenden Datenverkehrs für Clusterknoten in AKS][aks-egress].


<!-- LINKS - internal -->
[aks-egress]: ./limit-egress-traffic.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az-extension-update
