---
title: Erstellen benutzerdefinierter Richtliniendefinitionen für die Gastkonfiguration
description: Erfahren Sie, wie Sie eine Gastkonfigurationsrichtlinie erstellen können.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 28ad60284912261510a55438919924138d4e2b5e
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773013"
---
# <a name="how-to-create-custom-guest-configuration-policy-definitions"></a>Erstellen benutzerdefinierter Richtliniendefinitionen für die Gastkonfiguration

Bevor Sie beginnen, ist es eine gute Idee, die Übersichtsseite für die [Gastkonfiguration](../concepts/guest-configuration.md) zu lesen, und die Details zur Gastkonfigurationsrichtlinie wirken sich auf die [Konfiguration von Korrekturoptionen für die Gastkonfiguration aus](../concepts/guest-configuration-policy-effects.md).

> [!IMPORTANT]
> Details zur Gastkonfigurationserweiterung für Azure-VMs Weisen Sie die folgende Richtliniendefinition zu, um die Erweiterung auf allen Computern im gewünschten Umfang bereitzustellen: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> Für die Verwendung von Gastkonfigurationspaketen, die Konfigurationen anwenden, ist die Azure-VM-Gastkonfigurationserweiterung Version **1.29.24** oder höher oder der Arc-Agent **1.10.0** oder höher erforderlich.
>
> Benutzerdefinierte Richtliniendefinitionen für die Gastkonfiguration, die **AuditIfNotExists** verwenden, sind allgemein verfügbar, aber Definitionen, die **DeployIfNotExists** mit Gastkonfiguration verwenden, befinden sich in der **Preview**.

Führen Sie die folgenden Schritte aus, um eigene Richtlinien zu erstellen, die die Konformität überwachen oder den Zustand von Azure- oder Arc-fähigen Computern verwalten.

## <a name="install-powershell-7-and-required-powershell-modules"></a>Installieren von PowerShell 7 und erforderlichen PowerShell-Modulen

Stellen Sie zunächst sicher, dass Sie alle Schritte auf der Seite [Einrichten einer Erstellungsumgebung für Gastkonfigurationen](./guest-configuration-create-setup.md) befolgt haben, um die erforderliche Version von PowerShell für Ihr Betriebssystem und das Modul zu `GuestConfiguration` installieren.

## <a name="create-and-publish-a-guest-configuration-package-artifact"></a>Erstellen und Veröffentlichen eines Gastkonfigurationspaketartefakts

Falls noch nicht erfolgt, führen Sie alle Schritte auf der Seite [Erstellen benutzerdefinierter Gastkonfigurationspaketartefakte](./guest-configuration-create.md) zum Erstellen und Veröffentlichen eines benutzerdefinierten Gastkonfigurationspakets und [Testen von Gastkonfigurationspaketartefakten](./guest-configuration-create-test.md) aus, um das Gastkonfigurationspaket lokal in Ihrer Entwicklungsumgebung zu überprüfen.

## <a name="policy-requirements-for-guest-configuration"></a>Richtlinienanforderungen für die Gastkonfiguration

Der Abschnitt `metadata` der Richtliniendefinition muss zwei Eigenschaften für den Gastkonfigurationsdienst enthalten, um die Bereitstellung und die Berichterstellung von Gastkonfigurationszuweisungen zu automatisieren. Die Eigenschaft `category` muss auf „Guest Configuration“ festgelegt werden, und ein Abschnitt mit dem Namen `guestConfiguration` muss Informationen zur Gastkonfigurationszuweisung enthalten. Mit dem `New-GuestConfigurationPolicy`-Cmdlet wird dieser Text automatisch erstellt.

Im folgenden Beispiel wird der Abschnitt `metadata` veranschaulicht, der automatisch von `New-GuestConfigurationPolicy` erstellt wird.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

Die Eigenschaft `category` muss auf „Guest Configuration“ festgelegt werden. Wenn der Definitionseffekt auf „DeployIfNotExists" festgelegt ist, muss der Abschnitt `then`Bereitstellungsdetails zu einer Gastkonfigurationszuweisung enthalten. Mit dem `New-GuestConfigurationPolicy`-Cmdlet wird dieser Text automatisch erstellt.

