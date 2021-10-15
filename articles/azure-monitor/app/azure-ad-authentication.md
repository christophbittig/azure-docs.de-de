---
title: Azure AD-Authentifizierung für Application Insights (Vorschau)
description: Erfahren Sie, wie Sie die Azure AD-Authentifizierung aktivieren, um sicherzustellen, dass nur authentifizierte Telemetriedaten in Ihren Application Insights-Ressourcen erfasst werden.
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: b38e3498ae61c9639c71eb358a4089dc59243616
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857709"
---
# <a name="azure-ad-authentication-for-application-insights-preview"></a>Azure AD-Authentifizierung für Application Insights (Vorschau)
Für Application Insights wird jetzt die Azure Active Directory-Authentifizierung unterstützt. Mit Azure AD können Sie sicherstellen, dass nur authentifizierte Telemetriedaten in Ihren Application Insights-Ressourcen erfasst werden. 

In der Regel ist die Nutzung verschiedener Authentifizierungssysteme sehr umständlich und risikoreich, da es schwierig ist, Anmeldeinformationen im großen Stil zu verwalten. Sie können nun die lokale Authentifizierung deaktivieren und sicherstellen, dass ausschließlich Telemetriedaten in Ihren Application Insights-Ressourcen erfasst werden, die mit [verwalteten Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) und [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) authentifiziert wurden. Dieses Feature ist eine Möglichkeit zur Verbesserung der Sicherheit und Zuverlässigkeit von Telemetriedaten, die als Grundlage für operative (Warnungen, automatische Skalierung usw.) und geschäftliche Entscheidungen verwendet werden.

> [!IMPORTANT]
> Die Azure AD-Authentifizierung befindet sich derzeit in der VORSCHAUVERSION.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Im Folgenden finden Sie SDKs/Szenarien, die in der öffentlichen Vorschauphase nicht unterstützt werden:
- [Application Insights Java 2.x SDK](java-2x-agent.md): Die Azure AD-Authentifizierung ist nur für den Application Insights-Java Agent >=3.2.0 verfügbar. 
- [ApplicationInsights-JavaScript-Web SDK](javascript.md). 
- [Application Insights-OpenCensus-Python SDK](opencensus-python.md) mit Python-Version 3.4 und 3.5.
- Ein [zertifikat-/geheimnisbasiertes Azure AD](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) wird in der Produktion nicht empfohlen. Verwenden Sie stattdessen verwaltete Identitäten. 
- Die Überwachung ohne Code (für Sprachen) ist für App Service, VM-Skalierungsgruppen, Azure Functions usw. standardmäßig aktiviert.
- [Verfügbarkeitstests](availability-overview.md).
- [Profiler](profiler-overview.md).

## <a name="prerequisites-to-enable-azure-ad-authentication-ingestion"></a>Voraussetzungen zum Aktivieren der Erfassung von Azure AD-Authentifizierungen

- Kenntnisse in:
    - [Verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md). 
    - [Dienstprinzipal](../../active-directory/develop/howto-create-service-principal-portal.md).
    - [Zuweisen von Azure-Rollen](../../role-based-access-control/role-assignments-portal.md). 
- Sie verfügen über die Rolle „Besitzer“ für die Ressourcengruppe und können anderen mithilfe der [integrierten Azure-Rollen](../../role-based-access-control/built-in-roles.md) Zugriff gewähren.

## <a name="configuring-and-enabling-azure-ad-based-authentication"></a>Konfigurieren und Aktivieren der Azure AD-basierten Authentifizierung 

1. Erstellen Sie entweder mithilfe einer verwalteten Identität oder eines Dienstprinzipals eine Identität, falls Sie noch keine besitzen:

    1. Mithilfe einer verwalteten Identität (empfohlen):

        [Richten Sie eine verwaltete Identität für Ihren Azure-Dienst ein](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) (VM, App Service usw.).

    1. Mithilfe des Dienstprinzipals (nicht empfohlen):

        Weitere Informationen über das Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff finden Sie unter [Erstellen eines Dienstprinzipals](../../active-directory/develop/howto-create-service-principal-portal.md).

