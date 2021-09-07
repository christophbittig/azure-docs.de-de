---
title: Schützen von Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie schwache Verschlüsselungen, alte Protokolle und die NTLM-Kennworthashsynchronisierung für eine verwaltete Azure AD DS-Domäne (Azure Active Directory Domain Services) deaktivieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/21/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe8f41c2ecf92034f81f2332aabee5a55df66a92
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439252"
---
# <a name="harden-an-azure-active-directory-domain-services-managed-domain"></a>Härten einer über Azure Active Directory Domain Services verwalteten Domäne

Standardmäßig erlaubt Azure Active Directory Domain Services (Azure AD DS) die Verwendung von Verschlüsselungen wie NTLM v1 und TLS v1. Diese Verschlüsselungen sind möglicherweise für einige Legacyanwendungen erforderlich, gelten jedoch als schwach und können deaktiviert werden, wenn Sie sie nicht benötigen. Wenn Sie über eine lokale Hybridverbindung über Azure AD Connect verfügen, können Sie auch die NTLM-Kennworthashsynchronisierung deaktivieren.

In diesem Artikel erfahren Sie, wie Sie eine verwaltete Domäne mithilfe von Einstellungen härten, wie z. B.: 

- Deaktivieren von NTLM v1- und TLS v1-Verschlüsselungen
- Deaktivieren der NTLM-Kennworthashsynchronisierung
- Deaktivieren der Möglichkeit zum Ändern von Kennwörtern mit RC4-Verschlüsselung
- Aktivieren von Kerberos Armoring

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie folgende Ressourcen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance], sofern erforderlich.

## <a name="use-security-settings-to-harden-your-domain"></a>Verwenden von Sicherheitseinstellungen zum Härten Ihrer Domäne

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure AD Domain Services**, und wählen Sie den Eintrag aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie auf der linken Seite die Option **Sicherheitseinstellungen** aus.
1. Klicken Sie bei den folgenden Einstellungen auf **Aktivieren** oder **Deaktivieren**:
   - **Reiner TLS 1.2-Modus**
   - **NTLM-Authentifizierung**
   - **Kennwortsynchronisierung aus lokaler Umgebung**
   - **NTLM-Kennwortsynchronisierung aus lokaler Umgebung**
   - **RC4-Verschlüsselung**
   - **Kerberos-Schutz**

   ![Screenshot der Sicherheitseinstellungen zum Deaktivieren schwacher Verschlüsselungen und der NTLM-Kennworthashsynchronisierung](media/secure-your-domain/security-settings.png)

## <a name="assign-azure-policy-compliance-for-tls-12-usage"></a>Zuweisen der Azure Policy-Compliance für die TLS 1.2-Verwendung

Zusätzlich zu den **Sicherheitseinstellungen** verfügt Microsoft Azure Policy über die Einstellung **Compliance**, um die Verwendung von TLS 1.2 zu erzwingen. Die Richtlinie hat keine Auswirkungen, bis sie zugewiesen wurde. Wenn die Richtlinie zugewiesen ist, wird sie unter **Compliance** angezeigt:

- Wenn die Zuweisung **Überprüfung** lautet, meldet die Compliance, ob die Azure AD DS-Instanz konform ist.
- Wenn die Zuweisung **Verweigern** lautet, verhindert die Compliance, dass eine Azure AD DS-Instanz erstellt wird, wenn TLS 1.2 nicht erforderlich ist, und verhindert jegliche Aktualisierung einer Azure AD DS-Instanz, bis TLS 1.2 erforderlich ist.

![Screenshot der Compliance-Einstellungen](media/secure-your-domain/policy-tls.png)

## <a name="audit-ntlm-failures"></a>Überwachen von NTLM-Fehlern

Das Deaktivieren der NTLM-Kennwortsynchronisierung verbessert zwar die Sicherheit, aber viele Anwendungen und Dienste sind nicht für die Arbeit ohne sie konzipiert. Beispielsweise kann beim Herstellen einer Verbindung mit einer beliebigen Ressource über ihre IP-Adresse (z. B. DNS-Serververwaltung oder RDP) der Zugriff verweigert werden. Wenn Sie die NTLM-Kennwortsynchronisierung deaktivieren und Ihre Anwendung oder Ihr Dienst nicht wie erwartet funktioniert, können Sie eine Überprüfung auf NTLM-Authentifizierungsfehler durchführen. Aktivieren Sie zu diesem Zweck die Sicherheitsüberwachung für die Ereigniskategorie **Anmelden/Abmelden** > **Anmeldung überwachen**, wobei NTLM als **Authentifizierungspaket** in den Ereignisdetails angegeben ist. Weitere Informationen finden Sie unter [Aktivieren von Sicherheitsüberwachungen in Azure Active Directory Domain Services](security-audit-events.md).

