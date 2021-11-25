---
title: Verbindungsarchitektur der Azure SQL-Datenbank
description: In diesem Artikel wird die Verbindungsarchitektur in Azure SQL-Datenbank für interne und externe Datenbankverbindungen von Azure erläutert.
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: mathoma, vanto
ms.date: 01/25/2021
ms.openlocfilehash: ed77571e076dad04fc2110f688c80d4418fb840d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554140"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Verbindungsarchitektur von Azure SQL-Datenbank und Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In diesem Artikel wird die Architektur verschiedener Komponenten erläutert, die den Netzwerkdatenverkehr an einen Server in Azure SQL-Datenbank oder Azure Synapse Analytics weiterleiten. Außerdem werden andere Verbindungsrichtlinien und ihre Auswirkung auf Clients beschrieben, die eine Verbindung innerhalb und außerhalb von Azure herstellen.

Dieser Artikel gilt *nicht* für **Azure SQL Managed Instance**. Informationen dazu finden Sie unter [Konnektivitätsarchitektur für eine verwaltete Instanz](../managed-instance/connectivity-architecture-overview.md).

> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="connectivity-architecture"></a>Verbindungsarchitektur

Das folgende Diagramm bietet einen allgemeinen Überblick über die Verbindungsarchitektur.

![Diagramm mit einer allgemeinen Übersicht über die Konnektivitätsarchitektur](./media/connectivity-architecture/connectivity-overview.png)

In den folgenden Schritten wird das Herstellen einer Verbindung mit einer Azure SQL-Datenbank-Instanz beschrieben:

- Clients stellen eine Verbindung mit dem Gateway her, das über eine öffentliche IP-Adresse verfügt und an Port 1433 lauscht.
- Je nach effektiver Verbindungsrichtlinie leitet das Gateway den Datenverkehr an den richtigen Datenbankcluster um oder fungiert als Proxy.
- Innerhalb des Datenbankclusters wird der Datenverkehr an die entsprechende Datenbank weitergeleitet.

## <a name="connection-policy"></a>Verbindungsrichtlinie

Server in Azure SQL-Datenbank und Azure Synapse unterstützen diese drei Optionen zum Festlegen der Verbindungsrichtlinie des Servers:

- **Umleiten (empfohlen):** Clients stellen Verbindungen direkt mit dem Knoten her, der die Datenbank hostet. Dies führt zu geringerer Latenz und verbessertem Durchsatz. Damit dieser Modus bei Verbindungen verwendet wird, müssen Clients
  - Ausgehende Kommunikation zwischen dem Client und allen IP-Adressen für Azure SQL in der Region an Ports im Bereich zwischen 11000 und 11999 zulassen. die Diensttags für SQL verwenden, um die Verwaltung zu vereinfachen.  
  - die ausgehende Kommunikation zwischen dem Client und den IP-Adressen des Azure SQL-Datenbank-Gateways an Port 1433 zulassen.

- **Proxy:** In diesem Modus werden alle Verbindungen über die Azure SQL-Datenbank-Gateways geleitet. Dies führt zu höherer Latenz und geringerem Durchsatz. Damit dieser Modus bei Verbindungen verwendet wird, müssen Clients die ausgehende Kommunikation zwischen dem Client und den IP-Adressen des Azure SQL-Datenbank-Gateways an Port 1433 zulassen.

- **Standardwert:** Diese Verbindungsrichtlinie ist nach dem Erstellen auf allen Servern aktiv, es sei denn, Sie ändern sie explizit in `Proxy` oder `Redirect`. Die Standardrichtlinie für alle Clientverbindungen, die innerhalb von Azure hergestellt werden (z. B. über einen virtuellen Azure-Computer), ist `Redirect`. Für alle Clientverbindungen, die außerhalb von Azure hergestellt werden (z. B. über Ihre lokale Arbeitsstation), lautet sie `Proxy`.

Es wird empfohlen, die Verbindungsrichtlinie `Redirect` statt der Verbindungsrichtlinie `Proxy` zu verwenden, um die niedrigste Latenz und den höchsten Durchsatz zu erzielen. Allerdings müssen Sie die zusätzlichen Anforderungen erfüllen, um Netzwerkdatenverkehr wie oben beschrieben zu ermöglichen. Wenn es sich bei dem Client um einen virtuellen Azure-Computer handelt, lässt sich dies mithilfe von Netzwerksicherheitsgruppen (NSG) mit [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) erreichen. Wenn der Client eine Verbindung über eine lokale Arbeitsstation herstellt, müssen Sie sich möglicherweise an den Netzwerkadministrator wenden, um Netzwerkdatenverkehr über die Unternehmensfirewall zuzulassen.

## <a name="connectivity-from-within-azure"></a>Verbindung aus Azure

