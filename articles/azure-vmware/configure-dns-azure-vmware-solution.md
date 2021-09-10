---
title: Konfigurieren der DNS-Weiterleitung für Azure VMware Solution
description: Erfahren Sie, wie Sie die DNS-Weiterleitung für Azure VMware Solution unter Verwendung des Azure-Portals konfigurieren.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/15/2021
ms.openlocfilehash: 4dbf13234f29eb572519f6975e152f22b7093543
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356660"
---
# <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Konfigurieren einer DNS-Weiterleitung im Azure-Portal

>[!IMPORTANT]
>Für private Azure VMware Solution-Clouds, die am oder nach dem 1. Juli 2021 erstellt wurden, können Sie ab sofort die private DNS-Auflösung konfigurieren. Öffnen Sie für private Clouds, die vor dem 1. Juli 2021 erstellt wurden und eine private DNS-Auflösung benötigen, eine [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support), und fordern Sie privates DNS an. 

Standardmäßig können Azure VMware Solution-Verwaltungskomponenten wie vCenter nur Namensdatensätze auflösen, die über öffentliches DNS verfügbar sind. Für bestimmte Hybridanwendungsfälle müssen Azure VMware Solution-Verwaltungskomponenten für eine ordnungsgemäße Funktion jedoch in der Lage sein, Namenseinträge aus privat gehosteten DNS-Systemen aufzulösen, zum Beispiel für kundenseitig verwaltete Systeme wie vCenter und Active Directory.

Privates DNS für Azure VMware Solution-Verwaltungskomponenten ermöglicht es Ihnen, über den NSX-T-DNS-Dienst Regeln für die bedingte Weiterleitung des gewünschten Domänennamens an einen ausgewählten Satz privater DNS-Server zu definieren. 

Diese Funktion verwendet den DNS-Weiterleitungsdienst in NSX-T. Ein DNS-Dienst und eine DNS-Standardzone werden als Teil Ihrer privaten Cloud bereitgestellt. Damit Azure VMware Solution-Verwaltungskomponenten Einträge aus Ihren privaten DNS-Systemen auflösen können, müssen Sie eine FQDN-Zone definieren und sie auf den NSX-T-DNS-Dienst anwenden. Der DNS-Dienst leitet DNS-Abfragen für jede Zone basierend auf den externen DNS-Servern weiter, die in dieser Zone definiert sind.

>[!NOTE]
>Der DNS-Dienst kann mit bis zu fünf FQDN-Zonen verknüpft werden. Jede FQDN-Zone kann bis zu drei DNS-Servern zugeordnet sein.

>[!TIP]
>Falls gewünscht, können Sie die Regeln für die bedingte Weiterleitung auch für Workloadsegmente verwenden, indem Sie die VMs in diesen Segmenten so konfigurieren, dass sie die IP-Adresse des NSX-T-DNS-Diensts als DNS-Server verwenden.


## <a name="architecture"></a>Aufbau

Das Diagramm zeigt, dass der NSX-T-DNS-Dienst DNS-Abfragen an DNS-Systeme weiterleiten kann, die in Azure-Umgebungen und lokalen Umgebungen gehostet werden.

:::image type="content" source="media/networking/dns/dns-forwarder-diagram.png" alt-text="Diagramm: Der NSX-T-DNS-Dienst kann DNS-Abfragen an DNS-Systeme weiterleiten, die in Azure-Umgebungen und lokalen Umgebungen gehostet werden." border="false":::


## <a name="configure-dns-forwarder"></a>Konfigurieren der DNS-Weiterleitung

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Workloadnetzwerk** die Optionen **DNS** > **DNS-Zonen** aus. Wählen Sie anschließend **Hinzufügen**.

   >[!NOTE]
   >Für private Clouds, die am oder nach dem 1. Juli 2021 erstellt wurden, wird bei Erstellung der privaten Cloud die DNS-Standardzone für Sie erstellt.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-1.png" alt-text="Screenshot: Hinzufügen von DNS-Zonen zu einer privaten Azure VMware Solution-Cloud":::

