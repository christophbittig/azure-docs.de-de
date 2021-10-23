---
title: Linkaktionen in Azure Monitor-Arbeitsmappen
description: Erfahren Sie, wie Sie Linkaktionen in Azure Monitor-Arbeitsmappen verwenden.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: dd8d4b1d7aefe070f72c4087162527dadd69f09e
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129242"
---
# <a name="link-actions"></a>Linkaktionen

Auf Linkaktionen kann über Schritte für Arbeitsmappenlinks oder über Spalteneinstellungen für [Raster](../visualize/workbooks-grid-visualizations.md), [Kacheln](../visualize/workbooks-tile-visualizations.md) oder [Graphs](../visualize/workbooks-graph-visualizations.md) zugegriffen werden.

## <a name="general-link-actions"></a>Allgemeine Linkaktionen

| Linkaktion | Aktion beim Klicken |
|:------------- |:-------------|
| `Generic Details` | Zeigt die Zeilenwerte in einer Kontextansicht des Eigenschaftenrasters an. |
| `Cell Details` | Zeigt die Zellenwerte in einer Kontextansicht des Eigenschaftenrasters an. Nützlich, wenn die Zelle einen dynamischen Typ mit Informationen enthält (z. B. JSON mit Anforderungseigenschaften wie Standort, Rolleninstanz usw.). |
| `Url` | Als Wert der Zelle wird eine gültige HTTP-URL erwartet, und die Zelle ist ein Link, der diese URL auf einer neuen Registerkarte öffnet.|

## <a name="application-insights"></a>Application Insights

| Linkaktion | Aktion beim Klicken |
|:------------- |:-------------|
| `Custom Event Details` | Öffnet die Application Insights-Suchdetails mit der ID des benutzerdefinierten Ereignisses (`itemId`) in der Zelle. |
| `* Details` | Ähnlich wie „Benutzerdefinierte Ereignisdetails“, mit Ausnahme von Abhängigkeiten, Ausnahmen, Seitenaufrufen, Anforderungen und Ablaufverfolgungen. |
| `Custom Event User Flows` | Öffnet die Oberfläche für Benutzerflows von Application Insights, pivotiert für den Namen des benutzerdefinierten Ereignisses in der Zelle. |
| `* User Flows` | Ähnlich wie „Benutzerdefinierte Ereignisbenutzerflows“, mit Ausnahme von Ausnahmen, Seitenaufrufen und Anforderungen. |
| `User Timeline` | Öffnet die Benutzerzeitachse mit der Benutzer-ID (`user_Id`) in der Zelle. |
| `Session Timeline` | Öffnet die Suchoberfläche von Application Insights für den Wert in der Zelle (z. B. Suchen nach dem Text „abc“, wobei „abc“ der Wert in der Zelle ist). |

`*` bezeichnet einen Platzhalter in der obigen Tabelle.

## <a name="azure-resource"></a>Azure-Ressource

