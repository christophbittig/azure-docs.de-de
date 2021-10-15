---
title: Planen der Notfallwiederherstellung für Azure Virtual Desktop
description: Erstellen Sie einen Notfallwiederherstellungsplan für Ihre Azure Virtual Desktop-Bereitstellung, um Ihre Daten zu schützen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 54202b49e2881d1d128136b876cbf88c2bc87400
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359751"
---
# <a name="azure-virtual-desktop-disaster-recovery"></a>Notfallwiederherstellung für Azure Virtual Desktop

Zum Schützen der Daten Ihrer Organisation müssen Sie möglicherweise eine Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) einführen. Eine solide BCDR-Strategie sorgt dafür, dass Ihre Apps und Workloads während geplanter und ungeplanter Wartungen oder Azure-Ausfälle weiter in Betrieb bleiben.

Azure Virtual Desktop bietet BCDR für den Azure Virtual Desktop-Dienst, um Metadaten von Kunden bei Ausfällen zu bewahren. Wenn ein Ausfall in einer Region auftritt, führen die Komponenten der Dienstinfrastruktur einen Failover auf den zweiten Standort aus und setzen ihre Funktion wie gewohnt fort. Sie können weiterhin auf die Metadaten für den Dienst zugreifen, und die Benutzer können weiterhin Verbindungen mit den verfügbaren Hosts herstellen. Die Verbindungen von Endbenutzern bleiben online, sofern auf die Mandantenumgebung oder die Hosts zugegriffen werden kann.

Damit sichergestellt ist, dass Benutzer auch während des Ausfalls einer Region Verbindungen herstellen können, müssen Sie ihre virtuellen Computer (VMs) an einen anderen Standort replizieren. Bei einem Ausfall erfolgt dann ein Failover vom primären Standort auf die replizierten VMs am sekundären Standort. Die Benutzer können ohne Unterbrechung weiterhin auf Apps am sekundären Standort zugreifen. Über die VM-Replikation hinaus müssen Sie den Zugriff auf die Benutzeridentitäten am sekundären Standort sicherstellen. Wenn Sie Profilcontainer verwenden, müssen diese ebenfalls repliziert werden. Stellen Sie schließlich sicher, dass Ihre Geschäftsanwendungen, die von Daten am primären Standort abhängig sind, einen Failover mit den restlichen Daten ausführen können.

Zusammenfassend lässt sich sagen, dass Sie die Verbindungen Ihrer Benutzer während eines Ausfalls nur dann sicherstellen können, wenn Sie die folgenden Dinge in der angegebenen Reihenfolge ausführen:

- Replizieren der VMs an einen sekundären Standort.
- Wenn Sie Profilcontainer verwenden, richten Sie am zweiten Standort Datenreplikation ein.
- Stellen Sie sicher, dass Benutzeridentitäten, die Sie am primären Standort einrichten, am sekundären Standort verfügbar sind.
- Sorgen Sie dafür, dass für alle Branchenanwendungen, die sich auf Daten an Ihrem primären Standort stützen, ein Failover auf den sekundären Standort erfolgt.

## <a name="vm-replication"></a>VM-Replikation

Zunächst müssen Sie Ihre VMs an den sekundären Standort replizieren. Ihre Optionen dafür hängen von der Konfiguration Ihrer VMs ab:

- Mit Azure Site Recovery können Sie alle Ihre VMs sowohl für gepoolte als auch für persönliche Hostpools konfigurieren. Bei dieser Methode müssen Sie nur einen Hostpool und die ihm zugeordneten App-Gruppen und Arbeitsbereiche einrichten.
- Sie können einen neuen Hostpool in der Failoverregion einrichten, dabei aber zugleich alle Ressourcen an Ihrem Failoverstandort deaktiviert lassen. Für diese Methode müssen Sie neue App-Gruppen und Arbeitsbereiche in der Failoverregion einrichten. Anschließend können Sie einen Azure Site Recovery-Plan zum Aktivieren von Host Pools verwenden.
- Sie können einen Hostpool erstellen, der von erstellten VMs sowohl in der primären als auch der Failoverregion aufgefüllt wird, wobei die VMs in der Failoverregion deaktiviert bleiben. In diesem Fall müssen Sie nur einen Hostpool und die ihm zugeordneten App-Gruppen und Arbeitsbereiche einrichten. Mit dieser Methode können Sie einen Azure Site Recovery-Plan verwenden, um die Hostpools einzuschalten.

