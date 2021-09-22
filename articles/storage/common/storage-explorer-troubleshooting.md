---
title: Azure Storage-Explorer – Leitfaden zur Problembehandlung | Microsoft-Dokumentation
description: Übersicht über Debugverfahren für Azure Storage-Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 9015e3ce69042a2e7d96588956becff889827ab3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225187"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage-Explorer – Leitfaden zur Problembehandlung

Microsoft Azure Storage-Explorer ist eine eigenständige App, mit der Sie unter Windows, macOS und Linux komfortabel mit Azure Storage-Daten arbeiten können. Die App kann mit Storage-Konten verbunden werden, die in Azure, nationalen Clouds und Azure Stack gehostet werden.

In diesem Leitfaden sind Lösungen für häufig aufgetretene Probleme im Storage-Explorer zusammengefasst.

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC-Berechtigungsprobleme

Die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, [Azure RBAC](../../role-based-access-control/overview.md)) ermöglicht eine hochgradig anpassbare Zugriffsverwaltung von Azure-Ressourcen, indem mehrere Berechtigungen in _Rollen_ kombiniert werden. Hier folgen einige Strategien, um Azure RBAC im Storage-Explorer optimal zu nutzen.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Wie kann ich im Storage-Explorer auf meine Ressourcen zugreifen?

Falls Sie Probleme beim Zugreifen auf Speicherressourcen per Azure RBAC haben, wurden Ihnen möglicherweise nicht die entsprechenden Rollen zugewiesen. In den folgenden Abschnitten werden die Berechtigungen beschrieben, die von Storage-Explorer derzeit zum Zugreifen auf Ihre Speicherressourcen benötigt werden. Wenden Sie sich an Ihren Azure-Kontoadministrator, falls Sie nicht sicher sind, ob Sie über die richtigen Rollen oder Berechtigungen verfügen.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>„Lesen: Auflisten/Abrufen von Speicherkonten“ – Berechtigungsproblem

Sie benötigen die Berechtigung zum Auflisten von Speicherkonten. Ihnen muss die Rolle _Leser_ zugewiesen sein, um diese Berechtigung zu erhalten.

#### <a name="list-storage-account-keys"></a>Auflisten von Speicherkontoschlüsseln

Für Storage-Explorer können auch Kontoschlüssel zum Authentifizieren von Anforderungen verwendet werden. Sie können Zugriff auf Kontoschlüssel über leistungsstärkere Rollen erhalten, z. B. über die Rolle _Mitwirkender_.

