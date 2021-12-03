---
title: Verwalten von Sensoren mit Defender für IoT im Azure-Portal
description: Hier erfahren Sie, wie Sie im Azure-Portal mit Defender für IoT das Onboarding für Sensoren durchführen sowie Sensoren anzeigen und verwalten.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: c01f5826238a5e4091fbaef58b42205c0bb2d9d2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339895"
---
# <a name="manage-sensors-with-defender-for-iot-in-the-azure-portal"></a>Verwalten von Sensoren mit Defender für IoT im Azure-Portal

In diesem Artikel wird beschrieben, wie Sie im [Azure-Portal mit Defender für IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) das Onboarding für Sensoren durchführen sowie Sensoren anzeigen und verwalten.

## <a name="onboard-sensors"></a>Integrieren von Sensoren

Sie führen das Onboarding für einen Sensor durch, indem Sie ihn bei Microsoft Defender für IoT registrieren und eine Sensoraktivierungsdatei herunterladen.

### <a name="register-the-sensor"></a>Registrieren des Sensors

**So registrieren Sie sich:**

1. Navigieren Sie im Azure-Portal zur [Seite „Defender für IoT: Erste Schritte“](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

1. Wählen Sie **Sensor integrieren** aus.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/onboard-a-sensor.png" alt-text="Auswählen von „Sensor integrieren“ zum Starten des Onboardingprozesses für Ihren Sensor":::

1. Legen Sie einen Sensornamen fest. 

    Es wird empfohlen, die IP-Adresse des installierten Sensors als Teil des Namens anzugeben oder einen leicht identifizierbaren Namen zu verwenden. Dadurch wird eine einfachere Nachverfolgung und konsistente Benennung zwischen dem Registrierungsnamen im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) und der IP-Adresse des bereitgestellten Sensors sichergestellt, der in der Sensorkonsole angezeigt wird.

1. Ordnen Sie den Sensor einem Azure-Abonnement zu.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/name-subscription.png" alt-text="Eingeben eines aussagekräftigen Namens und Herstellen der Verbindung zwischen Ihrem Sensor und einem Abonnement":::

1. Wählen Sie mithilfe der Umschaltfläche **Mit der Cloud verbunden** einen Sensorverbindungsmodus aus. Ist der Umschalter auf „Ein“ festgelegt, ist der Sensor mit der Cloud verbunden. Wenn der Umschalter auf „Aus“ festgelegt ist, wird der Sensor lokal verwaltet.

   - **Mit der Cloud verbundene Sensoren**: Die vom Sensor erkannten Informationen werden in der Sensorkonsole angezeigt. Warnungsinformationen werden über einen IoT-Hub übermittelt und können für andere Azure-Dienste wie z. B. Microsoft Sentinel freigegeben werden. Darüber hinaus können Threat Intelligence-Pakete aus Defender für IoT an Sensoren gepusht werden. Wenn der Sensor hingegen nicht mit der Cloud verbunden ist, müssen Sie Threat Intelligence-Pakete herunterladen und dann auf Ihre Unternehmenssensoren hochladen. Um Defender für IoT das Pushen von Paketen an Sensoren zu ermöglichen, aktivieren Sie die Umschaltfläche **Automatische Threat Intelligence-Updates**. Weitere Informationen finden Sie unter [Forschung und Pakete zur Bedrohungsanalyse](how-to-work-with-threat-intelligence-packages.md).
   
   Bei mit der Cloud verbundenen Sensoren wird in der Sensorkonsole der Name angezeigt, der während des Onboardings definiert wurde. Sie können diesen Namen nicht direkt über die Konsole ändern. Bei lokal verwalteten Sensoren wird der während des Onboardings angewendete Name in Azure gespeichert, dieser kann jedoch in der Sensorkonsole geändert werden.

   - **Lokal verwaltete Sensoren**: Die von Sensoren erkannten Informationen werden in der Sensorkonsole angezeigt. Wenn Sie in einem Air-Gap-Netzwerk arbeiten und eine einheitliche Ansicht aller Informationen haben möchten, die von mehreren lokal verwalteten Sensoren erkannt wurden, arbeiten Sie mit der lokalen Verwaltungskonsole.

