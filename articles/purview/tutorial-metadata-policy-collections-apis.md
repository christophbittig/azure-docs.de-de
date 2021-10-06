---
title: 'Metadatenrichtlinien und -rollen-APIs für Azure Purview-Sammlungen – Schnellstart: Verwalten der rollenbasierten Zugriffssteuerung für Purview-Sammlungen'
description: In diesem Tutorial wird die Verwaltung der rollenbasierten Zugriffssteuerung für diese Sammlungen durch Benutzer, Gruppen oder Dienstprinzipale in Ihrem Unternehmen über Azure Purview-APIs erklärt.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 52c38e40d96b6d6f650a8b57fe2d5826a9a81842
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219683"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-rbac-on-azure-purview-collections"></a>Tutorial: Verwenden von REST-APIs zum Verwalten der rollenbasierten Zugriffssteuerung für Azure Purview-Sammlungen 

Im August 2021 wurde die Zugriffssteuerung in Purview von Azure IAM (Steuerungsebene) in [Azure Purview Collections](how-to-create-and-manage-collections.md) (Datenebene) verschoben. Diese Änderung ermöglicht es Datenkuratoren und Administratoren eines Unternehmens eine präzisere Zugriffssteuerung für ihre Datenquellen, die von Purview überprüft werden, und bietet Organisationen die Möglichkeit, den richtigen Zugriff und die richtige Nutzung ihrer Daten zu überwachen.

