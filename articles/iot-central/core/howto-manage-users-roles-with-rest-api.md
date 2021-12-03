---
title: Verwenden der REST-API zum Verwalten von Benutzern und Rollen in Azure IoT Central
description: Verwenden der IoT Central-REST-API zum Verwalten von Benutzern und Rollen in einer Anwendung
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d9892efecf010ea698e9f628cd92308e9ac8608b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078501"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-users-and-roles"></a>Verwenden der IoT Central-REST-API zum Verwalten von Benutzern und Rollen

Mit der IoT Central-REST-API können Sie Clientanwendungen entwickeln, die in IoT Central-Anwendungen integriert werden. Sie können mit der REST-API auch Benutzer und Rollen in Ihrer IoT Central-Anwendung verwalten.

Jeder IoT Central-REST-API-Aufruf erfordert einen Autorisierungsheader. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren von IoT Central-REST-API-Aufrufen](howto-authorize-rest-api.md).

Die Referenzdokumentation für die IoT Central-REST-API finden Sie unter [Azure IoT Central: Referenz zur REST-API](/rest/api/iotcentral/).

> [!TIP]
> Die [Vorschau-API](/rest/api/iotcentral/1.1-previewdataplane/users) enthält Unterstützung für das neue [Organisationsfeature](howto-create-organizations.md).

## <a name="manage-roles"></a>Verwalten von Rollen

Mit der REST-API können Sie die in Ihrer IoT Central-Anwendung definierten Rollen auflisten. Verwenden Sie die folgende Anforderung zum Abrufen einer Liste von Rollen-IDs aus Ihrer Anwendung:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie im folgenden Beispiel aus, das die drei integrierten Rollen und eine benutzerdefinierte Rolle enthält:

```json
{
  "value": [
    {
      "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4",
      "displayName": "Administrator"
    },
    {
      "id": "ae2c9854-393b-4f97-8c42-479d70ce626e",
      "displayName": "Operator"
    },
    {
      "id": "344138e9-8de4-4497-8c54-5237e96d6aaf",
      "displayName": "Builder"
    },
    {
      "id": "16f8533f-6b82-478f-8ba8-7e676b541b1b",
      "displayName": "Example custom role"
    }
  ]
}
```

## <a name="manage-users"></a>Verwalten von Benutzern

Mit der REST-API können Sie Folgendes durchführen:

- Auflisten der Benutzer in einer Anwendung
- Abrufen der Details zu einem einzelnen Benutzer
- Erstellen eines Benutzers
- Ändern eines Benutzers
- Löschen eines Benutzers

### <a name="list-users"></a>Benutzer auflisten

Verwenden Sie die folgende Anforderung zum Abrufen einer Liste von Benutzern aus Ihrer Anwendung:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus. Die Rollenwerte geben die Rollen-ID an, der der Benutzer zugeordnet ist:

```json
{
  "value": [
    {
      "id": "91907508-04fe-4349-91b5-b872f3055a95",
      "type": "email",
      "roles": [
        {
          "role": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        }
      ],
      "email": "user1@contoso.com"
    },
    {
      "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
      "type": "email",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "email": "user2@contoso.com"
    },
    {
      "id": "3ab9375e-d2d9-42da-b419-6ae86a938321",
      "type": "email",
      "roles": [
        {
          "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ],
      "email": "user3@contoso.com"
    },
    {
      "id": "fc5a250b-83fb-433d-892c-e0a144f68c2b",
      "type": "email",
      "roles": [
        {
          "role": "16f8533f-6b82-478f-8ba8-7e676b541b1b"
        }
      ],
      "email": "user4@contoso.com"
    }
  ]
}
```

### <a name="get-a-user"></a>Abrufen eines Benutzers

Verwenden Sie die folgende Anforderung zum Abrufen von Details zu einem einzelnen Benutzer aus Ihrer Anwendung:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users/dc1c916b-a652-49ea-b128-7c465a54c759?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus. Der Rollenwert gibt die Rollen-ID an, der der Benutzer zugeordnet ist:

```json
{
  "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user2@contoso.com"
}
```

### <a name="create-a-user"></a>Erstellen eines Benutzers

Verwenden Sie die folgende Anforderung zum Erstellen eines Benutzers in Ihrer Anwendung. Die ID und E-Mail-Adresse müssen in der Anwendung eindeutig sein:

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

Im folgenden Anforderungstext steht der `role` Wert für die Operatorrolle, die Sie zuvor abgerufen haben:

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus. Der Rollenwert gibt die Rolle an, der der Benutzer zugeordnet ist:

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

Sie können auch einen Dienstprinzipalbenutzer hinzufügen. Dies ist hilfreich, wenn Sie die Dienstprinzipalauthentifizierung für REST-API-Aufrufe verwenden müssen. Weitere Informationen finden Sie unter [Hinzufügen oder Erstellen eines Dienstprinzipalbenutzers](/rest/api/iotcentral/1.0dataplane/users/create#add-or-update-a-service-principal-user).

### <a name="change-the-role-of-a-user"></a>Ändern der Rolle eines Benutzers

Verwenden Sie die folgende Anforderung zum Ändern der einem Benutzer zugewiesenen Rolle. In diesem Beispiel wird die ID der Generatorrolle verwendet, die Sie zuvor abgerufen haben:

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

Anforderungstext. Der Wert gilt für die Generatorrolle, die Sie zuvor abgerufen haben:

```json
{
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ]
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ],
  "email": "user5@contoso.com"
}
```

### <a name="delete-a-user"></a>Löschen eines Benutzers

Verwenden Sie die folgende Anforderung zum Löschen eines Benutzers:

```http
DELETE https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Benutzer*innen und Rollen über die REST-API verwaltet werden, empfiehlt sich als nächster Schritt das [Verwenden der IoT Central-REST-API zum Steuern von Datenexporten](howto-manage-data-export-with-rest-api.md).