---
title: Rotieren der Anmeldeinformationen eines Dienstprinzipals für einen ARO-Cluster (Azure Red Hat OpenShift)
description: Hier erfahren Sie, wie Sie die Anmeldeinformationen eines Dienstprinzipals in Azure Red Hat OpenShift (ARO) rotieren.
author: swiencki
ms.author: b-swiencki
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 05/31/2021
ms.openlocfilehash: 286126bdd3cfcc5139549d1137431c8016c99472
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090869"
---
# <a name="rotate-service-principal-credentials-for-your-azure-red-hat-openshift-aro-cluster"></a>Rotieren der Anmeldeinformationen eines Dienstprinzipals für Ihren ARO-Cluster (Azure Red Hat OpenShift)
Der Artikel enthält die erforderlichen Informationen zum Rotieren der Anmeldeinformationen eines Dienstprinzipals in Azure Red Hat OpenShift-Clustern ([ARO](https://github.com/Azure/ARO-RP)).

## <a name="before-you-begin"></a>Vorbereitung
In diesem Artikel wird davon ausgegangen, dass ein ARO-Cluster mit den neuesten Updates vorhanden ist.

Zum Rotieren der Anmeldeinformationen eines Dienstprinzipals in einem ARO-Cluster ist mindestens Azure CLI 2.24.0 erforderlich.

Führen Sie zum Überprüfen der Azure CLI-Version Folgendes aus:
```azurecli-interactive
# Azure CLI version
az --version
```
Befolgen Sie zum Installieren oder Aktualisieren der Azure CLI die Anweisungen unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

In den folgenden Anweisungen wird die Bash-Syntax verwendet.

## <a name="service-principal-credential-rotation"></a>Rotation der Anmeldeinformationen eines Dienstprinzipals
>[!IMPORTANT]
>  Die Rotation der Anmeldeinformationen eines Dienstprinzipals kann je nach Clusterzustand mehr als zwei Stunden dauern.

Für die Rotation der Anmeldeinformationen eines Dienstprinzipals gibt es zwei Methoden:
 - [Automatisierte Rotation der Anmeldeinformationen eines Dienstprinzipals](#Automated-Service-Principal-Credential-Rotation)
 - [Rotation der Anmeldeinformationen eines Dienstprinzipals mit den vom Benutzer bereitgestellten Elementen „client-id“ und „client-secret“](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation)

### <a name="automated-service-principal-credential-rotation"></a>Automatisierte Rotation der Anmeldeinformationen eines Dienstprinzipals <a id="Automated-Service-Principal-Credential-Rotation"></a>
>[!IMPORTANT]
>  Bei der automatisierten Rotation der Anmeldeinformationen eines Dienstprinzipals muss der ARO-Cluster mit der Azure CLI-Version 2.24.0 oder einer höheren Version erstellt werden.

Bei der automatisierten Rotation der Anmeldeinformationen eines Dienstprinzipals wird überprüft, ob der Dienstprinzipal vorhanden ist, und der Dienstprinzipal wird rotiert bzw. ein neuer Dienstprinzipal erstellt.

Rotieren Sie automatisch die Anmeldeinformationen eines Dienstprinzipals mit dem folgenden Befehl:

```azurecli-interactive
# Automatically rotate service principal credentials
az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup
```

### <a name="user-provided-client-id-and-client-secret-service-principal-credential-rotation"></a>Rotation der Anmeldeinformationen eines Dienstprinzipals mit den vom Benutzer bereitgestellten Elementen „client-id“ und „client-secret“ <a id="User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation"></a>


Rotieren Sie die Anmeldeinformationen eines Dienstprinzipals manuell mit der vom Benutzer bereitgestellten Client-ID (client-id) und dem vom Benutzer bereitgestellten Clientgeheimnis (client-secret), indem Sie die folgenden Anweisungen befolgen:

Rufen Sie die Client-ID des Dienstprinzipals (`--client-id`) ab, und legen Sie sie als Umgebungsvariable `SP_ID` fest.
```azurecli-interactive
# Retrieve the service principal clientId
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
```
Generieren Sie mithilfe der obigen Variablen `--client-secret` ein neues sicheres Geheimnis (`SP_ID`) für den Dienstprinzipal. Speichern Sie das neue sichere Geheimnis als Umgebungsvariable `SP_SECRET`.
```azurecli-interactive
# Generate a new secure secret for the service principal
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```
Rotieren Sie die Anmeldeinformationen eines Dienstprinzipals mithilfe der oben genannten Umgebungsvariablen.
```azurecli-interactive
# Rotate service principal credentials
az aro update --client-id $SP_ID --client-secret $SP_SECRET \
    --name MyManagedCluster --resource-group MyResourceGroup
```

## <a name="troubleshoot"></a>Problembehandlung
### <a name="service-principal-expiration-date"></a>Ablaufdatum des Dienstprinzipals
Anmeldeinformationen eines Dienstprinzipals haben ein festgelegtes Ablaufdatum von einem Jahr und sollten innerhalb des angegebenen Zeitraums rotiert werden.

Wenn das Ablaufdatum verstrichen ist, sind die folgenden Fehler möglich:
```bash
Failed to refresh the Token for request to MyResourceGroup StatusCode=401
Original Error: Request failed. Status Code = '401'.
[with]
Response body: {"error":"invalid_client","error_description": The provided client secret keys are expired.
[or]
Response body: {"error":"invalid_client","error_description": Invalid client secret is provided.
```
Führen Sie Folgendes aus, um das Ablaufdatum der Anmeldeinformationen eines Dienstprinzipals zu überprüfen:
```azurecli-interactive
# Service principal expiry in ISO 8601 UTC format
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```
Wenn die Anmeldeinformationen des Dienstprinzipals abgelaufen sind, aktualisieren Sie sie mithilfe einer der beiden Methoden für die Rotation von Anmeldeinformationen.

### <a name="cluster-aad-application-contains-a-client-secret-with-an-empty-description"></a>Die Cluster-AAD-Anwendung enthält einen geheimen Clientschlüssel mit einer leeren Beschreibung.
Bei der Verwendung [der automatisierten Rotation der Anmeldeinformationen eines Dienstprinzipals](#Automated-Service-Principal-Credential-Rotation) tritt der folgende Fehler auf:
```bash
$ az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup

Cluster AAD application contains a client secret with an empty description.
Please either manually remove the existing client secret and run `az aro update --refresh-credentials`,
or manually create a new client secret and run `az aro update --client-secret <ClientSecret>`.
```
Der Cluster wurde nicht mithilfe von Azure CLI 2.24.0 oder höher erstellt. Verwenden Sie stattdessen die Methode für die [Rotation der Anmeldeinformationen eines Dienstprinzipals mit den vom Benutzer bereitgestellten Elementen „client-id“ und „client-secret“](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation).

### <a name="azure-cli-aro-update-help"></a>Azure CLI ARO update help
Ausführlichere Informationen erhalten Sie mit dem Befehl „Azure CLI ARO update help“:
```azurecli-interactive
# Azure CLI ARO update help
az aro update -h
```
