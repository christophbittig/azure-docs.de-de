---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2e5699a4522ef9da30b51a97eba49d7bc6e2c001
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025975"
---
|Sprache                                 |1.x         |2.x| 3.x | 4.x |
|-----------------------------------------|------------|---| --- | --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)|Allgemeine Verfügbarkeit (.NET Framework 4.8)|Allgemeine Verfügbarkeit (.NET Core 2.1<sup>1</sup>)| Allgemeine Verfügbarkeit (.NET Core 3.1)<br/>[Allgemeine Verfügbarkeit (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) | Allgemeine Verfügbarkeit (.NET 6.0) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|Allgemeine Verfügbarkeit (Node 6)|Allgemeine Verfügbarkeit (Node 10 und 8)| Allgemeine Verfügbarkeit (Node 14, 12 und 10) | Allgemeine Verfügbarkeit (Node 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|Allgemeine Verfügbarkeit (.NET Framework 4.8)|Allgemeine Verfügbarkeit (.NET Core 2.1<sup>1</sup>)| Allgemeine Verfügbarkeit (.NET Core 3.1) | Allgemeine Verfügbarkeit (.NET 6.0) |
|[Java](../articles/azure-functions/functions-reference-java.md)|–|Allgemeine Verfügbarkeit (Java 8)| Allgemeine Verfügbarkeit (Java 11 und 8)| Allgemeine Verfügbarkeit (Java 11 und 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |–|Allgemeine Verfügbarkeit (PowerShell Core 6)| Allgemeine Verfügbarkeit (PowerShell 7.0 und Core 6)| Allgemeine Verfügbarkeit (PowerShell 7.0)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|–|Allgemeine Verfügbarkeit (Python 3.7 und 3.6)| Allgemeine Verfügbarkeit (Python 3.9, 3.8, 3.7 und 3.6)| Allgemeine Verfügbarkeit (Python 3.9, 3.8)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript)<sup>2</sup> |–|Allgemein verfügbar| Allgemein verfügbar | Allgemein verfügbar |

<sup>1</sup> Apps mit .NET-Klassenbibliotheken für Runtimeversion 2.x werden im .NET Core 2.x-Kompatibilitätsmodus unter .NET Core 3.1 ausgeführt. Weitere Informationen finden Sie unter [Überlegungen zu Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Unterstützt durch Transpilierung in JavaScript

Weitere Informationen zu unterstützten Sprachversionen finden Sie im sprachspezifischen Artikel im Entwicklerhandbuch.   
Informationen zu geplanten Änderungen an der Sprachunterstützung finden Sie unter [Azure-Roadmap](https://azure.microsoft.com/roadmap/?tag=functions).
