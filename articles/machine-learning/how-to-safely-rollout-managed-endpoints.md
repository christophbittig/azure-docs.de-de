---
title: Sicherer Rollout für Onlineendpunkte
titleSuffix: Azure Machine Learning
description: Führen Sie neuere Versionen von ML-Modellen aus, ohne dass es zu Unterbrechungen kommt.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: b8dfb926a1c878893142998bea0091d9d8162e0f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032656"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>Sicherer Rollout für Onlineendpunkte (Vorschau)

Sie verfügen über ein vorhandenes Modell, das in der Produktion bereitgestellt wurde, und möchten eine neue Version des Modells bereitstellen. Wie führen Sie den Rollout Ihres neuen ML-Modells durch, ohne dass es zu Unterbrechungen kommt? Eine gute Möglichkeit ist die Blau-Grün-Bereitstellung. Bei diesem Ansatz wird eine neue Version eines Webdiensts in die Produktion eingeführt, indem der Rollout zunächst nur für einen kleinen Teil der Benutzer bzw. Anforderungen durchgeführt wird, bevor der vollständige Rollout erfolgt. In diesem Artikel wird davon ausgegangen, dass Sie Onlineendpunkte verwenden. Weitere Informationen finden Sie unter [Was sind Azure Machine Learning-Endpunkte (Vorschauversion)?](concept-endpoints.md).

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen eines neuen Onlineendpunkts mit dem Namen „blue“, von dem Version 1 des Modells bereitgestellt wird
> * Skalieren dieser Bereitstellung, damit mehr Anforderungen verarbeitet werden können
> * Bereitstellen von Version 2 des Modells auf einem Endpunkt mit dem Namen „green“, für den kein Livedatenverkehr akzeptiert wird
> * Isoliertes Testen der Bereitstellung „green“ 
> * Senden von 10 % des Livedatenverkehrs an die Bereitstellung „green“
> * Vollständiges Umstellen des gesamten Livedatenverkehrs auf die Bereitstellung „green“
> * Löschen der nun nicht mehr genutzten ersten Bereitstellung „blue“

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Für die Verwendung von Azure Machine Learning ist ein Azure-Abonnement erforderlich. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Sie müssen die Azure CLI und die ML-Erweiterung installieren und konfigurieren. Weitere Informationen finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschauversion)](how-to-configure-cli.md). 

* Sie müssen über eine Azure-Ressourcengruppe verfügen, für die Ihnen (oder dem von Ihnen genutzten Dienstprinzipal) die Zugriffsberechtigung `Contributor` (Mitwirkender) zugeordnet ist. Sie verfügen über eine Ressourcengruppe dieser Art, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben. 

* Sie müssen über einen Azure Machine Learning-Arbeitsbereich verfügen. Sie verfügen über einen Arbeitsbereich dieser Art, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben.

* Falls Sie die Standardwerte für die Azure CLI noch nicht festgelegt haben, sollten Sie Ihre Standardeinstellungen speichern. Führen Sie Folgendes aus, um zu vermeiden, dass Sie die Werte immer wieder neu übergeben müssen:

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

* Einen vorhandenen verwalteten Endpunkt. In diesem Artikel wird vorausgesetzt, dass Sie eine Bereitstellung verwenden, die gemäß der Beschreibung unter [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md) konfiguriert ist.

