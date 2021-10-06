---
title: Anpassen der Größe des Kapazitätspools oder eines Volumes für Azure NetApp Files | Microsoft-Dokumentation
description: Erfahren Sie, wie die Größe eines Kapazitätspools oder eines Volumes geändert wird. Durch eine Größenänderung des Kapazitätspools wird die erworbene Azure NetApp Files-Kapazität geändert.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: fcba2424c457d97a45096af9a35b5f2afa4086ba
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407245"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ändern der Größe eines Kapazitätspools oder Volumes
Sie können die Größe eines Kapazitätspools oder Volumes nach Bedarf ändern, beispielsweise, wenn ein Volume oder ein Kapazitätspool fast vollständig ausgelastet ist. 

Informationen zum Überwachen der Kapazität eines Volumes finden Sie unter [Überwachen der Kapazität eines Volumes](monitor-volume-capacity.md).

## <a name="resize-the-capacity-pool-using-the-azure-portal"></a>Ändern der Größe des Kapazitätspools über das Azure-Portal 

Sie können die Größe des Kapazitätspools in 1-TiB-Schritten erhöhen oder verringern. Allerdings kann die Größe des Kapazitätspools nicht kleiner als 4 TiB sein. Durch eine Größenänderung des Kapazitätspools wird die erworbene Azure NetApp Files-Kapazität geändert.

1. Navigieren Sie in der Ansicht für das NetApp-Konto zu **Kapazitätspools**, und klicken Sie auf den Kapazitätspool, dessen Größe Sie ändern möchten.
2. Klicken Sie mit der rechten Maustaste auf den Namen des Kapazitätspools, oder klicken Sie am Ende der Zeile mit dem Kapazitätspool auf das Symbol „...“, um das Kontextmenü anzuzeigen. Klicken Sie auf **Größe ändern**. 

    ![Screenshot: Kontextmenü des Pools](../media/azure-netapp-files/resize-pool-context-menu.png)  

3. Geben Sie im Fenster „Größe des Pools ändern“ die Poolgröße an.  Klicken Sie auf **OK**.

    ![Screenshot: Fenster „Größe des Pools ändern“](../media/azure-netapp-files/resize-pool-window.png) 

## <a name="resize-a-volume-using-the-azure-portal"></a>Ändern der Größe eines Volumes über das Azure-Portal

Sie können die Größe eines Volumes nach Bedarf anpassen. Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet.

1. Navigieren Sie in der Ansicht für das NetApp-Konto zu **Volumes**, und klicken Sie auf das Volume, dessen Größe Sie ändern möchten.
2. Klicken Sie mit der rechten Maustaste auf den Namen des Volumes, oder klicken Sie am Ende der Zeile mit dem Volume auf das Symbol „...“, um das Kontextmenü anzuzeigen. Klicken Sie auf **Größe ändern**.

    ![Screenshot: Kontextmenü des Volumes](../media/azure-netapp-files/resize-volume-context-menu.png) 
    
3. Geben Sie im Fenster „Volumekontingent aktualisieren“ das Kontingent für das Volume an. Klicken Sie auf **OK**.   

    ![Screenshot: Fenster „Volumekontingent aktualisieren“](../media/azure-netapp-files/resize-volume-quota-window.png) 

## <a name="resizing-the-capacity-pool-or-a-volume-using-azure-cli"></a>Ändern der Größe des Kapazitätspools oder eines Volumes mithilfe der Azure CLI  

Mit den folgenden Befehlen der [Azure-Befehlszeilentools](azure-netapp-files-sdk-cli.md) können Sie die Größe eines Kapazitätspools oder Volumes ändern:

* [`az netappfiles pool`](/cli/azure/netappfiles/pool?preserve-view=true&view=azure-cli-latest)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume?preserve-view=true&view=azure-cli-latest)

## <a name="resizing-the-capacity-pool-or-a-volume-using-rest-api"></a>Ändern der Größe des Kapazitätspools oder eines Volumes mithilfe der REST-API

Sie können eine Automatisierung erstellen, um die Größenänderung für den Kapazitätspool und das Volume zu verarbeiten.   

Entsprechende Informationen finden Sie unter [Entwickeln für Azure NetApp Files mit REST-API](azure-netapp-files-develop-with-rest-api.md) und [Entwickeln für Azure NetApp Files per REST-API mit PowerShell](develop-rest-api-powershell.md). 

Die REST-API-Spezifikation und der Beispielcode für Azure NetApp Files sind im [GitHub-Verzeichnis „resource-manager“](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable) verfügbar. 

## <a name="resize-a-cross-region-replication-destination-volume"></a>Ändern der Größe eines regionsübergreifenden Replikationszielvolumes 

In einer [regionsübergreifenden Replikationsbeziehung](cross-region-replication-introduction.md) wird die Größe eines Zielvolumes basierend auf der Größe des Quellvolumes automatisch angepasst. Daher müssen Sie die Größe des Zielvolumes nicht separat ändern. Dieses Verhalten zur automatischen Größenänderung gilt, wenn sich die Volumes in einer aktiven Replikationsbeziehung befinden oder wenn das Replikationspeering mit dem [Vorgang zur Neusynchronisierung](cross-region-replication-manage-disaster-recovery.md#resync-replication) unterbrochen wird. 

In der folgenden Tabelle wird das Verhalten der Größenänderung des Zielvolumes auf dem [Spiegelungsstatus](cross-region-replication-display-health-status.md) basierend beschrieben:

|  Spiegelungsstatus  | Verhalten der Größenänderung des Zielvolumes |
|-|-|
| *Gespiegelt* | Wenn das Zielvolume initialisiert wurde und bereit ist, Spiegelungsaktualisierungen zu empfangen, wird bei Änderung der Größe des Quellvolumes automatisch die Größe des Zielvolumes geändert. |
| *Beschädigt* | Wenn Sie die Größe des Quellvolumes ändern und der Spiegelungsstatus *beschädigt* ist, wird die Größe des Zielvolumes automatisch mit dem [Vorgang zum erneuten Synchronisieren](cross-region-replication-manage-disaster-recovery.md#resync-replication) angepasst.  |
| *Nicht initialisiert* | Wenn Sie die Größe des Quellvolumes ändern und der Spiegelungsstatus noch *nicht initialisiert* ist, muss die Größenänderung des Zielvolumes manuell erfolgen. Daher sollten Sie auf den Abschluss der Initialisierung warten (d. h., bis zum Spiegelungsstatus *gespiegelt*), um die Größe des Quellvolumes zu ändern. | 

> [!IMPORTANT]
> Vergewissern Sie sich, dass in den Kapazitätspools genügend Toleranzbereich für die Quell- und Zielvolumes der regionsübergreifenden Replikation vorhanden ist. Wenn Sie die Größe des Quellvolumes ändern, wird automatisch auch die Größe des Zielvolumes geändert. Sollte der Kapazitätspool des Zielvolumes jedoch nicht über genügend Toleranzbereich verfügen, tritt ein Fehler für die Größenänderung von Quell- und Zielvolume auf.

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
- [Verwalten eines manuellen QoS-Kapazitätspools](manage-manual-qos-capacity-pool.md)
- [Dynamisches Ändern der Dienstebene eines Volumes](dynamic-change-volume-service-level.md) 
- [Grundlegendes zum Volumekontingent](volume-quota-introduction.md)
- [Überwachen der Kapazität eines Volumes](monitor-volume-capacity.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](azure-netapp-files-faqs.md#capacity-management-faqs)
