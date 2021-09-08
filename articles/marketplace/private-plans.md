---
title: Private Pläne im kommerziellen Microsoft-Marketplace
description: Hier finden Sie Informationen zu privaten Plänen im kommerziellen Microsoft-Marketplace für Herausgeber von Apps und Diensten (Azure Marketplace).
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: navits09
ms.author: navits
ms.date: 08/13/2021
ms.openlocfilehash: 1048aa89e13f22e6708a3cb86eaa1cb4332bc2a1
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351483"
---
# <a name="private-plans-in-the-microsoft-commercial-marketplace"></a>Private Pläne im kommerziellen Microsoft-Marketplace

Herausgeber verwenden private Pläne, sodass sie nur für Zielkunden sichtbar sind. In diesem Artikel werden die Optionen und Vorteile von privaten Plänen erörtert.

## <a name="unlock-enterprise-deals-with-private-plans"></a>Neue Chancen für Unternehmen durch private Pläne

Private Pläne ermöglichen Herausgebern das Anbieten privater, angepasster Lösungen für Zielkunden mit Funktionen, die Unternehmen benötigen:

- *Ausgehandelte Preise*: Hiermit können Herausgeber Rabatte und Sonderpreise aus öffentlich verfügbaren Angeboten gewähren.
- *Private Bestimmungen* ermöglichen es Herausgebern, Geschäftsbedingungen genau an die Anforderungen eines bestimmten Kunden anzupassen.
- Mit *speziellen Konfigurationen* können Herausgeber ihr Angebot an Virtual Machine-Diensten, Azure-Anwendungen und SaaS-Apps (Software-as-a-Service) auf die Bedürfnisse einzelner Kunden ausrichten. Diese Option ermöglicht es Herausgebern auch, Vorschauzugriff auf neue Produktfunktionen bereitzustellen, bevor diese allen Kunden zugänglich gemacht werden.

Mit privaten Plänen können Herausgeber von der globalen Verfügbarkeit und Größe eines öffentlichen Marktplatzes profitieren und erhalten gleichzeitig die Flexibilität und Steuerungsmöglichkeiten, um kundenspezifische Geschäftsabschlüsse auszuhandeln und die entsprechenden Konfigurationen zu liefern. Unternehmen können jetzt auf genau die Art und Weise einkaufen und verkaufen, die sie selbst erwarten.

## <a name="create-private-plans"></a>Erstellen privater Pläne

Bei *neuen oder vorhandenen Angeboten mit Plänen* können Herausgeber ganz einfach neue private Variationen erstellen, indem sie neue Pläne (früher als SKUs bezeichnet) erstellen und diese als „privat“ kennzeichnen. Jedes Angebot kann bis zu 45 private Pläne enthalten.

<!--- [Private SKUs]() --->

Private Pläne sind für die folgenden Angebotstypen verfügbar:

- Virtueller Azure-Computer
- Azure-Anwendung (als Lösungsvorlage oder verwaltete Anwendungen implementiert)
- Verwalteter Dienst
- SaaS-Angebote

Private Pläne sind Komponenten eines Angebots und können nur von den jeweiligen Zielkunden angezeigt und erworben werden. Private Pläne können nur von den jeweiligen Zielkunden angezeigt und erworben werden. Private Pläne können Kunden sowohl in Azure Global als auch Azure Government zur Verfügung gestellt werden.

Für private Pläne können die Basisimages und/oder Angebotsmetadaten wiederverwendet werden, die bereits für einen öffentlichen Plan veröffentlicht wurden. Mit dieser Option können Herausgeber mehrere private Variationen eines öffentlichen Angebots erstellen, ohne mehrere Versionen der gleichen Basisimages und Angebotsmetadaten veröffentlichen zu müssen. Nur bei Angeboten für Azure Virtual Machines und Azure-Anwendungen gilt: Wenn ein privater Plan das gleiche Basisimage nutzt wie ein öffentlicher Plan, werden alle Änderungen am Basisimage des Angebots an alle öffentlichen und privaten Pläne weitergegeben, die dieses Basisimage nutzen.

Bei *neuen Angeboten, die nur private Pläne umfassen*, können Herausgeber diese als beliebiges anderes Angebot erstellen und dann die Pläne als „privat“ kennzeichnen. Angebote, die nur private Pläne umfassen, sind für Kunden, denen diese Angebote nicht zugeordnet sind, nicht über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) auffindbar oder zugänglich.

>[!NOTE]
>Ein Angebot, das nur private Pläne enthält, wird im öffentlichen Azure Marketplace oder in AppSource nicht angezeigt.

## <a name="target-customers-with-private-plans"></a>Private Pläne für bestimmte Zielkunden

Herausgeber können sowohl neue als auch vorhandene private Pläne mithilfe von Abonnementbezeichnern auf bestimmte Zielkunden ausrichten. Herausgeber mit Angeboten für Azure Virtual Machines, Azure-Anwendungen und verwaltete Dienste können die Verfügbarkeit eines privaten Plans auf eine individuelle Azure-Abonnement-ID beschränken oder eine CSV-Datei mit bis zu 10.000 Azure-Abonnement-IDs hochladen. Bei SaaS-Angeboten können Herausgeber eine Azure Active Directory Mandanten-ID zuordnen, um die Verfügbarkeit eines privaten Plans zu beschränken. Dabei können sie IDs manuell zuordnen oder eine CSV-Datei mit mehreren IDs hochladen.

Sobald ein Angebot zertifiziert und veröffentlicht wurde, können Kunden mithilfe des Features zum Synchronisieren privater Abonnements im Plan aktualisiert oder daraus entfernt werden. Auf diese Weise können Herausgeber schnell und einfach die Liste der Kunden aktualisieren, denen der private Plan angezeigt wird, ohne das Angebot erneut zertifizieren oder veröffentlichen zu müssen.

## <a name="deploying-a-private-plan"></a>Bereitstellen eines privaten Plans

Nachdem sich der Kunde beim Azure-Portal angemeldet hat, kann er Ihre privaten Pläne auswählen, indem er die folgenden Schritte ausführt.

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.
1. Wählen Sie unter **Azure-Dienste** die Option **Ressource erstellen** aus.
1. Wählen Sie auf der Seite **Neu** neben **Azure Marketplace** **Alle anzeigen** aus. Die Seite „Marketplace“ wird angezeigt.
1. Wählen Sie im linken Navigationsbereich auf **Private Pläne**.

> [!NOTE]
> Private Pläne sind nur im [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) zu finden. Sie werden nicht in [Microsoft AppSource](https://appsource.microsoft.com/) oder [Azure Marketplace](https://azuremarketplace.microsoft.com) angezeigt. Weitere Informationen zum Veröffentlichen in verschiedenen kommerziellen Marketplace-Onlinestores finden Sie unter [Einführung in Auflistungsoptionen](./determine-your-listing-type.md).

Private Pläne werden auch in Suchergebnissen angezeigt und können wie alle anderen Angebote über die Befehlszeile oder mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden.

[![[Private Angebote werden in den Suchergebnissen angezeigt.]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

>[!Note]
>Private Pläne werden bei Abonnements, die über einen Handelspartner des Cloud Solution Provider-Programms (CSP) eingerichtet wurden, nicht unterstützt.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