* Falls Sie die Umgebungsvariable „$ENDPOINT_NAME“ noch nicht festgelegt haben, sollten Sie dies jetzt durchführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (Empfohlen) Klonen Sie das Beispielrepository, und wechseln Sie zum Verzeichnis `cli/` des Repositorys: 

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples/cli
```

Die in diesem Tutorial verwendeten Befehle befinden sich in der Datei `deploy-declarative-safe-rollout-online-endpoints.sh`, und die YAML-Konfigurationsdateien finden Sie im Unterverzeichnis `endpoints/online/managed/canary-declarative-flow/`.

## <a name="confirm-your-existing-deployment-is-created"></a>Überprüfen der Erstellung Ihrer vorhandenen Bereitstellung

Sie können den Status Ihrer vorhandenen Bereitstellung anzeigen, indem Sie Folgendes ausführen: 

```azurecli
az ml endpoint show --name $ENDPOINT_NAME 
```

Es sollten der Endpunkt mit `$ENDPOINT_NAME` und eine Bereitstellung mit dem Namen `blue` angezeigt werden. 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>Skalieren Ihrer vorhandenen Bereitstellung zur Verarbeitung einer größeren Menge an Datenverkehr

In der Bereitstellung, die unter [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md) beschrieben ist, legen Sie `instance_count` auf den Wert `1` fest. Damit mehr Datenverkehr verarbeitet werden kann, wird der Wert von der zweiten Version der YAML-Datei (`2-scale-blue.yml`) in `2` geändert:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/2-scale-blue.yml" range="29":::

Aktualisieren Sie die Bereitstellung wie folgt:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!IMPORTANT]
> Die Aktualisierung per YAML-Code ist deklarativ. Dies bedeutet, dass Änderungen am YAML-Code in den zugrunde liegenden Azure Resource Manager-Ressourcen (Endpunkte und Bereitstellungen) widergespiegelt werden. Bei diesem Ansatz kann [GitOps](https://www.atlassian.com/git/tutorials/gitops) genutzt werden: *ALLE* Änderungen an Endpunkten bzw. Bereitstellungen erfolgen über den YAML-Code (auch `instance_count`). Dies ist mit der folgenden Nebenwirkung verbunden: Wenn Sie eine Bereitstellung aus dem YAML-Code entfernen und `az ml endpoint update` mit der Datei ausführen, wird die Bereitstellung gelöscht. 

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>Bereitstellen eines neuen Modells, ohne vorerst Datenverkehr zu senden

Fügen Sie für die Bereitstellung eines neuen Modells dem Abschnitt `deployments` Ihrer Konfigurationsdatei einen neuen Abschnitt hinzu. Geben Sie aber im Abschnitt `traffic` an, dass sie 0 % des Datenverkehrs erhalten soll. Diese Änderung ist in der Datei `3-create-green.yml` enthalten:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/3-create-green.yml" range="7,35-56":::

Aktualisieren Sie die Bereitstellung wie folgt: 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="create_green" :::

### <a name="test-the-new-deployment"></a>Testen der neuen Bereitstellung

In der Konfiguration wurde angegeben, dass Ihre soeben erstellte Bereitstellung `green` 0 % des Datenverkehrs erhalten soll. Als Test können Sie sie direkt aufrufen, indem Sie den Namen von `--deployment` angeben:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="test_green" :::

Falls Sie einen REST-Client verwenden möchten, um die Bereitstellung ohne Verwendung von Datenverkehrsregeln direkt aufzurufen, müssen Sie den folgenden HTTP-Header festlegen: `azureml-model-deployment: <deployment-name>`.

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>Testen der neuen Bereitstellung mit einem geringen Prozentsatz des Livedatenverkehrs

Nachdem Sie die Bereitstellung `green` getestet haben, wird anhand der Datei `4-flight-green.yml` demonstriert, wie Sie einen bestimmten Prozentsatz des Datenverkehrs bereitstellen, indem Sie die Konfiguration unter `traffic` in der Konfigurationsdatei ändern:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/4-flight-green.yml" range="5-7":::

Abgesehen von den hervorgehobenen Zeilen bleibt die Konfigurationsdatei unverändert. Aktualisieren Sie Ihre Bereitstellung wie folgt:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

Nun erhält Ihre Bereitstellung `green` 10 % der Anforderungen. 

## <a name="send-all-traffic-to-your-new-deployment"></a>Senden des gesamten Datenverkehrs an Ihre neue Bereitstellung

Wenn Sie mit der Bereitstellung `green` zufrieden sind, können Sie den gesamten Datenverkehr entsprechend umstellen. Der folgende Codeausschnitt enthält nur den relevanten Code aus der Konfigurationsdatei, die ansonsten unverändert ist:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/5-full-green.yml" range="5-7":::

Aktualisieren Sie die Bereitstellung wie folgt: 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>Entfernen der alten Bereitstellung

Schließen Sie die Umstellung auf Ihr neues Modell ab, indem Sie die ältere Bereitstellung `blue` löschen. Die fertige Konfigurationsdatei sieht wie folgt aus:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/6-delete-blue.yml" :::

Aktualisieren Sie die Bereitstellung wie folgt:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>Löschen des Endpunkts und der Bereitstellung

Falls Sie die Bereitstellung nicht weiter nutzen möchten, sollten Sie sie wie folgt löschen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::


## <a name="next-steps"></a>Nächste Schritte
- [Bereitstellen von Modellen per REST (Vorschau)](how-to-deploy-with-rest.md)
- [Erstellen und Verwenden von verwalteten Onlineendpunkten (Vorschau) in Studio](how-to-use-managed-online-endpoint-studio.md)
- [Tutorial: Zugreifen auf Azure-Ressourcen mit einem verwalteten Onlineendpunkt und einer systemseitig verwalteten Identität (Vorschau)](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [Überwachen verwalteter Onlineendpunkte (Vorschau)](how-to-monitor-online-endpoints.md)
- [Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
- [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md)
- [SKU-Liste für verwaltete Onlineendpunkte (Vorschau)](reference-managed-online-endpoints-vm-sku-list.md)
- [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](./how-to-troubleshoot-online-endpoints.md)
- [YAML-Referenz zu verwalteten Onlineendpunkten (Vorschauversion)](reference-yaml-endpoint-managed-online.md)