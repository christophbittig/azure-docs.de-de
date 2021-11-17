---
title: Das Linkfeature
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie mehr über das Linkfeature für Azure SQL Managed Instance, um Daten kontinuierlich aus SQL Server in die Cloud zu replizieren oder Ihre SQL Server-Datenbanken mit der geringstmöglichen Ausfallzeit zu migrieren.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, danil
ms.date: 11/05/2021
ms.openlocfilehash: 19851f1ff3bef1ab57d23013ada78bec5665b7c5
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989799"
---
# <a name="link-feature-for-azure-sql-managed-instance-limited-preview"></a>Linkfeature für Azure SQL Managed Instance (eingeschränkte Vorschauversion)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Das neue Linkfeature in Azure SQL Managed Instance verbindet Ihre SQL Server-Instanzen, die an einem beliebigen Ort gehostet werden, mit SQL Managed Instance und bietet Hybridflexibilität und Datenbankmobilität. Bei einem Ansatz mit Datenreplikation in die Cloud in nahezu Echtzeit können Sie Workloads in ein sekundäres, schreibgeschütztes Replikat in Azure auslagern, um die Vorteile von nur in Azure verfügbaren Features, Leistungs- und Skalierungsmöglichkeiten zu nutzen. 

Nach einem katastrophalen Ereignis können Sie Ihre schreibgeschützten Workloads weiterhin in SQL Managed Instance in Azure ausführen. Sie können nach Wahl auch eine oder mehrere Anwendungen von SQL Server zu SQL Managed Instance migrieren, und zwar zur gleichen Zeit, in Ihrem eigenen Tempo und mit der geringstmöglichen Ausfallzeit im Vergleich zu anderen derzeit in Azure verfügbaren Lösungen.

