---
title: Exportieren der Konfiguration für die Anwendungsbereitstellung und Ausführen eines Rollbacks auf einen bekannten fehlerfreien Status für die Notfallwiederherstellung in Azure Active Directory
description: Hier erfahren Sie, wie Sie für die Notfallwiederherstellung in Azure Active Directory die Konfiguration für die Anwendungsbereitstellung exportieren und ein Rollback auf einen bekannten fehlerfreien Status ausführen.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f84219d6ac47773603b91bd2d3556fdc3347f455
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991506"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Gewusst wie: Exportieren einer Bereitstellungskonfiguration und Ausführen eines Rollbacks zu einem als funktionierend bekannten Zustand

In diesem Artikel lernen Sie Folgendes:

- Exportieren und Importieren Ihrer Bereitstellungskonfiguration im Azure-Portal
- Exportieren und Importieren Ihrer Bereitstellungskonfiguration mithilfe der Microsoft Graph-API

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exportieren und Importieren Ihrer Bereitstellungskonfiguration im Azure-Portal

### <a name="export-your-provisioning-configuration"></a>Exportieren der Bereitstellungskonfiguration

So exportieren Sie Ihre Konfiguration

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) im linken Navigationsbereich **Azure Active Directory** aus.
1. Wählen Sie im Bereich **Azure Active Directory** die Option **Unternehmensanwendungen** aus, und wählen Sie dann Ihre Anwendung aus.
1. Wählen Sie im linken Navigationsbereich die Option **Bereitstellung** aus. Klicken Sie auf der Seite mit der Bereitstellungskonfiguration auf **Attributzuordnungen**, dann auf **Erweiterte Optionen anzeigen** und schließlich auf **Schema überprüfen**. Dadurch wird der Schema-Editor aufgerufen.
1. Klicken Sie in der Befehlsleiste oben auf der Seite auf „Download“, um Ihr Schema herunterzuladen.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Notfallwiederherstellung – Rollback auf einen als funktionierend bekannten Zustand

Durch das Exportieren und Speichern Ihrer Konfiguration können Sie ein Rollback auf eine frühere Version Ihrer Konfiguration ausführen. Wir empfehlen, die Bereitstellungskonfiguration zu exportieren und zu speichern, damit Sie sie später jederzeit bei Änderungen an den Attributzuordnungen oder Bereichsfiltern verwenden können. Sie müssen nur die JSON-Datei, die Sie in den vorherigen Schritten heruntergeladen haben, öffnen, den gesamten Inhalt der JSON-Datei kopieren, den gesamten Inhalt der JSON-Nutzlast im Schema-Editor ersetzen und einen Speichervorgang ausführen. Falls ein aktiver Bereitstellungszyklus vorhanden ist, wird er abgeschlossen. Beim nächsten Zyklus wird dann das aktualisierte Schema verwendet. Der nächste Zyklus ist zudem ein Startzyklus, bei dem alle Benutzer und Gruppen anhand der neuen Konfiguration ausgewertet werden. Berücksichtigen Sie bei einem Rollback auf eine frühere Konfiguration die folgenden Aspekte:

- Die Benutzer werden erneut ausgewertet, um zu bestimmen, ob sie zum Bereich gehören sollen. Wenn sich die Bereichsfilter geändert haben und ein Benutzer nicht mehr zum Bereich gehört, wird er deaktiviert. Auch wenn dieses Verhalten in den meisten Fällen wünschenswert ist, gibt es auch Momente, in denen Sie das verhindern möchten. In diesen Fällen können Sie die Funktion [Löschungen bei „Nicht im Bereich“ überspringen](./skip-out-of-scope-deletions.md). 
- Durch die Änderung der Bereitstellungskonfiguration wird der Dienst neu gestartet, und ein [Startzyklus](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental) wird ausgelöst.

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Exportieren und Importieren Ihrer Bereitstellungskonfiguration mithilfe der Microsoft Graph-API