### <a name="create-an-azure-policy-definition"></a>Erstellen einer Azure Policy-Definition

Nachdem ein benutzerdefiniertes Richtlinienpaket für Gastkonfigurationen erstellt und hochgeladen wurde, erstellen Sie die Richtliniendefinition für Gastkonfigurationen. Das Cmdlet `New-GuestConfigurationPolicy` verwendet ein benutzerdefiniertes Richtlinienpaket und erstellt eine Richtliniendefinition.

Für den Parameter **PolicyId** von `New-GuestConfigurationPolicy` ist eine eindeutige Zeichenfolge erforderlich: Ein global eindeutiger Bezeichner (GUID, Globally Unique Identifier) wird empfohlen. Generieren Sie für neue Definitionen mithilfe des Cmdlets eine neue `New-GUID` GUID. Verwenden Sie beim Aktualisieren der Definition dieselbe eindeutige Zeichenfolge für **PolicyId**, um sicherzustellen, dass die richtige Definition aktualisiert wird.

Parameter des Cmdlets `New-GuestConfigurationPolicy`:

- **PolicyId**: Eine GUID oder eine andere eindeutige Zeichenfolge, die die Definition identifiziert.
- **ContentUri**: Öffentlicher HTTP-/HTTPS-URI des Pakets mit dem Inhalt der Gastkonfiguration.
- **DisplayName**: Anzeigename der Richtlinie.
- **Beschreibung**: Beschreibung der Richtlinie.
- **Parameter**: Richtlinienparameter im Hashtabellenformat.
- **Version**: Version der Richtlinie.
- **Pfad**: Zielpfad, unter dem Richtliniendefinitionen erstellt werden.
- **Plattform**: Zielplattform (Windows/Linux) für das Paket mit den Richtlinien und dem Inhalt der Gastkonfiguration.
- **Modus**: (ApplyAndMonitor, ApplyAndAutoCorrect, Audit) wählen aus, ob die Richtlinie die Konfiguration überwachen oder bereitstellen soll. Der Standardwert ist „Audit“.
- Mit **Tag** werden der Richtliniendefinition ein oder mehrere Tags hinzugefügt.
- Mit **Category** wird das Feld mit den Kategoriemetadaten in der Richtliniendefinition festgelegt.

Weitere Informationen zum Parameter „Mode“ finden Sie auf der Seite [How to configure remediation options for guest configuration (Konfigurieren von Wiederherstellungsoptionen für die Gastkonfiguration)](../concepts/guest-configuration-policy-effects.md).

Erstellen Sie eine Richtliniendefinition, die mithilfe eines benutzerdefinierten Konfigurationspakets in einem angegebenen Pfad überwacht:

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID'
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Verbose
```

Erstellen Sie eine Richtliniendefinition, die mithilfe eines benutzerdefinierten Konfigurationspakets in einem angegebenen Pfad eine Konfiguration bereitstellt:

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID'
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Mode 'ApplyAndAutoCorrect' `
  -Verbose
```

In der Ausgabe des Cmdlets wird ein Objekt zurückgegeben, das den Anzeigenamen der Definition und den Pfad der Richtliniendateien enthält. JSON-Definitionsdateien, die Überwachungsrichtliniendefinitionen erstellen, weisen den Namen **auditIfNotExists.jsauf** und Dateien, die Richtliniendefinitionen zum Anwenden von Konfigurationen erstellen, weisen den Namen **deployIfNotExists.json** auf.

#### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtern von Richtlinien der Gastkonfiguration mit Tags

Die mit Cmdlets im Gastkonfigurationsmodul erstellten Richtliniendefinitionen können optional einen Filter für Tags enthalten. Der Parameter **Tag** von `New-GuestConfigurationPolicy` unterstützt ein Array mit Hashtabellen, die die einzelnen Tageinträge enthalten. Die Tags werden dem Abschnitt `If` der Richtliniendefinition hinzugefügt und können nicht per Richtlinienzuweisung geändert werden.

Unten ist ein Beispielcodeausschnitt einer Richtliniendefinition angegeben, mit der nach Tags gefiltert wird.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original guest configuration content
    }
  ]
}
```

#### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Verwenden von Parametern in benutzerdefinierten Richtliniendefinitionen für Gastkonfigurationen

Die Gastkonfiguration unterstützt das Außerkraftsetzen von Eigenschaften einer Konfiguration während der Laufzeit. Dieses Feature bewirkt, dass die Werte in der MOF-Datei im Paket nicht als statisch angesehen werden müssen. Die Überschreibungswerte werden über Azure Policy bereitgestellt und ändern nicht, wie die Konfigurationen erstellt oder kompiliert werden.

Die Cmdlets `New-GuestConfigurationPolicy` und `Get-GuestConfigurationPacakgeComplianceStatus ` enthalten einen Parameter mit dem Namen **Parameter**. Für diesen Parameter wird eine Hashtabellendefinition verwendet, die alle Details zu den einzelnen Parametern enthält, und es werden die erforderlichen Abschnitte der einzelnen Dateien erstellt, die für die Azure Policy-Definition verwendet werden.

Im folgenden Beispiel wird eine Richtliniendefinition zum Überwachen eines Diensts erstellt, bei der der Benutzer bei der Zuweisung der Richtlinie eine Auswahl aus einer Liste trifft.

```powershell
# This DSC Resource text:
Service 'UserSelectedNameExample'
  {
    Name = 'ParameterValue'
    Ensure = 'Present'
    State = 'Running'
  }`

# Would require the following hashtable:
$PolicyParameterInfo = @(
  @{
    Name = 'ServiceName'                                           # Policy parameter name (mandatory)
    DisplayName = 'windows service name.'                          # Policy parameter display name (mandatory)
    Description = 'Name of the windows service to be audited.'     # Policy parameter description (optional)
    ResourceType = 'Service'                                       # DSC configuration resource type (mandatory)
    ResourceId = 'UserSelectedNameExample'                         # DSC configuration resource id (mandatory)
    ResourcePropertyName = 'Name'                                  # DSC configuration resource property name (mandatory)
    DefaultValue = 'winrm'                                         # Policy parameter default value (optional)
    AllowedValues = @('BDESVC','TermService','wuauserv','winrm')   # Policy parameter allowed values (optional)
  }
)

New-GuestConfigurationPolicy
  -PolicyId 'My GUID'
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'Audit Windows Service.' `
  -Description 'Audit if a Windows Service isn't enabled on Windows machine.' `
  -Path '.\policies' `
  -Parameter $PolicyParameterInfo `
  -Version 1.0.0
