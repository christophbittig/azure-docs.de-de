---
title: Konzepte – Zugriff und Identität in Azure Kubernetes Service (AKS)
description: Erfahren Sie mehr über Zugriff und Identität in Azure Kubernetes Service (AKS), z. B. zu den Bereichen Integration von Azure Active Directory, rollenbasierte Kubernetes-Zugriffssteuerung (Kubernetes RBAC) sowie Rollen und Bindungen.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 70b8715119eb221fc860ba6e7a26b92bffadd12f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744918"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)

Sie können den Zugriff auf Kubernetes-Cluster auf verschiedene Arten authentifizieren, autorisieren, sichern und steuern. 
* Mit der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes RBAC) können Sie den Zugriff von Benutzern, Gruppen und Dienstkonten auf die Ressourcen beschränken, die wirklich benötigt werden. 
* Mit Azure Kubernetes Service (AKS) können Sie die Sicherheits- und Berechtigungsstruktur über Azure Active Directory und Azure RBAC weiter verbessern. 

Mit Kubernetes RBAC und AKS können Sie den Zugriff auf den Cluster schützen, und es werden nur die mindestens erforderlichen Berechtigungen für Entwickler und Operatoren bereitgestellt.

In diesem Artikel werden die wichtigsten Konzepte für die Authentifizierung und Zuweisung von Berechtigungen in AKS vorgestellt.

## <a name="aks-service-permissions"></a>AKS-Dienstberechtigungen

Beim Erstellen eines Clusters werden von AKS Ressourcen generiert oder geändert, die für die Erstellung und Ausführung des Clusters im Namen des Benutzers benötigt werden (z. B. VMs und NICs). Diese Identität unterscheidet sich von der Identitätsberechtigung des Clusters, die während der Erstellung des Clusters erstellt wird.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identität für die Erstellung und den Betrieb des Clusters: Berechtigungen

Die folgenden Berechtigungen werden von der Identität benötigt, die zum Erstellen und Betreiben des Clusters verwendet wird.

| Berechtigung | `Reason` |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Erforderlich zum Lesen der Datenträgerverschlüsselungssatz-ID. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Erforderlich zum Aktualisieren von Näherungsplatzierungsgruppen. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Erforderlich zum Konfigurieren von Anwendungsgateways und Beitreten zum Subnetz. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Erforderlich zum Konfigurieren der Netzwerksicherheitsgruppe für das Subnetz, wenn ein benutzerdefiniertes VNET verwendet wird.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Erforderlich zum Konfigurieren der ausgehenden öffentlichen IP-Adressen auf der Load Balancer Standard-Instanz. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Erforderlich zum Erstellen und Aktualisieren von Log Analytics-Arbeitsbereichen und der Azure-Überwachung für Container. |

### <a name="aks-cluster-identity-permissions"></a>AKS-Clusteridentität: Berechtigungen

Die folgenden Berechtigungen werden von der AKS-Clusteridentität verwendet, die erstellt und dem AKS-Cluster zugeordnet wird. Die einzelnen Berechtigungen werden aus den folgenden Gründen verwendet:

