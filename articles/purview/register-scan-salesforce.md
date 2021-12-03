---
title: Herstellen einer Verbindung mit und Verwalten von Salesforce
description: In diesem Leitfaden wird beschrieben, wie Sie in Azure Purview eine Verbindung mit Salesforce herstellen und mit den Features von Purview Ihre Salesforce-Quelle überprüfen und verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 81f4775771c162dce061bc1ad8901bd9b9542d40
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554938"
---
# <a name="connect-to-and-manage-salesforce-in-azure-purview-preview"></a>Herstellen einer Verbindung mit und Verwalten von Salesforce in Azure Purview (Vorschau)

In diesem Artikel wird beschrieben, wie Sie Salesforce in Azure Purview registrieren, authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

> [!IMPORTANT]
> Salesforce als Quelle befindet sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| Nein|

Beim Überprüfen von Salesforce unterstützt Purview das Extrahieren von Metadaten, einschließlich Salesforce-Organisationen, -Objekten, -Feldern, -Fremdschlüsseln und unique_constraints.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

* Stellen Sie sicher, dass die IP-Adresse des selbstgehosteten Integration Runtime-Computers innerhalb der [vertrauenswürdigen IP-Bereiche für Ihr Unternehmen](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) liegt, die in Salesforce festgelegt sind. Andernfalls müssen Sie zusätzlich das Sicherheitstoken bereitstellen, um sich von einem nicht vertrauenswürdigen Netzwerk aus bei Salesforce zu authentifizieren. Erfahren Sie mehr über die Konfiguration der Anmeldeinformationen im Abschnitt [Überprüfen](#scan).

* Für den Fall, dass Benutzer Salesforce-Dokumente übermitteln, müssen bestimmte Sicherheitseinstellungen konfiguriert werden, um diesen Zugriff auf Standardobjekte und benutzerdefinierte Objekte zu ermöglichen. So konfigurieren Sie Berechtigungen
    - Klicken Sie in Salesforce auf „Setup“ (Einrichtung) und dann auf „Manage Users“ (Benutzer verwalten).
    - Klicken Sie unter der Struktur „Manage Users“ (Benutzer verwalten) auf „Profiles“ (Profile).
    - Sobald die Profile auf der rechten Seite angezeigt werden, wählen Sie das Profil aus, das Sie bearbeiten möchten, und klicken Sie auf den Link „Edit“ (Bearbeiten) neben dem entsprechenden Profil.
    
    Stellen Sie für Standardobjekte sicher, dass im Abschnitt „Documents“ (Dokumente) die Leseberechtigungen aktiviert sind. Stellen Sie für benutzerdefinierte Objekte sicher, dass die Leseberechtigungen für jedes benutzerdefinierte Objekt ausgewählt sind.

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie Salesforce in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Salesforce-Quelle zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto im [Purview Studio](https://web.purview.azure.com/resource/).
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Quellen registrieren“ die Option **Salesforce** aus. Wählen Sie **Weiter**.

Gehen Sie auf dem Bildschirm **Quellen registrieren (Salesforce)** wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1. Geben Sie die Endpunkt-URL für die Salesforce-Anmeldung als **Domänen-URL** ein. Beispielsweise `https://login.salesforce.com`. Sie können die Instanz-URL Ihres Unternehmens (z. B. `https://na30.salesforce.com`) oder die „Meine Domäne“-URL (z. B. `https://myCompanyName.my.salesforce.com/`) verwenden.

1. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-salesforce/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Salesforce zu überprüfen und automatisch Ressourcen zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

Azure Purview verwendet die Salesforce REST-API-Version 41.0 zum Extrahieren von Metadaten, einschließlich REST-Anforderungen wie „Describe Global“-URI (/v41.0/sobjects/), „sObject Basic Information“-URI (/v41.0/sobjects/sObject/) und „SOQL Query“-URI (/v41.0/query?).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, erstellen Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die registrierte Salesforce-Quelle aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:
        * Wählen Sie **Consumerschlüssel** aus, während Sie Anmeldeinformationen erstellen.
        * Geben Sie den Benutzernamen des Benutzers, den die verbundene App imitiert, in das Eingabefeld „Benutzername“ ein.
        * Speichern Sie das Kennwort des Benutzers, den die verbundene App imitiert, in einem Azure Key Vault-Geheimnis. 
            * Wenn sich die IP-Adresse des selbstgehosteten Integration Runtime-Computers innerhalb der [vertrauenswürdigen IP-Bereiche für Ihr Unternehmen](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) liegt, die in Salesforce festgelegt sind, stellen Sie nur das Kennwort für den Benutzer bereit.
            * Verketten Sie andernfalls das Kennwort und das Sicherheitstoken als Wert des Geheimnisses. Das Sicherheitstoken ist ein automatisch generierter Schlüssel, der am Ende des Kennworts hinzugefügt werden muss, wenn Sie sich über ein nicht vertrauenswürdiges Netzwerk bei Salesforce anmelden. Erfahren Sie mehr über das [Abrufen oder Zurücksetzen eines Sicherheitstokens](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm).
        * Geben Sie den Consumerschlüssel aus der Definition der verbundenen App an. Sie finden ihn auf der Seite „Manage Connected Apps“ (Verbundene Apps verwalten) der verbundenen App oder in der Definition der verbundenen App.
        * Das Consumergeheimnis aus der Definition der verbundenen App wurde in einem Azure Key Vault-Geheimnis gespeichert. Sie können es zusammen mit dem Consumerschlüssel finden.

    1. **Objekte**: Geben Sie eine Liste von Objektnamen an, um den Gültigkeitsbereich für Ihre Überprüfung festzulegen. Beispielsweise `object1; object2`. Eine leere Liste bedeutet, dass alle verfügbaren Objekte abgerufen werden. Sie können Objektnamen als Platzhaltermuster angeben. Beispiel: `topic?`, `*topic*` oder `topic_?,*topic*`.

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies ist abhängig von der Größe der zu überprüfenden Salesforce-Quelle.

        > [!Note]
        > Faustregel: 1 GB Arbeitsspeicher pro 1000 Tabellen.

        :::image type="content" source="media/register-scan-salesforce/scan.png" alt-text="Überprüfen von Salesforce" border="true":::

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)
