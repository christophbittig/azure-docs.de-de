---
title: Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten
description: In diesem Artikel wird beschrieben, wie Sie Probleme mit Ihrem Purview-Konto im Zusammenhang mit Konfigurationen privater Endpunkte beheben.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: 1557b229c4db50dca2115fc9c11123c76c5adc50
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851817"
---
# <a name="troubleshooting-private-endpoint-configuration-for-purview-accounts"></a>Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten

> [!IMPORTANT]
> Wenn Sie einen privaten _Portalendpunkt_ für Ihr Purview-Konto **vor dem 27. September 2021 um 15:30 UTC** erstellt haben, müssen Sie die erforderlichen Maßnahmen ergreifen, wie in [DNS für private Portalendpunkte neu konfigurieren](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints) beschrieben. **Diese Maßnahmen müssen vor dem 12. November 2021 abgeschlossen sein. Andernfalls werden die vorhandenen privaten Portalendpunkte nicht mehr funktionieren**.

Dieser Leitfaden fasst bekannte Einschränkungen im Zusammenhang mit der Verwendung privater Endpunkte für Azure Purview zusammen und enthält eine Liste der Schritte und Lösungen zum Beheben einiger der häufigsten relevanten Probleme. 

## <a name="known-limitations"></a>Bekannte Einschränkungen

