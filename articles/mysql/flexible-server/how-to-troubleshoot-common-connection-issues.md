---
title: Beheben von Verbindungsproblemen mit Azure Database for MySQL – Flexible Server
description: Erfahren Sie, wie Sie Verbindungsprobleme mit Azure Database for MySQL Flexible Server beheben.
keywords: MySQL-Verbindung, Verbindungszeichenfolge, Verbindungsprobleme, beständiger Fehler, Verbindungsfehler
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: 355f4d2d352bb343ab01805a82e9703771c22436
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467775"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Beheben von Verbindungsproblemen mit Azure Database for MySQL – Flexible Server

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Verbindungsprobleme können verschiedene Ursachen haben:

* Firewalleinstellungen
* Verbindungstimeout
* Falsche Anmeldeinformationen
* Maximales Limit für einige Azure Database for MySQL Flexible Server-Ressourcen erreicht

In diesem Artikel wird erläutert, wie Sie einige der häufigsten Fehler behandeln können und welche Schritte zur Behandlung dieser Fehler unternommen werden können.

## <a name="troubleshoot-common-errors"></a>Problembehandlung für häufige Fehler

Wenn die Anwendung dauerhaft keine Verbindung mit Azure Database for MySQL Flexible Server herstellen kann, weist dies in der Regel auf Probleme der folgenden Art hin:

* Verschlüsselte Verbindung mithilfe von TLS/SSL: Flexible Server unterstützt verschlüsselte Verbindungen mit Transport Layer Security (TLS 1.2), und alle **eingehenden Verbindungen mit TLS 1.0 und TLS 1.1 werden standardmäßig verweigert**. Sie können die Erzwingung verschlüsselter Verbindungen deaktivieren oder die TLS-Version ändern. Erfahren Sie mehr über [Verschlüsselte Konnektivität mit der Transport Layer Security (TLS 1.2) in Azure Database for MySQL – Flexible Server](./how-to-connect-tls-ssl.md).
- Flexible Server in *Privater Zugriff (VNet-Integration)* : Stellen Sie sicher, dass Sie die Verbindung vom selben virtuellen Netzwerk wie der flexible Server aus herstellen. Weitere Informationen finden Sie unter [Virtuelles Netzwerk in Azure Database for MySQL Flexible Server].<!--(./concepts-networking-virtual-network.md)-->
- Flexible Server mit *Öffentlicher Zugriff (zugelassene IP-Adressen)* , stellen Sie sicher, dass die Firewall so konfiguriert ist, dass sie Verbindungen von Ihrem Client zulässt. Weitere Informationen finden Sie unter [Erstellen und Verwalten Flexible Server-Firewallregeln mithilfe des Azure-Portals](./how-to-manage-firewall-portal.md).
* Konfiguration der Clientfirewall: Die Firewall auf dem Client muss Verbindungen mit Ihrem Datenbankserver zulassen. IP-Adressen und Ports des Servers, mit dem Sie eine Verbindung herstellen, sowie Anwendungsnamen wie MySQL müssen in einigen Firewalls zugelassen sein.
* Benutzerfehler: Möglicherweise haben Sie sich bei der Eingabe von Verbindungsparametern, z. B. dem Servernamen in der Verbindungszeichenfolge, vertippt.

### <a name="resolve-connectivity-issues"></a>Lösen von Konnektivitätsproblemen

* Weitere Informationen zur verschlüsselten Konnektivität finden Sie unter [Verschlüsselte Konnektivität mithilfe von Transport Layer Security (TLS 1.2) in Azure Database for MySQL – Flexible Server](./how-to-connect-tls-ssl.md).
* Wenn Sie **Öffentlicher Zugriff (zugelassene IP-Adressen)** verwenden, richten Sie [Firewallregeln](./how-to-manage-firewall-portal.md) ein, um die Client-IP-Adresse zuzulassen. Richten Sie nur zu Testzwecken vorübergehend eine Firewallregel mit 0.0.0.0 als erster IP-Adresse und 255.255.255.255 als letzter IP-Adresse fest. Dadurch wird der Server für alle IP-Adressen geöffnet. Wird Ihr Konnektivitätsproblem dadurch behoben, entfernen Sie die Regel, und erstellen Sie eine Firewallregel für eine entsprechend eingeschränkte IP-Adresse bzw. einen entsprechend eingeschränkten IP-Adressbereich.
* Stellen Sie für alle Firewalls zwischen Client und Internet sicher, dass Port 3306 für ausgehende Verbindungen geöffnet ist.
* Überprüfen Sie die Verbindungszeichenfolge und andere Verbindungseinstellungen. Beachten Sie die vordefinierten Verbindungszeichenfolgen auf der Seite **Verbindungszeichenfolgen**, die für Ihren Server im Azure-Portal für gängige Sprachen verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte
- [Verwenden von MySQL Workbench zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL Flexible Server](./connect-workbench.md).
- [Verwenden von PHP zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL Flexible Server](./connect-php.md).
- [Verwenden von Python zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL Flexible Server](./connect-python.md).