Mit Microsoft Graph-API und dem Microsoft Graph-Tester können Sie die Attributzuordnungen und das Schema Ihrer Benutzerbereitstellung in eine JSON-Datei exportieren und wieder in Azure AD importieren. Sie können die hier aufgeführten Schritte auch verwenden, um eine Sicherung Ihrer Bereitstellungskonfiguration zu erstellen.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Schritt 1: Abrufen der Dienstprinzipal-ID der Bereitstellungs-App (Objekt-ID)

1. Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Abschnitt „Eigenschaften“ Ihrer Bereitstellungsanwendung. Wenn Sie beispielsweise die Zuordnung Ihrer *Anwendung für die Benutzerbereitstellung von Workday in AD* exportieren möchten, navigieren Sie zum Abschnitt „Eigenschaften“ dieser App.
1. Kopieren Sie im Abschnitt „Eigenschaften“ Ihrer Bereitstellungs-App den GUID-Wert, der dem Feld *Objekt-ID* zugeordnet ist. Dieser Wert wird auch als die **ServicePrincipalId** Ihrer App bezeichnet und in Microsoft Graph-Tester-Vorgängen verwendet.

   ![Dienstprinzipal-ID der Workday-App](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Schritt 2: Anmelden bei Microsoft Graph-Tester

1. Starten Sie den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer).
1. Klicken Sie auf die Schaltfläche „Mit Microsoft anmelden“, und melden Sie sich mit den globalen Azure AD-Anmeldeinformationen oder mit den Anmeldeinformationen für den App-Administrator an.

    ![Microsoft Graph-Anmeldung](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Nach der erfolgreichen Anmeldung sehen Sie die Details des Benutzerkontos im linken Bereich.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Schritt 3: Abrufen der Bereitstellungsauftrags-ID der Bereitstellungs-App

Führen Sie im Microsoft Graph-Tester die folgende GET-Abfrage durch und ersetzen Sie [servicePrincipalId] durch die in [Schritt 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) extrahierte **ServicePrincipalId**.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Sie erhalten eine Antwort, wie unten dargestellt. Kopieren Sie das „id attribut“ aus der Antwort. Dieser Wert ist die **ProvisioningJobId** und wird zum Abrufen der zugrundeliegenden Schemametadaten verwendet.

   [![ID des Bereitstellungsauftrags](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Schritt 4: Herunterladen des Bereitstellungsschemas

Führen Sie im Microsoft Graph-Tester die folgende GET-Abfrage durch und ersetzen Sie [servicePrincipalId] und [ProvisioningJobId] durch die in den vorherigen Schritten abgerufen Werten für „ServicePrincipalId“ und „ProvisioningJobId“.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieren Sie das JSON-Objekt aus der Antwort, und speichern Sie sie in einer Datei, um eine Sicherung des Schemas zu erstellen.

### <a name="step-5-import-the-provisioning-schema"></a>Schritt 5: Importieren des Bereitstellungsschemas

> [!CAUTION]
> Führen Sie diesen Schritt nur aus, wenn Sie das Schema für eine Konfiguration ändern müssen, die nicht über das Azure-Portal geändert werden kann, oder wenn Sie die Konfiguration aus einer zuvor gesicherten Datei mit einem gültigen und funktionierenden Schema wiederherstellen müssen.

Konfigurieren Sie im Microsoft Graph-Tester die folgende PUT-Abfrage durch und ersetzen Sie [servicePrincipalId] und [ProvisioningJobId] durch die in den vorherigen Schritten abgerufen Werten für „ServicePrincipalId“ und „ProvisioningJobId“.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieren Sie auf der Registerkarte „Anforderungstext“ den Inhalt der JSON-Schemadatei.

   [![Anforderungstext](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Fügen Sie auf der Registerkarte „Anforderungsheader“ das Content-Type-Header-Attribut mit dem Wert „application/json“ ein.

   [![Anforderungsheader](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Klicken Sie auf **Abfrage ausführen**, um das neue Schema zu importieren.