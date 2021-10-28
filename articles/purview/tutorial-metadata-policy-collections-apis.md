---
title: Informationen zu Metadatenrichtlinien und Rollen-APIs für Azure Purview-Sammlungen
description: In diesem Tutorial wird erläutert, wie Sie die rollenbasierte Zugriffssteuerung für Azure Purview-Sammlungen für Benutzer, Gruppen oder Dienstprinzipale verwalten.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 79848d8f54ea1d019f2a9e6cf93a61cc8b7c49f9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245927"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-on-azure-purview-collections"></a>Tutorial: Verwenden von REST-APIs zum Verwalten der rollenbasierten Zugriffssteuerung für Azure Purview-Sammlungen 

Im August 2021 wurde die Zugriffssteuerung in Azure Purview von Azure Identity & Access Management (Steuerungsebene) zu [Azure Purview-Sammlungen](how-to-create-and-manage-collections.md) (Datenebene) verlagert. Mit dieser Änderung erhalten Datenkuratoren und Administratoren in Unternehmen eine präzisere, differenzierte Zugriffskontrolle auf ihre von Azure Purview gescannten Datenquellen. Die Änderung ermöglicht es Organisationen auch, den richtigen Zugriff und die richtige Verwendung ihrer Daten zu überwachen.

Dieses Tutorial führt Sie durch die schrittweise Verwendung der Azure Purview Metadata Policy-APIs, um Benutzer, Gruppen oder Dienstprinzipale einer Sammlung hinzuzufügen und deren Rollen in der betreffenden Sammlung zu verwalten oder zu entfernen. REST-APIs sind eine alternative Methode zur Verwendung des Azure-Portals oder von Azure Purview Studio, um die gleiche differenzierte rollenbasierte Zugriffssteuerung zu erreichen.

