---
title: Beschriften von Bildern und Textdokumenten
title.suffix: Azure Machine Learning
description: Verwenden Sie Datenbeschriftungstools, um Text oder Bilder für Machine Learning in einem Datenbeschriftungsprojekt schnell zu beschriften.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a811c63e29800fa7f79d620f358e06939168ce4c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563034"
---
# <a name="labeling-images-and-text-documents"></a>Beschriften von Bildern und Textdokumenten

Nachdem Ihr Projektadministrator ein [Bilddatenbeschriftungsprojekt](./how-to-create-image-labeling-projects.md) oder ein [Textdatenbeschriftungsprojekt](./how-to-create-text-labeling-projects.md) in Azure Machine Learning erstellt hat, können Sie mit dem Beschriftungstool schnell Daten für ein Machine Learning-Projekt vorbereiten. Dieser Artikel beschreibt Folgendes:

> [!div class="checklist"]
> * Zugreifen auf Ihre Bezeichnungsprojekte
> * Die Bezeichnungstools
> * Verwenden der Tools für bestimmte Bezeichnungsaufgaben

## <a name="prerequisites"></a>Voraussetzungen

* Ein [Microsoft-Konto](https://account.microsoft.com/account) oder ein Azure Active Directory-Konto für die Organisation und das Projekt
* Zugriffsebene „Mitwirkender“ für den Arbeitsbereich, der das Bezeichnungsprojekt enthält

## <a name="sign-in-to-the-studio"></a>Anmelden bei Studio

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com) an.

1. Wählen Sie das Abonnement und den Arbeitsbereich mit dem Bezeichnungsprojekt aus.  Diese Informationen erhalten Sie vom Projektadministrator.

1. Wählen Sie auf der linken Seite **Datenbeschriftung** aus, um nach dem Projekt zu suchen.  

## <a name="understand-the-labeling-task"></a>Grundlegendes zur Bezeichnungsaufgabe

Wählen Sie in der Tabelle der Datenbeschriftungsprojekte den Link **Daten beschriften** für Ihr Projekt aus.

Es werden projektspezifische Anweisungen angezeigt. Sie enthalten relevante Informationen wie etwa die Art der vorliegenden Daten und Hinweise zum Treffen von Entscheidungen. Lesen Sie diese Informationen, und wählen Sie anschließend oben auf der Seite **Aufgaben** aus.  Stattdessen können Sie auch am unteren Rand der Seite auf **Beschriftung starten** klicken.

## <a name="selecting-a-label"></a>Auswählen einer Beschriftung

Bei Datenbeschriftungsaufgaben müssen jeweils geeignete Tags aus einer vom Projektadministrator angegebenen Gruppe ausgewählt werden. Die ersten neun Tags können über die Zifferntasten auf der Tastatur ausgewählt werden.  

## <a name="assisted-machine-learning"></a>Unterstütztes Machine Learning

Machine-Learning-Algorithmen können während der Beschriftung ausgelöst werden. Wenn diese Algorithmen in Ihrem Projekt aktiviert sind, wird ggf. Folgendes angezeigt:

* Bilder

    * Nach der Kennzeichnung einiger Daten wird am oberen Bildschirmrand neben dem Projektnamen unter Umständen **Gruppierte Aufgaben** angezeigt.  Das bedeutet, dass Bilder gruppiert werden, um ähnliche Bilder auf der gleichen Seite darzustellen.  Wechseln Sie in diesem Fall zu einer der Ansichten mit mehreren Bildern, um von der Gruppierung zu profitieren.  
    
    * Später wird neben dem Projektnamen unter Umständen **Vorab beschriftete Aufgaben** angezeigt.  Elemente werden dann mit einer von einem Machine-Learning-Klassifizierungsmodell vorgeschlagenen Beschriftung angezeigt. Machine Learning-Modelle sind niemals hundertprozentig genau. Es werden zwar nur Daten verwendet, bei denen das Modell eine hohe Zuverlässigkeit ermittelt hat, trotzdem kann es vorkommen, dass diese Bilder nicht korrekt vorab beschriftet wurden.  Wenn Beschriftungen angezeigt werden, korrigieren Sie falsche Beschriftungen, bevor Sie die Seite übermitteln.  
    
    * Bei Objektidentifizierungsmodellen sind Begrenzungsrahmen und Bezeichnungen möglicherweise bereits vorhanden.  Korrigieren Sie alle Fehler, bevor Sie die Seite senden.
    
    * Bei Segmentierungsmodellen sind Polygone und Bezeichnungen möglicherweise bereits vorhanden.  Korrigieren Sie alle Fehler, bevor Sie die Seite senden. 

