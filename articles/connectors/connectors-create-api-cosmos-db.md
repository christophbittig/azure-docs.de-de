---
title: Herstellen der Verbindung mit Azure Cosmos DB
description: Verarbeiten und Erstellen von Azure Cosmos DB-Dokumenten mithilfe Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: jcocchi
ms.author: jucocchi
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
tags: connectors
ms.openlocfilehash: 7a5025e284933284d910412fb2f9771dee43e31d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096009"
---
# <a name="process-and-create-azure-cosmos-db-documents-using-azure-logic-apps"></a>Verarbeiten und Erstellen von Azure Cosmos DB-Dokumenten mithilfe Azure Logic Apps

In Ihrem Workflow in Azure Logic Apps können Sie eine Verbindung mit Azure Cosmos DB herstellen und mit Dokumenten arbeiten, indem Sie den [Azure Cosmos DB-Connector](/connectors/documentdb/) verwenden. Dieser Connector bietet Trigger und Aktionen, die Ihr Workflow für Azure Cosmos DB-Vorgänge verwenden kann. Sie können automatisierte Workflows zum Verwalten von Dokumenten erstellen. Aktionen umfassen beispielsweise das Erstellen oder Aktualisieren, Lesen, Abfragen und Löschen von Dokumenten.

Sie können eine Verbindung mit Azure Cosmos DB über die beiden Ressourcentypen **Logik-App (Verbrauch)** und **Logik-App (Standard)** herstellen, indem Sie die Vorgänge [*verwalteter Connector*](managed.md) verwenden. Für **Logik-App (Standard)** bietet Azure Cosmos DB auch [*integrierte*](built-in.md) Vorgänge, die sich derzeit im Preview befinden und verschiedene Funktionen, die eine bessere Leistung und einen höheren Durchsatz bieten. Wenn Sie beispielsweise mit dem Ressourcentyp **Logik-App (Standard)** arbeiten, können Sie den integrierten Trigger verwenden, um auf Änderungen in einem Azure Cosmos DB-Container zu reagieren. Sie können Azure Cosmos DB-Vorgänge mit anderen Aktionen und Triggern in Ihren Logik-App-Workflows kombinieren, um Szenarien wie Ereignis-Sourcing und allgemeine Datenverarbeitung zu ermöglichen.

> [!NOTE]
> Derzeit können nur zustandsbehaftete Workflows in der Ressource **Logik-App (Standard)** sowohl verwaltete Connector-Vorgänge als auch integrierte Vorgänge verwenden. Zustandslose Workflows können nur integrierte Vorgänge verwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Ein [Azure Cosmos DB-Konto](../cosmos-db/sql/create-cosmosdb-resources-portal.md)

- Ein Logik-App-Workflow, aus dem Sie auf Ihr Azure Cosmos DB-Konto zugreifen möchten. Um den Azure Cosmos DB-Trigger zu verwenden, müssen Sie [Ihre Logik-App erstellen, während Sie den Ressourcentyp **Logik-App (Standard)** nutzen](../logic-apps/create-single-tenant-workflows-azure-portal.md) und einen leeren Workflow hinzufügen.

## <a name="add-azure-cosmos-db-trigger"></a>Azure Cosmos DB-Trigger hinzufügen

In Azure Logic Apps muss jeder Workflow mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird.

Wenn Sie mit dem Ressourcentyp **Logik-App (Standard)** arbeiten, ist der integrierte Trigger mit dem Namen **, sobald ein Element erstellt oder geändert wird (Vorschau)** , verfügbar und basiert auf dem [Azure Cosmos DB-Änderungsfeed-Muster](../cosmos-db/sql/change-feed-design-patterns.md). Dieser Trigger ist für den Ressourcentyp **Logik-App (Verbrauch)** nicht verfügbar.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Es sind keine Azure Cosmos DB-Trigger für den Ressourcentyp **Logik-App (Verbrauch)** verfügbar.

### <a name="standard"></a>[Standard](#tab/standard)

Um einen integrierten Azure Cosmos DB-Trigger zu einem Logik-App-Workflow in einem Azure Logic Apps Einzelmandanten hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Führen Sie die folgenden Schritte aus, um den Trigger zu finden:

   1. Wählen Sie im Designer **Vorgang auswählen** aus.

   1. Nachdem der Bereich **Trigger hinzufügen** geöffnet wurde, wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Integriert** aus.

   1. Geben Sie im Suchfeld `Azure Cosmos DB`ein. Wählen Sie in der Trigger-Liste den Trigger Namens **, wenn ein Element hinzugefügt oder geändert wird (Vorschau)** , aus.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-add.png" alt-text=" Der Screenshot zeigt das Azure-Portal und den Designer für einen Logik-App-Standardworkflow mit dem ausgewähltem Trigger mit dem Namen &quot;Wenn ein Element erstellt oder geändert wird (Vorschau)&quot;.":::

