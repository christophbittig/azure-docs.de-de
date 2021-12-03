---
title: Erstellen einer App Service-Umgebung
description: Erfahren Sie, wie Sie eine App Service-Umgebung erstellen.
author: madsd
ms.topic: article
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: ae243842f5201b7d8f2bfa3adcb42c5146c885d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525023"
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

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

Es gibt zwei wichtige Punkte, die vor der Bereitstellung Ihrer ASE berücksichtigt werden müssen.

- VIP-Typ
- Bereitstellungstyp

Es gibt zwei verschiedene VIP-Typen: intern und extern. Mit einer internen VIP sind Ihre Apps in der ASE unter einer Adresse in Ihrem ASE-Subnetz erreichbar, und Ihre Apps befinden sich nicht im öffentlichen DNS. Während der Erstellung im Portal gibt es eine Option zum Erstellen einer privaten Azure DNS-Zone für Ihre ASE. Mit einer externen VIP befinden sich Ihre Apps unter einer öffentlichen, über das Internet erreichbaren Adresse, und sie befinden sich im öffentlichen DNS. 

Es gibt drei verschiedene Bereitstellungstypen:

- Einzelzone
- Zonenredundant
- Hostgruppe

Die Einzelzonen-ASE ist in allen Regionen verfügbar, in denen ASEv3 verfügbar ist. Wenn Sie über eine Einzelzonen-ASE verfügen, unterliegen Sie einer Mindestgebühr für eine App Service-Planinstanz von „Windows Isolated v2“. Sobald Sie über eine oder mehrere Instanzen verfügen, wird diese Gebühr entfernt. Es handelt sich nicht um eine zusätzliche Gebühr.

In einer zonenredundanten ASE verteilen sich Ihre Apps auf drei Zonen in derselben Region. Die zonenredundante ASE ist in einer Teilmenge der ASE-fähigen Regionen verfügbar, die hauptsächlich durch die Regionen eingeschränkt wird, die Verfügbarkeitszonen unterstützen. Wenn Sie über eine zonenredundante ASE verfügen, ist die kleinste Größe für Ihren App Service-Plan drei Instanzen. Dadurch wird sichergestellt, dass in jeder Verfügbarkeitszone eine Instanz vorhanden ist. App Service-Pläne können eine oder mehrere Instanzen gleichzeitig hochskaliert werden. Die Skalierung muss nicht in Einheiten von drei erfolgen, aber die App wird nur über alle Verfügbarkeitszonen ausgeglichen verteilt, wenn die Gesamtzahl der Instanzen ein Vielfaches von drei ist. Eine zonenredundante ASE verfügt über das Dreifache an Infrastruktur und wird mit zonenredundanten Komponenten hergestellt, sodass Ihre Workloads verfügbar bleiben, auch wenn zwei der drei Zonen aus irgendeinem Grund ausfallen. Aufgrund der erhöhten Systemanforderungen beträgt die Mindestgebühr für eine zonenredundante ASE neun Instanzen. Wenn Sie über weniger als neun App Service-Planinstanzen in Ihrer ASEv3 verfügen, wird die Differenz als „Windows I1v2“ berechnet. Wenn Sie über neun oder mehr Instanzen verfügen, fallen für eine zonenredundante ASE keine zusätzlichen Gebühren an. Weitere Informationen zur Zonenredundanz finden Sie unter [Regionen und Verfügbarkeitszonen](./overview-zone-redundancy.md).

In einer Hostgruppenbereitstellung werden Ihre Apps in einer dedizierten Hostgruppe bereitgestellt. Die dedizierte Hostgruppe ist nicht zonenredundant. Durch eine dedizierte Hostgruppenbereitstellung kann Ihre ASE auf dedizierter Hardware bereitgestellt werden. Es gibt keine Mindestinstanzgebühr für die Verwendung einer ASE in einer dedizierten Hostgruppe, aber Sie müssen für die Hostgruppe bezahlen, wenn Sie die ASE bereitstellen. Zusätzlich dazu zahlen Sie eine rabattierte App Service-Plangebühr, wenn Sie Ihre Pläne erstellen und aufskalieren. Bei einer dedizierten Hostbereitstellung ist eine begrenzte Anzahl von Kernen verfügbar, die sowohl von den App Service-Plänen als auch von den Infrastrukturrollen verwendet werden. Dedizierte Hostbereitstellungen der ASE können die Gesamtzahl von 200 Instanzen, die normalerweise in einer ASE verfügbar sind, nicht erreichen. Die Anzahl der möglichen Gesamtinstanzen steht im Verhältnis zu der Gesamtzahl der App Service-Planinstanzen zuzüglich der lastbasierten Anzahl von Infrastrukturrollen.

