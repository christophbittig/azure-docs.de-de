---
title: Verwalten des Zugriffs mit Zugriffsüberprüfungen - Azure AD
description: Erfahren Sie, wie Sie den Benutzer- und Gastzugang als Mitglied einer Gruppe oder als Zuweisung zu einer Anwendung mit Azure Active Directory-Zugangsüberprüfungen verwalten können.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cf6b15b68c864c3f98d3a4866931148cfcd6411
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095555"
---
# <a name="manage-user-and-guest-user-access-with-access-reviews"></a>Verwaltung von Benutzer- und Gastbenutzerzugängen mit Zugriffsüberprüfungen
 
Mit Azure Active Directory (Azure AD) können Sie ganz einfach sicherstellen, dass Benutzer oder Gäste angemessenen Zugriff haben. Hierzu können Sie die Benutzer selbst oder einen Entscheidungsträger bitten, an einer Zugriffsüberprüfung teilzunehmen und den Zugriff des Benutzers erneut zu zertifizieren (oder zu „attestieren“). Basierend auf Vorschlägen von Azure AD können die Prüfer die Notwendigkeit des weiteren Zugriffs der einzelnen Benutzer abwägen. Nach Abschluss einer Zugriffsüberprüfung können Sie dann Änderungen vornehmen und Zugriffsrechte für Benutzer entfernen, die diese nicht mehr benötigen.
 
> [!NOTE]
> Dieser Artikel behandelt die Durchführung von Zugriffsüberprüfungen für Benutzer und Anwendungen. Informationen zur Durchführung einer Zugriffsüberprüfung für mehrere Ressourcen in Zugriffspaketen finden Sie hier [Zugriff eines Zugriffspakets in der Azure AD-Berechtigungsverwaltung überprüfen](entitlement-management-access-reviews-review-access.md). Wenn Sie den Benutzer- oder Dienstprinzipalzugriff auf Azure AD oder Azure-Ressourcenrollen überprüfen möchten, siehe [Start einer Zugriffsüberprüfung in Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).
 
## <a name="prerequisites"></a>Voraussetzungen
 
- Azure AD Premium P2
 
Weitere Informationen finden Sie unter [Lizenzanforderungen](access-reviews-overview.md#license-requirements).
 
## <a name="create-and-perform-an-access-review-for-users"></a>Erstellen und Durchführen einer Zugriffsüberprüfung für Benutzer
Ihnen muss eine der folgenden Rollen zugewiesen sein:
- Globaler Administrator
- Benutzeradministrator
- Identity Governance-Administrator 
- Privilegierter Rollenadministrator (nur für Überprüfungen von rollenzuweisbaren Gruppen)
- (Vorschau) Microsoft 365 oder AAD Security Group Eigentümer der zu überprüfenden Gruppe 

Navigieren Sie zur [Seite „Identity Governance“](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), und vergewissern Sie sich, dass Ihre Organisation Zugriffsüberprüfungen verwenden kann.

Sie können einen oder mehrere Benutzer als Prüfer einer Zugriffsüberprüfung festlegen.  
 
1. Wählen Sie eine Gruppe in Azure AD mit mindestens einem Mitglied aus. Oder wählen Sie eine mit Azure AD verbundene Anwendung aus, der mindestens ein Benutzer zugewiesen ist. 
 
2. Legen Sie fest, ob jeder Benutzer seinen eigenen Zugriff oder ob ein oder mehrere Benutzer den Zugriff aller Benutzer überprüfen soll/sollen.
 
3. Gehen Sie in einer der oben aufgeführten Rollen auf die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
4. Erstellen Sie die Zugriffsüberprüfung. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).
 
5. Bitten Sie die Prüfer zu Beginn der Zugriffsüberprüfung, ihre Einschätzung abzugeben. Standardmäßig erhalten alle Prüfer eine E-Mail von Azure AD mit einem Link zu dem Zugriffsbereich, in dem sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](self-access-review.md).
 
6. Wenn die Prüfer keine Angaben gemacht haben, kann Azure AD ihnen eine Erinnerung senden. Standardmäßig sendet Azure AD auf halbem Weg zum Enddatum automatisch eine Erinnerung an alle Prüfer.
 
7. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).
 
## <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen
 
Mit Azure Active Directory (Azure AD) können Sie problemlos die Zusammenarbeit über Organisationsgrenzen hinweg ermöglichen, indem Sie das Feature [Azure AD B2B](../external-identities/what-is-b2b.md) verwenden. Gastbenutzer von anderen Mandanten können von [Administratoren](../external-identities/add-users-administrator.md) oder [anderen Benutzern eingeladen](../external-identities/what-is-b2b.md) werden. Dies gilt auch für soziale Identitäten wie z.B. Microsoft-Konten.
 
 
 
## <a name="create-and-perform-an-access-review-for-guests"></a>Erstellen und Durchführen einer Zugriffsüberprüfung für Gäste
 
