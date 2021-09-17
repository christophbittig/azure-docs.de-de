---
title: Blockieren von Verbindungen für bestimmte API-Connectors
description: Einschränken der Erstellung und Verwendung von API-Verbindungen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: f13ffe10ed9ade4e6fe8767ef61b6709747b7d2a
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606248"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Blockieren der von Connectors in Azure Logic Apps erstellten Verbindungen

Falls Ihre Organisation das Herstellen einer Verbindung mit eingeschränkten oder nicht genehmigten Ressourcen mithilfe der Connectors in Azure Logic Apps nicht erlaubt, können Sie die Funktion zum Erstellen und Verwenden dieser Verbindungen in Logik-App-Workflows blockieren. Mit [Azure Policy](../governance/policy/overview.md) können Sie [Richtlinien](../governance/policy/overview.md#policy-definition) definieren und erzwingen, mit denen die Erstellung oder Verwendung von Verbindungen für Connectors, die Sie blockieren möchten, verhindert wird. Es kann beispielsweise sein, dass Sie aus Sicherheitsgründen Verbindungen mit bestimmten Social Media-Plattformen oder anderen Diensten und Systemen blockieren möchten.

In diesem Thema wird veranschaulicht, wie Sie über das Azure-Portal eine Richtlinie einrichten, mit der bestimmte Verbindungen blockiert werden. Sie können Richtliniendefinitionen aber auch auf andere Arten erstellen, z. B. per Azure-REST-API, Azure PowerShell, Azure CLI und mit Azure Resource Manager-Vorlagen. Weitere Informationen finden Sie im [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie noch nicht über ein Abonnement verfügen, müssen Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

* Die Verweis-ID für den Connector, den Sie blockieren möchten. Weitere Informationen finden Sie im Abschnitt zur [Ermittlung der Verweis-ID für den Connector](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Ermitteln der Verweis-ID für den Connector

Führen Sie die [Schritte für das Azure-Portal](#connector-ID-portal) aus, wenn Sie bereits über eine Logik-App mit der Verbindung verfügen, die Sie blockieren möchten. Führen Sie andernfalls die folgenden Schritte aus:

1. Navigieren Sie zur [Liste mit den Logic Apps-Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors).

1. Suchen Sie nach der Referenzseite für den Connector, den Sie blockieren möchten.

   Greifen Sie beispielsweise auf die folgende Seite zu, wenn Sie den Instagram-Connector, der veraltet ist, blockieren möchten:

   `https://docs.microsoft.com/connectors/instagram/`

1. Kopieren Sie am Ende der Seiten-URL die Verweis-ID des Connectors ohne den Schrägstrich (`/`), z. B. `instagram`, und speichern Sie sie.

   Später beim Erstellen Ihrer Richtliniendefinition verwenden Sie diese ID in der Bedingungsanweisung der Definition, z. B.:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure-Portal

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrer Logik-App, und öffnen Sie sie.

1. Wählen Sie im Logik-App-Menü die Option **Logik-App-Codeansicht** aus, damit Sie die JSON-Definition Ihrer Logik-App anzeigen können.

   ![Öffnen von „Logik-App-Codeansicht“ zum Ermitteln der Connector-ID](./media/block-connections-connectors/code-view-connector-id.png)

1. Suchen Sie nach dem Objekt `parameters`, in dem das Objekt `$connections` enthalten ist. Darin sind wiederum das Objekt `{connection-name}` für die einzelnen Verbindungen Ihrer Logik-App und Informationen zur jeweiligen Verbindung enthalten:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Suchen Sie beispielsweise für den Instagram-Connector nach dem Objekt `instagram`, mit dem eine Instagram-Verbindung identifiziert wird:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. Suchen Sie für die Verbindung, die Sie blockieren möchten, nach der Eigenschaft `id` und dem zugehörigen Wert im folgenden Format: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Hier sind beispielsweise die Eigenschaft `id` und der Wert für eine Instagram-Verbindung angegeben:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. Kopieren Sie am Ende des Eigenschaftswerts `id` die Verweis-ID des Connectors, z. B. `instagram`, und speichern Sie sie.

   Später beim Erstellen Ihrer Richtliniendefinition verwenden Sie diese ID in der Bedingungsanweisung der Definition, z. B.:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Erstellen einer Richtlinie zum Blockieren der Verbindungsherstellung

Führen Sie die folgenden Schritte aus, um die Erstellung einer Verbindung in einer Logik-App vollständig zu blockieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Geben Sie im Suchfeld des Portals `policy` (bzw. „Richtlinie“) ein, und wählen Sie die Option **Richtlinie** aus.

   ![Suchen und Auswählen von „Richtlinie“ im Azure-Portal](./media/block-connections-connectors/find-select-azure-policy.png)

1. Wählen Sie im Menü **Richtlinie** unter **Erstellen** die Option **Definitionen** >  **+ Richtliniendefinition** aus.

   ![Auswählen von „Definitionen“ > „+ Richtliniendefinition“](./media/block-connections-connectors/add-new-policy-definition.png)

1. Geben Sie unter **Richtliniendefinition** die Informationen zu Ihrer Richtliniendefinition basierend auf den Eigenschaften an, die unter dem Beispiel beschrieben sind:

   ![Screenshot, der die Eigenschaften für „Richtliniendefinition“ zeigt.](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Definitionsspeicherort** | Ja | <*Name des Azure-Abonnements*> | Das Azure-Abonnement, das für die Richtliniendefinition verwendet werden soll. <p><p>1. Wählen Sie die Schaltfläche mit den Auslassungszeichen ( **...** ) aus, um nach Ihrem Abonnement zu suchen. <br>2. Suchen Sie in der Liste **Abonnement** nach Ihrem Abonnement, und wählen Sie es aus. <br>3. Wählen Sie die Option **Auswählen** aus, wenn Sie fertig sind. |
   | **Name** | Ja | <*policy-definition-name*> | Der Name, der für die Richtliniendefinition verwendet werden soll. |
   | **Beschreibung** | Nein | <*policy-definition-name*> | Eine Beschreibung für die Richtliniendefinition. |
   | **Kategorie** | Ja | **Logik-Apps** | Der Name einer vorhandenen Kategorie oder neuen Kategorie für die Richtliniendefinition. |
   | **Richtlinienerzwingung** | Ja | **Aktiviert** | Mit dieser Einstellung wird angegeben, ob die Richtliniendefinition aktiviert oder deaktiviert werden soll, wenn Sie Ihre Arbeit speichern. |
   ||||

1. Unter **RICHTLINIENREGEL** wird das JSON-Bearbeitungsfeld vorab mit einer Vorlage für Richtliniendefinitionen gefüllt. Ersetzen Sie diese Vorlage durch Ihre [Richtliniendefinition](../governance/policy/concepts/definition-structure.md), indem Sie die unten in der Tabelle beschriebenen Eigenschaften und die folgende Syntax verwenden:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | `mode` | `All` | Der Modus, mit dem die von der Richtlinie ausgewerteten Ressourcentypen bestimmt werden. <p><p>In diesem Szenario wird `mode` auf `All` festgelegt, um die Richtlinie auf Azure-Ressourcengruppen, -Abonnements und alle Ressourcentypen anzuwenden. <p><p>Weitere Informationen finden Sie unter [Struktur von Richtliniendefinitionen: Modus](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Bedingung, die bestimmt, wann die Richtlinienregel erzwungen werden soll <p><p>In diesem Szenario wird mit `{condition-to-evaluate}` bestimmt, ob der Wert `api.id` in `Microsoft.Web/connections/api.id` mit dem Element `*managedApis/{connector-name}` übereinstimmt, in dem ein Platzhalterzeichen (*) angegeben ist. <p><p>Weitere Informationen finden Sie unter [Struktur von Richtliniendefinitionen: Richtlinienregel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `field` | `Microsoft.Web/connections/api.id` | Wert `field` für den Vergleich mit der Bedingung <p><p>In diesem Szenario wird für `field` der [*Alias*](../governance/policy/concepts/definition-structure.md#aliases), `Microsoft.Web/connections/api.id` verwendet, um auf den Wert in der Connectoreigenschaft `api.id` zuzugreifen. |
   | `like` | `*managedApis/{connector-name}` | Logischer Operator und Wert für den Vergleich des `field`-Werts <p><p>In diesem Szenario wird mit dem Operator `like` und dem Platzhalterzeichen (*) jeweils sichergestellt, dass die Regel unabhängig von der Region funktioniert und dass die Zeichenfolge `*managedApis/{connector-name}` der Wert für den Abgleich ist. Hierbei steht `{connector-name}` für die ID des Connectors, den Sie blockieren möchten. <p><p>Angenommen, Sie möchten die Erstellung von Verbindungen mit Social Media-Plattformen oder -Datenbanken blockieren: <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server oder Azure SQL: `sql` <p><p>Informationen zum Ermitteln dieser Connector-IDs finden Sie weiter oben in diesem Thema unter [Ermitteln der Verweis-ID für den Connector](#connector-reference-ID). |
   | `then` | `{effect-to-apply}` | Auswirkung, die bei Erfüllung der `if`-Bedingung erfolgen soll <p><p>In diesem Szenario umfasst `{effect-to-apply}` das Blockieren und Ablehnen einer Anforderung oder eines Vorgangs, die bzw. der die Bedingungen der Richtlinie nicht erfüllt. <p><p>Weitere Informationen finden Sie unter [Struktur von Richtliniendefinitionen: Richtlinienregel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect` umfasst die Blockade der Anforderung, mit der die angegebene Verbindung erstellt werden soll <p><p>Weitere Informationen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen: Verweigern](../governance/policy/concepts/effects.md#deny). |
   ||||

   Angenommen, Sie möchten die Erstellung von Verbindungen mit dem Instagram-Connector blockieren. Hier ist die Richtliniendefinition angegeben, die Sie verwenden können:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Hier ist dargestellt, wie das Feld **RICHTLINIENREGEL** angezeigt wird:

   ![Screenshot, der das Feld „RICHTLINIENREGEL“ mit einem Beispiel für eine Richtlinienregel zeigt.](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   Für mehrere Connectors können Sie weitere Bedingungen hinzufügen, z. B.:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind. Nachdem Sie die Richtliniendefinition gespeichert haben, werden von Azure Policy weitere Eigenschaftswerte generiert und der Richtliniendefinition hinzugefügt.

1. [Erstellen Sie als Nächstes eine Richtlinienzuweisung](#create-policy-assignment), um die Richtliniendefinition dem Ort zuzuweisen, an dem Sie sie erzwingen möchten.

Weitere Informationen zu Azure Policy-Definitionen finden Sie in den folgenden Artikeln:

* [Struktur von Azure Policy-Definitionen](../governance/policy/concepts/definition-structure.md)
* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)
* [Integrierte Azure Policy-Richtliniendefinitionen für Azure Logic Apps](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Erstellen einer Richtlinie zum Blockieren der Nutzung von Verbindungen

Wenn Sie eine Verbindung in einer Logik-App erstellen, handelt es sich dabei um eine separate Azure-Ressource. Wenn Sie nur die Logik-App löschen, wird die Verbindung nicht automatisch gelöscht und ist so lange vorhanden, bis das Löschen erfolgt. Es kann vorkommen, dass die Verbindung bereits vorhanden ist oder dass Sie die Verbindung für die Nutzung außerhalb einer Logik-App erstellen möchten. Sie können die Funktion trotzdem blockieren, um eine vorhandene Verbindung in einer Logik-App zu verwenden. Erstellen Sie hierfür eine Richtlinie, mit der das Speichern von Logik-Apps verhindert wird, die über die eingeschränkte oder nicht genehmigte Verbindung verfügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Geben Sie im Suchfeld des Portals `policy` (bzw. „Richtlinie“) ein, und wählen Sie die Option **Richtlinie** aus.

   ![Suchen und Auswählen von „Richtlinie“ im Azure-Portal](./media/block-connections-connectors/find-select-azure-policy.png)

1. Wählen Sie im Menü **Richtlinie** unter **Erstellen** die Option **Definitionen** >  **+ Richtliniendefinition** aus.

   ![Auswählen von „Definitionen“ > „+ Richtliniendefinition“](./media/block-connections-connectors/add-new-policy-definition.png)

1. Geben Sie unter **Richtliniendefinition** die Informationen zu Ihrer Richtliniendefinition basierend auf den Eigenschaften an, die unter dem Beispiel beschrieben sind (wir verwenden weiterhin Instagram als Beispiel):

   ![Eigenschaften von Richtliniendefinitionen](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Definitionsspeicherort** | Ja | <*Name des Azure-Abonnements*> | Das Azure-Abonnement, das für die Richtliniendefinition verwendet werden soll. <p><p>1. Wählen Sie die Schaltfläche mit den Auslassungszeichen ( **...** ) aus, um nach Ihrem Abonnement zu suchen. <br>2. Suchen Sie in der Liste **Abonnement** nach Ihrem Abonnement, und wählen Sie es aus. <br>3. Wählen Sie die Option **Auswählen** aus, wenn Sie fertig sind. |
   | **Name** | Ja | <*policy-definition-name*> | Der Name, der für die Richtliniendefinition verwendet werden soll. |
   | **Beschreibung** | Nein | <*policy-definition-name*> | Eine Beschreibung für die Richtliniendefinition. |
   | **Kategorie** | Ja | **Logik-Apps** | Der Name einer vorhandenen Kategorie oder neuen Kategorie für die Richtliniendefinition. |
   | **Richtlinienerzwingung** | Ja | **Aktiviert** | Mit dieser Einstellung wird angegeben, ob die Richtliniendefinition aktiviert oder deaktiviert werden soll, wenn Sie Ihre Arbeit speichern. |
   ||||

1. Unter **RICHTLINIENREGEL** wird das JSON-Bearbeitungsfeld vorab mit einer Vorlage für Richtliniendefinitionen gefüllt. Ersetzen Sie diese Vorlage durch Ihre [Richtliniendefinition](../governance/policy/concepts/definition-structure.md), indem Sie die unten in der Tabelle beschriebenen Eigenschaften und die folgende Syntax verwenden:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | `mode` | `All` | Der Modus, mit dem die von der Richtlinie ausgewerteten Ressourcentypen bestimmt werden. <p><p>In diesem Szenario wird `mode` auf `All` festgelegt, um die Richtlinie auf Azure-Ressourcengruppen, -Abonnements und alle Ressourcentypen anzuwenden. <p><p>Weitere Informationen finden Sie unter [Struktur von Richtliniendefinitionen: Modus](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Bedingung, die bestimmt, wann die Richtlinienregel erzwungen werden soll <p><p>In diesem Szenario wird mit `{condition-to-evaluate}` bestimmt, ob die Zeichenfolgenausgabe von `[string(field('Microsoft.Logic/workflows/parameters'))]` die Zeichenfolge `{connector-name}` enthält. <p><p>Weitere Informationen finden Sie unter [Struktur von Richtliniendefinitionen: Richtlinienregel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | Wert für den Vergleich mit der Bedingung <p><p>In diesem Szenario ist `value` die Zeichenfolgenausgabe von `[string(field('Microsoft.Logic/workflows/parameters'))]`, mit der das Objekt `$connectors` im Objekt `Microsoft.Logic/workflows/parameters` in eine Zeichenfolge konvertiert wird. |
   | `contains` | `{connector-name}` | Logischer Operator und Wert für den Vergleich mit der Eigenschaft `value` <p><p>In diesem Szenario wird mit dem Operator `contains` sichergestellt, dass die Regel unabhängig davon funktioniert, wo `{connector-name}` erscheint. Hierbei ist die Zeichenfolge `{connector-name}` die ID für den Connector, den Sie einschränken oder blockieren möchten. <p><p>Angenommen, Sie möchten die Verwendung von Verbindungen mit Social Media-Plattformen oder -Datenbanken blockieren: <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server oder Azure SQL: `sql` <p><p>Informationen zum Ermitteln dieser Connector-IDs finden Sie weiter oben in diesem Thema unter [Ermitteln der Verweis-ID für den Connector](#connector-reference-ID). |
   | `then` | `{effect-to-apply}` | Auswirkung, die bei Erfüllung der `if`-Bedingung erfolgen soll <p><p>In diesem Szenario umfasst `{effect-to-apply}` das Blockieren und Ablehnen einer Anforderung oder eines Vorgangs, die bzw. der die Bedingungen der Richtlinie nicht erfüllt. <p><p>Weitere Informationen finden Sie unter [Struktur von Richtliniendefinitionen: Richtlinienregel](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect` dient zum Ablehnen (`deny`) oder Blockieren der Anforderung zum Speichern einer Logik-App, von der die angegebene Verbindung genutzt wird <p><p>Weitere Informationen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen: Verweigern](../governance/policy/concepts/effects.md#deny). |
   ||||

   Angenommen, Sie möchten das Speichern von Logik-Apps blockieren, für die Instagram-Verbindungen verwendet werden. Hier ist die Richtliniendefinition angegeben, die Sie verwenden können:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Hier ist dargestellt, wie das Feld **RICHTLINIENREGEL** angezeigt wird:

   ![Regel für Richtliniendefinition](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind. Nachdem Sie die Richtliniendefinition gespeichert haben, werden von Azure Policy weitere Eigenschaftswerte generiert und der Richtliniendefinition hinzugefügt.

1. [Erstellen Sie als Nächstes eine Richtlinienzuweisung](#create-policy-assignment), um die Richtliniendefinition dem Ort zuzuweisen, an dem Sie sie erzwingen möchten.

Weitere Informationen zu Azure Policy-Definitionen finden Sie in den folgenden Artikeln:

* [Struktur von Azure Policy-Definitionen](../governance/policy/concepts/definition-structure.md)
* [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)
* [Integrierte Azure Policy-Richtliniendefinitionen für Azure Logic Apps](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

Als Nächstes müssen Sie die Richtliniendefinition dem Element zuweisen, für das Sie die Richtlinie erzwingen möchten, z. B. einer oder mehreren Ressourcengruppen, einem Azure AD-Mandanten (Azure Active Directory) oder einem Azure-Abonnement. Führen Sie die folgenden Schritte aus, um eine Richtlinienzuweisung zu erstellen:

1. Melden Sie sich wieder beim [Azure-Portal](https://portal.azure.com) an, falls Sie sich abgemeldet haben. Geben Sie im Suchfeld des Portals `policy` (bzw. „Richtlinie“) ein, und wählen Sie die Option **Richtlinie** aus.

   ![Suchen und Auswählen von „Richtlinie“ im Azure-Portal](./media/block-connections-connectors/find-select-azure-policy.png)

1. Wählen Sie im Menü **Richtlinie** unter **Erstellen** die Option **Zuweisungen** > **Richtlinie zuweisen** aus.

   ![Auswählen von „Zuweisungen“ > „Zuweisen“](./media/block-connections-connectors/add-new-policy-assignment.png)

1. Geben Sie unter **Grundlagen** die folgenden Informationen für die Richtlinienzuweisung an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Umfang** | Ja | Die Ressourcen, für die Sie die Richtlinienzuweisung erzwingen möchten. <p><p>1. Wählen Sie neben dem Feld **Bereich** die Schaltfläche mit den Auslassungszeichen ( **...** ) aus. <br>2. Wählen Sie in der Liste **Abonnement** das Azure-Abonnement aus. <br>3. Wählen Sie optional in der Liste **Ressourcengruppe** die Ressourcengruppe aus. <br>4. Wählen Sie die Option **Auswählen** aus, wenn Sie fertig sind. |
   | **Ausschlüsse** | Nein | Alle Azure-Ressourcen, die aus der Richtlinienzuweisung ausgeschlossen werden sollen. <p><p>1. Wählen Sie neben dem Feld **Ausschlüsse** die Schaltfläche mit den Auslassungszeichen ( **...** ) aus. <br>2. Wählen Sie in der Liste **Ressource** die Ressource und dann die Option **Zu ausgewähltem Bereich hinzufügen** aus. <br>3. Klicken Sie auf **Speichern**, wenn Sie fertig sind. |
   | **Richtliniendefinition** | Ja | Der Name für die Richtliniendefinition, die Sie zuweisen und erzwingen möchten. In diesem Beispiel wird weiterhin die Beispielrichtlinie zum „Blockieren von Instagram-Verbindungen“ verwendet. <p><p>1. Wählen Sie neben dem Feld **Richtliniendefinition** die Schaltfläche mit den Auslassungszeichen ( **...** ) aus. <br>2. Suchen Sie nach der Richtliniendefinition, und wählen Sie sie aus, indem Sie den Filter **Typ** oder das Feld **Suche** verwenden. <br>3. Wählen Sie die Option **Auswählen** aus, wenn Sie fertig sind. |
   | **Zuweisungsname** | Ja | Der Name für die Richtlinienzuweisung, wenn er sich vom Namen der Richtliniendefinition unterscheidet. |
   | **Zuweisungs-ID** | Ja | Die automatisch generierte ID für die Richtlinienzuweisung. |
   | **Beschreibung** | Nein | Eine Beschreibung für die Richtlinienzuweisung. |
   | **Richtlinienerzwingung** | Ja | Die Einstellung, mit der die Richtlinienzuweisung aktiviert oder deaktiviert wird. |
   | **Zugewiesen von** | Nein | Der Name der Person, die die Richtlinienzuweisung erstellt und angewendet hat. |
   ||||

   Gehen Sie beispielsweise wie folgt vor, um die Richtlinie basierend auf dem Instagram-Beispiel einer Azure-Ressourcengruppe zuzuweisen:

   ![Eigenschaften für Richtlinienzuweisung](./media/block-connections-connectors/policy-assignment-basics.png)

1. Wählen Sie abschließend **Überprüfen + erstellen** aus.

   Nachdem Sie eine Richtlinie erstellt haben, müssen Sie ggf. bis zu 15 Minuten warten, bis sie wirksam wird. Bei Änderungen kann es zu ähnlichen Verzögerungen kommen.

1. Wenn die Richtlinie wirksam ist, können Sie die [Richtlinie testen](#test-policy).

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Testen der Richtlinie

Beginnen Sie zum Testen Ihrer Richtlinie mit der Erstellung einer Verbindung, indem Sie den nun eingeschränkten Connector im Logik-App-Designer verwenden. Wenn Sie weiter das Instagram-Beispiel verwenden und sich bei Instagram anmelden, erhalten Sie einen Fehler der folgenden Art mit einem Hinweis, dass die Verbindung von Ihrer Logik-App nicht erstellt werden konnte:

![Verbindungsfehler aufgrund der angewendeten Richtlinie](./media/block-connections-connectors/connection-failure-message.png)

Die Meldung enthält die folgenden Informationen:

| BESCHREIBUNG | Inhalt |
|-------------|---------|
| Grund für den Fehler | `"Resource 'instagram' was disallowed by policy."` |
| Zuweisungsname | `"Block Instagram connections"` |
| Zuweisungs-ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| Richtliniendefinitions-ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich weiter über [Azure Policy](../governance/policy/overview.md).