---
title: Erstellen von Parametern für Werte in Workflows mit nur einem Mandanten
description: Definieren von Parametern für Werte in Workflows, die sich zwischen Bereitstellungsumgebungen in Azure Logic Apps-Instanzen mit nur einem Mandanten unterscheiden.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 08/09/2021
ms.openlocfilehash: a29eda23d12ca07057ff1081ae8d9bc4cfdc56ed
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137035"
---
# <a name="create-parameters-to-use-in-workflows-across-environments-in-single-tenant-azure-logic-apps"></a>Erstellen von Parametern für die Verwendung in Workflows umgebungsübergreifend in Azure Logic Apps-Instanzen mit nur einem Mandanten

In Azure Logic Apps können Sie Werte abstrahieren, die sich in Ihren Workflows in Ihren Entwicklungs-, Test- und Produktionsumgebungen ändern können, indem Sie *Parameter* definieren. Wenn Sie stattdessen Parameter verwenden, können Sie sich mehr auf das Entwerfen Ihrer Workflows konzentrieren und Ihre umgebungsspezifischen Variablen später einfügen.

In diesem Artikel wird die Funktionsweise von Parametern in Azure Logic Apps-Instanzen mit nur einem Mandanten eingeführt sowie das Bearbeiten, Verweisen und Verwalten von Umgebungsvariablen mithilfe der Parametererfahrung für nur einen Mandanten.

<a name="parameters-introduction"></a>

## <a name="parameters-in-single-tenant-versus-multi-tenant"></a>Parameter in Instanzen mit nur einem Mandanten im Vergleich zu mehrinstanzenfähigen Instanzen

Wenn Sie mit *mehrinstanzenfähigen* Azure Logic Apps-Instanzen gearbeitet haben, sind Sie möglicherweise bereits mit Parametern vertraut. Das Parametrisieren Ihrer Workfloweingaben in Azure Logic Apps-Instanzen mit nur einem Mandanten funktioniert ähnlich wie bei mehrinstanzenfähigen Azure Logic Apps-Instanzen, mit einem Hauptunterschied.

Beispielsweise können Sie sowohl im Einzelinstanz- als auch im mehrinstanzenfähigen Dienst Parameter definieren, wenn Sie im Workflow-Designer arbeiten. Nachdem Sie den Parameter definiert haben, können Sie aus jedem Workflow oder jeder Verbindung, der/die sich in *derselben* Logik-App-Ressource befindet, auf diesen Parameter verweisen. Im *mehrinstanzenfähigen* Dienst definieren Sie jedoch nach dem Erstellen und Verwenden von Parametern im Designer die Umgebungsvariablen in Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) und in den Vorlagenparameterdateien und legen sie dort fest. In diesem Szenario müssen Sie die Parameter *bei der Bereitstellung* definieren und festlegen, was bedeutet, dass Sie die ARM-Vorlage Ihrer Logik-App erneut bereitstellen müssen, auch wenn Sie nur eine Variable ändern müssen.

Im Vergleich dazu können Sie mit dem Dienst *mit nur einem Mandanten* mithilfe von Parametern *und* App-Einstellungen sowohl zur Laufzeit als auch zur Bereitstellungszeit mit Umgebungsvariablen arbeiten. In Azure Logic Apps-Instanzen mit nur einem Mandanten enthalten App-Einstellungen globale Konfigurationsoptionen für *alle Workflows* in derselben Logik-App. Weitere Informationen finden Sie unter [Bearbeiten von Host- und App-Einstellungen für Logik-Apps, die auf einem einzelnen Mandanten basieren](edit-app-settings-host-settings.md).

Beispielsweise können Sie App-Einstellungen für eine Integration in Azure Key Vault und für das [direkte Verweisen auf sichere Zeichenfolgen](../app-service/app-service-key-vault-references.md) wie Verbindungszeichenfolgen und Schlüssel verwenden. Ähnlich wie bei ARM-Vorlagen, bei denen Sie Umgebungsvariablen zum Bereitstellungszeitpunkt definieren können, können Sie App-Einstellungen in der [Workflowdefinition für Ihre Logik-App](/azure/templates/microsoft.logic/workflows) definieren. Anschließend können Sie dynamisch generierte Infrastrukturwerte erfassen (z. B. Verbindungsendpunkte oder Speicherzeichenfolgen).

