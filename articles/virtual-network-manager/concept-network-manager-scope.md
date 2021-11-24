---
title: Verstehen und Arbeit mit Azure Virtual Network Manager-Bereichen
description: Erfahren Sie mehr über Azure Virtual Network Manager-Bereiche und die Auswirkungen auf die Verwaltung virtueller Netzwerke.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 9a5a78fc77a2b0f0891bfdd2922ad1cd771aba42
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322898"
---
# <a name="understand-and-work-with-azure-virtual-network-manager-preview-scopes"></a>Verstehen und Arbeit mit Azure Virtual Network Manager-Bereichen (Vorschau)

In diesem Artikel erfahren Sie, wie Azure Virtual Network Manager das Konzept des *Bereichs* verwendet, um Verwaltungsgruppen oder Abonnements die Verwendung bestimmter Features von Virtual Network Manager zu ermöglichen. Außerdem erfahren Sie mehr über die *Hierarchie* und wie sich dies auf Ihre Benutzer auswirken kann, wenn Sie Virtual Network Manager verwenden. 

> [!IMPORTANT]
> Der Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="network-manager"></a>Netzwerkmanager

Der *Netzwerk-Manager* ist das Objekt der obersten Ebene, das aus untergeordneten Ressourcen wie *Netzwerkgruppen,* *Konfigurationen* und *Regeln* besteht. 

* **Netzwerkgruppen**: eine Teilmenge des Gesamtbereichs, auf den bestimmte Konnektivitäts- oder Sicherheitsadministratorrichtlinien angewendet werden können.

* **Konfigurationen**: Azure Virtual Network Manager bietet zwei Arten von Konfigurationen: eine Konnektivitätskonfiguration und eine Sicherheitskonfiguration. Mit Konnektivitätskonfigurationen können Sie Netzwerktopologien erstellen, während Sie mit Sicherheitskonfigurationen eine Sammlung von Regeln erstellen können, die Sie für virtuelle Netzwerke anwenden können.

* Regeln: eine Regelsammlung ist ein Satz von Netzwerksicherheitsregeln, mit denen Netzwerkdatenverkehr auf globaler Ebene für Ihre virtuellen Netzwerke zugelassen oder verweigert werden kann. 

> [!NOTE]
> Azure Virtual Network Manager erfordert, dass alle untergeordneten Ressourcen entfernt werden, bevor sie gelöscht werden können.
>

## <a name="scope"></a>`Scope`

Ein *Bereich* in Azure Virtual Network Manager ist eine Reihe von Ressourcen, auf die Features angewendet werden können. Wenn Sie einen Bereich angeben, beschränken Sie den Zugriff, für den Netzwerk-Manager Ressourcen verwalten kann. Der Wert für den Bereich kann auf Verwaltungsgruppenebene oder auf Abonnementebene sein. Hier erfahren Sie, wie Sie [Azure-Verwaltungsgruppen](../governance/management-groups/overview.md) zum Verwalten mehrerer Ressourcen-Hierarchie erstellen. Wenn Sie eine Verwaltungsgruppe als Bereich auswählen, werden alle untergeordneten Ressourcen in den Bereich eingeschlossen. 

> [!NOTE]
> Sie können nicht mehrere Azure Virtual Network Manager mit einem überlappenden Bereich derselben Hierarchie und den gleichen ausgewählten Features erstellen.
> 

## <a name="features"></a>Features

Features sind Bereichszugriff, den Sie Azure Virtual Network Manager verwalten können. Azure Virtual Network Manager verfügt derzeit über zwei Featurebereiche: *Konnektivität* und *SecurityAdmin*. Sie können beide Featurebereiche auf derselben Virtual Network Manager-Instanz aktivieren. Weitere Informationen zu den einzelnen Features finden Sie unter [Konnektivität](concept-connectivity-configuration.md) und [SecurityAdmin](concept-security-admins.md).

> [!NOTE]
> Features werden nur während der Bereitstellung von Azure Virtual Network Manager aktiviert. Wenn Sie eine Virtual Network Manager-Instanz mit nur einem Featurebereich bereitstellen, müssen Sie eine neue Azure Virtual Network Manager-Instanz erneut bereitstellen, um beide Features zu aktivieren.
>

## <a name="hierarchy"></a>Hierarchy

Azure Virtual Network Manager ermöglicht die Verwaltung Ihrer Netzwerkressourcen in einer Hierarchie. Eine Hierarchie bedeutet, dass Mehrere Virtual Network Manager-Instanzen überlappende Bereiche verwalten können, und die Konfigurationen in jedem Virtual Network Manager können sich auch überlagern. Beispielsweise können Sie die [Verwaltungsgruppe](../governance/management-groups/overview.md) der obersten Ebene eines Virtual Network-Managers und eine untergeordnete Verwaltungsgruppe als Bereich für einen anderen Virtual Network Manager haben. Bei einem Konfigurationskonflikt zwischen verschiedenen Virtual Network Manager-Instanzen, die die gleiche Ressource enthalten. Die Konfiguration des Virtual Network-Managers mit dem höheren Bereich wird angewendet.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz erstellen.
- Erfahren Sie mehr über [Netzwerkgruppen](concept-network-groups.md).