* Text
    
    * Später wird neben dem Projektnamen unter Umständen **Vorab beschriftete Aufgaben** angezeigt.  Elemente werden dann mit einer von einem Machine-Learning-Klassifizierungsmodell vorgeschlagenen Beschriftung angezeigt. Machine Learning-Modelle sind niemals hundertprozentig genau. Es werden zwar nur Daten verwendet, bei denen das Modell eine hohe Zuverlässigkeit ermittelt hat, trotzdem kann es vorkommen, dass diese Bilder nicht korrekt vorab beschriftet wurden.  Wenn Beschriftungen angezeigt werden, korrigieren Sie falsche Beschriftungen, bevor Sie die Seite übermitteln.

Insbesondere in der Anfangsphase eines Bezeichnungsprojekts ist die Genauigkeit des Machine Learning-Modells möglicherweise nur für die Kennzeichnung einer kleinen Teilmenge von Bildern ausreichend. Sobald diese Bilder gekennzeichnet wurden, kehrt das Bezeichnungsprojekt zur manuellen Kennzeichnung zurück, um mehr Daten für das nächste Modelltraining zu erfassen. Im Laufe der Zeit erhöht sich die Zuverlässigkeit des Modells für einen größeren Teil von Bildern, was im weiteren Projektverlauf zu mehr Vorabbeschriftungsaufgaben führt.

Wenn keine Vorabbeschriftungsaufgaben mehr vorhanden sind, beenden Sie das Bestätigen oder Korrigieren von Bezeichnungen und wechseln zurück zum manuellen Markieren der Elemente.

## <a name="image-tasks"></a><a name="image-tasks"></a> Bildaufgaben

Bei Bildklassifizierungsaufgaben können Sie auswählen, dass mehrere Bilder gleichzeitig angezeigt werden sollen. Verwenden Sie die Symbole über dem Bildbereich, um das Layout auszuwählen.

Wenn Sie alle angezeigten Bilder gleichzeitig auswählen möchten, verwenden Sie **Alle auswählen**. Wenn Sie einzelne Bilder auswählen möchten, verwenden Sie die runde Auswahlschaltfläche in der rechten oberen Ecke des Bilds. Sie müssen mindestens ein Bild auswählen, um ein Tag anzuwenden. Wenn Sie mehrere Bilder auswählen, wird jedes Tag, das Sie auswählen, auf alle ausgewählten Bilder angewendet.

Hier haben wir ein 2 × 2-Layout ausgewählt und sind im Begriff, das Tag „Mammal“ (Säugetier) auf die Bilder mit dem Bären und dem Orca anzuwenden. Das Bild mit dem Hai wurde bereits als „Cartilaginous fish“ (Knorpelfisch) markiert, und der Leguan wurde noch nicht markiert.

![Layouts mit mehreren Bildern und Auswahl](./media/how-to-label-data/layouts.png)

> [!Important] 
> Wechseln Sie das Layout nur, wenn Sie über eine neue Seite mit Daten ohne Bezeichnung verfügen. Durch das Wechseln von Layouts werden die bereits ausgeführten Bezeichnungen der Seite gelöscht.

Azure aktiviert die Schaltfläche **Senden**, wenn Sie alle Bilder auf der Seite markiert haben. Wählen Sie **Übermitteln** aus, um Ihre Arbeit zu speichern.

Nach der Übermittlung von Tags für die vorliegenden Daten aktualisiert Azure die Seite mit einer neuen Gruppe von Bildern aus der Arbeitswarteschlange.

## <a name="medical-image-tasks"></a>Aufgaben für medizinische Bilder

> [!IMPORTANT]
> Die Möglichkeit der Beschriftung von DICOM- oder ähnlichen Bildtypen ist nicht für die Verwendung als medizinisches Hilfsmittel, zur klinischen Unterstützung, als Diagnosetool oder sonstige Technologie für den Einsatz in Diagnose, Therapie, Linderung, Behandlung oder Prävention von Krankheiten oder medizinischen Fällen vorgesehen oder verfügbar. Microsoft erteilt keine Lizenzen oder Rechte zur Nutzung dieser Funktion für die beschriebenen Zwecke. Diese Funktion ist nicht als Ersatz für professionelle medizinische Beratung oder medizinische Gutachten, Diagnosen, Behandlungen oder das klinische Urteilsvermögen einer medizinischen Fachkraft konzipiert oder vorgesehen und sollte nicht als solcher eingesetzt werden. Der Kunde allein ist für die Verwendung der Datenbeschriftung für DICOM- oder ähnliche Bildtypen verantwortlich.

