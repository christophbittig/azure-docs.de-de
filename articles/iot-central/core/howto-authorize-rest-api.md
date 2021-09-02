---
title: Autorisieren der REST-API in Azure IoT Central
description: Wie Sie IoT Central-REST-API-Aufrufe authentifizieren und autorisieren
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 540137495e06bb05fbf5e96e3b930e0c8cda4b10
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461228"
---
# <a name="how-to-authenticate-and-authorize-iot-central-rest-api-calls"></a>Wie Sie IoT Central-REST-API-Aufrufe authentifizieren und autorisieren

Mit der IoT Central-REST-API können Sie Clientanwendungen entwickeln, die in IoT Central-Anwendungen integriert werden. Verwenden Sie die REST-API, um mit Ressourcen in Ihrer IoT Central-Anwendung zu arbeiten, z. B. Gerätevorlagen, Geräte, Aufträge, Benutzer und Rollen.

Jeder IoT Central REST-API-Aufruf erfordert einen Autorisierungsheader, der IoT Central verwendet, um die Identität des Aufrufers und die Berechtigungen zu bestimmen, die dem Aufrufer innerhalb der Anwendung erteilt werden.

In diesem Artikel werden die Tokentypen beschrieben, die Sie im Autorisierungsheader verwenden können, und es wird erklärt, wie Sie sie abrufen.

## <a name="token-types"></a>Tokentypen

Sie sollten das Benutzer-Bearertoken verwenden, wenn Sie einige Automatisierungs-/Test-/API-Aufrufe selbst durchführen. Das SPN-Bearertoken sollten Sie verwenden, wenn Sie Ihre Entwicklungsumgebung (d. h. DevOps) automatisieren/Skripts dafür erstellen. Das API-Token kann in beiden Fällen verwendet werden, birgt aber das Risiko von Ablauf und Verlusten. Deshalb empfehlen wir, nach Möglichkeit Bearer zu verwenden. Ist das sinnvoll? 

Um mithilfe IoT Central REST-API auf eine Anwendung zuzugreifen, können Sie eine der folgenden Funktionen verwenden:

- _Azure Active Directory OAuth-Bearertoken_. Ein Bearertoken ist einem Azure Active Directory-Benutzerkonto oder -Dienstprinzipal zugeordnet. Das Token gewährt dem Aufrufer die gleichen Berechtigungen wie dem Benutzer oder Dienstprinzipal in der IoT Central-Anwendung.
- IoT Central-API-Token. Ein API-Token ist einer Rolle in Ihrer IoT Central-Anwendung zugeordnet.

Weitere Informationen zu Benutzern und Rollen in IoT Central finden Sie unter [Verwalten von Benutzern und Rollen in Ihrer IoT Central Anwendung](howto-manage-users-roles.md).

## <a name="get-a-bearer-token"></a>Abrufen eines Bearertokens

Um ein Bearertoken für Ihr Azure Active Directory-Benutzerkonto abzurufen, verwenden Sie die folgenden Azure CLI-Befehle:

```azurecli
az login
az account get-access-token --resource https://apps.azureiotcentral.com
```

> [!IMPORTANT]
> Der `az login`-Befehl ist auch dann erforderlich, wenn Sie die Cloud Shell benutzen.

Die JSON-Ausgabe des vorherigen Befehls sieht wie im folgenden Beispiel aus:

```json
{
  "accessToken": "eyJ0eX...fNQ",
  "expiresOn": "2021-03-22 11:11:16.072222",
  "subscription": "{your subscription id}",
  "tenant": "{your tenant id}",
  "tokenType": "Bearer"
}
```

Das Bearertoken ist ungefähr eine Stunde lang gültig, dann müssen Sie ein neues erstellen.

Informationen zum Abrufen eines Bearertokens für einen Dienstprinzipal finden Sie unter [Dienstprinzipalauthentifizierung](/rest/api/iotcentral/authentication#service-principal-authentication).

## <a name="get-an-api-token"></a>Abrufen eines API-Tokens

Um ein API-Token abzurufen, können Sie die IoT Central-Benutzeroberfläche oder einen REST-API-Aufruf verwenden.

Auf der IoT Central-Benutzeroberfläche:

1. Navigieren Sie zu **Administration > API-Token**.
1. Wählen Sie **+ Token generieren** aus.
1. Geben Sie einen Namen für das Token ein und wählen Sie eine Rolle aus.
1. Wählen Sie **Generieren** aus.
1. IoT Central zeigt das Token an, das wie im folgenden Beispiel aussieht:

    `SharedAccessSignature sr=5782ed70...&sig=dvZZE...&skn=operator-token&se=1647948035850`

    Dieser Bildschirm ist das einzige Mal, dass Sie das API-Token sehen können. Wenn Sie es verlieren, müssen Sie ein neues generieren.

Ein API-Token ist ungefähr ein Jahr lang gültig. Sie können Token sowohl für integrierte als auch für benutzerdefinierte Rollen in Ihrer IoT Central-Anwendung generieren.

Sie können API-Token auf der IoT Central Benutzeroberfläche löschen, wenn Sie den Zugriff widerrufen müssen.

Mithilfe der REST API:

1. Verwenden Sie die REST-API, um eine Liste der Rollen-IDs aus Ihrer Anwendung abzurufen:

    ```http
    GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
    ```

    Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

    ```json
    {
      "value": [
        {
          "displayName": "Administrator",
          "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        },
        {
          "displayName": "Operator",
          "id": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        },
        {
          "displayName": "Builder",
          "id": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ]
    }
    ```

1. Verwenden Sie die REST-API, um ein API-Token für eine Rolle zu erstellen. So erstellen Sie beispielsweise ein API-Token namens `operator-token` für die Operatorrolle:

    ```http
    PUT https://{your app subdomain}.azureiotcentral.com/api/roles/operator-token?api-version=1.0
    ```

    Anforderungstext:

    ```json
    {
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ]
    }
    ```

    Die Antwort auf den vorherigen Befehl sieht wie folgt aus:

    ```json
    {
      "expiry": "2022-03-22T12:01:27.889Z",
      "id": "operator-token",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "token": "SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889"
    }
    ```

    Diese Antwort ist die einzige Zeit, in der Sie Zugriff auf das API-Token haben, wenn Sie es verlieren, müssen Sie ein neues generieren.

Sie können die REST-API verwenden, um API-Token in einer Anwendung aufzulisten und zu löschen.

## <a name="use-a-bearer-token"></a>Abrufen eines Bearertokens

Um ein Bearertoken zu verwenden, wenn Sie einen REST-API-Aufruf ausführen, sieht Ihr Autorisierungsheader wie im folgenden Beispiel aus:

`Authorization: Bearer eyJ0eX...fNQ`

## <a name="use-an-api-token"></a>Verwendung eines API-Tokens

Um ein Bearertoken zu verwenden, wenn Sie einen REST-API-Aufruf ausführen, sieht Ihr Autorisierungsheader wie im folgenden Beispiel aus:

`Authorization: SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889`

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie REST-API-Aufrufe autorisiert werden, empfiehlt es sich als nächsten Schritt: [ Wie die IoT Central REST-API zum Verwalten von Benutzern und Rollen verwendet wird](howto-manage-users-roles-with-rest-api.md).