Wir empfehlen die Verwendung von [Azure Site Recovery](../site-recovery/site-recovery-overview.md) zum Verwalten der Replikation von VMs an anderen Azure-Standorten, wie in [Azure-zu-Azure-Notfallwiederherstellung](../site-recovery/azure-to-azure-architecture.md) beschrieben. Insbesondere empfehlen wir die Verwendung von Azure Site Recovery für persönliche Hostpools, da Azure Site Recovery sowohl [serverbasierte als auch clientbasierte SKUs](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems) unterstützt.

Wenn Sie Azure Site Recovery verwenden, brauchen Sie diese VMs nicht manuell zu registrieren. Der Azure Virtual Desktop-Agent in der sekundären VM verwendet automatisch das neueste Sicherheitstoken, um eine Verbindung mit der nächstgelegenen Dienstinstanz herzustellen. Die VM (Sitzungshost) am sekundären Standort wird automatisch Teil des Hostpools. Der Endbenutzer muss während des Prozesses automatisch erneut eine Verbindung herstellen, abgesehen davon gibt es aber keine weiteren manuellen Vorgänge.

Wenn während des Ausfalls Benutzerverbindungen bestehen, müssen Sie die Benutzerverbindungen in der aktuellen Region beenden, bevor der Administrator den Failover auf die sekundäre Region einleiten kann.

Führen Sie dieses Cmdlet aus, um die Benutzer in Azure Virtual Desktop (klassisch) zu trennen:

```powershell
Invoke-RdsUserSessionLogoff
```

Führen Sie dieses Cmdlet aus, um die Benutzer in der in Azure integrierten Version von Azure Virtual Desktop zu trennen:

```powershell
Remove-AzWvdUserSession
```

