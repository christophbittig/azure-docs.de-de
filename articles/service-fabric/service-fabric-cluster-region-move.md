---
title: Verschieben eines Azure Service Fabric-Clusters in eine neue Region
description: Hier erfahren Sie, wie Sie einen Azure Service Fabric-Cluster und Anwendungen in eine andere Region verschieben.
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: micraft
ms.openlocfilehash: 3cbd2e21508296b6174a2322559973fb98728e2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083477"
---
# <a name="move-an-azure-service-fabric-cluster-to-a-new-region"></a>Verschieben eines Azure Service Fabric-Clusters in eine neue Region

Service Fabric-Clusterressourcen sind grundsätzlich auf eine Region beschränkt. Das bedeutet, dass Sie für eine Regionsverschiebung zuerst einen neuen Cluster in der Zielregion erstellen, dann vorhandene Workloads migrieren und schließlich den Datenverkehr an diese neue Zielregion leiten. In diesem Dokument werden die Schritte beschrieben, die für diese Migration erforderlich sind. Es gibt mehrere Aspekte, die eine Migration mehr oder weniger komplex machen können. Hierzu gehören die Einrichtung und Konfiguration Ihrer Cluster und Anwendungen, die Art der Kommunikation in Ihrem Cluster und die Frage, ob Ihre Workloads zustandslos, zustandsbehaftet oder eine Mischung aus beidem sind.  


## <a name="steps-to-follow-for-a-region-migration"></a>Schritte für eine Regionsmigration

Vor dem Durchführen einer regionsübergreifenden Migration empfehlen wir die Einrichtung einer Testumgebung und die Ausführung der folgenden Schritte. 

