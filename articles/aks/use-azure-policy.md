---
title: Verwenden von Azure Policy zum Schützen Ihres Clusters
description: Verwenden Sie Azure Policy, um einen AKS-Cluster (Azure Kubernetes Service) zu schützen.
ms.service: container-service
ms.topic: how-to
ms.date: 09/01/2021
ms.custom: template-how-to
ms.openlocfilehash: e4a70b32da22d5900e169b22cabde2654cb2dcfe
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548328"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Schützen Ihres Clusters mit Azure Policy

Um die Sicherheit Ihres Azure Kubernetes Service-Clusters (AKS) zu verbessern, können Sie mithilfe von Azure Policy integrierte Sicherheitsrichtlinien auf den Cluster anwenden und erzwingen. [Azure Policy][azure-policy] unterstützt Sie bei der Durchsetzung von Organisationsstandards und der Bewertung der Compliance im großen Stil. Nachdem Sie das [Azure Policy-Add-On für AKS][kubernetes-policy-reference] installiert haben, können Sie einzelne Richtliniendefinitionen oder als Initiativen bezeichnete Gruppen von Richtliniendefinitionen (mitunter als Richtliniensätze bezeichnet) auf Ihren Cluster anwenden. Eine vollständige Liste der AKS-Richtlinien- und -Initiativendefinitionen finden Sie unter [Integrierte Azure Policy-Definitionen für AKS][aks-policies].

In diesem Artikel wird gezeigt, wie Sie Richtliniendefinitionen auf Ihren Cluster anwenden und überprüfen, ob diese Zuweisungen erzwungen werden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein vorhandener AKS-Cluster. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].
- Installation des Azure Policy-Add-Ons für AKS auf einem AKS-Cluster. Befolgen Sie diese [Schritte zum Installieren des Azure Policy-Add-Ons][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Zuweisen einer integrierten Richtliniendefinition oder -initiative

Verwenden Sie das Azure-Portal, um eine Richtliniendefinition oder -initiative anzuwenden.

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie im linken Bereich der Seite „Azure Policy“ die Option **Definitionen** aus.
1. Wählen Sie unter **Kategorien** die Option `Kubernetes` aus.
1. Wählen Sie die Richtliniendefinition oder -initiative aus, die Sie anwenden möchten. Wählen Sie für dieses Beispiel die Initiative `Kubernetes cluster pod security baseline standards for Linux-based workloads` aus.
1. Wählen Sie **Zuweisen** aus.
1. Legen Sie **Bereich** auf die Ressourcengruppe des AKS-Clusters fest, für den das Azure Policy-Add-On aktiviert ist.
1. Wählen Sie die Seite **Parameter** aus, und ändern Sie **Auswirkung** von `audit` in `deny`, um neue Bereitstellungen zu blockieren, die gegen die Baseline-Initiative verstoßen. Sie können auch zusätzliche Namespaces hinzufügen, die Sie von der Auswertung ausschließen möchten. Übernehmen Sie für dieses Beispiel die Standardwerte.
1. Wählen Sie **Überprüfen und erstellen** und dann **Erstellen** aus, um die Richtlinienzuweisung zu übermitteln.

## <a name="create-and-assign-a-custom-policy-definition-preview"></a>Erstellen und Zuweisen einer benutzerdefinierten Richtliniendefinition (Vorschau)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Mit benutzerdefinierten Richtlinien können Sie Regeln für die Verwendung von Azure definieren. Beispielsweise können Sie Folgendes erzwingen:
- Sicherheitsmaßnahmen
- Kostenverwaltung
- Organisationsspezifische Regeln (z. B. Benennung oder Speicherorte)

Überprüfen Sie vor dem Erstellen einer benutzerdefinierten Richtlinie die [Liste der gängigen Muster und Beispiele][azure-policy-samples], um zu überprüfen, ob Ihr Anwendungsfall bereits behandelt wurde.

Benutzerdefinierte Richtliniendefinitionen werden in JSON geschrieben. Weitere Informationen zum Erstellen einer benutzerdefinierten Richtlinie finden Sie unter [Azure Policy-Definitionsstruktur][azure-policy-definition-structure] und [Erstellen einer benutzerdefinierten Richtliniendefinition][custom-policy-tutorial-create].

> [!NOTE]
> Azure Policy verwendet jetzt eine neue Eigenschaft, die als *templateInfo* bezeichnet wird und es Benutzern ermöglicht, den Quelltyp für die Einschränkungsvorlage zu definieren. Durch Definieren von *templateInfo* in Richtliniendefinitionen müssen Benutzer keine *constraintTemplate*- oder *constraint*-Eigenschaften mehr definieren. Benutzer müssen allerdings weiterhin *apiGroup*- und *kind*-Eigenschaften definieren. Weitere Informationen hierzu finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen][azure-policy-effects-audit].

