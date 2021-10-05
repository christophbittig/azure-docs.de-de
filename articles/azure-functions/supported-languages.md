---
title: In Azure Functions unterstützte Sprachen
description: Erfahren Sie, welche Sprachen unterstützt werden (GA) und welche sich in der Vorschau befinden sowie wie die Entwicklung mit Functions auf andere Sprachen ausgeweitet werden kann.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: d0db327a1f5b4189f9715687e3efa8fbe5b668db
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599937"
---
# <a name="supported-languages-in-azure-functions"></a>In Azure Functions unterstützte Sprachen

In diesem Artikel werden die Unterstützungsebenen für Sprachen erläutert, die Sie mit Azure Functions verwenden können. Außerdem werden Strategien zum Erstellen von Funktionen in Sprachen beschrieben, die nicht nativ unterstützt werden.

[!INCLUDE [functions-support-levels](../../includes/functions-support-levels.md)]

## <a name="languages-by-runtime-version"></a>Sprachen nach Runtimeversion 

Es sind [verschiedene Versionen der Azure Functions-Runtime](functions-versions.md) verfügbar. Die folgende Tabelle gibt an, welche Sprachen in den beiden Runtimeversionen unterstützt werden.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="language-major-version-support"></a>Sprachunterstützung für Hauptversionen

Azure Functions bietet eine garantierte Unterstützung für die Hauptversionen unterstützter Programmiersprachen. Für die meisten Sprachen gibt es Neben- oder Patchversionen, die zur Aktualisierung einer unterstützten Hauptversion veröffentlicht werden. Beispiele für Neben- oder Patchversionen sind Python 3.9.1 und Node 14.17. Sobald neue Nebenversionen unterstützter Sprachen verfügbar sind, werden die von Ihren Funktions-Apps verwendeten Nebenversionen automatisch auf diese neueren Neben- oder Patchversionen aktualisiert. 

> [!NOTE]
>Da Azure Functions die Unterstützung älterer Nebenversionen jederzeit einstellen kann, sobald eine neue Nebenversion verfügbar ist, sollten Sie Ihre Funktions-Apps nicht an eine bestimmte Neben-/Patchversion einer Programmiersprache binden.  
>

## <a name="custom-handlers"></a>Benutzerdefinierte Handler

Benutzerdefinierte Handler sind einfache Webserver, die Ereignisse vom Azure Functions-Host empfangen. Jede Sprache, die HTTP-Primitive unterstützt, kann einen benutzerdefinierten Handler implementieren. Dies bedeutet, dass benutzerdefinierte Handler zum Erstellen von Funktionen in Sprachen verwendet werden können, die nicht offiziell unterstützt werden. Weitere Informationen finden Sie unter [Benutzerdefinierte Azure Functions-Handler](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Spracherweiterbarkeit

Ab Version 2.x ist die Runtime auf [Spracherweiterbarkeit](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) ausgelegt. Die Sprachen JavaScript und Java in der Runtime 2.x verfügen über diese Erweiterbarkeit.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwickeln von Funktionen in unterstützten Sprachen finden Sie in den folgenden Ressourcen:

+ [Entwicklerreferenz zur C#-Klassenbibliothek](functions-dotnet-class-library.md)
+ [Entwicklerreferenz für C#-Skript](functions-reference-csharp.md)
+ [Java-Entwicklerreferenz](functions-reference-java.md)
+ [JavaScript-Entwicklerreferenz](functions-reference-node.md)
+ [PowerShell-Entwicklerreferenz](functions-reference-powershell.md)
+ [Python-Entwicklerreferenz](functions-reference-python.md)
+ [TypeScript-Entwicklerreferenz](functions-reference-node.md#typescript)
