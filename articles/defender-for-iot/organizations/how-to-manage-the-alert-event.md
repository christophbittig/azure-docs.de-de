---
title: Verwalten von Warnungsereignissen
description: Hier erfahren Sie, wie Sie Warnungsereignisse verwalten können, die im Netzwerk erkannt werden.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 64f8abc405b80c86107b8e90503d148286a1ea8c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339799"
---
# <a name="manage-alert-events"></a>Verwalten von Warnungsereignissen

Die folgenden Optionen sind für die Verwaltung von Warnungsereignissen verfügbar:

 | Aktion | BESCHREIBUNG |
 |--|--|
 | **Learn** | Autorisieren Sie das erkannte Ereignis. Weitere Informationen finden Sie im Artikel zum [Erlernen und Verlernen von Ereignissen](#about-learning-and-unlearning-events). |
 | **Bestätigen** | Blenden Sie die Warnung einmal für das erkannte Ereignis aus. Die Warnung wird wieder ausgelöst, wenn das Ereignis wieder erkannt wird. Weitere Informationen finden Sie im Artikel zum [Bestätigen und Aufgeben der Bestätigung von Ereignissen](#about-acknowledging-and-unacknowledging-events). |
 | **Mute** | Ignorieren Sie Aktivitäten mit identischen Geräten und vergleichbarem Datenverkehr fortlaufend. Weitere Informationen finden Sie im Artikel zum [Stummschalten und Aufheben der Stummschaltung von Ereignissen](#about-muting-and-unmuting-events). |
 
Sie können auch Warnungsinformationen exportieren.
## <a name="about-learning-and-unlearning-events"></a>Informationen zum Erlernen und Verlernen von Ereignissen

Ereignisse, die auf Abweichungen des gelernten Netzwerks hindeuten, spiegeln möglicherweise zulässige Netzwerkänderungen wider. Wenn Sie diese Änderungen genehmigen möchten, können Sie Defender für IoT anweisen, das Verhalten zu *erlernen*. Beispiele:

- Auf einem vorhandenen Gerät wurde eine neue Aktivität erkannt. Beispielsweise hat ein autorisiertes Gerät versucht, auf eine neue Ressource auf einem anderen Gerät zuzugreifen.

- Dem Netzwerk wird ein neues Gerät hinzugefügt.  

- Die Firmwareversion ändert sich gemäß den folgenden Standardwartungsverfahren.

- Ein neues Gerät hat einen Lese-/Schreibvorgang auf einem Zielcontroller ausgeführt.

- Ein neues Gerät führt einen Lese-/Schreibvorgang auf einem Zielcontroller aus und sollte als Programmiergerät definiert werden.

- Es wird eine neue legitime Überprüfung durchgeführt, und das Gerät sollte als Überprüfungsgerät definiert werden.

**So erlernen Sie ein Ereignis**: 

1. Navigieren Sie zur Registerkarte **Warnungen**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="Wählen Sie in der Navigationsleiste links auf dem Bildschirm die Registerkarte „Warnungen“ aus":::.

1. Wählen Sie eine Warnung aus der Liste der Warnungen aus.

1. Wählen Sie **Erlernen**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/learn.png" alt-text="Fenster „Address Scan Detected“ (Address-Scan erkannt)":::

In Warnungen reflektierte Aktivitäten werden beim Generieren von Data Mining-, Risikobewertungs- und Angriffsvektorberichten berechnet. Wenn Sie diese Ereignisse verwalten, aktualisiert der Sensor die Berichte entsprechend.

Wenn Sie **Learn** (Erlernen) auswählen, werden Datenverkehr, Konfigurationen oder Aktivitäten vom Sensor als zulässig betrachtet. Die Warnung wird für das Ereignis nicht mehr ausgelöst. Dies bedeutet auch, dass das Ereignis nicht einbezogen wird, wenn der Sensor eine Risikobewertung, einen Angriffsvektor und andere Berichte generiert.

Erlernte Ereignisse können verlernt werden. Wenn der Sensor Ereignisse verlernt, löst er Warnungen im Zusammenhang mit diesem Ereignis danach wieder aus.

**So verlernen Sie ein Ereignis**

1. Navigieren Sie zur Registerkarte **Warnungen**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="Wählen Sie in der Navigationsleiste links auf dem Bildschirm die Registerkarte „Warnungen“ aus":::.

1. Wählen Sie im Dropdownmenü der Ansicht die Option **Bestätigt** aus.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/view-acknowledged.png" alt-text="Wählen Sie im Dropdownmenü des Ansichtsabschnitts „Bestätigt“ aus." lightbox="media/how-to-work-with-alerts-sensor/view-acknowledged-expanded.png":::

1. Wählen Sie eine Warnung aus der Liste der Warnungen aus.

1. Wählen Sie **Verlernen** aus.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/unlearn.png" alt-text="Wählen Sie „Verlernen“ aus, um ein Ereignis zu verlernen":::.

Nachdem ein Ereignis verlernt wurde, wird es zurück zur Hauptansichtsseite verschoben.

**So sehen Sie, ob eine Warnung gelernt oder bestätigt wurde**:

1. Navigieren Sie zur Registerkarte „Zeitskala der Ereignisse“ :::image type="icon" source="media/how-to-work-with-alerts-sensor/event-timeline.png" border="false":::

1. Suchen Sie die Warnung auf der Zeitskala.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/event-timeline-acknowledged.png" alt-text="Sie finden die bestätigten Ereignisse in der Zeitskala der Ereignisse.":::

    Im Fenster der Warnung wird angezeigt, ob die Warnung gelernt oder bestätigt wurde.
 
## <a name="about-acknowledging-and-unacknowledging-events"></a>Informationen zum Bestätigen und Aufheben der Bestätigung von Ereignissen

In bestimmten Situationen möchten Sie möglicherweise nicht, dass ein Sensor ein erkanntes Ereignis erlernt, oder die Option ist möglicherweise nicht verfügbar. Stattdessen kann der Vorfall eine Entschärfung erfordern. Beispiel:

- **Entschärfen eines Vorfalls aufgrund einer Netzwerkkonfiguration oder eines Geräts**: Sie erhalten eine Warnung, dass ein neues Gerät im Netzwerk erkannt wurde. Bei der Prüfung stellen Sie fest, dass es sich bei diesem Gerät um ein nicht autorisiertes Netzwerkgerät handelt. Sie behandeln den Vorfall, indem Sie das Gerät vom Netzwerk trennen.
- **Aktualisieren einer Sensorkonfiguration**: Sie erhalten eine Warnung, dass ein Server eine übermäßige Anzahl von Remoteverbindungen initiiert hat. Diese Warnung wurde ausgelöst, da die definierten Sensoranomalie-Schwellenwerte an Sitzungen pro Minute überschritten wurden. Sie können auf den Vorfall reagieren, indem Sie die Schwellenwerte anpassen.

Nachdem Sie die Entschärfung oder Untersuchung durchgeführt haben, können Sie den Sensor anweisen, die Warnung auszublenden, indem Sie **Acknowledge** (Bestätigen) auswählen. Wenn das Ereignis wieder erkannt wird, wird die Warnung wieder ausgelöst.

So löschen Sie die Warnung

  - Wählen Sie **Acknowledge** (Bestätigen) aus.

So zeigen Sie die Warnung nochmal an:

  - Greifen Sie auf die Warnung zu, und wählen Sie **Unacknowledge** (Bestätigung aufheben) aus.

Heben Sie die Bestätigung von Warnungen auf, wenn eine weitere Untersuchung erforderlich ist.

## <a name="about-muting-and-unmuting-events"></a>Informationen zum Stummschalten und Aufheben der Stummschaltung von Ereignissen

Unter bestimmten Bedingungen möchten Sie möglicherweise den Sensor anweisen, ein bestimmtes Szenario in Ihrem Netzwerk zu ignorieren. Beispiel:

  - Die **Anomalie**-Engine löst eine Warnung für eine Spitze der Bandbreite zwischen zwei Geräten aus, aber die Spitze ist für diese Geräte zulässig.

  - Die **Protokollverletzungs**-Engine löst eine Warnung für eine Protokollabweichung aus, die zwischen zwei Geräten erkannt wurde, die Abweichung ist jedoch zwischen den Geräten zulässig.

In diesen Fällen ist das Erlernen nicht verfügbar. Wenn Erlernen nicht möglich ist und Sie die Warnung unterdrücken und das Gerät beim Berechnen von Risiken und Angriffsvektoren nicht einbeziehen möchten, können Sie stattdessen das Warnereignis stummschalten.

> [!NOTE] 
> Ereignisse, bei denen ein Internetgerät als Quelle oder Ziel definiert ist, können nicht stummgeschaltet werden.

### <a name="what-alert-activity-is-muted"></a>Welche Warnungsaktivitäten werden stummgeschaltet?

Die Stummschaltung bezieht sich auf Netzwerkgeräte und Datenverkehr, die für ein Ereignis erkannt werden. Der Titel der Warnung beschreibt den Datenverkehr, der stummgeschaltet wird.

Das Gerät oder die Geräte, die stummgeschaltet werden, werden als Bild in der Warnung angezeigt. Wenn zwei Geräte angezeigt werden, wird der Datenverkehr, für den spezifisch gewarnt wurde, zwischen ihnen stummgeschaltet.

**Beispiel 1**

Wenn ein Ereignis stummgeschaltet wird, wird es immer dann ignoriert, wenn das primäre Gerät (Quelle) dem sekundären Gerät (Ziel) vom Hersteller als unzulässig definierten Funktionscode sendet.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Screenshot: Sekundäres Gerät hat Funktionscode empfangen.":::

**Beispiel 2**

Wenn ein Ereignis stummgeschaltet wird, wird es immer dann ignoriert, wenn die Quelle unabhängig vom Ziel einen HTTP-Header mit unzulässigem Inhalt sendet.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Screenshot: Unzulässiger HTTP-Headerinhalt":::

**Folgendes gilt, nachdem ein Ereignis stummgeschaltet wurde:**

- Die Warnung wird in der Warnungsansicht **Bestätigt** angezeigt, bis die Stummschaltung aufgehoben wird.

- Die Aktion „Mute“ (Stummschalten) wird in der **Ereigniszeitachse** angezeigt.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Screenshot: „Event detected and muted.“ (Ereignis erkannt und stummgeschaltet)":::

- Der Sensor bezieht Geräte erneut ein, wenn er Risikobewertungen, Angriffsvektoren und andere Berichte generiert. Wenn Sie z. B. eine Warnung, die schädlichen Datenverkehr auf einem Gerät erkannt hat, stummgeschaltet haben, wird dieses Gerät nicht im Risikobewertungsbericht einbezogen.

**So können Sie eine Warnung stummschalten und wieder aktivieren:**

- Wählen Sie das Symbol zum **Stummschalten** in der Warnung aus.

**So zeigen Sie stummgeschaltete Warnungen an:**

1. Wählen Sie die Option **Acknowledged** (Bestätigt) in der Hauptansicht zu den **Warnungen** aus.

2. Zeigen Sie mit dem Mauszeiger auf eine Warnung, um festzustellen, ob Sie stummgeschaltet wurde.  

## <a name="export-alert-information"></a>Exportieren von Warnungsinformationen

Exportieren Sie Warnungsinformationen in eine CSV-Datei. Sie können Informationen zu allen erkannten Warnungen oder Informationen basierend auf der gefilterten Ansicht exportieren. Folgende Informationen werden exportiert:

- Quelladresse
- Zieladresse
- Warnungstitel
- Schweregrad der Warnung
- Warnmeldung
- Zusätzliche Informationen
- Status „Bestätigt“
- PCAP-Verfügbarkeit

So erfolgt das Exportieren

1. Wählen Sie im Seitenmenü die Option Warnungen aus.

1. Wählen Sie Exportieren aus.

1. Wählen Sie „Export Extended Alerts“ (Erweiterte Warnungen exportieren) aus, um für jede Warnung, die mehrere Geräte abdeckt, Warnungsinformationen in separaten Zeilen zu exportieren. Wenn „Export Extended Alerts“ (Erweiterte Warnungen exportieren) ausgewählt ist, wird in der CSV-Datei eine duplizierte Zeile des Warnungsereignisses mit den eindeutigen Elementen in jeder Zeile erstellt. Die Verwendung dieser Option vereinfacht die Untersuchung exportierter Warnungsereignisse.

## <a name="see-also"></a>Weitere Informationen

[Warnungstypen und -beschreibungen](alert-engine-messages.md)

[Steuern des zu überwachenden Datenverkehrs](how-to-control-what-traffic-is-monitored.md)
