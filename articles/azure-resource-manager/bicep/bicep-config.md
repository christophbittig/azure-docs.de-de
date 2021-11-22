---
title: Bicep-Konfigurationsdatei
description: Beschreibt die Konfigurationsdatei für Ihre Bicep-Bereitstellungen.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 8679daf65402c939f9eea7651b57b1198970fb8d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548138"
---
# <a name="configure-your-bicep-environment"></a>Konfigurieren Ihrer Bicep-Umgebung

Bicep unterstützt eine Konfigurationsdatei namens **bicepconfig.json**. In dieser Datei können Sie Werte hinzufügen, die Ihre Bicep-Entwicklungserfahrung anpassen. Wenn Sie diese Datei nicht hinzufügen, verwendet Bicep Standardwerte.

Um Werte anzupassen, erstellen Sie diese Datei in dem Verzeichnis, in dem Sie Bicep-Dateien speichern. Sie können „bicepconfig.json“-Dateien in mehreren Verzeichnissen hinzufügen. Die nächstgelegene Konfigurationsdatei in der Verzeichnishierarchie wird verwendet.

## <a name="available-settings"></a>Verfügbare Einstellungen

Bei der Arbeit mit [Modulen](modules.md) können Sie Aliase für Modulpfade hinzufügen. Diese Aliase vereinfachen Ihre Bicep-Datei, da Sie keine komplizierten Pfade wiederholen müssen. Sie können auch die Rangfolge der Anmeldeinformationen für die Authentifizierung bei der Registrierung konfigurieren. Die Anmeldeinformationen werden verwendet, um externe Module im lokalen Cache wiederherzustellen. Weitere Informationen finden Sie unter [Hinzufügen von Moduleinstellungen zur Bicep-Konfiguration](bicep-config-modules.md).

Bei der Arbeit mit dem [Bicep-Linter](linter.md) können Sie die Standardeinstellungen für die Bicep-Dateiüberprüfung überschreiben. Weitere Informationen finden Sie unter [Hinzufügen von Lintereinstellungen zur Bicep-Konfiguration](bicep-config-linter.md).

## <a name="intellisense"></a>Intellisense

Die Bicep-Erweiterung für Visual Studio Code unterstützt IntelliSense für Ihre **bicepconfig.json**-Datei. Verwenden Sie IntelliSense, um verfügbare Eigenschaften und Werte zu erkunden.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="IntelliSense-Unterstützung beim Konfigurieren von „bicepconfig.json“":::

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Moduleinstellungen zur Bicep-Konfiguration](bicep-config-modules.md)
* [Hinzufügen von Lintereinstellungen zur Bicep-Konfiguration](bicep-config-linter.md)
* Informationen zum [Bicep-Linter](linter.md)
