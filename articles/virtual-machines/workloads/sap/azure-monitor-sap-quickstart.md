---
title: Bereitstellen von Azure Monitor für SAP-Lösungen mit dem Azure-Portal
description: Hier erfahren Sie, wie Sie eine Browsermethode zur Bereitstellung von Azure Monitor für SAP-Lösungen verwenden können.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 82dc8aaa14aeae63549a872d887f40d16eebec96
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315329"
---
# <a name="deploy-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Bereitstellen von Azure Monitor für SAP-Lösungen über das Azure-Portal

In diesem Artikel wird beschrieben, wie Azure Monitor für SAP-Lösungen über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal) bereitgestellt wird. Hier erfahren Sie, wie über die browserbasierte Benutzeroberfläche des Portals Azure Monitor für SAP-Lösungen bereitgestellt wird und Anbieter konfiguriert werden.

## <a name="sign-in-to-the-portal"></a>Anmelden beim Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-monitoring-resource"></a>Erstellen einer Überwachungsressource

1. Wählen Sie im **Marketplace** das Angebot **Azure Monitor für SAP-Lösungen** aus.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Screenshot: Auswählen des Angebots „Azure Monitor für SAP-Lösungen“ im Azure Marketplace" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Geben Sie auf der Registerkarte **Grundlagen** die erforderlichen Werte ein. Gegebenenfalls können Sie einen vorhandenen Log Analytics-Arbeitsbereich verwenden.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Screenshot: Konfigurationsoptionen auf der Registerkarte „Grundlagen“" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

   Stellen Sie bei Auswahl eines virtuellen Netzwerks sicher, dass die Systeme, die Sie überwachen möchten, im virtuellen Netzwerk erreichbar sind. 

   > [!IMPORTANT]
   > Wenn Sie unter **Share data with Microsoft support** (Daten für den Microsoft-Support freigeben) die Option **Freigeben** auswählen, können unsere Supportteams zusätzlichen Support bereitstellen.

## <a name="configure-providers"></a>Konfigurieren von Anbietern

### <a name="sap-netweaver-provider"></a>Hinzufügen von SAP NetWeaver als Anbieter

Vom SAP-Startdienst werden viele verschiedene Diensten wie etwa die Überwachung des SAP-Systems bereitgestellt. Wir verwenden „SAPControl“, eine SOAP-Webdienstschnittstelle, die diese Funktionen verfügbar macht. Bei der Webdienstschnittstelle „SAPControl“ wird zwischen [geschützten und ungeschützten](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) Webdienstmethoden unterschieden. 

Damit Sie bestimmte Metriken abrufen können, müssen Sie den Schutz einiger Methoden für die aktuelle Version aufheben. Führen Sie für jedes SAP-System die folgenden Schritte aus:

1. Öffnen Sie eine SAP GUI-Verbindung mit dem SAP-Server.
2. Melden Sie sich über ein Administratorkonto an.
3. Führen Sie die Transaktion RZ10 aus.
4. Wählen Sie das entsprechende Profil (*DEFAULT.PFL*) aus.
5. Wählen Sie **Erweiterte Wartung** > **Ändern** aus. 
6. Wählen Sie den Profilparameter „service/protectedwebmethods“ aus, und ändern Sie ihn so, dass er den folgenden Wert erhält. Klicken Sie anschließend auf „Kopieren“.  

   ```service/protectedwebmethods instruction
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate ( replace the hostname , instance number and method name ) leverage the below powershell script 

```Powershell command to test unprotect method 
$SAPHostName = "<hostname>"
$InstanceNumber = "<instancenumber>"
$Function = "ABAPGetWPTable"
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
$sapcntrluri = "https://&quot; + $SAPHostName + &quot;:5&quot; + $InstanceNumber + &quot;14/?wsdl"
$sapcntrl = New-WebServiceProxy -uri $sapcntrluri -namespace WebServiceProxy -class sapcntrl
$FunctionObject = New-Object ($sapcntrl.GetType().NameSpace + ".$Function")
$sapcntrl.$Function($FunctionObject)
```
11. **Wiederholen Sie die Schritte 3 bis 10 für jedes Instanzprofil**.

>[!Important] 
>Der Dienst „sapstartsrv“ muss auf allen Instanzen des SAP-Systems neu gestartet werden, damit der Schutz für die Webmethode „SAPControl“ jeweils aufgehoben wird.  Diese schreibgeschützten SOAP-APIs sind erforderlich, damit der NetWeaver-Anbieter Metrikdaten aus dem SAP-System abrufen kann. Wenn der Schutz dieser Methoden nicht aufgehoben wird, bleiben Visualisierungen in der NetWeaver-Metrikarbeitsmappe leer oder fehlen ganz.
   
>[!Tip]
> Verwenden Sie eine Zugriffssteuerungsliste (Access Control List, ACL), um den Zugriff auf einen Serverport zu filtern. Weitere Informationen finden Sie in [diesem SAP-Hinweis](https://launchpad.support.sap.com/#/notes/1495075).

So installieren Sie den NetWeaver-Anbieter über das Azure-Portal:

1. Sie müssen die vorherigen Schritte ausgeführt und den Server neu gestartet haben.
1. Wählen Sie im Azure-Portal unter **Azure Monitor für SAP-Lösungen** die Option **Anbieter hinzufügen** aus, und gehen Sie dann wie folgt vor:

   1. Wählen Sie unter **Typ** die Option **SAP NetWeaver** aus.

   1. Geben Sie unter **Hostname** den Hostnamen des SAP-Systems ein.

   1. Geben Sie unter **Unterdomäne** ggf. eine Unterdomäne ein.

   1. Geben Sie unter **Instanznr.** die Instanznummer ein, die dem eingegebenen Hostnamen entspricht. 

   1. Geben Sie unter **SID** die System-ID ein.
   
   ![Screenshot: Konfigurationsoptionen zum Hinzufügen eines SAP NetWeaver-Anbieters.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    Wählen Sie abschließend **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen.

>[!Important]
>Wenn die SAP-Anwendungsserver (d. h. die virtuellen Computer) Teil einer Netzwerkdomäne sind, die beispielsweise mit Azure Active Directory verwaltet wird, muss im Textfeld „Unterdomäne“ die entsprechende Unterdomäne angegeben werden.  Der virtuelle Computer mit Azure Monitor für SAP-Collector im virtuellen Netzwerk ist nicht mit der Domäne verbunden. Daher kann er den Hostnamen der Instanzen im SAP-System nicht auflösen, außer es handelt sich beim Hostnamen um einen vollqualifizierten Domänennamen.  Bei fehlender Angabe bleiben die Visualisierungen in der NetWeaver-Arbeitsmappe unvollständig oder fehlen ganz.
 
>Wenn der Hostname des SAP-Systems beispielsweise dem vollqualifizierten Domänennamen „myhost.mycompany.global.corp“ entspricht, geben Sie den Hostnamen „myhost“ und die Unterdomäne „mycompany.global.corp“ an.  Wenn die GetSystemInstanceList-API vom NetWeaver-Anbieter auf dem SAP-System aufgerufen wird, werden von SAP die Hostnamen aller Instanzen im System zurückgegeben.  Die sich ergebende Liste wird von der Collector-VM für weitere API-Aufrufe verwendet, um Metriken speziell für Features der einzelnen Instanzen abzurufen(z. B. ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP usw.). Wenn angegeben, wird die Unterdomäne „mycompany.global.corp“ von der Collector-VM zum Erstellen des vollqualifizierten Domänennamens der einzelnen Instanzen im SAP-System verwendet.  
 
>Geben Sie im Feld „Hostname“ KEINE IP-Adresse an, wenn das SAP-System Teil einer Netzwerkdomäne ist.

   
### <a name="sap-hana-provider"></a>SAP HANA-Anbieter 

1. Wählen Sie die Registerkarte **Anbieter** aus, um die Anbieter hinzuzufügen, die Sie konfigurieren möchten. Sie können nacheinander mehrere Anbieter hinzufügen oder Anbieter nach der Bereitstellung der Überwachungsressource hinzufügen. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Screenshot: Registerkarte zum Hinzufügen von Anbietern" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Wählen Sie **Anbieter hinzufügen** aus, und gehen Sie dann wie folgt vor:

   1. Wählen Sie unter **Typ** die Option **SAP HANA** aus. 

      > [!IMPORTANT]
      > Stellen Sie sicher, dass für den SAP HANA-Knoten `master` ein SAP HANA-Anbieter konfiguriert ist.

   1. Geben Sie unter **IP-Adresse** die private IP-Adresse für den HANA-Server ein.

   1. Geben Sie unter **Datenbankmandant** den Namen des Mandanten ein, den Sie verwenden möchten. Sie können einen beliebigen Mandanten auswählen, doch wird die Verwendung von **SYSTEMDB** empfohlen, da dieser umfassendere Überwachungsbereiche ermöglicht. 

   1. Geben Sie unter **SQL-Port** die mit Ihrer HANA-Datenbank verknüpfte SQL-Portnummer ein. Sie muss das folgende Format aufweisen: *[3]*  +  *[instance#]*  +  *[13]* . Beispiel: **30013**. 

   1. Geben Sie unter **Datenbankbenutzername** den Benutzernamen ein, den Sie verwenden möchten. Stellen Sie sicher, dass dem Datenbankbenutzer die Rollen für *Überwachung* und *Katalog lesen* zugewiesen sind.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Screenshot: Konfigurationsoptionen zum Hinzufügen eines SAP HANA-Anbieters" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. Wählen Sie abschließend **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen.

   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server-Anbieter

1. Führen Sie vor dem Hinzufügen des Microsoft SQL Server-Anbieters das folgende Skript in SQL Server Management Studio aus, um einen Benutzer mit den erforderlichen Berechtigungen zum Konfigurieren des Anbieters zu erstellen.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ```

