---
title: Einrichten von Azure Defender für SQL
description: Erfahren Sie, wie Sie den optionalen Plan „Azure Defender für SQL“ in Azure Security Center aktivieren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: bbfd426c67d09ca8632c3ab665b1242d27c6ad58
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711691"
---
# <a name="enable-azure-defender-for-sql-servers-on-machines"></a>Aktivieren von „Azure Defender für SQL Server-Instanzen auf Computern“ 

Dieser Azure Defender-Plan erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu missbrauchen.

Es werden Warnungen angezeigt, wenn verdächtige Datenbankaktivitäten, potenzielle Sicherheitsrisiken oder Angriffe durch Einschleusung von SQL-Befehlen sowie ungewöhnliche Datenbankzugriffs- und -abfragemuster vorliegen.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Azure Defender für SQL Server-Instanzen auf Computern** wird gemäß den Angaben unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Geschützte SQL-Versionen:|Azure SQL Server (alle vom Microsoft-Support abgedeckten Versionen)|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Einrichten von Azure Defender für SQL-Server auf Computern

So aktivieren Sie diesen Plan

[Schritt 1: Installieren der Agent-Erweiterung](#step-1-install-the-agent-extension)

[Schritt 2: Bereitstellen des Log Analytics-Agents auf dem Host Ihrer SQL Server-Instanz:](#step-2-provision-the-log-analytics-agent-on-your-sql-servers-host)

[Schritt 3: Aktivieren des optionalen Plans auf der Security Center-Seite „Preise und Einstellungen“:](#step-3-enable-the-optional-plan-in-security-centers-pricing-and-settings-page)


### <a name="step-1-install-the-agent-extension"></a>Schritt 1: Installieren der Agent-Erweiterung

- **SQL Server auf einer Azure-VM:** Registrieren Sie Ihre SQL Server-VM bei der SQL-IaaS-Agent-Erweiterung, wie unter [Registrieren einer SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) erläutert.

- **SQL Server auf Azure Arc:** Installieren Sie den Azure Arc-Agent anhand der Installationsmethoden, die in der [Azure Arc-Dokumentation](../azure-arc/servers/manage-vm-extensions.md) beschrieben sind.

### <a name="step-2-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Schritt 2: Bereitstellen des Log Analytics-Agents auf dem Host Ihrer SQL Server-Instanz:

- **SQL Server auf einer Azure-VM:** Wenn Ihr SQL-Computer auf einer Azure-VM gehostet wird, können Sie [die automatische Bereitstellung des Log Analytics-Agents aktivieren <a name="auto-provision-mma"></a>](security-center-enable-data-collection.md#auto-provision-mma). Alternativ dazu können Sie das manuelle Verfahren zum [Integrieren Ihrer Azure Stack Hub-VMs](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms) befolgen.
- **SQL Server unter Azure Arc**: Wenn Ihre SQL Server-Instanz auf [Azure Arc](../azure-arc/index.yml)-fähigen Servern gehostet wird, können Sie den Log Analytics-Agent unter Verwendung der Security Center-Empfehlung „Log Analytics-Agent muss auf Ihren Windows-basierten Azure Arc-Computern installiert sein (Vorschau)“ bereitstellen.

- **Lokale SQL Server-Instanz**: Wenn Ihre SQL Server-Instanz auf einem lokalen Windows-Computer ohne Azure Arc gehostet wird, haben Sie zwei Möglichkeiten, ihn mit Azure zu verbinden:
    
    - **Bereitstellen von Azure Arc**: Sie können jeden Windows-Computer mit Security Center verbinden. Azure Arc bietet jedoch eine tiefere Integration über Ihre *gesamte* Azure-Umgebung. Wenn Sie Azure Arc einrichten, wird die Seite **SQL Server – Azure Arc** im Portal angezeigt, und Ihre Sicherheitswarnungen werden auf dieser Seite auf einer dedizierten Registerkarte **Sicherheit** angezeigt. Die erste und empfohlene Option ist also, [Azure Arc auf dem Host einzurichten](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) und den Anweisungen für **SQL-Server unter Azure Arc** (siehe oben) zu folgen.
        
    - **Verbinden des Windows-Computers ohne Azure Arc**: Wenn Sie sich dafür entscheiden, eine auf einem Windows-Computer ausgeführte SQL Server-Instanz ohne Azure Arc zu verbinden, folgen Sie den Anweisungen unter [Verbinden von Windows-Computern mit Azure Monitor](../azure-monitor/agents/agent-windows.md).


### <a name="step-3-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Schritt 3: Aktivieren des optionalen Plans auf der Security Center-Seite „Preise und Einstellungen“:

1. Öffnen Sie im Menü von Security Center die Seite **Preise und Einstellungen**.

    - Wenn Sie den **Standardarbeitsbereich von Azure Security Center** (namens „defaultworkspace-[Ihre Abonnement-ID]-[Region]“) verwenden, wählen Sie das relevante **Abonnement** aus.

    - Wenn Sie **einen nicht standardmäßigen Arbeitsbereich** verwenden, wählen Sie den relevanten **Arbeitsbereich** aus (geben Sie ggf. den Namen des Arbeitsbereichs in den Filter ein):

        ![Suchen eines nicht standardmäßigen Arbeitsbereichs nach Titel](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Legen Sie die Option für **Azure Defender für SQL-Server auf Computern** auf **Ein** fest. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="Security Center-Seite „Preise“ mit optionalen Plänen":::

    Der Plan wird auf allen SQL-Servern aktiviert, die mit dem ausgewählten Arbeitsbereich verbunden sind. Der Schutz ist nach dem ersten Neustart der SQL Server-Instanz vollständig aktiv.

    >[!TIP] 
    > Um einen neuen Arbeitsbereich zu erstellen, befolgen Sie die Anweisungen unter [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/logs/quick-create-workspace.md).


1. Konfigurieren Sie optional eine E-Mail-Benachrichtigung für Sicherheitswarnungen. 
    Sie können eine Liste von Empfängern festlegen, die eine E-Mail-Benachrichtigung erhalten sollen, wenn Security Center-Warnungen generiert werden. Die E-Mail enthält einen direkten Link zur Warnung in Azure Security Center mit allen relevanten Details. Weitere Informationen finden Sie unter [Einrichten von E-Mail-Benachrichtigungen für Sicherheitswarnungen](security-center-provide-security-contact-details.md).


## <a name="azure-defender-for-sql-alerts"></a>Azure Defender für SQL – Warnungen
Warnungen werden bei ungewöhnlichen und potenziell schädlichen Zugriffsversuchen oder Exploitvorgängen für SQL-Computer generiert. Diese Ereignisse können Warnungen auslösen, die auf der [Warnungsreferenzseite](alerts-reference.md#alerts-sql-db-and-warehouse) angezeigt werden.

## <a name="explore-and-investigate-security-alerts"></a>Erkunden und Untersuchen von Sicherheitswarnungen

Azure Defender für SQL-Warnungen sind auf der Security Center-Seite „Warnungen“, auf der Seite „Sicherheit“ des Computers, auf dem [Azure Defender-Dashboard](azure-defender-dashboard.md) oder über den direkten Link in den Warnungs-E-Mails verfügbar.

1. Um die Warnungen anzuzeigen, wählen Sie im Menü von Security Center **Sicherheitswarnungen** und dann eine Warnung aus.

1. Warnungen sind eigenständig und enthalten jeweils ausführliche Schritte zur Bereinigung und Untersuchungsinformationen. Sie können weitere Untersuchungen durchführen, indem Sie andere Azure Security Center- und Azure Sentinel-Funktionen für eine umfassendere Ansicht verwenden:

    * Aktivieren Sie die Überwachungsfunktion von SQL Server, um weitere Untersuchungen durchzuführen. Wenn Sie Azure Sentinel-Benutzer sind, können Sie die SQL-Überwachungsprotokolle aus den Windows-Sicherheitsprotokollereignissen in Sentinel hochladen, wo Ihnen umfassende Untersuchungsfunktionen zur Verfügung stehen. [Weitere Informationen zur SQL Server-Überwachung](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Um Ihren Sicherheitsstatus zu verbessern, verwenden Sie die in jeder Warnung aufgeführten Security Center-Empfehlungen für den Hostcomputer. Dadurch werden die Risiken zukünftiger Angriffe reduziert. 

    [Weitere Informationen zum Verwalten von und Reagieren auf Warnungen](security-center-managing-and-responding-alerts.md).


## <a name="faq---azure-defender-for-sql-servers-on-machines"></a>Häufig gestellte Fragen – Azure Defender für SQL-Server auf Computern

### <a name="if-i-enable-this-azure-defender-plan-on-my-subscription-are-all-sql-servers-on-the-subscription-protected"></a>Sind alle SQL Server im Abonnement geschützt, wenn ich diesen Azure Defender-Plan für mein Abonnement aktiviere? 

Nein. Zum Schützen einer SQL Server-Bereitstellung auf einem virtuellen Azure-Computer oder einer SQL Server-Instanz, die auf einem für Azure Arc aktivierten Computer ausgeführt wird, sind für Azure Defender folgende Komponenten erforderlich:

- ein Log Analytics-Agent auf dem Computer 
- der relevante Log Analytics-Arbeitsbereich, um die Azure Defender für SQL-Lösung zu aktivieren 

Der *Abonnementstatus*, der auf der SQL Server-Seite im Azure-Portal angezeigt wird, spiegelt den Standardarbeitsbereichsstatus wider und gilt für alle verbundenen Computer. Nur die SQL Server-Instanzen auf Hosts mit einem Log Analytics-Agent, der Berichte an diesen Arbeitsbereich sendet, wird durch Azure Defender geschützt. 




## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie im folgenden Artikel:

- [Sicherheitswarnungen für Azure SQL-Datenbank und Azure Synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Einrichten einer E-Mail-Benachrichtigung für Sicherheitswarnungen](security-center-provide-security-contact-details.md)
- [Weitere Informationen zu Azure Sentinel](../sentinel/index.yml)
