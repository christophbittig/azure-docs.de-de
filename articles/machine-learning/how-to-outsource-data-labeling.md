---
title: Unternehmen für Datenbeschriftung
titleSuffix: Azure Machine Learning
description: Beauftragen Sie ein Unternehmen für Datenkennzeichnungen, das Sie bei der Kennzeichnung der Daten in Ihrem Datenkennzeichnungsprojekt unterstützt
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.reviewer: sgilley
author: kvijaykannan
ms.author: vkann
ms.date: 10/21/2021
ms.topic: how-to
ms.openlocfilehash: 449a6de24b9b02d64e1bda66f24afaa683868a56
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558552"
---
# <a name="work-with-a-data-labeling-vendor-company"></a>Arbeiten Sie mit einem Datenkennzeichnungsunternehmen zusammen

Erfahren Sie, wie Sie ein Datenkennzeichnungsunternehmen einbinden, das Sie bei der Kennzeichnung Ihrer Daten unterstützt. Weitere Informationen zu diesen Unternehmen und den von ihnen zur Verfügung stellen Kennzeichnungsdiensten finden Sie auf ihren Listenseiten in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).


## <a name="workflow-summary"></a>Workflow-Zusammenfassung

Bevor Sie Ihr Datenkennzeichnungsprojekt erstellen:

1. Wählen Sie einen Kennzeichnungsunternehmen aus.  So suchen Sie einen Anbieter im Azure Marketplace:
    1. Überprüfen Sie [die Listendetails dieser Kennzeichnungsunternehmen](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).
    1. Wenn das Kennzeichnungsunternehmen Ihren Anforderungen entspricht, wählen Sie im Azure Marketplace die Option **Kontaktformular** aus. Azure Marketplace leitet Ihre Anfrage an das Kennzeichnungsunternehmen weiter. Sie können sich an mehrere Kennzeichnungsunternehmen wenden, bevor Sie ein Unternehmen auswählen.

1. Wenden Sie sich an das Kennzeichnungsunternehmen und schließen Sie einen Vertrag ab.

Sobald der Vertrag mit dem Kennzeichnungsunternehmen abgeschlossen ist:

1. Erstellen Sie eine Kennzeichnungsprojekt in [Azure Machine Learning Studio](https://ml.azure.com). Weitere Informationen zum Erstellen eines Projekts finden Sie unter Erstellen eines [Bildkennzeichnungsprojekts](how-to-create-image-labeling-projects.md) oder eines [Textkennzeichnungsprojekts](how-to-create-text-labeling-projects.md).
1. Sie sind nicht auf die Verwendung eines Datenkennzeichnungsunternehmen von Azure Marketplace beschränkt.  Wenn Sie jedoch einen Anbieter aus dem Azure Marketplace verwenden:
    1. Wählen Sie **Verwenden eines Kennzeichnungsunternehmens aus dem Azure Marketplace** aus.
    1. Wählen Sie in der Dropdownliste das entsprechende Kennzeichnungsunternehmen aus.

    > [!NOTE]
    > Der Firmenname des Kennzeichnungsunternehmen kann nach dem Erstellen des Kennzeichnungsprojekts nicht mehr geändert werden.

1. Aktivieren Sie für jeden Anbieter, egal ob Sie ihn über Azure Marketplace oder an anderer Stelle ausgewählt haben, den Zugriff (`labeler` Rolle, `techlead` Rolle) für das Kennzeichnungsunternehmen mithilfe von Rollenbasierter Azure-Zugriff (Role Based Access, RBAC). Diese Rollen ermöglichen dem Unternehmen den Zugriff auf die Ressourcen, um Ihre Daten mit Anmerkungen zu kommentieren.

## <a name="select-a-company"></a><a name="review"></a> Ein Unternehmen auswählen

Microsoft hat einige Kennzeichnungsunternehmen mit dem Wissen und der Erfahrung ermittelt, die Ihre Anforderungen erfüllen können. Sie können mehr über die Kennzeichnungsunternehmen und die Auswahl eines Anbieter erfahren. Dazu geben Sie die Anforderungen und Ansprüche Ihres Projekts bzw. Ihrer Projekte auf ihren Listenseiten in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend) an.

> [!IMPORTANT]
> Weitere Informationen zu diesen Unternehmen und den von ihnen zur Verfügung gestellten Kennzeichnungsdiensten finden Sie auf ihren Listenseiten in Azure Marketplace. Sie sind verantwortlich für die Entscheidung, ein Kennzeichnungsunternehmen zu beauftragen, das seine Dienste über den Azure Marketplace anbietet. Sie sollten unabhängig bewerten, ob ein Kennzeichnungsunternehmen und seine Erfahrung, Dienste, Mitarbeiter, Geschäftsbedingungen usw. Ihre Projektanforderungen erfüllen. Sie können sich mithilfe der Option **Kontaktformular** in Azure Marketplace an ein Kennzeichnungsunternehmen wenden, das Dienste über Azure Marketplace anbietet. Sie können davon ausgehen, dass Sie innerhalb von drei Werktagen von einem kontaktierten Unternehmen hören werden. Sie schließen den Vertrag mit dem Kennzeichnungsunternehmen ab und wickeln auch die Zahlungen direkt mit diesem Unternehmen ab.

