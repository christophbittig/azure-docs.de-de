---
title: 'Azure Defender für DNS: Vorteile und Features'
description: Enthält eine Beschreibung der Vorteile und Features von Azure Defender für DNS.
author: memildin
ms.author: memildin
ms.date: 10/11/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ad2b761a747401646c48dcbf32385b8e5ec153c1
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740225"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Einführung in Azure Defender für DNS

Azure Defender für DNS bietet eine zusätzliche Schutzebene für Ressourcen, die die Azure DNS-Funktion [Von Azure bereitgestellte Namensauflösung](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) nutzen. 

In Azure DNS überwacht Defender für DNS die Abfragen von diesen Ressourcen und erkennt verdächtige Aktivitäten, ohne dass zusätzliche Agents für Ihre Ressourcen erforderlich sind.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Azure Defender für DNS** wird gemäß [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Welche Vorteile bietet Azure Defender für DNS?

Azure Defender für DNS erkennt etwa folgende verdächtige und anomale Aktivitäten:

- **Datenexfiltration** aus Ihren Azure-Ressourcen mittels DNS-Tunneling
- **Schadsoftware**, die mit Command-and-Control-Servern kommuniziert
- **DNS-Angriffe** (Kommunikation mit schädlichen DNS-Resolvern) 
- **Kommunikation mit Domänen, die für schädliche Aktivitäten verwendet werden** (etwa Phishing und Crypto Mining)

Eine vollständige Liste der Warnungen, die von Azure Defender für DNS bereitgestellt werden, finden Sie auf der [Referenzseite zu Warnungen](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Abhängigkeiten

Von Azure Defender für DNS werden keine Agents verwendet. 

Aktivieren Sie Azure Defender für DNS zum Schutz Ihrer DNS-Schicht für jedes Ihrer Abonnements, wie unter [Aktivieren von Azure Defender](enable-azure-defender.md) beschrieben.


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie sich über Azure Defender für DNS informiert. 

> [!div class="nextstepaction"]
> [Aktivieren von Azure Defender](enable-azure-defender.md)

Verwandte Informationen finden Sie im folgenden Artikel: 

- Sicherheitswarnungen können von Security Center generiert oder von Security Center aus anderen Sicherheitsprodukten empfangen werden. Gehen Sie wie unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md) beschrieben vor, um alle diese Warnungen in Azure Sentinel, in ein beliebiges Drittanbieter-SIEM oder in ein beliebiges anderes externes Tool zu exportieren.
