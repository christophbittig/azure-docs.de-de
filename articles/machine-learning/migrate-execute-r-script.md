---
title: 'ML Studio (Classic): Migrieren von „R-Skript ausführen“'
description: Neuerstellen von „R-Skript ausführen“-Modulen aus Studio (Classic) zur Ausführung in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 7d8f014b752d4ff0838195671ef8c919a164ebef
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699128"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Migrieren von „R-Skript ausführen“-Modulen in Studio (Classic)

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

In diesem Artikel erfahren Sie, wie Sie ein **R-Skript ausführen**-Modul aus Studio (Classic) in Azure Machine Learning neu erstellen.

Weitere Informationen zur Migration aus Studio (Classic) finden Sie im Artikel mit der [Übersicht über die Migration](migrate-overview.md).

## <a name="execute-r-script"></a>Execute R Script

Der Azure Machine Learning-Designer wird jetzt unter Linux ausgeführt. Studio (Classic) wird unter Windows ausgeführt. Aufgrund der Änderung der Plattform müssen Sie das Modul **R-Skript ausführen** bei der Migration anpassen. Andernfalls treten in der Pipeline Fehler auf.

Zum Migrieren eines **R-Skript ausführen**-Moduls aus Studio (Classic) müssen Sie die Schnittstellen `maml.mapInputPort` und `maml.mapOutputPort` durch Standardfunktionen ersetzen.

In der folgenden Tabelle sind die Änderungen am R Script-Modul zusammengefasst:

|Funktion|Studio (klassisch)|Azure Machine Learning-Designer|
|---|---|---|
|Skriptschnittstelle|`maml.mapInputPort` und `maml.mapOutputPort`|Funktionsschnittstelle|
|Plattform|Windows|Linux|
|Zugriff über das Internet |Nein|Ja|
|Arbeitsspeicher|14 GB|Abhängig von Compute-SKU|

### <a name="how-to-update-the-r-script-interface"></a>Aktualisieren der R-Skriptschnittstelle

Inhalt eines **R-Skript ausführen**-Beispielmoduls in Studio (Classic):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Nachfolgend ist der aktualisierte Inhalt im Designer dargestellt. Beachten Sie, dass `maml.mapInputPort` und `maml.mapOutputPort` durch die Standardfunktionsschnittstelle `azureml_main` ersetzt wurden. 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Weitere Informationen finden Sie in der [Referenz zum Modul „R-Skript ausführen“](/algorithm-module-reference/execute-r-script.md).

### <a name="install-r-packages-from-the-internet"></a>Installieren von R-Paketen über das Internet

Mit dem Azure Machine Learning-Designer können Sie Pakete direkt über CRAN installieren.

Dies ist eine Verbesserung gegenüber Studio (Classic). Da Studio (Classic) in einer Sandboxumgebung ohne Internetzugriff ausgeführt wird, mussten Skripts in einem ZIP-Bundle hochgeladen werden, um weitere Pakete zu installieren. 

Verwenden Sie den folgenden Code zum Installieren von CRAN-Paketen im **R-Skript ausführen**-Modul des Designers:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie „R-Skript ausführen“-Module zu Azure Machine Learning migrieren.

Weitere Informationen finden Sie in den anderen Artikeln der Reihe zur Migration von Studio (Classic):

1. [Übersicht über die Migration](migrate-overview.md).
1. [Migrieren eines Datasets](migrate-register-dataset.md)
1. [Neuerstellen einer Studio (Classic)-Trainingspipeline](migrate-rebuild-experiment.md)
1. [Neuerstellen eines Studio (Classic)-Webdiensts](migrate-rebuild-web-service.md)
1. [Integrieren eines Machine Learning-Webdiensts in Clientanwendungen](migrate-rebuild-integrate-with-client-app.md)
1. **Migrieren von „R-Skript ausführen“-Modulen**