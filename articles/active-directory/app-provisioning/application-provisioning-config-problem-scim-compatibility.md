---
title: 'Bekannte Complianceprobleme im Zusammenhang mit dem SCIM 2.0-Protokoll (System for Cross-Domain Identity Management): Azure AD'
description: Erfahren Sie, wie Sie häufige Kompatibilitätsprobleme mit dem Protokoll beim Hinzufügen einer Anwendung, die nicht aus dem Katalog stammt und die SCIM 2.0 unterstützt, in Azure AD beheben.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/25/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: fddb35cd2b610280440ac01fe5ffc9027a59a2b7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021883"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Bekannte Probleme und Lösungen bei der Einhaltung des SCIM 2.0-Protokolls des Azure AD-Benutzerbereitstellungsdiensts

Über Azure Active Directory (Azure AD) können Benutzer und Gruppen automatisch für alle Anwendungen oder Systeme bereitgestellt werden, denen ein Webdienst mit der in der [SCIM 2.0-Protokollspezifikation](https://tools.ietf.org/html/draft-ietf-scim-api-19) definierten Schnittstelle vorgelagert ist. 

Die Azure AD-Unterstützung für das SCIM 2.0-Protokoll wird unter [Automatisches Bereitstellen von Benutzern und Gruppen aus Azure Active Directory für Anwendungen mit SCIM (System for Cross-domain Identity Management)](use-scim-to-provision-users-and-groups.md) beschrieben. Dort werden auch die einzelnen Teile des Protokolls aufgeführt, die implementiert werden, um Benutzer und Gruppen aus Azure AD für Anwendungen, die SCIM 2.0 unterstützen, automatisch bereitzustellen.

In diesem Artikel werden aktuelle und ehemalige Probleme mit dem Bereitstellungsdienst für Azure AD-Benutzer im Zusammenhang mit der Einhaltung des SCIM 2.0-Protokolls und Möglichkeiten zur Umgehung dieser Probleme beschrieben.

## <a name="understanding-the-provisioning-job"></a>Grundlegendes zum Bereitstellungsauftrag
Vom Bereitstellungsdienst wird das Konzept eines Auftrags verwendet, um Vorgänge für eine Anwendung auszuführen. Die Auftrags-ID (jobID) ist in der [Statusanzeige](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) angegeben. Alle neuen Bereitstellungsanwendungen werden mit einer Auftrags-ID erstellt, die mit „scim“ beginnt. Der SCIM-Auftrag stellt den aktuellen Zustand des Diensts dar. Ältere Aufträge haben die ID „customappsso“. Dieser Auftrag stellt den Zustand des Diensts im Jahr 2018 dar. 

Bei Verwendung einer Kataloganwendung enthält der Auftrag in der Regel den Namen der App (beispielsweise „zoom snowFlake“ oder „dataBricks“). Wenn Sie eine Kataloganwendung verwenden, können Sie diese Dokumentation überspringen. Die Informationen gelten hauptsächlich für katalogfremde Anwendungen mit der Auftrags-ID „SCIM“ oder „customAppSSO“.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0-Konformitätsprobleme und Status
Bei Elementen, die in der folgenden Tabelle als korrigiert markiert sind, weist der SCIM-Auftrag das korrekte Verhalten auf. Bei den vorgenommenen Änderungen haben wir uns zwar um Abwärtskompatibilität bemüht. Es empfiehlt sich jedoch nicht, das alte Verhalten zu implementieren. Das neue Verhalten sollte für alle neuen Implementierungen sowie bei der Aktualisierung bereits vorhandener Implementierungen verwendet werden.

> [!NOTE]
> Für die 2018 vorgenommenen Änderungen können Sie das customappsso-Verhalten wiederherstellen. Für die seit 2018 vorgenommenen Änderungen können Sie die URLs verwenden, um das ältere Verhalten wiederherzustellen. Bei den vorgenommenen Änderungen haben wir uns um Abwärtskompatibilität bemüht: Sie können entweder zur alten Auftrags-ID zurückkehren oder ein Flag verwenden. Es empfiehlt sich jedoch wie bereits erwähnt nicht, das alte Verhalten zu implementieren. Das neue Verhalten sollte für alle neuen Implementierungen sowie bei der Aktualisierung bereits vorhandener Implementierungen verwendet werden.

| **SCIM 2.0-Konformitätsproblem** |  **Korrigiert?** | **Korrekturdatum**  |  **Abwärtskompatibilität** |
|---|---|---|
| Azure AD erfordert „/scim“ im Stamm der SCIM-Endpunkt-URL der Anwendung  | Ja  |  18. Dezember 2018 | Downgrade zu „customappSSO“ |
| Erweiterungsattribute verwenden vor Attributnamen die Punktnotation „.“ anstelle der Doppelpunktnotation „:“. |  Ja  | 18. Dezember 2018  | Downgrade zu „customappSSO“ |
| Patchanforderungen für mehrwertige Attribute enthalten eine ungültige Syntax für Pfadfilter | Ja  |  18. Dezember 2018  | Downgrade zu „customappSSO“ |
| Anforderungen zur Erstellung von Gruppen enthalten einen ungültigen Schema-URI | Ja  |  18. Dezember 2018  |  Downgrade zu „customappSSO“ |
| Aktualisieren des PATCH-Verhaltens zum Sicherstellen der Konformität (z. B. aktiv als boolescher Wert und ordnungsgemäßes Entfernen von Gruppenmitgliedschaften) | Nein | TBD| Verwenden eines Featureflags |

## <a name="flags-to-alter-the-scim-behavior"></a>Flags zum Ändern des SCIM-Verhaltens
Verwenden Sie die weiter unten angegebenen Flags in der Mandanten-URL Ihrer Anwendung, um das Standardverhalten des SCIM-Clients zu ändern.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.png" alt-text="SCIM-Flags zum Ändern des Verhaltens":::

Verwenden Sie die folgende URL, um das PATCH-Verhalten zu aktualisieren und SCIM-Konformität sicherzustellen. Das Flag ändert die folgenden Verhalten:                
- Anforderungen zum Deaktivieren von Benutzern
- Anforderungen zum Hinzufügen eines Zeichenfolgenattributs mit einem einzelnen Wert
- Anforderungen zum Ersetzen mehrerer Attribute
- Anforderungen zum Entfernen eines Gruppenmitglieds        

Dieses Verhalten ist derzeit nur verfügbar, wenn Sie das Flag verwenden, wird jedoch in den nächsten Monaten zum Standardverhalten. Beachten Sie, dass dieses Featureflag bei bedarfsgesteuerter Bereitstellung nicht funktioniert. 
  * **URL (SCIM-konform):** aadOptscim062020
  * **SCIM-RFC-Verweise:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2    

Im Folgenden finden Sie Beispielanforderungen, die darlegen sollen, was die Synchronisierungs-Engine derzeit sendet, im Vergleich zu den Anforderungen, die gesendet werden, nachdem das Featureflag aktiviert wurde. 

**Anforderungen zum Deaktivieren von Benutzern:**

**Ohne Featureflag**
  ```json
  {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": "False"
        }
    ]
}
  ```

**Mit Featureflag**
  ```json
  {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
}
  ```

**Anforderungen zum Hinzufügen eines Zeichenfolgenattributs mit einem einzelnen Wert:**

**Ohne Featureflag**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
    {
        "op": "Add",
        "path": "nickName",
        "value": "Babs"
     }
   ]
}

  ```

**Mit Featureflag**
  ```json
{
  "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
  "Operations": [
    {
      "op": "add",
      "path": "nickName",
      "value": "Babs"
    }
  ]
}
  ```

**Anforderungen zum Ersetzen mehrerer Attribute:**

**Ohne Featureflag**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "Replace",
            "path": "displayName",
            "value": "Pvlo"
        },
        {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "TestBcwqnm@test.microsoft.com"
        },
        {
            "op": "Replace",
            "path": "name.givenName",
            "value": "Gtfd"
        },
        {
            "op": "Replace",
            "path": "name.familyName",
            "value": "Pkqf"
        },
        {
            "op": "Replace",
            "path": "externalId",
            "value": "Eqpj"
        },
        {
            "op": "Replace",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber",
            "value": "Eqpj"
        }
    ]
}
  ```

