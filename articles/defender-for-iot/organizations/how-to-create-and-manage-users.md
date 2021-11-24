---
title: Erstellen und Verwalten von Benutzern
description: Erfahren Sie, wie Sie Benutzer von Sensoren und der lokalen Verwaltungskonsole erstellen und verwalten. Benutzern kann eine der Rollen „Administrator“, „Sicherheitsanalyst“ oder „Benutzer mit Lesezugriff“ zugewiesen werden.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 73e08c7cdca6e57445566a99cb779a322306f944
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318938"
---
# <a name="about-defender-for-iot-console-users"></a>Grundlegendes zu Benutzern der Defender für IoT-Konsole

In diesem Artikel wird beschrieben, wie Sie Benutzer von Sensoren und der lokalen Verwaltungskonsole erstellen und verwalten. Zu den Benutzerrollen zählen „Administrator“, „Sicherheitsanalyst“ und „Benutzer mit Lesezugriff“. Jeder Rolle ist ein Bereich von Berechtigungen für Tools für den Sensor oder die lokale Verwaltungskonsole zugeordnet. Rollen sind so konzipiert, dass sie einen differenzierten und sicheren Zugriff auf Microsoft Defender für IoT ermöglichen.

Außerdem stehen Funktionen zum Nachverfolgen der Benutzeraktivität und zum Aktivieren der Active Directory-Anmeldung zur Verfügung.

Standardmäßig werden die einzelnen Sensoren und die lokale Verwaltungskonsole mit den Benutzern *„cyberx“ und „support“* installiert. Diese Benutzer haben Zugriff auf erweiterte Tools für die Problembehandlung und das Setup. Administratorbenutzer sollten sich mit diesen Benutzeranmeldeinformationen anmelden und dann einen Administratorbenutzer sowie weitere Benutzer mit den Rollen „Sicherheitsanalyst“ und „Benutzer mit Leseberechtigung“ erstellen.

## <a name="role-based-permissions"></a>Rollenbasierte Berechtigungen
Die folgenden Benutzerrollen sind verfügbar:

- **Nur Lesezugriff:** Benutzer mit Leseberechtigung führen Aufgaben wie das Anzeigen von Warnungen und Geräten in den Gerätezuordnungen aus. Diese Benutzer haben Zugriff auf Optionen, die unter **Navigation** angezeigt werden.

- **Sicherheitsanalyst:** Sicherheitsanalysten sind Benutzer mit Leseberechtigung. Sie können darüber hinaus Aktionen auf Geräten durchführen, Warnungen bestätigen und Untersuchungstools verwenden. Diese Benutzer haben Zugriff auf Optionen, die unter **Navigation** und **Analyse** angezeigt werden.

- **Administrator**: Administratoren haben Zugriff auf alle Tools, einschließlich der Definition von Systemkonfigurationen, der Erstellung und Verwaltung von Benutzern usw. Diese Benutzer haben Zugriff auf Optionen, die unter **Navigation**, **Analyse** und **Verwaltung** angezeigt werden.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Rollenbasierte Berechtigungen für die Tools der lokalen Verwaltungskonsole

In diesem Abschnitt werden die Berechtigungen beschrieben, die Administratoren, Sicherheitsanalysten und Benutzern mit Leseberechtigung für die lokale Verwaltungskonsole zur Verfügung stehen.  

| Berechtigung | Nur Lesezugriff | Sicherheitsanalyst | Administrator |
|--|--|--|--|
| Anzeigen und Filtern der Gerätezuordnungen im Unternehmen | ✓ | ✓ | ✓ |
| Erstellen eines Standorts |  |  | ✓ |
| Verwalten eines Standorts (Hinzufügen und Bearbeiten von Zonen) |  |  | ✓ |
| Anzeigen und Filtern des Gerätebestands | ✓ | ✓ | ✓ |
| Anzeigen und Verwalten von Warnungen: Bestätigen, Erlernen und Anheften | ✓ | ✓ | ✓ |
| Generieren von Berichten |  | ✓ | ✓ |
| Anzeigen von Risikobewertungsberichten |  | ✓ | ✓ |
| Festlegen von Warnungsausschlüssen |  | ✓ | ✓ |
| Anzeigen oder Definieren von Zugriffsgruppen |  |  | ✓ |
| Verwalten von Systemeinstellungen |  |  | ✓ |
| Verwalten von Benutzern |  |  | ✓ |
| Senden von Warnungsdaten an Partner |  |  | ✓ |
| Verwalten von Zertifikaten |  |  | ✓ |
| Sitzungstimeout bei inaktiven Benutzern | 30 Minuten | 30 Minuten  | 30 Minuten  |

