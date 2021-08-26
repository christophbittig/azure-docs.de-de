---
title: Bewährte Methoden für die Verwaltung der Identität
titleSuffix: Azure Kubernetes Service
description: Lernen Sie die Best Practices für Clusteroperator zum Verwalten der Authentifizierung und Autorisierung für Cluster in Azure Kubernetes Service (AKS) kennen.
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: a29bd1513f021be03cf6c6bd4aa83d13062de170
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356340"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Best Practices für die Authentifizierung und Autorisierung in Azure Kubernetes Service (AKS)

Beim Bereitstellen und Verwalten von Clustern in Azure Kubernetes Service (AKS) implementieren Sie Möglichkeiten zum Verwalten des Zugriffs auf Ressourcen und Dienste. Ohne diese Steuerungen kann Folgendes der Fall sein:
* Konten können Zugriff auf unnötige Ressourcen und Dienste haben. 
* Die Nachverfolgung, welcher Satz von Anmeldeinformationen zum Durchführen von Änderungen verwendet wurde, kann schwierig sein.

Dieser Artikel zu Best Practices konzentriert sich darauf, wie ein Clusteroperator Zugriff und Identität für AKS-Cluster verwalten kann. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Authentifizieren von Benutzern von AKS-Clustern mit Azure Active Directory
> * Steuern des Zugriffs auf Ressourcen mit der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC)
> * Verwenden von Azure RBAC zur differenzierten Steuerung des Zugriffs auf die AKS-Ressource, die Kubernetes-API im großen Stil und `kubeconfig`
> * Verwenden einer verwalteten Identität zur Authentifizierung von Pods bei anderen Diensten

## <a name="use-azure-active-directory-azure-ad"></a>Verwenden von Azure Active Directory (Azure AD)

