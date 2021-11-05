---
title: Mehrbenutzerautorisierung mit Resource Guard
description: Eine Übersicht zur Mehrbenutzerautorisierung mithilfe von Resource Guard
ms.topic: how-to
ms.date: 10/20/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: b02758f35c12355e401bc94028364df60004bb3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094872"
---
# <a name="multi-user-authorization-using-resource-guard-preview"></a>Mehrbenutzerautorisierung mit Resource Guard (Vorschau)

Mit der Mehrbenutzerautorisierung (Multi-User Authorization, MUA) für Azure Backup können Sie eine zusätzliche Schutzebene für kritische Vorgänge in Ihren Recovery Services-Tresoren hinzufügen. Für MUA verwendet Azure Backup eine weitere Azure-Ressource namens Resource Guard, um sicherzustellen, dass kritische Vorgänge nur mit geeigneter Autorisierung ausgeführt werden.

Dieses Dokument behandelt die folgenden Themen:

- Funktionsweise von MUA mit Resource Guard
- Vorbereitung
- Testszenarien
- Erstellen einer Resource Guard-Instanz
- Aktivieren von MUA für einen Recovery Services-Tresor
- Schutz vor nicht autorisierten Vorgängen in einem Tresor
- Autorisieren kritischer Vorgänge in einem Tresor
- Deaktivieren von MUA für einen Recovery Services-Tresor

>[!NOTE]
>- Die Mehrbenutzerautorisierung für Backup befindet sich derzeit in der Vorschau und ist in allen öffentlichen Azure-Regionen verfügbar.
>- Wenn Sie die Vorschauversion vor dem 2. November 2021 verwenden, registrieren Sie Ihr Abonnement mithilfe der Previewfunktion **AzureBackupResourceGuard** im Azure-Portal unter dem Anbieter **Microsoft.RecoveryServices**, um zu beginnen.

## <a name="how-does-mua-for-backup-work"></a>Wie funktioniert MUA for Backup?

Azure Backup verwendet Resource Guard als Autorisierungsdienst für einen Recovery Services-Tresor. Zum erfolgreichen Ausführen eines kritischen Vorgangs (wie unten beschrieben), müssen Sie daher auch über ausreichende Berechtigungen für den zugeordneten Resource Guard verfügen.

> [!Important]
> Um wie vorgesehen zu funktionieren, muss sich die Resource Guard-Instanz im Besitz eines anderen Benutzers befinden, und der Tresoradministrator darf nicht über die Berechtigung „Mitwirkender“ verfügen. Sie können Resource Guard in einem anderen Abonnement oder Mandanten als dem platzieren, der den Recovery Services-Tresor enthält, um einen besseren Schutz zu bieten.

### <a name="critical-operations"></a>Kritische Vorgänge

In der folgenden Tabelle sind die Vorgänge aufgeführt, die als kritische Vorgänge definiert sind und durch eine Resource Guard-Instanz geschützt werden können. Sie können sich entscheiden, bestimmte Vorgänge vom Schutz durch die Resource Guard-Instanz auszuschließen, wenn sie ihr Tresore zuordnen. Beachten Sie, dass Vorgänge, die als obligatorisch gekennzeichnet sind, nicht vom Schutz durch Resource Guard für die zugeordneten Tresore ausgenommen werden können. Darüber hinaus betrifft der Ausschluss der kritischen Vorgänge alle Tresore, die einer Resource Guard-Instanz zugeordnet sind.

**Vorgang** | **Obligatorisch/Optional**
--- | ---
Deaktivieren des vorläufigen Löschens | Obligatorisch.
Deaktivieren des MUA-Schutzes | Obligatorisch.
Ändern der Sicherungsrichtlinie | Optional: Kann ausgeschlossen werden
Ändern des Schutzes | Optional: Kann ausgeschlossen werden
Schutz beenden | Optional: Kann ausgeschlossen werden
Ändern der MARS-Sicherheits-PIN | Optional: Kann ausgeschlossen werden

### <a name="concepts"></a>Konzepte
Die beteiligten Konzepte und Prozesse bei der Verwendung von MUA für Backup werden unten erläutert.

Sehen wir uns die beiden folgenden Benutzer an, um ein klares Verständnis des Prozesses und der Zuständigkeiten zu erhalten. Auf diese beiden Rollen wird in diesem gesamten Artikel verwiesen.

