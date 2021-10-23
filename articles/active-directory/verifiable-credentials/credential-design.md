---
title: 'Gewusst wie: Anpassen Ihrer Azure Active Directory-Nachweise (Vorschau)'
description: In diesem Artikel wird veranschaulicht, wie Sie eigene benutzerdefinierte Nachweise erstellen.
services: active-directory
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1a42387be183d1fa2263e609bcbfed5f388e901c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004082"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Gewusst wie: Anpassen Ihrer Nachweise (Vorschau)

Nachweise bestehen aus zwei Komponenten: Regel- und Anzeigedateien. Die Regeldatei bestimmt, was vom Benutzer bereitgestellt werden muss, bevor er einen Nachweis erhält. Mit der Anzeigedatei wird das Branding des Nachweises und das Format der Ansprüche gesteuert. In diesem Leitfaden wird beschrieben, wie Sie die beiden Dateien so ändern, dass die Anforderungen Ihrer Organisation erfüllt sind. 

> [!IMPORTANT]
> Azure Active Directory-Nachweise befinden sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Regeldatei: Anforderungen für den Benutzer

Die Regeldatei ist eine einfache JSON-Datei, in der wichtige Eigenschaften der Nachweise beschrieben sind. Insbesondere wird beschrieben, wie Ansprüche verwendet werden, um Ihre Nachweise mit Daten zu füllen.

Derzeit sind drei Eingabetypen verfügbar, die in der Regeldatei konfiguriert werden können. Diese Typen werden vom Dienst für die Ausstellung der Nachweise genutzt, um Ansprüche in einen Nachweis einzufügen und diese Informationen anhand Ihrer DID zu bestätigen. Hier sind die drei Typen und die zugehörigen Erklärungen angegeben:

- ID-Token
- Nachweise per verifizierbarer Bereitstellung
- Selbst bestätigte Ansprüche

**ID-Token:** In der Beispiel-App und im Tutorial wird das ID-Token verwendet. Wenn diese Option konfiguriert ist, müssen Sie einen Open ID Connect-URI für die Konfiguration angeben und die Ansprüche einbinden, die in den Nachweisen enthalten sein sollen. Der Benutzer wird aufgefordert, sich zur Erfüllung dieser Anforderung bei der Authenticator-App anzumelden und die zugehörigen Ansprüche aus seinem Konto hinzuzufügen. 

**Nachweise:** Das Endergebnis eines Ausstellungsvorgangs ist die Erstellung eines Nachweises. Es kann aber auch vorkommen, dass Sie den Benutzer um die Bereitstellung von Nachweisen bitten, um die Ausstellung eines entsprechenden Nachweises zu ermöglichen. Die Regeldatei kann bestimmte Ansprüche aus den angegebenen Nachweisen verwenden und in den neu ausgestellten Nachweis Ihrer Organisation einfügen. 

**Selbst bestätigte Ansprüche:** Wenn diese Option ausgewählt wird, kann der Benutzer Informationen direkt in die Authenticator-App eingeben. Derzeit werden nur Zeichenfolgen als Eingabe für selbst bestätigte Ansprüche unterstützt. 

![Ausführliche Ansicht der Karte für einen Nachweis](media/credential-design/issuance-doc.png) 

**Statische Ansprüche:** Es ist auch möglich, in der Regeldatei einen statischen Anspruch zu deklarieren. Diese Eingabe stammt aber nicht vom Benutzer. Der Aussteller definiert einen statischen Anspruch in der Regeldatei. Dieser sieht wie jeder andere Anspruch im Nachweis aus. Fügen Sie nach „vc.type“ einfach „credentialSubject“ ein, und deklarieren Sie das Attribut und den Anspruch. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Eingabetyp: ID-Token

