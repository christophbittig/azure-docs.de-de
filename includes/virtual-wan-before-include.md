---
title: Datei einfügen
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ef4edb99ab2d6d4a25f2151c5b63c1c31ff91ef4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724961"
---
* Sie verfügen über ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

* Sie verfügen über ein virtuelles Netzwerk, mit dem Sie eine Verbindung herstellen möchten. 

   * Stellen Sie sicher, dass sich kein Subnetz Ihres lokalen Netzwerks mit den virtuellen Netzwerken für die Verbindungsherstellung überschneidet. 
   * Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel zur [Schnellstartanleitung](../articles/virtual-network/quick-create-portal.md).

* Ihr virtuelles Netzwerk darf nicht über vorhandene Gateways für virtuelle Netzwerke verfügen. 

   * Wenn Ihr virtuelles Netzwerk bereits über Gateways verfügt (VPN oder ExpressRoute), müssen Sie alle Gateways entfernen, bevor Sie fortfahren können. 
   * Für diese Konfiguration ist es erforderlich, dass virtuelle Netzwerke nur mit dem Gateway des Virtual WAN-Hubs verbunden werden.

* Ein virtueller Hub ist ein virtuelles Netzwerk, das von Virtual WAN erstellt und genutzt wird. Es ist der Kern Ihres Virtual WAN-Netzwerks in einer Region. 

   * Beschaffen Sie sich einen IP-Adressbereich für Ihre Region des virtuellen Hubs. 
   * Der von Ihnen für den Hub angegebene Adressbereich darf sich nicht mit einem der vorhandenen virtuellen Netzwerke überlappen, mit denen Sie eine Verbindung herstellen. 
   * Der Adressbereich darf nicht mit den lokalen Adressbereichen überlappen, mit denen Sie eine Verbindung herstellen. 
   * Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, sollten Sie sich an eine Person wenden, die Ihnen diese Informationen zur Verfügung stellen kann.
