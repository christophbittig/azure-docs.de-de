---
title: Herstellen einer Verbindung mit Azure Blob Storage
description: Informationen zum Erstellen und Verwalten von Blobs in Azure-Speicherkonten mithilfe von Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 06/23/2021
tags: connectors
ms.openlocfilehash: 7fc6b33248af8b638218858c95d1c0de8b056e76
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824869"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Erstellen und Verwalten von Blobs in Azure Blob Storage mithilfe von Azure Logic Apps

Mit dem [Azure Blob Storage-Connector ](/connectors/azureblobconnector/) können Sie innerhalb von Azure Logic Apps auf Dateien zugreifen, die als Blobs in Ihrem Azure-Speicherkonto gespeichert sind, und diese verwalten. Dieser Connector stellt Trigger und Aktionen für Blobvorgänge in Ihren Logik-App-Workflows bereit. Mit diesen Vorgängen können Sie Aufgaben und Workflows für die Verwaltung der Dateien in Ihrem Speicherkonto automatisieren. Zu den [verfügbaren Connectoraktionen](/connectors/azureblobconnector/#actions) zählen das Überprüfen, Löschen, Lesen und Hochladen von Blobs. Der [verfügbare Trigger](/connectors/azureblobconnector/#triggers) wird ausgelöst, wenn ein Blob hinzugefügt oder geändert wird.

Sie können sich mit Blob Storage sowohl über Logik-App-Ressourcentypen mit Standard- und Verbrauchstarif verbinden. Sie können den Connector mit Logik-Apps in einer Einzelmandanten-, mehrinstanzenfähigen oder Integrationsdienstumgebung (Integration Service Environment, ISE) verwenden. Für Logik-Apps in einer Umgebung mit nur einem Mandanten bietet Blob Storage integrierte Vorgänge sowie verwaltete Connectorvorgänge.

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

Technische Informationen zu den Triggern, Aktionen und Grenzwerten dieses Connectors finden Sie auf der [Referenzseite zum Connector](/connectors/azureblobconnector/).

Anstelle des Blob Storage-Connectors können Sie auch eine [verwaltete Identität mit einem HTTP-Trigger oder einer Aktion verwenden](#access-blob-storage-with-managed-identities), um Blobvorgänge durchzuführen.

> [!IMPORTANT]
> Logik-Apps können nicht direkt auf Speicherkonten zugreifen, die sich hinter Firewalls befinden, wenn sich beide in derselben Region befinden. Um dieses Problem zu umgehen, können Sie Ihre Logik-Apps und Ihr Speicherkonto in verschiedenen Regionen einrichten. Weitere Informationen zum Aktivieren des Zugriffs von Azure Logic Apps auf Speicherkonten hinter Firewalls finden Sie weiter unten in diesem Thema im Abschnitt [Zugriff auf Speicherkonten hinter Firewalls](#access-storage-accounts-behind-firewalls).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).
- Ein [Azure-Speicherkonto und -Speichercontainer](../storage/blobs/storage-quickstart-blobs-portal.md)
- Ein Logik-App-Workflow, in dem Sie auf Ihr Blob Storage-Konto zugreifen möchten. Um Ihre Logik-App mit einem Blob Storage-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Grenzwerte

- Standardmäßig können Blob Storage-Aktionen Dateien mit einer Größe von *bis zu 50 MB* lesen oder schreiben. Zum Verarbeiten von Dateien mit einer Größe von mehr als 50 MB bis maximal 1.024 MB unterstützen Azure Blob Storage-Aktionen die [Nachrichtenblockerstellung](../logic-apps/logic-apps-handle-large-messages.md). Für die Aktion [**Blobinhalt abrufen**](/connectors/azureblobconnector/#get-blob-content) wird implizit die Blockerstellung verwendet.
- Blob Storage-Trigger unterstützen nicht die Blockerstellung. Trigger wählen beim Anfordern von Dateiinhalten nur Dateien aus, die 50 MB oder kleiner sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 50 MB sind:
  - Verwenden Sie einen Blob Storage-Trigger, der Dateieigenschaften zurückgibt, z. B. [**Wenn ein Blob hinzugefügt oder geändert wird (nur Eigenschaften)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)).
  - Lassen Sie auf den Trigger die Blob Storage-Aktion [**Blobinhalt abrufen**](/connectors/azureblobconnector/#get-blob-content) folgen, die die vollständige Datei liest und die Blockerstellung implizit verwendet.

## <a name="add-blob-storage-trigger"></a>Hinzufügen eines Blob Storage-Triggers

In Logic Apps muss jede Logik-App mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt ist. 

Dieser Connector hat einen verfügbaren Trigger, der entweder [**Wenn ein Blob in Azure Storage hinzugefügt oder geändert wird** oder **Wenn ein Blob hinzugefügt oder geändert wird (nur Eigenschaften)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)) heißt. Der Trigger wird ausgelöst, wenn Eigenschaften eines Blobs in Ihrem Speichercontainer hinzugefügt oder aktualisiert werden. Jedes Mal erstellt die Logic Apps-Engine eine Logik-App-Instanz und beginnt mit der Ausführung Ihres Workflows.

### <a name="single-tenant"></a>[Einzelmandant](#tab/single-tenant)

So fügen Sie eine Blob Storage-Aktion in einer Logik-App mit Einzelmandanten und Standardtarif hinzu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie Ihren Workflow im Designer.
1. Geben Sie im Suchfeld den Begriff `Azure blob` als Filter ein. Wählen Sie in der Liste „Trigger“ den Trigger **Wenn ein Blob in Azure Storage hinzugefügt oder geändert wird** aus.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Screenshot der Logik-App des Typs „Standard“ im Designer mit Auswahl des Triggers „Wenn ein Blob in Azure Storage hinzugefügt oder geändert wird“.":::
1. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Blobspeicherverbindung erstellen](#connect-to-storage-account).
1. Geben Sie die erforderlichen Informationen für den Trigger ein.
    1. Fügen Sie auf der Registerkarte **Parameter** den **Blobpfad** dem Blob hinzu, das Sie überwachen möchten.
        Um Ihren Blobpfad zu finden, öffnen Sie Ihr Speicherkonto im Azure-Portal. Wählen Sie im Navigationsmenü unter **Datenspeicher** die Option **Container** aus. Wählen Sie Ihren Blobcontainer aus. Wählen Sie im Navigationsmenü des Containers unter **Einstellungen** die Option **Eigenschaften** aus. Kopieren Sie den Wert **URL**, der den Pfad zum Blob angibt. Der Pfad ähnelt `https://{your-storage-account}.blob.core.windows.net/{your-blob}`.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Screenshot: Standard-Logik-App im Designer mit Parameterkonfiguration für Blob Storage-Trigger.":::
    1. Konfigurieren Sie bei Bedarf weitere Triggereinstellungen.
    1. Wählen Sie **Fertig** aus.
1. Fügen Sie Ihrem Workflow eine oder mehrere Aktionen hinzu.
1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus, um Ihre Änderungen zu speichern.

### <a name="multi-tenant"></a>[Mehrinstanzenfähig](#tab/multi-tenant)

So fügen Sie eine Blob-Storage Aktion in einer mehrinstanzenfähigen Logik-App mit Verbrauchstarif hinzu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie Ihren Workflow im Logic Apps-Designer.
1. Geben Sie im Suchfeld „azure blob“ als Filter ein. Wählen Sie in der Liste „Trigger“ den Trigger **Wenn ein Blob hinzugefügt oder geändert wird (nur Eigenschaften)** aus.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="Screenshot: Logik-App mit Verbrauchstarif im Designer mit Auswahl des Blob Storage-Triggers.":::
1. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Blobspeicherverbindung erstellen](#connect-to-storage-account).
1. Geben Sie die erforderlichen Informationen für den Trigger ein.
    1. Wählen Sie für **Container** das Ordnersymbol aus, um Ihren Blob Storage-Container auszuwählen. Oder geben Sie den Pfad manuell ein.
    1. Konfigurieren Sie bei Bedarf weitere Triggereinstellungen.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="Screenshot: Logik-App mit Verbrauchstarif im Designer mit Parameterkonfiguration für Blob Storage-Trigger.":::
1. Fügen Sie Ihrem Workflow eine oder mehrere Aktionen hinzu.
1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus, um Ihre Änderungen zu speichern.

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Hinzufügen einer Blob Storage-Aktion

In Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, die auf einen Trigger oder eine andere Aktion folgt.

### <a name="single-tenant"></a>[Einzelmandant](#tab/single-tenant)

Für Logik-Apps in einer Umgebung mit nur einem Mandanten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie Ihren Workflow im Logic Apps-Designer.
1. Fügen Sie einen Trigger hinzu. Dieses Beispiel beginnt mit dem Trigger [**Wiederholung**](../connectors/connectors-native-recurrence.md).
1. Fügen Sie Ihrem Workflow einen neuen Schritt hinzu. Geben Sie im Suchfeld „azure blob“ als Filter ein. Wählen Sie dann die gewünschte Blob Storage-Aktion aus. In diesem Beispiel wird **Blobinhalte aus Azure Storage lesen** verwendet. 
   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Screenshot: Logik-App mit Standardtarif im Designer mit einer Liste der verfügbaren Blob Storage-Aktionen.":::
1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, [verbinden Sie sich mit Ihrem Blob Storage-Konto](#connect-to-storage-account).
1. Geben Sie die erforderlichen Informationen für die Aktion ein.
    1. Geben Sie für **Containername** den Pfad des gewünschten Blobcontainers ein.
    1. Geben Sie für **Blobname** den Pfad des gewünschten Blobs ein.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Screenshot: Logik-App mit Standardtarif im Designer mit Auswahl des Blob Storage-Triggers.":::
    1. Konfigurieren Sie bei Bedarf weitere Aktionseinstellungen.
1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus. 
1. Testen Sie Ihre Logik-App, um sicherzustellen, dass Ihr ausgewählter Container ein Blob enthält. 

> [!TIP]
> In diesem Beispiel wird nur der Inhalt eines Blobs abgerufen. Fügen Sie zum Anzeigen des Inhalts eine weitere Aktion hinzu, die mit dem Blob eine Datei erstellt, indem ein anderer Connector verwendet wird. Fügen Sie beispielsweise eine OneDrive-Aktion hinzu, die basierend auf dem Blobinhalt eine Datei erstellt.

### <a name="multi-tenant"></a>[Mehrinstanzenfähig](#tab/multi-tenant)

Für Logik-Apps in einer Umgebung mit mehreren Mandanten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie Ihren Workflow im Logic Apps-Designer.
1. Fügen Sie einen Trigger hinzu. Dieses Beispiel beginnt mit dem Trigger [**Wiederholung**](../connectors/connectors-native-recurrence.md).
1. Fügen Sie Ihrem Workflow einen neuen Schritt hinzu. Geben Sie im Suchfeld „azure blob“ als Filter ein. Wählen Sie dann die gewünschte Blob Storage-Aktion aus. In diesem Beispiel wird **Blobinhalt abrufen** verwendet.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="Screenshot: Logik-App mit Verbrauchstarif im Designer mit einer Liste der verfügbaren Blob Storage-Aktionen.":::
1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, [verbinden Sie sich mit Ihrem Blob Storage-Konto](#connect-to-storage-account).
1. Geben Sie die erforderlichen Informationen für die Aktion ein.
    1. Wählen Sie für **Blob** das Ordnersymbol aus, um Ihren Blob Storage-Container auszuwählen. Oder geben Sie den Pfad manuell ein.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="Screenshot: Logik-App mit Verbrauchstarif im Designer mit der Konfiguration einer Blob Storage-Aktion.":::
    1. Konfigurieren Sie bei Bedarf weitere Aktionseinstellungen.

---

## <a name="connect-to-storage-account"></a>Herstellen einer Verbindung mit dem Speicherkonto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Bevor Sie Ihren [Blob Storage-Trigger](#add-blob-storage-trigger) oder Ihre [Blob Storage-Aktion](#add-blob-storage-action) konfigurieren können, müssen Sie eine Verbindung mit einem Speicherkonto herstellen. Eine Verbindung erfordert die folgenden Eigenschaften.

| Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
|----------|----------|-------|-------------|
| **Verbindungsname** | Ja | <*connection-name*> | Der Name, der für Ihre Verbindung erstellt werden soll |
| **Verbindungszeichenfolge für Azure Blob Storage** | Ja | <*storage-account*> | Wählen Sie Ihr Speicherkonto in der Liste aus, oder geben Sie eine Zeichenfolge an. |

> [!TIP]
> Um eine Verbindungszeichenfolge zu finden, wechseln Sie zur Seite des Speicherkontos. Wählen Sie im Navigationsmenü unter **Sicherheit + Netzwerkbetrieb** die Option **Zugriffsschlüssel** aus. Wählen Sie **Schlüssel anzeigen** aus. Kopieren Sie einen der beiden verfügbaren Werte für die Verbindungszeichenfolgen.

### <a name="single-tenant"></a>[Einzelmandant](#tab/single-tenant)

Für Logik-Apps in einer Umgebung mit nur einem Mandanten:

1. Geben Sie für **Verbindungsname** einen Namen ein.
1. Geben Sie für **Verbindungszeichenfolge für Azure Blob Storage** die Verbindungszeichenfolge für das gewünschte Speicherkonto ein.
1. Wählen Sie **Erstellen** aus, um die Verbindung herzustellen.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Screenshot: Logik-App mit Standardtarif im Designer mit der Aufforderung zum Hinzufügen einer neuen Verbindung zu einem Blob Storage-Schritt.":::

Wenn bereits eine Verbindung vorhanden ist, Sie aber eine andere wählen möchten, wählen Sie im Editor des Schritts **Verbindung ändern** aus.

Wenn Sie Probleme haben, sich mit Ihrem Speicherkonto zu verbinden, finden Sie weitere Informationen unter [Zugreifen auf Speicherkonten hinter Firewalls](#access-storage-accounts-behind-firewalls).

### <a name="multi-tenant"></a>[Mehrinstanzenfähig](#tab/multi-tenant)

Für Logik-Apps in einer Umgebung mit mehreren Mandanten:

1. Geben Sie für **Verbindungsname** einen Namen ein.
1. Wählen Sie für **Speicherkonto** das Speicherkonto mit Ihrem Blobcontainer aus. Oder wählen Sie **Verbindungsinformationen manuell eingeben** aus, um den Pfad selbst einzugeben.
1. Wählen Sie **Erstellen** aus, um die Verbindung herzustellen.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="Screenshot: Logik-App mit Verbrauchstarif im Designer mit der Aufforderung zum Hinzufügen einer neuen Verbindung zu einem Blob Storage-Schritt.":::

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

Logik-Apps können nicht direkt auf Speicherkonten hinter Firewalls zugreifen, wenn sich beide in derselben Region befinden. Als Abhilfe können Sie Ihre Logik-Apps in einer anderen Region als der Ihres Speicherkontos einrichten. Gewähren Sie anschließend Zugriff auf die [ausgehenden IP-Adressen für die verwalteten Connectors in Ihrer Region](/connectors/common/outbound-ip-addresses#azure-logic-apps).

> [!NOTE]
> Diese Lösung gilt nicht für den Azure Table Storage-Connector und den Azure Queue Storage-Connector. Um auf Ihren Table Storage oder Queue Storage zuzugreifen, können Sie stattdessen [integrierte HTTP-Trigger und -Aktionen verwenden](../logic-apps/logic-apps-http-endpoint.md).

So fügen Sie Ihre ausgehenden IP-Adressen der Firewall des Speicherkontos hinzu

1. Beachten Sie die [ausgehenden IP-Adressen des verwalteten Connectors](/connectors/common/outbound-ip-addresses#azure-logic-apps) für die Region Ihrer Logik-App.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie Ihre Speicherkontoressource.

1. Wählen Sie im Menü Ihrer Speicherkontoressource unter **Sicherheit + Netzwerkbetrieb** die Option **Netzwerk** aus.

1. Wählen Sie unter **Zugriff erlauben von** den Eintrag **Ausgewählte Netzwerke** aus. Die zugehörigen Einstellungen werden jetzt auf der Seite angezeigt.

1. Fügen Sie unter **Firewall** die IP-Adressen oder -Bereiche hinzu, die Zugriff benötigen.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Screenshot der Seite „Netzwerk“ des Blob Storage-Kontos in Azure-Portal mit Firewalleinstellungen zum Hinzufügen von IP-Adressen und -Bereichen zur Positivliste.":::

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>Zugreifen auf Speicherkonten über ein vertrauenswürdiges virtuelles Netzwerk

Sie können das Speicherkonto in einem virtuellen Azure-Netzwerk platzieren, das Sie verwalten, und dieses virtuelle Netzwerk dann der Liste vertrauenswürdiger virtueller Netzwerke hinzufügen. Damit Ihre Logik-App über ein [vertrauenswürdiges virtuelles Netzwerk](../virtual-network/virtual-networks-overview.md) auf das Speicherkonto zugreifen kann, müssen Sie diese Logik-App in einer [Integration Service Environment (ISE, Integrationsdienstumgebung)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) bereitstellen, die eine Verbindung mit Ressourcen in einem virtuellen Netzwerk herstellen kann. Anschließend können Sie die Subnetze in dieser ISE der Liste vertrauenswürdiger Netzwerke hinzufügen. Azure Storage-Connectors wie der Blob Storage-Connector können direkt auf den Speichercontainer zugreifen. Dieses Setup ist mit der Verwendung der Dienstendpunkte aus einer ISE heraus identisch.

### <a name="access-storage-accounts-through-azure-api-management"></a>Zugreifen auf Speicherkonten über Azure API Management

Wenn Sie für [API Management](../api-management/api-management-key-concepts.md) einen Dedicated-Tarif verwenden, können Sie die Speicher-API als Front-End verwenden, indem Sie API Management verwenden und dessen IP-Adressen das Passieren der Firewall gestatten. Im Grunde fügen Sie das virtuelle Azure-Netzwerk, das von API Management verwendet wird, der Firewalleinstellung des Speicherkontos hinzu. Sie können dann entweder die API Management-Aktion oder die HTTP-Aktion verwenden, um die Azure Storage-APIs aufzurufen. Wenn Sie diese Option auswählen, müssen Sie den Authentifizierungsprozess jedoch selbst handhaben. Weitere Informationen finden Sie unter [Einfache Unternehmensintegrationsarchitektur](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="access-blob-storage-with-managed-identities"></a>Azure Blob Storage mit verwalteten Identitäten

Wenn Sie auf Blob Storage zugreifen möchten, ohne diesen Logic Apps-Connector zu verwenden, können Sie stattdessen [verwaltete Identitäten für die Authentifizierung](../active-directory/managed-identities-azure-resources/overview.md) einsetzen. Sie können eine Ausnahme erstellen, die vertrauenswürdigen Diensten von Microsoft, wie z. B. einer verwalteten Identität, den Zugriff auf Ihr Speicherkonto durch eine Firewall ermöglicht.

So verwenden Sie verwaltete Identitäten in Ihrer Logik-App für den Zugriff auf Blob Storage

1. [Konfigurieren Sie den Zugriff auf Ihr Speicherkonto.](#configure-storage-account-access)
1. [Erstellen Sie eine Rollenzuweisung für Ihre Logik-App.](#create-role-assignment-for-logic-app)
1. [Aktivieren Sie Unterstützung für die verwaltete Identität in Ihrer Logik-App.](#enable-support-for-managed-identity-in-logic-app)

> [!NOTE]
> Einschränkungen dieser Lösung:
> - Sie können *nur* den HTTP-Trigger oder die HTTP-Aktion in Ihrem Workflow verwenden.
> - Sie müssen eine verwaltete Identität einrichten, um ihre Speicherkontoverbindung zu authentifizieren.
> - Sie können keine integrierten Blob Storage-Vorgänge verwenden, wenn Sie sich mit einer verwalteten Identität authentifizieren.
> - Für Logik-Apps in einer Umgebung mit nur einem Mandanten wird nur die vom System zugewiesene verwaltete Identität zur Verfügung gestellt und unterstützt, nicht die vom Benutzer zugewiesene verwaltete Identität.

### <a name="configure-storage-account-access"></a>Konfigurieren des Zugriffs auf das Speicherkonto

Um die Unterstützung für Ausnahmen und verwaltete Identitäten einzurichten, konfigurieren Sie zunächst den entsprechenden Zugriff auf Ihr Speicherkonto:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie die Seite Ihres Speicherkontos. Wählen Sie im Navigationsmenü unter **Sicherheit + Netzwerkbetrieb** die Option **Netzwerk** aus. 
1. Wählen Sie unter **Zugriff erlauben von** die Option **Ausgewählte Netzwerke** aus. Zusammengehörige Einstellungen werden jetzt auf der Seite angezeigt.
1. Wenn Sie auf Ihrem Computer auf das Speicherkonto zugreifen müssen, aktivieren Sie unter **Firewall** die Option **Client-IP-Adresse hinzufügen**.
1. Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**. 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Screenshot der Seite „Netzwerk“ des Blob Storage-Kontos in Azure-Portal mit Einstellungen zum Zulassen ausgewählter Netzwerke, clientseitiger IP-Adressen und vertrauenswürdiger Microsoft-Dienste.":::
1. Wählen Sie **Speichern** aus.

> [!TIP]
> Wenn Sie die Fehlermeldung **403 Forbidden** erhalten, sobald Sie versuchen, sich in Ihrem Workflow mit dem Speicherkonto zu verbinden, gibt es mehrere mögliche Ursachen. Probieren Sie die folgende Lösung aus, bevor Sie mit zusätzlichen Schritten fortfahren. Deaktivieren Sie die Einstellung **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**, und speichern Sie Ihre Änderungen. Reaktivieren Sie dann die Einstellung, und speichern Sie die Änderungen erneut. 

### <a name="create-role-assignment-for-logic-app"></a>Erstellen einer Rollenzuweisung für Ihre Logik-App

Aktivieren Sie als Nächstes [die Unterstützung für verwaltete Identitäten](../logic-apps/create-managed-service-identity.md) in Ihrer Logik-App.

1. Öffnen Sie Ihre Logik-App im Azure-Portal.
1. Wählen Sie im Navigationsmenü unter **Einstellungen** die Option **Identität** aus.
1. Legen Sie unter **Systemseitig zugewiesen** **Status** auf **Ein** fest. Diese Einstellung ist möglicherweise bereits aktiviert.
1. Wählen Sie unter **Berechtigungen** die Option **Azure-Rollenzuweisungen** aus.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Screenshot des Logik-App-Menüs im Azure-Portal mit dem Einstellungsbereich „Identität“ mit der Schaltfläche zum Hinzufügen von Azure-Rollenzuweisungsberechtigungen.":::
1. Wählen Sie im Bereich **Azure-Rollenzuweisungen** die Option **Rollenzuweisung hinzufügen** aus.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="Screenshot des Bereichs „Rollenzuweisungen“ der Logik-App mit ausgewähltem Abonnement und Schaltfläche zum Hinzufügen einer neuen Rollenzuweisung.":::
1. Konfigurieren Sie die neue Rollenzuweisung wie folgt.
    1. Wählen Sie für **Bereich** die Option **Speicher** aus.
    1. Wählen Sie für **Abonnement** das Abonnement mit Ihrem Speicherkonto.
    1. Wählen Sie unter **Ressource** das Speicherkonto aus, auf das Sie in Ihrer Logik-App zugreifen möchten.
    1. Wählen Sie für **Rolle** die entsprechenden Berechtigungen für Ihr Szenario aus. In diesem Beispiel wird **Mitwirkender an Storage-Blobdaten** verwendet, die den Lese-, Schreib- und Löschzugriff auf Blobcontainer und -daten ermöglicht. Zeigen Sie im Dropdownmenü auf das Informationssymbol neben einer Rolle, um Berechtigungsdetails anzuzeigen.
    1. Wählen Sie **Speichern** aus, um das Erstellen der Rollenzuweisung abzuschließen.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="Screenshot des Konfigurationsbereichs „Rollenzuweisung“ mit Einstellungen für Bereich, Abonnement, Ressource und Rolle.":::

### <a name="enable-support-for-managed-identity-in-logic-app"></a>Aktivieren der Unterstützung verwalteter Identitäten in der Logik-App

Fügen Sie als Nächstes Ihrem Workflow den [HTTP-Trigger oder die HTTP-Aktion](connectors-native-http.md) hinzu. Stellen Sie sicher, dass Sie [die Authentifizierungsart so festlegen, dass die verwaltete Identität](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) verwendet wird. 

Die Schritte sind für Logik-Apps in Umgebungen mit einem Einzel- oder mit mehreren Mandanten identisch.

1. Öffnen Sie Ihren Workflow im Logic Apps-Designer.
1. Fügen Sie Ihrem Workflow je nach Szenario einen neuen Schritt mit einem **HTTP**-Trigger oder einer HTTP-Aktion hinzu.
1. Konfigurieren Sie alle Pflichtparameter für Ihren **HTTP**-Trigger oder Ihre HTTP-Aktion.
    1. Wählen Sie eine **Methode** für Ihre Anforderung. In diesem Beispiel wird die HTTP PUT-Methode verwendet.
    1. Geben Sie den **URI** Ihres Blobs ein. Der Pfad ähnelt `https://{your-storage-account}.blob.core.windows.net/{your-blob}`.
    1. Fügen Sie unter **Header** den Header mit dem Blobtyp `x-ms-blob-type` mit dem Wert `BlockBlob` hinzu.
    1. Fügen Sie unter **Header** auch den Header mit der API-Version `x-ms-version` mit dem entsprechenden Wert hinzu. Weitere Informationen finden Sie unter [Authentifizieren des Zugriffs mithilfe einer verwalteten Identität](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) und [Versionsverwaltung für Azure Storage-Dienste](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="Screenshot von Logic Apps-Designer mit erforderlichen Parametern für HTTP PUT-Aktion.":::
1. Wählen Sie **Neuen Parameter hinzufügen** und **Authentifizierung** aus, um [die verwaltete Identität zu konfigurieren](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).
    1. Wählen Sie unter **Authentifizierung**, für **Authentifizierungstyp** die Option **Verwaltete Identität**.
    1. Wählen Sie für **Verwaltete Identität** die Option **Systemseitig zugewiesene verwaltete Identität**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="Screenshot von Logic Apps-Designer mit Einstellungen für Authentifizierungsparameter für eine HTTP-Aktion für die verwaltete Identität.":::
1. Wählen Sie auf der Symbolleiste des Logic Apps-Designers **Speichern** aus.

Jetzt können Sie den [REST-API des Blob-Diensts](/rest/api/storageservices/blob-service-rest-api) aufrufen, um alle erforderlichen Speichervorgänge durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über Logic Apps-Connectors](../connectors/apis-list.md)
