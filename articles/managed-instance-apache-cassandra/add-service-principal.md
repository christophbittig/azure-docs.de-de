---
title: Hinzufügen eines Cosmos DB-Dienstprinzipals für Azure Managed Instance for Apache Cassandra
description: Erfahren Sie, wie Sie einen Cosmos DB-Dienstprinzipal zu einem vorhandenen virtuellen Netzwerk für Azure Managed Instance for Apache Cassandra hinzufügen.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06fd7f23db4b4df9b1153ac8b00afb35c265d0be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858582"
---
# <a name="use-azure-portal-to-add-cosmos-db-service-principal"></a>Verwenden des Azure-Portals zum Hinzufügen eines Cosmos DB-Dienstprinzipals

Für eine erfolgreiche Bereitstellung in einem bestehenden virtuellen Netzwerk benötigt Azure Managed Instance for Apache Cassandra den Azure Cosmos DB-Dienstprinzipal mit einer Rolle (wie Netzwerkmitwirkender), die die Aktion `Microsoft.Network/virtualNetworks/subnets/join/action` zulässt. In einigen Fällen kann es erforderlich sein, diese Berechtigungen manuell hinzuzufügen. In diesem Artikel wird gezeigt, wie Sie dies mithilfe des Azure-Portals erreichen können. 

## <a name="add-cosmos-db-service-principal"></a>Hinzufügen des Cosmos DB-Dienstprinzipals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu dem virtuellen Zielnetzwerk in Ihrem Abonnement, wählen Sie die Registerkarte „Zugriffssteuerung“ aus, und klicken Sie auf `add role assignment`:

   :::image type="content" source="./media/add-service-principal/service-principal-1.png" alt-text="Hinzufügen der Rollenzuweisung" lightbox="./media/add-service-principal/service-principal-1.png" border="true"::: 

1. Suchen Sie die Rolle `Network Contributor`, markieren Sie sie, und wählen Sie dann die Registerkarte `members` aus:

   :::image type="content" source="./media/add-service-principal/service-principal-2.png" alt-text="Hinzufügen der Rolle „Netzwerkmitwirkender“" lightbox="./media/add-service-principal/service-principal-2.png" border="true"::: 

   > [!NOTE]
   > Sie benötigen keine Rolle mit Berechtigungen, die so umfangreich wie „Netzwerkmitwirkender“ sind. Dies wird der Einfachheit halber als Beispiel verwendet. Sie können auch eine Kundenrolle mit eingeschränkteren Berechtigungen erstellen, solange sie die Aktion `Microsoft.Network/virtualNetworks/subnets/join/action` zulässt.

1. Stellen Sie sicher, dass `User, group, or service principal` für `Assign access to` ausgewählt ist, und klicken Sie dann auf `Select members`, um nach dem `Azure Cosmos DB`-Dienstprinzipal zu suchen. Wählen Sie ihn im rechten Fenster aus:

   :::image type="content" source="./media/add-service-principal/service-principal-3.png" alt-text="Auswählen des Cosmos DB-Dienstprinzipals" lightbox="./media/add-service-principal/service-principal-3.png" border="true"::: 

1. Klicken Sie oben auf die Registerkarte `Review + assign`, und klicken Sie dann unten auf die Schaltfläche `Review + assign`. Der Cosmos DB-Dienstprinzipal sollte jetzt zugewiesen werden. 

   :::image type="content" source="./media/add-service-principal/service-principal-4.png" alt-text="Überprüfen und Zuweisen" lightbox="./media/add-service-principal/service-principal-4.png" border="true"::: 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einem virtuellen Netzwerk den Azure Cosmos DB-Dienstprinzipal mit einer entsprechenden Rolle zuweisen, um verwaltete Cassandra-Bereitstellungen zu ermöglichen. Weitere Informationen zu Azure Managed Instance for Apache Cassandra finden Sie in den folgenden Artikeln:

* [Was ist Azure Managed Instance for Apache Cassandra? (Vorschau)](introduction.md)
* [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
