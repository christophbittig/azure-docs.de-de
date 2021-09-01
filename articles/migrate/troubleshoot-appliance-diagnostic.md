---
title: Behandeln von Problemen bei der Azure Migrate-Appliancediagnose
description: Hilfe bei der Diagnose und Behandlung von Problemen, die mit der Azure Migrate-Appliance auftreten können.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.openlocfilehash: 03be4d6a4b5afb15b5820c1c8b334e3fcb80bbbb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350628"
---
# <a name="diagnose-and-solve-issues-with-azure-migrate-appliance"></a>Diagnostizieren und Beheben von Problemen mit der Azure Migrate-Appliance

Die Funktion für **Diagnose und Problembehandlung** auf der Azure Migrate-Appliance hilft Benutzern, Probleme mit der Appliancekonfiguration zu identifizieren und selbst zu bewerten, die möglicherweise die Initiierung der Ermittlung blockieren, oder Probleme mit einem fortlaufenden Migrate-Vorgang wie Ermittlung, Bewertung und/oder Replikation (*im Falle einer VMware-Appliance*) auf der Appliance zu erkennen. 

Sie können die Funktion für **Diagnose und Problembehandlung** jederzeit über den Appliancekonfigurations-Manager ausführen, um einen Diagnosebericht zu generieren. Der Bericht enthält Informationen zu den durchgeführten Überprüfungen, ihrem Status, den identifizierten Problemen und Empfehlungsschritte zur Lösung der Probleme. 

> [!IMPORTANT]
> Die Funktion für **Diagnose und Problembehandlung** befindet sich derzeit in der Vorschau für die Azure Migrate-Appliance.
> Diese Vorschau wird durch den Kundensupport abgedeckt und kann für Produktionsworkloads verwendet werden.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="diagnostic-checks"></a>Diagnoseprüfungen

Die Funktion für *Diagnose und Problembehandlung* führt einige Vorabüberprüfungen aus, um zu überprüfen, ob die erforderlichen Konfigurationsdateien fehlen oder von einer Antivirensoftware auf der Appliance blockiert werden. Anschließend werden die folgenden Überprüfungen ausgeführt: 

**Kategorie** | **Diagnoseprüfung** |**Beschreibung**
--- | --- | --- |
**Voraussetzungsprüfungen** | Konnektivitätsprüfungen | Überprüft, ob die Appliance entweder direkt oder über einen Proxy mit Azure verbunden ist.
|| Überprüfung der Zeitsynchronisierung | Überprüft, ob die Zeit des Applianceservers mit der Netzwerkzeit synchron ist.
|| Überprüfung der automatischen Aktualisierung | Überprüft, ob automatische Aktualisierung aktiviert ist und ob alle auf der Appliance ausgeführten Agents auf dem neuesten Stand sind.
||VDDK-Überprüfung | Überprüft, ob die erforderlichen VDDK-Dateien heruntergeladen und an den erforderlichen Speicherort auf dem Applianceserver kopiert wurden.
**Integritätsprüfungen für den Dienst** |Betriebsstatus |Überprüft, ob die Agents auf der Appliance ausgeführt werden. <br>*Wenn dies nicht der Fall ist, löst die Appliance das Problem automatisch, indem die Agents neu gestartet werden.* 
||Konnektivität des Dienstendpunkts |Überprüft, ob die Agents entweder direkt oder über einen Proxy mit ihren jeweiligen Diensten in Azure kommunizieren können.
**Azure-spezifische Überprüfungen** |Verfügbarkeit der AAD-App* | Überprüft, ob die während der Applianceregistrierung erstellte AAD-App verfügbar ist und auf sie von der Appliance aus zugegriffen werden kann.
||Verfügbarkeit des Migrate-Projekts* | Überprüft, ob das Migrate-Projekt, bei dem die Appliance registriert wurde, noch vorhanden ist und auf das Projekt von der Appliance aus zugegriffen werden kann.
||Verfügbarkeit wichtiger Ressourcen*| Überprüft, ob die während der Applianceregistrierung erstellten Migrate-Ressourcen noch vorhanden sind und auf sie von der Appliance aus zugegriffen werden kann.
**Appliancespezifische Überprüfungen** | Key Vault-Zertifikatverfügbarkeit* | Überprüft, ob das während der Applianceregistrierung aus Key Vault heruntergeladene Zertifikat auf dem Applianceserver weiterhin verfügbar ist. <br> *Falls dies nicht der Fall ist, löst die Appliance das Problem automatisch, indem sie das Zertifikat erneut herunterlädt (vorausgesetzt, dass die Key Vault-Instanz verfügbar ist und auf sie zugegriffen werden kann)* .
|| Verfügbarkeit des Anmeldeinformationsspeichers | Überprüft, ob die Anmeldeinformationen-Speicherressourcen auf dem Applianceserver nicht verschoben/gelöscht/bearbeitet wurden.
|| Replikationsappliance/ASR-Komponenten | Überprüft, ob derselbe Server auch zum Installieren von ASR-/Replikationsappliancekomponenten verwendet wurde. *Es wird derzeit nicht unterstützt, sowohl Azure Migrate als auch die Replikationsappliance (für Agent-basierte Migration) auf demselben Server zu installieren.*
|| Verfügbarkeit der Betriebssystemlizenz | Überprüft, ob die Auswertungslizenz auf dem Applianceserver, der von OVA/VHD erstellt wurde, noch gültig ist. *Die Windows Server 2016-Evaluierungslizenz ist 180 Tage lang gültig.*
|| CPU- und Arbeitsspeichernutzung | Überprüft die CPU und den Arbeitsspeicher, die von den Migrate-Agents auf dem Applianceserver genutzt werden.  