**Backupadministrator**: Besitzer des Recovery Services-Tresors, der Verwaltungsvorgänge für den Tresor ausführt. Zunächst darf der Backupadministrator über keinerlei Berechtigungen für die Resource Guard-Instanz verfügen.

**Sicherheitsadministrator**: Besitzer der Resource Guard-Instanz, der als Gatekeeper für kritische Vorgänge im Tresor fungiert. Daher steuert der Sicherheitsadministrator Berechtigungen, die der Backupadministrator benötigt, um kritische Vorgänge für den Tresor auszuführen.

Im Folgenden finden Sie eine Diagrammdarstellung zum Ausführen eines kritischen Vorgangs für einen Tresor, für den MUA mithilfe einer Resource Guard-Instanz konfiguriert wurde.

:::image type="content" source="./media/multi-user-authorization/configure-mua-using-resource-card-diagram.png" alt-text="Diagrammdarstellung zum Konfigurieren von MUA mithilfe einer Resource Guard-Instanz.":::
 

Dies ist der Ereignisablauf in einem typischen Szenario:

1. Der Backupadministrator erstellt den Recovery Services-Tresor.
1. Der Sicherheitsadministrator erstellt die Resource Guard-Instanz. Die Resource Guard-Instanz kann in einem anderen Abonnement oder einem anderen Mandanten als der Recovery Services-Tresor enthalten sein. Es muss sichergestellt werden, dass der Backupadministrator nicht über Mitwirkendenberechtigungen für Resource Guard verfügt.
1. Der Sicherheitsadministrator erteilt dem Backupadministrator die Rolle **Leser** für die Resource Guard-Instanz (oder einen relevanten Bereich). Der Backupadministrator benötigt die Leserrolle, um MUA für den Tresor zu aktivieren.
1. Der Backupadministrator konfiguriert nun den Recovery Services-Tresor so, dass er über die Resource Guard-Instanz durch MUA geschützt wird.
1. Wenn der Backupadministrator jetzt einen kritischen Vorgang für den Tresor ausführen möchte, muss er Zugriff auf die Resource Guard-Instanz anfordern. Der Backupadministrator kann sich an den Sicherheitsadministrator wenden, um Zugangsdetails zum Ausführen solcher Vorgänge zu erhalten. Sie können dies mithilfe von PIM (Privileged Identity Management) oder anderer Prozesse tun, nach Maßgabe der Organisation.
1. Der Sicherheitsadministrator erteilt dem Backupadministrator vorübergehend die Rolle **Mitwirkender** für die Resource Guard-Instanz, um kritische Vorgänge auszuführen.
1. Der Backupadministrator leitet nun den kritischen Vorgang ein.
1. Azure Resource Manager überprüft, ob der Backupadministrator über ausreichende Berechtigungen verfügt. Da der Backupadministrator jetzt über die Rolle „Mitwirkender“ für die Resource Guard-Instanz verfügt, wird die Anforderung abgeschlossen.
   - Wenn der Backupadministrator nicht über die erforderlichen Berechtigungen/Rollen verfügt hätte, wäre die Anforderung fehlgeschlagen.
1. Der Sicherheitsadministrator vergewissert sich, dass die Berechtigungen zum Ausführen kritischer Vorgänge nach dem Ausführen der autorisierten Aktionen oder nach einer definierten Dauer widerrufen werden. Die Verwendung der JIT-Tools von [Azure Active Directory Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) kann nützlich sein, um dies sicherzustellen.

>[!NOTE]
>- MUA bietet Schutz für die oben aufgeführten Vorgänge, die nur für die Recovery Services-Tresore ausgeführt werden. Alle Vorgänge, die direkt für die Datenquelle (d. h. die geschützte Azure-Ressource/Workload) ausgeführt werden, liegen außerhalb des Bereichs der Resource Guard-Instanz. 
>- Dieses Feature ist derzeit nur über das Azure-Portal verfügbar.
>- Dieses Feature wird aktuell nur für Recovery Services-Tresore unterstützt und ist für Sicherungstresore nicht verfügbar.

## <a name="before-you-start"></a>Vorbereitung

-  Die Resource Guard-Instanz und der Recovery Services-Tresor müssen sich in derselben Azure-Region befinden.
-  Wie im vorherigen Abschnitt ausgeführt, muss sichergestellt werden, dass der Backupadministrator **nicht** über die Berechtigung **Mitwirkender** für Resource Guard verfügt. Sie können sich entscheiden, die Resource Guard-Instanz in einem anderen Abonnement desselben Verzeichnisses oder in einem anderen Verzeichnis auszuführen, um maximale Isolation sicherzustellen.