## <a name="use-powershell-to-harden-your-domain"></a>Verwenden von PowerShell zum Härten Ihrer Domäne

Bei Bedarf [installieren und konfigurieren Sie Azure PowerShell](/powershell/azure/install-az-ps). Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzAccount][Connect-AzAccount] bei Ihrem Azure-Abonnement anmelden. 

Bei Bedarf [installieren und konfigurieren Sie außerdem Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzureAD][Connect-AzureAD] bei Ihrem Azure AD-Mandanten anmelden.

Wenn Sie schwache Verschlüsselungssammlungen und die NTLM-Kennworthashsynchronisierung deaktivieren möchten, melden Sie sich bei Ihrem Azure-Konto an, und rufen Sie dann mithilfe des Cmdlets [Get-AzResource][Get-AzResource] die Azure AD DS-Ressource ab:

> [!TIP]
> Wenn Sie beim Ausführen des Befehls [Get-AzResource][Get-AzResource] eine Fehlermeldung mit dem Hinweis erhalten, dass die Ressource *Microsoft.AAD/DomainServices* nicht vorhanden ist, [erhöhen Sie die Zugriffsrechte, um alle Azure-Abonnements und Verwaltungsgruppen verwalten zu können][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definieren Sie als Nächstes *DomainSecuritySettings*, um die folgenden Sicherheitsoptionen zu konfigurieren:

1. Deaktivieren der NTLM v1-Unterstützung
2. Deaktivieren der Synchronisierung von NTLM-Kennworthashes aus Ihrer lokalen AD-Instanz
3. Deaktivieren von TLS v1

> [!IMPORTANT]
> Benutzer- und Dienstkonten können keine einfachen LDAP-Bindungen mehr ausführen, wenn Sie die NTLM-Kennworthashsynchronisierung für Ihre verwaltete Azure AD DS-Domäne deaktivieren. Wenn Sie einfache LDAP-Bindungen ausführen müssen, legen Sie im folgenden Befehl die Sicherheitskonfigurationsoption *"SyncNtlmPasswords"="Disabled"* nicht fest.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled";"KerberosRc4Encryption"="Disabled";"KerberosArmoring"="Disabled"}}
```

Wenden Sie schließlich mithilfe des Cmdlets [Set-AzResource][Set-AzResource] die definierten Sicherheitseinstellungen auf die verwaltete Domäne an. Geben Sie die Azure AD DS-Ressource aus dem ersten Schritt und die Sicherheitseinstellungen aus dem vorherigen Schritt an.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -ApiVersion “2021-03-01” -Verbose -Force
```

Es dauert einen Moment, bis die Sicherheitseinstellungen auf die verwaltete Domäne angewendet werden.

> [!IMPORTANT]
> Nachdem Sie die Windows-Authentifizierung (NTLM) deaktiviert haben, führen Sie eine vollständige Kennworthashsynchronisierung in Azure AD Connect durch, um alle Kennworthashes aus der verwalteten Domäne zu entfernen. Wenn Sie die Windows-Authentifizierung deaktivieren, aber keine Kennworthashsynchronisierung erzwingen, werden NTLM-Kennworthashes für ein Benutzerkonto nur bei der nächsten Kennwortänderung entfernt. Dieses Verhalten ermöglicht es einem Benutzer, sich weiterhin anzumelden, wenn er die Anmeldeinformationen auf einem System zwischengespeichert hat, auf dem NTLM als Authentifizierungsmethode verwendet wird.
>
> Wenn sich der NTLM-Kennworthash von dem Kerberos-Kennworthash unterscheidet, funktioniert ein Fallback auf NTLM nicht. Zwischengespeicherte Anmeldeinformationen funktionieren auch nicht mehr, wenn der virtuelle Computer über eine Verbindung mit dem verwalteten Domänencontroller verfügt.  

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Synchronisierungsvorgang finden Sie unter [Synchronisieren von Objekten und Anmeldeinformationen in einer verwalteten Domäne][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
