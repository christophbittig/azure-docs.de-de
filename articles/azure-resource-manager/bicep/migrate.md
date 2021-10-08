---
title: Migrieren von Azure-Ressourcen und JSON-ARM-Vorlagen für die Verwendung von Bicep
description: Beschreibt den empfohlenen Workflow beim Migrieren von Azure-Ressourcen und JSON-ARM-Vorlagen zur Verwendung von Bicep.
author: joshuawaddell
ms.author: jowaddel
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: e9ec55ac41cb30a902b337184cc75a4f976700bf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700442"
---
# <a name="migrate-to-bicep"></a>Migrieren zu Bicep

Die Definition Ihrer Azure-Ressourcen in Bicep bietet eine Reihe von Vorteilen, z. B. einfachere Syntax, Modularisierung, automatische Abhängigkeitsverwaltung, Typvalidierung und IntelliSense sowie ein verbessertes Erlebnis bei der Dokumentenerstellung.

Wenn Sie über vorhandene JSON Azure Resource Manager-Vorlagen (ARM-Vorlagen) und/oder bereitgestellte Ressourcen verfügen und diese sicher zu Bicep migrieren möchten, empfehlen wir Ihnen, einen empfohlenen Workflow zu befolgen, der aus fünf Phasen besteht:

:::image type="content" source="./media/migrate/five-phases.png" alt-text="Diagramm der fünf Phasen, um Azure-Ressourcen zu Bicep zu migrieren: Konvertieren, migrieren, umgestalten, testen und bereitstellen." border="false":::

Der erste Schritt im Prozess besteht im Erfassen Ihrer Azure-Ressourcen als JSON-Datei, sofern noch keine vorhanden ist. Anschließend dekompilieren Sie die JSON-Datei in eine Anfangsversion einer Bicep-Datei, die Sie durch Umgestalten verbessern. Wenn Sie über eine Arbeitsdatei verfügen, testen und stellen Sie bereit. Dazu verwenden Sie einen Ablauf, der das Risiko erheblicher Änderungen an Ihrer Azure-Umgebung möglichst gering hält.

:::image type="content" source="./media/migrate/migrate-bicep.png" alt-text="Abbildung des empfohlenen Workflows für die Migration von Azure-Ressourcen zu Bicep." border="false":::

In diesem Artikel wird dieser empfohlene Workflow zusammengefasst. Eine ausführliche Anleitung finden Sie unter [Migrieren von Azure-Ressourcen und JSON-ARM-Vorlagen zur Verwendung von Bicep](/learn/modules/migrate-azure-resources-bicep/) auf Microsoft Learn.

## <a name="phase-1-convert"></a>Phase 1: Konvertieren

In der _Konvertierungsphase_ der Migration Ihrer Ressourcen zu Bicep besteht das Ziel darin, eine anfängliche Darstellung Ihrer Azure-Ressourcen zu erfassen. Die Bicep-Datei, die Sie in dieser Phase erstellen, ist nicht vollständig und kann noch nicht verwendet werden. Die Datei bietet Ihnen jedoch einen Ausgangspunkt für Ihre Migration.

Die Konvertierungsphase besteht aus zwei Schritten, die Sie nacheinander ausführen:

1. **Erfassen Sie eine JSON-Darstellung Ihrer Azure-Ressourcen.** Wenn Sie über eine vorhandene JSON-Vorlage verfügen, die Sie in Bicep konvertieren, ist der erste Schritt einfach– Sie verfügen bereits über Ihre Quellvorlage. Wenn Sie über das Portal oder ein anderes Tool bereitgestellte Azure-Ressourcen konvertieren, müssen Sie die Ressourcendefinitionen *exportieren* und dann in Bicep konvertieren. Sie können das Azure-Portal, die Azure CLI und Azure PowerShell-Cmdlets verwenden, um einzelne Ressourcen, mehrere Ressourcen und ganze Ressourcengruppen zu exportieren.

1. **Konvertieren Sie die JSON Darstellung zu Bicep. Nehmen Sie dazu den Befehl _decompile_.** [Die Bicep-Tools enthalten den Befehl `decompile` zum Konvertieren von Vorlagen.](decompile.md) Sie können den `decompile`-Befehl entweder die Azure CLI oder über die Bicep-CLI aufrufen. Der Dekompilierungsprozess ist ein bestmöglicher Prozess und garantiert keine vollständige Zuordnung von JSON zu Bicep. Möglicherweise müssen Sie die generierte Bicep-Datei überarbeiten, um die bewährten Methoden Ihrer Vorlage zu erfüllen, bevor Sie die Datei zum Bereitstellen von Ressourcen verwenden.

