---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie das ContentDefinitions-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/12/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: af77df3d3839a019e4977b32c6b8b138b375b4f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007446"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Sie können das Aussehen und Verhalten von [selbstbestätigten technischen Profilen](self-asserted-technical-profile.md) anpassen. Azure Active Directory B2C (Azure AD B2C) führt den Code im Browser Ihres Kunden aus und verwendet einen modernen Ansatz namens Cross-Origin Resource Sharing (CORS, Ressourcenfreigabe zwischen verschiedenen Ursprüngen).

Zum Anpassen der Benutzeroberfläche geben Sie im **ContentDefinition**-Element eine URL mit benutzerdefiniertem HTML-Inhalt an. Verweisen Sie in dem selbstbestätigten technischen Profil oder in **OrchestrationStep** auf den Bezeichner der Inhaltsdefinition. Die Inhaltsdefinition darf ein **LocalizedResourcesReferences**-Element mit einer Liste der lokalisierten Ressourcen, die geladen werden sollen, enthalten. Azure AD B2C führt die Benutzeroberflächenelemente mit dem HTML-Inhalt, der über Ihre URL geladen wird, zusammen und zeigt anschließend die Seite für den Benutzer an.

Das **ContentDefinitions**-Element enthält die URLs für HTML5-Vorlagen, die in einer User Journey verwendet werden können. Der HTML5-Seiten-URI wird für einen angegebenen Schritt auf der Benutzeroberfläche verwendet. Dies könnten beispielsweise die Seiten für das Registrieren oder Anmelden oder das Zurücksetzen des Kennworts oder Fehlerseiten sein. Sie können das Aussehen und Verhalten durch Überschreiben des LoadUri für die HTML5-Datei ändern. Sie können neue Inhaltsdefinitionen nach Ihren Anforderungen erstellen. Dieses Element kann einen Verweis auf lokalisierte Ressourcen im Lokalisierungsbezeichner, der im [Localization](localization.md)-Element angegeben wird, enthalten.

Das folgende Beispiel zeigt den Bezeichner für die Inhaltsdefinition und die Definition von lokalisierten Ressourcen:

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Die Metadaten des selbstbestätigten technischen Profils **LocalAccountSignUpWithLogonEmail** enthalten den Bezeichner für die Inhaltsdefinition **ContentDefinitionReferenceId**, der auf `api.localaccountsignup` festgelegt ist.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

Das **ContentDefinition**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner für eine Inhaltsdefinition. Der Wert wird im Abschnitt **ID für Inhaltsdefinitionen** weiter unten auf dieser Seite beschrieben. |

Das **ContentDefinition**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Eine Zeichenfolge, die die URL der HTML5-Seite für die Inhaltsdefinition enthält. |
| RecoveryUri | 1:1 | Eine Zeichenfolge, die die URL der HTML-Seite für die Anzeige eines Fehlers im Zusammenhang mit der Inhaltsdefinition enthält. Derzeit nicht verwendet, der Wert muss `~/common/default_page_error.html` sein. |
| DataUri | 1:1 | Eine Zeichenfolge mit der relativen URL einer HTML-Datei, die die Benutzeroberfläche, die für den Schritt aufgerufen werden soll, bereitstellt. |
| Metadaten | 0:1 | Eine Sammlung von Schlüssel-Wert-Paaren, die Metadaten enthält, die von der Inhaltsdefinition genutzt werden. |
| LocalizedResourcesReferences | 0:1 | Eine Sammlung von Verweisen auf lokalisierte Ressourcen. Verwenden Sie dieses Element, um die Lokalisierung einer Benutzeroberfläche und eines Anspruchsattributs anzupassen. |

### <a name="loaduri"></a>LoadUri

