---
title: Verwalten von Telemetrieeinstellungen in Microsoft Azure Backup Server (MABS)
description: Dieser Artikel enthält Informationen zur Verwaltung der Telemetrieeinstellungen in MABS.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 353aec2b3bf03c7ce06d19db34a6b81f91d2ee12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338873"
---
# <a name="manage-telemetry-settings"></a>Verwalten von Telemetrieeinstellungen

>[!NOTE]
>Dieses Feature gilt ab MABS V3 UR2.

Dieser Artikel enthält Informationen zur Verwaltung der Telemetrieeinstellungen (Diagnose- und Hilfsprogrammdaten) in Microsoft Azure Backup Server (MABS).

Standardmäßig sendet MABS Diagnose- und Konnektivitätsdaten an Microsoft. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität von Microsoft-Produkten und -Diensten sicherzustellen und zu verbessern.

Administratoren können dieses Feature jederzeit deaktivieren. Ausführliche Informationen zu den gesammelten Daten finden Sie im [folgenden Abschnitt](#telemetry-data-collected).

## <a name="turn-onoff-telemetry-from-console"></a>Aktivieren/Deaktivieren der Telemetrie über die Konsole

1. Wechseln Sie in der Microsoft Azure Backup Server-Konsole zu **Verwaltung**, und klicken Sie im oberen Bereich auf **Optionen**.
1. Wählen Sie im Dialogfeld **Optionen** die Option **Einstellungen für Diagnose- und Nutzungsdaten** aus.

    ![Konsole für Telemetrieeinstellungen: Optionen](./media/telemetry/telemetry-options.png)

1. Wählen Sie die gewünschte Option für die Weitergabe von Diagnose- und Nutzungsdaten aus und klicken Sie auf **OK**.

    >[!NOTE]
    >Es empfiehlt sich, die [Datenschutzbestimmungen](https://privacy.microsoft.com/privacystatement) zu lesen, bevor Sie die Option auswählen.
    >- Um die Telemetrie zu aktivieren, wählen Sie **Ja, ich möchte Daten an Microsoft senden**.
    >- Um die Telemetrie zu deaktivieren, wählen Sie **Nein, ich möchte keine Daten an Microsoft senden**.

## <a name="telemetry-data-collected"></a>Gesammelte Telemetriedaten

| Daten im Zusammenhang mit | Gesammelte Daten* |
| --- | --- |
| **Einrichtung** | Installierte Version von MABS <br/><br/>Version des installierten MABS-Updaterollups <br/><br/> Eindeutiger Computerbezeichner <br/><br/> Betriebssystem, unter dem MABS installiert ist <br/><br/> Eindeutiger Bezeichner des Cloudabonnements<br/><br/> MARS-Agent-Version<br/><br/> Angabe, ob mehrstufiger Speicher aktiviert ist <br/><br/> Größe des verwendeten Speichers. |
| **Geschützte Workload** | Eindeutiger Workloadbezeichner <br/><br/>Größe der gesicherten Workload <br/><br/>Workloadtyp und zugehörige Versionsnummer <br/><br/>Angabe, ob die Workload aktuell durch MABS geschützt wird <br/><br/>Eindeutiger Bezeichner der Schutzgruppe, unter der die Workload geschützt wird<br/><br/> Ort, an dem die Workload gesichert wird (Datenträger/Band oder Cloud)|
| **Aufträge** | Zustnd des Sicherungs-/Wiederherstellungsauftrags. <br/><br/> Umfang der gesicherten/wiederhergestellten Daten. <br/><br/>Fehlermeldung, falls der Sicherungs-/Wiederherstellungsauftrag nicht erfolgreich war.<br/><br/> Dauer des Wiederherstellungsauftrags<br/><br/>Details zur Workload, für die der Sicherungs-/Wiederherstellungsauftrag ausgeführt wurde. |
| **Telemetrieänderungsstatus** | Die Statusänderungsdetails für die Telemetrieeinstellungen (Aktivierung/Deaktivierung und zugehöriger Zeitpunkt) |
| **Fehlermeldung bei Absturz der MABS-Konsole** | Die Details des Fehlers beim Absturz einer MABS-Konsole|

## <a name="next-steps"></a>Nächste Schritte

[Schützen von Workloads](./back-up-hyper-v-virtual-machines-mabs.md)