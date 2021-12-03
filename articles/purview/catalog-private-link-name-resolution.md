---
title: Konfigurieren der DNS-Namensauflösung für private Endpunkte
description: In diesem Artikel wird beschrieben, wie Sie einen privaten Endpunkt für Ihr Purview-Konto nutzen können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: e97ed074cd9c0fee7a2fc3b3c7795d205292da28
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254152"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Konfigurieren und Überprüfen der DNS-Namensauflösung für private Azure Purview-Endpunkte

> [!IMPORTANT]
> Wenn Sie einen privaten _Portal_-Endpunkt für Ihr Purview-Konto **vor dem 27. September 2021 um 15:30 UTC** erstellt haben, müssen Sie die erforderlichen Maßnahmen ergreifen, wie unter [DNS für private Portal-Endpunkte neu konfigurieren](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints) beschrieben. **Diese Maßnahmen müssen vor dem 12. November 2021 abgeschlossen sein. Andernfalls werden die vorhandenen privaten Portalendpunkte nicht mehr funktionieren.**

## <a name="conceptual-overview"></a>Konzeptionelle Übersicht
Eine genaue Namensauflösung ist eine wichtige Voraussetzung für die Einrichtung von privaten Endpunkten für Ihre Azure Purview-Konten. 

Möglicherweise müssen Sie die interne Namensauflösung in Ihren DNS-Einstellungen aktivieren, um die IP-Adressen der privaten Endpunkte in den vollqualifizierten Domänennamen (FQDN) von Datenquellen aufzulösen bzw. Ihren Verwaltungscomputer in das Azure Purview-Konto und die selbstgehostete Integration Runtime aufzulösen, je nach Bereitstellungsszenario.

Das folgende Beispiel zeigt die DNS-Namensauflösung von Azure Purview von außerhalb des virtuellen Netzwerks oder wenn kein privater Azure-Endpunkt konfiguriert ist.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Screenshot: Auflösung des Azure Purview-Namens von außerhalb des Unternehmensnetzwerks":::

Das folgende Beispiel zeigt die DNS-Namensauflösung von Azure Purview innerhalb des virtuellen Netzwerks.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Screenshot: Auflösung des Purview-Namens innerhalb des Unternehmensnetzwerks":::

## <a name="deployment-options"></a>Bereitstellungsoptionen 

Verwenden Sie eine der folgenden Optionen, um die interne Namensauflösung einzurichten, wenn Sie private Endpunkte für Ihr Azure Purview-Konto verwenden:

