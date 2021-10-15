---
title: Zuordnung der Azure-Nutzung durch Kunden
description: Verschaffen Sie sich einen Überblick über die Nachverfolgung der Nutzung von Azure-Anwendungen durch Kunden im kommerziellen Marketplace oder anderer bereitstellbaren geistigen Eigentümern von Partnern.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/04/2021
ms.custom: devx-track-terraform, devx-track-azurepowershell
ms.openlocfilehash: 4c6eaf47035d78a283b4e27bf86e1550ad7279d1
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457048"
---
# <a name="azure-customer-usage-attribution"></a>Zuordnung der Azure-Nutzung durch Kunden

Bei der Zuordnung der Nutzung durch Kunden wird die Nutzung von Azure-Ressourcen in Kundenabonnements Ihnen als Partner zugeordnet, die bei der Bereitstellung Ihres geistigen Eigentums erstellt wurden. Die Bildung dieser Zuordnungen in internen Microsoft-Systemen sorgt für mehr Transparenz des Azure-Speicherbedarfs Ihrer Software. Bei [Angeboten für Azure-Anwendungen im kommerziellen Marketplace](#commercial-marketplace-azure-apps) hilft diese Nachverfolgungsfunktion Ihnen dabei, sich an Microsoft-Vertriebsteams zu orientieren und Gutschriften für Microsoft-Partnerprogramme zu erhalten. Die Zuordnung der Nutzung durch Kunden gilt nicht für [Azure-VM-Angebote im kommerziellen Marketplace](marketplace-virtual-machines.md). Ein Marketplace-Herausgeber muss keinerlei Schritte für VM-Angebote ausführen, um sicherzustellen, dass der Azure-Verbrauch in Endkundenabonnements nachverfolgt wird.

Die Zuordnung der Nutzung durch Kunden unterstützt drei Bereitstellungsmethoden:

1. Azure Resource Manager-Vorlagen (die allgemeinen Grundlagen von Azure-Apps, die im kommerziellen Marketplace auch als „Lösungsvorlagen“ oder „verwaltete Apps“ bezeichnet werden): Partner erstellen Resource Manager-Vorlagen, um die Infrastruktur und Konfiguration ihrer Azure-Lösungen zu definieren. Eine Resource Manager-Vorlage ermöglicht es Ihren Kunden, die Ressourcen ihrer Lösung in einem konsistenten und wiederholbaren Zustand bereitzustellen.
1. Azure Resource Manager-APIs: Partner können die Azure Resource Manager-APIs aufrufen, um eine Resource Manager-Vorlage bereitzustellen oder Azure-Dienste direkt bereitzustellen.
1. Terraform: Partner können Terraform verwenden, um eine Resource Manager-Vorlage oder Azure-Dienste direkt bereitzustellen.

Außerhalb des kommerziellen Marketplace gibt es sekundäre Anwendungsfälle für die Zuordnung der Nutzung durch Kunden, die [im späteren Verlauf dieses Artikels beschrieben werden](#other-use-cases).

>[!IMPORTANT]
>- Die Zuordnung der Nutzung durch Kunden ist nicht dafür vorgesehen, die Arbeit von Systemintegratoren, Anbietern verwalteter Dienste oder Tools nachzuverfolgen, die primär zum Bereitstellen und Verwalten von Azure-Ressourcen entworfen wurden.
>- Die Zuordnung der Nutzung durch Kunden ist für neue Bereitstellungen konzipiert und unterstützt nicht die Überwachung von Ressourcen, die bereits bereitgestellt wurden.
>- Nicht alle Azure-Dienste sind mit der Zuordnung der Nutzung durch Kunden kompatibel. Azure Kubernetes Services (AKS) und VM Scale Sets und Azure Batch weisen bekannte Probleme auf, die zu unzureichender Meldung der Nutzung führen.

## <a name="commercial-marketplace-azure-apps"></a>Azure-Apps aus dem kommerziellen Marketplace

Die Nachverfolgung der Azure-Nutzung von Azure-Apps, die im kommerziellen Marketplace veröffentlicht wurden, erfolgt größtenteils automatisch. Wenn Sie eine Resource Manager-Vorlage im Rahmen der [technischen Konfiguration des Marketplace-Plans Ihrer Azure-App](./azure-app-solution.md#define-the-technical-configuration) hochladen, fügt Partner Center eine Nachverfolgungs-ID hinzu, die von Azure Resource Manager gelesen werden kann.

>[!NOTE]
>So stellen Sie sicher, dass die Nutzung Ihrer Anwendung in unseren Systemen ordnungsgemäß attributiert wird:
>1. Wenn Sie die Nachverfolgungs-ID im Ressourcentyp „Microsoft.Resources/deployment“ mit einer Variablen definieren, ersetzen Sie die Variable durch die Nachverfolgungs-ID, die im Partner Center auf der Seite **Technische Konfiguration** des Plans angezeigt wird (Informationen hierzu finden Sie weiter unten unter [Hinzufügen einer GUID zu einer Resource Manager-Vorlage](#add-a-guid-to-a-resource-manager-template)).
>2. Wenn für die Resource Manager-Vorlage Ressourcen vom Typ „Microsoft.Resources/deployments“ für andere Zwecke als für die Zuordnung der Nutzung durch Kunden verwendet werden, kann von Microsoft keine Nachverfolgungs-ID für die Zuordnung der Nutzung durch Kunden in Ihrem Namen hinzugefügt werden. Fügen Sie eine neue Ressource vom Typ „Microsoft.Resources/deployments“ und die Nachverfolgungs-ID hinzu, die im Partner Center auf der Seite **Technische Konfiguration** des Plans angezeigt wird (Informationen hierzu finden Sie weiter unten unter [Hinzufügen einer GUID zu einer Resource Manager-Vorlage](#add-a-guid-to-a-resource-manager-template)).

Wenn Sie Azure Resource Manager-APIs verwenden, müssen Sie Ihre Nachverfolgungs-ID gemäß der [Anweisungen unten](#use-resource-manager-apis) hinzufügen, um sie an Azure Resource Manager zu übergeben, während der Code Ihre Ressourcen bereitstellt. Diese ID wird in Partner Center auf der Seite „Technische Konfiguration“ Ihres Plans angezeigt. 

> [!NOTE]
> Für vorhandene Azure-Apps wurde im März 2021 eine einmalige Migration durchgeführt, um die Nachverfolgungs-IDs in der technischen Konfiguration der einzelnen Pläne zu aktualisieren. Die Nutzung früherer Bereitstellungen dieser Angebote werden weiterhin von Systemen von Microsoft überwacht.
>
>Wenn Sie Ihre Angebote aktualisieren, müssen Sie den Ressourcentyp **Microsoft. Resources/Deployments** nicht mehr in ihrer Hauptvorlagen-Datei hinzufügen.

## <a name="other-use-cases"></a>Weitere Anwendungsfälle

Sie können die Zuordnung der Nutzung durch Kunden zum Nachverfolgen der Azure-Nutzung von Lösungen verwenden, die nicht im kommerziellen Marketplace verfügbar sind. Diese Lösungen befinden sich normalerweise im Schnellstartrepository, in privaten GitHub-Repositorys oder stammen aus 1:1-Kundeninteraktionen, bei denen dauerhaftes geistiges Eigentum erstellt wird (z. B. eine bereitstellbare und skalierbare App).

Hierzu sind mehrere manuelle Schritte erforderlich:

1. Erstellen Sie eine oder mehrere GUIDs, die als Überwachungs-IDs verwendet werden.
1. Registrieren Sie diese GUIDs in Partner Center.
1. Fügen Sie Ihre registrierten GUIDs zu Ihrer Azure-App und/oder Benutzer-Agent-Zeichenfolgen hinzu.

### <a name="create-guids"></a>Erstellen von GUIDs

Im Gegensatz zu den Nachverfolgungs-IDs, die Partner Center in Ihrem Auftrag für Azure-Apps im kommerziellen Marketplace erstellt, erfordern andere Anwendungsfälle für die Zuordnung der Nutzung durch Kunden, dass Sie eine GUID erstellen, die Sie als Nachverfolgungs-ID verwenden. Eine GUID ist eine eindeutige Verweis-ID mit 32 Hexadezimalziffern. Zum Erstellen von GUIDs für die Nachverfolgung sollten Sie einen GUID-Generator verwenden, z. B. über PowerShell:

```powershell
[guid]::NewGuid()
```

Sie sollten eine eindeutige GUID für jedes Produkt und jeden Verteilungskanal erstellen. Sie können eine einzelne GUID für mehrere Verteilungskanäle eines Produkts verwenden, wenn Sie die Berichterstellung nicht aufteilen möchten. Die Berichterstellung erfolgt über die Microsoft Partner Network-ID und die GUID.

### <a name="register-guids"></a>Registrieren von GUIDs

Die GUIDs müssen als Nächstes in Partner Center registriert werden, damit Sie Ihnen als Partner zugeordnet werden können:

1. Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2165507) an.

1. Registrieren Sie sich als [kommerzieller Marketplace-Herausgeber](https://go.microsoft.com/fwlink/?linkid=2165614).

1. Klicken Sie in der oberen rechten Ecke auf **Einstellungen** (Zahnradsymbol), und wählen Sie dann **Kontoeinstellungen** aus.

1. Klicken Sie auf **Organisationsprofil** > **Bezeichner** > **Nachverfolgungs-GUID hinzufügen**.

1. Geben Sie im Feld **GUID** Ihre Nachverfolgungs-GUID ein. Geben Sie nur die GUID ohne das Präfix `pid-` ein. Geben Sie im Feld **Beschreibung** den Namen oder eine Beschreibung Ihrer Lösung ein.

1. Wenn Sie mehrere GUIDs registrieren möchten, klicken Sie erneut auf **Nachverfolgungs-GUID hinzufügen**. Auf der Seite werden weitere Felder angezeigt.

1. Wählen Sie **Speichern** aus.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Hinzufügen einer GUID zu einer Resource Manager-Vorlage

Nehmen Sie eine einzige Änderung an der Hauptvorlagendatei vor, um Ihre registrierte GUID zu einer Resource Manager-Vorlage hinzuzufügen:

1. Öffnen Sie die Resource Manager-Vorlage.

1. Fügen Sie eine neue Ressource vom Typ [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) in der Hauptvorlagendatei hinzu. De Ressource muss sich in der Datei **mainTemplate.json** oder in der Datei **azuredeploy.json** befinden. Sie darf sich nicht in geschachtelten oder verknüpften Vorlagen befinden.

1. Geben Sie den GUID-Wert nach dem Präfix `pid-` als Namen der Ressource ein. Wenn die GUID z. B. eb7927c8-dd66-43e1-b0cf-c346a422063 lautet, ist der Ressourcenname **pid-eb7927c8-dd66-43e1-b0cf-c346a422063**. Beispiel:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Überprüfen Sie die Vorlage auf Fehler.

1. Veröffentlichen Sie die Vorlage in den entsprechenden Repositorys erneut.

1. [Überprüfen Sie den Erfolg der GUID in der Vorlagenbereitstellung](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Weitere Informationen zum Erstellen und Veröffentlichen von Resource Manager-Vorlagen finden Sie unter [Erstellen und Bereitstellen Ihrer ersten Resource Manager-Vorlage](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Überprüfen von durch eine GUID nachverfolgten Bereitstellungen

Nachdem Sie Ihre Vorlage geändert und eine Testbereitstellung ausgeführt haben, verwenden Sie das folgende PowerShell-Skript, um die bereitgestellten und markierten Ressourcen abzurufen.

Mit dem Skript können Sie überprüfen, ob die GUID erfolgreich der Resource Manager-Vorlage hinzugefügt wurde. Das Skript gilt nicht für Resource Manager-API- oder Terraform-Bereitstellungen.

Melden Sie sich bei Azure an. Wählen Sie das Abonnement mit der Bereitstellung aus, die Sie überprüfen möchten, bevor Sie das Skript ausführen. Führen Sie das Skript im Abonnementkontext der Bereitstellung aus.

Die **GUID** (unten als „deploymentName“ bezeichnet) und der Name **resourceGroupName** der Bereitstellung sind erforderliche Parameter.

Sie erhalten [das ursprüngliche Skript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) auf GitHub.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Benachrichtigen der Kunden

Partner sollten ihre Kunden über Bereitstellungen informieren, bei denen die Zuordnung der Nutzung durch Kunden eingesetzt wird. Microsoft meldet die Azure-Nutzung dieser Bereitstellungen an den Partner. Die folgenden Beispiele enthalten Inhalte, die Sie für die Benachrichtigung Ihrer Kunden über diese Bereitstellungen verwenden können. Ersetzen Sie in den Beispielen \<PARTNER> durch den Namen Ihres Unternehmens. Partner müssen sicherstellen, dass die Benachrichtigung ihren Richtlinien für den Schutz und die Erfassung von Daten entspricht und Optionen aufweist, mit denen Kunden von der Nachverfolgung ausgenommen werden können.

#### <a name="notification-for-resource-manager-template-deployments"></a>Benachrichtigung für Bereitstellungen über Resource Manager-Vorlagen

Wenn Sie diese Vorlage bereitstellen, kann Microsoft die Installation der \<PARTNER>-Software mit den bereitgestellten Azure-Ressourcen identifizieren. Microsoft kann die Ressourcen korrelieren, die zur Unterstützung der Software verwendet werden. Microsoft erfasst diese Informationen, um die Nutzung der Produkte zu optimieren und um Geschäfte zu ermöglichen. Die Daten werden erfasst und gemäß der Datenschutzrichtlinien von Microsoft verwaltet. Diese Datenschutzrichtlinien finden Sie unter [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter).

#### <a name="notification-for-sdk-or-api-deployments"></a>Benachrichtigung für SDK- oder API-Bereitstellungen

Wenn Sie die \<PARTNER>-Software bereitstellen, kann Microsoft die Installation der \<PARTNER>-Software mit den bereitgestellten Azure-Ressourcen identifizieren. Microsoft kann die Ressourcen korrelieren, die zur Unterstützung der Software verwendet werden. Microsoft erfasst diese Informationen, um die Nutzung der Produkte zu optimieren und um Geschäfte zu ermöglichen. Die Daten werden erfasst und gemäß der Datenschutzrichtlinien von Microsoft verwaltet. Diese Datenschutzrichtlinien finden Sie unter [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter).

## <a name="use-resource-manager-apis"></a>Verwenden von Resource Manager-APIs

In einigen Fällen verwenden Sie möglicherweise direkte Abfragen der Resource Manager-REST-APIs, um Azure-Dienste bereitzustellen. [Azure unterstützt mehrere SDKs](../index.yml?pivot=sdkstools), um diese Aufrufe zu ermöglichen. Sie können eines dieser SDKs verwenden oder die REST-APIs direkt aufrufen, um Ressourcen bereitzustellen.

Fügen Sie beim Erstellen Ihrer API-Aufrufe Ihre Nachverfolgungs-ID in den Benutzer-Agent-Header der Anforderung ein, um die Zuordnung der Nutzung durch Kunden zu aktivieren. Formatieren Sie die Zeichenfolge mit dem Präfix `pid-`. Beispiele:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Wenn Sie Resource Manager-APIs mit einer Azure-App im kommerziellen Marketplace verwenden, verwenden Sie die in Partner Center bereitgestellte Nachverfolgungs-ID. Verwenden Sie KEINE GUID.

Verschiedene SDKs interagieren auf verschiedene Weise mit den Resource Manager-APIs und erfordern einige Unterschiede in Ihrem Code. In den unten gezeigten Beispielen wird der Ansatz ohne den kommerziellen Marketplace verwendet, bei dem eine GUID verwendet wird. Diese Beispiele befassen sich mit einer Vielzahl der beliebteren Azure SDKs.

#### <a name="example-python-sdk"></a>Beispiel: Python SDK

Bei Python wird das Attribut **config** verwendet. Sie können das Attribut nur einem Benutzer-Agent hinzufügen. Beispiel:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Fügen Sie das Attribut für jeden Client hinzu. Es gibt keine globale statische Konfiguration. Sie können eine Clientfactory markieren, um sicherzustellen, dass jeder Client nachverfolgt wird. Weitere Informationen finden Sie im [Clientfactory-Beispiel auf GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Beispiel: .NET SDK

Stellen Sie für .NET sicher, dass Sie den Benutzer-Agent festlegen. Verwenden Sie die Bibliothek [Microsoft.Azure.Management.Fluent](/dotnet/api/microsoft.azure.management.fluent), um den Benutzer-Agent mithilfe des folgenden Codes (Beispiel in C#) festzulegen:

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Beispiel: Azure PowerShell

Wenn Sie Ressourcen über Azure PowerShell bereitstellen, fügen Sie Ihre GUID mithilfe der folgenden Methode an:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Beispiel: Azure-Befehlszeilenschnittstelle

Wenn Sie die Azure CLI zum Anfügen Ihrer GUID verwenden, legen Sie die Umgebungsvariable **AZURE_HTTP_USER_AGENT** im Rahmen eines Skripts fest. Sie können die Variable auch global für den Bereich der Shell festlegen:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Weitere Informationen finden Sie unter [Azure SDK für Go](/azure/developer/go/).

## <a name="use-terraform"></a>Einsatz von Terraform

Terraform wird seit dem Release 1.21.0 für Azure-Anbieter unterstützt: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019). Dies gilt für alle Partner, die ihre Lösungen über Terraform bereitstellen, sowie für alle Ressourcen, die vom Azure-Anbieter (Version 1.21.0 oder höher) bereitgestellt und gemessen werden.

Für Azure-Anbieter für Terraform wurde ein neues optionales Feld namens [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) zum Festlegen der Nachverfolgungs-GUID hinzugefügt, die für Ihre Lösung verwendet wird. Der Wert dieses Feld kann zudem aus der Umgebungsvariable *ARM_PARTNER_ID* abgerufen werden.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Legen Sie für den Wert *partner_id* eine registrierte GUID fest. Fügen Sie den Präfix „pid-“ NICHT für die GUID fest. Legen Sie lediglich die tatsächliche GUID fest.

> [!IMPORTANT]
> Wenn Sie Terraform mit einer Azure-App im kommerziellen Marketplace verwenden, verwenden Sie die gesamte in Partner Center bereitgestellte Nachverfolgungs-ID. Verwenden Sie KEINE GUID.

## <a name="get-support"></a>Support

Informationen zu den Supportoptionen im kommerziellen Marketplace finden Sie unter [Support für das Programm „Kommerzieller Marketplace“ in Partner Center](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>So fragen Sie technische Beratung an

1. Besuchen Sie [Partner Technical Services](https://aka.ms/TechnicalJourney).
1. Klicken Sie auf **Cloud infrastructure and management** (Cloudinfrastruktur und -verwaltung), um die technische Journey anzuzeigen.
1. Klicken Sie auf **Deployment Services** > **Submit a request** (Bereitstellungsdienste > Anforderung senden).
1. Melden Sie sich mit Ihrem MSA (Microsoft Partner Network-Konto) oder Ihrem AAD-Konto (Partnerdashboardkonto) an.
1. Geben Sie die Kontaktinformationen in das daraufhin geöffnete Formular ein, oder überprüfen Sie diese. Möglicherweise sind einige Beratungsinformationen bereits ausgefüllt oder es werden Dropdownoptionen angezeigt.
1. Geben Sie einen Titel und eine ausführliche Beschreibung Ihres Problems ein.
1. Klicken Sie auf **Senden**.

Eine ausführliche Anleitung mit Screenshots finden Sie unter [Verwenden von technischen Presales- und Bereitstellungsdiensten](/partner-center/technical-benefits).

Im Anschluss werden Sie von einem technischen Berater eines Microsoft-Partners kontaktiert, der mit Ihnen einen passenden Termin für einen Anruf festlegt.

## <a name="report"></a>Bericht
Die Berichterstellung für den Azure-Verbrauch, die über die Zuordnung der Nutzung durch Kunden nachverfolgt wird, ist heute für ISV-Partner nicht verfügbar. Die Berichterstellung für das Programm „Kommerzieller Marketplace“ in Partner Center zur Abdeckung der Kundennutzung soll in der zweiten Hälfte des Jahres 2022 hinzugefügt werden.

## <a name="faq"></a>Häufig gestellte Fragen

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Kann eine Nachverfolgungs-ID geändert werden, nachdem sie hinzugefügt wurde?

Die Nachverfolgungs-IDs für Azure-Apps im kommerziellen Marketplace werden automatisch von Partner Center verwaltet. Allerdings kann ein Kunde eine Vorlage herunterladen und die Nachverfolgungs-ID ändern oder entfernen. Partner sollten die Rolle der Nachverfolgungs-ID ihren Kunden proaktiv erläutern, um zu verhindern, dass sie entfernt oder geändert wird. Die Änderung der Nachverfolgungs-ID wirkt sich lediglich auf neue Bereitstellungen und Ressourcen aus, nicht auf vorhandene.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Kann ich Vorlagen nachverfolgen, die nicht über ein Microsoft-Repository, sondern z. B. über GitHub bereitgestellt wurden?

Ja, solange die Nachverfolgungs-ID vorhanden ist, wenn die Vorlage bereitgestellt wird, wird die Nutzung nachverfolgt. Laden Sie zunächst eine Kopie Ihrer veröffentlichten Vorlage (die die Nachverfolgungs-ID enthält) über Ihr Angebot im kommerziellen Marketplace über das Azure-Portal herunter, um die Zuordnung zwischen Ihnen als Herausgeber und Ihrer Vorlage beizubehalten, die über ein Repository bereitgestellt wird, das nicht von Microsoft stammt. Veröffentlichen Sie diese Version in GitHub oder einem anderen Repository, das nicht von Microsoft bereitgestellt wird.

Wenn Ihre Vorlage nicht im kommerziellen Marketplace aufgeführt wird und eine registrierte GUID umfasst, stellen Sie sicher, dass die GUID in der Version vorhanden ist, die Sie auf GitHub oder einem anderen nicht von Microsoft stammenden Repository veröffentlichen.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Erhält der Kunde die Berichte auch?

Nein. Kunden können ihre Nutzung aller Ressourcen oder Ressourcengruppen im Azure-Portal nachverfolgen. Für Kunden wird die Anzeige der Nutzung nicht anhand der Nachverfolgungs-ID für die Zuordnung der Nutzung durch Kunden aufgeschlüsselt.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>Ähnelt die Zuordnung der Nutzung durch Kunden DPOR (Digital Partner of Record) oder PAL (Partner Admin Link)?

Die Zuordnung der Nutzung durch Kunden ist ein Mechanismus, mit dem die Azure-Nutzung einem wiederholbaren, bereitstellbaren geistigen Eigentum eines Partners zugeordnet wird. Diese Zuordnung erfolgt zum Zeitpunkt der Bereitstellung. DPOR und PAL sind dafür vorgesehen, einen Beratungspartner (Systemintegrator) oder Verwaltungspartner (Anbieter verwalteter Dienste) einem relevanten Azure-Fußabdruck eines Kunden für den Zeitraum zuzuordnen, in dem der Partner sich mit dem Kunden beschäftigt.