## <a name="usage-scenarios"></a>Verwendungsszenarios

Die folgende Tabelle beschreibt Szenarien zum Erstellen Ihres Resource Guard- und Recovery Services-Tresors (RS-Tresors) sowie den jeweiligen relativen Schutz.

>[!Important]
> Der Backupadministrator darf in keinem der Szenarien über die Berechtigung „Mitwirkender“ für Resource Guard verfügen.

**Verwendungsszenario** | **Schutz durch MUA** | **Einfache Implementierung** | **Hinweise**
--- | --- |--- |--- |
Der RS-Tresor und Resource Guard befinden sich **im selben Abonnement.** </br> Der Backupadministrator hat keinen Zugriff auf die Resource Guard-Instanz. | Geringste Isolation zwischen dem Backupadministrator und dem Sicherheitsadministrator. | Relativ einfach zu implementieren, da nur ein Abonnement erforderlich ist. | Berechtigungen/Rollen auf Ressourcenebene müssen unbedingt ordnungsgemäß zugewiesen werden.
Der RS-Tresor und Resource Guard befinden sich **in verschiedenen Abonnements, aber im selben Mandanten.** </br> Der Backupadministrator hat keinen Zugriff auf die Resource Guard-Instanz oder das entsprechende Abonnement. | Mittlere Isolation zwischen dem Backupadministrator und dem Sicherheitsadministrator. | Relativ mittlere Einfachheit der Implementierung, da zwei Abonnements (aber nur ein einzelner Mandant) erforderlich sind. | Stellen Sie sicher, dass die Berechtigungen/Rollen für die Ressource oder das Abonnement ordnungsgemäß zugewiesen sind.
Der RS-Tresor und Resource Guard befinden sich **in verschiedenen Mandanten.** </br> Der Backupadministrator hat keinen Zugriff auf die Resource Guard-Instanz, das entsprechende Abonnement oder den entsprechenden Mandanten.| Maximale Isolation zwischen dem Backupadministrator und dem Sicherheitsadministrator, daher maximale Sicherheit. | Relativ schwierig zu testen, da zum Testen zwei Mandanten oder Verzeichnisse erforderlich sind. | Stellen Sie sicher, dass die Berechtigungen/Rollen für die Ressource, das Abonnement oder das Verzeichnis ordnungsgemäß zugewiesen sind.

 >[!NOTE]
 > In diesem Artikel wird die Erstellung der Resource Guard-Instanz in einem anderen Mandanten veranschaulicht, was maximalen Schutz bietet. Im Hinblick auf das Anfordern und das Genehmigen von Anforderungen für die Ausführung kritischer Vorgänge veranschaulicht dieser Artikel das gleiche unter Verwendung von [Azure Active Directory Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) in dem Mandanten, der die Resource Guard-Instanz enthält. Sie können optional andere Mechanismen verwenden, um JIT-Berechtigungen für die Resource Guard-Instanz zu verwalten, gemäß Ihrem Setup.

## <a name="creating-a-resource-guard"></a>Erstellen einer Resource Guard-Instanz

Der **Sicherheitsadministrator** erstellt die Resource Guard-Instanz. Es wird empfohlen, sie in einem **anderen Abonnement** oder einem **anderen Mandanten** als den Tresor zu erstellen. Sie sollte sich jedoch in der **gleichen Region** wie der Tresor befinden. Der Backupadministrator darf **NICHT** über Zugriff als *Mitwirkender* auf die Resource Guard-Instanz oder das Abonnement verfügen, das sie enthält.

Erstellen Sie für das folgende Beispiel die Resource Guard-Instanz in einem anderen Mandanten als dem Mandanten des Tresors.
1. Navigieren Sie im Azure-Portal zu dem Verzeichnis, in dem Sie die Resource -Instanz erstellen möchten.
   
   :::image type="content" source="./media/multi-user-authorization/portal-settings-directories-subscriptions.png" alt-text="Screenshot: Portaleinstellungen":::
     
