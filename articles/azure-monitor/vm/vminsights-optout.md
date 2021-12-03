---
title: Deaktivieren der Überwachung in VM Insights
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung Ihrer virtuellen Computer in VM Insights beenden können.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 8edb225316dfd12acff5c1f6a036246d68d746c8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306433"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Deaktivieren der Überwachung Ihrer VMs in VM Insights

Nachdem Sie die Überwachung Ihrer virtuellen Computer (VMs) aktiviert haben, können Sie die Überwachung später in VM Insights deaktivieren. In diesem Artikel wird das Deaktivieren der Überwachung für eine oder mehrere VMs beschrieben.  

Zurzeit unterstützt VM Insights kein selektives Deaktivieren der VM-Überwachung. Möglicherweise unterstützt Ihr Log Analytics-Arbeitsbereich VM Insights und weitere Lösungen und kann auch andere Überwachungsdaten sammeln. Wenn Ihr Log Analytics-Arbeitsbereich diese Dienste bereitstellt, müssen Ihnen die Auswirkungen und die Methoden zum Deaktivieren der Überwachung bekannt sein, bevor Sie beginnen.

VM Insights nutzt die folgenden Komponenten:

* Einen Log Analytics-Arbeitsbereich, in dem Überwachungsdaten von VMs und anderen Quellen gespeichert werden.
* Eine Sammlung von Leistungsindikatoren, die im Arbeitsbereich konfiguriert ist. Diese Sammlung aktualisiert die Überwachungskonfiguration auf allen mit dem Arbeitsbereich verbundenen VMs.
* `VMInsights`, wobei es sich um eine im Arbeitsbereich konfigurierte Überwachungslösung handelt. Diese Lösung aktualisiert die Überwachungskonfiguration auf allen mit dem Arbeitsbereich verbundenen VMs.
* `MicrosoftMonitoringAgent` (für Windows) oder `OmsAgentForLinux` (für Linux) und `DependencyAgent`, die Azure VM-Erweiterungen sind. Diese Erweiterungen sammeln Daten und senden diese an den Arbeitsbereich.

Beachten Sie bei den Vorbereitungen zum Deaktivieren der Überwachung Ihrer VMs die folgenden Aspekte:

* Wenn Sie das Verfahren nur für eine einzelne VM ausgeführt und den vorab ausgewählten Log Analytics-Arbeitsbereich verwendet haben, können Sie die Überwachung deaktivieren, indem Sie den Abhängigkeits-Agent auf der VM deinstallieren und den Log Analytics-Agent von diesem Arbeitsbereich trennen. Dieses Verfahren ist geeignet, wenn Sie die VM für andere Zwecke verwenden und später möglicherweise mit einem anderen Arbeitsbereich verbinden möchten.
* Wenn Sie einen bereits vorhandenen Log Analytics-Arbeitsbereich ausgewählt haben, der andere Überwachungslösungen und die Sammlung von Daten aus anderen Quellen unterstützt, können Sie Lösungskomponenten aus dem Arbeitsbereich entfernen, ohne dass dies zu Unterbrechungen oder anderen Auswirkungen auf den Arbeitsbereich führt.  

>[!NOTE]
> Nach dem Entfernen der Lösungskomponenten aus dem Arbeitsbereich werden möglicherweise Leistungs- und Zuordnungsdaten für Ihre Azure-VMs angezeigt. In den Ansichten **Leistung** und **Zuordnung** werden schließlich keine Daten mehr angezeigt. Bei der ausgewählten Azure-VM steht die Option **Aktivieren** zur Verfügung, sodass Sie die Überwachung zu einem späteren Zeitpunkt wieder aktivieren können.  

## <a name="remove-vm-insights-completely"></a>Vollständiges Entfernen von VM Insights

Wenn Sie den Log Analytics-Arbeitsbereich weiterhin benötigen, führen Sie die folgenden Schritte aus, um VM Insights vollständig zu entfernen. Sie entfernen die Lösung `VMInsights` aus dem Arbeitsbereich.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, werden Vorschläge in der Liste auf Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
3. Wählen Sie in der Liste der Log Analytics-Arbeitsbereiche den Arbeitsbereich aus, den Sie beim Aktivieren von VM Insights ausgewählt hatten.
4. Wählen Sie links die Option **Lösungen** aus.  
5. Wählen Sie in der Liste der Lösungen den Eintrag **VMInsights(Arbeitsbereichsname)** aus. Wählen Sie auf der Seite **Übersicht** für die Lösung die Option **Löschen** aus. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Deaktivieren der Überwachung und Beibehalten des Arbeitsbereichs  

Wenn der Log Analytics-Arbeitsbereich weiterhin die Überwachung von Daten aus anderen Quellen unterstützen muss, führen Sie die folgenden Schritte aus, um die Überwachung auf dem virtuellen Computer zu deaktivieren, den Sie zum Auswerten von VM Insights verwendet haben. Bei virtuellen Azure-Computern entfernen Sie die VM-Erweiterungen für den Abhängigkeits-Agent und den Log Analytics-Agent für Windows oder Linux direkt vom virtuellen Computer. 

>[!NOTE]
>Entfernen Sie den Log Analytics-Agent in folgenden Fällen nicht: 
>
> * Azure Automation verwaltet den virtuellen Computer zur Orchestrierung von Prozessen oder zur Verwaltung von Konfiguration oder Updates. 
> * Microsoft Defender für Cloud verwaltet den virtuellen Computer zur Sicherheits- und Bedrohungserkennung. 
>
> Wenn Sie den Log Analytics-Agent entfernen, verhindern Sie eine proaktive Verwaltung Ihres virtuellen Computers durch diese Dienste und Lösungen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Wählen Sie im Azure-Portal die Option **Virtual Machines** aus. 
3. Wählen Sie einen virtuellen Computer in der Liste aus. 
4. Klicken Sie links auf **Erweiterungen**. Wählen Sie auf der Seite **Erweiterungen** die Option **DependencyAgent** aus.
5. Klicken Sie auf der Eigenschaftenseite der Erweiterung auf **Deinstallieren**.
6. Wählen Sie auf der Seite **Erweiterungen** die Option **MicrosoftMonitoringAgent** aus. Klicken Sie auf der Eigenschaftenseite der Erweiterung auf **Deinstallieren**.  
