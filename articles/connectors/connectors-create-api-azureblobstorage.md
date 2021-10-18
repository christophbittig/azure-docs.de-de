---
title: Herstellen einer Verbindung mit Azure Blob Storage
description: Informationen zum Erstellen und Verwalten von Blobs in Azure-Speicherkonten mithilfe von Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/11/2021
tags: connectors
ms.openlocfilehash: cc56c079173fad1509d9da9cf1d435675b1918f7
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808989"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Erstellen und Verwalten von Blobs in Azure Blob Storage mithilfe von Azure Logic Apps

Aus Ihrem Workflow in Azure Logic Apps können Sie mit dem [Azure Blob Storage-Connector ](/connectors/azureblobconnector/) auf Dateien zugreifen, die als Blobs in Ihrem Azure-Speicherkonto gespeichert sind, und diese verwalten. Dieser Connector bietet Trigger und Aktionen, die Ihr Workflow für Blobvorgänge verwenden kann. Anschließend können Sie Aufgaben zum Verwalten von Dateien in Ihrem Speicherkonto automatisieren. Zu den [Connectoraktionen](/connectors/azureblobconnector/#actions) zählen beispielsweise das Überprüfen, Löschen, Lesen und Hochladen von Blobs. Der [verfügbare Trigger](/connectors/azureblobconnector/#triggers) wird ausgelöst, wenn ein Blob hinzugefügt oder geändert wird.

Sie können sich mit Blob Storage sowohl über **Logik-App-Ressourcentypen mit Verbrauchs**- und **Standardtarif** verbinden. Sie können den Connector mit Logik-App-Workflows in mehrinstanzenfähigen Azure Logic Apps, in Azure Logic Apps mit einem Mandanten und in der Integrationsdienstumgebung (Integration Service Environment, ISE) verwenden. Mit **Logic App (Standard)** stellt Blob Storage integrierte Vorgänge *und* Vorgänge für verwaltete Connectors bereit.

> [!IMPORTANT]
> Ein Logik-App-Workflow kann nicht direkt auf ein Speicherkonto hinter einer Firewall zugreifen, wenn sich beide in derselben Region befinden. Um dieses Problem zu umgehen, können Sie Ihre Logik-Apps und Ihr Speicherkonto in verschiedenen Regionen einrichten. Weitere Informationen zum Aktivieren des Zugriffs von Azure Logic Apps auf Speicherkonten hinter Firewalls finden Sie weiter unten in diesem Thema im Abschnitt [Zugriff auf Speicherkonten hinter Firewalls](#access-storage-accounts-behind-firewalls).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Ein [Azure-Speicherkonto und -Speichercontainer](../storage/blobs/storage-quickstart-blobs-portal.md)

- Ein Logik-App-Workflow, in dem Sie auf Ihr Blob Storage-Konto zugreifen möchten. Um Ihren Workflow mit einem Blob Storage-Trigger starten zu können, benötigen Sie einen [leeren Logik App-Workflow](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Grenzwerte

- Für Logik-App-Workflows, die in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ausgeführt werden, verwendet die mit ISE bezeichnete Version dieses Connectors stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

- Standardmäßig können Blob Storage-Aktionen Dateien mit einer Größe von *bis zu 50 MB* lesen oder schreiben. Zum Verarbeiten von Dateien mit einer Größe von mehr als 50 MB bis maximal 1.024 MB unterstützen Azure Blob Storage-Aktionen die [Nachrichtenblockerstellung](../logic-apps/logic-apps-handle-large-messages.md). Für die Aktion [**Blobinhalt abrufen**](/connectors/azureblobconnector/#get-blob-content) wird implizit die Blockerstellung verwendet.

- Blob Storage-Trigger unterstützen keine Blockerstellung. Trigger wählen beim Anfordern von Dateiinhalten nur Dateien aus, die 50 MB oder kleiner sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 50 MB sind:

  - Verwenden Sie einen Blob Storage-Trigger, der Dateieigenschaften zurückgibt, z. B. [**Wenn ein Blob hinzugefügt oder geändert wird (nur Eigenschaften)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)).

  - Lassen Sie auf den Trigger die Blob Storage-Aktion [**Blobinhalt abrufen**](/connectors/azureblobconnector/#get-blob-content) folgen, die die vollständige Datei liest und die Blockerstellung implizit verwendet.

## <a name="connector-reference"></a>Connector-Referenz

Technische Informationen zu den Triggern, Aktionen und Grenzwerten dieses Connectors finden Sie auf der [Referenzseite zum Connector](/connectors/azureblobconnector/). Wenn Sie den Blob Storage-Connector nicht verwenden möchten, können Sie [stattdessen HTTP-Trigger oder -Aktionen zusammen mit einer verwalteten Identität für Blobvorgänge verwenden](#access-blob-storage-with-managed-identities).

## <a name="add-blob-storage-trigger"></a>Hinzufügen eines Blob Storage-Triggers

In Azure Logic Apps muss jeder Workflow mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird.

Dieser Connector hat einen verfügbaren Trigger, der entweder [**Wenn ein Blob in Azure Storage hinzugefügt oder geändert wird** oder **Wenn ein Blob hinzugefügt oder geändert wird (nur Eigenschaften)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)) heißt. Der Trigger wird ausgelöst, wenn Eigenschaften eines Blobs in Ihrem Speichercontainer hinzugefügt oder aktualisiert werden. Jedes Mal erstellt die Azure Logic Apps-Engine eine Logik-App-Instanz und beginnt mit der Ausführung Ihres Workflows.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Um einen Azure Blob Storage-Trigger einem Logik App-Workflow in mehrinstanzenfähigen Azure Logic Apps hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Geben Sie `Azure blob` als Filter in das Suchfeld des Designers ein. Wählen Sie in der Liste „Trigger“ den Trigger namens **Wenn ein Blob hinzugefügt oder geändert wird (nur Eigenschaften)** aus.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="Screenshot: Azure-Portal und Workflow-Designer mit ausgewählter Logik-App „Verbrauch“ und dem ausgewählten Trigger „Wenn ein Blob hinzugefügt oder geändert wird (nur Eigenschaften)“.":::

1. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Azure Blob Storage-Verbindung erstellen](#connect-blob-storage-account).

1. Geben Sie die erforderlichen Informationen für den Trigger ein.

   1. Wählen Sie für den Eigenschaftswert **Container** das Ordnersymbol aus, um nach Ihrem Blobspeichercontainer zu suchen. Oder geben Sie den Pfad manuell ein.

   1. Konfigurieren Sie bei Bedarf weitere Triggereinstellungen.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="Screenshot: Azure Blob-Trigger mit Parameterkonfiguration.":::

1. Fügen Sie Ihrem Workflow eine oder mehrere Aktionen hinzu.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus, um Ihre Änderungen zu speichern.

### <a name="standard"></a>[Standard](#tab/standard)

Um einen Azure Blob-Trigger einem Logik-App-Workflow in Azure Logic Apps mit einem Mandanten hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie im Designer **Vorgang auswählen** aus. Wählen Sie im Bereich **Trigger hinzufügen**, der geöffnet wird, unter dem Suchfeld **Vorgang auswählen** entweder **Integriert** aus, um nach dem *integrierten* **Azure Blob**-Trigger zu suchen, oder wählen Sie **Azure** aus, um nach dem Trigger für den **verwalteten** *Azure Blob Storage-Connector* zu suchen.

1. Geben Sie im Suchfeld `Azure blob`ein. Wählen Sie in der Liste „Trigger“ den Trigger **Wenn ein Blob in Azure Storage hinzugefügt oder geändert wird** aus.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Screenshot: Azure-Portal, Workflow-Designer, Logik-App-Standardworkflow und ausgewählter Azure Blob-Trigger.":::

1. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Azure Blob Storage-Verbindung erstellen](#connect-blob-storage-account).

1. Geben Sie die erforderlichen Informationen für den Trigger ein. Fügen Sie auf der Registerkarte **Parameter** den **Blobpfad** für das Blob hinzu, das Sie überwachen möchten.

   1. Um Ihren Blobpfad zu finden, öffnen Sie Ihr Speicherkonto im Azure-Portal.

   1. Wählen Sie im Navigationsmenü unter **Datenspeicher** die Option **Container** aus.

   1. Wählen Sie Ihren Blobcontainer aus. Wählen Sie im Navigationsmenü des Containers unter **Einstellungen** die Option **Eigenschaften** aus.

   1. Kopieren Sie den Wert **URL**, der den Pfad zum Blob angibt. Der Pfad ähnelt `https://<storage-container-name>/<folder-name>/{name}`. Geben Sie stattdessen ihren Container- und Ordnernamen an, behalten Sie jedoch die Literalzeichenfolge `{name}` bei.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Screenshot: Workflow-Designer für einen Logik-App-Standardworkflow mit Storage-Trigger und Parameterkonfiguration.":::

1. Setzen Sie die Erstellung Ihres Workflows dann fort, indem Sie mindestens eine Aktion hinzufügen.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus, um Ihre Änderungen zu speichern.

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Hinzufügen einer Blob Storage-Aktion

In Azure Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, der einem Trigger oder einer anderen Aktion folgt.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Um eine Azure Blob Storage-Aktion einem Logik App-Workflow in mehrinstanzenfähigen Azure Logic Apps hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie Ihren Workflow im [Azure-Portal](https://portal.azure.com) im Designer.

1. Wenn Ihr Workflow leer ist, fügen Sie einen beliebigen Trigger hinzu.

   Dieses Beispiel beginnt mit dem Trigger [**Wiederholung**](connectors-native-recurrence.md).

1. Wählen Sie unter dem Trigger oder der Aktion, dem bzw. der Sie die Blob Storage-Aktion hinzufügen möchten, die Option **Neuer Schritt** oder **Aktion hinzufügen** aus, falls Sie sich zwischen Schritten befinden.

1. Geben Sie in das Suchfeld des Designers `Azure blob` ein. Wählen Sie die gewünschte Blob Storage-Aktion aus.

   In diesem Beispiel wird die Aktion namens **Blobinhalt abrufen** verwendet.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="Screenshot: Logik-App mit Verbrauchstarif im Designer mit einer Liste der verfügbaren Blob Storage-Aktionen.":::

1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, [verbinden Sie sich mit Ihrem Blob Storage-Konto](#connect-blob-storage-account).

1. Geben Sie die erforderlichen Informationen für die Aktion ein.

    1. Wählen Sie für den Eigenschaftswert **Blob** das Ordnersymbol aus, um nach Ihrem Blobspeichercontainer zu suchen. Oder geben Sie den Pfad manuell ein.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="Screenshot: Logik-App mit Verbrauchstarif im Designer mit der Konfiguration einer Blob Storage-Aktion.":::

    1. Konfigurieren Sie bei Bedarf weitere Aktionseinstellungen.

### <a name="standard"></a>[Standard](#tab/standard)

Um einer Azure Blob-Aktion einem Logik-App-Workflow in Azure Logic Apps mit einem Mandanten hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie Ihren Workflow im [Azure-Portal](https://portal.azure.com) im Designer.

1. Wenn Ihr Workflow leer ist, fügen Sie einen beliebigen Trigger hinzu.

   Dieses Beispiel beginnt mit dem Trigger [**Wiederholung**](connectors-native-recurrence.md).

1. Wählen Sie unter dem Trigger oder der Aktion, dem bzw. der Sie die Blob Storage-Aktion hinzufügen möchten, die Option **Neuen Schritt hinzufügen** ( **+** ) > **Aktion hinzufügen** aus.

1. Stellen Sie im Designer sicher, dass **Vorgang hinzufügen** ausgewählt ist. Wählen Sie im Bereich **Aktion hinzufügen**, der geöffnet wird, unter dem Suchfeld **Vorgang auswählen** entweder **Integriert** aus, um nach den *integrierten* **Azure Blob**-Aktionen zu suchen, oder wählen Sie **Azure** aus, um nach Aktionen für den **verwalteten** *Azure Blob Storage-Connector* zu suchen.

1. Geben Sie im Suchfeld `Azure blob`ein. Wählen Sie die gewünschte Azure Blob-Aktion aus.

   In diesem Beispiel wird die Aktion namens **Liest Blobinhalt aus Azure Storage** verwendet, die nur den Blobinhalt liest. Um den Inhalt später anzuzeigen, fügen Sie eine andere Aktion hinzu, die eine Datei mit dem Blobinhalt mithilfe eines anderen Connectors erstellt. Sie können beispielsweise eine OneDrive-Aktion hinzufügen, die basierend auf dem Blobinhalt eine Datei erstellt.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Screenshot: Azure-Portal und Workflow-Designer mit einem Logik-App-Standardworkflow und den verfügbaren Azure Blob Storage-Aktionen.":::

1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, [verbinden Sie sich mit Ihrem Blob Storage-Konto](#connect-blob-storage-account).

1. Geben Sie die erforderlichen Informationen für die Aktion ein.

    1. Geben Sie als **Containernamen** den Pfad des gewünschten Blobspeichercontainers ein.

    1. Geben Sie für den Eigenschaftswert **Blobname** den Pfad für das zu verwendende Blob ein.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Screenshot: Logik-App mit Standardtarif im Designer mit Auswahl des Blob Storage-Triggers.":::

    1. Konfigurieren Sie bei Bedarf weitere Aktionseinstellungen.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Testen Sie Ihre Logik-App, um sicherzustellen, dass Ihr ausgewählter Container ein Blob enthält.

---

<a name="connect-blob-storage-account"></a>

## <a name="connect-to-blob-storage-account"></a>Herstellen einer Verbindung mit dem Blob Storage-Konto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Bevor Sie Ihren [Azure Blob Storage-Trigger](#add-blob-storage-trigger) oder Ihre [Azure Blob Storage-Aktion](#add-blob-storage-action) konfigurieren können, müssen Sie eine Verbindung mit einem Speicherkonto herstellen. Eine Verbindung erfordert die folgenden Eigenschaften:

| Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
|----------|----------|-------|-------------|
| **Verbindungsname** | Yes | <*connection-name*> | Der Name, der für Ihre Verbindung verwendet werden soll. |
| **Speicherkonto** | Ja | <*storage-account*> | Wählen Sie Ihr Speicherkonto in der Liste aus, oder geben Sie eine Zeichenfolge an. <p>**Hinweis**: Um die Verbindungszeichenfolge zu ermitteln, wechseln Sie zur Seite des Speicherkontos. Wählen Sie im Navigationsmenü unter **Sicherheit + Netzwerkbetrieb** die Option **Zugriffsschlüssel** > **Schlüssel anzeigen** aus. Kopieren Sie einen der verfügbaren Werte für Verbindungszeichenfolgen. |
|||||

Um eine Azure Blob Storage-Verbindung aus einem Logik App-Workflow in mehrinstanzenfähigen Azure Logic Apps zu erstellen, führen Sie die folgenden Schritte aus:

1. Geben Sie als **Verbindungsnamen** einen Namen für Ihre Verbindung ein.

1. Wählen Sie für **Speicherkonto** das Speicherkonto aus, in dem Ihr Blobcontainer vorhanden ist. Oder wählen Sie **Verbindungsinformationen manuell eingeben** aus, um den Pfad selbst einzugeben.

1. Wählen Sie **Erstellen** aus, um die Verbindung herzustellen.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="Screenshot: Workflow-Designer mit einem Verbrauchs-Logik-App-Workflow und einer Aufforderung zum Hinzufügen einer neuen Verbindung für den Schritt „Azure Blob Storage“.":::

> [!NOTE]
> Wenn Sie nach dem Erstellen der Verbindung über eine andere vorhandene Azure Blob Storage-Verbindung verfügen, die Sie stattdessen verwenden möchten, wählen Sie **Verbindung ändern** im Editor für Trigger- oder Aktionsdetails aus.

Wenn Sie Probleme haben, sich mit Ihrem Speicherkonto zu verbinden, finden Sie weitere Informationen unter [Zugreifen auf Speicherkonten hinter Firewalls](#access-storage-accounts-behind-firewalls).

### <a name="standard"></a>[Standard](#tab/standard)

Bevor Sie Ihren [Azure Blob-Trigger](#add-blob-storage-trigger) oder Ihre [Azure Blob-Aktion](#add-blob-storage-action) konfigurieren können, müssen Sie eine Verbindung mit einem Speicherkonto herstellen. Eine Verbindung erfordert die folgenden Eigenschaften:

| Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
|----------|----------|-------|-------------|
| **Verbindungsname** | Yes | <*connection-name*> | Der Name, der für Ihre Verbindung verwendet werden soll. |
| **Verbindungszeichenfolge für Azure Blob Storage** | Ja | <*storage-account*> | Wählen Sie Ihr Speicherkonto in der Liste aus, oder geben Sie eine Zeichenfolge an. <p>**Hinweis**: Um die Verbindungszeichenfolge zu ermitteln, wechseln Sie zur Seite des Speicherkontos. Wählen Sie im Navigationsmenü unter **Sicherheit + Netzwerkbetrieb** die Option **Zugriffsschlüssel** > **Schlüssel anzeigen** aus. Kopieren Sie einen der verfügbaren Werte für Verbindungszeichenfolgen. |
|||||

Um eine Azure Blob Storage-Verbindung aus einem Logik App-Workflow in Azure Logic Apps mit einem Mandanten zu erstellen, führen Sie die folgenden Schritte aus:

1. Geben Sie für **Verbindungsname** einen Namen ein.

1. Geben Sie für **Verbindungszeichenfolge für Azure Blob Storage** die Verbindungszeichenfolge für das gewünschte Speicherkonto ein.

1. Wählen Sie **Erstellen** aus, um die Verbindung herzustellen.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Screenshot: Workflow-Designer mit einem Logik-App-Standardworkflow und einer Aufforderung zum Hinzufügen einer neuen Verbindung für den Schritt „Azure Blob Storage“.":::

> [!NOTE]
> Wenn Sie nach dem Erstellen der Verbindung über eine andere vorhandene Azure Blob Storage-Verbindung verfügen, die Sie stattdessen verwenden möchten, wählen Sie **Verbindung ändern** im Editor für Trigger- oder Aktionsdetails aus.

Wenn Sie Probleme haben, sich mit Ihrem Speicherkonto zu verbinden, finden Sie weitere Informationen unter [Zugreifen auf Speicherkonten hinter Firewalls](#access-storage-accounts-behind-firewalls).

---

## <a name="access-storage-accounts-behind-firewalls"></a>Zugreifen auf Speicherkonten hinter Firewalls

Sie können ein Azure-Speicherkonto mit Netzwerksicherheit versehen, indem Sie den [Zugriff mit einer Firewall und Firewallregeln einschränken](../storage/common/storage-network-security.md). Dieses Setup stellt jedoch eine Herausforderung für Azure und andere Microsoft-Dienste dar, die Zugriff auf das Speicherkonto benötigen. Die lokale Kommunikation im Rechenzentrum abstrahiert die internen IP-Adressen, sodass Sie keine Firewallregeln mit IP-Einschränkungen einrichten können.

So greifen Sie mithilfe des Blob Storage-Connectors hinter Firewalls auf Speicherkonten zu

- [Zugreifen auf Speicherkonten in anderen Regionen](#access-storage-accounts-in-other-regions)
- [Zugreifen auf Speicherkonten über ein vertrauenswürdiges virtuelles Netzwerk](#access-storage-accounts-through-trusted-virtual-network)

Andere Lösungen für den Zugriff auf Speicherkonten hinter Firewalls:

- [Zugreifen auf Speicherkonten als vertrauenswürdiger Dienst mit verwalteten Identitäten](#access-blob-storage-with-managed-identities)
- [Zugreifen auf Speicherkonten über Azure API Management](#access-storage-accounts-through-azure-api-management)

### <a name="access-storage-accounts-in-other-regions"></a>Zugreifen auf Speicherkonten in anderen Regionen

Logik-App-Workflows können nicht direkt auf Speicherkonten hinter Firewalls zugreifen, wenn sich beide in derselben Region befinden. Als Abhilfe können Sie Ihre Logik-App-Ressourceen in einer anderen Region als der Ihres Speicherkontos einrichten. Gewähren Sie anschließend Zugriff auf die [ausgehenden IP-Adressen für die verwalteten Connectors in Ihrer Region](/connectors/common/outbound-ip-addresses#azure-logic-apps).

> [!NOTE]
> Diese Lösung gilt nicht für den Azure Table Storage-Connector und den Azure Queue Storage-Connector. Um auf Ihren Table Storage oder Queue Storage zuzugreifen, können Sie stattdessen [integrierte HTTP-Trigger und -Aktionen verwenden](../logic-apps/logic-apps-http-endpoint.md).

Um Sie Ihre ausgehenden IP-Adressen der Firewall des Speicherkontos hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Beachten Sie die [ausgehenden IP-Adressen des verwalteten Connectors](/connectors/common/outbound-ip-addresses#azure-logic-apps) für die Region Ihrer Logik-App-Ressource.

1. Suchen Sie nach Ihrer Speicherkontoressource im [Azure-Portal](https://portal.azure.com), und öffnen Sie sie.

1. Wählen Sie im Navigationsmenü des Speicherkontos unter **Sicherheit + Netzwerkbetrieb** die Option **Netzwerk** aus.

   1. Wählen Sie unter **Zugriff zulassen von** die Option **Ausgewählten Netzwerken** aus, um die relevanten Einstellungen anzuzeigen.

   1. Fügen Sie unter **Firewall** die IP-Adressen oder -Bereiche hinzu, die Zugriff benötigen. Wenn Sie auf Ihrem Computer auf das Speicherkonto zugreifen müssen, wählen Sie die Option **Client-IP-Adresse hinzufügen** aus.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Screenshot der Seite „Netzwerk“ des Blob Storage-Kontos in Azure-Portal mit Firewalleinstellungen zum Hinzufügen von IP-Adressen und -Bereichen zur Positivliste.":::

   1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>Zugreifen auf Speicherkonten über ein vertrauenswürdiges virtuelles Netzwerk

Sie können das Speicherkonto in einem virtuellen Azure-Netzwerk platzieren, das Sie verwalten, und dieses virtuelle Netzwerk dann der Liste vertrauenswürdiger virtueller Netzwerke hinzufügen. Damit Ihre Logik-App über ein [vertrauenswürdiges virtuelles Netzwerk](../virtual-network/virtual-networks-overview.md) auf das Speicherkonto zugreifen kann, müssen Sie diese Logik-App in einer [Integration Service Environment (ISE, Integrationsdienstumgebung)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) bereitstellen, die eine Verbindung mit Ressourcen in einem virtuellen Netzwerk herstellen kann. Anschließend können Sie die Subnetze in dieser ISE der Liste vertrauenswürdiger Netzwerke hinzufügen. Azure Storage-Connectors wie der Blob Storage-Connector können direkt auf den Speichercontainer zugreifen. Dieses Setup ist mit der Verwendung der Dienstendpunkte aus einer ISE heraus identisch.

### <a name="access-storage-accounts-through-azure-api-management"></a>Zugreifen auf Speicherkonten über Azure API Management

Wenn Sie für [API Management](../api-management/api-management-key-concepts.md) einen Dedicated-Tarif verwenden, können Sie die Speicher-API als Front-End verwenden, indem Sie API Management verwenden und dessen IP-Adressen das Passieren der Firewall gestatten. Im Grunde fügen Sie das virtuelle Azure-Netzwerk, das von API Management verwendet wird, der Firewalleinstellung des Speicherkontos hinzu. Sie können dann entweder die API Management-Aktion oder die HTTP-Aktion verwenden, um die Azure Storage-APIs aufzurufen. Wenn Sie diese Option auswählen, müssen Sie den Authentifizierungsprozess jedoch selbst handhaben. Weitere Informationen finden Sie unter [Einfache Unternehmensintegrationsarchitektur](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="access-blob-storage-with-managed-identities"></a>Azure Blob Storage mit verwalteten Identitäten

Wenn Sie auf Blob Storage zugreifen möchten, ohne diesen Connector zu verwenden, können Sie stattdessen [verwaltete Identitäten für die Authentifizierung](../active-directory/managed-identities-azure-resources/overview.md) verwenden. Sie können eine Ausnahme erstellen, die vertrauenswürdigen Diensten von Microsoft, wie z. B. einer verwalteten Identität, den Zugriff auf Ihr Speicherkonto durch eine Firewall ermöglicht.

So verwenden Sie verwaltete Identitäten in Ihrer Logik-App für den Zugriff auf Blob Storage:

1. [Konfigurieren Sie den Zugriff auf Ihr Speicherkonto](#configure-storage-account-access).

1. [Erstellen Sie eine Rollenzuweisung für Ihre Logik-App](#create-role-assignment-logic-app).

1. [Aktivieren Sie Unterstützung für die verwaltete Identität in Ihrer Logik-App](#enable-managed-identity-support).

> [!NOTE]
> Einschränkungen dieser Lösung:
>
> - Sie können *nur* den HTTP-Trigger oder die HTTP-Aktion in Ihrem Workflow verwenden.
> - Sie müssen eine verwaltete Identität einrichten, um ihre Speicherkontoverbindung zu authentifizieren.
> - Sie können keine integrierten Blob Storage-Vorgänge verwenden, wenn Sie sich mit einer verwalteten Identität authentifizieren.
> - Für Logik-Apps in einer Umgebung mit nur einem Mandanten wird nur die vom System zugewiesene verwaltete Identität zur Verfügung gestellt und unterstützt, nicht die vom Benutzer zugewiesene verwaltete Identität.

### <a name="configure-storage-account-access"></a>Konfigurieren des Zugriffs auf das Speicherkonto

Um die Unterstützung für Ausnahmen und verwaltete Identitäten einzurichten, konfigurieren Sie zunächst den entsprechenden Zugriff auf Ihr Speicherkonto:

1. Suchen Sie nach Ihrer Speicherkontoressource im [Azure-Portal](https://portal.azure.com), und öffnen Sie sie.

1. Wählen Sie im Navigationsmenü des Speicherkontos unter **Sicherheit + Netzwerkbetrieb** die Option **Netzwerk** aus.

   1. Wählen Sie unter **Zugriff zulassen von** die Option **Ausgewählten Netzwerken** aus, um die relevanten Einstellungen anzuzeigen.

   1. Wenn Sie auf Ihrem Computer auf das Speicherkonto zugreifen müssen, wählen Sie unter **Firewall** die Option **Client-IP-Adresse hinzufügen** aus.

   1. Wählen Sie unter **Ausnahmen** die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aus.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Screenshot: Azure-Portal und Bereich „Blob Storage-Kontonetzwerk“ mit Einstellungen für „Zulassen“.":::

   1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

> [!NOTE]
> Wenn Sie die Fehlermeldung **403 Forbidden** erhalten, sobald Sie versuchen, sich in Ihrem Workflow mit dem Speicherkonto zu verbinden, gibt es mehrere mögliche Ursachen. Probieren Sie die folgende Lösung aus, bevor Sie mit zusätzlichen Schritten fortfahren. Deaktivieren Sie die Einstellung **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**, und speichern Sie Ihre Änderungen. Reaktivieren Sie dann die Einstellung, und speichern Sie die Änderungen erneut.

<a name="create-role-assignment-logic-app"></a>

### <a name="create-role-assignment-for-logic-app"></a>Erstellen einer Rollenzuweisung für Ihre Logik-App

Aktivieren Sie dann [die Unterstützung für verwaltete Identitäten](../logic-apps/create-managed-service-identity.md) in Ihrer Logik-App-Ressource.

Die folgenden Schritte sind für Verbrauchs-Logik-Apps in mehrinstanzenfähigen Umgebungen und Standard-Logik-Apps in Umgebungen mit einem Mandanten identisch.

1. Öffnen Sie Ihre Logik-App-Ressource im [Azure-Portal](https://portal.azure.com).

1. Wählen Sie im Navigationsmenü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Identität** aus.

1. Legen Sie im Bereich **Systemseitig zugewiesen** den **Status** auf **Ein** fest. Diese Einstellung ist möglicherweise bereits aktiviert. Wählen Sie unter **Berechtigungen** die Option **Azure-Rollenzuweisungen** aus.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Screenshot: Azure-Portal und Ressourcenmenü der Logik-App mit dem Einstellungsbereich „Identität“ und der Schaltfläche „Berechtigungen für Azure-Rollenzuweisung“.":::

1. Wählen Sie im Bereich **Azure-Rollenzuweisungen** die Option **Rollenzuweisung hinzufügen** aus.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="Screenshot: Bereich „Rollenzuweisungen“ der Logik-App mit ausgewähltem Abonnement und Schaltfläche zum Hinzufügen einer neuen Rollenzuweisung.":::

1. Richten Sie im Bereich **Rollenzuweisungen hinzufügen** die neue Rollenzuweisung mithilfe der folgenden Schritte ein:

   1. Wählen Sie für **Bereich** die Option **Speicher** aus.

   1. Wählen Sie als **Abonnement** das Abonnement für Ihr Speicherkonto aus.

   1. Wählen Sie unter **Ressource** das Speicherkonto aus, auf das Sie in Ihrer Logik-App zugreifen möchten.

   1. Wählen Sie für **Rolle** die entsprechenden Berechtigungen für Ihr Szenario aus.

      In diesem Beispiel wird **Mitwirkender an Storage-Blobdaten** verwendet, die den Lese-, Schreib- und Löschzugriff auf Blobcontainer und -daten ermöglicht. Details zu den Berechtigungen werden angezeigt, wenn Sie mit der Maus auf das Informationssymbol neben einer Rolle im Dropdownmenü zeigen.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="Screenshot des Konfigurationsbereichs „Rollenzuweisung“ mit Einstellungen für Bereich, Abonnement, Ressource und Rolle.":::

   1. Wählen Sie **Speichern** aus, um das Erstellen der Rollenzuweisung abzuschließen.

<a name="enable-managed-identity-support"></a>

### <a name="enable-managed-identity-support-on-logic-app"></a>Aktivieren von Unterstützung für verwaltete Identitäten für eine Logik-App

Fügen Sie als Nächstes Ihrem Workflow den [HTTP-Trigger oder die HTTP-Aktion](connectors-native-http.md) hinzu. Stellen Sie sicher, dass Sie [die Authentifizierungsart so festlegen, dass die verwaltete Identität](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) verwendet wird.

Die folgenden Schritte sind für Verbrauchs-Logik-Apps in mehrinstanzenfähigen Umgebungen und Standard-Logik-Apps in Umgebungen mit einem Mandanten identisch.

1. Öffnen Sie den Logik-App-Workflow im Designer.

1. Fügen Sie Ihrem Workflow je nach Szenario einen **HTTP**-Trigger oder eine -Aktion hinzu. Richten Sie die erforderlichen Parameterwerte ein.

   1. Wählen Sie eine **Methode** für Ihre Anforderung aus. In diesem Beispiel wird die HTTP **PUT**-Methode verwendet.

   1. Geben Sie den **URI** Ihres Blobs ein. Der Pfad ähnelt `https://<storage-container-name>/<folder-name>/{name}`. Geben Sie stattdessen ihren Container- und Ordnernamen an, behalten Sie jedoch die Literalzeichenfolge `{name}` bei.

   1. Fügen Sie unter **Header** die folgenden Elemente hinzu:

      - Den Header mit dem Blobtyp `x-ms-blob-type` mit dem Wert `BlockBlob`.

      - Den Header mit der API-Version `x-ms-version` mit dem entsprechenden Wert.

      Weitere Informationen finden Sie unter [Authentifizieren des Zugriffs mithilfe einer verwalteten Identität](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) und [Versionsverwaltung für Azure Storage-Dienste](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="Screenshot: Workflow-Designer und HTTP-Trigger oder -Aktion mit den erforderlichen PUT-Parametern.":::

1. Wählen Sie in der Liste **Neuen Parameter hinzufügen** die Option **Authentifizierung** aus, um die [verwaltete Identität zu konfigurieren](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).

    1. Wählen Sie unter **Authentifizierung** für die Eigenschaft **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

    1. Wählen Sie für die Eigenschaft **Verwaltete Identität** die Option **Systemseitig zugewiesene verwaltete Identität** aus.

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="Screenshot: Workflow-Designer und HTTP-Aktion mit den Authentifizierungsparametereinstellungen der verwalteten Identität.":::

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

Jetzt können Sie den [REST-API des Blob-Diensts](/rest/api/storageservices/blob-service-rest-api) aufrufen, um alle erforderlichen Speichervorgänge durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Connectors für Azure Logic Apps](apis-list.md)