1. Suchen Sie in der Suchleiste nach **Resource Guards**, und wählen Sie in der Dropdownliste den entsprechenden Eintrag aus.
    
   :::image type="content" source="./media/multi-user-authorization/resource-guards-preview-inline.png" alt-text="Screenshot: Resource Guards in der Vorschau" lightbox="./media/multi-user-authorization/resource-guards-preview-expanded.png":::
    
   - Klicken Sie auf **Erstellen**, um mit der Erstellung einer Resource Guard-Instanz zu beginnen.
   - Geben Sie auf dem Blatt „Erstellen“ die erforderlichen Details für diese Resource Guard-Instanz ein.
       - Vergewissern Sie sich, dass sich die Resource Guard-Instanz in der gleichen Azure-Region wie der Recovery Services-Tresor befindet.
       - Außerdem ist es nützlich, eine Beschreibung hinzuzufügen, wie Sie bei Bedarf Zugriff zum Ausführen von Aktionen für zugeordnete Tresore erhalten oder anfordern. Diese Beschreibung wird auch in den zugeordneten Tresoren angezeigt, um den Backupadministrator beim Abrufen der erforderlichen Berechtigungen zu leiten. Sie können die Beschreibung bei Bedarf später bearbeiten, es ist jedoch empfehlenswert, jederzeit eine klar definierte Beschreibung zu haben.
       
        :::image type="content" source="./media/multi-user-authorization/create-resource-guard.png" alt-text="Screenshot: Erstellen von Resource Guard":::
                
1. Fügen Sie optional alle Tags zu Resource Guard hinzu, die den Anforderungen entsprechen.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Folgen Sie den Benachrichtigungen zum Status und zur erfolgreichen Erstellung der Resource Guard-Instanz.

### <a name="select-operations-to-protect-using-resource-guard"></a>Auswählen von Vorgängen zum Schützen durch Resource Guard

Wählen Sie unter allen unterstützten kritischen Vorgängen diejenigen aus, die Sie mit der Resource Guard-Instanz schützen möchten. Standardmäßig sind alle unterstützten kritischen Vorgänge aktiviert. Jedoch können Sie bestimmte Vorgänge von der Kontrolle durch MUA mithilfe von Resource Guard ausschließen. Der Sicherheitsadministrator kann die folgenden Schritte ausführen:

1. Navigieren Sie in der oben erstellten Resource Guard-Instanz zu **Eigenschaften**.
2. Wählen Sie **Deaktivieren** für Vorgänge aus, die Sie von der Autorisierung mithilfe der Resource Guard-Instanz ausnehmen möchten. Beachten Sie, dass die Vorgänge **Soft Delete deaktivieren** und **MUA-Schutz entfernen** nicht deaktiviert werden können.
3. Optional können Sie auf diesem Blatt auch die Beschreibung für die Resource Guard-Instanz aktualisieren. 
4. Klicken Sie auf **Speichern**.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-properties.png" alt-text="Screenshot: Eigenschaften der Resource Guard-Demo":::

## <a name="assign-permissions-to-the-backup-admin-on-the-resource-guard-to-enable-mua"></a>Zuweisen von Berechtigungen für den Backupadministrator in der Resource Guard-Instanz zum Aktivieren von MUA

Zum Aktivieren von MUA für einen Tresor muss der Administrator des Tresors über die Rolle **Leser** in der Resource Guard-Instanz oder dem Abonnement verfügen, das die Resource Guard-Instanz enthält. So weisen Sie die Rolle **Leser** in der Resource Guard-Instanz zu:

1. Navigieren Sie in der oben erstellten Resource Guard-Instanz zum Blatt „Zugriffssteuerung“ (IAM) und dann zu **Rollenzuweisung hinzufügen**.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-access-control.png" alt-text="Screenshot: Zugriffssteuerung in der Resource Guard-Demo":::
    
1. Wählen Sie in der Liste der integrierten Rollen **Leser** aus, und klicken Sie unten auf dem Bildschirm auf **Weiter**.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-inline.png" alt-text="Screenshot: Resource Guard-Demo: Rollenzuweisung hinzufügen" lightbox="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-expanded.png":::

1. Klicken Sie auf **Mitglieder auswählen**, und fügen Sie die E-Mail-ID des Backupadministrators ein, um ihn als den **Leser** hinzuzufügen. Da sich der Backupadministrator in diesem Fall in einem anderen Mandanten befindet, wird er dem Mandanten, der die Resource Guard-Instanz enthält, als Gast hinzugefügt.

