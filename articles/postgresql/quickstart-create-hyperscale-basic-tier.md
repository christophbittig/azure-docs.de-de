---
title: 'Schnellstart: Erstellen einer Servergruppe im Basic-Tarif – Hyperscale (Citus) – Azure Database for PostgreSQL'
description: Erste Schritte mit Azure Database for PostgreSQL und Hyperscale (Citus) im Basic-Tarif
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/03/2021
ms.openlocfilehash: 88bf816ca5cad294a7ab0592c80420f20ade4ed5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728720"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Erstellen einer Hyperscale (Citus)-Servergruppe im Basic-Tarif im Azure-Portal

Azure Database for PostgreSQL Hyperscale (Citus) ist ein verwalteter Dienst, mit dem Sie hochverfügbare PostgreSQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. Der [Basic-Tarif](concepts-hyperscale-tiers.md) ist eine praktische Bereitstellungsoption für die anfängliche Entwicklung sowie für Eingangstests.

In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal eine Hyperscale (Citus)-Servergruppe im Basic-Tarif erstellen. Sie stellen die Servergruppe bereit und überprüfen, ob Sie eine Verbindung mit ihr herstellen können, um Abfragen auszuführen.

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie eine Hyperscale-Servergruppe (Citus) bereitstellen. Sie haben mithilfe von psql eine Verbindung mit ihr hergestellt, haben ein Schema erstellt und Daten verteilt.

- Tutorial zum [Erstellen skalierbarer mehrinstanzenfähiger Anwendungen](./tutorial-design-database-hyperscale-multi-tenant.md)
- Bestimmen der besten [Anfangsgröße](howto-hyperscale-scale-initial.md) für Ihre Servergruppe
