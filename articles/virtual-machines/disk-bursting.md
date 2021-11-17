---
title: Verwaltetes Datenträgerbursting
description: Erfahren Sie mehr über Datenträgerbursting für Azure-Datenträger und virtuelle Azure-Computer.
author: roygara
ms.author: rogarana
ms.date: 11/09/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1ff0997686b69cb2b688eec0829e0b39915a53e1
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134896"
---
# <a name="managed-disk-bursting"></a>Verwaltetes Datenträgerbursting

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Azure bietet die Möglichkeit, die IOPS- und MB/s-Leistung für Datenträger zu steigern. Dies wird sowohl bei virtuellen Computern als auch bei Datenträgern als „Bursting“ bezeichnet. Das Bursting für VMs und Datenträger lässt sich effektiv nutzen, um sowohl auf Ihren VMs als auch auf dem Datenträger eine bessere Burstingleistung zu erzielen.

Das Bursting für virtuelle Azure-Computer ist unabhängig vom Bursting für Datenträgerressourcen. Sie benötigen keinen virtuellen Computer mit Burst-Unterstützung, um Bursting für einen geeigneten angefügten Datenträger nutzen zu können. Analog dazu muss an einen virtuellen Computer mit Burst-Unterstützung kein Datenträger mit Burst-Unterstützung angefügt sein, um Bursting für den virtuellen Computer nutzen zu können.

## <a name="common-scenarios"></a>Häufige Szenarios
Die folgenden Szenarien können von einem Bursting stark profitieren:
- **Verbesserung der Startzeiten:** Mit Bursting wird Ihre Instanz schneller starten. Beispielsweise ist der standardmäßige Betriebssystemdatenträger für Premium-fähige virtuelle Computer der P4-Datenträger, der eine bereitgestellte Leistung von bis zu 120 IOPS und 25 MB/s bietet. Mit Bursting kann der P4 bis zu 3500 IOPS und 170 MB/s erreichen, wodurch der Start maximal um das Sechsfache beschleunigt werden kann.
- **Verarbeiten von Batchaufträgen**: Einige Anwendungsworkloads sind zyklischer Natur. Sie benötigen die meiste Zeit eine Baselineleistung und für kurze Zeiträume eine höhere Leistung. Ein Beispiel hierfür ist ein Buchhaltungsprogramm, das tägliche Transaktionen verarbeitet, die eine geringe Menge an Datenverkehr auf dem Datenträger erfordern. Am Ende des Monats würde dieses Programm den Abgleich von Berichten abschließen, die einen viel höheren Datenverkehr benötigen.
- **Datenverkehrsspitzen**: Webserver und ihre Anwendungen können jederzeit einen Anstieg des Datenverkehrs erfahren. Wenn Ihr Webserver durch virtuelle Computer oder Datenträger gesichert ist, die Bursting verwenden, wären die Server besser für die Bewältigung von Datenverkehrsspitzen gerüstet. 

## <a name="disk-level-bursting"></a>Bursting auf Datenträgerebene

Derzeit gibt es zwei verwaltete Datenträgertypen, für die Bursting möglich ist: [SSD Premium](disks-types.md#premium-ssds) und [SSD Standard](disks-types.md#standard-ssds). Für andere Datenträgertypen ist derzeit kein Bursting möglich. Es gibt zwei Burstingmodelle für Datenträger:

- Ein bedarfsgesteuertes Burstingmodell, bei dem der Datenträger immer dann ein Bursting vornimmt, wenn die Anforderungen seine aktuelle Kapazität übersteigen. Bei diesem Modell fallen zusätzliche Gebühren beim Bursting auf dem Datenträger an. Bedarfsgesteuertes Bursting ist nur für Premium-SSDs verfügbar, die größer als 512 GiB sind.
- Ein auf Guthaben basierendes Modell, bei dem das Bursting auf dem Datenträger nur erfolgt, wenn Guthaben für das Bursting im Guthaben-Bucket akkumuliert wurde. Bei diesem Modell fallen keine zusätzlichen Gebühren an, wenn das Bursting auf dem Datenträger erfolgt. Das guthabenbasierte Bursting ist nur für SSD-Datenträger vom Typ Premium und Standard mit maximal 512 GiB verfügbar.

Bei Azure [SSD Premium](disks-types.md#premium-ssds) können Sie beide Burstingmodelle verwenden, bei [SSD Standard](disks-types.md#standard-ssds) ist derzeit jedoch nur das guthabenbasierte Bursting möglich.

Zusätzlich kann die [Leistungsstufe der verwalteten Datenträger geändert werden](disks-change-performance.md), was ideal sein kann, wenn Ihr Workload sonst im Burstmodus ausgeführt wird.

|  |Guthabenbasiertes Bursting  |Bedarfsgesteuertes Bursting  |Ändern der Leistungsstufe  |
|---------|---------|---------|---------|
| **Szenarios**|Ideal für die kurzfristige Skalierung (30 Minuten oder weniger).|Ideal für die kurzfristige Skalierung (keine Zeitbeschränkung).|Ideal, wenn Ihre Workload andernfalls ständig im Burstmodus ausgeführt werden würde.|
|**Kosten**     |Kostenlos         |Die Kosten sind variabel. Weitere Informationen finden Sie im Abschnitt [Abrechnung](#billing).        |Die Kosten für die einzelnen Leistungsstufen sind festgelegt. Details finden Sie unter [Preise für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).         |
|**Verfügbarkeit**     |Nur verfügbar für SSD-Datenträger vom Typ Premium und Standard mit maximal 512 GiB.         |Nur verfügbar für SSD Premium-Datenträger, die größer als 512 GiB sind.         |Verfügbar für alle SSD Premium-Größen.         |
|**Aktivierung**     |Standardmäßig auf berechtigten Datenträgern aktiviert.         |Muss vom Benutzer aktiviert werden.         |Der Benutzer muss seine Leistungsstufe manuell ändern.         |

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Aktivieren des bedarfsgesteuerten Burstings finden Sie unter [Aktivieren des bedarfsgesteuerten Burstings](disks-enable-bursting.md).
- Wie Sie einen Einblick in Ihre Burstingressourcen erhalten, erfahren Sie unter [Metriken zum Datenträgerbursting](disks-metrics.md).
- Genaue Informationen dazu, in welchem Umfang das Bursting für die einzelnen zulässigen Datenträgergrößen möglich ist, finden Sie unter [Skalierbarkeits- und Leistungsziele für VM-Datenträger](disks-scalability-targets.md).