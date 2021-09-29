---
title: 'ExpressRoute: Konfigurieren benutzerdefinierter Warnungen für angekündigte Routen'
description: In diesem Artikel erfahren Sie, wie Sie mit Azure Automation und Logic Apps die Anzahl der Routen überwachen, die vom ExpressRoute-Gateway in lokalen Netzwerken angekündigt werden, um den Grenzwert von 1.000 Routen nicht zu überschreiten.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 90ce78bfab0cacbaea871402394fe8963775844c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824683"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Konfigurieren benutzerdefinierter Warnungen zum Überwachen angekündigter Routen

In diesem Artikel erfahren Sie, wie Sie mit Azure Automation und Logic Apps die Anzahl der Routen fortlaufend überwachen, die vom ExpressRoute-Gateway in lokalen Netzwerken angekündigt werden. Die Überwachung kann helfen, das Überschreiten des Grenzwerts von 1.000 Routen zu verhindern](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering).

Mit **Azure Automation** können Sie die Ausführung eines benutzerdefinierten PowerShell-Skripts, das in einem *Runbook* gespeichert ist, automatisieren. Mit der in diesem Artikel beschriebenen Konfiguration enthält das Runbook ein PowerShell-Skript, mit dem ein oder mehrere ExpressRoute-Gateways abgefragt werden. Es sammelt ein Dataset, das die Ressourcengruppe, den Namen des ExpressRoute-Gateways sowie die Anzahl der lokal angekündigten Netzwerkpräfixe enthält.

Mit **Azure Logic Apps** können Sie einen benutzerdefinierten Workflow planen, mit dem das Azure Automation-Runbook aufgerufen wird. Das Runbook wird über einen Auftrag ausgeführt. Sobald die Datensammlung ausgeführt wird, werden die Daten durch den Azure Logic Apps-Workflow klassifiziert. Basierend auf den Übereinstimmungskriterien für die Anzahl der Netzwerkpräfixe ober- oder unterhalb eines vordefinierten Schwellenwerts werden anschließend Informationen an eine Ziel-E-Mail-Adresse gesendet.

### <a name="workflow"></a><a name="workflow"></a>Workflow

Die Einrichtung einer benutzerdefinierten Warnung umfasst die folgenden drei Schritte:

1. Erstellen eines Automation-Kontos mit einem ausführenden Konto und den entsprechenden Berechtigungen

2. Erstellen und Konfigurieren von Runbooks

3. Erstellen einer Logik-App zum Auslösen des Automation-Kontos und Senden einer Warnungs-E-Mail bei Überschreiten des Schwellenwerts (von z. B. 160)

## <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

* Ihre Bereitstellung enthält mindestens ein ExpressRoute-Gateway.

* Sie verfügen über Grundkenntnisse im Umgang mit [ausführenden Konten](../automation/manage-runas-account.md) in Azure Automation.

* Sie sind mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vertraut.

* Sie sind mit Azure PowerShell vertraut. Sie benötigen Azure PowerShell, um die Netzwerkpräfixe im ExpressRoute-Gateway zu erfassen. Weitere allgemeine Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Hinweise und Einschränkungen

* Die in diesem Artikel beschriebene benutzerdefinierte Warnung ist ein Add-On zur Verbesserung des Ablaufs und der Steuerung. Sie ersetzt nicht die nativen Warnungen in ExpressRoute.
* Die Datensammlung für ExpressRoute-Gateways wird im Hintergrund ausgeführt. Der Vorgang kann länger dauern als erwartet. Richten Sie die Wiederholung des Workflows sinnvoll ein, um eine Warteschlange für Aufträge zu vermeiden.
* Bereitstellungen über Skripts oder ARM-Vorlagen werden möglicherweise schneller ausgeführt als der benutzerdefinierte Warnungstrigger. Dies kann zu einer steigenden Anzahl von Netzwerkpräfixen im ExpressRoute-Gateway und zu einer Überschreitung des Grenzwerts von 1.000 Routen führen.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Erstellen und Konfigurieren von Konten

