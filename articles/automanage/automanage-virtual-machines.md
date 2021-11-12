---
title: Automatische Azure-Verwaltung für virtuelle Computer
description: Erfahren Sie mehr über die automatische Azure-Verwaltung für virtuelle Computer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: b93169ced916f5d16adcbd11fcc2d2217a07b643
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451177"
---
# <a name="preview-azure-automanage-for-machine-best-practices"></a>Vorschau: Azure Automanage für Best Practices für Computer

Dieser Artikel enthält Informationen zu Azure Automanage für Best Practices für Computer, der folgende Vorteile bietet:

- Das Onboarding virtueller Computer zur Auswahl bewährter Azure-Dienste wird intelligent durchgeführt
- Jeder Dienst wird automatisch nach den bewährten Methoden von Azure konfiguriert
- Best Practices-Dienste können angepasst werden.
- Es erfolgt eine Überwachung auf Datendrift, der behoben wird, wenn er erkannt wird
- Bietet eine einfache Benutzererfahrung (zeigen, klicken, festlegen, vergessen)

## <a name="overview"></a>Übersicht

Azure Automanage für Best Practices für Computer ist ein Dienst, durch den es nicht länger notwendig ist, bestimmte Dienste in Azure zu ermitteln, einzubinden und zu konfigurieren, von denen Ihre VM profitieren würde. Diese Dienste gelten als bewährte Azure-Dienste und tragen zur Verbesserung der Zuverlässigkeit, Sicherheit und Verwaltung virtueller Computer bei. Beispieldienste hierfür sind unter anderem [Azure-Updateverwaltung](../automation/update-management/overview.md) und [Azure Backup](../backup/backup-overview.md).

