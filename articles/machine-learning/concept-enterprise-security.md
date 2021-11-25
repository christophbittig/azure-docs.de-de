---
title: Unternehmenssicherheit und -governance
titleSuffix: Azure Machine Learning
description: 'Verwenden Sie Azure Machine Learning sicher: Authentifizierung, Autorisierung, Netzwerksicherheit, Datenverschlüsselung und Überwachung.'
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: d5034f59cc71352518c0570e525f952f0f657c71
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343133"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Sicherheit und Governance in Unternehmen für Azure Machine Learning

In diesem Artikel lernen Sie Sicherheits- und Governancefeatures kennen, die für Azure Machine Learning zur Verfügung stehen. Diese Features sind nützlich für Administratoren, DevOps und MLOps, die eine sichere, mit ihren Unternehmensrichtlinien kompatible Konfiguration erstellen möchten. Mit Azure Machine Learning und der Azure-Plattform haben Sie folgende Möglichkeiten:

* Einschränken des Zugriffs auf Ressourcen und Vorgänge durch Benutzerkonten oder Gruppen
* Einschränken der eingehenden und ausgehenden Netzwerkkommunikation
* Verschlüsseln von Daten während der Übertragung und im Ruhezustand
* Überprüfen auf Sicherheitsrisiken
* Anwenden und Überwachen von Konfigurationsrichtlinien

## <a name="restrict-access-to-resources-and-operations"></a>Einschränken des Zugriffs auf Ressourcen und Vorgänge

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) ist der Identitätsdienstanbieter für Azure Machine Learning. Damit können Sie die Sicherheitsobjekte (Benutzer, Gruppe, Dienstprinzipal und verwaltete Identität), die zum _Authentifizieren_ bei Azure-Ressourcen verwendet werden, erstellen und verwalten. Die mehrstufige Authentifizierung wird unterstützt, wenn Azure AD dafür konfiguriert ist.

Dies ist der Authentifizierungsprozess für Azure Machine Learning mit Multi-Factor Authentication in Azure AD:

1. Der Client meldet sich bei Azure AD an und ruft das Azure Resource Manager-Token ab.
1. Der Client übergibt das Token an Azure Resource Manager und an Azure Machine Learning.
1. Azure Machine Learning stellt ein Machine Learning Service-Token für das Benutzercomputeziel bereit (z. B. Azure Machine Learning-Computecluster). Dieses Token wird nach Abschluss der Ausführung vom Benutzercomputeziel für einen erneuten Aufruf von Machine Learning Service verwendet. Der Bereich ist auf den Arbeitsbereich beschränkt.

