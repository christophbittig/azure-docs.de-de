---
title: Aktivieren von Enterprise State Roaming in Azure Active Directory
description: Enthält häufig gestellte Fragen zu Enterprise State Roaming-Einstellungen auf Windows-Geräten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: na
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: f960e737f4623e90761dce17b6ad5b13ee47ee88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624594"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Aktivieren von Enterprise State Roaming in Azure Active Directory

Enterprise State Roaming ist für jedes Unternehmen mit einer Azure AD Premium- oder Enterprise Mobility + Security-Lizenz (EMS) verfügbar. Weitere Informationen zum Erhalt eines Azure AD-Abonnements finden Sie auf der [Azure AD-Produktseite](https://azure.microsoft.com/services/active-directory) .

Wenn Sie Enterprise State Roaming aktivieren, wird Ihrer Organisation über Azure Information Protection automatisch eine kostenlose Lizenz für den Schutz per Azure Rights Management mit begrenzter Nutzung zugeteilt. Dieses kostenlose Abonnement ist auf das Verschlüsseln und Entschlüsseln von Enterpriseeinstellungen und durch Enterprise State Roaming synchronisierte Anwendungsdaten beschränkt. Sie benötigen [ein kostenpflichtiges Abonnement](https://azure.microsoft.com/services/information-protection/), um den gesamten Funktionsumfang des Azure Rights Management-Diensts nutzen zu können.

> [!NOTE]
> Dieser Artikel bezieht sich auf den älteren HTML-basierten Microsoft Edge-Browser, der im Juli 2015 mit Windows 10 veröffentlicht wurde. Der Artikel gilt nicht für den neuen Chromium-basierten Microsoft Edge-Browser, der am 15. Januar 2020 veröffentlicht wurde. Weitere Informationen zum Synchronisierungsverhalten des neuen Microsoft Edge finden Sie im Artikel [Microsoft Edge-Synchronisierung](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>So aktivieren Sie Enterprise State Roaming

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com/) an.
1. Wählen Sie **Azure Active Directory** > **Geräte** > **Enterprise State Roaming**.
1. Wählen Sie **Benutzer können Einstellungen und App-Daten geräteübergreifend synchronisieren**. Weitere Informationen finden Sie unter [Verwalten von Geräten im Azure-Portal – Vorschau](./device-management-azure-portal.md).
  
   ![Bild der Geräteeinstellung „Benutzer können Einstellungen und App-Daten geräteübergreifend synchronisieren“](./media/enterprise-state-roaming-enable/device-settings.png)
  
Damit ein Windows 10-Gerät den Enterprise State Roaming-Dienst nutzen kann, muss das Gerät mit einer Azure AD-Identität authentifiziert werden. Für Geräte, die Azure AD beigetreten sind, ist die primäre Anmeldeidentität des Benutzers die Azure AD-Identität. Es ist also keine zusätzliche Konfiguration erforderlich. Für Geräte, die eine lokale Active Directory-Instanz nutzen, muss der IT-Administrator [in Azure Active Directory eingebundene Hybridgeräte konfigurieren](./hybrid-azuread-join-plan.md). 

## <a name="data-storage"></a>Datenspeicher

Enterprise State Roaming-Daten werden in [Azure-Regionen](https://azure.microsoft.com/regions/) gehostet, die am besten zu dem Wert für Land/Region passen, der in der Azure Active Directory-Instanz festgelegt ist. Enterprise State Roaming-Daten werden basierend auf drei großen geografischen Regionen partitioniert: Nordamerika, EMEA und APAC. Enterprise State Roaming-Daten für Mandanten befinden sich lokal in der geografischen Region und werden nicht über Regionen hinweg repliziert.  Beispiel:

| Wert für Land/Region | Daten gehostet in |
| -------------------- | ------------------------ |
| EMEA-Länder/-Regionen, z.B. Frankreich oder Sambia | Eine oder mehrere Azure-Regionen in Europa |
| Ein Land / eine Region aus Nordamerika wie USA oder Kanada | Eine oder mehrere Azure-Regionen in den USA |
| APAC-Länder/-Regionen, z.B. Australien oder Neuseeland | Eine oder mehrere Azure-Regionen in Asien |
| Regionen „Südamerika“ und „Antarktis“ | Eine oder mehrere Azure-Regionen in den USA |

Der Wert für Land/Region wird im Rahmen der Azure AD-Verzeichniserstellung festgelegt und kann später nicht mehr geändert werden. Wenn Sie ausführliche Informationen zum Speicherort Ihrer Daten benötigen, können Sie beim [Azure-Support](https://azure.microsoft.com/support/options/) ein Ticket erstellen.

## <a name="view-per-user-device-sync-status"></a>Anzeigen des Gerätesynchronisierungsstatus pro Benutzer

Führen Sie diese Schritte aus, um einen Bericht zum Gerätesynchronisierungsstatus pro Benutzer anzuzeigen.

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com/) an.
1. Wählen Sie **Azure Active Directory** > **Benutzer** > **Alle Benutzer**.
1. Wählen Sie den Benutzer und dann **Geräte** aus.
1. Wählen Sie unter **Anzeigen** die Option **Einstellungen für Gerätesynchronisierung und App-Daten**, um den Synchronisierungsstatus anzuzeigen.
  
   ![Bild der Gerätesynchronisierungsdaten-Einstellung](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Wenn für diese Benutzer Geräte synchronisiert werden, werden die Geräte hier angezeigt.
  
   ![Bild der Spaltendarstellung von Gerätesynchronisierungsdaten](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Beibehaltung von Daten

Daten, die mithilfe von Enterprise State Roaming mit der Microsoft Cloud synchronisiert werden, werden beibehalten, bis sie manuell gelöscht oder als veraltet deklariert werden. 

### <a name="explicit-deletion"></a>Explizite Löschung

Bei expliziter Löschung löscht ein Azure-Administrator einen Benutzer oder ein Verzeichnis oder fordert auf andere Weise explizit die Löschung dieser Daten an.

* **Löschung eines Benutzers**: Wenn ein Benutzer in Azure AD gelöscht wird, werden die Roamingdaten des Benutzerkontos nach 90 bis 180 Tagen gelöscht. 
* **Löschung eines Verzeichnisses:** Das Löschen eines gesamten Verzeichnisses in Azure AD ist ein unmittelbarer Vorgang. Alle diesem Verzeichnis zugeordneten Einstellungsdaten werden nach 90 bis 180 Tagen gelöscht. 
* **Löschung auf Anforderung:** Wenn der Azure AD-Administrator die Einstellungen oder Daten eines bestimmten Benutzers manuell löschen möchte, kann er beim [Azure-Support](https://azure.microsoft.com/support/)ein Ticket erstellen. 

### <a name="stale-data-deletion"></a>Löschung veralteter Daten

Daten, auf die ein Jahr lang nicht zugegriffen wurde („Aufbewahrungsdauer“), werden als veraltet eingestuft und ggf. aus der Microsoft Cloud gelöscht. Die Aufbewahrungsdauer kann sich ändern, wird jedoch nicht weniger als 90 Tage betragen. Bei den veralteten Daten kann es sich um eine bestimmte Gruppe von Windows-/Anwendungseinstellungen oder um alle Einstellungen eines Benutzers handeln. Beispiel:

* Wenn keine Geräte auf eine bestimmte Sammlung mit Einstellungen zugreifen (z.B. wird eine Anwendung vom Gerät entfernt, oder eine Einstellungsgruppe wie „Design“ wird für alle Geräte eines Benutzers deaktiviert), gilt diese Sammlung nach Ablauf der Aufbewahrungsdauer als veraltet und kann gelöscht werden. 
* Falls ein Benutzer die Einstellungssynchronisierung auf allen Geräten deaktiviert hat, wird nicht auf die Einstellungsdaten zugegriffen. Alle Einstellungsdaten für diesen Benutzer werden als veraltet eingestuft und können nach dem Aufbewahrungszeitraum gelöscht werden. 
* Wenn der Azure AD-Verzeichnisadministrator Enterprise State Roaming für das gesamte Verzeichnis deaktiviert hat, wird die Synchronisierung der Einstellungen für alle Benutzer im jeweiligen Verzeichnis beendet. Alle Einstellungsdaten aller Benutzer gelten dann als veraltet und können nach Ablauf der Aufbewahrungsdauer gelöscht werden. 

### <a name="deleted-data-recovery"></a>Wiederherstellen von gelöschten Daten

Die Richtlinie für die Datenaufbewahrung ist nicht konfigurierbar. Sobald die Daten dauerhaft gelöscht wurden, sind sie nicht wiederherstellbar. Allerdings werden die Einstellungsdaten nur aus der Microsoft Cloud gelöscht, nicht vom Endbenutzergerät. Falls mit einem Gerät zu einem späteren Zeitpunkt wieder eine Verbindung mit dem Enterprise State Roaming-Dienst hergestellt wird, werden die Einstellungen wieder synchronisiert und in der Microsoft Cloud gespeichert.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [Roaming von Einstellungen und Daten: Häufig gestellte Fragen](enterprise-state-roaming-faqs.yml)
* [Gruppenrichtlinien- und MDM-Einstellungen für Einstellungssynchronisierung](enterprise-state-roaming-group-policy-settings.md)
* [Windows 10-Roamingeinstellungen – Referenz](enterprise-state-roaming-windows-settings-reference.md)
* [Problembehandlung](enterprise-state-roaming-troubleshooting.md)
