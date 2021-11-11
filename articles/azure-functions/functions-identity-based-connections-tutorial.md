---
title: Erstellen einer Funktions-App ohne Standardspeichergeheimnisse in der Definition
titleSuffix: Azure Functions
ms.service: azure-functions
description: Erfahren Sie, wie Sie Storage-Verbindungszeichenfolgen aus Ihrer Funktions-App-Definition entfernen.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 8e6e4bfc80f2154843aab5d3971854054f4319b4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271153"
---
# <a name="tutorial-create-a-function-app-that-connects-to-azure-services-using-identities-instead-of-secrets"></a>Tutorial: Erstellen einer Funktions-App, die mithilfe von Identitäten anstelle von Geheimnissen eine Verbindung mit Azure-Diensten herstellt

In diesem Tutorial erfahren Sie, wie Sie eine Funktions-App nach Möglichkeit mithilfe von Azure Active Directory-Identitäten anstelle von Geheimnissen oder Verbindungszeichenfolgen konfigurieren. Durch Verwendung von Identitäten vermeiden Sie die versehentliche Offenlegung vertraulicher Geheimnisse und erhalten einen besseren Einblick in den Datenzugriff. Weitere Informationen zu identitätsbasierten Verbindungen finden Sie unter [Konfigurieren einer identitätsbasierten Verbindung](functions-reference.md#configure-an-identity-based-connection).

Obwohl die gezeigten Prozeduren in der Regel für alle Sprachen funktionieren, werden in diesem Tutorial derzeit speziell C#-Klassenbibliotheksfunktionen für Windows unterstützt. 

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> * Erstellen einer Funktions-App in Azure anhand einer ARM-Vorlage
> * Aktivieren von systemseitig zugewiesenen und benutzerseitig zugewiesenen verwalteten Identitäten in der Funktions-App
> * Erstellen von Rollenzuweisungen zum Erteilen von Berechtigungen für andere Ressourcen
> * Verschieben von Geheimnissen, die nicht durch Identitäten ersetzt werden können, in Azure Key Vault
> * Konfigurieren einer App zum Herstellen einer Verbindung mit dem Standardhostspeicher mithilfe der zugehörigen verwalteten Identität

Nachdem Sie dieses Tutorial abgeschlossen haben, sollten Sie das nachfolgende Tutorial abschließen, in dem das [Verwenden identitätsbasierter Verbindungen anstelle von Geheimnissen bei Triggern und Bindungen] gezeigt wird. 

## <a name="prerequisites"></a>Voraussetzungen

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.

## <a name="why-use-identity"></a>Gründe für die Verwendung von Identitäten

Die Verwaltung von Geheimnissen und Anmeldeinformationen ist häufig eine Herausforderung für Teams jeder Größe. Geheimnisse müssen vor Diebstahl oder versehentlicher Offenlegung geschützt und möglicherweise regelmäßig rotiert werden. Viele Azure-Dienste ermöglichen es Ihnen, stattdessen eine Identität in [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) zu verwenden, um Clients zu authentifizieren und auf Berechtigungen zu überprüfen, die schnell geändert und widerrufen werden können. Dies ermöglicht eine bessere Kontrolle über die Anwendungssicherheit bei geringerem Betriebsaufwand. Eine Identität kann ein menschlicher Benutzer sein, z. B. der Entwickler einer Anwendung, oder eine ausgeführte Anwendung in Azure mit einer [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md).

Einige Dienste unterstützen keine Azure Active Directory-Authentifizierung, sodass möglicherweise weiterhin Geheimnisse für Ihre Anwendungen nötig sind. Diese können jedoch in [Azure Key Vault](../key-vault/general/overview.md) gespeichert werden, wodurch sich der Verwaltungslebenszyklus für Ihre Geheimnisse vereinfachen lässt. Der Zugriff auf einen Schlüsseltresor wird ebenfalls durch Identitäten gesteuert.

Sie sollten verstehen, wie Sie nach Möglichkeit Identitäten anstelle von Geheimnissen verwenden und wie Sie in den übrigen Fällen Key Vault einsetzen. So können Sie Risiken reduzieren, den Betriebsaufwand verringern und ganz allgemein den Sicherheitsstatus für Ihre Anwendungen verbessern.

## <a name="create-a-function-app-that-uses-key-vault-for-necessary-secrets"></a>Erstellen einer Funktions-App, die Key Vault für erforderliche Geheimnisse verwendet

Azure Files ist ein Beispiel für einen Dienst, der die Authentifizierung bei Azure Active Directory für SMB-Dateifreigaben noch nicht unterstützt. Azure Files ist das Standarddateisystem für Windows-Bereitstellungen in Premium- und Verbrauchsplänen. Zwar könnten Sie [Azure Files vollständig entfernen](./storage-considerations.md#create-an-app-without-azure-files), aber dies würde zu Einschränkungen führen, die Sie möglicherweise nicht wünschen. Stattdessen verschieben Sie die Azure Files-Verbindungszeichenfolge in Azure Key Vault. Auf diese Weise wird sie zentral verwaltet, und der Zugriff wird von der Identität gesteuert.

### <a name="create-an-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz

Zunächst benötigen Sie einen Schlüsseltresor zum Speichern von Geheimnissen. Sie konfigurieren ihn für die Verwendung der [rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) von Azure, um festzulegen, wer Geheimnisse aus dem Tresor lesen kann.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen (+)** .

1. Klicken Sie auf der Seite **Ressource erstellen** auf **Sicherheit** > **Key Vault**.

1. Verwenden Sie auf der Seite **Grundlagen** die folgende Tabelle, um den Schlüsseltresor zu konfigurieren.

    | Option      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Ihr Abonnement | Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Name der neuen Ressourcengruppe, in der Sie Ihre Funktions-App erstellen. |
    | **Name des Schlüsseltresors** | Global eindeutiger Name | Der Name, der Ihren neuen Schlüsseltresor bezeichnet. Der Tresorname darf ausschließlich alphanumerische Zeichen und Bindestriche enthalten und darf nicht mit einer Zahl beginnen. |
    | **Tarif** | Standard | Optionen für die Abrechnung. Die Standardeinstellung reicht für dieses Tutorial aus. |
    |**Region**| Bevorzugte Region | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |

    Verwenden Sie die Standardauswahl für die Abschnitte „Wiederherstellungsoptionen“. 

1. Notieren Sie sich den verwendeten Namen, da Sie ihn später benötigen werden.

1. Klicken Sie auf **Weiter: Zugriffsrichtlinie**, um zur Registerkarte **Zugriffsrichtlinie** zu navigieren.

1. Wählen Sie unter **Berechtigungsmodell** die Option **Rollenbasierte Zugriffssteuerung in Azure** aus.

1. Klicken Sie auf **Überprüfen und erstellen**. Überprüfen Sie die Konfiguration, und klicken Sie dann auf **Erstellen**.

### <a name="set-up-an-identity-and-permissions-for-the-app"></a>Einrichten einer Identität und von Berechtigungen für die App

Um Azure Key Vault verwenden zu können, muss Ihre App über eine Identität verfügen, der die Berechtigung zum Lesen von Geheimnissen erteilt werden kann. Diese App verwendet eine benutzerseitig zugewiesene Identität, sodass die Berechtigungen eingerichtet werden können, bevor die App überhaupt erstellt wird. Weitere Informationen zu verwalteten Identitäten für Azure Functions finden Sie im Thema [Verwenden verwalteter Identitäten in Azure Functions](../app-service/overview-managed-identity.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen (+)** .

1. Klicken Sie auf der Seite **Ressource erstellen** auf **Identität** > **Benutzerseitig zugewiesene verwaltete Identität**.

1. Verwenden Sie auf der Seite **Grundlagen** die folgende Tabelle, um die Identität zu konfigurieren.

    | Option      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Ihr Abonnement | Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Name der neuen Ressourcengruppe, in der Sie Ihre Funktions-App erstellen. |
    |**Region**| Bevorzugte Region | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |
    | **Name** | Global eindeutiger Name | Name, der Ihre neue benutzerseitig zugewiesene Identität identifiziert. |

1. Klicken Sie auf **Überprüfen und erstellen**. Überprüfen Sie die Konfiguration, und klicken Sie dann auf **Erstellen**.

1. Nachdem die Identität erstellt wurde, navigieren Sie im Portal zu dieser Identität. Wählen Sie **Eigenschaften** aus, und notieren Sie sich die **Ressourcen-ID**, da Sie sie später benötigen werden.

1. Wählen Sie **Azure-Rollenzuweisungen** aus, und klicken Sie auf **Rollenzuweisung hinzufügen (Vorschau)** .

1. Verwenden Sie auf der Seite **Rollenzuweisung hinzufügen (Vorschau)** die in der folgenden Tabelle gezeigten Optionen.

    | Option      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ | ---------------- | ----------- |
    | **Umfang** |  Key Vault |  „Scope“ bezeichnet eine Gruppe von Ressourcen, für die die Rollenzuweisung gilt. Der Bereich umfasst Ebenen, die auf niedrigeren Ebenen geerbt werden. Wenn Sie beispielsweise einen Abonnementbereich auswählen, gilt die Rollenzuweisung für alle Ressourcengruppen und Ressourcen im Abonnement. |
    |**Abonnement**| Ihr Abonnement | Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    |**Ressource**| Ihr Schlüsseltresor | Der Schlüsseltresor, den Sie zuvor erstellt haben. |
    | **Rolle** | Key Vault-Geheimnisbenutzer | Eine Rolle ist eine Sammlung von Berechtigungen, die erteilt werden. „Key Vault-Geheimnisbenutzer“ gewährt der Identität die Berechtigung, Geheimniswerte aus dem Tresor zu lesen. |

1. Wählen Sie **Speichern** aus. Es kann ein oder zwei Minuten dauern, bis die Rolle angezeigt wird, wenn Sie die Liste der Rollenzuweisungen für die Identität aktualisieren.

Die Identität kann jetzt im Tresor gespeicherte Geheimnisse lesen. Später in diesem Tutorial fügen Sie zusätzliche Rollenzuweisungen für verschiedene Zwecke hinzu.

### <a name="generate-a-template-for-creating-a-function-app"></a>Generieren einer Vorlage zum Erstellen einer Funktions-App

Die Portaloberfläche zum Erstellen einer Funktions-App interagiert nicht mit Azure Key Vault, sodass Sie eine Azure Resource Manager-Vorlage generieren und bearbeiten müssen. Anhand dieser Vorlage können Sie Ihre Funktions-App erstellen und dabei auf die Azure Files-Verbindungszeichenfolge aus Ihrem Schlüsseltresor verweisen.

> [!IMPORTANT]
> Erstellen Sie die Funktions-App erst, nachdem Sie die ARM-Vorlage bearbeitet haben. Die Azure Files-Konfiguration muss zum Zeitpunkt der App-Erstellung eingerichtet werden.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen (+)** .

1. Klicken Sie auf der Seite **Ressource erstellen** auf **Compute** > **Funktions-App**.

1. Verwenden Sie auf der Seite **Grundlagen** die folgende Tabelle, um die Funktions-App zu konfigurieren.

    | Option      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Ihr Abonnement | Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Name der neuen Ressourcengruppe, in der Sie Ihre Funktions-App erstellen. |
    | **Name der Funktions-App** | Global eindeutiger Name | Der Name, der Ihre neue Funktionen-App bezeichnet Gültige Zeichen sind `a-z` (Groß-/Kleinschreibung nicht beachtet), `0-9` und `-`.  |
    |**Veröffentlichen**| Code | Wählen Sie aus, ob Sie Codedateien oder einen Docker-Container veröffentlichen. |
    | **Runtimestapel** | .NET | In diesem Tutorial wird .NET verwendet. |
    |**Region**| Bevorzugte Region | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |

1. Klicken Sie auf **Überprüfen und erstellen**. Ihre App verwendet die Standardwerte auf der Seite **Hosting** und **Überwachung**. Sie können gerne die Standardoptionen überprüfen, die in der von Ihnen generierten ARM-Vorlage enthalten sein werden. 

1. Statt hier Ihre Funktions-App zu erstellen, wählen Sie rechts neben der Schaltfläche **Weiter** die Option **Vorlage für Automatisierung herunterladen** aus.

1. Wählen Sie auf der Vorlagenseite die Option **Bereitstellen** und dann auf der Seite „Benutzerdefinierte Bereitstellung“ die Option **Vorlage bearbeiten** aus.

    :::image type="content" source="./media/functions-identity-connections-tutorial/function-app-portal-template-deploy-button.png" alt-text="Screenshot: Position der Schaltfläche „Bereitstellen“ im oberen Bereich des Bildschirms „Vorlage“":::

### <a name="edit-the-template"></a>Bearbeiten der Vorlage

Jetzt bearbeiten Sie die Vorlage, um die Azure Files-Verbindungszeichenfolge in Key Vault zu speichern und Ihrer Funktions-App den Verweis darauf zu erlauben. Stellen Sie vor dem Fortfahren sicher, dass Sie über die folgenden Werte aus den früheren Abschnitten verfügen:

- Die Ressourcen-ID der benutzerseitig zugewiesenen Identität
- Der Name Ihres Schlüsseltresors.

> [!NOTE]
> Wenn Sie eine vollständige Vorlage für die Automatisierung erstellen würden, sollten Sie Definitionen für die Identitäts- und Rollenzuweisungsressourcen mit den entsprechenden `dependsOn`-Klauseln hinzufügen. Dadurch würden die früheren Schritte ersetzt, in denen das Portal verwendet wurde. Lesen Sie den [Azure Resource Manager-Leitfaden](../azure-resource-manager/templates/syntax.md) und die Dokumentation für die einzelnen Dienste.


1. Suchen Sie im Editor den Beginn des `resources`-Arrays. Fügen Sie vor der Definition der Funktions-App den folgenden Abschnitt hinzu, in dem die Azure Files-Verbindungszeichenfolge in Key Vault eingefügt wird. Ersetzen Sie VAULT_NAME durch den Namen Ihres Schlüsseltresors.

    ```json
    {
        "type": "Microsoft.KeyVault/vaults/secrets",
        "apiVersion": "2016-10-01",
        "name": "VAULT_NAME/azurefilesconnectionstring",
        "properties": {
            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
        },
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
        ]
    },
    ``` 
    
1. Fügen Sie in der Definition für die Funktions-App-Ressource (in der `type` auf `Microsoft.Web/sites` festgelegt ist) dem Array `dependsOn` den Wert `Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring` hinzu. Ersetzen Sie VAULT_NAME erneut durch den Namen Ihres Schlüsseltresors. Dadurch wird Ihre App erst erstellt, nachdem dieses Geheimnis definiert wurde. Das Array `dependsOn` sollte dem folgenden Beispiel entsprechen.

    ```json
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "tags": null,
            "dependsOn": [
                "microsoft.insights/components/idcxntut",
                "Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring",
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            // ...
        }
    ```

1. Fügen Sie den `identity`-Block aus dem folgenden Beispiel in die Definition für Ihre Funktions-App-Ressource ein. Ersetzen Sie IDENTITY_RESOURCE_ID durch die Ressourcen-ID Ihrer benutzerseitig zugewiesenen Identität.

    ```json
    {
        "apiVersion": "2018-11-01",
        "name": "[parameters('name')]",
        "type": "Microsoft.Web/sites",
        "kind": "functionapp",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities": {
                "IDENTITY_RESOURCE_ID": {}
            }
        },
        "tags": null,
        // ...
    }
    ```

    Dieser `identity`-Block richtet außerdem eine systemseitig zugewiesene Identität ein, die Sie später in diesem Tutorial verwenden werden.

1. Fügen Sie dem `properties`-Objekt die Eigenschaft `keyVaultReferenceIdentity` hinzu, wie im folgenden Beispiel gezeigt. Ersetzen Sie IDENTITY_RESOURCE_ID durch die Ressourcen-ID Ihrer benutzerseitig zugewiesenen Identität.

    ```json
    {
        // ...
         "properties": {
                "name": "[parameters('name')]",
                "keyVaultReferenceIdentity": "IDENTITY_RESOURCE_ID",
                // ...
         }
    }
    ```

    Sie benötigen diese Konfiguration, da für eine App mehrere benutzerseitig zugewiesene Identitäten konfiguriert sein können. Wenn Sie eine benutzerseitig zugewiesene Identität verwenden möchten, müssen Sie über eine ID angeben, welche verwendet werden soll. Dies gilt nicht für systemseitig zugewiesene Identitäten, da eine App immer nur über eine Identität verfügt. Viele Features, die verwaltete Identitäten verwenden, gehen standardmäßig von der systemseitig zugewiesenen Identität aus.

1. Suchen Sie jetzt das JSON-Objekt, das die Anwendungseinstellung `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` definiert. Es sieht aus wie im folgenden Beispiel gezeigt:

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
    },
    ```

1. Ersetzen Sie das Feld `value` durch einen Verweis auf das Geheimnis, wie im folgenden Beispiel gezeigt. Ersetzen Sie VAULT_NAME durch den Namen Ihres Schlüsseltresors.

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('@Microsoft.KeyVault(SecretUri=', reference(resourceId('Microsoft.KeyVault/vaults/secrets', 'VAULT_NAME', 'azurefilesconnectionstring')).secretUri, ')')]"
    },
    ```

1. Klicken Sie auf **Speichern**, um die aktualisierte ARM-Vorlage zu speichern.

### <a name="deploy-the-modified-template"></a>Bereitstellen der geänderten Vorlage

1. Stellen Sie sicher, dass die Erstellungsoptionen, einschließlich der **Ressourcengruppe**, weiterhin korrekt sind, und wählen Sie **Überprüfen + erstellen** aus.

1. Nachdem Ihre Vorlage überprüft wurde, notieren Sie sich Ihren **Speicherkontonamen**, da Sie dieses Konto später verwenden werden. Klicken Sie abschließend auf **Erstellen**, um Ihre Azure-Ressourcen zu erstellen und Ihren Code in der Funktions-App bereitzustellen. 

1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressourcengruppe wechseln** und dann die neue Funktions-App aus. 

Glückwunsch! Sie haben Ihre Funktions-App erfolgreich erstellt, um auf die Azure Files-Verbindungszeichenfolge aus Azure Key Vault zu verweisen.

Wenn Ihre App einen Verweis auf ein Geheimnis hinzufügen muss, müssen Sie nur eine neue Anwendungseinstellung definieren, die auf den in Key Vault gespeicherten Wert verweist. Weitere Informationen hierzu finden Sie unter [Key Vault-Verweise für Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

> [!TIP]
> Die [Application Insights-Verbindungszeichenfolge](../azure-monitor/app/sdk-connection-string.md) und der darin enthaltene Instrumentierungsschlüssel werden nicht als Geheimnisse betrachtet und können mithilfe der Berechtigungen der Rolle [Leser](../role-based-access-control/built-in-roles.md#reader) aus App Insights abgerufen werden. Sie können, aber müssen sie nicht in Key Vault verschieben.

## <a name="use-managed-identity-for-azurewebjobsstorage-preview"></a>Verwenden der verwalteten Identität für AzureWebJobsStorage (Vorschau)

Als Nächstes verwenden Sie die systemseitig zugewiesene Identität, die Sie in den vorherigen Schritten für die `AzureWebJobsStorage`-Verbindung konfiguriert haben. `AzureWebJobsStorage` wird von der Functions-Runtime und von mehreren Triggern und Bindungen verwendet, um mehrere ausgeführte Instanzen zu koordinieren. Dieser Wert ist für den Betrieb Ihrer Funktions-App erforderlich, und ebenso wie Azure Files wird er standardmäßig mit einer Verbindungszeichenfolge konfiguriert, wenn Sie eine neue Funktions-App erstellen.

### <a name="grant-the-system-assigned-identity-access-to-the-storage-account"></a>Gewähren des Zugriffs einer systemseitig zugewiesenen Identität auf das Speicherkonto

Ähnlich wie bei den Schritten, die Sie zuvor mit der benutzerseitig zugewiesenen Identität und Ihrem Schlüsseltresor unternommen haben, erstellen Sie jetzt eine Rollenzuweisung, die der systemseitig zugewiesenen Identität Zugriff auf Ihr Speicherkonto gewährt.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Speicherkonto, das zuvor mit Ihrer Funktions-App erstellt wurde.

1. Wählen Sie **Access Control (IAM)** aus. Hier können Sie anzeigen und konfigurieren, wer auf die Ressource zugreifen kann.

1. Klicken Sie auf **Hinzufügen**, und wählen Sie anschließend **Rollenzuweisung hinzufügen** aus.

1. Suchen Sie nach **Besitzer von Speicherblobdaten**, wählen Sie diesen Eintrag aus, und klicken Sie auf **Weiter**.

1. Wählen Sie auf der Registerkarte **Mitglieder** unter **Zugriff zuweisen zu** die Option **Verwaltete Identität** aus.

1. Klicken Sie auf **Mitglieder auswählen**, um den Bereich **Verwaltete Identitäten auswählen** zu öffnen.

1. Vergewissern Sie sich, dass unter **Abonnement** das Abonnement aufgeführt ist, in dem Sie die Ressourcen zuvor erstellt haben.

1. Wählen Sie in der Auswahl **Verwaltete Identität** aus der Kategorie **Systemseitig zugewiesene verwaltete Identität** die Option **Funktions-App** aus. Neben der Bezeichnung „Funktions-App“ wird möglicherweise eine Zahl in Klammern angezeigt, die die Anzahl der Apps im Abonnement mit systemseitig zugewiesenen Identitäten angibt.

1. Ihre App sollte in einer Liste unterhalb der Eingabefelder angezeigt werden. Andernfalls können Sie das Feld **Auswählen** verwenden, um die Ergebnisse nach dem Namen Ihrer App zu filtern.

1. Klicken Sie auf Ihre Anwendung. Sie sollte nach unten in den Abschnitt **Ausgewählte Mitglieder** verschoben werden. Klicken Sie auf **Auswählen**.

1. Klicken Sie im Bildschirm **Rollenzuweisung hinzufügen** auf **Überprüfen + zuweisen**. Überprüfen Sie die Konfiguration, und klicken Sie dann auf **Überprüfen + zuweisen**.

### <a name="edit-the-azurewebjobsstorage-configuration"></a>Bearbeiten der AzureWebJobsStorage-Konfiguration

Als Nächstes aktualisieren Sie Ihre Funktions-App so, dass sie bei Nutzung des Blobdiensts für den Hostspeicher die systemseitig zugewiesene Identität verwendet.

> [!IMPORTANT]
> Die `AzureWebJobsStorage`-Konfiguration wird von einigen Triggern und Bindungen eingesetzt, und diese Erweiterungen müssen ebenfalls dazu in der Lage sein, identitätsbasierte Verbindungen zu verwenden. Apps, die Blobtrigger oder Event Hub-Trigger verwenden, müssen diese Erweiterungen möglicherweise aktualisieren. Da für diese App keine Funktionen definiert wurden, gibt es noch kein Problem. Weitere Informationen zu dieser Anforderung finden Sie unter [Herstellen einer Verbindung mit Hostspeicher mit einer Identität (Vorschau)](./functions-reference.md#connecting-to-host-storage-with-an-identity-preview).
>
> Auf ähnliche Weise wird `AzureWebJobsStorage` für Bereitstellungsartefakte eingesetzt, wenn ein serverseitiger Build in Linux-Verbrauch verwendet wird. Wenn Sie identitätsbasierte Verbindungen für `AzureWebJobsStorage` in Linux-Verbrauch aktivieren, muss die Bereitstellung über [ein externes Bereitstellungspaket](/run-functions-from-deployment-package) erfolgen.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Funktions-App.

1. Klicken Sie unter **Einstellungen** auf **Konfiguration**.

1. Klicken Sie neben der **AzureWebJobsStorage**-Anwendungseinstellung auf die Schaltfläche **Bearbeiten**, und ändern Sie sie basierend auf den folgenden Werten.

    | Option      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ | ---------------- | ----------- |
    | **Name** |  AzureWebJobsStorage__accountName | Aktualisieren Sie den Namen von **AzureWebJobsStorage** auf den genauen Namen: `AzureWebJobsStorage__accountName`. Diese Einstellung weist den Host an, die Identität zu verwenden, anstatt nach einem gespeicherten Geheimnis zu suchen. Die neue Einstellung verwendet einen doppelten Unterstrich (`__`), bei dem es sich in den Anwendungseinstellungen um ein Sonderzeichen handelt.  |
    | **Wert** | Name Ihres Kontos | Aktualisieren Sie den Namen von der Verbindungszeichenfolge auf nur Ihren **AccountName**. |

    Durch diese Konfiguration wird das System darüber informiert, dass es zum Herstellen einer Verbindung mit der Ressource eine Identität verwenden soll.

1. Klicken Sie auf **OK** und anschließend auf **Speichern** > **Weiter**, um Ihre Änderungen zu speichern. 

Sie haben die Anforderung der Speicherverbindungszeichenfolge für AzureWebJobsStorage entfernt, indem Sie Ihre App so konfiguriert haben, dass sie stattdessen über verwaltete Identitäten eine Verbindung mit Blobs herstellen kann.  

## <a name="next-steps"></a>Nächste Schritte 

In diesem Tutorial wurde gezeigt, wie Sie eine Funktions-App erstellen, ohne Geheimnisse in der zugehörigen Konfiguration zu speichern.

Im nächsten Tutorial erfahren Sie, wie Sie eine Identität in Trigger- und Bindungsverbindungen verwenden.

> [!div class="nextstepaction"]
> [Verwenden identitätsbasierter Verbindungen anstelle von Geheimnissen bei Triggern und Bindungen]

[Verwenden identitätsbasierter Verbindungen anstelle von Geheimnissen bei Triggern und Bindungen]: ./functions-identity-based-connections-tutorial-2.md
