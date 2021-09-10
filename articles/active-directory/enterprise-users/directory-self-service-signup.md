---
title: Self-Service-Registrierung für per E-Mail verifizierte Benutzer – Azure AD | Microsoft-Dokumentation
description: Verwenden der Self-Service-Registrierung in einer Azure Active Directory-Organisation (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 09/01/2021
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f02ff10ab2eaee7460942e1ba98df1f9771bfc
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426002"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Was ist die Self-Service-Registrierung für Azure Active Directory?

In diesem Artikel wird erläutert, wie Sie die Self-Service-Registrierung verwenden, um ein Unternehmen in Azure Active Directory (Azure AD) einzugeben. Wenn Sie einen Domänennamen von einer nicht verwalteten Azure AD-Organisation übernehmen möchten, lesen Sie [Übernehmen eines nicht verwalteten Mandanten als Administrator](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Gründe für das Verwenden der Self-Service-Registrierung

* Kunden erhalten schneller die gewünschten Dienste.
* Sie können E-Mail-basierte Angebote für einen Dienst erstellen.
* Sie können E-Mail-basierte Registrierungsabläufe erstellen, mit denen Benutzer schnell Identitäten mithilfe ihrer einfach zu merkenden geschäftlichen E-Mail-Aliase erstellen können.
* Ein per Self-Service erstellter Azure AD-Mandant kann in einen verwalteten Mandanten konvertiert werden, der für andere Dienste verwendet werden kann.

## <a name="terms-and-definitions"></a>Begriffe und Definitionen

* **Self-Service-Registrierung**: Dies ist die Methode, mit der sich ein Benutzer für einen Clouddienst registriert, wobei für ihn basierend auf seiner E-Mail-Domäne automatisch eine Identität in Azure AD erstellt wird.
* **Nicht verwalteter Azure AD-Mandant:** Dies ist der Mandant, in dem diese Identität erstellt wird. Ein nicht verwalteter Mandant ist ein Mandant ohne globalen Administrator.
* **Über E-Mail verifizierter Benutzer:** Dies ist ein Typ von Benutzerkonto in Azure AD. Ein Benutzer, für den nach der Registrierung für ein Self-Service-Angebot automatisch eine Identität erstellt wird, wird als über E-Mail verifizierter Benutzer bezeichnet. Ein über E-Mail verifizierter Benutzer ist ein normales Mitglied eines Mandanten mit der Kennzeichnung „creationmethod=EmailVerified“.

## <a name="how-do-i-control-self-service-settings"></a>Wie steuere ich Self-Service-Einstellungen?

Administratoren stehen derzeit zwei Self-Service-Steuerungsmöglichkeiten zur Verfügung. Sie können steuern, ob:

* Benutzer dem Mandanten per E-Mail beitreten können.
* Benutzer sich selbst für Anwendungen und Dienste lizenzieren können.

### <a name="how-can-i-control-these-capabilities"></a>Wie können diese Funktionen gesteuert werden?

Ein Administrator kann diese Funktionen mit den folgenden Parametern des Azure AD-Cmdlets „Set-MsolCompanySettings“ konfigurieren:

* **AllowEmailVerifiedUsers** steuert, ob Benutzer dem Mandanten per E-Mail-Überprüfung beitreten können. Zum Beitreten muss der Benutzer über eine E-Mail-Adresse in einer Domäne verfügen, die einer der überprüften Domänen im Mandanten entspricht. Diese Einstellung wird unternehmensweit auf alle Domänen im Mandanten angewendet. Wenn Sie diesen Parameter auf „$false“ festlegen, können dem Mandanten keine über E-Mail verifizierten Benutzer beitreten.
* **AllowAdHocSubscriptions** steuert, ob Benutzern das Ausführen der Self-Service-Registrierung erlaubt ist. Wenn Sie diesen Parameter auf „$false“ festlegen, können Benutzer keine Self-Service-Registrierung ausführen.
  
„AllowEmailVerifiedUsers“ und „AllowAdHocSubscriptions“ sind mandantenweite Einstellungen, die auf einen verwalteten oder nicht verwalteten Mandanten angewendet werden können. Hier sehen Sie ein Beispiel:

* Sie verwalten einen Mandanten mit einer verifizierten Domäne, z. B. „contoso.com“.
* Sie verwenden B2B-Zusammenarbeit aus einem anderen Mandanten, um einen Benutzer, der noch nicht vorhanden ist (userdoesnotexist@contoso.com), in den Basismandanten von „contoso.com“ einzuladen.
* Für den Basismandanten ist der Parameter „AllowEmailVerifiedUsers“ aktiviert.

Wenn die oben genannten Bedingungen erfüllt sind, wird ein Mitgliedsbenutzer im Basismandanten und ein B2B-Gastbenutzer im einladenden Mandanten erstellt.

Weitere Informationen zu Testregistrierungen für Flow und Power Apps finden Sie in den folgenden Artikeln:

* [Wie kann ich verhindern, dass vorhandene Benutzer mit der Nutzung von Power BI beginnen?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Flow in Ihrer Organisation – häufig gestellte Fragen](/power-automate/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Wie funktionieren diese Steuerungsmöglichkeiten zusammen?
Diese beiden Parameter können zusammen verwendet werden, um eine präzisere Steuerung der Self-Service-Registrierung zu erreichen. Der folgende Befehl erlaubt Benutzern beispielsweise die Self-Service-Registrierung, jedoch nur, wenn die Benutzer bereits über ein Konto in Azure AD verfügen (d. h. Benutzern, für die zuerst ein über E-Mail verifiziertes Konto erstellt werden müsste, ist die Self-Service-Registrierung nicht erlaubt):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Im folgenden Flussdiagramm werden die verschiedenen Kombinationen für diese Parameter und die resultierenden Bedingungen für den Mandanten und die Self-Service-Registrierung erläutert.

![Flussdiagramm der Steuerelemente für die Self-Service-Registrierung](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Die Details dieser Einstellung können mit dem PowerShell-Cmdlet „Get-MsolCompanyInformation“ abgerufen werden. Weitere Informationen dazu finden Sie unter [Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation).

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

Weitere Informationen und Beispiele zum Verwenden dieser Parameter finden Sie unter [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings).

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure AD](../fundamentals/add-custom-domain.md)
* [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure-Cmdlet-Referenz](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)
* [Schließen Ihres Geschäfts-, Schul- oder Unikontos in einem nicht verwalteten Mandanten](users-close-account.md)
