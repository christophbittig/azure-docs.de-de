---
title: Verwalten von Abonnements
description: Abonnements bestehen aus verwalteten, bestätigten Geräten und können je nach Bedarf on- oder offboarded werden.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 41240bd3b05c08d8acd3484bbe32fc99b6590d49
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444422"
---
# <a name="manage-defender-for-iot-subscriptions"></a>Verwalten von Defender für IoT-Abonnements

## <a name="about-subscriptions"></a>Informationen zu Abonnements

Ihre Defender für IoT-Bereitstellung wird über Ihre Azure Defender für IoT-Kontoabonnements verwaltet.
Sie können Ihre Abonnements für Defender für IoT über das [Azure Defender für IoT-Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) integrieren, bearbeiten und deaktivieren.

Für jedes Abonnement werden Sie aufgefordert, eine Reihe von *committeten Geräten* zu definieren. Bei committeten Geräten handelt es sich um die ungefähre Anzahl von Geräten, die in Ihrem Unternehmen überwacht werden. 

### <a name="subscription-billing"></a>Abonnementabrechnung

Die Abrechnung basiert auf der Anzahl der committeten Geräte, die jedem Abonnement zugeordnet sind.

Der Abrechnungszyklus für Azure Defender für IoT ist ein Kalendermonat. Änderungen, die Sie im Laufe des Monats an committeten Geräten vornehmen, werden eine Stunde nach der Bestätigung Ihres Updates implementiert und spiegeln sich in Ihrer monatlichen Rechnung wider. Das *Offboarding* von Abonnements wird ebenfalls eine Stunde nach der Bestätigung des Offboardings wirksam.

Ihr Unternehmen kann über mehrere zahlende Entitäten verfügen. In diesem Fall können Sie mehrere Abonnements integrieren.

Bevor Sie abonnieren, sollten Sie wissen, wie viele Geräte Ihre Abonnements abdecken sollen.