```

### <a name="publish-the-azure-policy-definition"></a>Veröffentlichen der Azure Policy Definition

Abschließend veröffentlichen Sie die Richtliniendefinitionen mit dem Cmdlet `Publish-GuestConfigurationPolicy`. Das Cmdlet verfügt nur über den Parameter **Path**, mit dem auf den Speicherort der JSON-Dateien verwiesen wird, die mit `New-GuestConfigurationPolicy` erstellt werden.

Um den Befehl „Veröffentlichen“ auszuführen, benötigen Sie Zugriff zum Erstellen von Richtliniendefinitionen in Azure. Die entsprechenden Autorisierungsanforderungen sind auf der Seite mit der [Übersicht über Azure Policy](../overview.md) dokumentiert. Die empfohlene integrierte Rolle ist **Mitwirkender bei Ressourcenrichtlinien**.

```powershell
Publish-GuestConfigurationPolicy -Path '.\policies'
```

Bei der in Azure erstellten Definition ist der letzte Schritt das Zuweisen der Definition. Informieren Sie sich darüber, wie Sie die Definition über das [Portal](../assign-policy-portal.md), die [Azure CLI](../assign-policy-azurecli.md) oder über [Azure PowerShell](../assign-policy-powershell.md) zuweisen können.

### <a name="optional-piping-output-from-each-command-to-the-next"></a>Optional: Ausgabe jedes Befehls an den nächsten weiterleiten

Die Ausgaben in der Gastkonfiguration unterstützen die Pipelineparameter anhand des Namens.
Sie können den Operator `|` verwenden, um die Ausgabe von jedem Befehl an den nächsten zu leiten.
Piping ist in Entwicklerumgebungen nützlich, wenn Sie schnell iterieren, da Sie nicht die Ausgabe jedes Befehls kopieren/einfügen müssen.

So führen Sie die Sequenz unter Verwendung des Operators `|` aus:

```powershell
# End to end flow piping output of each command to the next
$ConfigName         = myConfigName
$ResourceGroupName  = myResourceGroupName
$StorageAccountName = myStorageAccountName
$DisplayName        = 'Configure Linux machine per my scenario.'
$Description        = 'Details about my policy.'
New-GuestConfigurationPackage -Name $ConfigName -Configuration ./$ConfigName.mof -Path ./package/ -Type AuditAndSet -Force |
Publish-GuestConfigurationPackage -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName -Force |
New-GuestConfigurationPolicy -PolicyId 'My GUID' -DisplayName $DisplayName -Description $Description -Path './policies' -Platform 'Linux' -Version 1.0.0 -Mode 'ApplyAndAutoCorrect' |
Publish-GuestConfigurationPolicy
```

## <a name="policy-lifecycle"></a>Lebenszyklus von Richtlinien

Wenn Sie ein Update für die Definition freigeben möchten, ändern Sie die Details für das Gastkonfigurationspaket und die Azure Policy-Definition.

> [!NOTE]
> Die Eigenschaft `version` der Gastkonfigurationszuweisung betrifft nur Pakete, die von Microsoft gehostet werden. Bei der Versionsverwaltung für benutzerdefinierte Inhalte hat sich die Best Practice etabliert, die Version in den Dateinamen aufzunehmen.

Geben Sie zunächst beim Ausführen von `New-GuestConfigurationPackage` einen Namen für das Paket an, der es gegenüber früheren Versionen eindeutig kennzeichnet. Sie können z. B. eine Versionsnummer in den Namen einschließen wie in `PackageName_1.0.0`. Die Zahl in diesem Beispiel dient nur dazu, das Paket eindeutig zu machen, und nicht dazu, das Paket als neuer oder älter als andere Pakete zu kennzeichnen.

Aktualisieren Sie als anschließend die Parameter für das Cmdlet `New-GuestConfigurationPolicy` gemäß den folgenden Erläuterungen.

- **Version**: Beim Ausführen des Cmdlets `New-GuestConfigurationPolicy` müssen Sie eine Versionsnummer angeben, die höher als die der derzeitigen Veröffentlichung ist.
- **contentUri**: Wenn Sie das Cmdlet `New-GuestConfigurationPolicy` ausführen, müssen Sie einen URI zum Speicherort des Pakets angeben. Durch Einschließen einer Paketversion in den Dateinamen wird sichergestellt, dass sich der Wert dieser Eigenschaft in jedem Release ändert.
- **contentHash**: Diese Eigenschaft wird vom Cmdlet `New-GuestConfigurationPolicy` automatisch aktualisiert. Es handelt sich um einen Hashwert des Pakets, das mit `New-GuestConfigurationPackage` erstellt wurde. Diese Eigenschaft muss für die von Ihnen veröffentlichte Datei vom Typ `.zip` stimmen. Wenn nur die Eigenschaft **contentUri** aktualisiert wird, akzeptiert die Erweiterung das Inhaltspaket nicht.

Die einfachste Möglichkeit zum Freigeben eines aktualisierten Pakets ist das Wiederholen des Prozesses in diesem Artikel und das Angeben einer aktualisierten Versionsnummer. Mit dieser Vorgehensweise wird sichergestellt, dass alle Eigenschaften richtig aktualisiert wurden.

## <a name="next-steps"></a>Nächste Schritte

- [Weisen Sie Ihre Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie die [Konformitätsdetails für die Zuweisung von Gastkonfigurationsrichtlinien](./determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
