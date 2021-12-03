---
title: Durchführen des Onboardings von Anwendungen in Open Service Mesh
description: Hier erfahren Sie, wie Sie eine Anwendung in Open Service Mesh integrieren.
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 202702623353462bafd77002662a35e7c7b7d2f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066765"
---
# <a name="onboarding-applications-to-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Durchführen des Onboardings von Anwendungen in das OSM-Add-On (Open Service Mesh) für Azure Kubernetes Service (AKS)

Im folgenden Leitfaden erfahren Sie, wie Sie einen Kubernetes-Microservice in OSM integrieren.

## <a name="before-you-begin"></a>Voraussetzungen

Die in dieser Anleitung beschriebenen Schritte setzen voraus, dass Sie zuvor das OSM AKS Add-on für Ihren AKS-Cluster aktiviert haben. Lesen Sie andernfalls den Artikel [Bereitstellen des OSM-AKS-Add-Ons](./open-service-mesh-deploy-addon-az-cli.md), bevor Sie fortfahren. Außerdem muss Ihr AKS-Cluster Version Kubernetes `1.19+` und höher sein, die Kubernetes RBAC muss aktiviert sein und eine `kubectl` Verbindung mit dem Cluster hergestellt haben (wenn Sie Hilfe zu diesen Elementen benötigen, sehen Sie sich den [AKS-Schnellstart](./kubernetes-walkthrough.md)an und haben das AKS OSM-Add-on installiert.

Die folgenden Ressourcen müssen installiert sein:

- Azure CLI, Version 2.20.0 oder höher
- OSM-Add-On, Version v0.11.1 oder höher
- OSM-CLI, Version v0.11.1 oder höher

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Überprüfen Sie die OSM-Richtlinie (Open Service Mesh) für den Modus Permissiver Datenverkehr

Der Modus „Permissive Datenverkehr-Richtlinie“ in OSM ist ein Modus, in dem die Durchsetzung der [SMI](https://smi-spec.io/)-Verkehrsrichtlinie umgangen wird. In diesem Modus erkennt OSM automatisch Dienste, die Teil des Dienstnetzes sind, und programmiert Regeln für Datenverkehrsrichtlinien auf jedem Envoy-Proxy-Sidecar, um mit diesen Diensten kommunizieren zu können.

Führen Sie den folgenden Befehl aus, um den aktuellen Zuordnungsmodus des OSM für Ihren Cluster zu überprüfen:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o jsonpath='{.spec.traffic.enablePermissiveTrafficPolicyMode}{"\n"}'
true
```

Wenn **enablePermissiveTrafficPolicyMode** auf **true** festgelegt ist, können Sie Ihre Namespaces ohne jegliche Unterbrechung der Dienst-zu-Dienst-Kommunikation sicher integrieren. Wenn der **enablePermissiveTrafficPolicyMode** auf **false** konfiguriert ist, müssen Sie sicherstellen, dass Sie die richtigen [SMI-](https://smi-spec.io/)Verkehrszugriffsrichtlinienmanifeste bereitgestellt haben. Sie müssen auch sicherstellen, dass Sie ein Dienstkonto für jeden im Namensraum bereitgestellten Dienst haben. Ausführlichere Informationen zum toleranten Verkehrsmodus finden Sie im Artikel [Toleranter Verkehrsrichtlinienmodus](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="onboard-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Integrieren von Anwendungen mit Open Service Mesh (OSM) Permissive Traffic-Richtlinie konfiguriert als True

1. Machen Sie sich vor dem Integrieren von Anwendungen mit den [Anwendungsanforderungen](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/prereqs/) vertraut.

1. Wenn eine Anwendung im Mesh mit dem Kubernetes-API-Server kommunizieren muss, muss der Benutzer dies explizit zulassen. Hierzu kann entweder ein IP-Adressbereichsausschluss verwendet oder eine Richtlinie für ausgehenden Datenverkehr erstellt werden.

1. Integrieren von Kubernetes-Namespaces in OSM

    Führen Sie den Befehl `osm namespace add` aus, um einen Namespace mit Anwendungen zu integrieren, die von OSM verwaltet werden sollen:

    ```console
    $ osm namespace add <namespace>
    ```

    Durch den Befehl `osm namespace add` wird standardmäßig die automatische Sidecar-Einschleusung für Pods im Namespace ermöglicht.

    Verwenden Sie `osm namespace add <namespace> --disable-sidecar-injection`, wenn Sie die automatische Sidecar-Einschleusung im Rahmen der Registrierung eines Namespace im Mesh deaktivieren möchten.
    Nach dem Integrieren eines Namespace können Pods durch Konfigurieren der automatischen Sidecar-Einschleusung im Mesh registriert werden. Ausführlichere Informationen finden Sie im [Dokument zur Sidecar-Einschleusung](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/).

1.  Bereitstellen neuer Anwendungen oder erneutes Bereitstellen bereits vorhandener Anwendungen

    Für neue Bereitstellungen in integrierten Namespaces ist die automatische Sidecar-Einschleusung standardmäßig aktiviert. Das bedeutet, dass der Sidecar-Proxy von OSM automatisch in den Pod eingeschleust wird, wenn ein neuer Pod in einem verwalteten Namespace erstellt wird.
    Vorhandene Bereitstellungen müssen neu gestartet werden, damit OSM den Sidecar-Proxy bei der erneuten Erstellung des Pods automatisch einschleusen kann. Von einer Bereitstellung verwaltete Pods können mithilfe des Befehls `kubectl rollout restart deploy` neu gestartet werden.

    Konfigurieren Sie das zu verwendende Anwendungsprotokoll, um protokollspezifischen Datenverkehr ordnungsgemäß an Dienstports weiterzuleiten. Weitere Informationen finden Sie im [Leitfaden zur Wahl des Anwendungsprotokolls](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/app_protocol_selection/).


## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Onboarding bestehender bereitgestellter Anwendungen mit Open Service Mesh (OSM) Permissive Traffic-Richtlinie konfiguriert als False

Wenn die OSM-Konfiguration für die permissive Datenverkehrsrichtlinie auf gesetzt ist, benötigt`false`OSM explizite [SMI](https://smi-spec.io/)-Datenverkehrs-Zugriffsrichtlinien, die für die Dienst-zu-Dienst-Kommunikation innerhalb Ihres Clusters eingesetzt werden. Da OSM Kubernetes-Dienstkonten verwendet, um Zugriffssteuerungsrichtlinien zwischen Anwendungen im Mesh zu implementieren, wenden Sie [SMI](https://smi-spec.io/)-Richtlinien für den Datenverkehrszugriff an, um den Datenverkehrsfluss zwischen Anwendungen zu autorisieren.

Beispiele für SMI-Richtlinien finden Sie hier:
    - [demo/deploy-traffic-specs.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-specs.sh)
    - [demo/deploy-traffic-split.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-split.sh)
    - [demo/deploy-traffic-target.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-target.sh)


#### <a name="removing-namespaces"></a>Entfernen von Namespaces
Namespaces können mithilfe des Befehls `osm namespace remove` aus dem OSM-Mesh entfernt werden:

```console
$ osm namespace remove <namespace>
```

> [!NOTE]
>
> - Durch den Befehl **`osm namespace remove`** wird OSM lediglich angewiesen, keine Updates nicht mehr auf die Sidecar-Proxy-Konfigurationen im Namespace mehr anzuwenden. Die Proxy-Sidecars werden jedoch **nicht** entfernt. Die vorhandene Proxykonfiguration wird somit weiterhin verwendet, aber nicht mehr durch die OSM-Steuerungsebene aktualisiert. Wenn Sie die Proxys aus allen Pods entfernen möchten, entfernen Sie die Namespaces der Pods mithilfe der OSM-CLI aus dem Mesh, und stellen Sie die entsprechenden Pods oder Bereitstellungen erneut bereit.
