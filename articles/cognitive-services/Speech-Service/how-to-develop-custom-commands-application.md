---
title: 'Vorgehensweise: Entwickeln von Anwendungen mit benutzerdefinierten Befehlen: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Anwendungen mit benutzerdefinierten Befehlen entwickeln und anpassen können. Diese Sprachbefehls-Apps eignen sich am besten für die Aufgabenerledigung oder Befehl-und-Steuerung-Szenarien (Command-and-Control).
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: eur
ms.openlocfilehash: 33d5d790dca4f52e601cf49cedc038e98edbfe1b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503845"
---
# <a name="develop-custom-commands-applications"></a>Entwickeln von Anwendungen mit benutzerdefinierten Befehlen

In diesem Anleitungsartikel erfahren Sie, wie Anwendungen mit benutzerdefinierten Befehlen entwickelt und konfiguriert werden. Das Feature Benutzerdefinierte Befehle unterstützt Sie beim Erstellen von funktionsreichen Apps mit Sprachbefehlen, die für sprachbetonte Interaktionserfahrungen optimiert sind. Die Funktion eignet sich insbesondere für die Erledigung von Aufgaben oder Befehl-und Steuerungs-Szenarien (Command-and-Control). Dies kommt vor allem IoT-Geräten (Internet of Things, Internet der Dinge) sowie in die Umgebung integrierten und monitorlosen Geräten entgegen.

In diesem Artikel erstellen Sie eine Anwendung, die ein Fernsehgerät ein- und ausschalten, die Temperatur festlegen und einen Alarm einschalten kann. Nach dem Erstellen dieser einfachen Befehle erfahren Sie mehr über die folgenden Optionen für das Anpassen von Befehlen:

* Hinzufügen von Parametern zu Befehlen
* Hinzufügen von Konfigurationen zu Befehlsparametern
* Erstellen von Interaktionsregeln
* Erstellen von Sprachgenerierungsvorlagen für Sprachantworten
* Verwenden von Custom Voice-Tools

## <a name="create-an-application-by-using-simple-commands"></a>Erstellen einer Anwendung mithilfe einfacher Befehle

Beginnen Sie, indem Sie eine leere Anwendung mit benutzerdefinierten Befehlen erstellen. Weitere Informationen finden Sie in der [Schnellstartanleitung](quickstart-custom-commands-application.md). In dieser Anwendung erstellen Sie ein leeres Projekt, anstatt ein Projekt zu importieren.