Bildprojekte unterstützen das DICOM-Bildformat für Röntgendateibilder.

:::image type="content" source="media/how-to-label-data/x-ray-image.png" alt-text="Zu beschriftendes DICOM-Röntgenbild.":::

Während Sie die medizinischen Bilder mit denselben Tools wie alle anderen Bilder bezeichnen, gibt es für DICOM-Bilder ein zusätzliches Tool.  Wählen Sie das Tool **Fenster und Ebene** aus, um die Intensität des Bilds zu ändern. Dieses Tool ist nur für DICOM-Bilder verfügbar.

:::image type="content" source="media/how-to-label-data/window-level-tool.png" alt-text="Tool „Fenster und Ebenen“ für DICOM-Bilder.":::

## <a name="tag-images-for-multi-class-classification"></a>Taggen von Bildern für die Klassifizierung mit mehreren Klassen

Wenn Ihr Projekt vom Typ „Bildklassifizierung mit mehreren Klassen“ ist, weisen Sie dem gesamten Bild ein einzelnes Tag zu. Die Anweisungen stehen jederzeit auf der Seite **Anweisungen** unter **Detaillierte Anweisungen anzeigen** zur Verfügung.

Fehler bei der Tagzuweisung zu einem Bild können nachträglich korrigiert werden. Wählen Sie das Symbol **X** für die unter dem Bild angezeigte Bezeichnung aus, um das Tag zu löschen. Alternativ können Sie das Bild und anschließend eine andere Klasse auswählen. In diesem Fall wird das zuvor angewendete Tag durch den neu ausgewählten Wert ersetzt.

## <a name="tag-images-for-multi-label-classification"></a>Taggen von Bildern für die Klassifizierung mit mehreren Beschriftungen

Wenn Sie an einem Projekt vom Typ „Bildklassifizierung mit mehreren Beschriftungen“ arbeiten, wenden Sie *mindestens ein* Tag auf ein Bild an. Wenn Sie die projektspezifischen Anweisungen anzeigen möchten, wählen Sie **Anweisungen** aus, und navigieren Sie zu **Detaillierte Anweisungen anzeigen**.

Wählen Sie das Bild aus, auf das Sie eine Bezeichnung anwenden möchten, und wählen Sie anschließend das Tag aus. Das-Tag wird auf alle ausgewählten Bilder angewendet, und die Auswahl der Bilder wird aufgehoben. Wenn Sie weitere Tags anwenden möchten, müssen Sie die Bilder erneut auswählen. Die folgende Animation zeigt das Taggen mit mehreren Bezeichnungen:

1. **Alle auswählen** wird zum Anwenden des Tags „Ocean“ (Meer) verwendet.
1. Ein einzelnes Bild wird ausgewählt und als „Closeup“ (Nahaufnahme) markiert.
1. Drei Bilder werden ausgewählt und als „Wide Angle“ (Weitwinkel) markiert.

![Animation mit dem Flow für mehrere Bezeichnungen](./media/how-to-label-data/multilabel.gif)

Wenn Sie einen Fehler korrigieren möchten, klicken Sie auf das Symbol **X**, um ein einzelnes Tag zu löschen, oder wählen Sie die Bilder und anschließend das Tag aus, um es aus allen ausgewählten Bildern zu löschen. Dieses Szenario wird hier gezeigt. Durch Klicken auf „Land/Region“ wird dieses Tag aus den beiden ausgewählten Bildern gelöscht.

![Screenshot: Mehrere Markierungsaufhebungen](./media/how-to-label-data/multiple-deselection.png)

Azure aktiviert die Schaltfläche **Senden** erst, nachdem Sie mindestens ein Tag auf jedes Bild angewandt haben. Wählen Sie **Übermitteln** aus, um Ihre Arbeit zu speichern.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Taggen von Bildern und Angeben von Begrenzungsrahmen für die Objekterkennung

Bei einem Projekt vom Typ „Object Identification (Bounding Boxes)“ (Objektidentifizierung (Begrenzungsrahmen)) geben Sie Begrenzungsrahmen im Bild an und wenden jeweils ein Tag auf die einzelnen Rahmen an. Bilder können über mehrere Begrenzungsrahmen mit jeweils einem einzelnen Tag verfügen. Verwenden Sie **Detaillierte Anweisungen anzeigen**, um zu ermitteln, ob in Ihrem Projekt mehrere Begrenzungsrahmen verwendet werden.

