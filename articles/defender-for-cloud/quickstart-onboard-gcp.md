---
title: Verbinden eines GCP-Kontos mit Microsoft Defender für Cloud
description: Überwachen von GCP-Ressourcen in Microsoft Defender für Cloud
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: quickstart
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: da578e8d691a26d17a7aab329ce8263cf5066335
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526738"
---
#  <a name="connect-your-gcp-accounts-to-microsoft-defender-for-cloud"></a>Verbinden eines GCP-Kontos mit Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Microsoft Defender für Cloud schützt Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Durch Hinzufügen eines GCP-Kontos zu einem Azure-Abonnement stellt Defender für Cloud eine Verbindung mit GCP Security Command her. Defender für Cloud kann Ihre Ressourcen dann in beiden Cloudumgebungen schützen und Folgendes bereitstellen:

- Erkennung von Sicherheitsfehlkonfigurationen
- Eine einzelne Ansicht mit Security Center-Empfehlungen und GCP Security Command Center-Ergebnissen
- Integration Ihrer GCP-Ressourcen in die Sicherheitsbewertungsberechnungen von Defender für Cloud
- Integration der auf dem CIS-Standard basierenden GCP Security Command Center-Empfehlungen in das Dashboard zur Einhaltung gesetzlicher Bestimmungen von Defender für Cloud

> [!IMPORTANT]
> Bei der Ignite Fall 2021 wurde eine aktualisierte Methode zum Verbinden Ihrer Konten von anderen Cloudanbietern angekündigt. Dabei wird die neue Seite **Umgebungseinstellungen** verwendet. GCP-Konten werden von dieser Seite nicht unterstützt. Um ein GCP-Konto mit Ihrem Azure-Abonnement zu verbinden, müssen Sie die klassische Benutzerumgebung für Cloudconnectors verwenden, wie unten beschrieben.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="Screenshot: GCP-Projekte im Übersichtsdashboard von Microsoft Defender für Cloud." lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|[Microsoft Defender für Server](defender-for-servers-introduction.md) erforderlich|
|Erforderliche Rollen und Berechtigungen:|**Besitzer** oder **Mitwirkender** für das entsprechende Azure-Abonnement|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="connect-your-gcp-account"></a>Herstellen einer Verbindung mit Ihrem GCP-Konto

Erstellen Sie einen Connector für jede Organisation, die Sie in Defender für Cloud überwachen möchten.

Wenn Sie Ihre GCP-Konten mit bestimmten Azure-Abonnements verbinden, berücksichtigen Sie die [Google Cloud-Ressourcenhierarchie](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) und die folgenden Richtlinien:

- Sie können Ihre GCP-Konten auf *Organisationsebene* mit Defender für Cloud verbinden.
- Sie können mehrere Organisationen mit einem einzelnen Azure-Abonnement verbinden.
- Sie können mehrere Organisationen mit mehreren Azure-Abonnements verbinden.
- Wenn Sie eine Organisation verbinden, werden alle in dieser Organisation enthaltenen *Projekte* zu Defender für Cloud hinzugefügt.

Führen Sie die folgenden Schritte aus, um Ihren GCP-Cloudconnector zu erstellen: 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Schritt 1: Einrichten von GCP Security Command Center mit Security Health Analytics

Für alle GCP-Projekte in Ihrer Organisation müssen Sie außerdem folgende Schritte ausführen:

