---
title: Widerrufen des Benutzerzugriffs im Notfall in Azure Active Directory | Microsoft-Dokumentation
description: Widerrufen des gesamten Zugriffs für einen Benutzer in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: KarenH444
ms.reviewer: krbain
ms.date: 03/29/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10f530b703c67192104e3cb6ea553973b8534437
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292640"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Widerrufen des Benutzerzugriffs in Azure Active Directory

Szenarien, die einen Administrator dazu zwingen könnten, einem Benutzer jeglichen Zugriff zu entziehen, umfassen kompromittierte Konten, die Kündigung des Beschäftigungsverhältnisses und andere innerbetriebliche Bedrohungen. Je nach Komplexität der Umgebung können Administratoren mehrere Schritte unternehmen, um sicherzustellen, dass der Zugriff widerrufen wird. In einigen Szenarien kann zwischen der Einleitung des Widerrufs des Zugriffs und dem Zeitpunkt, an dem der Zugriff tatsächlich widerrufen wird, eine gewisse Zeitspanne liegen.

Um die Risiken zu entschärfen, müssen Sie verstehen, wie Token funktionieren. Es gibt viele Arten von Token, die unter eines der in den folgenden Abschnitten erwähnten Muster fallen.

## <a name="access-tokens-and-refresh-tokens"></a>Zugriffs- und Aktualisierungstoken

Zugriffs- und Aktualisierungstoken werden häufig bei umfangreichen Clientanwendungen verwendet und auch in browserbasierten Anwendungen wie Single-Page-Webanwendungen eingesetzt.

- Wenn Benutzer sich bei Azure AD authentifizieren, werden Autorisierungsrichtlinien ausgewertet, um festzustellen, ob dem Benutzer Zugriff auf eine bestimmte Ressource gewährt werden darf.  

- Bei Autorisierung stellt Azure AD ein Zugriffs- und ein Aktualisierungstoken für die Ressource aus.  

- Von Azure AD ausgestellte Zugriffstoken sind standardmäßig 1 Stunde gültig. Wenn das Authentifizierungsprotokoll es zulässt, kann die App den Benutzer automatisch erneut authentifizieren, indem sie das Aktualisierungstoken an Azure AD übergibt, sobald das Zugriffstoken abläuft.

Azure AD wertet dann die Autorisierungsrichtlinien erneut aus. Wenn der Benutzer weiterhin autorisiert ist, stellt Azure AD ein neues Zugriffstoken aus und aktualisiert dieses.

Zugriffstoken können ein Sicherheitsproblem darstellen, wenn der Zugriff innerhalb einer Zeitspanne widerrufen werden muss, die kürzer als die Gültigkeitsdauer des Token ist, die normalerweise etwa eine Stunde beträgt. Aus diesem Grund arbeitet Microsoft aktiv daran, [die kontinuierliche Auswertung](../conditional-access/concept-continuous-access-evaluation.md) des Zugriffs in Office 365-Anwendungen vorzunehmen, was dazu beiträgt, die Invalidierung von Zugriffstoken nahezu in Echtzeit zu gewährleisten.  

## <a name="session-tokens-cookies"></a>Sitzungstoken (Cookies)

Die meisten browserbasierten Anwendungen verwenden Sitzungstoken anstelle von Zugriffs- und Aktualisierungstoken.  

- Wenn ein Benutzer einen Browser öffnet und sich über Azure AD bei einer Anwendung authentifiziert, empfängt der Benutzer zwei Sitzungstoken: eines von Azure AD und ein zweites von der Anwendung.  

- Wenn eine Anwendung ein eigenes Sitzungstoken ausstellt, wird der Zugriff auf die Anwendung durch die Sitzung der Anwendung geregelt. Zu diesem Zeitpunkt unterliegt der Benutzer nur den Autorisierungsrichtlinien, die der Anwendung bekannt sind.

