---
title: Hinweis zur Migration des Gatewaydatenverkehrs
description: Dieser Artikel enthält Hinweise zur Migration der IP-Adressen von Azure SQL-Datenbank-Gateways
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, mathoma
ms.date: 07/01/2019
ms.openlocfilehash: c6afb13902282e1e89acb6fe7929e97994883589
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463414"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migration des Azure SQL-Datenbank-Datenverkehrs zu neueren Gateways
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Die Azure-Infrastruktur wird kontinuierlich verbessert, und Microsoft aktualisiert immer wieder die Hardware, um das bestmögliche Kundenerlebnis sicherzustellen. In den kommenden Monaten ist geplant, in einigen Regionen Gateways auf der Basis neuerer Hardwaregenerationen hinzuzufügen, Datenverkehr zu diesen Gateways zu migrieren und schließlich Gateways außer Betrieb zu nehmen, die auf älterer Hardware basieren.  

Kunden werden per Dienstintegritätsbenachrichtigungen rechtzeitig vor jeder Änderung der in den einzelnen Regionen verfügbaren Gateways informiert. Kunden können das [Azure-Portal zum Einrichten von Aktivitätsprotokollwarnungen verwenden](../../service-health/alerts-activity-log-service-notifications-portal.md).

Aktuelle Informationen finden Sie in der Tabelle mit den [Gateway-IP-Adressen von Azure SQL-Datenbank](connectivity-architecture.md#gateway-ip-addresses).

## <a name="status-updates"></a>Statusaktualisierungen

# <a name="in-progress"></a>[Vorgang wird ausgeführt](#tab/in-progress-ip)
## <a name="august-2021"></a>August 2021
Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:

- „Norwegen, Osten“: 51.120.104.32, 51.120.208.32
- „Japan, Osten“: 40.79.184.32
- „Indien, Mitte“: 40.80.48.32, 20.192.96.32

Dieses SQL-Gateway akzeptiert ab dem 2. August 2021 Kundendatenverkehr.

## <a name="june-2021"></a>Juni 2021
Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:

- Vereinigtes Königreich, Westen: 51.140.208.96, 51.140.208.97
- Südkorea, Mitte: 20.44.24.32, 20.194.64.33
- Japan, Osten: 13.78.104.32

Dieses SQL-Gateway akzeptiert ab dem 1. Juni 2021 Kundendatenverkehr.

# <a name="completed"></a>[Abgeschlossen](#tab/completed-ip)
Die folgenden Gatewaymigrationen sind abgeschlossen: 

## <a name="may-2021"></a>Mai 2021
Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:
- Vereinigtes Königreich, Süden: 51.140.144.36, 51.105.72.32  
- USA, Westen-Mitte: 13.71.193.32, 13.71.193.33 

Dieses SQL-Gateway nimmt Kundendatenverkehr ab dem 17. Mai 2021 an.

## <a name="april-2021"></a>April 2021
Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:
- USA, Osten 2: 40.70.144.193

Dieses SQL-Gateway nimmt Kundendatenverkehr ab dem 30. April 2021 an.

Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:
- Norwegen, Osten: 51.120.96.33
- Asien, Südosten: 13.67.16.193
- Südafrika, Norden: 102.133.152.32
- Südkorea, Süden: 52.231.151.96
- USA, Norden-Mitte: 52.162.105.9
- Australien, Südosten: 13.77.49.32 

Diese SQL-Gateways sollen ab dem 5. April 2021 Kundendatenverkehr akzeptieren.

## <a name="march-2021"></a>März 2021
Die folgenden SQL-Gateways in mehreren Regionen werden gerade deaktiviert:
- Brasilien, Süden: 104.41.11.5
- Asien, Osten: 191.234.2.139
- USA, Osten: 191.238.6.43
- Japan, Osten: 191.237.240.43
- Japan, Westen: 191.238.68.11
- Europa, Norden: 191.235.193.75
- USA, Süden-Mitte: 23.98.162.75
- Asien, Südosten: 23.100.117.95
- Europa, Westen: 191.237.232.75
- USA, Westen: 23.99.34.75

Es wird keine Beeinträchtigung der Kunden erwartet, da diese Gateways (die auf älterer Hardware ausgeführt werden) keinen Kundendatenverkehr weiterleiten. Die IP-Adressen für diese Gateways werden am 15. März 2021 deaktiviert.

## <a name="february-2021"></a>Februar 2021
Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:

- USA, Mitte: 13.89.169.20

Diese SQL-Gateways sollen ab dem 28. Februar 2021 Kundendatenverkehr akzeptieren.

## <a name="january-2021"></a>Januar 2021
Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:

- Australien, Mitte: 20.36.104.6 , 20.36.104.7 
- Australien, Mitte 2:  20.36.112.6 
- Brasilien, Süden: 191.234.144.16 ,191.234.152.3 
- Kanada, Osten: 40.69.105.9 ,40.69.105.10
- Indien, Mitte: 104.211.86.30, 104.211.86.31 
- Asien, Osten: 13.75.32.14 
- Frankreich, Mitte: 40.79.137.8, 40.79.145.12 
- Frankreich, Süden: 40.79.177.10 ,40.79.177.12
- Südkorea, Mitte: 52.231.17.22 ,52.231.17.23
- Indien, Westen: 104.211.144.4

Diese SQL-Gateways sollen ab dem 31. Januar 2021 Kundendatenverkehr akzeptieren.



### <a name="october-2020"></a>Oktober 2020

Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:

- Deutschland, Westen-Mitte: 51.116.240.0, 51.116.248.0

Diese SQL-Gateways sollen ab dem 12. Oktober 2020 Kundendatenverkehr akzeptieren. 

### <a name="september-2020"></a>September 2020
Neue SQL-Gateways werden den folgenden Regionen hinzugefügt. Diese SQL-Gateways sollen ab dem **15. September 2020** Kundendatenverkehr akzeptieren:

- Australien, Südosten: 13.77.48.10
- Kanada, Osten: 40.86.226.166, 52.242.30.154
- Vereinigtes Königreich, Süden: 51.140.184.11, 51.105.64.0

Vorhandene SQL-Gateways akzeptieren Datenverkehr in den folgenden Regionen. Diese SQL-Gateways sollen ab dem **15. September 2020** Kundendatenverkehr akzeptieren:

- Australien, Südosten: 191.239.192.109 und 13.73.109.251
- USA, Mitte: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 und 104.208.21.1
- Asien, Osten: 191.234.2.139, 52.175.33.150 und 13.75.32.4
- USA, Osten: 40.121.158.30, 40.79.153.12, 191.238.6.43 und 40.78.225.32
- USA, Osten 2: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 und 104.208.150.3
- Frankreich, Mitte: 40.79.137.0 und 40.79.129.1
- Japan, Westen: 104.214.148.156, 40.74.100.192, 191.238.68.11 und 40.74.97.10
- USA, Norden-Mitte: 23.96.178.199, 23.98.55.75 und 52.162.104.33
- Asien, Südosten: 104.43.15.0, 23.100.117.95 und 40.78.232.3
- USA, Westen: 104.42.238.205, 23.99.34.75 und 13.86.216.196

Neue SQL-Gateways werden den folgenden Regionen hinzugefügt. Diese SQL-Gateways sollen ab dem **10. September 2020** Kundendatenverkehr akzeptieren:

- USA, Westen-Mitte: 13.78.248.43 
- Südafrika, Norden: 102.133.120.2  

Neue SQL-Gateways werden den folgenden Regionen hinzugefügt. Diese SQL-Gateways sollen ab dem **1. September 2020** Kundendatenverkehr akzeptieren:

- Europa, Norden: 13.74.104.113 
- USA, Westen 2: 40.78.248.10 
- Europa, Westen: 52.236.184.163 
- USA, Süden-Mitte: 20.45.121.1, 20.49.88.1 

Vorhandene SQL-Gateways akzeptieren Datenverkehr in den folgenden Regionen. Diese SQL-Gateways sollen ab dem **1. September 2020** Kundendatenverkehr akzeptieren:
- Japan, Osten: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>August 2020

Neue SQL-Gateways werden den folgenden Regionen hinzugefügt:

- Australien, Osten: 13.70.112.9
- Kanada, Mitte: 52.246.152.0, 20.38.144.1 
- USA, Westen 2: 40.78.240.8

Diese SQL-Gateways sollen ab dem 10. August 2020 Kundendatenverkehr akzeptieren. 

### <a name="october-2019"></a>Oktober 2019
- Brasilien Süd
- USA (Westen)
- Europa, Westen
- East US
- USA (Mitte)
- Südostasien
- USA Süd Mitte
- Nordeuropa
- USA Nord Mitte
- Japan, Westen
- Japan, Osten
- USA (Ost) 2
- Asien, Osten

---

## <a name="impact-of-this-change"></a>Auswirkungen dieser Änderung

Die Migration des Datenverkehrs ändert ggf. die öffentliche IP-Adresse, die das DNS für Ihre Datenbank in Azure SQL-Datenbank auflöst.
Möglicherweise sind Sie betroffen, wenn Folgendes zutrifft:

- Die IP-Adresse für ein bestimmtes Gateway in Ihrer lokalen Firewall ist hartcodiert.
- Sie verfügen über Subnetze, die Microsoft.SQL als Dienstendpunkt verwenden, aber nicht mit den Gateway-IP-Adressen kommunizieren können.
- Sie verwenden die [zonenredundant Konfiguration für die Dienstebene „Universell“](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview).
- Sie verwenden die [zonenredundante Konfiguration für die Dienstebenen „Premium“ und „Unternehmenskritisch“](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability).

In den folgenden Fällen spüren Sie die Auswirkungen nicht:
 
- Sie verwenden Umleitung als Verbindungsrichtlinie.
- Sie nutzen Verbindungen mit SQL-Datenbank aus Azure heraus und über Service Tags.
- Verbindungen, die mithilfe unterstützter Versionen des JDBC-Treibers für SQL Server hergestellt werden, sind nicht betroffen. Informationen zu unterstützten JDBC-Versionen finden Sie unter [Herunterladen des Microsoft JDBC-Treibers für SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vorgehensweise, wenn Sie betroffen sind

Sie sollten ausgehenden Datenverkehr zu IP-Adressen für alle [Gateway-IP-Adressen](connectivity-architecture.md#gateway-ip-addresses) in der Region an TCP-Port 1433 zulassen. Lassen Sie außerdem den Portbereich 11000 bis 11999 zu, wenn Sie eine Verbindung von einem Client in Azure (z. B. einer Azure-VM) herstellen oder wenn Ihre Verbindungsrichtlinie auf „Umleitung“ festgelegt ist. Diese Empfehlung bezieht sich auf Clients, die Verbindungen von lokalen Standorten herstellen, und solche, die Verbindungen über Dienstendpunkte herstellen. Weitere Informationen zu Portbereichen finden Sie unter [Verbindungsarchitektur von Azure SQL](connectivity-architecture.md#connection-policy).

Bei Verbindungen von Anwendungen, die eine ältere Version des Microsoft JDBC-Treibers als 4.0 verwenden, können Fehler bei der Zertifikatüberprüfung auftreten. Bei älteren Versionen von Microsoft JDBC muss das Feld „Antragsteller“ des Zertifikats einen allgemeinen Namen (Common Name, CN) enthalten. Stellen Sie sicher, dass die Eigenschaft „hostNameInCertificate“ auf „*.database.windows.net“ festgelegt ist, um dieses Problem zu umgehen. Weitere Informationen zum Festlegen der Eigenschaft „hostNameInCertificate“ finden Sie unter [Herstellen von Verbindungen mit einer Verschlüsselung](/sql/connect/jdbc/connecting-with-ssl-encryption).

Wenn die oben genannte Lösung nicht funktioniert, stellen Sie unter der folgenden URL eine Supportanfrage für SQL-Datenbank oder SQL Managed Instance: https://aka.ms/getazuresupport.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Verbindungsarchitektur von Azure SQL](connectivity-architecture.md).
