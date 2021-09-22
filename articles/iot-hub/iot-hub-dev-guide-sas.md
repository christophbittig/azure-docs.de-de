---
title: Steuern des Zugriffs auf IoT Hub mithilfe von SAS-Token | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf IoT Hub für Geräte-Apps und Back-End-Apps mithilfe von SAS-Token (Shared Access Signature) steuern.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 032f67427f070818aa6874d33414c857fa6ca39c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867180"
---
# <a name="control-access-to-iot-hub-using-shared-access-signatures-and-security-tokens"></a>Steuern des Zugriffs auf IoT Hub mit SAS- (Shared Access Signatures) und Sicherheitstoken

In diesem Artikel werden die Optionen zum Sichern Ihres IoT Hubs beschrieben. IoT Hub verwendet *Berechtigungen*, um Zugriff auf den Endpunkt jedes IoT Hubs zu gewähren. Berechtigungen beschränkten den Zugriff auf einen IoT Hub basierend auf der Funktionalität.

Dieser Artikel bietet eine Einführung zu Folgendem:

* Die verschiedenen Berechtigungen, die Sie einem Client für den Zugriff auf Ihren IoT Hub gewähren können.
* Die von IoT Hub verwendeten Token zum Überprüfen von Berechtigungen.
* Festlegen des Geltungsbereichs für Anmeldeinformationen zum Begrenzen des Zugriffs auf bestimmte Ressourcen.
* Benutzerdefinierte Authentifizierungsmechanismen, die auf vorhandene Geräteidentitätsregistrierungen Authentifizierungsschemas zurückgreifen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Sie müssen über Berechtigungen für den Zugriff auf IoT Hub-Endpunkte verfügen. Beispielsweise muss ein Gerät ein Token mit den Anmeldeinformationen zusammen mit jeder an IoT Hub gesendeten Nachricht enthalten. Die Signaturschlüssel, wie die symmetrischen Schlüssel des Geräts, werden jedoch nie übertragen.

## <a name="access-control-and-permissions"></a>Access Control und Berechtigungen

Verwenden Sie Richtlinien für den gemeinsamen Zugriff für den Zugriff auf IoT Hub-Ebene, und verwenden Sie die Anmeldeinformationen der einzelnen Geräte, um den Zugriff nur auf dieses Gerät zu beschränken.

### <a name="iot-hub-level-shared-access-policies"></a>Richtlinien für den gemeinsamen Zugriff auf IoT Hub-Ebene

