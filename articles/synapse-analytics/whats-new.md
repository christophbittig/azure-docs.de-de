---
title: Neuigkeiten
description: Hier erfahren Sie mehr über die neuen Features und Dokumentationsverbesserungen für Azure Synapse Analytics.
services: synapse-analytics
author: ryanmajidi
ms.author: rymajidi
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: e86d15e5b15133891ad5fd3e3f61ea6cc6f790e5
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503506"
---
# <a name="whats-new-in-azure-synapse-analytics"></a>Neuerungen in Azure Synapse Analytics

In diesem Artikel werden Updates für Azure Synapse Analytics aufgelistet, die ab Oktober 2021 verfügbar sind.

Jedes Update ist mit dem Blog zu Azure Synapse Analytics und einem Artikel verknüpft, der weitere Informationen enthält.

## <a name="october-2021-update"></a>Update vom Oktober 2021

Die folgenden Updates sind für Azure Synapse Analytics diesen Monat neu.

### <a name="general"></a>Allgemein

- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#manage-cost) und [Artikel](../cost-management-billing/reservations/synapse-analytics-pre-purchase-plan.md) zum Verwalten Ihrer Kosten für Azure Synapse mit einem Plan mit Vorauszahlung
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#move-workspace-region) und [Artikel](how-to-move-workspace-from-one-region-to-another.md) zum Verschieben Ihres Azure Synapse-Arbeitsbereichs zwischen Azure-Regionen

### <a name="apache-spark-for-synapse"></a>Apache Spark für Synapse

- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#spark-performance) zu Spark-Leistungsoptimierungen 

### <a name="security"></a>Sicherheit

- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-rbac) und [Artikel](./security/synapse-workspace-synapse-rbac-roles.md): Alle Synapse RBAC-Rollen sind jetzt allgemein für die Verwendung in der Produktion verfügbar
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#user-assigned-managed-identities) und [Artikel](./security/workspaces-encryption.md) zum Nutzen von benutzerseitig zugewiesenen verwalteten Identitäten für die Mehrfachverschlüsselung
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#elevated-access) und [Artikel](./security/synapse-workspace-access-control-overview.md): Synapse-Administrator*innen haben jetzt erhöhte Zugriffsrechte auf dedizierte SQL-Pools
    
###  <a name="governance"></a>Governance

- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-purview-lineage) und [Artikel](../purview/how-to-lineage-azure-synapse-analytics.md): Synapse-Arbeitsbereiche können jetzt Herkunftsdaten automatisch in Azure Purview übertragen
 
### <a name="integrate"></a>Integrieren

- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#stringify-transform)und [Artikel](../data-factory/data-flow-stringify.md) zum Verwenden von „Stringifizieren“ in Datenflüssen zum einfachen Transformieren komplexer Datentypen in Zeichenfolgen
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#data-flowspark-ttl) und [Artikel](../data-factory/concepts-integration-runtime-performance.md) zum Steuern der Gültigkeitsdauerdauer (TTL) einer Spark-Sitzung in Datenflüssen

### <a name="cicd--git"></a>CI/CD & Git

- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#deploy-synapse-github-action) und [Artikel](./cicd/continuous-integration-delivery.md#configure-github-actions-secrets) zum Bereitstellen von Synapse-Arbeitsbereichen mithilfe von GitHub Actions
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#create-git-branch-in-studio) und [Artikel](./cicd/source-control.md#creating-feature-branches): Weitere Steuerungsoptionen beim Erstellen von Git-Verzweigungen in Synapse Studio

### <a name="developer-experience"></a>Entwickleroberfläche

- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-markdown-toolbar) und [Artikel](./spark/apache-spark-development-using-notebooks.md) zur verbesserten Markdown-Bearbeitung in Synapse-Notebooks (Vorschau)
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#pandas-dataframe-html) und [Artikel](./spark/apache-spark-data-visualization.md): Pandas-Dataframes werden automatisch als übersichtlich formatierte HTML-Tabellen gerendert
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-ipythong-widgets) und [Artikel](./spark/apache-spark-development-using-notebooks.md) zum Verwenden von IPython-Widgets in Synapse-Notebooks
- [Blogeintrag](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#mssparkutils-context) und [Artikel](./spark/microsoft-spark-utilities.md?pivots=programming-language-python): Der MSSparkUtils-Runtimekontext ist jetzt für Python und Scala verfügbar

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit Azure Synapse Analytics](get-started.md)