## <a name="phase-2-migrate"></a>Phase 2: Migrieren

In der _Migrationsphase_ der Migration Ihrer Ressourcen zu Bicep besteht das Ziel darin, den ersten Entwurf Ihrer bereitstellbaren Bicep-Datei zu erstellen und sicherzustellen, dass sie alle Azure-Ressourcen definiert, die sich im Geltungsbereich der Migration befinden.

Die Migrationsphase besteht aus drei Schritten, die Sie nacheinander ausführen:

1. **Erstellen einer neuen leeren Bicep-Datei.** Es ist eine bewährte Methode, eine ganz neue Bicep-Datei zu erstellen. Die in der _Konvertierungsphase_ erstellte Datei ist ein Referenzpunkt, den Sie sich ansehen können, aber Sie sollten die Datei nicht als endgültig betrachten oder in diesem Zustand bereitstellen.

1. **Kopieren der jeweiligen Ressourcen aus der dekompilierten Vorlage.** Kopieren Sie jede Ressource einzeln aus der konvertierten Bicep-Datei in die neue Bicep-Datei. Dieser Prozess hilft Ihnen, alle Probleme auf Basis der einzelnen Ressourcen zu lösen und Verwirrung zu vermeiden, wenn Ihre Vorlage an Umfang zunimmt.

