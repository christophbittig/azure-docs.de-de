---
title: Erstellen und Verwalten von Integration Runtimes
description: In diesem Artikel werden die Schritte zum Erstellen und Verwalten von Integration Runtimes in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 1a51af8fd34516ca87d7ab98332221a308480193
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217137"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Erstellen und Verwalten einer selbstgehosteten Integration Runtime

In diesem Artikel wird beschrieben, wie Sie eine selbstgehostete Integration Runtime (SHIR) erstellen und verwalten, um Datenquellen in Azure Purview zu überprüfen.

> [!NOTE]
> Die Purview-Integration Runtime kann nicht zusammen mit einer Azure Synapse Analytics- oder Azure Data Factory-Integration Runtime auf demselben Computer verwendet werden. Sie muss auf einem separaten Computer installiert werden.

> [!IMPORTANT]
> Wenn Sie Ihr Azure Purview-Konto nach dem 18. August 2021 erstellt haben, stellen Sie sicher, dass Sie die neueste Version der selbstgehosteten Integration Runtime aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) herunterladen und installieren.

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

## <a name="next-steps"></a>Nächste Schritte

- [So werden gelöschte Ressourcen bei Scans erkannt](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Verwenden privater Endpunkte mit Purview](catalog-private-link.md)
