---
title: Authentifizieren von Workflows mit verwalteten Identitäten
description: Verwenden Sie eine verwaltete Identität, um Trigger und Aktionen für durch Azure AD geschützte Ressourcen ohne Anmeldeinformationen oder Geheimnisse zu authentifizieren.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 06/25/2021
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: 76edcac6b77b70928cb2d6cd378b421b68b3d3ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346230"
---
# <a name="authenticate-access-to-azure-resources-using-managed-identities-in-azure-logic-apps"></a>Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps

Einige Trigger und Aktionen in Logik-App-Workflows unterstützen die Verwendung einer [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) (früher als *verwaltete Dienstidentität (Managed Service Identity, MSI)* bezeichnet) für die Authentifizierung beim Herstellen einer Verbindung mit Ressourcen, die durch Azure Active Directory (Azure AD) geschützt sind. Wenn für Ihre Logik-App-Ressource eine verwaltete Identität aktiviert und eingerichtet ist, müssen Sie keine eigenen Anmeldeinformationen, Geheimnisse oder Azure AD-Token verwenden. Azure verwaltet diese Identität und hilft, die Sicherheit von Authentifizierungsinformationen zu bewahren, da Sie keine Geheimnisse oder Token verwalten müssen.

In diesem Artikel wird gezeigt, wie Sie beide Arten von verwalteten Identitäten für Ihre Logik-App einrichten. Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Trigger und Aktionen, die verwaltete Identitäten unterstützen](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [Grenzwerte für verwaltete Identitäten für Logik-Apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-Dienste, die verwaltete Identitäten für die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

<a name="triggers-actions-managed-identity"></a>

## <a name="where-to-use-managed-identities"></a>Verwendungsbereich für verwaltete Identitäten

Azure Logic Apps unterstützt sowohl [*systemseitig zugewiesene* verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) als auch [*benutzerseitig zugewiesene* verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md), die Sie je nach Ausführungsort Ihrer Logik-App-Workflows für eine Gruppe von Logik-Apps freigeben können:

* Eine Logik-App mit mehreren Mandanten (Verbrauchsplan) unterstützt sowohl die systemseitig zugewiesene Identität als auch eine *einzelne* benutzerseitig zugewiesene Identität. Auf Logik-App-Ebene oder Verbindungsebene können Sie jedoch nur einen verwalteten Identitätstyp verwenden, da Sie nicht beide gleichzeitig aktivieren können.

  Eine auf einem einzelnen Mandanten basierende Logik-App (Standardplan) unterstützt derzeit nur die systemseitig zugewiesene Identität.

  Weitere Informationen zu mehreren Mandanten (Verbrauchsplan) und einem einzelnen Mandanten (Standardplan) finden Sie in der Dokumentation [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung](single-tenant-overview-compare.md).

<a name="built-in-managed-identity"></a>
<a name="managed-connectors-managed-identity"></a>

* Nur bestimmte integrierte und verwaltete Connectorvorgänge, die Azure AD Open Authentication unterstützen, können eine verwaltete Identität für die Authentifizierung verwenden. Die folgende Tabelle enthält nur eine *Beispielauswahl*. Eine vollständige Liste finden Sie unter [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

  | Vorgangsart | Unterstützte Vorgänge |
  |----------------|----------------------|
  | Integriert | – Azure API Management <br>– Azure App Services <br>– Azure Functions <br>– HTTP <br>– HTTP + Webhook <p><p> **Hinweis**: HTTP-Vorgänge können zwar mithilfe der systemseitig zugewiesenen Identität Verbindungen mit Azure Storage-Konten hinter Azure-Firewalls authentifizieren, unterstützen jedoch nicht die Authentifizierung derselben Verbindungen mithilfe der benutzerseitig zugewiesenen verwalteten Identität. |
  | Verwalteter Connector (**Vorschau**) | – Azure Automation <br>– Azure Event Grid <br>– Azure Key Vault <br>– Azure Resource Manager <br>– HTTP mit Azure AD |
  |||

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). Für die verwaltete Identität sowie die Azure-Zielressource, auf die Sie zugreifen möchten, muss dasselbe Azure-Abonnement verwendet werden.

* Damit eine verwaltete Identität Zugriff auf eine Azure-Ressource erhält, müssen Sie der Zielressource eine Rolle für diese Identität hinzufügen. Für das Hinzufügen von Rollen benötigen Sie [Azure AD-Administratorberechtigungen](../active-directory/roles/permissions-reference.md), mit denen Rollen verwalteten Identitäten im zugehörigen Azure AD-Mandanten zugewiesen werden können.

* Die Azure-Zielressource, auf die Sie zugreifen möchten. Für diese Ressource fügen Sie eine Rolle für die verwaltete Identität hinzu, die der Logik-App hilft, den Zugriff auf die Zielressource zu authentifizieren.

* [Die Logik-App, in der Sie den Trigger oder die Aktionen verwenden möchten, die verwaltete Identitäten unterstützen](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

## <a name="enable-managed-identity"></a>Aktivieren einer verwalteten Identität

Folgen Sie zum Einrichten der verwalteten Identität, die Sie verwenden möchten, dem Link für diese Identität:

* [Systemseitig zugewiesene Identität](#system-assigned)
* [Benutzerseitig zugewiesene Identität](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Aktivieren systemseitig zugewiesener Identitäten

Im Unterschied zu benutzerseitig zugewiesenen Identitäten müssen Sie systemseitig zugewiesene Identitäten nicht manuell erstellen. Zum Einrichten der systemseitig zugewiesenen Identität für Ihre Logik-App stehen folgende Optionen zur Verfügung:

* [Azure portal](#azure-portal-system-logic-app)
* [Azure Resource Manager-Vorlage (ARM-Vorlage)](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivieren der systemseitig zugewiesenen Identität im Azure-Portal

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Logik-App in der Designeransicht.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus. Wählen Sie **Vom System zugewiesen** > **Ein** > **Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

   ![Aktivieren der systemseitig zugewiesenen Identität](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Wenn Sie eine Fehlermeldung erhalten, dass Sie nur eine einzige verwaltete Identität verwenden können, ist Ihrer Logik-App bereits eine benutzerseitig zugewiesene Identität zugeordnet. Bevor Sie die systemseitig zugewiesene Identität hinzufügen können, müssen Sie zuerst die benutzerseitig zugewiesene Identität aus Ihrer Logik-App *entfernen*.

   In der Logik-App kann nun die systemseitig zugewiesene Identität verwendet werden, die bei Azure AD registriert ist und durch eine Objekt-ID dargestellt wird.

   ![Objekt-ID für die systemseitig zugewiesene Identität](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **Objekt-ID** | <*Identität-Ressourcen-ID*> | Eine GUID (Globally Unique Identifier), die die systemseitig zugewiesene Identität für Ihre Logik-App in einem Azure AD-Mandanten angibt |
   ||||

1. Führen Sie nun die [Schritte für das Gewähren des Zugriffs der Identität auf die Ressource](#access-other-resources) weiter unten in diesem Thema aus.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-an-arm-template"></a>Aktivieren der systemseitig zugewiesenen Identität in einer ARM-Vorlage

Sie können eine [ARM-Vorlage](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) verwenden, um das Erstellen und Bereitstellen von Azure-Ressourcen wie Logik-Apps zu automatisieren. Um die systemseitig zugewiesene verwaltete Identität für die Logik-App in der Vorlage zu aktivieren, fügen Sie das `identity`-Objekt und die untergeordnete `type`-Eigenschaft in der Ressourcendefinition der Logik-App in der Vorlage hinzu, beispielsweise:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Wenn die Ressourcendefinition der Logik-App in Azure erstellt wird, erhält das `identity`-Objekt diese weiteren Eigenschaften:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschaft (JSON) | Wert | BESCHREIBUNG |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Die GUID (Globally Unique Identifier) des Dienstprinzipalobjekts für die verwaltete Identität, die die Logik-App im Azure AD-Mandanten angibt. Diese GUID wird manchmal als „Objekt-ID“ oder `objectID` angezeigt. |
| `tenantId` | <*Azure-AD-tenant-ID*> | Die GUID (Globally Unique Identifier), die den Azure AD-Mandanten angibt, in dem die Logik-App nun Mitglied ist. Unter dem Azure AD-Mandanten hat der Dienstprinzipal den gleichen Namen wie die Logik-App-Instanz. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Aktivieren einer vom Benutzer zugewiesenen Identität

Zum Einrichten einer benutzerseitig zugewiesenen verwalteten Identität für Ihre Logik-App müssen Sie diese Identität zunächst als separate eigenständige Azure-Ressource erstellen. Sie können die folgenden Optionen verwenden:

* [Azure portal](#azure-portal-user-identity)
* [ARM-Vorlage](#template-user-identity)
* Azure PowerShell
  * [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Hinzufügen der Rollenzuweisung](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Hinzufügen der Rollenzuweisung](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure-REST-API
  * [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Hinzufügen der Rollenzuweisung](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Erstellen einer benutzerseitig zugewiesenen Identität im Azure-Portal

1. Geben Sie im [Azure-Portal](https://portal.azure.com) im Suchfeld auf einer beliebigen Seite `managed identities` ein, und wählen Sie **Verwaltete Identitäten** aus.

   ![Screenshot des Portals mit ausgewählter Option „Verwaltete Identitäten“](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Wählen Sie unter **Verwaltete Identitäten** die Option **Hinzufügen** aus.

   ![Hinzufügen einer neuen verwalteten Identität](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Geben Sie Informationen zu Ihrer verwalteten Identität an, und wählen Sie dann **Überprüfen und erstellen** aus, beispielsweise:

   ![Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Der Name des zu verwendenden Azure-Abonnements |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Der Name der zu verwendenden Ressourcengruppe. Erstellen Sie eine neue Gruppe, oder wählen Sie eine vorhandene Gruppe aus. Dieses Beispiel erstellt eine neue Gruppe namens `fabrikam-managed-identities-RG`. |
   | **Region** | Ja | <*Azure-Region*> | Die Azure-Region, in der die Informationen zu Ihrer Ressource gespeichert werden sollen. In diesem Beispiel wird „USA, Westen“ verwendet. |
   | **Name** | Ja | <*user-assigned-identity-name*> | Der Name für die benutzerseitig zugewiesene Identität. In diesem Beispiel wird `Fabrikam-user-assigned-identity` verwendet. |
   |||||

   Nach Überprüfung dieser Informationen erstellt Azure Ihre verwaltete Identität. Nun können Sie Ihrer Logik-App die vom Benutzer zugewiesene Identität hinzufügen. Sie können Ihrer Logik-App maximal eine vom Benutzer zugewiesene Identität hinzufügen.

1. Öffnen Sie im Azure-Portal die Logik-App in der Designeransicht.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** und dann **Vom Benutzer zugewiesen** > **Hinzufügen** aus.

   ![Hinzufügen von benutzerseitig zugewiesenen verwalteten Identitäten](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Wählen Sie im Bereich **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** in der Liste **Abonnement** Ihr Azure-Abonnement aus, sofern es nicht bereits markiert ist. Wählen Sie in der Liste mit *allen* verwalteten Identitäten in diesem Abonnement die gewünschte benutzerseitig zugewiesene Identität aus. Um die Liste zu filtern, geben Sie im Suchfeld **Benutzerseitig zugewiesene verwaltete Identitäten** den Namen der Identität oder Ressourcengruppe ein. Wenn Sie fertig sind, wählen Sie **Hinzufügen** aus.

   ![Auswählen der zu verwendenden benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Wenn Sie eine Fehlermeldung erhalten, dass Sie nur eine einzige verwaltete Identität verwenden können, ist Ihrer Logik-App bereits die systemseitig zugewiesene Identität zugeordnet. Bevor Sie die benutzerseitig zugewiesene Identität hinzufügen können, müssen Sie zuerst die systemseitig zugewiesene Identität in Ihrer Logik-App deaktivieren.

   Ihre Logik-App ist jetzt der benutzerseitig zugewiesenen verwalteten Identität zugeordnet.

   ![Zuordnen einer benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Führen Sie nun die [Schritte für das Gewähren des Zugriffs der Identität auf die Ressource](#access-other-resources) weiter unten in diesem Thema aus.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-arm-template"></a>Erstellen einer benutzerseitig zugewiesenen Identität in einer ARM-Vorlage

Sie können eine [ARM-Vorlage](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) verwenden, die [benutzerseitig zugewiesene Identitäten für die Authentifizierung](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) unterstützt, um das Erstellen und Bereitstellen von Azure-Ressourcen wie Logik-Apps zu automatisieren. Im Abschnitt `resources` Ihrer Vorlage sind in der Ressourcendefinition Ihrer Logik-App die folgenden Elemente erforderlich:

* Ein `identity`-Objekt, bei dem die `type`-Eigenschaft auf `UserAssigned` festgelegt ist

* Ein untergeordnetes `userAssignedIdentities`-Objekt, das die vom Benutzer zugewiesene Ressource und den Namen angibt

Dieses Beispiel zeigt die Ressourcendefinition einer Logik-App für eine HTTP PUT-Anforderung inklusive eines nicht parametrisierten `identity`-Objekts. Die Antworten auf die PUT-Anforderung und den nachfolgenden GET-Vorgang enthalten ebenfalls dieses `identity`-Objekt:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Wenn die Vorlage auch die Ressourcendefinition der verwalteten Identität enthält, können Sie das `identity`-Objekt parametrisieren. Dieses Beispiel zeigt, wie das untergeordnete `userAssignedIdentities`-Objekt auf die Variable `userAssignedIdentity` verweist, die Sie im Abschnitt `variables` Ihrer Vorlage definieren. Diese Variable verweist auf die Ressourcen-ID für Ihre benutzerseitig zugewiesene Identität.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Zuweisen des Zugriffs der Identität auf Ressourcen

Bevor Sie die verwaltete Identität Ihrer Logik-App für die Authentifizierung verwenden können, müssen Sie auf der Azure-Ressource, auf der Sie die Identität verwenden möchten, den Zugriff für Ihre Identität mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) einrichten.

Für diese Aufgabe muss der Identität in der Azure-Zielressource mithilfe einer der folgenden Optionen die entsprechende Rolle zugewiesen werden:

* [Azure-Portal](#azure-portal-assign-access)
* [ARM-Vorlage](../role-based-access-control/role-assignments-template.md)
* [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
* [Azure-REST-API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-managed-identity-role-based-access-in-the-azure-portal"></a>Zuweisen des rollenbasierten Zugriffs für verwaltete Identitäten im Azure-Portal

Auf der Azure-Ressource, auf der Sie die verwaltete Identität verwenden möchten, müssen Sie die Identität einer Rolle zuweisen, die auf die Zielressource zugreifen kann. Weitere allgemeine Informationen zu dieser Aufgabe finden Sie unter [Zuweisen des Zugriffs einer verwalteten Identität auf eine andere Ressource mithilfe von Azure RBAC](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Ressource, auf der Sie die Identität verwenden möchten.

1. Wählen Sie im Menü der Ressource die Optionen **Zugriffssteuerung (IAM)**  > **Hinzufügen** > **Rollenzuweisung hinzufügen** aus.

   > [!NOTE]
   > Wenn die Option **Rollenzuweisung hinzufügen** deaktiviert ist, sind Sie nicht zum Zuweisen von Rollen berechtigt. Weitere Informationen finden Sie unter [Integrierte Azure AD-Rollen](../active-directory/roles/permissions-reference.md).

1. Weisen Sie nun Ihrer verwalteten Identität die erforderliche Rolle zu. Weisen Sie auf der Registerkarte **Rolle** eine Rolle zu, die Ihrer Identität den erforderlichen Zugriff auf die aktuelle Ressource gewährt.

   Weisen Sie in diesem Beispiel die Rolle mit dem Namen **Mitwirkender an Storage-Blobdaten** zu, die Schreibzugriff für Blobs in einem Azure Storage-Container umfasst. Weitere Informationen zu bestimmten Rollen für Speichercontainer finden Sie unter [Rollen, die auf Blobs in einem Azure Storage-Container zugreifen können](../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights).

1. Wählen Sie als Nächstes die verwaltete Identität aus, der Sie die Rolle zuweisen möchten. Wählen Sie unter **Zugriff zuweisen zu** die Optionen **Verwaltete Identität** > **Mitglieder hinzufügen** aus.

1. Wählen Sie basierend auf dem Typ Ihrer verwalteten Identität die folgenden Werte aus, oder geben Sie sie an:

   | type | Azure-Dienstinstanz | Subscription | Member |
   |------|------------------------|--------------|--------|
   | **Systemseitig zugewiesen** | **Logik-App** | <*Name des Azure-Abonnements*> | <*Name Ihrer Logik-App*> |
   | **Benutzerseitig zugewiesen** | Nicht zutreffend | <*Name des Azure-Abonnements*> | <*Name Ihrer benutzerseitig zugewiesenen Identität*> |
   |||||

   Weitere Informationen zum Zuweisen von Rollen finden Sie in der Dokumentation [Zuweisen von Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

1. Nachdem Sie den Zugriff für die Identität eingerichtet haben, können Sie die Identität verwenden, um den [Zugriff für Trigger und Aktionen zu authentifizieren, die verwaltete Identitäten unterstützen](#authenticate-access-with-identity).

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Authentifizieren des Zugriffs mit der verwalteten Identität

Nachdem Sie [die verwaltete Identität für die Logik-App aktiviert](#azure-portal-system-logic-app) und [dieser Identität Zugriff auf die Zielressource oder Zielentität gewährt](#access-other-resources) haben, können Sie diese Identität in [Triggern und Aktionen verwenden, die verwaltete Identitäten unterstützen](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Für eine Azure-Funktion, für die die systemseitig zugewiesene Identität verwendet werden soll, müssen Sie zunächst [die Authentifizierung für Azure-Funktionen aktivieren](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

Die folgenden Schritte veranschaulichen, wie Sie die verwaltete Identität über das Azure-Portal mit einem Trigger oder einer Aktion verwenden. Informationen zum Angeben der verwalteten Identität in der zugrunde liegenden JSON-Definition eines Triggers oder einer Aktion finden Sie unter [Authentifizierung der verwalteten Identität](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Logik-App in der Designeransicht.

1. Wenn dies noch nicht erfolgt ist, fügen Sie [den Trigger oder die Aktion hinzu, der bzw. die verwaltete Identitäten unterstützt](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Sie können nicht für alle Trigger und Aktionen einen Authentifizierungstyp hinzufügen. Weitere Informationen finden Sie unter [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Führen Sie für den hinzugefügten Trigger oder die hinzugefügte Aktion die folgenden Schritte aus:

   * **Integrierte Trigger und Aktionen, die die Verwendung einer verwalteten Identität unterstützen**

     1. Fügen Sie die Eigenschaft **Authentifizierung** hinzu, falls diese Eigenschaft noch nicht angezeigt wird.

     1. Wählen Sie unter **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

     Weitere Informationen finden Sie unter [Example: Authentifizieren Sie den integrierten Trigger oder die integrierte Aktion über eine verwaltete Identität](#authenticate-built-in-managed-identity).
 
   * **Trigger und Aktionen verwalteter Connectors, die die Verwendung einer verwalteten Identität unterstützen**

     1. Wählen Sie auf der Seite zur Mandantenauswahl die Option **Verbindung mit verwalteter Identität herstellen** aus.

     1. Geben Sie auf der nächsten Seite einen Verbindungsnamen an.

        Standardmäßig wird in der Liste verwalteter Identitäten nur die derzeit aktivierte verwaltete Identität angezeigt, weil eine Logik-App nur die Aktivierung jeweils einer verwalteten Identität unterstützt. Beispiel:

        ![Screenshot mit der Seite „Verbindungsname“ und der ausgewählten verwalteten Identität.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     Weitere Informationen finden Sie unter [Example: Authentifizieren eines Triggers oder einer Aktion eines verwalteter Connectors über eine verwaltete Identität](#authenticate-managed-connector-managed-identity).

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Beispiel: Authentifizieren eines vordefinierten Triggers oder einer Aktion über eine verwaltete Identität

Die für die Logik-App aktivierte systemseitig zugewiesene Identität kann von dem HTTP-Trigger oder der HTTP-Aktion verwendet werden. Im Allgemeinen werden im HTTP-Trigger oder der HTTP-Aktion die folgenden Eigenschaften verwendet, um die Ressource oder Entität anzugeben, auf die Sie zugreifen möchten:

| Eigenschaft | Erforderlich | BESCHREIBUNG |
|----------|----------|-------------|
| **Methode** | Ja | Die HTTP-Methode, die in dem Vorgang verwendet wird, den Sie ausführen möchten. |
| **URI** | Ja | Die Endpunkt-URL für den Zugriff auf die Azure-Zielressource oder Azure-Zielentität. Die URI-Syntax enthält normalerweise die [Ressourcen-ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) für die Azure-Ressource oder den Azure-Dienst. |
| **Headers** | Nein | Alle Headerwerte, die Sie in der ausgehenden Anforderung einfügen müssen oder möchten, z. B. den Inhaltstyp |
| **Abfragen** | Nein | Alle Abfrageparameter, die Sie in der Anforderung einfügen müssen oder möchten, z. B. den Parameter für einen bestimmten Vorgang oder die API-Version für den auszuführenden Vorgang |
| **Authentifizierung** | Ja | Der Authentifizierungstyp, der zum Authentifizieren des Zugriffs auf die Zielressource oder Zielentität verwendet wird. |
||||

Als konkretes Beispiel wird angenommen, dass Sie den [Snapshot Blob-Vorgang](/rest/api/storageservices/snapshot-blob) für ein Blob in dem Azure Storage-Konto ausführen möchten, in dem Sie zuvor den Zugriff für die Identität eingerichtet haben. Jedoch unterstützt der [Azure Blob Storage-Connector](/connectors/azureblob/) diesen Vorgang derzeit nicht. Stattdessen können Sie diesen Vorgang mithilfe der [HTTP-Aktion](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) oder mit einem anderen [REST-API-Vorgang des Blob-Diensts](/rest/api/storageservices/operations-on-blobs) ausführen.

> [!IMPORTANT]
> Um über HTTP-Anforderungen und verwaltete Identitäten auf Azure Storage-Konten hinter Firewalls zuzugreifen, müssen Sie sicherstellen, dass Sie auch das Speicherkonto mit der [Ausnahme einrichten, die den Zugriff durch vertrauenswürdige Microsoft-Dienste](../connectors/connectors-create-api-azureblobstorage.md#access-blob-storage-with-managed-identities) zulässt.

Zum Ausführen des [Snapshot Blob-Vorgangs](/rest/api/storageservices/snapshot-blob) werden in der HTTP-Aktion die folgenden Eigenschaften angegeben:

| Eigenschaft | Erforderlich | Beispielwert | BESCHREIBUNG |
|----------|----------|---------------|-------------|
| **Methode** | Ja | `PUT`| Die im Snapshot Blob-Vorgang verwendete HTTP-Methode |
| **URI** | Ja | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Die Ressourcen-ID für eine Azure Blob Storage-Datei in der globalen (öffentlichen) Azure-Umgebung, in der diese Syntax verwendet wird. |
| **Headers** | Für Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Die Headerwerte `x-ms-blob-type`, `x-ms-version` und `x-ms-date`, die für Azure Storage-Vorgänge erforderlich sind. <p><p>**Wichtig**: In ausgehenden Anforderungen für HTTP-Trigger und HTTP-Aktionen für Azure Storage sind für den Header die `x-ms-version`-Eigenschaft und die API-Version für den auszuführenden Vorgang erforderlich. `x-ms-date` muss das aktuelle Datum sein. Andernfalls tritt bei Ihrer Logik-App ein `403 FORBIDDEN`-Fehler auf. Um das aktuelle Datum im erforderlichen Format abzurufen, können Sie den Ausdruck im Beispielwert verwenden. <p>Weitere Informationen finden Sie in den folgenden Themen: <p><p>- [Anforderungsheader: Snapshot Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Versionsverwaltung für Azure Storage-Dienste](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Abfragen** | Nur für den Vorgang für den Momentaufnahmeblob | `comp` = `snapshot` | Der Name und der Wert des Abfrageparameters für den Vorgang. |
|||||

HTTP-Beispielaktion, in der alle diese Eigenschaftswerte angezeigt werden:

![Hinzufügen einer HTTP-Aktion zum Zugreifen auf eine Azure-Ressource](./media/create-managed-service-identity/http-action-example.png)

1. Nachdem Sie die HTTP-Aktion hinzugefügt haben, fügen Sie ihr die Eigenschaft **Authentifizierung** hinzu. Wählen Sie in der Liste **Neuen Parameter hinzufügen** die Option **Authentifizierung** aus.

   ![Hinzufügen der Authentication-Eigenschaft zur HTTP-Aktion](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Sie können nicht für alle Trigger und Aktionen einen Authentifizierungstyp hinzufügen. Weitere Informationen finden Sie unter [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Wählen Sie in der Liste **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

   ![Auswählen von „Verwaltete Identität“ für die Authentifizierung](./media/create-managed-service-identity/select-managed-identity.png)

1. Wählen Sie in der Liste der verwalteten Identitäten einen Eintrag für Ihr Szenario aus.

   * Wenn Sie die vom System zugewiesene Identität eingerichtet haben, wählen Sie **Systemseitig zugewiesene verwaltete Identität** aus, sofern sie nicht bereits ausgewählt ist.

     ![Auswählen von „Systemseitig zugewiesene verwaltete Identität“](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Wenn Sie eine benutzerseitig zugewiesene Identität einrichten, wählen Sie diese Identität aus, sofern sie nicht bereits ausgewählt ist.

     ![Auswählen der benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   In diesem Beispiel wird weiterhin die **Systemseitig zugewiesene verwaltete Identität** verwendet.

1. Bei einigen Triggern und Aktionen wird die **Audience**-Eigenschaft auch angezeigt, damit Sie die Zielressourcen-ID festlegen können. Legen Sie den Wert der **Audience**-Eigenschaft auf die [Ressourcen-ID für die Zielressource oder den Zieldienst](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) fest. Andernfalls wird für die **Audience**-Eigenschaft standardmäßig die Ressourcen-ID `https://management.azure.com/` verwendet, bei der es sich um die Ressourcen-ID für Azure Resource Manager handelt.
  
    Wenn Sie beispielsweise den Zugriff auf eine [Key Vault-Ressource in der globalen Azure-Cloud](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault) authentifizieren möchten, müssen Sie die **Audience**-Eigenschaft auf *exakt* die folgende Ressourcen-ID festlegen: `https://vault.azure.net`. Beachten Sie, dass diese spezifische Ressourcen-ID *keine* nachgestellten Schrägstriche aufweist. Tatsächlich kann das Einschließen eines nachgestellten Schrägstrichs entweder einen Fehler vom Typ `400 Bad Request` oder `401 Unauthorized` bewirken.

   > [!IMPORTANT]
   > Vergewissern Sie sich, dass die Zielressourcen-ID *genau dem Wert entspricht*, der in Azure Active Directory (AD) erwartet wird, einschließlich aller erforderlichen nachgestellten Schrägstriche. Die Ressourcen-ID für alle Azure Blob Storage-Konten erfordert z. B. einen nachgestellten Schrägstrich. Allerdings erfordert die Ressourcen-ID für ein bestimmtes Speicherkonto keinen nachgestellten Schrägstrich. [Hier](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) finden Sie die Ressourcen-IDs für die Azure-Dienste, die die Azure AD-Authentifizierung unterstützen.

   In diesem Beispiel wird die **Audience**-Eigenschaft auf `https://storage.azure.com/` festgelegt, sodass die für die Authentifizierung verwendeten Zugriffstoken für alle Speicherkonten gültig sind. Sie können jedoch auch die Stammdienst-URL für ein bestimmtes Speicherkonto angeben, z. B. `https://fabrikamstorageaccount.blob.core.windows.net`.

   ![Festlegen der Zielressourcen-ID in der Audience-Eigenschaft](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Weitere Informationen zum Autorisieren des Zugriffs mit Azure AD für Azure Storage finden Sie in folgenden Themen:

   * [Autorisieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)
   * [Autorisieren des Zugriffs auf Azure Storage mit Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Fahren Sie damit fort, die Logik-App wie gewünscht zu erstellen.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Beispiel: Authentifizieren eines Triggers oder einer Aktion eines verwalteter Connectors über eine verwaltete Identität

Die Azure Resource Manager-Aktion **Ressourcen lesen** kann die verwaltete Identität verwenden, die Sie für Ihre Logik-App aktiviert haben. Dieses Beispiel zeigt Ihnen die Verwendung der systemseitig zugewiesenen verwalteten Identität.

1. Nachdem Sie die Aktion Ihrem Workflow hinzugefügt haben, wählen Sie auf der Seite zur Mandantenauswahl die Option **Verbindung mit verwalteter Identität herstellen** aus.

   ![Screenshot, der die Azure Resource Manager-Aktion und die Auswahl „Verbindung mit verwalteter Identität herstellen“ zeigt.](./media/create-managed-service-identity/select-connect-managed-identity.png)

   Die Aktion zeigt nun die Seite „Verbindungsname“ mit der Liste verwalteter Identitäten. Diese umfasst den Typ der verwalteten Identität, die derzeit in der Logik-App aktiviert ist.

1. Geben Sie auf der Seite „Verbindungsname“ einen Namen für die Verbindung ein. Wählen Sie in der Liste verwalteter Identitäten die verwaltete Identität aus, die in diesem Beispiel als **systemseitig zugewiesene verwaltete Identität** verwendet wird, und klicken Sie auf **Erstellen** . Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität aktiviert haben, wählen Sie stattdessen diese Identität aus.

   ![Screenshot, der die Azure Resource Manager-Aktion mit eingegebenem Verbindungsnamen und Auswahl von „Systemseitig zugewiesene verwaltete Identität“ anzeigt.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Wenn die verwaltete Identität nicht aktiviert ist, wird beim Erstellen der Verbindung der folgende Fehler angezeigt:

   *Sie müssen die verwaltete Identität für Ihre Logik-App aktivieren und dann den erforderlichen Zugriff auf die Identität in der Zielressource gewähren.*

   ![Screenshot, der die Azure Resource Manager-Aktion mit Fehler zeigt, wenn keine verwaltete Identität aktiviert ist.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. Nachdem die Verbindung erfolgreich erstellt wurde, kann der Designer dynamische Werte, Inhalte oder Schemas durch Authentifizierung über die verwaltete Identität abrufen.

1. Fahren Sie damit fort, die Logik-App wie gewünscht zu erstellen.

<a name="logic-app-resource-definition-connection-managed-identity"></a>

### <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity"></a>Ressourcendefinition und Verbindungen von Logik-Apps, die eine verwaltete Identität verwenden

Eine Verbindung, die eine verwaltete Identität aktiviert und verwendet, entspricht einem speziellen Verbindungstyp, der nur mit einer verwalteten Identität funktioniert. Zur Laufzeit verwendet die Verbindung die verwaltete Identität, die für die Logik-App aktiviert wurde. Diese Konfiguration wird im `parameters`-Objekt der Logik-App-Ressourcendefinition gespeichert. Dieses umfasst das `$connections`-Objekt, das bei Aktivierung der die benutzerseitig zugewiesenen Identität Verweise auf die Ressourcen-ID der Verbindung sowie die Ressourcen-ID der Identität enthält.

Dieses Beispiel zeigt die Konfiguration bei Aktivierung der systemseitig zugewiesenen verwalteten Identität in der Logik-App:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Dieses Beispiel zeigt die Konfiguration bei Aktivierung einer benutzerseitig zugewiesenen verwalteten Identität in der Logik-App:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Während der Laufzeit prüft der Logic Apps-Dienst, ob ein Trigger des verwalteten Connectors und Aktionen in der Logik-App für die Verwendung der verwalteten Identität eingerichtet sind. Außerdem stellt er sicher, dass alle erforderlichen Berechtigungen eingerichtet sind, um über die verwaltete Identität auf die Zielressourcen zuzugreifen, die durch den Trigger und die Aktionen angegeben werden. Bei erfolgreicher Ausführung ruft der Logic Apps-Dienst das Azure AD-Token ab, das der verwalteten Identität zugeordnet ist, authentifiziert anhand dieser Identität den Zugriff auf die Zielressource und führt den konfigurierten Vorgang in Trigger und Aktionen aus.

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities"></a>ARM-Vorlage für verwaltete Verbindungen und verwaltete Identitäten

Wenn Sie die Bereitstellung mit einer ARM-Vorlage automatisieren und Ihre Logik-App einen Trigger oder eine Aktion für einen verwalteten Connector enthält, der eine verwaltete Identität verwendet, vergewissern Sie sich, dass die zugrunde liegende Verbindungsressourcendefinition die `parameterValueType`-Eigenschaft mit `Alternative` als Eigenschaftswert enthält. Andernfalls richtet Ihre ARM-Bereitstellung die Verbindung nicht so ein, dass die verwaltete Identität für die Authentifizierung verwendet wird, und die Verbindung funktioniert im Workflow Ihrer Logik-App nicht. Diese Anforderung gilt nur für [bestimmte Trigger und Aktionen des verwalteten Connectors](#managed-connectors-managed-identity), bei denen Sie die Option [**Verbindung mit verwalteter Identität herstellen**](#authenticate-managed-connector-managed-identity) ausgewählt haben.

Hier ist beispielsweise die zugrunde liegende Verbindungsressourcendefinition für eine Azure Automation-Aktion, die eine verwaltete Identität verwendet, wobei die Definition die `parameterValueType`-Eigenschaft enthält, deren Eigenschaftswert auf `Alternative` festgelegt ist:

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Deaktivieren von verwalteten Identitäten

Wenn Sie die verwaltete Identität für die Logik-App nicht mehr verwenden möchten, haben Sie folgende Möglichkeiten:

* [Azure portal](#azure-portal-disable)
* [ARM-Vorlage](#template-disable)
* Azure PowerShell
  * [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-powershell.md)
  * [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-cli.md)
  * [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure-REST-API
  * [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-rest.md)
  * [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Wenn Sie die Logik-App löschen, wird in Azure die verwaltete Identität automatisch aus Azure AD entfernt.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Deaktivieren der verwalteten Identität im Azure-Portal

Heben Sie im Azure-Portal zunächst den Zugriff der Identität auf die [Zielressource](#disable-identity-target-resource) auf. Deaktivieren Sie als Nächstes die vom System zugewiesene Identität, oder entfernen Sie die vom Benutzer zugewiesene Identität aus Ihrer [Logik-App](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Entfernen des Zugriffs der Identität auf Ressourcen

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Azure-Zielressource, für die Sie den Zugriff für die verwaltete Identität aufheben möchten.

1. Wählen Sie im Menü der Zielressource die Option **Zugriffssteuerung (IAM)** aus. Wählen Sie auf der Symbolleiste die Option **Rollenzuweisungen** aus.

1. Wählen Sie in der Liste der Rollen die verwalteten Identitäten aus, die Sie entfernen möchten. Wählen Sie auf der Symbolleiste die Option **Entfernen** aus.

   > [!TIP]
   > Wenn die Option **Entfernen** deaktiviert ist, verfügen Sie wahrscheinlich nicht über die erforderlichen Berechtigungen. Weitere Informationen zu den Berechtigungen, mit denen Sie Rollen für Ressourcen verwalten können, finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../active-directory/roles/permissions-reference.md).

Die verwaltete Identität ist damit entfernt und hat keinen Zugriff mehr auf die Zielressource.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Deaktivieren einer verwalteten Identität für die Logik-App

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Logik-App in der Designeransicht.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus, und befolgen Sie dann die Schritte für Ihre Identität:

   * Wählen Sie **Vom System zugewiesen** > **Ein** > **Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

     ![Deaktivieren der systemseitig zugewiesenen Identität](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Wählen Sie **Vom Benutzer zugewiesen**, die verwaltete Identität und dann **Entfernen** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

     ![Entfernen der benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Die verwaltete Identität ist nun in Ihrer Logik-App deaktiviert.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-an-arm-template"></a>Deaktivieren einer verwalteten Identität in einer ARM-Vorlage

Wenn Sie die verwaltete Identität der Logik-App mithilfe einer ARM-Vorlage erstellt haben, legen Sie die untergeordnete `type`-Eigenschaft des `identity`-Objekts auf `None` fest.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Schützen des Zugriffs und der Daten in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)