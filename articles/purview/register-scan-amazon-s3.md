---
title: Amazon S3 Multi-Cloud Scanning Connector für Azure Purview
description: In dieser Schrittanleitung wird beschrieben, wie Sie Amazon S3-Buckets in Azure Purview überprüfen.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: 86f0296ced4846dce7ec4be0d5b503d343d060bc
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848114"
---
# <a name="amazon-s3-multi-cloud-scanning-connector-for-azure-purview"></a>Amazon S3 Multi-Cloud Scanning Connector für Azure Purview

Mit dem Multi-Cloud Scanning Connector für Azure Purview können Sie Ihre Organisationsdaten Cloudanbieter übergreifend, einschließlich Amazon Web Services, zusätzlich zu Azure-Speicherdiensten untersuchen.

In diesem Artikel erfahren Sie, wie Sie Azure Purview verwenden, um Ihre derzeit in Amazon S3-Standardbuckets gespeicherten unstrukturierten Daten zu überprüfen, und zu ermitteln, welche Typen vertraulicher Informationen in Ihren Daten vorhanden sind. In diesem Leitfaden wird außerdem beschrieben, wie Sie die Amazon S3-Buckets identifizieren, in denen die Daten derzeit gespeichert sind, um Information Protection und Datencompliance zu vereinfachen.

Verwenden Sie für diesen Dienst Purview, um ein Microsoft-Konto mit sicherem Zugriff auf AWS bereitzustellen, wo der Multi-Cloud Scanning Connector für Azure Purview ausgeführt wird. Der Multi-Cloud Scanning Connector für Azure Purview verwendet diesen Zugriff auf Ihre Amazon S3-Buckets, um Ihre Daten zu lesen, und meldet dann die Überprüfungsergebnisse, nur einschließlich der Metadaten und der Klassifizierung, zurück an Azure. Verwenden Sie die Purview-Klassifizierungs- und -Beschriftungsberichte, um die Ergebnisse Ihrer Datenüberprüfungen zu analysieren und zu prüfen.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| Ja | Ja | Ja | Ja | Ja | Nein | Eingeschränkt** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

