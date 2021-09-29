---
title: Anzeigen von Update- und Anmeldeaktivitäten für verwaltete Identitäten
description: Ausführliche Anweisungen zum Anzeigen der Aktivitäten für verwaltete Identitäten und den von verwalteten Identitäten durchgeführten Authentifizierungen
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03ab5ed10dcb9127c5b62850228d22222b4225be
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814426"
---
# <a name="view-update-and-sign-in-activities-for-managed-identities"></a>Anzeigen von Update- und Anmeldeaktivitäten für verwaltete Identitäten

In diesem Artikel wird erläutert, wie Sie Updates für verwaltete Identitäten und Anmeldeversuche von verwalteten Identitäten anzeigen.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter.
- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/) registrieren.

## <a name="view-updates-made-to-user-assigned-managed-identities"></a>Anzeigen von Updates an benutzerseitig zugewiesenen verwalteten Identitäten

Mit diesem Verfahren wird veranschaulicht, wie Sie Updates anzeigen, die an benutzerseitig zugewiesenen verwalteten Identitäten vorgenommen werden.

1. Navigieren Sie im Azure-Portal zum **Aktivitätsprotokoll**.

 ![Navigieren zum Aktivitätsprotokoll im Azure-Portal](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

2. Wählen Sie das Suchsteuerelement **Filter hinzufügen** und dann in der Liste **Vorgang** aus.

![Erstellen des Suchfilters](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

3. Geben Sie in der Dropdownliste **Vorgang** die folgenden Vorgangsnamen ein: „Benutzerseitig zugewiesene verwaltete Identität löschen“ und „UserAssignedIdentities schreiben“.

![Hinzufügen von Vorgängen zum Suchfilter](./media/how-to-view-managed-identity-activity/add-operations-to-search-filter.png)

4. Wenn übereinstimmende Vorgänge angezeigt werden, wählen Sie einen aus, um die Zusammenfassung anzuzeigen.

![Anzeigen einer Zusammenfassung des Vorgangs](./media/how-to-view-managed-identity-activity/view-summary-of-operation.png)

5. Wählen Sie die Registerkarte **JSON** aus, um ausführlichere Informationen zum Vorgang anzuzeigen, und scrollen Sie zum Knoten **Eigenschaften**, um Informationen zur geänderten Identität anzuzeigen.

![Anzeigen von Details zum Vorgang](./media/how-to-view-managed-identity-activity/view-json-of-operation.png)

## <a name="view-role-assignments-added-and-removed-for-managed-identities"></a>Anzeigen von Rollenzuweisungen, die für verwaltete Identitäten hinzugefügt und entfernt wurden

 > [!NOTE] 
 > Sie müssen nach der Objekt-ID (Prinzipal-ID) der verwalteten Identität suchen, für die Sie Änderungen an den Rollenzuweisungen anzeigen möchten.

1. Suchen Sie die verwaltete Identität, für die Sie Rollenzuweisungsänderungen anzeigen möchten. Wenn Sie nach einer systemseitig zugewiesenen verwalteten Identität suchen, wird die Objekt-ID auf dem Bildschirm **Identität** unter der Ressource angezeigt. Wenn Sie nach einer benutzerseitig zugewiesenen Identität suchen, wird die Objekt-ID auf der Seite **Übersicht** der verwalteten Identität angezeigt.

Benutzerseitig zugewiesene Identität:

![Abrufen der Objekt-ID einer benutzerseitig zugewiesenen Identität](./media/how-to-view-managed-identity-activity/get-object-id-of-user-assigned-identity.png)

Systemseitig zugewiesene Identität:

![Abrufen der Objekt-ID einer systemseitig zugewiesenen Identität](./media/how-to-view-managed-identity-activity/get-object-id-of-system-assigned-identity.png)

2. Kopieren Sie die Objekt-ID.
3. Navigieren Sie zum **Aktivitätsprotokoll**.

 ![Navigieren zum Aktivitätsprotokoll im Azure-Portal](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

4. Wählen Sie das Suchsteuerelement **Filter hinzufügen** und dann in der Liste **Vorgang** aus.

![Erstellen des Suchfilters](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

5. Geben Sie in der Dropdownliste **Vorgang** die folgenden Vorgangsnamen ein: „Rollenzuweisung erstellen“ und „Rollenzuweisung löschen“.

![Hinzufügen von Rollenzuweisungsvorgängen zum Suchfilter](./media/how-to-view-managed-identity-activity/add-role-assignment-operations-to-search-filter.png)

6. Fügen Sie die Objekt-ID in das Suchfeld ein. Die Ergebnisse werden automatisch gefiltert.

![Suchen nach Objekt-ID](./media/how-to-view-managed-identity-activity/search-by-object-id.png)
 
7. Wenn übereinstimmende Vorgänge angezeigt werden, wählen Sie einen aus, um die Zusammenfassung anzuzeigen.
 
![Zusammenfassung der Rollenzuweisung für eine verwaltete Identität](./media/how-to-view-managed-identity-activity/summary-of-role-assignment-for-msi.png)

## <a name="view-authentication-attempts-by-managed-identities"></a>Anzeigen von Authentifizierungsversuchen von verwalteten Identitäten

1. Navigieren Sie zu **Azure Active Directory**.

![Navigieren zu Active Directory](./media/how-to-view-managed-identity-activity/browse-to-active-directory.png)

2.  Wählen Sie im Abschnitt **Überwachung** die Option **Anmeldeprotokolle** aus.

![Auswählen von Anmeldeprotokollen](./media/how-to-view-managed-identity-activity/sign-in-logs-menu-item.png)

3. Wählen Sie die Registerkarte **Anmeldungen der verwalteten Identität** aus.

![Anmeldung der verwalteten Identität](./media/how-to-view-managed-identity-activity/msi-sign-ins.png)

4. Die Anmeldeereignisse werden jetzt nach verwalteten Identitäten gefiltert.

![Anmeldeereignisse nach verwalteter Identität](./media/how-to-view-managed-identity-activity/msi-sign-in-events.png) 

5.  Um die Unternehmensanwendung der Identität in Azure Active Directory anzuzeigen, wählen Sie die Spalte „ID der verwalteten Identität“ aus.
6.  Um die Azure-Ressource oder die benutzerseitig zugewiesene verwaltete Identität anzuzeigen, suchen Sie auf der Suchleiste im Azure-Portal nach dem Namen.

## <a name="next-steps"></a>Nächste Schritte

* [Verwaltete Identitäten für Azure-Ressourcen](./overview.md)
* [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/activity-log.md)
* [Azure Active Directory-Anmeldeprotokoll](../reports-monitoring/concept-sign-ins.md)