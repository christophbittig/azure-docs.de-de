---
title: Herstellen einer Verbindung mit dem SFTP-Server mit SSH
description: Automatisieren von Aufgaben, die Dateien auf einem SFTP-Server mithilfe von SSH und Azure Logic Apps überwachen, erstellen, verwalten, senden und empfangen
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 08/05/2021
tags: connectors
ms.openlocfilehash: 32638d71f2c700700be5eb1b2ad63f18969d82a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340216"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>Erstellen und Verwalten von SFTP-Dateien mithilfe von SSH und Azure Logic Apps

Um Aufgaben zu automatisieren, die Dateien auf einem [Secure File Transfer Protocol](https://www.ssh.com/ssh/sftp/)-Server (SFTP) mit dem [Secure Shell](https://www.ssh.com/ssh/protocol/)-Protokoll (SSH) erstellen und verwalten, können Sie mithilfe von Azure Logic Apps und dem SFTP-SSH-Connector automatisierte Integrationsworkflows erstellen. SFTP ist ein Netzwerkprotokoll, das Dateizugriff, Dateiübertragung und Dateiverwaltung über jeden beliebigen zuverlässigen Datenstrom ermöglicht.

Im Folgenden finden Sie einige Beispielaufgaben, die automatisiert werden können:

* Sie können die Vorgänge überwachen, wenn Dateien hinzugefügt oder geändert werden.
* Sie können Dateien abrufen, erstellen, kopieren, umbenennen, aktualisieren, auflisten und löschen.
* Sie können Ordner erstellen.
* Sie können Dateiinhalte und Metadaten abrufen.
* Sie können Archive in Ordner extrahieren.

In Ihrem Workflow können Sie einen Trigger verwenden, der Ereignisse auf dem SFTP-Server überwacht und die Ausgabe für andere Aktionen verfügbar macht. Sie können dann Aktionen verwenden, um verschiedene Aufgaben auf Ihrem SFTP-Server auszuführen. Sie können auch andere Aktionen einbeziehen, die die Ausgabe von SFTP-SSH-Aktionen verwenden. Wenn Sie beispielsweise regelmäßig Dateien von Ihrem SFTP-Server abrufen, können Sie mithilfe des Office 365 Outlook-Connectors oder des Outlook.com-Connectors E-Mail-Benachrichtigungen zu diesen Dateien und ihren Inhalten senden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

Weitere Unterschiede zwischen dem SFTP-SSH-Connector und dem SFTP-Connector finden Sie unter [Vergleichen von SFTP-SSH und SFTP](#comparison) weiter unten in diesem Thema.

## <a name="limits"></a>Einschränkungen

* Der SFTP-SSH-Connector unterstützt die folgenden SFTP-Server derzeit nicht:

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* SFTP-SSH-Aktionen, die [Blockerstellung](../logic-apps/logic-apps-handle-large-messages.md) unterstützen, können Dateien bis zu einer Größe von 1 GB verarbeiten, während SFTP-SSH-Aktionen, die keine Blockerstellung unterstützen, Dateien bis zu einer Größe von 50 MB verarbeiten können. Die Standardblockgröße beträgt 15 MB. Diese Größe kann sich jedoch dynamisch ändern, bei 5 MB beginnend und allmählich auf den maximalen Wert von 50 MB ansteigend. Die dynamische Größenanpassung basiert auf Faktoren wie Netzwerklatenz, Serverantwortzeit usw.

  > [!NOTE]
  > Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erfordert die mit ISE bezeichnete Version dieses Connectors Chunking, um stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) zu verwenden.

  Sie können dieses adaptive Verhalten außer Kraft setzen, indem Sie eine [konstante Blockgröße angeben](#change-chunk-size), die stattdessen verwendet werden soll. Diese Größe kann zwischen 5 MB und 50 MB liegen. Nehmen Sie beispielsweise an, Sie verfügen über eine 45 MB große Datei und ein Netzwerk, das diese Dateigröße ohne Latenz unterstützt. Die adaptive Segmentierung führt zu mehreren Aufrufen anstelle von einem Aufruf. Um die Anzahl von Aufrufen zu reduzieren, können Sie versuchen, die Blockgröße auf 50 MB festzulegen. Wenn in einem anderen Szenario mit einer Blockgröße von beispielsweise 15 MB ein Timeout für Ihre Logik-App auftritt, können Sie versuchen, die Größe auf 5 MB zu reduzieren.

  Blockgröße ist einer Verbindung zugeordnet. Dieses Attribut bedeutet, dass Sie dieselbe Verbindung sowohl für Aktionen verwenden können, die Blockerstellung unterstützen, als auch für Aktionen, die Blockerstellung nicht unterstützen. In diesem Fall liegt die Blockgröße für Aktionen, die keine Blockerstellung unterstützen, zwischen 5 MB und 50 MB. In dieser Tabelle sehen Sie, welche SFTP-SSH-Aktionen Blockerstellung unterstützen:

  | Aktion | Unterstützung für Blockerstellung | Unterstützung für die Außerkraftsetzung der Blockgröße |
  |--------|------------------|-----------------------------|
  | **Datei kopieren** | Nein | Nicht zutreffend |
  | **Datei erstellen** | Ja | Ja |
  | **Ordner erstellen** | Nicht verfügbar | Nicht verfügbar |
  | **Datei löschen** | Nicht verfügbar | Nicht verfügbar |
  | **Archiv in Ordner extrahieren** | Nicht verfügbar | Nicht verfügbar |
  | **Dateiinhalte abrufen** | Ja | Ja |
  | **Dateiinhalt anhand des Pfads abrufen** | Ja | Ja |
  | **Dateimetadaten abrufen** | Nicht verfügbar | Nicht verfügbar |
  | **Dateimetadaten anhand des Pfads abrufen** | Nicht verfügbar | Nicht verfügbar |
  | **Dateien im Ordner aufführen** | Nicht verfügbar | Nicht verfügbar |
  | **Datei umbenennen** | Nicht verfügbar | Nicht verfügbar |
  | **Datei aktualisieren** | Nein | Nicht verfügbar |
  ||||

* SFTP-SSH-Trigger unterstützen keine Nachrichtensegmentierung. Trigger wählen beim Anfordern von Dateiinhalten nur Dateien aus, die 15 MB oder kleiner sind. Verwenden Sie stattdessen das folgende Muster, um Dateien abzurufen, die größer als 15 MB sind:

  1. Verwenden Sie einen SFTP-SSH-Trigger, der nur Dateieigenschaften zurückgibt. Diese Trigger verfügen über Namen, die die Beschreibung **(nur Eigenschaften)** enthalten.

  1. Folgen Sie dem Trigger mit der SFTP-SSH-Aktion **Dateiinhalt abrufen**. Diese Aktion liest die komplette Datei und verwendet implizit die Nachrichtensegmentierung.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Vergleichen von SFTP-SSH und SFTP

In der folgenden Liste werden die wichtigsten SFTP-SSH-Funktionen beschrieben, die sich vom SFTP-Connector unterscheiden:

* Er verwendet die [SSH.NET](https://github.com/sshnet/SSH.NET)-Bibliothek, die eine Open Source-SSH-Bibliothek (Secure Shell) mit Unterstützung für .NET ist.

* Er stellt die Aktion **Ordner erstellen** bereit, wodurch ein Ordner unter dem angegebenen Pfad auf dem SFTP-Server erstellt wird.

* Er stellt die Aktion **Datei umbenennen** bereit, wodurch eine Datei auf dem SFTP-Server umbenannt wird.

* Zwischenspeichert die Verbindung mit dem SFTP-Server *für bis zu 1 Stunde*. Diese Funktion verbessert die Leistung und verringert die Anzahl der Versuche des Connectors, eine Verbindung mit dem Server herzustellen. Um die Dauer für dieses Verhalten beim Zwischenspeichern festzulegen, bearbeiten Sie die [Eigenschaft **ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in der SSH-Konfiguration auf Ihrem SFTP-Server.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ihre SFTP-Serveradresse und Kontoanmeldeinformationen, sodass Ihr Workflow auf Ihr SFTP-Konto zugreifen kann. Außerdem benötigen Sie Zugriff auf einen privaten SSH-Schlüssel und das Kennwort für den privaten SSH-Schlüssel. Um große Dateien mit der Blockerstellung hochzuladen, benötigen Sie sowohl Lese- als auch Schreibzugriff auf den Stammordner auf Ihrem SFTP-Server. Andernfalls erhalten Sie einen Fehler „401 – Nicht autorisiert“.

  Der SFTP-SSH-Connector unterstützt sowohl die Authentifizierung mit einem privaten Schlüssel als auch mit einem Kennwort. Der SFTP-SSH-Connector unterstützt jedoch *nur* diese privaten Schlüsselformate, Verschlüsselungsalgorithmen, Fingerabdrücke und Schlüsselaustauschalgorithmen:

  * **Formate für private Schlüssel**: Die Schlüssel „RSA“ (Rivest Shamir-Adleman) und „DSA“ (Digital Signature Algorithm) und OpenSSH- und ssh.com-Formate. Wenn Ihr privater Schlüssel im PuTTY-Dateiformat (PPK) vorliegt, [konvertieren Sie den Schlüssel zuerst in das OpenSSH-Dateiformat (PEM)](#convert-to-openssh).
  * **Verschlüsselungsalgorithmen**: DES-EDE3-CBC, DES EDE3 CFB, DES-CBC, AES-128-CBC, AES-192-CBC und AES-256-CBC
  * **Fingerabdruck**: MD5
  * **Schlüsselaustauschalgorithmen**: curve25519-sha256, curve25519-sha256@libssh.org, ecdh-sha2-nistp256, ecdh-sha2-nistp384, ecdh-sha2-nistp521, diffie-hellman-group-exchange-sha256, diffie-hellman-group-exchange-sha1, diffie-hellman-group16-sha512, diffie-hellman-group14-sha256, diffie-hellman-group14-sha1, and diffie-hellman-group1-sha1

  Nach dem Hinzufügen eines SFTP-SSH-Triggers oder einer Aktion zu Ihrem Workflow müssen Sie Verbindungsinformationen für Ihren SFTP-Server bereitstellen. Wenn Sie Ihren privaten SSH-Schlüssel für diese Verbindung angeben, ***geben Sie den Schlüssel nicht manuell ein, noch bearbeiten Sie ihn manuell** _, da dies zu einem Fehler bei der Verbindung führen könnte. Stellen Sie stattdessen sicher, dass Sie den _*_Schlüssel aus Ihrer Datei für private SSH-Schlüssel_*_ kopieren und diesen Schlüssel in die Verbindungsdetails _ *_einfügen_**. Weitere Informationen finden Sie im Abschnitt [Herstellen einer Verbindung zu SFTP mit SSH](#connect) weiter unten in diesem Artikel.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Der Logik-App-Workflow, in dem Sie auf Ihr SFTP-Konto zugreifen möchten. Um mit einem SFTP-SSH-Trigger zu beginnen, [erstellen Sie einen leeren Logik-App-Workflow](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine SFTP-SSH-Aktion zu verwenden, starten Sie Ihren Workflow mit einem anderen Trigger, z. B. dem **Wiederholungstrigger**.

## <a name="how-sftp-ssh-triggers-work"></a>Funktionsweise von SFTP-SSH-Triggern

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Abrufverhalten

Mit SFTP-SSH-Triggern wird das SFTP-Dateisystem abgefragt und nach Dateien gesucht, die sich seit der letzten Abfrage geändert haben. Bei einigen Tools können Sie den Zeitstempel beibehalten, wenn sich die Dateien ändern. In diesen Fällen müssen Sie diese Funktion deaktivieren, sodass der Trigger arbeiten kann. Hier sind einige gängige Einstellungen:

| SFTP-Client | Aktion |
|-------------|--------|
| Winscp | Navigieren Sie zu **Optionen** > **Voreinstellungen** > **Übertragen** > **Bearbeiten** > **Zeitstempel beibehalten** > **Deaktivieren**. |
| FileZilla | Wechseln Sie zu **Übertragung** > **Änderungszeitpunkt der übertragenen Dateien beibehalten** > **Deaktivieren**. |
|||

Wenn ein Trigger eine neue Datei findet, überprüft er, ob die neue Datei vollständig ist und nicht nur teilweise geschrieben wurde. Zum Beispiel werden bei einer Datei möglicherweise gerade Änderungen vorgenommen, wenn der Trigger den Dateiserver überprüft. Um zu vermeiden, dass eine nur zum Teil geschriebene Datei zurückgegeben wird, vermerkt der Trigger den Zeitstempel für die Datei mit den kürzlichen Änderungen, gibt diese Datei jedoch nicht sofort zurück. Der Trigger gibt die Datei erst dann zurück, wenn der Server erneut abgerufen wird. Dieses Verhalten kann in manchen Fällen zu Verzögerungen führen, die bis zu zweimal länger als das Abrufintervall des Triggers sein können.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Verschiebung und Drift von Triggerwiederholungen

Verbindungsbasierte Trigger, bei denen Sie zuerst eine Verbindung erstellen müssen, z. B. der SFTP-SSH-Trigger, unterscheiden sich von integrierten Triggern, die in Azure Logic Apps nativ ausgeführt werden, z. B der [Wiederholungstrigger](../connectors/connectors-native-recurrence.md). Bei wiederkehrenden, verbindungsbasierten Triggern ist der Wiederholungszeitplan nicht der einzige Faktor, der die Ausführung steuert, und die Zeitzone bestimmt nur die anfängliche Startzeit. Nachfolgende Ausführungen richten sich nach dem Wiederholungszeitplan, der letzten Triggerausführung *und* anderen Faktoren, die zu einer Verschiebung der Ausführungszeiten oder zu unerwartetem Verhalten führen können. Das unerwartete Verhalten kann beispielsweise so aussehen, dass der angegebene Zeitplan nicht beibehalten wird, wenn die Sommerzeit (DST) beginnt und endet. Um sicherzustellen, dass sich die Wiederholungszeit nicht verschiebt, wenn die Sommerzeit wirksam wird, sollten Sie die Wiederholung manuell anpassen. So wird der Workflow weiterhin zur erwarteten Zeit ausgeführt. Andernfalls verschiebt sich die Startzeit um eine Stunde nach vorn, wenn die Sommerzeit beginnt, und eine Stunde nach hinten, wenn die Sommerzeit endet. Weitere Informationen finden Sie unter [Wiederholung für verbindungsbasierte Trigger](../connectors/apis-list.md#recurrence-for-connection-based-triggers).

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY-basierten Schlüssel in OpenSSH konvertieren

Im PuTTY-Format und im OpenSSH-Format werden unterschiedliche Dateinamenerweiterungen verwendet. Im PuTTY-Format wird die Dateinamenerweiterung „PPK“ (PuTTY Private Key) verwendet. Das OpenSSH-Format verwendet die Dateinamenerweiterung „PEM“ (Privacy Enhanced Mail). Wenn Ihr privater Schlüssel im PuTTY-Format vorliegt, und Sie das OpenSSH-Format verwenden müssen, konvertieren Sie zunächst den Schlüssel mit folgenden Schritten in das OpenSSH-Format:

### <a name="unix-based-os"></a>Unix-basiertes Betriebssystem

1. Falls Sie die PuTTY-Tools nicht bereits auf Ihrem System installiert haben, sollten Sie dies jetzt nachholen. Gehen Sie beispielsweise wie folgt vor:

   `sudo apt-get install -y putty`

1. Führen Sie diesen Befehl aus, der eine Datei erstellt, die Sie mit dem SFTP-SSH-Connector verwenden können:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Beispiel:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows-Betriebssystem

1. Wenn Sie dies noch nicht getan haben, [laden Sie das neueste PuTTY-Generator-Tool (PuTTYgen.exe) herunter](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), und starten Sie dann das Tool.

1. Wählen Sie in diesem Bildschirm **Laden** aus.

   ![„Laden“ auswählen](./media/connectors-sftp-ssh/puttygen-load.png)

1. Navigieren Sie zu Ihrer privaten Schlüsseldatei im PuTTY-Format, und wählen Sie **Öffnen** aus.

1. Wählen Sie im Menü **Konvertierungen** den Eintrag **OpenSSH-Schlüssel exportieren** aus.

   ![„OpenSSH-Schlüssel exportieren“ auswählen](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Speichern Sie die private Schlüsseldatei mit der Dateinamenerweiterung `.pem`.

## <a name="considerations"></a>Überlegungen

In diesem Abschnitt werden Überlegungen zur Verwendung der Trigger und Aktionen dieses Connectors beschrieben.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Verwenden verschiedener SFTP-Ordner zum Hochladen und Verarbeiten von Dateien

Verwenden Sie auf dem SFTP-Server separate Ordner zum Speichern hochgeladener Dateien und für den Trigger, der diese Dateien zur Verarbeitung überwachen soll. Andernfalls wird der Trigger nicht ausgelöst und verhält sich unvorhersehbar, z. B. wird eine zufällige Anzahl von Dateien übersprungen, die vom Trigger verarbeitet wird. Diese Anforderung bedeutet jedoch, dass Sie eine Möglichkeit haben müssen, Dateien zwischen diesen Ordnern zu verschieben. 

Wenn dieses Triggerproblem auftritt, entfernen Sie die Dateien aus dem Ordner, den der Trigger überwacht, und verwenden Sie einen anderen Ordner zum Speichern der hochgeladenen Dateien.

<a name="create-file"></a>

### <a name="create-file"></a>Datei erstellen

Zum Erstellen einer Datei auf Ihrem SFTP-Server können Sie die SFTP-SSH-Aktion **Datei erstellen** verwenden. Wenn die Datei mit dieser Aktion erstellt wird, ruft der Logic Apps-Dienst auch automatisch Ihren SFTP-Server auf, um die Metadaten der Datei abzurufen. Wenn Sie jedoch die neu erstellte Datei verschieben, bevor der Logic Apps-Dienst den Aufruf zum Abrufen der Metadaten ausführen kann, erhalten Sie einen `404`Fehler mit der Meldung `'A reference was made to a file or folder which does not exist'`. Um das Lesen der Dateimetadaten nach dem Erstellen der Datei zu überspringen, führen Sie die Schritte zum [Hinzufügen der Eigenschaft **Alle Dateimetadaten abrufen** und Festlegen der Eigenschaft auf **Keine**](#file-does-not-exist) aus.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Herstellen einer Verbindung zu SFTP mit SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie für leere Logik-Apps im Suchfeld `sftp ssh` als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus.

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** aus. Geben Sie im Suchfeld den Begriff `sftp ssh` als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Geben Sie die erforderlichen Informationen für Ihre Verbindung ein.

   > [!IMPORTANT]
   >
   > Wenn Sie Ihren privaten SSH-Schlüssel in der Eigenschaft **SSH private key** eingeben, führen Sie diese zusätzlichen Schritte aus, um sicherzustellen, dass Sie den vollständigen und korrekten Wert für diese Eigenschaft angeben. Ein ungültiger Schlüssel führt zu einem Verbindungsfehler.

   Sie können jeden Text-Editor verwenden. Hier finden Sie Beispielschritte, die zeigen, wie Sie Ihren Schlüssel am Beispiel von Notepad.exe korrekt kopieren und einfügen.

   1. Öffnen Sie die Datei für den privaten SSH-Schlüssel in einem Text-Editor. Bei diesen Schritten verwenden wir als Beispiel Editor.

   1. Wählen Sie im Editor-Menü **Bearbeiten** die Option **Alles markieren** aus.

   1. Klicken Sie auf **Bearbeiten** > **Kopieren**.

   1. Fügen Sie in dem von Ihnen hinzugefügten SFTP-SSH-Trigger oder der Aktion den *vollständigen* kopierten Schlüssel in die Eigenschaft **Privater SSH-Schlüssel** ein, die mehrere Zeilen unterstützt. **_Sie dürfen den Schlüssel nicht manuell eingeben oder bearbeiten._**

1. Wählen Sie **Erstellen** aus, nachdem Sie die Verbindungsdetails eingegeben haben.

1. Geben Sie jetzt die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Außerkraftsetzen der Blockgröße

Um das adaptive Standardverhalten außer Kraft zu setzen, bei dem Blöcke verwendet werden, können Sie eine konstante Blockgröße zwischen 5 MB und 50 MB angeben.

1. Wählen Sie in der rechten oberen Ecke der Aktion die Schaltfläche mit den Auslassungspunkten ( **...** ) und dann **Einstellungen** aus.

   ![Öffnen der SFTP-SSH-Einstellungen](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Geben Sie unter **Inhaltsübertragung** in der Eigenschaft **Blockgröße** einen Integerwert zwischen `5` und `50` ein, z. B.: 

   ![Angeben der stattdessen zu verwendenden Blockgröße](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Wählen Sie **Fertig** aus, nachdem Sie den Vorgang abgeschlossen haben.

## <a name="examples"></a>Beispiele

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-Trigger: When a file is added or modified (Wenn eine Datei hinzugefügt oder geändert wird)

Dieser Trigger startet einen Workflow, wenn auf einem SFTP-Server eine Datei hinzugefügt oder geändert wird. Beispielsweise könnte der Workflow anhand einer Bedingung überprüfen, ob der Dateiinhalt bestimmte Kriterien erfüllt. Wenn der Inhalt die Bedingung erfüllt, kann er mit der SFTP-SSH-Aktion **Dateiinhalt abrufen** angezeigt werden, und eine weitere SFTP-SSH-Aktion kann diese Datei in einem anderen Ordner des SFTP-Servers ablegen.

**Beispiel für Unternehmen**: Sie können mit diesem Trigger beispielsweise einen SFTP-Ordner auf neue Dateien überwachen, die Kundenbestellungen darstellen. Anschließend können Sie eine SFTP-SSH-Aktion wie etwa **Dateiinhalt abrufen** verwenden, um den Inhalt einer Bestellung zur weiteren Verarbeitung abzurufen und in einer Bestelldatenbank zu speichern.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP – SSH-Aktion: Dateiinhalt anhand des Pfads abrufen

Diese Aktion ruft den Inhalt einer Datei auf einem SFTP-Server durch Angeben des Dateipfads ab. Sie können z.B. den Trigger aus dem vorherigen Beispiel sowie eine Bedingung hinzufügen, die vom Dateiinhalt erfüllt werden muss. Wenn die Bedingung als TRUE ausgewertet wird, kann die Aktion ausgeführt werden, die den Inhalt abruft.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Behandeln von Problemen

In diesem Abschnitt werden mögliche Lösungen für häufige Fehler oder Probleme beschrieben.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>Fehler 504: „Ein Verbindungsversuch ist fehlgeschlagen, da die Gegenstelle nach einer bestimmten Zeitspanne nicht ordnungsgemäß reagiert hat, oder die hergestellte Verbindung fehlerhaft war, da der verbundene Host nicht reagiert hat“ oder „Die Anforderung an den SFTP-Server dauerte länger als '00:00:30' Sekunden“.

Dieser Fehler kann auftreten, wenn Ihre Logik-App nicht in der Lage ist, erfolgreich eine Verbindung mit dem SFTP-Server herzustellen. Es gibt verschiedene mögliche Ursachen für dieses Problem. Probieren Sie daher die folgenden Optionen für die Problembehandlung aus:

* Das Verbindungstimeout beträgt 20 Sekunden. Überprüfen Sie, ob die Leistung Ihres SFTP-Servers ausreicht und zwischengeschaltete Geräte (z. B. Firewalls) keinen Overhead erzeugen.

* Wenn Sie eine Firewall eingerichtet haben, müssen Sie der Positivliste die **IP-Adresse des verwalteten Connectors** für Ihre Region hinzufügen. Informationen zum Ermitteln der IP-Adressen für die Region Ihrer Logik-App finden Sie unter [Ausgehende IP-Adressen des verwalteten Connectors: Azure Logic Apps](/connectors/common/outbound-ip-addresses).

* Wenn dieser Fehler nur zeitweilig auftritt, ändern Sie die Einstellung für die **Wiederholungsrichtlinie** in der SFTP-SSH-Aktion in eine Wiederholungsanzahl, die höher als die standardmäßigen vier Wiederholungsversuche ist.

* Überprüfen Sie, ob der SFTP-Server die Anzahl der Verbindungen von jeder IP-Adresse begrenzt. Wenn ein Grenzwert gilt, müssen Sie möglicherweise die Anzahl der parallelen Logik-App-Instanzen begrenzen.

* Erhöhen Sie die [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval)-Eigenschaft in der SSH-Konfiguration auf dem SFTP-Server auf etwa eine Stunde, um den Aufwand für die Verbindungsherstellung zu verringern.

* Sie können im SFTP-Serverprotokoll überprüfen, ob die Anforderung von der Logik-App den SFTP-Server erreicht hat. Wenn Sie weitere Informationen zum Konnektivitätsproblem benötigen, können Sie auch eine Netzwerkablaufverfolgung in Ihrer Firewall und auf dem SFTP-Server ausführen.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404-Fehler: „Es wurde auf eine Datei oder einen Ordner verwiesen, die oder der nicht vorhanden ist.“

Dieser Fehler kann auftreten, wenn Ihr Workflow mit der SFTP-SSH-Aktion **Datei erstellen** eine Datei auf Ihrem SFTP-Server erstellt, die dann aber sofort verschoben wird, bevor der Logic Apps-Dienst die Metadaten der Datei abrufen kann. Wenn Ihr Workflow die Aktion **Datei erstellen** ausführt, ruft der Logic Apps-Dienst automatisch Ihren SFTP-Server auf, um die Metadaten der Datei abzurufen. Wenn die Datei von Ihrer Logik-App verschoben wird, kann der Logic Apps-Dienst sie aber nicht mehr finden. Daher erhalten Sie die Fehlermeldung `404`.

Wenn Sie das Verschieden der Datei nicht vermeiden oder verzögern können, können Sie das Lesen der Dateimetadaten nach der Dateierstellung überspringen, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie in der Aktion **Datei erstellen** die Liste **Neuen Parameter hinzufügen**, wählen Sie die Eigenschaft **Alle Dateimetadaten abrufen** aus, und legen Sie den Wert auf **Nein** fest.

1. Wenn Sie diese Dateimetadaten zu einem späteren Zeitpunkt benötigen, können Sie die Aktion **Dateimetadaten abrufen** verwenden.

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/sftpwithssh/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erfordert die mit ISE bezeichnete Version dieses Connectors Chunking, um stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
