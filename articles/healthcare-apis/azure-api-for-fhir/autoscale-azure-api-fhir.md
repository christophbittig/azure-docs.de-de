---
title: Autoskalierung für Azure API for FHIR
description: In diesem Artikel wird das Feature für die automatische Skalierung für Azure API for FHIR.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: d1aa4a6797fe6d7794f07b5da7832d80ac026e71
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823330"
---
# <a name="autoscale-for-azure-api-for-fhir"></a>Autoskalierung für Azure API for FHIR 

Die Azure API for FHIR als verwalteter Dienst ermöglicht Es Kunden, mit FHIR-konformen Gesundheitsdaten zu bleiben und diese sicher über die Dienst-API austauschen. Um unterschiedliche Transaktionsworkloads zu bewältigen, können Kunden manuelle Skalierung oder automatische Skalierung verwenden.

## <a name="what-is-autoscale"></a>Was ist die automatische Skalierung?

Standardmäßig ist die Azure API for FHIR auf manuelle Skalierung festgelegt. Diese Option funktioniert gut, wenn die Transaktionsworkloads bekannt und konsistent sind. Kunden können den Durchsatz über das Portal auf bis zu 10.000 anpassen und eine Anforderung zum `RU/s` Erhöhen des Grenzwerts übermitteln. 

Mit der automatischen Skalierung können Kunden verschiedene Workloads ausführen, und der Durchsatz wird ohne manuelle `RU/s` Anpassungen automatisch hoch- und herunterskaliert.

## <a name="how-to-enable-autoscale"></a>Aktivieren der automatischen Skalierung

Sie können ein einmaliges Supportticket erstellen, um das Feature für die automatische Skalierung zu aktivieren. Das Microsoft-Supportteam aktiviert das Feature für die automatische Skalierung basierend auf der Supportpriorität.

> [!NOTE]
> Das Feature für die automatische Skalierung ist nicht im Azure-Portal.

## <a name="how-to-adjust-the-maximum-throughput-rus"></a>Wie passen Sie den maximalen Durchsatz von RU/s an?

Wenn die automatische Skalierung aktiviert ist, berechnet und legt das System den Anfangswert `Tmax` fest. Die Skalierbarkeit wird durch den maximalen Durchsatzwert oder bestimmt und skaliert `RU/s` `Tmax` zwischen `0.1 *Tmax` (oder 10 % `Tmax` ) und `Tmax RU/s` . 

Sie können die Maximale oder `RU/s` den Wert erhöhen und so hoch wie vom Dienst unterstützt `Tmax` werden. Wenn der Dienst ausgelastet ist, wird `RU/s` der Durchsatz auf den Wert `Tmax` hochskaliert. Wenn sich der Dienst im Leerlauf befindet, wird der Durchsatz auf einen Wert `RU/s` von 10 % `Tmax` herunterskaliert.
 
Sie können auch den Maximalwert oder `RU/s` den Wert `Tmax` verringern. Wenn Sie den Höchstwert senken, ist der Mindestwert, den Sie festlegen können, : , gerundet auf den nächsten `RU/s` `MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)` 1000 `RU/s` .

* **Beispiel 1:** Sie verfügen über 1 GB Daten, und die höchste bereitgestellte ist `RU/s` 10.000. Der Mindestwert ist Max (**4000**, 10.000/10, 1x400) = 4000. Die erste Zahl, **4000,** wird verwendet.
* **Beispiel 2:** Sie verfügen über 20 GB Daten, und die höchste bereitgestellte ist `RU/s` 100.000. Der Mindestwert ist Max (4000, **100.000/10,** 20x400) = 10.000. Die zweite Zahl, **100.000/10 =10.000,** wird verwendet.
* **Beispiel 3:** Sie verfügen über 80 GB Daten, und die höchste bereitgestellte RU/s beträgt 300.000. Der Mindestwert ist Max (4000, 300.000/10, **80x400**) = 32.000. Die dritte Zahl, **80x400=32.000,** wird verwendet.

Sie können den Höchstwert oder den Wert über das Portal anpassen, wenn es sich um eine gültige Zahl und nicht größer als `RU/s` `Tmax` 10.000 `RU/s` ist. Sie können ein Supportticket erstellen, um einen `Tmax` Wert von mehr als 10.000 an fordern.

>[!Note] 
>Wenn die Datenspeicherung wächst, erhöht das System automatisch den maximalen Durchsatz auf die nächsten höchsten RU/s, die diese Speicherebene unterstützen können.


## <a name="how-to-migrate-to-manual-scale"></a>Wie kann ich zur manuellen Skalierung migrieren?

Ein Supportticket ist erforderlich, um die automatische Skalierung in eine manuelle Skalierung zu ändern und die Durchsatz-RU/s anzugeben. Der Mindestwert für die manuelle Skalierung, den Sie festlegen können, ist : , gerundet auf den nächsten `MAX (400, highest max RU/s ever provisioned / 100, current storage in GB * 40)` 1000 `RU/s` . Die hier verwendeten Zahlen unterscheiden sich von denen, die bei der automatischen Skalierung verwendet werden.

Sobald die Änderung abgeschlossen ist, basieren die neuen Abrechnungsraten auf der manuellen Skalierung.

## <a name="what-is-the-cost-impact-of-autoscale"></a>Welche Auswirkungen hat die automatische Skalierung auf die Kosten?

Das Feature für die automatische Skalierung verursacht Kosten, da die bereitgestellten Durchsatzeinheiten automatisch verwalten. Diese Kostensteigerung gilt nicht für Speicher- und Laufzeitkosten. Informationen zu den Preisen finden Sie unter [Azure API for FHIR Preise.](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/)

>[!div class="nextstepaction"]
>[Informationen zur Azure-API für FHIR](overview.md)