1. Wählen Sie ein Tag für den Begrenzungsrahmen aus, den Sie erstellen möchten.
1. Wählen Sie das **Tool für rechteckige Felder**![Tool für rechteckige Felder](./media/how-to-label-data/rectangular-box-tool.png) aus, oder drücken Sie „R“.
3. Klicken Sie auf Ihr Ziel, und ziehen Sie diagonal, um einen ungefähren Begrenzungsrahmen zu erstellen. Ziehen Sie die Ränder oder Ecken, um das Begrenzungsfeld anzupassen.

![Erstellen von Begrenzungsrahmen](./media/how-to-label-data/bounding-box-sequence.png)

Wenn Sie einen Begrenzungsrahmen löschen möchten, klicken Sie auf das X-förmige Ziel, das nach der Erstellung neben dem Begrenzungsrahmen angezeigt wird.

Das Tag eines vorhandenen Begrenzungsrahmens kann nicht geändert werden. Sollte Ihnen bei der Tagzuweisung ein Fehler unterlaufen sein, müssen Sie den Begrenzungsrahmen löschen und einen neuen Begrenzungsrahmen mit dem richtigen Tag erstellen.

Vorhandene Begrenzungsrahmen können standardmäßig bearbeitet werden. Das Tool zum **Sperren/Entsperren von Bereichen** (![Tool zum Sperren/Entsperren von Bereichen](./media/how-to-label-data/lock-bounding-boxes-tool.png)) oder „L“ schaltet dieses Verhalten um. Wenn Bereiche gesperrt sind, können Sie nur die Form oder Position eines neuen Begrenzungsrahmens ändern.

Verwenden Sie das **Tool zum Bearbeiten von Bereichen** ![Symbol für das Tool zum Bearbeiten von Bereichen (vier nach außen gerichtete Pfeile, die nach oben, rechts, unten und links weisen)](./media/how-to-label-data/regions-tool.png) oder „M“, um einen vorhandenen Begrenzungsrahmen anzupassen. Ziehen Sie die Ränder oder Ecken, um die Form anzupassen. Klicken Sie auf den Innenbereich, um den gesamten Begrenzungsrahmen verschieben zu können. Wenn Sie einen Bereich nicht bearbeiten können, haben Sie diese Funktionalität wahrscheinlich mit dem Tool zum **Sperren/Entsperren von Bereichen** umgeschaltet.

Verwenden Sie das Tool für **vorlagenbasierte Felder** (![Tool für vorlagenbasierte Felder](./media/how-to-label-data/template-box-tool.png)) oder „T“, um mehrere Begrenzungsrahmen derselben Größe zu erstellen. Wenn das Bild keinen Begrenzungsrahmen besitzt und Sie vorlagenbasierte Rahmen aktivieren, erzeugt das Tool Rahmen mit einer Größe von 50 × 50 Pixel. Wenn Sie einen Begrenzungsrahmen erstellt haben und anschließend vorlagenbasierte Rahmen aktivieren, haben neue Begrenzungsrahmen die Größe des zuletzt erstellten Rahmens. Die Größe von vorlagenbasierten Feldern kann nach der Platzierung angepasst werden. Beim Ändern der Größe eines vorlagenbasierten Rahmens wird nur die Größe dieses spezifischen Rahmens geändert.

Wenn Sie *alle* Begrenzungsrahmen im aktuellen Bild löschen möchten, wählen Sie das **Tool zum Löschen aller Regionen**![Tool zum Löschen aller Regionen](./media/how-to-label-data/delete-regions-tool.png) aus.

Wählen Sie nach Erstellung der Begrenzungsrahmen für ein Bild die Option **Übermitteln** aus, um Ihre Arbeit zu speichern. Andernfalls geht Ihre Arbeit verloren.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Markieren von Bildern und Angeben von Polygonen für die Bildsegmentierung 

Ist Ihr Projekt vom Typ „Instanzsegmentierung (Polygon)“, geben Sie im Bild mindestens ein Polygon an, und wenden jeweils ein Tag auf jedes Polygon an. Bilder können über mehrere Begrenzungspolygone mit jeweils einem einzelnen Tag verfügen. Verwenden Sie **Detaillierte Anweisungen anzeigen**, um zu ermitteln, ob in Ihrem Projekt mehrere Begrenzungspolygone verwendet werden.