1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, dann [erstellen Sie eine Verbindung mit Azure Cosmos DB](#connect-to-azure-cosmos-db).

1. Auf der Registerkarte **Parameter**, geben Sie die erforderlichen Informationen für den Trigger ein.

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Datenbank-ID** | Ja | <*Datenbankname*> | Der Name der Datenbank mit dem Container, den Sie überwachen möchten. Diese Datenbank sollte auch über den Lease-Container verfügen. Wenn Sie noch nicht über einen Lease-Container verfügen, erstellt der Connector in einem späteren Schritt einen für Sie. |
   | **Überwachter Container ID** | Ja | <*Containername*> | Der Name des Containers, den Sie überwachen möchten. Dieser Container sollte bereits in der angegebenen Datenbank vorhanden sein. |
   | **Lease-Container ID** |  Ja | <*Lease-Containername*> | Entweder der Name eines vorhandenen Lease-Containers oder eines neuen Containers, den Sie für sich erstellen möchten. Der Trigger belegt `leases` als allgemeinen Standardnamen vor. |
   | **Erstellen eines Lease-Containers** | Nein | **Nein** oder **Ja** | Wenn der Lease-Container bereits in der angegebenen Datenbank vorhanden ist, wählen Sie **Nein** aus. Wenn der Trigger diesen Container erstellen soll, wählen Sie **Ja** aus. Wenn Sie **Ja** auswählen, stellen Sie sicher, dass Sie die Liste **Neuen Parameter hinzufügen** öffnen und die Eigenschaft **Lease-Containerdurchsatz** auswählen. Geben Sie die Anzahl der [Anforderungseinheiten (RUs)](../cosmos-db/request-units.md) ein, die Sie für diesen Container bereitstellen möchten. |
   |||||

   Die folgende Abbildung zeigt einen Beispieltrigger:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-parameters.png" alt-text="Der Screenshot zeigt den Workflow-Designer für einen Logik-App-Standardworkflow mit Azure Cosmos DB-Trigger und Parameterkonfiguration.":::

   > [!Note]
   > Der Trigger erstellt mehrere Workflowausführungen für jedes in Azure Cosmos DB erstellte oder geänderte Element, sodass die Ausgabe des dynamischen Inhalts dieses Triggers immer ein einzelnes Element ist.

1. Fügen Sie ihrem Workflow alle anderen Aktionen hinzu, die Sie möchten.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

---

## <a name="add-azure-cosmos-db-action"></a>Azure Cosmos DB-Aktion hinzufügen

In Azure Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, der einem Trigger oder einer anderen Aktion folgt. Der Azure Cosmos DB-Connector bietet Aktionen für die beiden Ressourcentypen **Logik-App (Verbrauch)** und **Logik-App (Standard).** Die folgenden Beispiele für jeden Ressourcentyp zeigen, wie man eine Aktion verwendet, die ein Dokument erstellt oder aktualisiert.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Um eine Azure Cosmos DB-Aktion zu einem Logik-App-Workflow in einem Azure Logic Apps Multi-Mandanten hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie Ihren Workflow im [Azure-Portal](https://portal.azure.com) im Designer.

1. Wenn Ihr Workflow leer ist, fügen Sie einen beliebigen Trigger hinzu.

   Dieses Beispiel beginnt mit dem Trigger [**Wenn eine HTTP-Anforderung empfangen wird**](connectors-native-reqres.md#add-request-trigger).

1. Wählen Sie unter dem Trigger oder der Aktion, dem bzw. der Sie die Azure Cosmos DB-Aktion hinzufügen möchten, die Option **Neuer Schritt** oder **Aktion hinzufügen** aus, falls Sie sich zwischen den Schritten befinden.

1. Geben Sie in das Suchfeld des Designers `Azure Cosmos DB` ein. Wählen Sie die gewünschte Azure Cosmos DB-Aktion aus.

   In diesem Beispiel wird die Aktion mit dem Namen **Dokument erstellen oder aktualisieren (V3)** verwendet.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-action-add.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Workflow für den Verbrauch mit den verfügbaren Azure Cosmos DB-Aktionen.":::

1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, dann [erstellen Sie eine Verbindung zu Ihrem Azure Cosmos DB-Konto](#connect-to-azure-cosmos-db).

1. Geben Sie die erforderlichen Informationen für die Aktion ein.

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Der Azure Cosmos DB-Kontoname** | Ja | Wählen Sie entweder **Verbindungseinstellungen verwenden (<*Azure-Cosmos-DB-Kontoname*>)** aus oder geben Sie den Namen manuell ein. | Der Kontoname für Ihr Azure Cosmos DB-Konto. |
   | **Datenbank-ID** | Ja | <*Datenbank-ID*> | Die Datenbank, die Sie verbinden möchten. |
   | **Container-ID** | Ja | <*Container-ID*> | Der Container, den Sie abfragen möchten. |
   | **Dokument** | Ja | <*JSON-Dokument*> | Das JSON-Dokument, das Sie erstellen möchten. In diesem Beispiel wird der Anforderungstext aus der Trigger-Ausgabe verwendet. <p><p>**Tipp**: Wenn das Token **Text** des HTTP-Triggers nicht in der Liste mit den dynamischen Inhalten angezeigt wird, die Sie hinzufügen können, wählen Sie neben dem Trigger-Namen die Option **Weitere Informationen anzeigen** aus. <p><p>**Hinweis**: Stellen Sie sicher, dass der Text aus wohlgeformten JSON-Code besteht und mindestens die `id` -Eigenschaft und die Partitionsschlüsseleigenschaft für Ihr Dokument enthält. Wenn bereits ein Dokument mit angegebenen `id` und Partitionsschlüssel vorhanden ist, wird das Dokument aktualisiert. Andernfalls wird ein neues Dokument erstellt. |
   |||||

   Die folgende Abbildung zeigt eine Beispielaktion:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-create-action.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Workflow für den Verbrauch mit der Azure Cosmos DB-Aktion &quot;Dokumente erstellen oder aktualisieren (V3)&quot; und Parameterkonfiguration.":::

1. Konfigurieren Sie alle anderen Aktionseinstellungen nach Bedarf.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Testen Sie Ihre Logik-App, um sicherzustellen, dass Ihr Workflow ein Dokument im angegebenen Container erstellt.

### <a name="standard"></a>[Standard](#tab/standard)

Um eine Azure Cosmos DB-Aktion zu einem Logik-App-Workflow in einem Azure Logic Apps Einzelmandanten hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie Ihren Workflow im [Azure-Portal](https://portal.azure.com) im Designer.

1. Wenn Ihr Workflow leer ist, fügen Sie einen beliebigen Trigger hinzu.

   Dieses Beispiel beginnt mit dem [**Wenn eine HTTP-Anforderung empfangen wird**-Trigger](connectors-native-reqres.md#add-request-trigger), der eine grundlegende Schemadefinition verwendet, um das Element darzustellen, das Sie erstellen möchten.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="Der Screenshot zeigt das Azure-Portal und den Designer für einen Logik-App-Standardworkflow mit dem Trigger &quot;Wenn eine HTTP-Anforderung empfangen wird&quot; und Parameterkonfiguration.":::

1. Wählen Sie unter dem Trigger oder der Aktion, dem bzw. der Sie die Azure Cosmos DB-Aktion hinzufügen möchten, die Option **Neuen Schritt hinzufügen** ( **+** ) > **Aktion hinzufügen** aus.

1. Stellen Sie im Designer sicher, dass **Vorgang hinzufügen** ausgewählt ist. Wählen Sie im Bereich **Aktion hinzufügen**, der unter dem Suchfeld **Vorgang auswählen** geöffnet wird, die Option **Integriert** aus, um die **Azure Cosmos DB** Aktionen zu finden.

   > [!NOTE]
   > Wenn Sie einen zustandsbehafteten Workflow verwenden, sind *verwaltete Connector*-Aktionen auch auf der Registerkarte **Azure** verfügbar. Verwenden Sie diese jedoch nur, wenn die gewünschten *integrierten* Aktionen nicht verfügbar sind.

1. Geben Sie im Suchfeld `Azure Cosmos DB`ein. Wählen Sie die gewünschte Azure Cosmos DB-Aktion aus.

   In diesem Beispiel wird die Aktion **Element erstellen oder aktualisieren (Vorschau)** verwendet, die ein neues Element erstellt oder ein vorhandenes Element aktualisiert.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-action-add.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Standardworkflow und den verfügbaren Azure Cosmos DB-Aktionen.":::

1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, dann [erstellen Sie eine Verbindung zu Ihrem Azure Cosmos DB-Konto](#connect-to-azure-cosmos-db).

1. Geben Sie die erforderlichen Informationen für die Aktion ein.

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Datenbank-ID** | Ja | <*Datenbank-ID*> | Die Datenbank, die Sie verbinden möchten. |
   | **Container-ID** | Ja | <*Container-ID*> | Der Container, den Sie abfragen möchten. |
   | **Element** | Ja | <*JSON-Dokument*> | Das JSON-Dokument, das Sie erstellen möchten. In diesem Beispiel wird der Anforderungstext aus der Trigger-Ausgabe verwendet. <p><p>**Tipp**: Wenn das Token **Text** des HTTP-Triggers nicht in der Liste mit den dynamischen Inhalten angezeigt wird, die Sie hinzufügen können, wählen Sie neben dem Trigger-Namen die Option **Weitere Informationen anzeigen** aus. <p><p>**Hinweis**: Stellen Sie sicher, dass der Text aus wohlgeformten JSON-Code besteht und mindestens die `id` -Eigenschaft und die Partitionsschlüsseleigenschaft für Ihr Dokument enthält. Wenn bereits ein Dokument mit angegebenen `id` und Partitionsschlüssel vorhanden ist, wird das Dokument aktualisiert. Andernfalls wird ein neues Dokument erstellt. | 
   | **Partitionsschlüssel** | Ja | <*Partitionsschlüssel*> | Der Partitionsschlüsselwert für das Dokument, das Sie erstellen möchten. Wenn diese Eigenschaft nicht angezeigt wird, öffnen Sie **Neue Parameterliste hinzufügen** und wählen Sie **Partitionsschlüssel** aus. <p><p>**Wichtig**: Obwohl diese Eigenschaft nicht als erforderlich mit einem Sternchen (*) markiert ist, ist die Eigenschaft tatsächlich erforderlich, damit der Workflow erfolgreich ausgeführt werden kann. |
   |||||

   Die folgende Abbildung zeigt eine Beispielaktion:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-create-action.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Standardworkflow mit der Azure Cosmos DB-Aktion &quot;Element erstellen oder aktualisieren&quot; und Parameterkonfiguration.":::

1. Konfigurieren Sie alle anderen Aktionseinstellungen nach Bedarf.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Testen Sie Ihre Logik-App, um sicherzustellen, dass Ihr Workflow ein Dokument im angegebenen Container erstellt.

---

## <a name="connect-to-azure-cosmos-db"></a>Herstellen der Verbindung mit Azure Cosmos DB

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Bevor Sie Ihren [Azure Cosmos DB-Trigger](#add-azure-cosmos-db-trigger) oder Ihre [Azure Cosmos DB-Aktion](#add-azure-cosmos-db-action) konfigurieren können, müssen Sie eine Verbindung mit einem Datenbankkonto herstellen.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

In einem **Logik-App (Verbrauch)** Workflow, erfordert eine Azure Cosmos DB-Verbindung die folgenden Eigenschaftswerte:

| Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
|----------|----------|-------|-------------|
| **Verbindungsname** | Ja | <*connection-name*> | Der Name, der für Ihre Verbindung verwendet werden soll. |
| **Authentifizierungstyp** | Ja | <*Verbindungstyp*> | Der Authentifizierungstyp, den Sie verwenden möchten. In diesem Beispiel wird **Zugriffsschlüssel** verwendet. <p><p>- Wenn Sie **Zugriffsschlüssel** auswählen, geben Sie die verbleibenden erforderlichen Eigenschaftswerte an, um die Verbindung zu erstellen. <p><p>- Wenn Sie **Azure AD Integriert** auswählen, sind keine weiteren Eigenschaftswerte erforderlich. Sie müssen die Verbindung jedoch konfigurieren, indem Sie die Schritte für [Azure AD Authentifizierung und Cosmos DB-Connector](/connectors/documentdb/#azure-ad-authentication-and-cosmos-db-connector) ausführen.  |
| **Zugriffsschlüssel für Ihr Azure Cosmos DB-Konto** | Ja | <*Zugriffsschlüssel*> | Der Zugriffsschlüssel für das Azure Cosmos DB-Konto, das für diese Verbindung verwendet werden soll. Dieser Wert ist entweder ein Lese-/Schreibschlüssel oder ein schreibgeschützter Schlüssel. <p><p>**Hinweis**: Um den Schlüssel zu finden, navigieren Sie zur Seite des Azure Cosmos DB-Kontos. Wählen Sie im Navigationsmenü unter **Einstellungen** die Option **Schlüssel** aus. Kopieren Sie einen der verfügbaren Schlüsselwerte. |
| **Konto ID** | Ja | <*Konto ID*> | Der Name für das Azure Cosmos DB-Konto, der für diese Verbindung verwendet werden soll. |
|||||

Die folgende Abbildung enthält eine Beispielverbindung:

:::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-connection-configure.png" alt-text="Der Screenshot zeigt ein Beispiel für die Azure Cosmos DB-Verbindungskonfiguration für einen Logik-App-Workflow für den Verbrauch.":::

> [!NOTE]
> Wenn Sie nach dem Erstellen der Verbindung über eine andere vorhandene Azure Cosmos DB-Verbindung verfügen, die Sie stattdessen verwenden möchten, wählen Sie **Verbindung ändern** im Editor für Trigger- oder Aktionsdetails aus.

### <a name="standard"></a>[Standard](#tab/standard)

In einem **Logik-App (Standard)** Workflow, erfordert eine Azure Cosmos DB-Verbindung die folgenden Eigenschaftswerte:

| Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
|----------|----------|-------|-------------|
| **Verbindungsname** | Ja | <*connection-name*> | Der Name, der für Ihre Verbindung verwendet werden soll. |
| **Verbindungszeichenfolge** | Ja | <*Verbindungszeichenfolge*> | Die Azure Cosmos DB-Verbindungszeichenfolge, die für Ihre Verbindung verwendet werden soll. <p><p>**Hinweis**: Um die Verbindungszeichenfolge zu finden, navigieren Sie zur Seite des Azure Cosmos DB-Kontos. Wählen Sie im Navigationsmenü unter **Einstellungen** die Option **Schlüssel** aus. Kopieren Sie einen der verfügbaren Werte für Verbindungszeichenfolgen. |
|||||

Die folgende Abbildung enthält eine Beispielverbindung:

:::image type="content" source="./media/connectors-create-api-cosmos-db/standard-connection-configure.png" alt-text="Der Screenshot zeigt ein Beispiel für die Azure Cosmos DB-Verbindungskonfiguration für einen Logik-App-Standardworkflow.":::

> [!NOTE]
> Wenn Sie nach dem Erstellen der Verbindung über eine andere vorhandene Azure Cosmos DB-Verbindung verfügen, die Sie stattdessen verwenden möchten, wählen Sie **Verbindung ändern** im Editor für Trigger- oder Aktionsdetails aus.

---

## <a name="connector-reference"></a>Connector-Referenz

Referenzinformationen zu Azure Cosmos DB Vorgängen *verwalteter Connector* z. B. Trigger, Aktionen und Grenzwerte, finden Sie auf der [Connector-Referenzseite](/connectors/documentdb/).

Für *integrierte* Azure Cosmos DB-Vorgänge ist keine entsprechende Referenzseite vorhanden. Lesen Sie stattdessen die folgende Tabelle, um weitere Informationen zu erfahren:

| type | Name | Parameter |
|------|------|------------|
| Trigger | Wenn ein Element erstellt oder geändert wird | - **Datenbank-ID**: Erforderlich. Der Name der Datenbank mit den überwachten Containern und den Lease-Containern. <br>- **Überwachte Container-ID**: Erforderlich. Der Name des überwachten Containers. <br>- **Lease-Container-ID**: Erforderlich. Der Name des Containers, der zum Speichern von Leases verwendet wird. <br>- **Lease-Container erstellen**: Erforderlich. Wenn wahr, erstellen Sie den Lease-Container, sofern er noch nicht vorhanden ist. <br>- **Lease-Containerdurchsatz**: Optional. Die Anzahl der Anforderungseinheiten, die beim Erstellen des Lease-Containers zugewiesen werden sollen. |
| Aktion | Erstellen oder Aktualisieren eines Elements | - **Datenbank-ID**: Erforderlich. Der Name der Datenbank. <br>- **Container-ID**: Erforderlich. Der Name des Containers, <br>- **Element**: Erforderlich. Das zu erstellende oder zu aktualisierende Element. <br>- **Partitionsschlüssel**: Erforderlich. Der Partitionsschlüsselwert für das angeforderte Element. <br>- **Ist Upsert**: Optional. Wenn wahr, ersetzen Sie das Element, sofern vorhanden. Andernfalls erstellen Sie das Element. |
| Aktion | Massenerstellung oder -aktualisierung vieler Elemente | Diese Aktion ist für Szenarien mit hohem Durchsatz optimiert und verfügt über zusätzliche Verarbeitung, bevor die Aktion Ihre Elemente übermittelt, die im Azure Cosmos DB-Container erstellt werden sollen. Bei einer großen Anzahl von Elementen beschleunigt diese zusätzliche Verarbeitung die Gesamtanforderungszeit. Bei einer kleinen Anzahl von Elementen kann dieser zusätzliche Mehraufwand zu einer langsameren Leistung führen als die Verwendung mehrerer Aktionen zum Erstellen von Elementen. <p><p>- **Datenbank-ID**: Erforderlich. Der Name der Datenbank. <br>- **Container-ID**: Erforderlich. Der Name des Containers, <br>- **Element**: Erforderlich. Ein Array von Elementen, die erstellt oder aktualisiert werden sollen. <br>- **Ist Upsert**: Optional. Wenn wahr, ersetzen Sie ein Element, sofern vorhanden. Andernfalls erstellen Sie das Element. |
| Aktion | Lesen eines Elements | - **Datenbank-ID**: Erforderlich. Der Name der Datenbank. <br>- **Container-ID**: Erforderlich. Der Name des Containers, <br>- **Element-ID**: Erforderlich. Der `id`Wert für das angeforderte Element. <br>- **Partitionsschlüssel**: Erforderlich. Der Partitionsschlüsselwert für das angeforderte Element. |
| Aktion | Löschen eines Elements | - **Datenbank-ID**: Erforderlich. Der Name der Datenbank. <br>- **Container-ID** Erforderlich. Der Name des Containers, <br>- **Element-ID**: Erforderlich. Der `id`Wert für das angeforderte Element. <br>- **Partitionsschlüssel**: Erforderlich. Der Partitionsschlüsselwert für das angeforderte Element. |
| Aktion | Abfrageelemente | - **Datenbank-ID**: Erforderlich. Der Name der Datenbank. <br>- **Container-ID**: Erforderlich. Der Name des Containers, <br>- **SQL-Abfrage**: Erforderlich. Der Azure Cosmos DB SQL Abfragetext.  <br>- **Partitionsschlüssel**: Optional. Der Partitionsschlüsselwert für die Anforderung, falls vorhanden. <br>- **Fortsetzungstoken**: Optional. Das Fortsetzungstoken für diese Abfrage, das vom Azure Cosmos DB-Dienst angegeben wird, falls vorhanden. <br>- **Maximale Elementanzahl**: Optional. Die maximale Anzahl von Elementen, die von der Abfrage zurückgegeben werden sollen. |
||||

## <a name="best-practices-for-azure-cosmos-db-built-in-operations"></a>Bewährte Methoden für integrierte Azure Cosmos DB-Vorgänge

### <a name="get-iterable-results-from-the-query-items-action-by-using-expressions"></a>Abrufen iterierbarer Ergebnisse aus der Aktion Abfrageelemente mithilfe von Ausdrücken

Der Ausgabetyp der integrierten Aktion **Abfrageelemente** in einem **Logik-App-Workflow (Standard)** ist ein nicht iterierbares Objekt. Um die Abfrageergebnisse als iterierbares Objekt für die Verarbeitung abzurufen, gehen Sie folgendermaßen vor:

1. Öffnen Sie Ihren Workflow im [Azure-Portal](https://portal.azure.com) im Designer.

1. Wenn Ihr Workflow leer ist, fügen Sie einen beliebigen Trigger hinzu.

   Dieses Beispiel beginnt mit dem Trigger [**Wiederholung**](connectors-native-recurrence.md).

1. Wählen Sie unter dem Trigger oder der Aktion, dem bzw. der Sie die Azure Cosmos DB-Aktion hinzufügen möchten, die Option **Neuen Schritt hinzufügen** ( **+** ) > **Aktion hinzufügen** aus.

1. Stellen Sie im Designer sicher, dass **Vorgang hinzufügen** ausgewählt ist. Wählen Sie im Bereich **Aktion hinzufügen**, der unter dem Suchfeld **Vorgang auswählen** geöffnet wird, die Option **Integriert** aus, um die **Azure Cosmos DB** Aktionen zu finden.

1. Geben Sie im Suchfeld `Azure Cosmos DB`ein. Wählen Sie die Aktion **Abfrageelemente (Vorschau)** aus.

1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, [erstellen Sie eine Verbindung zu Ihrem Azure Cosmos DB-Konto](#connect-to-azure-cosmos-db).

1. Geben Sie die erforderlichen Informationen für die Aktion ein.

   | Eigenschaften | Erforderlich | Wert | BESCHREIBUNG |
   |------------|----------|-------|-------------|
   | **Datenbank-ID** | Ja | <*Datenbank-ID*> | Die Datenbank, die Sie verbinden möchten. |
   | **Container-ID** | Ja | <*Container-ID*> | Der Container, den Sie abfragen möchten. |
   | **SQL-Abfrage** | Ja | <*SQL-Abfrage*> | Die SQL Abfrage für Ihre Anforderung. |
   |||||

   Die folgende Abbildung zeigt eine Beispielaktion:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-action.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Standardworkflow mit Azure Cosmos DB Abfrageelementen und Parameterkonfiguration.":::

1. Konfigurieren Sie alle anderen Aktionseinstellungen nach Bedarf.

1. Wählen Sie unter der Aktion **Neuen Schritt einfügen** ( **+** ) > **Aktion hinzufügen** aus. Wählen Sie im Bereich **Aktion hinzufügen**, der unter dem Suchfeld **Vorgang auswählen** geöffnet wird, die Option **Integriert** aus, um die **Für jedes** Aktionen zu finden und auszuführen.

1. Um die Ausgaben der Aktion **Abfrageelemente** zu konvertieren, führen Sie die folgenden Schritte aus:

   1. Klicken Sie im Aktionsdetailbereich **Für jedes** in das Feld **Ausgabe aus vorherigen Schritten auswählen**, damit die Liste mit den dynamischen Inhalten angezeigt wird. Wählen Sie die Registerkarte **Ausdruck** aus, um den Ausdrucks-Editor zu öffnen.

   1. Suchen Sie in der Funktionsliste den Abschnitt **Verweisende Funktionen** und wählen Sie die Funktion **Ausgaben (Aktionsname)** aus, die im Feld Ausdrucks-Editor angezeigt wird.

   1. Geben Sie im Editorfeld `'Query_items'` als Parameter für die Funktion **Ausgaben (Aktionsname)** gefolgt von `?['body']?['item']?['results']` ein. Greifen Sie dann auf das Array mit den Abfrageergebnissen zu und wählen Sie **OK** aus. Der vollständige Ausdruck folgt diesem Format:

      `outputs('Query_items')?['body']?['item']?['results']`

      :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-configure.png" alt-text="Der Screenshot zeigt das Azure-Portal und den Designer für einen Logik-App-Standardworkflow mit der Aktion &quot;Für jedes&quot; zum Verarbeiten von Azure Cosmos DB-Abfrageergebnissen.":::

1. Fügen Sie in der **Für jedes**-Schleife alle anderen Aktionen hinzu, die Sie wünschen.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Testen Sie Ihre Logik-App, um sicherzustellen, dass Ihr Workflow die von Ihnen erwartete Ausgabe zurückgibt.

### <a name="confirm-action-success-by-checking-the-operation-status-code"></a>Bestätigen Sie den Aktionserfolg durch Überprüfung des Vorgangsstatuscodes

Wenn Sie die integrierten Azure Cosmos DB-Aktionen in einem **Logik-App-Workflow (Standard)** verwenden, sollten Sie nach jeder Aktion einen Schritt ausführen, der den Statuscode überprüft, der vom Dienstaufruf an den Azure Cosmos DB-Dienst zurückgegeben wird. Auf diese Weise können nachfolgende Aktionen im Workflow auf die verschiedenen Statuscodes reagieren, die der Azure Cosmos DB-Dienst basierend auf Ihrem Szenario zurückgibt.

> [!NOTE]
> Im Gegensatz zu anderen verwalteten Connector-Aktionen und integrierten Aktionen unterscheidet sich der von einer Azure Cosmos DB-Aktion zurückgegebene Statuscode derzeit von dem Statuscode, der vom Dienstaufruf zurückgegeben wird, der an den Azure Cosmos DB-Dienst gesendet wird. Der Statuscode der Aktion ist in der Liste mit den dynamischen Inhalten verfügbar, während der Statuscode des Dienstaufrufs zurückgegeben und im `body` des Antwortobjekts geschachtelt wird.
>
> Die folgenden Schritte zeigen, wie Sie mithilfe eines Ausdrucks, der `body` des Antwortobjekts analysiert, auf den Statuscode des Dienstaufrufs zugreifen.

Mit den folgenden Schritten wird beispielsweise ein Workflow erstellt, der die Aktion **Element lesen** verwendet und zeigt, wie Sie den Statuscode überprüfen, der vom Dienstaufruf an den Azure Cosmos DB-Dienst zurückgegeben wird:

1. Öffnen Sie Ihren Workflow im [Azure-Portal](https://portal.azure.com) im Designer.

1. Wenn Ihr Workflow leer ist, fügen Sie einen beliebigen Trigger hinzu.

   Dieses Beispiel beginnt mit dem [**Wenn eine HTTP-Anforderung empfangen wird**-Trigger](connectors-native-reqres.md#), der eine grundlegende Schemadefinition verwendet, um das Dokument darzustellen, das Sie lesen möchten.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Standardworkflow mit Anforderungstrigger und Parameterkonfiguration.":::

1. Wählen Sie unter dem Trigger oder der Aktion, dem bzw. der Sie die Azure Cosmos DB-Aktion hinzufügen möchten, die Option **Neuen Schritt hinzufügen** ( **+** ) > **Aktion hinzufügen** aus.

1. Stellen Sie im Designer sicher, dass **Vorgang hinzufügen** ausgewählt ist. Wählen Sie im Bereich **Aktion hinzufügen**, der unter dem Suchfeld **Vorgang auswählen** geöffnet wird, die Option **Integriert** aus, um die **Azure Cosmos DB** Aktionen zu finden.

1. Geben Sie im Suchfeld `Azure Cosmos DB`ein. Wählen Sie die Aktion **Abfrageelemente (Vorschau)** aus.

1. Wenn Sie zur Eingabe von Verbindungsdetails aufgefordert werden, [erstellen Sie eine Verbindung zu Ihrem Azure Cosmos DB-Konto](#connect-to-azure-cosmos-db).

1. Geben Sie die erforderlichen Informationen für die Aktion ein.

   | Eigenschaften | Erforderlich | Wert | BESCHREIBUNG |
   |------------|----------|-------|-------------|
   | **Datenbank-ID** | Ja | <*Datenbank-ID*> | Die Datenbank, die Sie verbinden möchten. |
   | **Container-ID** | Ja | <*Container-ID*> | Der Container, den Sie abfragen möchten. |
   | **Element-ID** | Ja | <*Element-ID*> | Der `id` Wert für das Dokument, das Sie lesen möchten. |
   | **Partitionsschlüssel** | Ja | <*Partitionsschlüssel*> | Der Partitionsschlüsselwert für das Dokument, das Sie lesen möchten. |
   |||||

   Die folgende Abbildung zeigt eine Beispielaktion:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-read-configure.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Standardworkflow mit der Azure Cosmos DB-Aktion „Element lesen“ und Parameterkonfiguration.":::

1. Konfigurieren Sie alle anderen Aktionseinstellungen nach Bedarf.

1. Wählen Sie unter der Aktion **Neuen Schritt einfügen** ( **+** ) > **Aktion hinzufügen** aus. Wählen Sie im Bereich **Aktion hinzufügen**, der unter dem Suchfeld **Vorgang auswählen** geöffnet wird die Option **Integriert** aus, um die Aktion **Bedingung** zu suchen und hinzuzufügen.

1. Klicken Sie in das linke Bedingungsfeld **Wert auswählen** damit die Liste mit den dynamischen Inhalten erscheint. Wählen Sie die Registerkarte **Ausdruck** aus, um den Ausdrucks-Editor zu öffnen.

1. Suchen Sie in der Funktionsliste den Abschnitt **Verweisende Funktionen** und wählen Sie die Funktion **Ausgaben (Aktionsname)** aus, die im Feld Ausdrucks-Editor angezeigt wird.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-condition-configure.png" alt-text="Der Screenshot zeigt den Designer für einen Logik-App-Standardworkflow mit einer Bedingungskonfiguration.":::

1. Geben Sie im Feld Ausdrucks-Editor `'Read_an_item'` als Parameter für die Funktion **Ausgaben (Aktionsname)** gefolgt von `?['body']?['statusCode']` ein und wählen Sie **OK** aus.

   Dieser Ausdruck greifen auf den Statuscode zu, der vom Dienstaufruf zurückgegeben wird, der den Lesevorgang in Azure Cosmos DB durch Analyse des `body` Inhalts im Antwortobjekt ausführt. Der vollständige Ausdruck folgt diesem Format:

   `outputs('Read_an_item')?['body']?['statusCode']`

   > [!NOTE]
   > Beachten Sie, dass sich der Statuscode, der von einer Azure Cosmos DB-Aktion zurückgegeben wird, von dem Statuscode unterscheidet, der von dem Aufruf zurückgegeben wird, der tatsächlich an Azure Cosmos DB gesendet wird. Der Statuscode der Aktion ist in der Liste mit den dynamischen Inhalten verfügbar, während der Statuscode des Dienstaufrufs zurückgegeben und im `body` des Antwortobjekts geschachtelt wird.

1. Geben Sie im mittleren Bedingungsfeld und im Feld **Wert auswählen** ganz rechts, die Werte an, die für Ihren Workflow passend sind. In diesem Beispiel überprüft die Bedingung, ob der Statuscode gleich `OK` ist.

   > [!NOTE]
   > Statuscodes werden als Text und nicht als Zahlen dargestellt.

1. Fügen Sie in den Pfaden **Wahr** und **Falsch** der Bedingung alle anderen Aktionen hinzu, die zu Ihrem Szenario passen.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Testen Sie Ihre Logik-App, um sicherzustellen, dass Ihr Workflow die erwartete Ausgabe zurückgibt.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Connectors für Azure Logic Apps](apis-list.md)
