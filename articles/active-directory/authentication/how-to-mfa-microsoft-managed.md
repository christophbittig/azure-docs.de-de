---
title: Verwenden Sie von Microsoft verwaltete Einstellungen für die Richtlinie Authentifizierungsmethoden - Azure Active Directory
description: Erfahren Sie, wie Sie die von Microsoft verwalteten Einstellungen für Microsoft Authenticator verwenden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4134998bd3c7967d5ec151b900d67364373a2659
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566605"
---
# <a name="how-to-use-microsoft-managed-settings---authentication-methods-policy"></a>So verwenden Sie die von Microsoft verwalteten Einstellungen - Richtlinie für Authentifizierungsmethoden

Zusätzlich zur Konfiguration der Authentifizierungsmethoden-Richtlinieneinstellungen als **Aktiviert** oder **Deaktiviert** können IT-Administratoren einige Einstellungen als **Microsoft-verwaltet** konfigurieren. Eine Einstellung, die als **Microsoft verwaltet** konfiguriert ist, ermöglicht Azure AD die Aktivierung oder Deaktivierung der Einstellung. 

## <a name="settings-that-can-be-microsoft-managed"></a>Einstellungen, die von Microsoft verwaltet werden können

In der folgenden Tabelle sind die Einstellungen aufgeführt, die für die Microsoft-Verwaltung festgelegt werden können, und ob sie aktiviert oder deaktiviert ist. 

| Einstellung         | Konfiguration |
|-----------------|---------------|
| [Registrierungskampagne](how-to-nudge-authenticator-app.md)  | Disabled      |
| Nummernübereinstimmung        | Disabled      |
| Zusätzlicher Kontext  | Disabled      |

## <a name="next-steps"></a>Nächste Schritte

[Authentifizierungsmethoden in Azure Active Directory – Microsoft Authenticator-App](concept-authentication-authenticator-app.md)
