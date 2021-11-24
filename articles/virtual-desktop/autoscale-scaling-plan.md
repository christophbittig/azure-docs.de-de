---
title: Vorschauversion der automatischen Skalierung von Azure Virtual Desktop-Sitzungshosts
description: Verwenden der Funktion für die automatische Skalierung zum Zuordnen von Ressourcen in Ihrer Bereitstellung.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: a88c9ecef36786f67c930a22ecdd67d5890da92f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399065"
---
# <a name="autoscale-preview-for-azure-virtual-desktop-host-pools"></a>Automatische Skalierung (Vorschau) für Azure Virtual Desktop-Hostpools

> [!IMPORTANT]
> Die automatische Skalierung ist derzeit im Vorschaustadium verfügbar.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Mit dem Feature für die automatische Skalierung (Vorschauversion) können Sie die virtuellen Computer (VMs) Ihrer Azure Virtual Desktop-Bereitstellung hoch- oder herunterskalieren, um die Bereitstellungskosten zu optimieren. Basierend auf Ihren Anforderungen können Sie einen Skalierungsplan erstellen, der auf Folgendem basiert:

- Uhrzeit
- Bestimmte Wochentage
- Sitzungslimits pro Sitzungshost

>[!NOTE]
> - Azure Virtual Desktop (klassisch) unterstützt das Feature „Automatische Skalierung“ nicht. 
> - Die Autoskalierung unterstützt Azure Virtual Desktop für Azure Stack HCI nicht. 
> - Die automatische Skalierung unterstützt keine Skalierung kurzlebiger Datenträger.


Um optimale Ergebnisse zu erzielen, empfehlen wir die Verwendung der automatischen Skalierung mit virtuellen Computern, die Sie mit Azure Resource Manager-Vorlagen für Azure Virtual Desktop oder Erstherstellertools von Microsoft bereitgestellt haben.

>[!IMPORTANT]
>Für die Vorschauversion dieses Features gelten derzeit die folgenden Einschränkungen:
>
> - Sie können die automatische Skalierung nur in der öffentlichen Azure-Cloud verwenden.
> - Sie können die automatische Skalierung nur mit dem Azure-Portal konfigurieren.
> - Sie können den Skalierungsplan nur den US-Amerikanischen und europäischen Regionen bereitstellen.

## <a name="requirements"></a>Anforderungen

Bevor Sie Ihren ersten Skalierungsplan erstellen, stellen Sie sicher, dass Sie die folgenden Richtlinien befolgen:

- Derzeit können Sie die automatische Skalierung nur mit in einem Pool vorhandenen Hostpools konfigurieren.
- Alle Hostpools, die Sie automatisch skalieren, müssen über einen konfigurierten MaxSessionLimit-Parameter verfügen. Verwenden Sie den Standardwert nicht. Sie können diesen Wert in den Hostpooleinstellungen im Azure-Portal konfigurieren oder die Cmdlets [New-AZWvdHostPool](/powershell/module/az.desktopvirtualization/new-azwvdhostpool?view=azps-5.7.0&preserve-view=true) oder [Update-AZWvdHostPool](/powershell/module/az.desktopvirtualization/update-azwvdhostpool?view=azps-5.7.0&preserve-view=true) in PowerShell ausführen.
- Sie müssen Azure Virtual Desktop Zugriff gewähren, um den durch Ihre VM-Computeressourcen verursachten Energieverbrauch zu verwalten.

## <a name="create-a-custom-rbac-role"></a>Erstellen einer benutzerdefinierten RBAC-Rolle

Um mit der Erstellung eines Skalierungsplans zu beginnen, müssen Sie zunächst eine benutzerdefinierte Rolle für rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Ihrem Abonnement erstellen. Diese Rolle ermöglicht Windows Virtual Desktop den durch die VMs in Ihrem Abonnement verursachten Energieverbrauch zu verwalten. Außerdem kann der Dienst Aktionen sowohl auf Hostpools als auch auf VMs anwenden, wenn keine aktiven Benutzersitzungen durchgeführt werden.

