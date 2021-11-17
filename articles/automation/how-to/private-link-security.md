---
title: Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Automation
description: Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Automation
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 96d5fd7942d744fcface931991df9d32c3caf15e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485744"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Automation

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNet und bindet den Automation-Dienst dadurch in Ihr VNet ein. Der Netzwerkdatenverkehr zwischen den Computern im VNet und dem Automation-Konto wird über das VNet und eine private Verbindung im Microsoft-Backbone-Netzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt.

Angenommen, Sie verfügen über ein VNet, in dem der ausgehende Internetzugriff deaktiviert ist. Sie möchten jedoch privat auf Ihr Automation-Konto zugreifen und Automation-Funktionen wie Webhooks, State Configuration und Runbookaufträge für Hybrid Runbook Worker verwenden. Außerdem möchten Sie, dass Benutzer nur über das VNet auf das Automation-Konto zugreifen können.  Diese Ziele werden durch die Bereitstellung eines privaten Endpunkts erreicht.

In diesem Artikel wird erläutert, wann und wie Sie einen privaten Endpunkt mit Ihrem Automation-Konto einrichten.

![Konzeptionelle Übersicht von Private Link für Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Die Unterstützung für Private Link mit Azure Automation ist nur in Azure Commercial-Clouds und Clouds der US-Regierungsbehörden verfügbar.

## <a name="advantages"></a>Vorteile

Private Link bietet folgende Möglichkeiten:

- Herstellen einer privaten Verbindung mit Azure Automation ohne öffentlichen Netzwerkzugriff.
- Herstellen einer privaten Verbindung mit einem Azure Monitor Log Analytics-Arbeitsbereich ohne öffentlichen Netzwerkzugriff.

    >[!NOTE]
    >Ein separater privater Endpunkt für Ihren Log Analytics-Arbeitsbereich wird dann benötigt, wenn Ihr Automation-Konto mit einem Log Analytics-Arbeitsbereich verknüpft ist, um Auftragsdaten weiterzuleiten, und wenn Sie Funktionen wie Updateverwaltung, Änderungsnachverfolgung und Bestand, State Configuration oder „VMs außerhalb der Geschäftszeiten starten/beenden“ aktiviert haben. Weitere Informationen zu Private Link für Azure Monitor finden Sie unter [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Monitor](../../azure-monitor/logs/private-link-security.md).

- Sicherstellen, dass der Zugriff auf Ihre Automation-Daten nur über autorisierte private Netzwerke erfolgt.
- Verhindern von Datenexfiltration aus Ihren privaten Netzwerken durch Definieren Ihrer Azure Automation-Ressource, die eine Verbindung über Ihren privaten Endpunkt herstellt.
- Herstellen einer sicheren Verbindung zwischen Ihrem privaten lokalen Netzwerk und Azure Automation mittels ExpressRoute und Private Link.
- Kapseln des gesamten Datenverkehrs innerhalb des Microsoft Azure-Backbonenetzwerks.

Weitere Informationen finden Sie unter [Hauptvorteile von Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Einschränkungen

- In der aktuellen Implementierung von Private Link können Cloudaufträge von Automation-Konten nicht auf Azure-Ressourcen zugreifen, die mit einem privaten Endpunkt geschützt werden. Beispiele hierfür sind Azure Key Vault, Azure SQL, Azure Storage-Konto usw. Um dieses Problem zu umgehen, verwenden Sie stattdessen einen [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).
- Sie müssen die aktuelle Version des [Log Analytics-Agents](../../azure-monitor/agents/log-analytics-agent.md) für Windows oder Linux verwenden.
- Das [Log Analytics-Gateway](../../azure-monitor/agents/gateway.md) unterstützt Private Link nicht.

## <a name="how-it-works"></a>Funktionsweise

Azure Automation Private Link stellt eine Verbindung zwischen einem oder mehreren privaten Endpunkten (und damit auch den virtuellen Netzwerke, in denen sich die Endpunkte befinden) und Ihrer Azure Automation-Ressource her. Diese Endpunkte sind Computer, die Webhooks verwenden, um Runbooks zu starten, Computer, die die Rolle „Hybrid Runbook Worker“ hosten, und DSC-Knoten (Desired State Configuration).

Nachdem Sie private Endpunkte für Automation erstellt haben, wird jede der öffentlichen Automation-URLs einem privaten Endpunkt in Ihrem VNet zugeordnet. Sie (oder ein Computer) können direkt auf die Automation-URLs zugreifen.

### <a name="webhook-scenario"></a>Webhook-Szenario

Sie können Runbooks starten, indem Sie einen POST in der Webhook-URL ausführen. Die URL sieht beispielsweise wie folgt aus: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`.

### <a name="hybrid-runbook-worker-scenario"></a>Hybrid Runbook Worker-Szenario

Sie können die Benutzerfunktion „Hybrid Runbook Worker“ von Azure Automation verwenden, um Runbooks direkt auf dem Azure- oder Nicht-Azure-Computer auszuführen. Dies schließt auch Server ein, die bei Azure Arc-fähigen Servern registriert sind. Von dem Computer oder Server aus, der die Rolle hostet, können Sie Runbooks direkt auf dem Computer und mit Ressourcen in der Umgebung ausführen, um diese lokalen Ressourcen zu verwalten.

Der Hybrid Worker nutzt einen JRDS-Endpunkt zum Starten/Beenden von Runbooks, zum Herunterladen der Runbooks auf den Worker und zum Zurücksenden des Auftragsprotokollstreams an den Automation-Dienst. Nach dem Aktivieren des JRDS-Endpunkts sieht die URL wie folgt aus: `https://<automationaccountID>.jrds.<region>.privatelink.azure-automation.net`. Dadurch wird die Runbookausführung auf dem Hybrid Worker sichergestellt, der mit Azure Virtual Network verbunden ist, sodass Aufträge ausgeführt werden können, ohne dass eine ausgehende Verbindung mit dem Internet hergestellt werden muss.  

> [!NOTE]
>Mit der aktuellen Implementierung von Private Link für Azure Automation wird nur das Ausführen von Aufträgen auf dem Hybrid Runbook Worker unterstützt, der mit einem virtuellen Azure-Netzwerk verbunden ist, aber nicht das Ausführen von Cloudaufträgen.

## <a name="hybrid-worker-scenario-for-update-management"></a>Hybrid Worker-Szenario für die Updateverwaltung  

Der Hybrid Runbook Worker des Systems unterstützt mehrere ausgeblendete Runbooks, die vom Feature Updateverwaltung verwendet werden und für die Installation von benutzerdefinierten Updates auf Windows- und Linux-Computern vorgesehen sind. Wenn die Azure Automation-Updateverwaltung aktiviert ist, werden alle mit dem Log Analytics-Arbeitsbereich verbundenen Computer automatisch als System-Hybrid Runbook Worker konfiguriert.

Weitere Informationen zum Verstehen und Konfigurieren der Updateverwaltung finden Sie unter [Informationen zur Updateverwaltung](../update-management/overview.md). Die Updateverwaltung hängt von einem Log Analytics-Arbeitsbereich ab und erfordert daher die Verknüpfung des Arbeitsbereichs mit einem Automation-Konto. Es wird ein Log Analytics-Arbeitsbereich zum Speichern der von der Lösung gesammelten Daten sowie zum Hosten der Protokollsuchvorgänge und Ansichten benötigt.

Wenn Sie Ihre für die Updateverwaltung konfigurierten Computer auf sichere Weise über den Private Link-Kanal mit Automation und einem Log Analytics-Arbeitsbereich verbinden möchten, müssen Sie Private Link für den Log Analytics-Arbeitsbereich aktivieren, der mit dem Automation-Konto verknüpft ist, das mit Private Link konfiguriert wurde.

Mithilfe der Schritte in [Konfigurieren von Log Analytics](../../azure-monitor/logs/private-link-configure.md#configure-access-to-your-resources) können Sie steuern, wie ein Log Analytics-Arbeitsbereich von außerhalb des Private Link-Bereichs erreicht werden kann. Wenn Sie **Zugriff auf öffentliche Netzwerke für Erfassung zulassen** auf **Nein** festlegen, können Computer außerhalb der verbundenen Bereiche keine Daten in diesen Arbeitsbereich hochladen. Wenn Sie **Zugriff aus öffentlichen Netzwerken für Abfragen zulassen** auf **Nein** festlegen, können Computer außerhalb der Bereiche nicht auf Daten in diesem Arbeitsbereich zugreifen.

Verwenden Sie **DSCAndHybridWorker** als Zielunterressource, um Private Link für Hybrid Worker von Benutzern und vom System zu aktivieren.

> [!NOTE]
> Computer, die außerhalb von Azure gehostet und über die Updateverwaltung verwaltet werden und die mit dem Azure-VNet über das private ExpressRoute-Peering, VPN-Tunnel oder mittels Peering verknüpfte virtuelle Netzwerke über private Endpunkte verbunden sind, unterstützen Private Link.

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration-Szenario (agentsvc)

State Configuration bietet Ihnen den Azure-Dienst für die Konfigurationsverwaltung, mit dem Sie PowerShell DSC-Konfigurationen (Desired State Configuration) schreiben, verwalten und kompilieren können – für Knoten in der Cloud oder in einem lokalen Rechenzentrum.

Der Agent auf dem Computer wird beim DSC-Dienst registriert und verwendet dann den Dienstendpunkt zum Abrufen der DSC-Konfiguration. Der Agent-Dienstendpunkt sieht wie folgt aus: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net`.

Die URL für einen öffentlichen und privaten Endpunkt wäre identisch, würde aber einer privaten IP-Adresse zugeordnet, wenn Private Link aktiviert ist.

## <a name="planning-based-on-your-network"></a>Planung basierend auf dem Netzwerk

Bevor Sie Ihre Automation-Kontoressource einrichten, überprüfen Sie Ihre Anforderungen an die Netzwerkisolation. Bewerten Sie den Zugriff Ihrer virtuellen Netzwerke auf das öffentliche Internet sowie die Zugriffsbeschränkungen für Ihr Automation-Konto (einschließlich des Einrichtens eines Private Link-Gruppenbereichs für Azure Monitor-Protokolle, falls diese in Ihr Automation-Konto integriert sind). Nehmen Sie außerdem eine Überprüfung der [DNS-Einträge](./automation-region-dns-records.md) des Automation-Diensts in Ihren Plan auf, um sicherzustellen, dass die unterstützten Features ohne Probleme funktionieren.

### <a name="connect-to-a-private-endpoint"></a>Herstellen einer Verbindung mit einem privaten Endpunkt

Erstellen Sie einen privaten Endpunkt, um eine Verbindung mit unserem Netzwerk herzustellen. Sie können ihn im [Private Link-Center des Azure-Portals](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) erstellen. Nachdem Ihre Änderungen an publicNetworkAccess und Private Link übernommen wurden, kann es bis zu 35 Minuten dauern, bis sie wirksam werden.

In diesem Abschnitt erstellen Sie einen privaten Endpunkt für Ihre Automation-Konto.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Private Link Center** aus.

2. Wählen Sie unter **Private Link-Center – Übersicht** bei der Option **Private Verbindung mit einem Dienst herstellen** den Befehl **Starten** aus.

3. Geben Sie unter **Virtuellen Computer erstellen – Grundlagen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **PROJEKTDETAILS** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. Diese haben Sie im vorherigen Abschnitt erstellt.  |
    | **INSTANZDETAILS** |  |
    | Name | Geben Sie Ihren *PrivateEndpoint* ein. |
    | Region | Wählen Sie **YourRegion** aus. |
    |||

4. Klicken Sie auf **Weiter: Ressource** aus.

5. Geben Sie unter **Privaten Endpunkt erstellen – Ressource** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    |Verbindungsmethode  | Wählen Sie das Herstellen einer Verbindung mit einer Azure-Ressource im eigenen Verzeichnis aus.|
    | Subscription| Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.Automation/automationAccounts** aus. |
    | Resource |Wählen Sie *myAutomationAccount* aus.|
    |Unterressource des Ziels |Wählen Sie je nach Ihrem Szenario *Webhook* oder *DSCAndHybridWorker* aus.|
    |||

6. Klicken Sie auf **Weiter: Konfiguration** aus.

7. Geben Sie unter **Privaten Endpunkt erstellen – Konfiguration** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    |**NETZWERK**| |
    | Virtuelles Netzwerk| Wählen Sie *MyVirtualNetwork* aus. |
    | Subnet | Wählen Sie *mySubnet* aus. |
    |**PRIVATE DNS-INTEGRATION**||
    |Integration in eine private DNS-Zone |Wählen Sie **Ja** aus. |
    |Private DNS-Zone |Wählen Sie *(New)privatelink.azure-automation.net* aus. |
    |||

8. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

9. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

Wählen Sie in **Private Link Center** die Option **Private Endpunkte** aus, um Ihre Private Link-Ressource anzuzeigen.

![Automation-Ressource von Private Link](./media/private-link-security/private-link-automation-resource.png)

Wählen Sie die Ressource aus, um alle Details anzuzeigen. Hierdurch wird ein neuer privater Endpunkt für Ihr Automation-Konto erstellt und diesem eine private IP-Adresse aus Ihrem virtuellen Netzwerk zugewiesen. Der **Verbindungsstatus** lautet **Genehmigt**.

Ähnlich wird ein eindeutiger vollqualifizierter Domänenname (Fully Qualified Domain Name, FQDN) für die State Configuration (agentsvc) und für die Hybrid Runbook Worker-Auftragslaufzeit (jrds) erstellt. Jedem davon wird von Ihrem VNet eine gesonderte IP-Adresse zugewiesen, und der **Verbindungsstatus** lautet **Genehmigt**.

Wenn der Dienstconsumer über Azure RBAC-Berechtigungen für die Automation-Ressource verfügt, kann er die Automation-Genehmigungsmethode auswählen. In diesem Fall ist keine Aktion des Dienstanbieters erforderlich, wenn die Anforderung die Automation-Anbieterressource erreicht, und die Verbindung wird automatisch genehmigt.

## <a name="set-public-network-access-flags"></a>Einrichten von Netzwerkzugriffsflags

Sie können ein Automation-Konto so konfigurieren, dass es alle öffentlichen Konfigurationen verweigert und nur Verbindungen über private Endpunkte zulässt, um die Netzwerksicherheit weiter zu erhöhen. Wenn Sie den Zugriff auf das Automation-Konto so einschränken möchten, dass nur noch aus dem VNet darauf zugegriffen werden kann, und nicht über das öffentliche Internet, können Sie die Eigenschaft `publicNetworkAccess` auf `$false` festlegen.

Wenn die Einstellung **Öffentlicher Netzwerkzugriff** auf `$false` festgelegt ist, sind nur Verbindungen über private Endpunkte zulässig, und alle Verbindungen über öffentliche Endpunkte werden mit einer Fehlermeldung zu nicht autorisiertem Zugriff und mit dem HTTP-Statuscode 401 verweigert.

Das folgende PowerShell-Skript zeigt, wie Sie die **Public Network Access**-Eigenschaft auf der Ebene des Automation-Kontos mit `Get` und `Set` abrufen und festlegen können:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Sie können über das Azure-Portal auch die Eigenschaft „Öffentlicher Netzwerkzugriff“ steuern. Wählen Sie in Ihrem Automation-Konto im linken Bereich im Abschnitt **Kontoeinstellungen** die Option **Netzwerkisolation** aus. Wenn die Einstellung „Öffentlicher Netzwerkzugriff“ auf **Nein** festgelegt ist, sind nur Verbindungen über private Endpunkte zulässig, und alle Verbindungen über öffentliche Endpunkte werden mit einer Fehlermeldung verweigert.

![Einstellung „Öffentlicher Netzwerkzugriff“](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS-Konfiguration

Wenn Sie eine Verbindung mit einer Private Link-Ressource über einen voll qualifizierten Domänennamen (FQDN) als Teil der Verbindungszeichenfolge herstellen, ist es wichtig, Ihre DNS-Einstellungen so zu konfigurieren, dass sie in die zugewiesene private IP-Adresse aufgelöst werden. Bestehende Azure-Dienste verfügen möglicherweise bereits über eine DNS-Konfiguration, die beim Herstellen einer Verbindung über einen öffentlichen Endpunkt verwendet werden kann. Ihre DNS-Konfiguration sollte überprüft und aktualisiert werden, um eine Verbindung mithilfe Ihres privaten Endpunkts herzustellen.

Die Netzwerkschnittstelle, die dem privaten Endpunkt zugeordnet ist, enthält alle Informationen, die zur Konfiguration Ihres DNS erforderlich sind, einschließlich FQDN und privater IP-Adressen, die einer bestimmten Private Link-Ressource zugewiesen wurden.

Mit den folgenden Optionen können Sie Ihre DNS-Einstellungen für private Endpunkte konfigurieren:

* Verwenden Sie die Hostdatei (wird nur für Tests empfohlen). Sie können die Hostdatei auf einem virtuellen Computer verwenden, um die Verwendung des DNS als erste Möglichkeit für die Namensauflösung außer Kraft zu setzen. Der DNS-Eintrag sollte wie im folgenden Beispiel aussehen: `privatelinkFQDN.jrds.sea.azure-automation.net`.

* Verwenden Sie eine [private DNS-Zone](../../dns/private-dns-privatednszone.md). Sie können Private DNS-Zonen verwenden, um die DNS-Auflösung für einen bestimmten privaten Endpunkt außer Kraft zu setzen. Eine private DNS-Zone kann mit Ihrem virtuellen Netzwerk verknüpft werden, um bestimmte Domänen aufzulösen. Damit der Agent auf Ihrem virtuellen Computer über den privaten Endpunkt kommunizieren kann, erstellen Sie einen Private DNS-Eintrag als `privatelink.azure-automation.net`. Fügen Sie der IP-Adresse des privaten Endpunkts einen neuen DNS *A*-Eintragszuordnung hinzu.

* Verwenden Sie Ihre DNS-Weiterleitung (optional). Sie können Ihre DNS-Weiterleitung verwenden, um die DNS-Auflösung für eine bestimmte Private Link-Ressource zu überschreiben. Wenn Ihr [DNS-Server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) in einem virtuellen Netzwerk gehostet wird, können Sie zum Verwenden einer privaten DNS-Zone eine DNS-Weiterleitungsregel erstellen, um die Konfiguration für alle Private Link-Ressourcen zu vereinfachen.

Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu privaten Endpunkten finden Sie unter [Was ist ein privater Endpunkt in Azure?](../../private-link/private-endpoint-overview.md).
