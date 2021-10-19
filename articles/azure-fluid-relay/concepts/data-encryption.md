---
title: Datenverschlüsselung in Azure Fluid Relay
description: Besseres Verständnis der Datenverschlüsselung in Fluid Relay Server
author: hickeys
ms.author: hickeys
ms.date: 10/08/2021
ms.service: app-service
ms.topic: reference
ms.openlocfilehash: 3af5ba0580b0cb662c9ab87c73afac965a8c066f
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730596"
---
# <a name="data-encryption-in-azure-fluid-relay"></a>Datenverschlüsselung in Azure Fluid Relay

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Microsoft Azure Fluid Relay Server nutzt die Möglichkeit zur Verschlüsselung von ruhenden Daten von [Azure Kubernetes](/azure/aks/enable-host-encryption), [Microsoft Azure Cosmos DB]/azure/cosmos-db/database-encryption-at-rest) und [Azure Blob Storage](/azure/storage/common/storage-service-encryption). Die Dienst-zu-Dienst-Kommunikation zwischen AFRS und diesen Ressourcen ist TLS-verschlüsselt und wird von der Azure Virtual Network Boundary umschlossen, wodurch sie durch Netzwerksicherheitsregeln vor externen Störungen geschützt ist.

Das folgende Diagramm zeigt auf hoher Ebene, wie Azure Fluid Relay Server implementiert ist und wie es die Datenspeicherung handhabt.

:::image type="content" source="../images/data-encryption.png" alt-text="Diagramm: Datenspeicherung in Azure Fluid Relay":::

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-much-more-does-azure-fluid-relay-server-cost-if-encryption-is-enabled"></a>Wie viel mehr kostet Azure Fluid Relay Server, wenn die Verschlüsselung aktiviert ist?

Die Verschlüsselung ruhender Daten ist standardmäßig aktiviert. Es fallen keine zusätzlichen Kosten an.

### <a name="who-manages-the-encryption-keys"></a>Wer verwaltet die Verschlüsselungsschlüssel?

Die Schlüssel werden von Microsoft verwaltet.

### <a name="how-often-are-encryption-keys-rotated"></a>Wie oft werden die Verschlüsselungsschlüssel ausgetauscht?

Microsoft verfügt über eine Reihe von internen Richtlinien für die Rotation der Verschlüsselungsschlüssel, die auch für Azure Fluid Relay Server gelten. Die spezifischen Richtlinien werden nicht veröffentlicht. Microsoft veröffentlicht den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), der als eine Teilmenge der internen Richtlinien gilt und nützliche bewährte Methoden für Entwickler enthält.

### <a name="can-i-use-my-own-encryption-keys"></a>Kann ich meine eigenen Verschlüsselungsschlüssel verwenden?

Nein, dieses Feature ist noch nicht verfügbar. Halten Sie die Augen offen für weitere Aktualisierungen in diesem Bereich. 

### <a name="what-regions-have-encryption-turned-on"></a>Für welche Regionen ist die Verschlüsselung aktiviert?

Die Verschlüsselung ist für alle Azure Fluid Relay Server-Regionen und für alle Benutzerdaten aktiviert.

### <a name="does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Wirkt sich die Verschlüsselung auf die SLAs hinsichtlich Leistung, Wartezeit und Durchsatz aus?

A: Bei aktivierter Verschlüsselung ruhender Daten gibt es keine Auswirkungen oder Änderungen an den Leistungs-SLAs.

## <a name="see-also"></a>Siehe auch

- [Übersicht über die Azure Fluid Relay-Architektur](architecture.md)
- [Azure Fluid Relay-Tokenvertrag](../how-tos/fluid-json-web-token.md)
- [Authentifizierung und Autorisierung in Ihrer App](authentication-authorization.md)