- Stellen Sie im Rahmen der Bereitstellung privater Endpunkte [neue private Azure-DNS-Zonen](#option-1---deploy-new-azure-private-dns-zones) bereit. (Dies ist die Standardoption.)
- [Verwenden Sie eine vorhandene private Azure-DNS-Zone](#option-2---use-existing-azure-private-dns-zones). Nutzen Sie diese Option, wenn Sie einen privaten Endpunkt in einem Hub-and-Spoke-Modell aus einem anderen Abonnement oder sogar innerhalb desselben Abonnements verwenden. 
- [Verwenden Sie eigene DNS-Server](#option-3---use-your-own-dns-servers), wenn Sie keine DNS-Weiterleitung nutzen und stattdessen A-Einträge direkt auf Ihren lokalen DNS-Servern verwalten.

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>Option 1: Bereitstellen neuer privater Azure-DNS-Zonen  

### <a name="deploy-new-azure-private-dns-zones"></a>Neue Azure Private DNS-Zonen einrichten
Um die interne Namensauflösung zu aktivieren, können Sie die erforderlichen Azure-DNS-Zonen in Ihrem Azure-Abonnement erstellen, in dem das Azure Purview-Konto bereitgestellt wird. 

   :::image type="content" source="media/catalog-private-link/purview-pe-dns-zones.png" alt-text="Screenshot, der DNS-Zonen zeigt.":::

Wenn Sie private Ingestion-, Portal- und Konto-Endpunkte erstellen, werden die DNS CNAME-Ressourceneinträge für Azure Purview automatisch auf einen Alias in wenigen Subdomänen mit dem Präfix `privatelink` aktualisiert:

- Standardmäßig erstellen wir während der Bereitstellung des privaten Endpunkts _Konto_ für Ihr Purview-Konto auch eine [private DNS-Zone](../dns/private-dns-overview.md), die der Subdomäne `privatelink` für Azure Purview als `privatelink.purview.azure.com` einschließlich DNS A-Ressourceneinträgen für die privaten Endpunkte entspricht.

- Während der Bereitstellung des privaten Endpunkts _portal_ für Ihr Purview-Konto erstellen wir auch eine neue private DNS-Zone, die der Subdomäne `privatelink` für Azure Purview als `privatelink.purviewstudio.azure.com` entspricht, einschließlich DNS A-Ressourceneinträge für _Web_.

- Wenn Sie private Endpunkte für die Erfassung aktivieren, werden zusätzliche DNS-Zonen für verwaltete Ressourcen benötigt. 

Die folgende Tabelle zeigt ein Beispiel für private Azure-DNS-Zonen und DNS A-Einträge, die als Teil der Konfiguration des privaten Endpunkts für ein Azure Purview-Konto bereitgestellt werden, wenn Sie während der Bereitstellung die Option _Private DNS-Integration_ aktivieren: 

Privater Endpunkt  |Privater Endpunkt zugeordnet zu  |DNS-Zone (neu)  |A-Eintrag (Beispiel) |
|---------|---------|---------|---------|
|Konto     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|Portal     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Blob)          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Erfassung   |Von Purview verwaltetes Speicherkonto (Warteschlange)         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Event Hub)         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

### <a name="validate-virtual-network-links-on-azure-private-dns-zones"></a>Überprüfen Sie virtuelle Netzwerkverbindungen auf Azure Private DNS-Zonen

Sobald die Bereitstellung des privaten Endpunkts abgeschlossen ist, vergewissern Sie sich, dass auf allen entsprechenden Azure Private DNS-Zonen ein [virtueller Netzwerklink](../dns/private-dns-virtual-network-links.md) zum virtuellen Azure-Netzwerk besteht, in dem der private Endpunkt bereitgestellt wurde.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="Screenshot, der virtuelle Netzwerklinks auf DNS-Zonen zeigt ":::.

Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

### <a name="verify-internal-name-resolution"></a>Überprüfung der internen Namensauflösung

Wenn Sie die Azure Purview-Endpunkt-URL von einem Punkt außerhalb des virtuellen Netzwerks mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt von Azure Purview aufgelöst. Bei Auflösung aus dem virtuellen Netzwerk, das den privaten Endpunkt hostet, wird die Azure Purview-Endpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Wenn ein Azure Purview-Kontoname beispielsweise "Contoso-Purview" lautet, wird er von außerhalb des virtuellen Netzwerks, das den privaten Endpunkt hostet, aufgelöst:

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Ein | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

Die DNS-Ressourceneinträge für Contoso-Purview lauten, wenn sie in dem virtuellen Netzwerk, das den privaten Endpunkt hostet, aufgelöst werden:

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | Ein | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>Option 2: Verwenden einer vorhandenen privaten Azure-DNS-Zone

### <a name="use-existing-azure-private-dns-zones"></a>Vorhandene Azure Private DNS-Zonen verwenden

Während der Bereitstellung von Azure purview private Endpunkten können Sie _Private DNS-Integration_ unter Verwendung bestehender Azure Private DNS-Zonen wählen. Dies ist eine übliche Vorgehensweise für Organisationen, in denen ein privater Endpunkt für andere Dienste in Azure verwendet wird. Stellen Sie in diesem Fall während der Bereitstellung privater Endpunkte sicher, dass Sie die vorhandenen DNS-Zonen auswählen, anstatt neue zu erstellen. 

Dieses Szenario gilt auch, wenn Ihr Unternehmen ein zentrales oder Hub-Abonnement für alle Azure Private DNS-Zonen verwendet.

Die folgende Liste zeigt die erforderlichen Azure-DNS-Zonen und A-Einträge für private Purview-Endpunkte:

> [!NOTE]
> Aktualisieren Sie alle Namen mit `Contoso-Purview`, `scaneastusabcd1234` und `atlas-12345678-1234-1234-abcd-123456789abc`mit den entsprechenden Azure-Ressourcennamen in Ihrer Umgebung. Verwenden Sie beispielsweise anstelle von `scaneastusabcd1234` den Namen Ihres verwalteten Azure Purview-Speicherkontos.

Privater Endpunkt  |Privater Endpunkt zugeordnet zu  |DNS-Zone (vorhanden)  |A-Eintrag (Beispiel) |
|---------|---------|---------|---------|
|Konto     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|Portal     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Blob)          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Erfassung   |Von Purview verwaltetes Speicherkonto (Warteschlange)         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Erfassung     |Von Purview verwaltetes Speicherkonto (Event Hub)         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Diagramm zur Azure Purview-Namensauflösung"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