Nachdem Ihre benutzerdefinierte Richtliniendefinition erstellt wurde, finden Sie unter [Zuweisen einer Richtliniendefinition][custom-policy-tutorial-assign] eine exemplarische Vorgehensweise als Schrittanleitung zum Zuweisen der Richtlinie zu Ihrem Kubernetes-Cluster.

## <a name="validate-a-azure-policy-is-running"></a>Überprüfen, ob Azure Policy ausgeführt wird

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Richtlinienzuweisungen auf Ihren Cluster angewendet werden:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Die Synchronisierung der Richtlinienzuweisungen in den einzelnen Clustern kann bis zu [20 Minuten dauern][azure-policy-assign-policy].

Die Ausgabe sollte in etwa wie folgt aussehen:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Überprüfen der Ablehnung eines privilegierten Pods

Testen Sie als Erstes, was passiert, wenn Sie einen Pod mit dem Sicherheitskontext `privileged: true` planen. Dieser Sicherheitskontext weitet die Podberechtigungen aus. Die Initiative lässt keine privilegierten Pods zu, sodass die Anforderung abgelehnt wird, was wiederum zur Ablehnung der Bereitstellung führt.

Erstellen Sie eine Datei mit dem Namen `nginx-privileged.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Erstellen Sie den Pod mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f nginx-privileged.yaml
```

Der Pod kann wie erwartet nicht geplant werden, wie in der folgenden Beispielausgabe zu sehen:

```console
$ kubectl apply -f nginx-privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Da der Pod die Planungsphase nicht erreicht, müssen keine Ressourcen gelöscht werden, und Sie können direkt mit dem nächsten Schritt fortfahren.

### <a name="test-creation-of-an-unprivileged-pod"></a>Testen der Erstellung eines nicht privilegierten Pods

Im vorherigen Beispiel hat das Containerimage automatisch versucht, Stammberechtigungen zu verwenden, um NGINX an den Port 80 zu binden. Diese Anforderung wurde von der Richtlinieninitiative abgelehnt, sodass der Pod nicht gestartet werden kann. Lassen Sie uns jetzt versuchen, denselben NGINX-Pod ohne privilegierten Zugriff auszuführen.

Erstellen Sie eine Datei mit dem Namen `nginx-unprivileged.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Erstellen Sie den Pod mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Der Pod wird erfolgreich geplant. Wenn Sie mithilfe des Befehls [kubectl get pods][kubectl-get] den Status des Pods überprüfen, sehen Sie, dass der Pod ausgeführt wird (*Running*):

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Dieses Beispiel zeigt die Baseline-Initiative, die sich nur auf Bereitstellungen auswirkt, die gegen Richtlinien in der Sammlung verstoßen. Zulässige Bereitstellungen funktionieren weiterhin.

Löschen Sie den nicht privilegierten NGINX-Pod mithilfe des Befehls [kubectl delete][kubectl-delete], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Deaktivieren einer Richtlinie oder Initiative

So entfernen Sie die Baseline-Initiative

1. Navigieren Sie im Azure-Portal zum Richtlinienbereich.
1. Wählen Sie im linken Bereich **Zuweisungen** aus.
1. Klicken Sie neben der Initiative `Kubernetes cluster pod security baseline standards for Linux-based workloads` auf die Schaltfläche mit den Auslassungspunkten ( **...** ).
1. Wählen Sie **Zuweisung löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Funktionsweise von Azure Policy finden Sie hier:

- [Azure Policy – Übersicht][azure-policy]
- [Azure Policy-Initiativen und -Richtlinien für AKS][aks-policies]
- Entfernen Sie das [Azure Policy-Add-On][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[azure-policy-effects-audit]: ../governance/policy/concepts/effects.md#audit-properties
[custom-policy-tutorial-create]: ../governance/policy/tutorials/create-custom-policy-definition.md
[custom-policy-tutorial-assign]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-policy-definition
[azure-policy-samples]: ../governance/policy/samples/index.md
[azure-policy-definition-structure]: ../governance/policy/concepts/definition-structure.md
