---
title: Verwenden von Berechtigungen in Azure Spring Cloud
description: In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Rollen erstellen, die Berechtigungen an Azure Spring Cloud-Ressourcen vergeben.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 61c4eb0322de60bb90d49bd71b111fe8b2db07ea
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356175"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Verwenden von Berechtigungen in Azure Spring Cloud
In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Rollen erstellen, die Berechtigungen an Azure Spring Cloud-Ressourcen vergeben. Benutzerdefinierte Rollen erweitern [integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md) um verschiedene Bestandsberechtigungen.

Folgende benutzerdefinierte Rollen werden implementiert:

* **Entwicklerrolle**:

    * Bereitstellen
    * Testen
    * Apps neu starten
    * App-Konfigurationen im Git-Repository anwenden und ändern
    * Protokolldatenströme abrufen

* **Rolle „Technische Fachkraft für DevOps“:**

    * Beliebige Elemente in Azure Spring Cloud erstellen, lesen, aktualisieren und löschen
* **Rolle „Ops – Websitezuverlässigkeits-Techniker (SRE)“:**

    * Apps neu starten
    * Protokolldatenströme abrufen
    * Kann keine Änderungen an Apps oder Konfigurationen vornehmen

* **Rolle „Azure Pipelines/Jenkins/GitHub Actions“:**

    * Erstell-, Lese-, Aktualisierungs- und Löschvorgänge ausführen
    * Terraform oder ARM-Vorlagen verwenden, um beliebige Elemente in Azure Spring Cloud und in Apps innerhalb einer Dienstinstanz zu erstellen und zu konfigurieren: Azure Pipelines, Jenkins oder GitHub Actions

## <a name="define-the-developer-role"></a>Definieren der Entwicklerrolle

Die Entwicklerrolle umfasst Berechtigungen zum Neustarten von Apps sowie zum Anzeigen ihrer Protokolldatenströme. Von dieser Rolle können keine Änderungen an Apps oder an der Konfiguration vorgenommen werden.

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Öffnen Sie im Azure-Portal das Abonnement, in dem Sie die benutzerdefinierte Rolle zuweisen möchten.
2. Öffnen Sie **Zugriffssteuerung (IAM)** .
3. Wählen Sie **Hinzufügen**.
4. Wählen Sie **Benutzerdefinierte Rolle hinzufügen** aus.
5. Klicken Sie auf **Weiter**:

   ![Screenshot: Registerkarte „Allgemeine Informationen“ des Fensters zum Erstellen einer benutzerdefinierten Rolle](media/spring-cloud-permissions/create-custom-role.png)

6. Wählen Sie **Berechtigungen hinzufügen** aus:

   ![Screenshot: Schaltfläche „Berechtigungen hinzufügen“](media/spring-cloud-permissions/add-permissions.png)