1. Geben Sie im Feld **Name** den Projektnamen *Smart-Room-Lite* (oder einen anderen Namen Ihrer Wahl) ein.
1. Wählen Sie in der Liste **Sprache** die Option **Englisch (USA)** aus.
1. Wählen Sie eine LUIS-Ressource aus, oder erstellen Sie eine.

   > [!div class="mx-imgBorder"]
   > ![Screenshot des Fensters „Neues Projekt“.](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Aktualisieren von LUIS-Ressourcen (optional)

Sie können die ausgewählte Erstellungsressource im Fenster **Neues Projekt** aktualisieren. Sie können außerdem eine Vorhersageressource festlegen.

Eine Vorhersageressource wird zur Erkennung verwendet, nachdem Ihre Anwendung mit benutzerdefinierten Befehlen veröffentlicht wurde. Während der Entwicklungs- und Testphasen benötigen Sie keine Vorhersageressource.

### <a name="add-a-turnon-command"></a>Hinzufügen eines TurnOn-Befehls

Fügen Sie in der von Ihnen erstellten leeren Smart-Room-Lite-Anwendung mit benutzerdefinierten Befehlen einen Befehl hinzu. Der Befehl verarbeitet eine Äußerung, `Turn on the tv`. Er antwortet mit der Nachricht `Ok, turning the tv on`.

1. Erstellen Sie einen neuen Befehl, indem Sie oben im linken Bereich **Neuer Befehl** auswählen. Das Fenster **Neuer Befehl** wird geöffnet.
1. Geben Sie im Feld **Name** den Wert `TurnOn` ein.
1. Klicken Sie auf **Erstellen**.

Im mittleren Bereich werden die Eigenschaften des Befehls aufgelistet.

In der folgenden Tabelle werden die Konfigurationseigenschaften des Befehls erläutert: Weitere Informationen finden Sie unter [Konzepte und Definitionen für benutzerdefinierte Befehle](./custom-commands-references.md).

| Konfiguration            | BESCHREIBUNG                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Beispielsätze | Beispieläußerungen, mit denen der Benutzer diesen Befehl auslösen kann.                                                                 |
| Parameter       | Informationen, die zur Vervollständigung des Befehls erforderlich sind.                                                                                |
| Vervollständigungsregeln | Aktionen, die zur Erfüllung des Befehls auszuführen sind. Beispiel: dem Benutzer antworten oder mit einem anderen Webdienst kommunizieren. |
| Interaktionsregeln   | Weitere Regeln für den Umgang mit spezifischeren oder komplexeren Situationen.                                                              |


> [!div class="mx-imgBorder"]
> ![Screenshot der zeigt, wo ein Befehl erstellt werden soll.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Hinzufügen von Beispielsätzen

Im Abschnitt **Beispielsätze** geben Sie ein Beispiel für das, was der Benutzer sagen kann.

1. Wählen Sie im mittleren Bereich **Beispielsätze** aus.
1. Fügen Sie im rechten Bereich Beispiele hinzu:

    ```
    Turn on the tv
    ```

1.  Wählen Sie im oberen Bereich der Seite die Option **Speichern** aus.

Derzeit verfügen Sie noch nicht über Parameter, daher können Sie mit dem Abschnitt **Vervollständigungsregeln** fortfahren.

#### <a name="add-a-completion-rule"></a>Hinzufügen einer Vervollständigungsregel

Als nächstes benötigt der Befehl eine Vervollständigungsregel. Diese Regel teilt dem Benutzer mit, dass ein Vervollständigungsvorgang durchgeführt wird.

Weitere Informationen über Regeln und Vervollständigungsregeln finden Sie unter [Konzepte und Definitionen für benutzerdefinierte Befehle](./custom-commands-references.md).

1. Wählen Sie die Standardvervollständigungsregel **Erledigt** aus. Bearbeiten Sie sie anschließend wie folgt:


    | Einstellung    | Vorgeschlagener Wert                          | BESCHREIBUNG                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Name**       | `ConfirmationResponse`                  | Ein Name, der den Zweck der Regel beschreibt.          |
    | **Bedingungen** | Keine                                     | Bedingungen, die bestimmen, wann die Regel ausgeführt werden kann.    |
    | **Aktionen**    | **Sprachantwort senden** > **Einfacher Editor** > `Ok, turning the tv on` | Die durchzuführende Aktion, wenn die Bedingung der Regel erfüllt ist (true). |

   > [!div class="mx-imgBorder"]
   > ![Screenshot der zeigt, wo eine Sprachressource erstellt werden soll.](media/custom-commands/create-speech-response-action.png)

1. Wählen Sie **Speichern** aus, um die Aktion zu speichern.
1. Wählen Sie wieder im Abschnitt **Vervollständigungsregeln** **Speichern** aus, um alle Änderungen zu speichern.

    > [!NOTE]
    > Sie brauchen nicht die Standard-Vervollständigungsregel für den Befehl zu verwenden. Sie können die Standardvervollständigungsregel löschen und eine eigene Regel hinzufügen.

### <a name="add-a-settemperature-command"></a>Hinzufügen eines SetTemperature-Befehls

Fügen Sie nun einen weiteren Befehl hinzu, `SetTemperature`. Dieser Befehl akzeptiert eine einzelne Äußerung, `Set the temperature to 40 degrees`, und antwortet mit der Nachricht `Ok, setting temperature to 40 degrees`.

Zum Erstellen des neuen Befehls folgen Sie den Schritten, die Sie für den `TurnOn`-Befehl ausgeführt haben, verwenden Sie jedoch den Beispielsatz `Set the temperature to 40 degrees`.

Bearbeiten Sie dann die vorhandenen **Erledigt**-Vervollständigungsregeln wie folgt:

| Einstellung    | Vorgeschlagener Wert                          |
| ---------- | ---------------------------------------- |
| **Name**  | `ConfirmationResponse`                  |
| **Bedingungen** | Keine                                     |
| **Aktionen**    | **Sprachantwort senden** > **Einfacher Editor** > **Erste Variation** > `Ok, setting temperature to 40 degrees` |

Wählen Sie **Speichern** aus, um sämtliche Änderungen am Befehl zu speichern.

### <a name="add-a-setalarm-command"></a>Hinzufügen eines SetAlarm-Befehls

Erstellen Sie einen neuen Befehl `SetAlarm`. Verwenden Sie den Beispielsatz `Set an alarm for 9 am tomorrow`. Bearbeiten Sie dann die vorhandenen **Erledigt**-Vervollständigungsregeln wie folgt:

| Einstellung    | Vorgeschlagener Wert                          |
| ---------- | ---------------------------------------- |
| **Name**  | `ConfirmationResponse`                  |
| **Bedingungen** | Keine                                     |
| **Aktionen**    | **Sprachantwort senden** > **Einfacher Editor** > **Erste Variation** > `Ok, setting an alarm for 9 am tomorrow` |

Wählen Sie **Speichern** aus, um sämtliche Änderungen am Befehl zu speichern.

### <a name="try-it-out"></a>Ausprobieren

Testen Sie das Verhalten der Anwendung mithilfe des Testbereichs:

1. Wählen Sie in der oberen rechten Ecke des Bereichs das Symbol **Trainieren** aus.
1. Wenn das Training abgeschlossen ist, wählen Sie **Testen** aus.

Probieren Sie die folgenden Beispieläußerungen aus, indem Sie Sprache oder Text verwenden:

- Eingabe: *Stelle die Temperatur auf 5 Grad ein*
- Erwartete Antwort: OK, stelle die Temperatur auf 5 Grad ein
- Eingabe: *Schalte den Fernseher ein*
- Erwartete Antwort: OK, Fernseher wird eingeschaltet
- Eingabe: *Alarm für morgen 9 Uhr einstellen*
- Erwartete Antwort: OK, lege einen Alarm für morgen 9 Uhr fest

> [!div class="mx-imgBorder"]
> ![Screenshot, der den Test auf einer Webchatoberfläche anzeigt.](media/custom-commands/create-basic-test-chat-no-mic.png)

> [!TIP]
> Im Testbereich können Sie **Rundendetails** auswählen, um Informationen darüber zu erhalten, wie diese Sprach- oder Texteingabe verarbeitet wurde.

## <a name="add-parameters-to-commands"></a>Hinzufügen von Parametern zu Befehlen

In diesem Abschnitt erfahren Sie, wie Sie Ihren Befehlen Parameter hinzufügen. Befehle sind auf Parameter angewiesen, um eine Aufgabe abzuschließen. In komplexen Szenarien können Sie Parameter zur Definition von Bedingungen verwenden, durch die benutzerdefinierte Aktionen ausgelöst werden.

### <a name="configure-parameters-for-a-turnon-command"></a>Konfigurieren von Parametern für einen TurnOn-Befehl

Bearbeiten Sie zunächst den vorhandenen `TurnOn`-Befehl zum Ein- und Ausschalten mehrerer Geräte.

1. Da der Befehl jetzt die Szenarien zum Ein- und Ausschalten behandelt, benennen Sie den Befehl in *TurnOnOff* um.
   1. Wählen Sie den Befehl **TurnOn** im linken Bereich aus. Wählen Sie dann neben dem **Neu-Befehl** oben im Bereich die Schaltfläche „Bearbeiten“ aus.

   1. Ändern Sie im Fenster **Befehl umbenennen** den Namen in *TurnOnOff*.

1. Fügen Sie dem Befehl einen neuen Parameter hinzu. Der Parameter gibt an, ob der Benutzer das Gerät ein- oder ausschalten möchte.
   1. Wählen Sie oben im mittleren Bereich **Hinzufügen** aus. Wählen Sie im Dropdownmenü **Parameter** aus.
   1. Fügen Sie im Bereich auf der rechten Seite im Abschnitt **Parameter** im Feld **Name** `OnOff` hinzu.
   1. Wählen Sie **Erforderlich** aus. Wählen Sie im Fenster **Add response for a required parameter** (Antwort für einen erforderlichen Parameter hinzufügen) **Simple editor** (Einfacher Editor) aus. Fügen Sie im Feld **Erste Variation** *„Ein“ oder „Aus“?* hinzu.
   1. Wählen Sie **Aktualisieren** aus.

       > [!div class="mx-imgBorder"]
       > ![Screenshot: Abschnitt „Add response for a required parameter“ (Antwort für einen erforderlichen Parameter hinzufügen) mit ausgewählter Registerkarte „Simple Editor“ (Einfacher Editor)](media/custom-commands/add-required-on-off-parameter-response.png)

   1. Konfigurieren Sie die Eigenschaften des Parameters mithilfe der folgenden Tabelle. Informationen zu sämtlichen Konfigurationseigenschaften eines Befehls finden Sie unter [Konzepte und Definitionen für benutzerdefinierte Befehle](./custom-commands-references.md).


       | Konfiguration      | Vorgeschlagener Wert     | BESCHREIBUNG                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Name**               | `OnOff`           | Ein aussagekräftiger Name für den Parameter                                                                           |
       | **Erforderlich**           | Aktiviert         | Kontrollkästchen, das angibt, ob ein Wert für diesen Parameter vor dem Abschließen des Befehls erforderlich ist. |
       | **Antwort auf erforderlichen Parameter**      |**Einfacher Editor** > `On or Off?`      | Eine Aufforderung, die nach dem Wert dieses Parameters fragt, wenn er nicht bekannt ist. |
       | **Typ**               | **String**          | Parametertyp, z. B. Zahl, Zeichenfolge, Datum/Uhrzeit oder Geografie.   |
       | **Configuration**      | **Accept predefined input values from an internal catalog** (Vordefinierte Eingabewerte aus einem internen Katalog akzeptieren) | Für Zeichenfolgen schränkt diese Einstellung Eingaben auf eine Reihe möglicher Werte ein. |
       | **Vordefinierte Eingabewerte**     | `on`, `off`           | Menge der möglichen Werte und ihrer Aliase.         |


   1. Zum Hinzufügen vordefinierter Eingabewerte wählen Sie **Vordefinierte Eingabe hinzufügen** aus. Geben Sie im Fenster **Neues Element** *Name* ein, wie in der vorstehenden Tabelle abgebildet. In diesem Fall verwenden Sie keine Aliase, daher können Sie dieses Feld leer lassen.

      > [!div class="mx-imgBorder"]
      > ![Screenshot zum Erstellen eines Parameters.](media/custom-commands/create-on-off-parameter.png)

   1. Wählen Sie **Speichern** aus, um alle Konfigurationen des Parameters zu speichern.

#### <a name="add-a-subjectdevice-parameter"></a>Hinzufügen eines SubjectDevice-Parameters

1. Zum Hinzufügen eines zweiten Parameters, der den Namen der Geräte angibt, die mithilfe dieses Befehls gesteuert werden können, wählen Sie **Hinzufügen** aus. Verwenden Sie die folgende Konfiguration.


    | Einstellung            | Vorgeschlagener Wert       |
    | ------------------ | --------------------- |
    | **Name**               | `SubjectDevice`         |
    | **Erforderlich**           | Aktiviert               |
    | **Antwort auf erforderlichen Parameter**     | **Einfacher Editor** > `Which device do you want to control?`    | 
    | **Typ**               | **String**                |     
    | **Configuration**      | **Accept predefined input values from an internal catalog** (Vordefinierte Eingabewerte aus einem internen Katalog akzeptieren) | 
    | **Vordefinierte Eingabewerte** | `tv`, `fan`               |
    | **Aliase** (`tv`)      | `television`, `telly`     |

1. Wählen Sie **Speichern** aus.

#### <a name="modify-example-sentences"></a>Ändern von Beispielsätzen

Bei Befehlen, die Parameter verwenden, ist es hilfreich, Beispielsätze hinzuzufügen, die alle möglichen Kombinationen abdecken. Beispiel:

* Vollständige Parameterinformationen: `turn {OnOff} the {SubjectDevice}`
* Teilweise Parameterinformationen: `turn it {OnOff}`
* Keine Parameterinformationen: `turn something`

Beispielsätze mit unterschiedlichem Informationsumfang ermöglichen es der Anwendung mit benutzerdefinierten Befehlen, sowohl Lösungen mit Einzeldurchlauf als auch solche mit Mehrfachdurchlauf mithilfe von Teilinformationen aufzulösen.

Bearbeiten Sie vor diesem Hintergrund die Beispielsätze, sodass sie diese vorgeschlagenen Parameter verwenden:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Wählen Sie **Speichern** aus.

> [!TIP]
> Verwenden Sie im Editor für Beispielsätze geschweifte Klammern, um auf Ihre Parameter zu verweisen. Beispiel: `turn {OnOff} the {SubjectDevice}`.
> Verwenden Sie die Tab-Taste für automatische Vervollständigung auf der Grundlage der zuvor erstellten Parameter.

#### <a name="modify-completion-rules-to-include-parameters"></a>Ändern der Vervollständigungsregeln zum Einbeziehen von Parametern

Ändern Sie die vorhandene Vervollständigungsregel `ConfirmationResponse`.

1. Wählen Sie im Abschnitt **Bedingung** die Option **Bedingung hinzufügen** aus.
1. Wählen Sie im Fenster **Neue Bedingung** in der Liste **Typ** die Option **Erforderliche Parameter** aus. Wählen Sie in der anschließenden Liste sowohl **OnOff** als auch **SubjectDevice** aus.
1. Klicken Sie auf **Erstellen**.
1. Bearbeiten Sie im Abschnitt **Aktionen** die Aktion **Sprachantwort senden**, indem Sie mit der Maus darauf zeigen und die Bearbeiten-Schaltfläche auswählen. Verwenden Sie dieses Mal die neu erstellten Parameter `OnOff` und `SubjectDevice`:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Wählen Sie **Speichern** aus.

Testen Sie die Änderungen, indem Sie das Symbol **Trainieren** oben im rechten Bereich auswählen.

Wenn das Training abgeschlossen ist, wählen Sie **Testen** aus. Es wird ein Fenster **Testen Ihrer Anwendung** angezeigt. Probieren Sie die folgenden Interaktionen aus:

- Eingabe: *Schalte Fernseher aus*
- Ausgabe: OK, schalte Fernseher aus
- Eingabe: *Schalte Fernsehgerät aus*
- Ausgabe: OK, schalte Fernseher aus
- Eingabe: *Schalte es aus*
- Ausgabe: Welches Gerät soll gesteuert werden?
- Eingabe: *Der Fernseher*
- Ausgabe: OK, schalte Fernseher aus

### <a name="configure-parameters-for-a-settemperature-command"></a>Konfigurieren von Parametern für einen SetTemperature-Befehl

Ändern Sie den Befehl `SetTemperature` so, dass er die Temperatur nach den Anweisungen des Benutzers einstellt.

Fügen Sie einen `TemperatureValue`-Parameter hinzu. Verwenden Sie die folgende Konfiguration:

| Konfiguration      | Vorgeschlagener Wert     |
| ------------------ | ----------------|
| **Name**               | `TemperatureValue`           |
| **Erforderlich**           | Aktiviert         |
| **Antwort auf erforderlichen Parameter**      | **Einfacher Editor** > `What temperature would you like?`
| **Type**               | `Number`          |


Ändern Sie die Beispieläußerungen so, dass sie die folgenden Werte verwenden.

```
set the temperature to {TemperatureValue} degrees
change the temperature to {TemperatureValue}
set the temperature
change the temperature
```

Bearbeiten Sie die vorhandenen Vervollständigungsregeln. Verwenden Sie die folgende Konfiguration.

| Konfiguration      | Vorgeschlagener Wert     |
| ------------------ | ----------------|
| **Bedingungen**         | **Erforderlicher Parameter** > **TemperatureValue**           |
| **Aktionen**           | **Sprachantwort senden** > `Ok, setting temperature to {TemperatureValue} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Konfigurieren von Parametern für einen SetAlarm-Befehl

Fügen Sie einen Parameter mit dem Namen `DateTime` hinzu. Verwenden Sie die folgende Konfiguration.

   | Einstellung                           | Vorgeschlagener Wert                     |
   | --------------------------------- | ----------------------------------------|
   | **Name**                              | `DateTime`                               |
   | **Erforderlich**                          | Aktiviert                                 |
   | **Antwort auf erforderlichen Parameter**   | **Einfacher Editor** > `For what time?`            |
   | **Typ**                              | **DateTime**                                |
   | **Datumsstandardwerte**                     | Wenn das Datum fehlt, nehmen Sie Heute.            |
   | **Uhrzeitstandardwerte**                     | Verwenden Sie bei fehlender Uhrzeit den Tagesbeginn.     |


> [!NOTE]
> In diesem Artikel werden hauptsächlich die Parametertypen Zeichenfolge, Zahl und DateTime verwendet. Eine Liste aller unterstützten Parametertypen und deren Eigenschaften finden Sie unter [Konzepte und Definitionen für benutzerdefinierte Befehle](./custom-commands-references.md).


Bearbeiten Sie die Beispieläußerungen. Verwenden Sie die nachstehend aufgeführten Werte.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Bearbeiten Sie die vorhandenen Vervollständigungsregeln. Verwenden Sie die folgende Konfiguration.

   | Einstellung    | Vorgeschlagener Wert                               |
   | ---------- | ------------------------------------------------------- |
   | **Aktionen**    | **Sprachantwort senden** > `Ok, alarm set for {DateTime}`  |

Testen Sie die drei Befehle zusammen mit Äußerungen, die sich auf verschiedene Befehle beziehen. (Sie können zwischen den verschiedenen Befehlen wechseln.)

- Eingabe: *Alarm festlegen*
- Ausgabe: Welche Uhrzeit?
- Eingabe: *Schalte den Fernseher ein*
- Ausgabe: OK, Fernseher wird eingeschaltet
- Eingabe: *Alarm festlegen*
- Ausgabe: Welche Uhrzeit?
- Eingabe: *17:00 Uhr*
- Ausgabe: „Ok, alarm set for 2020-05-01 17:00:00“ (OK, Alarm für 01.05.2020 17 Uhr festgelegt)

## <a name="add-configurations-to-command-parameters"></a>Hinzufügen von Konfigurationen zu Befehlsparametern

In diesem Abschnitt erfahren Sie mehr über die erweiterte Parameterkonfiguration, einschließlich:

 - Wie Parameterwerte zu einem Satz gehören können, der außerhalb der Anwendung für benutzerdefinierte Befehle definiert ist.
 - Hinzufügen von Validierungsklauseln zu den Parameterwerten.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Konfigurieren eines Parameters als externe Katalogentität

Das Feature für benutzerdefinierte Befehle ermöglicht es Ihnen, Zeichenfolgenparameter zu konfigurieren, um auf externe Kataloge zu verweisen, die über einen Webendpunkt gehostet werden. Auf diese Weise können Sie den externen Katalog unabhängig aktualisieren, ohne die Anwendung für benutzerdefinierte Befehle bearbeiten zu müssen. Dieser Ansatz ist in Szenarien mit einer großen Anzahl an Katalogeinträgen nützlich.

Verwenden Sie den Parameter `SubjectDevice` aus dem Befehl `TurnOnOff` wieder. Die aktuelle Konfiguration für diesen Parameter lautet **Vordefinierte Eingaben aus dem internen Katalog akzeptieren**. Die Konfiguration bezieht sich auf eine statische Liste der Geräte in der Parameterkonfiguration. Lagern Sie diesen Inhalt in eine externe Datenquelle aus, die unabhängig aktualisiert werden kann.

Beginnen Sie das Auslagern des Inhalts mit dem Hinzufügen eines neuen Webendpunkts. Wechseln Sie im linken Bereich zum Abschnitt **Webendpunkte**. Fügen Sie dort einen neuen Webendpunkt hinzu. Verwenden Sie die folgende Konfiguration.

| Einstellung | Vorgeschlagener Wert |
|----|----|
| **Name** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Methode** | **GET** |


Wenn sich der vorgeschlagene Wert für die URL für Ihre Zwecke nicht eignet, konfigurieren und hosten Sie einen Webendpunkt, der eine JSON-Datei zurückgibt, die aus der Liste der zu steuernden Geräte besteht. Der Webendpunkt sollte eine JSON-Datei im folgenden Format zurückgeben:

```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Wechseln Sie dann zur Seite mit den Parametereinstellungen für **SubjectDevice**. Legen Sie die folgenden Eigenschaften fest.

| Einstellung | Vorgeschlagener Wert |
| ----| ---- |
| **Configuration** | **Akzeptieren vordefinierter Eingaben aus einem externen Katalog** |                               
| **Katalogendpunkt** | `getDevices` |
| **Methode** | **GET** |

Klicken Sie dann auf **Speichern**.

> [!IMPORTANT]
> Es wird keine Option zum Konfigurieren eines Parameters für die Annahme von Eingaben aus einem externen Katalog angezeigt, es sei denn, Sie haben den Webendpunkt im Abschnitt **Webendpunkt** im Bereich auf der linken Seite festgelegt.

Probieren Sie es aus, indem Sie **Trainieren** auswählen. Nachdem das Training abgeschlossen wurde, wählen Sie **Testen** aus, und probieren Sie ein paar Interaktionen.

* Eingabe: *Einschalten*
* Ausgabe: Welches Gerät soll gesteuert werden?
* Eingabe: *Beleuchtung*
* Ausgabe: OK, Beleuchtung wird eingeschaltet

> [!NOTE]
> Sie können jetzt alle auf dem Webendpunkt gehosteten Geräte steuern. Trotzdem müssen Sie die Anwendung trainieren, um die neuen Änderungen zu testen, und die Anwendung dann erneut veröffentlichen.

### <a name="add-validation-to-parameters"></a>Hinzufügen von Validierung zu Parametern

*Validierungen* sind Konstrukte, die sich auf bestimmte Parametertypen beziehen und mit denen Sie Einschränkungen für den Wert von Parametern konfigurieren können. Sie werden zur Eingabe von Korrekturen aufgefordert, wenn die Werte nicht innerhalb der Einschränkungen liegen. Eine Liste der Parametertypen, mit denen das Validierungskonstrukt erweitert wird, finden Sie unter [Konzepte und Definitionen für benutzerdefinierte Befehle](./custom-commands-references.md).

Testen Sie Validierungen mit dem `SetTemperature`-Befehl. Fügen Sie mit den folgenden Schritten eine Validierung für den Parameter `Temperature` hinzu.

1. Wählen Sie im Bereich auf der linken Seite den Befehl **SetTemperature** aus.
1. Wählen Sie im mittleren Bereich **Temperatur** aus.
1. Wählen Sie im Bereich auf der rechten Seite **Validierung hinzufügen** aus.
1. Konfigurieren Sie im Fenster **Neue Validierung** die Validierung, wie in der folgenden Tabelle angegeben. Klicken Sie anschließend auf **Erstellen**.


    | Parameterkonfiguration | Vorgeschlagener Wert | BESCHREIBUNG |
    | ---- | ---- | ---- |
    | **Mindestwert** | `60` | Bei Zahlenparameter der Minimalwert, den der Parameter annehmen kann |
    | **Höchstwert** | `80` | Bei Zahlenparameter der Maximalwert, den der Parameter annehmen kann |
    | **Fehlerantwort** |  **Einfacher Editor** > **Erste Variation** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Aufforderung zur Anforderung eines neuen Werts, wenn ein Validierungsfehler auftritt |

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Hinzufügen einer Bereichsvalidierung](media/custom-commands/add-validations-temperature.png)

Testen Sie dies, indem Sie das Symbol **Trainieren** oben im rechten Bereich auswählen. Nachdem das Training abgeschlossen ist, wählen Sie **Testen** aus. Versuchen Sie, einige Interaktionen auszuführen:

- Eingabe: *Stelle die Temperatur auf 22 Grad ein*
- Ausgabe: OK, stelle die Temperatur auf 22 Grad ein
- Eingabe: *Stelle die Temperatur auf 7 Grad ein*
- Ausgabe: Es kann nur eine Temperatur zwischen 15 und 27 Grad eingestellt werden.
- Eingabe: *Stelle stattdessen 22 Grad ein*
- Ausgabe: OK, stelle die Temperatur auf 22 Grad ein

## <a name="add-interaction-rules"></a>Hinzufügen von Interaktionsregeln

Interaktionsregeln sind *zusätzliche* Regeln zum Behandeln spezifischer oder komplexer Situationen. Es steht Ihnen zwar frei, Ihre eigenen Interaktionsregeln zu erstellen, in diesem Beispiel verwenden Sie jedoch Interaktionsregeln für die folgenden Szenarien:

* Bestätigen von Befehlen
* Hinzufügen einer einstufigen Korrektur zu Befehlen

Weitere Informationen über Interaktionsregeln finden Sie unter [Konzepte und Definitionen für benutzerdefinierte Befehle](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Hinzufügen von Bestätigungen zu einem Befehl

Zum Hinzufügen einer Bestätigung verwenden Sie den `SetTemperature`-Befehl. Um eine Bestätigung zu erhalten, erstellen Sie mit den folgenden Schritten Interaktionsregeln:

1. Wählen Sie im Bereich auf der linken Seite den Befehl **SetTemperature** aus.
1. Fügen Sie im mittleren Bereich Interaktionsregeln hinzu, indem Sie **Hinzufügen** auswählen. Wählen Sie dann **Interaktionsregeln** > **Befehl bestätigen** aus.

    Durch diese Aktion werden drei Interaktionsregeln hinzugefügt. Mit der Regel wird der Benutzer aufgefordert, das Datum und die Uhrzeit des Alarms zu bestätigen. Es wird eine Bestätigung (Ja oder Nein) für den nächsten Durchgang erwartet.

    1. Ändern Sie die Interaktionsregel **Befehl bestätigen** mithilfe der folgenden Konfiguration:
        1. Ändern Sie den Namen in **Temperatur bestätigen**.
        1. Die Bedingung **Alle erforderlichen Parameter** wurde bereits hinzugefügt.
        1. Fügen Sie eine neue Aktion hinzu: **Typ** > **Sprachantwort senden** > **Möchten Sie die Temperatur wirklich auf {TemperatureValue} Grad festlegen?**
        1. Behalten Sie im Abschnitt **Erwartungen** den Standardwert **Bestätigung vom Benutzer erwarten** bei.

         > [!div class="mx-imgBorder"]
         > ![Screenshot, der das Erstellen der erforderlichen Parameterantwort zeigt.](media/custom-speech-commands/add-confirmation-set-temperature.png)


    1. Ändern Sie die Interaktionsregel **Bestätigung erfolgreich**, um eine erfolgreiche Bestätigung zu behandeln (der Benutzer hat mit „Ja“ geantwortet).

          1. Ändern Sie den Namen in **Temperaturbestätigung erfolgreich**.
          1. Belassen Sie die bestehende Bedingung **Bestätigung war erfolgreich**.
          1. Fügen Sie eine neue Bedingung hinzu: **Typ** > **Erforderliche Parameter** > **TemperatureValue**.
          1. Belassen Sie den Standardwert **Status nach der Ausführung** als **Vervollständigungsregeln ausführen**.

    1. Ändern Sie die Interaktionsregel **Bestätigung verweigert** so, dass sie Szenarien verarbeitet, in denen die Bestätigung verweigert wird (der Benutzer hat mit „Nein“ geantwortet).

          1. Ändern Sie den Namen in **Temperaturbestätigung verweigert**.
          1. Belassen Sie die bestehende Bedingung **Bestätigung wurde verweigert**.
          1. Fügen Sie eine neue Bedingung hinzu: **Typ** > **Erforderliche Parameter** > **TemperatureValue**.
          1. Fügen Sie eine neue Aktion hinzu: **Typ** > **Sprachantwort senden** > **Kein Problem. Welche Temperatur dann?** .
          1. Ändern Sie den Standardwert **Status nach der Ausführung** als **Auf Benutzereingabe warten**.

> [!IMPORTANT]
> In diesem Artikel verwenden Sie die integrierte Bestätigungsfunktion. Sie können Interaktionsregeln auch einzeln manuell hinzufügen.

Testen Sie die Änderungen, indem Sie **Trainieren** auswählen. Wenn das Training abgeschlossen ist, wählen Sie **Testen** aus.

- **Eingabe**: *Stelle die Temperatur auf 80 Grad ein*.
- **Ausgabe**: Sind Sie sicher, dass Sie die Temperatur auf 80 Grad einstellen möchten?
- **Eingabe**: *Nein*
- **Ausgabe**: Kein Problem. Welche Temperatur dann?
- **Eingabe**: *72 Grad*
- **Ausgabe**: Sind Sie sicher, dass Sie die Temperatur auf 72 Grad einstellen möchten?
- **Eingabe**: *Ja*
- **Ausgabe**: OK, die Temperatur wird auf 72 Grad eingestellt.

### <a name="implement-corrections-in-a-command"></a>Implementieren von Korrekturen in einem Befehl

In diesem Abschnitt konfigurieren Sie eine Korrektur in einem Schritt. Diese Korrektur wird nach dem Ausführen des Vervollständigungsvorgangs angewendet. Es wird auch ein Beispiel dafür bereitgestellt, wie die Korrektur standardmäßig aktiviert wird, falls der Befehl noch nicht erfüllt ist. Um eine Korrektur hinzuzufügen, wenn der Befehl nicht abgeschlossen ist, fügen Sie den neuen `AlarmTone`-Parameter hinzu.

Wählen Sie im linken Bereich den Befehl **SetAlarm** aus. Fügen Sie dann den neuen Parameter **AlarmTone** hinzu.

- **Name** > `AlarmTone`
- **Typ** > **Zeichenfolge**
- **Standardwert** > **Chimes**
- **Konfiguration** > **Vordefinierte Eingabewerte aus dem internen Katalog akzeptieren**
- **Vordefinierte Eingabewerte** > **Chimes**, **Jingle** und **Echo** (Diese Werte stellen einzelne, vordefinierte Eingaben dar).


Aktualisieren Sie als Nächstes die Antwort für den **DateTime**-Parameter in **Bereit zum Festlegen des Alarms mit dem Ton {AlarmTone}. Welche Uhrzeit?** Ändern Sie dann die Vervollständigungsregel wie folgt:

1. Wählen Sie die vorhandene Vervollständigungsregel aus, **ConfirmationResponse**.
1. Zeigen Sie im Bereich auf der rechten Seite auf die vorhandene Aktion, und wählen Sie **Bearbeiten** aus.
1. Aktualisieren Sie die Sprachantwort zu `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}`.

> [!IMPORTANT]
> Der Alarmton kann ohne explizite Konfiguration in einem laufenden Befehl geändert werden. Er kann beispielsweise geändert werden, wenn der Befehl noch nicht abgeschlossen ist. Eine Korrektur ist *standardmäßig* für alle Befehlsparameter aktiviert, unabhängig von der Rundennummer, wenn der Befehl noch nicht erfüllt ist.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implementieren einer Korrektur, wenn ein Befehl abgeschlossen ist

Die Plattform für benutzerdefinierte Befehle ermöglicht eine Korrektur in einem Schritt auch nach dem Abschluss eines Befehls. Dieses Feature ist nicht standardmäßig aktiviert. Er muss explizit konfiguriert werden.

Verwenden Sie die folgenden Schritte, um eine Korrektur in einem Schritt zu konfigurieren:

1. Fügen Sie im Befehl **SetAlarm** eine Interaktionsregel vom Typ **Vorherigen Befehl aktualisieren** hinzu, um den zuvor festgelegten Alarm zu aktualisieren. Benennen Sie die Interaktionsregel in **Vorherigen Alarm aktualisieren** um.
1. Behalten Sie die Standardbedingung bei: **Vorheriger Befehl muss aktualisiert werden**.
1. Fügen Sie eine neue Bedingung hinzu: **Typ** > **Erforderlicher Parameter** > **DateTime**.
1. Fügen Sie eine neue Aktion hinzu: **Typ** > **Sprachantwort senden** > **Einfacher Editor** > **Vorheriger Alarm wird auf {DateTime} aktualisiert**.
1. Behalten Sie den Standardwert für den Status **Nach der Ausführung** als **Befehl abgeschlossen** bei.

Testen Sie die Änderungen, indem Sie **Trainieren** auswählen. Warten Sie auf den Abschluss des Trainings, und wählen Sie dann **Testen** aus.

- **Eingabe**: *Alarm festlegen*.
- **Ausgabe**: Bereit zum Festlegen des Alarms mit dem Ton „Glockentöne“. Welche Uhrzeit?
- **Eingabe**: *Alarm mit Ton „Jingle“ für morgen 9 Uhr festlegen*.
- **Ausgabe**: OK, Alarm für 21.05.2020 9 Uhr festgelegt. Der Alarmton ist „Jingle“.
- **Eingabe**: *Nein, 8 Uhr*.
- **Ausgabe**: Vorheriger Alarm wird auf 21.05.2020 8 Uhr aktualisiert

> [!NOTE]
> In einer realen Anwendung müssen Sie im Abschnitt **Aktionen** dieser Korrekturregel auch eine Aktivität an den Client zurücksenden oder einen HTTP-Endpunkt aufrufen, um die Alarmzeit in Ihrem System zu aktualisieren. Diese Aktion sollte nur für die Aktualisierung der Alarmzeit zuständig sein. Sie sollte nicht für andere Attribute des Befehls verwendet werden. In diesem Fall ist das betreffende Attribut der Alarmton.

## <a name="add-language-generation-templates-for-speech-responses"></a>Hinzufügen von Sprachgenerierungsvorlagen für Sprachantworten

Sprachgenerierungsvorlagen (LG-Vorlagen) ermöglichen es Ihnen, die an den Client gesendeten Antworten benutzerdefiniert anzupassen. Sie führen Abweichungen in die Antworten ein. Sprachgenerierung lässt sich mit diesen Mitteln erreichen:

* Sprachgenerierungsvorlagen
* Adaptive Ausdrücke

Vorlagen für benutzerdefinierte Befehle basieren auf den [LG-Vorlagen](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) des Bot Frameworks. Da das Feature Benutzerdefinierte Befehle bei Bedarf eine neue LG-Vorlage erstellt (für Sprachantworten in Parametern oder Aktionen), müssen Sie den Namen der LG-Vorlage nicht angeben.

Daher müssen Sie Ihre Vorlage nicht wie folgt definieren:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Stattdessen können Sie den Text der Vorlage nur wie folgt ohne den Namen definieren:

> [!div class="mx-imgBorder"]
> ![Screenshot eines Beispiels für den Vorlagen-Editor.](./media/custom-commands/template-editor-example.png)


Diese Änderung führt eine Variation in die Sprachantworten ein, die an den Client gesendet werden. Für eine Äußerung wird die entsprechende Sprachantwort nach dem Zufallsprinzip aus den bereitgestellten Optionen ausgewählt.

Die Nutzung von LG-Vorlagen ermöglicht es Ihnen außerdem, komplexe Sprachantworten für Befehle mithilfe adaptiver Ausdrücke zu definieren. Weitere Informationen finden Sie unter [LG-Vorlagenformat](/azure/bot-service/file-format/bot-builder-lg-file-format#templates).

Standardmäßig unterstützt das Feature Benutzerdefinierte Befehle alle Funktionen mit den folgenden geringfügigen Unterschieden:

* In den LG-Vorlagen werden Entitäten als `${entityName}` dargestellt. Das Feature Benutzerdefinierte Befehle verwendet keine Entitäten. Jedoch können Sie Parameter als Variablen verwenden, wahlweise in der Darstellung `${parameterName}` oder der Darstellung `{parameterName}`.
* Das Feature Benutzerdefinierte Befehle unterstützt keine Zusammenstellung und Erweiterung von Vorlagen, da die *LG*-Datei niemals direkt bearbeitet wird. Sie bearbeiten lediglich die Antworten automatisch erstellter Vorlagen.
* Das Feature Benutzerdefinierte Befehle unterstützt keine von LG injizierten benutzerdefinierten Funktionen. Vordefinierte Funktionen werden unterstützt.
* Das Feature Benutzerdefinierte Befehle unterstützt keine Optionen, wie `strict`, `replaceNull` und `lineBreakStyle`.

### <a name="add-template-responses-to-a-turnonoff-command"></a>Hinzufügen von Vorlagenressourcen zum Befehl „TurnOnOff“

Ändern Sie den Befehl `TurnOnOff`, um einen neuen Parameter hinzuzufügen. Verwenden Sie die folgende Konfiguration.

| Einstellung            | Vorgeschlagener Wert       |
| ------------------ | --------------------- |
| **Name**               | `SubjectContext`         |
| **Erforderlich**           | Nicht markiert               |
| **Typ**               | **String**                |
| **Standardwert**      | `all` |
| **Configuration**      | **Vordefinierte Eingabewerte aus einem internen Katalog akzeptieren** |
| **Vordefinierte Eingabewerte** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Ändern einer Vervollständigungsregel

Bearbeiten Sie den Abschnitt **Aktionen** der vorhandenen Vervollständigungsregel **ConfirmationResponse**. Wechseln Sie im Fenster **Aktion bearbeiten** zum **Vorlagen-Editor**. Ersetzen Sie dann den Text durch das folgende Beispiel.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Trainieren und testen Sie Ihre Anwendung mit der folgenden Eingabe und Ausgabe. Beachten Sie die Variation der Antworten. Die Variation wird durch mehrere Alternativen des Vorlagenwerts und auch durch die Verwendung adaptiver Ausdrücke erzeugt.

* Eingabe: *Schalte Fernseher ein*
* Ausgabe: OK, Fernseher wird eingeschaltet
* Eingabe: *Schalte Fernseher ein*
* Ausgabe: Erledigt, Fernseher ist eingeschaltet
* Eingabe: *Schalte das Licht aus*
* Ausgabe: OK, gesamte Beleuchtung wird ausgeschaltet
* Eingabe: *Schalte Raumbeleuchtung aus*
* Ausgabe: OK, die Raumbeleuchtung wird ausgeschaltet

## <a name="use-a-custom-voice"></a>Verwenden einer benutzerdefinierten Stimme

Eine weitere Möglichkeit zum Anpassen von Antworten auf benutzerdefinierte Befehle ist die Auswahl einer Ausgabestimme. Verwenden Sie die folgenden Schritte, um von der Standardstimme zu einer benutzerdefinierten Stimme zu wechseln:

1. Wählen Sie in Ihrer Anwendung für benutzerdefinierte Befehle im Bereich auf der linken Seite **Einstellungen** aus.
1. Wählen Sie im mittleren Bereich **Custom Voice** aus.
1. Wählen Sie in der Tabelle eine benutzerdefinierte Stimme oder eine öffentliche Stimme aus.
1. Wählen Sie **Speichern** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot von Beispielsätzen und Parametern.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Bei den öffentlichen Stimmen sind neuronale Typen nur für bestimmte Regionen verfügbar. Weitere Informationen finden Sie unter [Vom Speech-Dienst unterstützte Regionen](./regions.md#neural-and-standard-voices).
>
> Sie können benutzerdefinierte Stimmen auf der Projektseite **Custom Voice** erstellen. Weitere Informationen finden Sie unter [Erste Schritte mit Custom Voice](./how-to-custom-voice.md).

Die Anwendung antwortet nun in der ausgewählten Stimme statt in der Standardstimme.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie mithilfe des Speech SDKs Ihre [Anwendung für benutzerdefinierte Befehle in eine Client-App integrieren](how-to-custom-commands-setup-speech-sdk.md) können.
* [Richten Sie Continuous Deployment](how-to-custom-commands-deploy-cicd.md) für Ihre Anwendung für benutzerdefinierte Befehle mithilfe von Azure DevOps ein.