**Wird nur dann überprüft und gemeldet, wenn die Appliance bereits registriert wurde. Diese Überprüfungen werden im Kontext des aktuellen Azure-Benutzers ausgeführt, der bei der Appliance angemeldet ist*.

## <a name="running-diagnostic-checks"></a>Ausführen von Diagnoseüberprüfungen

Wenn während der Konfiguration Probleme mit der Appliance oder Probleme mit den fortlaufenden Migrationsvorgängen wie Ermittlung, Bewertung und/oder Replikation (im Fall einer *VMware-Appliance*) im Portal angezeigt werden, können Sie zum Appliancekonfigurations-Manager wechseln und die Diagnose ausführen.

> [!NOTE]
> Derzeit kann die Funktion für **Diagnose und Problembehandlung** Überprüfungen in Bezug auf die Appliancekonnektivität mit Azure und die Verfügbarkeit der erforderlichen Ressourcen auf dem Applianceserver und/oder in Azure durchführen. Die Konnektivitäts- oder Ermittlungsprobleme mit einer Quellumgebung wie vCenter Server/ESXi-Hosts/Hyper-V-Hosts/VMs/physische Server werden derzeit nicht mit der Funktion für **Diagnose und Problembehandlung** behandelt.
 
1. Wählen Sie im Menüband oben im Konfigurations-Manager die Funktion für **Diagnose und Problembehandlung** aus.

    ![Auswählen der Funktion für Diagnose und Problembehandlung](./media/troubleshoot-appliance-diagnostic-solve/appliance-configuration-manager-diagnose-solve.png)
    
    Nachdem die Funktion für **Diagnose und Problembehandlung** ausgewählt wurde, startet die Appliance automatisch die Ausführung der Diagnoseüberprüfungen. Dieser Vorgang dauert etwa 5 Minuten.
    *Wenn Sie die Überprüfungen zuvor durchgeführt haben, wird der Zeitstempel des letzten Diagnoseberichts angezeigt.*
     
    ![Diagnosebericht](./media/troubleshoot-appliance-diagnostic-solve/diagnostic-report.png)

1. Nach Abschluss der Diagnoseprüfungen können Sie den Bericht entweder auf einer anderen Registerkarte anzeigen, auf der Sie ihn in einem PDF-Format speichern können, oder sie können auf dem Applianceserver zu diesem Speicherort navigieren: **C:\Users\Public\Desktop\DiagnosticsReport**. Dort wird der Bericht automatisch in einem HTML-Format gespeichert.

    ![Anzeigen des Diagnoseberichts](./media/troubleshoot-appliance-diagnostic-solve/view-diagnostic-report.png)

1. Der Bericht enthält Informationen zu den durchgeführten Überprüfungen, ihrem Status, den identifizierten Problemen und Empfehlungsschritte zur Lösung der Probleme.

    ![Anzeigen des Status des Diagnoseberichts](./media/troubleshoot-appliance-diagnostic-solve/view-status.png)

1. Sie können die Korrekturschritte im Bericht ausführen, um ein Problem zu beheben. Wenn Sie das Problem nicht beheben können, wird empfohlen, den Diagnosebericht beim Erstellen eines Microsoft-Supportfalls anzufügen, damit die Lösung beschleunigt wird.

## <a name="next-steps"></a>Nächste Schritte
Wenn Probleme auftreten, die nicht durch die Funktion für **Diagnose und Problembehandlung** abgedeckt werden, können Sie unter [Problembehandlung der Azure Migrate-Appliance](./troubleshoot-appliance.md) Schritte zur Problembehandlung finden.