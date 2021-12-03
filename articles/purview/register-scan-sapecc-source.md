---
title: Verbinden mit und Verwalten einer SAP ECC-Quelle
description: In diesem Leitfaden wird beschrieben, wie Sie in Azure Purview eine Verbindung mit SAP ECC herstellen und mit den Features von Purview Ihre SAP ECC-Quelle überprüfen und verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 07aaa62e4e021377ccf247c0a4c058aa9c51d0a1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549601"
---
# <a name="connect-to-and-manage-sap-ecc-in-azure-purview"></a>Verbinden mit und Verwalten von SAP ECC in Azure Purview

In diesem Artikel wird beschrieben, wie Sie SAP ECC in Azure Purview registrieren, authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja**](how-to-lineage-sapecc.md)|

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory-Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

    >[!NOTE]
    >Die Überprüfung von SAP ECC ist ein speicherintensiver Vorgang. Es wird empfohlen, die selbstgehostete Integration Runtime auf einem Computer mit mindestens 128 GB RAM zu installieren.

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

* Laden Sie den [SAP Connector für Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) (64 Bit) von der SAP-Website herunter, und installieren Sie ihn auf dem Computer mit der selbstgehosteten Integration Runtime. Während der Installation müssen Sie im Fenster **Optionale Einrichtungsschritte** die Option **Assemblys in GAC installieren** auswählen.

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="Voraussetzung" border="true":::

* Der Connector liest Metadaten aus SAP mithilfe der [SAP Java-Connector-API 3.0 (JCo)](https://support.sap.com/en/product/connectors/jco.html). Stellen Sie sicher, dass der Java-Connector auf dem virtuellen Computer verfügbar ist, auf dem auch die selbstgehostete Integration Runtime installiert ist. Stellen Sie sicher, dass Sie die richtige JCo-Verteilung für Ihre Umgebung verwenden. Stellen Sie z. B. auf einem Microsoft Windows-Computer sicher, dass die Dateien „sapjco3.jar“ und „sapjco3.dll“ verfügbar sind.

    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

* Stellen Sie das ABAP-Funktionsmodul zur Metadatenextraktion auf dem SAP-Server bereit, indem Sie die Schritte im [Bereitstellungshandbuch für ABAP-Funktionen](abap-functions-deployment-guide.md) befolgen. Sie benötigen ein ABAP-Entwicklerkonto, um das RFC-Funktionsmodul auf dem SAP-Server zu erstellen. Das Benutzerkonto muss über ausreichende Berechtigungen zum Herstellen einer Verbindung mit dem SAP-Server und zum Ausführen der folgenden RFC-Funktionsmodule verfügen:
  * STFC_CONNECTION (Konnektivität überprüfen)
  * RFC_SYSTEM_INFO (Systeminformationen überprüfen)

## <a name="register"></a>Registrieren

In diesem Abschnitt wird beschrieben, wie Sie SAP ECC in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Die einzige unterstützte Authentifizierung für eine SAP ECC-Quelle ist die **Standardauthentifizierung**.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Quellen registrieren“ die Option **SAP ECC** aus. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="Registrieren von SAP ECC-Optionen" border="true":::

Gehen Sie auf dem Bildschirm **Quellen registrieren (SAP ECC)** wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1. Geben Sie den Namen des **Anwendungsservers** zum Herstellen der Verbindung mit der SAP ECC-Quelle ein. Es kann sich auch um eine IP-Adresse des SAP-Anwendungsserverhosts handeln.

1. Geben Sie die SAP-**Systemnummer** ein. Dies ist eine zweistellige ganze Zahl zwischen 00 und 99.

1. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="Registrieren von SAP ECC" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um SAP ECC zu überprüfen und automatisch Ressourcen zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, erstellen Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die registrierte SAP ECC-Quelle aus.

1. Wählen Sie **+ New scan** (+ Neue Überprüfung) aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:

        * beim Erstellen von Anmeldeinformationen die Standardauthentifizierung auswählen.
        * Geben Sie im Eingabefeld „Benutzername“ eine Benutzer-ID zum Herstellen der Verbindung mit dem SAP-Server ein.
        * Speichern Sie das Benutzerkennwort, das verwendet wird, um eine Verbindung mit dem SAP-Server herzustellen, im geheimen Schlüssel.

    1. **Client-ID**: Geben Sie die SAP-Client-ID ein. Dies ist eine dreistellige Zahl von 000 bis 999.

    1. **JCo-Bibliothekspfad**: der Verzeichnispfad, in dem sich die JCo-Bibliotheken befinden.

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem Computer mit der selbstgehosteten Integration Runtime für Überprüfungsprozesse verfügbar ist. Abhängig von der Größe der zu überprüfenden SAP ECC-Quelle. Es wird empfohlen, einen großen verfügbaren Arbeitsspeicher bereitzustellen, z. B. 100.

        :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="SAP ECC überprüfen" border="true":::

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)
