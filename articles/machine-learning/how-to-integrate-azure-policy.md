---
title: Azure Machine Learning prüfen und verwalten
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe von Azure Policy integrierte Richtlinien für Azure Machine Learning erstellen können, um sicherzustellen, dass Ihre Arbeitsbereiche Ihren Anforderungen entsprechen.
author: aashishb
ms.author: aashishb
ms.date: 10/21/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: ea374a6503b2d015ca92d3aee8179b3511c9755f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289678"
---
# <a name="audit-and-manage-azure-machine-learning"></a>Azure Machine Learning prüfen und verwalten

Wenn Teams an Azure Machine Learning zusammenarbeiten, können sie unterschiedlichen Anforderungen an die Konfiguration und Organisation von Ressourcen ausgesetzt sein. Teams für Machine Learning suchen möglicherweise nach Flexibilität bei der Organisation von Arbeitsbereichen für die Zusammenarbeit oder bei der Größe von Compute-Clustern, die den Anforderungen ihrer Anwendungsfälle entsprechen. In diesen Szenarien kann es zur höchsten Produktivität führen, wenn das Anwendungsteam seine eigene Infrastruktur verwalten kann.

Als Plattformadministrator können Sie mithilfe von Richtlinien Leitplanken für die Verwaltung der eigenen Ressourcen durch die Teams festlegen. [Azure Policy](../governance/policy/index.yml) hilft bei der Prüfung und Verwaltung des Ressourcenstatus. In diesem Artikel erfahren Sie mehr über die verfügbaren Audit-Kontrollen und Governanceverfahren für Azure Machine Learning.

## <a name="policies-for-azure-machine-learning"></a>Richtlinien für Azure Machine Learning

[Azure Policy](../governance/policy/index.yml) ist ein Governancetool, mit dem Sie sicherstellen können, dass Azure-Ressourcen mit Ihren Richtlinien konform sind.

Azure Machine Learning stellt eine Reihe von Richtlinien bereit, die Sie für gängige Szenarien mit Azure Machine Learning verwenden können. Sie können diese Richtliniendefinitionen Ihrem vorhandenen Abonnement zuweisen oder sie als Grundlage verwenden, um eigene benutzerdefinierte Definitionen zu erstellen.