- Die Autorisierungsrichtlinien von Azure AD werden so oft neu ausgewertet, wie die Anwendung den Benutzer zu Azure AD zurückleitet. Die erneute Auswertung erfolgt in der Regel automatisch, obwohl die Häufigkeit von der Konfiguration der Anwendung abhängt. Es ist möglich, dass die App den Benutzer keinesfalls zu Azure AD zurückleitet, solange das Sitzungstoken gültig ist.

- Damit ein Sitzungstoken widerrufen werden kann, muss die Anwendung den Zugriff auf der Grundlage ihrer eigenen Autorisierungsrichtlinien widerrufen. Azure AD kann ein von einer Anwendung ausgestelltes Sitzungstoken nicht direkt widerrufen.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Widerrufen des Zugriffs eines Benutzers in der Hybridumgebung

Für eine Hybridumgebung mit lokalem Active Directory, das mit Azure Active Directory synchronisiert ist, empfiehlt Microsoft IT-Administratoren, die folgenden Maßnahmen zu ergreifen. Wenn Sie über eine **reine Azure AD-Umgebung** verfügen, können Sie direkt mit dem Abschnitt [Azure Active Directory-Umgebung](#azure-active-directory-environment) fortfahren.


### <a name="on-premises-active-directory-environment"></a>Lokale Active Directory-Umgebung

Stellen Sie als Active Directory-Administrator eine Verbindung mit Ihrem lokalen Netzwerk her, öffnen Sie PowerShell, und führen Sie die folgenden Aktionen durch:

1. Deaktivieren Sie den Benutzer in Active Directory. Weitere Informationen finden Sie unter [Disable-ADAccount](/powershell/module/activedirectory/disable-adaccount).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

2. Setzen Sie das Kennwort des Benutzers in Active Directory zweimal zurück. Weitere Informationen finden Sie unter [Set-ADAccountPassword](/powershell/module/activedirectory/set-adaccountpassword).

    > [!NOTE]
    > Der Grund für das zweimalige Ändern des Kennworts eines Benutzers besteht darin, das Pass-the-Hash-Risiko zu verringern, insbesondere wenn es zu Verzögerungen bei der Replikation des lokalen Kennworts kommt. Wenn Sie sicher davon ausgehen können, dass dieses Konto nicht kompromittiert ist, genügt es, das Kennwort nur einmal zurückzusetzen.

    > [!IMPORTANT]
    > Verwenden Sie die Beispielkennwörter nicht in den folgenden Cmdlets. Stellen Sie sicher, dass Sie die Kennwörter in eine Zufallszeichenfolge ändern.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory-Umgebung

Öffnen Sie als Azure Active Directory-Administrator PowerShell. Führen Sie dann ``Connect-AzureAD`` und die folgenden Aktionen aus:

1. Deaktivieren Sie den Benutzer in Azure AD. Weitere Informationen finden Sie unter [Set-AzureADUser](/powershell/module/azuread/Set-AzureADUser).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```

2. Widerrufen Sie die Azure AD-Aktualisierungstoken des Benutzers. Weitere Informationen finden Sie unter [Revoke-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

3. Deaktivieren Sie die Geräte des Benutzers. Weitere Informationen finden Sie unter [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```
## <a name="when-access-is-revoked"></a>Wann der Zugriff widerrufen wird

Sobald Administratoren die oben genannten Schritte ausgeführt haben, kann der Benutzer keine neuen Token für Anwendungen erhalten, die an Azure Active Directory gebunden sind. Die Zeit, die zwischen dem Widerruf und dem Entzug des Zugriffs durch den Benutzer vergeht, hängt davon ab, wie die Anwendung Zugriff gewährt:

- Bei **Anwendungen, die Zugriffstoken verwenden**, verliert der Benutzer den Zugriff, wenn das Zugriffstoken abläuft.

- Bei **Anwendungen, in denen Sitzungstoken verwendet werden**, enden die bestehenden Sitzungen, sobald das Token abläuft. Wenn der deaktivierte Zustand des Benutzers mit der Anwendung synchronisiert ist, kann die Anwendung die bestehenden Sitzungen des Benutzers automatisch widerrufen, sofern sie entsprechend konfiguriert ist.  Die Zeitspanne hängt von der Häufigkeit der Synchronisierung zwischen Anwendung und Azure AD ab.

## <a name="best-practices"></a>Bewährte Methoden

- Stellen Sie eine automatisierte Lösung für Bereitstellung und Aufhebung bereit. Das Aufheben der Bereitstellung von Benutzern für Anwendungen ist eine effektive Möglichkeit, den Zugriff zu widerrufen, insbesondere für Anwendungen, die Sitzungstoken verwenden. Entwickeln Sie einen Prozess zum Aufheben der Bereitstellung von Benutzern für Apps, die keine automatische Bereitstellung und Aufhebung unterstützen. Stellen Sie sicher, dass Anwendungen ihre eigenen Sitzungstoken widerrufen und keine Azure AD-Zugriffstoken akzeptieren, auch wenn diese noch gültig sind.

  - Verwenden Sie [SaaS-App-Bereitstellung von Azure AD](../app-provisioning/user-provisioning.md). Die SaaS-App-Bereitstellung von Azure AD wird in der Regel automatisch alle 20 bis 40 Minuten ausgeführt. [Konfigurieren Sie die Azure AD-Bereitstellung](../saas-apps/tutorial-list.md), um die Bereitstellung von deaktivierten Benutzern in Anwendungen aufzuheben oder diese endgültig zu deaktivieren.
  
  - Verwenden Sie für Anwendungen, die nicht die SaaS-App-Bereitstellung von Azure AD verwenden, [Identity Manager (MIM)](/microsoft-identity-manager/mim-how-provision-users-adds) oder eine Drittanbieterlösung, um die Aufhebung der Benutzerbereitstellung zu automatisieren.  
  - Ermitteln und entwickeln Sie einen Prozess für Anwendungen, für die eine manuelle Aufhebung der Bereitstellung erforderlich ist. Stellen Sie sicher, dass Administratoren die erforderlichen manuellen Aufgaben zum Aufheben der Benutzerbereitstellung für diese Apps schnell ausführen können.
  
- [Verwalten Sie Geräte und Anwendungen mit Microsoft Intune](/mem/intune/remote-actions/device-management). Mit Intune verwaltete [Geräte können auf die Werkseinstellungen zurückgesetzt werden](/mem/intune/remote-actions/devices-wipe). Wenn das Gerät nicht verwaltet wird, können Sie [Unternehmensdaten aus verwalteten Apps löschen](/mem/intune/apps/apps-selective-wipe). Diese Prozesse sind effektiv, um potenziell vertrauliche Daten von Endbenutzergeräten zu entfernen. Allerdings muss das Gerät mit dem Internet verbunden sein, damit diese Prozesse ausgelöst werden können. Wenn das Gerät offline ist, kann das Gerät weiterhin auf lokal gespeicherte Daten zugreifen.

> [!NOTE]
> Daten auf dem Gerät können nach dem Zurücksetzen nicht wiederhergestellt werden.

- Verwenden Sie [Microsoft Defender for Cloud Apps, um den Datendownload](/cloud-app-security/use-case-proxy-block-session-aad) ggf. zu blockieren. Wenn nur ein Online-Zugriff auf die Daten möglich ist, können Organisationen Sitzungen überwachen und die Durchsetzung von Richtlinien in Echtzeit erreichen.

- Aktivieren Sie die [fortlaufende Zugriffsevaluierung (CAE) in Azure AD](../conditional-access/concept-continuous-access-evaluation.md). Mithilfe von CAE können Administratoren die Sitzungstoken und Zugriffstoken für Anwendungen widerrufen, die CAE unterstützen.  

## <a name="next-steps"></a>Nächste Schritte

- [Sichere Zugriffsmethoden für Azure AD-Administratoren](../roles/security-planning.md)
- [Hinzufügen oder Aktualisieren von Benutzerprofilinformationen](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Entfernen oder Löschen eines ehemaligen Mitarbeiters](/microsoft-365/admin/add-users/remove-former-employee)