#### <a name="assign-users-to-access-groups"></a>Zuweisen von Benutzern zu Zugriffsgruppen

Administratoren können die Benutzerzugriffssteuerung in Defender für IoT verbessern, indem sie Benutzer bestimmten *Zugriffsgruppen* zuweisen. Zugriffsgruppen werden Zonen, Standorten, Regionen und Geschäftseinheiten zugewiesen, in denen sich ein Sensor befindet. Durch das Zuweisen von Benutzern zu Zugriffsgruppen erhalten Administratoren mehr Kontrolle darüber, wo Benutzer Geräteerkennungen verwalten und analysieren. 

Diese Vorgehensweise kommt auch großen Organisationen entgegen, in denen Benutzerberechtigungen oftmals komplex sind oder durch eine globale Sicherheitsrichtlinie der Organisation festgelegt werden. Weitere Informationen finden Sie unter [Definieren einer globalen Zugriffssteuerung](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Rollenbasierte Berechtigungen für Sensortools

In diesem Abschnitt werden die Berechtigungen beschrieben, die Administratoren, Sicherheitsanalysten und Benutzern mit Leseberechtigung zur Verfügung stehen.  

| Berechtigung | Nur Lesezugriff | Sicherheitsanalyst | Administrator |
|--|--|--|--|
| Anzeigen des Dashboards | ✓ | ✓ | ✓ |
| Steuern der Vergrößerung von Zuordnungsansichten |  |  | ✓ |
| Anzeigen von Warnungen | ✓ | ✓ | ✓ |
| Verwalten von Warnungen: Bestätigen, Erlernen und Anheften |  | ✓ | ✓ |
| Anzeigen von Ereignissen auf einer Zeitachse |  | ✓ | ✓ |
| Autorisieren von Geräten, bekannten Scannergeräten, programmierbaren Geräten |  | ✓ | ✓ |
| Anzeigen von Untersuchungsdaten | ✓ | ✓ | ✓ |
| Verwalten von Systemeinstellungen |  |  | ✓ |
| Verwalten von Benutzern |  |  | ✓ |
| DNS-Server für Reverse-Lookup |  |  | ✓ |
| Senden von Warnungsdaten an Partner |  | ✓ | ✓ |
| Erstellen von Warnungskommentaren |  | ✓ | ✓ |
| Anzeigen des Verlauf von Programmieränderungen | ✓ | ✓ | ✓ |
| Erstellen angepasster Warnungsregeln |  | ✓ | ✓ |
| Verwalten mehrerer Benachrichtigungen gleichzeitig |  | ✓ | ✓ |
| Verwalten von Zertifikaten |  |  | ✓ |
| Sitzungstimeout bei inaktiven Benutzern | 30 Minuten | 30 Minuten | 30 Minuten |

## <a name="define-users"></a>Definieren von Benutzern

In diesem Abschnitt wird das Definieren von Benutzern beschrieben. Die Benutzer „cyberx“, „support“ und „Administrator“ können Definitionen anderer Benutzer hinzufügen, entfernen und aktualisieren.

**So definieren Sie einen Benutzer:**

1. Wählen Sie im linken Bereich des Sensors oder der lokalen Verwaltungskonsole **Benutzer** aus.
1. Wählen Sie im Fenster **Benutzer** die Option **Benutzer erstellen** aus.
1. Legen Sie im Bereich **Benutzer erstellen** die folgenden Parameter fest:

   - **Benutzername**: Geben Sie einen Benutzernamen ein.
   - **E-Mail**: Geben Sie die E-Mail-Adresse des Benutzers ein.
   - **Vorname:** Geben Sie den Vornamen des Benutzers ein.
   - **Nachname:** Geben Sie den Nachnamen des Benutzers ein.
   - **Rolle**: Legen Sie die Rolle des Benutzers fest. Weitere Informationen finden Sie unter [Rollenbasierte Berechtigungen](#role-based-permissions).
   - **Zugriffsgruppe:** Wenn Sie einen Benutzer für die lokale Verwaltungskonsole erstellen, legen Sie die Zugriffsgruppe des Benutzers fest. Weitere Informationen finden Sie unter [Definieren der globalen Zugriffssteuerung](how-to-define-global-user-access-control.md).
   - **Kennwort**: Wählen Sie den Benutzertyp wie folgt aus:
     - **Lokaler Benutzer:** Legen Sie ein Kennwort für den Benutzer eines Sensors oder einer lokalen Verwaltungskonsole fest. Das Kennwort muss mindestens sechs Zeichen umfassen und Buchstaben und Ziffern enthalten.
     - **Active Directory-Benutzer:** Sie können Benutzern ermöglichen, sich mit Active Directory-Anmeldeinformationen beim Sensor bzw. bei der Verwaltungskonsole anzumelden. Sie können definierten Active Directory-Gruppen bestimmte Berechtigungsebenen zuordnen. Konfigurieren Sie z. B. eine bestimmte Active Directory-Gruppe, und weisen Sie alle Benutzer in der Gruppe dem Typ „Benutzer mit Lesezugriff“ zu.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Verwalten von Benutzern":::

## <a name="user-session-timeout"></a>Benutzersitzungstimeout

Wenn Benutzer für einen bestimmten Zeitraum Tastatur oder Maus nicht verwenden, werden sie von ihrer Sitzung abgemeldet und müssen sich erneut anmelden.

Verwenden Benutzer 30 Minuten lang weder Maus noch Tastatur an der Konsole, wird eine Abmeldung von der Sitzung erzwungen.

Dieses Feature ist standardmäßig aktiviert und wird auch bei einem Upgrade aktiviert, es kann aber deaktiviert werden. Außerdem kann die Sitzungsdauer angepasst werden. Die Sitzungszeiten werden in Sekunden angegeben. Die Definitionen werden pro Sensor und lokaler Verwaltungskonsole angewandt.

An der Konsole wird eine Meldung zum Sitzungstimeout angezeigt, wenn das Timeout für Inaktivität überschritten wurde.

### <a name="control-inactivity-sign-out"></a>Steuern der Abmeldung bei Inaktivität

Administratorbenutzer können die Abmeldung bei Inaktivität aktivieren und deaktivieren und die zugehörigen Schwellenwerte anpassen.

**So greifen Sie auf den Befehl zu:**

1. Melden Sie sich bei der Befehlszeilenschnittstelle für den Sensor oder die lokale Verwaltungskonsole an, und verwenden Sie dabei Administratoranmeldeinformationen für Defender für IoT.

1. Geben Sie `sudo nano /var/cyberx/properties/authentication` ein.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Ändern Sie `infinity_session_expiration = true` in `infinity_session_expiration = false`, um die Funktion zu deaktivieren.

Um die Abmeldezeiten zu aktualisieren, ändern Sie den Wert von `= <number>` in die erforderliche Zeit.

## <a name="track-user-activity"></a>Nachverfolgen der Benutzeraktivität

Sie können die Benutzeraktivität auf jedem Sensor auf der Ereigniszeitachse nachverfolgen. Die Zeitachse zeigt das Ereignis oder das betroffene Gerät sowie das Datum und die Uhrzeit an, zu der der Benutzer die Aktivität ausgeführt hat.

**So zeigen Sie Benutzeraktivitäten an:**

1. Melden Sie sich beim Sensor an.

1. Aktivieren Sie auf der Ereigniszeitachse die Option **Vorgänge für Benutzer**.

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Anzeigen einer Benutzeraktivität":::

## <a name="integrate-with-active-directory-servers"></a>Integration mit Active Directory-Servern

Sie können den Sensor oder die lokale Verwaltungskonsole für die Zusammenarbeit mit Active Directory konfigurieren. Dadurch können Active Directory-Benutzer mithilfe ihrer Active Directory-Anmeldeinformationen auf die Defender für IoT-Konsolen zugreifen.

> [!Note]
> LDAP v3 wird unterstützt.

Zwei LDAP-basierte Authentifizierungstypen werden unterstützt:

- **Vollständige Authentifizierung:** Die Benutzerdetails werden vom LDAP-Server abgerufen. Beispiele hierfür sind Vorname, Nachname, E-Mail-Adresse und Benutzerberechtigungen.

- **Vertrauenswürdiger Benutzer:** Nur das Benutzerkennwort wird abgerufen. Die weiteren abgerufenen Benutzerinformationen hängen von den im Sensor definierten Benutzern ab.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Berechtigungen für Active Directory und Defender für IoT

Sie können den hier definierten Active Directory-Gruppen bestimmte Berechtigungsebenen zuordnen. Konfigurieren Sie z. B. eine bestimmte Active Directory-Gruppe, und weisen Sie allen Benutzern in der Gruppe die Berechtigungen für den Lesezugriff zu.

**So konfigurieren Sie Active Directory:**

1. Wählen Sie im linken Bereich **Systemeinstellungen** aus.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Anzeigen Ihrer Active Directory-Systemeinstellungen":::

1. Wählen Sie im Bereich **Systemeinstellungen** die Option **Active Directory** aus.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Bearbeiten Ihrer Active Directory-Konfigurationen":::

1. Wählen Sie im Dialogfeld **Edit Active Directory Configuration** (Active Directory Konfiguration bearbeiten) die Option **Active Directory Integration Enabled (Active Directory-Integration aktiviert)**  > **Speichern** aus. Das Dialogfeld **Edit Active Directory Configuration** (Active Directory Konfiguration bearbeiten) wird erweitert, sodass Sie nun die Parameter zum Konfigurieren von Active Directory eingeben können.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Eingeben der Parameter zum Konfigurieren von Active Directory":::

> [!NOTE]
> - Sie müssen die LDAP-Parameter hier genau so definieren, wie sie in Active Directory angezeigt werden.
> - Verwenden Sie für alle Active Directory-Parameter nur Kleinbuchstaben. Verwenden Sie selbst dann Kleinbuchstaben, wenn bei den Konfigurationen in Active Directory Großbuchstaben verwendet werden.
> - Es ist nicht möglich, sowohl LDAP als auch LDAPS für dieselbe Domäne zu konfigurieren. Sie können jedoch beide gleichzeitig für verschiedene Domänen verwenden.

1. Legen Sie die Parameter für Active Directory-Server wie folgt fest:

   | Serverparameter | BESCHREIBUNG |
   |--|--|
   | Vollqualifizierter Domänenname des Domänencontrollers (FQDN) | Legen Sie den vollqualifizierten Domänennamen (FQDN) genau so fest, wie er auf dem LDAP-Server angezeigt wird. Geben Sie beispielsweise `host1.subdomain.domain.com` ein. |
   | Domänencontrollerport | Legen Sie den Port fest, an dem Ihr LDAP konfiguriert ist. |
   | Primäre Domäne | Legen Sie den Domänennamen (z. B. `subdomain.domain.com`) und den Verbindungstyp gemäß Ihrer LDAP-Konfiguration fest. |
   | Active Directory-Gruppen | Geben Sie die Gruppennamen ein, die in der Active Directory-Konfiguration auf dem LDAP-Server definiert sind. |
   | Vertrauenswürdige Domänen | Wenn Sie vertrauenswürdige Domänen hinzufügen möchten, geben Sie den Domänennamen und den Verbindungstyp an. <br />Vertrauenswürdige Domänen können nur für Benutzer konfiguriert werden, die unter „Benutzer“ definiert wurden. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>ActiveDirectory-Gruppen für die lokale Verwaltungskonsole

Wenn Sie Active Directory-Gruppen für Benutzer der lokalen Verwaltungskonsole erstellen, müssen Sie für jede Active Directory-Gruppe eine Zugriffsgruppenregel erstellen. Die Active Directory-Anmeldeinformationen für die lokale Verwaltungskonsole können nicht verwendet werden, wenn für die Active Directory-Benutzergruppe keine Zugriffsgruppenregel vorhanden ist. Weitere Informationen finden Sie unter [Definieren der globalen Zugriffssteuerung](how-to-define-global-user-access-control.md).

1. Wählen Sie **Speichern** aus.

1. Wählen Sie zum Hinzufügen eines vertrauenswürdigen Servers **Server hinzufügen** aus, und konfigurieren Sie einen weiteren Server.

## <a name="change-a-users-password"></a>Ändern des Kennworts eines Benutzers

Der Administrator kann das Kennwort für die Rolle „Sicherheitsanalyst“ und „Benutzer mit Leseberechtigung“ ändern. Der Benutzer mit der Administratorrolle kann sein eigenes Kennwort nicht ändern und muss sich an eine Person mit einer Rolle auf höherer Ebene wenden. Benutzer mit den Rollen „Sicherheitsanalyst“ und „Benutzer mit Leseberechtigung“ können ihre Kennwörter oder die Kennwörter anderer Rollen nicht zurücksetzen. Benutzer mit den Rollen „Sicherheitsanalyst“ und „Benutzer mit Leseberechtigung“ müssen sich an einen Benutzer mit einer Rolle auf höherer Ebene wenden, um das Kennwort zurücksetzen zu lassen. Ein Benutzer mit der Rolle „CyberX“ kann das Kennwort für alle Benutzerrollen ändern. Ein Benutzer mit der Rolle „Support“ kann das Kennwort für die Rollen „Support“, „Administrator“, „Sicherheitsanalyst“ und „Benutzer mit Leseberechtigung“ ändern.  

**So setzen Sie das Kennwort eines Benutzers für den Sensor zurück:**

1. Melden Sie sich beim Sensor mit einem Benutzer mit der Rolle „Administrator“, „Support“ oder „CyberX“ an.

1. Wählen Sie **Benutzer** im linken Bereich aus.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Wählen Sie im Bereich auf der linken Seite die Option „Benutzer“ aus.":::

1. Suchen Sie den Benutzer, und wählen Sie **Bearbeiten** im Dropdownmenü **Aktionen** aus.

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Wählen Sie im Dropdownmenü „Aktionen“ die Option „Bearbeiten“ aus.":::

1. Geben Sie das neue Kennwort in die Felder **Neues Kennwort** und **Neues Kennwort bestätigen** ein.

    > [!NOTE]
    > Kennwörter müssen mindestens 16 Zeichen lang sein und klein und groß geschriebene alphabetische Zeichen, Zahlen und eines der folgenden Symbole enthalten: #%*+,-./:=?@[]^_{}~

1. Wählen Sie **Aktualisieren** aus.

**So setzen Sie das Kennwort eines Benutzers in der lokalen Verwaltungskonsole zurück:**

1. Melden Sie sich bei der lokalen Verwaltungskonsole mit einem Benutzer mit der Rolle „Administrator“, „Support“ oder „CyberX“ an.

1. Wählen Sie **Benutzer** im linken Bereich aus.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="Wählen Sie im linken Bereich die Benutzeroption aus.":::

1. Suchen Sie den Benutzer, und wählen Sie das Bearbeitungssymbol :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: aus.

1. Geben Sie das neue Kennwort in die Felder **Neues Kennwort** und **Neues Kennwort bestätigen** ein.

    > [!NOTE]
    > Kennwörter müssen mindestens 16 Zeichen lang sein und klein und groß geschriebene alphabetische Zeichen, Zahlen und eines der folgenden Symbole enthalten: #%*+,-./:=?@[]^_{}~

1. Wählen Sie **Aktualisieren** aus.

## <a name="recover-the-password-for-the-on-premises-management-console-or-the-sensor"></a>Wiederherstellen des Kennworts für die lokale Verwaltungskonsole oder den Sensor

Sie können das Kennwort für die lokale Verwaltungskonsole oder den Sensor mit dem Feature zur Kennwortwiederherstellung wiederherstellen. Nur die Benutzer „CyberX“ und „Support“ besitzen Zugriff auf das Feature zur Kennwortwiederherstellung.

**So stellen Sie das Kennwort für die lokale Verwaltungskonsole oder den Sensor wieder her:**

1. Wählen Sie auf dem Anmeldebildschirm der lokalen Verwaltungskonsole oder des Sensors die Option **Password recovery** (Kennwortwiederherstellung) aus. Der Bildschirm **Password Recovery** (Kennwortwiederherstellung) wird geöffnet.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery.png" alt-text="Auswählen von „Password recovery“ (Kennwortwiederherstellung) auf dem Anmeldebildschirm der lokalen Verwaltungskonsole oder des Sensors":::

1. Wählen Sie im Dropdownmenü entweder **CyberX** oder **Support** aus, und kopieren Sie den Code für den eindeutigen Bezeichner.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery-screen.png" alt-text="Auswählen des Benutzers „CyberX“ oder des Benutzers „Support“ im Dropdownmenü":::

1. Navigieren Sie zum Azure-Portal, und wählen Sie **Sites und Sensoren** aus.  

1. Wählen Sie in der oberen Symbolleiste das Symbol **Abonnementfilter** :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false"::: aus, und wählen Sie das Abonnement aus, mit dem Ihr Sensor verbunden ist.

1. Wählen Sie das Dropdownmenü **Weitere Aktionen** und dann **Kennwort der lokalen Verwaltungskonsole wiederherstellen** aus.

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text="Auswählen Ihres Sensors und der Option „Kennwort der lokalen Verwaltungskonsole wiederherstellen“.":::   

1. Geben Sie den eindeutigen Bezeichner ein, der auf dem Bildschirm **Password Recovery** (Kennwortwiederherstellung) angezeigt wurde, und wählen Sie **Recover** (Wiederherstellen) aus. Die Datei `password_recovery.zip` wird heruntergeladen.

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="Angeben des eindeutigen Bezeichners und Auswählen von „Wiederherstellen“.":::

    > [!NOTE]
    > Ändern Sie die Kennwortwiederherstellungsdatei nicht. Es handelt sich um eine signierte Datei, die nicht funktioniert, wenn Sie sie ändern.

1. Wählen Sie auf dem Bildschirm Password Recovery (Kennwortwiederherstellung) die Option **Upload** (Hochladen) aus. Das Fenster **Upload Password Recovery File** (Kennwortwiederherstellungsdatei hochladen) wird geöffnet.

1. Wählen Sie **Durchsuchen** aus, um nach Ihrer Datei `password_recovery.zip` zu suchen, oder ziehen Sie die Datei `password_recovery.zip` in das Fenster.

    > [!NOTE]
    > Möglicherweise wird eine Fehlermeldung angezeigt, die besagt, dass die Datei ungültig ist. Um diese Fehlermeldung zu beheben, stellen Sie sicher, dass Sie das richtige Abonnement ausgewählt haben, bevor Sie die Datei `password_recovery.zip` herunterladen, und laden Sie sie dann erneut herunter.  

1. Wählen Sie **Weiter** aus. Ihr Benutzer und das vom System generierte Kennwort für Ihre Verwaltungskonsole werden angezeigt.

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren und Einrichten Ihres Sensors](how-to-activate-and-set-up-your-sensor.md)
[Aktivieren und Einrichten Ihrer lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md)
[Nachverfolgen der Sensoraktivität](how-to-track-sensor-activity.md)
