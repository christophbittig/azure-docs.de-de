---
title: 'Server neustarten – Hyperscale (Citus): Azure Database for PostgreSQL'
description: So starten Sie die Datenbank in Azure Database for PostgreSQL – Hyperscale (Citus) neu
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 7/9/2021
ms.openlocfilehash: 0de8108cde0b5017221bf3ab038d98c98bb94546
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598619"
---
# <a name="restart-azure-database-for-postgresql---hyperscale-citus"></a>Neustart von Azure Database for PostgreSQL – Hyperscale (Citus)

Wenn Sie Ihre Hyperscale (Citus)-Servergruppe neu starten möchten, können Sie dies auf der Seite **Übersicht** der Gruppe im Azure-Portal tun. Wählen Sie die Schaltfläche **Neu starten** in der obersten Leiste aus. Ein Bestätigungsdialog wird angezeigt. Wählen Sie **Neu starten** aus, um den Vorgang fortzusetzen.

> [!NOTE]
> Wenn die Schaltfläche Neu starten für Ihre Servergruppe noch nicht vorhanden ist, richten Sie eine Anforderung an den Azure-Support zum Neustart Ihrer Servergruppe.

Der Neustart der Servergruppe gilt für alle Knoten. Sie können einzelne Knoten nicht selektiv neu starten. Der Neustart gilt für die gesamten virtuellen Computer der Knoten, nicht nur für die PostgreSQL-Serverinstanzen. Bei Anwendungen, die versuchen, die Datenbank zu verwenden, kommt es während des Neustarts zu Konnektivitätsausfällen.

**Nächste Schritte**

- Das Ändern einiger Serverparameter erfordert einen Neustart. Sehen Sie sich die Liste [aller Serverparameter](reference-hyperscale-parameters.md) an, die auf Hyperscale (Citus) konfigurierbar sind.
