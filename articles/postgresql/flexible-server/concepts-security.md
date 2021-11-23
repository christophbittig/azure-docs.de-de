---
title: Sicherheit in Azure Database for PostgreSQL – Flexible Server
description: In diesem Artikel erfahren Sie etwas über die Sicherheit in der Bereitstellungsoption „Flexible Server“ für Azure Database for PostgreSQL.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 10/01/2021
ms.openlocfilehash: f31c013bbdf12f34cd73495e3885e17a35b368ef
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312284"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Sicherheit in Azure Database for PostgreSQL – Flexible Server

Zum Schutz der Daten auf Ihrem Azure Database for PostgreSQL-Server gibt es mehrere Sicherheitsebenen. In diesem Artikel werden diese Sicherheitsoptionen beschrieben.

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

Azure Database for PostgreSQL verschlüsselt Daten auf zwei Arten:

- **Daten während der Übertragung**: Azure Database for PostgreSQL verschlüsselt Daten während der Übertragung mit Secure Sockets Layer und Transport Layer Security (SSL/TLS). Verschlüsselung wird standardmäßig erzwungen. Ausführlichere Informationen finden Sie in [diesem Leitfaden](how-to-connect-tls-ssl.md). Zur Erhöhung der Sicherheit können Sie die [SCRAM-Authentifizierung](how-to-connect-scram.md) aktivieren.

   Obwohl davon abgeraten wird, haben Sie bei Bedarf die Möglichkeit, TLS/SSL für Verbindungen mit Azure Database for PostgreSQL – Flexible Server zu deaktivieren, indem Sie den Serverparameter `require_secure_transport` auf OFF festlegen. Sie können die TLS-Version auch über die Serverparameter `ssl_min_protocol_version` und `ssl_max_protocol_version` festlegen.


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

Beispiel:

```SQL
postgres=> create role demouser with password 'password123';
```

Sie können die Liste mit den Rollen auf Ihrem Server in regelmäßigen Abständen überprüfen. Beispielsweise können Sie eine Verbindung herstellen, indem Sie den `psql`-Client verwenden, und die Tabelle `pg_roles` abfragen, in der alle Rollen und die zugehörigen Berechtigungen, z. B. zusätzliche Rollen erstellen, Datenbanken erstellen, Replikation usw., aufgeführt sind. 

```SQL
postgres=> \x
Expanded display is on.
postgres=> select * from pg_roles where rolname='demouser';
-[ RECORD 1 ]--+---------
rolname        | demouser
rolsuper       | f
rolinherit     | t
rolcreaterole  | f
rolcreatedb    | f
rolcanlogin    | f
rolreplication | f
rolconnlimit   | -1
rolpassword    | ********
rolvaliduntil  |
rolbypassrls   | f
rolconfig      |
oid            | 24827

```

Für Flexible Server ist auch die [Überwachungsprotokollierung](concepts-audit.md) verfügbar, um Aktivitäten in Ihren Datenbanken nachverfolgen zu können. 

> [!NOTE]
> Für Azure Database for PostgreSQL – Flexible Server wird [Schutz von Microsoft Defender für Cloud](../../security-center/azure-defender.md) derzeit nicht unterstützt. 

## <a name="updating-passwords"></a>Aktualisieren von Kennwörtern

Eine bewährte Methode zur Erhöhung der Sicherheit besteht darin, Ihr Administratorkennwort und die Kennwörter für Datenbankbenutzer regelmäßig zu rotieren. Wir empfehlen Ihnen, sichere Kennwörter mit Groß- und Kleinbuchstaben, Zahlen und Sonderzeichen zu verwenden.

### <a name="reset-administrator-password"></a>Zurücksetzen des Administratorkennworts

Befolgen Sie die [Anleitung](./how-to-manage-server-portal.md#reset-admin-password) zum Zurücksetzen des Administratorkennworts.

### <a name="update-database-user-password"></a>Aktualisieren eines Kennworts für Datenbankbenutzer

Sie können Clienttools verwenden, um die Kennwörter von Datenbankbenutzern zu aktualisieren. Beispiel:
```SQL
postgres=> alter role demouser with password 'Password123!';
ALTER ROLE
```
## <a name="next-steps"></a>Nächste Schritte
- Aktivieren Sie [Firewallregeln für IP-Adressen](concepts-firewall-rules.md) bei Netzwerken mit öffentlichem Zugriff.
- Informieren Sie sich über [Netzwerke mit privatem Zugriff mit Azure Database for PostgreSQL – Flexible Server](concepts-networking.md).
- Informieren Sie sich über [Azure Active Directory-Authentifizierung](../concepts-aad-authentication.md) in Azure Database for PostgreSQL.
