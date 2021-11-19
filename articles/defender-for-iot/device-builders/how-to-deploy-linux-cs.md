---
title: Installieren und Bereitstellen des C#-basierten Linux-Agents
description: Erfahren Sie, wie Sie den C#-basierten Sicherheits-Agent von Defender für IoT unter Linux installieren und bereitstellen.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 5a403af7f5c0b6f2b8d5d497979be06fee404a19
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306090"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Bereitstellen des C#-basierten Sicherheits-Agents von Defender für IoT unter Linux

In dieser Anleitung erfahren Sie, wie Sie den C#-basierten Sicherheits-Agent von Defender für IoT unter Linux installieren und bereitstellen.

In diesem Artikel lernen Sie Folgendes:

- Installieren
- Überprüfen der Bereitstellung
- Deinstallieren des Agents
- Problembehandlung

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu anderen Plattformen und Agent-Varianten finden Sie unter [Choose the right security agent](how-to-deploy-agent.md) (Auswählen des richtigen Sicherheits-Agents).

1. Um den Sicherheits-Agent bereitstellen zu können, müssen Sie auf dem Computer, auf dem Sie ihn installieren möchten, über lokale Administratorrechte verfügen.

1. [Erstellen Sie einen Defender-IoT-Micro-Agent](quickstart-create-security-twin.md) für das Gerät.

## <a name="installation"></a>Installation

Gehen Sie zum Bereitstellen des Sicherheits-Agents wie folgt vor:

1. Laden Sie von [GitHub](https://aka.ms/iot-security-github-cs) die neueste Version auf Ihren Computer herunter.

1. Extrahieren Sie den Inhalt des Pakets, und navigieren Sie zum Ordner _/Install_.

1. Führen Sie `chmod +x InstallSecurityAgent.sh` aus, um dem Skript **InstallSecurityAgent** Ausführungsberechtigungen hinzuzufügen.

1. Als Nächstes führen Sie den folgenden Befehl mit **root-Berechtigungen** aus:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Weitere Informationen zu Authentifizierungsparametern finden Sie unter [Konfigurieren der Authentifizierung](concept-security-agent-authentication-methods.md).

Dieses Skript führt folgende Aktionen aus:

- Installieren der erforderlichen Komponenten

- Hinzufügen eines Dienstbenutzers (mit deaktivierter interaktiver Anmeldung)

- Installieren des Agents als **Daemon** (wobei vorausgesetzt wird, dass das Gerät **systemd** für das klassische Bereitstellungsmodell verwendet)

- Konfigurieren von **sudoers**, damit der Agent bestimmte Aufgaben als Root ausführen kann

- Konfigurieren des Agents mit den angegebenen Authentifizierungsparametern

Sollten Sie weitere Hilfe benötigen, führen Sie das Skript mit dem Parameter „–help“ aus: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Deinstallieren des Agents

Wenn Sie den Agent deinstallieren möchten, führen Sie das Skript mit dem Parameter „–u“ aus: `./InstallSecurityAgent.sh -u`.

> [!NOTE]
> Bei der Deinstallation werden keine der fehlenden erforderlichen Komponenten entfernt, die ggf. im Zuge der Installation installiert wurden.

## <a name="troubleshooting"></a>Problembehandlung

1. Führen Sie Folgendes aus, um den Bereitstellungsstatus zu überprüfen:

    `systemctl status ASCIoTAgent.service`

1. Aktivieren Sie die Protokollierung. Sollte der Agent nicht starten, aktivieren Sie die Protokollierung, um weitere Informationen zu erhalten.

   So aktivieren Sie die Protokollierung:

   1. Öffnen Sie die Konfigurationsdatei zur Bearbeitung in einem beliebigen Linux-Editor:

        `vi /var/ASCIoTAgent/General.config`

   1. Bearbeiten Sie die folgenden Werte:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       Der Wert **logFilePath** ist konfigurierbar.

       > [!NOTE]
       > Es empfiehlt sich, die Protokollierung nach Abschluss der Problembehandlung zu **deaktivieren**. Bei **aktivierter** Protokollierung erhöhen sich Protokolldateigröße und Datennutzung.

   1. Führen Sie Folgendes aus, um den Agent neu zu starten:

       `systemctl restart ASCIoTAgent.service`

   1. Sehen Sie sich die Protokolldatei an, um mehr über den Fehler zu erfahren.

       Speicherort der Protokolldatei: `/var/ASCIoTAgent/IotAgentLog.log`

       Ändern Sie den Dateipfad gemäß dem Namen, den Sie in Schritt 2 für **logFilePath** gewählt haben.

## <a name="next-steps"></a>Nächste Schritte

- Lesen der [Übersicht](overview.md) über den Defender für IoT-Dienst
- Weitere Informationen zu Defender für IoT: [Info zu Agent-basierten Lösungen für Gerätehersteller](architecture-agent-based.md)
- Aktivieren Sie den [Dienst](quickstart-onboard-iot-hub.md).
- Lesen Sie die [häufig gestellten Fragen zum Microsoft Defender für IoT-Agent](resources-agent-frequently-asked-questions.md).
- Machen Sie sich mit [Warnungen](concept-security-alerts.md) vertraut.
