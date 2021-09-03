---
title: Schützen von Authentifizierungsgeheimnissen in Azure Key Vault
description: Verwenden der verwalteten Identität, um Authentifizierungsgeheimnisse in Azure Key Vault zu schützen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/17/2021
ms.author: cshoe
ms.openlocfilehash: 3f373122d6d68e6435cfe7083115c91718e05a09
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339575"
---
# <a name="securing-authentication-secrets-in-azure-key-vault"></a>Schützen von Authentifizierungsgeheimnissen in Azure Key Vault

Beim Konfigurieren von benutzerdefinierten Authentifizierungsanbietern sollten Sie Verbindungsgeheimnisse in Azure Key Vault speichern. In diesem Artikel wird veranschaulicht, wie Sie mithilfe einer verwalteten Identität Azure Static Web Apps Zugriff auf Key Vault für benutzerdefinierte Authentifizierungsgeheimnisse gewähren.

Für Sicherheitsgeheimnisse müssen die folgenden Elemente vorhanden sein.

- Erstellen Sie eine systemseitig zugewiesene Identität in der Static Web Apps-Instanz.
- Gewähren Sie einem Key Vault-Geheimnis Zugriff auf die Identität.
- Verweisen Sie in den Static Web Apps Anwendungseinstellungen auf das Key Vault Geheimnis.

In diesem Artikel wird veranschaulicht, wie jedes dieser Elemente in der Produktion für [Anwendungen mit eigenen Funktionen (BYOF, Bring Your Own Functions)](./functions-bring-your-own.md) eingerichtet wird.

Key Vault-Integration ist nicht verfügbar für:

- [Stagingversionen Ihrer statischen Web-App](./review-publish-pull-requests.md). Key Vault-Integration wird nur in der Produktionsumgebung unterstützt.
- [Statische Web-Apps mit verwalteten Funktionen](./apis.md). 

## <a name="prerequisites"></a>Voraussetzungen

- Vorhandene Azure Static Web Apps-Site, die [eigene Funktionen (Bring Your Own Functions, BYOF)](./functions-bring-your-own.md) verwendet.
- Vorhandene Key Vault-Ressource mit einem Geheimniswert.

## <a name="create-identity"></a>Erstellen einer Identität

1. Öffnen Sie Ihre Static Web Apps-Site im Azure-Portal.

1. Wählen Sie im Menü _Einstellungen_ die Option **Identität** aus.

1. Wählen Sie die Registerkarte **Systemseitig zugewiesen** aus.

1. Wählen Sie unter der Bezeichnung _Status_ die Schaltfläche **Ein** aus.

1. Wählen Sie die Schaltfläche **Speichern** aus.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity.png" alt-text="Hinzufügen einer systemseitig zugewiesenen Identität":::

1. Wenn das Bestätigungsdialogfeld angezeigt wird, wählen Sie die Schaltfläche **Ja** aus.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity-confirmation.png" alt-text="Bestätigen der die Identitätszuweisung.":::

Sie können jetzt eine Zugriffsrichtlinie hinzufügen, damit Ihrer statischen Web-App das Lesen von Key Vault-Geheimnisse gestattet wird.

## <a name="add-a-key-vault-access-policy"></a>Hinzufügen einer Key Vault-Zugriffsrichtlinie

1. Öffnen Sie Ihre Key Vault-Ressource im Azure-Portal.

1. Wählen Sie im Menü _Einstellungen_ die Option **Zugriffsrichtlinien** aus.

1. Wählen Sie den Link **Zugriffsrichtlinie hinzufügen** aus.

1. Wählen Sie in der Dropdownliste _Geheimnisberechtigungen_ die Option **Abrufen** aus.

1. Wählen Sie neben der Bezeichnung _Prinzipal auswählen_ den Link **Keine ausgewählt** aus.

1. Suchen Sie im Suchfeld nach Ihrem Static Web Apps-Anwendungsnamen.

1. Wählen Sie das Listenelement aus, das Ihrem Anwendungsnamen entspricht.

1. Wählen Sie die Schaltfläche **Auswählen** aus.

1. Wählen Sie die Schaltfläche **Hinzufügen** aus.

1. Wählen Sie die Schaltfläche **Speichern** aus.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-key-vault-save-policy.png" alt-text="Speichern einer Key Vault-Zugriffsrichtlinie":::

Die Zugriffsrichtlinie wird jetzt in Key Vault gespeichert. Greifen Sie als Nächstes auf den URI des Geheimnisses zu, der verwendet werden soll, wenn Sie Ihre statische Web-App der Key Vault-Ressource zuordnen.

1. Wählen Sie im Menü _Einstellungen_ die Option **Geheimnisse** aus.

1. Wählen Sie Ihr gewünschtes Geheimnis aus der Liste aus.

1. Wählen Sie Ihre gewünschte Geheimnisversion aus der Liste aus.

1. Wählen Sie die Schaltfläche **Kopieren** am Ende des Textfelds _Geheimnisbezeichner_ aus, um den URI-Wert des Geheimnisses in die Zwischenablage zu kopieren.

1. Fügen Sie diesen Wert zur späteren Verwendung in einen Text-Editor ein.

## <a name="add-application-setting"></a>Hinzufügen von Anwendungseinstellungen

1. Öffnen Sie Ihre Static Web Apps-Site im Azure-Portal.

1. Wählen Sie im Menü _Einstellungen_ die Option **Konfiguration** aus.

1. Wählen Sie im Abschnitt _Anwendungseinstellungen_ die Schaltfläche **Hinzufügen** aus.

1. Geben Sie einen Namen in das Textfeld für das Feld _Name_ ein.

1. Bestimmen Sie den Geheimniswert im Textfeld für das Feld _Wert_.

    Der Geheimniswert ist aus einigen verschiedenen Werten zusammengesetzt. Die folgende Vorlage zeigt, wie die endgültige Zeichenfolge aufgebaut ist.

    ```text
    @Microsoft.KeyVault(SecretUri=<YOUR-KEY-VAULT-SECRET-URI>)
    ```

    Verwenden Sie die folgenden Schritte, um den vollständigen Geheimniswert zu erstellen.

1. Kopieren Sie die oben stehende Vorlage, und fügen Sie sie in einen Text-Editor ein.

1. Ersetzen Sie `<YOUR-KEY-VAULT-SECRET-URI>` durch den Key Vault-URI-Wert, den Sie zuvor festgelegt haben.

1. Kopieren Sie den neuen vollständigen Zeichenfolgenwert.

1. Fügen Sie den Wert in das Textfeld für das Feld _Wert_ ein.

1. Klicken Sie auf die Schaltfläche **OK**.

1. Wählen Sie im oberen Bereich der Symbolleiste _Anwendungseinstellungen_ die Schaltfläche **Speichern** aus.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-application-settings-save.png" alt-text="Speichern von Anwendungseinstellungen":::

Wenn Ihre benutzerdefinierte Authentifizierungskonfiguration nun auf die neu erstellte Anwendungseinstellung verweist, wird der Wert unter Verwendung der Identität Ihrer statischen Web-App aus Azure Key Vault extrahiert.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Benutzerdefinierte Authentifizierung](./authentication-custom.md)
