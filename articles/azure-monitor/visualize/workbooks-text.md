---
title: Textparameter in Azure Monitor-Arbeitsmappen
description: Vereinfachen der komplexen Berichterstellung mit vordefinierten und benutzerdefiniert parametrisierten Arbeitsmappen. Erfahren Sie mehr über Textparameter in Arbeitsmappen.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: e3beedff4b9d65f5f0b69b5c60bd67d4b134d096
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287524"
---
# <a name="workbook-text-parameters"></a>Textparameter in Arbeitsmappen

Textfeldparameter bieten eine einfache Möglichkeit, Texteingaben von Arbeitsmappenbenutzern zu erfassen. Sie werden verwendet, wenn das Erfassen von Eingaben über ein Dropdown nicht praktikabel ist (z. B. bei einem beliebigen Schwellenwert oder generischen Filtern). Mit Arbeitsmappen können Autoren den Standardwert des Textfelds über eine Abfrage erhalten. Dies ermöglicht interessante Szenarien wie das Festlegen des Standardschwellenwerts basierend auf dem p95-Wert der Metrik.

Textfelder werden häufig als interne Variablen genutzt, die von anderen Steuerelementen der Arbeitsmappe verwendet werden. Dazu wird eine Abfrage für Standardwerte verwendet und das Eingabesteuerelement im Lesemodus als unsichtbar festgelegt. Ein Benutzer möchte beispielsweise einen Schwellenwert aus einer Formel (und nicht von einem Benutzer) beziehen und diesen Schwellenwert in nachfolgenden Abfragen verwenden.

## <a name="creating-a-text-parameter"></a>Erstellen eines Textparameters
1. Beginnen Sie mit einer leeren Arbeitsmappe im Bearbeitungsmodus.
2. Wählen Sie unter den Links in der Arbeitsmappe die Option _Parameter hinzufügen_ aus.
3. Wählen Sie die blaue Schaltfläche _Parameter hinzufügen_ aus.
4. Geben Sie im daraufhin angezeigten Bereich für den neuen Parameter Folgendes ein:
    1. Parametername: `SlowRequestThreshold`
    2. Parametertyp: `Text`
    3. Erforderlich: `checked`
    4. Daten abrufen aus: `None`
5. Wählen Sie auf der Symbolleiste die Option „Speichern“ aus, um den Parameter zu erstellen.

    :::image type="content" source="./media/workbooks-text/text-create.png" alt-text="Screenshot zum Erstellen eines Textparameters":::

So sieht die Arbeitsmappe im Lesemodus aus.

:::image type="content" source="./media/workbooks-text/text-readmode.png" alt-text="Screenshot eines Textparameters im Lesemodus" border="false":::

## <a name="parameter-field-style"></a>Stil für Parameterfeld
Der Textparameter unterstützt folgende Feldstile:

- Standard: Ein einzeiliges Textfeld.

     :::image type="content" source="./media/workbooks-text/standard-text.png" alt-text="Screenshot des Standardtextfelds":::

- Kennwort: Ein einzeiliges Kennwortfeld. Der Kennwortwert wird in der Benutzeroberfläche nur während der Benutzereingabe verborgen. Der Wert ist als Parameterwert bei Referenzierung noch vollständig zugänglich und wird beim Speichern der Arbeitsmappe unverschlüsselt gespeichert.

     :::image type="content" source="./media/workbooks-text/password-text.png" alt-text="Screenshot des Kennwortfelds":::

- Mehrzeilig: Ein mehrzeiliges Textfeld mit Unterstützung für umfassende IntelliSense-Funktionen und farblicher Hervorhebung der Syntax für folgende Sprachen:
    - Text
    - Markdown
    - JSON
    - SQL
    - TypeScript
    - KQL
    - TOML

    Der Benutzer kann auch die Höhe für den mehrzeiligen Editor angeben.

     :::image type="content" source="./media/workbooks-text/kql-text.png" alt-text="Screenshot eines mehrzeiligen Textfelds":::

