---
title: Erstellen und Auswählen von Kubernetes-Instanztypen (Vorschauversion)
description: Erstellen und Auswählen von Azure Arc-fähigen Kubernetes-Cluster-Instanztypen für Trainings- und Rückschluss-Workloads in Azure Machine Learning.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 46ae66658d5f07e1a44322bf64997a80d66db1c0
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485175"
---
# <a name="create-and-select-kubernetes-instance-types-preview"></a>Erstellen und Auswählen von Kubernetes-Instanztypen (Vorschauversion)

Erfahren Sie, wie Sie Kubernetes-Instanzen für Azure Machine Learning-Trainings- und Rückschluss-Workloads in Azure Arc-fähigen Kubernetes-Clustern erstellen und auswählen können.

## <a name="what-are-instance-types"></a>Was sind Instanztypen?

Instanztypen sind ein Azure Machine Learning, das es ermöglicht, bestimmte Arten von Serverknoten für Trainings- und Rückschluss-Workloads als Ziel zu verwenden.  Für einen Azure-VM ist ein Beispiel für einen Instanztyp `STANDARD_D2_V3`.

In Kubernetes-Clustern werden Instanztypen durch zwei Elemente definiert:

* [nodeSelector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector) – Mit A `nodeSelector` können Sie angeben, auf welchem Knoten ein Pod ausgeführt werden soll.  Der Knoten muss über eine entsprechende Bezeichnung verfügen.
* [resources](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) – Im Abschnitt `resources` werden die Computeressourcen (CPU, Arbeitsspeicher und Nvidia-GPU) für den Pod definiert.

## <a name="create-instance-types"></a>Erstellen von Instanztypen

Instanztypen werden in einer benutzerdefinierten Ressourcendefinition (CRD) dargestellt, die mit der Azure Machine Learning-Erweiterung installiert wird.  

### <a name="create-a-single-instance-type"></a>Erstellen eines einzelnen Instanztyps

Um einen neuen Instanztyp zu erstellen, müssen Sie eine neue benutzerdefinierte Ressource für den Instanztyp CRD erstellen.  

Wenn beispielsweise die CRD `my_instance_type.yaml` vorliegt:

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceType
metadata:
  name: myinstancetypename
spec:
  nodeSelector:
    mylabel: mylabelvalue
  resources:
    limits:
      cpu: "1"
      nvidia.com/gpu: 1
      memory: "2Gi"
    requests:
      cpu: "700m"
      memory: "1500Mi"
```

Verwenden Sie den Befehl `kubectl apply`, um einen neuen Instanztyp zu erstellen.

```bash
kubectl apply -f my_instance_type.yaml
```

Durch diesen Vorgang wird ein Instanztyp mit den folgenden Eigenschaften erstellt:

- Pods werden nur auf Knoten mit der Bezeichnung `mylabel: mylabelvalue` geplant.
- Pods werden Ressourcenanforderungen für CPU `700m` und Arbeitsspeicher `1500Mi` zugewiesen. 
- Pods werden Ressourcenlimits für CPU `1`, Arbeitsspeicher `2Gi` und Nvidia-GPU `1` zugewiesen.

> [!NOTE]
> Beachten Sie bei der Angabe Ihrer CRD die folgenden Konventionen:
> - Nvidia-GPU-Ressourcen werden nur im Abschnitt `limits` angegeben.  Weitere Informationen finden Sie unter [Kubernetes Documentation (Dokumentation zu Kubernetes)](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/#using-device-plugins).
> - CPU- und Arbeitsspeicherressourcen sind Zeichenfolgenwerte.
> - Die CPU kann in Millicores (`100m`) oder in ganzen Zahlen (`"1"` was gleichbedeutend mit `1000m` ist) angegeben werden.
> - Der Arbeitsspeicher kann als vollständige Zahl + Suffix (`1024Mi` für `1024 MiB`) angegeben werden.

### <a name="create-multiple-instance-types"></a>Erstellen mehrerer Instanztypen

Sie können CRDs auch verwenden, um mehrere Instanztypen gleichzeitig zu erstellen.

Wenn beispielsweise die CRD `my_instance_type.yaml` vorliegt:

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceTypeList
items:
  - metadata:
      name: cpusmall
    spec:
      resources:
        limits:
          cpu: "100m"
          nvidia.com/gpu: 0
          memory: "10Mi"
        requests:
          cpu: "100m"
          memory: "1Gi"

  - metadata:
      name: defaultinstancetype
    spec:
      resources:
        limits:
          cpu: "1"
          nvidia.com/gpu: 0
          memory: "1Gi"
        requests:
          cpu: "1"
          memory: "1Gi" 
```

