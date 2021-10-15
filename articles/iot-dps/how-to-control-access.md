---
title: Sicherheitsendpunkte im Microsoft Azure IoT Device Provisioning-Dienst
description: Konzepte für die Steuerung des Zugriffs auf IoT Device Provisioning Service (DPS) für Back-End-Apps. Enthält Informationen zu Sicherheitstoken.
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/22/2021
ms.author: v-stharr
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: dc33bf659785419619afe4f393a8a1ed09142452
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272527"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Steuern des Zugriffs auf den Azure IoT Hub Device Provisioning-Dienst

In diesem Artikel werden die verfügbaren Optionen für den Schutz Ihres IoT Device Provisioning-Diensts beschrieben. Der Bereitstellungsdienst (Provisioning Service) verwendet *Authentifizierung* und *Berechtigungen*, um Zugriff auf die einzelnen Endpunkte zu gewähren. Berechtigungen ermöglichen es dem Authentifizierungsprozess, den Zugriff auf eine Dienstinstanz auf der Grundlage von Funktionalität einzuschränken.

In diesem Artikel wird Folgendes erläutert:

* Der Authentifizierungsprozess und die Token, mit denen der Bereitstellungsdienst Berechtigungen für die [Dienste-REST-API und REST-API für Device Messaging](/rest/api/iot-dps/) überprüft.

* Die verschiedenen Berechtigungen, die Sie einer Back-End-App für den Zugriff auf die Dienst-API gewähren können.

## <a name="authentication"></a>Authentifizierung

Die Geräte-API unterstützt die schlüsselbasierte und X.509-zertifikatbasierte Geräteauthentifizierung.  

Die Dienst-API unterstützt die schlüsselbasierte Authentifizierung für Back-End-Apps.  

Bei der schlüsselbasierten Authentifizierung verwendet der Device Provisioning-Dienst Sicherheitstoken zum Authentifizieren von Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. Darüber hinaus sind Sicherheitstoken im Hinblick auf Gültigkeitszeitraum und Bereich beschränkt. Azure IoT Device Provisioning-SDKs generieren Token automatisch (also ohne eine spezielle Konfiguration).  

In einigen Fällen müssen Sie die REST-APIs des HTTP Device Provisioning-Diensts möglicherweise direkt (ohne die SDKs) verwenden. In den folgenden Abschnitten wird beschrieben, wie Sie sich bei den REST-APIs direkt authentifizieren können.

## <a name="device-api-authentication"></a>Geräte-API-Authentifizierung

Die [Geräte-API](/rest/api/iot-dps/device/runtime-registration) wird von Geräten für die Bestätigung beim Device Provisioning-Dienst verwendet, um eine Verbindung zu erhalten.

>[!NOTE]
>Wenn Sie eine authentifizierte Verbindung erhalten möchten, müssen Sie Geräte zuerst über eine Registrierung beim Device Provisioning-Dienst registrieren. Mithilfe der Dienst-AP können Sie ein Gerät über eine Registrierung programmgesteuert registrieren.

Ein Gerät muss sich im Rahmen des Bereitstellungsprozesses bei der Geräte-API authentifizieren. Die Methode, die ein Gerät zur Authentifizierung verwendet, wird definiert, wenn Sie eine Registrierungsgruppe oder eine individuelle Registrierung einrichten. Unabhängig von der Authentifizierungsmethode muss das Gerät einen HTTPS-PUT für die folgende URL ausstellen, um sich selbst bereitzustellen.

```http
    https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01
```
Bei der Nutzung von schlüsselbasierter Authentifizierung wird im HTTP-Anforderungsheader  **Authorization**  ein Sicherheitstoken im folgenden Format übergeben:

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

### <a name="security-token-structure-for-key-based-authentication"></a>Sicherheitstokenstruktur für die schlüsselbasierte Authentifizierung

Das Sicherheitstoken wird im HTTP-Anforderungsheader  **Authorization**  im folgenden Format übergeben:

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

Die folgenden Werte werden erwartet:

| Wert  | BESCHREIBUNG |
|:-------|:------------|
| `{signature}`  | Eine HMAC-SHA256-Signaturzeichenfolge der Form: `{URL-encoded-resourceURI} + "\n" + expiry`. **Wichtig**: Der Schlüssel wird aus Base64 decodiert und als Schlüssel für die HMAC-SHA256-Berechnung (Hashed Message Authentication Code, Hashbasierter Nachrichtenauthentifizierungscode) verwendet. |
| `{expiry}`  | UTF8-Zeichenfolge, dargestellt als die Anzahl von Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC.  |
| `{URL-encoded-resourceURI}`   |URL-Codierung von `{ID_Scope}/registrations/{registration_id}` in Kleinbuchstaben |
| `{policyName}`  | Bei der Geräte-API ist diese Richtlinie immer „Registrierung“. |

Der folgende Python-Codeausschnitt zeigt die Funktion  `generate_sas_token` , die das Token aus den Eingaben  `uri`, `key`, `policy_name` und `expiry` für eine individuelle Registrierung mithilfe eines Authentifizierungstyps mit symmetrischem Schlüssel berechnet.

```python

from base64 import b64encode, b64decode, encode 
from hashlib import sha256 
from time import time 
from urllib.parse import quote_plus, urlencode 
from hmac import HMAC 

 def generate_sas_token(uri, key, policy_name, expiry=3600): 
    ttl = time() + expiry 
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl)) 
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest()) 

    rawtoken = { 
        'sr' :  uri, 
        'sig': signature, 
        'se' : str(int(ttl)), 
        'skn' : policy_name 
    } 

    return 'SharedAccessSignature ' + urlencode(rawtoken) 

print(generate_sas_token("myIdScope/registrations/mydeviceregistrationid", "00mysymmetrickey", "registration"))

```

Das Ergebnis sollte ähnlich wie die folgende Ausgabe aussehen:

```bash

SharedAccessSignature sr=myIdScope%2Fregistrations%2Fmydeviceregistrationid&sig=SDpdbUNk%2F1DSjEpeb29BLVe6gRDZI7T41Y4BPsHHoUg%3D&se=1630175722&skn=registration 
```

Das folgende Beispiel zeigt, wie die Shared Access Signature (SAS) anschließend zur Authentifizierung mit der Geräte-API verwendet wird.  

```python

curl -L -i -X PUT -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -H 'Authorization: [token]' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

Wenn Sie eine auf einem symmetrischen Schlüssel basierende Registrierungsgruppe verwenden, müssen Sie zuerst mithilfe des Registrierungsgruppenschlüssels einen `device symmetric`-Schlüssel generieren. Verwenden Sie den primären oder sekundären Schlüssel der Registrierungsgruppe, um einen HMAC-SHA256 der Registrierungs-ID für das Gerät zu berechnen. Das Ergebnis wird dann in das Base64-Format konvertiert, um den abgeleiteten Geräteschlüssel abzurufen. Informationen zum Anzeigen von Codebeispielen finden Sie unter [Bereitstellen von Geräten mit symmetrischen Schlüsseln für Registrierungsgruppen](how-to-legacy-device-symm-key.md?tabs=linux%22%20%5Cl%20%22derive-a-device-key). Nachdem der symmetrische Schlüssel des Geräts abgeleitet wurde, können Sie es anhand der vorstehenden Beispiele registrieren.

>[!WARNING]
>Wenn Sie vermeiden möchten, dass der Gruppenhauptschlüssel in Ihren Gerätecode einbezogen wird, sollte der Vorgang zum Ableiten des Geräteschlüssels außerhalb des Geräts durchgeführt werden.

### <a name="certificate-based-authentication"></a>Zertifikatbasierte Authentifizierung 

Wenn Sie eine individuelle Registrierung oder eine Registrierungsgruppe für die X.509-zertifikatbasierte Authentifizierung eingerichtet haben, muss das Gerät sein ausgestelltes X.509-Zertifikat verwenden, um die Geräte-API zu bestätigen. In den folgenden Artikeln erfahren Sie, wie Sie die Registrierung einrichten und das Gerätezertifikat generieren können.

* Schnellstart - [Simuliertes X.509-Gerät für Azure IoT Hub bereitstellen](quick-create-simulated-device-x509.md)

* Schnellstart - [Registrieren Sie X.509-Geräte bei Azure Gerätebereitstellung-Service](quick-enroll-device-x509.md)

Nachdem die Registrierung eingerichtet und das Gerätezertifikat ausgestellt wurde, wird im folgenden Beispiel gezeigt, wie Sie sich mit dem X.509-Zertifikat des Geräts bei der Geräte-API authentifizieren können.

```bash

curl -L -i -X PUT –cert ./[device_cert].pem –key ./[device_cert_private_key].pem -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