1. Zuweisen einer Rolle zu dem Azure-Dienst 

    Führen Sie die Schritte unter [Zuweisen von Azure-Rollen](../../role-based-access-control/role-assignments-portal.md) aus, um die Rolle „Herausgeber von Überwachungsmetriken“ aus der Application Insights-Zielressource zu der Azure-Ressource hinzuzufügen, von der die Telemetriedaten gesendet werden. 

    > [!NOTE]
    > Obwohl in der Bezeichnung der Rolle „Herausgeber von Überwachungsmetriken“ nur der Begriff „Metriken“ verwendet wird, werden alle Telemetriedaten in der Application Insights-Ressource veröffentlicht.

1. Befolgen Sie die untenstehenden Konfigurationsempfehlungen pro Sprache.

### <a name="aspnet-and-net"></a>[ASP.NET und .NET](#tab/net)

> [!NOTE]
> Azure AD wird in dem Application Insights-.NET SDK ab [Version 2.18-Beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.18.0-beta3) unterstützt.

Das Application Insights-.NET SDK unterstützt die von [Azure Identity](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity#credential-classes) bereitgestellten Anmeldeinformationsklassen.

- `DefaultAzureCredential` wird für die lokale Entwicklung empfohlen.
- `ManagedIdentityCredential` wird für systemseitig zugewiesene und benutzerseitig zugewiesene verwaltete Identitäten empfohlen.
    - Verwenden Sie bei systemseitig zugewiesenen Identitäten den Standardkonstruktor ohne Parameter.
    - Stellen Sie bei benutzerseitig zugewiesenen Identitäten die clientId für den Konstruktor bereit.
- `ClientSecretCredential` wird für Dienstprinzipale empfohlen. 
    - Stellen Sie tenantId, clientId und clientSecret für den Konstruktor bereit.

Im Folgenden finden Sie ein Beispiel für das manuelle Erstellen und Konfigurieren einer `TelemetryConfiguration` mithilfe von .NET:

```csharp
var config = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
}
var credential = new DefaultAzureCredential();
config.SetAzureTokenCredential(credential);

```

Im Folgenden finden Sie ein Beispiel für die Konfiguration von `TelemetryConfiguration` mithilfe von ASP.NET Core:
```csharp
services.Configure<TelemetryConfiguration>(config =>
{
       var credential = new DefaultAzureCredential();
       config.SetAzureTokenCredential(credential);
});
services.AddApplicationInsightsTelemetry(new ApplicationInsightsServiceOptions
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
});
```
### <a name="nodejs"></a>[Node.js](#tab/nodejs)
 
> [!NOTE]
> Azure AD wird in Application Insights Node.JS ab [Version 2.1.0-beta.1](https://www.npmjs.com/package/applicationinsights/v/2.1.0-beta.1) unterstützt.

Application Insights Node.JS unterstützt die von [Azure Identity](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/identity/identity#credential-classes) bereitgestellten Anmeldeinformationsklassen.

#### <a name="defaultazurecredential"></a>DefaultAzureCredential

```javascript
let appInsights = require("applicationinsights");
import { DefaultAzureCredential } from "@azure/identity"; 
 
const credential = new DefaultAzureCredential();
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

#### <a name="clientsecretcredential"></a>ClientSecretCredential

```javascript
let appInsights = require("applicationinsights");
import { ClientSecretCredential } from "@azure/identity"; 
 
const credential = new ClientSecretCredential(
    "<YOUR_TENANT_ID>",
    "<YOUR_CLIENT_ID>",
    "<YOUR_CLIENT_SECRET>"
  );
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

### <a name="java"></a>[Java](#tab/java)

> [!NOTE]
> Azure AD wird im Application Insights Java-Agent ab [Java 3.2.0-BETA](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0-BETA) unterstützt. 

1. [Konfigurieren einer Anwendung mit dem Java-Agent](java-in-process-agent.md#get-started)

    > [!IMPORTANT]
    > Verwenden Sie die vollständige Verbindungszeichenfolge, die „IngestionEndpoint“ enthält, wenn Sie Ihre App mit dem Java-Agent konfigurieren. Beispiel: `InstrumentationKey=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX;IngestionEndpoint=https://XXXX.applicationinsights.azure.com/`.

    > [!NOTE]
    >  Weitere Informationen über die Migration vom 2.X SDK zum 3.X Java-Agent finden Sie unter [Upgrade des Application Insights Java 2.x SDK](java-standalone-upgrade-from-2x.md).

1. Fügen Sie die JSON-Konfiguration je nach verwendeter Authentifizierungsmethode zur Konfigurationsdatei „ApplicationInsights.json“ hinzu. Es wird empfohlen, dass Benutzer verwaltete Identitäten verwenden.

#### <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

Im Folgenden finden Sie ein Beispiel für die Konfiguration des Java-Agents für die Verwendung einer systemseitig zugewiesenen verwalteten Identität für die Authentifizierung mit Azure AD.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "SAMI" 
    } 
  } 
} 
```

#### <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

Im Folgenden finden Sie ein Beispiel für die Konfiguration des Java-Agents für die Verwendung einer benutzerseitig zugewiesenen verwalteten Identität für die Authentifizierung mit Azure AD.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "UAMI", 
      "clientId":"<USER-ASSIGNED MANAGED IDENTITY CLIENT ID>" 
    } 
  }     
} 
```
:::image type="content" source="media/azure-ad-authentication/user-assigned-managed-identity.png" alt-text="Screenshot einer benutzerseitig zugewiesenen verwalteten Identität" lightbox="media/azure-ad-authentication/user-assigned-managed-identity.png":::

#### <a name="client-secret"></a>Geheimer Clientschlüssel

Im Folgenden finden Sie ein Beispiel für die Konfiguration des Java-Agents für die Verwendung eines Dienstprinzipals für die Authentifizierung mit Azure AD. Wir empfehlen Benutzern, diese Art der Authentifizierung nur in der Entwicklungsphase zu verwenden. Das endgültige Ziel eines Authentifizierungsfeatures besteht darin, Geheimnisse zu beseitigen.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint",
   "preview": { 
        "authentication": { 
          "enabled": true, 
          "type": "CLIENTSECRET", 
          "clientId":"<YOUR CLIENT ID>", 
          "clientSecret":"<YOUR CLIENT SECRET>", 
          "tenantId":"<YOUR TENANT ID>" 
    } 
  } 
} 
```
:::image type="content" source="media/azure-ad-authentication/client-secret-tenant-id.png" alt-text="Screenshot: Bereich „Geheimer Clientschlüssel“ mit tenantID und ClientID" lightbox="media/azure-ad-authentication/client-secret-tenant-id.png":::

:::image type="content" source="media/azure-ad-authentication/client-secret-cs.png" alt-text="Screenshot: Bereich „Geheimer Clientschlüssel“ mit geheimem Clientschlüssel" lightbox="media/azure-ad-authentication/client-secret-cs.png":::

### <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Die Azure AD-Authentifizierung ist nur für Python v2.7, v3.6 und v3.7 verfügbar. Im Application Insights-Opencensus-Python SDK wird Azure AD ab der Betaversion [opencensus-ext-azure 1.1b0](https://pypi.org/project/opencensus-ext-azure/1.1b0/) unterstützt.

Erstellen Sie die entsprechenden [Anmeldeinformationen](/python/api/overview/azure/identity-readme#credentials) und übergeben Sie diese an den Konstruktor des Azure Monitor-Exporters. Stellen Sie sicher, dass Ihre Verbindungszeichenfolge den Instrumentierungsschlüssel und den Erfassungsendpunkt Ihrer Ressource enthält.

Nachstehend finden Sie die Authentifizierungstypen, die von den Opencensus-Azure Monitor-Exportern unterstützt werden. Es wird empfohlen, in der Produktionsumgebung verwaltete Identitäten zu verwenden.

#### <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential()
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential(client_id="<client-id>")
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="client-secret"></a>Geheimer Clientschlüssel

```python
from azure.identity import ClientSecretCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tenant_id = "<tenant-id>"
client_id = "<client-id"
client_secret = "<client-secret>"

