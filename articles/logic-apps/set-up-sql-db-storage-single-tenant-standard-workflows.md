---
title: Einrichten des SQL-Speichers für Logik-App-Standardworkflows
description: Richten Sie eine SQL-Datenbank ein, um Workflowartefakte, Zustände und den Ausführungsverlauf von Logik-Apps (Standard) in einer Azure Logic Apps-Instanz mit einem Mandanten zu speichern.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5dd6f2d024055a75e350bfb5b0eee0e26c115193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475804"
---
# <a name="set-up-sql-database-storage-for-standard-logic-apps-in-single-tenant-azure-logic-apps-preview"></a>Einrichten des SQL-Datenbankspeichers für Standard-Logik-Apps in einer Azure Logic Apps-Instanz mit einem Mandanten (Vorschau)

> [!IMPORTANT]
> Diese Funktion befindet sich in der Vorschauphase und unterliegt den [Zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn Sie den Ressourcentyp **Logik-App (Standard)** auswählen, um Workflows zu erstellen, die in Azure Logic Apps mit einem Mandanten, in der App Service-Umgebung v3 oder außerhalb von Azure ausgeführt werden, müssen Sie auch ein Azure Storage-Konto erstellen, um workflowbezogene Artefakte, Zustände und Laufzeitdaten zu speichern. Wenn Sie jedoch mehr Flexibilität und Kontrolle über die Laufzeitumgebung, den Durchsatz, die Skalierung, die Leistung und die Verwaltung Ihrer Logik-App-Workflows wünschen, können Sie den SQL Storage-Anbieter anstelle von Azure Storage für workflowbezogene Speichertransaktionen verwenden.

Dieser Abschnitt bietet eine Übersicht darüber, warum Sie den SQL-Speicher als primären Speicheranbieter für Azure Logic-Anwendungen (Apps) als Alternative zu Azure Storage verwenden sollten, und zeigt Ihnen, wie Sie SQL für die Speicherverwendung entweder während der Erstellung der Logic-Anwendung im Azure-Portal oder während der Bereitstellung der Logic-Anwendung über den Visual Studio Code einrichten.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen in der folgenden Dokumentation:

- [Was ist Azure Logic Apps?](logic-apps-overview.md)
- [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](single-tenant-overview-compare.md)

<a name="why-sql"></a>

## <a name="why-use-sql"></a>Gründe für die Verwendung von SQL

Als alternative Speicheroption, die für Azure Logic Apps mit nur einem Mandanten verfügbar ist, bietet SQL die folgenden Vorteile:

| Vorteil | BESCHREIBUNG |
|---------|-------------|
| **Portabilität** | SQL weist viele Formfaktoren auf, einschließlich virtueller Computer, Platform as a Service (PaaS) und Container. Sie können SQL-Datenbanken fast überall dort ausführen, wo Sie Logik-App-Workflows ausführen möchten. |
| **Steuerung** | SQL ermöglicht eine präzise Kontrolle über Datenbankdurchsatz, Leistung und Skalierung während bestimmter Zeiträume oder für bestimmte Workloads. Die Preise für SQL basieren auf der CPU-Auslastung und dem Durchsatz, was die Preise vorhersagbarer macht als für Azure Storage, wo die Kosten auf einzelnen Vorgängen basieren. |
| **Nutzen vorhandener Ressourcen** | Wenn Sie mit Microsoft-Tools vertraut sind, können Sie deren Ressourcen für moderne Integrationen mit SQL verwenden. Mit Azure-Hybridvorteilen können Sie Ressourcen in herkömmlichen lokalen Bereitstellungen und modernen Cloudimplementierungen wiederverwenden. SQL bietet auch ausgereifte und gut unterstützte Tools wie SQL Server Management Studio (SSMS), Befehlszeilenschnittstellen und SDKs. |
| **Compliance** | SQL bietet mehr Optionen als Azure Storage zum Sichern, Wiederherstellen, für das Failover und Erstellen in Redundanzen. Sie können die gleichen Unternehmensmechanismen wie andere Unternehmensanwendungen auf den Speicher Ihrer Logik-App anwenden. |
|||

<a name="when-use-sql"></a>

## <a name="when-to-use-sql"></a>Verwendung von SQL

In der folgenden Tabelle werden einige Gründe für die Verwendung von SQL beschrieben:

| Szenario | Empfehlen des Speicheranbieters |
|----------|----------------------------|
| Sie möchten Logik-App-Workflows in Azure mit mehr Kontrolle über Speicherdurchsatz und -leistung ausführen. | Verwenden Sie SQL als Speicheranbieter, da Azure Storage keine Tools zur Optimierung von Durchsatz und Leistung bereitstellt. |
| Sie möchten Logik-App-Workflows lokal ausführen, die Sie mit [Azure Arc-fähigen Logik-Apps](azure-arc-enabled-logic-apps-overview.md) aktivieren können. | Verwenden Sie SQL als Speicheranbieter, damit Sie auswählen können, wo Ihre SQL-Datenbank gehostet werden soll, z. B. lokal auf einem virtuellen Computer, in einem Container oder in mehreren Clouds. Erwägen Sie, Ihre Logik-App-Workflows in der Nähe der Systeme auszuführen, die Sie integrieren möchten, oder reduzieren Sie Ihre Abhängigkeit von der Cloud.   |
| Sie möchten vorhersagbare Speicherkosten. | Verwenden Sie SQL als Speicheranbieter, wenn Sie mehr Kontrolle über die Skalierungskosten wünschen. SQL Kosten basieren auf den einzelnen Computevorgängen und Eingabe-/Ausgabevorgängen pro Sekunde (IOPs). Azure Storage-Kosten basieren auf der Anzahl von Vorgängen, die für kleine Workloads, die auf 0 (null) skaliert werden, möglicherweise besser funktionieren. |
| Sie bevorzugen die Verwendung von SQL gegenüber Azure Storage. | SQL ist ein bekanntes und zuverlässiges Ökosystem, mit dem Sie die gleiche Governance und Verwaltung für Ihre Logik-Apps im Hintergrund anwenden können. |
| Sie möchten vorhandene SQL-Umgebungen wiederverwenden. | Verwenden Sie SQL als Speicheranbieter, wenn Sie bereits über SQL-Lizenzen verfügen, die Sie wiederverwenden oder in der Cloud modernisieren möchten. Sie sollten auch die Azure-Hybridvorteile für Ihre Logik-App-Integrationen verwenden. |
| Alle anderen | Verwenden Sie Azure Storage als Standardspeicheranbieter. |
|||

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto und ein aktives Abonnement. Wenn Sie keines besitzen, können Sie sich für ein  [kostenloses Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Eine SQL-Umgebung, die mit Ihrer Logik-App verwendet werden soll. Führen Sie jedoch die folgenden Schritte aus, bevor Sie Ihre Umgebung einrichten:

  1. Erstellen Sie eine SQL Server-Instanz.

     Unterstützte Typen sind unter anderem  [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads), [Azure SQL-Datenbank](https://azure.microsoft.com/products/azure-sql/database/) und  [Azure SQL Managed Instance](https://azure.microsoft.com/products/azure-sql/managed-instance/).

     - Wenn Ihre SQL Server-Instanz mit einem der unterstützten Typen in Azure gehostet wird, müssen Sie die folgenden Berechtigungen einrichten:

       1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer SQL Server-Ressource.

       1. Wählen Sie im Navigationsmenü der Ressource unter **Sicherheit** die Option **Firewalls und virtuelle Netzwerke** aus.

       1. Wählen Sie im daraufhin geöffneten Bereich unter **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** die Option **Ja** aus.

       1. Speichern Sie die Änderungen.

     - Wenn Ihre SQL Server-Instanz nicht in Azure gehostet wird, stellen Sie sicher, dass alle Firewalls oder Netzwerkeinstellungen auf Ihrem Server Azure-Diensten und -Ressourcen den Zugriff auf Ihren Server und Ihre Datenbank gestatten.

     - Wenn Sie SQL Express für die lokale Entwicklung verwenden, stellen Sie eine Verbindung mit der benannten Standardinstanz  `localhost\SQLExpress` her.

  1. Erstellen oder verwenden Sie eine vorhandene Datenbank.

     Sie müssen über eine nutzbare Datenbank verfügen, bevor Sie den SQL Storage-Anbieter einrichten können.

  1. Nun können Sie die [Schritte zum Einrichten Ihrer SQL-Umgebung](#set-up-sql-environment) in diesem Artikel ausführen.

- Für die lokale Entwicklung ist [Visual Studio Code](https://code.visualstudio.com/Download) auf Ihrem lokalen Computer installiert.

  > [!NOTE]
  > Stellen Sie sicher, dass Sie die [neueste Version für das Toolset „Azure Functions Core Tools“](https://github.com/Azure/azure-functions-core-tools/releases) installieren, um SQL-Unterstützung mithilfe der MSI-Version (Microsoft Installer) `func-cli-X.X.XXXX-x*.msi` sicherzustellen. Weitere Informationen zu den Installationsanforderungen für Visual Studio Code erhalten Sie unter [Erstellen von Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

<a name="set-up-sql-environment"></a>

## <a name="set-up-your-sql-environment"></a>Einrichten Ihrer SQL-Umgebung

1. Bevor Sie SQL Storage-Anbieter einrichten, führen Sie die erforderlichen Schritte unter [Voraussetzungen](#prerequisites) aus.

1. Richten Sie Berechtigungen für Ihre SQL Server-Instanz ein.

   Derzeit unterstützt der SQL Storage-Anbieter die SQL-Authentifizierung in Verbindungszeichenfolgen. Sie können auch die Windows-Authentifizierung für die lokale Entwicklung und für Tests verwenden. Derzeit ist keine Unterstützung für Azure Active Directory (Azure AD) und verwaltete Identitäten verfügbar.

   Sie müssen eine Identität verwenden, die über Berechtigungen zum Erstellen und Verwalten workflowbezogener Artefakte in der SQL-Zieldatenbank verfügt. Beispielsweise verfügt ein Administrator über alle erforderlichen Berechtigungen zum Erstellen und Verwalten dieser Artefakte. In der folgenden Liste werden die Artefakte beschrieben, die die Azure Logic Apps-Runtime mit einem Mandanten mithilfe der von Ihnen bereitgestellten SQL-Verbindungszeichenfolge versucht zu erstellen. Stellen Sie sicher, dass die in der SQL-Verbindungszeichenfolge verwendete Identität über die erforderlichen Berechtigungen zum Erstellen der folgenden Artefakte verfügt:

   - Erstellen und Löschen der folgenden Schemas:  `dt`,  `dc` und  `dq`
   - Hinzufügen, Ändern und Löschen von Tabellen in diesen Schemas
   - Hinzufügen, Ändern und Löschen benutzerdefinierter Tabellentypen in diesen Schemas

   Weitere Informationen zu zielgerichteten Berechtigungen erhalten Sie unter [SQL-Serverberechtigungen in der Datenbank-Engine](/sql/relational-databases/security/permissions-database-engine).

1. Herstellen einer Verbindung mit SQL

   - Stellen Sie sicher, dass Ihre SQL-Datenbank den erforderlichen Zugriff für die Entwicklung zulässt.

   - Wenn Sie Azure SQL-Datenbank verwenden, müssen Sie die folgenden Anforderungen erfüllen:

     - Lassen Sie für lokale Entwicklungs- und Testzwecke Verbindungen von der IP-Adresse Ihres lokalen Computers explizit zu. Sie können [Ihre IP-Firewallregeln in Azure SQL Server festlegen](../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules).

     - Erlauben Sie im [Azure-Portal](https://portal.azure.com) Ihrer Logik-App-Ressource, auf die SQL-Datenbank mit einer angegebenen Verbindungszeichenfolge zuzugreifen, indem Sie  [Azure-Dienste zulassen](../azure-sql/database/network-access-controls-overview.md#allow-azure-services).

     - Richten Sie für Ihr Szenario ggf. weitere  [Netzwerkzugriffssteuerungen für die SQL-Datenbank](../azure-sql/database/network-access-controls-overview.md) ein.

   - Wenn Sie Azure SQL Managed Instance verwenden, lassen Sie zu, dass Azure-Dienste (`logicapp`) über [geschützte öffentliche Endpunkte eine Verbindung mit Ihrer SQL-Datenbank herstellen](../azure-sql/managed-instance/public-endpoint-overview.md).

<a name="set-up-sql-logic-app-creation-azure-portal"></a>

## <a name="set-up-sql-during-creation-in-the-azure-portal"></a>Einrichten von SQL während der Erstellung im Azure-Portal

Wenn Sie Ihre Logik-App mit dem Ressourcentyp **Logik-App (Standard)** in Azure erstellen, können Sie SQL als Speicheranbieter einrichten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit Ihrem Azure-Konto an.

1. Geben Sie in das Suchfeld des Azure-Portals `logic apps` ein, und wählen Sie **Logik-Apps** aus.

   ![Screenshot, der das Suchfeld des Azure-Portals mit dem Suchtext „Logik-Apps“ und der Kategorie „Logik-Apps“ zeigt](./media/set-up-sql-db-storage-single-tenant-standard-workflows/find-logic-app-resource-template.png)

1. Wählen Sie auf der Seite **Logic Apps** die Option **Hinzufügen** aus.

1. Geben Sie auf der Seite **Logik-App erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen zu Ihrer Logik-App-Ressource ein.

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Das für Ihre Logik-App zu verwendende Azure-Abonnement. |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Die Azure-Ressourcengruppe, in der Sie Ihre Logik-App und zugehörige Ressourcen erstellen. Dieser Ressourcenname muss regionsübergreifend eindeutig sein und darf nur Buchstaben, Ziffern, Bindestriche ( **-** ), Unterstriche ( **_** ), Klammern ( **()** ) und Punkte ( **.** ) enthalten. <p><p>In diesem Beispiel wird eine Ressourcengruppe namens `Fabrikam-Workflows-RG` erstellt. |
   | **Typ** | Ja | **Standard** | Dieser Typ von Logik-App-Ressource wird in der Azure Logic Apps-Umgebung mit einem einzelnen Mandanten ausgeführt und verwendet das [Nutzungs-, Abrechnungs- und Preismodell „Standard“](logic-apps-pricing.md#standard-pricing). |
   | **Name der Logik-App** | Ja | <*logic-app-name*> | Der für Ihre Logik-App zu verwendende Name. Dieser Ressourcenname muss regionsübergreifend eindeutig sein und darf nur Buchstaben, Ziffern, Bindestriche ( **-** ), Unterstriche ( **_** ), Klammern ( **()** ) und Punkte ( **.** ) enthalten. <p><p>In diesem Beispiel wird eine Logik-App namens `Fabrikam-Workflows` erstellt. <p><p>**Hinweis:** Der Name Ihrer Logik-App erhält automatisch das Suffix `.azurewebsites.net`, da die **Logik-App -Ressource (Standard)** von der Azure Logic Apps-Runtime mit einem Mandanten unterstützt wird, die das Azure Functions-Erweiterbarkeitsmodell verwendet und als Erweiterung auf der Azure Functions-Runtime gehostet wird. Azure Functions verwendet die gleiche Benennungskonvention für Apps. |
   | **Veröffentlichen** | Ja | <*deployment-environment*> | Das Bereitstellungsziel für Ihre Logik-App. Standardmäßig ist **Workflow** für die Bereitstellung in einzelinstanzenfähigen Azure Logic Apps ausgewählt. Azure erstellt eine leere Logik-App-Ressource, in der Sie Ihren ersten Workflow hinzufügen müssen. <p><p>**Hinweis**: Derzeit erfordert die Option **Docker-Container** einen [*benutzerdefinierten Standort*](../azure-arc/kubernetes/conceptual-custom-locations.md) in einem Kubernetes-Cluster mit Azure Arc-Unterstützung, den Sie mit [Azure Arc-fähigen Logik-Apps (Preview)](azure-arc-enabled-logic-apps-overview.md) verwenden können. Die Ressourcenstandorte für Ihre Logik-App, der benutzerdefinierte Standort und der Cluster müssen alle identisch sein. |
   | **Region** | Ja | <*Azure-Region*> | Der Standort, der für die Erstellung Ihrer Ressourcengruppe und Ressourcen verwendet werden soll. In diesem Beispiel wird die Beispiel-Logik-App in Azure bereitgestellt, und es wird **USA, Westen** verwendet. <p>– Wenn Sie **Docker-Container** ausgewählt haben, wählen Sie Ihren benutzerdefinierten Standort aus. <p>– Wählen Sie zum Bereitstellen in einer [ASEv3](../app-service/environment/overview.md)-Ressource, die zuerst vorhanden sein muss, diese Umgebungsressource aus der Liste **Region** aus. |
   |||||

   Das folgende Beispiel zeigt die Seite **Logik-App erstellen** mit der Registerkarte **Basics** (Grundlagen):

   ![Screenshot, der das Azure-Portal und die Seite „Logik-App erstellen“ mit der Registerkarte „Grundlagen“ zeigt](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-logic-app-resource-portal.png)

1. Klicken Sie abschließend auf **Weiter: Hosting**. Geben Sie die folgenden Informationen über die Speicherlösung und den Hostingplan, die für Ihre Logik-App verwendet werden sollen, auf der Registerkarte **Hosting** an.

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Speichertyp** | Ja | **SQL und Azure Storage** | Dies ist der Speichertyp, den Sie für workflowbezogene Artefakte und Daten verwenden möchten. <p><p>– Wenn Sie zuvor einen benutzerdefinierten Standort als Region ausgewählt haben, wählen Sie **SQL** aus. <p><p>– Wenn Sie zuvor eine Azure-Region oder einen ASEv3-Standort ausgewählt haben, wählen Sie **SQL und Azure Storage** aus. <p><p>**Hinweis:** Wenn Sie die Bereitstellung in einer Azure-Region durchführen, benötigen Sie weiterhin ein Azure Storage-Konto, das zum einmaligen Hosten der Konfiguration der Logik-App auf der Azure Logic Apps-Plattform verwendet wird. Die Definition, der Zustand, der Ausführungsverlauf und andere Runtimeartefakte des Workflows werden in Ihrer SQL-Datenbank gespeichert. <p><p>Für Bereitstellungen an einem benutzerdefinierten Speicherort, der in einem Azure Arc-Cluster gehostet wird, benötigen Sie nur SQL als Speicheranbieter. |
   | **Speicherkonto** | Ja | <*Azure-storage-account-name*> | Das [Azure Storage-Konto](../storage/common/storage-account-overview.md), das für Speichertransaktionen verwendet werden soll. <p><p>Dieser Ressourcenname muss regionsübergreifend eindeutig sein und 3-24 Zeichen enthalten (nur Ziffern und Kleinbuchstaben). Wählen Sie entweder ein vorhandenes Konto aus, oder erstellen Sie ein neues Konto. <p><p>In diesem Beispiel wird ein Speicherkonto namens `fabrikamstorageacct` erstellt. |
   | **SQL-Verbindungszeichenfolge** | Ja | <*sql-connection-string*> | Dies ist Ihre SQL-Verbindungszeichenfolge, die derzeit nur die SQL-Authentifizierung unterstützt. Open Authorization (OAuth) oder die Authentifizierung mit verwalteten Identitäten werden nicht unterstützt. <p><p>**Hinweis:** Stellen Sie sicher, dass Sie eine richtige Verbindungszeichenfolge eingeben, da das Azure-Portal diese Zeichenfolge nicht für Sie überprüft. |
   | **Plantyp** | Ja | <*hosting-plan*> | Der Hostingplan, der für die Bereitstellung Ihrer Logik-App verwendet werden soll. <p><p>Weitere Informationen finden Sie unter [Hostingpläne und Tarife](logic-apps-pricing.md#standard-pricing). |
   | **Windows-Plan** | Ja | <*plan-name*> | Der zu verwendende Planname. Wählen Sie entweder einen vorhandenen Plannamen aus, oder geben Sie einen Namen für einen neuen Plan an. <p><p>In diesem Beispiel wird der Name `Fabrikam-Service-Plan`verwendet. |
   | **SKU und Größe** | Ja | <*pricing-tier*> | Der [Tarif](../app-service/overview-hosting-plans.md), der für Ihre Logik-App verwendet werden soll. Ihre Auswahl wirkt sich auf die Preise, den Compute, den Arbeitsspeicher und den Speicherplatz aus, die Ihre Logik-App und Ihre Workflows verwenden. <p><p>Wählen Sie **Größe ändern**, um den Standardtarif zu ändern. Sie können dann basierend auf der benötigten Workload andere Tarife auswählen. <p><p>Weitere Informationen finden Sie unter [Hostingpläne und Tarife](logic-apps-pricing.md#standard-pricing). |
   |||||

   Das folgende Beispiel zeigt die Seite **Logik-App erstellen** mit der Registerkarte **Hosting**:

   ![Screenshot, der das Azure-Portal und die Seite „Logik-App erstellen“ mit der Registerkarte „Hosting“ zeigt](./media/set-up-sql-db-storage-single-tenant-standard-workflows/set-up-sql-storage-details.png)

1. Beenden Sie die verbleibenden Erstellungsschritte unter [Erstellen eines Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal](create-single-tenant-workflows-azure-portal.md).

Wenn Sie fertig sind, befinden sich Ihre neue Logik-App-Ressource und der zugehörige Workflow in Azure und verwenden Ihre SQL-Datenbank als Speicheranbieter.

<a name="set-up-sql-local-dev"></a>

## <a name="set-up-sql-for-local-development-in-visual-studio-code"></a>Einrichten von SQL für die lokale Entwicklung in Visual Studio Code

Die folgenden Schritte zeigen, wie Sie SQL als Speicheranbieter für die lokale Entwicklung und Tests in Visual Studio Code einrichten:

1. Richten Sie Ihre Entwicklungsumgebung für die Arbeit mit der Azure Logic Apps-Instanz mit einem Mandanten ein.

   1. Sie müssen die [Voraussetzungen](create-single-tenant-workflows-visual-studio-code.md#prerequisites) erfüllen, um in Visual Studio Code mit der Azure Logic Apps-Erweiterung (Standard) zu arbeiten.

   1. [Richten Sie Visual Studio Code ein](create-single-tenant-workflows-visual-studio-code.md#set-up), um mit der Azure Logic Apps-Erweiterung (Standard) zu arbeiten.

   1. Stellen Sie in Visual Studio Code [eine Verbindung mit Ihrem Azure-Konto her](create-single-tenant-workflows-visual-studio-code.md#connect-azure-account), und [erstellen Sie ein leeres Logik-App-Projekt](create-single-tenant-workflows-visual-studio-code.md#create-project).

1. Öffnen Sie in Visual Studio Code den Explorer-Bereich, sofern er noch nicht geöffnet ist.

1. Bewegen Sie im Bereich „Explorer“ im Stammverzeichnis Ihres Logik-App-Projekts den Mauszeiger über einen beliebigen leeren Bereich unterhalb der Projektdateien und -ordner, öffnen Sie das Kontextmenü, und wählen Sie **Use SQL storage for your Logic App project** (SQL-Speicher für Ihr Logik-App-Projekt verwenden) aus.

   ![Screenshot: Visual Studio Code, Explorer-Bereich und Mauszeiger am Projektstamm im leeren Bereich, geöffnetes Kontextmenü und ausgewählte Option „Use SQL storage for your Logic App project“ (SQL-Speicher für Ihr Logik-App-Projekt verwenden)](./media/set-up-sql-db-storage-single-tenant-standard-workflows/use-sql-storage-logic-app-project.png)

1. Wenn die Eingabeaufforderung angezeigt wird, geben Sie SQL-Verbindungszeichenfolge ein. Sie können sich für die Verwendung einer lokalen SQL Express-Instanz oder einer anderen SQL-Datenbank entscheiden, über die Sie verfügen.

   ![Screenshot: Eingabeaufforderung für Visual Studio Code und SQL-Verbindungszeichenfolge](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

   Nach der Bestätigung erstellt Visual Studio Code die folgende Einstellung in der Datei **local.settings.json** Ihres Projekts. Sie können diese Einstellung jederzeit ändern.  

   ![Screenshot, der Visual Studio Code, das Logik-App-Projekt und das Öffnen der Datei „local.settings.json“ mit der Einstellung der SQL-Verbindungszeichenfolgen zeigt](./media/set-up-sql-db-storage-single-tenant-standard-workflows/local-settings-json-file.png)

<a name="set-up-sql-logic-app-deployment-visual-studio-code"></a>

## <a name="set-up-sql-during-deployment-from-visual-studio-code"></a>Einrichten von SQL während der Bereitstellung über Visual Studio Code

Sie können Ihr Logik-App-Projekt direkt aus Visual Studio Code in Azure veröffentlichen. Diese Aktion stellt Ihr Logik-App-Projekt mithilfe des Ressourcentyps  **Logik-App (Standard)**   bereit.

- Wenn Sie ein Projekt als neue **Logik-App-Ressource (Standard)** in Azure veröffentlichen und SQL als Speicheranbieter verwenden möchten, geben Sie beim Veröffentlichen Ihrer App Ihre SQL-Verbindungszeichenfolge ein. Die ausführlichen Schritte finden Sie unter [Einrichten von SQL für eine neue Logik-App-Bereitstellung](#deploy-new-logic-app-visual-studio-code).

- Wenn Sie Ihre SQL-Einstellungen bereits eingerichtet haben, können Sie Ihr Logik-App-Projekt in einer bereits bereitgestellten **Logik-App-Ressource (Standard)** in Azure veröffentlichen. Diese Aktion überschreibt Ihre vorhandene Logik-App.

  > [!NOTE]
  > Die lokale SQL Express-Instanz funktioniert nicht mit Logik-Apps, die in Azure bereitgestellt und gehostet werden.

<a name="deploy-new-logic-app-visual-studio-code"></a>

### <a name="set-up-sql-for-new-logic-app-standard-resource-deployment"></a>Einrichten von SQL für die Bereitstellung einer neuen Logik-App-Ressource (Standard)

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus.

1. Wählen Sie auf der Symbolleiste des Bereichs  **Azure: Logic Apps (Standard)**   die Option  **Deploy to Logic App** (In Logik-App bereitstellen) aus.

   ![Screenshot, der den Bereich „Azure: Logic Apps (Standard)“ und das ausgewählte Symbol für „In Logik-App bereitstellen“ zeigt](./media/set-up-sql-db-storage-single-tenant-standard-workflows/deploy-to-logic-app.png)

1. Wählen Sie bei entsprechender Aufforderung das Azure-Abonnement aus, das für Ihre Logik-App-Bereitstellung verwendet werden soll.

1. Wählen Sie aus der von Visual Studio Code geöffneten Liste die erweiterte Option für **Create new Logic App (Standard) in Azure Advanced** (Neue Logik-App (Standard) in Azure erstellen – Erweitert) aus. Andernfalls werden Sie nicht aufgefordert, SQL einzurichten.

   ![Screenshot: Ausgewählte Bereitstellungsoption „Neue Logik-App (Standard) in Azure erstellen – Erweitert“](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-create-logic-app-advanced.png)

1. Geben Sie bei Aufforderung einen global eindeutigen Namen für Ihre neue Logik-App an, wobei es sich um den Namen handelt, der für die  **Logik-App** -Ressource (Standard) verwendet werden soll. In diesem Beispiel wird  `Fabrikam-Workflows-App` verwendet.

   ![Screenshot: Eingabeaufforderung für einen global eindeutigen Namen, der für Ihre Logik-App verwendet werden soll](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-logic-app-name.png)

1. Wählen Sie einen Standort für Ihre Logik-App aus. Sie können auch mit der Eingabe beginnen, um die Liste zu filtern.

   - Wählen Sie zum Bereitstellen in Azure die Azure-Region aus, in der Sie die Bereitstellung durchführen möchten. Wenn Sie zuvor eine Ressource der App Service-Umgebung v3 (ASEv3) erstellt haben und dort die Bereitstellung durchführen möchten, wählen Sie Ihre ASEv3-Ressource aus.

   - Wählen Sie zum Bereitstellen in der Azure Arc-fähigen Logic Apps-Instanz Ihren zuvor konfigurierten benutzerdefinierten Standort aus.

   Das folgende Beispiel zeigt die nach **USA, Westen** gefilterte Standortliste.

   ![Screenshot: Aufforderung zum Auswählen eines Bereitstellungsspeicherorts mit verfügbaren Azure-Regionen und benutzerdefiniertem Standort für Azure Arc Bereitstellungen](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-location.png)

1. Wählen Sie den Hostingplantyp für Ihre neue Logik-App aus.

   1. Wenn Sie eine ASEv3-Ressource als App-Standort ausgewählt haben, wählen Sie **App Service Plan** und dann Ihre ASEv3-Ressource aus. Wählen Sie andernfalls **Workflow Standard** aus.

      ![Screenshot: Aufforderung zum Auswählen von „Workflow Standard“ oder „App Service Plan“](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-hosting-plan.png)

   1. Erstellen Sie entweder einen Namen für Ihren Plan, oder wählen Sie einen vorhandenen Plan aus.

      In diesem Beispiel wird  **Neuen App Service-Plan erstellen** ausgewählt, da keine vorhandenen Pläne verfügbar sind.

      ![Screenshot: Aufforderung zum Erstellen eines Namens für den Hostingplan mit ausgewählter Option „Neuen App Service-Plan erstellen“](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-app-service-plan.png)

1. Geben Sie einen Namen für Ihren Hostingplan an, und wählen Sie dann einen Tarif für Ihren ausgewählten Plan aus.

   Weitere Informationen finden Sie unter [Hostingpläne und Tarife](logic-apps-pricing.md#standard-pricing).

1. Wenn Sie zur Eingabe einer Azure-Ressourcengruppe aufgefordert werden, wählen Sie für eine optimale Leistung die gleiche Azure-Ressourcengruppe wie für Ihr Projekt für Ihre Bereitstellung aus.

   > [!NOTE]
   > Obwohl Sie eine andere Ressourcengruppe erstellen oder verwenden können, könnte sich dies negativ auf die Leistung auswirken. Wenn Sie eine andere Ressourcengruppe erstellen oder auswählen, nach der Anzeige der Bestätigungsaufforderung aber abbrechen, wird Ihre Bereitstellung ebenfalls abgebrochen.

1. Wenn Sie aufgefordert werden, ein Speicherkonto für Ihre Logik-App auszuwählen, wählen Sie eine der folgenden Optionen aus:

   - Wenn Sie zuvor einen benutzerdefinierten Standort ausgewählt haben, wählen Sie die Option **SQL** aus.

   - Wenn Sie die Bereitstellung in Azure durchführen möchten, wählen Sie die Option **SQL und Azure Storage** aus.

     > [!NOTE]
     > Diese Option ist nur für Azure-Bereitstellungen erforderlich. In Azure ist Azure Storage erforderlich, um das einmalige Hosting der Konfiguration der Logik-App auf der Azure Logic Apps-Plattform abzuschließen. Der fortlaufende Workflowstatus, der Ausführungsverlauf und andere Runtimeartefakte werden in Ihrer SQL-Datenbank gespeichert.
     >
     > Für Bereitstellungen an einem benutzerdefinierten Speicherort, der in einem Azure Arc-Cluster gehostet wird, benötigen Sie nur SQL als Speicheranbieter.  

1. Wählen Sie für zustandsbehaftete Workflows **Neues Speicherkonto erstellen** oder ein vorhandenes Speicherkonto (falls verfügbar) aus, wenn Sie dazu aufgefordert werden.

   ![Screenshot, der den Bereich „Azure: Logic Apps (Standard)“und eine Aufforderung zum Erstellen oder Auswählen eines Speicherkontos zeigt.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-storage.png)

1. Wählen Sie dann an der Bestätigungsaufforderung für den SQL-Speicher **Ja** aus. Geben Sie an der Eingabeaufforderung für die Verbindungszeichenfolge Ihre SQL-Verbindungszeichenfolge ein.

   > [!NOTE]
   > Stellen Sie sicher, dass Sie eine richtige Verbindungszeichenfolge eingeben, da Visual Studio Code diese Zeichenfolge nicht für Sie überprüft.

   ![Screenshot: Eingabeaufforderung für Visual Studio Code und SQL-Verbindungszeichenfolge](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

1. Beenden Sie die verbleibenden Bereitstellungsschritte unter [Veröffentlichen als neue Ressource „Logik-App (Standard)“](create-single-tenant-workflows-visual-studio-code.md#publish-new-logic-app).

Wenn Sie fertig sind, befinden sich Ihre neue Logik-App-Ressource und der zugehörige Workflow in Azure und verwenden Ihre SQL-Datenbank als Speicheranbieter.

## <a name="validate-deployments"></a>Überprüfen von Bereitstellungen

Nachdem Sie Ihre **Logik-App-Ressource (Standard)** in Azure bereitgestellt haben, können Sie überprüfen, ob Ihre Einstellungen richtig sind:

1. Öffnen Sie Ihre Logik-App-Ressource im [Azure-Portal](https://portal.azure.com).

1. Wählen Sie im Navigationsmenü der Ressource unter **Einstellungen** die Option **Konfiguration** aus.

1. Suchen Sie im Bereich **Konfiguration** unter **Anwendungseinstellungen** die App-Einstellung **Workflows.Sql.ConnectionString**, und überprüfen Sie, ob Ihre SQL-Verbindungszeichenfolge angezeigt wird und richtig ist.

1. Vergewissern Sie sich in Ihrer SQL-Umgebung, dass die SQL-Tabellen mit dem Schemanamen erstellt wurden, der mit „dt“ und „dq“ beginnt.

Der folgende Screenshot zeigt die Tabellen, die von der Azure Logic Apps-Runtime mit einem Mandanten für eine Logik-App-Ressource mit einem einzelnen Workflow erstellt wurden:

![Screenshot, der SQL-Tabellen zeigt, die von der Azure Logic Apps-Runtime mit einem Mandanten erstellt wurden](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-tables-sql.png)

Der Azure Logic Apps-Dienst mit einem Mandanten erstellt auch benutzerdefinierte Tabellentypen. Der folgende Screenshot zeigt benutzerdefinierte Tabellentypen, die von der Azure Logic Apps-Runtime mit einem Mandanten für eine Logik-App-Ressource mit einem einzelnen Workflow erstellt wurden:

![Screenshot, der benutzerdefinierte SQL-Tabellentypen zeigt, die von der Azure Logic Apps-Runtime mit einem Mandanten erstellt wurden](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-user-defined-tables-sql.png)

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md)
- [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md)
