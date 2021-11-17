---
title: Erstellen von Risikobewertungsberichten
description: Gewinnen Sie Erkenntnisse zu Netzwerkrisiken, die von einzelnen Sensoren erkannt wurden, oder eine aggregierte Ansicht der von allen Sensoren erkannten Risiken.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: fbd7dc1883c52f9a5b949ac8088a998a23519d14
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278757"
---
# <a name="risk-assessment-reporting"></a>Berichterstellung zur Risikobewertung

## <a name="about-risk-assessment-reports"></a>Informationen zu Risikobewertungsberichten

Berichte zur Risikobewertung bieten Folgendes:

- Eine Gesamtsicherheitsbewertung für die von den Sensoren in Ihrer Organisation erkannten Geräte.

- Eine Sicherheitsbewertung für jedes Netzwerkgerät, das von einem einzelnen Sensor erkannt wird.

- Eine Aufschlüsselung der Anzahl der gefährdeten Geräte, der Geräte, die Verbesserungen benötigen, und der sicheren Geräte.

-  Erkenntnisse zu Sicherheits- und Betriebsproblemen:

    - Konfigurationsprobleme

    - Sicherheitsrisiko von Geräten, nach Sicherheitsstufe priorisiert

    - Netzwerksicherheitsprobleme

    - Netzwerkbetriebsprobleme

    - Verbindungen mit ICS-Netzwerken

    - Internetverbindungen

    - Indikatoren für industrielle Schadsoftware

    - Protokollprobleme

    - Angriffsvektoren

### <a name="risk-mitigation"></a>Risikominderung

Die Berichte enthalten Empfehlungen, die Ihnen helfen, Ihre Sicherheitsbewertung zu verbessern. Zum Beispiel:
- Installieren Sie die neuesten Sicherheitsupdates.
- Upgraden Sie Firmware auf die neueste Version.
- Untersuchen Sie SPSs in unsicheren Zuständen.

## <a name="about-security-scores"></a>Informationen zu Sicherheitsbewertungen

Die Gesamtbewertung der Netzwerksicherheit wird in jedem Bericht generiert. Das Ergebnis gibt den Prozentsatz bezogen auf 100 %-ige Sicherheit an. Ein Wert von 30 % würde z. B. bedeuten, dass Ihr Netzwerk zu 30 % sicher ist.

Die Ergebnisse der Risikobewertung basieren auf Informationen, die aus der Paketüberprüfung, Engines zur Verhaltensmodellierung und einem SCADA-spezifischen Entwurf von Zustandsautomaten gewonnen wurden.

**Sichere Geräte** sind Geräte mit einer Sicherheitsbewertung von über 90 %.

**Geräte, die der Verbesserung bedürfen**: Geräte mit einer Sicherheitsbewertung zwischen 70 % und 89 %.

**Gefährdete Geräte** sind Geräte mit einer Sicherheitsbewertung von unter 70 %.

### <a name="about-backup-and-anti-virus-servers"></a>Informationen zu Sicherungs- und Antivirenservern

Die Risikobewertung kann beeinträchtigt werden, wenn Sie keine Adressen für Sicherungs- und Antivirenserver in Ihrem Sensor definieren. Das Hinzufügen dieser Adressen verbessert Ihre Bewertung. Standardmäßig sind diese Adressen nicht definiert.
Auf dem Deckblatt des Risikobewertungsberichts wird ggf. angezeigt, dass keine Sicherungsserver und Antivirenserver definiert sind.

**So fügen Sie Server hinzu**

1. Wählen Sie **Systemeinstellungen** und dann **Systemeigenschaften** aus.
1. Wählen Sie **Sicherheitsrisikobewertung** aus, und fügen Sie die Adressen in den Feldern **backup_servers** und **AV_addresses** hinzu. Verwenden Sie Kommas zum Trennen mehrerer Adressen.  Kommas als Trennzeichen  
1. Wählen Sie **Speichern** aus.
## <a name="create-risk-assessment-reports"></a>Erstellen von Risikobewertungsberichten

Erstellen Sie einen PDF-Bericht zur Risikobewertung. Der Name des Berichts wird automatisch als „risk-assessment-report-1.pdf“ generiert. Die Nummer wird für jeden neu erstellten Bericht aktualisiert.  Die Uhrzeit und der Tag der Erstellung werden angezeigt.

### <a name="create-a-sensor-risk-assessment-report"></a>Erstellen eines Sensorberichts zur Risikobewertung

Erstellen Sie einen Risikobewertungsbericht auf der Grundlage der Erkennungen des Sensors, bei dem Sie angemeldet sind.

So erstellen Sie einen Bericht

1. Melden Sie sich bei der Sensorkonsole an.
1. Wählen Sie im seitlichen Menü **Risk Assessment** (Risikobewertung) aus.
1. Wählen Sie **Generate Report** (Bericht generieren) aus. Der Bericht wird im Abschnitt „Archived Reports“ (Archivierte Berichte) angezeigt.
1. Wählen Sie den Bericht aus dem Abschnitt „Archived Reports“ (Archivierte Berichte) aus, um ihn herunterzuladen.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Ansicht der Risikobewertung.":::

So importieren Sie ein Firmenlogo:

- Wählen Sie **Logo importieren** aus.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Erstellen eines Berichts zur Risikobewertung für die lokale Verwaltungskonsole

Erstellen Sie einen Risikobewertungsbericht auf der Grundlage von Erkennungen, die von einem der von Ihrer lokalen Verwaltungskonsole verwalteten Sensoren durchgeführt wurden. 

So erstellen Sie einen Bericht

1. Wählen Sie im seitlichen Menü **Risk Assessment** (Risikobewertung) aus.

2. Wählen Sie in der Dropdownliste **Select Sensor** (Sensor auswählen) einen Sensor aus.

3. Wählen Sie **Generate Report** (Bericht generieren) aus.

4. Wählen Sie im Abschnitt **Archived Reports** (Archivierte Berichte) **Download** (Herunterladen) aus.

So importieren Sie ein Firmenlogo:

- Wählen Sie **Logo importieren** aus.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importieren Sie Ihr Logo über die Ansicht „Risikobewertung“.":::

## <a name="see-also"></a>Weitere Informationen

[Berichte zu Angriffsvektoren](how-to-create-attack-vector-reports.md)
