---
title: Verbinden Ihres AWS-Kontos mit Microsoft Defender für Cloud
description: Schützen Ihrer AWS-Ressourcen mit Microsoft Defender für Cloud
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: quickstart
ms.service: defender-for-cloud
manager: rkarlin
zone_pivot_groups: connect-aws-accounts
ms.openlocfilehash: 37352dcf8ae68a107fd10dc76a7ccce5fbab95d0
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526711"
---
#  <a name="connect-your-aws-accounts-to-microsoft-defender-for-cloud"></a>Verbinden Ihrer AWS-Konten mit Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Microsoft Defender für Cloud schützt Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Zum Schutz Ihrer AWS-basierten Ressourcen können Sie ein Konto mit einem von zwei Mechanismen verbinden:

- **Klassische Benutzeroberfläche für Cloudconnectors**: Im Rahmen des ersten Multi-Cloud-Angebots haben wir diese Cloudconnectors als Möglichkeit zum Verbinden Ihrer AWS- und GCP-Konten eingeführt. Wenn Sie bereits einen AWS-Connector über die klassische Benutzeroberfläche für Cloudconnectors konfiguriert haben, empfiehlt es sich, das Konto mithilfe des neueren Mechanismus erneut zu verbinden. Wenn Sie Ihr Konto über die Seite mit den Umgebungseinstellungen hinzugefügt haben, entfernen Sie den alten Connector, um zu vermeiden, dass doppelte Empfehlungen angezeigt werden.

- **Seite „Umgebungseinstellungen“ (in der Vorschau)** (empfohlen): Diese Vorschauseite bietet eine deutlich verbesserte, einfachere Onboardingumgebung (einschließlich automatischer Bereitstellung). Dieser Mechanismus weitet zudem die erweiterten Sicherheitsfeatures von Defender für Cloud auf Ihre AWS-Ressourcen aus.

    - Die **CSPM-Features von Defender für Cloud** werden auf Ihre AWS-Ressourcen ausgeweitet. Dieser Plan ohne Agent bewertet Ihre AWS-Ressourcen gemäß AWS-spezifischen Sicherheitsempfehlungen und ist in Ihrer Sicherheitsbewertung enthalten. Die Ressourcen werden auch auf Einhaltung integrierter AWS-spezifischer Standards (AWS CIS, AWS PCI-DSS und AWS Foundational Security Best Practices) bewertet. Die Seite [Bestandsverzeichnis](asset-inventory.md) von Defender für Cloud ist eine Multi-Cloud-Funktion, die Ihnen hilft, Ihre AWS-Ressourcen zusammen mit Ihren Azure-Ressourcen zu verwalten.
    - Mit **Microsoft Defender für Kubernetes** werden die Containerbedrohungserkennung und erweiterten Schutzmaßnahmen auf Ihre **Amazon EKS Linux-Cluster** ausgeweitet.
    - **Microsoft Defender für Server** stattet Ihre Windows- und Linux-EC2-Instanzen mit Bedrohungserkennung und erweiterten Schutzmaßnahmen aus. Dieser Plan umfasst die integrierte Lizenz für Microsoft Defender für Endpunkt, Sicherheitsbaselines und Bewertungen auf Betriebssystemebene, Überprüfungen zur Sicherheitsrisikobewertung, adaptive Anwendungssteuerung (AAC), Überwachung der Dateiintegrität (FIM) und mehr.

Im folgenden Screenshot sehen Sie die AWS-Konten, die im [Übersichtsdashboard](overview-page.md) von Defender für Cloud angezeigt werden.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Vier AWS-Projekte, die im Dashboard „Übersicht“ von Defender für Cloud aufgeführt sind" lightbox="./media/quickstart-onboard-aws/aws-account-in-overview.png":::


