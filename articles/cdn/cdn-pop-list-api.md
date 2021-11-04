---
title: Abrufen der aktuellen POP-IP-Liste für Azure CDN | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe der Rest-API POP-Server abrufen können. POP-Server nehmen Anforderungen an Ursprungsserver vor, die Azure Content Delivery Network-Endpunkten zugeordnet sind.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 2a471b16460ca92c43dda1c916840a56cb2a3808
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470070"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Abrufen der aktuellen POP-IP-Liste für Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Abrufen der aktuellen POP-IP-Liste von Verizon für Azure CDN

Mit der REST-API können Sie den Satz von IP-Adressen für Point of Presence (POP)-Server von Verizon abrufen. Diese POP-Server führen Anforderung an Ursprungsserver durch, die Azure Content Delivery Network (CDN)-Endpunkten in einem Verizon-Profil (**Azure CDN Standard von Verizon** oder **Azure CDN Premium von Verizon**) zugeordnet sind. Beachten Sie, dass sich dieser Satz von IP-Adressen von den IP-Adressen unterscheidet, die dem Client bei Anforderungen an die POPs angezeigt werden würden. 

Informationen zur Syntax des REST-API-Vorgangs zum Abrufen der POP-Liste finden Sie unter [Edgeknoten – Liste](/rest/api/cdn/edge-nodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Abrufen der aktuellen POP-IP-Liste von Microsoft für Azure CDN

Wenn Sie Ihre Anwendung so sperren möchten, dass nur Datenverkehr aus Azure CDN von Microsoft akzeptiert wird, müssen Sie IP-ACLs für Ihr Back-End einrichten. Sie können auch den Satz akzeptierter Werte für den Header „X-Forwarded-Host“ einschränken, der von Azure CDN von Microsoft gesendet wird. Diese Schritte werden wie folgt beschrieben:

Konfigurieren Sie IP-ACLs für Ihre Back-Ends so, dass sie nur Datenverkehr aus dem Back-End-IP-Adressraum von Azure CDN von Microsoft und den Infrastrukturdiensten von Azure akzeptieren. 

Verwenden Sie [Diensttags](../virtual-network/service-tags-overview.md) von Azure Front Door mit Azure CDN von Microsoft, um die Back-End-IP-Adressbereiche von Microsoft zu konfigurieren. Eine vollständige Liste finden Sie unter [Azure-IP-Adressbereiche und -Diensttags – öffentliche Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519) für Microsoft-Dienste.

## <a name="typical-use-case"></a>Typischer Anwendungsfall

Aus Sicherheitsgründen können Sie diese IP-Liste verwenden, um zu erzwingen, dass Anfragen an Ihren Ursprungsserver nur von einem gültigen Verizon-POP gestellt werden. Wenn ein Benutzer zum Beispiel den Hostnamen oder die IP-Adresse für den Ursprungsserver eines CDN-Endpunkts ermittelt hat, können Anfragen direkt an den Ursprungsserver gerichtet und so die Skalierungs- und Sicherheitsfunktionen von Azure CDN umgangen werden. Dieses Szenario kann verhindert werden, indem Sie die IPs in der zurückgegebenen Liste als die einzigen erlaubten IPs auf einem Ursprungsserver festlegen. Um sicherzustellen, dass Sie mit der aktuellen POP-Liste arbeiten, rufen Sie diese mindestens einmal täglich ab. 

## <a name="next-steps"></a>Nächste Schritte

Informationen zur REST-API finden Sie unter [Azure CDN-REST-APIs](/rest/api/cdn/).
