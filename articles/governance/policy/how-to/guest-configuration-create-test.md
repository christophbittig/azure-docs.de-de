---
title: 'Gewusst wie: Testen der Paketartefakte für Gastkonfigurationen'
description: Die Erfahrung beim Erstellen und Testen von Paketen, die Konfigurationen auf Computern überwachen oder anwenden.
ms.date: 07/20/2021
ms.topic: how-to
ms.openlocfilehash: 216cd207033b3bddd4960b85d8943e3842f8041f
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080649"
---
# <a name="how-to-test-guest-configuration-package-artifacts"></a>Gewusst wie: Testen der Paketartefakte für Gastkonfigurationen

Das PowerShell-Modul `GuestConfiguration` enthält Tools zum Automatisieren des Testens eines Konfigurationspakets außerhalb von Azure. Verwenden Sie diese Tools, um Probleme zu finden und schnell zu durchlaufen, bevor Sie mit dem Testen in einer mit Azure oder Arc verbundenen Umgebung fortfahren.

Bevor Sie mit dem Testen beginnen können, führen Sie alle Schritte auf den Seiten [Gewusst wie: Einrichten einer Umgebung zum Erstellen einer Gastkonfiguration](./guest-configuration-create-setup.md) und [Gewusst wie: Erstellen von Richtlinien für Gastkonfigurationen für Windows](./guest-configuration-create.md) aus, um ein benutzerdefiniertes Gastkonfigurationspaket zu erstellen und zu veröffentlichen.

> [!IMPORTANT]
> Benutzerdefinierte Pakete, die den Zustand einer Umgebung überwachen, sind allgemein verfügbar. Pakete, die Konfigurationen anwenden, befinden sich hingegen **in der Vorschau**. **Es gelten die folgenden Einschränkungen:**
> 
> Für die Verwendung von Gastkonfigurationspaketen, die Konfigurationen anwenden, ist die Azure-VM-Gastkonfigurationserweiterung Version **1.29.24** oder höher oder der Arc-Agent **1.10.0** oder höher erforderlich.
> 
> Zum Testen der Erstellung und Anwendung von Konfigurationen unter Linux ist das Modul `GuestConfiguration` nur unter Ubuntu 18 verfügbar, aber das vom Modul erstellte Paket und die Richtlinien können für jede Linux-Distribution/-Version genutzt werden, die in Azure oder Arc unterstützt wird.
>
> Die Option zum Testen von Paketen unter macOS ist nicht verfügbar.

Sie können das Paket über Ihre Arbeitsstation oder CI/CD-Umgebung (Continuous Integration/Continuous Deployment) testen.  Das `GuestConfiguration`-Modul enthält den gleichen Agent für Ihre Entwicklungsumgebung, der auf Azure-Computern oder Computern mit Arc-Unterstützung verwendet wird. Der Agent enthält eine eigenständige Instanz von PowerShell 7.1.3 für Windows und 7.2.0-preview.7 für Linux, sodass die Skriptumgebung, in der das Paket getestet wird, mit Computern konsistent ist, die Sie mithilfe der Gastkonfiguration verwalten.

Der Agent-Dienst auf Azure-Computern und Computern mit Arc-Unterstützung wird in Windows als „LocalSystem“-Konto und in Linux als „Root“ ausgeführt. Führen Sie die folgenden Befehle im privilegierten Sicherheitskontext aus, um optimale Ergebnisse zu erzielen.

Um PowerShell als „LocalSystem“ in Windows auszuführen, verwenden Sie das SysInternals-Tool [PSExec](/sysinternals/downloads/psexec).