::: zone pivot="env-settings"

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Preise:|Der CSPM-Plan ist kostenlos.<br>Der Defender für Kubernetes-Plan ist während der Vorschau kostenlos. Danach erfolgt die Abrechnung zum gleichen Preis wie beim Defender für Kubernetes-Plan für Azure-Ressourcen.<br>Für jeden AWS-Computer, der mit [Servern mit Azure Arc-Unterstützung](../azure-arc/servers/overview.md) verbunden ist, wird der Defender für Server-Plan zum gleichen Preis abgerechnet wie der Defender für Server-Plan für Azure-Computer. Wenn in AWS EC2 der Azure Arc-Agent nicht bereitgestellt ist, wird Ihnen dieser Computer nicht in Rechnung gestellt.|
|Erforderliche Rollen und Berechtigungen:|**Besitzer** des relevanten Azure-Abonnements<br>**Mitwirkender**: Kann auch eine Verbindung mit einem AWS-Konto herstellen, wenn er von einem Besitzer die Dienstprinzipaldetails erhält (für den Defender für Server-Plan erforderlich)|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>Voraussetzungen

- Um ein AWS-Konto mit Ihrem Azure-Abonnement zu verbinden, benötigen Sie natürlich Zugriff auf ein AWS-Konto.

- **Um den Defender für Kubernetes-Plan zu aktivieren,** benötigen Sie Folgendes:
    - Mindestens einen Amazon EKS-Cluster mit Zugriffsberechtigung auf den EKS K8s-API-Server. Wenn Sie einen neuen EKS-Cluster erstellen müssen, befolgen Sie die Anweisungen unter [Erste Schritte mit Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html).
    - Die Ressourcenkapazität zum Erstellen einer neuen SQS-Warteschlange, des Kinesis Fire Hose-Übermittlungsstreams und des S3-Buckets in der Region des Clusters.

