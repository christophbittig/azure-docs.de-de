---
title: Beschleunigen von Warnungsworkflows
description: Verbessern Sie Warnungs- und Incidentworkflows.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: e570cff67630a483d6926f6090165708a5ca17b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283978"
---
# <a name="accelerate-alert-workflows"></a>Beschleunigen von Warnungsworkflows

In diesem Abschnitt wird beschrieben, wie Sie in Microsoft Defender für IoT Warnungs-Workflows mithilfe von Warnungskommentaren, Warnungsgruppen und benutzerdefinierten Warnungsregeln beschleunigen.  Die folgenden Tools sind für diesen Zweck hilfreich:

- Analysieren und verwalten Sie das große Aufkommen der in Ihrem Netzwerk erkannten Warnungsereignisse.

- Identifizieren und behandeln Sie bestimmte Netzwerkaktivitäten.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Beschleunigen von Incidentworkflows mithilfe von Warnungskommentaren

Arbeiten Sie mit Warnungskommentaren, um die Kommunikation zwischen Einzelpersonen und Teams bei der Untersuchung eines Warnungsereignisses zu verbessern.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of-malicious-activity-screen.png" alt-text="Screenshot: Schädliche Aktivität":::

Mit Warnungskommentaren können Sie Folgendes verbessern:

- **Workflowschritte**: Bereitstellen der Schritte zur Bedrohungsabwehr.

- **Workflow-Nachverfolgung**: Benachrichtigung über die ausgeführten Schritte.

- **Anleitungen zum Workflow**: Bereitstellen von Empfehlungen, Erkenntnissen und Warnungen zu dem jeweiligen Ereignis.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Screenshot: Warnungskommentare":::

Eine Liste verfügbarer Optionen wird in jeder Warnung angezeigt. Benutzer können eine oder mehrere Meldungen auswählen.

So fügen Sie Warnungskommentare hinzu

1. Wählen Sie im seitlichen Menü **Systemeinstellungen** aus.

2. Wählen Sie im Fenster **Systemeinstellungen** die Option **Warnungskommentare** aus.

3. Geben Sie im Feld **Kommentare hinzufügen** den Text für den Kommentar ein. Sie können bis zu 50 Zeichen verwenden. Kommas sind nicht zulässig.

4. Wählen Sie **Hinzufügen** aus.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Beschleunigen von Incidentworkflows mithilfe von Warnungsgruppen

Mithilfe von Warnungsgruppen können SOC-Teams in ihren SIEM-Lösungen Warnungen anzeigen und filtern und diese Warnungen dann auf der Grundlage von Sicherheitsrichtlinien und geschäftlichen Prioritäten im Unternehmen verwalten. Beispiel: Warnungen zu neuen Erkennungen werden in einer Erkennungsgruppe organisiert. Diese Gruppe enthält dann Warnungen, die sich auf die Erkennung neuer Geräte, neuer VLANs, neuer Benutzerkonten, neuer Mac-Adressen usw. beziehen.

Warnungsgruppen werden beim Erstellen von Weiterleitungsregeln für die folgenden Partnerlösungen angewendet:

  - Syslog-Server

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Screenshot: Erstellen einer Weiterleitungsregel":::

Die entsprechende Warnungsgruppe wird in der Ausgabe der Partnerlösungen angezeigt. 

### <a name="requirements"></a>Anforderungen

Die Warnungsgruppe wird in unterstützten Partnerlösungen mit den folgenden Präfixen angezeigt:

- **cat** für QRadar, ArcSight, Syslog CEF, Syslog LEEF

- **Alert Group** für Syslog-Textnachrichten

- **alert_group** für Syslog-Objekte

Diese Felder sollten in der Partnerlösung so konfiguriert werden, dass der Name der Warnungsgruppe angezeigt wird. Wenn mit einer Warnungsgruppe keine Warnung verknüpft ist, wird das Feld in der Partnerlösung mit dem Wert **N/A** angezeigt.

### <a name="default-alert-groups"></a>Standardwarnungsgruppen

Die folgenden Warnungsgruppen werden automatisch definiert:

- Ungewöhnliches Kommunikationsverhalten
- Benutzerdefinierte Warnungen
- Remotezugriff
- Ungewöhnliches Verhalten bei der HTTP-Kommunikation
- Ermittlung
- Befehle zum Neustarten und Abbrechen
- Authentifizierung
- Firmware-Änderung
- Scannen
- Unautorisiertes Kommunikationsverhalten
- Unzulässige Befehle
- Sensordatenverkehr
- Bandbreitenauffälligkeiten
- Zugriff auf das Internet
- Verdacht auf Schadsoftware
- Pufferüberlauf 
- Vorgangsfehler
- Verdacht auf schädliche Aktivität
- Befehlsfehler
- Betriebsprobleme
- Konfigurationsänderungen
- Programmieren

