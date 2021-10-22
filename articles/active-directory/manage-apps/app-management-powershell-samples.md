---
title: PowerShell-Beispiele in der Anwendungsverwaltung
titleSuffix: Azure AD
description: Diese PowerShell-Beispiele werden für Apps verwendet, die Sie in Ihrem Azure Active Directory-Mandanten verwalten. Mithilfe dieser Beispielskripts können Sie Ablaufinformationen zu Geheimnissen und Zertifikaten ermitteln.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: davidmu
ms.reviewer: sureshja
ms.openlocfilehash: 95f0ee8106612dd2035f0e1c77d9fe5d3efe2540
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620030"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory-PowerShell-Beispiele für die Anwendungsverwaltung

Die folgende Tabelle enthält Links zu PowerShell-Skriptbeispielen für die Azure AD-Anwendungsverwaltung. Voraussetzungen für diese Beispiele:

- [Azure AD PowerShell V2-Modul für Graph](/powershell/azure/active-directory/install-adv2) oder
- [Azure AD PowerShell V2-Modul in der Vorschauversion für Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (sofern nicht anders angegeben)

Weitere Informationen zu den in diesen Beispielen verwendeten Cmdlets finden Sie unter [Anwendungen](/powershell/module/azuread/#applications).

| Link | BESCHREIBUNG |
|---|---|
|**Skripts für die Anwendungsverwaltung**||
| [Exportieren von Geheimnissen und Zertifikaten (App-Registrierungen)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exportieren Sie Geheimnisse und Zertifikate für App-Registrierungen im Azure Active Directory-Mandanten. |
| [Exportieren von Geheimnissen und Zertifikaten (Unternehmens-Apps)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exportieren Sie Geheimnisse und Zertifikate für Unternehmens-Apps im Azure Active Directory-Mandanten. |
| [Exportieren von ablaufenden Geheimnissen und Zertifikaten](scripts/powershell-export-apps-with-expriring-secrets.md) | Exportieren Sie App-Registrierungen mit ablaufenden Geheimnissen und Zertifikaten und deren Besitzer im Azure Active Directory-Mandanten. |
| [Exportieren von Geheimnissen und Zertifikaten, die nach dem erforderlichen Datum ablaufen](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exportieren Sie App-Registrierungen mit Geheimnissen und Zertifikaten, die nach dem erforderlichen Datum im Azure Active Directory-Mandanten ablaufen. Dabei wird der nicht interaktive OAuth-Fluss „Client_Credentials“ verwendet. |