Nachdem Sie Ihre Computer in Azure Automanage integriert haben, wird jeder Best Practices-Dienst mit den empfohlenen Einstellungen konfiguriert. Wenn Sie die Best Practices-Dienste und -Einstellungen jedoch anpassen möchten, können Sie die Option [Benutzerdefiniertes Profil](#custom-profiles) verwenden. 

Die automatische Azure-Verwaltung überwacht auch automatisch den Datendrift und korrigiert ihn, wenn er erkannt wird. Dies bedeutet Folgendes: Wenn Ihre VM oder Ihr Arc-fähiger Server in Azure Automanage eingebunden ist, überwachen wir Ihren Computer, um sicherzustellen, dass er während seines gesamten Lebenszyklus dem zugehörigen [Konfigurationsprofil](#configuration-profile) entspricht. Sobald Ihre VM vom Profil abweicht (weil z. B. ein Offboarding für einen Dienst durchgeführt wird), korrigieren wir dies und versetzen Ihren Computer wieder in den gewünschten Zustand.

Bei der automatischen Verarbeitung werden Kundendaten nicht außerhalb des geografischen Standorts gespeichert bzw. verarbeitet, in dem sich Ihre virtuellen Computer befinden. In der Region „Asien, Südosten“ speichert bzw. verarbeitet Automanage keine Daten außerhalb der Region.

> [!NOTE]
> Die automatische Verwaltung kann auf virtuellen Azure-Computern und auf Azure Arc-fähigen Servern aktiviert werden. Die automatische Verwaltung ist in der US Government Cloud derzeit nicht verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Es gibt mehrere Voraussetzungen zu beachten, bevor Sie versuchen, die automatische Azure-Verwaltung auf Ihren virtuellen Computern zu aktivieren.

- Unterstützte [Windows Server-Versionen](automanage-windows-server.md#supported-windows-server-versions) und [Linux-Distributionen](automanage-linux.md#supported-linux-distributions-and-versions)
- VMs müssen sich in einer unterstützten Region befinden (siehe unten).
- Benutzer muss über die richtigen Berechtigungen verfügen (siehe unten).
- Automatische Verwaltung unterstützt zurzeit keine Sandboxabonnements.
- Windows 10 wird von der automatischen Verwaltung derzeit nicht unterstützt.

### <a name="supported-regions"></a>Unterstützte Regionen
Automanage unterstützt nur VMs in den folgenden Regionen:
* Europa, Westen
* Nordeuropa
* USA (Mitte)
* East US
* USA (Ost) 2
* USA (Westen)
* USA, Westen 2
* Kanada, Mitte
* USA, Westen-Mitte
* USA Süd Mitte
* Japan, Osten
* UK, Süden
* Australien, Osten
* Australien, Südosten
* Asien, Südosten

### <a name="required-rbac-permissions"></a>Erforderliche RBAC-Berechtigungen
Für das Onboarding erfordert Automanage abhängig davon, ob Sie Automanage zum ersten Mal in einem Abonnement aktivieren, leicht abweichende RBAC-Rollen.

Wenn Sie Automanage zum ersten Mal in einem Abonnement aktivieren:
* Rolle **Besitzer** für die Abonnements, die Ihre Computer enthalten, _**oder**_
* Rollen **Mitwirkender** und **Benutzerzugriffsadministrator** in den Abonnements, die Ihre Computer enthalten

Wenn Sie Automanage für einen Computer in einem Abonnement aktivieren, das bereits über Automanage-Computer verfügt:
* Rolle **Mitwirkender** für die Ressourcengruppe, die Ihre Computer enthält

Der Automanage-Dienst erteilt dieser Erstanbieteranwendung (Anwendungs-ID der Automanage-API: d828acde-4b48-47f5-a6e8-52460104a052) die Berechtigung **Mitwirkender**, um Aktionen für Automanage-Computer durchzuführen.

> [!NOTE]
> Wenn Sie Automanage auf einem virtuellen Computer verwenden möchten, der mit einem Arbeitsbereich in einem anderen Abonnement verbunden ist, müssen Sie in jedem Abonnement über die oben beschriebenen Berechtigungen verfügen.

## <a name="participating-services"></a>Beteiligte Dienste

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Intelligentes Durchführen des Onboardings von Diensten.":::

Die vollständige Liste der beteiligten Azure-Dienste sowie das von ihnen unterstützte Profil finden Sie unter den folgenden Links:
- [Automanage für Linux](automanage-linux.md)
- [Automanage für Windows Server](automanage-windows-server.md)

 Es erfolgt ein automatisches Onboarding in diese beteiligten Dienste, wenn Sie die Best Practices-Konfigurationsprofile verwenden. Sie sind für unser Whitepaper über bewährte Methoden unerlässlich, das Sie in unserem [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management) finden.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Aktivieren der automatischen Verwaltung für VMs im Azure-Portal

Im Azure-Portal können Sie Automanage für eine vorhanden VM aktivieren. Eine kurze Anleitung zu diesem Prozess finden Sie unter [Schnellstart zur automatischen Verwaltung für virtuelle Computer](quick-create-virtual-machines-portal.md).

Wenn Sie Automanage zum ersten Mal für Ihre VM aktivieren, können Sie im Azure-Portal nach **Automanage – Best Practices für Azure-Computer** suchen. Klicken Sie auf **Für vorhandene VM aktivieren**, wählen Sie das zu verwendende [Konfigurationsprofil](#configuration-profile) und dann die Computer aus, die Sie einbinden möchten. Klicken Sie auf **Aktivieren**, und der Vorgang ist abgeschlossen.

Der einzige Fall, in dem Sie mit diesem Computer interagieren müssen, um die Dienste zu verwalten, ist ein gescheiterter Versuch, Ihre VM zu reparieren. Wenn wir Ihre VM erfolgreich korrigieren, stellen wir die Compliance wieder her, ohne Sie überhaupt zu warnen. Weitere Details finden Sie unter [Status von VMs](#status-of-vms).

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Aktivieren von Automanage für VMs mit Azure Policy
Sie können Automanage auch im großen Stil auf VMs aktivieren, indem Sie die Azure Policy-Integration verwenden. Die Richtlinie besitzt einen DeployIfNotExists-Effekt. Dies bedeutet, dass alle berechtigten VMs, die sich innerhalb des Richtlinienbereichs befinden, automatisch in die Best Practices für Automanage-VMs integriert werden.

Einen direkten Link zur Richtlinie finden Sie [hier](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3).

Weitere Informationen finden Sie in den Anweisungen zum Aktivieren der [integrierten Richtlinie von Automanage](virtual-machines-policy-enable.md). 

## <a name="configuration-profile"></a>Konfigurationsprofil

Wenn Sie Automanage für Ihren Computer aktivieren, ist ein Konfigurationsprofil erforderlich. Konfigurationsprofile sind die Grundlage dieses Diensts. Sie legen fest, für welche Dienste wir das Onboarding für Ihre Computer durchführen. Sie legen zu einem gewissen Grad ebenfalls fest, wie diese Dienste konfiguriert werden sollen.

### <a name="best-practice-configuration-profiles"></a>Best Practices-Konfigurationsprofile

Es sind derzeit zwei Best Practices-Konfigurationsprofile verfügbar.

- Das Profil **Dev/Test** ist für Entwicklungs-/Testcomputer konzipiert.
- Das Profil **Produktion** ist für die Produktion vorgesehen.

Der Grund für diese Differenzierung liegt darin, dass bestimmte Dienste auf der Grundlage der aktiven Workload empfohlen werden. Auf einem Computer in der Produktion werden wir für Sie z. B. automatisch das Onboarding für Azure Backup durchführen. Für einen Dev/Test-Computer wäre ein Sicherungsdienst jedoch ein unnötiger Kostenfaktor, da Dev/Test-Computer in der Regel geringere geschäftliche Auswirkungen haben.

### <a name="custom-profiles"></a>Benutzerdefinierte Profile

Mit benutzerdefinierten Profilen können Sie die Dienste und Einstellungen anpassen, die Sie auf Ihre Computer anwenden möchten. Dies ist eine hervorragende Option, wenn sich Ihre IT-Anforderungen von den Best Practices unterscheiden. Wenn Sie beispielsweise die Microsoft Antimalware-Lösung nicht verwenden möchten, weil Ihre IT-Organisation die Verwendung einer anderen 	Antischadsoftware vorschreibt, können Sie Microsoft Antimalware bei der Erstellung eines benutzerdefinierten Profils einfach deaktivieren.

> [!NOTE]
> Im Best Practices-Konfigurationsprofil „Dev/Test“ wird die VM in keiner Weise gesichert.

> [!NOTE]
> Wenn Sie das Konfigurationsprofil eines Computers ändern möchten, können Sie ihn einfach mit dem gewünschten Konfigurationsprofil erneut aktivieren. Wenn Ihr Computer jedoch den Status „Upgrade erforderlich“ aufweist, müssen Sie Automanage zunächst deaktivieren und dann erneut aktivieren. 

Die vollständige Liste der teilnehmenden Azure-Dienste, und ob sie Voreinstellungen unterstützen, finden Sie hier:
- [Automanage für Linux](automanage-windows-server.md)
- [Automanage für Windows Server](automanage-windows-server.md)


## <a name="status-of-vms"></a>Status von VMs

Navigieren Sie im Azure-Portal zur Seite **Automanage: Best Practices für Azure-Computer**, auf der alle automatisch verwalteten Computer aufgeführt sind. Hier wird der Gesamtstatus der einzelnen Computer angezeigt.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Liste der konfigurierten virtuellen Computer":::

Für jeden aufgelisteten Computer werden die folgenden Details angezeigt: Name, Konfigurationsprofil, Status, Ressourcentyp, Ressourcengruppe, Abonnement.

Die Spalte **Status** kann die folgenden Zustände anzeigen:
- *In Bearbeitung*: Die VM wurde gerade aktiviert und wird konfiguriert.
- *Konform*: Die VM wurde konfiguriert, und es wurden keine Abweichungen erkannt.
- *Nicht konform*: Die VM weist Abweichungen auf, die nicht korrigiert werden konnten, oder der Computer wurde ausgeschaltet und Automanage versucht, die VM bei der nächsten Ausführung wieder einzubinden oder zu reparieren.
- *Upgrade erforderlich*: Die VM ist in eine frühere Version von Automanage eingebunden und muss [auf die aktuelle Version aktualisiert werden](automanage-upgrade.md).

Wenn der **Status** als *Nicht konform* angezeigt wird, können Sie eine Problembehandlung durchführen, indem Sie auf den Status im Portal klicken und die angebotenen Links zur Problembehandlung verwenden.


## <a name="disabling-automanage-for-vms"></a>Deaktivieren der automatischen Verwaltung für VMs

Möglicherweise entscheiden Sie sich eines Tages dafür, die automatische Verwaltung für bestimmte VMs zu deaktivieren. Auf Ihrem Computer wird z. B. eine hochgradig vertrauliche, sichere Workload ausgeführt, und Sie müssen sie noch weiter sperren, als es Azure von sich aus vorgenommen hätte, sodass Sie den Computer außerhalb der bewährten Methoden von Azure konfigurieren müssen.

Navigieren Sie dazu im Azure-Portal zur Seite **Automanage – Best Practices für Azure-Computer**, auf der alle automatisch verwalteten VMs aufgeführt sind. Aktivieren Sie das Kontrollkästchen neben dem virtuellen Computer, den Sie über die automatische Verwaltung deaktivieren möchten, und klicken Sie dann auf die Schaltfläche zum **Deaktivieren der automatischen Verwaltung**.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Deaktivieren der automatischen Verwaltung auf einem virtuellen Computer":::

Lesen Sie die Meldung im angezeigten Popupelement sorgfältig durch, bevor Sie dem **Deaktivieren** zustimmen.

> [!NOTE]
> Das Deaktivieren der automatischen Verwaltung für eine VM führt zu folgendem Verhalten:
>
> - Die Konfiguration der VM und der Dienste, für die Onboarding durchgeführt wird, ändert sich nicht.
> - Sämtliche Gebühren, die durch diese Dienste anfallen, bleiben abrechenbar und fallen weiterhin an.
> - Die Überwachung des Automanage-Datendrifts wird sofort beendet.


In erster Linie werden wir den virtuellen Computer nicht von einem der Dienste trennen, auf dem wir das Onboarding für ihn durchgeführt und ihn konfiguriert haben. Somit bleiben alle durch diese Dienste entstandenen Kosten weiterhin abrechenbar. Sie müssen ggf. das Onboarding aufheben. Jegliches Verhalten der automatischen Verwaltung wird sofort beendet. Wir werden z. B. die VM nicht mehr auf Datendrift überwachen.

## <a name="automanage-and-azure-disk-encryption"></a>Automanage und Azure Disk Encryption
Automanage ist mit VMs kompatibel, bei denen Azure Disk Encryption (ADE) aktiviert wurde.

Wenn Sie die Produktionsumgebung nutzen, wird auch das Onboarding in Azure Backup durchgeführt. Es gibt eine einzige Voraussetzung für die erfolgreiche Verwendung von ADE und Azure Backup:
* Vor dem Onboarding Ihrer ADE-fähigen VM in die Produktionsumgebung von Automanage müssen Sie die Schritte im Abschnitt **Vorbereitung** [dieses Dokuments](../backup/backup-azure-vms-encryption.md#before-you-start) ausgeführt haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, dass es mit Automanage für Computer nicht mehr notwendig ist, die Best Practices-Dienste von Azure zu kennen, einzubinden und zu konfigurieren. Außerdem wird ein Computer, den Sie in Automanage für VMs integriert haben, automatisch wieder mit dem Konfigurationsprofil in Einklang gebracht, wenn es von diesem abweicht.

Sie können Automanage für Azure-VMs oder Arc-fähige Server im Azure-Portal aktivieren.

> [!div class="nextstepaction"]
> [Aktivieren der automatischen Verwaltung für virtuelle Computer im Azure-Portal](quick-create-virtual-machines-portal.md)
