---
title: Informationen zur Sicherung von Azure Database for PostgreSQL
description: Übersicht über Sicherung von Azure Database for PostgreSQL (Vorschau)
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: references_regions
ms.openlocfilehash: b868af4c96691c9496a0c5382d9416e784d3eb8c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219392"
---
# <a name="about-azure-database-for-postgresql-backup-preview"></a>Informationen zur Sicherung von Azure Database for PostgreSQL (Vorschau)

Azure Backup und Azure Database Services wurden zusammengeführt, um eine Sicherungslösung für Unternehmen mit Azure Database for PostgreSQL-Servern zu entwickeln, die Sicherungen für bis zu 10 Jahre aufbewahrt. Neben der langfristigen Aufbewahrung bietet die Lösung die folgenden Funktionen:

- Kundenseitig gesteuerte, geplante und bedarfsorientierte Sicherungen der einzelnen Datenbanken.
- Wiederherstellungen auf Datenbankebene auf einem beliebigen PostgreSQL-Server oder in einem beliebigen Blobspeicher.
- Zentrale Überwachung aller Vorgänge und Aufträge.
- Sicherungen werden in separaten Sicherheits- und Fehlerdomänen gespeichert. Wenn der Quellserver oder das Abonnement auf irgendeine Weise kompromittiert wird, bleiben die Sicherungen im [Azure Backup-Tresor](./backup-vault-overview.md) (in von Azure Backup verwalteten Speicherkonten) sicher.
- Die Verwendung von **pg_dump** ermöglicht eine größere Flexibilität bei Wiederherstellungen. Dadurch können Sie Wiederherstellungen über Datenbankversionen hinweg durchführen. 

Sie können diese Lösung unabhängig oder zusätzlich zur [nativen Sicherungslösung verwenden, die von Azure PostgreSQL angeboten wird](../postgresql/concepts-backup.md) und die eine Aufbewahrungsdauer von bis zu 35 Tagen ermöglicht. Die native Lösung eignet sich für operative Wiederherstellungen, wenn Sie z. B. die neuesten Sicherungen wiederherstellen möchten. Die Azure Backup-Lösung hilft Ihnen bei der Erfüllung Ihrer Konformitätsanforderungen sowie bei genaueren und flexibleren Sicherungen/Wiederherstellungen.

## <a name="support-matrix"></a>Unterstützungsmatrix