1. Wählen Sie **Anbieter hinzufügen** aus, und gehen Sie dann wie folgt vor:

   1. Wählen Sie unter **Typ** die Option **Microsoft SQL Server** aus. 

   1. Füllen Sie die übrigen Felder aus. Verwenden Sie dazu die mit Ihrer SQL Server-Instanz verknüpften Informationen. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Screenshot: Konfigurationsoptionen zum Hinzufügen eines Microsoft SQL Server-Anbieters" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

1. Wählen Sie abschließend **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen.

### <a name="high-availability-cluster-pacemaker-provider"></a>Anbieter für Hochverfügbarkeitscluster (Pacemaker)

Bevor Sie Anbieter für Hochverfügbarkeitscluster (Pacemaker) hinzufügen, installieren Sie zunächst den entsprechenden Agenten für Ihre Umgebung.

Stellen Sie bei **SUSE**-basierten Clustern sicher, dass „ha_cluster_provider“ auf allen Knoten installiert ist. Informieren Sie sich, wie [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter#installation) installiert wird. Unterstützte SUSE-Versionen: SLES für SAP 12 SP3 und höher.  
   
Stellen Sie bei **RHEL**-basierten Cluster sicher, dass PCP (Performance Co-Pilot) und das Unterpaket „pcp-pmda-hacluster“ auf allen Knoten installiert sind. Informieren Sie sich, wie [PCP HACLUSTER-Agent] installiert wird (https://access.redhat.com/articles/6139852). Unterstützte RHEL-Versionen: 8.2, 8.4 und höher.
 
Nach Abschluss der oben beschriebenen Installation erstellen Sie für jeden Clusterknoten einen Anbieter.

1. Wählen Sie **Anbieter hinzufügen** aus, und gehen Sie dann wie folgt vor:

1. Wählen Sie unter **Typ** die Option **Hochverfügbarkeitscluster (Pacemaker)** aus. 
   
1. Konfigurieren Sie die Anbieter für alle Knoten des Clusters, indem Sie die Endpunkt-URL in **HA Cluster Exporter Endpoint** (Endpunkt „ha_cluster_exporter“) eingeben. Geben Sie bei **SUSE**-basierten Clustern **http://\<IP  address\>:9664/metrics** ein. Geben Sie bei **RHEL**-basierten Clustern **http://\<IP address\>:44322/metrics?names=ha_cluster** ein.
 
1. Geben Sie in den entsprechenden Feldern die System-ID, den Hostnamen und den Clusternamen ein.
   
   > [!IMPORTANT]
   > Beim Hostnamen handelt es sich um den eigentlichen Hostnamen im virtuellen Computer. Verwenden sowohl beim SUSE-basierten als auch beim RHEL-basierten Cluster den Befehl „hostname -s“.  

1. Wählen Sie abschließend **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen.

### <a name="os-linux-provider"></a>Betriebssystemanbieter (Linux) 

1. Wählen Sie **Anbieter hinzufügen** aus, und gehen Sie dann wie folgt vor:

   1. Wählen Sie unter **Typ** die Option **Betriebssystem (Linux)** aus. 

      >[!IMPORTANT]
      > Stellen Sie zum Konfigurieren eines Betriebssystemanbieters (Linux) sicher, dass die [aktuelle Version von „node_exporter“](https://prometheus.io/download/#node_exporter) auf jedem Host (BareMetal-Instanz oder virtueller Computer) installiert ist, den Sie überwachen möchten. [Weitere Informationen](https://github.com/prometheus/node_exporter)

   1. Geben Sie unter **Name** einen Namen ein, der als Bezeichner für die BareMetal-Instanz dient.

   1. Geben Sie unter **Endpunkt von Node Exporter** **http://IP:9100/metrics** ein.

      >[!IMPORTANT]
      >Verwenden Sie die private IP-Adresse des Linux-Hosts. Stellen Sie sicher, dass sich der Host und die Azure Monitor für SAP-Ressource im selben virtuellen Netzwerk befinden. 
      >
      >Auf dem Linux-Host muss der Firewallport 9100 geöffnet sein. Wenn Sie `firewall-cmd` verwenden, verwenden Sie die folgenden Befehle: 
      >
      >`firewall-cmd --permanent --add-port=9100/tcp`
      >
      >`firewall-cmd --reload`
      >
      >Wenn Sie `ufw` verwenden, verwenden Sie die folgenden Befehle:
      >
      >`ufw allow 9100/tcp`
      >
      >`ufw reload`
      >
      > Wenn es sich beim Linux-Host um einen virtuellen Azure-Computer handelt, stellen Sie sicher, dass bei allen entsprechenden Netzwerksicherheitsgruppen eingehender Datenverkehr an Port 9100 von `VirtualNetwork` als Quelle zugelassen ist.
 
1. Wählen Sie abschließend  **Anbieter hinzufügen** aus. Fügen Sie nach Bedarf weitere Anbieter hinzu, oder wählen Sie  **Überprüfen und erstellen** aus, um die Bereitstellung abzuschließen. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Monitor für SAP-Lösungen:

> [!div class="nextstepaction"]
> [Überwachen von SAP in Azure](monitor-sap-on-azure.md)
