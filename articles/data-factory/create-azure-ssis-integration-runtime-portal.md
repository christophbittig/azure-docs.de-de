---
title: Erstellen von Azure-SSIS Integration Runtime über das Azure-Portal
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal Azure-SSIS Integration Runtime in Azure Data Factory erstellen, damit Sie SSIS-Pakete in Azure bereitstellen und ausführen können.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 13c5a0d77e82e699c24f13fce3f27d0ddea7fc9e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851171"
---
# <a name="create-an-azure-ssis-integration-runtime-via-azure-portal"></a>Erstellen von Azure-SSIS Integration Runtime über das Azure-Portal 

In diesem Artikel wird erläutert, wie Sie Azure-SSIS Integration Runtime (Azure-SQL Server Integration Services Integration Runtime) in Azure Data Factory (ADF) über das Azure-Portal bzw. die ADF-Benutzeroberfläche erstellen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Befolgen Sie die Schritt-für-Schritt-Anleitung unter [Erstellen einer Data Factory](./quickstart-create-data-factory-portal.md#create-a-data-factory), um Ihre Data Factory-Instanz über das Azure-Portal zu erstellen. Wählen Sie hierbei die Option **An Dashboard anheften** aus, um nach der Erstellung den schnellen Zugriff zu ermöglichen. 

Öffnen Sie nach der Erstellung Ihrer Data Factory-Instanz im Azure-Portal die zugehörige Übersichtsseite. Wählen Sie die Kachel **Erstellen und überwachen** aus, um die zugehörige Seite **Erste Schritte** auf einer separaten Registerkarte zu öffnen. Sie können darauf dann mit der Erstellung Ihrer Azure-SSIS IR fortfahren.   

## <a name="provision-an-azure-ssis-integration-runtime"></a>Bereitstellen einer Azure-SSIS Integration Runtime

Wählen Sie die Kachel **SSIS konfigurieren** aus, um den Bereich **Integration Runtime-Setup** zu öffnen.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Screenshot, der die ADF-Startseite zeigt.":::

   Der Bereich **Integration Runtime-Setup** enthält drei Seiten, auf denen Sie nacheinander allgemeine Einstellungen, Bereitstellungseinstellungen und erweiterte Einstellungen konfigurieren.

### <a name="general-settings-page"></a>Seite „Allgemeine Einstellungen“

Führen Sie auf der Seite **Allgemeine Einstellungen** des Bereichs **Integration Runtime-Setup** die folgenden Schritte aus.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png" alt-text="Allgemeine Einstellungen":::

1. Geben Sie unter **Name** den Namen Ihrer Integration Runtime ein.

2. Geben Sie unter **Beschreibung** die Beschreibung Ihrer Integration Runtime ein.

3. Wählen Sie unter **Standort** den Standort für Ihre Integration Runtime aus. Es werden nur unterstützte Standorte angezeigt. Sie sollten den gleichen Standort wie für den Datenbankserver zum Hosten von SSISDB auswählen.

4. Wählen Sie unter **Knotengröße** die Größe des Knotens in Ihrem Integration Runtime-Cluster aus. Nur unterstützte Knotengrößen werden angezeigt. Wählen Sie eine große Knotengröße (Hochskalieren) aus, wenn Sie zahlreiche compute- oder arbeitsspeicherintensive Pakete ausführen möchten.

   > [!NOTE]
   > Wenn Sie [Computeisolation](../azure-government/azure-secure-isolation-guidance.md#compute-isolation) benötigen, wählen Sie die Knotengröße **Standard_E64i_v3** aus. Diese Knotengröße stellt isolierte virtuelle Computer dar, die ihren gesamten physischen Host nutzen. Sie bietet damit die erforderliche Isolationsstufe, die für bestimmte Workloads erforderlich ist, wie z. B. für Workloads gemäß IL5 (Impact Level 5) des US-Verteidigungsministeriums.
   
5. Wählen Sie unter **Knotenzahl** die Anzahl von Knoten in Ihrer Integration Runtime aus. Nur unterstützte Knotenzahlen werden angezeigt. Wählen Sie einen großen Cluster mit zahlreichen Knoten (Aufskalieren) aus, wenn Sie viele Pakete parallel ausführen möchten.

6. Wählen Sie unter **Edition/Lizenz** die SQL Server-Edition für Ihre Integration Runtime aus: Standard oder Enterprise. Wählen Sie Enterprise aus, wenn Sie erweiterte Funktionen für Ihre Integration Runtime verwenden möchten.

7. Wählen Sie unter **Sparen Sie Geld** die Option „Azure-Hybridvorteil“ für Ihre Integration Runtime aus: **Ja** oder **Nein**. Wählen Sie **Ja**, wenn Sie eine eigene SQL Server-Lizenz mit Software Assurance verwenden möchten, um bei der Hybridnutzung von Kostenersparnissen zu profitieren.

8. Wählen Sie **Weiter**.

### <a name="deployment-settings-page"></a>Seite „Bereitstellungseinstellungen“

Auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) können Sie SSISDB- und/oder Azure-SSIS IR-Paketspeicher erstellen.

#### <a name="creating-ssisdb"></a>Erstellen von SSISDB

Wenn Sie Ihre Pakete in SSISDB bereitstellen möchten (Projektbereitstellungsmodell), aktivieren Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) das Kontrollkästchen **Vom Azure SQL-Datenbank-Server/von der verwalteten Instanz gehosteten SSIS-Katalog (SSISDB) zum Speichern Ihrer Projekte/Pakete/Umgebungen/Ausführungsprotokolle erstellen**. Wenn Sie Ihre Pakete im Dateisystem, in Azure Files oder SQL Server-Datenbank (MSDB) bereitstellen möchten, das bzw. die von Azure SQL Managed Instance gehostet wird (Paketbereitstellungsmodell), müssen Sie weder eine SSISDB erstellen noch das Kontrollkästchen aktivieren.

