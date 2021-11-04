---
title: Azure Database for PostgreSQL Single Server
description: Hier erhalten Sie einen Überblick über Azure Database for PostgreSQL Single Server.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 3f53d6f348c7c1b46562b3769a415a5a7acc9262
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463958"
---
# <a name="azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL Single Server

[Azure Database for PostgreSQL](./overview.md) auf Grundlage der PostgreSQL Community Edition ist in drei Bereitstellungsmodi verfügbar:

- Einzelner Server
- Flexible Server (Vorschau)
- Hyperscale (Citus)

In diesem Artikel erhalten Sie eine Übersicht und eine Einführung in die grundlegenden Konzepte des Bereitstellungsmodells Azure Database for MySQL Single Server. Weitere Informationen zum Bereitstellungsmodus „Flexible Server“ finden Sie unter [Übersicht über Flexible Server](./flexible-server/overview.md) und „Übersicht über Hyperscale (Citus)“.

## <a name="overview"></a>Übersicht

Bei Single Server handelt es sich um einen vollständig verwalteten Datenbankdienst mit minimalen Anforderungen für die Anpassung der Datenbank. Die Single Server-Plattform ist für die Verarbeitung der meisten Datenbankverwaltungsfunktionen (z. B. Patching, Sicherungen, Hochverfügbarkeit und Sicherheit) mit minimaler Benutzerkonfiguration und -steuerung konzipiert. Die Architektur ist für eine Verfügbarkeit von 99,99 Prozent in einer einzelnen Verfügbarkeitszone optimiert. Sie unterstützt die Communityversionen 9.6, 10 und 11 von PostgreSQL. Der Dienst ist heute in vielen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/) allgemein verfügbar.

Single Server-Instanzen eignen sich am besten für cloudnative Anwendungen, da sie für das automatisierte Patchen ohne genauen Patchzeitplan und ohne benutzerdefinierte PostgreSQL-Konfigurationseinstellungen konzipiert sind.

## <a name="high-availability"></a>Hochverfügbarkeit

Der Bereitstellungsmodus mit einem Einzelserver wurde für integrierte Hochverfügbarkeit und Flexibilität bei geringeren Kosten konzipiert. In der Architektur sind Compute und Speicher voneinander getrennt. Die Datenbank-Engine wird in einem eigenentwickelten Computecontainer ausgeführt, während sich die Datendateien im Azure-Speicher befinden. Der Speicher verwaltet drei lokal redundante synchrone Kopien der Datenbankdateien, sodass die Datenbeständigkeit sichergestellt wird.

Wenn bei geplanten oder ungeplanten Failoverereignissen der Server ausfällt, gewährleistet der Dienst die Hochverfügbarkeit der Server mit dem folgenden automatisierten Verfahren:

1. Ein neuer Computecontainer wird bereitgestellt.
2. Der Speicher mit Datendateien wird dem neuen Container zugeordnet.
3. Die PostgreSQL-Datenbank-Engine wird im neuen Computecontainer online geschaltet.
4. Der Gatewaydienst stellt ein transparentes Failover sicher, sodass keine Änderungen an der Anwendung erforderlich sind.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Azure Database for PostgreSQL Single Server":::

Für gewöhnlich dauert das Failover zwischen 60 und 120 Sekunden. Da der Single Server-Dienst cloudnativ konzipiert wurde, ist die Unterstützung von 99,99 Prozent Verfügbarkeit möglich, wodurch die Kosten für einen passiven unmittelbar betriebsbereiten Standbyserver entfallen.

Durch das globale Netzwerk der von Microsoft verwalteten Rechenzentren kann durch die branchenführende Verfügbarkeit von Azure von 99,99 Prozent gemäß Vereinbarung zum Servicelevel (SLA) sichergestellt werden, dass Ihre Anwendungen rund um die Uhr ausgeführt werden.

## <a name="automated-patching"></a>Automatisiertes Patchen

Der Dienst führt automatisches Patchen für die zugrunde liegende Hardware, das Betriebssystem und die Datenbank-Engine durch. Das Patchen umfasst Sicherheits- und Softwareupdates. Bei der PostgreSQL-Engine werden Upgrades von Nebenversionen automatisch ausgeführt und sind im Patchzyklus enthalten. Für das Patchen sind keine Benutzeraktionen oder Konfigurationseinstellungen erforderlich. Die Patchhäufigkeit wird basierend auf der Wichtigkeit der Nutzlast durch den Dienst verwaltet. Im Allgemeinen gilt für den Dienst im Rahmen der kontinuierlichen Integration und Veröffentlichung ein monatlicher Releasezeitplan. Benutzer können die [Benachrichtigungen zu geplanten Wartungen]() abonnieren, um 72 Stunden vorher über anstehende Wartungen benachrichtigt zu werden.

## <a name="automatic-backups"></a>Automatische Sicherungen

Für Azure Database for MySQL Single Server werden Sicherungen automatisch erstellt und in einem vom Benutzer konfigurierten lokal redundanten (LRS) oder georedundanten Speicher gespeichert. Mithilfe von Sicherungen können Sie den Server zu einem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums der Sicherung wiederherstellen. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Die Aufbewahrungsdauer kann optional auf bis zu 35 Tage festgelegt werden. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet. Weitere Informationen finden Sie unter [Sicherungen](./concepts-backup.md).

## <a name="adjust-performance-and-scale-within-seconds"></a>Anpassen von Leistung und Skalierung in nur wenigen Sekunden

