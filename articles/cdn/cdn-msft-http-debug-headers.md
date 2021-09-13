---
title: Debuggen von HTTP-Headern für Azure CDN von Microsoft | Microsoft-Dokumentation
description: Debugcache-Anforderungsheaders stellen zusätzliche Informationen zur Cacherichtlinie bereit, die auf das angeforderte Objekt angewendet wird. Diese Header sind spezifisch für Azure CDN von Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 56f9b5b33600947920ff928eac53c0149afef682
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445079"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debuggen von HTTP-Headern für Azure CDN von Microsoft
Der Debugantwortheader (`X-Cache`) stellt Details zu der Ebene des CDN-Stapels bereit, aus der der Inhalt bereitgestellt wurde. Dieser Header ist spezifisch für Azure CDN von Microsoft.

### <a name="response-header-format"></a>Format des Antwortheaders

Header | BESCHREIBUNG
-------|------------
X-Cache: TCP_HIT | Dieser Header wird zurückgegeben, wenn der Inhalt aus dem CDN-Edgecache bereitgestellt wird. 
X-Cache: TCP_REMOTE_HIT | Dieser Header wird zurückgegeben, wenn der Inhalt aus dem regionalen CDN-Cache (Origin Shield-Ebene) bereitgestellt wird.
X-Cache: TCP_MISS | Dieser Header wird zurückgegeben, wenn ein Cachefehler vorliegt und der Inhalt vom Origin bereitgestellt wird.
X-Cache: PRIVATE_NOSTORE | Dieser Header wird zurückgegeben, wenn die Anforderung nicht zwischengespeichert werden kann, da der Cachesteuerelement-Antwortheader auf „private“ oder „no-store“ festgelegt ist.
X-Cache: CONFIG_NOCACHE | Dieser Header wird zurückgegeben, wenn die Anforderung so konfiguriert ist, dass sie nicht im CDN-Profil zwischengespeichert wird.
X-Cache: N/A | Dieser Header wird zurückgegeben, wenn die Anforderung von der signierten URL und dem Regelsatz abgelehnt wurde.

Weitere Informationen zu HTTP-Headern, die in Azure CDN unterstützt werden, finden Sie unter [Front Door zu Back-End](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend).