Befolgen Sie zum Erstellen der benutzerdefinierten Rolle die Anweisungen unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md), während Sie die folgende JSON-Vorlage verwenden. Diese Vorlage enthält bereits alle erforderlichen Berechtigungen. Ausführlichere Anweisungen finden Sie unter [Zuweisen von benutzerdefinierten Rollen mit dem Azure-Portal](#assign-custom-roles-with-the-azure-portal).
```json
 {
 "properties": {
 "roleName": "Autoscale",
 "description": "Friendly description.",
 "assignableScopes": [
 "/subscriptions/<SubscriptionID>"
 ],
  "permissions": [
   {
   "actions": [
                 "Microsoft.Insights/eventtypes/values/read",
                 "Microsoft.Compute/virtualMachines/deallocate/action",
                 "Microsoft.Compute/virtualMachines/restart/action",
                 "Microsoft.Compute/virtualMachines/powerOff/action",
                 "Microsoft.Compute/virtualMachines/start/action",
                 "Microsoft.Compute/virtualMachines/read",
                 "Microsoft.DesktopVirtualization/hostpools/read",
                 "Microsoft.DesktopVirtualization/hostpools/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
],
  "notActions": [],
  "dataActions": [],
  "notDataActions": []
  }
 ]
}
}
```

## <a name="assign-custom-roles-with-the-azure-portal"></a>Zuweisen von benutzerdefinierten Rollen mit dem Azure-Portal

So erstellen Sie die benutzerdefinierte Rolle mit dem Azure-Portal und weisen sie Ihrem Abonnement zu:

1. Navigieren Sie im Azure-Portal zu **Abonnements**.

2. Wählen Sie die **+** -Schaltfläche in der oberen linken Ecke des Bildschirms und dann **Benutzerdefinierte Rolle hinzufügen** im Dropdownmenü aus, wie im folgenden Screenshot gezeigt.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Dropdownmenüs, das angezeigt wird, wenn Sie in der Systemsteuerung des Azure-Portals auf das Pluszeichen klicken. Die Option „Benutzerdefinierte Rolle hinzufügen“ ist ausgewählt und wird mit einem roten Rahmen hervorgehoben.](media/add-custom-role.png)

3. Benennen Sie als Nächstes die benutzerdefinierte Rolle, und fügen Sie eine Beschreibung hinzu. Es wird empfohlen, die Rolle „Autoskalierung“ zu nennen.

4. Fügen Sie auf der Registerkarte **Berechtigungen** dem Abonnement, dem Sie die Rolle zuweisen, die folgenden Berechtigungen hinzu:

    ```azcopy
        "Microsoft.Insights/eventtypes/values/read"
                 "Microsoft.Compute/virtualMachines/deallocate/action"
                 "Microsoft.Compute/virtualMachines/restart/action"
                 "Microsoft.Compute/virtualMachines/powerOff/action"
                 "Microsoft.Compute/virtualMachines/start/action"
                 "Microsoft.Compute/virtualMachines/read"
                 "Microsoft.DesktopVirtualization/hostpools/read"
                 "Microsoft.DesktopVirtualization/hostpools/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
    ```

5. Klicken Sie auf **OK**, wenn Sie fertig sind.

Danach müssen Sie die Rolle zuweisen, um Azure Virtual Desktop Zugriff zu gewähren.

So weisen Sie die benutzerdefinierte Rolle zu, um Zugriff zu gewähren:

1. Wählen Sie auf der Registerkarte **Zugriffssteuerung (IAM)** die Option **Rollenzuweisungen hinzufügen** aus.

2. Wählen Sie die soeben erstellte Rolle aus und fahren Sie mit dem nächsten Bildschirm fort.

3. Klicken Sie auf **Mitglieder auswählen**. Geben Sie in der Suchleiste **Windows Virtual Desktop** ein, und wählen Sie diese Eingabe aus, wie im folgenden Screenshot gezeigt. Wenn Sie über eine Azure Virtual Desktop-Bereitstellung (klassisch) und eine Azure Virtual Desktop-Anwendung mit Azure Resource Manager Azure Virtual Desktop-Objekten verfügen, werden zwei Apps mit dem gleichen Namen angezeigt. Wählen Sie beide aus.

    > [!div class="mx-imgBorder"]
    > ![Ein Screenshot des Menüs zur Rollenzuweisung. Das Feld „Auswählen“ ist rot hervorgehoben, und der Benutzer gibt „Windows Virtual Desktop“ in das Suchfeld ein.](media/search-for-role.png)

4. Wählen Sie **Überprüfen + Zuweisen** aus, um die Zuweisung abzuschließen.

