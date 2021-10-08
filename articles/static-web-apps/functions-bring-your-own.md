---
title: Einführen eigener Funktionen in Azure Static Web Apps (Bring Your Own Functions)
description: Verwenden Sie eine vorhandene Azure Functions-App mit Ihrer Azure Static Web Apps-Site.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/07/2021
ms.author: cshoe
ms.openlocfilehash: 7925bd70488106943f0030e6b26534938fbc7d36
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570890"
---
# <a name="bring-your-own-functions-to-azure-static-web-apps"></a>Einführen eigener Funktionen in Azure Static Web Apps (Bring Your Own Functions)

Azure Static Web Apps-APIs werden von zwei möglichen Konfigurationen unterstützt: verwaltete Funktionen und eigene Funktionen (Bring Your Own Functions). Details zu den beiden Konfigurationen finden Sie in der [API-Referenz](apis.md).

Dieser Artikel veranschaulicht, wie Sie eine vorhandene Azure Functions-App mit einer Azure Static Web Apps-Ressource verknüpfen.

> [!NOTE]
> „Bring Your Own Functions“ ist nur im Azure Static Web Apps-Standardplan verfügbar.

## <a name="example"></a>Beispiel

Sie haben eine Azure Functions-App, die einen Endpunkt über den folgenden Speicherort verfügbar macht.

```url
https://my-functions-app.azurewebsites.net/api/getProducts
```

Nach der Verknüpfung können Sie von Ihrer statischen Web-App über den `api`-Pfad auf denselben Endpunkt zugreifen, wie in dieser Beispiel-URL gezeigt.

```url
https://red-sea-123.azurestaticapps.net/api/getProducts
```

 Beide Endpunkt-URLs zeigen auf dieselbe Funktion.

## <a name="link-an-existing-azure-functions-app"></a>Verknüpfen einer vorhandenen Azure Functions-App

Bevor Sie eine vorhandene Functions-App zuordnen, müssen Sie zunächst die Konfiguration Ihrer statischen Web-App anpassen.

1. Legen Sie den `api_location`-Wert in der Datei für die [Workflowkonfiguration](./build-configuration.md) auf eine leere Zeichenfolge (`""`) fest.

1. Öffnen Sie Ihre Static Web Apps-Instanz im [Azure-Portal](https://portal.azure.com).

1. Wählen Sie im Menü _Einstellungen_ die Option **Funktionen** aus.

1. Wählen Sie in der Dropdownliste _Umgebung_ die Option **Produktion** aus.

1. Wählen Sie neben der Bezeichnung _Funktionsquelle_ die Option **Mit einer Funktions-App verknüpfen** aus.

1. Wählen Sie in der Dropdownliste _Abonnement_ den Namen Ihres Azure-Abonnements aus.

1. Wählen Sie in der Dropdownliste _Funktions-App_ den Namen der vorhandenen Functions-App aus, die Sie mit Ihrer statischen Web-App verknüpfen möchten.

1. Wählen Sie die Schaltfläche **Verknüpfen** aus.

    :::image type="content" source="media/functions-bring-your-own/azure-static-web-apps-link-existing-functions-app.png" alt-text="Verknüpfen einer vorhandenen Functions-App":::

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie den `api_location`-Wert in der Datei für die [Workflowkonfiguration](./build-configuration.md) auf eine leere Zeichenfolge (`""`) festlegen, bevor Sie eine vorhandene Functions-Anwendung verknüpfen.

## <a name="deployment"></a>Bereitstellung

Sie sind für die Einrichtung eines [Bereitstellungsworkflows](../azure-functions/functions-deployment-technologies.md) für Ihre Azure Functions-App zuständig.

## <a name="security-constraints"></a>Sicherheitseinschränkungen

- **Authentifizierung und Autorisierung**: Wenn für Ihre vorhandene Functions-App noch keine Authentifizierungs- und Autorisierungsrichtlinien eingerichtet wurden, hat die statische Web-App exklusiven Zugriff auf die API. Damit andere Anwendungen auf Ihre Functions-App zugreifen können, fügen Sie einen anderen Identitätsanbieter hinzu, oder ändern Sie die Sicherheitseinstellungen, um nicht authentifizierten Zugriff zuzulassen.

  > [!NOTE]
  > Wenn Sie die Authentifizierung und Autorisierung in Ihrer verknüpften Functions-App aktivieren, muss diese die Azure App Service-Authentifizierung und -Autorisierung Version 2 des Anbieters verwenden.

- **Erforderlicher öffentlicher Zugriff**: Eine vorhandene Functions-App darf die folgenden Sicherheitskonfigurationen nicht anwenden.
  - Einschränken der IP-Adresse der Functions-App
  - Einschränken des Datenverkehrs über private Links oder Dienstendpunkte

- **Funktionszugriffsschlüssel**: Wenn Ihre Funktion einen [Zugriffsschlüssel](../azure-functions/security-concepts.md#function-access-keys) erfordert, müssen Sie den Schlüssel mit Aufrufen von der statischen App an die API bereitstellen.

## <a name="restrictions"></a>Beschränkungen

- Für eine einzelne statische Web-App ist nur eine Azure Functions-App verfügbar.
- Der `api_location`-Wert in der [Workflowkonfiguration](./build-configuration.md) muss auf eine leere Zeichenfolge festgelegt werden.
- Wird nur in der Static Web Apps-Produktionsumgebung unterstützt.
- Während Ihre Azure Functions-App möglicherweise auf verschiedene Trigger reagiert, kann die statische Web-App nur über HTTP-Endpunkte auf Funktionen zugreifen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer API zu Azure Static Web Apps (Vorschauversion) mit Azure Functions](add-api.md)