Warnungsgruppen sind vordefiniert. Weitere Informationen zu Warnungen, die mit Warnungsgruppen verknüpft sind, und zum Erstellen benutzerdefinierter Warnungsgruppen erhalten Sie beim [Microsoft-Support](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Anpassen von Warnungsregeln

Verwenden Sie benutzerdefinierte Warnungsregeln, um die für Sie interessanten Aktivitäten genauer einzukreisen.

Sie können benutzerdefinierte Warnungsregeln basierend auf Folgendem hinzufügen:

- einer Kategorie, z. B. einem Protokoll, einem Port oder einer Datei
- Quell- und Zieladressen
- einer Bedingung, die auf der ausgewählten Kategorie basiert, z. B. einer Funktion, die einem Protokoll zugeordnet ist, einem Dateinamen, einem Port oder einer Transportnummer
- einer Bedingung, die auf einem Datums- und Uhrzeitverweis basiert, wenn eine Erkennung z. B. an einem bestimmten Tag oder zu einer bestimmten Tageszeit durchgeführt wurde

Wenn der Sensor die in der Regel beschriebene Aktivität erkennt, wird die Warnung gesendet. Sie enthält die Informationen, die von einzelnen Sensoren erkannt wurden. Definieren Sie beispielsweise eine Regel, die einen Sensor anweist, auf Grundlage einer Quell-IP-Adresse, Ziel-IP-Adresse oder eines Befehls (innerhalb eines Protokolls) eine Warnung auszulösen. Wenn der Sensor den in der Regel definierten Datenverkehr erkennt, wird eine Warnung oder ein Ereignis generiert.

Sie können auch Aktionen für Warnungsregeln verwenden, um Defender für IoT anzuweisen, folgende Aufgaben zu erledigen:

- Zulassen des Benutzerzugriffs auf die PCAP-Dateien aus der Warnung
- Zuweisen eines Warnungsschweregrads
- Generieren eines Ereignisses anstelle einer Warnung. Die erkannten Informationen werden auf der Ereigniszeitachse angezeigt.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Screenshot: Benutzerdefinierte Regel":::

Die Warnmeldung gibt an, dass eine benutzerdefinierte Regel die Warnung ausgelöst hat.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Screenshot: Benutzerdefinierte Warnungen":::

**So erstellen Sie eine benutzerdefinierte Warnungsregel:**

1. Wählen Sie im Seitenmenü eines Sensors **Benutzerdefinierte Warnungen** aus.
1. Wählen Sie das Pluszeichen ( **+** ) aus, um eine Regel zu erstellen.
1. Definieren Sie einen Regelnamen.
1. Wählen Sie im Bereich **Kategorien** eine Kategorie oder ein Protokoll aus.
1. Definieren Sie eine bestimmte Quelle und eine IP- oder MAC-Zieladresse, oder wählen Sie eine beliebige Adresse aus.
1. Definieren Sie Regelbedingungen. Es können zwei Kategorien von Bedingungen erstellt werden:
    - Bedingungen auf der Grundlage eindeutiger Werte, die der ausgewählten Kategorie zugeordnet sind. Wählen Sie „Hinzufügen“ aus, und definieren Sie die Werte.
    - Bedingungen auf der Grundlage des Zeitpunkts, zu dem die Aktivität erkannt wurde. Wählen Sie im Abschnitt „Erkennungen“ einen Zeitraum und einen Tag aus, an dem die Erkennung erfolgen muss, damit die Warnung gesendet wird. Sie können auswählen, ob die Warnung zu jeder Zeit gesendet werden soll, zu der die Aktivität erkannt wird, oder nur während bzw. außerhalb der Arbeitszeiten. Verwenden Sie die Option zum Definieren der Arbeitszeiten, um diese in Defender für IoT für Ihre Organisation festzulegen.
1. Definieren Sie Regelaktionen: 
    - Geben Sie an, ob die Regel einen **Alarm** oder ein **Ereignis** auslöst.
    - Weisen Sie der Warnung einen Schweregrad zu.
    - Geben Sie an, ob die Warnung eine PCAP-Datei enthält.
1. Wählen Sie **Speichern** aus.

Die Regel wird der Liste **Angepasste Warnungsregeln** hinzugefügt, in der Sie grundlegende Regelparameter, den Zeitpunkt der letzten Auslösung der Regel und mehr überprüfen können. Sie können die Regel in der Liste auch aktivieren und deaktivieren.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Screenshot: Vom Benutzer hinzugefügte benutzerdefinierte Regel":::

### <a name="see-also"></a>Weitere Informationen

[Anzeigen der in Warnungen angegebenen Informationen](how-to-view-information-provided-in-alerts.md)

[Verwalten des Warnungsereignisses](how-to-manage-the-alert-event.md)
