---
title: Aktivieren von Azure Machine Learning Studio in einem virtuellen Netzwerk
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Azure Machine Learning Studio konfigurieren, um auf Daten zuzugreifen, die in einem virtuellen Netzwerk gespeichert sind.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/10/2021
ms.custom: contperf-fy20q4, tracking-python, security
ms.openlocfilehash: b978955c375ff30f677a395ea549276b960a80d9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293096"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Verwenden von Azure Machine Learning Studio in einem virtuellen Netzwerk

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning Studio in einem virtuellen Netzwerk verwenden. Studio enthält Features wie automatisiertes maschinelles Lernen, den Designer und Datenbeschriftung. 

Einige der Funktionen von Studio sind in virtuellen Netzwerken standardmäßig deaktiviert. Wenn Sie diese Features aktivieren möchten, müssen Sie für Speicherkonten, die Sie in Studio verwenden möchten, die verwaltete Identität aktivieren. 

Die folgenden Vorgänge sind in einem virtuellen Netzwerk standardmäßig deaktiviert:

* Vorschau der Daten im Studio.
* Visualisieren von Daten im Designer.
* Bereitstellen eines Modells im Designer.
* Senden eines AutoML-Experiments.
* Starten eines Beschriftungsprojekts.

Das Studio unterstützt das Lesen von Daten aus den folgenden Datenspeichertypen in einem virtuellen Netzwerk:

* Azure Storage-Konto (Blob und Datei)
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL-Datenbank

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> - Erteilen der Studio-Berechtigung, auf Daten zuzugreifen, die in einem virtuellen Netzwerk gespeichert sind
> - Zugreifen auf Studio von einer Ressource innerhalb eines virtuellen Netzwerks aus
> - Erfahren Sie, wie sich das Studio auf die Speichersicherheit auswirkt.

