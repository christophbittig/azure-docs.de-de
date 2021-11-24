---
title: Einrichten einer Azure Migrate-Appliance für die Serverbewertung in einer VMware-Umgebung
description: In diesem Artikel erfahren Sie, wie Sie eine Azure Migrate-Appliance einrichten, um Server in VMware-Umgebungen zu bewerten und zu migrieren.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: c95c92c1f592cc5f042975e832df66e585d99740
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402483"
---
# <a name="set-up-an-appliance-for-servers-in-a-vmware-environment"></a>Einrichten einer Appliance für Server in einer VMware-Umgebung

In diesem Artikel wird beschrieben, wie Sie die Azure Migrate-Appliance für die Bewertung mit dem Tool [Azure Migrate: Ermittlung und Bewertung](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) einrichten.

Die [Azure Migrate-Appliance](migrate-appliance.md) ist eine unkomplizierte Appliance, die vom Tool „Azure Migrate: Ermittlung und Bewertung“ verwendet wird, um Server zu ermitteln, die in vCenter Server ausgeführt werden, und Serverkonfigurations- und Leistungsmetadaten an Azure zu senden.

## <a name="set-up-the-appliance"></a>Einrichten der Appliance

Sie können die Azure Migrate-Appliance mithilfe verschiedener Methoden bereitstellen:

- Erstellen Sie einen Server auf einer vCenter Server-VM mithilfe einer heruntergeladenen OVA-Vorlage. Diese Methode wird in diesem Artikel beschrieben.
- Richten Sie die Appliance mithilfe eines PowerShell-Installationsskripts auf einem vorhandenen Server ein. Sie sollten [ein PowerShell-Skript ausführen](deploy-appliance-script.md), wenn Sie keine OVA-Vorlage verwenden können oder wenn Sie in Azure Government arbeiten.

Nachdem Sie die Appliance erstellt haben, prüfen Sie, ob sie eine Verbindung mit Azure Migrate: Erkennung und Bewertung herstellen kann. Registrieren Sie die Appliance dann beim Projekt, und konfigurieren Sie die Appliance, um die Erkennung zu starten.

### <a name="deploy-by-using-an-ova-template"></a>Bereitstellen mithilfe einer OVA-Vorlage

Um die Appliance mithilfe einer OVA-Vorlage einzurichten, führen Sie die folgenden Schritte aus, die in diesem Abschnitt ausführlicher beschrieben werden:

1. Geben Sie einen Appliancenamen ein, und generieren Sie einen Projektschlüssel im Portal.
1. Laden Sie eine OVA-Vorlagendatei herunter und importieren Sie die Datei in vCenter Server. Vergewissern Sie sich, dass die OVA sicher ist.
1. Erstellen Sie die Appliance aus der OVA-Datei. Überprüfen Sie, ob die Appliance eine Verbindung mit Azure Migrate herstellen kann.
1. Führen Sie die Konfiguration der Appliance durch. 
1. Registrieren Sie die Appliance mithilfe des Projektschlüssels beim Projekt.

#### <a name="generate-the-project-key"></a>Generieren des Projektschlüssels

1. Wählen Sie unter **Migrationsziele** die Option **Server** > **Azure Migrate: Ermittlung und Bewertung** > **Ermitteln** aus.
1. Wählen Sie unter **Server ermitteln** die Option **Sind Ihre Server virtualisiert?** aus > **Ja, mit VMware vSphere-Hypervisor**.
1. Geben Sie unter **1: Projektschlüssel generieren** einen Namen für die Azure Migrate-Appliance ein, die Sie für die Ermittlung von Servern in der VMware-Umgebung einrichten. Für den Namen können bis zu 14 alphanumerische Zeichen angegeben werden.
1. Um mit dem Erstellen der erforderlichen Azure-Ressourcen zu beginnen, wählen Sie **Schlüssel generieren** aus. Schließen Sie den Bereich **Entdecken** nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein *Projektschlüssel* generiert.
1. Kopieren Sie den Schlüssel. Sie verwenden den Schlüssel zum Abschließen der Registrierung der Appliance, wenn Sie die Appliance konfigurieren.

#### <a name="download-the-ova-template"></a>Herunterladen der OVA-Vorlage

Wählen Sie in **2: Azure Migrate-Appliance herunterladen** die OVA-Datei aus, und klicken Sie auf **Herunterladen**.

##### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor dem Bereitstellen der OVA-Datei, dass die Datei sicher ist:

1. Öffnen Sie auf dem Server, auf dem Sie die Datei heruntergeladen haben, mithilfe der Option **Als Administrator ausführen** ein Eingabeaufforderungsfenster.
1. Führen Sie den folgenden Befehl aus, um den Hash für die OVA-Datei zu generieren:
  
    ```bash
    C:\>CertUtil -HashFile <file_location> <hashing_agorithm>
    ```
   
    Ein Beispiel: `C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256`

1. Überprüfen Sie den neuesten Hashwert, indem Sie das Ergebnis des obigen Befehls mit dem [hier](/azure/migrate/tutorial-discover-vmware#verify-security)dokumentierten Wert vergleichen.

#### <a name="create-the-appliance-server"></a>Erstellen des Servers auf der Appliance

Importieren Sie die heruntergeladene Datei, und erstellen Sie dann in der VMware-Umgebung einen Server:

1. Klicken Sie in der vSphere-Clientkonsole auf **Datei** > **OVF-Vorlage bereitstellen**.
1. Wählen sie im Assistenten für die Bereitstellung der OVF-Vorlage die Option **Quelle** und geben dann den Speicherort der OVA-Datei an.
1. Unter **Name**: Geben Sie einen Namen für die Notiz ein. Unter **Ort**: Wählen Sie das Bestandsobjekt aus, in dem der Server gehostet wird.
1. Unter **Host/Cluster**: Wählen Sie den Host oder Cluster, auf bzw. in dem der Server ausgeführt wird.
1. Unter **Speicher**: Geben Sie das Speicherziel des Servers an.
1. Unter **Datenträgerformat**: Geben Sie den Typ und die Größe des Datenträgers an.
1. Unter **Netzwerkzuordnung**: Geben Sie das Netzwerk an, mit dem der Server eine Verbindung herstellt. Das Netzwerk erfordert Internetkonnektivität, um Metadaten an Azure Migrate zu senden.
1. Überprüfen und bestätigen Sie die Einstellungen, und klicken Sie auf **Fertig stellen**.

#### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass der Server auf der Appliance eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) und [Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

<a name="4-configure-the-appliance"></a>

### <a name="configure-the-appliance"></a>Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch:

> [!NOTE]
> Wenn Sie die Appliance mithilfe eines [PowerShell-Skripts](deploy-appliance-script.md) und nicht mithilfe der heruntergeladenen OVA-Datei einrichten, können Sie die ersten beiden Schritte in diesem Verfahren überspringen.

1. Klicken Sie im vSphere-Client mit der rechten Maustaste auf den Server, und wählen Sie **Konsole öffnen** aus.
1. Wählen oder geben Sie die Sprache, die Zeitzone und das Kennwort für die Appliance an.
1. Öffnen Sie einen Browser auf einem beliebigen Server, der eine Verbindung mit dem Applianceserver herstellen kann. Öffnen Sie dann die URL des Appliance-Konfigurationsmanagers: `https://appliance name or IP address: 44368`.

     Sie können den Konfigurationsmanager auch auf dem Desktop des Servers auf der Appliance öffnen, indem Sie die Verknüpfung für den Konfigurations-Manager auswählen.
1. Akzeptieren Sie die Lizenzbedingungen und lesen Sie die Drittanbieterinformationen.
1. Wählen Sie im Konfigurationsmanager **die Option Voraussetzungen einrichten** aus und führen Sie dann die folgenden Schritte aus:
    1. **Konnektivität**: Die Appliance überprüft, ob der Server über Internetzugriff verfügt. Wenn der Server einen Proxy verwendet:
        1. Wählen **Sie Proxy einrichten** aus, um die Proxyadresse (im Formular `http://ProxyIPAddress` oder `http://ProxyFQDN`, wobei der *FQDN* auf einen *vollqualifizierten Domänennamen* verweist) und den Lauschport anzugeben.
        1.  Geben Sie die Anmeldeinformationen ein, wenn der Proxy eine Authentifizierung erfordert.
        1. Wenn Sie Proxydetails hinzugefügt oder den Proxy oder die Authentifizierung deaktiviert haben, wählen Sie **Speichern**, um die Konnektivitätsprüfung auszulösen und erneut zu überprüfen.

            Es werden nur HTTP-Proxys unterstützt.
    1. **Uhrzeitsynchronisierung**: Überprüfen Sie, ob die Uhrzeit der Appliance mit der Internetzeit synchronisiert ist, damit die Ermittlung ordnungsgemäß funktioniert.
    1. **Updates installieren**: Die Appliance stellt sicher, dass die neuesten Updates installiert sind. Wenn die Überprüfung fertig ist, können Sie **Appliancedienste anzeigen** auswählen, um den Status und die Versionen der auf dem Applianceserver ausgeführten Dienste anzuzeigen.
    1. **Install the VDDK** (VDDK installieren): Die Appliance überprüft, ob das VMware vSphere-VDDK (Virtual Disk Development Kit) installiert ist. Wenn das VDDK nicht installiert ist, laden Sie VDDK 6.7 von VMware herunter. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei am angegebenen Ort auf der Appliance, wie unter *Installationsanweisungen* beschrieben.

        Bei der Servermigration mit Azure Migrate wird das VDDK zur Replikation von Servern während der Migration zu Azure verwendet. 
1. Während der Konfiguration der Appliance können Sie jederzeit die *Voraussetzungen erneut ausführen*, um zu prüfen, ob die Appliance alle Voraussetzungen erfüllt:

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Screenshot: Einrichten der erforderlichen Komponenten im Appliance-Konfigurationsmanager.":::

#### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten Projektschlüssel ein. Wenn Sie nicht über den Schlüssel verfügen, wechseln Sie zu **Ermittlung und Bewertung**  > **Ermitteln** > **Vorhandene Appliances verwalten**. Wählen Sie den Appliancenamen aus, den Sie beim Erstellen des Projektschlüssels angegeben haben, und kopieren Sie dann den angezeigten Schlüssel.
1. Für die Authentifizierung bei Azure benötigen Sie einen Gerätecode. Wählen Sie **Anmelden**. Bei **Forfahren mit Azure Login** wählen Sie **Code kopieren und anmelden** aus, um den Gerätecode zu kopieren und eine Azure-Anmeldeaufforderung auf einer neuen Browserregisterkarte zu öffnen. Vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben, um die Eingabeaufforderung zu sehen.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Screenshot: Kopieren des Gerätecodes und Login.":::

1. Fügen Sie auf einer neuen Registerkarte in Ihrem Browser den Gerätecode ein, und melden Sie sich mit Ihrem Azure-Benutzernamen und dem zugehörigen Kennwort an. Die Anmeldung mit einer PIN wird nicht unterstützt.

    Wenn Sie die Registerkarte für die Anmeldung versehentlich schließen, ohne die Anmeldung durchzuführen, aktualisieren Sie die Browserregisterkarte des Appliance-Konfigurations-Managers, um den Gerätecode und die Schaltfläche **Code kopieren und anmelden** wieder anzuzeigen.
1. Kehren Sie nach erfolgreicher Anmeldung zur Browserregisterkarte zurück, auf der der Appliance-Konfigurationsmanager angezeigt wird. Wenn das Azure-Benutzerkonto, mit dem Sie sich angemeldet haben, über die erforderlichen Berechtigungen für die Azure-Ressourcen verfügt, die während der Schlüsselgenerierung erstellt wurden, wird die Applianceregistrierung gestartet.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie sich die Registrierungsdetails anzeigen lassen, indem Sie auf **Details anzeigen** klicken.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Screenshot des Bereichs „Bei Azure Migrate registrieren“, der zeigt, dass die Appliance erfolgreich registriert wurde.":::

## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Führen Sie die Setupschritte im Appliance-Konfigurationsmanager aus, um die Ermittlung vorzubereiten und zu starten.

### <a name="provide-vcenter-server-details"></a>Angeben von vCenter Server-Details

Die Appliance muss eine Verbindung mit der vCenter Server-Instanz herstellen, um die Konfigurations- und Leistungsdaten der Server zu ermitteln:

1. Wählen Sie unter **Schritt 1: Geben Sie vCenter Server Anmeldeinformationen an** aus und wählen Sie **Anmeldeinformationen hinzufügen** aus, um einen Namen für die Anmeldeinformationen einzugeben. Fügen Sie den Benutzernamen und das Kennwort für das vCenter Server-Konto hinzu, mit dem die Appliance Server ermittelt, die auf vCenter Server ausgeführt werden.
    - Sie sollten ein Konto mit den erforderlichen Berechtigungen festgelegt haben, wie weiter oben in diesem Artikel beschrieben.
    - Wenn Sie die Erkennung auf bestimmte VMware-Objekte (vCenter Server-Rechenzentren, Cluster, Hosts, Ordner von Clustern oder Hosts oder einzelne Server) beschränken möchten, lesen Sie die Anweisungen zum [Festlegen des Erkennungsbereichs](set-discovery-scope.md), um das von Azure Migrate verwendete Konto einzuschränken.
1. Unter **Schritt 2: Bereitstellen von vCenter Server-Details** wählen Sie **Erkennungsquelle hinzufügen**, um den Namen für die Anmeldeinformationen aus der Dropdown-Liste auszuwählen. Wählen Sie die IP-Adresse oder den FQDN des vCenter Server aus. Sie können den Port als Standard (443) beibehalten oder einen benutzerdefinierten Port angeben, an dem vCenter Server lauscht. Wählen Sie **Speichern** aus.
1. Die Appliance versucht, die Verbindung mit dem Server, auf dem vCenter Server ausgeführt wird, mithilfe der Anmeldeinformationen zu überprüfen. Der Überprüfungsstatus für die vCenter Server IP-Adresse oder den FQDN wird in der Tabelle mit den Anmeldeinformationen angezeigt.
1. Sie können die Verbindung zu vCenter Server jederzeit vor Beginn der Ermittlung *erneut überprüfen*.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Screenshot: Verwalten von Anmeldeinformationen und Ermittlungsquellen für vCenter Server im Appliance-Konfigurationsmanager.":::

### <a name="provide-server-credentials"></a>Angeben von Serveranmeldeinformation

In diesem Schritt können Sie mehrere Serveranmeldeinformationen angeben: **Schritt 3: Geben Sie Anmeldeinformationen für den Server an, um eine Softwareinventur, eine Abhängigkeitsanalyse ohne Agent, eine Ermittlung von SQL Server-Instanzen und -Datenbanken und eine Ermittlung von ASP.NET-Web-Apps in Ihrer VMware-Umgebung durchzuführen**. Wenn Sie keine dieser Appliancefunktionen verwenden möchten, können Sie diesen Schritt überspringen und mit der vCenter Server-Ermittlung fortfahren. Sie können diese Option jederzeit ändern.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Screenshot: Bereitstellen von Anmeldeinformationen für die Softwareinventur, Abhängigkeitsanalyse und s q l Serverermittlung.":::

Wenn Sie diese Features nutzen möchten, stellen Sie die Serveranmeldeinformationen bereit, indem Sie die folgenden Schritte ausführen. Die Appliance versucht, die Anmeldeinformationen automatisch den Servern zuzuordnen, um die Ermittlungsfeatures auszuführen.

Hinzufügen von Serveranmeldeinformationen:

1. Wählen Sie **Add Credentials** (Anmeldeinformationen hinzufügen) aus.
1. Wählen Sie im Dropdown-Menü **Anmeldeinformationstyp** aus.
    
    Sie können Anmeldeinformationen für die Authentifizierung für eine Domäne/, Windows (ohne Domäne)/, Linux (ohne Domäne)/ und SQL Server angeben. Erfahren Sie mehr über die [Bereitstellung von Anmeldeinformationen](add-server-credentials.md) und zu deren Handhabung.
1. Geben Sie für jeden Anmeldeinformationstyp Folgendes ein:
    * Einen Anzeigenamen.
    * Einen Benutzernamen.
    * Ein Kennwort.
    Wählen Sie **Speichern** aus.

    Wenn Sie Domänenanmeldeinformationen zu Verwendung wählen, müssen Sie auch den FQDN für die Domäne eingeben. Der FQDN wird benötigt, um die Echtheit der Anmeldeinformationen mithilfe der Active Directory-Instanz der jeweiligen Domäne zu bestätigen.
1. Überprüfen Sie die für das Konto [erforderlichen Berechtigungen](add-server-credentials.md#required-permissions) für „Schritt 3: Geben Sie Anmeldeinformationen für den Server an, um eine Softwareinventur, eine Abhängigkeitsanalyse ohne Agent, eine Ermittlung von SQL Server-Instanzen und -Datenbanken und eine Ermittlung von ASP.NET-Web-Apps durchzuführen.“
1. Um mehrere Anmeldeinformationen gleichzeitig hinzuzufügen, wählen Sie **Weitere hinzufügen**, um die Anmeldeinformationen zu speichern und dann weitere Anmeldeinformationen hinzuzufügen.
    Wenn Sie **Speichern** oder **Weitere hinzufügen** auswählen, überprüft die Appliance die Domänenanmeldeinformationen mit dem Active Directory-Instanz der Domäne auf Authentifizierung. Die Überprüfung erfolgt nach jeder Addition, um Kontosperren zu vermeiden, während die Appliance die Zuordnung von Anmeldeinformationen zu den jeweiligen Servern durchlauft.

So überprüfen Sie die Überprüfung der Domänenanmeldeinformationen:

Sehen Sie sich im Konfigurationsmanager in der Tabelle mit den Anmeldeinformationen den **Überprüfungsstatus** für Domänenanmeldeinformationen an. Nur Domänenanmeldeinformationen werden überprüft.

Wenn die Überprüfung fehlschlägt, können Sie den Status **Fehler** auswählen, um den Validierungsfehler anzuzeigen. Beheben Sie das Problem und wählen Sie dann **Anmeldeinformationen erneut überprüfen** aus, um die Überprüfung der Anmeldeinformationen erneut zu überprüfen.

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Screenshot: Bereitstellen und Überprüfen mehrerer Anmeldeinformationen.":::

### <a name="start-discovery"></a>Ermittlung starten

Zum Starten der vCenter Server-Ermittlung wählen Sie in **Schritt 3: Geben Sie Anmeldeinformationen für den Server an, um eine Softwareinventur, eine Abhängigkeitsanalyse ohne Agent, eine Ermittlung von SQL Server-Instanzen und -Datenbanken und eine Ermittlung von ASP.NET-Web-Apps in Ihrer VMware-Umgebung durchzuführen.** die Option **Ermittlung starten** aus. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus anhand von IP-Adresse oder FQDN von vCenter Server in der Tabelle mit den Quellen überprüfen.

## <a name="how-discovery-works"></a>Wie die Ermittlung funktioniert

* Die Inventur benötigt ca. 15 Minuten, bis Metadaten des ermittelten Servers im Azure-Portal angezeigt werden.
* Wenn Sie Serveranmeldeinformationen angegeben haben, wird die Software-Inventur (Ermittlung installierter Anwendungen) automatisch eingeleitet, wenn die vCenter Server-Ermittlung abgeschlossen wurde. Die Software-Inventur erfolgt einmal alle 12 Stunden.
* [Die Softwareinventur](how-to-discover-applications.md) identifiziert SQL Server-Instanzen, die auf den Servern ausgeführt werden. Anhand dieser gesammelten Informationen versucht die Appliance, mithilfe der in der Appliance angegebenen Anmeldeinformationen für Windows-Authentifizierung oder SQL Server-Authentifizierung eine Verbindung mit den entsprechenden SQL Server-Instanzen herzustellen. Anschließend werden Daten zu SQL Server-Datenbanken und deren Eigenschaften erfasst. Die SQL Server-Ermittlung erfolgt einmal alle 24 Stunden.
* Über [Softwareinventur](how-to-discover-applications.md) wird die Webserverrolle für die Server identifiziert. Unter Verwendung der gesammelten Informationen versucht die Appliance, über die für die Appliance angegebenen Anmeldeinformationen für die Windows-Authentifizierung eine Verbindung mit dem IIS-Webserver herzustellen. Anschließend werden Daten zu Web-Apps gesammelt. Die Web-App-Ermittlung erfolgt einmal alle 24 Stunden.
* Die Ermittlung installierter Anwendungen kann länger als 15 Minuten dauern. Die Dauer hängt von der Anzahl der ermittelten Server ab. Bei 500 Servern dauert es ungefähr ein Stunde, bis der ermittelte Bestand im Azure Migrate-Portal angezeigt wird.
* Bei der Softwareinventur werden die hinzugefügten Serveranmeldeinformationen mit Servern abgeglichen und für die Abhängigkeitsanalyse ohne Agent überprüft. Wenn die Ermittlung von Servern abgeschlossen ist, können Sie im Portal die Abhängigkeitsanalyse ohne Agent auf den Servern aktivieren. Nur die Server mit erfolgreicher Überprüfung können ausgewählt werden, um die Abhängigkeitsanalyse ohne Agent zu aktivieren.
* Die Daten für SQL Server-Instanzen und -Datenbanken sowie Web-App-Daten werden innerhalb von 24 Stunden nach Beginn der Ermittlung im Portal angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Tutorials zur [VMware-Bewertung](./tutorial-assess-vmware-azure-vm.md) und zur [Migration ohne Agent](tutorial-migrate-vmware.md).