Weitere Informationen finden Sie in den Szenarien [Virtual Network-Workloads ohne benutzerdefinierten DNS-Server](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) und [Lokale Workloads mit DNS-Weiterleitung](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

### <a name="verify-virtual-network-links-on-azure-private-dns-zones"></a>Überprüfen Sie virtuelle Netzwerkverbindungen auf Azure Private DNS-Zonen

Sobald die Bereitstellung des privaten Endpunkts abgeschlossen ist, vergewissern Sie sich, dass auf allen entsprechenden Azure Private DNS-Zonen ein [virtueller Netzwerklink](../dns/private-dns-virtual-network-links.md) zum virtuellen Azure-Netzwerk besteht, in dem der private Endpunkt bereitgestellt wurde.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="Screenshot, der virtuelle Netzwerklinks auf DNS-Zonen zeigt ":::.

Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

### <a name="configure-dns-forwarders-if-custom-dns-is-used"></a>Konfigurieren Sie DNS-Forwarder, wenn ein benutzerdefinierter DNS verwendet wird

Zusätzlich müssen Sie Ihre DNS-Konfigurationen im virtuellen Azure-Netzwerk validieren, in dem sich die selbst gehostete Integrations-Laufzeit-VM oder der Verwaltungs-PC befindet. 

   :::image type="content" source="media/catalog-private-link/purview-pe-custom-dns.png" alt-text="Diagramm, das den benutzerdefinierten DNS des virtuellen Azure-Netzwerks zeigt":::

- Wenn es auf _Standard_ konfiguriert ist, sind in diesem Schritt keine weiteren Maßnahmen erforderlich.

-  Wenn ein benutzerdefinierter DNS-Server verwendet wird, sollten Sie die entsprechenden DNS-Forwarder innerhalb Ihrer DNS-Server für die folgenden Zonen hinzufügen:
  
   -  Purview.azure.com
   -  Blob.core.windows.net
   -  Queue.core.windows.net
   -  Servicebus.windows.net

### <a name="verify-internal-name-resolution"></a>Überprüfung der internen Namensauflösung

Wenn Sie die Azure Purview-Endpunkt-URL von einem Punkt außerhalb des virtuellen Netzwerks mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt von Azure Purview aufgelöst. Bei Auflösung aus dem virtuellen Netzwerk, das den privaten Endpunkt hostet, wird die Azure Purview-Endpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Wenn ein Azure Purview-Kontoname beispielsweise "Contoso-Purview" lautet, wird er von außerhalb des virtuellen Netzwerks, das den privaten Endpunkt hostet, aufgelöst:

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Ein | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

Die DNS-Ressourceneinträge für Contoso-Purview lauten, wenn sie in dem virtuellen Netzwerk, das den privaten Endpunkt hostet, aufgelöst werden:

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | Ein | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-3---use-your-own-dns-servers"></a>Option 3: Verwenden eigener DNS-Server

Wenn Sie keine DNS-Weiterleitung verwenden und A-Einträge stattdessen direkt auf Ihren lokalen DNS-Servern verwalten, um die Endpunkte über ihre privaten IP-Adressen aufzulösen, müssen Sie möglicherweise die folgenden A-Einträge auf Ihren DNS-Servern erstellen.

> [!NOTE]
> Aktualisieren Sie alle Namen mit `Contoso-Purview`, `scaneastusabcd1234` und `atlas-12345678-1234-1234-abcd-123456789abc`mit den entsprechenden Azure-Ressourcennamen in Ihrer Umgebung. Verwenden Sie beispielsweise anstelle von `scaneastusabcd1234` den Namen Ihres verwalteten Azure Purview-Speicherkontos.

| Name | Typ | Wert |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | Ein | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | Ein | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| Ein | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `Contoso-Purview.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.scan.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.catalog.Purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.proxy.purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.guardian.purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `gateway.purview.azure.com` | Ein | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `manifest.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `cdn.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `hub.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `catalog.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `cseo.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `datascan.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `datashare.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `datasource.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `policy.prod.ext.web.purview.azure.com` | Ein | \<portal private endpoint IP address of Azure Purview\> |
| `sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |


## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>Überprüfen und Testen von DNS-Namensauflösung und Konnektivität 

1. Wenn Sie private Azure-DNS-Zonen verwenden, stellen Sie sicher, dass die folgenden DNS-Zonen und die entsprechenden A-Einträge in Ihrem Azure-Abonnement erstellt werden:

   |Privater Endpunkt  |Privater Endpunkt zugeordnet zu  |DNS-Zone  |A-Eintrag (Beispiel) |
   |---------|---------|---------|---------|
   |Konto     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
   |Portal     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
   |Erfassung     |Von Purview verwaltetes Speicherkonto (Blob)          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |Erfassung   |Von Purview verwaltetes Speicherkonto (Warteschlange)         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |Erfassung     |Von Purview verwaltetes Speicherkonto (Event Hub)         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. Erstellen Sie [virtuelle Netzwerkverknüpfungen](../dns/private-dns-virtual-network-links.md) in Ihren privaten Azure-DNS-Zonen, um die interne Namensauflösung in Ihren virtuellen Azure-Netzwerken zu ermöglichen.
   
3. Testen Sie von Ihrem Verwaltungs-PC und der VM der selbstgehosteten Integration Runtime aus die Namensauflösung und die Netzwerkkonnektivität mit Ihrem Azure Purview-Konto mithilfe von Tools wie Nslookup.exe und PowerShell.

Um die Namensauflösung zu testen, müssen Sie die folgenden FQDNs durch ihre privaten IP-Adressen auflösen: (Verwenden Sie anstelle von Contoso-Purview, scaneastusabcd1234 oder atlas-12345678-1234-1234-abcd-123456789abc den Hostnamen, der mit Ihrem purview-Kontonamen und den Namen der verwalteten Ressourcen verbunden ist)

- `Contoso-Purview.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

Um die Netzwerkkonnektivität zu testen, können Sie von der VM der selbstgehosteten Integration Runtime aus die PowerShell-Konsole starten und die Konnektivität unter Verwendung von `Test-NetConnection` testen. Sie müssen jeden Endpunkt über den zugehörigen privaten Endpunkt auflösen und „TcpTestSucceeded“ als TRUE auswerten. (Verwenden Sie anstelle von Contoso-Purview, scaneastusabcd1234 oder atlas-12345678-1234-1234-abcd-123456789abc den Hostnamen, der mit dem Namen Ihres Purview-Kontos und den Namen der verwalteten Ressourcen verbunden ist)

- `Test-NetConnection -ComputerName Contoso-Purview.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei der Konfiguration des privaten Endpunkts für Ihr Azure Purview-Konto](catalog-private-link-troubleshoot.md)
- [Verwalten von Datenquellen in Azure Purview](./manage-data-sources.md)
