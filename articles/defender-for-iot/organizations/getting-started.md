---
title: 'Schnellstart: Erste Schritte'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie sich mit dem grundlegenden Workflow für die Bereitstellung von Defender für IoT vertraut machen.
ms.topic: quickstart
ms.date: 06/06/2021
ms.openlocfilehash: cae5b1db400cebe341463dbc752fe5559b779380
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437661"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Schnellstart: Erste Schritte mit Defender für IoT

Dieser Artikel bietet eine Übersicht über die erforderlichen Schritte für die Einrichtung von Azure Defender für IoT. Für diesen Prozess ist Folgendes erforderlich:

- Registrieren Ihres Abonnements und der Sensoren im Portal für Azure Defender für IoT
- Installieren der Software für die Sensor- und die lokale Verwaltungskonsole
- Durchführen der ersten Aktivierung der Sensor- und Verwaltungskonsole

## <a name="permission-requirements"></a>Berechtigungsanforderungen

### <a name="for-sensors-and-on-premises-management-consoles"></a>Für Sensoren und lokale Verwaltungskonsolen

Für einige der Einrichtungsschritte sind bestimme Benutzerberechtigungen erforderlich.

Benutzerberechtigungen eines Administrators sind erforderlich, um die Sensor- und Verwaltungskonsole zu aktivieren, SSL-/TLS-Zertifikate hochzuladen und neue Kennwörter zu generieren.
### <a name="for-the-defender-for-iot-portal"></a>Für das Defender für IoT-Portal

In der folgenden Tabelle werden die Benutzerzugriffsberechtigungen für Tools im Portal für Azure Defender für IoT beschrieben:

| Berechtigung | Sicherheitsleseberechtigter | Sicherheitsadministrator | Abonnementmitwirkender | Abonnementbesitzer |
|--|--|--|--|--|
| Anzeigen von Details und Zugreifen auf Software, Aktivierungsdateien und Threat Intelligence-Pakete  | ✓ | ✓ | ✓ | ✓ |
| Integrieren von Sensoren  |  |  ✓ | ✓ | ✓ |
| Integrieren von Abonnements und Aktualisieren von committeten Geräten  |  |  | ✓ | ✓ |
| Wiederherstellen von Kennwörtern  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identifizieren der Lösungsinfrastruktur

**Klären Ihrer Netzwerkeinrichtungsanforderungen**

Ermitteln Sie Folgendes:

- Netzwerkarchitektur
- Überwachte Bandbreite
- Anforderungen zum Erstellen von Zertifikaten
- Weitere Netzwerkdetails

Weitere Informationen finden Sie unter [Informationen zur Netzwerkeinrichtung für Azure Defender für IoT](how-to-set-up-your-network.md).

**Ermitteln der zur Verarbeitung der Netzwerklast erforderlichen Sensor- und Verwaltungsappliances**

Azure Defender für IoT unterstützt sowohl physische als auch virtuelle Bereitstellungen. Für physische Bereitstellungen können Sie verschiedene zertifizierte Appliances erwerben. Weitere Informationen finden Sie unter [Ermitteln erforderlicher Appliances](how-to-identify-required-appliances.md).

Sie sollten die ungefähre Anzahl der Geräte berechnen, die überwacht werden. Wenn Sie später Ihr Azure-Abonnement im Portal registrieren, müssen Sie diese Zahl eingeben. Zahlen können in Intervallen von 1.000 hinzugefügt werden, z. B. „1000“, „2000“, „3000“. Die Anzahl überwachter Geräte wird als *committete Geräte* bezeichnet.

## <a name="register-with-azure-defender-for-iot"></a>Registrieren in Azure Defender für IoT

Zur Registrierung gehört Folgendes:

- Durchführen des Onboardings Ihrer Azure-Abonnements in Azure Defender für IoT
- Definieren committeter Geräte
- Herunterladen einer Aktivierungsdatei für die lokale Verwaltungskonsole

**So führen Sie die Registrierung durch:**

1. Navigieren Sie zum Portal für Azure Defender für IoT.

1. Klicken Sie auf **Onboard subscription** (Onboarding für Abonnement durchführen).

1. Wählen Sie auf der Seite **Preise** ein Abonnement aus, oder erstellen Sie ein neues, und fügen Sie die Anzahl committeter Geräte hinzu.

1. Klicken Sie auf die Registerkarte **Download the on-premises management console** (Lokale Verwaltungskonsole herunterladen), und speichern Sie die heruntergeladene Aktivierungsdatei. Diese Datei enthält die aggregierten committeten Geräte, die Sie definiert haben. Die Datei wird nach der ersten Anmeldung in die Verwaltungskonsole hochgeladen.

