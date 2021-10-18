---
title: Bereitstellen eines Azure Fluid Relay-Diensts
description: Bereitstellen eines Azure Fluid Relay-Diensts über das Azure-Portal
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 29397802e0f4e4c4a47e82db8c1ef17bc89c7d9a
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661771"
---
# <a name="how-to-provision-an-azure-fluid-relay-service"></a>Bereitstellen eines Azure Fluid Relay-Diensts

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Bevor Sie Ihre App mit einem Azure Fluid Relay-Server verbinden können, müssen Sie eine Azure Fluid Relay-Serverressource in Ihrem Azure-Konto bereitstellen. In diesem Artikel werden die Schritte erläutert, mit denen Sie Ihren Azure Fluid Relay-Dienst bereitstellen und verwenden können. 

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen eines Azure Fluid Relay-Diensts müssen Sie über ein Azure-Konto verfügen. Falls Sie kein Konto besitzen, können Sie [Azure kostenlos testen](https://azure.com/free).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist eine logische Sammlung mit Azure-Ressourcen. Alle Ressourcen werden in einer Ressourcengruppe bereitgestellt und verwaltet. So erstellen Sie eine Ressourcengruppe:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im linken Navigationsbereich die Option **Ressourcengruppen** aus. Wählen Sie anschließend **Hinzufügen**.

    :::image type="content" source="../images/add-resource-group.png" alt-text="Screenshot: Seite „Ressourcengruppen“ im Azure-Portal":::

3. Wählen Sie unter Abonnement den Namen des Azure-Abonnements aus, in dem Sie die Ressourcengruppe erstellen möchten.

    :::image type="content" source="../images/create-resource-group.png" alt-text="Screenshot: Seite „Ressourcengruppe erstellen“ im Azure-Portal":::

1. Geben Sie einen eindeutigen Namen für die Ressourcengruppe ein. Das System überprüft sofort, ob der Name im derzeit ausgewählten Azure-Abonnement verfügbar ist.
1. Wählen Sie eine Region für die Ressourcengruppe aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Wählen Sie auf der Seite Bewerten + erstellen die Option **Erstellen**.

## <a name="create-a-fluid-relay-resource"></a>Erstellen einer Fluid Relay-Ressource
Jede Azure Fluid Relay-Serverressource bietet einen Mandanten, den Sie in Ihrer Fluid-Anwendung verwenden können. Innerhalb dieses Mandanten können Sie viele Container/Sitzungen erstellen. So erstellen Sie eine Fluid Relay-Instanz in Ihrer Ressourcengruppe über das Portal

1. Wählen Sie im Azure-Portal oben links **Ressource erstellen** aus.
2. Suchen nach „Fluid“
 
    :::image type="content" source="../images/marketplace-fluid-relay.png" alt-text="Screenshot: Seite „Ressource erstellen“ mit Suchergebnissen für den Begriff „Fluid“":::

3. Wählen Sie **Fluid Relay** und dann **Erstellen** aus.
 
    :::image type="content" source="../images/fluid-relay-details-page.png" alt-text="Screenshot: Detailseite für Azure Fluid Relay-Marketplace":::

4. Gehen Sie auf der Seite „Erstellen“ wie folgt vor:

    :::image type="content" source="../images/create-fluid-relay-server.png" alt-text="Screenshot: Konfigurieren eines neuen Azure Fluid Relay-Servers":::

    1. Wählen Sie das Abonnement aus, in dem Sie den Namespace erstellen möchten.
    2. Wählen Sie die Ressourcengruppe aus, die Sie im vorherigen Schritt erstellt haben.
    3. Geben Sie einen Namen für die Fluid Relay-Ressource ein.
    4. Wählen Sie einen Standort für den Namespace aus.
    
    > [!NOTE]
    > Während der öffentlichen Vorschau werden nur die Regionen „USA, Westen 2“, „Europa, Westen“ und „Asien, Südosten“ unterstützt.

5. Klicken Sie im unteren Bereich der Seite auf die Schaltfläche **Überprüfen + erstellen**.

6. Überprüfen Sie die Einstellungen auf der Seite Bewerten + erstellen, und wählen Sie *Erstellen* aus. Warten Sie, bis die Bereitstellung abgeschlossen ist.

    :::image type="content" source="../images/create-server-validation-complete.png" alt-text="Screenshot: Seite für neuen Dienst nach erfolgreichem Abschluss der Überprüfung":::

7. Wählen Sie auf der Seite Bereitstellung die Option **Zu Ressource wechseln** aus, um zur Seite für Ihren Namespace zu navigieren.

    :::image type="content" source="../images/deployment-complete.png" alt-text="Screenshot des Azure-Portals, der anzeigt, dass die Bereitstellung abgeschlossen ist":::

8. Vergewissern Sie sich, dass die Fluid Relay-Seite ähnlich wie in diesem Beispiel angezeigt wird.

    :::image type="content" source="../images/resource-details.png" alt-text="Screenshot: Beispieldetailseite für eine bereitgestellte Fluid Relay-Ressource":::

## <a name="next-steps"></a>Nächste Schritte
Sie haben soeben eine Ressourcengruppe erstellt und eine Azure Fluid Relay-Ressource in dieser Gruppe bereitgestellt. Als nächstes können Sie [in Ihrer App eine Verbindung mit Ihrem Azure Fluid Relay-Dienst herstellen](../quickstarts/quickstart-dice-roll.md).
