---
title: 'Azure VMware Solution by CloudSimple: Einrichten von Workload-DNS und -DHCP für die private Cloud'
description: Beschreibt das Einrichten von DNS und DHCP für Anwendungen und Workloads, die in ihrer privaten CloudSimple-Cloudumgebung ausgeführt werden.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e947f5f277378b446a84bd8457fd02e55f91d0a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612843"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Einrichten von DNS- und DHCP-Anwendungen und -Workloads in der privaten CloudSimple-Cloud

Anwendungen und Workloads, die in einer privaten Cloudumgebung ausgeführt werden, erfordern Namensauflösung und DHCP-Dienste für Nachschlagen und IP-Adresszuweisung.  Zur Bereitstellung dieser Dienste ist eine ordnungsgemäße DHCP- und DNS-Infrastruktur erforderlich.  Sie können einen virtuellen Computer konfigurieren, um diese Dienste in Ihrer privaten Cloudumgebung bereitzustellen.  

## <a name="prerequisites"></a>Voraussetzungen

* Eine verteilte Portgruppe mit konfiguriertem VLAN
* Routeneinrichtung zu lokalen oder internetbasierten DNS-Servern
* Vorlage für virtuelle Maschinen oder ISO zum Erstellen eines virtuellen Computers

## <a name="linux-based-dns-server-setup"></a>Setup für Linux-basiertes DNS-Server

Linux bietet verschiedene Pakete für das Einrichten von DNS-Servern.  Dies ist ein [Beispielsetup von DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) mit Anleitungen zu Einrichten eines Open-Source-BIND-DNS-Servers.

## <a name="windows-based-setup"></a>Windows-basiertes Setup

In diesen Microsoft-Themen wird beschrieben, wie Sie einen Windows-Server als DNS-Server und als DHCP-Server einrichten.

* [Windows-Server als DNS-Server](/windows-server/networking/dns/dns-top)
* [Windows-Server als DHCP-Server](/windows-server/networking/technologies/dhcp/dhcp-top)
