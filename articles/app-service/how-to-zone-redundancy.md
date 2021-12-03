---
title: Unterstützung für Verfügbarkeitszonen für öffentlichen, mehrinstanzenfähigen App Service
description: Erfahren Sie, wie Sie Ihren App Service so bereitstellen, dass Ihre Apps zonenredundant sind.
author: seligj95
ms.topic: article
ms.date: 11/16/2021
ms.author: jordanselig
ms.custom: references_regions
ms.openlocfilehash: e0388695708e8ab3c47ea8049038fb95384dabd2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551291"
---
# <a name="availability-zone-support-for-public-multi-tenant-app-service"></a>Unterstützung für Verfügbarkeitszonen für öffentlichen, mehrinstanzenfähigen App Service

Microsoft Azure App Service kann in [Verfügbarkeitszonen (AZ)](../availability-zones/az-overview.md) bereitgestellt werden, um Resilienz und Zuverlässigkeit für Ihre unternehmenskritischen Workloads zu erzielen. Diese Architektur wird auch als „Zonenredundanz“ bezeichnet.

Eine App befindet sich in einem App Service-Plan (ASP), und der App Service-Plan wiederum befindet sich in einer einzelnen Skalierungseinheit. Wenn ein App Service als zonenredundant konfiguriert ist, verteilt die Plattform die VM-Instanzen im App Service-Plan automatisch auf alle drei Zonen in der ausgewählten Region. Wenn eine Kapazität größer als drei angegeben und die Anzahl der Instanzen durch drei teilbar ist, werden die Instanzen gleichmäßig verteilt. Andernfalls werden Anzahlen von Instanzen über 3*N hinaus auf die verbleibenden ein oder zwei Zonen verteilt. Für App Services, die nicht als zonenredundant konfiguriert sind, werden die VM-Instanzen in einer einzelnen Zone in der ausgewählten Region platziert.

## <a name="requirements"></a>Requirements (Anforderungen)

Zonenredundanz ist eine Eigenschaft des App Service-Plans. Im Folgenden finden Sie die aktuellen Anforderungen/Einschränkungen zum Aktivieren von Zonenredundanz:

- Sowohl Windows als auch Linux wird unterstützt.
- Erfordert einen **Premium v2**- oder **Premium v3**-App Service-Plan.
- Mindestanzahl der Instanzen von drei
  - Die Plattform erzwingt diese Mindestanzahl im Hintergrund, wenn Sie eine Instanzanzahl von weniger als drei angeben.
- Kann in jeder der folgenden Regionen aktiviert werden:
  - USA, Westen 2
  - USA, Westen 3
  - USA (Mitte)
  - East US
  - USA (Ost) 2
  - Kanada, Mitte
  - Brasilien Süd
  - Nordeuropa
  - Europa, Westen
  - Deutschland, Westen-Mitte
  - Frankreich, Mitte
  - UK, Süden
  - Japan, Osten
  - Asien, Südosten
  - Australien (Osten)
- Zonenredundanz kann nur beim Erstellen eines **neuen** App Service-Plans angegeben werden.
  - Derzeit können Sie einen bereits vorhandenen App Service-Plan nicht konvertieren. Details zum Erstellen eines neuen App Service-Plans, der Zonenredundanz unterstützt, finden Sie im nächsten Punkt der Aufzählung.
- Zonenredundanz wird nur im neueren Teil des App Service-Speicherbedarfs unterstützt.
  - Zurzeit, wenn Sie Pv3 nutzen, ist es möglich, dass Sie bereits in einem Speicherbedarf sind, der Zonenredundanz unterstützt. In diesem Szenario können Sie einen neuen App Service-Plan erstellen und Zonenredundanz angeben, wenn Sie den neuen App Service-Plan erstellen.
  - Wenn Sie nicht Pv3 oder eine Skalierungseinheit verwenden, die Zonenredundanz unterstützt, sich in einer nicht unterstützten Region befinden oder unsicher sind, führen Sie die folgenden Schritte aus:
    - Erstellen einer neuen Ressourcengruppe in einer unterstützten Region
        - Hierdurch wird sichergestellt, dass die App Service-Steuerungsebene eine Skalierungseinheit in der ausgewählten Region finden kann, die Zonenredundanz unterstützt.
    - Erstellen eines neuen App Service-Plans (und einer neuen App) in einer Region Ihrer Wahl mithilfe der **neuen** Ressourcengruppe
    - Sicherstellen, dass die Eigenschaft „zoneRedundant“ (wie unten beschrieben) beim Erstellen des neuen App Service-Plans auf „true“ festgelegt ist
- Muss mithilfe von [Azure Resource Manager-Vorlagen (ARM)](../azure-resource-manager/templates/overview.md) erstellt werden.

