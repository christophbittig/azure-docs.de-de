---
title: Verwalten von Fluid-Containern
description: Übersicht über das Verwalten von Containern im Azure Fluid Relay-Dienst
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 934122f4cb952a4be915c55c2555e336705a71c2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710391"
---
# <a name="managing-fluid-containers"></a>Verwalten von Fluid-Containern

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Ein Container ist die unteilbare Speichereinheit im Azure Fluid Relay-Dienst und stellt die aus einer Fluid-Sitzung gespeicherten Daten dar, einschließlich Vorgängen und Momentaufnahmen. Die Fluid-Runtime verwendet den Container, um den Zustand einer Fluid-Sitzung zu aktivieren, wenn ein Benutzer zum ersten Mal beitritt oder nach dem Verlassen erneut beitritt.

Beim Erstellen einer Anwendung mit dem Fluid Framework müssen Sie bei der Containererstellung und -verwaltung verschiedene Faktoren berücksichtigen, wie in diesem Diagramm zusammengefasst.

:::image type="content" source="../images/fluid-service-architecture-ownership.jpg" alt-text="Abbildung: Architektur eines Fluid-Diensts und Informationen zu den Teilen, die sich im Besitz von Entwicklern oder von Microsoft befinden":::

## <a name="key-concepts"></a>Wichtige Begriffe

### <a name="container-permissions"></a>Containerberechtigungen 

In den meisten Fällen möchten Entwickler einen Bestand an Containern und Containerberechtigungen verwalten. Dies umfasst Informationen darüber, wer Zugriff auf die Container hat, sowie Metadaten wie den Anzeigenamen des Containers.

### <a name="accessing-containers"></a>Zugreifen auf Container

Container werden über die Container-ID referenziert. Bevor ein Benutzer einen Container erstellen oder öffnen kann, muss er ein JWT anfordern, das von der Fluid-Runtime bei der Kommunikation mit dem Azure Fluid Relay-Dienst verwendet wird. Jeder Prozess mit einem gültigen JWT kann auf einen Container zugreifen. Es liegt in der Verantwortung des Entwicklers, JWTs für den Containerzugriff zu generieren, wodurch er die Kontrolle über die Geschäftslogik hat, um den Zugriff je nach Szenario zu steuern. Der Azure Fluid Relay-Dienst hat keine Kenntnis darüber, welche Benutzer Zugriff auf einen Container haben sollen. Weitere Informationen zu diesem Thema finden Sie unter [Azure Fluid Relay-Tokenvertrag](../how-tos/fluid-json-web-token.md).

> [!NOTE]
> Das JWT-Feld **documentID** entspricht der Fluid-Container-ID.

### <a name="container-naming"></a>Containerbenennung

Container werden zum Zeitpunkt der Containererstellung vom Azure Fluid Relay-Dienst benannt. Die Aktion „Erstellen“ gibt einen Containernamen in Form einer GUID zurück, die später zum Öffnen des Containers verwendet werden muss. In den meisten Fällen möchten Entwickler diese Container-ID-GUID zusammen mit einem Anzeigenamen in ihrem eigenen Datenspeicher speichern, um Containerermittlungsabläufe zu vereinfachen. 

### <a name="container-discovery"></a>Containerermittlung

Entwickler sind für alle Erfahrungen und Geschäftslogiken im Zusammenhang mit der Benutzerermittlung von vorhandenen Containern verantwortlich. Dies könnte in Form einer durchsuchbaren Liste von Containern auf der Grundlage der Teilnahme des Benutzers an der Fluid-Sitzung, der direkten gemeinsamen Nutzung von Containern durch andere Benutzer oder der programmgesteuerten Zuweisung von Containern zu bestehenden Artefakten oder Prozessen erfolgen.

## <a name="see-also"></a>Siehe auch

- [Übersicht über die Azure Fluid Relay-Architektur](architecture.md)
- [Verteilte Datenstrukturen](data-structures.md)
- [Azure Fluid Relay-Tokenvertrag](../how-tos/fluid-json-web-token.md)