| Berechtigung | `Reason` |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Zum Erstellen von Benutzern und zum Betreiben des Clusters erforderlich
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Erforderlich zum Konfigurieren des Lastenausgleichs für einen LoadBalancer-Dienst. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Erforderlich zum Ermitteln und Konfigurieren von öffentlichen IP-Adressen für einen LoadBalancer-Dienst. |
| `Microsoft.Network/publicIPAddresses/join/action` | Erforderlich zum Konfigurieren von öffentlichen IP-Adressen für einen LoadBalancer-Dienst. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Erforderlich zum Erstellen oder Löschen von Sicherheitsregeln für einen LoadBalancer-Dienst. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Erforderlich zum Konfigurieren von AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Erforderlich zum Konfigurieren von Speicherkonten für AzureFile oder AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Erforderlich zum Konfigurieren von Routingtabellen und Routen für Knoten. |
| `Microsoft.Compute/virtualMachines/read` | Erforderlich zum Suchen nach Informationen für virtuelle Computer in einer VM-Verfügbarkeitsgruppe, z. B. Zonen, Fehlerdomäne, Größe und Datenträger. |
| `Microsoft.Compute/virtualMachines/write` | Erforderlich, um AzureDisks an einen virtuellen Computer in einer VM-Verfügbarkeitsgruppe anzufügen. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Erforderlich zum Suchen nach Informationen für virtuelle Computer in einer VM-Skalierungsgruppe, z. B. Zonen, Fehlerdomäne, Größe und Datenträger. |
| `Microsoft.Network/networkInterfaces/write` | Erforderlich, um einen virtuellen Computer aus einer VM-Verfügbarkeitsgruppe dem Back-End-Adresspool eines Lastenausgleichsmoduls hinzuzufügen. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Erforderlich, um eine VM-Skalierungsgruppe dem Back-End-Adresspool eines Lastenausgleichsmoduls hinzuzufügen und Knoten in einer VM-Skalierungsgruppe aufzuskalieren. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Erforderlich, um AzureDisks anzufügen und einen virtuellen Computer aus einer VM-Skalierungsgruppe dem Lastenausgleichsmodul hinzuzufügen. |
| `Microsoft.Network/networkInterfaces/read` | Erforderlich, um für virtuelle Computer in einer VM-Verfügbarkeitsgruppe nach internen IP-Adressen und Back-End-Adresspools eines Lastenausgleichsmoduls zu suchen. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Erforderlich, um für einen virtuellen Computer in einer VM-Skalierungsgruppe nach internen IP-Adressen und Back-End-Adresspools eines Lastenausgleichsmoduls zu suchen. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Erforderlich, um in einer VM-Skalierungsgruppe nach öffentlichen IP-Adressen für einen virtuellen Computer zu suchen. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Erforderlich für die Überprüfung, ob für das interne Lastenausgleichsmodul in einer anderen Ressourcengruppe ein Subnetz vorhanden ist. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Erforderlich zum Konfigurieren von Momentaufnahmen für AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Erforderlich, um nach VM-Größen zur Ermittlung von Grenzwerten für AzureDisk-Volumes zu suchen. |

### <a name="additional-cluster-identity-permissions"></a>Clusteridentität: Weitere Berechtigungen

Beim Erstellen eines Clusters mit bestimmten Attributen benötigen Sie die folgenden zusätzlichen Berechtigungen für die Clusteridentität. Da diese Berechtigungen nicht automatisch zugewiesen werden, müssen Sie sie der Clusteridentität nach der Erstellung hinzufügen.

| Berechtigung | `Reason` |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Erforderlich, wenn eine Netzwerksicherheitsgruppe in einer anderen Ressourcengruppe verwendet wird. Erforderlich zum Konfigurieren von Sicherheitsregeln für einen LoadBalancer-Dienst. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Erforderlich, wenn ein Subnetz in einer anderen Ressourcengruppe, z. B. einem benutzerdefinierten VNET, verwendet wird. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Erforderlich, wenn ein Subnetz mit einer zugeordneten Routingtabelle in einer anderen Ressourcengruppe, z. B. einem VNET mit einer benutzerdefinierten Routingtabelle, verwendet wird. Erforderlich zum Überprüfen, ob für das Subnetz in der anderen Ressourcengruppe bereits ein Subnetz vorhanden ist. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Erforderlich, wenn ein internes Lastenausgleichsmodul in einer anderen Ressourcengruppe verwendet wird. Erforderlich für die Überprüfung, ob für das interne Lastenausgleichsmodul in der Ressourcengruppe bereits ein Subnetz vorhanden ist. |
| `Microsoft.Network/privatednszones/*` | Erforderlich, wenn eine private DNS-Zone in einer anderen Ressourcengruppe, z. B. einer benutzerdefinierten privaten DNS-Zone, verwendet wird. |

## <a name="aks-node-access"></a>AKS-Knotenzugriff

Standardmäßig ist kein Knotenzugriff für AKS erforderlich.  Der folgende Zugriff ist für den Knoten notwendig, wenn eine bestimmte Komponente genutzt wird.

