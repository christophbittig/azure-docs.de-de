---
title: Was sind gekennzeichnete Anmeldungen in Azure Active Directory?
description: Dieser Artikel enthält eine allgemeine Übersicht über gekennzeichnete Anmeldungen für Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/12/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056ed3df8e9658b00917680aa2ad291f3529d1c8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484589"
---
# <a name="what-are-flagged-sign-ins-in-azure-active-directory"></a>Was sind gekennzeichnete Anmeldungen in Azure Active Directory?

Wenn sich ein Benutzer nicht anmelden konnte, sollten Sie als IT-Administrator das Problem schnellstmöglich beheben, um die Blockierung des Benutzers aufzuheben. Aufgrund der Menge an verfügbaren Daten im Anmeldeprotokoll ist die Suche nach den richtigen Informationen unter Umständen nicht ganz einfach.

Dieser Artikel enthält eine Übersicht über ein Feature, das die Suche nach den entsprechenden Problemen vereinfacht und somit dafür sorgt, dass sich Probleme bei der Benutzeranmeldung deutlich schneller lösen lassen.




## <a name="what-it-is"></a>Funktionsbeschreibung

Anhand von Azure AD-Anmeldeereignissen lässt sich nachvollziehen, was bei Benutzeranmeldungen und bei der Authentifizierungskonfiguration in einem Mandanten funktioniert hat und was nicht. Von Azure AD werden allerdings täglich mehr als acht Milliarden Authentifizierungen verarbeitet, sodass es für Administratoren aufgrund der hohen Anzahl von Anmeldeereignissen schwierig sein kann, die relevanten Ereignisse zu finden. Mit anderen Worten: Die schiere Anzahl von Anmeldeereignissen kann dazu führen, dass das Signal von Benutzern, die Unterstützung benötigen, in der Masse der Ereignisse untergeht.

Gekennzeichnete Anmeldungen dienen dazu, das Signal-Rausch-Verhältnis für Benutzeranmeldungen mit Unterstützungsbedarf zu erhöhen. Das Feature soll einerseits Benutzern die Möglichkeit geben, auf Anmeldefehler aufmerksam zu machen, bei denen sie Hilfe benötigen, und andererseits die Suche nach den richtigen Ereignissen für Administratoren und Helpdeskmitarbeiter schneller und effizienter machen. Neben den üblichen Informationen von Anmeldeereignissen enthalten gekennzeichnete Anmeldeereignisse zusätzlich die Angabe, dass ein Benutzer das Ereignis zur Überprüfung durch Administratoren gekennzeichnet hat.
 
Gekennzeichnete Anmeldungen geben dem Benutzer die Möglichkeit, die Kennzeichnung zu aktivieren, wenn auf einer Anmeldeseite ein Fehler auftritt, und diesen Fehler anschließend zu reproduzieren. Das Fehlerereignis wird dann auf dem Azure AD-Berichterstellungsblatt für Anmeldungen als „Zur Überprüfung gekennzeichnet“ angezeigt.

Gekennzeichnete Anmeldungen haben also folgende Vorteile:

- **Benutzer können Anmeldefehler melden**, bei denen sie Unterstützung von ihren Mandantenadministratoren benötigen.

- Die Suche nach den Anmeldefehlern, die für einen Benutzer behoben werden müssen, wird **vereinfacht**.

- Helpdeskmitarbeiter können **proaktiv** nach den Problemen suchen, bei denen Benutzer Unterstützung benötigen. Endbenutzer müssen lediglich das Ereignis kennzeichnen.

## <a name="how-it-works"></a>Funktionsweise

Gekennzeichnete Anmeldungen ermöglichen die Aktivierung der Kennzeichnung, wenn bei der Anmeldung über einen Browser ein Authentifizierungsfehler auftritt. Wenn einem Benutzer ein Anmeldefehler angezeigt wird, kann er die Kennzeichnung aktivieren. In den nächsten 20 Minuten wird für jedes Anmeldeereignis dieses Benutzers, bei dem der gleiche Browser und das gleiche Clientgerät bzw. der gleiche Computer verwendet wird, im Anmeldebericht „Zur Überprüfung gekennzeichnet: Ja“ angezeigt. Nach 20 Minuten wird die Kennzeichnung automatisch wieder deaktiviert.

### <a name="user-how-to-flag-an-error"></a>Benutzer: Kennzeichnen eines Fehlers

