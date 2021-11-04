---
title: Konfigurieren von Azure HPC Cache-Einstellungen
description: In diesem Artikel wird erläutert, wie Sie zusätzliche Einstellungen wie die MTU-, benutzerdefinierte NTP- und DNS-Konfiguration für den Cache konfigurieren und auf die Expressmomentaufnahmen von Azure Blob Storage-Zielen zugreifen.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: femila
ms.openlocfilehash: ad4dedc023dc6ba5f58e51c816d422acb3ab2c69
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019987"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurieren zusätzlicher Azure HPC Cache-Einstellungen

Auf der Seite **Netzwerk** im Azure-Portal finden Sie Optionen zum Anpassen mehrerer Einstellungen. Die meisten Benutzer müssen für diese Einstellungen keine Änderungen gegenüber den Standardwerten vornehmen.

In diesem Artikel wird außerdem beschrieben, wie Sie die Momentaufnahmefunktion für Azure Blob Storage-Ziel verwenden. Die Momentaufnahmefunktion besitzt keine konfigurierbaren Einstellungen.

Öffnen Sie die Seite **Netzwerk** des Caches im Azure-Portal, um die Einstellungen anzuzeigen.

![Screenshot: Seite „Netzwerk“ im Azure-Portal](media/networking-page.png)

