---
title: Verwalten von Geräten in Azure AD über das Azure-Portal
description: Hier erfahren Sie, wie Sie Geräte über das Azure-Portal verwalten.
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
ms.openlocfilehash: ecfbb1ad70fba1444d97d70abd0a5052a91fce15
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040308"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Verwalten der Geräteidentität mithilfe des Azure-Portals

Azure AD bietet Ihnen eine Zentrale zum Verwalten von Geräteidentitäten und zum Überwachen zugehöriger Ereignisinformationen.

[![Geräteübersicht im Azure-Portal](./media/device-management-azure-portal/devices-azure-portal.png)](./media/device-management-azure-portal/devices-azure-portal.png#lightbox)

Sie können mit folgenden Schritten auf die Geräteübersicht zugreifen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Geräte**.

In der Geräteübersicht finden Sie die Gesamtzahl aller Geräte, veraltete Geräte, nicht konforme Geräte und nicht verwaltete Geräte. Sie können auch schnell auf Links zu Intune, Funktionen für bedingten Zugriff, BitLocker-Schlüsseln und grundlegenden Überwachungsfunktionen zugreifen. 

Die Geräteanzahl auf der Übersichtsseite wird nicht in Echtzeit aktualisiert. Änderungen werden alle paar Stunden widergespiegelt.

Von dort aus können Sie zu **Alle Geräte** wechseln, um folgende Aktivitäten auszuführen:

- Identifizieren von Geräten, einschließlich der folgenden:
   - Geräte, die in Azure AD eingebunden oder registriert sind
   - Geräte, die mithilfe von [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) bereitgestellt wurden
   - Drucker mit [Universal Print](/universal-print/fundamentals/universal-print-getting-started)
- Führen Sie Aufgaben zur Verwaltung von Geräteidentitäten aus, z. B. Aktivieren, Deaktivieren, Löschen oder Verwalten.
   - [Drucker](/universal-print/fundamentals/) und [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)-Geräte verfügen in Azure AD über eingeschränkte Verwaltungsoptionen. Sie müssen über die entsprechenden Verwaltungsoberflächen verwaltet werden.
- Konfigurieren Sie Ihre Geräteidentitätseinstellungen.
- Aktivieren oder deaktivieren Sie Enterprise State Roaming.
- Überprüfen gerätebezogener Überwachungsprotokolle
- Geräte herunterladen (Vorschau)

[![Ansicht „Alle Geräte“ im Azure-Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

> [!TIP]
> - In Hybrid-Azure AD eingebundene Windows 10-Geräte haben keinen Besitzer. Wenn Sie ein Gerät nach Besitzer suchen und es nicht finden, suchen Sie nach der Geräte-ID.
>
> - Wenn in der Spalte **Registriert** ein Gerät mit dem Zusatz „In Azure AD Hybrid eingebunden“ und dem Zustand „Ausstehend“ angezeigt wird, deutet das darauf hin, dass das Gerät von Azure AD Connect synchronisiert wurde und auf die vollständige Registrierung vom Client wartet. Weitere Informationen finden Sie unter [Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](hybrid-azuread-join-plan.md). Weitere Informationen finden Sie im Artikel mit [häufig gestellten Fragen zu Geräten](faq.yml).
>
> - Für einige iOS-Geräte werden in Gerätenamen, die Apostrophe enthalten, möglicherweise andere Zeichen verwendet, die wie Apostrophe aussehen. Die Suche nach solchen Geräte ist daher ein wenig kompliziert. Wenn Ihnen Suchergebnisse nicht ordnungsgemäß angezeigt werden, stellen Sie sicher, dass die Suchzeichenfolge ein Zeichen enthält, das einem Apostroph entspricht.

### <a name="manage-an-intune-device"></a>Verwalten eines Intune-Geräts

Wenn Sie über Rechte zum Verwalten von Geräten in Intune verfügen, können Sie Geräte verwalten, bei denen die „Verwaltung mobiler“ Geräte als **Microsoft Intune** gekennzeichnet ist. Wenn das Gerät nicht bei Microsoft Intune registriert ist, wird die Option „Verwalten“ abgeblendet angezeigt.

### <a name="enable-or-disable-an-azure-ad-device"></a>Aktivieren oder Deaktivieren von Azure AD-Geräten

Sie haben zwei Optionen, um Geräte zu aktivieren bzw. zu deaktivieren:

- Die Symbolleiste auf der Seite **Alle Geräte**, nachdem Sie ein oder mehrere Geräte ausgewählt haben
- Die Symbolleiste nach dem Drilldown auf ein bestimmtes Gerät

> [!IMPORTANT]
> - Zum Aktivieren oder Deaktivieren eines Geräts müssen Sie ein globaler Administrator, ein Intune-Administrator oder ein Cloudgeräteadministrator in Azure AD sein. 
> - Durch die Deaktivierung eines Geräts wird seine erfolgreiche Authentifizierung mit Azure AD verhindert. Dadurch wird wiederum verhindert, dass das Gerät auf Ihre Azure AD-Ressourcen zugreift, die durch gerätebasierten bedingten Zugriff oder mithilfe Ihrer Anmeldeinformationen für Windows Hello for Business geschützt werden.
> - Durch Deaktivieren eines Geräts werden sowohl das primäre Aktualisierungstoken (Primary Refresh Token, PRT) als auch etwaige Aktualisierungstoken (Refresh Tokens, RT) auf dem Gerät widerrufen.
> - Drucker können nicht in Azure AD aktiviert oder deaktiviert werden.

### <a name="delete-an-azure-ad-device"></a>Löschen eines Azure AD-Geräts

Sie haben zwei Optionen, um ein Gerät zu löschen:

- Die Symbolleiste auf der Seite **Alle Geräte**, nachdem Sie ein oder mehrere Geräte ausgewählt haben
- Die Symbolleiste nach dem Drilldown auf ein bestimmtes Gerät

> [!IMPORTANT]
> - Zum Löschen eines Geräts muss Ihnen in Azure AD eine der Rollen „Cloudgeräteadministrator“, „Intune-Administrator“ oder „Globaler Administrator“ zugewiesen sein.
> - Drucker und Windows Autopilot-Geräte können in Azure AD nicht gelöscht werden.
> - Das Löschen eines Geräts:
>    - Verhindert, dass über das Gerät auf Ihre Azure AD-Ressourcen zugegriffen wird
>    - Entfernt alle Details, die an das Gerät angefügt wurden, z.B. BitLocker-Schlüssel für Windows-Geräte  
>    - Stellt eine Aktivität dar, die nicht rückgängig gemacht werden kann, und wird nur empfohlen, wenn das Löschen notwendig ist

Wenn ein Gerät von einer anderen Verwaltungsstelle wie z. B. Microsoft Intune verwaltet wird, stellen Sie sicher, dass das Gerät zurückgesetzt oder ausgemustert wurde, bevor Sie es löschen. Informieren Sie sich über das [Verwalten veralteter Geräte](manage-stale-devices.md), bevor Sie Geräte löschen.

### <a name="view-or-copy-device-id"></a>Anzeigen oder Kopieren einer Geräte-ID

Sie können während der Problembehandlung eine Geräte-ID oder PowerShell verwenden, um die ID-Details eines Geräts zu überprüfen. Um auf die Kopieroption zuzugreifen, wählen Sie das Gerät aus.

![Anzeigen der Geräte-ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Anzeigen oder Kopieren von BitLocker-Schlüsseln

Sie können die BitLocker-Schlüssel anzeigen und kopieren, um Benutzern die Wiederherstellung verschlüsselter Laufwerke zu ermöglichen. Diese Schlüssel sind nur für Windows-Geräte verfügbar, die verschlüsselt sind und deren Schlüssel in Azure AD gespeichert sind. Sie finden diese Schlüssel beim Zugriff auf die Details eines Geräts, indem Sie **Wiederherstellungsschlüssel anzeigen** auswählen. Wenn Sie **Wiederherstellungsschlüssel anzeigen** auswählen, wird ein Überwachungsprotokoll generiert, das Sie in der Kategorie `KeyManagement` finden.

![BitLocker-Schlüssel anzeigen](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Um die BitLocker-Schlüssel anzuzeigen oder zu kopieren, müssen Sie Besitzer des Geräts sein oder eine der folgenden Rollen innehaben:

- Cloudgeräteadministrator
- Globaler Administrator
- Helpdeskadministrator
- Intune-Dienstadministrator
- Sicherheitsadministrator
- Sicherheitsleseberechtigter

### <a name="device-list-filtering-preview"></a>Filtern der Geräteliste (Vorschau)

Bisher konnten Sie die Geräteliste nur nach Aktivität und Aktivierungszustand filtern. In dieser Vorschauversion können Sie nun die Geräteliste nach den folgenden Attributen eines Geräts filtern:

- Aktivierungszustand
- Konformitätszustand
- Jointyp (in Azure AD eingebunden, hybrid in Azure AD eingebunden, in Azure AD registriert)
- Aktivitätszeitstempel
- OS
- Gerätetyp (Drucker, sichere virtuelle Computer, freigegebene Geräte, registrierte Geräte)

So aktivieren Sie die Filterfunktionen (Vorschau) in der Ansicht **Alle Geräte**

![Aktivieren der Filterfunktionen (Vorschau)](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Geräte**.
1. Wählen Sie das Banner aus, das besagt: **Testen Sie die Verbesserungen des neuen Gerätefilters. Klicken Sie hier, um die Vorschau zu aktivieren.**

Die Option zum **Hinzufügen von Filtern** ist jetzt für Ihre Ansicht **Alle Geräte** verfügbar.

### <a name="download-devices-preview"></a>Geräte herunterladen (Vorschau)

Cloud-Geräte-Administratoren, InTune-Administratoren und globale Administratoren können die Option **Geräte herunterladen (Vorschauversion)** verwenden, um eine CSV-Datei mit Geräten auf der Grundlage angewendeter Filter zu exportieren. Wenn die Liste nicht gefiltert ist, werden alle Geräte exportiert. Eine Exportaufgabe kann je nach ausgewählten Optionen bis zu eine Stunde dauern.

Die exportierte Liste enthält die folgenden Geräteidentitäts-Attribute:

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Konfigurieren der Geräteeinstellungen

Damit Geräteidentitäten im Azure AD-Portal verwaltet werden können, müssen diese in Azure AD [registriert oder eingebunden](overview.md) werden. Als Administrator können Sie den Prozess der Registrierung und Einbindung von Geräten steuern, indem Sie die Geräteeinstellungen konfigurieren.

Sie müssen einer der folgenden Rollen zugewiesen sein, um die Geräteeinstellungen im Azure-Portal anzeigen oder verwalten zu können:

- Globaler Administrator
- Cloudgeräteadministrator
- Globaler Leser
- Verzeichnisleseberechtigter

![Geräteeinstellungen in Verbindung mit Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Benutzer dürfen Geräte in Azure AD einbinden**: Diese Einstellung ermöglicht Ihnen die Auswahl der Benutzer, die ihre Geräte als in Azure AD eingebundene Geräte registrieren können. Die Standardeinstellung ist **Alle**.

> [!NOTE]
> **Benutzer dürfen Geräte in Azure AD einbinden**: Diese Einstellung gilt nur für die Azure AD-Einbindung unter Windows 10. Diese Einstellung gilt nicht für hybrid in Azure AD eingebundene Geräte, [in Azure AD eingebundene Azure-VMs](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) und in Azure AD eingebundene Geräte, die den [Selbstbereitstellungsmodus von Windows Autopilot](/mem/autopilot/self-deploying) verwenden, da diese Methoden in einem Kontext ohne Benutzer funktionieren.

- **Weitere lokale Administratoren für in Azure AD eingebundene Geräte**: Sie können die Benutzer auswählen, denen lokale Administratorrechte auf einem Gerät erteilt werden. Diese Benutzer werden der Rolle *Geräteadministratoren* in Azure AD hinzugefügt. Globale Administratoren in Azure AD und Gerätebesitzer erhalten standardmäßig lokale Administratorrechte. Diese Option ist eine Premium Edition-Funktion, die über Produkte wie Azure AD Premium oder die Enterprise Mobility Suite (EMS) zur Verfügung steht.
- **Benutzer dürfen ihre Geräte für Azure AD registrieren**: Sie müssen diese Einstellung konfigurieren, um die Registrierung von Windows 10-, persönlichen, iOS-, Android- und macOS-Geräten in Azure AD zuzulassen. Bei Auswahl von **Keine** dürfen Geräte nicht bei Azure AD registriert werden. Für die Registrierung bei Microsoft Intune oder der mobilen Geräteverwaltung (MDM) für Microsoft 365 ist eine Registrierung erforderlich. Wenn Sie einen dieser Dienste konfiguriert haben, wird **ALLE** ausgewählt, und die Option **KEINE** ist nicht verfügbar.
- **Zum Registrieren oder Einbinden von Geräten in Azure AD Multi-Factor Authentication anfordern**: Sie können auswählen, ob Benutzer einen zusätzlichen Authentifizierungsfaktor bereitstellen müssen, um ihr Gerät in Azure AD einbinden oder registrieren zu können. Der Standardwert ist **No**. Es wird empfohlen, beim Registrieren oder Einbinden eines Geräts die mehrstufige Authentifizierung zu verwenden. Bevor Sie die mehrstufige Authentifizierung für diesen Dienst aktivieren, müssen Sie sicherstellen, dass sie für die Benutzer konfiguriert ist, die ihre Geräte registrieren. Weitere Informationen zu den verschiedenen Azure AD Multi-Factor Authentication-Diensten finden Sie unter [Erste Schritte mit Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> Die Einstellung **Zum Registrieren oder Einbinden von Geräten in Azure AD Multi-Factor Authentication anfordern** gilt für Geräte, die entweder in Azure AD eingebunden (mit einigen Ausnahmen) oder bei Azure AD registriert sind. Diese Einstellung gilt nicht für hybrid in Azure AD eingebundene Geräte, [in Azure AD eingebundene Azure-VMs](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) und in Azure AD eingebundene Geräte, die den [Selbstbereitstellungsmodus von Windows Autopilot](/mem/autopilot/self-deploying) verwenden.

> [!IMPORTANT]
> - Wir empfehlen in „Bedingter Zugriff“ die [Benutzeraktion „Geräte registrieren oder einbinden“](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) zum Erzwingen der mehrstufigen Authentifizierung beim Einbinden oder Registrieren eines Geräts. 
> - Sie müssen diese Einstellung auf **Nein** festlegen, wenn Sie zum Anfordern einer mehrstufigen Authentifizierung die Richtlinie für bedingten Zugriff nutzen. 

- **Maximale Anzahl von Geräten pro Benutzer**: Mit dieser Einstellung können Sie die maximale Anzahl von in Azure AD eingebundenen oder bei Azure AD registrierten Geräten festlegen, die ein Benutzer in Azure AD verwenden kann. Wenn ein Benutzer dieses Kontingent erreicht, kann er erst dann weitere Geräte hinzufügen, wenn eines oder mehrere der vorhandenen Geräte entfernt wurden. Der Standardwert lautet **50**. Sie können den Wert auf bis zu 100 erhöhen. Wenn Sie einen Wert über 100 eingeben, wird die Azure AD auf 100 festgelegt. Sie können auch einen unbegrenzten Wert verwenden, um außer den vorhandenen Kontingentlimits keine Beschränkung zu erzwingen.

> [!NOTE]
> Die Einstellung **Maximale Anzahl von Geräten pro Benutzer** gilt für Geräte, die entweder in Azure AD eingebunden oder bei Azure AD registriert sind. Diese Einstellung gilt nicht für in Azure AD Hybrid eingebundene Geräte.

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Überwachungsprotokolle

Geräteaktivitäten sind über die Aktivitätsprotokolle verfügbar. Diese Protokolle enthalten vom Geräteregistrierungsdienst und von Benutzern ausgelöste Aktivitäten:

- Erstellen von Geräten und Hinzufügen von Besitzern/Benutzern auf dem Gerät
- Änderungen der Geräteeinstellungen
- Gerätevorgänge wie beispielsweise das Löschen oder Aktualisieren eines Geräts

Ihr Einstiegspunkt für die Überwachungsdaten ist die Option **Überwachungsprotokolle** im Abschnitt **Aktivität** der Seite **Geräte**.

Das Überwachungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Datum und Uhrzeit des Auftretens
- Ziele
- Initiator/Akteur einer Aktivität (Wer)
- Aktivität (Was)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Screenshot: Tabelle im Abschnitt „Aktivität“ der Seite „Geräte“. Die Tabelle enthält Datum, Ziel, Akteur und Aktivität für vier Überwachungsprotokolle." border="false":::

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Screenshot: Symbolleiste auf der Seite „Geräte“. Das Element „Spalten“ ist hervorgehoben." border="false":::

Sie können die Überwachungsdaten mit den folgenden Feldern filtern, um die gemeldeten Daten gemäß Ihren Bedürfnissen einzugrenzen:

- Category
- Aktivitätsressourcentyp
- Aktivität
- Datumsbereich
- Ziel
- Initiiert von (Akteur)

Sie können auch nach bestimmten Einträgen suchen.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Screenshot: Filtersteuerelemente für Überwachungsdaten mit Feldern für Kategorie, Aktivitätsressourcentyp, Aktivität, Datumsbereich, Ziel und Akteur sowie einem Suchfeld" border="false":::

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von veralteten Geräten in Azure AD](manage-stale-devices.md)
- [Problembehandlung beim Gerätestatus „Ausstehend“](/troubleshoot/azure/active-directory/pending-devices)
