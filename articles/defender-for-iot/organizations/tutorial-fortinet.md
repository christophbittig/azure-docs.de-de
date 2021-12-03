---
title: Integrieren von Fortinet in Microsoft Defender für loT
description: In diesem Tutorial erfahren Sie, wie Sie Microsoft Defender für loT in Fortinet integrieren.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0556cab649a87b7c0309671acc60d9756a3d03c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283655"
---
# <a name="tutorial-integrate-fortinet-with-microsoft-defender-for-iot"></a>Tutorial: Integrieren von Fortinet in Microsoft Defender für loT

Anhand dieses Tutorials lernen Sie, wie Sie Fortinet in Microsoft Defender für loT integrieren und nutzen.

Microsoft Defender für IoT verringert das IIoT- sowie ICS- und SCADA-Risiko mit ICS-fähigen selbstlernenden Engines, die sofortige Einblicke in ICS-Geräte, Sicherheitsrisiken und Bedrohungen liefern.  Defender für IoT erreicht dies, ohne sich auf Agents, Regeln, Signaturen, spezielle Fähigkeiten oder Vorkenntnisse zur Umgebung verlassen zu müssen.

Defender für IoT und Fortinet sind eine technologische Partnerschaft eingegangen, die Angriffe auf IoT- und ICS-Netzwerke erkennt und stoppt.

Fortinet und Microsoft Defender für IoT verhindern Folgendes:

- Nicht autorisierte Änderungen an speicherprogrammierbaren Steuerungen (SPS, Programmable Logic Controller, PLC).

- Schadsoftware, mit der ICS- und IoT-Geräte über deren native Protokolle verändert werden.

- Sammeln von Daten durch Aufklärungstools

- Protokollverletzungen durch Fehlkonfigurationen oder böswillige Angreifer.

Defender für IoT erkennt anomales Verhalten in IoT- und ICS-Netzwerken und übermittelt diese Informationen folgendermaßen an FortiGate und FortiSIEM:

- **Transparenz:** Die von Defender für IoT übermittelten Informationen bieten FortiSIEM-Administratoren Einblick in zuvor nicht sichtbare IoT- und ICS-Netzwerke.

- **Blockieren bösartiger Angriffe:** FortiGate-Administratoren können mithilfe der von Defender für IoT erkannten Informationen Regeln erstellen, um anomales Verhalten zu verhindern – unabhängig davon, ob dieses Verhalten durch chaotische Akteure oder falsch konfigurierte Geräte verursacht wird –, bevor Schäden an der Produktion, am Gewinn oder für Menschen entstehen.

FortiSIEM und die Fortinet-Lösung zur Verwaltung von Sicherheitsvorfällen und -ereignissen für Geräte verschiedener Hersteller vereinen Sichtbarkeit, Korrelation, automatische Reaktion und Wartung in einer einzigen skalierbaren Lösung.

Durch eine zentrale Ansicht aller Geschäftsdienste lässt sich die Komplexität der Verwaltung von Netzwerk- und Sicherheitsvorgängen reduzieren. Dadurch werden nicht nur Ressourcen freigesetzt, sondern auch die Erkennung von Sicherheitsverletzungen wird verbessert. FortiSIEM bietet Kreuzkorrelationen bei Anwendung von maschinellem Lernen und UEBA zur Beschleunigung der Reaktion auf Bedrohungen, sodass Sicherheitsverletzungen verhindert werden, bevor sie auftreten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Erstellen eines API-Schlüssels in Fortinet
> - Festlegen einer Weiterleitungsregel zum Blockieren von Warnungen zu Schadsoftware
> - Blockieren der Quelle von verdächtigen Warnungen
> - Senden von Defender für IoT-Warnungen an FortiSIEM
> - Blockieren einer schädlichen Quelle mithilfe der Fortigate-Firewall

