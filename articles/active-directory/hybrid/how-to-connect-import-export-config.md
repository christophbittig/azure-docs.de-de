---
title: Importieren und Exportieren von Azure AD Connect-Konfigurationseinstellungen
description: In diesem Artikel finden Sie häufig gestellte Fragen zur Cloudbereitstellung.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ef5d481cebcdd4c7f08813e8523a03fd45d777b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768094"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Importieren und Exportieren von Azure AD Connect-Konfigurationseinstellungen 

Azure Active Directory (Azure AD) Connect-Bereitstellungen variieren von Installationen im Express-Modus mit einer einzigen Gesamtstruktur bis zu komplexen Bereitstellungen, die mithilfe benutzerdefinierter Synchronisierungsregeln über mehrere Gesamtstrukturen synchronisiert werden. Aufgrund der großen Anzahl von Konfigurationsoptionen und -mechanismen ist es von entscheidender Bedeutung, dass Sie verstehen, welche Einstellungen aktiv sind, und schnell in der Lage sind, einen Server mit einer identischen Konfiguration bereitzustellen. Mit diesem Feature wird die Möglichkeit eingeführt, die Konfiguration eines bestimmten Synchronisierungsservers zu katalogisieren und die Einstellungen in eine neue Bereitstellung zu importieren. Unterschiedliche Momentaufnahmen der Synchronisierungseinstellungen können verglichen werden, um die Unterschiede zwischen zwei Servern oder zwischen zwei Zuständen desselben Servers zu unterschiedlichen Zeitpunkten zu visualisieren.

Bei jeder Konfigurationsänderung über den Azure AD Connect-Assistenten wird automatisch eine neue JSON-Einstellungsdatei mit Zeitstempel in  **%ProgramData%\AADConnect** exportiert. Der Name der Einstellungsdatei weist das Format **Applied-SynchronizationPolicy-*.JSON** auf, wobei der letzte Teil des Dateinamens ein Zeitstempel ist.

> [!IMPORTANT]
> Nur von Azure AD Connect vorgenommene Änderungen werden automatisch exportiert. Alle mit PowerShell, Synchronization Service Manager oder dem Synchronisierungsregel-Editor vorgenommenen Änderungen müssen nach Bedarf exportiert werden, um eine aktuelle Kopie zu erhalten. Der Export nach Bedarf kann auch verwendet werden, um eine Kopie der Einstellungen zur Notfallwiederherstellung an einem sicheren Ort aufzubewahren.

## <a name="export-azure-ad-connect-settings"></a>Exportieren von Azure AD Connect-Einstellungen 

Um eine Zusammenfassung Ihrer Konfigurationseinstellungen anzuzeigen, öffnen Sie das Azure AD Connect-Tool, und wählen Sie die zusätzliche Aufgabe **Aktuelle Konfiguration anzeigen oder exportieren** aus. Eine kurze Zusammenfassung der Einstellungen wird angezeigt, und Sie erhalten die Möglichkeit zum Exportieren der vollständigen Konfiguration Ihres Servers.

Standardmäßig werden die Einstellungen in **%ProgramData%\AADConnect** exportiert. Sie können die Einstellungen auch an einem geschützten Speicherort speichern, um bei einem Notfall die Verfügbarkeit sicherzustellen. Die Einstellungen werden im JSON-Dateiformat exportiert und dürfen zur Gewährleistung logischer Konsistenz nicht manuell erstellt oder bearbeitet werden. Das Importieren manuell erstellter oder bearbeiteter Dateien wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen.

## <a name="import-azure-ad-connect-settings"></a>Importieren von Azure AD Connect-Einstellungen

Gehen Sie wie folgt vor, um zuvor exportierte Einstellungen zu importieren:
 
1. Installieren Sie **Azure AD Connect** auf einem neuen Server.
1. Wählen Sie nach der **Startseite** die Option **Anpassen** aus.
1. Wählen Sie **Synchronisierungseinstellungen importieren** aus. Suchen Sie nach der zuvor exportierten JSON-Einstellungsdatei.
1. Wählen Sie **Installieren** aus.

   ![Screenshot des Bildschirms „Erforderliche Komponenten installieren“](media/how-to-connect-import-export-config/import-1.png)

> [!NOTE]
> Überschreiben Sie die Einstellungen auf dieser Seite, beispielsweise die Verwendung von SQL Server anstelle von LocalDB oder die Verwendung eines vorhandenen Dienstkontos anstelle eines Standard-VSA. Diese Einstellungen werden aus der Datei mit den Konfigurationseinstellungen nicht importiert. Sie sind nur zu Informations- und Vergleichszwecken vorhanden.

>[!NOTE]
>Das Ändern der exportierten JSON-Datei zum Ändern der Konfiguration wird nicht unterstützt.

### <a name="import-installation-experience"></a>Importinstallationsoberfläche 

Die Importinstallationsoberfläche ist mit einem Mindestmaß an Benutzereingaben absichtlich einfach gehalten, um die Reproduktion eines vorhandenen Servers zu erleichtern.

