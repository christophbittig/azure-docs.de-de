---
title: Netzwerkdatenzugriff in Studio
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie der Datenzugriff mit Azure Machine Learning Studio funktioniert, wenn sich Ihr Arbeitsbereich oder Speicher in einem virtuellen Netzwerk befindet.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/08/2021
ms.openlocfilehash: c93a788686cd4ae7c0f20535b2ca0b4746fbdff2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350247"
---
# <a name="network-data-access-with-azure-machine-learning-studio"></a>Netzwerkdatenzugriff mit Azure Machine Learning Studio

Der Datenzugriff ist komplex, und es ist wichtig zu erkennen, dass es viele Teile gibt. Beispielsweise unterscheidet sich der Zugriff auf Daten aus Azure Machine Learning Studio von der Verwendung des SDK. Wenn Sie das SDK in Ihrer lokalen Entwicklungsumgebung verwenden, greifen Sie direkt auf Daten in der Cloud zu. Wenn Sie Studio verwenden, greifen Sie nicht immer direkt von Ihrem Client aus auf den Datenspeicher zu. Studio nutzt den Arbeitsbereich, um in Ihrem Namen auf Daten zuzugreifen.

> [!IMPORTANT]
> Die Informationen in diesem Artikel richten sich an Azure-Administratoren, die die für eine Azure Machine Learning-Lösung erforderliche Infrastruktur erstellen.

> [!TIP]
> Studio unterstützt nur das Lesen von Daten aus den folgenden Datenspeichertypen in einem VNet:
>
> * Azure Storage-Konto (Blob und Datei)
> * Azure Data Lake Storage Gen1
> * Azure Data Lake Storage Gen2
> * Azure SQL-Datenbank

## <a name="data-access"></a>Datenzugriff

Im Allgemeinen umfasst der Datenzugriff von Studio die folgenden Überprüfungen:

1. Wer greift zu?
    - Je nach Speichertyp gibt es mehrere verschiedene Authentifizierungstypen. Zum Beispiel Kontoschlüssel, Token, Dienstprinzipal, verwaltete Identität und Benutzeridentität.
    - Wenn die Authentifizierung mithilfe einer Benutzeridentität erfolgt, ist es wichtig zu wissen, *welcher* Benutzer versucht, auf den Speicher zuzugreifen.
