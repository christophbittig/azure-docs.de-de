---
title: Microsoft Azure-Zertifizierungen für SAP | Microsoft-Dokumentation
description: Aktualisierte Liste der aktuellen Konfigurationen und Zertifizierungen von SAP auf der Azure-Plattform.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/21/2021
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: a367c3753ae6d02dd4267a0135960ed630ef78c0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720666"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-Zertifizierungen und -Konfigurationen in Microsoft Azure

SAP und Microsoft können auf eine lange Geschichte enger Zusammenarbeit in einer starken Partnerschaft zurückblicken, die den Kunden beider Unternehmen Vorteile bringt. Microsoft führt kontinuierlich Aktualisierungen seiner Plattform durch und übermittelt stets neue Zertifizierungsinformationen an SAP, um sicherzustellen, dass Microsoft Azure die optimale Plattform zum Ausführen von SAP-Workloads ist. In den folgenden Tabellen werden die in Azure unterstützten Konfigurationen und die anwachsende Liste von SAP-Zertifizierungen beschrieben. Diese Liste ist eine Übersicht, die hier und da von den offiziellen SAP-Listen abweichen kann. Wie Sie die detaillierten Daten erhalten, erfahren Sie im Artikel [Welche SAP-Software wird für Azure-Bereitstellungen unterstützt?](./sap-supported-product-on-azure.md).

## <a name="sap-hana-certifications"></a>SAP HANA-Zertifizierungen
Referenzen:

- [SAP HANA-zertifizierte IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) zur Unterstützung für SAP HANA für native Azure-VMs und HANA (große Instanzen)

| SAP-Produkt | Unterstütztes Betriebssystem | Azure-Angebote |
| --- | --- | --- |
| Business One unter HANA | SUSE Linux Enterprise | [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:120) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:125) |
| HANA Enterprise für BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:105) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:105) |


## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-Zertifizierungen
Microsoft Azure ist für die folgenden SAP-Produkte zertifiziert. Microsoft und SAP bieten vollständigen Support für die Plattform.
Referenzen:

- [1928533 – SAP-Anwendungen in Azure: Unterstützte Produkte und Azure-VM-Typen](https://launchpad.support.sap.com/#/notes/1928533) für alle SAP NetWeaver-basierten Anwendungen, einschließlich SAP TREX, SAP LiveCache und SAP Content Server. sowie alle Datenbanken mit Ausnahme von SAP HANA


| SAP-Produkt | Gastbetriebssystem | RDBMS | VM-Typ (virtueller Computer) |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (nur Windows und Oracle Linux), DB2, SAP ASE |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, D2as_v4 bis D64as_v4, E2s_v3 bis E64s_v3, E2as_v4 bis E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (nur Windows und Oracle Linux), DB2, SAP ASE |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, D2as_v4 bis D64as_v4, E2s_v3 bis E64s_v3, E2as_v4 bis E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP BusinessObjects BI | Windows |– |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, D2as_v4 bis D64as_v4, E2s_v3 bis E64s_v3, E2as_v4 bis E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (nur Windows und Oracle Linux), DB2, SAP ASE |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, D2as_v4 bis D64as_v4, E2s_v3 bis E64s_v3, E2as_v4 bis E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Andere in Azure unterstützte SAP-Workloads

| SAP-Produkt | Gastbetriebssystem | RDBMS | VM-Typ (virtueller Computer) |
| --- | --- | --- | --- |
| SAP Business One unter SQL Server | Windows  | SQL Server | Alle NetWeaver-zertifizierten VM-Typen<br /> [SAP-Hinweis 928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows und Linux | | Alle NetWeaver-zertifizierten VM-Typen<br /> SAP-Hinweis 2451795 |
| SAP Business Objects BI-Plattform | Windows und Linux | | SAP-Hinweis 2145537 |
| SAP Data Services 4.2 | | | SAP-Hinweis 2288344 |
| SAP Hybris Commerce-Plattform  | Windows | SQL Server, Oracle | Alle NetWeaver-zertifizierten VM-Typen <br /> [Hybris-Dokumentation](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce-Plattform  | SLES 12 oder aktueller | SAP HANA | Alle NetWeaver-zertifizierten VM-Typen <br /> [Hybris-Dokumentation](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce-Plattform  | RHEL 7 oder aktueller | SAP HANA | Alle NetWeaver-zertifizierten VM-Typen <br /> [Hybris-Dokumentation]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) Commerce-Plattform 1811 und höher  | Windows, SLES oder RHEL | SQL Azure-Datenbank | Alle NetWeaver-zertifizierten VM-Typen <br /> [Hybris-Dokumentation](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
