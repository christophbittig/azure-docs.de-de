---
title: Überwachen der Kapazität eines Azure NetApp Files-Volumes | Microsoft-Dokumentation
description: Beschreibt Möglichkeiten zum Überwachen der Kapazitätsauslastung eines Azure NetApp Files-Volumes.
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
ms.openlocfilehash: 8f2dfe5d185b7de08dca189a83d856ac04d5404e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224114"
---
# <a name="monitor-the-capacity-of-a-volume"></a>Überwachen der Kapazität eines Volumes  

In diesem Artikel werden Möglichkeiten zum Überwachen der Kapazitätsauslastung eines Azure NetApp Files-Volumes beschrieben.  

## <a name="using-windows-or-linux-clients"></a>Verwenden von Windows- oder Linux-Clients

In diesem Abschnitt wird gezeigt, wie Sie einen Windows- oder Linux-Client zum Überwachen der Volumekapazität verwenden. In den in diesem Abschnitt beschriebenen Szenarien wird davon ausgegangen, dass ein Volume mit einer Größe von 1 TiB (Kontingent) in einem Kapazitätspool auf Dienstebene mit 4 TiB Ultra konfiguriert ist. 

### <a name="windows-smb-clients"></a>Windows (SMB)-Clients

Sie können Windows-Clients verwenden, um die verwendete und verfügbare Kapazität eines Volumes über die dem Netzwerk zugeordneten Laufwerkseigenschaften zu überprüfen. Dazu stehen die beiden folgenden Methoden zur Auswahl: 

* Wechseln Sie zum Datei-Explorer, klicken Sie mit der rechten Maustaste auf das zugeordnete Laufwerk, und wählen Sie **Eigenschaften** aus, um die Kapazität anzuzeigen.  

    [ ![Screenshot: Laufwerks- und Volumeeigenschaften im Datei-Explorer.](../media/azure-netapp-files/monitor-explorer-drive-properties.png) ](../media/azure-netapp-files/monitor-explorer-drive-properties.png#lightbox)

* Verwenden Sie an der Eingabeaufforderung den Befehl `dir`: 

    ![Screenshot: Verwendung des dir-Befehls zum Anzeigen der Kapazität.](../media/azure-netapp-files/monitor-volume-properties-dir-command.png) 

Der im Datei-Explorer oder mit dem Befehl `dir` angezeigte *verfügbare Speicherplatz* ist genau. Der *verbrauchte/belegte Speicherplatz* ist jedoch eine Schätzung, wenn Momentaufnahmen auf dem Volume generiert werden. Die [verbrauchte Momentaufnahmekapazität](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)  wird auf den insgesamt verbrauchten Speicherplatz auf dem Volume angerechnet. Zum Abrufen des absoluten Volumeverbrauchs, einschließlich der von Momentaufnahmen verwendeten Kapazität, verwenden Sie die [Azure NetApp-Metriken](azure-netapp-files-metrics.md#volumes) im Azure-Portal. 

### <a name="linux-nfs-clients"></a>Linux (NFS)-Clients 

Linux-Clients können die verwendete und verfügbare Kapazität eines Volumes mithilfe des [df-Befehls](https://linux.die.net/man/1/df) überprüfen.  

Die Option `-h` zeigt die Größe einschließlich des belegten und verfügbaren Speicherplatzes in einem lesbaren Format an (unter Verwendung der Einheitengrößen M, G und T).

Die folgende Momentaufnahme zeigt den Volumekapazitätsbericht in Linux:

![Screenshot: Volumekapazitätsbericht in Linux.](../media/azure-netapp-files/monitor-volume-properties-linux-command.png) 

Der mit dem Befehl `df` angezeigte *verfügbare Speicherplatz* ist genau. Der *verbrauchte/belegte Speicherplatz* ist jedoch eine Schätzung, wenn Momentaufnahmen auf dem Volume generiert werden. Die [verbrauchte Momentaufnahmekapazität](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)  wird auf den insgesamt verbrauchten Speicherplatz auf dem Volume angerechnet. Zum Abrufen des absoluten Volumeverbrauchs, einschließlich der von Momentaufnahmen verwendeten Kapazität, verwenden Sie die [Azure NetApp-Metriken](azure-netapp-files-metrics.md#volumes) im Azure-Portal. 

## <a name="using-azure-portal"></a>Verwenden des Azure-Portals
Azure NetApp Files nutzt die standardmäßige [Azure Monitor](../azure-monitor/overview.md)-Funktionalität. Sie können Azure Monitor somit zum Überwachen von Azure NetApp Files-Volumes verwenden.  

## <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle  

Sie können die [`az netappfiles volume`](/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true)-Befehle der [Azure-Befehlszeilentools (CLI-Tools)](azure-netapp-files-sdk-cli.md) verwenden, um ein Volume zu überwachen.
 
## <a name="using-rest-api"></a>Verwenden der REST-API  

Siehe [REST-API für Azure NetApp Files](azure-netapp-files-develop-with-rest-api.md) und [REST-API mit PowerShell für Azure NetApp Files](develop-rest-api-powershell.md). 

Die REST-API-Spezifikation und der Beispielcode für Azure NetApp Files sind im [GitHub-Verzeichnis „resource-manager“](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable) verfügbar. 

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zum Volumekontingent](volume-quota-introduction.md)
* [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
* [Ändern der Größe eines Kapazitätspools oder Volumes](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)