App-Einstellungen haben jedoch Größenlimits, und aus bestimmten Azure Logic Apps-Bereichen kann nicht auf sie verwiesen werden. Parameter bieten eine größere Bandbreite von Anwendungsfällen als App-Einstellungen, z. B. Unterstützung für große Werte und komplexe Objekte.

Wenn Sie beispielsweise Visual Studio Code als lokales Entwicklungstool zum lokalen Ausführen von Workflows verwenden, können Sie in Ihrem Logik-App-Projekt Parameter mithilfe der Datei **parameters.json** definieren. Sie können dann aus jedem Workflow in der Datei **workflow.json** Ihres Projekts oder aus jedem Verbindungsobjekt in der Datei **connections.json** Ihres Projekts auf einen beliebigen Parameter in dieser Parameterdatei verweisen. In der folgenden Liste werden einige häufige Anwendungsfälle beschrieben:

* Sie verfügen über eine Testparameterdatei, die alle Werte enthält, die Sie während Tests verwenden. Bei der Bereitstellung können Sie Ihre Testparameterdatei durch Ihre Produktionsparameterdatei ersetzen.

* Sie parametrisieren verschiedene Teile Ihrer **connections.json**-Datei. Anschließend können Sie Ihre **connections.json**-Datei in der Quellcodeverwaltung überprüfen und dann alle Verbindungen über Ihre **parameters.json**-Datei verwalten.

* Sie parametrisieren komplexe Objekte wie das `authentication`-JSON-Objekt. Sie können beispielsweise den `authentication`-Objektwert durch eine Zeichenfolge ersetzen, die einen einzelnen Parameterausdruck enthält, z. B. `@parameters('api-auth')`.

* Sie überprüfen und bearbeiten die App-Einstellungen in der Datei **local.settings.json** Ihres Projekts. Anschließend können Sie in Ihren Parametern auf diese App-Einstellungen verweisen.

