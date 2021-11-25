---
title: 'Red Hat JBoss EAP in Azure: Bewährte Methoden'
description: Der Leitfaden enthält Informationen zu den bewährten Methoden für die Verwendung von Red Hat JBoss Enterprise Application Platform in Microsoft Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 195a0bfa-dff1-429b-b030-19ca95ee6abe
ms.date: 06/08/2021
ms.openlocfilehash: aaaa6fb269178954ade464745c6c640845457612
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289222"
---
# <a name="red-hat-jboss-eap-on-azure-best-practices"></a>Red Hat JBoss EAP in Azure: Bewährte Methoden

Der Leitfaden „Red Hat JBoss EAP in Azure: Bewährte Methoden“ für die Verwendung von Red Hat JBoss Enterprise Application Platform (EAP) in Microsoft Azure. JBoss EAP kann mit der Microsoft Azure-Plattform verwendet werden, solange Sie diese innerhalb der spezifischen unterstützten Konfigurationen für die Ausführung von JBoss EAP in Azure verwenden. Wenn Sie eine JBoss EAP-Clusterumgebung manuell konfigurieren, wenden Sie die spezifischen Konfigurationen an, die für die Verwendung von JBoss EAP-Clusteringfeatures in Azure erforderlich sind. In diesem Leitfaden werden die unterstützten Konfigurationen für Verwendung von JBoss EAP in Microsoft Azure beschrieben.

JBoss EAP ist eine mit Jakarta Enterprise Edition(EE) 8-kompatible Implementierung für Webprofil- und vollständige Plattformspezifikationen. Es ist auch eine zertifizierte Implementierung der Java EE 8-Spezifikation. Hauptversionen von JBoss EAP werden an bestimmten Punkten aus dem WildFly-Communityprojekt geforkt, wenn das Communityprojekt die gewünschte Vollständigkeitsstufe für Features erreicht hat. Danach wird JBoss EAP über einen längeren Zeitraum getestet und zur Produktionsreife geführt. Dies umfasst Stabilisierung, Zertifizierung und Erweiterung für die Verwendung in der Produktion. Während der Lebensdauer einer JBoss EAP-Hauptversion können ausgewählte Features vom Communityprojekt ausgewählt und zurückportiert werden. Diese Features werden dann in einer Reihe von Nebenreleases zur Verbesserung von Features innerhalb derselben Hauptversionsfamilie zur Verfügung gestellt.

## <a name="supported-and-unsupported-configurations-of-jboss-eap-on-azure"></a>Unterstützte und nicht unterstützte Konfigurationen von JBoss EAP in Azure

