---
title: Erstellen einer Vision-Lösung ohne Code in Azure Percept Studio
description: Es wird beschrieben, wie Sie in Azure Percept Studio eine Vision-Lösung ohne Code erstellen und in Ihrem Azure Percept DK bereitstellen.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: a8d1ce721c1bd67895661be25ac0bab3c230839b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227193"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Erstellen einer Vision-Lösung ohne Code in Azure Percept Studio

Mit Azure Percept Studio können Sie benutzerdefinierte Lösungen für maschinelles Sehen (Computer Vision) entwickeln und bereitstellen, ohne Code schreiben zu müssen. In diesem Artikel werden die folgenden Themen behandelt:

- Erstellen eines Vision-Projekts in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Erfassen von Trainingsbildern mit Ihrem DevKit
- Versehen Ihrer Trainingsbilder mit einer Bezeichnung in [Custom Vision](https://www.customvision.ai/)
- Trainieren Ihres benutzerdefinierten Modells für die Objekterkennung oder Klassifizierung
- Bereitstellen Ihres Modells für Ihr DevKit
- Verbessern Ihres Modells durch Einrichten des erneuten Trainings

Dieses Tutorial ist für Entwickler geeignet, die über wenig oder gar keine KI-Erfahrung verfügen bzw. die gerade mit der Verwendung von Azure Percept beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure Percept DK (DevKit)
- [Azure-Abonnement](https://azure.microsoft.com/free/)
- Azure Percept DK-Setup: Sie haben Ihr DevKit mit einem WLAN verbunden, eine IoT Hub-Instanz erstellt und das DevKit mit der IoT Hub-Instanz verbunden.

## <a name="create-a-vision-prototype"></a>Erstellen eines Prototyps für das Vision-Projekt

1. Starten Sie Ihren Browser, und navigieren Sie zu [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klicken Sie auf der Übersichtsseite auf die Registerkarte **Demos und Tutorials**. :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Übersichtsbildschirm von Azure Percept Studio" lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. Klicken Sie unter **Vision tutorials and demos** (Vision-Tutorials und -Demos) auf **Create a vision prototype** (Vision-Prototyp erstellen).

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Bildschirm mit Demos und Tutorials in Azure Percept Studio" lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Gehen Sie auf der Seite **New Azure Percept Custom Vision prototype** (Neuer Azure Percept Custom Vision-Prototyp) wie folgt vor:

    1. Geben Sie im Feld **Projektname** einen Namen für Ihren Vision-Prototyp ein.

    1. Geben Sie im Feld **Projektbeschreibung** eine Beschreibung des Vision-Prototyps ein.

    1. Wählen Sie im Dropdownmenü **Gerätetyp** die Option **Azure Percept DK** aus.

    1. Wählen Sie im Dropdownmenü **Ressource** eine Ressource aus, oder klicken Sie auf **Neue Ressource erstellen**. Führen Sie im Fenster **Erstellen** Folgendes durch, falls Sie eine neue Ressource erstellen möchten:
        1. Geben Sie einen Namen für die neue Ressource ein.
        1. Wählen Sie Ihr Azure-Abonnement aus.
        1. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue.
        1. Wählen Sie Ihre bevorzugte Region aus.
        1. Wählen Sie Ihren Tarif aus (Empfehlung: S0).
        1. Klicken Sie unten im Fenster auf **Erstellen**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Fenster „Ressource erstellen“":::

    1. Wählen Sie unter **Projekttyp** aus, ob im Rahmen Ihres Vision-Projekts die Objekterkennung oder die Bildklassifizierung durchgeführt werden soll. Weitere Informationen zu den Projekttypen erhalten Sie, wenn Sie auf **Entscheidungshilfe** klicken.

    1. Wählen Sie unter **Optimierung** aus, ob Ihr Projekt in Bezug auf Genauigkeit, geringe Netzwerklatenz oder (soweit möglich) beides optimiert werden soll.

    1. Klicken Sie auf die Schaltfläche **Erstellen** .

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Bildschirm zum Erstellen eines benutzerdefinierten Vision-Prototyps":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Verbinden eines Geräts mit Ihrem Projekt und Erfassen von Bildern

Nach dem Erstellen einer Vision-Lösung müssen Sie dieser Ihr DevKit und die zugehörige IoT Hub-Instanz hinzufügen.

1. Schalten Sie Ihr DevKit ein.

1. Wählen Sie im Dropdownmenü **IoT Hub** die IoT Hub-Instanz aus, mit der Sie Ihr DevKit während des OOBE-Vorgangs verbunden haben.

1. Wählen Sie im Dropdownmenü **Geräte** Ihr DevKit aus.

Als Nächstes müssen Sie Bilder für das Training Ihres KI-Modells entweder laden oder erfassen. Wir empfehlen Ihnen, mindestens 30 Bilder pro Tagtyp hochzuladen. Wenn Sie beispielsweise ein Erkennungsmodul für Hunde und Katzen entwickeln möchten, müssen Sie mindestens 30 Bilder von Hunden und 30 Bilder von Katzen hochladen. Gehen Sie wie folgt vor, um mit dem Vision-SoM Ihres DevKit Bilder zu erfassen:

1. Wählen Sie im Fenster **Image capture** (Bilderfassung) die Option **View device stream** (Gerätestream anzeigen) aus, um den Videodatenstrom des Vision-SoM anzuzeigen.

1. Überprüfen Sie den Videodatenstrom, um sicherzustellen, dass die Kamera Ihres Vision-SoM für die Aufnahme der Trainingsbilder richtig ausgerichtet ist. Nehmen Sie bei Bedarf entsprechende Anpassungen vor.

1. Klicken Sie im Fenster **Image capture** (Bilderfassung) auf **Take photo** (Foto aufnehmen).

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Bildschirm für Bilderfassung":::

1. Alternativ können Sie auch eine automatisierte Bilderfassung einrichten, um gleichzeitig eine große Zahl von Bildern zu erfassen. Aktivieren Sie hierfür das Kontrollkästchen **Automatic image capture** (Automatische Bilderfassung). Wählen Sie unter **Capture rate** (Erfassungsrate) die gewünschte Rate für die Erfassung und unter **Ziel** die Gesamtzahl an zu erfassenden Bildern aus. Klicken Sie auf **Set automatic capture** (Automatische Erfassung festlegen), um den Prozess für die automatische Bilderfassung zu starten.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Dropdownmenü für automatische Bilderfassung":::

Nachdem eine ausreichende Zahl von Fotos erfasst wurde, klicken Sie unten auf dem Bildschirm auf **Next: Tag images and model training** (Weiter: Bilder kennzeichnen und Modelltraining). Alle Bilder werden in [Custom Vision](https://www.customvision.ai/) gespeichert.

> [!NOTE]
> Falls Sie sich dafür entscheiden, Trainingsbilder direkt in Custom Vision hochzuladen, sollten Sie den Hinweis beachten, dass die Bilddateigröße 6 MB nicht überschreiten darf.

## <a name="tag-images-and-train-your-model"></a>Kennzeichnen von Bildern und Trainieren Ihres Modells

Fügen Sie Ihren Bildern Bezeichnungen hinzu, bevor Sie Ihr Modell trainieren.

1. Klicken Sie auf der Seite **Tag images and model training** (Bilder kennzeichnen und Modelltraining) auf **Open project in Custom Vision** (Projekt in Custom Vision öffnen).

1. Klicken Sie links auf der Seite **Custom Vision** unter **Tags** auf **Untagged** (Ohne Tags), um die Bilder anzuzeigen, die Sie eben im vorherigen Schritt erfasst haben. Wählen Sie eines oder mehrere Ihrer nicht mit Tags gekennzeichneten Bilder aus.

1. Klicken Sie im Fenster **Image Detail** (Bilddetail) auf das Bild, um mit dem Kennzeichnen zu beginnen. Falls Sie die Objekterkennung als Projekttyp ausgewählt haben, müssen Sie zusätzlich einen [Begrenzungsrahmen](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) um bestimmte Objekte ziehen, die Sie mit Tags kennzeichnen möchten. Passen Sie den Begrenzungsrahmen je nach Bedarf an. Geben Sie Ihr Objekttag ein, und klicken Sie auf **+** , um das Tag anzuwenden. Wenn Sie beispielsweise eine Vision-Lösung erstellen, bei der Sie benachrichtigt werden, dass ein Regel in einem Geschäft aufgefüllt werden muss, gehen Sie wie folgt vor: Sie fügen Bildern von leeren Regalen das Tag „Leeres Regal“ und Bildern von aufgefüllten Regalen das Tag „Volles Regal“ hinzu. Wiederholen Sie dies für alle nicht gekennzeichneten Bilder.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Bildschirm zum Kennzeichnen von Bildern in Custom Vision":::

1. Klicken Sie nach dem Kennzeichnen Ihrer Bilder oben rechts im Fenster auf das Symbol **X**. Klicken Sie unter **Tags** auf **Tagged** (Mit Tags), um alle neu gekennzeichneten Bilder anzuzeigen.

1. Nachdem Sie Ihre Bilder gekennzeichnet haben, können Sie Ihr KI-Modell trainieren. Klicken Sie hierzu oben auf der Seite auf **Trainieren**. Zum Trainieren des Modells müssen mindestens 15 Bilder pro Tagtyp vorhanden sein (Empfehlung: mindestens 30 Bilder). Das Training dauert normalerweise ca. 30 Minuten, aber der Vorgang kann auch mehr Zeit in Anspruch nehmen, wenn Ihr Satz an Bildern sehr umfangreich ist.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Bildauswahl für Training mit hervorgehobener Schaltfläche „Trainieren“":::

1. Nach Abschluss des Trainingsvorgangs wird die Leistung Ihres Modells auf dem Bildschirm angezeigt. Weitere Informationen zum Auswerten dieser Ergebnisse finden Sie unter [Auswerten des Erkennungsmoduls](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector). Nach dem Training haben Sie auch die Möglichkeit, [Ihr Modell mit weiteren Bildern zu testen](../cognitive-services/custom-vision-service/test-your-model.md) und das Training bei Bedarf zu wiederholen. Jeder Trainingsvorgang für Ihr Modell wird als neue Iteration gespeichert. Weitere Informationen zur Verbesserung der Modellleistung finden Sie in der [Custom Vision-Dokumentation](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md).

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Ergebnisse des Modelltrainings":::

    > [!NOTE]
    > Falls Sie Ihr Modell anhand von weiteren Bildern in Custom Vision testen möchten, sollten Sie beachten, dass die Dateigröße der Testbilder 4 MB nicht überschreiten darf.

Wenn Sie mit der Leistung Ihres Modells zufrieden sind, können Sie Custom Vision schließen, indem Sie die Browserregisterkarte schließen.

## <a name="deploy-your-ai-model"></a>Bereitstellen Ihres KI-Modells

1. Wechseln Sie zurück zur Azure Percept Studio-Registerkarte, und klicken Sie unten auf dem Bildschirm auf **Next: Evaluate and deploy** (Weiter: Auswerten und bereitstellen).

1. Im Fenster **Evaluate and deploy** (Auswerten und bereitstellen) wird die Leistung Ihrer ausgewählten Modelliteration angezeigt. Wählen Sie im Dropdownmenü **Model iteration** (Modelliteration) die Iteration aus, die Sie für Ihr DevKit bereitstellen möchten, und klicken Sie unten auf dem Bildschirm auf **Deploy model** (Modell bereitstellen).

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Bildschirm für Modellbereitstellung" lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Nachdem Sie Ihr Modell bereitgestellt haben, können Sie den Videodatenstrom Ihres Geräts anzeigen, um die Modellrückschlüsse in Aktion zu sehen.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Gerätestream für Kopfhörererkennung":::

Nachdem Sie dieses Fenster geschlossen haben, können Sie Ihr Vision-Projekt jederzeit bearbeiten, indem Sie auf der Startseite von Azure Percept Studio unter **KI-Projekte** auf **Vision** klicken und den Namen Ihres Vision-Projekts auswählen.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Seite mit Vision-Projekt" lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Verbessern Ihres Modells durch Einrichten des erneuten Trainings

Nachdem Sie Ihr Modell trainiert und auf dem Gerät bereitgestellt haben, können Sie die Modellleistung verbessern, indem Sie die Parameter für das erneute Training einrichten, um weitere Trainingsdaten zu erfassen. Dieses Feature ermöglicht die Erfassung von Bildern auf der Grundlage eines Wahrscheinlichkeitsbereichs, um die Leistung eines trainierten Modells zu verbessern. So können Sie beispielsweise festlegen, dass Ihr Gerät nur Trainingsbilder erfassen soll, wenn die Wahrscheinlichkeit niedrig ist. Hier finden Sie einige [zusätzliche Anleitungen](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) zum Hinzufügen weiterer Bilder sowie zum Ausbalancieren von Trainingsdaten.

1. Kehren Sie zum Einrichten des erneuten Trainings zu Ihrem **Projekt** zurück, und navigieren Sie zur **Projektzusammenfassung**.
1. Wählen Sie auf der Registerkarte **Bilderfassung** die Option **Automatische Bilderfassung** und anschließend **Erneutes Training einrichten** aus.
1. Richten Sie die automatisierte Bilderfassung ein, um gleichzeitig eine große Zahl von Bildern zu erfassen. Aktivieren Sie hierfür das Kontrollkästchen **Automatische Bilderfassung**.
1. Wählen Sie unter **Capture rate** (Erfassungsrate) die gewünschte Rate für die Erfassung und unter **Ziel** die Gesamtzahl an zu erfassenden Bildern aus.
1. Wählen Sie im Abschnitt **Erneutes Training einrichten** die Iterationen aus, für die Sie weitere Trainingsdaten erfassen möchten, und wählen Sie anschließend den Wahrscheinlichkeitsbereich aus. In Ihr Projekt werden nur Bilder hochgeladen, die der Wahrscheinlichkeitsrate entsprechen.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="Bilderfassung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie für dieses Tutorial eine neue Azure-Ressource erstellt haben und Sie Ihre Vision-Lösung nicht weiterentwickeln oder nutzen möchten, sollten Sie die folgenden Schritte ausführen, um die Ressource zu löschen:

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/).
1. Klicken Sie auf **Alle Ressourcen**.
1. Aktivieren Sie das Kontrollkästchen neben der Ressource, die Sie in diesem Tutorial erstellt haben. Als Ressourcentyp wird **Cognitive Services** angezeigt.
1. Klicken Sie oben auf dem Bildschirm auf das Symbol **Löschen**.

## <a name="video-walkthrough"></a>Exemplarische Vorgehensweise per Video

Eine visuelle exemplarische Vorgehensweise für die oben beschriebenen Schritte finden Sie im folgenden Video:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich als Nächstes die Gewusst-wie-Artikel zum maschinellen Sehen an, um sich über weitere Features von Vision-Lösungen in Azure Percept Studio zu informieren.

<!--
Add links to how-to articles and oobe article.
-->