Wenn Sie noch nicht über ein Azure-Konto verfügen, können Sie [noch heute Ihr kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Voraussetzungen

Es gibt keine Voraussetzungen für dieses Tutorial.

## <a name="create-an-api-key-in-fortinet"></a>Erstellen eines API-Schlüssels in Fortinet

Ein API-Schlüssel (Application Programming Interface, Anwendungsprogrammierschnittstelle) ist ein eindeutig generierter Code, der es einer API ermöglicht, die Anwendung oder den Benutzer zu identifizieren, die bzw. der Zugriff darauf anfordert. Ein API-Schlüssel ist erforderlich, damit Microsoft Defender für Iot und Fortinet ordnungsgemäß kommunizieren können.

**So erstellen Sie einen API-Schlüssel in Fortinet**:

1. Navigieren Sie in FortiGate zu **System** > **Administratorprofile**.

1. Erstellen Sie ein Profil mit den folgenden Berechtigungen:

    | Parameter | Auswahl |
    |--|--|
    | **Security Fabric** | Keine |
    | **Fortiview** | Keine |
    | **Benutzer und Gerät** | Keine |
    | **Firewall** | Benutzerdefiniert |
    | **Richtlinie** | Lesen/Schreiben |
    | **Adresse** | Lesen/Schreiben  |
    | **Dienst** | Keine |
    | **Zeitplan** | Keine |
    | **Protokolle und Bericht** | Keine |
    | **Network** | Keine |
    | **System** | Keine |
    | **Sicherheitsprofil** | Keine |
    | **VPN** | Keine |
    | **WAN-Opt und Cache** | Keine |
    | **WLAN und Switch** | Keine |

1. Navigieren Sie zu **System** > **Administratoren**, und erstellen Sie einen neuen **REST-API-Administrator** mit den folgenden Feldern:

    | Parameter | BESCHREIBUNG |
    | --------- | ----------- |
    | **Benutzername** | Geben Sie den Namen für die Weiterleitungsregel ein. |
    | **Kommentare** | Geben Sie den Mindestschweregrad für die Weiterleitung von Vorfällen ein. Wenn z. B. **Gering** ausgewählt ist, werden Warnungen mit einem geringen Schweregrad und alle Warnungen mit höheren Schweregraden weitergeleitet. |
    | **Administrator Profile** (Administratorprofil) | Wählen Sie in der Dropdownliste den Namen des Profils aus, das Sie im vorherigen Schritt definiert haben. |
    | **PKI Group** (PKI-Gruppe) | Verschieben Sie den Umschalter auf **Deaktivieren**. |
    | **CORS Allow Origin** (CORS-Ursprung zulassen) | Verschieben Sie den Umschalter auf **Aktivieren**. |
    | **Restrict login to trusted hosts** (Anmeldung auf vertrauenswürdige Hosts beschränken) | Fügen Sie die IP-Adressen der Sensoren und Verwaltungskonsolen hinzu, die eine Verbindung mit FortiGate herstellen werden. |

Wenn der API-Schlüssel generiert wurde, speichern Sie ihn, weil er nicht erneut bereitgestellt wird.

:::image type="content" source="media/tutorial-fortinet/api-key.png" alt-text="Screenshot: Beschreibung zum automatischen Generieren des neuen API-Schlüssels":::

## <a name="set-a-forwarding-rule-to-block-malware-related-alerts"></a>Festlegen einer Weiterleitungsregel zum Blockieren von Warnungen zu Schadsoftware

Die FortiGate-Firewall kann zum Blockieren von verdächtigem Datenverkehr verwendet werden.

**So legen Sie eine Weiterleitungsregel zum Blockieren von Warnungen zu Schadsoftware fest**:

1. Melden Sie sich bei der Verwaltungskonsole von Microsoft Defender für IoT an.

1. Wählen Sie im linken Bereich die Option **Weiterleitung** aus.

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="Screenshot: Fenster der Option „Weiterleitung“ in einem Sensor":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

1. Wählen Sie **Weiterleitungsregeln erstellen** aus, und definieren Sie die folgenden Regelparameter:

    | Parameter | BESCHREIBUNG |
    | --------- | ----------- |
    | **Name** | Geben Sie einen aussagekräftigen Namen für die Weiterleitungsregel ein. |
    | **Schweregrad** | Wählen Sie im Dropdownmenü den Mindestschweregrad für die Weiterleitung von Vorfällen aus. Wenn z. B. **Gering** ausgewählt ist, werden Warnungen mit einem geringen Schweregrad und alle Warnungen mit höheren Schweregraden weitergeleitet. |
    | **Protokolle** | Wenn Sie ein bestimmtes Protokoll angeben möchten, wählen Sie **Spezifisch** und dann das Protokoll aus, auf das diese Regel angewendet wird. Standardmäßig sind alle Protokolle ausgewählt. |
    | **Engines** | Wenn Sie eine bestimmte Sicherheits-Engine auswählen möchten, auf die diese Regel angewendet wird, wählen Sie **Spezifisch** und dann die Engine aus. Standardmäßig werden alle Sicherheits-Engines verwendet. |
    | **Systembenachrichtigungen** | Leiten Sie den *Online*- und *Offline*-Status des Sensors weiter. Diese Option ist nur verfügbar, wenn Sie bei der lokalen Verwaltungskonsole angemeldet sind. |

1. Wählen Sie im Abschnitt „Aktionen“ die Option **Hinzufügen** und dann im Dropdownmenü **Send to FortiGate** (An FortiGate senden) aus.

    :::image type="content" source="media/tutorial-fortinet/fortigate.png" alt-text="Screenshot des Abschnitts „Aktion hinzufügen“ im Fenster „Weiterleitungsregel erstellen“":::

1. Legen Sie zum Konfigurieren der Weiterleitungsregel für FortiGate die folgenden Parameter fest:

    :::image type="content" source="media/tutorial-fortinet/configure.png" alt-text="Screenshot des Fensters zum Konfigurieren von „Weiterleitungsregel erstellen“":::

    | Parameter | BESCHREIBUNG |
    |--|--|
    | **Host** | Geben Sie die IP-Adresse für den FortiGate-Server ein. |
    | **API-Schlüssel** | Geben Sie den [API-Schlüssel](#create-an-api-key-in-fortinet) ein, den Sie in FortiGate erstellt haben. |
    | **Incoming Interface (Eingehende Schnittstelle)** | Geben Sie den Port für die eingehende Firewallschnittstelle ein. |
    | **Outgoing Interface (Ausgehende Schnittstelle)** | Geben Sie den Port für die ausgehende Firewallschnittstelle ein. |
    | **Konfigurieren**| Vergewissern Sie sich, dass bei den folgenden Optionen ein **√** angezeigt wird, um die Blockierung von verdächtigen Quellen über die FortiGate-Firewall zu aktivieren: <br> - **Block illegal function codes** (Ungültige Funktionscodes blockieren): Protokollverletzungen: Ungültiger Feldwert, der gegen die ICS-Protokollspezifikation verstößt (potenzieller Exploit) <br /> - **Block unauthorized PLC programming/firmware updates** (Nicht autorisierte PLC-Programmierung/Firmwareupdates blockieren): Nicht autorisierte SPS-Änderungen <br /> - **Block unauthorized PLC stop** (Nicht autorisierte SPS-Beendigung blockieren): SPS-Beendigung (Downtime) <br> - **Block malware-related alerts** (Warnungen zu Schadsoftware blockieren): Blockierung der Angriffsversuche durch industrielle Schadsoftware (TRITON, NotPetya usw.) <br> -  **(Optional)** Sie können die Option für **Automatische Blockierung** auswählen. Wenn „Automatische Blockierung“ ausgewählt ist, wird die Blockierung automatisch und sofort ausgeführt. <br /> - **Block unauthorized scanning** (Nicht autorisierte Überprüfung blockieren): Nicht autorisierte Überprüfung (potenzielle Aufklärung) |

1. Klicken Sie auf **Submit** (Senden).

## <a name="block-the-source-of-suspicious-alerts"></a>Blockieren der Quelle von verdächtigen Warnungen

Die Quelle von verdächtigen Warnungen kann blockiert werden, um weitere Vorkommen zu verhindern.

**So blockieren Sie die Quelle von verdächtigen Warnungen**:

1. Melden Sie sich bei der Verwaltungskonsole an, und wählen Sie im Menü auf der linken Seite **Warnungen** aus.

1. Wählen Sie die auf die Fortinet-Integration bezogene Warnung aus.

1. Zum automatischen Blockieren der verdächtigen Quelle wählen Sie **Block Source** (Quelle blockieren) aus.

    :::image type="content" source="media/tutorial-fortinet/block-source.png" alt-text="Screenshot des Fensters „Warnung“":::

1. Wählen Sie im Dialogfeld Bitte bestätigen die Option **OK** aus.

## <a name="send-defender-for-iot-alerts-to-fortisiem"></a>Senden von Defender für IoT-Warnungen an FortiSIEM

Defender für IoT-Warnungen enthalten Informationen zu einem umfangreichen Spektrum an Sicherheitsereignissen, beispielsweise:

- Abweichungen von der erlernten Baseline für Netzwerkaktivitäten

- Schadsoftwareerkennungen

- Erkennungen auf der Grundlage verdächtiger Ablaufänderungen

- Netzwerkanomalien

- Abweichungen von Protokollspezifikationen

Sie können Defender für IoT so konfigurieren, dass Warnungen an den FortiSIEM-Server gesendet werden. Dort werden die Warnungsinformationen im Fenster „Analytics“ (Analyse) angezeigt:

:::image type="content" source="media/tutorial-fortinet/analytics.png" alt-text="Screenshot des Fensters „Analytics“ (Analyse)":::

Alle Defender für IoT-Warnungen werden dann ohne weitere Konfiguration in FortiSIEM analysiert und dort als sicherheitsrelevante Ereignisse angezeigt. Die folgenden Ereignisdetails werden standardmäßig angezeigt:

:::image type="content" source="media/tutorial-fortinet/event-detail.png" alt-text="Screenshot: Anzeigen Ihrer Ereignisdetails im Fenster „Event Details“ (Ereignisdetails)":::

Sie können dann Weiterleitungsregeln in Defender für IoT verwenden, um Warnungsinformationen an FortiSIEM zu senden.

**So verwenden Sie Weiterleitungsregeln in Defender für IoT zum Senden von Warnungsinformationen an FortiSIEM**:

1. Wählen Sie im Sensor oder im linken Bereich der Verwaltungskonsole die Option **Weiterleitung** aus.

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="Screenshot: Ansicht Ihrer Weiterleitungsregeln im Fenster „Weiterleitung“":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

2. Wählen Sie **Create Forwarding Rules** (Weiterleitungsregeln erstellen) aus, und definieren Sie die Parameter für die Regel.

    | Parameter | Beschreibung |
    |--|--|
    | **Name** | Geben Sie einen aussagekräftigen Namen für die Weiterleitungsregel ein. |
    | **Schweregrad** | Wählen Sie den Mindestschweregrad für die Weiterleitung von Vorfällen aus. Wenn z. B. **Gering** ausgewählt ist, werden Warnungen mit einem geringen Schweregrad und alle Warnungen mit höheren Schweregraden weitergeleitet. |
    | **Protokolle** | Wenn Sie ein bestimmtes Protokoll angeben möchten, wählen Sie **Spezifisch** und dann das Protokoll aus, auf das diese Regel angewendet wird. Standardmäßig sind alle Protokolle ausgewählt. |
    | **Engines** | Wenn Sie eine bestimmte Sicherheits-Engine auswählen möchten, für die die Regel angewandt wird, wählen Sie **Spezifisch** und dann die Engine aus. Standardmäßig werden alle Sicherheits-Engines verwendet. |
    | **Systembenachrichtigungen** | Leiten Sie den *Online*- oder *Offline*-Status eines Sensors weiter. Diese Option ist nur verfügbar, wenn Sie bei der lokalen Verwaltungskonsole angemeldet sind. |

3. Wählen Sie im Abschnitt „Aktionen“ die Option **Send to FortiSIEM** (An FortiSIEM senden) aus.

    :::image type="content" source="media/tutorial-fortinet/forward-rule.png" alt-text="Screenshot: Erstellen einer Weiterleitungsregel und Auswählen von „Send to Fortinet“ (An Fortinet senden)":::

4. Geben Sie die Details für den FortiSIEM-Server ein.

    :::image type="content" source="media/tutorial-fortinet/details.png" alt-text="Screenshot: Hinzufügen der FortiSIEM-Details zur Weiterleitungsregel":::

    | Parameter | BESCHREIBUNG |
    | --------- | ----------- |
    | **Host** | Geben Sie die IP-Adresse für den FortiSIEM-Server ein. |
    | **Port** | Geben Sie den FortiSIEM-Serverport ein. |
    | **Zeitzone** | Zeitstempel für die Warnungserkennung |

5. Klicken Sie auf **Submit** (Senden).

## <a name="block-a-malicious-source-using-the-fortigate-firewall"></a>Blockieren einer schädlichen Quelle mithilfe der Fortigate-Firewall

Sie können Richtlinien festlegen, damit schädliche Quellen in der FortiGate-Firewall mithilfe von Warnungen in Defender für IoT automatisch blockiert werden.

:::image type="content" source="media/tutorial-fortinet/firewall.png" alt-text="Screenshot: Ansicht des Fensters für die FortiGate-Firewall":::

Beispielsweise kann die schädliche Quelle durch folgende Warnung blockiert werden:

:::image type="content" source="media/tutorial-fortinet/suspicion.png" alt-text="Screenshot: Fenster „NotPetya Malware suspicion“ (NotPetya Malware-Verdacht)":::

**So legen Sie eine FortiGate-Firewallregel fest, die eine schädliche Quelle blockiert**:

1. Zuerst müssen Sie in FortiGate [einen API-Schlüssel erstellen](#create-an-api-key-in-fortinet).

1. Melden Sie sich beim Defender für IoT-Sensor oder bei der Verwaltungskonsole an, wählen Sie **Weiterleitung** aus, und [legen Sie eine Weiterleitungsregel fest, die Warnungen zu Schadsoftware blockiert](#set-a-forwarding-rule-to-block-malware-related-alerts).

1. Wählen Sie im Defender für IoT-Sensor oder in der Verwaltungskonsole **Warnungen** aus, und [blockieren Sie eine schädliche Quelle](#block-a-malicious-source-using-the-fortigate-firewall).

1. Navigieren Sie zum FortiGage-Fenster **Administrator**, und suchen Sie die Adresse der schädlichen Quelle, die Sie blockiert haben.

   :::image type="content" source="media/tutorial-fortinet/administrator.png" alt-text="Screenshot: Ansicht des FortiGate-Fensters „Administrator“":::

   Die Blockierungsrichtlinie wird automatisch erstellt und dann im FortiGate-Fenster mit der IPv4-Richtlinie angezeigt.

   :::image type="content" source="media/tutorial-fortinet/policy.png" alt-text="Screenshot: Ansicht des FortiGate-Fensters mit der IPv4-Richtlinie ":::

1. Wählen Sie die Richtlinie aus, und sorgen Sie dafür, dass „Enable this policy“ (Diese Richtlinie aktivieren) auf die Position „Ein“ geschaltet wird.

   :::image type="content" source="media/tutorial-fortinet/edit.png" alt-text="Screenshot: Ansicht des FortiGate-Fensters zum Bearbeiten der IPv4-Richtlinie":::

    | Parameter | BESCHREIBUNG|
    |--|--|
    | **Name** | Der Name der Richtlinie. |
    | **Incoming Interface (Eingehende Schnittstelle)** | Hierbei handelt es sich um die eingehende Firewallschnittstelle für den Datenverkehr. |
    | **Outgoing Interface (Ausgehende Schnittstelle)** | Hierbei handelt es sich um die ausgehende Firewallschnittstelle für den Datenverkehr. |
    | **Quelle** | Hierbei handelt es sich um Quelladresse(n) für den Datenverkehr. |
    | **Ziel** | Hierbei handelt es sich um Zieladresse(n) für den Datenverkehr. |
    | **Zeitplan** | Hier wird festgelegt, wann die neu definierte Regel gilt. Beispiel: `always`. |
    | **Service** | Hierbei handelt es sich um das Protokoll oder bestimmte Ports für den Datenverkehr. |
    | **Aktion** | Hierbei handelt es sich um die Aktion, die die Firewall ausführt. |

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mit der Fortinet-Integration beginnen können. Lesen Sie weiter, um mehr über unsere Palo Alto-Integration zu erfahren.

> [!div class="nextstepaction"]
> [Schaltfläche „Nächste Schritte“](./tutorial-palo-alto.md)