Weitere Informationen zu Betriebssystemen, Java-Plattformen und anderen unterstützten Plattformen, auf denen EAP verwendet werden kann, finden Sie in der [Dokumentation zu den von JBoss EAP unterstützten EAP-Konfigurationen](https://access.redhat.com/articles/2026253).

Mit dem Red Hat Cloud Access-Programm können Sie ein JBoss EAP-Abonnement verwenden, um JBoss EAP auf Ihrem virtuellen Azure-Computer zu installieren. Dabei handelt es sich um On-Demand-Betriebssysteme mit nutzungsbasierter Zahlung (Pay-As-You-Go, PAYG) aus dem Microsoft Azure Marketplace. Betriebssystemabonnements für virtuelle Computer, in diesem Fall Red Hat Enterprise Linux (RHEL), sind von einem JBoss EAP-Abonnement getrennt. Red Hat Cloud Access ist ein Red Hat-Abonnementfeature, das Unterstützung für JBoss EAP unter Red Hat-zertifizierten Cloudinfrastrukturanbietern wie Microsoft Azure bietet. Mit Red Hat Cloud Access können Sie Ihre Abonnements auf einfache und kostengünstige Weise zwischen herkömmlichen lokalen Servern und öffentlichen cloudbasierten Ressourcen verschieben.

Weitere Informationen zu [Red Hat Cloud Access finden Sie im Kundenportal](https://www.redhat.com/en/technologies/cloud-computing/cloud-access). Zur Erinnerung: Sie benötigen Red Hat Cloud Access nicht für PAYG-Angebote auf Azure Marketplace. 

Jedes Red Hat JBoss EAP-Release wird für verschiedene marktführende Betriebssysteme, Java Virtual Machines (JVMs) und Datenbankkombinationen getestet und unterstützt. Red Hat bietet Produktions- und Entwicklungsunterstützung für unterstützte Konfigurationen und getestete Integrationen gemäß Ihrer Abonnementvereinbarung. Die Unterstützung gilt sowohl für physische als auch für virtuelle Umgebungen. Überprüfen Sie neben den oben genannten Betriebssystemeinschränkungen [die unterstützten Konfigurationen für JBoss EAP](https://access.redhat.com/articles/2026253), z. B. unterstützte JDK-Anbieter (Java Development Kit) und -Versionen. Sie erhalten Informationen zu unterstützten Konfigurationen verschiedener JBoss EAP-Versionen wie 7.0, 7.1, 7.2 und 7.3. Überprüfen Sie die [Produkt-/Konfigurationsmatrix für Microsoft Azure](https://access.redhat.com/articles/product-configuration-for-azure) auf unterstützte RHEL-Betriebssysteme, Mindestanforderungen an die VM-Kapazität und Informationen zu anderen unterstützten Red Hat-Produkten. Überprüfen Sie [Certified Cloud Provider/Microsoft Azure](https://access.redhat.com/ecosystem/cloud-provider/2068823) (Zertifizierter Cloudanbieter/Microsoft Azure) auf Red Hat-Softwareprodukte, die für den Betrieb in Microsoft Azure zertifiziert sind.

Es gibt einige nicht unterstützte Features, wenn JBoss EAP in einer Microsoft Azure-Umgebung verwendet wird, einschließlich:

- **Verwaltete Domänen**: Ermöglicht die Verwaltung mehrerer JBoss EAP-Instanzen von einem einzelnen Steuerungspunkt aus. Verwaltete JBoss EAP-Domänen werden in Microsoft Azure momentan nicht unterstützt. Nur eigenständige JBoss EAP-Serverinstanzen werden unterstützt. Das Konfigurieren von JBoss EAP-Clustern mithilfe eigenständiger JBoss EAP-Server wird in Azure unterstützt.

- **ActiveMQ Artemis-Hochverfügbarkeit (HA) mit freigegebenem Speicher**: JBoss EAP-Messaging-HA mit freigegebenen Artemis-Speichern wird in Microsoft Azure nicht unterstützt.

- **`mod_custer`-Ankündigung**: Sie können JBoss EAP nicht als Untertow-`mod_cluster`-Proxy-Lastenausgleich verwenden. Die mod_cluster-Ankündigungsfunktion wird aufgrund von UDP-Multicasteinschränkungen (User Datagram Protocol) von Azure nicht unterstützt.

## <a name="other-features-of-jboss-eap-on-azure"></a>Weitere Features von JBoss EAP in Azure

JBoss EAP verfügt über vorkonfigurierte Optionen für Features wie Hochverfügbarkeitsclustering, Messaging und verteiltes Zwischenspeichern. Hiermit können Benutzer Anwendungen schreiben, bereitstellen und ausführen, indem sie die verschiedenen APIs und Dienste von JBoss EAP verwenden.

- **Jakarta EE-kompatibel**: Jakarta EE 8-kompatibel für Webprofil- und vollständige Plattformspezifikationen.

- **Jakarta EE-konform**: Java EE 8-zertifiziert für Webprofil- und vollständige Plattformspezifikationen.

- **Verwaltungskonsole und Verwaltungs-CLI**: für eigenständige Serververwaltungsschnittstellen. Die Verwaltungs-CLI enthält auch einen Batchmodus, mit dem Verwaltungsaufgaben geskriptet und automatisiert werden können. Das direkte Bearbeiten der JBoss EAP-XML-Konfigurationsdateien wird nicht empfohlen.

- **Vereinfachtes Verzeichnislayout**: Das Verzeichnis „modules“ enthält alle Anwendungsservermodule. Die eigenständigen Verzeichnisse enthalten die Artefakte und Konfigurationsdateien für eigenständige Bereitstellungen.

- **Mechanismus für das modulare Laden von Klassen**: Module werden bei Bedarf geladen und entladen. Der Mechanismus für das modulare Laden von Klassen verbessert die Leistung, bietet Sicherheitsvorteile und reduziert Start- und Neustartzeiten.

- **Optimierte Datenquellenverwaltung**: Datenbanktreiber werden wie andere Dienste bereitgestellt. Darüber hinaus werden Datenquellen mithilfe der Verwaltungskonsole und der Verwaltungs-CLI erstellt und verwaltet.

- **Unified Security Framework**: Elytron bietet ein einzelnes einheitliches Framework, mit dem der Zugriff für eigenständige Server verwaltet und konfiguriert werden kann. Es kann auch verwendet werden, um den Sicherheitszugriff für Anwendungen zu konfigurieren, die auf JBoss EAP-Servern bereitgestellt werden.

## <a name="creating-your-microsoft-azure-environment"></a>Erstellen Ihrer Microsoft Azure-Umgebung

Erstellen Sie die VMs, die Ihre JBoss EAP-Instanzen in Ihrer Microsoft Azure-Umgebung hosten. Verwenden Sie die Azure-VM-Größe „Standard_A2“ oder höher. Sie können entweder die Azure On-Demand PAYG-Premium-Images verwenden, um Ihre VMs zu erstellen, oder Sie können Ihre eigenen VMs manuell erstellen. Beispielsweise können Sie RHEL-VMs wie folgt bereitstellen:

* Verwenden des On-Demand Marketplace RHEL-Images in Azure: Es gibt mehrere Angebote in Azure Marketplace, über die Sie die virtuelle RHEL-VM auswählen können, die Sie für die Einrichtung der JBoss EAP verwenden möchten. Besuchen Sie [Deploy RHEL 8 VM from Azure Marketplace](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/assembly_deploying-a-rhel-image-as-a-virtual-machine-on-microsoft-azure_cloud-content) (Bereitstellen von RHEL 8-VM aus Azure Marketplace). Sie haben zwei Optionen für die Auswahl der RHEL-Betriebssystemlizenzierung im Azure Marketplace. Wählen Sie entweder PAYG oder Bring-Your-Own-Subscription (BYOS) über das Red Hat Gold Image-Modell aus. Wenn Sie eine RHEL-VM mit dem PAYG-Plan bereitgestellt haben, werden nur Ihre JBoss EAP-Abonnementdetails verwendet, um die resultierende Bereitstellung für ein Red Hat-Abonnement zu abonnieren.

* [Manuelles Erstellen und Bereitstellen eines RHEL-Images für Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Verwenden Sie die neueste Nebenversion jeder Hauptversion von RHEL.

Für eine Microsoft Windows Server-VM finden Sie in der [Microsoft Azure-Dokumentation](../../windows/overview.md) Informationen zum Erstellen einer Windows Server-VM in Azure.

## <a name="jboss-eap-installation"></a>JBoss EAP-Installation

> [!NOTE]
>  Wenn Sie das JBoss EAP-unter-RHEL-Angebot über Azure Marketplace verwenden, wird JBoss EAP automatisch für die Azure-Umgebung installiert und konfiguriert.

Die folgenden Schritte gelten, wenn Sie EAP manuell auf einer RHEL-VM bereitstellen, die in Microsoft Azure bereitgestellt wird.

Sobald Ihre VM eingerichtet ist, können Sie JBoss EAP installieren. Sie benötigen Zugriff auf das [Red Hat-Kundenportal](https://access.redhat.com), bei dem es sich um die zentrale Plattform für Red Hat Knowledge Base (KB)- und Abonnementressourcen handelt. Wenn Sie kein EAP-Abonnement haben, registrieren Sie sich über [Red Hat Developer Subscription for Individuals](https://developers.redhat.com/register) für ein kostenloses Entwicklerabonnement. Suchen Sie nach der Registrierung im Abschnitt „Subscriptions“ des [Red Hat-Kundenportals](https://access.redhat.com/management/) nach den Anmeldeinformationen (Pool-IDs). Beachten Sie, dass dieses Abonnement nicht für die Verwendung in der Produktion vorgesehen ist.

Die Variable *EAP_HOME* wird zum Angeben des Pfads zur JBoss EAP-Installation verwendet. Ersetzen Sie diese Variable durch den tatsächlichen Pfad zu Ihrer JBoss EAP-Installation.

> [!IMPORTANT]
> Es gibt mehrere verschiedene Möglichkeiten, JBoss EAP zu installieren. Jede Methode wird am besten in bestimmten Situationen verwendet. Wenn Sie eine RHEL On-Demand-VM aus dem Microsoft Azure Marketplace verwenden, installieren Sie JBoss EAP mithilfe der ZIP- oder Installationsprogrammmethode. **Registrieren Sie keine RHEL On-Demand-VM bei der Red Hat-Abonnementverwaltung (Red Hat Subscription Management, RHSM), weil Ihnen dieser virtuelle Computer zweimal berechnet wird, da er die PAYG-Abrechnungsmethode verwendet.

* **ZIP-Installation**: Das ZIP-Archiv eignet sich für die Installation unter allen unterstützten Betriebssystemen. Die ZIP-Installationsmethode sollte verwendet werden, wenn Sie die Instanz manuell extrahieren möchten. Die ZIP-Installation bietet eine Standardinstallation von JBoss EAP sowie alle Konfigurationen, die nach der Installation ausgeführt werden müssen. Wenn Sie JBoss Operations Network (ON)-Server zum Bereitstellen und Installieren von JBoss EAP-Patches verwenden möchten, sollten die JBoss EAP-Zielinstanzen mithilfe der ZIP-Installationsmethode installiert werden. Weiter Details finden Sie unter [Zip Installation](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#zip_installation) (ZIP-Installation).

* **JAR-Installationsprogramm**: Das JAR-Installationsprogramm kann entweder in einer Konsole oder als grafischer Assistent ausgeführt werden. Beide Optionen enthalten Schritt-für-Schritt-Anweisungen zum Installieren und Konfigurieren der Serverinstanz. Das JAR-Installationsprogramm ist die bevorzugte Methode zum Installieren von JBoss EAP auf allen unterstützten Plattformen. Weitere Informationen finden Sie unter [JAR Installer Installation](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#installer_installation) (Installation mit JAR-Installationsprogramm).

* **RPM-Installation**: JBoss EAP kann mit RPM-Paketen in unterstützten Installationen von RHEL 6, RHEL 7 und RHEL 8 installiert werden. Die RPM-Installationsmethode eignet sich am besten, wenn Sie planen, die Installation von EAP auf Ihrer RHEL-VM in Azure zu automatisieren. Bei der RPM-Installation von JBoss EAP wird alles installiert, was zum Ausführen von JBoss EAP als Dienst erforderlich ist. Weitere Informationen finden Sie unter [RPM Installation](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#rpm_installation) (RPM-Installation).

## <a name="other-offers-by-azure-and-red-hat"></a>Weitere Angebote von Azure und Red Hat

Red Hat und Microsoft haben als Partner zusammengearbeitet, um auf dem Azure Marketplace einige Azure-Lösungsvorlagen bereitzustellen und so einen guten Ausgangspunkt für die Migration zu Azure zu schaffen. Wählen Sie aus der Liste der Angebote in der Dokumentation eine geeignete Umgebung aus.

### <a name="azure-marketplace-offers"></a>Azure Marketplace-Angebote

Sie können im [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP) auf diese Angebote zugreifen.

Dieses Marketplace-Angebot umfasst verschiedene Kombinationen von JBoss EAP- und RHEL-Versionen mit flexiblen Modellen für das Abonnement von Support. JBoss EAP ist immer BYOS, aber für das RHEL-Betriebssystem können Sie zwischen BYOS oder PAYG wählen. Das Azure Marketplace-Angebot umfasst Planoptionen für JBoss EAP unter RHEL als eigenständige bzw. gruppierte VMs oder gruppierte VM-Skalierungsgruppen. 

Es folgen die sechs Pläne:

- JBoss EAP 7.3 unter RHEL 8.3 (eigenständige VM, nutzungsbasierte Zahlung)
- JBoss EAP 7.3 unter RHEL 8.3 (eigenständige VM, BYOS)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VMs, nutzungsbasierte Zahlung)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VMs, BYOS)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VM-Skalierungsgruppen, nutzungsbasierte Zahlung)
- JBoss EAP 7.3 unter RHEL 8.3 (gruppierte VM-Skalierungsgruppen, BYOS)

:::image type="content" source="./media/red-hat-marketplace-image-1.png" alt-text="Abbildung: Option zum Bereitstellen des Red-Hat-Images über den GitHub-Link für die Bereitstellung.":::

### <a name="azure-quickstart-templates"></a>Azure-Schnellstartvorlagen

Neben Azure Marketplace-Angeboten werden auch Schnellstartvorlagen zur Verfügung gestellt, mit denen Sie EAP in Azure testen können. Diese Schnellstarts enthalten vordefinierte ARM-Vorlagen und ein Skript zum Bereitstellen von JBoss EAP in Azure mit verschiedenen Konfigurationen und Versionskombinationen. 

Es folgen mögliche Lösungsarchitekturen:

* JBoss EAP unter RHEL (eigenständige VM)
* JBoss EAP unter RHEL (gruppierte VMs)
* JBoss EAP unter RHEL (gruppierte VM-Skalierungsgruppen)

    :::image type="content" source="./media/red-hat-marketplace-image.png" alt-text="Abbildung: Red Hat-Angebote, die über Azure Marketplace verfügbar sind":::

Sie können zwischen RHEL 7.7 und 8.0 und JBoss EAP 7.2 und 7.3 wählen. Wählen Sie eine der folgenden Kombinationen für die Bereitstellung aus:

- JBoss EAP 7.2 unter RHEL 7.7
- JBoss EAP 7.2 unter RHEL 8.0
- JBoss EAP 7.3 unter RHEL 8.0

Wählen Sie zunächst eine Schnellstartvorlage mit einer entsprechenden JBoss EAP-unter-RHEL-Kombination aus, die Ihr Bereitstellungsziel erfüllt. Die Liste mit den verfügbaren Schnellstartvorlagen ist hier angegeben.

* [JBoss EAP unter RHEL (eigenständige VM)](https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/): Die Azure-Vorlage stellt eine Webanwendung namens JBoss-EAP on Azure on JBoss EAP (Version 7.2 oder 7.3) bereit, die auf einer RHEL-VM (Version 7.7 oder 8.0) ausgeführt wird.

* [JBoss EAP unter RHEL (gruppierte VMs)](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/): Die Azure-Vorlage stellt eine Webanwendung namens eap-session-replication, die auf „n“ RHEL-VMs ausgeführt wird, in einem JBoss EAP-Cluster bereit. „n“ ist die Anfangsanzahl von VMs, die Sie am Anfang festlegen. Alle VMs werden dem Back-End-Pool eines Load Balancers hinzugefügt.

* [JBoss EAP unter RHEL (gruppierte VM-Skalierungsgruppen)](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-vmss-rhel/): Die Azure-Vorlage stellt eine Webanwendung namens eap-session-replication, die auf VM-Skalierungsgruppeninstanzen vom Typ RHEL 7.7 oder 8.0 ausgeführt wird, in einem JBoss EAP 7.2- oder 7.3-Cluster bereit.

## <a name="configuring-jboss-eap-to-work-on-cloud-platforms"></a>Konfigurieren von JBoss EAP für die Arbeit auf Cloudplattformen

Nachdem Sie JBoss EAP auf Ihrer VM installiert haben, können Sie JBoss EAP für die Ausführung als Dienst konfigurieren. Die Konfiguration von JBoss EAP für die Ausführung als Dienst hängt von der JBoss EAP-Installationsmethode und dem Typ des VM-Betriebssystems ab. Beachten Sie, dass bei der RPM-Installation von JBoss EAP alles installiert wird, was zum Ausführen von JBoss EAP als Dienst erforderlich ist. Weitere Informationen finden Sie unter [Configuring JBoss EAP to run as a service](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#configuring_jboss_eap_to_run_as_a_service) (Konfigurieren von JBoss EAP für die Ausführung als Dienst).

### <a name="starting-and-stopping-jboss-eap"></a>Starten und Beenden von JBoss EAP

#### <a name="starting-jboss-eap"></a>Starten von JBoss EAP

JBoss EAP wird unter RHEL und Windows Server unterstützt und wird nur in einem eigenständigen Serverbetriebsmodus ausgeführt. Der spezifische Befehl zum Starten von JBoss EAP hängt von der zugrunde liegenden Plattform ab. Server werden zunächst in einem angehaltenen Zustand gestartet und akzeptieren keine Anforderungen, bis alle erforderlichen Dienste gestartet wurden. Zu diesem Zeitpunkt werden die Server in einen normalen Ausführungszustand versetzt und können mit der Annahme von Anforderungen beginnen. Im Folgenden wird der Befehl zum Starten von JBoss EAP als eigenständiger Server ausgeführt:

- Befehl zum Starten von JBoss EAP (über ZIP-oder Installationsprogrammmethode installiert) als eigenständiger Server auf einer RHEL-VM:
    ```
    $EAP_HOME/bin/standalone.sh
    ```
- Verwenden Sie für Windows Server das Skript `EAP_HOME\bin\standalone.bat`, um JBoss EAP als eigenständigen Server zu starten.
- Das Starten von JBoss EAP unterscheidet sich bei einer RPM-Installation von einer ZIP- oder JAR-Installation. Verwenden Sie beispielsweise für RHEL 7 und höher den folgenden Befehl:
    ```
    systemctl start eap7-standalone.service
    ```
Das Startskript, das zum Starten von JBoss EAP (über ZIP- oder Installationsprogrammmethode installiert) verwendet wird, verwendet die Datei `EAP_HOME/bin/standalone.conf`, oder `standalone.conf.bat` für Windows Server, um einige Standardeinstellungen festzulegen, z. B. JVM-Optionen. Passen Sie die Einstellungen in dieser Datei an. JBoss EAP verwendet standardmäßig die Konfigurationsdatei `standalone.xml`, kann aber mit einer anderen gestartet werden. Verwenden Sie `/etc/opt/rh/eap7/wildfly/eap7-standalone.conf`, um die Standardkonfigurationsdatei zum Starten von JBoss EAP zu ändern, die über die RPM-Methode installiert wurde. Verwenden Sie dieselbe eap7-standalone.conf-Datei, um andere Konfigurationsänderungen wie die WildFly-Bindungsadresse vorzunehmen.

Ausführliche Informationen zu den verfügbaren eigenständigen Konfigurationsdateien und deren Verwendung finden Sie unter [Stand-alone Server Configuration Files](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#standalone_server_configuration_files) (Eigenständige Serverkonfigurationsdateien).

Verwenden Sie das Argument „--server-config“, um JBoss EAP mit einer anderen Konfiguration zu starten. Verwenden Sie für eine vollständige Liste mit allen verfügbaren Startskriptargumenten und deren Zweck das Argument „--help“, oder lesen Sie [Server Runtime Arguments](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#reference_of_switches_and_arguments_to_pass_at_server_runtime) (Serverlaufzeitargumente).

#### <a name="stopping-jboss-eap"></a>Beenden von JBoss EAP

Wie Sie JBoss EAP beenden, hängt davon ab, wie sie gestartet wurde. Drücken Sie `Ctrl+C` im Terminal, in dem JBoss EAP gestartet wurde, um eine interaktive Instanz von JBoss EAP zu beenden. Verwenden Sie zum Beenden der Hintergrundinstanz von JBoss EAP die Verwaltungs-CLI, um eine Verbindung mit der ausgeführten Instanz herzustellen und den Server herunterzufahren. Weitere Informationen finden Sie unter [Stopping JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#stopping_jboss_eap) (Beenden von JBoss EAP).

Das Beenden von JBoss EAP unterscheidet sich bei einer RPM-Installation von einer ZIP- oder Installationsprogramminstallation. Der Befehl zum Beenden einer RPM-Installation von JBoss EAP hängt davon ab, welchen Betriebsmodus Sie starten möchten und welche RHEL-Version Sie ausführen. Der eigenständige Modus wird nur in Azure unterstützt. 

- Verwenden Sie beispielsweise für RHEL 7 und höher den folgenden Befehl:
    ```
    systemctl stop eap7-standalone.service
    ```
JBoss EAP kann auch angehalten oder ordnungsgemäß heruntergefahren werden, sodass aktive Anforderungen normal abgeschlossen werden können, ohne neue Anforderungen zu akzeptieren. Sobald der Server angehalten wurde, kann er heruntergefahren, in den Zustand „Ausgeführt“ zurückgeführt oder zur Wartung in einem angehaltenen Zustand belassen werden. Während der Server angehalten wird, werden Verwaltungsanforderungen weiterhin verarbeitet. Der Server kann mithilfe der Verwaltungskonsole oder der Verwaltungs-CLI angehalten und fortgesetzt werden.

### <a name="configuring-jboss-eap-subsystems-to-work-on-cloud-platforms"></a>Konfigurieren von JBoss EAP-Subsystemen für die Arbeit auf Cloudplattformen

Viele der APIs und Funktionen, die für Anwendungen verfügbar gemacht werden, die in JBoss EAP bereitgestellt werden, sind in Subsystemen organisiert. Diese Subsysteme können von Administratoren konfiguriert werden, um je nach Ziel der Anwendung ein anderes Verhalten bereitzustellen. Weitere Informationen zu den Subsystemen finden Sie unter [JBoss EAP Subsystems](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#jboss_eap_subsystems) (JBoss EAP-Subsysteme).

Für einige JBoss-EAP-Subsysteme muss die Konfiguration ordnungsgemäß auf Cloudplattformen funktionieren. Die Konfiguration ist erforderlich, da ein JBoss-EAP-Server an die private IP-Adresse einer Cloud-VM gebunden ist. Private IP-Adressen sind nur innerhalb der Cloudplattform sichtbar. Für bestimmte Subsysteme muss die private IP-Adresse der öffentlichen IP-Adresse des Servers zugeordnet werden, die von außerhalb der Cloud sichtbar ist. Weitere Informationen zum Ändern dieser Subsysteme finden Sie unter [Configuring JBoss EAP Subsystems to Work on Cloud Platforms](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#configuring_subsystems_for_cloud_platforms) (Konfigurieren von JBoss EAP-Subsystemen für die Arbeit auf Cloudplattformen).

## <a name="using-jboss-eap-high-availability-in-microsoft-azure"></a>Verwenden von JBoss EAP-Hochverfügbarkeit in Microsoft Azure

Azure unterstützt keine JGroups-Ermittlungsprotokolle, die auf UDP-Multicast basieren. JGroups verwendet standardmäßig den UDP-Stapel. Stellen Sie sicher, dass Sie diesen in TCP ändern, da Azure UDP nicht unterstützt. Obwohl Sie andere JGroups-Ermittlungsprotokolle wie TCPPING oder JDBC_PING verwenden können, wird das freigegebene Dateiermittlungsprotokoll *Azure_PING* empfohlen, das für Azure entwickelt wurde.

*AZURE_PING* verwendet einen allgemeinen Blobcontainer in einem Microsoft Azure-Speicherkonto. Wenn Sie noch nicht über einen Blobcontainer verfügen, auf den AZURE_PING zugreifen kann, erstellen Sie einen, auf den Ihre VM zugreifen kann. Weitere Informationen finden Sie unter [Configuring JBoss EAP High Availability in Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#using_jboss_eap_high_availability_in_microsoft_azure) (Konfigurieren der Hochverfügbarkeit von JBoss EAP in Microsoft Azure).

Konfigurieren Sie JBoss EAP mit Lastenausgleichsumgebung. Stellen Sie sicher, dass alle Balancer und Worker an zugängliche IP-Adressen in Ihrem internen Microsoft Azure Virtual Network (VNet) gebunden sind. Weitere Informationen zur Lastenausgleichskonfiguration finden Sie unter [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491) (Installieren und Konfigurieren eines Hochverfügbarkeitsclusters unter Red Hat Enterprise Linux 7.4 (und höher) in Microsoft Azure).

## <a name="other-best-practices"></a>Weitere bewährte Methoden

- Für Sie als Administrator eines JBoss EAP-Setups auf einer VM ist es wichtig, sicherzustellen, dass Ihre VM sicher ist. Dadurch wird das Risiko, dass Gast- und Host-Betriebssysteme von Schadsoftware infiziert werden, erheblich verringert. Durch das Schützen Ihrer VM werden Angriffe auf JBoss EAP und Fehlfunktionen von Anwendungen, die auf JBoss EAP gehostet werden, reduziert. Steuern Sie den Zugriff auf die Azure-VMs mithilfe von Features wie [Azure Policy](https://azure.microsoft.com/services/azure-policy/) und [integrierte Rollen](../../../role-based-access-control/built-in-roles.md) für die [rollenbasierte Zugriffssteuerung in Azure (RBAC)](../../../role-based-access-control/overview.md). Schützen Sie Ihren virtuellen Computer vor Schadsoftware, indem Sie Microsoft Antimalware oder die Endpunktschutz-Lösung eines Microsoft-Partners installieren und Ihre Antischadsoftwarelösung mit [Microsoft Defender für Cloud](https://azure.microsoft.com/services/security-center/) integrieren, um den Status Ihres Schutzes zu überwachen. RHEL-VMs können Sie schützen, indem Sie die Portweiterleitung und die Stammanmeldung blockieren, die in der Datei „//ssh/sshd_config“ deaktiviert werden können.


- Verwenden Sie Umgebungsvariablen, um Ihre Arbeit mit JBoss EAP auf Azure-VMs einfach und reibungslos zu gestalten. Beispielsweise können Sie EAP_HOME verwenden, um den Pfad zur JBoss EAP-Installation anzugeben, die mehrmals verwendet wird. In solchen Fällen sind Umgebungsvariablen praktisch. Umgebungsvariablen sind auch ein gängiges Mittel zum Konfigurieren von Diensten und zum Behandeln von Webanwendungsgeheimnissen. Wenn eine Umgebungsvariable über die Shell mithilfe des Exportbefehls festgelegt wird, existiert sie für die Dauer der Benutzersitzungen. Dies ist problematisch, wenn die Variable sitzungsübergreifend beibehalten werden muss. Wenn eine Variable für die Umgebung eines Benutzers erhalten bleiben soll, exportieren wir die Variable aus dem Profilskript des Benutzers. Fügen Sie den Exportbefehl für jede Umgebungsvariable hinzu, die Sie im bash_profile beibehalten möchten. Wenn Sie eine permanente globale Umgebungsvariable für alle Benutzer festlegen möchten, die Zugriff auf die VM haben, können Sie sie dem Standardprofil hinzufügen. Es wird empfohlen, globale Umgebungsvariablen in einem Verzeichnis namens `/etc/profile.d` zu speichern. Das Verzeichnis enthält eine Liste von Dateien, die zum Festlegen von Umgebungsvariablen für das gesamte System verwendet werden. Durch die Verwendung des Befehls „set“ zum Festlegen von Systemumgebungsvariablen in einer Windows Server-Eingabeaufforderung wird die Umgebungsvariable nicht dauerhaft festgelegt. Verwenden Sie entweder den Befehl *setx* oder die Systemschnittstelle in der Systemsteuerung.

- Verwalten Sie Ihre VM-Updates und -Upgrades. Verwenden Sie die Lösung für [Updateverwaltung](../../../automation/update-management/overview.md) in Azure Automation, um Betriebssystemupdates für Ihre Windows- und Linux-Computer in Azure zu verwalten. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates verwalten. Das Aktualisieren der VM-Software stellt sicher, dass wichtige Microsoft Azure-Patches, Hypervisortreiber und Softwarepakete auf dem neuesten Stand bleiben. Für Nebenreleases sind direkte Upgrades möglich. Beispielsweise von RHEL 6.9 auf RHEL 6.10 oder RHEL 7.3 auf RHEL 7.4. Direkte Upgrades können durch Ausführen des Befehls *yum update* durchgeführt werden. Microsoft Azure unterstützt kein direktes Upgrade eines Hauptrelease, z. B. von RHEL 6 auf RHEL 7.

- Verwenden Sie [Azure Monitor](../../../azure-monitor/data-platform.md), um sich über den Zustand Ihrer Ressourcen zu informieren. Zu den Azure Monitor-Features gehören [Protokolldateien zur Ressourcendiagnose](../../../azure-monitor/essentials/platform-logs-overview.md). Mit ihnen werden Ihre VM-Ressourcen überwacht und potenzielle Probleme erkannt, die unter Umständen Leistung und Verfügbarkeit beeinträchtigen. Die [Azure-Diagnoseerweiterung](../../../azure-monitor/agents/diagnostics-extension-overview.md) kann Überwachungs- und Diagnosefunktionen auf virtuellen Windows-Computern bereitstellen. Aktivieren Sie diese Funktionen, indem Sie die Erweiterung in die Azure Resource Manager-Vorlage einbeziehen. Aktivieren Sie die Startdiagnose. Dies ist ein wichtiges Tool für die Problembehandlung einer VM, die nicht gestartet wird. Die Konsolenausgabe und das Startprotokoll können den technischen Red Hat-Support beim Beheben eines Startproblems sehr unterstützen. Aktivieren Sie die Startdiagnose im Microsoft Azure-Portal, während Sie einen virtuellen Computer erstellen, oder auf einem vorhandenen virtuellen Computer. Nach der Aktivierung können Sie die Konsolenausgabe für den virtuellen Computer anzeigen und das Startprotokoll zur Problembehandlung herunterladen.

- Eine weitere Möglichkeit, die sichere Kommunikation sicherzustellen, ist die Verwendung eines privaten Endpunkts in Ihrem [virtuellen Netzwerk (VNet)](../../../virtual-network/virtual-networks-overview.md) und [virtuellen privaten Netzwerken (VPN)](../../../vpn-gateway/vpn-gateway-about-vpngateways.md). Offene Netzwerke sind für die Außenwelt zugänglich und daher anfällig für Angriffe von böswilligen Benutzern. VNet und VPN beschränken den Zugriff auf ausgewählte Benutzer. Das VNet verwendet eine private IP-Adresse, um isolierte Kommunikationskanäle zwischen Servern innerhalb desselben Bereichs zu erstellen. Die isolierte Kommunikation ermöglicht es mehreren Servern unter demselben Konto, Informationen und Daten abgeschirmt von einem öffentlichen Raum auszutauschen. Wenn Sie lokal arbeiten, stellen Sie über ein privates Netzwerk eine Verbindung mit einem Remoteserver her. Es gibt verschiedene Methoden wie die Verwendung einer JumpVM/JumpBox im VNet, in dem sich der Anwendungsserver befindet, oder die Verwendung von [Azure Virtual Network Peering](../../../virtual-network/virtual-network-peering-overview.md), [Azure Application Gateway](../../../application-gateway/overview.md), [Azure Bastion](https://azure.microsoft.com/services/azure-bastion) und so weiter. All diese Methoden ermöglichen eine vollständig sichere und private Verbindung und können mehrere Remoteserver verbinden.

- Verwenden Sie eine [Azure-Netzwerksicherheitsgruppe (NSG)](../../../virtual-network/network-security-groups-overview.md), um den Netzwerkdatenverkehr zum und vom Anwendungsserver im Azure-VNet zu filtern. Eine NSG enthält Sicherheitsregeln, die eingehenden Netzwerkdatenverkehr an verschiedene Typen von Azure-Ressourcen (bzw. von ihnen ausgehenden Netzwerkdatenverkehr) zulassen oder verweigern. Für jede Regel können Sie die Quelle, das Ziel, den Port und das Protokoll angeben. Schützen Sie Ihre Anwendung unter JBoss EAP, indem Sie diese NSG-Regeln verwenden und Ports für das Internet blockieren oder zulassen.

- Für eine bessere Funktionalität der Anwendung, die unter JBoss EAP in Azure ausgeführt wird, können Sie das in Azure verfügbare Feature für Hochverfügbarkeit verwenden. Hochverfügbarkeit in Azure kann mithilfe von Azure-Ressourcen wie Load Balancer, Application Gateway oder [VM-Skalierungsgruppe](../../../virtual-machine-scale-sets/overview.md) erreicht werden. Diese HA-Methoden bieten Redundanz und verbesserte Leistung, wodurch Sie eine Anwendungsinstanz problemlos warten oder aktualisieren können, indem Sie die Last auf eine andere verfügbare Anwendungsinstanz verteilen. Unter Umständen müssen Sie die Anzahl von Anwendungsinstanzen erhöhen, die Ihre Anwendung ausführen, um die zusätzliche Kundennachfrage zu decken. VM-Skalierungsgruppen verfügen auch über ein Feature für die automatische Skalierung, mit dem Ihre Anwendung automatisch hoch- oder herunterskaliert werden kann, wenn sich der Bedarf ändert.

## <a name="optimizing-the-jboss-eap-server-configuration"></a>Optimieren der JBoss EAP-Serverkonfiguration

Nachdem Sie den JBoss EAP-Server installiert und einen Verwaltungsbenutzer erstellt haben, können Sie Ihre Serverkonfiguration optimieren. Lesen Sie unbedingt die Informationen im [Performance Tuning Guide](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/performance_tuning_guide/index) (Leitfaden zur Leistungsoptimierung), um die Serverkonfiguration zu optimieren und häufige Probleme beim Bereitstellen von Anwendungen in einer Produktionsumgebung zu vermeiden.

## <a name="resource-links-and-support"></a>Links zu Ressourcen und Support

Wenden Sie sich bei Supportfragen, Problemen oder Anpassungsanforderungen an den [Red Hat-Support](https://access.redhat.com/support) oder [Microsoft Azure-Support](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

* Weitere Informationen: [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/getting_started_with_jboss_eap_for_openshift_online/index)
* Red Hat Subscription Manager (RHSM) [Cloudzugriff](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)
* [Azure Red Hat OpenShift (ARO)](https://azure.microsoft.com/services/openshift/)
* Microsoft-Dokumentation zu [Red Hat in Azure](./overview.md)
* [RHEL BYOS Gold-Images in Azure](./byos.md)
* [Schnellstart-Videotutorial](https://www.youtube.com/watch?v=3DgpVwnQ3V4) zu JBoss EAP on Azure 

## <a name="next-steps"></a>Nächste Schritte

* [Anfrage zur Migration von JBoss EAP in Azure inquiry](https://aka.ms/JavaCloud)
* Ausführen von JBoss EAP in [Azure App Service](/azure/developer/java/ee/jboss-on-azure)
* Bereitstellen von JBoss EAP für RHEL-VM/-VM-Skalierungsgruppen über [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Bereitstellen von JBoss EAP für RHEL-VM/-VM-Skalierungsgruppen über [Azure Quickstart](https://aka.ms/Quickstart-JBoss-EAP)
* Verwenden des [Azure App Service-Migrations-Assistenten](https://azure.microsoft.com/services/app-service/migration-assistant/)
* Verwenden des Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
