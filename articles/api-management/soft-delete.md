---
title: Azure API Management – Vorläufiges Löschen (Vorschau) | Microsoft-Dokumentation
description: Das vorläufige Löschen ermöglicht es Ihnen, gelöschte API Management-Instanzen wiederherzustellen.
ms.service: api-management
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 11/27/2020
ms.openlocfilehash: ca142ec96bf4ec45cc7c2f612a38ee0d10bf9615
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000573"
---
# <a name="api-management-soft-delete-preview"></a>Azure API Management – Vorläufiges Löschen (Vorschau)

Über das vorläufige Löschen (Vorschau) einer API Management-Instanz können Sie kürzlich gelöschte API Management-Instanzen (APIM) wiederherstellen.

> [!IMPORTANT]
> Nur API Management-Instanzen, die mithilfe von `2020-06-01-preview` und späteren API-Versionen gelöscht wurden, werden vorläufig gelöscht und können mithilfe der in diesem Artikel beschriebenen Schritte wiederhergestellt werden. APIM-Instanzen, die mit früheren API-Versionen gelöscht wurden, werden weiterhin endgültig gelöscht. Azure PowerShell und Azure CLI verwenden derzeit nicht die `2020-06-01-preview`-Version und führen ebenfalls zum endgültigen Löschen.

## <a name="supporting-interfaces"></a>Unterstützende Schnittstellen

Das Feature zum vorläufigen Löschen ist über die [REST-API](/rest/api/apimanagement/2021-01-01-preview/api-management-service/restore) verfügbar.

> [!TIP]
> Tipps und Tools für den Aufruf von Azure-REST-APIs finden Sie unter [Azure-REST-API-Referenz](/rest/api/azure/).

| Vorgang | BESCHREIBUNG | API Management-Namespace | API-Mindestversion |
|--|--|--|--|
| [Erstellen oder Aktualisieren](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update) | Erstellt oder aktualisiert einen API Management-Dienst.  | API Management-Dienst | Any |
| [Erstellen oder Aktualisieren](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update), wobei die `restore`-Eigenschaft auf **true** festgelegt ist. | Hebt das Löschen des API Management-Diensts auf, wenn er zuvor vorläufig gelöscht wurde. Wenn `restore` angegeben und auf `true` festgelegt wird, werden alle anderen Eigenschaften ignoriert.  | API Management-Dienst |  2020-06-01-preview |
| [Löschen](/rest/api/apimanagement/2021-01-01-preview/api-management-service/delete) | Löscht einen vorhandenen API Management-Dienst. | API Management-Dienst | 2020-06-01-preview|
| [Get By Name](/rest/api/apimanagement/2021-01-01-preview/deleted-services/get-by-name) (Nach Name abrufen): | Einen vorläufig gelöschten API Management-Dienst über den Namen abrufen. | Gelöschte Dienste | 2020-06-01-preview |
| [List By Subscription](/rest/api/apimanagement/2021-01-01-preview/deleted-services/list-by-subscription) (Nach Abonnement auflisten): | Listet alle vorläufig gelöschten Dienste auf, die für das Aufheben der Löschung für das angegebene Abonnement verfügbar sind. | Gelöschte Dienste | 2020-06-01-preview
| [Bereinigen](/rest/api/apimanagement/2021-01-01-preview/deleted-services/purge) | Bereinigt den API Management-Dienst (löscht ihn ohne die Möglichkeit, ihn wiederherzustellen). | Gelöschte Dienste | 2020-06-01-preview

## <a name="soft-delete-behavior"></a>Verhalten des vorläufigen Löschens

Sie können jede beliebige API-Version zum Erstellen Ihrer API Management-Instanz verwenden. Sie müssen jedoch `2020-06-01-preview` oder spätere Versionen verwenden, um Ihre APIM-Instanz vorläufig zu löschen (und haben die Möglichkeit, sie wiederherzustellen).

Nach dem Löschen einer API Management-Instanz befindet sich der Dienst in einem gelöschten Zustand, sodass er für APIM-Vorgänge nicht zugänglich ist. In diesem Zustand kann die APIM-Instanz nur aufgelistet, wiederhergestellt oder bereinigt (endgültig gelöscht) werden.