1. Klicken Sie auf **Auswählen**, und fahren Sie dann mit **Überprüfen + zuweisen** fort, um die Rollenzuweisung abzuschließen.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-select-members-inline.png" alt-text="Screenshot: Auswählen von Mitgliedern in der Resource Guard-Demo" lightbox="./media/multi-user-authorization/demo-resource-guard-select-members-expanded.png":::

## <a name="enable-mua-on-a-recovery-services-vault"></a>Aktivieren von MUA für einen Recovery Services-Tresor

Da der Backupadministrator nun über die Rolle „Leser“ für die Resource Guard-Instanz verfügt, kann er problemlos die Autorisierung für mehrere Benutzer für von ihm verwaltete Tresore aktivieren. Die folgenden Schritte werden vom **Backupadministrator** ausgeführt:

1. Navigieren Sie zum Recovery Services-Tresor. Navigieren Sie im linken Navigationsbereich zu **Eigenschaften**, navigieren Sie zu **Mehrbenutzerautorisierung**, und klicken Sie dann auf **Aktualisieren**.

1. Jetzt wird Ihnen die Option zum Aktivieren von MUA und zum Auswählen einer Resource Guard-Instanz mit einem der folgenden Verfahren vorgestellt:

   1. Sie können einerseits den URI der Resource Guard-Instanz angeben. Achten Sie darauf, dass Sie den URI einer Resource Guard-Instanz angeben, auf die Sie Zugriff als **Leser** haben, und dass sie sich in der gleichen Region wie der Tresor befindet. Sie finden den URI (Resource Guard-ID) der Resource Guard-Instanz auf dem **Übersichtsbildschirm**:

      :::image type="content" source="./media/multi-user-authorization/resource-guard-rg-inline.png" alt-text="Screenshot der Resource Guard-Instanz." lightbox="./media/multi-user-authorization/resource-guard-rg-expanded.png":::
    
   1. Alternativ können Sie die Resource Guard-Instanz in der Liste der Resource Guard-Instanzen auswählen, auf die Sie Zugriff als **Leser** haben und die in der Region verfügbar sind.

      1. Klicken Sie auf **Ressource Guard auswählen**
      1. Klicken Sie auf die Dropdownliste, und wählen Sie das Verzeichnis aus, in dem sich die Resource Guard-Instanz befindet.
      1. Klicken Sie auf **Authentifizieren**, um Ihre Identität und den Zugriff zu überprüfen.
      1. Wählen Sie nach der Authentifizierung die **Resource Guard**-Instanz in der angezeigten Liste aus.

      :::image type="content" source="./media/multi-user-authorization/testvault1-multi-user-authorization-inline.png" alt-text="Screenshot: Mehrbenutzerautorisierung" lightbox="./media/multi-user-authorization/testvault1-multi-user-authorization-expanded.png" :::

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind, um MUA zu aktivieren.

   :::image type="content" source="./media/multi-user-authorization/testvault1-enable-mua.png" alt-text="Screenshot: Aktivieren der Mehrbenutzerauthentifizierung":::

## <a name="protect-against-unauthorized-protected-operations"></a>Schutz vor nicht autorisierten (geschützten) Vorgängen

Nachdem Sie MUA aktiviert haben, werden die Vorgänge im Bereich für den Tresor eingeschränkt, wenn der Backupadministrator ihre Ausführung versucht, ohne im Besitz der erforderlichen Rolle (d. h. der Rolle „Mitwirkender“) für die Resource Guard-Instanz zu sein.

 >[!NOTE]
 >Es wird dringend empfohlen, das Setup nach der Aktivierung von MUA zu testen, um sicherzustellen, dass geschützte Vorgänge wie erwartet blockiert werden und MUA ordnungsgemäß konfiguriert ist.

Unten ist eine Abbildung dargestellt, die zeigt, was geschieht, wenn der Backupadministrator versucht, einen solchen geschützten Vorgang auszuführen (hier ist z. B. das Deaktivieren von vorläufigem Löschen dargestellt. Das Verhalten für andere geschützte Vorgänge ist ähnlich). Die folgenden Schritte werden von einem Backupadministrator ohne erforderliche Berechtigungen ausgeführt.

