---
title: Erstellen einer privaten Verbindung zum Verwalten von Ressourcen – Azure-Portal
description: Erstellen Sie im Azure-Portal eine private Verbindung zum Verwalten von Ressourcen.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: efe1fdb47bf550b996f4f44eed9f2d41f2a1083d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227349"
---
# <a name="use-portal-to-create-private-link-for-managing-azure-resources"></a>Erstellen einer privaten Verbindung zum Verwalten von Azure-Ressourcen im Azure-Portal

In diesem Artikel wird erläutert, wie Sie mit [Azure Private Link](../../private-link/index.yml) den Zugriff für die Verwaltung von Ressourcen in Ihren Abonnements einschränken. Sie erfahren, wie Sie im Azure-Portal die Verwaltung von Ressourcen über einen privaten Zugriff einrichten.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>Erstellen einer privaten Ressourcenmanagementverbindung

Wenn Sie eine private Verbindung zur Verwaltung von Ressourcen erstellen, wird diese automatisch zugeordnet.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Private Ressourcenmanagementverbindungen**, und wählen Sie die Option aus.

   :::image type="content" source="./media/create-private-link-access-portal/search.png" alt-text="Suchen nach „Private Ressourcenmanagementverbindungen“":::

1. Wenn Ihr Abonnement noch keine privaten Verbindungen zur Ressourcenverwaltung enthält, wird eine leere Seite angezeigt. Klicken Sie auf **Private Ressourcenmanagementverbindung erstellen**.

   :::image type="content" source="./media/create-private-link-access-portal/start-create.png" alt-text="Auswählen der Schaltfläche „Private Ressourcenmanagementverbindung erstellen“":::

1. Geben Sie Werte für die neue private Verbindung zur Ressourcenverwaltung an. Die Stammverwaltungsgruppe für das ausgewählte Verzeichnis wird für die neue Ressource verwendet. Klicken Sie auf **Überprüfen + erstellen**.

   :::image type="content" source="./media/create-private-link-access-portal/provide-values.png" alt-text="Angeben von Werten für die private Ressourcenmanagementverbindung":::

1. Wenn die Überprüfung erfolgreich war, wählen Sie **Erstellen** aus.

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

Erstellen Sie nun einen privaten Endpunkt, der auf die private Ressourcenmanagementverbindung verweist.

1. Navigieren Sie zum **Private Link Center**. Klicken Sie auf **Private Verbindung erstellen**.

   :::image type="content" source="./media/create-private-link-access-portal/private-link-center.png" alt-text="Private Link Center":::

1. Geben Sie auf der Registerkarte **Grundlagen** Werte für Ihren privaten Endpunkt an.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-basics.png" alt-text="Angeben von Werten auf der Registerkarte „Grundlagen“":::

1. Wählen Sie auf der Registerkarte **Ressource** die Option **Verbindung mit einer Azure-Ressource in meinem Verzeichnis herstellen** aus. Wählen Sie als Ressourcentyp die Option **Microsoft.Authorization/resourceManagementPrivateLinks** aus. Wählen Sie als untergeordnete Zielressource die Option **ResourceManagement** aus.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-resource.png" alt-text="Angeben von Werten für die Ressource":::

1. Wählen Sie auf der Registerkarte **Konfiguration** Ihr virtuelles Netzwerk aus. Es wird empfohlen, eine Integration mit einer privaten DNS-Zone auszuführen. Klicken Sie auf **Überprüfen + erstellen**.

1. Wenn die Überprüfung erfolgreich war, wählen Sie **Erstellen** aus.

## <a name="verify-private-dns-zone"></a>Überprüfen der privaten DNS-Zone

Überprüfen Sie die lokale IP-Adresse für die DNS-Zone, um sicherzustellen, dass Ihre Umgebung ordnungsgemäß konfiguriert ist.

1. Wählen Sie in der Ressourcengruppe, in der Sie den privaten Endpunkt bereitgestellt haben, die Ressource der privaten DNS-Zone mit dem Namen **privatelink.azure.com** aus.

1. Vergewissern Sie sich, dass die Datensatzgruppe mit dem Namen **management** über eine gültige lokale IP-Adresse verfügt.

   :::image type="content" source="./media/create-private-link-access-portal/verify.png" alt-text="Überprüfen der lokalen IP-Adresse":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu privaten Verbindungen finden Sie unter [Azure Private Link](../../private-link/index.yml).