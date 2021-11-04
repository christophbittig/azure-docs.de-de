---
title: Versionshinweise zu Azure Database for PostgreSQL – Flexible Server
description: Hier finden Sie Versionshinweise zu Azure Database for PostgreSQL – Flexible Server.
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2021
ms.openlocfilehash: 24b5db0411241778ce9afd63a4a167b9d6d483eb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433415"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>Versionshinweise: Azure Database for PostgreSQL – Flexible Server

Auf dieser Seite finden Sie aktuelle Neuigkeiten und Updates zu Featureergänzungen, Unterstützung von Engine-Versionen und Erweiterungen sowie weitere Ankündigungen, die für Flexible Server – PostgreSQL relevant sind.

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

## <a name="release-september-2021"></a>Release: September 2021

* Unterstützung für [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server).
* Unterstützung für die [neuen Regionen](overview.md#azure-regions) „Indien, Mitte“ und „Japan, Westen“.
* Unterstützung für den Nicht-SSL-Konnektivitätsmodus mithilfe eines neuen `require_secure_transport`-Serverparameters.
* Unterstützung für den `log_line_prefix`-Serverparameter, der die Zeichenfolge am Anfang jeder Protokollzeile hinzufügt.
* Unterstützung für [Azure Resource Health](../../service-health/resource-health-overview.md) für Flexible Server-Integritätsdiagnose und zum Erhalten von Unterstützung.
* Verschiedene Fehlerbehebungen, Stabilitäts- und Leistungsverbesserungen

## <a name="release-july-2021"></a>Release: Juli 2021

* Unterstützung für [neue Regionen](overview.md#azure-regions): „Asien, Osten“, „Deutschland, Westen-Mitte“, „Südkorea, Süden“, „USA, Süden-Mitte“, „Vereinigtes Königreich, Westen“.
* Unterstützung für die [Erweiterung „pglogical“](concepts-logical.md) v2.3.2 mit PostgreSQL 11, 12 und 13.<sup>$</sup>
* PgBouncer umfasst jetzt `ignore_startup_parameters` das Ignorieren bestimmter Parameter des clientseitigen Treibers, einschließlich der Parameter `extra_float_digits` und `pgbouncer.query_wait_timeout`.  <sup>$</sup>.
* Unterstützung für `pg_stat_reset_shared('bgwriter');` und `pg_stat_reset_shared('archiver');` zum Zurücksetzen der in den Ansichten `pg_stat_bgwriter` und `pg_stat_archiver` angezeigten Leistungsindikatoren<sup>$</sup>.
* Hier finden Sie verschiedene Möglichkeiten zur Fehlerbehebung und Stabilitäts- und Leistungsverbesserungen<sup>$</sup>.

<sup> **$**</sup> Neue Server erhalten diese Features automatisch. Auf Ihren vorhandenen Servern werden diese Features während des zukünftigen Wartungsfensters Ihres Servers aktiviert.

## <a name="release-june-2021"></a>Release: Juni 2021

* Unterstützung für die [aktuellen PostgreSQL-Nebenversionen](./concepts-supported-versions.md) 13.3, 12.7 und 11.12 bei neuen Servererstellungen<sup>$</sup>.
* Unterstützung für [neue Regionen](overview.md#azure-regions), einschließlich Australien, Südosten, Brasilien, Süden, Südkorea, Mitte, Norwegen, Osten, Südafrika, Norden, Schweiz, Norden, VAE, Norden und USA, Westen.
* Unterstützung für [On-Demand-Failoverfunktionen](./concepts-high-availability.md#on-demand-failover), einschließlich erzwungener Failover und geplanten Failover für zonenredundante Hochverfügbarkeitsbereitstellungen.
* Die Unterstützung der [SCRAM-Authentifizierung](how-to-connect-scram.md) für alle mit dem neuen Server erstellten Hauptversionen <sup>$</sup>.
* Die Unterstützung für `pg_prewarm` das mit `shared_preload_libraries` im Voraus mit dem neuen Server geladen werden kann, erstellt <sup>$</sup>.
* Unterstützung für die lo-Erweiterung. Auf der [Seite „Erweiterungen“](./concepts-extensions.md) finden Sie Versionen, die von den einzelnen Hauptversionen unterstützt werden<sup>$</sup>.
* Hier finden Sie verschiedene Möglichkeiten zur Fehlerbehebung und Stabilitäts- und Leistungsverbesserungen<sup>$</sup>.
  
<sup> **$**</sup> Neue Server erhalten diese Features automatisch.  Ihre vorhandenen Server werden automatisch auf die neueste unterstützte Nebenversion aktualisiert. Während des zukünftigen Wartungsfensters Ihres Servers werden auch neue Funktionen aktiviert.

## <a name="release-may-2021"></a>Release: Mai 2021

* Unterstützung für [PostgreSQL-Hauptversion 13](./concepts-supported-versions.md)
* Unterstützung für Erweiterungen, pg_partman, pg_cron und pgaudit. Auf der [Seite „Erweiterungen“](./concepts-extensions.md) finden Sie Versionen, die von den einzelnen Hauptversionen unterstützt werden.
* Verschiedene Fehlerbehebungen, Stabilitäts- und Leistungsverbesserungen

## <a name="release-april-2021"></a>Release: April 2021

* Unterstützung für die [aktuellen PostgreSQL-Nebenversionen](./concepts-supported-versions.md) 12.6 und 11.11 bei neuen Servererstellungen
* Unterstützung für die [private DNS-Zone](./concepts-networking.md#private-access-vnet-integration) im virtuellen Netzwerk (VNet)
* Unterstützung für die Auswahl der Verfügbarkeitszone während der Zeitpunktwiederherstellung
* Unterstützung für neue [Regionen](./overview.md#azure-regions) wie „Australien, Osten“, „Kanada, Mitte“ und „Frankreich, Mitte“
* Unterstützung für die [integrierte PgBouncer-Verbindungspoolfunktion](./concepts-pgbouncer.md) 
<!--- * Support for [pglogical](https://github.com/2ndQuadrant/pglogical) extension version 2.3.2. -->
* [Intelligente Leistung](concepts-query-store.md) in der öffentlichen Vorschauversion
* Verschiedene Fehlerbehebungen, Stabilitäts- und Leistungsverbesserungen

## <a name="contacts"></a>Kontakte

Sollten Sie Fragen oder Vorschläge im Zusammenhang mit Azure Database for PostgreSQL Flexible Server haben, senden Sie eine E-Mail an das zuständige Azure Database for PostgreSQL-Team ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Bei dieser E-Mail-Adresse handelt es sich nicht um einen Alias für den technischen Support.

Weitere Kontaktmöglichkeiten:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0).
  

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine Einführung in den Bereitstellungsmodus für Azure Database for PostgreSQL – Flexible Server gelesen haben, sind Sie bereit, Ihren ersten Server zu erstellen: [Erstellen einer Instanz von Azure Database for PostgreSQL – Flexible Server im Azure-Portal](./quickstart-create-server-portal.md)
