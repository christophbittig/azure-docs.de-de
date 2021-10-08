---
title: Allgemeine Fehlercodes für Azure Key Vault | Microsoft-Dokumentation
description: Allgemeine Fehlercodes für Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: bd66619c65dd39e32a11b81096d4b763430fc16c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553677"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Allgemeine Fehlercodes für Azure Key Vault

Die in der folgenden Tabelle aufgelisteten Fehlercodes können von einem Vorgang in Azure Key Vault zurückgegeben werden.

| Fehlercode | Meldung für den Benutzer |
|--|--|
| VaultAlreadyExists |  Ihr neuer Schlüsseltresor konnte nicht mit dem angegebenen Namen erstellt werden, weil der Name bereits verwendet wird. Wenn Sie kürzlich einen Schlüsseltresor mit diesem Namen gelöscht haben, befindet er sich möglicherweise noch im vorläufig gelöschten Zustand. [Hier](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) können Sie überprüfen, ob der Tresor noch im vorläufig gelöschten Zustand vorhanden ist. |
| VaultNameNotValid |  Der Name des Tresors sollte eine Zeichenfolge mit 3 bis 24 Zeichen sein, die nur Zahlen (0–9), Buchstaben (a–z, A–Z) und Bindestriche (-) enthalten darf |
| AccessDenied |  Möglicherweise fehlen in der Zugriffsrichtlinie Berechtigungen, um diesen Vorgang durchzuführen. |
| ForbiddenByFirewall |  Die Clientadresse ist nicht autorisiert, und der Aufrufer ist kein vertrauenswürdiger Dienst. |
| ConflictError |  Sie fordern mehrere Vorgänge für dasselbe Element an.  |
| RegionNotSupported |  Die angegebene Azure-Region wird für diese Ressource nicht unterstützt. |
| SkuNotSupported |  Der angegebene SKU-Typ wird für diese Ressource nicht unterstützt. |
| ResourceNotFound |  Die angegebene Azure-Ressource wurde nicht gefunden. |
| ResourceGroupNotFound | Die angegebene Azure-Ressourcengruppe wurde nicht gefunden. |
| CertificateExpired |  Überprüfen Sie das Ablaufdatum und die Gültigkeitsdauer des Zertifikats. |


## <a name="next-steps"></a>Nächste Schritte

- [Entwicklerhandbuch zu Azure-Schlüsseltresor](developers-guide.md)
- Weitere Informationen zum [Authentifizieren bei Key Vault](authentication.md)
