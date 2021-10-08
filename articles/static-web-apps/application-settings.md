---
title: Konfigurieren von Anwendungseinstellungen für Azure Static Web Apps
description: Es wird beschrieben, wie Sie Anwendungseinstellungen für Azure Static Web Apps konfigurieren.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/23/2021
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: a104860eb72a6376c2ab337f31bb06fd041f42a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597092"
---
# <a name="configure-application-settings-for-azure-static-web-apps"></a>Konfigurieren von Anwendungseinstellungen für Azure Static Web Apps

Anwendungseinstellungen enthalten Konfigurationseinstellungen für Werte, die sich ggf. ändern können, z. B. Datenbank-Verbindungszeichenfolgen. Indem Sie Anwendungseinstellungen hinzufügen, können Sie die Konfigurationseingabe für Ihre App ändern, ohne dass Sie dafür den Anwendungscode ändern müssen.

Anwendungseinstellungen:

- Sind als Umgebungsvariablen für die Back-End-API einer statischen Web-App verfügbar.
- Können zum Speichern von Geheimnissen verwendet werden, die in der [Authentifizierungskonfiguration](key-vault-secrets.md) verwendet werden.
- Sind im Ruhezustand verschlüsselt.
- Werden in [Staging](review-publish-pull-requests.md)- und Produktionsumgebungen kopiert.
- Dürfen nur alphanumerische Zeichen, `.` und `_` enthalten.

> [!IMPORTANT]
> Die in diesem Artikel beschriebenen Anwendungseinstellungen gelten nur für die Back-End-API einer statischen Azure-Web-App.
>
> Informationen zum Konfigurieren von Umgebungsvariablen, die zum Erstellen Ihrer Front-End-Webanwendung erforderlich sind, finden Sie unter [Buildkonfiguration](build-configuration.md#environment-variables).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Azure Static Web Apps-Anwendung
- [Azure CLI](/cli/azure/install-azure-cli): Erforderlich, wenn Sie die Befehlszeile verwenden.

## <a name="configure-api-application-settings-for-local-development"></a>Konfigurieren von API-Anwendungseinstellungen für die lokale Entwicklung

APIs in Azure Static Web Apps basieren auf Azure Functions. Dies ermöglicht es Ihnen, Anwendungseinstellungen in der Datei _local.settings.json_ zu definieren, wenn Sie die Anwendung lokal ausführen. In dieser Datei sind die Anwendungseinstellungen in der Eigenschaft `Values` der Konfiguration definiert.

> [!NOTE]
> Die Datei _local.settings.json_ wird nur für die lokale Entwicklung verwendet. Verwenden Sie das [Azure-Portal](https://portal.azure.com), um Anwendungseinstellungen für die Produktionsumgebung zu konfigurieren.

Mit der unten angegebenen Beispieldatei _local.settings.json_ wird veranschaulicht, wie Sie einen Wert für `DATABASE_CONNECTION_STRING` hinzufügen.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Auf in der Eigenschaft `Values` definierte Einstellungen kann im Code in Form von Umgebungsvariablen verwiesen werden. In Node.js-Funktionen sind sie z. B. im `process.env`-Objekt verfügbar.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

Die Datei `local.settings.json` wird vom GitHub-Repository nicht nachverfolgt, weil vertrauliche Informationen, z. B. Datenbank-Verbindungszeichenfolgen, häufig in der Datei enthalten sind. Da die lokalen Einstellungen auf Ihrem Computer verbleiben, müssen Sie Ihre Einstellungen in Azure manuell konfigurieren.

Im Allgemeinen erfolgt das Konfigurieren Ihrer Einstellungen nur unregelmäßig und ist nicht bei jedem Buildvorgang erforderlich.

## <a name="configure-application-settings"></a>Konfigurieren von Anwendungseinstellungen

Sie können Anwendungseinstellungen über das Azure-Portal oder mit der Azure CLI konfigurieren.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Das Azure-Portal verfügt über eine Oberfläche zum Erstellen, Aktualisieren und Löschen von Anwendungseinstellungen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Suchen Sie in der Suchleiste nach **Static Web Apps**, und wählen Sie die Option aus.

1. Klicken Sie in der Randleiste auf die Option **Konfiguration**.

1. Wählen Sie die Umgebung aus, auf die Sie die Anwendungseinstellungen anwenden möchten. Stagingumgebungen werden beim Generieren eines Pull Requests automatisch erstellt und nach dem Zusammenführen des Pull Requests in die Produktionsumgebung höhergestuft. Sie können Anwendungseinstellungen pro Umgebung festlegen.

1. Klicken Sie auf die Schaltfläche **Hinzufügen**, um eine neue App-Einstellung hinzuzufügen.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Ansicht für die Konfiguration von Azure Static Web Apps":::

1. Geben Sie einen **Namen** und **Wert** ein.

1. Klicken Sie auf **OK**.

1. Klicken Sie auf **Speichern**.

### <a name="use-the-azure-cli"></a>Verwenden der Azure CLI

Sie können den Befehl `az rest` verwenden, um für Ihre Einstellungen Massenuploads in Azure durchzuführen. Der Befehl akzeptiert Anwendungseinstellungen als JSON-Objekte in einer übergeordneten Eigenschaft mit dem Namen `properties`.

Die einfachste Möglichkeit, eine JSON-Datei mit den entsprechenden Werten zu erstellen, ist die Erstellung einer geänderten Version Ihrer Datei _local.settings.json_.

1. Fügen Sie Ihrer _GITIGNORE_-Datei den folgenden Eintrag hinzu, um sicherzustellen, dass Ihre neue Datei mit den vertraulichen Daten nicht öffentlich verfügbar gemacht wird.

   ```bash
   local.settings*.json
   ```

1. Erstellen Sie als Nächstes eine Kopie der Datei _local.settings.json_, und geben Sie ihr den Namen _local.settings.properties.json_.

1. Entfernen Sie in der neuen Datei alle anderen Daten, bei denen es sich nicht um Anwendungseinstellungen handelt, und benennen Sie `Values` in `properties` um.

   Die Datei sollte nun in etwa wie im folgenden Beispiel aussehen:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

1. Führen Sie den folgenden Befehl aus, um die statischen Web-Apps in Ihrem Abonnement aufzulisten und deren Details anzuzeigen.

    ```bash
    az staticwebapp list -o json
    ```

    Suchen Sie die statische Web-App, die Sie konfigurieren möchten, und notieren Sie sich deren ID.

1. Führen Sie in einem Terminal oder an der Befehlszeile den folgenden Befehl zum Hochladen der Einstellungen aus. Ersetzen Sie `<YOUR_APP_ID>` durch die ID der App, die Sie im vorherigen Schritt abgerufen haben.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "<YOUR_APP_ID>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

  > [!IMPORTANT]
  > Die Datei „local.settings.properties.json“ muss sich in demselben Verzeichnis befinden, in dem dieser Befehl ausgeführt wird. Diese Datei kann einen beliebigen Namen haben. Der Name hat keine signifikante Bedeutung.

### <a name="view-application-settings-with-the-azure-cli"></a>Anzeigen von Anwendungseinstellungen mit der Azure CLI

Sie können Anwendungseinstellungen mit der Azure CLI anzeigen.

- Führen Sie in einem Terminal oder an der Befehlszeile den unten angegebenen Befehl aus. Stellen Sie sicher, dass Sie den Platzhalter `<YOUR_APP_ID>` durch Ihren Wert ersetzen.

   ```bash
   az rest --method post --uri "<YOUR_APP_ID>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren von Front-End-Frameworks](front-end-frameworks.md)
