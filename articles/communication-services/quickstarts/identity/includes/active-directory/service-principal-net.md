---
ms.openlocfilehash: f34320c0ba70da94315858ed09ffd1ca3e90d846
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656971"
---
> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/use-managed-Identity).

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `ActiveDirectoryQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: `Program.cs`.

```console
dotnet new console -o ActiveDirectoryAuthenticationQuickstart
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd ActiveDirectoryAuthenticationQuickstart
dotnet build
```

### <a name="install-the-sdk-packages"></a>Installieren der SDK-Pakete

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>Verwenden der SDK-Pakete

Fügen Sie `Program.cs` die folgenden `using`-Anweisungen hinzu, um die Azure Identity und Azure Storage SDKs zu verwenden.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
using Azure;
```

## <a name="create-a-defaultazurecredential"></a>Erstellen eines DefaultAzureCredentials

Für diese Schnellstartanleitung verwenden wir [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Diese Anmeldeinformationen eignen sich für Produktions- und Entwicklungsumgebungen. Da sie für jeden Vorgang benötigt werden, erstellen wir sie in der `Program.cs`-Klasse. Fügen Sie am Anfang der Datei Folgendes hinzu:

```csharp
private DefaultAzureCredential credential = new DefaultAzureCredential();
```

## <a name="issue-a-token-with-service-principals"></a>Ausgabe eines Tokens mit Dienstprinzipalen

Nun fügen wir Code hinzu, der die erstellten Anmeldeinformationen verwendet, um ein VoIP-Zugriffstoken auszugeben. Wir werden diesen Code später aufrufen.

```csharp
public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
{
    var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
    var identityResponse = client.CreateUser();
    var identity = identityResponse.Value;

    var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

    return tokenResponse;
}
```

## <a name="send-an-sms-with-service-principals"></a>Senden einer SMS mit Dienstprinzipalen

Als weiteres Beispiel für die Verwendung von Dienstprinzipalen fügen wir diesen Code hinzu, der dieselben Anmeldeinformationen zum Senden einer SMS verwendet:

```csharp
public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
{
    SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
    SmsSendResult sendResult = smsClient.Send(
            from: from,
            to: to,
            message: message,
            new SmsSendOptions(enableDeliveryReport: true) // optional
        );

    return sendResult;
}
```

## <a name="write-the-main-method"></a>Schreiben der Main-Methode

Ihre `Program.cs` sollte bereits eine Main-Methode enthalten. Fügen Sie nun Code hinzu, der unseren zuvor erstellten Code aufruft, um die Verwendung von Dienstprinzipalen zu veranschaulichen:

```csharp
static void Main(string[] args)
{
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    Uri endpoint = new("https://<RESOURCENAME>.communication.azure.com/");

    // We need an instance of the program class to use within this method.
    Program instance = new();

    Console.WriteLine("Retrieving new Access Token, using Service Principals");
    Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
    Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

    Console.WriteLine("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from using Service Principals");
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

Die endgültige `Program.cs`-Datei sollte wie folgt aussehen:

```csharp
class Program
     {
          private DefaultAzureCredential credential = new DefaultAzureCredential();
          static void Main(string[] args)
          {
               // You can find your endpoint and access key from your resource in the Azure portal
               // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
               Uri endpoint = new("https://acstestingrifox.communication.azure.com/");

               // We need an instance of the program class to use within this method.
               Program instance = new();

               Console.WriteLine("Retrieving new Access Token, using Service Principals");
               Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
               Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

               Console.WriteLine("Sending SMS using Service Principals");

               // You will need a phone number from your resource to send an SMS.
               SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from Service Principals");
               Console.WriteLine($"Sms id: {result.MessageId}");
               Console.WriteLine($"Send Result Successful: {result.Successful}");
          }
          public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
          {
               var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
               var identityResponse = client.CreateUser();
               var identity = identityResponse.Value;

               var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

               return tokenResponse;
          }
          public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
          {
               SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
               SmsSendResult sendResult = smsClient.Send(
                    from: from,
                    to: to,
                    message: message,
                    new SmsSendOptions(enableDeliveryReport: true) // optional
               );

               return sendResult;
          }
    }
```

## <a name="run-the-program"></a>Ausführen des Programms

Sie sollten Ihre Anwendung jetzt mithilfe von `dotnet run` aus Ihrem Anwendungsordner ausführen können. Die Ausgabe sollte wie folgt aussehen:
```
Retrieving new Access Token, using Service Principals
Retrieved Access Token: ey....
Sending SMS using Service Principals
Sms id: Outgoing_..._noam
Send Result Successful: True
```