Informationen dazu, wie das Offboarding für ein Abonnement durchgeführt wird, finden Sie unter [Offboard a subscription](how-to-manage-subscriptions.md#offboard-a-subscription) (Offboarding für ein Abonnement durchführen).

## <a name="install-and-set-up-the-on-premises-management-console"></a>Installieren und Einrichten der lokalen Verwaltungskonsole

Nach Erwerb Ihrer lokalen Verwaltungskonsolenappliance sind die folgenden Schritte erforderlich:

- Herunterladen des ISO-Pakets im Portal für Azure Defender für IoT
- Installieren der Software
- Aktivieren und Durchführen der ersten Einrichtung der Verwaltungskonsole

**Führen Sie die folgenden Schritte zur Installation und Einrichtung aus:**

1. Klicken Sie im Portal für Azure Defender für IoT auf **Getting Started** (Erste Schritte).

1. Klicken Sie auf die Registerkarte **On-premises management console** (Lokale Verwaltungskonsole).

1. Wählen Sie eine Version aus, und klicken Sie auf **Herunterladen**.

1. Installieren Sie die Software für die lokale Verwaltungskonsole. Weitere Informationen finden Sie unter [Installation von Defender für IoT](how-to-install-software.md).

1. Aktivieren Sie die Verwaltungskonsole, und richten Sie sie ein. Weitere Informationen finden Sie unter [Aktivieren und Einrichten Ihrer lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Integrieren eines Sensors ##

Sie führen das Onboarding für einen Sensor durch, indem Sie ihn bei Azure Defender für IoT registrieren und eine Sensoraktivierungsdatei herunterladen:

1. Definieren Sie einen Sensornamen, und verknüpfen Sie ihn mit einem Abonnement.

1. Wählen Sie einen Verbindungsmodus für den Sensor aus:

   - **Mit der Cloud verbundene Sensoren:** Die von Sensoren erkannten Informationen werden in der Sensorkonsole angezeigt. Zusätzlich werden Warnungsinformationen über einen IoT-Hub übermittelt und können für andere Azure-Dienste wie Azure Sentinel freigegeben werden.  Sie können Threat Intelligence-Pakete aus dem Portal für Azure Defender für IoT auch automatisch an Ihre Sensoren pushen. Weitere Informationen finden Sie unter [Forschung und Pakete zur Bedrohungsanalyse](how-to-work-with-threat-intelligence-packages.md).

   - **Lokal verwaltete Sensoren**: Die von Sensoren erkannten Informationen werden in der Sensorkonsole angezeigt. Wenn Sie in einem Air-Gap-Netzwerk arbeiten und eine einheitliche Ansicht aller Informationen haben möchten, die von mehreren lokal verwalteten Sensoren erkannt wurden, arbeiten Sie mit der lokalen Verwaltungskonsole.

1. Wählen Sie eine Site zum Zuweisen Ihres Sensors in einem IoT Hub aus. Dabei dient der IoT-Hub als Gateway zwischen diesem Sensor und Azure Defender für IoT. Legen Sie einen Anzeigenamen und eine Zone fest. Sie können auch beschreibende Tags hinzufügen. Der Anzeigename, die Zone und Tags sind beschreibende Einträge auf der [Seite „Standorte und Sensoren“](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors).

1. Wählen Sie **Registrieren**.

1. Wählen Sie **Aktivierungsdatei herunterladen** aus.

Ausführliche Informationen zum Onboarding finden Sie unter [Durchführen des Onboardings für und Verwalten von Sensoren im Defender für IoT-Portal](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Installieren und Einrichten des Sensors

Laden Sie das ISO-Paket im Portal für Azure Defender für IoT herunter, installieren Sie die Software, und richten Sie den Sensor ein.

1. Klicken Sie im Portal für Azure Defender für IoT auf **Getting Started** (Erste Schritte).

1. Klicken Sie auf **Set up sensor** (Sensor einrichten).

1. Wählen Sie eine Version aus, und klicken Sie auf **Herunterladen**.

1. Installieren Sie die Sensorsoftware. Weitere Informationen finden Sie unter [Installation von Defender für IoT](how-to-install-software.md).

1. Aktivieren Sie Ihren Sensor, und richten Sie ihn ein. Weitere Informationen finden Sie unter [Aktivieren und Einrichten eines Sensors](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Verbinden von Sensoren mit einer lokalen Verwaltungskonsole

Verbinden Sie Sensoren mit der Verwaltungskonsole, um Folgendes zu erreichen:

- Sensoren senden Warnungen und Informationen zum Gerätebestand an die lokale Verwaltungskonsole.

- Die lokale Verwaltungskonsole kann Sensorsicherungen durchführen, Warnungen verwalten, die von Sensoren erkannt werden, Verbindungsunterbrechungen von Sensoren untersuchen und andere Aktivitäten für verbundene Sensoren durchführen.

Sie sollten mehrere Sensoren, die dieselben Netzwerke in einer Zone überwachen, gruppieren. So können von mehreren Sensoren erfasste Informationen zusammengefügt werden.

Weitere Informationen finden Sie unter [Verbinden von Sensoren mit der lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Senden von Warnungsinformationen an Azure Sentinel (optional)

Konfigurieren Sie Azure Sentinel so, dass Warnungsinformationen an Azure Sentinel gesendet werden. Unter [Verbinden Ihrer Daten aus Defender für IoT mit Azure Sentinel (Vorschau)](how-to-configure-with-sentinel.md) erhalten Sie weitere Informationen.  

## <a name="next-steps"></a>Nächste Schritte ##

[Einführung in Azure Defender für IoT (Vorschau)](overview.md)

[Architektur von Azure Defender für IoT](architecture.md)