Die folgende Tabelle enthält eine Auswahl von Richtlinien, die Sie mit Azure Machine Learning zuweisen können. Eine vollständige Liste der integrierten Richtlinien für Azure Machine Learning finden Sie unter [Integrierte Richtlinien für Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

| Richtlinie | BESCHREIBUNG |
| ----- | ----- |
| **Kundenverwalteter Schlüssel** | Überwachen oder erzwingen Sie, ob Arbeitsbereiche einen kundenseitig verwalteten Schlüssel verwenden müssen. |
| **Privater Link** | Überprüfen oder erzwingen Sie, ob Arbeitsbereiche einen privaten Endpunkt zur Kommunikation mit einem virtuellen Netzwerk verwenden. |
| **Privater Endpunkt** | Konfigurieren Sie das Azure Virtual Network-Subnetz, in dem der private Endpunkt erstellt werden soll. |
| **Private DNS-Zone** | Konfigurieren Sie die private DNS-Zone, die für die private Verbindung verwendet werden soll. |
| **Benutzerseitig zugewiesene verwaltete Identität** | Überwachen oder erzwingen Sie, ob Arbeitsbereiche eine vom Benutzer zugewiesene verwaltete Identität verwenden. |
| **Deaktivieren der lokalen Authentifizierung** | Überwachen oder erzwingen Sie, ob lokale Authentifizierungsmethoden für Azure Machine Learning-Computeressourcen deaktiviert sein sollen. |
| **Ändern oder Deaktivieren der lokalen Authentifizierung** | Konfigurieren Sie Computeressourcen für die Deaktivierung lokaler Authentifizierungsmethoden. |

Richtlinien können in unterschiedlichen Bereichen festgelegt werden, z. B. auf Abonnement- oder Ressourcengruppenebene. Weitere Informationen finden Sie in der [Dokumentation zu Azure Policy](../governance/policy/overview.md).

## <a name="assigning-built-in-policies"></a>Zuweisung von integrierten Richtlinien

Um die integrierten Richtliniendefinitionen im Zusammenhang mit Azure Machine Learning anzuzeigen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu __Azure Policy__.
1. Wählen Sie __Definitionen__ aus.
1. Wählen Sie als __Typ__ die Option _Integriert_ und als __Kategorie__ die Option __Machine Learning__ aus.

Von hier aus können Sie Richtliniendefinitionen auswählen, um sie anzuzeigen. Beim Anzeigen einer Definition können Sie den Link __Zuweisen__ verwenden, um die Richtlinie einem bestimmten Bereich zuzuweisen und die Parameter für die Richtlinie zu konfigurieren. Weitere Informationen finden Sie unter [Zuweisen einer Richtlinie: Portal](../governance/policy/assign-policy-portal.md).

Sie können Richtlinien auch mit [Azure PowerShell](../governance/policy/assign-policy-powershell.md), der [Azure CLI](../governance/policy/assign-policy-azurecli.md) und [Vorlagen](../governance/policy/assign-policy-template.md) zuweisen.

## <a name="conditional-access-policies"></a>Bedingte Zugriffsrichtlinien

Mit dem [bedingten Zugriff](../active-directory/conditional-access/overview.md) von Azure Active Directory können Sie steuern, welche Benutzer auf Ihren Azure Machine Learning-Arbeitsbereich zugreifen dürfen.

## <a name="enable-self-service-using-landing-zones"></a>Ermöglichung von Selbstbedienung durch Zielzonen

Zielzonen sind ein architektonisches Muster zur Einrichtung von Azure-Umgebungen, das Skalierung, Governance, Sicherheit und Produktivität berücksichtigt. Eine Datenzielzone ist eine vom Administrator konfigurierte Umgebung, die von einem Anwendungsteam zum Hosten von Daten- und Analyselasten verwendet wird.

Der Zweck der Zielzone ist es, sicherzustellen, dass beim Start eines Teams in der Azure-Umgebung alle Arbeiten zur Konfiguration der Infrastruktur abgeschlossen sind. So werden beispielsweise die Sicherheitskontrollen in Übereinstimmung mit den organisatorischen Standards eingerichtet und die Netzwerkkonnektivität hergestellt.

Mithilfe des Musters dieser Zielzonen können Teams für Machine Learning ihre eigenen Ressourcen per Selbstbedienung bereitstellen und verwalten. Mithilfe von Azure-Richtlinien können Sie als Administrator Azure-Ressourcen auf Konformität prüfen und verwalten und sicherstellen, dass die Arbeitsbereiche Ihren Anforderungen entsprechen. 

Azure Machine Learning lässt sich mit [Datenzielzonen](https://github.com/Azure/data-landing-zone) im [Cloud Adoption Framework Datenmanagement- und Analyseszenario](/azure/cloud-adoption-framework/scenarios/data-management/) integrieren. Diese Referenzimplementierung bietet eine optimierte Umgebung für die Migration von Machine Learning-Workloads und enthält vorkonfigurierte Richtlinien für Azure Machine Learning.

## <a name="configure-built-in-policies"></a>Konfigurieren integrierter Richtlinien

### <a name="workspace-encryption-with-customer-managed-key"></a>Arbeitsbereichverschlüsselung mit kundenseitig verwaltetem Schlüssel

Hiermit wird gesteuert, ob Arbeitsbereiche mit einem kundenseitig verwalteten Schlüssel oder Metriken und Metadaten mithilfe eines von Microsoft verwalteten Schlüssels verschlüsselt werden sollen. Weitere Informationen zur Verwenden on kundenseitig verwalteten Schlüsseln finden Sie im Abschnitt [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) des Artikels zur Datenverschlüsselung.

Um diese Richtlinie zu konfigurieren, legen Sie den Effektparameter auf __audit__ (Überwachen) oder __Deny__ (Verweigern) fest. Bei Festlegung auf __audit__ können Sie einen Arbeitsbereich ohne einen kundenseitig verwalteten Schlüssel erstellen, und im Aktivitätsprotokoll wird ein Warnungsereignis erstellt.

Wenn die Richtlinie auf __deny__ festgelegt ist, können Sie keinen Arbeitsbereich erstellen, es sei denn, es wird ein kundenseitig verwalteter Schlüssel angegeben. Der Versuch, einen Arbeitsbereich ohne einen kundenseitig verwalteten Schlüssel zu erstellen, führt zu einem Fehler, der `Resource 'clustername' was disallowed by policy` ähnelt und einen Fehler im Aktivitätsprotokoll generiert. Der Richtlinienbezeichner wird ebenfalls als Teil dieses Fehlers zurückgegeben.

### <a name="workspace-should-use-private-link"></a>Arbeitsbereiche sollen Private Link verwenden

Hiermit wird gesteuert, ob ein Arbeitsbereich über Azure Private Link mit Azure Virtual Network kommunizieren soll. Weitere Informationen zum Verwenden von Private Link finden Sie unter [Konfigurieren von Private Link für einen Arbeitsbereich](how-to-configure-private-link.md).

Um diese Richtlinie zu konfigurieren, legen Sie den Effektparameter auf __audit__ (Überwachen) oder __Deny__ (Verweigern) fest. Bei Festlegung auf __audit__ können Sie einen Arbeitsbereich ohne Private Link erstellen, und im Aktivitätsprotokoll wird ein Warnungsereignis erstellt.

Bei Festlegung der Richtlinie auf __deny__ können Sie nur dann einen Arbeitsbereich erstellen, wenn Private Link verwendet wird. Der Versuch, einen Arbeitsbereich ohne Private Link zu erstellen, führt zu einem Fehler. Der Fehler wird auch im Aktivitätsprotokoll protokolliert. Der Richtlinienbezeichner wird als Teil dieses Fehlers zurückgegeben.

### <a name="workspace-should-use-private-endpoint"></a>Arbeitsbereiche sollen private Endpunkte verwenden

Hiermit wird ein Arbeitsbereich so konfiguriert, dass innerhalb des angegebenen Azure Virtual Network-Subnetzes ein privater Endpunkt erstellt wird.

Legen Sie zur Konfiguration dieser Richtlinie den Parameter „effect“ auf __DeployIfNotExists__ fest. Stellen Sie die __privateEndpointSubnetID__ auf die Azure Ressourcen-Manager-ID des Subnetzes ein.

### <a name="workspace-should-use-private-dns-zones"></a>Arbeitsbereiche sollen private DNS-Zonen verwenden

Hiermit wird ein Arbeitsbereich so konfiguriert, dass eine private DNS-Zone verwendet und dabei die DNS-Standardauflösung für einen privaten Endpunkt überschrieben wird.

Legen Sie zur Konfiguration dieser Richtlinie den Parameter „effect“ auf __DeployIfNotExists__ fest. Geben Sie für __privateDnsZoneId__ die Azure Resource Manager-ID der privaten DNS-Zone an, die verwendet werden soll. 

### <a name="workspace-should-use-user-assigned-managed-identity"></a>Der Arbeitsbereich sollte eine vom Benutzer zugewiesene verwaltete Identität verwenden

Steuert, ob ein Arbeitsbereich mit einer vom System zugewiesenen verwalteten Identität (Standard) oder einer vom Benutzer zugewiesenen verwalteten Identität erstellt wird. Die verwaltete Identität für den Arbeitsbereich wird für den Zugriff auf zugehörige Ressourcen wie Azure Storage, Azure Container Registry, Azure Key Vault und Azure Anwendung Insights verwendet. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten mit Azure Machine Learning](how-to-use-managed-identities.md).

Um diese Richtlinie zu konfigurieren, setzen Sie den Effekt-Parameter auf __prüfen__, __ablehnen__, oder __deaktiviert__ ein. Wenn diese Option auf __Audit__ gesetzt ist, können Sie einen Arbeitsbereich erstellen, ohne eine dem Benutzer zugewiesene verwaltete Identität anzugeben. Es wird eine vom System zugewiesene Identität verwendet und ein Warnereignis im Aktivitätsprotokoll erstellt.

Wenn die Richtlinie auf __Verweigern__ eingestellt ist, können Sie keinen Arbeitsbereich erstellen, es sei denn, Sie geben während des Erstellungsprozesses eine dem Benutzer zugewiesene Identität an. Der Versuch, einen Arbeitsbereich zu erstellen, ohne eine dem Benutzer zugewiesene Identität anzugeben, führt zu einem Fehler. Der Fehler wird auch in das Aktivitätsprotokoll aufgenommen. Der Richtlinienbezeichner wird als Teil dieses Fehlers zurückgegeben.

### <a name="disable-local-authentication"></a>Deaktivieren der lokalen Authentifizierung

Hiermit legen Sie fest, ob die lokale Authentifizierung (SSH) für einen Computecluster oder eine Instanz von Azure Machine Learning deaktiviert werden soll.

Um diese Richtlinie zu konfigurieren, setzen Sie den Effekt-Parameter auf __prüfen__, __ablehnen__, oder __deaktiviert__ ein. Wenn __audit__ festgelegt ist, können Sie einen Computecluster mit aktiviertem SSH-Protokoll erstellen, und ein Warnungsereignis wird im Aktivitätsprotokoll erstellt.

Wenn die Richtlinie auf __deny__ festgelegt ist, können Sie einen Computecluster nur erstellen, wenn SSH deaktiviert ist. Der Versuch, einen Computecluster mit aktiviertem SSH-Protokoll zu erstellen, führt zu einem Fehler. Der Fehler wird auch im Aktivitätsprotokoll protokolliert. Der Richtlinienbezeichner wird als Teil dieses Fehlers zurückgegeben.

### <a name="modifydisable-local-authentication"></a>Ändern oder Deaktivieren der lokalen Authentifizierung

Hiermit deaktivieren Sie die lokale Authentifizierung (SSH) in einer Erstellungsanforderung für einen Computecluster oder eine Instanz von Azure Machine Learning.

Legen Sie den Parameter „effect“ auf __Modify__ oder __Disabled__ fest, um diese Richtlinie zu konfigurieren. Wenn __Modify__ festgelegt ist, wird die lokale Authentifizierung für erstellte Computecluster oder Instanzen automatisch deaktiviert, die im Anwendungsbereich der Richtlinie liegen.

## <a name="next-steps"></a>Nächste Schritte

* [Dokumentation zu Azure Policy](../governance/policy/overview.md)
* [Integrierte Richtlinien für Azure Machine Learning](policy-reference.md)
* [Arbeiten mit Sicherheitsrichtlinien bei Microsoft Defender für Cloud](../security-center/tutorial-security-policy.md)
* Das [Cloud Adoption Framework-Szenario für Datenmanagement und -analyse](/azure/cloud-adoption-framework/scenarios/data-management/) beschreibt Überlegungen zum Betrieb von Daten- und Analyse-Workloads in der Cloud.
* [Cloud Adoption Framework-Datenzielzonen](https://github.com/Azure/data-landing-zone) bieten eine Referenzimplementierung für die Verwaltung von Daten- und Analyse-Workloads in Azure.
* [Erfahren Sie, wie Sie Richtlinien verwenden, um Azure Private Link mit Azure Private DNS-Zonen zu integrieren](/azure/cloud-adoption-framework/ready/azure-best-practices/private-link-and-dns-integration-at-scale), um die Private Link-Konfiguration für den Arbeitsbereich und abhängige Ressourcen zu verwalten.
