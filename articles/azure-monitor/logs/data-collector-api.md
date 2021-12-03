---
title: HTTP-Datensammler-API von Azure Monitor | Microsoft-Dokumentation
description: Mit der HTTP-Datensammler-API von Azure Monitor können Sie einem Log Analytics-Arbeitsbereich über jeden Client, der die REST-API aufrufen kann, POST JSON-Daten hinzufügen. In diesem Artikel wird beschrieben, wie Sie die API verwenden. Außerdem finden Sie Beispiele zum Veröffentlichen von Daten mit verschiedenen Programmiersprachen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: a7d998499285dbaa400269bc87d027febfd0ca14
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325617"
---
# <a name="send-log-data-to-azure-monitor-by-using-the-http-data-collector-api-preview"></a>Senden von Protokolldaten an Azure Monitor mit der HTTP-Datensammler-API (Vorschau)

In diesem Artikel wird gezeigt, wie Sie die HTTP-Datensammler-API verwenden, um Protokolldaten von einem REST-API-Client an Azure Monitor zu senden.  Sie erfahren, wie die von Ihrem Skript oder Ihrer Anwendung gesammelten Daten formatiert und in eine Anforderung eingefügt werden, um diese dann von Azure Monitor autorisieren zu lassen. Wir stellen Beispiele für Azure PowerShell, C# und Python bereit.

> [!NOTE]
> Die HTTP-Datensammler-API von Azure Monitor befindet sich in der Phase „Public Preview“.

## <a name="concepts"></a>Konzepte
Mit der HTTP-Datensammler-API können Sie einem Log Analytics-Arbeitsbereich in Azure Monitor über jeden Client, der eine REST-API aufrufen kann, Protokolldaten senden.  Bei diesem Client kann es sich um ein Runbook in Azure Automation handeln, das Verwaltungsdaten aus Azure oder einer anderen Cloud sammelt, oder ein anderes Verwaltungssystem, das Azure Monitor zum Konsolidieren und Analysieren von Protokolldaten verwendet.

Alle Daten im Log Analytics-Arbeitsbereich werden als Datensätze mit einem bestimmten Datensatztyp gespeichert.  Sie formatieren die Daten zum Senden an die HTTP-Datensammler-API als mehrere Datensätze im JSON-Format (JavaScript Object Notation).  Wenn Sie die Daten übermitteln, wird ein einzelner Datensatz im Repository für jeden Datensatz in der Anforderungsnutzlast erstellt.

![Screenshot: Übersicht über den HTTP-Datensammler.](media/data-collector-api/overview.png)

## <a name="create-a-request"></a>Erstellen einer Anforderung
Zum Verwenden der HTTP-Datensammler-API erstellen Sie eine POST-Anforderung mit den zu sendenden Daten im JSON-Format. Die nächsten drei Tabellen enthalten die Attribute, die für die einzelnen Anforderungen erforderlich sind. Jedes Attribut wird weiter unten in diesem Artikel ausführlicher beschrieben.

### <a name="request-uri"></a>Anforderungs-URI
| attribute | Eigenschaft |
|:--- |:--- |
| Methode |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Inhaltstyp |Anwendung/json |
| | |

### <a name="request-uri-parameters"></a>URI-Parameter der Anforderung
| Parameter | BESCHREIBUNG |
|:--- |:--- |
| CustomerID |Eindeutiger Bezeichner für den Log Analytics-Arbeitsbereich |
| Resource |Der Name der API-Ressource: /api/logs |
| API-Version |Die Version der bei dieser Anforderung verwendeten API. Die aktuelle Version ist 2016-04-01. |
| | |

