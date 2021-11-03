---
title: 'Azure-Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals'
description: In diesem Schnellstart erfahren Sie, wie Sie einen Azure Event Hub im Azure-Portal erstellen.
ms.topic: quickstart
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b08ea54d7f408f35c9a584d9608848638dead827
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081332"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals
Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der Millionen von Ereignissen pro Sekunde empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In dieser Schnellstartanleitung erstellen Sie einen Event Hub mit dem [Azure-Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

- Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist eine logische Sammlung mit Azure-Ressourcen. Alle Ressourcen werden in einer Ressourcengruppe bereitgestellt und verwaltet. So erstellen Sie eine Ressourcengruppe:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich die Option **Ressourcengruppen** aus. Wählen Sie anschließend **Hinzufügen**.

   ![Ressourcengruppen: Schaltfläche „Hinzufügen“](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. Wählen Sie unter **Abonnement** den Namen des Azure-Abonnements aus, in dem Sie die Ressourcengruppe erstellen möchten.
1. Geben Sie einen eindeutigen **Namen für die Ressourcengruppe** ein. Das System überprüft sofort, ob der Name im derzeit ausgewählten Azure-Abonnement verfügbar ist.
1. Wählen Sie eine **Region** für die Ressourcengruppe aus.
1. Klicken Sie auf **Überprüfen + erstellen**.

   ![Ressourcengruppe: erstellen](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. Wählen Sie auf der Seite **Bewerten + erstellen** die Option **Erstellen**. 

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace

Ein Event Hubs-Namespace bietet einen Container mit einem eindeutigen Bereich, in dem Sie einen oder mehrere Event Hubs erstellen können. Führen Sie die folgenden Schritte aus, um mit dem Portal einen Namespace in Ihrer Ressourcengruppe zu erstellen:

1. Wählen Sie im Azure-Portal oben links **Ressource erstellen** aus.
1. Wählen Sie im linken Menü **Alle Dienste** aus. Wählen Sie dann in der Kategorie **Analyse** neben **Event Hubs** den **Stern (`*`)** aus. Überprüfen Sie, ob **Event Hubs** im linken Navigationsmenü zu **FAVORITEN** hinzugefügt wurde. 
    
   ![Suchen nach Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. Wählen Sie im linken Navigationsmenü unter **FAVORITEN** die Option **Event Hubs**. Wählen Sie anschließend auf der Symbolleiste die Option **Hinzufügen** aus.

   ![Schaltfläche „Hinzufügen“](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. Gehen Sie auf der Seite **Namespace erstellen** wie folgt vor:  
   1. Wählen Sie das **Abonnement** aus, in dem Sie den Namespace erstellen möchten.  
   1. Wählen Sie die **Ressourcengruppe** aus, die Sie im vorherigen Schritt erstellt haben.   
   1. Geben Sie einen **Namen** für den Namespace ein. Das System überprüft sofort, ob dieser Name verfügbar ist.  
   1. Wählen Sie einen **Standort** für den Namespace aus.
   1. Wählen Sie **Basic** für den **Tarif** aus. Informationen zu den Unterschieden zwischen den Tarifen finden Sie in den Artikeln [Kontingente und Grenzwerte](event-hubs-quotas.md), [Event Hubs Premium](event-hubs-premium-overview.md) und [Event Hubs Dedicated](event-hubs-dedicated-overview.md). 
   1. Lassen Sie die Einstellungen für **Durchsatzeinheiten** (für den Standard-Tarif) oder **Verarbeitungseinheiten** (für den Premium-Tarif) unverändert. Weitere Informationen zu Durchsatzeinheiten oder Verarbeitungseinheiten finden Sie unter [Skalierung mit Event Hubs](event-hubs-scalability.md).  
   1. Wählen Sie am unteren Rand der Seite die Option **Bewerten + erstellen** aus.
      
      ![Erstellen eines Event Hub-Namespace](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. Überprüfen Sie die Einstellungen auf der Seite **Bewerten + erstellen**, und wählen Sie **Erstellen** aus. Warten Sie, bis die Bereitstellung abgeschlossen ist. 
      
      ![Seite „Bewerten und erstellen“](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. Wählen Sie auf der Seite **Bereitstellung** die Option **Zu Ressource wechseln** aus, um zur Seite für Ihren Namespace zu navigieren. 
      
      ![Bereitstellung abgeschlossen: Zu Ressource wechseln](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Vergewissern Sie sich, dass die Seite **Event Hubs-Namespace** in etwa aussieht wie das folgende Beispiel:   
      
      ![Startseite für den Namespace](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Von Azure Event Hubs wird ein Kafka-Endpunkt bereitgestellt. Dieser Endpunkt ermöglicht Ihrem Event Hubs-Namespace die native Erkennung des [Apache Kafka](https://kafka.apache.org/intro)-Nachrichtenprotokolls und der APIs. Mit dieser Funktion können Sie wie bei Kafka-Themen mit Ihrem Event Hubs kommunizieren, ohne Ihre Protokollclients ändern oder eigene Cluster ausführen zu müssen. Event Hubs unterstützt [Apache Kafka Version 1.0.](https://kafka.apache.org/10/documentation.html) und höher. Weitere Informationen finden Sie unter [Verwenden von Azure Event Hubs aus Apache Kafka-Anwendungen](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Führen Sie die folgenden Schritte aus, um im Namespace einen Event Hub zu erstellen:

1. Wählen Sie auf der Seite „Event Hubs-Namespace“ im linken Menü **Event Hubs** aus.
1. Wählen Sie oben im Fenster die Option **+ Event Hub** aus.
   
    ![Event Hub hinzufügen: Schaltfläche](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Geben Sie einen Namen für den Event Hub ein, und wählen Sie **Erstellen** aus.
   
    ![Event Hub erstellen](./media/event-hubs-quickstart-portal/create-event-hub5.png)

    Über die Einstellung **Partitionsanzahl** können Sie den Verbrauch vieler Consumer parallelisieren. Weitere Informationen finden Sie unter [Partitionen](event-hubs-scalability.md#partitions).

    Die Einstellung **Nachrichtenaufbewahrung** gibt an, wie lange der Event Hubs-Dienst Daten speichert. Weitere Informationen finden Sie unter [Ereignisaufbewahrung](event-hubs-features.md#event-retention).
1. Sie können den Status der Event Hub-Erstellung in den Warnungen überprüfen. Nachdem der Event Hub erstellt wurde, wird er in der Liste der Event Hubs angezeigt.

    ![Erstellter Event Hub](./media/event-hubs-quickstart-portal/event-hub-created.png)
    
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Ressourcengruppe, einen Event Hubs-Namespace und einen Event Hub erstellt. Schritt-für-Schritt-Anleitungen zum Senden von Ereignissen an einen oder Empfangen von Ereignissen von einem Event Hub finden Sie in den folgenden Tutorials: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (nur senden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (nur empfangen)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