1. Richten Sie **GCP Security Command Center** mithilfe [dieser Anweisungen aus der GCP-Dokumentation](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup) ein.
1. Aktivieren Sie **Security Health Analytics** mithilfe [dieser Anweisungen aus der GCP-Dokumentation](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Vergewissern Sie sich, dass Daten an Security Command Center übertragen werden.

Die Anweisungen zum Verbinden Ihrer GCP-Umgebung für die Sicherheitskonfiguration entsprechen den Empfehlungen von Google zur Sicherheitskonfiguration. Die Integration nutzt das Google Security Command Center sowie weitere Ressourcen, die sich auf Ihre Abrechnung auswirken können.

Wenn Sie Security Health Analytics erstmalig aktivieren, kann es mehrere Stunden dauern, bis die Daten verfügbar sind.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Schritt 2: Aktivieren der GCP Security Command Center-API

1. Wählen Sie in der **Cloud Console-API-Bibliothek** von Google die Projekte in der Organisation aus, die Sie mit Microsoft Defender für Cloud verbinden möchten.
1. Suchen Sie in der API-Bibliothek nach **Security Command Center API**, und wählen Sie diesen Eintrag aus.
1. Wählen Sie auf der Seite der API **ENABLE** (Aktivieren) aus.

Erfahren Sie mehr über die [Security Command Center-API](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Schritt 3: Erstellen eines dedizierten Dienstkontos für die Integration der Sicherheitskonfiguration

1. Wählen Sie in der **GCP-Konsole** ein Projekt aus der Organisation aus, in der Sie das erforderliche Dienstkonto erstellen. 

    > [!NOTE]
    > Wenn dieses Dienstkonto auf der Organisationsebene hinzugefügt wird, wird es für den Zugriff auf die Daten verwendet, die von Security Command Center aus allen anderen aktivierten Projekten in der Organisation gesammelt werden. 

1. Wählen Sie im **Navigationsmenü** unter **IAM & admin** die Option **Service accounts** (Dienstkonten) aus.
1. Wählen Sie **CREATE SERVICE ACCOUNT** (Dienstkonto erstellen) aus.
1. Geben Sie einen Kontonamen ein, und wählen Sie **Create** (Erstellen) aus.
1. Geben Sie die **Rolle** als **Defender für Cloud Admin Viewer** an,und wählen Sie **Continue** (Weiter) aus.
1. Der Abschnitt **Grant users access to this service account** (Benutzern Zugriff auf dieses Dienstkonto gewähren) ist optional. Wählen Sie **Fertig** aus.
1. Kopieren Sie den Wert **E-Mail** des erstellten Dienstkontos, und speichern Sie ihn für die spätere Verwendung.
1. Wählen Sie im **Navigationsmenü** unter **IAM & admin** die Option **IAM** aus.
    1. Wechseln Sie zur Organisationsebene.
    1. Wählen Sie **ADD** (Hinzufügen) aus.
    1. Fügen Sie im Feld **New members** (Neue Mitglieder) den Wert von **E-Mail** ein, den Sie zuvor kopiert haben.
    1. Geben Sie die Rolle als **Defender für Cloud Admin Viewer** an,und wählen Sie **Save** (Speichern) aus.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Festlegen der relevanten GCP-Berechtigungen":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Schritt 4: Erstellen eines privaten Schlüssels für das dedizierte Dienstkonto
1. Wechseln Sie zur Projektebene.
1. Wählen Sie im **Navigationsmenü** unter **IAM & admin** die Option **Service accounts** (Dienstkonten) aus.
1. Öffnen Sie das dedizierte Dienstkonto, und wählen Sie „Edit“ (Bearbeiten) aus.
1. Wählen Sie im Abschnitt **Keys** (Schlüssel) die Option **ADD KEY** (Schlüssel hinzufügen) und dann **Create new key** (Neuen Schlüssel erstellen) aus.
1. Wählen Sie auf dem Bildschirm „Create private key“ (Privaten Schlüssel erstellen) die Option **JSON** und dann **CREATE** (Erstellen) aus.
1. Speichern Sie diese JSON-Datei für die spätere Verwendung.


### <a name="step-5-connect-gcp-to-defender-for-cloud"></a>Schritt 5. Herstellen einer Verbindung zwischen GCP und Defender für Cloud
1. Öffnen Sie im Defender für Cloud-Menü die Option **Umgebungseinstellungen**, und wählen Sie die Option aus, um zurück zur klassischen Benutzeroberfläche für Connectors zu wechseln.

    :::image type="content" source="media/quickstart-onboard-gcp/classic-connectors-experience.png" alt-text="Zurückwechseln zur klassischen Benutzeroberfläche für Cloudconnectors in Defender für Cloud.":::

1. Wählen Sie „add GCP account“ (GCP-Konto hinzufügen) aus.
1. Führen Sie auf der Seite „Onboarding“ die folgenden Schritte aus, und wählen Sie dann **Next** (Weiter) aus.
    1. Überprüfen Sie das ausgewählte Abonnement.
    1. Geben Sie im Feld **Display name** (Anzeigename) einen Anzeigenamen für den Connector ein.
    1. Geben Sie im Feld **Organization ID** (Organisations-ID) die ID Ihrer Organisation ein. Wenn sie Ihnen nicht bekannt ist, finden Sie weitere Informationen unter [Creating and managing organizations](https://cloud.google.com/resource-manager/docs/creating-managing-organization) (Erstellen und Verwalten von Organisationen).
    1. Navigieren Sie im Dateifeld **Private Key** (Privater Schlüssel) zu der JSON-Datei, die Sie in [Schritt 4: Erstellen eines privaten Schlüssels für das dedizierte Dienstkonto](#step-4-create-a-private-key-for-the-dedicated-service-account) heruntergeladen haben.


### <a name="step-6-confirmation"></a>Schritt 6. Bestätigung

Wenn der Connector erstellt und GCP Security Command Center ordnungsgemäß konfiguriert wurde:

- Der GCP-CIS-Standard wird auf dem Dashboard zur Einhaltung gesetzlicher Bestimmungen von Defender für Cloud angezeigt.
- Sicherheitsempfehlungen für Ihre GCP-Ressourcen werden 5-10 Minuten nach Abschluss des Onboardings im Defender für Cloud-Portal und im Dashboard für die Einhaltung gesetzlicher Bestimmungen angezeigt: :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="GCP-Ressourcen und Empfehlungen auf der Seite mit den Defender für Cloud-Empfehlungen":::


## <a name="monitoring-your-gcp-resources"></a>Überwachen Ihrer GCP-Ressourcen

Wie oben zu sehen ist, werden auf der Seite mit Sicherheitsempfehlungen in Microsoft Defender für Cloud Ihre GCP-Ressourcen in Verbindung mit Ihren Azure- und AWS-Ressourcen für eine echte Multi-Cloud-Ansicht angezeigt.

Zum Anzeigen aller aktiven Empfehlungen für Ihre Ressourcen nach Ressourcentyp verwenden Sie die Ressourcenbestandsseite von Defender für Cloud, und filtern Sie nach dem gewünschten GCP-Ressourcentyp:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Ressourcentypfilter der Seite für den Ressourcenbestand mit den GCP-Optionen"::: 


## <a name="faq---connecting-gcp-accounts-to-microsoft-defender-for-cloud"></a>Häufig gestellte Fragen: Verbinden eines GCP-Kontos mit Microsoft Defender für Cloud

### <a name="can-i-connect-multiple-gcp-organizations-to-defender-for-cloud"></a>Kann ich mehrere GCP-Organisationen mit Defender für Cloud verbinden?
Ja. Über den GCP-Connector von Defender für Cloud werden Ihre Google Cloud-Ressourcen auf *Organisationsebene* verbunden. 

Erstellen Sie einen Connector für jede GCP-Organisation, die Sie in Defender für Cloud überwachen möchten. Wenn Sie eine Organisation verbinden, werden alle in dieser Organisation enthaltenen Projekte zu Defender für Cloud hinzugefügt.

Informationen zur Google Cloud-Ressourcenhierarchie finden Sie in der [Online Dokumentation von Google](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-defender-for-cloud"></a>Gibt es eine API, über die ich meine GCP-Ressourcen mit Defender für Cloud verbinden kann?
Ja. Informationen zum Erstellen, Bearbeiten oder Löschen von Defender für Cloud-Cloudconnectors mit einer REST-API finden Sie in den Details zur [Connectors-API](/rest/api/securitycenter/connectors).

## <a name="next-steps"></a>Nächste Schritte

Das Herstellen einer Verbindung mit Ihrem GCP-Konto ist Teil der in Microsoft Defender für Cloud verfügbaren Multi-Cloud-Umgebung. Zugehörige Informationen finden Sie auf der folgenden Seite:

- [Verbinden von AWS-Konten mit Microsoft Defender für Cloud](quickstart-onboard-aws.md)
- [Google Cloud-Ressourcenhierarchie:](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy) Informationen zur Google Cloud-Ressourcenhierarchie finden Sie in der Online Dokumentation von Google.
