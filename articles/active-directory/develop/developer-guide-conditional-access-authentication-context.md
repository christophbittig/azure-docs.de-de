---
title: Anleitung für Entwickler zu Authentifizierungskontext für bedingten Zugriff mit Azure AD
description: Anleitung und Szenarios für Entwickler zu Authentifizierungskontext für bedingten Zugriff mit Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: CelesteDG
ms.reviewer: kkrishna
ms.workload: identity
ms.custom: aaddev
ms.openlocfilehash: 48e9598acac2739b7663fcd9a46006208d44783c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459499"
---
# <a name="developers-guide-to-conditional-access-authentication-context"></a>Anleitung für Entwickler zu Authentifizierungskontext für bedingten Zugriff

[Bedingter Zugriff](../conditional-access/overview.md) ist die Zero Trust-Steuerungsebene, mit der Sie gezielte Richtlinien für den Zugriff auf all Ihre Apps festlegen können – alt oder neu, privat oder öffentlich, lokal oder in mehreren Clouds. Mit [Authentifizierungskontext für bedingten Zugriff](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview) können Sie innerhalb dieser Apps verschiedene Richtlinien anwenden. 

Mit Authentifizierungskontext für bedingten Zugriff (Authentifizierungskontext) können Sie differenzierte Richtlinien auf vertrauliche Daten und Aktionen anwenden, anstatt nur auf Anwendungsebene. Sie können Zero Trust-Richtlinien für den Zugriff mit den geringsten Rechten genauer festlegen und gleichzeitig Benutzerkonflikte minimieren, die Produktivität der Benutzer erhöhen und die Sicherheit von Ressourcen optimieren. Heute kann der Authentifizierungskontext von Anwendungen verwendet werden, bei denen [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) für Authentifizierungsfunktionen eingesetzt wird, die Ihr Unternehmen entwickelt hat, um vertrauliche Ressourcen zu schützen, z. B. für hochwertige Transaktionen oder die Anzeige personenbezogener Daten von Mitarbeitern.

Verwenden Sie das neue Authentifizierungskontextfeature der Engine für bedingten Zugriff mit Azure AD, um eine Anforderung für eine Step-up-Authentifizierung innerhalb einer Anwendung und innerhalb eines Diensts auszulösen. Entwickler haben jetzt die Möglichkeit, in ihren Anwendungen selektiv eine verbesserte, sicherere Authentifizierung, z. B. mehrstufige Authentifizierung, von den Endbenutzern zu verlangen. Mit diesem Feature können Entwickler für den Großteil ihrer Anwendung die Benutzerfreundlichkeit optimieren, während der Zugriff auf Vorgänge und Daten, die größere Sicherheit erfordern, durch strengere Authentifizierungssteuerungen abgesichert wird.

## <a name="problem-statement"></a>Problembeschreibung

IT-Administratoren und aufsichtsführende Stellen haben oft Schwierigkeiten, einen Ausgleich bei der Authentifizierung von Benutzern zu schaffen, also Benutzern einerseits nicht zu häufig zusätzliche Authentifizierungsstufen abzuverlangen, andererseits aber ein ausreichendes Sicherheitsniveau und die angemessene Einhaltung von Richtlinien für Anwendungen und Dienste zu gewährleisten, die in Teilen vertrauliche Daten und Vorgänge beinhalten. Die Entscheidung fällt hier gegebenenfalls zwischen einer sicheren Richtlinie, durch die die Produktivität der Benutzer beim Zugriff auf den Großteil der Daten und Aktionen beeinträchtigt wird, und einer Richtlinie, die für vertrauliche Ressourcen nicht sicher genug ist. 

Was wäre, wenn bei Anwendungen beides kombiniert werden könnte? Soll heißen: Anwendungen funktionieren für die meisten Benutzer und Vorgänge mit einem im Vergleich niedrigeren Sicherheitsniveau und weniger häufigen Eingabeaufforderungen, die Sicherheitsanforderungen werden aber bedingungsbasiert strikter, wenn Benutzer auf vertraulichere Teile zugreifen. 

## <a name="common-scenarios"></a>Häufige Szenarien

Beispiel: Benutzer können sich mithilfe mehrstufiger Authentifizierung bei SharePoint anmelden, aber für den Zugriff auf die Websitesammlung in SharePoint, die vertrauliche Dokumente enthält, kann zur Bedingung gemacht werden, dass ein kompatibles Gerät verwendet wird und der Zugriff nur über vertrauenswürdige IP-Adressbereiche erfolgt. 

