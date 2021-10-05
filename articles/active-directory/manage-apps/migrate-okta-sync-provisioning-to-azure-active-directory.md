---
title: 'Tutorial: Migrieren der Okta-Synchronisierungsbereitstellung zur Azure AD Connect-basierten Synchronisierung'
titleSuffix: Active Directory
description: Erfahren Sie, wie Sie Ihre Okta-Synchronisierungsbereitstellung zur Azure AD Connect-basierten Synchronisierung migrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: d70d3f32093fc2e509d351e0ce194bac1d7490b8
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084149"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization"></a>Tutorial: Migrieren der Okta-Synchronisierungsbereitstellung zur Azure Active Directory Connect-basierten Synchronisierung

Dieser Artikel enthält einen Leitfaden für Organisationen, die derzeit die Benutzerbereitstellung von Okta zu Azure Active Directory (Azure AD) verwenden, zur Migration der Benutzersynchronisierung oder der universellen Synchronisierung zu Azure AD Connect. Dadurch wird die weitere Bereitstellung in Azure AD und Office 365 ermöglicht.

Die Migration von Synchronisierungsplattformen ist keine unerhebliche Änderung. Jeder Schritt des in diesem Artikel beschriebenen Vorgangs muss in Ihrer eigenen Umgebung überprüft werden, bevor Sie Azure AD Connect im Stagingmodus entfernen oder den Agent für die Azure AD-Cloudbereitstellung aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

Beim Wechsel von der Okta-Bereitstellung zu Azure AD haben Kunden zwei Möglichkeiten: Azure AD Connect-Server oder die Azure AD-Cloudbereitstellung. Es empfiehlt sich, den vollständigen [Artikel von Microsoft](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync) zum Vergleich der beiden Produkte zu lesen.

Die Azure AD-Cloudbereitstellung ist der bekannteste Migrationspfad für Okta-Kunden, die die universelle Synchronisierung oder die Benutzersynchronisierung verwenden. Die Agents für die Cloudbereitstellung sind einfach und können auf oder in der Nähe von Domänencontrollern wie den Agents für die Synchronisierung von Okta-Verzeichnissen installiert werden. Es wird nicht empfohlen, sie auf dem gleichen Server zu installieren.

Der Azure AD Connect-Server sollte gewählt werden, wenn Ihre Organisation beim Synchronisieren von Benutzern eine der folgenden Technologien nutzen möchte.

- Gerätesynchronisierung: Azure AD Hybrid Join oder Hello for Business

- Pass-Through-Authentifizierung

- Unterstützung von mehr als 150.000 Objekten

- Unterstützung für Rückschreiben

>[!NOTE]
>Bei der Installation von Azure AD Connect oder der Azure AD-Cloudbereitstellung sind alle Voraussetzungen zu berücksichtigen. Weitere Informationen vor der Installation finden Sie in [diesem Artikel](../hybrid/how-to-connect-install-prerequisites.md).  

## <a name="step-1---confirm-immutableid-attribute-synchronized-by-okta"></a>Schritt 1: Überprüfen des mit Okta synchronisierten Attributs „ImmutableID“

„ImmutableID“ ist das Kernattribut, das zum Verknüpfen von synchronisierten Objekten mit ihren lokalen Entsprechungen verwendet wird. Okta konvertiert die Active Directory-objectGUID eines lokalen Objekts in eine Base64-codierte Zeichenfolge. Diese Zeichenfolge wird dann im Feld „ImmutableID“ in Azure AD mit einem Stempel versehen.

Sie können eine Verbindung mit Azure AD PowerShell herstellen und den aktuellen Wert von „ImmutableID“ überprüfen. Wenn Sie das Azure AD PowerShell-Modul noch nie verwendet haben, führen Sie zunächst `Install-Module AzureAD` in einer administrativen PowerShell-Sitzung und dann die folgenden Befehle aus.

```Powershell
Import-module AzureAD
Connect-AzureAD
```

Wenn Sie das Modul bereits verwenden, erhalten Sie möglicherweise eine Warnung zum Aktualisieren auf die neueste Version, wenn es veraltet ist.

Importieren Sie das Modul nach der Installation, und führen Sie die folgenden Schritte aus, um eine Verbindung mit dem Azure AD-Dienst herzustellen:

1. Geben Sie im Authentifizierungsfenster Ihre Anmeldeinformationen für den globalen Administrator ein.

   ![Abbildung des Importmoduls](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

2. Überprüfen Sie nach dem Herstellen der Verbindung mit dem Mandanten die Einstellungen der ImmutableID-Werte. Im gezeigten Beispiel werden die Okta-Standardwerte von objectGUID für ImmutableID verwendet.

   ![Abbildung der Okta-Standardwerte von objectGUID für ImmutableID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

3. Es gibt mehrere Möglichkeiten, die Konvertierung von objectGUID in Base64 lokal manuell zu überprüfen. Verwenden Sie für die individuelle Überprüfung folgendes Beispiel:

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'

   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![Abbildung der manuellen Änderung der Okta-objectGUID in ImmutableID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="step-2---mass-validation-methods-for-objectguid"></a>Schritt 2: Massenvalidierungsmethoden für objectGUID

Vor dem Wechsel zu Azure AD Connect müssen Sie sicherstellen, dass die ImmutableID-Werte in Azure AD exakt mit den lokalen Werten übereinstimmen.

Im Beispiel werden **alle** lokalen AD-Benutzer erfasst und eine Liste der bereits berechneten objectGUID- und ImmutableID-Werte in eine CSV-Datei exportiert.

1. Führen Sie folgende Befehle in PowerShell auf einem lokalen Domänencontroller aus. 

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![Abbildung der lokalen Befehle auf dem Domänencontroller](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

2. Führen Sie folgende Befehle in einer Azure AD PowerShell-Sitzung aus, um die bereits synchronisierten Werte zu erfassen:

   ```powershell

   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Abbildung der Azure AD PowerShell-Sitzung](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   Überprüfen Sie in beiden Exporten, ob die ImmutableID-Werte für die einzelnen Benutzer übereinstimmen.

   >[!IMPORTANT]
   >Wenn die ImmutableID-Werte in der Cloud nicht mit den objectGUID-Werten übereinstimmen, haben Sie die Standardwerte für die Okta-Synchronisierung geändert. Wahrscheinlich haben Sie ein anderes Attribut ausgewählt, um ImmutableID-Werte zu bestimmen. Bevor Sie mit dem nächsten Abschnitt fortfahren, muss das Quellattribut der ImmutableID-Werte identifiziert werden.      Aktualisieren Sie das Attribut, das über Okta synchronisiert wird, bevor Sie die Okta-Synchronisierung deaktivieren.

## <a name="step-3---install-azure-ad-connect-in-staging-mode"></a>Schritt 3: Installieren von Azure AD Connect im Stagingmodus

Nach der Vorbereitung einer Liste der Quellen und Ziele wird der Azure AD Connect-Server installiert. Bei Verwendung der Azure AD Connect-Cloudbereitstellung können Sie diesen Abschnitt überspringen.

1. Fahren Sie mit dem [Download und der Installation von Azure AD Connect](../hybrid/how-to-connect-install-custom.md) auf dem ausgewählten Server fort. 

2. Wählen Sie auf der Seite **Benutzer werden identifiziert** unter **Wählen Sie aus, wie Benutzer bei Azure AD identifiziert werden sollen** das Optionsfeld **Bestimmtes Attribut auswählen** aus. Wählen Sie dann **mS-DS-ConsistencyGUID** aus, wenn Sie die Okta-Standardwerte nicht geändert haben.

   >[!WARNING]
   >Dies ist der wichtigste Schritt, bevor Sie auf dieser Seite die Option **Weiter** auswählen. Achten Sie darauf, dass das Attribut, das Sie als Quellanker auswählen, das Attribut ist, über das Ihre vorhandenen Azure AD-Benutzer **aktuell** gefüllt werden. Wenn Sie das falsche Attribut auswählen, müssen Sie Azure AD Connect deinstallieren und neu installieren und anschließend diese Option erneut aktivieren.

   ![Abbildung von mS-DS-ConsistencyGUID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

3. Aktivieren Sie auf der Seite **Konfigurieren** das Kontrollkästchen **Stagingmodus aktivieren**. Wählen Sie dann **Installieren** aus.

   ![Abbildung von „Stagingmodus aktivieren“](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

4. Wählen Sie nach Abschluss der Konfiguration die Option **Beenden** aus.

Vor dem Beenden des Stagingmodus müssen Sie überprüfen, ob die ImmutableID-Werte ordnungsgemäß übereinstimmen.

1. Öffnen Sie den Synchronisierungsdienst als **Administrator**.

   ![Abbildung des Öffnens des Synchronisierungsdiensts](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

2. Überprüfen Sie zunächst, ob für die vollständige Synchronisierung mit dem Connectorbereich domain.onmicrosoft.com auf der Registerkarte **Connectors with Flow Updates** (Connectors mit Flowupdates) Benutzer angezeigt werden.

   ![Abbildung eines Connectors mit Flowupdate](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

3. Vergewissern Sie sich anschließend, dass im Export keine Löschungen anstehen. Wählen Sie die Registerkarte **Connectors** aus, und markieren Sie den Connectorbereich „domain.onmicrosoft.com“. Wählen Sie dann **Search Connector Space** (Connectorbereich durchsuchen) aus.

   ![Abbildung von „Search Connector Space“ (Connectorbereich durchsuchen)](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

4. Wählen Sie im Dialogfeld „Search Connector Space“ (Connectorbereich durchsuchen) die Dropdownliste „Bereich“ und dann **Pending Export** (Ausstehender Export) aus.

   ![Abbildung mit ausstehendem Export](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

5. Aktivieren Sie **Löschen**, und wählen Sie dann **Suchen** aus. Wenn alle Objekte ordnungsgemäß übereinstimmen, sollten keine übereinstimmenden Datensätze für Löschungen vorhanden sein. Notieren Sie alle Objekte, deren Löschung aussteht, und die zugehörigen lokalen Werte.

   ![Abbildung der gelöschten übereinstimmenden Datensätze](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

6. Deaktivieren Sie anschließend **Löschen**. Aktivieren Sie **Hinzufügen und Ändern**, und wählen Sie dann „Suchen“ aus. Dadurch sollten Updatefunktionen für alle Benutzer angezeigt werden, die derzeit über Okta mit Azure AD synchronisiert werden. Fügen Sie alle neuen Objekte hinzu, die derzeit nicht über Okta synchronisiert werden, aber in der Organisationseinheitsstruktur vorhanden sind, die bei der Installation von Azure AD Connect ausgewählt wurde.

   ![Abbildung des Hinzufügens eines neuen Objekts](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

7. Durch Doppelklicken auf Updates werden die Daten der Kommunikation zwischen Azure AD Connect und Azure AD angezeigt.

8. Bei Hinzufügungsfunktionen für einen Benutzer, der in Azure AD bereits vorhanden ist, stimmt das zugehörige lokale Konto nicht mit dem Cloudkonto überein, und in AD Connect werden ein neues Objekt erstellt und alle unerwarteten neuen Hinzufügungen aufgezeichnet. Stellen Sie sicher, dass Sie den ImmutableID-Wert in Azure AD korrigieren, bevor Sie den Stagingmodus beenden.

   In diesem Beispiel wurde über Okta das Mail-Attribut mit einem Stempel im Konto des Benutzers versehen, obwohl der lokale Wert nicht ordnungsgemäß ausgefüllt wurde. Bei der Übernahme des Kontos von John Smith in Azure AD Connect, wird das Mail-Attribut aus dem zugehörigen Objekt gelöscht.

   Vergewissern Sie sich, dass die Updates weiterhin alle Attribute enthalten, die in Azure AD erwartet werden. Wenn mehrere Attribute gelöscht werden, müssen Sie diese lokalen AD-Werte möglicherweise manuell auffüllen, bevor Sie den Stagingmodus entfernen.

   ![Abbildung der Auffüllung lokaler AD-Werte](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >Bevor Sie mit dem nächsten Schritt fortfahren, stellen Sie sicher, dass alle Benutzerattribute ordnungsgemäß synchronisiert sind und wie erwartet auf der Registerkarte **Pending Export** (Ausstehender Export) angezeigt werden. Wenn sie gelöscht werden, stellen Sie sicher, dass die ImmutableID-Werte übereinstimmen und sich der Benutzer in einer der ausgewählten Organisationseinheiten für die Synchronisierung befindet.

## <a name="step-4---install-azure-ad-cloud-sync-agents"></a>Schritt 4: Installieren von Agents für die Azure AD-Cloudsynchronisierung

Nach der Vorbereitung einer Liste der Quellen und Ziele werden [Agents für die Azure AD-Cloudsynchronisierung installiert und konfiguriert](../cloud-sync/tutorial-single-forest.md). Bei Verwendung des Azure AD Connect-Servers können Sie diesen Abschnitt überspringen.

## <a name="step-5---disable-okta-provisioning-to-azure-ad"></a>Schritt 5: Deaktivieren der Okta-Bereitstellung für Azure AD

Wenn die Azure AD Connect-Installation überprüft wurde und die ausstehenden Exporte angezeigt werden, kann die Okta-Bereitstellung für Azure AD deaktiviert werden.

1. Navigieren Sie zum Okta-Portal, wählen Sie **Applications** (Anwendungen) und dann Ihre Okta-App aus, die zum Bereitstellen von Benutzern für Azure AD verwendet wird. Öffnen Sie die Registerkarte „Provisioning“ (Bereitstellung), und wählen Sie den Bereich **Integration** aus.

   ![Abbildung des Bereichs „Integration“ in Okta](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

2. Wählen Sie **Edit** (Bearbeiten) aus, deaktivieren Sie die Option **Enable API integration** (API-Integration aktivieren), und wählen Sie **Save** (Speichern) aus.

   ![Abbildung: „Edit“ (Bearbeiten) – „Enable API integration“ (API-Integration aktivieren) in Okta](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Wenn Sie mehrere Office 365-Apps für die Verarbeitung der Bereitstellung für Azure AD verwenden, stellen Sie sicher, dass alle deaktiviert sind.

## <a name="step-6---disable-staging-mode-in-azure-ad-connect"></a>Schritt 6: Deaktivieren des Stagingmodus in Azure AD Connect

Nach dem Deaktivieren der Okta-Bereitstellung kann die Synchronisierung von Objekten mit dem Azure AD Connect-Server beginnen. Bei Verwendung von Agents für die Azure AD-Cloudsynchronisierung können Sie diesen Abschnitt überspringen.

1. Führen Sie auf dem Desktop erneut den Installations-Assistenten aus, und wählen Sie **Konfigurieren** aus.

   ![Abbildung des Azure AD Connect-Servers](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

2. Wählen Sie **Stagingmodus konfigurieren** und dann **Weiter** aus, und geben Sie Ihre Anmeldeinformationen für den globalen Administrator ein.

   ![Abbildung von „Stagingmodus konfigurieren“](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

3. Deaktivieren Sie **Stagingmodus aktivieren**, und wählen Sie „Weiter“ aus.

   ![Abbildung der Deaktivierung von „Stagingmodus aktivieren“](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

4. Wählen Sie **Konfigurieren** aus, um den Vorgang fortzusetzen.

   ![Abbildung von „Bereit zur Konfiguration“](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

5. Öffnen Sie nach Abschluss der Konfiguration den Synchronisierungsdienst als **Administrator**. Zeigen Sie den Export auf dem Connector domain.onmicrosoft.com an. Vergewissern Sie sich, dass alle Hinzufügungen, Aktualisierungen und Löschungen wie erwartet ausgeführt werden.

   ![Abbildung der Überprüfung des Synchronisierungsdiensts](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

Damit haben Sie die Migration zur Azure AD Connect-basierten Bereitstellung erfolgreich ausgeführt. Updates und Erweiterungen des Funktionsumfangs von Azure AD Connect können durch erneutes Ausführen des Installations-Assistenten durchgeführt werden.

## <a name="step-7---enable-cloud-sync-agents"></a>Schritt 7: Aktivieren von Agents für die Cloudsynchronisierung

Nach dem Deaktivieren der Okta-Bereitstellung kann die Synchronisierung von Objekten mit dem Agent für die Azure AD-Cloudsynchronisierung beginnen. Kehren Sie dazu zum [Azure AD-Portal](https://aad.portal.azure.com/) zurück.

1. Ändern Sie das **Konfigurationsprofil** in **Aktiviert**.

2. Kehren Sie nach der Aktivierung zum Bereitstellungsmenü zurück, und wählen Sie **Protokolle** aus.

3. Überprüfen Sie, ob vorhandene Objekte mit dem Bereitstellungsconnector ordnungsgemäß aktualisiert wurden. Die Agents für die Cloudsynchronisierung sind nicht destruktiv. Wenn eine Übereinstimmung nicht ordnungsgemäß erfolgt ist, treten bei den Updates Fehler auf.

4. Wenn ein Benutzer nicht übereinstimmt, nehmen Sie die erforderlichen Updates vor, um die ImmutableID-Werte zu binden, und starten Sie dann die Synchronisierung der Cloudbereitstellung neu.

## <a name="next-steps"></a>Nächste Schritte

- [Migrieren von Anwendungen von Okta zu Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)

- [Migrieren des Okta-Verbunds zur verwalteten Azure AD-Authentifizierung](migrate-okta-federation-to-azure-active-directory.md)

- [Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
