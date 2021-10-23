---
title: Szenarien zum Authentifizieren per Azure Container Registry über Kubernetes
description: Übersicht über Optionen und Szenarien für die Authentifizierung bei einer Azure-Containerregistrierung über einen Kubernetes-Cluster zum Pullen von Containerimages
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/20/2021
ms.openlocfilehash: 2fc24714183438f9d740de79f9a9e9ce11f17391
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545395"
---
# <a name="scenarios-to-authenticate-with-azure-container-registry-from-kubernetes"></a>Szenarien zum Authentifizieren per Azure Container Registry über Kubernetes


Sie können eine Azure-Containerregistrierung als Quelle für Containerimages für Kubernetes verwenden, einschließlich von Ihnen verwalteter Cluster, verwalteter Cluster, die in [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md) oder anderen Clouds gehostet werden, sowie „lokaler“ Kubernetes-Konfigurationen wie [minikube](https://minikube.sigs.k8s.io/) und [kind](https://kind.sigs.k8s.io/). 

Zum Pullen von Images aus einer Azure-Containerregistrierung in Ihren Kubernetes-Cluster muss ein Authentifizierungs- und Autorisierungsmechanismus eingerichtet werden. Wählen Sie je nach Clusterumgebung eine der folgenden Methoden aus:

## <a name="scenarios"></a>Szenarien

| Kubernetes-Cluster |Authentifizierungsmethode  | BESCHREIBUNG  | Beispiel | 
|---------|---------|---------|----------|
| AKS-Cluster |Verwaltete AKS-Identität    |  Aktivieren Sie die vom AKS-Kubelet [verwaltete Identität](../aks/use-managed-identity.md), um Images aus einer angefügten Azure-Containerregistrierung zu pullen.<br/><br/> Registrierung und Cluster müssen sich im selben Active Directory-Mandanten befinden. Sie können zum selben Azure-Abonnement gehören, müssen das aber nicht unbedingt.      | [Authentifizieren per Azure Container Registry über Azure Kubernetes Service](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)| 
| AKS-Cluster | AKS-Dienstprinzipal     | Aktivieren Sie den [AKS-Dienstprinzipal](../aks/kubernetes-service-principal.md) mit Berechtigungen für eine Azure-Zielcontainerregistrierung.<br/><br/>Registrierung und Cluster können zum selben Azure-Abonnement oder zu verschiedenen Abonnements gehören oder sich im selben Azure Active Directory-Mandanten befinden oder in verschiedenen.        | [Pullen von Images aus einer Azure-Containerregistrierung in einen AKS-Cluster in einem anderen AD-Mandanten](authenticate-aks-cross-tenant.md)
| Anderer Kubernetes-Cluster als AKS |Pod: [imagePullSecrets](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)   |  Verwenden Sie den allgemeinen Kubernetes-Mechanismus, um Registrierungsanmeldeinformationen für Podbereitstellungen zu verwalten.<br/><br/>Konfigurieren Sie den AD-Dienstprinzipal, das repositorybezogene Token oder andere unterstützte [Registrierungsanmeldeinformationen](container-registry-authentication.md).  | [Pullen von Images aus einer Azure-Containerregistrierung in einen Kubernetes-Cluster mithilfe eines Pullgeheimnisses](container-registry-auth-kubernetes.md) | 



## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Authentifizierung mit einer Azure-Containerregistrierung](container-registry-authentication.md).
