---
title: Erstellen benutzerdefinierter Paketartefakte für Gastkonfigurationen
description: Erfahren Sie, wie Sie eine Paketdatei für eine Gastkonfiguration erstellen.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 12767e40ef99cf218666b6dc540a5ae1c2e2bffa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772537"
---
# <a name="how-to-create-custom-guest-configuration-package-artifacts"></a>Erstellen benutzerdefinierter Paketartefakte für Gastkonfigurationen

Bevor Sie beginnen, sollten Sie die Übersicht zur [Gastkonfiguration](../concepts/guest-configuration.md) lesen.

Beim Überwachen/Konfigurieren von Windows und Linux wird für die Gastkonfiguration eine [DSC](/powershell/scripting/dsc/overview/overview)-Konfiguration (Desired State Configuration) verwendet. Die DSC-Konfiguration definiert den Zustand, in dem sich der Computer befinden soll.

> [!IMPORTANT]
> Benutzerdefinierte Pakete, die den Zustand einer Umgebung überwachen, sind allgemein verfügbar. Pakete hingegen, die Konfigurationen anwenden, befinden sich **in der Vorschauphase**. **Es gelten die folgenden Einschränkungen:**
> 
> Für die Verwendung von Gastkonfigurationspaketen, die Konfigurationen anwenden, ist die Azure-VM-Gastkonfigurationserweiterung Version **1.29.24** oder höher oder der Arc-Agent **1.10.0** oder höher erforderlich.
> 
> Zum Testen der Erstellung und Anwendung von Konfigurationen unter Linux ist das Modul `GuestConfiguration` nur unter Ubuntu 18 verfügbar, aber das vom Modul erstellte Paket und die Richtlinien können für jede Linux-Distribution/-Version genutzt werden, die in Azure oder Arc unterstützt wird.
>
> Die Option zum Testen von Paketen unter macOS ist nicht verfügbar.
> 
> Verwenden Sie keine geheimen oder vertraulichen Informationen in benutzerdefinierten Inhaltspaketen.

Erstellen Sie über die folgenden Schritte eine eigene Konfiguration zum Überprüfen des Zustands eines Azure- oder Nicht-Azure-Computers.

## <a name="install-powershell-7-and-required-powershell-modules"></a>Installieren von PowerShell 7 und erforderlichen PowerShell-Modulen

Stellen Sie zunächst sicher, dass Sie alle Schritte auf der Seite [Einrichten einer Erstellungsumgebung für Gastkonfigurationen](./guest-configuration-create-setup.md) befolgt haben, um die erforderliche Version von PowerShell für Ihr Betriebssystem, das Modul `GuestConfiguration`, sowie ggf. das Modul `PSDesiredStateConfiguration` zu installieren.

## <a name="author-a-configuration"></a>Erstellen einer Konfiguration

Erstellen und kompilieren Sie vor dem Erstellen eines Konfigurationspakets eine DSC-Konfiguration.
Bei Bedarf sind Beispielkonfigurationen für Windows und Linux verfügbar.

> [!IMPORTANT]
> Verwenden Sie beim Kompilieren von Konfigurationen für Windows Version `2.0.5` von `PSDesiredStateConfiguration` (die stabile Version). Installieren Sie beim Kompilieren von Konfigurationen für Linux die Vorabversion `3.0.0`.

