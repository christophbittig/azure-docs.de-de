---
title: Selektive Kennworthashsynchronisierung für Azure AD Connect
description: In diesem Artikel wird beschrieben, wie Sie die selektive Kennworthashsynchronisierung für die Verwendung mit Azure AD Connect einrichten und konfigurieren.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bbdc6a28af9bab40ede4f6a3a065b3ee776cf29
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235400"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Konfigurieren der selektiven Kennworthashsynchronisierung für Azure AD Connect

Die [Kennworthashsynchronisierung](whatis-phs.md) ist eine der Anmeldemethoden, die zur Implementierung von Hybrididentitäten verwendet wird. Azure AD Connect synchronisiert einen Hash eines Benutzerkennworthashs aus einer lokalen Active Directory-Instanz mit einer cloudbasierten Azure AD-Instanz.  Standardmäßig erfolgt die Kennworthashsynchronisierung nach der Einrichtung für alle Benutzer, die Sie synchronisieren.

Wenn Sie einen Teil der Benutzer von der Kennworthashsynchronisierung mit Azure AD ausschließen möchten, können Sie die selektive Kennworthashsynchronisierung mithilfe der in diesem Artikel beschriebenen Schritte konfigurieren.

> [!IMPORTANT]
> Microsoft unterstützt die Änderung oder den Einsatz der Azure AD Connect-Synchronisierung außerhalb dieser formal dokumentierten Konfigurationen oder Aktionen nicht. Jede dieser Konfigurationen oder Aktionen kann zu einem inkonsistenten oder nicht unterstützten Zustand von Azure AD Connect sync führen. Infolgedessen kann Microsoft nicht garantieren, dass wir in der Lage sind, effizienten technischen Support für solche Bereitstellungen zu leisten.

## <a name="consider-your-implementation"></a>Planen Ihrer Implementierung

