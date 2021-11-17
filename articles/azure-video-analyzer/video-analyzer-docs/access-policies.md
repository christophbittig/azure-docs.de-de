---
title: Azure Video Analyzer-Zugriffsrichtlinien
description: Dieser Artikel erklärt, wie Azure Video Analyzer JWT-Tokens in Zugriffsrichtlinien verwendet, um Videos zu sichern.
ms.topic: reference
ms.date: 11/04/2021
ms.openlocfilehash: 9d9a0f4db83d132fae5bafb2a25d25075296b4ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486138"
---
# <a name="access-policies"></a>Zugriffsrichtlinien

Die Zugriffsrichtlinien bestimmen die Berechtigungen und die Dauer des Zugriffs auf eine bestimmten Azure Video Analyzer-Videoressource. Diese Zugriffsrichtlinien ermöglichen eine größere Kontrolle und Flexibilität, indem sie JWT-Tokens von 3-Anbietern (Nicht-AAD-Clients) erlauben, die Autorisierung für Client-APIs zu ermöglichen: 

- Zugriff auf Video-Metadaten. 
- Zugriff auf Video-Streaming. 

## <a name="access-policy-definition"></a>Definition der Azure-Zugriffsrichtlinie

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> Es wird nur ein Tastentyp benötigt. 

### <a name="roles"></a>Rollen

Derzeit wird nur die Leserrolle unterstützt.

### <a name="issuer-matching-rules"></a>Übereinstimmungsregeln für Zertifikatsaussteller

In der Richtlinie können mehrere Ausgaben angegeben werden, im Token kann ein einzelner Aussteller angegeben werden.  Der Aussteller stimmt überein, wenn der Token-Aussteller sich unter den in der Richtlinie angegebenen Ausstellern befindet.

### <a name="audience-matching-rules"></a>Regeln für den Abgleich mit der Zielgruppe

Wenn der Zielgruppenwert ${System.Runtime.BaseResourceUrlPattern} für die Videoressource ist, muss die im JWT-Token angegebene Zielgruppe mit der URL der Basisressource übereinstimmen. Wenn nicht, dann muss die Token-Zielgruppe mit der Zielgruppe aus der Zugriffsrichtlinie übereinstimmen.

### <a name="claims-matching-rules"></a>Regeln für den Abgleich von Ansprüchen

In der Zugriffsrichtlinie und im JWT-Token können mehrere Ansprüche angegeben werden.  Alle Ansprüche aus einer Zugriffsrichtlinie müssen im Token enthalten sein, um die Validierung zu bestehen. Das JWT-Token kann jedoch zusätzliche Ansprüche enthalten, die nicht in der Zugriffsrichtlinie aufgeführt sind.

### <a name="keys"></a>Schlüssel

Es werden zwei Arten von Schlüsseln unterstützt, nämlich die RSA- und die ECC-Schlüssel.

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg – Algorithmus.  Kann 256, 384 oder 512 sein 
* kid – Schlüssel-ID
* n – Modulus
* e – Öffentlicher Exponent 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg – Algorithmus.  Kann 256, 384 oder 512 sein
* kid – Schlüssel-ID
* x – Koordinatenwert.
* y – Koordinatenwert.

### <a name="token-validation-process"></a>Token-Validierungsvorgang

Kunden müssen ihre eigenen JWT-Tokens erstellen und sie werden anhand der folgenden Methode validiert:

- Aus der Liste der Richtlinien, die mit der Schlüssel-ID übereinstimmen, validieren wir:
  - Tokensignatur
  - Tokenablauf
  - Aussteller
  - Zielgruppe
  - Zusätzliche Ansprüche

### <a name="policy-audience-and-token-matching-examples"></a>Beispiele für die Azure-Richtlinien-Zielgruppe und Token-Abgleich:

| **Richtlinien-Zielgruppe**                      | Angeforderte URL                         | Token-URL                            | Ergebnis |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| (Beliebiges Literal)                            | (JEGLICHES)                                 | (Übereinstimmung)                              | Erteilen  |
| (Beliebiges Literal)                            | (JEGLICHES)                                 | (Keine Übereinstimmung)                          | Verweigern   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | Erteilen  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | Verweigern   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | Erteilen  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | Erteilen  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | Erteilen  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | Erteilen  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | Verweigern   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | Verweigern   |

> [!NOTE]  
> Azure Video Analyzer unterstützt maximal 20 Richtlinien.  ${System.Runtime.BaseResourceUrlPattern} ermöglicht eine größere Flexibilität beim Zugriff auf bestimmte Ressourcen durch die Verwendung einer Zugriffsrichtlinie und mehrerer Token.  Diese Token ermöglichen wiederum den Zugriff auf verschiedene Azure Video Analyzer-Ressourcen, je nach Zielgruppe. 

## <a name="creating-a-token"></a>Erstellen eines Tokens

In diesem Abschnitt erstellen wir ein JWT-Token, das wir später im Artikel verwenden.  Wir verwenden eine Beispielanwendung, die das JWT-Token erstellt und Ihnen alle Felder zur Verfügung stellt, die zum Erstellen der Zugriffsrichtlinie erforderlich sind.

