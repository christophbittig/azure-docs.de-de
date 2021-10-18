---
title: Konfigurieren von AKS-Knoten (Azure Kubernetes Service) mit einem HTTP-Proxy
description: Verwenden des HTTP-Proxykonfigurationsfeatures für AKS-Knoten (Azure Kubernetes Service).
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: 19a1392756596a1cbfe7000ebd9c7013c053c153
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856921"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>HTTP-Proxyunterstützung in Azure Kubernetes Service (Vorschau)

AKS-Cluster (Azure Kubernetes Service) verfügen unabhängig davon, ob sie in einem verwalteten oder benutzerdefinierten virtuellen Netzwerk bereitgestellt werden, über bestimmte ausgehende Abhängigkeiten, die für eine ordnungsgemäße Funktion erforderlich sind. In Umgebungen, in denen Internetzugriff über HTTP-Proxys erfolgen musste, war dies bisher ein Problem. Knoten hatten keine Möglichkeit, Bootstrapping für die Konfiguration, Umgebungsvariablen und Zertifikate auszuführen, die für den Zugriff auf Internetdienste erforderlich sind.

Dieses Feature fügt AKS-Clustern HTTP-Proxyunterstützung hinzu, wodurch eine einfache Schnittstelle verfügbar wird, die Clusteroperatoren verwenden können, um für AKS benötigten Netzwerkdatenverkehr in von Proxys abhängigen Umgebungen zu schützen.

Einige komplexere Lösungen erfordern möglicherweise die Erstellung einer Vertrauenskette, um sichere Kommunikation über das Netzwerk einzurichten. Das Feature ermöglicht auch die Installation einer vertrauenswürdigen Zertifizierungsstelle auf den Knoten als Teil des Bootstrappingvorgangs eines Clusters.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>Einschränkungen und andere Details

Folgende Szenarios werden **nicht** unterstützt:
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

Die Verwendung von AKS mit einem HTTP-Proxy erfolgt bei der Clustererstellung mit dem Befehl [az aks create][az-aks-create] und der Übergabe der Konfiguration als JSON- oder YAML-Datei.

Das Schema für die Konfigurationsdatei sieht wie folgt aus:

```json
"httpProxyConfig": {
    "httpProxy": "string",
    "httpsProxy": "string",
    "noProxy": [
        "string"
    ],
    "trustedCa": "string"
}
```

Erstellen Sie eine Datei, und geben Sie Werte für *httpProxy*, *httpsProxy* und *noProxy* an. Wenn ihre Umgebung dies erfordert, geben Sie auch einen Wert für *trustedCa* an. Stellen Sie als Nächstes einen Cluster bereit, und übergeben Sie Ihren Dateinamen über das `proxy-configuration-file`-Flag.

```azurecli
az aks create -n $clusterName -g $resourceGroup --proxy-configuration-file aks-proxy-config.json
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
az aks update -n $clusterName -g $resourceGroup --proxy-configuration-file aks-proxy-config-2.json
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