Im Folgenden sind die einzigen Änderungen aufgeführt, die während der Installation vorgenommen werden können. Alle anderen Änderungen können nach der Installation mithilfe des Azure AD Connect-Assistenten vorgenommen werden:
- **Azure Active Directory-Anmeldeinformationen**: Standardmäßig wird der Kontoname des globalen Azure-Administrators vorgeschlagen, der den ursprünglichen Server konfiguriert hat. Dieser *muss* geändert werden, wenn Sie Informationen in einem neuen Verzeichnis synchronisieren möchten.
- **Benutzeranmeldung**: Die für den ursprünglichen Server konfigurierten Anmeldeoptionen sind standardmäßig ausgewählt. Sie werden automatisch zur Eingabe von Anmeldeinformationen oder anderen Informationen aufgefordert, die bei der Konfiguration erforderlich sind. In seltenen Fällen muss möglicherweise ein Server mit anderen Optionen eingerichtet werden, um eine Änderung des Verhaltens auf dem aktiven Server zu vermeiden. Wählen Sie andernfalls **Weiter** aus, um dieselben Einstellungen zu verwenden.
- **Anmeldeinformationen für lokale Verzeichnisse**: Sie müssen für jedes in Ihren Synchronisierungseinstellungen enthaltene lokale Verzeichnis Anmeldeinformationen angeben, um ein Synchronisierungskonto zu erstellen oder ein vorab erstelltes benutzerdefiniertes Synchronisierungskonto bereitzustellen. Diese Vorgehensweise ist mit der Neuinstallation identisch, mit der Ausnahme, dass Sie keine Verzeichnisse hinzufügen oder entfernen können.
- **Konfigurationsoptionen**: Wie bei einer Neuinstallation können Sie die Anfangseinstellungen für das Starten der automatischen Synchronisierung oder das Aktivieren des Stagingmodus konfigurieren. Der Hauptunterschied besteht darin, dass der Stagingmodus bewusst standardmäßig aktiviert ist, um vor dem aktiven Exportieren der Ergebnisse nach Azure einen Vergleich der Konfigurations- und Synchronisierungsergebnisse zu gestatten.

![Screenshot des Bildschirms „Verzeichnisse verbinden“](media/how-to-connect-import-export-config/import-2.png)

> [!NOTE]
> Die primäre Rolle kann nur einem Synchronisierungsserver zugeordnet werden, der Konfigurationsänderungen aktiv nach Azure exportiert. Alle übrigen Server müssen in den Stagingmodus versetzt werden.

## <a name="migrate-settings-from-an-existing-server"></a>Migrieren von Einstellungen eines vorhandenen Servers 

Wenn die Einstellungsverwaltung von einem vorhandenen Server nicht unterstützt wird, können Sie den Server entweder direkt aktualisieren oder die Einstellungen zur Verwendung auf einem neuen Stagingserver migrieren.

Für die Migration muss ein PowerShell-Skript ausgeführt werden, das die vorhandenen Einstellungen zur Verwendung in einer neuen Installation extrahiert. Verwenden Sie diese Methode, um die Einstellungen des vorhandenen Servers zu katalogisieren und dann auf einen neu installierten Stagingserver anzuwenden. Bei einem Vergleich der Einstellungen des ursprünglichen Servers mit denen eines neu erstellten Servers werden die Änderungen zwischen den Servern schnell sichtbar. Befolgen Sie grundsätzlich den Zertifizierungsprozess Ihrer Organisation, um sicherzustellen, dass keine zusätzliche Konfiguration erforderlich ist.

### <a name="migration-process"></a>Migrationsprozess 
Gehen Sie wie folgt vor, um die Einstellungen zu migrieren:

 1. Starten Sie auf dem neuen Stagingserver die Datei **AzureADConnect.msi**, und halten Sie auf der **Startseite** von Azure AD Connect an.

 2. Kopieren Sie **MigrateSettings.ps1** aus dem Verzeichnis „Microsoft Azure AD Connect\Tools“ in einen Speicherort auf dem vorhandenen Server. Beispiel: „C:\setup“, wobei „setup“ ein Verzeichnis ist, das auf dem vorhandenen Server erstellt wurde.</br>
     ![Screenshot: Azure AD Connect-Verzeichnisse](media/how-to-connect-import-export-config/migrate-1.png)

     >[!NOTE]
     > Die Meldung „Ein Positionsparameter wurde nicht gefunden, der das Argument **True** akzeptiert" wird angezeigt (siehe Abbildung):
     >
     >
     >![Screenshot des Fehlers](media/how-to-connect-import-export-config/migrate-5.png) Bearbeiten Sie dann die Datei „MigrateSettings.ps1“, entfernen Sie **$true**, und führen Sie dann das Skript aus: ![Screenshot zum Bearbeiten der Konfiguration](media/how-to-connect-import-export-config/migrate-6.png)
 



 3. Führen Sie das Skript wie nachfolgend dargestellt aus, und speichern Sie das gesamte Serverkonfigurationsverzeichnis. Kopieren Sie dieses Verzeichnis auf den neuen Stagingserver. Sie müssen den gesamten Ordner **Exported-ServerConfiguration-** * auf den neuen Server kopieren.
     ![Screenshot eines Skripts in Windows PowerShell](media/how-to-connect-import-export-config/migrate-2.png)![Screenshot des Vorgangs zum Kopieren des Ordners „Exported-ServerConfiguration-*“](media/how-to-connect-import-export-config/migrate-3.png)

 4. Starten Sie **Azure AD Connect**, indem Sie auf dem Desktop auf das entsprechende Symbol doppelklicken. Akzeptieren Sie die Microsoft-Software-Lizenzbedingungen, und wählen Sie dann auf der nächsten Seite die Option **Anpassen** aus.
 5. Aktivieren Sie das Kontrollkästchen **Synchronisierungseinstellungen importieren**. Wählen Sie **Durchsuchen** aus, um nach dem kopierten Ordner „Exported-ServerConfiguration-*“ zu suchen. Wählen Sie die Datei „MigratedPolicy.json“ aus, um die migrierten Einstellungen zu importieren.

     ![Screenshot der Option „Synchronisierungseinstellungen importieren“](media/how-to-connect-import-export-config/migrate-4.png)

