---
title: 'Azure Storage-Explorer: Befehlszeilenoptionen | Microsoft-Dokumentation'
description: 'Dokumentation zu Azure Storage-Explorer: Befehlszeilenoptionen beim Start'
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: f5c43822bb9b856126c9b1ff80212d457044887b
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449529"
---
# <a name="azure-storage-explorer-command-line-options"></a>Storage-Explorer: Befehlszeilenoptionen

Microsoft Azure Storage-Explorer verfügt über eine Reihe von Befehlszeilenoptionen, die beim Starten der Anwendung hinzugefügt werden können. Die meisten Befehlszeilenoptionen dienen zum Debuggen oder zur Problembehandlung.

## <a name="command-line-options"></a>Befehlszeilenoptionen
Option  | BESCHREIBUNG
:------- | :-----------
`--debug`/`--prod`  | Startet die Anwendung im Debug- oder Produktionsmodus. Im Debugmodus werden die Daten der lokalen Anlage im lokalen Speicher der Anwendung gespeichert und nicht verschlüsselt. Ausgeblendete Eigenschaften werden im Bereich „Eigenschaften“ für ausgewählte Ressourcenknoten angezeigt. Der Ausführlichkeitsgrad des Protokolls wird so festgelegt, dass Debugmeldungen ausgegeben werden, die die interne Setuplogik von Storage-Explorer offenlegen. Standardwert: `--prod`.
`--lang`  | Startet die Anwendung mit einer bestimmten Sprache. Beispiel: `--lang="zh-Hans"`.
`--disable-gpu` | Startet die Anwendung ohne GPU-Beschleunigung.
`--auto-open-dev-tools` | Ermöglicht der Anwendung, das Fenster „Entwicklertools“ zu öffnen, sobald das Browserfenster angezeigt wird. Diese Option ist nützlich, wenn Sie einen Breakpoint in einer Zeile im Startcode des Browserfensters erreichen möchten.
`--verbosity` | Legt den Ausführlichkeitsgrad der Storage-Explorer-Protokollierung fest. Die unterstützten Protokollierungsgrade sind `debug`, `verbose`, `info`, `warn`, `error` und `silent`. Beispiel: `--verbosity=verbose`. Bei Ausführung im Produktionsmodus ist der standardmäßige Ausführlichkeitsgrad `info`. Bei Ausführung im Debugmodus ist der Ausführlichkeitsgrad des Protokolls immer `debug`.
`--log-dir` | Legt das Verzeichnis fest, in dem Protokolldateien gespeichert werden. Beispiel: `--log-dir=path_to_a_directory`.

Beispiel für das Starten von Storage-Explorer mit benutzerdefinierten Befehlszeilenoptionen

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Diese Befehlszeilenoptionen können sich in neuen Versionen von Storage-Explorer ändern.

## <a name="when-to-use-command-line-options"></a>Verwenden von Befehlszeilenoptionen

Einige Befehlszeilenoptionen können zum Anpassen von Storage-Explorer verwendet werden. Für Optionen, die über entsprechende Benutzereinstellungen verfügen, z. B. `--lang`. Es wird empfohlen, anstelle der Befehlszeilenoption Benutzereinstellungen zu verwenden. 

Die anderen Befehlszeilenoptionen können für das Debuggen und Problembehandlung hilfreich sein. Wenn in Storage-Explorer ein Problem auftritt, können Sie das Problem im Debugmodus reproduzieren, um detailliertere Informationen zur Untersuchung zu erhalten.