> **Best Practices-Leitfaden** 
> 
> Stellen Sie AKS-Cluster mit Azure AD-Integration bereit. Die Verwendung von Azure AD zentralisiert die Identitätsverwaltungskomponente. Jede Änderung von Benutzerkonto oder Gruppenstatus wird automatisch im Zugriff auf den AKS-Cluster aktualisiert. Beschränken Sie Benutzer oder Gruppen mithilfe von [Rollen, Clusterrollen oder Bindungen](#use-kubernetes-role-based-access-control-kubernetes-rbac) auf ein Minimum an Berechtigungen.

Die Entwickler und Anwendungsbesitzer des Kubernetes-Clusters benötigen Zugriff auf verschiedene Ressourcen. Kubernetes bietet keine Identitätsverwaltungslösung, mit der Sie die Ressourcen steuern, mit denen Benutzer interagieren können. Stattdessen integrieren Sie Ihren Cluster in der Regel in eine vorhandene Identitätslösung. Hier kommt Azure AD ins Spiel – eine Lösung zur Identitätsverwaltung für Unternehmen, die in AKS-Cluster integriert werden kann.

Mit Clustern mit Azure AD-Integration in AKS erstellen Sie *Rollen* oder *Clusterrollen*, die Berechtigungen für den Zugriff auf Ressourcen definieren. Dann *binden* Sie die Rollen von Azure AD aus an Benutzer oder Gruppen. Weitere Informationen zu Kubernetes RBAC finden Sie im [nächsten Abschnitt](#use-kubernetes-role-based-access-control-kubernetes-rbac). Azure AD-Integration und das Steuern des Zugriffs auf Ressourcen ist im folgenden Diagramm dargestellt:

![Authentifizierung auf Clusterebene für die Azure Active Directory-Integration in AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Der Entwickler authentifiziert sich mit Azure AD.
1. Der Azure AD-Tokenausstellungs-Endpunkt stellt das Zugriffstoken aus.
1. Der Entwickler führt eine Aktion mithilfe des Azure AD-Tokens durch, wie etwa `kubectl create pod`.
1. Kubernetes überprüft das Token in Azure AD und ruft die Gruppenmitgliedschaften des Entwicklers ab.
1. Kubernetes RBAC und Clusterrichtlinien werden angewendet.
1. Ob die Anforderung des Entwicklers erfolgreich ist, hängt von der vorherigen Überprüfung der Azure AD-Gruppenmitgliedschaft sowie Kubernetes RBAC und Richtlinien ab.

Wie Sie einen AKS-Cluster erstellen, der Azure AD verwendet, erfahren Sie unter [Integrieren von Azure Active Directory in Azure Kubernetes Service][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Verwenden der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC)

> **Best Practices-Leitfaden**
> 
> Definieren Sie Benutzer- oder Gruppenberechtigungen für Clusterressourcen mit Kubernetes RBAC. Erstellen von Rollen und Bindungen, die die Mindestberechtigungen zuweisen. Führen Sie die Integration in Azure AD durch, um automatisch alle Änderungen von Benutzerstatus oder Gruppenmitgliedschaft zu aktualisieren und den Zugriff auf Clusterressourcen auf dem aktuellen Stand zu halten.

In Kubernetes stellen Sie eine differenzierte Steuerung des Zugriffs auf Clusterressourcen bereit. Sie definieren Berechtigungen auf Clusterebene oder für bestimmte Namespaces. Sie bestimmen, welche Ressourcen mit welchen Berechtigungen verwaltet werden können. Diese Rollen wenden Sie dann auf Benutzer oder Gruppen mit einer Bindung an. Weitere Informationen zu *Rollen*, *Clusterrollen* und *Bindungen* finden Sie unter [Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)][aks-concepts-identity].

Sie erstellen beispielsweise eine Rolle mit vollem Zugriff auf Ressourcen im Namespace mit dem Namen *finance-app*, wie im folgenden YAML-Beispielmanifest gezeigt:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Anschließend erstellen Sie eine Rollenbindung (RoleBinding) und binden den Azure AD-Benutzer *developer1\@contoso.com* an die Rollenbindung, wie im folgenden YAML-Beispielmanifest gezeigt:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Wenn *developer1\@contoso.com* für den AKS-Cluster authentifiziert wird, verfügt er über vollständige Berechtigungen für Ressourcen im *finance-app*-Namespace. So trennen Sie logisch den Zugriff auf Ressourcen und steuern ihn. Verwenden Sie Kubernetes RBAC in Verbindung mit der Azure AD-Integration.

Informationen zur Verwendung von Azure AD-Gruppen für die Steuerung des Zugriffs auf Kubernetes-Ressourcen per Kubernetes RBAC finden Sie unter [Steuern des Zugriffs auf Clusterressourcen per rollenbasierter Zugriffssteuerung und mit Azure Active Directory-Identitäten in Azure Kubernetes Service][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Verwenden von Azure RBAC 
> **Best Practices-Leitfaden** 
> 
> Verwenden Sie Azure RBAC, um die minimal erforderlichen Benutzer- und Gruppenberechtigungen für AKS-Ressourcen in einem oder mehreren Abonnements zu definieren.

Für den vollständigen Betrieb eines AKS-Clusters sind zwei Zugriffsebenen erforderlich: 
1. Zugriff auf die AKS-Ressource in Ihrem Azure-Abonnement. 

    Diese Zugriffsebene ermöglicht Ihnen Folgendes:
      * Steuern der Skalierung oder des Upgrades Ihres Clusters mithilfe der AKS-APIs
      * Pullen von `kubeconfig`

    Informationen zum Steuern des Zugriffs auf die AKS-Ressource und `kubeconfig` finden Sie unter [Beschränken des Zugriffs auf die Clusterkonfigurationsdatei](control-kubeconfig-access.md).

2. Zugriff auf die Kubernetes-API. 
    
    Diese Zugriffsebene wird auf eine der folgenden Arten gesteuert:
    * [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (herkömmlich) oder 
    * Integration von Azure RBAC in AKS für die Kubernetes-Autorisierung

    Informationen zur differenzierten Vergabe von Berechtigungen für die Kubernetes-API mit Azure RBAC finden Sie unter [Verwenden von Azure RBAC für Kubernetes-Autorisierung](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Verwenden verwalteter Podidentitäten

> **Best Practices-Leitfaden** 
> 
> Verwenden Sie keine festen Anmeldeinformationen in Pods oder Containerimages, da sie offengelegt oder missbraucht werden können. Verwenden Sie stattdessen *Podidentitäten*, um den Zugriff mit einer zentralen Azure AD-Identitätslösung automatisch anzufordern. 

> [!NOTE]
> Podidentitäten sind nur für die Verwendung mit Linux-Pods und -Containerimages vorgesehen. Die Unterstützung von verwalteten Podidentitäten für Windows-Container ist in Kürze verfügbar.

Für den Zugriff auf andere Azure-Dienste wie Cosmos DB, Key Vault oder Blob Storage benötigt der Pod entsprechende Anmeldeinformationen. Sie können Anmeldeinformationen für den Zugriff mit dem Containerimage definieren oder als Kubernetes-Geheimnis einfügen. In beiden Fällen müssen Sie diese manuell erstellen und zuweisen. In der Regel werden diese Anmeldeinformationen podübergreifend wiederverwendet und nicht regelmäßig rotiert.

Mit verwalteten Podidentitäten für Azure-Ressourcen fordern Sie automatisch Zugriff auf Dienste über Azure AD an. Verwaltete Podidentitäten befinden sich derzeit in der Vorschauphase für AKS. Informationen zu den ersten Schritten finden Sie in der Dokumentation [Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)](./use-azure-ad-pod-identity.md). 

Azure Active Directory-Podidentität unterstützt zwei Betriebsmodi:

1. Standardmodus: In diesem Modus werden die folgenden zwei Komponenten im AKS-Cluster bereitgestellt: 
    * [Managed Identity Controller (MIC)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/): Ein Kubernetes-Controller, der über den Kubernetes-API-Server auf Änderungen an Pods, [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) und [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) überwacht. Wenn eine relevante Änderung erkannt wird, fügt der MIC nach Bedarf [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/) hinzu oder löscht sie. Insbesondere bei der Planung eines Pods weist der MIC die verwaltete Identität in Azure der zugrunde liegenden VMSS zu, die während der Erstellungsphase vom Knotenpool verwendet wird. Wenn alle Pods, die die Identität verwenden, gelöscht werden, wird die Identität aus der VMSS des Knotenpools entfernt, es sei denn, dieselbe verwaltete Identität wird von anderen Pods verwendet. Der MIC führt ähnliche Aktionen aus, wenn AzureIdentity oder AzureIdentityBinding erstellt oder gelöscht werden.
    * [Node Management Identity (NMI)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/): Dies ist ein Pod, der auf jedem Knoten im AKS-Cluster als DaemonSet ausgeführt wird. NMI fängt Sicherheitstokenanforderungen an den [Azure Instance Metadata Service](../virtual-machines/linux/instance-metadata-service.md?tabs=linux) auf jedem Knoten ab, leitet sie an sich selbst weiter und überprüft, ob der Pod Zugriff auf die Identität hat, für die er ein Token anfordert, und ruft das Token im Auftrag der Anwendung vom Azure Active Directory-Mandanten ab.
2. Verwalteter Modus: In diesem Modus gibt es nur die NMI. Die Identität muss vom Benutzer manuell zugewiesen und verwaltet werden. Weitere Informationen finden Sie unter [Podidentität im verwalteten Modus](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/). Wenn Sie in diesem Modus den Befehl [az aks pod-identity add](/cli/azure/aks/pod-identity?view=azure-cli-latest#az_aks_pod_identity_add) verwenden, um einem Azure Kubernetes Service-Cluster (AKS) eine Podidentität hinzuzufügen, werden [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) und [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) in dem durch den Parameter `--namespace` angegebenen Namespace erstellt, während der AKS-Ressourcenanbieter die durch den Parameter `--identity-resource-id` angegebene verwaltete Identität der VM-Skalierunggruppe (VMSS) jedes Knotenpools im AKS-Cluster zuweist.

> [!NOTE]
> Wenn Sie stattdessen die Azure Active Directory-Podidentität mithilfe des [AKS-Cluster-Add-Ons](./use-azure-ad-pod-identity.md) installieren, verwendet das Setup den `managed` Modus.

Der `managed` Modus bietet folgende Vorteile gegenüber dem `standard` Modus:

1. Die Identitätszuweisung zur VMSS eines Knotenpools kann 40 bis 60 Sekunden dauern. Bei cron-Aufträgen oder Anwendungen, die Zugriff auf die Identität benötigen und die Zuweisungsverzögerung nicht tolerieren können, ist es am besten, den `managed` Modus zu verwenden, da die Identität der VMSS des Knotenpools manuell oder mit dem Befehl [az aks pod-identity add](/cli/azure/aks/pod-identity?view=azure-cli-latest#az_aks_pod_identity_add) vorab zugewiesen wird.
2. Im `standard` Modus erfordert der MIC Schreibberechtigungen für die vom AKS-Cluster verwendete VMSS und die Berechtigung `Managed Identity Operator` für die benutzerseitig zugewiesenen verwalteten Identitäten. Während der Ausführung im `managed mode` sind die Rollenzuweisungen nicht erforderlich, da es keinen MIC gibt.

Anstatt Anmeldeinformationen für Pods manuell zu definieren, fordern verwaltete Podidentitäten ein Zugriffstoken in Echtzeit an und verwenden es nur für den Zugriff auf die jeweils zugewiesenen Dienste. In AKS gibt es zwei Komponenten, die die Vorgänge bearbeiten, damit Pods verwaltete Identitäten verwenden können:

* **Der Node Management Identity-Server (NMI)** ist ein Pod, der auf jedem Knoten im AKS-Cluster als DaemonSet ausgeführt wird. Der NMI-Server überwacht Podanforderungen an Azure-Dienste.
* **Der Azure-Ressourcenanbieter** fragt den Kubernetes-API-Server ab und sucht nach einer Azure-Identitätszuordnung, die einem Pod entspricht.

Wenn Pods ein Sicherheitstoken von Azure Active Directory für den Zugriff auf einen Azure-Dienst anfordern, leiten Netzwerkregeln den Datenverkehr an den NMI-Server um. 
1. Der NMI-Server erfüllt folgende Aufgaben:
    * Identifizieren von Pods, die Zugriff auf Azure-Dienste anfordern, basierend auf ihrer Remoteadresse
    * Abfragen des Azure-Ressourcenanbieters 
1. Der Azure-Ressourcenanbieter sucht im AKS-Cluster nach Azure-Identitätszuordnungen.
1. Der NMI-Server fordert basierend auf der Identitätszuordnung des Pods ein Zugriffstoken von Azure AD an. 
1. Azure AD bietet Zugriff auf den NMI-Server, der an den Pod zurückgegeben wird. 
    * Dieses Zugriffstoken kann dann vom Pod verwendet werden, um Zugriff auf Azure-Dienste anzufordern.

Im folgenden Beispiel erstellt ein Entwickler einen Pod, der eine verwaltete Identität verwendet, um Zugriff auf Azure SQL-Datenbank anzufordern:

![Mit Podidentitäten kann ein Pod automatisch Zugriff auf andere Dienste anfordern.](media/operator-best-practices-identity/pod-identities.png)

1. Der Clusteroperator erstellt ein Dienstkonto, um Identitäten zuzuordnen, wenn Pods Zugriff auf Dienste anfordern.
1. Der NMI-Server wird bereitgestellt, um alle Podanforderungen zusammen mit dem Azure-Ressourcenanbieter für Zugriffstoken an Azure AD weiterzuleiten.
1. Ein Entwickler stellt einen Pod mit einer verwalteten Identität bereit, die ein Zugriffstoken über den NMI-Server anfordert.
1. Das Token wird an den Pod zurückgegeben und für den Zugriff auf Azure SQL-Datenbank verwendet.

> [!NOTE]
> Verwaltete Podidentitäten befinden sich zurzeit in der Vorschauversion.

Informationen zur Verwendung von verwalteten Podidentitäten finden Sie unter [Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Nächste Schritte

Dieser Best Practices-Artikel konzentriert sich auf Authentifizierung und Autorisierung für Ihre Cluster und Ressourcen. Um einige dieser Best Practices zu implementieren, lesen Sie folgende Artikel:

* [Integrieren von Azure Active Directory in AKS][aks-aad]
* [Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

Weitere Informationen zu Clustervorgängen in AKS finden Sie in den folgenden Best Practices:

* [Best Practices für Clusterisolierung in Azure Kubernetes Service (AKS)][aks-best-practices-cluster-isolation]
* [Best Practices für grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-scheduler]
* [Best Practices für erweiterte Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md