SAS-Richtlinien können eine beliebige Kombination von Berechtigungen gewähren. Sie können Richtlinien im [Azure-Portal](https://portal.azure.com) programmgesteuert mithilfe von [IoT Hub-Ressourcenanbieter-REST-APIs](/rest/api/iothub/iothubresource) oder mit dem CLI-Befehl [az iot hub policy](/cli/azure/iot/hub/policy) definieren. Ein neu erstellter IoT Hub verfügt über die folgenden Standardrichtlinien:
  
| SAS-Richtlinie | Berechtigungen |
| -------------------- | ----------- |
| iothubowner | Alle Berechtigungen |
| Dienst | **ServiceConnect**-Berechtigung |
| device | **DeviceConnect**-Berechtigung |
| registryRead | **RegistryRead**-Berechtigung |
| registryReadWrite | **RegistryReadWrite**- und **RegistryWrite**-Berechtigungen |

In der folgenden Tabelle werden die Berechtigungen aufgeführt, die Sie zum Steuern des Zugriffs auf den IoT Hub verwenden können.

| Berechtigung | Notizen |
| --- | --- |
| **ServiceConnect** |Diese Berechtigung gewährt Zugriff auf die clouddienstseitigen Endpunkte für Kommunikation und Überwachung. <br/>Clouddiensten wird die Berechtigung erteilt, D2C-Nachrichten zu empfangen, C2D-Nachrichten zu senden und die zugehörigen Übermittlungsbestätigungen zu empfangen. <br/>Erteilt die Berechtigung zum Abrufen der Übermittlungsbestätigungen für Dateiuploads. <br/>Gewährt die Berechtigung zum Zugriff auf Zwillinge zum Aktualisieren von Tags und gewünschten Eigenschaften, zum Abrufen gemeldeter Eigenschaften und zum Ausführen von Abfragen. <br/>Diese Berechtigung wird von Back-End Cloud Services verwendet. |
| **DeviceConnect** |Diese Berechtigung gewährt Zugriff auf die geräteseitigen Endpunkte. <br/>Gewährt die Berechtigung zum Senden von D2C-Nachrichten und zum Empfangen von C2D-Nachrichten. <br/>Gewährt die Berechtigung zum Ausführen eines Dateiupload von einem Gerät. <br/>Gewährt die Berechtigung zum Abrufen von gewünschten Eigenschaftsbenachrichtigungen für Gerätezwillinge und zum Aktualisieren von gemeldeten Eigenschaften. <br/>Gewährt die Berechtigung zum Ausführen eines Dateiupload. <br/>Diese Berechtigung wird von Geräten verwendet. |
| **RegistryRead** |Diese Berechtigung gewährt Lesezugriff auf die Identitätsregistrierung. Weitere Informationen finden Sie unter [Identitätsregistrierung](iot-hub-devguide-identity-registry.md). <br/>Diese Berechtigung wird von Back-End Cloud Services verwendet. |
| **RegistryReadWrite** |Diese Berechtigung gewährt Lese- und Schreibzugriff auf die Identitätsregistrierung. Weitere Informationen finden Sie unter [Identitätsregistrierung](iot-hub-devguide-identity-registry.md). <br/>Diese Berechtigung wird von Back-End Cloud Services verwendet. |

### <a name="per-device-security-credentials"></a>Sicherheitsanmeldeinformationen auf Gerätebasis

Jeder IoT Hub enthält eine [Identitätsregistrierung](iot-hub-devguide-identity-registry.md). Sie können für jedes Gerät in dieser Identitätsregistrierung Sicherheitsanmeldeinformationen konfigurieren, die **DeviceConnect**-Berechtigungen erteilen, deren Gültigkeitsbereich auf die entsprechenden Geräteendpunkte festgelegt ist.

## <a name="security-tokens"></a>Sicherheitstoken

IoT Hub verwendet Sicherheitstoken zum Authentifizieren von Geräten und Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. Darüber hinaus sind Sicherheitstoken im Hinblick auf Gültigkeitszeitraum und Bereich beschränkt. Diese Sicherheitstoken sind auch als SAS-Token (Shared Access Signature) bekannt. [Azure IoT SDKs](iot-hub-devguide-sdks.md) generieren Token automatisch, ohne dass eine spezielle Konfiguration erforderlich ist. In einigen Szenarien müssen Sie Sicherheitstoken allerdings direkt generieren und verwenden. Zu diesen Szenarien zählen:

* Die direkte Verwendung von MQTT-, AMQP- oder HTTPS-Oberflächen

* Die Implementierung des Tokendienstmusters, wie unter [Benutzerdefinierte Geräteauthentifizierung](iot-hub-dev-guide-sas.md#create-a-token-service-to-integrate-existing-devices) beschrieben.

Sie verwenden Sicherheitstoken, um zeitlich begrenzten Zugriff auf Geräte und Dienste für bestimmte Funktionen in IoT Hub zu gewähren. Zur Autorisierung der Verbindungsherstellung mit IoT Hub müssen Geräte und Dienste Sicherheitstoken senden, die entweder mit einem freigegebenen Zugriffsschlüssel oder mit einem symmetrischen Schlüssel signiert sind. Diese Schlüssel werden mit einer Geräteidentität in der Identitätsregistrierung gespeichert.

### <a name="security-token-structure"></a>Struktur von Sicherheitstoken

Ein mit einem Schlüssel für den gemeinsamen Zugriff signiertes Token gewährt Zugriff auf alle Funktionen, die den SAS-Richtlinienberechtigungen zugeordnet sind. Ein Token, das mit einem symmetrischen Schlüssel der Geräteidentität signiert ist, erteilt nur die Berechtigung **DeviceConnect** für die zugeordnete Geräteidentität.

Das Sicherheitstoken weist das folgende Format auf:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier sind die erwarteten Werte:

| Wert | BESCHREIBUNG |
| --- | --- |
| {signature} |Eine HMAC-SHA256-Signaturzeichenfolge in folgendem Format: `{URL-encoded-resourceURI} + "\n" + expiry`. **Wichtig**: Der Schlüssel wird aus Base64 decodiert und als Schlüssel für die HMAC-SHA256-Berechnung verwendet. |
| {resourceURI} |Das URI-Präfix (nach Segment) der Endpunkte, auf die mit diesem Token zugegriffen werden kann – beginnend mit dem Hostnamen des IoT Hubs (kein Protokoll). Zum Beispiel, `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8-Zeichenfolge, dargestellt als die Anzahl von Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC. |
| {URL-encoded-resourceURI} |URL-Codierung des Ressourcen-URI (beides in Kleinbuchstaben) |
| {policyName} |Der Name der gemeinsam genutzten Zugriffsrichtlinie, auf die dieses Token verweist. Nicht vorhanden, wenn das Token auf Geräteregistrierungs-Anmeldeinformationen verweist. |

Das URI-Präfix wird nach Segment, nicht nach Zeichen berechnet. Beispielsweise ist `/a/b` ein Präfix für `/a/b/c`, aber nicht für `/a/bc`.

### <a name="nodejs"></a>[Node.js](#tab/node)

Der folgende „Node.js“-Codeausschnitt zeigt eine Funktion namens **GenerateSasToken**, die das Token aus den Eingaben `resourceUri, signingKey, policyName, expiresInMins` berechnet. In den nächsten Abschnitten wird erläutert, wie die verschiedenen Eingaben für die verschiedenen Anwendungsfälle für Token initialisiert werden.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

### <a name="python"></a>[Python](#tab/python)

Hier ist zum Vergleich der entsprechende Python-Code zum Generieren eines Sicherheitstokens:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```
### <a name="c"></a>[C#](#tab/csharp)

Die Funktion in C# zum Generieren eines Sicherheitstokens ist wie folgt:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```
### <a name="java"></a>[Java](#tab/java)

Java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```
---

### <a name="protocol-specifics"></a>Protokolldetails

Jedes unterstützte Protokoll, z.B. MQTT, AMQP und HTTPS, transportiert Tokens auf unterschiedliche Weise.

Bei Verwendung von MQTT weist das CONNECT-Paket die Geräte-ID als Client-ID, `{iothubhostname}/{deviceId}` im Feld „Benutzername“ und ein SAS-Token im Feld „Kennwort“ auf. `{iothubhostname}` sollte der vollständige CNAME des IoT Hubs (z.B. „contoso.azure-devices.net“) sein.

Bei Verwendung von [AMQP](https://www.amqp.org/) unterstützt IoT Hub [SASL PLAIN](https://tools.ietf.org/html/rfc4616) und die [auf AMQP-Ansprüchen basierte Sicherheit](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Wenn Sie eine auf AMQP-Ansprüchen beruhende Sicherheit verwenden, gibt der Standard vor, wie die oben aufgeführten Token übertragen werden.

Für SASL PLAIN kann der **Benutzername** Folgendes sein:

* `{policyName}@sas.root.{iothubName}`, wenn Token auf IoT Hub-Ebene verwendet werden.
* `{deviceId}@sas.{iothubname}`, wenn Token für Geräte verwendet werden.

In beiden Fällen enthält das Kennwortfeld das Token gemäß der Beschreibung unter [IoT Hub-Sicherheitstoken](iot-hub-dev-guide-sas.md#security-tokens).

HTTPS implementiert die Authentifizierung, indem ein gültiges Token in den Anforderungsheader **Authorization** eingeschlossen wird.

Beispiel: Benutzername (bei der Geräte-ID wird Groß-/Kleinschreibung berücksichtigt): `iothubname.azure-devices.net/DeviceId`

Kennwort (Sie können ein SAS-Token mit dem CLI-Erweiterungsbefehl [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) oder den [Azure IoT Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) generieren):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Die [Azure IoT SDKs](iot-hub-devguide-sdks.md) generieren automatisch Token, wenn eine Verbindung mit dem Dienst hergestellt wird. In einigen Fällen unterstützen die Azure IoT SDKs nicht alle Protokolle oder Authentifizierungsmethoden.

### <a name="special-considerations-for-sasl-plain"></a>Besonderheiten für SASL PLAIN

Bei Verwendung von SASL PLAIN mit AMQP kann ein Client, der eine Verbindung mit einem IoT Hub herstellt, ein einziges Token für jede TCP-Verbindung verwenden. Wenn das Token abläuft, wird die TCP-Verbindung mit dem Dienst getrennt, und es wird ein Versuch zur erneuten Verbindungsherstellung ausgelöst. Dieses Verhalten ist für eine Back-End-App unproblematisch, für eine Geräte-App jedoch schädlich. Dies hat folgende Gründe:

* Gateways stellen eine Verbindung üblicherweise für eine Vielzahl von Geräten her. Bei Verwendung von SASL PLAIN muss für jedes Gerät, das eine Verbindung mit einem IoT Hub herstellen möchte, eine eigene TCP-Verbindung erstellt werden. Bei diesem Szenario erhöht sich der Verbrauch von Rechen- und Netzwerkressourcen erheblich, und es kommt zu einer höheren Latenz für jede Geräteverbindung.

* Der erhöhte Ressourcenverbrauch bei der erneuten Verbindungsherstellung nach jedem Tokenablauf wirkt sich negativ auf Geräte mit Ressourceneinschränkungen aus.

## <a name="use-security-tokens-from-service-components"></a>Verwenden der Sicherheitstoken von Dienstkomponenten

Dienstkomponenten können nur Sicherheitstoken mithilfe von SAS-Richtlinien generieren, die die entsprechenden Berechtigungen erteilen, wie bereits erläutert.

Hier sind die Dienstfunktionen, die für die Endpunkte verfügbar gemacht werden:

| Endpunkt | Funktionalität |
| --- | --- |
| `{iot hub host name}/devices` |Erstellen, Aktualisieren, Abrufen und Löschen von Geräteidentitäten. |
| `{iot hub host name}/messages/events` |Empfangen von Gerät-an-Cloud-Nachrichten. |
| `{iot hub host name}/servicebound/feedback` |Empfangen von Feedback für Cloud-an-Gerät-Nachrichten. |
| `{iot hub host name}/devicebound` |Senden von Cloud-an-Gerät-Nachrichten. |

Beispielsweise würde ein Dienst, der zum Generieren die vorab erstellte SAS-Richtlinie mit dem Namen **registryRead** verwendet, ein Token mit den folgenden Parametern erstellen:

* Ressourcen-URI: `{IoT hub name}.azure-devices.net/devices`
* Signaturschlüssel: einer der Schlüssel der `registryRead` -Richtlinie
* Richtlinienname: `registryRead`
* Eine Ablaufzeit

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Das Ergebnis, das Lesezugriff für alle Geräteidentitäten gewähren würde, wäre:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="authenticating-a-device-to-iot-hub"></a>Authentifizieren eines Geräts für IoT Hub

### <a name="supported-x509-certificates"></a>Unterstützte X.509-Zertifikate

Sie können ein X.509-Zertifikat verwenden, um ein Gerät bei IoT Hub zu authentifizieren. Laden Sie hierzu entweder einen Zertifikatfingerabdruck oder eine Zertifizierungsstelle (Certificate Authority, CA) in Azure IoT Hub hoch. Weitere Informationen finden Sie unter [Geräteauthentifizierung mit X.509-Zertifikaten](iot-hub-x509ca-overview.md). Informationen zum Hochladen und Überprüfen einer Zertifizierungsstelle bei Ihrem IoT Hub finden Sie unter [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](./tutorial-x509-scripts.md).

### <a name="enforcing-x509-authentication"></a>Erzwingen der X.509-Authentifizierung

Zur Erhöhung der Sicherheit kann ein IoT Hub so konfiguriert werden, dass die SAS-Authentifizierung für Geräte und Module nicht zugelassen ist, sodass X.509 die einzige akzeptierte Authentifizierungsoption bleibt. Dieses Feature steht im Azure-Portal zurzeit nicht zur Verfügung. Um es zu konfigurieren, legen Sie in den IoT Hub-Ressourceneigenschaften `disableDeviceSAS` und `disableModuleSAS` auf `true` fest:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.disableDeviceSAS=true properties.disableModuleSAS=true
```

### <a name="use-sas-tokens-as-a-device"></a>Verwenden von SAS-Token als Gerät

Es gibt zwei Methoden zum Abrufen von **DeviceConnect**-Berechtigungen mit IoT Hub über Sicherheitstoken: mit einem [symmetrischen Identitätsschlüssel aus der Identitätsregistrierung](#use-a-symmetric-key-in-the-identity-registry) oder mit einem [Schlüssel für den gemeinsamen Zugriff](#use-a-shared-access-policy-to-access-on-behalf-of-a-device).

Denken Sie daran, dass alle Funktionen, auf die auf Geräten zugegriffen werden kann, für Endpunkte mit dem Präfix `/devices/{deviceId}` absichtlich verfügbar gemacht werden.

Die geräteseitigen Endpunkte sind (unabhängig vom Protokoll):

| Endpunkt | Funktionalität |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Senden von Gerät-an-Cloud-Nachrichten. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Empfangen von Cloud-an-Gerät-Nachrichten. |

#### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Verwenden eines symmetrischen Schlüssels in der Identitätsregistrierung

Wenn Sie den symmetrischen Schlüssel einer Geräteidentität zum Generieren eines Tokens verwenden, wird das policyName-Element (`skn`) des Tokens nicht angegeben.

Beispielsweise muss ein Token, das für den Zugriff auf alle Gerätefunktionen erstellt wird, die folgenden Parameter aufweisen:

* Ressourcen-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* Signaturschlüssel: ein symmetrischer Schlüssel für die `{device id}` -Identität
* Kein Richtlinienname
* Eine Ablaufzeit

Ein Beispiel mit der oben genannten Node.js-Funktion:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Das Ergebnis, das Zugriff auf alle Funktionen für „device1“ gewährt wird, wäre:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Sie können ein SAS-Token mit dem CLI-Erweiterungsbefehl [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) oder den [Azure IoT Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) generieren.

#### <a name="use-a-shared-access-policy-to-access-on-behalf-of-a-device"></a>Verwenden einer SAS-Richtlinie für den Zugriff im Auftrag eines Geräts

Wenn Sie ein Token auf der Grundlage einer SAS-Richtlinie erstellen, legen Sie das Feld `skn` auf den Namen der Richtlinie fest. Diese Richtlinie muss die Berechtigung **DeviceConnect** erteilen.

Die beiden wichtigsten Szenarien für die Verwendung von SAS-Richtlinien für den Zugriff auf Gerätefunktionen sind:

* [Protokollgateways für Clouds](iot-hub-devguide-endpoints.md),
* [Tokendienste](iot-hub-dev-guide-sas.md#create-a-token-service-to-integrate-existing-devices), die verwendet werden, um benutzerdefinierte Authentifizierungsschemas zu implementieren.

Da die SAS-Richtlinie potenziell Zugriff gewähren kann, um als ein beliebiges Gerät eine Verbindung herzustellen, ist es wichtig, beim Erstellen von Sicherheitstoken den richtigen Ressourcen-URI zu verwenden. Diese Einstellung ist besonders wichtig für Tokendienste, die das Token mit dem Ressourcen-URI auf ein bestimmtes Gerät festlegen müssen. Dieser Punkt ist weniger relevant für Protokollgateways, da sie Datenverkehr bereits für alle Geräte vermitteln.

Beispielsweise würde ein Tokendienst, der die vorab erstellte SAS-Richtlinie mit dem Namen **device** verwendet, ein Token mit den folgenden Parametern erstellen:

* Ressourcen-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* Signaturschlüssel: einer der Schlüssel der `device` -Richtlinie
* Richtlinienname: `device`
* Eine Ablaufzeit

Ein Beispiel mit der oben genannten Node.js-Funktion:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Das Ergebnis, das Zugriff auf alle Funktionen für „device1“ gewährt wird, wäre:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Ein Protokollgateway könnte das gleiche Token für alle Geräte verwenden, indem einfach der Ressourcen-URI auf `myhub.azure-devices.net/devices`festgelegt wird.

## <a name="create-a-token-service-to-integrate-existing-devices"></a>Erstellen eines Tokendiensts zum Integrieren vorhandener Geräte

Mit der [Identitätsregistrierung](iot-hub-devguide-identity-registry.md) von IoT Hub können Sie Sicherheitsanmeldeinformationen und die Zugriffssteuerung pro Gerät/Modul mithilfe von [Token](iot-hub-dev-guide-sas.md#security-tokens) konfigurieren. Wenn eine IoT-Lösung bereits über eine benutzerdefinierte Identitätsregistrierung und/oder über ein Authentifizierungsschema verfügt, können Sie diese Infrastruktur durch Erstellen eines *Tokendiensts* in IoT Hub integrieren. Auf diese Weise können Sie andere IoT-Features in der Lösung nutzen.

Ein Tokendienst ist ein benutzerdefinierter Clouddienst. Er verwendet eine *SAS-Richtlinie* von IoT Hub mit der Berechtigung **DeviceConnect**, um Token für den *Gerätebereich* oder *Modulbereich* zu erstellen. Mit diesen Token kann ein Gerät oder ein Modul eine Verbindung mit Ihrer IoT Hub-Instanz herstellen.

![Schritte des Tokendienstmusters](./media/iot-hub-devguide-security/tokenservice.png)

Hier sind die wichtigsten Schritte des Tokendienstmusters:

1. Erstellen Sie eine IoT Hub-SAS-Richtlinie mit der Berechtigung **DeviceConnect** für Ihren IoT-Hub. Sie können diese Richtlinie im [Azure-Portal](https://portal.azure.com) oder programmgesteuert erstellen. Diese Richtlinie wird vom Tokendienst zum Signieren der von ihm erstellten Token verwendet.

2. Wenn ein Gerät/Modul auf IoT Hub zugreifen muss, fordert es von Ihrem Tokendienst ein signiertes Token an. Das Gerät kann mit Ihrer benutzerdefinierten Identitätsregistrierung bzw. mit dem Authentifizierungsschema authentifiziert werden, um die Geräte- oder Modulidentität zu ermitteln, die der Tokendienst zum Erstellen des Tokens verwendet.

3. Der Tokendienst gibt ein Token zurück. Das Token wird mit `/devices/{deviceId}` oder `/devices/{deviceId}/module/{moduleId}` als `resourceURI` erstellt, wobei `deviceId` das authentifizierte Gerät bzw. `moduleId` das authentifizierte Modul ist. Der Tokendienst verwendet die SAS-Richtlinie, um das Token zu erstellen.

4. Das Gerät/Modul nutzt das Token direkt mit IoT Hub.

> [!NOTE]
> Sie können die .NET-Klasse [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) oder die Java-Klasse [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) zum Erstellen eines Tokens im Tokendienst verwenden.

Der Tokendienst kann die Gültigkeitsdauer für das Token wie gewünscht festlegen. Wenn das Token abläuft, trennt IoT Hub die Geräte-/Modulverbindung. Das Gerät/Modul muss dann ein neues Token vom Tokendienst anfordern. Eine kurze Ablaufzeit erhöht die Last für das Gerät/Modul und den Tokendienst gleichermaßen.

Damit ein Gerät/Modul eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie es der IoT Hub-Identitätsregistrierung hinzufügen – auch wenn es für die Verbindung ein Token und keinen Schlüssel verwendet. Aus diesem Grund können Sie weiterhin die geräte-/modulspezifische Zugriffssteuerung nutzen, indem Sie Geräte-/Modulidentitäten in der [Identitätsregistrierung](iot-hub-devguide-identity-registry.md) aktivieren oder deaktivieren. Dieser Ansatz verringert die Risiken der Verwendung von Token mit langen Ablaufzeiten.

### <a name="comparison-with-a-custom-gateway"></a>Vergleich mit einem benutzerdefinierten Gateway

Das Tokendienstmuster ist der empfohlene Weg zur Implementierung einer benutzerdefinierten Identitätsregistrierung bzw. eines Authentifizierungsschemas mit IoT Hub. Dieses Muster wird empfohlen, da der größte Teil des Lösungsdatenverkehrs weiterhin über IoT Hub abgewickelt wird. Wenn das benutzerdefinierte Authentifizierungsschema allerdings so eng mit dem Protokoll verknüpft ist, muss der gesamte Datenverkehr unter Umständen durch ein *benutzerdefiniertes Gateway* verarbeitet werden. Ein Beispiel eines solchen Szenarios ist die Verwendung von [Transport Layer Security (TLS) und vorinstallierten Schlüsseln (PSKs)](https://tools.ietf.org/html/rfc4279). Weitere Informationen finden Sie im Artikel zum [Protokollgateway](iot-hub-protocol-gateway.md).


## <a name="additional-reference-material"></a>Weiteres Referenzmaterial

Weitere Referenzthemen im IoT Hub-Entwicklerhandbuch:

* Unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) werden die verschiedenen Endpunkte beschrieben, die jeder IoT Hub für Laufzeit- und Verwaltungsvorgänge verfügbar macht.

* Unter [Drosselung und Kontingente](iot-hub-devguide-quotas-throttling.md) werden die Kontingente und das Drosselungsverhalten des IoT Hub-Diensts beschrieben.

* Unter [Verstehen und Verwenden von Azure IoT Hub SDKs](iot-hub-devguide-sdks.md) werden die verschiedenen Sprach-SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit IoT Hub verwenden können.

* Unter [IoT Hub-Abfragesprache](iot-hub-devguide-query-language.md) wird die Abfragesprache beschrieben, mit der Sie Informationen zu Gerätezwillingen und Aufträgen aus IoT Hub abrufen können.

* [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](iot-hub-mqtt-support.md) enthält weitere Informationen zur IoT Hub-Unterstützung für das MQTT-Protokoll.

* [RFC 5246 – Die Transport Layer Security (TLS)-Protokollversion 1.2](https://tools.ietf.org/html/rfc5246/) enthält weitere Informationen zur TLS-Authentifizierung.

* Weitere Informationen zur Authentifizierung per Zertifizierungsstelle finden Sie unter [Geräteauthentifizierung mit X.509-Zertifikaten](iot-hub-x509ca-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie gelernt haben, wie Sie den Zugriff auf IoT Hub steuern, sind möglicherweise die folgenden Themen im IoT Hub-Entwicklerhandbuch für Sie interessant:

* [Verwenden von Gerätezwillingen zum Synchronisieren von Status und Konfigurationen](iot-hub-devguide-device-twins.md)
* [Aufrufen einer direkten Methode auf einem Gerät](iot-hub-devguide-direct-methods.md)
* [Planen von Aufträgen auf mehreren Geräten](iot-hub-devguide-jobs.md)

Wenn Sie einige der in diesem Artikel beschriebenen Konzepte ausprobieren möchten, sehen Sie sich die folgenden IoT Hub-Tutorials an:

* [Erste Schritte mit Azure IoT Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)
* [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Verarbeiten von Gerät-zu-Cloud-Nachrichten mit IoT Hub](tutorial-routing.md)