## <a name="how-creating-a-scaling-plan-works"></a>So wird ein Skalierungsplan erstellt

Bevor Sie Ihren Plan erstellen, beachten Sie Folgendes:

- Sie können einen Skalierungsplan einem oder mehrere Hostpools desselben Hostpooltyps zuweisen. Der Zeitplan des Skalierungsplans wird auch auf alle zugewiesenen Hostpools angewendet.

- Sie können nur einen Skalierungsplan pro Hostpool zuordnen. Wenn Sie mehreren Hostpools einen einzelnen Skalierungsplan zuweisen, können diese Hostpools nicht einem anderen Skalierungsplan zugewiesen werden.

- Ein Skalierungsplan kann nur in seiner konfigurierten Zeitzone eingesetzt werden.

- Ein Skalierungsplan kann einen oder mehrere Zeitpläne haben. Beispielsweise unterschiedliche Zeitpläne an Wochentagen im Vergleich zum Wochenende.

- Stellen Sie sicher, dass Sie die Verwendungsmuster verstehen, bevor Sie Ihren Zeitplan definieren. Sie müssen ungefähr die folgenden Tageszeiten einplanen:

    - Anlaufzeiten: Der Anfang des Tages, an dem die Nutzung beginnt.
    - Spitzenzeiten: Die Tageszeit, zu der die Nutzung am höchsten ist.
    - Auslaufzeiten: Wenn die Nutzung nachlässt. Dies ist normalerweise der Fall, wenn Sie Ihre VMs herunterfahren, um Kosten zu sparen.
    - Nebenzeiten: Die Zeiten mit der geringstmöglichen Nutzung. Sie können die maximale Anzahl von VMs definieren, die während dieser Zeit aktiv sein können.

- Der Skalierungsplan wird wirksam, sobald Sie ihn aktivieren.

Beachten Sie auch die folgenden Einschränkungen:

- Verwenden Sie die automatische Skalierung nicht in Kombination mit anderen Skalierungstools von Microsoft oder Drittanbietern. Stellen Sie sicher, dass Sie diese für die Hostpools deaktivieren, für die Sie die Skalierungspläne anwenden.

- Bei der automatischen Skalierung wird der Ausgleichsmodus überschrieben. Achten Sie daher darauf, beim Aktualisieren von VMs in Hostpools Ausschlusstags zu verwenden.

- Bei der automatischen Skalierung werden vorhandene Lastenausgleichsalgorithmen in den Einstellungen Ihres Hostpools ignoriert, und stattdessen wird der Lastenausgleich basierend auf Ihrer Zeitplankonfiguration angewendet.

## <a name="create-a-scaling-plan"></a>Erstellen eines Skalierungsplans

So erstellen Sie einen Skalierungsplan:

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.

2. Wechseln Sie zu **Azure Virtual Desktop** > **Skalierungspläne**, und wählen Sie dann **Erstellen** aus.

3. Suchen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails**, und wählen Sie den Namen des Abonnements aus, dem Sie den Skalierungsplan zuweisen möchten.

4. Wählen Sie **Neu erstellen** aus, wenn Sie eine neue Ressourcengruppe erstellen möchten. Wenn Sie eine vorhandene Ressourcengruppe verwenden möchten, wählen Sie ihren Namen im Dropdownmenü aus.

5. Geben Sie einen Namen für den Skalierungsplan in das Feld **Name** ein.

6. Optional können Sie auch einen Anzeigenamen hinzufügen, der Ihren Benutzern angezeigt wird, und eine Beschreibung für Ihren Plan.

7. Wählen Sie unter **Region** eine Region für Ihren Skalierungsplan aus. Die Metadaten für das Objekt werden in der Geografie gespeichert, die der Region zugeordnet ist. Weitere Informationen zu Regionen finden Sie unter [Datenstandorte für Azure Virtual Desktop](data-locations.md).

8. Wählen Sie für **Zeitzone** die Zeitzone aus, die Sie mit Ihrem Plan verwenden möchten.

9. Geben Sie unter **Ausschlusstags** Tags für VMs ein, die Sie nicht in Skalierungsvorgänge einbeziehen möchten. Sie können diese Funktion beispielsweise für die Wartung verwenden. Wenn Sie VMs im Ausgleichsmodus festgelegt haben, verwenden Sie das Tag, damit die automatische Skalierung den Ausgleichsmodus nicht überschreibt.