Die gleichen Rollen, die für die Erstellung einer Zugangsüberprüfung für Benutzer erforderlich sind, sind auch für die Erstellung einer Zugangsüberprüfung für Gäste erforderlich. Weitere Informationen finden Sie unter [Anlegen und Durchführen einer Zugriffsüberprüfung für Benutzer](manage-access-review.md#create-and-perform-an-access-review-for-users).

Azure AD ermöglicht mehrere Szenarien für die Überprüfung von Gastbenutzern.
 
Sie können Folgendes überprüfen:
 
 - Eine Gruppe in Azure AD mit mindestens einem Gast als Mitglied
 - Eine mit Azure AD verbundene Anwendung, der mindestens ein Gastbenutzer zugewiesen ist 
 
Beim Überprüfen des Gastbenutzerzugriffs auf Microsoft 365-Gruppen können Sie entweder eine Überprüfung für jede Gruppe einzeln erstellen oder automatische, wiederkehrende Zugriffsüberprüfungen für Gastbenutzer in allen Microsoft 365-Gruppen aktivieren. Im folgenden Video erhalten Sie weitere Informationen zu wiederkehrenden Zugriffsüberprüfungen für Gastbenutzer: 
 
> [!VIDEO https://www.youtube.com/embed/3D2_YW2DwQ8]
 
Sie können dann entscheiden, ob Sie jeden Gast bitten, seinen eigenen Zugriff zu überprüfen, oder ob Sie einen oder mehrere Benutzer bitten, den Zugriff von jedem Gast zu überprüfen.
 
 Diese Szenarien werden in den folgenden Abschnitten behandelt.
 
### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Auffordern von Gästen zur Überprüfung ihrer eigenen Mitgliedschaft in einer Gruppe
 
Mithilfe von Zugriffsüberprüfungen können Sie sicherstellen, dass Benutzer, die eingeladen und einer Gruppe hinzugefügt wurden, weiterhin Zugriff benötigen. Sie können Gäste ganz einfach darum bitten, ihre eigene Mitgliedschaft in dieser Gruppe zu überprüfen.
 
1. Um eine Zugriffsüberprüfung für die Gruppe zu erstellen, geben Sie an, dass die Überprüfung nur Gastbenutzermitglieder umfasst und die Mitglieder sich selbst überprüfen. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).
 
2. Bitten Sie jeden Gast, seine eigene Mitgliedschaft zu überprüfen. Standardmäßig erhält jeder Gast, der eine Einladung angenommen hat, eine E-Mail von Azure AD mit einem Link zu einer Zugriffsüberprüfung. Azure AD stellt Anweisungen für Gastbenutzer bereit, mit denen sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](self-access-review.md) können.
 
3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).
 
4.  Zusätzlich zu den Benutzern, die angegeben haben, dass sie keinen weiteren Zugriff benötigen, können Sie auch Benutzer entfernen, die nicht reagiert haben.
 
5. Wenn die Gruppe nicht für die Zugriffsverwaltung verwendet wird, können Sie auch Benutzer entfernen, die nicht ausgewählt wurden, an der Überprüfung teilzunehmen, weil sie ihre Einladung nicht angenommen haben. Ein solches Versäumnis kann darauf hindeuten, dass die E-Mail-Adresse des eingeladenen Benutzers einen Tippfehler enthielt. Wenn eine Gruppe als Verteilerliste verwendet wird, wurden einige Gastbenutzer vermutlich nicht für eine Teilnahme ausgewählt, weil es sich bei ihnen um Kontaktobjekte handelt.
 
### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Auffordern eines Sponsors zum Überprüfen der Mitgliedschaft eines Gasts in einer Gruppe
 
Sie können einen Sponsor (z.B. den Besitzer einer Gruppe) bitten, die Notwendigkeit einer weitergehenden Mitgliedschaft eines Gasts in einer Gruppe zu überprüfen.
 
1. Um eine Zugriffsüberprüfung für die Gruppe zu erstellen, geben Sie an, dass die Überprüfung nur Gastbenutzermitglieder umfasst. Geben Sie dann mindestens einen Prüfer an. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).
 
2. Bitten Sie die Prüfer, ihre Einschätzung abzugeben. Standardmäßig erhalten alle Prüfer eine E-Mail von Azure AD mit einem Link zu dem Zugriffsbereich, in dem sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](self-access-review.md).
 
3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).

> [!NOTE]
>  Sie können externe Identitäten für die Anmeldung bei Ihrem Mandanten sperren und die externen Identitäten nach 30 Tagen aus Ihrem Mandanten löschen. Während dieses Zeitraums können Einstellungen, Ergebnisse, Prüfer oder Überwachungsprotokolle, die Teil der aktuellen Prüfung sind, nicht angezeigt oder konfiguriert werden. Weitere Informationen finden Sie unter [Deaktivieren und Löschen externer Identitäten mit Azure AD Access Reviews ](access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).

 
### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Auffordern von Gästen zum Überprüfen ihres eigenen Zugriffs auf eine Anwendung
 
