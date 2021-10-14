---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: 729275766806bf5fe47d35b04ddc58b11c58217b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908800"
---
## <a name="supported-versions"></a>Unterstützte Versionen

Versionen der Functions-Laufzeit funktionieren mit bestimmten Versionen von .NET. Weitere Informationen zu den Functions-Versionen finden Sie unter [Übersicht über die Runtimeversionen von Azure Functions](../articles/azure-functions/functions-versions.md). Die Versionsunterstützung hängt davon ab, ob Ihre Funktionen prozessintern oder prozessextern (isoliert) ausgeführt werden. 

In der folgenden Tabelle sind die höchsten .NET Core- bzw. .NET Framework-Versionen aufgeführt, die mit einer bestimmten Version von Functions verwendet werden können. 

| Version der Functions-Laufzeit | In-Process<br/>([.NET-Klassenbibliothek](../articles/azure-functions/functions-dotnet-class-library.md)) | Out-of-Process<br/>([Isolierte .NET-Ausführung](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x (Vorschau) | .NET 6.0 (Vorschau)| .NET 6.0 (Vorschau)<sup>2</sup> |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>1</sup> | – |
| Functions 1.x | .NET Framework 4.8 | – |

<sup>1</sup> Weitere Informationen finden Sie unter [Überlegungen zu Functions-Versionen 2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).    
<sup>2</sup> Sie können derzeit nur isolierte Prozessfunktionen mithilfe von Azure Functions Core Tools erstellen. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer C#-Funktion über die Befehlszeile in Azure](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process).  

Aktuelle Informationen zu Azure Functions-Releases (einschließlich Informationen zur Entfernung bestimmter älterer Nebenversionen) finden Sie unter [Azure App Service-Ankündigungen](https://github.com/Azure/app-service-announcements/issues).
