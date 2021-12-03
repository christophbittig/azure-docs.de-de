---
title: Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Log Analytics-Arbeitsbereich erstellen, um Verwaltungslösungen und die Datensammlung in Ihren cloudbasierten und lokalen Umgebungen im Azure-Portal zu aktivieren.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 3d9e075e50b4be0a21f4138a49faf8309474ad9c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440049"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal
Im Menü **Log Analytics-Arbeitsbereiche** können Sie einen Log Analytics-Arbeitsbereich über das Azure-Portal einrichten. Ein Log Analytics-Arbeitsbereich ist eine spezifische Umgebung für Azure Monitor-Protokolldaten. Jeder Arbeitsbereich verfügt über ein eigenes Datenrepository und eine eigene Konfiguration. Datenquellen und Lösungen sind so konfiguriert, dass die zugehörigen Daten in einem bestimmten Arbeitsbereich gespeichert werden. Ein Log Analytics-Arbeitsbereich ist erforderlich, wenn Sie Daten aus den folgenden Quellen erfassen möchten:

* Azure-Ressourcen im Abonnement
* Lokale Computer, die von System Center Operations Manager überwacht werden
* Gerätesammlungen aus Configuration Manager 
* Diagnose- oder Protokolldaten aus dem Azure-Speicher

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an. 

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs
Klicken Sie im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.

![Azure-Portal](media/quick-create-workspace/azure-portal-01.png)
  
Klicken Sie auf **Hinzufügen**, und geben Sie dann Werte für die folgenden Optionen an:

   * Wählen Sie ein **Abonnement** aus, mit dem eine Verknüpfung erstellt werden soll, indem Sie in der Dropdownliste einen anderen Eintrag auswählen, falls der Standardeintrag nicht geeignet ist.
   * Wählen Sie für **Ressourcengruppe** eine vorhandene und bereits eingerichtete Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.  
   * Geben Sie einen Namen für den neuen **Log Analytics-Arbeitsbereich** ein, z.B. *DefaultLAWorkspace*. Dieser Name muss innerhalb einer Ressourcengruppe eindeutig sein.
   * Wählen Sie eine verfügbare **Region** aus.  Weitere Informationen finden Sie auf der Seite zur [Verfügbarkeit von Log Analytics in den einzelnen Regionen](https://azure.microsoft.com/regions/services/), und suchen Sie im Feld **Nach einem Produkt suchen** nach Azure Monitor.  


        ![Erstellen des Log Analytics-Ressourcenblatts](media/quick-create-workspace/create-workspace.png)  


Klicken Sie auf **Überprüfen + erstellen**, um die Einstellungen zu überprüfen, und dann auf **Erstellen**, um den Arbeitsbereich zu erstellen. Dadurch wird als Standardtarif die nutzungsbasierte Bezahlung ausgewählt. Hier fallen keine Kosten an, bis Sie mit der Erfassung einer ausreichenden Datenmenge beginnen. Weitere Informationen zu anderen Tarifen finden Sie unter [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/log-analytics/).



## <a name="troubleshooting"></a>Problembehandlung
Wenn Sie einen Arbeitsbereich erstellen, der in den letzten 14 Tagen gelöscht wurde und sich im [Zustand des vorläufigen Löschens](../logs/delete-workspace.md#soft-delete-behavior) befindet, kann der Vorgang je nach Arbeitsbereichskonfiguration ein anderes Ergebnis aufweisen:
1. Wenn Sie denselben Arbeitsbereichsnamen, dieselbe Ressourcengruppe, dasselbe Abonnement und dieselbe Region wie beim gelöschten Arbeitsbereich angeben, wird der Arbeitsbereich einschließlich Daten, Konfiguration und verbundener Agents wiederhergestellt.
2. Der Arbeitsbereichsname muss innerhalb einer Ressourcengruppe eindeutig sein. Bei Verwendung eines bereits vorhandenen Arbeitsbereichsnamens wird die Fehlermeldung „Der Arbeitsbereichsname ‚Arbeitsbereichsname‘ ist nicht eindeutig“ oder ein Konflikt angezeigt. Dies schließt auch Namen von Elementen in Ihrer Ressourcengruppe ein, die vorläufig gelöscht wurden. Wenn Sie das vorläufige Löschen außer Kraft setzen, den Arbeitsbereich dauerhaft löschen und einen neuen, gleichnamigen Arbeitsbereich erstellen möchten, gehen Sie folgendermaßen vor, um den Arbeitsbereich zunächst wiederherzustellen und dann dauerhaft zu löschen:
   - [Wiederherstellen](../logs/delete-workspace.md#recover-workspace) Ihres Arbeitsbereichs
   - [Dauerhaftes Löschen](../logs/delete-workspace.md#permanent-workspace-delete) Ihres Arbeitsbereichs
   - Erstellen eines neuen Arbeitsbereichs mit demselben Arbeitsbereichsnamen

## <a name="next-steps"></a>Nächste Schritte
Sie besitzen einen verfügbaren Arbeitsbereich und können nun die Erfassung von Überwachungstelemetrie konfigurieren, Protokollsuchen zum Analysieren dieser Daten ausführen und eine Verwaltungslösung hinzufügen, um weitere daten- und analysebasierte Einblicke zu gewinnen. 

* Unter [Überwachen der Integrität von Log Analytics-Arbeitsbereichen in Azure Monitor](../logs/monitor-workspace.md) erfahren Sie mehr über das Erstellen von Warnungsregeln zum Überwachen der Integrität Ihres Arbeitsbereichs. 
* Unter [Sammeln von Azure-Dienstprotokollen und Metriken zur Verwendung in Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) erfahren Sie mehr über das Aktivieren der Datensammlung aus Azure-Ressourcen mit Azure-Diagnose oder Azure Storage.