Das Element **LoadUri** wird verwendet, um die URL der HTML5-Seite für die Inhaltsdefinition anzugeben. Die [Starter Packs für benutzerdefinierte Richtlinien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) von Azure AD B2C enthalten Inhaltsdefinitionen, die Azure AD B2C-HTML-Seiten verwenden. Das Element **LoadUri** beginnt mit `~`. einem relativen Pfad zu Ihrem Azure AD B2C-Mandanten.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  ...
</ContentDefinition>
```

Sie können [die Benutzeroberfläche mit HTML-Vorlagen anpassen](customize-ui-with-html.md). Bei der Verwendung von HTML-Vorlagen müssen Sie eine absolute URL angeben. Im folgenden Beispiel wird eine Inhaltsdefinition mit HTML-Vorlage veranschaulicht:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="datauri"></a>DataUri

Das **DataUri**-Element wird verwendet, um den Seitenbezeichner anzugeben. Azure AD B2C verwendet den Seitenbezeichner, um Elemente der Benutzeroberfläche und clientseitigen JavaScript-Code zu laden und zu initiieren. Das Format des Werts ist `urn:com:microsoft:aad:b2c:elements:page-name:version`. Die folgende Tabelle enthält die Seitenbezeichner, die Sie verwenden können.

| Seitenbezeichner | BESCHREIBUNG |
| ----- | ----------- |
| `globalexception` | Zeigt eine Fehlerseite an, wenn eine Ausnahme oder ein Fehler auftreten. |
| `providerselection`, `idpselection` | Listet die Identitätsanbieter auf, unter denen Benutzer bei der Registrierung auswählen können.  |
| `unifiedssp` | Zeigt ein Formular für die Anmeldung mit einem lokalen Konto, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert, an. Dieser Wert stellt auch die Funktion „Angemeldet bleiben“ und die Verknüpfung „Kennwort vergessen?“ bereit . |
| `unifiedssd` | Zeigt ein Formular für die Anmeldung mit einem lokalen Konto, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert, an. Dieser Seitenbezeichner ist veraltet. Nutzen Sie stattdessen den Seitenbezeichner `unifiedssp`.  |
| `multifactor` | Überprüft während der Registrierung oder Anmeldung (per SMS oder Sprachnachricht) die Telefonnummern. |
| `selfasserted` | Zeigt ein Formular zum Sammeln von Daten eines Benutzers an. Hier können Benutzer beispielsweise ihr Profil erstellen oder aktualisieren. |

### <a name="select-a-page-layout"></a>Auswählen eines Seitenlayouts

Sie können [clientseitigen JavaScript-Code](javascript-and-page-layout.md) aktivieren, indem Sie `contract` zwischen `elements` und dem Seitentyp einfügen. Beispiel: `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

Der Bereich [version](page-layout.md) des `DataUri` gibt das Inhaltspaket an, das HTML, CSS und JavaScript für die Benutzeroberflächenelemente in Ihrer Richtlinie enthält. Wenn Sie beabsichtigen, clientseitigen JavaScript-Code zu aktivieren, müssen die Elemente, auf denen der JavaScript-Code basiert, unveränderlich sein. Wenn sie nicht unveränderlich sind, könnten Änderungen zu einem unerwarteten Verhalten auf Ihren Benutzerseiten führen. Um diese Probleme zu vermeiden, können Sie die Verwendung eines Seitenlayouts erzwingen und eine Seitenlayoutversion angeben. Damit stellen Sie sicher, dass alle Inhaltsdefinitionen, auf denen Ihr JavaScript-Code basiert, unveränderlich sind. Auch wenn Sie nicht beabsichtigen, JavaScript zu aktivieren, können Sie eine Seitenlayoutversion für Ihre Seiten angeben.

Das folgende Beispiel zeigt den **DataUri** von `selfasserted`version`1.2.0`:

```xml
<!-- 
<BuildingBlocks> 
  <ContentDefinitions>-->
    <ContentDefinition Id="api.localaccountpasswordreset">
      <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
      <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
      <Metadata>
        <Item Key="DisplayName">Local account change password page</Item>
      </Metadata>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions> 
</BuildingBlocks> -->
```

#### <a name="migrating-to-page-layout"></a>Migrieren zum Seitenlayout

Um vom alten **DataUri-Wert** (ohne Seitenvertrag) zur Seitenlayoutversion zu migrieren, fügen Sie das Wort `contract`, gefolgt von einer Seitenversion, hinzu. Verwenden Sie die folgende Tabelle, um vom alten **DataUri-Wert** zur Seitenlayoutversion zu migrieren.

