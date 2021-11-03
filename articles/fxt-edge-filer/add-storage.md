---
title: 'Tutorial: Hinzufügen von Speicher zu einem Azure FXT Edge Filer-Cluster'
description: Konfigurieren von Back-End-Speicher und des clientseitigen Pseudo-Namespace für Azure FXT Edge Filer
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: f3995ee02894c9cddb4f9941999b057bfeb1d260
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044967"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Tutorial: Hinzufügen von Back-End-Speicher und Konfigurieren des virtuellen Namespace

Dieses Tutorial erklärt, wie Sie Back-Edge-Speicher für Ihren Cache hinzufügen und das clientseitige virtuelle Dateisystem einrichten.

Der Cluster stellt eine Verbindung mit Back-End-Speichersystemen her, um auf die Datenclients-Anforderung zuzugreifen und Änderungen auf beständigere Weise als im Cache zu speichern.

Der Namespace ist das clientseitige Pseudo-Dateisystem, mit dem Sie den Back-End-Speicher austauschen können, ohne die clientseitigen Workflows zu ändern.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Hinzufügen von Back-End-Speicher zum Azure FXT Edge Filer-Cluster
> * Definieren des clientseitigen Pfads für den Speicher

## <a name="about-back-end-storage"></a>Informationen zu Back-End-Speicher

Der Azure FXT Edge Filer-Cluster verwendet eine *Kernspeichereinheit*-Definition, um ein Back-End-Speichersystem mit dem FXT-Cluster zu verknüpfen.

Azure FXT Edge Filer ist mit mehreren gängigen NAS-Hardwaresystemen kompatibel und kann leere Container von Azure Blob oder andere Cloudspeicher verwenden.

Cloudspeichercontainer müssen beim Hinzufügen leer sein, damit das FXT-Betriebssystem alle Daten auf dem Cloudspeichervolume vollständig verwalten kann. Sie können Ihre vorhandenen Daten in den Cloudcontainer verschieben, nachdem Sie den Container als Kernspeichereinheit zum Cluster hinzugefügt haben.

Verwenden Sie die Einstellungen, um Ihrem System eine Kernspeichereinheit hinzuzufügen.

