---
title: Indexerverbindungen über einen privaten Endpunkt
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie Indexerverbindungen für den Zugriff auf Inhalte aus anderen Azure-Ressourcen, die über einen privaten Endpunkt geschützt werden.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 519181594bd98068cd66413a114a61d794b4d411
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350812"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Erstellen von Indexerverbindungen über einen privaten Endpunkt

> [!NOTE]
> Sie können mithilfe des [Ansatzes „Vertrauenswürdige Microsoft-Dienste“](../storage/common/storage-network-security.md#trusted-microsoft-services) virtuelle Netzwerke oder IP-Einschränkungen für ein Speicherkonto umgehen. Außerdem können Sie den Suchdienst aktivieren, um auf Daten im Speicherkonto zuzugreifen. Informationen hierzu finden Sie unter [Indexer access to Azure Storage with the trusted service exception](search-indexer-howto-access-trusted-service-exception.md) (Indexer-Zugriff auf Azure Storage mit Ausnahme des vertrauenswürdigen Diensts).
> 
> Wenn Sie diesen Ansatz verwenden, erfolgt die Kommunikation zwischen Azure Cognitive Search und Ihrem Speicherkonto jedoch über die öffentliche IP-Adresse des Speicherkontos, über das sichere Microsoft-Backbone-Netzwerk.

Viele Azure-Ressourcen, z. B. Azure-Speicherkonten, können so konfiguriert werden, dass sie Verbindungen aus einer Liste virtueller Netzwerke akzeptieren und externe Verbindungen ablehnen, die aus einem öffentlichen Netzwerk stammen. Wenn Sie einen Indexer zum Indizieren von Daten in Azure Cognitive Search verwenden und sich Ihre Datenquelle in einem privaten Netzwerk befindet, können Sie eine ausgehende [private Endpunktverbindung](../private-link/private-endpoint-overview.md) erstellen, um die Daten zu erreichen.

Für diese Indexerverbindungsmethode gelten die beiden folgenden Anforderungen:

+ Die Azure-Ressource, die Inhalte oder Code bereitstellt, muss vorher beim [Azure Private Link-Dienst](https://azure.microsoft.com/services/private-link/) registriert werden.

+ Zum Ausführen des Azure Cognitive Search-Diensts ist mindestens der Basic-Tarif erforderlich. Im Free-Tarif steht dieses Feature nicht zur Verfügung. Wenn Ihr Indexer über ein Skillset verfügt, ist zusätzlich der Tarif Standard 2 (S2) oder höher erforderlich. Weitere Informationen finden Sie unter [Diensteinschränkungen](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Freigegebene Verwaltungs-APIs für Private Link-Ressourcen

Private Endpunkte von gesicherten Ressourcen, die über Azure Cognitive Search-APIs erstellt wurden, werden als *freigegebene Private Link-Ressourcen* bezeichnet. Der Grund: Der Zugriff auf eine Ressource (z. B. ein Speicherkonto), die in den [Azure Private Link-Dienst](https://azure.microsoft.com/services/private-link/) integriert wurde, wird von Ihnen „freigegeben“.

Über seine Verwaltungs-REST-API stellt Azure Cognitive Search einen [CreateOrUpdate](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update)-Vorgang bereit, mit dem Sie den Zugriff aus einem Azure Cognitive Search-Indexer konfigurieren können.

Sie können private Endpunktverbindungen zu einigen Ressourcen nur mithilfe der Vorschauversion der Search Management-API (Version *2020-08-01-Preview* oder höher) erstellen, die in der folgenden Tabelle als *Vorschau* bezeichnet wird. Ressourcen ohne eine *Vorschau*-Bezeichnung können entweder mit der Vorschauversion oder der allgemein verfügbaren API-Version (*2020-08-01* oder höher) erstellt werden.

In der folgenden Tabelle sind die Azure-Ressourcen aufgeführt, für die Sie ausgehende private Endpunkte aus Azure Cognitive Search erstellen können. Zum Erstellen einer freigegebenen Private Link-Ressource geben Sie die **Gruppen-ID**-Werte genau so ein, wie sie in der API geschrieben werden. Bei den Werten wird Groß- und Kleinschreibung unterschieden.

| Azure-Ressource | Gruppen-ID |
| --- | --- |
| Azure Storage: Blob | `blob`|
| Azure Storage: Tabellen | `table`|
| Azure Cosmos DB: SQL-API | `Sql`|
| Azure SQL-Datenbank | `sqlServer`|
| Azure Database for MySQL (Vorschau) | `mysqlServer`|
| Azure-Schlüsseltresor | `vault` |
| Azure Functions (Vorschau) | `sites` |

Sie können die Azure-Ressourcen, bei denen ausgehende private Endpunktverbindungen unterstützt werden, auch anhand der [Liste unterstützter APIs](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported) abfragen.

Im weiteren Verlauf dieses Artikels wird eine Mischung aus dem Azure-Portal (oder der [Azure CLI](/cli/azure/), wenn Sie diese bevorzugen) und [Postman](https://www.postman.com/) (oder einem anderen HTTP-Client wie [curl](https://curl.se/), wenn Sie diesen bevorzugen) verwendet, um die Aufrufe der REST-API zu demonstrieren.

## <a name="set-up-indexer-connection-through-private-endpoint"></a>Einrichten einer Indexerverbindung über einen privaten Endpunkt

Befolgen Sie die folgenden Anweisungen, um eine Indexerverbindung mit einer sicheren Azure-Ressource über einen privaten Endpunkt herzustellen.

Die Beispiele in diesem Artikel basieren auf den folgenden Annahmen:
* Der Name des Suchdiensts lautet _contoso-search_. Er ist in der Ressourcengruppe _contoso_ eines Abonnements mit der Abonnement-ID _00000000-0000-0000-0000-000000000000_ enthalten. 
* Die Ressourcen-ID dieses Suchdiensts lautet _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_.

### <a name="step-1-secure-your-azure-resource"></a>Schritt 1: Schützen Ihrer Azure-Ressource

Die Schritte zum Einschränken des Zugriffs unterscheiden sich je nach Ressource. In den folgenden Szenarios werden drei der gängigsten Ressourcentypen gezeigt.

- Szenario 1: Datenquelle

    Im Folgenden finden Sie ein Beispiel für die Konfiguration eines Azure-Speicherkontos. Wenn Sie diese Option auswählen und die Seite leer lassen, bedeutet dies, dass kein Datenverkehr aus virtuellen Netzwerken zulässig ist.

    ![Der Screenshot des Bereichs „Firewalls und virtuelle Netzwerke“ für Azure-Speicher zeigt die Option zum Zulassen des Zugriffs auf ausgewählte Netzwerke.](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

- Szenario 2: Azure Key Vault

    Im Folgenden finden Sie ein Beispiel für die Konfiguration von Azure Key Vault.
 
    ![Der Screenshot des Bereichs „Firewalls und virtuelle Netzwerke“ für Azure Key Vault zeigt die Option zum Zulassen des Zugriffs auf ausgewählte Netzwerke.](media\search-indexer-howto-secure-access\key-vault-firewall-noaccess.png)
    
- Szenario 3: Azure Functions

    Für Azure Functions müssen keine Änderungen an der Netzwerkeinstellung vorgenommen werden. Wenn Sie später in den folgenden Schritten den freigegebenen privaten Endpunkt erstellen, lässt die Funktion automatisch nur den Zugriff über den privaten Link zu, nachdem Sie einen freigegebenen privaten Endpunkt für die Funktion erstellt haben.

### <a name="step-2-create-a-shared-private-link-resource-to-the-azure-resource"></a>Schritt 2: Erstellen einer freigegebenen Private Link-Ressource für die Azure-Ressource

Im folgenden Abschnitt wird beschrieben, wie Sie eine freigegebene Private Link-Ressource entweder mithilfe des Microsoft Azure-Portals oder der Azure CLI erstellen.

#### <a name="option-1-portal"></a>Option 1: Portal

> [!NOTE]
> Das Portal unterstützt nur das Erstellen eines freigegebenen privaten Endpunkts unter Verwendung von Gruppen-ID-Werten mit allgemeiner Verfügbarkeit. Führen Sie für MySQL und Azure Functions die unter Option 2 beschriebenen Azure CLI-Schritte aus.

Damit Azure Cognitive Search eine ausgehende private Endpunktverbindung erstellt, klicken Sie auf dem Blatt „Freigegebener privater Zugriff“ auf „Freigegebenen privaten Zugriff hinzufügen“. Auf dem Blatt, das auf der rechten Seite geöffnet wird, können Sie „Verbindung mit einer Azure-Ressource in meinem Verzeichnis herstellen“ oder „Verbindung mit einer Azure-Ressource nach Ressourcen-ID oder Alias herstellen“ auswählen.

Wenn Sie die erste Option (empfohlen) verwenden, unterstützt Sie das Blatt bei der Auswahl des geeigneten Speicherkontos und beim automatischen Ausfüllen anderer Eigenschaften wie der Gruppen-ID der Ressource und dem Ressourcentyp.

   ![Screenshot: „Freigegebenen privaten Zugriff hinzufügen“ mit einer unterstützenden Benutzeroberfläche zum Erstellen einer freigegebenen Private Link-Ressource. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource.png)

Bei Verwendung der zweiten Option können Sie die Azure-Ressourcen-ID manuell eingeben und die entsprechende Gruppen-ID auswählen. Die Gruppen-IDs werden am Anfang dieses Artikels aufgeführt.

![Screenshot: „Freigegebenen privaten Zugriff hinzufügen“ mit einer manuellen Benutzeroberfläche zum Erstellen einer freigegebenen Private Link-Ressource. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-manual.png)

#### <a name="option-2-azure-cli"></a>Option 2: Azure CLI

Alternativ können Sie den folgenden API-Aufruf mit der [Azure CLI](/cli/azure/) vornehmen. Verwenden Sie die API-Version „2020-08-01-preview“, wenn Sie eine Gruppen-ID verwenden, die sich in der Vorschau befindet. Beispiel: Gruppen-IDs für *Websites* und *mysqlServer* und in der Vorschauversion erfordern die Verwendung der Vorschau-API.

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/<search service subscription ID>/resourceGroups/<search service resource group name>/providers/Microsoft.Search/searchServices/<search service name>/sharedPrivateLinkResources/<shared private endpoint name>?api-version=2020-08-01 --body @create-pe.json
```

Im Folgenden sehen Sie ein Beispiel für den Inhalt der *create-pe.json*-Datei:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Bei Erfolg wird die Antwort `202 Accepted` zurückgegeben. Der Prozess der Erstellung eines ausgehenden privaten Endpunkts ist ein zeitintensiver (asynchroner) Vorgang. Er umfasst die Bereitstellung der folgenden Ressourcen:

+ Ein privater Endpunkt, dem eine private IP-Adresse in einem `"Pending"`-Zustand zugeordnet wurde. Die private IP-Adresse wird aus dem Adressraum abgerufen, der dem virtuellen Netzwerk der Ausführungsumgebung für den suchdienstspezifischen privaten Indexer zugeordnet ist. Nach der Genehmigung des privaten Endpunkts stammt jede Kommunikation von Azure Cognitive Search zur Azure-Ressource von der privaten IP-Adresse und einem sicheren Private Link-Kanal.

+ Eine private DNS-Zone für den Ressourcentyp, basierend auf der `groupId`. Durch Bereitstellen dieser Ressource stellen Sie sicher, dass bei jedem DNS-Lookup zur privaten Ressource die dem privaten Endpunkt zugeordnete IP-Adresse verwendet wird.

Sorgen Sie dafür, dass Sie die richtige `groupId` für den Ressourcentyp angeben, für den Sie den privaten Endpunkt erstellen. Jeder Konflikt führt zu einer nicht erfolgreichen Antwortnachricht.

### <a name="step-3-check-the-status-of-the-private-endpoint-creation"></a>Schritt 3: Überprüfen des Status der Erstellung des privaten Endpunkts

In diesem Schritt bestätigen Sie, dass sich der Bereitstellungsstatus der Ressource in „Erfolgreich“ ändert.

#### <a name="option-1-portal"></a>Option 1: Portal

> [!NOTE]
> Der Bereitstellungsstatus wird im Portal sowohl für die allgemeine Verfügbarkeit als auch für Gruppen-IDs angezeigt, die sich in der Vorschau befinden.

Im Portal wird der Status des freigegebenen privaten Endpunkts angezeigt. Im folgenden Beispiel lautet der Status „Wird aktualisiert“.

![Screenshot: Bereich „Freigegebenen privaten Zugriff hinzufügen“, der zeigt, dass die Ressourcenerstellung aktuell ausgeführt wird. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-progress.png)

Nachdem die Ressource erfolgreich erstellt wurde, erhalten Sie eine Portalbenachrichtigung, und der Bereitstellungsstatus der Ressource ändert sich in „Succeeded“ (Erfolgreich).

![Screenshot: Bereich „Freigegebenen privaten Zugriff hinzufügen“, der zeigt, dass die Ressourcenerstellung abgeschlossen wurde. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-success.png)

#### <a name="option-2-azure-cli"></a>Option 2: Azure CLI

Der `PUT`-Aufruf zum Erstellen des freigegebenen privaten Endpunkts gibt einen `Azure-AsyncOperation`-Headerwert zurück, der wie folgt aussieht:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Sie können den Status abfragen, indem Sie den Wert `Azure-AsyncOperationHeader` manuell abfragen.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01
```

### <a name="step-4-approve-the-private-endpoint-connection"></a>Schritt 4: Genehmigen der Verbindung mit einem privaten Endpunkt

> [!NOTE]
> In diesem Abschnitt durchlaufen Sie mithilfe des Microsoft Azure-Portals den Genehmigungsablauf für einen privaten Endpunkt zur Azure-Ressource, mit der eine Verbindung hergestellt wird. Alternativ könnten Sie die [REST-API](/rest/api/storagerp/privateendpointconnections) verwenden, die über den Speicherressourcenanbieter zur Verfügung steht.
>
> Andere Anbieter, z. B. Azure Cosmos DB oder Azure SQL Server, bieten ähnliche Speicherressourcenanbieter-APIs für die Verwaltung privater Endpunktverbindungen.

1. Navigieren Sie im Azure-Portal zu der Azure-Ressource, mit der Sie eine Verbindung herstellen, und wählen Sie die Registerkarte **Netzwerk** aus. Navigieren Sie dann zu dem Abschnitt, in dem die Verbindungen mit privaten Endpunkten aufgeführt sind. Im Folgenden finden Sie ein Beispiel für ein Speicherkonto. Nachdem der asynchrone Vorgang erfolgreich ausgeführt wurde, sollte eine Anforderung für eine private Endpunktverbindung mit der Anforderungsnachricht aus dem vorherigen API-Aufruf vorliegen.

   ![Der Screenshot des Azure-Portals zeigt den Bereich „Private Endpunktverbindungen“.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Wählen Sie den privaten Endpunkt aus, der von Azure Cognitive Search erstellt wurde. Identifizieren Sie in der Spalte **Privater Endpunkt** die private Endpunktverbindung anhand des Namens, der in der vorherigen API angegeben wurde, wählen Sie **Genehmigen** aus, und geben Sie dann eine entsprechende Nachricht ein. Der Inhalt der Nachricht spielt keine Rolle.

   Vergewissern Sie sich, dass die private Endpunktverbindung wie im folgenden Screenshot angezeigt wird. Es könnte zwischen ein und zwei Minuten dauern, bis der Status im Portal aktualisiert wird.

   ![Der Screenshot des Azure-Portals zeigt im Bereich „Private Endpunktverbindungen“ den Status „Genehmigt“.](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Nachdem die Anforderung für die private Endpunktverbindung genehmigt wurde, *kann* der Datenverkehr über den privaten Endpunkt fließen. Nachdem der private Endpunkt genehmigt wurde, erstellt Azure Cognitive Search die erforderlichen DNS-Zonenzuordnungen in der dafür erstellten DNS-Zone.

### <a name="step-5-query-the-status-of-the-shared-private-link-resource"></a>Schritt 5: Abfragen des Status der freigegebenen Private Link-Ressource

Um zu bestätigen, dass die freigegebene Private Link-Ressource nach der Genehmigung aktualisiert wurde, besuchen Sie das Blatt „Freigegebener privater Zugriff“ des Suchdiensts im Azure-Portal erneut, und überprüfen Sie den „Verbindungsstatus“.

   ![Screenshot: Azure-Portal mit einer „genehmigten“ freigegebenen Private Link-Ressource.](media\search-indexer-howto-secure-access\new-shared-private-link-resource-approved.png)

Alternativ können Sie den „Verbindungsstatus“ auch mithilfe der [GET-API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) abrufen.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01
```

Dadurch wird JSON-Code zurückgegeben, bei dem der Verbindungsstatus im Abschnitt „properties“ als „status“ angezeigt wird. Im Folgenden finden Sie ein Beispiel für ein Speicherkonto.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

Wenn der „Bereitstellungsstatus“ (`properties.provisioningState`) der Ressource `Succeeded` und der Verbindungsstatus (`properties.status`) `Approved` lautet, bedeutet dies, dass die freigegebene Private Link-Ressource funktionsfähig ist und der Indexer so konfiguriert werden kann, dass er über den privaten Endpunkt kommuniziert.

### <a name="step-6-configure-the-indexer-to-run-in-the-private-environment"></a>Schritt 6: Konfigurieren der Indexerausführung in der privaten Umgebung

> [!NOTE]
> Sie können diesen Schritt vor der Genehmigung der privaten Endpunktverbindung ausführen. Bis zur Genehmigung der privaten Endpunktverbindung endet jeder Indexer, der die Kommunikation mit einer sicheren Ressource (z. B. dem Speicherkonto) versucht, mit einem vorübergehenden Fehlerstatus. Neue Indexer werden nicht erstellt. Sobald die private Endpunktverbindung genehmigt wurde, können Indexer auf das private Speicherkonto zugreifen.

Die folgenden Schritte zeigen, wie Sie den Indexer für die Ausführung in der privaten Umgebung mithilfe der REST-API konfigurieren. Sie können die Ausführungsumgebung auch mit dem JSON-Editor im Portal festlegen.

1. Erstellen Sie die Datenquellendefinition, den Index und das Skillset (wenn Sie eine verwenden) wie gewohnt. In keiner dieser Definitionen gibt es Eigenschaften, die sich bei Verwendung eines freigegebenen privaten Endpunkts ändern.

1. [Erstellen Sie einen Indexer](/rest/api/searchservice/create-indexer), der auf die Datenquelle, den Index und das Skillset verweist, die Sie im vorhergehenden Schritt erstellt haben. Erzwingen Sie außerdem durch Festlegen der Indexerkonfigurationseigenschaft `executionEnvironment` auf `private`, dass der Indexer in der privaten Ausführungsumgebung ausgeführt wird.

    ```json
    {
        "name": "indexer",
        "dataSourceName": "blob-datasource",
        "targetIndexName": "index",
        "parameters": {
            "configuration": {
                "executionEnvironment": "private"
            }
        },
        "fieldMappings": []
    }
    ```

    Es folgt ein Beispiel für die Anforderung in Postman.
    
    ![Der Screenshot zeigt die Erstellung eines Indexers auf der Postman-Benutzeroberfläche.](media\search-indexer-howto-secure-access\create-indexer.png)    

Nachdem der Indexer erfolgreich erstellt wurde, sollte er sich über die private Endpunktverbindung mit der Azure-Ressource verbinden. Sie können den Status des Indexers mithilfe der [Indexer-Status-API](/rest/api/searchservice/get-indexer-status) überwachen.

> [!NOTE]
> Wenn Sie bereits Indexer haben, können Sie sie über die [PUT-API](/rest/api/searchservice/create-indexer) aktualisieren, indem Sie die Eigenschaft `executionEnvironment` auf `private` festlegen oder den JSON-Editor im Portal verwenden.

## <a name="troubleshooting"></a>Problembehandlung

+ Wenn Ihre Indexererstellung mit einer Fehlermeldung wie z. B. „Die Datenquellen-Anmeldeinformationen sind ungültig“ fehlschlägt, bedeutet dies, dass entweder der Status der privaten Endpunktverbindung noch nicht *Genehmigt* lautet oder die Verbindung nicht funktionsfähig ist. So beheben Sie das Problem: 
  + Rufen Sie den Status der freigegebenen Private Link-Ressource über die [GET-API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) ab. Wenn der Status *Genehmigt* lautet, überprüfen Sie die Eigenschaft `properties.provisioningState` der Ressource. Wenn der Status hier `Incomplete` lautet, bedeutet dies, dass einige der zugrunde liegenden Abhängigkeiten für die Ressource nicht eingerichtet werden konnten. Eine erneute Ausgabe der `PUT`-Anforderung zum erneuten Erstellen der freigegebenen Private Link-Ressource sollte das Problem beheben. Möglicherweise ist eine erneute Genehmigung erforderlich. Überprüfen Sie den Status der Ressource erneut, um sich zu vergewissern, dass das Problem behoben wurde.

+ Wenn Sie den Indexer erstellen, ohne seine Eigenschaft `executionEnvironment` festzulegen, könnte die Erstellung erfolgreich sein. Sein Ausführungsverlauf wird aber zeigen, dass die Indexerausführungen nicht erfolgreich sind. So beheben Sie das Problem:
  + [Aktualisieren Sie den Indexer](/rest/api/searchservice/update-indexer), um die Ausführungsumgebung anzugeben.

+ Wenn Sie den Indexer ohne Festlegen der Eigenschaft `executionEnvironment` erstellt haben und er erfolgreich ausgeführt wird, hat Azure Cognitive Search entschieden, dass seine Ausführungsumgebung die suchdienstspezifische *private* Umgebung ist. Dies kann sich ändern – je nach den vom Indexer verbrauchten Ressourcen, der Last für den Suchdienst und anderen Faktoren –, und zu einem späteren Zeitpunkt kann ein Fehler auftreten. So beheben Sie das Problem:
  + Wir empfehlen dringend, dass Sie die Eigenschaft `executionEnvironment` auf `private` festlegen und so sicherstellen, dass dabei in Zukunft kein Fehler mehr auftritt.

+ Wenn Sie die Netzwerkseite Ihrer Datenquelle im Azure-Portal anzeigen und einen privaten Endpunkt auswählen, den Sie für ihren Azure Cognitive Search-Dienst für den Zugriff auf diese Datenquelle erstellt haben, erhalten Sie möglicherweise den Fehler *Kein Zugriff*. Dies entspricht dem erwarteten Verhalten. Sie können den Status der Verbindungsanforderung über die Portalseite des Zieldiensts ändern, aber um die freigegebene Private Link-Ressource weiter zu verwalten, müssen Sie die freigegebene Private Link-Ressource auf der Netzwerkseite Ihres Suchdiensts im Azure-Portal anzeigen.

[Kontingente und Grenzwerte](search-limits-quotas-capacity.md) bestimmen, wie viele freigegebene Private Link-Ressourcen erstellt werden können und hängen von der SKU des Suchdiensts ab.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu privaten Endpunkten:

+ [Behandeln von Problemen mit freigegebenen Private Link-Ressourcen](troubleshoot-shared-private-link-resources.md)
+ [Was sind private Endpunkte?](../private-link/private-endpoint-overview.md)
+ [Für private Endpunkte erforderliche DNS-Konfigurationen](../private-link/private-endpoint-dns.md)