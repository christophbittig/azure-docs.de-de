---
title: Datei einfügen
description: Datei einfügen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/17/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cc661942dc1cf110a07df383149b03c8a4ea7409
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321023"
---
Der Code für alle Funktionen in einer bestimmten Funktions-App befindet sich in einem Stammprojektordner, der eine Hostkonfigurationsdatei enthält. Die Datei [host.json](../articles/azure-functions/functions-host-json.md) enthält die runtimespezifische Konfiguration und befindet sich im Stammordner der Funktions-App. Ein Ordner *bin* enthält Pakete und andere Bibliotheksdateien, die von der Funktions-App benötigt werden. Bestimmte Ordnerstrukturen, die von der Funktions-App benötigt werden, sind von der Sprache abhängig:

* [C#-kompiliert (CSPROJ)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-Skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#-Skript](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)

In der Version 2.x und höheren Versionen der Functions-Runtime müssen sich alle Funktionen in der Funktions-App denselben Sprachstapel teilen. 
