---
title: Adaptive Anwendungssteuerung in Microsoft Defender für Cloud
description: In diesem Dokument erfahren Sie, wie Sie die adaptive Anwendungssteuerung in Microsoft Defender für Cloud verwenden, um eine Liste von Anwendungen zu erstellen, die für Azure-Computer ausgeführt werden dürfen.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 717ef750a6948a7e3d97a304d79869249346dc93
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526139"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Verwenden von adaptiven Anwendungssteuerungen zum Verringern der Angriffsfläche Ihres Computers

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Informieren Sie sich über die Vorteile der adaptiven Anwendungssteuerungen von Microsoft Defender für Cloud und darüber, wie Sie Ihre Sicherheit mit diesem datengesteuerten, intelligenten Feature verbessern können.

## <a name="what-are-adaptive-application-controls"></a>Was ist die adaptive Anwendungssteuerung?

Adaptive Anwendungssteuerungen sind eine intelligente und automatisierte Lösung zum Definieren von Zulassungslisten bekannter, sicherer Anwendungen für Ihre Computer. 

Häufig verfügen Organisationen über Sammlungen von Computern, die routinemäßig dieselben Prozesse ausführen. Microsoft Defender für Cloud nutzt maschinelles Lernen, um die auf Ihren Computern ausgeführten Anwendungen zu analysieren und eine Liste bekanntermaßen sicherer Software zu erstellen. Zulassungslisten basieren auf Ihren spezifischen Azure-Workloads, und Sie können die Empfehlungen mithilfe der unten stehenden Anweisungen weiter anpassen.

Wenn Sie adaptive Anwendungssteuerungen aktiviert und konfiguriert haben, erhalten Sie Sicherheitswarnungen, wenn eine Anwendung ausgeführt wird, die nicht als sicher definiert ist.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Was sind die Vorteile von adaptiven Anwendungssteuerungen?

Durch die Definition von Listen mit bekannten sicheren Anwendungen und das Generieren von Warnungen, wenn etwas anderes ausgeführt wird, können Sie mehrere Übersichts- und Complianceziele erreichen:

- Erkennen potenzieller Schadsoftware, auch wenn sie von Antischadsoftware-Lösungen übersehen wird
- Verbessern der Konformität mit lokalen Sicherheitsrichtlinien, die die ausschließliche Verwendung lizenzierter Software vorschreiben
- Identifizieren veralteter oder nicht unterstützter Anwendungsversionen 
- Identifizieren von Software, die von Ihrer Organisation gesperrt ist, aber trotzdem auf Ihren Computern ausgeführt wird
- Erhöhen der Überwachung von Apps, die auf sensible Daten zugreifen

Derzeit sind keine Erzwingungsoptionen verfügbar. Adaptive Anwendungssteuerungen sollen Sicherheitswarnungen ausgeben, wenn eine andere als die von Ihnen als sicher definierte Anwendung ausgeführt wird.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Erfordert [Microsoft Defender für Server](defender-for-servers-introduction.md)|
|Unterstützte Computer:|:::image type="icon" source="./media/icons/yes-icon.png"::: Azure- und Nicht-Azure-Computer, auf denen Windows und Linux ausgeführt wird<br>:::image type="icon" source="./media/icons/yes-icon.png"::: [Azure Arc](../azure-arc/index.yml)-Computer|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsleseberechtigter**- und **Leseberechtigter**-Rollen können Gruppen und Listen der bekannten sicheren Anwendungen anzeigen<br>**Mitwirkender**- und **Sicherheitsadministrator**-Rollen können Gruppen und Listen der bekannten sicheren Anwendungen bearbeiten|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Aktivieren von Anwendungssteuerelementen auf einer Gruppe von Computern

Wenn Microsoft Defender für Cloud Gruppen von Computern in Ihren Abonnements identifiziert hat, die konsistent einen ähnlichen Menge an Anwendungen ausführen, erhalten Sie die folgende Empfehlung: **Die adaptive Anwendungssteuerung zum Definieren sicherer Anwendungen sollten auf Ihren Computern aktiviert werden.**

