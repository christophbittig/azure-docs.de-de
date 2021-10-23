---
title: Konfigurieren einer Azure Front Door-Route
description: In diesem Artikel erfahren Sie, wie Sie eine Route zwischen ihren Domänen und Ursprungsgruppen konfigurieren.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 05/17/2021
ms.author: qixwang
ms.openlocfilehash: a9f0095ebd82ab82003c03c1ca9d59f70d908c9f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006107"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Konfigurieren einer Route für Azure Front Door Standard/Premium

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

In diesem Artikel werden die einzelnen Einstellungen erläutert, die zum Erstellen einer AFD-Route (Azure Front Door) für einen vorhandenen Endpunkt verwendet werden. Nachdem Sie Ihrem vorhandenen Azure Front End-Endpunkt eine benutzerdefinierte Domäne und einen Ursprung hinzugefügt haben, müssen Sie die Route konfigurieren, um die Zuordnung zwischen Domänen und Ursprüngen für die Weiterleitung des Datenverkehrs zwischen diesen zu definieren.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie eine Azure Front Door-Route konfigurieren können, müssen Sie mindestens eine Ursprungsgruppe und eine benutzerdefinierte Domäne innerhalb des aktuellen Endpunkts erstellt haben. 

Informationen zum Einrichten einer Ursprungsgruppe finden Sie unter [Erstellen einer neuen Ursprungsgruppe für Azure Front Door Standard/Premium](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Erstellen einer neuen Route für Azure Front Door Standard/Premium

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Azure Front Door Standard/Premium-Profil.

1. Wählen Sie unter **Einstellungen** die Option **Endpoint Manager** aus.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Screenshot der Front Door-Endpunkt-Manager-Einstellungen" lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Wählen Sie dann **Endpunkt bearbeiten** für den Endpunkt aus, für den Sie die Route konfigurieren möchten.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Screenshot zum Auswählen von „Endpunkt bearbeiten“":::

1. Die Seite **Endpunkt bearbeiten** wird angezeigt. Wählen Sie **+ Hinzufügen** für Routen aus.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Screenshot zum Hinzufügen einer Route auf der Seite „Endpunkt bearbeiten“":::    
    
1. Geben Sie auf der Seite **Route hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Screenshot der Seite „Route hinzufügen“" lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Einstellung | BESCHREIBUNG |
    | --- | --- |
    | Name | Geben Sie einen eindeutigen Namen für die neue Route ein. |   
    | Domain| Wählen Sie mindestens eine Domäne aus, die überprüft wurde und keiner anderen Route zugeordnet ist. |
    | Abzugleichende Muster  | Konfigurieren Sie alle URL-Pfadmuster, die diese Route akzeptiert. Beispielsweise können Sie diese Einstellung auf `/images/*` festlegen, um alle Anforderungen für die URL `www.contoso.com/images/*` zu akzeptieren. Die Azure Front Door versucht zunächst, den Datenverkehr auf Grundlage einer genauen Übereinstimmung zu ermitteln. Wenn keine genau übereinstimmenden Pfade gefunden werden, wird nach einem übereinstimmenden Platzhalterpfad gesucht. Wenn keine Routingregeln mit einem übereinstimmenden Pfad gefunden werden, wird die Anforderung abgelehnt und eine HTTP-Antwort „400: Ungültige Anforderung“ gesendet. Bei Pfaden für abzugleichende Muster wird die Groß-/Kleinschreibung nicht beachtet. Dies bedeutet, dass Pfade mit unterschiedlichen Schreibweise als Duplikate behandelt werden. Beispielsweise verfügen Sie bei den Pfaden `/FOO` und `/foo` über den gleichen Host, der das gleiche Protokoll verwendet. Diese Pfade werden als Duplikate betrachtet, was in der Einstellung „Abzugleichende Muster“ nicht zulässig ist. |
    | Akzeptierte Protokolle | Geben Sie die Protokolle an, die Azure Front Door akzeptieren soll, wenn der Client die Anforderung sendet. |
    | Umleiten | Geben Sie an, ob HTTPS für eine eingehende Anforderung mit HTTP-Anforderung erzwungen wird. |
    | Ursprungsgruppe | Wählen Sie aus, an welche Ursprungsgruppe eine Weiterleitung erfolgen soll, wenn eine Anforderung zurück an Ursprung auftritt. |
    | Ursprungspfad | Dieser Pfad wird verwendet, um die URL umzuschreiben, die von Azure Front Door beim Erstellen der an den Ursprung weitergeleiteten Anforderung verwendet wird. Dieser Pfad wird standardmäßig nicht angegeben. Daher verwendet Azure Front Door den eingehenden URL-Pfad in der Anforderung zum Ursprung. Sie können auch einen Platzhalterpfad angeben, der alle übereinstimmenden Teile des eingehenden Pfads in den Anforderungspfad des Ursprungs kopiert. </br></br>Ein Muster für den Abgleich: `/foo/*`</br>Der Ursprungspfad: `/fwd/`</br></br>Der Eingehende URL-Pfad: `/foo/a/b/c/`</br>Die URL von Azure Front Door zum Ursprung: `fwd/a/b/c`. |
    | Weiterleitungsprotokoll | Wählen Sie das für die Weiterleitungsanforderung verwendete Protokoll aus. |
    | Caching | Wählen Sie diese Option aus, um das Zwischenspeichern statischer Inhalte mit Azure Front Door zu aktivieren. |
    | Regel | Wählen Sie Regelsätze aus, die auf diese Route angewendet werden. Weitere Informationen zum Konfigurieren von Regeln finden Sie unter [Konfigurieren eines Regelsatzes für Azure Front Door](how-to-configure-rule-set.md). | 

1. Wählen Sie **Hinzufügen** aus, um die neue Route zu erstellen. Die Route wird in der Liste der Routen für den Endpunkt angezeigt.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Screenshot der Routenliste":::  
    
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Route löschen möchten, die Sie nicht mehr benötigen, wählen Sie die Route und dann **Löschen** aus. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Screenshot zum Löschen einer Route":::  

## <a name="next-steps"></a>Nächste Schritte
Informationen zu benutzerdefinierten Domänen erhalten Sie im Tutorial zum Hinzufügen einer benutzerdefinierten Domäne zu Ihrem Azure Front Door-Endpunkt.

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne]()
