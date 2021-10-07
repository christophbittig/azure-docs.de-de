---
title: Beheben von Problemen mit der Sicherheit und Zugriffssteuerung
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit der Sicherheit und Zugriffskontrolle in Azure Data Factory und Synapse Analytics beheben.
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: lle
ms.openlocfilehash: 990fc37f08116aad5b576e2e207aa04913795894
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814973"
---
# <a name="troubleshoot-azure-data-factory-and-synapse-analytics-security-and-access-control-issues"></a>Fehlerbehebung bei Sicherheits- und Zugriffskontrollproblemen in Azure Data Factory und Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden gängige Methoden zur Fehlerbehebung für Sicherheit und Zugriffskontrolle in Azure Data Factory- und Synapse Analytics-Pipelines untersucht.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Konnektivitätsprobleme in der Kopieraktivität des Clouddatenspeichers

#### <a name="symptoms"></a>Symptome

Möglicherweise werden verschiedene Fehlermeldungen zurückgegeben, wenn Konnektivitätsprobleme im Quellen- oder Senkendatenspeicher auftreten.

#### <a name="cause"></a>Ursache 

Das Problem wird in der Regel durch einen der folgenden Faktoren verursacht:

* Die Proxyeinstellung im Knoten der selbstgehosteten Integration Runtime (IR), wenn Sie eine selbstgehostete IR verwenden.

* Die Firewalleinstellung im Knoten der selbstgehosteten Integration Runtime (IR), wenn Sie eine selbstgehostete IR verwenden.

* Die Firewalleinstellung im Clouddatenspeicher.

#### <a name="resolution"></a>Lösung

* Überprüfen Sie die folgenden Punkte, um sicherzustellen, dass ein Konnektivitätsproblem vorliegt:

   - Der Fehler wird von den Quellen- oder Senkenconnectors ausgelöst.
   - Der Fehler tritt am Anfang der Kopieraktivität auf.
   - Der Fehler ist für Azure IR oder die selbstgehostete IR mit einem Knoten konsistent, da es möglicherweise ein zufälliger Fehler in einer selbstgehosteten IR mit mehreren Knoten ist, wenn das Problem nur bei einigen der Knoten auftritt.

* Wenn Sie eine **selbstgehostete IR** verwenden, überprüfen Sie Ihre Proxy-, Firewall- und Netzwerkeinstellungen, da eine Verbindung mit dem gleichen Datenspeicher erfolgreich hergestellt werden könnte, wenn Sie eine Azure IR verwenden. Informationen zur Problembehandlung in diesem Szenario finden Sie hier:

   * [Ports und Firewalls für die selbstgehostete IR](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Kopieren von Daten nach und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory](./connector-azure-data-lake-store.md)
  
* Wenn Sie eine **Azure IR** verwenden, versuchen Sie, die Firewalleinstellung des Datenspeichers zu deaktivieren. Diese Vorgehensweise kann die Probleme in den folgenden zwei Situationen beheben:
  
   * [IP-Adressen von Azure Integration Runtime](./azure-integration-runtime-ip-addresses.md) sind nicht in der Zulassungsliste enthalten.
   * Das Feature *Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben* ist für [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) und [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities) deaktiviert.
   * Die Einstellung *Zugriff auf Azure-Dienste zulassen* ist nicht für Azure Data Lake Storage Gen1 aktiviert.

Wenn keine der vorherigen Methoden funktioniert, wenden Sie sich an Microsoft, um Hilfe zu erhalten.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problem durch ungültigen oder leeren Authentifizierungsschlüssel nach Deaktivierung des Zugriffs über das öffentliche Netzwerk

#### <a name="symptoms"></a>Symptome

Nachdem Sie den öffentlichen Netzwerkzugriff für den Dienst deaktiviert haben, gibt die selbst gehostete Integrationslaufzeit den folgenden Fehler aus: "Der Authentifizierungsschlüssel ist ungültig oder leer.”

#### <a name="cause"></a>Ursache

Das Problem wird wahrscheinlich durch ein Problem bei der Domain Name System-Auflösung (DNS) verursacht, da die Deaktivierung der öffentlichen Konnektivität und das Einrichten eines privaten Endpunkts eine erneute Verbindung verhindert.

Um zu überprüfen, ob der vollständig qualifizierte Domänenname (FQDN) des Dienstes in die öffentliche IP-Adresse aufgelöst wird, gehen Sie wie folgt vor:

1. Vergewissern Sie sich, dass Sie die virtuelle Azure-Maschine (VM) in demselben virtuellen Netzwerk wie der private Endpunkt des Dienstes erstellt haben.

2. Führen Sie PsPing und Ping von der Azure-VM zum FQDN des Dienstes aus:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Sie müssen einen Port für den PsPing-Befehl angeben. Port 443 wird hier angezeigt, ist jedoch nicht erforderlich.

