---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 76bfd886b471e5e4e2c3fa8a6733dcb67e7ea77d
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493229"
---
|Sprache                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)<sup>1</sup>|Allgemeine Verfügbarkeit (.NET Framework 4.8)|Allgemeine Verfügbarkeit (.NET Core 2.1<sup>2</sup>)| Allgemeine Verfügbarkeit (.NET Core 3.1)<br/>[Allgemeine Verfügbarkeit (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|Allgemeine Verfügbarkeit (Node 6)|Allgemeine Verfügbarkeit (Node 10 und 8)| Allgemeine Verfügbarkeit (Node 14, 12 und 10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|Allgemeine Verfügbarkeit (.NET Framework 4.8)|Allgemeine Verfügbarkeit (.NET Core 2.1<sup>2</sup>)| Allgemeine Verfügbarkeit (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|–|Allgemeine Verfügbarkeit (Java 8)| Allgemeine Verfügbarkeit (Java 11 und 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |–|Allgemeine Verfügbarkeit (PowerShell Core 6)| Allgemeine Verfügbarkeit (PowerShell 7.0 und Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|–|Allgemeine Verfügbarkeit (Python 3.7 und 3.6)| Allgemeine Verfügbarkeit (Python 3.8, 3.7 und 3.6) <br/> Vorschau (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |–|Allgemeine Verfügbarkeit<sup>3</sup>| Allgemeine Verfügbarkeit<sup>3</sup> |

<sup>1</sup> Es ist eine experimentelle Version von Azure Functions verfügbar, mit der Sie die Vorschauversion von .NET 6.0 verwenden können. Weitere Informationen finden Sie auf der Seite zur [frühen Vorschauversion von Azure Functions v4](https://aka.ms/functions-dotnet6earlypreview-wiki).
<sup>2</sup> Apps mit .NET-Klassenbibliotheken für Runtimeversion 2.x werden im .NET Core 2.x-Kompatibilitätsmodus unter .NET Core 3.1 ausgeführt. Weitere Informationen finden Sie unter [Überlegungen zu Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>3</sup> Unterstützt durch Transpilierung in JavaScript

Weitere Informationen zu unterstützten Sprachversionen finden Sie im sprachspezifischen Artikel im Entwicklerhandbuch.   
Informationen zu geplanten Änderungen an der Sprachunterstützung finden Sie unter [Azure-Roadmap](https://azure.microsoft.com/roadmap/?tag=functions).
