---
title: Verwalten des Zugriffs über öffentliche Netzwerke für Azure IoT Device Provisioning Service (DPS)
description: Dokumentation zum Deaktivieren und Aktivieren des Zugriffs über öffentliche Netzwerke für Azure IoT Device Provisioning Service (DPS)
ms.author: v-stharr
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 74c22d802c022d51a1ef8b4aa231cc92661d582b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858576"
---
# <a name="manage-public-network-access-for-your-iot-device-provisioning-service"></a>Verwalten des Zugriffs über öffentliche Netzwerke für Ihren Azure IoT Device Provisioning Service

Wenn Sie den Zugriff auf [einen privaten Endpunkt für DPS in Ihrem VNet](virtual-network-support.md) einschränken möchten, deaktivieren Sie den Zugriff über öffentliche Netzwerke. Verwenden Sie hierzu das Azure-Portal oder die `publicNetworkAccess`-API. Sie können den öffentlichen Zugriff auch über das Portal oder die `publicNetworkAccess`-API zulassen.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Deaktivieren des Zugriffs über öffentliche Netzwerke im Azure-Portal

So deaktivieren Sie den Zugriff über öffentliche Netzwerke:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.
3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.
4. Wählen Sie im Menü **Einstellungen** auf der linken Seite *Netzwerk* aus.
5. Wählen Sie unter **Öffentlicher Netzwerkzugriff** die Option *Deaktiviert* aus.
6. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/iot-dps-public-network-access/disable-public-access.png" alt-text="Screenshot des Navigationspfads im Azure-Portal zum Deaktivieren des Zugriffs über öffentliche Netzwerke" :::

So aktivieren Sie den Zugriff über öffentliche Netzwerke:

1. Wählen Sie **Alle Netzwerke** aus.
2. Wählen Sie **Speichern** aus.

## <a name="access-the-dps-after-disabling-the-public-network-access"></a>Zugreifen auf den DPS nach dem Deaktivieren des Zugriffs über öffentliche Netzwerke

Nachdem der Zugriff über öffentliche Netzwerke deaktiviert wurde, kann auf die DPS-Instanz nur über deren [privaten VNet-Endpunkt mithilfe von Azure Private Link](virtual-network-support.md) zugegriffen werden. Diese Einschränkung umfasst den Zugriff über das Azure-Portal.

## <a name="dps-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>DPS-Endpunkt, IP-Adresse und Ports nach Deaktivierung des Zugriffs über öffentliche Netzwerke

DPS ist eine mehrinstanzenfähige Platform-as-a-Service-Instanz (PaaS), bei der verschiedene Kunden denselben Pool an Compute-, Netzwerk- und Speicherhardwareressourcen gemeinsam nutzen. DPS-Hostnamen werden einem öffentlichen Endpunkt mit einer öffentlich routingfähigen IP-Adresse über das Internet zugeordnet. Dieser öffentliche DPS-Endpunkt wird von verschiedenen Kunden gemeinsam genutzt, und alle IoT-Geräte in WANs und lokalen Netzwerken können darauf zugreifen. 

Die Deaktivierung des Zugriffs über öffentliche Netzwerke wird für eine bestimmte DPS-Ressource erzwungen und so die Isolation sichergestellt. Um den Dienst für andere Kundenressourcen über den öffentlichen Pfad aktiv zu halten, bleibt der öffentliche Endpunkt auflösbar, IP-Adressen bleiben erkennbar, und die Ports bleiben offen. Dies ist kein Problem, da Microsoft mehrere Sicherheitsebenen integriert, um eine vollständige Isolation zwischen Mandanten sicherzustellen. Weitere Informationen hierzu finden Sie unter [Isolation in der öffentlichen Azure-Cloud](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>IP-Filter

Wenn der Zugriff über öffentliche Netzwerke deaktiviert ist, werden alle [IP-Filterregeln](../iot-dps/iot-dps-ip-filtering.md) ignoriert. Der Grund dafür ist, dass alle IP-Adressen aus dem öffentlichen Internet blockiert werden. Wenn Sie IP-Filter nutzen möchten, verwenden Sie die Option **Ausgewählte IP-Adressbereiche**.

### <a name="turn-on-all-network-ranges"></a>Aktivieren aller Netzwerkbereiche

So aktivieren Sie alle Netzwerkbereiche:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.
3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.
4. Wählen Sie im Menü **Einstellungen** auf der linken Seite *Netzwerk* aus.
5. Wählen Sie unter **Öffentlicher Netzwerkzugriff** die Option *Alle Netzwerke* aus.
6. Wählen Sie **Speichern** aus.
