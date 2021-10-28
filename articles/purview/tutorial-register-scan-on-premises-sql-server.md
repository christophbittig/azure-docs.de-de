---
title: 'Tutorial: Registrieren und Überprüfen einer lokalen SQL Server-Instanz'
description: In diesem Tutorial wird beschrieben, wie Sie eine lokale SQL Server-Instanz bei Purview registrieren und den Server mit einer selbstgehosteten IR überprüfen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5fc1369c20130d8c689abe853e321fc1507fa414
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249569"
---
# <a name="tutorial-register-and-scan-an-on-premises-sql-server"></a>Tutorial: Registrieren und Überprüfen einer lokalen SQL Server-Instanz

Azure Purview wurde für die Verbindungsherstellung mit Datenquellen entwickelt, damit Sie vertrauliche Daten verwalten, die Datenermittlung vereinfachen und die richtige Nutzung von Daten sicherstellen können. Mit Purview können Verbindungen mit Quellen in Ihrer gesamten Datenlandschaft hergestellt werden, z. B. Multi-Cloud- und lokale Datenquellen. Für dieses Szenario verwenden Sie eine selbstgehostete Integration Runtime, um eine Verbindung mit Daten auf einer lokalen SQL Server-Instanz herzustellen. Anschließend nutzen Sie Azure Purview, um diese Daten zu überprüfen und zu klassifizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Anmelden bei Purview Studio
> * Erstellen einer Sammlung in Azure Purview
> * Erstellen einer selbstgehosteten Integration Runtime
> * Speichern von Anmeldeinformationen in Azure Key Vault
> * Registrieren einer lokalen SQL Server-Instanz bei Purview
> * Überprüfen der SQL Server-Instanz
> * Durchsuchen Ihres Datenkatalogs zum Anzeigen von Ressourcen auf Ihrer SQL Server-Instanz

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine aktive Instanz von [Azure Key Vault](../key-vault/general/quick-create-portal.md).
- Ein Azure Purview-Konto. Falls Sie noch kein Konto haben, können Sie für die Erstellung unsere [Schnellstartanleitung](create-catalog-portal.md) befolgen.
- Eine [lokale SQL Server-Instanz](https://www.microsoft.com/sql-server/sql-server-downloads).

## <a name="sign-in-to-purview-studio"></a>Anmelden bei Purview Studio

Für die Interaktion mit Purview stellen Sie über das Azure-Portal eine Verbindung mit [Purview Studio](https://web.purview.azure.com/resource/) her. Sie gelangen zu Purview Studio, indem Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Purview-Ressource wechseln und auf der Übersichtsseite die Kachel **Purview Studio öffnen** auswählen.

:::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/open-purview-studio.png" alt-text="Screenshot: Purview-Fenster im Azure-Portal mit hervorgehobener Schaltfläche „Purview Studio“" border="true":::

## <a name="create-a-collection"></a>Erstellen einer Sammlung

Sammlungen werden in Azure Purview verwendet, um Ressourcen und Quellen in einer benutzerdefinierten Hierarchie anzuordnen, damit eine Verbesserung der Strukturierung und Ermittelbarkeit erzielt werden kann. Darüber hinaus werden sie auch zum Verwalten des Zugriffs in Purview verwendet. In diesem Tutorial erstellen wir eine Sammlung für Ihre SQL Server-Datenquelle und alle zugehörigen Ressourcen. Im Tutorial werden keine Informationen zum Zuweisen von Berechtigungen für andere Benutzer behandelt. Weitere Informationen hierzu finden Sie in unserem [Leitfaden zu Berechtigungen in Purview](catalog-permissions.md).

### <a name="check-permissions"></a>Überprüfen Sie die Berechtigungen.

Zum Erstellen und Verwalten von Sammlungen in Purview muss Ihnen innerhalb von Purview die Rolle **Sammlungsadministrator** zugewiesen sein. Sie können diese Berechtigungen in [Purview Studio](use-purview-studio.md) überprüfen.

1. Wählen Sie im linken Bereich **Data Map > Sammlungen** aus, um die Seite für die Sammlungsverwaltung zu öffnen.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/find-collections.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und ausgewählter Registerkarte „Sammlungen“" border="true":::

1. Wählen Sie Ihre Stammsammlung aus. Diese Stammsammlung ist die oberste Sammlung in Ihrer Sammlungsliste und hat den gleichen Namen wie Ihre Purview-Ressource. In unserem Beispiel unten ist dies „Purview Account“ (Purview-Konto).

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-root-collection.png" alt-text="Screenshot: Purview Studio mit geöffneter Datenzuordnung und hervorgehobener Stammsammlung" border="true":::

1. Wählen Sie im Sammlungsfenster **Rollenzuweisungen** aus.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/role-assignments.png" alt-text="Screenshot: Purview Studio mit geöffneter Datenzuordnung und hervorgehobener Registerkarte „Rollenzuweisungen“" border="true":::

1. Um eine Sammlung erstellen zu können, müssen Sie unter „Rollenzuweisungen“ in der Liste mit den Sammlungsadministratoren enthalten sein. Wenn Sie die Purview-Ressource erstellt haben, sollten Sie bereits als Sammlungsadministrator in der Stammsammlung aufgeführt werden. Andernfalls müssen Sie sich an den Sammlungsadministrator wenden, damit Ihnen die entsprechende Berechtigung erteilt wird.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/collection-admins.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und hervorgehobenem Abschnitt „Sammlungsadministrator“" border="true":::

### <a name="create-the-collection"></a>Erstellen der Sammlung

1. Wählen Sie **+ Sammlung hinzufügen** aus. Auch hier können Sammlungen nur von [Sammlungsadministratoren](#check-permissions) verwaltet werden.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-add-a-collection.png" alt-text="Screenshot: Purview Studio-Fenster für neue Sammlungen mit hervorgehobenen Schaltflächen zum Hinzufügen einer Sammlung" border="true":::

1. Geben Sie im rechten Bereich den Namen und eine Beschreibung für die Sammlung ein. Bei Bedarf können Sie der neuen Sammlung auch Benutzer oder Gruppen als Sammlungsadministratoren hinzufügen.
1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/create-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster „Neue Sammlung“, mit ausgewähltem Anzeigenamen und Sammlungsadministrator und hervorgehobener Schaltfläche „Erstellen“" border="true":::

1. Die Informationen zur neuen Sammlung werden auf der Seite angezeigt.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/created-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster für die neu erstellte Sammlung" border="true":::

## <a name="create-a-self-hosted-integration-runtime"></a>Erstellen einer selbstgehosteten Integration Runtime

Die selbstgehostete Integration Runtime (SHIR) ist die Computeinfrastruktur, die von Purview für die Verbindungsherstellung mit lokalen Datenquellen verwendet wird. Die SHIR wird heruntergeladen und auf einem Computer installiert, der sich in demselben Netzwerk wie die lokale Datenquelle befindet.

In diesem Tutorial wird vorausgesetzt, dass der Computer, auf dem Sie Ihre selbstgehostete Integration Runtime installieren, Netzwerkverbindungen mit dem Internet herstellen kann. Über diese Verbindung kann die SHIR die Kommunikation zwischen Ihrer Quelle und Azure Purview durchführen. Wenn Ihr Computer über eine Firewall mit Einschränkungen verfügt oder falls Sie Ihre Firewall schützen möchten, hilft Ihnen der Abschnitt zu den [Netzwerkanforderungen für die selbstgehostete Integration Runtime](manage-integration-runtimes.md#networking-requirements) weiter.

1. Wählen Sie auf der Startseite von Purview Studio im linken Navigationsbereich die Option **Data Map** aus.

1. Wählen Sie im linken Bereich unter **Quellverwaltung** die Option **Integration Runtimes** und dann **+ Neu** aus.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="Auswahl der Schaltfläche „Integration Runtimes“":::

1. Wählen Sie auf der Seite **Integration Runtime-Setup** die Option **Selbstgehostet** aus, um eine selbstgehostete IR zu erstellen, und wählen Sie dann **Weiter** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-self-hosted-ir.png" alt-text="Erstellen einer neuen SHIR":::

1. Geben Sie einen Namen für Ihre IR ein, und wählen Sie Erstellen aus.

1. Führen Sie auf der Seite **Integration Runtime settings** (Integration Runtime-Einstellungen) die Schritte im Abschnitt **Manual setup** (Manuelles Setup) aus. Sie müssen die Integration Runtime von der Downloadwebsite auf einen virtuellen oder physischen Computer herunterladen, der sich in demselben Netzwerk wie Ihre lokale SQL Server-Instanz befindet. Informationen zur Art des benötigten Computers finden Sie in unserem [Leitfaden zur Verwaltung von Integration Runtimes](manage-integration-runtimes.md#prerequisites).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/integration-runtime-settings.png" alt-text="Abrufen des Schlüssels":::

   - Kopieren Sie den Authentifizierungsschlüssel, und fügen Sie ihn ein.

   - Laden Sie die selbstgehostete Integration Runtime von [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) auf einen lokalen Windows-Computer herunter. Führen Sie das Installationsprogramm aus. Selbstgehostete Integration Runtime-Versionen wie 5.4.7803.1 und 5.6.7795.1 werden unterstützt. 

   - Fügen Sie auf der Seite **Integration Runtime (selbstgehostet) registrieren** einen der beiden Schlüssel ein, die Sie zuvor gespeichert haben, und wählen Sie **Registrieren** aus.

     :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/register-integration-runtime.png" alt-text="Einfügen des Schlüssels":::

   - Klicken Sie auf der Seite **Neuer Knoten der Integrationslaufzeit (selbstgehostet)** auf **Fertig stellen**.

1. Nachdem die Registrierung der selbstgehosteten Integration Runtime erfolgreich abgeschlossen wurde, wird das folgende Fenster angezeigt:

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/successfully-registered.png" alt-text="Erfolgreiche Registrierung":::

## <a name="set-up-sql-authentication"></a>Einrichten der SQL-Authentifizierung

Es gibt nur eine Möglichkeit, die Authentifizierung für eine lokale SQL Server-Datenquelle einzurichten:

- SQL-Authentifizierung

### <a name="sql-authentication"></a>SQL-Authentifizierung

Das SQL-Konto muss Zugriff auf die **Masterdatenbank** besitzen. Dies liegt daran, dass sich `sys.databases` in der Datenbank befindet. Der Purview-Scanner muss `sys.databases` durchlaufen, um alle SQL-Datenbankinstanzen auf dem Server zu finden.

#### <a name="create-a-new-login-and-user"></a>Erstellen einer neuen Anmeldung und einen neuen Benutzers

Führen Sie die folgenden Schritte aus, wenn Sie eine neue Anmeldung und einen neuen Benutzer erstellen möchten, der Ihre SQL Server-Datenquelle überprüfen (scannen) kann:

> [!Note]
> Alle nachfolgenden Schritte können mit dem [hier](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql) bereitgestellten Code ausgeführt werden.

1. Navigieren Sie zu SQL Server Management Studio (SSMS), stellen Sie eine Verbindung mit dem Server her, navigieren Sie zu „Sicherheit“, wählen und halten Sie „Anmeldung“ (oder über rechte Maustaste), und erstellen Sie eine neue Anmeldung. Wählen Sie „SQL Server-Authentifizierung“ aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Eine neue Anmeldung und einen neuen Benutzer erstellen":::

1. Wählen Sie „Serverrollen“ im linken Navigationsbereich aus, und stellen Sie sicher, dass die Rolle „public“ zugewiesen ist.

1. Wählen Sie „Benutzerzuordnung“ im linken Navigationsbereich aus, wählen Sie alle Datenbanken in der Zuordnung aus, und wählen Sie dann die folgende Datenbankrolle aus: **db_datareader**.

1. Klicken Sie zum Speichern auf **OK**.

1. Navigieren Sie erneut zu dem Benutzer, den Sie erstellt haben, indem Sie den Benutzer auswählen und halten (oder rechte Maustaste verwenden) und die Option **Eigenschaften** auswählen. Geben Sie ein neues Kennwort ein, und bestätigen Sie es. Wählen Sie die Option „Altes Kennwort angeben“ aus, und geben Sie das alte Kennwort ein. **Nachdem Sie eine neue Anmeldung erstellt haben, müssen Sie Ihr Kennwort ändern.**

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/change-password.png" alt-text="Kennwort ändern":::

#### <a name="create-a-key-vault-credential"></a>Erstellen von Key Vault-Anmeldeinformationen

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor. Wählen Sie **Einstellungen > Geheimnisse** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Auswahl von „Geheimnisse“ im Menü auf der linken Seite":::

1. Wählen Sie die Option **+ Generieren/Importieren** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-generate-import.png" alt-text="Auswahl von „Generieren/Importieren“ im oberen Menü":::

1. Wählen Sie **Manuell** aus, und geben Sie **Name** und **Wert** als *Kennwort* Ihrer SQL Server-Anmeldung ein, um die Optionen für den Upload anzuzeigen. Vergewissern Sie sich, dass **Aktiviert** auf **Ja** festgelegt ist. Stellen Sie beim Festlegen eines Aktivierungs- und Ablaufdatums sicher, dass das aktuelle Datum zwischen diesen beiden Datumsangaben liegt, da Sie die Anmeldeinformationen sonst nicht verwenden können.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-credential-secret.png" alt-text="Hinzufügen von Werten zu Key Vault-Anmeldeinformationen":::

1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Navigieren Sie in [Azure Purview Studio](#sign-in-to-purview-studio) im Menü auf der linken Seite zur Seite **Verwaltung**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-management.png" alt-text="Auswahl der Seite „Verwaltung“ im linken Menü":::

1. Wählen Sie die Seite **Anmeldeinformationen** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-credentials.png" alt-text="Hervorgehobene Schaltfläche „Anmeldeinformationen“ auf der Seite „Verwaltung“":::

1. Wählen Sie auf der Seite **Anmeldeinformationen** die Option **Manage Key Vault Connections** (Key Vault-Verbindungen verwalten) aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/manage-key-vault-connections.png" alt-text="Verwalten von Azure Key Vault-Verbindungen":::

1. Wählen Sie auf der Seite zum Verwalten Ihrer Key Vault-Verbindungen die Option **+ Neu** aus.

1. Geben Sie die erforderlichen Informationen ein, und wählen Sie anschließend **Erstellen** aus.

1. Vergewissern Sie sich, dass die Zuordnung Ihrer Key Vault-Instanz zu Ihrem Azure Purview-Konto wie im folgenden Beispiel erfolgreich war:

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-kv-connections.png" alt-text="Anzeigen der Azure Key Vault-Verbindungen zur Bestätigung":::

1. Erstellen Sie neue Anmeldeinformationen für SQL Server, indem Sie **+ Neu** auswählen.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-new.png" alt-text="Auswählen von „+ Neu“ zum Erstellen von Anmeldeinformationen":::

1. Geben Sie die erforderlichen Informationen an. Wählen Sie die **Authentifizierungsmethode** und eine **Key Vault-Verbindung** aus, von der Sie ein Geheimnis auswählen möchten.

1. Wählen Sie **Erstellen** aus, nachdem Sie alle Details eingegeben haben.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/new-credential.png" alt-text="Neue Anmeldeinformationen":::

1. Vergewissern Sie sich, dass Ihre neuen Anmeldeinformationen in der Liste angezeigt werden und einsatzbereit sind.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-credentials.png" alt-text="Anzeigen von Anmeldeinformationen":::

## <a name="register-sql-server"></a>Registrieren der SQL Server-Instanz

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Purview-Konto, und wählen Sie die Option [Purview Studio](#sign-in-to-purview-studio) aus.

1. Wählen Sie im linken Navigationsbereich unter „Quellen und Überprüfen“ die Option **Integration Runtimes** aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls nicht, führen Sie die [hier](manage-integration-runtimes.md) beschriebenen Schritte aus, um eine selbstgehostete Integration Runtime für Überprüfungen auf einer lokalen oder Azure-VM mit Zugriff auf Ihr lokales Netzwerk zu erstellen.

1. Wählen Sie im linken Navigationsbereich die Option **Data Map** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie **SQL Server** und dann **Weiter** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Die SQL Server-Datenquelle einrichten":::

1. Geben Sie einen Anzeigenamen und Serverendpunkt an, und wählen Sie dann **Fertig stellen** aus, um die Datenquelle zu registrieren. Hat Ihre SQL Server-Datenquelle z. B. den FQDN **foobar.database.windows.net**, geben Sie *foobar* als Serverendpunkt ein.

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in Purview Studio die Registerkarte **Data Map** aus.

1. Wählen Sie die von Ihnen registrierte SQL Server-Quelle aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus.

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Tabellen festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Purview- oder SQL-Datenquelle nicht weiter nutzen möchten, können Sie die unten angegebenen Schritte ausführen, um die Integration Runtime, SQL-Anmeldeinformationen und Purview-Ressourcen zu löschen.

### <a name="remove-shir-from-purview"></a>Entfernen der SHIR aus Purview

1. Wählen Sie auf der Startseite von [Purview Studio](https://web.purview.azure.com/resource/) im linken Navigationsbereich die Option **Data Map** aus.

1. Wählen Sie im linken Bereich unter **Quellverwaltung** die Option **Integration Runtimes** aus.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="Auswahl der Schaltfläche „Integration Runtimes“":::

1. Aktivieren Sie das Kontrollkästchen neben Ihrer Integration Runtime, und wählen Sie dann die Schaltfläche **Löschen** aus.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/delete-integration-runtime.png" alt-text="Hervorhebung: Kontrollkästchen neben Integration Runtime und Schaltfläche „Löschen“":::

1. Wählen Sie im nächsten Fenster die Option **Bestätigen** aus, um den Löschvorgang zu bestätigen.

1. Das Fenster wird automatisch aktualisiert, und Ihre SHIR sollte unter „Integration Runtimes“ nicht mehr aufgeführt sein.

### <a name="uninstall-self-hosted-integration-runtime"></a>Deinstallieren der selbstgehosteten Integration Runtime

1. Melden Sie sich bei dem Computer an, auf dem Ihre selbstgehostete Integration Runtime installiert ist.
1. Öffnen Sie die Systemsteuerung, und suchen Sie unter *Programm deinstallieren* nach „Microsoft Integration Runtime“.

1. Deinstallieren Sie die vorhandene Integration Runtime.

> [!IMPORTANT] 
> Wählen Sie Ja im folgenden Prozess aus. Bewahren Sie beim Deinstallationsvorgang keine Daten auf.

:::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-yes.png" alt-text="Screenshot: Schaltfläche „Ja“ zum Löschen aller Benutzerdaten aus der Integration Runtime":::

### <a name="remove-sql-credentials"></a>Entfernen der SQL-Anmeldeinformationen

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und dann zu der Key Vault-Ressource, auf der Sie Ihre Purview-Anmeldeinformationen gespeichert haben.

1. Wählen Sie im Menü auf der linken Seite unter **Einstellungen** die Option **Geheimnisse** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Auswahl von „Geheimnisse“ im linken Menü in Azure Key Vault":::

1. Wählen Sie das Geheimnis für die SQL Server-Anmeldeinformationen aus, das Sie für dieses Tutorial erstellt haben.
1. Wählen Sie **Löschen** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete-credential.png" alt-text="Löschen des Geheimnisses im oberen Menü in Azure Key Vault":::

1. Wählen Sie **Ja** aus, um die Ressourcen endgültig zu löschen.

### <a name="delete-purview-account"></a>Löschen eines Purview-Kontos

Führen Sie die folgenden Schritte aus, wenn Sie Ihr Purview-Konto nach Abschluss dieses Tutorials löschen möchten.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und dann zu Ihrem Purview-Konto.

1. Wählen Sie oben auf der Seite die Schaltfläche **Löschen** aus.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete.png" alt-text="Auswahl der Schaltfläche „Löschen“ auf der Seite mit dem Purview-Konto im Azure-Portal":::

1. Wenn der Prozess abgeschlossen ist, erhalten Sie im Azure-Portal eine Benachrichtigung.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden von Purview-REST-APIs](tutorial-using-rest-apis.md)