credential = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...
```
---

## <a name="disable-local-authentication"></a>Deaktivieren der lokalen Authentifizierung

Nachdem die Azure AD Authentifizierung aktiviert wurde, können Sie die lokale Authentifizierung deaktivieren. Dies ermöglicht Ihnen das Erfassen von Telemetriedaten, die ausschließlich durch Azure AD authentifiziert wurden, und wirkt sich darüber hinaus auf den Datenzugriff aus (z. B. über API-Schlüssel). 

Sie können die lokale Authentifizierung mithilfe des Azure-Portals, über Azure Policy oder über ein Programm deaktivieren.

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie in der Application Insights-Ressource im linken Menü unter *Konfigurieren* die Option **Eigenschaften** aus. Wählen Sie dann **Aktiviert (zum Ändern klicken)** aus, wenn die lokale Authentifizierung aktiviert ist. 

   :::image type="content" source="./media/azure-ad-authentication/enabled.png" alt-text="Screenshot: „Eigenschaften“ unter der Überschrift *Konfigurieren* (ausgewählt) und Schaltfläche „Aktiviert (zum Ändern klicken)“ unter Lokale Authentifizierung":::

1. Wählen Sie **Deaktiviert** und wenden Sie die Änderungen an.

   :::image type="content" source="./media/azure-ad-authentication/disable.png" alt-text="Screenshot: Lokale Authentifizierung mit hervorgehobener Schaltfläche „Aktiviert/Deaktiviert“":::

1. Nachdem Ihre Ressource die lokale Authentifizierung deaktiviert hat, sehen Sie die entsprechenden Informationen im Bereich **Übersicht**.

   :::image type="content" source="./media/azure-ad-authentication/overview.png" alt-text="Screenshot der Registerkarte „Übersicht“ mit hervorgehobener Option „Deaktiviert (zum Ändern klicken)“":::

### <a name="azure-policy"></a>Azure Policy 

Die Azure Policy-Instanz für „DisableLocalAuth“ verweigert Benutzer*innen das Erstellen einer neuen Application Insights-Ressource, wenn diese Eigenschaft nicht auf TRUE festgelegt ist. Der Richtlinienname lautet „Application Insights Components should block non-AAD auth ingestion“ (Application Insights-Komponenten sollten die Erfassung von Nicht-AAD-Authentifizierungen blockieren).

Um diese Richtliniendefinition auf Ihr Abonnement anzuwenden, [erstellen Sie eine neue Richtlinienzuweisung, und weisen Sie die Richtlinie dann zu](../../governance/policy/assign-policy-portal.md).

Im Folgenden finden Sie die Richtlinienvorlagendefinition:
```JSON
{
    "properties": {
        "displayName": "Application Insights components should block non-AAD auth ingestion",
        "policyType": "BuiltIn",
        "mode": "Indexed",
        "description": "Improve Application Insights security by disabling log ingestion that are not AAD-based.",
        "metadata": {
            "version": "1.0.0",
            "category": "Monitoring"
        },
        "parameters": {
            "effect": {
                "type": "String",
                "metadata": {
                    "displayName": "Effect",
                    "description": "The effect determines what happens when the policy rule is evaluated to match"
                },
                "allowedValues": [
                    "audit",
                    "deny",
                    "disabled"
                ],
                "defaultValue": "audit"
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Insights/components"
                    },
                    {
                        "field": "Microsoft.Insights/components/DisableLocalAuth",
                        "notEquals": "true"                        
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effect')]"
            }
        }
    }
}
```

### <a name="programmatic-enablement"></a>Programmgesteuerte Aktivierung 

Die `DisableLocalAuth`-Eigenschaft wird verwendet, um jede lokale Authentifizierung für Ihre Application Insights-Ressourcen zu deaktivieren. Wenn diese Eigenschaft auf `true` festgelegt ist, erzwingt sie die Nutzung der Azure AD-Authentifizierung für sämtliche Zugriffe.

Im Folgenden finden Sie eine Azure Resource Manager-Vorlage, mit der Sie eine arbeitsbereichsbasierte Application Insights-Ressource mit deaktivierter lokaler Authentifizierung erstellen können.

```JSON 
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        },
        "disableLocalAuth": {
            "type": "bool"
        }
     
    },
    "resources": [
        {
        "name": "[parameters('name')]",
        "type": "microsoft.insights/components",
        "location": "[parameters('regionId')]",
        "tags": "[parameters('tagsArray')]",
        "apiVersion": "2020-02-02-preview",
        "dependsOn": [],
        "properties": {
            "Application_Type": "[parameters('type')]",
            "Flow_Type": "Redfield",
            "Request_Source": "[parameters('requestSource')]",
            "WorkspaceResourceId": "[parameters('workspaceResourceId')]",
            "DisableLocalAuth": "[parameters('disableLocalAuth')]"
            }
    }
 ]
}