Ein Beispiel wird im Dokument [Erste Schritte mit Desired State Configuration (DSC) für Windows](/powershell/scripting/dsc/getting-started/wingettingstarted#define-a-configuration-and-generate-the-configuration-document) bereitgestellt.

Für Linux müssen Sie ein benutzerdefiniertes DSC-Ressourcenmodul mithilfe der [PowerShell-Klassen](/powershell/scripting/dsc/resources/authoringResourceClass) erstellen.
Ein vollständiges Beispiel für eine benutzerdefinierte Ressource und Konfiguration (die mit einer Gastkonfiguration getestet wurde) finden Sie auf der PowerShell-Dokumentationsseite [Schreiben einer benutzerdefinierten DSC-Ressource mit PowerShell-Klassen](/powershell/scripting/dsc/resources/authoringResourceClass).

## <a name="create-a-configuration-package-artifact"></a>Erstellen eines Konfigurationspaketartefakts

Nachdem die MOF-Datei kompiliert wurde, müssen die unterstützenden Dateien in einem Paket zusammengefasst werden.
Das fertige Paket wird von der Gastkonfiguration verwendet, um die Azure Policy-Definitionen zu erstellen.

Mit dem Cmdlet `New-GuestConfigurationPackage` wird das Paket erstellt. Module, die von der Konfiguration benötigt werden, müssen im `$Env:PSModulePath` für die Entwicklungsumgebung verfügbar sein, damit die Befehle im Modul sie dem Paket hinzufügen können.

Parameter des Cmdlets `New-GuestConfigurationPackage` beim Erstellen von Windows-Inhalten:

- **Name**: Name des Pakets mit der Gastkonfiguration.
- **Konfiguration:** Vollständiger Pfad für das kompilierte DSC-Konfigurationsdokument.
- **Pfad**: Pfad des Ausgabeordners. Dieser Parameter ist optional. Wenn er nicht angegeben ist, wird das Paket im aktuellen Verzeichnis erstellt.
- **Typ** (Audit, AuditandSet): Bestimmt, ob die Konfiguration nur überwacht oder ob sie angewendet werden soll, und ändert den Zustand des Computers. Der Standardwert lautet „Audit“.

Für diesen Schritt ist keine Rechteerweiterung erforderlich. Das Force-Cmdlet wird verwendet, um vorhandene Pakete zu überschreiben, wenn Sie den Befehl mehrmals ausführen.

Mit den folgenden Befehlen werden Paketartefakte erstellt:

```powershell
# Create a package that will only audit compliance
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type Audit `
  -Force
```

```powershell
# Create a package that will audit and apply the configuration (Set)
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type AuditAndSet `
  -Force
```

Es wird ein Objekt mit dem Namen und dem Pfad des erstellten Pakets zurückgegeben.

```
Name      Path                                                    
----      ----                                                    
MyConfig  /Users/.../MyConfig/MyConfig.zip
```

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Erwarteter Inhalt eines Gastkonfigurationsartefakts

Das fertige Paket wird von der Gastkonfiguration verwendet, um die Azure Policy-Definitionen zu erstellen. Das Paket enthält Folgendes:

- Kompilierte DSC-Konfiguration als MOF-Datei
- Ordner „Modules“
  - GuestConfiguration-Modul
  - DscNativeResources-Modul
  - Für die MOF-Datei erforderliche DSC-Ressourcenmodule
- Eine Datei mit der Metakonfiguration, in der das Paket `type` und `version` gespeichert werden

Das PowerShell-Cmdlet erstellt die ZIP-Paketdatei. Es ist kein Ordner auf Stammebene oder Versionsordner erforderlich. Das Paketformat muss eine ZIP-Datei sein, deren Gesamtgröße unkomprimiert 100 MB nicht überschreiten darf.

## <a name="extending-guest-configuration-with-third-party-tools"></a>Erweitern der Gastkonfiguration mit Drittanbietertools

Die Artefaktpakete für die Gastkonfiguration können zum Einschluss von Drittanbietertools erweitert werden. Für die Erweiterung der Gastkonfiguration müssen zwei Komponenten entwickelt werden.

- Eine Desired State Configuration-Ressource (DSC) zum Verarbeiten aller Verwaltungsaktivitäten für das Drittanbietertool
  - Installieren
  - Invoke
  - Ausgabe konvertieren
- Inhalt im richtigen Format für die native Nutzung durch das Tool

Für die DSC-Ressource muss eine benutzerdefinierte Entwicklung durchgeführt werden, falls noch keine Communitylösung vorhanden ist. Sie können überprüfen, ob Communitylösungen verfügbar sind, indem Sie im PowerShell-Katalog nach dem Tag [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) suchen.

> [!NOTE]
> Für die Erweiterbarkeit der Gastkonfiguration gilt das Szenario „BYOL (Bring Your Own License)“. Stellen Sie sicher, dass Sie die für Drittanbietertools geltenden Bedingungen erfüllt haben, bevor Sie diese verwenden.

Nachdem die DSC-Ressource in der Entwicklungsumgebung installiert wurde, sollten Sie den Parameter **FilesToInclude** für `New-GuestConfigurationPackage` verwenden, um den Inhalt für die Drittanbieterplattform in das Inhaltsartefakt einzubinden.

## <a name="next-steps"></a>Nächste Schritte

- [Testen Sie das Paketartefakt](./guest-configuration-create-test.md) über Ihre Entwicklungsumgebung.
- [Veröffentlichen Sie das Paketartefakt](./guest-configuration-create-publish.md), damit es für Ihre Computer zugänglich ist.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy-Definition](./guest-configuration-create-definition.md) für eine bedarfsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre benutzerdefinierte Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie [Details zur Konformität für die Gastkonfiguration](./determine-non-compliance.md#compliance-details-for-guest-configuration) für Richtlinienzuweisungen anzeigen können.
