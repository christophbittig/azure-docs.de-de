---
title: Datei einfügen
description: Datei einfügen
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 10/15/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: ac789ee1971eb5e310b2f7d3ae35412d9386b560
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571120"
---
Azure Database for PostgreSQL – Hyperscale (Citus) unterstützt drei Netzwerkoptionen:

* Kein Zugriff
  * Dies ist die Standardeinstellung für eine neu erstellte Servergruppe, wenn der öffentliche oder private Zugriff nicht aktiviert ist. Keine Computer, weder innerhalb noch außerhalb von Azure, können eine Verbindung mit den Datenbankknoten herstellen.
* Öffentlicher Zugriff
  * Dem Koordinatorknoten wird eine öffentliche IP-Adresse zugewiesen.
  * Der Zugriff auf den Koordinatorknoten wird durch eine Firewall geschützt.
  * Optional kann der Zugriff auf alle Workerknoten aktiviert werden. In diesem Fall werden den Workerknoten öffentliche IP-Adressen zugewiesen und sie werden durch dieselbe Firewall geschützt.
* Privater Zugriff (Vorschau)
  * Den Knoten der Servergruppe werden nur private IP-Adressen zugewiesen.
  * Jeder Knoten erfordert einen privaten Endpunkt, damit Hosts im ausgewählten virtuellen Netzwerk auf die Hyperscale (Citus)-Knoten zugreifen können.
  * Sicherheitsfeatures virtueller Azure-Netzwerke wie Netzwerksicherheitsgruppen können für die Zugriffssteuerung verwendet werden.

Wenn Sie eine Hyperscale (Citus)-Servergruppe erstellen, können Sie den öffentlichen oder privaten Zugriff aktivieren oder sich für die Standardeinstellung „Kein Zugriff“ entscheiden. Nachdem die Servergruppe erstellt wurde, können Sie zwischen öffentlichem oder privatem Zugriff wechseln oder beide gleichzeitig aktivieren.
