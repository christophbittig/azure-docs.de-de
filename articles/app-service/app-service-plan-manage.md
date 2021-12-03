---
title: Verwalten von App Service-Plänen
description: Erfahren Sie, wie Sie verschiedene Aufgaben zum Verwalten eines App Service-Plans ausführen, z. B. das Erstellen, Verschieben, Skalieren und Löschen.
keywords: App Service, Azure App Service, Skalierung, App Services-Plan, ändern, verwalten, Verwaltung
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 80bac3de850cb4260ed2e91e6b98063adac6d6fe
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236730"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Verwalten eines App Service-Plans in Azure

Ein [Azure App Service-Plan](overview-hosting-plans.md) stellt Ressourcen bereit, die zum Ausführen einer App Service-App erforderlich sind. Diese Anleitung veranschaulicht das Verwalten eines App Service-Plans.

## <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

> [!TIP]
> Wenn Sie einen Plan in einer App Service-Umgebung erstellen möchten, können Sie ihn in der **Region** auswählen und die restlichen Schritte wie unten beschrieben ausführen.

Sie können einen leeren App Service-Plan erstellen; Sie können einen Plan aber auch im Rahmen der App-Erstellung erstellen.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen**.

   ![Erstellen einer Ressource im Azure-Portal][createResource] 

1. Wählen die Option **Neu** > **Web-App** oder eine andere Art von App Service-App aus.

   ![Erstellen Sie eine App im Azure-Portal.][createWebApp] 

2. Konfigurieren Sie den Abschnitt **Instanzdetails**, bevor Sie den App Service-Plan konfigurieren. Einstellungen wie **Veröffentlichen** und **Betriebssysteme** können die verfügbaren Tarife für Ihren App Service-Plan ändern. **Region** bestimmt, wo Ihr App Service-Plan erstellt wird. 
   
3. Wählen Sie im Abschnitt **App Service-Plan** einen vorhandenen Plan aus, oder erstellen Sie einen Plan, indem Sie **Neu erstellen** auswählen.

   ![Erstellen eines App Service-Plans][createASP] 

4. Beim Erstellen eines Plans können Sie den Tarif für den neuen Plan auswählen. Wählen Sie in **SKU und Größe** die Option **Größe ändern** aus, um den Tarif zu ändern. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Verschieben einer App in einen anderen App Service-Plan

Sie können eine App in einen anderen App Service-Plan verschieben, solange sich der Quellplan und der Zielplan in der _gleichen Ressourcengruppe und geografischen Region_ befinden.

> [!NOTE]
> Azure stellt jeden neuen App Service-Plan in einer Bereitstellungseinheit bereit, die intern als Webspace bezeichnet wird. Jede Region kann viele Webspaces aufweisen, Ihre App kann sich jedoch nur zwischen Plänen bewegen, die im gleichen Webspace erstellt wurden. Eine App Service-Umgebung ist ein isolierter Webspace, so dass Apps zwischen verschiedenen Plänen innerhalb der gleichen App Service-Umgebung verschoben werden können, jedoch nicht zwischen Plänen in verschiedenen App Service-Umgebungen.
>
> Beim Erstellen eines Plans können Sie nicht den gewünschten Webspace angeben, es ist aber möglich, sicherzustellen, dass ein Plan im gleichen Webspace wie ein vorhandener Plan erstellt wird. Kurzgefasst werden alle mit der gleichen Kombination aus Ressourcengruppe und Region erstellten Pläne im gleichen Webspace bereitgestellt. Wenn Sie beispielsweise einen Plan in Ressourcengruppe A und Region B erstellt haben, wird jeder Plan, den Sie nachfolgend in Ressourcengruppe A und Region B erstellen, im gleichen Webspace bereitgestellt. Beachten Sie, dass für Pläne keine Möglichkeit zum Wechsel in andere Webspaces besteht. Sie können einen Plan also nicht „in den gleichen Webspace“ wie einen anderen Plan verschieben, indem Sie ihn in eine andere Ressourcengruppe verschieben.
> 

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die Option **App Services**, wählen Sie sie aus, und wählen Sie die App aus, die Sie verschieben möchten.

2. Wählen Sie im linken Menü **App Service-Plan ändern** aus.

3. Wählen Sie im Dropdown **App Service-Plan** einen vorhandenen Plan aus, in den Sie die App verschieben möchten. Im Dropdown werden nur Pläne angezeigt, die sich in derselben Ressourcengruppe und geografischen Region wie der aktuelle App Service-Plan befinden. Wenn kein solcher Plan vorhanden ist, können Sie dort standardmäßig einen Plan erstellen. Sie können einen neuen Plan auch manuell erstellen, indem Sie **Neu erstellen** auswählen.

4. Wenn Sie einen Plan erstellen, können Sie den Tarif für den neuen Plan auswählen. Wählen Sie in **Tarif** den vorhandenen Tarif aus, um ihn zu ändern. 
   
   > [!IMPORTANT]
   > Wenn Sie eine App aus einem Plan mit einem höheren Tarif in einen Plan mit einem niedrigeren Tarif verschieben, z. B. von **D1** in **F1**, kann die App bestimmte Funktionen im Zielplan verlieren. Wenn Ihrer App beispielsweise TLS/SSL-Zertifikate verwendet, wird Ihnen möglicherweise folgende Fehlermeldung angezeigt:
   >
   > `Cannot update the site with hostname '<app_name>' because its current TLS/SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed TLS/SSL configuration is 'Disabled'.`

5. Wenn Sie fertig sind, wählen Sie **OK** aus.
   
   ![Auswahlelement für App Service-Pläne][change] 

## <a name="move-an-app-to-a-different-region"></a>Verschieben einer App in eine andere Region

Die Region, in der Ihre App ausgeführt wird, ist die Region des App Service-Plans, in dem sie sich befindet. Sie können die Region eines App Service-Plans jedoch nicht ändern. Wenn Sie die App in einer anderen Region ausführen möchten, ist das Klonen der App eine Möglichkeit. Beim Klonen wird eine Kopie Ihrer App in einem neuen oder vorhandenen App Service-Plan in einer beliebigen Region erstellt.

Sie finden **App klonen** im Abschnitt **Entwicklungstools** des Menüs.

> [!IMPORTANT]
> Für das Klonen gelten einige Einschränkungen. Informationen dazu erhalten Sie unter [Klonen der Azure App Service-App](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skalieren eines App Service-Plans

Informationen zum Hochskalieren des Tarifs eines App Service-Plans finden Sie unter [Hochskalieren einer App in Azure](manage-scale-up.md).

Informationen zum Aufskalieren einer App-Instanz finden Sie unter [Manuelles oder automatisches Skalieren der Instanzenzahl](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Löschen eines App Service-Plans

Um unerwartete Kosten zu vermeiden, löscht App Service den Plan standardmäßig, wenn Sie die letzte App in einem App Service-Plan löschen. Wenn Sie den Plan beibehalten möchten, sollten Sie ihn in den **Free**-Tarif ändern, damit keine Kosten entstehen.

> [!IMPORTANT]
> Für App Service-Pläne, denen keine Apps zugeordnet sind, fallen trotzdem Gebühren an, da die konfigurierten VM-Instanzen weiterhin reserviert werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hochskalieren einer App in Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