[![Authentifizierung in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Jedem Arbeitsbereich ist eine vom System zugewiesene [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) zugeordnet, die den gleichen Namen hat wie der Arbeitsbereich. Diese verwaltete Identität wird für den sicheren Zugriff auf Ressourcen verwendet, die vom Arbeitsbereich verwendet werden. Sie verfügt über die folgenden Azure RBAC-Berechtigungen für zugehörige Ressourcen:

| Resource | Berechtigungen |
| ----- | ----- |
| Arbeitsbereich | Mitwirkender |
| Speicherkonto | Mitwirkender an Storage-Blobdaten |
| Schlüsseltresor | Zugriff auf alle Schlüssel, Geheimnisse und Zertifikate |
| Azure Container Registry | Mitwirkender |
| Ressourcengruppe, die den Arbeitsbereich enthält | Mitwirkender |

Die systemseitig zugewiesene verwaltete Identität wird für die interne Dienst-zu-Dienst-Authentifizierung von Azure Machine Learning bei anderen Azure-Ressourcen verwendet. Das Identitätstoken ist für Benutzer nicht zugänglich und kann nicht von ihnen verwendet werden, um Zugriff auf diese Ressourcen zu erhalten. Benutzer können nur dann über die [Steuerungs- und Datenebenen-APIs von Azure Machine Learning](how-to-assign-roles.md) auf die Ressourcen zugreifen, wenn sie über ausreichende RBAC-Berechtigungen verfügen.

Die verwaltete Identität benötigt Berechtigungen für Mitwirkende in der Ressourcengruppe, die den Arbeitsbereich enthält, um die zugeordneten Ressourcen bereitzustellen und [Azure Container Instances für Webdienstendpunkte bereitzustellen](how-to-deploy-azure-container-instance.md).

Administratoren sollten den Zugriff der verwalteten Identität auf die in der vorhergehenden Tabelle genannten Ressourcen nicht widerrufen. Sie können den Zugriff mithilfe des [Vorgangs zum erneuten Synchronisieren von Schlüsseln](how-to-change-storage-access-key.md) wiederherstellen.

> [!NOTE]
> Wenn Ihre Azure Machine Learning-Arbeitsbereiche über Computeziele (Computecluster, Compute-Instanz, Azure Kubernetes Service usw.) verfügen, die vor dem __14. Mai 2021__ erstellt wurden, verfügen Sie möglicherweise auch über ein zusätzliches Azure Active Directory-Konto. Der Kontoname beginnt mit `Microsoft-AzureML-Support-App-` und verfügt für jede Arbeitsbereichsregion über Zugriff der Mitwirkenderebene auf Ihr Abonnement.
> 
> Wenn Ihrem Arbeitsbereich kein Azure Kubernetes Service (AKS) angefügt ist, können Sie dieses Azure AD-Konto unbesorgt löschen. 
> 
> Wenn Ihrem Arbeitsbereich AKS-Cluster angefügt sind _und diese vor dem 14. Mai 2021 erstellt wurden_, __löschen Sie dieses Azure AD-Konto nicht__. In diesem Szenario müssen Sie zuerst den AKS-Cluster löschen und neu erstellen, bevor Sie das Azure AD-Konto löschen können.

Sie können den Arbeitsbereich für die Verwendung einer benutzerseitig zugewiesenen verwalteten Identität bereitstellen und der verwalteten Identität zusätzliche Rollen zuweisen, z. B. zum Zugreifen auf Ihre eigene Azure Container Registry für Docker-Basisimages. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten](how-to-use-managed-identities.md).

Sie können auch verwaltete Identitäten für die Verwendung mit Azure Machine Learning-Computeclustern konfigurieren. Diese verwaltete Identität ist unabhängig von der verwalteten Identität des Arbeitsbereichs. Bei einem Computecluster wird die verwaltete Identität verwendet, um auf Ressourcen wie gesicherte Datenspeicher zuzugreifen, auf die der Benutzer, der den Trainingsauftrag durchführt, möglicherweise keinen Zugriff hat. Weitere Informationen finden Sie unter [Identitätsbasierter Datenzugriff auf Speicherdienste in Azure](how-to-identity-based-data-access.md).

> [!TIP]
> Es gibt einige Ausnahmen bei der Verwendung von Azure AD und Azure RBAC in Azure Machine Learning:
> * Sie können optional den __SSH__-Zugriff auf Computeressourcen wie die Azure Machine Learning-Computeinstanz und Computecluster aktivieren. Der SSH-Zugriff basiert auf öffentlichen/privaten Schlüsselpaaren, nicht auf Azure AD. Der SSH-Zugriff wird nicht durch Azure RBAC geregelt.
> * Mit Authentifizierung auf Basis von __Schlüssel__ oder __Token__ können Sie sich bei Modellen authentifizieren, die als Webdienste (Rückschlussendpunkte) bereitgestellt werden. Schlüssel sind statische Zeichenfolgen, während Token mithilfe eines Azure AD-Sicherheitsobjekts abgerufen werden. Weitere Informationen finden Sie unter [Konfigurieren der Authentifizierung für Modelle, die als Webdienst bereitgestellt werden](how-to-authenticate-web-service.md).

Weitere Informationen finden Sie in den folgenden Artikeln:
* [Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](how-to-setup-authentication.md)
* [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md)
* [Herstellen einer Verbindung mit Speicherdiensten in Azure](how-to-access-data.md)
* [Verwenden von Geheimnissen als Anmeldeinformationen für die Authentifizierung in Azure Machine Learning-Trainingsausführungen](how-to-use-secrets-in-runs.md)
* [Verwenden von verwalteten Identitäten mit Azure Machine Learning (Vorschau)](how-to-use-managed-identities.md)
* [Verwenden der Azure AD-Identität mit Ihrem Machine Learning-Webdienst in Azure Kubernetes Service](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Netzwerksicherheit und -isolation

Um den Netzwerkzugriff auf Azure Machine Learning-Ressourcen zu beschränken, können Sie [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md) verwenden. Mit VNETs können Sie Netzwerkumgebungen erstellen, die teilweise oder vollständig vom öffentlichen Internet isoliert sind. Dadurch werden die Angriffsfläche für die Lösung und die Möglichkeit einer Datenexfiltration reduziert.

Sie können ein VPN-Gateway (Virtual Private Network, virtuelles privates Netzwerk) verwenden, um einzelne Clients oder Ihr eigenes Netzwerk mit dem VNET zu verbinden.

Der Azure Machine Learning-Arbeitsbereich kann mit [Azure Private Link](../private-link/private-link-overview.md) einen privaten Endpunkt hinter dem VNET erstellen. Dadurch wird eine Gruppe privater IP-Adressen bereitgestellt, die für den Zugriff auf den Arbeitsbereich innerhalb des VNET verwendet werden können. Einige der Dienste, auf denen Azure Machine Learning basiert, können auch Azure Private Link verwenden, aber einige basieren auf Netzwerksicherheitsgruppen oder benutzerdefiniertem Routing.

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md)
* [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
* [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Verwenden von Azure Machine Learning Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md)
* [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
* [Konfigurieren der Firewall](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Datenverschlüsselung

Azure Machine Learning verwendet verschiedene Computeressourcen und Datenspeicher auf der Azure-Plattform. Weitere Informationen zu deren Unterstützung der Verschlüsselung von Daten im Ruhezustand und während der Übertragung finden Sie unter [Datenverschlüsselung mit Azure Machine Learning](concept-data-encryption.md).

Beim Bereitstellen von Modellen als Webdienste können Sie TLS (Transport Layer Security) aktivieren, um Daten während der Übertragung zu verschlüsseln. Weitere Informationen finden Sie unter [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Überprüfung auf Sicherheitsrisiken

[Microsoft Defender for Cloud](../security-center/security-center-introduction.md) bietet eine einheitliche Sicherheitsverwaltung und erweiterten Bedrohungsschutz für Hybrid Cloud-Workloads. Für Azure Machine Learning sollten Sie die Überprüfung der [Azure Container Registry](../container-registry/container-registry-intro.md)-Ressourcen sowie der Azure Kubernetes Service-Ressourcen aktivieren. Weitere Informationen finden Sie unter [Azure Container Registry-Imageüberprüfungen durch Defender für Cloud](../security-center/defender-for-container-registries-introduction.md) und [Azure Kubernetes Service-Integration mit Defender für Cloud](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Überwachen und Verwalten der Compliance

[Azure Policy](../governance/policy/index.yml) ist ein Governancetool, mit dem Sie sicherstellen können, dass Azure-Ressourcen mit Ihren Richtlinien konform sind. Sie können Richtlinien festlegen, um bestimmte Konfigurationen zuzulassen oder zu erzwingen, z. B. ob Ihr Azure Machine Learning Arbeitsbereich einen privaten Endpunkt verwendet. Weitere Informationen zu Azure Policy finden Sie in der [Dokumentation zu Azure Policy](../governance/policy/overview.md). Weitere Informationen zu den Richtlinien, die speziell für Azure Machine Learning gelten, finden Sie unter [Überwachen und Verwalten der Compliance mit Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Nächste Schritte

* [Bewährte Azure Machine Learning-Methoden für die Unternehmenssicherheit](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)
* [Sichere Azure Machine Learning-Webdienste mit TLS](how-to-secure-web-service.md)
* [Nutzen eines als Webdienst bereitgestellten Machine Learning-Modells](how-to-consume-web-service.md)
* [Verwenden von Azure Machine Learning hinter Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Verwenden von Azure Machine Learning mit einem virtuellen Azure-Netzwerk](how-to-network-security-overview.md)
* [Datenverschlüsselung von ruhenden Daten und während der Übertragung](concept-data-encryption.md)
* [Erstellen einer Echtzeitempfehlungs-API in Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