1. Wählen Sie **FQDN-Zone** aus, und geben Sie einen Namen und bis zu drei IP-Adressen des DNS-Servers im Format **10.0.0.53** ein. Klicken Sie anschließend auf **OK**.

   :::image type="content" source="media/networking/dns/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Screenshot: Erforderliche Informationen zum Hinzufügen einer FQDN-Zone":::

   >[!IMPORTANT]
   >Auch wenn NSX-T Leerzeichen und andere nicht alphanumerische Zeichen in einem DNS-Zonennamen zulässt, werden einer Azure-Ressource bestimmte NSX-Ressourcen (beispielsweise DNS-Zonen) zugeordnet, für die verschiedene Zeichen im Namen unzulässig sind. 
   >
   >Deshalb müssen Namen von DNS-Zonen, die andernfalls in NSX-T gültig wären, möglicherweise an die [Benennungskonventionen für Azure-Ressourcen](../azure-resource-manager/management/resource-name-rules.md#microsoftresources) angepasst werden.

      Der Vorgang kann einige Minuten in Anspruch nehmen, und Sie können den Fortschritt über den Abschnitt **Benachrichtigungen** verfolgen. In den Benachrichtigungen wird eine Meldung angezeigt, wenn die DNS-Zone erstellt wurde.

1. Ignorieren Sie die Meldung zur DNS-Standardzone, da diese als Teil Ihrer privaten Cloud für Sie erstellt wird.

1. Wählen Sie die Registerkarte **DNS-Dienst** und anschließend **Bearbeiten** aus.

   >[!TIP]
   >Für private Clouds, die am oder nach dem 1. Juli 2021 erstellt wurden, können Sie die Meldung zur DNS-Standardzone ignorieren, da diese als Teil Ihrer privaten Cloud für Sie erstellt wird.


   >[!IMPORTANT]
   >Während bestimmte Vorgänge in Ihrer privaten Cloud über NSX-T Manager ausgeführt werden können, _müssen_ Sie für private Clouds, die am oder nach dem 1. Juli 2021 erstellt wurden, den DNS-Dienst über die Funktion für den vereinfachten Netzwerkbetrieb im Azure-Portal bearbeiten, wenn Sie Konfigurationsänderungen für das T1-Standardgateway durchführen möchten.  

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-2.png" alt-text="Screenshot: Registerkarte „DNS-Dienst“ mit ausgewählter Schaltfläche „Bearbeiten“":::   

1. Wählen Sie in der Dropdownliste **FQDN-Zonen** den neu erstellten FQDN und dann **OK** aus.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-3.png" alt-text="Screenshot: Ausgewählter FQDN für den DNS-Dienst":::

   Der Vorgang kann einige Minuten in Anspruch nehmen. Sobald der Vorgang abgeschlossen wurde, wird eine Meldung *Abgeschlossen* im Abschnitt **Benachrichtigungen** angezeigt. An diesem Punkt sollten Verwaltungskomponenten in Ihrer privaten Cloud in der Lage sein, DNS-Einträge aus der FQDN-Zone aufzulösen, die im NSX-T-DNS-Dienst angegeben wurde. 

1. Wiederholen Sie die obigen Schritte für weitere FQDN-Zonen, einschließlich aller anwendbaren Reverse-Lookupzonen.


## <a name="verify-name-resolution-operations"></a>Überprüfen der Namensauflösungsvorgänge

Nachdem Sie die DNS-Weiterleitung konfiguriert haben, stehen Ihnen verschiedene Optionen zur Verfügung, um die Namensauflösungsvorgänge zu überprüfen. 

### <a name="nsx-t-manager"></a>NSX-T Manager

NSX-T Manager stellt die Statistiken des DNS-Weiterleitungsdiensts auf globaler Dienstebene und pro Zone zur Verfügung. 

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **DNS** (Netzwerk > DNS) aus, und erweitern Sie dann Ihren DNS-Weiterleitungsdienst.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services.png" alt-text="Screenshot der Registerkarte „DNS Services“ (DNS-Dienste) in NSX-T Manager":::

1. Klicken Sie auf **View Statistics** (Statistik anzeigen), und wählen Sie dann in der Dropdownliste **Zone Statistics** (Zonenstatistik) Ihre FQDN-Zone aus.

   In der oberen Hälfte werden die Statistiken für den gesamten Dienst angezeigt, die untere Hälfte zeigt die Statistiken für die angegebene Zone an. In diesem Beispiel sehen Sie die weitergeleiteten Abfragen an die DNS-Dienste, die während der Konfiguration der FQDN-Zone angegeben wurden.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services-statistics.png" alt-text="Screenshot: Statistik zur DNS-Weiterleitung":::


### <a name="powercli"></a>PowerCLI

Mit der NSX-T-Richtlinien-API können Sie nslookup-Befehle vom NSX-T-DNS-Weiterleitungsdienst aus ausführen. Die erforderlichen Cmdlets sind Bestandteil des `VMware.VimAutomation.Nsxt`-Moduls in PowerCLI. Das folgende Beispiel zeigt die Ausgabe von Version 12.3.0 dieses Moduls.

1. Stellen Sie eine Verbindung mit Ihrem NSX-T-Server her. 

   >[!TIP]
   >Sie können die IP-Adresse Ihres NSX-T-Servers im Azure-Portal unter **Verwalten** > **Identität** abrufen.
 
   ```powershell
   Connect-NsxtServer -Server 10.103.64.3
   ```

1. Fordern Sie einen Proxy für den nslookup-Dienst der DNS-Weiterleitung an.

   ```powershell
   $nslookup = Get-NsxtPolicyService -Name com.vmware.nsx_policy.infra.tier_1s.dns_forwarder.nslookup
   ```

1. Führen Sie über den DNS-Weiterleitungsdienst Lookups durch.

   ```powershell
   $response = $nslookup.get('TNT86-T1', 'vc01.contoso.corp')
   ```

  Der erste Parameter im Befehl ist die ID für das T1-Gateway Ihrer privaten Cloud, die Sie auf der Registerkarte „DNS-Dienst“ im Azure-Portal finden.

1. Rufen Sie anhand der folgenden Antworteigenschaften eine unformatierte Lookupantwort ab.

   ```powershell
   $response.dns_answer_per_enforcement_point.raw_answer; (()) DiG 9.10.3-P4-Ubuntu (()) @10.103.64.192 -b 10.103.64.192 vc01.contoso.corp +timeout=5 +tries=3 +nosearch ; (1 server found) ;; global options: +cmd ;; Got answer: ;; -))HEADER((- opcode: QUERY, status: NOERROR, id: 10684 ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1  ;; OPT PSEUDOSECTION: ; EDNS: version: 0, flags:; udp: 4096 ;; QUESTION SECTION: ;vc01.contoso.corp.  IN A  ;; ANSWER SECTION: vc01.contoso.corp. 3046 IN A 172.21.90.2  ;; Query time: 0 msec ;; SERVER: 10.103.64.192:53(10.103.64.192) ;; WHEN: Thu Jul 01 23:44:36 UTC 2021 ;; MSG SIZE  rcvd: 62
   ```

   Das vorliegende Beispiel enthält eine Antwort für die Abfrage von vc01.contoso.corp, die einen A-Eintrag mit der Adresse 172.21.90.2 zeigt. Außerdem zeigt dieses Beispiel eine zwischengespeicherte Antwort vom DNS-Weiterleitungsdienst, sodass Ihre Ausgabe leicht variieren kann.