Nachdem Sie alle Benutzer in der primären Region abgemeldet haben, können Sie ein Failover der VMs in der primären Region ausführen und Benutzern die Möglichkeit geben, Verbindungen mit den VMs in der sekundären Region herzustellen. Weitere Informationen zur Funktionsweise dieses Prozesses finden Sie unter [Replizieren von Azure-VMs in eine andere Azure-Region](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Virtuelles Netzwerk

Betrachten Sie als nächsten Punkt Ihre Netzwerkkonnektivität während des Ausfalls. Sie müssen sicherstellen, dass Sie in Ihrer sekundären Region ein virtuelles Netzwerk (VNET) eingerichtet haben. Wenn Ihre Benutzer auf lokale Ressourcen zugreifen müssen, müssen Sie dieses VNET für diesen Zugriff konfigurieren. Sie können lokale Verbindungen mit einem VPN, ExpressRoute oder einem virtuellen WAN einrichten.

Wir empfehlen, zum Einrichten des VNETs in der Failoverregion Azure Site Recovery zu verwenden, da hierbei die Einstellungen Ihres primären Netzwerks erhalten bleiben und kein Peering erforderlich ist.

## <a name="user-identities"></a>Benutzeridentitäten

Stellen Sie als nächstes sicher, dass der Domänencontroller am sekundären Standort verfügbar ist. 

Die Verfügbarkeit des Domänencontrollers kann auf drei Wegen sichergestellt werden:

   - Am sekundären Standort wird ein Active Directory-Domänencontroller verwendet
   - Es wird ein lokaler Active Directory-Domänencontroller verwendet
   - Der Active Directory-Domänencontroller wird mithilfe von [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) repliziert

## <a name="user-and-app-data"></a>Benutzer- und Anwendungsdaten

Wenn Sie Profilcontainer verwenden, besteht der nächste Schritt darin, Datenreplikation am sekundären Standort einzurichten. Es bestehen fünf Optionen zum Speichern von FSLogix-Profilen:

   - Direkte Speicherplätze (S2D)
   - Netzwerklaufwerke (VM mit Zusatzlaufwerken)
   - Azure Files
   - Azure NetApp Files
   - Cloudcache für die Replikation

Weitere Informationen finden Sie unter [Speicheroptionen für FSLogix-Profilcontainer in Azure Virtual Desktop](store-fslogix-profile.md).

Wenn Sie die Notfallwiederherstellung für Profile einrichten, bieten sich Ihnen die folgenden Optionen:

   - Einrichten der nativen Azure-Replikation (beispielsweise der Speicherkontoreplikation von Azure Files Standard, der Azure NetApp Files-Replikation oder der Azure-Dateisynchronisierung für Dateiserver).
    
     >[!NOTE]
     >Die NetApp-Replikation erfolgt nach der erstmaligen Einrichtung automatisch. Bei Azure Site Recovery-Plänen können Sie Prä- und Postskripts hinzufügen, um den Failover von anderen Ressourcen als VMs bei der Replikation von Azure Storage-Ressourcen auszuführen.

   - Einrichten von FSLogix Cloud Cache sowohl für Anwendungs- als auch für Benutzerdaten.
   - Richten Sie Notfallwiederherstellung nur für Anwendungsdaten ein, um den Zugriff auf geschäftswichtige Daten zu jeder Zeit zu gewährleisten. Bei dieser Methode können Sie Benutzerdaten nach dem Ende des Ausfalls abrufen.

Sehen wir uns einmal an, wie FSLogix zum Einrichten von Notfallwiederherstellung für die einzelnen Optionen konfiguriert wird.

### <a name="fslogix-configuration"></a>FSLogix-Konfiguration

Der FSLogix-Agent kann mehrere Profilstandorte unterstützen, wenn Sie die Registrierungseinträge für FSLogix konfigurieren.

Konfigurieren der Registrierungseinträge:

1. Öffnen Sie den **Registrierungs-Editor**.
2. Navigieren Sie zu **Computer** > **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **FSLogix** > **Profiles**.
   
     > [!div class="mx-imgBorder"]
     > ![Screenshot des Fensters „Profile“ im Registrierungs-Editor. „VHDLocation“ ist ausgewählt.](media/regedit-profiles.png)

3. Klicken Sie mit der rechten Maustaste auf **VHDLocations**, und wählen Sie **Mehrteilige Zeichenfolge bearbeiten** aus.

     > [!div class="mx-imgBorder"]
     > ![Screenshot des Fensters zum Bearbeiten von mehrteiligen Zeichenfolgen. In den Wertdaten sind die Standorte „USA, Mitte“ und „USA, Osten“ aufgelistet.](media/multi-string-edit.png)

4. Geben Sie im Feld **Wertdaten** die gewünschten Standorte ein.
5. Wenn Sie fertig sind, wählen Sie **OK**.

Wenn der erste Standort nicht verfügbar ist, führt der FSLogix-Agent automatisch einen Failover auf den zweiten aus, usw.

Es empfiehlt sich, den FSLogix-Agent mit einem Pfad zum sekundären Standort in der Hauptregion zu konfigurieren. Sobald der primäre Standort herunterfährt, wird der FLogix-Agent in Rahmen der VM-Azure Site Recovery-Replikation repliziert. Sobald die replizierten VMs bereit sind, versucht der Agent automatisch, den Pfad zur sekundären Region zu verwenden.

Angenommen, Ihre primären Sitzungshost-VMs befinden sich am Standort „USA, Mitte“, Ihre Profilcontainer befinden sich jedoch aus Leistungsgründen in der Region „USA, Mitte“.

In diesem Fall würden Sie den FSLogix-Agent mit einem Pfad zum Speicher in „USA, Mitte“ konfigurieren. Sie würden für die Sitzungshost-VMs Replikation in „USA, Westen“ konfigurieren. Sobald der Pfad zu „USA, Mitte“ ausfällt, versucht der Agent, stattdessen einen neuen Pfad zum Speicher in „USA, Westen“ zu erstellen.

### <a name="s2d"></a>S2D

Da S2D die Replikation zwischen Regionen intern behandelt, brauchen Sie den sekundären Pfad nicht manuell einzurichten.

### <a name="network-drives-vm-with-extra-drives"></a>Netzwerklaufwerke (VM mit Zusatzlaufwerken)

Wenn Sie die Netzwerkspeicher-VMs wie die Sitzungshost-VMs mithilfe von Azure Site Recovery replizieren, behält die Wiederherstellung den gleichen Pfad bei, was bedeutet, dass Sie FSlogix nicht zu konfigurieren brauchen.

### <a name="azure-files"></a>Azure Files

Azure Files unterstützt die regionsübergreifende asynchrone Replikation, die Sie beim Erstellen des Speicherkontos angeben können. Wenn Ihre Ziele für die Notfallwiederherstellung mit dem asynchronen Verhalten von Azure Files bereits abgedeckt sind, ist keine weitere Konfiguration erforderlich.

Wenn Sie synchrone Replikation benötigen, um Datenverluste zu minimieren, empfiehlt sich stattdessen der Einsatz von FSLogix Cloud Cache.

>[!NOTE]
>In diesem Abschnitt wird der Authentifizierungsmechanismus beim Failover für Azure Files nicht behandelt.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Erfahren Sie mehr über Azure NetApp Files unter [Erstellen von Replikationspeering für Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>App-Abhängigkeiten

Stellen Sie schließlich sicher, dass für alle Geschäftsanwendungen, die auf Daten aufbauen, die sich am primären Standort befinden, ein Failover zum sekundären Standort ausgeführt werden kann. Achten Sie ferner unbedingt darauf, die erforderlichen Einstellungen zu konfigurieren, damit die Apps am neuen Standort funktionieren können. Wenn beispielsweise eine der Apps vom SQL-Back-End abhängt, stellen Sie sicher, dass SQL am sekundären Standort repliziert wird. Sie sollten die App für die Verwendung des sekundären Standorts entweder als Teil des Failoverprozesses oder als Standardkonfiguration konfigurieren. Anwendungsabhängigkeiten können in Azure Site Recovery-Plänen modelliert werden. Weitere Informationen finden Sie unter [Informationen zu Wiederherstellungsplänen](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Tests der Notfallwiederherstellung

Nachdem Sie das Einrichten der Notfallwiederherstellung abgeschlossen haben, ist es sinnvoll, Ihren Plan zu testen, um sicherzustellen, dass er funktioniert.

Hier finden Sie einige Vorschläge zum Testen Ihres Plans:

- Wenn die Test-VMs über Internetzugang verfügen, übernehmen sie jeden vorhandenen Sitzungshost für neue Verbindungen, aber alle vorhandenen Verbindungen mit den ursprünglichen Sitzungshost bleiben aktiv. Achten Sie darauf, dass der Administrator, der den Test durchführt, alle aktiven Benutzer abmeldet, bevor er den Plan testet. 
- Tests der vollständigen Notfallwiederherstellung sollten nur während eines Wartungsfensters durchgeführt werden, um die Arbeit Ihrer Benutzer nicht zu unterbrechen. Sie können in der Validierungsumgebung für den Test auch einen Hostpool verwenden. 
- Vergewissern Sie sich, dass Ihr Test alle geschäftskritischen Apps abdeckt.
- Es empfiehlt sich, in einen Failoverprozess nicht mehr als 100 VMs einzubeziehen. Wenn Sie eine größere Anzahl VMs betreiben, empfehlen wir den Failover in Batches im Abstand von 10 Minuten.

## <a name="next-steps"></a>Nächste Schritte

Antworten auf Fragen zum Schutz Ihrer Daten über die Ausfallplanung hinaus finden Sie in unserem [Sicherheitsleitfaden](security-guide.md).