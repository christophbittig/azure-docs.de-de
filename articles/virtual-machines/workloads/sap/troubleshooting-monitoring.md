---
title: Überwachen von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Informieren Sie sich über die Überwachung von SAP HANA in Azure (große Instanzen).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, contperf-fy21q4
ms.openlocfilehash: d8325a9b5a3b8adaf3f39cde791e9a00f3f897e3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036597"
---
# <a name="monitor-sap-hana-large-instances-on-azure"></a>Überwachen von SAP HANA in Azure (große Instanzen)

In diesem Artikel wird die Überwachung von SAP HANA in Azure (große Instanzen) – auch als BareMetal-Infrastruktur bezeichnet – beschrieben.

SAP HANA in Azure (große Instanzen) unterscheidet sich nicht von anderen IaaS-Bereitstellungen. Die Überwachung des Betriebssystems und der Anwendung ist wichtig. Sie sollten wissen, wie die Anwendungen die folgenden Ressourcen nutzen:

- CPU
- Arbeitsspeicher
- Netzwerkbandbreite
- Speicherplatz

Überwachen Sie SAP HANA (große Instanzen), um festzustellen, ob die oben genannten Ressourcen ausreichend Kapazität bieten oder irgendwann erschöpft werden. In den folgenden Abschnitten finden Sie weitere Details zu diesen Ressourcen.

## <a name="cpu-resource-consumption"></a>CPU-Ressourcennutzung

SAP definiert einen maximalen Schwellenwert für die CPU-Nutzung der SAP HANA-Workload. Indem Sie unter diesem Schwellenwert bleiben, stellen Sie sicher, dass Sie über genügend CPU-Ressourcen zum Verarbeiten der im Arbeitsspeicher gespeicherten Daten verfügen. Eine hohe CPU-Nutzung kann auftreten, wenn SAP HANA-Dienste Abfragen aufgrund fehlender Indizes oder ähnlicher Probleme ausführen. Die Überwachung der CPU-Nutzung der Instanz von HANA (große Instanzen) und von bestimmten HANA-Diensten ist daher von entscheidender Bedeutung.

## <a name="memory-consumption"></a>Arbeitsspeicherverbrauch 

Die Arbeitsspeichernutzung muss sowohl innerhalb als auch außerhalb von HANA auf der Instanz von SAP HANA (große Instanzen) überwacht werden. Überwachen Sie, wie die Daten den für HANA zugeordneten Arbeitsspeicher nutzen, damit Sie die SAP-Richtlinien zur Dimensionierung einhalten können. Überwachen Sie die Arbeitsspeichernutzung auf der großen Instanz, um sicherzustellen, dass nicht zu viel Arbeitsspeicher von anderer Software als HANA verbraucht wird. Sie müssen verhindern, dass solche Software mit HANA um Arbeitsspeicher konkurriert.

## <a name="network-bandwidth"></a>Netzwerkbandbreite 

Die Bandbreite des Azure Virtual Network-Gateways (VNet-Gateway) ist begrenzt. Daten können nur innerhalb dieser Grenzen in das Azure-VNet übertragen werden. Überwachen Sie die Daten, die von allen Azure-VMs in einem VNet empfangen werden. So wissen Sie, wann Sie sich den Grenzwerten der von Ihnen ausgewählten Azure-Gateway-SKU nähern. Es ist auch sinnvoll, ein- und ausgehenden Netzwerkdatenverkehr auf der Instanz von HANA (große Instanzen) zu überwachen, um die im Laufe der Zeit verarbeiteten Datenmengen nachzuverfolgen.

## <a name="disk-space"></a>Speicherplatz

Die Speicherplatzbelegung steigt in der Regel mit der Zeit. Häufige Ursachen sind:
- Zunahme der Datenmenge im Laufe der Zeit
- Ausführung von Transaktionsprotokollsicherungen
- Speicherung von Ablaufverfolgungsdateien
- Erstellung von Speichermomentaufnahmen 

Aus diesem Grund ist es wichtig, die Speicherplatzbelegung zu überwachen und den Speicherplatz zu verwalten, der der Instanz von HANA (große Instanzen) zugeordnet ist.

## <a name="preloaded-system-diagnostic-tools"></a>Im Voraus geladene Systemdiagnosetools

Bei **SKUs von Typ II** großer HANA-Instanzen sind bereits Systemdiagnosetools auf dem Server geladen. Sie können diese Diagnosetools verwenden, um die Integrität des Systems zu überprüfen.
 
Führen Sie den folgenden Befehl aus, um die Systemdiagnose-Protokolldatei unter „/var/log/health_check“ zu generieren.

```
/opt/sgi/health_check/microsoft_tdi.sh
```
Wenn Sie zur Behandlung eines Problems das Microsoft-Supportteam zurate ziehen, werden Sie eventuell gebeten, die Protokolldateien mithilfe dieser Diagnosetools zu übermitteln. Sie können die Datei mit dem folgenden Befehl zippen:

```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

## <a name="azure-monitor-for-sap-solutions"></a>Azure Monitor für SAP-Lösungen

Sie können Azure Monitor für SAP-Lösungen verwenden, um alle der oben aufgeführten sowie weitere Ressourcen zu überwachen. Azure Monitor für SAP-Lösungen ist eine native Azure-Lösung. Sie ermöglicht es Ihnen, Daten aus der Azure-Infrastruktur sowie aus Datenbanken an einem zentralen Ort zu sammeln und die Daten zur schnelleren Problembehandlung visuell zu korrelieren. Weitere Informationen finden Sie unter [Überwachen von SAP in Azure](../../../virtual-machines/workloads/sap/monitor-sap-on-azure.md).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die Überwachung und Problembehandlung in SAP HANA.

> [!div class="nextstepaction"]
> [Überwachung und Problembehandlung auf HANA-Seite](hana-monitor-troubleshoot.md)
