---
title: Verwalten von Geräten in Azure AD über das Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie das Azure-Portal verwenden, um Geräteidentitäten zu verwalten und zugehörige Ereignisinformationen zu überwachen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/14/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cdb24ac332530a8294cd6a39b5fe58ab2727ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049936"
---
# <a name="manage-device-identities-by-using-the-azure-portal"></a>Verwalten von Geräteidentitäten mit dem Azure-Portal

Azure Active Directory (Azure AD) stellt einen zentralen Ort zum Verwalten von Geräteidentitäten und zum Überwachen zugehöriger Ereignisinformationen bereit.

[![Screenshot: Geräteübersicht im Azure-Portal.](./media/device-management-azure-portal/devices-azure-portal.png)](./media/device-management-azure-portal/devices-azure-portal.png#lightbox)

Sie können auf die Geräteübersicht zugreifen, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Geräte**.

In der Geräteübersicht finden Sie die Gesamtzahl aller Geräte, veraltete Geräte, nicht kompatible Geräte und nicht verwaltete Geräte. Hier finden Sie auch Links zu Intune, zum bedingten Zugriff, zu BitLocker-Schlüsseln und grundlegenden Überwachungsfunktionen. 

Die Geräteanzahl auf der Übersichtsseite wird nicht in Echtzeit aktualisiert. Änderungen sollten innerhalb von ein paar Stunden widergespiegelt werden.

Von hier aus können Sie zu **Alle Geräte** wechseln, um folgende Aktivitäten auszuführen:

- Identifizieren von Geräten, einschließlich der folgenden:
   - Geräte, die in Azure AD eingebunden oder registriert sind
   - Geräte, die mithilfe von [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) bereitgestellt wurden
   - Drucker, die [Universal Print](/universal-print/fundamentals/universal-print-getting-started) verwenden
- Ausführen von Aufgaben zur Verwaltung von Geräteidentitäten (z. B. Aktivieren, Deaktivieren, Löschen und Verwalten)
   - Die Optionen zur Verwaltung von [Druckern](/universal-print/fundamentals/) und [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) sind in Azure AD eingeschränkt. Diese Geräte müssen über die entsprechenden Verwaltungsoberflächen verwaltet werden.
- Konfigurieren Sie Ihre Geräteidentitätseinstellungen.
- Aktivieren oder Deaktivieren von Enterprise State Roaming
- Überprüfen gerätebezogener Überwachungsprotokolle
- Herunterladen von Geräten (Vorschau).

[![Screenshot: Ansicht „Alle Geräte“ im Azure-Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox).

> [!TIP]
> - In Azure AD hybrid eingebundene Windows 10-Geräte haben keinen Besitzer. Wenn Sie ein Gerät nach Besitzer suchen und es nicht finden, suchen Sie nach der Geräte-ID.
>
> - Wenn in der Spalte **Registriert** ein Gerät mit dem Zusatz **Hybrid in Azure AD eingebunden** und dem Status **Ausstehend** angezeigt wird, deutet das an, dass das Gerät von Azure AD Connect synchronisiert wurde und auf die vollständige Registrierung vom Client wartet. Lesen Sie hierzu auch [Planen der Implementierung einer Azure AD-Hybrideinbindung](hybrid-azuread-join-plan.md). Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Geräteverwaltung](faq.yml).
>
> - Bei einigen iOS-Geräten werden in Gerätenamen, die Apostrophzeichen enthalten, möglicherweise andere Zeichen verwendet, die wie ein Apostroph aussehen. Die Suche nach solchen Geräten ist also etwas kompliziert. Wenn keine korrekten Suchergebnisse angezeigt werden, stellen Sie sicher, dass die Suchzeichenfolge das passende Apostrophzeichen enthält.

## <a name="manage-an-intune-device"></a>Verwalten eines Intune-Geräts

Wenn Sie über Rechte zum Verwalten von Geräten in Intune verfügen, können Sie Geräte verwalten, bei denen **Microsoft Intune** für die Verwaltung mobiler Geräte aufgeführt ist. Ist das Gerät nicht bei Microsoft Intune registriert, ist die Option **Verwalten** nicht verfügbar.

## <a name="enable-or-disable-an-azure-ad-device"></a>Aktivieren oder Deaktivieren von Azure AD-Geräten

Es gibt zwei Möglichkeiten, Geräte zu aktivieren oder zu deaktivieren:

- Die Symbolleiste auf der Seite **Alle Geräte**, nachdem Sie ein oder mehrere Geräte ausgewählt haben
- Die Symbolleiste nach dem Drilldown für ein bestimmtes Gerät

> [!IMPORTANT]
> - Zum Aktivieren oder Deaktivieren eines Geräts müssen Sie ein globaler Administrator, ein Intune-Administrator oder ein Cloudgeräteadministrator in Azure AD sein. 
> - Durch das Deaktivieren eines Geräts wird die Authentifizierung über Azure AD verhindert. Dadurch kann es nicht mehr auf Ihre Azure AD-Ressourcen zugreifen, die durch den gerätebasierten bedingten Zugriff geschützt sind, und es kann keine Windows Hello for Business-Anmeldeinformationen mehr verwenden.
> - Durch das Deaktivieren eines Geräts werden sowohl das primäre Aktualisierungstoken (Primary Refresh Token, PRT) als auch etwaige Aktualisierungstoken (Refresh Tokens, RT) auf dem Gerät widerrufen.
> - Drucker können in Azure AD weder aktiviert noch deaktiviert werden.

## <a name="delete-an-azure-ad-device"></a>Löschen eines Azure AD-Geräts

Es gibt zwei Möglichkeiten, ein Gerät zu löschen:

- Die Symbolleiste auf der Seite **Alle Geräte**, nachdem Sie ein oder mehrere Geräte ausgewählt haben
- Die Symbolleiste nach dem Drilldown für ein bestimmtes Gerät

> [!IMPORTANT]
> - Zum Löschen eines Geräts müssen Sie ein Cloudgeräteadministrator, Intune-Administrator oder globaler Administrator in Azure AD sein.
> - Drucker und Windows Autopilot-Geräte können in Azure AD nicht gelöscht werden.
> - Das Löschen eines Geräts:
>    - verhindert, dass über das Gerät auf Ihre Azure AD-Ressourcen zugegriffen wird
>    - entfernt alle Details, die an das Gerät angefügt sind (z. B. BitLocker-Schlüssel bei Windows-Geräten)  
>    - ist eine nicht wiederherstellbare Aktivität wird nur empfohlen, wenn dies erforderlich ist

Wenn ein Gerät von einer anderen Verwaltungsautorität wie Microsoft Intune verwaltet wird, stellen Sie sicher, dass es zurückgesetzt wurde oder nicht mehr verwendet wird, bevor Sie es löschen. Informieren Sie sich über das [Verwalten veralteter Geräte](manage-stale-devices.md), bevor Sie ein Gerät löschen.

## <a name="view-or-copy-a-device-id"></a>Anzeigen oder Kopieren einer Geräte-ID

Mithilfe einer Geräte-ID können Sie Gerätedetails am Gerät überprüfen oder über PowerShell eine Problembehandlung vornehmen. Um auf die Kopieroption zuzugreifen, wählen Sie das Gerät aus.

![Screenshot: Geräte-ID und Schaltfläche „Kopieren“.](./media/device-management-azure-portal/35.png)
  
## <a name="view-or-copy-bitlocker-keys"></a>Anzeigen oder Kopieren von BitLocker-Schlüsseln

Sie können die BitLocker-Schlüssel anzeigen und kopieren, um Benutzern die Wiederherstellung verschlüsselter Laufwerke zu ermöglichen. Diese Schlüssel sind nur für Windows-Geräte verfügbar, die verschlüsselt sind und deren Schlüssel in Azure AD gespeichert sind. Sie finden diese Schlüssel beim Zugriff auf die Details eines Geräts, indem Sie **Wiederherstellungsschlüssel anzeigen** auswählen. Wenn Sie **Wiederherstellungsschlüssel anzeigen** auswählen, wird ein Überwachungsprotokoll generiert, das Sie in der Kategorie `KeyManagement` finden.

![Screenshot: Anzeigen der BitLocker-Schlüssel.](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Um die BitLocker-Schlüssel anzuzeigen oder zu kopieren, müssen Sie Besitzer des Geräts sein oder über eine der folgenden Rollen verfügen:

- Cloudgeräteadministrator
- Globaler Administrator
- Helpdeskadministrator
- Intune-Dienstadministrator
- Sicherheitsadministrator
- Sicherheitsleseberechtigter

## <a name="device-list-filtering-preview"></a>Filtern der Geräteliste (Vorschau)

Bisher konnten Sie die Geräteliste nur nach Aktivität und Aktivierungszustand filtern. In dieser Vorschau können Sie die Geräteliste nach den folgenden Geräteattributen filtern:

- Aktivierungszustand
- Konformitätszustand
- Jointyp (in Azure AD eingebunden, hybrid in Azure AD eingebunden, in Azure AD registriert)
- Aktivitätszeitstempel
- OS
- Gerätetyp (Drucker, sicherer virtueller Computer, freigegebenes Gerät, registriertes Gerät)

So aktivieren Sie die Filterfunktionen (Vorschau) in der Ansicht **Alle Geräte**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Geräte**.
1. Wählen Sie das Banner mit dem Text **Testen Sie die neuen Verbesserungen des Gerätefilters. Klicken Sie, um die Vorschau zu aktivieren** aus.

   ![Aktivieren der Filterfunktionen (Vorschau)](./media/device-management-azure-portal/device-filter-preview-enable.png)

Sie können jetzt der Ansicht **Alle Geräte** Filter hinzufügen.

## <a name="download-devices-preview"></a>Geräte herunterladen (Vorschau)

Cloudgeräteadministratoren, Intune-Administratoren und globale Administratoren können mithilfe der Option **Geräte herunterladen (Vorschau)** eine CSV-Datei exportieren, in der Geräte aufgelistet sind. Sie können Filter anwenden, um zu bestimmen, welche Geräte aufgelistet werden sollen. Wenn Sie keine Filter anwenden, werden alle Geräte aufgelistet. Abhängig von Ihrer Auswahl kann das Ausführen einer Exportaufgabe bis zu einer Stunde dauern.

Die exportierte Liste enthält die folgenden Geräteidentitätsattribute:

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Konfigurieren der Geräteeinstellungen

Wenn Sie Geräteidentitäten im Azure-Portal verwalten möchten, müssen diese in Azure AD [registriert oder eingebunden](overview.md) sein. Als Administrator können Sie den Prozess der Registrierung und Einbindung von Geräten steuern, indem Sie die Geräteeinstellungen konfigurieren.

Sie müssen einer der folgenden Rollen zugewiesen sein, um die Geräteeinstellungen im Azure-Portal anzeigen oder verwalten zu können:

- Globaler Administrator
- Cloudgeräteadministrator
- Globaler Leser
- Verzeichnis lesen

![Screenshot: Geräteeinstellungen in Verbindung mit Azure AD.](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Benutzer dürfen Geräte in Azure AD einbinden**: Diese Einstellung ermöglicht Ihnen die Auswahl der Benutzer, die ihre Geräte als in Azure AD eingebundene Geräte registrieren können. Die Standardeinstellung ist **Alle**.

   > [!NOTE]
   > Die Einstellung **Benutzer dürfen Geräte in Azure AD einbinden** gilt nur für die Azure AD-Einbindung unter Windows 10. Diese Einstellung gilt nicht für hybrid in Azure AD eingebundene Geräte, [in Azure AD eingebundene Azure-VMs](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) oder in Azure AD eingebundene Geräte, die den [Selbstbereitstellungsmodus von Windows Autopilot](/mem/autopilot/self-deploying) verwenden, da diese Methoden in einem Kontext ohne Benutzer funktionieren.

- **Weitere lokale Administratoren für in Azure AD eingebundene Geräte**: Mit dieser Einstellung können Sie die Benutzer auswählen, denen lokale Administratorrechte auf einem Gerät erteilt werden. Diese Benutzer werden der Rolle Geräteadministratoren in Azure AD hinzugefügt. Globale Administratoren in Azure AD und Gerätebesitzer erhalten standardmäßig lokale Administratorrechte. Diese Option ist eine Premium Edition-Funktion, die über Produkte wie Azure AD Premium oder Enterprise Mobility + Security zur Verfügung steht.
- **Benutzer dürfen ihre Geräte bei Azure AD registrieren**: Sie müssen diese Einstellung konfigurieren, um Benutzern die Registrierung von Windows 10- und persönlichen Geräten sowie iOS-, Android- und macOS-Geräten bei Azure AD zu erlauben. Bei Auswahl von **Keine** dürfen Geräte nicht bei Azure AD registriert werden. Für die Anmeldung bei Microsoft Intune oder die mobile Geräteverwaltung für Microsoft 365 ist eine Registrierung erforderlich. Wenn Sie einen dieser Dienste konfiguriert haben, wird **ALLE** ausgewählt, und die Option **KEINE** ist nicht verfügbar.
- **Zum Registrieren oder Einbinden von Geräten in Azure AD Multi-Factor Authentication anfordern**: Mit dieser Einstellung können Sie angeben, ob Benutzer einen zusätzlichen Authentifizierungsfaktor bereitstellen müssen, um ihre Geräte in Azure AD einbinden oder bei Azure AD registrieren zu können. Der Standardwert ist **No**. Es wird empfohlen, die mehrstufige Authentifizierung anzufordern, wenn ein Gerät registriert oder eingebunden wird. Bevor Sie die mehrstufige Authentifizierung für diesen Dienst aktivieren, müssen Sie sicherstellen, dass diese auch für die Benutzer konfiguriert ist, die ihre Geräte registrieren. Weitere Informationen zu den verschiedenen Azure AD Multi-Factor Authentication-Diensten finden Sie unter [Erste Schritte mit Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

   > [!NOTE]
   > Die Einstellung **Zum Registrieren oder Einbinden von Geräten in Azure AD Multi-Factor Authentication anfordern** gilt für Geräte, die entweder in Azure AD eingebunden (mit einigen Ausnahmen) oder bei Azure AD registriert sind. Diese Einstellung gilt nicht für hybrid in Azure AD eingebundene Geräte, [in Azure AD eingebundene Azure-VMs](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) oder in Azure AD eingebundene Geräte, die den [Selbstbereitstellungsmodus von Windows Autopilot](/mem/autopilot/self-deploying) verwenden.

   > [!IMPORTANT]
   > - Wir empfehlen, beim bedingten Zugriff die Benutzeraktion [Geräte registrieren oder einbinden](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) zu verwenden, um beim Einbinden oder Registrieren eines Geräts die mehrstufige Authentifizierung zu erzwingen. 
   > - Sie müssen diese Einstellung auf **Nein** festlegen, wenn Sie zum Anfordern einer mehrstufigen Authentifizierung die Richtlinie für bedingten Zugriff nutzen. 

- **Maximale Anzahl von Geräten**: Mit dieser Einstellung können Sie die maximale Anzahl von in Azure AD eingebundenen oder bei Azure AD registrierten Geräten festlegen, die ein Benutzer in Azure AD verwenden kann. Wenn ein Benutzer dieses Limit erreicht hat, kann er erst dann weitere Geräte hinzufügen, wenn eines oder mehrere der vorhandenen Geräte entfernt wurden. Der Standardwert lautet **50**. Sie können den Wert auf bis zu 100 erhöhen. Wenn Sie einen Wert über 100 eingeben, wird er von Azure AD auf 100 festgelegt. Sie können auch **Unbegrenzt** verwenden, um außer den vorhandenen Kontingentlimits keine Beschränkung zu erzwingen.

   > [!NOTE]
   > Die Einstellung **Maximale Anzahl von Geräten** gilt für Geräte, die entweder in Azure AD eingebunden oder bei Azure AD registriert sind. Diese Einstellung gilt nicht für eingebundene Azure AD-Hybridgeräte.

- **Enterprise State Roaming:** Informationen zu dieser Einstellung finden Sie im [Übersichtsartikel](enterprise-state-roaming-overview.md).

## <a name="audit-logs"></a>Überwachungsprotokolle

Geräteaktivitäten werden in den Aktivitätsprotokollen angezeigt. Diese Protokolle enthalten vom Geräteregistrierungsdienst und von Benutzern ausgelöste Aktivitäten:

- Erstellen von Geräten und Hinzufügen von Besitzern/Benutzern auf dem Gerät
- Änderungen der Geräteeinstellungen
- Gerätevorgänge wie beispielsweise das Löschen oder Aktualisieren eines Geräts

Ihr Einstiegspunkt für Überwachungsdaten ist die Option **Überwachungsprotokolle** im Abschnitt **Aktivität** auf der Seite **Geräte**.

Das Überwachungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Datum und Uhrzeit des Auftretens
- Ziele
- Initiator/Akteur einer Aktivität
- Die Aktivität.

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Screenshot: Tabelle im Abschnitt „Aktivität“ auf der Seite „Geräte“. Die Tabelle enthält Datum, Ziel, Akteur und Aktivität für vier Überwachungsprotokolle." border="false":::

Die Listenansicht kann durch Auswahl von **Spalten** in der Symbolleiste angepasst werden:

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Screenshot: Symbolleiste auf der Seite „Geräte“." border="false":::

Um die Berichtsdaten auf eine Ebene zu reduzieren, die für Sie funktioniert, können Sie sie mithilfe der folgenden Felder filtern:

- **Kategorie**
- **Aktivitätsressourcentyp**
- **Aktivität**
- **Datumsbereich**
- **Target**
- **Initiiert von (Akteur)**

Sie können auch nach bestimmten Einträgen suchen.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Screenshot: Steuerelemente zum Filtern der Überwachungsdaten." border="false":::

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von veralteten Geräten in Azure AD](manage-stale-devices.md)
- [Problembehandlung beim Gerätestatus „Ausstehend“](/troubleshoot/azure/active-directory/pending-devices)
