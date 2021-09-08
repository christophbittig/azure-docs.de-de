---
title: In Azure Functions unterstützte Sprachen
description: Erfahren Sie, welche Sprachen unterstützt werden (GA) und welche sich in der Vorschau befinden sowie wie die Entwicklung mit Functions auf andere Sprachen ausgeweitet werden kann.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 02a416fa669cd71702e991c79677575bd4563bfb
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493230"
---
# <a name="supported-languages-in-azure-functions"></a>In Azure Functions unterstützte Sprachen

In diesem Artikel werden die Unterstützungsebenen für Sprachen erläutert, die Sie mit Azure Functions verwenden können. Außerdem werden Strategien zum Erstellen von Funktionen in Sprachen beschrieben, die nicht nativ unterstützt werden.

## <a name="levels-of-support"></a>Unterstützungsebenen

Es gibt zwei Unterstützungsebenen:

* **Allgemein verfügbar (generally available, GA)** : Vollständige Unterstützung und Freigabe für die Verwendung in Produktionsumgebungen.
* **Vorschau**: Noch nicht unterstützt, die Erreichung der allgemeinen Verfügbarkeit wird aber für die Zukunft erwartet.

## <a name="languages-by-runtime-version"></a>Sprachen nach Runtimeversion 

Es sind [drei Versionen der Azure Functions-Runtime](functions-versions.md) verfügbar. Die folgende Tabelle gibt an, welche Sprachen in den beiden Runtimeversionen unterstützt werden.

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
