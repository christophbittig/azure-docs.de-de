---
title: Erstellen und Verwalten von Integrationskonten
description: Erstellen und Verwalten von Integrationskonten zum Erstellen von B2B-Workflows für die Unternehmensintegration in Azure Logic Apps mit dem Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: fa900b7df1db1efbc8fe28a96cd2048113d7390d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550382"
---
# <a name="create-and-manage-integration-accounts-for-b2b-workflows-in-azure-logic-apps-using-the-enterprise-integration-pack"></a>Erstellen und Verwalten von Integrationskonten für B2B-Workflows in Azure Logic Apps mithilfe des Enterprise Integration Pack

Bevor Sie B2B (Business-to-Business)- und Unternehmensintegrations-Workflows mithilfe von Azure Logic Apps erstellen können, müssen Sie eine *Integrationskonto* ressource erstellen. Dieses Konto ist ein skalierbarer, cloudbasierter Container in Azure, der das Speichern und Verwalten von B2B-Artefakten vereinfacht, die Sie in Ihren Workflows für B2B-Szenarien definieren und verwenden. Solche Artefakte umfassen [Handelspartner](logic-apps-enterprise-integration-partners.md), [Vereinbarungen](logic-apps-enterprise-integration-agreements.md), [Zuordnungen](logic-apps-enterprise-integration-maps.md), [Schemas](logic-apps-enterprise-integration-schemas.md), [Zertifikate](logic-apps-enterprise-integration-certificates.md) usw. Sie benötigen außerdem ein Integrationskonto, um B2B-Nachrichten auf elektronischem Wege mit anderen Organisationen austauschen zu können. Wenn andere Organisationen Protokolle und Nachrichtenformate verwenden, die von denen Ihrer Organisation abweichen, müssen Sie diese Formate konvertieren, damit das System Ihrer Organisation diese Nachrichten verarbeiten kann. Zu den unterstützten Branchenstandardprotokollen gehören [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) und [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

> [!TIP]
> Informationen zum Erstellen eines Integrationskontos zur Verwendung mit einer [Integrationsdienstumgebung](connect-virtual-network-vnet-isolated-environment-overview.md) finden Sie unter [Erstellen von Integrationskonten in einer ISE](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

In diesem Artikel wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Erstellen eines Integrationskontos.
* Verknüpfen eines Integrationskontos mit einer Logik-App-Ressource.
* Ändern des Tarifs für Ihr Integrationskonto
* Aufheben der Verknüpfung eines Integrationskontos mit einer Logik-App.
* Verschieben eines Integrationskontos in eine andere Azure-Ressourcengruppe bzw. ein anderes Azure-Abonnement.
* Löschen eines Integrationskontos.

Falls Sie noch nicht mit Azure Logic Apps vertraut sind, lesen Sie [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie in [B2B-Unternehmensintegrations-Workflows mit Azure Logic Apps und Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Sie müssen dasselbe Azure-Abonnement sowohl für Ihr Integrationskonto als auch für Ihre Logik-App-Ressource verwenden.

* Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigen Sie eine Logik-App-Ressource, die Sie [mit Ihrem Integrationskonto verknüpfen können](#link-account). Diese Verknüpfung ist erforderlich, bevor Sie Ihre Artefakte in Ihrem Workflow verwenden können. Sie können Ihre Artefakte ohne diese Verknüpfung erstellen, aber die Verknüpfung ist erforderlich, wenn Sie bereit sind, diese Artefakte in Ihren Workflows zu verwenden.

* Wenn Sie den [Ressourcentyp **Logik-App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, können Sie Ihrer Logic-App-Ressource Zuordnungen und Schemas entweder über das Azure-Portal oder mit Visual Studio Code direkt hinzufügen. Sie können diese Artefakte dann in mehreren Workflows innerhalb *derselben Logik-App-Ressource* verwenden. Sie müssen jedoch weiterhin ein Integrationskonto für Ihre anderen B2B-Artefakte erstellen sowie für die Verwendung von B2B-Vorgängen wie [AS2](logic-apps-enterprise-integration-as2.md)-, [X12](logic-apps-enterprise-integration-x12.md)-, [EDIFACT](logic-apps-enterprise-integration-edifact.md)- und [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Allerdings müssen Sie Ihr Integrationskonto nicht mit Ihrer Logik-App-Ressource verknüpfen, weshalb die Verknüpfungsfunktionalität nicht vorhanden ist.

## <a name="create-integration-account"></a>Integrationskonto erstellen

Integrationskonten sind in verschiedenen Tarifen verfügbar, die [sich preislich unterscheiden](https://azure.microsoft.com/pricing/details/logic-apps/). Auf Grundlage des von Ihnen gewählten Tarifs können beim Erstellen eines Integrationskontos Kosten entstehen. Weitere Informationen finden Sie im [Preis- und Abrechnungsmodell für Logic Apps](logic-apps-pricing.md#integration-accounts) und unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/).

Bestimmen Sie auf Grundlage Ihrer Anforderungen und Szenarien den geeigneten Integrationskontotarif, der erstellt werden soll. Sowohl Ihr Integrationskonto als auch Ihre Logik-App-Ressource müssen *denselben* Standort oder dieselbe Region verwenden. In der folgenden Tabelle werden die verfügbaren Tarife beschrieben:

| Tarif | BESCHREIBUNG |
|------|-------------|
| **Basic** | Für Szenarien, in denen Sie nur die Verarbeitung von Nachrichten nutzen oder als Partner eines kleines Unternehmens fungieren möchten, das eine Handelspartnerbeziehung mit einem größeren Unternehmen eingegangen ist. <p><p>Ist von der SLA für Logic Apps abgedeckt. |
| **Standard** | Für Szenarien mit komplexeren B2B-Beziehungen und einer größeren Anzahl zu verwaltender Entitäten. <p><p>Ist von der SLA für Logic Apps abgedeckt. |
| **Free** | Nur für Erkundungs- und nicht für Produktionsszenarien. Bei diesem Tarif gelten Einschränkungen für Region, Durchsatz und Nutzung. Der Free-Tarif ist beispielsweise nur für öffentliche Regionen in Azure verfügbar, z. B. USA, Westen oder Asien, Südosten, aber nicht für [Azure China 21ViaNet](/azure/china/overview-operations) oder [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Hinweis**: Ist von der SLA für Logic Apps nicht abgedeckt. |
|||

Für diese Aufgabe können Sie das Azure-Portal, die [Azure CLI](/cli/azure/resource#az_resource_create) oder [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount) verwenden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Hauptsuchfeld in Azure `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Klicken Sie unter **Integrationskonten** auf **Erstellen**.

1. Geben Sie im Bereich **Integrationskonto erstellen** die folgenden Informationen zu Ihrem Integrationskonto an:

   | Eigenschaft | Erforderlich | Wert | Beschreibung |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Der Name Ihres Azure-Abonnements |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Der Name der [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), die zum Organisieren verwandter Ressourcen verwendet werden soll. Erstellen Sie für dieses Beispiel eine neue Ressourcengruppe namens `FabrikamIntegration-RG`. |
   | **Name des Integrationskontos** | Ja | <*integration-account-name*> | Der Name Ihres Integrationskontos, der nur Buchstaben, Ziffern, Bindestriche (`-`), Unterstriche (`_`), Klammern (`(`, `)`) und Punkte (`.`) enthalten kann. In diesem Beispiel wird `Fabrikam-Integration` verwendet. |
   | **Region** | Ja | <*Azure-Region*> | Die Azure-Region, in der die Metadaten zu Ihrem Integrationskonto gespeichert werden sollen. Wählen Sie entweder den gleichen Standort wie für die Logik-App aus, oder erstellen Sie Ihre Logik-Apps am gleichen Standort, an dem sich das Integrationskonto befindet. In diesem Beispiel verwenden wir `West US`. <p>**Hinweis**: Um ein Integrationskonto in einer [Integrationsdienstumgebung (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) zu erstellen, wählen Sie **Einer Integrationsdienstumgebung zuordnen** aus und dann Ihre ISE als Standort. Weitere Informationen finden Sie unter [Erstellen von Integrationskonten in einer ISE](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Tarif** | Ja | <*Preisstufe*> | Der Tarif für das Integrationskonto – diesen können Sie später ändern. Wählen Sie für dieses Beispiel die Option **Free** aus. Weitere Informationen finden Sie in der folgenden Dokumentation: <p>- [Logic Apps – Preismodell](logic-apps-pricing.md#integration-accounts) <br>- [Logic Apps – Grenzwerte und Konfiguration](logic-apps-limits-and-config.md#integration-account-limits) <br>- [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Aktivieren von Log Analytics** | Nein | Nicht markiert | Wählen Sie diese Option für dieses Beispiel nicht aus. |
   |||||

1. Wählen Sie **Überprüfen und erstellen** aus, wenn Sie fertig sind.

   Nach Abschluss der Bereitstellung öffnet Azure Ihr Integrationskonto.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Um die Erweiterung [az logic integration-account](/cli/azure/logic/integration-account) hinzuzufügen, verwenden Sie den Befehl [az extension add](/cli/azure/extension#az_extension_add):

   ```azurecli
   az extension add –-name logic
   ```

1. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden:

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Um die Integrationskonten für eine Ressourcengruppe aufzulisten, verwenden Sie den Befehl [az logic integration-account list](/cli/azure/logic/integration-account#az_logic_integration_account_list):

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Führen Sie zum Erstellen eines Integrationskontos den Befehl [az logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create) aus:

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Der Name Ihres Integrationskontos darf nur Buchstaben, Ziffern, Bindestriche (-), Unterstriche (_), Klammern ((, )) und Punkte (.) enthalten.

   Um ein bestimmtes Integrationskonto anzuzeigen, verwenden Sie den Befehl [az logic integration-account show](/cli/azure/logic/integration-account#az_logic_integration_account_show):

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Mit dem Befehl [az logic integration-account update](/cli/azure/logic/integration-account#az_logic_integration_account_update) können Sie die SKU oder den Tarif ändern:

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Weitere Informationen zu den Preisen finden Sie unter diesen Ressourcen:

   * [Logic Apps – Preismodell](logic-apps-pricing.md#integration-accounts)
   * [Logic Apps – Grenzwerte und Konfiguration](logic-apps-limits-and-config.md#integration-account-limits)
   * [Logik-Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/)

Um ein Integrationskonto mithilfe einer JSON-Datei zu importieren, verwenden Sie den Befehl [az logic integration-account import](/cli/azure/logic/integration-account#az_logic_integration_account_import):

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

---

<a name="link-account"></a>

## <a name="link-to-logic-app-consumption-resource-only"></a>Verknüpfen mit Logik-App (nur Verbrauchsressource)

Damit Ihr **Logik-App (Verbrauch)** -Workflow auf die B2B-Artefakte in Ihrem Integrationskonto zugreifen kann, müssen Sie Ihre Logik-App-Ressource zuerst mit Ihrem Integrationskonto verknüpfen. Sowohl die Logik-App als auch das Integrationskonto müssen dasselbe Azure-Abonnement und dieselbe Azure-Region verwenden. Um diese Aufgabe abzuschließen, können Sie das Azure-Portal verwenden. Wenn Sie Visual Studio verwenden und sich Ihre Logik-App in einem [Azure Resource Group-Projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) befindet, können Sie Ihre [Logik-App mithilfe von Visual Studio mit einem Integrationskonto verknüpfen](manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) eine vorhandene Logik-App, oder erstellen Sie eine neue.

1. Wählen Sie im Menü Ihrer Logik-App unter **Einstellungen** die Option **Workfloweinstellungen** aus. Öffnen Sie unter **Integrationskonto** die Liste **Wählen Sie ein Integrationskonto aus** aus, und wählen Sie dann das gewünschte Integrationskonto aus.

   ![Screenshot des Azure-Portals mit Integrationskontomenü mit geöffnetem Bereich „Workfloweinstellungen“ und geöffneter Liste „Integrationskonto auswählen“.](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Klicken Sie zum Fertigstellen der Verknüpfung auf **Speichern**.

   ![Screenshot des Bereichs „Workfloweinstellungen“ und ausgewählter Option „Speichern“.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Nachdem Ihr Integrationskonto erfolgreich verknüpft wurde, wird in Azure eine Bestätigungsmeldung angezeigt.

   ![Screenshot der Azure-Bestätigungsmeldung.](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Ihre Logik-App kann jetzt die Artefakte in Ihrem Integrationskonto sowie die B2B-Connectors (z.B. XML-Überprüfung und Flatfilecodierung- bzw. decodierung) verwenden.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Ändern des Tarifs

Um die [Grenzwerte](logic-apps-limits-and-config.md#integration-account-limits) eines Integrationskontos zu erhöhen, können Sie [ein Upgrade auf einen höheren Tarif durchführen](#upgrade-pricing-tier), sofern verfügbar. Sie können beispielsweise ein Upgrade vom Free-Tarif auf den Tarif „Basic“ oder „Standard“ durchführen. Sie können auch [ein Downgrade auf einen niedrigeren Tarif durchführen](#downgrade-pricing-tier), sofern verfügbar. Weitere Informationen zu Preisen finden Sie in der folgenden Dokumentation:

* [Logik-Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps – Preismodell](logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Tarifupgrade

Sie können für diese Änderung das Azure-Portal oder die Azure-Befehlszeilenschnittstelle verwenden.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Hauptsuchfeld in Azure `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

   Azure zeigt alle Integrationskonten in Ihren Azure-Abonnements an.

1. Wählen Sie unter **Integrationskonten** das zu verschiebende Integrationskonto aus. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus.

   ![Screenshot des Azure-Portals mit dem Integrationskontomenü und ausgewählter Option „Übersicht“.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Wählen Sie im Bereich „Übersicht“ die Option **Tarif aktualisieren** aus – daraufhin werden alle verfügbaren höheren Tarife aufgelistet. Wenn Sie einen Tarif auswählen, tritt die Änderung sofort in Kraft.

   ![Screenshot des Integrationskontobereichs „Übersicht“ mit ausgewählter Option „Tarif aktualisieren“.](media/logic-apps-enterprise-integration-create-integration-account/upgrade-pricing-tier.png)

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. [Installieren Sie die Azure CLI](/cli/azure/get-started-with-azure-cli), wenn Sie dies noch nicht getan haben.

1. Öffnen Sie die [Azure Cloud Shell](../cloud-shell/overview.md)-Umgebung im Azure-Portal.

   ![Screenshot der Azure-Portal-Symbolleiste mit ausgewählter Option „Cloud Shell“.](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Geben Sie an der Eingabeaufforderung den [Befehl **az resource**](/cli/azure/resource#az_resource_update) ein, und legen Sie `skuName` auf den gewünschten höheren Tarif fest.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Wenn Sie z.B. derzeit den Basic-Tarif nutzen, können Sie `skuName` auf `Standard` festlegen:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Tarifdowngrade

Verwenden Sie für diese Änderung die [Azure CLI](/cli/azure/get-started-with-azure-cli).

1. [Installieren Sie die Azure CLI](/cli/azure/get-started-with-azure-cli), wenn Sie dies noch nicht getan haben.

1. Öffnen Sie die [Azure Cloud Shell](../cloud-shell/overview.md)-Umgebung im Azure-Portal.

   ![Screenshot der Azure-Portal-Symbolleiste mit ausgewählter Option „Cloud Shell“.](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Geben Sie an der Eingabeaufforderung den [Befehl **az resource**](/cli/azure/resource#az_resource_update) ein, und legen Sie `skuName` auf den gewünschten niedrigeren Tarif fest.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Wenn Sie z.B. derzeit den Standard-Tarif nutzen, können Sie `skuName` auf `Basic` festlegen:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Aufheben der Verknüpfung mit der Logik-App

Wenn Sie Ihre Logik-App mit einem anderen Integrationskonto verknüpfen oder kein Integrationskonto mehr mit Ihrer Logik-App verwenden möchten, löschen Sie die Verknüpfung über den Azure-Ressourcen-Explorer.

1. Öffnen Sie ein Browserfenster, und wechseln Sie zu [Azure-Ressourcen-Explorer (https://resources.azure.com)](https://resources.azure.com). Melden Sie sich mit denselben Azure-Kontoanmeldeinformationen an.

   ![Screenshot eines Webbrowsers mit Azure-Ressourcen-Explorer.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Geben Sie im Suchfeld den Namen Ihrer Logik-App ein, um sie zu suchen und auszuwählen.

   ![Screenshot des Explorersuchfelds, das den Namen Ihrer Logik-App enthält.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Wählen Sie in der Titelleiste des Explorers **Lesen/Schreiben** aus.

   ![Screenshot der Titelleiste mit ausgewählter Option „Lesen/Schreiben“.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Wählen Sie auf der Registerkarte **Daten** die Option **Bearbeiten** aus.

   ![Screenshot der Registerkarte „Daten“ mit ausgewählter Option „Bearbeiten“.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Suchen Sie im Editor das `integrationAccount`-Objekt, und löschen Sie diese Eigenschaft, die im folgenden Format angegeben ist:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Beispiel:

   ![Screenshot der zeigt, wie Sie das Objekt „integrationAccount“ (Integrationskonto) suchen.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Klicken Sie auf der Registerkarte **Daten** auf **PUT**, um die Änderungen zu speichern.

   ![Screenshot der Registerkarte „Daten“ mit ausgewählter Option „Put“ (Ablegen).](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Öffnen Sie Ihre Logik-App im Azure-Portal. Überprüfen Sie in Ihrem Logik-App-Menü unter **Workflow-Einstellungen**, ob die Eigenschaft **Integrationskonto** nun leer ist.

   ![Screenshot des Azure-Portals mit dem Logik-App-Menü und ausgewählter Option „Workfloweinstellungen“.](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Verschieben eines Integrationskontos

Sie können Ihr Integrationskonto in eine andere Azure-Ressourcengruppe bzw. ein anderes Azure-Abonnement verschieben. Wenn Sie Ressourcen verschieben, erstellt Azure neue Ressourcen-IDs. Stellen Sie daher sicher, dass Sie die neuen IDs verwenden, und aktualisieren Sie alle Skripts und Tools, die den verschobenen Ressourcen zugeordnet sind. Wenn Sie das Abonnement ändern möchten, müssen Sie auch eine vorhandene oder neue Ressourcengruppe angeben.

Für diese Aufgabe können Sie das Azure-Portal verwenden, indem Sie die Schritte in diesem Abschnitt ausführen. Alternativ dazu können Sie auch die [Azure CLI](/cli/azure/resource#az_resource_move) verwenden.

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Hauptsuchfeld in Azure `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

   Azure zeigt alle Integrationskonten in Ihren Azure-Abonnements an.

1. Wählen Sie unter **Integrationskonten** das zu verschiebende Integrationskonto aus. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus.

1. Wählen Sie im Bereich „Übersicht“ neben **Ressourcengruppe** oder neben **Abonnementname** den Befehl **Ändern** aus.

   ![Screenshot des Azure-Portals mit dem Bereich „Übersicht“ mit ausgewählter Option „Ändern“ neben „Ressourcengruppe“ oder „Abonnementname“.](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Wählen Sie alle zugehörigen Ressourcen aus, die Sie ebenfalls verschieben möchten.

1. Je nach Auswahl führen Sie die folgenden Schritte aus, um die Ressourcengruppe oder das Abonnement zu ändern:

   * Ressourcengruppe: Wählen Sie aus der Liste **Ressourcengruppe** die Zielressourcengruppe aus. Oder wählen Sie **Neue Ressourcengruppe erstellen** aus, um eine andere Ressourcengruppe zu erstellen.

   * Abonnement: Wählen Sie aus der Liste **Abonnement** das Zielabonnement aus. Wählen Sie aus der Liste **Ressourcengruppe** die Zielressourcengruppe aus. Oder wählen Sie **Neue Ressourcengruppe erstellen** aus, um eine andere Ressourcengruppe zu erstellen.

1. Um zu bestätigen, dass Ihnen bekannt ist, dass alle Skripts oder Tools, die den verschobenen Ressourcen zugeordnet sind, erst dann wieder funktionieren, wenn Sie sie mit den neuen Ressourcen-IDs aktualisieren, aktivieren Sie das Kontrollkästchen für die Bestätigung, und klicken Sie dann auf **OK**.

1. Stellen Sie im Anschluss sicher, dass Sie alle Skripts mit den neuen Ressourcen-IDs für Ihre verschobenen Ressourcen aktualisieren.  

## <a name="delete-integration-account"></a>Löschen eines Integrationskontos

Für diese Aufgabe können Sie das Azure-Portal verwenden, indem Sie die Schritte in diesem Abschnitt ausführen. Alternativ dazu können Sie auch die [Azure CLI](/cli/azure/resource#az_resource_delete) oder [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount) verwenden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Hauptsuchfeld in Azure `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

   Azure zeigt alle Integrationskonten in Ihren Azure-Abonnements an.

1. Wählen Sie unter **Integrationskonten** das zu löschende Integrationskonto aus. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus.

   ![Screenshot des Azure-Portals mit der Liste „Integrationskonten“ und dem Integrationskontomenü mit ausgewählter Option „Übersicht“.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Wählen Sie im Bereich „Übersicht“ die Option **Löschen** aus.

   ![Screenshot des Bereichs „Übersicht“ mit ausgewählter Option „Löschen“.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Um zu bestätigen, dass Sie Ihr Integrationskonto löschen möchten, klicken Sie auf **Ja**.

   ![Screenshot des Bestätigungsfelds und ausgewählter Option „Ja“.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

<a name="delete-account-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Löschen eines Integrationskontos den Befehl [az logic integration-account delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete) aus:

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

---

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Handelspartnern in Ihrem Integrationskonto](logic-apps-enterprise-integration-partners.md)
* [Erstellen von Vereinbarungen zwischen Partnern in Ihrem Integrationskonto](logic-apps-enterprise-integration-agreements.md)