Wenn eine Zone ausfällt, erkennt die App Service-Plattform verlorene Instanzen und versucht automatisch, neue Ersatzinstanzen zu finden. Wenn Sie außerdem automatische Skalierung konfiguriert haben und diese entscheidet, dass weitere Instanzen benötigt werden, gibt die automatische Skalierung auch eine Anforderung an App Service aus, um weitere Instanzen hinzuzufügen (das Verhalten der automatischen Skalierung ist unabhängig vom Verhalten der App Service-Plattform). Es ist wichtig zu beachten, dass es keine Garantie dafür gibt, dass Anforderungen zusätzlicher Instanzen in einem Zonenausfallszenario erfolgreich sein werden, da das Auffüllen verlorener Instanzen auf „Best Effort“-Basis erfolgt. Die empfohlene Lösung besteht in der Bereitstellung Ihrer App Service-Pläne, um den Verlust einer Zone abzufangen, wie im nächsten Abschnitt dieses Artikels beschrieben.

Anwendungen, die in einem für Zonenredundanz aktivierten App Service-Plan bereitgestellt werden, werden weiterhin ausgeführt und bedienen Datenverkehr, auch wenn andere Zonen in derselben Region ausfallen. Es ist jedoch möglich, dass Verhalten außerhalb der Laufzeit, einschließlich Skalieren von App Service-Plänen, Anwendungserstellung, -konfiguration und -veröffentlichung, weiterhin von einem Ausfall in anderen Verfügbarkeitszonen betroffen sind. Zonenredundanz für App Service-Pläne stellt nur eine kontinuierliche Uptime für bereitgestellte Anwendungen sicher.

Wenn die App Service-Plattform einem zonenredundanten App Service-Plan Instanzen zuordnet, verwendet sie einen [„Best Effort“-Zonenausgleich, der von den zugrunde liegenden Azure-VM-Skalierungsgruppen angeboten wird](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing). Ein App Service-Plan ist „ausgeglichen“, wenn jede Zone entweder über dieselbe Anzahl von VMs oder +/-1 VM in allen anderen Zonen verfügt, die vom App Service-Plan verwendet werden.

## <a name="how-to-deploy-a-zone-redundant-app-service"></a>Bereitstellen eines zonenredundanten App Service

Derzeit müssen Sie eine ARM-Vorlage verwenden, um einen zonenredundanten App Service zu erstellen. Sobald der App Service-Plan über eine ARM-Vorlage erstellt wurde, kann er über das Azure-Portal und mit Tools der CLI angezeigt und mit ihm interagiert werden. Eine ARM-Vorlage ist nur für die erstmalige Erstellung des App Service-Plans erforderlich.

Die einzigen Änderungen, die in einer ARM-Vorlage erforderlich sind, um einen zonenredundanten App Service anzugeben, sind die neue Eigenschaft ***zoneRedundant** _ (erforderlich) und optional die App Service-Planinstanzanzahl (_*_capacity_*_ (Kapazität)) für die Ressource [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms?tabs=json). Wenn Sie keine Kapazität angeben, verwendet die Plattform standardmäßig drei. Die Eigenschaft _*_zoneRedundant_*_ sollte auf _*_true_*_ und _ *_capacity_** (Kapazität) sollte auf Grundlage der Workloadanforderung festgelegt werden, jedoch nicht niedriger als drei. Eine gute Faustregel für die Auswahl der Kapazität (capacity) ist es, genügend Instanzen für die Anwendung sicherzustellen, sodass beim Verlust einer Zone von Instanzen genügend Kapazität übrig bleibt, um die erwartete Last zu bewältigen.

> [!TIP]
> Um die Instanzkapazität zu bestimmen, können Sie die folgende Berechnung verwenden:
>
> Da die Plattform VMs auf drei Zonen verteilt und Sie mindestens den Ausfall einer Zone abfangen können müssen, multiplizieren Sie die Instanzanzahl des Spitzenworkloads mit dem Faktor „Zonen/(Zonen-1)“ oder „3/2“. Wenn Ihre typische Spitzenworkload beispielsweise 4 Instanzen erfordert, sollten Sie 6 Instanzen bereitstellen: (2/3 * 6 Instanzen) = 4 Instanzen.
>

Der folgende ARM-Vorlagencodeausschnitt zeigt die neue Spezifikation der Eigenschaft ***zoneRedundant** _ und *_capacity_* (Kapazität).

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "your-appserviceplan-name-here",
    "location": "West US 3",
    "sku": {
        "name": "P1v3",
        "tier": "PremiumV3",
        "size": "P1v3",
        "family": "Pv3",
        "capacity": 3
    },
    "kind": "app",
    "properties": {
        "zoneRedundant": true
    }
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie ARM-Vorlagen erstellen und bereitstellen](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)

> [!div class="nextstepaction"]
> [ARM-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/)
