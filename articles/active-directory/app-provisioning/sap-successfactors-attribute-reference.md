---
title: SAP SuccessFactors-Attributreferenz für Azure Active Directory
description: Hier erfahren Sie, welche SuccessFactors-Attribute von der SuccessFactors- Bereitstellung bzw. personalbasierten Bereitstellung in Azure Active Directory unterstützt werden.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 83e3a0462543131298ee9460225db015d960bc52
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990784"
---
# <a name="sap-successfactors-attribute-reference-for-azure-active-directory"></a>SAP SuccessFactors-Attributreferenz für Azure Active Directory

In diesem Artikel finden Sie Informationen zu folgenden Themen:

- [SuccessFactors-Entitäten und -Attribute](#supported-successfactors-entities-and-attributes)
- [Standardzuordnung von Attributen](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Unterstützte SuccessFactors-Entitäten und -Attribute

Die folgende Tabelle zeigt die Liste der SuccessFactors-Attribute, die standardmäßig in den folgenden beiden Bereitstellungsanwendungen enthalten sind:

- [Benutzerbereitstellung von SuccessFactors in Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [Benutzerbereitstellung von SuccessFactors in Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Informationen zum Erweitern des Schemas um zusätzliche Attribute finden Sie in der [Referenz zur Integration von SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes). 

| \# | SuccessFactors-Entität                  | SuccessFactors-Attribut     | Vorgangstyp |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Lesen           |
| 2  | PerPerson                              | personId                     | Lesen           |
| 3  | PerPerson                              | perPersonUuid                | Lesen           |
| 4  | PerPersonal                            | displayName                  | Lesen           |
| 5  | PerPersonal                            | firstName                    | Lesen           |
| 6  | PerPersonal                            | gender                       | Lesen           |
| 7  | PerPersonal                            | lastName                     | Lesen           |
| 8  | PerPersonal                            | middleName                   | Lesen           |
| 9  | PerPersonal                            | preferredName                | Lesen           |
| 10 | Benutzer                                   | addressLine1                 | Lesen           |
| 11 | Benutzer                                   | addressLine2                 | Lesen           |
| 12 | Benutzer                                   | addressLIne3                 | Lesen           |
| 13 | Benutzer                                   | businessPhone                | Lesen           |
| 14 | Benutzer                                   | cellPhone                    | Lesen           |
| 15 | Benutzer                                   | city                         | Lesen           |
| 16 | Benutzer                                   | country                      | Lesen           |
| 17 | Benutzer                                   | custom01                     | Lesen           |
| 18 | Benutzer                                   | custom02                     | Lesen           |
| 19 | Benutzer                                   | custom03                     | Lesen           |
| 20 | Benutzer                                   | custom04                     | Lesen           |
| 21 | Benutzer                                   | custom05                     | Lesen           |
| 22 | Benutzer                                   | custom06                     | Lesen           |
| 23 | Benutzer                                   | custom07                     | Lesen           |
| 24 | Benutzer                                   | custom08                     | Lesen           |
| 25 | Benutzer                                   | custom09                     | Lesen           |
| 26 | Benutzer                                   | custom10                     | Lesen           |
| 27 | Benutzer                                   | custom11                     | Lesen           |
| 28 | Benutzer                                   | custom12                     | Lesen           |
| 29 | Benutzer                                   | custom13                     | Lesen           |
| 30 | Benutzer                                   | custom14                     | Lesen           |
| 31 | Benutzer                                   | empId                        | Lesen           |
| 32 | Benutzer                                   | homePhone                    | Lesen           |
| 33 | Benutzer                                   | jobFamily                    | Lesen           |
| 34 | Benutzer                                   | nickname                     | Lesen           |
| 35 | Benutzer                                   | state                        | Lesen           |
| 36 | Benutzer                                   | timeZone                     | Lesen           |
| 37 | Benutzer                                   | username                     | Lesen           |
| 38 | Benutzer                                   | zipCode                      | Lesen           |
| 39 | PerPhone                               | areaCode                     | Lesen           |
| 40 | PerPhone                               | countryCode                  | Lesen           |
| 41 | PerPhone                               | Erweiterung                    | Lesen           |
| 42 | PerPhone                               | phoneNumber                  | Lesen           |
| 43 | PerPhone                               | phoneType                    | Lesen           |
| 44 | PerEmail                               | emailAddress                 | Lesen, Schreiben    |
| 45 | PerEmail                               | emailType                    | Lesen           |
| 46 | EmpEmployment                          | firstDateWorked              | Lesen           |
| 47 | EmpEmployment                          | lastDateWorked               | Lesen           |
| 48 | EmpEmployment                          | userId                       | Lesen           |
| 49 | EmpEmployment                          | isContingentWorker           | Lesen           |
| 50 | EmpJob                                 | countryOfCompany             | Lesen           |
| 51 | EmpJob                                 | emplStatus                   | Lesen           |
| 52 | EmpJob                                 | endDate                      | Lesen           |
| 53 | EmpJob                                 | startDate                    | Lesen           |
| 54 | EmpJob                                 | jobTitle                     | Lesen           |
| 55 | EmpJob                                 | position                     | Lesen           |
| 65 | EmpJob                                 | customString13               | Lesen           |
| 56 | EmpJob                                 | managerId                    | Lesen           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Lesen           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Lesen           |
| 59 | EmpJob\.Company                        | company                      | Lesen           |
| 60 | EmpJob\.Company                        | companyId                    | Lesen           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | Lesen           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Lesen           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Lesen           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Lesen           |
| 65 | EmpJob\.Department                     | department                   | Lesen           |
| 66 | EmpJob\.Department                     | departmentId                 | Lesen           |
| 67 | EmpJob\.Division                       | division                     | Lesen           |
| 68 | EmpJob\.Division                       | divisionId                   | Lesen           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Lesen           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Lesen           |
| 71 | EmpJob\.Location                       | LocationName                 | Lesen           |
| 72 | EmpJob\.Location                       | officeLocationAddress        | Lesen           |
| 73 | EmpJob\.Location                       | officeLocationCity           | Lesen           |
| 74 | EmpJob\.Location                       | officeLocationCustomString4  | Lesen           |
| 75 | EmpJob\.Location                       | officeLocationZipCode        | Lesen           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Lesen           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Lesen           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Lesen           |

## <a name="default-attribute-mapping"></a>Standardzuordnung von Attributen

Die folgende Tabelle enthält die standardmäßige Attributzuordnung zwischen den oben aufgeführten SuccessFactors-Attributen und AD/Azure AD-Attributen. Auf dem Blatt „Zuordnung“ der Azure AD-Bereitstellungs-App können Sie diese Standardzuordnung ändern, um Attribute aus der obigen Liste aufzunehmen. 

| \# | SuccessFactors-Entität                  | SuccessFactors-Attribut | Standardzuordnung von AD-/Azure AD-Attributen   | Verarbeitungshinweis                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Als übereinstimmendes Attribut verwendet                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Nicht zugeordnet – als Quellanker verwendet\] | Während der ersten Synchronisierung verknüpft der Bereitstellungsdienst „personUuid“ mit dem vorhandenen objectGuid-Element.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Nicht verfügbar                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Nicht verfügbar                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | Nicht verfügbar                                                                                           |
| 6  | Benutzer                                   | addressLine1             | streetAddress                           | Nicht verfügbar                                                                                           |
| 7  | Benutzer                                   | city                     | l                                       | Nicht verfügbar                                                                                           |
| 8  | Benutzer                                   | country                  | co                                      | Nicht verfügbar                                                                                           |
| 9  | Benutzer                                   | state                    | st                                      | Nicht verfügbar                                                                                           |
| 10 | Benutzer                                   | username                 | samAccountName                          | Nicht verfügbar                                                                                           |
| 11 | Benutzer                                   | zipCode                  | postalCode                              | Nicht verfügbar                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | Nicht verfügbar                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | Nicht verfügbar                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | Nicht verfügbar                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | Nicht verfügbar                                                                                           |
| 16 | EmpJob\.Department                     | department               | department                              | Nicht verfügbar                                                                                           |
| 17 | EmpJob\.Division                       | division                 | company                                 | Nicht verfügbar                                                                                           |
| 18 | EmpJob\.Location                       | officeLocationAddress    | streetAddress                           | Nicht verfügbar                                                                                           |
| 19 | EmpJob\.Location                       | officeLocationZipCode    | postalCode                              | Nicht verfügbar                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Wenn „activeEmploymentsCount=0“ angegeben ist, deaktivieren Sie das Konto.                                           |
