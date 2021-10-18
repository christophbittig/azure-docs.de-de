---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/27/2021
ms.author: rogarana
ms.openlocfilehash: aedfaf0606547653e88ad6e120f51a542dabdb95
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155160"
---
Sie können eine Reihe von Datenträgern an einen virtuellen Azure-Computer anfügen. Basierend auf den Skalierbarkeits- und Leistungszielen für Datenträger eines virtuellen Computers können Sie die Anzahl und den Typ der Datenträger festlegen, die Sie benötigen, um Ihre Anforderungen an Leistung und Kapazität zu erfüllen.

> [!IMPORTANT]
> Begrenzen Sie zur Optimierung der Leistung die Anzahl stark ausgelasteter Datenträger, die an den virtuellen Computer angefügt sind, um eine mögliche Drosselung zu vermeiden. Wenn nicht alle angefügten Datenträger zum gleichen Zeitpunkt stark ausgelastet sind, kann der virtuelle Computer eine größere Anzahl von Datenträgern unterstützen.

**Verwaltete Azure-Datenträger:**

In der folgenden Tabelle sind die standardmäßigen und maximal zulässigen Limits für die Anzahl von Ressourcen pro Region und Abonnement aufgeführt. Die Grenzwerte gelten unabhängig davon, ob Datenträger mit plattformseitig verwalteten Schlüsseln oder kundenseitig verwalteten Schlüsseln verschlüsselt werden. Es gilt kein Limit für die Anzahl von verwalteten Datenträgern, Momentaufnahmen und Images pro Ressourcengruppe.  

> | Resource | Begrenzung |
> | --- | --- |
> | Verwaltete Standarddatenträger | 50.000 |
> | Verwaltete SSD Standard-Datenträger | 50.000 |
> | Verwaltete Premium-Datenträger | 50.000 |
> | Standard_LRS-Momentaufnahmen<sup>1</sup> | 75.000 |
> | Standard_ZRS-Momentaufnahmen<sup>1</sup> | 75.000 |
> | Verwaltetes Image | 50.000 |

<sup>1</sup> Die Gesamtzahl der vollständigen Datenträger-Momentaufnahmen, über die ein einzelner Datenträger verfügen kann, beträgt 200. Ein einzelner Datenträger kann auch über 200 inkrementelle Momentaufnahmen verfügen, die getrennt von vollständigen Datenträger-Momentaufnahmen gezählt werden. 

**Für Standardspeicherkonten:** Ein Standardspeicherkonto hat eine maximale Gesamtanforderungsrate von 20.000 IOPS. Die gesamten IOPS auf allen Datenträgern eines virtuellen Computers in einem Standardspeicherkonto dürfen dieses Limit nicht überschreiten.
  
Basierend auf dem Limit für Anforderungsraten können Sie die Anzahl der Datenträger mit hoher Auslastung ungefähr berechnen, die von einem Speicherkonto unterstützt werden. Im Basic-Tarif liegt die maximal zulässige Anzahl von Datenträgern mit hoher Auslastung für einen virtuellen Computer beispielsweise bei 66 (20.000/300 IOPS pro Datenträger). Die maximale Anzahl von Datenträgern mit hoher Auslastung für einen virtuellen Computer im Standard-Tarif liegt bei etwa 40 Datenträgern (20.000/500 IOPS pro Datenträger). 

**Für Storage Premium-Konten:** Ein Storage Premium-Konto hat eine maximale Gesamtdurchsatzrate von 50 Gbit/s. Der Gesamtdurchsatz aller Ihrer VM-Datenträger darf dieses Limit nicht überschreiten.

