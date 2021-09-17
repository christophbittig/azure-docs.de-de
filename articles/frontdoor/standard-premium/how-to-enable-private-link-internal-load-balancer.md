---
title: Verbinden von Azure Front Door Premium mit einem internen Lastenausgleichsursprung mit Private Link
titleSuffix: Azure Private Link
description: Erfahren Sie, wie Sie Ihre Instanz von Azure Front Door Premium mit einem internen Lastenausgleich verbinden.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: duau
ms.openlocfilehash: e902c8d3a1662f83328ab39a85a11009c58da24b
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606577"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Verbinden von Azure Front Door Premium mit einem internen Lastenausgleichsursprung mit Private Link

Dieser Artikel führt Sie durch die Konfiguration der Azure Front Door Premium-SKU für die Verbindung mit Ihrem Ursprung für den Lastenausgleich unter Verwendung des Azure Private Link-Diensts.

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie eine Instanz des [Private Link-Diensts](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Aktivieren von Private Link für einen internen Lastenausgleich
 
In diesem Abschnitt ordnen Sie den Private Link-Dienst einem privaten Endpunkt zu, der im privaten Netzwerk von Azure Front Door erstellt wurde. 

1. Wählen Sie in Ihrem Azure Front Door Premium-Profil unter *Einstellungen* die Option **Ursprungsgruppen** aus.

1. Wählen Sie die Ursprungsgruppe aus, in der Sie Private Link für den internen Lastenausgleich aktivieren möchten.

1. Wählen Sie **+ Ursprung hinzufügen** aus, um einen Ursprung für den internen Lastenausgleich hinzuzufügen.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Screenshot der Aktivierung von Private Link für einen internen Lastenausgleich":::

1. Wählen Sie unter **Azure-Ressource auswählen** die Option **In mein Verzeichnis** aus. Wählen Sie die folgenden Einstellungen aus, oder geben Sie sie ein, um die Site zu konfigurieren, mit der Azure Front Door Premium privat verbunden werden soll.

    | Einstellung | Wert |
    | ------- | ----- |
    | Region | Wählen Sie die Region aus, die identisch ist oder Ihrem Ursprung am nächsten ist. |
    | Ressourcentyp | Wählen Sie **Microsoft.Network/privateLinkServices** aus. |
    | Resource | Wählen Sie Ihre private Verbindung mit dem internen Lastenausgleich aus. |
    | Untergeordnete Zielressource | Lassen Sie dieses Feld leer. |
    | Request-Nachricht | Passen Sie die Nachricht an, oder wählen Sie den Standardwert aus. |

1. Wählen Sie anschließend **Hinzufügen** und dann **Aktualisieren** aus, um die Konfiguration zu speichern.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-private-link-service"></a>Genehmigen der Azure Front Door Premium-Verbindung mit einem privaten Endpunkt über die Web-App

1. Wechseln Sie zu Private Link Center, und wählen Sie **Private Link-Dienste** aus. Wählen Sie dann den Namen Ihrer privaten Verbindung aus.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Screenshot der Liste der privaten Verbindungen":::

1. Wählen Sie unter *Einstellungen* die Option **Verbindungen mit privaten Endpunkten** aus.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Screenshot der Übersichtsseite für die private Verbindung":::

1. Wählen Sie die *ausstehende* Anforderung für den privaten Endpunkt aus Azure Front Door Premium aus, und wählen Sie dann **Genehmigen** aus.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Screenshot der ausstehenden Genehmigung für die private Verbindung":::

1. Nach der Genehmigung sollte der Screenshot wie der folgende Screenshot aussehen. Es dauert einige Minuten, bis die Verbindung vollständig hergestellt ist. Sie können jetzt über Azure Front Door Premium auf Ihren internen Lastenausgleich zugreifen.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Screenshot der genehmigten Anforderung einer privaten Verbindung":::

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [Private Link-Dienst](../../private-link/private-link-service-overview.md).
