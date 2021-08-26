---
title: Sicherheit in Azure Database for PostgreSQL – Flexible Server
description: In diesem Artikel erfahren Sie mehr über die Sicherheit Flexible Server-Bereitstellungsoption für Azure Database for PostgreSQL – Flexible Server
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 489ae3e9593ed5fa0865fb6f3fbe2eb617113d71
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181492"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Sicherheit in Azure Database for PostgreSQL – Flexible Server

Zum Schutz der Daten auf Ihrem Azure Database for PostgreSQL-Server stehen mehrere Sicherheitsebenen zur Verfügung. In diesem Artikel werden diese Sicherheitsoptionen beschrieben.

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

### <a name="in-transit"></a>Während der Übertragung
 Mit Azure Database for PostgreSQL werden Ihre Daten durch Verschlüsselung während der Übertragung mit Transport Layer Security geschützt. Die Verschlüsselung (SSL/TLS) wird standardmäßig erzwungen.

### <a name="at-rest"></a>Ruhende Daten
Der Azure Database for PostgreSQL-Dienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten, einschließlich Sicherungen, werden auf dem Datenträger verschlüsselt (einschließlich der temporären Dateien, die während der Ausführung von Abfragen erstellt wurden). Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Dies ähnelt anderen Verschlüsselungstechnologien für ruhende Daten wie Transparent Data Encryption (TDE) in SQL Server Oracle-Datenbanken. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.


## <a name="network-security"></a>Netzwerksicherheit

Sie können zwei Hauptnetzwerkoptionen auswählen, wenn Sie Ihre Azure Database for PostgreSQL – Flexible Server ausführen. den privaten Zugriff (VNET-Integration) und den öffentlichen Zugriff (zugelassene IP-Adressen) . Durch die Nutzung des privaten Zugriffs wird Ihr flexibler Server in Ihrem Azure-Virtual Network bereitgestellt. Virtuelle Azure-Netzwerke ermöglichen eine private und sichere Netzwerkkommunikation. Ressourcen in einem virtuellen Netzwerk können über private IP-Adressen kommunizieren.
Auf Ihren flexiblen Server wird über einen öffentlichen Endpunkt zugegriffen. Der öffentliche Endpunkt ist eine öffentlich auflösbare DNS-Adresse, auf die der Zugriff durch eine Firewall geschützt werden kann, die standardmäßig alle Verbindungen blockiert. 



### <a name="ip-firewall-rules"></a>IP-Firewallregeln
IP-Firewallregeln gewähren Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie in der [Übersicht über Firewallregeln](concepts-firewall-rules.md).


### <a name="private-vnet-access"></a>Privater VNET-Zugriff
Sie können Ihren flexiblen Server in Ihrer Azure Virtual Network-Instanz bereitstellen. Virtuelle Azure-Netzwerke ermöglichen eine private und sichere Netzwerkkommunikation. Weitere Informationen finden Sie unter [flexibler Server](concepts-networking.md)

### <a name="network-security-groups-nsg"></a>Netzwerksicherheitsgruppen (NSGs)
Mit Sicherheitsregeln in Netzwerksicherheitsgruppen können Sie den Typ des ein- und ausgehenden Netzwerkdatenverkehrs von Subnetzen virtueller Netzwerke und Netzwerkschnittstellen filtern.  Weitere Informationen finden Sie unter [Übersicht über Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md)

## <a name="access-management"></a>Zugriffsverwaltung

Beim Erstellen des Azure Database for PostgreSQL-Servers geben Sie Anmeldeinformationen für eine Administratorrolle ein. Diese Administratorrolle kann verwendet werden, um zusätzliche [PostgreSQL-Rollen](https://www.postgresql.org/docs/current/user-manag.html) zu erstellen.

Mithilfe von [Azure Active Directory (AAD)-Authentifizierung](../concepts-aad-authentication.md) können Sie auch eine Verbindung mit dem Server herstellen.


### <a name="azure-defender-protection"></a>Azure Defender-Datenschutz

 Azure Database for PostgreSQL -Flexible unterstützt derzeit die [Azure Defender Protection](../../security-center/azure-defender.md) nicht. 


Aktivitäten in Ihren Datenbanken können mithilfe der [Überwachungsprotokollierung](../concepts-audit.md) nachverfolgt werden. 


## <a name="next-steps"></a>Nächste Schritte
  - Aktivieren von Firewallregeln für [IPs](concepts-firewall-rules.md) für Netzwerke mit öffentlichem Zugriff
  - Informationen zu [Netzwerken mit privatem Zugriff mit Azure Database for PostgreSQL – Flexible Server](concepts-networking.md)
  - Informieren Sie sich über [Azure Active Directory-Authentifizierung](../concepts-aad-authentication.md) in Azure Database for PostgreSQL.