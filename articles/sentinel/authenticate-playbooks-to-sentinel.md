---
title: Authentifizieren von Playbooks bei Microsoft Sentinel | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihren Playbooks Zugriff auf Microsoft Sentinel und Autorisierung geben, um Abhilfemaßnahmen zu ergreifen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6cb45659ab0e31ed4d1259fc19c10b6f6bdfa015
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522173"
---
# <a name="authenticate-playbooks-to-microsoft-sentinel"></a>Authentifizieren von Playbooks bei Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

So wie Logic Apps funktioniert, muss es separat eine Verbindung herstellen und sich unabhängig bei jeder Ressource jedes Typs authentifizieren, mit der es interagiert, einschließlich Microsoft Sentinel selbst. Logic Apps verwendet zu diesem Zweck [spezielle Konnektoren](/connectors/connector-reference/), wobei jeder Ressourcentyp seinen eigenen Konnektor hat. In diesem Dokument werden die Arten von Verbindung und Authentifizierung im [Microsoft Sentinel-Connector für Logic Apps](/connectors/azuresentinel/) erläutert, die Playbooks zur Interaktion mit Microsoft Sentinel verwenden können, um Zugriff auf die Informationen in den Tabellen Ihres Arbeitsbereichs zu haben.

Dieses Dokument ist zusammen mit unserem Leitfaden zum [Verwenden von Triggern und Aktionen in Playbooks](playbook-triggers-actions.md) eine Ergänzung zu unserer anderen Playbookdokumentation: [Tutorial: Verwenden von Playbooks mit Automatisierungsregeln in Microsoft Sentinel](tutorial-respond-threats-playbook.md).

Eine Einführung in Playbooks finden Sie unter [Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel](automate-responses-with-playbooks.md).

Die vollständige Spezifikation des Microsoft Sentinel-Connectors finden Sie in der [Dokumentation zu Logic Apps-Connectors](/connectors/azuresentinel/).

## <a name="authentication"></a>Authentifizierung

Der Microsoft Sentinel-Connector in Logic Apps und seine Komponententrigger und -aktionen können für jede Identität verwendet werden, die über die erforderlichen Berechtigungen (Lesen und/oder Schreiben) für den relevanten Arbeitsbereich verfügt. Der Connector unterstützt mehrere Identitätstypen:

