---
title: SAML-Protokoll für einmaliges Abmelden in Azure
description: In diesem Artikel wird das SAML-Protokoll für einmaliges Abmelden in Azure Active Directory beschrieben.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 71384446b37841513bbd362b6398e0cd892167ab
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778012"
---
# <a name="single-sign-out-saml-protocol"></a>SAML-Protokoll für einmaliges Abmelden

Azure Active Directory (Azure AD) unterstützt das SAML 2.0-Webbrowserprofil für einmaliges Abmelden. Damit die einmalige Abmeldung ordnungsgemäß funktioniert, muss die **LogoutURL** der Anwendung bei der Anwendungsregistrierung explizit in Azure AD registriert worden sein. Wenn die App dem [Azure-App-Katalog hinzugefügt wird](v2-howto-app-gallery-listing.md), kann dieser Wert standardmäßig festgelegt werden. Andernfalls muss der Wert von der Person bestimmt und festgelegt werden, die die App ihrem Azure AD-Mandanten hinzufügt. Azure AD verwendet die LogoutURL zur Umleitung von Benutzern nach deren Abmeldung. 

Azure AD unterstützt die Umleitungsbindung (HTTP GET), aber nicht die HTTP POST-Bindung.

In der folgenden Abbildung ist der Workflow des Azure AD-Prozesses für das einmalige Abmelden dargestellt.

![Workflow für einmaliges Abmelden mit Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Der Clouddienst sendet eine `LogoutRequest` -Nachricht an Azure AD, um anzugeben, dass eine Sitzung beendet wurde. Der folgende Auszug enthält ein `LogoutRequest` -Beispielelement.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Für das an Azure AD gesendete `LogoutRequest` -Element sind die folgenden Attribute erforderlich:

* `ID`: Dieses Attribut dient zum Identifizieren der Abmeldeanforderung. Der Wert von `ID` darf nicht mit einer Zahl beginnen. Die übliche Vorgehensweise besteht darin, **id** an die Zeichenfolgendarstellung einer GUID anzufügen.
* `Version`: Legen Sie den Wert dieses Elements auf **2.0** fest. Dieser Wert ist erforderlich.
* `IssueInstant`: Bei diesem Attribut handelt es sich um eine `DateTime`-Zeichenfolge mit einem UTC-Wert (Coordinated Universal Time) und [Roundtripformat („o“)](/dotnet/standard/base-types/standard-date-and-time-format-strings). Azure AD erwartet einen Wert dieses Typs, aber der Wert wird nicht erzwungen.

### <a name="issuer"></a>Issuer (Aussteller)
Das `Issuer`-Element in einem `LogoutRequest`-Element muss mit einem der **ServicePrincipalNames**-Werte im Clouddienst von Azure AD exakt übereinstimmen. Normalerweise ist es auf den **App-ID-URI** festgelegt, der bei der Anwendungsregistrierung angegeben wird.

### <a name="nameid"></a>NameID
Der Wert des `NameID`-Elements muss genau mit `NameID` des Benutzers übereinstimmen, der abgemeldet wird.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD sendet als Antwort auf ein `LogoutRequest`-Element ein `LogoutResponse`-Element. Der folgende Auszug enthält ein Beispiel für `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD legt die Werte `ID`, `Version` und `IssueInstant` im `LogoutResponse`-Element fest. Außerdem wird das `InResponseTo`-Element auf den Wert des `ID`-Attributs des `LogoutRequest`-Elements festgelegt, von dem die Antwort stammt.

### <a name="issuer"></a>Issuer (Aussteller)
Azure AD legt diesen Wert auf `https://login.microsoftonline.com/<TenantIdGUID>/` fest, wobei \<TenantIdGUID> die Mandanten-ID des Azure AD-Mandanten ist.

Verwenden Sie zum Auswerten des Werts für das `Issuer` -Element den Wert für **App-ID-URI** , der bei der Anwendungsregistrierung angegeben wurde.

### <a name="status"></a>Status
Azure AD verwendet das `StatusCode`-Element im `Status`-Element, um anzugeben, ob die Abmeldung erfolgreich war. Wenn beim Abmelden ein Fehler auftritt, kann das `StatusCode` -Element auch benutzerdefinierte Fehlermeldungen enthalten.