## <a name="referencing-a-text-parameter"></a>Verweisen auf einen Textparameter
1. Fügen Sie der Arbeitsmappe ein Abfragesteuerelement hin, indem Sie auf den blauen Link `Add query` klicken und eine Application Insights-Ressource auswählen.
2. Fügen Sie im KQL-Feld den folgenden Codeausschnitt hinzu:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Wenn Sie den Textparameter mit dem Wert 500 zusammen mit dem Abfragesteuerelement verwenden, führen Sie effektiv die unten stehende Abfrage aus:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Führen Sie die Abfrage aus, um die Ergebnisse anzuzeigen.

    :::image type="content" source="./media/workbooks-text/text-reference.png" alt-text="Screenshot eines Textparameters, auf den in der KQL verwiesen wird":::

> [!NOTE]
> Im obigen Beispiel stellt `{SlowRequestThreshold}` einen Integerwert dar. Wenn Sie eine Zeichenfolge wie `{ComputerName}` abfragen, müssen Sie die Kusto-Abfrage so ändern, dass Anführungszeichen hinzugefügt werden `"{ComputerName}"`, damit das Parameterfeld eine Eingabe ohne Anführungszeichen akzeptiert.

## <a name="setting-default-values-using-queries"></a>Festlegen von Standardwerten mithilfe von Abfragen
1. Beginnen Sie mit einer leeren Arbeitsmappe im Bearbeitungsmodus.
2. Wählen Sie unter den Links in der Arbeitsmappe die Option _Parameter hinzufügen_ aus.
3. Wählen Sie die blaue Schaltfläche _Parameter hinzufügen_ aus.
4. Geben Sie im daraufhin angezeigten Bereich für den neuen Parameter Folgendes ein:
    1. Parametername: `SlowRequestThreshold`
    2. Parametertyp: `Text`
    3. Erforderlich: `checked`
    4. Daten abrufen aus: `Query`
5. Fügen Sie im KQL-Feld den folgenden Codeausschnitt hinzu:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Mit dieser Abfrage wird der Standardwert des Textfelds auf die Dauer des 95. Perzentils für alle Anforderungen in der App festgelegt.
6. Ausführen einer Abfrage, um die Ergebnisse anzuzeigen
7. Wählen Sie auf der Symbolleiste die Option „Speichern“ aus, um den Parameter zu erstellen.

    :::image type="content" source="./media/workbooks-text/text-default-value.png" alt-text="Screenshot eines Textparameters mit dem Standardwert aus der KQL":::

> [!NOTE]
> In diesem Beispiel werden Application Insights-Daten abgefragt. Der Ansatz kann jedoch für jede auf Protokollen basierende Datenquellen wie Log Analytics, Azure Resource Graph usw. verwendet werden.

## <a name="adding-validations"></a>Hinzufügen von Validierungen 

Für Standard- und Kennworttextparameter kann der Benutzer Validierungsregeln hinzufügen, die auf das Textfeld angewendet werden. Fügen Sie einen gültigen regulären Ausdruck (RegEx) mit einer Fehlermeldung hinzu. Wenn eine Meldung festgelegt ist, wird sie als Fehler angezeigt, wenn das Feld ungültig ist.

Wenn „Übereinstimmung“ ausgewählt ist, ist das Feld gültig, wenn der Wert mit dem regulären Ausdruck übereinstimmt. Ist „Übereinstimmung“ nicht ausgewählt, ist das Feld gültig, wenn es nicht mit dem regulären Ausdruck übereinstimmt.

:::image type="content" source="./media/workbooks-text/validation-settings.png" alt-text="Screenshot der Einstellungen für die Textvalidierung":::

## <a name="format-json-data"></a>Formatieren von JSON-Daten 

Wenn JSON als Sprache für das mehrzeilige Textfeld ausgewählt ist, enthält das Feld eine Schaltfläche zum Formatieren der JSON-Daten des Felds. Der Benutzer kann auch die Tastenkombination `(ctrl + \)` verwenden, um die JSON-Daten zu formatieren.

Wenn Daten aus einer Abfrage stammen, kann der Benutzer die Option zum Vorabformatieren der von der Abfrage zurückgegebenen JSON-Daten auswählen.

:::image type="content" source="./media/workbooks-text/pre-format-json-data.png" alt-text="Screenshot zur Vorabformatierung von JSON-Daten":::

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr](./workbooks-overview.md#visualizations) über die vielen umfassenden Visualisierungsoptionen für Arbeitsmappen.
* [Steuern](./workbooks-access-control.md) Sie den Zugriff auf Ihre Arbeitsmappenressourcen, und geben Sie diese frei.