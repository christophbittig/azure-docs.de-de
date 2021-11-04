---
title: Importieren einer GraphQL-API mithilfe des Azure-Portals | Microsoft-Dokumentation
titleSuffix: ''
description: Hier erfahren Sie, wie API Management GraphQL unterstützt und wie Sie eine GraphQL-API und GraphQL-Einschränkungen hinzufügen.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1541e9de6d2f9346826e256fcb854b0ca7cdac3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095312"
---
# <a name="import-a-graphql-api-preview"></a>Importieren einer GraphQL-API (Vorschauversion)

GraphQL ist eine Open-Source-Abfragesprache für APIs, die dem Branchenstandard entspricht. Im Gegensatz zu endpunktbasierten (oder REST-basierten) APIs, die auf Aktionen für Ressourcen ausgerichtet sind, unterstützen GraphQL-APIs mehr Anwendungsfälle und konzentrieren sich auf Datentypen, Schemas und Abfragen.

API Management löst die Sicherheits-, Authentifizierungs- und Autorisierungsprobleme, die mit der Veröffentlichung von GraphQL-APIs verbunden sind. Wenn Sie Ihre GraphQL-APIs mit API Management verfügbar machen, haben Sie die folgenden Möglichkeiten:
* Hinzufügen eines GraphQL-Diensts als API über das Azure-Portal  
* Schützen von GraphQL-APIs vor GraphQL-spezifischen Angriffen durch Anwenden vorhandener Zugriffssteuerungsrichtlinien und [neuer Richtlinien](graphql-validation-policies.md) 
* Untersuchen des Schemas und Ausführen von Testabfragen für GraphQL-APIs im Azure-Portal und im Entwicklerportal 

[!INCLUDE [preview-callout-graphql.md](./includes/preview/preview-callout-graphql.md)]

In diesem Artikel führen Sie Folgendes durch:
> [!div class="checklist"]
> * Erwerb von Grundwissen zu den Vorteilen von GraphQL-APIs
> * Hinzufügen einer GraphQL-API zu Ihrer API Management-Instanz
> * Testen Ihrer GraphQL-API
> * Kennenlernen der Einschränkungen Ihrer GraphQL-API in API Management

## <a name="prerequisites"></a>Voraussetzungen

- Eine bestehende API Management-Instanz. [Erstellen Sie eine, falls nicht schon geschehen](get-started-create-service-instance.md).
- Eine GraphQL-API 

## <a name="add-a-graphql-api"></a>Hinzufügen einer GraphQL-API

1. Navigieren Sie zu Ihrer API Management-Instanz.
1. Wählen Sie im Seitennavigationsmenü im Abschnitt **APIs** die Option **APIs** aus.
1. Wählen Sie unter **Define a new API** (Neue API definieren) das **GraphQL-Symbol** aus.

    :::image type="content" source="media/graphql-api/import-graphql-api.png" alt-text="GraphQL-Symbol aus der API-Liste auswählen":::

