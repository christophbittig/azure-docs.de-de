---
title: TrustFrameworkPolicy – Azure Active Directory B2C
description: Erfahren Sie, wie Sie das TrustFrameworkPolicy-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 1eb877215137338e2522ae9e8b215cceb469f49b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055472"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Eine benutzerdefinierte Richtlinie wird als eine oder mehrere XML-formatierte Dateien dargestellt, die aufeinander in einer hierarchischen Kette verweisen. Die XML-Elemente definieren Elemente der Richtlinie wie das Anspruchsschema, Anspruchstransformationen, Inhaltsdefinitionen, Anspruchsanbieter, technische Profile, User Journeys und Orchestrierungsschritte. Jede Richtliniendatei wird im **TrustFrameworkPolicy**-Element der obersten Ebene einer Richtliniendatei definiert.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Das **TrustFrameworkPolicy**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | Die Schemaversion, die zum Ausführen der Richtlinie verwendet werden soll. Der Wert muss `0.3.0.0` sein. |
| TenantObjectId | Nein | Der eindeutige Objektbezeichner des Azure Active Directory (Azure AD) B2C-Mandanten. |
| TenantId | Ja | Der eindeutige Bezeichner des Mandanten, zu dem diese Richtlinie gehört. |
| `PolicyId` | Ja | Der eindeutige Bezeichner für die Richtlinie. Diesem Bezeichner muss das Präfix *B2C_1A_* vorangestellt werden. |
| PublicPolicyUri | Ja | Der URI für die Richtlinie, bei dem es sich um eine Kombination der Mandanten-ID und der Richtlinien-ID handelt. |
| DeploymentMode | Nein | Mögliche Werte sind `Production` oder `Development`. `Production` ist die Standardeinstellung. Verwenden Sie diese Eigenschaft, um Ihre Richtlinie zu debuggen. Weitere Informationen finden Sie unter [Sammeln von Protokollen](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Nein | Der Endpunkt, der für die Protokollierung verwendet wird. Der Wert muss auf `urn:journeyrecorder:applicationinsights` festgelegt werden, wenn das Attribut vorhanden ist. Weitere Informationen finden Sie unter [Sammeln von Protokollen](troubleshoot-with-application-insights.md). |


Das folgende Beispiel zeigt, wie das **TrustFrameworkPolicy**-Element angegeben wird:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Das **TrustFrameworkPolicy**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Der Bezeichner einer Basisrichtlinie |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Die Bausteine Ihrer Richtlinie |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Eine Sammlung von Anspruchsanbietern |
| [UserJourneys](userjourneys.md) | 0:1 | Eine Sammlung von User Journeys |
| [SubJourneys](subjourneys.md) | 0:1 | Eine Sammlung von untergeordneten Journeys. |
| [RelyingParty](relyingparty.md) | 0:1 | Eine Definition einer Richtlinie der vertrauenden Seite. |

Damit eine Richtlinie von einer anderen Richtlinie erben kann, muss ein **BasePolicy**-Element unter dem **TrustFrameworkPolicy**-Element der Richtliniendatei deklariert werden. Das **BasePolicy**-Element ist ein Verweis auf die Basisrichtlinie, von der diese Richtlinie abgeleitet ist.

Das **BasePolicy**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | --------|
| TenantId | 1:1 | Der Bezeichner Ihres Azure AD B2C-Mandanten. |
| PolicyId | 1:1 | Der Bezeichner der übergeordneten Richtlinie. |


Im folgenden Beispiel wird gezeigt, wie Sie eine Basisrichtlinie angeben. Diese Richtlinie **B2C_1A_TrustFrameworkExtensions** ist von der Richtlinie **B2C_1A_TrustFrameworkBase** abgeleitet.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

