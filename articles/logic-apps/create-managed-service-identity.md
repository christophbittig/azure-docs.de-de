---
title: Authentifizieren von Verbindungen mit verwalteten Identitäten
description: Verwenden Sie eine verwaltete Identität, um Workflowverbindungen mit durch Azure AD geschützten Ressourcen ohne Anmeldeinformationen oder Geheimnisse in Azure Logic-Apps zu authentifizieren.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell, subject-rbac-steps, ignite-fall-2021
ms.openlocfilehash: c6e814f0b0b36408210cac7657c947f16a0076c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085158"
---
# <a name="authenticate-access-to-azure-resources-with-managed-identities-in-azure-logic-apps"></a>Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps

Einige Trigger und Aktionen in Logik-App-Workflows unterstützen die Verwendung einer [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) (früher als *verwaltete Dienstidentität (Managed Service Identity, MSI)* bezeichnet) für die Authentifizierung beim Herstellen von Verbindungen mit Ressourcen, die durch Azure Active Directory (Azure AD) geschützt sind. Wenn für Ihre Logik-App-Ressource eine verwaltete Identität aktiviert ist, müssen Sie keine Anmeldeinformationen, Geheimnisse oder Azure AD-Token bereitstellen. Azure verwaltet diese Identität und hilft, die Sicherheit von Authentifizierungsinformationen zu bewahren, da Sie keine dieser sensiblen Daten verwalten müssen.

Azure Logic Apps unterstützt die [*systemseitig zugewiesene* verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md), die Sie nur mit einer Logik-App-Ressource verwenden können, und die [*benutzerseitig zugewiesene* verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md), die Sie für eine Gruppe von Logik-App-Ressourcen freigeben können, je nachdem, wo Ihre Logik-App-Workflows ausgeführt werden.

| Logik-App-Ressourcentyp | Environment | BESCHREIBUNG |
|-------------------------|-------------|-------------|
| Nutzung | - Azure Logic Apps mit mehreren Mandanten <p><p>- Integrationsdienstumgebung (Integration Service Environment, ISE) | Sie können *entweder* die systemseitig zugewiesene Identität oder eine *einzelne* benutzerseitig zugewiesene Identität auf Ressourcen- und Verbindungsebene der Logik-App aktivieren und verwenden. |
| Standard | - Azure Logic Apps-Instanz mit nur einem Mandanten <p><p>- App Service-Umgebung v3 (ASEv3) <p><p>- Logik-Apps mit Azure Arc-Unterstützung | Sie können derzeit *nur* die systemseitig zugewiesene Identität verwenden, die automatisch aktiviert wird. Die vom Benutzer zugewiesene Identität ist derzeit nicht verfügbar. |
|||

