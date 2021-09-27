---
title: Was ist ein in Azure AD eingebundenes Hybridgerät?
description: Erfahren Sie, wie Sie mithilfe der Geräteidentitätsverwaltung Geräte verwalten können, die auf Ressourcen in Ihrer Umgebung zugreifen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccd53294598be8103b7ce9774e65aef802e79205
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618075"
---
# <a name="hybrid-azure-ad-joined-devices"></a>In Azure AD eingebundene Hybridgeräte

Organisationen mit vorhandenen Active Directory-Implementierungen können von einigen Funktionen profitieren, die von Azure Active Directory (Azure AD) bereitgestellt werden, indem sie in Azure AD eingebundene Hybridgeräte implementieren. Diese Geräte sind in Ihre lokale Active Directory-Instanz eingebunden und dafür registriert.

In Azure AD eingebundene Hybridgeräte benötigen regelmäßig eine Netzwerk-Sichtverbindung mit Ihren lokalen Domänencontrollern. Ohne diese Verbindung werden Geräte unbrauchbar. Wenn diese Anforderung ein Problem ist, sollten Sie die [Azure AD-Einbindung](concept-azure-ad-join.md) Ihrer Geräte in Erwägung ziehen.

| Azure AD Hybrid Join | BESCHREIBUNG |
| --- | --- |
| **Definition** | Eingebunden in lokales AD und Azure AD, und es ist eine Anmeldung bei dem Gerät mit dem Organisationskonto erforderlich. |
| **Hauptzielgruppe** | Geeignet für Hybridorganisationen mit vorhandener lokaler AD-Infrastruktur. |
|   | Anwendbar für alle Benutzer einer Organisation. |
| **Gerätebesitz** | Organization |
| **Betriebssysteme** | Windows 10, 8.1 und 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 und 2019 |
| **Bereitstellung** | Windows 10, Windows Server 2016/2019 |
|   | Domänenbeitritt über IT und automatischer Beitritt per Azure AD Connect- oder ADFS-Konfiguration |
|   | Domänenbeitritt per Windows Autopilot und automatischer Beitritt per Azure AD Connect- oder ADFS-Konfiguration |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 und Windows Server 2008 R2: MSI erforderlich |
| **Anmeldeoptionen für Gerät** | Organisationskonten mit: |
|   | Kennwort |
|   | Windows Hello for Business für Windows 10 |
| **Geräteverwaltung** | Gruppenrichtlinie |
|   | Eigenständige Configuration Manager-Version oder kombinierte Verwaltung mit Microsoft Intune |
| **Wichtige Funktionen** | Einmaliges Anmelden sowohl für cloudbasierte als auch für lokale Ressourcen |
|   | Bedingter Zugriff per Domänenbeitritt oder mit Intune bei kombinierter Verwaltung |
|   | Self-Service-Kennwortzurücksetzung und Windows Hello-PIN-Zurücksetzung auf dem Sperrbildschirm |
|   | Geräteübergreifendes Enterprise State Roaming |

![In Azure AD eingebundene Hybridgeräte](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Szenarien

Verwenden Sie in Azure AD eingebundene Hybridgeräte in folgenden Fällen:

- Sie unterstützen kompatible Geräte unter Windows 7 und 8.1.
- Sie möchten die Gruppenrichtlinie weiterhin nutzen, um die Gerätekonfiguration zu verwalten.
- Sie möchten weiterhin vorhandene Lösungen für die Imageerstellung verwenden, um Geräte bereitzustellen und zu konfigurieren.
- Auf den Geräten sind Win32-Apps bereitgestellt, die die Active Directory-Authentifizierung für Computer benötigen.

## <a name="next-steps"></a>Nächste Schritte

- [Planen der Implementierung Ihrer Azure AD-Hybrideinbindung](hybrid-azuread-join-plan.md)
- [Verwalten der Geräteidentität mithilfe des Azure-Portals](device-management-azure-portal.md)
- [Verwalten von veralteten Geräten in Azure AD](manage-stale-devices.md)
