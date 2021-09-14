---
title: Konfigurieren von privaten Endpunkten in Azure Static Web Apps
description: Sie erfahren, wie Sie den Zugriff auf private Endpunkte in Azure Static Web Apps konfigurieren.
services: static-web-apps
author: burkeholland
ms.author: buhollan
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 7/28/2021
ms.openlocfilehash: 8d6f5e019852200068d4db342ef4ab533d8779b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346281"
---
# <a name="configure-private-endpoint-in-azure-static-web-apps"></a>Konfigurieren von privaten Endpunkten in Azure Static Web Apps

Sie können einen privaten Endpunkt (auch private Verbindung) verwenden, um den Zugriff auf Ihre statische Web-App zu beschränken, damit diese nur über Ihr privates Netzwerk zugänglich ist.

> [!NOTE]
> Die Unterstützung für private Endpunkte in Static Web Apps befindet sich derzeit in der Vorschau.

## <a name="how-it-works"></a>Funktionsweise

Das virtuelle Netzwerk (VNet) von Azure ist ein Netzwerk wie in einem herkömmlichen Rechenzentrum, doch die Ressourcen im VNet kommunizieren miteinander auf sichere Weise über das Microsoft Backbone-Netzwerk.

Durch die Konfiguration von Static Web Apps mit einem privaten Endpunkt können Sie eine private IP-Adresse aus Ihrem VNet verwenden. Nachdem diese Verbindung hergestellt wurde, ist Ihre statische Web-App in Ihr VNet integriert. Danach ist Ihre statische Web-App nicht mehr für das öffentliche Internet verfügbar und nur noch von Computern in Ihrem Azure VNet zugänglich.

> [!NOTE]
> Wenn Sie Ihre Anwendung hinter einem privaten Endpunkt platzieren, ist Ihre App nur in der Region verfügbar, in der sich Ihr VNet befindet. Danach ist Ihre Anwendung nicht mehr über mehrere Points of Presence verfügbar.

> [!WARNING]
> Aktuell sichern private Endpunkte nur Ihre Produktionsumgebung. Unterstützung für Stagingumgebungen wird mit einem Dienstupdate in der Zukunft hinzugefügt.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement.
  - Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein [Azure VNet](../virtual-network/quick-create-portal.md)
- Eine mit [Azure Static Web Apps](./get-started-portal.md) bereitgestellte Anwendung, die den Standard-Hostingplan nutzt.

## <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt erstellen Sie einen privaten Endpunkt für Ihre statische Web-App.

> [!IMPORTANT]
> Ihre statische Web-App muss mit dem Standard-Hostingplan bereitgestellt werden, um private Endpunkte nutzen zu können. Sie können den Hostingplan über die Option **Hostingplan** im seitlichen Menü ändern.

1. Öffnen Sie Ihre statische Web-App im Portal.

1. Wählen Sie die Option **Private Endpunkte** aus dem seitlichen Menü aus.

1. Klicken Sie auf die Schaltfläche **Hinzufügen** .

1. Geben Sie im Dialog „Privaten Endpunkt hinzufügen“ diese Informationen ein:

   | Einstellung                         | Wert                         |
   | ------------------------------- | ----------------------------- |
   | Name                            | Geben Sie **myPrivateEndpoint** ein.  |
   | Subscription                    | Wählen Sie Ihr Abonnement aus.     |
   | Virtual Network                 | Wählen Sie Ihr virtuelles Netzwerk aus.  |
   | Subnet                          | Wählen Sie das Subnetz aus.           |
   | Integration in eine private DNS-Zone | Übernehmen Sie den Standardwert **Ja**. |

   :::image type="content" source="media/create-private-link-dialog.png" alt-text="./media/create-private-link-dialog.png":::

1. Klicken Sie auf **OK**.

## <a name="testing-your-private-endpoint"></a>Testen Ihres privaten Endpunkts

Da Ihre Anwendung nicht mehr öffentlich verfügbar ist, können Sie nur innerhalb Ihres virtuellen Netzwerks darauf zugreifen. Zum Testen richten Sie einen virtuellen Computer innerhalb Ihres virtuellen Netzwerks ein und navigieren zu Ihrer Site.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu privaten Endpunkten](../private-link/private-endpoint-overview.md)