Wenn Sie im Azure-Portal ein Automation-Konto erstellen, wird automatisch ein [ausführendes Konto](../automation/automation-security-overview.md#run-as-accounts) erstellt. Mit diesem Konto sind folgende Aktionen möglich:

* Erstellen einer Azure AD-Anwendung (Azure Active Directory) mit einem selbstsignierten Zertifikat. Das ausführende Konto selbst verfügt über ein Zertifikat, das standardmäßig jedes Jahr erneuert werden muss.

* Erstellen eines Dienstprinzipalkontos für die Anwendung in Azure AD.

* Zuweisen der Rolle „Mitwirkender“ (Azure RBAC) für das verwendete Azure-Abonnement. Mit dieser Rolle werden Azure Resource Manager-Ressourcen mithilfe von Runbooks verwaltet.

Zum Erstellen eines Automation-Kontos sind Rechte und Berechtigungen erforderlich. Weitere Informationen hierzu finden Sie unter [Erforderliche Berechtigungen zum Erstellen eines Automation-Kontos](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. Erstellen eines Automation-Kontos

Erstellen Sie ein Automation-Konto mit Berechtigungen für ausführende Konten. Eine Anleitung hierzu finden Sie unter [Erstellen eines Azure Automation-Kontos](../automation/quickstarts/create-account-portal.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Hinzufügen eines Automation-Kontos" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. Zuweisen einer Rolle zum ausführenden Konto

Standardmäßig wird dem Dienstprinzipal, der von Ihrem **ausführenden Konto** verwendet wird, die Rolle **Mitwirkender** zugewiesen. Sie können die dem Dienstprinzipal zugewiesene Standardrolle beibehalten oder die Berechtigungen einschränken, indem Sie eine [integrierte Rolle](../role-based-access-control/built-in-roles.md) (z. B. „Leser“) oder eine [benutzerdefinierte Rolle](../active-directory/roles/custom-create.md) zuweisen.

 Führen Sie die folgenden Schritte aus, um die Rolle des Dienstprinzipals festzulegen, der von Ihrem ausführenden Konto verwendet wird:

1. Navigieren Sie zu Ihrem Automation-Konto. Navigieren Sie zu **Kontoeinstellungen**, und klicken Sie auf **Ausführende Konten**.

2. Klicken Sie auf **Rollen**, um die verwendete Rollendefinition anzuzeigen.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Zuweisen einer Rolle":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Erstellen und Konfigurieren von Runbooks

### <a name="1-install-modules"></a><a name="install-modules"></a>1. Installieren von Modulen

Damit Sie PowerShell-Cmdlets in Azure Automation-Runbooks ausführen können, müssen Sie zunächst einige zusätzliche Az-Module von Azure PowerShell installieren. Führen Sie die folgenden Schritte aus, um die Module zu installieren:

1. Öffnen Sie Ihr Azure Automation-Konto, und navigieren Sie zu **Module**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Navigieren zu „Module“":::

2. Durchsuchen Sie den Katalog, und importieren Sie die folgenden Module: **Az.Accounts**, **Az.Network**, **Az.Automation** und **Az.Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Suchen nach und Importieren von Modulen" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. Erstellen eines Runbooks

1. Navigieren Sie zu Ihrem Automation-Konto, um das PowerShell-Runbook zu erstellen. Klicken Sie unter **Prozessautomatisierung** auf die Kachel **Runbooks** und anschließend auf **Runbook erstellen**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Erstellen eines Runbooks":::

2. Klicken Sie auf **Erstellen**, um das Runbook zu erstellen.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Klicken auf „Erstellen“":::

3. Wählen Sie das neu erstellte Runbook aus, und klicken Sie dann auf **Bearbeiten**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Runbook bearbeiten":::

4. Fügen Sie unter **Bearbeiten** das PowerShell-Skript ein. Sie können das [Beispielskript](#script) ändern und zur Überwachung von ExpressRoute-Gateways in einer oder mehreren Ressourcengruppen verwenden.

   Beachten Sie im Beispielskript die folgenden Einstellungen:

    * Das Array **$rgList** enthält eine Liste der Ressourcengruppen mit ExpressRoute-Gateways. Sie können die ExpressRoute-Gateways auf der Liste anpassen.
    * Mit der Variable **$thresholdNumRoutes** wird der Schwellenwert für die Anzahl der Netzwerkpräfixe definiert, die in lokalen Netzwerken von einem ExpressRoute-Gateway angekündigt werden.

#### <a name="example-script"></a><a name="script"></a>Beispielskript

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. Speichern und Veröffentlichen des Runbooks

1. Klicken Sie auf **Speichern**, um eine Entwurfskopie des Runbooks zu speichern.
2. Klicken Sie auf **Veröffentlichen**, um das Runbook als offizielle Version des Runbooks im Automation-Konto zu veröffentlichen.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Speichern und Veröffentlichen des Runbooks":::

Beim Ausführen des PowerShell-Skripts werden folgende Werte erfasst:
 
* Resource group

* Name des ExpressRoute-Gateways

* IP-Adresse des ersten BGP-Peers (peer1)

* IP-Adresse des zweiten BGP-Peers (peer2)

* Anzahl der Netzwerkpräfixe, die vom ExpressRoute-Gateway im ersten BGP-Peer (peer1) angekündigt werden

* Anzahl der Netzwerkpräfixe, die vom ExpressRoute-Gateway im zweiten BGP-Peer (peer2) angekündigt werden

* Timestamp

* Status, klassifiziert als:

  * „OK“, wenn die Anzahl der Routen unter dem Schwellenwert liegt
  * „ALERT“, wenn die Anzahl der Routen über dem Schwellenwert liegt
  * „WARNING“, wenn sich die Anzahl der Netzwerkpräfixe, die in den beiden BGP-Peers angekündigt werden, unterscheidet

* Warnmeldung für eine ausführliche Beschreibung des Status („OK“, „ALERT“, „WARNING“)

Die gesammelten Informationen werden vom PowerShell-Skript in eine JSON-Ausgabe konvertiert. Das Runbook verwendet das PowerShell-Cmdlet [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output) als Ausgabestream zur Übermittlung von Informationen an den Client.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. Überprüfen des Runbooks

Nachdem das Runbook erstellt wurde, müssen Sie es überprüfen. Klicken Sie auf **Starten**, und suchen Sie in der Ausgabe und den Fehlern nach den unterschiedlichen Auftragsdatenströmen.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Überprüfen des Runbooks" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Erstellen und Konfigurieren einer Logik-App

Mit Azure Logic Apps werden alle Sammlungs- und Aktionsprozesse orchestriert. In den folgenden Abschnitten erstellen Sie einen Workflow zur Verwendung einer Logik-App.

### <a name="workflow"></a>Workflow

Für diese Logik-App erstellen Sie einen Workflow, der ExpressRoute-Gateways regelmäßig überwacht. Wenn neue Elemente vorhanden sind, sendet der Workflow eine E-Mail für jedes Element. Am Ende entspricht der Workflow grob dem folgenden Beispiel:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps-Workflow":::

### <a name="1-create-a-logic-app"></a>1. Erstellen einer Logik-App

Erstellen Sie im **Logik-App-Designer** mithilfe der Vorlage **Leere Logik-App** eine Logik-App. Die Schritte dazu finden unter [Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Leere Vorlage":::

### <a name="2-add-a-trigger"></a>2. Hinzufügen eines Triggers

Jeder Workflow beginnt mit einem Trigger. Der Trigger wird ausgelöst, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers wird von Azure Logic Apps eine neue Workflowinstanz erstellt und ausgeführt.

Zum regelmäßigen Ausführen eines Workflows, der auf einem vordefinierten Zeitplan basiert, fügen Sie dem Workflow den integrierten Trigger **Wiederholung** hinzu. Geben Sie im Suchfeld den Suchbegriff **Zeitplan** ein. Wählen Sie das Symbol **Zeitplan** aus. Klicken Sie in der Liste Trigger auf **Wiederholung**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Wiederholung – Zeitplan":::

Im Trigger „Wiederholung“ können Sie die Zeitzone und eine Wiederholung für den Workflow festlegen. Das Intervall und die Häufigkeit definieren zusammen den Zeitplan für den Trigger des Workflows. Berücksichtigen Sie die folgenden Aspekte, um eine sinnvolle Mindesthäufigkeit für die Wiederholungen festzulegen:

* Die Ausführung des PowerShell-Skripts im Automation-Runbook nimmt einige Zeit in Anspruch. Die Laufzeit hängt von der Anzahl der ExpressRoute-Gateways ab, die überwacht werden sollen. Ist die Wiederholungshäufigkeit zu hoch, landen die Aufträge in der Warteschlange.

* Das PowerShell-Skript wird als Auftrag im Hintergrund ausgeführt. Dieser wird nicht sofort, sondern erst nach einer variablen Verzögerung gestartet.

* Ist die Wiederholungshäufigkeit zu hoch, führt dies zu einer unnötigen Auslastung der Azure ExpressRoute-Gateways.

Nach Abschluss der Workflowkonfiguration können Sie die Konsistenz der Wiederholungshäufigkeit überprüfen, indem Sie den Workflow einige Male ausführen. Überprüfen Sie anschließend das Ergebnis im **Ausführungsverlauf**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Der Screenshot zeigt die Werte für Wiederholungsintervall und -häufigkeit an." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. Erstellen eines Auftrags

Ein Logik-App-Workflow greift über Connectors auf andere Apps, Dienste und die Plattform zu. Im nächsten Schritt wird ein Connector ausgewählt, über den Sie auf das zuvor definierte Azure Automation-Konto zugreifen.

1. Klicken Sie im **Logik-App-Designer** unter **Wiederholung** auf **Neuer Schritt**. Klicken Sie unter **Aktion auswählen** und unterhalb des Suchfelds auf die Option **Alle**.
2. Geben Sie im Suchfeld den Suchbegriff **Azure Automation** ein, und starten Sie die Suche. Klicken Sie auf **Auftrag erstellen**. Mit der Aktion **Auftrag erstellen** wird das zuvor erstellte Automation-Runbook ausgelöst.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Auftrag erstellen":::

3. Melden Sie sich mithilfe eines Dienstprinzipals an. Dazu können Sie einen vorhandenen Dienstprinzipal verwenden oder einen neuen erstellen. Eine Anleitung zum Erstellen eines neuen Dienstprinzipals finden Sie unter [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../active-directory/develop/howto-create-service-principal-portal.md). Klicken Sie auf **Verbindung über Dienstprinzipal herstellen**.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Screenshot, der den Abschnitt „Serie“ mit hervorgehobener Aktion „Verbindung über Dienstprinzipal herstellen“ zeigt.":::

4. Geben Sie einen **Verbindungsnamen**, Ihre **Client-ID** (Anwendungs-ID), Ihren **geheimen Clientschlüssel** und Ihre **Mandanten-ID** ein. Wählen Sie anschließend **Erstellen**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Verbindung über Dienstprinzipal herstellen":::

5. Überprüfen Sie auf der Seite **Auftrag erstellen**, ob der Dienstprinzipal für die **Ressourcengruppe**, die das Automation-Konto hostet, über die Rolle „Leser“ verfügt sowie für das **Automation-Konto** über die Rolle „Automation-Auftragsoperator“. Vergewissern Sie sich zudem, dass Sie **Name des Runbooks** als neuen Parameter hinzugefügt haben.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Der Screenshot zeigt Werte für „Auftrag erstellen“ unter „Wiederholung“ an, wo Sie den Runbooknamen überprüfen können." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. Abrufen der Auftragsausgabe

1. Wählen Sie **Neuer Schritt** aus. Geben Sie im Suchfeld den Suchbegriff „Azure Automation“ ein. Wählen Sie in der Liste **Aktionen** die Aktion **Auftragsausgabe abrufen** aus.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Auftragsausgabe abrufen":::

2. Geben Sie auf der Seite **Auftragsausgabe abrufen** die erforderlichen Informationen für den Zugriff auf das Automation-Konto an. Wählen Sie unter **Abonnement, Ressourcengruppe** und **Automation-Konto** jeweils die gewünschte Option aus. Klicken Sie in das Feld **Auftrags-ID**. Wählen Sie in der angezeigten Liste **Dynamischer Inhalt** die Option **Auftrags-ID** aus.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Auftrags-ID" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. Analysieren des JSON-Elements

Über die Informationen, die in der Ausgabe der Azure Automation-Aktion „Auftrag erstellen“ (vorherige Schritte) enthalten sind, wird ein JSON-Element generiert. Mit der integrierten Aktion **JSON analysieren** werden benutzerfreundliche Token aus den Eigenschaften und deren Werten im JSON-Element erstellt. Diese Eigenschaften können Sie für Ihren Workflow verwenden.

1. Fügen Sie eine Aktion hinzu. Klicken Sie unter der Aktion **Auftragsausgabe abrufen** auf **Neuer Schritt**.
2. Geben Sie im Suchfeld unter **Aktion auswählen** den Suchbegriff „JSON analysieren“ ein, um nach Connectors für diese Aktion zu suchen. Wählen Sie in der Liste **Aktionen** die Aktion **JSON analysieren** für die Datenvorgänge aus, die Sie verwenden möchten.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="JSON analysieren":::

3. Klicken Sie in das Feld **Inhalt**. Klicken Sie in der angezeigten Liste „Dynamischer Inhalt“ auf die Option **Inhalt**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Der Screenshot zeigt das Dialogfeld „JSON analysieren“ mit ausgewählter Option „Inhalt“ an." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Die JSON-Analyse erfordert ein Schema. Sie können das Schema mithilfe der Ausgabe des Automation-Runbooks generieren. Öffnen Sie eine neue Webbrowsersitzung, führen Sie das Automation-Runbook aus, und rufen Sie die Ausgabe ab. Wechseln Sie zurück zur Logic Apps-Aktion **JSON-Datenvorgänge analysieren**. Klicken Sie unten auf der Seite auf **Beispielnutzlast zum Generieren eines Schemas verwenden**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Beispielnutzlast zum Generieren eines Schemas verwenden":::

5. Fügen Sie unter **Geben oder fügen Sie eine JSON-Beispielnutzlast ein** die Ausgabe des Automation-Runbooks ein, und klicken Sie auf **Fertig**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Einfügen der Beispielnutzlast" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Über die Analyse der JSON-Eingabenutzlast wird automatisch ein Schema generiert.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Generieren eines Schemas" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. Definieren und Initialisieren einer Variable

In diesem Schritt des Workflows erstellen Sie eine Bedingung für das Senden einer Warnungs-E-Mail. Damit der Nachrichtentext der E-Mail flexibel angepasst werden kann, wird eine zusätzliche Variable in den Workflow eingefügt.

1. Klicken Sie unter der Aktion **Auftragsausgabe abrufen** auf **Neuer Schritt**. Suchen Sie über das Suchfeld nach **Variablen**, und wählen Sie die Option aus.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Der Screenshot zeigt das Dialogfeld „Aktion auswählen“ mit Variable im Suchfeld und ausgewählter Option „Variablen“.":::

2. Wählen Sie in der Liste **Aktionen** die Aktion **Variable initialisieren** aus.

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Variablen initialisieren":::

3. Geben Sie den Namen der Variable an. Wählen Sie unter **Typ** die Option **Zeichenfolge** aus. Der **Wert** für die Variable wird später im Workflow zugewiesen.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Der Screenshot zeigt „JSON analysieren“ in Verbindung mit „Variable initialisieren“ an, wo Sie einen Namen, einen Typ und einen Wert eingeben können." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. Erstellen einer ForEach-Aktion

Nach der JSON-Analyse wird der Inhalt der *Text*-Ausgabe durch die Aktion **JSON-Datenvorgänge analysieren** gespeichert. Damit Sie die Ausgabe verarbeiten können, erstellen Sie eine ForEach-Schleife, durch die eine oder mehrere Aktionen für jedes Element im Array wiederholt werden.

1. Klicken Sie unter **Variable initialisieren** auf **Aktion hinzufügen**. Geben Sie im Suchfeld die Zeichenfolge „for each“ als Filter ein.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Der Screenshot zeigt das Dialogfeld „Aktion auswählen“ mit „for each“ im Suchfeld sowie mit ausgewählter Steuerung an.":::

2. Wählen Sie in der Liste **Aktionen** die Aktion **ForEach – Steuerung** aus.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="ForEach – Steuerung":::

3. Klicken Sie in das Textfeld **Ausgabe von vorherigen Schritten auswählen**. Klicken Sie in der angezeigten Liste **Dynamischer Inhalt** auf die Option **Text**, die die Ausgabe des analysierten JSON-Elements darstellt.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Der Screenshot zeigt die „For each“ zugeordnete initialisierte Variable an, die das Textfeld „Ausgabe von vorherigen Schritten auswählen“ enthält.":::

4. Für jedes Element des JSON-Texts soll eine Bedingung festgelegt werden. Wählen Sie aus der Gruppe der Aktionen die Aktion **Steuerung** aus.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Steuerung":::

5. Wählen Sie in der Liste **Aktionen** die Option **Bedingung – Steuerung** aus. Mit der Kontrollstruktur „Bedingung – Steuerung“ werden die Daten in Ihrem Workflow mit bestimmten Werten oder Feldern verglichen. Sie können nun verschiedene Aktionen angeben, die nur dann ausgeführt werden, wenn die Daten die Bedingung erfüllen.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Bedingung – Steuerung":::

6. Ändern Sie im Stammverzeichnis der Aktion **Bedingung** den logischen Operator in **OR**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Oder" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Überprüfen Sie den Wert für die Anzahl der Netzwerkpräfixe, die von einem ExpressRoute-Gateway in den beiden BGP-Peers angekündigt werden. Die Anzahl der Routen wird unter **Dynamischer Inhalt** „numRoutePeer1“ und „numRoutePeer2“ angezeigt. Geben Sie im Feld „Wert“ den Wert für **numRoutePeer1** ein.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. Wenn Sie der Bedingung eine weitere Zeile hinzufügen möchten, klicken Sie auf **Hinzufügen > Zeile hinzufügen**. Klicken Sie unter **Dynamischer Inhalt** für das zweite Feld auf **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. Die logische Bedingung ist TRUE, wenn eine der beiden dynamischen Variablen „numRoute1“ oder „numRoute2“ über dem Schwellenwert liegt. In diesem Beispiel ist der Schwellenwert auf 800 festgelegt (was 80 % des Grenzwerts von 1.000 Routen entspricht). Sie können den Schwellenwert an Ihre Anforderungen anpassen. Aus Gründen der Konsistenz sollte dieser Wert mit dem Wert übereinstimmen, der im PowerShell-Skript des Runbooks verwendet wird.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Logische Bedingung":::

10. Unter **Wenn TRUE** können Sie die Aktionen zum Senden der Warnungs-E-Mail formatieren und erstellen. Suchen Sie unter „Aktion auswählen“ nach **Variablen**, und wählen Sie die Option aus.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Wenn TRUE":::

11. Klicken Sie unter „Variablen“ auf **Aktion hinzufügen**. Wählen Sie in der Liste **Aktionen** die Aktion **Variable festlegen** aus.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Screenshot des Abschnitts „Variablen“ mit ausgewählter Registerkarte „Aktionen“ und hervorgehobener Aktion „Variable festlegen“.":::

12. Wählen Sie für **Name** die Variable mit dem Namen **EmailBody** aus, die Sie zuvor erstellt haben. Fügen Sie für **Wert** das HTML-Skript ein, das zum Formatieren der Warnungs-E-Mail erforderlich ist. Fügen Sie über **Dynamischer Inhalt** die Werte des JSON-Texts ein. Nachdem Sie diese Einstellungen konfiguriert haben, enthält die Variable **EmailBody** alle Informationen zu der Warnung im HTML-Format.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Variable festlegen":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. Hinzufügen des E-Mail-Connectors

Logic Apps stellt viele E-Mail-Connectors bereit. In diesem Beispiel fügen Sie einen Outlook-Connector zum Senden der Warnungs-E-Mail hinzu. Klicken Sie unter **Variable festlegen** auf **Aktion hinzufügen**. Geben Sie unter **Aktion auswählen** im Suchfeld den Suchbegriff „E-Mail senden“ ein.

1. Wählen Sie **Office 365 Outlook** aus.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Senden von E-Mails":::

2. Wählen Sie in der Liste **Aktionen** die Option **E-Mail senden (V2)** aus.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="E-Mail senden (V2)":::

3. Melden Sie sich an, um eine Verbindung mit Office 365 Outlook herzustellen.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Anmelden":::

4. Klicken Sie unterhalb des Felds **Text** auf **Dynamischen Inhalt hinzufügen**. Fügen Sie die Variable **EmailBody** aus dem Bereich „Dynamischer Inhalt“ hinzu. Füllen Sie die Felder **Betreff** und **An** aus.

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Text":::

5. Mit der Aktion **E-Mail senden (V2)** wird die Einrichtung des Workflows abgeschlossen.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="E-Mail senden (V2)" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. Überprüfen des Workflows

Der letzte Schritt besteht nun in der Überprüfung des Workflows. Klicken Sie in der **Logic Apps-Übersicht** auf **Trigger ausführen**. Klicken Sie auf **Wiederholung**. Im **Ausführungsverlauf** können Sie den Workflow überwachen und überprüfen.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Trigger ausführen":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Anpassen des Workflows finden Sie unter [Azure Logic Apps](../logic-apps/logic-apps-overview.md).