---
title: Verwenden identitätsbasierter Verbindungen mit Azure Functions-Triggern und -Bindungen
ms.service: azure-functions
description: Erfahren Sie, wie Sie beim Herstellen einer Verbindung mit einer Service Bus-Warteschlange mithilfe von Azure Functions identitätsbasierte Verbindungen anstelle von Verbindungszeichenfolgen verwenden.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 5a3eb4dc6006d9072abac95b7940692e8af737a5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271174"
---
# <a name="tutorial-use-identity-based-connections-instead-of-secrets-with-triggers-and-bindings"></a>Tutorial: Verwenden identitätsbasierter Verbindungen anstelle von Geheimnissen bei Triggern und Bindungen

Dieses Tutorial zeigt Ihnen, wie Sie Azure Functions so konfigurieren, dass beim Herstellen einer Verbindung mit Azure Service Bus-Warteschlangen verwaltete Identitäten anstelle von Geheimnissen verwendet werden, die in den Einstellungen der Funktions-App gespeichert sind. Das Tutorial ist eine Fortsetzung des Tutorials [Erstellen einer Funktions-App ohne Standardspeichergeheimnisse in der Definition][vorheriges Tutorial]. Weitere Informationen zu identitätsbasierten Verbindungen finden Sie unter [Konfigurieren einer identitätsbasierten Verbindung](functions-reference.md#configure-an-identity-based-connection).

Obwohl die gezeigten Verfahren in der Regel für alle Sprachen funktionieren, werden in diesem Tutorial derzeit speziell C#-Klassenbibliotheksfunktionen für Windows unterstützt. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen Sie einen Service Bus-Namespace und eine Service Bus-Warteschlange.
> * Konfigurieren Ihrer Funktions-App mit einer verwalteten Identität
> * Erstellen einer Rollenzuweisung, die dieser Identität die Berechtigung zum Lesen aus der Service Bus-Warteschlange erteilt
> * Erstellen und Bereitstellen einer Funktions-App mit einem Service Bus-Trigger
> * Überprüfen Ihrer identitätsbasierten Verbindung mit Service Bus

## <a name="prerequisite"></a>Voraussetzung

Absolvieren Sie das vorherige Tutorial: [Erstellen einer Funktions-App ohne Standardspeichergeheimnisse in der Definition][vorheriges Tutorial].

## <a name="create-a-service-bus-and-queue"></a>Erstellen einer Service Bus-Instanz und -Warteschlange

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen**.

1. Klicken Sie auf der Seite **Ressource erstellen** auf **Integration** > **Service Bus**.

1. Verwenden Sie auf der Registerkarte **Grundlagen** die folgende Tabelle, um die Einstellungen für den Service Bus-Namespace zu konfigurieren. Behalten Sie für die übrigen Optionen die Standardwerte bei.

    | Option      | Vorgeschlagener Wert  | Beschreibung      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. |
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Die Ressourcengruppe aus, die Sie mit Ihrer Funktions-App erstellt haben. |
    | **Namespacename** | Global eindeutiger Name | Der Namespace Ihrer Instanz, über die Ihre Funktion ausgelöst werden soll. Da der Namespace öffentlich zugänglich ist, müssen Sie einen Namen verwenden, der in Azure global eindeutig ist. Der Name muss außerdem zwischen 6 und 50 Zeichen lang sein, kann nur alphanumerische Zeichen und Bindestriche enthalten und darf nicht mit einer Zahl beginnen. |
    | **[Location](https://azure.microsoft.com/regions/)** | myFunctionRegion | Die Region, in der Sie Ihre Funktions-App erstellt haben. |
    | **Preisstufe** | Basic | Der Basistarif von Service Bus. |

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie **Erstellen** aus, wenn die Validierung erfolgreich war.

1. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.

1. Wählen Sie in Ihrem neuen Service Bus-Namespace **+ Warteschlange** aus, um eine Warteschlange hinzuzufügen.

1. Geben Sie `myinputqueue` als Namen der neuen Warteschlange ein, und klicken Sie auf **Erstellen**.

Nachdem Sie nun über eine Warteschlange verfügen, werden Sie der verwalteten Identität Ihrer Funktions-App eine Rollenzuweisung hinzufügen.

## <a name="configure-your-service-bus-trigger-with-a-managed-identity"></a>Konfigurieren Ihres Service Bus-Triggers mit einer verwalteten Identität

Um Service Bus-Trigger mit identitätsbasierten Verbindungen zu verwenden, müssen Sie die Rollenzuweisung **Azure Service Bus-Datenempfänger** zur verwalteten Identität in Ihrer Funktions-App hinzufügen. Diese Rolle ist erforderlich, wenn verwaltete Identitäten für eine Auslösung über den Service Bus-Namespace verwendet werden. Sie können dieser Rolle auch Ihr eigenes Konto hinzufügen, um während lokaler Tests eine Verbindung mit dem Service Bus-Namespace herstellen zu können.

> [!NOTE]
> Die Rollenanforderungen für die Verwendung identitätsbasierter Verbindungen variieren je nach Dienst und Art der Verbindung. Die Anforderungen variieren außerdem abhängig von Trigger, Eingabe- und Ausgabebindungen. Weitere Informationen zu spezifischen Rollenanforderungen finden Sie in der Trigger- und Bindungsdokumentation für den Dienst.

1. Wählen Sie in Ihrem soeben erstellten Service Bus-Namespace die Option **Zugriffssteuerung (IAM)** aus. Mithilfe dieser Optionen können Sie anzeigen und konfigurieren, wer Zugriff auf die Ressource hat.  

1. Klicken Sie auf **Hinzufügen**, und wählen Sie **Rollenzuweisung hinzufügen** aus.

1. Suchen Sie nach **Azure Service Bus-Datenempfänger**, wählen Sie die Einstellung aus, und klicken Sie auf **Weiter**.

1. Wählen Sie auf der Registerkarte **Mitglieder** unter **Zugriff zuweisen zu** die Option **Verwaltete Identität** aus.

1. Klicken Sie auf **Mitglieder auswählen**, um den Bereich **Verwaltete Identitäten auswählen** zu öffnen.

1. Vergewissern Sie sich, dass unter **Abonnement** das Abonnement aufgeführt ist, in dem Sie zuvor die Ressource erstellt haben.

1. Wählen Sie in der Auswahl **Verwaltete Identität** aus der Kategorie **Systemseitig zugewiesene verwaltete Identität** die Option **Funktions-App** aus. Neben der Bezeichnung „Funktions-App“ wird möglicherweise eine Zahl in Klammern angezeigt, die die Anzahl von Apps im Abonnement mit systemseitig zugewiesenen Identitäten angibt.

1. Ihre App sollte in einer Liste unterhalb der Eingabefelder angezeigt werden. Andernfalls können Sie das Feld **Auswählen** verwenden, um die Ergebnisse nach dem Namen Ihrer App zu filtern.

1. Klicken Sie auf Ihre Anwendung. Sie sollte nach unten in den Abschnitt **Ausgewählte Mitglieder** verschoben werden. Klicken Sie auf **Auswählen**.

1. Klicken Sie im Bildschirm **Rollenzuweisung hinzufügen** auf **Überprüfen + zuweisen**. Überprüfen Sie die Konfiguration, und klicken Sie dann auf **Überprüfen + zuweisen**.

Sie haben Ihrer Funktions-App mithilfe von verwalteten Identitäten Zugriff auf den Service Bus-Namespace erteilt.

## <a name="connect-to-service-bus-in-your-function-app"></a>Herstellen einer Verbindung mit Service Bus in Ihrer Funktions-App

1. Suchen Sie im Portal nach der Funktions-App, die Sie im [vorheriges Tutorial] erstellt haben, oder suchen Sie sie auf der Seite **Funktions-App**.

1. Wählen Sie in Ihrer Funktions-App unter **Einstellungen** die Option **Konfigurationen** aus.

1. Wählen Sie unter **Anwendungseinstellungen** die Option **+ Neue Anwendungseinstellung** aus, um die neue Einstellung in der folgenden Tabelle zu erstellen.

    | Name      | Wert  | Beschreibung |
    | ------------ | ---------------- | ----------- |
    | **ServiceBusConnection__fullyQualifiedNamespace** | <SERVICE_BUS_NAMESPACE>.servicebus.windows.net | Durch diese Einstellung werden bei der Verbindungsherstellung zwischen Ihrer Funktions-App und Service Bus identitätsbasierte Verbindungen anstelle von Geheimnissen verwendet. |

1. Klicken Sie nach der Erstellung der zwei Einstellungen auf **Speichern** > **Bestätigen**.

Nachdem Sie die Funktions-App auf die Verbindung mit dem Service Bus-Namespace unter Verwendung einer verwalteten Identität vorbereitet haben, können Sie Ihrem lokalen Projekt eine neue Funktion hinzufügen, die einen Service Bus-Trigger verwendet.

## <a name="add-a-service-bus-triggered-function"></a>Hinzufügen einer über Service Bus ausgelösten Funktion

1. Führen Sie den Befehl `func init` wie folgt aus, um in einem Ordner mit dem Namen LocalFunctionProj ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Navigieren Sie zum Projektordner:

    ```console
    cd LocalFunctionProj
    ```

1. Führen Sie im Stammprojektordner die folgenden Befehle aus:

    ```command
    dotnet remove package Microsoft.Azure.Webjobs.Extensions.ServiceBus
    dotnet add package Microsoft.Azure.Webjobs.Extensions.ServiceBus --prerelease
    ```

    Dadurch wird die Standardversion des Service Bus-Erweiterungspakets durch eine Version ersetzt, die verwaltete Identitäten unterstützt.

1. Führen Sie den folgenden Befehl aus, um dem Projekt eine über Service Bus ausgelöste Funktion hinzuzufügen:

    ```csharp
    func new --name ServiceBusTrigger --template ServiceBusQueueTrigger 
    ```

    Der Befehl fügt Code für einen neuen Service Bus-Trigger und einen Verweis auf das Erweiterungspaket hinzu. Sie müssen eine Service Bus-Namespace-Verbindungseinstellung für diesen Trigger hinzufügen.

1. Öffnen Sie die neue Projektdatei „ServiceBusTrigger.cs“, und ersetzen Sie die `ServiceBusTrigger`-Klasse durch den folgenden Code:

    ```csharp
    public static class ServiceBusTrigger
    {
        [FunctionName("ServiceBusTrigger")]
        public static void Run([ServiceBusTrigger("myinputqueue", 
            Connection = "ServiceBusConnection")]string myQueueItem, ILogger log)
        {
            log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
        }
    }
    ```

    In diesem Codebeispiel wird der Warteschlangenname in `myinputqueue` geändert – den Namen der zuvor erstellten Warteschlange. Außerdem wird der Name der Service Bus-Verbindung auf `ServiceBusConnection` festgelegt. Dies ist der Service Bus-Namespace, der von der identitätsbasierten Verbindung `ServiceBusConnection__fullyQualifiedNamespace` verwendet wird, die Sie im Portal konfiguriert haben.

> [!NOTE]  
> Wenn Sie jetzt versuchen, Ihre Funktionen mit `func start` auszuführen, erhalten Sie einen Fehler. Dies liegt daran, dass Sie lokal noch keine identitätsbasierte Verbindung definiert haben. Wenn Sie Ihre Funktion lokal ausführen möchten, legen Sie die App-Einstellung `ServiceBusConnection__fullyQualifiedNamespace` in `local.settings.json` so wie [im vorherigen Abschnitt](#connect-to-service-bus-in-your-function-app) fest. Darüber hinaus müssen Sie die Rolle Ihrer Entwickleridentität zuweisen. Weitere Informationen finden Sie in der [Dokumentation zur lokalen Entwicklung mit identitätsbasierten Verbindungen](./functions-reference.md#local-development-with-identity-based-connections).

## <a name="publish-the-updated-project"></a>Veröffentlichen Sie das aktualisierte Projekt.

1. Führen Sie den folgenden Befehl aus, um die für das Bereitstellungspaket benötigten Dateien lokal zu generieren:

    ```console
    dotnet publish --configuration Release
    ```

1. Navigieren Sie zum Unterordner `\bin\Release\netcoreapp3.1\publish`, und erstellen Sie eine ZIP-Datei aus ihrem Inhalt.

1. Veröffentlichen Sie ZIP-Datei, indem Sie den folgenden Befehl ausführen und dabei die Parameter `FUNCTION_APP_NAME`, `RESOURCE_GROUP_NAME` und `PATH_TO_ZIP` entsprechend ersetzen:

    ```azurecli
    az functionapp deploy -n FUNCTION_APP_NAME -g RESOURCE_GROUP_NAME --src-path PATH_TO_ZIP
    ```

Nachdem Sie nun die Funktions-App mit dem neuen Trigger aktualisiert haben, können Sie überprüfen, ob sie mit der Identität funktioniert.

## <a name="validate-your-changes"></a>Validieren Ihrer Änderungen

1. Suchen Sie im Portal nach `Application Insights`, und wählen Sie unter **Dienste** die Option **Application Insights** aus.  

1. Suchen Sie in **Application Insights** nach Ihrer benannten Instanz.

1. Klicken Sie in Ihrer Instanz unterhalb von **Untersuchen** auf **Livemetriken**.

1. Lassen Sie die vorherige Registerkarte geöffnet, und öffnen Sie das Azure-Portal auf einer neuen Registerkarte. Navigieren Sie auf der neuen Registerkarte zu Ihrem Service Bus-Namespace, und wählen Sie im linken Blatt **Warteschlangen** aus.

1. Wählen Sie Ihre Warteschlange namens `myinputqueue` aus.

1. Wählen Sie im linken Blatt **Service Bus Explorer** aus.

1. Senden sie eine Testnachricht.

1. Wählen Sie die geöffnete Registerkarte **Livemetriken** aus, und verfolgen Sie die Ausführung der Service Bus-Warteschlange.

Glückwunsch! Sie haben Ihren Service Bus-Warteschlangentrigger erfolgreich mit einer verwalteten Identität eingerichtet!

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Funktions-App mit identitätsbasierten Verbindungen erstellt.

Verwenden Sie die folgenden Links, um mehr über Azure Functions mit identitätsbasierten Verbindungen zu erfahren:

- [Verwaltete Identität in Azure Functions](../app-service/overview-managed-identity.md)
- [Identitätsbasierte Verbindungen in Azure Functions](./functions-reference.md#configure-an-identity-based-connection)
- [Funktionsdokumentation für die lokale Entwicklung](./functions-reference.md#local-development-with-identity-based-connections)

[vorheriges Tutorial]: ./functions-identity-based-connections-tutorial.md