Weitere Informationen zu den integrierten Rollen in Azure Purview finden Sie im [Leitfaden für Azure Purview-Berechtigungen](catalog-permissions.md#roles), Der Leitfaden ordnet die Rollen der Ebene der Zugriffsberechtigungen zu, die Benutzern gewährt werden.

## <a name="metadata-policy-api-reference-summary"></a>API-Referenz für Metadatenrichtlinien: Zusammenfassung
Die folgende Tabelle enthält eine Übersicht über die [API-Referenz für Azure Purview-Metadatenrichtlinien](/rest/api/purview/metadatapolicydataplane/Metadata-Policy). 

Ersetzen Sie {pv-acc-name} durch den Namen Ihres Azure Purview-Kontos, bevor Sie diese APIs ausführen. Wenn Ihr Purview-Kontoname beispielsweise *FabrikamPurviewAccount* ist, heißen Ihre API-Endpunkte *FabrikamPurviewAccount.purview.azure.com*.

| API-Funktion | REST-Methode | API-Endpunkt | Beschreibung | 
| :- | :- | :- | :- | 
| Alle Metadatenrollen lesen| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Liest alle Metadatenrollen aus Ihrem Azure Purview-Konto.| 
| Metadatenrichtlinie anhand Sammlungsname lesen| GET| https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| Liest die Metadatenrichtlinie unter Verwendung eines angegebenen Sammlungsnamens (der zufällige Name mit sechs Zeichen, der von Azure Purview beim Erstellen der Richtlinie generiert wird).| 
| Metadatenrichtlinie anhand Richtlinien-ID lesen| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Liest die Metadatenrichtlinie mithilfe einer angegebenen Richtlinien-ID. Die Richtlinien-ID weist das GUID-Format auf.| 
| Alle Metadatenrichtlinien lesen| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Liest alle Metadatenrichtlinien aus Ihrem Azure Purview-Konto. Sie können eine bestimmte Richtlinie, die Sie verwenden möchten, in der von dieser API generierten JSON-Ausgabeliste auswählen.| 
| Metadatenrichtlinie aktualisieren/erstellen| PUT| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Aktualisiert die Metadatenrichtlinie mithilfe einer angegebenen Richtlinien-ID. Die Richtlinien-ID weist das GUID-Format auf.|
| | | 

## <a name="azure-purview-catalog-collections-api-reference-summary"></a>API-Referenz für Azure Purview-Katalogsammlungen: Zusammenfassung
Die folgende Tabelle enthält eine Übersicht über die APIs für Azure Purview-Sammlungen. Wählen Sie in der linken Spalte den API-Vorgang aus, um eine vollständige Dokumentation zu den einzelnen APIs zu erhalten.

| Vorgang | BESCHREIBUNG | 
| :- | :- | 
| [Sammlung erstellen oder aktualisieren](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | Erstellt oder aktualisiert eine Sammlungsentität. | 
| [Sammlung löschen](/rest/api/purview/accountdataplane/collections/delete-collection) | Löscht eine Sammlungsentität. | 
| [Abrufen der Sammlung](/rest/api/purview/accountdataplane/collections/get-collection) | Ruft eine Sammlung ab.| 
| [Sammlungspfad abrufen](/rest/api/purview/accountdataplane/collections/get-collection-path) | Ruft die Ketten für den übergeordneten Namen und den Anzeigenamen ab, die den Sammlungspfad darstellen.| 
| [Namen untergeordneter Sammlungen auflisten](/rest/api/purview/accountdataplane/collections/list-child-collection-names) | Listet die Namen der untergeordneten Sammlungen in der Sammlung auf.| 
| [Sammlungen auflisten](/rest/api/purview/accountdataplane/collections/list-collections) | Listet die Sammlungen im Konto auf.| 


- Bei Verwendung der API muss dem Dienstprinzipal, dem Benutzer oder der Gruppe, der/die die API ausführt, die Rolle [Sammlungsadministrator](how-to-create-and-manage-collections.md#check-permissions) in Azure Purview zugewiesen sein, damit diese API erfolgreich ausgeführt werden kann.

- Für alle Azure Purview-APIs, die {collectionName} erfordern, müssen Sie *„name“* (und nicht *„friendlyName“* ) verwenden. Ersetzen Sie {collectionName} durch die tatsächliche sechsstellige alphanumerische Zeichenfolge für den Sammlungsnamen. 
   > [!NOTE]
   > Dieser Name stimmt nicht mit dem Anzeigenamen überein, den Sie beim Erstellen der Sammlung angegeben haben. Wenn Sie {collectionName} nicht zur Hand haben, können Sie die [Sammlung auflisten](/rest/api/purview/accountdataplane/collections/list-collections)-API verwenden, um den Sammlungsnamen mit sechs Zeichen in der JSON-Ausgabe auszuwählen.

Hier sehen Sie ein Beispiel für eine JSON-Datei: 

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

Hier finden Sie einige wichtige Bezeichner in der JSON-Ausgabe, die von den Sammlungs-APIs erhalten wurde:

**Name**: Der Name der Richtlinie. 

**ID**: Der eindeutige Bezeichner für die Richtlinie.

**Version**: Die aktuelle Versionsnummer der Richtlinie. 
   > [!IMPORTANT]
   > Die Versionsnummer wird jedes Mal erhöht, wenn die „Metadatenrichtlinie aktualisieren“-API aufgerufen wird. Stellen Sie sicher, dass Sie die neueste Kopie der Richtlinie abrufen, indem Sie die „Richtlinie anhand Richtlinien-ID abrufen“-API aufrufen. Führen Sie diese Aktualisierung jedes Mal aus, bevor Sie die „Richtlinie aktualisieren/erstellen“-API aufrufen, damit Sie immer über die aktuelle Version der JSON-Datei verfügen.

**DecisionRules:** Listet die Regeln und die Auswirkung dieser Richtlinie auf. Bei Metadatenrichtlinien ist die Auswirkung immer *„Permit“* .

## <a name="add-or-remove-users-from-a-collection-or-role"></a>Hinzufügen oder Entfernen von Benutzern in einer Sammlung oder Rolle

Verwenden Sie die Azure Purview-REST-APIs, um einen Benutzer, eine Gruppe oder einen Dienstprinzipal in einer Sammlung oder Rolle hinzuzufügen oder zu entfernen. Eine detaillierte API-Nutzung wird zusammen mit JSON-Beispielausgaben bereitgestellt. Es wird dringend empfohlen, die Anweisungen in den nächsten Abschnitten nacheinander zu befolgen, um die Azure Purview-Metadatenrichtlinien-APIs besser zu verstehen.

## <a name="get-all-metadata-roles"></a>Abrufen aller Metadatenrollen

Führen Sie den folgenden Befehl aus, um alle verfügbaren Zugriffsberechtigungsrollen für Metadaten aufzulisten:

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```

Die JSON-Ausgabe beschreibt die Rollen und die zugehörigen Berechtigungen in diesem Format.

Die Standardmetadatenrollen sind in der folgenden Tabelle aufgeführt:

| Rollen-ID | Berechtigungen | Rollenbeschreibung | 
| :- | :- | :- | 
| purviewmetadatarole\_builtin\_data-source-administrator| Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read| Gewährt anderen Zugriff zum Lesen und Schreiben von Sammlungen, Registrieren von Datenquellen und Auslösen von Überprüfungen.| 
| purviewmetadatarole\_builtin\_collection-administrator| Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write| Vollzugriff auf die gesamte Sammlung auf Administratorebene, einschließlich Hinzufügen oder Entfernen von Benutzern und Dienstprinzipalnamen (SPNs) in der Sammlung, Verwaltungsrechten und Gewähren oder Widerrufen von Zugriff. In einigen Fällen kann der Sammlungsadministrator vom Ersteller der Sammlung abweichen.| 
| purviewmetadatarole\_builtin\_purview-reader| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read| Gewährt nur Lesezugriff für die Datenbehandlung und alle Metadaten, z. B. Klassifizierungen, Vertraulichkeitsbezeichnungen, Erkenntnisse, und das Lesen von Ressourcen in einer Sammlung, mit Ausnahme von Überprüfungsbindungen.| 
| purviewmetadatarole\_builtin\_data-curator| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read| Gewährt Vollzugriff für die Datenbehandlung und alle Metadaten, z. B. Klassifizierungen, Vertraulichkeitsbezeichnungen, Erkenntnisse, und das Lesen von Ressourcen in einer Sammlung, mit Ausnahme von Überprüfungsbindungen.| 
| purviewmetadatarole\_builtin\_data-share-contributor| Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write| Gewährt Zugriff auf Datenfreigaben als Mitwirkender. | 

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
Der vorangehende Befehl listet alle verfügbaren Metadatenrichtlinien in der gesamten Sammlungshierarchie in Form einer Baumstruktur auf, von der Stammsammlung an der Spitze bis zu allen untergeordneten Richtlinien. Jede untergeordnete Sammlung enthält alle untergeordneten Sammlungen der nächsten Ebene.

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

## <a name="get-the-selected-metadata-policy"></a>Abrufen der ausgewählten Metadatenrichtlinie 

Sie können eine von zwei APIs verwenden, um die JSON-Struktur der Metadatenrichtlinie einer bestimmten Sammlung abzurufen, indem Sie entweder {collectionName} oder {PolicyID} angeben.

Wie in den folgenden beiden Abschnitten beschrieben, dienen beide APIs demselben Zweck, und die JSON-Ausgaben beider sind genau gleich.

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-collection-name"></a>Abrufen der Metadatenrichtlinie der Sammlung mithilfe des Sammlungsnamens

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```

1. Der Name des Azure Purview-Kontos ist {your_purview_account_name}. Ersetzen Sie ihn durch den Namen Ihres Azure Purview-Kontos.

1. Suchen Sie in der JSON-Ausgabe der vorherigen API „Alle Metadatenrichtlinien abrufen“ den folgenden Abschnitt: 

    { "type": "CollectionReference", "referenceName": "7xkdg2"}

1. Ersetzen Sie „{collectionName}“ in der API-URL durch den Wert von „referenceName“: „{6-char-collection-name}“. Wenn Ihr sechsstelliger Sammlungsname beispielsweise „7xkdg2“ lautet, wird die API-URL wie folgt formatiert: 

   https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01

1. Führen Sie die folgende API aus: 

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

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-policy-id"></a>Abrufen der Metadatenrichtlinie der Sammlung mithilfe der Richtlinien-ID

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

1. Der Name des Azure Purview-Kontos ist {your_purview_account_name}. Ersetzen Sie ihn durch den Namen Ihres Azure Purview-Kontos.

1. Suchen Sie in der JSON-Ausgabe der vorherigen API „Alle Metadatenrichtlinien abrufen“ den folgenden Abschnitt: 

    {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}

1. Ersetzen Sie „{policyId}“ in der API-URL durch den Wert von „id“. Wenn Ihre „{policy-guid}“ beispielsweise „c6639bb2-9c41-4be0-912b-775750e725de“ ist, wird die API-URL wie folgt formatiert:
 
   https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01

1. Führen Sie nun die folgende API aus: 

   > [!NOTE]
   > Die Ausgabe dieses API-Aufrufs und die Ausgabe des vorherigen API-Aufrufs sind identisch. Wie bereits erwähnt, können Sie jede der beiden APIs wählen.

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


## <a name="update-the-collection-policy"></a>Aktualisieren der Sammlungsrichtlinie

```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

In diesem Abschnitt aktualisieren Sie die Richtlinien-JSON, die Sie im vorangegangenen Schritt erhalten haben, indem Sie einen Benutzer, eine Gruppe oder einen Dienstprinzipal in der Sammlung hinzufügen oder entfernen. Anschließend übermitteln Sie es mit einer PUT-REST-Methode an den Azure Purview-Dienst.

Unabhängig davon, ob Sie einen Benutzer, eine Gruppe oder einen Dienstprinzipal hinzufügen oder entfernen, führen Sie denselben API-Prozess aus.

1. Geben Sie die Objekt-ID {guid} für den Benutzer, die Gruppe oder den Dienstprinzipal im Array „attributeValueIncludedIn“ des JSON-Codes an.

1. Suchen Sie in der JSON-Ausgabe der „Richtlinie anhand der Richtlinien-ID abrufen“-API im vorherigen das Array „attributeValueIncludedIn“ und fügen Sie die Objekt-ID für den Benutzer, die Gruppe, den Dienstprinzipal im Array hinzu oder entfernen Sie diese. Wenn Sie nicht sicher sind, wie die Objekt-ID des Benutzers, der Gruppe oder des Dienstprinzipals abgerufen wird, finden Sie weitere Informationen unter [Get-AzureADUser](/powershell/module/azuread/get-azureaduser).

1. Die JSON-Zuordnung enthält mehrere Abschnitte für jede der vier Rollen. Verwenden Sie für die Berechtigungsrolle als Sammlungsadministrator den Abschnitt mit der ID „purviewmetadatarole_builtin_collection-administrator“. Verwenden Sie ebenso den entsprechenden Abschnitt für die anderen Rollen.

1. Um den Vorgang zum Hinzufügen/Entfernen besser zu verstehen, sollten Sie den Unterschied zwischen der JSON-Ausgabe der vorherigen API und der folgenden Ausgabe genau ansehen. In der folgenden JSON-Ausgabe wurde die Benutzer-ID „3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f“ als Sammlungsadministrator hinzugefügt.

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
## <a name="add-the-root-collection-administrator-role"></a>Hinzufügen der Rolle „Stammsammlungsadministrator“
Standardmäßig ist der Benutzer, der das Azure Purview-Konto erstellt hat, der Stammsammlungsadministrator (d. h. Administrator der obersten Ebene in der Sammlungshierarchie). In einigen Fällen muss eine Organisation jedoch den Stammsammlungsadministrator mithilfe der API ändern. Es kann beispielsweise sein, dass der aktuelle Stammsammlungsadministrator nicht mehr in der Organisation vorhanden ist. In einem solchen Fall kann in der Organisation möglicherweise nicht auf das Azure-Portal zugegriffen werden. Dann ist die Verwendung der API für die Zuweisung eines neuen Stammsammlungsadministrators und die Verwaltung von Sammlungsberechtigungen die einzige Möglichkeit, um wieder Zugriff auf das Azure Purview-Konto zu erhalten.

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
Um den vorherigen Befehl auszuführen, müssen Sie nur die Objekt-ID des neuen Stammsammlungsadministrators übergeben. Wie bereits erwähnt, kann die Objekt-ID die ID eines beliebigen Benutzers, einer Gruppe oder eines Dienstprinzipals sein.

```json
{"objectId": "{guid}"}
```

> [!NOTE]
> Benutzer, die diese API aufrufen, müssen über die Berechtigungen „Besitzer“ oder „Benutzerkonto“ und „Authentifizierung“ (UAA) für das Azure Purview-Konto verfügen, um eine Schreibaktion für das Konto auszuführen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Sie können Azure Purview-REST-APIs mithilfe des [PowerShell-Hilfsprogramms](https://aka.ms/purview-api-ps) ausführen. Es kann direkt über den PowerShell-Katalog installiert werden. Mit diesem Hilfsprogramm können Sie die gleichen Befehle ausführen, allerdings über Windows PowerShell.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
