---
title: Erstellen eines Sprach-Assistenten ohne Code in Azure Percept Studio
description: Es wird beschrieben, wie Sie ohne Code eine Sprachlösung erstellen und für Ihr Azure Percept DK bereitstellen.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f4c8544c768f3eaf783450acfe27e4a3d42fc833
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059830"
---
# <a name="create-a-no-code-voice-assistant-in-azure-percept-studio"></a>Erstellen eines Sprach-Assistenten ohne Code in Azure Percept Studio

In diesem Tutorial erstellen Sie einen Sprach-Assistenten aus einer Vorlage, den Sie für Ihr Azure Percept DK und für Azure Percept-Audio verwenden können. Die Demo für den Sprach-Assistenten wird in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) ausgeführt und enthält eine Auswahl virtueller Objekte, die per Sprache gesteuert werden. Sagen Sie Ihr Schlüsselwort, um ein Objekt zu steuern. Beim Schlüsselwort handelt es sich um ein Wort oder einen kurzen Ausdruck, mit dem Sie Ihr Gerät aufwecken, gefolgt von einem Befehl. Jede Vorlage reagiert auf verschiedene spezifische Befehle.

In diesem Leitfaden wird Schritt für Schritt der Prozess zum Einrichten Ihrer Geräte, Erstellen eines Sprach-Assistenten und der benötigten Ressourcen für [Speech-Dienste](../cognitive-services/speech-service/overview.md), Testen des Sprach-Assistenten, Konfigurieren Ihres Schlüsselworts und Erstellen von benutzerdefinierten Schlüsselwörtern beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

