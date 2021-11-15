---
title: Azure IoT Hub-Unterstützung für virtuelle Netzwerke
description: Hier erfahren Sie mehr über die Verwendung von Konnektivitätsmustern für virtuelle Netzwerke mit IoT Hub.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: jlian
ms.openlocfilehash: bd5a88bb8fc47e5f4815aea57544d6093281999b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258843"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub-Unterstützung für virtuelle Netzwerke mit Private Link und verwalteter Identität

Standardmäßig werden IoT Hub-Hostnamen einem öffentlichen Endpunkt mit einer öffentlich routingfähigen IP-Adresse über das Internet zugeordnet. Dieser öffentliche IoT Hub-Endpunkt wird von mehreren Kunden gemeinsam genutzt, und sämtliche IoT-Geräte in WANs und lokalen Netzwerken können darauf zugreifen.

![Öffentlicher IoT Hub-Endpunkt](./media/virtual-network-support/public-endpoint.png)

IoT Hub-Features wie das [Nachrichtenrouting](./iot-hub-devguide-messages-d2c.md), der [Dateiupload](./iot-hub-devguide-file-upload.md) und das [Importieren und Exportieren von Geräten per Massenvorgang](./iot-hub-bulk-identity-mgmt.md) erfordern ebenfalls Konnektivität zwischen IoT Hub und einer kundeneigenen Azure-Ressource über den öffentlichen Endpunkt. Diese Konnektivitätspfade bilden gemeinsam den ausgehenden Datenverkehr von IoT Hub zu Kundenressourcen.

Möglicherweise möchten Sie die Konnektivität mit Ihren Azure-Ressourcen (einschließlich IoT Hub) über ein VNET einschränken, das Sie besitzen und betreiben. Zu den Gründen gehören die folgenden:

* Netzwerkisolation für Ihren IoT-Hub durch Unterbinden der Konnektivität mit dem öffentlichen Internet.

* Aktivieren einer privaten Konnektivitätsumgebung von Ihren lokalen Netzwerkressourcen, um sicherzustellen, dass Ihre Daten und der Datenverkehr direkt an das Azure-Backbone-Netzwerk übertragen wird

* Verhindern der Exfiltration von Angriffen von vertraulichen, lokalen Netzwerken 

* Befolgen bewährter Azure-Konnektivitätsmuster mithilfe [privater Endpunkte](../private-link/private-endpoint-overview.md)

In diesem Artikel wird beschrieben, wie Sie diese Ziele erreichen, indem Sie [Azure Private Link](../private-link/private-link-overview.md) für eingehende Verbindungen mit IoT Hub verwenden und für ausgehende Verbindungen von IoT Hub mit anderen Azure-Ressourcen Ausnahmen für vertrauenswürdige Microsoft-Dienste definieren.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Eingehende Konnektivität mit IoT Hub mithilfe von Azure Private Link

