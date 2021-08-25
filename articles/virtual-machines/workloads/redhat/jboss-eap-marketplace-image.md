---
title: Azure Marketplace-Angebot für Red Hat JBoss EAP in Red Hat Enterprise Linux-VMs und -VM-Skalierungsgruppen in Azure
description: Hier erfahren Sie, wie Sie Red Hat JBoss EAP mithilfe des Azure Marketplace-Angebots für Red Hat Enterprise Linux-VMs (RHEL) und -VM-Skalierungsgruppen in Azure bereitstellen.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 05/25/2021
ms.openlocfilehash: 3c819367f35cb4a8174abaac1380eb439ace206a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769705"
---
# <a name="deploy-red-hat-jboss-enterprise-platform-eap-on-azure-vms-and-virtual-machine-scale-sets-using-the-azure-marketplace-offer"></a>Bereitstellen von Red Hat JBoss Enterprise Platform (EAP) in VMs und VM-Skalierungsgruppen in Azure mithilfe des Azure Marketplace-Angebots

Die Azure Marketplace-Angebote für [Red Hat JBoss Enterprise Application Platform](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) für Red Hat Enterprise Linux [(RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) in Azure sind eine gemeinsame Lösung von [Red Hat](https://www.redhat.com/) und Microsoft. Red Hat ist ein führender Anbieter von Open-Source-Lösungen und Mitwirkender u. a. an [Java](https://www.java.com/)-Standards, [OpenJDK](https://openjdk.java.net/), [MicroProfile](https://microprofile.io/), [Jakarta EE](https://jakarta.ee/) und [Quarkus](https://quarkus.io/). JBoss EAP ist eine führende Java-Anwendungsserverplattform, die sowohl in Web Profile als auch Full Platform Java EE-zertifiziert und Jakarta EE-konform ist. Jedes JBoss EAP-Release wird für verschiedene marktführende Betriebssysteme, Java Virtual Machines (JVMs) und Datenbankkombinationen getestet und unterstützt.  In JBoss EAP und RHEL ist alles enthalten, was Sie in beliebigen Umgebungen zum Erstellen, Ausführen, Bereitstellen und Verwalten von Java-Unternehmensanwendungen benötigen.  Die Lösung steht für lokale, virtuelle Umgebungen und private, öffentliche oder Hybrid Clouds zur Verfügung. Die gemeinsame Lösung von Red Hat und Microsoft bietet integrierten Support und Flexibilität bei der Softwarelizenzierung.

## <a name="jboss-eap-on-azure-integrated-support"></a>Integrierter Support für JBoss EAP in Azure

Das Azure Marketplace-Angebot „JBoss EAP“ ist eine gemeinsame Lösung von Red Hat und Microsoft, das integrierten Support und Flexibilität bei der Softwarelizenzierung bietet. Sie können Supporttickets sowohl bei Microsoft als auch bei Red Hat einreichen.  Wir beheben die Probleme gemeinsam, damit Sie nicht mehrere Tickets bei beiden Anbietern einreichen müssen. Der integrierte Support deckt alle Supportprobleme auf Ebene der Azure-Infrastruktur und des Red Hat-Anwendungsservers ab.    

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Konto mit aktivem Abonnement: Wenn Sie kein Azure-Abonnement haben, können Sie Ihre [Visual Studio Subscription-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) (ehemals MSDN) aktivieren oder [ein kostenloses Konto erstellen](https://azure.microsoft.com/pricing/free-trial).

* JBoss EAP-Installation: Sie benötigen ein Red Hat-Konto mit RHSM-Berechtigung (Red Hat Subscription Management) für JBoss EAP. Mit dieser Berechtigung können Sie die für Red Hat getestete und zertifizierte JBoss EAP-Version herunterladen.  Wenn Sie keine EAP-Berechtigung haben, registrieren Sie sich über [Red Hat Developer Subscription for Individuals](https://developers.redhat.com/register) für ein kostenloses Entwicklerabonnement. Nach der Registrierung finden Sie die erforderlichen Anmeldeinformationen (Pool-IDs) im [Red Hat-Kundenportal](https://access.redhat.com/management/).

* RHEL-Optionen: Wählen Sie zwischen „Nutzungsbasierte Zahlung“ und „Bring-Your-Own-Subscription (BYOS)“. Bei BYOS müssen Sie Ihr [RHEL Gold Image](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/) für [Red Hat Cloud Access](https://access.redhat.com/) aktivieren, bevor Sie das Marketplace-Angebot mithilfe der Lösungsvorlage bereitstellen. Befolgen Sie [diese Anweisungen](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index), um RHEL Gold-Images für den Einsatz in Microsoft Azure zu aktivieren.

* Azure-Befehlszeilenschnittstelle (CLI).

## <a name="azure-marketplace-offer-subscription-options"></a>Abonnementoptionen für Azure Marketplace-Angebote

Das Azure Marketplace-Angebot „JBoss EAP unter RHEL“ wird für Azure-VMs bzw. -VM-Skalierungsgruppen in weniger als 10 Minuten installiert und bereitgestellt. Sie können im [Azure Marketplace](https://azuremarketplace.microsoft.com/) auf diese Angebote zugreifen.

Das Marketplace-Angebot umfasst verschiedene Kombinationen von EAP- und RHEL-Versionen zum Erfüllen Ihrer jeweiligen Anforderungen. Füg JBoss EAP gilt immer BYOS, aber für das RHEL-Betriebssystem können Sie zwischen BYOS oder nutzungsbasierter Zahlung wählen. Das Angebot umfasst Plankonfigurationsoptionen für JBoss EAP unter RHEL als eigenständige, gruppierte VMs oder gruppierte VM-Skalierungsgruppen. 

Diese sechs Pläne sind verfügbar: 

- JBoss EAP 7.3 unter RHEL 8.3 (eigenständige VM, nutzungsbasierte Zahlung)
- JBoss EAP 7.3 unter RHEL 8.3 (eigenständige VM, BYOS)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VMs, nutzungsbasierte Zahlung)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VMs, BYOS)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VM-Skalierungsgruppen, nutzungsbasierte Zahlung)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VM-Skalierungsgruppen, BYOS)

## <a name="using-rhel-os-with-payg-model"></a>Verwenden des RHEL-Betriebssystems mit nutzungsbasierter Zahlung

Mit dem Azure Marketplace-Angebot können Sie RHEL bedarfsorientiert als VMs/VM-Skalierungsgruppen mit nutzungsbasierter Zahlung bereitstellen. Für Pläne mit nutzungsbasierter Zahlung fallen zusätzlich zu den üblichen Compute-, Netzwerk- und Speicherkosten für die Azure-Infrastruktur stündliche RHEL-Abonnementgebühren an.

Ausführliche Informationen zur nutzungsbasierten Zahlung finden Sie unter [Virtuelle Red Hat-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Um RHEL im Modell mit nutzungsbasierter Zahlung einsetzen zu können, benötigen Sie ein Azure-Abonnement. (Für die Angebote zu [RHEL in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.rhel-20190605) muss im Azure-Abonnement eine Zahlungsmethode angegeben werden.)

## <a name="using-rhel-os-with-byos-model"></a>Verwenden des Betriebssystems RHEL mit BYOS

Um RHEL für VMs/VM-Skalierungsgruppen mit BYOS einsetzen zu können, müssen Sie über ein gültiges Red Hat-Abonnement mit Berechtigungen zur Nutzung von RHEL in Azure verfügen. Diese BYOS-Pläne für JBoss EAP unter RHEL sind als [private Azure-Angebote](../../../marketplace/private-offers.md) verfügbar. Sie müssen unbedingt die folgenden Voraussetzungen zum Bereitstellen eines BYOS-Angebotsplans für RHEL über Azure Marketplace erfüllen. 

1. Stellen Sie sicher, dass Sie unter Ihrem Red Hat-Abonnement über Berechtigungen für das RHEL-Betriebssystem und für JBoss EAP verfügen.
2. Führen Sie für Ihre Azure-Abonnement-ID die Autorisierung zur Nutzung von RHEL-Images mit BYOS durch. Halten Sie sich an die [RHSM-Dokumentation (Red Hat Subscription Management)](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index), um diesen Prozess durchzuführen. Dies umfasst die folgenden Schritte:
    1. Aktivieren Sie Microsoft Azure in Ihrem Red Hat Cloud Access-Dashboard als Anbieter.
    2. Fügen Sie Ihre Azure-Abonnement-IDs hinzu.
    3. Aktivieren Sie neue Produkte für Cloud Access unter Microsoft Azure.
    4. Aktivieren Sie Red Hat Gold Images für Ihr Azure-Abonnement. Ausführlichere Informationen finden Sie im Kapitel zum Thema [Aktivieren und Verwalten von Abonnements für Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#using-gold-images-on-azure_cloud-access#using-gold-images-on-azure_cloud-access). 
    5. Warten Sie ab, bis die Red Hat Gold Images in Ihrem Azure-Abonnement verfügbar sind. Diese Gold-Images sind in der Regel innerhalb von maximal 3 Stunden nach Einreichung als private Azure-Angebote verfügbar.

3. Akzeptieren Sie die Azure Marketplace-Geschäftsbedingungen für RHEL-Images mit BYOS. Führen Sie dazu die [Azure CLI](/cli/azure/install-azure-cli)-Befehl wie nachstehend angegeben aus. Ausführlichere Informationen finden Sie in der Dokumentation unter [Bring-Your-Own-Subscription-Gold-Images für Red Hat Enterprise Linux in Azure](./byos.md). Es ist wichtig, dass Sie die neueste Azure CLI-Version ausführen.
    1. Starten Sie eine Azure CLI-Sitzung, und authentifizieren Sie sich mit Ihrem Azure-Konto. Hilfreiche Informationen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli). Stellen Sie vor dem Fortfahren sicher, dass Sie die neueste Azure CLI-Version ausführen.
    2. Vergewissern Sie sich, dass die RHEL-Pläne mit BYOS für Ihr Abonnement verfügbar sind, indem Sie den folgenden CLI-Befehl ausführen. Wenn Sie hier keine Ergebnisse erhalten, lesen Sie Schritt 2. Stellen Sie sicher, dass Ihr Azure-Abonnement mit der Berechtigung für JBoss EAP für BYOS-Pläne unter RHEL aktiviert ist.

    ```cli
    az vm image list --offer rhel-byos --all #use this command to verify the availability of RHEL BYOS images
    ```

    3. Führen Sie den folgenden Befehl aus, um die Azure Marketplace-Geschäftsbedingungen wie für JBoss EAP im BYOS-Plan für RHEL erforderlich zu akzeptieren.

    ```cli
    az vm image terms accept --publisher redhat --offer jboss-eap-rhel --plan $PLANID
    ```

    4. Dabei ist `$PLANID` eine der folgenden (wiederholen Sie Schritt 3 für jeden gewünschten Marketplace-Angebotsplan):

    ```cli
    jboss-eap-73-byos-rhel-80-byos
    jboss-eap-73-byos-rhel-8-byos-clusteredvm
    jboss-eap-73-byos-rhel-80-byos-vmss
    jboss-eap-73-byos-rhel-80-payg
    jboss-eap-73-byos-rhel-8-payg-clusteredvm
    jboss-eap-73-byos-rhel-80-payg-vmss
    ```

4. Ihr Abonnement ist jetzt für die Bereitstellung von BYOS-Plänen von EAP unter RHEL bereit. Während der Bereitstellung werden Ihre Abonnements automatisch mithilfe von `subscription-manager` mit den Anmeldeinformationen angefügt, die Sie bei der Bereitstellung angegeben haben.

## <a name="using-jboss-eap-with-byos-model"></a>Verwenden von JBoss EAP mit BYOS-Modell

In Azure ist JBoss EAP nur mit dem BYOS-Modell verfügbar. Beim Bereitstellen Ihres Plans für JBoss EAP unter RHEL müssen Sie Ihre RHSM-Anmeldeinformationen mit der RHSM-Pool-ID und gültigen EAP-Berechtigungen angeben. Wenn Sie keine EAP-Berechtigung haben, erwerben Sie ein [Red Hat Developer Subscription for Individuals](https://developers.redhat.com/register). Nach der Registrierung finden Sie die erforderlichen Anmeldeinformationen (Pool-IDs) im Navigationsmenü „Subscription“ (Abonnement). Während der Bereitstellung werden Ihre Abonnements automatisch mithilfe von `subscription-manager` mit den Anmeldeinformationen angefügt, die Sie bei der Bereitstellung angegeben haben.

## <a name="template-solution-architectures"></a>Lösungsarchitekturen von Vorlagen

Mit diesen Angebotsplänen werden alle Azure-Computeressourcen zum Ausführen des Setups von JBoss EAP unter RHEL erstellt. Die folgenden Ressourcen werden von der Lösungsvorlage erstellt:

- RHEL-VM
- Ein Load Balancer (für Clusterkonfiguration)
- Virtuelles Netzwerk mit einzelnem Subnetz
- JBoss EAP-Setup in einer RHEL-VM/VM-Skalierungsgruppe (eigenständig oder gruppiert)
- Java-Beispielanwendung
- Speicherkonto!

## <a name="after-a-successful-deployment"></a>Nach erfolgreicher Bereitstellung

1. [Erstellen Sie eine Jump-VM mit VNet-Peering](../../windows/quick-create-portal.md#create-virtual-machine) in einem anderen virtuellen Netzwerk. Greifen Sie auf den Server zu, und machen Sie die Anwendung mittels [Peering virtueller Netzwerke](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) verfügbar.
2. [Erstellen Sie eine öffentliche IP-Adresse](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) für den Zugriff auf Server und Anwendung.
3. [Erstellen Sie eine Jump-VM im gleichen virtuellen Netzwerk (VNet)](../../windows/quick-create-portal.md#create-virtual-machine) in einem anderen (bzw. neuen) Subnetz im gleichen VNet, und greifen Sie über eine Jump-VM auf den Server zu. Mithilfe der Jump-VM kann die Anwendung verfügbar gemacht werden.
4. Machen Sie die Anwendung mithilfe einer [Application Gateway](../../../application-gateway/quick-create-portal.md#create-an-application-gateway)-Instanz verfügbar.
5. Machen Sie die Anwendung mithilfe eines externen Lastenausgleichs verfügbar.
6. [Verwenden Sie Azure Bastion,](../../../bastion/bastion-overview.md) um über Ihren Browser und das Azure-Portal auf Ihre RHEL-VMs zuzugreifen. 

### <a name="1-create-a-jump-vm-in-a-different-virtual-network-and-access-the-rhel-vm-using-virtual-network-peering-recommended-method"></a>1. Erstellen einer Jump-VM in einem anderen virtuellen Netzwerk und Zugreifen auf die RHEL-VM mittels Peering virtueller Netzwerke (empfohlene Methode)

1. [Erstellen Sie einen virtuellen Windows-Computer](../../windows/quick-create-portal.md#create-virtual-machine): Erstellen Sie in einer neuen Azure-Ressourcengruppe eine Windows-VM, die sich in derselben Region wie die RHEL-VM befinden muss. Geben Sie die erforderlichen Details an, und übernehmen Sie für andere Konfigurationen die Standardeinstellungen, da die Jump-VM in einem neuen virtuellen Netzwerk erstellt wird.
2. [Richten Sie ein Peering virtueller Netzwerke ein](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks): Per Peering verknüpfen Sie die RHEL-VM mit der Jump-VM. Nach erfolgreichem Peering virtueller Netzwerke können beide VMs miteinander kommunizieren.
3. Wechseln Sie zur Detailseite der Jump VM, und kopieren Sie die öffentliche IP-Adresse. Melden Sie sich bei der Jump-VM mit der öffentlichen IP-Adresse an.
4. Kopieren Sie auf der Seite „Ausgabe“ die private IP-Adresse der RHEL-VM, und melden Sie sich damit auf der Jump-VM bei der RHEL-VM an.
5. Fügen Sie die App-URL, die Sie auf der Seite „Ausgabe“ kopiert haben, auf der Jump-VM in einen Browser ein. Zeigen Sie die Webseite „JBoss EAP in Azure“ in diesem Browser an.
6. Greifen Sie auf die JBoss EAP-Verwaltungskonsole zu: Fügen Sie die auf der Seite „Ausgabe“ kopierte URL der Verwaltungskonsole auf der Jump-VM in einen Browser ein. Geben Sie Benutzername und Kennwort für JBoss EAP ein, um sich anzumelden.

### <a name="2-create-a-public-ip-to-access-the-rhel-vm-and-jboss-eap-admin-console"></a>2. Erstellen einer öffentlichen IP-Adresse für den Zugriff auf die RHEL-VM und JBoss EAP-Verwaltungskonsole

1. Der von Ihnen erstellten RHEL-VM ist keine öffentliche IP-Adresse zugeordnet. Sie können [eine öffentliche IP-Adresse](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) für den Zugriff auf die VM erstellen und [die öffentliche IP-Adresse der VM zuordnen](../../../virtual-network/associate-public-ip-address-vm.md). Das Erstellen einer öffentlichen IP-Adresse kann über das Azure-Portal bzw. [Azure PowerShell](/powershell/)- oder [Azure CLI](/cli/azure/install-azure-cli)-Befehle erfolgen.
2. Rufen Sie die öffentliche IP-Adresse einer VM ab: Wechseln Sie zur Seite mit den VM-Details, und kopieren Sie die öffentliche IP-Adresse. Greifen Sie mithilfe der öffentlichen IP-Adresse auf die RHEL-VM und JBoss EAP-Verwaltungskonsole zu.
3. Zeigen Sie die Webseite „JBoss EAP in Azure“ an: Öffnen Sie einen Webbrowser, und wechseln Sie zu *http://<ÖFFENTLICHER_HOSTNAME>:8080/* . Daraufhin sollte die Standardbegrüßungsseite von EAP angezeigt werden.
4. Melden Sie sich bei der JBoss EAP-Verwaltungskonsole an. Öffnen Sie einen Webbrowser, und wechseln Sie zu *http://<ÖFFENTLICHER_HOSTNAME>:9990*. Geben Sie für die Anmeldung Benutzername und Kennwort für JBoss EAP ein.

### <a name="3-create-a-jump-vm-in-a-different-subnet-new-subnet-in-the-same-vnet-and-access-the-rhel-vm-via-a-jump-vm"></a>3. Erstellen einer Jump-VM in einem anderen (bzw. neuen) Subnetz im gleichen VNet und Zugreifen auf die RHEL-VM über eine Jump-VM

1. Fügen Sie dem vorhandenen virtuellen Netzwerk [ein neues Subnetz](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) mit der RHEL-VM hinzu.
2. [Erstellen Sie in Azure in der Ressourcengruppe mit der RHEL-VM eine Windows-VM.](../../windows/quick-create-portal.md#create-virtual-machine) Geben Sie die erforderlichen Details an, und übernehmen Sie alle anderen Standardeinstellungen mit Ausnahme von VNet und Subnetz. Vergewissern Sie sich, dass das vorhandene VNet in der Ressourcengruppe vorhanden ist. Wählen Sie das Subnetz aus, das Sie im obigen Schritt erstellt haben, da es sich hierbei um Ihre Jump-VM handelt.
3. Greifen Sie auf die öffentliche IP-Adresse der Jump-VM zu: Wechseln Sie nach erfolgreicher Bereitstellung zur Seite mit den VM-Details, und kopieren Sie die öffentliche IP-Adresse. Melden Sie sich bei der Jump-VM mit der öffentlichen IP-Adresse an.
4. Melden Sie sich bei der RHEL-VM an: Kopieren Sie auf der Seite „Ausgabe“ die private IP-Adresse der RHEL-VM, und melden Sie sich damit auf der Jump-VM bei der RHEL-VM an.
5. Greifen Sie auf die Begrüßungsseite von JBoss EAP zu: Öffnen Sie in Ihrer Jump-VM einen Browser. Fügen Sie die App-URL ein, die Sie auf der Seite „Ausgabe“ der Bereitstellung kopiert haben.
6. Greifen Sie auf die JBoss EAP-Verwaltungskonsole zu: Fügen Sie die auf der Seite „Ausgabe“ kopierte URL der Verwaltungskonsole auf der Jump-VM in einen Browser ein, um auf die JBoss EAP-Verwaltungskonsole zuzugreifen. Geben Sie Benutzername und Kennwort von JBoss EAP ein, um sich anzumelden.

### <a name="4-expose-the-application-using-an-external-load-balancer"></a>4. Verfügbarmachen der Anwendung mithilfe eines externen Lastenausgleichs

1. [Erstellen Sie eine Application Gateway-Instanz](../../../application-gateway/quick-create-portal.md#create-an-application-gateway): Um auf die Ports der RHEL-VM zugreifen zu können, erstellen Sie in einem anderen Subnetz eine Application Gateway-Instanz. Das Subnetz darf nur die Application Gateway-Instanz enthalten.
2. Legen Sie Parameter für *Front-Ends* fest: Stellen Sie sicher, dass Sie öffentliche IP-Adresse oder beides auswählen und die erforderlichen Details angeben. Wählen Sie im Abschnitt *Back-Ends* die Option **Back-End-Pool hinzufügen** aus. Fügen Sie die RHEL-VM dem Back-End-Pool der Application Gateway-Instanz hinzu.
3. Legen Sie Zugriffsports fest: Fügen Sie im Abschnitt *Konfiguration* Routingregeln für den Zugriff auf die Ports 8080 und 9990 der RHEL-VM hinzu.
4. Kopieren Sie die öffentliche IP-Adresse der Application Gateway-Instanz: Nachdem die Application Gateway-Instanz mit den erforderlichen Konfigurationen erstellt wurde, wechseln Sie zur Übersichtsseite, und kopieren Sie die öffentliche IP-Adresse der Application Gateway-Instanz.
5. Zeigen Sie die Webseite „JBoss EAP in Azure“ an: Öffnen Sie einen Webbrowser, und wechseln Sie zu *http://<ÖFFENTLICHE_IP-ADRESSE_VON_APP_GATEWAY>:8080*. Daraufhin sollte die Standardbegrüßungsseite von EAP angezeigt werden.
6. Melden Sie sich bei der JBoss EAP-Verwaltungskonsole an. Öffnen Sie einen Webbrowser, und wechseln Sie zu *http://<ÖFFENTLICHE_IP-ADRESSE_VON_APP_GATEWAY>:9990*. Geben Sie für die Anmeldung Benutzername und Kennwort für JBoss EAP ein.

### <a name="5-use-an-external-load-balancer-elb-to-access-your-rhel-vmvirtual-machine-scale-sets"></a>5. Verwenden eines externen Lastenausgleichs für den Zugriff auf Ihre RHEL-VM/-VM-Skalierungsgruppe

1. [Erstellen Sie einen Lastenausgleich](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources), um auf die Ports der RHEL-VM zuzugreifen. Geben Sie die erforderlichen Details an, um den externen Lastenausgleich bereitzustellen, und übernehmen Sie ansonsten die Standardeinstellungen. Übernehmen Sie für die Konfiguration des externen Lastenausgleichs die SKU „Basic“.
2. Fügen Sie Regeln für den Lastenausgleich hinzu: Nachdem der Lastenausgleich erfolgreich erstellt wurde, [erstellen Sie für den Lastenausgleich Ressourcen](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources). Fügen Sie dann Regeln für den Lastenausgleich hinzu, um auf die Ports 8080 und 9990 der RHEL-VM zuzugreifen.
3. Fügen Sie die RHEL-VM dem Back-End-Pool des Lastenausgleichs hinzu: Klicken Sie im Abschnitt „Einstellungen“ auf *Back-End-Pools*. Wählen Sie dann den Back-End-Pool aus, den Sie im vorherigen Schritt erstellt haben. Wählen Sie die VM aus, die der Option *Zugeordnet zu* entspricht, und fügen Sie dann die RHEL-VM hinzu.
4. Rufen Sie die öffentliche IP-Adresse des Lastenausgleichs ab: Wechseln Sie zur Übersichtsseite des Lastenausgleichs, und kopieren Sie dessen öffentliche IP-Adresse.
5. Zeigen Sie die Webseite „JBoss EAP in Azure“ an: Öffnen Sie einen Webbrowser, und wechseln Sie zu *http://<ÖFFENTLICHE_IP-ADRESSE_VON_LASTENAUSGLEICH>:8080/* . Daraufhin sollte die Standardbegrüßungsseite von EAP angezeigt werden.
6. Melden Sie sich bei der JBoss EAP-Verwaltungskonsole an: Öffnen Sie einen Webbrowser, und wechseln Sie zu *http://<ÖFFENTLICHE_IP-ADRESSE_VON_APP_GATEWAY>:9990*. Geben Sie für die Anmeldung Benutzername und Kennwort für JBoss EAP ein.

### <a name="6-use-azure-bastion-to-connect"></a>6. Herstellen der Verbindung mithilfe von Azure Bastion 

1. Erstellen Sie einen Azure Bastion-Host für das VNet mit Ihrer RHEL-VM. Der Bastion-Dienst stellt über SSH automatisch eine Verbindung mit Ihrer RHEL-VM her.
2. Erstellen Sie Rollen des Typs „Leser“ auf der VM, die NIC mit der privaten IP-Adresse der VM und die Azure Bastion-Ressource.
3. Der eingehende RHEL-Port ist für SSH (22) geöffnet. 
4. Stellen Sie im Azure-Portal eine Verbindung mithilfe Ihres Benutzernamens und Kennworts her. Klicken Sie auf „Verbinden“, und wählen Sie in der Dropdownliste „Bastion“ aus. Wählen Sie dann „Verbinden“ aus, um eine Verbindung mit Ihrer RHEL-VM herzustellen. Sie können sich mithilfe einer Datei mit privatem Schlüssel oder eines im Azure Key Vault gespeicherten privaten Schlüssels verbinden.

## <a name="azure-platform"></a>Azure Platform

- **Prüfungsfehler**: Die Bereitstellung wird nicht gestartet, wenn die Parameterkriterien nicht erfüllt sind (z. B. die Kriterien für das Kennwort des Administrators) oder wenn im Abschnitt „Parameter“ keine Pflichtparameter angegeben wurden. Sie können die Details der Parameter überprüfen, ehe Sie auf *Erstellen* klicken.
- **Fehler bei der Ressourcenbereitstellung**: Sobald die Bereitstellung beginnt, werden die bereitgestellten Ressourcen auf der Seite „Bereitstellung“ angezeigt. Wenn die Bereitstellung nach der Parameterprüfung fehlschlägt, ist eine detailliertere Fehlermeldung verfügbar. 
- **Fehler bei der VM-Bereitstellung**: Wenn Ihre Bereitstellung am Ressourcenpunkt **Benutzerdefinierte Skripterweiterung für VM** fehlschlägt, ist eine detailliertere Fehlermeldung in der VM-Protokolldatei verfügbar. Weitere Informationen zur Problembehandlung finden Sie im nächsten Abschnitt.

## <a name="troubleshooting-eap-deployment-extension"></a>Problembehandlung für die EAP-Bereitstellungserweiterung

Diese Angebote nutzen benutzerdefinierte Skripterweiterungen für VMs, um JBoss EAP bereitzustellen und den JBoss EAP-Verwaltungsbenutzer zu konfigurieren. Ihre Bereitstellung kann aus verschiedenen Gründen fehlschlagen, wie z. B.:

- Ungültige RHSM- oder EAP-Berechtigung
- Ungültige Pool-ID für JBoss EAP- oder RHEL-Betriebssystemberechtigung
- Azure Marketplace-Geschäftsbedingungen nicht für RHEL-VMs akzeptiert

Führen Sie für eine weitere Problembehandlung die folgenden Schritte aus:

1. Melden Sie sich über SSH bei der bereitgestellten VM an. Sie können die öffentliche IP-Adresse der VM auf der VM-Übersichtsseite im Azure-Portal abrufen.
1. Ändern Sie den Benutzer in „root“.

    ```cli
    sudo su -
    ```

1. Geben Sie bei Aufforderung das VM-Administratorkennwort ein.
1. Ändern Sie das Verzeichnis in das Protokollierungsverzeichnis.

    ```cli
    cd /var/lib/waagent/custom-script/download/0 #for EAP on RHEL stand-alone VM
    ```

    ```cli
    cd /var/lib/waagent/custom-script/download/1 #for EAP on RHEL clustered VM
    ```

1. Überprüfen Sie die Protokolle in der Protokolldatei „eap.log“.

    ```cli
    more eap.log
    ```

Die Protokolldatei enthält Details, die die Ursache des Bereitstellungsfehlers und mögliche Lösungen enthalten. Wenn Ihre Bereitstellung aufgrund des RHSM-Kontos oder der Berechtigungen fehlgeschlagen ist, erfahren Sie im Abschnitt „Hinweise zu Support und Abonnement“, wie Sie die Voraussetzungen erfüllen. Versuchen Sie es anschließend noch mal. Wenn Sie EAP mit einem RHEL-Clusterplan bereitstellen, stellen Sie sicher, dass die Bereitstellung nicht die Kontingentgrenze erreicht. Es ist wichtig, ihre regionalen und für die VM-Serie geltenden vCPU-Kontingente zu prüfen, ehe Sie die Anzahl der Instanzen für die Bereitstellung angeben. Wenn Ihr Abonnement oder Ihre Region nicht über ein ausreichendes Kontingent verfügt, [fordern Sie im Azure-Portal ein Kontingent an](../../../azure-portal/supportability/regional-quota-requests.md).

Weitere Informationen zur Problembehandlung bei benutzerdefinierten Skripterweiterungen für VMs finden Sie unter [Verwenden der benutzerdefinierten Skripterweiterung von Azure, Version 2, mit Linux-VMs](../../extensions/custom-script-linux.md).

## <a name="resource-links-and-support"></a>Links zu Ressourcen und Support

Wenden Sie sich bei Supportfragen, Problemen oder Anpassungsanforderungen an den [Red Hat-Support](https://access.redhat.com/support) oder [Microsoft Azure-Support](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

* Weitere Informationen: [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/)
* [JBoss EAP unter Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP in Azure App Service](/azure/developer/java/ee/jboss-on-azure) 
* [Azure-Hybridvorteile](../../windows/hybrid-use-benefit-licensing.md)
* [Red Hat-Abonnementverwaltung](https://access.redhat.com/products/red-hat-subscription-management)
* [Microsoft-Dokumentation zu Red Hat in Azure](./overview.md)

## <a name="next-steps"></a>Nächste Schritte

* Bereitstellen von JBoss EAP für RHEL-VM/-VM-Skalierungsgruppen über [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Bereitstellen von JBoss EAP für RHEL-VM/-VM-Skalierungsgruppen per [Azure-Schnellstart](https://aka.ms/Quickstart-JBoss-EAP)
* Konfigurieren einer Java-App für [Azure App Service](../../../app-service/configure-language-java.md)
* Tutorial zum Bereitstellen von [JBoss EAP in Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)
* Verwenden des [Azure App Service-Migrations-Assistenten](https://azure.microsoft.com/services/app-service/migration-assistant/)
* Verwenden des Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