> [!NOTE]
>
> Wenn Sie Amazon AWS- oder Google Cloud-Speicher verwenden möchten, müssen Sie eine Featurelizenz für FlashCloud<sup>TM</sup> installieren. Wenden Sie sich an Ihren Microsoft-Vertreter, um einen Lizenzschlüssel zu erhalten, und folgen Sie dann den Anweisungen im Legacy-Konfigurationsleitfaden für [das Hinzufügen oder Entfernen von Featurelizenzen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
>
> Unterstützung für Azure Blob-Speicher ist in der Azure FXT Edge Filer-Softwarelizenz enthalten.

Ausführlichere Informationen zum Hinzufügen von Kernspeichereinheiten finden Sie in diesen Abschnitten des Cluster-Konfigurationsleitfadens:

* Weitere Informationen zur Auswahl und Vorbereitung auf das Hinzufügen einer Kernspeichereinheit finden Sie unter [Working With Core Filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview) (Arbeiten mit Kernspeichereinheiten).
* Detaillierte Voraussetzungen und Schritt-für-Schritt-Anleitungen finden Sie in diesen Artikeln:

  * [Adding a New NAS Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas) (Hinzufügen einer neuen NAS-Kernspeichereinheit)
  * [Adding a New Cloud Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud) (Hinzufügen einer neuen Cloud-Kernspeichereinheit)

Nachdem Sie eine Kernspeichereinheit hinzugefügt haben, können Sie ihre Einstellungen auf der Einstellungsseite für Kernspeichereinheitdetails aktualisieren.

## <a name="add-a-core-filer"></a>Hinzufügen einer Kernspeichereinheit

Definieren Sie eine Kernspeichereinheit, indem Sie auf der Einstellungsseite **Core Filer** > **Manage Core Filers** (Kernspeichereinheit > Kernspeichereinheiten verwalten) auf die Schaltfläche **Create** (Erstellen) klicken.

![Klicken auf die Schaltfläche „Erstellen“ oberhalb der Liste auf der Seite „Kernspeichereinheiten verwalten“](media/fxt-cluster-config/create-core-filer-button.png)

Der Assistent zum **Hinzufügen einer neuen Kernspeichereinheit** führt Sie durch den Prozess der Erstellung einer Kernspeichereinheit, die mit Ihrem Back-End-Speicher verknüpft ist. Der Cluster-Konfigurationsleitfaden enthält Schritt-für-Schritt-Beschreibungen des Prozesses, der sich für NFS/NAS-Speicher und Cloud-Speicher unterscheidet (Links finden Sie oben).

Subtasks umfassen:

* Angeben des Typs der Kernspeichereinheit (NAS oder Cloud)

  ![Erste Seite des Assistenten für neue Kernspeichereinheiten für NAS-Hardware Die Option für „Cloud-Kernspeichereinheit“ ist deaktiviert, und eine Fehlermeldung über die fehlende Lizenz wird angezeigt.](media/fxt-cluster-config/new-nas-1.png)

* Legen Sie den Namen der Kernspeichereinheit fest. Wählen Sie einen Namen, der den Clusteradministrator darüber informiert, um welches Speichersystem es sich handelt.

* Geben Sie für NAS-Kernspeichereinheiten den vollqualifizierten Domänennamen (FQDN) oder die IP-Adresse an. Der FQDN wird für alle Kernspeichereinheit empfohlen und ist für SMB-Zugriff erforderlich.

* Wählen Sie eine Cacherichtlinie aus – die zweite Seite des Assistenten listet die verfügbaren Cacherichtlinien für die neue Kernspeichereinheit auf. Weitere Informationen finden Sie im Abschnitt zu [Cacherichtlinien in der Anleitung zur Clusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html).

  ![Zweite Seite des Assistenten für neue Kernspeichereinheiten für NAS-Hardware; das Dropdownmenü für die Cacherichtlinie ist geöffnet und zeigt mehrere deaktivierte Optionen und drei gültige Cacherichtlinienoptionen (Umgehung, Zwischenspeichern von Lesevorgängen und Zwischenspeichern von Lese-/Schreibvorgängen).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Für Cloudspeicher müssen Sie unter anderem den Clouddienst und die Zugangsdaten angeben. Ausführliche Informationen finden Sie unter [Cloud Service and Protocol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) (Clouddienst und Protokoll) in der Anleitung zur Clusterkonfiguration.

  ![Informationen zur Cloud-Kernspeichereinheit im Assistenten für neue Kernspeichereinheiten](media/fxt-cluster-config/new-core-filer-cloud3.png)
  
  Wenn Sie bereits Cloud-Zugangsdaten für diesen Cluster hinzugefügt haben, werden diese in der Liste angezeigt. Sie können Anmeldeinformationen auf der Einstellungsseite **Cluster** > **Cloudanmeldeinformationen** aktualisieren und hinzufügen.

Nach der Angabe aller erforderlichen Einstellungen im Assistenten klicken Sie auf die Schaltfläche **Add Filer** (Speichereinheit hinzufügen), um die Änderung zu übermitteln.

Nach einigen Sekunden wird das Speichersystem in der Kernspeichereinheitenliste **Dashboard** angezeigt und kann über die Einstellungsseiten für Kernspeichereinheiten aufgerufen werden.

![Kernspeichereinheit „Flurry-NAS“ auf der Einstellungsseite „Kernspeichereinheiten verwalten“ mit erweiterter Ansicht der Speichereinheitendetails](media/fxt-cluster-config/core-filer-in-manage-page.png)

Der Kernspeichereinheit auf diesem Screenshot fehlt eine VServer-Instanz. Sie müssen die Kernspeichereinheit mit einer VServer-Instanz verknüpfen und eine Verbindung erstellen, damit Clients auf den Speicher zugreifen können. Diese Schritte sind unten in [Konfigurieren des Namespace](#configure-the-namespace) beschrieben.

## <a name="configure-the-namespace"></a>Konfigurieren des Namespace

Der Azure FXT Edge Filer-Cluster erstellt ein virtuelles Dateisystem, das als *Cluster-Namespace* bezeichnet wird. Dieses vereinfacht den Clientzugriff auf Daten, die auf verschiedenen Back-End-Systemen gespeichert sind. Da Clients Dateien über einen virtuellen Pfad anfordern, können Speichersysteme ohne Änderung des Client-Workflows hinzugefügt oder ersetzt werden.

Mit dem Cluster-Namespace können Sie auch Cloud- und NAS-Speichersysteme in einer ähnlichen Dateistruktur darstellen.

Die VServer-Instanzen des Clusters verwalten den Namespace und das Bereitstellen von Inhalt für Clients. Die Erstellung des Cluster-Namespace erfolgt in zwei Schritten:

1. Erstellen einer VServer-Instanz
1. Einrichten von Verbindungen zwischen den Back-End-Speichersystemen und den clientseitigen Dateisystempfaden

### <a name="create-a-vserver"></a>Erstellen einer VServer-Instanz

VServer-Instanzen sind virtuelle Dateiserver, die den Datenfluss zwischen dem Client und den Kernspeichereinheiten des Clusters steuern:

* VServer-Instanzen hosten clientseitige IP-Adressen.
* VServer-Instanzen erstellen den Namespace und definieren Verbindungen, die die clientseitige virtuelle Verzeichnisstruktur zu Exporten auf dem Back-End-Speicher zuordnen.
* VServer-Instanzen erzwingen Dateizugriffssteuerungen, einschließlich Exportrichtlinien für Kernspeichereinheiten und Benutzerauthentifizierungssystemen.
* VServer-Instanzen stellen die SMB-Infrastruktur bereit.

Bevor Sie mit der Konfiguration einer Cluster-VServer-Instanz beginnen, lesen Sie die verlinkte Dokumentation, und wenden Sie sich an Ihren Microsoft-Vertreter, um Hilfe beim Verständnis von Namespaces und VServer-Instanzen zu erhalten. Wenn Sie VLANs verwenden [erstellen](configure-network.md#adjust-network-settings) sie diese vor dem Erstellen der VServer-Instanz.

Diese Abschnitte in der Anleitung zur Clusterkonfiguration helfen Ihnen, sich mit FXT VServer und globalen Namespace-Funktionen vertraut zu machen:

* [Erstellen und Verwenden von VServer-Instanzen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Verwenden eines globalen Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Erstellen einer VServer-Instanz](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Sie benötigen mindestens eine VServer-Instanz für Ihren Cluster.

Zum Erstellen einer neuen VServer-Instanz benötigen Sie die folgenden Informationen:

* Den für die VServer-Instanz vorgesehenen Namen

* Den Bereich der clientseitigen IP-Adressen, die die VServer-Instanz verarbeitet

  Sie müssen einen einzigen Bereich von zusammenhängenden IP-Adressen angeben, wenn Sie die VServer-Instanz erstellen. Sie können später weitere Adressen über die Einstellungsseite **Clientseitiges Netzwerk** hinzufügen.

* Das für diese VServer-Instanz zu verwendende VLAN, wenn Ihr Netzwerk über VLANs verfügt

Verwenden Sie die Einstellungsseite **VServer** > **Manage VServers** (VServer > VServer verwalten), um eine neue VServer-Instanz zu erstellen. Details finden Sie unter [Creating a VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) (Erstellen einer VServer-Instanz) in der Anleitung zur Clusterkonfiguration.

![Popupfenster für das Erstellen einer neuen VServer-Instanz](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Erstellen einer Verbindung

Eine *Verbindung* ordnet dem für den Client sichtbaren Namespace einen Back-End-Speicherpfad zu.

Sie können dieses System verwenden, um den in Client-Bereitstellungspunkten verwendeten Pfad zu vereinfachen und um die Kapazität nahtlos zu skalieren, da ein virtueller Pfad Speicherplatz für mehrere Kernspeichereinheiten bieten kann.

![Assistentenseite „Neue Verbindung hinzufügen“ mit ausgefüllten Einstellungen](media/fxt-cluster-config/add-junction-full.png)

Ausführliche Informationen zum Erstellen einer Namespace-Verbindung finden Sie unter [**VServer** > **Namespace**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) in der Anleitung zur Clusterkonfiguration.

![Die Einstellungsseite „VServer“ > „Namespace“ mit den Details für eine Verbindung](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurieren von Exportregeln

Sobald Sie sowohl eine VServer-Instanz als auch eine Kernspeichereinheit haben, sollten Sie die Exportregeln und -richtlinien anpassen, die steuern, wie Clients auf Dateien in den Exporten der Kernspeichereinheit zugreifen können.

Fügen Sie zunächst über die Seite **VServer** > **Export Rules** (Exportregeln) neue Regeln hinzu, ändern Sie die Standardrichtlinie, oder erstellen Sie eine eigene benutzerdefinierte Exportrichtlinie.

Verwenden Sie dann die Seite **VServer** > **Export Policies** (Exportrichtlinien), um die angepasste Richtlinie auf Exporte Ihrer Kernspeichereinheit anzuwenden, wenn über diese VServer-Instanz darauf zugegriffen wird.

Details finden Sie im Artikel [Controlling Access to Core Filer Exports](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) (Steuern des Zugriffs auf Exporte der Kernspeichereinheit) in der Anleitung zur Clusterkonfiguration.

## <a name="next-steps"></a>Nächste Schritte

Schließen Sie nach dem Hinzufügen von Speicher und dem Konfigurieren des clientseitigen Namespace die Ersteinrichtung Ihres Clusters ab:

> [!div class="nextstepaction"]
> [Konfigurieren der Netzwerkeinstellungen des Clusters](configure-network.md)
