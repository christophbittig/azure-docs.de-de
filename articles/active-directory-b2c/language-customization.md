---
title: Sprachanpassung in Azure Active Directory B2C
description: Erfahren Sie mehr über das Anpassen der Oberfläche für die Sprache in Ihren Benutzerflows in Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5464e14cddc6319ad82eaaa054e39ba661312f97
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007066"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Sprachanpassung in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Mit der Sprachanpassung in Azure Active Directory B2C (Azure AD B2C) kann Ihr Benutzerflow verschiedene Sprachen abdecken, um Ihre Kundenanforderungen zu erfüllen. Microsoft stellt Übersetzungen für [36 Sprachen](#supported-languages) bereit. Sie können aber auch eigene Übersetzungen für beliebige Sprachen bereitstellen. Auch wenn Ihre Benutzeroberfläche nur für eine einzelne Sprache bestimmt ist, können Sie beliebigen Text auf den Seiten anpassen.

## <a name="how-language-customization-works"></a>Funktionsweise der Sprachanpassung

Mit der Sprachanpassung können Sie die Sprachen auswählen, in denen Ihr Benutzerflow verfügbar ist. Nach Aktivierung des Features können Sie über Ihre Anwendung den Abfragezeichenfolgen-Parameter `ui_locales` angeben. Wenn Sie Azure AD B2C aufrufen, wird Ihre Seite in das von Ihnen angegebene Gebietsschema übersetzt. Mit dieser Art von Konfiguration haben Sie die vollständige Kontrolle über die Sprachen Ihres Benutzerflows, und die Spracheinstellungen des Kundenbrowsers können ignoriert werden.

Möglicherweise müssen Sie aber gar nicht so genau steuern, welche Sprachen Ihren Kunden angezeigt werden. Wenn Sie den Parameter `ui_locales` nicht angeben, wird die Benutzeroberfläche des Kunden durch die Einstellungen des Browsers vorgegeben. Sie können weiterhin steuern, in welche Sprachen Ihr Benutzerflow übersetzt wird, indem Sie ihn als unterstützte Sprache hinzufügen. Wenn der Browser eines Kunden mit einer Sprache konfiguriert ist, die Sie nicht unterstützen möchten, wird stattdessen die Sprache angezeigt, die Sie als Standardeinstellung für die unterstützte Kultur ausgewählt haben.

* **In „ui-locales“ angegebene Sprache**: Nach Aktivierung der Sprachanpassung wird Ihr Benutzerflow in die hier angegebene Sprache übersetzt.
* **Vom Browser angeforderte Sprache**: Ohne Angabe des Parameters `ui_locales` wird Ihr Benutzerflow in die vom Browser angeforderte Sprache übersetzt, *sofern diese Sprache unterstützt wird*.
* **Standardsprache der Richtlinie**: Wenn vom Browser keine oder eine nicht unterstützte Sprache angegeben wird, wird der Benutzerflow in die Standardsprache der Richtlinie übersetzt.