1. Der Benutzer erhält während der Anmeldung einen Fehler.
2. Der Benutzer klickt auf der Fehlerseite auf **Details anzeigen**.
3. Klicken Sie unter **Details zur Problembehandlung** auf **Kennzeichnung aktivieren**. Der Text ändert sich in **Kennzeichnung deaktivieren**. Die Kennzeichnung ist nun aktiviert.
4. Schließen Sie das Browserfenster.
5. Öffnen Sie ein neues Browserfenster (in der gleichen Browseranwendung), und führen Sie die nicht erfolgreiche Anmeldung erneut durch. 
6.  Reproduzieren Sie den zuvor aufgetretenen Anmeldefehler.

Nach der Aktivierung der Kennzeichnung müssen die gleiche Browseranwendung und der gleiche Client verwendet werden. Andernfalls werden die Ereignisse nicht gekennzeichnet.


### <a name="admin-find-flagged-events-in-reports"></a>Administrator: Suchen nach gekennzeichneten Ereignissen in Berichten

1.  Wählen Sie im linken Bereich des Azure AD-Portals die Option **Anmeldeprotokolle** aus.
2.  Klicken Sie auf **Filter hinzufügen**.
3.  Wählen Sie im Filtermenü mit dem Titel **Feld auswählen** die Option **Zur Überprüfung gekennzeichnet** aus, und klicken Sie auf **Anwenden**.
4.  Daraufhin werden alle Ereignisse angezeigt, die von Benutzern gekennzeichnet wurden.
5.  Wenden Sie bei Bedarf weitere Filter an, um die Ereignisansicht weiter einzugrenzen.
6.  Wählen Sie das Ereignis aus, um zu überprüfen, was passiert ist.


### <a name="admin-or-developer-find-flagged-events-using-ms-graph"></a>Administrator oder Entwickler: Suchen nach gekennzeichneten Ereignissen mithilfe von MS Graph

Sie können die Meldungs-API für Anmeldungen mit einer gefilterten Abfrage verwenden, um gekennzeichnete Anmeldungen zu finden.

Alle gekennzeichneten Anmeldungen anzeigen: `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true`

Abfragen gekennzeichneter Anmeldungen für bestimmte Benutzer nach UPN (z. B. user@contoso.com): `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true and userPrincipalname eq 'user@contoso.com'`

Abfragen gekennzeichneter Anmeldungen für bestimmte Benutzer und ein Datum größer als: `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true and createdDateTime ge 2021-10-01 and userPrincipalname eq 'user@contoso.com'`
 
Weitere Informationen zur Verwendung der Graph-API für Anmeldungen finden Sie unter [signIn-Ressourcentyp](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-1.0&preserve-view=true).



 
## <a name="who-can-create-flagged-sign-ins"></a>Wer kann gekennzeichnete Anmeldungen erstellen?

Jeder Benutzer, der sich per Webseite bei Azure AD anmeldet, kann Anmeldungen zur Überprüfung kennzeichnen. Mitglieds- und Gastbenutzer können Anmeldefehler zur Überprüfung kennzeichnen. 

## <a name="who-can-review-flagged-sign-ins"></a>Wer kann gekennzeichnete Anmeldungen überprüfen?

Für die Überprüfung gekennzeichneter Anmeldeereignisse sind Berechtigungen zum Lesen der Anmeldeberichtsereignisse im Azure AD-Portal erforderlich. Weitere Informationen finden Sie unter [Wer kann auf sie zugreifen?](concept-sign-ins.md#who-can-access-it).


Zum Kennzeichnen von Anmeldefehlern sind keine zusätzlichen Berechtigungen erforderlich.


## <a name="what-you-should-know"></a>Wichtige Informationen 

**Gekennzeichnete Anmeldungen** und **Risikoanmeldungen** heißen zwar ähnlich, sind aber unterschiedliche Funktionen:

- Gekennzeichnete Anmeldungen sind Anmeldefehlerereignisse, für die Benutzer um Unterstützung bitten. 
- Eine Risikoanmeldung ist eine Identity Protection-Funktion. Weitere Informationen finden Sie unter [Was ist Identity Protection?](../identity-protection/overview-identity-protection.md).




## <a name="next-steps"></a>Nächste Schritte

- [Anmeldeprotokolle in Azure Active Directory](concept-sign-ins.md)
- [Anmeldediagnose für Azure AD-Szenarien](concept-sign-in-diagnostics-scenarios.md)
