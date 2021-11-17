---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: 7257f70acb8b931791f3f08437ba61b05e1b4f6d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026678"
---
Dieser Artikel unterstützt die Erstellung beider Arten von kompilierten C#-Funktionen: 

| Ausführungsmodell | BESCHREIBUNG |
| --- | --- |
| **[In-Process](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| Ihr Funktionscode wird im gleichen Prozess wie der Functions-Hostprozess ausgeführt. Unterstützt sowohl .NET Core 3.1 als auch .NET 6.0. Weitere Informationen finden Sie unter [Entwickeln von C#-Klassenbibliotheksfunktionen mithilfe von Azure Functions](../articles/azure-functions/functions-dotnet-class-library.md). |
| **[Isolierter Prozess](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| Ihr Funktionscode wird in einem separaten .NET-Workerprozess ausgeführt. Unterstützt sowohl .NET 5.0 als auch .NET 6.0. Weitere Informationen finden Sie unter [Leitfaden: Ausführen von .NET 5.0-Funktionen in Azure](../articles/azure-functions/dotnet-isolated-process-guide.md). |