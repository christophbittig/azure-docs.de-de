---
title: Unterstützung für Service-Connector-Regionen
description: Verfügbarkeits- und Supportliste für Service-Connector-Region
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021, references_regions
ms.openlocfilehash: 75678da43f400eb3ee56f956dcd307207b501e91
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853052"
---
# <a name="service-connector-region-support"></a>Support von Service-Connector-Region

Wenn Sie mit Service Connector eine Dienstverbindung erstellen, wird die konzeptionelle Verbindungsressource standardmäßig in derselben Region wie Ihre Compute-Dienstinstanz bereitgestellt. Auf dieser Seite werden die Unterstützungsinformationen für die Region und das entsprechende Verhalten von Service Connector Public Preview angezeigt.

## <a name="supported-regions-with-regional-endpoint"></a>Unterstützte Regionen mit regionalem Endpunkt

Wenn sich Ihre Computedienstinstanz in einer der von Service Connector unterstützten unten aufgeführten Regionen befindet, können Sie mit Service Connector Dienstverbindungen erstellen und verwalten.

- USA, Westen-Mitte
- Europa, Westen
- Nordeuropa
- East US
- USA, Westen 2

## <a name="supported-regions-with-geographical-endpoint"></a>Unterstützte Regionen mit geografischem Endpunkt

Ihre Compute-Service-Instanz wird möglicherweise in der Region erstellt, in der Service Connector Unterstützung für geografische Regionen bietet. Dies bedeutet, dass Ihre Dienstverbindung in einer anderen Region als Ihre Compute-Instanz erstellt wird. Sie sehen ein Banner mit diesen Informationen, wenn Sie eine Dienstverbindung erstellen. Der regionale Unterschied kann sich auf Ihre Compliance, Datenresidenz und Datenlatenz auswirken.

- USA (Ost) 2
- USA, Westen 3
- USA Süd Mitte

## <a name="not-supported-regions-in-public-preview"></a>Nicht unterstützte Regionen in der öffentlichen Vorschau

Sie können weiterhin den CLI-Befehl von Service Connector oder den Portalknoten in der Region sehen, die Service Connector unterstützt. Sie können in diesen Regionen jedoch keine Dienstverbindungen erstellen oder verwalten. Das Produktteam arbeitet aktiv daran, mehr Regionen zu ermöglichen.