| Alter DataUri-Wert | Neuer DataUri-Wert |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5` |

Im folgenden Beispiel werden die Inhaltsdefinitions-IDs mit dem zugehörigen **DataUri** für die [aktuelle Seitenversion](page-layout.md) gezeigt:

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Metadaten

Das **Metadata**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Element | 0:n | Die Metadaten zu der Inhaltsdefinition. |

Das **Item**-Element des **Metadata**-Elements enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Schlüssel | Ja | Der Metadatenschlüssel.  |

#### <a name="metadata-keys"></a>Metadatenschlüssel

Die Inhaltsdefinition unterstützt die folgenden Metadatenelemente:

| Key | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| DisplayName | Nein | Eine Zeichenfolge, die den Namen der Inhaltsdefinition enthält. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Das **LocalizedResourcesReferences**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Eine Liste mit Verweisen auf lokalisierte Ressourcen für die Inhaltsdefinition. |

Das **LocalizedResourcesReference**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| Sprache | Ja | Eine Zeichenfolge mit einer unterstützten Sprache für die Richtlinie gemäß „RFC 5646 – Tags for Identifying Languages“ (Tags für das Angeben von Sprachen). |
| LocalizedResourcesReferenceId | Ja | Der Bezeichner des **LocalizedResources**-Elements. |

Das folgende Beispiel zeigt eine Inhaltsdefinition für die Registrierung oder Anmeldung mit einem Verweis auf die Lokalisierung für Englisch, Französisch und Spanisch:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Weitere Informationen zum Hinzufügen von Unterstützung für die Lokalisierung Ihrer Inhaltsdefinitionen finden Sie unter [Lokalisierung](localization.md).

## <a name="content-definition-ids"></a>ID für Inhaltsdefinitionen

Das ID-Attribut des **ContentDefinition**-Elements gibt den Typ der Seite an, die mit der Inhaltsdefinition verknüpft ist. Das Element definiert den Kontext, den eine benutzerdefinierte HTML5/CSS-Vorlage anwendet. In der folgenden Tabelle werden die Gruppe mit den IDs der Inhaltsdefinitionen, die vom Identity Experience Framework erkannt werden, und die entsprechenden Seitentypen beschrieben. Sie können eigene Inhaltsdefinitionen mit beliebigen IDs erstellen.

| ID | Standardvorlage | Beschreibung |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Fehlerseite:** zeigt eine Fehlerseite an, wenn eine Ausnahme oder ein Fehler auftreten. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seite zur Auswahl des Identitätsanbieters:** Auf dieser Seite sind Identitätsanbieter aufgelistet, unter denen Benutzer bei der Anmeldung wählen können. Bei den Optionen handelt es sich normalerweise um Unternehmensidentitätsanbieter oder Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Auswahl des Identitätsanbieters für die Registrierung:** listet die Identitätsanbieter auf, unter denen Benutzer bei der Registrierung auswählen können. Bei den Optionen handelt es sich normalerweise um Unternehmensidentitätsanbieter oder Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten. |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite „Kennwort vergessen“:** enthält ein Formular, das Benutzer ausfüllen müssen, um eine Kennwortzurücksetzung zu initiieren. |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite für Anmeldung mit lokalem Konto:** zeigt ein Formular für die Anmeldung mit einem lokalen Konto, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert, an. Das Formular kann ein Texteingabefeld und ein Kennworteingabefeld enthalten. |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite für Registrierung mit lokalem Konto:** zeigt ein Formular für die Registrierung mit einem lokalen Konto, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert, an. Das Formular kann verschiedene Eingabesteuerelemente enthalten, z.B. ein Texteingabefeld, ein Kennworteingabefeld, ein Optionsfeld, Dropdownfelder mit einer Auswahlmöglichkeit und Kontrollkästchen mit mehreren Optionen. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Seite für die mehrstufige Authentifizierung:** überprüft während der Registrierung oder Anmeldung (per SMS oder Sprachnachricht) die Telefonnummern. |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Seite zur Registrierung über ein Social Media-Konto:** enthält ein Formular, das Benutzer ausfüllen müssen, wenn die Registrierung mit einem vorhandenen Konto bei einem sozialen Netzwerk als Identitätsanbieter erfolgt. Diese Seite ähnelt der vorherigen Seite für die Registrierung bei einem Konto für ein soziales Netzwerk, mit Ausnahme der Felder für die Kennworteingabe. |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Seite zur Profilaktualisierung:** enthält ein Formular, auf das Benutzer zum Aktualisieren des Profils zugreifen können. Diese Seite ähnelt der Seite für die Registrierung bei einem Konto für ein soziales Netzwerk, mit Ausnahme der Felder für die Kennworteingabe. |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Einheitliche Seite für Registrierung oder Anmeldung:** führt den Registrierungs- oder Anmeldevorgang für Benutzer durch. Benutzer können Unternehmensidentitätsanbieter, Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten verwenden. |

## <a name="next-steps"></a>Nächste Schritte

Ein Beispiel für die Anpassung der Benutzeroberfläche mithilfe von Inhaltsdefinitionen finden Sie unter:

[Anpassen der Benutzeroberfläche Ihrer Anwendung mit einer benutzerdefinierten Richtlinie](customize-ui-with-html.md)