Dieses Tutorial führt Sie durch die schrittweise Verwendung der **[Azure Purview Metadata Policy-APIs](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** , um Benutzer, Gruppen oder Dienstprinzipale zu einer Sammlung hinzuzufügen und deren Rollen in der betreffenden Sammlung zu verwalten oder zu entfernen. REST-APIs sind eine alternative Methode zur Verwendung des Azure-Portals oder von Purview Studio, um die gleiche präzise rollenbasierte Zugriffssteuerung zu erreichen.

Der [Leitfaden zu Purview-Berechtigungen](catalog-permissions.md#roles) enthält weitere Informationen zu den einzelnen integrierten Rollen in Azure Purview und ordnet die Rolle der Ebene der Zugriffsberechtigungen zu, die dem Benutzer gewährt werden.


## <a name="metadata-policy-api-reference-summary"></a>API-Referenz für Metadatenrichtlinien – Zusammenfassung
Diese Tabelle enthält eine Übersicht über die **[API-Referenz für Azure Purview-Metadatenrichtlinien](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** . Ersetzen Sie {pv-acc-name} durch den Namen Ihres Purview-Kontos, bevor Sie diese APIs ausführen. Wenn Ihr Purview-Kontoname beispielsweise „FabrikamPurviewAccount“ ist, lauten Ihre API-Endpunkte „FabrikamPurviewAccount.purview.azure.com“.

|**API-Funktion**|**REST-Methode**|**API-Endpunkt**|**Beschreibung**|
|:-|:-|:-|:-|
|Alle Metadatenrollen lesen|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Liest alle Metadatenrollen aus Ihrem Purview-Konto.|
|Metadatenrichtlinie anhand Sammlungsname lesen|GET|https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| Liest die Metadatenrichtlinie anhand eines angegebenen Sammlungsnamens (der willkürliche sechsstellige Name, der von Purview beim Erstellen der Richtlinie generiert wurde).|
|Metadatenrichtlinie anhand Richtlinien-ID lesen|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Liest die Metadatenrichtlinie anhand einer angegebenen Richtlinien-ID. Die Richtlinien-ID weist das GUID-Format auf.|
|Alle Metadatenrichtlinien lesen|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Liest alle Metadatenrichtlinien aus Ihrem Purview-Konto. Sie können eine bestimmte Richtlinie, die Sie verwenden möchten, in der JSON-Ausgabeliste auswählen, die von dieser API ausgegeben wird.|
|Metadatenrichtlinie aktualisieren/erstellen|PUT|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Aktualisiert die Metadatenrichtlinie anhand einer angegebenen Richtlinien-ID. Die Richtlinien-ID weist das GUID-Format auf.|

## <a name="purview-catalog-collections-api-reference-summary"></a>API-Referenz für Purview-Katalogsammlungen – Zusammenfassung
Diese Tabelle enthält eine Übersicht über die **APIs für Purview-Sammlungen**. Klicken Sie unten auf die einzelnen API-VORGÄNGE, um eine vollständige Dokumentation zur jeweiligen API zu erhalten.

| **VORGÄNGE** | **Beschreibung** |
|:-|:-|
| [Sammlung erstellen oder aktualisieren](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | Erstellt oder aktualisiert eine Sammlungsentität. |
| [Löschen einer Sammlung](/rest/api/purview/accountdataplane/collections/delete-collection) |Löscht eine Sammlungsentität. |
| [Sammlung abrufen](/rest/api/purview/accountdataplane/collections/get-collection) |Sammlungen abrufen|
| [Sammlungspfad abrufen](/rest/api/purview/accountdataplane/collections/get-collection-path) |Ruft die Ketten für den übergeordneten Namen und den Anzeigenamen ab, die den Sammlungspfad darstellen.|
| [Untergeordnete Sammlungsnamen auflisten](/rest/api/purview/accountdataplane/collections/list-child-collection-names) |Listet die Namen der untergeordneten Sammlungen in der Sammlung auf.|
| [Sammlungen auflisten](/rest/api/purview/accountdataplane/collections/list-collections) |Listet die Sammlungen im Konto auf.|


- Bei Verwendung der API muss dem Dienstprinzipal (SP), dem Benutzer oder der Gruppe, der/die die API ausführt, die Rolle [Sammlungsadministrator](how-to-create-and-manage-collections.md#check-permissions) in Purview zugewiesen sein, damit diese API erfolgreich ausgeführt werden kann.
- Für alle Purview-APIs, die {collectionName} erfordern, benötigen Sie „name“ (und nicht „friendlyName“). Ersetzen Sie {collectionName} durch die tatsächliche sechsstellige alphanumerische Zeichenfolge für den Sammlungsnamen. Beachten Sie, dass diese nicht mit dem Anzeigenamen übereinstimmt, den Sie beim Erstellen der Sammlung angegeben haben. Wenn Sie {collectionName} nicht zur Hand haben, können Sie die [Sammlung auflisten](/rest/api/purview/accountdataplane/collections/list-collections)-API verwenden, um den sechsstelligen Sammlungsnamen in der JSON-Ausgabe auszuwählen.
- JSON-Beispiel: 

```json
{
    "name": "74dhe7", 
    "friendlyName": "Friendly Name",
    "parentCollection": {
        "type": "CollectionReference",
        "referenceName": "{your_purview_account_name}"
    },
    "systemData": {
        "createdBy": "{guid}",
        "createdByType": "Application",
        "createdAt": "2021-08-26T21:21:51.2646627Z",
        "lastModifiedBy": "7f8d47e2-330c-42f0-8744-fcfb1ecb3ea0",
        "lastModifiedByType": "Application",
        "lastModifiedAt": "2021-08-26T21:21:51.2646628Z"
    },
    "collectionProvisioningState": "Succeeded"
}
```

### <a name="policy-json-description"></a>JSON-Beschreibung der Richtlinie
Details zu einigen wichtigen Bezeichnern in der JSON-Ausgabe, die von den Sammlungs-APIs erhalten wurde.

**Name**: Der Name der Richtlinie. 

**Id**: Der eindeutige Bezeichner für die Richtlinie.

**Version**: Die letzte Versionsnummer der Richtlinie. \*\*(Die Versionsnummer wird jedes Mal erhöht, wenn die „Metadatenrichtlinie aktualisieren“-API aufgerufen wird. Stellen Sie sicher, dass Sie die neueste Kopie der Richtlinie abrufen, indem Sie die „Richtlinie anhand Richtlinien-ID abrufen“-API aufrufen. Diese Aktualisierung muss jedes Mal ausgeführt werden, bevor die „Richtlinie aktualisieren/erstellen“-API aufgerufen wird, damit Sie immer über die neueste Version des JSON-Codes verfügen.)

**DecisionRules:** Listet die Regeln und die Auswirkung dieser Richtlinie auf. Bei Metadatenrichtlinien ist die Auswirkung immer „Permit“.

## <a name="use-purview-rest-apis-to-add-or-remove-usergroupserviceprincipal-to-a-collection-or-role"></a>Verwenden von Purview-REST-APIs, um Benutzer, Gruppen, Dienstprinzipale zu einer Sammlung oder Rolle hinzuzufügen oder daraus zu entfernen
Zusammen mit JSON-Beispielausgaben wird die Verwendung der APIs ausführlich beschrieben. Es wird dringend empfohlen, den folgenden Schritten nacheinander zu folgen, um ein besseres Verständnis der Purview-APIs für Metadatenrichtlinien zu erhalten.

## <a name="get-all-metadata-roles"></a>Abrufen aller Metadatenrollen
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```
Listet alle verfügbaren Metadatenzugriffsberechtigungsrollen auf.

Die JSON-Ausgabe beschreibt die Rollen und die zugehörigen Berechtigungen in diesem Format.

### <a name="default-metadata-roles"></a>Standardmetadatenrollen

|**Rollen-ID**|**Berechtigungen**|**Rollenbeschreibung**|
|:-|:-|:-|
|purviewmetadatarole\_builtin\_data-source-administrator|Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read|Gewährt anderen Zugriff zum Lesen und Schreiben von Sammlungen, Registrieren von Datenquellen und Auslösen von Überprüfungen.|
|purviewmetadatarole\_builtin\_collection-administrator|Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write|Gewährt Vollzugriff auf die gesamte Sammlung auf Administratorebene, einschließlich Hinzufügen und Entfernen von Benutzern und Dienstprinzipalnamen zur bzw. aus der Sammlung, Verwalten von Rechten, Gewähren und Widerrufen des Zugriffs. In einigen Fällen kann der Sammlungsadministrator vom Ersteller der Sammlung abweichen.|
|purviewmetadatarole\_builtin\_purview-reader|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read|Gewährt nur Lesezugriff für die Datenbehandlung und alle Metadaten, z. B. Klassifizierungen, Vertraulichkeitsbezeichnungen, Erkenntnissen, Lesen von Ressourcen in einer Sammlung, mit Ausnahme von Überprüfungsbindungen.|
|purviewmetadatarole\_builtin\_data-curator|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read|Gewährt Vollzugriff für die Datenbehandlung und alle Metadaten, z. B. Klassifizierungen, Vertraulichkeitsbezeichnungen, Erkenntnissen, Lesen von Ressourcen in einer Sammlung, mit Ausnahme von Überprüfungsbindungen.|
|purviewmetadatarole\_builtin\_data-share-contributor|Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write|Gewährt Zugriff auf Datenfreigaben als Mitwirkender.|

```json
{
  "values": [
    {
      "id": "purviewmetadatarole_builtin_data-curator",
      "name": "data-curator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Curator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/data/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-source-administrator",
      "name": "data-source-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Source Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/scan/read",
                "Microsoft.Purview/accounts/scan/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_collection-administrator",
      "name": "collection-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Collection Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/collection/read",
                "Microsoft.Purview/accounts/collection/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_purview-reader",
      "name": "purview-reader",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Purview Reader",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-share-contributor",
      "name": "data-share-contributor",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data share contributor",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/share/read",
                "Microsoft.Purview/accounts/share/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    }
  ]
}
```

## <a name="get-all-metadata-policies"></a>Abrufen aller Metadatenrichtlinien
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies?api-version=2021-07-01
```
Listet alle Metadatenrichtlinien, die in der gesamten Sammlungshierarchie verfügbar sind, beginnend mit der Stammsammlung und alle untergeordneten Richtlinien in einem hierarchischen Format auf. Die Hierarchie beginnt mit der Stammsammlung am Anfang, gefolgt von den untergeordneten Sammlungen. Jede untergeordnete Sammlung kapselt die jeweiligen untergeordnete Sammlungen der nächsten Ebene.
Beispiel:

```json
{
  "values": [
    {
      "name": "policy_FabrikamPurview",
      "id": "9b2f1cb9-584c-4a16-811e-9232884b5cac",
      "version": 30,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "fabrikampurview"
                },
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "8988fe5c-5736-4179-9435-0a64c273b90b",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9",
                    "26f98046-5b02-4fa9-b709-e0519c658891",
                    "73fc02dc-becd-468b-a2a3-82238e722dae"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "ffd851fa-86ec-431b-95ea-8b84d5012383",
                    "cf84b126-4384-4952-91f1-7f705b25e569",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b",
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "649f56ab-2dd2-40de-a731-3d3f28e7af92",
                    "c29a5809-f9ec-49fd-b762-2d4d64abb93e",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "73fc02dc-becd-468b-a2a3-82238e722dae",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "d34eb741-be5e-4098-90d7-eca8d4a5153f",
                    "664ec992-9af0-4773-88f2-dc39edc46f6f",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:fabrikampurview",
            "name": "permission:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_purview-reader:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_purview-reader:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "fabrikampurview"
        }
      }
    },
    {
      "name": "policy_b2zpf1",
      "id": "12b0bb28-2acc-413e-8fe1-179ff9cc54c3",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "b2zpf1"
                },
                {
                  "fromRule": "permission:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:b2zpf1"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "name": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:b2zpf1",
            "name": "permission:b2zpf1",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:b2zpf1"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "b2zpf1"
        },
        "parentCollectionName": "ukx7pq"
      }
    },
    {
      "name": "policy_7wte2n",
      "id": "a72084e4-ccab-4aec-a364-08ab001e4999",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "7wte2n"
                },
                {
                  "fromRule": "permission:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:7wte2n"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "name": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:7wte2n",
            "name": "permission:7wte2n",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:7wte2n"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "7wte2n"
        },
        "parentCollectionName": "ukx7pq"
      }
    }
  ]
}
```

## <a name="get-selected-metadata-policy"></a>Abrufen einer bestimmten Metadatenrichtlinie 
Es gibt zwei APIs zum Abrufen der JSON-Struktur der Metadatenrichtlinie einer bestimmten Sammlung – entweder durch Angabe von {collectionName} oder {PolicyID}.
Beide APIs (in den folgenden beiden Abschnitten beschrieben) dienen demselben Zweck, und die JSON-Ausgaben beider APIs sind identisch.

### <a name="get-metadatapolicy-of-the-collection-by-collectionname"></a>Abrufen der Metadatenrichtlinie der Sammlung anhand des Sammlungsnamens
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```
1. Der Name des Purview-Kontos ist {your_purview_account_name}. Ersetzen Sie ihn durch den Namen Ihres Purview-Kontos.
1. Suchen Sie in der JSON-Ausgabe der vorherigen API „Alle Metadatenrichtlinien abrufen“ den Abschnitt { "type": "CollectionReference", "referenceName": "7xkdg2"}.
1. Ersetzen Sie „{collectionName}“ in der API-URL durch den Wert von „referenceName“: „{6-char-collection-name}“. Wenn der sechsstellige Sammlungsname „7xkdg2“ ist, sieht die API-URL also wie folgt aus: https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01
1. Führen Sie diese API jetzt aus. 

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```

### <a name="get-metadatapolicy-of-the-collection-by-policyid"></a>Abrufen der Metadatenrichtlinie der Sammlung anhand der Richtlinien-ID
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```
1. Der Name des Purview-Kontos ist {your_purview_account_name}. Ersetzen Sie ihn durch den Namen Ihres Purview-Kontos.
1. Suchen Sie in der JSON-Ausgabe der vorherigen API „Alle Metadatenrichtlinien abrufen“ den Abschnitt {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}.
1. Ersetzen Sie „{policyId}“ in der API-URL durch den Wert von „id“. Wenn „{policy-guid}“ „c6639bb2-9c41-4be0-912b-775750e725de“ ist, sieht die API-URL also wie folgt aus: https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01
1. Führen Sie diese API jetzt aus. Beachten Sie, dass die Ausgabe dieses API-Aufrufs und die Ausgabe des vorherigen API-Aufrufs identisch sind. Sie können eine der beiden wie zuvor im Tutorial erwähnt auswählen.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```


## <a name="update-policy-addremove-usergroup-from-collection"></a>Aktualisieren der Richtlinie: Benutzer/Gruppe zur Sammlung hinzufügen bzw. daraus entfernen
```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

In diesem Schritt wird der im vorherigen Schritt erhaltene JSON-Richtliniencode aktualisiert und mithilfe einer PUT-REST-Methode an den Purview-Dienst gepusht.
Unabhängig davon, ob Sie einen Benutzer, eine Gruppe oder einen Dienstprinzipal **hinzufügen** bzw. **entfernen** möchten, führen Sie den gleichen API-Prozess aus.

1. Geben Sie die Objekt-IDs {guid} für den Benutzer, die Gruppe oder den Dienstprinzipal im Array „attributeValueIncludedIn“ des JSON-Codes an.
1. Suchen Sie in der JSON-Ausgabe der „Richtlinie anhand der Richtlinien-ID abrufen“-API im vorherigen das Array „attributeValueIncludedIn“ und fügen Sie die Objekt-ID für den Benutzer, die Gruppe, den Dienstprinzipal im Array **hinzu** oder **entfernen** Sie diese. Wenn Sie sich nicht sicher sind, wie Sie die Benutzer- oder Gruppenobjekt-ID abrufen, finden Sie entsprechende Informationen im Tutorial [Get-AzureADUser](/powershell/module/azuread/get-azureaduser).
1. Beachten Sie, dass die JSON-Zuordnung zu allen vier Rollen mehrere Abschnitte umfasst. Verwenden Sie für die Berechtigungsrolle als Sammlungsadministrator den Abschnitt mit der ID „purviewmetadatarole_builtin_collection-administrator“. Verwenden Sie ebenso den entsprechenden Abschnitt für die anderen Rollen.
1. Um den Vorgang zum Hinzufügen/Entfernen besser zu verstehen, sollten Sie den Unterschied zwischen der JSON-Ausgabe der vorherigen API und der folgenden genau untersuchen. Sie werden feststellen, dass in der folgenden JSON-Ausgabe die Benutzer-ID „3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f“ als Sammlungsadministrator hinzugefügt wurde.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0",
                "3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```
## <a name="add-root-collection-admin"></a>Hinzufügen eines Stammsammlungsadministrators
Standardmäßig ist der Benutzer, der das Purview-Konto erstellt hat, der Stammsammlungsadministrator (Administrator der obersten Ebene in der Sammlungshierarchie). In einigen Fällen muss eine Organisation jedoch den Stammsammlungsadministrator u. U. mit der API ändern. Es kann beispielsweise sein, dass der aktuelle Stammsammlungsadministrator nicht mehr in der Organisation vorhanden ist. In diesen Fällen ist das Azure-Portal möglicherweise für niemanden in der Organisation zugänglich. Daher ist die Verwendung der API zum Zuweisen eines neuen Sammlungsadministrators und Verwalten von Sammlungsberechtigungen unvermeidbar und die einzige Möglichkeit, den Zugriff auf das Purview-Konto zurückzuerlangen.

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
Sie müssen lediglich die Objekt-ID des neuen Stammsammlungsadministrators übergeben. Wie bereits erwähnt, kann die Objekt-ID die ID eines beliebigen Benutzers, einer Gruppe oder eines Dienstprinzipals sein.
```json
{"objectId": "{guid}"}
```
Wenn der Vorgang erfolgreich war, geben alle REST-APIs die HTTP-Antwort 200 – OK zurück.

> [!NOTE]
> Der Benutzer, der diese API aufruft, benötigt Berechtigung „Besitzer“ oder „Benutzerzugriffsadministrator“ für das Purview-Konto, um eine Schreibaktion für das Konto auszuführen.


##  <a name="purview-rest-api-combined-archive"></a>Kombiniertes Archiv für die Purview-REST-API
Damit Sie schnell die ersten Schritte mit Purview-APIs durchführen können, können Sie folgendermaßen vorgehen: Laden Sie ein Archiv aller Purview-REST-API-Spezifikationen [herunter](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip), die in der Datei [azure-purview-rest-api-specs.zip](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip) zusammengefasst sind. Sie können diese API-Vorlagen verwenden, um die Code-, Skript-, Automatisierungs-, [AutoRest](https://github.com/Azure/autorest)- oder Postman-Sammlungen von Purview-APIs besser zu verstehen und eigene zu erstellen.

## <a name="powershell-utility-to-run-purview-apis"></a>PowerShell-Hilfsprogramm zum Ausführen von Purview-APIs
Sie können Purview-REST-APIs mithilfe des PowerShell-Hilfsprogramms [Purview-API-PowerShell](https://aka.ms/purview-api-ps) ausführen. Es kann direkt über den PowerShell-Katalog installiert werden. Sie können damit alle Befehle ausführen, allerdings in Windows PowerShell.

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 