| Zugriff | `Reason` |
|---|---|
| `kubelet` | Erforderlich, damit der Kunde dem ACR MSI-Zugriff gewähren kann. |
| `http app routing` | Erforderlich für die Schreibberechtigung auf „beliebiger Name“.aksapp.io. |
| `container insights` | Erforderlich, damit der Kunde dem Log Analytics-Arbeitsbereich Berechtigungen gewähren kann. |

## <a name="kubernetes-rbac"></a>RBAC in Kubernetes

Kubernetes RBAC ermöglicht eine präzise Filterung von Benutzeraktionen. Der Steuerungsmechanismus ist wie folgt:
* Sie weisen Benutzern oder Benutzergruppen die Berechtigung zum Erstellen und Ändern von Ressourcen oder zum Anzeigen von Protokollen zur Ausführung von Anwendungsworkloads zu. 
* Sie können Berechtigungen auf einen einzelnen Namespace begrenzen oder für den gesamten AKS-Cluster erteilen. 
* Sie erstellen *Rollen*, um Berechtigungen zu definieren, und weisen dann Benutzern diese Rollen mit *Rollenbindungen* zu.

Weitere Informationen finden Sie unter [Verwenden der Kubernetes RBAC-Autorisierung][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Rollen und Clusterrollen

#### <a name="roles"></a>Rollen
Bevor Sie Benutzern mit Kubernetes RBAC Berechtigungen zuweisen, definieren Sie Benutzerberechtigungen als *Rolle*. Erteilen Sie Berechtigungen innerhalb eines Namespace mithilfe von Rollen. 

> [!NOTE]
> Mit Kubernetes-Rollen werden Berechtigungen *erteilt* und nicht *verweigert*.

Wenn Sie Berechtigungen für den gesamten Cluster oder für Clusterressourcen außerhalb eines bestimmten Namespace erteilen möchten, können Sie stattdessen *Clusterrollen* verwenden.

#### <a name="clusterroles"></a>Clusterrollen

Eine Clusterrolle erteilt Berechtigungen für Ressourcen im gesamten Cluster und nicht nur in einem bestimmten Namespace und wendet diese an.

### <a name="rolebindings-and-clusterrolebindings"></a>Rollenbindungen und Clusterrollenbindungen

Nachdem Sie Rollen definiert haben, um Berechtigungen für Ressourcen zu gewähren, weisen Sie diese Kubernetes RBAC-Berechtigungen mit einer *Rollenbindung* zu. Wenn Ihr AKS-Cluster [in Azure Active Directory (Azure AD) integriert ist](#azure-ad-integration), werden mit Rollenbindungen Berechtigungen für Azure AD-Benutzer zum Ausführen von Aktionen innerhalb des Clusters erteilt. Weitere Informationen dazu finden Sie unter [Steuern des Zugriffs auf Clusterressourcen per rollenbasierter Zugriffssteuerung von Kubernetes und mit Azure Active Directory-Identitäten](azure-ad-rbac.md).

#### <a name="rolebindings"></a>Rollenbindungen

Weisen Sie Benutzern Rollen für einen bestimmten Namespace mithilfe von Rollenbindungen zu. Mit Rollenbindungen können Sie einen einzelnen AKS-Cluster logisch aufteilen und den Benutzern nur den Zugriff auf die Anwendungsressourcen im jeweils zugewiesenen Namespace erlauben. 

Wenn Sie Rollen über den gesamten Cluster oder an Clusterressourcen außerhalb eines bestimmten Namespace binden möchten, verwenden Sie stattdessen *Clusterrollenbindungen*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Mit einer Clusterrollenbindung binden Sie Rollen an Benutzer und wenden sie auf Ressourcen im gesamten Cluster und nicht auf einen bestimmten Namespace an. Mit diesem Ansatz können Sie Ihren Administratoren oder Supporttechnikern Zugriff auf alle Ressourcen im AKS-Cluster gewähren.


> [!NOTE]
> Microsoft/AKS führt alle Clusteraktionen mit Benutzereinwilligung unter der integrierten Kubernetes-Rolle `aks-service` und der integrierten Rollenbindung `aks-service-rolebinding` durch. 
> 
> Diese Rolle ermöglicht es AKS, Clusterprobleme zu beheben und zu diagnostizieren, damit können jedoch keine Berechtigungen geändert oder Rollen bzw. Rollenbindungen erstellt oder andere Aktionen mit hohen Berechtigungen ausgeführt werden. Der Rollenzugriff ist nur unter aktiven Supporttickets mit Just-in-time-Zugriff (JIT) aktiviert. [Hier finden Sie weitere Informationen zu AKS-Supportrichtlinien](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes-Dienstkonten

*Dienstkonten* sind einer der Hauptbenutzertypen in Kubernetes. Die Kubernetes-API enthält und verwaltet Dienstkonten. Anmeldeinformationen für Dienstkonten werden als Kubernetes-Geheimnisse gespeichert. Dadurch können sie von autorisierten Pods für die Kommunikation mit dem API-Server verwendet werden. Die meisten API-Anforderungen stellen einen Authentifizierungstoken für ein Dienstkonto oder ein normales Benutzerkonto bereit.

Normale Benutzerkonten ermöglichen den eher herkömmlichen Zugriff für Personen wie Administratoren oder Entwickler und nicht nur für Dienste und Prozesse. Kubernetes bietet zwar keine Identitätsverwaltungslösung zum Speichern regulärer Benutzerkonten und Kennwörter, aber Sie können externe Identitätslösungen in Kubernetes integrieren. Bei AKS-Clustern fungiert Azure AD als integrierte Identitätsverwaltungslösung.

Weitere Informationen zu den Identitätsoptionen in Kubernetes finden Sie unter [Authentifizierung in Kubernetes][kubernetes-authentication].

## <a name="azure-ad-integration"></a>Azure AD-Integration

Verbessern Sie die Sicherheit Ihres AKS-Clusters mit Azure AD-Integration. Azure AD ist ein mehrinstanzenfähiger, cloudbasierter Verzeichnis- und Identitätsverwaltungsdienst, der Kernverzeichnisdienste, Anwendungszugriffsverwaltung und Identitätsschutz in sich vereint und auf jahrzehntelange Erfahrung mit der Identitätsverwaltung in Unternehmen aufbaut. Mit Azure AD können Sie lokale Identitäten in AKS-Cluster integrieren und so eine zentrale Quelle für die Kontoverwaltung und Sicherheit bereitstellen.

![Azure Active Directory-Integration mit AKS-Clustern](media/concepts-identity/aad-integration.png)

Mit in Azure AD integrierten AKS-Clustern können Sie Benutzern oder Gruppen Zugriff auf Kubernetes-Ressourcen in einem Namespace oder im ganzen Cluster gewähren. 

1. Zum Abrufen eines `kubectl`-Konfigurationskontexts führt ein Benutzer den Befehl [az aks get-credentials][az-aks-get-credentials] aus. 
1. Wenn ein Benutzer mit dem AKS-Cluster mit `kubectl` interagiert, wird er aufgefordert, sich mit seinen Azure AD-Anmeldeinformationen anzumelden. 

Dieser Ansatz stellt eine zentrale Quelle für die Verwaltung von Benutzerkonten und Anmeldekennwörtern bereit. Der Benutzer kann nur auf die Ressourcen zugreifen, die der Clusteradministrator definiert hat.

Die Azure AD-Authentifizierung wird für AKS-Cluster mit OpenID Connect bereitgestellt. OpenID Connect ist eine Identitätsebene, die auf dem OAuth 2.0-Protokoll aufbaut. Weitere Informationen zu OpenID Connect finden Sie in der [OpenID Connect-Dokumentation][openid-connect]. Innerhalb des Kubernetes-Clusters werden Authentifizierungstoken mithilfe der [Webhooktokenauthentifizierung][webhook-token-docs] überprüft. Die Webhooktokenauthentifizierung wird als Teil des AKS-Clusters konfiguriert und verwaltet.

### <a name="webhook-and-api-server"></a>Webhook und API-Server

![Authentifizierungsablauf für Webhook und API-Server](media/concepts-identity/auth-flow.png)

Wie in der obigen Abbildung gezeigt, ruft der API-Server den AKS-Webhookserver auf und führt die folgenden Schritte aus:

1. `kubectl` verwendet die Azure AD-Clientanwendung zum Anmelden von Benutzern mit dem [OAuth 2.0-Flow für die Geräteautorisierungsgenehmigung](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD stellt dabei ein access_token, ein id_token und ein refresh_token bereit.
3. Der Benutzer sendet mit einem access_token aus `kubeconfig` eine Anforderung an `kubectl`.
4. `kubectl` sendet das access_token an den API-Server.
5. Der API-Server wird mit dem Auth-WebHook-Server konfiguriert, um die Validierung auszuführen.
6. Der Webhookserver für die Authentifizierung bestätigt, dass die Signatur des JSON-Webtokens gültig ist, indem der öffentliche Azure AD-Signaturschlüssel überprüft wird.
7. Die Serveranwendung verwendet vom Benutzer bereitgestellte Anmeldeinformationen, um die Gruppenmitgliedschaften des angemeldeten Benutzers aus der MS Graph-API abzufragen.
8. An den API-Server wird eine Antwort mit Benutzerinformationen gesendet, z. B. mit dem UPN-Anspruch (User Principal Name) des Zugriffstokens und der Gruppenmitgliedschaft des Benutzers basierend auf der Objekt-ID.
9. Die API führt eine Autorisierungsentscheidung basierend auf der Kubernetes-Rolle bzw. -Rollenbindung aus.
10. Nach der Autorisierung gibt der API-Server eine Antwort an `kubectl` zurück.
11. `kubectl` stellt Feedback für den Benutzer bereit.
 
In der [Schrittanleitung zu der von AKS verwalteten Azure AD-Integration](managed-aad.md) erfahren Sie, wie Sie AKS in Azure AD integrieren.

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure ist ein Autorisierungssystem, das auf [Azure Resource Manager](../azure-resource-manager/management/overview.md) basiert und eine präzise Verwaltung des Zugriffs auf Azure-Ressourcen ermöglicht.

| RBAC-System | Beschreibung |
|---|---|
| RBAC in Kubernetes | Ist für Kubernetes-Ressourcen innerhalb Ihres AKS-Clusters bestimmt. |
| Azure RBAC | Ist für Ressourcen innerhalb Ihres Azure-Abonnements bestimmt. |

Mit der rollenbasierten Zugriffssteuerung von Azure erstellen Sie eine *Rollendefinition*, welche die anzuwendenden Berechtigungen erläutert. Anschließend weisen Sie einem Benutzer oder einer Gruppe diese Rollendefinition über eine *Rollenzuweisung* für einen bestimmten *Bereich* zu. Der Bereich kann eine einzelne Ressource, eine Ressourcengruppe oder das gesamte Abonnement sein.

Weitere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)?][azure-rbac]

Für den vollständigen Betrieb eines AKS-Clusters sind zwei Zugriffsebenen erforderlich: 
* [Zugriff auf die AKS-Ressource in Ihrem Azure-Abonnement.](#azure-rbac-to-authorize-access-to-the-aks-resource) 
  * Steuern der Skalierung oder des Upgrades Ihres Clusters mithilfe der AKS-APIs
  * Pullen von `kubeconfig`
* Zugriff auf die Kubernetes-API. Dieser Zugriff wird mit einer der beiden folgenden Methoden gesteuert:
  * [Kubernetes RBAC](#kubernetes-rbac) (herkömmlich)
  * [Integration von Azure RBAC in AKS für die Kubernetes-Autorisierung](#azure-rbac-for-kubernetes-authorization)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC zum Autorisieren des Zugriffs auf die AKS-Ressource

Mit Azure RBAC können Sie Ihren Benutzern (oder Identitäten) einen präzisen Zugriff auf AKS-Ressourcen in einem Abonnement oder abonnementsübergreifend bereitstellen. Beispielsweise können Sie die [Rolle „Mitwirkender“ für Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) verwenden, um eine Skalierung und ein Upgrade Ihres Clusters durchzuführen. Währenddessen hat ein anderer Benutzer mit der [Administratorrolle für Azure Kubernetes Service-Cluster](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) nur die Berechtigung zum Pullen der `kubeconfig`-Administratordatei.

Alternativ können Sie dem Benutzer die allgemeine Rolle [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) zuweisen. Mit der allgemeinen Rolle „Mitwirkender“ können Benutzer die oben genannten Berechtigungen sowie alle Aktionen ausführen, die für die AKS-Ressource möglich sind, mit Ausnahme der Verwaltung von Berechtigungen.

[Verwenden Sie Azure RBAC, um den Zugriff auf die Kubernetes-Konfigurationsdatei in AKS zu definieren](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization"></a>Azure RBAC für die Kubernetes-Autorisierung

Bei der Azure RBAC-Integration verwendet AKS einen Kubernetes-Webhookserver für die Autorisierung, damit Sie Berechtigungen und Zuweisungen von in Azure AD integrierten Kubernetes-Clusterressourcen mit Azure-Rollendefinitionen und -Rollenzuweisungen verwalten können.

![Azure RBAC für die Kubernetes-Autorisierungsablauf](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Wie im Diagramm oben gezeigt, folgen bei der Verwendung der Azure RBAC-Integration alle Anforderungen an die Kubernetes-API demselben Authentifizierungsablauf, der im [Abschnitt zur Azure Active Directory-Integration](#azure-ad-integration) erläutert wurde. 

Wenn die Identität, die die Anforderung sendet, in Azure AD vorhanden ist, arbeitet Azure mit Kubernetes RBAC zusammen, um die Anforderung zu autorisieren. Wenn die Identität außerhalb von Azure AD vorhanden ist (z. B. ein Kubernetes-Dienstkonto), wird die normale Kubernetes RBAC zur Autorisierung verwendet.

In diesem Szenario verwenden Sie Azure RBAC-Mechanismen und -APIs, um Benutzern integrierte Rollen zuzuweisen oder benutzerdefinierte Rollen zu erstellen, wie es auch bei Kubernetes-Rollen der Fall ist. 

Mit diesem Feature gewähren Sie Benutzern nicht nur abonnementübergreifende Berechtigungen für die AKS-Ressource, sondern konfigurieren auch die Rolle und die Berechtigungen für Aktionen innerhalb dieser Cluster, die den Zugriff auf die Kubernetes-API steuern. Sie können beispielsweise die Rolle `Azure Kubernetes Service RBAC Reader` für den Abonnementbereich gewähren. Der Rollenempfänger kann dann alle Kubernetes-Objekte aus allen Clustern auflisten und abrufen, ohne sie zu ändern.

> [!IMPORTANT]
> Sie müssen Azure RBAC für die Kubernetes-Autorisierung aktivieren, bevor Sie dieses Feature verwenden können. Ausführlichere Informationen und schrittweise Anleitungen finden Sie unter [Verwenden von Azure RBAC für Kubernetes-Autorisierung](manage-azure-rbac.md).

#### <a name="built-in-roles"></a>Integrierte Rollen

AKS stellt die folgenden vier integrierten Rollen bereit. Sie sind mit den [integrierten Rollen in Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) vergleichbar, weisen aber einige Unterschiede wie die Unterstützung von CRDs auf. Sehen Sie sich die vollständige Liste der Aktionen an, die für die einzelnen [integrierten Azure-Rollen](../role-based-access-control/built-in-roles.md) zulässig sind.

| Rolle                                | Beschreibung  |
|-------------------------------------|--------------|
| RBAC-Leser von Azure Kubernetes Service  | Ermöglicht schreibgeschützten Zugriff, um die meisten Objekte in einem Namespace anzuzeigen. <br> Ermöglicht nicht das Anzeigen von Rollen oder Rollenbindungen.<br> Ermöglicht nicht das Anzeigen von `Secrets`. Das Lesen des `Secrets`-Inhalts ermöglicht den Zugriff auf `ServiceAccount`-Anmeldeinformationen im Namespace, was den API-Zugriff als beliebiges `ServiceAccount` im Namespace ermöglichen würde (eine Form von Berechtigungsausweitung).  |
| RBAC-Writer von Azure Kubernetes Service | Ermöglicht Lese-/Schreibzugriff auf die meisten Objekte in einem Namespace. <br> Ermöglicht nicht das Anzeigen oder Ändern von Rollen oder Rollenbindungen. <br> Ermöglicht den Zugriff auf `Secrets` und das Ausführen von Pods als beliebiges ServiceAccount im Namespace, sodass sie verwendet werden kann, um die API-Zugriffsebenen eines beliebigen ServiceAccount im Namespace zu erhalten. |
| RBAC-Administrator von Azure Kubernetes Service  | Ermöglicht Administratorzugriff, der in einem Namespace erteilt werden soll. <br> Ermöglicht Lese-/Schreibzugriff auf die meisten Ressourcen in einem Namespace (oder Clusterbereich), einschließlich der Möglichkeit zum Erstellen von Rollen und Rollenbindungen innerhalb des Namespace. <br> Ermöglicht keinen Schreibzugriff auf das Ressourcenkontingent oder den Namespace selbst. |
| RBAC-Clusteradministrator von Azure Kubernetes Service  | Ermöglicht Superuserzugriff, um beliebige Aktionen für beliebige Ressourcen auszuführen. <br> Ermöglicht die vollständige Kontrolle über alle Ressourcen im Cluster und in allen Namespaces. |


## <a name="summary"></a>Zusammenfassung

Die Tabelle gibt einen kurze Übersicht darüber, wie Sich Benutzer bei Kubernetes authentifizieren können, wenn Azure AD-Integration aktiviert ist. In allen Fällen lautet die Befehlssequenz des Benutzers wie folgt:
1. Ausführen von `az login` zur Authentifizierung bei Azure.
1. Ausführen von `az aks get-credentials`, um Anmeldeinformationen für den Cluster in `.kube/config` herunterzuladen.
1. Ausführung von `kubectl`-Befehlen. 
   * Der erste Befehl kann die browserbasierte Authentifizierung zum Authentifizieren beim Cluster auslösen, wie in der folgenden Tabelle beschrieben.

Im Azure-Portal können Sie Folgendes finden:
* Die *Rollenzuweisung* (Azure RBAC-Rollenzuweisung), auf die in der zweiten Spalte verwiesen wird, ist auf der Registerkarte **Zugriffssteuerung** angegeben. 
* Die Clusteradministrator-Azure AD-Gruppe wird auf der Registerkarte **Konfiguration** angezeigt.
  * Kann auch über den Parameternamen `--aad-admin-group-object-ids` in der Azure CLI gefunden werden.


| BESCHREIBUNG        | Rollenzuweisung erforderlich| Clusteradministrator-Azure AD-Gruppe(n) | Verwendung |
| -------------------|------------|----------------------------|-------------|
| Legacy-Administratoranmeldung mit Clientzertifikat| **Azure Kubernetes-Administratorrolle**. Diese Rolle ermöglicht die Verwendung von `az aks get-credentials` mit dem `--admin`-Flag, das ein [Legacy-Clusteradministratorzertifikat (nicht Azure AD)](control-kubeconfig-access.md) in `.kube/config` des Benutzers herunterlädt. Dies ist der einzige Zweck der „Azure Kubernetes-Administratorrolle“.|–|Wenn Sie permanent blockiert werden, indem Sie keinen Zugriff auf eine gültige Azure AD-Gruppe mit Zugriff auf Ihren Cluster haben.| 
| Azure AD mit manuellen (Cluster-)Rollenbindungen| **Azure Kubernetes-Benutzerrolle**. Mit der Rolle „Benutzer“ kann `az aks get-credentials` ohne `--admin`-Flag verwendet werden. (Dies ist der einzige Zweck der „Azure Kubernetes-Benutzerrolle“.) Das Ergebnis ist in einem Azure AD-fähigen Cluster das Herunterladen [eines leeren Eintrags](control-kubeconfig-access.md) in `.kube/config`, der bei erstmaliger Verwendung durch `kubectl` die browserbasierte Authentifizierung auslöst.| Der Benutzer befindet sich nicht in einer dieser Gruppen. Da der Benutzer in keiner Gruppe von Clusteradministratoren vorhanden ist, werden seine Rechte vollständig durch beliebige Rollenbindungen oder Clusterrollenbindungen gesteuert, die von Clusteradministratoren eingerichtet wurden. Die (Cluster-)Rollenbindungen [nominieren Azure AD-Benutzer oder Azure AD-Gruppen](azure-ad-rbac.md) als ihre `subjects`. Wenn solche Bindungen nicht eingerichtet wurden, kann der Benutzer keine `kubectl`-Befehle ausführen.|Wenn Sie eine differenzierte Zugriffssteuerung wünschen und Azure RBAC nicht für die Kubernetes-Autorisierung verwenden. Beachten Sie, dass der Benutzer, der die Bindungen einrichtet, sich mit einer der anderen Methoden anmelden muss, die in dieser Tabelle aufgeführt sind.|
| Azure AD von Mitglied der Gruppe „Administratoren“| Wie oben|Der Benutzer ist Mitglied einer der hier aufgelisteten Gruppen. AKS generiert automatisch eine Clusterrollenbindung, die alle aufgelisteten Gruppen an die Kubernetes-Rolle `cluster-admin` bindet. Daher können Benutzer in diesen Gruppen alle `kubectl`-Befehle als `cluster-admin` ausführen.|Wenn Sie Benutzern komfortabel vollständige Administratorrechte erteilen möchten und _nicht_ Azure RBAC für die Kubernetes-Autorisierung verwenden.|
| Azure AD mit Azure RBAC für Kubernetes-Autorisierung|Zwei Rollen: <br> Erstens **Azure Kubernetes-Benutzerrolle** (wie oben). <br> Zweitens eine der oben aufgeführten „Azure Kubernetes Service **RBAC**“-Rollen oder Ihre eigene benutzerdefinierte Alternative.|Das Feld „Administratorrollen“ auf der Registerkarte „Konfiguration“ ist irrelevant, wenn Azure RBAC für die Kubernetes-Autorisierung aktiviert ist.|Sie verwenden Azure RBAC für die Kubernetes-Autorisierung. Dieser Ansatz ermöglicht Ihnen eine differenzierte Steuerung, ohne Rollenbindungen oder Clusterrollenbindungen einrichten zu müssen.|

## <a name="next-steps"></a>Nächste Schritte

- Die ersten Schritte mit Azure AD und der rollenbasierten Zugriffssteuerung von Kubernetes sind unter [Integrieren von Azure Active Directory in AKS][aks-aad] beschrieben.
- Zugehörige bewährte Methoden finden Sie unter [Best Practices für die Authentifizierung und Autorisierung in Azure Kubernetes Service (AKS)][operator-best-practices-identity].
- Informationen zu den ersten Schritten mit Azure RBAC für die Kubernetes-Autorisierung finden Sie im [Artikel zum Autorisieren des Zugriffs im AKS-Cluster (Azure Kubernetes Service) mit Azure RBAC](manage-azure-rbac.md).
- Informationen zum Schützen der `kubeconfig`-Datei finden Sie unter [Beschränken des Zugriffs auf die Clusterkonfigurationsdatei](control-kubeconfig-access.md).

Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie in den folgenden Artikeln:

- [Kubernetes/AKS: Cluster und Workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS: Sicherheit][aks-concepts-security]
- [Kubernetes/AKS: virtuelle Netzwerke][aks-concepts-network]
- [Kubernetes/AKS: Speicher][aks-concepts-storage]
- [Kubernetes/AKS: Skalierung][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
