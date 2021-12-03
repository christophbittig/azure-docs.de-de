---
title: Was sind bei Azure AD registrierte Geräte?
description: Erfahren Sie, wie Sie mit bei Azure AD registrierten Geräten für Ihre Benutzer das BYOD-Szenario (Bring Your Own Device) bzw. mobile Geräte unterstützen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bd3f6993c56187a64ba9db3397a659f9dea098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049917"
---
# <a name="azure-ad-registered-devices"></a>Bei Azure AD registrierte Geräte

Ziel von bei Azure AD registrierten Geräten ist die Bereitstellung der Unterstützung für das BYOD-Szenario (Bring Your Own Device) oder von mobilen Geräten für Ihre Benutzer. In diesen Szenarien kann ein Benutzer mit einem persönlichen Gerät auf die Ressourcen Ihres Unternehmens zugreifen.

| Bei Azure AD registriert | BESCHREIBUNG |
| --- | --- |
| **Definition** | Registrierung bei Azure AD, ohne dass ein Organisationskonto für die Anmeldung am Gerät verwendet werden muss |
| **Hauptzielgruppe** | Gilt für alle Benutzer mit den folgenden Kriterien: |
|   | Bring Your Own Device |
|   | Mobile Geräte |
| **Gerätebesitz** | Benutzer oder Organisation |
| **Betriebssysteme** | Windows 10 und höher, iOS, Android und macOS |
| **Bereitstellung** | Windows 10 und höher – Einstellungen |
|   | iOS/Android: Unternehmensportal oder Microsoft Authenticator-App |
|   | macOS: Unternehmensportal |
| **Anmeldeoptionen für Gerät** | Lokale Anmeldeinformationen von Endbenutzern |
|   | Kennwort |
|   | Windows Hello |
|   | PIN |
|   | Biometrische Daten oder Muster für andere Geräte |
| **Geräteverwaltung** | Mobile Geräteverwaltung (z. B. Microsoft Intune) |
|   | Verwaltung mobiler Anwendungen |
| **Wichtige Funktionen** | SSO für Cloudressourcen |
|   | Bedingter Zugriff bei der Registrierung in Intune |
|   | Bedingter Zugriff über App-Schutzrichtlinie |
|   | Ermöglichung der telefonischen Anmeldung per Microsoft Authenticator-App |

![Bei Azure AD registrierte Geräte](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD-registrierte Geräte werden mit einem lokalen Konto angemeldet, z. B. einem Microsoft-Konto auf einem Gerät mit Windows 10 oder höher. Diese Geräte verfügen über ein Azure AD-Konto für den Zugriff auf Organisationsressourcen. Der Zugriff auf Ressourcen in der Organisation kann basierend auf diesem Azure AD-Konto und auf Richtlinien für den bedingten Zugriff, die auf die Geräteidentität angewendet werden, eingeschränkt werden.

Administratoren können diese bei Azure AD registrierten Geräte schützen und genauer steuern, indem sie MDM-Tools (Mobile Device Management, Mobile Geräteverwaltung) verwenden, z. B. Microsoft Intune. MDM ist eine Möglichkeit zur Erzwingung der von einer Organisation geforderten Konfigurationen, z. B. Verschlüsselung des Speichers, Kennwortkomplexität und aktuelle Sicherheitssoftware. 

Die Azure AD-Registrierung kann durchgeführt werden, wenn zum ersten Mal auf eine Arbeitsanwendung zugegriffen wird oder manuell über das Menü mit den Einstellungen für Windows 10 oder Windows 11. 

## <a name="scenarios"></a>Szenarien

Ein Benutzer Ihrer Organisation möchte von seinem Heim-PC aus auf das Tool für die Registrierung für Boni zugreifen. Ihre Organisation verlangt, dass jeder von einem Intune-konformen Gerät auf dieses Tool zugreift. Der Benutzer registriert seinen Heim-PC bei Azure AD. Dabei werden die erforderlichen Intune-Richtlinien erzwungen, und dem Benutzer wird Zugriff auf seine Ressourcen gewährt.

Ein anderer Benutzer möchte auf sein E-Mail-Konto der Organisation über sein eigenes Android-Smartphone zugreifen, das manipuliert wurde. Für Ihr Unternehmen gilt die Regel, dass ein konformes Gerät verwendet werden muss, und es wurde eine Intune-Konformitätsrichtlinie erstellt, um manipulierte Geräte zu blockieren. Es wird verhindert, dass der Mitarbeiter mit diesem Gerät auf Ressourcen der Organisation zugreift.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der Geräteidentität mithilfe des Azure-Portals](device-management-azure-portal.md)
- [Verwalten von veralteten Geräten in Azure AD](manage-stale-devices.md)