|Support  |Details  |
|---------|---------|
|Unterstützte Bereitstellungen   |  [Azure Database for PostgreSQL: Einzelserver](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Unterstützte Azure-Regionen |  USA, Osten, USA, Osten 2, USA, Mitte, USA, Süden-Mitte, USA, Westen, USA, Westen 2; USA, Westen-Mitte, Brasilien, Süden; Kanada, Mitte, Europa, Norden, Europa, Westen, Vereinigtes Königreich, Süden, Vereinigtes Königreich, Westen, Deutschland, Westen-Mitte, Schweiz, Norden, Schweiz, Westen, Asien, Osten,Asien, Südosten, Japan, Osten, Japan, Westen, Südkorea, Mitte, Südkorea, Süden, Indien, Mitte, Australien, Osten, Australien, Mitte, Australien, Mitte 2, VAE, Norden  |
|Unterstützte Azure PostgreSQL-Versionen    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Überlegungen und Einschränkungen in Bezug auf Features

- Alle Vorgänge werden nur über das Azure-Portal unterstützt. 
- Der empfohlene Grenzwert für die maximale Datenbankgröße beträgt 400 GB.
- Regionsübergreifende Sicherungen werden nicht unterstützt. Daher können Sie einen Azure PostgreSQL-Server nicht in einem Tresor in einer anderen Region sichern. Ebenso können Sie eine Sicherung nur auf einem Server wiederherstellen, der sich in derselben Region wie der Tresor befindet. Wir unterstützen jedoch abonnementübergreifende Sicherungen und Wiederherstellungen. 
- Bei der Wiederherstellung werden nur die Daten wiederhergestellt. „Rollen“ werden nicht wiederhergestellt.
- In der Vorschauversion wird empfohlen, dass Sie die Lösung nur in der Testumgebung ausführen.

## <a name="backup-process"></a>Sicherungsprozess

1. Als Sicherungsadministrator können Sie die Azure PostgreSQL-Datenbanken angeben, die Sie sichern möchten. Darüber hinaus können Sie auch die Details des Azure Key Vault angeben, in dem die Anmeldeinformationen gespeichert werden, die zum Herstellen einer Verbindung mit den angegebenen Datenbanken erforderlich sind. Diese Anmeldeinformationen werden vom Datenbankadministrator im Azure Key Vault per Seeding sicher verwaltet werden.
1. Der Sicherungsdienst überprüft dann, ob er über die [entsprechenden Berechtigungen zum Authentifizieren](#azure-backup-authentication-with-the-postgresql-server) beim angegebenen PostgreSQL-Server und zum Sichern seiner Datenbanken verfügt.
1. Azure Backup erstellt eine Workerrolle (VM) mit einer darin installierten Sicherungserweiterung, um mit dem geschützten PostgreSQL-Server zu kommunizieren. Diese Erweiterung besteht aus einem Koordinator und einem PostgreSQL-Plug-In. Der Koordinator löst Workflows für verschiedene Vorgänge aus, z. B. Sicherung und Wiederherstellung, und das Plug-In verwaltet den eigentlichen Datenfluss.
1. Zum geplanten Zeitpunkt kommuniziert der Koordinator mit dem Plug-In, damit es das Streaming der Sicherungsdaten vom PostgreSQL-Server mit **pg_dump (custom)** startet.
1. Das Plug-In sendet die Daten direkt an die von Azure Backup verwalteten Speicherkonten (maskiert durch den Azure Backup-Tresor), sodass kein Stagingspeicherort erforderlich ist. Die Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt und vom Azure Backup-Dienst in Speicherkonten gespeichert.

 :::image type="content" source="./media/backup-azure-database-postgresql-overview/backup-process.png" alt-text="Diagramm des Sicherungsvorgangs.":::

## <a name="azure-backup-authentication-with-the-postgresql-server"></a>Azure Backup-Authentifizierung beim PostgreSQL-Server

Azure Backup befolgt strenge, durch Azure festgelegte Sicherheitsrichtlinien. Es wird nicht davon ausgegangen, dass Berechtigungen für die zu sichernde Ressource vorliegen, sondern diese müssen vom Benutzer explizit erteilt werden. 

### <a name="key-vault-based-authentication-model"></a>Schlüsseltresorbasiertes Authentifizierungsmodell

Der Azure Backup-Dienst muss beim Erstellen der einzelnen Sicherungen eine Verbindung mit Azure PostgreSQL herstellen. Zum Herstellen dieser Verbindung wird zwar „Benutzername + Kennwort“ (oder Verbindungszeichenfolge) entsprechend der Datenbank verwendet, diese Anmeldeinformationen werden aber nicht mit Azure Backup gespeichert. Stattdessen müssen diese Anmeldeinformationen vom Datenbankadministrator im [Azure Key Vault als Geheimnis](../key-vault/secrets/about-secrets.md) per Seeding sicher verwaltet werden. Der Workloadadministrator ist für das Verwalten und Rotieren von Anmeldeinformationen verantwortlich. Azure Backup ruft die neuesten Geheimnisdetails aus dem Schlüsseltresor auf, um die Sicherung zu erstellen.
 
:::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-based-authentication-model.png" alt-text="Diagramm des Workload- oder Datenbankflows.":::

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-backup"></a>Satz erforderlicher Berechtigungen für die Azure PostgreSQL-Datenbanksicherung

1. Erteilen Sie der MSI des Azure Backup-Tresors die folgenden Zugriffsberechtigungen:

   - Zugriff als _Leser_ auf den Azure PostgreSQL-Server.
   - Zugriff als _Key Vault-Geheimnisbenutzer_ (oder die Berechtigungen „get“, „list“ für Geheimnisse) für Azure Key Vault.

1. Netzwerk-Sichtverbindungszugriff auf:

   - Azure PostgreSQL-Server: Das Flag **Zugriff auf Azure-Dienste zulassen** muss auf **Ja** festgelegt sein.
   - Key Vault: Das Flag **Vertrauenswürdige Microsoft-Dienste zulassen** muss auf **Ja** festgelegt sein.

1. Sicherungsberechtigungen des Datenbankbenutzers für die Datenbank

>[!Note]
>Sie können diese Berechtigungen innerhalb des Flows [Sicherung konfigurieren](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases) mit einem einzigen Klick gewähren, wenn Sie (der Sicherungsadministrator) über Schreibzugriff auf die vorgesehenen Ressourcen verfügen, oder eine ARM-Vorlage verwenden, wenn Sie nicht über die erforderlichen Berechtigungen verfügen (wenn mehrere Personas beteiligt sind). 

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-restore"></a>Satz erforderlicher Berechtigungen für die Azure PostgreSQL-Datenbankwiederherstellung

Berechtigungen für die Wiederherstellung ähneln denen, die für die Sicherung erforderlich sind, und Sie müssen die Berechtigungen für den PostgreSQL-Zielserver und den zugehörigen Schlüsseltresor erteilen. Diese Berechtigungen können derzeit nur im Flow „Sicherung konfigurieren“ und nicht inline gewährt werden. Daher müssen Sie [den Zugriff auf den Postgres-Server und den entsprechenden Schlüsseltresor manuell gewähren](#grant-access-on-the-azure-postgresql-server-and-key-vault-manually).

Stellen Sie außerdem sicher, dass der Datenbankbenutzer (entsprechend den im Schlüsseltresor gespeicherten Anmeldeinformationen) über die folgenden Wiederherstellungsberechtigungen für die Datenbank verfügt:

- ALTER USER username CREATEDB;
- Weisen Sie dem Datenbankbenutzer die Rolle _azure_pg_admin_ zu.

### <a name="azure-active-directory-based-authentication-model"></a>Azure Active Directory-basiertes Authentifizierungsmodell

Wir hatten zuvor ein anderes Authentifizierungsmodell gestartet, das vollständig auf Azure Active Directory (Azure AD) basierte. Wir stellen nun jedoch das neue Key Vault-basierte Authentifizierungsmodell (wie oben erläutert) als alternative Option bereit, um den Konfigurationsprozess zu erleichtern. 

[Laden Sie dieses Dokument herunter](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx), um ein automatisiertes Skript und entsprechende Anweisungen zur Verwendung diese Authentifizierungsmodells zu erhalten. Es erteilt einem Azure PostgreSQL-Server ausreichende Berechtigungen für die Sicherung und Wiederherstellung.

>[!Note]
>Der gesamte neue konfigurierende Schutz erfolgt nur mit dem neuen Key Vault-Authentifizierungsmodell. Alle vorhandenen Sicherungsinstanzen, die mit der Azure AD-basierten Authentifizierung konfiguriert wurden, sind jedoch weiterhin vorhanden, und es werden regelmäßige Sicherungen erstellt. Um diese Sicherungen wiederherzustellen, müssen Sie die Azure AD-basierte Authentifizierung befolgen.

## <a name="grant-access-on-the-azure-postgresql-server-and-key-vault-manually"></a>Manuelles Gewähren des Zugriffs auf den Azure PostgreSQL-Server und Azure Key Vault

Informationen zum Erteilen aller Zugriffsberechtigungen, die von Azure Backup benötigt werden, finden Sie in den folgenden Abschnitten:

### <a name="access-permissions-on-the-azure-postgresql-server"></a>Zugriffsberechtigungen für den Azure PostgreSQL-Server

1. Legen Sie den Zugriff als **Leser** für die MSI des Azure Backup-Tresors auf den Azure PostgreSQL-Server fest.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-inline.png" alt-text="Screenshot: Option zum Festlegen den Zugriff als „Leser“ für die MSI des Azure Backup-Tresors auf den Azure PostgreSQL-Server." lightbox="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-expanded.png":::

1. Netzwerk-Sichtverbindungszugriff auf den Azure PostgreSQL-Server: Legen Sie das Flag „Zugriff auf Azure-Dienste zulassen“ auf „Ja“ fest.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-inline.png" alt-text="Screenshot: Option zum Festlegen des Netzwerk-Sichtverbindungszugriffs auf den Azure PostgreSQL-Server." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-expanded.png":::

### <a name="access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server"></a>Zugriffsberechtigungen für den (dem PostgreSQL-Server zugeordneten) Azure Key Vault-Objekt

1. Legen Sie den Zugriff als **Key Vault-Geheimnisbenutzer** (oder die Berechtigungen **get**, **list** für Geheimnisse) der MSI des Azure Backup-Tresors auf das Azure Key Vault-Objekt fest. Zum Zuweisen von Berechtigungen können Sie Rollenzuweisungen oder Zugriffsrichtlinien verwenden. Es ist nicht erforderlich, die Berechtigung mit beiden Optionen hinzuzufügen, da dies nicht hilft.

   - Bei Verwenden der Autorisierung durch die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) (d. h. das Berechtigungsmodell ist auf die rollenbasierte Zugriffssteuerung in Azure festgelegt):

     - Gewähren Sie unter „Zugriffskontrolle“ den Zugriff als _Key Vault-Geheimnisbenutzer_ der MSI des Azure Backup-Tresors auf den Schlüsseltresor. Inhaber dieser Rolle können Geheimnisse lesen.
     - [Gewähren der Berechtigung zum Zugreifen auf einen Azure-Schlüsseltresor für Anwendungen mit Azure RBAC](../key-vault/general/rbac-guide.md?tabs=azure-cli).

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-inline.png" alt-text="Screenshot: Option zum Bereitstellen des Benutzerzugriffs auf das Geheimnis." lightbox="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-inline.png" alt-text="Screenshot: Option zum Erteilen des Zugriffs als „Key Vault-Geheimnisbenutzer“ der MSI des Azure Backup-Tresors auf den Schlüsseltresor." lightbox="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-expanded.png":::  

   - Verwenden von Zugriffsrichtlinien (d. h. das Berechtigungsmodell ist auf die Zugriffsrichtlinie des Tresors festgelegt):

     - Legen Sie die Get- und List-Berechtigungen für Geheimnisse fest.
     - Weitere Informationen zum [Zuweisen einer Azure Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy.md?tabs=azure-portal)

     :::image type="content" source="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-inline.png" alt-text="Screenshot: Die Option zum Erteilen von Berechtigungen mithilfe des Berechtigungsmodells ist auf das Key Vault-Zugriffsrichtlinienmodell festgelegt." lightbox="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-expanded.png":::  
 

1. Netzwerk-Sichtverbindungszugriff auf den Schlüsseltresor: Legen Sie das Flag **Vertrauenswürdige Microsoft-Dienste zulassen** auf **Ja** fest.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-inline.png" alt-text="Screenshot: Festlegen des Flags „Vertrauenswürdige Microsoft-Dienste zulassen“ auf „Ja“ für &quot;Netzwerk-Sichtverbindungszugriff im Schlüsseltresor." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-expanded.png":::

### <a name="database-users-backup-privileges-on-the-database"></a>Sicherungsberechtigungen des Datenbankbenutzers für die Datenbank

Führen Sie die folgende Abfrage im [PG admin](#using-the-pg-admin-tool)-Tool aus (ersetzen Sie _username_ durch die ID des Datenbankbenutzers):

```
DO $do$
DECLARE
sch text;
BEGIN
EXECUTE format('grant connect on database %I to %I', current_database(), 'username');
FOR sch IN select nspname from pg_catalog.pg_namespace
LOOP
EXECUTE format($$ GRANT USAGE ON SCHEMA %I TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL TABLES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON TABLES TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL SEQUENCES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON SEQUENCES TO username $$, sch);
END LOOP;
END;
```

## <a name="using-the-pg-admin-tool"></a>Verwenden des PG admin-Tools

[Laden Sie das PG admin-Tool herunter](https://www.pgadmin.org/download/), wenn Sie es noch nicht haben. Mit diesem Tool können Sie eine Verbindung mit dem Azure PostgreSQL-Server herstellen. Außerdem können Sie diesem Server Datenbanken und neue Benutzer hinzufügen.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-inline.png" alt-text="Screenshot: Prozess zum Herstellen einer Verbindung mit dem Azure PostgreSQL-Server mithilfe des PG-Verwaltungstools." lightbox="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-expanded.png":::

Erstellen Sie einen neuen Server mit einem Namen Ihrer Wahl. Geben Sie den Hostnamen/Adressnamen ein, der mit dem **Servernamen** identisch ist, der in der Azure PostgreSQL-Ressourcenansicht im Azure-Portal angezeigt wird.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-inline.png" alt-text="Screenshot: Option zum Erstellen eines neuen Servers mit dem PG admin-Tool." lightbox="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-inline.png" alt-text="Screenshot: Option zum Eingeben des Hostnamens oder des Adressnamens, der dem Servernamen entspricht." lightbox="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-expanded.png":::

Stellen Sie sicher, dass Sie den Firewallregeln die _aktuelle Client-ID-Adresse_ hinzufügen, damit die Verbindung hergestellt werden kann.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-inline.png" alt-text="Screenshot: Prozess zum Hinzufügen der aktuellen Client-ID-Adresse zu den Firewallregeln." lightbox="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-expanded.png":::

Sie können dem Server neue Datenbanken und Datenbankbenutzer hinzufügen. Für Datenbankbenutzer fügen Sie eine neue **Anmeldung bzw. Gruppenrollen** hinzu. Stellen Sie sicher, dass **Kann sich anmelden?** auf **Ja** festgelegt ist.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-inline.png" alt-text="Screenshot: Prozess zum Hinzufügen neuer Datenbanken und Datenbankbenutzer zum Server." lightbox="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-inline.png" alt-text="Screenshot: Prozess zum Hinzufügen einer neuen Anmeldung oder Gruppenrolle für Datenbankbenutzer." lightbox="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-inline.png" alt-text="Screenshot: Überprüfen, ob die Option „Kann sich anmelden“ ist auf „Ja“ festgelegt ist." lightbox="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-expanded.png":::

## <a name="next-steps"></a>Nächste Schritte

[Sicherung von Azure Database for PostgreSQL](backup-azure-database-postgresql.md)