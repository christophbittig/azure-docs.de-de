---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.openlocfilehash: 00b9451edfc7016fb8a3d4dcfc7c5adc1b95c880
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861388"
---
1. Navigieren Sie in Visual Studio Code zu *src/edge*. Die *ENV*-Datei und einige Bereitstellungsvorlagendateien werden angezeigt.

    Die Bereitstellungsvorlage verweist auf das Bereitstellungsmanifest für das Edgegerät, wobei für einige Eigenschaften Variablen verwendet werden. Die *ENV*-Datei enthält die Werte für diese Variablen.
1. Navigieren Sie zum Ordner *src/cloud-to-device-console-app*. Darin werden die Datei *appsettings.json* und einige andere Dateien angezeigt:

    * ***c2d-console-app.csproj***: Die Projektdatei für Visual Studio Code.
    * ***operations.json***: Eine Liste mit den Vorgängen, die vom Programm ausgeführt werden sollen.
    * ***Program.cs***: Das Beispiel für den Programmcode. Mit diesem Code wird Folgendes durchgeführt:

      * Laden der App-Einstellungen
      * Aufrufen direkter Methoden, die vom Video Analyzer-Edge-Modul verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](../../../direct-methods.md) aufrufen.
      * Anhalten der Ausführung, damit Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
      * Aufrufen direkter Methoden zur Bereinigung der Ressourcen
