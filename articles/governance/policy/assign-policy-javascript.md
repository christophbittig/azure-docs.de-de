---
title: 'Schnellstart: Neue Richtlinienzuweisung mit JavaScript'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von JavaScript eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 4b64d33bf0b48d0584f818b212e524e3f9676edc
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323733"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mithilfe von JavaScript

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln. Im Rahmen dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden. Im Anschluss identifizieren Sie virtuelle Computer, die _nicht konform_ sind.

Die JavaScript-Bibliothek dient zum Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Anleitung erfahren Sie, wie Sie mithilfe der JavaScript-Bibliothek eine Richtlinienzuweisung erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses](https://azure.microsoft.com/free/) Konto erstellen, bevor Sie beginnen.

- **Node.js**: [Node.js](https://nodejs.org/) Version 12 oder höher ist erforderlich.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Hinzufügen der Policy-Bibliotheken

Damit JavaScript mit Azure Policy verwendet werden kann, müssen die Bibliotheken hinzugefügt werden. Diese Bibliotheken funktionieren überall dort, wo JavaScript verwendet werden kann, einschließlich [Bash unter Windows 10](/windows/wsl/install-win10).

1. Richten Sie ein Node.js-Projekt ein, indem Sie den folgenden Befehl ausführen.

   ```bash
   npm init -y
   ```

1. Fügen Sie einen Verweis auf die yargs-Bibliothek hinzu.

   ```bash
   npm install yargs
   ```

1. Fügen Sie einen Verweis zu den Azure Policy-Bibliotheken hinzu.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Fügen Sie einen Verweis auf die Active Directory-Authentifizierungsbibliothek hinzu.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Überprüfen Sie in _package.json_, ob `@azure/arm-policy` in Version **3.1.0** oder höher, `@azure/arm-policyinsights` in Version **3.2.0** oder höher und `@azure/ms-rest-nodeauth` in Version **3.0.5** oder höher vorliegen.

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Definition **Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden** (`06a78e20-9358-41c9-923c-fb736d382a4d`) zu. Mit dieser Richtliniendefinition werden Ressourcen identifiziert, die die in der Richtliniendefinition festgelegten Bedingungen nicht erfüllen.

1. Erstellen Sie eine neue Datei mit dem Namen _policyAssignment.js_, und geben Sie den folgenden Code ein.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");

   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {

       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);

           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };

       createAssignment();
   }
   ```

1. Geben Sie den folgenden Befehl in das Terminal ein:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

In diesen Befehlen werden folgende Informationen verwendet:

- **subID**: Die Abonnement-ID für den Authentifizierungskontext. Achten Sie darauf, `{subscriptionId}` durch Ihr Abonnement zu ersetzen.
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

1. Erstellen Sie eine neue Datei mit dem Namen _policyState.js_, und geben Sie den folgenden Code ein.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");

   if (argv.subID && argv.name) {

       const getStates = async () => {

           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };

       getStates();
   }
   ```

1. Geben Sie den folgenden Befehl in das Terminal ein:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

Ersetzen Sie `{subscriptionId}` durch das Abonnement, in dem die Kompatibilitätsergebnisse für die Richtlinienzuweisung mit dem Namen „audit-vm-manageddisks“ angezeigt werden sollen, die in den vorherigen Schritten erstellt wurde. Eine Liste anderer Bereiche und Möglichkeiten zum Zusammenfassen der Daten finden Sie im Artikel zu [PolicyStates*](/javascript/api/@azure/arm-policyinsights/)-Methoden.

Ihre Ergebnisse sollten in etwa wie im folgenden Beispiel aussehen:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Die Ergebnisse entsprechen dem, was im Azure-Portal auf der Registerkarte **Ressourcenkonformität** einer Richtlinienzuweisung angezeigt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

- Löschen Sie die Richtlinienzuweisung _Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger_ über das Portal. Da die Richtliniendefinition integriert ist, kann sie nicht entfernt werden.

- Wenn Sie die installierten Bibliotheken aus Ihrer Anwendung entfernen möchten, führen Sie den folgenden Befehl aus.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Richtliniendefinition zum Identifizieren nicht kompatibler Ressourcen in Ihrer Azure-Umgebung zugewiesen.

Weitere Informationen zum Zuweisen von Richtliniendefinitionen, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)
