---
title: Authentifizieren von Azure Confidential Ledger-Knoten
description: Authentifizieren von Azure Confidential Ledger-Knoten
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 20e5c46ea174807efea21cf4812eef47ee8c0257
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450094"
---
# <a name="authenticating-azure-confidential-ledger-nodes"></a>Authentifizieren von Azure Confidential Ledger-Knoten

Azure Confidential Ledger-Knoten können durch Codebeispiele und durch Benutzer authentifiziert werden.

## <a name="code-samples"></a>Codebeispiele

Bei der Initialisierung rufen Codebeispiele das Knotenzertifikat durch Abfragen des Identitätsdiensts ab. Nach dem Abrufen des Knotenzertifikats fragt ein Codebeispiel das Ledgernetzwerk ab, um eine Quote zu erhalten, die dann mithilfe der Binärdateien für die Host-Überprüfung überprüft wird. Wenn die Überprüfung erfolgreich ist, fährt das Codebeispiel mit Ledgeroperationen fort.

## <a name="users"></a>Benutzer

Benutzer können die Authentizität von Azure Confidential Ledger-Knoten überprüfen und bestätigen, dass sie tatsächlich mit der Enklave ihres Ledgers zusammenarbeiten. Sie können auf verschiedene Weise Vertrauensstellungen in Azure Confidential Ledger-Knoten aufbauen, die aufeinander gestapelt werden können, um das allgemeine Vertrauensniveau zu erhöhen. Daher tragen die Schritte 1 bis 2 dazu bei, im Rahmen des anfänglichen TLS-Handshakes und der Authentifizierung in funktionalen Workflows Vertrauen in diese Azure Confidential Ledger-Enklave zu schaffen. Darüber hinaus wird eine permanente Clientverbindung zwischen dem Client des Benutzers und dem Confidential Ledger aufrechterhalten.

- **Überprüfen des Confidential Ledger-Knotens:** Dies erfolgt durch Abfragen des von Microsoft gehosteten Identitätsdiensts, der ein Netzwerkzertifikat bereitstellt und somit dabei hilft, zu überprüfen, ob der Ledger-Knoten ein vom Netzwerkzertifikat unterstütztes/signiertes Zertifikat für diese spezielle Instanz präsentiert. Ähnlich wie PKI-basiertes HTTPS wird das Zertifikat eines Servers von einer bekannten Zertifizierungsstelle (ZS) oder Zwischenzertifizierungsstelle signiert. Im Fall von Azure Confidential Ledger wird das Zertifizierungsstellenzertifikat von einem Identitätsdienst in Form eines Netzwerkzertifikats zurückgegeben. Dies ist eine wichtige vertrauensbildende Maßnahme für Benutzer von Confidential Ledger. Wenn dieses Knotenzertifikat nicht vom zurückgegebenen Netzwerkzertifikat signiert ist, sollte die Clientverbindung fehlschlagen (wie im Beispielcode implementiert).
- **Überprüfen der Confidential Ledger-Enklave**: Ein Confidential Ledger wird in einer Intel® SGX-Enklave ausgeführt, die durch eine Quote dargestellt wird, ein Datenblob, das in dieser Enklave generiert wird. Sie kann von jeder anderen Entität verwendet werden, um zu überprüfen, ob die Quote von einer Anwendung erstellt wurde, die mit Intel® SGX-Schutz ausgeführt wird. Die Quote ist so strukturiert, dass eine einfache Überprüfung möglich ist. Sie enthält Ansprüche, mit denen verschiedene Eigenschaften der Enklave und der ausgeführten Anwendung identifiziert werden können. Dies ist ein wichtiger vertrauensbildender Mechanismus für Benutzer des Confidential Ledger. Dies kann durch Aufrufen einer API eines funktionalen Workflows erreicht werden, um eine Enklave-Quote abzurufen. Die Clientverbindung sollte fehlschlagen, wenn die Quote ungültig ist. Die abgerufene Quote kann dann mit dem Tool „open_enclaves Host_Verify“ überprüft werden. Weitere Informationen dazu finden Sie hier.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Confidential Ledger](overview.md)
- [Azure Confidential Ledger-Architektur](architecture.md)