1. Wählen Sie ein Tag für das Polygon aus, das Sie erstellen möchten.
1. Wählen Sie das Tool **Polygonbereich zeichnen** ![Tool „Polygonbereich zeichnen“](./media/how-to-label-data/polygon-tool.png) oder „P“ aus.
1. Klicken Sie für jeden Punkt im Polygon.  Wenn Sie die Form abgeschlossen haben, doppelklicken Sie zum Fertigstellen.

    :::image type="content" source="media/how-to-label-data/polygon.gif" alt-text="Erstellen von Polygonen für Katze und Hund":::

Wenn Sie ein Polygon löschen möchten, klicken Sie auf das X-förmige Ziel, das nach der Erstellung neben dem Polygon angezeigt wird.

Wenn Sie das Tag für ein Polygon ändern möchten, wählen Sie das Tool **Verschiebebereich** aus, klicken Sie auf das Polygon, und wählen Sie das richtige Tag aus.

Sie können vorhandene Polygone bearbeiten. Das Tool zum **Sperren/Entsperren von Bereichen** ![Bearbeiten von Polygonen mit dem Tool zum Sperren/Entsperren von Bereichen](./media/how-to-label-data/lock-bounding-boxes-tool.png) oder „L“ schaltet dieses Verhalten um. Wenn Bereiche gesperrt sind, können Sie nur die Form oder Position eines neuen Polygons ändern.

Verwenden Sie das **Tool zum Hinzufügen oder Entfernen von Polygonpunkten** ![Symbol für das Tool zum Hinzufügen oder Entfernen von Polygonpunkten](./media/how-to-label-data/add-remove-points-tool.png) oder „U“, um ein vorhandenes Polygon anzupassen. Klicken Sie auf das Polygon, um einen Punkt hinzuzufügen oder zu entfernen. Wenn Sie einen Bereich nicht bearbeiten können, haben Sie diese Funktionalität wahrscheinlich mit dem Tool zum **Sperren/Entsperren von Bereichen** umgeschaltet.

Wenn Sie *alle* Polygone im aktuellen Bild löschen möchten, wählen Sie das Tool **Alle Bereiche löschen** ![Tool „Alle Bereiche löschen“](./media/how-to-label-data/delete-regions-tool.png) aus.

Wählen Sie nach Erstellung der Polygone für ein Bild die Option **Übermitteln** aus, um Ihre Arbeit zu speichern. Andernfalls geht Ihre Arbeit verloren.

## <a name="label-text-preview"></a><a name="label-text"></a>Beschriften von Text (Vorschau)

> [!IMPORTANT]
> Die Textbeschriftung befindet sich in der öffentlichen Vorschau.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Verwenden Sie beim Taggen von Text die Symbolleiste zum:

* Erhöhen oder Verringern der Textgröße
* Ändern der Schriftart
* Überspringen der Beschriftung dieses Elements und Wechseln zum nächsten Element

Fehler bei der Tagzuweisung können nachträglich korrigiert werden. Wählen Sie das Symbol „**X**“ für die unter dem Bild angezeigte Beschriftung aus, um das Tag zu löschen.

Es gibt zwei Textprojekttypen:


|Projekttyp  |Kennzeichnung (Tagging)  |
|---------|---------|
| Klassifizierung mit mehreren Klassen | Weisen Sie dem gesamten Textelement ein einzelnes Tag zu.  Sie können nur ein Tag für jedes Textelement auswählen.   |
| Kassifizierung mit mehreren Beschriftungen     | Weisen Sie *jedem Textelement* ein oder mehrere Tags zu.  Sie können für jedes Textelement mehrere Tags auswählen.       |

Wenn Sie die projektspezifischen Anweisungen anzeigen möchten, wählen Sie **Anweisungen** aus, und navigieren Sie zu **Detaillierte Anweisungen anzeigen**.

## <a name="finish-up"></a>Fertigstellen

Wenn Sie eine Seite mit markierten Daten übermitteln, weist Azure Ihnen neue Daten ohne Bezeichnung aus einer Arbeitswarteschlange zu. Sind keine Daten ohne Bezeichnung mehr vorhanden, erhalten Sie eine entsprechende Meldung mit einem Link zur Startseite des Portals.

Wenn Sie mit dem Anwenden von Bezeichnungen fertig sind, wählen Sie in der rechten oberen rechten Ecke des Bezeichnungsportals Ihren Namen und anschließend **Abmelden** aus. Wenn Sie sich nicht abmelden, wird in Azure zu einem späteren Zeitpunkt eine Zeitüberschreitung festgestellt, und die Daten werden einer anderen Person für die Bezeichnung zugewiesen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Bildklassifizierungsmodelle in Azure trainieren](./tutorial-train-models-with-aml.md).
