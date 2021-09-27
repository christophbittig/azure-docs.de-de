---
title: Implementieren von Widgets im Entwicklerportal
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie Widgets implementieren, die Daten von externen APIs nutzen und im Entwicklerportal für API Management anzeigen.
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: af2ac28df5b41606d9199d6fa36c3f683e86c59d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639905"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Implementieren von Widgets im Entwicklerportal

In diesem Tutorial implementieren Sie ein Widget, das Daten von externen APIs nutzt und im Entwicklerportal für API Management anzeigt.

Das Widget ruft Sitzungsbeschreibungen aus der Beispiel-API namens [Conference API](https://conferenceapi.azurewebsites.net/?format=json) ab. Der Sitzungsbezeichner wird über einen angegebenen Widget-Editor festgelegt.

Als Unterstützung bei der Entwicklung können Sie sich das fertige Widget im Ordner `examples` im [GitHub-Repository](https://github.com/Azure/api-management-developer-portal/) für das Entwicklerportal für API Management ansehen: `/examples/widgets/conference-session`.

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Screenshot des veröffentlichten Widgets":::

## <a name="prerequisites"></a>Voraussetzungen

* Richten Sie eine [lokale Umgebung](developer-portal-self-host.md#step-1-set-up-local-environment) für das aktuelle Release des Entwicklerportals ein.

* Machen Sie sich mit dem [Aufbau des Paperbits-Widgets](https://paperbits.io/wiki/widget-anatomy) vertraut.


## <a name="copy-the-scaffold"></a>Kopieren des Gerüsts

Verwenden Sie ein `widget`-Gerüst aus dem Ordner `/scaffolds` als Ausgangspunkt für die Erstellung des neuen Widgets.

1. Kopieren Sie den Ordner `/scaffolds/widget` nach `/community/widgets`.
1. Benennen Sie den Ordner in `conference-session` um.

## <a name="rename-exported-module-classes"></a>Umbenennen der exportierten Modulklassen

Benennen Sie die exportierten Modulklassen um, indem Sie das Präfix `Widget` in den folgenden Dateien durch `ConferenceSession` ersetzen:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Ändern Sie in der Datei `widget.design.module.ts` beispielsweise `WidgetDesignModule` in `ConferenceSessionDesignModule`:
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
zu 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Registrieren des Widgets

Registrieren Sie die Module des Widgets in den Stammmodulen des Portals, indem Sie in den entsprechenden Dateien die folgenden Zeilen hinzufügen:

1. `src/apim.design.module.ts`: Ein Modul, das Abhängigkeiten zur Entwurfszeit registriert

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts`: Ein Modul, das Abhängigkeiten zur Veröffentlichungszeit registriert

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts`: Laufzeitabhängigkeiten

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Platzieren des Widgets im Portal

Nun können Sie das duplizierte Gerüst hinzufügen und im Entwicklerportal verwenden.

1. Führen Sie den Befehl `npm start` aus.

1. Platzieren Sie das neue Widget nach dem Laden der Anwendung auf einer Seite. Sie finden es in der Widgetauswahl in der Kategorie `Community` unter dem Namen `Your widget`.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Screenshot der Widgetauswahl":::

1. Speichern Sie die Seite, indem Sie **STRG**+**S** (oder unter macOS **⌘**+**S**) drücken.

    > [!NOTE]
    > Zur Entwurfszeit können Sie weiterhin mit der Website interagieren, indem Sie die **STRG-TASTE** (oder **⌘**) gedrückt halten.

## <a name="add-custom-properties"></a>Hinzufügen benutzerdefinierter Eigenschaften

Damit das Widget Sitzungsbeschreibungen abrufen kann, muss es den Sitzungsbezeichner kennen. Fügen Sie den entsprechenden Schnittstellen und Klassen die `Session ID`-Eigenschaft hinzu:

Das Widget muss den Sitzungsbezeichner kennen, um Sitzungsbeschreibungen abrufen zu können. Fügen Sie den entsprechenden Schnittstellen und Klassen die Eigenschaft „Session ID“ hinzu:

1. `widgetContract.ts`: Datenvertrag (Datenebene), der definiert, wie die Widgetkonfiguration beibehalten wird.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts`: Modell (Geschäftsebene) – eine primäre Darstellung des Widgets im System. Diese Datei wird von Editoren aktualisiert und von der Präsentationsebene gerendert.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts`: Ansichtsmodell (Präsentationsebene) – ein spezifisches Objekt des Benutzeroberflächen-Frameworks, das vom Entwicklerportal mit der HTML-Vorlage gerendert wird.

    > [!NOTE]
    > In dieser Datei müssen Sie nichts ändern.

## <a name="configure-binders"></a>Konfigurieren von Bindern

Aktivieren Sie die Übermittlung der `sessionNumber` von der Datenquelle an die Widgetpräsentation. Bearbeiten Sie die Entitäten `ModelBinder` und `ViewModelBinder`:

1. Mit `widgetModelBinder.ts` können Sie das Modell anhand der im Vertrag beschriebenen Daten vorbereiten.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` enthält Informationen dazu, wie das Entwicklerportal das Modell (als Ansichtsmodell) in einem bestimmten Benutzeroberflächen-Framework darstellen muss.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Anpassen der Widgetvorlage zur Entwurfszeit

Die Komponenten der einzelnen Bereiche werden unabhängig voneinander ausgeführt. Sie verfügen über separate Container für die Abhängigkeitsinjektion, eine eigene Konfiguration, einen Lebenszyklus usw. Sie können sogar von unterschiedlichen Benutzeroberflächen-Frameworks unterstützt werden (in diesem Beispiel „Knockout.js“).

Zur Entwurfszeit ist jede Laufzeitkomponente nur ein HTML-Tag mit bestimmten Attributen und/oder Inhalten. Die Konfiguration wird bei Bedarf mit einfachem Markup übergeben. In unkomplizierten Fällen wie in diesem Beispiel wird der Parameter im Attribut übergeben. Wenn die Konfiguration komplexer ist, können Sie einen Bezeichner der erforderlichen Einstellung(en) verwenden, der von einem festgelegten Konfigurationsanbieter (z. B. `ISettingsProvider`) abgerufen wird.

1. Aktualisieren Sie die Datei `ko/widgetView.html`:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Wenn das Entwicklerportal die `attr`-Bindung zur *Entwurfszeit* oder *Veröffentlichungszeit* ausführt, sieht der resultierende HTML-Code wie folgt aus:

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Zur Laufzeit liest die Komponente `widget-runtime` dann die `sessionNumber` und verwendet sie im Initialisierungscode (siehe unten).

1. Aktualisieren Sie die Datei `widgetHandlers.ts`, um die Sitzungs-ID bei der Erstellung zuzuweisen:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Überarbeiten des Laufzeit-Ansichtsmodells

Bei den Laufzeitkomponenten handelt es sich um den Code, der in der Website selbst ausgeführt wird. Im Entwicklerportal für API Management sind dies z. B. all die Skripts hinter dynamischen Komponenten (z. B. *API-Details* und *API-Konsole*), die Vorgänge wie die Generierung des Codebeispiels, das Senden von Anforderungen usw. behandeln.

Das Ansichtsmodell Ihrer Laufzeitkomponente muss über die folgenden Methoden und Eigenschaften verfügen:

- Die `sessionNumber`-Eigenschaft (markiert mit dem Decorator-Element `Param`), die als von außen übergebener Eingabeparameter der Komponente verwendet wird (zur Entwurfszeit generiertes Markup, siehe vorheriger Schritt).
- Die an die Widgetvorlage gebundene `sessionDescription`-Eigenschaft (siehe `widget-runtime.html` weiter unten in diesem Artikel).
- Die `initialize`-Methode (mit dem Decorator-Element `OnMounted`), die aufgerufen wird, nachdem das Widget erstellt wurde und alle Parameter zugewiesen wurden. Diese Stelle im Code eignet sich gut zum Lesen der `sessionNumber` und Aufrufen der API mithilfe von `HttpClient`. `HttpClient` ist eine Abhängigkeit, die vom IoC-Container (Inversion of Control) eingefügt wird.

- Das Entwicklerportal erstellt zunächst das Widget und weist alle zugehörigen Parameter zu. Anschließend ruft es die `initialize`-Methode auf.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Optimieren der Widgetvorlage

Aktualisieren Sie Ihr Widget, um die Sitzungsbeschreibung anzuzeigen.

Verwenden Sie ein Absatztag und eine `markdown`- (oder `text`)-Bindung in der `ko/runtime/widget-runtime.html` Datei, um die Beschreibung zu rendern:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Hinzufügen des Widget-Editors

Das Widget ist jetzt zum Abrufen der Beschreibung der Sitzung `107` konfiguriert. Sie haben `107` im Code als Standardsitzung angegeben. Um zu überprüfen, ob Sie alles richtig konfiguriert haben, führen Sie `npm start` aus, und vergewissern Sie sich, dass das Entwicklerportal die Beschreibung auf der Seite anzeigt.

Führen Sie nun die folgenden Schritte aus, damit der Benutzer die Sitzungs-ID über einen Widget-Editor einrichten kann:

1. Aktualisieren Sie die Datei `ko/widgetEditorViewModel.ts`:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Mit Ausnahme der neuen `onChange`-Eigenschaft mit dem Decorator-Element `@Event()` verwendet das Editor-Ansichtsmodell den gleichen Ansatz wie oben beschrieben. Diese Eigenschaft verbindet den Rückruf zum Benachrichtigen der Listener (in diesem Fall ein Inhalts-Editor) über Änderungen am Modell.

1. Aktualisieren Sie die Datei `ko/widgetEditorView.html`:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Führen Sie `npm start` erneut aus. Sie sollten `sessionNumber` im Widget-Editor ändern können. Ändern Sie die ID in `108`, speichern Sie die Änderungen, und aktualisieren Sie die Registerkarte des Browsers. Falls Probleme auftreten, müssen Sie das Widget möglicherweise erneut auf der Seite hinzufügen.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Screenshot des Widget-Editors":::

## <a name="rename-the-widget"></a>Umbenennen des Widgets

Ändern Sie den Namen des Widgets in der Datei `constants.ts`:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Wenn Sie das Widget dem Repository hinzufügen, muss `widgetName` mit dem Ordnernamen identisch und vom Anzeigenamen abgeleitet sein (Kleinbuchstaben und Leerzeichen durch Bindestriche ersetzt). Die Kategorie sollte weiterhin `Community` sein.

## <a name="next-steps"></a>Nächste Schritte


Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)

- [Widgets beisteuern](developer-portal-widget-contribution-guidelines.md) – wir freuen uns über Beiträge aus der Community.

- Unter [Verwenden von Widgets aus der Community](developer-portal-use-community-widgets.md) erfahren Sie, wie Sie von der Community beigesteuerte Widgets verwenden.