Der Single Server-Dienst ist in drei SKU-Tarifen verfügbar: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Der Basic-Tarif eignet sich am besten für die kostengünstige Entwicklung und Workloads mit geringer Parallelität. Die Tarife „Universell“ und „Arbeitsspeicheroptimiert“ eignen sich besser für Produktionsworkloads, die eine hohe Parallelität, Skalierung und vorhersagbare Leistung erfordern. Sie können Ihre erste App mit einer kleinen Datenbank für wenige USD im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Die Speicherskalierung erfolgt online und unterstützt die automatische Vergrößerung des Speichers. Durch die dynamische Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie zahlen nur für die Ressourcen, die Sie verwenden. Einzelheiten hierzu finden Sie unter [Tarife]().

## <a name="enterprise-grade-security-compliance-and-governance"></a>Sicherheit, Compliance und Governance auf Unternehmensniveau

Der Single Server-Dienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten einschließlich Sicherungen und während der Ausführung von Abfragen erstellte temporäre Daten werden auf dem Datenträger verschlüsselt. Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel können vom System (Standard) oder vom [Kunden]() verwaltet werden. Der Dienst verschlüsselt Daten während der Übertragung mit der Transport Layer Security (SSL/TLS), was standardmäßig erzwungen wird. Der Dienst unterstützt die TLS-Versionen 1.2, 1.1 und 1.0 mit der Möglichkeit, die [mindestens erforderliche TLS-Version]() zu erzwingen.

Der Dienst ermöglicht den privaten Zugriff auf die Server über Private Link und bietet Advanced Threat Protection-Funktionen. Advanced Threat Protection erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu nutzen.

Neben der nativen Authentifizierung unterstützt der Single Server-Dienst zudem die Authentifizierung mit Azure Active Directory. Bei der Authentifizierung mit Azure AD wird eine Verbindung mit den PostgreSQL-Servern mithilfe von Identitäten hergestellt, die in Azure AD definiert und verwaltet werden. Mit der Azure AD-Authentifizierung können Sie Datenbankbenutzeridentitäten und andere Azure-Dienste an einem zentralen Ort verwalten, wodurch die Zugriffssteuerung vereinfacht und zentralisiert wird.

[Überwachungsprotokolle]() (in der Vorschau) sind verfügbar, um alle Aktivitäten auf Datenbankebene zu überwachen.

Der Single Server-Dienst ist mit allen branchenführenden Zertifizierungen wie FedRAMP, HIPAA und PCI-DSS kompatibel. Weitere Informationen zur Sicherheit der Azure-Plattform finden Sie im [Azure Trust Center]().

Weitere Informationen zu Sicherheitsfeatures von Azure Database for PostgreSQL finden Sie in der [Übersicht über die Sicherheit]().

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Der Single Server-Dienst verfügt über integrierte Features zur Leistungsüberwachung und Warnung. Alle Azure-Metriken werden im Minutentakt erfasst, und für jede Metrik steht ein Verlauf von 30 Tagen zur Verfügung. Sie können Warnungen für die Metriken konfigurieren. Der Dienst ermöglicht das Konfigurieren von Protokollen für langsame Abfragen und verfügt über eine differenzierte Funktion für [das Speichern von Abfragen](./concepts-query-store.md). Der Abfragespeicher vereinfacht das Beheben von Leistungsproblemen, da er es Ihnen ermöglicht, die am längsten ausgeführten und ressourcenintensivsten Abfragen schnell zu ermitteln. Mit diesen Tools können Sie Ihre Workloads schnell optimieren und Ihren Server für optimale Leistung konfigurieren. Weitere Informationen finden Sie unter [Überwachung](./concepts-monitoring.md).

## <a name="migration"></a>Migration

Der Dienst führt die Communityversion von PostgreSQL aus. Dies ermöglicht vollständige Anwendungskompatibilität und erfordert minimale Refactoringkosten für die Migration vorhandener Anwendungen, die in der PostgreSQL-Engine entwickelt wurden, zum Single Server-Dienst. Die Migration zum Single Server-Dienst kann mithilfe einer der folgenden Optionen durchgeführt werden:

- **Sichern und Wiederherstellen:** Für Offlinemigrationen, bei denen sich Benutzer einige Downtimes leisten können, kann das Sichern und Wiederherstellen mit Communitytools wie pg_dump und pg_restore die schnellste Option für die Migration sein. Ausführliche Informationen finden Sie unter [Migrieren der MySQL-Datenbank auf Azure-Datenbank für MySQL durch Sicherungen und Wiederherstellungen](./howto-migrate-using-dump-and-restore.md).
- **Azure Database Migration Service:** Für eine nahtlose und einfachere Migration zum Single Server-Dienst mit minimalen Downtimes können Sie Azure Database Migration Service verwenden. Weitere Informationen finden Sie unter [DMS über Portal](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) und [DMS über CLI](../dms/tutorial-postgresql-azure-postgresql-online.md).

## <a name="contacts"></a>Kontakte

Sollten Sie Fragen oder Vorschläge im Zusammenhang mit der Verwendung von Azure Database for PostgreSQL haben, senden Sie eine E-Mail an das zuständige Azure Database for PostgreSQL-Team ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Bei dieser E-Mail-Adresse handelt es sich nicht um einen Alias für den technischen Support.

Weitere Kontaktmöglichkeiten:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die Grundlagen zum Bereitstellungsmodus Azure Database for PostgreSQL Single Server kennen, können Sie mit den folgenden Themen fortfahren:
- Erstellen Sie Ihren ersten Server.