1. Navigieren Sie zum Deaktivieren des vorläufigen Löschens zum Recovery Services-Tresor > Eigenschaften > Sicherheitseinstellungen, und klicken Sie auf **Aktualisieren**, wodurch die Sicherheitseinstellungen angezeigt werden.
1. Deaktivieren Sie das vorläufige Löschen mithilfe des Schiebereglers. Sie werden darüber informiert, dass es sich um einen geschützten Vorgang handelt und Sie dessen Zugriff auf die Resource Guard-Instanz bestätigen müssen.
1. Wählen Sie das Verzeichnis aus, das die Resource Guard-Instanz enthält, und authentifizieren Sie sich. Dieser Schritt ist möglicherweise nicht erforderlich, wenn sich die Resource Guard-Instanz im gleichen Verzeichnis wie der Tresor befindet.
1. Klicken Sie anschließend auf **Speichern**. Bei der Anforderung tritt ein Fehler auf, der Sie darüber informiert, dass Sie nicht über die erforderlichen Berechtigungen auf der Resource Guard-Instanz verfügen, um diesen Vorgang auszuführen.

## <a name="authorize-critical-protected-operations-using-azure-ad-privileged-identity-management"></a>Autorisieren kritischer (geschützter) Vorgänge mit Azure AD Privileged Identity Management

In den folgenden Unterabschnitten wird das Autorisieren dieser Anforderungen mit PIM erläutert. Es gibt Fälle, in denen Sie möglicherweise kritische Vorgänge für Ihre Sicherungen ausführen müssen, und MUA kann Ihnen helfen, sicherzustellen, dass diese nur ausgeführt werden, wenn die richtigen Genehmigungen oder Berechtigungen vorhanden sind. Wie bereits erwähnt, muss der Backupadministrator über die Rolle „Mitwirkender“ für Resource Guard verfügen, um kritische Vorgänge im von Resource Guard abgedeckten Bereich ausführen zu können. Eine der Möglichkeiten, für solche Vorgänge eine Just-In-Time-Ausführung zu ermöglichen, besteht in der Verwendung von [Azure Active Directory (Azure AD) Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure).

>[!NOTE]
> Die Verwendung von Azure AD PIM stellt zwar den empfohlenen Ansatz dar, jedoch können Sie auch manuelle oder benutzerdefinierte Methoden für die Verwaltung des Zugriffs des Backupadministrators auf die Resource Guard-Instanz verwenden. Verwenden Sie zum manuellen Verwalten des Zugriffs auf Resource Guard die Einstellung „Zugriffssteuerung (IAM)“ in der linken Navigationsleiste von Resource Guard, und erteilen Sie dem Backupadministrator die Rolle **Mitwirkender**.

### <a name="create-an-eligible-assignment-for-the-backup-admin-if-using-azure-ad-privileged-identity-management"></a>Erstellen einer berechtigten Zuweisung für den Backupadministrator (bei Verwendung von Azure AD Privileged Identity Management)

Mithilfe von PIM kann der Sicherheitsadministrator für Resource Guard eine berechtigte Zuweisung für den Backupadministrator als Mitwirkender erstellen. Dadurch kann der Backupadministrator eine Anforderung (für die Rolle „Mitwirkender“) erstellen, wenn er einen geschützten Vorgang ausführen muss. Dazu geht der **Sicherheitsadministrator** wie folgt vor:

1. Navigieren Sie im Sicherheits-Mandanten (der die Resource Guard-Instanz enthält) zu **Privileged Identity Management** (suchen Sie in der Suchleiste im Azure-Portal danach), und navigieren Sie dann zu **Azure-Ressourcen** (im linken Menü unter **Verwalten**).
1. Wählen Sie die Ressource (die Resource Guard-Instanz oder das enthaltende Abonnement bzw. die Ressourcengruppe) aus, der Sie die Rolle **Mitwirkender** zuweisen möchten.

   1. Wenn die entsprechende Ressource nicht in der Liste der Ressourcen angezeigt wird, achten Sie darauf, das enthaltende Abonnement hinzuzufügen, damit es von PIM verwaltet wird.

1. Navigieren Sie in der ausgewählten Ressource zu **Zuweisungen** (im linken Menü unter **Verwalten**), und gehen Sie zu **Zuweisungen hinzufügen**.

    :::image type="content" source="./media/multi-user-authorization/add-assignments.png" alt-text="Screenshot zum Hinzufügen von Zuweisungen":::

1. Unter „Zuweisungen hinzufügen“
   1. Wählen Sie die Rolle als Mitwirkender aus.
   1. Navigieren Sie zu „Mitglieder auswählen“, und fügen Sie den Benutzernamen (oder die E-Mail-ID) des Backupadministrators hinzu
   1. Klicken Sie auf „Weiter“.

   :::image type="content" source="./media/multi-user-authorization/add-assignments-membership.png" alt-text="Screenshot zum Hinzufügen der Zuweisungsmitgliedschaft":::

