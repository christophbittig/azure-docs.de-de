---
title: Verwalten von proprietären Protokollen (Horizon)
description: Defender für IoT Horizon bietet eine offene Entwicklungsumgebung (Open Development Environment, ODE) zum Sichern von IoT- und ICS-Geräten, auf denen proprietäre Protokolle ausgeführt werden.
ms.date: 11/09/2021
ms.topic: reference
ms.openlocfilehash: 6a9995ec70f061381f1315e03c3781537733d97f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347283"
---
# <a name="defender-for-iot-horizon"></a>Defender für IoT Horizon

Defender für IoT Horizon enthält eine offene Entwicklungsumgebung (Open Development Environment, ODE) zum Sichern von IoT- und ICS-Geräten, auf denen proprietäre Protokolle ausgeführt werden.

Horizon bietet Folgendes:

  - Unbegrenzte, vollständige Unterstützung für allgemeine, proprietäre, benutzerdefinierte Protokolle oder Protokolle, die von einem beliebigen Standard abweichen. 
  - Ein neues Maß an Flexibilität und Umfang für DPI-Entwicklung.
  - Ein Tool, das die OT-Sichtbarkeit und -Steuerung exponentiell erweitert, ohne dass ein Upgrade auf neue Versionen durchgeführt werden muss.
  - Die Sicherheit, die eine proprietäre Entwicklung (ohne Weitergabe vertraulicher Informationen) ermöglicht.

Verwenden Sie das Horizon SDK zum Entwerfen von Zergliederungs-Plug-Ins, die den Netzwerkdatenverkehr decodieren, damit er von automatisierten Defender für IoT-Netzwerkanalyseprogrammen verarbeitet werden kann.

Protokollzergliederungen werden als externe Plug-Ins entwickelt und in eine umfangreiche Palette von Defender für IoT-Dienste integriert, z. B. Dienste, die Überwachungs-, Warnungs- und Berichterstattungsfunktionen bereitstellen.

Wenden Sie sich an <ms-horizon-support@microsoft.com>, um ausführliche Informationen zum Arbeiten mit dem ODE SDK (Open Development Environment) und zum Erstellen von Protokoll-Plug-Ins zu erhalten.

Sobald das Plug-In entwickelt wurde, können Sie über die Horizon-Webkonsole folgende Aktionen ausführen:

  - Ihr Plug-In hochladen
  - Plug-Ins aktivieren und deaktivieren  
  - Das Plug-In überwachen und debuggen, um die Leistung auszuwerten
  - Erstellen Sie benutzerdefinierte Warnungen auf der Grundlage von proprietären Protokollen. Zeigen Sie diese Warnungen in der Konsole an, und leiten Sie sie an Partneranbieter weiter. 

    :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-plugin.png" alt-text="Upload über Ihr Horizon-Plug-In"::: 

Dieses Feature steht Administratoren, Cyberx-Benutzern oder Supportbenutzern zur Verfügung.

So melden Sie sich bei der Horizon-Konsole an:

1. Melden Sie sich über die Befehlszeilenschnittstelle (CLI) bei Ihrem Sensor an.
2. Ändern Sie in der Datei `/var/cyberx/properties/horizon.properties` die Eigenschaft `ui.enabled` in `true` (`horizon.properties:ui.enabled=true`).
3. Melden Sie sich bei der Sensorkonsole an. 
4. Wählen Sie im Hauptmenü die Option **Horizon** aus. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-from-the-menu.png" alt-text="Wählen Sie im Hauptmenü die Option „Horizon“ aus.":::  

In der Horizon-Konsole werden die von Defender für IoT bereitgestellten Infrastruktur-Plug-Ins und alle anderen Plug-Ins angezeigt, die Sie erstellt und hochgeladen haben. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/infrastructure.png" alt-text="Screenshot der Infrastruktur von Horizon":::

## <a name="upload-plugins"></a>Hochladen von Plug-Ins

Nachdem Sie ihr proprietäres Zergliederungs-Plug-in erstellt und getestet haben, können Sie es über die Horizon-Konsole hochladen und überwachen.

So laden Sie ein Plug-In hoch:

1. Wählen Sie in der Konsole **UPLOAD** aus.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/upload-a-plugin.png" alt-text="Wählen Sie für Ihr Plug-In „Upload“ aus.":::

2. Ziehen Sie, oder navigieren Sie zu Ihrem Plug-In. Wenn der Uploadvorgang fehlschlägt, wird eine Fehlermeldung angezeigt.

Wenden Sie sich an <ms-horizon-support@microsoft.com>, um ausführliche Informationen zum Arbeiten mit dem ODE SDK (Open Development Environment) und zum Erstellen von Protokoll-Plug-Ins zu erhalten.

## <a name="enable-and-disable-plugins"></a>Aktivieren und Deaktivieren von Plug-Ins

Mithilfe der Umschaltfläche können Sie Plug-Ins aktivieren und deaktivieren. Wenn Plug-Ins deaktiviert wurden, wird der Datenverkehr nicht mehr überwacht.

Infrastruktur-Plug-Ins können nicht deaktiviert werden.

## <a name="monitor-plugin-performance"></a>Überwachen der Plug-In-Leistung 

Das Übersichtsfenster der Horizon-Konsole enthält grundlegende Informationen zu den von Ihnen hochgeladenen Plug-Ins, die Sie deaktivieren und aktivieren können.

:::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-overview.png" alt-text="Screenshot der Übersichtsseite von „Horizon“"::: 

| Application | Der Name des von Ihnen hochgeladenen Plug-ins. |
|--|--|
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/toggle-icon.png" border="false"::: | Schalten Sie das Plug-In ein oder aus. Wenn Sie das Plug-in ausschalten, verarbeitet der Sensor nicht den im Plug-in definierten Protokolldatenverkehr. |
| Time | Die Uhrzeit, zu der die Daten zuletzt analysiert wurden. Wird alle fünf Sekunden aktualisiert. |
| PPS | Die Anzahl von Paketen pro Sekunde. |
| Bandbreite | Die durchschnittliche Bandbreite, die innerhalb der letzten fünf Sekunden erkannt wurde. |
| Malforms | Falsch formatierte Prüfungen werden verwendet, nachdem das Protokoll positiv geprüft wurde. Wenn bei der Verarbeitung der Pakete, basierend auf dem Protokoll, ein Fehler auftritt, wird eine Fehlerantwort zurückgegeben.<br/> <br />In dieser Spalte wird die Anzahl von Fehlern des Typs „malform“ (falsch formatiert) in den letzten fünf Sekunden angegeben. |
| Warnungen | Pakete entsprechen der Struktur und Spezifikation, aber es gibt ein unerwartetes Verhalten, das auf der Warnungskonfiguration des Plug-Ins basiert. |
| Errors | Die Anzahl von Paketen, bei denen die grundlegenden Protokollprüfungen, ob das Paket den Protokolldefinitionen entspricht, nicht bestanden wurden.  Die hier angezeigte Zahl gibt die Anzahl von Fehlern an, die in den letzten fünf Sekunden erkannt wurden. |
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/monitor-icon.png" border="false"::: | Überprüfen Sie die Details zu den bei Ihrem Plug-In erkannten Malforms (Falschformatierungen) und Warnungen. |

### <a name="plugin-performance-details"></a>Details zur Plug-In-Leistung

Sie können die Echtzeit-Plug-In-Leistung überwachen, indem Sie die Anzahl der bei Ihrem Plug-In erkannten Malforms und Warnungen analysieren. Eine Option steht zur Verfügung, um den Bildschirm einzufrieren und zur weiteren Untersuchung zu exportieren.

:::image type="content" source="media/how-to-manage-proprietary-protocols/snmp-monitor.png" alt-text="Screenshot des SNMP-Monitors – Übersicht":::

### <a name="horizon-logs"></a>Horizon-Protokolle

Horizon-Analyse-Informationen stehen zum Export in den Analysedetails, Analyseprotokollen und Exportprotokollen zur Verfügung.

:::image type="content" source="media/how-to-manage-proprietary-protocols/export-logs-details.png" alt-text="Analysedetails zu den Exportprotokollen":::

## <a name="trigger-horizon-alerts"></a>Auslösen von Horizon-Warnungen 

