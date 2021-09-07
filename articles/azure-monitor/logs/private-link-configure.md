---
title: Konfigurieren von Private Link
description: Konfigurieren von Private Link
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: cdbfd69da09bb3f55f0cdf87bb545e2c6bb40a79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339101"
---
# <a name="configure-your-private-link"></a>Konfigurieren von Private Link
Zum Konfigurieren von Private Link sind einige Schritte erforderlich: 
* Erstellen eines Private Link-Bereichs mit Ressourcen
* Erstellen eines privaten Endpunkts für Ihr Netzwerk und Verbinden des Endpunkts mit dem Bereich
* Konfigurieren des erforderlichen Zugriffs für Ihre Azure Monitor-Ressourcen

Im vorliegenden Artikel wird erläutert, wie Sie diese Schritte über das Azure-Portal ausführen, und es wird eine ARM-Beispielvorlage (Azure Resource Manager) zum Automatisieren des Verfahrens bereitgestellt. 

## <a name="create-a-private-link-connection"></a>Erstellen einer Private Link-Verbindung

Erstellen Sie als Erstes eine Ressource für einen Azure Monitor Private Link-Bereich.

1. Wechseln Sie im Azure-Portal zu **Ressource erstellen**, und suchen Sie nach **Azure Monitor Private Link-Bereich**.

   ![Suchen des Azure Monitor-Private Link-Bereichs](./media/private-link-security/ampls-find-1c.png)

2. Klicken Sie auf **Erstellen**.
3. Wählen Sie ein Abonnement und eine Ressourcengruppe aus.
4. Benennen Sie den Azure Monitor Private Link-Bereich. Es empfiehlt sich, einen aussagekräftigen Namen wie „AppServerProdTelem“ zu verwenden.
5. Klicken Sie auf **Überprüfen + erstellen**. 

   ![Erstellen des Azure Monitor-Private Link-Bereichs](./media/private-link-security/ampls-create-1d.png)

6. Warten Sie die Überprüfung ab, und wählen Sie dann **Erstellen** aus.

### <a name="connect-azure-monitor-resources"></a>Verbinden von Azure Monitor-Ressourcen

Stellen Sie eine Verbindung zwischen Azure Monitor-Ressourcen (Log Analytics-Arbeitsbereiche und Application Insights-Komponenten) und Ihrem Azure Monitor-Private Link-Bereich her.