10. Wählen Sie **Weiter** aus, um zur Registerkarte **Zeitpläne** zu gelangen.

## <a name="configure-a-schedule"></a>Konfigurieren eines Zeitplans

Mit Zeitplänen können Sie definieren, wann die automatische Skalierung im Laufe des Tages An- und Auslaufzeitmodi aktiviert. In jeder Phase des Zeitplans deaktiviert die automatische Skalierung nur VMs, wenn für einen Sitzungshost keine Sitzungen aktiv sind. Die Standardwerte, die bei Ihrem Versuch, einen Zeitplan zu erstellen, angezeigt werden, sind die vorgeschlagenen Werte für Wochentage. Sie können sie jedoch nach Bedarf ändern. 

So erstellen oder ändern Sie einen Zeitplan:

1. Wählen Sie auf der Registerkarte **Zeitpläne** die Option **Zeitplan hinzufügen** aus.

2. Geben Sie im Feld **Zeitplanname** einen Namen für Ihren Zeitplan ein.

3. Wählen Sie im Feld **Wiederholen am** die Tage aus, an denen Ihr Zeitplan wiederholt werden soll.

4. Füllen Sie auf der Registerkarte **Anlaufzeiten** die folgenden Felder aus:

    - Wählen Sie unter **Startzeit** eine Zeit aus dem Dropdownmenü aus, um mit der Vorbereitung von VMs für Hauptgeschäftszeiten zu beginnen.

    - Für den **Lastenausgleichsalgorithmus** wird empfohlen, den **breitenorientierten Algorithmus** auszuwählen. Beim breitenorientierten Lastenausgleich werden Benutzer auf vorhandene VMs verteilt, um kurze Zugriffszeiten zu gewährleisten.
        
        >[!NOTE]
        >Die hier ausgewählte Einstellung für den Lastenausgleich überschreibt die Einstellung, die Sie für ihre ursprünglichen Hostpooleinstellungen ausgewählt haben.

    - Geben Sie unter **Minimaler Prozentsatz der Hosts** den Prozentsatz der Sitzungshosts ein, auf dem Sie in dieser Phase immer bleiben möchten. Wenn der eingegebene Prozentsatz keine ganze Zahl ist, wird er auf die nächste ganze Zahl aufgerundet. Wenn beispielsweise in einem Hostpool von 7 Sitzungshosts der Mindestprozentsatz der Hosts **10 %** für die Startstunden beträgt, bleibt eine VM während der Startzeiten immer aktiviert, und die Funktion für die automatische Skalierung deaktiviert diesen virtuellen Computer nicht. 
    
    - Geben Sie **unter Kapazitätsschwellenwert** den Prozentsatz der verfügbaren Hostpoolkapazität ein, der eine Skalierungsaktion auslöst. Wenn beispielsweise zwei Sitzungshosts im Hostpool mit einem maximalen Sitzungslimit von 20 aktiviert sind, beträgt die verfügbare Hostpoolkapazität 40. Wenn Sie den Kapazitätsschwellenwert auf **75 %** festlegen und die Sitzungshosts über mehr als 30 Benutzersitzungen verfügen, aktiviert die Funktion für die automatische Skalierung einen dritten Sitzungshost. Dadurch wird die verfügbare Hostpoolkapazität von 40 in 60 geändert.

