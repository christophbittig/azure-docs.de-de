---
title: Amazon RDS Multi-Cloud-Scan-Verbindung für Azure Purview
description: Diese Anleitung beschreibt Details zum Scannen von Amazon RDS-Datenbanken, einschließlich Microsoft SQL- und PostgreSQL-Daten.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: eb8140fa1d4c9328bf69fb84b867255876a235c6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021496"
---
# <a name="amazon-rds-multi-cloud-scanning-connector-for-azure-purview-public-preview"></a>Amazon RDS Multi-Cloud-Scan-Verbindung für Azure Purview (öffentliche Vorschau)

Mit der Multi-Cloud-Scan-Verbindung für Azure Purview können Sie Ihre Unternehmensdaten zusätzlich zu Azure-Speicherdiensten über Cloud-Anbieter hinweg untersuchen, einschließlich Amazon Web Services.

In diesem Artikel wird beschrieben, wie Sie mit Azure Purview Ihre derzeit in Amazon RDS gespeicherten strukturierten Daten scannen, einschließlich Microsoft SQL- und PostgreSQL-Datenbanken, und ermitteln, welche Arten vertraulicher Informationen in Ihren Daten vorhanden sind. Außerdem erfahren Sie, wie Sie die Amazon RDS-Datenbanken identifizieren, in denen die Daten derzeit gespeichert sind, um den Datenschutz und die Datenkonformität zu vereinfachen.

Verwenden Sie für diesen Service Purview, um ein Microsoft-Konto mit sicherem Zugriff auf AWS bereitzustellen, auf dem die Multi-Cloud-Scan-Verbindungen für Azure Purview ausgeführt werden. Die Multi-Cloud-Scan-Verbindungen für Azure Purview verwenden diesen Zugriff auf Ihre Amazon RDS-Datenbanken, um Ihre Daten zu lesen, und melden dann die Scanergebnisse, einschließlich nur der Metadaten und der Klassifizierung, an Azure zurück. Verwenden Sie die Purview-Klassifizierungs- und -Beschriftungsberichte, um die Ergebnisse Ihrer Datenüberprüfungen zu analysieren und zu prüfen.

