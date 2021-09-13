---
title: Richtliniendefinitionen zum Kennzeichnen von Ressourcen
description: In diesem Artikel werden die Azure-Richtliniendefinitionen beschrieben, die Sie zuweisen können, um Konformität in Bezug auf Tags sicherzustellen.
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: f7af799423b0aceac66dfa97e92c92d25c3916db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456255"
---
# <a name="assign-policy-definitions-for-tag-compliance"></a>Zuweisen von Richtliniendefinitionen für Tagkonformität

Sie verwenden [Azure Policy](../../governance/policy/overview.md), um Taggingregeln und -konventionen zu erzwingen. Durch die Erstellung einer Richtlinie vermeiden Sie das Szenario, dass Ressourcen in Ihrem Abonnement bereitgestellt werden, die nicht über die erwarteten Tags für Ihr Unternehmen verfügen. Anstelle der manuellen Anwendung von Tags oder der Suche nach Ressourcen, die nicht konform sind, erstellen Sie eine Richtlinie, die die erforderlichen Tags während der Bereitstellung automatisch anwendet. Tags können jetzt auch auf vorhandene Ressourcen mit dem neuen Effekt [Ändern](../../governance/policy/concepts/effects.md#modify) und einer [Korrekturaufgabe](../../governance/policy/how-to/remediate-resources.md) angewendet werden. Im folgenden Abschnitt werden exemplarische Richtliniendefinitionen für Tags gezeigt.

## <a name="policy-definitions"></a>Richtliniendefinitionen

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Taggen von Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](tag-resources.md).
* Nicht alle Ressourcentypen unterstützen Tags. Um festzustellen, ob Sie ein Tag auf einen Ressourcentyp anwenden können, lesen Sie [Tagunterstützung für Azure-Ressourcen](tag-support.md).
