---
title: Hoch- und Herunterskalieren einer Azure Database for PostgreSQL Hyperscale-Servergruppe mithilfe der Befehlszeilenschnittstelle (az oder kubectl)
description: Hoch- und Herunterskalieren einer Azure Database for PostgreSQL Hyperscale-Servergruppe mithilfe der Befehlszeilenschnittstelle (az oder kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 1c33c2f4bf89b76abf40d12146965114ba4918b0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564497"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-az-or-kubectl"></a>Hoch- und Herunterskalieren einer Azure Database for PostgreSQL Hyperscale-Servergruppe mithilfe der Befehlszeilenschnittstelle (az oder kubectl)

Es kann vorkommen, dass Sie die Merkmale oder die Definition einer Servergruppe ändern müssen. Zum Beispiel:

- Zentrales Hoch- oder Herunterskalieren der Anzahl von virtuellen Kernen, die von den einzelnen Koordinatorknoten oder Workerknoten verwendet werden
- Zentrales Hoch- oder Herunterskalieren des Speichers, der von den einzelnen Koordinatorknoten oder Workerknoten verwendet wird

In diesem Leitfaden wird erläutert, wie Sie virtuelle Kerne und/oder Arbeitsspeicher skalieren.

Wenn Sie die Einstellungen der virtuellen Kerne oder des Arbeitsspeichers der Servergruppe zentral hoch-oder herunterskalieren, haben Sie die Möglichkeit, für jede Einstellung der virtuellen Kerne oder des Arbeitsspeichers einen Minimal- und/oder einen Maximalwert festzulegen. Wenn Sie die Servergruppe so konfigurieren möchten, dass eine bestimmte Anzahl von virtuellen Kernen oder eine bestimmte Menge an Arbeitsspeicher verwendet wird, legen Sie die Minimalwerteinstellungen auf die Maximalwerteinstellungen fest. Bevor Sie die für virtuelle Kerne und den Arbeitsspeicher festgelegten Werte erhöhen, müssen Sie Folgendes sicherstellen: 
- Sie verfügen über genügend Ressourcen in der physischen Infrastruktur, die Ihre Bereitstellung hostet. 
- Workloads, die auf demselben System zugeordnet sind, konkurrieren nicht um dieselben virtuellen Kerne oder denselben Arbeitsspeicher.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Anzeigen der aktuellen Definition der Servergruppe

Führen Sie einen der folgenden Befehle aus, um die aktuelle Definition der Servergruppe und die aktuellen Einstellungen für virtuelle Kerne und den Arbeitsspeicher anzuzeigen:

### <a name="with-azure-cli-az"></a>Mit Azure CLI (az)

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```
### <a name="cli-with-kubectl"></a>CLI mit kubectl

```console
kubectl describe postgresql/<server group name> -n <namespace name>
```

Die Konfiguration der Servergruppe wird zurückgegeben. Wenn Sie die Servergruppe mit den Standardeinstellungen erstellt haben, sollte die Definition wie folgt angezeigt werden:

```json
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
...
```

## <a name="interpret-the-definition-of-the-server-group"></a>Interpretieren der Definition der Servergruppe

In der Definition einer Servergruppe ist der Abschnitt, der die Einstellungen der minimalen/maximalen Anzahl virtueller Kerne pro Knoten und die minimale/maximale Größe des Arbeitsspeichers pro Knoten festlegt, der Abschnitt **scheduling**. In diesem Abschnitt werden die maximalen Einstellungen in einem Unterabschnitt namens **limits** und die minimalen Einstellungen im Unterabschnitt **requests** persistent gespeichert.

Wenn Sie minimale Einstellungen festlegen, die sich von den maximalen Einstellungen unterscheiden, gewährleistet die Konfiguration, dass der Servergruppe bei Bedarf die angeforderten Ressourcen zugewiesen werden. Die von Ihnen festgelegten Grenzwerte werden nicht überschritten.

Die Ressourcen (virtuelle Kerne und Arbeitsspeicher), die tatsächlich von der Servergruppe verwendet werden, entsprechen den maximalen Einstellungen und sind von den Workloads und den Ressourcen abhängig, die im Cluster verfügbar sind. Wenn Sie die Einstellungen nicht durch ein Maximum begrenzen, kann Ihre Servergruppe alle Ressourcen verwenden, die der Kubernetes-Cluster den Kubernetes-Knoten zuweist, auf denen Ihre Servergruppe geplant ist.

Diese Einstellungen für virtuelle Kerne und Arbeitsspeicher gelten für alle Rollen der Postgres-Instanzen, die die PostgreSQL Hyperscale-Servergruppe bilden: Koordinator und Worker. Sie können Anforderungen und Grenzwerte pro Rolle definieren. Sie können für jede Rolle unterschiedliche Einstellungen für Anforderungen und Grenzwerte definieren. Abhängig von Ihren Anforderungen können sie auch gleich sein.

In einer Standardkonfiguration ist nur der Mindestspeicher auf 256Mi festgelegt, da dies die Mindestmenge an Speicher ist, die für die Ausführung von PostgreSQL Hyperscale empfohlen wird.

> [!NOTE]
> Das Festlegen eines Mindestwerts bedeutet nicht, dass die Servergruppe diesen Mindestwert notwendigerweise verwendet. Das bedeutet, dass garantiert wird, dass der Servergruppe mindestens dieses Minimum zugewiesen wird, wenn sie es benötigt. Nehmen wir beispielsweise an, dass wir `--minCpu 2` festlegen. Dies bedeutet nicht, dass die Servergruppe immer mindestens 2 virtuelle Kerne verwendet. Es bedeutet vielmehr, dass die Servergruppe weniger als 2 virtuelle Kerne verwenden kann, wenn sie nicht so viel benötigt, und es wird garantiert, dass mindestens 2 virtuelle Kerne zugewiesen werden, wenn diese später benötigt werden. Dies impliziert, dass der Kubernetes-Cluster Ressourcen anderen Workloads so zuordnet, dass er der Servergruppe 2 virtuelle Kerne zuordnen kann, wenn sie benötigt werden. Außerdem ist das Hoch- und Herunterskalieren kein Onlinevorgang, da es einen Neustart der Kubernetes-Pods erfordert.

>[!NOTE]
>Bevor Sie die Konfiguration Ihres Systems ändern, stellen Sie sicher, dass Sie sich [hier](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities) mit dem Kubernetes-Ressourcenmodell vertraut machen.

## <a name="scale-up-and-down-the-server-group"></a>Hoch- und Herunterskalieren der Servergruppe

Das Hochskalieren bezeichnet das Erhöhen der Einstellungswerte für virtuelle Kerne oder Arbeitsspeicher Ihrer Servergruppe.
Das Herunterskalieren bezeichnet das Verringern der Einstellungswerte für virtuelle Kerne oder Arbeitsspeicher Ihrer Servergruppe.

Die Einstellungen, die Sie festlegen, müssen innerhalb der Konfiguration berücksichtigt werden, die Sie für den Kubernetes-Cluster festlegen. Stellen Sie sicher, dass Sie keine Werte festlegen, die ihr Kubernetes-Cluster nicht erfüllen kann. Dies kann zu Fehlern oder unvorhersehbarem Verhalten wie der Nichtverfügbarkeit der Datenbankinstanz führen. Wenn der Status Ihrer Servergruppe beispielsweise lange Zeit nach der Änderung der Konfiguration im Status _updating_ (wird aktualisiert) verbleibt, kann das ein Hinweis darauf sein, dass Sie die unten aufgeführten Parameter auf Werte festlegen, die Ihr Kubernetes-Cluster nicht erfüllen kann. Wenn dies der Fall ist, machen Sie die Änderung rückgängig, oder lesen Sie den Abschnitt zur Problembehandlung.

Welche Einstellungen sollten festgelegt werden?
- Den Mindestwert für virtuelle Kerne legen Sie mit `--cores-request` fest.
- Den Höchstwert für virtuelle Kerne legen Sie mit `--cores-limit` fest.
- Den Mindestwert für den Arbeitsspeicher legen Sie mit `--memory-request` fest.
- Den Höchstwert für den Arbeitsspeicher legen Sie mit `--memory-limit` fest.

Wie geben Sie an, für welche Rolle eine Einstellung gilt?
- Zum Konfigurieren der Einstellung für die Koordinatorrolle geben Sie `coordinator=<value>` an.
- Zum Konfigurieren der Einstellung für die Workerrolle (die angegebene Einstellung wird auf allen Workern auf den gleichen Wert festgelegt) geben Sie `worker=<value>` an.


> [!CAUTION]
> Bei Kubernetes wird beim Konfigurieren einer Grenzwerteinstellung ohne Konfiguration der entsprechenden Anforderungseinstellung der Anforderungswert erzwungen auf den Grenzwert festgelegt. Dies kann dazu führen, dass Ihre Servergruppe nicht verfügbar ist, da ihre Pods möglicherweise nicht neu geplant werden, wenn kein Kubernetes-Knoten mit ausreichenden Ressourcen verfügbar ist. Um diese Situation zu vermeiden, veranschaulichen die folgenden Beispiele, wie Sie sowohl die Anforderung als auch die Grenzwerteinstellungen festlegen.


**Die allgemeine Syntax sieht wie folgt aus:**

```azurecli
az postgres arc-server edit -n <servergroup name> --memory-limit/memory-request/cores-request/cores-limit <coordinator=val1,worker=val2> --k8s-namespace <namespace> --use-k8s
```

Der Wert, den Sie für die Arbeitsspeichereinstellung angeben, ist eine Zahl gefolgt von einer Volumeneinheit. Um beispielsweise 1 GB anzugeben, geben Sie „1024Mi“ oder „1Gi“ an.
Um eine Anzahl von Kernen anzugeben, übergeben Sie einfach eine Zahl ohne Einheit. 

### <a name="examples-using-the-azure-cli"></a>Beispiele für die Verwendung der Azure-Befehlszeilenschnittstelle

**Konfigurieren Sie die Koordinatorrolle so, dass sie höchstens zwei Kerne verwendet, und die Workerrolle so, dass sie höchstens vier Kerne verwendet:**

```azurecli
 az postgres arc-server edit -n postgres01 --cores-request coordinator=1, --cores-limit coordinator=2  --k8s-namespace arc --use-k8s
 az postgres arc-server edit -n postgres01 --cores-request worker=1, --cores-limit worker=4 --k8s-namespace arc --use-k8s
```

oder
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator=1,worker=1 --cores-limit coordinator=4,worker=4 --k8s-namespace arc --use-k8s
```

> [!NOTE]
> Weitere Informationen zu diesen Parametern erhalten Sie, wenn Sie `az postgres arc-server edit --help` ausführen.

### <a name="example-using-kubernetes-native-tools-like-kubectl"></a>Beispiel für die Verwendung nativer Kubernetes-Tools wie `kubectl`

Führen Sie den folgenden Befehl aus: 
```console
kubectl edit postgresql/<servergroup name> -n <namespace name>
```

Sie starten damit den `vi`-Editor, in dem Sie navigieren und die Konfiguration ändern können. Verwenden Sie Folgendes, um die gewünschte Einstellung dem Namen des Felds in der Spezifikation zuzuordnen:

> [!CAUTION]
> Das folgende Beispiel veranschaulicht, wie Sie die Konfiguration bearbeiten können. Stellen Sie vor dem Aktualisieren der Konfiguration sicher, dass die Parameter auf Werte festgelegt sind, die der Kubernetes-Cluster akzeptieren kann.

Angenommen, Sie möchten die folgenden Einstellungen für die Koordinator- und Workerrollen auf die folgenden Werte festlegen:
- Mindestanzahl virtueller Kerne = `2` 
- Maximale Anzahl virtueller Kerne = `4`
- Mindestgröße des Arbeitsspeichers = `512Mb`
- Maximale Größe des Arbeitsspeichers = `1Gb` 

Dazu würden Sie die Definition Ihrer Servergruppe so festlegen, dass sie der folgenden Konfiguration entspricht:

```json
...
  spec:
  dev: false
  engine:
    extensions:
    - name: citus
    version: 12
  scale:
    replicas: 1
    syncReplicas: "0"
    workers: 4
  scheduling:
    default:
      resources:
        requests:
          memory: 256Mi
    roles:
      coordinator:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 256Mi
      worker:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 256Mi
...
```

Wenn Sie mit dem `vi`-Editor nicht vertraut sind, finden Sie [hier](https://www.computerhope.com/unix/uvi.htm) eine Beschreibung der Befehle, die Sie möglicherweise benötigen:
- Bearbeitungsmodus: `i`
- Navigation mit Pfeiltasten
- Beenden der Bearbeitung: `esc`
- Beenden ohne Speichern: `:qa!`
- Beenden nach Speichern: `:qw!`


## <a name="reset-to-default-values"></a>Zurücksetzen auf Standardwerte
Zum Zurücksetzen der Parameter für Kerne/Arbeitsspeichergrenzwerte/Anforderungen auf ihre Standardwerte bearbeiten Sie sie und übergeben eine leere Zeichenfolge anstelle eines tatsächlichen Werts. Wenn Sie beispielsweise den Parameter für den Kerngrenzwert zurücksetzen möchten, führen Sie die folgenden Befehle aus:

```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --k8s-namespace arc --use-k8s
az postgres arc-server edit -n postgres01 --cores-limit coordinator='',worker='' --k8s-namespace arc --use-k8s
```

oder 
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --cores-limit coordinator='',worker='' --k8s-namespace arc --use-k8s
```

## <a name="next-steps"></a>Nächste Schritte

- [Skalieren Ihrer Azure Database for PostgreSQL Hyperscale-Servergruppe](scale-out-in-postgresql-hyperscale-server-group.md)
- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
