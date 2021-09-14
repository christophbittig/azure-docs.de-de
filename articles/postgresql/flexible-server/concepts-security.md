---
title: Sicherheit in Azure Database for PostgreSQL – Flexible Server
description: In diesem Artikel erfahren Sie etwas über die Sicherheit in der Bereitstellungsoption „Flexible Server“ für Azure Database for PostgreSQL.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 6fcd772fce8a3f5e869f12e9b20c1219adf8155b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866853"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Sicherheit in Azure Database for PostgreSQL – Flexible Server

Zum Schutz der Daten auf Ihrem Azure Database for PostgreSQL-Server gibt es mehrere Sicherheitsebenen. In diesem Artikel werden diese Sicherheitsoptionen beschrieben.

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

Azure Database for PostgreSQL verschlüsselt Daten auf zwei Arten:

- **Daten während der Übertragung**: Azure Database for PostgreSQL verschlüsselt Daten während der Übertragung mit Secure Sockets Layer und Transport Layer Security (SSL/TLS). Verschlüsselung wird standardmäßig erzwungen.
- **Ruhende Daten**: Azure Database for PostgreSQL nutzt für die Speicherverschlüsselung das FIPS 140-2-zertifizierte Kryptografiemodul. Daten werden auf dem Datenträger verschlüsselt, einschließlich Sicherungen und der temporären Dateien, die während der Ausführung von Abfragen erstellt wurden. 

  Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Dies ähnelt anderen Verschlüsselungstechnologien für ruhende Daten wie Transparent Data Encryption in SQL Server- oder Oracle-Datenbanken. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.


## <a name="network-security"></a>Netzwerksicherheit

Wenn Sie Azure Database for PostgreSQL – Flexible Server ausführen, haben Sie zwei Hauptoptionen für das Netzwerk:

- **Privater Zugriff**: Sie können Ihren Server in einem virtuellen Azure-Netzwerk bereitstellen. Virtuelle Azure-Netzwerke ermöglichen eine private und sichere Netzwerkkommunikation. Ressourcen in einem virtuellen Netzwerk können über private IP-Adressen kommunizieren. Weitere Informationen finden Sie unter [Übersicht über den Netzwerkbetrieb bei Azure Database for PostgreSQL – Flexible Server](concepts-networking.md).

  Mit Sicherheitsregeln in Netzwerksicherheitsgruppen können Sie den Typ des ein- und ausgehenden Netzwerkdatenverkehrs von Subnetzen virtueller Netzwerke und Netzwerkschnittstellen filtern. Weitere Informationen finden Sie in der [Übersicht über Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md).

- **Öffentlicher Zugriff**: Auf den Server kann über einen öffentlichen Endpunkt zugegriffen werden. Der öffentliche Endpunkt ist eine öffentlich auflösbare DNS-Adresse. Der Zugriff darauf wird durch eine Firewall geschützt, die alle Verbindungen standardmäßig blockiert. 

  IP-Firewallregeln gewähren Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie in der [Übersicht über Firewallregeln](concepts-firewall-rules.md).

## <a name="access-management"></a>Zugriffsverwaltung

Während Sie den Azure Database for PostgreSQL-Server erstellen, geben Sie Anmeldeinformationen für eine Administratorrolle ein. Diese Administratorrolle kann zum Erstellen weiterer [PostgreSQL-Rollen](https://www.postgresql.org/docs/current/user-manag.html) genutzt werden.

Mithilfe von [Azure Active Directory-Authentifizierung](../concepts-aad-authentication.md) können Sie auch eine Verbindung mit dem Server herstellen. Aktivitäten in Ihren Datenbanken können mithilfe der [Überwachungsprotokollierung](../concepts-audit.md) nachverfolgt werden. 

> [!NOTE]
> Azure Database for PostgreSQL – Flexible Server unterstützt zurzeit den [Azure Defender-Datenschutz](../../security-center/azure-defender.md) nicht. 

## <a name="next-steps"></a>Nächste Schritte
- Aktivieren Sie [Firewallregeln für IP-Adressen](concepts-firewall-rules.md) bei Netzwerken mit öffentlichem Zugriff.
- Informieren Sie sich über [Netzwerke mit privatem Zugriff mit Azure Database for PostgreSQL – Flexible Server](concepts-networking.md).
- Informieren Sie sich über [Azure Active Directory-Authentifizierung](../concepts-aad-authentication.md) in Azure Database for PostgreSQL.