## <a name="steps"></a>Schritte

Im Folgenden werden die Voraussetzungen und Schritte für die Verwendung von Authentifizierungskontext für bedingten Zugriff beschrieben. 

### <a name="prerequisites"></a>Voraussetzungen

**Erstens** sollte die App mit Microsoft Identity Platform integriert werden, indem [OpenID Connect](v2-protocols-oidc.md)/ [OAuth 2.0](v2-oauth2-auth-code-flow.md)-Protokolle für die Authentifizierung und Autorisierung verwendet werden. Es empfiehlt sich, dass Sie [Microsoft Identity Platform-Authentifizierungsbibliotheken](reference-v2-libraries.md) verwenden, um Ihre Anwendung mit Azure Active Directory zu integrieren und entsprechend abzusichern. Die [Microsoft Identity Platform-Dokumentation](index.yml) ist ein guter Ausgangspunkt, wenn Sie sich damit vertraut machen möchten, wie Sie Anwendungen mit Microsoft Identity Platform integrieren. Die Unterstützung von Authentifizierungskontext für bedingten Zugriff basiert auf Protokollerweiterungen, die vom Branchenstandardprotokoll [OpenID Connect](v2-protocols-oidc.md) bereitgestellt werden. Entwickler verwenden einen [für bedingten Zugriff genutzten Authentifizierungskontextverweis](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)-**Wert** mit dem [Anspruchsanforderungsparameter](claims-challenge.md), um für Anwendungen eine Möglichkeit verfügbar zu machen, Richtlinien auszulösen und zu erfüllen.