3. Überprüfen Sie, ob beide Befehle in eine öffentliche Azure Data Factory-IP-Adresse aufgelöst werden, die auf einer bestimmten Region basiert. Die IP-Adresse sollte das folgende Format aufweisen: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Lösung

Gehen Sie zur Lösung des Problems wie folgt vor:

- Als Option möchten wir Ihnen vorschlagen, manuell einen "Virtual Network link" unter der "Private link DNS Zone" für den Dienst hinzuzufügen. Einzelheiten finden Sie im Artikel [Azure Private Link](./data-factory-private-link.md#dns-changes-for-private-endpoints). Die Anweisung dient dazu, die private DNS-Zone oder den benutzerdefinierten DNS-Server so zu konfigurieren, dass der FQDN des Dienstes in eine private IP-Adresse aufgelöst wird. 

- Wenn Sie die private DNS-Zone oder den benutzerdefinierten DNS-Server jedoch nicht konfigurieren möchten, versuchen Sie es mit der folgenden temporären Lösung:

  1. Ändern Sie die Host-Datei in Windows, und ordnen Sie die private IP (den privaten Endpunkt des Dienstes) dem FQDN des Dienstes zu.
  
     Wechseln Sie auf dem virtuellen Azure-Computer zu `C:\Windows\System32\drivers\etc`, und öffnen Sie dann die Datei *host* im Editor. Fügen Sie die Zeile hinzu, mit der die private IP-Adresse dem FQDN am Ende der Datei zugeordnet wird, und speichern Sie die Änderung.
     
     :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png" alt-text="Screenshot der Zuordnung der privaten IP-Adresse zum Host.":::

  1. Führen Sie die gleichen Befehle wie in den vorherigen Überprüfungsschritten erneut aus, um die Antwort zu überprüfen, die die private IP-Adresse enthalten sollte.

  1. Registrieren Sie die selbstgehostete Integration Runtime erneut, und das Problem sollte gelöst sein.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>IR-Authentifizierungsschlüssel kann aufgrund eines privaten Links nicht auf selbstgehosteten VMs registriert werden

#### <a name="symptoms"></a>Symptome

Sie können den IR-Authentifizierungsschlüssel nicht auf der selbstgehosteten VM registrieren, da der private Link aktiviert ist. Sie erhalten die folgende Fehlermeldung:

„Fehler beim Abrufen des Diensttokens aus dem ADF-Dienst mit dem Schlüssel ***************. Zeitaufwand: 0,1250079 Sekunden, Fehlercode: InvalidGatewayKey, activityId: XXXXXXX Detaillierte Fehlermeldung: Die Client-IP-Adresse ist keine gültige private IP-Adresse, weil Data Factory nicht auf das öffentliche Netzwerk zugreifen und so nicht auf die Cloud zugreifen konnte, um die Verbindung herzustellen.“

#### <a name="cause"></a>Ursache

Das Problem könnte von dem virtuellen Computer verursacht worden sein, in dem Sie die selbstgehostete IR zu installieren versuchen. Um eine Verbindung mit der Cloud herzustellen, stellen Sie sicher, dass öffentlicher Netzwerkzugriff aktiviert ist.

#### <a name="resolution"></a>Lösung

**Lösung 1**
 
Gehen Sie zur Lösung des Problems wie folgt vor:

1. Wechseln Sie zur Seite [Factorys – Update](/rest/api/datafactory/Factories/Update).

1. Wählen Sie oben rechts die Schaltfläche **Jetzt testen** aus.
1. Geben Sie unter **Parameter** die erforderlichen Informationen an. 
1. Fügen Sie unter **Text** die folgende Eigenschaft ein:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Wählen Sie **Ausführen** aus, um die Funktion auszuführen. 

1. Geben Sie unter **Parameter** die erforderlichen Informationen an. 

1. Fügen Sie unter **Text** die folgende Eigenschaft ein:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Wählen Sie **Ausführen** aus, um die Funktion auszuführen. 
1. Vergewissern Sie sich, dass **Antwortcode: 200** angezeigt wird. Die Eigenschaft, die Sie eingefügt haben, sollte auch in der JSON-Definition angezeigt werden.

1. Fügen Sie den IR-Authentifizierungsschlüssel erneut der Integration Runtime hinzu.

**Lösung 2**

Um das Problem zu lösen, gehen Sie zu [Azure Private Link](./data-factory-private-link.md).

Versuchen Sie, den Zugriff über das öffentliche Netzwerk auf der Benutzeroberfläche zu aktivieren, wie im folgenden Screenshot gezeigt:

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png" alt-text="Screenshot des Kontrollkästchens &quot;Aktiviert&quot; für &quot;Öffentlichen Netzwerk-Zugang zulassen&quot; auf der Seite &quot; Netzwerke&quot;.":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access-synapse.png" alt-text="Screenshot des Kontrollkästchens &quot;Aktiviert&quot; für &quot;Öffentlichen Netzzugang zulassen&quot; auf der Seite &quot;Netzwerke&quot;. ":::

---

### <a name="service-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>Die private DNS-Zone eines Dienstes überschreibt die Azure Resource Manager DNS-Auflösung und verursacht den Fehler "Nicht gefunden"

#### <a name="cause"></a>Ursache
Sowohl Azure Resource Manager als auch der Dienst verwenden dieselbe private Zone, wodurch ein potenzieller Konflikt im privaten DNS des Kunden entsteht, in dem die Azure Resource Manager-Datensätze nicht gefunden werden.

#### <a name="solution"></a>Lösung
1. Suchen Sie im Azure-Portal unter **privatelink.azure.com** nach privaten DNS-Zonen.
:::image type="content" source="media/security-access-control-troubleshoot-guide/private-dns-zones.png" alt-text="Screenshot: Suchen nach privaten DNS-Zonen":::
2. Überprüfen Sie, ob ein Eintrag vom Typ „A“ mit dem Namen **adf** vorhanden ist.
:::image type="content" source="media/security-access-control-troubleshoot-guide/a-record.png" alt-text="Screenshot: A-Eintrag":::
3.  Navigieren Sie zu **VNET-Verknüpfungen**, und löschen Sie alle Einträge.
:::image type="content" source="media/security-access-control-troubleshoot-guide/virtual-network-link.png" alt-text="Screenshot: VNET-Verknüpfung":::
4.  Navigieren Sie zu Ihrem Dienst im Azure-Portal und erstellen Sie den privaten Endpunkt für das Portal neu.
:::image type="content" source="media/security-access-control-troubleshoot-guide/create-private-endpoint.png" alt-text="Screenshot: Neuerstellen des privaten Endpunkts":::
5.  Kehren Sie zu den privaten DNS-Zonen zurück, und überprüfen Sie, ob eine neue private DNS-Zone vorliegt (**privatelink.adf.azure.com**).
:::image type="content" source="media/security-access-control-troubleshoot-guide/check-dns-record.png" alt-text="Screenshot: Neuer DNS-Eintrag":::

### <a name="connection-error-in-public-endpoint"></a>Verbindungsfehler beim öffentlichen Endpunkt

#### <a name="symptoms"></a>Symptome

Beim Kopieren von Daten mit dem öffentlichen Azure Blob Storage-Kontozugriff, schlagen Pipelineausführungen zufällig mit dem folgenden Fehler fehl.

Beispiel: Die Azure Blob Storage-Senke hat Azure Integration Runtime (öffentlich, nicht verwaltetes VNET) verwendet, während die Azure SQL-Datenbank-Quelle die Integration Runtime für verwaltete VNETs verwendet hat. Oder die Quelle/Senke verwendet die Integration Runtime für verwaltete VNETs nur mit öffentlichem Speicherzugriff.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Ursache

Der Dienst kann immer noch Verwaltetes VNet IR verwenden, aber Sie könnten auf diesen Fehler stoßen, weil der öffentliche Endpunkt zu Azure Blob Storage im Verwalteten VNet basierend auf dem Testergebnis nicht zuverlässig ist und Azure Blob Storage und Azure Data Lake Gen2 nicht unterstützt werden, um über den öffentlichen Endpunkt vom Verwalteten Virtuellen Netzwerk des Dienstes gemäß [Verwaltetes virtuelles Netzwerk & verwaltete private Endpunkte](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network) verbunden zu werden.

#### <a name="solution"></a>Lösung

- Aktivieren Sie einen privaten Endpunkt für die Quell- und Senkenseite, wenn Sie die Integration Runtime für verwaltete VNETs verwenden.
- Wenn Sie dennoch den öffentlichen Endpunkt verwenden möchten, können Sie stattdessen nur die Integration Runtime für verwaltete VNETs für die Quelle und die Senke verwenden. Auch wenn Sie wieder zu öffentlichen IR wechseln, kann der Dienst weiterhin das Verwaltete VNet IR verwenden, wenn das Verwaltete VNet IR noch vorhanden ist.

### <a name="internal-error-while-trying-to-delete-a-data-factory-or-synapse-workspace-with-customer-managed-key-cmk-and-user-assigned-managed-identity-ua-mi"></a>Interner Fehler beim Versuch, eine Datenfabrik oder einen Synapse-Arbeitsbereich mit Customer Managed Key (CMK) und User Assigned Managed Identity (UA-MI) zu löschen

#### <a name="symptoms"></a>Symptome
`{\"error\":{\"code\":\"InternalError\",\"message\":\"Internal error has occurred.\"}}`

#### <a name="cause"></a>Ursache

Wenn Sie Vorgänge im Zusammenhang mit CMK durchführen, sollten Sie zuerst alle Vorgänge im Zusammenhang mit dem Dienst abschließen und dann erst externe Vorgänge (wie Managed Identities oder Key Vault). Wenn Sie z. B. alle Ressourcen löschen möchten, müssen Sie zuerst die Serviceinstanz und dann den Schlüsseltresor löschen.  Wenn Sie den Schlüsseltresor zuerst löschen, tritt dieser Fehler auf, da der Dienst die erforderlichen Objekte nicht mehr lesen kann und nicht in der Lage ist zu überprüfen, ob eine Löschung möglich ist oder nicht. 

#### <a name="solution"></a>Lösung

Dieses Problem kann auf drei Arten behandelt werden. Dies sind:

* Sie haben dem Dienst den Zugriff auf den Schlüsseltresor entzogen, in dem der CMK-Schlüssel gespeichert war. Sie können den Zugriff auf die folgenden Berechtigungen neu zuweisen: **Holen, Schlüssel entpacken und Schlüssel umpacken**. Diese Berechtigungen sind erforderlich, um vom Kunden verwaltete Schlüssel zu aktivieren. Siehe [Zugang zu kundenverwalteten Schlüsseln gewähren](enable-customer-managed-key.md#grant-data-factory-access-to-azure-key-vault). Sobald die Genehmigung erteilt ist, sollten Sie den Dienst löschen können.
 
* Der Kunde hat Key Vault / CMK vor der Löschung des Dienstes gelöscht. CMK im Dienst sollte "Soft Delete" und "Purge Protect" aktiviert haben, die eine Standardaufbewahrungsrichtlinie von 90 Tagen haben. Sie können den gelöschten Schlüssel wiederherstellen.  
Bitte lesen Sie die Informationen zu [Wiederherstellen von gelöschten Schlüsseln](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates ) und [Gelöschter Schlüsselwert](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)

* User Assigned Managed Identity (UA-MI) wurde vor dem Dienst gelöscht. Sie können das mithilfe von REST-API-Aufrufen wiederherstellen. Sie können das in einem HTTP-Client Ihrer Wahl in einer beliebigen Programmiersprache durchführen. Wenn Sie die REST-API-Aufrufe mit Azure-Authentifizierung noch nicht eingerichtet haben, ist die einfachste Möglichkeit um die Einrichtung auszuführen die Verwendung von POSTMAN/Fiddler. Bitte führen Sie die folgenden Schritte aus.

   1.  Führen Sie einen GET-Aufruf mit Method: GET Url wie `https://management.azure.com/subscriptions/YourSubscription/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourFactoryName?api-version=2018-06-01`

   2. Sie müssen eine neue vom Benutzer verwaltete Identität mit einem anderen Namen erstellen (der gleiche Name funktioniert möglicherweise, aber es ist sicherer, einen anderen Namen als in der „GET-Antwort“ zu verwenden)

   3. Ändern Sie die Encryption.identity-Eigenschaft und identity.userassignedidentities so, dass sie auf die neu erstellte verwaltete Identität verweisen. Entfernen Sie die „clientId“ und „principalId“ aus dem userAssignedIdentity-Objekt. 

   4.  Führen Sie einen PUT-Aufruf an dieselbe URL durch und übergeben Sie den neuen Body. Es ist sehr wichtig, dass Sie alles übergeben, was Sie in der GET-Antwort erhalten haben und nur die Identität ändern. Andernfalls würden sie andere Einstellungen versehentlich außer Kraft setzen. 

   5.  Nachdem der Aufruf erfolgreich war, können Sie die Entitäten erneut sehen und das Löschen wiederholen. 

## <a name="sharing-self-hosted-integration-runtime"></a>Freigeben der selbstgehosteten Integration Runtime

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Die Freigabe der selbstgehosteten Integration Runtime von einem anderen Mandanten wird nicht unterstützt. 

#### <a name="symptoms"></a>Symptome

Möglicherweise bemerken Sie andere Datenfabriken (auf verschiedenen Tenants), wenn Sie versuchen, die selbstgehostete IR von der Benutzeroberfläche aus freizugeben, aber Sie können sie nicht für Datenfabriken freigeben, die sich auf verschiedenen Tenants befinden.

#### <a name="cause"></a>Ursache

Die selbstgehostete Integration Runtime kann nicht mandantenübergreifend freigegeben werden.


## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in den folgenden Ressourcen:

*  [Private Link für Data Factory](data-factory-private-link.md)
*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
