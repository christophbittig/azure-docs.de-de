---
title: Upgrade von Mobility Service- und Appliancekomponenten - Vorschau
description: In diesem Artikel werden automatische Updates für den Mobilitäts-Agent und das Verfahren im Zusammenhang mit manuellen Updates beschrieben - Vorschau
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: a683aacc762ce169045ee17891b49624ef1ee991
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437643"
---
# <a name="upgrade-mobility-service-and-appliance-components-preview"></a>Upgrade von Mobility Service- und Appliancekomponenten (Vorschau)

Ab dieser Vorschau müssen Sie die Root-/Administratoranmeldeinformationen des Quellcomputers nicht verwalten, um Upgrades durchzuführen. Die Anmeldeinformationen sind nur für die Erstinstallation des Agents erforderlich. Anschließend können Sie die Anmeldeinformationen entfernen.


## <a name="update-mobility-agent-automatically"></a>Automatisches Update des Mobilitäts-Agents

Standardmäßig sind automatische Updates für einen Tresor aktiviert. Automatische Updates werden täglich um 24:00 Uhr Ortszeit ausgelöst, wenn eine neue Version verfügbar ist.

> [!NOTE]
> Wenn Sie private Vorschaubits verwenden, werden automatische Updates für die geschützten Computer blockiert. Stellen Sie sicher, dass Site Recovery mithilfe einer neuen Vorschau-Appliance wieder auf Ihrem Computer eingerichtet haben.

Um die neuesten Features, Verbesserungen und Fehlerbehebungen zu nutzen, empfehlen wir Ihnen, in den **Upgradeeinstellungen des Mobilitäts-Agents (Vorschau)** die Option **Site Recovery-Verwaltung zulassen** auszuwählen. Automatische Updates erfordern keinen Neustart und wirken sich nicht auf die aktuelle Replikation Ihrer virtuellen Computer aus. Automatische Updates stellen außerdem sicher, dass alle Replikationsappliances im Tresor automatisch aktualisiert werden.

![Automatische Updates für Mobilitäts-Agent aktiviert](./media/upgrade-mobility-service-preview/automatic-updates-on.png)

Um die automatischen Updates zu deaktivieren, schalten Sie die Schaltfläche **Site Recovery-Verwaltung zulassen** um.

![Automatische Updates für Mobilitäts-Agent deaktiviert](./media/upgrade-mobility-service-preview/automatic-updates-off.png)


## <a name="update-mobility-agent-manually"></a>Manuelles Update des Mobilitäts-Agents

Wenn Sie automatische Updates für Ihren Mobilitäts-Agent deaktiviert haben, können Sie den Agent mithilfe der folgenden Verfahren manuell aktualisieren:

### <a name="upgrade-mobility-agent-on-multiple-protected-items"></a>Aktualisieren des Mobilitäts-Agents für mehrere geschützte Elemente

Führen Sie die folgenden Schritte aus, um den Mobilitäts-Agent für mehrere geschützte Elemente manuell zu aktualisieren:

1. Navigieren Sie zu **Recovery Services-Tresor** > **Replizierte Elemente** und klicken Sie auf *Ein neues Site Recovery-Mobilitäts-Agent-Update ist verfügbar*. Klicken Sie darauf, um es zu installieren.

   ![Den Mobilitäts-Agent für mehrere geschützte Elemente manuell aktualisieren](./media/upgrade-mobility-service-preview/agent-update.png)

