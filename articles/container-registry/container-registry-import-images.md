---
title: Importieren von Containerimages
description: Importieren von Containerimages in eine Azure-Containerregistrierung mithilfe von Azure-APIs, ohne dass Docker-Befehle ausgeführt werden müssen
ms.topic: article
ms.date: 09/13/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14d2008599c0740bd36108760e3d4e50054b5f4d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582267"
---
# <a name="import-container-images-to-a-container-registry"></a>Importieren von Containerimages in eine Containerregistrierung

Sie können Containerimages einfach in eine Azure-Containerregistrierung importieren (kopieren), ohne Docker-Befehle verwenden zu müssen. Sie können beispielsweise Images aus einer Entwicklungsregistrierung in eine Produktionsregistrierung importieren oder Basisimages aus einer öffentlichen Registrierung kopieren.

Azure Container Registry ermöglicht einige allgemeine Szenarien zum Kopieren von Images und anderer Artefakte aus einer vorhandenen Registrierung:

* Importieren von Images aus einer öffentlichen Registrierung

* Importieren von Images oder OCI-Artefakten einschließlich Helm 3-Diagrammen aus einer anderen Azure Container Registry-Instanz in demselben oder einem anderen Azure-Abonnement oder -Mandanten

* Importieren aus einer Azure-fremden privaten Containerregistrierung

Der Imageimport in eine Azure-Containerregistrierung bietet gegenüber der Verwendung von Docker CLI-Befehlen die folgenden Vorteile:

* Für Ihre Clientumgebung ist keine lokale Docker-Installation erforderlich. Daher können Sie unabhängig vom unterstützten Betriebssystemtyp beliebige Containerimages importieren.

* Wenn Sie Images mit mehreren Architekturen (etwa offizielle Docker-Images) importieren, werden Images für alle Architekturen und Plattformen kopiert, die in der Manifestliste angegeben sind.

* Der Zugriff auf die Zielregistrierung muss nicht über den öffentlichen Endpunkt der Registrierung erfolgen.

## <a name="limitations"></a>Einschränkungen

* Die maximale Anzahl von Manifesten für ein importiertes Image beträgt 50.
* Die maximale Größe der Ebene für ein aus einer öffentlichen Registrierung importiertes Image beträgt 2 GiB.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Damit Sie Containerimages importieren können, muss für diesen Artikel die Azure-Befehlszeilenschnittstelle in Azure Cloud Shell oder lokal (Version 2.0.55 oder höhere Version empfohlen) ausgeführt werden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Damit Sie Containerimages importieren können, muss für diesen Artikel die Azure PowerShell in Azure Cloud Shell oder lokal (Version 5.9.0 oder höher empfohlen) ausgeführt werden. Führen Sie `Get-InstalledModule -Name Az` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder eine Aktualisierung vornehmen müssen, beachten Sie den Abschnitt [Installieren des Azure Az PowerShell-Moduls][install-the-azure-az-powershell-module].

---

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

> [!IMPORTANT]
> Änderungen am Imageimport zwischen zwei Azure Container Registry-Instanzen zum Januar 2021:
> * Für den Import in eine Azure Container Registry-Instanz mit Netzwerkeinschränkungen oder aus dieser muss die eingeschränkte Registrierung das Umgehen des Netzwerks für den [**Zugriff durch vertrauenswürdige Dienste erlauben**](allow-access-trusted-services.md). Standardmäßig ist die Einstellung aktiviert und ermöglicht den Import. Wenn die Einstellung in einer neu erstellten Registrierung mit einem privaten Endpunkt oder mit Firewallregeln für die Registrierung nicht aktiviert ist, tritt beim Import ein Fehler auf.
> * Bei einer vorhandenen Azure Container Registry-Instanz mit Netzwerkeinschränkungen, die als Importquelle oder -ziel verwendet wird, ist die Aktivierung dieser Netzwerksicherheitsfunktion optional, wird jedoch empfohlen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie nicht bereits über eine Azure-Containerregistrierung verfügen, erstellen Sie eine Registrierung. Die hierzu erforderlichen Schritte finden Sie unter [Schnellstart: Erstellen einer privaten Containerregistrierung mit der Azure CLI](container-registry-get-started-azure-cli.md).

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Wenn Sie nicht bereits über eine Azure-Containerregistrierung verfügen, erstellen Sie eine Registrierung. Um die Schritte zu sehen, siehe [Schnellstart: Wie man eine private Containerregistrierung mithilfe von Azure PowerShell erstellt](container-registry-get-started-powershell.md).

