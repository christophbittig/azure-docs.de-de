---
title: 'Tutorial: Signieren und Senden von Anforderungen an die SMS-API von ACS mit Postman'
titleSuffix: An Azure Communication Services tutorial
description: Hier erfahren Sie, wie Sie mithilfe von Postman Anforderungen signieren und an ACS senden, um eine SMS zu senden.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.openlocfilehash: 1b4998be1b49e58c7e2d22749ee46e1f8d754ecd
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255271"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Tutorial: Signieren und Senden von Anforderungen mit Postman
In diesem Tutorial wird Postman eingerichtet und dazu verwendet, eine HTTP-Anforderung an Azure Communication Services zu senden. Am Ende dieses Tutorials werden Sie erfolgreich eine SMS-Nachricht mit Communication Services und Postman gesendet haben. Sie können dann mit Postman andere APIs in Azure Communication Services untersuchen.

Dieses Tutorial umfasst Folgendes:
> [!div class="checklist"]
> * Herunterladen von Postman
> * Einrichten von Postman zum Signieren von HTTP-Anforderungen
> * Senden einer Anforderung an die SMS-API von Communication Services, eine Nachricht zu senden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie unter [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Beim kostenlosen Konto erhalten Sie eine Azure-Gutschrift in Höhe von 200 US-Dollar, damit Sie Kombinationen verschiedener Dienste ausprobieren können.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. Informationen zum Erstellen einer Communication Services-Ressource finden Sie [hier](../quickstarts/create-communication-resource.md).
- Eine zum Senden von SMS geeignete ACS-Telefonnummer. Wie Sie eine solche Telefonnummer erhalten, erfahren Sie unter [Schnellstart: Beschaffen einer Telefonnummer über das Azure-Portal](../quickstarts/telephony-sms/get-phone-number.md).

## <a name="downloading-and-installing-postman"></a>Herunterladen und Installieren von Postman

Postman ist eine Desktopanwendung, mit der API-Anforderungen an jede beliebige HTTP-API gesendet werden können. Sie wird häufig zum Testen und Erkunden von APIs verwendet. Hier wird die aktuelle [Desktopversion von der Postman-Website](https://www.postman.com/downloads/) heruntergeladen. Von Postman sind Versionen für Windows, Mac und Linux verfügbar. Laden Sie daher die passende Version für Ihr Betriebssystem herunter. Öffnen Sie die heruntergeladene Anwendung. Daraufhin wird ein Startbildschirm angezeigt, in dem Sie aufgefordert werden, sich anzumelden oder ein Postman-Konto zu erstellen. Die Kontoerstellung ist optional und kann durch Klicken auf den Link „Skip and go to app“ (Überspringen und weiter zur App) übersprungen werden. Wenn Sie ein Konto erstellen, werden Ihre API-Anforderungseinstellungen in Postman gespeichert, sodass Sie Ihre Anforderungen auf anderen Computern wiederverwenden können.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Startbildschirm von Postman, auf dem Sie ein Konto erstellen oder die App ohne Kontoerstellung öffnen können":::

Nachdem Sie ein Konto erstellt oder die Kontoerstellung übersprungen haben, wird das Hauptfenster von Postman angezeigt.

## <a name="creating-and-configuring-a-postman-collection"></a>Erstellen und Konfigurieren einer Postman-Sammlung

Anforderungen können von Postman auf verschiedene Arten strukturiert werden. Im Rahmen dieses Tutorials wird eine Postman-Sammlung erstellt. Wählen Sie hierzu auf der linken Seite der Anwendung die Schaltfläche „Collections“ (Sammlungen) aus:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Hauptbildschirm von Postman mit hervorgehobener Registerkarte „Collections“ (Sammlungen)":::

Klicken Sie anschließend auf „Create new Collection“ (Neue Sammlung erstellen), um den Erstellungsprozess zu starten. Im mittleren Bereich von Postman wird eine neue Registerkarte geöffnet. Geben Sie der Sammlung einen Namen Ihrer Wahl. In diesem Beispiel hat die Sammlung den Namen „ACS“:

:::image type="content" source="media/postman/acs-collection.png" alt-text="Postman mit geöffneter Communication Services-Sammlung und hervorgehobenem Namen der Sammlung.":::

Nachdem Sie Ihre Sammlung erstellt und benannt haben, können Sie sie konfigurieren.

### <a name="adding-collection-variables"></a>Hinzufügen von Sammlungsvariablen

Für die Authentifizierung sowie zur Vereinfachung von Anforderungen geben wir in der neu erstellten Communication Services-Sammlung zwei Sammlungsvariablen an. Diese Variablen sind für alle Anforderungen in Ihrer Communication Services-Sammlung verfügbar. Öffnen Sie die Registerkarte „Variables“ (Variablen) der Sammlung, um mit der Erstellung von Variablen zu beginnen.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Postman mit der Registerkarte „Variablen“ einer Communication Services-Sammlung.":::

Erstellen Sie auf der Registerkarte der Sammlung zwei Variablen:
- „key“ (Schlüssel): Bei dieser Variablen handelt es sich um einen Ihrer Schlüssel auf der Schlüsselseite von Azure Communication Services im Azure-Portal. Beispiel: `oW...A==`.
- „endpoint“ (Endpunkt): Bei dieser Variablen handelt es sich um Ihren Azure Communication Services-Endpunkt von der Schlüsselseite. **Wichtig: Entfernen Sie den nachgestellten Schrägstrich.** Beispiel: `https://contoso.communication.azure.com`.

Geben Sie diese Werte in die Spalte „Initial Value“ (Anfangswert) des Variablenbildschirms ein. Klicken Sie nach der Eingabe auf die Schaltfläche „Persist All“ (Alle speichern), die sich auf der rechten Seite direkt über der Tabelle befindet. Nach ordnungsgemäßer Konfiguration sollte der Postman-Bildschirm in etwa wie folgt aussehen:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Postman mit den ordnungsgemäß festgelegten Variablen einer Communication Services-Sammlung.":::

Weitere Informationen zu Variablen finden Sie in der [entsprechenden Postman-Dokumentation](https://learning.postman.com/docs/sending-requests/variables).

### <a name="creating-a-pre-request-script"></a>Erstellen eines Voranforderungsskripts

Als Nächstes erstellen wir in Postman ein Voranforderungsskript. Ein Voranforderungsskript ist ein Skript, das vor jeder Anforderung in Postman ausgeführt wird, um Anforderungsparameter für Sie zu ändern oder anzupassen. Wir verwenden dieses Skript, um die HTTP-Anforderungen zu signieren, damit sie von Azure Communication Services autorisiert werden können. Weitere Informationen zu den Signaturanforderungen finden Sie in [unserem Leitfaden zur Authentifizierung](/rest/api/communication/authentication).

Dieses Skript wird innerhalb der Sammlung erstellt, sodass es für jede Anforderung in der Sammlung ausgeführt wird. Klicken Sie hierzu auf der Registerkarte „Collection“ (Sammlung) auf die Unterregisterkarte „Pre-request Script“ (Voranforderungsskript).

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Postman mit Auswahl der Unterregisterkarte „Voranforderungsskript“ einer Communication Services-Sammlung.":::

Auf dieser Unterregisterkarte können Sie ein Voranforderungsskript erstellen, indem Sie es im Textbereich darunter eingeben. Es ist unter Umständen einfacher, das Skript zunächst in einem vollwertigen Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/) zu schreiben und es anschließend einzufügen. In diesem Tutorial werden die einzelnen Komponenten des Skripts näher erläutert. Falls Sie das Skript einfach nur in Postman kopieren und loslegen möchten, überspringen Sie diesen Abschnitt. Beginnen wir nun damit, das Skript zu schreiben.

### <a name="writing-the-pre-request-script"></a>Schreiben des Voranforderungsskripts

Als Erstes erstellen wir eine UTC-Zeichenfolge (koordinierte Weltzeit) und fügen sie dem HTTP-Header „Date“ (Datum) hinzu. Außerdem speichern wir diese Zeichenfolge in einer Variablen, um sie später beim Signieren zu verwenden:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Als Nächstes hashen wir den Anforderungstext mit SHA256 und platzieren ihn im Header `x-ms-content-sha256`. Da Postman einige [Standardbibliotheken](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) zum globalen Hashen und Signieren enthält, müssen wir sie nicht installieren oder voraussetzen:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Nun verwenden wir unsere zuvor angegebene Endpunktvariable, um den Wert für den HTTP-Hostheader zu ermitteln. Dies ist erforderlich, da der Hostheader erst nach der Verarbeitung des Skripts festgelegt wird:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Nach Erstellung dieser Information können wir die Zeichenfolge erstellen, die für die HTTP-Anforderung signiert wird. Sie besteht aus mehreren zuvor erstellten Werten:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Zum Schluss müssen wir diese Zeichenfolge noch mit unserem Communication Services-Schlüssel signieren und der Anforderung im Header `Authorization` hinzufügen:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Das fertige Voranforderungsskript

Das fertige Voranforderungsskript sollte in etwa wie folgt aussehen:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Geben oder fügen Sie dieses fertige Skript in den Textbereich auf der Registerkarte „Pre-request Script“ (Voranforderungsskript) ein:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Postman mit eingegebenem Voranforderungsskript einer ACS-Sammlung":::

Drücken Sie nach der Eingabe STRG+S, oder klicken Sie auf die Schaltfläche „Save“ (Speichern), um das Skript in der Sammlung zu speichern. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Schaltfläche zum Speichern des Voranforderungsskripts in Postman":::

## <a name="creating-a-request-in-postman"></a>Erstellen einer Anforderung in Postman

Nachdem nun alles eingerichtet ist, können wir in Postman eine Communication Services-Anforderung erstellen. Klicken Sie zunächst auf das Plussymbol (+) neben der Communication Services-Sammlung:

:::image type="content" source="media/postman/create-request.png" alt-text="Plusschaltfläche in Postman":::

Dadurch wird in Postman eine neue Registerkarte für die Anforderung erstellt. Als Nächstes müssen wir uns um die Konfiguration kümmern. Wir senden eine Anforderung an die API zum Senden von SMS. Hilfreiche Informationen zu dieser API finden Sie in [dieser Dokumentation](/rest/api/communication/sms/send). Gehen Sie zum Konfigurieren der Anforderung von Postman wie folgt vor:

Legen Sie zunächst den Anforderungstyp auf `POST` fest, und geben Sie `{{endpoint}}/sms?api-version=2021-03-07` in das Feld für die Anforderungs-URL ein. Diese URL enthält die zuvor von uns erstellte Variable `endpoint`, um sie automatisch an Ihre Communication Services-Ressource zu senden.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Postman-Anforderung mit dem Typ „POST“ und korrekt festgelegter URL":::

Wählen Sie als Nächstes die Registerkarte „Body“ (Text) der Anforderung aus, und legen Sie das Optionsfeld darunter auf „raw“ (Unformatiert) fest. Legen Sie auf der rechten Seite das Dropdownfeld „Text“ auf „JSON“ fest:

:::image type="content" source="media/postman/postman-json.png" alt-text="Festlegen des Anforderungstexts auf „raw“ (Unformatiert) und „JSON“":::

Dadurch wird die Anforderung zum Senden und Empfangen von Informationen in einem JSON-Format konfiguriert.

Geben Sie im Textbereich darunter einen Anforderungstext im folgenden Format ein:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

Für den Wert „from“ (Von) müssen Sie wie bereits erwähnt über das Azure Communication Services-Portal [eine Telefonnummer beschaffen](../quickstarts/telephony-sms/get-phone-number.md). Geben Sie sie ohne Leerzeichen und mit vorangestellter Landeskennzahl ein. Beispiel: `+15555551234`. Unter „message“ (Nachricht) können Sie eine beliebige Nachricht eingeben, die Sie senden möchten (beispielsweise `Hello from ACS`). Der Wert „to“ (An) muss ein Telefon sein, auf das Sie Zugriff haben und das SMS-Nachrichten empfangen kann. Verwenden Sie beispielsweise Ihr eigenes Mobiltelefon.

Nach Abschluss der Eingabe muss die Anforderung in der zuvor erstellten Communication Services-Sammlung gespeichert werden. Dadurch wird sichergestellt, dass die zuvor erstellten Variablen und das zuvor erstellte Voranforderungsskript angewendet werden. Klicken Sie hierzu in der rechten oberen Ecke des Anforderungsbereichs auf die Schaltfläche „Save“ (Speichern).

:::image type="content" source="media/postman/postman-save.png" alt-text="Schaltfläche „Save“ (Speichern) für eine Postman-Anforderung":::

Daraufhin wird ein Dialogfenster angezeigt, in dem Sie zur Angabe des Namens und des Speicherorts der Anforderung aufgefordert werden. Sie können einen beliebigen Namen verwenden. Wichtig ist jedoch, dass Sie in der unteren Hälfte des Dialogfelds Ihre Communication Services-Sammlung auswählen:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="Das Postman-Dialogfeld „Anforderung speichern“ mit ausgewählter Communication Services Sammlung.":::

## <a name="sending-a-request"></a>Senden einer Anforderung

Nachdem nun alles eingerichtet ist, sollten Sie die Anforderung senden können und auf Ihrem Telefon eine SMS erhalten. Vergewissern Sie sich hierzu, dass die erstellte Anforderung ausgewählt ist, und klicken Sie dann auf der rechten Seite auf die Schaltfläche „Send“ (Senden):

:::image type="content" source="media/postman/postman-send.png" alt-text="Postman-Anforderung mit hervorgehobener Schaltfläche „Send“ (Senden)":::

Wenn alles funktioniert, sollte nun die Antwort von Communication Services (Statuscode 202) angezeigt werden:

:::image type="content" source="media/postman/postman-202.png" alt-text="Erfolgreich gesendete Postman-Anforderung mit Statuscode 202":::

Bei dem Mobiltelefon, zu dem die Nummer gehört, die Sie im Wert „to“ (An) angegeben haben, sollte außerdem eine SMS eingegangen sein. Sie verfügen nun über eine funktionsfähige Postman-Konfiguration, die mit Azure Communication Services kommunizieren und SMS-Nachrichten senden kann.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkunden von Azure Communication Services-APIs](/rest/api/communication/)
> [Weitere Informationen zur Authentifizierung](/rest/api/communication/authentication)
> [Weitere Informationen zu Postman](https://learning.postman.com/)

Weitere Möglichkeiten:

- [Hinzufügen von Chatfunktionen zu Ihrer App](../quickstarts/chat/get-started.md)
- [Erstellen von Benutzerzugriffstoken](../quickstarts/access-tokens.md)
- [Erfahren Sie mehr über die Client- und Serverarchitektur](../concepts/client-and-server-architecture.md)
- [Informationen zur Authentifizierung](../concepts/authentication.md)