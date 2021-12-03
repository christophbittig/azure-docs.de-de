---
title: Migrieren von Daten aus einer PostgreSQL-Datenbank zu einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe
titleSuffix: Azure Arc-enabled database services
description: Migrieren von Daten aus einer PostgreSQL-Datenbank zu einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: ec41b338acbf055e8fa499ff6b4e867844b30e4d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553055"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrieren einer PostgreSQL-Datenbank zu einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe

In diesem Dokument wird beschrieben, wie Sie Ihre vorhandene PostgreSQL-Datenbank (die nicht in Azure Arc-fähigen Data Services gehostet wird) in Ihre Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe verlagern.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Weitere Überlegungen

Eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe ist die Communityversion von PostgreSQL und wird mit aktivierter CitusData-Erweiterung ausgeführt. Daher sollte jedes Tool, das mit PostgreSQL außerhalb von Azure Arc funktioniert, auch mit einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe funktionieren.


Folglich sollten Sie mit den Tools, die Sie aktuell für Postgres einsetzen, in der Lage sein, die folgenden Aktionen durchzuführen:
1. Sichern Ihrer Postgres-Datenbank auf der Instanz, die außerhalb von Azure Arc gehostet wird
2. Wiederherstellen der Datenbank in der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe

Sie müssen nur noch die folgenden Schritte ausführen:
- Zurücksetzen der Serverparameter
- Zurücksetzen der Sicherheitskontexte: Benutzer und Rollen neu erstellen, Berechtigungen zurücksetzen usw.

Um diesen Sicherungs-/Wiederherstellungsvorgang auszuführen, können Sie ein beliebiges Tool verwenden, das Sicherungen/Wiederherstellungen für Postgres ausführen kann. Zum Beispiel:
- Azure Data Studio und die zugehörige Postgres-Erweiterung
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Beispiel

Veranschaulichen wir die Schritte anhand des Tools `pgAdmin`.
Betrachten Sie folgendes Setup:
- **Quelle:**  
    Ein Postgres-Server, der lokal auf einem Bare-Metal-Server mit dem Namen JEANYDSRV ausgeführt wird. Der Server verfügt über Version 12 und hostet eine Datenbank mit dem Namen MyOnPremPostgresDB, die eine Tabelle „T1“ mit einer Zeile namens :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="Migrate-source"::: enthält.

- **Ziel:**  
    Ein Postgres-Server namens postgres01, der in einer Azure Arc-Umgebung ausgeführt wird. Der Server verfügt über Version 12. Er enthält keine Datenbank mit Ausnahme der standardmäßigen Postgres-Datenbank.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrate-destination":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Eine lokale Sicherung der Quelldatenbank erstellen

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrate-source-backup":::

Diese konfigurieren:
1. Einen Dateinamen zuweisen: **MySourceBackup**
2. Das Format auf **Benutzerdefiniert**
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="Migrate-source-backup-configure"::: festlegen

Die Sicherung wird erfolgreich abgeschlossen:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrate-source-backup-completed":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Erstellen einer leeren Datenbank im Zielsystem in Ihrer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe

> [!NOTE]
> Um eine Postgres-Instanz im Tool `pgAdmin` zu registrieren, müssen Sie die öffentliche IP-Adresse Ihrer Instanz in Ihrem Kubernetes-Cluster verwenden und den Port und den Sicherheitskontext entsprechend festlegen. Sie finden diese Informationen in der Zeile des `psql`-Endpunkts, nachdem Sie folgenden Befehl ausgeführt haben:

```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```
Es wird eine ähnliche Ausgabe wie die folgende zurückgegeben:
```console
{
  "instances": [
    {
      "endpoints": [
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
],
"engine": "PostgreSql",
"name": "postgres01"
}
  ],
  "namespace": "arc"
}
```

Wir nennen die Zieldatenbank **RESTORED_MyOnPremPostgresDB**.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrate-destination-db-create" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Die Datenbank im Arc-Setup wiederherstellen

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-db-restore":::

Die Wiederherstellung konfigurieren:
1. Auf die Datei verweisen, die die wiederherzustellende Sicherung enthält: **MySourceBackup**
2. Das Format bei **Benutzerdefiniert oder tar**
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrate-db-restore-configure"::: belassen

3. Klicken Sie auf **Wiederherstellen**.  

   Die Wiederherstellung ist erfolgreich.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrate-db-restore-completed":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Überprüfen, ob die Datenbank erfolgreich in Ihrer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe wiederhergestellt wurde

Verwenden Sie eine der folgenden beiden Methoden:

**Von `pgAdmin`:**  

Erweitern Sie die Postgres-Instanz, die im Azure Arc-Setup gehostet wird. Die Tabelle wird in der wiederhergestellten Datenbank angezeigt. Wenn Sie die Daten auswählen, wird dieselbe Zeile wie in der lokalen Instanz angezeigt:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrate-db-restore-verification":::

**Von `psql` innerhalb Ihres Azure Arc-Setups:**  

In Ihrem Arc-Setup können Sie `psql` verwenden, um eine Verbindung mit der Postgres-Instanz herzustellen, den Datenbankkontext auf `RESTORED_MyOnPremPostgresDB` festzulegen und die Daten abzufragen:

1. Listen Sie die Endpunkte auf, um Ihre `psql` Verbindungszeichenfolge zu bilden:

   ```Az CLI
   az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
   ```

   ```Az CLI
   {
     "instances": [
       {
         "endpoints": [
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ],
   "engine": "PostgreSql",
   "name": "postgres01"
   }
     ],
     "namespace": "arc"
   }
   ```

1. Verwenden Sie aus der `psql`-Verbindungszeichenfolge den Parameter `-d`, um den Datenbanknamen anzugeben. Mit dem folgenden Befehl werden Sie zur Eingabe des Kennworts aufgefordert:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` verbinden

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Wenn Sie die Tabelle auswählen, werden die Daten angezeigt, die Sie von der lokalen Postgres-Instanz wiederhergestellt haben:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Die Leistungsvorteile von PostgreSQL Hyperscale mit Azure Arc-Unterstützung werden erst vollständig sichtbar, wenn Sie aufskalieren und Daten auf den Workerknoten der PostgreSQL Hyperscale-Servergruppe horizontal partitionieren bzw. verteilen. Weitere Informationen finden Sie unter [Nächste Schritte](#next-steps).
>
> - Es ist derzeit nicht möglich, für eine vorhandene Postgres-Instanz, die lokal oder in einer anderen Cloud ausgeführt wird, ein „Onboarding in Azure Arc“ durchzuführen. Anders ausgedrückt: Es ist nicht möglich, einen „Azure Arc-Agent“ auf Ihrer vorhandenen Postgres-Instanz zu installieren, um diese in ein Postgres-Setup mit Azure Arc-Unterstützung zu verwandeln. Stattdessen müssen Sie eine neue Postgres-Instanz erstellen und Daten auf die Instanz übertragen. Sie können dazu das oben aufgeführte Verfahren oder ein ETL-Tool Ihrer Wahl verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die Konzepte und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und von der gesamten Leistung von Azure Database for PostgreSQL Hyperscale zu profitieren:
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> *Überspringen Sie in diesen Dokumenten die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for Postgres-Instanz für Hyperscale (Citus)** . Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Instanz von PostgreSQL Hyperscale mit Azure Arc-Unterstützung übertragbar.

- [Skalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe](scale-out-in-postgresql-hyperscale-server-group.md)
