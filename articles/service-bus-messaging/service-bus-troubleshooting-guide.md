---
title: Leitfaden zur Problembehandlung für Azure Service Bus | Microsoft-Dokumentation
description: Lernen Sie Tipps zur Problembehandlung und Empfehlungen für einige Probleme kennen, die bei der Verwendung von Azure Service Bus auftreten können.
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 433383845771fca2b3df1ce1da81e070dd8e75c1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359381"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Leitfaden zur Problembehandlung für Azure Service Bus
In diesem Artikel finden Sie Tipps zur Problembehandlung und Empfehlungen für einige Probleme, die bei der Verwendung von Azure Service Bus auftreten können. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Konnektivitäts-, Zertifikat- oder Timeoutprobleme
Die folgenden Schritte unterstützen Sie bei der Problembehandlung von Konnektivitäts-/Zertifikat-/Timeoutproblemen für alle Dienste unter *.servicebus.windows.net. 

- Navigieren Sie zu `https://<yournamespace>.servicebus.windows.net/`, oder verwenden Sie [wget](https://www.gnu.org/software/wget/). Dies hilft bei der Überprüfung, ob Probleme mit der IP-Filterung oder dem virtuellen Netzwerk bzw. der Zertifikatkette vorliegen – diese treten bei Verwendung des Java SDK nicht selten auf.

    Beispiel für eine erfolgreiche Meldung:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Beispiel für eine Fehlermeldung:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Führen Sie den folgenden Befehl aus, um zu überprüfen, ob ein Port auf der Firewall blockiert ist. Die verwendeten Ports lauten 443 (HTTPS), 5671 (AMQP) und 9354 (.NET-Messaging/SBMP). Abhängig von der verwendeten Bibliothek werden auch andere Ports verwendet. Dies ist der Beispielbefehl, mit dem überprüft wird, ob Port 5671 blockiert ist. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Unter Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Führen Sie bei zeitweiligen Konnektivitätsproblemen den folgenden Befehl aus, um zu überprüfen, ob gelöschte Pakete vorhanden sind. Mit diesem Befehl wird versucht, jede Sekunde 25 verschiedene TCP-Verbindungen mit dem Dienst herzustellen. Anschließend können Sie überprüfen, wie viele davon erfolgreich/fehlerhaft waren, und außerdem die Latenz der TCP-Verbindung anzeigen. Sie können das `psping`-Tool [hier](/sysinternals/downloads/psping) herunterladen.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Sie können äquivalente Befehle verwenden, wenn Sie andere Tools wie `tnc`, `ping` usw. nutzen. 
- Rufen Sie eine Netzwerkablaufverfolgung ab, wenn die vorherigen Schritte nicht hilfreich sind, und analysieren Sie diese mit Tools wie [Wireshark](https://www.wireshark.org/). Wenden Sie sich bei Bedarf an den [Microsoft-Support](https://support.microsoft.com/). 
- Informationen zum Ermitteln der IP-Adressen, die der Positivliste für Ihre Verbindungen hinzugefügt werden müssen, finden Sie unter [Welche IP-Adressen muss ich in die Zulassungsliste aufnehmen?](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allowlist-). 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Mögliche Probleme im Zusammenhang mit Dienstupgrades/-neustarts

### <a name="symptoms"></a>Symptome
- Anforderungen werden ggf. vorübergehend gedrosselt.
- Es gehen möglicherweise weniger Nachrichten/Anforderungen ein.
- Die Protokolldatei enthält unter Umständen Fehlermeldungen.
- Die Verbindung zwischen Anwendungen und dem Dienst wird möglicherweise für einige Sekunden getrennt.

### <a name="cause"></a>Ursache
Upgrades und Neustarts des Back-End-Diensts können folgende Probleme in Ihren Anwendungen verursachen.

### <a name="resolution"></a>Lösung
Wenn der Anwendungscode das SDK verwendet, ist die Wiederholungsrichtlinie bereits integriert und aktiv. Die Verbindung wird ohne nennenswerte Auswirkungen auf die Anwendung bzw. den Workflow wiederhergestellt.

## <a name="unauthorized-access-send-claims-are-required"></a>Nicht autorisierter Zugriff: Sendeansprüche sind erforderlich

### <a name="symptoms"></a>Symptome 
Dieser Fehler tritt möglicherweise auf, wenn Sie mit einer vom Benutzer zugewiesenen Identität mit Sendeberechtigungen versuchen, über Visual Studio auf ein Service Bus-Thema auf einem lokalen Computer zuzugreifen.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Ursache
Die Identität verfügt nicht über die erforderlichen Zugriffsberechtigungen für das Service Bus-Thema. 

### <a name="resolution"></a>Lösung
Installieren Sie die Bibliothek [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/), um das Problem zu lösen.  Weitere Informationen finden Sie unter [Authentifizierung für die lokale Entwicklung](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication). 

Informationen zum Zuweisen von Berechtigungen zu Rollen finden Sie unter [Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Azure Service Bus-Ressourcen](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Service Bus-Ausnahme: Fehler beim Put-Token.

### <a name="symptoms"></a>Symptome
Wenn Sie versuchen, mehr als 1000 Nachrichten über dieselbe Service Bus-Verbindung zu senden, erhalten Sie folgende Fehlermeldung: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Ursache
Die Anzahl von Token, die zum Senden und Empfangen von Nachrichten über eine einzelne Verbindung mit einem Service Bus-Namespace verwendet werden können, ist begrenzt. Das Limit ist 1000. 

### <a name="resolution"></a>Lösung
Öffnen Sie eine neue Verbindung mit dem Service Bus-Namespace, um weitere Nachrichten senden zu können.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Fehler beim Hinzufügen einer VNET-Regel mithilfe von PowerShell

### <a name="symptoms"></a>Symptome
Sie haben zwei Subnetze aus einem einzelnen virtuellen Netzwerk in einer VNET-Regel konfiguriert. Wenn Sie versuchen, ein Subnetz mithilfe des Cmdlets [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) zu entfernen, wird das Subnetz nicht aus der VNET-Regel entfernt. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Ursache
Die Azure Resource Manager-ID, die Sie für das Subnetz angegeben haben, ist möglicherweise ungültig. Dies kann der Fall sein, wenn sich das virtuelle Netzwerk in einer anderen Ressourcengruppe als der Service Bus-Namespace befindet. Wenn Sie die Ressourcengruppe des virtuellen Netzwerks nicht explizit angeben, erstellt der CLI-Befehl die Azure Resource Manager-ID mithilfe der Ressourcengruppe des Service Bus-Namespace. Daher schlägt das Entfernen des Subnetzes aus der Netzwerkregel fehl. 

### <a name="resolution"></a>Lösung
Geben Sie die vollständige Azure Resource Manager-ID des Subnetzes an, die den Namen der Ressourcengruppe enthält, in der sich das virtuelle Netzwerk befindet. Beispiel:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln: 

- [Azure Resource Manager-Ausnahmen](service-bus-resource-manager-exceptions.md): In diesem Artikel werden die Ausnahmen aufgelistet, die bei der Interaktion mit Azure Service Bus über Azure Resource Manager (über Vorlagen oder direkte Aufrufe) generiert werden.
- [Messagingausnahmen](service-bus-messaging-exceptions.md): In diesem Artikel finden Sie eine Liste der Ausnahmen, die .NET Framework für Azure Service Bus generiert.