1. Auf dem nächsten Bildschirm
   1. Wählen Sie unter „Zuweisungstyp“ die Option **Berechtigt** aus.
   1. Geben Sie die Gültigkeitsdauer für diese Berechtigung an.
   1. Klicken Sie auf **Zuweisen**, um die Erstellung der berechtigten Zuweisung abzuschließen.

   :::image type="content" source="./media/multi-user-authorization/add-assignments-setting.png" alt-text="Screenshot der Einstellung „Zuweisungen hinzufügen“":::

### <a name="set-up-approvers-for-activating-contributor-role"></a>Festlegen von genehmigenden Personen für das Aktivieren der Rolle „Mitwirkender“

Standardmäßig ist für das Setup oben in PIM möglicherweise keine genehmigende Person (und kein Ablauf der Genehmigungsanforderung) konfiguriert. Damit sichergestellt ist, dass genehmigende Personen erforderlich sind, damit nur autorisierte Anforderungen weitergeleitet werden, muss der Sicherheitsadministrator die folgenden Schritte ausführen.
Beachten Sie, dass ohne diese Konfiguration alle Anforderungen automatisch genehmigt werden, ohne die Überprüfung durch Sicherheitsadministratoren oder eine festgelegte genehmigende Stelle zu durchlaufen. Weitere Informationen dazu finden Sie [hier](../active-directory/privileged-identity-management/pim-resource-roles-configure-role-settings.md).

1. Wählen Sie in Azure AD PIM in der linken Navigationsleiste **Azure-Ressourcen** und dann Ihre Resource Guard-Instanz aus.

1. Wechseln Sie zu **Einstellungen**, und navigieren Sie dann zur Rolle **Mitwirkender**.

   :::image type="content" source="./media/multi-user-authorization/add-contributor.png" alt-text="Screenshot: Hinzufügen eines Mitwirkenden":::

1. Wenn für die Einstellung **Genehmigende Personen** keine oder die falschen genehmigenden Personen angezeigt werden, klicken Sie auf **Bearbeiten**, um die genehmigenden Personen hinzuzufügen, die Aktivierungsanforderungen für die Rolle „Mitwirkender“ überprüfen und genehmigen müssen.

1. Wählen Sie auf der Registerkarte **Aktivierung** die Option **Erzwingen der Genehmigung für die Aktivierung** aus, und fügen Sie die genehmigenden Personen hinzu, die die einzelnen Anforderungen genehmigen müssen. Sie können auch weitere Sicherheitsoptionen auswählen, z. B. die Verwendung von MFA und das Festlegen von Tickets, um die Rolle „Mitwirkender“ zu aktivieren. Wählen Sie optional entsprechend Ihren Anforderungen relevante Einstellungen auf den Registerkarten **Zuweisung** und **Benachrichtigung** aus.

   :::image type="content" source="./media/multi-user-authorization/edit-role-settings.png" alt-text="Screenshot: Bearbeiten der Rolleneinstellung":::

1. Klicken Sie auf **Aktualisieren**, wenn Sie fertig sind.

### <a name="request-activation-of-an-eligible-assignment-to-perform-critical-operations"></a>Anfordern der Aktivierung einer berechtigten Zuweisung zum Ausführen kritischer Vorgänge

Nachdem der Sicherheitsadministrator eine berechtigte Zuweisung erstellt hat, muss der Backupadministrator die Zuweisung der Rolle „Mitwirkender“ aktivieren, damit geschützte Aktionen ausgeführt werden können. Die folgenden Aktionen werden vom **Backupadministrator** ausgeführt, um die Rollenzuweisung zu aktivieren.

1. Melden Sie sich bei [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) an. Wenn sich die Resource Guard-Instanz in einem anderen Verzeichnis befindet, wechseln Sie zu diesem Verzeichnis, und navigieren Sie dann zu [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure).
1. Navigieren Sie im linken Menü zu „Meine Rollen“ > „Azure-Ressourcen“.
1. Dem Backupadministrator wird eine berechtigte Zuweisung für die Rolle „Mitwirkender“ angezeigt. Klicken Sie auf **Aktivieren**, um sie zu aktivieren.
1. Der Backupadministrator wird per Portalbenachrichtigung darüber informiert, dass die Anforderung zur Genehmigung gesendet wird.

   :::image type="content" source="./media/multi-user-authorization/identity-management-myroles-inline.png" alt-text="Screenshot: Aktivieren berechtigter Zuweisungen" lightbox="./media/multi-user-authorization/identity-management-myroles-expanded.png":::