2. Verfügen sie über Berechtigungen?
    - Sind die Anmeldeinformationen richtig? Wenn ja, verfügt der Dienstprinzipal, die verwaltete Identität usw. über die erforderlichen Berechtigungen für den Speicher? Die Berechtigungen werden über die rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Controls, RBAC) gewährt.
    - [Der Leser](/azure/role-based-access-control/built-in-roles#reader) des Speicherkontos liest Metadaten des Speichers.
    - [Storage Blobdatenleser](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) liest Daten in einem Blobcontainer.
    - [Mitwirkender](/azure/role-based-access-control/built-in-roles#contributor) ermöglicht den Schreibzugriff auf ein Speicherkonto.
    - Je nach Speichertyp sind möglicherweise weitere Rollen erforderlich.
3. Woher stammt der Zugriff?
    - Benutzer: Befindet sich die Client-IP-Adresse im VNet-/Subnetzbereich?
    - Arbeitsbereich: Ist der Arbeitsbereich öffentlich oder verfügt er über einen privaten Endpunkt in einem VNet/Subnetz?
    - Speicher: Lässt der Speicher öffentlichen Zugriff zu oder schränkt er den Zugriff über einen Dienstendpunkt oder einen privaten Endpunkt ein?
4. Welcher Vorgang wird ausgeführt?
    - CRUD-Vorgänge (Create, Read, Update, Delete) für einen Datenspeicher/ein Dataset werden von Azure Machine Learning verarbeitet.
    - Datenzugriffsaufrufe (z. B. Vorschau oder Schema) wechseln zum zugrunde liegenden Speicher und benötigen zusätzliche Berechtigungen.
5. Wo wird dieser Vorgang ausgeführt? Computeressourcen in Ihrem Azure-Abonnement oder in einem Microsoft-Abonnement gehostete Ressourcen?
    - Alle Aufrufe von Dataset- und Datenspeicherdiensten (mit Ausnahme der Option "Profil generieren") verwenden Ressourcen, die in einem __Microsoft-Abonnement__ gehostet werden, um die Vorgänge auszuführen.
    - Aufträge, einschließlich der Option "Profil generieren" für Datasets, werden auf einer Computeressource in __Ihrem Abonnement__ ausgeführt und greifen von dort aus auf die Daten zu. Daher benötigt die Computeidentität die Berechtigung für den Speicher und nicht die Identität des Benutzers, der den Auftrag übermittelt.

Das folgende Diagramm zeigt den allgemeinen Ablauf eines Datenzugriffsaufrufs. In diesem Beispiel versucht ein Benutzer, einen Datenzugriffsaufruf über einen Machine Learning-Arbeitsbereich ohne Computeressource zu tätigen.

:::image type="content" source="./media/concept-network-data-access/data-access-flow.svg" alt-text="Diagramm des Logikflusses beim Zugriff auf Daten":::

## <a name="azure-storage-account"></a>Azure Storage-Konto

Wenn Sie ein Azure Storage-Konto aus Azure Machine Learning Studio verwenden, müssen Sie die verwaltete Identität des Arbeitsbereichs den folgenden Azure RBAC-Rollen für das Speicherkonto hinzufügen:

* [Leser von Blobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)
* Wenn das Speicherkonto einen privaten Endpunkt für die Verbindung mit dem VNet verwendet, müssen Sie der verwalteten Identität die Rolle [Leser](../role-based-access-control/built-in-roles.md#reader) für den privaten Endpunkt des Speicherkontos gewähren.

Weitere Informationen finden Sie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md).

In den folgenden Abschnitten finden Sie Informationen zu Einschränkungen bei der Verwendung eines Azure Storage-Kontos mit Ihrem Arbeitsbereich in einem VNet.
### <a name="using-an-existing-storage-account"></a>Verwenden eines vorhandenen Speicherkontos

Wenn Sie beim Erstellen eines Arbeitsbereichs ein vorhandenes Speicherkonto als __Standardspeicher__ verwenden, wird der `azureml-filestore` Ordner im Dateispeicher nicht automatisch erstellt. Dieser Ordner ist erforderlich, wenn [AutoML](concept-automated-ml.md)-Experimente übermittelt werden.

Um dieses Problem zu vermeiden, können Sie entweder zulassen, dass Azure Machine Learning beim Erstellen des Arbeitsbereichs den Standardspeicher für Sie erstellt, oder sicherstellen, dass sich das vorhandene Speicherkonto beim Erstellen des Arbeitsbereichs __nicht__ im VNet befindet. Weitere Informationen zu Netzwerken mit Azure Storage-Konto finden Sie unter [Konfigurieren von Azure Storage-Konten mit virtuellen Netzwerken](/azure/storage/common/storage-network-security).

### <a name="azure-storage-firewall"></a>Azure Storage-Firewall

Wenn sich ein Azure Storage-Konto hinter einem virtuellen Netzwerk befindet, kann die Speicherfirewall normalerweise verwendet werden, damit Ihr Client eine direkte Verbindung über das Internet herstellen kann. Bei Verwendung von Studio stellt jedoch nicht Ihr Client eine Verbindung mit dem Speicherkonto her. Es ist der Azure Machine Learning Service, der die Anforderung vornimmt. Die IP-Adresse des Diensts wird nicht dokumentiert und ändert sich häufig. __Wenn Sie die Speicherfirewall aktivieren, kann Studio nicht auf das Speicherkonto in einer VNet-Konfiguration zugreifen__.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Wenn Sie Azure Data Lake Storage Gen1 als Datenspeicher verwenden, können Sie nur Zugriffssteuerungslisten im POSIX-Stil nutzen. Sie können der vom Arbeitsbereich verwalteten Identität wie jedem anderen Sicherheitsprinzipal Zugriff auf Ressourcen zuweisen. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Beim Verwenden von Azure Data Lake Storage Gen2 als Datenspeicher können Sie den Datenzugriff in einem virtuellen Netzwerk sowohl per Azure RBAC als auch mit POSIX-Zugriffssteuerungslisten (Access Control Lists, ACLs) steuern.

**Um Azure RBAC zu verwenden**, führen Sie die Schritte im Abschnitt [Datenspeicher: Azure Storage-Konto](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account) des Artikels „Verwenden von Azure Machine Learning Studio in einem Azure-VNet“ aus. Data Lake Storage Gen2 basiert auf Azure Storage, sodass die gleichen Schritte gelten, wenn Sie Azure RBAC verwenden.

Für die **Verwendung von ACLs** kann der verwalteten Identität des Arbeitsbereichs wie jedem anderen Sicherheitsprinzipal Zugriff gewährt werden. Weitere Informationen finden Sie unter [Zugriffssteuerungslisten für Dateien und Verzeichnisse](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

Wenn Sie mit einer verwalteten Identität auf Daten zugreifen möchten, die in einer Azure SQL-Datenbank-Instanz gespeichert sind, müssen Sie einen eigenständigen SQL-Benutzer erstellen, der der verwalteten Identität zugeordnet ist. Weitere Informationen zum Erstellen eines Benutzers von einem externen Anbieter finden Sie unter [Erstellen eigenständiger Benutzer mit Zuordnung zu Azure AD-Identitäten](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Nachdem Sie einen eigenständigen SQL-Benutzer erstellt haben, erteilen Sie mithilfe des [GRANT T-SQL-Befehls](/sql/t-sql/statements/grant-object-permissions-transact-sql) Berechtigungen.

### <a name="deny-public-network-access"></a>Verweigern des Zugriffs auf öffentliches Netzwerk

In der Azure SQL-Datenbank können Sie mit __Öffentlichen Netzwerkzugriff verweigern__ den öffentlichen Zugriff auf die Datenbank blockieren. Der Zugriff auf die SQL-Datenbank wird __nicht unterstützt__, wenn diese Option aktiviert ist. Bei Verwendung einer SQL-Datenbank mit Azure Machine Learning Studio erfolgt der Datenzugriff immer über den öffentlichen Endpunkt für die SQL-Datenbank.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Aktivieren von Studio in einem Netzwerk finden Sie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md).

