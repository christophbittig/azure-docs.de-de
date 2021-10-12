---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: ed981abd0ff849fb2a88164b2814794a48603ce7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559904"
---
Dieser Artikel unterstützt die Erstellung beider Arten von kompilierten C#-Funktionen: 

| Ausführungsmodell | BESCHREIBUNG |
| --- | --- |
| **[In-Process](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| Ihr Funktionscode wird im gleichen Prozess wie der Functions-Hostprozess ausgeführt. Weitere Informationen finden Sie unter [Entwickeln von C#-Klassenbibliotheksfunktionen mithilfe von Azure Functions](../articles/azure-functions/functions-dotnet-class-library.md). |
| **[Isolierter Prozess](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| Ihr Funktionscode wird in einem separaten .NET-Workerprozess ausgeführt. Weitere Informationen finden Sie im [Handbuch zum Ausführen von Funktionen unter .NET 5.0 in Azure](../articles/azure-functions/dotnet-isolated-process-guide.md). |