### <a name="approve-activation-of-requests-to-perform-critical-operations"></a>Genehmigen der Aktivierung von Anforderungen zum Ausführen kritischer Vorgänge

Nachdem der Backupadministrator eine Anforderung zum Aktivieren der Mitwirkendenrolle ausgelöst hat, muss die Anforderung vom **Sicherheitsadministrator** geprüft und genehmigt werden.
1. Navigieren Sie im Sicherheitsmandanten zu [Azure AD Privileged Identity Management.](/azure/active-directory/privileged-identity-management/pim-configure)
1. Navigieren Sie zu **Anforderungen genehmigen**.
1. Unter **Azure-Ressourcen** wird die Anforderung angezeigt, die vom Backupadministrator ausgelöst wurde und die Aktivierung als **Mitwirkender** anfordert.
1. Überprüfen Sie die Anforderung. Wenn Sie echt ist, wählen Sie die Anforderung aus, und klicken Sie auf **Genehmigen**, um sie zu genehmigen.
1. Der Backupadministrator wird per E-Mail (oder über andere Benachrichtigungsmechanismen in der Organisation) darüber informiert, dass seine Anforderung jetzt genehmigt wurde.
1. Nach der Genehmigung kann der Backupadministrator für den angeforderten Zeitraum geschützte Vorgänge ausführen.

## <a name="performing-a-protected-operation-after-approval"></a>Ausführen eines geschützten Vorgangs nach der Genehmigung

Nachdem die Anforderung der Rolle „Mitwirkender“ des Backupadministrators bei der Resource Guard-Instanz genehmigt wurde, kann er geschützte Vorgänge für den zugeordneten Tresor durchführen. Wenn sich die Resource Guard-Instanz in einem anderen Verzeichnis befindet, muss sich der Backupadministrator selbst authentifizieren.

>[!NOTE]
> Wenn der Zugriff mithilfe eines JIT-Mechanismus zugewiesen wurde, wird die Rolle „Mitwirkender“ am Ende des genehmigten Zeitraums wieder entzogen. Andernfalls entfernt der Sicherheitsadministrator die dem Backup-Administrator zum Ausführen des kritischen Vorgangs zugewiesene Rolle **Mitwirkender** manuell.

## <a name="disable-mua-on-a-recovery-services-vault"></a>Deaktivieren von MUA für einen Recovery Services-Tresor

Das Deaktivieren von MUA ist ein geschützter Vorgang und wird daher mit MUA geschützt. Dies bedeutet, dass der Backupadministrator über die erforderliche Rolle „Mitwirkender“ in Resource Guard verfügen muss. Details zum Erhalt dieser Rolle werden hier beschrieben. Im Anschluss finden Sie eine Zusammenfassung der Schritte zum Deaktivieren von MUA für einen Tresor.
1. Der Backupadministrator fordert beim Sicherheitsadministrator die Rolle **Mitwirkender** für die Resource Guard-Instanz an. Er kann dies mithilfe der von der Organisation genehmigten Methoden anfordern, z. B. JIT-Verfahren wie [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) oder anderer interner Tools und Verfahren. 
1. Der Sicherheitsadministrator genehmigt die Anforderung (falls er sie als berechtigt erachtet) und informiert den Backupadministrator. Der Backupadministrator verfügt nun über die Rolle „Mitwirkender“ in Resource Guard.
1. Der Backupadministrator navigiert zum Tresor > Eigenschaften > Mehrbenutzerautorisierung.
1. Klicken Sie auf **Aktualisieren**.
   1. Deaktivieren Sie das Kontrollkästchen „Mit Resource Guard schützen“.
   1. Wählen Sie das Verzeichnis aus, das die Resource Guard-Instanz enthält, und überprüfen Sie den Zugriff mithilfe der Schaltfläche Authentifizieren (falls zutreffend).
   1. Klicken Sie nach der **Authentifizierung** auf **Speichern**. Mit dem richtigen Zugriff sollte die Anforderung erfolgreich abgeschlossen werden.