7. Suchen Sie im Suchfeld nach **Microsoft.app**. Wählen Sie **Microsoft Azure Spring Cloud** aus:

   ![Screenshot: Ergebnisse der Suche nach „Microsoft.app“](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Wählen Sie die Berechtigungen für die Rolle „Entwickler“ aus.

   Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
   * **Schreiben: Azure Spring Cloud-Dienstinstanz erstellen oder aktualisieren**
   * **Lesen: Azure Spring Cloud-Dienstinstanz abrufen**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
   * **Lesen: Microsoft Azure Spring Cloud-Anwendung lesen**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsressourcen-Upload-URL abrufen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/bindings** Folgendes aus:
   * **Lesen: Microsoft Azure Spring Cloud-Anwendungsbindung lesen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments** Folgendes aus:
   * **Schreiben: Microsoft Azure Spring Cloud-Anwendungsbereitstellung schreiben**
   * **Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellungs-Protokolldatei-URL abrufen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/domains** Folgendes aus:
   * **Lesen: Benutzerdefinierte Microsoft Azure Spring Cloud-Anwendungsdomäne lesen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/certificates** Folgendes aus:
   * **Lesen: Microsoft Azure Spring Cloud-Zertifikat lesen**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationResults/Spring** Folgendes aus:
   * **Lesen: Vorgangsergebnis lesen**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
   * **Lesen: Vorgangsstatus lesen**

    [ ![Screenshot: Ausgewählte Optionen für Entwicklerberechtigungen](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Wählen Sie **Hinzufügen**.

10. Berechtigungen überprüfen.

11. Klicken Sie auf **Überprüfen und erstellen**.

#### <a name="json"></a>[JSON](#tab/JSON)
1. Öffnen Sie im Azure-Portal das Abonnement, in dem Sie die benutzerdefinierte Rolle zuweisen möchten.
2. Öffnen Sie **Zugriffssteuerung (IAM)** .
3. Wählen Sie **Hinzufügen**.
4. Wählen Sie **Benutzerdefinierte Rolle hinzufügen** aus.
5. Klicken Sie auf **Weiter**.

6. Wählen Sie die Registerkarte **JSON** aus.

7. Wählen Sie **Bearbeiten** aus, und löschen Sie den Standardtext:

   ![Screenshot: JSON-Standardtext](media/spring-cloud-permissions/create-custom-role-edit-json.png)

8. Fügen Sie den folgenden JSON-Code ein, um die Entwicklerrolle zu definieren:

   ```json
   {
     "properties": {
       "roleName": "Developer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/domains/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/certificates/read",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

   ![Screenshot: JSON-Code für die Entwicklerrolle](media/spring-cloud-permissions/create-custom-role-json.png)

9. Wählen Sie **Speichern** aus.

10. Berechtigungen überprüfen.

11. Klicken Sie auf **Überprüfen und erstellen**.

---

## <a name="define-the-devops-engineer-role"></a>Definieren der Rolle „Technische Fachkraft für DevOps“

Mit dem folgenden Verfahren wird eine Rolle mit Berechtigungen zum Bereitstellen, Testen und Neustarten von Azure Spring Cloud-Apps definiert:

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Wiederholen Sie die Schritte 1 bis 4 aus dem Verfahren zum Hinzufügen der Entwicklerrolle.

2. Wählen Sie die Berechtigungen für die Rolle „Technische Fachkraft für DevOps“ aus:

   Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
   * **Schreiben: Azure Spring Cloud-Dienstinstanz erstellen oder aktualisieren**
   * **Löschen: Azure Spring Cloud-Dienstinstanz löschen**
   * **Lesen: Azure Spring Cloud-Dienstinstanz abrufen**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt aktivieren**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt deaktivieren**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel neu erstellen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
   * **Schreiben: Microsoft Azure Spring Cloud-Anwendung schreiben**
   * **Löschen: Microsoft Azure Spring Cloud-Anwendung löschen**
   * **Lesen: Microsoft Azure Spring Cloud-Anwendung lesen**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsressourcen-Upload-URL abrufen**
   * **Sonstiges: Benutzerdefinierte Microsoft Azure Spring Cloud-Anwendungsdomäne überprüfen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/bindings** Folgendes aus:
   * **Schreiben: Microsoft Azure Spring Cloud-Anwendungsbindung schreiben**
   * **Löschen: Microsoft Azure Spring Cloud-Anwendungsbindung löschen**
   * **Lesen: Microsoft Azure Spring Cloud-Anwendungsbindung lesen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments** Folgendes aus:
   * **Schreiben: Microsoft Azure Spring Cloud-Anwendungsbereitstellung schreiben**
   * **Löschen: Azure Spring Cloud-Anwendungsbereitstellung löschen**
   * **Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellungs-Protokolldatei-URL abrufen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments/skus** Folgendes aus:
   * **Lesen: Auflisten der verfügbaren SKUs für die Anwendungsbereitstellung**

   Wählen Sie unter **Microsoft.AppPlatform/locations** Folgendes aus:
   * **Sonstiges: Verfügbarkeit des Namens prüfen**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationResults/Spring** Folgendes aus:
   * **Lesen: Vorgangsergebnis lesen**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
   * **Lesen: Vorgangsstatus lesen**

   Wählen Sie unter **Microsoft.AppPlatform/skus** Folgendes aus:
   * **Lesen: Verfügbare SKUs auflisten**

   [ ![Screenshot: Ausgewählte Optionen für DevOps-Berechtigungen](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Wählen Sie **Hinzufügen**.

4. Berechtigungen überprüfen.

5. Klicken Sie auf **Überprüfen und erstellen**.

#### <a name="json"></a>[JSON](#tab/JSON)

1. Wiederholen Sie die Schritte 1 bis 4 aus dem Verfahren zum Hinzufügen der Entwicklerrolle.
2. Klicken Sie auf **Weiter**.

3. Wählen Sie die Registerkarte **JSON** aus.

4. Wählen Sie **Bearbeiten** aus, und löschen Sie den Standardtext:

   ![Screenshot: JSON-Standardtext](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Fügen Sie den folgenden JSON-Code ein, um die Rolle „Technische Fachkraft für DevOps“ zu definieren:

   ```json
   {
     "properties": {
       "roleName": "DevOps engineer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read",
             "Microsoft.AppPlatform/skus/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Berechtigungen überprüfen.

7. Klicken Sie auf **Überprüfen und erstellen**.

---

## <a name="define-the-ops---site-reliability-engineering-role"></a>Definieren der Rolle „Ops – Websitezuverlässigkeits-Techniker (SRE)“

Mit dem folgenden Verfahren wird eine Rolle mit Berechtigungen zum Bereitstellen, Testen und Neustarten von Azure Spring Cloud-Apps definiert:

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Wiederholen Sie die Schritte 1 bis 4 aus dem Verfahren zum Hinzufügen der Entwicklerrolle.
2. Wählen Sie die Berechtigungen für die Rolle „Ops – Websitezuverlässigkeits-Techniker (SRE)“ aus:

   Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
   * **Lesen: Azure Spring Cloud-Dienstinstanz abrufen**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
   * **Lesen: Microsoft Azure Spring Cloud-Anwendung lesen**

   Wählen Sie unter **Microsoft.AppPlatform/apps/deployments** Folgendes aus:
   * **Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationResults/Spring** Folgendes aus:
   * **Lesen: Vorgangsergebnis lesen**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
   * **Lesen: Vorgangsstatus lesen**

   [ ![Screenshot: Ausgewählte Optionen für Berechtigungen für „Ops – Websitezuverlässigkeits-Techniker (SRE)“](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Wählen Sie **Hinzufügen**.

4. Berechtigungen überprüfen.

5. Klicken Sie auf **Überprüfen und erstellen**.

#### <a name="json"></a>[JSON](#tab/JSON)
1. Wiederholen Sie die Schritte 1 bis 4 aus dem Verfahren zum Hinzufügen der Entwicklerrolle.
2. Klicken Sie auf **Weiter**.

3. Wählen Sie die Registerkarte **JSON** aus.

4. Wählen Sie **Bearbeiten** aus, und löschen Sie den Standardtext:

   ![Screenshot: JSON-Standardtext](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Fügen Sie den folgenden JSON-Code ein, um die Rolle „Ops – Websitezuverlässigkeits-Techniker (SRE)“ zu definieren:

   ```json
   {
     "properties": {
       "roleName": "Ops - Site Reliability Engineering",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Berechtigungen überprüfen.

7. Klicken Sie auf **Überprüfen und erstellen**.

---

## <a name="define-the-azure-pipelines--jenkins--github-actions-role"></a>Definieren der Rolle „Azure Pipelines/Jenkins/GitHub Actions“

Von dieser Rolle können beliebige Elemente in Azure Spring Cloud und in Apps innerhalb einer Dienstinstanz erstellt und konfiguriert werden. Diese Rolle dient zum Freigeben oder Bereitstellen von Code.

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

1. Wiederholen Sie die Schritte 1 bis 4 aus dem Verfahren zum Hinzufügen der Entwicklerrolle.
2. Öffnen Sie die Optionen für **Berechtigungen**.

3. Wählen Sie die Berechtigungen für die Rolle „Azure Pipelines/Jenkins/GitHub Actions“ aus:

   Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
   * **Schreiben: Azure Spring Cloud-Dienstinstanz erstellen oder aktualisieren**
   * **Löschen: Azure Spring Cloud-Dienstinstanz löschen**
   * **Lesen: Azure Spring Cloud-Dienstinstanz abrufen**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt aktivieren**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt deaktivieren**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten**
   * **Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel neu erstellen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
   * **Schreiben: Microsoft Azure Spring Cloud-Anwendung schreiben**
   * **Löschen: Microsoft Azure Spring Cloud-Anwendung löschen**
   * **Lesen: Microsoft Azure Spring Cloud-Anwendung lesen**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsressourcen-Upload-URL abrufen**
   * **Sonstiges: Benutzerdefinierte Microsoft Azure Spring Cloud-Anwendungsdomäne überprüfen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/bindings** Folgendes aus:
   * **Schreiben: Microsoft Azure Spring Cloud-Anwendungsbindung schreiben**
   * **Löschen: Microsoft Azure Spring Cloud-Anwendungsbindung löschen**
   * **Lesen: Microsoft Azure Spring Cloud-Anwendungsbindung lesen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments** Folgendes aus:
   * **Schreiben: Microsoft Azure Spring Cloud-Anwendungsbereitstellung schreiben**
   * **Löschen: Azure Spring Cloud-Anwendungsbereitstellung löschen**
   * **Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten**
   * **Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellungs-Protokolldatei-URL abrufen**

   Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments/skus** Folgendes aus:
   * **Lesen: Auflisten der verfügbaren SKUs für die Anwendungsbereitstellung**

   Wählen Sie unter **Microsoft.AppPlatform/locations** Folgendes aus:
   * **Sonstiges: Verfügbarkeit des Namens prüfen**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationResults/Spring** Folgendes aus:
   * **Lesen: Vorgangsergebnis lesen**

   Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
   * **Lesen: Vorgangsstatus lesen**

   Wählen Sie unter **Microsoft.AppPlatform/skus** Folgendes aus:
   * **Lesen: Verfügbare SKUs auflisten**

   [ ![Screenshot: Ausgewählte Optionen für Berechtigungen für „Azure Pipelines/Jenkins/GitHub Actions“](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)

4. Wählen Sie **Hinzufügen**.

5. Berechtigungen überprüfen.

6. Klicken Sie auf **Überprüfen und erstellen**.

#### <a name="json"></a>[JSON](#tab/JSON)

1. Wiederholen Sie die Schritte 1 bis 4 aus dem Verfahren zum Hinzufügen der Entwicklerrolle.
2. Klicken Sie auf **Weiter**.

3. Wählen Sie die Registerkarte **JSON** aus.

4. Wählen Sie **Bearbeiten** aus, und löschen Sie den Standardtext:

   ![Screenshot: JSON-Standardtext](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Fügen Sie den folgenden JSON-Code ein, um die Rolle „Azure Pipelines/Jenkins/GitHub Actions“ zu definieren:

   ```json
   {
     "properties": {
       "roleName": "Azure Pipelines/Provisioning",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Wählen Sie **Hinzufügen**.

7. Berechtigungen überprüfen.

---

## <a name="see-also"></a>Weitere Informationen

* [Erstellen oder Aktualisieren benutzerdefinierter Azure-Rollen über das Azure-Portal](../role-based-access-control/custom-roles-portal.md)

Weitere Informationen zu drei Methoden zum Definieren benutzerdefinierter Berechtigungen finden Sie hier:
* [Klonen einer Rolle](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Ganz von vorn beginnen](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Verwenden einer JSON-Datei](../role-based-access-control/custom-roles-portal.md#start-from-json)
