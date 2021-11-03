---
title: Definieren eines technischen Validierungsprofils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Validieren Sie Ansprüche mithilfe eines technischen Validierungsprofils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 3a394a793a6c681a73aace76d191f571db0a9089
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131032210"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Validierungsprofils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ein technisches Validierungsprofil ist ein ganz einfaches technisches Profil aus jedem beliebigen Protokoll, z. B. [Azure Active Directory](active-directory-technical-profile.md) oder einer [REST-API](restful-technical-profile.md). Das technische Validierungsprofil gibt Ausgabeansprüche oder einen HTTP-Statuscode des Typs 4xx mit den folgenden Daten zurück. Weitere Informationen finden Sie unter [Zurückgeben einer Fehlermeldung.](restful-technical-profile.md#returning-validation-error-message)

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Der Umfang der Ausgabeansprüche eines technischen Validierungsprofils ist auf das [selbst bestätigte technische Profil](self-asserted-technical-profile.md), das das technische Validierungsprofil aufruft, und dessen technische Validierungsprofile beschränkt. Wenn Sie die Ausgabeansprüche im nächsten Orchestrierungsschritt verwenden möchten, müssen Sie die Ausgabeansprüche dem selbst bestätigten technischen Profil hinzufügen, das das technische Validierungsprofil aufruft.

Technische Validierungsprofile werden in der Reihenfolge ausgeführt, in der sie im **ValidationTechnicalProfiles**-Element angezeigt werden. Sie können in einem technischen Validierungsprofil konfigurieren, ob die Ausführung jeglicher nachfolgender technischer Validierungsprofile fortgesetzt werden soll, wenn das technische Validierungsprofil einen Fehler auslöst oder erfolgreich ist.

Technische Validierungsprofile können bedingt ausgeführt werden, basierend auf Vorbedingungen, die im **ValidationTechnicalProfiles**-Element definiert sind. Beispielsweise können Sie überprüfen, ob ein bestimmter Anspruch vorhanden oder ein Anspruch gleich oder ungleich dem angegebenen Wert ist.

Ein selbstbestätigtes technisches Profil kann definieren, dass ein technisches Validierungsprofil verwendet wird, um einige oder alle seiner Ausgabeansprüche zu validieren. Alle Eingabeansprüche des technischen Profils, auf das verwiesen wird, müssen in den Ausgabeansprüchen des verweisenden technischen Validierungsprofils enthalten sein.

> [!NOTE]
> Nur selbstbestätigte technische Profile können technische Validierungsprofile verwenden. Wenn Sie die Ausgabeansprüche von nicht selbst bestätigten technischen Profilen überprüfen müssen, sollten Sie einen zusätzlichen Orchestrierungsschritt in Ihrer User Journey in Betracht ziehen, um das technische Profil für die Validierung zu unterstützen.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Das **ValidationTechnicalProfiles**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Ein technisches Profil, mit dem einige oder alle Ausgabeansprüche des verweisenden technischen Profils überprüft werden sollen. |

Das **ValidationTechnicalProfile**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner eines technischen Profils, das bereits in der Richtlinie oder der übergeordneten Richtlinie definiert ist. |
|ContinueOnError|Nein| Zeigt an, ob die Validierung jeglicher nachfolgender technischer Validierungsprofile fortgesetzt werden soll, wenn das technische Validierungsprofil einen Fehler auslöst. Mögliche Werte: `true` oder `false` (Standard, die Verarbeitung weiterer Validierungsprofile wird beendet und ein Fehler zurückgegeben). |
|ContinueOnSuccess | Nein | Zeigt an, ob die Validierung jeglicher nachfolgender Validierungsprofile fortgesetzt werden soll, wenn das technische Validierungsprofil erfolgreich ist. Mögliche Werte: `true` oder `false`. Der Standardwert ist `true`, was bedeutet, dass die Verarbeitung weiterer Validierungsprofile fortgesetzt wird. |

Das **ValidationTechnicalProfile**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Eine Liste von Vorbedingungen, die für die Ausführung des technischen Validierungsprofil erfüllt sein müssen. |

Das **Precondition**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Type` | Ja | Der Typ der Überprüfung oder Abfrage, die für die Vorbedingung ausgeführt werden soll. Entweder wird `ClaimsExist` angegeben, um sicherzustellen, dass Aktionen ausgeführt werden, wenn die angegebenen Ansprüche im aktuellen Satz von Ansprüchen des Benutzers vorhanden sind, oder es wird `ClaimEquals` angegeben, damit die Aktionen ausgeführt werden, wenn der angegebene Anspruch vorhanden ist und sein Wert gleich dem angegebenen Wert ist. |
| `ExecuteActionsIf` | Ja | Zeigt an, ob die Aktionen in der Vorbedingung ausgeführt werden sollen, wenn der Test „true“ oder „false“ ist. |

Das **Precondition**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Wert | 1:n | Die Daten, die von der Überprüfung verwendet werden. Wenn der Typ dieser Überprüfung `ClaimsExist` ist, gibt dieses Feld eine ClaimTypeReferenceId an, die abzufragen ist. Wenn der Typ der Überprüfung `ClaimEquals` ist, gibt dieses Feld eine ClaimTypeReferenceId an, die abzufragen ist. Während ein anderes Wertelement den zu überprüfenden Wert enthält.|
| Aktion | 1:1 | Die Aktion, die ausgeführt werden soll, wenn die Überprüfung der Vorbedingung innerhalb eines Orchestrierungsschritts „true“ ist. Der Wert der **Aktion** wird auf `SkipThisValidationTechnicalProfile` festgelegt. Gibt an, dass das zugeordnete technische Validierungsprofil nicht ausgeführt werden soll. |

### <a name="example"></a>Beispiel

Im folgenden Beispiel werden diese technischen Validierungsprofile verwendet:

1. Das erste technische Validierungsprofil überprüft Benutzeranmeldeinformationen und wird nicht fortgesetzt, wenn ein Fehler auftritt, z. B. ein ungültiger Benutzername oder ein ungültiges Kennwort.
2. Das nächste technische Validierungsprofil wird nicht ausgeführt, wenn der „userType“-Anspruch nicht vorhanden ist oder wenn der Wert von „usertype“ `Partner` ist. Das technische Validierungsprofil versucht, das Benutzerprofil aus der internen Kundendatenbank zu lesen, und wird fortgesetzt, wenn ein Fehler auftritt, z.B. ein nicht verfügbarer REST-API-Dienst oder ein beliebiger interner Fehler.
3. Das letzte technische Validierungsprofil wird nicht ausgeführt, wenn der „userType“-Anspruch nicht vorhanden war oder wenn der Wert von „usertype“ `Customer` ist. Das technische Validierungsprofil versucht, das Benutzerprofil aus der internen Partnerdatenbank zu lesen, und wird fortgesetzt, wenn ein Fehler auftritt, z.B. ein nicht verfügbarer REST-API-Dienst oder ein beliebiger interner Fehler.

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