5. Füllen Sie auf der Registerkarte **Spitzenzeiten** die folgenden Felder aus:

    - Geben Sie unter **Startzeit** eine Startzeit für den Zeitraum ein, zu dem Ihre Nutzungsrate während des Tages am höchsten ist. Stellen Sie sicher, dass sich der Zeitraum in derselben Zeitzone befindet, die Sie für Ihren Skalierungsplan angegeben haben. Diese Zeit ist auch die Endzeit für die Anlaufphase.

    - Für **Lastenausgleich** können Sie entweder breiten- oder tiefenorientierten Lastenausgleich auswählen. Beim breitenorientierten Lastenausgleich werden neue Benutzersitzungen auf alle verfügbaren Sitzungen im Hostpool verteilt. Beim tiefenorientierten Lastenausgleich werden neue Benutzersitzungen auf verfügbare Sitzungshosts mit der höchsten Anzahl von Verbindungen verteilt, für die das maximale Sitzungslimit noch nicht erreicht wurde. Weitere Informationen zu Lastenausgleichstypen finden Sie unter [Konfigurieren der Lastenausgleichsmethode für Azure Virtual Desktop](configure-host-pool-load-balancing.md).

    >[!NOTE]
    >Sie können den Kapazitätsschwellenwert hier nicht ändern. Stattdessen wird die Einstellung, die Sie in **Anlaufzeiten** eingegeben haben, auf diese Einstellung übertragen.

    - Für **Auslaufzeiten** geben Sie Werte in ähnliche Felder wie **Anlaufzeiten** ein, dieses Mal jedoch für den Zeitpunkt, an dem die Nutzung Ihres Hostpools abfällt. Dies beinhaltet folgende Felder:

      - Startzeit
      - Lastenausgleichsalgorithmus
      - Mindestprozentsatz der Hosts (%)
      - Kapazitätsschwellenwert (%)
      - Abmeldung der Benutzer erzwingen

    >[!IMPORTANT]
    >Wenn Sie die Autoscale-Funktion aktiviert haben, um die Abmeldung der Benutzer während des Rampdowns zu erzwingen, wählt die Funktion den Sitzungshost mit der geringsten Anzahl von Benutzersitzungen zum Herunterfahren aus. Die Autoscale-Funktion versetzt den Sitzungshost in den Auslaufmodus, sendet allen aktiven Benutzersitzungen eine Benachrichtigung, dass sie abgemeldet werden, und meldet dann alle Benutzer nach Ablauf der angegebenen Wartezeit ab. Nachdem die Autoscale-Funktion alle Benutzersitzungen abgemeldet hat, wird die Zuordnung der VM aufgehoben. Wenn Sie die erzwungene Abmeldung während des Rampdowns nicht aktiviert haben, werden Sitzungshosts ohne aktive oder getrennte Sitzungen freigegeben.

    - Ebenso funktioniert **Nebenzeiten** in gleicher Weise wie **Spitzenzeiten**:

      - Startzeit, die auch das Ende der Auslaufzeit ist.
      - Lastenausgleichsalgorithmus. Es wird empfohlen, **tiefenorientiert** auszuwählen, um die Anzahl der Sitzungshosts basierend auf den Sitzungen auf jedem virtuellen Computer schrittweise zu reduzieren.
      - Genau wie Spitzenzeiten können Sie den Kapazitätsschwellenwert hier nicht konfigurieren. Stattdessen wird der Wert übernommen, den Sie in **Auslaufzeiten** eingegeben haben.

## <a name="assign-host-pools"></a>Zuweisen von Hostpools

Nachdem Sie Ihren Skalierungsplan eingerichtet haben, ist es nun an der Zeit, den Plan Ihren Hostpools zuzuweisen. Markieren Sie das Kontrollkästchen für jeden Hostpool, den Sie einbeziehen möchten. Wenn Sie die automatische Skalierung nicht aktivieren möchten, deaktivieren Sie alle Kontrollkästchen. Sie können diese Einstellung zu einem späteren Zeitpunkt jederzeit ändern.

>[!NOTE]
>Wenn Sie einen Skalierungsplan erstellen oder aktualisieren, der bereits Hostpools zugewiesen ist, werden die Änderungen sofort angewendet.

## <a name="add-tags"></a>Hinzufügen von Tags 

Danach müssen Sie Tags eingeben. Tags sind Name-Wert-Paare zum Kategorisieren von Ressourcen für eine konsolidierte Abrechnung. Sie können dasselbe Tag auf mehrere Ressourcen und Ressourcengruppen anwenden. Weitere Informationen zum Taggen von Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).

>[!NOTE] 
>Wenn Sie nach dem Erstellen von Tags Ressourceneinstellungen auf anderen Registerkarten ändern, werden Ihre Tags automatisch aktualisiert.

Wenn Sie fertig sind, wechseln Sie zur Registerkarte **Überprüfen + erstellen**, und wählen Sie **Erstellen** aus, um Ihren Hostpool bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihren Skalierungsplan erstellt haben, haben Sie u. a. folgende Möglichkeiten:

- [Zuweisen Ihres Skalierungsplans zu neuen und vorhandenen Hostpools](autoscale-new-existing-host-pool.md)
- [Aktivieren der Diagnose für Ihren Skalierungsplan](autoscale-diagnostics.md)