## <a name="create-new-cluster"></a>Neuen Cluster erstellen
1. [Richten Sie einen Cluster in der neuen Region ein](./service-fabric-cluster-creation-via-arm.md#use-your-own-custom-template), indem Sie Ihre vorhandene ARM-Vorlage für Ihre Cluster- und Infrastrukturtopologie umschreiben. Wenn Sie derzeit nicht über eine ARM-Vorlage zur Beschreibung Ihres Clusters verfügen, empfiehlt es sich, die aktuelle ARM-Vorlage aus dem [Azure-Ressourcen-Explorer](https://resources.azure.com/) abzurufen. Der Azure-Ressourcen-Explorer kann Ihnen dabei helfen, Ihre aktuell bereitgestellten Ressourcen und die zugehörigen Konfigurationsinformationen zu entdecken, aus denen Sie ARM-Vorlagen zusammenstellen können, mit denen Sie wiederholt Klone Ihrer vorhandenen Umgebung bereitstellen können. Bevor Sie fortfahren, vergewissern Sie sich in dieser Phase, dass Sie über funktionierende ARM-Vorlagen verfügen, die Klone Ihrer vorhandenen Umgebung bereitstellen können. 

## <a name="move-applications-and-traffic"></a>Verschieben von Anwendungen und Datenverkehr
1. [Stellen Sie vorhandene Anwendungen und Dienste über ARM bereit.](service-fabric-application-arm-resource.md) Stellen Sie sicher, dass sämtliche von Ihnen angepasste Anwendungsparameter und Konfigurationen beibehalten werden. Wenn für Ihre Anwendung beispielsweise der Parameter „count“ auf den Standardwert 5 festgelegt ist, Sie diesen Wert in der Quellumgebung aber auf 7 geändert haben, müssen Sie sicherstellen, dass der Wert für die Anwendungsbereitstellung in der neuen Region auf 7 festgelegt wird. Wenn Sie Ihre Anwendungs- und Dienstinstanzen nicht über ARM verwalten, müssen Sie sich selbst darum kümmern, die in der aktuellen Region ausgeführten Anwendungen und Dienste und deren Konfiguration zu identifizieren und diese Anwendungen und Dienste in der neuen Region bzw. dem neuen Cluster zu duplizieren. 

2. Migrieren Ihrer Dienste  
   -  Zustandsbehaftete Workloads: 
      * <p>Um sicherzustellen, dass Ihre zustandsbehafteten Dienste einen stabilen Zustand erreicht haben, stellen Sie zunächst sicher, dass Sie sämtlichen eingehenden Datenverkehr zu diesen Diensten gestoppt haben. Die Vorgehensweise richtet sich dabei danach, wie der Datenverkehr an Ihre Dienste geleitet wird. Möglicherweise müssen Sie den Dienst von Event Hubs trennen oder einen Dienst wie APIM oder Azure Network Load Balancer daran hindern, Datenverkehr an den Dienst zu leiten, indem Sie die entsprechenden Routing- oder Weiterleitungsregeln entfernen. Sobald kein Datenverkehr mehr eintrifft und die Dienste sämtliche Hintergrundaufgaben in Bezug auf diese Anforderungen abgeschlossen haben, können Sie fortfahren. </p>
      
      * Sichern Sie sämtliche zustandsbehafteten Dienste, indem Sie den [Sicherungs- und Wiederherstellungsdienst](service-fabric-reliable-services-backup-restore.md) verwenden und eine [bedarfsbasierte Sicherung](service-fabric-backup-restore-service-ondemand-backup.md) ausführen. Führen Sie diesen Prozess unbedingt erst dann aus, wenn keinerlei Datenverkehr mehr eintrifft und sämtliche Hintergrundaufgaben abgeschlossen wurden. Nach Abschluss der Sicherung können Sie die Daten in den zustandsbehafteten Diensten der neuen Region und des neuen Clusters [wiederherstellen](service-fabric-backup-restore-service-trigger-restore.md). Das zum Durchführen der Sicherung verwendete Azure-Speicherkonto muss aus der neuen Region erreichbar sein.

   -  Zustandslose Dienste: 
      * <p>Über das Bereitstellen der Dienste im neuen Cluster hinaus sollten keine weiteren Aufgaben auszuführen sein. Dies erfolgt idealerweise im Rahmen der ARM-Anwendungsbereitstellung aus Schritt 2. Sie müssen außerdem sicherstellen, dass die Dienste genauso konfiguriert sind wie im Quellcluster.</p>

   -  Alle Dienste:  
      * <p>Stellen Sie sicher, dass sämtliche Kommunikationsphasen zwischen Clients und den Diensten genauso konfiguriert sind wie im Quellcluster. Diese Überprüfung kann beispielsweise beinhalten, dass sichergestellt ist, dass Zwischenstellen wie Event Hubs, Network Load Balancers, App Gateways oder API Management mit den Regeln eingerichtet sind, die erforderlich sind, damit Datenverkehr zum Cluster fließen kann.</p>  

3. Leiten Sie Datenverkehr von der alten Region an die neue um. Für die Migration empfiehlt sich die Verwendung von [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md), da dieser Dienst eine Reihe von [Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md) bietet. Auf welche Weise Sie Ihre Routingregeln für den Datenverkehr aktualisieren, hängt davon ab, ob Sie die vorhandene Region beibehalten oder außer Betrieb nehmen möchten und wie der Datenverkehr innerhalb Ihrer Anwendung fließt. Möglicherweise müssen Sie herausfinden, ob private bzw. öffentliche IP-Adressen oder DNS-Namen zwischen verschiedenen Azure-Ressourcen in verschiedenen Regionen verschoben werden können. Service Fabric kennt diesen Teil Ihres Systems nicht, daher müssen Sie recherchieren und ggf. die Azure-Teams einbeziehen, die an den entsprechenden Datenverkehrsflüssen beteiligt sind – insbesondere dann, wenn Ihr System komplex oder die Latenz Ihrer Workload von entscheidender Bedeutung ist. Dokumente wie [Konfigurieren einer benutzerdefinierten Domäne](../api-management/configure-custom-domain.md), [Öffentliche IP-Adressen](../virtual-network/ip-services/public-ip-addresses.md) und [DNS-Zonen und -Einträge](../dns/dns-zones-records.md) können dabei hilfreich sein und enthalten Beispiele der Informationen, die Sie je nach Datenverkehrsflüssen und Protokollen benötigen. Im Folgenden finden Sie zwei Beispielszenarien, die zeigen, wie Sie die Aktualisierung des Datenverkehrsroutings angehen könnten:  
   * Sie planen nicht, die vorhandene Quellregion beizubehalten, und haben einen DNS/CNAME-Eintrag, der mit der öffentlichen IP-Adresse eines Network Load Balancers verknüpft ist, der Aufrufe an den ursprünglichen Quellcluster weiterleitet. Aktualisieren Sie den DNS/CNAME-Eintrag, sodass dieser mit einer neuen öffentlichen IP-Adresse des neuen Network Load Balancers in der neuen Region verknüpft ist. Durch diese Übertragung wechseln Clients, die bisher den vorhandenen Cluster verwendet haben, zum neuen Cluster. 
  
   * Sie planen, die vorhandene Quellregion beizubehalten, und haben einen DNS/CNAME-Eintrag, der mit der öffentlichen IP-Adresse eines Network Load Balancers verknüpft ist, der Aufrufe an den ursprünglichen Quellcluster weitergeleitet hat. Richten Sie eine Azure Traffic Manager-Instanz ein, und verknüpfen Sie den DNS-Namen mit dieser Instanz. Die Azure Traffic Manager-Instanz kann so konfiguriert werden, dass sie Datenverkehr an die einzelnen Network Load Balancers in jeder Region weiterleitet. 

4. Wenn Sie planen, beide Regionen beizubehalten, müssen Sie in der Regel eine Art „Rückwärtssynchronisierung“ ausführen, wobei sich die SOT (Source of Truth) in einem Remotespeicher wie SQL, Cosmos DB oder einem Blob- oder Dateispeicher befindet und zwischen den Regionen synchronisiert wird. Wenn dies für Ihre Workload gilt, empfiehlt es sich, zu überprüfen, ob der Datenfluss zwischen den Regionen erwartungsgemäß funktioniert.  

## <a name="final-validation"></a>Finale Validierung
1. Überprüfen Sie zum Schluss, ob die Daten erwartungsgemäß fließen und ob die Dienste in der neuen Region (ggf. auch in der alten Region) erwartungsgemäß funktionieren. 

2. Wenn Sie die alte Region nicht beibehalten möchten, können Sie zu diesem Zeitpunkt die Ressourcen in dieser Region entfernen. Es empfiehlt sich allerdings, vor dem Löschen von Ressourcen einige Zeit zu warten, falls ein Problem auftritt, das einen Rollback zur ursprünglichen Quellregion erfordert.  

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihren Cluster und Ihre Anwendungen in eine neue Region verschoben haben, sollten Sie überprüfen, ob Sicherungen eingerichtet sind, um erforderliche Daten zu schützen.

> [!div class="nextstepaction"]
> [Einrichten von Sicherungen nach einer Migration](service-fabric-backuprestoreservice-quickstart-azurecluster.md)