- Azure Percept DK (DevKit)
- Azure Percept-Audio
- Lautsprecher oder Kopfhörer mit 3,5-mm-Stecker (optional)
- [Azure-Abonnement](https://azure.microsoft.com/free/)
- [Azure Percept DK-Setup:](./quickstart-percept-dk-set-up.md) Sie haben Ihr DevKit mit einem WLAN verbunden, eine IoT Hub-Instanz erstellt und das DevKit mit der IoT Hub-Instanz verbunden.
- [Einrichten von Azure Percept-Audio](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Erstellen eines Sprach-Assistenten mit einer verfügbaren Vorlage

1. Navigieren Sie zu [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Öffnen Sie die Registerkarte **Demos und Tutorials**.

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Screenshot: Startseite des Azure-Portals":::

1. Klicken Sie unter **Sprachtutorials und -demos** auf **Sprach-Assistenten-Vorlagen ausprobieren**. Auf der rechten Seite des Bildschirms wird ein Fenster geöffnet.

1. Gehen Sie im Fenster wie folgt vor:

    1. Wählen Sie im Dropdownmenü **IoT Hub** den IoT-Hub aus, mit dem Ihr DevKit verbunden ist.

    1. Wählen Sie im Dropdownmenü **Gerät** Ihr DevKit aus.

    1. Wählen Sie eine der verfügbaren Sprach-Assistenten-Vorlagen aus.

    1. Klicken Sie auf das Kontrollkästchen **Ich stimme den Bestimmungen für dieses Projekt zu**.

    1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Screenshot: Erstellung einer Vorlage für den Sprach-Assistenten":::

1. Nach dem Klicken auf **Erstellen** wird im Portal ein neues Fenster geöffnet, in dem Sie Ihre Sprachdesignressource erstellen können. Gehen Sie im Fenster wie folgt vor:

    1. Wählen Sie im Feld **Abonnement** Ihr Azure-Abonnement aus.

    1. Wählen Sie im Dropdownmenü **Ressourcengruppe** Ihre bevorzugte Ressourcengruppe aus. Klicken Sie unter dem Dropdownmenü auf **Erstellen**, und befolgen Sie die Aufforderungen, wenn Sie eine neue Ressourcengruppe für Ihren Sprach-Assistenten erstellen möchten.

    1. Geben Sie unter **Anwendungspräfix** einen Namen ein. Dies ist das Präfix für Ihr Projekt und den Namen Ihres benutzerdefinierten Befehls.

    1. Wählen Sie unter **Region** die Region aus, in der Ressourcen bereitgestellt werden sollen.

    1. Wählen Sie unter **Tarif für LUIS-Vorhersagen** die Option **Standard** aus (im Free-Tarif werden Sprachanforderungen nicht unterstützt).

    1. Klicken Sie auf die Schaltfläche **Erstellen**. Ressourcen für die Sprach-Assistenten-Anwendung werden unter Ihrem Abonnement bereitgestellt.

        > [!WARNING]
        > Schließen Sie das Fenster **NICHT**, bevor die Bereitstellung der Ressource im Portal abgeschlossen ist. Das vorzeitige Schließen des Fensters kann für den Sprach-Assistenten zu unerwartetem Verhalten führen. Nachdem Ihre Ressource bereitgestellt wurde, wird die Demo angezeigt.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Screenshot: Fenster zum Auswählen von Abonnement und Ressourcengruppe":::

## <a name="test-out-your-voice-assistant"></a>Testen Ihres Sprach-Assistenten

Sagen Sie zum Interagieren mit Ihrem Sprach-Assistenten das Schlüsselwort, und nennen Sie dann einen Befehl. Wenn das akustische SoM Ihr Schlüsselwort erkennt, gibt das Gerät einen Glockenton aus (den Sie hören, wenn Lautsprecher oder Kopfhörer angeschlossen sind), und die LEDs blinken blau. Die LEDs blinken schnell nacheinander blau auf, während Ihr Befehl verarbeitet wird. Die Antwort des Sprach-Assistenten auf Ihren Befehl wird in Textform im Demofenster und als Ton über Ihre Lautsprecher bzw. Kopfhörer ausgegeben. Als Standardschlüsselwort (neben **Benutzerdefiniertes Schlüsselwort**) ist „Computer“ festgelegt. Jede Vorlage verfügt über verschiedene kompatible Befehle, mit denen Sie mit virtuellen Objekten im Demofenster interagieren können. Wenn Sie beispielsweise die Demo für das Hotel- und Gaststättengewerbe oder das Gesundheitswesen verwenden möchten, sagen Sie „Computer, turn on TV“ (Computer, Fernseher einschalten), um den virtuellen Fernseher einzuschalten.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Screenshot: Fenster mit der Demo für das Hotel- und Gaststättengewerbe":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Demo für Hotel- und Gaststättengewerbe und Gesundheitswesen: Befehle

Die Demos für „Gesundheitswesen“ und für „Hotel- und Gaststättengewerbe“ verfügen jeweils über virtuelle Fernseher, Beleuchtung, Jalousien und Thermostate, mit denen Sie interagieren können. Die folgenden Befehle (und zusätzliche Varianten) werden unterstützt:

* „Turn on/off the lights.“ (Licht ein-/ausschalten.)
* „Turn on/off the TV.“ (Fernseher ein-/ausschalten.)
* „Turn on/off the AC.“ (Klimaanlage ein-/ausschalten.)
* „Open/close the blinds.“ (Jalousien öffnen/schließen.)
* „Set temperature to X degrees.“ (Temperatur auf X Grad einstellen.) (X ist die gewünschte Temperatur, z. B. 75 Grad Fahrenheit/24 Grad Celsius.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Screenshot: Fenster mit der Demo für das Gesundheitswesen":::

### <a name="automotive-demo-commands"></a>Demo für Automobilindustrie: Befehle

Die Demo für „Automobilindustrie“ umfasst eine Sitzheizung, eine Scheibenheizung und einen Thermostat als virtuelle Elemente, mit denen Sie interagieren können. Die folgenden Befehle (und zusätzliche Varianten) werden unterstützt:

* „Turn on/off the defroster.“ (Scheibenheizung ein-/ausschalten.)
* „Turn on/off the seat warmer.“ (Sitzheizung ein-/ausschalten.)
* „Set temperature to X degrees.“ (Temperatur auf X Grad einstellen.) (X ist die gewünschte Temperatur, z. B. 75 Grad Fahrenheit/24 Grad Celsius.)
* „Increase/decrease the temperature by Y degrees.“ (Temperatur um Y Grad erhöhen/verringern.)


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Screenshot: Fenster mit der Demo für die Automobilindustrie":::

### <a name="inventory-demo-commands"></a>Demo für Bestandsverwaltung: Befehle

Die Demo für die Bestandsverwaltung verfügt über verschiedene virtuelle Kisten in blauer, gelber und grüner Farbe, mit denen Sie über eine App für die virtuelle Bestandsverwaltung interagieren können. Die folgenden Befehle (und zusätzliche Varianten) werden unterstützt:

* „Add/remove X boxes.“ (X Kisten hinzufügen/entfernen.) (X ist die Anzahl von Kisten, z. B. 4.)
* „Order/ship X boxes.“ (X Kisten bestellen/versenden.)
* „How many boxes are in stock?“ (Wie viele Kisten sind auf Lager?)
* „Count Y boxes.“ (Y Kisten zählen.) (Y ist die Farbe der Kisten, z. B. Gelb.)
* „Ship everything in stock.“ (Gesamten Bestand versenden.)


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Screenshot: Fenster mit der Demo für die Bestandsverwaltung":::

## <a name="configure-your-keyword"></a>Konfigurieren des Schlüsselworts

Sie können das Schlüsselwort für Ihren Sprach-Assistenten anpassen.

1. Klicken Sie im Demofenster neben **Benutzerdefiniertes Schlüsselwort** auf **Ändern**.

1. Wählen Sie eins der verfügbaren Schlüsselwörter aus. Zur Auswahl stehen verschiedene vordefinierte Schlüsselwörter sowie die von Ihnen erstellten benutzerdefinierten Schlüsselwörter.

1. Klicken Sie auf **Speichern**.

### <a name="create-a-custom-keyword"></a>Erstellen eines benutzerdefinierten Schlüsselworts

Sie können ein eigenes Schlüsselwort für Ihre Sprachanwendung erstellen. Das Trainieren für Ihr benutzerdefiniertes Schlüsselwort dauert nur wenige Minuten.

1. Klicken Sie im oberen Bereich des Demofensters auf **+ Benutzerdefiniertes Schlüsselwort erstellen**. 

1. Geben Sie das gewünschte Schlüsselwort ein. Hierbei kann es sich um ein einzelnes Wort oder um einen kurzen Ausdruck handeln.

1. Wählen Sie Ihre **Speech-Ressource** aus (wird im Demofenster neben **Benutzerdefinierter Befehl** angezeigt und enthält Ihr Anwendungspräfix).

1. Klicken Sie auf **Speichern**. 

## <a name="create-a-custom-command"></a>Erstellen eines benutzerdefinierten Befehls

Das Portal enthält auch Funktionen zum Erstellen von benutzerdefinierten Befehlen mit vorhandenen Sprachressourcen. Der Begriff „Benutzerdefinierter Befehl“ bezieht sich auf den Sprach-Assistenten selbst, und nicht auf einen bestimmten Befehl in der vorhandenen Anwendung. Mit der Erstellung eines benutzerdefinierten Befehls erstellen Sie auch ein neues Sprachprojekt, das Sie dann in [Speech Studio](https://speech.microsoft.com/) weiterentwickeln müssen.

Klicken Sie zum Erstellen eines neuen benutzerdefinierten Befehls im Demofenster oben auf der Seite auf **+ Benutzerdefinierten Befehl erstellen**, und gehen Sie dann wie folgt vor:

1. Geben Sie einen Namen für Ihren benutzerdefinierten Befehl ein.

1. Geben Sie eine Beschreibung Ihres Projekts ein (optional).

1. Wählen Sie Ihre bevorzugte Sprache aus.

1. Wählen Sie Ihre Sprachressource aus.

1. Wählen Sie Ihre LUIS-Ressource aus.

1. Wählen Sie Ihre LUIS-Erstellungsressource aus, oder erstellen Sie eine neue.

1. Klicken Sie auf **Erstellen**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Screenshot: Fenster für die Erstellung benutzerdefinierter Befehle":::

Nachdem Sie einen benutzerdefinierten Befehl erstellt haben, müssen Sie für die weitere Entwicklung zu [Speech Studio](https://speech.microsoft.com/) wechseln. Führen Sie die folgenden Schritte aus, falls Sie Speech Studio öffnen und Ihr benutzerdefinierter Befehl nicht angezeigt wird:

1. Klicken Sie in Azure Percept Studio im linken Bereich unter **KI-Projekte** auf **Sprache**.

1. Wählen Sie die Registerkarte **Befehle** aus.

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Screenshot: Liste mit benutzerdefinierten Befehlen, die bearbeitet werden können":::

1. Wählen Sie den benutzerdefinierten Befehl aus, den Sie weiterentwickeln möchten. Das Projekt wird in Speech Studio geöffnet.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Screenshot: Startseite von Speech Studio":::

Weitere Informationen zur Entwicklung von benutzerdefinierten Befehlen finden Sie in der [Dokumentation zum Speech-Dienst](../cognitive-services/speech-service/custom-commands.md).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>Sprach-Assistent wurde erstellt, reagiert aber nicht auf Befehle

Überprüfen Sie die LEDs der Interposer-Platine:

* Mit drei dauerhaft blau leuchtenden LEDs wird angezeigt, dass der Sprach-Assistent bereit ist und auf das Schlüsselwort wartet.
* Wenn die mittlere LED (L02) weiß leuchtet, wurde die Initialisierung vom DevKit abgeschlossen, und die Konfiguration mit einem Schlüsselwort ist erforderlich.
* Wenn die mittlere LED (L02) weiß blinkt, ist die Initialisierung des Audio-SOM noch nicht abgeschlossen. Der Initialisierungsvorgang kann einige Minuten dauern.

Weitere Informationen zu den LED-Anzeigen finden Sie im [Artikel zu den LEDs](./audio-button-led-behavior.md).

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Sprach-Assistent reagiert nicht auf ein benutzerdefiniertes Schlüsselwort, das in Speech Studio erstellt wurde

Dies kann passieren, wenn das Sprachmodul veraltet ist. Führen Sie die folgenden Schritte aus, um das Sprachmodul auf die neueste Version zu aktualisieren:

1. Klicken Sie auf der Startseite von Azure Percept Studio im Menü auf der linken Seite auf **Geräte**.

1. Suchen Sie nach Ihrem Gerät, und wählen Sie es aus.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Screenshot: Geräteliste in Azure Percept Studio":::

1. Wählen Sie im Gerätefenster die Registerkarte **Sprache** aus.

1. Überprüfen Sie die Version des Sprachmoduls. Wenn ein Update verfügbar ist, wird neben der Versionsnummer die Schaltfläche **Update** angezeigt.

1. Klicken Sie auf **Update**, um das Update für das Sprachmodul bereitzustellen. Es dauert normalerweise zwei bis drei Minuten, bis der Updatevorgang abgeschlossen ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit der Arbeit an Ihrer Sprach-Assistenten-Anwendung fertig sind, ist es ratsam, die im Rahmen dieses Tutorials bereitgestellten Sprachressourcen mit den folgenden Schritten zu bereinigen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü auf der linken Seite die Option **Ressourcengruppen** aus, oder geben Sie ihren Namen in der Suchleiste ein.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Screenshot: Startseite des Azure-Portals mit linkem Menübereich und Ressourcengruppen":::

1. Wählen Sie Ihre Ressourcengruppe aus.

1. Wählen Sie alle sechs Ressourcen aus, die Ihr Anwendungspräfix enthalten, und klicken Sie im oberen Menübereich auf das Symbol **Löschen**.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Screenshot: Zum Löschen ausgewählte Speech-Ressourcen":::

1. Geben Sie zum Bestätigen des Löschvorgangs im entsprechenden Feld **Ja** ein, vergewissern Sie sich, dass Sie die richtigen Ressourcen ausgewählt haben, und klicken Sie auf **Löschen**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Screenshot: Bestätigungsfenster für den Löschvorgang":::

> [!WARNING]
> Hierbei werden alle benutzerdefinierten Schlüsselwörter entfernt, die mit den von Ihnen gelöschten Sprachressourcen erstellt wurden, und die Demo für den Sprach-Assistenten funktioniert nicht mehr.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun ohne Code eine Sprachlösung erstellt haben, können Sie versuchen, eine [Vision-Lösung ohne Code](./tutorial-nocode-vision.md) für Ihr Azure Percept DK zu erstellen.