### <a name="request-headers"></a>Anforderungsheader
| Header | BESCHREIBUNG |
|:--- |:--- |
| Authorization |Die Signatur der Autorisierung. Weiter unten in diesem Artikel erhalten Sie Informationen zum Erstellen eines HMAC-SHA256-Headers. |
| Log-Type |Geben Sie den Datensatztyp der Daten an, die übermittelt werden. Er darf ausschließlich Buchstaben, Zahlen und den Unterstrich (_) sowie maximal 100 Zeichen enthalten. |
| x-ms-date |Das Datum, an dem die Anforderung verarbeitet wurde, im RFC 7234-Format. |
| x-ms-AzureResourceId | Die Ressourcen-ID der Azure-Ressource, der die Daten zugeordnet werden sollen. Mit dieser ID wird die Eigenschaft [_ResourceId](./log-standard-columns.md#_resourceid) ausgefüllt, und es wird zugelassen, dass die Daten in [resource-context](./design-logs-deployment.md#access-mode)-Abfragen einbezogen werden können. Wenn dieses Feld nicht angegeben wird, werden die Daten nicht in „resource-context“-Abfragen einbezogen. |
| time-generated-field | Der Name eines Felds in den Daten, das den Zeitstempel des Datenelements enthält. Wenn Sie ein Feld angeben, wird der Inhalt für **TimeGenerated** verwendet. Wenn Sie dieses Feld nicht angeben, ist der Standardwert für **TimeGenerated** die Zeit, zu der die Nachricht erfasst wurde. Der Inhalt des Nachrichtenfelds sollte das ISO 8601-Format (jjjj-mm-ttThh:mm:ssZ) einhalten. |
| | |

## <a name="authorization"></a>Authorization
Jede Anforderung an die HTTP-Datensammler-API von Azure Monitor muss einen „Authorization“-Header enthalten. Zum Authentifizieren einer Anforderung müssen Sie sie mit dem Primär- oder Sekundärschlüssel für den Arbeitsbereich signieren, der die Anforderung sendet. Übergeben Sie anschließend diese Signatur als Teil der Anforderung.   

Dies ist das Format für den „Authorization“-Header:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* ist der eindeutige Bezeichner des Log Analytics-Arbeitsbereichs. Die *Signatur* ist ein [HMAC (Hash-based Message Authentication Code)](/dotnet/api/system.security.cryptography.hmacsha256), der aus der Anforderung erstellt und dann mit dem [SHA256-Algorithmus](/dotnet/api/system.security.cryptography.sha256) berechnet wird. Anschließend codieren Sie ihn mit der Base64-Codierung.

Verwenden Sie dieses Format zum Codieren der **SharedKey**-Signaturzeichenfolge:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
                  Content-Type + "\n" +
                  "x-ms-date:" + x-ms-date + "\n" +
                  "/api/logs";
```

Dies ist ein Beispiel für eine Signaturzeichenfolge:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Codieren Sie anschließend die Signaturzeichenfolge, indem Sie den HMAC-SHA256-Algorithmus auf die UTF-8-codierte Zeichenfolge anwenden und dann das Ergebnis als Base64 codieren. Verwenden Sie dieses Format:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Die Beispiele in den nächsten Abschnitten enthalten Beispielcode, den Sie zum Erstellen eines „Authorization“-Headers verwenden können.

## <a name="request-body"></a>Anforderungstext
Der Text der Nachricht muss das JSON-Format aufweisen. Er muss einen oder mehrere Datensätze mit Paaren aus Eigenschaftenname und -wert in im folgenden Format enthalten. Der Name der Eigenschaft darf ausschließlich Buchstaben, Zahlen und den Unterstrich (_) enthalten.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Sie können mehrere Datensätze in einem Anforderungsbatch zusammenfassen. Verwenden Sie dazu das folgende Format. Alle Datensätze müssen denselben Datensatztyp aufweisen.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Datensatztyp und Eigenschaften
Sie definieren einen benutzerdefinierten Datensatztyp beim Senden von Daten über die HTTP-Datensammler-API von Azure Monitor. Derzeit können Sie keine Daten in vorhandene Datensatztypen schreiben, die von anderen Datentypen und Lösungen erstellt wurden. Azure Monitor liest die eingehenden Daten und erstellt dann die Eigenschaften, die den Datentypen der eingegebenen Werte entsprechen.

Jede Anforderung an die Datensammler-API muss einen **Log-Type**-Header mit dem Namen für den Datensatztyp enthalten. Das Suffix **_CL** wird automatisch an den eingegebenen Namen angefügt, um es von anderen Protokolltypen wie z.B. benutzerdefinierten Protokollen zu unterscheiden. Wenn Sie beispielsweise den Namen **MyNewRecordType** eingeben, erstellt Azure Monitor einen Datensatz vom Typ **MyNewRecordType_CL**. Dadurch wird sichergestellt, dass keine Konflikte zwischen benutzerdefinierten Typnamen und den im Lieferumfang von aktuellen oder zukünftiger Microsoft-Lösungen enthaltenen Typen auftreten.

Um den Datentyp einer Eigenschaft festzulegen, fügt Azure Monitor ein Suffix an den Eigenschaftennamen an. Wenn eine Eigenschaft einen NULL-Wert enthält, ist die Eigenschaft nicht in diesem Datensatz enthalten. Diese Tabelle enthält den Datentyp der Eigenschaft und das entsprechende Suffix:

| Datentyp der Eigenschaft | Suffix |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Datum/Uhrzeit |_t |
| GUID (als Zeichenfolge gespeichert) |_g |
| | |

> [!NOTE]
> Zeichenfolgenwerte, die als GUIDs erscheinen, erhalten das Suffix „_g“ und werden als GUID formatiert, auch wenn der eingehende Wert keine Bindestriche enthält. Beispielsweise werden sowohl „8145d822-13a7-44ad-859c-36f31a84f6dd“ als auch „8145d82213a744ad859c36f31a84f6dd“ als „8145d822-13a7-44ad-859c-36f31a84f6dd“ gespeichert. Die einzigen Unterschiede zwischen dieser und einer anderen Zeichenfolge ist das Suffix „_g“ im Namen und das Einfügen von Bindestrichen, wenn diese nicht in der Eingabe enthalten sind. 

Der Datentyp, den Azure Monitor für die einzelnen Eigenschaften verwendet, hängt davon ab, ob der Datensatztyp für den neuen Datensatz bereits vorhanden ist.

* Wenn der Datensatztyp nicht vorhanden ist, erstellt Azure Monitor einen neuen und verwendet dabei den JSON-Typrückschluss, um den Datentyp für die einzelnen Eigenschaften des neuen Datensatzes zu ermitteln.
* Wenn der Datensatztyp vorhanden ist, erstellt Azure Monitor einen neuen Datensatz basierend auf vorhandenen Eigenschaften. Wenn der Datentyp für eine Eigenschaft im neuen Datensatz nicht übereinstimmt und nicht in den vorhandenen Typ konvertiert werden kann oder wenn der Datensatz eine nicht vorhandene Eigenschaft enthält, erstellt Azure Monitor eine neue Eigenschaft mit dem relevanten Suffix.

Beim folgenden Übermittlungseintrag würde beispielsweise ein Datensatz mit den drei Eigenschaften **number_d**, **boolean_b** und **string_s** erstellt:

![Screenshot des Beispieldatensatzes 1.](media/data-collector-api/record-01.png)

Wenn Sie den folgenden Eintrag übermitteln, bei dem alle Werte als Zeichenfolgen formatiert sind, würden die Eigenschaften nicht geändert. Sie können die Werte in vorhandene Datentypen konvertieren.

![Screenshot des Beispieldatensatzes 2.](media/data-collector-api/record-02.png)

Wenn Sie dann aber diese weitere Übermittlung durchführen, erstellt Azure Monitor die neuen Eigenschaften **boolean_d** und **string_d**. Diese Werte können nicht konvertiert werden.

![Screenshot des Beispieldatensatzes 3.](media/data-collector-api/record-03.png)

Wenn Sie dann den folgenden Eintrag übermitteln, bevor der Datensatztyp erstellt wurde, erstellt Azure Monitor einen Datensatz mit den drei Eigenschaften **number_s**, **boolean_s** und **string_s**. In diesem Eintrag sind alle Anfangswerte als Zeichenfolge formatiert:

![Screenshot des Beispieldatensatzes 4.](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Reservierte Eigenschaften
Die folgenden Eigenschaften sind reserviert und sollten nicht in einem benutzerdefinierten Datensatztyp verwendet werden. Wenn Ihre Nutzdaten einen dieser Eigenschaftennamen enthalten, wird ein Fehler angezeigt:

- tenant

## <a name="data-limits"></a>Datengrenzwerte
Für Daten, die an die Datensammlungs-API von Azure Monitor gesendet werden, gelten bestimmte Einschränkungen:

* Maximal 30 MB pro Sendung an die Datensammlungs-API von Azure Monitor. Diese Größenbeschränkung gilt für eine einzelne Sendung. Wenn eine einzelne Sendung mehr als 30 MB Daten enthält, teilen Sie die Daten auf, und senden Sie kleinere Datenblöcke gleichzeitig.
* Maximal 32 KB für Feldwerte. Wenn ein Feldwert größer ist als 32 KB, werden die Daten abgeschnitten.
* Es werden maximal 50 Felder pro Typ empfohlen. Dies ist aus Sicht der Benutzerfreundlichkeit und Suchleistung ein praktikabler Wert.  
* Tabellen in Log Analytics-Arbeitsbereichen unterstützen maximal 500 Spalten (in diesem Artikel als Felder bezeichnet). 
* Maximal 50 Zeichen für Spaltennamen.

## <a name="return-codes"></a>Rückgabecodes
Der HTTP-Statuscode 200 bedeutet, dass die Anforderung für die Verarbeitung empfangen wurde. Er zeigt an, dass der Vorgang erfolgreich abgeschlossen wurde.

In der folgenden Tabelle sind sämtliche Statuscodes aufgeführt, die der Dienst möglicherweise zurückgibt:

| Code | Status | Fehlercode | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Die Anforderung wurde erfolgreich angenommen. |
| 400 |Ungültige Anforderung |InactiveCustomer |Der Arbeitsbereich wurde geschlossen. |
| 400 |Ungültige Anforderung |InvalidApiVersion |Die angegebene API-Version wurde vom Dienst nicht erkannt. |
| 400 |Ungültige Anforderung |InvalidCustomerId |Die angegebene Arbeitsbereichs-ID ist ungültig. |
| 400 |Ungültige Anforderung |InvalidDataFormat |Es wurde ein ungültiges JSON-Format übermittelt. Der Antworttext enthält eventuell weitere Informationen zum Beheben des Fehlers. |
| 400 |Ungültige Anforderung |InvalidLogType |Der angegebene Protokolltyp enthält Sonderzeichen oder Ziffern. |
| 400 |Ungültige Anforderung |MissingApiVersion |Die API-Version wurde nicht angegeben. |
| 400 |Ungültige Anforderung |MissingContentType |Der Inhaltstyp wurde nicht angegeben. |
| 400 |Ungültige Anforderung |MissingLogType |Der erforderliche Protokolltyp für den Wert wurde nicht angegeben. |
| 400 |Ungültige Anforderung |UnsupportedContentType |Der Inhaltstyp wurde nicht auf **application/json** festgelegt. |
| 403 |Verboten |InvalidAuthorization |Der Dienst konnte die Anforderung nicht authentifizieren. Vergewissern Sie sich, dass die Arbeitsbereichs-ID und der Verbindungsschlüssel gültig sind. |
| 404 |Nicht gefunden | | Die angegebene URL ist falsch, oder die Anforderung ist zu groß. |
| 429 |Zu viele Anforderungen | | Der Dienst erwartet eine große Datenmenge von Ihrem Konto. Versuchen Sie die Anforderung später erneut. |
| 500 |Interner Serverfehler |UnspecifiedError |Auf dem Server wurde ein interner Fehler festgestellt. Versuchen Sie die Anforderung erneut. |
| 503 |Dienst nicht verfügbar |ServiceUnavailable |Der Dienst kann derzeit keine Anforderungen empfangen. Bitte wiederholen Sie die Anforderung. |
| | |

## <a name="query-data"></a>Daten abfragen
Zum Abfragen von Daten, die über die HTTP-Datensammler-API von Azure Monitor übermittelt wurden, suchen Sie nach Datensätzen mit einem **Typ**, der dem von Ihnen angegebenen **LogType**-Wert entspricht und das Suffix **_CL** aufweist. Wenn Sie z. B. **MyCustomLog** verwendet haben, werden alle Datensätze mit `MyCustomLog_CL` zurückgegeben.

## <a name="sample-requests"></a>Beispielanforderungen
In den nächsten Abschnitten finden Sie Beispiele für das Senden von Daten an die HTTP-Datensammler-API von Azure Monitor in verschiedenen Programmiersprachen.

Legen Sie für jedes Beispiel die Variablen für den Autorisierungsheader wie folgt fest:

1. Suchen Sie im Azure-Portal Ihren Log Analytics-Arbeitsbereich.
2. Wählen Sie **Agent-Verwaltung** aus.
2. Wählen Sie rechts neben **Arbeitsbereichs-ID** das Symbol **Kopieren** aus, und fügen Sie die ID als Wert der Variable **Kunden-ID** ein.
3. Wählen Sie rechts neben **Primärschlüssel** das Symbol **Kopieren** aus, und fügen Sie die ID als Wert der Variable **Gemeinsam verwendeter Schlüssel** ein.

Sie können auch die Variablen für den Protokolltyp und die JSON-Daten ändern.

### <a name="powershell-sample"></a>PowerShell-Beispiel
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}

# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C#-Beispiel
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-sample"></a>Python-Beispiel
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Java-Beispiel

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;
import java.util.Locale;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC256(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE, Locale.US);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC256(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha256HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha256HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha256HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternativen und Überlegungen

Wenngleich die Datensammler-API die meisten Ihrer Anforderungen an die Erfassung von Freiformdaten in Azure-Protokollen erfüllen sollte, gibt es Fälle, in denen eine Alternative erforderlich sein könnte, um einige der Einschränkungen der API zu umgehen. Nachfolgend sind einige Möglichkeiten sowie wichtige Überlegungen in diesem Zusammenhang aufgeführt:

| Alternative | BESCHREIBUNG | Am besten geeignet für |
|---|---|---|
| [Benutzerdefinierte Ereignisse](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Native SDK-basierte Erfassung in Application Insights | Application Insights (in der Regel über ein SDK in Ihrer Anwendung instrumentiert) bietet die Möglichkeit, benutzerdefinierte Daten über benutzerdefinierte Ereignisse zu senden. | <ul><li> Daten, die innerhalb Ihrer Anwendung generiert, aber nicht vom SDK über einen der Standarddatentypen (Anforderungen, Abhängigkeiten, Ausnahmen usw.) erkannt werden.</li><li> Daten, die meist mit anderen Anwendungsdaten in Application Insights korreliert sind. </li></ul> |
| Datensammler-API in Azure Monitor-Protokollen | Die Datensammler-API in Azure Monitor-Protokollen ist eine völlig offene Methode zur Datenerfassung. Alle in einem JSON-Objekt formatierten Daten können hier gesendet werden. Nachdem sie gesendet wurden, werden sie verarbeitet und in Monitor-Protokollen zur Verfügung gestellt, um mit anderen Daten in Monitor-Protokollen oder mit anderen Application Insights-Daten korreliert zu werden. <br/><br/> Es ist ziemlich einfach, die Daten als Dateien in einen Azure Blob Storage-Blob hochzuladen, von wo aus diese Dateien verarbeitet und in Log Analytics hochgeladen werden. Eine Beispielimplementierung finden Sie unter [Erstellen einer Datenpipeline mit der Datensammler-API](./create-pipeline-datacollector-api.md). | <ul><li> Daten, die nicht notwendigerweise innerhalb einer Anwendung generiert werden, die innerhalb von Application Insights instrumentiert wird.<br>Beispiele sind Lookup- und Faktentabellen, Referenzdaten, vorab aggregierte Statistiken usw. </li><li> Daten, auf die mit anderen Azure Monitor-Daten über Querverweise verwiesen wird (Application Insights, andere Monitor-Protokolldatentypen, Defender für Cloud, Container Insights/VMs usw.). </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | Azure Data Explorer (jetzt allgemein und öffentlich verfügbar) ist die Datenplattform, die Application Insights Analytics und Azure Monitor Logs zugrunde liegt. Die Verwendung der Datenplattform im Rohformat bietet volle Flexibilität (erfordert aber den Verwaltungsmehraufwand) für den Cluster (Kubernetes RBAC, Bindungsrate, Schema usw.). Azure Data Explorer bietet viele [Erfassungsoptionen](/azure/data-explorer/ingest-data-overview#ingestion-methods), wie z. B. [CSV-, TSV- und JSON-Dateien](/azure/kusto/management/mappings). | <ul><li> Daten, die nicht mit anderen Daten in Application Insights oder Monitor-Protokollen korreliert werden. </li><li> Daten, die erweiterte Erfassungs- oder Verarbeitungsfunktionen erfordern, die aktuell nicht in Azure Monitor-Protokollen verfügbar sind. </li></ul> |


## <a name="next-steps"></a>Nächste Schritte
- Verwenden Sie zum Abrufen von Daten aus dem Log Analytics-Arbeitsbereich die [Protokollsuch-API](./log-query-overview.md).

- Informieren Sie sich näher über das [Erstellen einer Datenpipeline mit der Datensammler-API](create-pipeline-datacollector-api.md) mithilfe eines Logic Apps-Workflows für Azure Monitor.
