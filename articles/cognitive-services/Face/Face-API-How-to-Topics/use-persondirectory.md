---
title: 'Beispiel: Verwenden der PersonDirectory-Struktur – Gesichtserkennung'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die PersonDirectory-Datenstruktur verwenden, um Gesichts- und Personendaten mit größerer Kapazität und mit anderen neuen Features zu speichern.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/22/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 70bbf20570c39fa59da9af7f7883aeef2e107df4
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633786"
---
# <a name="use-the-persondirectory-structure"></a>Verwenden der PersonDirectory-Struktur

Um Gesichtserkennungsvorgänge wie „Identifizieren“ und „Ähnliches suchen“ durchzuführen, müssen Kunden der Gesichtserkennungs-API eine gemischte Liste von **Personen** objekten erstellen. Das neue **PersonDirectory** ist eine Datenstruktur, die eindeutige IDs, optionale Namenszeichenfolgen und optionale Benutzermetadaten-Zeichenfolgen für jede **Person** enidentität enthält, die dem Verzeichnis hinzugefügt wird.

Derzeit bietet die Gesichtserkennungs-API die **LargePersonGroup**-Struktur, die über ähnliche Funktionen verfügt, aber auf 1 Mio. Identitäten beschränkt ist. Die **PersonDirectory**-Struktur kann auf bis zu 75 Milo. Identitäten hochskaliert werden.

Ein weiterer wichtiger Unterschied zwischen **PersonDirectory** und vorherigen Datenstrukturen besteht darin, dass Sie nach dem Hinzufügen von Gesichtern zu einem **Person** enobjekt keine Train-Aufrufe mehr tätigen müssen – der Aktualisierungsprozess erfolgt automatisch.

## <a name="prerequisites"></a>Voraussetzungen
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal [eine Gesichtserkennungsressource erstellen](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace), um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in den Code eingefügt.
  * Sie können den kostenlosen Tarif (F0) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="add-persons-to-the-persondirectory"></a>Hinzufügen von Personen zum PersonDirectory
**Personen** sind die Basisregistrierungseinheiten im **PersonDirectory**. Nachdem Sie dem Verzeichnis **eine Person** hinzugefügt haben, können Sie dieser **Person** bis zu 248 Gesichtsbilder pro Erkennungsmodell hinzufügen. Anschließend können Sie anhand dieser Bilder Gesichter mithilfe unterschiedlicher Bereiche identifizieren.

### <a name="create-the-person"></a>Erstellen der Person
Um eine **Person** zu erstellen, müssen Sie die **CreatePerson**-API aufrufen und einen Namen oder einen userData-Eigenschaftswert angeben.

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var addPersonUri = "https:// {endpoint}/face/v1.0-preview/persons";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Person");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(addPersonUri, content); 
}
```

Der CreatePerson-Aufruf gibt eine generierte ID für die **Person** einen Vorgangsort zurück. Die **Person** endaten werden asynchron verarbeitet, weshalb Sie den Vorgangsort verwenden, um die Ergebnisse abzurufen. 

### <a name="wait-for-asynchronous-operation-completion"></a>Warten auf den Abschluss des asynchronen Vorgangs
Sie müssen den Status des asynchronen Vorgangs mithilfe der zurückgegebenen Vorgangsortzeichenfolge abfragen, um den Fortschritt zu überprüfen. 

Zunächst sollten Sie ein Datenmodell wie das folgende definieren, um die Statusantwort zu verarbeiten.

```csharp
[Serializable]
public class AsyncStatus
{
    [DataMember(Name = "status")]
    public string Status { get; set; }

    [DataMember(Name = "createdTime")]
    public DateTime CreatedTime { get; set; }

    [DataMember(Name = "lastActionTime")]
    public DateTime? LastActionTime { get; set; }

    [DataMember(Name = "finishedTime", EmitDefaultValue = false)]
    public DateTime? FinishedTime { get; set; }

    [DataMember(Name = "resourceLocation", EmitDefaultValue = false)]
    public string ResourceLocation { get; set; }

    [DataMember(Name = "message", EmitDefaultValue = false)]
    public string Message { get; set; }
}
```

Mithilfe der oben genannten HttpResponseMessage können Sie dann die URL abrufen und auf Ergebnisse warten.

```csharp
string operationLocation = response.Headers.GetValues("Operation-Location").FirstOrDefault();