## <a name="creating-an-ase-in-the-portal"></a>Erstellen einer ASE im Portal

1. Suchen Sie zum Erstellen einer ASE im Marketplace nach **App Service-Umgebung v3**.

2. Grundlegendes:  Wählen Sie das Abonnement aus, wählen Sie die Ressourcengruppe aus, bzw. erstellen Sie sie, und geben Sie den Namen Ihrer ASE ein.  Wählen Sie den Typ der virtuellen IP-Adresse aus. Wenn Sie „Intern“ auswählen, wird als ASE-Adresse für eingehenden Datenverkehr eine Adresse in Ihrem ASE-Subnetz verwendet. Wenn Sie „Extern“ auswählen, wird als ASE-Adresse für eingehenden Datenverkehr eine öffentliche Adresse mit Internetzugriff verwendet. Der ASE-Name wird auch für das Domänensuffix Ihrer ASE verwendet. Wenn Ihr ASE-Name *contoso* lautet und Sie über eine ASE mit interner VIP verfügen, lautet das Domänensuffix *contoso.appserviceenvironment.net*. Wenn Ihr ASE-Name *contoso* lautet und Sie über externe VIP verfügen, lautet das Domänensuffix *contoso.p.azurewebsites.net*. 

    ![App Service-Umgebung: Registerkarte „Grundlagen“ zur Erstellung](./media/creation/creation-basics.png)

3. Hosting: Wählen Sie für die Hostgruppenbereitstellung entweder *Aktiviert* oder *Deaktiviert* aus. „Hostgruppenbereitstellung“ wird verwendet, um eine dedizierte Hardwarebereitstellung auszuwählen. Wenn Sie „Aktiviert“ auswählen, wird Ihre ASE auf dedizierter Hardware bereitgestellt. Bei der Bereitstellung auf dedizierter Hardware wird Ihnen während der ASE-Erstellung der gesamte dedizierte Host und anschließend ein reduzierter Preis für Ihre App Service-Planinstanzen in Rechnung gestellt.

    ![App Service-Umgebung: Hostingauswahl](./media/creation/creation-hosting.png)

4. Netzwerk: Hier können Sie Ihr virtuelles Netzwerk und Ihr Subnetz auswählen oder erstellen. Wenn Sie eine ASE mit interner VIP erstellen, können Sie private Azure DNS-Zonen konfigurieren, damit Ihr Domänensuffix auf Ihre ASE verweist. Details zur manuellen DNS-Konfiguration finden Sie im DNS-Abschnitt unter [Verwenden einer App Service-Umgebung][UsingASE].

    ![App Service-Umgebung: Netzwerkauswahl](./media/creation/creation-networking.png)

5. Überprüfen und erstellen: Überprüfen Sie, ob die Konfiguration korrekt ist, und wählen Sie „Erstellen“ aus. Die Erstellung Ihrer ASE kann bis zu knapp zwei Stunden dauern. 

Nachdem die Erstellung Ihrer ASE abgeschlossen ist, können Sie sie als Speicherort auswählen, wenn Sie Ihre Apps erstellen. Weitere Informationen zum Erstellen von Apps in Ihrer ASE sowie zum Verwalten Ihrer ASE finden Sie unter [Verwenden einer App Service-Umgebung][UsingASE].

## <a name="dedicated-hosts"></a>Dedizierte Hosts

Die ASE wird normalerweise auf VMs bereitgestellt, die auf einem mehrinstanzfähigen Hypervisor bereitgestellt werden. Wenn Sie auf dedizierten Systemen (Hardware eingeschlossen) bereitstellen müssen, können Sie Ihre ASE auf dedizierten Hosts bereitstellen. Dedizierte Hosts werden paarweise bereitgestellt, um Redundanz zu gewährleisten. Dedizierte hostbasierte ASE-Bereitstellungen werden anders berechnet als normal. Es fällt eine Gebühr für den dedizierten Host und dann für jede App Service-Plan-Instanz eine weitere Gebühr an. Bereitstellungen in Hostgruppen sind nicht zonenredundant. Wenn die Bereitstellung auf dedizierten Hosts erfolgen soll, wählen Sie auf der Registerkarte „Hosting“ für die Hostgruppenbereitstellung die Option **Aktivieren** aus.

<!--Links-->
[Intro]: ./overview.md
[UsingASE]: ./using.md
