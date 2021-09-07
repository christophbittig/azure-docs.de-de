---
title: Datei einfügen
description: Datei einfügen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b77128033cbe0f99023dbee8a5a15e06820399b1
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255014"
---
>[!NOTE]
>Ab dem 1. Juli 2018 wird die Unterstützung für TLS 1.0 und 1.1 vom Azure-VPN-Gateway entfernt. Das VPN-Gateway unterstützt dann nur noch TLS 1.2. Informationen zum Beibehalten der Unterstützung finden Sie unter [Updates zum Ermöglichen der Unterstützung von TLS 1.2](#tls1).

Die folgenden älteren Algorithmen werden ebenfalls am 1. Juli 2018 für TLS als veraltet markiert:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-81"></a><a name="tls1"></a>Wie aktiviere ich Unterstützung für TLS 1.2 unter Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
