---
title: Beheben von Problemen mit der Netzwerkkonnektivität | Microsoft-Dokumentation
description: Enthält Tipps zur Problembehandlung für häufige Fehler bei der Verwendung von Azure Migrate mit privaten Endpunkten.
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 4c23367862728d42148971b4941d6c94a750c227
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504605"
---
# <a name="troubleshoot-network-connectivity"></a>Problembehandlung für die lokale Netzwerkkonnektivität
Dieser Artikel hilft Ihnen bei der Behandlung von Problemen mit der Netzwerkkonnektivität bei der Verwendung von Azure Migrate mit privaten Endpunkten.

## <a name="validate-private-endpoints-configuration"></a>Überprüfen der Konfiguration privater Endpunkte

Stellen Sie sicher, dass der private Endpunkt den Status „Genehmigt“ aufweist.  

1. Wechseln Sie zu den Eigenschaftenseiten der **Azure Migrate**-Tools **Ermittlung und Bewertung** und **Servermigration**.

2. Die jeweilige Eigenschaftenseite enthält die Liste der privaten Endpunkte und FQDNs der privaten Verbindungen, die automatisch von Azure Migrate erstellt wurden.  

3. Wählen Sie den zu diagnostizierenden privaten Endpunkt aus.  
      a. Vergewissern Sie sich, dass der Verbindungsstatus Genehmigt lautet.           
      b. Wenn sich die Verbindung im Status „Ausstehend“ befindet, müssen Sie sie zunächst genehmigen lassen.                         
      c. Sie können auch zur Ressource des privaten Endpunkts navigieren und überprüfen, ob das virtuelle Netzwerk mit dem virtuellen Netzwerk des privaten Endpunkts des Migrate-Projekts übereinstimmt.                                                        

     ![Anzeigen einer Verbindung mit dem privaten Endpunkt](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


## <a name="validate-the-data-flow-through-the-private-endpoints"></a>Überprüfen des Datenflusses über die privaten Endpunkte
Sehen Sie sich die Datenflussmetriken an, um den Datenfluss über private Endpunkte zu überprüfen. Wählen Sie auf der Eigenschaftenseite der Azure Migrate-Tools „Serverbewertung“ und „Servermigration“ den privaten Endpunkt aus. Damit gelangen Sie zum Abschnitt „Übersicht“ für den privaten Endpunkt in Azure Private Link Center. Wählen Sie im linken Menü **Metriken** aus, um die Werte für _Data Bytes In_ (Eingehende Datenbytes) und _Data Bytes Out_ (Ausgehende Datenbytes) und damit den Datenfluss anzuzeigen.

## <a name="verify-dns-resolution"></a>Überprüfen der DNS-Auflösung

Die lokale Appliance (oder der Replikationsanbieter) greift über die vollqualifizierten Domänennamen (FQDNs) der privaten Verbindung auf die Azure Migrate-Ressourcen zu. Möglicherweise sind zusätzliche DNS-Einstellungen erforderlich, um die private IP-Adresse der privaten Endpunkte aus der Quellumgebung aufzulösen. In [diesem Artikel](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) werden DNS-Konfigurationsszenarien erläutert, die bei der Problembehandlung mit der Netzwerkkonnektivität helfen können.  

Führen Sie zum Überprüfen der privaten Verbindung von dem lokalen Server aus, auf dem die Migrate-Appliance gehostet wird, eine DNS-Auflösung für die Azure Migrate-Ressourcenendpunkte (Ressourcen-FQDNs der privaten Verbindung) durch, und vergewissern Sie sich, dass eine Auflösung in eine private IP-Adresse erfolgt.
Die Details des privaten Endpunkts und die FQDNs der Ressource für die private Verbindung sind auf den Eigenschaftenseiten der Tools Ermittlung und Bewertung und Servermigration verfügbar. Wählen Sie **DNS-Einstellungen herunterladen** aus, um die Liste anzuzeigen.   

 ![Eigenschaften von Azure Migrate: Ermittlung und Bewertung](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 [![Eigenschaften von Azure Migrate: Servermigration](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-expanded.png#lightbox)

Ein anschauliches Beispiel für die DNS-Auflösung des FQDN für die private Verbindung des Speicherkontos.  

-  Geben Sie Folgendes ein: _nslookup ```<storage-account-name>_.blob.core.windows.net.``` Ersetzen Sie dabei ```<storage-account-name>``` durch den Namen des Speicherkontos, das für Azure Migrate verwendet wird.  

    Sie erhalten eine Meldung ähnlich der folgenden:  

   ![Beispiel für die DNS-Auflösung](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

-  Für das Speicherkonto wird als private IP-Adresse 10.1.0.5 zurückgegeben. Diese Adresse gehört zum Subnetz des virtuellen Netzwerks des privaten Endpunkts.   

Sie können die DNS-Auflösung für andere Azure Migrate-Artefakte mithilfe eines ähnlichen Ansatzes überprüfen.   

Führen Sie bei einer nicht korrekten DNS-Auflösung die folgenden Schritte aus:  

Zum Testen **empfohlen**: Sie können die DNS-Einträge Ihrer Quellumgebung manuell aktualisieren, indem Sie die DNS-Datei „hosts“ auf Ihrer lokalen Appliance bearbeiten und die FQDNs der Ressource für die private Verbindung und die zugehörigen privaten IP-Adressen einfügen.
- Wenn Sie ein benutzerdefiniertes DNS verwenden, überprüfen Sie dessen Einstellungen und vergewissern sich, dass die DNS-Konfiguration korrekt ist. Einen entsprechenden Leitfaden finden Sie unter [Was ist privater Endpunkt in Azure? – DNS-Konfiguration](../private-link/private-endpoint-overview.md#dns-configuration).
- Wenn Sie von Azure bereitgestellte DNS-Server verwenden, finden Sie im folgenden Abschnitt weitere Informationen zur Problembehandlung.  

> [!Tip]
> Sie können die DNS-Einträge Ihrer Quellumgebung manuell aktualisieren, indem Sie die DNS-Datei „hosts“ auf Ihrer lokalen Appliance bearbeiten und die FQDNs der Ressource für die private Verbindung und die zugehörigen privaten IP-Adressen einfügen. Diese Option wird nur für Testzwecke empfohlen. <br/>  


## <a name="validate-the-private-dns-zone"></a>Überprüfen der privaten DNS-Zone   
Wenn die DNS-Auflösung nicht wie im vorherigen Abschnitt beschrieben funktioniert, liegt möglicherweise ein Problem mit Ihrer privaten DNS Zone vor.  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Vergewissern, dass die erforderliche private DNS-Zonenressource vorhanden ist  
Standardmäßig erstellt Azure Migrate auch eine private DNS-Zone, die der Unterdomäne *privatelink* für jeden Ressourcentyp entspricht. Die private DNS-Zone wird in derselben Azure-Ressourcengruppe wie die Ressourcengruppe des privaten Endpunkts erstellt. Die Azure-Ressourcengruppe sollte die Ressourcen der privaten DNS-Zone im folgenden Format enthalten:
-  privatelink.vaultcore.azure.net für den Schlüsseltresor
-  privatelink.blob.core.windows.net für das Speicherkonto
-  privatelink.siterecovery.windowsazure.com für den Recovery Services-Tresor (bei Hyper-V- und Agent-basierten Replikationen)
-  privatelink.prod.migration.windowsazure.com für das Migrieren von Projekt, Bewertungsprojekt und Ermittlungsstandort   

Die private DNS-Zone wird automatisch von Azure Migrate erstellt (mit Ausnahme des vom Benutzer ausgewählten Cache-/Replikationsspeicherkontos). Sie können die verknüpfte private DNS-Zone ermitteln, indem Sie zur Seite des privaten Endpunkts navigieren und „DNS-Konfiguration“ auswählen. Hier sollte die private DNS-Zone im Abschnitt „Integration von privatem DNS“ angezeigt werden.

[![Screenshot der DNS-Konfiguration](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-expanded.png#lightbox)

Wenn die DNS-Zone nicht vorhanden ist (wie unten dargestellt), [erstellen Sie eine neue private DNS-Zonenressource](../dns/private-dns-getstarted-portal.md).  

[![Erstellen einer privaten DNS-Zone](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-expanded.png#lightbox)

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Überprüfen der Verknüpfung der privaten DNS-Zone mit dem virtuellen Netzwerk  
Die private DNS-Zone muss mit dem virtuellen Netzwerk verknüpft sein, das den privaten Endpunkt für die DNS-Abfrage enthält, damit die private IP-Adresse des Ressourcenendpunkts aufgelöst werden kann. Wenn die private DNS-Zone nicht mit dem richtigen virtuellen Netzwerk verknüpft ist, ignoriert jegliche DNS-Auflösung aus diesem virtuellen Netzwerk die private DNS-Zone.   

Navigieren Sie im Azure-Portal zur Ressource „Private DNS-Zone“, und wählen Sie im linken Menü „Verknüpfungen virtueller Netzwerke“ aus. Daraufhin sollten die verknüpften virtuellen Netzwerke angezeigt werden.

[![Anzeigen von Verknüpfungen virtueller Netzwerke](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-expanded.png#lightbox)

Dadurch wird eine Liste mit Links angezeigt, die jeweils den Namen eines virtuellen Netzwerks in Ihrem Abonnement enthalten. Das virtuelle Netzwerk, das die Ressource des privaten Endpunkts enthält, muss hier aufgeführt sein. Befolgen Sie andernfalls [diesen Artikel](../dns/private-dns-getstarted-portal.md#link-the-virtual-network), um die private DNS-Zone mit einem virtuellen Netzwerk zu verknüpfen.    

Nachdem die private DNS-Zone mit dem virtuellen Netzwerk verknüpft ist, suchen DNS-Anforderungen aus dem virtuellen Netzwerk nach DNS-Einträgen in der privaten DNS-Zone. Dies ist für eine korrekte Adressenauflösung in dem virtuellen Netzwerk erforderlich, in dem der private Endpunkt erstellt wurde.   

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Überprüfen der A-Datensätze der privaten DNS-Zone

Wechseln Sie zur privaten DNS-Zone, für die Sie die Problembehandlung ausführen möchten. Auf der Übersichtsseite werden alle DNS-Einträge für diese private DNS-Zone angezeigt. Überprüfen Sie, ob für die Ressource ein DNS-A-Datensatz vorhanden ist. Der Wert des A-Datensatzes (die IP-Adresse) muss die private IP-Adresse der Ressource sein. Wenn Sie den A-Datensatz finden, dieser aber die falsche IP-Adresse enthält, müssen Sie die falsche IP-Adresse entfernen und eine neue hinzufügen. Es wird empfohlen, den gesamten A-Datensatz zu entfernen, einen neuen Eintrag hinzuzufügen und dann eine DNS-Leerung auf der lokalen Quellappliance durchführen.   

Ein anschauliches Beispiel für den DNS-A-Datensatz des Speicherkontos in der privaten DNS-Zone:

   ![DNS-Datensätze](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Ein anschauliches Beispiel für den DNS-A-Datensatz der Microservices des Recovery Services-Tresors in der privaten DNS-Zone:

   [![DNS-Einträge für den Recovery Services-Tresor](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-expanded.png#lightbox)  

>[!Note]
> Wenn Sie einen A-Datensatz entfernen oder ändern, kann der Computer weiterhin zu der alten IP-Adresse auflösen, da der TTL-Wert (Time to Live, Gültigkeitsdauer) möglicherweise noch nicht abgelaufen ist.  

### <a name="items-that-may-affect-private-link-connectivity"></a>Aspekte, die sich auf die Konnektivität einer privaten Verbindung auswirken können  

Dies ist eine unvollständige Liste von Elementen, die in erweiterten oder komplexen Szenarien zu finden sind:

-  Firewalleinstellungen: entweder die mit dem virtuellen Netzwerk verbundene Azure Firewall oder eine benutzerdefinierte Firewalllösung, die auf dem Computer mit der Appliance bereitgestellt wird  
-  Netzwerkpeering, das beeinträchtigen kann, welche DNS-Server verwendet werden und wie Datenverkehr weitergeleitet wird.  
-  Benutzerdefinierte Gatewaylösungen (NAT) beeinträchtigen möglicherweise, wie Datenverkehr weitergeleitet wird, einschließlich Datenverkehr von DNS-Abfragen.

Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung bei Konnektivitätsproblemen mit privaten Endpunkten](../private-link/troubleshoot-private-endpoint-connectivity.md).  

## <a name="common-issues-while-using-azure-migrate-with-private-endpoints"></a>Häufige Probleme bei der Verwendung von Azure Migrate mit privaten Endpunkten
In diesem Abschnitt werden einige der häufig auftretenden Probleme aufgeführt und Schritte zur Problembehandlung vorgeschlagen, die Sie selbst ausführen können, um das Problem zu beheben.

### <a name="appliance-registration-fails-with-the-error-forbiddentoaccesskeyvault"></a>Fehler bei der Applianceregistrierung: ForbiddenToAccessKeyVault
Azure Key Vault-Erstellungs- oder Aktualisierungsvorgang für <_KeyVaultName_> aufgrund des Fehlers <_Fehlermeldung_> fehlgeschlagen

#### <a name="possible-causes"></a>Mögliche Ursachen:
Dieses Problem kann auftreten, wenn das zum Registrieren der Appliance verwendete Azure-Konto nicht über die erforderlichen Berechtigungen verfügt oder die Azure Migrate-Appliance nicht auf die Key Vault-Instanz zugreifen kann.

#### <a name="remediation"></a>Abhilfe:

**Schritte zum Behandeln von Key Vault-Zugriffsproblemen**:
1. Stellen Sie sicher, dass das für die Registrierung der Appliance verwendete Azure-Benutzerkonto mindestens über „Mitwirkender“-Berechtigungen für das Abonnement verfügt.
1. Stellen Sie sicher, dass der Benutzer, der versucht, die Appliance zu registrieren, Zugriff auf die Key Vault-Instanz hat und dass im Abschnitt „Key Vault > Zugriffsrichtlinie“ eine Zugriffsrichtlinie zugewiesen ist. [Weitere Informationen](../key-vault/general/assign-access-policy-portal.md)
-  Weitere Informationen zu den erforderlichen Azure-Rollen und -Berechtigungen finden Sie [hier](./migrate-appliance.md#appliance---vmware).

**Schritte zum Beheben von Konnektivitätsproblemen mit der Key Vault-Instanz**: Wenn Sie die Appliance für private Endpunktkonnektivität aktiviert haben, führen Sie die folgenden Schritte aus, um Probleme mit der Netzwerkkonnektivität zu beheben:
-  Stellen Sie sicher, dass die Appliance entweder im gleichen virtuellen Netzwerk gehostet wird oder über eine private Verbindung mit dem virtuellen Azure-Zielnetzwerk (in dem der private Key Vault-Endpunkt erstellt wurde) verbunden ist. Der private Key Vault-Endpunkt wird in dem virtuellen Netzwerk erstellt, das während der Projekterstellung ausgewählt wurde. Sie können die Details des virtuellen Netzwerks auf der Seite **Azure Migrate > Eigenschaften** überprüfen.
   ![Azure Migrate-Eigenschaften](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)  

-  Stellen Sie sicher, dass die Appliance über eine private Verbindung mit der Key Vault-Instanz verbunden ist. Um die Konnektivität der privaten Verbindung zu überprüfen, führen Sie eine DNS-Auflösung des Key Vault-Ressourcenendpunkts von dem lokalen Server aus, auf dem die Appliance gehostet wird, und stellen Sie sicher, dass er in eine private IP-Adresse aufgelöst wird.
-  Navigieren Sie zu **Azure Migrate: Ermittlung und Bewertung > Eigenschaften**, um die Details privater Endpunkte für Ressourcen wie die Key Vault-Instanz zu ermitteln, die während des Schritts der Schlüssgenerierung erstellt wurden.  

      ![Azure Migrate-Serverbewertungseigenschaften](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
-  Wählen Sie **DNS-Einstellungen herunterladen** aus, um die DNS-Zuordnungen herunterzuladen.

      ![Herunterladen von DNS-Einstellungen](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)  

-  Öffnen Sie die Befehlszeile, und führen Sie den folgenden nslookup-Befehl aus, um die Netzwerkkonnektivität mit der in der DNS-Einstellungsdatei angegebenen Key Vault-URL zu überprüfen.   

    ```console
    nslookup <your-key-vault-name>.vault.azure.net
    ```

    Wenn Sie den Befehl „ns lookup“ ausführen, um die IP-Adresse eines Schlüsseltresors über einen öffentlichen Endpunkt aufzulösen, sieht das Ergebnis wie folgt aus:

    ```console
    c:\ >nslookup <your-key-vault-name>.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  (public IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
    ```

    Wenn Sie den Befehl „ns lookup“ ausführen, um die IP-Adresse eines Schlüsseltresors über einen privaten Endpunkt aufzulösen, sieht das Ergebnis wie folgt aus:

    ```console
    c:\ >nslookup your_vault_name.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.20 (private IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
              <your-key-vault-name>.privatelink.vaultcore.azure.net
    ```

    Der nslookup-Befehl sollte wie oben erwähnt in eine private IP-Adresse aufgelöst werden. Die private IP-Adresse sollte mit der in der DNS-Einstellungsdatei aufgeführten IP-Adresse übereinstimmen.

Führen Sie bei einer nicht korrekten DNS-Auflösung die folgenden Schritte aus:

1. Aktualisieren Sie die DNS-Einträge Ihrer Quellumgebung manuell, indem Sie die DNS-Datei „hosts“ auf Ihrer lokalen Appliance bearbeiten und die DND-Zuordnungen und die zugehörigen privaten IP-Adressen einfügen. Diese Option wird für Testzwecke empfohlen.

      ![DNS-Datei „hosts“](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

1. Wenn Sie einen benutzerdefinierten DNS-Server verwenden, überprüfen Sie ihre benutzerdefinierten Einstellungen und vergewissern sich, dass die DNS-Konfiguration richtig ist. Einen entsprechenden Leitfaden finden Sie unter [Was ist privater Endpunkt in Azure? – DNS-Konfiguration](../private-link/private-endpoint-overview.md#dns-configuration).

1. **Überlegungen zum Proxyserver:** Wenn die Appliance einen Proxyserver für ausgehende Konnektivität verwendet, müssen Sie möglicherweise Ihre Netzwerkeinstellungen und -konfigurationen überprüfen, um sicherzustellen, dass die URLs für private Verbindungen erreichbar sind und das Routing wie erwartet funktionieren kann.

    - Wenn der Proxyserver für Internetkonnektivität verwendet wird, müssen Sie möglicherweise Weiterleitungen oder Regeln für den Datenverkehr hinzufügen, damit anstelle des Proxyservers die FQDNs für die privaten Verbindungen verwendet werden. Weitere Informationen zum Hinzufügen von Regeln zur Umgehung von Proxys finden Sie [im verlinkten Artikel](/azure/migrate/how-to-use-azure-migrate-with-private-endpoints#set-up-prerequisites).
    - Wenn der Proxyserver für den gesamten ausgehenden Datenverkehr verwendet wird, stellen Sie sicher, dass der Proxyserver die FQDNs für die privaten Verbindungen in die entsprechenden privaten IP-Adressen auflösen kann. Als schnelle Problemumgehung können Sie die DNS-Einträge auf dem Proxyserver wie oben gezeigt manuell mit den DNS-Zuordnungen und den zugehörigen privaten IP-Adressen aktualisieren. Diese Option wird für Testzwecke empfohlen.

1. Wenn das Problem weiterhin besteht, finden Sie in [diesem Abschnitt](#validate-the-private-dns-zone) weitere Informationen zur Problembehandlung.

Nachdem Sie die Konnektivität überprüft haben, wiederholen Sie den Registrierungsvorgang.

### <a name="start-discovery-fails-with-the-error-agentnotconnected"></a>Fehler bei der Startermittlung: AgentNotConnected
Die Appliance konnte keine Erkennung initiieren, da der lokale Agent nicht mit dem Endpunkt des Azure Migrate-Diensts kommunizieren kann: <_URLname_> in Azure.

   ![Fehler „Agent nicht verbunden“](./media/how-to-use-azure-migrate-with-private-endpoints/agent-not-connected-error.png)  

#### <a name="possible-causes"></a>Mögliche Ursachen:
Dieses Problem kann auftreten, wenn die Appliance die in der Fehlermeldung genannten Dienstendpunkte nicht erreichen kann.

#### <a name="remediation"></a>Abhilfe:
Stellen Sie sicher, dass die Appliance entweder direkt oder über einen Proxy verbunden ist und den in der Fehlermeldung angegebenen Dienstendpunkt auflösen kann.  

Wenn Sie die Appliance für private Endpunktkonnektivität aktiviert haben, stellen Sie sicher, dass die Appliance über eine private Verbindung mit dem virtuellen Azure-Netzwerk verbunden ist und die in der Fehlermeldung angegebenen Dienstendpunkte auflösen kann.

**Schritte zum Beheben von Problemen mit der Konnektivität der privaten Verbindung mit Azure Migrate Dienstendpunkten:**

Wenn Sie die Appliance für private Endpunktkonnektivität aktiviert haben, führen Sie die folgenden Schritte aus, um Probleme mit der Netzwerkkonnektivität zu beheben:

-  Stellen Sie sicher, dass die Appliance entweder im gleichen virtuellen Netzwerk gehostet wird oder über eine private Verbindung mit dem virtuellen Azure-Zielnetzwerk (in dem die privaten Endpunkte erstellt wurden) verbunden ist. Private Endpunkte für die Azure Migrate-Dienste werden in dem virtuellen Netzwerk erstellt, das während der Projekterstellung ausgewählt wurde. Sie können die Details des virtuellen Netzwerks auf der Seite **Azure Migrate > Eigenschaften** überprüfen.

      ![Azure Migrate-Eigenschaften](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)   

-  Stellen Sie sicher, dass die Appliance über eine private Verbindung mit den Dienstendpunkt-URLs und anderen URLs verbunden ist, die in der Fehlermeldung erwähnt werden. Um die Konnektivität der privaten Verbindung zu überprüfen, führen Sie eine DNS-Auflösung der URLs von dem lokalen Server aus, auf dem die Appliance gehostet wird, und stellen Sie sicher, dass sie in private IP-Adressen aufgelöst werden.
-  Navigieren Sie zu **Azure Migrate: Ermittlung und Bewertung > Eigenschaften**, um die Details privater Endpunkte für die Dienstendpunkte zu ermitteln, die während des Schritts der Schlüssgenerierung erstellt wurden. 

      ![Azure Migrate-Serverbewertungseigenschaften](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  

- Wählen Sie **DNS-Einstellungen herunterladen** aus, um die DNS-Zuordnungen herunterzuladen.

   ![Herunterladen von DNS-Einstellungen](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)   

|**DNS-Zuordnungen mit URLs für private Endpunkte**  | **Details** |
|--- | ---|
|*.disc.privatelink.test.migration.windowsazure.com | Azure Migrate Discovery-Dienstendpunkt
|*.asm.privatelink.test.migration.windowsazure.com  | Azure Migrate Assessment-Dienstendpunkt  
|*.hub.privatelink.test.migration.windowsazure.com  | Azure Migrate-Hubendpunkt zum Empfangen von Daten aus anderen Microsoft- oder externen Angeboten von [unabhängigen Softwareanbietern (ISVs)](./migrate-services-overview.md#isv-integration)
|*.vault.azure.net | Key Vault-Endpunkt
|*.blob.core.windows.net | Speicherkontoendpunkt für Abhängigkeits- und Leistungsdaten  

Zusätzlich zu den oben genannten URLs benötigt die Appliance direkt oder über einen Proxy Zugriff auf die folgenden URLs über das Internet.

| **Andere URLs der öffentlichen Cloud <br> (Öffentliche Endpunkt-URLs)** | **Details** |
|--- | ---|
|*.portal.azure.com | Navigieren Sie zum Azure-Portal.
|*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> | Verwendet für Zugriffssteuerung und Identitätsverwaltung durch Azure Active Directory
|management.azure.com | Zum Auslösen von Azure Resource Manager-Bereitstellungen
|*.services.visualstudio.com (optional) | Hochladen von Applianceprotokollen, die für die interne Überwachung verwendet werden.
|aka.ms/* (optional) | Zulassen des Zugriffs auf aka-Links; die für das Herunterladen und Installieren der neuesten Updates für Appliancedienste verwendet werden
|download.microsoft.com/download | Zulassen von Downloads aus Microsoft Download Center    

-  Öffnen Sie die Befehlszeile, und führen Sie den folgenden nslookup-Befehl aus, um die Konnektivität der privaten Verbindung mit den in der DNS-Einstellungsdatei angegebenen URLs zu überprüfen. Wiederholen Sie diesen Schritt für alle URLs in der DNS-Einstellungsdatei.

    _**Abbildung**: Überprüfen der Konnektivität der privaten Verbindung mit dem Endpunkt des Ermittlungsdiensts_

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
    ```
    Wenn die Anforderung den Ermittlungsendpunkt über einen privaten Endpunkt erreichen kann, wird ein Ergebnis angezeigt, das wie folgt aussieht:

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.23 (private IP address)
    Aliases:  04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
              prod.cus.discoverysrv.windowsazure.com
    ```

    Der nslookup-Befehl sollte wie oben erwähnt in eine private IP-Adresse aufgelöst werden. Die private IP-Adresse sollte mit der in der DNS-Einstellungsdatei aufgeführten IP-Adresse übereinstimmen.

Führen Sie bei einer nicht korrekten DNS-Auflösung die folgenden Schritte aus:

1. Aktualisieren Sie die DNS-Einträge Ihrer Quellumgebung manuell, indem Sie die DNS-Datei „hosts“ auf Ihrer lokalen Appliance bearbeiten und die DND-Zuordnungen und die zugehörigen privaten IP-Adressen einfügen. Diese Option wird für Testzwecke empfohlen.

      ![DNS-Datei „hosts“](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

1. Wenn Sie einen benutzerdefinierten DNS-Server verwenden, überprüfen Sie ihre benutzerdefinierten Einstellungen und vergewissern sich, dass die DNS-Konfiguration richtig ist. Einen entsprechenden Leitfaden finden Sie unter [Was ist privater Endpunkt in Azure? – DNS-Konfiguration](../private-link/private-endpoint-overview.md#dns-configuration).

1. **Überlegungen zum Proxyserver:** Wenn die Appliance einen Proxyserver für ausgehende Konnektivität verwendet, müssen Sie möglicherweise Ihre Netzwerkeinstellungen und -konfigurationen überprüfen, um sicherzustellen, dass die URLs für private Verbindungen erreichbar sind und das Routing wie erwartet funktionieren kann.

    - Wenn der Proxyserver für Internetkonnektivität verwendet wird, müssen Sie möglicherweise Weiterleitungen oder Regeln für den Datenverkehr hinzufügen, damit anstelle des Proxyservers die FQDNs für die privaten Verbindungen verwendet werden. Weitere Informationen zum Hinzufügen von Regeln zur Umgehung von Proxys finden Sie [im verlinkten Artikel](/azure/migrate/how-to-use-azure-migrate-with-private-endpoints#set-up-prerequisites).
    - Wenn der Proxyserver für den gesamten ausgehenden Datenverkehr verwendet wird, stellen Sie sicher, dass der Proxyserver die FQDNs für die privaten Verbindungen in die entsprechenden privaten IP-Adressen auflösen kann. Als schnelle Problemumgehung können Sie die DNS-Einträge auf dem Proxyserver wie oben gezeigt manuell mit den DNS-Zuordnungen und den zugehörigen privaten IP-Adressen aktualisieren. Diese Option wird für Testzwecke empfohlen.

1. Wenn das Problem weiterhin besteht, finden Sie in [diesem Abschnitt](#validate-the-private-dns-zone) weitere Informationen zur Problembehandlung.

Nachdem Sie die Konnektivität überprüft haben, wiederholen Sie den Ermittlungsvorgang.

### <a name="importexport-request-fails-with-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>Fehler bei Import/Export-Anforderung: „403: Diese Anforderung ist nicht zum Ausführen des Vorgangs autorisiert.“ 

Die Anforderung zum Exportieren/Importieren/Herunterladen des Berichts ist nicht erfolgreich, und für Projekte mit privater Endpunktverbindung wird folgende Fehlermeldung angezeigt: *403: Diese Anforderung ist nicht zum Ausführen des Vorgangs autorisiert.*

#### <a name="possible-causes"></a>Mögliche Ursachen: 
Dieser Fehler kann auftreten, wenn die Anforderung zum Exportieren/Importieren/Herunterladen des Berichts nicht über ein autorisiertes Netzwerk eingeleitet wurde. Dieser Fall kann vorliegen, wenn die Anforderung zum Exportieren/Importieren/Herunterladen des Berichts von einem Client eingeleitet wurde, der nicht über ein privates Netzwerk mit dem Azure Migrate-Dienst (Azure Virtual Network) verbunden ist. 

#### <a name="remediation"></a>Wiederherstellung
**Option 1** *(empfohlen)* :
  
Wiederholen Sie zur Beseitigung dieses Fehlers den Import/Export/Download-Vorgang von einem Client aus, der sich in einem virtuellen Netzwerk befindet, das über eine private Verbindung mit Azure verbunden ist. Sie können das Azure-Portal in Ihrem lokalen Netzwerk oder auf Ihrer Appliance-VM öffnen und den Vorgang wiederholen. 

**Option 2**:

Die Anforderung zum Import/Export/Download stellt eine Verbindung mit einem Speicherkonto her, um Berichte hoch- oder herunterzuladen. Sie können auch die Netzwerkeinstellungen des Speicherkontos ändern, das für den Import/Export/Download-Vorgang verwendet wird, und den Zugriff auf das Speicherkonto über andere Netzwerke (öffentliche Netzwerke) zulassen.  

So richten Sie das Speicherkonto für die Konnektivität mit öffentlichen Endpunkten ein

1. **Suchen Sie das Speicherkonto**: Der Name des Speicherkontos ist auf der Eigenschaftenseite „Azure Migrate: Ermittlung und Bewertung“ verfügbar. Der Name des Speicherkontos weist das Suffix *usa* auf. 

      :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png" alt-text="Screenshot: Option „DNS-Einstellungen herunterladen“"::: 

2. Navigieren Sie zum Speicherkonto, und bearbeiten Sie die Netzwerkeigenschaften des Speicherkontos, um den Zugriff von allen/anderen Netzwerken zuzulassen. 

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall-virtual-networks.png" alt-text="Screenshot: Netzwerkeigenschaften des Speicherkontos":::

3. Alternativ können Sie den Zugriff auf ausgewählte Netzwerke einschränken und die öffentliche IP-Adresse des Clients hinzufügen, von dem aus Sie auf das Azure-Portal zugreifen möchten.  

      :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall.png" alt-text="Screenshot: Hinzufügens der öffentlichen IP-Adresse des Clients":::