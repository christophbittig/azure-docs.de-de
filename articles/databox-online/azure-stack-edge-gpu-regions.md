---
title: Auswählen einer Region für Microsoft Azure Stack Edge Pro mit GPU | Microsoft-Dokumentation
description: In diesem Artikel werden Regionsoptionen für Azure Stack Edge-Dienst, -Datenspeicher und -Geräte für Azure Stack Edge Pro mit GPU, Azure Stack Edge Pro R und Azure Stack Edge Mini R erläutert.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: alkohli
ms.openlocfilehash: 7b585c61dd46339be71e66be66b402a0e107c194
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429891"
---
# <a name="choosing-a-region-for-azure-stack-edge"></a>Auswählen einer Region für Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

In diesem Artikel wird die Verfügbarkeit von Regionen für Azure Stack Edge-Dienst, -Datenspeicher und -Gerätelieferungen für ein Azure Stack Edge-Gerät beschrieben. Zudem wird erläutert, wie die Auswahl der Region sich auf Dienst, Leistung und Abrechnung auswirkt.


## <a name="overview"></a>Übersicht

Die Azure-Rechenzentren werden in verschiedenen geografischen Regionen der Welt betrieben, um die Ansprüche der Kunden an Leistung und Anforderungen sowie die Präferenzen in Bezug auf den Datenspeicherort zu erfüllen. Ein Azure-Geografie ist ein definiertes Gebiet der Welt, in dem sich mindestens eine Azure-Region befindet. Eine Azure-Region ist ein Bereich in einem Gebiet mit mindestens einem Rechenzentrum.

Das Auswählen einer Azure-Region ist sehr wichtig, und die Wahl der Region wird von Faktoren wie Datenresidenz und Datenhoheit, Dienstverfügbarkeit, Leistung, Kosten und Redundanz beeinflusst. Weitere Informationen dazu, wie Sie eine Region auswählen, finden Sie unter [Auswählen der richtigen Azure-Region](https://azure.microsoft.com/overview/datacenters/how-to-choose/).

Bei einem Azure Stack Edge Pro GPU-Gerät wird die Auswahl der Region durch folgende Faktoren bestimmt:

- Regionen, in denen der Azure Stack Edge-Dienst verfügbar ist.
- Regionen, in denen die Speicherkonten zum Speichern von Azure Stack Edge-Daten platziert werden sollten, damit eine optimale Leistung erzielt wird.
- Länder/Regionen, in die das Azure Stack Edge-Gerät geliefert werden kann.


## <a name="region-availability-for-the-service"></a>Regionale Verfügbarkeit für den Dienst

Der Azure Stack Edge-Dienst wird derzeit in den öffentlichen Regionen „USA, Osten“, „Europa, Westen“ und „Asien, Südosten“ unterstützt. **Diese drei Regionen unterstützen geografische Standorte auf der ganzen Welt.**

Die Region des Diensts ist das Land oder die Region, das bzw. die der Azure Stack Edge-Verwaltungsressource zugewiesen ist. Die Verwaltungsressource verwendet den Azure Stack Edge-Dienst, um ein Azure Stack Edge-Gerät zu aktivieren, bereitzustellen und zurückzugeben.

Der Azure Stack Edge-Dienst überwacht auch die Integrität des Geräts: Probleme, Fehler, Warnungen, Reaktionsfähigkeit. Zudem überwacht der Dienst die Nutzung und Verbrauchseinheiten für die Abrechnung – als Informationen der Steuerungsebene des Geräts. Die Region, die der Verwaltungsressource zugewiesen ist, ist die Region, in der die Abrechnung erfolgt.

Das Gerät muss eine Verbindung mit dem Azure Stack Edge-Dienst herstellen, um aktiviert werden zu können. Wenn Sie keine weitere Interaktion mit dem Dienst wünschen, können Sie das Gerät in den getrennten Modus schalten. 

Über den Azure Stack Edge-Dienst werden keine Daten übertragen. Daten werden zwischen dem Gerät und dem Speicherkonto übertragen, das in der Region des Kunden bereitgestellt wurde, aus der die Daten stammen. 

Üblicherweise sollte ein Standort ausgewählt werden, der möglichst nah zu der geografischen Region liegt, in der das Gerät bereitgestellt wird. Das Gerät und der Dienst können aber auch in anderen Standorten bereitgestellt werden.

## <a name="region-availability-for-data-storage"></a>Regionale Verfügbarkeit für Datenspeicher

Azure Stack Edge-Daten werden in Azure-Speicherkonten gespeichert, und diese Konten sind in allen Azure-Regionen verfügbar. Wenn Sie ein Azure-Speicherkonto erstellen, wählen Sie den primären Standort dieses Kontos aus. Diese Auswahl bestimmt die Region, in der sich die Daten befinden.

Sie wählen ein Speicherkonto aus, wenn Sie eine [Freigabe auf Ihrem Gerät erstellen](azure-stack-edge-gpu-deploy-add-shares.md#add-a-share). Ihr Azure Stack Edge-Dienst und Ihr Azure-Speicher können sich an zwei getrennten Standorten befinden.

Grundsätzlich sollten Sie die Region auswählen, die für den Dienst für Ihr Speicherkonto am nächsten liegt. Es kann aber sein, dass die nächstgelegene Microsoft Azure-Region nicht die Region mit der kürzesten Wartezeit ist. Die Latenz bestimmt die Leistung des Netzwerkdienst und somit die Leistung des Geräts. Wenn Sie ein Speicherkonto in einer anderen Region auswählen, müssen Sie daher wissen, welche Latenzen es zwischen Ihrem Dienst und der Region gibt, die Ihrem Speicherkonto zugeordnet ist.

## <a name="region-of-device"></a>Region des Geräts

In der Produktübersicht finden Sie die Länder und geografischen Regionen, in denen Azure Stack Edge-Modelle verfügbar sind:

- [Regionale Verfügbarkeit für Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-overview.md#region-availability)
- [Regionale Verfügbarkeit für Azure Stack Edge Pro R](azure-stack-edge-pro-r-overview.md#region-availability)
- [Regionale Verfügbarkeit für Azure Stack Edge Mini R](azure-stack-edge-mini-r-overview.md#region-availability)

In diesen geografischen Regionen kann Microsoft physische Hardwarekomponenten sowie Ersatzteile für Hardware für Azure Stack Edge liefern.

> [!IMPORTANT]
> Platzieren Sie ein physisches Azure Stack Edge-Gerät nicht in einer Region, in der Azure Stack Edge nicht unterstützt wird. Microsoft hat keine Möglichkeit, Ersatzteile in Ländern/Regionen zu liefern, in denen Azure Stack Edge nicht unterstützt wird.


## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Preise für verschiedene Azure Stack Edge-Modelle](https://azure.microsoft.com/pricing/details/azure-stack/edge/).
* [Bereiten Sie die Bereitstellung Ihres Azure Stack Edge Pro-Geräts vor](azure-stack-edge-gpu-deploy-prep.md).
