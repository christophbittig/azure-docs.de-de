---
title: Purview-Kontoarchitektur und bewährte Methoden
description: In diesem Artikel finden sich Beispiele für die Architekturen von Azure Purview-Kontoarchitekturen und Beschreibungen bewährter Methoden.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: a31e16ffd74808fb6b6232f0cbda3d447cfce513
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007312"
---
# <a name="azure-purview-accounts-architectures-and-best-practices"></a>Azure Purview-Kontoarchitektur und bewährte Methoden  

Azure Purview ist eine einheitliche Lösung für die Datengovernance. Durch Bereitstellen eines Azure Purview-Kontos können Sie die Datengovernance in Ihrem gesamten Datenbestand in Cloud- und lokalen Umgebungen zentral verwalten. Um Azure Purview als zentralisierte Datengovernance-Lösung zu verwenden, müssen Sie mindestens ein Purview-Konto in Ihrem Azure-Abonnement bereitstellen. Es wird empfohlen, die Anzahl der Purview-Instanzen auf ein Minimum zu beschränken. In einigen Fällen sind jedoch mehr Purview-Instanzen erforderlich, um die Sicherheits- und Complianceanforderungen des Unternehmens zu erfüllen.

## <a name="intended-audience"></a>Zielpublikum

- Datenarchitektur-Team
- Daten-Governance- und Datenverwaltungs-Teams
- Datensicherheitsteam

## <a name="single-purview-account"></a>Einzelnes Azure Purview-Konto

Sie sollten für Ihre Organisation so wenige Azure Purview-Konten bereitstellen wie möglich. So werden die „Netzwerkeffekte“ maximal genutzt, das heißt der Wert der Plattform nimmt exponentiell zu, je mehr Daten sie beinhaltet. 

Verwenden Sie die [Azure Purview-Sammlungshierarchie](./concept-best-practices-collections.md), um die Datenverwaltungsstruktur Ihrer Organisation in einem einzelnen Purview-Konto zu definieren. In diesem Szenario wird ein Purview-Konto in einem Azure-Abonnement bereitgestellt. Datenquellen aus mindestens einem Azure-Abonnement können in Azure Purview registriert und gescannt werden. Sie können auch Datenquellen aus Ihren lokalen oder Multi-Cloud-Umgebungen registrieren und scannen.

:::image type="content" source="media/concept-best-practices/accounts-single-account.png" alt-text="Screenshot: Einzelnes Azure Purview-Konto"lightbox="media/concept-best-practices/accounts-single-account.png":::

## <a name="multiple-purview-accounts"></a>Mehrere Purview-Konten

Für einige Organisationen müssen möglicherweise mehrere Azure Purview-Konten eingerichtet werden. Sehen Sie sich die folgenden Szenarien als Beispiele an, wenn Sie die Architektur Ihrer Azure Purview-Konten definieren:  

### <a name="testing-new-features"></a>Testen neuer Features 

Es wird empfohlen, zum Testen von Scankonfigurationen oder Klassifizierungen in isolierten Umgebungen eine neue Instanz des Purview-Kontos zu erstellen. Für manche Szenarien gibt es in einigen Bereichen der Plattform ein Feature zur „Versionsverwaltung“, z. B. im Glossar. Es ist jedoch einfacher, eine „Wegwerfinstanz“ von Purview zu verwenden, um die erwartete Funktionalität frei zu testen und das Feature dann in der Produktionsinstanz einzuführen.  

Erwägen Sie außerdem die Verwendung eines Purview-Testkontos, wenn Sie keinen Rollback ausführen können. Beispielsweise können Sie derzeit Attribute von Glossarbegriffen nicht aus einer Purview-Instanz entfernen, nachdem sie Ihrem Purview-Konto hinzugefügt wurden. In diesem Fall wird empfohlen, zuerst ein Purview-Testkonto zu verwenden.
 
### <a name="isolating-productionand-non-production-environments"></a>Abgrenzen von Produktionsumgebungen und Nicht-Produktionsumgebungen 

Erwägen Sie die Bereitstellung separater Purview-Kontoinstanzen für Entwicklungs-, Test- und Produktionsumgebungen, insbesondere dann, wenn separate Datenbestände für jede Umgebung bestehen.  

In einem solchen Szenario können Produktions- und Nicht-Produktionsdatenquellen innerhalb ihrer jeweiligen Purview-Instanzen registriert und gescannt werden.

Sie können Datenquellen bei Bedarf in mehr als einer Purview-Instanz registrieren.

:::image type="content" source="media/concept-best-practices/accounts-multiple-accounts.png" alt-text="Screenshot: mehrere Azure Purview-Konten für verschiedene Umgebungen"lightbox="media/concept-best-practices/accounts-multiple-accounts.png":::

### <a name="fulfilling-compliance-requirements"></a>Erfüllen von Complianceanforderungen  

Wenn Sie Datenquellen in Azure Purview scannen, werden Informationen zu Ihren Metadaten in Ihrer Azure Purview Data Map in der Azure-Region, in der Ihr Purview-Konto bereitgestellt wird, erfasst und gespeichert. Erwägen Sie die Bereitstellung separater Instanzen von Azure Purview, wenn Sie bestimmte Vorschriften und Complianceanforderungen erfüllen müssen, die selbst das Speichern von Metadaten an einem bestimmten geografischen Standort betreffen.  

