---
title: Verwenden der im Azure Security Center enthaltenen Lizenz für Microsoft Defender für den Endpunkt
description: Hier erfahren Sie mehr über Microsoft Defender für den Endpunkt und dessen Bereitstellung vom Azure Security Center aus.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/08/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9441d285a97ca4c3a1ee46ab40c49f71f5d405f3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021398"
---
# <a name="protect-your-endpoints-with-defender-for-clouds-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Schützen Sie Ihre Endpunkte mit der im Security Center integrierten EDR-Lösung: Microsoft Defender für den Endpunkt

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für den Endpunkt ist eine ganzheitliche, cloudbasierte Lösung für die Endpunktsicherheit. Wichtigste Features:

- Risikobasierte Verwaltung und Bewertung von Sicherheitsrisiken 
- Verringerung der Angriffsfläche
- Verhaltensbasierter und cloudgestützter Schutz
- Endpoint Detection and Response (EDR, Endpunkterkennung und Reaktion)
- Automatisierte Untersuchung und Wiederherstellung
- Verwaltete Bedrohungssuchdienste

> [!TIP]
> Endpoint Detection and Response (EDR) wurde ursprünglich als **Windows Defender ATP** veröffentlicht und 2019 in **Microsoft Defender ATP** umbenannt.
>
> Auf der Ignite 2020 wurde die [Microsoft Defender XDR-Suite](https://www.microsoft.com/security/business/threat-protection) vorgestellt, und diese EDR-Komponente wurde in **Microsoft Defender für den Endpunkt** umbenannt.


## <a name="availability"></a>Verfügbarkeit

| Aspekt                                       | Details                                                                                                                                                                                                                                                                               |
|----------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:                               | • Integration in Defender für Endpunkt für Windows: allgemeine Verfügbarkeit (General Availability, GA)<br> • Integration in Defender für Endpunkt für Linux: Vorschauversion                                                                                                                                     |
| Preise:                                     | Erfordert [Microsoft Defender für Server](defender-for-servers-introduction.md)                                                                                                                                                                                                           |
| Unterstützte Umgebungen:                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Azure Arc-Computer unter Windows/Linux<br>:::image type="icon" source="./media/icons/yes-icon.png":::Virtuelle Azure-Computer unter Linux ([unterstützte Versionen](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux))<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Virtuelle Azure-Computer unter Windows Server 2022, 2019, 2016, 2012 R2, 2008 R2 SP1, [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md), [Windows 10 Enterprise Multisession](../virtual-desktop/windows-10-multisession-faq.yml) (ehemals Enterprise for Virtual Desktops (EVD)<br>:::image type="icon" source="./media/icons/no-icon.png"::: Virtuelle Azure-Computer unter Windows 10 (außer EVD oder WVD)           |
| Erforderliche Rollen und Berechtigungen:              | • Zum Aktivieren/Deaktivieren der Integration: **Sicherheitsadministrator** oder **Besitzer**<br>• Zum Anzeigen von Defender für Endpunkt-Warnungen im Security Center: **Sicherheitsleseberechtigter**, **Leser**, **Ressourcengruppenmitwirkender**, **Ressourcengruppenbesitzer**, **Sicherheitsadministrator**, **Abonnementeigentümer** oder **Abonnementmitwirkender** |
| Clouds:                                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet                                                         |
|                                              |                                                                                                                                                                                                                                                                                       |

## <a name="benefits-of-integrating-microsoft-defender-for-endpoint-with-defender-for-cloud"></a>Vorteile der Integration von Microsoft Defender für Endpunkt im Security Center

Microsoft Defender für den Endpunkt bietet folgende Features:

- **Erweiterte Erkennungssensoren für Sicherheitsverletzungen**. Sensoren von Defender für den Endpunkt sammeln eine Vielzahl von verhaltensbezogenen Signalen von Ihren Computern.

- **Sicherheitsrisikobewertung durch die Lösung zum Bedrohungs- und Sicherheitsrisikomanagement von Microsoft**. Wenn Microsoft Defender für Endpunkt aktiviert ist, kann das Security Center vom Bedrohungs- und Sicherheitsrisikomanagement-Modul entdeckte Sicherheitsrisiken anzeigen und dieses Modul auch als unterstützte Lösung zur Sicherheitsrisikobewertung anbieten. Weitere Informationen finden Sie unter [Untersuchen Sie Schwachstellen mit dem Bedrohungs- und Schwachstellenmanagement von Microsoft Defender for Endpoint](deploy-vulnerability-assessment-tvm.md).

    Dieses Modul enthält auch die unter [Zugreifen auf einen Softwarebestand](asset-inventory.md#access-a-software-inventory) beschriebenen Softwarebestandsfeatures und kann automatisch mit [Einstellungen für die automatische Bereitstellung](auto-deploy-vulnerability-assessment.md) für unterstützte Computer aktiviert werden.

- **Auf Analysen basierende, cloudgestützte Erkennung von Sicherheitsverletzungen**. Defender für den Endpunkt nimmt eine schnelle Anpassung an sich ändernde Bedrohungen vor. Das Produkt verwendet fortschrittliche Analysen und Big Data. Es wird durch die Leistungsfähigkeit von Intelligent Security Graph mit Signalen aus Windows, Azure und Office verstärkt, um unbekannte Bedrohungen zu erkennen. Das Produkt stellt aussagekräftige Warnmeldungen zur Verfügung und ermöglicht eine schnelle Reaktion.

- **Informationen zu Bedrohungen:** Defender für den Endpunkt generiert Warnungen, wenn es Angriffstools, -techniken und -verfahren erkennt. Das Produkt verwendet Daten, die von Microsoft-Bedrohungsspezialisten und Sicherheitsteams generiert werden, ergänzt durch Informationen von Partnern.

Durch die Integration von Defender für den Endpunkt im Security Center können Sie von den folgenden zusätzlichen Funktionen profitieren:

- **Automatisiertes Onboarding**. Der Sensor von Defender für Endpunkt wird vom Security Center auf allen unterstützten Computern automatisch aktiviert, die mit dem Security Center verbunden sind.

- **Zentralisierte Benutzeroberfläche**. Auf den Seiten des Portals vom Security Center werden Warnungen von Defender für Endpunkt angezeigt. Zur weiteren Untersuchung verwenden Sie die speziellen Portalseiten von Microsoft Defender für den Endpunkt, auf denen Sie zusätzliche Informationen wie die Warnungsprozessstruktur und das Incidentdiagramm finden. Sie können auch eine detaillierte Computerzeitachse verwenden, die jedes Verhalten über einen historischen Zeitraum von bis zu sechs Monaten anzeigt.

    :::image type="content" source="./media/integration-defender-for-endpoint/microsoft-defender-security-center.png" alt-text="Security Center von Microsoft Defender für den Endpunkt" lightbox="./media/integration-defender-for-endpoint/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Welche Anforderungen gelten für Mandanten für den Microsoft Defender für den Endpunkt?

Wenn Sie das Security Center zum Überwachen Ihrer Computer verwenden, wird für Defender für Endpunkt automatisch ein Mandant erstellt.

- **Speicherort:** Die von Defender für den Endpunkt gesammelten Daten werden im Rahmen der Bereitstellung des Mandanten am Standort des Mandanten gespeichert. Kundendaten (in pseudonymisierter Form) können auch in den zentralen Speicher- und Verarbeitungssystemen in den USA gespeichert werden. Nachdem Sie den Speicherort konfiguriert haben, kann dieser nicht mehr geändert werden. Wenn Sie Ihre eigene Lizenz für Microsoft Defender für Endpunkt besitzen und Ihre Daten an einen anderen Speicherort verschieben müssen, [bitten Sie den Microsoft-Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), den Mandanten zurückzusetzen.
- **Verschieben von Abonnements:** Wenn Sie Ihr Azure-Abonnement zwischen Azure-Mandanten verschoben haben, sind ein paar manuelle Vorbereitungsschritte erforderlich, bevor das Security Center Defender für Endpoint bereitstellen kann. Ausführliche Informationen erhalten Sie vom [Microsoft-Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>Aktivieren von Microsoft Defender für die Integration des Endpunkts

### <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass Ihr Computer die erforderlichen Anforderungen für Defender für Endpunkt erfüllt:

1. Stellen Sie sicher, dass der Computer wie gefordert mit Azure und dem Internet verbunden ist:

    - **Virtuelle Azure-Computer (Windows oder Linux)** : Konfigurieren Sie die Netzwerkeinstellungen, die unter Konfigurieren von Geräteproxy- und Internetkonnektivitätseinstellungen beschrieben werden: [Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet) oder [Linux](/microsoft-365/security/defender-endpoint/linux-static-proxy-configuration).

    - **Lokale Computer**: Verbinden Sie die Zielcomputer wie unter [Verbinden von Hybridcomputern mit Servern mit Azure Arc-Unterstützung](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) beschrieben mit Azure Arc.

1. **Microsoft Defender für den Server** aktivieren. Weitere Informationen finden Sie unter [Schnellstart: Aktivieren der erweiterten Sicherheitsfeatures des Security Centers](enable-enhanced-security.md).

    > [!IMPORTANT]
    > Die Integration des Security Centers bei Microsoft Defender für Endpunkt ist standardmäßig aktiviert. Wenn Sie die erweiterten Sicherheitsfeatures aktivieren, erteilen Sie damit also Azure Defender die Zustimmung, dass Server auf die Daten von Microsoft Defender für Endpoint im Zusammenhang mit Sicherheitsrisiken, installierter Software und Warnungen für Ihre Endpunkte zugreifen dürfen.

1. Wenn Sie Ihr Abonnement zwischen Azure-Mandanten verschoben haben, sind auch einige manuelle Vorbereitungsschritte erforderlich. Ausführliche Informationen erhalten Sie vom [Microsoft-Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).




### <a name="enable-the-integration"></a>Die Integration aktivieren

### <a name="windows"></a>[**Windows**](#tab/windows)

1. Wählen Sie im Menü vom Security Center **Umgebungseinstellungen** und anschließend das Abonnement mit den Windows-Computern aus, für die Defender für Endpunkt bereitgestellt werden soll.

1. Wählen Sie **Integrationen** aus.

1. Wählen Sie **Microsoft Defender für Endpunkt den Zugriff auf meine Daten erlauben** und dann **Speichern** aus.

    :::image type="content" source="./media/integration-defender-for-endpoint/enable-integration-with-edr.png" alt-text="Aktivieren der Integration zwischen dem Azure Security Center und Microsoft Defender für den Endpunkt, der EDR-Lösung von Microsoft":::

    Die Computer werden vom Azure Security Center automatisch in Microsoft Defender für Endpunkt integriert. Das Onboarding kann bis zu 24 Stunden dauern.

### <a name="linux-preview"></a>[**Linux** (Vorschau)](#tab/linux)

Während des Vorschauzeitraums haben Sie zwei Möglichkeiten, Defender für Endpunkt auf Linux-Computern bereitzustellen, je nachdem, ob Sie Defender bereits auf Ihren Windows-Computern bereitgestellt haben:

- [Vorhandene Nutzer mit aktivierten erweiterten Sicherheitsfeatures des Security Centers und Microsoft Defender für Endpoint für Windows](#existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows)
- [Neue Benutzer, die die Integration in Microsoft Defender für Endpunkt für Windows noch nie aktiviert haben](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)


### <a name="existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows"></a>Vorhandene Nutzer mit aktivierten erweiterten Sicherheitsfeatures des Security Centers und Microsoft Defender für Endpoint für Windows

Wenn Sie die Integration in **Defender für Endpunkt für Windows** bereits aktiviert haben, haben Sie die vollständige Kontrolle darüber, wann und ob Defender für Endpunkt auf Ihren **Linux**-Computern bereitgestellt wird.

1. Wählen Sie im Menü des Security Centers **Umgebungseinstellungen** und anschließend das Abonnement mit den Linux-Computern aus, für die Defender für Endpunkt bereitgestellt werden soll.

1. Wählen Sie **Integrationen** aus. Ob die Integration aktiviert ist, erkennen Sie daran, ob das Kontrollkästchen **Microsoft Defender für Endpunkt den Zugriff auf meine Daten erlauben** wie dargestellt aktiviert ist:

    :::image type="content" source="./media/integration-defender-for-endpoint/integration-enabled.png" alt-text="Die Integration zwischen dem Azure Security Center und Microsoft Defender für den Endpunkt, der EDR-Lösung von Microsoft, ist aktiviert.":::

    > [!NOTE]
    > Wenn das Kontrollkästchen nicht aktiviert ist, befolgen Sie die Anleitung unter [Neue Benutzer, die die Integration in Microsoft Defender für Endpunkt für Windows noch nie aktiviert haben](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows).

    Bei dieser Vorschauversion wurde unterhalb dieses Kontrollkästchens die Schaltfläche **Enable for Linux machines** (Für Linux-Computer aktivieren) neu eingefügt:

    :::image type="content" source="./media/integration-defender-for-endpoint/deploy-to-linux.png" alt-text="Mit der Vorschauversion wurde eine Schaltfläche hinzugefügt, über die manuell festgelegt werden kann, wann Defender für Endpunkt für Linux bereitgestellt wird.":::

1. So fügen Sie Ihre Linux-Computer zu Ihrer Integration hinzu

    1. Wählen Sie **Enable for Linux machines** (Für Linux-Computer aktivieren) aus.
    1. Wählen Sie **Speichern** aus.
    1. Überprüfen Sie die Informationen in der Bestätigungsaufforderung. Wenn Sie zufrieden sind, wählen Sie **Aktivieren** aus, um den Vorgang fortzusetzen. 

    :::image type="content" source="./media/integration-defender-for-endpoint/enable-for-linux-result.png" alt-text="Bestätigen der Integration zwischen dem Security Center und Microsoft Defender für Endpunkt für Linux, der EDR-Lösung von Microsoft":::

    Das Azure Security Center:

    - Ihre Linux-Computer werden automatisch in Defender für Endpunkt integriert.
    - Computer, auf denen andere fanotify-basierte Lösungen ausgeführt werden, werden ignoriert (Informationen zur erforderlichen Kerneloption `fanotify` finden Sie unter [Linux-Systemanforderungen](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements))
    - Frühere Installationen von Defender für Endpunkt werden erkannt und zur Integration im Security Center neu konfiguriert.

    Diese Integration kann bis zu 1 Stunden dauern.

    > [!NOTE]
    > Wenn Sie das nächste mal zu dieser Seite des Azure-Portals zurückkehren, wird die Schaltfläche **Enable for Linux machines** (Für Linux-Computer aktivieren) nicht mehr angezeigt. Wenn Sie die Integration für Linux deaktivieren möchten, müssen Sie auch die Integration für Windows deaktivieren. Hierzu deaktivieren Sie das Kontrollkästchen für **Microsoft Defender für Endpunkt den Zugriff auf meine Daten erlauben** und wählen **Speichern** aus.


1. Wenn Sie die Installation von Defender für Endpunkt auf einem Linux-Computer überprüfen möchten, führen Sie auf Ihren Computern den folgenden Shell-Befehl aus:

    `mdatp health`

    Wenn Microsoft Defender für Endpunkt installiert ist, wird der entsprechende Integritätsstatus angezeigt:

    `healthy : true`

    `licensed: true`

    Darüber hinaus wird im Azure-Portal eine neue Azure-Erweiterung namens `MDE.Linux` auf Ihren Computern angezeigt.

### <a name="new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows"></a>Neue Benutzer, die die Integration in Microsoft Defender für Endpunkt für Windows noch nie aktiviert haben

Wenn Sie die Integration für Windows bisher noch nicht aktiviert haben, wird das Security Center mit der Option **Microsoft Defender für Endpunkt den Zugriff auf meine Daten erlauben** es ermöglichen, dass Defender für Endpunkt auf Ihren Windows- *und* auf Ihren Linux-Computern bereitgestellt wird.

1. Wählen Sie im Menü des Security Centers **Umgebungseinstellungen** und anschließend das Abonnement mit den Linux-Computern aus, für die Defender für Endpunkt bereitgestellt werden soll.

1. Wählen Sie **Integrationen** aus.

1. Wählen Sie **Microsoft Defender für Endpunkt den Zugriff auf meine Daten erlauben** und dann **Speichern** aus.

    Das Azure Security Center:

    - Ihre Windows- und Linux-Computer werden automatisch in Defender für Endpunkt integriert.
    - Linux-Computer, auf denen andere fanotify-basierte Lösungen ausgeführt werden, werden ignoriert (Informationen zur erforderlichen Kerneloption `fanotify` finden Sie unter [Linux-Systemanforderungen](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements))
    - Frühere Installationen von Defender für Endpunkt werden erkannt und zur Integration im Security Center neu konfiguriert.

    Diese Integration kann bis zu 1 Stunden dauern.

1. Wenn Sie die Installation von Defender für Endpunkt auf einem Linux-Computer überprüfen möchten, führen Sie auf Ihren Computern den folgenden Shell-Befehl aus:

    `mdatp health`

    Wenn Microsoft Defender für Endpunkt installiert ist, wird der entsprechende Integritätsstatus angezeigt:

    `healthy : true`

    `licensed: true`

    Darüber hinaus wird im Azure-Portal eine neue Azure-Erweiterung namens `MDE.Linux` auf Ihren Computern angezeigt.
--- 

## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Zugreifen auf das Portal von Microsoft Defender für den Endpunkt

1. Vergewissern Sie sich, dass das Benutzerkonto über die notwendigen Berechtigungen verfügt. Weitere Informationen finden Sie unter [Zuweisen von Benutzerzugriff auf Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Überprüfen Sie, ob Sie einen Proxy oder eine Firewall einsetzen, der bzw. die anonymen Datenverkehr blockiert. Der Sensor von Defender für den Endpunkt stellt eine Verbindung aus dem Systemkontext her, daher muss anonymer Datenverkehr zugelassen werden. Um den ungehinderten Zugriff auf das Portal von Defender für den Endpunkt sicherzustellen, befolgen Sie die Anweisungen unter [Aktivieren des Zugriffs auf Dienst-URLs im Proxyserver](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. [Security Center von Microsoft Defender für den Endpunkt](https://securitycenter.windows.com/) öffnen. Weitere Informationen zu den Features und Symbolen des Portals finden Sie in der [Übersicht über das Portal des Security Centers Defender für den Endpunkt](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 






## <a name="send-a-test-alert"></a>Senden einer Testwarnung

Wenn von Defender für Endpunkt eine harmlose Testwarnung generiert werden soll, wählen Sie die Registerkarte für das jeweilige Betriebssystem Ihres Endpunkts aus:

### <a name="windows"></a>[**Windows**](#tab/windows)

Für Endpunkte unter Windows:

1. Erstellen Sie einen Ordner „C:\test-MDATP-test“.
1. Verwenden Sie Remotedesktop, um auf Ihren Computer zuzugreifen.
1. Öffnen Sie ein Befehlszeilenfenster.
1. Kopieren Sie an der Eingabeaufforderung den folgenden Befehl, und führen Sie ihn aus. Das Eingabeaufforderungsfenster wird automatisch geschlossen.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/integration-defender-for-endpoint/generate-edr-alert.png" alt-text="Ein Eingabeaufforderungsfenster mit dem Befehl zum Generieren einer Testwarnung":::

    Wenn der Befehl erfolgreich ist, wird Ihnen eine neue Warnung auf dem Dashboard des Azure Security Centers und im Portal von Microsoft Defender für den Endpunkt angezeigt. Die Anzeige dieser Warnung kann einige Minuten dauern.
1. Um die Warnung im Security Center zu überprüfen, navigieren Sie zu **Sicherheitswarnungen** > **Verdächtige PowerShell-Befehlszeile**.
1. Wählen Sie im Untersuchungsfenster den Link aus, um zum Portal von Microsoft Defender für den Endpunkt zu navigieren.

    > [!TIP]
    > Die Warnung wird mit dem Schweregrad **Info** ausgelöst.


### <a name="linux"></a>[**Linux**](#tab/linux)

Für Endpunkte unter Linux:

1. Laden Sie unter https://aka.ms/LinuxDIY das Tool für Testwarnungen herunter.
1. Extrahieren Sie den Inhalt der ZIP-Datei, und führen Sie das folgende Shell-Skript aus:

    `./mde_linux_edr_diy`

    Wenn der Befehl erfolgreich ist, wird Ihnen eine neue Warnung auf dem Dashboard des Azure Security Centers und im Portal von Microsoft Defender für den Endpunkt angezeigt. Die Anzeige dieser Warnung kann einige Minuten dauern.

1. Wenn Sie die Warnung im Security Center überprüfen möchten, navigieren Sie zu **Sicherheitswarnungen** > **Auflistung von Dateien mit vertraulichen Daten** (Enumeration of files with sensitive data).
1. Wählen Sie im Untersuchungsfenster den Link aus, um zum Portal von Microsoft Defender für den Endpunkt zu navigieren.

    > [!TIP]
    > Die Warnung wird mit **niedrigem** Schweregrad ausgelöst.

--- 



## <a name="faq---defender-for-clouds-integration-with-microsoft-defender-for-endpoint"></a>FAQ - Die Integration des Security Centers bei Microsoft Defender für Endpunkt

- [Worum handelt es sich bei der „MDE.Windows“- bzw. „MDE.Linux“-Erweiterung, die auf meinem Computer ausgeführt wird?](#whats-this-mdewindows--mdelinux-extension-running-on-my-machine)
- [Welche Lizenzierungsanforderungen gelten für Microsoft Defender für den Endpunkt?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Kann ich für Azure Defender einen Rabatt erhalten, wenn ich bereits über eine Lizenz für Microsoft Defender für Endpunkt verfüge?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-microsoft-defender-for-servers)
- [Wie steige ich von einem Drittanbieter-EDR-Tool um?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="whats-this-mdewindows--mdelinux-extension-running-on-my-machine"></a>Worum handelt es sich bei der „MDE.Windows“- bzw. „MDE.Linux“-Erweiterung, die auf meinem Computer ausgeführt wird?

In der Vergangenheit wurde Microsoft Defender für Endpunkt vom Log Analytics-Agent bereitgestellt. Als wir die [Unterstützung um Windows Server 2019 ](release-notes-archive.md#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga) und Linux erweitert haben, haben wir auch eine Erweiterung zum Ausführen des automatischen Onboardings hinzugefügt. 

Die Erweiterung wird vom Security Center auf Computern mit folgenden Betriebssystemen automatisch bereitgestellt:

- Windows Server 2019
- Windows 10 Virtual Desktop (WVD)
- Andere Versionen von Windows Server, wenn das Security Center das Betriebssystem nicht erkennt (z. B. wenn ein benutzerdefiniertes VM-Image verwendet wird). In diesem Fall wird Microsoft Defender für Endpunkt weiterhin vom Log Analytics-Agent bereitgestellt.
- Linux.

> [!IMPORTANT]
> Wenn Sie die MDE.Windows-Erweiterung löschen, entfernt dies nicht Microsoft Defender für Endpunkt. Informationen zum Offboarden finden Sie unter [Offboarding von Windows-Servern](/microsoft-365/security/defender-endpoint/configure-server-endpoints).

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Welche Lizenzierungsanforderungen gelten für Microsoft Defender für den Endpunkt?
Defender für Endpunkt ist ohne zusätzliche Kosten in **Azure Defender für Server** enthalten. Alternativ kann er separat für 50 Computer oder mehr erworben werden.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-microsoft-defender-for-servers"></a>Kann ich für Azure Defender einen Rabatt erhalten, wenn ich bereits über eine Lizenz für Microsoft Defender für Endpunkt verfüge?
Wenn Sie bereits eine Lizenz für Microsoft Defender für Endpunkt besitzen, müssen Sie für diesen Teil Ihrer Azure-Defender-Lizenz nicht bezahlen.

Fordern Sie Ihren Rabatt beim [Supportteam des Security Centers](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) an. Dazu müssen Sie die entsprechende Arbeitsbereichs-ID, die Region und die Anzahl der Microsoft Defender for Endpoint-Lizenzen angeben, die für Computer im angegebenen Arbeitsbereich angewendet wurden.

Der Rabatt gilt ab dem Genehmigungsdatum und wird nicht rückwirkend wirksam.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Wie steige ich von einem Drittanbieter-EDR-Tool um?
Vollständige Anweisungen zum Wechseln von einer Nicht-Microsoft-Endpunktlösung finden Sie in der Dokumentation zu Microsoft Defender für den Endpunkt: [Übersicht über die Migration](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).


## <a name="next-steps"></a>Nächste Schritte

- [Vom Azure Security Center unterstützte Plattformen und Features](security-center-os-coverage.md)
- [Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz Ihrer Azure-Ressourcen helfen](review-security-recommendations.md)
