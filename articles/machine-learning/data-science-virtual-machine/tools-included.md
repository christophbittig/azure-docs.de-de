---
title: In Azure Data Science Virtual Machine enthaltene Tools
titleSuffix: Azure Data Science Virtual Machine
description: Hier finden Sie eine Liste der Tools, die in den Windows- und Ubuntu-DSVM-Images enthalten sind.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/12/2021
ms.custom: contperf-fy20q4
ms.openlocfilehash: ba7006c9dc266819e4c965ae3a72fc0f78506056
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649984"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Welche Tools sind in Azure Data Science Virtual Machine enthalten?

Data Science Virtual Machine stellt eine einfache Möglichkeit dar, Daten zu erkunden und maschinelles Lernen in der Cloud zu nutzen. Data Science Virtual Machine-Instanzen sind mit dem gesamten Betriebssystem, Sicherheitspatches, Treibern und gängiger Data Science- und Entwicklungssoftware vorkonfiguriert. Bei der Hardwareumgebung reicht das Spektrum von kostengünstigeren CPU-zentrischen Computern bis hin zu Hochleistungscomputern mit mehreren GPUs, NVMe-Speicher und umfangreichem Arbeitsspeicher. Bei Computern mit GPUs sind alle Treiber installiert, die Version aller Frameworks für maschinelles Lernen ist jeweils abgestimmt, um die GPU-Kompatibilität zu gewährleisten, und die Beschleunigung ist in jeder Anwendungssoftware mit GPU-Unterstützung aktiviert.

In Data Science Virtual Machine sind die nützlichsten Data Science-Tools vorinstalliert. 

## <a name="build-deep-learning-and-machine-learning-solutions"></a>Erstellen von Lösungen für maschinelles Lernen und Deep Learning

