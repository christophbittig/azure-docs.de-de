---
title: Unterstützte Versionen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: PostgreSQL-Versionen in Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 8dd0929d56bac0b2f825d976ba07bd0956c0d063
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755189"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Unterstützte Datenbankversionen in Azure Database for PostgreSQL – Hyperscale (Citus)

## <a name="postgresql-versions"></a>PostgreSQL-Versionen

Die PostgreSQL-Version, die in einer Hyperscale (Citus)-Servergruppe ausgeführt wird, kann während der Erstellung angepasst werden. Hyperscale (Citus) unterstützt zurzeit die folgenden Hauptversionen:

### <a name="postgresql-version-13"></a>PostgreSQL Version 13

Die aktuelle Nebenversion ist 13.4. Informationen zu Verbesserungen und Fehlerbehebungen in diesem Nebenrelease finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/13/release-13-4.html).

### <a name="postgresql-version-12"></a>PostgreSQL Version 12

Die aktuelle Nebenversion ist 12.8. Informationen zu Verbesserungen und Fehlerbehebungen in diesem Nebenrelease finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/12/release-12-8.html).

### <a name="postgresql-version-11"></a>PostgreSQL Version 11

Die aktuelle Nebenversion ist 11.13. Informationen zu Verbesserungen und Fehlerbehebungen in diesem Nebenrelease finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/11/release-11-13.html).

### <a name="postgresql-version-10-and-older"></a>PostgreSQL Version 10 und früher

Die PostgreSQL-Version 10 und frühere Versionen werden für Azure Database for PostgreSQL – Hyperscale (Citus) nicht unterstützt.

## <a name="citus-and-other-extension-versions"></a>Citus und andere Erweiterungsversionen

Je nachdem, welche PostgreSQL-Version in einer Servergruppe ausgeführt wird, werden auch verschiedene [Versionen von Postgres-Erweiterungen](concepts-hyperscale-extensions.md) installiert.  Insbesondere enthält Postgres 13 Citus 10, und frühere Postgres-Versionen enthalten Citus 9.5.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich an, welche [Erweiterungen](concepts-hyperscale-extensions.md) in welchen Versionen installiert sind.
* Informieren Sie sich, wie [eine Hyperscale (Citus)-Servergruppe erstellt wird](quickstart-create-hyperscale-portal.md).
