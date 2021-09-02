---
title: Ermitteln von SQL Server-Instanzen in einem vorhandenen Azure Migrate-Projekt
description: Erfahren Sie, wie SQL Server-Instanzen in einem vorhandenen Azure Migrate-Projekt ermittelt werden.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 38169324211a22012426b895d66bc6d0015f5587
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346763"
---
# <a name="discover-web-apps-and-sql-server-instances-in-an-existing-project"></a>Ermitteln von Web-Apps und SQL Server-Instanzen in einem vorhandenen Projekt

In diesem Artikel wird beschrieben, wie Sie Web-Apps und SQL Server-Instanzen und -Datenbanken in einem [Azure Migrate](./migrate-services-overview.md)-Projekt ermitteln, das vor der Vorschau der Azure SQL-Bewertungsfunktion und/oder vor der Vorschau der Azure App Service-Bewertungsfunktion erstellt wurde.

Wenn Sie ASP-NET-Web-Apps und SQL Server-Instanzen und -Datenbanken ermitteln, die auf lokalen Computern ausgeführt werden, können Sie einen Migrationspfad zu Azure identifizieren und anpassen. Die Azure Migrate-Appliance führt diese Ermittlung mithilfe der Domänenanmeldeinformationen oder Nicht-Domänenanmeldeinformationen des Windows-Betriebssystems oder der SQL Server-Anmeldeinformationen für die Authentifizierung aus, die Zugriff auf die SQL Server-Instanzen und -Datenbanken haben, die auf den Zielservern ausgeführt werden.
Dieser Ermittlungsvorgang erfolgt ohne Agents, d. h. auf den Zielservern ist nichts installiert.

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie Folgendes sicher:
    - Vor der Ankündigung der SQL- und Web-App-Bewertungsfunktion für Ihre Region wurde ein [Azure Migrate-Projekt](./create-manage-projects.md) erstellt.
    - Das Azure [Migrate-Serverbewertungstool](./how-to-assess.md) wurde einem Projekt hinzugefügt.
- Sie haben die Unterstützung und die Anforderungen für die App-Ermittlung [überprüft](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Stellen Sie sicher, dass auf den Servern, auf denen Sie die App-Ermittlung ausführen, PowerShell Version 2.0 oder höher sowie die VMware Tools (höher als 10.2.0) installiert sind.
- Überprüfen Sie die [Anforderungen](./migrate-appliance.md) für die Bereitstellung der Azure Migrate-Appliance.
- Stellen Sie sicher, dass Sie über die [erforderlichen Rollen](./create-manage-projects.md#verify-permissions) im Abonnement verfügen, um Ressourcen zu erstellen.
- Stellen Sie sicher, dass Ihr Gerät Zugriff auf das Internet hat.

## <a name="enable-discovery-of-aspnet-web-apps-and-sql-server-instances-and-databases"></a>Aktivieren der Ermittlung von ASP.NET-Web-Apps und SQL Server-Instanzen und -Datenbanken

1. Wählen Sie in Ihrem Azure Migrate-Projekt eine der folgenden Optionen aus:
    - Wählen Sie auf der Hub-Kachel **Nicht aktiviert** oder die :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Azure Migrate Hub-Kachel mit nicht aktivierter SQL- und Web-App-Ermittlung"::: aus.
    - Wählen Sie **Nicht aktiviert** für einen beliebigen Eintrag auf der Seite „Serverermittlung“ unter der Spalte „SQL-Instanzen“ oder „Web-Apps“ aus. :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Blatt „Von Azure Migrate ermittelte Server“ mit nicht aktivierter SQL- und Web-Apps-Ermittlung":::.
2. Um ASP.NET-Web-Apps und SQL Server-Instanzen und -Datenbanken zu ermitteln, führen Sie die folgenden Schritte aus:
    - Wählen Sie **Upgrade** aus, um die erforderliche Ressource zu erstellen.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Schaltfläche zum Aktualisieren der Azure Migrate-Appliance":::
    - Bestätigen Sie, dass die auf der Appliance ausgeführten Dienste auf die neuesten Versionen aktualisiert wurden. Starten Sie hierzu den Appliancekonfigurations-Manager auf Ihrem Applianceserver, und wählen Sie im Bereich „Erforderliche Komponenten“ die Option „Appliancedienste anzeigen“ aus.
        - Die Appliance und zugehörige Komponenten werden automatisch aktualisiert. :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Überprüfen Sie die Geräteversion":::.
    - Fügen Sie im Bereich „Anmeldeinformationen und Ermittlungsquellen verwalten“ des Appliancekonfigurations-Managers Domänen- oder SQL Server-Authentifizierungsanmeldeinformationen hinzu, die über Sysadmin-Zugriff auf die SQL Server-Instanz und die zu ermittelnden Datenbanken verfügen.
    - Die Ermittlung von ASP.NET-Web-Apps funktioniert sowohl mit Domänen- als auch mit Nicht-Domänenanmeldeinformationen für das Windows-Betriebssystem, solange das verwendete Konto über lokale Administratorrechte auf Servern verfügt.
    Sie können die automatische Zuordnung von Anmeldeinformationen der Appliance nutzen, wie [hier](./tutorial-discover-vmware.md#start-continuous-discovery) beschrieben.

    Hinweise:
    - Stellen Sie sicher, dass der Softwarebestand bereits aktiviert ist, oder stellen Sie Domänen- oder Nicht-Domänenanmeldeinformationen zur Aktivierung bereit. Zum Ermitteln von SQL Server-Instanzen und ASP.NET-Web-Apps muss der Softwarebestand ausgeführt werden.
    - Die Appliance versucht, die Domänenanmeldeinformationen mit AD zu validieren, wenn sie hinzugefügt werden. Stellen Sie sicher, dass der Applianceserver über eine Netzwerksichtverbindung mit dem AD-Server verfügt, dem die Anmeldeinformationen zugeordnet sind. Nicht-Domänenanmeldeinformationen und Anmeldeinformationen, die SQL Server-Authentifizierung zugeordnet sind, werden nicht überprüft.

3. Nachdem die gewünschten Anmeldeinformationen hinzugefügt wurden, wählen Sie „Ermittlung starten“ aus, um die Überprüfung zu starten.

> [!Note]
>Lassen Sie die Ausführung von Web-Apps und SQL-Ermittlung für einige Zeit zu, bevor Sie Bewertungen für Azure App Service Azure SQL erstellen. Wenn die Ermittlung von Web-Apps und SQL Server-Instanzen und -Datenbanken nicht abgeschlossen werden kann, werden die entsprechenden Instanzen im Bewertungsbericht als **Unbekannt** markiert.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen einer [Azure SQL-Bewertung](./how-to-create-azure-sql-assessment.md)
- Weitere Informationen zur [Azure SQL-Bewertungen](./concepts-azure-sql-assessment-calculation.md)
- Weitere Informationen zum [Erstellen einer Azure App Service-Bewertung](./how-to-create-azure-app-service-assessment.md)
- Weitere Informationen zu [Azure App Service-Bewertungen](./concepts-azure-webapps-assessment-calculation.md)