1. **Identifizieren und Neuerstellen von fehlenden Ressourcen.** Nicht alle Azure-Ressourcentypen können über das Azure-Portal, die Azure CLI oder Azure PowerShell exportiert werden. Beispielsweise werden Erweiterungen für virtuelle Computer wie DependencyAgentWindows und MMAExtension (Microsoft Monitoring Agent) als Ressourcentypen für den Export nicht unterstützt. Alle Ressourcen, die nicht exportiert wurden (z. B. VM-Erweiterungen), müssen Sie in Ihrer neuen Bicep-Datei neu erstellen. Es gibt mehrere Tools und Ansätze, mit denen Sie Ressourcen neu erstellen können, z. B. [Azure-Ressourcen-Explorer](/azure/azure-resource-manager/templates/view-resources?azure-portal=true#use-resource-explorer),die [Referenzdokumentation zu Bicep-und ARM-Vorlagen](/azure/templates/?azure-portal=true) und die [Azure-Schnellstart-Vorlagen-](https://azure.microsoft.com/resources/templates?azure-portal=true)Website.

## <a name="phase-3-refactor"></a>Phase 3: Refactoring

In der _Umgestaltungsphase_ der Migration Ihrer Ressourcen zu Bicep besteht das Ziel in der Verbesserung der Qualität Ihres Bicep-Codes. Diese Verbesserungen können Änderungen umfassen, z. B. das Hinzufügen von Codekommentaren, die die Vorlage mit Ihren Vorlagenstandards in Einklang bringen.

Die Bereitstellungsphase besteht aus acht Schritten, die Sie in beliebiger Reihenfolge ausführen können:

1. **Überprüfen der Ressourcen-API-Versionen.** Beim Exportieren von Azure-Ressourcen verfügt die exportierte Vorlage möglicherweise nicht über die neueste API-Version für einen Ressourcentyp. Wenn sie bestimmte Eigenschaften für zukünftige Bereitstellungen benötigen, aktualisieren Sie die API auf die entsprechende Version. Es ist eine bewährte Methode, die API-Versionen für jede exportierte Ressource zu überprüfen.

1. **Überprüfen Sie die Linter-Vorschläge in Ihrer neuen Bicep-Datei.** Beim Erstellen von Bicep-Dateien mithilfe der [Bicep-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep&azure-portal=true) wird der [Bicep-Linter](linter.md) automatisch ausgeführt und hebt Vorschläge und Fehler in Ihrem Code hervor. Viele der Vorschläge und Fehler enthalten eine Option für eine schnelle Korrektur des Problems. Überprüfen Sie diese Empfehlungen, und passen Sie Ihre Bicep-Datei an.

1. **Überarbeiten von Parametern, Variablen und Symbolnamen.** Es ist möglich, dass die Namen von Parametern, Variablen und symbolischen Namen, die vom Decompiler generiert werden, nicht mit Ihrer Standardbenennungskonvention übereinstimmen. Überprüfen Sie die generierten Namen, und nehmen Sie bei Bedarf Anpassungen vor.

1. **Vereinfachen von Ausdrücken.** Der Dekompilierungsprozess nutzt möglicherweise nicht alle Features von Bicep. Überprüfen Sie alle bei der Konvertierung generierten Ausdrücke, und vereinfachen Sie sie. Die dekompilierte Vorlage kann z. B. eine `concat()` oder `format()` -Funktion enthalten, die mithilfe von [Zeichenfolgeninterpolation](bicep-functions-string.md#concat) vereinfacht werden kann. Überprüfen Sie alle Lintervorschläge, und nehmen Sie bei Bedarf Anpassungen vor.

1. **Überprüfen von untergeordneten und Erweiterungsressourcen.** Bei Bicep gibt es mehrere Möglichkeiten, [untergeordnete Ressourcen](child-resource-name-type.md) und [Erweiterungsressourcen](scope-extension-resources.md) zu deklarieren, einschließlich der Verkettung der Namen Ihrer Ressourcen mithilfe des Schlüsselworts `parent` und geschachtelter Ressourcen. Überprüfen Sie diese Ressourcen nach der Dekompilierung, und stellen Sie sicher, dass die Struktur Ihren Standards entspricht. Stellen Sie beispielsweise sicher, dass Sie keine Zeichenfolgenverkettung verwenden, um untergeordnete Ressourcennamen zu erstellen. Sie sollten die `parent`-Eigenschaft oder eine geschachtelte Ressource verwenden. Auf ähnliche Weise kann auf Subnetze entweder als Eigenschaften eines virtuellen Netzwerks oder als separate Ressource verwiesen werden.

1. **Modularisieren.** Wenn Sie eine Vorlage konvertieren, die über viele Ressourcen verfügt, sollten Sie der Einfachheit halber erwägen, die einzelnen Ressourcentypen in [Module](modules.md) aufzuteilen. Bicep-Module tragen dazu bei, die Komplexität Ihrer Bereitstellungen zu reduzieren, und erhöhen die Wiederverwendbarkeit Ihres Bicep-Codes.

    > [!NOTE]
    > Es ist möglich, Ihre JSON-Vorlagen als Module in einer Bicep-Bereitstellung zu verwenden. Bicep hat die Möglichkeit, JSON-Module zu erkennen und auf diese ähnlich zu verweisen, wie Sie Bicep-Module verwenden.

1. **Fügen Sie Kommentare und Beschreibungen hinzu.** Guter Bicep-Code ist _selbstdokumentierend_. Mit Bicep können Sie Ihrem Code Kommentare und `@description()` Attribute hinzufügen, mit denen Sie Ihre Infrastruktur dokumentieren können. Bicep unterstützt sowohl einzeilige Kommentare mit einer `//`-Zeichenfolge als auch mehrzeilige Kommentare, die mit `/*` beginnen und mit `*/` enden. Sie können Kommentare zu bestimmten Zeilen in Ihrem Code und für Codeabschnitte eingeben.

1. **Befolgen Sie bewährte Methoden für Bicep.** Stellen Sie sicher, dass Ihre Bicep-Datei die Standardempfehlungen befolgt. Lesen Sie das Referenzdokument [Bewährte Methoden für Bicep](best-practices.md), um alle Informationen zu erhalten, die Sie möglicherweise noch nicht kennen.

## <a name="phase-4-test"></a>Phase 4: Testen

In der _Testphase_ der Migration Ihrer Ressourcen zu Bicep besteht das Ziel darin, die Integrität Ihrer migrierten Vorlagen zu überprüfen und eine Testbereitstellung durchzuführen.

Die Testphase besteht aus zwei Schritten, die Sie nacheinander ausführen:

1. **Ausführen des Was-wäre-wenn-Vorgangs der ARM-Vorlagenbereitstellung.** Um die konvertierten Vorlagen vor der Bereitstellung zu überprüfen, können Sie den [Azure Resource Manager-Was-wäre-wenn-Vorgang der Vorlagenbereitstellung](../templates/deploy-what-if.md) verwenden. Dabei wird der aktuelle Zustand Ihrer Umgebung mit dem gewünschten Zustand verglichen, der in der Vorlage definiert ist. Das Tool gibt die Liste der Änderungen aus, die auftreten, *ohne* die Änderungen auf Ihre Umgebung anzuwenden. Sie können Was-wäre-wenn sowohl mit inkrementellen als auch mit vollständigen Bereitstellungen verwenden. Auch wenn Sie ihre Vorlage im inkrementellen Modus bereitstellen möchten, empfiehlt es sich, Ihren Was-wäre-wenn-Vorgang im vollständigen Modus auszuführen.

1. **Ausführen einer Testbereitstellung.** Bevor Sie Ihre konvertierte Bicep-Vorlage in der Produktion verwenden, sollten Sie mehrere Testbereitstellungen ausführen. Wenn Sie über mehrere Umgebungen (Produktion, Entwicklung, Test) verfügen, sollten Sie versuchen, Ihre Vorlage zunächst in einer Ihrer Nicht-Produktionsumgebungen bereitzustellen. Vergleichen Sie nach der Bereitstellung die ursprünglichen Ressourcen mit den neuen Ressourcenbereitstellungen hinsichtlich ihrer Konsistenz.

## <a name="phase-5-deploy"></a>Phase 5: Bereitstellen

In der _Bereitstellungsphase_ der Migration Ihrer Ressourcen zu Bicep besteht das Ziel darin, Ihre endgültige Bicep-Datei in der Produktion bereitzustellen.

Die Bereitstellungsphase besteht aus vier Schritten, die Sie in der folgenden Reihenfolge ausführen:

1. **Vorbereiten eines Rollbackplans.** Die Möglichkeit zur Wiederherstellung nach einer fehlerhaften Bereitstellung ist entscheidend. Entwickeln Sie einen Plan zum Rollback, falls Breaking Changes in Ihre Umgebungen eingeführt werden. Erfassen Sie die Ressourcentypen, die bereitgestellt werden, z. B. virtuelle Computer, Web-Apps und Datenbanken. Die Datenebene jeder Ressource sollte ebenfalls berücksichtigt werden. Haben Sie eine Möglichkeit, einen virtuellen Computer und seine Daten wiederherzustellen? Haben Sie eine Methode, um eine Datenbank nach Löschung wiederherzustellen? Ein gut entwickelter Rollbackplan trägt dazu bei, die Ausfallzeit möglichst gering zu halten, wenn Probleme bei einer Bereitstellung auftreten.

1. **Ausführen des Was-wäre-wenn-Vorgangs für die Produktion.** Bevor Sie Ihre endgültige Bicep-Datei in der Produktion bereitstellen, führen Sie den Was-wäre-wenn-Vorgang für Ihre Produktionsumgebung aus, stellen Sie sicher, dass Sie Produktionsparameterwerte verwenden, und erwägen Sie, die Ergebnisse zu dokumentieren.

1. **Manuelle Bereitstellung.** Wenn Sie die konvertierte Vorlage in einer Pipeline verwenden möchten, z. B. in [Azure DevOps](add-template-to-azure-pipelines.md) oder [GitHub Actions](deploy-github-actions.md), sollten Sie die Bereitstellung zuerst auf Ihrem lokalen Computer ausführen. Es ist besser, die Funktionalität der Vorlage zu überprüfen, bevor Sie sie Ihrer Produktionspipeline hinzufügen. Auf diese Weise können Sie schnell reagieren, wenn ein Problem vorliegt.

1. **Ausführen von Feuerproben.** Nach Abschluss der Bereitstellung empfiehlt es sich, eine Reihe von *Feuerproben* durchzuführen – einfache Überprüfungen, die sicherstellen, dass Ihre Anwendung oder Ihre Workload ordnungsgemäß funktioniert. Testen Sie beispielsweise, ob auf Ihre Web-App über normale Zugriffskanäle wie das öffentliche Internet oder über ein Unternehmens-VPN zugegriffen werden kann. Versuchen Sie für Datenbanken, eine Datenbankverbindung herzustellen und eine Reihe von Abfragen auszuführen. Melden Sie sich für virtuelle Computer bei der VM an, und stellen Sie sicher, dass alle Dienste aktiv sind und ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

Um mehr über den Bicep-Dekompilierer zu erfahren, siehe [Dekompilieren von ARM-Vorlagen JSON nach Bicep](decompile.md).
