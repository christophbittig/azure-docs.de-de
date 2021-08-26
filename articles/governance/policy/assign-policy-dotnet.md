---
title: 'Schnellstart: Neue Richtlinienzuweisung mit .NET Core'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von .NET Core eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 62e9b5634f5535e9a462a094ea6adee29695ee03
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324490"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mit .NET Core

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln. Im Rahmen dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden. Im Anschluss identifizieren Sie virtuelle Computer, die _nicht konform_ sind.

Die .NET Core-Bibliothek wird zum Verwalten von Azure-Ressourcen verwendet. In dieser Anleitung erfahren Sie, wie Sie mithilfe der .NET Core-Bibliothek für Azure Policy eine Richtlinienzuweisung erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Ein Azure-Dienstprinzipal, einschließlich _clientId_ und _clientSecret_. Falls Sie nicht über einen Dienstprinzipal für die Verwendung mit Azure Policy verfügen oder einen neuen erstellen möchten, helfen Ihnen die Informationen unter [Azure-Verwaltungsbibliotheken für die .NET-Authentifizierung](/dotnet/azure/sdk/authentication#mgmt-auth) weiter.
  Überspringen Sie den Schritt zum Installieren der .NET Core-Pakete, weil wir dies in den nächsten Schritten durchführen.

## <a name="create-the-azure-policy-project"></a>Erstellen des Azure Policy-Projekts

Erstellen Sie eine neue Konsolenanwendung, und installieren Sie die erforderlichen Pakete, um für .NET Core das Verwalten von Azure Policy zu ermöglichen.

1. Überprüfen Sie, ob die aktuelle .NET Core-Version (mindestens **3.1.8**) installiert ist. Falls nicht, können Sie sie unter [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) herunterladen.

1. Initialisieren Sie eine neue .NET Core-Konsolenanwendung mit dem Namen „policyAssignment“:

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Ändern Sie die Verzeichnisse in den neuen Projektordner, und installieren Sie die erforderlichen Pakete für Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Ersetzen Sie die Standarddatei `program.cs` durch den folgenden Code, und speichern Sie die aktualisierte Datei:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;

   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];

               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Kompilieren und veröffentlichen Sie die Konsolenanwendung `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Definition **Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden** (`06a78e20-9358-41c9-923c-fb736d382a4d`) zu. Mit dieser Richtliniendefinition werden Ressourcen identifiziert, die die in der Richtliniendefinition festgelegten Bedingungen nicht erfüllen.

1. Ändern Sie die Verzeichnisse in den `{run-folder}`, den Sie mit dem vorherigen `dotnet publish`-Befehl definiert haben.

1. Geben Sie den folgenden Befehl in das Terminal ein:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

In diesen Befehlen werden folgende Informationen verwendet:

- Ersetzen Sie `{tenantId}` durch Ihre Mandanten-ID.
- `{clientId}`: Ersetzen Sie diese ID durch die Client-ID Ihres Dienstprinzipals.
- `{clientSecret}`: Ersetzen Sie dieses Geheimnis durch den geheimen Clientschlüssel Ihres Dienstprinzipals.
- Ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID.
- **name**: Der eindeutige Name für das Richtlinienzuweisungsobjekt. Im obigen Beispiel wird _audit-vm-manageddisks_ verwendet.
- **displayName**: Der Anzeigename für die Richtlinienzuweisung. Verwenden Sie in diesem Fall _Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger_.
- **policyDefID**: Der Pfad der Richtliniendefinition, auf dessen Grundlage Sie die Zuweisung erstellen. In diesem Fall ist es die ID der Richtliniendefinition _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_.
- **description**: Eine ausführlichere Erläuterung des Richtlinienzwecks oder des Grunds, warum sie diesem Bereich zugewiesen ist
- **scope**: Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Ein solcher Bereich kann eine Verwaltungsgruppe oder auch nur eine einzelne Ressource sein. Ersetzen Sie `{scope}` unbedingt durch eins der folgenden Muster:
  - Verwaltungsgruppe: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonnement: `/subscriptions/{subscriptionId}`
  - Ressourcengruppe: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Ressource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Sie können nun nicht konforme Ressourcen identifizieren, um den Konformitätszustand Ihrer Umgebung nachzuvollziehen.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Nachdem Sie nun die Richtlinienzuweisung erstellt haben, können Sie Ressourcen identifizieren, die nicht konform sind.

1. Initialisieren Sie eine neue .NET Core-Konsolenanwendung mit dem Namen „policyCompliance“:

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Ändern Sie die Verzeichnisse in den neuen Projektordner, und installieren Sie die erforderlichen Pakete für Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Ersetzen Sie die Standarddatei `program.cs` durch den folgenden Code, und speichern Sie die aktualisierte Datei:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;

   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];

               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };

                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Kompilieren und veröffentlichen Sie die Konsolenanwendung `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Ändern Sie die Verzeichnisse in den `{run-folder}`, den Sie mit dem vorherigen `dotnet publish`-Befehl definiert haben.

1. Geben Sie den folgenden Befehl in das Terminal ein:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

In diesen Befehlen werden folgende Informationen verwendet:

- Ersetzen Sie `{tenantId}` durch Ihre Mandanten-ID.
- `{clientId}`: Ersetzen Sie diese ID durch die Client-ID Ihres Dienstprinzipals.
- `{clientSecret}`: Ersetzen Sie dieses Geheimnis durch den geheimen Clientschlüssel Ihres Dienstprinzipals.
- Ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID.
- **name**: Der eindeutige Name für das Richtlinienzuweisungsobjekt. Im obigen Beispiel wird _audit-vm-manageddisks_ verwendet.

Die Ergebnisse in `response` entsprechen dem, was im Azure-Portal auf der Registerkarte **Ressourcenkonformität** einer Richtlinienzuweisung angezeigt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

- Löschen Sie die Richtlinienzuweisung _Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger_ über das Portal. Da die Richtliniendefinition integriert ist, kann sie nicht entfernt werden.

- Falls Sie die .NET Core-Konsolenanwendung und die installierten Pakete entfernen möchten, löschen Sie die Projektordner `policyAssignment` und `policyCompliance`.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Richtliniendefinition zum Identifizieren nicht kompatibler Ressourcen in Ihrer Azure-Umgebung zugewiesen.

Weitere Informationen zum Zuweisen von Richtliniendefinitionen, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)
