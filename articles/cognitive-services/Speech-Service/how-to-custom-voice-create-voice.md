---
title: Erstellen einer benutzerdefinierten Stimme – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Wenn Sie zum Hochladen Ihrer Daten bereit sind, navigieren Sie zum Custom Voice-Portal. Erstellen Sie ein Custom Voice-Projekt, oder wählen Sie ein bereits vorhandenes Projekt aus. Das Projekt muss die gleiche Sprache/das gleiche Gebietsschema und die gleichen Geschlechtseigenschaften besitzen wie die Daten, die Sie für Ihr Stimmtraining verwenden möchten.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: eur
ms.openlocfilehash: 16f9d66b669e792d0bccc2676bdc1db327de3229
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507206"
---
# <a name="create-and-use-your-voice-model"></a>Erstellen und Verwenden Ihres Sprachmodells

Unter [Vorbereiten von Trainingsdaten](how-to-custom-voice-prepare-data.md) haben Sie mehr über die verschiedenen Datentypen erfahren, die Sie zum Trainieren einer benutzerdefinierten neuronalen Stimme und die verschiedenen Formatanforderungen verwenden können. Nachdem Sie Ihre Daten und die aufgezeichnete Erklärung des Sprechers vorbereitet haben, können Sie sie in [Speech Studio](https://aka.ms/custom-voice-portal) hochladen. In diesem Artikel erfahren Sie, wie Sie eine benutzerdefinierte neuronale Stimme über das Speech Studio-Portal trainieren. Hier finden Sie Informationen zu den [unterstützten Sprachen](language-support.md#customization) für die benutzerdefinierte neuronale Stimme.

## <a name="prerequisites"></a>Voraussetzungen

* Schließen Sie [Erste Schritte mit „Benutzerdefinierte neuronale Stimme“](how-to-custom-voice.md) ab.
* [Vorbereiten von Trainingsdaten](how-to-custom-voice-prepare-data.md)

## <a name="set-up-voice-talent"></a>Einrichten eines Sprechers

Ein Sprecher ist eine Einzelperson oder Zielsprecher, dessen Stimmen aufgezeichnet und verwendet werden, um neuronale Stimmmodelle zu erstellen. Bevor Sie eine Stimme erstellen, definieren Sie Ihre Stimmpersona, und wählen Sie einen geeigneten Sprecher aus. Weitere Informationen zum Aufzeichnen von Stimmbeispielen finden Sie im [Tutorial](record-custom-voice-samples.md).

Um eine neuronale Stimme zu trainieren, müssen Sie ein Sprecherprofil mit einer Audiodatei erstellen, die vom Sprecher aufgezeichnet wurde, der der Verwendung seiner Sprachdaten zum Trainieren eines benutzerdefinierten Stimmmodells zugestimmt hat. Stellen Sie beim Vorbereiten des Aufzeichnungsskripts sicher, dass Sie den folgenden Satz einschließen:

**„Ich [Vor- und Nachname nennen] akzeptiere, dass die Aufzeichnungen meiner Stimme von [Name des Unternehmens nennen] verwendet werden, um eine synthetische Version meiner Stimme zu erstellen und diese zu verwenden.“**
Dieser Satz wird verwendet, um zu überprüfen, ob die Trainingsdaten mit den Audiodaten in der Einverständniserklärung übereinstimmen. > Weitere Informationen zur [Überprüfung des Sprechers](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)

> [!NOTE]
> Das Feature „Benutzerdefinierte neuronale Stimme“ ist mit eingeschränktem Zugriff verfügbar. Stellen Sie sicher, dass Sie die [Anforderungen für verantwortungsvolle KI](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) kennen, und [beantragen Sie dann den Zugriff](https://aka.ms/customneural). 

Bei den folgenden Schritten wird davon ausgegangen, dass Sie die Dateien für die aufgezeichnete Erklärung der Sprecher vorbereitet haben.  Wechseln Sie zu [Speech Studio](https://aka.ms/custom-voice-portal), um ein Projekt für eine benutzerdefinierte neuronale Stimme auszuwählen, und führen Sie dann die folgenden Schritte aus, um ein Sprecherprofil zu erstellen.

1. Navigieren Sie zu **Sprachsynthese** > **Custom Voice** > **Projekt auswählen** > **Sprecher einrichten**.

2. Wählen Sie **Sprecher hinzufügen** aus.

3. Wählen Sie als Nächstes zum Definieren von Stimmmerkmalen das zu verwendende **Zielszenario** aus. Beschreiben Sie dann Ihre **Stimmmerkmale**.

> [!NOTE]
> Die von Ihnen bereitgestellten Szenarien müssen mit dem übereinstimmen, was Sie im Antragsformular beantragt haben.

4. Wechseln Sie dann zu **Upload voice talent statement** (Sprecheraussage hochladen), und befolgen Sie die Anweisungen zum Hochladen der Sprecheraussage, die Sie zuvor vorbereitet haben.

> [!NOTE]
> Stellen Sie sicher, dass die mündliche Aussage mit denselben Einstellungen wie Ihre Trainingsdaten aufgezeichnet wird, einschließlich der Aufzeichnungsumgebung und der Sprechweise.

5. Wechseln Sie abschließend zu **Überprüfen und erstellen**. Sie können die Einstellungen überprüfen und **Übermitteln** auswählen.

## <a name="upload-your-data"></a>Hochladen Ihrer Daten

Wenn Sie bereit sind, Ihre Daten hochzuladen, wechseln Sie zur Registerkarte zum **Vorbereiten von Trainingsdaten**, um Ihren ersten Trainingssatz hinzuzufügen und Daten hochzuladen.  Ein Trainingssatz besteht aus einer Reihe von Audioäußerungen und ihren Zuordnungsskripts, die zum Trainieren eines Stimmmodells verwendet werden. Sie können einen Trainingssatz verwenden, um Ihre Trainingsdaten zu organisieren. Die Überprüfung der Datenbereitschaft erfolgt pro Trainingssatz. Sie können mehrere Daten in einen Trainingssatz importieren.

Gehen Sie wie folgt vor, um Ihre Trainingsdaten zu erstellen und zu überprüfen.

1. Wählen Sie auf der Registerkarte **Trainingsdaten vorbereiten** die Option **Trainingssatz hinzufügen** zum Eingeben von **Name** und **Beschreibung** > **Erstellen** zum Hinzufügen eines neuen Trainingssatzes aus.

   Wenn der Trainingssatz erfolgreich erstellt wurde, können Sie mit dem Hochladen Ihrer Daten beginnen. 

2. Wählen Sie zum Hochladen von Daten **Daten hochladen** > **Datentyp auswählen** > **Daten hochladen** und **Zieltrainingssatz angeben** aus. Geben Sie **Name** und **Beschreibung** für Ihre Daten ein. Überprüfen Sie die Einstellungen, und wählen Sie **Übermitteln** aus.

> [!NOTE]
>- Doppelte Audionamen werden aus dem Training entfernt. Achten Sie darauf, dass die ausgewählten Daten in der ZIP-Datei oder in mehreren ZIP-Dateien nicht die gleichen Audionamen enthalten. Wenn Äußerungs-IDs (entweder in Audio- oder Skriptdateien) doppelt vorhanden sind, werden sie abgelehnt.
>- Wenn Sie Datendateien in der vorherigen Version von Speech Studio erstellt haben, müssen Sie einen Trainingssatz für Ihre Daten angeben, bevor Sie sie verwenden. Andernfalls wird dem Datennamen ein Ausrufezeichen angefügt, und die Daten könnten nicht verwendet werden.

Alle Daten, die Sie hochladen, müssen den Anforderungen für den ausgewählten Datentyp entsprechen. Es ist wichtig, dass Sie Ihre Daten ordnungsgemäß formatieren, bevor sie hochgeladen werden. Dadurch wird sichergestellt, dass die Daten vom Dienst „Benutzerdefinierte neuronale Stimme“ verarbeitet werden. Wechseln Sie zu [Trainingsdaten vorbereiten](how-to-custom-voice-prepare-data.md), und stellen Sie sicher, dass Ihre Daten ordnungsgemäß formatiert sind.

> [!NOTE]
> - Benutzer mit einem Standardabonnement (S0) können fünf Datendateien gleichzeitig hochladen. Wenn Sie das Limit erreichen, warten Sie, bis der Import mindestens einer Datendatei beendet ist. Versuchen Sie es anschließend noch mal.
> - Benutzer des kostenlosen Abonnements (F0) können maximal 10 ZIP-Dateien pro Abonnement als Datendateien importieren. Bei Benutzern des Standardabonnements (S0) beträgt die Höchstzahl 500.

Datendateien werden nach dem Klicken auf die Schaltfläche **Übermitteln** automatisch überprüft. Bei der Datenüberprüfung werden Format, Größe und Samplingrate der Audiodateien geprüft. Sollten Fehler vorliegen, beheben Sie sie, und übermitteln Sie die Daten erneut. 

Nachdem die Daten hochgeladen wurden, können Sie die Details in der Detailansicht des Trainingssatzes überprüfen. Auf der Registerkarte **Übersicht** können Sie die Aussprachebewertungen und den Rauschpegel Ihrer jeweiligen Daten genauer überprüfen. Die Aussprachebewertung liegt zwischen 0 und 100. Werte unter 70 deuten in der Regel auf einen Aussprachefehler oder auf einen Fehler bei der Skriptzuordnung hin. Ein starker Akzent kann die Aussprachebewertung verringern und die Qualität der erzeugten digitalen Stimme negativ beeinflussen.

Ein höheres Signal-Rausch-Verhältnis (SNR) deutet auf ein geringeres Rauschen in den Audioaufnahmen hin. Wenn Ihre Aufnahmen in einem professionellen Tonstudio vorgenommen wurden, können Sie SNR-Werte größer als 50 erzielen. Ein SNR-Wert unter 20 kann dazu führen, dass in der generierten Stimme das Rauschen deutlich zu hören ist.

Bei einer niedrigen Aussprachebewertung oder einem geringen SNR-Wert sollten Sie die betroffenen Äußerungen erneut aufnehmen. Wenn die erneute Aufzeichnung nicht möglich ist, erwägen Sie den Ausschluss dieser Äußerungen aus Ihren Daten.

Unter **Datendetails** können Sie die Datendetails des Trainingssatzes überprüfen. Befolgen Sie bei typischen Problemen mit den Daten die Anweisungen in der angezeigten Meldung, um sie vor dem Training zu beheben.

Die Probleme sind in drei Typen unterteilt. Beziehen Sie sich auf die folgenden drei Tabellen, um die jeweiligen Fehlertypen zu überprüfen.

Beheben Sie die erste Art von Fehlern, die in der folgenden Tabelle aufgeführt sind, manuell. Andernfalls werden die Daten mit diesen Fehlern während des Trainings ausgeschlossen.

| Category | Name | BESCHREIBUNG |
| --------- | ----------- | --------------------------- |
| Skript | Ungültiges Trennzeichen| Sie müssen die Äußerungs-ID und den Skriptinhalt durch ein TAB-Zeichen trennen.|
| Skript | Ungültige Skript-ID| Die Skriptzeilen-ID muss numerisch sein.|
| Skript | Dupliziertes Skript|Jede Zeile des Skriptinhalts muss eindeutig sein. Die Zeile wird mit {} dupliziert.|
| Skript | Skript zu lang| Das Skript darf 1.000 Zeichen nicht überschreiten.|
| Skript | Keine Audioübereinstimmung| Die ID jeder Äußerung (jeder Zeile der Skriptdatei) muss mit der Audio-ID übereinstimmen.|
| Skript | Kein gültiges Skript| In diesem Dataset wurde kein gültiges Skript gefunden. Korrigieren Sie die Skriptzeilen, die in der detaillierten Problemliste angezeigt werden.|
| Audio | Keine Skriptübereinstimmung| Keine Audiodatei stimmt mit der Skript-ID überein. Der Name der WAV-Dateien muss mit den IDs in der Skriptdatei übereinstimmen.|
| Audio | Ungültiges Audioformat| Das Audioformat der WAV-Dateien ist ungültig. Überprüfen Sie das WAV-Dateiformat mithilfe eines Audiotools wie [SoX](http://sox.sourceforge.net/).|
| Audio | Niedrige Samplingrate| Die Samplingrate der WAV-Dateien darf nicht unter 16 KHz liegen.|
| Audio | Zu lange Audioinhalte| Die Audiodauer ist länger als 30 Sekunden. Teilen Sie den langen Audioinhalt in mehrere Dateien auf. Äußerungen sollten kürzer als 15 Sekunden sein.|
| Audio | Keine gültigen Audiodaten| In diesem Dataset wurden keine gültigen Audiodaten gefunden. Überprüfen Sie Ihre Audiodaten, und laden Sie sie erneut hoch.|

Die zweite Art von Fehlern, die in der folgenden Tabelle aufgeführt sind, wird automatisch behoben, aber es wird empfohlen, die korrigierten Daten zu überprüfen.

| Category | Name | BESCHREIBUNG |
| --------- | ----------- | --------------------------- |
| Audio | Stereoaudio automatisch korrigiert | Verwenden Sie Mono in Ihren Audiobeispielaufzeichnungen. Stereoaudiokanäle werden automatisch in einem Monokanal zusammengeführt, was zu Inhaltsverlusten führen kann.  Laden Sie das normalisierte Dataset herunter, und überprüfen Sie es.|
| Lautstärke | Lautstärkespitze automatisch korrigiert |Die Lautstärkespitze sollte zwischen -3 dB (70 % der maximalen Lautstärke) und -6 dB (50 %) liegen. Steuern Sie die Lautstärkespitze während der Beispielaufzeichnung oder Datenvorbereitung. Diese Audiodaten werden automatisch gemäß dem Spitzenbereich (-4 dB oder 65 %) linear skaliert. Laden Sie das normalisierte Dataset herunter, und überprüfen Sie es.|
|Konflikt | Stille automatisch korrigiert| Es wurde erkannt, dass die Stille zu Beginn länger als 200 ms dauert, und sie wurde automatisch auf 200 ms gekürzt. Laden Sie das normalisierte Dataset herunter, und überprüfen Sie es. |
| Konflikt |Stille automatisch korrigiert | Es wurde erkannt, dass die Stille am Ende länger als 200 ms dauert, und sie wurde automatisch auf 200 ms gekürzt. Laden Sie das normalisierte Dataset herunter, und überprüfen Sie es. |
| Konflikt |Stille automatisch korrigiert |Es wurde erkannt, dass die Stille zu Beginn weniger als 100 ms dauert, und sie wurde automatisch auf 100 ms erweitert. Laden Sie das normalisierte Dataset herunter, und überprüfen Sie es. |
| Konflikt |Stille automatisch korrigiert | Es wurde erkannt, dass die Stille am Ende weniger als 100 ms dauert, und sie wurde automatisch auf 100 ms erweitert. Laden Sie das normalisierte Dataset herunter, und überprüfen Sie es.|

Wenn die dritte Art von Fehlern, die in der folgenden Tabelle aufgeführt sind, nicht behoben wird, obwohl die Daten mit diesen Fehlern während des Trainings nicht ausgeschlossen werden, wirkt sich dies auf die Qualität des Trainings aus. Für ein qualitativ hochwertigeres Training wird empfohlen, diese Fehler manuell zu beheben. 

| Category | Name | BESCHREIBUNG |
| --------- | ----------- | --------------------------- |
| Skript | Nicht normalisierter Text|Dieses Skript enthält Ziffern von 0-9. Erweitern Sie sie zu normalisierten Wörtern, und gleichen Sie sie mit den Audiodaten ab. Beispiel: Normalisieren Sie „123“ zu „einhundertdreiundzwanzig“.|
| Skript | Nicht normalisierter Text|Dieses Skript enthält Symbole {}. Normalisieren Sie die Symbole so, dass sie mit den Audiodaten übereinstimmen. Beispiel: „50 %“ in „fünfzig Prozent“.|
| Skript | Zu wenige Frageäußerungen| Mindestens 10 % der gesamten Äußerungen sollten Fragesätze sein. Dies hilft dem Stimmmodell, eine richtige Fragenbetonung auszudrücken.|
| Skript |Zu wenige Ausrufeäußerungen| Mindestens 10 % der gesamten Äußerungen sollten Ausrufesätze sein. Dies hilft dem Stimmmodell, einen aufgeregten Ton richtig auszudrücken.|
| Audio| Niedrige Samplingrate für neuronale Stimme | Sie sollten für ihre WAV-Dateien eine Samplingrate von mindestens 24 KHz verwenden, um neuronale Stimmen zu erstellen. Wenn sie niedriger ist, wird sie automatisch auf 24 KHz upgesampelt.|
| Volume |Gesamtlautstärke zu niedrig|Die Lautstärke sollte nicht unter -18 dB (10 % der maximalen Lautstärke) liegen. Halten Sie den durchschnittlichen Lautstärkepegel während der Beispielaufzeichnung oder Datenvorbereitung im richtigen Bereich.|
| Lautstärke | Lautstärkeüberlauf| Ein Lautstärkeüberlauf wird unter {}s erkannt. Passen Sie die Aufzeichnungsgeräte an, um einen Lautstärkeüberlauf beim Spitzenwert zu vermeiden.|
| Lautstärke | Problem mit der Stille zu Beginn | Die ersten 100 ms Stille sind nicht rein. Reduzieren Sie den Rauschpegel der Aufzeichnung, und lassen Sie die ersten 100 ms zu Beginn still.|
| Lautstärke| Problem mit der Stille am Ende| Die letzten 100 ms Stille sind nicht rein.  Reduzieren Sie den Rauschpegel der Aufzeichnung, und lassen Sie die letzten 100 ms am Ende still.|
| Konflikt | Niedrige Bewertung von Wörtern|Überprüfen Sie Skript- und Audioinhalte, um sicherzustellen, dass sie übereinstimmen, und steuern Sie den Rauschpegel. Reduzieren Sie die Dauer der langen Stille, oder teilen Sie die Audiodaten in mehrere Äußerungen auf, wenn sie zu lang ist.|
| Konflikt | Problem mit der Stille zu Beginn |Zusätzliche Audioinhalte waren vor dem ersten Wort zu hören. Überprüfen Sie Skript- und Audioinhalte, um sicherzustellen, dass sie übereinstimmen, steuern Sie den Rauschpegel, und sorgen Sie für die ersten 100 ms Stille.|
| Konflikt | Problem mit der Stille am Ende| Zusätzliche Audioinhalte waren nach dem letzten Wort zu hören. Überprüfen Sie Skript- und Audioinhalte, um sicherzustellen, dass sie übereinstimmen, steuern Sie den Rauschpegel, und sorgen Sie für die letzten 100 ms Stille.|
| Konflikt | Niedriges Signal-Rausch-Verhältnis | Der Pegel des Signal-Rausch-Verhältnisses ist niedriger als 20 dB. Es werden mindestens 35 dB empfohlen.|
| Konflikt | Keine Bewertung verfügbar |Fehler beim Erkennen von Sprachinhalten in diesem Audio. Überprüfen Sie Audio- und Skriptinhalte, um sicherzustellen, dass die Audiodaten gültig sind und mit dem Skript übereinstimmen.|

## <a name="train-your-custom-neural-voice-model"></a>Trainieren Ihres benutzerdefinierten neuronalen Stimmmodells

Nach der Überprüfung Ihrer Datendateien können Sie mit ihnen Ihr benutzerdefiniertes neuronales Stimmmodell erstellen.

1. Wählen Sie auf der Registerkarte **Modell trainieren** die Option **Modell trainieren** aus, um ein Stimmmodell mit den hochgeladenen Daten zu erstellen.

2. Wählen Sie die neuronale Trainingsmethode für Ihr Modell und Ihre Zielsprache aus.

Standardmäßig wird Ihr Stimmmodell in derselben Sprache wie Ihre Trainingsdaten trainiert. Sie können auch eine sekundäre Sprache (Vorschauversion) für Ihr Stimmmodell erstellen.  Überprüfen Sie die Sprachen, die für benutzerdefinierte neuronale Stimmen und sprachübergreifende Features unterstützt werden: [Sprache für die Anpassung](language-support.md#customization).

Das Training von benutzerdefinierten neuronalen Stimmen ist nicht kostenlos. Ausführliche Informationen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Wenn Sie jedoch vor dem 31.03.2021 statistisch-parametrische oder verkettete Stimmmodelle mit S0 Speech-Ressourcen bereitgestellt haben, werden für Ihr Azure-Abonnement kostenlose neuronale Trainingsguthaben angeboten, und Sie können fünf verschiedene Versionen neuronaler Stimmen kostenlos trainieren.

3. Wählen Sie als Nächstes die Daten aus, die Sie für das Training verwenden möchten, und geben Sie eine Sprecherdatei an.

>[!NOTE]
>- Sie müssen mindestens 300 Äußerungen auswählen, um eine benutzerdefinierte neuronale Stimme zu erstellen.
>- Um eine neuronale Stimme zu trainieren, müssen Sie ein Sprecherprofil mit der Audiozustimmungsdatei des Sprechers angeben, in der dieser der Verwendung seiner Sprachdaten zum Trainieren eines benutzerdefinierten Sprachmodells zustimmt. Das Feature „Benutzerdefinierte neuronale Stimme“ ist mit eingeschränktem Zugriff verfügbar. Stellen Sie sicher, dass Sie die [Anforderungen für verantwortungsvolle KI](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) kennen, und [fordern Sie hier den Zugriff an](https://aka.ms/customneural).

4. Wählen Sie dann Ihr Testskript aus.

Bei jedem Training werden 100 Beispielaudiodateien automatisch generiert, um Sie beim Testen des Modells mit einem Standardskript zu unterstützen. Sie können auch optional ein eigenes Testskript bereitstellen. Das Testskript muss die Dateinamen (die ID jeder Äußerung) ausschließen, andernfalls werden diese IDs gesprochen. Das folgende Beispiel zeigt, wie die Äußerungen in einer TXT-Datei strukturiert sind:

```
This is the waistline, and it's falling.
We have trouble scoring.
It was Janet Maslin.
```

Jeder Absatz der Äußerung ergibt eine separate Audiodatei. Wenn Sie alle Sätze in einer Audiodatei kombinieren möchten, fügen Sie sie zu einem einzigen Absatz zusammen.

>[!NOTE]
>- Das Testmanuskript muss eine TXT-Datei von höchstens 1 MB sein. Unterstützte Codierungsformat sind z. B. ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE oder UTF-16-BE.  
>- Die generierten Audiodateien sind eine Kombination aus dem hochgeladenen Testskript und dem Standardtestskript.

5. Geben Sie unter **Name** einen Namen und unter **Beschreibung** eine Beschreibung ein, um dieses Modell identifizieren zu können.

Wählen Sie den Namen sorgfältig aus. Der hier eingegebene Name ist der Name, der zur Festlegung der Stimme in der Sprachsyntheseanforderung als Teil der SSML-Eingabe verwendet wird. Zulässig sind nur Buchstaben, Zahlen und einige wenige Satzzeichen wie -, _ und (', '). Verwenden Sie unterschiedliche Namen für unterschiedliche neuronale Stimmmodelle.

Im Feld **Beschreibung** werden in der Regel die Namen der Daten erfasst, die zur Erstellung des Modells verwendet wurden.

6. Überprüfen Sie die Einstellungen, und wählen Sie dann **Übermitteln** aus, um mit dem Trainieren des Modells zu beginnen.

> [!NOTE]
> Doppelte Audionamen werden aus dem Training entfernt. Achten Sie darauf, dass die ausgewählten Daten nicht in mehreren ZIP-Dateien die gleichen Audionamen enthalten.

In der Tabelle **Modell trainieren** wird ein neuer Eintrag angezeigt, der diesem neu erstellten Modell entspricht. Außerdem zeigt die Tabelle den Status an: „Verarbeitung“, „Erfolgreich“ oder „Fehler“.

Der angezeigte Status gibt Aufschluss über die Konvertierung Ihrer Daten in ein Stimmmodell (wie hier gezeigt).

| State | Bedeutung |
| ----- | ------- |
| Verarbeitung | Ihr Stimmmodell wird erstellt. |
| Erfolgreich | Ihr Stimmmodell wurde erstellt und kann bereitgestellt werden. |
| Fehler | Beim Trainieren Ihres Stimmmodells ist ein Fehler aufgetreten. Dies kann verschiedene Ursachen haben (beispielsweise ein unvorhergesehenes Datenproblem oder ein Netzwerkproblem). |

Die Trainingsdauer hängt davon ab, wie viele Daten Sie trainieren. Das Trainieren einer benutzerdefinierten neuronalen Stimme dauert im Durchschnitt etwa 40 Computestunden. 

> [!NOTE]
> Benutzer mit einem Standard-Abonnement (S0) können drei Stimmen gleichzeitig trainieren. Wenn das Limit erreicht wurde, warten Sie, bis der Trainingsvorgang mindestens eines Stimmmodells beendet wurde, und versuchen Sie es dann noch mal. 

7. Nachdem Sie das Training des Modells erfolgreich abgeschlossen haben, können Sie die Modelldetails überprüfen.

Nach der erfolgreichen Erstellung des Stimmmodells können Sie die generierten Beispielaudiodateien verwenden, um es vor der Bereitstellung zur Nutzung zu testen.

Die Qualität der Stimme hängt von vielen Faktoren ab, z. B. der Größe des Trainingsdatasets, der Qualität der Aufzeichnung, der Genauigkeit der Transkriptionsdatei sowie davon, wie gut die in den Trainingsdaten aufgezeichnete Stimme zu der Persönlichkeit der für Ihren vorgesehenen Anwendungsfall entworfenen Stimme passt. [Weitere Informationen zu den Funktionen und Beschränkungen dieser Technologie sowie bewährte Methoden zur Verbesserung der Modellqualität](/legal/cognitive-services/speech-service/custom-neural-voice/characteristics-and-limitations-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) 

## <a name="create-and-use-a-custom-neural-voice-endpoint"></a>Erstellen und Verwenden eines Endpunkts für eine benutzerdefinierte neuronale Stimme

Nach dem erfolgreichen Erstellen und Testen des Sprachmodells können Sie es in einem benutzerdefinierten Text-to-Speech-Endpunkt bereitstellen. Diesen verwenden Sie anschließend anstelle des üblichen Endpunkts beim Senden von Text-to-Speech-Anforderungen über die REST-API. Der benutzerdefinierte Endpunkt kann nur von dem Abonnement aufgerufen werden, mit dem Sie das Modell bereitgestellt haben.

Gehen Sie wie folgt vor, um einen benutzerdefinierten Endpunkt für eine benutzerdefinierte neuronale Stimme zu erstellen.

1. Wählen Sie auf der Registerkarte **Modell bereitstellen** die Option **Modell bereitstellen** aus. 
2. Geben Sie als nächstes einen **Namen** und eine **Beschreibung** für Ihren benutzerdefinierten Endpunkt ein.
3. Wählen Sie ein Stimmmodell aus, das Sie diesem Endpunkt zuordnen möchten. 
4. Wählen Sie abschließend **Bereitstellen** aus, um Ihren Endpunkt zu erstellen.

Nach dem Klicken auf die Schaltfläche **Bereitstellen** wird in der Endpunkttabelle ein Eintrag für Ihren neuen Endpunkt angezeigt. Das Instanziieren des neuen Endpunkts kann einige Minuten in Anspruch nehmen. Wenn als Bereitstellungsstatus **Erfolgreich** angezeigt wird, kann der Endpunkt verwendet werden.

Sie können Ihren Endpunkt **aussetzen** und **fortsetzen**, wenn Sie ihn nicht immer verwenden. Wenn ein Endpunkt nach dem Aussetzen wieder aktiviert wird, bleibt die Endpunkt-URL unverändert, sodass Sie den Code in Ihren Apps nicht anpassen müssen. 

Sie können den Endpunkt aber auch auf ein neues Modell aktualisieren. Stellen Sie zum Ändern des Modells sicher, dass das neue Modell den gleichen Namen hat wie dasjenige, das Sie aktualisieren möchten. 

> [!NOTE]
>- Benutzer mit einem Standard-Abonnement (S0) können bis zu 50 Endpunkte mit jeweils einer eigenen benutzerdefinierten neuronalen Stimme bereitstellen.
>- Wenn Sie Ihre benutzerdefinierte neuronale Stimme verwenden möchten, müssen Sie den Namen des Stimmmodells angeben sowie den benutzerdefinierten URI direkt in einer HTTP-Anforderung verwenden. Außerdem müssen Sie das gleiche Abonnement verwenden, um die Authentifizierung des TTS-Diensts zu durchlaufen.

Nachdem Sie Ihren Endpunkt bereitgestellt haben, wird sein Name als Link angezeigt. Klicken Sie auf den Link, um spezifische Informationen zu Ihrem Endpunkt anzuzeigen (z. B. Endpunktschlüssel, Endpunkt-URL und Beispielcode).

Der benutzerdefinierte Endpunkt verfügt über dieselben Funktionen wie der Standardendpunkt für Text-to-Speech-Anforderungen.  Weitere Informationen finden Sie unter [Speech SDK](./get-started-text-to-speech.md) oder [REST-API](rest-text-to-speech.md).

Wir stellen auch ein Onlinetool, [Audioinhaltserstellung](https://speech.microsoft.com/audiocontentcreation), zur Verfügung, mit dem Sie die Audioausgabe über eine benutzerfreundliche Benutzeroberfläche optimieren können.

## <a name="next-steps"></a>Nächste Schritte

- [Aufnehmen von Sprachbeispielen](record-custom-voice-samples.md)
- [Text-to-Speech-REST-API](rest-text-to-speech.md)
- [API für lange Audioinhalte](long-audio-api.md)
