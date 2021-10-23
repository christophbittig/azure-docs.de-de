---
title: Deaktivieren der lokalen Authentifizierung in Azure Automation
description: In diesem Artikel wird das Deaktivieren der lokalen Authentifizierung in Azure Automation beschrieben.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 54da805b7f4c2332d0d475d2c5eb31b24fca597f
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165651"
---
# <a name="disable-local-authentication-in-automation"></a>Deaktivieren der lokalen Authentifizierung in Automation

Azure Automation bietet Microsoft Azure AD-Authentifizierungsunterstützung (Azure Active Directory) für alle öffentlichen Endpunkte des Automation-Diensts. Durch diese wichtige Sicherheitserweiterung werden Zertifikatabhängigkeiten aufgehoben, und Organisationen erhalten die Möglichkeit, lokale Authentifizierungsmethoden zu deaktivieren. Dieses Feature ermöglicht eine nahtlose Integration, wenn eine zentralisierte Steuerung und Verwaltung von Identitäten und Ressourcenanmeldeinformationen über Azure AD erforderlich ist.

Azure Automation bietet ein optionales Feature zum Deaktivieren der lokalen Authentifizierung auf Automation-Kontoebene mithilfe der Azure-Richtlinie [Das Azure Automation-Konto konfigurieren, um die lokale Authentifizierung zu deaktivieren](../automation/policy-reference.md#azure-automation). Standardmäßig ist dieses Flag für das Konto auf „false“ festgelegt, sodass Sie sowohl die lokale Authentifizierung als auch die Azure AD-Authentifizierung verwenden können. Wenn Sie die lokale Authentifizierung deaktivieren, akzeptiert der Automation-Dienst nur die Azure AD-basierte Authentifizierung.

Im Azure-Portal wird möglicherweise eine Warnmeldung auf der Landing Page für das ausgewählte Automation-Konto angezeigt, wenn die Authentifizierung deaktiviert ist.Wenn Sie überprüfen möchten, ob die lokale Authentifizierungsrichtlinie aktiviert ist, verwenden Sie das PowerShell-Cmdlet [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount), und überprüfen Sie die Eigenschaft `DisableLocalAuth`. Der Wert `true` bedeutet, dass die lokale Authentifizierung deaktiviert ist.

Das Deaktivieren der lokalen Authentifizierung tritt nicht sofort in Kraft. Warten Sie einige Minuten, bis der Dienst zukünftige Authentifizierungsanforderungen blockiert.
 
## <a name="re-enable-local-authentication"></a>Erneutes Aktivieren der lokalen Authentifizierung

Zum erneuten Aktivieren der lokalen Authentifizierung führen Sie das PowerShell-Cmdlet `Set-AzAutomationAccount` mit dem Parameter `-DisableLocalAuth false` aus.Warten Sie einige Minuten, bis der Dienst die Änderung akzeptiert hat und lokale Authentifizierungsanforderungen zulässt. 

## <a name="compatibility"></a>Kompatibilität

In der folgenden Tabelle werden die Verhaltensweisen oder Features beschrieben, die bei Deaktivieren der lokalen Authentifizierung nicht mehr funktionieren.

|Szenario | Alternative |
|---|---|
|Starten eines Runbooks über einen Webhook | Starten Sie einen Runbookauftrag mithilfe einer Azure Resource Manager-Vorlage, die Azure AD-Authentifizierung verwendet. |
|Verwenden der Automation-Konfiguration für den gewünschten Zustand| Verwenden Sie die [Azure Policy-Gastkonfiguration](../governance/policy/concepts/guest-configuration.md).  |
|Verwenden von Agent-basierten Hybrid Runbook Workern| Verwenden Sie [erweiterungsbasierte Hybrid Runbook Worker (Vorschau)](./extension-based-hybrid-runbook-worker-install.md).|

## <a name="limitations"></a>Einschränkungen

Wenn die lokale Authentifizierung deaktiviert ist, funktioniert das Patchen der Updateverwaltung nicht.


## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über die Azure Automation-Kontoauthentifizierung](./automation-security-overview.md)
