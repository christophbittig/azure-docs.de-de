---
title: Verwenden von Communitywidgets im Entwicklerportal
titleSuffix: Azure API Management
description: Hier finden Sie Informationen zu Communitywidgets für das API Management-Entwicklerportal, und Sie erfahren, wie Sie sie in Ihren Code integrieren und verwenden.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 3fe751483ea22ff3464cc1fd505b313a5a48ade7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656320"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Verwenden von Communitywidgets im Entwicklerportal

Alle Entwickler platzieren ihre für die Community beigesteuerten Widgets im Ordner `/community/widgets/` des [GitHub-Repositorys](https://github.com/Azure/api-management-developer-portal) für das API Management-Entwicklerportal. Die Widgets wurden jeweils vom Entwicklerportalteam akzeptiert. Sie können die Widgets verwenden, indem Sie sie in Ihre [selbstgehostete Version](developer-portal-self-host.md) des Portals einfügen. Von der verwalteten Version des Entwicklerportals werden aktuell keine Communitywidgets unterstützt.

> [!NOTE]
> Die beigesteuerten Widgets und ihre Abhängigkeiten werden vom Entwicklerportalteam sorgfältig untersucht. Das Team kann jedoch nicht garantieren, dass die Widgets gefahrlos geladen werden können. Die Entscheidung für oder gegen die Verwendung eines von der Community beigesteuerten Widgets liegt allein in Ihrem Ermessen. Weitere Informationen zu unseren vorbeugenden Maßnahmen finden Sie in unseren [Richtlinien für Mitwirkende](developer-portal-widget-contribution-guidelines.md#contribution-guidelines).

## <a name="inject-and-use-external-widgets"></a>Einfügen und Verwenden externer Widgets

1. Richten Sie eine [lokale Umgebung](developer-portal-self-host.md#step-1-set-up-local-environment) für das aktuelle Release des Entwicklerportals ein.

1. Navigieren Sie im Verzeichnis `/community/widgets` zum Ordner des Widgets. Lesen Sie die Beschreibung des Widgets in der Datei `readme.md`.

1. Registrieren Sie das Widget in den Modulen des Portals:

    1. `src/apim.design.module.ts`: Ein Modul, das Abhängigkeiten zur Entwurfszeit registriert
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts`: Ein Modul, das Abhängigkeiten zur Veröffentlichungszeit registriert
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts`: Ein Modul, das Abhängigkeiten zur Laufzeit registriert
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Überprüfen Sie, ob das Widget über eine Datei vom Typ `npm_dependencies` verfügt.

1. Falls ja, kopieren Sie die Befehle aus der Datei, und führen Sie sie im obersten Verzeichnis des Repositorys aus.

    Dadurch werden die Abhängigkeiten des Widgets installiert.

1. Führen Sie `npm start` aus.

Das Widget wird in der Widgetauswahl in der Kategorie **Community** angezeigt.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Screenshot: Widgetauswahl":::


## <a name="next-steps"></a>Nächste Schritte


Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)

- [Beisteuern von Widgets zum API Management-Entwicklerportal](developer-portal-widget-contribution-guidelines.md)
