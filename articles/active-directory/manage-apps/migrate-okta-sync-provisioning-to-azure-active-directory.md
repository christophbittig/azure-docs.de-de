---
title: Tutorial zum Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung
titleSuffix: Active Directory
description: In diesem Tutorial erfahren Sie, wie Sie Ihre Okta-Synchronisierungsbereitstellung zur Azure AD Connect-basierten Synchronisierung migrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 0902fbd761cb66eb5dbee4a5e3406ecba688c0b7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451481"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-ad-connect-based-synchronization"></a>Tutorial: Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung migrieren

In diesem Tutorial erfahren Sie, wie Ihre Organisation aktuell die Benutzerbereitstellung von Okta zu Azure Active Directory (AD) und die Benutzersynchronisierung oder die universelle Synchronisierung zu Azure AD Connect migrieren kann. Durch diese Funktion wird die weitere Bereitstellung in Azure AD und Office 365 ermöglicht.

Die Migration von Synchronisierungsplattformen ist keine unerhebliche Änderung. Jeder Schritt des in diesem Artikel beschriebenen Vorgangs muss in Ihrer eigenen Umgebung überprüft werden, bevor Sie Azure AD Connect im Stagingmodus entfernen oder den Agent für die Azure AD-Cloudbereitstellung aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie von der Okta-Bereitstellung zu Azure AD wechseln, haben Sie zwei Möglichkeiten. Sie können entweder einen Azure AD Connect-Server oder Azure AD Cloudbereitstellung verwenden. Eine Übersicht über die Unterschiede zwischen den beiden Optionen finden sie im [Vergleichsartikel von Microsoft](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync).

Die Azure AD-Cloudbereitstellung ist der bekannteste Migrationspfad für Okta-Kunden, die die universelle Synchronisierung oder Benutzersynchronisierung verwenden. Die Cloudbereitstellungs-Agents sind einfach. Sie können auf oder in der Nähe von Domänencontrollern wie den Agents für die Synchronisierung von Okta-Verzeichnissen installiert werden. Installieren Sie sie nicht auf demselben Server.

Verwenden Sie einen Azure AD Connect-Server, wenn Ihre Organisation beim Synchronisieren von Benutzern eine der folgenden Technologien nutzen möchte:

- Gerätesynchronisierung: Azure AD Hybrid Join oder Hello for Business
- Pass-Through-Authentifizierung
- Unterstützung von mehr als 150.000 Objekten
- Unterstützung für Rückschreiben

>[!NOTE]
>Bei der Installation von Azure AD Connect oder der Azure AD-Cloudbereitstellung sind alle Voraussetzungen zu berücksichtigen. Bevor Sie die Installation fortsetzen, finden Sie weitere Informationen unter [Voraussetzungen für Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md).

## <a name="confirm-immutableid-attribute-synchronized-by-okta"></a>Überprüfen des mit Okta synchronisierten Attributs „ImmutableID“

„ImmutableID“ ist das Kernattribut, das zum Verknüpfen von synchronisierten Objekten mit ihren lokalen Entsprechungen verwendet wird. Okta konvertiert die Active Directory-objectGUID eines lokalen Objekts in eine Base64-codierte Zeichenfolge. Diese Zeichenfolge wird dann im Feld „ImmutableID“ in Azure AD standardmäßig mit einem Stempel versehen.

Sie können eine Verbindung mit Azure AD PowerShell herstellen und den aktuellen Wert von „ImmutableID“ überprüfen. Wenn Sie das Azure AD PowerShell-Modul noch nie verwendet haben, führen Sie zunächst `Install-Module AzureAD` in einer administrativen PowerShell-Sitzung und dann die folgenden Befehle aus:

```Powershell
Import-module AzureAD
Connect-AzureAD
```

Wenn Sie das Modul bereits verwenden, erhalten Sie möglicherweise eine Warnung zum Aktualisieren auf die neueste Version, wenn es veraltet ist.

Importieren Sie das Modul nach der Installation und führen Sie die folgenden Schritte aus, um eine Verbindung mit dem Azure AD-Dienst herzustellen:

1. Geben Sie im Authentifizierungsfenster Ihre Anmeldeinformationen für den globalen Administrator ein.

   ![Screenshot von import-module.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

1. Überprüfen Sie nach dem Herstellen der Verbindung mit dem Mandanten die Einstellungen der ImmutableID-Werte. Im gezeigten Beispiel werden die Okta-Standardwerte von objectGUID für ImmutableID verwendet.

   ![Screenshot der Okta-Standardwerte von objectGUID für ImmutableID.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

1. Es gibt mehrere Möglichkeiten, die Konvertierung von objectGUID in Base64 lokal manuell zu überprüfen. Verwenden Sie für die individuelle Überprüfung folgendes Beispiel:

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'
   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![Screenshot der manuellen Änderung der Okta-objectGUID in ImmutableID.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="mass-validation-methods-for-objectguid"></a>Massenvalidierungsmethoden für objectGUID

Vor dem Wechsel zu Azure AD Connect müssen Sie sicherstellen, dass die ImmutableID-Werte in Azure AD exakt mit den lokalen Werten übereinstimmen.

Im Beispiel werden *alle* lokalen Azure AD-Benutzer erfasst und eine Liste der bereits berechneten objectGUID- und ImmutableID-Werte in eine CSV-Datei exportiert.

1. Führen Sie folgende Befehle in PowerShell auf einem lokalen Domänencontroller aus:

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![Screenshot der Befehle auf einem lokalen Domänencontroller.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

1. Führen Sie folgende Befehle in einer Azure AD PowerShell-Sitzung aus, um die bereits synchronisierten Werte zu erfassen:

   ```powershell
   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Screenshot einer Azure AD PowerShell-Sitzung.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   Überprüfen Sie in beiden Exporten, ob die ImmutableID-Werte für die einzelnen Benutzer übereinstimmen.

   >[!IMPORTANT]
   >Wenn die ImmutableID-Werte in der Cloud nicht mit den objectGUID-Werten übereinstimmen, haben Sie die Standardwerte für die Okta-Synchronisierung geändert. Wahrscheinlich haben Sie ein anderes Attribut ausgewählt, um ImmutableID-Werte zu bestimmen. Bevor Sie mit dem nächsten Abschnitt fortfahren, muss das Quellattribut der ImmutableID-Werte identifiziert werden. Aktualisieren Sie das Attribut, das über Okta synchronisiert wird, bevor Sie die Okta-Synchronisierung deaktivieren.

## <a name="install-azure-ad-connect-in-staging-mode"></a>Installieren von Azure AD Connect im Stagingmodus

Nach der Vorbereitung einer Liste der Quellen und Ziele wird der Azure AD Connect-Server installiert. Bei Verwendung der Azure AD Connect-Cloudbereitstellung können Sie diesen Abschnitt überspringen.

1. Fahren Sie mit dem [Download und der Installation von Azure AD Connect](../hybrid/how-to-connect-install-custom.md) auf dem ausgewählten Server fort.

1. Wählen Sie auf der Seite **Benutzer werden identifiziert** unter **Wählen Sie aus, wie Benutzer bei Azure AD identifiziert werden sollen** die Option **Bestimmtes Attribut auswählen** aus. Wählen Sie dann **mS-DS-ConsistencyGUID** aus, wenn Sie die Okta-Standardwerte nicht geändert haben.

   >[!WARNING]
   >Dies ist der wichtigste Schritt auf dieser Seite. Bevor Sie **Weiter** auswählen, stellen Sie sicher, dass das Attribut, das Sie als Quellanker auswählen, das Attribut ist, über das Ihre vorhandenen Azure AD-Benutzer *aktuell* gefüllt werden. Wenn Sie das falsche Attribut auswählen, müssen Sie Azure AD Connect deinstallieren und neu installieren und anschließend diese Option erneut aktivieren.
   
   ![Screenshot von mS-DS-ConsistencyGuid.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

1. Aktivieren Sie auf der Seite **Konfigurieren** das Kontrollkästchen **Stagingmodus aktivieren**. Wählen Sie dann **Installieren** aus.

   ![Screenshot des Kontrollkästchens „Stagingmodus aktivieren“.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

1. Wählen Sie nach Abschluss der Konfiguration die Option **Beenden** aus.

   Vergewissern Sie sich vor dem Beenden des Stagingmodus, dass die ImmutableID-Werte ordnungsgemäß übereinstimmen.

1. Öffnen Sie den **Synchronisierungsdienst** als Administrator.

   ![Screenshot vom Öffnen des Synchronisierungsdienstes.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

1. Überprüfen Sie, ob für die **vollständige Synchronisierung** mit dem Connectorbereich domain.onmicrosoft.com auf der Registerkarte **Connectors with Flow Updates (Connectors mit Flowupdates)** Benutzer angezeigt werden.

   ![Screenshot der Registerkarte „Connectors with Flow Updates“.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

1. Vergewissern Sie sich, dass im Export keine Löschungen anstehen. Wählen Sie die Registerkarte **Connectors** aus, und markieren Sie den Connectorbereich „domain.onmicrosoft.com“. Wählen Sie dann **Search Connector Space** (Connectorbereich durchsuchen) aus.

   ![Screenshot der Aktion „Search Connector Space (Connectorbereich suchen)“.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

1. Wählen Sie im Dialogfeld **Search Connector Space (Connectorbereich durchsuchen)** die Dropdownliste **Bereich** und dann **Pending Export (Ausstehender Export)** aus.

   ![Screenshot von „Pending Export (Ausstehender Export)“.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

1. Wählen Sie **Löschen** und anschließend **Suchen** aus. Wenn alle Objekte ordnungsgemäß übereinstimmen, sollten keine übereinstimmenden Datensätze für **Löschungen** vorhanden sein. Notieren Sie alle Objekte, deren Löschung aussteht, und die zugehörigen lokalen Werte.

   ![Screenshot der gelöschten übereinstimmenden Datensätze.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

1. Deaktivieren Sie **Löschen**. Aktivieren Sie **Hinzufügen** und **Ändern**, und wählen Sie dann „Suchen“ aus. Dadurch sollten Updatefunktionen für alle Benutzer angezeigt werden, die derzeit über Okta mit Azure AD synchronisiert werden. Fügen Sie alle neuen Objekte hinzu, die derzeit nicht über Okta synchronisiert werden, aber in der Organisationseinheitsstruktur vorhanden sind, die bei der Installation von Azure AD Connect ausgewählt wurde.

   ![Screenshot vom Hinzufügen eines neuen Objekts.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

1. Durch Doppelklicken auf Updates werden die Daten der Kommunikation zwischen Azure AD Connect und Azure AD angezeigt.

1. Bei **Hinzufügungsfunktionen** für einen Benutzer, der in Azure AD bereits vorhanden ist, stimmt das zugehörige lokale Konto nicht mit dem Cloudkonto überein. In AD Connect wird dann ein neues Objekt erstellt und alle unerwarteten neuen Hinzufügungen werden aufzeichnet. Stellen Sie sicher, dass Sie den ImmutableID-Wert in Azure AD korrigieren, bevor Sie den Stagingmodus beenden.

   In diesem Beispiel wurde über Okta das **Mail**-Attribut mit einem Stempel im Konto des Benutzers versehen, obwohl der lokale Wert nicht ordnungsgemäß ausgefüllt wurde. Bei der Übernahme des Kontos von John Smith in Azure AD Connect, wird das **Mail**-Attribut aus dem zugehörigen Objekt gelöscht.

   Vergewissern Sie sich, dass die Updates weiterhin alle Attribute enthalten, die in Azure AD erwartet werden. Wenn mehrere Attribute gelöscht werden, müssen Sie diese lokalen AD-Werte möglicherweise manuell auffüllen, bevor Sie den Stagingmodus entfernen.

   ![Screenshot vom Ausfüllen lokaler Hinzufügungswerte.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >Bevor Sie mit dem nächsten Schritt fortfahren, stellen Sie sicher, dass alle Benutzerattribute ordnungsgemäß synchronisiert sind und wie erwartet auf der Registerkarte **Pending Export** (Ausstehender Export) angezeigt werden. Wenn sie gelöscht werden, stellen Sie sicher, dass die ImmutableID-Werte übereinstimmen und sich der Benutzer in einer der ausgewählten Organisationseinheiten für die Synchronisierung befindet.

## <a name="install-azure-ad-cloud-sync-agents"></a>Installieren von Agents für die Azure AD-Cloudsynchronisierung

Nach der Vorbereitung einer Liste der Quellen und Ziele werden [Agents für die Azure AD-Cloudsynchronisierung installiert und konfiguriert](../cloud-sync/tutorial-single-forest.md). Bei Verwendung eines Azure AD Connect-Servers können Sie diesen Abschnitt überspringen.

## <a name="disable-okta-provisioning-to-azure-ad"></a>Deaktivieren der Okta-Bereitstellung für Azure AD

Wenn Sie die Azure AD Connect-Installation überprüft haben und die ausstehenden Exporte korrekt angezeigt werden, können Sie die Okta-Bereitstellung für Azure AD deaktivieren.

1. Navigieren Sie zum Okta-Portal. Wählen Sie **Applications** (Anwendungen) und dann Ihre Okta-App aus, die zum Bereitstellen von Benutzern für Azure AD verwendet wird. Öffnen Sie die Registerkarte **Provisioning** (Bereitstellung) und wählen Sie den Bereich **Integration** aus.

   ![Screenshot des Abschnitts „Integration“ in Okta.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

1. Wählen Sie **Edit** (Bearbeiten) aus, deaktivieren Sie die Option **Enable API integration** (API-Integration aktivieren) und wählen Sie **Save** (Speichern) aus.

   ![Screenshot vom Bearbeiten der Option zum Aktivieren der API-Integration in Okta.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Wenn Sie mehrere Office 365-Apps für die Verarbeitung der Bereitstellung für Azure AD verwenden, stellen Sie sicher, dass alle deaktiviert sind.

## <a name="disable-staging-mode-in-azure-ad-connect"></a>Deaktivieren des Stagingmodus in Azure AD Connect

Nach dem Deaktivieren der Okta-Bereitstellung kann die Synchronisierung von Objekten mit dem Azure AD Connect-Server beginnen. Bei Verwendung von Agents für die Azure AD-Cloudsynchronisierung können Sie diesen Abschnitt überspringen.

1. Führen Sie auf dem Desktop erneut den Installations-Assistenten aus und wählen Sie **Konfigurieren** aus.

   ![Screenshot vom Azure AD Connect-Server.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

1. Wählen Sie **Stagingmodus konfigurieren** und anschließend **Weiter** aus. Geben Sie Ihre Anmeldeinformationen als globaler Administrator ein.

   ![Screenshot der Option „Stagingmodus konfigurieren“.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

1. Deaktivieren Sie die Option **Stagingmodus aktivieren** und wählen Sie **Weiter** aus.

   ![Screenshot vom Deaktivieren der Option „Stagingmodus aktivieren“.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

1. Wählen Sie **Konfigurieren** aus, um den Vorgang fortzusetzen.

   ![Screenshot vom Auswählen der Konfigurationsschaltfläche.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

1. Öffnen Sie nach Abschluss der Konfiguration den Synchronisierungsdienst als **Administrator**. Zeigen Sie den **Export** auf dem Connector domain.onmicrosoft.com an. Überprüfen Sie, dass alle Hinzufügungen, Updates und Löschungen wie erwartet durchgeführt wurden.

   ![Screenshot vom Überprüfen des Synchronisierungsdienstes.](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

Damit haben Sie die Migration zur Azure AD Connect-basierten Bereitstellung erfolgreich ausgeführt. Updates und Erweiterungen des Funktionsumfangs von Azure AD Connect können durch erneutes Ausführen des Installations-Assistenten durchgeführt werden.

## <a name="enable-cloud-sync-agents"></a>Aktivieren von Agents für die Cloudsynchronisierung

Nach dem Deaktivieren der Okta-Bereitstellung kann die Synchronisierung von Objekten mit dem Agent für die Azure AD-Cloudsynchronisierung beginnen. Kehren Sie dazu zum [Azure AD-Portal](https://aad.portal.azure.com/) zurück.

1. Ändern Sie das **Konfigurationsprofil** in **Aktiviert**.

1. Kehren Sie zum Bereitstellungsmenü zurück und wählen Sie **Protokolle** aus.

1. Überprüfen Sie, ob vorhandene Objekte mit dem Bereitstellungsconnector ordnungsgemäß aktualisiert wurden. Die Agents für die Cloudsynchronisierung sind nicht destruktiv. Wenn eine Übereinstimmung nicht ordnungsgemäß erfolgt ist, treten bei den Updates Fehler auf.

1. Wenn ein Benutzer nicht übereinstimmt, nehmen Sie die erforderlichen Updates vor, um die ImmutableID-Werte zu binden. Starten Sie dann die Synchronisierung der Cloudbereitstellung neu.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Migration von Okta zu Azure AD finden Sie unter:

- [Migrieren von Anwendungen von Okta zu Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [Migrieren des Okta-Verbunds zur verwalteten Azure AD-Authentifizierung](migrate-okta-federation-to-azure-active-directory.md)
- [Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