Ein privater Endpunkt ist eine private IP-Adresse, die in einem kundeneigenen VNET zugeordnet ist, über das eine Azure-Ressource erreichbar ist. Mit Azure Private Link können Sie einen privaten Endpunkt für Ihren IoT-Hub einrichten und damit den Diensten in Ihrem VNET erlauben, IoT Hub zu erreichen, ohne dass Datenverkehr an den öffentlichen IoT Hub-Endpunkt gesendet werden muss. Ebenso können Ihre lokalen Geräte ein [virtuelles privates Netzwerk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Peering verwenden, um eine Verbindung mit Ihrem VNET und Ihrer IoT Hub-Instanz (über den privaten Endpunkt) herzustellen. So können Sie die Konnektivität mit den öffentlichen Endpunkten Ihres IoT-Hubs einschränken oder vollständig blockieren, indem Sie [IoT Hub-IP-Filter](./iot-hub-ip-filtering.md) oder [die Funktion zum Umschalten des Zugriffs über öffentliche Netzwerke](iot-hub-public-network-access.md) verwenden. Auf diese Weise erfolgen Verbindungen mit Ihrem Hub ausschließlich über den privaten Endpunkt für Geräte. Der Schwerpunkt dieses Setups liegt auf Geräten, die sich in einem lokalen Netzwerk befinden. Dieses Setup wird nicht für Geräte empfohlen, die in einem WAN bereitgestellt werden.

![IoT Hub des virtuellen Netzwerks – Eingang](./media/virtual-network-support/virtual-network-ingress.png)

Vergewissern Sie sich, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie fortfahren:

* Sie haben ein [Azure-VNET](../virtual-network/quick-create-portal.md) mit einem Subnetz erstellt, in dem der private Endpunkt erstellt wird.

* Richten Sie für Geräte, die in lokalen Netzwerken betrieben werden, Verbindungen mit Ihrem Azure-VNET über ein [virtuelles privates Netzwerk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder per privatem [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Peering ein.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Einrichten eines privaten Endpunkts für eingehenden IoT Hub-Datenverkehr

Ein privater Endpunkt kann für IoT Hub-Geräte-APIs (z. B. D2C-Nachrichten) sowie Dienst-APIs (z. B. Erstellen und Aktualisieren von Geräten) verwendet werden.

1. Klicken Sie im Azure-Portal auf **Netzwerk**, dann auf **Privater Zugriff** und anschließend auf die Option **+ Privaten Endpunkt erstellen**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Screenshot, der zeigt, wo ein privater Endpunkt für IoT Hub hinzugefügt wird" border="true":::

1. Geben Sie das Abonnement, die Ressourcengruppe, den Namen und die Region an, in der der neue private Endpunkt erstellt werden soll. Idealerweise sollte ein privater Endpunkt in der gleichen Region wie Ihr Hub erstellt werden.

1. Klicken Sie auf **Weiter: Ressource**, und geben Sie das Abonnement für die IoT Hub-Ressource an. Wählen Sie dann **"Microsoft.Devices/IotHubs"** als Ressourcentyp, den IoT Hub-Namen als **Ressource** und **iotHub** als Zielunterressource aus.

1. Klicken Sie auf **Weiter: Konfiguration**, und stellen Sie Ihr virtuelles Netzwerk und Subnetz bereit, in dem der private Endpunkt erstellt werden soll. Klicken Sie bei Bedarf auf die Option für die Integration in eine private Azure-DNS-Zone.

1. Klicken Sie auf **Weiter: Tags**, und stellen Sie optional Tags für Ihre Ressource bereit.

1. Klicken Sie auf **Überprüfen und erstellen**, um die Private Link-Ressource zu erstellen.

### <a name="built-in-event-hub-compatible-endpoint"></a>Integrierter, mit Event Hub kompatibler Endpunkt 

Auf den [integrierten, mit Event Hub kompatiblen Endpunkt](iot-hub-devguide-messages-read-builtin.md) kann auch über den privaten Endpunkt zugegriffen werden. Bei konfigurierter privater Verbindung sollte für den integrierten Endpunkt eine zusätzliche Verbindung für private Endpunkte angezeigt werden. Dabei handelt es sich um das Element mit `servicebus.windows.net` im vollqualifizierten Domänennamen.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Abbildung: Zwei private Endpunkte mit jeweils angegebener privater IoT Hub-Verbindung":::

Der [IP-Filter](iot-hub-ip-filtering.md) von IoT Hub kann optional den öffentlichen Zugriff auf den integrierten Endpunkt steuern. 

Wenn Sie den Zugriff des öffentlichen Netzwerks auf Ihren IoT-Hub vollständig blockieren möchten, [deaktivieren Sie den öffentlichen Netzwerkzugriff](iot-hub-public-network-access.md), oder verwenden Sie IP-Filter, um alle IP-Adressen zu blockieren, und wählen Sie die Option zum Anwenden von Regeln auf den integrierten Endpunkt aus.

### <a name="pricing-for-private-link"></a>Preise für Private Link

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Ausgehende Konnektivität von IoT Hub zu anderen Azure-Ressourcen

IoT Hub kann für das [Nachrichtenrouting](./iot-hub-devguide-messages-d2c.md), den [Dateiupload](./iot-hub-devguide-file-upload.md) und das [Importieren und Exportieren von Geräten per Massenvorgang](./iot-hub-bulk-identity-mgmt.md) über den öffentlichen Endpunkt der Ressourcen eine Verbindung mit Azure Blob Storage-, Event Hub- und Service Bus-Ressourcen herstellen. Durch Binden einer Ressource an ein VNET wird standardmäßig die Konnektivität mit dieser Ressource blockiert. Dies führt dazu, dass diese Konfiguration IoT Hub daran hindert, Daten an Ihre Ressourcen zu senden. Um dieses Problem zu beheben, müssen Sie die Konnektivität von Ihrer IoT Hub-Ressource mit Ihren Speicherkonto-, Event Hub- oder Service Bus-Ressourcen über die Option **Vertrauenswürdige Microsoft-Dienste** aktivieren. 

Damit andere Dienste Ihren IoT-Hub als vertrauenswürdigen Microsoft-Dienst finden können, muss Ihr Hub die verwaltete Identität verwenden. Sobald eine verwaltete Identität bereitgestellt ist, müssen Sie der verwalteten Identität Ihres Hubs die Azure RBAC-Berechtigung für den Zugriff auf Ihren benutzerdefinierten Endpunkt erteilen. Befolgen Sie den Artikel [Unterstützung verwalteter Identitäten in IoT-Hub](./iot-hub-managed-identity.md), um eine verwaltete Identität mit der Azure RBAC-Berechtigung bereitzustellen und den benutzerdefinierten Endpunkt zu Ihrem IoT-Hub hinzuzufügen. Stellen Sie sicher, dass Sie die Ausnahme für vertrauenswürdige Microsoft-Erstanbieter aktivieren, um den Zugriff Ihres IoT-Hubs auf den benutzerdefinierten Endpunkt zu ermöglichen, wenn Sie die Firewallkonfigurationen eingerichtet haben.

### <a name="pricing-for-trusted-microsoft-service-option"></a>Preise für vertrauenswürdige Microsoft-Dienstoption
Das Feature für die Ausnahme für vertrauenswürdige Microsoft-Erstanbieterdienste ist kostenlos. Die Gebühren für die bereitgestellten Speicherkonten, Event Hubs oder Service Bus-Ressourcen werden separat berechnet.
## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die unten angegebenen Links, um weitere Informationen zu IoT Hub-Features zu erhalten:

* [Nachrichtenrouting](./iot-hub-devguide-messages-d2c.md)
* [Hochladen von Dateien mit IoT Hub](./iot-hub-devguide-file-upload.md)
* [Importieren und Exportieren von IoT Hub-Geräteidentitäten per Massenvorgang](./iot-hub-bulk-identity-mgmt.md)
