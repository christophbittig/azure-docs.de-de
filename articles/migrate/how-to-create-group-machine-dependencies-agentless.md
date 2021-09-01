---
title: Einrichten der Abhängigkeitsanalyse ohne Agent in Azure Migrate
description: Richten Sie die Abhängigkeitsanalyse ohne Agent in Azure Migrate ein.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 7f4066f466b6dd9e8cb41bf68592aa88c192c44d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346928"
---
# <a name="analyze-server-dependencies-agentless"></a>Analysieren von Serverabhängigkeiten (ohne Agent)

In diesem Artikel wird das Einrichten der Abhängigkeitsanalyse ohne Agent mithilfe des Azure Migrate-Tools zur Ermittlung und Bewertung beschrieben. Mit der [Abhängigkeitsanalyse](concepts-dependency-visualization.md) können Sie Abhängigkeiten zwischen Servern im Hinblick auf die Bewertung und Migration zu Azure besser identifizieren und verstehen.

> [!IMPORTANT]
>Die Abhängigkeitsanalyse ohne Agent ist derzeit nur für Server in der VMware-Umgebung verfügbar, die mithilfe des Tools Azure Migrate: Ermittlung und Bewertung ermittelt wurden.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

- In der Abhängigkeitsanalyseansicht können Sie zurzeit keine Server zu einer Gruppe hinzufügen oder daraus entfernen.
- Eine Abhängigkeitszuordnung für eine Gruppe von Servern ist derzeit nicht verfügbar.
- In einem Azure Migrate-Projekt kann die Sammlung von Abhängigkeitsdaten gleichzeitig für 1.000 Server aktiviert werden. Sie können eine höhere Anzahl von Servern analysieren, indem Sie eine Reihe von Batches mit jeweils 1.000 Servern erstellen.

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie sicher, dass Sie ein [Projekt erstellt](./create-manage-projects.md) und das Azure Migrate-Tool zur Ermittlung und Bewertung hinzugefügt haben.
- Informieren Sie sich über die [VMware-Anforderungen](migrate-support-matrix-vmware.md#vmware-requirements) für die Ausführung von Abhängigkeitsanalysen.
- Sehen Sie sich die [Appliance-Anforderungen](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) an, bevor Sie die Appliance einrichten.
- [Informieren Sie sich über die Anforderungen der Abhängigkeitsanalyse](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless), bevor Sie die Abhängigkeitsanalyse auf Servern aktivieren.

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

- Sie [müssen ein schreibgeschütztes vCenter Server-Konto](./tutorial-discover-vmware.md#prepare-vmware) zur Ermittlung und Bewertung erstellen. Das schreibgeschützte Konto erfordert für **Virtuelle Computer** > **Gastvorgänge** aktivierte Berechtigungen, um mit den Servern zum Sammeln von Abhängigkeitsdaten interagieren zu können.
- Sie benötigen ein Benutzerkonto, damit Azure Migrate zum Sammeln von Abhängigkeitsdaten auf den Server zugreifen kann. [Informieren](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Sie sich über die Kontoanforderungen für Windows- und Linux-Server.

### <a name="add-credentials-and-initiate-discovery"></a>Hinzufügen von Anmeldeinformationen und Initiieren der Ermittlung

1. Öffnen Sie den Appliance-Konfigurations-Manager, und schließen Sie die Voraussetzungsüberprüfung sowie die Registrierung der Appliance ab.
2. Navigieren Sie zum Panel **Manage credentials and discovery sources** (Verwalten von Anmeldeinformationen und Ermittlungsquellen).
1.  Klicken Sie in **Schritt 1: Geben Sie die vCenter Server-Anmeldeinformationen ein** auf **Anmeldeinformationen hinzufügen**, um Anmeldeinformationen für das vCenter Server-Konto anzugeben, das die Appliance verwendet, um Server in der vCenter Server-Instanz zu ermitteln.
1. Klicken Sie in **Schritt 2: Geben Sie vCenter Server-Details an** auf **Ermittlungsquelle hinzufügen**, um den Anzeigenamen für die Anmeldeinformationen aus der Dropdownliste auszuwählen, und geben Sie in **IP-Adresse/FQDN** den entsprechenden Wert für die vCenter Server-Instanz an. :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 im Appliance-Konfigurations-Manager für vCenter Server-Details":::
1. Klicken Sie unter **Schritt 3: Geben Sie Anmeldeinformationen für den Server an, um eine Softwareinventur, eine Abhängigkeitsanalyse ohne Agents und eine Ermittlung von SQL Server-Instanzen und -Datenbanken in Ihrer VMware-Umgebung durchzuführen** auf **Anmeldeinformationen hinzufügen**, um mehrere Serveranmeldeinformationen zum Initiieren der Softwareinventur bereitzustellen.
1. Klicken Sie auf **Ermittlung starten**, um die vCenter Server-Ermittlung zu starten.

 Nach Abschluss der vCenter Server-Ermittlung initiiert die Appliance die Ermittlung installierter Anwendungen, Rollen und Features (Softwareinventur). Während der Softwareinventur werden die hinzugefügten Serveranmeldeinformationen für die Server durchlaufen und für die Abhängigkeitsanalyse ohne Agent überprüft. Sie können die Abhängigkeitsanalyse ohne Agent für Server über das Portal aktivieren. Nur die Server mit erfolgreicher Überprüfung können ausgewählt werden, um die Abhängigkeitsanalyse ohne Agent zu aktivieren.

## <a name="start-dependency-discovery"></a>Starten der Abhängigkeitsermittlung

Wählen Sie die Server aus, auf denen Sie die Abhängigkeitsermittlung aktivieren möchten.

1. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Ermittelte Server**.
2. Wählen Sie in **Appliancename** die Appliance aus, deren Ermittlung Sie überprüfen möchten.
1. Sie können unter **Abhängigkeiten (ohne Agents)** den Überprüfungsstatus der Server anzeigen.
1. Klicken Sie auf die Dropdownliste **Abhängigkeitsanalyse**.
1. Klicken Sie auf **Server hinzufügen**.
1. Wählen Sie auf der Seite **Server hinzufügen** die Server aus, auf denen Sie die Abhängigkeitsanalyse aktivieren möchten. Sie können die Abhängigkeitszuordnung nur auf Servern aktivieren, auf denen die Überprüfung erfolgreich war. Der nächste Überprüfungszyklus wird 24 Stunden nach dem letzten Überprüfungszeitstempel ausgeführt.
1. Nachdem Sie die Server ausgewählt haben, klicken Sie auf **Server hinzufügen**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Starten der Abhängigkeitsanalyse":::

Ungefähr sechs Stunden nach dem Aktivieren der Abhängigkeitsanalyse auf den Servern können Sie die Abhängigkeiten visualisieren. Wenn Sie die Abhängigkeitsanalyse auf mehreren Servern gleichzeitig aktivieren möchten, können Sie hierfür [PowerShell](#start-or-stop-dependency-analysis-using-powershell) verwenden.

## <a name="visualize-dependencies"></a>Visualisieren von Abhängigkeiten

1. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Ermittelte Server**.
1. Wählen Sie in **Appliancename** die Appliance aus, deren Ermittlung Sie überprüfen möchten.
1. Suchen Sie nach dem Server, dessen Abhängigkeiten Sie überprüfen möchten.
1. Klicken Sie in der Spalte **Abhängigkeiten (ohne Agents)** auf **Abhängigkeiten anzeigen**.
1. Ändern Sie den Zeitraum, für den Sie die Zuordnung anzeigen möchten, über die Dropdown-Liste **Zeitdauer**.
1. Erweitern Sie die Gruppe **Client**, um die Server aufzulisten, die eine Abhängigkeit vom ausgewählten Server aufweisen.
1. Erweitern Sie die Gruppe **Port**, um die Server aufzulisten, die eine Abhängigkeit vom ausgewählten Server aufweisen.
1. Um zur Kartenansicht eines abhängigen Servers zu navigieren, klicken Sie auf den Servernamen > **Serverübersicht laden**.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="Erweitern der Serverportgruppe und Laden der Serverübersicht":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Erweitern der Clientgruppe":::

8. Erweitern Sie den ausgewählten Server, um Details auf Prozessebene für die einzelnen Abhängigkeiten anzuzeigen.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Erweitern des Servers zum Anzeigen von Prozessen":::

> [!NOTE]
> Prozessinformationen für eine Abhängigkeit sind nicht immer verfügbar. Wenn keine Informationen verfügbar sind, wird die Abhängigkeit mit „Unbekannter Prozess“ als Prozess dargestellt.

## <a name="export-dependency-data"></a>Exportieren der Abhängigkeitsdaten

1. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Ermittelte Server**.
2. Klicken Sie auf die Dropdownliste **Abhängigkeitsanalyse**.
3. Klicken Sie auf **Anwendungsabhängigkeiten exportieren**.
4. Wählen Sie auf der Seite **Anwendungsabhängigkeiten exportieren** den Namen der Appliance aus, die die gewünschten Server ermittelt.
5. Wählen Sie die Start- und Endzeit aus. Beachten Sie, dass Sie nur Daten für die letzten 30 Tage herunterladen können.
6. Klicken Sie auf **Abhängigkeit exportieren**.

Die Abhängigkeitsdaten werden im CSV-Format exportiert und heruntergeladen. Die heruntergeladene Datei enthält die Abhängigkeitsdaten für alle Server, auf denen die Abhängigkeitsanalyse aktiviert war. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Exportieren von Abhängigkeiten":::

### <a name="dependency-information"></a>Abhängigkeitsinformationen

Jede Zeile in der exportierten CSV-Datei entspricht einer Abhängigkeit, die im angegebenen Zeitfenster beobachtet wurde.

In der folgenden Tabelle sind die Felder der exportierten CSV-Datei zusammengefasst. Beachten Sie, dass die Felder für den Servernamen, die Anwendung und den Prozess nur für Server aufgefüllt werden, auf denen die Abhängigkeitsanalyse ohne Agent aktiviert ist.

**Feldname** | **Details**
--- | --- 
Zeitfenster | Das Zeitfenster, in dem die Abhängigkeit beobachtet wurde. <br/> Abhängigkeitsdaten werden derzeit in Zeitfenstern von 6 Stunden erfasst.
Name des Quellservers | Der Name des Quellservers 
Quellanwendung | Der Name der Anwendung auf dem Quellserver  
Quellprozess | Der Name des Prozesses auf dem Quellserver  
Name des Zielservers | Der Name des Zielservers
Ziel-IP | Die IP-Adresse des Zielservers
Zielanwendung | Der Name der Anwendung auf dem Zielserver
Zielprozess | Der Name des Prozesses auf dem Zielserver  
Zielport | Die Portnummer auf dem Zielserver

## <a name="stop-dependency-discovery"></a>Beenden der Abhängigkeitsermittlung

Wählen Sie die Server aus, auf denen Sie die Abhängigkeitsermittlung beenden möchten.

1. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Ermittelte Server**.
1. Wählen Sie in **Appliancename** die Appliance aus, deren Ermittlung Sie überprüfen möchten.
1. Klicken Sie auf die Dropdownliste **Abhängigkeitsanalyse**.
1. Klicken Sie auf **Server entfernen**.
1. Wählen Sie auf der Seite **Server entfernen** den Server aus, für den Sie die Abhängigkeitsanalyse beenden möchten.
1. Nachdem Sie die Server ausgewählt haben, klicken Sie auf **Server entfernen**.

Wenn Sie die Abhängigkeitsanalyse auf mehreren Servern gleichzeitig beenden möchten, können Sie hierfür [PowerShell](#start-or-stop-dependency-analysis-using-powershell) verwenden.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Starten oder Beenden der Abhängigkeitsanalyse mit PowerShell

Laden Sie das PowerShell-Modul aus dem GitHub-Repository mit den [Azure PowerShell-Beispielen](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) herunter.

### <a name="log-in-to-azure"></a>Anmelden an Azure

1. Melden Sie sich mit dem Cmdlet „Connect-AzAccount“ bei Ihrem Azure-Abonnement an.

    ```PowerShell
    Connect-AzAccount
    ```
    Verwenden Sie den folgenden Befehl, falls Sie Azure Government nutzen.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Wählen Sie das Abonnement aus, unter dem Sie das Projekt erstellt haben. 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importieren Sie das heruntergeladene PowerShell-Modul „AzMig_Dependencies“.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Aktivieren oder Deaktivieren der Sammlung von Abhängigkeitsdaten

1. Verwenden Sie die folgenden Befehle, um die Liste mit den ermittelten Servern in Ihrem Projekt abzurufen. Im Beispiel unten lautet der Projektname „FabrikamDemoProject“ und die zugehörige Ressourcengruppe „FabrikamDemoRG“. Die Liste mit den Servern wird in der Datei „FabrikamDemo_VMs.csv“ gespeichert.

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    Der Datei können Sie die Anzeigenamen der Server, den aktuellen Status der Abhängigkeitssammlung und die ARM-ID aller ermittelten Server entnehmen. 

2. Erstellen Sie eine CSV-Eingabedatei, um Abhängigkeiten zu aktivieren oder zu deaktivieren. Die Datei muss eine Spalte mit der Überschrift „ARM ID“ enthalten. Alle zusätzlichen Überschriften in der CSV-Datei werden ignoriert. Sie können die CSV-Datei erstellen, indem Sie die im vorherigen Schritt generierte Datei verwenden. Erstellen Sie eine Kopie der Datei, und behalten Sie die Server bei, für die Sie Abhängigkeiten aktivieren oder deaktivieren möchten. 

    Im folgenden Beispiel wird die Abhängigkeitsanalyse in der Liste mit den Servern in der Eingabedatei „FabrikamDemo_VMs_Enable.csv“ aktiviert.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    Im folgenden Beispiel wird die Abhängigkeitsanalyse in der Liste mit den Servern in der Eingabedatei „FabrikamDemo_VMs_Disable.csv“ deaktiviert.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualisieren von Netzwerkverbindungen in Power BI

Azure Migrate verfügt über eine Power BI-Vorlage, die Sie zum gleichzeitigen Visualisieren der Netzwerkverbindungen vieler Server und zum Filtern nach Prozess und Server verwenden können. Laden Sie Power BI zum Visualisieren mit Abhängigkeitsdaten, indem Sie die unten angegebene Anleitung verwenden.

1. Laden Sie das PowerShell-Modul und die Power BI-Vorlage aus dem GitHub-Repository mit den [Azure PowerShell-Beispielen](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) herunter.

2. Melden Sie sich bei Azure an, indem Sie die folgende Anleitung befolgen: 
    - Melden Sie sich mit dem Cmdlet „Connect-AzAccount“ bei Ihrem Azure-Abonnement an.

        ```PowerShell
        Connect-AzAccount
        ```

    - Verwenden Sie den folgenden Befehl, falls Sie Azure Government nutzen.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Wählen Sie das Abonnement aus, unter dem Sie das Projekt erstellt haben.

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importieren Sie das heruntergeladene PowerShell-Modul „AzMig_Dependencies“.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Führen Sie den folgenden Befehl aus. Mit diesem Befehl werden die Abhängigkeitsdaten in eine CSV-Datei heruntergeladen und verarbeitet, um eine Liste mit eindeutigen Abhängigkeiten zu generieren, die für die Visualisierung in Power BI genutzt werden können. Im Beispiel unten lautet der Projektname „FabrikamDemoProject“ und die zugehörige Ressourcengruppe „FabrikamDemoRG“. Die Abhängigkeiten werden für Server heruntergeladen, die über FabrikamAppliance ermittelt werden. Die eindeutigen Abhängigkeiten werden in der Datei „FabrikamDemo_Dependencies.csv“ gespeichert.

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Öffnen Sie die heruntergeladene Power BI-Vorlage.

6. Laden Sie die heruntergeladenen Abhängigkeitsdaten in Power BI.
    - Öffnen Sie die Vorlage in Power BI.
    - Klicken Sie in der Symbolleiste auf **Daten abrufen**. 
    - Wählen Sie in der Liste mit den häufig verwendeten Datenquellen die Option **Text/CSV** aus.
    - Wählen Sie die heruntergeladene Datei mit den Abhängigkeiten aus.
    - Klicken Sie auf **Laden**.
    - Sie sehen, dass eine Tabelle mit dem Namen der CSV-Datei importiert wird. Die Tabelle wird rechts in der Felderleiste angezeigt. Benennen Sie sie in „AzMig_Dependencies“ um.
    - Klicken Sie in der Symbolleiste auf „Aktualisieren“.

    Die importierten Daten sollten nun im Diagramm mit den Netzwerkverbindungen und unter den Slicern für den Namen des Quellservers, Zielservers, Quellprozesses und Zielprozesses widergespiegelt werden.

7. Visualisieren Sie die Karte mit den Netzwerkverbindungen, die nach Servern und Prozessen gefiltert wurde. Speichern Sie die Datei.

## <a name="next-steps"></a>Nächste Schritte

[Gruppieren von Servern](how-to-create-a-group.md) für die Bewertung.