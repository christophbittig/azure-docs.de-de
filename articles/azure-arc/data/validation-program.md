---
title: Validierung von Datendiensten mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
description: Beschreibt das Validierungsprogramm für Kubernetes-Distributionen für Datendienste mit Azure Arc-Unterstützung.
keywords: Kubernetes, Arc, Azure, K8s, Validierung, Datendienste, SQL Managed Instance
ms.openlocfilehash: 1935648b2d3de48aecf9878865f377c41c8498cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355465"
---
# <a name="azure-arc-enabled-data-services-kubernetes-validation"></a>Kubernetes-Validierung von Datendiensten mit Azure Arc-Unterstützung

Das Team für Datendienste mit Azure Arc-Unterstützung hat mit Branchenpartnern zusammengearbeitet, um bestimmte Distributionen und Lösungen hinsichtlich des Hostings von Datendiensten mit Azure Arc-Unterstützung zu validieren. Diese Validierung erweitert die [Validierung von Kubernetes mit Azure Arc-Unterstützung](../kubernetes/validation-program.md) für die Datendienste. In diesem Artikel werden Partnerlösungen, Versionen, Kubernetes-Versionen, SQL Server-Versionen und PostgreSQL Hyperscale-Versionen identifiziert, die hinsichtlich der Unterstützung der Datendienste überprüft wurden. 

Informationen dazu, wie alle Komponenten mit Azure Arc-Unterstützung validiert sind, finden Sie unter [Übersicht über das Validierungsprogramm](../validation-program/overview.md).

> [!NOTE]
> Zum aktuellen Zeitpunkt ist SQL Managed Instance mit Azure Arc-Unterstützung in ausgewählten Regionen allgemein verfügbar.
>
> Azure Arc-fähiges PostgreSQL Hyperscale ist in ausgewählten Regionen als Vorschauversion verfügbar.

## <a name="partners"></a>Partner

### <a name="dell"></a>Dell

|Lösung und Version | Kubernetes-Version | Version der Datendienste mit Azure Arc-Unterstützung | Version der SQL-Engine | PostgreSQL Hyperscale-Version
|-----|-----|-----|-----|-----|
| Dell EMC PowerFlex |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerFlex, Version 3.6 |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerFlex CSI, Version 1.4 |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerStore X|1.20.6|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) |postgres 12.3 (Ubuntu 12.3-1) |
| Powerstore T|1.20.6|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) |postgres 12.3 (Ubuntu 12.3-1)|

### <a name="nutanix"></a>Nutanix

|Lösung und Version | Kubernetes-Version | Version der Datendienste mit Azure Arc-Unterstützung | SQL Server-Version | PostgreSQL Hyperscale-Version
|-----|-----|-----|-----|-----|
| Karbon 2.2<br/>AOS: 5.19.1.5<br/>AHV: 20201105.1021<br/>PC: Version pc.2021.3.02<br/> | 1.19.8-0 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4123)|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="purestorage"></a>PureStorage

|Lösung und Version | Kubernetes-Version | Version der Datendienste mit Azure Arc-Unterstützung | SQL Server-Version | PostgreSQL Hyperscale-Version
|-----|-----|-----|-----|-----|
| Portworx Enterprise 2.7 | 1.20.7 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4138)||

### <a name="red-hat"></a>Red Hat

|Lösung und Version | Kubernetes-Version | Version der Datendienste mit Azure Arc-Unterstützung | SQL Server-Version | PostgreSQL Hyperscale-Version
|-----|-----|-----|-----|-----|
| OpenShift 7.13 | 1.20.0 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4138)|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="vmware"></a>VMware

|Lösung und Version | Kubernetes-Version | Version der Datendienste mit Azure Arc-Unterstützung | SQL Server-Version | PostgreSQL Hyperscale-Version
|-----|-----|-----|-----|-----|
| TKGm v1.3.1 | 1.20.5 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4123)|postgres 12.3 (Ubuntu 12.3-1)|

## <a name="data-services-validation-process"></a>Validierungsprozess für Datendienste

Das Sonobuoy-Plug-In für Azure Arc-fähige Datendienste automatisiert die Bereitstellung und das Testen von Datendiensten mit Azure Arc-Unterstützung in einem Kubernetes-Cluster.

### <a name="prerequisites"></a>Voraussetzungen

Installieren Sie Tools: 

- [Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata)
- [kubectl](https://kubernetes.io/docs/home/)
- [Azure Data Studio – Insider-Build](https://github.com/microsoft/azuredatastudio)

Erstellen Sie eine Kubernetes-Konfigurationsdatei, die für den Zugriff auf den Kubernetes-Zielcluster konfiguriert und als aktueller Kontext festgelegt ist. Wie dies generiert und lokal auf Ihren Computer übertragen wird, ist von Plattform zu Plattform unterschiedlich. Siehe [Kubernetes.io](https://kubernetes.io/docs/home/).

### <a name="process"></a>Prozess

Die Konformitätstests werden im Rahmen der Validierung der Datendienste mit Azure Arc-Unterstützung durchgeführt. Eine Voraussetzung für die Ausführung dieser Tests ist die Weitergabe Tests des Kubernetes mit Azure Arc-Unterstützung für die verwendete Kubernetes-Distribution.

Mit diesen Tests wird überprüft, ob das Produkt den Anforderungen an die Ausführung und den Betrieb von Datendiensten entspricht. Dies hilft bei der Bewertung, ob das Produkt für Bereitstellungen in Unternehmen bereit ist.

Die Tests für Datendienste decken Folgendes im indirekt verbundenen Modus ab.

1. Bereitstellen des Datencontrollers im indirekten Modus
2. Bereitstellen von [SQL Managed Instance mit Azure Arc-Unterstützung](create-sql-managed-instance.md)
3. Bereitstellen von [Azure Arc-fähigem PostgreSQL Hyperscale](create-postgresql-hyperscale-server-group.md)
4. Aufskalieren von [PostgreSQL Hyperscale](scale-out-in-postgresql-hyperscale-server-group.md)

Weitere Tests werden in zukünftigen Releases von Datendiensten mit Azure Arc-Unterstützung hinzugefügt.

## <a name="additional-information"></a>Zusätzliche Informationen

- [Übersicht über das Validierungsprogramm](../validation-program/overview.md)
- [Validierung von Kubernetes mit Azure Arc-Unterstützung](../kubernetes/validation-program.md)
- [Azure Arc-Validierungsprogramm – GitHub-Projekt](https://github.com/Azure/azure-arc-validation/)

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Datencontrollers](create-data-controller.md)
