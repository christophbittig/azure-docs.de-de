---
title: Erstellen eines benutzerdefinierten Conda-Kanals für die Paketverwaltung
description: Erfahren Sie, wie Sie einen Conda-Kanal für die Paketverwaltung erstellen.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 937bfbf1ab1f874c6582b005ee0f7376ecf46235
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340306"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Erstellen eines benutzerdefinierten Conda-Kanals für die Paketverwaltung 
Beim Installieren von Python-Paketen verwendet der Conda-Paket-Manager Kanäle, um nach Paketen zu suchen. Das Erstellen eines benutzerdefinierten Conda-Kanals kann aus verschiedenen Gründen erforderlich sein. Beispielsweise stellen Sie Folgendes fest:

- Für Ihren Arbeitsbereich ist der Schutz vor Datenexfiltration aktiviert, und ausgehende Verbindungen werden blockiert.  
- Sie verfügen über Pakete, die nicht in öffentliche Repositorys hochgeladen werden sollen.
- Sie möchten ein alternatives Repository für die Benutzer in Ihrem Arbeitsbereich einrichten.

Dieser Artikel bietet eine Schritt-für-Schritt-Anleitung, die Sie beim Erstellen eines benutzerdefinierten Conda-Kanals in Ihrem Azure Data Lake Storage-Konto unterstützt.

## <a name="set-up-your-local-machine"></a>Einrichten des lokalen Computers

1. Installieren Sie Conda auf Ihrem lokalen Computer. Sie können der [Azure Synapse Spark-Runtime](./apache-spark-version-support.md) die Conda-Version entnehmen, die in derselben Runtime verwendet wird.
   
2. Um einen benutzerdefinierten Kanal zu erstellen, installieren Sie conda-build.
```
conda install conda-build
```
3. Organisieren Sie alle Pakete für die Plattform, die Sie bereitstellen möchten. In diesem Beispiel wird das Anaconda-Archiv auf dem lokalen Computer installiert.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3  
```

4. Sie können [diese Vorlage](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) herunterladen, um eine ähnliche Umgebung wie die in der Azure Synapse-Runtime erstellte Umgebung zu erstellen. Möglicherweise gibt es geringfügige Unterschiede zwischen der Vorlage und der tatsächlichen Azure Synapse-Umgebung. Nach dem Herunterladen können Sie den folgenden Befehl ausführen:
```
apt-get -yq install gcc g++
conda env update --prune -f base_environment.yml
```

## <a name="mount-the-storage-account-onto-your-machine"></a>Einbinden des Speicherkontos auf dem Computer
Als Nächstes binden Sie das Azure Data Lake Storage Gen2-Konto auf dem lokalen Computer ein. Dieser Vorgang kann auch mit einem WASB-Konto ausgeführt werden. Wir verwenden jedoch ein Beispiel für das ADLSg2-Konto. 
 
Weitere Informationen zum Einbinden des Speicherkontos auf dem lokalen Computer finden Sie auf [dieser Seite](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. Sie können blobfuse aus dem Linux-Softwarerepository für Microsoft-Produkte installieren.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<storage-account-name>
export AZURE_STORAGE_SAS_TOKEN="<SAS>" 
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net
```

2. Erstellen Sie den Bereitstellungspunkt (```mkdir /path/to/mount```), und binden Sie einen Blobcontainer mit blobfuse ein. In diesem Beispiel verwenden wir den Wert **privatechannel** für die Variable **mycontainer**.
   
```
sudo mkdir /home/trusted-service-user/privatechannel 
sudo mkdir -p /mnt/blobfusetmp 
blobfuse /home/trusted-service-user/privatechannel --container-name=privatechannel --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo chown trusted-service-user /mnt/blobfusetmp 
```
## <a name="create-the-channel"></a>Erstellen des Kanals
In den nächsten Schritten erstellen wir einen benutzerdefinierten Conda-Kanal.

1. Erstellen Sie auf Ihrem lokalen Computer ein Verzeichnis, in dem Sie alle Pakete für den benutzerdefinierten Kanal organisieren können. Organisieren Sie alle ```tar.bz2```-Pakete aus https://repo.anaconda.com/pkgs/main/linux-64/ im Unterverzeichnis. Schließen Sie auch alle abhängigen tar.bz2-Pakete ein.
   
```

cd ~/privatechannel/ 
mkdir -P channel/linux64 

<Add all .tar.bz2 from https://repo.anaconda.com/pkgs/main/linux-64/> 
// Note: Add all dependent .tar.bz2 as well 

cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 
conda index channel/noarch 
conda index channel/linux-64 
conda index channel
```

2. Jetzt können Sie das Speicherkonto überprüfen, in dem das Verzeichnis ```privatechannel/channel``` im Beispiel erstellt wurde.

>[!Note]
> Das einem Container zugeordnete SAS-Token wird in Conda ignoriert. Daher müssen Sie den Container „privatechannel“ als Container mit öffentlichem Zugriff markieren.


Weitere Informationen finden Sie auch im Abschnitt des [Conda-Benutzerhandbuchs](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) zum Erstellen benutzerdefinierter Kanäle. 

## <a name="storage-account-permissions"></a>Speicherkontoberechtigungen
Jetzt müssen die Berechtigungen für das Speicherkonto überprüft werden. Um diese Berechtigungen festzulegen, navigieren Sie zu dem Pfad, in dem der benutzerdefinierte Kanal erstellt wird. Erstellen Sie dann ein SAS-Token für ```privatechannel```, das über Lese-, Schreib- und Ausführungsberechtigungen verfügt. 

Der Kanalname ist jetzt die Blob-SAS-URL, die in diesem Prozess generiert wird.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Erstellen einer Conda-Umgebungskonfigurationsdatei
Überprüfen Sie abschließend den Installationsvorgang, indem Sie die Conda-Beispieldatei ```environment.yml``` erstellen. Wenn Sie über einen Arbeitsbereich mit aktiviertem Schutz vor Datenexfiltration verfügen, müssen Sie in der Umgebungsdatei den Kanal ``nodefaults`` angeben.

Hier ist ein Beispiel für eine Conda-Konfigurationsdatei:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Sobald Sie die Conda-Beispieldatei erstellt haben, können Sie eine virtuelle Conda-Umgebung erstellen. Dies können Sie durch Ausführen des folgenden Befehls lokal überprüfen:

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Nachdem Sie den benutzerdefinierten Kanal überprüft haben, können Sie mit der [Python-Poolverwaltung](./apache-spark-manage-python-packages.md) die Bibliotheken im Apache Spark-Pool aktualisieren.

## <a name="next-steps"></a>Nächste Schritte
- Anzeigen der Standardbibliotheken: [Versionsunterstützung für Apache Spark](apache-spark-version-support.md)
- Verwalten von Python-Paketen: [Python-Paketverwaltung](./apache-spark-manage-python-packages.md)

