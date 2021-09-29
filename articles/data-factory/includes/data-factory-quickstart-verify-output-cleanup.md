---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 18ff33287be7f5763a23cdf33c6e023501645299
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056872"
---
## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Die Pipeline erstellt den Ausgabeordner automatisch im Blobcontainer „adftutorial“. Anschließend wird die Datei „emp.txt“ aus dem Eingabe- in den Ausgabeordner kopiert. 

1. Wählen Sie im Azure-Portal auf der Seite des Containers **adftutorial** die Option **Aktualisieren** aus, um den Ausgabeordner anzuzeigen. 
    
    :::image type="content" source="media/data-factory-quickstart-verify-output-cleanup/output-refresh.png" alt-text="Screenshot: Containerseite, die Sie aktualisieren können":::

2. Wählen Sie in der Ordnerliste **output** aus. 

3. Überprüfen Sie, ob die Datei **emp.txt** in den Ausgabeordner kopiert wurde. 

    :::image type="content" source="media/data-factory-quickstart-verify-output-cleanup/output-file.png" alt-text="Screenshot: Inhalt des Ausgabeordners":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die im Rahmen dieser Schnellstartanleitung erstellten Ressourcen können auf zwei Arten bereinigt werden. Sie können die [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) einschließlich aller darin enthaltenen Ressourcen löschen. Falls die anderen Ressourcen erhalten bleiben sollen, löschen Sie nur die Data Factory, die Sie in diesem Tutorial erstellt haben.

Wenn Sie eine Ressourcengruppe löschen, werden alle Ressourcen einschließlich enthaltener Data Factorys gelöscht. Führen Sie den folgenden Befehl aus, um die gesamte Ressourcengruppe zu löschen: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> Das Löschen einer Ressourcengruppe kann einige Zeit in Anspruch nehmen. Bitte haben Sie etwas Geduld.

Wenn Sie nur die Data Factory und nicht die gesamte Ressourcengruppe löschen möchten, führen Sie den folgenden Befehl aus: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```