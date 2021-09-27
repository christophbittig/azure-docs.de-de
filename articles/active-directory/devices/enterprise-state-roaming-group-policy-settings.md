---
title: Gruppenrichtlinien- und MDM-Einstellungen für ESR – Azure Active Directory
description: Verwaltungseinstellungen für Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed9ad565a09c95d566876710dea94a7e6a1a7983
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619835"
---
# <a name="group-policy-and-mdm-settings"></a>Gruppenrichtlinien- und MDM-Einstellungen

Verwenden Sie diese Gruppenrichtlinien- und MDM-Einstellungen (Mobile Device Management, Verwaltung mobiler Geräte) nur für unternehmenseigene Geräte, da diese Richtlinien auf das gesamte Gerät eines Benutzers angewendet werden. Das Anwenden einer MDM-Richtlinie zum Deaktivieren der Einstellungssynchronisierung für ein persönliches, benutzereigenes Gerät hat eine negative Auswirkung auf die Nutzung dieses Geräts. Außerdem werden weitere Benutzerkonten auf dem Gerät von der Richtlinie ebenfalls beeinflusst.

Unternehmen, die das Roaming für persönliche (nicht verwaltete) Geräte verwalten möchten, können das Azure-Portal zum Aktivieren oder Deaktivieren des Roamings verwenden, anstatt die Gruppenrichtlinie oder MDM.
In den folgenden Tabellen sind die verfügbaren Richtlinieneinstellungen beschrieben.

> [!NOTE]
> Dieser Artikel bezieht sich auf den älteren HTML-basierten Microsoft Edge-Browser, der im Juli 2015 mit Windows 10 veröffentlicht wurde. Der Artikel gilt nicht für den neuen Chromium-basierten Microsoft Edge-Browser, der am 15. Januar 2020 veröffentlicht wurde. Weitere Informationen zum Synchronisierungsverhalten des neuen Microsoft Edge finden Sie im Artikel [Microsoft Edge-Synchronisierung](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>MDM-Einstellungen

Die MDM-Richtlinieneinstellungen gelten sowohl für Windows 10 als auch für Windows 10 Mobile.  Für Windows 10 Mobile wird das auf einem Microsoft-Konto basierende Roaming nur über das OneDrive-Konto eines Benutzers unterstützt. Unter [Geräte und Endpunkte](enterprise-state-roaming-windows-settings-reference.md) finden Sie ausführliche Informationen zu den für die Azure AD-basierte Synchronisierung unterstützten Geräten.

| Name | BESCHREIBUNG |
| --- | --- |
| Microsoft-Konto-Verbindung zulassen |Ermöglicht Benutzern die Authentifizierung mit einem Microsoft-Konto auf dem Gerät. |
| Einstellungen synchronisieren zulassen |Ermöglicht Benutzern das Roaming von Windows-Einstellungen und App-Daten; durch Deaktivieren dieser Richtlinie wird die Synchronisierung mobiler Geräte ebenso wie Sicherungen auf mobilen Geräten deaktiviert. |

## <a name="group-policy-settings"></a>Gruppenrichtlinieneinstellungen

Die Gruppenrichtlinieneinstellungen gelten für Windows 10-Geräte, die in eine Active Directory-Domäne eingebunden sind. Die Tabelle enthält Legacyeinstellungen, die scheinbar zum Verwalten von Synchronisierungseinstellungen geeignet sind, aber für Enterprise State Roaming für Windows 10 nicht funktionieren (in der Beschreibung als „Nicht verwenden“ gekennzeichnet).

Diese Einstellungen befinden sich unter `Computer Configuration > Administrative Templates > Windows Components > Sync your settings`. 

| Name | BESCHREIBUNG |
| --- | --- |
| Konten: Microsoft-Konten blockieren |Diese Richtlinieneinstellung verhindert, dass Benutzer auf diesem Computer neue Microsoft-Konten hinzufügen. |
| Nicht synchronisieren |Verhindert das Roaming von Windows-Einstellungen und App-Daten durch Benutzer. |
| Personalisierung nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Designs“. |
| Browsereinstellungen nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Internet Explorer“. |
| Kennwörter nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Kennwörter“. |
| Weitere Windows-Einstellungen nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Weitere Windows-Einstellungen“. |
| Desktoppersonalisierung nicht synchronisieren |Nicht verwenden, hat keine Auswirkung. |
| Keine Synchronisierung über getaktete Verbindungen |Deaktiviert das Roaming über getaktete Verbindungen, z. B. eine 3G-Mobilfunkverbindung. |
| Apps nicht synchronisieren |Nicht verwenden, hat keine Auswirkung. |
| App-Einstellungen nicht synchronisieren |Deaktiviert das Roaming von App-Daten. |
| Starteinstellungen nicht synchronisieren |Nicht verwenden, hat keine Auswirkung. |

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Übersicht zu [Enterprise State Roaming](enterprise-state-roaming-overview.md) an.
