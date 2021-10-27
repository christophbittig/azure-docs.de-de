---
title: Referenz zur Integration von Azure Active Directory mit SAP SuccessFactors
description: Fundierte technische Einblicke in die HR-Bereitstellung mit SAP SuccessFactors für Azure Active Directory.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 10/11/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 8c215c8b032fb3981771d2091b449b1934e78533
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990824"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Integration der Azure Active Directory-Bereitstellung mit SAP SuccessFactors 

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../app-provisioning/user-provisioning.md) ist mit [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) integriert, um den Identitätslebenszyklus von Benutzern zu verwalten. Azure Active Directory bietet drei vorkonfigurierte Integrationen: 

* [Lokale Benutzerbereitstellung von SuccessFactors in Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Benutzerbereitstellung von SuccessFactors in Azure Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors: Rückschreiben](../saas-apps/sap-successfactors-writeback-tutorial.md)

In diesem Artikel wird erläutert, wie die Integration funktioniert und wie Sie das Bereitstellungsverhalten für verschiedene HR-Szenarien anpassen können. 

## <a name="establishing-connectivity"></a>Herstellen von Verbindungen 
Der Azure AD-Bereitstellungsdienst verwendet für Verbindungen mit den OData-API-Endpunkten von Employee Central die Standardauthentifizierung. Wenn Sie die App für die SuccessFactors-Bereitstellung einrichten, verwenden Sie den Parameter *Mandanten-URL* im Abschnitt mit den *Administratoranmeldeinformationen*, um die [URL des API-Rechenzentrums](https://apps.support.sap.com/sap/support/knowledge/en/2215682) zu konfigurieren. 

Um die Konnektivität zwischen dem Azure AD-Bereitstellungsdienst und SuccessFactors besser zu schützen, können Sie die Azure AD-IP-Adressbereiche mithilfe der unten beschriebenen Schritte der SuccessFactors-IP-Positivliste hinzufügen:

1. Laden Sie die [aktuellen IP-Adressbereiche](https://www.microsoft.com/download/details.aspx?id=56519) für die öffentliche Azure-Cloud herunter. 
1. Öffnen Sie die Datei, und suchen Sie nach dem Tag **AzureActiveDirectory**. 

   >[!div class="mx-imgBorder"] 
   >![Azure AD-IP-Adressbereich](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Kopieren Sie alle IP-Adressbereiche, die innerhalb des-Elements *addressPrefixes* aufgeführt sind, und verwenden Sie den Bereich, um eine eigene Liste zur IP-Adresseinschränkung zu erstellen.
1. Übersetzen Sie die CIDR-Werte in IP-Adressbereiche.  
1. Melden Sie sich beim SuccessFactors-Administratorportal an, um der Positivliste IP-Adressbereiche hinzuzufügen. Weitere Informationen finden Sie im [SAP-Supporthinweis Nr. 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Sie können nun in diesem Tool [IP-Adressbereiche](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) eingeben. 

## <a name="supported-entities"></a>Unterstützte Entitäten
Der Azure AD-Bereitstellungsdienst ruft für jeden Benutzer in SuccessFactors die folgenden Entitäten ab. Jede Entität wird mit dem Abfrageparameter *$expand* der OData-API erweitert. Weitere Informationen finden Sie unten in der Spalte *Abrufregel*. Einige Entitäten werden standardmäßig erweitert, während andere Entitäten nur erweitert werden, wenn ein bestimmtes Attribut in der Zuordnung vorhanden ist. 

| \# | SuccessFactors-Entität                  | OData-Knoten     | Abrufregel |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *Stammknoten*                  | Always           |
| 2  | PerPersonal                            | personalInfoNav              | Always           |
| 3  | PerPhone                               | phoneNav                     | Always           |
| 4  | PerEmail                               | emailNav                     | Always           |
| 5  | EmpEmployment                          | employmentNav                | Always           |
| 6  | Benutzer                                   | employmentNav/userNav        | Always           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Always           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Always           |
| 9  | Vorgesetzter eines Benutzers                         | employmentNav/userNav/manager/empInfo | Immer  |
| 10 | FOCompany                              | employmentNav/jobInfoNav/companyNav | Nur, wenn eines der Attribute `company` oder `companyId` zugeordnet ist |
| 11 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Nur, wenn eines der Attribute `department` oder `departmentId` zugeordnet ist |
| 12 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Nur, wenn eines der Attribute `businessUnit` oder `businessUnitId` zugeordnet ist |
| 13 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Nur, wenn eines der Attribute `costCenter` oder `costCenterId` zugeordnet ist |
| 14 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Nur, wenn eines der Attribute `division` oder `divisionId` zugeordnet ist |
| 15 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Nur, wenn eines der Attribute `jobCode` oder `jobCodeId` zugeordnet ist |
| 16 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Nur, wenn das Attribut `payGrade` zugeordnet ist |
| 17 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Nur, wenn das Attribut `location` zugeordnet ist |
| 18 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Wenn die Zuordnung eines der folgenden Attribute enthält: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Nur, wenn das Attribut `eventReason` zugeordnet ist |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Nur, wenn `assignmentType` zugeordnet ist |
| 21 | EmploymentType Picklist                | employmentNav/jobInfoNav/employmentTypeNav | Nur, wenn `employmentType` zugeordnet ist |
| 22 | EmployeeClass (Auswahlliste)                 | employmentNav/jobInfoNav/employeeClassNav | Nur, wenn `employeeClass` zugeordnet ist |
| 23 | EmplStatus (Auswahlliste)                    | employmentNav/jobInfoNav/emplStatusNav | Nur, wenn `emplStatus` zugeordnet ist |
| 24 | AssignmentType (Auswahlliste)                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Nur, wenn `assignmentType` zugeordnet ist |
| 25 | Position                               | employmentNav/jobInfoNav/positionNav | Nur, wenn `positioNav` zugeordnet ist |
| 26 | Manager-Benutzer                           | employmentNav/jobInfoNav/managerUserNav | Nur, wenn `managerUserNav` zugeordnet ist |

## <a name="how-full-sync-works"></a>Funktionsweise der vollständigen Synchronisierung
Basierend auf der Attributzuordnung sendet der Azure AD-Bereitstellungsdienst während der vollständigen Synchronisierung die folgende GET-Abfrage der OData-API, um die tatsächlichen Daten aller aktiven Benutzer abzurufen. 

> [!div class="mx-tdCol2BreakAll"]
>| Parameter | BESCHREIBUNG |
>| ----------|-------------|
>| OData-API-Host | Fügt HTTPS an die *Mandanten-URL* an. Beispiel: `https://api4.successfactors.com` |
>| OData-API-Endpoint | `/odata/v2/PerPerson` |
>| OData-Abfrageparameter $format | `json` |
>| OData-Abfrageparameter $filter | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData-Abfrageparameter $expand | Dieser Parameterwert hängt von den zugeordneten Attributen ab. Beispiel: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData-Abfrageparameter customPageSize | `100` |

> [!NOTE]
> Während der ersten vollständigen Synchronisierung pullt der Azure AD-Bereitstellungsdienst keine Daten von inaktiven/ausgeschiedenen Mitarbeitern.

Für jeden SuccessFactors-Benutzer sucht der Bereitstellungsdienst mithilfe des in der Zuordnung definierten zugehörigen Attributs nach einem Konto im Ziel (Azure AD/lokales Active Directory). Beispiel: Wenn *personIdExternal* der *employeeId* zugeordnet und als abzugleichendes Attribut festgelegt ist, verwendet der Bereitstellungsdienst den Wert von *personIdExternal*, um den Benutzer mit dem Filter *employeeId* zu suchen. Wenn ein übereinstimmender Benutzer gefunden wird, aktualisiert er die Zielattribute. Wenn keine Übereinstimmung gefunden wird, erstellt er am Ziel einen neuen Eintrag. 

Um die vom OData-API-Endpunkt zurückgegebenen Daten für einen bestimmten `personIdExternal`-Wert zu überprüfen, ersetzen Sie den `SuccessFactorsAPIEndpoint` in der API-Abfrage unten durch die URL Ihres API-Rechenzentrumsservers und verwenden ein Tool wie [Postman](https://www.postman.com/downloads/), um die Abfrage aufzurufen. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Funktionsweise der inkrementellen Synchronisierung

Nach der vollständigen Synchronisierung verwaltet der Azure AD-Bereitstellungsdienst einen `LastExecutionTimestamp` und verwendet diesen zum Erstellen von Deltaabfragen für das Abrufen inkrementeller Änderungen. Die Zeitstempelattribute, die in jeder SuccessFactors-Entität enthalten sind (z. B. `lastModifiedDateTime`, `startDate`, `endDate` oder `latestTerminationDate`), werden ausgewertet, um festzustellen, ob die Änderung zwischen dem `LastExecutionTimestamp` und der `CurrentExecutionTime` liegt. Falls dies zutrifft, gilt die Änderung des Eintrags als wirksam und wird für die Synchronisierung verarbeitet. 

## <a name="reading-attribute-data"></a>Lesen von Attributdaten

Wenn der Azure AD-Bereitstellungsdienst SuccessFactors abfragt, ruft er ein JSON-Resultset ab. Das JSON-Resultset enthält eine Reihe von Attributen, die in Employee Central gespeichert sind. Das Bereitstellungsschema ist standardmäßig so konfiguriert, dass nur eine Teilmenge dieser Attribute abgerufen wird. 

Um zusätzliche Attribute abzurufen, führen Sie die unten aufgeführten Schritte aus:
    
1. Navigieren Sie zu **Unternehmensanwendungen** -> **SuccessFactors-App** -> **Bereitstellung** -> **Bereitstellung bearbeiten** -> **Seite „Attributzuordnung“** .
1. Scrollen Sie nach unten, und klicken Sie auf **Erweiterte Optionen anzeigen**.
1. Klicken Sie auf **Attributliste für SuccessFactors bearbeiten**. 

   > [!NOTE] 
   > Wenn die Option **Attributliste für SuccessFactors bearbeiten** im Azure-Portal nicht angezeigt wird, können Sie über die URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* auf die Seite zugreifen. 

1. In der Ansicht werden in der Spalte **API-Ausdruck** die JSONPath-Ausdrücke angezeigt, die vom Connector verwendet werden.

   >[!div class="mx-imgBorder"] 
   >![API-Ausdruck](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. Sie können einen vorhandenen JSONPath-Wert bearbeiten oder dem Schema ein neues Attribut mit einem gültigen JSONPath-Ausdruck hinzufügen. 

Der nächste Abschnitt enthält eine Liste häufiger Szenarien, in denen JSONPath-Werte bearbeitet werden. 

## <a name="handling-different-hr-scenarios"></a>Umgang mit verschiedenen HR-Szenarien

JSONPath ist eine Abfragesprache für JSON, die XPath für XML ähnelt. Wie XPath ermöglicht auch JSONPath das Extrahieren und Filtern von Daten aus der JSON-Nutzlast.

Mithilfe einer JSONPath-Transformation können Sie das Verhalten der Azure AD-Bereitstellungs-App so anpassen, dass benutzerdefinierte Attribute abgerufen werden. Dies hilft Ihnen in Szenarien wie einer Neueinstellung und einer Mitarbeiterstatusumwandlung oder bei der globalem Zuweisung. 

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellungs-App für die folgenden Szenarien zur Personalverwaltung anpassen können: 
* [Abrufen zusätzlicher Attribute](#retrieving-additional-attributes)
* [Abrufen benutzerdefinierter Attribute](#retrieving-custom-attributes)
* [Umgang mit Mitarbeiterstatusumwandlungen](#handling-worker-conversion-scenario)
* [Umgang mit Wiedereinstellungen](#handling-rehire-scenario)
* [Globale Zuweisung](#handling-global-assignment-scenario)
* [Umgang mit gleichzeitigen Aufträgen](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Abrufen zusätzlicher Attribute

Das Standardschema der Azure AD-Bereitstellungs-App für SuccessFactors bietet bereits [mehr als 90 vordefinierte Attribute](sap-successfactors-attribute-reference.md). Führen Sie die unten aufgeführten Schritte aus, um dem Bereitstellungsschema weitere SuccessFactors-Attribute hinzuzufügen: 

1. Rufen Sie mithilfe der unten stehenden OData-Abfrage Daten für einen gültigen Testbenutzer von Employee Central ab. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. Ermitteln der dem Attribut zugeordneten Employee Central-Entität
   * Wenn das Attribut Teil der *EmpEmployment*-Entität ist, suchen Sie unter dem Knoten *employmentNav* nach dem Attribut. 
   * Wenn das Attribut Teil der *User*-Entität ist, suchen Sie unter dem Knoten *employmentNav/userNav* nach dem Attribut.
   * Wenn das Attribut Teil der *EmpJob*-Entität ist, suchen Sie unter dem Knoten *employmentNav/jobInfoNav* nach dem Attribut. 
1. Erstellen Sie den JSON-Pfad, der dem-Attribut zugeordnet ist, und fügen Sie dieses neue Attribut der Liste der SuccessFactors-Attribute hinzu. 
   * Beispiel 1: Angenommen, Sie möchten das Attribut *okToRehire* hinzufügen, das Teil der *employmentNav*-Entität ist, und dann den JSONPath `$.employmentNav.results[0].okToRehire` verwenden.
   * Beispiel 2: Angenommen, Sie möchten das Attribut *timeZone* hinzufügen, das Teil der *userNav*-Entität ist, und dann den JSONPath `$.employmentNav.results[0].userNav.timeZone` verwenden.
   * Beispiel 3: Angenommen, Sie möchten das Attribut *flsaStatus* hinzufügen, das Teil der *jobInfoNav*-Entität ist, und dann den JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus` verwenden.
1. Speichern Sie das Schema. 
1. Starten Sie die Bereitstellung erneut.

### <a name="retrieving-custom-attributes"></a>Abrufen benutzerdefinierter Attribute

Standardmäßig sind die folgenden benutzerdefinierten Attribute in der Azure AD-Bereitstellungs-App für SuccessFactors vordefiniert: 
* *custom01 – custom15* in der User-Entität (userNav)
* *customString1 – customString15* in der EmpEmployment-Entität (employmentNav) *empNavCustomString1 – empNavCustomString15*
* *customString1 – customString15* in der EmpJobInfo-Entität (jobInfoNav) *empJobNavCustomString1 – empJobNavCustomString15*

Angenommen, in Ihrer Employee Central-Instanz wird im Attribut *customString35* in *EmpJobInfo* die Beschreibung des Standorts gespeichert. Sie möchten diesen Wert in das Active Directory-Attribut *physicalDeliveryOfficeName* übertragen. Führen Sie die folgenden Schritte aus, um die Attributzuordnung für dieses Szenario zu konfigurieren: 

1. Bearbeiten Sie die SuccessFactors-Attributliste, um das neue Attribut *empJobNavCustomString35* hinzuzufügen.
1. Legen Sie den JSONPath-API-Ausdruck für dieses Attribut wie folgt fest: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Speichern Sie die Zuordnungsänderung, und laden Sie sie erneut im Azure-Portal.  
1. Ordnen Sie auf dem Blatt „Attributzuordnung“ die *empJobNavCustomString35* dem *physicalDeliveryOfficeName* zu.
1. Speichern Sie die Zuordnung.

Erweitern dieses Szenarios: 
* Wenn Sie das Attribut *custom35* der *User*-Entität zuordnen möchten, verwenden Sie den JSONPath `$.employmentNav.results[0].userNav.custom35`.
* Wenn Sie das Attribut *customString35* der *EmpEmployment*-Entität zuordnen möchten, verwenden Sie den JSONPath `$.employmentNav.results[0].customString35`.

### <a name="handling-worker-conversion-scenario"></a>Umgang mit Mitarbeiterstatusumwandlungen

Bei der Mitarbeiterstatusumwandlung wird ein vorhandener Vollzeitmitarbeiter zu einem Vertragsmitarbeiter oder ein Vertragsmitarbeiter zu einem Vollzeitmitarbeiter. In diesem Szenario fügt Employee Central eine neue *EmpEmployment*-Entität zusammen mit einer neuen *User*-Entität für dieselbe *Person*-Entität hinzu. Die *User*-Entität, die in der vorherigen *EmpEmployment*-Entität geschachtelt war, wird auf NULL festgelegt. Damit in diesem Szenario bei einer Statusumwandlung neue Mitarbeiterdaten angezeigt werden, können Sie das Schema der Bereitstellungs-App mithilfe der unten aufgeführten Schritte per Massenvorgang aktualisieren:  

1. Öffnen Sie das Blatt „Attributzuordnung“ Ihrer SuccessFactors-Bereitstellungs-App. 
1. Scrollen Sie nach unten, und klicken Sie auf **Erweiterte Optionen anzeigen**.
1. Klicken Sie auf den Link **Review your schema here** (Schema hier überprüfen), um den Schema-Editor zu öffnen. 

   >![Screenshot mit dem Link zum Überprüfen des Schemas, über den der Schema-Editor geöffnet wird.](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. Klicken Sie auf den Link **Download**, um eine Kopie des Schemas vor der Bearbeitung zu speichern. 

   >![Screenshot zur Darstellung des Schema-Editors mit ausgewählter Downloadoption, um eine Kopie des Schemas zu speichern.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. Drücken Sie im Schema-Editor die Tastenkombination STRG+H, um das Steuerelement für das Suchen und Ersetzen zu öffnen.
1. Kopieren Sie den Wert von `$.employmentNav.results[0]`, und fügen Sie ihn im Textfeld für die Suche ein.
1. Kopieren Sie den Wert von `$.employmentNav.results[?(@.userNav != null)]`, und fügen Sie ihn im Textfeld für das Ersetzen ein. Beachten Sie den Leerraum um den Operator `!=`. Dieser ist für die erfolgreiche Verarbeitung des JSONPath-Ausdrucks wichtig. 
   >![Suchen und Ersetzen für die Statusumwandlung](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Klicken Sie auf die Option „Alle ersetzen“, um das Schema zu aktualisieren. 
1. Speichern Sie das Schema. 
1. Durch den oben beschriebenen Prozess werden alle JSONPath-Ausdrücke wie folgt aktualisiert: 
   * Alter JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Neuer JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Starten Sie die Bereitstellung erneut. 

### <a name="handling-rehire-scenario"></a>Umgang mit Wiedereinstellungen

In der Regel gibt es zwei Optionen für die Verarbeitung von Wiedereinstellungen: 
* Option 1: Erstellen eines neuen Personalprofils in Employee Central
* Option 2: Wiederverwenden eines vorhandenen Personalprofils in Employee Central

Wenn Ihre Personalabteilung Option 1 verwendet, sind keine Änderungen am Bereitstellungsschema erforderlich. Wenn Ihre Personalabteilung Option 2 verwendet, fügt Employee Central eine neue *EmpEmployment*-Entität zusammen mit einer neuen *User*-Entität für dieselbe *Person*-Entität hinzu. Anders als beim Szenario zur Statusumwandlung wird die *User*-Entität in der vorherigen *EmpEmployment*-Entität jedoch nicht auf NULL festgelegt. 

Damit in diesem Wiedereinstellungsszenario (Option 2) die neuesten Mitarbeiterdaten für das Profil des wiedereingestellten Mitarbeiters angezeigt werden, können Sie das Schema der Bereitstellungs-App mithilfe der unten aufgeführten Schritte per Massenvorgang aktualisieren:  

1. Öffnen Sie das Blatt „Attributzuordnung“ Ihrer SuccessFactors-Bereitstellungs-App. 
1. Scrollen Sie nach unten, und klicken Sie auf **Erweiterte Optionen anzeigen**.
1. Klicken Sie auf den Link **Review your schema here** (Schema hier überprüfen), um den Schema-Editor zu öffnen.   
1. Klicken Sie auf den Link **Download**, um eine Kopie des Schemas vor der Bearbeitung zu speichern.   
1. Drücken Sie im Schema-Editor die Tastenkombination STRG+H, um das Steuerelement für das Suchen und Ersetzen zu öffnen.
1. Kopieren Sie den Wert von `$.employmentNav.results[0]`, und fügen Sie ihn im Textfeld für die Suche ein.
1. Kopieren Sie den Wert von `$.employmentNav.results[-1:]`, und fügen Sie ihn im Textfeld für das Ersetzen ein. Dieser JSONPath-Ausdruck gibt den neuesten *EmpEmployment*-Datensatz zurück.   
1. Klicken Sie auf die Option „Alle ersetzen“, um das Schema zu aktualisieren. 
1. Speichern Sie das Schema. 
1. Durch den oben beschriebenen Prozess werden alle JSONPath-Ausdrücke wie folgt aktualisiert: 
   * Alter JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Neuer JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Starten Sie die Bereitstellung erneut. 

Diese Schemaänderung unterstützt auch das Szenario für die Statusumwandlung von Mitarbeitern. 

### <a name="handling-global-assignment-scenario"></a>Globale Zuweisung

Wenn ein Benutzer in Employee Central für die globale Zuweisung verarbeitet wird, fügt SuccessFactors eine neue *EmpEmployment*-Entität hinzu und legt die *assignmentClass* auf „GA“ fest. Außerdem wird eine neue *User*-Entität erstellt. Daher verfügt der Benutzer nun über Folgendes:
* Eine *EmpEmployment* + *User*-Entität, die der Startzuweisung entspricht, bei der *assignmentClass* auf „ST“ festgelegt ist. 
* Eine weitere *EmpEmployment* + *User*-Entität, die der globalen Zuweisung entspricht, bei der *assignmentClass* auf „GA“ festgelegt ist.

Führen Sie die unten aufgeführten Schritte aus, um Attribute abzurufen, die zum Benutzerprofil für die Standardzuweisung und dem für die globale Zuweisung gehören: 

1. Öffnen Sie das Blatt „Attributzuordnung“ Ihrer SuccessFactors-Bereitstellungs-App. 
1. Scrollen Sie nach unten, und klicken Sie auf **Erweiterte Optionen anzeigen**.
1. Klicken Sie auf den Link **Review your schema here** (Schema hier überprüfen), um den Schema-Editor zu öffnen.   
1. Klicken Sie auf den Link **Download**, um eine Kopie des Schemas vor der Bearbeitung zu speichern.   
1. Drücken Sie im Schema-Editor die Tastenkombination STRG+H, um das Steuerelement für das Suchen und Ersetzen zu öffnen.
1. Kopieren Sie den Wert von `$.employmentNav.results[0]`, und fügen Sie ihn im Textfeld für die Suche ein.
1. Kopieren Sie den Wert von `$.employmentNav.results[?(@.assignmentClass == 'ST')]`, und fügen Sie ihn im Textfeld für das Ersetzen ein. 
1. Klicken Sie auf die Option „Alle ersetzen“, um das Schema zu aktualisieren. 
1. Speichern Sie das Schema. 
1. Durch den oben beschriebenen Prozess werden alle JSONPath-Ausdrücke wie folgt aktualisiert: 
   * Alter JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Neuer JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Laden Sie das Blatt „Attributzuordnung“ der App erneut. 
1. Scrollen Sie nach unten, und klicken Sie auf **Erweiterte Optionen anzeigen**.
1. Klicken Sie auf **Attributliste für SuccessFactors bearbeiten**.
1. Fügen Sie neue Attribute hinzu, um Daten zur globalen Zuweisung abzurufen. Beispiel: Wenn Sie den Abteilungsnamen abrufen möchten, der einem Profil mit globaler Zuweisung zugeordnet ist, können Sie das Attribut *globalAssignmentDepartment* hinzufügen, bei dem der JSONPath-Ausdruck auf `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` festgelegt ist. 
1. Sie können nun beide Abteilungswerte in Active Directory-Attribute oder selektiv einen Wert mithilfe einer Ausdruckszuordnung übertragen. Beispiel: Der nachfolgende Ausdruck legt den Wert des AD-Attributs *department* auf *globalAssignmentDepartment* fest, sofern dieser vorhanden ist. Andernfalls wird der Wert auf das *department*-Attribut festgelegt, das mit der Standardzuweisung verknüpft ist. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Speichern Sie die Zuordnung. 
1. Starten Sie die Bereitstellung erneut. 

### <a name="handling-concurrent-jobs-scenario"></a>Umgang mit gleichzeitigen Aufträgen

Wenn ein Benutzer in Employee Central über mehrere/gleichzeitige Aufträge verfügt, gibt es zwei *EmpEmployment*- und *User*-Entitäten, deren *assignmentClass* auf „ST“ festgelegt ist. Gehen Sie anhand der unten aufgeführten Schritte vor, um Attribute abzurufen, die zu beiden Aufträgen gehören: 

1. Öffnen Sie das Blatt „Attributzuordnung“ Ihrer SuccessFactors-Bereitstellungs-App. 
1. Scrollen Sie nach unten, und klicken Sie auf **Erweiterte Optionen anzeigen**.
1. Klicken Sie auf **Attributliste für SuccessFactors bearbeiten**.
1. Angenommen, Sie möchten die Abteilung pullen, die job 1 und job 2 zugeordnet ist. Das vordefinierte Attribut *department* ruft bereits den Wert von „department“ für den ersten Auftrag ab. Sie können ein neues Attribut namens *secondJobDepartment* definieren und den JSONPath-Ausdruck auf `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized` festlegen.
1. Sie können nun beide Abteilungswerte in Active Directory-Attribute oder selektiv einen Wert mithilfe einer Ausdruckszuordnung übertragen. 
1. Speichern Sie die Zuordnung. 
1. Starten Sie die Bereitstellung erneut. 

### <a name="retrieving-position-details"></a>Abrufen von Positionsdetails

Der SuccessFactors-Connector unterstützt die Erweiterung des Positionsobjekts. Um Positionsobjektattribute wie Tätigkeitsstufen oder Positionsbezeichnungen in einer bestimmten Sprache zu erweitern und abzurufen, können Sie JSONPath-Ausdrücke (wie unten dargestellt) verwenden. 

| Attributname | JSONPath-Ausdruck |
| -------------- | ------------------- |
| positionJobLevel | $.employmentNav.results[0].jobInfoNav.results[0].positionNav.jobLevel |
| positionNameFR | $.employmentNav.results[0].jobInfoNav.results[0].positionNav.externalName_fr_FR |
| positionNameDE | $.employmentNav.results[0].jobInfoNav.results[0].positionNav.externalName_de_DE |

## <a name="writeback-scenarios"></a>Szenarien für das Rückschreiben

In diesem Abschnitt werden verschiedene Szenarien für das Rückschreiben behandelt. Die Empfehlungen für die Konfigurationsansätze basieren darauf, wie E-Mail-Adressen und Telefonnummern in SuccessFactors eingerichtet wurden.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>Unterstützte Szenarien für das Rückschreiben von Telefonnummern und E-Mail-Adressen 

| \# | Szenarioanforderung | Primäre E-Mail-Adresse <br> Flagwert | Telefon (geschäftlich) <br> primärer Flagwert | Mobiltelefon <br> primärer Flagwert | Telefon (geschäftlich) <br> mapping | Mobiltelefon <br> mapping |
|--|--|--|--|--|--|--|
| 1 | * Legen Sie nur geschäftliche E-Mail-Adressen als primär fest. <br> * Legen Sie keine Telefonnummern fest. | true | true | false | \[Nicht festgelegt\] | \[Nicht festgelegt\] | 
| 2 | * In SuccessFactors sind die geschäftliche E-Mail-Adresse und die geschäftliche Telefonnummer primär. <br> * Übertragen Sie immer die Azure AD-Telefonnummer als die geschäftliche Telefonnummer und die Mobiltelefonnummer als Mobiltelefon. | true | true | false | telephoneNumber | mobile | 
| 3 | * In SuccessFactors sind die geschäftliche E-Mail-Adresse und Telefonnummer primär. <br> * Übertragen Sie immer die Azure AD-Telefonnummer als geschäftliche Telefonnummer und die Mobiltelefonnummer als Mobiltelefon. | true | false | true |  telephoneNumber | mobile | 
| 4 | * In SuccessFactors ist die geschäftliche E-Mail-Adresse primär. <br> * Überprüfen Sie in Azure AD, ob die geschäftliche Telefonnummer vorhanden ist. Falls sie vorhanden ist, überprüfen Sie, ob die Mobiltelefonnummer ebenfalls vorhanden ist. Kennzeichnen Sie die geschäftliche Telefonnummer nur als primär, wenn keine Mobiltelefonnummer vorhanden ist. | true | Verwenden Sie die Ausdruckszuordnung: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | Verwenden Sie die Ausdruckszuordnung: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * In SuccessFactors sind die geschäftliche E-Mail-Adresse und die geschäftliche Telefonnummer primär. <br> * Wenn in Azure AD eine Mobiltelefonnummer verfügbar ist, legen Sie diese als geschäftliche Telefonnummer fest, und verwenden Sie andernfalls telephoneNumber. | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Nicht festgelegt\] | 

* Wenn in der Attributzuordnung für das Rückschreiben keine Zuordnung für die Telefonnummer enthalten ist, wird nur die E-Mail-Adresse in den Rückschreibvorgang eingeschlossen.
* Während des Onboardings einer Neueinstellung in Employee Central sind die geschäftliche E-Mail-Adresse und Telefonnummer möglicherweise noch nicht verfügbar. Wenn das Festlegen der geschäftlichen E-Mail-Adresse und Telefonnummer als primär während des Onboardings obligatorisch ist, können Sie bei der Erstellung des neu eingestellten Mitarbeiters Platzhalterwerte für geschäftliche Telefonnummer und E-Mail-Adresse festlegen, die später von der Rückschreib-App aktualisiert werden.
 
### <a name="enabling-writeback-with-userid"></a>Aktivieren des Rückschreibens mit UserID

Die Writeback-App von SuccessFactors verwendet die folgende Logik, um Benutzerobjektattribute zu aktualisieren: 
* Zuerst sucht sie im Changeset nach dem Attribut *userId*. Wenn dies vorhanden ist, wird „UserId“ zum Durchführen des SuccessFactors-API-Aufrufs verwendet. 
* Wird *userId* nicht gefunden, wird standardmäßig der Attributwert *personIdExternal* verwendet. 

In der Regel entspricht der Attributwert *personIdExternal* in SuccessFactors dem Wert des Attributs *userId*. In Szenarien wie Wiedereinstellung und Mitarbeiterkonvertierung verfügt ein Mitarbeiter in SuccessFactors möglicherweise über zwei Mitarbeiterdatensätze: einen aktiven und einen inaktiven. Aktualisieren Sie in solchen Szenarien die Konfiguration der SuccessFactors-Bereitstellungs-Apps wie unten beschrieben, um sicherzustellen, dass durch das Zurückschreiben das aktive Benutzerprofil aktualisiert wird. Diese Konfiguration sorgt dafür, dass *userId* immer in dem für den Connector sichtbaren Changeset vorhanden ist und beim SuccessFactors-API-Aufruf verwendet wird.

1. Öffnen Sie SuccessFactors für die Azure AD-Benutzerbereitstellungs-App oder SuccessFactors für die lokale AD-Benutzerbereitstellungs-App. 
1. Stellen Sie sicher, dass in einem Attribut des Typs „extensionAttribute“ *(extensionAttribute1-15)* in Azure AD immer der Wert *userId* aus dem aktiven Mitarbeiterdatensatz der einzelnen Mitarbeiter gespeichert wird. Dazu müssen Sie das Attribut *userId* von SuccessFactors einem der Attribute des Typs „extensionAttribute“ in Azure AD zuordnen. 
    > [!div class="mx-imgBorder"]
    > ![Zuordnung von eingehenden UserID-Attributen](./media/sap-successfactors-integration-reference/inbound-userid-attribute-mapping.png)
1. Anweisungen zu JSONPath-Einstellungen finden Sie im Abschnitt [Umgang mit Wiedereinstellungen](#handling-rehire-scenario). Damit können Sie sicherstellen, dass der Wert *userId* des aktuellen Mitarbeiterdatensatzes an Azure AD übertragen wird. 
1. Speichern Sie die Zuordnung. 
1. Führen Sie den Bereitstellungsauftrag aus, um zu gewährleisten, dass die Werte des Attributs *userId* an Azure AD übertragen werden. 
    > [!NOTE]
    > Wenn Sie SuccessFactors für die lokale Active Directory-Benutzerbereitstellung verwenden, konfigurieren Sie AAD Connect so, dass der Attributwert *UserId* von der lokalen Active Directory-Instanz mit Azure AD synchronisiert wird.   
1. Öffnen Sie die SuccessFactors-Writeback-App im Azure-Portal. 
1. Ordnen Sie das gewünschte *extensionAttribute*, das den Wert „userId“ enthält, dem SuccessFactors-Attribut *userId* zu.
    > [!div class="mx-imgBorder"]
    > ![Rückschreiben der UserID-Attributzuordnung](./media/sap-successfactors-integration-reference/userid-attribute-mapping.png)
1. Speichern Sie die Zuordnung. 
1. Wechseln Sie zu *Attributzuordnung -> Erweitert -> Schema überprüfen*, um den JSON-Schema-Editor zu öffnen.
1. Laden Sie eine Kopie des Schemas als Sicherung herunter. 
1. Drücken Sie im Schema-Editor STRG+F, und suchen Sie nach dem JSON-Knoten, der die userId-Zuordnung zu einem Azure AD-Quellattribut enthält. 
1. Aktualisieren Sie das Attribut „flowBehavior“ von „FlowWhenChanged“ in „FlowAlways“, wie unten dargestellt. 
    > [!div class="mx-imgBorder"]
    > ![Verhaltensaktualisierung des Zuordnungsflows](./media/sap-successfactors-integration-reference/mapping-flow-behavior-update.png)
1. Speichern Sie die Zuordnung, und testen Sie das Rückschreibeszenario mit einer bedarfsorientierten Bereitstellung. 

### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Nicht unterstützte Szenarien für das Rückschreiben von Telefonnummern und E-Mail-Adressen

* In Employee Central werden während des Onboardings die persönliche E-Mail-Adresse und die persönliche Telefonnummer als primär festgelegt. Diese Einstellung kann von der Rückschreib-App nicht geändert werden, um die geschäftliche E-Mail-Adresse und die geschäftliche Telefonnummer als primär festzulegen.
* In Employee Central ist die geschäftliche Telefonnummer als primär festgelegt. Die Rückschreib-App kann dies nicht ändern und stattdessen die Mobiltelefonnummer als primär festlegen.
* Die Rückschreib-App kann die aktuellen primären Flageinstellungen nicht lesen und verwendet die gleichen Werte für den Schreibvorgang. Es werden immer die in der Attributzuordnung konfigurierten Flagwerte verwendet. 

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zum Konfigurieren der Bereitstellung aus SuccessFactors in Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Weitere Informationen zum Konfigurieren des Rückschreibens an SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Weitere Informationen zu unterstützten SuccessFactors-Attributen für die eingehende Bereitstellung](sap-successfactors-attribute-reference.md)










