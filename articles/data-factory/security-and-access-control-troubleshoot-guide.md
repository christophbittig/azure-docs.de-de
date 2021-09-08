---
title: Beheben von Problemen mit der Sicherheit und Zugriffssteuerung
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit der Sicherheit und Zugriffssteuerung in Azure Data Factory beheben.
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 07/28/2021
ms.author: lle
ms.openlocfilehash: a025e46914390d203537d0ddd0c9faf5f22488ab
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864155"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Beheben von Problemen mit der Sicherheit und Zugriffssteuerung in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden gängige Methoden zur Problembehandlung für die Sicherheit und Zugriffssteuerung in Azure Data Factory beschrieben.

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

Nachdem Sie den öffentlichen Netzwerkzugriff für Data Factory deaktiviert haben, gibt die selbstgehostete Integration Runtime folgende Fehlermeldung aus: „Der Authentifizierungsschlüssel ist ungültig oder leer.“

#### <a name="cause"></a>Ursache

Das Problem wird wahrscheinlich durch ein Problem bei der Domain Name System-Auflösung (DNS) verursacht, da die Deaktivierung der öffentlichen Konnektivität und das Einrichten eines privaten Endpunkts eine erneute Verbindung verhindert.

Gehen Sie folgendermaßen vor, um zu überprüfen, ob der vollständig qualifizierte Data Factory-Domänenname (FQDN) in die öffentliche IP-Adresse aufgelöst ist:

1. Vergewissern Sie sich, dass Sie den virtuellen Azure-Computer (VM) im gleichen virtuellen Netzwerk wie den privaten Data Factory-Endpunkt erstellt haben.

2. Führen Sie PsPing und Ping von der Azure-VM zum Data Factory-FQDN aus:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Sie müssen einen Port für den PsPing-Befehl angeben. Port 443 wird hier angezeigt, ist jedoch nicht erforderlich.

3. Überprüfen Sie, ob beide Befehle in eine öffentliche Azure Data Factory-IP-Adresse aufgelöst werden, die auf einer bestimmten Region basiert. Die IP-Adresse sollte das folgende Format aufweisen: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Lösung

Gehen Sie zur Lösung des Problems wie folgt vor:

- Als Option empfiehlt es sich, manuell eine „VNet-Verknüpfung“ unter der „Private Link-DNS-Zone“ von Data Factory hinzuzufügen. Lesen Sie für weitere Informationen den Artikel [Azure Private Link für Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints). Die Anweisung dient zum Konfigurieren der privaten DNS-Zone oder des benutzerdefinierten DNS-Servers, um den Data Factory-FQDN in eine private IP-Adresse aufzulösen. 