---

Um ein Image in eine Azure-Containerregistrierung importieren zu können, muss Ihre Identität über Schreibberechtigungen für die Zielregistrierung (mindestens die Rolle „Mitwirkender“ oder eine benutzerdefinierte Rolle, die die importImage-Aktion zulässt) verfügen. Informationen hierzu finden Sie unter [Azure Container Registry: Rollen und Berechtigungen](container-registry-roles.md#custom-roles).

## <a name="import-from-a-public-registry"></a>Importieren aus einer öffentlichen Registrierung

### <a name="import-from-docker-hub"></a>Importieren aus Docker Hub

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie beispielsweise den Befehl [az acr import][az-acr-import], um das Image `hello-world:latest` mit mehreren Architekturen aus Docker Hub in eine Registrierung namens *myregistry* zu importieren. `hello-world` ist ein offizielles Image von Docker Hub und befindet sich daher im `library`-Standardrepository. Fügen Sie den Repositorynamen und optional ein Tag in den Wert des Imageparameters `--source` ein. (Optional können Sie ein Image anhand seines Manifest-Digest anstelle seines Tags bestimmen. Damit wird eine bestimmte Version eines Images garantiert.)

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Mit dem Befehl `az acr repository show-manifests` können Sie überprüfen, ob diesem Image mehrere Manifeste zugeordnet sind:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Wenn Sie ein [Docker Hub-Konto](https://www.docker.com/pricing) haben, sollten Sie beim Importieren eines Images aus Docker Hub diese Anmeldeinformationen verwenden. Übergeben Sie den Docker Hub-Benutzernamen mit dem zugehörigen Kennwort oder ein [persönliches Zugriffstoken](https://docs.docker.com/docker-hub/access-tokens/) als Parameter an `az acr import`. Im folgenden Beispiel wird ein öffentliches Image aus dem Repository `tensorflow` mithilfe von Docker Hub-Anmeldeinformationen in Docker Hub importiert:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Verwenden Sie beispielsweise den Befehl [Import-AzContainerRegistryImage][import-azcontainerregistryimage], um ein Multi-Architektur-`hello-world:latest` Image aus dem Docker Hub hin zu einer Registrierung namens *myregistry* zu importieren. `hello-world` ist ein offizielles Image von Docker Hub und befindet sich daher im `library`-Standardrepository. Fügen Sie den Repository-Namen und optional ein Tag in den Wert des Parameters `-SourceImage` ein. (Optional können Sie ein Image anhand seines Manifest-Digest anstelle seines Tags bestimmen. Damit wird eine bestimmte Version eines Images garantiert.)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage library/hello-world:latest
```

Wenn Sie das Cmdlet `Get-AzContainerRegistryManifest` ausführen, können Sie überprüfen, ob diesem Image mehrere Manifeste zugeordnet sind:

```azurepowershell
Get-AzContainerRegistryManifest -RepositoryName library/hello-world -RegistryName myregistry
```

Wenn Sie ein [Docker Hub-Konto](https://www.docker.com/pricing) haben, sollten Sie beim Importieren eines Images aus Docker Hub diese Anmeldeinformationen verwenden. Übergeben Sie den Docker Hub-Benutzernamen mit dem zugehörigen Kennwort oder ein [persönliches Zugriffstoken](https://docs.docker.com/docker-hub/access-tokens/) als Parameter an `Import-AzContainerRegistryImage`. Im folgenden Beispiel wird ein öffentliches Image aus dem Repository `tensorflow` mithilfe von Docker Hub-Anmeldeinformationen in Docker Hub importiert:

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage tensorflow/tensorflow:latest-gpu -Username <Docker Hub user name> -Password <Docker Hub token>
```

---

### <a name="import-from-microsoft-container-registry"></a>Importieren aus der Microsoft-Containerregistrierung

Importieren Sie beispielsweise das Windows Server Core-Image `ltsc2019` aus dem Repository `windows` in die Microsoft Container Registry.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mcr.microsoft.com -SourceImage windows/servercore:ltsc2019
```

---

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importieren von Images aus einer Azure-Containerregistrierung im selben AD-Mandanten

Mithilfe von integrierten Azure Active Directory-Berechtigungen können Sie ein Image aus einer Azure-Containerregistrierung im selben AD-Mandanten importieren.

* Ihre Identität muss über Azure Active Directory-Berechtigungen zum Lesen aus der Quellregistrierung (Rolle „Leser“) und zum Importieren in die Zielregistrierung (Rolle „Mitwirkender“ bzw. [benutzerdefinierte Rolle](container-registry-roles.md#custom-roles), die die importImage-Aktion zulässt) verfügen.

* Die Registrierung kann sich in dem gleichen oder einem anderen Azure-Abonnement im gleichen Active Directory-Mandanten befinden.

* Der [öffentliche Zugriff](container-registry-access-selected-networks.md#disable-public-network-access) auf die Quellregistrierung kann deaktiviert werden. Wenn der öffentliche Zugriff deaktiviert ist, geben Sie die Quellregistrierung nach Ressourcen-ID anstelle des Servernamens der Registrierungsanmeldung an.

* Wenn die Quell- oder Zielregistrierung über einen privaten Endpunkt verfügt oder Registrierungsfirewallregeln angewandt werden, stellen Sie sicher, dass die eingeschränkte Registrierung den Zugriff auf das Netzwerk [für vertrauenswürdige Dienste zulässt](allow-access-trusted-services.md).

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importieren aus einer Registrierung im gleichen Abonnement

Importieren Sie beispielsweise das Image `aci-helloworld:latest` aus der Quellregistrierung *mysourceregistry* in *myregistry* im gleichen Azure-Abonnement.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Das folgende Beispiel importiert das `aci-helloworld:latest`-Image in *myregistry* aus einer Quellregistrierung *mysourceregistry*, in der der Zugriff auf den öffentlichen Endpunkt der Registrierung deaktiviert ist. Geben Sie die Ressourcen-ID der Quellregistrierung mit dem Parameter `--registry` an. Beachten Sie, dass der Parameter `--source` nur das Quellrepository und Tag, aber nicht den Namen des Anmeldeservers für die Registrierung angibt.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Das folgende Beispiel importiert ein Image nicht anhand des Tags, sondern anhand des Manifest-Digest (SHA-256-Hash, dargestellt als `sha256:...`):

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mysourceregistry.azurecr.io -SourceImage aci-helloworld:latest
```

Das folgende Beispiel importiert das `aci-helloworld:latest`-Image in *myregistry* aus einer Quellregistrierung *mysourceregistry*, in der der Zugriff auf den öffentlichen Endpunkt der Registrierung deaktiviert ist. Geben Sie die Ressourcen-ID der Quellregistrierung mit dem Parameter `--registry` an. Beachten Sie, dass der Parameter `--source` nur das Quellrepository und Tag, aber nicht den Namen des Anmeldeservers für die Registrierung angibt.

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryResourceId '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry' -SourceImage aci-helloworld:latest
```

Das folgende Beispiel importiert ein Image nicht anhand des Tags, sondern anhand des Manifest-Digest (SHA-256-Hash, dargestellt als `sha256:...`):

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mysourceregistry.azurecr.io -SourceImage aci-helloworld@sha256:123456abcdefg
```

---

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importieren aus einer Registrierung in einem anderen Abonnement

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Im folgenden Beispiel befindet sich *mysourceregistry* im gleichen Active Directory-Mandanten in einem anderen Abonnement als *myregistry*. Geben Sie die Ressourcen-ID der Quellregistrierung mit dem Parameter `--registry` an. Beachten Sie, dass der Parameter `--source` nur das Quellrepository und Tag, aber nicht den Namen des Anmeldeservers für die Registrierung angibt.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Im folgenden Beispiel befindet sich *mysourceregistry* im gleichen Active Directory-Mandanten in einem anderen Abonnement als *myregistry*. Geben Sie die Ressourcen-ID der Quellregistrierung mit dem Parameter `--registry` an. Beachten Sie, dass der Parameter `--source` nur das Quellrepository und Tag, aber nicht den Namen des Anmeldeservers für die Registrierung angibt.

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryResourceId '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry' -SourceImage aci-helloworld:latest
```

---

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importieren aus einer Registrierung mit Dienstprinzipal-Anmeldeinformationen

Sie können für den Import aus einer Registrierung, auf die Sie nicht mit integrierten Active Directory-Berechtigungen zugreifen können, Anmeldeinformationen für einen Dienstprinzipal für die Quellregistrierung verwenden (sofern verfügbar). Geben Sie die App-ID und das Kennwort eines Active Directory-[Dienstprinzipals](container-registry-auth-service-principal.md) an, der über ACRPull-Zugriff auf die Quellregistrierung verfügt. Die Verwendung eines Dienstprinzipals empfiehlt sich für Buildsysteme und andere unbeaufsichtigte Systeme, die Images in Ihre Registrierung importieren müssen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Username <SP_App_ID> -Password <SP_Passwd>
```

---

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importieren von Images aus einer Azure-Containerregistrierung in einem anderen AD-Mandanten

Wenn Sie Images aus einer Azure-Containerregistrierung in einem anderen Azure Active Directory-Mandanten importieren möchten, geben Sie die Quellregistrierung über den Anmeldeservernamen an sowie die Anmeldeinformationen für Pullzugriff auf die Registrierung.

### <a name="cross-tenant-import-with-username-and-password"></a>Mandantenübergreifender Import mit Benutzername und Kennwort
Verwenden Sie z. B. ein [Token mit Repositorygültigkeitsbereich](container-registry-repository-scoped-permissions.md) und ein Kennwort oder die App-ID und das Kennwort für einen Active Directory-[Dienstprinzipal](container-registry-auth-service-principal.md), der über ACRPull-Zugriff auf die Quellregistrierung verfügt.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Username <SP_App_ID> -Password <SP_Passwd>
```

---

### <a name="cross-tenant-import-with-access-token"></a>Mandantenübergreifender Import mit Zugriffstoken

Um mithilfe einer Identität im Quellmandanten, die über Registrierungsberechtigungen verfügt, auf die Quellregistrierung zuzugreifen, können Sie ein Zugriffstoken abrufen:

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
# Login to Azure CLI with the identity, for example a user-assigned managed identity
az login --identity --username <identity_ID>

# Get access token returned by `az account get-access-token`
az account get-access-token
```

Übergeben Sie im Zielmandanten das Zugriffstoken als Kennwort an den Befehl `az acr import`. Die Quellregistrierung wird über den Namen des Anmeldeservers angegeben. Beachten Sie, dass in diesem Befehl kein Benutzername erforderlich ist:

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --password <access-token>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
# Login to Azure PowerShell with the identity, for example a user-assigned managed identity
Connect-AzAccount -Identity -AccountId <identity_ID>

# Get access token returned by `Get-AzAccessToken`
Get-AzAccessToken
```

Übergeben Sie im Zielmandanten das Zugriffstoken als Kennwort an das Cmdlet `Import-AzContainerRegistryImage`. Die Quellregistrierung wird über den Namen des Anmeldeservers angegeben. Beachten Sie, dass in diesem Befehl kein Benutzername erforderlich ist:

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Password <access-token>
```

---

## <a name="import-from-a-non-azure-private-container-registry"></a>Importieren aus einer Azure-fremden privaten Containerregistrierung

Importieren Sie ein Image aus einer privaten Registrierung außerhalb von Azure, indem Sie Anmeldeinformationen angeben, die den Pullzugriff auf die Registrierung ermöglichen. Rufen Sie beispielsweise ein Image per Pull aus einer privaten Docker-Registrierung ab:

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io/sourcerepo -SourceImage sourcerrepo:tag -Username <username> -Password <password>
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Containerimages aus einer öffentlichen Registrierung oder einer anderen privaten Registrierung in eine Azure-Containerregistrierung importieren.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

* Informationen zu weiteren Optionen beim Importieren von Images finden Sie in der Befehlsreferenz für [az acr import][az-acr-import].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* Für zusätzliche Möglichkeiten zum Importieren von Images siehe die Cmdlet-Referenz zu [Import-AzContainerRegistryImage][import-azcontainerregistryimage].

---

* Der Imageimport kann Ihnen helfen, Inhalte in eine Containerregistrierung in einer anderen Azure-Region, einem anderen Abonnement oder Azure AD-Mandanten zu verschieben. Weitere Informationen finden Sie unter [Manuelles Verschieben einer Containerregistrierung in eine andere Region](manual-regional-move.md).

* Erfahren Sie, wie Sie den [Export von Artefakten aus einer Containerregistrierung mit Netzwerkeinschränkungen deaktivieren](data-loss-prevention.md).


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
[install-the-azure-az-powershell-module]: /powershell/azure/install-az-ps
[import-azcontainerregistryimage]: /powershell/module/az.containerregistry/import-azcontainerregistryimage