## <a name="service-api-authentication"></a>Dienst-API-Authentifizierung

Die [Geräte-API](/rest/api/iot-dps/service/device-registration-state) wird zum Abrufen des Registrierungsstatus und Entfernen von Geräteregistrierungen verwendet. Außerdem wird der Dienst von Back-End-Apps zum programmgesteuerten Verwalten von [einzelnen Gruppen](/rest/api/iot-dps/service/individual-enrollment) und [Registrierungsgruppen](/rest/api/iot-dps/service/enrollment-group) genutzt. Die Dienst-API unterstützt die schlüsselbasierte Authentifizierung für Back-End-Apps.  

Sie müssen entsprechende Berechtigungen für den Zugriff auf die Dienst-API-Endpunkte haben. Beispiel: Eine Back-End-App muss jede Nachricht, die sie an den Dienst sendet, mit einem Token mit Sicherheitsanmeldeinformationen versehen.

Der Azure IoT Hub Device Provisioning-Dienst gewährt Zugriff auf Endpunkte, indem er das Token anhand der SAS-Richtlinien überprüft. Sicherheitsanmeldeinformationen, beispielsweise symmetrische Schlüssel, werden niemals über eine physische Verbindung gesendet.

### <a name="access-control-and-permissions"></a>Access Control und Berechtigungen

