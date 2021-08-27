---
title: Metrics Advisor-Dienstverschlüsselung
titleSuffix: Azure Cognitive Services
description: Verschlüsselung für ruhende Daten des Metrics Advisor-Diensts
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: mbullwin
ms.openlocfilehash: 03f6a30bbe857b59c3fa8a6fa668bdac7cb681b1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341730"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten des Metrics Advisor-Diensts

Der Metrics Advisor-Dienst verschlüsselt automatisch Ihre Daten, wenn sie in der Cloud gespeichert werden. Durch die Verschlüsselung des Metrics Advisor-Diensts werden Ihre Daten ausreichend geschützt, und Sie können so die Sicherheits- und Complianceanforderungen Ihrer Organisation besser erfüllen.

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

Metrics Advisor unterstützt CMK und doppelte Verschlüsselung mit BYOS (Bring Your Own Storage). 

## <a name="steps-to-create-a-metrics-advisor-with-byos"></a>Schritte zum Erstellen eines Metrics Advisor mit BYOS

### <a name="step1-create-an-azure-database-for-postgresql-and-set-admin"></a>Schritt 1: Erstellen einer Azure Database for PostgreSQL und Festlegen des Administrators

- Erstellen einer Azure-Datenbank für PostgreSQL

    Melden Sie sich beim Azure-Portal an, und erstellen Sie eine Ressource der Azure Database for PostgreSQL. Beachten Sie dabei einige Dinge:

    1. Wählen Sie die Bereitstellungsoption **„Einzelserver“** aus. 
    2. Wenn Sie „Datenquelle“ auswählen, geben Sie diese als **„Keine“** an.
    3. Stellen Sie für „Standort“ sicher, dass Sie am **selben Standort** wie dem der Metrics Advisor-Ressource erstellen.
    4. „Version“ sollte auf **11** festgelegt werden. 
    5. „Compute + Speicher“ sollte eine „Arbeitsspeicheroptimierte“ SKU mit mindestens **32 virtuellen Kernen** auswählen.
    
    ![Erstellen einer Azure-Datenbank für PostgreSQL](media/cmk-create.png)

- Festlegen des Active Directory-Administrators für neu erstellte PG

    Nach dem erfolgreichen Erstellen Ihrer Azure Database for PostgreSQL. Wechseln Sie zur Ressourcenseite der neu erstellten Azure PG-Ressource. Wählen Sie die Registerkarte „Active Directory-Administrator“ aus, und legen Sie sich selbst als Administrator fest.


### <a name="step2-create-a-metrics-advisor-resource-and-enable-managed-identity"></a>Schritt 2: Erstellen einer Metrics Advisor Ressource und Aktivieren der verwalteten Identität

- Erstellen einer Metrics Advisor-Ressource im Azure-Portal

    Wechseln Sie erneut zum Azure-Portal, und suchen Sie nach „Metrics Advisor“. Denken Sie beim Erstellen des Metrics Advisor an Folgendes:

    1. Wählen Sie **dieselbe „Region“** wie beim Erstellen von Azure Database for PostgreSQL aus. 
    2. Markieren Sie „Bring Your Own Storage“ als **„Ja“** , und wählen Sie die Azure Database for PostgreSQL, die Sie gerade erstellt haben, in der Dropdownliste aus.

- Aktivieren der verwalteten Identität für Metrics Advisor

    Nachdem Sie die Metrics Advisor-Ressource erstellt haben, wählen Sie „Identität“ aus, und legen Sie „Status“ auf **„Ein“** fest, um die verwaltete Identität zu aktivieren.

- Abrufen der Anwendungs-ID der verwalteten Identität

    Wechseln Sie zu Azure Active Directory, und wählen Sie „Unternehmensanwendungen“ aus. Ändern Sie den „Anwendungstyp“ in **„Verwaltete Identität“** , kopieren Sie den Ressourcennamen von Metrics Advisor, und suchen Sie. Anschließend können Sie die „Anwendungs-ID“ im Abfrageergebnis anzeigen und kopieren.

### <a name="step3-grant-metrics-advisor-access-permission-to-your-azure-database-for-postgresql"></a>Schritt 3: Erteilen von Zugriffsberechtigung an Metrics Advisor für Ihre Azure Database for PostgreSQL

- Erteilen der Rolle **„Besitzer“** an die verwaltete Identität für Ihre Azure Database for PostgreSQL

- Festlegen von Firewallregeln

    1. Legen Sie „Zugriff auf Azure-Dienste zulassen“ auf „Ja“ fest. 
    2. Fügen Sie Ihre clientIP-Adresse hinzu, um sich bei Azure Database for PostgreSQL anzumelden.

- Rufen Sie das Zugriffstoken für Ihr Konto mit dem Ressourcentyp „https://ossrdbms-aad.database.windows.net“ ab. Das Zugriffstoken ist das Kennwort, das Sie für die Anmeldung bei der Azure Database for PostgreSQL mit Ihrem Konto benötigen. Ein Beispiel für die Verwendung des `az`-Clients:

   ```
   az login
   az account get-access-token --resource https://ossrdbms-aad.database.windows.net
   ```

- Nachdem Sie das Token abgerufen haben, verwenden Sie es, um sich bei Ihrer Azure Database for PostgreSQL anzumelden. Ersetzen Sie den „Servernamen“ durch denjenigen, den Sie in der „Übersicht“ Ihrer Azure Database for PostgreSQL finden.

   ```
   export PGPASSWORD=<access-token>
   psql -h <servername> -U <adminaccount@servername> -d postgres
   ```

- Führen Sie nach der Anmeldung die folgenden Befehle aus, um Metrics Advisor die Zugriffsberechtigung für Azure Database for PostgreSQL zu gewähren. Ersetzen Sie die „appid“ durch diejenige, die Sie in Schritt 2 abrufen.

   ```
   SET aad_validate_oids_in_tenant = off;
   CREATE ROLE metricsadvisor WITH LOGIN PASSWORD '<appid>' IN ROLE azure_ad_user;
   ALTER ROLE metricsadvisor CREATEDB;
   GRANT azure_pg_admin TO metricsadvisor;
   ```

Wenn Sie alle oben genannten Schritte ausgeführt haben, haben Sie erfolgreich eine Metrics Advisor-Ressource mit CMK-Unterstützung erstellt. Warten Sie einige Minuten, bis auf Ihren Metrics Advisor zugegriffen werden kann.

## <a name="next-steps"></a>Nächste Schritte

* [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für den Metrics Advisor-Dienst](https://aka.ms/cogsvc-cmk)
* [Weitere Informationen zu Azure Key Vault](../../key-vault/general/overview.md)