> [!NOTE] 
> Wenn Sie mit dem Generieren eines JWT-Tokens auf der Grundlage eines RSA- oder ECC-Zertifikats vertraut sind, können Sie diesen Abschnitt überspringen.

1. Klonen Sie das [Repository mit AVA-C#-Beispielen](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp). Wechseln Sie dann zum JWTTokenIssuer-Anwendungsordner *src/jwt-token-issuer*, und suchen Sie die JWTTokenIssuer-Anwendung.
2. Öffnen Sie Visual Studio Code, und wechseln Sie in den Ordner, in den Sie die Anwendung JWTTokenIssuer heruntergeladen haben. Dieser Ordner sollte die *\*CSPROJ*-Datei enthalten.
3. Wechseln Sie im Explorer-Bereich zur Datei *program.cs*.
4. Ändern Sie in Zeile 77 die Zielgruppe in Ihren Video Analyzer-Endpunkt, gefolgt von „/videos/\*“, sodass sie wie folgt aussieht:

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Den Video Analyzer-Endpunkt finden Sie im Übersichtsabschnitt der Video Analyzer-Ressource im Azure-Portal.

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="Screenshot: Endpunkt des Player-Widgets":::
    
5. Ändern Sie in Zeile 78 den Aussteller in den Ausstellerwert Ihres Zertifikats. Beispiel: `https://contoso.com`
6. Speichern Sie die Datei .    

   > [!NOTE]
   > Möglicherweise wird die Meldung `Required assets to build and debug are missing from 'jwt token issuer'. Add them?` angezeigt. Wählen Sie `Yes` aus.
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="Screenshot: Eingabeaufforderung für erforderliche Ressource in Visual Studio Code":::
   
7. Öffnen Sie ein Eingabeaufforderungsfenster, und wechseln Sie in den Ordner mit den JWTTokenIssuer-Dateien. Führen Sie die folgenden beiden Befehle aus: `dotnet build`, gefolgt von `dotnet run`. Wenn Sie über die C#-Erweiterung für Visual Studio Code verfügen, können Sie auch F5 auswählen, um die JWTTokenIssuer-Anwendung auszuführen.

Die Anwendung wird erstellt und dann ausgeführt. Nach dem Erstellen erstellt es ein selbstsigniertes Zertifikat und generiert die JWT-Tokeninformationen aus diesem Zertifikat. Sie können auch die Datei JWTTokenIssuer.exe ausführen, die sich im Debugordner des Verzeichnisses befindet, aus dem JWTTokenIssuer erstellt wird. Der Vorteil der Ausführung der Anwendung besteht darin, dass Sie Eingabeoptionen wie folgt angeben können:

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

JWTTokenIssuer erstellt das JWT-Token und die folgenden erforderlichen Komponenten:

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

Stellen Sie sicher, dass Sie diese Werte zur späteren Verwendung kopieren.


## <a name="creating-an-access-policy"></a>Erstellen einer Azure-Zugriffs-Richtlinie

Es gibt zwei Möglichkeiten, eine Zugriffsrichtlinie zu erstellen.

### <a name="in-the-azure-portal"></a>Im Azure-Portal

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrer Ressourcengruppe, in der sich Ihr Video Analyzer-Konto befindet.
1. Wählen Sie die Azure Video Analyzer-Ressource aus.
1. Wählen Sie unter **Video Analyzer** die Option **Zugriffsrichtlinien** aus.

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="Player-Widget: Portal-Zugriffsrichtlinien":::
   
1. Wählen Sie **Neu** aus, und geben Sie die folgenden Informationen ein:

   > [!NOTE] 
   > Diese Werte entstammen der JWTTokenIssuer-Anwendung, die im vorherigen Schritt erstellt wurde.

   - Name der Zugriffsrichtlinie – beliebiger Name

   - Aussteller – muss mit dem JWT-Token-Aussteller übereinstimmen 

   - Zielgruppe: Zielgruppe für das JWT-Token – `${System.Runtime.BaseResourceUrlPattern}` ist die Standardeinstellung.

   - Schlüsseltyp: RSA 

   - Algorithmus - die unterstützten Werte sind RS256, RS384, RS512

   - Schlüssel-ID: aus Ihrem Zertifikat generiert. Weitere Informationen finden Sie unter [Erstellen eines Tokens](#creating-a-token).

   - RSA-Schlüsselmodulus: Dieser Wert wird aus Ihrem Zertifikat generiert. Weitere Informationen finden Sie unter [Erstellen eines Tokens](#creating-a-token).

   - RSA-Schlüsselexponent: Dieser Wert wird aus Ihrem Zertifikat generiert. Weitere Informationen finden Sie unter [Erstellen eines Tokens](#creating-a-token).

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="Player-Widget - Portal-Zugriffsrichtlinien"::: 
   
1. Wählen Sie **Speichern** aus.
### <a name="create-access-policy-via-api"></a>Erstellen Sie eine Zugriffsrichtlinie via API

Siehe Azure Resource Manager (ARM) API 

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](overview.md)
