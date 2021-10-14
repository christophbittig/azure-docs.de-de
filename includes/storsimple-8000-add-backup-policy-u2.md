---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 09/14/2021
ms.author: alkohli
ms.openlocfilehash: 88ef9534c1a0048ef113ce0666ddcdd47171c234
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589372"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>So fügen Sie eine StorSimple-Sicherungsrichtlinie hinzu

1. Wechseln Sie zu Ihrem StorSimple-Gerät, und klicken Sie auf **Sicherungsrichtlinie**.

2. Klicken Sie auf dem Blatt **Sicherungsrichtlinie** auf der Befehlsleiste auf **+ Richtlinie hinzufügen**.
   
    ![Sicherungsrichtlinie hinzufügen](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. Führen Sie auf dem Blatt **Sicherungsrichtlinie erstellen** die folgenden Schritte aus:
   
   1. **Gerät auswählen** wird automatisch mit den Informationen Ihres ausgewählten Geräts ausgefüllt.
   
   2. Geben Sie für Ihre **Sicherungsrichtlinie einen Namen** an, der zwischen 3 und 150 Zeichen lang ist. Der Name der Richtlinie kann nach der Erstellung nicht mehr geändert werden.
       
   3. Zum Hinzufügen von Volumes zu dieser Sicherungsrichtlinie wählen Sie **Volumes hinzufügen**, und klicken dann in der tabellarischen Volumeauflistung auf das bzw. die Kontrollkästchen, um dieser Sicherungsrichtlinie ein oder mehrere Volumes zuzuweisen.

       ![Hinzufügen einer Sicherungsrichtlinie 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Zum Definieren eines Zeitplans für diese Sicherungsrichtlinie klicken Sie auf **Erster Zeitplan** und ändern dann die folgenden Parameter:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Hinzufügen einer Sicherungsrichtlinie 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Wählen Sie als **Momentaufnahmetyp** die Option **Cloud** oder **Lokal** aus.

       2. Geben Sie die Häufigkeit der Sicherungen an. (Geben Sie eine Zahl an, und wählen Sie dann in der Dropdownliste **Tage** oder **Wochen** aus.)

       3. Geben Sie einen Aufbewahrungszeitplan ein.

       4. Geben Sie Startzeit und -datum für die Sicherungsrichtlinie ein.

       5. Klicken Sie auf **OK**, um den Zeitplan zu definieren.
       
       > [!NOTE]
       > Wenn Sie 64 Sicherungen für einen Zeitplan erreichen und diese Sicherungen beibehalten möchten, können Sie den [Zeitplan deaktivieren](..\articles\storsimple\storsimple-8000-manage-backup-policies-u2.md#disable-a-schedule) und dann einen neuen Zeitplan mit einer maximalen Aufbewahrung von 64 Sicherungen hinzufügen. Diese Problemumgehung funktioniert, bis Sie den Grenzwert von 256 Sicherungen pro Volume erreichen. An diesem Punkt müssen Sie ältere Sicherungen löschen, bevor Sie neue Sicherungen erstellen können.

   5. Klicken Sie auf **Erstellen**, um eine Sicherungsrichtlinie zu erstellen.
   
   6. Sie werden benachrichtigt, wenn die Sicherungsrichtlinie erstellt wurde. Die neu hinzugefügte Richtlinie wird in der Tabellenansicht auf dem Blatt **Sicherungsrichtlinie** angezeigt.

       ![Hinzufügen einer Sicherungsrichtlinie 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
