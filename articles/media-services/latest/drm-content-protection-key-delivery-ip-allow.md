---
title: Einschränken des Zugriffs auf die DRM-Lizenz- und AES-Schlüsselübermittlung mithilfe von Listen zugelassener IP-Adressen
description: Hier erfahren Sie, wie Sie den Zugriff auf DRM-Lizenzen und AES-Schlüssel mithilfe von Listen zugelassener IP-Adressen einschränken.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cc0a35c196956d1f6afaf3d3fb27b9f428bb9f6d
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204808"
---
# <a name="restrict-access-to-drm-license-and-aes-key-delivery-using-ip-allowlists"></a>Einschränken des Zugriffs auf die DRM-Lizenz- und AES-Schlüsselübermittlung mithilfe von Listen zugelassener IP-Adressen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Beim Sichern von Medien mit dem [Inhaltsschutz](./drm-content-protection-concept.md) und den DRM-Features (Digital Rights Management, Verwaltung digitaler Rechte) von Azure Media Services kann es vorkommen, dass Sie die Übermittlung von Lizenzen oder Schlüsselanforderungen auf einen bestimmten IP-Adressbereich von Clientgeräten in Ihrem Netzwerk beschränken müssen. Um die Wiedergabe und Übermittlung von Schlüsseln zu beschränken, können Sie die Liste zugelassener IP-Adressen für die Schlüsselübermittlung verwenden.

Sie können die Liste zugelassener IP-Adressen auch verwenden, um den öffentlichen Internetzugriff auf Datenverkehr zur Schlüsselübermittlung vollständig zu blockieren und nur Datenverkehr von Ihren privaten Netzwerkendpunkten zuzulassen.

Die Liste zugelassener IP-Adressen für die Schlüsselübermittlung beschränkt die Übermittlung von DRM-Lizenzen und AES-128-Schlüsseln auf Clients im angegebenen IP-Adressbereich in der Liste.

## <a name="setting-the-allowlist-for-key-delivery"></a>Festlegen der Liste zugelassener IP-Adressen für die Schlüsselübermittlung

Die Einstellungen für die Liste zugelassener IP-Adressen für die Schlüsselübermittlung befinden sich in der Media Services-Kontoressource. Beim Erstellen eines neuen Media Services-Kontos können Sie die zulässigen IP-Adressbereiche über die **KeyDelivery**-Eigenschaft der [Media Services-Kontoressource](/rest/api/media/mediaservices/create-or-update) einschränken.

Die **defaultAction**-Eigenschaft kann auf „Allow“ oder „Deny“ festgelegt werden, um die Übermittlung von Lizenzen und Schlüsseln an Clients im IP-Adressbereich der Liste zugelassener IP-Adressen zu steuern.

Die **ipAllowList**-Eigenschaft ist ein Array von einzelnen IPv4-Adressen und/oder IPv4-Adressbereichen in der [CIDR-Notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation).

## <a name="setting-the-allowlist-in-the-portal"></a>Festlegen der Liste zugelassener IP-Adressen im Portal

Sie können die Liste zugelassener IP-Adressen für die Schlüsselübermittlung im Azure-Portal konfigurieren und aktualisieren.  Navigieren Sie zu Ihrem Media Services-Konto, und wählen Sie unter **Einstellungen** das Menü **Schlüsselübermittlung** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Kontos](./account-create-how-to.md)
- [Übersicht: Digital Rights Management (DRM) und Inhaltsschutz](./drm-content-protection-concept.md)
