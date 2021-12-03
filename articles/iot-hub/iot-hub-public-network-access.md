---
title: Verwalten des Zugriffs über öffentliche Netzwerke für Azure IoT Hub
description: Dokumentation zum Deaktivieren und Aktivieren des Zugriffs über öffentliche Netzwerke für IoT Hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: b77fa4d3e82114c4b52edca250aada9837157782
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068277"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Verwalten des Zugriffs über öffentliche Netzwerke für Ihren IoT-Hub

Wenn Sie den Zugriff auf nur [einen privaten Endpunkt für einen IoT-Hub in Ihrem VNet](virtual-network-support.md) einschränken möchten, deaktivieren Sie den Zugriff über öffentliche Netzwerke. Verwenden Sie hierzu das Azure-Portal oder die `publicNetworkAccess`-API. Sie können den öffentlichen Zugriff auch über das Portal oder die `publicNetworkAccess`-API zulassen.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Deaktivieren des Zugriffs über öffentliche Netzwerke im Azure-Portal

1. [Navigieren Sie zum Azure-Portal](https://portal.azure.com).
2. Navigieren Sie zu Ihrem IoT Hub. Wechseln Sie zu **Ressourcengruppen**, und wählen Sie die entsprechende Gruppe und dann Ihren IoT-Hub aus.
3. Wählen Sie im linken Menü die Option **Netzwerk** aus.
4. Wählen Sie unter „Zugriff über öffentliche Netzwerke zulassen auf“ die Option **Deaktiviert** aus.
5. Wählen Sie **Speichern** aus.

:::image type="content" source="./media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Screenshot des Navigationspfads im Azure-Portal zum Deaktivieren des Zugriffs über öffentliche Netzwerke":::

Wenn Sie den Zugriff über öffentliche Netzwerke aktivieren möchten, wählen Sie **Alle Netzwerke** und dann **Speichern** aus.

### <a name="accessing-the-iot-hub-after-disabling-the-public-network-access"></a>Zugreifen auf den IoT Hub nach Deaktivierung des Zugriffs auf das öffentliche Netzwerk

Nachdem der Zugriff auf das öffentliche Netzwerk deaktiviert wurde, ist der IoT Hub nur über den [privaten VNet-Endpunkt über Azure Private Link](virtual-network-support.md) zu erreichen. Diese Einschränkung schließt den Zugriff über das Azure-Portal mit ein, weil API-Aufrufe an den IoT Hub-Dienst direkt über Ihren Browser mit Ihren Anmeldeinformationen erfolgen.

### <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>IoT Hub-Endpunkt, IP-Adresse und Ports nach Deaktivierung des Zugriffs auf öffentliche Netzwerke

Weil IoT Hub ist eine mehrinstanzenfähige Platform-as-a-Service-Instanz (PaaS) ist, können verschiedene Kunden denselben Pool an Compute-, Netzwerk- und Speicherhardwareressourcen gemeinsam nutzen. IoT Hub-Hostnamen werden einem öffentlichen Endpunkt mit einer öffentlich routingfähigen IP-Adresse über das Internet zugeordnet. Dieser öffentliche IoT Hub-Endpunkt wird von verschiedenen Kunden gemeinsam genutzt, und alle IoT-Geräte in WANs und lokalen Netzwerken können darauf zugreifen. 

Die Deaktivierung des Zugriffs auf öffentliche Netzwerke wird für eine bestimmte IoT Hub-Ressource erzwungen und so die Isolation sichergestellt. Um den Dienst für andere Kundenressourcen über den öffentlichen Pfad aktiv zu halten, bleibt der öffentliche Endpunkt auflösbar, IP-Adressen bleiben erkennbar, und die Ports bleiben offen. Dies ist kein Problem, da Microsoft mehrere Sicherheitsebenen integriert, um eine vollständige Isolation zwischen Mandanten sicherzustellen. Weitere Informationen hierzu finden Sie unter [Isolation in der öffentlichen Azure-Cloud](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

### <a name="ip-filter"></a>IP-Filter

Wenn der Zugriff über öffentliche Netzwerke deaktiviert ist, werden alle [IP-Filterregeln](iot-hub-ip-filtering.md) ignoriert. Der Grund dafür ist, dass alle IP-Adressen aus dem öffentlichen Internet blockiert werden. Wenn Sie IP-Filter nutzen möchten, verwenden Sie die Option **Ausgewählte IP-Adressbereiche**.

### <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Fehlerkorrektur für den integrierten Event Hub-kompatiblen Endpunkt

Es gibt einen IoT Hub-Fehler, bei dem der Zugriff auf den [integrierten Event Hub-kompatiblen Endpunkt](iot-hub-devguide-messages-read-builtin.md) über das öffentliche Internet weiterhin möglich ist, wenn der Zugriff über öffentliche Netzwerke auf den IoT-Hub deaktiviert ist. Wenn Sie mehr über diesen Fehler erfahren und uns diesbezüglich kontaktieren möchten, lesen Sie [Deaktivierter Zugriff auf integrierte Azure Event Hub-Endpunkte durch Deaktivierung des Zugriffs über öffentliche Netzwerke für Azure IoT Hub](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).

## <a name="turn-on-network-access-using-azure-portal"></a>Aktivieren des Netzwerkzugriffs über das Azure-Portal

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com).
2. Navigieren Sie zu Ihrem IoT Hub. Wechseln Sie zu **Ressourcengruppen**, und wählen Sie die entsprechende Gruppe und dann Ihren Hub aus.
3. Wählen Sie im linken Menü die Option **Netzwerk** aus.
4. Wählen Sie unter „Zugriff über öffentliche Netzwerke zulassen auf“ die Option **Ausgewählte IP-Adressbereiche** aus.
5. Wählen Sie im daraufhin geöffneten Dialogfeld **IP-Filter** die Option **Client-IP-Adresse hinzufügen** aus, und geben Sie einen Namen und einen Adressbereich ein.
6. Wählen Sie **Speichern** aus. Wenn die Schaltfläche abgeblendet ist, stellen Sie sicher, dass Ihre Client-IP-Adresse bereits als IP-Filter hinzugefügt wurde.

:::image type="content" source="./media/iot-hub-publicnetworkaccess/turn-on-public-network-access.png" alt-text="Screenshot des Navigationspfads im Azure-Portal zum Aktivieren des Zugriffs über öffentliche Netzwerke":::

### <a name="turn-on-all-network-ranges"></a>Aktivieren aller Netzwerkbereiche

1. Navigieren Sie zu Ihrem IoT Hub. Wechseln Sie zu **Ressourcengruppen**, und wählen Sie die entsprechende Gruppe und dann Ihren Hub aus.
1. Wählen Sie im linken Menü die Option **Netzwerk** aus.
1. Wählen Sie unter „Zugriff über öffentliche Netzwerke zulassen auf“ die Option **Alle Netzwerke** aus.
1. Wählen Sie **Speichern** aus.

### <a name="check-iot-hub-access-using-cloud-shell"></a>Überprüfen des IoT Hub-Zugriffs mit Cloud Shell

Sie können den IoT Hub-Zugriff mithilfe von Azure Cloud Shell überprüfen. Stellen Sie sicher, dass Sie alle Netzwerkbereiche aktiviert haben, und führen Sie dann die folgenden Befehle aus. Ersetzen Sie „SubscriptionName“ durch den Namen Ihres Abonnements und „MyIoTHub“ durch den Namen Ihres Hubs.

```azurecli
  az account set -s "SubscriptionName"
  az iot hub device-identity list --hub-name "MyIoTHub"
```

```azurepowershell
  Set-AzContext -Name "SubscriptionName"
  Get-AzIoTHubDevice -IotHubName "MyIoTHub"
```
### <a name="troubleshooting"></a>Problembehandlung

Wenn Sie Probleme mit dem Zugriff auf Ihren IoT-Hub haben, ist möglicherweise Ihre Netzwerkkonfiguration die Ursache. Wenn beispielsweise beim Versuch, auf die IoT-Geräteseite zuzugreifen, die folgende Fehlermeldung angezeigt wird, überprüfen Sie auf der Seite **Netzwerk**, ob der Zugriff über öffentliche Netzwerk deaktiviert oder auf ausgewählte IP-Adressbereiche beschränkt ist.

```
  Unable to retrieve devices. Please ensure that your network connection is online and network settings allow connections from your IP address.
```

Um Zugriff auf den IoT-Hub zu erhalten, bitten Sie Ihren IT-Administrator, Ihre IP-Adresse dem IP-Adressbereich hinzuzufügen oder den Zugriff auf alle Netzwerke aus öffentlichen Netzwerken zu ermöglichen. Wenn das Problem dadurch nicht behoben werden kann, überprüfen Sie die Einstellungen Ihres lokalen Netzwerks, oder wenden Sie sich an Ihren lokalen Netzwerkadministrator, um die Konnektivität mit dem IoT Hub zu korrigieren. Manchmal kann beispielsweise ein Proxy im lokalen Netzwerk den Zugriff auf IoT Hub beeinträchtigen.

Wenn die obigen Befehle nicht funktionieren oder Sie nicht alle Netzwerkbereiche aktivieren können, wenden Sie sich an den Microsoft-Support.
