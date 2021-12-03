---
title: Erstellen und Verwalten von Integration Runtimes
description: In diesem Artikel werden die Schritte zum Erstellen und Verwalten von Integration Runtimes in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 10/22/2021
ms.openlocfilehash: 10e893886d98fc5ea6d79bf8092cc5b0d948d84a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309716"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Erstellen und Verwalten einer selbstgehosteten Integration Runtime

In diesem Artikel wird beschrieben, wie Sie eine selbstgehostete Integration Runtime (SHIR) erstellen und verwalten, um Datenquellen in Azure Purview zu überprüfen.

> [!NOTE]
> Die Purview-Integration Runtime kann nicht zusammen mit einer Azure Synapse Analytics- oder Azure Data Factory-Integration Runtime auf demselben Computer verwendet werden. Sie muss auf einem separaten Computer installiert werden.

## <a name="prerequisites"></a>Voraussetzungen

- Die unterstützten Versionen von Windows lauten:
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

Die Installation der selbstgehosteten Integration Runtime auf einem Domänencontroller wird nicht unterstützt.

- Die selbstgehostete Integration Runtime erfordert ein 64-Bit-Betriebssystem mit .NET Framework 4.7.2 oder höher. Ausführlichere Informationen finden Sie unter [Systemanforderungen für .NET Framework](/dotnet/framework/get-started/system-requirements) .
- Die empfohlene Mindestkonfiguration für den Computer mit der selbstgehosteten Integration Runtime ist ein 2-GHz-Prozessor mit 4 Kernen, 8 GB RAM und 80 GB verfügbarem Speicherplatz auf dem Datenträger. Details zu den Systemanforderungen finden Sie auf der [Downloadseite](https://www.microsoft.com/download/details.aspx?id=39717).
- Wenn sich der Hostcomputer im Ruhezustand befindet, reagiert die selbstgehostete Integration Runtime nicht auf Datenanforderungen. Konfigurieren Sie vor der Installation der selbstgehosteten Integration Runtime einen entsprechenden Energiesparplan auf dem Computer. Wenn für den Computer der Ruhezustand konfiguriert ist, wird im Installationsprogramm für die selbstgehostete Integration Runtime eine Meldung angezeigt.
- Sie müssen der Administrator des Computers sein, um die selbstgehostete Integration Runtime erfolgreich installieren und konfigurieren zu können.
- Überprüfungen erfolgen in einer bestimmten Häufigkeit gemäß dem von Ihnen festgelegten Zeitplan. Die Prozessor- und RAM-Nutzung auf dem Computer folgt dem gleichen Muster mit Spitzen- und Leerlaufzeiten. Außerdem ist die Ressourcenverwendung auch stark von der Menge der Daten abhängig, die überprüft werden. Wenn mehrere Überprüfungsaufträge gleichzeitig ausgeführt werden, steigt der Ressourcenverbrauch zu Spitzenzeiten an.
- Unter Umständen tritt für Aufgaben während der Extraktion von Daten in den Formaten Parquet, ORC oder Avro ein Fehler auf.

> [!IMPORTANT]
> Wenn Sie die Self-Hosted Integration Runtime zum Scannen von Parquet-Dateien verwenden möchten, müssen Sie die **64-bit JRE 8 (Java Runtime Environment) oder OpenJDK** auf Ihrem IR-Rechner installieren. Eine Installationsanleitung finden Sie in unserem Abschnitt [Java Runtime Environment am Ende der Seite](#java-runtime-environment-installation).

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Einrichten einer selbstgehosteten Integration Runtime

Verwenden Sie die folgenden Verfahren, um eine selbstgehostete Integration Runtime zu erstellen und einzurichten.

## <a name="create-a-self-hosted-integration-runtime"></a>Erstellen einer selbstgehosteten Integration Runtime

1. Wählen Sie auf der Homepage von [Purview Studio](https://web.purview.azure.com/resource/) im linken Navigationsbereich die Option **Data Map** aus.

2. Wählen Sie im linken Bereich unter **Sources and scanning** (Quellen und Überprüfung) die Option **Integration Runtimes** und dann **+ Neu** aus.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Auswahl von Integration Runtimes.":::

3. Wählen Sie auf der Seite **Integration Runtime-Setup** die Option **Selbstgehostet** aus, um eine selbstgehostete IR zu erstellen, und wählen Sie dann **Weiter** aus.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Erstellen einer neuen SHIR":::

4. Geben Sie einen Namen für Ihre IR ein, und wählen Sie Erstellen aus.

5. Führen Sie auf der Seite **Integration Runtime settings** (Integration Runtime-Einstellungen) die Schritte im Abschnitt **Manual setup** (Manuelles Setup) aus. Sie müssen die Integration Runtime von der Downloadwebsite auf einen virtuellen oder physischen Computer herunterladen, den Sie für die Ausführung verwenden möchten.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Abrufen des Schlüssels":::

   - Kopieren Sie den Authentifizierungsschlüssel, und fügen Sie ihn ein.

   - Laden Sie die selbstgehostete Integration Runtime von [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) auf einen lokalen Windows-Computer herunter. Führen Sie das Installationsprogramm aus. Selbstgehostete Integration Runtime-Versionen wie 5.4.7803.1 und 5.6.7795.1 werden unterstützt. 

   - Fügen Sie auf der Seite **Integration Runtime (selbstgehostet) registrieren** einen der beiden Schlüssel ein, die Sie zuvor gespeichert haben, und wählen Sie **Registrieren** aus.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Einfügen des Schlüssels":::

   - Klicken Sie auf der Seite **Neuer Knoten der Integrationslaufzeit (selbstgehostet)** auf **Fertig stellen**.

6. Nachdem die selbstgehostete Integration Runtime erfolgreich registriert wurde, wird das folgende Fenster angezeigt:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Erfolgreiche Registrierung":::

### <a name="configure-proxy-server-settings"></a>Konfigurieren von Proxyservereinstellungen

Wenn Sie die Option **Systemproxy verwenden** für den HTTP-Proxy auswählen, verwendet die selbstgehostete Integration Runtime die Proxyeinstellungen in „diahost.exe.config“ und „diawp.exe.config“. Falls für diese Dateien kein Proxy angegeben wird, stellt die selbstgehostete Integration Runtime eine direkte Verbindung mit dem Clouddienst her, ohne einen Proxy zu durchlaufen. Das folgende Verfahren enthält Anweisungen für die Aktualisierung der Datei „diahost.exe.config“:

1. Erstellen Sie im Datei-Explorer eine sichere Kopie von C:\Programme\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config als Sicherung der Originaldatei.
1. Öffnen Sie den Editor als Administrator.
1. Öffnen Sie in Notepad die Textdatei C:\Programme\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config.
1. Suchen Sie wie im folgenden Code gezeigt nach dem Standardtag **system.net**:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Anschließend können Sie die Informationen zum Proxyserver wie im folgenden Beispiel gezeigt hinzufügen:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Mit dem Proxytag können zusätzliche Eigenschaften verwendet werden, um erforderliche Einstellungen wie `scriptLocation` anzugeben. Informationen zur Syntax finden Sie unter dem [\<proxy\>-Element (Netzwerkeinstellungen)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings).

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. Speichern Sie die Konfigurationsdatei am ursprünglichen Speicherort. Starten Sie dann den Hostdienst für die selbstgehostete Integration Runtime, der die Änderungen übernimmt.

   Starten Sie den Dienst mit dem Dienste-Applet in der Systemsteuerung neu. Alternativ wählen Sie im Konfigurations-Manager für Integration Runtime die Schaltfläche **Dienst beenden** und dann **Dienst starten** aus.

   Wenn der Dienst nicht gestartet wird, haben Sie wahrscheinlich in der von Ihnen bearbeiteten Konfigurationsdatei eine fehlerhafte XML-Tagsyntax hinzugefügt.

> [!IMPORTANT]
> Vergessen Sie nicht, beide Dateien – „diahost.exe.config“ und „diawp.exe.config“ – zu aktualisieren.

Sie müssen auch sicherstellen, dass Microsoft Azure in der Positivliste Ihres Unternehmens enthalten ist. Sie können die Liste mit den gültigen Azure-IP-Adressen herunterladen. IP-Adressbereiche für jede Cloud, unterteilt nach Region und den markierten Diensten in dieser Cloud, sind jetzt unter MS Download verfügbar: 
   - Öffentlich: https://www.microsoft.com/download/details.aspx?id=56519

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Mögliche Symptome für Probleme im Zusammenhang mit der Firewall und dem Proxyserver

Falls Fehlermeldungen wie in der Liste unten angezeigt werden, ist die Ursache wahrscheinlich eine falsche Konfiguration der Firewall oder des Proxyservers. Eine solche Konfiguration verhindert, dass die selbst gehostete Integrationslaufzeit eine Verbindung zu verwalteten Azure-Speicherkonten oder Datenquellen herstellen kann. Um sicherzustellen, dass die Firewall und der Proxyserver richtig konfiguriert sind, überprüfen Sie den vorherigen Abschnitt.

- Wenn Sie versuchen, die selbstgehostete Integration Runtime zu registrieren, erhalten Sie die folgende Fehlermeldung: „Fehler beim Registrieren dieses Knotens der Integrationslaufzeit. Stellen Sie sicher, dass der Authentifizierungsschlüssel gültig ist und der Hostdienst des Integrationsdiensts auf diesem Computer ausgeführt wird.“
- Wenn Sie den Konfigurations-Manager für die Integration Runtime öffnen, wird der Status als **Getrennt** oder **Verbindung wird hergestellt** angezeigt. Beim Anzeigen der Windows-Ereignisprotokolle sehen Sie unter **Ereignisanzeige** > **Anwendungs- und Dienstprotokolle** > **Microsoft Integration Runtime** beispielsweise folgende Fehlermeldung:

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

## <a name="networking-requirements"></a>Netzwerkanforderungen

Ihr selbstgehosteter Integration Runtime-Computer muss eine Verbindung mit verschiedenen Ressourcen herstellen, um ordnungsgemäß zu funktionieren:

* Quellen, die Sie mithilfe der selbstgehosteten Integration Runtime überprüfen möchten
* Alle Azure Key Vault-Instanzen, die zum Speichern von Anmeldeinformationen für die Purview-Ressource verwendet werden
* Das verwaltete Speicherkonto und Event Hub-Ressourcen, die von Purview erstellt wurden

Die verwalteten Speicher- und Event Hub-Ressourcen finden Sie in Ihrem Abonnement unterhalb einer Ressourcengruppe, die den Namen Ihrer Purview-Ressource enthält. Azure Purview verwendet diese Ressourcen unter anderem zum Erfassen der Ergebnisse der Überprüfung, sodass die selbstgehostete Integration Runtime in der Lage sein muss, eine direkte Verbindung mit diesen Ressourcen herzustellen.

Nachfolgend werden die Domänen und Ports aufgeführt, die über Unternehmens- und Computerfirewalls zugelassen werden müssen.

> [!NOTE]
> Fügen Sie für aufgelistete Domänen mit der Kennzeichnung \<managed Purview storage account> den Namen des verwalteten Speicherkontos hinzu, das Ihrer Purview-Ressource zugeordnet ist. Sie finden diese Ressource im Portal. Durchsuchen Sie Ihre Ressourcengruppen nach einer Gruppe mit dem folgenden Namen: managed-rg-\<your Purview Resource name>. Beispiel: managed-rg-contosoPurview. Sie verwenden den Namen des Speicherkontos in dieser Ressourcengruppe.
> 
> Fügen Sie für aufgelistete Domänen mit der Kennzeichnung \<managed Event Hub resource> den Namen der verwalteten Event Hub-Instanz hinzu, die Ihrer Purview-Ressource zugeordnet ist. Diese befindet sich in derselben Ressourcengruppe wie das verwaltete Speicherkonto.

| Domänennamen                  | Ausgehende Ports | BESCHREIBUNG                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | Globale Infrastruktur, die Purview zum Ausführen der Überprüfungen verwendet. Der Platzhalter ist erforderlich, da keine dedizierte Ressource vorhanden ist. |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview verwendet diese Domäne, um eine Verbindung mit der zugeordneten Service Bus-Instanz herzustellen. Sie wird durch die Zulassung der obigen Domäne abgedeckt, aber wenn Sie private Endpunkte verwenden, müssen Sie den Zugriff auf diese einzelne Domäne testen.|
| `*.frontend.clouddatahub.net` | 443            | Globale Infrastruktur, die Purview zum Ausführen der Überprüfungen verwendet. Der Platzhalter ist erforderlich, da keine dedizierte Ressource vorhanden ist. |
| `<managed Purview storage account>.core.windows.net`          | 443            | Wird von der selbstgehosteten Integration Runtime verwendet, um eine Verbindung mit dem verwalteten Azure Storage-Konto herzustellen.|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | Warteschlangen, die von Purview zum Ausführen des Überprüfungsprozesses verwendet werden. |
| `download.microsoft.com` | 443           | Optional, für SHIR-Updates. |

Basierend auf Ihren Quellen müssen Sie möglicherweise auch die Domänen anderer Azure- oder externer Quellen zulassen. Im Folgenden finden Sie einige Beispiele sowie die Azure Key Vault-Domäne, wenn Sie eine Verbindung mit Anmeldeinformationen herstellen, die im Key Vault gespeichert sind.

| Domänennamen                  | Ausgehende Ports | BESCHREIBUNG                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | Optional, zum Herstellen der Verbindung mit einem Azure Storage-Konto |
| `*.database.windows.net`      | 1433           | Optional, zum Herstellen der Verbindung mit einer Azure SQL-Datenbank oder Azure Synapse Analytics |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Optional, zum Herstellen der Verbindung mit Azure Data Lake Storage Gen 1. |
| `<datastoragename>.dfs.core.windows.net`    | 443            | Optional, zum Herstellen der Verbindung mit Azure Data Lake Storage Gen 2. |
| `<your Key Vault Name>.vault.azure.net` | 443           | Erforderlich, wenn Anmeldeinformationen in Azure Key Vault gespeichert werden. |
| Verschiedene Domänen | Abhängig von Umgebung          | Domänen für weitere Quellen zur Verbindungsherstellung über SHIR. |
  
> [!IMPORTANT]
> In den meisten Umgebungen müssen Sie außerdem bestätigen, dass DNS ordnungsgemäß konfiguriert ist. Dient der Bestätigung, dass Sie von Ihrem SHIR-Computer aus mithilfe von **nslookup** die Konnektivität mit jeder der oben genannten Domänen überprüfen können. Jede nslookup-Abfrage sollte die IP-Adresse der Ressource zurückgeben. Wenn Sie [private Endpunkte](catalog-private-link.md) verwenden, sollte die private IP-Adresse und nicht die öffentliche IP-Adresse zurückgegeben werden. Wenn keine IP-Adresse oder bei Verwendung privater Endpunkte die öffentliche IP-Adresse zurückgegeben wird, müssen Sie Ihre DNS-/VNet-Zuordnung oder Ihr privates Endpunkt-/VNet-Peering überprüfen.

## <a name="manage-a-self-hosted-integration-runtime"></a>Verwalten einer selbstgehosteten Integration Runtime

Sie können eine selbstgehostete Integration Runtime bearbeiten, indem Sie im **Verwaltungscenter** zu **Integration Runtimes** navigieren und die IR und dann „Bearbeiten“ auswählen. Sie können die Beschreibung jetzt aktualisieren, den Schlüssel kopieren oder neue Schlüssel generieren.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Bearbeiten der IR":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Bearbeiten von Details der IR":::

Sie können eine selbstgehostete Integration Runtime löschen, indem Sie im Verwaltungscenter zu **Integration Runtimes** navigieren und die IR und dann **Löschen** auswählen. Nachdem eine IR gelöscht wurde, treten für alle Überprüfungen, die darauf basieren, Fehler auf.

## <a name="java-runtime-environment-installation"></a>Installation der Java-Laufzeitumgebung

Wenn Sie Parquet-Dateien mit der Self-Hosted Integration Runtime mit Purview scannen wollen, müssen Sie entweder die Java Runtime Environment oder OpenJDK auf Ihrem Self-Hosted IR Rechner installieren.

Beim Scannen von Parkettdateien mit der selbst gehosteten IR sucht der Dienst die Java-Laufzeitumgebung, indem er erstens in der Registrierung *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* nach JRE und, falls nicht gefunden, zweitens in der Systemvariable *`JAVA_HOME`* nach OpenJDK sucht.

- **Für JRE:** Die 64-Bit-Integration Runtime erfordert die 64-Bit-JRE. Diese steht [hier](https://go.microsoft.com/fwlink/?LinkId=808605) zur Verfügung.
- **Für OpenJDK:** Die Unterstützung ist seit der IR-Version 3.13 verfügbar. Packen Sie die Datei „jvm.dll“ zusammen mit allen anderen erforderlichen OpenJDK-Assemblys in einem selbstgehosteten IR-Computer, und legen Sie die Umgebungsvariable JAVA_HOME des Systems entsprechend fest.

## <a name="proxy-server-considerations"></a>Proxyserver-Aspekte

Konfigurieren Sie die selbstgehostete Integration Runtime mit den geeigneten Proxyeinstellungen, wenn die Netzwerkumgebung Ihres Unternehmens einen Proxyserver für den Internetzugriff verwendet. Sie können den Proxy während der anfänglichen Registrierungsphase festlegen.

:::image type="content" source="media/manage-integration-runtimes/self-hosted-proxy.png" alt-text="Angeben des Proxys":::

Wenn die selbstgehostete Integration Runtime konfiguriert ist, verwendet sie den Proxyserver zum Herstellen der Verbindung mit der Quelle und dem Ziel des Clouddiensts (für die das HTTP- oder HTTPS-Protokoll verwendet wird). Aus diesem Grund wählen Sie beim anfänglichen Setup die Option **Verknüpfung ändern** aus.

:::image type="content" source="media/manage-integration-runtimes/set-http-proxy.png" alt-text="Festlegen des Proxys":::

Es gibt drei Konfigurationsoptionen:

- **Proxy nicht verwenden**: Für die selbstgehostete IR wird nicht explizit ein Proxy verwendet, um eine Verbindung mit Clouddiensten herzustellen.
- **Systemproxy verwenden**: Die selbstgehostete IR verwendet die in „diahost.exe.config“ und „diawp.exe.config“ konfigurierten Proxyeinstellungen. Wenn für diese Dateien keine Proxykonfiguration angegeben wird, stellt die selbstgehostete Integration Runtime eine direkte Verbindung mit dem Clouddienst her, ohne einen Proxy zu durchlaufen.
- **Benutzerdefinierten Proxy verwenden**: Konfigurieren Sie hier die HTTP-Proxyeinstellungen, die für die selbstgehostete Integration Runtime verwendet werden sollen, anstatt die Konfigurationen in den Dateien „diahost.exe.config“ und „diawp.exe.config“ zu nutzen. Die Werte für **Adresse** und **Port** sind erforderlich. Die Werte für **Benutzername** und **Kennwort** sind je nach den Authentifizierungseinstellungen Ihres Proxys optional. Alle Einstellungen werden für die selbstgehostete Integrationslaufzeit per Windows DPAPI verschlüsselt und lokal auf dem Computer gespeichert.

Der Hostdienst der Integration Runtime wird automatisch neu gestartet, nachdem Sie die aktualisierten Proxyeinstellungen gespeichert haben.

Wenn Sie nach der Registrierung der selbstgehosteten Integration Runtime die Proxyeinstellungen anzeigen oder aktualisieren möchten, können Sie den Konfigurations-Manager für die Microsoft Integration Runtime verwenden.

1. Öffnen Sie den **Konfigurations-Manager für Microsoft Integration Runtime**.
3. Wählen Sie unter **HTTP-Proxy** den Link **Ändern** aus, um das Dialogfeld **HTTP-Proxy festlegen** zu öffnen.
4. Wählen Sie **Weiter** aus. Eine Warnung wird angezeigt, die Sie zur Bestätigung auffordert, dass die Proxyeinstellung gespeichert und der Hostdienst der Integration Runtime neu gestartet werden soll.

Sie können das Konfigurations-Manager-Tool verwenden, um den HTTP-Proxy anzuzeigen und zu aktualisieren.

> [!NOTE]
> Wenn Sie einen Proxyserver mit NTLM-Authentifizierung einrichten, wird der Hostdienst der Integration Runtime unter dem Domänenkonto ausgeführt. Wenn Sie später das Kennwort für das Domänenkonto ändern, sollten Sie daran denken, die Konfigurationseinstellungen für den Dienst entsprechend zu aktualisieren und den Dienst neu zu starten. Aufgrund dieser Anforderung empfehlen wir Ihnen, mit einem dedizierten Domänenkonto auf den Proxyserver zuzugreifen, für das das Kennwort nicht regelmäßig aktualisiert werden muss.

## <a name="installation-best-practices"></a>Bewährte Methoden für die Installation

Sie können die selbstgehostete Integration Runtime installieren, indem Sie aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) ein Setuppaket des Typs „Verwaltete Identität“ herunterladen.

- Konfigurieren Sie den Energiesparplan auf dem Hostcomputer für die selbstgehostete Integration Runtime, damit der Computer nicht in den Ruhezustand versetzt wird. Wenn der Hostcomputer in den Ruhezustand versetzt wird, wechselt die selbstgehostete Integration Runtime in den Offlinemodus.
- Sichern Sie regelmäßig die Anmeldeinformationen, die der selbstgehosteten Integration Runtime zugeordnet sind.

## <a name="next-steps"></a>Nächste Schritte

- [So werden gelöschte Ressourcen bei Scans erkannt](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Verwenden privater Endpunkte mit Purview](catalog-private-link.md)
