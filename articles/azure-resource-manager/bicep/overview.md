---
title: Sprache „Bicep“ für das Bereitstellen von Azure-Ressourcen
description: Hier wird die Sprache „Bicep“ für das Bereitstellen der Infrastruktur in Azure beschrieben. Bei der Entwicklung von Vorlagen vereinfacht sie im Vergleich zur Verwendung von JSON die Erstellung.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 028ec297aa30bf6bfd397fee83b8b4525a66bdbc
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484298"
---
# <a name="what-is-bicep"></a>Was ist Bicep?

Bicep ist eine domänenspezifische Sprache (DSL), die deklarative Syntax zum Bereitstellen von Azure-Ressourcen verwendet. Sie bietet eine präzise Syntax, zuverlässige Typsicherheit und Unterstützung für die Wiederverwendung von Code. Wir glauben, dass Bicep die beste Form der Erstellung für Ihre Infrastructure-as-Code-Lösungen in Azure bietet.

Sie können Bicep anstelle von JSON verwenden, um Azure Resource Manager-Vorlagen (ARM-Vorlagen) zu erstellen. Die JSON-Syntax zum Erstellen einer ARM-Vorlage kann ausführlich sein und komplizierte Ausdrücke erfordern. Die Bicep-Syntax reduziert diese Komplexität und erleichtert die Entwicklung. Bicep ist eine transparente Abstraktion von ARM-Vorlagen-JSON-Code und büßt keine der JSON-Vorlagenfunktionen ein. Während der Bereitstellung konvertiert die Bicep-Befehlszeilenschnittstelle eine Bicep-Datei in ARM-Vorlagen-JSON-Code.

Bicep ist nicht als allgemeine Programmiersprache zum Schreiben von Anwendungen vorgesehen. Eine Bicep-Datei deklariert Azure-Ressourcen und -Ressourceneigenschaften, ohne eine Sequenz von Programmierbefehlen zum Erstellen von Ressourcen zu schreiben.

In einer ARM-Vorlage gültige Ressourcentypen, API-Versionen und Eigenschaften sind auch in einer Bicep-Datei gültig.

