---
title: Upgrade von Azure Arc-Datencontrollern im indirekten Modus mithilfe der CLI
description: Upgrade von Azure Arc-Datencontrollern im indirekten Modus mithilfe der CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: dd5122f123dae93ea07cab65983183b1ffd8b463
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495341"
---
# <a name="upgrade-indirect-mode-azure-arc-data-controller-using-the-cli"></a>Upgrade von Azure Arc-Datencontrollern im indirekten Modus mithilfe der CLI

In diesem Artikel wird beschrieben, wie Sie einen indirekt verbundenen Datencontroller mit Azure Arc-Unterstützung mithilfe des Azure CLI (`az`) upgraden.

> [!IMPORTANT]
> Dieser Artikel gilt nicht für einen direkt verbundenen Datencontroller mit Azure Arc-Unterstützung. Die neuesten Informationen zum Upgraden eines direkt verbundenen Datencontrollers finden Sie in den [Versionshinweisen](/azure/azure-arc/data/release-notes#data-controller-upgrade).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen einen Datencontroller im indirekten Modus mit dem imageTag v1.0.0_2021-07-30 oder höher.

Führen Sie Folgendes aus, um die Version zu überprüfen:

```console
kubectl get datacontrollers -n -o custom-columns=BUILD:.spec.docker.imageTag
```

## <a name="install-tools"></a>Installieren von Tools

Bevor Sie mit den Aufgaben in diesem Artikel fortfahren können, müssen Sie Folgendes installieren:

- Die [Azure CLI (az)](/cli/azure/install-azure-cli)
- Die [`arcdata`-Erweiterung für die Azure CLI](install-arcdata-extension.md)

[!INCLUDE [azure-arc-angle-bracket-example](../../../includes/azure-arc-angle-bracket-example.md)]

## <a name="view-available-images-and-chose-a-version"></a>Anzeigen verfügbarer Images und Auswahl einer Version

Pullen Sie die Liste der verfügbaren Images für den Datencontroller mit dem folgenden Befehl:

   ```azurecli
   az arcdata dc list-upgrades --k8s-namespace <namespace> –-use-k8s
   ```

Der obige Befehl gibt eine Ausgabe wie im folgenden Beispiel zurück:

```output
Found 2 valid versions.  The current datacontroller version is v1.0.0_2021-07-30.
v1.1.0_2021-11-02
v1.0.0_2021-07-30
```

## <a name="upgrade-data-controller"></a>Upgraden des Datencontrollers

In diesem Abschnitt wird gezeigt, wie Sie einen Datencontroller im indirekten Modus upgraden.

> [!NOTE]
> Einige der Datendienstebenen und -modi sind allgemein verfügbar, einige befinden sich in der Vorschau.
> Wenn Sie allgemein verfügbare Dienste und Vorschaudienste auf demselben Datencontroller installieren, können Sie kein direktes Upgrade ausführen.
> Für ein Upgrade löschen Sie alle nicht allgemein verfügbaren (GA) Datenbankinstanzen. Die Liste der allgemein verfügbaren Dienste und Vorschaudienste finden Sie in den [Versionshinweisen](/azure/azure-arc/data/release-notes).

### <a name="indirect-mode"></a>Indirekter Modus

Sie müssen eine Verbindung mit einem Kubernetes-Cluster herstellen und sich authentifizieren sowie einen vorhandenen Kubernetes-Kontext auswählen, bevor Sie mit dem Upgrade des Azure Arc-Datencontrollers beginnen.

Sie können zuerst einen Testlauf durchführen. Der Testlauf überprüft, ob die Registrierung vorhanden ist, sowie das Versionsschema und das Autorisierungstoken des privaten Repositorys (sofern verwendet). Um einen Testlauf auszuführen, verwenden Sie den Parameter `--dry-run` in dem Befehl `az arcdata dc upgrade`. Beispiel:

```azurecli
az arcdata dc upgrade --desired-version <version> --k8s-namespace <namespace> --dry-run --use-k8s
```

Die Ausgabe für den vorherigen Befehl lautet:

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
****Dry Run****
Arcdata Control Plane would be upgraded to: 20211024.1
```

Um den Datencontroller zu upgraden, führen Sie den Befehl `az arcdata dc upgrade` aus. Wenn Sie kein Zielimage angeben, wird der Datencontroller auf die neueste Version aktualisiert. Im folgenden Beispiel wird eine lokale Variable (`$version`) verwendet, um die Version zu verwenden, die Sie zuvor ausgewählt haben ([Anzeigen verfügbarer Images und Auswahl einer Version](#view-available-images-and-chose-a-version)).

```azurecli
az arcdata dc upgrade --desired-version $version --k8s-namespace <namespace> --use-k8s
```

Die Ausgabe für den vorherigen Befehl zeigt den Status der Schritte an:

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Creating service account: arc:cr-upgrade-worker
Creating cluster role: arc:cr-upgrade-worker
Creating cluster role binding: arc:crb-upgrade-worker
Cluster role binding: arc:crb-upgrade-worker created successfully.
Cluster role: arc:cr-upgrade-worker created successfully.
Service account arc:cr-upgrade-worker has been created successfully.
Creating privileged job arc-elevated-bootstrapper-job
```

## <a name="monitor-the-upgrade-status"></a>Überwachen des Upgradestatus

Sie können den Fortschritt des Upgrades mit kubectl oder der CLI überwachen.

### <a name="kubectl"></a>kubectl

```console
kubectl get datacontrollers --namespace <namespace>
kubectl get monitors --namespace <namespace>
```

Das Upgrade ist ein zweiteiliger Prozess. Zuerst erfolgt das Upgrade des Controllers, dann das Upgrade des Überwachungsstapels. Verwenden Sie während des Upgrades ```kubectl get monitors -n <namespace> -w```, um den Status anzuzeigen. Ausgabe:

```output
NAME           STATUS     AGE
monitorstack   Updating   36m
monitorstack   Updating   36m
monitorstack   Updating   39m
monitorstack   Updating   39m
monitorstack   Updating   41m
monitorstack   Ready      41m
```

### <a name="cli"></a>CLI

```azurecli
 az arcdata dc status show --k8s-namespace <namespace> --use-k8s
```

Das Upgrade ist ein zweiteiliger Prozess. Zuerst erfolgt das Upgrade des Controllers, dann das Upgrade des Überwachungsstapels. Wenn das Upgrade abgeschlossen ist, sieht die Ausgabe wie folgt aus:

```output
Ready
```

## <a name="troubleshoot-upgrade-problems"></a>Behandeln von Upgradeproblemen

Wenn Probleme beim Upgrade auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).