- **Um den Defender für Server-Plan zu aktivieren,** benötigen Sie Folgendes:
    - Microsoft Defender für Server wurde aktiviert (siehe [Schnellstart: Aktivieren von erweiterten Sicherheitsfeatures](enable-enhanced-security.md)).
    - Ein aktives AWS-Konto mit EC2-Instanzen, die von AWS Systems Manager (SSM) verwaltet werden und den SSM-Agent verwenden. Bei einigen Amazon Machine Images (AMIs) ist der SSM-Agent vorinstalliert und ihre AMIs sind unter [AMIs mit vorinstalliertem SSM-Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent-technical-details.html#ami-preinstalled-agent) aufgeführt. Wenn Ihre EC2-Instanzen über keinen SSM-Agent verfügen, führen Sie die entsprechenden Anweisungen von Amazon aus:
        - [Installieren des SSM-Agents für eine Hybridumgebung (Windows)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-win.html)
        - [Installieren des SSM-Agents für eine Hybridumgebung (Linux)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-linux.html)


## <a name="connect-your-aws-account"></a>Herstellen einer Verbindung mit Ihrem AWS-Konto

Führen Sie die folgenden Schritte aus, um Ihren AWS-Cloudconnector zu erstellen: 

1. Öffnen Sie im Menü von Defender für Cloud **Umgebungseinstellungen**.
1. Wählen Sie **Umgebung hinzufügen** > **Amazon Web Services** aus.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-environment-settings.png" alt-text="Verbinden eines AWS-Kontos mit einem Azure-Abonnement.":::

1. Geben Sie die Details des AWS-Kontos ein, einschließlich des Speicherorts für die Connectorressource, und wählen Sie **Weiter: Pläne auswählen** aus.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-details.png" alt-text="Schritt 1 des Assistenten zum Hinzufügen von AWS-Konten: Eingeben der Kontodetails.":::

1. Auf der Registerkarte „Pläne auswählen“ wählen Sie aus, welche Defender für Cloud-Funktionen für dieses AWS-Konto aktiviert werden sollen. 

    > [!NOTE]
    > Jede Funktion verfügt über eigene Anforderungen für Berechtigungen und kann Gebühren verursachen.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-plans-selection.png" alt-text="Auf der Registerkarte „Pläne auswählen“ wählen Sie aus, welche Defender für Cloud-Funktionen für dieses AWS-Konto aktiviert werden sollen.":::

    > [!IMPORTANT]
    > Um den aktuellen Status Ihrer Empfehlungen anzuzeigen, fragt der CSPM-Plan mehrmals täglich die AWS-Ressourcen-APIs ab. Für diese schreibgeschützten API-Aufrufe werden keine Gebühren berechnet, sie *werden* jedoch in CloudTrail registriert, wenn Sie einen Trail für Leseereignisse aktiviert haben. Wie in der [AWS-Dokumentation](https://aws.amazon.com/cloudtrail/pricing/) erläutert, fallen für einen Trail keine zusätzlichen Gebühren an. Wenn Sie die Daten aus AWS exportieren (z. B. in ein externes SIEM), können sich durch diese erhöhte Anzahl von Aufrufen auch die Erfassungskosten erhöhen. In solchen Fällen wird empfohlen, die schreibgeschützten Aufrufe aus dem Benutzer- oder Rollen-ARN von Defender für Cloud herauszufiltern: arn:aws:iam::[accountId]:role/CspmMonitorAws. (Hier wird der Standardrollename verwendet. Überprüfen Sie den in Ihrem Konto konfigurierten Rollennamen.)

    - Um die Defender für Server-Abdeckung auf AWS EC2 auszuweiten, legen Sie den **Servers**-Plan auf **Ein** fest, und bearbeiten Sie die Konfiguration nach Bedarf. 

    - Um die Defender für Kubernetes-Abdeckung auf Ihre AWS EKS-Linux-Cluster auszuweiten, legen Sie den **Containers**-Plan auf **Ein** fest, und bearbeiten Sie die Konfiguration nach Bedarf.

1. Schließen Sie die Installation ab:
    1. Wählen Sie **Weiter: Zugriff konfigurieren** aus.
    1. Laden Sie die Vorlage „CloudFormation“ herunter.
    1. Erstellen Sie mithilfe der heruntergeladenen CloudFormation-Vorlage den Stapel in AWS gemäß den Anweisungen auf dem Bildschirm.
    1. Wählen Sie **Weiter: Überprüfen und generieren** aus.
    1. Klicken Sie auf **Erstellen**.

Defender für Cloud beginnt sofort mit der Überprüfung Ihrer AWS-Ressourcen, und innerhalb weniger Stunden werden Sicherheitsempfehlungen angezeigt.

::: zone-end


::: zone pivot="classic-connector"

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|[Microsoft Defender für Server](defender-for-servers-introduction.md) erforderlich|
|Erforderliche Rollen und Berechtigungen:|**Besitzer** des relevanten Azure-Abonnements<br>**Mitwirkender**: Kann auch eine Verbindung mit einem AWS-Konto herstellen, wenn er von einem Besitzer die Dienstprinzipaldetails erhält|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||


## <a name="connect-your-aws-account"></a>Herstellen einer Verbindung mit Ihrem AWS-Konto

Führen Sie die folgenden Schritte aus, um Ihren AWS-Cloudconnector zu erstellen: 

### <a name="step-1-set-up-aws-security-hub"></a>Schritt 1: Einrichten von AWS Security Hub:

1. Um Sicherheitsempfehlungen für mehrere Regionen anzuzeigen, wiederholen Sie die folgenden Schritte für jede relevante Region.

    > [!IMPORTANT]
    > Wenn Sie ein AWS-Verwaltungskonto verwenden, wiederholen Sie die folgenden drei Schritte, um das Verwaltungskonto und alle verbundenen Mitgliedskonten in allen relevanten Regionen zu konfigurieren.

    1. Aktivieren Sie [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Aktivieren Sie [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Überprüfen Sie, ob Daten an den Security Hub fließen. Wenn Sie Security Hub erstmalig aktivieren, kann es mehrere Stunden dauern, bis die Daten verfügbar sind.

### <a name="step-2-set-up-authentication-for-defender-for-cloud-in-aws"></a>Schritt 2: Einrichten der Authentifizierung für Defender für Cloud in AWS

Es gibt zwei Methoden, um Defender für Cloud die Authentifizierung bei AWS zu ermöglichen:

- **Erstellen einer IAM-Rolle für Defender für Cloud** (empfohlen): Dies ist die sicherste Methode.
- **AWS-Benutzer für Defender für Cloud**: Diese Option ist weniger sicher und kann genutzt werden, wenn IAM nicht aktiviert ist.

#### <a name="create-an-iam-role-for-defender-for-cloud"></a>Erstellen einer IAM-Rolle für Defender für Cloud
1. Wählen Sie in Ihrer Amazon Web Services-Konsole unter **Security, Identity & Compliance** (Sicherheit, Identität und Compliance) die Option **IAM** aus.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS-Dienste":::

1. Klicken Sie auf **Roles** (Rollen) und anschließend auf **Create role** (Rolle erstellen).
1. Wählen Sie **Another AWS account** (Ein anderes AWS-Konto) aus.
1. Geben Sie die folgenden Details ein:

    - **Konto ID**: Geben Sie die Microsoft-Konto-ID (**158177204117**) ein, die auf der AWS-Connectorseite in Defender für Cloud angezeigt wird.
    - **Require External ID** (Externe ID erforderlich): Diese Option sollte ausgewählt werden.
    - **Externe ID**: Geben Sie die Abonnement-ID ein, die auf der AWS-Connectorseite in Defender für Cloud angezeigt wird. 

1. Wählen Sie **Weiter** aus.
1. Wählen Sie im Abschnitt **Attach permission policies** (Berechtigungsrichtlinien anfügen) die folgenden [von AWS verwalteten Richtlinien](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) aus:

    - SecurityAudit (`arn:aws:iam::aws:policy/SecurityAudit`)
    - AmazonSSMAutomationRole (`arn:aws:iam::aws:policy/service-role/AmazonSSMAutomationRole`)
    - AWSSecurityHubReadOnlyAccess (`arn:aws:iam::aws:policy/AWSSecurityHubReadOnlyAccess`)

1. Fügen Sie optional Tags hinzu. Das Hinzufügen von Tags zum Benutzer wirkt sich nicht auf die Verbindung aus.
1. Wählen Sie **Weiter** aus.

1. Wählen Sie in der Liste der Rollen die von Ihnen erstellte Rolle aus.

1. Speichern Sie den Amazon Resource Name (ARN) für später. 

#### <a name="create-an-aws-user-for-defender-for-cloud"></a>Erstellen eines AWS-Benutzers für Defender für Cloud 
1. Öffnen Sie die Registerkarte **Users** (Benutzer), und wählen Sie **Add user** (Benutzer hinzufügen) aus.
1. Geben Sie im Schritt **Details** einen Benutzernamen für Defender für Cloud ein, und wählen Sie für den AWS-Zugriffstyp **Programmgesteuerter Zugriff** aus. 
1. Wählen Sie **Next: Permissions** (Weiter: Berechtigungen) aus.
1. Wählen Sie **Attach existing policies directly** (Vorhandene Richtlinien direkt anfügen) aus, und wenden Sie die folgenden Richtlinien an:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Klicken Sie auf **Weiter: Tags**. Fügen Sie optional Tags hinzu. Das Hinzufügen von Tags zum Benutzer wirkt sich nicht auf die Verbindung aus.
1. Wählen Sie **Review** (Überprüfen) aus.
1. Speichern Sie die CSV-Datei mit der automatisch generierten **Zugriffsschlüssel-ID** und dem **geheimen Zugriffsschlüssel** zur späteren Verwendung.
1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Benutzer erstellen** aus.


### <a name="step-3-configure-the-ssm-agent"></a>Schritt 3: Konfigurieren des SSM-Agents

AWS Systems Manager ist für die Automatisierung von Aufgaben in Ihren AWS-Ressourcen erforderlich. Wenn Ihre EC2-Instanzen über keinen SSM-Agent verfügen, führen Sie die entsprechenden Anweisungen von Amazon aus:

- [Installieren und Konfigurieren des SSM-Agents auf Windows-Instanzen](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Installieren und Konfigurieren des SSM-Agents auf Amazon EC2-Linux-Instanzen](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Schritt 4: Einrichten der erforderlichen Azure Arc-Komponenten
1. Stellen Sie sicher, dass die entsprechenden [Azure-Ressourcenanbieter](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) registriert sind:
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. Erstellen Sie einen Dienstprinzipal für Onboarding im großen Stil. Als **Besitzer** des Abonnements, das Sie für das Onboarding verwenden möchten, erstellen Sie einen Dienstprinzipal für das Azure Arc-Onboarding, wie unter [Erstellen eines Dienstprinzipals für flexibles Onboarding](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) beschrieben.


### <a name="step-5-connect-aws-to-defender-for-cloud"></a>Schritt 5. Herstellen einer Verbindung zwischen AWS und Defender für Cloud

1. Öffnen Sie im Defender für Cloud-Menü die Option **Umgebungseinstellungen**, und wählen Sie die Option aus, um zurück zur klassischen Connectorumgebung zu wechseln.

    :::image type="content" source="media/quickstart-onboard-gcp/classic-connectors-experience.png" alt-text="Zurückwechseln zur klassischen Benutzeroberfläche für Cloudconnectors in Defender für Cloud.":::

1. Wählen Sie **Add AWS Account** (AWS-Konto hinzufügen) aus.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Schaltfläche „AWS-Konto hinzufügen“ auf der Seite „Multi-Cloud-Connectors“ von Defender für Cloud":::
1. Konfigurieren Sie die Optionen auf der Registerkarte **AWS authentication** (AWS-Authentifizierung):
    1. Geben Sie einen **Anzeigenamen** für den Connector ein.
    1. Überprüfen Sie, ob das Azure-Abonnement richtig ist. Dabei handelt es sich um das Abonnement, das die Empfehlungen für den Connector und den AWS Security Hub enthält.
    1. Je nach der Authentifizierungsoption, die Sie in [Schritt 2. Einrichten der Authentifizierung für Defender für Cloud in AWS](#step-2-set-up-authentication-for-defender-for-cloud-in-aws) ausgewählt haben:
        - Wählen Sie **Assume Role** (Rolle übernehmen) aus, und fügen Sie den ARN aus [Erstellen einer IAM-Rolle für Defender für Cloud](#create-an-iam-role-for-defender-for-cloud) ein.

            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Einfügen der ARN-Dabei im entsprechenden Feld des AWS-Verbindungs-Assistenten im Azure-Portal":::

            oder

        - Wählen Sie **Anmeldeinformationen** aus, und fügen Sie den **Zugriffsschlüssel** und den **geheimen Schlüssel** aus der CSV-Datei ein, die Sie unter [Erstellen eines AWS-Benutzers für Defender für Cloud](#create-an-aws-user-for-defender-for-cloud) gespeichert haben.
1. Wählen Sie **Weiter** aus.
1. Konfigurieren Sie die Optionen auf der Registerkarte **Azure Arc Configuration** (Azure Arc-Konfiguration):

    Defender für Cloud ermittelt die EC2-Instanzen im verbundenen AWS-Konto und verwendet SSM, um für sie ein Onboarding in Azure Arc durchzuführen. 

    > [!TIP]
    > Eine Liste der unterstützten Betriebssysteme finden Sie in den häufig gestellten Fragen unter [Welche Betriebssysteme werden für meine EC2-Instanzen unterstützt?](#what-operating-systems-for-my-ec2-instances-are-supported).

    1. Wählen Sie die **Ressourcengruppe** und die **Azure-Region** aus, für die das Onboarding der ermittelten AWS EC2-Instanzen im ausgewählten Abonnement durchgeführt werden soll.
    1. Geben Sie die **Dienstprinzipal-ID** und das **Clientgeheimnis des Dienstprinzipals** für Azure Arc ein, wie unter [Erstellen eines Dienstprinzipals für flexibles Onboarding](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) beschrieben.
    1. Wenn der Computer die Internetverbindung über einen Proxyserver herstellt, geben Sie die IP-Adresse des Proxyservers oder den Namen und die Portnummer an, die für die Kommunikation mit dem Proxyserver verwendet werden. Geben Sie den Wert im Format ```http://<proxyURL>:<proxyport>``` ein.
    1. Klicken Sie auf **Überprüfen + erstellen**.

        Überprüfen der Zusammenfassungsinformationen

        Im Abschnitt „Tags“ werden alle Azure-Tags aufgelistet, die automatisch für jede EC2-Instanz, für die das Onboarding durchgeführt wurde, erstellt werden,mit den jeweiligen relevanten Details, damit sie in Azure leicht erkannt werden. 

        Unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md) erfahren Sie mehr über Azure-Tags.

### <a name="step-6-confirmation"></a>Schritt 6. Bestätigung

Wenn der Connector erfolgreich erstellt und AWS Security Hub ordnungsgemäß konfiguriert wurde:

- Defender für Cloud überprüft die Umgebung auf AWS EC2-Instanzen, führt für sie ein Onboarding in Azure Arc durch und ermöglicht die Installation des Log Analytics-Agents sowie die Bereitstellung von Bedrohungsschutz und Sicherheitsempfehlungen. 
- Der Defender für Cloud-Dienst sucht alle sechs Stunden nach neuen AWS EC2-Instanzen und führt für sie der Konfiguration entsprechend automatisch ein Onboarding durch.
- Der AWS-CIS-Standard wird im Dashboard für die Einhaltung gesetzlicher Bestimmungen von Defender für Cloud angezeigt.
- Wenn die Security Hub-Richtlinie aktiviert wurde, werden 5–10 Minuten nach Abschluss des Onboardings Empfehlungen im Defender für Cloud-Portal und im Dashboard für die Einhaltung gesetzlicher Bestimmungen angezeigt.


::: zone-end

:::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS-Ressourcen und -Empfehlungen auf der Empfehlungsseite in Defender für Cloud" lightbox="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png":::


## <a name="monitoring-your-aws-resources"></a>Überwachen Ihrer AWS-Ressourcen

Wie im vorherigen Screenshot zu sehen, werden auf der Seite „Sicherheitsempfehlungen“ von Defender für Cloud Ihre AWS-Ressourcen angezeigt. Sie können den Umgebungsfilter verwenden, um die Multi-Cloud-Funktionen von Defender für Cloud zu nutzen: Zeigen Sie die Empfehlungen für Azure-, AWS- und GCP-Ressourcen zusammen an.

Zum Anzeigen aller aktiven Empfehlungen für Ihre Ressourcen nach Ressourcentyp verwenden Sie die Ressourcenbestandsseite von Defender für Cloud, und filtern Sie nach dem gewünschten AWS-Ressourcentyp:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Ressourcentypfilter der Ressourcenbestandsseite mit den AWS-Optionen"::: 


## <a name="faq---aws-in-defender-for-cloud"></a>Häufig gestellte Fragen – AWS in Defender für Cloud

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Welche Betriebssysteme werden für meine EC2-Instanzen unterstützt?

Unterstützte Betriebssysteme für automatisches Onboarding in Azure Arc für AWS-Computer

- Ubuntu 16.04 – der SSM-Agent ist standardmäßig vorinstalliert.
- Ubuntu 18.04 – der SSM-Agent ist standardmäßig vorinstalliert.
- Windows-Server – der SSM-Agent ist standardmäßig vorinstalliert.
- CentOS Linux 7 – SSM muss manuell installiert werden, oder das Onboarding ist separat durchzuführen.
- SUSE Linux Enterprise Server (SLES) 15 (x64) – SSM muss manuell installiert werden, oder das Onboarding ist separat durchzuführen.
- Red Hat Enterprise Linux (RHEL) 7 (x64) – SSM muss manuell installiert werden, oder das Onboarding ist separat durchzuführen.


## <a name="next-steps"></a>Nächste Schritte

Das Herstellen einer Verbindung mit Ihrem AWS-Konto ist Teil der in Microsoft Defender für Cloud verfügbaren Multi-Cloud-Umgebung. Zugehörige Informationen finden Sie auf der folgenden Seite:

- [Verbinden Ihrer GCP-Konten mit Microsoft Defender für Cloud](quickstart-onboard-gcp.md)