Informationen zu Grenzwerten für verwaltete Identitäten in Azure Logic Apps finden Sie unter [Grenzwerte für verwaltete Identitäten für Logik-Apps](logic-apps-limits-and-config.md#managed-identity). Weitere Informationen zu den Ressourcentypen und Umgebungen der Logik-App „Verbrauch“ und „Standard“ finden Sie in der folgenden Dokumentation:

* [Übersicht: Was ist Azure Logic Apps?](logic-apps-overview.md#resource-environment-differences)
* [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung](single-tenant-overview-compare.md)
* [Azure Arc-fähige Logik-Apps](azure-arc-enabled-logic-apps-overview.md)

<a name="triggers-actions-managed-identity"></a>
<a name="managed-connectors-managed-identity"></a>

## <a name="where-you-can-use-a-managed-identity"></a>Verwendung einer verwalteten Identität

Nur bestimmte integrierte und verwaltete Connectorvorgänge, die Azure AD Open Authentication (Azure AD OAuth) unterstützen, können eine verwaltete Identität für die Authentifizierung verwenden. Die folgende Tabelle enthält nur eine *Beispielauswahl*. Eine ausführlichere Liste der [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen,](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) und [Azure-Dienste, die Azure AD Authentifizierung mit verwalteten Identitäten unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

In der folgenden Tabelle sind die Vorgänge aufgeführt, bei denen Sie entweder die systemseitig zugewiesene verwaltete Identität oder die benutzerseitig zugewiesene verwaltete Identität im Ressourcentyp **Logik-App (Verbrauch)** verwenden können:

| Vorgangsart | Unterstützte Vorgänge |
|----------------|----------------------|
| Integriert | – Azure API Management <br>– Azure App Services <br>– Azure Functions <br>– HTTP <br>– HTTP + Webhook <p>**Hinweis**: HTTP-Vorgänge können Verbindungen mit Azure Storage Konten hinter Azure-Firewalls mit der systemseitig zugewiesenen Identität authentifizieren. Sie unterstützen jedoch nicht die vom Benutzer zugewiesene verwaltete Identität für die Authentifizierung derselben Verbindungen. |
| Verwalteter Connector (**Vorschau**) | Einmalige Authentifizierung: <br>– Azure Automation <br>– Azure Event Grid <br>– Azure Key Vault <br>– Azure Resource Manager <br>– HTTP mit Azure AD <p>Mehrfachauthentifizierung: <br> : Azure Blob Storage <br>- SQL Server |
|||

### <a name="standard"></a>[Standard](#tab/standard)

In der folgenden Tabelle sind die Vorgänge aufgeführt, bei denen Sie die systemseitig zugewiesene verwaltete Identität im Ressourcentyp **Logik-App (Standard)** verwenden können:

| Vorgangsart | Unterstützte Vorgänge |
|----------------|----------------------|
| Integriert | – HTTP <br>– HTTP + Webhook <p>**Hinweis**: HTTP-Vorgänge können Verbindungen mit Azure Storage Konten hinter Azure-Firewalls mit der systemseitig zugewiesenen Identität authentifizieren. |
| Verwalteter Connector (**Vorschau**) | Einmalige Authentifizierung: <br>– Azure Automation <br>– Azure Event Grid <br>– Azure Key Vault <br>– Azure Resource Manager <br>– HTTP mit Azure AD <p>Mehrfachauthentifizierung: <br> : Azure Blob Storage <br>- SQL Server |
|||

---

In diesem Artikel erfahren Sie, wie Sie die systemseitig zugewiesene Identität oder die benutzerseitig zugewiesene Identität basierend darauf aktivieren und einrichten, ob Sie den Ressourcentyp **Logik-App (Verbrauch)** oder **Logik-App (Standard)** verwenden. Im Gegensatz zur systemseitig zugewiesenen Identität, die Sie nicht manuell erstellen müssen, *müssen* Sie die benutzerseitig zugewiesene Identität für den **Ressourcentyp Logik-App (Verbrauch)** manuell erstellen. Dieser Artikel enthält die Schritte zum Erstellen der vom Benutzer zugewiesenen Identität mithilfe der Azure-Portal- und Azure Resource Manager-Vorlage (ARM-Vorlage). Informationen zu Azure PowerShell, Azure CLI und der Azure-REST-API finden Sie in der folgenden Dokumentation:

| Tool | Dokumentation |
|------|---------------|
| Azure PowerShell | [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure-Befehlszeilenschnittstelle | [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| Azure-REST-API | [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). Für die verwaltete Identität sowie die Azure-Zielressource, auf die Sie zugreifen möchten, muss dasselbe Azure-Abonnement verwendet werden.

* Damit eine verwaltete Identität Zugriff auf eine Azure-Ressource erhält, müssen Sie der Zielressource eine Rolle für diese Identität hinzufügen. Für das Hinzufügen von Rollen benötigen Sie [Azure AD-Administratorberechtigungen](../active-directory/roles/permissions-reference.md), mit denen Rollen verwalteten Identitäten im zugehörigen Azure AD-Mandanten zugewiesen werden können.

* Die Azure-Zielressource, auf die Sie zugreifen möchten. Für diese Ressource fügen Sie eine Rolle für die verwaltete Identität hinzu, die der Ressource oder Verbindung der Logik-App hilft, den Zugriff auf die Zielressource zu authentifizieren.

* Die Logik-App-Ressource, in der Sie den [Trigger oder die Aktionen verwenden möchten, die verwaltete Identitäten unterstützen](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

  | Logik-App-Ressourcentyp | Unterstützung verwalteter Identitäten |
  |-------------------------|--------------------------|
  | Verbrauch | Dem System oder dem Benutzer zugewiesenen Identität |
  | Standard | Systemseitig zugewiesene Identität (automatisch aktiviert) |
  |||

<a name="system-assigned-azure-portal"></a>
<a name="azure-portal-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivieren der systemseitig zugewiesenen Identität im Azure-Portal

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie Ihre Logik-App-Ressource im [Azure-Portal](https://portal.azure.com).

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus.

1. Wählen Sie im Bereich **Identität** unter **dem System zugewiesen** die Option **Aktiviert** > **Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

   ![Screenshot, der Azure-Portal mit dem Bereich „Identität“ der Logik-App (Verbrauch) und der Registerkarte „System zugewiesen“ mit den ausgewählten Einstellungen „Aktiviert“ und „Speichern“ zeigt.](./media/create-managed-service-identity/enable-system-assigned-identity-consumption.png)

   > [!NOTE]
   > Wenn Sie eine Fehlermeldung erhalten, dass Sie nur eine einzige verwaltete Identität verwenden können, ist Ihrer Logik-App-Ressource bereits eine benutzerseitig zugewiesene Identität zugeordnet. Bevor Sie die systemseitig zugewiesene Identität hinzufügen können, müssen Sie zuerst die benutzerseitig zugewiesene Identität aus Ihrer Logik-App-Ressource *entfernen*.

   In der Logik-App-Ressource kann nun die systemseitig zugewiesene Identität verwendet werden, die bei Azure AD registriert ist und durch eine Objekt-ID dargestellt wird.

   ![Screenshot: Bereich „Identität“ der Logik-App (Verbrauch) mit der Objekt-ID für die systemseitig zugewiesene Identität](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **Objekt-ID (Prinzipal)** | <*Identität-Ressourcen-ID*> | Eine GUID (Globally Unique Identifier), die die systemseitig zugewiesene Identität für Ihre Logik-App in einem Azure AD-Mandanten angibt. |
   ||||

1. Führen Sie nun die [Schritte für das Gewähren des Zugriffs der Identität auf die Ressource](#access-other-resources) weiter unten in diesem Thema aus.

### <a name="standard"></a>[Standard](#tab/standard)

Auf dem Ressourcentyp **Logik-App (Standard)** wird die systemseitig zugewiesene Identität automatisch aktiviert.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus.

1. Wählen Sie im Bereich **Identität** unter **dem System zugewiesen** die Option **Aktiviert** > **Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

   ![Screenshot, der Azure-Portal mit dem Bereich „Identität“ der Logik-App (Standard) und der Registerkarte „System zugewiesen“ mit den ausgewählten Einstellungen „Aktiviert“ und „Speichern“ zeigt.](./media/create-managed-service-identity/enable-system-assigned-identity-standard.png)

   In der Logik-App-Ressource kann nun die systemseitig zugewiesene Identität verwendet werden, die bei Azure AD registriert ist und durch eine Objekt-ID dargestellt wird.

   ![Screenshot: Bereich „Identität“ der Logik-App (Standard) mit der Objekt-ID für die systemseitig zugewiesene Identität](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **Objekt-ID (Prinzipal)** | <*Identität-Ressourcen-ID*> | Eine GUID (Globally Unique Identifier), die die systemseitig zugewiesene Identität für Ihre Logik-App in einem Azure AD-Mandanten angibt. |
   ||||

1. Führen Sie nun die [Schritte für das Gewähren des Zugriffs der Identität auf die Ressource](#access-other-resources) weiter unten in diesem Thema aus.

---

<a name="system-assigned-template"></a>
<a name="template-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-an-arm-template"></a>Aktivieren der systemseitig zugewiesenen Identität in einer ARM-Vorlage

Sie können eine [ARM-Vorlage](logic-apps-azure-resource-manager-templates-overview.md) verwenden, um das Erstellen und Bereitstellen von Azure-Ressourcen wie Logik-Apps zu automatisieren. Um die systemseitig zugewiesene verwaltete Identität für die Logik-App-Ressource in der Vorlage zu aktivieren, fügen Sie das `identity`-Objekt und die untergeordnete `type`-Eigenschaft in der Ressourcendefinition der Logik-App in der Vorlage hinzu, beispielsweise:

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

### <a name="standard"></a>[Standard](#tab/standard)

```json
{
   "apiVersion": "2021-01-15",
   "type": "Microsoft.Web/sites",
   "name": "[variables('sites_<logic-app-resource-name>_name')]",
   "location": "[resourceGroup().location]",
   "kind": "functionapp,workflowapp",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

---

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

<a name="azure-portal-user-identity"></a>
<a name="user-assigned-azure-portal"></a>

## <a name="create-user-assigned-identity-in-the-azure-portal-consumption-only"></a>Erstellen einer benutzerseitig zugewiesenen Identität im Azure-Portal (nur Verbrauch)

Bevor Sie die vom Benutzer zugewiesene Identität für Ihre **Logik-App-Ressource (Verbrauch)** aktivieren können, müssen Sie diese Identität zunächst als separate Azure-Ressource erstellen.

1. Geben Sie in das Suchfeld des [Azure-Portals](https://portal.azure.com) `managed identities` ein. Klicken Sie auf **Verwaltete Identitäten**.

   ![Screenshot des Azure-Portals mit ausgewählter Option „Verwaltete Identitäten“.](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Wählen Sie im Bereich **Verwaltete Identitäten** **Erstellen** aus.

   ![Screenshot: Bereich „Verwaltete Identitäten“ und „Erstellen“ ausgewählt.](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Geben Sie Informationen zu Ihrer verwalteten Identität an, und wählen Sie dann **Überprüfen und erstellen** aus, beispielsweise:

   ![Screenshot: Bereich „Vom Benutzer zugewiesene verwaltete Identität erstellen“ mit Details zur verwalteten Identität.](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Der Name des zu verwendenden Azure-Abonnements |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Der Name der zu verwendenden Azure-Ressourcengruppe. Erstellen Sie eine neue Gruppe, oder wählen Sie eine vorhandene Gruppe aus. Dieses Beispiel erstellt eine neue Gruppe namens `fabrikam-managed-identities-RG`. |
   | **Region** | Ja | <*Azure-Region*> | Die Azure-Region, in der die Informationen zu Ihrer Ressource gespeichert werden sollen. In diesem Beispiel wird **USA, Westen** verwendet. |
   | **Name** | Ja | <*user-assigned-identity-name*> | Der Name für die benutzerseitig zugewiesene Identität. In diesem Beispiel wird `Fabrikam-user-assigned-identity` verwendet. |
   |||||

   Nach Überprüfung dieser Informationen erstellt Azure Ihre verwaltete Identität. Jetzt können Sie ihrer Logik-App-Ressource die benutzerseitig zugewiesene Identität hinzufügen, die nur über eine benutzerseitig zugewiesene Identität verfügen kann.

1. Öffnen Sie Ihre Logik-App-Ressource im Azure-Portal.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus.

1. Wählen Sie im Bereich **Identität** die Option **benutzerseitig zugewiesen** > **Hinzufügen** aus.

   ![Screenshot: Bereich „Identität“ mit ausgewählter Option „Hinzufügen“.](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Führen Sie im Bereich **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** die folgenden Schritte aus:

   1. Wählen Sie in der Liste **Abonnement** Ihr Azure-Abonnement aus, sofern noch nicht ausgewählt.

   1. Wählen Sie in der Liste mit *allen* verwalteten Identitäten in diesem Abonnement die gewünschte benutzerseitig zugewiesene Identität aus. Um die Liste zu filtern, geben Sie im Suchfeld **Benutzerseitig zugewiesene verwaltete Identitäten** den Namen der Identität oder Ressourcengruppe ein.

      ![Screenshot, der die benutzerseitig zugewiesene Identität zeigt.](./media/create-managed-service-identity/select-user-assigned-identity.png)

   1. Wenn Sie fertig sind, wählen Sie **Hinzufügen** aus.

      > [!NOTE]
      > Wenn Sie eine Fehlermeldung erhalten, dass Sie nur eine einzige verwaltete Identität verwenden können, ist Ihrer Logik-App bereits die systemseitig zugewiesene Identität zugeordnet. Bevor Sie die benutzerseitig zugewiesene Identität hinzufügen können, müssen Sie zuerst die systemseitig zugewiesene Identität deaktivieren.

   Ihre Logik-App ist jetzt der benutzerseitig zugewiesenen verwalteten Identität zugeordnet.

   ![Screenshot: Zuordnung zwischen einer benutzerseitig zugewiesenen Identität und einer Logik-App-Ressource.](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Führen Sie nun die [Schritte für das Gewähren des Zugriffs der Identität auf die Ressource](#access-other-resources) weiter unten in diesem Thema aus.

<a name="template-user-identity"></a>

## <a name="create-user-assigned-identity-in-an-arm-template-consumption-only"></a>Erstellen einer benutzerseitig zugewiesenen Identität in einer ARM-Vorlage (nur Verbrauch)

Sie können eine [ARM-Vorlage](logic-apps-azure-resource-manager-templates-overview.md) verwenden, die [benutzerseitig zugewiesene Identitäten für die Authentifizierung](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) unterstützt, um das Erstellen und Bereitstellen von Azure-Ressourcen wie Logik-Apps zu automatisieren. Im Abschnitt `resources` Ihrer Vorlage sind in der Ressourcendefinition Ihrer Logik-App die folgenden Elemente erforderlich:

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

Bevor Sie die verwaltete Identität Ihrer Logik-App für die Authentifizierung verwenden können, müssen Sie auf der Azure-Ressource, auf der Sie die Identität verwenden möchten, den Zugriff für Ihre Identität mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) einrichten. In den Schritten in diesem Abschnitt wird beschrieben, wie Sie dieser Identität die entsprechende Rolle für die Azure-Ressource mithilfe der [Azure-Portal-](#azure-portal-assign-access) und [Azure Resource Manager-Vorlage (ARM-Vorlage)](../role-based-access-control/role-assignments-template.md) zuweisen. Informationen zu Azure PowerShell, Azure CLI und der Azure-REST-API finden Sie in der folgenden Dokumentation:

| Tool | Dokumentation |
|------|---------------|
| Azure PowerShell | [Hinzufügen der Rollenzuweisung](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) |
| Azure CLI | [Hinzufügen der Rollenzuweisung](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md) |
| Azure-REST-API | [Hinzufügen der Rollenzuweisung](../role-based-access-control/role-assignments-rest.md) |
|||

<a name="azure-portal-assign-access"></a>

### <a name="assign-managed-identity-role-based-access-in-the-azure-portal"></a>Zuweisen des rollenbasierten Zugriffs für verwaltete Identitäten im Azure-Portal

Auf der Azure-Ressource, auf der Sie die verwaltete Identität zur Authentifizierung verwenden möchten, müssen Sie die Identität einer Rolle zuweisen, die auf die Zielressource zugreifen kann. Weitere allgemeine Informationen zu dieser Aufgabe finden Sie unter [Zuweisen des Zugriffs einer verwalteten Identität auf eine andere Ressource mithilfe von Azure RBAC](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

> [!NOTE]
> Wenn eine verwaltete Identität Zugriff auf eine Azure-Ressource im gleichen Abonnement hat, kann die Identität nur auf diese Ressource zugreifen. Bei einigen Triggern und Aktionen, die verwaltete Identitäten unterstützen, müssen Sie jedoch zuerst die Azure-Ressourcengruppe auswählen, die die Zielressource enthält. Wenn die Identität keinen Zugriff auf Ressourcengruppenebene hat, werden keine Ressourcen in dieser Gruppe aufgelistet, obwohl sie Zugriff auf die Zielressource haben.
>
> Um dieses Verhalten zu behandeln, müssen Sie der Identität auch Zugriff auf die Ressourcengruppe gewähren, nicht nur auf die Ressource. Wenn Sie Ihr Abonnement auswählen müssen, bevor Sie die Zielressource auswählen können, müssen Sie der Identität Zugriff auf das Abonnement gewähren.

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
   | **Benutzerseitig zugewiesen** (nur Verbrauch) | Nicht zutreffend | <*Name des Azure-Abonnements*> | <*Name Ihrer benutzerseitig zugewiesenen Identität*> |
   |||||

   Weitere Informationen zum Zuweisen von Rollen finden Sie in der Dokumentation [Zuweisen von Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

1. Nachdem Sie den Zugriff für die Identität eingerichtet haben, können Sie die Identität verwenden, um den [Zugriff für Trigger und Aktionen zu authentifizieren, die verwaltete Identitäten unterstützen](#authenticate-access-with-identity).

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Authentifizieren des Zugriffs mit der verwalteten Identität

Nachdem Sie [die verwaltete Identität für die Logik-App-Ressource aktiviert](#azure-portal-system-logic-app) und [dieser Identität Zugriff auf die Zielressource oder Zielentität gewährt](#access-other-resources) haben, können Sie diese Identität in [Triggern und Aktionen verwenden, die verwaltete Identitäten unterstützen](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Für eine Azure-Funktion, für die die systemseitig zugewiesene Identität verwendet werden soll, müssen Sie zunächst [die Authentifizierung für Azure-Funktionen aktivieren](logic-apps-azure-functions.md#enable-authentication-for-functions).

Die folgenden Schritte veranschaulichen, wie Sie die verwaltete Identität über das Azure-Portal mit einem Trigger oder einer Aktion verwenden. Informationen zum Angeben der verwalteten Identität in der zugrunde liegenden JSON-Definition eines Triggers oder einer Aktion finden Sie unter [Authentifizierung der verwalteten Identität](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie Ihre Logik-App-Ressource im [Azure-Portal](https://portal.azure.com).

1. Wenn dies noch nicht erfolgt ist, fügen Sie [den Trigger oder die Aktion hinzu, der bzw. die verwaltete Identitäten unterstützt](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Sie können nicht für alle Trigger und Aktionen einen Authentifizierungstyp hinzufügen. Weitere Informationen finden Sie unter [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Führen Sie für den hinzugefügten Trigger oder die hinzugefügte Aktion die folgenden Schritte aus:

   * **Eingebaute Vorgänge, die die Authentifizierung der verwalteten Identität unterstützen**

     1. Fügen Sie in der Liste **Neuen Parameter hinzufügen** die Eigenschaft **Authentifizierung** hinzu, wenn die Eigenschaft noch nicht angezeigt wird.

        ![Screenshot der integrierten Beispielaktion mit geöffneter Liste „Neuen Parameter hinzufügen“ und ausgewählter Option „Authentifizierung“ in „Verbrauch“.](./media/create-managed-service-identity/built-in-authentication-consumption.png)

     1. Wählen Sie in der Liste **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

        ![Screenshot der integrierten Beispielaktion mit geöffneter Liste „Authentifizierungstyp“ und ausgewählter Option „Verwaltete Identität“ in „Verbrauch“.](./media/create-managed-service-identity/built-in-managed-identity-consumption.png)

     Weitere Informationen finden Sie im [Beispiel: Authentifizieren eines vordefinierten Triggers oder einer Aktion über eine verwaltete Identität](#authenticate-built-in-managed-identity).

   * **Verwaltete Connectorvorgänge, die die Authentifizierung mit verwalteter Identität unterstützen** (Vorschau)

     1. Wählen Sie auf der Seite zur Mandantenauswahl die Option **Verbindung mit verwalteter Identität herstellen (Vorschau)** aus, zum Beispiel:

        ![Screenshot, der die Azure Resource Manager-Aktion und die Auswahl „Verbindung mit verwalteter Identität herstellen“ in „Verbrauch“ zeigt.](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

     1. Geben Sie auf der nächsten Seite, **Verbindungsname**, einen Namen an, der für die Verbindung verwendet werden soll.

     1. Wählen Sie als Authentifizierungstyp basierend auf Ihrem verwalteten Connector eine der folgenden Optionen aus:

        * **Einzelauthentifizierung**: Diese Connectors unterstützen nur einen Authentifizierungstyp. Wählen Sie in der Liste **Verwaltete Identität** die derzeit aktivierte verwaltete Identität aus, sofern sie noch nicht ausgewählt ist, und wählen Sie dann **Erstellen** aus. Beispiel:

          ![Screenshot: Seite „Verbindungsname“ und ausgewählte einzelne verwaltete Identität in „Verbrauch“.](./media/create-managed-service-identity/single-system-identity-consumption.png)

        * **Mehrfachauthentifizierung**: Diese Connectors unterstützen mehr als einen Authentifizierungstyp. Wählen Sie in der Liste **Authentifizierungstyp** **Verwaltete Identität der Logic Apps** > **Erstellen** aus. Beispiel:

          ![Screenshot: Seite „Verbindungsname“ und ausgewählte „Verwaltete Identität der Logic Apps“ in „Verbrauch“.](./media/create-managed-service-identity/multi-system-identity-consumption.png)

        Weitere Informationen finden Sie im [Beispiel: Authentifizieren eines Triggers mit verwaltetem Connector oder einer Aktion über eine verwaltete Identität](#authenticate-managed-connector-managed-identity).

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie Ihre Logik-App-Ressource im [Azure-Portal](https://portal.azure.com).

1. Wenn dies noch nicht erfolgt ist, fügen Sie [den Trigger oder die Aktion hinzu, der bzw. die verwaltete Identitäten unterstützt](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Sie können nicht für alle Trigger und Aktionen einen Authentifizierungstyp hinzufügen. Weitere Informationen finden Sie unter [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Führen Sie für den hinzugefügten Trigger oder die hinzugefügte Aktion die folgenden Schritte aus:

   * **Eingebaute Vorgänge, die die Authentifizierung der verwalteten Identität unterstützen**

     1. Fügen Sie in der Liste **Neuen Parameter hinzufügen** die Eigenschaft **Authentifizierung** hinzu, wenn die Eigenschaft noch nicht angezeigt wird.

        ![Screenshot der integrierten Beispielaktion mit geöffneter Liste „Neuen Parameter hinzufügen“ und ausgewählter Option „Authentifizierung“ – Standard.](./media/create-managed-service-identity/built-in-authentication-standard.png)

     1. Wählen Sie in der Liste **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

        ![Screenshot der integrierten Beispielaktion mit geöffneter Liste „Authentifizierungstyp“ und ausgewählter Option „Verwaltete Identität“ in „Standard“.](./media/create-managed-service-identity/built-in-managed-identity-standard.png)

     Weitere Informationen finden Sie im [Beispiel: Authentifizieren eines vordefinierten Triggers oder einer Aktion über eine verwaltete Identität](#authenticate-built-in-managed-identity).

   * **Verwaltete Connectorvorgänge, die die Authentifizierung mit verwalteter Identität unterstützen** (Vorschau)

     1. Wählen Sie auf der Seite zur Mandantenauswahl die Option **Verbindung mit verwalteter Identität herstellen (Vorschau)** aus, zum Beispiel:

        ![Screenshot, der die Azure Resource Manager-Aktion und die Auswahl „Verbindung mit verwalteter Identität herstellen“ in „Standard“ zeigt.](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

     1. Geben Sie auf der nächsten Seite, **Verbindungsname**, einen Namen an, der für die Verbindung verwendet werden soll.

     1. Wählen Sie als Authentifizierungstyp basierend auf Ihrem verwalteten Connector eine der folgenden Optionen aus:

        * **Einzelauthentifizierung**: Diese Connectors unterstützen nur einen Authentifizierungstyp. Wählen Sie in der Liste **Verwaltete Identität** die derzeit aktivierte verwaltete Identität aus, sofern sie noch nicht ausgewählt ist, und wählen Sie dann **Erstellen** aus. Beispiel:

          ![Screenshot: Seite „Verbindungsname“ und ausgewählte einzelne verwaltete Identität in „Standard“.](./media/create-managed-service-identity/single-system-identity-standard.png)

        * **Mehrfachauthentifizierung**: Diese Connectors unterstützen mehr als einen Authentifizierungstyp. Wählen Sie in der Liste **Authentifizierungstyp** **Verwaltete Identität der Logic Apps** > **Erstellen** aus. Beispiel:

          ![Screenshot: Seite „Verbindungsname“ und ausgewählte „Verwaltete Identität der Logic Apps“ in „Standard“.](./media/create-managed-service-identity/multi-system-identity-standard.png)

        Weitere Informationen finden Sie im [Beispiel: Authentifizieren eines Triggers mit verwaltetem Connector oder einer Aktion über eine verwaltete Identität](#authenticate-managed-connector-managed-identity).

---

<a name="authenticate-built-in-managed-identity"></a>

## <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Beispiel: Authentifizieren eines vordefinierten Triggers oder einer Aktion über eine verwaltete Identität

Die für die Logik-App-Ressource aktivierte systemseitig zugewiesene Identität kann von dem integrierten HTTP-Trigger oder der HTTP-Aktion verwendet werden. Im Allgemeinen werden im HTTP-Trigger oder der HTTP-Aktion die folgenden Eigenschaften verwendet, um die Ressource oder Entität anzugeben, auf die Sie zugreifen möchten:

| Eigenschaft | Erforderlich | BESCHREIBUNG |
|----------|----------|-------------|
| **Methode** | Ja | Die HTTP-Methode, die in dem Vorgang verwendet wird, den Sie ausführen möchten. |
| **URI** | Ja | Die Endpunkt-URL für den Zugriff auf die Azure-Zielressource oder Azure-Zielentität. Die URI-Syntax enthält normalerweise die [Ressourcen-ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) für die Azure-Ressource oder den Azure-Dienst. |
| **Headers** | Nein | Alle Headerwerte, die Sie in der ausgehenden Anforderung einfügen müssen oder möchten, z. B. den Inhaltstyp |
| **Abfragen** | Nein | Alle Abfrageparameter, die Sie in der Anforderung einfügen müssen oder möchten, z. B. den Parameter für einen bestimmten Vorgang oder die API-Version für den auszuführenden Vorgang |
| **Authentifizierung** | Ja | Der Authentifizierungstyp, der zum Authentifizieren des Zugriffs auf die Zielressource oder Zielentität verwendet wird. |
||||

Als konkretes Beispiel wird angenommen, dass Sie den [Snapshot Blob-Vorgang](/rest/api/storageservices/snapshot-blob) für ein Blob in dem Azure Storage-Konto ausführen möchten, in dem Sie zuvor den Zugriff für die Identität eingerichtet haben. Jedoch unterstützt der [Azure Blob Storage-Connector](/connectors/azureblob/) diesen Vorgang derzeit nicht. Stattdessen können Sie diesen Vorgang mithilfe der [HTTP-Aktion](logic-apps-workflow-actions-triggers.md#http-action) oder mit einem anderen [REST-API-Vorgang des Blob-Diensts](/rest/api/storageservices/operations-on-blobs) ausführen.

> [!IMPORTANT]
> Um über HTTP-Anforderungen und verwaltete Identitäten auf Azure Storage-Konten hinter Firewalls zuzugreifen, müssen Sie sicherstellen, dass Sie auch das Speicherkonto mit der [Ausnahme einrichten, die den Zugriff durch vertrauenswürdige Microsoft-Dienste](../connectors/connectors-create-api-azureblobstorage.md#access-blob-storage-with-managed-identities) zulässt.

Zum Ausführen des [Snapshot Blob-Vorgangs](/rest/api/storageservices/snapshot-blob) werden in der HTTP-Aktion die folgenden Eigenschaften angegeben:

| Eigenschaft | Erforderlich | Beispielwert | BESCHREIBUNG |
|----------|----------|---------------|-------------|
| **Methode** | Ja | `PUT`| Die im Snapshot Blob-Vorgang verwendete HTTP-Methode |
| **URI** | Ja | `https://<storage-account-name>/<folder-name>/{name}` | Die Ressourcen-ID für eine Azure Blob Storage-Datei in der globalen (öffentlichen) Azure-Umgebung, in der diese Syntax verwendet wird. |
| **Headers** | Für Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Die Headerwerte `x-ms-blob-type`, `x-ms-version` und `x-ms-date`, die für Azure Storage-Vorgänge erforderlich sind. <p><p>**Wichtig**: In ausgehenden Anforderungen für HTTP-Trigger und HTTP-Aktionen für Azure Storage sind für den Header die `x-ms-version`-Eigenschaft und die API-Version für den auszuführenden Vorgang erforderlich. `x-ms-date` muss das aktuelle Datum sein. Andernfalls tritt bei Ihrem Workflow ein `403 FORBIDDEN`-Fehler auf. Um das aktuelle Datum im erforderlichen Format abzurufen, können Sie den Ausdruck im Beispielwert verwenden. <p>Weitere Informationen finden Sie in diesen Themen: <p><p>- [Anforderungsheader: Snapshot Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Versionsverwaltung für Azure Storage-Dienste](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Abfragen** | Nur für den Vorgang für den Momentaufnahmeblob | `comp` = `snapshot` | Der Name und der Wert des Abfrageparameters für den Vorgang. |
|||||

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Das folgende Beispiel zeigt eine HTTP-Beispielaktion mit allen zuvor beschriebenen Eigenschaftswerten, die für den Momentaufnahmeblobvorgang verwendet werden sollen:

![Screenshot: Azure-Portal mit dem Workflow der Logik-App (Verbrauch) und der HTTP-Aktion, die für den Zugriff auf Ressourcen eingerichtet ist.](./media/create-managed-service-identity/http-action-example.png)

1. Nachdem Sie die HTTP-Aktion hinzugefügt haben, fügen Sie ihr die Eigenschaft **Authentifizierung** hinzu. Wählen Sie in der Liste **Neuen Parameter hinzufügen** die Option **Authentifizierung** aus.

   ![Screenshot: Workflow „Verbrauch“ mit HTTP-Aktion und Liste „Neuen Parameter hinzufügen“ mit ausgewählter Eigenschaft „Authentifizierung“ geöffnet](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Sie können nicht für alle Trigger und Aktionen einen Authentifizierungstyp hinzufügen. Weitere Informationen finden Sie unter [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Wählen Sie in der Liste **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

   ![Screenshot: Workflow „Verbrauch“ mit HTTP-Aktion und Eigenschaft „Authentifizierung“ mit ausgewähltem Wert für „Verwaltete Identität“.](./media/create-managed-service-identity/select-managed-identity.png)

1. Wählen Sie in der Liste der verwalteten Identitäten einen Eintrag für Ihr Szenario aus.

   * Wenn Sie die vom System zugewiesene Identität eingerichtet haben, wählen Sie **Systemseitig zugewiesene verwaltete Identität** aus, sofern sie nicht bereits ausgewählt ist.

     ![Screenshot: Workflow „Verbrauch“ mit HTTP-Aktion und Eigenschaft „Verwaltete Identität“ mit ausgewähltem Wert „systemseitig zugewiesene verwaltete Identität“.](./media/create-managed-service-identity/select-system-assigned-identity.png)

   * Wenn Sie eine benutzerseitig zugewiesene Identität einrichten, wählen Sie diese Identität aus, sofern sie nicht bereits ausgewählt ist.

     ![Screenshot: Workflow „Verbrauch“ mit HTTP-Aktion und Eigenschaft „Verwaltete Identität“ mit ausgewähltem Wert „benutzerseitig zugewiesene Identität“.](./media/create-managed-service-identity/select-user-assigned-identity-action.png)

   In diesem Beispiel wird weiterhin die **Systemseitig zugewiesene verwaltete Identität** verwendet.

1. Bei einigen Triggern und Aktionen wird die **Audience**-Eigenschaft auch angezeigt, damit Sie die Zielressourcen-ID festlegen können. Legen Sie den Wert der **Audience**-Eigenschaft auf die [Ressourcen-ID für die Zielressource oder den Zieldienst](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) fest. Andernfalls wird für die **Audience**-Eigenschaft standardmäßig die Ressourcen-ID `https://management.azure.com/` verwendet, bei der es sich um die Ressourcen-ID für Azure Resource Manager handelt.
  
    Wenn Sie beispielsweise den Zugriff auf eine [Key Vault-Ressource in der globalen Azure-Cloud](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault) authentifizieren möchten, müssen Sie die **Audience**-Eigenschaft auf *exakt* die folgende Ressourcen-ID festlegen: `https://vault.azure.net`. Beachten Sie, dass diese spezifische Ressourcen-ID *keine* nachgestellten Schrägstriche aufweist. Tatsächlich kann das Einschließen eines nachgestellten Schrägstrichs entweder einen Fehler vom Typ `400 Bad Request` oder `401 Unauthorized` bewirken.

   > [!IMPORTANT]
   > Vergewissern Sie sich, dass die Zielressourcen-ID *genau dem Wert entspricht*, der in Azure Active Directory (AD) erwartet wird, einschließlich aller erforderlichen nachgestellten Schrägstriche. Die Ressourcen-ID für alle Azure Blob Storage-Konten erfordert z. B. einen nachgestellten Schrägstrich. Allerdings erfordert die Ressourcen-ID für ein bestimmtes Speicherkonto keinen nachgestellten Schrägstrich. [Hier](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) finden Sie die Ressourcen-IDs für die Azure-Dienste, die die Azure AD-Authentifizierung unterstützen.

   In diesem Beispiel wird die **Audience**-Eigenschaft auf `https://storage.azure.com/` festgelegt, sodass die für die Authentifizierung verwendeten Zugriffstoken für alle Speicherkonten gültig sind. Sie können jedoch auch die Stammdienst-URL für ein bestimmtes Speicherkonto angeben, z. B. `https://<your-storage-account>.blob.core.windows.net`.

   ![Screenshot des Workflows „Verbrauch“ mit HTTP-Aktion und Eigenschaft „Zielgruppe“, die auf die Zielressourcen-ID festgelegt sind](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Weitere Informationen zum Autorisieren des Zugriffs mit Azure AD für Azure Storage finden Sie in der folgenden Dokumentation:

   * [Autorisieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)

   * [Autorisieren des Zugriffs auf Azure Storage mit Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Fahren Sie damit fort, den Workflow wie gewünscht zu erstellen.

### <a name="standard"></a>[Standard](#tab/standard)

Das folgende Beispiel zeigt eine HTTP-Beispielaktion mit allen zuvor beschriebenen Eigenschaftswerten, die für den Momentaufnahmeblobvorgang verwendet werden sollen:

![Screenshot: Azure-Portal mit dem Workflow der Logik-App (Standard) und der HTTP-Aktion, die für den Zugriff auf Ressourcen eingerichtet ist.](./media/create-managed-service-identity/http-action-example-standard.png)

1. Nachdem Sie die HTTP-Aktion hinzugefügt haben, fügen Sie ihr die Eigenschaft **Authentifizierung** hinzu. Wählen Sie in der Liste **Neuen Parameter hinzufügen** die Option **Authentifizierung** aus.

   ![Screenshot: Workflow „Standard“ mit HTTP-Aktion und Liste „Neuen Parameter hinzufügen“ mit ausgewählter Eigenschaft „Authentifizierung“ geöffnet](./media/create-managed-service-identity/add-authentication-property-standard.png)

   > [!NOTE]
   > Sie können nicht für alle Trigger und Aktionen einen Authentifizierungstyp hinzufügen. Weitere Informationen finden Sie unter [Authentifizierungstypen für Trigger und Aktionen, die die Authentifizierung unterstützen](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Wählen Sie in der Liste **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

   ![Screenshot: Workflow „Standard“ mit HTTP-Aktion und Eigenschaft „Authentifizierung“ mit ausgewähltem Wert für „Verwaltete Identität“.](./media/create-managed-service-identity/select-managed-identity-standard.png)

1. Wählen Sie in der Liste der verwalteten Identität die Option **systemseitig zugewiesene verwaltete Identität** aus, falls sie noch nicht ausgewählt ist.

     ![Screenshot: Workflow „Standard“ mit geöffneter HTTP-Aktion und Liste „Verwaltete Identität“ mit ausgewählter Option „Systemseitig zugewiesene verwaltete Identität“](./media/create-managed-service-identity/select-system-assigned-identity-standard.png)

1. Bei einigen Triggern und Aktionen wird die **Audience**-Eigenschaft auch angezeigt, damit Sie die Zielressourcen-ID festlegen können. Legen Sie den Wert der **Audience**-Eigenschaft auf die [Ressourcen-ID für die Zielressource oder den Zieldienst](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) fest. Andernfalls wird für die **Audience**-Eigenschaft standardmäßig die Ressourcen-ID `https://management.azure.com/` verwendet, bei der es sich um die Ressourcen-ID für Azure Resource Manager handelt.
  
    Wenn Sie beispielsweise den Zugriff auf eine [Key Vault-Ressource in der globalen Azure-Cloud](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault) authentifizieren möchten, müssen Sie die **Audience**-Eigenschaft auf *exakt* die folgende Ressourcen-ID festlegen: `https://vault.azure.net`. Beachten Sie, dass diese spezifische Ressourcen-ID *keine* nachgestellten Schrägstriche aufweist. Tatsächlich kann das Einschließen eines nachgestellten Schrägstrichs entweder einen Fehler vom Typ `400 Bad Request` oder `401 Unauthorized` bewirken.

   > [!IMPORTANT]
   > Vergewissern Sie sich, dass die Zielressourcen-ID *genau dem Wert entspricht*, der in Azure Active Directory (AD) erwartet wird, einschließlich aller erforderlichen nachgestellten Schrägstriche. Die Ressourcen-ID für alle Azure Blob Storage-Konten erfordert z. B. einen nachgestellten Schrägstrich. Allerdings erfordert die Ressourcen-ID für ein bestimmtes Speicherkonto keinen nachgestellten Schrägstrich. [Hier](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) finden Sie die Ressourcen-IDs für die Azure-Dienste, die die Azure AD-Authentifizierung unterstützen.

   In diesem Beispiel wird die **Audience**-Eigenschaft auf `https://storage.azure.com/` festgelegt, sodass die für die Authentifizierung verwendeten Zugriffstoken für alle Speicherkonten gültig sind. Sie können jedoch auch die Stammdienst-URL für ein bestimmtes Speicherkonto angeben, z. B. `https://<your-storage-account>.blob.core.windows.net`.

   ![Festlegen der Zielressourcen-ID in der Audience-Eigenschaft](./media/create-managed-service-identity/specify-audience-url-target-resource-standard.png)

   Weitere Informationen zum Autorisieren des Zugriffs mit Azure AD für Azure Storage finden Sie in der folgenden Dokumentation:

   * [Autorisieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)
   * [Autorisieren des Zugriffs auf Azure Storage mit Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Fahren Sie damit fort, den Workflow wie gewünscht zu erstellen.

---

<a name="authenticate-managed-connector-managed-identity"></a>

## <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Beispiel: Authentifizieren eines Triggers oder einer Aktion eines verwalteter Connectors über eine verwaltete Identität

Der Azure Resource Manager-Connector verfügt über die Aktion **Ressource lesen**, die die verwaltete Identität verwenden kann, die Sie für Ihre Logik-App-Ressource aktivieren. Dieses Beispiel zeigt Ihnen die Verwendung der systemseitig zugewiesenen verwalteten Identität.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Nachdem Sie ihrem Workflow die Aktion hinzugefügt und Ihren Azure AD-Mandanten ausgewählt haben, wählen Sie **Mit verwalteter Identität verbinden (Vorschau)** aus.

   ![Screenshot, der die Azure Resource Manager-Aktion und die Auswahl „Verbindung mit verwalteter Identität herstellen“ zeigt.](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

1. Geben Sie auf der Seite „Verbindungsname“ einen Namen für die Verbindung an, und wählen Sie die verwaltete Identität aus, die Sie verwenden möchten.

   Die Azure Resource Manager-Aktion ist eine Einzelauthentifizierungsaktion, sodass im Bereich „**Verbindungsinformationen**“ eine Liste mit „verwalteten Identitäten“ angezeigt wird, die automatisch die verwaltete Identität auswählt, die derzeit für die Logik-App-Ressource aktiviert ist. Wenn Sie eine systemseitig zugewiesene verwaltete Identität aktiviert haben, wählt die Liste **Verwaltete Identität** **die systemseitig zugewiesene verwaltete Identität** aus. Wenn Sie stattdessen eine benutzerseitig zugewiesene verwaltete Identität aktiviert haben, wählt die Liste stattdessen diese Identität aus.

   Wenn Sie einen Trigger oder eine Aktion mit mehreren Authentifizierungen verwenden, z. B. Azure Blob Storage, wird im Bereich „Verbindungsinformationen“ eine Liste mit dem **Authentifizierungstyp** angezeigt, die die Option **Verwaltete Identität der Logic Apps** und andere Authentifizierungstypen enthält.

   In diesem Beispiel ist die **systemseitig zugewiesene verwaltete Identität** die einzige verfügbare Auswahl.

   ![Screenshot, der die Azure Resource Manager-Aktion mit eingegebenem Verbindungsnamen und Auswahl von „Systemseitig zugewiesene verwaltete Identität“ anzeigt.](./media/create-managed-service-identity/single-system-identity-consumption.png)

   > [!NOTE]
   > Wenn die verwaltete Identität nicht aktiviert ist, wenn Sie versuchen, die Verbindung zu erstellen, die Verbindung zu ändern oder entfernt wurde, während eine Verbindung mit aktivierter verwalteter Identität noch vorhanden ist, wird eine Fehlermeldung angezeigt, dass Sie die Identität aktivieren und Zugriff auf die Zielressource gewähren müssen.

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

1. Nachdem die Verbindung erfolgreich erstellt wurde, kann der Designer dynamische Werte, Inhalte oder Schemas durch Authentifizierung über die verwaltete Identität abrufen.

1. Fahren Sie damit fort, den Workflow wie gewünscht zu erstellen.

### <a name="standard"></a>[Standard](#tab/standard)

1. Nachdem Sie ihrem Workflow die Aktion hinzugefügt haben, wählen Sie im Bereich **Verbindung erstellen** der Aktion Ihren Azure AD-Mandanten und dann **Verbinden mit verwalteter Identität (Vorschau)** aus.

   ![Screenshot, der die Azure Resource Manager-Aktion und die Auswahl „Verbindung mit verwalteter Identität herstellen“ zeigt.](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

1. Geben Sie auf der Seite „Verbindungsname“ einen Namen für die Verbindung ein.

   Die Azure Resource Manager-Aktion ist eine Einzelauthentifizierungsaktion, sodass im Bereich „**Verbindungsinformationen**“ eine Liste mit „verwalteten Identitäten“ angezeigt wird, die automatisch die verwaltete Identität auswählt, die derzeit für die Logik-App-Ressource aktiviert ist. Wenn Sie eine systemseitig zugewiesene verwaltete Identität aktiviert haben, wählt die Liste **Verwaltete Identität** **die systemseitig zugewiesene verwaltete Identität** aus. Wenn Sie stattdessen eine benutzerseitig zugewiesene verwaltete Identität aktiviert haben, wählt die Liste stattdessen diese Identität aus.

   In diesem Beispiel ist die **systemseitig zugewiesene verwaltete Identität** die einzige verfügbare Auswahl.

   ![Screenshot, der die Azure Resource Manager-Aktion mit eingegebenem Verbindungsnamen und Auswahl von „Systemseitig zugewiesene verwaltete Identität“ anzeigt.](./media/create-managed-service-identity/single-system-identity-standard.png)

   Wenn Sie einen Trigger oder eine Aktion mit mehreren Authentifizierungen verwenden, z. B. Azure Blob Storage, wird im Bereich „Verbindungsinformationen“ eine Liste mit dem **Authentifizierungstyp** angezeigt, die die Option **Verwaltete Identität** und andere Authentifizierungstypen enthält.

   > [!NOTE]
   > Wenn die verwaltete Identität nicht aktiviert ist, wenn Sie versuchen, die Verbindung zu erstellen, die Verbindung zu ändern oder entfernt wurde, während eine Verbindung mit aktivierter verwalteter Identität noch vorhanden ist, wird eine Fehlermeldung angezeigt, dass Sie die Identität aktivieren und Zugriff auf die Zielressource gewähren müssen.

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

1. Nachdem die Verbindung erfolgreich erstellt wurde, kann der Designer dynamische Werte, Inhalte oder Schemas durch Authentifizierung über die verwaltete Identität abrufen.

1. Fahren Sie damit fort, den Workflow wie gewünscht zu erstellen.

---

<a name="logic-app-resource-definition-connection-managed-identity"></a>

## <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity-consumption"></a>Ressourcendefinition und Verbindungen von Logik-Apps, die eine verwaltete Identität (Verbrauch) verwenden

Eine Verbindung, die eine verwaltete Identität aktiviert und verwendet, entspricht einem speziellen Verbindungstyp, der nur mit einer verwalteten Identität funktioniert. Zur Laufzeit verwendet die Verbindung die verwaltete Identität, die für die Logik-App-Ressource aktiviert wurde. Während der Laufzeit prüft der Azure Logic Apps-Dienst, ob ein Trigger des verwalteten Connectors und Aktionen im Logik-App-Workflow für die Verwendung der verwalteten Identität eingerichtet sind. Außerdem stellt er sicher, dass alle erforderlichen Berechtigungen eingerichtet sind, um über die verwaltete Identität auf die Zielressourcen zuzugreifen, die durch den Trigger und die Aktionen angegeben werden. Bei erfolgreicher Ausführung ruft Azure Logic App das Azure AD-Token ab, das der verwalteten Identität zugeordnet ist, authentifiziert anhand dieser Identität den Zugriff auf die Zielressource und führt den konfigurierten Vorgang in Trigger und Aktionen aus.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

In einer **Logik-App-Ressource (Verbrauch)** wird die Verbindungskonfiguration im `parameters`-Objekt der Logik-App-Ressourcendefinition gespeichert, das das Objekt `$connections` enthält, das Zeiger auf die Ressourcen-ID der Verbindung zusammen mit der Ressourcen-ID der Identität enthält, wenn die vom Benutzer zugewiesene Identität aktiviert ist.

Dieses Beispiel zeigt die Konfiguration bei Aktivierung der systemseitig zugewiesenen verwalteten Identität in der Logik-App:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
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
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
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

### <a name="standard"></a>[Standard](#tab/standard)

In einer **Logik-App-Ressource (Standard)** wird die Verbindungskonfiguration in der Logik-App-Ressource oder in der `connections.json`-Datei des Projekts gespeichert, die ein JSON-Objekt `managedApiConnections` enthält, das Verbindungskonfigurationsinformationen für jeden verwalteten Connector enthält, der in einem Workflow verwendet wird. Diese Verbindungsinformationen enthalten beispielsweise Zeiger auf die Ressourcen-ID der Verbindung sowie die Eigenschaften der verwalteten Identität, z. B. die Ressourcen-ID, wenn die benutzerseitig zugewiesene Identität aktiviert ist.

Dieses Beispiel zeigt die Konfiguration bei Aktivierung einer benutzerseitig zugewiesenen verwalteten Identität in der Logik-App:

```json
{
    "managedApiConnections": {
        "<connector-name>": {
            "api": {
                "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/<connector-name>"
            },
            "connection": {
                "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/<connection-name>"
            },
            "connectionRuntimeUrl": <connection-URL>,
            "authentication": { // Authentication with APIHub
                "type": "ManagedServiceIdentity"
            },
            "connectionProperties": {
                "authentication": { //Authentication with the target resource
                    "type": "ManagedServiceIdentity",
                    "identity": "<user-assigned-identity>", // Optional
                    "audience": "<resource-URL>"
                }
            }
        }
    }
}
```

---

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities-consumption"></a>ARM-Vorlage für verwaltete Verbindungen und verwaltete Identitäten (Verbrauch)

Wenn Sie die Bereitstellung mit einer ARM-Vorlage automatisieren und Ihr Logik-App-Workflow einen Trigger oder eine Aktion für einen verwalteten Connector enthält, der eine verwaltete Identität verwendet, vergewissern Sie sich, dass die zugrunde liegende Verbindungsressourcendefinition die `parameterValueType`-Eigenschaft mit `Alternative` als Eigenschaftswert enthält. Andernfalls richtet Ihre ARM-Bereitstellung die Verbindung nicht so ein, dass die verwaltete Identität für die Authentifizierung verwendet wird, und die Verbindung funktioniert im Workflow Ihrer Logik-App nicht. Diese Anforderung gilt nur für [bestimmte Trigger und Aktionen des verwalteten Connectors](#triggers-actions-managed-identity), bei denen Sie die Option [**Verbindung mit verwalteter Identität herstellen**](#authenticate-managed-connector-managed-identity) ausgewählt haben.

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

Um die Verwendung der verwalteten Identität für die Authentifizierung zu beenden, [entfernen Sie zunächst den Zugriff der Identität auf die Zielressource](#disable-identity-target-resource). [Deaktivieren Sie als Nächstes in Ihrer Logik-App-Ressource die systemseitig zugewiesene Identität, oder entfernen Sie die vom Benutzer zugewiesene Identität](#disable-identity-logic-app).

Wenn Sie die verwaltete Identität für Ihre Logik-App-Ressource deaktivieren, entfernen Sie die Funktion für diese Identität, Zugriff auf Azure-Ressourcen anzugeben, auf die die Identität Zugriff hatte.

> [!NOTE]
> Wenn Sie die systemseitig zugewiesene Identität deaktivieren, funktionieren alle Verbindungen, die von Workflows im Workflow dieser Logik-App verwendet werden, nicht zur Laufzeit, auch wenn Sie die Identität sofort wieder aktivieren. Dieses Verhalten tritt auf, da durch das Deaktivieren der Identität die Objekt-ID gelöscht wird. Jedes Mal, wenn Sie die Identität aktivieren, generiert Azure die Identität mit einer anderen und eindeutigen Objekt-ID. Um dieses Problem zu beheben, müssen Sie die Verbindungen neu erstellen, damit sie die aktuelle Objekt-ID für die aktuelle systemseitig zugewiesene Identität verwenden.
>
> Versuchen Sie, die systemseitig zugewiesene Identität so weit wie möglich zu deaktivieren. Wenn Sie den Zugriff der Identität auf Azure-Ressourcen entfernen möchten, entfernen Sie die Rollenzuweisung der Identität aus der Zielressource. Wenn Sie die Logik-App-Ressource löschen, wird in Azure die verwaltete Identität automatisch aus Azure AD entfernt.

Die Schritte in diesem Abschnitt behandeln die Verwendung des [Azure-Portals](#azure-portal-disable) und der [Azure Resource Manager Vorlage (ARM-Vorlage)](#template-disable). Informationen zu Azure PowerShell, Azure CLI und der Azure-REST-API finden Sie in der folgenden Dokumentation:

| Tool | Dokumentation |
|------|---------------|
| Azure PowerShell | 1. [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-powershell.md). <br>2. [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). |
| Azure-Befehlszeilenschnittstelle | 1. [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-cli.md). <br>2. [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). |
| Azure-REST-API | 1. [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-rest.md). <br>2. [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md). |
|||

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Deaktivieren der verwalteten Identität im Azure-Portal

Um den Zugriff für die verwaltete Identität zu entfernen, entfernen Sie die Rollenzuweisung der Identität aus der Zielressource, und deaktivieren Sie dann die verwaltete Identität.

<a name="disable-identity-target-resource"></a>

#### <a name="remove-role-assignment"></a>Entfernen von Rollenzuweisungen

Mit den folgenden Schritten wird der Zugriff auf die Zielressource aus der verwalteten Identität entfernt:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Azure-Zielressource, für die Sie den Zugriff für die verwaltete Identität aufheben möchten.

1. Wählen Sie im Menü der Zielressource die Option **Zugriffssteuerung (IAM)** aus. Wählen Sie auf der Symbolleiste die Option **Rollenzuweisungen** aus.

1. Wählen Sie in der Liste der Rollen die verwalteten Identitäten aus, die Sie entfernen möchten. Wählen Sie auf der Symbolleiste die Option **Entfernen** aus.

   > [!TIP]
   > Wenn die Option **Entfernen** deaktiviert ist, verfügen Sie wahrscheinlich nicht über die erforderlichen Berechtigungen. Weitere Informationen zu den Berechtigungen, mit denen Sie Rollen für Ressourcen verwalten können, finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../active-directory/roles/permissions-reference.md).

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app-resource"></a>Deaktivieren einer verwalteten Identität für die Logik-App-Ressource

1. Öffnen Sie Ihre Logik-App-Ressource im [Azure-Portal](https://portal.azure.com).

1. Wählen Sie im Navigationsmenü der Logik-App unter **Einstellungen** die Option **Identität** aus, und befolgen Sie dann die Schritte für Ihre Identität:

   * Wählen Sie **Vom System zugewiesen** > **Ein** > **Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

   * Wählen Sie **Vom Benutzer zugewiesen**, die verwaltete Identität und dann **Entfernen** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-an-arm-template"></a>Deaktivieren einer verwalteten Identität in einer ARM-Vorlage

Wenn Sie die verwaltete Identität der Logik-App mithilfe einer ARM-Vorlage erstellt haben, legen Sie die untergeordnete `type`-Eigenschaft des `identity`-Objekts auf `None` fest.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Schützen des Zugriffs und der Daten in Azure Logic Apps](logic-apps-securing-a-logic-app.md)
