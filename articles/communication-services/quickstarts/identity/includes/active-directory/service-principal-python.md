---
ms.openlocfilehash: ad95a54a62c863b3c80cc372d57efbdd2adc49dc
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656962"
---
> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/use-managed-Identity).

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

### <a name="install-the-sdk-packages"></a>Installieren der SDK-Pakete

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Erstellen einer neuen Datei
Öffnen und speichern Sie eine neue Datei in Ihrem erstellten Ordner mit dem Namen `authentication.py`. Wir werden unseren Code in dieser Datei platzieren.

### <a name="use-the-sdk-packages"></a>Verwenden der SDK-Pakete

Fügen Sie die folgenden `import`-Anweisungen am Anfang Ihrer Datei hinzu, um die von uns installierten SDKs zu verwenden.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Erstellen eines DefaultAzureCredential-Standards

Wir verwenden [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Diese Anmeldeinformationen eignen sich für Produktions- und Entwicklungsumgebungen. Da sie in dieser Schnellstartanleitung verwendet werden, erstellen wir sie am Anfang der Datei.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>Erstellen einer Identität und Ausstellen eines Tokens mit Dienstprinzipalen

Nun fügen wir Code hinzu, der die erstellten Anmeldeinformationen verwendet, um ein VoIP-Zugriffstoken auszugeben. Wir werden diesen Code später aufrufen:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-service-principals"></a>Senden einer SMS mit Dienstprinzipalen
Als weiteres Beispiel für die Verwendung von Dienstprinzipalen wird dieser Code hinzugefügt, der dieselben Anmeldeinformationen zum Senden einer SMS verwendet:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Schreiben des Hauptcodes

Mit unseren erstellten Funktionen können wir jetzt den Hauptcode schreiben, der die Funktionen aufruft, die wir zuvor geschrieben haben.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

Ihre endgültige `authentication.py`-Datei sollte in etwa wie folgt aussehen:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Ausführen des Programms

Wenn alles erledigt ist, können Sie die Datei durch Eingabe von `python authentication.py` aus dem Verzeichnis Ihres Projekts ausführen. Ist alles in Ordnung, sollte etwa folgende Ausgabe angezeigt werden:

```Bash
    $ python authentication.py
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
