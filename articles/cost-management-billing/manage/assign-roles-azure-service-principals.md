---
title: Zuweisen von Rollen zu Azure Enterprise Agreement-Dienstprinzipalnamen
description: Dieser Artikel unterstützt Sie beim Zuweisen von Rollen zu Dienstprinzipalnamen mithilfe von PowerShell und REST-APIs.
author: bandersmsft
ms.reviewer: sapnakeshari
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/22/2021
ms.author: banders
ms.openlocfilehash: 65f37b3f071286d143dc83674a7c7108a37cff44
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260377"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Zuweisen von Rollen zu Azure Enterprise Agreement-Dienstprinzipalnamen

Sie können Ihre EA-Registrierung (Enterprise Agreement) im [Azure Enterprise Portal](https://ea.azure.com/) verwalten. Direkte Enterprise-Kund*innen können jetzt die EA-Registrierung (Enterprise Agreement) im [Azure-Portal](https://portal.azure.com/) verwalten.
Sie können verschiedene Rollen erstellen, um Ihre Organisation zu verwalten, Kosten anzuzeigen und Abonnements zu erstellen. Dieser Artikel unterstützt Sie bei der Automatisierung einiger dieser Aufgaben durch die Verwendung von Azure PowerShell und REST-APIs mit Azure-Dienstprinzipalnamen (Service Prinzipal Names, SPNs).

Bevor Sie beginnen, stellen Sie sicher, dass Sie den Inhalt der folgenden Artikel kennen:

- [Verwalten von Azure Enterprise Agreement-Rollen](understand-ea-roles.md)
- [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps)
- [How to call Azure REST APIs with Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman) (Aufrufen von Azure-REST-APIs mit Postman, in englischer Sprache)

## <a name="create-and-authenticate-your-service-principal"></a>Erstellen und Authentifizieren des Dienstprinzipals

Zum Automatisieren von EA-Aktionen mithilfe eines SPN müssen Sie eine Azure AD-Anwendung (Azure Active Directory) erstellen. Die Authentifizierung kann automatisiert erfolgen.

Führen Sie die Schritte in diesen Artikeln aus, um Ihren Dienstprinzipal zu erstellen und zu authentifizieren.

- [Erstellen eines Dienstprinzipals](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
- [Abrufen der Werte für Mandanten-ID und App-ID für die Anmeldung](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Hier sehen Sie ein Beispiel für die Seite für die Anwendungsregistrierung:

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Screenshot: Registrieren einer Anwendung" lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Suchen des SPN und der Mandanten-ID

Außerdem benötigen Sie die Objekt-ID des SPN und die Mandanten-ID der Anwendung. Sie benötigen diese Informationen für Berechtigungszuweisungen weiter unten in diesem Artikel.

1. Öffnen Sie Azure Active Directory, und wählen Sie **Unternehmensanwendungen** aus.
1. Suchen Sie Ihre App in der Liste.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Screenshot: Beispiel für eine Unternehmensanwendung" lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

1. Wählen Sie die App aus, um die Anwendungs-ID und Objekt-ID zu ermitteln:

   :::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Screenshot: Eine Anwendungs-ID und Objekt-ID für eine Unternehmensanwendung" lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

1. Die Mandanten-ID finden Sie auf der Seite **Übersicht** von Microsoft Azure AD.

   :::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Screenshot: Mandanten-ID" lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

>[!NOTE]
>Ihre Mandanten-ID kann an anderen Stellen als Prinzipal-ID, SPN oder Objekt-ID bezeichnet werden. Der Wert der Azure AD-Mandanten-ID ähnelt einer GUID mit dem folgenden Format: `11111111-1111-1111-1111-111111111111`.

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Berechtigungen, die dem SPN zugewiesen werden können

Später in diesem Artikel erteilen Sie der Azure AD-App Berechtigungen zum Ausführen von Aktionen mit einer EA-Rolle. Sie können dem SPN nur die folgenden Rollen zuweisen, und Sie benötigen die genaue Rollendefinitions-ID wie hier gezeigt:

| Rolle | Zulässige Aktionen | Rollendefinitions-ID |
| --- | --- | --- |
| EnrollmentReader | Kann die Nutzung und die Gebühren für alle Konten und Abonnements anzeigen. Kann den Saldo der Azure-Vorauszahlung (früher als Mindestverbrauch bezeichnet) für die Registrierung anzeigen. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| EA purchaser (EA-Einkäufer) | Kann Reservierungsaufträge erwerben und Reservierungstransaktionen anzeigen. Kann die Nutzung und die Gebühren für alle Konten und Abonnements anzeigen. Kann den Saldo der Azure-Vorauszahlung (früher als Mindestverbrauch bezeichnet) für die Registrierung anzeigen. | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | Herunterladen der Nutzungsdetails für die verwaltete Abteilung. Kann die Nutzung und die Gebühren für die Abteilung anzeigen. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Erstellen neuer Abonnements im angegebenen Kontobereich. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Die Rolle „EnrollmentReader“ kann einem SPN nur von einem Benutzer zugewiesen werden, der über die Rolle „EnrollmentWriter“ verfügt.
- Die Rolle „DepartmentReader“ kann einem SPN nur von einem Benutzer zugewiesen werden, der über die Rolle „EnrollmentWriter“ oder „DepartmentWriter“ verfügt.
- Die Rolle „SubscriptionCreator“ kann einem SPN nur von einem Benutzer zugewiesen werden, der der Besitzer des Registrierungskontos ist. Die Rolle wird im EA-Portal nicht angezeigt. Sie wird programmgesteuert erstellt und dient nur zur programmgesteuerten Verwendung.
- Die Rolle des EA-Einkäufers wird im EA-Portal nicht angezeigt. Sie wird programmgesteuert erstellt und dient nur zur programmgesteuerten Verwendung.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Zuweisen der Registrierungskonto-Rollenberechtigung zum SPN

1. Lesen Sie den REST-API-Artikel [Rollenzuweisungen – PUT](/rest/api/billing/2019-10-01-preview/role-assignments/put). Klicken Sie im Artikel auf **Jetzt ausprobieren**, um mit der Verwendung des SPN zu beginnen.

   :::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Screenshot der Option „Jetzt testen“ im Artikel „Rollenzuweisungen – PUT“" lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

1. Melden Sie sich mit Ihren Kontoanmeldeinformationen beim Mandanten mit dem Registrierungszugriff an, den Sie zuweisen möchten.

1. Geben Sie in der API-Anforderung die folgenden Parameter an.

   - `billingAccountName`: Dieser Parameter ist die **Abrechnungskonto-ID**. Sie finden ihn im Azure-Portal auf der Übersichtsseite **Kostenverwaltung + Abrechnung**.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot der ID des Abrechnungskontos" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: Dieser Parameter ist eine eindeutige GUID, die Sie angeben müssen. Eine GUID kann mit dem PowerShell-Befehl [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) erstellt werden. Sie können eine eindeutige GUID auch über die Website [Online-Generator für GUIDs/UUIDs](https://guidgenerator.com/) generieren.

   - `api-version`: Verwenden Sie die Version **2019-10-01-preview**. Verwenden Sie den Beispielanforderungstext in [Rollenzuweisungen – PUT – Beispiele](/rest/api/billing/2019-10-01-preview/role-assignments/put#examples).

      Der Anforderungstext enthält JSON-Code mit drei Parametern, die Sie verwenden müssen.

      | Parameter | Ort |
      | --- | --- |
      | `properties.principalId` | Dies ist der Wert der Objekt-ID. Siehe [Suchen des SPN und der Mandanten-ID](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | Siehe [Suchen des SPN und der Mandanten-ID](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` |

      Der Name des Abrechnungskontos ist der Parameter, den Sie in den API-Parametern verwendet haben. Dabei handelt es sich um die Registrierungs-ID, die im EA-Portal und Azure-Portal angezeigt wird.

      Beachten Sie, dass `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` die ID der Abrechnungsrollendefinition für einen Benutzer mit der Rolle „EnrollmentReader“ ist.

1. Klicken Sie auf **Ausführen**, um den Befehl zu starten.

   :::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Screenshot: Beispiel für eine Rollenzuweisung mit „PUT“ nach Klicken auf „Jetzt testen“ mit Beispielinformationen zum Ausführen des Befehls" lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

   Die Antwort `200 OK` bedeutet, dass der SPN erfolgreich hinzugefügt wurde.

Jetzt können Sie den SPN verwenden, um automatisch auf EA-APIs zuzugreifen. Der SPN verfügt über die Rolle „EnrollmentReader“.

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>Zuweisen der Rollenberechtigung „EA Purchaser“ (EA-Einkäufer) zum SPN

Führen Sie für die Rolle „EA Purchaser“ (EA-Einkäufer) die gleichen Schritte wie für „EnrollmentReader“ aus. Geben Sie `roleDefinitionId` an, indem Sie das folgende Beispiel verwenden:

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

## <a name="assign-the-department-reader-role-to-the-spn"></a>Zuweisen der Rolle „DepartmentReader“ zum SPN

1. Lesen Sie den REST-API-Artikel [Zuweisen von Registrierungsrollen für Abteilungen – PUT](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put). Klicken Sie im Artikel auf **Jetzt ausprobieren**.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot: Die Option „Jetzt testen“ im Artikel „Zuweisen von Registrierungsrollen für Abteilungen – PUT“" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. Melden Sie sich mit Ihren Kontoanmeldeinformationen beim Mandanten mit dem Registrierungszugriff an, den Sie zuweisen möchten.

1. Geben Sie in der API-Anforderung die folgenden Parameter an.

   - `billingAccountName`: Dieser Parameter ist die **Abrechnungskonto-ID**. Sie finden ihn im Azure-Portal auf der Übersichtsseite **Kostenverwaltung + Abrechnung**.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot der ID des Abrechnungskontos" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: Dieser Parameter ist eine eindeutige GUID, die Sie angeben müssen. Eine GUID kann mit dem PowerShell-Befehl [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) erstellt werden. Sie können eine eindeutige GUID auch über die Website [Online-Generator für GUIDs/UUIDs](https://guidgenerator.com/) generieren.

   - `departmentName`: Dieser Parameter ist die Abteilungs-ID. Abteilungs-IDs werden im Azure-Portal auf der Seite **Kostenverwaltung + Abrechnung** > **Abteilungen** angezeigt.

      In diesem Beispiel wurde die Abteilung „ACE“ verwendet. Die ID im Beispiel lautet `84819`.

      :::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Screenshot: Beispiel für eine Abteilungs-ID" lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

   - `api-version`: Verwenden Sie die Version **2019-10-01-preview**. Verwenden Sie das Beispiel in [Zuweisen von Registrierungsrollen für Abteilungen – PUT](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put).

      Der Anforderungstext enthält JSON-Code mit drei Parametern, die Sie verwenden müssen.

      | Parameter | Ort |
      | --- | --- |
      | `properties.principalId` | Dies ist der Wert der Objekt-ID. Siehe [Suchen des SPN und der Mandanten-ID](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | Siehe [Suchen des SPN und der Mandanten-ID](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a` |

      Der Name des Abrechnungskontos ist der Parameter, den Sie in den API-Parametern verwendet haben. Dabei handelt es sich um die Registrierungs-ID, die im EA-Portal und Azure-Portal angezeigt wird.

      Die Definitions-ID `db609904-a47f-4794-9be8-9bd86fbffd8a` der Abrechnungsrolle gilt für „DepartmentReader“.

1. Klicken Sie auf **Ausführen**, um den Befehl zu starten.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Screenshot: Beispiel für die Rollenzuweisung mit „PUT“ nach Klicken auf „Jetzt testen“ im Artikel zum Zuweisen von Registrierungsrollen für Abteilungen mit Beispielinformationen zum Ausführen des Befehls" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

   Die Antwort `200 OK` bedeutet, dass der SPN erfolgreich hinzugefügt wurde.

Jetzt können Sie den SPN verwenden, um automatisch auf EA-APIs zuzugreifen. Der SPN verfügt über die Rolle „DepartmentReader“.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Zuweisen der Rolle „SubscriptionCreator“ zum SPN

1. Lesen Sie den Artikel [Zuweisen von Registrierungsrollenzuweisungen für Konten – PUT](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put). Klicken Sie im Artikel auf **Jetzt ausprobieren**, um dem SPN die Rolle „SubscriptionCreator“ zuzuweisen.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot: Die Option „Jetzt testen“ im Artikel „Zuweisen von Registrierungsrollen für Konten – PUT“" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. Melden Sie sich mit Ihren Kontoanmeldeinformationen beim Mandanten mit dem Registrierungszugriff an, den Sie zuweisen möchten.

1. Geben Sie in der API-Anforderung die folgenden Parameter an. Lesen Sie den Artikel [Zuweisen von Registrierungsrollen für Konten – PUT – URI-Parameter](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put#uri-parameters).

   - `billingAccountName`: Dieser Parameter ist die **Abrechnungskonto-ID**. Sie finden ihn im Azure-Portal auf der Übersichtsseite **Kostenverwaltung + Abrechnung**.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot: Abrechnungskonto-ID" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: Dieser Parameter ist eine eindeutige GUID, die Sie angeben müssen. Eine GUID kann mit dem PowerShell-Befehl [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) erstellt werden. Sie können eine eindeutige GUID auch über die Website [Online-Generator für GUIDs/UUIDs](https://guidgenerator.com/) generieren.

   - `enrollmentAccountName`: Dieser Parameter ist die Konto-**ID**. Suchen Sie im Azure-Portal auf der Seite **Kostenverwaltung + Abrechnung** die Konto-ID für den Kontonamen.

      In diesem Beispiel wurde ein Konto mit dem Namen „GTM Test Account“ verwendet. Die ID lautet `196987`.

      :::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Screenshot der Konto-ID" lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

   - `api-version`: Verwenden Sie die Version **2019-10-01-preview**. Verwenden Sie das Beispiel in [Zuweisen von Registrierungsrollen für Abteilungen – PUT – Beispiele](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put#examples).

      Der Anforderungstext enthält JSON-Code mit drei Parametern, die Sie verwenden müssen.

      | Parameter | Ort |
      | --- | --- |
      | `properties.principalId` | Dies ist der Wert der Objekt-ID. Siehe [Suchen des SPN und der Mandanten-ID](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | Siehe [Suchen des SPN und der Mandanten-ID](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71` |

      Der Name des Abrechnungskontos ist der Parameter, den Sie in den API-Parametern verwendet haben. Dabei handelt es sich um die Registrierungs-ID, die im EA-Portal und Azure-Portal angezeigt wird.

      Die Definitions-ID `a0bcee42-bf30-4d1b-926a-48d21664ef71` der Abrechnungsrolle gilt für die Rolle „SubscriptionCreator“.

1. Klicken Sie auf **Ausführen**, um den Befehl zu starten.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Screenshot: Die Option „Jetzt testen“ im Artikel „Zuweisen von Registrierungsrollen für Konten – PUT“" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

   Die Antwort `200 OK` bedeutet, dass der SPN erfolgreich hinzugefügt wurde.

Jetzt können Sie den SPN verwenden, um automatisch auf EA-APIs zuzugreifen. Der SPN verfügt über die Rolle „SubscriptionCreator“.

## <a name="troubleshoot"></a>Problembehandlung

Sie müssen die Objekt-ID der Unternehmensanwendung identifizieren und verwenden, in der Sie die EA-Rolle vergeben haben. Wenn Sie die Objekt-ID einer anderen Anwendung verwenden, werden API-Aufrufe fehlschlagen. Vergewissern Sie sich, dass Sie die richtige Objekt-ID der Enterprise-Anwendung verwenden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Verwaltung im Azure EA-Portal](ea-portal-administration.md).
