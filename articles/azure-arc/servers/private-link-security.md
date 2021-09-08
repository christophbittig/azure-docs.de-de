---
title: Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Arc
description: Erfahren Sie, wie Sie Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Arc verwenden.
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 1bd683631e9a7edb321abb56ed423cac11b42557
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467999"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-arc"></a>Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Arc

Mit [Azure Private Link](../../private-link/private-link-overview.md) können Sie Azure-PaaS-Dienste über private Endpunkte sicher mit Ihrem virtuellen Netzwerk verknüpfen. Bei vielen Diensten richten Sie einfach für jede Ressource einen Endpunkt ein. Dies bedeutet, dass Sie Ihre lokalen Server oder Server in mehreren Clouds mit Azure Arc verbinden und den gesamten Datenverkehr über eine Azure [ExpressRoute](../../expressroute/expressroute-introduction.md)- oder Site-to-Site-[VPN-Verbindung](../../vpn-gateway/vpn-gateway-about-vpngateways.md) übertragen können, statt öffentliche Netzwerke zu verwenden.

Beginnend mit Azure Arc-fähigen Servern können Sie ein Private Link-Bereichsmodell verwenden, um mehreren Servern oder Computern die Kommunikation mit ihren Azure Arc-Ressourcen über einen einzelnen privaten Endpunkt zu ermöglichen.

In diesem Artikel wird erläutert, wie Sie einen Private Link-Bereich für Azure Arc (Vorschau) einrichten und nutzen.

> [!NOTE]
> Private Link-Bereich für Azure Arc (Vorschau) ist in allen kommerziellen Cloudregionen verfügbar, in der US Government-Cloud derzeit jedoch noch nicht.

## <a name="advantages"></a>Vorteile

Private Link bietet folgende Möglichkeiten:

- Herstellen einer privaten Verbindung mit Azure Arc ohne öffentlichen Netzwerkzugriff.
- Sicherstellen, dass der Zugriff auf die Daten des Arc-fähigen Computers oder Servers nur über autorisierte private Netzwerke erfolgt. Dies schließt auch Daten von [VM-Erweiterungen](manage-vm-extensions.md) ein, die auf dem Computer oder Server installiert sind und Unterstützung für die Verwaltung und Überwachung nach der Bereitstellung bieten.
- Verhindern der Datenexfiltration aus Ihren privaten Netzwerken, indem Sie bestimmte Azure Arc-fähige Server und andere Ressourcen für Azure-Dienste wie Azure Monitor bestimmen, die über Ihren privaten Endpunkt eine Verbindung herstellen.
- Sicheres Verbinden Ihres privaten lokalen Netzwerks mit Azure Arc über ExpressRoute und Private Link.
- Kapseln des gesamten Datenverkehrs innerhalb des Microsoft Azure-Backbonenetzwerks.

