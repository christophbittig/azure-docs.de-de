---
title: Häufig gestellte Fragen zu Azure Confidential Ledger
description: Häufig gestellte Fragen zu Azure Confidential Ledger
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 2c5d6aab297fb357d94066a05dd7f6ff766b31df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458112"
---
# <a name="frequently-asked-questions-for-azure-confidential-ledger"></a>Häufig gestellte Fragen zu Azure Confidential Ledger

## <a name="how-can-i-tell-if-the-acc-ledger-service-would-be-useful-to-my-organization"></a>Wie kann ich feststellen, ob der ACC Ledger-Dienst für meine Organisation nützlich wäre?

Azure Confidential Ledger eignet sich ideal für Organisationen mit Datensätzen, die für einen motivierten Angreifer so wertvoll sind, dass er versucht, das zugrunde liegende Protokollierungs-/Speichersystem zu kompromittieren. Dazu zählen auch Insiderszenarien, bei denen ein nicht autorisierter Mitarbeiter versuchen könnte, bestehende Datensätze zu fälschen, zu ändern oder zu entfernen.

## <a name="what-makes-acc-ledger-much-more-secure"></a>Was macht ACC Ledger wesentlich sicherer?

Wie der Name schon sagt, verwendet der Ledger die [Azure Confidential Computing-Plattform](../confidential-computing/index.yml). Ein Ledger erstreckt sich über drei oder mehr identische Instanzen, von denen jede in einer dedizierten, vollständig bestätigten hardwaregestützten Enklave ausgeführt wird. Die Integrität des Ledgers wird durch eine konsensbasierte Blockchain aufrechterhalten.

## <a name="when-writing-to-the-acc-ledger-do-i-need-to-store-write-receipts"></a>Muss ich beim Schreiben in den ACC Ledger Schreibbelege speichern?

Nicht unbedingt. Bei einigen Lösungen müssen Benutzer aktuell Schreibbelege für die zukünftige Protokollvalidierung verwalten. Dazu müssen Benutzer diese Belege in einem sicheren Speicher verwalten, was zusätzlichen Aufwand bedeutet. Der Ledger beseitigt dieses Problem durch seinen Ansatz auf Basis einer Merkle-Struktur, bei dem Schreibbelege einen vollständigen Strukturpfad zu einem signierten Vertrauensanker enthalten. Benutzer können Transaktionen überprüfen, ohne Ledgerdaten speichern oder verwalten zu müssen.

## <a name="how-do-i-verify-ledgers-authenticity"></a>Wie überprüfe ich die Authentizität des Ledgers?

Sie können überprüfen, ob die Ledger-Serverknoten, mit denen Ihr Client kommuniziert, authentisch sind. Weitere Informationen finden Sie unter [Authentifizieren von Confidential Ledger-Knoten.](authenticate-ledger-nodes.md)



## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Confidential Ledger](overview.md)