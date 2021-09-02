---
title: Verwalten von Modell- und Lerneinstellungen – Personalisierung
description: Die maschinell erlernten Modell- und Lerneinstellungen können zur Sicherung in Ihre eigene Quellcodeverwaltung exportiert werden.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1e1e5dd9c21b89aba4c905656dd79c7934f8b061
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831602"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Verwalten von Modell- und Lerneinstellungen

Die maschinell erlernten Modell- und Lerneinstellungen können zur Sicherung in Ihre eigene Quellcodeverwaltung exportiert werden.

## <a name="export-the-personalizer-model"></a>Exportieren des Personalisierungsmodells

Überprüfen Sie im Abschnitt **Modell- und Lerneinstellungen** der Ressourcenverwaltung die Modellerstellung und das Datum der letzten Aktualisierung, und exportieren Sie das aktuelle Modell. Sie können das Azure-Portal oder die Personalisierungs-APIs verwenden, um eine Modelldatei zu Archivierungszwecken zu exportieren.

![Exportieren des aktuellen Personalisierungsmodells](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Löschen von Daten für Ihre Lernschleife

1. Wählen Sie im Azure-Portal für Ihre Personalisierungsressource auf der Seite **Modell- und Lerneinstellungen** die Option **Daten löschen** aus.
1. Aktivieren Sie alle drei Kontrollkästchen, um alle Daten zu löschen und die Lernschleife in den ursprünglichen Zustand zurückzusetzen.

    ![Löschen Sie im Azure-Portal Daten aus der Personalisierungsressource.](./media/settings/clear-data-from-personalizer-resource.png)

    |Wert|Zweck|
    |--|--|
    |Protokollierte Personalisierungs- und Relevanzdaten.|Diese Protokolldaten werden in Offline-Auswertungen verwendet. Löschen Sie die Daten, wenn Sie Ihre Ressource zurücksetzen.|
    |Setzen Sie das Personalisierungsmodell zurück.|Dieses Modell ändert sich bei jedem erneuten Training. Diese Trainingshäufigkeit wird in der **Uploadfrequenz für das Modell** auf der Seite **Konfiguration** angegeben. |
    |Legen Sie die Lernrichtlinie auf die Standardeinstellung fest.|Wenn Sie die Lernrichtlinie im Rahmen einer Offline-Auswertung geändert haben, wird diese auf die ursprüngliche Lernrichtlinie zurückgesetzt.|

1. Wählen Sie **Ausgewählte Daten löschen** aus, um den Löschvorgang zu starten. Der Status wird in den Azure-Benachrichtigungen in der oberen rechten Navigationsleiste angezeigt.

## <a name="import-a-new-learning-policy"></a>Importieren einer neuen Lernrichtlinie

Die [Lernrichtlinieneinstellungen](concept-active-learning.md#understand-learning-policy-settings) legen die _Hyperparameter_ des Modelltrainings fest. Führen Sie eine [Offlineauswertung](how-to-offline-evaluation.md) durch, um eine neue Lernrichtlinie zu finden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und wählen Sie Ihre Personalisierungsressource aus.
1. Wählen Sie im Abschnitt **Ressourcenmanagement** die Option **Modell- und Lerneinstellungen** aus.
1. Wählen Sie für **Lerneinstellungen importieren** die Datei aus, die Sie mit dem oben angegebenen JSON-Format erstellt haben, und wählen Sie dann die Schaltfläche **Hochladen** aus.

    Warten Sie auf die Benachrichtigung, dass die Lernrichtlinie erfolgreich hochgeladen wurde.

## <a name="export-a-learning-policy"></a>Exportieren einer Lernrichtlinie

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und wählen Sie Ihre Personalisierungsressource aus.
1. Wählen Sie im Abschnitt **Ressourcenmanagement** die Option **Modell- und Lerneinstellungen** aus.
1. Wählen Sie für **Lerneinstellungen importieren** die Schaltfläche **Lerneinstellungen exportieren** aus. Damit wird die `json`-Datei auf dem lokalen Computer gespeichert.

## <a name="next-steps"></a>Nächste Schritte

[Analysieren Ihrer Lernschleife mit einer Offlineauswertung](how-to-offline-evaluation.md)