> [!NOTE]
> Als allgemeine Empfehlung sollten Sie die Verwendung von Parametern als Standardvorgehensweise zum Parametrisieren von Werten erwägen, nicht die Verwendung von App-Einstellungen. Wenn Sie sichere Schlüssel oder Zeichenfolgen speichern müssen, können Sie auf diese Weise die Empfehlung befolgen, von Ihren Parametern aus auf App-Einstellungen zu verweisen. Wenn Sie möchten, können Sie beide Optionen in Ihrer Lösung verwenden, indem Sie mithilfe von Parametern auf App-Einstellungen verweisen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ein [Logik-App-Workflow, der in einer Azure Logic Apps-Instanz mit nur einem Mandanten gehostet wird](single-tenant-overview-compare.md).

  Wenn Sie keine Logik-App haben, [erstellen Sie Ihre Logik-App (Standard) im Azure-Portal](create-single-tenant-workflows-azure-portal.md) oder [in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

## <a name="define-use-and-edit-parameters"></a>Definieren, Verwenden und Bearbeiten von Parametern

### <a name="azure-portal"></a>Azure-Portal

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre auf einem einzelnen Mandanten basierende Logik-App. Wählen Sie unter **Workflows** Ihren Workflow aus, und öffnen Sie ihn im Designer.

1. Wählen Sie auf der Designer-Symbolleiste **Parameter** aus.

   ![Screenshot des Azure-Portals, des Workflow-Designers und des ausgewählten Elements „Parameter“ auf der Designersymbolleiste.](./media/parameterize-workflow-app/portal-designer-select-parameters.png)

1. Wählen Sie im Bereich **Parameter** die Option **Parameter erstellen** aus.

1. Geben Sie die folgenden Informationen zum zu erstellenden Parameter an:

   | Eigenschaft | Erforderlich | Beschreibung |
   |----------|----------|-------------|
   | **Name** | Ja | Der Name des zu erstellenden Parameters. |
   | **Typ** | Ja | Der Datentyp für den Parameter wie **Array**, **Bool**, **Float**, **Int**, **Object** und **String**. <p><p>**Hinweis**: In Workflows, die auf einem einzelnen Mandanten basieren, werden sichere Datentypen wie `securestring` und `secureobject` nicht unterstützt. |
   | **Wert** | Ja | Der Wert für den Parameter. <p><p>In Azure Logic Apps-Instanzen mit nur einem Mandanten müssen Sie den Parameterwert angeben, da die Workflowlogik, die Verbindungsinformationen und die Parameterwerte nicht an einem einzigen Ort vorhanden sind. Der Designer muss in der Lage sein, den Parameterwert vor dem Laden aufzulösen. |
   |||

   Das folgende Beispiel zeigt eine Definition für einen Zeichenfolgenparameter:

   ![Screenshot des Azure-Portals, des Workflow-Designers und des Bereichs „Parameter“ mit einer Beispielparameterdefinition.](./media/parameterize-workflow-app/define-parameter.png)

1. Wenn Sie fertig sind, schließen Sie den Bereich **Parameter**, aber stellen Sie sicher, dass Sie Ihren Workflow speichern, um Ihre neue Parameterdefinition zu speichern.

1. Um aus einem Trigger oder einer Aktion, der/die sich in einem Workflow in derselben Logik-App befindet, auf den Parameter zu verweisen, führen Sie die folgenden Schritte aus:

   1. Öffnen Sie den gewünschten Workflow im Designer, und erweitern Sie den Trigger oder die Aktion.

   1. Klicken Sie in der Eigenschaft, in der Sie den Parameter verwenden möchten, in das Bearbeitungsfeld dieser Eigenschaft.

   1. Wählen Sie in der Liste mit den dynamischen Inhalten, die geöffnet wird, unter **Parameter** den von Ihnen zuvor erstellten Parameter aus, z. B.:

      ![Screenshot einer Beispielaktion mit dem Cursor im Bearbeitungsfeld der Eigenschaft, einer erweiterten Liste mit dynamischen Inhalten und dem ausgewählten, zuvor erstellten Parameter.](./media/parameterize-workflow-app/reference-parameter.png)

1. Um Parameter in derselben Logik-App anzuzeigen oder zu bearbeiten, führen Sie einen der beiden Schritte aus:

   * Öffnen Sie einen beliebigen Workflow in dieser Logik-App. Wählen Sie im Menü „Workflow“ den **Designer** aus. Wählen Sie auf der Designersymbolleiste **Parameter** aus.

     Der Bereich **Parameter** wird geöffnet und zeigt alle Parameter an, die Sie aus Workflows in dieser Logik-App definiert haben.

   * Um JSON massenhaft anzuzeigen oder zu bearbeiten, wählen Sie im Hauptmenü Ihrer Logik-App **Parameter** aus.

     Der JSON-Ansicht **Parameter** wird geöffnet und zeigt alle Parameter an, die Sie aus Workflows in dieser Logik-App definiert haben.

### <a name="visual-studio-code"></a>Visual Studio Code

1. Definieren Sie in einer JSON-Projektdatei namens **parameters.json** auf Stammebene *alle* Parameter mit ihren Werten. Diese Datei enthält ein Objekt, das *Schlüssel-Wert*-Paare enthält. Jeder *Schlüssel* ist der Name für jeden Parameter, während jeder *Wert* die Struktur für jeden Parameter ist. Jede Struktur muss sowohl eine `type`- als auch eine `value`-Deklaration enthalten.

   > [!IMPORTANT]
   > Ihre Datei **parameters.json** muss alle Parameter und deren Werte definieren und enthalten, auf die Sie verweisen oder an anderer Stelle in Ihrem Projekt verwenden, z. B. in Workflowdefinitionen oder Verbindungen.

   Das folgende Beispiel zeigt eine Datei mit grundlegenden Parametern:

   ```json
   {
        "responseString": { 
            "type": "string", 
            "value": "hello" 
        },
        "functionAuth": { 
            "type": "object", 
            "value": { 
                "type": "QueryString", 
                "name": "Code", 
                "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey>')" 
            }
        }
    }
    ```

    > [!NOTE]
    > In der Datei **parameters.json** ist `@appsetting` der einzige gültige Ausdruckstyp.

1. Um auf Parameter in Ihren Trigger- oder Aktionseingaben zu verweisen, verwenden Sie den Ausdruck `@parameters('<parameter-name>')`.

#### <a name="parameterize-connections-file"></a>Parametrisieren der Verbindungsdatei

Ersetzen Sie zum Parametrisieren Ihrer **connections.json**-Datei die Werte für Literale wie `ConnectionRuntimeUrl` durch einen einzelnen `parameters()`-Ausdruck, z. B. `@parameters('api-runtimeUrl')`. In der Datei **connections.json** sind `@parameters` und `@appsetting` die einzigen gültigen Ausdruckstypen.

> [!IMPORTANT]
>
> Wenn Sie während der Entwicklung die Datei **connections.json** parametrisieren, wird die Designererfahrung sowohl lokal als auch im Azure-Portal eingeschränkt. Wenn Sie den Designer für die Entwicklung verwenden müssen, verwenden Sie stattdessen eine nicht parametrisierte **connections.json**-Datei. Ersetzen Sie sie dann in Ihren Bereitstellungspipelines durch die parametrisierte Datei. 
> Die Runtime funktioniert weiterhin mit Parametrisierung. Designerverbesserungen befinden sich in der Entwicklung.

Das folgende Beispiel zeigt eine parametrisierte **connections.json**-Datei, die sowohl App-Einstellungen als auch Parameter verwendet. Obwohl Sie nach Möglichkeit Parameter verwenden sollten, handelt es sich bei diesem Szenario um eine Ausnahme oder einen Grenzfall, bei dem Sie App-Einstellungen gegenüber Parametern vorziehen würden, da App-Einstellungen während der Bereitstellung generiert werden und sich in einer Entwicklungspipeline leichter dynamisch auffüllen lassen. Diese Beispieldatei verwendet einen Parameter für das komplexe Authentifizierungsobjekt `blob_auth` und App-Einstellungen für die anderen Werte. In diesem Fall können Sie einen Parameter für das Authentifizierungsobjekt verwenden, da es unwahrscheinlich ist, dass Sie in Ihrem Workflow auf den Parameter verweisen:

```json
{
   "serviceProviderConnections": {
      "serviceBus": {
         "parameterValues": {
            "connectionString": "@appsetting('serviceBus_connectionString')"
        },
        "serviceProvider": {
           "id": "/serviceProviders/serviceBus"
        },
        "displayName": "servicebus"
     }
   },
   "managedApiConnections": {
      "azureblob": {
         "api": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/providers/Microsoft.Web/locations/@{appsetting('WORKFLOWS_LOCATION_NAME')}/managedApis/azureblob"
         },
         "connection": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/resourceGroups/@{appsetting('WORKFLOWS_RESOURCE_GROUP_NAME')}/providers/Microsoft.Web/connections/azureblob"
         },
         "connectionRuntimeUrl": "@appsetting('BLOB_CONNECTION_RUNTIMEURL')",
         "authentication": "@parameters('blob_auth')"
      }
   }
}
```

> [!NOTE]
> Wenn Sie einen Ausdruck haben, der inline mit einfachem Text ist, stellen Sie sicher, dass Sie das interpolierte Format für diesen Ausdruck verwenden, indem Sie den Ausdruck mit geschweiften Klammern ({}) umschließen. Dieses Format hilft, Probleme beim Parsen zu vermeiden.
>
> Wenn Sie zum Beispiel `"<text>/@<function-name>('<parameter-name>')/<text>"` haben, verwenden Sie stattdessen die folgende Version: `"<text>/@{<function-name>('<parameter-name>')}/<text>"`. 
>
> Weitere Informationen finden Sie unter [Überlegungen zur Verwendung von Funktionen](workflow-definition-language-functions-reference.md#function-considerations).
     

## <a name="manage-parameters-files"></a>Verwalten von Parameterdateien

In der Regel müssen Sie mehrere Versionen von Parameterdateien verwalten. Möglicherweise benötigen Sie Werte für verschiedene Bereitstellungsumgebungen, z. B. Entwicklung, Tests und Produktion. Das Verwalten dieser Parameterdateien funktioniert häufig wie das Verwalten von ARM-Vorlagenparameterdateien. Wenn Sie in einer bestimmten Umgebung bereitstellen, bringen Sie die entsprechende Parameterdatei in der Regel über eine Pipeline für DevOps ein.

Führen Sie den folgenden Befehl aus, um Parameterdateien dynamisch mittels Azure CLI zu ersetzen:

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

Bei einem NuGet-basierten Logik-App-Projekt müssen Sie Ihre Projektdatei ( **&lt;logic-app-name&gt;.csproj**) so aktualisieren, dass die Parameterdatei in der Buildausgabe enthalten sind, zum Beispiel:

```csproj
<ItemGroup>
  <None Update="parameters.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup>
```

> [!NOTE]
> Derzeit ist die Funktion zum dynamischen Ersetzen von Parameterdateien im Azure-Portal oder Workflow-Designer noch nicht verfügbar.

Weitere Informationen zur Einrichtung Ihrer Logik-Apps für die DevOps-Bereitstellung finden Sie in der folgenden Dokumentation:

* [Übersicht über die DevOps-Bereitstellung für auf einem einzelnen Mandanten basierende Logik-Apps](devops-deployment-single-tenant-azure-logic-apps.md)
* [Einrichten der DevOps-Bereitstellung für auf einem einzelnen Mandanten basierende Logik-Apps](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>Verwalten von App-Einstellungen

In Azure Logic Apps-Instanzen mit nur einem Mandanten enthalten App-Einstellungen globale Konfigurationsoptionen für *alle Workflows* in derselben Logik-App. Wenn Sie Workflows lokal in Visual Studio Code ausführen, können Sie auf diese App-Einstellungen in der **local.settings.json**-Datei als lokale Umgebungsvariablen zugreifen. Anschließend können Sie in Ihren Parametern auf diese App-Einstellungen verweisen.

Informationen zum Hinzufügen, Aktualisieren oder Löschen von App-Einstellungen finden Sie in den folgenden Abschnitten zu Visual Studio Code, dem Azure-Portal, der Azure CLI oder ARM-Vorlagen (Bicep).

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um die App-Einstellungen für Ihre Logik-App im Azure-Portal zu überprüfen:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihre auf einem einzelnen Mandanten basierende Logik-App.

1. Wählen Sie im Menü Ihrer Logik-App unter **Einstellungen** die Option **Konfiguration** aus.

1. Überprüfen Sie auf der Seite **Konfiguration** auf der Registerkarte **Anwendungseinstellungen** die App-Einstellungen für Ihre Logik-App.

1. Wählen Sie **Werte anzeigen** aus, um alle Werte anzuzeigen. Alternativ können Sie einen einzelnen Wert auswählen, um ihn anzuzeigen.

Führen Sie die folgenden Schritte aus, um eine neue Einstellung hinzuzufügen:

1. Wählen Sie auf der Registerkarte **Anwendungseinstellungen** unter **Anwendungseinstellungen** die Option **Neue Anwendungseinstellung** aus.

1. Geben Sie für **Name** den *Schlüssel* oder Namen für Ihre neue Einstellung ein.

1. Geben Sie für **Wert** den Wert für Ihre neue Einstellung ein.

1. Wenn Sie Ihr neues *Schlüssel-Wert*-Paar erstellen möchten, wählen Sie **OK** aus.

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="Screenshot: Azure-Portal und Konfigurationsbereich mit den App-Einstellungen und Werten für eine auf einem einzelnen Mandanten basierende Logik-App." lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den Befehl `az logicapp config appsettings list` aus, um Ihre aktuellen App-Einstellungen mithilfe der Azure CLI zu überprüfen. Stellen Sie sicher, dass Ihr Befehl die Parameter `--name -n` und `--resource-group -g` enthält. Beispiel:

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Führen Sie den Befehl `az logicapp config appsettings set` aus, um eine App-Einstellung mithilfe der Azure CLI hinzuzufügen oder zu aktualisieren. Stellen Sie sicher, dass Ihr Befehl die Parameter `--name n` und `--resource-group -g` enthält. Der folgende Befehl erstellt beispielsweise eine Einstellung mit einem Schlüssel namens `CUSTOM_LOGIC_APP_SETTING` mit einem Wert von `12345`:

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[Resource Manager- oder Bicep-Vorlage](#tab/azure-resource-manager)

Um Ihre App-Einstellungen in einer ARM- oder Bicep-Vorlage zu überprüfen und zu definieren, suchen Sie die Ressourcendefinition Ihrer Logik-App, und aktualisieren Sie das `appSettings`-JSON-Objekt. Die vollständige Ressourcendefinition finden Sie in der [ARM-Vorlagenreferenz](/azure/templates/microsoft.web/sites).

Dieses Beispiel zeigt Dateieinstellungen für ARM-Vorlagen oder Bicep-Vorlagen:

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](single-tenant-overview-compare.md)
