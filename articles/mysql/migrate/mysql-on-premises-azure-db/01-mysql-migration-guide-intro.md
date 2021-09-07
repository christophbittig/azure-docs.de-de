---
title: 'Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Einführung'
description: Leitfaden für die Migration von einer lokalen MySQL-Instanz zu Azure Database for MySQL
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5023d53132985875fc0b927148a6efbfb0239cc3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085126"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

Dieser Migrationsleitfaden enthält aufeinander aufbauende und umsetzbare Informationen für MySQL-Kunden und Softwareintegratoren, die MySQL-Workloads zu [Azure Database for MySQL](../../overview.md) migrieren möchten. Dieser Leitfaden enthält anwendbare Informationen, die für die meisten Fälle gelten, sowie Anleitungen, die eine erfolgreiche Planung und Ausführung einer MySQL-Migration zu Azure ermöglichen.

Beim Verschieben vorhandener Datenbanken und MySQL-Workloads in die Cloud können Herausforderungen in Bezug auf die Workloadfunktionalität und die Konnektivität vorhandener Anwendungen auftreten. Dieser Leitfaden enthält hilfreiche Links und Empfehlungen, bei denen der Schwerpunkt auf einer erfolgreichen Migration liegt und mit denen sichergestellt wird, dass Workloads und Anwendungen weiterhin wie ursprünglich vorgesehen funktionieren.

Die bereitgestellten Informationen konzentrieren sich auf eine Customer Journey unter Verwendung des [Microsoft Cloud Adoption Framework](/azure/cloud-adoption-framework/get-started/), um Bewertungs- und Migrationsaktivitäten sowie Aktivitäten nach der Optimierung für eine Azure Database for MySQL-Umgebung durchzuführen.

## <a name="mysql"></a>MySQL

MySQL blickt auf eine lange Geschichte in der Open-Source-Community zurück und wird von Unternehmen auf der ganzen Welt gerne für Websites und andere unternehmenskritische Anwendungen eingesetzt. Dieser Leitfaden unterstützt Administratoren, die die Migration vorbereiten, planen und ausführen sollen. Administratoren, die noch nicht mit MySQL vertraut sind, können auch die [MySQL-Dokumentation](https://dev.mysql.com/doc/) lesen, um ausführlichere Informationen zur internen Funktionsweise von MySQL zu erhalten. Darüber hinaus enthält dieser Leitfaden in den einzelnen Abschnitten Links zu verschiedenen Referenzartikeln, die hilfreiche Informationen und Tutorials enthalten.

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

[Azure Database for MySQL](../../overview.md) ist ein PaaS-Angebot (Platform-as-a-Service) von Microsoft, bei dem die MySQL-Umgebung vollständig verwaltet wird. In dieser vollständig verwalteten Umgebung werden Betriebssystem- und Softwareupdates automatisch angewendet sowie Hochverfügbarkeit und Schutz der Daten implementiert.

Zusätzlich zum PaaS-Angebot kann MySQL weiterhin auf virtuellen Azure-Computern ausgeführt werden. Lesen Sie den Artikel [Auswählen der richtigen MySQL Server-Option in Azure](../../select-right-deployment-type.md), um zu entscheiden, welcher Bereitstellungstyp sich am besten für die Zieldatenworkload eignet.

![Vergleich von MySQL-Umgebungen](./media/image3.jpg)

**Vergleich von MySQL-Umgebungen**

Dieser Leitfaden konzentriert sich aufgrund der verschiedenen Vorteile gegenüber IaaS (Infrastructure-as-a-Service) ausschließlich auf die Migration der lokalen MySQL-Workloads zum PaaS-Angebot für Azure Database for MySQL. Zu diesen Vorteilen zählen u. a. das Hochskalieren und horizontale Skalieren, nutzungsbasierte Bezahlung, Hochverfügbarkeit, Sicherheit und Verwaltbarkeitsfeatures.  

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Repräsentativer Anwendungsfall](./02-representative-use-case.md)
