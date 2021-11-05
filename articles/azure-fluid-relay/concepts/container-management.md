---
title: Verwalten von Fluid-Containern
description: Übersicht über das Verwalten von Containern im Azure Fluid Relay-Dienst
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 38999f706cdec3b27f41b9408c1ffcf5c689c41b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039480"
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

## <a name="example-container-creation-flow"></a>Beispielflow für die Containererstellung

:::image type="content" source="../images/container-creation-flow.jpg" lightbox="../images/container-creation-flow-lightbox.jpg" alt-text="Ein Diagramm, das die Datenflüsse für den Containererstellungsprozess beschreibt":::

In diesem Beispiel wird die App/Seite mit einem generischen JWT geladen (nicht an einen bestimmten Container gebunden), den die Client-App zum Zeitpunkt der Erstellung eines neuen Containers verwendet.

Die clientseitige App verwendet die Fluid Framework-API, um einen neuen Container im Azure Fluid Relay-Dienst zu erstellen, was zu einem Containerobjekt mit einer neu zugewiesenen Container-ID führt. Weitere Interaktionen mit dem Container erfordern ein neues JWT, das die Container-ID enthält.

Nachdem der Client den neuen Container erstellt hat, speichert er die Container-ID in einem System, das Containern und Benutzern die Verwaltung von Berechtigungen zuordnet. Dieses System steuert alle Containerermittlungs-/Browserumgebungen, die Entwickler*innen für ihre Benutzer*innen erstellen möchten.

Vor der Interaktion mit dem Container fordert der Client ein containerspezifisches JWT an, das für nachfolgende Aufrufe von der Fluid Framework-Runtime an den Azure Fluid Relay-Dienst verwendet wird. 

## <a name="exporting-container-content"></a>Exportieren von Containerinhalten

Wenn eine Anwendung Daten speichert, die möglicherweise von Endbenutzer*innen exportiert werden müssen, sind Anwendungsentwickler*innen dafür verantwortlich, diese Exportfunktionalität in die Anwendung zu integrieren. Dabei wird der aktuelle Status des Fluidcontainers verwendet, wie er durch die im Container definierten verteilten Datenstrukturen dargestellt wird. Weitere Informationen zum Herstellen einer Verbindung mit und zum Öffnen von Fluid-Containern finden Sie unter [Container (fluidframework.com)](https://fluidframework.com/docs/build/containers/). Weitere Informationen zum Auflisten und Löschen von Containern mit der Steuerungsebenen-API finden Sie unter [Löschen von Fluidcontainern in Microsoft Azure Fluid Relay Server](../how-tos/container-deletion.md).

## <a name="see-also"></a>Siehe auch

- [Übersicht über die Azure Fluid Relay-Architektur](architecture.md)
- [Verteilte Datenstrukturen](data-structures.md)
- [Azure Fluid Relay-Tokenvertrag](../how-tos/fluid-json-web-token.md)