> [!NOTE]
> Eine frühere Version dieser Seite umfasste eine Root-Squash-Einstellung auf Cacheebene. Diese Einstellung befindet sich nun jedoch unter [Clientzugriffsrichtlinien](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Anpassen des MTU-Werts
<!-- linked from troubleshoot-nas article -->

Sie können die maximale Größe der Übertragungseinheit für den Cache mithilfe des Dropdownmenüs namens **MTU-Größe** auswählen.

Der Standardwert beträgt 1500 Bytes, aber Sie können ihn in 1400 ändern.

> [!NOTE]
> Wenn Sie die MTU-Größe des Caches verringern, stellen Sie sicher, dass die Clients und Speichersysteme, die mit dem Cache kommunizieren, dieselbe MTU-Einstellung oder einen niedrigeren Wert besitzen.

Wenn Sie den Wert für die Cache-MTU herabsetzen, kann dies hilfreich sein bei der Umgehung von Paketgrößeneinschränkungen im restlichen Netzwerk des Caches. Beispielsweise können einige VPNs keine 1500-Byte-Pakete in voller Größe erfolgreich übertragen. Wenn Sie die Größe der über das VPN gesendeten Pakete verringern, könnte dies das Problem beseitigen. Beachten Sie jedoch, dass eine niedrigere MTU-Einstellung des Caches bedeutet, dass alle anderen Komponenten, die mit dem Cache kommunizieren, einschließlich Clients und Speichersystemen, ebenfalls eine niedrigere MTU-Einstellung aufweisen müssen, um Kommunikationsprobleme zu vermeiden.

Wenn Sie die MTU-Einstellungen für andere Systemkomponenten nicht ändern möchten, sollten Sie die MTU-Einstellung des Caches nicht verringern. Es gibt noch andere Lösungen, um Einschränkungen der VPN-Paketgröße zu umgehen. Weitere Informationen zur Diagnose und Behebung dieses Problems finden Sie unter [Anpassen von Einschränkungen der VPN-Paketgröße](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) im Artikel zur Problembehandlung bei NAS.

Weitere Informationen zu den MTU-Einstellungen in virtuellen Azure-Netzwerken finden Sie unter [Optimierung der TCP/IP-Leistung für Azure-VMs](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Anpassen von NTP

Ihr Cache verwendet standardmäßig den Azure-basierten Zeitserver „time.windows.com“. Wenn Sie Ihr Cache einen anderen NTP-Server verwenden soll, legen Sie diesen im Abschnitt **NTP-Konfiguration** fest. Verwenden Sie einen vollqualifizierten Domänennamen (FQDN) oder eine IP-Adresse.

## <a name="set-a-custom-dns-configuration"></a>Festlegen einer benutzerdefinierten DNS-Konfiguration

> [!CAUTION]
> Ändern Sie die DNS-Konfiguration Ihres Caches nicht, wenn dies nicht erforderlich ist. Konfigurationsfehler können maßgebliche Konsequenzen haben. Wenn Ihre Konfiguration keine Namen von Azure-Diensten auflösen kann, ist die HPC-Cache-Instanz dauerhaft nicht erreichbar.
>
> Wenden Sie sich an den für Sie zuständigen Azure-Vertriebsmitarbeiter, bevor Sie versuchen, eine benutzerdefinierte DNS-Konfiguration einzurichten.

Azure HPC Cache wird automatisch zur Verwendung des sicheren und praktischen Azure DNS-Systems konfiguriert. Allerdings erfordern einige ungewöhnliche Konfigurationen, dass der Cache anstelle des Azure-Systems ein separates lokales DNS-System verwendet. Der Abschnitt **DNS-Konfiguration** der Seite **Netzwerk** wird zum Festlegen dieser Art von System verwendet.

Wenden Sie sich an Azure-Vertriebsmitarbeiter oder den Microsoft-Dienstsupport, um in Erfahrung zu bringen, ob Sie eine benutzerdefinierte Cache-DNS-Konfiguration verwenden müssen.

Wenn Sie Ihr eigenes lokales DNS-System für Azure HPC Cache konfigurieren, muss Ihr lokaler DNS-Server in der Lage sein, Azure-Endpunktnamen direkt aufzulösen. HPC Cache funktioniert nicht, wenn die Auflösung öffentlicher Namen für Ihren DNS-Server nicht zur Verfügung steht.

Stellen Sie sicher, dass Ihre DNS-Konfiguration diese Elemente erfolgreich auflösen kann, bevor Sie sie für Azure HPC Cache verwenden:

* ``*.core.windows.net``
* Zertifikatssperrlisten-Download und OCSP-Überprüfungsdienste (Online Certificate Status-Protokoll) Im Abschnitt zu [Firewallregeln](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) des [Azure-TLS-Artikels](../security/fundamentals/tls-certificate-changes.md) wird eine unvollständige Liste bereitgestellt, Sie sollten sich jedoch an einen technischen Vertreter von Microsoft wenden, um Informationen zu allen Anforderungen zu erhalten.
* Den vollqualifizierten Domänennamen Ihres NTP-Servers („time.windows.com“ oder ein benutzerdefinierter Server)

Wenn Sie einen benutzerdefinierten DNS-Server für Ihren Cache festlegen müssen, verwenden Sie die bereitgestellten Felder:

* **DNS-Suchdomäne** (optional): Geben Sie Ihre Suchdomäne ein, z. B. ``contoso.com``. Ein einzelner Wert ist zulässig. Alternativ können Sie diesen leer lassen.
* **DNS-Server:** Geben Sie bis zu drei DNS-Server ein. Geben Sie diese anhand der IP-Adresse an.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Verwenden Sie einen Test-Cache, um das DNS-Setup zu überprüfen und zu verfeinern, bevor Sie es in einer Produktionsumgebung verwenden.

### <a name="refresh-storage-target-dns"></a>Aktualisieren des Speicherziel-DNS

Wenn Ihr DNS-Server IP-Adressen aktualisiert, sind die zugehörigen NFS-Speicherziele vorübergehend nicht verfügbar. Informationen zum Aktualisieren der IP-Adressen Ihres benutzerdefinierten DNS-Systems finden Sie unter [Aufrufen und Verwalten von Speicherzielen](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Anzeigen von Momentaufnahmen für Blobspeicherziele

Azure HPC Cache speichert automatisch Speichermomentaufnahmen für Azure-Blobspeicherziele. Momentaufnahmen bieten einen schnellen Bezugspunkt für den Inhalt des Back-End-Speichercontainers.

Momentaufnahmen sind kein Ersatz für Datensicherungen und enthalten keine Informationen zum Zustand der zwischengespeicherten Daten.

> [!NOTE]
> Diese Momentaufnahmefunktion unterscheidet sich von der Momentaufnahmenfunktion, die in NetApp- oder Isilon-Speichersoftware enthalten ist. Diese Momentaufnahmeimplementierungen leeren Änderungen aus dem Cache in das Back-End-Speichersystem, bevor die Momentaufnahme erstellt wird.
>
> Aus Effizienzgründen leert die Azure HPC Cache-Momentaufnahme nicht zuerst Änderungen, sondern zeichnet nur Daten auf, die in den Blob-Container geschrieben wurden. Diese Momentaufnahme stellt nicht den Zustand von zwischengespeicherten Daten dar, weshalb aktuelle Änderungen möglicherweise nicht enthalten sind.

Diese Funktion ist nur für Azure-Blobspeicherziele verfügbar, und Ihre Konfiguration kann nicht geändert werden.

Momentaufnahmen werden alle acht Stunden (UTC 0:00, 08:00 und 16:00) erstellt.

Azure HPC Cache speichert tägliche, wöchentliche und monatliche Momentaufnahmen, bis sie durch neue ersetzt werden. Die Aufbewahrungsfristen für Momentaufnahmen sind:

* Bis zu 20 tägliche Momentaufnahmen
* Bis zu 8 wöchentliche Momentaufnahmen
* Bis zu 3 monatliche Momentaufnahmen

Greifen Sie auf die Momentaufnahmen aus dem `.snapshot` Verzeichnis im Stammverzeichnis des eingebundenen BLOB Storage-Ziels zu.
