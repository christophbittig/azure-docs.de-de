---
title: Konfigurieren der DNS-Namensauflösung für private Endpunkte
description: In diesem Artikel wird beschrieben, wie Sie einen privaten Endpunkt für Ihr Purview-Konto nutzen können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b6d0b4cb58562540cd3b2024631bb9cec2d320c9
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607047"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Konfigurieren und Überprüfen der DNS-Namensauflösung für private Azure Purview-Endpunkte

## <a name="conceptual-overview"></a>Konzeptionelle Übersicht
Eine genaue Namensauflösung ist eine wichtige Voraussetzung für die Einrichtung von privaten Endpunkten für Ihre Azure Purview-Konten. 

Möglicherweise müssen Sie die interne Namensauflösung in Ihren DNS-Einstellungen aktivieren, um die IP-Adressen der privaten Endpunkte in den vollqualifizierten Domänennamen (FQDN) von Datenquellen aufzulösen bzw. Ihren Verwaltungscomputer in das Azure Purview-Konto und die selbstgehostete Integration Runtime aufzulösen, je nach Bereitstellungsszenario.

Das folgende Beispiel zeigt die DNS-Namensauflösung von Azure Purview von außerhalb des virtuellen Netzwerks oder wenn kein privater Azure-Endpunkt konfiguriert ist.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Screenshot: Auflösung des Azure Purview-Namens von außerhalb des Unternehmensnetzwerks":::

Das folgende Beispiel zeigt die DNS-Namensauflösung von Azure Purview innerhalb des virtuellen Netzwerks.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Screenshot: Auflösung des Purview-Namens innerhalb des Unternehmensnetzwerks":::

## <a name="deployment-options"></a>Bereitstellungsoptionen 

Verwenden Sie eine der folgenden Optionen zum Einrichten der internen Namensauflösung, wenn Sie private Endpunkte für Ihr Azure Purview-Konto verwenden:

-  Stellen Sie im Rahmen der Bereitstellung privater Endpunkte [neue private Azure-DNS-Zonen](#option-1---deploy-new-azure-private-dns-zones) bereit. (Dies ist die Standardoption.)
- [Verwenden Sie eine vorhandene private Azure-DNS-Zone](#option-2---use-existing-azure-private-dns-zones). Nutzen Sie diese Option, wenn Sie einen privaten Endpunkt in einem Hub-and-Spoke-Modell aus einem anderen Abonnement oder sogar innerhalb desselben Abonnements verwenden. 
- [Verwenden Sie eigene DNS-Server](#option-3---use-your-own-dns-servers), wenn Sie keine DNS-Weiterleitung nutzen und stattdessen A-Einträge direkt auf Ihren lokalen DNS-Servern verwalten.

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>Option 1: Bereitstellen neuer privater Azure-DNS-Zonen  

Um die interne Namensauflösung zu aktivieren, können Sie die erforderlichen Azure-DNS-Zonen in Ihrem Azure-Abonnement erstellen, in dem das Azure Purview-Konto bereitgestellt wird. 

Wenn Sie private Endpunkte für das Portal und das Konto erstellen, werden die DNS-CNAME-Ressourceneinträge für Azure Purview automatisch auf einen Alias in einer Unterdomäne mit dem Präfix `privatelink` aktualisiert. Standardmäßig wird außerdem eine [private DNS-Zone](../dns/private-dns-overview.md) entsprechend der Unterdomäne `privatelink` für Azure Purview als privatelink.purview.azure.com mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte erstellt. Wenn Sie private Endpunkte für die Erfassung aktivieren, werden zusätzliche DNS-Zonen für verwaltete Ressourcen benötigt. 

Die folgende Tabelle zeigt ein Beispiel für private Azure-DNS-Zonen und DNS A-Einträge, die als Teil der Konfiguration des privaten Endpunkts für ein Azure Purview-Konto bereitgestellt werden, wenn Sie während der Bereitstellung die Option _Private DNS-Integration_ aktivieren: 

Privater Endpunkt  |Privater Endpunkt zugeordnet zu  |DNS-Zone (neu)  |A-Eintrag (Beispiel) |
|---------|---------|---------|---------|
|Konto     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|Portal     |Azure Purview-Konto          |`privatelink.purview.azure.com`        |Web         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Blob)          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Erfassung   |Von Purview verwaltetes Speicherkonto (Warteschlange)         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Event Hub)         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

Wenn Sie die Azure Purview-Endpunkt-URL von einem Punkt außerhalb des virtuellen Netzwerks mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt von Azure Purview aufgelöst. Bei Auflösung aus dem virtuellen Netzwerk, das den privaten Endpunkt hostet, wird die Azure Purview-Endpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Beispiel: Wenn der Name eines Azure Purview-Kontos „PurviewA“ lautet, wird er von außerhalb des virtuellen Netzwerks aufgelöst, das den privaten Endpunkt hostet:

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Ein | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

Die DNS-Ressourceneinträge für „PurviewA“ lauten nach dem Auflösen im virtuellen Netzwerk, das den privaten Endpunkt hostet, wie folgt:

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | Ein | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>Option 2: Verwenden einer vorhandenen privaten Azure-DNS-Zone
Während der Bereitstellung privater Azure Purview-Endpunkte können Sie die Option _Private DNS-Integration_ unter Verwendung vorhandener privater Azure-DNS-Zonen auswählen. Dies ist eine übliche Vorgehensweise für Organisationen, in denen ein privater Endpunkt für andere Dienste in Azure verwendet wird. Stellen Sie in diesem Fall während der Bereitstellung privater Endpunkte sicher, dass Sie die vorhandenen DNS-Zonen auswählen, anstatt neue zu erstellen. 

Dies gilt auch, wenn Ihre Organisation ein zentrales Abonnement oder ein Hubabonnement für alle privaten Azure-DNS-Zonen verwendet.

Die folgende Liste zeigt die erforderlichen Azure-DNS-Zonen und A-Einträge für private Purview-Endpunkte:

> [!NOTE]
> Aktualisieren Sie alle Namen mit `PurviewA`, `scaneastusabcd1234` und `atlas-12345678-1234-1234-abcd-123456789abc`mit den entsprechenden Azure-Ressourcennamen in Ihrer Umgebung. Verwenden Sie beispielsweise anstelle von `scaneastusabcd1234` den Namen Ihres verwalteten Azure Purview-Speicherkontos.

Privater Endpunkt  |Privater Endpunkt zugeordnet zu  |DNS-Zone (vorhanden)  |A-Eintrag (Beispiel) |
|---------|---------|---------|---------|
|Konto     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|Portal     |Azure Purview-Konto          |`privatelink.purview.azure.com`        |Web         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Blob)          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Erfassung   |Von Purview verwaltetes Speicherkonto (Warteschlange)         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Event Hub)         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