```

## <a name="troubleshooting"></a>Problembehandlung

Dieser Abschnitt enthält verschiedene Szenarien und Schritte, die Benutzer ausführen können, um Probleme zu beheben, bevor sie ein Supportticket erstellen. 

### <a name="ingestion-http-errors"></a>HTTP-Erfassungsfehler

Vom Erfassungsdienst werden unabhängig von der SDK-Sprache spezifische Fehler zurückgegeben. Netzwerkdatenverkehr kann mithilfe eines Tools wie Fiddler erfasst werden. Sie sollten den Datenverkehr nach dem in der Verbindungszeichenfolge festgelegten Erfassungsendpunkt filtern.

#### <a name="http11-400-authentication-not-support"></a>HTTP/1.1 400 Authentifizierung wird nicht unterstützt 

Dies deutet darauf hin, dass die Application Insights-Ressource nur für Azure AD konfiguriert wurde, das SDK aber nicht ordnungsgemäß konfiguriert ist und Daten an die falsche API gesendet werden.

> [!NOTE]
>  Azure AD wird von „v2/track“ nicht unterstützt. Wenn das SDK ordnungsgemäß konfiguriert ist, werden die Telemetriedaten an „v2.1/track“ gesendet.

Als Nächstes sollten Sie die SDK-Konfiguration überprüfen.

#### <a name="http11-401-authorization-required"></a>HTTP/1.1 401 Authorization required (Authorisierung erforderlich)

Dies deutet darauf hin, dass das SDK ordnungsgemäß konfiguriert wurde, aber kein gültiges Token abgerufen werden konnte. Möglicherweise liegt ein Problem mit Azure Active Directory vor.

Als Nächstes sollten Sie Ausnahmen in den SDK-Protokollen oder Netzwerkfehler von Azure Identity identifizieren.

#### <a name="http11-403-unauthorized"></a>HTTP/1.1 403 Unauthorized (Nicht autorisiert) 

Dies deutet darauf hin, dass das SDK mit Anmeldeinformationen konfiguriert wurde, denen keine Berechtigung für die Application Insights-Ressource oder für das Abonnement erteilt wurde.

Als Nächstes sollten Sie die Zugriffssteuerung der Application Insights-Ressource überprüfen. Das SDK muss mit Anmeldeinformationen konfiguriert werden, denen die Rolle „Herausgeber von Überwachungsmetriken“ zugewiesen wurde.

### <a name="language-specific-troubleshooting"></a>Sprachspezifische Problembehandlung

### <a name="aspnet-and-net"></a>[ASP.NET und .NET](#tab/net)

#### <a name="event-source"></a>Ereignisquelle

Das Application Insights-.NET SDK gibt Fehlerprotokolle über die Ereignisquelle aus. Weitere Informationen zum Sammeln von Ereignisquellenprotokollen finden Sie unter [Problembehandlung ohne Daten – Sammeln von Protokollen mit PerfView](asp-net-troubleshoot-no-data.md#PerfView).

Wenn das SDK kein Token abrufen kann, wird die Ausnahmemeldung als „Fehler beim Abrufen des AAD-Tokens Fehlermeldung: “ protokolliert.

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Interne Protokolle können mithilfe des folgenden Setups aktiviert werden. Sobald diese Funktion aktiviert ist, werden Fehlerprotokolle (einschließlich aller Fehler im Zusammenhang mit der Azure AD-Integration) in der Konsole angezeigt. Beispiel: Das Generieren des Tokens schlägt fehl, wenn falsche Anmeldeinformationen angegeben werden, oder es treten Fehler auf, wenn der Erfassungsendpunkt nicht mit den bereitgestellten Anmeldeinformationen authentifiziert werden kann.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").setInternalLogging(true, true);
```