## <a name="post-installation-verification"></a>Überprüfung nach der Installation 

Der Vergleich der ursprünglich importierten Einstellungsdatei mit der exportierten Einstellungsdatei des neu bereitgestellten Servers ist ein wichtiger Schritt, um die Unterschiede zwischen der beabsichtigten und der resultierenden Bereitstellung zu verstehen. Mit Ihrer bevorzugten Anwendung zum Textvergleich erhalten Sie sofort eine Darstellung, in der alle gewünschten oder versehentlichen Änderungen hervorgehoben sind.

Auch wenn viele ehemals manuelle Konfigurationsschritte jetzt nicht mehr nötig sind, müssen Sie weiterhin den Zertifizierungsprozess Ihrer Organisation befolgen, um sicherzustellen, dass keine zusätzliche Konfiguration erforderlich ist. Eine solche Konfiguration könnte erfolgen, wenn Sie erweiterte Einstellungen verwenden, die derzeit nicht in diesem Release der Einstellungsverwaltung erfasst werden.

Folgende Einschränkungen sind bekannt:
- **Synchronisierungsregeln**: Die Rangfolge für eine benutzerdefinierte Regel muss im reservierten Bereich zwischen 0 und 99 liegen, um Konflikte mit den Standardregeln von Microsoft zu vermeiden. Wenn Sie eine benutzerdefinierte Regel außerhalb des reservierten Bereichs einfügen, wird Ihre benutzerdefinierte Regel beim Hinzufügen von Standardregeln zur Konfiguration möglicherweise verschoben. Ein ähnliches Problem tritt auf, wenn Ihre Konfiguration geänderte Standardregeln enthält. Es wird davon abgeraten, Änderungen an einer Standardregel vorzunehmen, weil Regeln dadurch wahrscheinlich falsch angeordnet werden.
- **Geräterückschreiben**: Diese Einstellungen werden katalogisiert. Sie werden derzeit nicht während der Konfiguration angewendet. Wenn das Geräterückschreiben für den ursprünglichen Server aktiviert war, müssen Sie das Feature auf dem neu bereitgestellten Server manuell konfigurieren.
- **Synchronisierte Objekttypen**: Auch wenn die Liste synchronisierter Objekttypen (wie Benutzer, Kontakte und Gruppen) mithilfe von Synchronization Service Manager eingeschränkt werden kann, wird dieses Feature über die Synchronisierungseinstellungen derzeit nicht unterstützt. Nach Abschluss der Installation müssen Sie die erweiterte Konfiguration manuell erneut anwenden.
- **Benutzerdefinierte Ausführungsprofile**: Obwohl der Standardsatz von Ausführungsprofilen mithilfe von Synchronization Service Manager geändert werden kann, wird dieses Feature über die Synchronisierungseinstellungen derzeit nicht unterstützt. Nach Abschluss der Installation müssen Sie die erweiterte Konfiguration manuell erneut anwenden.
- **Konfigurieren der Bereitstellungshierarchie**: Dieses erweiterte Feature von Synchronization Service Manager wird über die Synchronisierungseinstellungen nicht unterstützt. Es muss nach Abschluss der Erstbereitstellung manuell neu konfiguriert werden.
- **AD FS- und PingFederate-Authentifizierung**: Die diesen Authentifizierungsfeatures zugeordneten Anmeldemethoden werden automatisch vorab ausgewählt. Alle anderen erforderlichen Konfigurationsparameter müssen Sie interaktiv angeben.
- **Eine deaktivierte benutzerdefinierte Synchronisierungsregel wird als aktiviert importiert**: Eine deaktivierte benutzerdefinierte Synchronisierungsregel wird als aktiviert importiert. Sie müssen diese unbedingt auch auf dem neuen Server deaktivieren.

 ## <a name="next-steps"></a>Nächste Schritte

- [Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md) 
- [Express-Einstellungen](how-to-connect-install-express.md)
- [Benutzerdefinierte Einstellungen](how-to-connect-install-custom.md)
- [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md) 
