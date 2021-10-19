---
title: Übersicht über die Azure Fluid Relay-Architektur
description: Übersicht über die Azure Fluid Relay-Architektur
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/overview/
ms.openlocfilehash: 6b58c90692a7d4eba404e4d5da84b175d229f343
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661774"
---
# <a name="overview-of-azure-fluid-relay-architecture"></a>Übersicht über die Azure Fluid Relay-Architektur

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Es gibt drei primäre Konzepte, die Sie beim Erstellen einer Anwendung mit Fluid verstehen müssen.

- Dienst
- Container
- Freigegebene Objekte

## <a name="service"></a>Dienst

Für Fluid-Clients ist ein zentralisierter Dienst erforderlich, den alle verbundenen Clients zum Senden und Empfangen von Vorgängen verwenden. Wenn Sie Fluid in einer Anwendung nutzen, müssen Sie das richtige Paket für den zugrunde liegenden Dienst verwenden, mit dem Sie eine Verbindung herstellen.

Für den Azure Fluid Relay-Dienst ist dieses Paket **@fluidframework/azure-client** . Dieses Paket hilft beim Erstellen und Laden von Fluid-Containern, die über Azure Fluid Relay in Azure gehostet werden.

## <a name="container"></a>Container

Der **Container** ist die primäre Kapselungseinheit in Fluid. Er besteht aus einer Sammlung freigegebener Objekten und unterstützender APIs, um den Lebenszyklus des Containers und der darin enthaltenen Objekte zu verwalten.

Das Erstellen neuer Container ist eine clientgesteuerte Aktion, und die Lebensdauer von Containern ist an die Daten gebunden, die auf dem unterstützenden Server gespeichert sind. Beim Abrufen vorhandener Container ist es wichtig, den vorherigen Zustand des Containers zu berücksichtigen.

Weitere Informationen zu Containern finden Sie unter [Containers](https://fluidframework.com/docs/build/containers/) (Container) auf fluidframework.com.

## <a name="shared-objects"></a>Freigegebene Objekte

Ein **freigegebenes Objekt** ist ein Objekt, das die Zusammenarbeit bei Daten unterstützt, indem eine bestimmte API verfügbar gemacht wird. Im Kontext eines Containers können viele freigegebene Objekte existieren, und sie können entweder statisch oder dynamisch erstellt werden. **Verteilte Datenstrukturen (Distributed Data Structures, DDSs)** und **DataObjects** sind Typen von freigegebenen Objekten.

Weitere Informationen finden Sie unter [Data modeling](https://fluidframework.com/docs/build/data-modeling/) (Datenmodellierung) auf fluidframework.com.

## <a name="package-structure"></a>Paketstruktur

Es gibt zwei primäre **Pakete**, die Sie beim Erstellen mit Fluid verwenden. Das Paket **fluid-framework** und ein dienstspezifisches Clientpaket wie **azure-client**.

Weitere Informationen finden Sie unter [Packages](https://fluidframework.com/docs/build/packages/) (Pakete) auf fluidframework.com.

### <a name="the-fluid-framework-package"></a>Das Paket „fluid-framework“

Das Paket **fluid-framework** ist eine Sammlung von Fluid-Kern-APIs, die das Erstellen und Verwenden von Anwendungen vereinfachen. Dieses Paket enthält alle allgemeinen Typdefinitionen sowie alle primitiven freigegebenen Objekte.

### <a name="the-fluidframeworkazure-client-package"></a>Das @fluidframework/azure-client-Paket

Das Paket **@fluidframework/azure-client** stellt eine API zum Herstellen einer Verbindung mit Azure Fluid Relay-Dienstinstanzen bereit, um Fluid-Container zu erstellen und zu laden. Weitere Informationen zur Verwendung dieser API finden Sie unter [Vorgehensweise: Herstellen einer Verbindung mit einem Azure Fluid Relay-Dienst](../how-tos/connect-fluid-azure-service.md).
