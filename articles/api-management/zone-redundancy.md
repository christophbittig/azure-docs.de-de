---
title: Unterstützung von Verfügbarkeitszonen für Azure API Management
description: Hier erfahren Sie, wie Sie durch die Aktivierung von Zonenredundanz die Resilienz Ihrer Azure API Management-Dienstinstanz in einer Region verbessern.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/11/2021
ms.author: danlep
ms.custom: references_regions
ms.openlocfilehash: d43d6e7b04e117e4144356ac22ea2858408405df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582840"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Unterstützung von Verfügbarkeitszonen für Azure API Management 

In diesem Artikel wird gezeigt, wie Sie Zonenredundanz für Ihre API Management-Instanz mithilfe des Azure-Portals aktivieren. [Zonenredundanz](../availability-zones/az-overview.md#availability-zones) bietet Resilienz und Hochverfügbarkeit für eine Dienstinstanz in einer bestimmten Azure-Region (Standort). Mit Zonenredundanz werden das Gateway und die Steuerungsebene Ihrer API Management-Instanz (Verwaltungs-API, Entwicklerportal, Git-Konfiguration) in Rechenzentren in physisch getrennten Zonen repliziert, wodurch sie resilient gegen einen Zonenausfall sind. 

API Management unterstützt außerdem [Bereitstellungen in mehreren Regionen](api-management-howto-deploy-multi-region.md). Dadurch wird die von geografisch verteilten API-Consumern wahrgenommene Anforderungslatenz reduziert und die Verfügbarkeit der Gatewaykomponente verbessert, wenn eine Region offline geschaltet wird. Die Kombination aus Verfügbarkeitszonen für Redundanz innerhalb einer Region und Bereitstellungen in mehreren Regionen zur Verbesserung der Gatewayverfügbarkeit bei einem regionalen Ausfall trägt zu einer höheren Zuverlässigkeit und Leistung Ihrer API Management-Instanz bei.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Unterstützte Regionen

Das Konfigurieren von API Management für Zonenredundanz wird derzeit in den folgenden Azure-Regionen unterstützt:

* Australien (Osten)
* Brasilien Süd
* Kanada, Mitte
* Indien, Mitte (*)
* USA, Mitte
* East US
* USA (Ost) 2
* Frankreich, Mitte
* Deutschland, Westen-Mitte
* Japan, Osten
* Südkorea, Mitte (*)
* Europa, Norden
* Norwegen, Osten (*)
* Südafrika, Norden (*)
* USA Süd Mitte
* Asien, Südosten
* UK, Süden
* Europa, Westen
* USA, Westen 2
* USA, Westen 3

> [!IMPORTANT]
> Die Regionen mit * verfügen über restriktiven Zugriff in einem Azure-Abonnement, um die Unterstützung von Verfügbarkeitszonen zu aktivieren. Arbeiten Sie mit Ihrem Microsoft-Vertriebs- oder -Kundendienstmitarbeiter zusammen.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen unter [Erstellen einer API Management-Dienstinstanz](get-started-create-service-instance.md). Wählen Sie die Dienstebene „Premium“ aus.
* Wenn Ihre API Management-Instanz in einem [virtuellen Netzwerk](api-management-using-with-vnet.md) bereitgestellt wird, müssen Sie an jedem neuen Standort, an dem Sie Zonenredundanz aktivieren möchten, ein virtuelles Netzwerk, ein Subnetz und eine öffentliche IP-Adresse einrichten.

## <a name="enable-zone-redundancy---portal"></a>Aktivieren der Zonenredundanz: Portal

Aktivieren Sie im Portal optional Zonenredundanz, wenn Sie ihrem API Management-Dienst einen Standort hinzufügen oder die Konfiguration eines vorhandenen Standorts aktualisieren.

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **Standorte** aus.
1. Wählen Sie einen vorhandenen Standort oder auf der oberen Leiste die Option **+ Hinzufügen** aus. Der Standort muss [Verfügbarkeitszonen unterstützen](#supported-regions).
1. Wählen Sie die Anzahl der **[Skalierungseinheiten](upgrade-and-scale.md)** am Standort aus.
1. Wählen Sie unter **Verfügbarkeitszonen** mindestens eine Zone aus. Die Anzahl von ausgewählten Einheiten muss gleichmäßig auf die Verfügbarkeitszonen verteilt werden. Wenn Sie beispielsweise drei Einheiten ausgewählt haben, wählen Sie drei Zonen aus, sodass jede Zone eine Einheit hostet.
1. Wenn die API Management-Instanz in einem [virtuellen Netzwerk](api-management-using-with-vnet.md) bereitgestellt wird, konfigurieren Sie die Einstellungen des virtuellen Netzwerks am Standort, indem Sie ein vorhandenes virtuelles Netzwerk, ein Subnetz und eine öffentliche IP-Adresse auswählen, die am Standort verfügbar sind. Für einen vorhandenen Standort müssen das virtuelle Netzwerk und das Subnetz über das Blatt Virtual Network konfiguriert werden.
1. Wählen Sie **Anwenden** und dann **Speichern** aus.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Aktivieren der Zonenredundanz":::

> [!IMPORTANT]
> Die öffentliche IP-Adresse am Standort ändert sich, wenn Sie Verfügbarkeitszonen aktivieren, hinzufügen oder entfernen. Beim Aktualisieren von Verfügbarkeitszonen in einer Region mit Netzwerkeinstellungen müssen Sie eine andere öffentliche IP-Adressressource als die zuvor eingerichtete konfigurieren.

> [!NOTE]
> Es kann 15 bis 45 Minuten dauern, bis die Änderung für Ihre API Management-Instanz übernommen wird.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr darüber, wie Sie eine [Azure API Management-Dienstinstanz für mehrere Azure-Regionen bereitstellen](api-management-howto-deploy-multi-region.md).
* Sie können Zonenredundanz auch mithilfe einer [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-simple-zones) aktivieren.
* Informieren Sie sich über [Azure-Dienste mit Unterstützung für Verfügbarkeitszonen](../availability-zones/az-region.md).
* Weitere Informationen zum Erstellen für [Zuverlässigkeit](/azure/architecture/framework/resiliency/app-design) in Azure.
