---
title: Ermitteln von Softwareinventar auf lokalen Servern mit Azure Migrate
description: Hier erfahren Sie, wie Sie das Softwareinventar auf lokalen Servern mit der Ermittlung und Bewertung von Azure Migrate ermitteln.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: c337229a8e9a4fa98e80c955c317813950c51bfb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346290"
---
# <a name="discover-installed-software-inventory-web-apps-and-sql-server-instances-and-databases"></a>Ermitteln von Softwareinventar, Web-Apps sowie SQL Server-Instanzen und -Datenbanken

In diesem Artikel wird beschrieben, wie Sie installiertes Softwareinventar, Web-Apps sowie SQL Server-Instanzen und -Datenbanken mithilfe des Azure Migrate-Tools zur Ermittlung und Bewertung auf Servern ermitteln, die in Ihrer VMware-Umgebung ausgeführt werden.

Mit der Softwareinventur kann ein Migrationspfad zu Azure für Ihre Workloads identifiziert und angepasst werden. Die Softwareinventur verwendet die Azure Migrate-Appliance, um eine Ermittlung mithilfe der Serveranmeldeinformationen durchzuführen. Dieser Vorgang kommt ohne Agents aus. Es werden keine Agents auf den Servern zur Erfassung der Daten installiert.

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie sicher, dass Sie ein [Projekt erstellt](./create-manage-projects.md) und das Azure Migrate-Tool zur Ermittlung und Bewertung hinzugefügt haben.
- Sehen Sie sich die [VMware-Anforderungen](migrate-support-matrix-vmware.md#vmware-requirements) an, um die Softwareinventur durchzuführen.
- Sehen Sie sich die [Appliance-Anforderungen](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) an, bevor Sie die Appliance einrichten.
- Sehen Sie sich die [Anforderungen zur Anwendungsermittlung](migrate-support-matrix-vmware.md#software-inventory-requirements) an, bevor Sie die Softwareinventur auf Servern initiieren.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Bereitstellen und Konfigurieren der Azure Migrate-Appliance

1. [Überprüfen Sie die Anforderungen](migrate-appliance.md#appliance---vmware) für die Bereitstellung der Azure Migrate-Appliance.
2. Überprüfen Sie die Azure-URLs, die die Appliance für den Zugriff in [öffentlichen Clouds](migrate-appliance.md#public-cloud-urls) und [Government-Clouds](migrate-appliance.md#government-cloud-urls) benötigt.
3. [Überprüfen Sie die Daten](migrate-appliance.md#collected-data---vmware), die die Appliance während der Ermittlung und Bewertung sammelt.
4. [Beachten](migrate-support-matrix-vmware.md#port-access-requirements) Sie die Portzugriffsanforderungen für die Appliance.
5. [Stellen Sie die Azure Migrate-Appliance bereit](how-to-set-up-appliance-vmware.md), um die Ermittlung zu starten. Zum Bereitstellen der Appliance laden Sie eine OVA-Vorlage herunter, und importieren Sie sie in VMware, um einen Server zu erstellen, der in Ihrer vCenter Server-Instanz ausgeführt wird. Nach Bereitstellung der Appliance müssen Sie sie beim Projekt registrieren und zum Initiieren der Ermittlung konfigurieren.
6. Beim Konfigurieren der Appliance müssen Sie Folgendes im Appliance-Konfigurations-Manager angeben:
    - Die Details der vCenter-Server-Instanz, zu der eine Verbindung hergestellt werden soll
    - Anmeldeinformationen für vCenter Server, die für die Ermittlung des Servers in Ihrer VMware-Umgebung ausgelegt sind
    - Serveranmeldeinformationen, bei denen es sich um Domänenanmeldeinformationen bzw. Windows-Anmeldeinformationen (ohne Domäne) bzw. Linux-Anmeldeinformationen (ohne Domäne) handeln kann. [Erfahren Sie mehr](add-server-credentials.md) zur Bereitstellung von Anmeldeinformationen und zu deren Handhabung.

## <a name="verify-permissions"></a>Überprüfen von Berechtigungen

- Sie [müssen ein schreibgeschütztes vCenter Server-Konto](./tutorial-discover-vmware.md#prepare-vmware) zur Ermittlung und Bewertung erstellen. Das schreibgeschützte Konto benötigt für **Virtuelle Computer** > **Gastvorgänge** aktivierte Berechtigungen, um mit den Servern zur Durchführung von Softwareinventur interagieren zu können.
- Sie können im Appliance-Konfigurations-Manager mehrere Anmeldeinformationen für Domänen und ohne Domäne (Windows/Linux) für die Anwendungsermittlung hinzufügen. Sie benötigen ein Gastbenutzerkonto für Windows-Server und ein reguläres/normales Benutzerkonto (ohne sudo-Zugriff) für alle Linux-Server. [Erfahren Sie mehr](add-server-credentials.md) über das Bereitstellen von Anmeldeinformationen und deren Handhabung.

### <a name="add-credentials-and-initiate-discovery"></a>Hinzufügen von Anmeldeinformationen und Initiieren der Ermittlung

1. Öffnen Sie den Appliance-Konfigurations-Manager, und schließen Sie die Voraussetzungsüberprüfung sowie die Registrierung der Appliance ab.
2. Navigieren Sie zum Panel **Manage credentials and discovery sources** (Verwalten von Anmeldeinformationen und Ermittlungsquellen).
1.  Klicken Sie in **Schritt 1: Geben Sie die vCenter Server-Anmeldeinformationen ein** auf **Anmeldeinformationen hinzufügen**, um Anmeldeinformationen für das vCenter Server-Konto anzugeben, das die Appliance verwendet, um Server in der vCenter Server-Instanz zu ermitteln.
1. Klicken Sie in **Schritt 2: Geben Sie vCenter Server-Details an** auf **Ermittlungsquelle hinzufügen**, um den Anzeigenamen für die Anmeldeinformationen aus der Dropdownliste auszuwählen, und geben Sie in **IP-Adresse/FQDN** den entsprechenden Wert für die vCenter Server-Instanz an. :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 im Appliance-Konfigurations-Manager für vCenter Server-Details":::
1. Klicken Sie auf **Anmeldeinformationen hinzufügen**, um mehrere Serveranmeldeinformationen anzugeben: **Schritt 3: Geben Sie Anmeldeinformationen für den Server an, um eine Softwareinventur, eine Abhängigkeitsanalyse ohne Agent, eine Ermittlung von SQL Server-Instanzen und -Datenbanken und eine Ermittlung von ASP.NET-Web-Apps in Ihrer VMware-Umgebung durchzuführen**.
1. Klicken Sie auf **Ermittlung starten**, um die vCenter Server-Ermittlung zu starten.

 Nach Abschluss der vCenter Server-Ermittlung initiiert die Appliance die Ermittlung installierter Anwendungen, Rollen und Features (Softwareinventur). Die Dauer hängt von der Anzahl der ermittelten Server ab. Bei 500 Servern dauert es ungefähr ein Stunde, bis der ermittelte Bestand im Azure Migrate-Portal angezeigt wird.

## <a name="review-and-export-the-inventory"></a>Überprüfen und Exportieren des Bestands

Nach Abschluss der Softwareinventur können Sie den Bestand im Azure-Portal überprüfen und exportieren.

1. Klicken Sie auf **Azure Migrate – Server, Datenbanken und Web-Apps** > **Azure Migrate: Ermittlung und Bewertung** und dann auf die angezeigte Anzahl, um die Seite für **Ermittelte Server** zu öffnen.

    > [!NOTE]
    > Zu diesem Zeitpunkt können Sie auch optional die Abhängigkeitsanalyse für die ermittelten Server aktivieren, damit Sie Abhängigkeiten serverübergreifend visualisieren können, wenn Sie eine Bewertung durchführen möchten. [Weitere Informationen](concepts-dependency-visualization.md) zur Abhängigkeitsanalyse.

2. Klicken Sie in der Spalte **Softwareinventar** auf die angezeigte Anzahl, um die ermittelten Anwendungen, Rollen und Features zu überprüfen.
4. Klicken Sie zum Exportieren des Bestands in **Ermittelte Server** auf **Softwarebestand exportieren**.

Das Softwareinventar wird im Excel-Format exportiert und heruntergeladen. Im Arbeitsblatt für das **Softwareinventar** werden alle Anwendungen angezeigt, die auf allen Servern ermittelt wurden.

## <a name="discover-sql-server-instances-and-databases"></a>Ermitteln von SQL Server-Instanzen und -Datenbanken

- Die Softwareinventur identifiziert auch die SQL Server-Instanzen, die in Ihrer VMware-Umgebung ausgeführt werden.
- Wenn Sie keine Anmeldeinformationen für die Windows- oder SQL Server-Authentifizierung für den Appliance-Konfigurations-Manager angegeben haben, fügen Sie die Anmeldeinformationen hinzu, damit sie von der Appliance zum Herstellen einer Verbindung mit den entsprechenden SQL Server-Instanzen verwendet werden können.

    > [!NOTE]
    > Die Appliance kann nur mit den SQL Server-Instanzen verbunden werden, mit denen sie über eine direkte Verbindung verfügt. Für die Softwareinventarisierung selbst ist möglicherweise keine direkte Netzwerkverbindung erforderlich.

Sobald die Verbindung hergestellt wurde, sammelt die Appliance Konfigurations- und Leistungsdaten von SQL Server-Instanzen und -Datenbanken. Die SQL Server-Konfigurationsdaten werden alle 24 Stunden aktualisiert, und die Leistungsdaten werden alle 30 Sekunden aufgezeichnet. Daher kann es bis zu 24 Stunden dauern, bis Änderungen an den Eigenschaften der SQL Server-Instanz und -Datenbanken im Portal angezeigt werden, zum Beispiel der Datenbankstatus, der Kompatibilitätsgrad und weitere Eigenschaften.

## <a name="discover-aspnet-web-apps"></a>Ermitteln von ASP.NET Web-Apps

Bei der Softwareinventarisierung wird die auf ermittelten Servern vorhandene Webserverrolle identifiziert. Wenn für einen Server die Webserverrolle aktiviert ist, führt Azure Migrate die Ermittlung von Web-Apps auf dem Server durch.
Der Benutzer kann sowohl Domänenanmeldeinformationen als auch Nicht-Domänenanmeldeinformationen auf der Appliance hinzufügen. Stellen Sie sicher, dass das verwendete Konto über lokale Administratorrechte auf Quellservern verfügt. Azure Migrate ordnet Anmeldeinformationen automatisch den jeweiligen Servern zu, sodass sie nicht manuell zugeordnet werden müssen. Vor allem werden diese Anmeldeinformationen nie an Microsoft gesendet und verbleiben auf der Appliance, die in der Quellumgebung ausgeführt wird.
Nachdem die Appliance verbunden wurde, sammelt sie Konfigurationsdaten für den IIS-Webserver und ASP.NET-Web-Apps. Konfigurationsdaten von Web-Apps werden alle 24 Stunden aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie eine Bewertung](how-to-create-assessment.md) für ermittelte Server.
- [Bewerten von Web-Apps](how-to-create-azure-app-service-assessment.md) für die Migration zu Azure App Service