Verwenden Sie den Befehl `kubectl apply`, um mehrere Instanztypen zu erstellen.

```bash
kubectl apply -f my_instance_type.yaml
``` 

Bei diesem Vorgang werden zwei Instanztypen erstellt: einer mit dem Namen `defaultinstancetype` und der andere mit dem Namen `cpusmall` sowie mit unterschiedlichen Ressourcenspezifikationen. Weitere Informationen zu Standardinstanztypen finden Sie im Abschnitt [Standardinstanztypen](#default-instance-types) dieses Dokuments.

## <a name="default-instance-types"></a>Standardinstanztypen

Wenn ein Trainings- oder Rückschluss-Workload ohne Instanztyp übermittelt wird, wird der Standardinstanztyp verwendet.  

Um einen Standardinstanztyp für einen Kubernetes-Cluster anzugeben, müssen Sie einen Instanztyp mit dem Namen `defaultinstancetype` erstellen und die entsprechenden Eigenschaften `nodeSelector` und `resources` wie für jeden anderen Instanztyp festlegen.  Der Instanztyp wird automatisch als Standard erkannt.

Wenn kein Standardinstanztyp definiert ist, gilt das folgende Standardverhalten:

* Es wird kein nodeSelector angewendet, was bedeutet, dass der Pod auf jedem Knoten geplant werden kann.
* Den Pods des Workloads werden die Standardressourcen zugewiesen:

    ```yaml
    resources:
        limits:
        cpu: "0.6"
        memory: "1536Mi"
        requests:
        cpu: "0.6"
        memory: "1536Mi"
    ```

> [!IMPORTANT]
> Der Standardinstanztyp wird nicht als benutzerdefinierte InstanceType-Ressource im Cluster , sondern auf allen Clients (Azure Machine Learning Studio, Azure CLI Python SDK) angezeigt.

## <a name="select-an-instance-type-for-training-workloads"></a>Auswählen eines Instanztyps für Trainingsworkloads

Um einen Instanztyp für einen Trainingsauftrag mithilfe der Azure Machine Learning 2.0 CLI auszuwählen, geben Sie seinen Namen als Teil des Abschnitts `compute` in der YAML-Spezifikationsdatei an.  

```yaml
command: python -c "print('Hello world!')"
environment:
  docker:
    image: python
compute:
  target: azureml:<compute_target_name>
  instance_type: <instance_type_name>
```

Ersetzen Sie im obigen Beispiel `<compute_target_name>` durch den Namen Ihres Kubernetes-Computeziels und `<instance_type_name>` durch den Namen des Instanztyps, den Sie auswählen möchten.

> [!TIP]
> Der Standardinstanztyp verwendet gezielt wenig Ressourcen. Um sicherzustellen, dass alle Machine Learning-Workloads mit den entsprechenden Ressourcen erfolgreich ausgeführt werden, wird dringend empfohlen, benutzerdefinierte Instanztypen zu erstellen.

## <a name="select-an-instance-type-for-inferencing-workloads"></a>Auswählen eines Instanztyps für Rückschluss-Workloads

Wenn Sie einen Instanztyp für Rückschluss-Workloads mithilfe der Azure Machine Learning 2.0 CLI auswählen, geben Sie dessen Namen als Teil des Abschnitts `deployments` an.  Beispiel:

```yaml
type: online
auth_mode: key
target: azureml:<your compute target name>
traffic:
  blue: 100

deployments:
  - name: blue
    app_insights_enabled: true
    model: 
      name: sklearn_mnist_model
      version: 1
      local_path: ./model/sklearn_mnist_model.pkl
    code_configuration:
      code: 
        local_path: ./script/
      scoring_script: score.py
    instance_type: <instance_type_name>
    environment: 
      name: sklearn-mnist-env
      version: 1
      path: .
      conda_file: file:./model/conda.yml
      docker:
        image: mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210727.v1
```

Ersetzen Sie im obigen Beispiel `<instance_type_name>` durch den Namen des Instanztyps, den Sie auswählen möchten.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren des maschinellen Lernens mit Azure Arc-Unterstützung (Vorschauversion)](how-to-attach-arc-kubernetes.md)
- [Trainieren von Modellen (Erstellen von Aufträgen) mit der CLI (v2)](how-to-train-cli.md)
- [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