> [!NOTE]
> Mit Zugriffsschlüsseln werden für deren Besitzer uneingeschränkte Berechtigungen gewährt. Daher wird nicht empfohlen, diese Schlüssel an Kontobenutzer weiterzugeben. Wenn Sie Zugriffsschlüssel widerrufen müssen, können Sie sie über das [Azure-Portal](https://portal.azure.com/) neu generieren.

#### <a name="data-roles"></a>Datenrollen

Ihnen muss mindestens eine Rolle zugewiesen sein, über die Zugriff zum Lesen der Daten von Ressourcen gewährt wird. Wenn Sie beispielsweise Blobs auflisten oder herunterladen müssen, benötigen Sie mindestens die Rolle _Storage-Blobdatenleser_.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Warum benötige ich eine Rolle auf der Verwaltungsebene, damit meine Ressourcen in Storage-Explorer angezeigt werden?

Azure Storage verfügt über zwei Zugriffsebenen: _Verwaltung_ und _Daten_. Auf Abonnements und Speicherkonten wird über die Verwaltungsebene zugegriffen. Auf Container, Blobs und andere Datenressourcen wird über die Datenebene zugegriffen. Wenn Sie beispielsweise eine Liste mit Ihren Speicherkonten aus Azure abrufen möchten, senden Sie eine Anforderung an den Verwaltungsendpunkt. Wenn Sie eine Liste mit den Blobcontainern eines Kontos anzeigen möchten, können Sie eine Anforderung an den entsprechenden Dienstendpunkt senden.

Azure-Rollen können Berechtigungen für den Zugriff auf die Verwaltungs- oder Datenebene gewähren. Mit der Rolle „Leser“ wird beispielsweise Lesezugriff auf die Ressourcen der Verwaltungsebene gewährt.

Mit der Rolle „Leser“ werden genau genommen keine Berechtigungen für die Datenebene gewährt, und sie ist für den Zugriff darauf nicht erforderlich.

Storage-Explorer erleichtert Ihnen den Zugriff auf Ihre Ressourcen, indem die benötigten Informationen zum Herstellen einer Verbindung mit Ihren Azure-Ressourcen zusammengestellt werden. Zum Anzeigen Ihrer Blobcontainer sendet Storage-Explorer z. B. eine Anforderung zum Auflisten von Containern an den Blob-Dienstendpunkt. Um diesen Endpunkt zu erhalten, durchsucht Storage-Explorer die Liste mit den Abonnements und Speicherkonten, auf die Sie Zugriff haben. Zur Ermittlung Ihrer Abonnements und Speicherkonten benötigt Storage-Explorer auch Zugriff auf die Verwaltungsebene.

Wenn Sie nicht über eine Rolle verfügen, mit der Berechtigungen für die Verwaltungsebene gewährt werden, kann Storage-Explorer nicht die Informationen abrufen, die zum Herstellen der Verbindung mit der Datenebene benötigt werden.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wie kann ich vorgehen, wenn ich die benötigten Berechtigungen für die Verwaltungsebene nicht von meinem Administrator erhalten kann?

Wenn Sie auf Blobcontainer, ADLS Gen2-Container oder -Verzeichnisse oder aber Warteschlangen zugreifen möchten, können Sie diese Ressourcen unter Angabe Ihrer Azure-Anmeldeinformationen anfügen.

1. Öffnen Sie das Dialogfeld „Verbinden“.
1. Wählen Sie den Ressourcentyp aus, mit dem Sie eine Verbindung herstellen möchten.
1. Wählen Sie **Über Azure Active Directory anmelden (Azure AD)** aus. Wählen Sie **Weiter** aus.
1. Wählen Sie das Benutzerkonto und den Mandanten aus, die der Ressource zugeordnet sind, die angefügt werden soll. Wählen Sie **Weiter** aus.
1. Geben Sie die URL für die Ressource und dann einen eindeutigen Anzeigenamen für die Verbindung ein. Wählen Sie **Weiter** und dann **Verbinden** aus.

Für andere Ressourcentypen verfügen wir derzeit nicht über eine Azure RBAC-bezogene Lösung. Zur Umgehung dieses Problems können Sie eine SAS-URL anfordern und an Ihre Ressource anfügen, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie das Dialogfeld „Verbinden“.
1. Wählen Sie den Ressourcentyp aus, mit dem Sie eine Verbindung herstellen möchten.
1. Wählen Sie **Shared Access Signature (SAS)** aus. Wählen Sie **Weiter** aus.
1. Geben Sie die empfangene SAS-URL und dann einen eindeutigen Anzeigenamen für die Verbindung ein. Wählen Sie **Weiter** und dann **Verbinden** aus.
 
Weitere Informationen zum Anfügen an Ressourcen finden Sie unter [Anfügen an eine einzelne Ressource](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Empfohlene in Azure integrierte Rollen

Es gibt mehrere integrierte Azure-Rollen, die die Berechtigungen bereitstellen können, die für die Verwendung von Storage-Explorer erforderlich sind. Einige dieser Rollen sind:
- [Besitzer:](../../role-based-access-control/built-in-roles.md#owner) Sie können alles verwalten, einschließlich des Zugriffs auf Ressourcen.
- [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor): Sie können alles verwalten, ausgenommen den Zugriff auf Ressourcen.
- [Leser:](../../role-based-access-control/built-in-roles.md#reader) Lesen und Auflisten von Ressourcen.
- [Speicherkontomitwirkender:](../../role-based-access-control/built-in-roles.md#storage-account-contributor) Vollständige Verwaltung von Speicherkonten.
- [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Vollzugriff auf Azure Storage-Blobcontainer und -Daten.
- [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Lesen, Schreiben und Löschen von Azure Storage-Containern und -Blobs.
- [Leser von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Lesen und Auflisten von Azure Storage-Containern und -Blobs.

> [!NOTE]
> Die Rollen „Besitzer“, „Mitwirkender“ und „Speicherkontomitwirkender“ gewähren Zugriff auf den Kontoschlüssel.

## <a name="ssl-certificate-issues"></a>SSL-Zertifikatprobleme

### <a name="understanding-ssl-certificate-issues"></a>Grundlegendes zu SSL-Zertifikatproblemen

Machen Sie sich bei Bedarf zunächst mit dem [Abschnitt zu SSL-Zertifikaten](./storage-explorer-network.md#ssl-certificates) in der Netzwerkdokumentation für Storage-Explorer vertraut, bevor Sie fortfahren.

### <a name="use-system-proxy"></a>Verwenden eines Systemproxys

Wenn Sie nur Features verwenden, die die Einstellung **Systemproxy verwenden** unterstützen, versuchen Sie, diese Einstellung zu verwenden. Weitere Informationen zur Systemproxyeinstellung finden Sie [hier](./storage-explorer-network.md#use-system-proxy-preview).

### <a name="importing-ssl-certificates"></a>Importieren von SSL-Zertifikaten

Wenn Sie über eine Kopie der selbstsignierten Zertifikate verfügen, können Sie Storage-Explorer anweisen, sie als vertrauenswürdig zu behandeln. Führen Sie dazu die folgenden Schritte aus:

1. Rufen Sie eine X.509-Kopie (CER) des Zertifikats mit Base64-Verschlüsselung ab.
2. Wechseln Sie zu **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren**, suchen Sie über die Dateiauswahl die CER-Datei, wählen Sie sie aus, und öffnen Sie sie.

Dieses Problem kann auch auftreten, wenn es mehrere Zertifikate gibt (Stamm- und Zwischenzertifikat). Um diesen Fehler zu beheben, müssen alle Zertifikate importiert werden.

### <a name="finding-ssl-certificates"></a>Suchen nach SSL-Zertifikaten

Wenn Sie über keine Kopie der selbstsignierten Zertifikate verfügen, bitten Sie Ihren IT-Administrator um Unterstützung.

Sie können versuchen, sie mithilfe der folgenden Schritte zu finden:

1. Installieren Sie OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Eine Light-Version sollte ausreichend sein.
    * Mac und Linux: Sollte im Betriebssystem enthalten sein.
2. Führen Sie OpenSSL aus.
    * Windows: Öffnen Sie das Installationsverzeichnis, wählen Sie **/bin/** aus, und doppelklicken Sie dann auf **openssl.exe**.
    * Mac und Linux: Führen Sie `openssl` über ein Terminal aus.
3. Führen Sie den Befehl `s_client -showcerts -connect <hostname>:443` für einen der Microsoft- oder Azure-Hostnamen aus, hinter denen sich Ihre Speicherressourcen befinden. Hier finden Sie eine Liste mit Hostnamen, auf die häufig von Storage-Explorer zugegriffen wird.
4. Suchen Sie nach selbstsignierten Zertifikaten. Sind Antragsteller (`("s:")`) und Zertifikataussteller (`("i:")`) identisch, handelt es sich höchstwahrscheinlich um ein selbstsigniertes Zertifikat.
5. Wenn Sie die selbstsignierten Zertifikate gefunden haben, kopieren Sie jeweils den gesamten Inhalt von (einschließlich) `-----BEGIN CERTIFICATE-----` bis `-----END CERTIFICATE-----`, und fügen Sie ihn in eine neue CER-Datei ein.
6. Öffnen Sie Storage-Explorer, und navigieren Sie zu **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren**. Verwenden Sie dann die Dateiauswahl, um die von Ihnen erstellten CER-Dateien zu suchen, auszuwählen und zu öffnen.

### <a name="disabling-ssl-certificate-validation"></a>Deaktivieren der SSL-Zertifikatüberprüfung

Wenn Sie durch Befolgen dieser Schritte keine selbstsignierten Zertifikate finden können, wenden Sie sich über das Feedbacktool an uns. Sie können Storage-Explorer auch über die Befehlszeile öffnen. Verwenden Sie hierzu das Flag `--ignore-certificate-errors`. Wenn Storage-Explorer mit diesem Flag geöffnet wird, werden Zertifikatfehler ignoriert. **Dieses Flag wird nicht empfohlen.**

## <a name="sign-in-issues"></a>Probleme bei der Anmeldung

### <a name="understanding-sign-in"></a>Grundlegendes zur Anmeldung

Lesen Sie bei Bedarf zunächst die Dokumentation [Anmelden bei Storage-Explorer](./storage-explorer-sign-in.md), bevor Sie fortfahren.

### <a name="frequently-having-to-reenter-credentials"></a>Häufige Aufforderung zur erneuten Eingabe von Anmeldeinformationen

Eine Aufforderung zur erneuten Eingabe der Anmeldeinformationen ist häufig das Ergebnis von Richtlinien für bedingten Zugriff, die von Ihrem AAD-Administrator festgelegt wurden. Wenn Sie vom Storage-Explorer zur erneuten Eingabe der Anmeldeinformationen über den Kontobereich aufgefordert werden, sollte der Link **Fehlerdetails...** angezeigt werden. Klicken Sie auf den Link, um zu erfahren, warum Sie durch den Storage-Explorer zur erneuten Eingabe von Anmeldeinformationen aufgefordert werden. Fehler durch Richtlinien für bedingten Zugriff, die eine erneute Eingabe der Anmeldeinformationen erforderlich machen, können beispielsweise so aussehen:
- Das Aktualisierungstoken ist abgelaufen...
- Sie müssen für den Zugriff die mehrstufige Authentifizierung verwenden...
- Aufgrund einer Konfigurationsänderung Ihres Administrators...

Um die Häufigkeit der erneuten Eingabe von Anmeldeinformationen aufgrund von Fehlern wie den oben genannten zu reduzieren, müssen Sie mit Ihrem Azure AD-Administrator sprechen.

### <a name="conditional-access-policies"></a>Bedingte Zugriffsrichtlinien

Wenn für Ihr Konto Richtlinien für bedingten Zugriff gelten, stellen Sie sicher, dass Sie für die Einstellung **Anmelden mit** den Wert **Standardwebbrowser** verwenden. Informationen zu dieser Einstellung finden Sie unter [Ändern des Anmeldeorts](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="browser-complains-about-http-redirect-during-sign-in"></a>Browser gibt bei der Anmeldung eine Warnung zur HTTP-Umleitung aus

Wenn die Anmeldung beim Storage-Explorer über den Webbrowser durchgeführt wird, erfolgt am Ende des Anmeldevorgangs eine Umleitung an `localhost`. Browser geben gelegentlich eine Warnung oder einen Fehler dazu aus, dass die Umleitung mit HTTP anstelle von HTTPS ausgeführt wird. Einige Browser versuchen möglicherweise auch, eine Umleitung mit HTTPS zu erzwingen. In diesen Fällen haben Sie abhängig vom verwendeten Browser verschiedene Möglichkeiten:
- Ignorieren Sie die Warnung.
- Fügen Sie eine Ausnahme für `localhost` hinzu.
- Deaktivieren Sie das Erzwingen von HTTPS, entweder global oder nur für `localhost`.

Falls keine dieser Optionen verwendet werden kann, können Sie alternativ den [Anmeldeort ändern](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Token kann nicht abrufen werden, Mandant wurde herausgefiltert

Wenn Sie in einer Fehlermeldung darauf hingewiesen werden, dass kein Token abgerufen werden kann, weil ein Mandant herausgefiltert wurde, bedeutet das, dass Sie versuchen, auf einen Mandanten zuzugreifen, der aufgrund eines Filters nicht angezeigt wird. Wechseln Sie zum Aufheben des Filters für den Mandanten zum **Kontobereich**, und stellen Sie sicher, dass das Kontrollkästchen für den in der Fehlermeldung genannten Mandanten aktiviert ist. Weitere Informationen zum Filtern von Mandanten im Storage-Explorer finden Sie unter [Verwalten von Konten](./storage-explorer-sign-in.md#managing-accounts).

### <a name="authentication-library-failed-to-start-properly"></a>Authentifizierungsbibliothek kann nicht ordnungsgemäß gestartet werden

Wenn Sie beim Start in einer Fehlermeldung darauf hingewiesen werden, dass die Authentifizierungsbibliothek für den Storage-Explorer nicht ordnungsgemäß gestartet werden konnte, stellen Sie sicher, dass Ihre Installationsumgebung alle geltenden [Voraussetzungen](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites) erfüllt. Eine nicht erfüllte Voraussetzung ist die wahrscheinlichste Ursache für eine solche Fehlermeldung.

Wenn Sie der Meinung sind, dass Ihre Installationsumgebung alle Voraussetzungen erfüllt, [melden Sie ein Problem auf GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new). Schließen Sie beim Melden eines Problems die folgenden Informationen ein:
- Ihr Betriebssystem.
- Die von Ihnen verwendete Storage-Explorer-Version.
- Geben Sie an, ob die Voraussetzungen überprüft wurden.
- Die [Authentifizierungsprotokolle](#authentication-logs) nach einem nicht erfolgreichen Start von Storage-Explorer. Nach dem Auftreten eines Fehlers dieser Art wird automatisch die ausführliche Authentifizierungsprotokollierung aktiviert.

### <a name="blank-window-when-using-integrated-sign-in"></a>Leeres Fenster bei Verwendung der integrierten Anmeldung

Wenn Sie die **integrierte Anmeldung** verwenden und ein leeres Anmeldefenster angezeigt wird, müssen Sie wahrscheinlich zu einer anderen Anmeldemethode wechseln. Leere Anmeldedialogfelder treten am häufigsten auf, wenn ein Active Directory-Verbunddienste-Server (AD FS) Storage-Explorer auffordert, eine Umleitung durchzuführen, die von Electron nicht unterstützt wird.

Sie können zu einer anderen Anmeldemethode wechseln, indem Sie unter **Einstellungen** > **Anwendung** > **Anmeldung** die Einstellung **Anmelden mit** ändern. Informationen zu den verschiedenen Arten von Anmeldemethoden finden Sie unter [Ändern des Anmeldeorts](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="reauthentication-loop-or-upn-change"></a>Schleife für eine erneute Authentifizierung oder Änderung des UPN

Wenn Sie sich in einer Schleife für eine erneute Authentifizierung befinden oder den UPN für eines Ihrer Konten geändert haben, führen Sie diese Schritte aus:

1. Öffnen Sie den Storage-Explorer.
2. Navigieren Sie zu „Hilfe“ > „Zurücksetzen“.
3. Stellen Sie sicher, dass mindestens „Authentifizierung“ aktiviert ist. Sie können andere Elemente deaktivieren, die Sie nicht zurücksetzen möchten.
4. Klicken Sie auf die Schaltfläche „Zurücksetzen“.
5. Starten Sie den Storage-Explorer neu, und versuchen Sie, sich erneut anzumelden.

Falls nach dem Zurücksetzen weiterhin Probleme auftreten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie den Storage-Explorer.
2. Entfernen Sie alle Konten, und schließen Sie dann Storage-Explorer.
3. Löschen Sie den Ordner `.IdentityService` von Ihrem Computer. Der Ordner befindet sich unter Windows unter `C:\users\<username>\AppData\Local`. Bei Mac und Linux finden Sie den Ordner im Stammverzeichnis Ihres Benutzerverzeichnisses.
4. Wenn Sie Mac oder Linux verwenden, müssen Sie auch den Eintrag „Microsoft.Developer.IdentityService“ aus dem Keystore des Betriebssystems löschen. Unter Mac ist der Keystore die Anwendung *Gnome Keychain*. Unter Linux wird die Anwendung in der Regel als _Schlüsselbund_ bezeichnet, der Name kann jedoch abhängig von Ihrer Distribution abweichen.
6. Starten Sie den Storage-Explorer neu, und versuchen Sie, sich erneut anzumelden.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: Schlüsselbundfehler oder kein Anmeldefenster

Die macOS-Keychain kann manchmal in einem Zustand wechseln, der Probleme in Verbindung mit der Storage-Explorer-Authentifizierungsbibliothek verursacht. Führen Sie die folgenden Schritte durch, um diesen Zustand der Keychain aufzuheben:

1. Schließen Sie Storage Explorer.
2. Öffnen Sie die Keychain (drücken Sie BEFEHL+LEERTASTE, geben Sie **keychain** ein, und drücken Sie dann die EINGABETASTE).
3. Wählen Sie den Keychain-Wert „login“ (Anmelden) aus.
4. Wählen Sie das Schlosssymbol aus, um die Keychain zu sperren. (Das Schlosssymbol wird nach Abschluss des Vorgangs als verriegelt angezeigt. Es kann einige Sekunden dauern, je nachdem, welche Apps geöffnet sind).

    ![Schlosssymbol](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Öffnen Sie den Storage-Explorer.
6. Sie werden mit einer Nachricht wie „Servicehub möchte auf den Schlüsselbund zugreifen“ aufgefordert. Geben Sie das Kennwort Ihres Mac-Administratorkontos ein, und wählen Sie **Immer zulassen** (oder **Zulassen**, falls **Immer zulassen** nicht verfügbar ist) aus.
7. Versuchen Sie, sich anzumelden.

### <a name="default-browser-doesnt-open"></a>Standardbrowser wird nicht geöffnet

Wenn Ihr Standardbrowser beim Versuch einer Anmeldung nicht geöffnet wird, probieren Sie alle folgenden Maßnahmen aus:
- Neustart von Storage-Explorer
- Öffnen Sie Ihren Browser manuell, bevor Sie mit der Anmeldung beginnen.
- Versuchen Sie die **integrierte Anmeldung** zu verwenden. Anweisungen dazu finden Sie unter [Ändern des Anmeldeorts](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="other-sign-in-issues"></a>Andere Probleme bei der Anmeldung

Wenn keines der oben genannten Anmeldeprobleme vorliegt oder Sie Ihr Anmeldeproblem nicht beheben können, [melden Sie ein Problem auf GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Fehlende Abonnements und fehlerhafte Mandanten

Wenn Sie nach erfolgreicher Anmeldung Ihre Abonnements nicht abrufen können, probieren Sie die folgenden Methoden zur Problembehandlung:

* Überprüfen Sie, ob Ihr Konto Zugriff auf die erwarteten Abonnements hat. Sie können Ihren Zugriff überprüfen, indem Sie sich am Portal für die Azure-Umgebung anmelden, die Sie verwenden möchten.
* Stellen Sie sicher, dass die Anmeldung mit der richtigen Azure-Umgebung (Azure, Azure China 21Vianet, Azure Deutschland, Azure US Government oder Benutzerdefinierte Umgebung) erfolgt ist.
* Wenn Sie sich hinter einem Proxyserver befinden, sollten Sie sicherstellen, dass Sie den Storage-Explorer-Proxy richtig konfiguriert haben.
* Entfernen Sie das Konto, und fügen Sie es wieder hinzu.
* Wenn ein Link mit der Beschriftung „Weitere Informationen“ oder „Fehlerdetails“ angezeigt wird, prüfen Sie, welche Fehlermeldungen für die fehlerhaften Mandanten gemeldet werden. Wenn Sie nicht sicher sind, wie Sie auf die Fehlermeldungen reagieren sollen, können Sie [ein Problem in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues) öffnen.

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Angefügtes Konto oder angefügte Ressource kann nicht entfernt werden

Wenn ein zugeordnetes Konto oder eine verbundene Speicherressource über die Benutzeroberfläche nicht entfernt werden kann, können Sie alle zugeordneten Ressourcen manuell löschen, indem Sie die folgenden Ordner löschen:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Schließen Sie Storage-Explorer, bevor Sie diese Ordner löschen.

> [!NOTE]
> Falls Sie irgendwann SSL-Zertifikate importiert haben, sichern Sie den Inhalt des Verzeichnisses `certs`. Anschließend können Sie mithilfe der Sicherung die SSL-Zertifikate wieder importieren.

## <a name="proxy-issues"></a>Proxyprobleme

Storage-Explorer unterstützt das Herstellen einer Verbindung mit Azure Storage-Ressourcen über einen Proxyserver. Wenn beim Herstellen einer Verbindung mit Azure über einen Proxy Probleme auftreten, finden Sie hier einige Vorschläge.

> [!NOTE]
> Storage-Explorer unterstützt nur die Standardauthentifizierung mit Proxyservern. Andere Authentifizierungsmethoden wie NTLM werden nicht unterstützt.

> [!NOTE]
> Storage-Explorer unterstützt keine Dateien für die automatische Proxykonfiguration, die zum Konfigurieren von Proxyeinstellungen verwendet werden können.

### <a name="verify-storage-explorer-proxy-settings"></a>Überprüfen der Storage-Explorer-Proxyeinstellungen

Die Einstellung **Anwendung → Proxy → Proxykonfiguration** bestimmt, von welcher Quelle Storage-Explorer die Proxykonfiguration abruft.

Wenn Sie „Umgebungsvariablen verwenden“ auswählen, stellen Sie sicher, dass Sie die Umgebungsvariable `HTTPS_PROXY` oder `HTTP_PROXY` festlegen (bei Umgebungsvariablen wird die Groß-/Kleinschreibung berücksichtigt, achten Sie also darauf, dass Sie die richtigen Variablen festlegen). Wenn diese Variablen nicht definiert oder ungültig sind, verwendet Storage-Explorer keinen Proxy. Starten Sie Storage-Explorer neu, nachdem Sie Umgebungsvariablen geändert haben.

Wenn Sie „App-Proxyeinstellungen verwenden“ auswählen, stellen Sie sicher, dass die In-App-Proxyeinstellungen richtig sind.

### <a name="steps-for-diagnosing-issues"></a>Schritte zur Diagnose von Problemen

Wenn weiterhin Probleme auftreten, probieren Sie die folgenden Methoden zur Problembehandlung:

1. Wenn Sie ohne Verwendung des Proxys eine Verbindung mit dem Internet herstellen können, überprüfen Sie, ob der Storage-Explorer ohne aktivierte Proxyeinstellungen ausgeführt wird. Wenn Storage-Explorer erfolgreich eine Verbindung herstellen kann, liegt möglicherweise ein Problem mit dem Proxyserver vor. Identifizieren Sie die Probleme zusammen mit dem Administrator.
2. Überprüfen Sie, ob andere Anwendungen, die den Proxyserver verwenden, wie erwartet ausgeführt werden.
3. Prüfen Sie, ob Sie sich für die Azure-Umgebung, die Sie verwenden möchten, am Portal anmelden können.
4. Vergewissern Sie sich, dass Antworten von den Dienstendpunkten empfangen werden können. Geben Sie eine der Endpunkt-URLs im Browser ein. Wenn Sie eine Verbindung herstellen können, sollten Sie eine XML-Antwort wie `InvalidQueryParameterValue` erhalten.
5. Prüfen Sie, ob eine andere Person, die Storage-Explorer mit demselben Proxyserver verwendet, eine Verbindung herstellen kann. Wenn dies der Fall ist, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden.

### <a name="tools-for-diagnosing-issues"></a>Tools zum Diagnostizieren von Problemen

Ein Netzwerktool wie Fiddler kann Ihnen dabei helfen, Probleme zu diagnostizieren.

1. Konfigurieren Sie das Netzwerktool als Proxyserver, der auf dem lokalen Host ausgeführt wird. Wenn Sie weiterhin hinter einem echten Proxy arbeiten müssen, müssen Sie das Netzwerktool möglicherweise so konfigurieren, dass eine Verbindung über den Proxy hergestellt wird.
2. Überprüfen Sie die vom Netzwerktool verwendete Portnummer.
3. Konfigurieren Sie die Proxyeinstellungen für Storage-Explorer so, dass der lokale Host und die Portnummer des Netzwerktools (z. B. „localhost:8888“) verwendet wird.
 
Bei korrekter Konfiguration protokolliert das Netzwerktool die vom Storage-Explorer an Verwaltungs- und Dienstendpunkte gesendeten Netzwerkanforderungen.
 
Wenn das Netzwerktool Storage-Explorer-Datenverkehr anscheinend nicht protokolliert, testen Sie das Tool mit einer anderen Anwendung. Geben Sie z. B. die Endpunkt-URL für eine Ihrer Speicherressourcen (z. B. `https://contoso.blob.core.windows.net/`) in einem Webbrowser ein. Sie erhalten dann eine Antwort wie die folgende:

  ![Codebeispiel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Die Antwort deutet darauf hin, dass die Ressource vorhanden ist, auch wenn Sie nicht darauf zugreifen können.

Wenn Ihr Netzwerktool nur Datenverkehr von anderen Anwendungen anzeigt, müssen Sie möglicherweise die Proxyeinstellungen in Storage-Explorer anpassen. Andernfalls müssen Sie die Einstellungen Ihres Tools anpassen.

### <a name="contact-proxy-server-admin"></a>Kontaktaufnahme mit dem Administrator für den Proxyserver

Wenn Ihre Proxyeinstellungen richtig sind, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden, um Folgendes zu erreichen:

* Sicherstellen, dass der Proxy nicht den Datenverkehr zu Azure Verwaltungs- oder Ressourcenendpunkten blockiert.
* das von Ihrem Proxyserver verwendete Authentifizierungsprotokoll überprüfen. Storage-Explorer unterstützt nur Standardauthentifizierungsprotokolle. NTLM-Proxys werden vom Storage-Explorer nicht unterstützt.

## <a name="unable-to-retrieve-children-error-message"></a>Fehlermeldung: Untergeordnete Elemente können nicht abgerufen werden

Wenn Sie die Verbindung mit Azure über einen Proxy hergestellt haben, überprüfen Sie, ob die Proxyeinstellungen richtig sind.

Wenn Ihnen vom Besitzer des Abonnements oder Kontos Zugriff auf eine Ressource gewährt wurde, vergewissern Sie sich, dass Sie über die Berechtigung zum Lesen oder Auflisten für diese Ressource verfügen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Verbindungszeichenfolge weist keine vollständigen Konfigurationseinstellungen auf

Wenn Sie diese Fehlermeldung erhalten, verfügen Sie möglicherweise nicht über die erforderlichen Berechtigungen zum Abrufen der Schlüssel für Ihr Speicherkonto. Um zu überprüfen, ob dies der Fall ist, navigieren Sie zum Portal, und suchen Sie Ihr Speicherkonto. Klicken Sie hierzu einfach mit der rechten Maustaste auf den Knoten für Ihr Speicherkonto, und wählen Sie dann **Im Portal öffnen** aus. Wechseln Sie dann zum Blatt **Zugriffsschlüssel**. Wenn Sie nicht über die Berechtigung zum Anzeigen von Schlüsseln verfügen, wird die Meldung „Sie haben keinen Zugriff“ angezeigt. Um dieses Problem zu umgehen, können Sie den Kontoschlüssel von einer anderen Person erhalten und mit Name und Schlüssel anfügen, oder Sie können eine andere Person um die SAS für das Speicherkonto bitten und mit dieser das Speicherkonto anfügen.

Wenn die Kontoschlüssel angezeigt werden, ist es ratsam, ein Problem in GitHub zu melden, damit wir Ihnen beim Beheben des Problems helfen können.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Fehler beim Hinzufügen einer neuen Verbindung aufgetreten: TypeError: Eigenschaft 'version' von undefiniert kann nicht gelesen werden

Wenn Sie diese Fehlermeldung erhalten, wenn Sie versuchen, eine benutzerdefinierte Verbindung hinzuzufügen, können die Verbindungsdaten, die in der lokalen Verwaltung für Anmeldeinformationen gespeichert sind, beschädigt sein. Versuchen Sie, Ihre beschädigten lokalen Verbindungen zu löschen und sie dann erneut hinzuzufügen, um dieses Problem zu umgehen:

1. Starten Sie den Storage-Explorer. Wechseln Sie im Menü zu **Hilfe** > **Entwicklertools umschalten**.
2. Wechseln Sie im geöffneten Fenster auf der Registerkarte **Anwendung** zu **Lokaler Speicher** (linke Seite) > **file://** .
3. Abhängig von der Art der Verbindung, bei der ein Problem auftritt, suchen Sie nach dem Schlüssel, und kopieren Sie dann den Wert in einen Text-Editor. Der Wert ist ein Array Ihrer benutzerdefinierten Verbindungsnamen, wie die folgenden:
    * Speicherkonten
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blobcontainer
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Dateifreigaben
        * `StorageExplorer_CustomConnections_Files_v1`
    * Warteschlangen
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabellen
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Nachdem Sie Ihre aktuellen Verbindungsnamen gespeichert haben, legen Sie den Wert in den Entwicklertools auf `[]` fest.

Wenn Sie die nicht beschädigten Verbindungen beibehalten möchten, können Sie die folgenden Schritte ausführen, um die beschädigten Verbindungen zu finden. Wenn es Ihnen nichts ausmacht, alle bestehenden Verbindungen zu verlieren, können Sie diese Schritte überspringen und den plattformspezifischen Anweisungen folgen, um Ihre Verbindungsdaten zu löschen.

1. Fügen Sie in einem Text-Editor jeden Verbindungsnamen erneut den Entwicklertools hinzu, und überprüfen Sie dann, ob die Verbindung weiterhin funktioniert.
2. Wenn eine Verbindung ordnungsgemäß funktioniert, ist sie nicht beschädigt und Sie können sie dort sicher belassen. Wenn eine Verbindung nicht funktioniert, entfernen Sie den Wert aus den Entwicklertools und zeichnen Sie ihn auf, damit Sie ihn später wieder hinzufügen können.
3. Wiederholen Sie den Vorgang, bis Sie alle Ihre Verbindungen überprüft haben.

Nachdem Sie alle Ihre Verbindungen überprüft haben, müssen Sie für alle Verbindungsnamen, die nicht zurückgesetzt werden, ihre beschädigten Daten löschen (falls vorhanden) und sie mit den Standardschritten in Storage-Explorer zurücksetzen:

### <a name="windows"></a>[Windows](#tab/Windows)

1. Suchen Sie im Menü **Start** nach der **Anmeldeinformationsverwaltung** und öffnen Sie sie.
2. Wechseln Sie zu **Windows-Anmeldeinformationen**.
3. Suchen Sie unter **Generische Anmeldeinformationen** nach Einträgen, die den Schlüssel `<connection_type_key>/<corrupted_connection_name>` aufweisen (z. B. `StorageExplorer_CustomConnections_Accounts_v1/account1`).
4. Löschen Sie diese Einträge, und fügen Sie die Verbindungen erneut hinzu.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Öffnen Sie Spotlight (BEFEHL+LEERTASTE), und suchen Sie nach **Schlüsselbundverwaltung (Keychain Access)** .
2. Suchen Sie nach Einträgen, die den Schlüssel `<connection_type_key>/<corrupted_connection_name>` aufweisen (z. B. `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Löschen Sie diese Einträge, und fügen Sie die Verbindungen erneut hinzu.

### <a name="linux"></a>[Linux](#tab/Linux)

Die lokale Anmeldeinformationsverwaltung variiert je nach Linux-Distribution. Wenn Ihre Linux-Distribution kein integriertes GUI-Tool für die lokale Anmeldeinformationsverwaltung enthält, können Sie ein Drittanbietertool zur Verwaltung Ihrer lokalen Anmeldeinformationen installieren. Sie können z. B. [Seahorse](https://wiki.gnome.org/Apps/Seahorse/) verwenden, ein Open-Source-GUI-Tool zur Verwaltung lokaler Anmeldeinformationen für Linux.

1. Öffnen Sie Ihr lokales Tool zur Verwaltung von Anmeldeinformationen, und suchen Sie nach Ihren gespeicherten Anmeldeinformationen.
2. Suchen Sie nach Einträgen, die den Schlüssel `<connection_type_key>/<corrupted_connection_name>` aufweisen (z. B. `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Löschen Sie diese Einträge, und fügen Sie die Verbindungen erneut hinzu.
---

Wenn der Fehler nach Ausführung dieser Schritte weiterhin auftritt oder wenn Sie mitteilen möchten, was Ihrer Meinung nach die Verbindungen beschädigt hat, [öffnen Sie ein Problem](https://github.com/microsoft/AzureStorageExplorer/issues) auf unserer GitHub-Seite.

## <a name="issues-with-sas-url"></a>Probleme mit der SAS-URL

Wenn Sie eine Verbindung mit einem Dienst über eine SAS-URL herstellen und ein Fehler auftritt:

* Stellen Sie sicher, dass die URL die erforderlichen Berechtigungen zum Lesen oder Auflisten von Ressourcen umfasst.
* Vergewissern Sie sich, dass die URL nicht abgelaufen ist.
* Wenn die SAS-URL auf einer Zugriffsrichtlinie basiert, stellen Sie sicher, dass die Zugriffsrichtlinie nicht aufgehoben wurde.

Wenn Sie versehentlich eine ungültige SAS-URL angefügt haben und diese jetzt nicht trennen können, führen Sie diese Schritte aus:

1. Drücken Sie in Storage-Explorer F12, um das Fenster mit den Entwicklertools zu öffnen.
2. Wählen Sie auf der Registerkarte **Anwendung** die Option **Lokaler Speicher** > **file://** in der Struktur auf der linken Seite aus.
3. Suchen Sie den Schlüssel, der dem Diensttyp des problematischen SAS-URI zugeordnet ist. Wenn z.B. der fehlerhafte SAS-URI für einen Blobcontainer gilt, suchen Sie nach dem Schlüssel mit dem Namen `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Der Wert des Schlüssels sollte ein JSON-Array sein. Suchen Sie das Objekt, das dem fehlerhaften URI zugeordnet ist, und löschen Sie es anschließend.
5. Drücken Sie STRG+R, um den Storage-Explorer neu zu laden.

## <a name="linux-dependencies"></a>Linux-Abhängigkeiten

### <a name="snap"></a>Snap

Storage-Explorer 1.10.0 und höher ist als Snap im Snap Store verfügbar. Das Storage-Explorer-Snap installiert alle seine Abhängigkeiten automatisch, und es wird aktualisiert, wenn eine neue Version des Snaps verfügbar ist. Die Installation des Storage-Explorer-Snaps ist die empfohlene Installationsmethode.

Storage-Explorer erfordert den Einsatz eines Kennwort-Managers, mit dem Sie möglicherweise manuell eine Verbindung herstellen müssen, damit Storage-Explorer ordnungsgemäß funktioniert. Sie können Storage-Explorer mit dem Kennwort-Manager Ihres Systems verbinden, indem Sie den folgenden Befehl ausführen:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>.tar.gz-Datei

Sie können die Anwendung auch als „.tar.gz“-Datei herunterladen, aber Sie müssen die Abhängigkeiten manuell installieren.

Storage-Explorer, wie im „.tar. gz“-Download bereitgestellt, wird nur für die folgenden Versionen von Ubuntu unterstützt. Storage-Explorer funktioniert möglicherweise mit anderen Linux-Distributionen, diese werden aber nicht offiziell unterstützt.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Für Storage-Explorer muss .NET Core auf dem System installiert sein. Wir empfehlen .NET Core 2.1, aber Storage-Explorer funktioniert auch mit Version 2.2.

> [!NOTE]
> Storage-Explorer bis Version 1.7.0 erfordert .NET Core 2.0. Wenn Sie bereits eine neuere Version von .NET Core installiert haben, müssen Sie [Storage-Explorer mit Patches versehen](#patching-storage-explorer-for-newer-versions-of-net-core). Wenn Sie Storage-Explorer 1.8.0 oder höher ausführen, benötigen Sie mindestens .NET Core 2.1.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04 ](#tab/2004)

1. Laden Sie die Storage-Explorer- Datei „.tar.gz“ herunter.
2. Installieren Sie die [.NET Core-Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Laden Sie die Storage-Explorer- Datei „.tar.gz“ herunter.
2. Installieren Sie die [.NET Core-Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Laden Sie die Storage-Explorer- Datei „.tar.gz“ herunter.
2. Installieren Sie die [.NET Core-Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Viele Bibliotheken, die von Storage-Explorer benötigt werden, werden mit den Standardinstallationen von Ubuntu von Canonical vorinstalliert. In benutzerdefinierten Umgebungen fehlen möglicherweise einige dieser Bibliotheken. Wenn beim Starten von Storage-Explorer Probleme auftreten, sollten Sie sicherstellen, dass die folgenden Pakete auf dem System installiert sind:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Versehen von Storage-Explorer mit Patches für neuere Versionen von .NET Core

Bei Storage-Explorer 1.7.0 oder früher müssen Sie möglicherweise die von Storage-Explorer verwendete .NET Core-Version mit Patches versehen.

1. Laden Sie Version 1.5.43 von StreamJsonRpc [über NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) herunter. Suchen Sie rechts auf der Seite nach dem Link zum Herunterladen des Pakets („Download package“).
2. Ändern Sie nach dem Herunterladen des Pakets die Dateierweiterung von `.nupkg` in `.zip`.
3. Entzippen Sie das Paket.
4. Öffnen Sie den Ordner `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopieren Sie `StreamJsonRpc.dll` an die folgenden Speicherorte im Storage-Explorer-Ordner:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>„In Explorer öffnen“ über das Azure-Portal funktioniert nicht

Wenn die Schaltfläche **In Explorer öffnen** im Azure-Portal nicht funktioniert, sollten Sie sicherstellen, dass Sie einen kompatiblen Browser verwenden. Für die folgenden Browser wurde die Kompatibilität getestet:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Sammeln von Protokollen

Wenn Sie ein Problem an GitHub melden, werden Sie möglicherweise aufgefordert, bestimmte Protokolle zu sammeln, um Ihr Problem zu diagnostizieren.

### <a name="storage-explorer-logs"></a>Storage-Explorer-Protokolle

Ab Version 1.16.0 protokolliert der Storage-Explorer verschiedene Dinge in ihren eigenen Anwendungsprotokollen. Sie können auf einfache Weise zu diesen Protokollen kommen, indem Sie auf „Hilfe > Protokollverzeichnis öffnen“ klicken. Standardmäßig werden Storage-Explorer-Protokolle mit einem geringen Ausführlichkeitsgrad erfasst. Um den Ausführlichkeitsgrad zu ändern, fügen Sie eine Umgebungsvariable mit dem Namen `STG_EX_LOG_LEVEL` und einen der folgenden Werte hinzu:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (Standardebene)
- `verbose`
- `debug`

Protokolle werden für jede ausgeführte Sitzung des Storage-Explorers in Ordner aufgeteilt. Es wird empfohlen, alle Protokolldateien, die Sie bereitstellen möchten, in einem ZIP-Archiv zusammenzufassen, wobei die Dateien aus verschiedenen Sitzungen in unterschiedlichen Ordnern abgelegt werden.

### <a name="authentication-logs"></a>Authentifizierungsprotokolle

Bei Problemen im Zusammenhang mit der Anmeldung oder Storage-Explorer-Authentifizierungsbibliothek müssen Sie höchstwahrscheinlich Authentifizierungsprotokolle sammeln. Authentifizierungsprotokolle werden hier gespeichert:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS und Linux `~/.ServiceHub/logs`

Im Allgemeinen können Sie die folgenden Schritte ausführen, um die Protokolle zu erfassen:

1. Wechseln Sie zu **Einstellungen (Zahnradsymbol links)**  > **Anwendungen** > **Anmeldung**, und aktivieren Sie die Option **Ausführliche Authentifizierungsprotokollierung**. Wenn der Storage-Explorer aufgrund eines Problems mit der Authentifizierungsbibliothek nicht gestartet werden kann, wird dies für Sie durchgeführt.
2. Schließen Sie Storage Explorer.
1. Optional/empfohlen: Löschen Sie vorhandene Protokolle aus dem Ordner `logs`. Dadurch wird die Menge an Informationen reduziert, die Sie uns senden müssen.
4. Öffnen Sie den Storage-Explorer, und reproduzieren Sie das Problem.
5. Schließen Sie den Storage-Explorer.
6. Zippen Sie den Inhalt des Ordners `logs`.

### <a name="azcopy-logs"></a>AzCopy-Protokolle

Wenn Sie Probleme beim Übertragen von Daten haben, müssen Sie möglicherweise die AzCopy-Protokolle abrufen. AzCopy-Protokolle können problemlos mit zwei verschiedenen Methoden gefunden werden:
- Für fehlgeschlagene Übertragungen, die sich noch im Aktivitätsprotokoll finden, klicken Sie auf „Zur AzCopy-Protokolldatei wechseln“.
- Wechseln Sie für Übertragungen, bei denen in der Vergangenheit Fehler aufgetreten sind, zum Ordner „AzCopy-Protokolle“. Dieser Ordner befindet sich unter:
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS und Linux `~/.azcopy

### <a name="network-logs"></a>Netzwerkprotokolle

Bei einigen Problemen müssen Sie Protokolle der Netzwerkaufrufe bereitstellen, die von Storage-Explorer durchgeführt werden. Unter Windows können Sie dazu Fiddler verwenden.

> [!NOTE]
> Fiddler-Ablaufverfolgungen können Kennwörter enthalten, die Sie während der Erfassung der Ablaufverfolgung in Ihrem Browser eingegeben/gesendet haben. Lesen Sie unbedingt die Anweisungen zum Bereinigen einer Fiddler-Ablaufverfolgung. Laden Sie keine Fiddler-Ablaufverfolgungen auf GitHub hoch. Sie werden informiert, wohin Sie Ihre Fiddler-Ablaufverfolgung sicher senden können.

Teil 1: Installieren und Konfigurieren von Fiddler

1. Installieren Sie Fiddler.
2. Starten Sie Fiddler.
3. Klicken Sie auf „Tools > Optionen“.
4. Klicken Sie auf die Registerkarte „HTTPS“.
5. Stellen Sie sicher, dass „Capture CONNECTs“ (CONNECTs erfassen) und „Decrypt HTTPS traffic“ (HTTPS-Datenverkehr entschlüsseln) aktiviert sind.
6. Klicken Sie auf die Schaltfläche „Actions“ (Aktionen).
7. Wählen Sie im nächsten Dialogfeld „Trust Root Certificate“ (Stammzertifikat vertrauen) und dann „Yes“ (Ja) aus.
8. Klicken Sie noch mal auf die Schaltfläche „Actions“ (Aktionen).
9. Wählen Sie „Export Root Certificate to Desktop“ (Stammzertifikat nach Desktop exportieren) aus.
10. Wechseln Sie zu Ihrem Desktop.
11. Suchen Sie die Datei „FiddlerRoot.cer“.
12. Doppelklick Sie auf die Datei, um sie zu öffnen.
13. Navigieren Sie zur Registerkarte „Details“.
14. Klicken Sie auf „In Datei kopieren...“.
15. Wählen Sie im Export-Assistenten die folgenden Optionen aus:
    - Base64-codiertes X.509
    - Für den Dateinamen klicken Sie auf „Durchsuchen...“ Speichern Sie das Zertifikat in `C:\Users\<your user dir>\AppData\Roaming\StorageExplorer\certs` unter einem beliebigen Dateinamen.
16. Schließen Sie das Fenster „Zertifikat“.
17. Starten Sie den Storage-Explorer.
18. Wechseln Sie zu „Bearbeiten > Proxy konfigurieren“.
19. Wählen Sie im Dialogfeld „App-Proxyeinstellungen verwenden“ aus, und legen Sie die URL auf http://localhost und den Port auf 8888 fest.
20. Klicken Sie auf OK.
21. Neustart von Storage-Explorer
22. In Fiddler sollten Netzwerkaufrufe aus einem `storageexplorer:`-Prozess angezeigt werden.

Teil 2: Reproduzieren des Problems
1. Schließen Sie alle Apps bis auf Fiddler.
2. Löschen Sie das Fiddler-Protokoll (X-Symbol oben links in der Nähe des Menüs „Ansicht“).
3. Optional/empfohlen: Lassen Sie Fiddler sich einige Minuten einrichten. Wenn Netzwerkaufrufe angezeigt werden, klicken Sie mit der rechten Maustaste darauf, und wählen Sie „Jetzt filtern“ > „<process name> ausblenden“ aus.
4. Starten Sie den Storage-Explorer.
5. Reproduzieren Sie das Problem.
6. Klicken Sie auf „Datei > Speichern > Alle Sitzungen...“, und speichern Sie diese an einem Ort, den Sie sich gut merken können.
7. Schließen Sie Fiddler und den Storage-Explorer.

Teil 3: Bereinigen der Fiddler-Ablaufverfolgung
1. Doppelklicken Sie auf die Fiddler-Ablaufverfolgung (SAZ-Datei).
2. Drücken Sie `ctrl`+`f`.
3. Stellen Sie sich, dass im angezeigten Dialogfeld die folgenden Optionen festgelegt sind: Suchen = Anforderungen und Antworten, Untersuchen = Header und Textkörper
4. Suchen Sie nach allen Kennwörtern, die Sie beim Erfassen der Fiddler-Ablaufverfolgung verwendet haben, sowie nach hervorgehobenen Einträgen. Klicken Sie mit der rechten Maustaste, und wählen Sie „Entfernen > Ausgewählte Sitzungen“ aus.
5. Wenn Sie beim Erfassen der Ablaufverfolgung definitiv Kennwörter in Ihren Browser eingegeben haben, aber mithilfe von STRG+F keine Einträge finden und Ihre Kennwörter nicht ändern möchten bzw. die verwendeten Kennwörter für andere Konten verwendet werden, können Sie das Senden der SAZ-Datei überspringen. Vorsicht ist besser als Nachsicht. :)
6. Speichern Sie die Ablaufverfolgung noch mal unter einem neuen Namen.
7. Optional: Löschen Sie die ursprüngliche Ablaufverfolgung.

## <a name="next-steps"></a>Nächste Schritte

Wenn keine dieser Maßnahmen zur Problemlösung führt, haben Sie folgende Möglichkeiten:
- Erstellen ein Supporttickets
- [Melden Sie ein Problem auf GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Sie können dazu auch die Schaltfläche **Problem auf GitHub melden** in der linken unteren Ecke auswählen.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