1. Wählen Sie in Ihrem Azure Monitor Private Link-Bereich im Menü links **Azure Monitor-Ressourcen** aus. Wählen Sie die Schaltfläche **Hinzufügen** aus.
2. Fügen Sie den Arbeitsbereich oder die Komponente hinzu. Durch Auswahl der Schaltfläche **Hinzufügen** wird ein Dialogfeld geöffnet, in dem Sie Azure Monitor-Ressourcen auswählen können. Sie können Ihre Abonnements und Ressourcengruppen durchsuchen oder zum Filtern den entsprechenden Namen eingeben. Wählen Sie den Arbeitsbereich bzw. die Komponente und dann **Anwenden** aus, um die entsprechende Ressource dem Bereich hinzuzufügen.

    ![Screenshot der Benutzeroberfläche zur Auswahl eines Bereichs](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Das Löschen von Azure Monitor-Ressourcen erfordert, dass Sie diese zunächst von allen AMPLS-Objekten trennen, mit denen sie verbunden sind. Es ist nicht möglich, Ressourcen zu löschen, die mit einem AMPLS verbunden sind.

### <a name="connect-to-a-private-endpoint"></a>Herstellen einer Verbindung mit einem privaten Endpunkt

Nachdem Sie Ihre Ressource mit dem Azure Monitor Private Link-Bereich verbunden haben, erstellen Sie jetzt einen privaten Endpunkt für die Verbindung mit dem Netzwerk. Diesen Schritt können Sie im [Private Link-Center im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) oder – wie im gezeigten Beispiel – im Azure Monitor Private Link-Bereich ausführen.

1. Wählen Sie in Ihrer Bereichsressource im Ressourcenmenü links **Private Endpunktverbindungen** aus. Wählen Sie **Privater Endpunkt** aus, um den Prozess zum Erstellen des Endpunkts zu starten. Sie können hier auch Verbindungen genehmigen, die im Private Link-Center gestartet wurden, indem Sie die entsprechende Verbindung und dann **Genehmigen** auswählen.

    ![Screenshot der Benutzeroberfläche für Verbindungen mit privaten Endpunkten](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Wählen Sie das Abonnement, die Ressourcengruppe, den Namen des Endpunkts sowie die Region aus, in der sich der Endpunkt befinden soll. Die Region muss mit der Region des VNET übereinstimmen, mit dem Sie die Verbindung herstellen.

3. Klicken Sie auf **Weiter: Ressource** aus. 

4. Gehen Sie auf dem Bildschirm „Ressource“ folgendermaßen vor:

   a. Wählen Sie das **Abonnement** aus, das Ihre Ressource für den Azure Monitor Private Link-Bereich enthält. 

   b. Wählen Sie als **Ressourcentyp** die Option **Microsoft.insights/privateLinkScopes** aus. 

   c. Wählen Sie aus der Dropdownliste **Ressource** den Private Link-Bereich aus, den Sie weiter oben erstellt haben. 

   d. Klicken Sie auf **Weiter: Konfiguration >** .
      ![Screenshot der Auswahlmöglichkeiten auf dem Bildschirm „Privaten Endpunkt erstellen“](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. Gehen Sie im Bereich „Konfiguration“ folgendermaßen vor:

   a.    Wählen Sie das **virtuelle Netzwerk** und das **Subnetz** aus, mit dem Sie Ihre Azure Monitor-Ressourcen verbinden möchten. 
 
   b.    Wählen Sie für **In private DNS-Zone integrieren** die Option **Ja** aus, und lassen Sie die neue private DNS-Zone automatisch erstellen. Die tatsächlichen DNS-Zonen können von den im folgenden Screenshot dargestellten Werten abweichen. 
   > [!NOTE]
   > Wenn Sie **Nein** wählen und die manuelle Verwaltung von DNS-Einträgen bevorzugen, müssen Sie zuerst die Einrichtung Ihrer Private Link-Instanz einschließlich dieses privaten Endpunkts und der AMPLS-Konfiguration abschließen. Konfigurieren Sie anschließend Ihr DNS gemäß den Anweisungen unter [DNS-Konfiguration für private Azure-Endpunkte ](../../private-link/private-endpoint-dns.md). Stellen Sie sicher, dass Sie keine leeren Datensätze als Vorbereitung für Ihre Private Link-Einrichtung erstellen. Die von Ihnen erstellten DNS-Einträge können bestehende Einstellungen überschreiben und Ihre Konnektivität mit Azure Monitor beeinträchtigen.
 
   c.    Klicken Sie auf **Überprüfen + erstellen**.
 
   d.    Warten Sie die Überprüfung ab. 
 
   e.    Wählen Sie **Erstellen** aus. 

    ![Screenshot der ausgewählten Details für den privaten Endpunkt](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nun haben Sie einen neuen privaten Endpunkt erstellt, der mit dieser AMPLS-Instanz verbunden ist.


## <a name="configure-access-to-your-resources"></a>Konfigurieren des Zugriffs auf Ihre Ressourcen
Bisher wurde auf die Konfiguration Ihres Netzwerks eingegangen, aber Sie müssen auch überlegen, wie Sie den Netzwerkzugriff auf Ihre überwachten Ressourcen konfigurieren möchten: Log Analytics-Arbeitsbereiche und Application Insights-Komponenten.

Öffnen Sie das Azure-Portal. Im Ressourcenmenü auf der linken Seite befindet sich ein Menüelement mit dem Namen **Netzwerkisolation**. Diese Seite steuert, welche Netzwerke über Private Link auf die Ressource zugreifen können und ob andere Netzwerke mit der Ressource kommunizieren dürfen.


> [!NOTE]
> Ab dem 16. August 2021 erfolgt eine strikte Erzwingung der Netzwerkisolation. Ressourcen, die so konfiguriert sind, dass sie Abfragen aus öffentlichen Netzen blockieren, und die nicht über einen Azure Monitor-Private Link-Bereich (AMPLS) mit einem privaten Netzwerk verbunden sind, akzeptieren keinerlei Abfragen aus beliebigen Netzwerken.

![Log Analytics-Netzwerkisolation](./media/private-link-security/ampls-network-isolation.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Verbundene Azure Monitor-Private Link-Bereiche
Hier können Sie die Ressourcenverbindungen mit Azure Monitor-Private Link-Bereichen überprüfen und konfigurieren. Durch das Herstellen einer Verbindung mit Azure Monitor-Private Link-Bereichen kann Datenverkehr von einem virtuellen Netzwerk, das mit einem AMPLS verbundenen ist, an die Ressource fließen. Dieser Vorgang hat den gleichen Effekt wie das Herstellen einer Verbindung aus dem Bereich, wie unter [Herstellen einer Verbindung mit Azure Monitor-Ressourcen](#connect-azure-monitor-resources) beschrieben wurde. 

Zum Hinzufügen einer neuen Verbindung wählen Sie **Hinzufügen** und den Azure Monitor Private Link-Bereich aus. Wählen Sie **Übernehmen** aus, um die Verbindung damit herzustellen. Ihre Ressource kann eine Verbindung mit fünf AMPLS-Objekten herstellen, wie unter [Berücksichtigen von AMPLS-Limits](./private-link-design.md#consider-ampls-limits) beschrieben.

### <a name="virtual-networks-access-configuration---managing-access-from-outside-of-private-links-scopes"></a>Zugriffskonfiguration für virtuelle Netzwerke: Verwalten des Zugriffs von außerhalb eines Private Link-Bereichs
Die Einstellungen im unteren Bereich dieser Seite steuern den Zugriff über öffentliche Netzwerke – also über Netzwerke, die nicht über die aufgeführten Bereiche (AMPLSs) verbunden sind.

Wenn Sie **Zugriff auf öffentliche Netzwerke für Erfassung zulassen** auf **Nein** festlegen, können Clients (Computer, SDKs usw.) außerhalb der verbundenen Bereiche keine Daten hochladen oder Protokolle an die Ressource senden.

Wenn Sie **Zugriff auf öffentliche Netzwerke für Abfragen zulassen** auf **Nein** festlegen, können Clients (Computer, SDKs usw.) außerhalb der verbundenen Bereiche keine Daten in der Ressource abfragen. Dies schließt den Zugriff auf Protokolle, Metriken, den Livemetrikstream sowie darauf aufsetzende Funktionalität ein, wie z. B. Arbeitsmappen, Dashboards, auf Abfrage-APIs basierende Clientfunktionen, Erkenntnisse im Azure-Portal und vieles mehr. Funktionen außerhalb des Azure-Portals und solche, die Log Analytics-Daten abfragen, müssen auch innerhalb des über Private Link verbundenen virtuellen Netzwerks ausgeführt werden.


### <a name="exceptions"></a>Ausnahmen

#### <a name="diagnostic-logs"></a>Diagnoseprotokolle
Protokolle und Metriken, die über [Diagnoseeinstellungen](../essentials/diagnostic-settings.md) in einen Arbeitsbereich hochgeladen werden, werden über einen sicheren privaten Microsoft-Kanal geleitet und nicht durch diese Einstellungen gesteuert.

#### <a name="azure-resource-manager"></a>Azure Resource Manager
Die Einschränkung des Zugriffs in der oben beschriebenen Weise gilt für Daten in der Ressource. Konfigurationsänderungen – einschließlich Aktivierung und Deaktivierung dieser Zugriffseinstellungen – werden jedoch von Azure Resource Manager verwaltet. Zum Steuern dieser Einstellungen sollten Sie den Zugriff auf Ressourcen mithilfe geeigneter Rollen, Berechtigungen, Netzwerkkontrollen und Überwachungsfunktionen einschränken. Weitere Informationen finden Sie unter [Rollen, Berechtigungen und Sicherheit in Azure Monitor](../roles-permissions-security.md).

Darüber hinaus fragen bestimmte Funktion (beispielsweise der LogicApp-Connector, die Lösung zur Updateverwaltung und das Blatt „Arbeitsbereichszusammenfassung“ im Portal, auf dem das Lösungsdashboard angezeigt wird) Daten über Azure Resource Manager ab und können daher nur dann Daten abfragen, wenn Private Link-Einstellungen auch auf Resource Manager angewendet werden.


## <a name="review-and-validate-your-private-link-setup"></a>Überprüfen und Validieren Ihres Private Link-Setups

### <a name="reviewing-your-endpoints-dns-settings"></a>Überprüfen der DNS-Einstellungen Ihres Endpunkts
Für den von Ihnen erstellten privaten Endpunkt sollten nun fünf DNS-Zonen konfiguriert sein:

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> Jede dieser Zonen ordnet bestimmte Azure Monitor-Endpunkte privaten IP-Adressen aus dem IP-Adresspool des VNET zu. Die IP-Adressen, die in den folgenden Abbildungen gezeigt werden, sind lediglich Beispiele. Ihre Konfiguration sollte stattdessen private IP-Adressen aus Ihrem eigenen Netzwerk enthalten.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Diese Zone deckt die globalen Endpunkte ab, die von Azure Monitor verwendet werden. Das bedeutet, dass diese Endpunkte Anforderungen verarbeiten, bei denen alle Ressourcen und nicht nur eine bestimmte Ressource berücksichtigt werden. Dieser Zone sollten folgende Endpunkte zugeordnet sein:
* `in.ai`: Application Insights-Erfassungsendpunkt (globaler und regionaler Eintrag)
* `api`: Application Insights- und Log Analytics-API-Endpunkt
* `live`: Application Insights-Livemetrikendpunkt
* `profiler`: Application Insights-Profiler-Endpunkt
* `snapshot`: Application Insights-Momentaufnahmenendpunkt [ ![ Screenshot der privaten DNS-Zone „monitor-azure-com“](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Diese Zone deckt die arbeitsbereichsspezifische Zuordnung zu OMS-Endpunkten ab. Es sollte ein Eintrag für jeden Arbeitsbereich angezeigt werden, der mit dem mit diesem privaten Endpunkt verbundenen AMPLS verknüpft ist.
[![Screenshot der privaten DNS-Zone „oms-opinsights-azure-com“](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Diese Zone deckt die arbeitsbereichsspezifische Zuordnung zu ODS-Endpunkten ab – dem Erfassungsendpunkt von Log Analytics. Es sollte ein Eintrag für jeden Arbeitsbereich angezeigt werden, der mit dem mit diesem privaten Endpunkt verbundenen AMPLS verknüpft ist.
[![Screenshot der privaten DNS-Zone „ods-opinsights-azure-com“](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Diese Zone deckt die arbeitsbereichsspezifische Zuordnung zu den Automatisierungsendpunkten des Agent-Diensts ab. Es sollte ein Eintrag für jeden Arbeitsbereich angezeigt werden, der mit dem mit diesem privaten Endpunkt verbundenen AMPLS verknüpft ist.
[![Screenshot der privaten DNS-Zone „agentsvc-azure-automation-net“](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
Diese Zone konfiguriert die Konnektivität mit dem Speicherkonto der Lösungspakete der globalen Agents. Dadurch können Agents neue oder aktualisierte Lösungspakete (auch Management Packs genannt) herunterladen. Für die Behandlung von Log Analytics-Agents ist unabhängig von der Anzahl der verwendeten Arbeitsbereiche nur ein einzelner Eintrag erforderlich.
[![Screenshot: Private DNS-Zone „blob-core-windows-net“](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Dieser Eintrag wird nur den Private Links-Setups hinzugefügt, die am oder nach dem 19. April 2021 (oder ab Juni 2021 in Azure Sovereign Clouds) erstellt wurden.


### <a name="validating-you-are-communicating-over-a-private-link"></a>Überprüfen der Kommunikation über Private Link
* Um zu überprüfen, ob Ihre Anforderungen nun über den privaten Endpunkt gesendet werden, können Sie diese mit einem Tool für die Netzwerknachverfolgung oder sogar mit Ihrem Browser überprüfen. Wenn Sie beispielsweise versuchen, Ihren Arbeitsbereich oder Ihre Anwendung abzufragen, stellen Sie sicher, dass die Anforderung an die private IP-Adresse gesendet wird, die dem API-Endpunkt zugeordnet ist (in diesem Beispiel *172.17.0.9*).

    Hinweis: In einigen Browsern können andere DNS-Einstellungen verwendet werden (siehe [Browser-DNS-Einstellungen](./private-link-design.md#browser-dns-settings)). Stellen Sie sicher, dass Ihre DNS-Einstellungen gültig sind.

* Um sicherzustellen, dass Ihr Arbeitsbereich oder Ihre Komponente keine Anforderungen von öffentlichen Netzwerken (die nicht über AMPLS verbunden sind) empfängt, legen Sie die Ressourcenflags für öffentliche Erfassung und Abfragen auf *Nein* fest, wie unter [Konfigurieren des Zugriffs auf Ihre Ressourcen](#configure-access-to-your-resources) erläutert.

* Verwenden Sie von einem Client in Ihrem geschützten Netzwerk aus `nslookup` für einen in Ihren DNS-Zonen aufgelisteten Endpunkt. Dieser sollte von Ihrem DNS-Server in die zugeordnete private IP-Adresse, anstelle der standardmäßig verwendeten öffentlichen IP-Adresse aufgelöst werden.


## <a name="use-apis-and-command-line"></a>Verwenden von APIs und der Befehlszeile

Sie können den oben beschriebenen Prozess mithilfe von Azure Resource Manager-Vorlagen, REST- und Befehlszeilenschnittstellen automatisieren.

Zum Erstellen und Verwalten von Private Link-Bereichen verwenden Sie die [REST-API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) oder die [Azure CLI (az monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

Verwenden Sie zum Verwalten des Netzwerkzugriffs für Ihren Arbeitsbereich oder Ihre Komponente die Flags `[--ingestion-access {Disabled, Enabled}]` und `[--query-access {Disabled, Enabled}]` in [Log Analytics-Arbeitsbereichen](/cli/azure/monitor/log-analytics/workspace) oder [Application Insights-Komponenten](/cli/azure/ext/application-insights/monitor/app-insights/component).

### <a name="example-azure-resource-manager-template-arm-template"></a>Azure Resource Manager-Beispielvorlage (ARM-Vorlage)
Mit der nachstehenden Azure Resource Manager-Vorlage werden folgende Aufgaben ausgeführt:
* Erstellen eines Private Link-Bereichs (AMPLS) mit dem Namen „my-scope“
* Erstellen eines Log Analytics-Arbeitsbereichs mit dem Namen „my-workspace“
* Hinzufügen einer bereichsspezifischen Ressource für den AMPLS „my-scope“ mit dem Namen „my-workspace-connection“

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2019-10-17-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {}
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [privaten Speicher](private-storage.md).
- Weitere Informationen zu [Private Link für Automation](../../automation/how-to/private-link-security.md)