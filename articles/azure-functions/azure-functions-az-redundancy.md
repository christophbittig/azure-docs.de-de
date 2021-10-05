---
title: Azure Functions-Verfügbarkeitszonenunterstützung auf elastischen Premiumplänen
description: Lernen Sie, wie Sie die Verfügbarkeitszonenredundanz mit Azure Functions für hochverfügbare Funktionsanwendungen auf elastischen Premiumplänen nutzen können.
ms.topic: conceptual
ms.author: johnguo
ms.date: 09/07/2021
ms.custom: references_regions
ms.openlocfilehash: 796f4db37429e73dd1c3d50dc71b0a599977d861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700427"
---
# <a name="azure-functions-support-for-availability-zone-redundancy"></a>Azure Functions-Unterstützung für Verfügbarkeitszonenredundanz

Verfügbarkeitszonen (AZ)-Unterstützung für Azure Functions ist jetzt auf elastischen Premium- und dedizierten (App Service) Plänen verfügbar. Eine zonenredundante Azure Function-Anwendung gleicht ihre Instanzen automatisch zwischen Verfügbarkeitszonen aus, um eine höhere Verfügbarkeit zu erreichen. Dieses Dokument fokussiert auf die Unterstützung der Zonenredundanz für elastische Premium Funktions-Pläne. Für Zonenredundanz bei dedizierten Plänen, siehe [hier](../app-service/how-to-zone-redundancy.md).

## <a name="overview"></a>Übersicht

Eine [Verfügbarkeitszone](../availability-zones/az-overview.md#availability-zones) ist ein Hochverfügbarkeitsangebot, das Ihre Anwendungen und Daten vor Fehlschlägen im Rechenzentrum schützt. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Um die Widerstandsfähigkeit zu gewährleisten, gibt es mindestens drei separate Zonen in allen aktivierten Regionen. Sie können Hochverfügbarkeit in Ihre Anwendungsarchitektur einbauen, indem Sie Ihre Rechen-, Speicher-, Netz- und Datenressourcen innerhalb einer Zone zusammenlegen und in anderen Zonen replizieren.

Eine zonenredundante Funktions-App verteilt automatisch die Last der Instanzen, auf denen Ihre App ausgeführt wird, zwischen den Verfügbarkeitszonen in der Region. Bei zonenredundanten elastischen Premium-Apps werden die Instanzen, auf denen die App ausgeführt wird, auch dann gleichmäßig auf Verfügbarkeitszonen verteilt, wenn die App herunter- und hochskaliert wird.

## <a name="requirements"></a>Anforderungen

> [!IMPORTANT]
> Achten Sie bei der [Auswahl eines Speicherkontos](storage-considerations.md#storage-account-requirements) für Ihre Funktions-App darauf, dass Sie ein [zonenredundantes Speicherkonto (ZRS) verwenden.](../storage/common/storage-redundancy.md#zone-redundant-storage) Andernfalls kann es im Falle eines zonalen Ausfalls zu unerwartetem Verhalten der Funktionen kommen, da sie von Speicher abhängig sind. 

- Es werden sowohl Windows als auch Linux unterstützt.
- Muss auf einem [elastischen Premium-](functions-premium-plan.md) oder dedizierten Hosting-Plan gehostet werden. Anweisungen zur Zonenredundanz mit einem dedizierten (App Service) Hosting-Plan finden Sie [hier](../app-service/how-to-zone-redundancy.md).
  - Die Unterstützung von Verfügbarkeitszonen (AZ) ist derzeit nicht für Funktionsanwendungen auf [Verbrauch](consumption-plan.md)splänen verfügbar.
- Für zonenredundante Pläne muss eine Mindestanzahl von 3 Instanzen angegeben werden.
- Funktionsanwendungen auf einem elastischen Premium-Plan müssen zusätzlich eine Mindestanzahl von 3 [bereitstehenden Instanzen](functions-premium-plan.md#always-ready-instances) haben.
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
- Zu diesem Zeitpunkt muss durch [ARM-Vorlage](../azure-resource-manager/templates/index.yml) erstellt werden.

## <a name="how-to-deploy-a-function-app-on-a-zone-redundant-premium-plan"></a>Bereitstellung einer Funktions-App auf einem zonenredundanten Premium-Plan

Für die anfängliche Erstellung eines zonenredundanten elastischen Premium Functions-Plans müssen Sie die Bereitstellung über [ARM-Vorlagen](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md). Nachdem er erfolgreich erstellt wurde, können Sie den Funktionsplan über das Azure-Portal und das CLI-Tooling anzeigen und mit ihm interagieren. Eine ARM-Vorlage ist nur für die erstmalige Erstellung des Funktionsplans erforderlich. Eine Anleitung zum Hosten von Funktionen auf Premium-Plänen finden Sie [hier](functions-infrastructure-as-code.md#deploy-on-premium-plan). Nachdem der zonenredundante Plan erstellt und bereitgestellt wurde, ist jede Funktionsanwendung, die auf Ihrem neuen Plan gehostet wird, zonenredundant. 

Die einzigen Eigenschaften, die bei der Erstellung eines zonenredundanten Funktionsplans beachtet werden müssen, sind die neue Eigenschaft **zoneRedundant** und die Felder für die Anzahl der Funktionsplaninstanzen (**Kapazität**). Die Eigenschaft **zoneRedundant** muss auf **true** gesetzt werden, und die Eigenschaft **Kapazität** sollte auf der Grundlage der Arbeitslastanforderungen festgelegt werden, jedoch nicht weniger als 3. Die Wahl der richtigen Kapazität hängt von mehreren Faktoren und Strategien für hohe Verfügbarkeit/Fehlertoleranz ab. Eine gute Faustregel ist, genügend Instanzen für die Anwendung sicherzustellen, so dass beim Verlust einer Zone von Instanzen genügend Kapazität übrig bleibt, um die erwartete Last zu bewältigen.

> [!IMPORTANT]
> Azure Function Apps, die auf einem elastischen Premium-, zonenredundanten Funktionsplan gehostet werden, müssen eine Mindestanzahl von 3 [immer bereiten Instanzen](functions-premium-plan.md#always-ready-instances) haben. Damit soll sichergestellt werden, dass eine zonenredundante Funktionsanwendung immer über genügend Instanzen verfügt, um mindestens eine Workerrolle pro Zone zu versorgen.

Unten sehen Sie ein ARM-Vorlagen-Codeausschnitt für einen zonenredundanten Premium-Funktionsplan: Es zeigt das neue Feld **zoneRedundant** und die Spezifikation **Kapazität**.

```
    "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2021-01-15",
            "name": "your_plan_name_here",
            "location": "Central US",
            "sku": {
                "name": "EP3",
                "tier": "ElasticPremium",
                "size": "EP3",
                "family": "EP", 
                "capacity": 3
            },
            "kind": "elastic",
            "properties": {
                "perSiteScaling": false,
                "elasticScaleEnabled": true,
                "maximumElasticWorkerCount": 20,
                "isSpot": false,
                "reserved": false,
                "isXenon": false,
                "hyperV": false,
                "targetWorkerCount": 0,
                "targetWorkerSizeId": 0, 
                "zoneRedundant": true
            }
        }
    ]
```

Um mehr zu lernen, siehe [Automatisierte Ressourcenbereitstellung für Ihre Funktionsanwendung in Azure Functions](functions-infrastructure-as-code.md).