Wenn Sie eine Verbindung aus Azure herstellen, verfügen Ihre Verbindungen standardmäßig über die Verbindungsrichtlinie `Redirect`. Die Verbindungsrichtlinie `Redirect` bedeutet, dass nach Aufbau der TCP-Sitzung mit Azure SQL-Datenbank die Clientsitzung an den richtigen Datenbankcluster umgeleitet wird. Dabei wird die virtuelle Ziel-IP von der des Gateways für Azure SQL-Datenbank in die des Clusters geändert. Danach fließen alle nachfolgenden Pakete direkt an den Cluster, wobei das Gateway von Azure SQL-Datenbank umgangen wird. Das folgende Diagramm veranschaulicht diesen Datenverkehrfluss.

![Architekturübersicht](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Verbindung von außerhalb von Azure

Wenn Sie von außerhalb von Azure eine Verbindung herstellen, verfügen Ihre Verbindungen standardmäßig über die Verbindungsrichtlinie `Proxy`. Die Richtlinie `Proxy` bedeutet, dass die TCP-Sitzung über das Gateway von Azure SQL-Datenbank hergestellt wird und dass alle nachfolgenden Pakete über das Gateway fließen. Das folgende Diagramm veranschaulicht diesen Datenverkehrfluss.

![Diagramm der Einrichtung der TCP-Sitzung über das Gateway von Azure SQL-Datenbank und des gesamten nachfolgenden Paketflusses über das Gateway](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Öffnen Sie zusätzlich die TCP-Ports 1434 und 14000 bis 14999, um das [Herstellen einer dedizierten Administratorverbindung](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac) zu ermöglichen.

## <a name="gateway-ip-addresses"></a>Gateway-IP-Adressen

In der folgenden Tabelle sind die einzelnen Gateway-IP-Adressen und auch Gateway-IP-Adressbereiche pro Region aufgeführt.

In regelmäßigen Abständen werden Gateways, die alte Hardware verwenden, außer Betrieb genommen, und der Datenverkehr wird gemäß dem unter [Migration des Azure SQL-Datenbank-Datenverkehrs zu neueren Gateways](gateway-migration.md) beschriebenen Prozess zu neuen Gateways migriert. Kunden wird die Verwendung der **Gateway-IP-Adresssubnetze** dringend empfohlen, damit sie nicht von dieser Aktivität in einer Region betroffen sind.

> [!IMPORTANT]
> Wenn Sie sich bei Azure SQL-Datenbank oder Azure Synapse anmelden, kann eine Verbindung mit einem **beliebigen Gateway in einer Region** hergestellt werden. Lassen Sie für eine konsistente Konnektivität mit Azure SQL-Datenbank oder Azure Synapse den Netzwerkdatenverkehr an und von **ALLEN** Gateway-IP-Adressen und Gateway-IP-Adresssubnetzen für die Region zu.

| Regionsname          | Gateway-IP-Adressen | Gateway-IP-Adresssubnetze |
| --- | --- | --- |
| Australien, Mitte    | 20.36.105.0, 20.36.104.6, 20.36.104.7 | 20.36.105.32/29 |
| Australien, Mitte 2   | 20.36.113.0, 20.36.112.6 | 20.36.113.32/29 |
| Australien (Osten)       | 13.75.149.87, 40.79.161.1, 13.70.112.9 | 13.70.112.32/29, 40.79.160.32/29, 40.79.168.32/29 |
| Australien, Südosten | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 | 13.77.49.32/29 |
| Brasilien Süd         | 191.233.200.14, 191.234.144.16, 191.234.152.3 | 191.233.200.32/29, 191.234.144.32/29 |
| Kanada, Mitte       | 40.85.224.249, 52.246.152.0, 20.38.144.1 | 13.71.168.32/29, 20.38.144.32/29, 52.246.152.32/29 |
| Kanada, Osten          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 | 40.69.105.32/29|
| USA (Mitte)           | 13.67.215.62, 52.182.137.15, 104.208.21.1, 13.89.169.20 | 104.208.21.192/29, 13.89.168.192/29, 52.182.136.192/29 |
| China, Osten           | 139.219.130.35 |  52.130.112.136/29 |
| China, Osten 2         | 40.73.82.1 | 52.130.120.88/29 | 
| China, Norden          | 139.219.15.17      | 52.130.128.88/29 |
| China, Norden 2        | 40.73.50.0         | 52.130.40.64/29 |
| Asien, Osten            | 52.175.33.150, 13.75.32.4, 13.75.32.14, 20.205.77.200, 20.205.83.224  | 13.75.32.192/29, 13.75.33.192/29 |
| East US              | 40.121.158.30, 40.79.153.12, 40.78.225.32 | 20.42.65.64/29, 20.42.73.0/29, 52.168.116.64/29 |
| USA (Ost) 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0,  191.239.224.107, 104.208.150.3,  40.70.144.193 | 104.208.150.192/29, 40.70.144.192/29, 52.167.104.192/29 |
| Frankreich, Mitte       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 | 40.79.136.32/29, 40.79.144.32/29 |
| Frankreich, Süden         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 | 40.79.176.40/29, 40.79.177.32/29 |
| Deutschland, Westen-Mitte | 51.116.240.0, 51.116.248.0, 51.116.152.0 | 51.116.152.32/29, 51.116.240.32/29, 51.116.248.32/29 |
| Indien, Mitte        | 104.211.96.159, 104.211.86.30 , 104.211.86.31, 40.80.48.32, 20.192.96.32  | 104.211.86.32/29, 20.192.96.32/29 |
| Indien (Süden)          | 104.211.224.146    | 40.78.192.32/29, 40.78.193.32/29 |
| Indien, Westen           | 104.211.160.80, 104.211.144.4 | 104.211.144.32/29, 104.211.145.32/29 |
| Japan, Osten           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5, 13.78.104.32, 40.79.184.32 | 13.78.104.32/29, 40.79.184.32/29, 40.79.192.32/29 |
| Japan, Westen           | 104.214.148.156, 40.74.100.192, 40.74.97.10 | 40.74.96.32/29 |
| Korea, Mitte        | 52.231.32.42, 52.231.17.22 ,52.231.17.23, 20.44.24.32, 20.194.64.33 | 20.194.64.32/29,20.44.24.32/29, 52.231.16.32/29 |
| Korea, Süden          | 52.231.200.86, 52.231.151.96 |  |
| USA Nord Mitte     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 | 52.162.105.192/29 |
| Nordeuropa         | 40.113.93.91, 52.138.224.1, 13.74.104.113 | 13.69.233.136/29, 13.74.105.192/29, 52.138.229.72/29 |
| Norwegen, Osten          | 51.120.96.0, 51.120.96.33, 51.120.104.32, 51.120.208.32 | 51.120.96.32/29 |
| Norwegen, Westen          | 51.120.216.0       | 51.120.217.32/29 |
| Südafrika, Norden   | 102.133.152.0, 102.133.120.2, 102.133.152.32 | 102.133.120.32/29, 102.133.152.32/29, 102.133.248.32/29|
| Südafrika, Westen    | 102.133.24.0       | 102.133.25.32/29 |
| USA Süd Mitte     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1 | 20.45.121.32/29, 20.49.88.32/29, 20.49.89.32/29, 40.124.64.136/29 |
| Südostasien      | 104.43.15.0, 40.78.232.3, 13.67.16.193 | 13.67.16.192/29, 23.98.80.192/29, 40.78.232.192/29|
| Schweiz, Norden    | 51.107.56.0, 51.107.57.0 | 51.107.56.32/29 |
| Schweiz, Westen     | 51.107.152.0, 51.107.153.0 | 51.107.153.32/29 |
| VAE, Mitte          | 20.37.72.64        | 20.37.72.96/29, 20.37.73.96/29 |
| Vereinigte Arabische Emirate, Norden            | 65.52.248.0        | 40.120.72.32/29, 65.52.248.32/29 |
| UK, Süden             | 51.140.184.11, 51.105.64.0, 51.140.144.36, 51.105.72.32 | 51.105.64.32/29, 51.105.72.32/29, 51.140.144.32/29 |
| UK, Westen              | 51.141.8.11, 51.140.208.96, 51.140.208.97 | 51.140.208.96/29, 51.140.209.32/29 |
| USA, Westen-Mitte      | 13.78.145.25, 13.78.248.43, 13.71.193.32, 13.71.193.33 | 13.71.193.32/29 |
| Europa, Westen          | 40.68.37.158, 104.40.168.105, 52.236.184.163  | 104.40.169.32/29, 13.69.112.168/29, 52.236.184.32/29 |
| USA (Westen)              | 104.42.238.205, 13.86.216.196   | 13.86.217.224/29 |
| USA, Westen 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  | 13.66.136.192/29, 40.78.240.192/29, 40.78.248.192/29 |
| USA, Westen 3            | 20.150.168.0, 20.150.184.2   | 20.150.168.32/29, 20.150.176.32/29, 20.150.184.32/29 |
|                      |                    |                    |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Ändern der Verbindungsrichtlinie von Azure SQL-Datenbank für einen Server finden Sie unter [az sql server conn-policy](/cli/azure/sql/server/conn-policy).
- Weitere Informationen zum Verbindungsverhalten von Azure SQL-Datenbank für Kunden, die ADO.NET 4.5 oder höher verwenden, finden Sie unter [Andere Ports als 1433 für ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Einen allgemeinen Überblick über die Anwendungsentwicklung finden Sie unter [SQL-Datenbankanwendungsentwicklung – Übersicht](develop-overview.md).