Weitere Informationen finden Sie unter [Hauptvorteile von Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funktionsweise

Private Link-Bereich für Azure Arc (Vorschau) verbindet private Endpunkte (und die virtuellen Netzwerke, in denen sie enthalten sind) mit einer Azure-Ressource, in diesem Fall mit Azure Arc-fähigen Servern. Wenn Sie eine der von Arc-fähigen Servern unterstützten VM-Erweiterungen, wie z. B. Azure Automation-Updateverwaltung oder Azure Monitor, aktivieren, verbinden diese Ressourcen andere Azure-Ressourcen. Also beispielsweise:

- Log Analytics-Arbeitsbereich, erforderlich für Azure Automation-Updateverwaltung, Azure Automation-Änderungsnachverfolgung und Bestand, Azure Monitor-VM-Erkenntnisse und Azure Monitor-Protokollsammlung mit Log Analytics-Agent
- Azure Automation-Konto, erforderlich für Updateverwaltung sowie Änderungsnachverfolgung und Bestand
- Azure Key Vault
- Azure Blob Storage, erforderlich für benutzerdefinierte Skripterweiterung

:::image type="content" source="./media/private-link-security/private-link-topology.png" alt-text="Diagramm der grundlegenden Ressourcentopologie" border="true":::

Für Konnektivität zwischen den anderen Azure-Ressourcen und einem zuvor aufgeführten Arc-fähigen Server muss Private Link für jeden Dienst konfiguriert werden. Weitere Informationen finden Sie in den folgenden Artikeln zum Konfigurieren von Private Link für [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) und [Azure Blob Storage](../../private-link/tutorial-private-endpoint-storage-portal.md).

> [!IMPORTANT]
> Azure Private Link ist jetzt allgemein verfügbar. Sowohl der Dienst für private Endpunkte als auch der Dienst „Private Link“ (Dienst im Hintergrund von Load Balancer Standard) ist allgemein verfügbar. Für unterschiedliche Azure PaaS-Instanzen wird das Onboarding für Azure Private Link basierend auf verschiedenen Zeitplänen durchgeführt. Den genauen Azure PaaS-Status für Private Link finden Sie unter [Verfügbarkeit von Azure Private Link](../../private-link/availability.md). Weitere Informationen zu bekannten Einschränkungen finden Sie unter [Privater Endpunkt](../../private-link/private-endpoint-overview.md#limitations) und [Private Link-Dienst](../../private-link/private-link-service-overview.md#limitations).

* Der private Endpunkt in Ihrem VNet ermöglicht, Azure Arc-fähige Server über private IP-Adressen im Pool Ihres Netzwerks zu erreichen, anstatt die öffentlichen IP-Adressen dieser Endpunkte zu verwenden. Auf diese Weise können Sie Ihre Azure Arc-fähigen Serverressourcen weiterhin nutzen, ohne das VNet für nicht angeforderten ausgehenden Datenverkehr zu öffnen.

* Datenverkehr vom privaten Endpunkt zu Ihren Ressourcen wird über das Microsoft Azure-Backbone und nicht über öffentliche Netzwerke geroutet.

* Sie können Ihre einzelnen Komponenten so konfigurieren, dass Erfassung und Abfragen aus öffentlichen Netzwerken zugelassen oder verweigert werden. Dies bietet Schutz auf Ressourcenebene, sodass Sie den Datenverkehr zu bestimmten Ressourcen kontrollieren können.

## <a name="restrictions-and-limitations"></a>Einschränkungen

Es gelten eine Reihe von Einschränkungen für das Objekt „Private Link-Bereich“ von Arc-fähigen Servern, die beim Planen der Einrichtung von Private Link zu beachten sind.

- Sie können einem virtuellen Netzwerk mindestens einen Private Link-Bereich für Azure Arc zuordnen.

- Ein Azure Arc-fähiger Computer oder eine Serverressource kann sich nur mit einem Private Link-Bereich eines Azure Arc-fähigen Servers verbinden.

- Alle lokalen Computer müssen denselben privaten Endpunkt verwenden, indem die ordnungsgemäßen Informationen zum privaten Endpunkt (Name des FQDN-Eintrags und private IP-Adresse) mithilfe derselben DNS-Weiterleitung aufgelöst werden. Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../../private-link/private-endpoint-dns.md).

- Der Azure Arc-fähige Computer oder Server, der Private Link-Bereich für Azure Arc und das virtuelle Netzwerk müssen sich in derselben Azure-Region befinden.

- Datenverkehr zu Azure Active Directory und Azure Resource Manager-Diensttags muss in Vorschauphase von Ihrer lokalen Netzwerkfirewall zugelassen werden. 

- Andere genutzte Azure-Dienste, z. B. Azure Monitor, erfordern eigene private Endpunkte in Ihrem virtuellen Netzwerk.

- „Private Link-Bereich“ für Azure Arc-fähige Server ist derzeit in US Government-Regionen von Azure nicht verfügbar.

## <a name="planning-your-private-link-setup"></a>Planen Ihres Private Link-Setups

Um Ihren Server über eine private Verbindung mit Azure Arc zu verbinden, müssen Sie Ihr Netzwerk wie folgt konfigurieren:

1. Richten Sie eine Verbindung zwischen Ihrem lokalen Netzwerk und einem virtuellen Azure-Netzwerk ein, indem Sie eine [Site-to-Site-VPN](../../vpn-gateway/tutorial-site-to-site-portal.md)-Verbindung oder [ExpressRoute-Leitung](../../expressroute/expressroute-howto-linkvnet-arm.md) verwenden.

1. Stellen Sie einen Private Link-Bereich für Azure Arc (Vorschau) bereit, der steuert, welche Computer oder Server über private Endpunkte mit Azure Arc kommunizieren können, und ordnen Sie ihn über einen privaten Endpunkt Ihrem virtuellen Azure-Netzwerk zu.

1. Ändern Sie die DNS-Konfiguration in Ihrem lokalen Netzwerk so, dass die Adressen der privaten Endpunkte aufgelöst werden.

1. Konfigurieren Sie Ihre lokale Firewall so, dass der Zugriff auf Azure Active Directory und Azure Resource Manager möglich ist. Dies ist ein vorübergehender Schritt, der nicht mehr erforderlich ist, sobald private Endpunkte für diese Dienste in die Vorschau aufgenommen werden.

1. Ordnen Sie die Computer oder Server, die bei Azure Arc-fähigen Servern registriert sind, dem Private Link-Bereich zu.

1. Optional können Sie private Endpunkte für andere Azure-Dienste bereitstellen, von denen Ihr Computer oder Server verwaltet wird, z. B:

    - Azure Monitor
    - Azure Automation
    - Azure-BLOB-Speicher
    - Azure Key Vault

Dieser Artikel geht davon aus, dass Sie Ihre ExpressRoute-Leitung oder Site-to-Site-VPN-Verbindung bereits eingerichtet haben.

## <a name="network-configuration"></a>Netzwerkkonfiguration

Azure Arc-fähige Server lassen sich in verschiedene Azure-Dienste integrieren, um Ihren Hybridcomputern oder -servern Verwaltungs- und Governancefunktionen in der Cloud zu ermöglichen. Die meisten dieser Dienste bieten bereits private Endpunkte, aber Sie müssen Ihre Firewall- und Routingregeln so konfigurieren, dass der Zugriff auf Azure Active Directory und Azure Resource Manager über das Internet möglich ist, bis diese Dienste private Endpunkte anbieten.

Dazu stehen zwei Möglichkeiten zur Verfügung:

- Wenn Ihr Netzwerk so konfiguriert ist, dass der gesamte internetgebundene Datenverkehr über die Azure-VPN-Verbindung oder ExpressRoute-Leitung geroutet wird, können Sie die Netzwerksicherheitsgruppe (NSG), die Ihrem Subnetz in Azure zugeordnet ist, so konfigurieren, dass ausgehender TCP 443-Zugriff (HTTPS) auf Azure AD und Azure mithilfe von [Diensttags](../../virtual-network/service-tags-overview.md) erlaubt wird. Die NSG-Regeln sollten wie folgt aussehen:

    |Einstellung |Azure AD-Regel | Azure-Regel |
    |--------|--------------|-----------------------------|
    |`Source` |Virtuelles Netzwerk |Virtuelles Netzwerk |
    |Source port ranges |* |* |
    |Destination |Diensttag |Diensttag |
    |Zieldiensttag |AzureActiveDirectory |AzureResourceManager |
    |Zielportbereiche |443 |443 |
    |Protocol |TCP |TCP |
    |Aktion |Zulassen |Zulassen |
    |Priorität |150 (muss niedriger sein als alle Regeln, die Internetzugriff blockieren) |151 (muss niedriger sein als alle Regeln, die Internetzugriff blockieren) |
    |Name |AllowAADOutboundAccess |AllowAzOutboundAccess |

- Konfigurieren Sie die Firewall in Ihrem lokalen Netzwerk so, dass ausgehender TCP 443-Zugriff (HTTPS) auf Azure AD und Azure mithilfe der herunterladbaren Diensttagdateien zulässig ist. Die JSON-Datei enthält alle öffentlichen IP-Adressbereiche, die von Azure AD und Azure verwendet werden, und wird monatlich aktualisiert, um alle Änderungen widerzuspiegeln. Das Diensttag von Azure AD ist `AzureActiveDirectory`, das von Azure `AzureResourceManager`. Wenden Sie sich an Ihren Netzwerkadministrator und Netzwerkfirewallanbieter, um zu erfahren, wie Sie Ihre Firewallregeln konfigurieren.

Weitere Informationen finden Sie im visuellen Diagramm unter dem Abschnitt zur [Funktionsweise](#how-it-works) des Flusses des Netzwerkdatenverkehrs.

## <a name="create-a-private-link-scope"></a>Erstellen eines Private Link-Bereichs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wechseln Sie im Azure-Portal zu **Ressource erstellen** und dann zu **Private Link-Bereich für Azure Arc**. Sie können auch den folgenden Link verwenden, um die Seite [Private Link-Bereich für Azure Arc](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2FprivateLinkScopes) im Portal zu öffnen.

    :::image type="content" source="./media/private-link-security/find-scope.png" alt-text="Navigieren zum Private Link-Bereich" border="true":::

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie ein Abonnement und eine Ressourcengruppe aus. In der Vorschauphase müssen sich Ihr virtuelles Netzwerk und die Azure Arc-fähigen Server in demselben Abonnement befinden wie der Private Link-Bereich für Azure Arc.

1. Geben Sie dem Private Link-Bereich für Azure Arc einen Namen. Es empfiehlt sich ein aussagekräftiger Name.

   Sie können optional verlangen, dass jeder Arc-fähige Computer oder Server, der mit diesem Private Link-Bereich für Azure Arc (Vorschau) verbunden ist, Daten über den privaten Endpunkt an den Dienst sendet. Wenn Sie **Zugriff auf öffentliches Netzwerk aktivieren** auswählen, können Computer oder Server, die diesem Private Link-Bereich für Azure Arc (Vorschau) zugeordnet sind, über private oder öffentliche Netzwerke mit dem Dienst kommunizieren. Sie können diese Einstellung nach Erstellen des Bereichs ändern, falls Sie Ihre Meinung ändern sollten.

1. Klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="./media/private-link-security/create-private-link-scope.png" alt-text="Private Link-Bereich erstellen" border="true":::

1. Warten Sie die Überprüfung ab, und wählen Sie dann **Erstellen** aus.

## <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

Nachdem der Private Link-Bereich für Azure Arc (Vorschau) erstellt wurde, müssen Sie ihn über einen privaten Endpunkt mit mindestens einem virtuellen Netzwerk verbinden. Der private Endpunkt macht den Zugriff auf die Azure Arc-Dienste über eine private IP-Adresse im Adressraum Ihres virtuellen Netzwerks verfügbar.

1. Wählen Sie in Ihrer Bereichsressource im Ressourcenmenü links **Private Endpunktverbindungen** aus. Wählen Sie **Hinzufügen** aus, um den Prozess zum Erstellen des Endpunkts zu starten. Sie können hier auch Verbindungen genehmigen, die im Private Link-Center gestartet wurden, indem Sie die entsprechende Verbindung und dann **Genehmigen** auswählen.

    :::image type="content" source="./media/private-link-security/create-private-endpoint.png" alt-text="Erstellen eines privaten Endpunkts" border="true":::

1. Wählen Sie das Abonnement, die Ressourcengruppe, den Namen des Endpunkts sowie die Region aus, in der sich der Endpunkt befinden soll. Die Region muss mit der Region des VNET übereinstimmen, mit dem Sie die Verbindung herstellen.

1. Klicken Sie auf **Weiter: Ressource** aus.

1. Führen Sie auf der Seite **Ressource** die folgenden Schritte aus:

   a. Wählen Sie das **Abonnement** aus, das Ihre Ressource für den Private Link-Bereich für Azure Arc enthält.

   b. Wählen Sie als **Ressourcentyp** die Option **Microsoft.HybridCompute/privateLinkScopes** aus.

   c. Wählen Sie in der Dropdownliste **Ressource** den Private Link-Bereich aus, den Sie zuvor erstellt haben.

   d. Klicken Sie auf **Weiter: Konfiguration >** .

    :::image type="content" source="./media/private-link-security/create-private-endpoint-configuration.png" alt-text="Vollständige Erstellung des privaten Endpunkts" border="true":::

1. Gehen Sie auf der Seite **Konfiguration** so vor:

   a. Wählen Sie das **virtuelle Netzwerk** und das **Subnetz** aus, mit dem Sie Ihre Azure Monitor-Ressourcen verbinden möchten. 

   b. Wählen Sie für **In private DNS-Zone integrieren** die Option **Ja** aus, und lassen Sie die neue private DNS-Zone automatisch erstellen. Die tatsächlichen DNS-Zonen können von den im folgenden Screenshot dargestellten Werten abweichen.

     > [!NOTE]
     > Wenn Sie **Nein** wählen und die manuelle Verwaltung von DNS-Einträgen bevorzugen, müssen Sie zuerst die Einrichtung Ihrer Private Link-Instanz einschließlich dieses privaten Endpunkts und der Konfiguration des Private Link-Bereichs abschließen. Konfigurieren Sie anschließend Ihr DNS gemäß den Anweisungen unter [DNS-Konfiguration für private Azure-Endpunkte ](../../private-link/private-endpoint-dns.md). Stellen Sie sicher, dass Sie keine leeren Datensätze als Vorbereitung für Ihre Private Link-Einrichtung erstellen. Die von Ihnen erstellten DNS-Einträge können bestehende Einstellungen überschreiben und Ihre Konnektivität mit Arc-fähigen Servern beeinträchtigen.

   c.    Klicken Sie auf **Überprüfen + erstellen**.

   d.    Warten Sie die Überprüfung ab.

   e.    Wählen Sie **Erstellen** aus.

## <a name="configure-on-premises-dns-forwarding"></a>Konfigurieren der lokalen DNS-Weiterleitung

Ihre lokalen Computer oder Server müssen in der Lage sein, die DNS-Einträge der privaten Verbindung in die IP-Adressen des privaten Endpunkts aufzulösen. Wie Sie dies konfigurieren, hängt davon ab, ob Sie private Azure-DNS-Zonen zur Verwaltung von DNS-Einträgen verwenden, ob Sie Ihren eigenen DNS-Server lokal einsetzen und wie viele Server Sie konfigurieren.

### <a name="dns-configuration-using-azure-integrated-private-dns-zones"></a>DNS-Konfiguration mit in Azure integrierten privaten DNS-Zonen

Wenn Sie bei der Erstellung des privaten Endpunkts private DNS-Zonen für Azure Arc-fähige Server und die Gastkonfiguration einrichten, müssen Ihre lokalen Computer oder Server in der Lage sein, DNS-Abfragen an die integrierten Azure DNS-Server weiterzuleiten, um die Adressen des privaten Endpunkts ordnungsgemäß aufzulösen. Sie benötigen eine DNS-Weiterleitung in Azure (entweder eine eigens eingerichtete VM oder eine Azure Firewall-Instanz mit aktiviertem DNS-Proxy). Anschließend können Sie Ihren lokalen DNS-Server so konfigurieren, dass er Abfragen an Azure weiterleitet, um IP-Adressen privater Endpunkte aufzulösen.

Die Dokumentation zu privaten Endpunkten enthält Anleitungen zum Konfigurieren [lokaler Workloads mithilfe einer DNS-Weiterleitung](../../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

### <a name="manual-dns-server-configuration"></a>Manuelle DNS-Serverkonfiguration

Wenn Sie sich bei der Erstellung des privaten Endpunkts gegen die Verwendung privater Azure-DNS-Zonen entschieden haben, müssen Sie die erforderlichen DNS-Einträge auf Ihrem lokalen DNS-Server erstellen.

1. Öffnen Sie das Azure-Portal.

1. Navigieren Sie zu der privaten Endpunktressource, die Ihrem virtuellen Netzwerk und dem Private Link-Bereich zugeordnet ist.

1. Wählen Sie im linken Bereich **DNS-Konfiguration** aus, um eine Liste der DNS-Einträge und der entsprechenden IP-Adressen anzuzeigen, die Sie auf Ihrem DNS-Server einrichten müssen. Die FQDNs und IP-Adressen ändern sich basierend auf der Region, die Sie für Ihren privaten Endpunkt ausgewählt haben, und den verfügbaren IP-Adressen in Ihrem Subnetz.

    :::image type="content" source="./media/private-link-security/dns-configuration.png" alt-text="DNS-Konfigurationsdetails" border="true":::

1. Befolgen Sie die Anweisungen Ihres DNS-Serveranbieters, um die erforderlichen DNS-Zonen und A-Einträge hinzuzufügen, die mit der Tabelle im Portal übereinstimmen müssen. Stellen Sie sicher, dass Sie einen DNS-Server auswählen, der für Ihr Netzwerk geeignet ist. Jeder Computer oder Server, der diesen DNS-Server verwendet, löst nun die IP-Adressen der privaten Endpunkte auf und muss mit dem Private Link-Bereich für Azure Arc (Vorschau) verbunden sein, da sonst die Verbindung abgelehnt wird.

### <a name="single-server-scenarios"></a>Szenarien mit Einzelserver

Wenn Sie Private Link nur zur Unterstützung einiger Computer oder Server verwenden möchten, müssen Sie möglicherweise nicht die DNS-Konfiguration Ihres gesamten Netzwerks ändern. In diesem Fall können Sie die Hostnamen und IP-Adressen der privaten Endpunkte in die Datei **Hosts** Ihres Betriebssystems aufnehmen. Abhängig von der Betriebssystemkonfiguration kann die Datei „Hosts“ die primäre oder alternative Methode zum Auflösen des Hostnamens in die IP-Adresse sein.

#### <a name="windows"></a>Windows

1. Öffnen Sie unter Verwendung eines Kontos mit Administratorrechten **C:\Windows\System32\drivers\etc\hosts**.

1. Fügen Sie die IP-Adressen und Hostnamen des privaten Endpunkts hinzu, wie in der Tabelle in Schritt 3 unter [Manuelle DNS-Serverkonfiguration](#manual-dns-server-configuration) gezeigt. Die Datei „Hosts“ erfordert zuerst die IP-Adresse, gefolgt von einem Leerzeichen und dem Hostnamen.

1. Speichern Sie die Datei mit Ihren Änderungen. Möglicherweise müssen Sie die Datei zuerst in einem anderen Verzeichnis speichern und dann in den ursprünglichen Pfad kopieren.

#### <a name="linux"></a>Linux

1. Nutzen Sie ein Konto mit der Berechtigung **sudoers** und führen Sie `sudo nano /etc/hosts` aus, um die Datei „Hosts“ zu öffnen.

1. Fügen Sie die IP-Adressen und Hostnamen des privaten Endpunkts hinzu, wie in der Tabelle in Schritt 3 unter [Manuelle DNS-Serverkonfiguration](#manual-dns-server-configuration) gezeigt. Die Datei „Hosts“ erfordert zuerst die IP-Adresse, gefolgt von einem Leerzeichen und dem Hostnamen.

1. Speichern Sie die Datei mit Ihren Änderungen.

## <a name="connect-to-an-azure-arc-enabled-servers"></a>Herstellen einer Verbindung mit einem Azure Arc-fähigen Server

> [!NOTE]
> Die unterstützte Mindestversion des Connected Machine-Agents für Azure Arc mit privatem Endpunkt ist Version 1.4. Mithilfe des im Portal generierten Skripts zur Bereitstellung Arc-fähiger Servern wird die neueste Version heruntergeladen.

### <a name="configure-a-new-arc-enabled-server-to-use-private-link"></a>Konfigurieren eines neuen Arc-fähigen Servers für die Verwendung von Private Link

Wenn Sie einen Computer oder Server zum ersten Mal mit Azure Arc-fähigen Servern verbinden, können Sie ihn optional mit einem Private Link-Bereich verbinden. Gehen Sie wie folgt vor: 

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

1. Navigieren Sie zu **Server – Azure Arc**.

1. Wählen Sie auf der Seite **Server - Azure Arc** oben links **Hinzufügen** aus.

1. Wählen Sie auf der Seite **Server mit Azure Arc hinzufügen** je nach Bereitstellungsszenario entweder die Option **Einen einzelnen Server hinzufügen** oder **Mehrere Server hinzufügen** und dann **Skript generieren** aus.

1. Wählen Sie auf der Seite **Skript generieren** das Abonnement und die Ressourcengruppe für die Verwaltung des Computers in Azure aus. Wählen Sie einen Azure-Standort zum Speichern der Computermetadaten aus. Bei diesem Standort kann es sich um denselben Standort wie bei der Ressourcengruppe oder um einen anderen Standort handeln.

1. Lesen Sie sich die Informationen auf der Seite **Voraussetzungen** durch, und wählen Sie dann **Weiter: Ressourcendetails** aus.

1. Geben Sie auf der Seite **Ressourcendetails** Folgendes an:

    1. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Ressourcengruppe aus, über die der Computer verwaltet wird.
    1. Wählen Sie in der Dropdownliste **Region** die Azure-Region aus, in der die Metadaten des Computers oder Servers gespeichert werden sollen.
    1. Wählen Sie in der Dropdownliste **Betriebssystem** das Betriebssystem aus, für das das Skript konfiguriert ist.
    1. Wählen Sie unter **Netzwerkkonnektivität** die Option **Privater Endpunkt (Vorschau)** und dann in der Dropdownliste den Private Link-Bereich für Azure Arc aus, der in Teil 1 erstellt wurde.

       :::image type="content" source="./media/private-link-security/arc-enabled-servers-create-script.png" alt-text="Auswählen der Konnektivitätsoption „Privater Endpunkt“" border="true":::

    1. Klicken Sie auf **Weiter: Tags**.

1. Wenn Sie auf der Seite **Authentifizierung** die Option **Mehrere Server hinzufügen** ausgewählt haben, wählen Sie in der Dropdownliste den Dienstprinzipal aus, der für Arc-fähige Server erstellt wurde. Wenn Sie keinen Dienstprinzipal für Arc-fähige Server erstellt haben, lesen Sie zunächst, [wie Sie einen Dienstprinzipal erstellen](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale), um sich mit den erforderlichen Berechtigungen und den Schritten zu dessen Erstellung vertraut zu machen. Wählen Sie **Weiter: Tags** aus, um fortzufahren.

1. Überprüfen Sie auf der Seite **Tags** die vorgeschlagenen standardmäßigen **physischen Speicherorttags**, und geben Sie einen Wert ein, oder geben Sie mindestens ein **benutzerdefiniertes Tag** an, um Ihre Standards zu unterstützen.

1. Klicken Sie auf **Weiter: Skript herunterladen und ausführen**.

1. Überprüfen Sie die auf der Seite **Skript herunterladen und ausführen** die Zusammenfassungsinformationen, und wählen Sie dann **Herunterladen** aus. Sollten noch Änderungen erforderlich sein, wählen Sie **Zurück** aus.

Nach dem Herunterladen des Skripts müssen Sie es auf Ihrem Computer oder Server mit einem privilegierten Konto (Administrator oder root) ausführen. Je nach Netzwerkkonfiguration müssen Sie den Agent möglicherweise von einem Computer mit Zugriff auf das Internet herunterladen und auf Ihren Computer oder Server übertragen und dann das Skript mit dem Pfad zum Agent ändern. 

Der Windows-Agent kann von [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent) heruntergeladen werden, der Linux-Agent von [https://packages.microsoft.com](https://packages.microsoft.com). Suchen Sie in Ihrem Verzeichnis mit der Betriebssystemdistribution nach der neuesten Version von **azcmagent**, und installieren Sie sie mit Ihrem lokalen Paket-Manager. 

Das Skript gibt nach Abschluss des Onboardings Statusmeldungen zurück, die Sie darüber informieren, ob das Onboarding erfolgreich war.

> [!NOTE]
> Wenn Sie den Connected Machine-Agent auf einem Linux-Server bereitstellen, kann es während der Überprüfung der Netzwerkkonnektivität zu einer Verzögerung von fünf Minuten kommen, gefolgt vom Fehler `you do not have access to login.windows.net`, auch wenn Ihre Firewall ordnungsgemäß konfiguriert ist. Dies ist ein bekanntes Problem, das in einem künftigen Release behoben wird. Das Onboarding sollte dennoch erfolgreich sein, sofern Ihre Firewall ordnungsgemäß konfiguriert ist.

### <a name="configure-an-existing-arc-enabled-server"></a>Konfigurieren eines vorhandenen Arc-fähigen Servers

Arc-fähigen Servern, die vor Ihrem Private Link-Bereich eingerichtet wurden, können Sie erlauben, den Private Link-Bereich für Arc-fähige Server zu verwenden, indem Sie die folgenden Schritte ausführen.

1. Navigieren Sie im Azure-Portal zu Ihrer Ressource „Private Link-Bereich für Azure Arc“.

1. Wählen Sie im linken Bereich **Azure Arc-Ressourcen** und dann **+ Hinzufügen** aus.

1. Wählen Sie die Server in der Liste aus, die Sie dem Private Link-Bereich zuordnen möchten, und dann **Auswählen** aus, um die Änderungen zu speichern.

    > [!NOTE]
    > Nur Azure Arc-fähige Server im selben Abonnement und derselben Region wie Ihr Private Link-Bereich werden angezeigt.

    :::image type="content" source="./media/private-link-security/select-servers-private-link-scope.png" alt-text="Auswählen von Azure Arc-Ressourcen" border="true":::

Es kann bis zu 15 Minuten dauern, bis der Private Link-Bereich Verbindungen mit den kurz zuvor zugeordneten Servern akzeptiert.

## <a name="troubleshooting"></a>Problembehandlung

1. Überprüfen Sie Ihre lokalen DNS-Server, um sicherzustellen, dass sie entweder zu Azure DNS weitergeleitet werden oder mit den entsprechenden A-Einträgen in Ihrer Private Link-Zone konfiguriert sind. Diese Lookupbefehle müssen private IP-Adressen in Ihrem virtuellen Azure-Netzwerk zurückgeben. Wenn sie öffentliche IP-Adressen auflösen, überprüfen Sie die DNS-Konfiguration Ihres Computers oder Servers und Netzwerks.

    nslookup gbl.his.arc.azure.com  nslookup agentserviceapi.guestconfiguration.azure.com

1. Wenn Sie Probleme mit dem Onboarding eines Computers oder Servers haben, vergewissern Sie sich, dass Sie Ihrer lokalen Netzwerkfirewall Diensttags für Azure Active Directory und Azure Resource Manager hinzugefügt haben. Der Agent muss mit diesen Diensten über das Internet kommunizieren, bis private Endpunkte für diese Dienste verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu privaten Endpunkten finden Sie unter [Was ist ein privater Endpunkt in Azure?](../../private-link/private-endpoint-overview.md).

* Wenn bei der Einrichtung der Konnektivität mit privaten Azure-Endpunkten Probleme auftreten, finden Sie weitere Informationen unter [Behandeln von Konnektivitätsproblemen mit privaten Azure-Endpunkten](../../private-link/troubleshoot-private-endpoint-connectivity.md).

* Weitere Informationen finden Sie in den folgenden Artikeln zum Konfigurieren von Private Link für [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) und [Azure Blob Storage](../../private-link/tutorial-private-endpoint-storage-portal.md).