- [Verwaltete Identität (Vorschau)](#authenticate-with-managed-identity)
- [Azure AD-Benutzer](#authenticate-as-an-azure-ad-user)
- [Dienstprinzipal (Azure AD-Anwendungen)](#authenticate-as-a-service-principal-azure-ad-application)

    ![Authentifizierungsoptionen](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>Erforderliche Berechtigungen

| Rollen/Connectorkomponenten | Auslöser | „Get“-Aktionen | Incident aktualisieren,<br>Kommentar hinzufügen |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Microsoft Sentinel-Leser](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Microsoft Sentinel-[Antwortender](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)/[Mitwirkender](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Erfahren Sie mehr über Berechtigungen in Microsoft Sentinel](./roles.md).

### <a name="authenticate-with-managed-identity"></a>Authentifizieren mit einer verwalteten Identität

Mit dieser Authentifizierungsmethode können Sie dem Playbook (eine Logic Apps-Workflowressource) direkt Berechtigungen erteilen, sodass vom Playbook durchgeführte Aktionen des Microsoft Sentinel-Connectors so im Auftrag des Playbooks funktionieren, als wäre es ein unabhängiges Objekt mit eigenen Berechtigungen für Microsoft Sentinel. Die Verwendung dieser Methode senkt die Anzahl der Identitäten, die Sie verwalten müssen. 

> [!NOTE]
> Um einer verwalteten Identität Zugriff auf andere Ressourcen (z. B. Ihren Microsoft Sentinel-Arbeitsbereich) zu gewähren, muss Ihr angemeldeter Benutzer über eine Rolle mit Berechtigungen zum Schreiben von Rollenzuweisungen verfügen, z. B. „Besitzer“ oder „Benutzerzugriffsadministrator“ des Microsoft Sentinel-Arbeitsbereichs.

So führen Sie eine Authentifizierung mit verwalteter Identität durch:

1. [Aktivieren Sie die verwaltete Identität](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal) für die Logic Apps-Workflowressource. Zusammenfassung:

    - Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus. Wählen Sie **Systemseitig zugewiesen > Ein > Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

    - In der Logik-App kann nun die systemseitig zugewiesene Identität verwendet werden, die bei Azure AD registriert ist und durch eine Objekt-ID dargestellt wird.

1. [Gewähren Sie dieser Identität Zugriff](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources) auf den Microsoft Sentinel-Arbeitsbereich:
    1. Wählen Sie im Microsoft Sentinel-Menü **Einstellungen** aus.
    1. Wählen Sie die Registerkarte **Arbeitsbereichseinstellungen** aus. Wählen Sie im Menü des Arbeitsbereichs **Zugriffssteuerung (IAM)** aus.
    1. Wählen Sie in der Schaltflächenleiste im oberen Bereich **Hinzufügen** und anschließend **Rollenzuweisung hinzufügen** aus. Wenn die Option **Rollenzuweisung hinzufügen** deaktiviert ist, sind Sie nicht zum Zuweisen von Rollen berechtigt.
    1. Weisen Sie im angezeigten neuen Bereich die entsprechende Rolle zu:

        | Role | Situation |
        | --- | --- |
        | [**Microsoft Sentinel-Antwortender**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) | Playbook enthält Schritte zum Aktualisieren von Incidents oder Watchlists. |
        | [**Microsoft Sentinel-Leser**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) | Playbook empfängt nur Incidents |
        |

        Erfahren Sie mehr über die verfügbaren [Rollen in Microsoft Sentinel](./roles.md).
    1. Wählen Sie unter **Zugriff zuweisen zu** die Option **Logik-App** aus.
    1. Wählen Sie das Abonnement aus, zu dem das Playbook gehört, und wählen Sie den Namen des Playbooks aus.
    1. Wählen Sie **Speichern** aus.

1. Aktivieren Sie die Authentifizierungsmethode für verwaltete Identitäten im Logic Apps-Connector für Microsoft Sentinel:

    1. Fügen Sie im Logic Apps-Designer einen Schritt für den Logic Apps-Connector für Microsoft Sentinel hinzu. Wenn der Connector bereits für eine bestehende Verbindung aktiviert ist, klicken Sie auf den Link **Verbindung ändern**.

        ![Ändern der Verbindung](media/authenticate-playbooks-to-sentinel/change-connection.png)

    1. Wählen Sie in der daraufhin angezeigten Liste der Verbindungen am unteren Rand **Neu hinzufügen** aus. 

    1. Erstellen Sie eine neue Verbindung, indem Sie **Verbindung mit verwalteter Identität herstellen (Vorschau)** auswählen.

        ![Option „Verwaltete Identität“](media/authenticate-playbooks-to-sentinel/auth-methods-msi-choice.png)

    1. Geben Sie einen Namen für diese Verbindung ein, und wählen Sie **Systemseitig zugewiesene verwaltete Identität** und dann **Erstellen** aus.

        ![Herstellen einer Verbindung mit einer verwalteten Identität](media/authenticate-playbooks-to-sentinel/auth-methods-msi.png)

### <a name="authenticate-as-an-azure-ad-user"></a>Authentifizieren als Azure AD-Benutzer

Um eine Verbindung herzustellen, wählen Sie **Anmelden** aus. Sie werden aufgefordert, Ihre Kontoinformationen anzugeben. Befolgen Sie anschließend die restlichen Anweisungen auf dem Bildschirm, um eine Verbindung herzustellen.

### <a name="authenticate-as-a-service-principal-azure-ad-application"></a>Authentifizieren als Dienstprinzipal (Azure AD-Anwendung)

Dienstprinzipale können durch Registrieren einer Azure AD-Anwendung erstellt werden. **Vorzugsweise** sollten Sie eine registrierte Anwendung anstelle eines Benutzerkontos als Identität des Connectors verwenden, da Sie so besser Berechtigungen steuern, Anmeldeinformationen verwalten und bestimmte Einschränkungen bei der Verwendung des Connectors aktivieren können.

Um Ihre eigene Anwendung mit dem Microsoft Sentinel-Connector zu verwenden, führen Sie die folgenden Schritte aus:

1. Registrieren Sie die Anwendung mit Azure AD, und erstellen sie einen Dienstprinzipal. [Weitere Informationen](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).

1. Rufen Sie Anmeldeinformationen (für die zukünftige Authentifizierung) ab.

    Rufen Sie auf dem Blatt in der registrierten Anwendung die Anmeldeinformationen für die Anwendung ab:

    - **Client-ID**: unter **Übersicht**
    - **Geheimer Clientschlüssel**: unter **Zertifikate und Geheimnisse**.

1. Erteilen Sie Berechtigungen für den Microsoft Sentinel-Arbeitsbereich.

    In diesem Schritt erhält die App die Berechtigung zum Arbeiten mit dem Microsoft Sentinel-Arbeitsbereich.

    1. Navigieren Sie im Microsoft Sentinel-Arbeitsbereich zu **Einstellungen** -> **Arbeitsbereichseinstellungen** -> **Zugriffssteuerung (IAM)** .

    1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

    1. Wählen Sie die Rolle aus, die Sie der Anwendung zuweisen möchten. Um der Anwendung beispielsweise das Ausführen von Aktionen zu ermöglichen, die Änderungen wie das Aktualisieren eines Incidents im Sentinel-Arbeitsbereich vornehmen, wählen Sie die Rolle **Microsoft Sentinel-Mitwirkender** aus. Für Aktionen, die nur Daten lesen, ist die Rolle **Microsoft Sentinel-Leser** ausreichend. [Erfahren Sie mehr über die verfügbaren Rollen in Microsoft Sentinel](./roles.md).

    1. Suchen Sie die erforderliche Anwendung, und speichern Sie sie. Azure AD-Anwendungen werden standardmäßig nicht in den verfügbaren Optionen angezeigt. Suchen Sie nach dem Namen Ihrer Anwendung, und wählen Sie ihn aus.

1. Authenticate

    In diesem Schritt verwenden wir die App-Anmeldeinformationen für die Authentifizierung beim Sentinel-Connector in Logic Apps.

    - Klicken Sie auf **Verbindung über Dienstprinzipal herstellen**.

        ![Option „Dienstprinzipal“](media/authenticate-playbooks-to-sentinel/auth-methods-spn-choice.png)

    - Geben Sie die erforderlichen Parameter ein (die Sie auf dem Blatt für die registrierte Anwendung finden).
        - **Mandant**: unter **Übersicht**
        - **Client-ID**: unter **Übersicht**
        - **Geheimer Clientschlüssel**: unter **Zertifikate und Geheimnisse**
        
        ![Verbindung über Dienstprinzipal herstellen](media/authenticate-playbooks-to-sentinel/auth-methods-spn.png)

### <a name="manage-your-api-connections"></a>Verwalten Ihrer API-Verbindungen

Jedes Mal, wenn eine Authentifizierung zum ersten Mal erstellt wird, wird eine neue Azure-Ressource vom Typ API-Verbindung erstellt. Die gleiche API-Verbindung kann in allen Microsoft Sentinel-Aktionen und -Triggern in derselben Ressourcengruppe verwendet werden.

Alle API-Verbindungen sind im Blatt **API-Verbindungen** zu finden (suchen Sie im Azure-Portal nach *API-Verbindungen*).

Sie finden sie auch, indem Sie zum Blatt **Ressourcen** gehen und die Anzeige nach dem Typ *API-Verbindung* filtern. So können Sie mehrere Verbindungen für Massenvorgänge auswählen.

Um die Autorisierung einer vorhandenen Verbindung zu ändern, geben Sie die Verbindungs-Ressource ein, und wählen Sie **API-Verbindung bearbeiten** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die verschiedenen Methoden zum Authentifizieren eines Logic Apps-basierten Playbooks bei Microsoft Sentinel kennengelernt.

- Erfahren Sie mehr über die [Verwendung von Triggern und Aktionen in Playbooks](playbook-triggers-actions.md).
