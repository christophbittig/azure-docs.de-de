---
title: 'Schnellstart: Hinzufügen eines Gastbenutzers mit PowerShell – Azure AD'
description: In diesem Schnellstart erfahren Sie, wie Sie mit PowerShell eine Einladung an einen externen Benutzer für die Azure AD B2B-Zusammenarbeit senden.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/28/2018
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom: it-pro, seo-update-azuread-jan, mode-api
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42dd773bd88b7f51aa23fda8463a31ec2d41a6bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052482"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Schnellstart: Hinzufügen eines Gastbenutzers mit PowerShell

Es gibt viele Möglichkeiten, externe Partner zu Ihren Apps und Diensten für die Azure Active Directory B2B-Zusammenarbeit einzuladen. Im vorherigen Schnellstart haben Sie erfahren, wie Sie Gastbenutzer direkt im Azure Active Directory-Verwaltungsportal hinzufügen. Sie können auch PowerShell verwenden, um Gastbenutzer einzeln oder gruppenweise hinzuzufügen. In diesem Schnellstart fügen Sie mit dem Befehl „New-AzureADMSInvitation“ Ihrem Azure-Mandanten einen Gastbenutzer hinzu.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

### <a name="powershell-module"></a>PowerShell-Modul
Installieren Sie das [Azure AD PowerShell V2-Modul für Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) oder das [Azure AD PowerShell V2-Modul für Graph in der Vorschauversion](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

### <a name="get-a-test-email-account"></a>Einrichten eines Test-E-Mail-Kontos

Sie benötigen ein Test-E-Mail-Konto, an das Sie die Einladung senden können. Das Konto muss sich außerhalb Ihrer Organisation befinden. Sie können jede Art von Konto verwenden, auch ein Konto bei sozialen Netzwerken wie die „gmail.com“- oder „outlook.com“-Adresse.

## <a name="sign-in-to-your-tenant"></a>Anmelden im Mandanten

Führen Sie den folgenden Befehl aus, um eine Verbindung mit der Mandantendomäne herzustellen:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Beispiel: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Geben Sie bei Aufforderung Ihre Anmeldeinformationen ein.

## <a name="send-an-invitation"></a>Senden einer Einladung

1. Um eine Einladung an Ihr Test-E-Mail-Konto zu senden, führen Sie den folgenden PowerShell-Befehl aus, und ersetzen Sie dabei **Sanda** und **sanda\@fabrikam.com** durch den Namen Ihres Test-E-Mail-Kontos und Ihre E-Mail-Adresse: 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. Der Befehl sendet eine Einladung an die angegebene E-Mail-Adresse. Die Ausgabe sollte etwa so aussehen:

   ![PowerShell-Ausgabe: ausstehende Annahme durch Benutzer](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Überprüfen, ob der Benutzer im Verzeichnis vorhanden ist

1. Um zu überprüfen, ob der eingeladene Benutzer Azure AD hinzugefügt wurde, führen Sie den folgenden Befehl aus:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Überprüfen Sie, ob der von Ihnen eingeladene Benutzer mit einem Benutzerprinzipalnamen (UPN) im Format *E-Mail-Adresse*#EXT#\@*Domäne* in der Ausgabe angezeigt wird. Beispiel: *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, wobei „contoso.onmicrosoft.com“ der Organisation entspricht, für die Sie die Einladungen gesendet haben.

   ![PowerShell-Ausgabe: Gastbenutzer wurde hinzugefügt](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Testbenutzerkonto nicht mehr benötigen, können Sie es aus dem Verzeichnis löschen. Führen Sie den folgenden Befehl aus, um ein Benutzerkonto zu löschen:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Beispiel: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie mit PowerShell einen einzelnen Gastbenutzer zu Ihrem Verzeichnis eingeladen und hinzugefügt. Als Nächstes erfahren Sie, wie Sie mit PowerShell Masseneinladungen an Gastbenutzer senden.

> [!div class="nextstepaction"]
> [Tutorial: Masseneinladen von Benutzern für die Azure AD B2B-Zusammenarbeit](tutorial-bulk-invite.md)
