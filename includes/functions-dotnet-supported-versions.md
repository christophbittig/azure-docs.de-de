---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: f576840f87387ba6896c6fda3a5ef663cedaffaf
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132040391"
---
## <a name="supported-versions"></a>Unterstützte Versionen

Versionen der Functions-Laufzeit funktionieren mit bestimmten Versionen von .NET. Weitere Informationen zu den Functions-Versionen finden Sie unter [Übersicht über die Runtimeversionen von Azure Functions](../articles/azure-functions/functions-versions.md). Die Versionsunterstützung hängt davon ab, ob Ihre Funktionen prozessintern oder prozessextern (isoliert) ausgeführt werden. 

In der folgenden Tabelle sind die höchsten .NET Core- bzw. .NET Framework-Versionen aufgeführt, die mit einer bestimmten Version von Functions verwendet werden können. 

| Version der Functions-Laufzeit | In-Process<br/>([.NET-Klassenbibliothek](../articles/azure-functions/functions-dotnet-class-library.md)) | Out-of-Process<br/>([Isolierte .NET-Ausführung](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x | .NET 6.0 | .NET 6.0 |
| Functions 3.x | .NET Core 3.1 | .NET 5.0<sup>1</sup> |
| Functions 2.x | .NET Core 2.1<sup>2</sup> | – |
| Functions 1.x | .NET Framework 4.8 | – |


<sup>1</sup> Für den Buildprozess ist auch das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download) erforderlich.   
<sup>2</sup> Weitere Informationen finden Sie unter [Überlegungen zu Functions-Versionen 2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).     

Aktuelle Informationen zu Azure Functions-Releases (einschließlich Informationen zur Entfernung bestimmter älterer Nebenversionen) finden Sie unter [Azure App Service-Ankündigungen](https://github.com/Azure/app-service-announcements/issues).