### <a name="java"></a>[Java](#tab/java)

#### <a name="http-traffic"></a>HTTP-Datenverkehr

Sie können den Netzwerkdatenverkehr mithilfe eines Tools wie Fiddler untersuchen. Fügen Sie entweder die folgenden Proxyeinstellungen in der Konfigurationsdatei hinzu, um dem Datenverkehr das Tunneln durch Fiddler zu ermöglichen:

```JSON
"proxy": {
"host": "localhost",
"port": 8888
}
```

Oder fügen Sie die folgenden jvm-Argumente hinzu, wenn Sie Ihre Anwendung ausführen:`-Djava.net.useSystemProxies=true -Dhttps.proxyHost=localhost -Dhttps.proxyPort=8888`

Wenn Azure AD im Agent aktiviert ist, enthält der ausgehende Datenverkehr den HTTP-Header „Autorisierung“.


#### <a name="401-unauthorized"></a>401 – Nicht autorisiert 

Wenn die folgende WARN-Meldung in der Protokolldatei `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 401, please check your credentials` angezeigt wird, bedeutet das, dass der Agent keine Telemetriedaten gesendet hat. Sie haben die Azure AD-Authentifizierung auf dem Agenten wahrscheinlich nicht aktiviert, aber Ihre Application Insights-Ressource ist mit `DisableLocalAuth: true` konfiguriert. Stellen Sie sicher, dass Sie gültige Anmeldeinformationen übergeben und über die Zugriffsberechtigungen für Ihre Application Insights-Ressource verfügen.


