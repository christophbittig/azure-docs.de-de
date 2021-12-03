---
title: 'Bereitstellen eines Azure-Clouddiensts (erweiterter Support): Azure-Portal'
description: Bereitstellen eines Azure-Clouddiensts (erweiterter Support) über das Azure-Portal
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4e35814e898e35a7ba9251d3eb36f6bde4d086e1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339989"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Bereitstellen einer Instanz von Azure Cloud Services (erweiterter Support) über das Azure-Portal
In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal eine Clouddienstbereitstellung mit erweitertem Support erstellen. 

## <a name="before-you-begin"></a>Vorbereitung

Informieren Sie sich über die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support), und erstellen Sie die entsprechenden Ressourcen. 

## <a name="deploy-a-cloud-services-extended-support"></a>Bereitstellen einer Instanz von Cloud Services (erweiterter Support) 
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

2.  Suchen Sie mithilfe der Suchleiste am oberen Rand des Azure-Portals nach **Cloud Services (erweiterter Support)** , und wählen Sie die entsprechende Option aus.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Abbildung: Blatt mit allen Ressourcen im Azure-Portal":::
 
3.  Wählen Sie im Bereich „Cloud Services (erweiterter Support)“ die Option **Erstellen** aus. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Abbildung: Erwerben eines Clouddiensts über den Marketplace":::

4. Das Fenster zum Erstellen von Cloud Services (erweiterter Support) wird mit der Registerkarte **Basiseinstellungen** geöffnet. 
    - Wählen Sie ein Abonnement aus.
    - Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine neue.
    - Geben Sie den gewünschten Namen für Ihre Clouddienstbereitstellung mit erweitertem Support ein.
        - Der DNS-Name des Clouddiensts ist ein separater Wert. Er wird durch die DNS-Namensbezeichnung der öffentlichen IP-Adresse angegeben und kann auf der Registerkarte „Konfiguration“ im Abschnitt für öffentliche IP-Adressen geändert werden.
    -  Wählen Sie die gewünschte Zielregion für die Bereitstellung aus.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Abbildung: Startblatt für Cloud Services (erweiterter Support)":::

5. Fügen Sie Ihre Clouddienst-Konfigurationsdatei, Ihre Paketdatei und Ihre Definitionsdatei hinzu. Sie können vorhandene Dateien aus dem Blobspeicher hinzufügen oder sie über Ihren lokalen Computer hochladen. Wenn Sie zum Hochladen Ihren lokalen Computer verwenden, werden diese Dateien in einem Speicherkonto gespeichert. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Abbildung: Uploadabschnitt der Registerkarte „Basiseinstellungen“ bei der Erstellung":::

6. Wenn alle Felder ausgefüllt sind, können Sie zur Registerkarte **Konfiguration** navigieren und die Konfiguration abschließen. 
    - Wählen Sie ein virtuelles Netzwerk aus, das dem Clouddienst zugeordnet werden soll, oder erstellen Sie ein neues. 
        - Clouddienstbereitstellungen mit erweitertem Support **müssen** sich in einem virtuellen Netzwerk befinden. Auf das virtuelle Netzwerk **muss** außerdem in der Dienstkonfigurationsdatei (.cscfg) im Abschnitt `NetworkConfiguration` verwiesen werden.
    - Wählen Sie eine vorhandene öffentliche IP-Adresse aus, die dem Clouddienst zugeordnet werden soll, oder erstellen Sie eine neue.
        - Wenn in Ihrer Dienstdefinitionsdatei (.csdef) **IP-Eingabeendpunkte** definiert sind, muss für Ihren Clouddienst eine öffentliche IP-Adresse erstellt werden. 
        - Von Cloud Services (erweiterter Support) wird nur die Basic-SKU für IP-Adressen unterstützt.
        - Wenn Ihre Dienstkonfiguration (.cscfg) eine reservierte IP-Adresse enthält, muss der Zuordnungstyp für die öffentliche IP-Adresse auf **Statisch** festgelegt werden. 
        - Optional: Weisen Sie einen DNS-Namen für Ihren Clouddienstendpunkt zu, indem Sie die DNS-Bezeichnungseigenschaft der öffentlichen IP-Adresse aktualisieren, die dem Clouddienst zugeordnet ist.  
    - Optional: Starten Sie den Clouddienst. Wählen Sie aus, ob der Dienst sofort nach der Erstellung gestartet werden soll.
    - Auswählen einer Key Vault-Instanz 
        - Key Vault ist erforderlich, wenn Sie in Ihrer Dienstkonfigurationsdatei (.cscfg) ein Zertifikat (oder mehrere Zertifikate) angeben. Wenn Sie einen Schlüsseltresor auswählen, wird anhand der Zertifikatfingerabdrücke nach den ausgewählten Zertifikaten aus Ihrer Dienstkonfigurationsdatei (.cscfg) gesucht. Sollten in Ihrem Schlüsseltresor nicht alle Zertifikate vorhanden sein, können Sie die fehlenden Zertifikate jetzt hochladen und anschließend auf **Aktualisieren** klicken.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Abbildung: Konfigurationsblatt im Azure-Portal beim Erstellen einer Instanz von Cloud Services (erweiterter Support)":::

7. Wenn alle Felder ausgefüllt sind, können Sie zur Registerkarte **Überprüfen und erstellen** wechseln, um Ihre Bereitstellungskonfiguration zu überprüfen und Ihren Clouddienst (erweiterter Support) zu erstellen.

## <a name="next-steps"></a>Nächste Schritte 
- Sehen Sie sich die [häufig gestellten Fragen](faq.yml) zu Cloud Services (erweiterter Support) an.
- Stellen Sie einen Clouddienst (erweiterter Support) über das [Azure-Portal](deploy-portal.md), per [PowerShell](deploy-powershell.md), per [Vorlage](deploy-template.md) oder mithilfe von [Visual Studio](deploy-visual-studio.md) bereit.
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).
