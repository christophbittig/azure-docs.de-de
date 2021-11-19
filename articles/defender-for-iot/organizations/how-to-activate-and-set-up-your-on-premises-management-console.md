---
title: Aktivieren und Einrichten der lokalen Verwaltungskonsole
description: Durch die Aktivierung der Verwaltungskonsole wird sichergestellt, dass Sensoren bei Azure registriert werden, Informationen an die lokale Verwaltungskonsole gesendet werden und die lokale Verwaltungskonsole Verwaltungsaufgaben auf verbundenen Sensoren ausführt.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 77f2a62cce7da4f9faac62820a85f38c0dd06ce5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306223"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Aktivieren und Einrichten der lokalen Verwaltungskonsole 

Durch die Aktivierung und Einrichtung der lokalen Verwaltungskonsole wird Folgendes sichergestellt:

- Netzwerkgeräte, die Sie über verbundene Sensoren überwachen, werden bei einem Azure-Konto registriert.

- Sensoren senden Informationen an die lokale Verwaltungskonsole.

- Die lokale Verwaltungskonsole führt Verwaltungsaufgaben auf verbundenen Sensoren aus.

- Sie haben ein SSL-Zertifikat installiert.

## <a name="sign-in-for-the-first-time"></a>Erstmaliges Anmelden

**So melden Sie sich bei der Verwaltungskonsole an:**

1. Navigieren Sie zu der IP-Adresse, die Sie während der Systeminstallation für die lokale Verwaltungskonsole erhalten haben.
 
1. Geben Sie den Benutzernamen und das Kennwort ein, die Sie während der Systeminstallation für die lokale Verwaltungskonsole erhalten haben. 