Um PowerShell unter Linux als „Root“ auszuführen, verwenden Sie den [Su-Befehl](https://manpages.ubuntu.com/manpages/man1/su.1.html).

## <a name="validate-the-configuration-package-meets-requirements"></a>Überprüfen, ob das Konfigurationspaket die Anforderungen erfüllt

Testen Sie zunächst mithilfe von `Get-GuestConfigurationPacakgeComplianceStatus `, ob das Konfigurationspaket die grundlegenden Anforderungen erfüllt. Der Befehl überprüft die folgenden Paketanforderungen.

- MOF ist am richtigen Speicherort vorhanden und gültig
- Erforderliche Module/Abhängigkeiten sind mit der richtigen Version vorhanden, ohne Duplikate
- Überprüfen, ob das Paket signiert ist (optional)
- Testen, ob `Test` und `Get` Informationen zum Konformitätsstatus zurückgeben

Parameter des Cmdlets `Get-GuestConfigurationPackageComplianceStatus `:

- **Path**: Dateipfad oder URI des Gastkonfigurationspakets
- **Parameter**: Richtlinienparameter im Hashtabellenformat.

Wenn dieser Befehl zum ersten Mal ausgeführt wird, wird der Gastkonfigurations-Agent auf dem Testcomputer unter Windows unter dem Pfad `c:\programdata\GuestConfig\bin` und unter Linux unter `/var/lib/GuestConfig/bin` installiert. Auf diesen Pfad kann ein Benutzerkonto nicht zugreifen, sodass für den Befehl eine Rechteerweiterung erforderlich ist.

Führen Sie zum Testen des Pakets folgenden Befehl aus:

In Windows aus einer PowerShell 7-Sitzung mit erhöhten Rechten.

```powershell
# Get the current compliance results for the local machine
Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip
```

In Linux durch Ausführen von PowerShell mit „sudo“.

```bash
# Get the current compliance results for the local machine
sudo pwsh -command 'Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip'
```

Der Befehl gibt ein Objekt aus, das den Konformitätsstatus und die Details pro Ressource enthält.

```powershell
  complianceStatus  resources
  ----------------  ---------
  True              @{BuiltInAccount=localSystem; ConfigurationName=MyConfig; Credential=; Dependencies=System.Obje…
```

#### <a name="test-the-configuration-package-can-apply-a-configuration"></a>Testen, ob das Konfigurationspaket eine Konfiguration anwenden kann

Wenn der Konfigurationspaketmodus `AuditandSet` ist, können Sie schließlich mit dem Befehl `Start-GuestConfigurationPackageRemediation` testen, ob die `Set`-Methode Einstellungen auf einen lokalen Computer anwenden kann.

> [!IMPORTANT]
> Dieser Befehl versucht, Änderungen in der lokalen Umgebung vorzunehmen, in der er ausgeführt wird.

Parameter des Cmdlets `Start-GuestConfigurationPackageRemediation`:

- **Pfad**: Vollständiger Pfad des Pakets mit der Gastkonfiguration.

In Windows aus einer PowerShell 7-Sitzung mit erhöhten Rechten.

```powershell
# Test applying the configuration to local machine
Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip
```

In Linux durch Ausführen von PowerShell mit „sudo“.

```bash
# Test applying the configuration to local machine
sudo pwsh -command 'Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip'
```

Der Befehl gibt keine Ausgabe zurück, solange keine Fehler auftreten. Verwenden Sie zur Problembehandlung bei Details in Verbindung mit Ereignissen, die während der Ausführung von `Set` auftreten, den `-verbose`-Parameter.

Nachdem Sie den Befehl `Start-GuestConfigurationPackageRemediation` ausgeführt haben, können Sie den Befehl `Get-GuestConfigurationComplianceStatus` erneut ausführen, um zu bestätigen, dass sich der Computer jetzt im richtigen Zustand befindet.

## <a name="next-steps"></a>Nächste Schritte

- [Veröffentlichen Sie das Paketartefakt](./guest-configuration-create-publish.md), damit es für Ihre Computer zugänglich ist.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy-Definition](./guest-configuration-create-definition.md) für die skalierungsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre benutzerdefinierte Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Informieren Sie sich, wie Sie die [Compliancedetails für die Richtlinienzuweisungen der Gastkonfiguration](./determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