1. Wählen Sie im Dialogfeld **Vollständig** aus, und füllen Sie die erforderlichen Formularfelder aus.

    :::image type="content" source="media/graphql-api/create-from-graphql-schema.png" alt-text="Veranschaulichung: Felder für die GraphQL-Erstellung":::

    | Feld | BESCHREIBUNG |
    |----------------|-------|
    | Anzeigename | Das ist der Name, mit dem Ihre GraphQL-API angezeigt wird. |
    | Name | Das ist der unformatierte Name der GraphQL-API. Er wird automatisch ausgefüllt, wenn Sie den Anzeigenamen eingeben. |
    | GraphQL-API-Endpunkt | Das ist die Basis-URL mit dem Namen Ihres GraphQL-API-Endpunkts. <br /> Beispiel: *https://example.com/your-GraphQL-name* . Sie können auch den beliebten [GraphQL-Endpunkt „Star Wars“](https://swapi-graphql.netlify.app/.netlify/functions/index) als Demo verwenden. |
    | Schemadatei hochladen | Wählen Sie diese Option aus, um Ihr Verzeichnis zu durchsuchen und Ihre Schemadatei hochzuladen. |
    | BESCHREIBUNG | Fügen Sie eine Beschreibung Ihrer API hinzu. |
    | URL-Schema | Wählen Sie „HTTP“, „HTTPS“ oder „Beides“ aus. Standardeinstellung: *Beides* |
    | API-URL-Suffix| Fügen Sie ein URL-Suffix hinzu, um diese spezifische API in dieser API Management-Instanz zu identifizieren. Es muss in dieser API Management-Instanz eindeutig sein. |
    | Basis-URL | In diesem nicht bearbeitbaren Feld wird die Basis-URL für Ihre API angezeigt. |
    | `Tags` | Ordnen Sie Ihre GraphQL-API neuen oder vorhandenen Tags zu. |
    | Produkte | Ordnen Sie Ihre GraphQL-API einem Produkt zu, um sie zu veröffentlichen. |
    | Gateways | Ordnen Sie Ihre GraphQL-API den vorhandenen Gateways zu. Standardgateway: *Verwaltet* |
    | Versionsverwaltung für diese API? | Wählen Sie diese Option aus, um die Versionskontrolle für Ihre GraphQL-API zu verwenden. |
 
1. Klicken Sie auf **Erstellen**.

## <a name="test-your-graphql-api"></a>Testen Ihrer GraphQL-API

1. Navigieren Sie zu Ihrer API Management-Instanz.
1. Wählen Sie im Seitennavigationsmenü im Abschnitt **APIs** die Option **APIs** aus.
1. Wählen Sie unter **Alle APIs** Ihre GraphQL-API aus.
1. Klicken Sie auf die Registerkarte **Test**, um auf die Testkonsole zuzugreifen. 
1. Unter **Header**:
    1. Wählen Sie im Dropdownmenü **Name** den Header aus.
    1. Geben Sie den Wert in das Feld **Wert** ein.
    1. Fügen Sie weitere Header hinzu, indem Sie **+ Header hinzufügen** auswählen.
    1. Löschen Sie Header mithilfe des **Papierkorbsymbols**.
1. Wenn Sie Ihrer GraphQL-API ein Produkt hinzugefügt haben, wenden Sie den Produktbereich mithilfe von **Apply product scope** (Produktbereich anwenden) an.
1. Führen Sie im **Abfrage-Editor** eine der folgenden Aktionen aus:
    1. Wählen Sie mindestens ein Feld oder Unterfeld aus der Liste im Seitenmenü aus. Die von Ihnen ausgewählten Felder und Unterfelder werden im Abfrage-Editor angezeigt.
    1. Beginnen Sie mit der Eingabe im Abfrage-Editor, um eine Abfrage zu erstellen.
    
        :::image type="content" source="media/graphql-api/test-graphql-query.png" alt-text="Veranschaulichung: Felder zum Abfrage-Editor hinzufügen":::

1. Fügen Sie unter **Abfragevariablen** Variablen hinzu, um eine Abfrage oder Mutation wiederzuverwenden und unterschiedliche Werte zu übergeben.
1. Klicken Sie auf **Send**.
1. Zeigen Sie die **Antwort** an.

    :::image type="content" source="media/graphql-api/graphql-query-response.png" alt-text="Anzeigen der Testabfrageantwort":::

1. Wiederholen Sie die vorherigen Schritte, um verschiedene Nutzdaten zu testen.
1. Beenden Sie die Testkonsole, wenn die Tests abgeschlossen sind.

## <a name="limitations"></a>Einschränkungen

* Nur GraphQL-Passthrough wird unterstützt. 
* Jede GraphQL-API in API Management entspricht nur einem einzigen GraphQL-Back-End-Endpunkt.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
