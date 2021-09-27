---
title: Importieren der AdventureWorks-Beispieldatenbank in PostgreSQL Hyperscale mit Azure Arc-Unterstützung
description: Wiederherstellen der AdventureWorks-Beispieldatenbank in PostgreSQL Hyperscale mit Azure Arc-Aktivierung
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: f3174e0980470f9ad23a53810767d75ba375bd9e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650171"
---
# <a name="import-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Importieren der AdventureWorks-Beispieldatenbank in PostgreSQL Hyperscale mit Azure Arc-Unterstützung

[AdventureWorks](/sql/samples/adventureworks-install-configure) ist eine Beispieldatenbank, die eine OLTP-Datenbank enthält, die in Tutorials und Beispielen verwendet wird. Sie wird von Microsoft als Teil des [GitHub-Repositorys für SQL Server-Beispiele](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases) bereitgestellt und verwaltet.

Durch ein Open-Source-Projekt wurde die AdventureWorks-Datenbank so konvertiert, dass sie mit Azure Arc-fähigem PostgreSQL Hyperscale kompatibel ist.
- [Ursprüngliches Projekt](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Ein Folgeprojekt, durch das CSV-Dateien vorab in ein mit PostgreSQL kompatibles Format konvertiert werden.](https://github.com/NorfolkDataSci/adventure-works-postgres)

In diesem Dokument wird ein einfacher Prozess beschrieben, mit dem die AdventureWorks-Beispieldatenbank in Ihre PostgreSQL Hyperscale-Servergruppe importiert werden kann.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Herunterladen der AdventureWorks-Sicherungsdatei

Laden Sie die Datei „AdventureWorks.sql“ in den PostgreSQL Hyperscale-Servergruppencontainer herunter. In diesem Beispiel verwenden wir den Befehl `kubectl exec`, um einen Befehl remote innerhalb des PostgreSQL Hyperscale-Servergruppencontainers auszuführen und die Datei in den Container herunterzuladen. Sie können diese Datei von einem beliebigen Speicherort herunterladen, auf den `curl` zugreifen kann. Gehen Sie genauso vor, wenn Sie über andere Datenbank-Sicherungsdateien verfügen, die Sie in den PostgreSQL Hyperscale-Servergruppencontainer ziehen möchten. Sobald sich die Dateien im PostgreSQL Hyperscale-Servergruppencontainer befinden, lassen sich die Datenbank und das Schema ganz einfach erstellen und mit Daten auffüllen.

Führen Sie einen Befehl wie diesen aus, um die Dateien herunterzuladen. Ersetzen Sie den Wert des Podnamens und des Namespacenamens, bevor Sie den Befehl ausführen:

> [!NOTE]
>  Ihr Container muss über eine Internetverbindung über Port 443 verfügen, um die Datei von GitHub herunterladen zu können.

> [!NOTE]
>  Verwenden Sie den Podnamen des Koordinatorknotens der Postgres Hyperscale-Servergruppe. Sein Name lautet \<server group name\>c-0 (z. B. postgres01c-0, wobei c für Koordinatorknoten steht).  Wenn Sie nicht sicher sind, wie der Podname lautet, führen Sie den Befehl `kubectl get pod` aus.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-import-the-adventureworks-database"></a>Schritt 2: Importieren der AdventureWorks-Datenbank

Genauso können Sie einen kubectl exec-Befehl ausführen, um die Datenbank über das CLI-Tool „psql“ zu erstellen und zu laden. Das Tool ist in den PostgreSQL Hyperscale-Servergruppencontainern enthalten.

Führen Sie einen Befehl wie diesen aus, um die leere Datenbank zu erstellen. Ersetzen Sie zuerst den Wert des Podnamens und des Namespacenamens, bevor Sie den Befehl ausführen.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Führen Sie dann einen Befehl wie diesen aus, um die Datenbank zu importieren. Ersetzen Sie den Wert des Podnamens und des Namespacenamens, bevor Sie den Befehl ausführen.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Hinweis: Die Leistungsvorteile von PostgreSQL Hyperscale mit Azure Arc-Aktivierung werden erst vollständig sichtbar, wenn Sie skalieren und Daten/Tabellen auf den Workerknoten der PostgreSQL Hyperscale-Servergruppe horizontal partitionieren/verteilen. Siehe [Empfohlene nächste Schritte](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Empfohlene nächste Schritte
- Lesen Sie die Konzepte und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und von der gesamten Leistung von Azure Database for PostgreSQL Hyperscale zu profitieren. :
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Instanz von PostgreSQL Hyperscale mit Azure Arc-Unterstützung übertragbar.

- [Skalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe](scale-out-in-postgresql-hyperscale-server-group.md)