- Wenn Sie die private DNS-Zone oder den benutzerdefinierten DNS-Server jedoch nicht konfigurieren möchten, versuchen Sie es mit der folgenden temporären Lösung:

  1. Ändern Sie die Hostdatei in Windows, und ordnen Sie die private IP-Adresse (den privaten Azure Data Factory-Endpunkt) dem Azure Data Factory-FQDN zu.
  
     Wechseln Sie auf dem virtuellen Azure-Computer zu `C:\Windows\System32\drivers\etc`, und öffnen Sie dann die Datei *host* im Editor. Fügen Sie die Zeile hinzu, mit der die private IP-Adresse dem FQDN am Ende der Datei zugeordnet wird, und speichern Sie die Änderung.
     
     ![Screenshot der Zuordnung der privaten IP-Adresse zum Host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

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

Um das Problem zu lösen, gehen Sie zu [Azure Private Link für Azure Data Factory](./data-factory-private-link.md).

Versuchen Sie, den Zugriff über das öffentliche Netzwerk auf der Benutzeroberfläche zu aktivieren, wie im folgenden Screenshot gezeigt:

![Screenshot des Steuerelements „Aktiviert“ für „Zugriff über öffentliche Netzwerke zulassen“ im Bereich „Netzwerk“.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>Überschreibung der Azure Resource Manager-DNS-Auflösung durch die private DNS-Zone von ADF führt zum Fehler „Nicht gefunden“

#### <a name="cause"></a>Ursache
Sowohl Azure Resource Manager als auch ADF verwenden dieselbe private Zone, was bei einem Szenario, in dem die Azure Resource Manager-Einträge nicht gefunden werden, zu einem potenziellen Konflikt mit dem privaten DNS des Kunden führt.

#### <a name="solution"></a>Lösung
1. Suchen Sie im Azure-Portal unter **privatelink.azure.com** nach privaten DNS-Zonen.
![Screenshot: Suchen nach privaten DNS-Zonen](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. Überprüfen Sie, ob ein Eintrag vom Typ „A“ mit dem Namen **adf** vorhanden ist.
![Screenshot: A-Eintrag](media/security-access-control-troubleshoot-guide/a-record.png)
3.  Navigieren Sie zu **VNET-Verknüpfungen**, und löschen Sie alle Einträge.
![Screenshot: VNET-Verknüpfung](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Navigieren Sie im Azure-Portal zu Ihrer Data Factory-Instanz, und erstellen Sie den privaten Endpunkt für das Azure Data Factory-Portal neu.
![Screenshot: Neuerstellen des privaten Endpunkts](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  Kehren Sie zu den privaten DNS-Zonen zurück, und überprüfen Sie, ob eine neue private DNS-Zone vorliegt (**privatelink.adf.azure.com**).
![Screenshot: Neuer DNS-Eintrag](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>Verbindungsfehler beim öffentlichen Endpunkt

#### <a name="symptoms"></a>Symptome

Beim Kopieren von Daten mit dem öffentlichen Azure Blob Storage-Kontozugriff, schlagen Pipelineausführungen zufällig mit dem folgenden Fehler fehl.

Beispiel: Die Azure Blob Storage-Senke hat Azure Integration Runtime (öffentlich, nicht verwaltetes VNET) verwendet, während die Azure SQL-Datenbank-Quelle die Integration Runtime für verwaltete VNETs verwendet hat. Oder die Quelle/Senke verwendet die Integration Runtime für verwaltete VNETs nur mit öffentlichem Speicherzugriff.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Ursache

ADF verwendet möglicherweise weiterhin die Integration Runtime für verwaltete VNETs, jedoch könnte ein solcher Fehler auftreten, weil der öffentliche Endpunkt für Azure Blob Storage im verwalteten VNET aufgrund des Testergebnisses nicht zuverlässig ist und die Verbindung mit Azure Blob Storage und Azure Data Lake Gen2 über einen öffentlichen Endpunkt eines verwalteten, virtuellen ADF-Netzwerks gemäß [Verwaltete virtuelle Netzwerke und verwaltete private Endpunkte](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network) nicht unterstützt wird.

#### <a name="solution"></a>Lösung

- Aktivieren Sie einen privaten Endpunkt für die Quell- und Senkenseite, wenn Sie die Integration Runtime für verwaltete VNETs verwenden.
- Wenn Sie dennoch den öffentlichen Endpunkt verwenden möchten, können Sie stattdessen nur die Integration Runtime für verwaltete VNETs für die Quelle und die Senke verwenden. Selbst wenn Sie zur öffentlichen Integration Runtime zurückwechseln, verwendet ADF möglicherweise weiterhin die Integration Runtime für verwaltete VNETs, wenn diese noch vorhanden ist.

### <a name="internal-error-while-trying-to-delete-adf-with-customer-managed-key-cmk-and-user-assigned-managed-identity-ua-mi"></a>Es ist ein interner Fehler beim Versuch, ADF mit kundenverwalteten Schlüsseln (Customer Managed Key, CMK) und vom Benutzer zugewiesener verwalteter Identität (UA-MI) zu löschen, aufgetreten

#### <a name="symptoms"></a>Symptome
`{\"error\":{\"code\":\"InternalError\",\"message\":\"Internal error has occurred.\"}}`

#### <a name="cause"></a>Ursache

Wenn Sie Vorgänge im Zusammenhang mit CMK durchführen, sollten Sie zuerst alle ADF-bezogenen Vorgänge und dann externe Vorgänge (z. B. verwaltete Identitäten oder Key Vault Vorgänge) durchführen. Wenn Sie beispielsweise alle Ressourcen löschen möchten, müssen Sie zuerst die Factory löschen und dann den Schlüsseltresor löschen. Wenn Sie dies in einer anderen Reihenfolge tun, schlägt der ADF-Aufruf fehl, da er verwandte Objekte nicht mehr lesen kann, und er kann nicht überprüfen, ob das Löschen möglich ist oder nicht. 

#### <a name="solution"></a>Lösung

Dieses Problem kann auf drei Arten behandelt werden. Dies sind:

* Sie haben den Zugriff von ADF auf den Schlüsseltresor widerrufen, in dem der CMK-Schlüssel gespeichert wurde. 
Sie können den Zugriff auf die Data Factory mit den folgenden Berechtigungen neu zuweisen: **Abrufen, Aufhebung der Umschließung des Schlüssels und Schlüssel Umschließen**. Diese Berechtigungen sind erforderlich, um vom Kunden verwaltete Schlüssel in Data Factory aktivieren zu können. Informationen hierzu finden Sie im Abschnitt [Zugriff auf die ADF gewähren](enable-customer-managed-key.md#grant-data-factory-access-to-azure-key-vault). Sobald die Erlaubnis erteilt wurde, sollten Sie in der Lage sein, die ADF zu löschen
 
* Der Kunde hat den Key Vault/CMK vor dem Löschen der ADF gelöscht. Die Einstellungen „Vorläufiges Löschen“ und „Schutz vor endgültigem Löschen“ für CMK in der ADF müssen aktiviert sein. Dafür ist eine Standardaufbewahrungsrichtlinie von 90 Tagen festgelegt. Sie können den gelöschten Schlüssel wiederherstellen.  
 Bitte lesen Sie die Informationen zu [Wiederherstellen von gelöschten Schlüsseln](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates) und [Gelöschter Schlüsselwert](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)

* Die vom Benutzer zugewiesene verwaltete Identität (UA-MI) wurde vor ADF gelöscht. Sie können das mithilfe von REST-API-Aufrufen wiederherstellen. Sie können das in einem HTTP-Client Ihrer Wahl in einer beliebigen Programmiersprache durchführen. Wenn Sie die REST-API-Aufrufe mit Azure-Authentifizierung noch nicht eingerichtet haben, ist die einfachste Möglichkeit um die Einrichtung auszuführen die Verwendung von POSTMAN/Fiddler. Bitte führen Sie die folgenden Schritte aus.

   1.  Führen Sie ein GET-Aufruf an die Factory mithilfe der folgenden Methode aus GET-URL wie   `https://management.azure.com/subscriptions/YourSubscription/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourFactoryName?api-version=2018-06-01`

   2. Sie müssen eine neue vom Benutzer verwaltete Identität mit einem anderen Namen erstellen (der gleiche Name funktioniert möglicherweise, aber es ist sicherer, einen anderen Namen als in der „GET-Antwort“ zu verwenden)

   3. Ändern Sie die Encryption.identity-Eigenschaft und identity.userassignedidentities so, dass sie auf die neu erstellte verwaltete Identität verweisen. Entfernen Sie die „clientId“ und „principalId“ aus dem userAssignedIdentity-Objekt. 

   4.  Nehmen Sie einen PUT-Aufruf an dieselbe Factory-URL vor und übergeben Sie den neuen Text. Es ist sehr wichtig, dass Sie alles übergeben, was Sie in der GET-Antwort erhalten haben und nur die Identität ändern. Andernfalls würden sie andere Einstellungen versehentlich außer Kraft setzen. 

   5.  Nachdem der Aufruf erfolgreich war, können Sie die Entitäten erneut sehen und das Löschen wiederholen. 

## <a name="sharing-self-hosted-integration-runtime"></a>Freigeben der selbstgehosteten Integration Runtime

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Die Freigabe der selbstgehosteten Integration Runtime von einem anderen Mandanten wird nicht unterstützt. 

#### <a name="symptoms"></a>Symptome

Beim Versuch, die selbstgehostete Integration Runtime über die Azure Data Factory-Benutzeroberfläche übergreifend für Data Factorys in anderen Mandanten freizugeben, werden Ihnen eventuell andere Data Factorys (in verschiedenen Mandanten) angezeigt, und der Versuch schlägt fehl.

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