> [!NOTE]
> Das Linkfeature wird in der eingeschränkten öffentlichen Vorschauversion veröffentlicht und derzeit nur von SQL Server 2019 Enterprise Edition CU13 (oder höher) unterstützt. [Registrieren Sie sich jetzt](https://aka.ms/mi-link-signup) für die eingeschränkte öffentliche Vorschau. 

## <a name="overview"></a>Übersicht

Die zugrundeliegende Technologie der nahezu in Echtzeit erfolgenden Datenreplikation zwischen SQL Server und SQL Managed Instance basiert auf verteilten Verfügbarkeitsgruppen, die Teil des allgemein bekannten und bewährten Technologiestapels von Always On-Verfügbarkeitsgruppen sind. Dehnen Sie Ihre lokale SQL Server-Verfügbarkeitsgruppe auf sichere Weise auf SQL Managed Instance in Azure aus. 

Dazu müssen weder eine Verfügbarkeitsgruppe noch mehrere Knoten vorhanden sein. Der Link unterstützt SQL Server-Instanzen auf Einzelknoten ohne vorhandene Verfügbarkeitsgruppen sowie SQL Server-Instanzen mit mehreren Knoten und vorhandenen Verfügbarkeitsgruppen. Über den Link können Sie die modernen Vorteile von Azure nutzen, ohne Ihren gesamten SQL Server-Datenbestand in die Cloud zu migrieren.

Sie können den Link so lange aufrechterhalten, wie Sie ihn brauchen, also über Monate und sogar Jahre. Wenn Sie bereit sind, im Rahmen Ihrer Modernisierungsstrategie zu Azure zu migrieren, ermöglicht der Link eine erheblich verbesserte Migrationserfahrung mit einem Minimum an Ausfallzeit im Vergleich zu allen anderen heute verfügbaren Optionen und bietet eine echte Onlinemigration zu SQL Managed Instance.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

Daten, die über das Linkfeature von SQL Server zu Azure SQL Managed Instance repliziert werden, können in einer Reihe von Szenarien zum Einsatz kommen, z. B: 

- **Verwenden von Azure-Diensten ohne Migration in die Cloud** 
- **Auslagern schreibgeschützter Workloads in Azure** 
- **Migrieren zu Azure**

![Hauptszenario für Managed Instance-Link](./media/managed-instance-link/mi-link-main-scenario.png)


### <a name="use-azure-services"></a>Verwenden von Azure-Diensten 

Verwenden Sie das Linkfeature, um Azure-Dienste mit SQL Server-Daten ohne Migration in die Cloud zu nutzen. Beispiele hierfür sind Berichterstellung, Analysen, Sicherungen, maschinelles Lernen und andere Aufträge, die Daten an Azure senden. 

### <a name="offload-workloads-to-azure"></a>Auslagern von Workloads in Azure 

Sie können mit dem Linkfeature auch Workloads in Azure auslagern. So kann eine Anwendung beispielsweise SQL Server für Workloads mit Lese-/Schreibzugriff nutzen, während schreibgeschützte Workloads an SQL Managed Instance in einer der über 60 Azure-Regionen weltweit ausgelagert werden. Sobald der Link eingerichtet ist, ist der Lese-/Schreibzugriff auf die primäre Datenbank in SQL Server möglich, während auf die replizierten Daten in SQL Managed Instance in Azure nur Lesezugriff besteht. Dies ermöglicht verschiedene Szenarien, in denen replizierte Datenbanken in SQL Managed Instance für die horizontale Leseskalierung und das Auslagern schreibgeschützter Workloads in Azure verwendet werden können. SQL Managed Instance kann parallel dazu auch unabhängige Datenbanken mit Lese- und Schreibzugriff hosten. Dies ermöglicht das Kopieren der replizierten Datenbank in eine andere Datenbank mit Lese-/Schreibzugriff in derselben verwalteten Instanz zur weiteren Verarbeitung der Daten.

Der Link ist auf eine Datenbank beschränkt (ein Link pro Datenbank) und ermöglicht so die Konsolidierung und Dekonsolidierung von Workloads in Azure. So können Sie beispielsweise Datenbanken von mehreren SQL Server-Instanzen in eine einzige SQL Managed Instance in Azure replizieren (Konsolidierung) oder Datenbanken aus einer einzelnen SQL Server-Instanz in mehrere verwaltete Instanzen über eine 1:1-Beziehung zwischen einer Datenbank und einer verwalteten Instanz replizieren – und zwar in jede beliebige Azure-Region weltweit (Dekonsolidierung). Letzteres bietet Ihnen eine effiziente Möglichkeit, Ihre Workloads Ihren Kunden in jeder beliebigen Region weltweit schnell näher zu bringen, die Sie als schreibgeschützte Replikate nutzen können.

### <a name="migrate-to-azure"></a>Migrieren zu Azure 

Das Linkfeature erleichtert auch die Migration von SQL Server zu SQL Managed Instance und ermöglicht Folgendes: 

- Die leistungsfähigste Migration mit geringstmöglicher Ausfallzeit im Vergleich zu allen anderen derzeit verfügbaren Lösungen
- Echte Onlinemigration zu SQL Managed Instance auf jeder Dienstebene 

Da das Linkfeature eine Migration mit minimaler Ausfallzeit ermöglicht, können Sie zu Ihrer verwalteten Instanz migrieren und gleichzeitig Ihre primäre Workload online betreiben. Während die Onlinemigration bei der Migration zur Dienstebene „Universell“ bisher auch mit anderen Lösungen möglich war, erlaubt das Linkfeature nun auch echte Onlinemigrationen zur Dienstebene „Unternehmenskritisch“. 

## <a name="how-it-works"></a>Funktionsweise

Die zugrunde liegende Technologie hinter dem Linkfeature für SQL Managed Instance sind verteilte Verfügbarkeitsgruppen. Die Lösung unterstützt Systeme mit einem Knoten ohne vorhandene Verfügbarkeitsgruppen bzw. Systeme mit mehreren Knoten und vorhandenen Verfügbarkeitsgruppen.  

![Funktionsweise des Linkfeatures für SQL Managed Instance](./media/managed-instance-link/mi-link-ag-dag.png)

Zwischen einem lokalen Netzwerk und Azure besteht sichere Konnektivität, z. B. per VPN oder Express Route. Wenn SQL Server auf einem virtuellen Azure-Computer gehostet wird, kann der interne Azure-Backbone zwischen dem virtuellen Computer und der verwalteten Instanz verwendet werden, z. B. globales VNet-Peering. Die Vertrauensstellung zwischen beiden Systemen gründet auf der zertifikatbasierten Authentifizierung, für die SQL Server und SQL Managed Instance ihre öffentlichen Schlüssel austauschen.

Es können bis zu 100 Links zwischen derselben oder verschiedenen SQL Server-Quellen und einer einzigen SQL Managed Instance bestehen. Dieser Grenzwert richtet sich nach der Anzahl der Datenbanken, die zu diesem Zeitpunkt in einer verwalteten Instanz gehostet werden können. Ebenso kann eine einzelne SQL Server-Instanz mehrere parallele Links zur Datenbankreplikation mit mehreren verwalteten Instanzen in verschiedenen Azure-Regionen in einer 1:1-Beziehung zwischen einer Datenbank und einer verwalteten Instanz herstellen. Für das Feature muss SQL Server 2019 mit CU13 oder höher installiert sein.

## <a name="sign-up-for-link"></a>Registrieren für den Link

Für das Linkfeature benötigen Sie Folgendes:

- SQL Server 2019 Enterprise Edition mit lokal oder auf einer Azure-VM installiertem [CU13 (oder höher)](https://support.microsoft.com/topic/kb5005679-cumulative-update-13-for-sql-server-2019-5c1be850-460a-4be4-a569-fe11f0adc535).
- Zwischen SQL Server und verwalteter Instanz ist Netzwerkkonnektivität erforderlich. Wenn Ihre SQL Server-Instanz lokal ausgeführt wird, verwenden Sie eine VPN-Verbindung oder ExpressRoute. Wenn Ihre SQL Server-Instanz auf einer Azure-VM ausgeführt wird, stellen Sie Ihre VM entweder im selben Subnetz wie Ihre verwaltete Instanz bereit, oder verwenden Sie globales VNet-Peering, um zwei separate Subnetze zu verbinden. 
- Auf einer beliebigen Dienstebene bereitgestellte Azure SQL Managed Instance.

Über den folgenden Link können Sie sich für die eingeschränkte Vorschauversion des Linkfeatures registrieren. 

> [!div class="nextstepaction"]
> [Registrieren für die Vorschauversion des Linkfeatures](https://aka.ms/mi-link-signup)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Linkfeature finden Sie in den folgenden Themen:

- [Das Linkfeature für Azure SQL Managed Instance – Das Herstellen einer Verbindung zwischen SQL Server und Azure neu gedacht](https://aka.ms/mi-link-techblog)

Informationen zu anderen Replikationsszenarien finden Sie unter: 

- [Transaktionsreplikation mit Azure SQL Managed Instance (Vorschau)](replication-transactional-overview.md)
