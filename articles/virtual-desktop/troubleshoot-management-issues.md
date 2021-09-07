---
title: 'Probleme bei der Azure Virtual Desktop-Verwaltung: Azure'
description: Hier werden allgemeine Verwaltungsprobleme in Azure Virtual Desktop und ihre Lösung erläutert.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 529cf643cb68bc806aef991142e68f42fccc0d42
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112420694"
---
# <a name="management-issues"></a>Verwaltungsprobleme

In diesem Artikel werden allgemeine Verwaltungsfehler und Vorschläge zur Lösung dieser Fehler beschrieben.

## <a name="common-management-errors"></a>Allgemeine Verwaltungsfehler

Die folgende Tabelle enthält Fehlermeldungen, die aufgrund von verwaltungsbezogenen Problemen angezeigt werden, sowie Vorschläge für deren Behebung.

|Fehlermeldung|Vorgeschlagene Lösung|
|---|---|
|Fehler beim Erstellen des Registrierungsschlüssels |Es konnte kein Registrierungstoken erstellt werden. Versuchen Sie, es mit einer kürzeren Ablaufzeit (zwischen 1 Stunde und 1 Monat) erneut zu erstellen. |
|Fehler beim Löschen des Registrierungsschlüssels|Das Registrierungstoken konnte nicht gelöscht werden. Versuchen Sie, es erneut zu löschen. Wenn dies immer noch nicht funktioniert, überprüfen Sie mithilfe von PowerShell, ob das Token immer noch vorhanden ist. Wenn dies der Fall ist, löschen Sie es mit PowerShell.|
|Fehler beim Ändern des Ausgleichsmodus für den Sitzungshost |Der Ausgleichsmodus für die VM konnte nicht geändert werden. Überprüfen Sie den VM-Status. Wenn der virtuelle Computer nicht verfügbar ist, können Sie den Ausgleichsmodus nicht ändern.|
|Fehler beim Trennen von Benutzersitzungen |Die Verbindung des Benutzers mit der VM konnte nicht getrennt werden. Überprüfen Sie den VM-Status. Wenn der virtuelle Computer nicht verfügbar ist, können Sie die Benutzersitzung nicht trennen. Wenn die VM verfügbar ist, überprüfen Sie den Status der Benutzersitzung, um festzustellen, ob sie getrennt ist. |
|Fehler beim Abmelden aller Benutzer im Sitzungshost |Benutzer konnten nicht von der VM abgemeldet werden. Überprüfen Sie den VM-Status. Wenn sie nicht verfügbar ist, können Benutzer nicht abgemeldet werden. Prüfen Sie den Status der Benutzersitzung, um festzustellen, ob die Benutzer bereits abgemeldet sind. Sie können die Abmeldung mit PowerShell erzwingen. |
|Fehler beim Aufheben der Zuweisung des Benutzers zur Anwendungsgruppe|Die Veröffentlichung einer App-Gruppe für einen Benutzer konnte nicht aufgehoben werden. Überprüfen Sie, ob der Benutzer in Azure AD verfügbar ist. Überprüfen Sie, ob der Benutzer einer Benutzergruppe angehört, in der die App-Gruppe veröffentlicht wurde. |
|Fehler beim Abrufen der verfügbaren Standorte |Überprüfen Sie den Standort der VM, der im Assistenten zum Erstellen von Hostpools verwendet wurde. Wenn das Image an diesem Standort nicht verfügbar ist, fügen Sie dort ein Image hinzu oder wählen einen anderen VM-Standort aus. |

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Error: App-Gruppen können keine Benutzerzuweisungen hinzugefügt werden

Nach dem Zuweisen eines Benutzers zu einer App-Gruppe zeigt das Azure-Portal die Warnung „Sitzungsende“ oder „Probleme bei der Authentifizierung – Erweiterung Microsoft_Azure_WVD“ an. Die Zuweisungsseite wird nicht geladen, und danach werden die Seiten im gesamten Azure-Portal nicht mehr geladen (z. B. Azure Monitor, Log Analytics, Service Health).

Dieser Fehler tritt in der Regel auf, weil ein Problem mit der Richtlinie für bedingten Zugriff vorliegt. Das Azure-Portal versucht, ein Token für Microsoft Graph abzurufen, das von SharePoint Online abhängig ist. Der Kunde besitzt eine Richtlinie für bedingten Zugriff namens „Microsoft Office 365 Data Storage Terms of Use“ (Nutzungsbedingungen für den Microsoft Office 365-Datenspeicher), die voraussetzt, dass der Benutzer die Nutzungsbedingungen akzeptiert, um auf den Datenspeicher zugreifen zu können. Er ist jedoch noch nicht angemeldet, sodass das Azure-Portal das Token nicht abrufen kann.

Zur Behebung des Problems muss sich der Administrator vor der Anmeldung beim Azure-Portal zuerst bei SharePoint anmelden und die Nutzungsbedingungen akzeptieren. Danach sollte dieser sich wie gewohnt beim Azure-Portal anmelden können.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu allgemeinen Fehlerszenarien, die von der Diagnosefunktion identifiziert werden können, finden Sie unter [Identifizieren und Diagnostizieren von Problemen](diagnostics-role-service.md#common-error-scenarios).