Um das ID-Token als Eingabe abzurufen, muss mit der Regeldatei der bekannte Endpunkt des mit OIDC kompatiblen Identitätssystems konfiguriert werden. In diesem System müssen Sie eine Anwendung mit den richtigen Informationen registrieren. Weitere Informationen hierzu finden Sie unter [Beispiele für die Kommunikation des Ausstellerdiensts](issuer-openid.md). Außerdem muss die „client_id“ in die Regeldatei eingefügt werden, und es muss ein Bereichsparameter mit den richtigen Bereichen vorhanden sein. Azure Active Directory benötigt beispielsweise den E-Mail-Bereich, wenn Sie einen E-Mail-Anspruch im ID-Token zurückgeben möchten.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| `attestations.idTokens` | Ein Array mit OpenID Connect-Identitätsanbietern, die für die Ermittlung von Benutzerinformationen unterstützt werden. |
| `...mapping` | Ein Objekt, mit dem beschrieben wird, wie Ansprüche in den einzelnen ID-Token den Attributen im sich ergebenden Nachweis zugeordnet werden. |
| `...mapping.{attribute-name}` | Das Attribut, das im sich ergebenden Nachweis aufgefüllt werden soll. |
| `...mapping.{attribute-name}.claim` | Der Anspruch in ID-Token, dessen Wert zum Einfügen des Attributs verwendet werden soll. |
| `...configuration` | Der Speicherort des Konfigurationsdokuments Ihres Identitätsanbieters. Diese URL muss dem [OpenID Connect-Standard für Identitätsanbieter-Metadaten](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata) entsprechen. Das Konfigurationsdokument muss die Felder `issuer`, `authorization_endpoint`, `token_endpoint` und `jwks_uri` enthalten. |
| `...client_id` | Die Client-ID, die während des Registrierungsvorgangs für den Client abgerufen wird. |
| `...scope` | Eine durch Leerzeichen getrennte Liste mit Bereichen, die vom IdP benötigt werden, damit die richtigen Ansprüche im ID-Token zurückgegeben werden können. |
| `...redirect_uri` | Hierfür muss immer der Wert `vcclient://openid/` verwendet werden. |
| `validityInterval` | Ein Zeitraum (in Sekunden), der für die Lebensdauer Ihrer Nachweise steht. Nach Ablauf dieses Zeitraums ist der Nachweis nicht mehr gültig. Wenn dieser Wert weggelassen wird, bedeutet dies, dass der Nachweis so lange gültig bleibt, bis er explizit widerrufen wird. |
| `vc.type` | Ein Array mit Zeichenfolgen zum Angeben des Schemas, dessen Vorgaben von Ihrem Nachweis erfüllt werden. Der Abschnitt unten enthält hierzu ausführlichere Informationen. |

### <a name="vctype-choose-credential-types"></a>vc.type: Auswählen von Nachweistypen 

Für alle Nachweise muss der „Typ“ in der zugehörigen Regeldatei deklariert werden. Anhand des Typs können Ihre Nachweise von Nachweisen unterschieden werden, die von anderen Organisationen ausgestellt werden. Darüber hinaus wird hiermit sichergestellt, dass die Interoperabilität zwischen Ausstellern und Überprüfern gegeben ist. Beim Angeben des Nachweistyps müssen Sie einen oder mehrere Typen auswählen, die für die jeweiligen Nachweise geeignet sind. Jeder Typ wird als eindeutige Zeichenfolge angegeben. In vielen Fällen wird ein URI genutzt, um die globale Eindeutigkeit sicherzustellen. Der URI muss nicht adressierbar sein, sondern wird als Zeichenfolge behandelt. 

Für einen Diplom-Nachweis der Contoso University können beispielsweise die folgenden Typen deklariert werden:

| Typ | Zweck |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Deklariert, dass von der Contoso University ausgestellte Diplome Attribute enthalten, die mit dem Objekt `EducationaCredential` von „schema.org“ definiert werden. |
| `https://schemas.ed.gov/universityDiploma2020` | Deklariert, dass von der Contoso University ausgestellte Diplome Attribute enthalten, die vom US-Bildungsministerium definiert werden. |
| `https://schemas.contoso.edu/diploma2020` | Deklariert, dass von der Contoso University ausgestellte Diplome Attribute enthalten, die von der Contoso University definiert werden. |

Durch das Deklarieren aller drei Typen können die Diplome der Contoso University verwendet werden, um unterschiedliche Anforderungen von Überprüfern zu erfüllen. Eine Bank kann von einem Benutzer mehrere `EducationCredential`-Elemente anfordern, und das Diplom kann genutzt werden, um die Anforderung zu erfüllen. Wenn die Contoso University Alumni Association einen Nachweis vom Typ `https://schemas.contoso.edu/diploma2020` anfordert, kann diese Anforderung mit dem Diplom ebenfalls erfüllt werden.

