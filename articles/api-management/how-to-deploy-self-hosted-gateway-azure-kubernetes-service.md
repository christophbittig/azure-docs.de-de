---
title: Bereitstellen eines selbstgehosteten Gateways für Azure Kubernetes Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine selbstgehostete Gatewaykomponente von Azure API Management für Azure Kubernetes Service bereitstellen.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 06/11/2021
ms.author: apimpm
ms.openlocfilehash: c43f31be807d6a649cdd750ee15841a0ecbd7631
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300757"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Bereitstellen für Azure Kubernetes Service

In diesem Artikel werden die Schritte für die Bereitstellung einer selbstgehosteten Gatewaykomponente von Azure API Management für den [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) beschrieben. Informationen zur Bereitstellung eines selbstgehosteten Gateways in einem Kubernetes-Cluster finden Sie im [Artikel mit der Anleitung](how-to-deploy-self-hosted-gateway-kubernetes.md).

> [!NOTE]
> Sie können auch das selbstgehostete Gateway in einem [Azure Arc-fähigen Kubernetes-Cluster](how-to-deploy-self-hosted-gateway-azure-arc.md) als [Clustererweiterung](../azure-arc/kubernetes/extensions.md) bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
- [Erstellen eines Azure Kubernetes-Clusters](../aks/kubernetes-walkthrough-portal.md)
- [Stellen Sie eine Gatewayressource in Ihrer API Management-Instanz bereit](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Bereitstellen des selbstgehosteten Gateways für AKS

1. Wählen Sie **Gateways** unter **Bereitstellung und Infrastruktur** aus.
2. Wählen Sie die selbstgehostete Gatewayressource aus, die Sie bereitstellen möchten.
3. Wählen Sie **Bereitstellung** aus.
4. Im Textfeld **Token** wurde automatisch ein neues Token mit den Standardwerten **Ablauf** und **Geheimer Schlüssel** generiert. Passen Sie einen Wert oder beide Werte an, und wählen Sie **Generieren** aus, um ein neues Token zu erstellen.
5. Stellen Sie sicher, dass **Kubernetes** unter **Bereitstellungsskripts** ausgewählt ist.
6. Wählen Sie den **<Gatewayname>.yml**-Dateilink neben **Bereitstellung** aus, um die Datei herunterzuladen.
7. Passen Sie `config.service.endpoint`, die Portzuordnungen und den Containernamen in der YML-Datei nach Bedarf an.
8. Abhängig von Ihrem Szenario müssen Sie möglicherweise den [Diensttyp](../aks/concepts-network.md#services) ändern. 
    * Der Standardwert lautet `LoadBalancer`. Hierbei handelt es sich um den externen Lastenausgleich. 
    * Den [internen Lastenausgleich](../aks/internal-lb.md) können Sie verwenden, um den Zugriff auf das selbstgehostete Gateway auf interne Benutzer zu begrenzen. 
    * Im folgenden Beispiel wird `NodePort` verwendet.
1. Wählen Sie das Symbol **Kopieren** rechts neben dem Textfeld **Bereitstellen** aus, um den `kubectl`-Befehl in der Zwischenablage zu speichern.
1. Fügen Sie den Befehl in das Terminalfenster (oder Befehlsfenster) ein. Bei diesem Befehl wird erwartet, dass die heruntergeladene Umgebungsdatei im aktuellen Verzeichnis vorhanden ist.
    ```console
        kubectl apply -f <gateway-name>.yaml
    ```
1. Führen Sie den Befehl aus. Mit dem Befehl wird der Cluster angewiesen, folgende Aktionen auszuführen:
    * Ausführen des Containers mit dem aus der Microsoft Container Registry heruntergeladenen Image des selbstgehosteten Gateways 
    * Konfigurieren des Containers, sodass die Ports HTTP 8080 und HTTPS 443 verfügbar sind.
1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Gatewaypod ausgeführt wird. Ihr Podname lautet anders.
    ```console
    kubectl get pods
    NAME                                   READY     STATUS    RESTARTS   AGE
    contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
    ```
1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Gatewaydienst ausgeführt wird. Ihr Dienstname und die IP-Adressen lauten anders.
    ```console
    kubectl get services
    NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
    ```
1. Kehren Sie zurück zum Azure-Portal, und vergewissern Sie sich, dass der bereitgestellte Gatewayknoten einen fehlerfreien Status meldet.

> [!TIP]
> Verwenden Sie den Befehl <code>kubectl logs <gateway-pod-name></code>, um eine Momentaufnahme des Protokolls des selbstgehosteten Gateways anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum selbstgehosteten Gateway finden Sie unter [Azure API Management: Übersicht über das selbstgehostete Gateway](self-hosted-gateway-overview.md).
* Hier erfahren Sie, wie Sie [das selbstgehostete API Management-Gateway für Azure Arc-fähige Kubernetes-Cluster bereitstellen](how-to-deploy-self-hosted-gateway-azure-arc.md).
* Weitere Informationen zu [Azure Kubernetes Service](../aks/intro-kubernetes.md).
* Hier erfahren Sie mehr zum [Konfigurieren und Beibehalten von Protokollen in der Cloud](how-to-configure-cloud-metrics-logs.md).
* Hier erfahren Sie mehr zum [lokalen Konfigurieren und Beibehalten von Protokollen](how-to-configure-local-metrics-logs.md).
