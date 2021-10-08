---
title: Bereitstellen von Azure Monitor für SAP-Lösungen mit dem Azure-Portal
description: Hier erfahren Sie, wie Sie eine Browsermethode zur Bereitstellung von Azure Monitor für SAP-Lösungen verwenden können.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 85cfe6887ded3844e2143754c31a3c6efee5e132
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579458"
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
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList```

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService```
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate the connection by logging into each instance and running the following commands:

    - For all instances : `sapcontrol -nr <NN> -function GetProcessList`
    - For the ENQUE instance : `sapcontrol -nr <NN> -function EnqGetStatistic`
    - For ABAP instances : `sapcontrol -nr <NN> -function ABAPGetWPTable`
    - For ABAP/J2EE/JEE instances : `sapcontrol -nr <NN> -function GetQueueStatistic`

>[!Important] 
>It is critical that the sapstartsrv service is restarted on each instance of the SAP system for the SAPControl web methods to be unprotected.  These read-only SOAP API are required for the NetWeaver provider to fetch metric data from the SAP System and failure to unprotect these methods will lead to empty or missing visualizations on the NetWeaver metric workbook.
   
>[!Tip]
> Use an access control list (ACL) to filter the access to a server port. For more information, see [this SAP note](https://launchpad.support.sap.com/#/notes/1495075).

To install the NetWeaver provider on the Azure portal:

1. Make sure you've completed the earlier prerequisite steps and that the server has been restarted.
1. On the Azure portal, under **Azure Monitor for SAP Solutions**, select **Add provider**, and then:

   1. For **Type**, select **SAP NetWeaver**.

   1. For **Hostname**, enter the host name of the SAP system.

   1. For **Subdomain**, enter a subdomain if one applies.

   1. For **Instance No**, enter the instance number that corresponds to the host name you entered. 

   1. For **SID**, enter the system ID.
   
   ![Screenshot showing the configuration options for adding a SAP NetWeaver provider.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

>[!Important]
>If the SAP application servers (ie. virtual machines) are part of a network domain, such as one managed by Azure Active Directory, then it is critical that the corresponding subdomain is provided in the Subdomain text box.  The Azure Monitor for SAP collector VM that exists inside the Virtual Network is not joined to the domain and as such will not be able to resolve the hostname of instances inside the SAP system unless the hostname is a fully qualified domain name.  Failure to provide this will result in missing / incomplete visualizations in the NetWeaver workbook.
 
>For example, if the hostname of the SAP system has a fully qualified domain name of "myhost.mycompany.global.corp" then please enter a Hostname of "myhost" and provide a Subdomain of "mycompany.global.corp".  When the NetWeaver provider invokes the GetSystemInstanceList API on the SAP system, SAP returns the hostnames of all instances in the system.  The collector VM will use this list to make additional API calls to fetch metrics specific to each instance's features (e.g.  ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP, etc…). If specified, the collector VM will then use the subdomain  "mycompany.global.corp" to build the fully qualified domain name of each instance in the SAP system.  
 
>Please DO NOT specify an IP Address for the hostname field if the SAP system is a part of network domain.

   
### SAP HANA provider 

1. Select the **Providers** tab to add the providers you want to configure. You can add multiple providers one after another, or add them after you deploy the monitoring resource. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Screenshot showing the tab where you add providers." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Select **Add provider**, and then:

   1. For **Type**, select **SAP HANA**. 

      > [!IMPORTANT]
      > Ensure that a SAP HANA provider is configured for the SAP HANA `master` node.

   1. For **IP address**, enter the private IP address for the HANA server.

   1. For **Database tenant**, enter the name of the tenant you want to use. You can choose any tenant, but we recommend using **SYSTEMDB** because it enables a wider array of monitoring areas. 

   1. For **SQL port**, enter the port number associated with your HANA database. It should be in the format of *[3]* + *[instance#]* + *[13]*. An example is **30013**. 

   1. For **Database username**, enter the username you want to use. Ensure the database user has the *monitoring* and *catalog read* roles assigned.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Screenshot showing configuration options for adding an SAP HANA provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

   
### Microsoft SQL Server provider

1. Before you add the Microsoft SQL Server provider, run the following script in SQL Server Management Studio to create a user with the appropriate permissions for configuring the provider.

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
