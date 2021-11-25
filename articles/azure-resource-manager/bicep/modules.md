---
title: Bicep-Module
description: Hier wird beschrieben, wie ein Modul in einer Bicep-Datei definiert und wie Modulbereiche eingesetzt werden.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: a8aedd784875fccbad81957550380cc4fa236616
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551272"
---
# <a name="bicep-modules"></a>Bicep-Module

Mit Bicep können Sie Bereitstellungen in Modulen organisieren. Bei einem Modul handelt es sich um eine Bicep-Datei, die von einer anderen Bicep-Datei bereitgestellt wird. Mit Modulen verbessern Sie die Lesbarkeit Ihrer Bicep-Dateien, indem Sie komplexe Details Ihrer Bereitstellung kapseln. Sie können Module auch problemlos für verschiedene Bereitstellungen wiederverwenden.

Um Module für Personen innerhalb Ihrer Organisation freizugeben, [erstellen Sie eine private Registrierung](private-module-registry.md). Module in der Registrierung sind nur für Benutzer mit den richtigen Berechtigungen verfügbar.

Bicep-Module werden in eine einzelne ARM-Vorlage (Azure Resource Manager) mit [geschachtelten Vorlagen](../templates/linked-templates.md#nested-template) konvertiert.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen zu Modulen und praktische Anleitungen finden Sie unter [Erstellen von zusammensetzbaren Bicep-Dateien mithilfe von Modulen](/learn/modules/create-composable-bicep-files-using-modules/) auf **Microsoft Learn**.

## <a name="definition-syntax"></a>Definitionssyntax

Die grundlegende Syntax zum Definieren eines Moduls ist:

```bicep
module <symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}
```

Ein einfaches, reales Beispiel würde also folgendermaßen aussehen:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" :::

Verwenden Sie den symbolischen Namen, um in einem anderen Teil der Bicep-Datei auf das Modul zu verweisen. Beispielsweise können Sie den symbolischen Namen verwenden, um die Ausgabe eines Moduls zu erhalten. Der symbolische Name kann die Zeichen a–z, A–Z und 0–9 sowie Unterstriche (`_`) enthalten. Der Name darf nicht mit einer Zahl beginnen. Ein Modul darf nicht denselben Namen wie ein Parameter, eine Variable oder eine Ressource haben.

Der Pfad kann entweder eine lokale Datei oder eine Datei in einer Registrierung sein. Weitere Informationen finden Sie unter [Pfad zum Modul](#path-to-module).

Die Eigenschaft **Name** ist erforderlich. Sie wird zum Namen der geschachtelten Bereitstellungsressource in der generierten Vorlage.

Wenn Sie einen **Bereich angeben müssen**, der sich vom Bereich für die Hauptdatei unterscheiden soll, fügen Sie die Bereichseigenschaft hinzu. Weitere Informationen finden Sie unter [Modulbereich festlegen](#set-module-scope).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/scope-definition.bicep" highlight="4" :::

Um **ein Modul bedingt bereitzustellen**, fügen Sie einen `if`-Ausdruck hinzu. Die Verwendung ähnelt der [bedingten Bereitstellung einer Ressource](conditional-resource-deployment.md).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/conditional-definition.bicep" highlight="2" :::

Um **mehrere Instanzen** eines Moduls bereitzustellen, fügen Sie den `for`-Ausdruck hinzu. Sie können den Decorator `batchSize` verwenden, um anzugeben, ob die Instanzen nacheinander oder parallel bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Iterative Schleifen in Bicep](loops.md).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/iterative-definition.bicep" highlight="3" :::

Module werden wie Ressourcen parallel bereitgestellt, es sei denn, sie hängen von anderen Modulen oder Ressourcen ab. In der Regel müssen Sie keine Abhängigkeiten festlegen, da sie implizit bestimmt werden. Wenn Sie eine explizite Abhängigkeit festlegen müssen, können Sie `dependsOn` der Moduldefinition hinzufügen. Weitere Informationen zu Abhängigkeiten finden Sie unter [Ressourcenabhängigkeiten](resource-declaration.md#dependencies).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/dependsOn-definition.bicep" highlight="6-8" :::

## <a name="path-to-module"></a>Pfad zum Modul

Die Datei für das Modul kann entweder eine lokale Datei oder eine externe Datei in einer Bicep-Modulregistrierung sein. Beide Optionen werden nachstehend vorgestellt.

### <a name="local-file"></a>Lokale Datei

Wenn das Modul eine **lokale Datei** ist, geben Sie einen relativen Pfad zu dieser Datei an. Alle Pfade in Bicep müssen mithilfe des Schrägstrichs (/) als Verzeichnistrennzeichen angegeben werden, um eine plattformübergreifende konsistente Kompilierung sicherzustellen. Der umgekehrte Schrägstrich von Windows (\\) wird nicht unterstützt. Pfade können Leerzeichen enthalten.

Verwenden Sie beispielsweise Folgendes, um eine Datei bereitzustellen, die sich im Verzeichnis eine Ebene über Ihrer Hauptdatei befindet:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" highlight="1" :::

### <a name="file-in-registry"></a>Datei in der Registrierung

Wenn Sie [ein Modul in einer Registrierung veröffentlicht haben](bicep-cli.md#publish), können Sie einen Link zu diesem Modul erstellen. Geben Sie den Namen für die Azure Container Registry und einen Pfad zum Modul an. Geben Sie den Modulpfad mit der folgenden Syntax an:

```bicep
module <symbolic-name> 'br:<registry-name>.azurecr.io/<file-path>:<tag>' = {
```

- **br** ist der Schemaname für eine Bicep-Registrierung.
- Der **Dateipfad** wird in der Azure Container Registry mit `repository` bezeichnet. Der **Dateipfad** kann Segmente enthalten, die durch das Zeichen `/` getrennt sind.
- **tag** wird verwendet, um eine Version für das Modul anzugeben.

Zum Beispiel:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/registry-definition.bicep" highlight="1" :::

Wenn Sie auf ein Modul in einer Registrierung verweisen, ruft die Bicep-Erweiterung in Visual Studio Code automatisch [bicep restore](bicep-cli.md#restore) auf, um das externe Modul in den lokalen Cache zu kopieren. Die Wiederherstellung des externen Moduls dauert einen Moment. Wenn IntelliSense für das Modul nicht sofort funktioniert, warten Sie, bis die Wiederherstellung abgeschlossen ist.

Der vollständige Pfad für ein Modul in einer Registrierung kann lang sein. Anstatt bei jeder Verwendung des Moduls den vollständigen Pfad anzugeben, können Sie [Aliase in der Datei „bicepconfig.json“ konfigurieren](bicep-config-modules.md#aliases-for-modules). Die Aliase erleichtern das Verweisen auf das Modul. Mit einem Alias können Sie z. B. den Pfad wie folgt kürzen:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/alias-definition.bicep" highlight="1" :::

## <a name="parameters"></a>Parameter

Die Parameter, die Sie in Ihrer Moduldefinition angeben, entsprechen den Parametern in der Bicep-Datei.

Das folgende Bicep-Beispiel enthält drei Parameter: storagePrefix, storageSKU und location. Der storageSKU-Parameter hat einen Standardwert, sodass Sie während der Bereitstellung keinen Wert für diesen Parameter angeben müssen.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="3,15,17" :::

Um das vorherige Beispiel als Modul zu verwenden, geben Sie Werte für diese Parameter an.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="14-17" :::

## <a name="set-module-scope"></a>Festlegen des Modulbereichs

Beim Deklarieren eines Moduls können Sie für das Modul einen Bereich festlegen, der sich vom Bereich für die enthaltene Bicep-Datei unterscheidet. Legen Sie den Bereich für das Modul mithilfe der Eigenschaft `scope` fest. Wenn die scope-Eigenschaft nicht angegeben wird, wird das Modul im Zielbereich des übergeordneten Moduls bereitgestellt.

Die folgende Bicep-Datei erstellt eine Ressourcengruppe und ein Speicherkonto in dieser Ressourcengruppe. Die Datei wird in einem Abonnement bereitgestellt, aber der Bereich des Moduls ist die neue Ressourcengruppe.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/rg-and-storage.bicep" highlight="2,12,19" :::

Im nächsten Beispiel werden Speicherkonten in zwei verschiedenen Ressourcengruppen bereitgestellt. Beide Ressourcengruppen müssen bereits vorhanden sein.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/scope-two-resource-groups.bicep" highlight="1,13,22" :::

Legen Sie für die Bereichseigenschaft ein gültiges Bereichsobjekt fest. Wenn Ihre Bicep-Datei eine Ressourcengruppe, ein Abonnement oder eine Verwaltungsgruppe bereitgestellt, können Sie den Bereich für ein Modul auf den symbolischen Namen für diese Ressource festlegen. Sie können auch die Bereichsfunktionen verwenden, um einen gültigen Bereich zu erhalten.

Dabei handelt es sich um die folgenden Funktionen:

- [Ressourcengruppe](bicep-functions-scope.md#resourcegroup)
- [Abonnement](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

Im folgenden Beispiel wird der Bereich mithilfe der Funktion `managementGroup` festgelegt:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/function-scope.bicep" highlight="5" :::

## <a name="output"></a>Output

Sie können Werte aus einem Modul abrufen und in der Bicep-Hauptdatei verwenden. Um einen Ausgabewert von einem Modul zu erhalten, verwenden Sie die `outputs`-Eigenschaft für das Modulobjekt.

Im ersten Beispiel werden ein Speicherkonto erstellt und die primären Endpunkte zurückgegeben.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="33" :::

Bei Verwendung als Modul können Sie diesen Ausgabewert erhalten.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="20" :::

## <a name="next-steps"></a>Nächste Schritte

- Ein Tutorial finden Sie unter [Erstellen Ihrer ersten Bicep-Vorlage](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).
- Mithilfe der Funktion [getSecret](bicep-functions-resource.md#getsecret) können Sie einen vertraulichen Wert an ein Modul übergeben.