Benutzer können auch mit einem Testabonnement arbeiten, das die Überwachung einer begrenzten Anzahl von Geräten für 30 Tage unterstützt.
Informationen zu den Preisen für committete Geräte finden Sie unter [Azure Defender – Preise](https://azure.microsoft.com/pricing/details/azure-defender/).

## <a name="requirements"></a>Requirements (Anforderungen)

Vergewissern Sie sich vor dem Onboarding eines Abonnements über Folgendes:

- Ihr Azure-Konto ist eingerichtet.
- Sie verfügen über die erforderlichen Azure-Benutzerberechtigungen.

### <a name="azure-account-subscription-requirements"></a>Azure-Kontoabonnementanforderungen

Für den Einstieg in Azure Defender für IoT benötigen Sie ein Microsoft Azure-Kontoabonnement.

Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein kostenloses Testkonto registrieren. Weitere Informationen finden Sie unter https://azure.microsoft.com/free/.

Wenn Sie bereits Zugriff auf ein Azure-Abonnement haben, dies aber nicht aufgeführt ist, wenn Sie Defender für IoT abonnieren, überprüfen Sie Ihre Kontodetails, und lassen Sie Ihre Berechtigungen vom Abonnementbesitzer bestätigen. Siehe https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade.

### <a name="user-permission-requirements"></a>Erforderliche Benutzerberechtigungen

Azure-**Abonnementbesitzer** und -**Abonnementmitwirkende** können Azure Defender für IoT-Abonnements integrieren, aktualisieren und das Offboarding für sie durchführen.

## <a name="onboard-a-trial-subscription"></a>Integrieren eines Testabonnements

Wenn Sie Defender für IoT auswerten möchten, können Sie ein Testabonnement verwenden. Die Testversion ist 30 Tage lang gültig und unterstützt 1.000 committete Geräte. Mithilfe der Testversion können Sie einen oder mehrere Defender für IoT-Sensoren in Ihrem Netzwerk bereitstellen. Verwenden Sie die Sensoren, um Datenverkehr zu überwachen, Daten zu analysieren, Warnungen zu generieren, mehr über Netzwerkrisiken und Sicherheitsrisiken zu erfahren und sonstigen Zwecken. Mit der Testversion können Sie auch eine lokale Verwaltungskonsole herunterladen, um von Sensoren generierte aggregierte Informationen anzeigen zu können.

In diesem Abschnitt wird beschrieben, wie Sie ein Testabonnement für einen Sensor erstellen.

**So erstellen Sie ein kostenloses Testabonnement:**

1. Navigieren Sie zum [Portal für Azure Defender für IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Wählen Sie **Erste Schritte** aus.
1. Klicken Sie auf **Onboard subscription** (Onboarding für Abonnement durchführen).
1. Wählen Sie auf der Preiseseite die Option **Mit einer Testversion beginnen** aus.
1. Wählen Sie im Bereich „Testabonnement integrieren“ ein Abonnement und dann **Auswerten** aus.
1. Bestätigen Sie Ihre Auswertung.
1. Integrieren Sie bei Bedarf einen Sensor, oder richten Sie einen Sensor ein.

## <a name="onboard-a-subscription"></a>Onboarding für ein Abonnement durchführen

In diesem Abschnitt wird beschrieben, wie Sie ein Abonnement integrieren.

**Um ein Onboarding für ein Abonnement durchzuführen:**

1. Navigieren Sie zum [Portal für Azure Defender für IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Wählen Sie **Erste Schritte** aus.
1. Klicken Sie auf **Onboard subscription** (Onboarding für Abonnement durchführen).
1. Wählen Sie auf der Preiseseite die Option **Abonnieren**.
1. Wählen Sie im Bereich **Abonnement integrieren** ein Abonnement und die Anzahl der committeten Geräte im Dropdownmenü aus.

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Wählen Sie Ihr Abonnement aus, sowie die Anzahl der bestätigten Geräte.":::

1. Wähle **Abonnieren** aus.
1. Bestätigen Sie Ihr Abonnement.
1. Falls noch nicht geschehen, integrieren Sie einen Sensor, oder richten Sie einen Sensor ein.

## <a name="update-committed-devices-in-a-subscription"></a>Aktualisieren von committeten Geräten in einem Abonnement

Möglicherweise müssen Sie Ihr Abonnement mit mehr committeten Geräten oder einer geringeren Anzahl committeter Geräte aktualisieren. Möglicherweise erfordern mehr Geräte eine Überwachung, wenn Sie z. B. die vorhandene Standortabdeckung erhöhen, mehr Geräte als erwartet entdeckt haben oder Netzwerkänderungen wie das Hinzufügen von Switches vorgenommen werden.

**So aktualisieren Sie ein Abonnement:**
1. Navigieren Sie zum [Portal für Azure Defender für IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Klicken Sie auf **Onboard subscription** (Onboarding für Abonnement durchführen).
1. Wählen Sie das Abonnement und dann die drei Punkte aus. (...).
1. Wählen Sie **Bearbeiten** aus.
1. Aktualisieren Sie die committeten Geräte, und wählen Sie **Speichern** aus.
2. Wählen Sie im angezeigten Bestätigungsdialogfeld **Bestätigungen** aus.
Änderungen an der Gerätefestlegung werden eine Stunde nach der Bestätigung der Änderung wirksam. Die Abrechnung für diese Änderungen wird am Anfang des Monats nach der Bestätigung der Änderung widergespiegelt.

Sie müssen eine neue Aktivierungsdatei in Ihre lokale Verwaltungskonsole hochladen. Die Aktivierungsdatei gibt die neue Anzahl committeter Geräte an. Weitere Informationen finden Sie unter [Hochladen einer Aktivierungsdatei](how-to-manage-the-on-premises-management-console.md#upload-an-activation-file).
## <a name="offboard-a-subscription"></a>Offboarding eines Abonnements

Möglicherweise müssen Sie für ein Abonnement ein Offboarding durchführen, z. B. wenn Sie mit einer neuen Zahlungsentität arbeiten müssen. Das Offboarding von Abonnements wird ebenfalls eine Stunde nach der Bestätigung des Offboardings wirksam.
Ihre kommende monatliche Rechnung spiegelt diese Änderung wider.

Entfernen Sie vor dem Offboarding des Abonnements alle Sensoren, die dem Abonnement zugeordnet sind. Weitere Informationen zum Löschen eines Sensors finden Sie unter [Löschen eines Sensors](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor).

**Offboarding eines Abonnements:**

1. Navigieren Sie zum [Portal für Azure Defender für IoT](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Wählen Sie das Abonnement und dann die drei Punkte aus. (...).

1. Wählen Sie **Offboarding des Abonnements durchführen** aus.

1. Aktivieren Sie im Bestätigungspopupfenster das Kontrollkästchen, um zu bestätigen, dass Sie alle dem Abonnement zugeordneten Sensoren gelöscht haben.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Aktivieren des Kontrollkästchens und Klicken auf „Offboard“ für das Offboarding des Sensors":::

1. Wählen Sie **Offboarding** aus.

## <a name="apply-a-new-subscription"></a>Anwenden eines neuen Abonnements

Geschäftliche Überlegungen erfordern möglicherweise, dass Sie ein anderes Abonnement als das derzeit verwendete Abonnement auf Ihre Bereitstellung anwenden. Wenn Sie das Abonnement ändern, müssen Sie eine neue Sensoraktivierungsdatei hochladen. Die Datei enthält Informationen zum Ablaufdatum des Abonnements.

**So wenden Sie ein neues Abonnement an:**

1. Löschen Sie das derzeit verwendete Abonnement.
1. Wählen Sie ein neues Abonnement aus.
1. Laden Sie eine Aktivierungsdatei für den Sensor herunter, der dem Abonnement zugeordnet ist.
1. Laden Sie die Aktivierungsdatei auf den Sensor hoch.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Sensoren im Defender für IoT-Portal](how-to-manage-sensors-on-the-cloud.md)

- [Aktivieren und Einrichten der lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md)