Wenn Ihre Organisation über Daten in mehreren Regionen verfügt und Ihre Metadaten in derselben Region wie die tatsächlichen Daten bleiben müssen, müssen Sie mehrere Purview-Instanzen bereitstellen, eine für jede Region. In diesem Fall sollten Datenquellen aus den einzelnen Regionen im Purview-Konto für die entsprechende Region registriert und gescannt werden.

:::image type="content" source="media/concept-best-practices/accounts-multiple-regions.png" alt-text="Screenshot: mehrere Azure Purview-Konten zum Erfüllen von Complianceanforderungen"lightbox="media/concept-best-practices/accounts-multiple-regions.png":::

### <a name="having-data-sources-distributed-across-multiple-tenants"></a>Auf mehrere Mandanten verteilte Datenquellen  

Purview kann derzeit nicht mit mehreren Mandanten verwendet werden. Wenn Azure-Datenquellen auf mehrere Azure-Abonnements in verschiedenen Azure Active Directory-Mandanten verteilt sind, empfiehlt es sich, für jeden Mandanten separate Azure Purview-Konten bereitzustellen. 

Eine Ausnahme gilt für VM-basierte Datenquellen und Power BI-Mandanten. Weitere Informationen zum Scannen und Registrieren von mandantenübergreifendem Power BI in einem einzelnen Purview-Konto finden Sie unter [Registrieren und Überprüfen in einem mandantenübergreifenden Power BI-Szenario](/register-scan-power-bi-tenant#register-and-scan-a-cross-tenant-power-bi). 

:::image type="content" source="media/concept-best-practices/accounts-multiple-tenants.png" alt-text="Screenshot: mehrere Azure Purview-Konten zur Verwendung mit mehreren Mandanten"lightbox="media/concept-best-practices/accounts-multiple-tenants.png"::: 

### <a name="billing-model"></a>Abrechnungsmodell 

Beachten Sie beim Definieren des Budgetmodells und der Azure Purview-Architektur für Ihre Organisation das [Azure Purview-Preismodell](https://azure.microsoft.com/pricing/details/azure-purview). Eine Abrechnung wird für ein einzelnes Purview-Konto in dem Abonnement generiert, in dem es bereitgestellt wird. Dieses Modell gilt auch für andere Purview-Kosten wie das Scannen und Klassifizieren von Metadaten in Purview Data Map.

Manche Organisationen verfügen über viele Geschäftseinheiten (Business Units, BUs), die separat arbeiten, und in einigen Fällen teilen sie sich nicht einmal die Abrechnung untereinander. In diesen Fällen erstellt die Organisation für jede BU eine Purview-Instanz. Dieses Modell ist nicht ideal, kann jedoch notwendig sein, insbesondere, da BUs häufig nicht bereit sind, die Azure-Abrechnung untereinander zu teilen. 

Weitere Informationen zum Cloud Computing-Kostenmodell in Rückbuchungs- und Showbackmodellen finden Sie unter [Was ist Cloudbuchhaltung?](/cloud-adoption-framework/strategy/cloud-accounting).  

## <a name="additional-considerations-and-recommendations"></a>Zusätzliche Überlegungen und Empfehlungen 

- Halten Sie die Anzahl der Purview-Konten gering, um die Verwaltung zu vereinfachen. Wenn Sie mehrere Purview-Konten erstellen, müssen Sie möglicherweise zusätzliche Scans, Zugriffssteuerungsmodelle, Anmeldeinformationen und Runtimes für Ihre Purview-Konten erstellen und verwalten. Darüber hinaus müssen Sie möglicherweise Klassifizierungen und Glossarbegriffe für jedes Purview-Konto verwalten.

- Überprüfen Sie Ihr Budget und finanzielle Erfordernisse. Verwenden Sie nach Möglichkeit das Rückbuchungs- oder Showbackmodell, wenn Sie Azure-Dienste verwenden, und teilen Sie die Kosten von Azure Purview auf die Organisation auf, um die Anzahl von Purview-Konten möglichst gering zu halten. 

- Verwenden [Sie Azure Purview-Sammlungen](concept-best-practices-collections.md), um die Zugriffssteuerung für Metadaten in Azure Purview Data Map für die Geschäftsbenutzer sowie Datenverwaltungs- und Governanceteams Ihrer Organisation zu definieren. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Purview](./catalog-permissions.md).

- Beachten Sie [Beschränkungen von Azure Purview](./how-to-manage-quotas.md#azure-purview-limits) bevor Sie neue Purview-Konten bereitstellen. Derzeit können maximal drei Purview-Konten pro Region und Mandant erstellt werden (alle Abonnements kombiniert). Möglicherweise müssen Sie sich an den Microsoft-Support wenden, um diesen Grenzwert in Ihrem Abonnement oder Mandanten zu erhöhen, bevor Sie zusätzliche Instanzen von Azure Purview bereitstellen.  

- Überprüfen Sie die [Voraussetzungen für Azure Purview,](./create-catalog-portal.md#prerequisites) bevor Sie neue Purview-Konten in Ihrer Umgebung bereitstellen.
  
## <a name="next-steps"></a>Nächste Schritte
-  [Erstellen eines Purview-Kontos](./create-catalog-portal.md)