---
title: Azure Confidential Ledger-Architektur
description: Azure Confidential Ledger-Architektur
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 855ad26422eb91c5c971d4525d419e5cee8b6606
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476412"
---
# <a name="architecture"></a>Architektur

Azure Confidential Ledger, ein REST-API-Dienst, ermöglicht Benutzern die Interaktion mit dem Ledger über administrative und funktionale API-Aufrufe.  Wenn Daten im Ledger aufgezeichnet werden, werden sie an die zulässigen Blockchainknoten gesendet, bei denen es sich um mit Secure Enclave gesicherte Replikate handelt. Die Replikate folgen einem Konsenskonzept. Ein Benutzer kann auch Belege für die Daten abrufen, für die ein Commit für den Ledger ausgeführt wurde.

Es gibt auch ein optionales Konsortium, das die Zusammenarbeit mehrerer Parteien in Zukunft unterstützen wird.

## <a name="architecture-diagram"></a>Architekturdiagramm

Dieses Bild stellt eine Architekturübersicht über Azure Confidential Ledger dar und zeigt Azure Confidential Ledger-Benutzer, die mit den Cloud-APIs für einen erstellten Ledger interagieren.

:::image type="content" source="./media/architecture-overview.png" alt-text="Übersicht über die Architektur":::

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Confidential Ledger](overview.md)
- [Authentifizieren von Azure Confidential Ledger-Knoten](authenticate-ledger-nodes.md)