Aktivieren Sie dieses Kontrollkästchen unabhängig von Ihrem Bereitstellungsmodell, wenn Sie den von Azure SQL Managed Instance gehosteten SQL Server-Agent zum Orchestrieren/Planen Ihrer Paketausführungen verwenden möchten, da dies durch SSISDB aktiviert wird. Weitere Informationen finden Sie unter [Planen der Ausführung von SSIS-Paketen mit einem Agent für die verwaltete Azure SQL-Instanz](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Wenn Sie das Kontrollkästchen aktivieren, führen Sie die folgenden Schritte aus, um Ihren eigenen Datenbankserver zum Hosten der SSISDB bereitzustellen, die wir in Ihrem Namen erstellen und verwalten.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png" alt-text="Bereitstellungseinstellungen für SSISDB":::
   
1. Geben Sie unter **Abonnement** das Azure-Abonnement mit dem Azure-Datenbankserver zum Hosten von SSISDB an. 

1. Wählen Sie als **Standort** den Standort des Datenbankservers zum Hosten von SSISDB aus. Sie sollten denselben Standort wie den für die Integration Runtime auswählen.

1. Wählen Sie unter **Katalogdatenbank-Serverendpunkt** den Endpunkt Ihres Datenbankservers zum Hosten von SSISDB aus. 
   
   Basierend auf dem ausgewählten Datenbankserver kann die SSISDB-Instanz in Ihrem Namen als Einzeldatenbank, als Teil eines Pools für elastische Datenbanken oder auf einer verwalteten Instanz erstellt werden. Der Zugriff kann über ein öffentliches Netzwerk oder durch den Beitritt zu einem virtuellen Netzwerk erfolgen. Einen Leitfaden zum Auswählen des Datenbankservertyps zum Hosten von SSISDB finden Sie unter [Vergleich zwischen SQL-Datenbank und einer verwalteten SQL-Instanz](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

   Wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit privatem Endpunkt zum Hosten der SSISDB auswählen oder Zugriff auf lokale Daten ohne Konfiguration einer selbstgehosteten IR benötigen, müssen Sie Ihre Azure-SSIS IR-Instanz einem virtuellen Netzwerk hinzufügen. Weitere Informationen finden Sie im Artikel zum [Beitritt einer Azure-SSIS Integration Runtime zu einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md).
      
1. Aktivieren Sie entweder das Kontrollkästchen **AAD-Authentifizierung mit der vom System verwalteten Identität für Data Factory** oder das Kontrollkästchen **Verwenden der AAD-Authentifizierung mit einer vom Benutzer zugewiesenen verwalteten Identität für Data Factory**, um die Azure AD-Authentifizierungsmethode für Azure-SSIS IR zu wählen, um auf Ihren Datenbankserver zuzugreifen, der SSISDB hostet. Aktivieren Sie keines der Kontrollkästchen, um stattdessen die SQL Authentifizierungsmethode auszuwählen.

   Wenn Sie eines der Kontrollkästchen aktivieren, müssen Sie das angegebene System/die verwaltete Identität für Ihre Data Factory-Instanz einer Azure AD-Gruppe mit den Zugriffsberechtigungen für Ihren Datenbankserver hinzufügen. Wenn Sie das Kontrollkästchen **Verwenden der AAD-Authentifizierung mit einer vom Benutzer zugewiesenen verwalteten Identität für Data Factory** aktivieren, dann können Sie alle vorhandenen Anmeldeinformationen auswählen, die mit den von Ihnen angegebenen benutzerseitig zugeordneten verwalteten Identitäten erstellt wurden, oder neue erstellen. Weitere Informationen finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md).

1. Geben Sie unter **Administratorbenutzername** den Benutzernamen für die SQL-Authentifizierung für den Datenbankserver an, der die SSISDB hostet. 

1. Geben Sie unter **Administratorbenutzerkennwort** das Kennwort für die SQL-Authentifizierung für den Datenbankserver an, der die SSISDB hostet. 

1. Aktivieren Sie das Kontrollkästchen **Azure-SSIS Integration Runtime-Paar (Dual Standby) mit SSISDB-Failover verwenden**, um ein Dual Standby-Azure SSIS IR-Paar zu konfigurieren, das synchron mit der Failovergruppe für verwaltete Azure SQL-Datenbank-Instanzen für Geschäftskontinuität und Notfallwiederherstellung (BCDR, Business Continuity and Disaster Recovery) funktioniert.
   
   Wenn Sie das Kontrollkästchen aktivieren, geben Sie einen Namen ein, um Ihr Paar der primären und sekundären Azure-SSIS-IRs im Textfeld **Dual Standby-Paarname** zu identifizieren. Beim Erstellen Ihrer primären und sekundären Azure-SSIS-IRs müssen Sie denselben Paarnamen eingeben.

   Weitere Informationen finden Sie unter [Konfigurieren von Azure-SSIS Integration Runtime für Business Continuity & Disaster Recovery (BCDR)](./configure-bcdr-azure-ssis-integration-runtime.md).

1. Wählen Sie unter **Katalogdatenbank-Dienstebene** die Dienstebene für Ihren Datenbankserver zum Hosten der SSISDB aus. Wählen Sie den Tarif „Basic“, „Standard“ oder „Premium“ oder den Namen eines Pools für elastische Datenbanken aus.

Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – die Option **Weiter** aus.

> [!NOTE]
> Wenn Sie den Azure SQL-Datenbankserver zum Hosten von SSISDB verwenden, werden Ihre Daten standardmäßig im georedundanten Speicher für Sicherungen gespeichert. Wenn Sie nicht möchten, dass Ihre Daten in anderen Regionen repliziert werden, befolgen Sie die Anweisungen zum [Konfigurieren der Redundanz für Sicherungsspeicher mithilfe von PowerShell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
#### <a name="creating-azure-ssis-ir-package-stores"></a>Erstellen von Azure-SSIS IR-Paketspeichern

Aktivieren Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) das Kontrollkästchen **Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance** (Paketspeicher zum Verwalten Ihrer Pakete erstellen, die im Dateisystem/in Azure Files/der SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten Azure SQL Managed Instance verwendet wird), wenn Sie Ihre in MSDB, im Dateisystem oder in Azure Files (Paketbereitstellungsmodell) bereitgestellten Pakete mit Azure-SSIS IR-Paketspeichern verwalten möchten.
   
Ein Azure-SSIS IR-Paketspeicher ermöglicht Ihnen das Importieren/Exportieren/Löschen/Ausführen von Paketen und das Überwachen/Beenden der Ausführung von Paketen über SSMS, ähnlich wie im [Legacy-SSIS-Paketspeicher](/sql/integration-services/service/package-management-ssis-service). Weitere Informationen finden Sie unter [Verwalten von SSIS-Paketen mit Azure-SSIS IR-Paketspeichern](./azure-ssis-integration-runtime-package-store.md).
   
Wenn Sie dieses Kontrollkästchen aktivieren, können Sie Ihrer Azure-SSIS IR mehrere Paketspeicher hinzufügen, indem Sie **Neu** auswählen. Umgekehrt kann ein einziger Paketspeicher von mehreren Azure-SSIS IRs gemeinsam genutzt werden.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png" alt-text="Bereitstellungseinstellungen für MSDB/Dateisystem/Azure Files":::

Führen Sie im Bereich **Paketspeicher hinzufügen** die folgenden Schritte aus.
   
   1. Geben Sie unter **Name des Paketspeichers** den Namen Ihres Paketspeichers ein. 

   1. Wählen Sie unter **Mit Paketspeicher verknüpfter Dienst** Ihren vorhandenen verknüpften Dienst zum Speichern der Zugriffsinformationen für das Dateisystem/Azure Files/die verwaltete Azure SQL-Instanz aus, in dem bzw. der Ihre Pakete bereitgestellt werden, oder erstellen Sie einen neuen Dienst, indem Sie **Neu** auswählen. Führen Sie im Bereich **Neuer verknüpfter Dienst** die folgenden Schritte aus.
   
      > [!NOTE]
      > Für den Zugriff auf Azure Files können Sie die verknüpften Dienste **Azure File Storage** oder **Dateisystem** verwenden. Wenn Sie den verknüpften Dienst **Azure File Storage** verwenden, unterstützt der Azure-SSIS IR-Paketspeicher derzeit nur die **Standardauthentifizierungsmethode**. (**Kontoschlüssel** und **SAS-URI** werden nicht unterstützt.)  

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png" alt-text="Bereitstellungseinstellungen für verknüpfte Dienste":::

      1. Geben Sie unter **Name** den Namen Ihres verknüpften Diensts ein. 
         
      1. Geben Sie unter **Beschreibung** die Beschreibung Ihres verknüpften Diensts ein. 
         
      1. Wählen Sie unter **Typ** einen der Typen **Azure File Storage**, **Verwaltete Azure SQL-Instanz** oder **Dateisystem** aus.

      1. Sie können **Verbinden über Integration Runtime** ignorieren, da wir immer Ihre Azure-SSIS IR zum Abrufen der Zugriffsinformationen für Paketspeicher verwenden.

      1. Wählen Sie bei Auswahl von **Azure File Storage** für **Authentifizierungsmethode** die Option **Standard** aus, und führen Sie anschließend die folgenden Schritte aus. 

         1. Wählen Sie unter **Kontoauswahlmethode** eine der Methoden **Aus Azure-Abonnement** oder **Manuell eingeben** aus.
         
         1. Wenn Sie **Aus Azure-Abonnement** auswählen, wählen Sie die entsprechenden Werte für **Azure-Abonnement**, **Speicherkontoname** und **Dateifreigabe** aus.
            
         1. Wenn Sie **Manuell eingeben** auswählen, geben Sie unter **Host** `\\<storage account name>.file.core.windows.net\<file share name>`, unter **Benutzername** `Azure\<storage account name>` und unter **Kennwort** `<storage account key>` ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wenn Sie **Verwaltete Azure SQL-Instanz** auswählen, führen Sie die folgenden Schritte aus. 

         1. Wählen Sie **Verbindungszeichenfolge** oder Ihre **Azure Key Vault**-Instanz aus, in der sie als Geheimnis gespeichert ist.

         1. Wenn Sie **Verbindungszeichenfolge** auswählen, führen Sie die folgenden Schritte aus. 
             1. Wählen Sie bei Auswahl von **Aus Azure-Abonnement** unter **Methode für Kontoauswahl** das entsprechende **Azure-Abonnement**, den **Servernamen**, den **Endpunkttyp** und den **Datenbanknamen** aus. Führen Sie bei Auswahl von **Manuell eingeben** die folgenden Schritte aus. 
                1.  Geben Sie unter **Vollqualifizierter Domänenname** `<server name>.<dns prefix>.database.windows.net` oder `<server name>.public.<dns prefix>.database.windows.net,3342` als privaten bzw. öffentlichen Endpunkt Ihrer verwalteten Azure SQL-Instanz ein. Wenn Sie den privaten Endpunkt eingeben, kann **Verbindung testen** nicht angewendet werden, da dieser Endpunkt über die ADF-Benutzeroberfläche nicht erreicht werden kann.

                1. Geben Sie unter **Datenbankname** `msdb` ein.

            1. Wählen Sie unter **Authentifizierungstyp** einen der folgenden Typen aus: **SQL-Authentifizierung**, **Verwaltete Identität**, **Dienstprinzipal**, oder **Benutzerseitig zugewiesene verwaltete Identität**.

                - Wenn Sie **SQL-Authentifizierung** auswählen, geben Sie die entsprechenden Werte für **Benutzername** und **Kennwort** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

                -  Wenn Sie **Verwaltete Identität** auswählen, gewähren Sie Ihrer verwalteten ADF-Identität den Zugriff auf Ihre verwaltete Azure SQL Managed Instance.

                - Wenn Sie **Dienstprinzipal** auswählen, geben Sie die entsprechenden Werte für **Dienstprinzipal-ID** und **Dienstprinzipalschlüssel** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

                -  Wenn Sie **Benutzerseitig zugewiesene verwaltete Identität** auswählen, gewähren Sie der angegebenen benutzerseitig zugewiesene verwaltete ADF-Identität den Zugriff auf Ihre Azure SQL Managed Instance. Sie können dann alle vorhandenen Anmeldeinformationen auswählen, die mit ihren angegebenen vom Benutzer zugewiesenen verwalteten Identitäten erstellt wurden, oder neue erstellen.

      1. Wenn Sie **Dateisystem** auswählen, geben Sie den UNC-Pfad des Ordners, in dem Ihre Pakete für **Host** bereitgestellt werden, sowie die entsprechenden Werte für **Benutzername** und **Kennwort** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – **Erstellen** aus.

   1. Ihre hinzugefügten Paketspeicher werden auf der Seite **Bereitstellungseinstellungen** angezeigt. Wenn Sie sie entfernen möchten, können Sie die entsprechenden Kontrollkästchen aktivieren und dann **Löschen** auswählen.

Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – die Option **Weiter** aus.

### <a name="advanced-settings-page"></a>Seite „Erweiterte Einstellungen“

Führen Sie auf der Seite **Erweiterte Einstellungen** des Bereichs **Integration Runtime-Setup** die folgenden Schritte aus.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png" alt-text="Erweiterte Einstellungen":::

   1. Legen Sie unter **Maximale Anzahl von parallelen Ausführungen pro Knoten** die maximale Anzahl von Paketen fest, die in Ihrem Integration Runtime-Cluster pro Knoten gleichzeitig ausgeführt werden sollen. Nur unterstützte Paketzahlen werden angezeigt. Wählen Sie eine niedrigere Anzahl aus, wenn Sie mehrere Kerne verwenden möchten, um ein einzelnes großes Paket auszuführen, das compute- oder arbeitsspeicherintensiv ist. Wählen Sie eine hohe Anzahl aus, wenn Sie mindestens ein kleines Paket in einem einzelnen Kern ausführen möchten.

   1. Aktivieren Sie das Kontrollkästchen **Azure-SSIS IR mit zusätzlichen Systemkonfigurationen/Komponenteninstallationen anpassen**, um festzulegen, ob Sie benutzerdefinierte Standard-/Express-Setups für Ihre Azure-SSIS IR-Instanz hinzufügen möchten. Weitere Informationen finden Sie unter [Anpassen des Setups für Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png" alt-text="Erweiterte Einstellungen bei benutzerdefinierten Setups":::
   
      1. Geben Sie für **SAS-URI des Containers für benutzerdefinierte Setups** den SAS-URI des Containers ein, in dem Sie Skripts und zugehörige Dateien für benutzerdefinierte Standard-Setups speichern.

      1. Wählen Sie für **benutzerdefinierte Express-Setups** die Option **Neu** aus, um den Bereich **Benutzerdefiniertes Express-Setup hinzufügen** zu öffnen, und wählen Sie dann im Dropdown Menü **Typ des benutzerdefinierten Express-Setups** einen Typ aus, z. B. **Befehl „cmdkey“ ausführen**, **Umgebungsvariable hinzufügen**, **Lizenzierte Komponente installieren** usw.

         Bei Auswahl des Typs **Lizenzierte Komponente installieren** können Sie im Dropdownmenü **Komponentenname** alle integrierten Komponenten unserer ISV-Partner auswählen und bei Bedarf im Feld **Lizenzschlüssel**/**Lizenzdatei** den erworbenen Produktlizenzschlüssel eingeben/die erworbene Produktlizenzdatei hochladen.
  
         Ihre hinzugefügten benutzerdefinierten Express-Setups werden auf der Seite **Erweiterte Einstellungen** angezeigt. Um diese zu entfernen, können Sie die entsprechenden Kontrollkästchen aktivieren und dann **Löschen** auswählen.

   1. Aktivieren Sie das Kontrollkästchen **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden), um auszuwählen, ob Sie Ihre Azure-SSIS IR-Instanz in ein virtuelles Netzwerk einbinden möchten. 

      Aktivieren Sie diese Option, wenn Sie einen Azure SQL-Datenbank-Server verwenden, der mit einem virtuellen Netzwerkdienstendpunkt, einer IP-Firewallregel, einem privaten Endpunkt oder Azure SQL Managed Instance konfiguriert wurde, und dieser Server zum Hosten von SSISDB einem virtuellen Netzwerk beitritt. Wenn Sie in Ihren SSIS-Paketen über lokale Datenquellen bzw. -ziele verfügen und Zugriff auf lokale Daten benötigen, ohne eine selbstgehostete Integration Runtime zu konfigurieren, sollten Sie diese Option ebenfalls aktivieren. Weitere Informationen finden Sie im Artikel zum [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md). 

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Erweiterte Einstellungen mit einem virtuellen Netzwerk":::

      1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

      1. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

      1. Wählen Sie für **Typ** den Typ Ihres virtuellen Netzwerks aus: **Azure Resource Manager-VNET** oder „Klassisches virtuelles Netzwerk“. Es wird empfohlen, die Option **Azure Resource Manager-VNET** auszuwählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

      1. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Dies sollte derselbe Name sein, der zum Konfigurieren eines VNET-Dienstendpunkts bzw. privaten Endpunkts für Ihren Azure SQL-Datenbank-Server verwendet wird, der SSISDB hostet. Alternativ kann dies der Name sein, der von Ihrer Azure SQL Managed Instance-Instanz verknüpft wurde, die SSISDB hostet. Oder er sollte mit dem Namen des Netzwerks übereinstimmen, das mit Ihrem lokalen Netzwerk verbunden ist. Andernfalls kann es sich um ein beliebiges virtuelles Netzwerk handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

      1. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Dies sollte derselbe Name sein, der zum Konfigurieren eines VNET-Dienstendpunkts für Ihren Azure SQL-Datenbank-Server verwendet wird, der SSISDB hostet. Alternativ sollte es sich um ein anderes als das von Ihrer SQL Managed Instance-Instanz verknüpftes Subnetz handeln, das SSISDB hostet. Andernfalls kann es sich um ein beliebiges Subnetz handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

      1. Wählen Sie für **VNet-Injektionsmethode** die Injektionsmethode Ihrer virtuellen Netzwerks aus: **Express**/**Standard**. 
   
         Informationen zum Vergleich dieser Methoden finden Sie im Artikel [Konfiguration des virtuellen Netzwerks für eine Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-virtual-network-configuration.md#compare). 
   
         Wenn Sie **Express** auswählen, finden Sie weitere Informationen unter [Injektionsmethode „Express“ für virtuelle Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md). 
      
         Wenn Sie **Standard** auswählen, finden Sie weitere Details im Artikel [Injektionsmethode „Standard“ für virtuelle Netzwerke](azure-ssis-integration-runtime-standard-virtual-network-injection.md).  Mit dieser Methode können Sie auch die folgenden Aktionen ausführen:

         1. Aktivieren Sie das Kontrollkästchen **Hiermit werden statische öffentliche IP-Adressen für Ihre Azure-SSIS Integration Runtime bereitgestellt**, um festzulegen, ob Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden möchten, damit Sie sie in der Firewall Ihrer Datenspeicher zulassen können.

            Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

            1. Wählen Sie für **Erste statische öffentliche IP-Adresse** die erste statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip). Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.
      
            1. Wählen Sie für **Zweite statische öffentliche IP-Adresse** die zweite statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip). Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.

   1. Aktivieren Sie das Kontrollkästchen **Selbstgehostete Integration Runtime als Proxy für Ihre Azure-SSIS Integration Runtime-Instanz einrichten**, um festzulegen, ob Sie eine selbstgehostete IR als Proxy für Ihre Azure-SSIS IR-Instanz konfigurieren möchten. Weitere Informationen finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png" alt-text="Erweiterte Einstellungen bei einer selbstgehosteten IR":::

      1. Wählen Sie für die **selbstgehostete Integration Runtime** Ihre vorhandene selbstgehostete IR als Proxy für Azure-SSIS IR aus.

      1. Wählen Sie für den **verknüpften Dienst des Stagingspeichers** den verknüpften Dienst für Ihren vorhandenen Azure-Blobspeicher aus oder erstellen Sie einen neuen Dienst für den Stagingprozess.

      1. Geben Sie für den **Stagingpfad** einen Blobcontainer in Ihrem ausgewählten Azure-Blobspeicherkonto an oder lassen Sie das Feld leer, um einen Standardpfad für den Stagingprozess zu verwenden.

   1. Wählen Sie **VNET-Überprüfung** aus. Wenn die Überprüfung erfolgreich war, wählen Sie **Weiter** aus. 

Überprüfen Sie auf der Seite **Zusammenfassung** alle Einstellungen für Azure-SSIS IR, lesen Sie die empfohlenen Dokumentationslinks, und klicken Sie auf **Fertig stellen**, um die Erstellung Ihrer Integration Runtime zu starten.

> [!NOTE]
> Abgesehen von einer benutzerdefinierten Einrichtungszeit sollte dieser Prozess innerhalb von fünf Minuten abgeschlossen sein. Es kann aber 20 bis 30 Minuten dauern, bis Azure-SSIS IR mit einem virtuellen Netzwerk mit der Injektionsmethode „Standard“ verknüpft ist.
>
> Bei Verwendung der SSISDB stellt der Data Factory-Dienst eine Verbindung mit Ihrem Datenbankserver her, um die SSISDB vorzubereiten. Außerdem konfiguriert er Berechtigungen und Einstellungen für das virtuelle Netzwerk (sofern angegeben) und verknüpft die Azure-SSIS IR mit dem virtuellen Netzwerk.
>
> Wenn Sie Azure-SSIS IR bereitstellen, werden auch die Access Redistributable-Komponente und das Azure Feature Pack für SSIS installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel-Dateien, Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von integrierten Komponenten bereits unterstützt werden. Weitere Informationen zu integrierten/vorinstallierten Komponenten finden Sie unter [Integrierte/vorinstallierte Komponenten für Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Weitere Informationen zu zusätzlichen Komponenten, die Sie installieren können, finden Sie unter [Custom setups for Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) (Benutzerdefinierte Setups for Azure-SSIS IR).

### <a name="connections-pane"></a>Bereich „Verbindungen“

Wechseln Sie im Bereich **Verbindungen** von **Verwaltungshub** zur Seite **Integration Runtimes**, und wählen Sie **Aktualisieren** aus. 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png" alt-text="Bereich „Verbindungen“":::

   Sie können Ihre Azure-SSIS IR bearbeiten/neu konfigurieren, indem Sie deren Namen auswählen. Sie können auch die entsprechenden Schaltflächen zum Überwachen/Starten/Beenden/Löschen Ihrer Azure-SSIS IR, zum automatischen Generieren einer ADF-Pipeline mit der Aktivität „SSIS-Paket ausführen“ zur Ausführung in Ihrer Azure-SSIS IR und zum Anzeigen des JSON-Codes bzw. der Nutzlast Ihrer Azure-SSIS IR auswählen.  Ihre Azure-SSIS IR kann nur bearbeitet/gelöscht werden, wenn sie angehalten wurde.

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS Integration Runtimes im Portal

1. Wechseln Sie auf der Azure Data Factory-Benutzeroberfläche zur Registerkarte **Verwalten** und dann im Bereich **Verbindungen** zur Registerkarte **Integration Runtimes**, um vorhandene Integration Runtimes in Ihrer Data Factory anzuzeigen.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png" alt-text="Anzeigen vorhandener IRs":::

1. Wählen Sie **Neu** aus, um eine neue Azure-SSIS IR zu erstellen, und öffnen Sie den Bereich **Integration Runtime-Setup**.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png" alt-text="Integration Runtime über das Menü":::

1. Wählen Sie im Bereich **Integration Runtime-Setup** die Kachel **Migrieren vorhandener SSIS-Pakete per Lift & Shift für die Ausführung in Azure** und anschließend **Weiter** aus.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png" alt-text="Angeben des Integration Runtime-Typs":::

1. Die übrigen Schritte zum Einrichten einer Azure-SSIS IR finden Sie im Abschnitt [Bereitstellen einer Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime).
 
## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure PowerShell zum Erstellen einer Integration Runtime](create-azure-ssis-integration-runtime-powershell.md)
- [Verwenden einer Azure Resource Manager-Vorlage zum Erstellen einer Integration Runtime](create-azure-ssis-integration-runtime-resource-manager-template.md)
- [Bereitstellen von SSIS-Paketen](create-azure-ssis-integration-runtime-deploy-packages.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
- [Bereitstellen und Ausführen eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Herstellen einer Verbindung mit dem SSIS-Katalog (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Zugreifen auf Datenspeicher und Dateifreigaben mit Windows-Authentifizierung in SSIS-Paketen in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