Informationen zur Überwachung des Status der Bicep-Arbeit finden Sie im [Bicep-Projektrepository](https://github.com/Azure/bicep).

Weitere Informationen zu Bicep finden Sie im folgenden Video:

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Erste Schritte

So beginnen Sie mit Bicep

1. **Installieren Sie die Tools**. Siehe [Einrichten von Bicep-Entwicklungs- und -Bereitstellungsumgebungen](./install.md). Alternativ können Sie das [VS Code Devcontainer-/Codespaces-Repository](https://github.com/Azure/vscode-remote-try-bicep) verwenden, um eine vorkonfigurierte Erstellungsumgebung zu erhalten.
2. **Schließen Sie die [Schnellstartanleitung](./quickstart-create-bicep-use-visual-studio-code.md) und die [Microsoft Learn Bicep-Module ab](./learn-bicep.md)** .

Informationen zum Dekompilieren einer vorhandenen ARM-Vorlage in Bicep finden Sie unter [Decompile ARM templates to Bicep](./decompile.md) (Dekompilieren von ARM-Vorlagen in Bicep). Mit dem [Bicep Playground](https://bicepdemo.z22.web.core.windows.net/) können Sie Bicep und die JSON-Entsprechung nebeneinander anzeigen.

Informationen zu den Ressourcen, die in Ihrer Bicep-Datei verfügbar sind, finden Sie unter [Bicep resource reference](/azure/templates/) (Bicep-Ressourcenreferenz).

Bicep-Beispiele finden Sie im [Bicep-GitHub-Repository](https://github.com/Azure/bicep/tree/main/docs/examples).

## <a name="benefits-of-bicep-versus-other-tools"></a>Vorteile von Bicep gegenüber anderen Tools

Im Vergleich zu anderen Tools bietet Bicep die folgenden Vorteile:

- **Unterstützung für alle Ressourcentypen und API-Versionen:** In Bicep werden alle Vorschau- und allgemein verfügbaren Versionen für Azure-Dienste direkt unterstützt. Sobald ein Ressourcenanbieter neue Ressourcentypen und API-Versionen einführt, können Sie sie in der Bicep-Datei verwenden. Sie müssen nicht warten, bis Tools aktualisiert werden, um die neuen Dienste verwenden zu können.
- **Einfache Syntax**: Im Vergleich zur entsprechenden JSON-Vorlage sind Bicep-Dateien präziser und einfacher zu lesen. Für Bicep sind keine Vorkenntnisse über Programmiersprachen erforderlich. Die Bicep-Syntax ist deklarativ und gibt an, welche Ressourcen und Ressourceneigenschaften Sie bereitstellen möchten.
- **Erstellungsfunktionen:** Wenn Sie Bicep-Dateien über VS Code erstellen, profitieren Sie von erstklassigen Erstellungsfunktionen. Der Editor bietet umfassende Typsicherheit, IntelliSense und Syntaxvalidierung.
- **Modularität:** Sie können den Bicep-Code mithilfe von [Modulen](./modules.md) in verwaltbare Teile unterteilen. Über ein Modul werden zugehörige Ressourcen bereitgestellt. Mit Modulen lässt sich Code wiederverwenden und die Entwicklung vereinfachen. Sie können ein Modul jederzeit in einer Bicep-Datei hinzufügen, wenn Sie die entsprechenden Ressourcen bereitstellen möchten.
- **Integration in Azure-Diensten:** Bicep ist in Azure-Diensten wie Azure Policy, Vorlagenspezifikationen und Blueprints integriert.
- **Keine zu verwaltenden Status oder Statusdateien:** Alle Statusänderungen werden in Azure gespeichert. Benutzer können zusammenarbeiten und sicher sein, dass ihre Updates erwartungsgemäß verarbeitet werden. Verwenden Sie den [Was-wäre-wenn-Vorgang](./deploy-what-if.md), um vor der Bereitstellung der Vorlage eine Vorschau der Änderungen anzuzeigen.
- **Keine Kosten und Open Source:** Bicep ist gänzlich kostenlos. Sie müssen nicht für Premium-Funktionen bezahlen. Bicep wird auch vom Microsoft-Support unterstützt.

## <a name="bicep-improvements"></a>Bicep-Verbesserungen

Bicep bietet im Vergleich zum entsprechenden JSON-Code eine einfachere und präzisere Syntax. Sie verwenden keine in Klammern stehenden Ausdrücke `[...]`. Stattdessen rufen Sie Funktionen direkt auf und erhalten Werte von Parametern und Variablen. Sie weisen jeder bereitgestellten Ressource einen symbolischen Namen zu, sodass Sie einfach in Ihrer Vorlage auf diese Ressource verweisen können.

Der folgende JSON-Code gibt z. B. einen Ausgabewert aus einer Ressourceneigenschaft zurück.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Der entsprechende Ausgabeausdruck in Bicep ist einfacher zu schreiben. Im folgenden Beispiel wird dieselbe Eigenschaft mit dem symbolischen Namen **publicIP** für eine Ressource zurückgegeben, die in der Vorlage definiert ist:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Einen vollständigen Vergleich der Syntax finden Sie unter [Vergleichen von JSON und Bicep für Vorlagen](compare-template-syntax.md).

Bicep verwaltet Abhängigkeiten zwischen Ressourcen automatisch. Sie können die Einstellung `dependsOn` vermeiden, wenn der symbolische Name einer Ressource in einer anderen Ressourcendeklaration verwendet wird.

Die Struktur der Bicep-Datei ist flexibler als die JSON-Vorlage. Sie können an beliebiger Stelle in der Datei Parameter, Variablen und Ausgaben deklarieren. In JSON müssen Sie alle Parameter, Variablen und Ausgaben in den entsprechenden Abschnitten der Vorlage deklarieren.

## <a name="faq"></a>Häufig gestellte Fragen

**Warum wird eine neue Sprache entwickelt, anstatt eine vorhandene zu verwenden?**

Sie können sich Bicep eher als Revision der vorhandenen ARM-Vorlagensprache als eine neue Sprache vorstellen. Die Syntax wurde geändert, aber die Kernfunktionen und die Runtime bleiben unverändert.

Vor der Entwicklung von Bicep wurde die Verwendung einer vorhandenen Programmiersprache in Betracht gezogen. Wir sind der Ansicht, dass unserer Zielgruppe das Erlernen der Bicep-Sprache leichter fällt als das Erlernen einer völlig anderen Sprache.

**Warum liegt der Fokus nicht auf Terraform oder anderen Infrastructure-as-Code-Angeboten von Drittanbietern?**

Verschiedene Benutzer bevorzugen unterschiedliche Konfigurationssprachen und -tools. Wir möchten sicherstellen, dass alle diese Tools eine optimale Nutzung in Azure bieten. Bicep ist Teil dieser Lösung.

Wenn Sie mit Terraform zufrieden sind, gibt es keinen Grund für einen Wechsel. Microsoft ist bestrebt, die optimale Nutzung von Terraform in Azure zu bieten.

Für Kunden, die ARM-Vorlagen ausgewählt haben, verbessert Bicep die Dokumenterstellung. Außerdem hilft Bicep Kunden beim Produktübergang, wenn diese keine Infrastructure-as-Code-Lösung verwendet haben.

**Ist dies für die Verwendung in der Produktion bereit?**

Ja. Ab Version 0.3 wird Bicep von Microsoft-Supportplänen unterstützt. Mit Bicep kann dasselbe erreicht werden wie mit ARM-Vorlagen. Derzeit sind keine wichtigen Änderungen geplant, es ist jedoch möglich, dass wir in Zukunft wichtige Änderungen vornehmen müssen.

**Kann Bicep nur in Azure verwendet werden?**

Derzeit ist nicht geplant, Bicep über Azure hinaus zu erweitern. Wir möchten Azure vollständig unterstützen und die Bereitstellung optimieren.

Damit dieses Ziel erreicht werden kann, ist die Verwendung einiger APIs außerhalb von Azure nötig. Erwartungsgemäß werden Erweiterbarkeitspunkte für diese Szenarios bereitgestellt.

**Was geschieht mit meinen vorhandenen ARM-Vorlagen?**

Diese funktionieren weiterhin genau so wie zuvor. Sie müssen keine Änderungen vornehmen. Die JSON-Sprache wird für zugrunde liegende ARM-Vorlagen weiterhin unterstütz. Bicep-Dateien werden in JSON kompiliert, und dieser JSON-Code wird für die Bereitstellung an Azure gesendet.

Sie können die [JSON-Dateien umgehend in Bicep dekompilieren](./decompile.md).

**Kann ich Bicep zum Bereitstellen in Azure Stack Hub verwenden?**

Ja, Sie können Bicep für Ihre Azure Stack Hub-Bereitstellungen verwenden, aber beachten Sie dabei, dass Bicep möglicherweise Typen anzeigt, die in Azure Stack Hub noch nicht verfügbar sind. Sie können eine Reihe von Beispielen im [GitHub-Repository mit Azure Stack Hub-Schnellstartvorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/Bicep) anzeigen. 

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit der [Schnellstartanleitung](./quickstart-create-bicep-use-visual-studio-code.md).