| Tool | DSVM unter Windows Server 2019 | DSVM unter Ubuntu 18.04 | Hinweise zur Verwendung |
|--|:-:|:-:|:-:|
| [CUDA-, cuDNN-, NVIDIA-Treiber](https://developer.nvidia.com/cuda-toolkit) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [CUDA-, cuDNN- und NVIDIA-Treiber in DSVM](./dsvm-tools-deep-learning-frameworks.md#cuda-cudnn-nvidia-driver) |
| [Horovod](https://github.com/horovod/horovod) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [Horovod in DSVM](./dsvm-tools-deep-learning-frameworks.md#horovod) |
| [NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [nvidia-smi in DSVM](./dsvm-tools-deep-learning-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| [PyTorch](https://pytorch.org) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [PyTorch in DSVM](./dsvm-tools-deep-learning-frameworks.md#pytorch) |
| [TensorFlow](https://www.tensorflow.org) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [TensorFlow in DSVM](./dsvm-tools-deep-learning-frameworks.md#tensorflow) |
| Integration mit [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (Python) | <span class='green-check'>&#9989;</span></br> (Python SDK-Beispiele) | <span class='green-check'>&#9989;</span></br> (Python SDK, CLI, Beispiele) | [Azure ML-SDK](./dsvm-tools-data-science.md#azure-machine-learning-sdk-for-python) |
| [XGBoost](https://github.com/dmlc/xgboost) | <span class='green-check'>&#9989;</span></br> (CUDA-Support) | <span class='green-check'>&#9989;</span></br> (CUDA-Support) | [XGBoost in DSVM](./dsvm-tools-data-science.md#xgboost) |
| [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> | [Vowpal Wabbit in DSVM](./dsvm-tools-data-science.md#vowpal-wabbit) |
| [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | <span class='red-x'>&#10060;</span> | <span class='red-x'>&#10060;</span> |  |
| LightGBM | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (GPU-, MPI-Unterstützung) |  |
| H2O | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| CatBoost | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Intel MKL | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| OpenCV | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Dlib | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Docker | <span class='green-check'>&#9989;</span> <br/> (Nur Windows-Container) | <span class='green-check'>&#9989;</span> |  |
| Nccl | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Rattle | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| ONNX-Runtime | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |



## <a name="store-retrieve-and-manipulate-data"></a>Speichern, Abrufen und Bearbeiten von Daten

| Tool | DSVM unter Windows Server 2019 | DSVM unter Ubuntu 18.04 | Hinweise zur Verwendung |
|--|-:|:-:|:-:|
| Relationale Datenbanken | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server in DSVM](./dsvm-tools-data-platforms.md#sql-server-developer-edition) |
| Datenbanktools | SQL Server Management Studio<br/> SQL Server Integration Services<br/> [bcp, sqlcmd](/sql/tools/command-prompt-utility-reference-database-engine) | [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (Abfragetool), <br /> bcp, sqlcmd <br /> ODBC/JDBC-Treiber |  |
| [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [Azure-Befehlszeilenschnittstelle](/cli/azure) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [AzCopy](../../storage/common/storage-use-azcopy-v10.md) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> | [AzCopy auf DSVM](./dsvm-tools-ingestion.md#azcopy) |
| [Blob-FUSE-Treiber](https://github.com/Azure/azure-storage-fuse) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [blobfuse in DSVM](./dsvm-tools-ingestion.md#blobfuse) |
| [Azure Cosmos DB: Datenmigrationstool](../../cosmos-db/import-data.md) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [Cosmos DB in DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| Unix/Linux-Befehlszeilentools | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Apache Spark 3.1 (eigenständige Instanz) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> |  |

## <a name="program-in-python-r-julia-and-nodejs"></a>Programm in Python, R, Julia und Node.js

| Tool | DSVM unter Windows Server 2019 | DSVM unter Ubuntu 18.04 | Hinweise zur Verwendung |
|--|:-:|:-:|:-:|
| [CRAN-R](https://cran.r-project.org/) mit beliebten vorinstallierten Paketen | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Anaconda Python](https://www.continuum.io/) mit beliebten vorinstallierten Paketen | <span class='green-check'>&#9989;</span><br/> (Miniconda) | <span class='green-check'>&#9989;</span></br> (Miniconda) |  |
| [Julia (Julialang)](https://julialang.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterHub (Notebook-Server für mehrere Benutzer) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterLab (Notebook-Server für mehrere Benutzer) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| Node.js | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Jupyter Notebook-Server](https://jupyter.org/) mit folgenden Kernels: | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span> | [Jupyter Notebook-Beispiele](./dsvm-samples-and-walkthroughs.md) |
| &nbsp;&nbsp;&nbsp;&nbsp; R |  |  | [R Jupyter-Beispiele](./dsvm-samples-and-walkthroughs.md#r-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Python |  |  | [Python Jupyter-Beispiele](./dsvm-samples-and-walkthroughs.md#python-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Julia |  |  | [Julia Jupyter-Beispiele](./dsvm-samples-and-walkthroughs.md#julia-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; PySpark |  |  | [pySpark Jupyter-Beispiele](./dsvm-samples-and-walkthroughs.md#sparkml) |

**DSVM unter Ubuntu 18.04 und DSVM unter Windows Server 2019** verfügen über die folgenden Jupyter-Kernel:-</br> 
* Python 3.8 – Standard</br>  
* Python 3.8 – PyTorch</br>  
* Python 3.8 – TensorFlow</br>  
* Python 3.6 – AzureML – TensorFlow</br>  
* Python 3.6 – AzureML – PyTorch</br>  
* Python 3.6 – AzureML – AutoML</br>  
* R</br>  
* Python 3.7 – Spark (lokal)</br>  
* Julia 1.2.0</br>  
* R Spark – HDInsight</br>  
* Scala Spark – HDInsight</br>  
* Python 3 Spark – HDInsight</br>  

**DSVM unter Ubuntu 18.04 und DSVM unter Windows Server 2019** verfügen über die folgenden Conda-Umgebungen:-</br> 
* py38_default  </br>
* py38_tensorflow </br> 
* py38_pytorch  </br>
* azureml_py36_tensorflow  </br>
* azureml_py36_pytorch  </br>
* azureml_py36_automl  </br>


## <a name="use-your-preferred-editor-or-ide"></a>Verwenden Ihres bevorzugten Editors oder Ihrer bevorzugten IDE

| Tool | DSVM unter Windows Server 2019 | DSVM unter Ubuntu 18.04 | Hinweise zur Verwendung |
|--|:-:|:-:|:-:|
| [Editor (Notepad++)](https://notepad-plus-plus.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Nano](https://www.nano-editor.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Visual Studio 2019 Community Edition](https://www.visualstudio.com/community/) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [Visual Studio in DSVM](dsvm-tools-development.md#visual-studio-community-edition) |
| [Visual Studio Code](https://code.visualstudio.com/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [Visual Studio Code in DSVM](./dsvm-tools-development.md#visual-studio-code) |
| [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [RStudio Desktop in DSVM](./dsvm-tools-development.md#rstudio-desktop) |
| [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) <br/> (Standardmäßig deaktiviert) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [PyCharm in DSVM](./dsvm-tools-development.md#pycharm) |
| [IntelliJ IDEA](https://www.jetbrains.com/idea/) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [Vim](https://www.vim.org) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Git](https://git-scm.com/) und Git Bash | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [OpenJDK](https://openjdk.java.net) 11 | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| .NET Framework | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Azure SDK | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |

## <a name="organize--present-results"></a>Organisieren und Präsentieren von Ergebnissen

| Tool | DSVM unter Windows Server 2019 | DSVM unter Ubuntu 18.04 | Hinweise zur Verwendung |
|--|:-:|:-:|:-:|
| [Microsoft 365](https://www.microsoft.com/microsoft-365) (Word, Excel, PowerPoint) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Microsoft Teams](https://www.microsoft.com/microsoft-teams) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Power BI Desktop](https://powerbi.microsoft.com/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Microsoft Edge Browser | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