Zur Sicherstellung der Interoperabilität für Ihre Nachweise empfehlen wir Ihnen, eng mit den entsprechenden Organisationen zusammenzuarbeiten, um Typen, Schemas und URIs für Nachweise zur Verwendung in Ihrer Branche zu definieren. Viele Brancheneinrichtungen leisten Hilfe in Bezug auf den Aufbau offizieller Dokumente, die entsprechend abgewandelt werden können, um den Inhalt von Nachweisen zu definieren. Darüber hinaus sollten Sie auch eng mit den Überprüfern Ihrer Nachweise zusammenarbeiten, um sich damit vertraut zu machen, wie diese Ihre Nachweise anfordern und nutzen möchten.

## <a name="input-type-verifiable-credential"></a>Eingabetyp: Nachweis

>[!NOTE]
>Für Regeldateien, bei denen nach einem Nachweis gefragt wird, wird beim Anfordern von Nachweisen nicht das Format für den Austausch der Angaben genutzt. Dieser Vorgang wird später aktualisiert, wenn der Ausstellerdienst das Standardmanifest für Nachweise unterstützt. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| `attestations.presentations` | Ein Array mit Nachweisen, die als Eingaben angefordert werden. |
| `...mapping` | Ein Objekt, mit dem beschrieben wird, wie Ansprüche in den einzelnen angegebenen Nachweisen den Attributen in den sich ergebenden Nachweisen zugeordnet werden. |
| `...mapping.{attribute-name}` | Das Attribut, das im sich ergebenden Nachweis eingefügt werden soll. |
| `...mapping.{attribute-name}.claim` | Der Anspruch im Nachweis, dessen Wert zum Einfügen des Attributs verwendet werden soll. |
| `...mapping.{attribute-name}.indexed` | Pro Nachweis kann jeweils nur ein Anspruch zur Speicherung für den Widerruf aktiviert werden. Weitere Informationen finden Sie im [Artikel zum Widerrufen eines Nachweises](how-to-issuer-revoke.md). |
| `credentialType` | „credentialType“ des Nachweises, um dessen Bereitstellung Sie den Benutzer bitten. |
| `contracts` | Der URI des Vertrags im Portal für den Nachweis-Dienst. |
| `issuers.iss` | Die DID des Ausstellers für den Nachweis, der vom Benutzer verlangt wird. |
| `validityInterval` | Ein Zeitraum (in Sekunden), der für die Lebensdauer Ihrer Nachweise steht. Nach Ablauf dieses Zeitraums ist der Nachweis nicht mehr gültig. Wenn dieser Wert weggelassen wird, bedeutet dies, dass der Nachweis so lange gültig bleibt, bis er explizit widerrufen wird. |
| `vc.type` | Ein Array mit Zeichenfolgen zum Angeben des Schemas, dessen Vorgaben von Ihrem Nachweis erfüllt werden. |


## <a name="input-type-self-attested-claims"></a>Eingabetyp: Selbst bestätigte Ansprüche

Während des Ausstellungsvorgangs kann der Benutzer aufgefordert werden, einige selbst bestätigte Informationen einzugeben. Derzeit ist hierbei nur der Eingabetyp „Zeichenfolge“ möglich. 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| `attestations.selfIssued` | Ein Array mit selbst ausgestellten Ansprüchen, für die eine Eingabe des Benutzers erforderlich ist. |
| `...mapping` | Ein Objekt, mit dem beschrieben wird, wie selbst bestätigte Ansprüche den Attributen im sich ergebenden Nachweis zugeordnet werden. |
| `...mapping.alias` | Das Attribut, das im sich ergebenden Nachweis aufgefüllt werden soll. |
| `...mapping.alias.claim` | Der Anspruch im Nachweis, dessen Wert zum Einfügen des Attributs verwendet werden soll. |
| `validityInterval` | Ein Zeitraum (in Sekunden), der für die Lebensdauer Ihrer Nachweise steht. Nach Ablauf dieses Zeitraums ist der Nachweis nicht mehr gültig. Wenn dieser Wert weggelassen wird, bedeutet dies, dass der Nachweis so lange gültig bleibt, bis er explizit widerrufen wird. |
| `vc.type` | Ein Array mit Zeichenfolgen zum Angeben des Schemas, dessen Vorgaben von Ihrem Nachweis erfüllt werden. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Anzeigedatei: Nachweise in Microsoft Authenticator

Nachweise verfügen über eine begrenzte Zahl von Optionen, die Sie nutzen können, um Ihr Branding darzustellen. Dieser Artikel enthält eine Anleitung zum Anpassen Ihrer Nachweise sowie eine Beschreibung der bewährten Methoden zum Entwerfen von Nachweisen, die nach dem Ausstellen für Benutzer gut aussehen.

