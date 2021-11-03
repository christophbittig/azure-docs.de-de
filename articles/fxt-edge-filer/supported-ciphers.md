---
title: Unterstützte Verschlüsselungsverfahren für Azure FXT Edge Filer
description: Liste der Verschlüsselungsstandards, die von FXT Edge Filer-Clustern verwendet werden.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 0c05e821ab19c4a617a8a6e13bd7f1c37331a661
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032998"
---
# <a name="supported-encryption-standards-for-azure-fxt-edge-filer"></a>Unterstützte Verschlüsselungsstandards für Azure FXT Edge Filer

In diesem Dokument werden die Verschlüsselungsstandards beschrieben, die für Azure FXT Edge Filer benötigt werden. Diese Standards sind ab Version 5.1.1.2 des Betriebssystems implementiert.

Diese Standards gelten für [Avere vFXT for Azure](../avere-vfxt/index.yml) und Azure FXT Edge Filer.

Jedes Verwaltungs- oder Infrastruktursystem, das eine Verbindung mit dem Azure FXT Edge Filer-Cache oder mit einzelnen Knoten herstellt, muss diese Standards erfüllen.

(Clientcomputer binden den Cache über NFS ein, sodass diese Verschlüsselungsanforderungen nicht gelten. Verwenden Sie andere angemessene Maßnahmen, um deren Sicherheit zu gewährleisten.)

## <a name="tls-standard"></a>TLS-Standard

* TLS 1.2 muss aktiviert sein.
* SSL V2 und V3 müssen deaktiviert sein.

TLS 1.0 und TLS 1.1 können verwendet werden, wenn dies aus Gründen der Abwärtskompatibilität mit privaten Objektspeichern unbedingt erforderlich ist. Es ist jedoch besser, Ihren privaten Speicher auf moderne Sicherheitsstandards upzugraden. Wenden Sie sich wegen weiterer Informationen an den Microsoft-Kundendienst und -Support.

## <a name="permitted-cipher-suites"></a>Zulässige Verschlüsselungssammlungen

Mit Azure FXT Edge Filer können die folgenden TLS-Verschlüsselungssammlungen ausgehandelt werden:

* ECDHE-ECDSA-AES128-GCM-SHA256
* ECDHE-ECDSA-AES256-GCM-SHA384
* ECDHE-RSA-AES128-GCM-SHA256
* ECDHE-RSA-AES256-GCM-SHA384
* ECDHE-ECDSA-AES128-SHA256
* ECDHE-ECDSA-AES256-SHA384
* ECDHE-RSA-AES128-SHA256
* ECDHE-RSA-AES256-SHA384

Die HTTPS-Schnittstelle für die Clusterverwaltung (wird für die Web-GUI der Systemsteuerung und administrative RPC-Verbindungen verwendet) unterstützt nur die oben genannten Verschlüsselungssammlungen und TLS 1.2. Beim Herstellen einer Verbindung mit der Verwaltungsschnittstelle werden keine anderen Protokolle oder Verschlüsselungssammlungen unterstützt.

## <a name="ssh-server-access"></a>SSH-Serverzugriff

Diese Standards gelten für den SSH-Server, der in diese Produkte eingebettet ist.

Der SSH-Server lässt keine Remoteanmeldung als Superuser „root“ zu. Wenn SSH-Remotezugriff unter der Anleitung des Microsoft-Kundendiensts und -Supports erforderlich ist, melden Sie sich als SSH-Administratorbenutzer an, der über eine eingeschränkte Shell verfügt.

Die folgenden SSH-Verschlüsselungssammlungen sind auf dem SSH-Clusterserver verfügbar. Stellen Sie sicher, dass jeder Client, der SSH zum Herstellen einer Verbindung mit dem Cluster verwendet, über aktuelle Software verfügt, die diese Standards erfüllt.

### <a name="ssh-encryption-standards"></a>SSH-Verschlüsselungsstandards

| Typ | Unterstützte Werte |
|--|--|
| Ciphers | aes256-gcm@openssh.com</br> aes128-gcm@openssh.com</br> aes256-ctr</br> aes128-ctr |
| MACs | hmac-sha2-512-etm@openssh.com</br> hmac-sha2-256-etm@openssh.com</br> hmac-sha2-512</br> hmac-sha2-256 |
| KEX-Algorithmen | ecdh-sha2-nistp521</br> ecdh-sha2-nistp384</br> ecdh-sha2-nistp256</br> diffie-hellman-group-exchange-sha256 |

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie dem Azure FXT Edge Filer-Cluster [Speicher hinzufügen](add-storage.md).
* [Stellen Sie eine Verbindung mit der Systemsteuerung her](cluster-create.md#open-the-settings-pages), um den Cluster zu verwalten.
* [Binden Sie Clients ein](mount-clients.md), um auf Daten aus dem Cluster zuzugreifen.
* [Wenden Sie sich an den Support](support-ticket.md), um mehr über Verschlüsselungsstandards zu erfahren.