Stopwatch s = Stopwatch.StartNew();
string status = "notstarted";
do
{
    if (status == "succeeded")
    {
        await Task.Delay(500);
    }

    var operationResponseMessage = await client.GetAsync(operationLocation);

    var asyncOperationObj = JsonConvert.DeserializeObject<AsyncStatus>(await operationResponseMessage.Content.ReadAsStringAsync());
    status = asyncOperationObj.Status;

} while ((status == "running" || status == "notstarted") && s.Elapsed < TimeSpan.FromSeconds(30));
```


Sobald der Status als „erfolgreich“ zurückgegeben wird, gilt das **Person** enobjekt als dem Verzeichnis hinzugefügt.

> [!NOTE]
> Der asynchrone Vorgang aus dem Aufruf „**Person** erstellen“ muss keinen Status „erfolgreich“ anzeigen, damit Gesichter hinzugefügt werden können. Er muss jedoch abgeschlossen sein, bevor die **Person** einer **DynamicPersonGroup** hinzugefügt (siehe unten „Erstellen und Aktualisieren einer **DynamicPersonGroup**“) oder während eines Identify-Aufrufs verglichen werden kann. Vergewissern Sie sich, dass Aufrufe sofort funktionieren, nachdem Gesichter der **Person** erfolgreich hinzugefügt wurden.


### <a name="add-faces-to-persons"></a>Hinzufügen von Gesichtern zu Personen

Sobald Sie über die **Person** en-ID aus dem Aufruf „Person erstellen“ verfügen, können Sie pro Erkennungsmodell bis zu 248 Gesichtsbilder zu einer **Person** hinzufügen. Geben Sie das Erkennungsmodell („recognition“, und optional das Erkennungsmodell („detection“)) an, das im Aufruf verwendet werden soll, da die Daten unter jedem Erkennungsmodell separat innerhalb des **PersonDirectory** verarbeitet werden.

Die derzeit unterstützten Erkennungsmodelle sind:
* `Recognition_02`
* `Recognition_03`
* `Recognition_04`

Wenn das Bild mehrere Gesichter enthält, müssen Sie außerdem den rechteckigen Begrenzungsrahmen für das Gesicht angeben, das das beabsichtigte Ziel ist. Der folgende Code fügt einem **Person** enobjekt Gesichter hinzu.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var queryString = "userData={userDefinedData}&targetFace={left,top,width,height}&detectionModel={detectionModel}";
var uri = "https://{endpoint}/face/v1.0-preview/persons/{personId}/recognitionModels/{recognitionModel}/persistedFaces?" + queryString;

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("url", "{image url}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

Nach dem Aufruf von „Gesichter hinzufügen“ werden die Gesichtsdaten asynchron verarbeitet, und Sie müssen auf dieselbe Weise wie zuvor auf den Erfolg des Vorgangs warten.

Wenn der Vorgang für die Gesichtshinzufügung abgeschlossen ist, sind die Daten für Identify-Aufrufe bereit.

## <a name="create-and-update-a-dynamicpersongroup"></a>Erstellen und Aktualisieren einer **DynamicPersonGroup**

**DynamicPersonGroups** sind Sammlungen von Verweisen auf **Person** enobjekte in einem **PersonDirectory**. Sie werden verwendet, um Teilmengen des Verzeichnisses zu erstellen. Eine häufige Verwendung besteht darin, wenn Sie weniger falsch positive Ergebnisse und eine höhere Genauigkeit in einem Identify-Vorgang erhalten möchten, den Bereich auf die **Person** enobjekte zu beschränken, bei denen Sie eine Übereinstimmung erwarten. Zu den praktischen Anwendungsfällen gehören Verzeichnisse für den spezifischen Gebäudezugang auf einem größeren Campus oder bei einer größeren Organisation. Das Organisationsverzeichnis kann 5 Mio. Personen enthalten, aber Sie müssen nur eine bestimmte Gruppe von 800 Personen nach einem bestimmten Gebäude durchsuchen, sodass Sie eine **DynamicPersonGroup** erstellen würden, die diese spezifischen Personen enthält. 

Wenn Sie schon einmal eine **PersonGroup** verwendet haben, beachten Sie zwei wesentliche Unterschiede: 
* Jede **Person** innerhalb einer **DynamicPersonGroup** ist ein Verweis auf die tatsächliche **Person** im **PersonDirectory**, was bedeutet, dass es nicht erforderlich ist, eine **Person** in jeder Gruppe neu zu erstellen.
* Wie in den vorherigen Abschnitten erwähnt, ist es nicht notwendig, Train-Aufrufe zu tätigen, da die Gesichtsdaten automatisch auf Verzeichnisebene verarbeitet werden.

### <a name="create-the-group"></a>Erstellen der Gruppe

Um eine **DynamicPersonGroup** zu erstellen, müssen Sie eine Gruppen-ID mit alphanumerischen Zeichen oder Bindestrichen bereitstellen. Diese ID dient als eindeutiger Bezeichner für alle Verwendungszwecke der Gruppe.

Es gibt zwei Möglichkeiten, um eine Gruppensammlung zu initialisieren. Sie können zunächst eine leere Gruppe erstellen und diese dann später auffüllen:

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);
}
```

Dieser Prozess ist unmittelbar, und es ist nicht notwendig, auf den Erfolg asynchroner Vorgänge zu warten.

Alternativ können Sie sie mit einem Satz von **Personen**-IDs erstellen, um diese Verweise von Anfang an aufzunehmen, indem Sie den Satz im Argument _AddPersonIds_ angeben:

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