> [!TIP]
> Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:
>
> * [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
> * [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
> * [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
> * [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
> * [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
> * [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)


## <a name="prerequisites"></a>Voraussetzungen

+ In der [Übersicht über die Netzwerksicherheit](how-to-network-security-overview.md) finden Sie Informationen zu gängigen Szenarien im Zusammenhang mit virtuellen Netzwerken sowie zur Architektur.

+ Ein bereits vorhandenes virtuelles Netzwerk und Subnetz, das verwendet werden kann

+ Ein vorhandener [Azure Machine Learning-Arbeitsbereich mit einem privaten Endpunkt](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Ein vorhandenes [Azure Storage-Konto](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts), das Ihrem virtuellen Netzwerk hinzugefügt wurde.

## <a name="limitations"></a>Einschränkungen

### <a name="azure-storage-account"></a>Azure Storage-Konto

Es gibt ein bekanntes Problem, bei dem der Standarddateispeicher nicht automatisch den Ordner `azureml-filestore` erstellt, der zum Übermitteln von Experimenten für automatisiertes maschinelles Lernen erforderlich ist. Dieses Problem tritt auf, wenn Benutzer einen vorhandenen Dateispeicher während der Erstellung des Arbeitsbereichs als Standarddateispeicher festlegen.

Sie haben zwei Möglichkeiten, um dieses Problem zu vermeiden: 1. Verwenden Sie den Standarddateispeicher, der bei der Erstellung des Arbeitsbereichs automatisch für Sie erstellt wird. 2. Wenn Sie einen eigenen Dateispeicher verwenden möchten, stellen Sie sicher, dass sich dieser während der Erstellung des Arbeitsbereichs außerhalb des VNet befindet. Nachdem der Arbeitsbereich erstellt wurde, fügen Sie das Speicherkonto dem virtuellen Netzwerk hinzu.

Um dieses Problem zu beheben, entfernen Sie das Dateispeicherkonto aus dem virtuellen Netzwerk und fügen es dann dem virtuellen Netzwerk wieder hinzu.

### <a name="designer-sample-pipeline"></a>Beispielpipeline für den Designer

Es gibt ein bekanntes Problem, bei dem der Benutzer keine Beispielpipeline auf der Designer-Homepage ausführen kann. Das in der Beispielpipeline verwendeten Beispieldataset ist ein Azure Global-Dataset, das nicht für alle virtuellen Netzwerkumgebungen geeignet ist.

Um dieses Problem zu beheben, können Sie einen öffentlichen Arbeitsbereich verwenden, um eine Beispielpipeline auszuführen. Dadurch können Sie erfahren, wie der Designer verwendet werden soll und dann das Beispieldataset durch Ihr eigenes Dataset im virtuellen Netzwerk „Workspace“ ersetzen.

## <a name="datastore-azure-storage-account"></a>Datenspeicher: Azure Storage-Konto

Führen Sie die folgenden Schritte aus, um den Zugriff auf die gespeicherten Daten in Azure-Blob- und -Dateispeicher zu ermöglichen:

> [!TIP]
> Der erste Schritt ist für das Standardspeicherkonto des Arbeitsbereichs nicht erforderlich. Alle anderen Schritte sind für *alle* Speicherkonten erforderlich, die sich hinter dem VNET befinden und vom Arbeitsbereich verwendet werden, einschließlich des Standardspeicherkontos.

1. **Überspringen Sie diesen Schritt, wenn das Speicherkonto der *Standardspeicher* für Ihren Arbeitsbereich ist**. Wenn es nicht der Standardspeicher ist, müssen Sie **der verwalteten Identität des Arbeitsbereichs die Rolle „Storage-Blobdatenleser“ für das Azure-Speicherkonto gewähren**, damit die Daten aus dem Blobspeicher gelesen werden können.

    Weitere Informationen finden Sie unter der integrierten Rolle [Storage-Blobdatenleser](../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

1. **Gewähren Sie der verwalteten Identität des Arbeitsbereichs die Rolle „Leser“ für private Speicherendpunkte**. Wenn für Ihren Speicherdienst ein __privater Endpunkt__ verwendet wird, müssen Sie der vom Arbeitsbereich verwalteten Identität **Lesezugriff** auf den privaten Endpunkt gewähren. Die vom Arbeitsbereich verwaltete Identität in Azure AD hat den gleichen Namen wie Ihr Azure Machine Learning-Arbeitsbereich.

    > [!TIP]
    > Ihr Speicherkonto kann über mehrere private Endpunkte verfügen. Beispielsweise kann ein Speicherkonto über einen separaten privaten Endpunkt für Blob, Datei und DFS verfügen (Azure Data Lake Storage Gen2). Fügen Sie allen diesen Endpunkten die verwaltete Identität hinzu.

    Weitere Informationen finden Sie unter der integrierten Rolle [Leser](../role-based-access-control/built-in-roles.md#reader).

   <a id='enable-managed-identity'></a>
1. **Aktivieren Sie die Authentifizierung mit verwalteten Identitäten für Standardspeicherkonten**. Jeder Azure Machine Learning-Arbeitsbereich verfügt über zwei Standardspeicherkonten: ein Blob Storage-Standardkonto und ein Standardkonto für den Dateispeicher, die beim Erstellen des Arbeitsbereichs definiert werden. Sie können auch auf der Verwaltungsseite neue Standardeinstellungen für den **Datenspeicher** festlegen.

    ![Screenshot der Speicherorte von Standarddatenspeichern](./media/how-to-enable-studio-virtual-network/default-datastores.png)

    In der folgenden Tabelle ist beschrieben, warum die Authentifizierung mit verwalteten Identitäten für die Standardspeicherkonten Ihres Arbeitsbereichs genutzt wird.

    |Speicherkonto  | Hinweise  |
    |---------|---------|
    |Standardblobspeicher für den Arbeitsbereich| Speichert Modellressourcen vom Designer. Aktivieren Sie die Authentifizierung mit verwalteten Identitäten für dieses Speicherkonto, um Modelle im Designer bereitzustellen. <br> <br> Sie können eine Designer-Pipeline visualisieren und ausführen, wenn sie nicht den Standarddatenspeicher verwendet, sondern einen, der für die Verwendung der verwalteten Identität konfiguriert wurde. Wenn Sie jedoch versuchen, ein trainiertes Modell ohne aktivierte verwaltete Identität im Standarddatenspeicher bereitzustellen, tritt dabei ein Fehler auf, unabhängig davon, welche anderen Datenspeicher verwendet werden.|
    |Standarddateispeicher für den Arbeitsbereich| Speichert Experimentressourcen für automatisiertes maschinelles Lernen. Aktivieren Sie die Authentifizierung mit verwalteten Identitäten für dieses Speicherkonto, um Experimente für automatisiertes maschinelles Lernen zu übermitteln. |

1. **Konfigurieren Sie Datenspeicher für die Verwendung der Authentifizierung mit verwalteten Identitäten**. Nachdem Sie Ihrem virtuellen Netzwerk mit einem [Dienstendpunkt](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts) oder [privaten Endpunkt](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts) ein Azure Storage-Konto hinzugefügt haben, müssen Sie Ihren Datenspeicher für die Verwendung der Authentifizierung anhand der [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) konfigurieren. Auf diese Weise kann Studio auf Daten in Ihrem Speicherkonto zugreifen.

    Azure Machine Learning verwendet [Datenspeicher](concept-data.md#datastores), um eine Verbindung mit Speicherkonten herzustellen. Verwenden Sie beim Erstellen eines neuen Datenspeichers die folgenden Schritte, um einen Datenspeicher für die Verwendung der Authentifizierung mit verwalteten Identitäten zu konfigurieren:

    1. Wählen Sie __Datastores__ (Datenspeicher) im Studio aus.

    1. Wenn Sie einen vorhandenen Datenspeicher aktualisieren möchten, wählen Sie diesen aus, und wählen Sie __Anmeldeinformationen aktualisieren__.

        Um einen neuen Datenspeicher zu erstellen, wählen Sie __+ New datastore__ (+ Neuer Datenspeicher) aus.

    1. Wählen Sie in den Datenspeichereinstellungen für __Use workspace managed identity for data preview and profiling in Azure Machine Learning studio__ (Vom Arbeitsbereich verwaltete Identität für Datenvorschau und Profilerstellung in Azure Machine Learning Studio verwenden) __Ja__ aus.

        ![Screenshot der Aktivierung einer verwalteten Identität für einen Arbeitsbereich](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

    Mit diesen Schritten wird die vom Arbeitsbereich verwaltete Identität mithilfe von Azure RBAC dem neuen Speicherdienst als __Leser__ hinzugefügt. __Lesezugriff__ erlaubt dem Arbeitsbereich das Anzeigen der Ressource, aber keine Änderungen.

## <a name="datastore-azure-data-lake-storage-gen1"></a>Datenspeicher: Azure Data Lake Storage Gen1

Wenn Sie Azure Data Lake Storage Gen1 als Datenspeicher verwenden, können Sie nur Zugriffssteuerungslisten im POSIX-Stil nutzen. Sie können der vom Arbeitsbereich verwalteten Identität wie jedem anderen Sicherheitsprinzipal Zugriff auf Ressourcen zuweisen. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="datastore-azure-data-lake-storage-gen2"></a>Datenspeicher: Azure Data Lake Storage Gen2

Beim Verwenden von Azure Data Lake Storage Gen2 als Datenspeicher können Sie den Datenzugriff in einem virtuellen Netzwerk sowohl per Azure RBAC als auch mit POSIX-Zugriffssteuerungslisten (Access Control Lists, ACLs) steuern.

**Um Azure RBAC zu verwenden**, führen Sie die Schritte im Abschnitt [Datenspeicher: Azure Storage-Konto](#datastore-azure-storage-account) dieses Artikels aus. Data Lake Storage Gen2 basiert auf Azure Storage, sodass die gleichen Schritte gelten, wenn Sie Azure RBAC verwenden.

Für die **Verwendung von ACLs** kann der verwalteten Identität des Arbeitsbereichs wie jedem anderen Sicherheitsprinzipal Zugriff gewährt werden. Weitere Informationen finden Sie unter [Zugriffssteuerungslisten für Dateien und Verzeichnisse](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

## <a name="datastore-azure-sql-database"></a>Datenspeicher: Azure SQL-Datenbank

Wenn Sie mit einer verwalteten Identität auf Daten zugreifen möchten, die in einer Azure SQL-Datenbank-Instanz gespeichert sind, müssen Sie einen eigenständigen SQL-Benutzer erstellen, der der verwalteten Identität zugeordnet ist. Weitere Informationen zum Erstellen eines Benutzers von einem externen Anbieter finden Sie unter [Erstellen eigenständiger Benutzer mit Zuordnung zu Azure AD-Identitäten](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Nachdem Sie einen eigenständigen SQL-Benutzer erstellt haben, erteilen Sie mithilfe des [GRANT T-SQL-Befehls](/sql/t-sql/statements/grant-object-permissions-transact-sql) Berechtigungen.

## <a name="intermediate-component-output"></a>Ausgabe der Zwischenkomponente

Wenn Sie die Ausgabe der Zwischenkomponente des Azure Machine Learning-Designers nutzen, können Sie den Ausgabespeicherort für jede Komponente im Designer angeben. Damit können Sie zwischengeschaltete Datasets zu Sicherheits-, Protokollierungs- oder Überwachungszwecken an einem separaten Ort speichern. Führen Sie die folgenden Schritte aus, um die Ausgabe anzugeben:

1. Wählen Sie die Komponente aus, für die Sie die Ausgabe angeben möchten.
1. Wählen Sie im Bereich der Komponenteneinstellungen auf der rechten Seite die Option **Ausgabeeinstellungen** aus.
1. Geben Sie den Datenspeicher an, den Sie für die einzelnen Komponentenausgaben verwenden möchten.

Stellen Sie sicher, dass Sie auf die zwischengeschalteten Speicherkonten in Ihrem virtuellen Netzwerk Zugriff haben. Andernfalls verursacht die Pipeline einen Fehler.

Aktivieren Sie die [Authentifizierung mit verwalteten Identitäten](#enable-managed-identity) für die zwischengeschalteten Speicherkonten, damit Ausgabedaten visualisiert werden können.
## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Zugriff auf Studio von einer Ressource innerhalb des virtuellen Netzwerks aus

Wenn Sie über eine Ressource innerhalb eines virtuellen Netzwerks (z. B. eine Computeinstanz oder eine VM) auf das Studio zugreifen, müssen Sie aus dem virtuellen Netzwerk an das Studio ausgehenden Datenverkehr zulassen. 

Wenn Sie z. B. ausgehenden Datenverkehr mit Netzwerksicherheitsgruppen (NSG) einschränken, fügen Sie einem __Diensttag__-Ziel von __AzureFrontDoor.Frontend__ eine Regel hinzu.

## <a name="firewall-settings"></a>Firewalleinstellungen

Einige Speicherdienste, z. B. ein Azure Storage-Konto, verfügen über Firewalleinstellungen, die für den öffentlichen Endpunkt dieser speziellen Dienstinstanz gelten. In der Regel können Sie mit dieser Einstellung den Zugriff von bestimmten IP-Adressen aus dem öffentlichen Internet zulassen bzw. blockieren. Dies wird bei Verwendung von Azure Machine Learning Studio __nicht unterstützt__. Sie wird bei Verwendung des Azure Machine Learning SDK oder der Befehlszeilenschnittstelle unterstützt.

> [!TIP]
> Azure Machine Learning Studio wird bei Verwendung des Azure Firewall-Diensts unterstützt. Weitere Informationen finden Sie unter [Verwenden Ihres Arbeitsbereichs hinter einer Firewall](how-to-access-azureml-behind-firewall.md).
## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

* [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
* [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
* [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
* [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)