1. Wählen Sie eine Site zum Zuweisen Ihres Sensors in einem IoT Hub aus. Dabei dient die IoT Hub-Instanz als Gateway zwischen diesem Sensor und Microsoft Defender für IoT. Legen Sie einen Anzeigenamen und eine Zone fest. Sie können auch beschreibende Tags hinzufügen. Der Anzeigename, die Zone und Tags sind beschreibende Einträge auf der [Seite „Standorte und Sensoren“](#view-onboarded-sensors).

1. Wählen Sie **Registrieren**. 

### <a name="download-the-sensor-activation-file"></a>Herunterladen der Sensoraktivierungsdatei

Nach der Registrierung eines Sensors können Sie eine Aktivierungsdatei herunterladen. Die Sensoraktivierungsdatei enthält Anweisungen zum Verwaltungsmodus des Sensors. Sie können für jeden von Ihnen bereitgestellten Sensor eine eindeutige Aktivierungsdatei herunterladen. Ein Benutzer, der sich zum ersten Mal bei der Sensorkonsole anmeldet, lädt die Aktivierungsdatei auf den Sensor hoch.

**Gehen Sie wie folgt vor, um eine Aktivierungsdatei herunterzuladen:**

1. Wählen Sie auf der Seite **Sensor integrieren** die Option **Registrieren** aus.

1. Wählen Sie **Aktivierungsdatei herunterladen** aus.

1. Stellen Sie die Datei für den Benutzer bereit, der sich zum ersten Mal bei der Sensorkonsole anmeldet.

## <a name="view-onboarded-sensors"></a>Anzeigen integrierter Sensoren

So zeigen Sie wichtige Betriebsinformationen zu integrierten Sensoren an

1. Navigieren Sie im Azure-Portal zu [Defender für IoT: Erste Schritte](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

1. Wählen Sie **Standorte und Sensoren** aus. Die Seite zeigt, für wie viele Sensoren ein Onboarding durchgeführt wurde, wie viele Sensoren mit der Cloud verbunden sind und wie viele Sensoren lokal verwaltet werden. Darüber hinaus werden die folgenden Informationen bereitgestellt:

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/sites-and-sensors.png" alt-text="Auswählen der Seite „Standorte und Sensoren“ zum Anzeigen aller zugehöriger Sensoren":::

    - Der Sensorname, der während des Onboardings zugewiesen wurde
    - Der Verbindungstyp (mit der Cloud verbunden oder lokal verwaltet)
    - Die Zone, die dem Sensor zugeordnet ist
    - Die installierte Sensorversion
    - Der Status der Sensorverbindung mit der Cloud.
    - Der letzte Zeitpunkt, zu dem eine Verbindungsherstellung des Sensors mit der Cloud erkannt wurde.

## <a name="manage-onboarded-sensors"></a>Verwalten integrierter Sensoren

Verwenden Sie das [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) zum Ausführen von Verwaltungsaufgaben für Sensoren.

Integrierte Sensoren können auf der Seite **Standorte und Sensoren** angezeigt werden. Sie können auf dieser Seite auch Sensorinformationen bearbeiten.

### <a name="export-sensor-details"></a>Exportieren von Sensordetails

Wählen Sie zum Exportieren von Informationen zum integrierten Sensor oben auf der Seite **Standorte und Sensoren** das Symbol **Exportieren** aus.

:::image type="content" source="media/how-to-manage-sensors-on-the-cloud/export-sensors.png" alt-text="Schaltfläche „Exportieren“ zum Exportieren der Sensorinformationen":::

### <a name="edit-sensor-zone-details"></a>Bearbeiten von Sensorzonendetails

Verwenden Sie die Bearbeitungsoptionen auf der Seite **Standorte und Sensoren**, um den Sensornamen und die Zone zu bearbeiten.

**So bearbeiten Sie die Informationen**

1. Klicken Sie auf die **Auslassungspunkte** ( **...** ) für den zu bearbeitenden Sensor.

1. Wählen Sie **Bearbeiten** aus.

1. Aktualisieren Sie die Sensorzone, oder erstellen Sie eine neue Zone.

### <a name="delete-a-sensor"></a>Löschen eines Sensors

Wenn Sie einen mit der Cloud verbundenen Sensor löschen, werden keine Informationen an den IoT-Hub gesendet. Löschen Sie lokal verbundene/verwaltete Sensoren, wenn Sie nicht mehr damit arbeiten.

**Gehen Sie wie folgt vor, um einen Sensor zu löschen:**

1. Wählen Sie die Auslassungspunkte ( **...** ) für den zu löschenden Sensor aus.

1. Klicken Sie auf **Sensor löschen**.

### <a name="reactivate-a-sensor"></a>Reaktivieren eines Sensors 

Möglicherweise müssen Sie den Sensor reaktivieren, wenn Sie folgende Schritte ausführen möchten:

- **Arbeiten im mit der Cloud verbundenen Modus statt im lokal verwalteten Modus**: Nach dem erneuten Aktivieren werden die Erkennungen des Sensors im Sensor angezeigt. Neu erkannte Warnungsinformationen werden über den IoT-Hub gesendet. Diese Informationen können über andere Azure-Dienste wie z. B. Microsoft Sentinel freigegeben werden.

- **Arbeiten im lokal verwalteten Modus statt im mit der Cloud verbundenen Modus:** Nach der Reaktivierung werden Informationen zur Sensorerkennung nur im Sensor angezeigt.

- **Zuordnen des Sensors zu einem neuen IoT-Hub**:  Registrieren Sie den Sensor dafür erneut bei einem neuen Hub, und laden Sie dann eine neue Aktivierungsdatei herunter.

**Gehen Sie wie folgt vor, um einen Sensor erneut zu aktivieren:**

1. Wechseln Sie im **Azure-Portal** zur Seite [Standorte und Sensoren](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

1. Wählen Sie den Sensor aus, für den Sie eine neue Aktivierungsdatei hochladen möchten.

1. Klicken Sie auf die **Auslassungspunkte** ( **...** ), und wählen Sie dann **Sensor löschen** aus.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/delete-a-sensor.png" alt-text="Auswählen der Auslassungspunkte und der Option „Sensor löschen“":::

1. Wiederholen Sie das [Onboarding des Sensors](#onboard-sensors) nochmal im neuen Modus oder in einem neuen IoT-Hub, indem Sie auf der Seite „Erste Schritte“ die Option **Sensor integrieren** auswählen.

1. Laden Sie die Aktivierungsdatei herunter.

1. Melden Sie sich bei der Sensorkonsole von Defender für IoT an.

1. Wählen Sie in der Sensorkonsole **Systemeinstellungen** und dann **Reaktivierung** aus.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Hochladen Ihrer Aktivierungsdatei zum erneuten Aktivieren des Sensors":::

1. Wählen Sie **Hochladen** und dann die Datei aus, die Sie auf der Seite „Sensor integrieren“ gespeichert haben.

1. Wählen Sie **Aktivieren** aus.

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren und Einrichten Ihres Sensors](how-to-activate-and-set-up-your-sensor.md)