> [!NOTE]
> Sobald der Aufruf zurückgegeben wird, kann die erstellte **DynamicPersonGroup** in einem Identify-Aufruf verwendet werden, wobei in dem Prozess alle **Personen** verweise bereitgestellt werden. Der Abschlussstatus der zurückgegebenen Vorgangs-ID zeigt hingegen den Aktualisierungsstatus der Beziehung zwischen Person und Gruppe an.

### <a name="update-the-dynamicpersongroup"></a>Aktualisieren der DynamicPersonGroup

Nach der anfänglichen Erstellung können Sie der **DynamicPersonGroup** mit der API zum Aktualisieren dynamischer Personengruppen **Person** enverweise hinzufügen und daraus entfernen. Um der Gruppe **Person** enobjekte hinzuzufügen, listen Sie die **Personen**-IDs im Argument _addPersonsIds_ auf. Um **Person** enobjekte zu entfernen, listen Sie sie im Argument _removePersonIds_ auf. Das Hinzufügen wie das Entfernen kann in einem einzigen Aufruf erfolgen:

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Dynamic Person Group updated");
body.Add("userData", "User defined data updated");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("removePersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PatchAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

Sobald der Aufruf zurückgegeben wurde, werden die Aktualisierungen an der Sammlung widergespiegelt, wenn die Gruppe abgefragt wird. Wie bei der Erstellungs-API zeigt der zurückgegebene Vorgang den Aktualisierungsstatus der Beziehung zwischen Person und Gruppe für jede **Person** an, die an der Aktualisierung beteiligt ist. Sie müssen nicht auf den Abschluss des Vorgangs warten, bevor Sie weitere Update-Aufrufe an die Gruppe senden.

## <a name="identify-faces-in-a-persondirectory"></a>Identifizieren von Gesichtern in einem PersonDirectory

Die gängigste Methode zum Verwenden von Gesichtsdaten in einem **PersonDirectory** besteht darin, die registrierten **Person** enobjekte mit einem bestimmten Gesicht zu vergleichen und den wahrscheinlichsten Kandidaten zu identifizieren, zu dem es gehört. In der Anforderung können mehrere Gesichter bereitgestellt werden, wovon jedes in der Antwort einen eigenen Satz von Vergleichsergebnissen erhält.

Im **PersonDirectory** gibt es drei Arten von Bereichen, anhand derer jedes Gesicht identifiziert werden kann:

### <a name="scenario-1-identify-against-a-dynamicpersongroup"></a>Szenario 1: Identifizieren anhand einer DynamicPersonGroup
 
Wenn Sie die _dynamicPersonGroupId_-Eigenschaft in der Anforderung angeben, wird das Gesicht mit jeder **Person** verglichen, auf die in der Gruppe verwiesen wird. In einem Aufruf kann nur eine einzige **DynamicPersonGroup** angegeben werden. 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("dynamicPersonGroupId", "{dynamicPersonGroupIdToIdentifyIn}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-2-identify-against-a-specific-list-of-persons"></a>Szenario 2: Identifizieren anhand einer spezifischen Liste von Personen

Sie können auch eine Liste mit **Personen**-IDs in der _personIds_-Eigenschaft angeben, um das Gesicht jeder davon zu vergleichen.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-3-identify-against-the-entire-persondirectory"></a>Szenario 3: Identifizieren anhand des gesamten **PersonDirectory**

Wenn Sie in der Anforderung ein einzelnes Sternchen in der _personIds_-Eigenschaft angeben, wird das Gesicht mit jeder einzelnen **Person** verglichen, die im **PersonDirectory** registriert ist. 
 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"*"});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```
In allen drei Szenarien vergleicht die Identifikation nur das eingehende Gesicht mit Gesichtern, deren AddPersonFace-Aufruf mit einer „erfolgreich“-Antwort zurückgegeben wurde.

## <a name="verify-faces-against-persons-in-the-persondirectory"></a>Überprüfen von Gesichtern anhand von Personen im **PersonDirectory**

Mit einer Gesichts-ID, die von einem Erkennungsaufruf zurückgegeben wird, können Sie überprüfen, ob das Gesicht zu einer bestimmten **Person** gehört, die im **PersonDirectory** registriert ist. Geben Sie die **Person** mithilfe der _personId_-Eigenschaft an.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/verify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceId", "{guid1}");
body.Add("personId", "{guid1}");
var jsSerializer = new JavaScriptSerializer();
byte[] byteData = Encoding.UTF8.GetBytes(jsSerializer.Serialize(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

Die Antwort enthält einen booleschen Wert, der angibt, ob der Dienst das neue Gesicht derselben **Person** zurechnet. Außerdem wird eine Zuverlässigkeitsbewertung für die Vorhersage angegeben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie erfahren, wie Sie die **PersonDirectory**-Struktur verwenden, um Gesichts- und Personendaten für Ihre Gesichtserkennungs-App zu speichern. Als Nächstes lernen Sie die bewährten Methoden zum Hinzufügen der Gesichtsdaten Ihrer Benutzer kennen.

* [Best Practices für das Hinzufügen von Benutzern](../enrollment-overview.md)
