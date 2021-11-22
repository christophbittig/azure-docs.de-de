---
title: Anmeldung bei Azure AD mit einer E-Mail-Adresse als alternative Anmelde-ID
description: Hier erfahren Sie, wie Sie es Benutzern ermöglichen, sich mit ihren E-Mail-Adressen als alternative Anmelde-ID bei Azure Active Directory anzumelden.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/07/2021
ms.author: justinha
author: calui
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: e7b34e98776f252c2122d58601c8067df208b2f2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486793"
---
# <a name="sign-in-to-azure-ad-with-email-as-an-alternate-login-id-preview"></a>Anmeldung bei Azure AD mit einer E-Mail-Adresse als alternative Anmelde-ID (Vorschau)

> [!NOTE]
> Die Anmeldung bei Azure AD mit der E-Mail-Adresse als alternative Anmelde-ID ist eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Viele Organisationen möchten es Benutzern ermöglichen, sich mit denselben Anmeldeinformationen bei Azure Active Directory (Azure AD) anzumelden, die sie auch für ihre lokale Verzeichnisumgebung verwenden. Bei diesem Ansatz, der als Hybridauthentifizierung bezeichnet wird, müssen sich Benutzer nur eine Kombination von Anmeldeinformationen merken.

Einige Organisationen sind aus folgenden Gründen nicht auf die Hybridauthentifizierung umgestiegen:

* Der Azure AD-UPN (Benutzerprinzipalname) wird standardmäßig auf denselben Wert wie der lokale UPN festgelegt.
* Wenn Sie den Azure AD-UPN ändern, stimmen die lokale Umgebung und die Azure AD-Umgebung nicht mehr überein, was zu Problemen mit bestimmten Anwendungen und Diensten führen kann.
* Aufgrund von betrieblichen oder Compliancegründen möchte die Organisation den lokalen UPN nicht für die Anmeldung bei Azure AD verwenden.

Zur Unterstützung der Umstellung auf die Hybridauthentifizierung können Sie Azure AD so konfigurieren, dass sich Benutzer mit ihren E-Mail-Adressen als alternative Anmelde-ID anmelden können. Wenn *Contoso* beispielsweise eine Markenumstellung auf *Fabrikam* durchführen würde, könnte die E-Mail-Adresse als alternative Anmelde-ID verwendet werden, anstatt Anmeldungen weiterhin mit dem veralteten UPN `ana@contoso.com` durchzuführen. Für den Zugriff auf eine Anwendung oder einen Dienst würden Benutzer sich dann mit einer E-Mail-Adresse bei Azure AD anmelden, die nicht als UPN registriert ist, z. B. `ana@fabrikam.com`.

![Diagramm der E-Mail-Adresse als alternative Anmelde-ID.](media/howto-authentication-use-email-signin/email-alternate-login-id.png)

In diesem Artikel erfahren Sie, wie Sie die E-Mail-Adresse als alternative Anmelde-ID aktivieren und verwenden.

## <a name="before-you-begin"></a>Voraussetzungen

Sie müssen Folgendes über die Verwendung von E-Mail-Adressen als alternative Anmelde-IDs wissen:

* Das Feature ist in Azure AD Free und höher verfügbar.
* Das Feature ermöglicht die Anmeldung mit *ProxyAddresses* (zusätzlich zu UPN) für cloudauthentifizierte Azure AD-Benutzer. Wie dies für Azure AD B2B-Szenarien Anwendung findet, erfahren Sie im Abschnitt [B2B](#b2b-guest-user-sign-in-with-an-email-address).
* Wenn ein Benutzer sich mit einer E-Mail-Adresse anmeldet, die nicht als UPN registriert ist, geben die Ansprüche `unique_name` und `preferred_username` (sofern vorhanden) im [ID-Token](../develop/id-tokens.md) den Wert der E-Mail-Adresse zurück, die nicht als UPN registriert ist.
* Das Feature unterstützt die verwaltete Authentifizierung mit Kennworthashsynchronisierung (Password Hash Sync, PHS) oder Passthrough-Authentifizierung (PTA).
* Es gibt zwei Optionen zum Konfigurieren des Features:
    * [HRD-Richtlinie (Home Realm Discovery, Startbereichsermittlung):](#enable-user-sign-in-with-an-email-address) Verwenden Sie diese Option, um das Feature für den gesamten Mandanten zu aktivieren. Hierzu sind globaler Administratorberechtigungen erforderlich.
    * [Richtlinie für gestaffelten Rollout:](#enable-staged-rollout-to-test-user-sign-in-with-an-email-address) Verwenden Sie diese Option, um das Feature mit spezifischen Azure AD-Gruppen zu testen. Hierzu sind globaler Administratorberechtigungen erforderlich.

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

In der aktuellen Vorschauversion gelten die folgenden Einschränkungen für die Verwendung von E-Mail-Adressen als alternative Anmelde-IDs:

* **Benutzeroberfläche:** Benutzern wird möglicherweise ihr UPN angezeigt, selbst wenn sie sich mit einer E-Mail-Adresse angemeldet haben, die nicht als UPN registriert ist. Beobachten lässt sich das folgende Beispielverhalten:
    * Der Benutzer wird aufgefordert, sich mit dem UPN anzumelden, wenn er mit `login_hint=<non-UPN email>` zur Azure AD-Anmeldung umgeleitet wird.
    * Wenn ein Benutzer sich mit einer E-Mail-Adresse anmeldet, die nicht als UPN registriert ist, und ein falsches Kennwort eingibt, wird der UPN auf der Seite *„Kennwort eingeben“* angezeigt.
    * Bei einigen Websites und Apps von Microsoft (z. B. Microsoft Office) zeigt das Steuerelement *Konto-Manager*, das in der Regel oben rechts angezeigt wird, möglicherweise den UPN des Benutzers anstelle der E-Mail-Adresse an, die nicht als UPN registriert ist und für die Anmeldung verwendet wurde.

* **Nicht unterstützte Flows:** Einige Flows sind derzeit nicht mit E-Mail-Adressen kompatibel, die nicht als UPN registriert wurden. Dazu gehören unter anderem folgende:
    * Identity Protection gleicht nicht als UPN registrierte E-Mail-Adressen nicht mit der Risikoerkennung *Kompromittierte Anmeldeinformationen* ab. Diese Risikoerkennung verwendet zum Abgleichen kompromittierter Anmeldeinformationen den UPN. Weitere Informationen finden Sie unter [Risikoerkennung und Problembehandlung in Azure AD Identity Protection][identity-protection].
    * Wenn ein Benutzer sich mit einer nicht als UPN registrierten E-Mail-Adresse anmeldet, kann dieser sein Kennwort nicht ändern. Die Self-Service-Kennwortzurücksetzung (SSPR) von Azure AD sollte erwartungsgemäß funktionieren. Während der SSPR wird dem Benutzer möglicherweise sein UPN angezeigt, wenn er seine Identität unter Verwendung einer Nicht-UPN-E-Mail-Adresse verifiziert.

* **Nicht unterstützte Szenarien:** Folgende Szenarien werden nicht unterstützt: Anmelden mit einer nicht als UPN registrierten E-Mail-Adresse bei den folgenden Diensten/Geräten:
    * [In Azure AD eingebundene Hybridgeräte](../devices/concept-azure-ad-join-hybrid.md)
    * [In Azure AD eingebundene Geräte](../devices/concept-azure-ad-join.md)
    * [Bei Azure AD registrierte Geräte](../devices/concept-azure-ad-register.md)
    * [Anmeldungen mit Kennwortanmeldeinformationen des Ressourcenbesitzers (Resource Owner Password Credentials, ROPC)](../develop/v2-oauth-ropc.md)
    * Anwendungen mit Legacyauthentifizierung wie POP3 und SMTP
    * Skype for Business
    * Microsoft Office unter macOS
    * Microsoft 365-Verwaltungsportal

* **Nicht unterstützte Apps:** Einige Anwendungen von Drittanbietern funktionieren möglicherweise nicht wie erwartet, wenn sie voraussetzen, dass die Ansprüche vom Typ `unique_name` oder `preferred_username` unveränderlich sind oder immer einem bestimmten Benutzerattribut entsprechen (beispielsweise UPN).

* **Protokollierung:** Änderungen an der Konfiguration des Features in der HRD-Richtlinie werden nicht explizit in den Überwachungsprotokollen angezeigt. Darüber hinaus ist das Feld für die Art des Anmeldebezeichners in den Anmeldeprotokollen möglicherweise nicht immer korrekt und sollte nicht verwendet werden, um zu bestimmen, ob das Feature für die Anmeldung verwendet wurde.

* **Richtlinie für gestaffelte Rollouts:** Die folgenden Einschränkungen gelten nur, wenn das Feature per Richtlinie für gestaffelte Rollouts aktiviert wird:
    * Das Feature funktioniert für Benutzer, die in anderen Richtlinien für gestaffelte Rollouts enthalten sind, nicht wie erwartet.
    * Von der Richtlinie für gestaffelte Rollouts werden maximal zehn Gruppen pro Feature unterstützt.
    * Von der Richtlinie für gestaffelte Rollouts werden keine geschachtelten Gruppen unterstützt.
    * Von der Richtlinie für gestaffelte Rollouts werden keine dynamischen Gruppen unterstützt.
    * Durch Kontaktobjekte innerhalb der Gruppe wird das Hinzufügen der Gruppe zu einer Richtlinie für gestaffelte Rollouts blockiert.

* **Doppelte Werte:** Innerhalb eines Mandanten kann ein UPN eines reinen Cloudbenutzers den gleichen Wert haben wie die Proxyadresse eines anderen Benutzers, die aus dem lokalen Verzeichnis synchronisiert wurde. In diesem Szenario kann der Benutzer, der nur die Cloud verwendet, sich nicht mit seinem UPN anmelden, wenn das Feature aktiviert ist. Weitere Informationen zu diesem Problem finden Sie im Abschnitt [Problembehandlung](#troubleshoot).

## <a name="overview-of-alternate-login-id-options"></a>Übersicht über alternative Optionen für die Anmelde-ID
Für die Anmeldung bei Azure AD geben Benutzer einen Wert ein, der ihr Konto eindeutig identifiziert. Bisher konnten Sie nur den Azure AD-UPN als Anmeldebezeichner verwenden.

Für Organisationen, bei denen der lokale UPN der bevorzugten E-Mail-Adresse für Anmeldungen des Benutzers entspricht, war dieser Ansatz perfekt geeignet. In diesen Organisationen kann der Azure AD-UPN auf denselben Wert wie der lokale UPN festgelegt werden, und die Anmeldung erfolgt für Benutzer überall gleich.

### <a name="alternate-login-id-for-ad-fs"></a>Alternative Anmelde-ID für AD FS

Allerdings wird der lokale UPN in einigen Organisationen nicht als Anmeldebezeichner verwendet. In lokalen Umgebungen konfigurieren Sie die lokale AD DS-Instanz, um Anmeldungen mit einer alternativen Anmelde-ID zu ermöglichen. Das Festlegen des Azure AD-UPN auf denselben Wert wie den lokalen UPN ist keine Option, da Azure AD dann erfordern würde, dass Benutzer sich mit diesem Wert anmelden.

### <a name="alternate-login-id-in-azure-ad-connect"></a>Alternative Anmelde-ID in Azure AD Connect

Die typische Umgehung für dieses Problem besteht darin, den Azure AD-UPN auf die E-Mail-Adresse festzulegen, mit der sich der Benutzer anmelden möchte. Dieser Ansatz funktioniert, führt jedoch zu unterschiedlichen UPNs zwischen lokalen Azure AD-Instanzen und Azure AD selbst. Außerdem ist diese Konfiguration nicht mit allen Microsoft 365-Workloads kompatibel.

### <a name="email-as-an-alternate-login-id"></a>E-Mail-Adresse als alternative Anmelde-ID

Ein anderer Ansatz besteht darin, die UPNs für Azure AD und die lokalen UPNs zu synchronisieren, damit sie denselben Wert aufweisen, und Azure AD dann so zu konfigurieren, dass Benutzer sich mit einer verifizierten E-Mail-Adresse in Azure AD anmelden können. Dazu definieren Sie mindestens eine E-Mail-Adresse im *ProxyAddresses*-Attribut des Benutzers im lokalen Verzeichnis. Das *ProxyAddresses*-Attribut wird dann mithilfe von Azure AD Connect automatisch mit Azure AD synchronisiert.


| Option | BESCHREIBUNG |
|---|---|
| [Alternative Anmelde-ID für AD FS](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) | Aktivieren Sie die Anmeldung mit einem alternativen Attribut (z. B. E-Mail-Adressen) für AD FS-Benutzer. |
| [Alternative Anmelde-ID in Azure AD Connect](../hybrid/plan-connect-userprincipalname.md#alternate-login-id) | Synchronisieren Sie ein alternatives Attribut (z. B. E-Mail-Adressen) als Azure AD-UPN. |
| E-Mail-Adresse als alternative Anmelde-ID | Aktivieren Sie die Anmeldung mit *ProxyAddresses*-Attributen einer verifizierten Domäne für Azure AD-Benutzer. |

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synchronisieren von Anmelde-E-Mail-Adressen mit Azure AD

Die Authentifizierung herkömmlicher Active Directory Domain Services (AD DS) oder Active Directory-Verbunddienste (AD FS) erfolgt direkt in Ihrem Netzwerk und wird von Ihrer AD DS-Infrastruktur verarbeitet. Bei der Hybridauthentifizierung können sich Benutzer stattdessen direkt bei Azure AD anmelden.

Zur Unterstützung dieses Hybridauthentifizierungsansatzes synchronisieren Sie Ihre lokale AD DS-Umgebung mithilfe von [Azure AD Connect][azure-ad-connect] mit Azure AD und konfigurieren sie für die Verwendung von PHS oder PTA. Weitere Informationen finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure AD-Hybrididentitätslösung][hybrid-auth-methods].

Bei beiden Konfigurationsoptionen übermittelt der Benutzer seinen Benutzernamen und sein Kennwort an Azure AD, das die Anmeldeinformationen überprüft und ein Ticket ausstellt. Wenn sich Benutzer bei Azure AD anmelden, ist es nicht mehr erforderlich, dass Ihre Organisation eine AD FS-Infrastruktur hostet und verwaltet.

Eines der Benutzerattribute, das automatisch von Azure AD Connect synchronisiert wird, ist *ProxyAddresses*. Wenn Benutzer eine E-Mail-Adresse besitzen, die in der lokalen AD DS-Umgebung als Teil des *ProxyAddresses*-Attributs definiert ist, wird diese automatisch mit Azure AD synchronisiert. Diese E-Mail-Adresse kann dann direkt beim Azure AD-Anmeldevorgang als alternative Anmelde-ID verwendet werden.

> [!IMPORTANT]
> Nur E-Mail-Adressen in für den Mandanten verifizierten Domänen werden mit Azure AD synchronisiert. Jeder Azure AD-Mandant verfügt über mindestens eine verifizierte Domäne, deren Eigentümer er nachweislich ist, und die eindeutig an ihren Mandanten gebunden ist.
>
> Weitere Informationen finden Sie unter [Hinzufügen und Verifizieren eines benutzerdefinierten Domänennamens in Azure][verify-domain].

## <a name="b2b-guest-user-sign-in-with-an-email-address"></a>B2B-Gastbenutzeranmeldung mit einer E-Mail-Adresse

![Diagramm von „E-Mail-Adresse als alternative Anmelde-ID“ für die B2B-Gastbenutzeranmeldung.](media/howto-authentication-use-email-signin/email-alternate-login-id-b2b.png)

„E-Mail als alternative Anmelde-ID“ kann für [Azure AD B2B-Zusammenarbeit (Business-to-Business)](../external-identities/what-is-b2b.md) unter einem „Bring Your Own Sign-In Identifiers“-Modell verwendet werden. Wenn im Basismandanten E-Mail-Adressen als alternative Anmelde-IDs aktiviert sind, können Azure AD-Benutzer die Gastanmeldung mit einer Nicht-UPN-E-Mail-Adresse auf dem Endpunkt des Ressourcenmandanten durchführen. Es ist keine Aktion vom Ressourcenmandanten erforderlich, um diese Funktionalität zu aktivieren.

## <a name="enable-user-sign-in-with-an-email-address"></a>Aktivieren der Benutzeranmeldung mit einer E-Mail-Adresse

> [!NOTE]
> Diese Konfigurationsoption verwendet die HRD-Richtlinie. Weitere Informationen finden Sie unter [homeRealmDiscoveryPolicy-Ressourcentyp](/graph/api/resources/homeRealmDiscoveryPolicy?view=graph-rest-1.0&preserve-view=true).

Nachdem Benutzer mit angewendetem *ProxyAddresses*-Attribut mithilfe von Azure AD Connect mit Azure AD synchronisiert wurden, müssen Sie die Funktion aktivieren, mit der sich Benutzer per E-Mail-Adresse als alternativer Anmelde-ID für Ihren Mandanten anmelden können. Dieses Feature weist Azure AD-Anmeldeserver an, den Anmeldebezeichner nicht nur auf UPN-Werte zu überprüfen, sondern auch *ProxyAddresses*-Werte auf E-Mail-Adressen.

Während der Vorschauphase können Sie die Funktion zur Anmeldung per E-Mail-Adresse als alternativer Anmelde-ID derzeit nur mit PowerShell aktivieren. Sie benötigen die Berechtigungen als *globaler Administrator*, um die folgenden Schritte auszuführen:

1. Öffnen Sie eine PowerShell-Sitzung als Administrator, und installieren Sie dann das [AzureADPreview][Install-Module]-Modul mithilfe des Cmdlets *Install-Module*:

    ```powershell
    Install-Module AzureADPreview
    ```

    Wenn Sie dazu aufgefordert werden, wählen Sie **Y**, um NuGet zu installieren oder das Modul aus einem nicht vertrauenswürdigen Repository zu installieren.

1. Melden Sie sich mit dem Cmdlet [Connect-AzureAD][Connect-AzureAD] als *globaler Administrator* beim Azure AD-Mandanten an:

    ```powershell
    Connect-AzureAD
    ```

    Der Befehl gibt die Informationen zu Ihrem Konto, Ihrer Umgebung und Ihrer Mandanten-ID zurück.

1. Überprüfen Sie mithilfe des Cmdlets [Get-AzureADPolicy][Get-AzureADPolicy] wie folgt, ob *HomeRealmDiscoveryPolicy* bereits in Ihrem Mandanten vorhanden ist:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. Wenn derzeit keine Richtlinie konfiguriert ist, gibt der Befehl nichts zurück. Wenn eine Richtlinie zurückgegeben wird, können Sie diesen Schritt überspringen und mit dem nächsten Schritt fortfahren, um eine vorhandene Richtlinie zu aktualisieren.

    Um die Richtlinie *HomeRealmDiscoveryPolicy* dem Mandanten hinzuzufügen, verwenden Sie das Cmdlet [New-AzureADPolicy][New-AzureADPolicy], und legen Sie das *AlternateIdLogin*-Attribut auf *"Enabled": true* wie im folgenden Beispiel fest:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    Wenn die Richtlinie erfolgreich erstellt wurde, gibt der Befehl die Richtlinien-ID zurück, wie in der folgenden Beispielausgabe dargestellt:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Wenn bereits eine Richtlinie konfiguriert ist, überprüfen Sie, ob das  *AlternateIdLogin* -Attribut aktiviert ist, wie in der folgenden Beispielrichtlinienausgabe dargestellt:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Wenn die Richtlinie vorhanden ist, das *AlternateIdLogin*-Attribut jedoch nicht vorhanden oder nicht aktiviert ist, oder wenn andere Attribute für die Richtlinie vorhanden sind, die Sie beibehalten möchten, aktualisieren Sie die vorhandene Richtlinie mithilfe des Cmdlets [Set-AzureADPolicy][Set-AzureADPolicy].

    > [!IMPORTANT]
    > Wenn Sie die Richtlinie aktualisieren, stellen Sie sicher, dass Sie alle alten Einstellungen und das neue *AlternateIdLogin*-Attribut einschließen.

    Im folgenden Beispiel wird das  *AlternateIdLogin*-Attribut hinzugefügt und das möglicherweise bereits festgelegte  *AllowCloudPasswordValidation*-Attribut beibehalten:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    Vergewissern Sie sich, dass die aktualisierte Richtlinie ihre Änderungen aufweist und dass das *AlternateIdLogin*-Attribut jetzt aktiviert ist:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

Nachdem die Richtlinie angewendet wurde, kann es bis zu einer Stunde dauern, bis Benutzer sich mit Ihrer alternativen Anmelde-ID anmelden können.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Aktivieren von gestaffelten Rollouts zum Testen der Benutzeranmeldung mit einer E-Mail-Adresse  

> [!NOTE]
>Diese Konfigurationsoption verwendet die Richtlinie für gestaffelte Rollouts. Weitere Informationen finden Sie unter [featureRolloutPolicy-Ressourcentyp](/graph/api/resources/featurerolloutpolicy?preserve-view=true&view=graph-rest-1.0).

Die Richtlinie für gestaffelte Rollouts ermöglicht es Mandantenadministratoren, Features für spezifische Azure AD-Gruppen zu aktivieren. Es wird empfohlen, dass Mandantenadministratoren einen gestaffelten Rollout verwenden, um die Benutzeranmeldung mit einer E-Mail-Adresse zu testen. Wenn Administratoren bereit sind, dieses Feature für ihren gesamten Mandanten bereitzustellen, sollten sie die [HRD-Richtlinie](#enable-user-sign-in-with-an-email-address) verwenden.  


Sie benötigen die Berechtigungen als *globaler Administrator*, um die folgenden Schritte auszuführen:

1. Öffnen Sie eine PowerShell-Sitzung als Administrator, und installieren Sie dann das [AzureADPreview][Install-Module]-Modul mithilfe des Cmdlets *Install-Module*:

    ```powershell
    Install-Module AzureADPreview
    ```

    Wenn Sie dazu aufgefordert werden, wählen Sie **Y**, um NuGet zu installieren oder das Modul aus einem nicht vertrauenswürdigen Repository zu installieren.

1. Melden Sie sich mit dem Cmdlet [Connect-AzureAD][Connect-AzureAD] als *globaler Administrator* beim Azure AD-Mandanten an:

    ```powershell
    Connect-AzureAD
    ```

    Der Befehl gibt die Informationen zu Ihrem Konto, Ihrer Umgebung und Ihrer Mandanten-ID zurück.

1. Führen Sie die folgenden Cmdlets aus, um alle vorhandenen Richtlinien für gestaffelte Rollouts aufzulisten:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

1. Wenn für dieses Feature keine Richtlinien für gestaffelte Rollouts vorhanden sind, erstellen Sie eine neue Richtlinie für gestaffelte Rollouts, und notieren Sie sich die Richtlinien-ID:

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

1. Suchen Sie die directoryObject-ID für die Gruppe, die der Richtlinie für gestaffelte Rollouts hinzugefügt werden soll. Notieren Sie sich den Wert, der für den *Id*-Parameter zurückgegeben wird, da Sie ihn im nächsten Schritt benötigen.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

1. Fügen Sie die Gruppe wie im folgenden Beispiel der Richtlinie für gestaffelte Rollouts hinzu. Ersetzen Sie den Wert im Parameter *-Id* durch den Wert, der in Schritt 4 für die Richtlinien-ID zurückgegeben wurde, und den Wert im Parameter *-RefObjectId* durch die *Id*, die Sie sich in Schritt 5 notiert haben. Es kann bis zu einer Stunde dauern, bis Benutzer in der Gruppe sich mit ihrer E-Mail-Adresse als alternative Anmelde-ID bei Azure AD anmelden können.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
Für neu zur Gruppe hinzugefügte Mitglieder kann es bis zu 24 Stunden dauern, bis sie sich mit ihrer E-Mail-Adresse als alternative Anmelde-ID bei Azure AD anmelden können.

### <a name="removing-groups"></a>Entfernen von Gruppen

Führen Sie den folgenden Befehl aus, um eine Gruppe aus einer Richtlinie für gestaffelte Rollouts zu entfernen:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Entfernen von Richtlinien

Um eine Richtlinie für gestaffelte Rollouts zu entfernen, deaktivieren Sie die Richtlinie zunächst und entfernen sie dann aus dem System:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="test-user-sign-in-with-an-email-address"></a>Testen der Benutzeranmeldung mit einer E-Mail-Adresse

Rufen Sie [https://myprofile.microsoft.com][my-profile] auf, und melden Sie sich mit einer nicht als UPN registrierten E-Mail-Adresse (z. B. `balas@fabrikam.com` ) an, um zu testen, ob Benutzer sich mit einer E-Mail-Adresse anmelden können. Die Anmeldung sollte der Anmeldung mit einem UPN ähneln.

## <a name="troubleshoot"></a>Problembehandlung

Wenn Benutzer Probleme mit der Anmeldung mit ihrer E-Mail-Adresse haben, führen Sie die folgenden Schritte zur Problembehandlung durch:

1. Stellen Sie sicher, dass eine Stunde vergangen ist, seitdem die E-Mail-Adresse als alternative Anmelde-ID aktiviert wurde. Wenn der Benutzer erst kürzlich zu einer Gruppe für Richtlinien für gestaffelte Rollouts hinzugefügt wurde, müssen Sie sicherstellen, dass 24 Stunden seit dieser Hinzufügung zur Gruppe vergangen sind.
1. Vergewissern Sie sich bei Verwendung von HRD-Richtlinien, dass die Definitionseigenschaft *AlternateIdLogin* der Azure AD-Richtlinie *HomeRealmDiscoveryPolicy* auf *"Enabled": true* und die Eigenschaft *IsOrganizationDefault* auf *True* festgelegt sind:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```
    Vergewissern Sie sich bei Verwendung von Richtlinien für gestaffelte Rollouts, dass die Eigenschaft *IsEnabled* der Azure AD-Richtlinie *FeatureRolloutPolicy* auf *True* festgelegt ist:

    ```powershell
    Get-AzureADMSFeatureRolloutPolicy
    ```
1. Stellen Sie sicher, dass die E-Mail-Adresse des Benutzerkontos im *ProxyAddresses*-Attribut in Azure AD festgelegt ist.

### <a name="conflicting-values-between-cloud-only-and-synced-users"></a>Widersprüchliche Werte zwischen synchronisierten Benutzern und reinen Cloudbenutzern

In einem Mandanten kann der UPN eines reinen Cloudbenutzers denselben Wert wie die Proxyadresse eines anderen Benutzers aufweisen, die aus dem lokalen Verzeichnis synchronisiert wurde. In diesem Szenario kann der Benutzer, der nur die Cloud verwendet, sich nicht mit seinem UPN anmelden, wenn das Feature aktiviert ist. Im Folgenden werden die Schritte zum Ermitteln dieses Problems aufgeführt.

1. Öffnen Sie eine PowerShell-Sitzung als Administrator, und installieren Sie dann das [AzureADPreview][Install-Module]-Modul mithilfe des Cmdlets *Install-Module*:

    ```powershell
    Install-Module AzureADPreview
    ```

    Wenn Sie dazu aufgefordert werden, wählen Sie **Y**, um NuGet zu installieren oder das Modul aus einem nicht vertrauenswürdigen Repository zu installieren.

1. Melden Sie sich mit dem Cmdlet [Connect-AzureAD][Connect-AzureAD] als *globaler Administrator* beim Azure AD-Mandanten an:

    ```powershell
    Connect-AzureAD
    ```

1. Rufen Sie die betroffenen Benutzer ab.

    ```powershell
    # Get all users
    $allUsers = Get-AzureADUser -All $true
    
    # Get list of proxy addresses from all synced users
    $syncedProxyAddresses = $allUsers |
        Where-Object {$_.ImmutableId} |
        Select-Object -ExpandProperty ProxyAddresses |
        ForEach-Object {$_ -Replace "smtp:", ""}
    
    # Get list of user principal names from all cloud-only users
    $cloudOnlyUserPrincipalNames = $allUsers |
        Where-Object {!$_.ImmutableId} |
        Select-Object -ExpandProperty UserPrincipalName
    
    # Get intersection of two lists
    $duplicateValues = $syncedProxyAddresses |
        Where-Object {$cloudOnlyUserPrincipalNames -Contains $_}
    ``` 

1. So geben Sie die betroffenen Benutzer aus:

    ```powershell
    # Output affected synced users
    $allUsers |
        Where-Object {$_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    
    # Output affected cloud-only users
    $allUsers |
        Where-Object {!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    ```

1. So geben Sie die betroffenen Benutzer in einer CSV-Datei aus:

    ```powershell
    # Output affected users to CSV
    $allUsers |
        Where-Object {
            ($_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0) -Or
            (!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName)
        } |
        Select-Object ObjectId, DisplayName, UserPrincipalName, @{n="ProxyAddresses"; e={$_.ProxyAddresses -Join ','}}, @{n="IsSyncedUser"; e={$_.ImmutableId.Length -GT 0}}, UserType |
        Export-Csv -Path .\AffectedUsers.csv -NoTypeInformation
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Hybrididentität, z. B. mit Azure AD App Proxy oder Azure AD Domain Services, finden Sie unter [Azure AD Hybrididentität für den Zugriff und die Verwaltung lokaler Workloads][hybrid-overview].

Weitere Informationen zu Hybrididentitätsvorgängen finden Sie unter [Funktionsweise der Kennworthashsynchronisierung][phs-overview] und [Funktionsweise der Passthrough-Authentifizierung][pta-overview].

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
