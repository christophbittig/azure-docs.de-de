---
title: Red Hat-Workloads in Azure – Übersicht | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den in Azure verfügbaren Red Hat-Produktangeboten.
author: mamccrea
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: mamccrea
ms.openlocfilehash: 30107ce9bfc611faaca37bf79a0b53f68c9cbd70
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458433"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-Workloads in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs 

Red Hat-Workloads werden in einer Reihe von Angeboten in Azure unterstützt. Red Hat Enterprise Linux-Images (RHEL) gehören ebenso wie die Red Hat Update Infrastructure (RHUI) zum Kern von RHEL-Workloads.

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux-Images

Azure bietet eine umfassende Palette an RHEL-Images. Diese Images werden über zwei verschiedene Lizenzierungsmodelle zur Verfügung gestellt: nutzungsbasierte Bezahlung und Nutzung eigener Abonnements (Bring-Your-Own-Subscription, BYOS). Neue RHEL-Images werden bei Erscheinen neuer RHEL-Versionen in Azure veröffentlicht und während ihres gesamten Lebenszyklus bei Bedarf aktualisiert.

### <a name="pay-as-you-go-images"></a>Images mit nutzungsbasierter Bezahlung

Azure bietet eine Reihe von RHEL-Images mit nutzungsbasierter Bezahlung. Diese Images umfassen die Berechtigung zur ordnungsgemäßen Nutzung von RHEL und sind einer Updatequelle (Red Hat Update Infrastructure) zugeordnet. Bei diesen Images wird eine Premiumgebühr für die RHEL-Berechtigung und -Updates berechnet. Folgende RHEL-Imagevarianten mit nutzungsbasierter Bezahlung sind verfügbar:

* RHEL-Standardimages
* RHEL für SAP
* RHEL für SAP mit Hochverfügbarkeits- und Updatediensten

Die Verwendung von Images mit nutzungsbasierter Bezahlung empfiehlt sich, wenn Sie sich keine Gedanken um die separate Bezahlung der entsprechenden Anzahl von Abonnements machen möchten.

### <a name="red-hat-gold-images"></a>Red Hat Gold-Images

Azure bietet auch Red Hat Gold-Images (`rhel-byos`). Diese Images eignen sich für Kunden, die bereits über Red Hat-Abonnements verfügen und diese in Azure verwenden möchten. Ihre vorhandenen Red Hat-Abonnements müssen für Red Hat Cloud Access aktiviert werden, um sie in Azure nutzen zu können. Der Zugriff auf diese Images wird automatisch gewährt, wenn Ihre Red Hat-Abonnements für Cloud Access aktiviert sind und die Qualifizierungsanforderungen erfüllen. Mit diesen Images können Kunden eine doppelte Abrechnung vermeiden, wie sie bei der Verwendung von Images mit nutzungsbasierter Bezahlung auftreten kann.
* Informationen zum Aktivieren von Red Hat-Abonnements für Cloud Access mit Azure finden Sie [hier](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/red-hat-cloud-access-program-overview_cloud-access#ref_ca-unit-conversion_cloud-access).
* Wie Sie Red Hat Gold-Images über das Azure-Portal, mithilfe der Azure-Befehlszeilenschnittstelle oder per PowerShell-Cmdlet finden, erfahren Sie [hier](./byos.md).

> [!NOTE]
> Als doppelte Fakturierung wird eine Situation bezeichnet, in der ein Benutzer zweimal für RHEL-Abonnements bezahlt. Dieser Fall tritt in der Regel ein, wenn ein Kunde den Red Hat-Abonnement-Manager verwendet, um eine Berechtigung an einen virtuellen RHEL-Computer mit nutzungsbasierter Bezahlung anzufügen. Wenn ein Kunde also beispielsweise den Abonnement-Manager verwendet, um eine Berechtigung für SAP-Pakete an ein RHEL-Image mit nutzungsbasierter Bezahlung anzufügen, kommt es indirekt zu einer doppelten Abrechnung, da der Kunde zweimal für RHEL bezahlt: einmal über die Premiumgebühr der nutzungsbasierten Bezahlung und einmal über das SAP-Abonnement. Bei Benutzern von BYOS-Images passiert das nicht.

### <a name="generation-2-images"></a>Images der Generation 2

Virtuelle Computer der 2. Generation bieten im Vergleich zu virtuellen Computern der 1. Generation einige neuere Features. Weitere Informationen finden Sie in der [Dokumentation zur 2. Generation](../../generation-2.md). Der Hauptunterschied aus Sicht eines RHEL-Images besteht darin, dass virtuelle Computer der 2. Generation eine UEFI anstelle einer BIOS-Firmwareschnittstelle verwenden. Außerdem nutzen sie beim Systemstart eine GUID-Partitionstabelle (GPT) anstelle eines Master Boot Records (MBR). Dank der Verwendung einer GPT können unter anderem Betriebssystemdatenträger größer als 2 TB sein. Darüber hinaus werden [virtuelle Computer der Mv2-Serie](../../mv2-series.md) nur mit Images der Generation 2 ausgeführt.

RHEL-Images der 2. Generation sind im Azure Marketplace verfügbar. Suchen Sie in der Liste mit allen Images, die bei Verwendung der Azure-Befehlszeilenschnittstelle angezeigt wird, nach „gen2“ in der Image-SKU. Navigieren Sie im Rahmen des VM-Bereitstellungsprozesses zur Registerkarte **Erweitert**, um einen virtuellen Computer der 2. Generation bereitzustellen.

## <a name="red-hat-update-infrastructure"></a>Red Hat-Updateinfrastruktur

Azure stellt die Red Hat-Updateinfrastruktur nur für virtuelle RHEL-Computer mit nutzungsbasierter Bezahlung bereit. Bei der RHUI handelt es sich im Grunde um eine Spiegelung der Red Hat-CDNs, der Zugriff darauf ist aber nur über die virtuellen RHEL-Computer mit nutzungsbasierter Bezahlung möglich. Sie haben Zugriff auf die entsprechenden Pakete (abhängig vom bereitgestellten RHEL-Image). So hat beispielsweise ein RHEL für SAP-Image neben den grundlegenden RHEL-Paketen auch Zugriff auf die SAP-Pakete.

### <a name="rhui-update-behavior"></a>Updateverhalten der RHUI

RHEL-Images, die mit der RHUI verbunden sind, werden standardmäßig auf die neueste Nebenversion von RHEL aktualisiert, wenn ein Vorgang vom Typ `yum update` ausgeführt wird. Das bedeutet, dass ein virtueller Computer mit RHEL 7.4 auf RHEL 7.7 aktualisiert wird, wenn auf dem virtuellen Computer ein Vorgang vom Typ `yum update` ausgeführt wird. Dieses Verhalten ist für die RHUI beabsichtigt. Wenn Sie das Upgradeverhalten anpassen möchten, können Sie von regulären RHEL-Repositorys zu [EUS-Repositorys (Extended Update Support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms) wechseln.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [RHEL-Images in Azure](./redhat-images.md).
* Erfahren Sie mehr über die [Red Hat-Updateinfrastruktur](./redhat-rhui.md).
* Erfahren Sie mehr über das [Angebot für Red Hat Gold-Images (`rhel-byos`)](./byos.md).
