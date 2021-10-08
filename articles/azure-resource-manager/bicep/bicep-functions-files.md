---
title: 'Bicep-Funktionen: Dateien'
description: Beschreibt die Funktionen, die in einer Bicep-Datei zum Laden von Inhalten aus einer Datei verwendet werden.
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: 10c4bb501945bdcd5b502797e8383dbea1d0f63c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552674"
---
# <a name="file-functions-for-bicep"></a>Dateifunktionen für Bicep

In diesem Artikel werden die Bicep-Funktionen zum Laden von Inhalten aus externen Dateien beschrieben.

## <a name="loadfileasbase64"></a>loadFileAsBase64

`loadFileAsBase64(filePath)`

Lädt die Datei als Base64-Zeichenfolge. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| filePath | Ja | Zeichenfolge | Der Pfad zur Datei, die geladen werden soll. Der Pfad ist relativ zur bereitgestellten Bicep-Datei. |

### <a name="remarks"></a>Bemerkungen

Verwenden Sie diese Funktion, wenn Sie über binären Inhalt verfügen, den Sie in die Bereitstellung einbeziehen möchten. Anstatt die Datei manuell in eine Base64-Zeichenfolge zu codieren und Ihrer Bicep-Datei hinzufügen zu müssen, laden Sie die Datei mit dieser Funktion. Die Datei wird geladen, wenn die Bicep-Datei in eine JSON-Vorlage kompiliert wird. Während der Bereitstellung enthält die JSON-Vorlage den Inhalt der Datei als hartcodierte Zeichenfolge.

Diese Funktion erfordert die **Bicep-Version 0.4.412 oder höher**. 

Die maximal zulässige Größe der Datei beträgt **96 KB**.

### <a name="return-value"></a>Rückgabewert

Die Datei als Base64-Zeichenfolge.

## <a name="loadtextcontent"></a>loadTextContent

`loadTextContent(filePath, [encoding])`

Lädt den Inhalt der angegebenen Datei als Zeichenfolge. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| filePath | Ja | Zeichenfolge | Der Pfad zur Datei, die geladen werden soll. Der Pfad ist relativ zur bereitgestellten Bicep-Datei. |
| encoding | Nein | Zeichenfolge | Die Codierung der Datei. Standardwert: `utf-8`. Verfügbare Optionen: `iso-8859-1`, `us-ascii`, `utf-16`, `utf-16BE` oder `utf-8`.  |

### <a name="remarks"></a>Bemerkungen

Verwenden Sie diese Funktion, wenn Sie über Inhalte verfügen, die eher in einer separaten Datei gespeichert sind. Laden Sie den Inhalt mit dieser Funktion, anstatt den Inhalt in Ihrer Bicep-Datei zu duplizieren. Beispielsweise können Sie ein Bereitstellungsskript aus einer Datei laden. Die Datei wird geladen, wenn die Bicep-Datei in die JSON-Vorlage kompiliert wird. Während der Bereitstellung enthält die JSON-Vorlage den Inhalt der Datei als hartcodierte Zeichenfolge.

Beim Laden einer JSON-Datei können Sie die [json](bicep-functions-object.md#json)-Funktion mit der Funktion „loadTextContent“ verwenden, um ein JSON-Objekt zu erstellen. In VS Code sind die Eigenschaften des geladenen Objekts in IntelliSense verfügbar. Beispielsweise können Sie eine Datei mit Werten erstellen, die für viele Bicep-Dateien freigegeben werden. Ein Beispiel finden Sie in diesem Artikel.

Diese Funktion erfordert die **Bicep-Version 0.4.412 oder höher**.

Die maximal zulässige Größe der Datei beträgt **131.072 Zeichen**, einschließlich Zeilenenden.

### <a name="return-value"></a>Rückgabewert

Der Inhalt der Datei als Zeichenfolge.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird ein Skript aus einer Datei geladen und für ein Bereitstellungsskript verwendet.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loaddeploymentscript.bicep" highlight="13" :::

Im nächsten Beispiel erstellen Sie eine JSON-Datei, die Werte enthält, die Sie für eine Netzwerksicherheitsgruppe verwenden möchten.

::: code language="json" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/nsg-security-rules.json" :::

Sie laden diese Datei und konvertieren sie in ein JSON-Objekt. Sie verwenden das Objekt, um der Ressource entsprechende Werte zuzuweisen.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loadsharedrules.bicep" highlight="3,13-21" :::

Sie können die Wertedatei in anderen Bicep-Dateien wiederverwenden, die eine Netzwerksicherheitsgruppe bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](./file.md).