Wenn Sie Fiddler verwenden, wird Ihnen möglicherweise der folgende Antwortheader angezeigt: `HTTP/1.1 401 Unauthorized - please provide the valid authorization token`.


#### <a name="credentialunavailableexception"></a>CredentialUnavailableException

Wenn die folgende Ausnahme in der Protokolldatei `com.azure.identity.CredentialUnavailableException: ManagedIdentityCredential authentication unavailable. Connection to IMDS endpoint cannot be established` angezeigt wird, bedeutet das, dass der Agent das Zugriffstoken nicht erfolgreich abrufen konnte. Der Grund dafür ist wahrscheinlich, dass Sie in Ihrer Konfiguration der benutzerseitig zugewiesenen verwalteten Identität eine ungültige `clientId` angegeben haben.


#### <a name="failed-to-send-telemetry"></a>Fehler beim Senden von Telemetriedaten

Wenn die folgende WARN-Meldung in der Protokolldatei `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 403, please check your credentials` angezeigt wird, bedeutet das, dass der Agent keine Telemetriedaten gesendet hat. Dies kann daran liegen, dass die bereitgestellten Anmeldeinformationen nicht nicht über die nötigen Zugriffsrechte für das Sammeln der Telemetriedaten in der Komponente verfügen.

Wenn Sie Fiddler verwenden, wird Ihnen möglicherweise der folgende Antwortheader angezeigt: `HTTP/1.1 403 Forbidden - provided credentials do not grant the access to ingest the telemetry into the component`.

Die Grundursache kann einer der folgenden Gründe sein:
- Sie haben die Ressource entweder mit aktivierter systemseitig zugewiesener verwalteter Identität erstellt oder Sie haben der Ressource zwar die benutzerseitig zugewiesene Identität zugeordnet, aber vergessen, die `Monitoring Metrics Publisher`-Rolle zur Ressource (bei Verwendung von SAMI) oder zur benutzerseitig zugewiesenen Identität (bei Verwendung von UAMI) hinzuzufügen.
- Sie haben zwar die richtigen Anmeldeinformationen angegeben, um die Zugriffstoken zu erhalten, aber die Anmeldeinformationen gehören nicht zur richtigen Application Insights-Ressource. Stellen Sie sicher, dass Ihre Ressource (VM, App Service usw.) oder die benutzerseitig zugewiesene Identität mit der `Monitoring Metrics Publisher`-Rolle in Ihrer Application Insights-Ressource angezeigt wird.

#### <a name="invalid-tenantid"></a>Ungültige TenantId

