---
title: Unterstützte Versionen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: PostgreSQL-Versionen in Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 299005d8d7c961335decbce46bab8c558d2589a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346444"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Unterstützte Datenbankversionen in Azure Database for PostgreSQL – Hyperscale (Citus)

## <a name="postgresql-versions"></a>PostgreSQL-Versionen

Die PostgreSQL-Version, die in einer Hyperscale (Citus)-Servergruppe ausgeführt wird, kann während der Erstellung angepasst werden. Hyperscale (Citus) unterstützt zurzeit die folgenden Hauptversionen:

### <a name="postgresql-version-13"></a>PostgreSQL Version 13

Das aktuelle Nebenrelease ist 13.3. Informationen zu Verbesserungen und Fehlerbehebungen in diesem Nebenrelease finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/13/static/release-13-2.html).

### <a name="postgresql-version-12"></a>PostgreSQL Version 12

Das aktuelle Nebenrelease ist 12.7. Informationen zu Verbesserungen und Fehlerbehebungen in diesem Nebenrelease finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-6.html).

### <a name="postgresql-version-11"></a>PostgreSQL Version 11

Das aktuelle Nebenrelease ist 11.12. Informationen zu Verbesserungen und Fehlerbehebungen in diesem Nebenrelease finden Sie in der [Dokumentation zu PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html).

### <a name="postgresql-version-10-and-older"></a>PostgreSQL Version 10 und früher

Die PostgreSQL-Version 10 und frühere Versionen werden für Azure Database for PostgreSQL – Hyperscale (Citus) nicht unterstützt.

## <a name="citus-and-other-extension-versions"></a>Citus und andere Erweiterungsversionen

Je nachdem, welche PostgreSQL-Version in einer Servergruppe ausgeführt wird, werden auch verschiedene [Versionen von Postgres-Erweiterungen](concepts-hyperscale-extensions.md) installiert.  Insbesondere enthält Postgres 13 Citus 10, und frühere Postgres-Versionen enthalten Citus 9.5.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich an, welche [Erweiterungen](concepts-hyperscale-extensions.md) in welchen Versionen installiert sind.
* Informieren Sie sich, wie [eine Hyperscale (Citus)-Servergruppe erstellt wird](quickstart-create-hyperscale-portal.md).