Verbessern Sie die Warnungsverwaltung in Ihrem Unternehmen durch Auslösen von benutzerdefinierten Warnungen für ein beliebiges Protokoll, die auf Netzwerkdatenverkehr-Zergliederungen des Horizon-Frameworks basieren. 

Diese Warnungen können zur Übermittlung von Informationen verwendet werden:  

  - Informationen zu Erkennungen von Datenverkehr basierend auf Protokollen und zugrunde liegenden Protokollen in einem proprietären Horizon-Plug-In.

  - Informationen zu einer Kombination von Protokollfeldern aus allen Protokollebenen. Beispielsweise können Sie in einer Umgebung, in der MODBUS ausgeführt wird, eine Warnung generieren, wenn der Sensor einen Schreibbefehl in einem Speicherregister für eine bestimmte IP-Adresse und ein Ethernet-Ziel erkennt, oder eine Warnung, wenn auf eine bestimmte IP-Adresse zugegriffen wird.

Warnungen werden ausgelöst, wenn die Bedingungen für Horizon-Warnungsregeln erfüllt sind.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/custom-alert-rules.png" alt-text="Beispiel: Benutzerdefinierte Regeln für Horizon":::

Wenn Sie mit benutzerdefinierten Horizon-Warnungen arbeiten, können Sie außerdem Ihre eigenen Warnungstitel und Warnmeldungen schreiben. Protokollfelder und aufgelöste Werte können ebenfalls in den Text der Warnmeldung eingebettet werden.

Mithilfe von benutzerdefinierter, bedingungsbasierter Warnungsauslösung und Messaging können Sie bestimmte Netzwerkaktivitäten ermitteln sowie Ihre Sicherheits-, IT- und Betriebsteams effektiv aktualisieren.

### <a name="working-with-horizon-alerts"></a>Arbeiten mit Horizon-Warnungen

Durch benutzerdefinierte Horizon-Warnungsregeln generierte Warnungen werden im Fenster „Warnungen“ des Sensors und der Verwaltungskonsole sowie in integrierten Partnersystemen bei Verwendung von Weiterleitungsregeln angezeigt. 

Von Horizon generierte Warnungen können bestätigt oder stummgeschaltet werden. Die Option „Learn“ steht bei benutzerdefinierten Warnungen nicht zur Verfügung, da die Warnungsereignisse in der Richtlinien-Baseline nicht gelernt werden können.

Wenn Weiterleitungsregeln verwendet werden, werden Warninformationen an Partneranbieter weitergeleitet.

Der Schweregrad für benutzerdefinierte Horizon-Warnungen ist kritisch.

Benutzerdefinierte Horizon-Warnungen enthalten im Abschnitt **Verwalten dieses Ereignisses** statischen Text, der angibt, dass die Warnung vom Sicherheitsteam Ihrer Organisation generiert wurde.

### <a name="required-permissions"></a>Erforderliche Berechtigungen

Benutzer, die als Defender für IoT-Benutzer definiert wurden, haben die Berechtigung zum Erstellen von benutzerdefinierten Horizon-Warnungsregeln.

### <a name="about-creating-rule-conditions"></a>Informationen zum Erstellen von Regelbedingungen

Regelbedingungen beschreiben den Netzwerkdatenverkehr, der erkannt werden sollte, damit die Warnung ausgelöst wird. Regelbedingungen können eine oder mehrere Gruppen von Feldern, Operatoren und Werten umfassen. Erstellen Sie Bedingungssätze mithilfe von **UND**.

Wenn die Regelbedingung oder der Bedingungssatz erfüllt ist, wird die Warnung gesendet. Falls die Bedingungslogik ungültig ist, werden Sie entsprechend benachrichtigt.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/and-condition.png" alt-text="Verwenden Sie für Ihre benutzerdefinierte Regel die Bedingung „UND“.":::

Sie können für ein einziges Protokoll auch mehrere Regeln erstellen. Dies bedeutet: Wenn die Regelbedingungen erfüllt sind, wird bei jeder von Ihnen erstellten Regel eine Warnung ausgelöst.