> [!NOTE]
> Falls Sie benutzerdefinierte Benutzerattribute verwenden, müssen Sie Ihre eigenen Übersetzungen angeben. Weitere Informationen finden Sie unter [Anpassen von Zeichenfolgen](#customize-your-strings).

Sehen Sie sich dieses Video an, um zu erfahren, wie Sie Sprache mit Azure AD B2C lokalisieren oder anpassen.

>[!Video https://www.youtube.com/embed/yqrX5_tA7Ms]

::: zone pivot="b2c-custom-policy"

Für die Lokalisierung sind drei Schritte erforderlich: 

1. Einrichten der expliziten Liste der unterstützten Sprachen
1. Angeben der sprachspezifischen Zeichenfolgen und Sammlungen
1. Bearbeiten der [Inhaltsdefinition](contentdefinitions.md) für die Seite 

::: zone-end 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Unterstützen von angeforderten Sprachen für „ui_locales“

Bei Richtlinien, die vor der allgemeinen Verfügbarkeit der Sprachanpassung erstellt wurden, muss das Feature zunächst aktiviert werden. Bei später erstellten Richtlinien und Benutzerflows ist die Sprachanpassung standardmäßig aktiviert.

Wenn Sie die Sprachanpassung für einen Benutzerflow aktivieren, können Sie die Sprache des Benutzerflows durch Hinzufügen des Parameters `ui_locales` steuern.

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, den Sie für Übersetzungen aktivieren möchten.
1. Wählen Sie **Sprachen** aus.
1. Klicken Sie auf **Sprachanpassung aktivieren**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Auswählen der im Benutzerflow zu aktivierenden Sprachen

Aktivieren Sie einen Satz an Sprachen, in die Ihr Benutzerflow übersetzt werden soll, wenn der Browser die Anforderung ohne den `ui_locales`-Parameter stellt.

1. Stellen Sie anhand der oben genannten Anweisungen sicher, dass die Sprachanpassung für Ihren Benutzerflow aktiviert ist.
1. Wählen Sie auf der Seite **Sprachen** für den Benutzerflow eine Sprache aus, die Sie unterstützen möchten.
1. Legen Sie im Bereich „Eigenschaften“ die Option **Aktiviert** auf **Ja** fest.
1. Klicken Sie oben im Bereich „Eigenschaften“ auf **Speichern**.

>[!NOTE]
>Ohne Angabe des Parameters `ui_locales` wird die Seite nur in die Browsersprache des Kunden übersetzt, wenn sie aktiviert ist.
>

## <a name="customize-your-strings"></a>Anpassen von Zeichenfolgen

Mit der Sprachanpassung können Sie alle Zeichenfolgen Ihres Benutzerflows anpassen.

1. Stellen Sie anhand der oben genannten Anweisungen sicher, dass die Sprachanpassung für Ihren Benutzerflow aktiviert ist.
1. Wählen Sie auf der Seite **Sprachen** für den Benutzerflow die Sprache aus, die Sie anpassen möchten.
1. Wählen Sie unter **Ressourcendateien auf Seitenebene** die Seite aus, die Sie bearbeiten möchten.
1. Klicken Sie auf **Standardeinstellungen herunterladen** (oder **Außerkraftsetzungen herunterladen**, wenn Sie diese Sprache zuvor bearbeitet haben).

Bei diesen Schritten erhalten Sie eine JSON-Datei, die Sie nutzen können, um mit dem Bearbeiten Ihrer Zeichenfolgen zu beginnen.

### <a name="change-any-string-on-the-page"></a>Ändern einer beliebigen Zeichenfolge auf der Seite

1. Öffnen Sie die JSON-Datei, die Sie im Rahmen der obigen Anleitung heruntergeladen haben, in einem JSON-Editor.
1. Suchen Sie nach dem Element, das Sie ändern möchten. Sie können nach der `StringId` der gewünschten Zeichenfolge oder nach dem Attribut `Value` suchen, das Sie ändern möchten.
1. Aktualisieren Sie das `Value`-Attribut mit den Daten, die angezeigt werden sollen.
1. Ändern Sie `Override` für jede Zeichenfolge, die Sie ändern möchten, in `true`.
1. Speichern Sie die Datei, und laden Sie Ihre Änderungen hoch. (Das Steuerelement zum Hochladen befindet sich dort, wo Sie auch die JSON-Datei heruntergeladen haben.)

> [!IMPORTANT]
> Stellen Sie beim Überschreiben einer Zeichenfolge sicher, dass Sie den Wert für `Override` auf `true` festlegen. Wenn der Wert nicht geändert wird, wird der Eintrag ignoriert.

### <a name="change-extension-attributes"></a>Ändern von Erweiterungsattributen

Wenn Sie die Zeichenfolge für ein benutzerdefiniertes Benutzerattribut ändern oder dem JSON-Code eine Zeichenfolge hinzufügen möchten, verwenden Sie das folgende Format:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Ersetzen Sie `<ExtensionAttribute>` durch den Namen Ihres benutzerdefinierten Benutzerattributs.

Ersetzen Sie `<ExtensionAttributeValue>` durch die neue Zeichenfolge, die angezeigt werden soll.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Angeben einer Liste mit Werten mithilfe von „LocalizedCollections“

Wenn Sie eine feste Liste mit Werten für Antworten bereitstellen möchten, müssen Sie ein Attribut vom Typ `LocalizedCollections` erstellen. Bei `LocalizedCollections` handelt es sich um ein Array mit Paaren aus `Name` und `Value`. Die Reihenfolge der Elemente ist dieselbe, in der sie angezeigt werden. Verwenden Sie zum Hinzufügen von `LocalizedCollections` das folgende Format:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` ist das Benutzerattribut, für das dieses Attribut vom Typ `LocalizedCollections` eine Antwort darstellt.
* `Name` ist der Wert, der dem Benutzer angezeigt wird.
* `Value` wird im Anspruch zurückgegeben, wenn diese Option ausgewählt wird.

### <a name="upload-your-changes"></a>Hochladen von Änderungen

1. Kehren Sie nach dem Ändern der JSON-Datei wieder zu Ihrem B2C-Mandanten zurück.
1. Wählen Sie **Benutzerflows** aus, und klicken Sie auf den Benutzerflow, den Sie für Übersetzungen aktivieren möchten.
1. Wählen Sie **Sprachen** aus.
1. Wählen Sie die gewünschte Zielsprache für die Übersetzung aus.
1. Wählen Sie die Seite aus, auf der Sie Übersetzungen bereitstellen möchten.
1. Klicken Sie auf das Ordnersymbol, und wählen Sie die hochzuladende JSON-Datei aus.

Die Änderungen werden automatisch in Ihrem Benutzerflow gespeichert.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Anpassen der Benutzeroberfläche der Seite mithilfe der Sprachanpassung

Es gibt zwei Möglichkeiten zum Lokalisieren Ihrer [HTML-Inhalte](customize-ui-with-html.md). Eine davon ist die Aktivierung der [Sprachanpassung](language-customization.md). Wenn Sie dieses Feature aktivieren, kann Azure AD B2C den Open ID Connect-Parameter `ui-locales` an Ihren Endpunkt weiterleiten. Ihr Inhaltsserver kann diesen Parameter verwenden, um benutzerdefinierte HTML-Seiten bereitzustellen, die sprachspezifisch sind.

Alternativ können Sie auf der Grundlage des verwendeten Gebietsschemas Inhalte aus unterschiedlichen Quellen abrufen. In Ihrem CORS-fähigen Endpunkt können Sie eine Ordnerstruktur zum Hosten von Inhalten für bestimmte Sprachen einrichten. Wenn Sie den Platzhalterwert `{Culture:RFC5646}` verwenden, wird der passende Inhalt abgerufen. Ein Beispiel: Angenommen, Ihr benutzerdefinierter Seiten-URI sieht wie folgt aus:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Sie können die Seite in `fr` laden. HTML- und CSS-Inhalte der Seite werden dann aus der folgenden Quelle abgerufen:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Hinzufügen benutzerdefinierter Sprachen

Sie können auch Sprachen hinzufügen, für die Microsoft aktuell keine Übersetzungen bereitstellt. Sie müssen in diesem Fall die Übersetzungen für alle Zeichenfolgen im Benutzerflow bereitstellen. Sprach- und Gebietsschema-Codes sind auf die begrenzt, die im ISO-Standard 639-1 festgelegt sind. Das Gebietsschema-Codeformat sollte „ISO_639-1_Code“-„Ländercode“ lauten, z. B. `en-GB`. Weitere Informationen finden Sie unter [Gebietsschema-ID-Formate](/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a).

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
2. Klicken Sie auf den Benutzerflow, für den Sie benutzerdefinierte Sprachen hinzufügen möchten, und klicken Sie dann auf **Sprachen**.
3. Wählen Sie im oberen Bereich der Seite **Benutzerdefinierte Sprache hinzuzufügen** aus.
4. Geben Sie im Kontextbereich mithilfe eines gültigen Gebietsschemacodes die Sprache an, für die Sie Übersetzungen bereitstellen.
5. Für jede Seite können Sie eine Reihe von Außerkraftsetzungen für Englisch herunterladen und Übersetzungen erstellen.
6. Wenn Sie mit der Bearbeitung der JSON-Dateien fertig sind, können Sie sie für die einzelnen Seiten hochladen.
7. Nachdem Sie auf **Aktivieren** geklickt haben, kann Ihr Benutzerflow diese Sprache für Ihre Benutzer anzeigen.
8. Speichern Sie die Sprache.

>[!IMPORTANT]
>Sie müssen benutzerdefinierte Sprachen aktivieren oder Außerkraftsetzungen dafür hochladen, bevor Sie speichern können.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Einrichten der Liste der unterstützten Sprachen

Öffnen Sie die Erweiterungendatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie das `Localization`-Element mit den unterstützten Sprachen hinzu: Englisch (Standard) und Spanisch.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Angeben sprachspezifischer Bezeichnungen

Die [LocalizedResources](localization.md#localizedresources) des `Localization`-Elements enthalten die Liste der lokalisierten Zeichenfolgen. Das lokalisierte Ressourcenelement verfügt über einen Bezeichner, der zur eindeutigen Identifizierung von lokalisierten Ressourcen verwendet wird. Dieser Bezeichner wird später im [ContentDefinition](contentdefinitions.md)-Element verwendet.

Sie konfigurieren die lokalisierten Ressourcenelemente für die Inhaltsdefinition und jede Sprache, die Sie unterstützen möchten. Zum Anpassen der einheitlichen Registrierungs- oder Anmeldeseiten für Englisch und Spanisch fügen Sie die folgenden `LocalizedResources`-Elemente hinter dem Ende des `</SupportedLanguages>`-Elements hinzu.

> [!NOTE]
> Im folgenden Beispiel haben wir das Doppelkreuzsymbol `#` am Anfang aller Zeilen hinzugefügt, sodass Sie die lokalisierten Bezeichnungen einfacher auf dem Bildschirm finden können.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Bearbeiten der Inhaltsdefinition mit der Lokalisierung

Fügen Sie den gesamten Inhalt des ContentDefinitions-Elements ein, das Sie als untergeordnetes Element des BuildingBlocks-Elements kopiert haben.

Im folgenden Beispiel werden der Registrierungs- oder Anmeldeseite sowie der Registrierungsseite für das lokale Konto benutzerdefinierte Zeichenfolgen für Englisch („en“) und Spanisch („es“) hinzugefügt. Die **LocalizedResourcesReferenceId** für jede **LocalizedResourcesReference** ist mit ihrem Gebietsschema identisch, es kann aber jede beliebige Zeichenfolge als Bezeichner verwendet werden. Sie verweisen für jede Sprach- und Seitenkombination auf die entsprechenden **LocalizedResources**, die Sie zuvor erstellt haben.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="upload-and-test-your-updated-custom-policy"></a>Hochladen und Testen Ihrer aktualisierten benutzerdefinierten Richtlinie

### <a name="upload-the-custom-policy"></a>Hochladen der benutzerdefinierten Richtlinie

1. Speichern Sie die Erweiterungsdatei.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus.
1. Laden Sie die zuvor geänderte Erweiterungsdatei hoch.

### <a name="test-the-custom-policy-by-using-run-now"></a>Testen der benutzerdefinierten Richtlinie mit **Jetzt ausführen**

1. Wählen Sie die hochgeladene Richtlinie aus, und wählen Sie dann **Jetzt ausführen** aus.
1. Die lokalisierte Registrierungs- oder Anmeldeseite sollte angezeigt werden.
1. Klicken Sie auf den Registrierungslink, um die lokalisierte Registrierungsseite anzuzeigen.
1. Stellen Sie die Standardsprache Ihres Browsers auf Spanisch um. Sie können auch der Autorisierungsanforderung den Parameter `ui_locales` in der Abfragezeichenfolge hinzufügen. Beispiel: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Zusätzliche Informationen

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Bezeichnungen der Seite für die Benutzeroberflächenanpassung als Außerkraftsetzungen

Wenn Sie die Sprachanpassung aktivieren, werden Ihre bisherigen Bearbeitungen für Bezeichnungen, die Sie auf der Seite für die Benutzeroberflächenanpassung vorgenommen haben, in einer JSON-Datei für Englisch (en) gespeichert. Sie können Ihre Bezeichnungen und anderen Zeichenfolgen weiterhin ändern, indem Sie Sprachressourcen in die Sprachanpassung hochladen.

::: zone-end

### <a name="up-to-date-translations"></a>Aktuelle Übersetzungen

Microsoft ist bemüht, Ihnen möglichst aktuelle Übersetzungen zur Verfügung zu stellen. Die Übersetzungen werden kontinuierlich optimiert und auf ihre Konformität überprüft. Microsoft ermittelt Fehler und Änderungen für die globale Terminologie und nimmt geeignete Aktualisierungen in Ihrem Benutzerflow vor.

### <a name="support-for-right-to-left-languages"></a>Unterstützung für von rechts nach links geschriebene Sprachen

Von rechts nach links geschriebene Sprachen werden von Microsoft derzeit nicht unterstützt. Dies erreichen Sie, indem Sie benutzerdefinierte Gebietsschemas verwenden und mithilfe von CSS ändern, wie Zeichenfolgen angezeigt werden. Sollten Sie dieses Feature benötigen, stimmen Sie im [Azure-Feedback](https://feedback.azure.com/d365community/idea/10a7e89c-c325-ec11-b6e6-000d3a4f0789) dafür ab.

### <a name="social-identity-provider-translations"></a>Übersetzungen für den Fall „Soziales Netzwerk als Identitätsanbieter“

Für Anmeldungen per sozialem Netzwerk stellt Microsoft den OIDC-Parameter `ui_locales` bereit. Einige soziale Netzwerke, die als Identitätsanbieter fungieren, erkennen diesen jedoch nicht an. Dazu zählen auch Facebook und Google.

### <a name="browser-behavior"></a>Browserverhalten

Von Chrome und Firefox wird jeweils die eigene festgelegte Sprache angefordert. Wenn es sich dabei um eine unterstützte Sprache handelt, wird sie vor der Standardsprache angezeigt. Für Microsoft Edge wird derzeit keine Sprache angefordert, sondern es wird gleich die Standardsprache verwendet.

## <a name="supported-languages"></a>Unterstützte Sprachen

Azure AD B2C bietet Unterstützung für die folgenden Sprachen. Sprachen für Benutzerflows werden von Azure AD B2C bereitgestellt. Die Sprachen für Benachrichtigungen von Multi-Factor Authentication (MFA) werden von [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md) bereitgestellt.

| Sprache              | Sprachcode | Benutzerabläufe         | MFA-Benachrichtigungen  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabisch                | ar            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Bulgarisch             | bg            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Bengalisch                | bn            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Katalanisch               | ca            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Tschechisch                 | cs            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Dänisch                | da            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Deutsch                | de            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Griechisch                 | el            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Englisch               | en            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Spanisch               | es            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Estnisch              | et            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Baskisch                | eu            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Finnisch               | fi            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Französisch                | fr            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Galizisch              | gl            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Hebräisch                | he            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Kroatisch              | hr            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Ungarisch             | hu            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Indonesisch            | id            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Italienisch               | it            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Japanisch              | ja            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Kasachisch                | kk            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Kannada               | kn            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Koreanisch                | ko            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Litauisch            | lt            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Lettisch               | lv            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Marathi               | mr            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Malaiisch                 | ms            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Norwegisch Bokmål      | nb            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Niederländisch                 | nl            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Norwegisch             | nein            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Pandschabi               | pa            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Polnisch                | pl            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Portugiesisch (Brasilien)   | pt-br         | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Portugiesisch (Portugal) | pt-pt         | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Rumänisch              | ro            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Russisch               | ru            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Slowakisch                | sk            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Slowenisch             | sl            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Serbisch (Kyrillisch)    | sr-cryl-cs    | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Serbisch (Lateinisch)       | sr-latn-cs    | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Schwedisch               | sv            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Tamilisch                 | ta            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) |
| Thailändisch                  | th            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Türkisch               | tr            | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Ukrainisch             | uk            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Vietnamesisch            | vi            | ![„X“ für „Nein“.](./media/user-flow-language-customization/no.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Chinesisch (vereinfacht)  | zh-hans       | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |
| Chinesisch (traditionell) | zh-hant       | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) | ![Grünes Häkchen.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Nächste Schritte

::: zone pivot="b2c-user-flow"

Weitere Informationen dazu, wie Sie die Benutzeroberfläche Ihrer Anwendungen anpassen können, finden Sie unter [Anpassen der Benutzeroberfläche einer Anwendung in Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Erfahren Sie mehr über das [localization](localization.md)-Element in der IEF-Referenz.
- Weitere Informationen finden Sie in der Liste der [Lokalisierungszeichenfolgen-IDs](localization-string-ids.md) in Azure AD B2C.

::: zone-end 
