---
title: Informationen zu Azure Migrate
description: Erfahren Sie mehr über den Azure Migrate-Dienst.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 7370413d792cbabf1b18db5b70da6260417c04d4
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157921"
---
# <a name="about-azure-migrate"></a>Informationen zu Azure Migrate

Dieser Artikel enthält eine kurze Übersicht über den Azure Migrate-Dienst.

Azure Migrate verfügt über einen zentralisierten Hub für die Bewertung und die Migration von lokaler Infrastruktur, Anwendungen und Daten zu Azure. Er bietet Folgendes:

- **Vereinheitlichte Migrationsplattform**: Ein einzelnes Portal zum Starten, Ausführen und Nachverfolgen Ihrer Migration zu Azure
- **Verfügbare Tools**: Eine Reihe von Tools für Bewertung und Migration. Zu Azure Migrate-Tools zählen „Azure Migrate: Ermittlung und Bewertung“ und „Azure Migrate: Servermigration“. Azure Migrate kann auch in andere Azure-Dienste und -Tools sowie Angebote von unabhängigen Softwareanbietern (Independent Software Vendors, ISVs) integriert werden.
- **Bewertung und Migration**: Im Azure Migrate-Hub können Sie Folgendes bewerten und migrieren:
    - **Server, Datenbanken und Web-Apps**: Bewerten Sie lokale Server (Web-Apps und SQL Server-Instanzen eingeschlossen), und migrieren Sie sie zu Azure-VMs oder Azure VMware Solution (AVS, Vorschau).
    - **Datenbanken**: Bewerten Sie lokale Datenbanken, und migrieren Sie sie zu Azure SQL-Datenbank oder zu einer verwalteten SQL-Instanz.
    - **Webanwendungen**: Bewerten Sie lokale Webanwendungen, und migrieren Sie sie zu Azure App Service.
    - **Virtuelle Desktops**: Bewerten Sie die lokale VDI-Instanz (Virtual Desktop Infrastructure), und migrieren Sie sie zu Windows Virtual Desktop in Azure.
    - **Data:** Große Datenmengen werden mit Azure Data Box-Produkten schnell und kostengünstig zu Azure migriert.

## <a name="integrated-tools"></a>Integrierte Tools

Der Azure Migrate-Hub umfasst die folgenden Tools:

**Tool** | **Bewerten und Migrieren** | **Details**
--- | --- | ---
**Azure Migrate: Ermittlung und Bewertung** | Ermitteln und Bewerten von Servern, einschließlich SQL und Web-Apps | Als Vorbereitung für die Migration zu Azure werden lokale Server ermittelt und bewertet, die unter VMware, Hyper-V und auf physischen Servern ausgeführt werden.
**Azure Migrate: Servermigration** | Migrieren von Servern | VMware-VMs, Hyper-V-VMs, physische Server und andere virtualisierte Server und VMs der öffentlichen Cloud werden zu Azure migriert.
**Data Migration Assistant** | Bewerten Sie SQL Server-Datenbanken für die Migration zu Azure SQL-Datenbank, zur verwalteten Azure SQL-Instanz oder zu Azure-VMs, auf denen SQL Server ausgeführt wird. | Der Datenmigrations-Assistent ist ein eigenständiges Tool zur Bewertung von SQL Server-Instanzen. Ermöglicht die Ermittlung möglicher Probleme, die einer Migration im Wege stehen können. Er identifiziert nicht unterstützte Features, neue Features, von denen Sie nach der Migration profitieren können, sowie den richtigen Pfad für die Datenbankmigration. [Weitere Informationen](/sql/dma/dma-overview)
**Azure Database Migration Service** | Migrieren Sie lokale Datenbanken zu Azure-VMs, auf denen SQL Server, Azure SQL-Datenbank oder SQL Managed Instance-Instanzen ausgeführt werden. | [Weitere Informationen](../dms/dms-overview.md) zu Database Migration Service
**Movere** | Bewerten von Servern | Weitere Informationen zu Movere finden Sie [hier](#movere).
**Migrations-Assistent für Web-Apps** | Lokale Web-Apps werden bewertet und zu Azure migriert. |  Der Migrations-Assistent von Azure App Service ist ein eigenständiges Tool, mit dem lokale Websites für die Migration zu Azure App Service bewertet werden können.<br/><br/> Verwenden Sie den Migrations-Assistenten zum Migrieren von .NET- und PHP-Web-Apps zu Azure. [Weitere Informationen](https://appmigration.microsoft.com/) zum Azure App Service-Migrations-Assistenten
**Azure Data Box** | Migrieren von Offlinedaten | Verschieben Sie große Mengen an Offlinedaten mit Azure Data Box-Produkten zu Azure. [Weitere Informationen](../databox/index.yml)

> [!NOTE]
> In Azure Government können von externen integrierten Tools und ISV-Angeboten keine Daten an Azure Migrate gesendet werden. Tools können unabhängig verwendet werden.

## <a name="isv-integration"></a>ISV-Integration

Azure Migrate kann in verschiedene ISV-Angebote integriert werden. 

**ISV**    | **Feature**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrieren von Servern.
[Cloudamize](https://www.cloudamize.com/platform) | Bewerten von Servern.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Bewerten und Migrieren von Servern
[Device42](https://docs.device42.com/) | Bewerten von Servern.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Bewerten der VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrieren von Servern.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Bewerten von Servern.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Bewerten von Servern und Datenbanken
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Migrieren von Servern.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Tool „Azure Migrate: Ermittlung und Bewertung“

Mit dem Tool „Azure Migrate: Ermittlung und Bewertung“ werden lokale VMware-VMs, Hyper-V-VMs und physische Server für die Migration zu Azure ermittelt und bewertet.

Vom Tool wird Folgendes durchgeführt:

- **Azure-Bereitschaft**: Bewertet, ob lokale Server, SQL Server-Instanzen und Web-Apps bereit für die Migration zu Azure sind.
- **Azure-Dimensionierung:** Schätzt die Größe von virtuellen Azure-Computern/der Azure SQL-Konfiguration oder die Anzahl von Azure VMware Solution-Knoten nach der Migration.
- **Azure-Kostenschätzung:** Schätzt die Kosten für die Ausführung lokaler Server in Azure.
- **Abhängigkeitsanalyse:** Hiermit werden serverübergreifende Abhängigkeiten und Optimierungsstrategien für das Verschieben untereinander abhängiger Server in Azure ermittelt. Unter [Abhängigkeitsanalyse](concepts-dependency-visualization.md) erfahren Sie mehr über die Ermittlung und Bewertung.

Bei der Ermittlung und Bewertung wird eine einfache [Azure Migrate-Appliance](migrate-appliance.md) verwendet, die Sie lokal bereitstellen.

- Die Appliance wird auf einer VM oder einem physischen Server ausgeführt. Sie können sie mit einer heruntergeladenen Vorlage leicht installieren.
- Die Appliance ermittelt lokale Server. Servermetadaten und Leistungsdaten werden kontinuierlich an Azure Migrate gesendet.
- Die Applianceermittlung erfolgt ohne Agent. Auf ermittelten Servern wird nichts installiert.
- Nach der Ermittlung der Appliance fassen Sie die ermittelten Server in Gruppen zusammen und führen Bewertungen für jede Gruppe durch.

## <a name="azure-migrate-server-migration-tool"></a>Von der Azure Tool für die Servermigration

Azure Migrate: Servermigration unterstützt Sie bei der Migration von Servern zu Azure:

**Migrieren** | **Details**
--- | ---
Lokale VMware-VMs | Migrieren von virtuellen Computern zu Azure mit oder ohne Agents.<br/><br/> Bei der Migration ohne Agent verwendet die Servermigration dieselbe Appliance, die auch vom Tool „Ermittlung und Bewertung“ zum Ermitteln und Bewerten von Servern genutzt wird.<br/><br/> Bei der Agent-basierten Migration wird von der Servermigration eine Replikationsappliance verwendet.
Lokale Hyper-V-VMs | Migrieren von VMs zu Azure.<br/><br/> Von der Servermigration werden Anbieter-Agents verwendet, die auf dem Hyper-V-Host für die Migration installiert sind.
Lokale physische Server oder Server, die in anderen Clouds gehostet werden | Sie können physische Server zu Azure migrieren. Sie können auch andere virtualisierte Server sowie virtuelle Computer aus anderen öffentlichen Clouds migrieren, indem Sie sie für die Migration als physische Server behandeln. Von der Servermigration wird für die Migration eine Replikationsappliance verwendet.

## <a name="selecting-assessment-and-migration-tools"></a>Auswählen von Bewertungs- und Migrationstools

Wählen Sie im Azure Migrate-Hub das Tool aus, das Sie für die Bewertung oder die Migration verwenden möchten, und fügen Sie es einem Projekt hinzu. Falls Sie ein ISV-Tool oder Movere hinzufügen, gehen Sie wie folgt vor:

- Beziehen Sie zunächst eine Lizenz, oder registrieren Sie sich für eine kostenlose Testversion (gemäß Anweisungen im Tool). Für jeden ISV bzw. jedes Tool ist eine Toollizenzierung angegeben.
- In jedem Tool ist eine Option zum Herstellen einer Verbindung mit Azure Migrate vorhanden. Befolgen Sie zur Verbindungsherstellung die Anweisungen im Tool.
- Verfolgen Sie Ihre Migration über alle Tools im Projekt nach.

## <a name="movere"></a>Movere

Bei Movere handelt es sich um eine SaaS-Plattform (Software-as-a-Service). Sie trägt durch die präzise Darstellung vollständiger IT-Umgebungen innerhalb eines Tages zur Verbesserung der Business Intelligence bei. Organisationen wachsen, verändern sich und werden digital optimiert. Mit Movere können diese Organisationen darauf vertrauen, dass sie dabei über die nötige Transparenz und Kontrolle für ihre Umgebungen verfügen – unabhängig von Plattform, Anwendung oder Geografie.

Movere wurde von Microsoft [übernommen](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) und wird nicht mehr als eigenständiges Angebot verkauft. Movere steht im Rahmen des Microsoft-Programms für Lösungsbewertungen und Wirtschaftlichkeit in der Cloud zur Verfügung. Weitere Informationen zu Movere finden Sie [hier](https://www.movere.io).

Informieren Sie sich auch über Azure Migrate, unseren integrierten Migrationsdienst. Azure Migrate bietet einen zentralen Hub, um die Cloudmigration zu vereinfachen. Der Hub bietet umfassende Unterstützung für Workloads wie physische und virtuelle Server, Datenbanken und Anwendungen. Die End-to-End-Transparenz ermöglicht eine mühelose Nachverfolgung des Status während der Ermittlung, Bewertung und Migration.

Dank der integrierten Azure- und Partner-ISV-Tools bietet Azure Migrate ein breites Spektrum an Features, u. a.:

- Ermittlung virtueller und physischer Server
- Leistungsbasierte Größenanpassung
- Kostenplanung
- Importbasierte Bewertung
- Abhängigkeitsanalyse der Anwendungen ohne Agent

Sollten Sie professionelle Hilfe bei Ihren ersten Schritten benötigen: Microsoft verfügt über kompetente [Azure Expert Managed Service Provider](https://azure.microsoft.com/partners), die Sie auf Ihrem Weg begleiten. Besuchen Sie die [Azure Migrate-Website](https://azure.microsoft.com/services/azure-migrate/).

## <a name="azure-migrate-versions"></a>Azure Migrate-Versionen

Es sind zwei Versionen des Azure Migrate-Diensts verfügbar:

- **Aktuelle Version:** Verwenden Sie diese Version, um Projekte zu erstellen, lokale Server zu ermitteln und Bewertungen und Migrationen zu orchestrieren. [Erfahren Sie mehr](whats-new.md) über die Neuerungen in dieser Version.
- **Vorherige Version**: In der vorherigen Version von Azure Migrate (auch als klassische Version von Azure Migrate bezeichnet) wird nur die Bewertung lokaler Server unterstützt, die unter VMware ausgeführt werden. Die klassische Version von Azure Migrate wird im Februar 2024 eingestellt. Ab Februar 2024 wird die klassische Version von Azure Migrate nicht mehr unterstützt, und die Bestandsmetadaten in klassischen Projekten werden gelöscht. Sie können keine Projekte oder Komponenten aus der vorherigen Version in die neue Version aktualisieren. Sie müssen [ein neues Projekt erstellen](create-manage-projects.md) und ihm [Bewertungs- und Migrationstools](./create-manage-projects.md) hinzufügen. Verwenden Sie die Tutorials, um zu verstehen, wie die verfügbaren Bewertungs- und Migrationstools verwendet werden. Wenn Sie einen Log Analytics-Arbeitsbereich mit einem klassischen Projekt verknüpft haben, können Sie ihn an ein Projekt der aktuellen Version anfügen, nachdem Sie das klassische Projekt gelöscht haben.

    Wenn Sie auf bereits vorhandene Projekte im Azure-Portal zugreifen möchten, suchen Sie nach **Azure Migrate**, und wählen Sie die entsprechende Option aus. Auf dem **Azure Migrate**-Dashboard finden Sie eine Benachrichtigung und einen Link für den Zugriff auf alte Projekte.

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie unsere Tutorials zum Bewerten von [VMware-VMs](./tutorial-discover-vmware.md), [Hyper-V-VMs](./tutorial-discover-hyper-v.md) und [physischen Servern](./tutorial-discover-physical.md) aus.
- Sehen Sie sich die [häufig gestellten Fragen](resources-faq.md) zu Azure Migrate an.