Um den Verwaltungsaufwand für die Konfiguration zu reduzieren, sollten Sie zunächst die Anzahl von Benutzerobjekten einschätzen, die Sie von der Kennworthashsynchronisierung ausschließen möchten. Überlegen Sie, welches der nachfolgenden Szenarien (die sich gegenseitig ausschließen) Ihren Anforderungen entspricht, um die richtige Konfigurationsoption auszuwählen.
- Wenn die Anzahl der **auszuschließenden** Benutzer **kleiner** ist als die Anzahl **einzuschließender** Benutzer, führen Sie die Schritte in [diesem Abschnitt](#excluded-users-is-smaller-than-included-users) aus.
- Wenn die Anzahl der **auszuschließenden** Benutzer **größer** ist als die Anzahl **einzuschließender** Benutzer, führen Sie die Schritte in [diesem Abschnitt](#excluded-users-is-larger-than-included-users) aus.

> [!IMPORTANT]
> Nachdem Sie eine der beiden Konfigurationsoptionen ausgewählt haben, ist eine anfängliche Synchronisierung (vollständige Synchronisierung) erforderlich, um die Änderungen anzuwenden. Diese wird im nächsten Synchronisierungszyklus ausgeführt.

> [!IMPORTANT]
> Das Konfigurieren der selektiven Kennwort-Hash-Synchronisierung übt einen direkten Einfluss auf das Kennwortrückschreiben aus. Kennwortänderungen oder Kennwortrücksetzungen, die in Azure Active Directory initiiert werden, werden nur dann in das lokale Active Directory zurückgeschrieben, wenn sich der Benutzer im Geltungsbereich der Kennwort-Hash-Synchronisierung befindet.

### <a name="the-admindescription-attribute"></a>Das adminDescription-Attribut

Bei beiden Szenarien wird das adminDescription-Attribut von Benutzern auf einen bestimmten Wert festgelegt.  Dies ermöglicht die Anwendung der Regeln und ist das, was das Funktionieren der selektive PHS ermöglicht.

|Szenario|Wert von adminDescription|
|-----|-----|
|Weniger ausgeschlossene als eingeschlossene Benutzer|PHSFiltered|
|Mehr ausgeschlossene als eingeschlossene Benutzer|PHSIncluded|

Dieses Attribut kann wie folgt festgelegt werden:

- über die Benutzeroberfläche von Active Directory-Benutzer und -Computer
- mithilfe des PowerShell-Cmdlets `Set-ADUser`  Weitere Informationen finden Sie unter [Set-ADUser](/powershell/module/activedirectory/set-aduser).

### <a name="disable-the-synchronization-scheduler"></a>Deaktivieren des Synchronisierungsplaners

Bevor Sie mit einem der beiden Szenarien beginnen können, müssen Sie den Synchronisierungsplaner deaktivieren, während Sie Änderungen an den Synchronisierungsregeln vornehmen.
 1. Starten Sie Windows PowerShell.

     `set-adsyncscheduler-synccycleenabled$false`

2. Vergewissern Sie sich, dass der Planer deaktiviert ist, indem Sie das folgende Cmdlet ausführen:

    `get-adsyncscheduler`

Weitere Informationen zum Planer finden Sie unter [Azure AD Connect-Synchronisierungsplaner](how-to-connect-sync-feature-scheduler.md).

## <a name="excluded-users-is-smaller-than-included-users"></a>Weniger ausgeschlossene als eingeschlossene Benutzer

Im folgenden Abschnitt wird beschrieben, wie Sie die selektive Kennworthashsynchronisierung aktivieren, wenn die Anzahl der **auszuschließenden** Benutzer **kleiner** als die Anzahl der **einzuschließenden** Benutzer ist.

> [!IMPORTANT]
> Stellen Sie vor dem Fortfahren wie oben beschrieben sicher, dass der Synchronisierungsplaner deaktiviert ist.

- Erstellen Sie eine bearbeitbare Kopie von **Eingehend aus AD – Benutzerkonto aktiviert** mit **deaktivierter Option zum Aktivieren der Kennworthashsynchronisierung**, und definieren Sie den Bereichsfilter.
- Erstellen Sie eine bearbeitbare Kopie von **Eingehend aus AD – Benutzerkonto aktiviert** mit **aktivierter Option zum Aktivieren der Kennworthashsynchronisierung**, und definieren Sie den Bereichsfilter.
- Erneutes Aktivieren des Synchronisierungsplaners
- Legen Sie in Active Directory für Benutzer, für die Sie die Kennworthashsynchronisierung zulassen möchten, den Attributwert fest, der als Bereichsattribut definiert wurde.

> [!IMPORTANT]
> Die angegebenen Schritte zur Konfiguration der selektiven Kennwort-Hash-Synchronisierung wirken sich nur auf Benutzerobjekte aus, bei denen das Attribut **adminDescription** in Active Directory mit dem Wert **PHSFiltered** gefüllt ist.
Wenn dieses Attribut nicht angegeben oder der Wert nicht auf **PHSFiltered** festgelegt wurde, werden diese Regeln nicht auf die Benutzerobjekte angewandt.

### <a name="configure-the-necessary-synchronization-rules"></a>Konfigurieren der erforderlichen Synchronisierungsregeln

 1. Starten Sie den Synchronisierungsregel-Editor, und legen Sie die Filter **Kennwortsynchronisierung** auf **Ein** und **Regeltyp** auf **Standard** fest.
     ![Starten des Synchronisierungsregel-Editors](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Wählen Sie die Regel **Eingehend aus AD – Benutzerkonto aktiviert** für den Active Directory-Gesamtstrukturconnector aus, für den Sie die selektive Kennworthashsynchronisierung konfigurieren möchten, und klicken Sie auf **Bearbeiten**. Wählen Sie im nächsten Dialogfeld **Ja** aus, um eine bearbeitbare Kopie der ursprünglichen Regel zu erstellen.
     ![Auswählen der Regel](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. Die erste Regel wird die Kennwort-Hash-Synchronisierung deaktivieren. Geben Sie der neuen benutzerdefinierten Regel den folgenden Namen: **In vom AD - User AccountEnabled - Filter Users from PHS**.
 Ändern Sie den Rangfolgenwert in eine Zahl unter 100 (z. B. **90** oder den niedrigsten Wert in Ihrer Umgebung).
 Stellen Sie sicher, dass die Kontrollkästchen **Kennwortsynchronisierung aktivieren** und **Deaktiviert** deaktiviert sind.
 Klicken Sie auf **Weiter**.
  ![Bearbeiten der Eingangsregel](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. Klicken Sie unter **Bereichsfilter** auf **Klausel hinzufügen**.
 Wählen Sie in der Spalte „Attribut“ **adminDescription** und in der Spalte „Operator“ **EQUAL** aus, und geben Sie als Wert **PHSFiltered** ein.
     ![Bereichsfilter](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Es sind keine weiteren Änderungen erforderlich. **Verknüpfungsregeln** und **Transformationen** sollten mit den Standardeinstellungen übernommen werden. Sie können daher jetzt auf **Speichern** klicken.
 Klicken Sie in dem Warnungsdialogfeld, in dem Sie darüber informiert werden, dass im nächsten Synchronisierungszyklus des Connectors eine vollständige Synchronisierung ausgeführt wird, auf **OK**.
     ![Speichern der Regel](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Erstellen Sie als Nächstes eine weitere benutzerdefinierte Regel mit aktivierter Kennworthashsynchronisierung. Wählen Sie erneut die Standardregel **Eingehend aus AD – Benutzerkonto aktiviert** für die Active Directory-Gesamtstruktur aus, für die Sie die selektive Kennworthashsynchronisierung konfigurieren möchten, und klicken Sie auf **Bearbeiten**. Wählen Sie im nächsten Dialogfeld **Ja** aus, um eine bearbeitbare Kopie der ursprünglichen Regel zu erstellen.
     ![Benutzerdefinierte Regel](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Geben Sie für die neue benutzerdefinierte Regel den folgenden Namen an: **Eingehend aus AD – Benutzerkonto aktiviert – Benutzer in Kennworthashsynchronisierung eingeschlossen**.
 Ändern Sie den Rangfolgenwert in eine niedrigere Zahl als bei der zuvor erstellten Regel (in diesem Beispiel **89**).
 Vergewissern Sie sich, dass das Kontrollkästchen **Kennwortsynchronisierung aktivieren** aktiviert und das Kontrollkästchen **Deaktiviert** deaktiviert ist.
 Klicken Sie auf **Weiter**.  
     ![Bearbeiten der neuen Regel](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. Klicken Sie unter **Bereichsfilter** auf **Klausel hinzufügen**.
 Wählen Sie in der Spalte „Attribut“ **adminDescription** und in der Spalte „Operator“ **NOTEQUAL** aus, und geben Sie als Wert **PHSFiltered** ein.
     ![Bereichsregel](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Es sind keine weiteren Änderungen erforderlich. **Verknüpfungsregeln** und **Transformationen** sollten mit den Standardeinstellungen übernommen werden. Sie können daher jetzt auf **Speichern** klicken.
 Klicken Sie in dem Warnungsdialogfeld, in dem Sie darüber informiert werden, dass im nächsten Synchronisierungszyklus des Connectors eine vollständige Synchronisierung ausgeführt wird, auf **OK**.
     ![Verknüpfungsregeln](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Bestätigen Sie das Erstellen der Regeln. Entfernen Sie die Filter für die **Kennwortsynchronisierung** (**Ein**) und den **Regeltyp** (**Standard**). Es sollten die beiden neuen Regeln angezeigt werden, die Sie soeben erstellt haben.
     ![Bestätigen der Regeln](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Erneutes Aktivieren des Synchronisierungsplaners

Nachdem Sie die Schritte zum Konfigurieren der erforderlichen Synchronisierungsregeln abgeschlossen haben, aktivieren Sie den Synchronisierungsplaner mit den folgenden Schritten erneut:
 1. Führen Sie in Windows PowerShell folgenden Befehl aus:

     `set-adsyncscheduler-synccycleenabled$true`

 2. Vergewissern Sie sich dann, dass die Aktivierung erfolgreich war, indem Sie Folgendes ausführen:

     `get-adsyncscheduler`

Weitere Informationen zum Planer finden Sie unter [Azure AD Connect-Synchronisierungsplaner](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Bearbeiten des Benutzerattributs **adminDescription**

Wenn die gesamte Konfiguration abgeschlossen ist, müssen Sie das **adminDescription**-Attribut für alle Benutzer bearbeiten, die Sie von der Kennworthashsynchronisierung in Active Directory **ausschließen** möchten, und die Zeichenfolge im Bereichsfilter hinzufügen: **PHSFiltered**.

  ![Attribut bearbeiten](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

Sie können auch den folgenden PowerShell-Befehl verwenden, um das **adminDescription** -Attribut eines Benutzers zu bearbeiten:

`set-adusermyuser-replace@{adminDescription="PHSFiltered"}`

## <a name="excluded-users-is-larger-than-included-users"></a>Mehr ausgeschlossene als eingeschlossene Benutzer

Im folgenden Abschnitt wird beschrieben, wie Sie die selektive Kennworthashsynchronisierung aktivieren, wenn die Anzahl der **auszuschließenden** Benutzer **größer** als die Anzahl der **einzuschließenden** Benutzer ist.

> [!IMPORTANT]
> Stellen Sie vor dem Fortfahren wie oben beschrieben sicher, dass der Synchronisierungsplaner deaktiviert ist.

Im Folgenden finden Sie eine Zusammenfassung der Aktionen, die in den folgenden Schritten ausgeführt werden:

- Erstellen Sie eine bearbeitbare Kopie von **Eingehend aus AD – Benutzerkonto aktiviert** mit **deaktivierter Option zum Aktivieren der Kennworthashsynchronisierung**, und definieren Sie den Bereichsfilter.
- Erstellen Sie eine bearbeitbare Kopie von **Eingehend aus AD – Benutzerkonto aktiviert** mit **aktivierter Option zum Aktivieren der Kennworthashsynchronisierung**, und definieren Sie den Bereichsfilter.
- Erneutes Aktivieren des Synchronisierungsplaners
- Legen Sie in Active Directory für Benutzer, für die Sie die Kennworthashsynchronisierung zulassen möchten, den Attributwert fest, der als Bereichsattribut definiert wurde.

> [!IMPORTANT]
> Die angegebenen Schritte zur Konfiguration der selektiven Kennwort-Hash-Synchronisierung wirken sich nur auf Benutzerobjekte aus, bei denen das Attribut **adminDescription** im Active Directory mit dem Wert **PHSIncluded** ausgefüllt ist.
Wenn dieses Attribut nicht angegeben oder der Wert nicht auf **PHSIncluded** festgelegt wurde, werden diese Regeln nicht auf die Benutzerobjekte angewandt.

### <a name="configure-the-necessary-synchronization-rules"></a>Konfigurieren der erforderlichen Synchronisierungsregeln

 1. Starten Sie den Synchronisierungsregel-Editor, und legen Sie die Filter **Kennwortsynchronisierung** auf **Ein** und **Regeltyp** auf **Standard** fest.
     ![Regeltyp](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Wählen Sie die Regel **Eingehend aus AD – Benutzerkonto aktiviert** für die Active Directory-Gesamtstruktur aus, für die Sie die selektive Kennworthashsynchronisierung konfigurieren möchten, und klicken Sie auf **Bearbeiten**. Wählen Sie im nächsten Dialogfeld **Ja** aus, um eine bearbeitbare Kopie der ursprünglichen Regel zu erstellen.
     ![Eingehend aus AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. Die erste Regel wird die Kennwort-Hash-Synchronisierung deaktivieren. Geben Sie der neuen benutzerdefinierten Regel den folgenden Namen: **In vom AD - User AccountEnabled - Filter Users from PHS**.
 Ändern Sie den Rangfolgenwert in eine Zahl unter 100 (z. B. **90** oder den niedrigsten Wert in Ihrer Umgebung).
 Stellen Sie sicher, dass die Kontrollkästchen **Kennwortsynchronisierung aktivieren** und **Deaktiviert** deaktiviert sind.
 Klicken Sie auf **Weiter**.
  ![Festlegen der Rangfolge](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. Klicken Sie unter **Bereichsfilter** auf **Klausel hinzufügen**.
Wählen Sie in der Spalte „Attribut“ **adminDescription** und in der Spalte „Operator“ **NOTEQUAL** aus, und geben Sie als Wert **PHSIncluded** ein.
     ![Klausel hinzufügen](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Es sind keine weiteren Änderungen erforderlich. **Verknüpfungsregeln** und **Transformationen** sollten mit den Standardeinstellungen übernommen werden. Sie können daher jetzt auf **Speichern** klicken.
 Klicken Sie in dem Warnungsdialogfeld, in dem Sie darüber informiert werden, dass im nächsten Synchronisierungszyklus des Connectors eine vollständige Synchronisierung ausgeführt wird, auf **OK**.
     ![Transformation](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Erstellen Sie als Nächstes eine weitere benutzerdefinierte Regel mit aktivierter Kennworthashsynchronisierung. Wählen Sie erneut die Standardregel **Eingehend aus AD – Benutzerkonto aktiviert** für die Active Directory-Gesamtstruktur aus, für die Sie die selektive Kennworthashsynchronisierung konfigurieren möchten, und klicken Sie auf **Bearbeiten**. Wählen Sie im nächsten Dialogfeld **Ja** aus, um eine bearbeitbare Kopie der ursprünglichen Regel zu erstellen.
     ![Benutzerkonto aktiviert](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Geben Sie für die neue benutzerdefinierte Regel den folgenden Namen an: **Eingehend aus AD – Benutzerkonto aktiviert – Benutzer in Kennworthashsynchronisierung eingeschlossen**.
 Ändern Sie den Rangfolgenwert in eine niedrigere Zahl als bei der zuvor erstellten Regel (in diesem Beispiel **89**).
 Vergewissern Sie sich, dass das Kontrollkästchen **Kennwortsynchronisierung aktivieren** aktiviert und das Kontrollkästchen **Deaktiviert** deaktiviert ist.
 Klicken Sie auf **Weiter**.
     ![Aktivieren der Kennwortsynchronisierung](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. Klicken Sie unter **Bereichsfilter** auf **Klausel hinzufügen**.
 Wählen Sie in der Spalte „Attribut“ **adminDescription** und in der Spalte „Operator“ **EQUAL** aus, und geben Sie als Wert **PHSIncluded** ein.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Es sind keine weiteren Änderungen erforderlich. **Verknüpfungsregeln** und **Transformationen** sollten mit den Standardeinstellungen übernommen werden. Sie können daher jetzt auf **Speichern** klicken.
 Klicken Sie in dem Warnungsdialogfeld, in dem Sie darüber informiert werden, dass im nächsten Synchronisierungszyklus des Connectors eine vollständige Synchronisierung ausgeführt wird, auf **OK**.
     ![Jetzt speichern](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10. Bestätigen Sie das Erstellen der Regeln. Entfernen Sie die Filter für die **Kennwortsynchronisierung** (**Ein**) und den **Regeltyp** (**Standard**). Es sollten die beiden neuen Regeln angezeigt werden, die Sie soeben erstellt haben.
     ![Aktivierte Synchronisierung](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Erneutes Aktivieren des Synchronisierungsplaners

Nachdem Sie die Schritte zum Konfigurieren der erforderlichen Synchronisierungsregeln abgeschlossen haben, aktivieren Sie den Synchronisierungsplaner mit den folgenden Schritten erneut:

1. Führen Sie in Windows PowerShell folgenden Befehl aus:

   `set-adsyncscheduler-synccycleenabled$true`

2. Vergewissern Sie sich dann, dass die Aktivierung erfolgreich war, indem Sie Folgendes ausführen:

   `get-adsyncscheduler`

Weitere Informationen zum Planer finden Sie unter [Azure AD Connect-Synchronisierungsplaner](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Bearbeiten des Benutzerattributs **adminDescription**

Wenn die gesamte Konfiguration abgeschlossen ist, müssen Sie das **adminDescription**-Attribut für alle Benutzer bearbeiten, die Sie in die Kennworthashsynchronisierung in Active Directory **einschließen** möchten, und die Zeichenfolge im Bereichsfilter hinzufügen: **PHSIncluded**.

  ![Bearbeiten von Attributen](media/how-to-connect-selective-password-hash-synchronization/include-11.png)

Sie können auch den folgenden PowerShell-Befehl verwenden, um das **adminDescription** -Attribut eines Benutzers zu bearbeiten:

`Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}`

## <a name="next-steps"></a>Nächste Schritte

- [Was ist Kennworthashsynchronisierung?](whatis-phs.md)
- [Funktionsweise der Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md)