### <a name="about-titles-and-messages"></a>Informationen zu Titeln und Nachrichten

Warnmeldungen können die von Ihnen eingegebenen alphanumerischen Zeichen sowie erkannte Datenverkehrsvariablen enthalten. Beziehen Sie beispielsweise die erkannten Quell- und Zieladressen in die Warnmeldungen ein. Verschiedene Sprachen werden unterstützt.

### <a name="about-alert-recommendations"></a>Informationen zu Warnungsempfehlungen 

Benutzerdefinierte Horizon-Warnungen enthalten im Abschnitt **Verwalten dieses Ereignisses** statischen Text, der angibt, dass die Warnung vom Sicherheitsteam Ihrer Organisation generiert wurde. Sie können auch mit Warnkommentaren arbeiten und so die Kommunikation zwischen Einzelpersonen und Teams verbessern, die Ihre Warnung lesen. 

## <a name="create-horizon-alert-rules"></a>Erstellen von Horizon-Warnungsregeln

In diesem Artikel wird beschrieben, wie die Warnungsregel erstellt wird.

So erstellen Sie benutzerdefinierte Horizon-Warnungen:

1. Klicken Sie in der Horizon-Konsole im Menü „Plug-Ins“mit der rechten Maustaste auf das gewünschte Plug-In.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/plugins-menu.png" alt-text="Klicken Sie im Menü mit der rechten Maustaste auf das gewünschte Plug-In.":::

2. Wählen Sie **Horizon Custom Alerts** (Benutzerdefinierte Horizon-Warnungen) aus. Das Fenster **Regel** für das ausgewählte Plug-In wird geöffnet.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/sample-rule-window.png" alt-text="Das Fenster „Regel“ für Ihr Plug-In wird geöffnet.":::

3. Geben Sie im Feld „Titel“ einen Titel ein.

4. Geben Sie im Feld „Nachricht“ eine Warnmeldung ein. Schließen Sie erkannte Feldparameter in der Nachricht in geschweifte Klammern `{}` ein. Wenn Sie die erste Klammer eingeben, werden relevante Felder angezeigt.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/rule-window.png" alt-text="Verwenden Sie {} im Regelfenster, um erkannte Felder einzubeziehen.":::

5. Definieren Sie Warnungsbedingungen.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/define-conditions.png" alt-text="Definieren Sie die Bedingungen für die Warnung.":::

6. Wählen Sie eine **Variable** aus. Variablen stellen die im Plug-In konfigurierten Felder dar.

7. Wählen Sie einen **Operator** aus:

    - Gleich
    
    - Ungleich
    
    - Kleiner als
    
    - Kleiner als oder gleich
    
    - Größer als
    
    - Größer als oder gleich

8. Geben Sie einen **Wert** als Zahl ein. Wenn die ausgewählte Variable eine MAC-Adresse oder IP-Adresse ist, muss der Wert von einer Adresse in Dezimalpunktschreibweise in ein Dezimalformat konvertiert werden. Verwenden Sie ein Konvertierungstool für IP-Adressen, z. B <https://www.ipaddressguide.com/ip>.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/ip-address-value.png" alt-text="Wert der übersetzten IP-Adresse":::

9. Wählen Sie **UND** aus, um einen Bedingungssatz zu erstellen.

10. Wählen Sie **SAVE** (SPEICHERN) aus. Die Regel wird dem Abschnitt „Regeln“ hinzugefügt.

### <a name="edit-and-delete-horizon-custom-alert-rules"></a>Bearbeiten und Löschen von benutzerdefinierten Warnungsregeln für Horizon

Verwenden Sie Optionen zum Bearbeiten und Löschen nach Bedarf. Bestimmte Regeln werden eingebettet und können nicht bearbeitet oder gelöscht werden.

### <a name="create-multiple-rules"></a>Erstellen von mehreren Regeln

Beim Erstellen von mehreren Regeln werden Warnungen ausgelöst, wenn eine beliebige Regelbedingung gültig ist oder Bedingungssätze gültig sind.

## <a name="see-also"></a>Weitere Informationen

[Anzeigen der in Warnungen angegebenen Informationen](how-to-view-information-provided-in-alerts.md)
