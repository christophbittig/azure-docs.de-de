---
title: 'Microsoft Defender für Storage: Vorteile und Features'
description: Hier erfahren Sie mehr über die Vorteile und Features von Microsoft Defender für Storage.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6bc972f1e548ba94d591d3c000a83f40b8c1c507
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009809"
---
# <a name="introduction-to-microsoft-defender-for-storage"></a>Einführung in Microsoft Defender für Storage

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Bei **Microsoft Defender für Storage** handelt es sich um eine native intelligente Azure-Sicherheitsebene, die ungewöhnliche und potenziell schädliche Versuche erkennt, auf Ihre Speicherkonten zuzugreifen oder diese unbefugt zu nutzen. Das Tool verwendet die erweiterten Funktionen von künstlicher Sicherheitsintelligenz und [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684), um kontextbezogene Sicherheitswarnungen und entsprechende Empfehlungen bereitzustellen.

Bei anomalen Aktivitäten werden Sicherheitswarnungen ausgelöst. Diese Warnungen werden in Microsoft Defender für Cloud angezeigt und außerdem mit Informationen zu verdächtigen Aktivitäten und Empfehlungen zur Untersuchung und Beseitigung von Bedrohungen per E-Mail an Abonnementadministratoren gesendet.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Microsoft Defender für Storage** wird gemäß den Angaben in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Geschützte Speichertypen:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet|
|||


## <a name="what-are-the-benefits-of-microsoft-defender-for-storage"></a>Welche Vorteile hat die Nutzung von Microsoft Defender für Storage?

Microsoft Defender für Storage ermöglicht Folgendes:

- **Native Azure-Sicherheit**: Basierend auf der einfachen Aktivierung per Klick sorgt Defender für Storage für den Schutz der Daten, die in Azure-Blobs, Azure Files und Data Lakes gespeichert sind. Als nativer Azure-Dienst ermöglicht Defender für Storage eine zentralisierte Sicherheitsüberwachung für alle Datenressourcen, die von Azure verwaltet werden, und ist in andere Azure-Sicherheitsdienste, z. B. Microsoft Sentinel, integriert.
- **Umfassende Suite für die Erkennung**: Die Erkennungsfunktionen in Defender für Storage basieren auf Microsoft Threat Intelligence und decken die wichtigsten Speicherbedrohungen ab, z. B. anonymer Zugriff, kompromittierte Anmeldeinformationen, Social Engineering, Berechtigungsmissbrauch und schädliche Inhalte.
- **Umfassende Reaktionsmöglichkeiten:** Die Automatisierungstools von Defender für Cloud erleichtern es, identifizierte Bedrohungen zu verhindern bzw. darauf zu reagieren. Weitere Informationen hierzu finden Sie unter [Automatisieren der Reaktionen auf Defender für Cloud-Trigger](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Allgemeine Übersicht über die Features von Microsoft Defender für Storage":::


## <a name="what-kind-of-alerts-does-microsoft-defender-for-storage-provide"></a>Welche Art von Warnungen bietet Microsoft Defender für Storage?

Sicherheitswarnungen werden ausgelöst, wenn Folgendes vorliegt:

- **Verdächtige Zugriffsmuster**, z. B. der erfolgreiche Zugriff von einem Tor-Exitknoten oder von einer IP-Adresse, die von Microsoft Threat Intelligence als verdächtig eingestuft wird
- **Verdächtige Aktivitäten**, z. B. anormale Datenextraktionen oder ungewöhnliche Änderungen der Zugriffsberechtigungen
- **Upload schädlicher Inhalte**, z. B. das Hochladen von potenziellen Malware-Dateien (basierend auf Hashzuverlässigkeitsanalysen) oder das Hosting von Phishinginhalten

Warnungen enthalten Details zum Incident, durch den sie ausgelöst wurden, sowie Empfehlungen zur Untersuchung und Eindämmung von Bedrohungen. Warnungen können in Microsoft Sentinel oder in ein anderes SIEM-Tool eines Drittanbieters oder in ein anderes externes Tool exportiert werden.

> [!TIP]
> Eine bewährte Methode ist das [Konfigurieren von Microsoft Defender für Storage](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) auf Abonnementebene. Sie können das Tool jedoch auch [für einzelne Speicherkonten konfigurieren](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Was ist Hashzuverlässigkeitsanalyse für Schadsoftware?

Um zu ermitteln, ob eine hochgeladene Datei verdächtig ist, verwendet Microsoft Defender für Storage von [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) unterstützte Hashzuverlässigkeitsanalyse. Die Tools zum Schutz vor Bedrohungen überprüfen die hochgeladenen Dateien nicht, sondern überprüfen die Speicherprotokolle und vergleichen die Hashes von neu hochgeladenen Dateien mit denjenigen bekannter Viren, Trojaner, Spyware und Ransomware. 

Wenn der Verdacht besteht, dass eine Datei Schadsoftware enthält, zeigt Defender für Cloud eine Warnung an und kann optional den Speicherbesitzer per E-Mail um Genehmigung bitten, die verdächtige Datei zu löschen. Um diese automatische Entfernung von Dateien festzulegen, die laut Hashzuverlässigkeitsanalyse Schadsoftware enthalten, stellen Sie [Workflowautomatisierung bereit, die bei Warnungen ausgelöst wird, die die Angabe „Upload potenzieller Schadsoftware in ein Speicherkonto“ enthalten](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Wenn Sie die Defender für Cloud-Features zum Schutz vor Bedrohungen aktivieren möchten, müssen Sie für das Abonnement mit den entsprechenden Workloads erweiterte Sicherheitsfeatures aktivieren.
>
> Sie können **Microsoft Defender für Storage** entweder auf Abonnement- oder auf Ressourcenebene aktivieren.

## <a name="trigger-a-test-alert-for-microsoft-defender-for-storage"></a>Auslösen einer Testwarnung für Microsoft Defender für Storage

Generieren Sie zum Testen der Sicherheitswarnungen von Microsoft Defender für Storage in Ihrer Umgebung mit den folgenden Schritten die Warnung „Zugriff von einem Tor-Beendigungsknoten auf ein Speicherkonto“:

1. Öffnen Sie ein Speicherkonto, für das Microsoft Defender für Storage aktiviert ist.
1. Wählen Sie auf der Seitenleiste die Option „Container“ aus, und öffnen Sie einen vorhandenen Container, oder erstellen Sie einen neuen Container.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Öffnen eines Blobcontainers über ein Azure Storage-Konto" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Laden Sie eine Datei in diesen Container hoch.

    > [!CAUTION]
    > Laden Sie keine Datei mit vertraulichen Daten hoch.

1. Verwenden Sie das Kontextmenü der hochgeladenen Datei, um „SAS generieren“ auszuwählen.

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="Option „SAS generieren“ für eine Datei in einem Blobcontainer":::

1. Übernehmen Sie die Standardoptionen, und wählen Sie **SAS-Token und -URL generieren** aus.

1. Kopieren Sie die generierte SAS-URL.

1. Öffnen Sie auf dem lokalen Computer den Tor-Browser.

    > [!TIP]
    > Sie können Tor von der Tor Project-Website [https://www.torproject.org/download/](https://www.torproject.org/download/) herunterladen.

1. Navigieren Sie im Tor-Browser zur SAS-URL.

1. Laden Sie die in Schritt 3 hochgeladene Datei herunter.

    Innerhalb von zwei Stunden erhalten Sie die folgende Sicherheitswarnung von Defender für Cloud:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Sicherheitswarnung zum Zugriff über einen Tor-Exitknoten":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über Microsoft Defender für Storage erfahren.

Weitere Informationen finden Sie in den folgenden Artikeln: 

- Sie können Warnungen exportieren, und zwar unabhängig davon, ob sie von Defender für Cloud generiert oder von Defender für Cloud von einem anderen Sicherheitsprodukt empfangen werden. Befolgen Sie die Anweisungen unter [Exportieren von Sicherheitswarnungen und -empfehlungen (Vorschau)](continuous-export.md), um die Warnungen in Microsoft Sentinel (oder ein Drittanbieter-SIEM) bzw. ein beliebiges anderes externes Tool zu exportieren.
- [Aktivieren von Azure Defender für Storage](../storage/common/azure-defender-storage-configure.md)
- [Liste der Warnungen von Microsoft Defender für Storage](alerts-reference.md#alerts-azurestorage)
- [Microsoft Threat Intelligence-Funktionen](https://go.microsoft.com/fwlink/?linkid=2128684)