Gleichzeitig wird Azure die Löschung der zugrundeliegenden Daten, die der APIM-Instanz entsprechen, zur Ausführung nach dem vorgegebenen Aufbewahrungszeitraum (48 Stunden) planen. Der der Instanz entsprechende DNS-Eintrag wird ebenfalls für die Dauer des Aufbewahrungsintervalls beibehalten. Der Name einer vorläufig gelöschten API Management-Instanz kann erst nach Ablauf des Aufbewahrungszeitraums erneut verwendet werden.

Wenn Ihre APIM-Instanz nicht innerhalb von 48 Stunden wiederhergestellt wird, wird sie endgültig gelöscht (nicht wiederherstellbar). Sie können auch [Bereinigen](#purge-a-soft-deleted-apim-instance) (endgültig löschen) für Ihre APIM-Instanz unter Verzicht auf den Aufbewahrungszeitraum für vorläufiges Löschen auswählen.

## <a name="list-deleted-apim-instances"></a>Auflisten gelöschter APIM-Instanzen

Sie können überprüfen, ob eine vorläufig gelöschte APIM-Instanz zum Wiederherstellen verfügbar ist, indem Sie entweder die Vorgänge [Get By Name](/rest/api/apimanagement/2021-01-01-preview/deleted-services/get-by-name) (Nach Name abrufen) oder [List By Subscription](/rest/api/apimanagement/2021-01-01-preview/deleted-services/list-by-subscription) (Nach Abonnement auflisten) für vorläufiges Löschen verwenden.

### <a name="get-a-soft-deleted-instance-by-name"></a>Abrufen einer vorläufig gelöschten Instanz über den Namen

Verwenden Sie den API Management-Vorgang [Get By Name](/rest/api/apimanagement/2021-01-01-preview/deleted-services/get-by-name) (Abrufen nach Name), wobei `{subscriptionId}`, `{location}` und `{serviceName}` durch Ihr Azure-Abonnement, den Speicherort der Azure-Ressource und den Namen der API Management-Instanz ersetzt werden:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Falls zum Aufheben des Löschvorgangs verfügbar, gibt Azure einen Datensatz der APIM-Instanz zurück, der sein `deletionDate` und `scheduledPurgeDate` anzeigt. Beispiel:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Auflisten aller vorläufig gelöschten Instanzen für ein bestimmtes Abonnement

Verwenden Sie den API Management-Vorgang [List By Subscription](/rest/api/apimanagement/2021-01-01-preview/deleted-services/list-by-subscription) (Nach Abonnement auflisten), wobei Sie `{subscriptionId}` durch Ihre Abonnement-ID ersetzen:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Dies gibt eine Liste aller vorläufig gelöschten Dienste zurück, die unter dem gegebenen Abonnement zum Wiederherstellen verfügbar sind, wobei für jeden Dienst `deletionDate` und `scheduledPurgeDate` angezeigt werden.

## <a name="recover-a-deleted-apim-instance"></a>Wiederherstellen einer gelöschten APIM-Instanz

Verwenden Sie den API Management-Vorgang [Create Or Update](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update) (Erstellen oder Aktualisieren), wobei `{subscriptionId}`, `{resourceGroup}` und `{apimServiceName}` durch Ihr Azure-Abonnement, den Ressourcengruppennamen und den Namen der API Management-Instanz ersetzt werden:

```rest
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2021-01-01-preview
```

. . . und legen Sie die Eigenschaft `restore` im Anforderungstext auf `true` fest. (Wenn dieses Flag angegeben und auf *true* festgelegt wird, werden alle anderen Eigenschaften ignoriert.) Beispiel:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Bereinigen einer vorläufig gelöschten APIM-Instanz

Verwenden Sie den API Management-Vorgang [Purge](/rest/api/apimanagement/2021-01-01-preview/deleted-services/purge) (Bereinigen), wobei `{subscriptionId}`, `{location}` und `{serviceName}` durch Ihr Azure-Abonnement, den Speicherort der Azure-Ressource und den Namen der API Management-Instanz ersetzt werden:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Dadurch wird Ihre API Management-Instanz endgültig aus Azure gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über langfristige Optionen für die API Management-Sicherung und -Wiederherstellung:

- [Implementieren der Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)