**Zweitens** ist für [bedingten Zugriff](../conditional-access/overview.md) eine Azure Active Directory Premium P1-Lizenzierung erforderlich. Weitere Informationen zur Lizenzierung finden Sie auf der Seite [Azure Active Directory (AD) – Preise](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

**Drittens** ist zu beachten, dass die Verwendung von Authentifizierungskontext für bedingten Zugriff heute nur für Anwendungen verfügbar ist, bei denen sich Benutzer anmelden. Anwendungen, die sich selbst authentifizieren, werden nicht unterstützt. Verwenden Sie den [Leitfaden für Authentifizierungsflows und Anwendungsszenarios](authentication-flows-app-scenarios.md), um mehr über die unterstützten authentifizierungsbezogenen App-Typen und Flows in Microsoft Identity Platform zu erfahren.

### <a name="integration-steps"></a>Schritte für die Integration

Sobald die Anwendung mithilfe der unterstützten Authentifizierungsprotokolle integriert und in einem Azure AD-Mandanten registriert ist, für den das Feature für bedingten Zugriff verfügbar ist, können Sie den Prozess starten, um dieses Feature in Anwendungen zu integrieren, bei denen sich Benutzer anmelden.

> [!NOTE]
> Eine ausführliche exemplarische Vorgehensweise zu diesem Feature finden Sie auch als aufgezeichnete Sitzung unter [Verwenden von Authentifizierungskontext für bedingten Zugriff zum Zweck der Step\-up-Authentifizierung in Anwendungen](https://www.youtube.com/watch?v=_iO7CfoktTY) (Englisch).

**Erstens**: Deklarieren Sie die Authentifizierungskontexte, und machen Sie diese im Mandanten verfügbar. Weitere Informationen finden Sie unter [Konfigurieren von Authentifizierungskontexten](../conditional-access/concept-conditional-access-cloud-apps.md#configure-authentication-contexts).

Die Werte **C1–C25** sind für die Verwendung als **Authentifizierungskontext-IDs** in einem Mandanten verfügbar. Beispiele für Authentifizierungskontext:

- **C1**: Sichere Authentifizierung erforderlich
- **C2**: Kompatible Geräte erforderlich
- **C3**: Vertrauenswürdige Standorte erforderlich

Erstellen oder ändern Sie Richtlinien für bedingten Zugriff, um Authentifizierungskontexte für bedingten Zugriff zu verwenden. Mögliche Beispiele für Richtlinien:

- Alle Benutzer, die sich bei dieser Webanwendung anmelden, müssen die zweistufige Authentifizierung für die Authentifizierungskontext-ID **C1** erfolgreich abgeschlossen haben.
- Alle Benutzer, die sich bei dieser Webanwendung anmelden, müssen die zweistufige Authentifizierung erfolgreich abgeschlossen haben und auch über einen bestimmten IP-Adressbereich für die Authentifizierungskontext-ID **C3** auf die Web-App zugreifen.

> [!NOTE]
> Die Authentifizierungskontextwerte für bedingten Zugriff werden separat von Anwendungen deklariert und verwaltet. Es ist nicht ratsam, dass Anwendungen eine harte Abhängigkeit von Authentifizierungskontext-IDs aufweisen. Die Richtlinien für bedingten Zugriff werden in der Regel von IT-Administratoren erstellt, da sie über ein besseres Verständnis der Ressourcen verfügen, auf die Richtlinien angewendet werden können. Hinsichtlich eines Azure AD-Mandanten wissen IT-Administratoren beispielsweise, wie viele Benutzer des Mandanten für die Verwendung von zweistufiger Authentifizierung für mehrstufige Authentifizierung ausgestattet sind. So können sie sicherstellen, dass Richtlinien für bedingten Zugriff, die eine zweistufige Authentifizierung erfordern, auf die entsprechend ausgestatteten Benutzer beschränkt werden. Ganz ähnlich gilt: Wenn die Anwendung in mehreren Mandanten verwendet wird, können sich die verwendeten Authentifizierungskontext-IDs unterscheiden und sind in einigen Fällen möglicherweise überhaupt nicht verfügbar.

**Zweitens**: Den Entwicklern einer Anwendung, die die Verwendung von Authentifizierungskontext für bedingten Zugriff planen, wird empfohlen, zunächst den Anwendungsadministratoren oder IT-Administratoren eine Möglichkeit bereitzustellen, potenzielle vertrauliche Aktionen den Authentifizierungskontext-IDs zuzuordnen. Die entsprechenden Schritte sehen in etwa wie folgt aus:

1. Identifizieren Sie Aktionen im Code, die zur Zuordnung zu Authentifizierungskontext-IDs verfügbar gemacht werden können.
1. Erstellen Sie einen Bildschirm im Verwaltungsportal der Anwendung (oder eine entsprechende Funktionalität), die IT-Administratoren verwenden können, um vertrauliche Aktionen einer verfügbaren Authentifizierungskontext-ID zuzuordnen.
1. Das Codebeispiel unter [Verwenden von Authentifizierungskontext für bedingten Zugriff zum Ausführen einer Step-up-Authentifizierung](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Englisch) veranschaulicht die Vorgehensweise.

Diese Schritte entsprechen den Änderungen, die Sie in der Codebasis vornehmen müssen. Die Schritte umfassen im Großen und Ganzen Folgendes:

- Fragen Sie MS Graph ab, um [alle verfügbaren Authentifizierungskontexte aufzulisten](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences).
- Lassen Sie zu, dass IT-Administratoren vertrauliche Vorgänge bzw. Vorgänge, die hohe Berechtigungen erfordern, auswählen und sie mithilfe von Zertifizierungsstellenrichtlinien den verfügbaren Authentifizierungskontexten zuzuweisen können. 
- Speichern Sie diese Zuordnungsinformationen in Ihrer Datenbank bzw. ihrem lokalen Speicher.

:::image type="content" source="media/developer-guide-conditional-access-authentication-context/configure-conditional-access-authentication-context.png" alt-text="Setupflow zum Erstellen von Authentifizierungskontext":::

**Drittens**: Von Ihrer Anwendung – in diesem Beispiel wird davon ausgegangen, dass es sich um eine Web-API handelt, – müssen dann Aufrufe anhand der gespeicherten Zuordnung ausgewertet und entsprechende Anspruchsaufforderungen für die zugehörige Clientanwendungen ausgelöst werden. Zur Vorbereitung auf diese Aktion müssen die folgenden Schritte ausgeführt werden:

1. Werten Sie in einem vertraulichen und durch Authentifizierungskontext geschützten Vorgang die Werte im **acrs**-Anspruch anhand der zuvor gespeicherten Zuordnungen der Authentifizierungskontext-IDs aus, und erstellen Sie eine [Anspruchsaufforderung](claims-challenge.md), wie im folgenden Codeausschnitt angegeben. 

1. Die folgende Abbildung zeigt die Interaktion zwischen dem Benutzer, der Clientanwendung und der Web-API.

   :::image type="content" source="media/developer-guide-conditional-access-authentication-context/authentication-context-application-flow.png" alt-text="Abbildung der Interaktion von Benutzer, Webanwendung, API und Azure AD":::

   Der folgende Codeausschnitt stammt aus dem Codebeispiel unter [Verwenden von Authentifizierungskontext für bedingten Zugriff zum Ausführen einer Step-up-Authentifizierung](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Englisch). Mit der ersten Methode (`CheckForRequiredAuthContext()`) in der API 

      - Wird überprüft, ob die aufgerufene Aktion der Anwendung eine Step-up-Authentifizierung erfordert. Dies geschieht, indem die Datenbank auf eine gespeicherte Zuordnung für diese Methode überprüft wird.
      - Wenn diese Aktion tatsächlich Authentifizierungskontext mit erhöhten Rechten erfordert, wird der **acrs**-Anspruch auf eine vorhandene, übereinstimmende Authentifizierungskontext-ID überprüft.
      - Ist keine übereinstimmende Authentifizierungskontext-ID ermittelbar, wird eine [Anspruchsaufforderung](claims-challenge.md#claims-challenge-header-format) ausgelöst.

      ```
      public void CheckForRequiredAuthContext(string method)
      {
          string authType = _commonDBContext.AuthContext.FirstOrDefault(x => x.Operation == method 
                      && x.TenantId == _configuration["AzureAD:TenantId"])?.AuthContextId;

          if (!string.IsNullOrEmpty(authType))
          {
              HttpContext context = this.HttpContext;
              string authenticationContextClassReferencesClaim = "acrs";

              if (context == null || context.User == null || context.User.Claims == null 
                  || !context.User.Claims.Any())
              {
                  throw new ArgumentNullException("No Usercontext is available to pick claims from");
              }

              Claim acrsClaim = context.User.FindAll(authenticationContextClassReferencesClaim).FirstOrDefault(x 
                  => x.Value == authType);

              if (acrsClaim == null || acrsClaim.Value != authType)
              {
                  if (IsClientCapableofClaimsChallenge(context))
                  {
                      string clientId = _configuration.GetSection("AzureAd").GetSection("ClientId").Value;
                      var base64str = Convert.ToBase64String(Encoding.UTF8.GetBytes("{\"access_token\":{\"acrs\":{\"essential\":true,\"value\":\"" + authType + "\"}}}"));

                      context.Response.Headers.Append("WWW-Authenticate", $"Bearer realm=\"\", authorization_uri=\"https://login.microsoftonline.com/common/oauth2/authorize\", client_id=\"" + clientId + "\", error=\"insufficient_claims\", claims=\"" + base64str + "\", cc_type=\"authcontext\"");
                      context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
                      string message = string.Format(CultureInfo.InvariantCulture, "The presented access tokens had insufficient claims. Please request for claims requested in the WWW-Authentication header and try again.");
                      context.Response.WriteAsync(message);
                      context.Response.CompleteAsync();
                      throw new UnauthorizedAccessException(message);
                  }
                  else
                  {
                      throw new UnauthorizedAccessException("The caller does not meet the authentication  bar to carry our this operation. The service cannot allow this operation");
                  }
              }
          }
      }
      ```

   > [!NOTE]
   > Das Format der Anspruchsaufforderung wird im Artikel zur [Anspruchsaufforderung in Microsoft Identity Platform](claims-challenge.md) beschrieben. 

1. Fangen Sie in der Clientanwendung die Anspruchsaufforderung ab, und leiten Sie den Benutzer zur weiteren Richtlinienauswertung zurück zu Azure AD. Der folgende Codeausschnitt stammt aus dem Codebeispiel unter [Verwenden von Authentifizierungskontext für bedingten Zugriff zum Ausführen einer Step-up-Authentifizierung](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Englisch).

   ```
   internal static string ExtractHeaderValues(WebApiMsalUiRequiredException response)
   {
       if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized && response.Headers.WwwAuthenticate.Any())
       {
           AuthenticationHeaderValue bearer = response.Headers.WwwAuthenticate.First(v => v.Scheme == "Bearer");
           IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
           var errorValue = GetParameterValue(parameters, "error");

           try
           {
               // read the header and checks if it conatins error with insufficient_claims value.
               if (null != errorValue && "insufficient_claims" == errorValue)
               {
                   var claimChallengeParameter = GetParameterValue(parameters, "claims");
                   if (null != claimChallengeParameter)
                   {
                       var claimChallenge = ConvertBase64String(claimChallengeParameter);

                       return claimChallenge;
                   }
               }
           }
           catch (Exception ex)
           {
               throw ex;
           }
       }
       return null;
   }
   ```

   Behandeln von Ausnahmen im Aufruf der Web-API: Bei Anzeige einer Anspruchsaufforderung wird der Benutzer zur weiteren Verarbeitung zurück an Azure AD geleitet.

   ```
   try
   {
       // Call the API 
       await _todoListService.AddAsync(todo);
   }
   catch (WebApiMsalUiRequiredException hex)
   {
       // Challenges the user if exception is thrown from Web API.
       try
       {
           var claimChallenge =ExtractHeaderValues(hex);
           _consentHandler.ChallengeUser(new string[] { "user.read" }, claimChallenge);

           return new EmptyResult();
       }
       catch (Exception ex)
       {
           _consentHandler.HandleException(ex);
       }

       Console.WriteLine(hex.Message);
   }
   return RedirectToAction("Index");
   ```
   
1. (Optional) Deklarieren Sie die Clientfunktion. Clientfunktionen helfen Ressourcenanbietern (Resource Provider, RP) wie der obigen Web-API, zu erkennen, ob die aufrufende Clientanwendung die Anspruchsaufforderung versteht und dann ihre Antwort entsprechend anpassen kann. Diese Funktion kann nützlich sein, wenn nicht alle API-Clients Anspruchsaufforderungen bewältigen können und einige ältere Clients weiterhin eine andere Antwort erwarten. Weitere Informationen finden Sie im Abschnitt [Clientfunktionen](claims-challenge.md#client-capabilities).

## <a name="caveats-and-recommendations"></a>Auflagen und Empfehlungen

Verwenden Sie keine Hartcodierung für die Authentifizierungskontextwerte in Ihrer App. Authentifizierungskontexte sollten von Apps [mithilfe von MS Graph-Aufrufen](/graph/api/resources/authenticationcontextclassreference) gelesen und angewendet werden. Diese Vorgehensweise ist für [mehrinstanzenfähige Anwendungen](howto-convert-app-to-be-multi-tenant.md) von entscheidender Bedeutung. Die Werte für Authentifizierungskontext variieren zwischen Azure AD-Mandanten und sind in der kostenlosen Edition von Azure AD nicht verfügbar. Weitere Informationen dazu, wie Authentifizierungskontext von Apps in ihrem Code abgefragt, festgelegt und verwendet werden soll, finden Sie im Codebeispiel unter [Verwenden von Authentifizierungskontext für bedingten Zugriff zum Ausführen einer Step-up-Authentifizierung](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Englisch). 

Verwenden Sie keinen Authentifizierungskontext, wenn die App selbst Ziel von Richtlinien für bedingten Zugriff sein soll. Das Feature funktioniert am besten, wenn Teile der Anwendung erfordern, dass der Benutzer striktere Authentifizierungsanforderungen erfüllt.

## <a name="next-steps"></a>Nächste Schritte

- [Differenzierter bedingter Zugriff für vertrauliche Daten und Aktionen (Blog)](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
- [Zero Trust mit der Microsoft Identity Platform](/security/zero-trust/identity-developer)
- [Erstellen Zero Trust-bereiter Apps mit der Microsoft Identity Platform](/security/zero-trust/identity-developer)
- [Verwenden des Authentifizierungskontexts für bedingten Zugriff zum Durchführen einer Step\-up\-Authentifizierung für Vorgänge mit hohen Berechtigungen in einer Web-App](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md)
- [Verwenden von Authentifizierungskontext für bedingten Zugriff zum Ausführen einer Step-up-Authentifizierung für Vorgänge mit hohen Berechtigungen in einer Web-API (Englisch)](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)
- [Authentifizierungskontext für bedingten Zugriff](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)
- [authenticationContextClassReference-Ressourcentyp – MS Graph](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)
- [Anspruchsaufforderung, Anspruchsanforderung und Clientfunktionen in Microsoft Identity Platform](claims-challenge.md)
- [Verwenden von Authentifizierungskontext mit Microsoft Information Protection und SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [Authentifizierungsflows und Anwendungsszenarien](authentication-flows-app-scenarios.md)
- [Verwenden von CAE-fähigen APIs in Ihren Anwendungen](app-resilience-continuous-access-evaluation.md)