| Linkaktion | Aktion beim Klicken |
|:------------- |:-------------|
| `ARM Deployment` | Stellt eine Azure Resource Manager-Vorlage bereit.  Wenn dieses Element ausgewählt ist, werden zusätzliche Felder angezeigt, damit der Autor die zu öffnende Azure Resource Manager-Vorlage, die Parameter für die Vorlage usw. konfigurieren kann. Weitere Informationen finden Sie unter [Linkeinstellungen für die Azure Resource Manager-Bereitstellung](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Erstellt eine Warnungsregel für eine Ressource.  |
| `Custom View` | Öffnet eine benutzerdefinierte Ansicht. Wenn dieses Element ausgewählt ist, werden zusätzliche Felder angezeigt, damit der Autor die Ansichtserweiterung, den Ansichtsnamen und die zum Öffnen der Ansicht verwendeten Parameter konfigurieren kann. Weitere Informationen finden Sie unter [Linkeinstellungen für benutzerdefinierte Ansichten](#custom-view-link-settings). |
| `Metrics` | Öffnet eine Metrikenansicht.  |
| `Resource overview` | Öffnet die Ansicht der Ressource im Portal gemäß dem Wert der Ressourcen-ID in der Zelle. Der Autor kann optional auch einen `submenu`-Wert festlegen, mit dem ein bestimmtes Menüelement in der Ressourcenansicht geöffnet wird. |
| `Workbook (template)` | Öffnet eine Arbeitsmappenvorlage.  Wenn dieses Element ausgewählt ist, werden zusätzliche Felder angezeigt, damit der Autor die zu öffnende Vorlage usw. konfigurieren kann.  |

## <a name="link-settings"></a>Linkeinstellungen

Bei Verwendung des Linkrenderers sind die folgenden Einstellungen verfügbar:

![Screenshot der Linkeinstellungen](./media/workbooks-link-actions/link-settings.png)

| Einstellung | Erklärung |
|:------------- |:-------------|
| `View to open` | Ermöglicht dem Autor die Auswahl einer der oben aufgelisteten Aktionen. |
| `Menu item` | Wenn „Ressourcenübersicht“ ausgewählt ist, handelt es sich hierbei um das Menüelement in der zu öffnenden Übersicht für die Ressource. Hiermit können Warnungen oder Aktivitätsprotokolle anstelle der „Übersicht“ für die Ressource geöffnet werden. Die Werte für das Menüelement sind für jeden `Resourcetype` in Azure unterschiedlich.|
| `Link label` | Wenn dieser Wert angegeben wird, wird er in der Rasterspalte angezeigt. Wird dieser Wert nicht angegeben, wird der Wert der Zelle angezeigt. Wenn ein anderer Wert angezeigt werden soll, z. B. ein Wärmebild oder ein Symbol, verwenden Sie nicht den `Link`-Renderer, sondern den entsprechenden Renderer, und wählen Sie die Option `Make this item a link` aus. |
| `Open link in Context Blade` | Wenn diese Option aktiviert ist, wird der Link auf der rechten Seite des Fensters als Popup-Kontextansicht und nicht als vollständige Ansicht geöffnet. |

Bei Verwendung der Option `Make this item a link` sind die folgenden Einstellungen verfügbar:

| Einstellung | Erklärung |
|:------------- |:-------------|
| `Link value comes from` | Wenn eine Zelle als ein Renderer mit einem Link angezeigt wird, gibt dieses Feld an, woher der im Link zu verwendende „Link“-Wert stammt, sodass der Autor eine Auswahl in einer Dropdownliste der anderen Spalten im Raster treffen kann. Beispielsweise kann die Zelle ein Wärmebildwert sein, doch Sie möchten, dass über den Link die Ressourcenübersicht für die Ressourcen-ID in der Zeile geöffnet wird. In diesem Fall würden Sie festlegen, dass der Linkwert aus dem Feld `Resource Id` stammt.
| `View to open` | Wie oben. |
| `Menu item` | Wie oben. |
| `Open link in Context Blade` | Wie oben. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Linkeinstellungen für die Azure Resource Manager-Bereitstellung

Wenn der ausgewählte Linktyp `ARM Deployment` ist, muss der Autor zusätzliche Einstellungen angeben, um eine Azure Resource Manager-Bereitstellung zu öffnen. Es stehen zwei Hauptregisterkarten für Konfigurationen bereit.

### <a name="template-settings"></a>Vorlageneinstellungen

In diesem Abschnitt wird definiert, woher die Vorlage stammen soll und welche Parameter zum Ausführen der Azure Resource Manager-Bereitstellung verwendet werden.

| Quelle | Erklärung |
|:------------- |:-------------|
|`Resource group id comes from` | Die Ressourcen-ID wird zum Verwalten bereitgestellter Ressourcen verwendet. Das Abonnement dient zum Verwalten bereitgestellter Ressourcen und der Kosten. Die Ressourcengruppen werden wie Ordner zum Organisieren und Verwalten all Ihrer Ressourcen verwendet. Wenn dieser Wert nicht angegeben ist, tritt bei der Bereitstellung ein Fehler auf. Wählen Sie `Cell`, `Column`, `Static Value` oder `Parameter` wie unter [Linkquellen](#link-sources) angegeben aus.|
|`ARM template URI from` | Der URI für die Azure Resource Manager-Vorlage selbst. Der Vorlagen-URI muss den Benutzern zur Verfügung gestellt werden, die die Vorlage bereitstellen sollen. Wählen Sie `Cell`, `Column`, `Parameter` oder `Static Value` wie unter [Linkquellen](#link-sources) angegeben aus. Sehen Sie sich zunächst die [Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) an.|
|`ARM Template Parameters` | In diesem Abschnitt werden die Vorlagenparameter definiert, die für den oben definierten Vorlagen-URI verwendet werden. Diese Parameter dienen zum Bereitstellen der Vorlage auf der Ausführungsseite. Das Raster enthält eine Schaltfläche zum Erweitern der Symbolleiste, um die Parameter mit den im Vorlagen-URI definierten Namen aufzufüllen und auf statische leere Werte festzulegen. Diese Option kann nur verwendet werden, wenn im Raster keine Parameter vorhanden sind und der Vorlagen-URI festgelegt wurde. Der untere Abschnitt ist eine Vorschau der Parameterausgabe. Wählen Sie „Aktualisieren“ aus, um die Vorschau mit aktuellen Änderungen zu aktualisieren. Parameter sind normalerweise Werte, während Verweise auf KeyVault-Geheimnisse zeigen können, auf die der Benutzer Zugriff hat. <br/><br/> **Einschränkung beim Blatt „Vorlagenansicht“** : Verweisparameter werden nicht ordnungsgemäß gerendert und als NULL/Wert angezeigt, sodass Benutzer die Verweisparameter nicht korrekt über die Registerkarte „Vorlagenansicht“ bereitstellen können.|

![Screenshot der Vorlageneinstellungen für Azure Resource Manager](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX-Einstellungen

In diesem Abschnitt wird konfiguriert, was den Benutzern angezeigt wird, bevor sie die Azure Resource Manager-Bereitstellung ausführen.

| Quelle | Erklärung |
|:------------- |:-------------|
|`Title from` | Titel, der in der Ausführungsansicht verwendet wird. Wählen Sie `Cell`, `Column`, `Parameter` oder `Static Value` wie unter [Linkquellen](#link-sources) angegeben aus.|
|`Description from` | Dies ist der Markdowntext, der Benutzern eine hilfreiche Beschreibung bietet, wenn sie die Vorlage bereitstellen möchten. Wählen Sie `Cell`, `Column`, `Parameter` oder `Static Value` wie unter [Linkquellen](#link-sources) angegeben aus. <br/><br/> **HINWEIS:** Wenn `Static Value` ausgewählt ist, wird ein mehrzeiliges Textfeld angezeigt. In diesem Textfeld können Sie Parameter mithilfe von `{paramName}`auflösen. Sie können auch Spalten als Parameter handhaben, indem Sie nach dem Spaltennamen `_column` anhängen, z. B. `{columnName_column}`. In der folgenden Beispielabbildung kann mit `{VMName_column}` auf die Spalte `VMName` verwiesen werden. Der Wert nach dem Doppelpunkt ist der [Parameterformatierer](../visualize/workbooks-parameters.md#parameter-options) (in diesem Fall `value`).|
|`Run button text from` | Bezeichnung, die auf der Ausführungsschaltfläche zum Bereitstellen der Azure Resource Manager-Vorlage verwendet wird. Diese wird von Benutzern ausgewählt, um mit der Bereitstellung der Azure Resource Manager-Vorlage zu beginnen.|

![Screenshot der UX-Einstellungen für Azure Resource Manager](./media/workbooks-link-actions/ux-settings.png)

Nachdem diese Konfigurationen festgelegt wurden, wird den Benutzern beim Auswählen des Links die Ansicht mit der in den UX-Einstellungen beschriebenen Benutzeroberfläche geöffnet. Wenn der Benutzer `Run button text from` auswählt, wird eine Azure Resource Manager-Vorlage anhand der Werte in den [Vorlageneinstellungen](#template-settings) bereitgestellt. Mit „Vorlage anzeigen“ wird die Registerkarte „Vorlagenansicht“ geöffnet, damit der Benutzer die Vorlage und die Parameter vor der Bereitstellung überprüfen kann.

![Screenshot der Ansicht zum Ausführen von Azure Resource Manager](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Linkeinstellungen für benutzerdefinierte Ansichten

Hiermit können Sie benutzerdefinierte Ansichten im Azure-Portal öffnen. Überprüfen Sie die gesamte Konfiguration und alle Einstellungen. Falsche Werte führen zu Fehlern im Portal oder bewirken, dass die Ansichten nicht ordnungsgemäß geöffnet werden. Es gibt zwei Möglichkeiten, die Einstellungen zu konfigurieren: über `Form` oder `URL`.

> [!NOTE]
> Ansichten mit einem Menü können nicht auf einer Kontextregisterkarte geöffnet werden. Wenn eine Ansicht mit einem Menü zum Öffnen auf einer Kontextregisterkarte konfiguriert ist, wird beim Auswählen des Links keine Kontextregisterkarte angezeigt.

### <a name="form"></a>Formular

| Quelle | Erklärung |
|:------------- |:-------------|
|`Extension name` | Der Name der Erweiterung, die den Namen der Ansicht hostet.|
|`View name` | Der Name der zu öffnenden Ansicht.|

#### <a name="view-inputs"></a>Ansichtseingaben

Es gibt zwei Arten von Eingaben: Raster und JSON. Verwenden Sie `Grid` für einfache Schlüssel- und Werteingaben auf Registerkarten, oder wählen Sie `JSON` aus, um eine geschachtelte JSON-Eingabe anzugeben.

- Raster
    - `Parameter Name`: Der Name des Parameters für die Ansichtseingabe.
    - `Parameter Comes From`: Gibt an, woher der Wert des Ansichtsparameters stammen soll. Wählen Sie `Cell`, `Column`, `Parameter` oder `Static Value` wie unter [Linkquellen](#link-sources) angegeben aus.
    > [!NOTE]
    > Wenn `Static Value` ausgewählt ist, können die Parameter mithilfe des Links `{paramName}` im Textfeld aufgelöst werden. Spalten können als Parameter gehandhabt werden, indem Sie nach dem Spaltennamen `_column` anhängen, z. B. `{columnName_column}`.

    - `Parameter Value`: Je nach Festlegung für `Parameter Comes From` ist dies eine Dropdownliste der verfügbaren Parameter, Spalten oder eines statischen Werts.

    ![Screenshot des Bildschirms „Spalteneinstellungen bearbeiten“ mit Einstellungen für benutzerdefinierte Ansichten aus einem Formular](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Geben Sie die Eingabe auf der Registerkarte in einem JSON-Format im Editor an. Wie beim `Grid`-Modus, kann auf Parameter und Spalten mit `{paramName}` für Parameter und `{columnName_column}` für Spalten verwiesen werden. Bei Auswahl von `Show JSON Sample` wird die erwartete Ausgabe aller aufgelösten Parameter und Spalten für die Ansichtseingabe angezeigt.

    ![Screenshot des Bildschirms zum Öffnen der Einstellungen für benutzerdefinierte Ansichten mit Ansichtseingaben vom Typ JSON](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Fügen Sie eine Portal-URL mit der Erweiterung, dem Namen der Ansicht und den zum Öffnen der Ansicht erforderlichen Eingaben ein. Nach dem Auswählen von `Initialize Settings` wird `Form` aufgefüllt und der Autor kann Ansichtseingaben hinzufügen, ändern oder entfernen.

![Screenshot der Bearbeitung von Spalteneinstellungen mit Einstellungen für benutzerdefinierte Ansichten aus einer URL ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Linkeinstellungen für die Arbeitsmappe (Vorlage)

Wenn der ausgewählte Linktyp `Workbook (Template)` ist, muss der Autor zusätzliche Einstellungen angeben, um die richtige Arbeitsmappenvorlage zu öffnen. Die folgenden Einstellungen weisen Optionen dafür auf, wie das Raster den entsprechenden Wert für jede der Einstellungen findet.

| Einstellung | Erklärung |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Dies ist die Ressourcen-ID der Azure-Ressource, die „Besitzer“ der Arbeitsmappe ist. In der Regel handelt es sich dabei um eine Application Insights-Ressource oder einen Log Analytics-Arbeitsbereich. In Azure Monitor kann dies auch die Literalzeichenfolge `"Azure Monitor"` sein. Beim Speichern der Arbeitsmappe wird sie hiermit verknüpft. |
| `Workbook resources` | Ein Array von Azure-Ressourcen-IDs, die die in der Arbeitsmappe verwendete Standardressource angeben. Wenn die zu öffnende Vorlage beispielsweise Metriken virtueller Computer anzeigt, handelt es sich bei den hier aufgeführten Werten um Ressourcen-IDs virtueller Computer.  Häufig sind für den Besitzer und die Ressourcen die gleichen Einstellungen festgelegt. |
| `Template Id` | Geben Sie die ID der zu öffnenden Vorlage an. Handelt es sich hierbei um eine Communityvorlage aus dem Katalog (der häufigste Fall), stellen Sie dem Pfad für die Vorlage `Community-` als Präfix voran, z. B. `Community-Workbooks/Performance/Apdex` für die Vorlage `Workbooks/Performance/Apdex`. Wenn es sich um einen Link zu einer gespeicherten Arbeitsmappe/Vorlage handelt, ist dies die vollständige Azure-Ressourcen-ID dieses Elements. |
| `Workbook Type` | Geben Sie die Art der zu öffnenden Arbeitsmappenvorlage an. In den meisten Fällen wird die Option `Default` oder `Workbook` ausgewählt, um den Wert in der aktuellen Arbeitsmappe zu verwenden. |
| `Gallery Type` | Hiermit wird der Katalogtyp angegeben, der in der Ansicht „Katalog“ der geöffneten Vorlage angezeigt wird. In den meisten Fällen wird die Option `Default` oder `Workbook` ausgewählt, um den Wert in der aktuellen Arbeitsmappe zu verwenden. |
|`Location comes from` | Das Feld für den Speicherort sollte angegeben werden, wenn Sie eine bestimmte Arbeitsmappenressource öffnen. Wird der Speicherort nicht angegeben, erfolgt das Auffinden des Arbeitsmappeninhalts wesentlich langsamer. Wenn Sie den Speicherort kennen, geben Sie ihn an. Wenn Sie den Speicherort nicht kennen oder eine Vorlage öffnen, die keinen bestimmten Speicherort hat, behalten Sie bei diesem Feld die Einstellung „Standard“ bei.|
|`Pass specific parameters to template` | Wählen Sie diese Option aus, um bestimmte Parameter an die Vorlage zu übergeben. Bei Auswahl dieser Option werden nur die angegebenen Parameter an die Vorlage übergeben. Andernfalls werden alle Parameter in der aktuellen Arbeitsmappe an die Vorlage übergeben, was bedeutet, dass die *Namen* der Parameter in beiden Arbeitsmappen identisch sein müssen, damit dieser Parameterwert funktioniert.|
|`Workbook Template Parameters` | In diesem Abschnitt werden die Parameter definiert, die an die Zielvorlage übergeben werden. Der Name sollte mit dem Namen des Parameters in der Zielvorlage übereinstimmen. Wählen Sie als Wert `Cell`, `Column`, `Parameter` oder `Static Value`aus. Name und Wert dürfen nicht leer sein, wenn dieser Parameter an die Zielvorlage übergeben werden soll.|

Für jede der oben genannten Einstellungen muss der Autor auswählen, woher der Wert in der verknüpften Arbeitsmappe stammt. Informationen finden Sie unter [Linkquellen](#link-sources).

Beim Öffnen des Arbeitsmappenlinks werden der neuen Arbeitsmappenansicht alle Werte übergeben, die mit den oben angegebenen Einstellungen konfiguriert wurden.

![Screenshot der Einstellungen für Arbeitsmappenlinks](./media/workbooks-link-actions/workbook-link-settings.png)

![Screenshot der Parametereinstellungen für Arbeitsmappenvorlagen](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Linkquellen

| Quelle | Erklärung |
|:------------- |:-------------|
| `Cell` | Hiermit wird der Wert in dieser Zelle im Raster als Linkwert verwendet. |
| `Column` | Wenn diese Option ausgewählt ist, wird ein weiteres Feld angezeigt, in dem der Autor eine andere Spalte im Raster auswählen kann.  Der Wert dieser Spalte für die Zeile wird im Linkwert verwendet. Dies wird üblicherweise verwendet, um das Feld `Template Id` auf `column` festzulegen, damit mit jeder Zeile eines Rasters eine andere Vorlage geöffnet werden kann, oder um das Feld `Workbook resources` auf eine Spalte festzulegen, die eine Azure-Ressourcen-ID enthält, damit für verschiedene Ressourcen dieselbe Arbeitsmappenvorlage geöffnet wird. |
| `Parameter` | Wenn diese Option ausgewählt ist, wird ein weiteres Feld angezeigt, in dem der Autor einen Parameter auswählen kann. Der Wert dieses Parameters wird als Wert verwendet, wenn auf den Link geklickt wird. |
| `Static value` | Wenn diese Option ausgewählt ist, wird ein weiteres Feld angezeigt, in das der Autor einen statischen Wert eingeben kann, der in der verknüpften Arbeitsmappe verwendet wird. Dies wird üblicherweise verwendet, wenn alle Zeilen im Raster denselben Wert für ein Feld verwenden. |
| `Step` | Hiermit wird der Wert verwendet, der im aktuellen Schritt der Arbeitsmappe festgelegt ist. Dies ist bei Schritten für Abfragen und Metriken üblich, um die Arbeitsmappenressourcen in der verknüpften Arbeitsmappe auf diejenigen festzulegen, die *im Schritt für Abfragen/Metriken* verwendet werden, und nicht auf die aktuelle Arbeitsmappe. |
| `Workbook` | Hiermit wird der Wert verwendet, der in der aktuellen Arbeitsmappe festgelegt ist. |
| `Default` | Hiermit wird der Standardwert verwendet, der dann übernommen wird, wenn kein Wert angegeben wurde. Dies ist beim Katalogtyp üblich, bei dem der Standardkatalog durch den Typ der Besitzerressource festgelegt wird. |

## <a name="next-steps"></a>Nächste Schritte

- [Steuern](../visualize/workbooks-access-control.md) Sie den Zugriff auf Ihre Arbeitsmappenressourcen, und geben Sie diese frei.
- Erfahren Sie, wie [Gruppen in Arbeitsmappen](../visualize/workbooks-groups.md) verwendet werden.