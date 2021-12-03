---
title: Erstellen eines Clientzertifikats mit Microsoft Azure Confidential Ledger
description: Erstellen eines Clientzertifikats mit Microsoft Azure Confidential Ledger
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 216a626bfaaf86a8c2237b5d871ab7895d921127
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476393"
---
# <a name="creating-a-client-certificate"></a>Erstellen eines Clientzertifikats

Die Azure Confidential Ledger-APIs erfordern eine clientzertifikatbasierte Authentifizierung. Nur die Zertifikate, die während der Ledgererstellung oder des Ledgerupdates einer Positivliste hinzugefügt wurden, können zum Aufrufen der funktionalen Confidential Ledger-APIs verwendet werden.

Sie benötigen ein Zertifikat im PEM-Format. Sie können mehr als ein Zertifikat erstellen und mithilfe der Ledgerupdate-API hinzufügen oder löschen.

## <a name="openssl"></a>OpenSSL

Es wird empfohlen, OpenSSL zum Generieren von Zertifikaten zu verwenden. Wenn Sie Git installiert haben, können Sie OpenSSL in der Git-Shell ausführen. Andernfalls können Sie OpenSSL für Ihr Betriebssystem installieren.

- **Windows**: Installieren Sie [Chocolatey für Windows](https://chocolatey.org/install), öffnen Sie ein PowerShell-Terminalfenster im Administratormodus, und führen Sie `choco install openssl` aus. Alternativ können Sie OpenSSL für Windows über [diesen Link](http://gnuwin32.sourceforge.net/packages/openssl.htm) installieren.
- **Linux**: Führen Sie `sudo apt-get install openssl` aus.

Sie können anschließend ein Zertifikat generieren, indem Sie `openssl` in einem Bash- oder PowerShell-Terminalfenster ausführen.

```bash
openssl ecparam -out "privkey_name.pem" -name "secp384r1" -genkey
openssl req -new -key "privkey_name.pem" -x509 -nodes -days 365 -out "cert.pem" -"sha384" -subj=/CN="ACL Client Cert"
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Confidential Ledger](overview.md)