Sie können [Berechtigungen](#device-provisioning-service-permissions) auf folgende Weise gewähren:

* **SAS-Autorisierungsrichtlinien:** SAS-Richtlinien können eine beliebige Kombination von [Berechtigungen](#device-provisioning-service-permissions) gewähren. Sie können Richtlinien im [Azure-Portal][lnk-management-portal] oder programmgesteuert mithilfe der [REST-APIs des Device Provisioning-Diensts][lnk-resource-provider-apis] definieren. Ein neu erstellter Bereitstellungsdienst verfügt über folgende Standardrichtlinie:

* **provisioningserviceowner**: Richtlinie mit sämtlichen Berechtigungen. Weitere Informationen finden Sie unter [Berechtigungen](#device-provisioning-service-permissions).

> [!NOTE]
> Der Device Provisioning-Dienst wird über Ihr Azure-Abonnement geschützt – ebenso wie alle Anbieter im [Azure Resource Manager][lnk-azure-resource-manager].

Weitere Informationen zum Erstellen und Verwenden von Sicherheitstoken finden Sie im nächsten Abschnitt.

HTTP ist das einzige unterstützte Protokoll. Zur Implementierung der Authentifizierung wird ein gültiges Token in den Anforderungsheader **Authorization** eingeschlossen.

#### <a name="example"></a>Beispiel

```bash
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Die [SDKs für den Azure IoT Device Provisioning-Dienst][lnk-sdks] generieren automatisch Token, wenn eine Verbindung mit dem Dienst hergestellt wird.

### <a name="security-tokens"></a>Sicherheitstoken

Der Device Provisioning-Dienst verwendet Sicherheitstoken zum Authentifizieren von Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. Darüber hinaus sind Sicherheitstoken im Hinblick auf Gültigkeitszeitraum und Bereich beschränkt. [SDKs für den Azure IoT Device Provisioning-Dienst][lnk-sdks] generieren Token automatisch (also ohne spezielle Konfiguration). In einigen Szenarien müssen Sie Sicherheitstoken allerdings direkt generieren und verwenden. Zu solchen Szenarien zählt auch die direkte Verwendung der HTTP-Oberfläche.

### <a name="security-token-structure"></a>Struktur von Sicherheitstoken

Sie verwenden Sicherheitstoken, um für Dienste zeitlich begrenzten Zugriff auf bestimmte Funktionen des IoT Device Provisioning-Diensts zu gewähren. Zur Autorisierung der Verbindungsherstellung mit dem Bereitstellungsdienst müssen Dienste Sicherheitstoken senden, die entweder mit einem freigegebenen Zugriffsschlüssel oder mit einem symmetrischen Schlüssel signiert sind.

Ein mit einem Schlüssel für den gemeinsamen Zugriff signiertes Token gewährt Zugriff auf alle Funktionen, die den SAS-Richtlinienberechtigungen zugeordnet sind. 

Das Sicherheitstoken weist das folgende Format auf:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier sind die erwarteten Werte:

| Wert | BESCHREIBUNG |
| --- | --- |
| {signature} |Eine HMAC-SHA256-Signaturzeichenfolge in folgendem Format: `{URL-encoded-resourceURI} + "\n" + expiry`. **Wichtig**: Der Schlüssel wird aus Base64 decodiert und als Schlüssel für die HMAC-SHA256-Berechnung verwendet.|
| {expiry} |UTF8-Zeichenfolge, dargestellt als die Anzahl von Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC. |
| {URL-encoded-resourceURI} | URL-Codierung des Ressourcen-URI (beides in Kleinbuchstaben). URI-Präfix (nach Segment) der Endpunkte, auf die mit diesem Token zugegriffen werden kann – beginnend mit dem Hostnamen des IoT Device Provisioning-Diensts (kein Protokoll). Beispiel: `mydps.azure-devices-provisioning.net`. |
| {policyName} |Der Name der gemeinsam genutzten Zugriffsrichtlinie, auf die dieses Token verweist. |

>[!NOTE]
>Das URI-Präfix wird nach Segment, nicht nach Zeichen berechnet. Beispielsweise ist `/a/b` ein Präfix für `/a/b/c`, aber nicht für `/a/bc`.

Der folgende „Node.js“-Codeausschnitt zeigt eine Funktion namens **GenerateSasToken**, die das Token aus den Eingaben `resourceUri, signingKey, policyName, expiresInMins` berechnet. In den nächsten Abschnitten wird erläutert, wie die verschiedenen Eingaben für die verschiedenen Anwendungsfälle für Token initialisiert werden.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Hier ist zum Vergleich der entsprechende Python-Code zum Generieren eines Sicherheitstokens:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Da die zeitliche Gültigkeit des Tokens auf Computern mit dem IoT Device Provisioning-Dienst überprüft wird, darf die Uhr des Computers, auf dem das Token generiert wird, nur minimal abweichen.

### <a name="use-security-tokens-from-service-components"></a>Verwenden der Sicherheitstoken von Dienstkomponenten

Dienstkomponenten können nur Sicherheitstoken mithilfe von SAS-Richtlinien generieren, die die entsprechenden Berechtigungen erteilen, wie bereits erläutert.

Hier sind die Dienstfunktionen, die für die Endpunkte verfügbar gemacht werden:

| Endpunkt | Funktionalität |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Ermöglicht Geräteregistrierungsvorgänge mit dem Device Provisioning-Dienst |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Ermöglicht Vorgänge für die Verwaltung von Geräteregistrierungsgruppen |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Ermöglicht Vorgänge zum Abrufen und Verwalten des Status von Geräteregistrierungen |


Beispielsweise würde ein Dienst, der mit einer vorab erstellten SAS-Richtlinie namens `enrollmentread` generiert wurde, ein Token mit den folgenden Parametern erstellen:

* Ressourcen-URI: `{mydps}.azure-devices-provisioning.net`
* Signaturschlüssel: einer der Schlüssel der `enrollmentread` -Richtlinie
* Richtlinienname: `enrollmentread`
* Eine Ablaufzeit

![Erstellen Sie eine gemeinsame Zugriffsrichtlinie für Ihre Gerätebereitstellung-Service-Instanz im Portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Das Ergebnis, das Lesezugriff auf alle Registrierungsdatensätze gewähren würde, sähe wie folgt aus:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referenzthemen:

Die folgenden Referenzthemen enthalten weitere Informationen zum Steuern des Zugriffs auf Ihren IoT Device Provisioning-Dienst.

### <a name="device-provisioning-service-permissions"></a>Berechtigungen des Device Provisioning-Diensts

Die folgende Tabelle enthält die Berechtigungen, die Sie zum Steuern des Zugriffs auf den IoT Device Provisioning-Dienst verwenden können:

| Berechtigung | Notizen |
| --- | --- |
| **ServiceConfig** |Gewährt Zugriff zum Ändern der Dienstkonfigurationen. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **EnrollmentRead** |Gewährt Lesezugriff auf die Geräteregistrierungen und Registrierungsgruppen. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **EnrollmentWrite** |Gewährt Schreibzugriff auf die Geräteregistrierungen und Registrierungsgruppen. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **RegistrationStatusRead** |Gewährt Lesezugriff auf den Status der Geräteregistrierung. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **RegistrationStatusWrite**  |Gewährt Schreibzugriff auf den Status der Geräteregistrierung. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/