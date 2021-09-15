---
author: v-dalc
ms.service: databox
ms.topic: include
ms.date: 08/17/2021
ms.author: alkohli
ms.openlocfilehash: 708ac746156582291cf071e730701013f1e005b0
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397893"
---
Gehen Sie wie folgt vor, um eine Verwaltungsressource für ein Gerät zu erstellen, das über Azure Edge Hardware Center bestellt wurde:

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen beim Azure-Portal unter der folgenden URL an: [https://portal.azure.com](https://portal.azure.com).

1. Es gibt zwei Möglichkeiten, mit der Erstellung einer neuen Verwaltungsressource zu beginnen:

    - Über Azure Edge Hardware Center: Suchen Sie nach **Azure Edge Hardware Center**, und wählen Sie den Eintrag aus. Klicken Sie in Hardware Center auf **Alle Bestellartikel**. Aktivieren Sie das Element **Name**. Wählen Sie in der **Übersicht** der Elemente die Option **Hardware konfigurieren** aus.
    
       Die Option **Hardware konfigurieren** wird nach dem Versand eines Geräts angezeigt. 

       ![Abbildung: Vier Schritte, um mit der Erstellung von Verwaltungsressourcen auf der Grundlage eines Bestellartikels in Azure Edge Hardware Center zu beginnen](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-a.png#lightbox) 
    
    - In Azure Stack Edge: Suchen Sie nach **Azure Stack Edge**, und wählen Sie den Eintrag aus. Wählen Sie **+ Erstellen** aus. Wählen Sie dann **Create management resource** (Verwaltungsressource erstellen) aus.
    
       ![Abbildung: Drei Schritte, um mit der Erstellung von Verwaltungsressourcen in Azure Stack Edge zu beginnen](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-b.png#lightbox) 

    Der Assistent zum **Erstellen von Verwaltungsressourcen** wird geöffnet.

1. Legen Sie auf der Registerkarte **Grundlagen** die folgenden Einstellungen fest:

    |Einstellung                                  |Wert                                                                                       |
    |-----------------------------------------|--------------------------------------------------------------------------------------------|
    |**Ein Abonnement auswählen**<sup>1</sup>    |Wählen Sie das Abonnement aus, das Sie für die Verwaltungsressource verwenden möchten.                                 |
    |**Ressourcengruppe**<sup>1</sup>           |Wählen Sie die Ressourcengruppe aus, die Sie für die Verwaltungsressource verwenden möchten. |
    |**Name**                                 |Geben Sie einen Namen für die Verwaltungsressource an.                                                 |
    |**Azure-Ressourcen bereitstellen in**             |Wählen Sie das Land oder die Region aus, in dem bzw. der die Metadaten für die Verwaltungsressource gespeichert werden. Die Metadaten können sich an einem anderen Standort als das physische Gerät befinden. |
    |**Gerätetyp**<sup>2</sup>              |Wählen Sie den Gerätetyp aus. Diese Option entspricht der Konfiguration, die für das Hardwareprodukt in einer Azure Edge Hardware Center-Bestellung ausgewählt ist.<br>Bei einem Gerät vom Typ „Azure Stack Edge Pro – GPU“ lautet der Gerätetyp beispielsweise entweder **Azure Stack Edge Pro – 1 GPU** oder **Azure Stack Edge Pro – 1 GPU**.|       

    <sup>1</sup> Eine Organisation verwendet für die Bestellung von Geräten unter Umständen andere Abonnements und Ressourcengruppen als für ihre Verwaltung.

    <sup>2</sup> Wenn Sie **Hardware konfigurieren** für den Bestellartikel verwenden, wird der Gerätetyp vom Bestellartikel abgeleitet, und es werden keine **GERÄTEDETAILS** angezeigt. 

    ![Screenshot: Registerkarte „Grundlagen“ unter „Create Management Resource“ (Verwaltungsressource erstellen). Die Registerkarte „Grundlagen“, verschiedene Optionen und die Schaltfläche „Überprüfen + erstellen“ sind hervorgehoben.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-02.png)

    Wählen Sie zum Fortfahren **Überprüfen + erstellen** aus.

5. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die grundlegenden Einstellungen für die Verwaltungsressource und die Nutzungsbedingungen. Klicken Sie anschließend auf **Erstellen**.

    - Wenn Sie diesen Vorgang gestartet haben, indem Sie für einen zugestellten Artikel in Azure Edge Hardware Center auf **Hardware konfigurieren** geklickt haben, werden das Gerät, der Name der Auftragsressource und der Bestellstatus oben auf dem Bildschirm aufgeführt. 

      ![Screenshot: Registerkarte „Überprüfen + erstellen“, wenn eine Azure Stack Edge-Verwaltungsressource für einen Bestellartikel in Azure Edge Hardware Center erstellt wird. Informationen zur Gerätebestellung sind hervorgehoben.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-03.png)

    - Wenn Sie den Vorgang in Azure Stack Edge gestartet haben, wird anstelle der Informationen zur Gerätebestellung der Gerätetyp unter **Grundlagen** aufgeführt. 

      ![Screenshot: Registerkarte „Überprüfen + erstellen“, wenn eine Azure Stack Edge-Verwaltungsressource in Azure Stack Edge gestartet wurde. Unter „Grundlagen“ ist der Gerätetyp hervorgehoben.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-04.png)  

    Die Schaltfläche **Erstellen** ist erst verfügbar, wenn alle Überprüfungen erfolgreich waren.

6. Nach Abschluss des Prozesses wird der Bereich „Übersicht“ für die neue Ressource geöffnet.

    ![Screenshot: Abgeschlossene Verwaltungsressource in Azure Stack Edge](media/azure-edge-hardware-center-create-management-resource/create-management-resource-05\.png) 