Für Benutzer ausgestellte Nachweise werden in Microsoft Authenticator als Karten angezeigt. Als Administrator können Sie für die Karte die Farbe, ein Symbol und Textzeichenfolgen so auswählen, wie dies am besten zur Marke Ihrer Organisation passt.

![Dokumentation der Ausstellung](media/credential-design/detailed-view.png) 

Karten enthalten darüber hinaus anpassbare Felder, mit denen Sie Benutzer über den Zweck der Karte, die darin enthaltenen Attribute und andere Dinge informieren können.

## <a name="create-a-credential-display-file"></a>Erstellen einer Anzeigedatei für Nachweise

Ähnlich wie bei der Regeldatei handelt es sich bei der Anzeigedatei um eine einfache JSON-Datei, mit der beschrieben wird, wie der Inhalt Ihrer Nachweise in der Microsoft Authenticator-App angezeigt werden soll. 

>[!NOTE]
> Derzeit wird dieses Anzeigemodell nur von Microsoft Authenticator verwendet.

Die Anzeigedatei ist wie unten angegeben aufgebaut.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| `locale` | Gibt die Sprache des Nachweises an. Für die zukünftige Verwendung reserviert. | 
| `card.title` | Zeigt den Typ des Nachweises für den Benutzer an. Die empfohlene maximale Länge beträgt 25 Zeichen. | 
| `card.issuedBy` | Zeigt den Namen der ausstellenden Organisation für den Benutzer an. Die empfohlene maximale Länge beträgt 40 Zeichen. |
| `card.backgroundColor` | Bestimmt die Hintergrundfarbe der Karte im hexadezimalen Format. Auf alle Karten wird ein leichter Gradientwert angewendet. |
| `card.textColor` | Bestimmt die Textfarbe der Karte im hexadezimalen Format. Hierbei ist die Verwendung von Schwarzweiß zu empfehlen. |
| `card.logo` | Ein Logo, das auf der Karte angezeigt wird. Die angegebene URL muss öffentlich adressierbar sein. Unabhängig von der Größe des Smartphones wird eine maximale Höhe von 36 px und eine maximale Breite von 100 px empfohlen. Empfohlen wird PNG mit transparentem Hintergrund. | 
| `card.description` | Ergänzender Text, der neben jeder Karte angezeigt wird. Kann für beliebige Zwecke genutzt werden. Als maximale Länge werden 100 Zeichen empfohlen. |
| `consent.title` | Ergänzender Text, der beim Ausstellen einer Karte angezeigt wird. Wird zum Angeben von Details zum Ausstellungsprozess verwendet. Als Länge werden 100 Zeichen empfohlen. |
| `consent.instructions` | Ergänzender Text, der beim Ausstellen einer Karte angezeigt wird. Wird zum Angeben von Details zum Ausstellungsprozess verwendet. Als Länge werden 100 Zeichen empfohlen. |
| `claims` | Ermöglicht das Angeben von Bezeichnungen für Attribute, die in den einzelnen Nachweisen enthalten sind. |
| `claims.{attribute}` | Gibt das Attribut des Nachweises an, für das die Bezeichnung gilt. |
| `claims.{attribute}.type` | Gibt den Attributtyp an. Derzeit wird nur der Typ „Zeichenfolge“ unterstützt. |
| `claims.{attribute}.label` | Der Wert, der als Bezeichnung für das Attribut verwendet werden soll, das in Authenticator angezeigt wird. Diese Bezeichnung unterscheidet sich ggf. von der Bezeichnung, die in der Regeldatei verwendet wurde. Die empfohlene maximale Länge beträgt 40 Zeichen. |

>[!NOTE]
  >Wenn ein Anspruch in der Regeldatei enthalten ist und in der Anzeigedatei dann weggelassen wird, kommt es zu unterschiedlichem Verhalten. Unter iOS wird der Anspruch im Abschnitt „Details“ (siehe obige Abbildung) nicht angezeigt, während dies unter Android der Fall ist.  

## <a name="next-steps"></a>Nächste Schritte

Sie verfügen nun über mehr Informationen zum Entwerfen von Nachweisen und darüber, wie Sie zur Erfüllung Ihrer Anforderungen eigene Nachweise erstellen können.

- [Beispiele für die Kommunikation des Ausstellerdiensts](issuer-openid.md)