Mithilfe von Zugriffsüberprüfungen können Sie sicherstellen, dass Benutzer, die für eine bestimmte Anwendung eingeladen wurden, weiterhin Zugriff benötigen. Sie können die Gäste ganz einfach bitten, ihren eigenen Zugriffsbedarf selbst zu überprüfen.
 
1. Um eine Zugriffsüberprüfung für die Anwendung zu erstellen, geben Sie an, dass die Überprüfung nur Gäste umfasst und die Benutzer ihren eigenen Zugriff überprüfen. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).
 
2. Bitten Sie Gäste, ihren eigenen Zugriff auf die Anwendung zu überprüfen. Standardmäßig erhält jeder Gast, der eine Einladung angenommen hat, eine E-Mail von Azure AD. Diese E-Mail enthält einen Link zu der Zugriffsüberprüfung im Zugriffsbereich Ihrer Organisation. Azure AD stellt Anweisungen für Gastbenutzer bereit, mit denen sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](self-access-review.md) können.
 
3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).
 
4. Zusätzlich zu den Benutzern, die ihren eigenen Bedarf an weiterem Zugang verneint haben, können Sie auch Gastbenutzer entfernen, die nicht geantwortet haben. Sie können auch Gastnutzer entfernen, die nicht zur Teilnahme ausgewählt wurden, insbesondere wenn sie nicht kürzlich eingeladen wurden. Diese Nutzer nahmen die Einladung nicht an und hatten daher keinen Zugang zu der Anwendung. 
 
### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Auffordern eines Sponsors zum Überprüfen des Zugriffs eines Gasts auf eine Anwendung
 
Sie können einen Sponsor – z.B. den Besitzer einer Anwendung – bitten, den Bedarf eines Gasts für den weiteren Zugriff auf die Anwendung zu überprüfen.
 
1. Um eine Zugriffsüberprüfung für die Anwendung zu erstellen, geben Sie an, dass die Überprüfung nur Gäste umfasst. Geben Sie dann mindestens einen Benutzer als Prüfer an. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).
 
2. Bitten Sie die Prüfer, ihre Einschätzung abzugeben. Standardmäßig erhalten alle Prüfer eine E-Mail von Azure AD mit einem Link zu dem Zugriffsbereich, in dem sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](self-access-review.md).
 
3. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).
 
### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Auffordern von Gästen zur Überprüfung ihres allgemeinen Zugriffsbedarfs
 
In einigen Organisationen sind Gästen ihre Gruppenmitgliedschaften möglicherweise nicht bekannt.
 
 
1. Erstellen Sie eine Sicherheitsgruppe mit den Gästen als Mitgliedern in Azure AD, wenn noch keine geeignete Gruppe vorhanden ist. Sie können z.B. eine Gruppe mit einer manuell verwalteten Mitgliedschaft von Gästen erstellen. Oder Sie können eine dynamische Gruppe mit einem Namen wie „Gäste von Contoso“ für Benutzer im Contoso-Mandanten erstellen, deren UserType-Attributwert „Guest“ lautet. Beachten Sie, dass ein Gastbenutzer, der Mitglied der Gruppe ist, die anderen Mitglieder der Gruppe sehen kann.
 
2. Um eine Zugriffsüberprüfung für diese Gruppe zu erstellen, geben Sie an, dass die Prüfer selbst Mitglieder sind. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).
 
3. Bitten Sie jeden Gast, seine eigene Mitgliedschaft zu überprüfen. Standardmäßig erhält jeder Gast, der eine Einladung angenommen hat, eine E-Mail von Azure AD mit einem Link zu der Zugriffsüberprüfung im Zugriffsbereich Ihrer Organisation. Azure AD stellt Anweisungen für Gastbenutzer bereit, mit denen sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](perform-access-review.md) können. 
 
4. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).
 
5. Entfernen Sie den Gastzugriff für Gäste, die abgelehnt wurden, die Überprüfung nicht abgeschlossen haben oder ihre Einladung zuvor nicht angenommen haben. Wenn einige der Gäste Kontakte sind, die für die Teilnahme an der Überprüfung ausgewählt wurden oder die eine Einladung zuvor nicht angenommen haben, können Sie deren Konten über das Azure-Portal oder mit PowerShell deaktivieren. Wenn ein Gast keinen Zugriff mehr benötigt und kein Kontakt ist, können Sie das zugehörige Benutzerobjekt aus dem Verzeichnis entfernen, indem Sie das Gastbenutzerobjekt über das Azure-Portal oder mit PowerShell löschen.

 
## <a name="next-steps"></a>Nächste Schritte
 
[Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md)