- Private Erfassungsendpunkte für AWS-Quellen werden derzeit nicht unterstützt.
- Das Überprüfen mehrerer Azure-Quellen mithilfe der selbstgehosteten Integration Runtime wird nicht unterstützt.
- Die Verwendung von Azure Integration Runtime zum Überprüfen von Datenquellen hinter einem privaten Endpunkt wird nicht unterstützt.
- Bei Verwendung des Azure-Portals können die privaten Erfassungsendpunkte über die in den vorherigen Schritten beschriebene Benutzeroberfläche des Azure Purview-Portals erstellt werden. Sie können nicht über Private Link Center erstellt werden.
- Wenn sich die privaten Azure DNS-Zonen in einem anderen Abonnement befinden als die privaten Endpunkte, wird das Erstellen von DNS-A-Einträgen für private Erfassungsendpunkte innerhalb vorhandener Azure DNS-Zonen über das Azure Purview-Portal nicht unterstützt. A-Einträge können manuell in den DNS-Zielzonen im anderen Abonnement hinzugefügt werden. 
- Der Computer mit der selbstgehosteten Integration Runtime muss in demselben VNet bereitgestellt werden, in dem der private Azure Purview-Erfassungsendpunkt bereitgestellt wird.
- Das Überprüfen eines Power BI-Mandanten, für den ein privater Endpunkt konfiguriert und der öffentliche Zugriff blockiert ist, wird derzeit nicht unterstützt.
- Informationen zu Einschränkungen im Zusammenhang mit dem Private Link-Dienst finden Sie unter [Private Link-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="recommended-troubleshooting-steps"></a>Empfohlene Schritte zur Problembehandlung  

1. Nachdem Sie private Endpunkte für Ihr Purview-Konto bereitgestellt haben, überprüfen Sie Ihre Azure-Umgebung, um sicherzustellen, dass private Endpunktressourcen erfolgreich bereitgestellt werden. Je nach Szenario muss mindestens einer der folgenden privaten Azure-Endpunkte in Ihrem Azure-Abonnement bereitgestellt werden:

    |Privater Endpunkt  |Privater Endpunkt, zugewiesen zu | Beispiel|
    |---------|---------|---------|
    |Konto  |Azure Purview-Konto         |mypurview-private-account  |
    |Portal     |Azure Purview-Konto         |mypurview-private-portal  |
    |Erfassung     |Verwaltetes Speicherkonto (Blob)         |mypurview-ingestion-blob  |
    |Erfassung     |Verwaltetes Speicherkonto (Warteschlange)         |mypurview-ingestion-queue  |
    |Erfassung     |Verwalteter Event Hubs-Namespace         |mypurview-ingestion-namespace  |

2. Wenn der private Endpunkt für das Portal bereitgestellt wird, stellen Sie sicher, dass Sie auch den privaten Endpunkt für das Konto bereitstellen.

3. Wenn ein privater Endpunkt für das Portal bereitgestellt und die Einstellung für den Zugriff auf das öffentliche Netzwerk in Ihrem Azure Purview-Konto auf „Verweigern“ festgelegt ist, stellen Sie sicher, dass Sie [Azure Purview Studio](https://web.purview.azure.com/resource/) über das interne Netzwerk starten.
  <br>
    - Sie können zum Abfragen von `web.purview.azure.com` das Befehlszeilentool **NSlookup.exe** verwenden, um die ordnungsgemäße Namensauflösung zu überprüfen. Das Ergebnis muss eine private IP-Adresse zurückgeben, die zum privaten Endpunkt für das Portal gehört. 
    - Beim Überprüfen der Netzwerkkonnektivität können Sie alle Netzwerktesttools verwenden, um die ausgehende Konnektivität mit dem Endpunkt `web.purview.azure.com` an Port **443** zu testen. Die Verbindung muss erfolgreich sein.    

3. Wenn private Azure DNS-Zonen verwendet werden, stellen Sie sicher, dass die erforderlichen Azure DNS-Zonen bereitgestellt sind und für jeden privaten Endpunkt ein DNS-Eintrag (A) vorhanden ist.

4. Testen Sie die Netzwerkkonnektivität und Namensauflösung des Verwaltungscomputers für den Purview-Endpunkt und die Purview-Web-URL. Wenn die privaten Endpunkte für das Konto und das Portal bereitgestellt werden, müssen sie über private IP-Adressen aufgelöst werden.


    ```powershell
    Test-NetConnection -ComputerName web.purview.azure.com -Port 443
    ```

    Beispiel für eine erfolgreiche ausgehende Verbindung über eine private IP-Adresse:

    ```
    ComputerName     : web.purview.azure.com
    RemoteAddress    : 10.9.1.7
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName purview-test01.purview.azure.com -Port 443
    ```

    Beispiel für eine erfolgreiche ausgehende Verbindung über eine private IP-Adresse:

    ```
    ComputerName     : purview-test01.purview.azure.com
    RemoteAddress    : 10.9.1.8
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```
    
5. Wenn Sie Ihr Azure Purview-Konto nach dem 18. August 2021 erstellt haben, stellen Sie sicher, dass Sie die neueste Version der selbstgehosteten Integration Runtime aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) herunterladen und installieren.
   
6. Testen Sie die Netzwerkkonnektivität und Namensauflösung der selbstgehosteten Integration Runtime für den Purview-Endpunkt.

7. Testen Sie die Netzwerkkonnektivität und Namensauflösung der selbstgehosteten Integration Runtime für verwaltete Azure Purview-Ressourcen wie Blobwarteschlangen und Event Hubs über Port 443 und private IP-Adressen. (Ersetzen Sie das verwaltete Speicherkonto und den Event Hubs-Namespace durch den entsprechenden Namen der verwalteten Ressource, die Ihrem Azure Purview-Konto zugewiesen ist.)

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.blob.core.windows.net -Port 443
    ```
    Beispiel für eine erfolgreiche ausgehende Verbindung mit verwaltetem Blobspeicher über eine private IP-Adresse:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.6
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.queue.core.windows.net -Port 443
    ```
    Beispiel für eine erfolgreiche ausgehende Verbindung mit verwaltetem Warteschlangenspeicher über eine private IP-Adresse:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.5
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `Atlas-1225cae9-d651-4039-86a0-b43231a17a4b`.servicebus.windows.net -Port 443
    ```
    Beispiel für eine erfolgreiche ausgehende Verbindung mit Event Hub-Namespace über eine private IP-Adresse:

    ```
    ComputerName     : Atlas-1225cae9-d651-4039-86a0-b43231a17a4b.servicebus.windows.net
    RemoteAddress    : 10.15.1.4
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

8. Testen Sie die Netzwerkkonnektivität und Namensauflösung des Netzwerks, in dem sich die Datenquelle befindet, für den Purview-Endpunkt und verwaltete Ressourcenendpunkte.

9.  Wenn sich Datenquellen im lokalen Netzwerk befinden, überprüfen Sie Ihre DNS-Weiterleitungskonfiguration. Testen Sie die Namensauflösung innerhalb desselben Netzwerks, in dem sich Datenquellen befinden, für die selbstgehostete Integration Runtime, Azure Purview-Endpunkte und verwaltete Ressourcen. Es wird erwartet, dass über die DNS-Abfrage für jeden Endpunkt eine gültige private IP-Adresse abgerufen wird.
    
    Weitere Informationen finden Sie in den Szenarien [Virtual Network-Workloads ohne benutzerdefinierten DNS-Server](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) und [Lokale Workloads mit DNS-Weiterleitung](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

10. Wenn Verwaltungscomputer und selbstgehostete Integration Runtime-VMs im lokalen Netzwerk bereitgestellt werden und Sie die DNS-Weiterleitung in Ihrer Umgebung eingerichtet haben, überprüfen Sie die DNS-Einstellungen und Netzwerkeinstellungen in Ihrer Umgebung. 

11. Wenn der private Erfassungsendpunkt verwendet wird, stellen Sie sicher, dass die selbstgehostete Integration Runtime erfolgreich im Purview-Konto registriert und sowohl auf der selbstgehosteten Integration Runtime-VM als auch in [Purview Studio](https://web.purview.azure.com/resource/) als ausgeführt angezeigt wird.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="issue"></a>Problem 
Beim Durchführen einer Überprüfung wird möglicherweise die folgende Fehlermeldung angezeigt:

`Internal system error. Please contact support with correlationId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx System Error, contact support.`

### <a name="cause"></a>Ursache 
Dies kann ein Hinweis auf Probleme im Zusammenhang mit der Konnektivität oder Namensauflösung zwischen dem virtuellen Computer, auf dem die selbstgehostete Integration Runtime ausgeführt wird, und dem Speicherkonto oder Event Hub der verwalteten Azure Purview-Ressourcen sein.

### <a name="resolution"></a>Lösung 
Überprüfen Sie, ob die Namensauflösung zwischen dem virtuellen Computer, auf dem die selbstgehostete Integration Runtime ausgeführt wird, und anderen Ressourcen erfolgt.


### <a name="issue"></a>Problem 
Beim Durchführen einer neuen Überprüfung wird möglicherweise die folgende Fehlermeldung angezeigt:

  `message: Unable to setup config overrides for this scan. Exception:'Type=Microsoft.WindowsAzure.Storage.StorageException,Message=The remote server returned an error: (404) Not Found.,Source=Microsoft.WindowsAzure.Storage,StackTrace= at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndExecuteAsync[T](IAsyncResult result)`

### <a name="cause"></a>Ursache 
Dies kann ein Hinweis auf die Ausführung einer älteren Version der selbstgehosteten Integration Runtime sein. Sie müssen mindestens Version 5.9.7885.3 der selbstgehosteten Integration Runtime verwenden.

### <a name="resolution"></a>Lösung 
Führen Sie ein Upgrade der selbstgehosteten Integration Runtime auf Version 5.9.7885.3 durch.


### <a name="issue"></a>Problem 
Beim Azure Purview-Konto mit privater Endpunktbereitstellung ist während der Bereitstellung ein Azure Policy-Validierungsfehler aufgetreten.

### <a name="cause"></a>Ursache
Dieser Fehler deutet darauf hin, dass möglicherweise eine Azure Policy-Zuweisung für Ihr Azure-Abonnement vorhanden ist, die die Bereitstellung der erforderlichen Azure-Ressourcen verhindert. 

### <a name="resolution"></a>Lösung 
Überprüfen Sie Ihre vorhandenen Azure Policy-Zuweisungen, und stellen Sie sicher, dass die Bereitstellung der folgenden Azure-Ressourcen in Ihrem Azure-Abonnement zulässig ist. 
   
> [!NOTE]
> Je nach Szenario müssen Sie möglicherweise einen oder mehrere der folgenden Azure-Ressourcentypen bereitstellen: 
>  -   Azure Purview (Microsoft.Purview/Accounts)
>  -   Privater Endpunkt (Microsoft.Network/privateEndpoints)
>  -   Private DNS-Zonen (Microsoft.Network/privateDnsZones)
>  -   Event Hub-Namespace (Microsoft.EventHub/namespaces)
>  -   Speicherkonto (Microsoft.Storage/storageAccounts)


### <a name="issue"></a>Problem
Keine Autorisierung für den Zugriff auf dieses Purview-Konto. Dieses Purview-Konto befindet sich hinter einem privaten Endpunkt. Greifen Sie über einen Client im gleichen virtuellen Netzwerk (VNet) auf das Konto zu, das für den privaten Endpunkt des Purview-Kontos konfiguriert wurde.

### <a name="cause"></a>Ursache 
Benutzer*innen versuchen über einen öffentlichen Endpunkt eine Verbindung mit Azure Purview herzustellen oder verwenden öffentliche Azure Purview-Endpunkte, bei denen **Öffentlicher Netzwerkzugriff** auf **Verweigern** festgelegt ist.

### <a name="resolution"></a>Lösung
Verwenden Sie in diesem Fall zum Öffnen von Azure Purview Studio entweder einen Computer, der im gleichen virtuellen Netzwerk wie der private Endpunkt des Azure Purview-Portals bereitgestellt wurde, oder eine VM, die mit einem Unternehmensnetzwerk verbunden ist, in dem die Hybridkonnektivität zulässig ist.

### <a name="issue"></a>Problem
Beim Scannen eines SQL-Servers mit einer selbst gehosteten Integrationslaufzeit wird möglicherweise die folgende Fehlermeldung angezeigt:

  `Message=This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms`

### <a name="cause"></a>Ursache 
Die selbst gehostete Integrations-Laufzeitmaschine hat den FIPS-Modus aktiviert.
FIPS (Federal Information Processing Standards) definiert eine bestimmte Gruppe von Kryptografiealgorithmen, die verwendet werden können. Wenn der FIPS-Modus auf dem Computer aktiviert ist, werden einige kryptografische Klassen, von denen die aufgerufenen Prozesse abhängen, in einigen Szenarien blockiert.

### <a name="resolution"></a>Lösung
Deaktivieren Sie den FIPS-Modus auf dem selbst gehosteten Integrationsserver.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht in diesem Artikel aufgeführt ist oder Sie es nicht beheben können, können Sie in einem der folgenden Kanäle Unterstützung erhalten:

- Erhalten Sie Antworten von Experten über [Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-purview.html).
- Wenden Sie sich an [@AzureSupport](https://twitter.com/azuresupport) – Dies ist die offizielle Microsoft Azure-Ressource auf Twitter zur Verbesserung der Benutzerfreundlichkeit durch Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
- Wenn Sie weiterhin Hilfe benötigen, besuchen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/), und **senden Sie eine Supportanfrage**.
