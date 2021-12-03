---
title: Benutzerdefinierte E-Mail-Überprüfung mit SendGrid
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie SendGrid integrieren, um die Überprüfungs-E-Mail anzupassen, die an Ihre Kunden gesendet wird, wenn sie sich für die Verwendung Ihrer Azure AD B2C-fähigen Anwendungen registrieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/10/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7cebff64b67d5ec9f97700929d576ef8dbbc9bf0
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180114"
---
# <a name="custom-email-verification-with-sendgrid"></a>Benutzerdefinierte E-Mail-Überprüfung mit SendGrid

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Verwenden Sie benutzerdefinierte E-Mails in Azure Active Directory B2C (Azure AD B2C), um angepasste E-Mails an Benutzer zu senden, die sich für die Verwendung Ihrer Anwendungen registrieren. Mithilfe des E-Mail-Drittanbieters SendGrid können Sie eigene E-Mail-Vorlagen sowie *Absenderadressen* und Betreffzeilentexte verwenden. Darüber hinaus werden Lokalisierung und benutzerdefinierte Einstellungen für Einmalkennwörter (One-Time Passwords, OTPs) unterstützt.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Für die benutzerdefinierte E-Mail-Überprüfung ist die Verwendung eines E-Mail-Drittanbieters wie [SendGrid](https://sendgrid.com), [Mailjet](https://Mailjet.com) oder [SparkPost](https://sparkpost.com), einer benutzerdefinierten REST-API oder eines HTTP-basierten E-Mail-Anbieters (einschließlich Ihres eigenen Anbieters) erforderlich. In diesem Artikel wird die Einrichtung einer Lösung beschrieben, bei der SendGrid verwendet wird.

## <a name="create-a-sendgrid-account"></a>Erstellen eines SendGrid-Kontos

Beginnen Sie mit dem Einrichten eines SendGrid-Kontos, wenn Sie noch über keines verfügen. (Azure-Kunden können monatlich 25.000 kostenlose E-Mails freischalten.) Anweisungen zum Einrichten finden Sie im Abschnitt [Erstellen eines SendGrid-Kontos](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account) unter [Senden von E-Mails in Azure mit SendGrid](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-twilio-sendgrid-accountcreate-a-twilio-sendgrid-account).

Führen Sie die Schritte zum [Erstellen eines SendGrid-API-Schlüssels](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key) aus. Notieren Sie sich den API-Schlüssel zur Verwendung in einem späteren Schritt.

> [!IMPORTANT]
> Mit SendGrid können Kunden E-Mails von freigegebenen und [dedizierten IP-Adressen](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/) senden. Bei Verwendung dedizierter IP-Adressen müssen die IP-Adressen zunächst „aufgewärmt“ werden, um Ihre eigene Reputation aufzubauen. Weitere Informationen finden Sie unter [Aufwärmen einer IP-Adresse](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/).

## <a name="create-azure-ad-b2c-policy-key"></a>Erstellen des Azure AD B2C-Richtlinienschlüssels

Speichern Sie als Nächstes den SendGrid-API-Schlüssel in einem Azure AD B2C-Richtlinienschlüssel, auf den Ihre Richtlinien verweisen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Wählen Sie unter **Optionen** die Option **Manuell** aus.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `SendGridSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Secret** den SendGrid-API-Schlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.

## <a name="create-sendgrid-template"></a>Erstellen der SendGrid-Vorlage

Wenn Sie ein SendGrid-Konto erstellt und den SendGrid-API-Schlüssel in einem Azure AD B2C-Richtlinienschlüssel gespeichert haben, erstellen Sie eine [dynamische SendGrid-Transaktionsvorlage](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Öffnen Sie auf der SendGrid-Website die Seite [Transaktionsvorlagen](https://sendgrid.com/dynamic_templates), und wählen Sie **Dynamische Vorlage erstellen** aus.
1. Geben Sie einen eindeutigen Vorlagennamen wie `Verification email` ein, und wählen Sie dann **Erstellen** aus.
1. Um mit der Bearbeitung der neuen Vorlage zu beginnen, wählen Sie die Vorlage aus, d. h. `Verification email`, und wählen Sie dann **Version hinzufügen** aus.
1. Wählen Sie **Leere Vorlage** und dann **Code-Editor** aus.
1. Fügen Sie im HTML-Editor die folgende HTML-Vorlage ein, oder verwenden Sie eine eigene Vorlage. Die Parameter `{{otp}}` und `{{email}}` werden dynamisch durch das Einmalkennwort und die E-Mail-Adresse des Benutzers ersetzt.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">

                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Erweitern Sie **Einstellungen** auf der linken Seite, und geben Sie unter **Versionsname** eine Vorlagenversion ein. 
1. Geben Sie unter **Betreff** den Eintrag `{{subject}}` ein.
1. Klicken Sie am oberen Rand der Seite auf **Speichern**.
1. Wählen Sie den Zurück-Pfeil aus, um zur Seite **Transactional Templates** (Transaktionsvorlagen) zurückzukehren.
1. Notieren Sie sich die **ID** der erstellten Vorlage zur Verwendung in einem späteren Schritt. Beispiel: `d-989077fbba9746e89f3f6411f596fb96`. Diese ID geben Sie an, wenn Sie [die Anspruchstransformation hinzufügen](#add-the-claims-transformation).


[!INCLUDE [active-directory-b2c-important-for-custom-email-provider](../../includes/active-directory-b2c-important-for-custom-email-provider.md)]

## <a name="add-azure-ad-b2c-claim-types"></a>Hinzufügen von Azure AD B2C-Anspruchstypen

Fügen Sie in Ihrer Richtlinie im `<ClaimsSchema>`-Element in `<BuildingBlocks>` die folgenden Anspruchstypen hinzu.

Diese Anspruchstypen sind erforderlich, um die E-Mail-Adresse mit einem OTP-Code (One-Time Password, Einmalkennwort) zu generieren und zu überprüfen.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>SendGrid request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>Hinzufügen der Anspruchstransformation

Als Nächstes benötigen Sie eine Anspruchstransformation zum Ausgeben eines JSON-Zeichenfolgenanspruchs, der als Text der an SendGrid gesendeten Anforderung verwendet wird.

Die Struktur des JSON-Objekts wird durch die IDs in der Punktnotation der InputParameters und der TransformationClaimTypes der InputClaims definiert. Zahlen in Punktnotation implizieren Arrays. Die Werte stammen aus den Werten der InputClaims und den Value-Eigenschaften der InputParameters. Weitere Informationen zu JSON-Anspruchstransformationen finden Sie unter [Transformationen von JSON-Ansprüchen](json-transformations.md).

Fügen Sie im `<ClaimsTransformations>`-Element in `<BuildingBlocks>` die folgenden Anspruchstransformation hinzu. Nehmen Sie die folgenden Aktualisierungen an der Anspruchstranformations-XML vor:

* Aktualisieren Sie den InputParameter-Wert `template_id` mit der ID der SendGrid-Transaktionsvorlage, die Sie zuvor unter [Erstellen der SendGrid-Vorlage](#create-sendgrid-template) erstellt haben.
* Aktualisieren Sie den Adresswert `from.email`. Verwenden Sie eine gültige E-Mail-Adresse, um zu verhindern, dass die Überprüfungs-E-Mail als Spam markiert wird.
   > [!NOTE]
   > Diese E-Mail-Adresse muss in SendGrid unter Absenderauthentifizierung entweder mit Domänenauthentifizierung oder Single Sender Authentication überprüft werden.
* Aktualisieren Sie den Wert des Betreffzeilen-Eingabeparameters `personalizations.0.dynamic_template_data.subject` mit einer für Ihre Organisation geeigneten Betreffzeile.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your SendGrid template. -->
        <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>Hinzufügen der DataUri-Inhaltsdefinition

Fügen Sie unterhalb der Anspruchstransformationen in `<BuildingBlocks>` die folgende [ContentDefinition](contentdefinitions.md) hinzu, um auf den Daten-URI der Version 2.1.2 zu verweisen:

```xml
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>Erstellen eines DisplayControl-Elements

Ein Anzeigesteuerelement zur Überprüfung wird verwendet, um die E-Mail-Adresse mit einem an den Benutzer gesendeten Überprüfungscode zu überprüfen.

Das folgende Beispielanzeigesteuerelement ist für Folgendes konfiguriert:

1. Erfassen des `email`-Adressanspruchstyps vom Benutzer
1. Warten, dass der Benutzer den `verificationCode`-Anspruchstyp mit dem an den Benutzer gesendeten Code angibt
1. Zurückgeben von `email` an das selbstbestätigte technische Profil, das einen Verweis auf das Anzeigesteuerelement enthält
1. Verwenden der Aktion `SendCode` zum Generieren eines Einmalkennwortcodes und Senden einer E-Mail mit dem Einmalkennwortcode an den Benutzer

![Senden des Überprüfungscodes in einer E-Mail](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

Fügen Sie der Richtlinie unter „content definitions“ in `<BuildingBlocks>` das folgende [DisplayControl](display-controls.md)-Element vom Typ [VerificationControl](display-control-verification.md) hinzu.

```xml
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>Hinzufügen technischer Profile für Einmalkennwörter

Mit dem technischen Profil `GenerateOtp` wird ein Code für die E-Mail-Adresse generiert. Mit dem technischen Profil `VerifyOtp` wird der der E-Mail-Adresse zugeordnete Code überprüft. Sie können die Konfiguration des Formats und den Ablauf des Einmalkennworts ändern. Weitere Informationen zu technischen Profilen für Einmalkennwörter finden Sie unter [Definieren eines technischen Einmalkennwortprofils](one-time-password-technical-profile.md).

> [!NOTE]
> Durch das Web.TPEngine.Providers.OneTimePasswordProtocolProvider-Protokoll generierte OTP-Codes sind an die Browsersitzung gebunden. Das bedeutet, dass ein Benutzer eindeutige OTP-Codes in verschiedenen Browsersitzungen generieren kann, die jeweils für die entsprechenden Sitzungen gültig sind. Ein durch den integrierten Benutzerflow generierter OTP-Code ist dagegen unabhängig von der Browsersitzung. Wenn ein Benutzer also einen neuen OTP-Code in einer neuen Browsersitzung generiert, ersetzt dieser den vorherigen OTP-Code.

Fügen Sie dem `<ClaimsProviders>`-Element die folgenden technischen Profile hinzu.

```xml
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>Hinzufügen eines technischen REST-API-Profils

Mit diesem technischen REST-API-Profil wird der E-Mail-Inhalt (im SendGrid-Format) generiert. Weitere Informationen zu technischen RESTful-Profilen finden Sie unter [Definieren eines technischen RESTful-Profils](restful-technical-profile.md).

Fügen Sie wie bei den technischen Profilen für Einmalkennwörter dem `<ClaimsProviders>`-Element die folgenden technischen Profile hinzu.

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Erstellen eines Verweises auf das DisplayControl-Element

Fügen Sie im letzten Schritt einen Verweis auf das erstellte DisplayControl-Element hinzu. Überschreiben Sie Ihre bestehenden `LocalAccountSignUpWithLogonEmail` und `LocalAccountDiscoveryUsingEmailAddress` selbstbestätigten technischen Profile, die in der Basisrichtlinie konfiguriert sind, mit dem folgenden XML-Schnipsel. Wenn Sie eine frühere Version der Azure AD B2C-Richtlinie verwendet haben, verwenden diese technischen Profile `DisplayClaims` mit einem Verweis auf die `DisplayControl`.

Weitere Informationen finden Sie unter [Selbstbestätigtes technisches Profil](restful-technical-profile.md) und [DisplayControl](display-controls.md).

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Optional] Lokalisieren Ihrer E-Mail

Zum Lokalisieren der E-Mail müssen Sie lokalisierte Zeichenfolgen an SendGrid oder Ihren E-Mail-Anbieter senden. Sie können beispielsweise den E-Mail-Betreff, den Nachrichtentext, Ihre Codemeldung oder die Signatur der E-Mail lokalisieren. Hierfür können Sie die Anspruchstransformation [GetLocalizedStringsTransformation](string-transformations.md) nutzen, um lokalisierte Zeichenfolgen in Anspruchstypen zu kopieren. Die Anspruchstransformation `GenerateEmailRequestBody`, mit der die JSON-Nutzlast generiert wird, verwendet Eingabeansprüche, die die lokalisierten Zeichenfolgen enthalten.

1. Definieren Sie in Ihrer Richtlinie die folgenden Zeichenfolgenansprüche: „subject“, „message“, „codeIntro“ und „signature“.
1. Definieren Sie die Anspruchstransformation [GetLocalizedStringsTransformation](string-transformations.md), um die lokalisierten Zeichenfolgenwerte in den Ansprüchen aus Schritt 1 zu ersetzen.
1. Ändern Sie die Anspruchstransformation `GenerateEmailRequestBody`, um Eingabeansprüche mit dem folgenden XML-Codeausschnitt zu verwenden.
1. Aktualisieren Sie Ihre SendGrid-Vorlage so, dass anstelle aller Zeichenfolgen, die von Azure AD B2C lokalisiert werden, dynamische Parameter verwendet werden.

    ```xml
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Fügen Sie das folgende [Localization](localization.md)-Element hinzu.

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. Fügen Sie Verweise auf die „LocalizedResources“-Elemente hinzu, indem Sie das [ContentDefinitions](contentdefinitions.md)-Element aktualisieren.

    ```xml
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Fügen Sie schließlich den technischen Profilen `LocalAccountSignUpWithLogonEmail` und `LocalAccountDiscoveryUsingEmailAddress` die folgende Eingabeanspruchstransformation hinzu.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="optional-localize-the-ui"></a>[Optional] Lokalisieren der Benutzeroberfläche

Mithilfe des Localization-Elements können Sie mehrere Gebietsschemas oder Sprachen in der Richtlinie für die User Journeys unterstützen. Die Lokalisierungsunterstützung in Richtlinien ermöglicht es Ihnen, sprachspezifische Zeichenfolgen sowohl für [Anzeigesteuerelemente zur Überprüfung von Benutzeroberflächenelementen](localization-string-ids.md#verification-display-control-user-interface-elements) als auch für [Fehlermeldungen für Einmalkennwort](localization-string-ids.md#one-time-password-error-messages) bereitzustellen. Fügen Sie Ihren „LocalizedResources“ folgendes „LocalizedString“ hinzu.

```xml
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
    <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxNumberOfCodeGenerated">You have exceeded the number of code generation attempts allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```


## <a name="next-steps"></a>Nächste Schritte

- Ein Beispiel für [Benutzerdefinierte E-Mail-Überprüfung - DisplayControls benutzerdefinierte Richtlinie](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol/policy/SendGrid) finden Sie auf GitHub.
- Informationen zur Verwendung einer benutzerdefinierten REST-API oder eines HTTP-basierten SMTP-E-Mail-Anbieters finden Sie unter [Definieren eines technischen RESTful-Profils in einer benutzerdefinierten Azure AD B2C-Richtlinie](restful-technical-profile.md).

::: zone-end
