---
title: 'Azure VMware Solution by CloudSimple: Dienst'
description: Hier erfahren Sie mehr über den CloudSimple-Dienst. Nach Erstellung des Diensts können Sie Knoten erwerben, Knoten reservieren und private Clouds erstellen.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 940b9e67bb10c33a76f6b854d7cf03f3a7a6ee49
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315119"
---
# <a name="cloudsimple-service-overview"></a>Übersicht über den CloudSimple-Dienst

Der CloudSimple-Dienst ermöglicht es Ihnen, Azure VMware Solution by CloudSimple zu nutzen.  Nach Erstellung des Diensts können Sie Knoten erwerben, Knoten reservieren und private Clouds erstellen.  Sie erstellen den CloudSimple-Dienst in jeder Azure-Region, in der der CloudSimple-Dienst verfügbar ist. Der Dienst definiert das Umkreisnetzwerk von Azure VMware Solution by CloudSimple. Dieses Umkreisnetzwerk unterstützt unter anderem Dienste wie VPN, ExpressRoute und Internetkonnektivität mit Ihren privaten Clouds.

## <a name="gateway-subnet"></a>Gatewaysubnetz

Ein Gatewaysubnetz ist für jeden CloudSimple-Dienst erforderlich und für die Region spezifisch, in der es erstellt wird. Das Gatewaysubnetz wird beim Erstellen des Umkreisnetzwerks verwendet und erfordert einen CIDR-Block vom Typ „/28“.  Der Adressraum des Gatewaysubnetzes muss eindeutig sein. Er darf sich nicht mit einem Netzwerk überschneiden, das mit der CloudSimple-Umgebung kommuniziert. Zu den Netzwerken, die mit CloudSimple kommunizieren, gehören unter anderem lokale Netzwerke und das virtuelle Azure-Netzwerk.  Ein Gatewaysubnetz kann nach der Erstellung nicht mehr gelöscht werden.  Das Gatewaysubnetz wird entfernt, wenn der Dienst gelöscht wird.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Erstellen eines CloudSimple-Diensts in Azure](quickstart-create-cloudsimple-service.md).
