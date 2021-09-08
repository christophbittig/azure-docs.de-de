---
title: Änderungen am Verhalten in PowerShell Desired State Configuration für die Gastkonfiguration
description: Dieser Artikel bietet eine Übersicht über die Plattform, die zum Übermitteln von Konfigurationsänderungen an Computer über Azure Policy verwendet wird.
ms.date: 05/31/2021
ms.topic: how-to
ms.openlocfilehash: ee5165ea9e8a80fc31863389df018548859e9b20
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257164"
---
# <a name="changes-to-behavior-in-powershell-desired-state-configuration-for-guest-configuration"></a>Änderungen am Verhalten in PowerShell Desired State Configuration für die Gastkonfiguration

Bevor Sie beginnen, sollten Sie den Überblick über die [Gastkonfiguration](./guest-configuration.md) lesen.

[Für dieses Dokument ist ein Video zur exemplarischen Vorgehensweise verfügbar](https://youtu.be/nYd55FiKpgs).

Die Gastkonfiguration verwendet die Version 3 der[Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview), um Computer zu überwachen und zu konfigurieren. Die DSC-Konfiguration definiert den Zustand, in dem sich der Computer befinden soll. Es gibt viele wichtige Unterschiede bei der Implementierung von DSC in der Gastkonfiguration.

## <a name="guest-configuration-uses-powershell-7-cross-platform"></a>Die Gastkonfiguration verwendet plattformübergreifend PowerShell 7

Die Gastkonfiguration ist so konzipiert, dass die Verwaltung von Windows und Linux konsistent sein kann. In beiden Betriebssystemumgebungen kann ein Benutzer mit PowerShell DSC-Kenntnissen die Konfigurationen mit skriptbasierten Fähigkeiten erstellen und veröffentlichen.

Die Gastkonfiguration verwendet nur die PowerShell DSC Version 3 und basiert nicht auf der vorherigen Implementierung von [DSC für Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) oder den „nx“-Anbietern, die in diesem Repository enthalten sind.

Die Gastkonfiguration wird in PowerShell Version 7.1.3 für Windows und in der PowerShell 7.2 Vorschauversion 6 für Linux betrieben. Ab der Version 7.2 wurde das Modul `PSDesiredStateConfiguration` nicht mehr als Teil der PowerShell-Installation installiert, sondern als [Modul aus dem PowerShell-Katalog](https://www.powershellgallery.com/packages/PSDesiredStateConfiguration).

## <a name="multiple-configurations"></a>Mehrere Konfigurationen

Die Gastkonfiguration unterstützt das Zuweisen mehrerer Konfigurationen zum gleichen Computer. Innerhalb des Betriebssystems der Gastkonfigurationserweiterung sind keine besonderen Schritte erforderlich. Es ist nicht erforderlich, [Teilkonfigurationen](/powershell/scripting/dsc/pull-server/partialConfigs)zu konfigurieren.

## <a name="configuration-mode-is-set-in-the-package-artifact"></a>Der Konfigurationsmodus wird im Paketartefakt festgelegt

Beim Erstellen des Konfigurationspakets wird der Modus mit den folgenden Optionen festgelegt:

- _Überwachung_: Überprüft die Konformität eines Computers. Es sind keine Änderungen vorgenommen worden.
- _AuditandSet_: Überprüft den Kompatibilitätsstatus des Computers und stellt ihn wieder her.
  Es werden Änderungen vorgenommen, wenn der Computer nicht konform ist.

Der Modus wird im Paket und nicht im [Lokalen Konfigurationsmanager](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings)-Dienst festgelegt, da er je nach Konfiguration abweichen kann, wenn mehrere Konfigurationen zugewiesen werden.

## <a name="parameter-support-through-azure-resource-manager"></a>Parameterunterstützung über den Azure Resource Manager

Parameter, die vom Eigenschaften-Array `configurationParameter` in den [Gastkonfigurationszuweisungen](guest-configuration-assignments.md) festgelegt werden, überschreiben den statischen Text in einer MOF-Konfigurationsdatei, wenn die Datei auf einem Computer gespeichert wird. Parameter ermöglichen die Steuerung von Anpassungen und Änderungen durch einen Operator über die Dienst-API, ohne dass Befehle auf dem Computer ausgeführt werden müssen.

Die Parameter in Azure Policy, die Werte an die Gastkonfigurationszuweisungen übergeben, müssen den Typ _Zeichenfolge_ aufweisen. Arrays können nicht über Parameter übergeben werden. Dies gilt selbst dann, wenn die DSC-Ressource Arrays unterstützt.

## <a name="sequence-of-events"></a>Abfolge von Ereignissen

Wenn die Gastkonfiguration einen Computer überwacht oder konfiguriert, wird die gleiche Abfolge von Ereignissen sowohl für Windows als auch für Linux verwendet. Die wesentliche Änderung des Verhaltens ist, dass die `Get`-Methode vom Dienst aufgerufen wird, um die Details zum Zustand des Computers zurückzugeben.

1. Der Agent führt zuerst `Test` aus, um zu ermitteln, ob die Konfiguration den richtigen Status aufweist.
1. Wenn das Paket auf `Audit` festgelegt ist, bestimmt der von der Funktion zurückgegebene boolesche Wert, ob der Zustand von Azure Resource Manager für die Gastzuweisung konform/nicht konform sein soll.
1. Wenn das Paket auf `AuditandSet` festgelegt ist, bestimmt der boolesche Wert, ob der Computer wiederhergestellt werden soll, indem die Konfiguration mithilfe der `Set`-Methode angewendet wird.
   Wenn die `Test`-Methode „False“ zurückgibt, wird `Set` ausgeführt. Wenn die `Test`-Methode „True“ zurückgibt, wird `Set` nicht ausgeführt.
1. Zuletzt führt der Anbieter `Get` aus, um den aktuellen Zustand der einzelnen Einstellungen zurückzugeben. Dadurch sind Details dazu verfügbar, warum ein Computer nicht konform ist und um zu bestätigen, dass der aktuelle Zustand konform ist.

## <a name="trigger-set-from-outside-machine"></a>Auslösen von außerhalb des Computers festlegen

Eine Herausforderung in früheren Versionen von DSC war die Korrektur von Abweichungen im großen Stil ohne viel benutzerdefinierten Code und die Abhängigkeit von WinRM-Remoteverbindungen. Die Gastkonfiguration löst dieses Problem. Benutzer der Gastkonfiguration haben die Kontrolle über die Abweichungskorrektur durch [On-Demand-Wiederherstellung](./guest-configuration-policy-effects.md#remediation-on-demand-applyandmonitor).

## <a name="maximum-size-of-custom-configuration-package"></a>Die maximale Größe des benutzerdefinierten Konfigurationspakets

In Azure Automation State waren die DSC-Konfigurationen in Ihrer [Größe begrenzt](../../../automation/automation-dsc-compile.md#compile-your-dsc-configuration-in-windows-powershell).
Die Gastkonfiguration unterstützt eine Gesamtpaketgröße von 100 MB (vor der Komprimierung). Es gibt keine spezifische Beschränkung für die Größe der MOF-Datei innerhalb des Pakets.

## <a name="special-requirements-for-get"></a>Besondere Anforderungen an Get

Die Funktionsmethode `Get` hat spezielle Anforderungen an die Gastkonfiguration and die Azure Policy Gastkonfiguration, die für die Windows PowerShell Desired State Configuration nicht benötigt wurden.

- Die zurückgegebene Hashtabelle muss eine Eigenschaft namens **Reasons** (Gründe) enthalten.
- Die Reasons-Eigenschaft muss ein Array sein.
- Jedes Element im Array sollte eine Hashtabelle mit Schlüsseln namens **Code** und **Phrase** sein.

Die Eigenschaft „Reasons“ wird vom Dienst verwendet, um die Konformität von Informationen zu standardisieren. Sie können sich jedes Element in Reasons als „Grund“ vorstellen, dass die Ressource konform oder nicht konform ist. Die Eigenschaft ist ein Array, da eine Ressource aus mehr als einem Grund nicht konform sein könnte.

Die Eigenschaften **Code** und **Phrase** werden vom Dienst erwartet. Wenn Sie eine benutzerdefinierte Ressource erstellen, legen Sie den auszugebenden Text (typischerweise stdout) als Grund für die fehlende Konformität der Ressource als Wert für **Phrase** fest.
**Code** weist bestimmte Formatierungsanforderungen auf, sodass die Berichterstellung eindeutig Informationen zur Ressource anzeigen kann, die für die Überwachung verwendet wird. Diese Lösung macht die Gastkonfiguration erweiterbar. Sie können beliebige Befehle ausführen. Die einzige Voraussetzung ist, dass die Ausgabe des Befehls als Zeichenfolgenwert für die Eigenschaft **Phrase** zurückgegeben werden kann.

- **Code** (Zeichenfolge): Der Name der Ressource, wiederholt, und dann ein Kurzname ohne Leerzeichen als Bezeichner für den Grund. Diese drei Werte sollten ohne Leerzeichen durch Doppelpunkte getrennt sein.
  - Ein Beispiel wäre `registry:registry:keynotpresent`
- **Phrase** (Zeichenfolge): Für Menschen lesbarer Text, um zu erläutern, warum die Einstellung nicht konform ist.
  - Ein Beispiel wäre `The registry key $key isn't present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

### <a name="the-reasons-property-embedded-class"></a>Die eingebettete Reasons-Eigenschaftsklasse

In skriptbasierten Ressourcen (nur für Windows) ist die Reasons-Klasse wie folgt in der MOF-Schemadatei enthalten.

```mof
[ClassVersion("1.0.0.0")]
class Reason
{
  [Read] String Phrase;
  [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
  [Key, Description("Example description")] String Example;
  [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

In klassenbasierten Ressourcen (für Windows und Linux) ist die `Reason`-Klasse wie folgt im PowerShell-Modul enthalten. Bei Linux wird die Groß-/Kleinschreibung beachtet, sodass „C“ im Code und „P“ in Phrase großgeschrieben werden muss.

```powershell
enum ensure {
  Absent
  Present
}

class Reason {
  [DscProperty()]
  [string] $Code

  [DscProperty()]
  [string] $Phrase
}

[DscResource()]
class Example {

  [DscProperty(Key)]
  [ensure] $ensure

  [DscProperty()]
  [Reason[]] $Reasons

  [Example] Get() {
    # return current current state
  }

  [void] Set() {
    # set the state
  }

  [bool] Test() {
    # check whether state is correct
  }
}

```

Wenn die Ressource über erforderliche Eigenschaften verfügt, müssen diese Eigenschaften von `Get` parallel zur Klasse `Reason` zurückgegeben werden. Wenn `Reason` nicht eingeschlossen ist, enthält der Dienst ein „Catch-All“-Verhalten, das die Werteingaben mit `Get` und den von `Get` zurückgegebenen Werten vergleicht und einen ausführlichen Vergleich als `Reason` bereitstellt.

## <a name="configuration-names"></a>Konfigurationsnamen

Der Name der benutzerdefinierten Konfiguration muss überall einheitlich sein. Der Name der `.zip`-Datei für das Inhaltspaket, der Konfigurationsname in der MOF-Datei und der Name der Gastzuweisung in der Resource Manager-Vorlage müssen identisch sein.

## <a name="common-dsc-features-not-available-during-guest-configuration-public-preview"></a>Die allgemeinen DSC-Funktionen, die während der öffentlichen Vorschau der Gastkonfiguration nicht verfügbar sind

Während der öffentlichen Vorschauversion unterstützt die Gastkonfiguration die [Angabe computerübergreifender Abhängigkeiten](/powershell/scripting/dsc/configurations/crossnodedependencies) mithilfe von „WaitFor*“-Ressourcen nicht. Es ist nicht möglich, dass ein Computer überwacht und darauf wartet, dass ein anderer Computer einen Zustand erreicht, bevor er fortschreitet.

Die [Neustartbehandlung](/powershell/scripting/dsc/configurations/reboot-a-node) ist in der öffentlichen Vorschauversion der Gastkonfiguration einschließlich `$global:DSCMachineStatus` nicht verfügbar. Die Konfigurationen können einen Knoten während oder am Ende einer Konfiguration nicht neu starten.

## <a name="known-compatibility-issues-with-supported-modules"></a>Die bekannten Kompatibilitätsprobleme mit unterstützten Modulen

Das Modul `PsDscResources` im PowerShell-Katalog und das Modul `PSDesiredStateConfiguration`, das mit Windows geliefert wird, werden von Microsoft unterstützt und sind eine häufig verwendete Gruppe von Ressourcen für DSC. Beachten Sie die folgenden bekannten Kompatibilitätsprobleme, bis das Modul `PSDscResources` für DSCv3 aktualisiert wurde.

- Verwenden Sie keine Ressourcen aus dem Modul `PSDesiredStateConfiguration`, das mit Windows geliefert wird. Wechseln Sie stattdessen zu `PSDscResources`.
- Verwenden Sie nicht die Ressourcen `WindowsFeature` und `WindowsFeatureSet` in `PsDscResources`. Wechseln Sie stattdessen zu den Ressourcen `WindowsOptionalFeature` und `WindowsOptionalFeatureSet`.

## <a name="coexistance-with-dsc-version-3-and-previous-versions"></a>Parallelität mit der DSC Version 3 und früheren Versionen

Die DSC Version 3 in der Gastkonfiguration kann gleichzeitig mit älteren Versionen vorhanden sein, die in [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) und [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted) installiert sind.
Die Implementierungen sind getrennt. Es gibt jedoch keine Konflikterkennung über die DSC-Versionen hinweg. Versuchen Sie daher nicht, die gleichen Einstellungen zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

- Übersicht über die [Gastkonfiguration](./guest-configuration.md).
- Richten Sie eine [Entwicklungsumgebung](../how-to/guest-configuration-create-setup.md) für ein benutzerdefiniertes Gastkonfigurationspaket ein.
- [Erstellen Sie ein Paketartefakt](../how-to/guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](../how-to/guest-configuration-create-test.md) aus Ihrer Entwicklungsumgebung.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy Definition](../how-to/guest-configuration-create-definition.md) für die skalierungsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie die [Konformitätsdetails für die Zuweisung von Gastkonfigurationsrichtlinien](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