> [!IMPORTANT]
> Die Multi-Cloud-Scan-Verbindungen für Azure Purview sind separate Add-Ons für Azure Purview. Die Bedingungen für die Multi-Cloud-Scan-Verbindungen für Azure Purview sind in der Vereinbarung enthalten, unter der Sie Microsoft Azure-Dienste erworben haben. Weitere Informationen finden Sie unter „Rechtliche Hinweise zu Microsoft Azure“ (https://azure.microsoft.com/support/legal/ ).
>

> [!IMPORTANT]
> Purview-Unterstützung für Amazon RDS befindet sich derzeit in der VORSCHAU. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="purview-scope-for-amazon-rds"></a>Umfang von Purview für Amazon RDS

- **Unterstützte Datenbank-Engines**: Der strukturierte Amazon RDS-Datenspeicher unterstützt mehrere Datenbank-Engines. Azure Purview unterstützt Amazon RDS mit/basierend auf Microsoft SQL und PostgreSQL.

- **Maximal unterstützte Spalten**: Das Scannen von RDS-Tabellen mit mehr als 300 Spalten wird nicht unterstützt.

- **Unterstützung des öffentlichen Zugriffs**: Purview unterstützt das Scannen nur mit VPC Private Link in AWS und umfasst kein Scannen mit öffentlichem Zugriff.

- **Unterstützte Regionen**: Purview unterstützt nur Amazon RDS-Datenbanken, die sich in den folgenden AWS-Regionen befinden:

    - USA, Osten (Ohio)
    - USA, Osten (Nord Virginia)
    - USA, Westen (Nord Kalifornien)
    - USA, Westen (Oregon)
    - Europa (Frankfurt)
    - Asien-Pazifik, (Tokio)
    - Asien-Pazifik, (Singapur)
    - Asien-Pazifik, (Sydney)
    - Europa (Irland)
    - Europa (London)
    - Europa (Paris)

- **IP-Adressanforderungen**: Ihre RDS-Datenbank muss eine statische IP-Adresse haben. Die statische IP-Adresse wird verwendet, um AWS PrivateLink zu konfigurieren, wie in diesem Artikel beschrieben.

- **Bekannte Probleme**: Die folgenden Funktionen werden derzeit nicht unterstützt:

    - Die Schaltfläche **Verbindung testen**. Die Scan-Statusmeldungen zeigen alle Fehler im Zusammenhang mit dem Verbindungsaufbau an.
    - Auswählen bestimmter Tabellen in Ihrer Datenbank zum Scannen.
    - [Datenherkunft](concept-data-lineage.md).

Weitere Informationen finden Sie unter:

- [Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Purview](how-to-manage-quotas.md)
- [Unterstützte Datenquellen und Dateitypen in Azure Purview](sources-and-scans.md)
- [Verwenden privater Endpunkte für Ihr Purview-Konto](catalog-private-link.md)

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die folgenden Voraussetzungen erfüllt haben, bevor Sie Ihre Amazon RDS-Datenbank als Purview-Datenquellen hinzufügen und Ihre RDS-Daten scannen.

> [!div class="checklist"]
> * Sie müssen ein Azure Purview-Datenquellenadministrator sein.
> * Sie benötigen ein Purview-Konto. [Erstellen Sie eine Azure Purview-Kontoinstanz](create-catalog-portal.md), falls Sie noch keine haben.
> * Sie benötigen eine Amazon RDS PostgreSQL- oder Microsoft SQL-Datenbank mit Daten.


## <a name="configure-aws-to-allow-purview-to-connect-to-your-rds-vpc"></a>Konfigurieren Sie AWS so, dass Purview eine Verbindung zu Ihrer RDS-VPC herstellen kann

Azure Purview unterstützt das Scannen nur, wenn Ihre Datenbank in einer Virtual Private Cloud (VPC) gehostet wird, in der nur von derselben VPC aus auf Ihre RDS-Datenbank zugegriffen werden kann.

Die Azure Multi-Cloud-Scan-Verbindungen für den Azure Purview-Dienst werden in einem separaten Microsoft-Konto in AWS ausgeführt. Um Ihre RDS-Datenbanken zu scannen, muss das Microsoft AWS-Konto auf Ihre RDS-Datenbanken in Ihrer VPC zugreifen können. Um diesen Zugriff zu ermöglichen, müssen Sie [AWS PrivateLink](https://aws.amazon.com/privatelink/) zwischen der RDS-VPC (im Kundenkonto) und der VPC konfigurieren, auf der die Multi-Cloud-Scan-Verbindungen für Azure Purview ausgeführt werden (im Microsoft-Konto).

Das folgende Diagramm zeigt die Komponenten sowohl in Ihrem Kundenkonto als auch in Ihrem Microsoft-Konto. Gelb hervorgehoben sind die Komponenten, die Sie erstellen müssen, um die Konnektivität der RDS-VPC in Ihrem Konto mit der VPC zu aktivieren, in der die Multi-Cloud-Scan-Verbindungen für Azure Purview im Microsoft-Konto ausgeführt werden.

:::image type="content" source="media/register-scan-amazon-rds/vpc-architecture.png" alt-text="Diagramm der Multi-Cloud-Scan-Verbindungen für den Azure Purview-Dienst in einer VPC-Architektur." border="false":::


> [!IMPORTANT]
> Alle AWS-Ressourcen, die für das private Netzwerk eines Kunden erstellt werden, verursachen zusätzliche Kosten auf der AWS-Rechnung des Kunden.
>

### <a name="configure-aws-privatelink-using-a-cloudformation-template"></a>Konfigurieren Sie AWS PrivateLink mit einer CloudFormation-Vorlage

Das folgende Verfahren beschreibt, wie Sie eine AWS CloudFormation-Vorlage verwenden, um AWS PrivateLink zu konfigurieren, damit Purview eine Verbindung zu Ihrer RDS-VPC herstellen kann. Dieses Verfahren wird in AWS ausgeführt und ist für einen AWS-Administrator bestimmt.

Diese CloudFormation-Vorlage steht im [Azure GitHub-Repository](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS) zum Download zur Verfügung und hilft Ihnen beim Erstellen einer Zielgruppe, eines Load-Balancers und eines Endpunktdienstes.

- **Wenn Sie mehrere RDS-Server in derselben VPC haben**, führen Sie dieses Verfahren einmal aus, indem Sie [alle RDS-Server-IP-Adressen und -Ports angeben](#parameters). In diesem Fall enthält die CloudFormation-Ausgabe unterschiedliche Ports für jeden RDS-Server.

    Verwenden Sie beim [Registrieren dieser RDS-Server als Datenquellen in Purview](#register-an-amazon-rds-data-source) die in der Ausgabe enthaltenen Ports anstelle der echten RDS-Server-Ports.

- **Wenn Sie RDS-Server in mehreren VPCs haben**, führen Sie dieses Verfahren für jede der VPCs aus.


> [!TIP]
> Sie können dieses Verfahren auch manuell durchführen. Weitere Informationen finden Sie unter [AWS PrivateLink manuell konfigurieren (erweitert)](#configure-aws-privatelink-manually-advanced).
>

**So bereiten Sie Ihre RDS-Datenbank mit einer CloudFormation-Vorlage vor**:

1. Laden Sie die für dieses Verfahren erforderliche CloudFormation-Vorlage [RDSPrvateLink_CloudFormation.yaml](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS) aus dem Azure GitHub-Repository herunter:

    1. Wählen Sie rechts auf der [verknüpften GitHub-Seite](https://github.com/Azure/Azure-Purview-Starter-Kit/blob/main/Amazon/AWS/RDS/Amazon_AWS_RDS_PrivateLink_CloudFormation.zip) **Herunterladen** aus, um die ZIP-Datei herunterzuladen.

    1. Extrahieren Sie die ZIP-Datei an einen lokalen Speicherort, damit Sie auf die Datei **RDSPrvateLink_CloudFormation.yaml** zugreifen können.

1. Navigieren Sie im AWS-Portal zum **CloudFormation**-Service. Wählen Sie rechts oben auf der Seite **Stapel erstellen** > **Mit neuen Ressourcen (Standard)** aus.

1. Wählen Sie auf der Seite **Voraussetzung – Vorlage vorbereiten** die Option **Vorlage ist bereit** aus.

1. Wählen Sie im Abschnitt **Vorlage angeben** die Option **Vorlagendatei hochladen** aus. Wählen Sie **Datei auswählen**, navigieren Sie zur Datei **RDSPrvateLink_CloudFormation.yaml**, die Sie zuvor heruntergeladen haben, und wählen Sie dann **Weiter** aus, um fortzufahren.

1. Geben Sie im Abschnitt **Stapelname** einen Namen für Ihren Stapel ein. Dieser Name wird zusammen mit einem automatisch hinzugefügten Suffix für die später im Prozess erstellten Ressourcennamen verwendet. Deshalb gilt Folgendes:

    - Achten Sie darauf, einen aussagekräftigen Namen für Ihren Stapel zu verwenden.
    - Stellen Sie sicher, dass der Stapelname nicht länger als 19 Zeichen ist.

1. <a name="parameters"></a>Geben Sie im Bereich **Parameter** die folgenden Werte ein, indem Sie die Daten verwenden, die auf Ihrer RDS-Datenbankseite in AWS verfügbar sind:

    |Name  |Beschreibung  |
    |---------|---------|
    |**Endpunkt und Port**    | Geben Sie die aufgelöste IP-Adresse der RDS-Endpunkt-URL und den Port ein. Beispiel: `192.168.1.1:5432` <br><br>- **Wenn ein RDS-Proxy konfiguriert ist**, verwenden Sie die IP-Adresse des Lese-/Schreibendpunkts des Proxys für die entsprechende Datenbank. Wir empfehlen, bei der Arbeit mit Purview einen RDS-Proxy zu verwenden, da die IP-Adresse statisch ist.<br><br>- **Wenn Sie mehrere Endpunkte hinter derselben VPC haben**, geben Sie bis zu 10 durch Kommas getrennte Endpunkte ein.  In diesem Fall wird ein einzelner Load Balancer für die VPC erstellt, der eine Verbindung von der Amazon RDS Multi-Cloud-Scan-Verbindung  für Azure Purview in AWS zu allen RDS-Endpunkten in der VPC ermöglicht.       |
    |**Netzwerk**     | Geben Sie Ihre VPC-ID ein        |
    |**VPC IPv4 CIDR**     | Geben Sie den Wert der CIDR Ihrer VPC ein. Sie finden diesen Wert, indem Sie den VPC-Link auf Ihrer RDS-Datenbankseite auswählen. Beispiel: `192.168.0.0/16`        |
    |**Subnetze**     |Wählen Sie alle Subnetze aus, die Ihrer VPC zugeordnet sind.         |
    |**Security**     | Wählen Sie die der RDS-Datenbank zugeordneten VPC-Sicherheitsgruppe aus.         |
    |     |         |

    Wenn Sie fertig sind, wählen Sie **Weiter**, um fortzufahren.

1. Die Einstellungen unter **Stapeloptionen konfigurieren** sind für dieses Verfahren optional.

    Definieren Sie Ihre Einstellungen nach Bedarf für Ihre Umgebung. Wählen Sie für weitere Informationen die Links **Mehr erfahren** aus, um auf die AWS-Dokumentation zuzugreifen. Wenn Sie fertig sind, wählen Sie **Weiter**, um fortzufahren.

1. Überprüfen Sie auf der Seite **Überprüfen**, ob die von Ihnen ausgewählten Werte für Ihre Umgebung richtig sind. Nehmen Sie alle erforderlichen Änderungen vor und wählen Sie dann **Stapel erstellen**, wenn Sie fertig sind.

1. Achten Sie auf die zu erstellenden Ressourcen. Nach Abschluss werden die relevanten Daten für dieses Verfahren auf den folgenden Registerkarten angezeigt:

    - **Ereignisse**: Zeigt die Ereignisse/Aktivitäten an, die von der CloudFormation-Vorlage ausgeführt werden
    - **Ressourcen**: Zeigt die neu erstellte Zielgruppe, den Load Balancer und den Endpunktdienst an
    - **Ausgaben**: Zeigt den Wert **ServiceName** sowie die IP-Adresse und den Port der RDS-Server an

        Wenn Sie mehrere RDS-Server konfiguriert haben, wird ein anderer Port angezeigt. Verwenden Sie in diesem Fall den hier angezeigten Port anstelle des tatsächlichen RDS-Server-Ports, wenn Sie [Ihre RDS-Datenbank](#register-an-amazon-rds-data-source) als Purview-Datenquelle registrieren.

1. Kopieren Sie auf der Registerkarte **Ausgaben** den Schlüsselwert **ServiceName** in die Zwischenablage.

    Sie verwenden den Wert des Schlüssels **ServiceName** im Azure Purview-Portal, wenn Sie [Ihre RDS-Datenbank](#register-an-amazon-rds-data-source) als Purview-Datenquelle registrieren. Geben Sie dort den Schlüssel **ServiceName** in das Feld **Mit privatem Netzwerk über Endpunktdienst verbinden** ein.

## <a name="register-an-amazon-rds-data-source"></a>Registrieren einer Amazon RDS-Datenquelle

**So fügen Sie Ihren Amazon RDS-Server als Azure Purview-Datenquelle hinzu**:

1. Navigieren Sie in Azure Purview zur Seite **Datenzuordnung**, und wählen Sie **Registrieren** ![Symbol registrieren](./media/register-scan-amazon-s3/register-button.png).

1.  Wählen Sie auf der Seite **Quellen** die Option **Registrieren.** Wählen Sie auf der rechts angezeigten Seite **Quellen registrieren** die Registerkarte **Datenbank** und dann **Amazon RDS (PostgreSQL)** oder **Amazon RDS (SQL)** aus.

    :::image type="content" source="media/register-scan-amazon-rds/register-amazon-rds.png" alt-text="Screenshot der Seite Quellen registrieren zur Auswahl von Amazon RDS (PostgreSQL).":::

1. Geben Sie die Details für Ihre Quelle ein:

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |**Name**     |  Geben Sie einen aussagekräftigen Namen für Ihre Quelle ein, z.B. `AmazonPostgreSql-Ups`       |
    |**Servername**     | Geben Sie den Namen Ihrer RDS-Datenbank in der folgenden Syntax ein: `<instance identifier>.<xxxxxxxxxxxx>.<region>.rds.amazonaws.com`  <br><br>Wir empfehlen, diese URL aus dem Amazon RDS-Portal zu kopieren und sicherzustellen, dass die URL die AWS-Region enthält.      |
    |**Port**     |  Geben Sie den Port ein, der für die Verbindung mit der RDS-Datenbank verwendet wird:<br><br>        - PostgreSQL: `5432`<br> - Microsoft SQL: `1433`<br><br>        Wenn Sie [AWS PrivateLink mit einer CloudFormation-Vorlage konfiguriert haben](#configure-aws-privatelink-using-a-cloudformation-template) und mehrere RDS-Server in derselben VPC haben, verwenden Sie die auf der CloudFormation-Registerkarte **Ausgänge** aufgeführten Ports anstelle der gelesenen RDS-Serverports.       |
    |**Verbindung mit privatem Netzwerk über Endpunktdienst**     |  Geben Sie den Schlüsselwert **ServiceName** ein, den Sie am Ende des [vorherigen Verfahrens](#configure-aws-privatelink-using-a-cloudformation-template) erhalten haben. <br><br>Wenn Sie Ihre RDS-Datenbank manuell vorbereitet haben, verwenden Sie den Wert **ServiceName**, den Sie am Ende von [Schritt 5: Endpunktdienst erstellen](#step-5-create-an-endpoint-service) erhalten haben.       |
    |**Sammlung** (optional)     |  Wählen Sie eine Sammlung aus, zu der Sie Ihre Datenquelle hinzufügen möchten. Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).       |
    |     |         |

1. Wählen Sie **Registrieren**, wenn Sie fortfahren möchten.

Ihre RDS-Datenquelle wird in der Quellenzuordnung oder -liste angezeigt. Beispiel:

:::image type="content" source="media/register-scan-amazon-rds/amazon-rds-in-sources.png" alt-text="Screenshot einer Amazon RDS-Datenquelle auf der Seite Quellen.":::

## <a name="create-purview-credentials-for-your-rds-scan"></a>Erstellen Sie Purview-Anmeldeinformationen für Ihren RDS-Scan

Die für Amazon RDS-Datenquellen unterstützten Anmeldeinformationen umfassen nur die Benutzername/Kennwort-Authentifizierung mit einem Kennwort, das in einem geheimen Azure KeyVault gespeichert ist.

### <a name="create-a-secret-for-your-rds-credentials-to-use-in-purview"></a>Erstellen Sie ein Geheimkennwort für Ihre RDS-Anmeldeinformationen zur Verwendung in Purview

1.  Fügen Sie Ihr Geheimkennwort zu einem Azure KeyVault hinzu. Weitere Informationen finden Sie unter [Festlegen und Abrufen eines Geheimkennworts aus Key Vault mithilfe des Azure-Portals](/azure/key-vault/secrets/quick-create-portal).

1.  Fügen Sie Ihrem KeyVault eine Zugriffsrichtlinie mit den Berechtigungen **Anfordern** und **Liste** hinzu. Beispiel:

    :::image type="content" source="media/register-scan-amazon-rds/keyvault-for-rds.png" alt-text="Screenshot einer Zugriffsrichtlinie für RDS in Purview.":::

    Wählen Sie beim Definieren des Prinzipals für die Richtlinie Ihr Purview-Konto aus. Beispiel:

    :::image type="content" source="media/register-scan-amazon-rds/select-purview-as-principal.png" alt-text="Screenshot der Auswahl Ihres Purview-Kontos als Prinzipal.":::

    Wählen Sie **Speichern**, um Ihr Zugriffsrichtlinien-Update zu speichern. Weitere Informationen finden Sie unter [Zuweisen einer Azure Key Vault-Zugriffsrichtlinie](/azure/key-vault/general/assign-access-policy-portal).

1. Fügen Sie in Azure Purview eine KeyVault-Verbindung hinzu, um KeyVault mit Ihrem RDS-Geheimkennwort mit Purview zu verbinden. Weitere Informationen finden Sie unter [Anmeldeinformationen für die Quellauthentifizierung in Azure Purview](manage-credentials.md).

### <a name="create-your-purview-credential-object-for-rds"></a>Erstellen Sie Ihr Purview-Anmeldeinformationsobjekt für RDS

Erstellen Sie in Azure Purview ein Anmeldeinformationsobjekt, das Sie beim Scannen Ihres Amazon RDS-Kontos verwenden möchten.

1. Wählen Sie im Bereich Purview **Management** **Sicherheit und Zugriff** > **Anmeldeinformationen** > **Neu** aus.

1. Wählen Sie **SQL-Authentifizierung** als Authentifizierungsmethode aus. Geben Sie dann Details für den Key Vault ein, in dem Ihre RDS-Anmeldeinformationen gespeichert sind, einschließlich der Namen Ihres Key Vault und des Geheimkennworts.

    Beispiel:

    :::image type="content" source="media/register-scan-amazon-rds/new-credential-for-rds.png" alt-text="Screenshot eines neuen Berechtigungsnachweises für RDS.":::

Weitere Informationen finden Sie unter [Anmeldeinformationen für die Quellauthentifizierung in Azure Purview](manage-credentials.md).

## <a name="scan-an-amazon-rds-database"></a>Eine Amazon RDS-Datenbank scannen

So konfigurieren Sie einen Azure Purview-Scan für Ihre RDS-Datenbank:

1.  Wählen Sie auf der Seite Purview **Quellen** die zu scannende Amazon RDS-Datenquelle aus.

1.  Wählen Sie :::image type="icon" source="media/register-scan-amazon-s3/new-scan-button.png" border="false"::: **Neuer Scan** aus, um mit der Definition Ihres Scans zu beginnen. Geben Sie im sich rechts öffnenden Fenster die folgenden Details ein und wählen Sie dann **Weiter**.

    - **Name**: Geben Sie einen aussagekräftigen Namen für Ihren Scan ein.
    - **Datenbankname**: Geben Sie den Namen der Datenbank ein, die Sie scannen möchten. Sie müssen die von außerhalb von Purview verfügbaren Namen finden und für jede Datenbank auf dem registrierten RDS-Server einen separaten Scan erstellen.
    - **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen aus, die Sie zuvor für die Multi-Cloud-Scan-Verbindungen für Azure Purview erstellt haben, um auf die RDS-Datenbank zuzugreifen.

1.  Wählen Sie im Bereich **Suchregelsatz auswählen** den gewünschten Suchregelsatz aus oder erstellen Sie einen neuen. Weitere Informationen finden Sie unter [Erstellen eines Überprüfungsregelsatzes](create-a-scan-rule-set.md).

1.  Wählen Sie im Bereich **Scan-Trigger festlegen** aus, ob Sie den Scan einmal oder zu einem wiederkehrenden Zeitpunkt ausführen möchten, und wählen Sie dann **Weiter**.

1.  Überprüfen Sie im Bereich **Scan überprüfen** die Details und wählen Sie dann **Speichern und ausführen** oder **Speichern**, um ihn später auszuführen.

Wählen Sie während des Scanvorgangs **Aktualisieren**, um den Scanfortschritt zu überwachen.

> [!NOTE]
> Bei der Arbeit mit Amazon RDS PostgreSQL-Datenbanken werden nur vollständige Scans unterstützt. Inkrementelle Scans werden nicht unterstützt, da PostgreSQL keinen Wert für **Letzte Änderungszeit** hat. 
>

## <a name="explore-scanning-results"></a>Scanergebnisse untersuchen

Nachdem ein Purview-Scan in Ihren Amazon RDS-Datenbanken abgeschlossen ist, führen Sie einen Drilldown im Bereich Purview **Datenkarte** durch, um den Scanverlauf anzuzeigen. Wählen Sie eine Datenquelle aus, um deren Details anzuzeigen, und wählen Sie dann die Registerkarte **Überprüfungen** aus, um alle zurzeit ausgeführten oder abgeschlossenen Überprüfungen anzuzeigen.

Verwenden Sie die anderen Bereiche von Purview, um Details zu den Inhalten in Ihrem Datenbestand zu erfahren, einschließlich Ihrer Amazon RDS-Datenbanken:

- **Erkunden Sie RDS-Daten im Katalog**. Der Purview-Katalog zeigt eine einheitliche Ansicht über alle Quelltypen hinweg, und RDS-Scanergebnisse werden ähnlich wie in Azure SQL angezeigt. Sie können den Katalog mit Filtern durchsuchen oder die Assets durchsuchen und durch die Hierarchie navigieren. Weitere Informationen finden Sie unter:

    - [Tutorial: Durchsuchen von Ressourcen in Azure Purview (Vorschau) und Anzeigen ihrer Herkunft](tutorial-browse-and-view-lineage.md)
    - [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
    - [Registrieren und Überprüfen von Azure SQL-Datenbank](register-scan-azure-sql-database.md)

- **Zeigen Sie Erkenntnisberichte an**, um Statistiken für die Klassifizierung, Vertraulichkeitsbezeichnungen, Dateitypen sowie weitere Details zu Ihrem Inhalt anzuzeigen.

    Alle Purview Insight-Berichte enthalten die Amazon RDS-Scanergebnisse zusammen mit den restlichen Ergebnissen aus Ihren Azure-Datenquellen. Falls relevant, wird den Filteroptionen für Berichte ein Asset-Typ **Amazon RDS** hinzugefügt.

    Weitere Informationen finden Sie unter [Grundlegendes zu Erkenntnissen in Azure Purview](concept-insights.md).

- **RDS-Daten in anderen Purview-Funktionen anzeigen**, z.B. in den Bereichen **Scans** und **Glossar**. Weitere Informationen finden Sie unter:

    - [Erstellen eines Überprüfungsregelsatzes](create-a-scan-rule-set.md)
    - [Tutorial: Erstellen und Importieren von Glossarbegriffen in Azure Purview (Vorschau)](tutorial-import-create-glossary-terms.md)


## <a name="configure-aws-privatelink-manually-advanced"></a>AWS PrivateLink manuell konfigurieren (erweitert)

In diesem Verfahren werden die manuellen Schritte beschrieben, die zum Vorbereiten Ihrer RDS-Datenbank in einer VPC für die Verbindung mit Azure Purview erforderlich sind.

Wir empfehlen standardmäßig, stattdessen eine CloudFormation-Vorlage zu verwenden, wie weiter oben in diesem Artikel beschrieben. Weitere Informationen finden Sie unter [AWS PrivateLink mit einer CloudFormation-Vorlage konfigurieren](#configure-aws-privatelink-using-a-cloudformation-template).

### <a name="step-1-retrieve-your-amazon-rds-endpoint-ip-address"></a>Schritt 1: Abrufen Ihrer Amazon RDS-Endpunkt-IP-Adresse

Lokalisieren Sie die IP-Adresse Ihres Amazon RDS-Endpunkts, der in einer Amazon VPC gehostet wird. Diese IP-Adresse verwenden Sie später beim Anlegen Ihrer Zielgruppe.

**So rufen Sie Ihre RDS-Endpunkt-IP-Adresse ab**:

1.  Navigieren Sie in Amazon RDS zu Ihrer RDS-Datenbank und identifizieren Sie Ihre Endpunkt-URL. Dieser befindet sich unter **Konnektivität & Sicherheit** als Ihr **Endpunkt**-Wert.

    > [!TIP]
    > Verwenden Sie den folgenden Befehl, um eine Liste der Datenbanken in Ihrem Endpunkt abzurufen: `aws rds describe-db-instances`
    >

1.  Verwenden Sie die Endpunkt-URL, um die IP-Adresse Ihrer Amazon RDS-Datenbank zu ermitteln. Verwenden Sie beispielsweise eine der folgenden Methoden:

    - **Ping**: `ping <DB-Endpoint>`

    - **nslookup**: `nslookup <Db-Endpoint>`

    - **Online nslookup**. Geben Sie Ihren Datenbankwert **Endpunkt** in das Suchfeld ein und wählen Sie **DNS-Einträge suchen**. **NSLookup.io** zeigt Ihre IP-Adresse auf dem nächsten Bildschirm an.

### <a name="step-2-enable-your-rds-connection-from-a-load-balancer"></a>Schritt 2: Aktivieren Sie Ihre RDS-Verbindung über einen Load Balancer

So stellen Sie sicher, dass Ihre RDS-Verbindung vom Load Balancer, den Sie später erstellen, zugelassen wird:

1.  **VPC-IP-Bereich suchen**.

    Navigieren Sie in Amazon RDS zu Ihrer RDS-Datenbank. Wählen Sie im Bereich **Konnektivität und Sicherheit** den Link **VPC** aus, um den IP-Bereich (IPv4 CIDR) zu finden.

    Im Bereich **Ihre VPCs** wird Ihr IP-Bereich in der Spalte **IPv4 CIDR** angezeigt.

    > [!TIP]
    > Um diesen Schritt über die CLI auszuführen, verwenden Sie den folgenden Befehl: `aws ec2 describe-vpcs`
    >
    > Weitere Informationen finden Sie unter [ec2 – AWS CLI 1.19.105-Befehlsreferenz (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/).
    >

1.  <a name="security-group"></a>**Eine Sicherheitsgruppe für diesen IP-Bereich erstellen**.

    1. Öffnen Sie die Amazon EC2-Konsole unter https://console.aws.amazon.com/ec2/ und navigieren Sie zu **Sicherheitsgruppen**.

    1. Wählen Sie **Sicherheitsgruppe erstellen** und erstellen Sie dann Ihre Sicherheitsgruppe. Achten Sie dabei darauf, die folgenden Details anzugeben:

        - **Name der Sicherheitsgruppe**: Geben Sie einen aussagekräftigen Namen ein
        - **Beschreibung**: Geben Sie eine Beschreibung für Ihre Sicherheitsgruppe ein
        - **VPC**: Wählen Sie Ihre RDS-Datenbank-VPC

    1.  Wählen Sie unter **Eingehende Regeln** die Option **Regel hinzufügen** aus und geben Sie die folgenden Details ein:

        - **Typ**: Wählen Sie **Benutzerdefiniertes TCP** aus.
        - **Portbereich**: Geben Sie Ihren RDS-Datenbankport ein
        - **Quelle**: Wählen Sie **Benutzerdefiniert** und geben Sie den VPC-IP-Bereich aus dem vorherigen Schritt ein.

    1.  Scrollen Sie zum Ende der Seite und wählen Sie **Sicherheitsgruppe erstellen**.

1.  **Verknüpfen Sie die neue Sicherheitsgruppe mit RDS**.

    1.  Navigieren Sie in Amazon RDS zu Ihrer RDS-Datenbank und wählen Sie **Ändern**.

    1.  Scrollen Sie nach unten zum Abschnitt **Konnektivität** und fügen Sie im Feld **Sicherheitsgruppe** die neue Sicherheitsgruppe hinzu, die Sie im [vorherigen Schritt](#security-group) erstellt haben. Scrollen Sie dann zum Ende der Seite und wählen Sie **Weiter.**

    1.  Wählen Sie im Abschnitt **Änderungen planen** die Option **Sofort anwenden** aus, um die Sicherheitsgruppe sofort zu aktualisieren.

    1.  Wählen Sie **DB-Instance ändern**.

> [!TIP]
> Um diesen Schritt über die CLI auszuführen, verwenden Sie die folgenden Befehle:
>
> - `aws  ec2 create-security-group--description <value>--group-name <value>[--vpc-id <value>]`
>
>     Weitere Informationen finden Sie unter [create-security-group – AWS CLI 1.19.105-Befehlsreferenz (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html).
>
> - `aws rds --db-instance-identifier <value> --vpc-security-group-ids <value>`
>
>     Weitere Informationen finden Sie unter [modify-db-instance – AWS CLI 1.19.105-Befehlsreferenz (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html).
>

### <a name="step-3-create-a-target-group"></a>Schritt 3: Zielgruppe erstellen

**So erstellen Sie Ihre Zielgruppe in AWS**:

1.  Öffnen Sie die Amazon EC2-Konsole unter https://console.aws.amazon.com/ec2/ und navigieren Sie zu **Load Balancing** > **Zielgruppen**.

1.  Wählen Sie **Zielgruppe erstellen** und erstellen Sie Ihre Zielgruppe. Achten Sie dabei darauf, die folgenden Details anzugeben:

    - **Zieltyp**: Wählen Sie **IP-Adressen** (optional)
    - **Protokoll**: Wählen Sie **TCP**
    - **Port**: Geben Sie Ihren RDS-Datenbankport ein
    - **VPC**: Geben Sie Ihre RDS-Datenbank-VPC ein

    > [!NOTE]
    > Sie finden den RDS-Datenbankport und die VPC-Werte auf Ihrer RDS-Datenbankseite unter **Konnektivität und Sicherheit**.

    Wenn Sie fertig sind, wählen Sie **Weiter**, um fortzufahren.

1.  Geben Sie auf der Seite **Ziele registrieren** Ihre RDS-Datenbank-IP-Adresse ein und wählen Sie dann **Als ausstehend einschließen** aus.

1.  Nachdem das neue Ziel in der Tabelle **Ziele** aufgeführt ist, wählen Sie unten auf der Seite **Zielgruppe erstellen** aus.

> [!TIP]
> Um diesen Schritt über CLI auszuführen, verwenden Sie den folgenden Befehl:
>
> - `aws elbv2 create-target-group --name <tg-name> --protocol <db-protocol> --port <db-port> --target-type ip --vpc-id <db-vpc-id>`
>
>     Weitere Informationen finden Sie unter [create-target-group – AWS CLI 2.2.7-Befehlsreferenz (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-target-group.html).
>
> - `aws elbv2 register-targets --target-group-arn <tg-arn> --targets Id=<db-ip>,Port=<db-port>`
>
>     Weitere Informationen finden Sie unter [register-targets – AWS CLI 2.2.7-Befehlsreferenz (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/register-targets.html).
>

### <a name="step-4-create-a-load-balancer"></a>Schritt 4: Einrichten eines Load Balancers

Sie können entweder [einen neuen Netzwerk-Load-Balancer erstellen](#create-load-balancer), um den Datenverkehr an die RDS-IP-Adresse weiterzuleiten, oder einem vorhandenen Load-Balancer [einen neuen Zuhörer hinzufügen](#listener).

<a name="create-load-balancer"></a>**So erstellen Sie einen Netzwerk-Load-Balancer zum Weiterleiten von Datenverkehr an die RDS-IP-Adresse**:

1.  Öffnen Sie die Amazon EC2-Konsole unter https://console.aws.amazon.com/ec2/ und navigieren Sie zu **Load Balancing** > **Load Balancers**.

1.  Wählen Sie **Load Balancer erstellen** > **Netzwerk Load Balancer** und wählen Sie dann die folgenden Werte aus oder geben Sie sie ein:

    - **Schema**: Wählen Sie **Intern**

    - **VPC**: Wählen Sie Ihre RDS-Datenbank-VPC

    - **Zuordnung**: Stellen Sie sicher, dass der RDS für alle AWS-Regionen definiert ist, und wählen Sie dann alle diese Regionen aus. Sie finden diese Informationen im Wert **Verfügbarkeitszone** auf der Seite [RDS-Datenbank](#step-1-retrieve-your-amazon-rds-endpoint-ip-address) auf der Registerkarte **Konnektivität und Sicherheit**.

    - **Zuhörer und Weiterleitung**:

        - *Protokoll*: Wählen Sie **TCP**
        - *Port*: Wählen Sie **RDS-DB-Port**
        - *Standardaktion*: Wählen Sie die im [vorherigen Schritt](#step-3-create-a-target-group) erstellte Zielgruppe aus.

1.  Wählen Sie unten auf der Seite **Load-Balancer erstellen** > **Load-Balancer anzeigen** aus.

1.  Warten Sie einige Minuten und aktualisieren Sie den Bildschirm, bis die Spalte **State** des neuen Load Balancer **Aktiv** ist.


> [!TIP]
> Um diesen Schritt über die CLI auszuführen, verwenden Sie die folgenden Befehle:
> - `aws elbv2 create-load-balancer --name <lb-name> --type network --scheme internal --subnet-mappings SubnetId=<value>`
>
>    Weitere Informationen finden Sie unter [create-load-balancer – AWS CLI 2.2.7-Befehlsreferenz (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-load-balancer.html).
>
> - `aws elbv2 create-listener --load-balancer-arn <lb-arn> --protocol TCP --port 80 --default-actions Type=forward,TargetGroupArn=<tg-arn>`
>
>    Weitere Informationen finden Sie unter [create-listener – AWS CLI 2.2.7-Befehlsreferenz (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-listener.html).
>

<a name="listener"></a>**So fügen Sie einem vorhandenen Load-Balancer einen Zuhörer hinzu**:

1. Öffnen Sie die Amazon EC2-Konsole unter https://console.aws.amazon.com/ec2/ und navigieren Sie zu **Load Balancing** > **Load Balancers**.

1. Wählen Sie Load-Balancer > **Zuhörer** > **Zuhörer hinzufügen** aus.

1. Wählen Sie auf der Registerkarte **Zuhörer** im Bereich **Protokoll: Port** **TCP** aus und geben Sie einen neuen Port für Ihren Zuhörer ein.


> [!TIP]
> Um diesen Schritt über CLI auszuführen, verwenden Sie den folgenden Befehl: `aws elbv2  create-listener --load-balancer-arn <value> --protocol <value>  --port <value> --default-actions Type=forward,TargetGroupArn=<target_group_arn>`
>
> Weitere Informationen finden Sie in der [AWS-Dokumentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-listener.html).
>

### <a name="step-5-create-an-endpoint-service"></a>Schritt 5: Einen Endpunktdienst erstellen

Nachdem der [Load Balancer erstellt wurde](#step-4-create-a-load-balancer) und sein Status **Aktiv** ist, können Sie den Endpunktdienst erstellen.

**So erstellen Sie den Endpunktdienst**:

1.  Öffnen Sie die Amazon VPC-Konsole unter https://console.aws.amazon.com/vpc/ und navigieren Sie zu **Virtual Private Cloud > Endpunktdienste**.

1.  Wählen Sie **Endpunktdienst erstellen** und in der Dropdown-Liste **Verfügbare Load Balancer** den neuen Load Balancer aus, der im [vorherigen Schritt](#step-4-create-a-load-balancer) erstellt wurde, oder den Load Balancer, in dem Sie einen neuen Zuhörer hinzugefügt haben.

1.  Deaktivieren Sie auf der Seite **Endpunktdienst erstellen** die Auswahl für die Option **Akzeptanz für Endpunkt erforderlich**.

1.  Wählen Sie unten auf der Seite **Dienst erstellen** > **Schließen** aus.

1.  Zurück auf der Seite **Endpunktdienste**:

    1. Wählen Sie den neuen Endpunktdienst aus, den Sie erstellt haben.
    1. Wählen Sie auf der Registerkarte **Prinzipale zulassen** die Option **Prinzipale hinzufügen** aus.
    1. Geben Sie im Feld **Hinzuzufügende Prinzipale > ARN** `arn:aws:iam::181328463391:root` ein.
    1. Wählen Sie **Prinzipale hinzufügen** aus.

    > [!NOTE]
    > Verwenden Sie beim Hinzufügen einer Identität ein Sternchen (*****), um Berechtigungen für alle Prinzipale hinzuzufügen. Dadurch können alle Prinzipale in allen AWS-Konten einen Endpunkt für Ihren Endpunktdienst erstellen. Weitere Informationen finden Sie in der [AWS-Dokumentation](https://docs.aws.amazon.com/vpc/latest/privatelink/add-endpoint-service-permissions.html).

> [!TIP]
> Um diesen Schritt über die CLI auszuführen, verwenden Sie die folgenden Befehle:
>
> - `aws ec2 create-vpc-endpoint-service-configuration --network-load-balancer-arns  <lb-arn>  --no-acceptance-required`
>
>    Weitere Informationen finden Sie unter [create-vpc-endpoint-service-configuration – AWS CLI 2.2.7-Befehlsreferenz (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-vpc-endpoint-service-configuration.html).
>
> - `aws ec2 modify-vpc-endpoint-service-permissions --service-id <endpoint-service-id> --add-allowed-principals <purview-scanner-arn>`
>
>    Weitere Informationen finden Sie unter [modify-vpc-endpoint-service-permissions – AWS CLI 2.2.7-Befehlsreferenz (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html).
>

<a name="service-name"></a>**So kopieren Sie den Dienstnamen zur Verwendung in Azure Purview**:

Nachdem Sie Ihren Endpunktdienst erstellt haben, können Sie den Wert **ServiceName** im Azure Purview-Portal kopieren, wenn Sie [Ihre RDS-Datenbank](#register-an-amazon-rds-data-source) als Purview-Datenquelle registrieren.

Suchen Sie den **ServiceName** auf der Registerkarte **Details** für Ihren ausgewählten Endpunktdienst.

> [!TIP]
> Um diesen Schritt über CLI auszuführen, verwenden Sie den folgenden Befehl: `Aws ec2 describe-vpc-endpoint-services`
>
> Weitere Informationen finden Sie unter [describe-vpc-endpoint-services – AWS CLI 2.2.7-Befehlsreferenz (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-vpc-endpoint-services.html).
>

## <a name="troubleshoot-your-vpc-connection"></a>Fehler bei Ihrer VPC-Verbindung beheben

In diesem Abschnitt werden häufige Fehler beschrieben, die beim Konfigurieren Ihrer VPC-Verbindung mit Azure Purview auftreten können, und wie Sie diese beheben können.

### <a name="invalid-vpc-service-name"></a>Ungültiger VPC-Dienstname

Wenn in Azure Purview ein Fehler von `Invalid VPC service name` oder `Invalid endpoint service` angezeigt wird, führen Sie die folgenden Schritte zur Fehlerbehebung aus:

1. Stellen Sie sicher, dass Ihr VPC-Dienstname korrekt ist. Beispiel:

    :::image type="content" source="media/register-scan-amazon-rds/locate-service-name.png" alt-text="Screenshot des VPC-Servicenamens in AWS.":::

1. Stellen Sie sicher, dass der Microsoft ARN in den zulässigen Prinzipalen aufgeführt ist: `arn:aws:iam::181328463391:root`

    Weitere Informationen finden Sie unter [Schritt 5: Erstellen eines Endpunktdienstes](#step-5-create-an-endpoint-service).

1. Stellen Sie sicher, dass Ihre RDS-Datenbank in einer der unterstützten Regionen aufgeführt ist. Weitere Informationen finden Sie unter [Umfang von Purview für Amazon RDS](#purview-scope-for-amazon-rds).

### <a name="invalid-availability-zone"></a>Ungültige Verfügbarkeitszone

Wenn in Azure Purview ein Fehler von `Invalid Availability Zone` angezeigt wird, stellen Sie sicher, dass Ihr RDS für mindestens eine der folgenden drei Regionen definiert ist:

- **us-east-1a**
- **us-east-1b**
- **us-east-1c**

Weitere Informationen finden Sie in der [AWS-Dokumentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-getting-started.html).

### <a name="rds-errors"></a>RDS-Fehler

Die folgenden Fehler können in Azure Purview auftreten:

- `Unknown database`. In diesem Fall existiert die definierte Datenbank nicht. Überprüfen Sie, ob der konfigurierte Datenbankname korrekt ist

- `Failed to login to the Sql data source. The given auth credential does not have permission on the target database.` In diesem Fall sind Ihr Benutzername und Ihr Passwort falsch. Überprüfen Sie Ihre Zugangsdaten und aktualisieren Sie sie bei Bedarf.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Erkenntnisberichten in Azure Purview:

> [!div class="nextstepaction"]
> [Grundlegendes zu Erkenntnissen in Azure Purview](concept-insights.md)