Wenn Sie Ihr Kennwort vergessen haben, wählen Sie die Option **Kennwort wiederherstellen** aus, und lesen Sie die Anweisungen unter [Kennwortwiederherstellung](how-to-manage-the-on-premises-management-console.md#password-recovery) zum Wiederherstellen Ihres Kennworts.

## <a name="activate-the-on-premises-management-console"></a>Aktivieren der lokalen Verwaltungskonsole

Nachdem Sie sich zum ersten Mal angemeldet haben, müssen Sie die lokale Verwaltungskonsole aktivieren, indem Sie eine Aktivierungsdatei abrufen und hochladen. 

**Um die lokale Verwaltungskonsole zu aktivieren:**

1. Melden Sie sich bei der lokalen Verwaltungskonsole an.

1. Wählen Sie in der Warnungsbenachrichtigung am oberen Rand der Anzeige den Link **Aktion ausführen** aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/take-action.png" alt-text="Wählen Sie in der Warnung oben auf der Anzeige den Link Aktion ausführen aus.":::

1. Wählen Sie auf der Aktivierungs-Pop-up-Anzeige den **Azure-Portal**-Link aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/azure-portal.png" alt-text="Wählen Sie den Azure-Portal Link aus der Pop-up-Meldung aus.":::
 
1. Wählen Sie ein Abonnement aus, dem die lokale Verwaltungskonsole zugeordnet werden soll, und wählen Sie dann die Schaltfläche **Aktivierungsdatei für die lokale Verwaltungskonsole herunterladen aus**. Die Aktivierungsdatei wird heruntergeladen.

   Die lokale Verwaltungskonsole kann einem oder mehreren Abonnements zugeordnet werden. Die Aktivierungsdatei wird mit allen ausgewählten Abonnements und der Anzahl der bestätigten Geräte zum Zeitpunkt des Downloads verknüpft.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/multiple-subscriptions.png" alt-text="Sie können mehrere Abonnements für das Onboarding Ihrer lokalen Verwaltungskonsole auswählen.":::

   Wenn Sie noch kein Onboarding für das Abonnement durchgeführt haben, können Sie [ein Onboarding für das Abonnement durchführen](how-to-manage-subscriptions.md#onboard-a-subscription).

   > [!Note]
   > Wenn Sie ein Abonnement löschen, müssen Sie eine neue Aktivierungsdatei in alle lokalen Verwaltungskonsolen hochladen, die mit dem gelöschten Abonnement verbunden waren.

1. Navigieren Sie zurück zur **Aktivierungs**-Pop-up-Anzeige, und wählen Sie **Datei auswählen** aus.

1. Wählen Sie die heruntergeladene Datei aus.

Nach der erstmaligen Aktivierung kann die Anzahl der überwachten Geräte die Anzahl der zugesicherten Geräte überschreiten, die während des Onboardings definiert wurden. Dieses Problem tritt auf, wenn Sie weitere Sensoren mit der Verwaltungskonsole verbinden. Bei einer Abweichung zwischen der Anzahl überwachter Geräte und der Anzahl zugesicherter Geräte wird in der Verwaltungskonsole eine Warnung angezeigt. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/device-commitment-update.png" alt-text="Wenn die Warnung zur Gerätezusage angezeigt wird, müssen Sie eine neue Aktivierungsdatei hochladen.":::

Wenn diese Warnung angezeigt wird, müssen Sie eine neue [Aktivierungsdatei hochladen](#activate-the-on-premises-management-console).

### <a name="activate-an-expired-license-versions-under-100"></a>Aktivieren einer abgelaufenen Lizenz (Versionen unter 10.0)

Für Benutzer mit Versionen unter 10.0 läuft die Lizenz möglicherweise ab und die folgende Warnung wird angezeigt. 

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/activation-popup.png" alt-text="Wenn Ihre Lizenz abläuft, müssen Sie Ihre Lizenz über die Aktivierungsdatei aktualisieren.":::

**So aktivieren Sie Ihre Lizenz**

1. Öffnen Sie einen Fall beim [Support](https://portal.azure.com/?passwordRecovery=true&Microsoft_Azure_IoT_Defender=canary#create/Microsoft.Support).

1. Stellen Sie dem Support Ihre Aktivierungs-ID-Nummer bereit.

1. Der Support stellt Ihnen neue Lizenzinformationen in Form einer Zeichenfolge aus Buchstaben zur Verfügung.

1. Lesen Sie die Geschäftsbedingungen, und aktivieren Sie zur Genehmigung das Kontrollkästchen.

1. Fügen Sie die Zeichenfolge in das vorgesehene Feld ein.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/add-license.png" alt-text="Fügen Sie die Zeichenfolge in das vorgesehene Feld ein.":::

1. Wählen Sie **Aktivieren** aus.

## <a name="set-up-a-certificate"></a>Einrichten eines Zertifikats

Nachdem Sie die Verwaltungskonsole installiert haben, wird ein lokales selbst signiertes Zertifikat generiert. Dieses Zertifikat wird für den Zugriff auf die Konsole verwendet. Nachdem sich ein Administrator zum ersten Mal bei der Verwaltungskonsole angemeldet hat, wird dieser Benutzer aufgefordert, ein SSL/TLS-Zertifikat zu integrieren. 

Zwei Sicherheitsstufen stehen zur Verfügung:

- Erfüllen spezifischer Zertifikat- und Verschlüsselungsanforderungen, die von Ihrer Organisation angefordert wurden, durch Hochladen des von der Zertifizierungsstelle signierten Zertifikats.

- Zulassen der Überprüfung zwischen der Verwaltungskonsole und verbundenen Sensoren. Die Überprüfung wird anhand einer Zertifikatssperrliste und des Ablaufdatums des Zertifikats ausgewertet. *Wenn bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen der Verwaltungskonsole und dem Sensor angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt.* Diese Option wird nach der Installation standardmäßig aktiviert.  

Die Konsole unterstützt die folgenden Arten von Zertifikaten:

- Private und Enterprise Key-Infrastruktur (private PKI)

- Public Key-Infrastruktur (Public PKI)

- Lokal auf dem Gerät generiert (lokal selbstsigniert) 

  > [!IMPORTANT]
  > Wir empfehlen, dass Sie kein selbstsigniertes Zertifikat verwenden. Das Zertifikat ist nicht sicher und sollte nur für Testumgebungen verwendet werden. Der Besitzer des Zertifikats kann nicht überprüft und die Sicherheit Ihres Systems nicht aufrechterhalten werden. Verwenden Sie diese Option auf keinen Fall in Produktionsnetzwerken.

**So laden Sie ein Zertifikat hoch:**

1. Wenn Sie nach der Anmeldung dazu aufgefordert werden, definieren Sie einen Zertifikatnamen.

1. Laden Sie die CRT-Datei und die Schlüsseldatei hoch.

1. Geben Sie eine Passphrase ein, und laden Sie bei Bedarf eine PEM-Datei hoch.

Möglicherweise müssen Sie die Anzeige aktualisieren, nachdem Sie das von einer Zertifizierungsstelle signierte Zertifikat hochgeladen haben.

**So deaktivieren Sie die Überprüfung zwischen der Verwaltungskonsole und verbundenen Sensoren:**

1. Wählen Sie **Weiter** aus.

1. Schalten Sie den Umschalter für **Enable system-wide validation** (Systemweite Überprüfung aktivieren) auf „Aus“.

Informationen zum Hochladen eines neuen Zertifikats, unterstützter Zertifikatdateien und zugehöriger Elemente finden Sie unter [Verwalten der lokalen Verwaltungskonsole](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Verbinden von Sensoren mit der lokalen Verwaltungskonsole

Stellen Sie sicher, dass Sensoren Informationen an die lokale Verwaltungskonsole senden und dass die lokale Verwaltungskonsole Sicherungen ausführen, Warnungen verwalten sowie andere Aktivitäten auf den Sensoren ausführen kann. Mit den folgenden Verfahren können Sie überprüfen, ob eine anfängliche Verbindung zwischen Sensoren und der lokalen Verwaltungskonsole hergestellt wird.

Zum Verbinden von Microsoft Defender für IoT-Sensoren mit der lokalen Verwaltungskonsole gibt es zwei Optionen:

- Herstellen einer Verbindung über die Sensorkonsole

- Herstellen einer Verbindung mithilfe von Tunneln

Nachdem Sie eine Verbindung hergestellt haben, müssen Sie mit diesen Sensoren einen Standort einrichten.

### <a name="connect-sensors-to-the-on-premises-management-console-from-the-sensor-console"></a>So verbinden Sie bestimmte Sensoren über die Sensorkonsole mit der lokalen Verwaltungskonsole

**So verbinden Sie bestimmte Sensoren über die Sensorkonsole mit der lokalen Verwaltungskonsole:**

1. Wählen Sie in der lokalen Verwaltungskonsole die Option **Systemeinstellungen** aus.

1. Kopieren Sie **Kopieren der Verbindungszeichenfolge**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-string.png" alt-text="Kopieren Sie die Verbindungszeichenfolge für den Sensor.":::

1. Navigieren Sie auf dem Sensor zu **Systemeinstellungen** und wählen Sie **Verbindung mit Verwaltungskonsole** aus.:::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-to-management-console.png" border="false":::

1. Fügen Sie die kopierte Verbindungszeichenfolge aus der lokalen Verwaltungskonsole in das Feld **Verbindungszeichenfolge** ein.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/paste-connection-string.png" alt-text="Fügen Sie die Verbindungszeichenfolge im entsprechenden Feld ein.":::

1. Wählen Sie **Verbinden** aus.

### <a name="connect-sensors-by-using-tunneling"></a>Verbinden von Sensoren mithilfe von Tunneln

Ermöglichen Sie eine gesicherte Tunnelverbindung zwischen Organisationssensoren und der lokalen Verwaltungskonsole. Durch dieses Setup wird eine Interaktion mit der Organisationsfirewall umgangen und so die Angriffsfläche verringert.

Mithilfe von Tunneln können Sie die lokale Verwaltungskonsole über deren IP-Adresse und einen einzelnen Port (9000) mit einem beliebigen Sensor verbinden.

**So richten Sie Tunneln in der lokalen Verwaltungskonsole ein:**

- Melden Sie sich bei der lokalen Verwaltungskonsole an, und führen Sie die folgenden Befehle aus:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

**So richten Sie Tunneln auf dem Sensor ein:**

1. Öffnen Sie den TCP-Port 9000 manuell auf dem Sensor („network.properties“). Wenn der Port nicht geöffnet ist, lehnt der Sensor die Verbindung über lokale Verwaltungskonsole ab.

2. Melden Sie sich bei jedem Sensor an, und führen Sie die folgenden Befehle aus:

   ```bash
   sudo cyberx-xsense-management-connect -ip <on-premises management console IP Address> -token < Copy the string that appears after the IP colon (:) from the Connection String field, Management Console Connection dialog box>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Einrichten eines Standorts

Die Standardzuordnung für Unternehmen bietet eine allgemeine Ansicht Ihrer Geräte auf mehreren Ebenen geografischer Standorte.

Möglicherweise wird eine Ansicht Ihrer Geräte benötigt, in der die Organisationsstruktur und die Benutzerberechtigungen komplex sind. In diesen Fällen könnte die Standorteinrichtung – zusätzlich zur Standardstruktur von Standort oder Zone – durch eine globale Organisationsstruktur bestimmt werden.

Zur Unterstützung dieser Umgebung müssen Sie eine globale Unternehmenstopologie erstellen, die auf den Geschäftseinheiten, Regionen, Standorten und Zonen Ihrer Organisation basiert. Außerdem müssen Sie die Benutzerzugriffsberechtigungen für diese Entitäten mithilfe von Zugriffsgruppen definieren.

Zugriffsgruppen ermöglichen eine bessere Kontrolle darüber, wo Benutzer Geräte auf der Defender für IoT-Plattform verwalten und analysieren.

### <a name="how-it-works"></a>Funktionsweise

Sie können eine Geschäftseinheit und eine Region für jeden Standort in Ihrer Organisation definieren. Anschließend können Sie Zonen hinzufügen, die logische Entitäten in Ihrem Netzwerk sind. 

Weisen Sie jeder Zone mindestens einen Sensor zu. Das fünfstufige Modell bietet die Flexibilität und Granularität, die für die Bereitstellung des Schutzsystems erforderlich sind, das die Struktur Ihrer Organisation widerspiegelt.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Das Diagramm zeigt Sensoren und eine regionale Beziehung.":::

Mithilfe der Unternehmensansicht können Sie Ihre Sites direkt bearbeiten. Wenn Sie in der Unternehmensansicht einen Standort auswählen, wird die Anzahl der geöffneten Warnungen neben den einzelnen Zonen angezeigt.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Screenshot einer Karte der lokalen Verwaltungskonsole mit eingeblendeten Daten zu Berlin.":::

**So richten Sie einen Standort ein:**

1. Fügen Sie neue Geschäftseinheiten hinzu, um die logische Struktur Ihrer Organisation widerzuspiegeln.

   1. Wählen Sie in der Unternehmensansicht **Alle Standorte** > **Geschäftseinheiten verwalten** aus.

      :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-business-unit.png" alt-text="Wählen Sie in der Unternehmensansichtsanzeige im Dropdown-Menü für alle Sites die Option Geschäftseinheit verwalten aus.":::

   1. Geben Sie den Namen der neuen Geschäftseinheit ein, und wählen Sie **HINZUFÜGEN** aus.

1. Fügen Sie neue Regionen hinzu, um die Regionen Ihrer Organisation widerzuspiegeln.

   1. Wählen Sie in der Unternehmensansicht **Alle Regionen** > **Regionen verwalten** aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-regions.png" alt-text="Wählen Sie alle Regionen und dann Regionen verwalten aus, um die Regionen in Ihrem Unternehmen zu verwalten.":::

   1. Geben Sie den neuen Regionsnamen ein, und wählen Sie **HINZUFÜGEN** aus.

1. Fügen Sie einen Standort hinzu.

   1. Wählen Sie in der Unternehmensansicht in der oberen Leiste :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: aus. Der Cursor wird als Pluszeichen ( **+** ) angezeigt.

   1. Positionieren Sie das **+** auf dem neuen Standort, und wählen Sie ihn aus. Das Dialogfeld **Neuen Standort erstellen** wird geöffnet.

      :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Screenshot der Ansicht „Neuen Standort erstellen“":::

   1. Definieren Sie den Namen und die physische Adresse für den neuen Standort, und wählen Sie **SPEICHERN** aus. Der neue Standort wird in der Standortkarte angezeigt.

4. [Fügen Sie einem Standort Zonen hinzu](#create-enterprise-zones).

5. [Verbinden Sie die Sensoren](how-to-manage-individual-sensors.md#connect-a-sensor-to-the-management-console).

6. [Weisen Sie einen Sensor Standortzonen zu](#assign-sensors-to-zones).

### <a name="delete-a-site"></a>Löschen einer Website

Wenn Sie einen Standort nicht mehr benötigen, können Sie ihn über die lokale Verwaltungskonsole löschen.

**So löschen Sie einen Standort:**

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone löschen** aus. Das Bestätigungsfeld wird angezeigt, und es wird überprüft, ob Sie den Standort löschen möchten.

2. Wählen Sie im Bestätigungsfeld **BESTÄTIGEN** aus.

## <a name="create-enterprise-zones"></a>Erstellen von Unternehmenszonen

Zonen sind logische Entitäten, mit denen Sie Geräte innerhalb eines Standorts entsprechend verschiedenen Merkmalen in Gruppen aufteilen können. So können Sie beispielsweise Gruppen für Produktionslinien, Unterstationen, Standortbereiche oder Gerätetypen erstellen. Sie können Zonen basierend auf allen Merkmalen definieren, die für Ihre Organisation geeignet sind.

Sie konfigurieren Zonen im Rahmen des Standortkonfigurationsprozesses.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Screenshot der Ansicht „Standortverwaltungszonen“":::

In der folgenden Tabelle werden die Parameter im Fenster **Standortverwaltung** beschrieben.

| Parameter | Beschreibung |
|--|--|
| Name | Der Name des Sensors. Sie können diesen Namen nur über den Sensor ändern. Weitere Informationen finden Sie im Benutzerhandbuch zu Defender für IoT. |
| IP | Die IP-Adresse des Sensors. |
| Version | Die Sensorversion. |
| Konnektivität | Der Konnektivitätsstatus des Sensors. Der Status kann **Verbunden** oder **Getrennt** lauten. |
| Letztes Upgrade | Das Datum des letzten Upgrades. |
| Upgradestatus | Die Statusanzeige zeigt den Status des Upgradeprozesses so an:<br />– Paket wird hochgeladen.<br />– Installation wird vorbereitet.<br />– Prozesse werden angehalten.<br />– Daten werden gesichert.<br />– Momentaufnahme wird erstellt.<br />– Konfiguration wird aktualisiert.<br />– Abhängigkeiten werden aktualisiert.<br />– Bibliotheken werden aktualisiert.<br />– Datenbanken werden gepatcht.<br />– Prozesse werden gestartet.<br />– Systemintegrität wird überprüft.<br />– Überprüfung war erfolgreich.<br />– Erfolg<br />– Fehler<br />– Upgrade wurde gestartet.<br />– Installation wird gestartet.ogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Falls Sie Informationen zum Upgrade benötigen, bitten Sie den [Microsoft-Support](https://support.microsoft.com/) um Hilfe. |
| Geräte | Die Anzahl von OT-Geräten, die der Sensor überwacht |
| Alerts | Die Anzahl von Warnungen auf dem Sensor. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Ermöglicht das Zuweisen eines Sensors zu Zonen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Ermöglicht das Löschen eines nicht verbundenen Sensors aus dem Standort. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Gibt an, wie viele Sensoren zurzeit mit der Zone verbunden sind. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Gibt an, wie viele OT-Ressourcen zurzeit mit der Zone verbunden sind. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Gibt die Anzahl der Warnungen an, die von der Zone zugewiesenen Sensoren gesendet werden. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Hebt die Zuweisung von Sensoren zu Zonen auf. |

**So fügen Sie eine Zone zu einem Standort hinzu:**

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone hinzufügen** aus. Das Dialogfeld **Neue Zone erstellen** wird angezeigt.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Screenshot der Ansicht „Neue Zone erstellen“":::

1. Geben Sie den Zonennamen ein.

1. Geben Sie eine Beschreibung für die neue Zone ein, in der die Merkmale, mit denen Sie den Standort in Zonen aufgeteilt haben, eindeutig angegeben werden.

1. Wählen Sie **SAVE** (SPEICHERN) aus. Die neue Zone wird im Fenster **Standortverwaltung** unter dem Standort angezeigt, zu dem diese Zone gehört.

**So bearbeiten Sie eine Zone:**

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone bearbeiten** aus. Das Dialogfeld **Zone bearbeiten** wird angezeigt.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Screenshot des Dialogfelds „Zone bearbeiten“":::

1. Bearbeiten Sie die Zonenparameter, und wählen Sie **SPEICHERN** aus.

**So löschen Sie eine Zone**

1. Wählen Sie im Fenster **Standortverwaltung** in der Leiste, die den Zonennamen enthält, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: und dann **Zone löschen** aus.

1. Wählen Sie im Bestätigungsfeld **JA** aus.

**So filtern Sie nach dem Konnektivitätsstatus:**

- Wählen Sie in der oberen linken Ecke neben **Konnektivität** :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: und dann eine der folgenden Optionen aus:

  - **All**: Zeigt alle Sensoren an, die an diese lokale Verwaltungskonsole berichten.

  - **Verbunden**: Zeigt nur verbundene Sensoren an.

  - **Getrennt**: Zeigt nur getrennte Sensoren an.

**So filtern Sie nach dem Upgradestatus:**

- Wählen Sie in der oberen linken Ecke neben **Upgradestatus** :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: und dann eine der folgenden Optionen aus:

  - **All**: Zeigt alle Sensoren an, die an diese lokale Verwaltungskonsole berichten.

  - **Gültig**: Zeigt Sensoren mit einem gültigen Upgradestatus an.

  - **In Bearbeitung**: Zeigt Sensoren an, die gerade aktualisiert werden.

  - **Fehler:** Zeigt Sensoren an, deren Upgradeprozess fehlgeschlagen ist.

## <a name="assign-sensors-to-zones"></a>Zuweisen von Sensoren zu Zonen

Sie müssen für jede Zone Sensoren zuweisen, die eine lokale Datenverkehrsanalyse durchführen und Warnungen ausgeben. Sie können nur die Sensoren zuweisen, die mit der lokalen Verwaltungskonsole verbunden sind.

**So weisen Sie einen Sensor zu:**

1. Wählen Sie **Standortverwaltung** aus. Die nicht zugewiesenen Sensoren werden in der oberen linken Ecke des Dialogfelds angezeigt.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Screenshot der Ansicht „Nicht zugewiesene Sensoren“":::

1. Überprüfen Sie, ob der Status für **Konnektivität** „Verbunden“ lautet. Falls das nicht zutrifft, finden Sie Informationen zum Herstellen einer Verbindung unter [Verbinden von Sensoren mit der lokalen Verwaltungskonsole](#connect-sensors-to-the-on-premises-management-console). 

1. Wählen Sie :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: für den Sensor aus, den Sie zuweisen möchten.

1. Wählen Sie im Dialogfeld **Sensor zuweisen** die Geschäftseinheit, die Region, den Standort und die Zone aus, die zugewiesen werden sollen.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Screenshot der Ansicht „Sensor zuweisen“":::

1. Wählen Sie **ZUWEISEN** aus.

**So heben Sie die Zuweisung eines Sensors auf und löschen ihn:**

1. Trennen Sie die Verbindung des Sensors mit der lokalen Verwaltungskonsole. Informationen dazu finden Sie unter [Verbinden von Sensoren mit der lokalen Verwaltungskonsole](#connect-sensors-to-the-on-premises-management-console).

1. Wählen Sie im Fenster **Standortverwaltung** den gewünschten Sensor und dann :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: aus. Nach kurzer Zeit wird der Sensor in der Liste der nicht zugewiesenen Sensoren angezeigt.

1. Zum Löschen des nicht zugewiesenen Sensors aus dem Standort wählen Sie ihn aus der Liste der nicht zugewiesenen Sensoren und dann :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: aus.

## <a name="see-also"></a>Weitere Informationen

[Behandeln von Problemen mit dem Sensor und der lokalen Verwaltungskonsole](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
