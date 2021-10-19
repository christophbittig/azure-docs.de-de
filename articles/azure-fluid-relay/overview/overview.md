---
title: Übersicht über Azure Fluid Relay
description: Übersicht über das Fluid Framework und Azure Fluid Relay.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 08/19/2021
ms.topic: how-to
ms.service: azure-fluid
ms.openlocfilehash: 30c0e289951555a5ffcd1d0776e0b273f8573da7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661823"
---
# <a name="azure-fluid-relay-overview"></a>Übersicht über Azure Fluid Relay

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Das [Fluid Framework](https://fluidframework.com/) ist ein plattformunabhängiges Open-Source-Framework. [Azure Fluid Relay](../overview/overview.md) ist ein verwaltetes Angebot für das Fluid Framework, mit dem Entwickler in Echtzeit Zusammenarbeitsumgebungen erstellen und den Zustand über verbundene JavaScript-Clients in Echtzeit replizieren können.

## <a name="what-is-the-fluid-framework"></a>Was ist das Fluid Framework?

Fluid Framework ist eine Sammlung von Clientbibliotheken für die Verteilung und Synchronisierung von freigegebenen Zuständen. Diese Bibliotheken ermöglichen es mehreren Clients, gleichzeitig gemeinsame Datenstrukturen zu erstellen und zu bearbeiten, indem sie ähnliche Codierungsmuster verwenden wie bei der Arbeit mit lokalen Daten.

Weitere Informationen finden Sie unter [FluidFramework.com](https://fluidframework.com).

## <a name="why-fluid"></a>Warum Fluid?

Da es schwierig ist, latenzarme Umgebungen für die Zusammenarbeit zu erstellen!

Fluid Framework bietet Folgendes:

- Clientorientiertes Anwendungsmodell mit Datenpersistenz, das keinen benutzerdefinierten Servercode erfordert.
- Verteilte Datenstrukturen mit vertrauten Programmiermustern.
- Sehr geringe Latenz.

Die Entwickler bei Microsoft haben die Zusammenarbeit in viele Anwendungen integriert, aber viele erfordern eine anwendungsspezifische serverseitige Logik, um die Zusammenarbeit zu bewältigen. Das Fluid Framework ist das Ergebnis der Investitionen von Microsoft in die Reduzierung der Komplexität bei der Erstellung von Anwendungen für die Zusammenarbeit.

Was wäre, wenn Sie überhaupt nicht in Servercode investieren müssten? Stellen Sie sich vor, Sie könnten einen Server für allgemeine Zwecke verwenden, der so konzipiert ist, dass er einfach und kostengünstig ist. Stellen Sie sich vor, Ihre gesamte Entwicklung würde sich auf die Clientumgebung konzentrieren und die Datensynchronisierung würde für Sie erledigt werden. Das ist das Versprechen von Fluid.

## <a name="focused-on-the-client-developer"></a>Ausgerichtet auf den Cliententwickler

Anwendungen, die mit dem Fluid Framework erstellt werden, benötigen keinen benutzerdefinierten Code auf dem Server, um anspruchsvolle Datensynchronisierungsszenarien wie die Echtzeiteingabe in Text-Editoren zu ermöglichen. Cliententwickler können sich auf Kundenerfahrungen konzentrieren, während Fluid die Synchronisierung von Daten übernimmt.

Das Fluid Framework funktioniert mit dem Anwendungsframework Ihrer Wahl. Unabhängig davon, ob Sie nun JavaScript oder ein Framework wie React, Angular oder Vue bevorzugen, gestaltet das Fluid Framework das Erstellen von Umgebungen für die Zusammenarbeit einfach und flexibel.

## <a name="how-fluid-works"></a>Funktionsweise von Fluid

Fluid wurde entwickelt, um Umgebungen für die Zusammenarbeit mit hervorragender Leistung zu ermöglichen. Um dieses Ziel zu erreichen, hat das Team die Serverlogik so einfach und schlicht wie möglich gehalten. Dieser Ansatz hat dazu beigetragen, eine praktisch sofortige Synchronisierung zwischen den Clients bei geringen Serverkosten sicherzustellen.

Um den Server einfach zu halten, ist jeder Fluid-Client für seinen eigenen Zustand verantwortlich. Während frühere Systeme eine Quelle der Wahrheit auf dem Server beibehielten, ist der Fluid-Dienst dafür verantwortlich, Datenvorgänge entgegenzunehmen, die Vorgänge in eine Reihenfolge zu bringen und die sequenzierten Vorgänge an die Clients zurückzugeben. Jeder Client ist in der Lage, diese Sequenz zu verwenden, um unabhängig von der Reihenfolge, in der er die Vorgänge erhält, den aktuellen Zustand zu erzeugen.

Die folgenden Schritte sind ein typischer Flow.

1. Clientcode ändert Daten lokal.
1. Die Fluid-Runtime sendet diese Änderung an den Fluid-Dienst.
1. Der Fluid-Dienst sequenziert diesen Vorgang und überträgt ihn an alle Clients.
1. Die Fluid-Runtime integriert diesen Vorgang in lokale Daten und löst ein valueChanged-Ereignis aus.
1. Der Clientcode verarbeitet dieses Ereignis (aktualisiert die Ansicht, führt die Geschäftslogik aus).

## <a name="getting-to-version-10"></a>Auf dem Weg zur Version 1.0

Die Kerntechnologie für das Fluid Framework ist ausgereift und stabil. Die Ebenen, die auf dieser Grundlage erstellt werden, befinden sich jedoch noch in der Entwicklung. In den kommenden Monaten werden wir die APIs weiterentwickeln, neue Features hinzufügen und daran arbeiten, die Verwendung des Frameworks weiter zu vereinfachen. Diese Änderungen werden durch Microsofts interne Verwendung von Fluid und durch die Anforderungen, die wir von Entwicklern erhalten, die derzeit Fluid erstellen, vorangetrieben.

Das Fluid Framework ist noch nicht für Produktionslösungen bereit. Aber wir freuen uns, es jetzt als Open Source zu veröffentlichen, um Entwicklern die Möglichkeit zu geben, es zu erkunden, kennenzulernen und dazu beizutragen, sowohl durch Feedback als auch durch direkte Beteiligung.