Microsoft überprüft in regelmäßigen Abständen die Liste potenzieller Kennzeichnungsunternehmen in Azure Marketplace und kann der Liste jederzeit Anbieter hinzufügen oder sie daraus entfernen.  

* Wenn ein Anbieter aus der Liste entfernt wird, wirkt sich das nicht auf vorhandene Projekte oder den Zugriff dieses Unternehmens auf diese Projekte aus.
* Wenn Sie einen Anbieter verwenden, der nicht mehr in Azure Marketplace aufgeführt ist, wählen Sie für Ihr neues Projekt nicht die Option **Verwenden eines Kennzeichnungsunternehmens aus Azure Marketplace**.
* Ein entfernter Anbieter ist nicht mehr in Azure Marketplace aufgelistet.
* Ein entfernter Anbieter kann nicht mehr über den Azure Marketplace kontaktiert werden.

Sie können mehrere Anbieter von Kennzeichnungsunternehmen für verschiedene Kennzeichnungsprojekte einbinden. Jedes Projekt wird mit einem Kennzeichnungsunternehmen verknüpft.

Im Folgenden finden Sie Kennzeichnungsunternehmen, die Ihnen helfen können, Ihre Daten mithilfe von Azure Machine Learning Datenkennzeichnungsdiensten zu kennzeichnen. Zeigen Sie die [Liste der Kennzeichnungsunternehmen](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend) an.

* [iSoftStone](https://azuremarketplace.microsoft.com/marketplace/consulting-services/isoftstoneinc1614950352893.20210527) 

* [Quadrant-Ressource](https://azuremarketplace.microsoft.com/marketplace/consulting-services/quadrantresourcellc1587325810226.quadrant_resource_data_labeling)

## <a name="enter-into-a-contract"></a>Schließen Sie einen Vertrag ab 

Nachdem Sie das Kennzeichnungsunternehmen ausgewählt haben, mit dem Sie arbeiten möchten, müssen Sie direkt einen Vertrag mit dem Kennzeichnungsunternehmen abschließen, in dem die Bedingungen für Ihren Auftrag festgelegt sind. Microsoft ist kein Teilnehmer dieser Vereinbarung und spielt keine Rolle bei der Bestimmung oder Aushandlung der Bedingungen. Beträge, die im Rahmen dieser Vereinbarung fällig sind, werden direkt an das Kennzeichnungsunternehmen bezahlt.

Wenn Sie die Funktion Microsoft Azure Machine Learning unterstütztes Kennzeichnen in einem Kennzeichnungsprojekt aktivieren, berechnet Microsoft Ihnen separat die Computerressourcen, die in Verbindung mit diesem Dienst verbraucht werden. Alle anderen Gebühren im Zusammenhang mit Ihrer Nutzung von Azure Machine Learning (z. B. für die Speicherung von Daten, die in Ihrem Azure Machine Learning-Arbeitsbereich verwendet werden) unterliegen den Bedingungen Ihrer Vereinbarung mit Microsoft.

## <a name="enable-access"></a>Aktivieren des Zugriffs

Damit das Kennzeichnungsunternehmen Zugriff auf Ihre Projekte hat, aktivieren Sie den Zugriff für sie über [rollenbasierter Zugriff in Azure (Role Based Access, RBAC)](how-to-assign-roles.md#manage-workspace-access) auf der Arbeitsbereichsebene.  Wenn Sie mehrere Kennzeichnungsunternehmen für verschiedene Kennzeichnungsprojekte verwenden möchten, empfehlen wir Ihnen, separate Arbeitsbereiche für jedes Unternehmen zu erstellen.

> [!IMPORTANT]
> Sie und nicht Microsoft sind für alle Aspekte Ihrer Bindung mit einem Kennzeichnungsunternehmen verantwortlich, einschließlich, aber nicht beschränkt auf, Probleme im Zusammenhang mit dem Umfang, der Qualität, dem Zeitplan und den Preisen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Datenkennzeichnungsprojekts und Exportieren der Kennzeichnungen](how-to-create-image-labeling-projects.md)
* [Erstellen eines Textkennzeichnungsprojekts und Exportieren der Kennzeichnungen (Vorschau)](how-to-create-text-labeling-projects.md)