Wählen Sie die Empfehlung aus, oder öffnen Sie die Seite der adaptiven Anwendungssteuerung, um die Liste der empfohlenen, bekannten sicheren Anwendungen und Gruppen von Computern anzuzeigen.

1. Öffnen Sie das Workloadschutz-Dashboard, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerung** aus.

    :::image type="content" source="./media/adaptive-application/opening-adaptive-application-control.png" alt-text="Öffnen von „Adaptive Anwendungssteuerungen“ über das Azure-Dashboard." lightbox="./media/adaptive-application/opening-adaptive-application-control.png":::

    Die Seite **Adaptive Anwendungssteuerung** wird geöffnet, auf der Ihre VMs auf den folgenden Registerkarten gruppiert sind:

    - **Konfiguriert**: Gruppen von Computern, die bereits über eine definierte Zulassungsliste von Anwendungen verfügen. Für jede Gruppe zeigt die konfigurierte Registerkarte Folgendes an:
        - die Anzahl von Computern in der Gruppe
        - letzte Warnungen

    - **Empfohlen**: Gruppen von Computern, auf denen die gleichen Anwendungen konsistent ausgeführt werden, und für die keine Zulassungsliste konfiguriert ist. Sie sollten die adaptive Anwendungssteuerung für diese Gruppen zu aktivieren.
    
      > [!TIP]
      > Wenn Sie einen Gruppennamen mit dem Präfix "REVIEWGROUP" sehen, enthält er Rechner mit einer teilweise konsistenten Liste von Anwendungen. Microsoft Defender für Cloud kann kein Muster erkennen, empfiehlt aber, diese Gruppe zu überprüfen, um festzustellen, ob _Sie_ manuell einige Regeln für die adaptive Anwendungssteuerung definieren können. Dies ist in [Bearbeiten der Regel für die adaptive Anwendungssteuerung einer Gruppe](#edit-a-groups-adaptive-application-controls-rule) beschrieben.
      >
      > Sie können auch Computer aus dieser Gruppe in andere Gruppen verschieben, wie in [Verschieben eines Computers aus einer Gruppe in eine andere](#move-a-machine-from-one-group-to-another) beschrieben.

    - **Keine Empfehlung**: Computer ohne definierte Zulassungsliste von Anwendungen, die die Funktion nicht unterstützen. Ihr Computer befindet sich aus folgenden Gründen möglicherweise auf dieser Registerkarte:
      - Ein Log Analytics-Agent fehlt.
      - Der Log Analytics-Agent sendet keine Ereignisse.
      - Es handelt sich um einen Windows-Computer mit einer bereits vorhandenen [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview)-Richtlinie, die entweder durch ein Gruppenrichtlinienobjekt oder eine lokale Sicherheitsrichtlinie aktiviert ist.

      > [!TIP]
      > Defender für Cloud benötigt mindestens die Daten von zwei Wochen, um die eindeutigen Empfehlungen pro Gruppe von Computern zu definieren. Computer, die vor kurzem erstellt wurden oder zu Abonnements gehören, die erst kürzlich mit Microsoft Defender für Server geschützt wurden, werden unter der Registerkarte **Keine Empfehlung** angezeigt.

1. Öffnen Sie die Registerkarte **Empfohlen**. Die Gruppen von Computern mit empfohlenen Zulassungslisten werden angezeigt.

   ![Registerkarte „Empfohlen“.](./media/adaptive-application/adaptive-application-recommended-tab.png)

1. Wählen Sie eine Gruppe aus. 

1. Um die neue Regel zu konfigurieren, überprüfen Sie die verschiedenen Abschnitte dieser Seite **Konfigurieren von Regeln zur Anwendungssteuerung** und der Inhalte, die für diese bestimmte Gruppe von Computern eindeutig sein werden:

   ![Konfigurieren einer neuen Regel.](./media/adaptive-application/adaptive-application-create-rule.png)

   1. **Computer auswählen**: Standardmäßig werden alle Computer in der identifizierten Gruppe ausgewählt. Heben Sie die Auswahl von Computern auf, die Sie von dieser Regel ausnehmen möchten.
   
   1. **Empfohlene Anwendungen**: Lesen Sie diese Liste mit Anwendungen, die auf den Computern in dieser Gruppe gängig sind, und für die ein Zulassen der Ausführung empfohlen wird.
   
   1. **Weitere Anwendungen**: Überprüfen Sie diese Liste von Anwendungen, die auf den Computern innerhalb dieser Gruppe entweder seltener vorkommen, oder die als nutzbar bekannt sind. Ein Warnsymbol gibt an, dass eine bestimmte Anwendung von einem Angreifer zur Umgehung einer Anwendungszulassungsliste verwendet werden könnte. Sie sollten diese Anwendungen sorgfältig überprüfen.

      > [!TIP]
      > Beide Anwendungslisten enthalten die Option, eine bestimmte Anwendung auf bestimmte Benutzer zu beschränken. Wenden Sie nach Möglichkeit das Prinzip der geringsten Berechtigungen an.
      > 
      > Anwendungen werden von ihren Herausgebern definiert. Wenn eine Anwendung keine Informationen über den Herausgeber aufweist (nicht signiert ist), wird eine Pfadregel für den vollständigen Pfad der jeweiligen Anwendung erstellt.

   1. Wählen Sie **Überwachung** aus, um die Regel anzuwenden. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Bearbeiten der Regeln für die adaptive Anwendungssteuerung einer Gruppe

Sie können die Zulassungsliste für eine Gruppe von Computern aufgrund bekannter Änderungen in Ihrer Organisation bearbeiten. 

So bearbeiten Sie die Regeln für eine Gruppe von Computern:

1. Öffnen Sie das **Workloadschutz-Dashboard**, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerung** aus.

1. Wählen Sie auf der Registerkarte **Konfiguriert** die Gruppe mit der Regel aus, die Sie bearbeiten möchten.

1. Lesen Sie die verschiedenen Abschnitte der Seite **Konfigurieren von Regeln zur Anwendungssteuerung**, wie unter [Aktivieren der adaptiven Anwendungssteuerung für eine Gruppe von Computern](#enable-application-controls-on-a-group-of-machines) beschrieben.

1. Fügen Sie optional eine oder mehrere benutzerdefinierte Regeln hinzu:

   1. Wählen Sie **Regel hinzufügen** aus.

      ![Hinzufügen einer benutzerdefinierten Regel.](./media/adaptive-application/adaptive-application-add-custom-rule.png)

   1. Falls Sie einen bekannten sicheren Pfad definieren, ändern Sie den **Regeltyp** in „Pfad“, und geben Sie einen einzelnen Pfad ein. Sie können Platzhalter in den Pfad einschließen.
   
      > [!TIP]
      > Einige Szenarien, in denen Platzhalter in einem Pfad nützlich sein können:
      > 
      > * Verwenden eines Platzhalters am Ende eines Pfads, um alle ausführbaren Dateien in diesem Ordner und Unterordnern zuzulassen.
      > * Verwenden eines Platzhalters in der Mitte eines Pfads zum Aktivieren des Namens einer bekannten ausführbaren Datei mit einem sich ändernden Ordnernamen (z. B. persönliche Benutzerordner mit einer bekannten ausführbaren Datei, automatisch generierte Ordnernamen usw.).
  
   1. Definieren Sie die zulässigen Benutzer und die geschützten Dateitypen.

   1. Wenn Sie die Regel definiert haben, wählen Sie **Hinzufügen** aus.

1. Wählen Sie **Speichern** aus, um die Änderungen zu übernehmen.


## <a name="review-and-edit-a-groups-settings"></a>Überprüfen und Bearbeiten der Einstellungen einer Gruppe

1. Zum Anzeigen der Details und Einstellungen der Gruppe wählen Sie **Gruppeneinstellungen** aus.

    In diesem Bereich werden der Name der Gruppe (der geändert werden kann), der Betriebssystemtyp, der Standort und andere relevante Details angezeigt.

    :::image type="content" source="./media/adaptive-application/adaptive-application-group-settings.png" alt-text="Seite „Gruppeneinstellungen“ für adaptive Anwendungssteuerungen." lightbox="./media/adaptive-application/adaptive-application-group-settings.png":::

1. Ändern Sie optional den Namen der Gruppe oder den jeweiligen Schutzmodus für Dateitypen.

1. Wählen Sie **Anwenden** und dann **Speichern** aus.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Antwort auf die Empfehlung „Zulassungslistenregeln in der Richtlinie für die adaptive Anwendungssteuerung müssen aktualisiert werden“

Diese Empfehlung wird angezeigt, wenn das maschinelle Lernen von Defender für Cloud ein potenziell legitimes Verhalten identifiziert, das bisher noch nicht zugelassen wurde. Die Empfehlung schlägt neue Regeln für vorhandene Definitionen vor, um die Anzahl der falsch positiven Warnungen zu reduzieren.

So beheben Sie die Probleme:

1. Wählen Sie auf der Empfehlungenseite die Empfehlung **Zulassungslistenregeln in der Richtlinie für die adaptive Anwendungssteuerung müssen aktualisiert werden**, um Gruppen mit neu identifizierten, potenziell legitimen Verhalten anzuzeigen.

1. Wählen Sie die Gruppe mit der Regel aus, die Sie bearbeiten möchten.

1. Lesen Sie die verschiedenen Abschnitte der Seite **Konfigurieren von Regeln zur Anwendungssteuerung**, wie unter [Aktivieren der adaptiven Anwendungssteuerung für eine Gruppe von Computern](#enable-application-controls-on-a-group-of-machines) beschrieben.

1. Wählen Sie **Überwachung** aus, um die Änderungen zu übernehmen.




## <a name="audit-alerts-and-violations"></a>Überwachen von Warnungen und Verstößen

1. Öffnen Sie das **Workloadschutz-Dashboard**, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerung** aus.

1. Wenn Sie Gruppen mit Computern anzeigen möchten, bei denen kürzlich Warnungen aufgetreten sind, überprüfen Sie die auf der Registerkarte **Konfiguriert** aufgelisteten Gruppen.

1. Wählen Sie eine Gruppe zur weiteren Untersuchung aus.

   ![Letzte Warnungen.](./media/adaptive-application/recent-alerts.png)

1. Wählen Sie eine Warnung aus, um weitere Informationen und die Liste der betroffenen Computer zu erhalten.

    Auf der Seite „Warnungen“ werden weitere Details zu den Warnungen angezeigt, und es wird ein Link **Maßnahme ergreifen** mit Empfehlungen zur Entschärfung der Bedrohung angezeigt.

    :::image type="content" source="media/adaptive-application/adaptive-application-alerts-start-time.png" alt-text="Die Startzeit von Warnungen der adaptiven Anwendungssteuerung ist die Zeit, zu der die adaptive Anwendungssteuerung die Warnung erstellt hat.":::

    > [!NOTE]
    > „Adaptive Anwendungssteuerungen“ berechnet Ereignisse einmal alle zwölf Stunden. Die auf der Seite „Warnungen“ angezeigte „Startzeit der Aktivität" ist der Zeitpunkt, zu dem die adaptiven Anwendungssteuerungen die Warnung generiert haben, und **nicht** der Zeitpunkt, zu dem der verdächtige Prozess aktiv war.


## <a name="move-a-machine-from-one-group-to-another"></a>Verschieben eines Computers aus einer Gruppe in eine andere

Wenn Sie einen Computer aus einer Gruppe in eine andere Gruppen verschieben, ändert sich die angewandte Anwendungssteuerungsrichtlinie in die Einstellungen der Gruppe, in die die Verschiebung erfolgt. Sie können einen Computer auch aus einer konfigurierten Gruppe in eine nicht konfigurierte Gruppe verschieben. Dadurch werden alle auf den Computer angewendeten Anwendungssteuerungsregeln entfernt.

1. Öffnen Sie das **Workloadschutz-Dashboard**, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerung** aus.

1. Wählen Sie auf der Seite **Adaptive Anwendungssteuerung** auf der Registerkarte **Konfiguriert** die Gruppe aus, die den Computer enthält, der verschoben werden soll.

1. Öffnen Sie die Liste **Konfigurierte Computer**.

1. Öffnen Sie das Menü des Computers über die drei Punkte am Ende der Zeile, und wählen Sie **Verschieben** aus. Der Bereich **Computer in eine andere Gruppe verschieben** wird geöffnet.

1. Wählen Sie die Zielgruppe und dann **Computer** verschieben aus.

1. Wählen Sie zum Speichern der Änderungen **Speichern** aus.





## <a name="manage-application-controls-via-the-rest-api"></a>Verwalten der Anwendungssteuerung über die REST-API 

Verwenden Sie die REST-API, um Ihre adaptive Anwendungssteuerung programmgesteuert zu verwalten. 

Die entsprechende API-Dokumentation ist im [Abschnitt „Adaptive Anwendungssteuerung“ in den API-Dokumenten für Defender für Cloud](/rest/api/securitycenter/adaptiveapplicationcontrols) verfügbar.

Einige der Funktionen, die über die REST-API verfügbar sind:

* **List**: Ruft alle Ihre Gruppenempfehlungen ab und stellt einen JSON-Code mit einem Objekt für jede Gruppe bereit.

* **Get**: Ruft den JSON-Code mit den vollständigen Empfehlungsdaten (d. h. Liste von Computern, Herausgeber-/Pfadregeln usw.) ab.

* **Put**: Konfiguriert Ihre Regel (verwenden Sie den JSON-Code, den Sie mit **Get** abgerufen haben, als Text für diese Anforderung).
 
   > [!IMPORTANT]
   > Die **Put**-Funktion erwartet weniger Parameter als der vom Get-Befehl zurückgegebene JSON-Code enthält.
   >
   > Entfernen Sie die folgenden Eigenschaften, bevor Sie den JSON-Code in der Put-Anforderung verwenden: recommendationStatus, configurationStatus, issues, location und sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Häufig gestellte Fragen zu adaptiven Anwendungssteuerungen

- [Gibt es Optionen zur Erzwingung der Anwendungssteuerung?](#are-there-any-options-to-enforce-the-application-controls)
- [Warum wird in meinen empfohlenen Anwendungen eine Qualys-App angezeigt?](#why-do-i-see-a-qualys-app-in-my-recommended-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Gibt es Optionen zur Erzwingung der Anwendungssteuerung?
Derzeit sind keine Erzwingungsoptionen verfügbar. Adaptive Anwendungssteuerungen sollen **Sicherheitswarnungen** ausgeben, wenn eine andere als die von Ihnen als sicher definierte Anwendung ausgeführt wird. Sie bieten eine Reihe von Vorteilen ([Was sind die Vorteile adaptiver Anwendungssteuerungen?](#what-are-the-benefits-of-adaptive-application-controls)) und sind, wie auf dieser Seite gezeigt, überaus anpassbar.

### <a name="why-do-i-see-a-qualys-app-in-my-recommended-applications"></a>Warum wird in meinen empfohlenen Anwendungen eine Qualys-App angezeigt?
[Microsoft Defender für Server](defender-for-servers-introduction.md) enthält eine kostenlose Überprüfung auf Sicherheitsrisiken für Ihre Computer. Sie benötigen weder eine Qualys-Lizenz noch ein Qualys-Konto – die Abwicklung erfolgt nahtlos in Defender für Cloud. Ausführliche Informationen zu diesem Scanner und Anweisungen zu seiner Bereitstellung finden Sie in der [integrierten Qualys-Lösung zur Sicherheitsrisikobewertung für Defender für Cloud](deploy-vulnerability-assessment-vm.md).

Um sicherzustellen, dass keine Warnungen generiert werden, wenn Defender für Cloud den Scanner bereitstellt, enthält die empfohlene Zulassungsliste der adaptiven Anwendungssteuerung den Scanner für alle Computer. 


## <a name="next-steps"></a>Nächste Schritte
Auf dieser Seite haben Sie erfahren, wie Sie mithilfe der adaptiven Anwendungssteuerung in Microsoft Defender für Cloud Zulassungslisten für Anwendungen definieren, die auf Azure- und Nicht-Azure-Computern ausgeführt werden. Weitere Informationen zu anderen Cloudworkload-Schutzfeatures finden Sie unter:

* [Grundlegendes zum Just-In-Time(JIT)-VM-Zugriff](just-in-time-access-overview.md)
* [Integration von Security Center in Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
