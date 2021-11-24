---
title: 'Richtlinie für die Versionsunterstützung: Azure Database for MySQL – Einzelserver und Flexible Server (Vorschau)'
description: Hier wird die Richtlinie zu den unterstützten Haupt- und Nebenversionen von MySQL in Azure Database for MySQL beschrieben.
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 11/03/2020
ms.openlocfilehash: 248234c816cc3341929417282521d30c2105c671
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323251"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL: Richtlinie für die Versionsunterstützung

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Auf dieser Seite wird die Versionsrichtlinie für Azure Database for MySQL beschrieben, die auf die Bereitstellungsmodi für Azure Database for MySQL – Einzelserver und Azure Database for MySQL – flexibler Server (Vorschau) angewendet werden kann.

## <a name="supported-mysql-versions"></a>Unterstützte MySQL-Versionen

Azure Database for MySQL wurde basierend auf [MySQL Community Edition](https://www.mysql.com/products/community/) mit der InnoDB-Speicher-Engine entwickelt. Dieser Dienst unterstützt alle aktuellen Hauptversionen, die von der Community unterstützt werden, und zwar MySQL 5.6, 5.7 und 8.0. Für MySQL wird das Benennungsschema „X.Y.Z“ verwendet. „X“ steht dabei für die Hauptversion, „Y“ für die Nebenversion und „Z“ für das Programmfehlerbehebungs-Release. Weitere Informationen zum Schema finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Azure Database for MySQL unterstützt derzeit die folgenden Haupt- und Nebenversionen von MySQL:

| Version | [Single Server](overview.md) <br/> Aktuelle Nebenversion |[Flexible Server](./flexible-server/overview.md) <br/> Aktuelle Nebenversion  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL-Version 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (eingestellt) | Nicht unterstützt|
|MySQL-Version 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL, Version 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> Bei der Bereitstellungsoption „Einzelserver“ werden die Verbindungen mit einem Gateway zu Serverinstanzen umgeleitet. Sobald die Verbindung hergestellt ist, zeigt der MySQL-Client die im Gateway festgelegte Version von MySQL an, nicht die tatsächliche Version, die auf Ihrer MySQL-Serverinstanz ausgeführt wird. Um die Version Ihrer MySQL-Serverinstanz zu ermitteln, geben Sie den `SELECT VERSION();`-Befehl an der MySQL-Eingabeaufforderung ein. Wenn Ihre Anwendung die Anforderung hat, sich mit einer bestimmten Hauptversion zu verbinden, z. B. v5.7 oder v8.0, können Sie dies tun, indem Sie den Port in Ihrer Server-Verbindungszeichenfolge ändern, wie in unserer Dokumentation [hier](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version) beschrieben.

> [!IMPORTANT]
> MySQL v5.6 wird auf dem Single Server ab Februar 2021 nicht mehr eingesetzt. Ab dem 1. September 2021 können Sie keine neuen v5.6-Server mit der Bereitstellungsoption Azure Database für MySQL - Single Server erstellen. Sie können jedoch Zeitpunktwiederherstellungen durchführen und Lesereplikate für Ihre vorhandenen Server erstellen.

Lesen Sie die Richtlinie zur Versionsunterstützung für eingestellte Versionen in der [Dokumentation zur Richtlinie für die Versionsunterstützung](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql).

## <a name="major-version-support"></a>Support für die Hauptversion

Jede Hauptversion von MySQL wird durch Azure Database for MySQL ab dem Datum, an dem Azure mit der Unterstützung für die Version beginnt, bis zu dem Datum unterstützt, an dem die Version durch die MySQL-Community eingestellt wird, wie in der [Versionsrichtlinie](https://www.mysql.com/support/eol-notice.html) angegeben.

## <a name="minor-version-support"></a>Support für die Nebenversionen

Azure Database for MySQL führt im Rahmen regelmäßiger Wartungsarbeiten automatisch Nebenversionsupgrades auf die bevorzugte Azure MySQL-Version durch. 

## <a name="major-version-retirement-policy"></a>Richtlinie zur Einstellung der Hauptversion

Die folgende Tabelle enthält die Details zur Einstellung von MySQL-Hauptversionen. Die Daten entsprechen der [MySQL-Versionsrichtlinie](https://www.mysql.com/support/eol-notice.html).

| Version | Neues | Startdatum des Azure-Supports | Deaktivierungstermin|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funktionen](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20. März 2018 | Februar 2021
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funktionen](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20. März 2018 | Oktober 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Features](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11. Dezember 2019 | April 2026

## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Eingestellte Versionen der MySQL-Engine werden in Azure Database for MySQL nicht unterstützt

Wenn Sie nach dem Einstellungsdatum der jeweiligen MySQL-Datenbankversionen die eingestellte Version weiterhin ausführen, beachten Sie die folgenden Einschränkungen:

- Da die Community keine weiteren Fehlerbehebungen oder Sicherheitskorrekturen mehr veröffentlicht, patcht Azure Database for MySQL die eingestellte Datenbank-Engine nicht bei Fehlern oder Sicherheitsproblemen und ergreift keinerlei Sicherheitsmaßnahmen im Hinblick auf die eingestellte Datenbank-Engine. Azure führt jedoch weiterhin regelmäßige Wartung und Patchen für den Host, das Betriebssystem, die Container sowie alle anderen dienstbezogenen Komponenten durch.
- Im Fall eines Unterstützungsproblems im Zusammenhang mit der MySQL-Datenbank können wir Ihnen möglicherweise keinen Support bieten. In solchen Fällen müssen Sie Ihre Datenbank aktualisieren, damit wir Ihnen Support dafür bieten können.
- Sie können für die eingestellte Version keine neuen Datenbankserver erstellen. Sie können jedoch Zeitpunktwiederherstellungen durchführen und Lesereplikate für Ihre vorhandenen Server erstellen.
- Neue von Azure Database for MySQL entwickelte Dienstfunktionen stehen möglicherweise nur für unterstützte Datenbankserverversionen zur Verfügung.
- Betriebszeit-SLAs gelten ausschließlich für dienstbezogene Probleme bei Azure Database for MySQL und nicht für Ausfallzeiten, die durch Fehler im Zusammenhang mit der Datenbank-Engine verursacht werden.  
- Im Extremfall einer ernsthaften Bedrohung des Diensts aufgrund eines Sicherheitsrisikos der MySQL-Datenbank-Engine, das in der eingestellten Datenbankversion identifiziert wird, beendet Azure möglicherweise den Computeknoten Ihres Datenbankservers, um zunächst den Dienst zu sichern. Sie werden aufgefordert, ein Upgrade des Servers auszuführen, bevor Sie den Server wieder online schalten. Während des Upgradeprozesses sind Ihre Daten jederzeit über automatische Sicherungen geschützt, die für den Dienst ausgeführt werden und bei Bedarf zur Wiederherstellung der älteren Version verwendet werden können. 

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich für die Option „Einzelserver“ unter [Unterstützte Azure Database for MySQL-Serverversionen](./concepts-supported-versions.md).
- Unterstützte Versionen für Azure Database für MySQL – Flexible Server [unterstützte Versionen](flexible-server/concepts-supported-versions.md)
- Informationen zu Upgrades finden Sie unter [Sicherungen und Wiederherstellungen](./concepts-migrate-dump-restore.md) für MySQL.