Wenn die folgende Ausnahme in der Protokolldatei `com.microsoft.aad.msal4j.MsalServiceException: Specified tenant identifier <TENANT-ID> is neither a valid DNS name, nor a valid external domain.` angezeigt wird, bedeutet das, dass der Agent das Zugriffstoken nicht erfolgreich abrufen konnte. Der Grund dafür ist wahrscheinlich, dass Sie in Ihrer Konfiguration des geheimen Clientschlüssels eine ungültige/falsche `tenantId` angegeben haben.

#### <a name="invalid-client-secret"></a>Ungültiger geheimer Clientschlüssel

Wenn die folgende Ausnahme in der Protokolldatei `com.microsoft.aad.msal4j.MsalServiceException: Invalid client secret is provided` angezeigt wird, bedeutet das, dass der Agent das Zugriffstoken nicht erfolgreich abrufen konnte. Der Grund dafür ist wahrscheinlich, dass Sie in Ihrer Konfiguration des geheimen Clientschlüssels eine ungültige `clientSecret` angegeben haben.


#### <a name="invalid-clientid"></a>Ungültige ClientId

Wenn die folgende Ausnahme in der Protokolldatei `com.microsoft.aad.msal4j.MsalServiceException: Application with identifier <CLIENT_ID> was not found in the directory` angezeigt wird, bedeutet das, dass der Agent das Zugriffstoken nicht erfolgreich abrufen konnte. Der Grund dafür ist wahrscheinlich, dass Sie in Ihrer Konfiguration des geheimen Clientschlüssels eine ungültige/falsche „clientId“ angegeben haben.

 Dies kann auftreten, wenn die Anwendung nicht vom Administrator des Mandanten installiert wurde oder wenn sie von den Benutzern des Mandanten keine Zustimmung erhalten hat. Unter Umständen haben Sie Ihre Authentifizierungsanforderung an den falschen Mandanten gesendet.

### <a name="python"></a>[Python](#tab/python)

#### <a name="error-starts-with-credential-error-with-no-status-code"></a>Der Fehler beginnt mit „Anmeldeinformationsfehler“ (ohne Statuscode)

Die Anmeldeinformationen, die Sie verwenden, sind fehlerhaft und der Client kann kein Autorisierungstoken abrufen. Dies liegt in der Regel daran, dass die erforderlichen Zustandsdaten fehlen. Ein Beispiel wäre die Eingabe von Anmeldeinformationen einer systemseitig verwalteten Identität, wenn die Ressource nicht für die Verwendung einer systemseitig verwalteten Identität konfiguriert ist.

#### <a name="error-starts-with-authentication-error-with-no-status-code"></a>Der Fehler beginnt mit „Authentifizierungsfehler“ (ohne Statuscode)

Der Client konnte sich mit den gegebenen Anmeldeinformationen nicht authentifizieren. Tritt normalerweise auf, wenn den Anmeldeinformationen nicht die richtigen Rollen zugewiesen sind.

#### <a name="im-getting-a-status-code-400-in-my-error-logs"></a>Ich erhalte den Statuscode 400 in meinen Fehlerprotokollen.

Wahrscheinlich fehlt Ihnen eine Anmeldeinformation oder Ihre Anmeldeinformation ist auf `None` festgelegt und Ihre Application Insights-Ressource ist mit `DisableLocalAuth: true` konfiguriert. Stellen Sie sicher, dass Sie gültige Anmeldeinformationen übergeben und über die Zugriffsberechtigungen für Ihre Application Insights-Ressource verfügen.

#### <a name="im-getting-a-status-code-403-in-my-error-logs"></a>Ich erhalte den Statuscode 403 in meinen Fehlerprotokollen.

Tritt in der Regel auf, wenn die bereitgestellten Anmeldeinformationen nicht über die erforderlichen Zugriffsberechtigungen verfügen, um Telemetriedaten für die Application Insights-Ressource zu erfassen. Stellen Sie sicher, dass Ihre KI-Ressource über die richtigen Rollenzuweisungen verfügt.

---
## <a name="next-steps"></a>Nächste Schritte
* [Überwachen Ihrer Telemetriedaten im Portal](overview-dashboard.md)
* [Diagnose mit Live Metrics Stream](live-stream.md)
