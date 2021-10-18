---
title: Versionskompatibilität mit Fluid Framework-Versionen
description: >
  Wie Sie feststellen können, welche Versionen der Fluid Framework-Versionen mit dem Azure Fluid Relay-Dienst kompatibel sind.
services: azure-fluid
author: tylerbutler
ms.author: tylerbu
ms.date: 09/28/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 62493eeb270a171fe874a877dfd51ac404eaab27
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661821"
---
# <a name="version-compatibility-with-fluid-framework-releases"></a>Versionskompatibilität mit Fluid Framework-Versionen

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Um Ihre Anwendung mit dem Azure Fluid Relay Service zu verbinden, verwenden Sie die **@fluidframework/azure-client** Bibliothek. Sie werden auch die **fluid-framework** Bibliothek verwenden, um die Kerndatenstrukturen zu nutzen, die vom Fluid Framework bereitgestellt werden.

Da Sie Azure Fluid Relay verwenden, sollten Sie zunächst die neueste verfügbare Version von @fluidframework/azure-client installieren und diese Version verwenden, um die zu verwendende Version der Fluid-Framework-Bibliothek zu bestimmen. Die Bibliothek drückt eine *Peer-Abhängigkeit* von der Version des Fluid-Framework-Pakets aus, von der sie abhängt.

Sie können das Werkzeug [install-peerdeps](https://www.npmjs.com/package/install-peerdeps) verwenden, um sowohl @fluidframework/azure-client als auch die kompatible Version von fluid-framework mit dem folgenden Befehl zu installieren:

```bash
npx install-peerdeps @fluidframework/azure-client
```

> [!TIP]
> Während der öffentlichen Vorschau werden die Versionen von **@fluidframework/azure-client** und **fluid-framework** übereinstimmen. Das heißt, wenn die aktuelle Version von **@fluidframework/azure-client** 0.48 ist, dann ist sie kompatibel mit **fluid-framework** 0.48. Der umgekehrte Fall trifft ebenfalls zu.

## <a name="compatibility-table"></a>Kompatibilitätstabelle

| npm-Paket                         | Mindestversion | API                                                              |
| ----------------------------------  | :-------------- | :--------------------------------------------------------------- |
| @fluidframework/azure-client        | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-client/)        |
| Fluid-Rahmenwerk                     | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/fluid-framework/)     |
| @fluidframework/azure-service-utils | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-service-utils/) |
| @fluidframework/test-client-utils   | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/test-client-utils/)   |

[0.48.4]: https://fluidframework.com/docs/updates/v0.48/

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines Azure Fluid Relay-Diensts](../how-tos/connect-fluid-azure-service.md)
- [Verbinden Sie sich mit einem Azure Fluid Relay-Dienst](../how-tos/connect-fluid-azure-service.md)
- [Verwenden Sie AzureClient für lokale Tests](../how-tos/local-mode-with-azure-client.md)
