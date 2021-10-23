---
title: Datei einfügen
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fdfb53ce569af92939e9978699de913c3fd1c17d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050592"
---
Beachten Sie bei der Arbeit mit benutzerdefinierten IPsec-Richtlinien die folgenden Anforderungen:

* **IKE**: Für IKE können Sie einen beliebigen Parameter aus der IKE-Verschlüsselung, einen beliebigen Parameter aus der IKE-Integrität sowie einen beliebigen Parameter aus der DH-Gruppe auswählen.
* **IPsec**: Für IPsec können Sie einen beliebigen Parameter aus der IPsec-Verschlüsselung, einen beliebigen Parameter aus der IPsec-Integrität sowie PFS auswählen. Falls einer der Parameter für die IPsec-Verschlüsselung oder die IPsec-Integrität GCM ist, müssen die Parameter für beide Einstellungen GCM sein.

>[!NOTE]
> Bei benutzerdefinierten IPsec-Richtlinien besteht kein Konzept von Antwortdienst und Initiator (im Gegensatz zu IPsec-Standardrichtlinien). Beide Seiten (lokale und Azure-VPN-Gateways) verwenden die gleichen Einstellungen für IKE Phase 1 und IKE Phase 2. Es werden beide Protokolle – IKEv1 und IKEv2 – unterstützt.
>

**Verfügbare Einstellungen und Parameter**

| Einstellung | Parameter |
|--- |--- |
| IKE-Verschlüsselung | GCMAES256, GCMAES128, AES256, AES128 |
| IKE-Integrität | SHA384, SHA256 |
| DH-Gruppe | ECP384, ECP256, DHGroup24, DHGroup14 |
| IPsec-Verschlüsselung | GCMAES256, GCMAES128, AES256, AES128, Keine |
| IPsec-Integrität | GCMAES256, GCMAES128, SHA256 |
| PFS-Gruppe | ECP384, ECP256, PFS24, PFS14, Keine |
| SA-Gültigkeitsdauer |Integer, min. 300/Standard: 3600 Sekunden |