Weitere Informationen finden Sie in den Szenarien [Virtual Network-Workloads ohne benutzerdefinierten DNS-Server](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) und [Lokale Workloads mit DNS-Weiterleitung](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Diagramm zur Azure Purview-Namensauflösung"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den FQDN für den Azure Purview-Endpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Konfigurieren Sie Ihren DNS-Server so, dass Ihre Private Link-Unterdomäne an die private DNS-Zone für das virtuelle Netzwerk delegiert wird. Alternativ können Sie die A-Einträge für `PurviewA.privatelink.purview.azure.com` mit der IP-Adresse des privaten Endpunkts konfigurieren.
Stellen Sie nach Abschluss der Bereitstellung des privaten Endpunkts sicher, dass eine [virtuelle Netzwerkverknüpfung](../dns/private-dns-virtual-network-links.md) für die Namensauflösung zwischen der entsprechenden privaten Azure-DNS-Zone und dem virtuellen Azure-Netzwerk besteht, in dem der private Endpunkt bereitgestellt wurde. 

Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

## <a name="option-3---use-your-own-dns-servers"></a>Option 3: Verwenden eigener DNS-Server

Wenn Sie keine DNS-Weiterleitung verwenden und A-Einträge stattdessen direkt auf Ihren lokalen DNS-Servern verwalten, um die Endpunkte über ihre privaten IP-Adressen aufzulösen, müssen Sie möglicherweise die folgenden A-Einträge auf Ihren DNS-Servern erstellen.

> [!NOTE]
> Aktualisieren Sie alle Namen mit `PurviewA`, `scaneastusabcd1234` und `atlas-12345678-1234-1234-abcd-123456789abc`mit den entsprechenden Azure-Ressourcennamen in Ihrer Umgebung. Verwenden Sie beispielsweise anstelle von `scaneastusabcd1234` den Namen Ihres verwalteten Azure Purview-Speicherkontos.

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | Ein | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | Ein | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| Ein | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `PurviewA.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>Überprüfen und Testen von DNS-Namensauflösung und Konnektivität 

1. Wenn Sie private Azure-DNS-Zonen verwenden, stellen Sie sicher, dass die folgenden DNS-Zonen und die entsprechenden A-Einträge in Ihrem Azure-Abonnement erstellt werden:

   |Privater Endpunkt  |Privater Endpunkt zugeordnet zu  |DNS-Zone  |A-Eintrag (Beispiel) |
   |---------|---------|---------|---------|
   |Konto     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
   |Portal     |Azure Purview-Konto          |`privatelink.purview.azure.com`        |Web         |
   |Erfassung     |Von Purview verwaltetes Speicherkonto (Blob)          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |Erfassung   |Von Purview verwaltetes Speicherkonto (Warteschlange)         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |Erfassung     |Von Purview verwaltetes Speicherkonto (Event Hub)         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. Erstellen Sie [virtuelle Netzwerkverknüpfungen](../dns/private-dns-virtual-network-links.md) in Ihren privaten Azure-DNS-Zonen, um die interne Namensauflösung in Ihren virtuellen Azure-Netzwerken zu ermöglichen.
   
3. Testen Sie von Ihrem Verwaltungs-PC und der VM der selbstgehosteten Integration Runtime aus die Namensauflösung und die Netzwerkkonnektivität mit Ihrem Azure Purview-Konto mithilfe von Tools wie Nslookup.exe und PowerShell.

Um die Namensauflösung zu testen, müssen Sie die folgenden FQDNs über ihre privaten IP-Adressen auflösen: (Verwenden Sie anstelle von PurviewA, scaneastusabcd1234 oder atlas-12345678-1234-1234-abcd-123456789abc den Hostnamen, der Ihrem Purview-Kontonamen und den Namen der verwalteten Ressourcen zugeordnet ist.)

- `PurviewA.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

Um die Netzwerkkonnektivität zu testen, können Sie von der VM der selbstgehosteten Integration Runtime aus die PowerShell-Konsole starten und die Konnektivität unter Verwendung von `Test-NetConnection` testen. Sie müssen jeden Endpunkt über den zugehörigen privaten Endpunkt auflösen und „TcpTestSucceeded“ als TRUE auswerten. (Verwenden Sie anstelle von PurviewA, scaneastusabcd1234 oder atlas-12345678-1234-1234-abcd-123456789abc den Hostnamen, der Ihrem Purview-Kontonamen und den Namen der verwalteten Ressourcen zugeordnet ist.)

- `Test-NetConnection -ComputerName PurviewA.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei der Konfiguration des privaten Endpunkts für Ihr Azure Purview-Konto](catalog-private-link-troubleshoot.md)
- [Verwalten von Datenquellen in Azure Purview](./manage-data-sources.md)