2. Wählen Sie die zu aktualisierenden Quellcomputer aus und klicken Sie dann auf **OK**.

   >[!NOTE]
   >Wenn die Voraussetzungen zur Aktualisierung des Mobilitätsdiensts nicht erfüllt sind, kann der virtuelle Computer nicht ausgewählt werden. Weitere Informationen finden Sie unter [Fehlerbehebung](#resolve-blocking-issues-for-agent-upgrade).


4. Nach dem Initiieren des Upgrades wird ein Site Recovery-Auftrag im Tresor für jeden Upgradevorgang erstellt und kann nachverfolgt werden, indem Sie zu **Überwachung** > **Site Recovery-Aufträge** navigieren.

### <a name="update-mobility-agent-for-a-single-protected-machine"></a>Aktualisieren des Mobilitäts-Agents für einen einzelnen geschützten Computer

Führen Sie die folgenden Schritte aus, um den Mobilitäts-Agent eines geschützten Elements zu aktualisieren:
1. Navigieren Sie zu **Recovery Services-Tresor** > **Replizierte Elemente** und wählen Sie einen virtuellen Computer aus.
2. Sehen Sie sich auf dem Blatt **Übersicht** des virtuellen Computers im Bereich **Agent-Version** die aktuelle Version des Mobilitäts-Agents an. Wenn ein neues Update verfügbar ist, wird der Status auf **Neues Update verfügbar** aktualisiert.

   ![Den Mobilitäts-Agent für ein einzelnes geschütztes Element manuell aktualisieren](./media/upgrade-mobility-service-preview/agent-version.png)

3. Klicken Sie auf **Neues Update verfügbar**. Die neueste verfügbare Version wird angezeigt. Klicken Sie auf **Auf diese Version aktualisieren**, um den Updateauftrag zu starten.

   ![Updatedetails für den Mobilitäts-Agent](./media/upgrade-mobility-service-preview/agent-update-details.png)

   > [!NOTE]
   > Wenn das Upgrade blockiert ist, überprüfen und beheben Sie die Fehler wie [hier](#resolve-blocking-issues-for-agent-upgrade) beschrieben.

## <a name="mobility-agent-on-latest-version"></a>Mobilitäts-Agent mit der neuesten Version

Nachdem der Mobilitäts-Agent auf die neueste Version aktualisiert oder automatisch auf die neueste Version aktualisiert wurde, wird der Status als **Aktuell** angezeigt.

### <a name="resolve-blocking-issues-for-agent-upgrade"></a>Beheben von Blockierungsproblemen beim Agent-Upgrade

Wenn die Voraussetzungen für das Upgrade des Mobilitäts-Agents nicht erfüllt sind, kann der virtuelle Computer nicht aktualisiert werden. Beheben Sie diese Schritte, um mit dem Upgrade fortzufahren.

Die Voraussetzungen umfassen u. a. folgendes:

- Ein ausstehender obligatorischer Neustart auf dem geschützten Computer.

- Wenn die Replikationsappliance in einer inkompatiblen Version vorliegt.

- Wenn die Komponenten der Replikationsappliance (Proxyserver oder Prozessserver) nicht mit Azure-Diensten kommunizieren können.

- Wenn der Mobilitäts-Agent auf dem geschützten Computer nicht mit der Replikationsappliance kommunizieren kann.

Falls einer der oben genannten Punkte zutrifft, wird der Status auf **Kann nicht auf die neueste Version aktualisiert werden** aktualisiert. Klicken Sie auf den Status, um die Gründe für das Blockieren des Updates und empfohlene Aktionen zur Behebung des Problems anzuzeigen.

>[!NOTE]
>Warten Sie nach dem Beheben der Blockierung 30 Minuten und versuchen Sie es dann erneut. Es dauert eine Weile, bis die neuesten Informationen in den Site Recovery-Diensten aktualisiert sind.

### <a name="mobility-agent-upgrade-job-failure"></a>Fehler beim Upgradeauftrag des Mobilitäts-Agents

Falls der Upgradevorgang des Mobilitäts-Agents fehlschlägt (manuell ausgelöster oder automatischer Upgradevorgang), wird der Auftrag mit der Ursache für den Fehler aktualisiert. Beheben Sie die Fehler und wiederholen Sie den Vorgang.

Um die Fehler zu sehen, können Sie zu den Site Recovery-Aufträgen navigieren und auf einen bestimmten Auftrag klicken. Dann werden Ihnen die entsprechenden Lösungsmöglichkeiten angezeigt. Alternativ können Sie auch die folgenden Schritte ausführen:

1. Navigieren Sie zum Abschnitt replizierte Elemente und wählen Sie einen bestimmten virtuellen Computer aus.

2. Auf dem Blatt **Übersicht** wird für die **Agent-Version** die aktuelle Version des Mobilitäts-Agents angezeigt.

3. Neben der aktuellen Version wird der Status mit der Meldung **Update fehlgeschlagen** aktualisiert. Klicken Sie auf den Status, um den Aktualisierungsvorgang zu wiederholen.

4.  Ein Link zum vorherigen Upgradeauftrag ist verfügbar. Klicken Sie auf den Auftrag, um zum jeweiligen Auftrag zu navigieren.

5. Beheben Sie die Fehler des vorherigen Auftrags.

Lösen Sie den Aktualisierungsvorgang aus, nachdem Sie die Fehler des vorherigen fehlgeschlagenen Auftrags behoben haben.

## <a name="upgrade-appliance"></a>Upgrade der Appliance

Standardmäßig sind für eine Appliance automatische Updates aktiviert. Automatische Updates werden täglich um 24:00 Uhr lokaler Zeit ausgelöst, wenn eine neue Version für eine der Komponenten verfügbar ist.

Navigieren Sie zum Überprüfen des Updatestatus einer der Komponenten zum Applianceserver und öffnen Sie den **Microsoft Azure Appliance Configuration Manager**. Navigieren Sie zu **Appliancekomponenten** und erweitern Sie diese, um die Liste aller Komponenten und deren Version anzuzeigen.

Wenn einer dieser aktualisiert werden muss, wird der **Status** entsprechend angezeigt. Klicken Sie auf die Statusmeldung, um das Upgrade der Komponente durchzuführen.

  ![Komponenten der Replikationsappliance](./media/upgrade-mobility-service-preview/appliance-components.png)

### <a name="turn-off-auto-update"></a>Deaktivieren der automatischen Aktualisierung

1. Öffnen Sie auf dem Server, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
2. Navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Zum Deaktivieren der automatischen Aktualisierung erstellen Sie einen Registrierungsschlüssel **AutoUpdate** mit dem DWORD-Wert 0.

    ![Festlegen des Registrierungsschlüssels](./media/upgrade-mobility-service-preview/registry-key.png)


### <a name="turn-on-auto-update"></a>Aktivieren der automatischen Aktualisierung

Sie können die automatische Aktualisierung aktivieren, indem Sie den AutoUpdate-Registrierungsschlüssel aus HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance löschen.

So löschen Sie den Registrierungsschlüssel:

1. Öffnen Sie auf dem Server, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
2. Navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Löschen Sie den Registrierungsschlüssel **AutoUpdate**, der zuvor zum Deaktivieren der automatischen Aktualisierung erstellt wurde.

### <a name="resolve-issues-with-component-upgrade"></a>Beheben von Problemen beim Komponentenupgrade

Wenn die Voraussetzungen für das Upgrade einer der Komponenten nicht erfüllt sind, kann sie nicht aktualisiert werden. Gründe/Voraussetzungen sind z. B.:

- Wenn eine Komponente der Replikationsappliance in einer inkompatiblen Version vorliegt.

- Wenn die Replikationsappliance nicht mit Azure-Diensten kommunizieren kann.

Falls einer der oben genannten Punkte zutrifft, wird der Status auf **Kann nicht auf die neueste Version aktualisiert werden** aktualisiert. Klicken Sie auf den Status, um die Gründe für das Blockieren des Updates und empfohlene Aktionen zur Behebung des Problems anzuzeigen. Nachdem Sie die Gründe der Blockierung behoben haben, versuchen Sie das Update manuell vorzunehmen.
