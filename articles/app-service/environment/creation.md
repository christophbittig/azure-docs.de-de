---
title: Erstellen einer App Service-Umgebung
description: Erfahren Sie, wie Sie eine App Service-Umgebung erstellen.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 07/06/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 45d2c817f579cd183337a42e252dd3e606eafefa
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433200"
---
# <a name="create-an-app-service-environment"></a>Erstellen einer App Service-Umgebung
> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung V3 beschrieben, die mit App Service-Plänen vom Typ „Isoliert V2“ verwendet wird.
> 


Die [App Service-Umgebung (ASE)][Intro] ist eine Bereitstellung von App Service für einen Mandanten, die in Ihre Azure Virtual Network-Instanz (VNET) eingeschleust wird. Die Bereitstellung einer ASE erfordert die Verwendung eines einzelnen Subnetzes. Dieses Subnetz kann für nichts anderes als die ASE verwendet werden. 

## <a name="before-you-create-your-ase"></a>Bevor Sie Ihre ASE erstellen

Nachdem die ASE erstellt wurde, können Sie Folgendes nicht mehr ändern:

- Standort
- Subscription
- Resource group
- Verwendetes Azure Virtual Network (VNET)
- Verwendete Subnetze
- Subnetzgröße
- Name Ihrer ASE

Das Subnetz muss groß genug sein, um die maximale Größe abdecken zu können, auf die Sie Ihre ASE skalieren. Wählen Sie ein ausreichend großes Subnetz zur Unterstützung ihrer maximalen Skalierungsanforderungen aus, da es nach der Erstellung nicht mehr geändert werden kann. Die empfohlene Größe ist /24 mit 256 Adressen.

## <a name="creating-an-ase-in-the-portal"></a>Erstellen einer ASE im Portal

1. Suchen Sie zum Erstellen einer ASE im Marketplace nach **App Service-Umgebung v3**.  
2. Grundlegendes:  Wählen Sie das Abonnement aus, wählen Sie die Ressourcengruppe aus, bzw. erstellen Sie sie, und geben Sie den Namen Ihrer ASE ein.  Wählen Sie den Typ der virtuellen IP-Adresse aus. Wenn Sie „Intern“ auswählen, wird als ASE-Adresse für eingehenden Datenverkehr eine Adresse in Ihrem ASE-Subnetz verwendet. Wenn Sie „Extern“ auswählen, wird als ASE-Adresse für eingehenden Datenverkehr eine öffentliche Adresse mit Internetzugriff verwendet. Der ASE-Name wird auch für das Domänensuffix Ihrer ASE verwendet. Wenn Ihr ASE-Name *contoso* lautet und Sie über eine ASE mit interner VIP verfügen, lautet das Domänensuffix *contoso.appserviceenvironment.net*. Wenn Ihr ASE-Name *contoso* lautet und Sie über externe VIP verfügen, lautet das Domänensuffix *contoso.p.azurewebsites.net*. 
![App Service-Umgebung: Registerkarte „Grundlagen“ zur Erstellung](./media/creation/creation-basics.png)
3. Hosting: Wählen Sie für die Hostgruppenbereitstellung entweder *Aktiviert* oder *Deaktiviert* aus. „Hostgruppenbereitstellung“ wird verwendet, um dedizierte Hardware auszuwählen. Wenn Sie „Aktiviert“ auswählen, wird Ihre ASE auf dedizierter Hardware bereitgestellt. Bei der Bereitstellung auf dedizierter Hardware wird Ihnen während der ASE-Erstellung der gesamte dedizierte Host und anschließend ein reduzierter Preis für Ihre App Service-Planinstanzen in Rechnung gestellt. 
![App Service-Umgebung: Hostingauswahl](./media/creation/creation-hosting.png)
4. Netzwerk: Hier können Sie Ihr virtuelles Netzwerk und Ihr Subnetz auswählen oder erstellen. Wenn Sie eine ASE mit interner VIP erstellen, können Sie Azure DNS Private Zones konfigurieren, um Ihr Domänensuffix auf Ihre ASE zu verweisen. Details zur manuellen DNS-Konfiguration finden Sie im DNS-Abschnitt unter [Verwenden einer App Service-Umgebung][UsingASE].
![App Service-Umgebung: Netzwerkauswahl](./media/creation/creation-networking.png)
5. Überprüfen und erstellen: Überprüfen Sie, ob die Konfiguration korrekt ist, und wählen Sie „Erstellen“ aus. Die Erstellung Ihrer ASE kann bis zu knapp zwei Stunden dauern. 

Nachdem die Erstellung Ihrer ASE abgeschlossen ist, können Sie sie als Speicherort auswählen, wenn Sie Ihre Apps erstellen. Weitere Informationen zum Erstellen von Apps in Ihrer ASE sowie zum Verwalten Ihrer ASE finden Sie unter [Verwenden einer App Service-Umgebung][UsingASE].

## <a name="dedicated-hosts"></a>Dedizierte Hosts

Die ASE wird normalerweise auf VMs bereitgestellt, die auf einem mehrinstanzfähigen Hypervisor bereitgestellt werden. Wenn Sie auf dedizierten Systemen (Hardware eingeschlossen) bereitstellen müssen, können Sie Ihre ASE auf dedizierten Hosts bereitstellen. Dedizierte Hosts werden paarweise bereitgestellt, um Redundanz zu gewährleisten. Dedizierte hostbasierte ASE-Bereitstellungen werden anders berechnet als normal. Es fällt eine Gebühr für den dedizierten Host und dann für jede App Service-Plan-Instanz eine weitere Gebühr an. Bereitstellungen in Hostgruppen sind nicht zonenredundant. Wenn die Bereitstellung auf dedizierten Hosts erfolgen soll, wählen Sie auf der Registerkarte „Hosting“ für die Hostgruppenbereitstellung die Option **Aktivieren** aus.

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