> [!IMPORTANT]
> Der Multi-Cloud Scanning Connector für Azure Purview ist ein separates Add-On zu Azure Purview. Die Geschäftsbedingungen für den Multi-Cloud Scanning Connector für Azure Purview sind in der Vereinbarung enthalten, unter der Sie Microsoft Azure-Dienste erworben haben. Weitere Informationen finden Sie unter „Rechtliche Hinweise zu Microsoft Azure“ (https://azure.microsoft.com/support/legal/ ).
>

## <a name="purview-scope-for-amazon-s3"></a>Purview-Umfang für Amazon S3

Private Erfassungsendpunkte für AWS-Quellen werden derzeit nicht unterstützt.

Weitere Informationen zu Purview-Grenzwerten finden Sie unter:

- [Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Purview](how-to-manage-quotas.md)
- [Unterstützte Datenquellen und Dateitypen in Azure Purview](sources-and-scans.md)

### <a name="storage-and-scanning-regions"></a>Speicher- und Überprüfungsregionen

Der Purview-Connector für den Amazon S3-Dienst wird derzeit nur in bestimmten Regionen bereitgestellt. In der folgenden Tabelle werden die Regionen, in denen Ihre Daten gespeichert sind, der jeweiligen Region zugeordnet, in der sie von Azure Purview überprüft würden.

> [!IMPORTANT]
> Kunden werden alle zugehörigen Datenübertragungsgebühren gemäß der Region Ihres Buckets in Rechnung gestellt.
>

| Speicherregion | Überprüfungsregion |
| ------------------------------- | ------------------------------------- |
| USA, Osten (Ohio)                  | USA, Osten (Ohio)                        |
| USA, Osten (Nord Virginia)           | USA, Osten (Nord Virginia)                       |
| USA, Westen (Nord Kalifornien)         | USA, Westen (Nord Kalifornien)                        |
| USA, Westen (Oregon)                | USA, Westen (Oregon)                      |
| Afrika (Kapstadt)              | Europa (Frankfurt)                    |
| Asien-Pazifik (Hongkong)        | Asien-Pazifik, (Tokio)                |
| Asien-Pazifik (Mumbai)           | Asien-Pazifik, (Singapur)                |
| Asien-Pazifik (Osaka lokal)      | Asien-Pazifik, (Tokio)                 |
| Asien-Pazifik (Seoul)            | Asien-Pazifik, (Tokio)                 |
| Asien-Pazifik, (Singapur)        | Asien-Pazifik, (Singapur)                 |
| Asien-Pazifik, (Sydney)           | Asien-Pazifik, (Sydney)                  |
| Asien-Pazifik, (Tokio)            | Asien-Pazifik, (Tokio)                |
| Kanada (Mitte)                | USA, Osten (Ohio)                        |
| China (Beijing)                 | Nicht unterstützt                    |
| China (Ningxia)                 | Nicht unterstützt                   |
| Europa (Frankfurt)              | Europa (Frankfurt)                    |
| Europa (Irland)                | Europa (Irland)                   |
| Europa (London)                 | Europa (London)                 |
| Europa (Mailand)                  | Europa (Paris)                    |
| Europa (Paris)                  | Europa (Paris)                   |
| Europa (Stockholm)              | Europa (Frankfurt)                    |
| Naher Osten (Bahrain)           | Europa (Frankfurt)                    |
| Südamerika (São Paulo)       | USA, Osten (Ohio)                        |
| | |

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie Ihre Amazon S3-Buckets als Purview-Datenquellen hinzufügen und Ihre S3-Daten überprüfen.

> [!div class="checklist"]
> * Sie müssen ein Azure Purview-Datenquellenadministrator sein.
> * [Erstellen Sie ein Purview-Konto](#create-a-purview-account), falls Sie noch keins haben
> * [Erstellen Sie eine neue AWS-Rolle für die Verwendung mit Purview](#create-a-new-aws-role-for-purview)
> * [Erstellen von Purview-Anmeldeinformationen für Ihre AWS-Bucketüberprüfung](#create-a-purview-credential-for-your-aws-s3-scan)
> * [Konfigurieren Sie die Überprüfung für verschlüsselte Amazon S3-Buckets](#configure-scanning-for-encrypted-amazon-s3-buckets), falls zutreffend
> * Wenn Sie Ihre Buckets als Purview-Ressourcen hinzufügen, benötigen Sie die Werte Ihres [AWS-ARN](#retrieve-your-new-role-arn), den [Bucketnamen](#retrieve-your-amazon-s3-bucket-name) und manchmal Ihre [AWS-Konto-ID](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Erstellen eines Purview-Kontos

- **Wenn Sie bereits über ein Purview-Konto verfügen,** können Sie mit den Konfigurationen fortfahren, die für die AWS S3-Unterstützung erforderlich sind. Beginnen Sie mit dem [Erstellen von Purview-Anmeldeinformationen für Ihre AWS-Bucketüberprüfung](#create-a-purview-credential-for-your-aws-s3-scan).

- **Wenn Sie ein Purview-Konto erstellen müssen,** befolgen Sie die Anweisungen unter [Erstellen einer Azure Purview-Kontoinstanz](create-catalog-portal.md). Nachdem Sie Ihr Konto erstellt haben, kehren Sie hierhin zurück, um die Konfiguration abzuschließen und mit der Verwendung des Purview-Connectors für Amazon S3 zu beginnen.

### <a name="create-a-new-aws-role-for-purview"></a>Erstellen einer neuen AWS-Rolle für Purview

In diesem Verfahren wird beschrieben, wie Sie die Werte für Ihre Azure-Konto-ID und externe ID ermitteln, Ihre AWS-Rolle erstellen und dann den Wert für Ihre Rollen-ARN in Purview eingeben.

**So suchen Sie Ihre Microsoft-Konto-ID und externe ID:**

1. Gehen Sie in Purview zum **Verwaltungszentrum** > **Sicherheit und greifen Sie auf** > **Zugangsdaten** zu.

1. Wählen Sie **Neu** aus, um neue Anmeldeinformationen zu erstellen.

    Wählen Sie im Fenster **Neue Berechtigung**, das rechts erscheint, im Dropdown-Menü **Authentifizierungsmethode** die Option **Rolle ARN**. 
    
    Kopieren Sie dann die angezeigten Werte **Microsoft-Konto-ID** und **Externe ID** in eine separate Datei, oder halten Sie diese bereit, um sie in das entsprechende Feld in AWS einzufügen. Beispiel:

    [ ![Suche nach den Werten der Microsoft-Konto-ID und der externen ID](./media/register-scan-amazon-s3/locate-account-id-external-id.png) ](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**So erstellen Sie Ihre AWS-Rolle für Purview:**

1.  Öffnen Sie Ihre **Amazon Web Services**-Konsole, und wählen Sie unter **Sicherheit, Identität und Compliance** den Eintrag **IAM** aus.

1. Wählen Sie zuerst **Rollen** und dann **Rolle erstellen** aus.

1. Wählen Sie **Ein anderes AWS-Konto** aus, und geben Sie dann die folgenden Werte ein:

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |**Konto-ID**     |    Geben Sie Ihre Microsoft-Konto-ID ein. Beispiel: `181328463391`     |
    |**Externe ID**     |   Wählen Sie unter „Optionen“ **Externe ID anfordern...** aus, und geben Sie dann Ihre externe ID in das angegebene Feld ein. <br>Beispiel: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Beispiel:

    ![Hinzufügen der Microsoft-Konto-ID zu Ihrem AWS-Konto.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. Filtern Sie im Bereich **Rollen > Berechtigungsrichtlinien anfügen** die angezeigten Berechtigungen nach **S3**. Wählen Sie **AmazonS3ReadOnlyAccess** und dann **Weiter: Tags** aus.

    ![Wählen Sie die „ReadOnlyAccess“-Richtlinie für die neue Amazon S3-Überprüfungsrolle aus.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > Die Richtlinie **AmazonS3ReadOnlyAccess** bietet die minimalen Berechtigungen, die für das Scannen der S3-Buckets erforderlich sind, und kann auch andere Berechtigungen enthalten.
    >
    >Um nur die Mindestberechtigungen anzuwenden, die für das Scannen Ihrer Buckets erforderlich sind, erstellen Sie eine neue Richtlinie mit den unter [Mindestberechtigungen für Ihre AWS-Richtlinien](#minimum-permissions-for-your-aws-policy) aufgeführten Berechtigungen, je nachdem, ob Sie einen einzelnen Bucket oder alle Buckets in Ihrem Konto scannen möchten. 
    >
    >Wenden Sie die neue Richtlinie auf die Rolle anstelle von **AmazonS3ReadOnlyAccess an.**

1. Im Bereich **Tags hinzufügen (optional)** können Sie optional auswählen, dass ein aussagekräftiges Tag für diese neue Rolle erstellt werden soll. Nützliche Tags ermöglichen es Ihnen, den Zugriff für jede von Ihnen erstellte Rolle zu organisieren, nachzuverfolgen und zu steuern.

    Geben Sie nach Bedarf einen neuen Schlüssel und Wert für Ihr Tag ein. Wenn Sie fertig sind, oder wenn Sie diesen Schritt überspringen möchten, wählen Sie **Weiter: Überprüfen** aus, um die Rollendetails zu überprüfen und die Rollenerstellung abzuschließen.

    ![Fügen Sie ein aussagekräftiges Tag hinzu, um den Zugriff für Ihre neue Rolle zu organisieren, nachzuverfolgen oder zu steuern.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Gehen Sie im Bereich **Überprüfen** wie folgt vor:

    - Geben Sie im Feld **Rollenname** einen aussagekräftigen Namen für Ihre Rolle ein.
    - Geben Sie im Feld **Rollenbeschreibung** eine optionale Beschreibung ein, um den Zweck der Rolle zu bezeichnen.
    - Vergewissern Sie sich im Abschnitt **Richtlinien**, dass die richtige Richtlinie (**AmazonS3ReadOnlyAccess**) an die Rolle angefügt ist.

    Wählen Sie dann **Rolle erstellen** aus, um den Prozess abzuschließen.

    Beispiel:

    ![Überprüfen von Details vor dem Erstellen Ihrer Rolle.](./media/register-scan-amazon-s3/review-role.png)


### <a name="create-a-purview-credential-for-your-aws-s3-scan"></a>Erstellen von Purview-Anmeldeinformationen für Ihre AWS S3-Überprüfung

In diesem Verfahren wird beschrieben, wie Sie neue Purview-Anmeldeinformationen erstellen, die beim Überprüfen Ihrer AWS-Buckets verwendet werden sollen.

> [!TIP]
> Wenn Sie direkt von [Erstellen einer neuen AWS-Rolle für Purview](#create-a-new-aws-role-for-purview) aus fortfahren, ist der Bereich **Neue Anmeldeinformationen** möglicherweise bereits in Purview geöffnet.
>
> Sie können im Verlauf des Prozesses auch neue Anmeldeinformationen erstellen, während Sie [Ihre Überprüfung konfigurieren](#create-a-scan-for-one-or-more-amazon-s3-buckets). Wählen Sie in diesem Fall im Feld **Anmeldeinformationen** die Option **Neu** aus.
>

1. Gehen Sie in Purview zum **Verwaltungszentrum**, und wählen Sie unter **Sicherheit und Zugriff** die Option **Zugangsdaten**.

1. Wählen Sie **Neu** aus, und verwenden Sie im rechts angezeigten Bereich **Neue Anmeldeinformationen** die folgenden Felder, um Ihre Purview-Anmeldeinformationen zu erstellen:

    |Feld |BESCHREIBUNG  |
    |---------|---------|
    |**Name**     |Geben Sie einen aussagekräftigen Namen für diese Anmeldeinformationen ein.        |
    |**Beschreibung**     |Geben Sie eine optionale Beschreibung für diese Anmeldeinformationen ein, z. B. `Used to scan the tutorial S3 buckets` (Zum Überprüfen der Tutorial-S3-Buckets).         |
    |**Authentifizierungsmethode**     |Wählen Sie **Rollen-ARN** aus, da Sie für den Zugriff auf Ihren Bucket einen Rollen-ARN verwenden.         |
    |**Rollen-ARN**     | Nachdem Sie [Ihre Amazon IAM-Rolle erstellt haben](#create-a-new-aws-role-for-purview), navigieren Sie im AWS IAM-Bereich zu Ihrer Rolle, kopieren Sie den **Rollen-ARN**-Wert, und geben Sie ihn hier ein. Beispiel: `arn:aws:iam::181328463391:role/S3Role`. <br><br>Weitere Informationen finden Sie unter [Abrufen Ihres neuen Rollen-ARN](#retrieve-your-new-role-arn). |
    | | |
    
    Die **Microsoft-Konto-ID** und die **Externe ID** werden bei der [Erstellung Ihres Rollen-ARN in AWS verwendet.](#create-a-new-aws-role-for-purview).

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind, um die Erstellung der Anmeldeinformationen abzuschließen.

Weitere Informationen zu Purview-Anmeldeinformationen finden Sie unter [Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview](manage-credentials.md).


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Konfigurieren der Überprüfung verschlüsselter Amazon S3-Buckets

AWS-Buckets unterstützen mehrere Verschlüsselungstypen. Buckets, die **AWS-KMS**-Verschlüsselung verwenden, ist eine spezielle Konfiguration erforderlich, um die Überprüfung zu ermöglichen.

> [!NOTE]
> Für Buckets, die keine Verschlüsselung bzw. AES-256- oder AWS-KMS S3-Verschlüsselung verwenden, überspringen Sie diesen Abschnitt, und fahren Sie mit dem [Abrufen Ihres Amazon S3-Bucketnamens](#retrieve-your-amazon-s3-bucket-name) fort.
>

**So überprüfen Sie den Verschlüsselungstyp, der in Ihren Amazon S3-Buckets verwendet wird**

1. Navigieren Sie in AWS zu **Speicher** > **S3**, und wählen Sie links im Menü **Buckets** aus.

    ![Wählen Sie die Registerkarte „Amazon S3-Buckets“ aus.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Wählen Sie den Bucket aus, den Sie überprüfen möchten. Wählen Sie auf der Seite mit den Details des Buckets die Registerkarte **Eigenschaften** aus, und scrollen Sie nach unten zum Bereich **Standardverschlüsselung**.

    - Wenn der von Ihnen ausgewählte Bucket für eine andere Verschlüsselung als **AWS-KMS** konfiguriert ist, und auch, wenn die Standardverschlüsselung für Ihren Bucket **deaktiviert** ist, überspringen Sie den Rest dieses Verfahrens, und fahren Sie mit dem [Abrufen Ihres Amazon S3-Bucketnamens](#retrieve-your-amazon-s3-bucket-name) fort.

    - Wenn der von Ihnen ausgewählte Bucket für **AWS-KMS**-Verschlüsselung konfiguriert ist, fahren Sie wie unten beschrieben fort, um eine neue Richtlinie hinzuzufügen, die das Überprüfen eines Bucket mit benutzerdefinierter **AWS-KMS**-Verschlüsselung zulässt.

    Beispiel:

    ![Anzeigen eines mit AWS-KMS-Verschlüsselung konfigurierten Amazon S3-Buckets](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**So fügen Sie eine neue Richtlinie hinzu, die das Überprüfen eines Buckets mit benutzerdefinierter AWS-KMS-Verschlüsselung zulässt**

1. Navigieren Sie in AWS zu **Dienste** >  **IAM** >  **Richtlinien**, und wählen Sie **Richtlinie erstellen** aus.

1. Definieren Sie auf der Registerkarte **Richtlinie erstellen** > **Visueller Editor** Ihre Richtlinie mit den folgenden Werten:

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |**Service**     |  Geben Sie **KMS** ein, und wählen Sie den Eintrag aus.       |
    |**Aktionen**     | Wählen Sie unter **Zugriffsebene** die Option **Schreiben** aus, um den Abschnitt **Schreiben** zu erweitern.<br>Sobald dieser erweitert ist, wählen Sie nur die Option **Entschlüsseln** aus.        |
    |**Ressourcen**     |Wählen Sie eine bestimmte Ressource oder **Alle Ressourcen** aus.         |
    | | |

    Wenn Sie fertig sind, wählen Sie **Richtlinie überprüfen** aus, um fortzufahren.

    ![Erstellen Sie eine Richtlinie zum Überprüfen eines Buckets mit AWS-KMS-Verschlüsselung.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Geben Sie auf der Seite **Richtlinie überprüfen** einen aussagekräftigen Namen für die Richtlinie und eine optionale Beschreibung ein, und wählen Sie dann **Richtlinie erstellen** aus.

    Die neu erstellte Richtlinie wird Ihrer Liste mit Richtlinien hinzugefügt.

1. Fügen Sie Ihre neue Richtlinie an die Rolle an, die Sie zum Überprüfen hinzugefügt haben.

    1. Navigieren Sie zurück zur Seite **IAM** > **Rollen**, und wählen Sie die Rolle aus, die Sie [zuvor](#create-a-new-aws-role-for-purview) hinzugefügt hatten.

    1. Wählen Sie auf der Registerkarte **Berechtigungen** die Option **Richtlinien anfügen** aus.

        ![Wählen Sie auf der Registerkarte „Berechtigungen“ Ihrer Rolle „Richtlinien anfügen“ aus.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Suchen Sie auf der Seite **Berechtigungen anfügen** nach der oben erstellten neuen Richtlinie, und wählen Sie diese aus. Wählen Sie **Richtlinie anfügen** aus, um der Rolle Ihre Richtlinie anzufügen.

        Die Seite **Zusammenfassung** wird aktualisiert, und Ihre neue Richtlinie ist Ihrer Rolle angefügt.

        ![Anzeigen einer aktualisierten Zusammenfassungsseite mit der neuen Richtlinie, die an Ihre Rolle angefügt ist.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Abrufen Ihres neuen Rollen-ARN

Sie müssen Ihren AWS-Rollen-ARN aufzeichnen und in Purview kopieren, wenn Sie [eine Überprüfung für Ihren Amazon S3-Bucket erstellen](#create-a-scan-for-one-or-more-amazon-s3-buckets).

**So rufen Sie Ihren Rollen-ARN ab**

1. Suchen Sie im AWS-Bereich **Identity & Access Management (IAM)**  > **Rollen** nach der neuen Rolle, die Sie [für Purview erstellt haben](#create-a-purview-credential-for-your-aws-s3-scan), und wählen Sie sie aus.

1. Wählen Sie auf der Seite **Zusammenfassung** der Rolle rechts neben dem **Rollen-ARN**-Wert die Schaltfläche **In Zwischenablage kopieren** aus.

    ![Kopieren Sie den Rollen-ARN-Wert in die Zwischenablage.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

In Purview können Sie Ihre Anmeldeinformationen für AWS S3 bearbeiten und die abgerufene Rolle in das Feld **Rollen-ARN** einfügen. Weitere Informationen finden Sie unter [Erstellen einer Überprüfung für einen oder mehrere Amazon S3-Buckets](#create-a-scan-for-one-or-more-amazon-s3-buckets).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Abrufen Ihres Amazon S3-Bucketnamens

Sie benötigen den Namen Ihres Amazon S3-Buckets, um ihn in Purview zu kopieren, wenn Sie [eine Überprüfung für Ihren Amazon S3-Bucket erstellen](#create-a-scan-for-one-or-more-amazon-s3-buckets).

**So rufen Sie Ihren Bucketnamen ab**

1. Navigieren Sie in AWS zu **Speicher** > **S3**, und wählen Sie links im Menü **Buckets** aus.

    ![Zeigen Sie die Registerkarte „Amazon S3-Buckets“ an.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Suchen Sie nach Ihrem Bucket, und wählen Sie ihn aus, um die Seite mit den Details des Buckets anzuzeigen, und kopieren Sie dann den Bucketnamen in die Zwischenablage.

    Beispiel:

    ![Abrufen und Kopieren der S3-Bucket-URL.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Fügen Sie Ihren Bucketnamen in eine sichere Datei ein, und fügen Sie ein `s3://`-Präfix hinzu, um den Wert zu erstellen, den Sie eingeben müssen, wenn Sie Ihren Bucket als Purview-Ressource konfigurieren.

    Beispiel: `s3://purview-tutorial-bucket`

> [!TIP]
> Nur die Stammebene Ihres Buckets wird als Purview-Datenquelle unterstützt. Die folgende URL, die einen Unterordner enthält, wird beispielsweise *nicht* unterstützt: `s3://purview-tutorial-bucket/view-data`
>
> Wenn Sie jedoch eine Überprüfung für einen bestimmten S3-Bucket konfigurieren, können Sie einen oder mehrere bestimmte Ordner für die Überprüfung auswählen. Weitere Informationen finden Sie im Schritt zum [Festlegen des Bereichs für Ihre Überprüfung](#create-a-scan-for-one-or-more-amazon-s3-buckets).
>

### <a name="locate-your-aws-account-id"></a>Auffinden Ihrer AWS-Konto-ID

Sie benötigen Ihre AWS-Konto-ID, um Ihr AWS-Konto als Purview-Datenquelle zu registrieren, zusammen mit allen zugehörigen Buckets.

Ihre AWS-Konto-ID ist die ID, die Sie zum Anmelden bei der AWS-Konsole verwenden. Sie finden sie auch, sobald Sie beim IAM-Dashboard angemeldet sind, im linken Bereich unter den Navigationsoptionen sowie am oberen Rand als numerischer Teil Ihrer Anmelde-URL:

Beispiel:

![Abrufen Ihrer AWS-Konto-ID.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Hinzufügen eines einzelnen Amazon S3-Buckets als Purview-Ressource

Verwenden Sie dieses Verfahren, wenn Sie nur über einen einzigen S3-Bucket verfügen, den Sie als Datenquelle bei Purview registrieren möchten, oder wenn Sie über mehrere Buckets in Ihrem AWS-Konto verfügen, aber nicht alle bei Purview registrieren möchten.

**So fügen Sie Ihren Bucket hinzu:**

1. Gehen Sie in Azure Purview auf die Seite **Data Map** und wählen Sie **Registrieren** ![Symbol registrieren.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3** > **Weiter**.

    ![Fügen Sie einen Amazon AWS-Bucket als Purview-Datenquelle hinzu.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Wenn Sie mehrere [Sammlungen](manage-data-sources.md#manage-collections) haben und Ihr Amazon S3 zu einer bestimmten Sammlung hinzufügen möchten, wählen Sie oben rechts die **Zuordnungsansicht** aus, und wählen Sie dann **Registrieren** ![Symbol „Registrieren“](./media/register-scan-amazon-s3/register-button.png) aus. Schaltfläche in Ihrer Sammlung.
    >

1. Geben Sie im Bereich **Quellen registrieren (Amazon S3)** , der geöffnet wird, die folgenden Details ein:

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |**Name**     |Geben Sie einen aussagekräftigen Namen ein, oder verwenden Sie den bereitgestellten Standardwert.         |
    |**Bucket-URL**     | Geben Sie Ihre AWS-Bucket-URL mit der folgenden Syntax ein: `s3://<bucketName>`     <br><br>**Hinweis**: Achten Sie darauf, dass Sie nur die Stammebene Ihres Buckets verwenden. Weitere Informationen finden Sie unter [Abrufen Ihres Amazon S3-Bucketnamens](#retrieve-your-amazon-s3-bucket-name). |
    |**Auswählen einer Sammlung** |Wenn Sie sich für die Registrierung einer Datenquelle aus einer Sammlung heraus entschieden haben, wird diese Sammlung bereits aufgeführt. <br><br>Wählen Sie nach Bedarf eine andere Sammlung aus, **Keine**, um keine Sammlung zuzuweisen, oder **Neu**, um jetzt eine neue Sammlung zu erstellen. <br><br>Weitere Informationen zu Purview-Sammlungen finden Sie unter [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md#manage-collections).|
    | | |

    Wenn Sie fertig sind, wählen Sie **Fertig stellen** aus, um die Registrierung abzuschließen.

Fahren Sie fort mit [Erstellen einer Überprüfung für einen oder mehrere Amazon S3-Buckets](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-aws-account-as-a-purview-resource"></a>Hinzufügen eines AWS-Kontos als Purview-Ressource

Verwenden Sie dieses Verfahren, wenn Sie über mehrere S3-Buckets in Ihrem Amazon-Konto verfügen und alle als Purview-Datenquellen registrieren möchten.

Wenn Sie [Ihre Überprüfung konfigurieren](#create-a-scan-for-one-or-more-amazon-s3-buckets), können Sie die bestimmten zu überprüfenden Buckets auswählen, wenn Sie nicht alle zusammen überprüfen möchten.

**So fügen Sie Ihr Amazon-Konto hinzu:**

1. Gehen Sie in Azure Purview auf die Seite **Data Map** und wählen Sie **Registrieren** ![Symbol registrieren.](./media/register-scan-amazon-s3/register-button.png) > **Amazon-Konten** > **Weiter**.

    ![Fügen Sie ein Amazon-Konto als Purview-Datenquelle hinzu.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Wenn Sie mehrere [Sammlungen](manage-data-sources.md#manage-collections) haben und Ihr Amazon S3 zu einer bestimmten Sammlung hinzufügen möchten, wählen Sie oben rechts die **Zuordnungsansicht** aus, und wählen Sie dann **Registrieren** ![Symbol „Registrieren“](./media/register-scan-amazon-s3/register-button.png) aus. Schaltfläche in Ihrer Sammlung.
    >

1. Geben Sie im Bereich **Quellen registrieren (Amazon S3)** , der geöffnet wird, die folgenden Details ein:

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |**Name**     |Geben Sie einen aussagekräftigen Namen ein, oder verwenden Sie den bereitgestellten Standardwert.         |
    |**AWS-Konto-ID**     | Geben Sie Ihre AWS-Konto-ID ein. Weitere Informationen finden Sie unter [Auffinden Ihrer AWS-Konto-ID](#locate-your-aws-account-id).|
    |**Auswählen einer Sammlung** |Wenn Sie sich für die Registrierung einer Datenquelle aus einer Sammlung heraus entschieden haben, wird diese Sammlung bereits aufgeführt. <br><br>Wählen Sie nach Bedarf eine andere Sammlung aus, **Keine**, um keine Sammlung zuzuweisen, oder **Neu**, um jetzt eine neue Sammlung zu erstellen. <br><br>Weitere Informationen zu Purview-Sammlungen finden Sie unter [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md#manage-collections).|
    | | |

    Wenn Sie fertig sind, wählen Sie **Fertig stellen** aus, um die Registrierung abzuschließen.

Fahren Sie fort mit [Erstellen einer Überprüfung für einen oder mehrere Amazon S3-Buckets](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Erstellen einer Überprüfung für einen oder mehrere Amazon S3-Buckets

Nachdem Sie Ihre Buckets als Purview-Datenquellen hinzugefügt haben, können Sie eine Überprüfung so konfigurieren, dass sie in geplanten Intervallen oder sofort ausgeführt wird.

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Datenzuordnung** aus, und führen Sie dann einen der folgenden Schritte aus:

    - Wählen Sie in der **Zuordnungsansicht** die Option **Neue Überprüfung** ![Symbol „Neue Überprüfung“](./media/register-scan-amazon-s3/new-scan-button.png) aus. in Ihrem Feld „Datenquelle“.
    - Zeigen Sie in der **Listenansicht** mit dem Mauszeiger auf die Zeile für Ihre Datenquelle, und wählen Sie **Neue Überprüfung** ![ Symbol „Neue Überprüfung“](./media/register-scan-amazon-s3/new-scan-button.png) aus.

1. Definieren Sie im rechts angezeigten Bereich **Überprüfen** die folgenden Felder, und wählen Sie dann **Weiter** aus:

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |**Name**     |  Geben Sie einen aussagekräftigen Namen für Ihre Überprüfung ein, oder verwenden Sie den Standardwert.       |
    |**Type** |Wird nur angezeigt, wenn Sie Ihr AWS-Konto hinzugefügt haben, einschließlich aller Buckets. <br><br>Die aktuellen Optionen umfassen nur **Alle** > **Amazon S3**. Bleiben Sie dran, um über weitere auswählbare Optionen auf dem Laufenden zu bleiben, die mit der sich erweiternden Unterstützungsmatrix von Purview entwickelt werden. |
    |**Credential**     |  Wählen Sie Purview-Anmeldeinformation mit Ihrem Rollen-ARN aus. <br><br>**Tipp**: Wenn Sie zu diesem Zeitpunkt neue Anmeldeinformationen erstellen möchten, wählen Sie **Neu** aus. Weitere Informationen finden Sie unter [Erstellen von Purview-Anmeldeinformationen für Ihre AWS-Bucketüberprüfung](#create-a-purview-credential-for-your-aws-s3-scan).     |
    | **Amazon S3**    |   Wird nur angezeigt, wenn Sie Ihr AWS-Konto hinzugefügt haben, einschließlich aller Buckets. <br><br>Wählen Sie mindestens einen zu überprüfenden Bucket aus, oder klicken Sie auf **Alle auswählen**, um alle Buckets in Ihrem Konto zu überprüfen.      |
    | | |

    Purview überprüft automatisch, ob der Rollen-ARN gültig ist und ob die Buckets und Objekte in den Buckets zugänglich sind, und fährt dann fort, wenn die Verbindung erfolgreich hergestellt wird.

    > [!TIP]
    > Um andere Werte einzugeben und selbst die Verbindung zu testen, bevor Sie fortfahren, wählen Sie unten rechts **Verbindung testen** aus, bevor Sie **Weiter** auswählen.
    >

1. <a name="scope-your-scan"></a>Wählen Sie im Bereich **Festlegen des Bereichs für Ihre Überprüfung** die jeweiligen Buckets oder Ordner aus, die in der Überprüfung enthalten sein sollen.

    Wenn Sie eine Überprüfung für ein ganzes AWS-Konto erstellen, können Sie bestimmte Buckets für die Überprüfung auswählen. Wenn Sie eine Überprüfung für ein bestimmtes AWS S3-Konto erstellen, können Sie bestimmte Ordner für die Überprüfung auswählen.

1. Wählen Sie im Bereich **Überprüfungsregelsatz auswählen** entweder den Standardregelsatz **AmazonS3** aus, oder wählen Sie **Neuer Überprüfungsregelsatz** aus, um einen neuen benutzerdefinierten Regelsatz zu erstellen. Nachdem Sie Ihren Regelsatz ausgewählt haben, wählen Sie **Weiter** aus.

    Wenn Sie sich dafür entscheiden, einen neuen benutzerdefinierten Überprüfungsregelsatz zu erstellen, verwenden Sie den Assistenten, um die folgenden Einstellungen zu definieren:

    |Bereich  |BESCHREIBUNG  |
    |---------|---------|
    |**Neuer Überprüfungsregelsatz** /<br>**Beschreibung der Überprüfungsregel**    |   Geben Sie einen aussagekräftigen Namen und eine optionale Beschreibung für Ihren Regelsatz ein.      |
    |**Dateitypen auswählen**     | Wählen Sie alle Dateitypen aus, die Sie in die Überprüfung einschließen möchten, und wählen Sie dann **Weiter** aus.<br><br>Um einen neuen Dateityp hinzuzufügen, wählen Sie **Neuer Dateityp** aus, und definieren Sie Folgendes: <br>– Die Dateierweiterung, die Sie hinzufügen möchten. <br>– Eine optionale Beschreibung.  <br>– Ob der Dateiinhalt ein benutzerdefiniertes Trennzeichen besitzt oder ein Systemdateityp ist. Geben Sie dann Ihr benutzerdefiniertes Trennzeichen ein, oder wählen Sie Ihren Systemdateityp aus. <br><br>Wählen Sie **Erstellen** aus, um Ihren benutzerdefinierten Dateityp zu erstellen.     |
    |**Klassifizierungsregeln auswählen**     |   Navigieren Sie zu den Klassifizierungsregeln, die Sie für Ihr Dataset ausführen möchten, und wählen Sie sie aus.      |
    |     |         |

    Wählen Sie **Erstellen** aus, wenn Sie fertig sind, um Ihren Regelsatz zu erstellen.

1. Wählen Sie im Bereich **Überprüfungstrigger festlegen** eine der folgenden Optionen aus, und wählen Sie dann **Weiter** aus:

    - **Serie**, um einen Zeitplan für eine wiederkehrende Überprüfung zu konfigurieren.
    - **Einmalig**, um eine Überprüfung zu konfigurieren, die sofort startet.

1. Überprüfen Sie im Bereich **Überprüfen Ihrer Überprüfung** die Details Ihrer Überprüfung, um zu bestätigen, dass sie korrekt sind, und wählen Sie dann **Speichern** oder **Speichern und ausführen** aus, wenn Sie im vorherigen Bereich **Einmalig** ausgewählt haben.

    > [!NOTE]
    > Sobald die Überprüfung gestartet wurde, kann es bis zu 24 Stunden bis zu deren Abschluss dauern. Sie können 24 Stunden nach dem Start der jeweiligen Überprüfung Ihre **Erkenntnisberichte** überprüfen und den Katalog durchsuchen.
    >

Weitere Informationen finden Sie unter [Erkunden der Ergebnisse der Purview-Überprüfung](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Erkunden der Ergebnisse der Purview-Überprüfung

Wenn eine Purview-Überprüfung Ihrer Amazon S3-Buckets abgeschlossen ist, führen Sie ein Drilldown in den Purview-Bereich **Datenzuordnung** aus, um den Überprüfungsverlauf anzuzeigen.

Wählen Sie eine Datenquelle aus, um deren Details anzuzeigen, und wählen Sie dann die Registerkarte **Überprüfungen** aus, um alle zurzeit ausgeführten oder abgeschlossenen Überprüfungen anzuzeigen.
Wenn Sie ein AWS-Konto mit mehreren Buckets hinzugefügt haben, wird der Überprüfungsverlauf für jeden Bucket unter dem Konto angezeigt.

Beispiel:

![Anzeigen der AWS S3-Bucketüberprüfungen unter Ihrer AWS-Kontoquelle.](./media/register-scan-amazon-s3/account-scan-history.png)

Verwenden Sie die anderen Bereiche von Purview, um Details zu den Inhalten in Ihrem Datenbestand zu ermitteln, einschließlich Ihrer Amazon S3-Buckets:

- **Durchsuchen Sie den Purview-Datenkatalog,** und filtern Sie nach einem bestimmten Bucket. Beispiel:

    ![Durchsuchen des Katalogs nach AWS S3-Objekten.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Zeigen Sie Erkenntnisberichte an**, um Statistiken für die Klassifizierung, Vertraulichkeitsbezeichnungen, Dateitypen sowie weitere Details zu Ihrem Inhalt anzuzeigen.

    Alle Erkenntnisberichte von Purview enthalten die Amazon S3-Überprüfungsergebnisse, zusammen mit dem Rest der Ergebnisse aus Ihren Azure-Datenquellen. Wenn dies relevant ist, wurde ein zusätzlicher **Amazon S3**-Ressourcentyp zu den Berichtfilteroptionen hinzugefügt.

    Weitere Informationen finden Sie unter [Grundlegendes zu Erkenntnissen in Azure Purview](concept-insights.md).

## <a name="minimum-permissions-for-your-aws-policy"></a>Mindestberechtigungen für Ihre AWS-Richtlinie

Das Standardverfahren zum [Erstellen einer AWS-Rolle für Purview](#create-a-new-aws-role-for-purview) zur Verwendung beim Scannen der S3-Buckets, ist die **AmazonS3ReadOnlyAccess** -Richtlinie.

Die Richtlinie **AmazonS3ReadOnlyAccess** bietet die minimalen Berechtigungen, die für das Scannen der S3-Buckets erforderlich sind, und kann auch andere Berechtigungen enthalten.

Um nur die Mindestberechtigungen anzuwenden, die für das Scannen Ihrer Buckets erforderlich sind, erstellen Sie eine neue Richtlinie mit den in den folgenden Abschnitten aufgeführten Berechtigungen, je nachdem, ob Sie einen einzelnen Bucket oder alle Buckets in Ihrem Konto scannen möchten.

Wenden Sie die neue Richtlinie auf die Rolle anstelle von **AmazonS3ReadOnlyAccess an.**

### <a name="individual-buckets"></a>Einzelne Buckets

Bei der Überprüfung einzelner S3-Buckets umfassen die AWS-Mindestberechtigungen:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Stellen Sie sicher, dass Sie die Ressource mit dem jeweiligen Bucket-Namen definieren. Beispiel:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Alle Buckets in Ihrem Konto

Wenn Sie alle Bucket in Ihrem AWS-Konto scannen, umfassen die minimalen AWS-Berechtigungen:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Stellen Sie sicher, dass Sie die Ressource mit einem Platzhalter definieren. Beispiel:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Erkenntnisberichten in Azure Purview:

> [!div class="nextstepaction"]
> [Grundlegendes zu Erkenntnissen in Azure Purview](concept-insights.md)