**Mit Featureflag**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "TestMhvaes@test.microsoft.com"
        },
        {
            "op": "replace",
            "value": {
                "displayName": "Bjfe",
                "name.givenName": "Kkom",
                "name.familyName": "Unua",
                "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber": "Aklq"
            }
        }
    ]
} 
  ```

**Anforderungen zum Entfernen eines Gruppenmitglieds:**

**Ohne Featureflag**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "Remove",
            "path": "members",
            "value": [
                {
                    "value": "u1091"
                }
            ]
        }
    ]
} 
  ```

**Mit Featureflag**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"7f4bc1a3-285e-48ae-8202-5accb43efb0e\"]"
        }
    ]
}
  ```


  * **Downgrade-URL:** Sobald das neue SCIM-konforme Verhalten bei nicht im Katalog enthaltenen Anwendungen zum Standardverhalten wird, können Sie mit der folgenden URL ein Rollback auf das alte, nicht SCIM-konforme Verhalten durchführen: AzureAdScimPatch2017



## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Upgraden vom älteren customappsso-Auftrag zum SCIM-Auftrag
Durch die folgenden Schritte wird Ihr vorhandener customappsso-Auftrag gelöscht und ein neuer SCIM-Auftrag erstellt: 

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
2. Navigieren Sie im Azure-Portal im Abschnitt **Azure Active Directory > Unternehmensanwendungen** zu Ihrer SCIM-Anwendung, und wählen Sie sie aus.
3. Kopieren Sie im Abschnitt **Eigenschaften** Ihrer SCIM-App die **Objekt-ID**.
4. Wechseln Sie in einem neuen Webbrowserfenster zu https://developer.microsoft.com/graph/graph-explorer, und melden Sie sich als Administrator für den Azure AD-Mandanten an, unter dem Ihre App hinzugefügt wurde.
5. Führen Sie im Graph-Tester den unten stehenden Befehl aus, um die ID Ihres Bereitstellungsauftrags zu suchen. Ersetzen Sie „[object-id]“ durch die Dienstprinzipal-ID (Objekt-ID), die Sie im dritten Schritt kopiert haben.

   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Aufträge abrufen](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Aufträge abrufen") 


6. Kopieren Sie in den Ergebnissen die vollständige „ID“-Zeichenfolge, die mit „customappsso“ oder mit „scim“ beginnt.
7. Führen Sie den folgenden Befehl aus, um die Konfiguration für die Attributzuordnung abzurufen, damit Sie eine Sicherung erstellen können. Verwenden Sie dieselbe [object-id] wie zuvor, und ersetzen Sie die [job-id] durch die ID des Bereitstellungsauftrags, die Sie im letzten Schritt kopiert haben.

   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`

   ![Schema abrufen](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Schema abrufen") 

8. Kopieren Sie die JSON-Ausgabe aus dem letzten Schritt, und speichern Sie sie in einer Textdatei. Der JSON-Code enthält alle benutzerdefinierten Attributzuordnungen, die Sie in Ihrer alten App hinzugefügt haben, und sollte einige Tausend Zeilen mit JSON-Code umfassen.
9. Führen Sie den folgenden Befehl aus, um den Bereitstellungsauftrag zu löschen:

   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Führen Sie den folgenden Befehl aus, um einen neuen Bereitstellungsauftrag zu erstellen, der über die neuesten Fehlerbehebungen für den Dienst verfügt.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   "templateId": "scim"   }`

11. Kopieren Sie in den Ergebnissen des letzten Schritts die vollständige „ID“-Zeichenfolge, die mit „scim“ beginnt. Sie können optional auch Ihre alten Attributzuordnungen erneut anwenden. Führen Sie dazu den Befehl unten aus, und ersetzen Sie „[new-job-id]“ durch die neue Auftrags-ID, die Sie kopiert haben. Geben Sie außerdem die JSON-Ausgabe aus Schritt 7 als Anforderungstext ein.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Wechseln Sie wieder zurück zum ersten Webbrowserfenster, und wählen Sie die Registerkarte **Bereitstellung** für Ihre Anwendung aus.
13. Überprüfen Sie Ihre Konfiguration, und starten Sie anschließend den Bereitstellungsauftrag. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Downgraden vom SCIM-Auftrag zum customappsso-Auftrag (nicht empfohlen)
 Das alte Verhalten kann zwar mithilfe eines Downgrades wiederhergestellt werden, dies wird jedoch nicht empfohlen, da einige der von uns vorgenommenen Aktualisierungen nicht für customappsso zur Verfügung stehen und customappsso möglicherweise irgendwann nicht mehr unterstützt wird. 

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
2. Erstellen Sie im Azure-Portal im Abschnitt **Azure Active Directory > Unternehmensanwendungen > Anwendung erstellen** eine neue Anwendung mit der Option **Non-gallery** (Nicht aus dem Katalog).
3. Kopieren Sie im Abschnitt **Eigenschaften** der neuen benutzerdefinierten App die **Objekt-ID**.
4. Wechseln Sie in einem neuen Webbrowserfenster zu https://developer.microsoft.com/graph/graph-explorer, und melden Sie sich als Administrator für den Azure AD-Mandanten an, unter dem Ihre App hinzugefügt wurde.
5. Führen Sie im Graph-Tester den folgenden Befehl aus, um die Konfiguration der Bereitstellung für Ihre App zu initialisieren.
   Ersetzen Sie „[object-id]“ durch die Dienstprinzipal-ID (Objekt-ID), die Sie im dritten Schritt kopiert haben.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`

6. Wechseln Sie wieder zurück zum ersten Webbrowserfenster, und wählen Sie die Registerkarte **Bereitstellung** für Ihre Anwendung aus.
7. Schließen Sie die Konfiguration der Benutzerbereitstellung wie gewohnt ab.


## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie mehr über die Bereitstellung und Bereitstellungsaufhebung für SaaS-Anwendungen.](user-provisioning.md)
