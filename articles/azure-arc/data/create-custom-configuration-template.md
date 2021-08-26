---
title: Erstellen von benutzerdefinierten Konfigurationsvorlagen
description: Erstellen von benutzerdefinierten Konfigurationsvorlagen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dinethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e7d5d470fd967c9f350fddaae2032085caf17c07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355406"
---
# <a name="create-custom-configuration-templates"></a>Erstellen von benutzerdefinierten Konfigurationsvorlagen

In diesem Artikel wird erläutert, wie Sie eine benutzerdefinierte Konfigurationsvorlage für Azure Arc-fähige Datencontroller erstellen. 

Einer der erforderlichen Parameter während der Bereitstellung eines Datencontrollers im indirekten Konnektivitätsmodus ist der Parameter `az arcdata dc create --profile-name`. Derzeit finden Sie die verfügbare Liste der integrierten Profile, indem Sie folgende Abfrage ausführen:

```azurecli
az arcdata dc config list
```
Diese Profile sind JSON-Vorlagendateien, die verschiedene Einstellungen für den Azure Arc-fähigen Datencontroller enthalten, z. B. Docker-Registrierungs- und Repositoryeinstellungen, Speicherklassen für Daten und Protokolle, Speichergröße für Daten und Protokolle, Sicherheit, Diensttyp usw., die auch an Ihre Umgebung angepasst werden können. 

In einigen Fällen sollten Sie diese Konfigurationsvorlagen jedoch an Ihre Anforderungen anpassen und die angepasste Konfigurationsvorlage mithilfe des Parameters `--path` an den Befehl `az arcdata dc create` übergeben, statt eine vorkonfigurierte Konfigurationsvorlage mit dem Parameter `--profile-name` zu übergeben.

## <a name="create-customjson-file"></a>Erstellen der Datei „custom.json“

Führen Sie `az arcdata dc config init` aus, um eine „control.json“-Datei mit vordefinierten Einstellungen zu initiieren, die auf Ihrer Kubernetes-Distribution des Clusters basieren.
Beispielsweise kann eine Vorlagendatei „control.json“, die auf der Vorlage `azure-arc-kubeadm` basiert, die sich in einem Unterverzeichnis namens `custom` im aktuellen Arbeitsverzeichnis befindet, für einen Kubernetes-Cluster wie folgt erstellt werden:

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
Die erstellte „control.json“-Datei kann in einem beliebigen Editor bearbeitet werden, z. B. Visual Studio Code, um die für Ihre Umgebung geeigneten Einstellungen anzupassen.

## <a name="use-custom-controljson-file-to-deploy-azure-arc-enabled-data-controller-using-azure-cli-az"></a>Verwenden der benutzerdefinierten Datei „control.json“ zum Bereitstellen eines Azure Arc-fähigen Datencontrollers mithilfe von Azure CLI (az)

Nachdem die Vorlagendatei erstellt wurde, kann sie während des create-Befehls für den Azure Arc-fähigen Datencontroller wie folgt angewendet werden:

```azurecli
az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect  --k8s-namespace <namespace> --use-k8s

#Example:
#az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription ID> --resource-group my-resource-group --location eastus --connectivity-mode indirect --k8s-namespace <namespace> --use-k8s
```

## <a name="use-custom-controljson-file-for-deploying-azure-arc-data-controller-using-azure-portal"></a>Verwenden der benutzerdefinierten Datei „control.json“ zum Bereitstellen eines Azure Arc-fähigen Datencontrollers mithilfe des Azure-Portals

Wählen Sie auf dem Bildschirm zum Erstellen des Azure Arc-fähigen Datencontrollers unter „Benutzerdefinierte Vorlage“ die Option „Benutzerdefinierte Vorlage konfigurieren“ aus. Dadurch wird ein Blatt zum Bereitstellen benutzerdefinierter Einstellungen aufgerufen. Auf diesem Blatt können Sie entweder die Werte für die verschiedenen Einstellungen eingeben oder eine vorkonfigurierte „control.json“-Datei direkt hochladen. 

Nachdem Sie sichergestellt haben, dass die Werte korrekt sind, klicken Sie auf „Anwenden“, um mit der Bereitstellung des Azure Arc-Datencontrollers fortzufahren.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen des Datencontrollers: Direkter Verbindungsmodus (Voraussetzungen)](create-data-controller-direct-prerequisites.md)

[Azure Arc-Datencontroller erstellen (CLI)](create-data-controller-direct-cli.md)
