---
title: Automatische Azure-Verwaltung für virtuelle Computer
description: Erfahren Sie mehr über die automatische Azure-Verwaltung für virtuelle Computer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: c45be7452d71002c2120e37999c2ecfacb8608b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800002"
---
# <a name="preview-azure-automanage-for-virtual-machines"></a>Vorschau: Automatische Azure-Verwaltung (Azure Automanage) für virtuelle Computer

Dieser Artikel enthält Informationen zur automatischen Azure-Verwaltung für virtuelle Computer, die folgende Vorteile bieten:

- Das Onboarding virtueller Computer zur Auswahl bewährter Azure-Dienste wird intelligent durchgeführt
- Jeder Dienst wird automatisch nach den bewährten Methoden von Azure konfiguriert
- Es erfolgt eine Überwachung auf Datendrift, der behoben wird, wenn er erkannt wird
- Bietet eine einfache Benutzererfahrung (zeigen, klicken, festlegen, vergessen)

## <a name="overview"></a>Übersicht

Die automatische Azure-Verwaltung für virtuelle Computer ist ein Dienst, der die Anforderung beseitigt, bestimmte Dienste in Azure zu entdecken, zu integrieren und zu konfigurieren, die Ihrem virtuellen Computer zugute kommen würden. Diese Dienste gelten als bewährte Azure-Dienste und tragen zur Verbesserung der Zuverlässigkeit, Sicherheit und Verwaltung virtueller Computer bei. Beispieldienste hierfür sind unter anderem [Azure-Updateverwaltung](../automation/update-management/overview.md) und [Azure Backup](../backup/backup-overview.md).

Nachdem Sie das Onboarding Ihrer virtuellen Computer für Azure Automanage durchgeführt haben, wird jeder bewährte Azure-Dienst mit den empfohlenen Einstellungen konfiguriert. Die bewährten Methoden sind für jeden Dienst unterschiedlich. Ein Beispiel hierfür könnte Azure Backup sein, bei dem die bewährte Methode darin bestehen könnte, den virtuellen Computer einmal täglich zu sichern und einen Aufbewahrungszeitraum von sechs Monaten einzuhalten.

Die automatische Azure-Verwaltung überwacht auch automatisch den Datendrift und korrigiert ihn, wenn er erkannt wird. Das bedeutet, wenn für Ihren virtuellen Computer das Onboarding in die automatische Azure-Verwaltung durchgeführt wird, konfigurieren wir ihn nicht nur nach den bewährten Azure-Methoden, sondern wir überwachen Ihren Computer, um sicherzustellen, dass er diese bewährten Methoden während seines gesamten Lebenszyklus einhält. Wenn Ihr virtueller Computer von diesen Methoden abweicht (z. B. Offboarding eines Diensts) oder ein Datendrift auftritt, korrigieren wir dies und bringen Ihren Computer wieder in den gewünschten Zustand.

Bei der automatischen Verarbeitung werden Kundendaten nicht außerhalb des geografischen Standorts gespeichert bzw. verarbeitet, in dem sich Ihre virtuellen Computer befinden. In der Region Südostasien speichert bzw. verarbeitet die automatische Verarbeitung keine Daten außerhalb von Südostasien.

> [!NOTE]
> Die automatische Verwaltung kann auf virtuellen Azure-Computern und auf Arc-fähigen Servern aktiviert werden. Die automatische Verwaltung ist in der US Government Cloud derzeit nicht verfügbar.

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
Ihr Konto erfordert etwas andere RBAC-Rollen, je nachdem, ob Sie Automanage mit einem neuen Automanage-Konto aktivieren.

Wenn Sie Automanage mit einem neuen Automanage-Konto aktivieren:
* Rolle **Besitzer** für die Abonnements, die Ihre VMs enthalten, _**oder**_
* Rollen **Mitwirkender** und **Benutzerzugriffsadministrator** in den Abonnements, die Ihre VMs enthalten.

Wenn Sie Automanage mit einem vorhandenen Automanage-Konto aktivieren:
* Rolle **Mitwirkender** für die Ressourcengruppe, die Ihre VMs enthält.

Dem Automanage-Konto werden die Berechtigungen **Mitwirkender** und **Mitwirkender bei Ressourcenrichtlinien** zum Ausführen von Aktionen auf Automanage-Computern erteilt.

> [!NOTE]
> Wenn Sie Automanage auf einem virtuellen Computer verwenden möchten, der mit einem Arbeitsbereich in einem anderen Abonnement verbunden ist, müssen Sie in jedem Abonnement über die oben beschriebenen Berechtigungen verfügen.

## <a name="participating-services"></a>Beteiligte Dienste

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Intelligentes Durchführen des Onboardings von Diensten.":::

Die vollständige Liste der teilnehmenden Azure-Dienste sowie deren unterstützte Umgebung finden Sie unter:
- [Automanage für Linux](automanage-linux.md)
- [Automanage für Windows Server](automanage-windows-server.md)

 Wir werden mit Ihnen automatisch das Onboarding für diese teilnehmenden Dienste durchführen. Sie sind für unser Whitepaper über bewährte Methoden unerlässlich, das Sie in unserem [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management) finden.

Bei all diesen Diensten werden wir das Onboarding automatisch durchführen, sie automatisch konfigurieren, auf Datendrift überwachen und schlichten, wenn ein Datendrift festgestellt wird.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Aktivieren der automatischen Verwaltung für VMs im Azure-Portal

Im Azure-Portal können Sie die automatische Verwaltung auf einem vorhandenen virtuellen Computer oder beim Erstellen eines neuen virtuellen Computers aktivieren. Eine kurze Anleitung zu diesem Prozess finden Sie unter [Schnellstart zur automatischen Verwaltung für virtuelle Computer](quick-create-virtual-machines-portal.md).

Wenn Sie zum ersten Mal die automatische Verwaltung für Ihre VM aktivieren, können Sie im Azure-Portal nach **Automatische Verwaltung – Bewährte Methoden für Azure-VMs** suchen. Klicken Sie auf **Für vorhandene VM aktivieren**, wählen Sie die VMs aus, für die Sie das Onboarding durchführen möchten, klicken Sie auf **Auswählen** und dann auf **Aktivieren**, dann sind Sie fertig.

Die einzige Zeit, in der Sie möglicherweise mit dieser VM interagieren müssen, um diese Dienste zu verwalten, ist für den Fall, dass wir versucht haben, Ihre VM zu reparieren, dies jedoch nicht gelungen ist. Wenn wir Ihre VM erfolgreich korrigieren, stellen wir die Compliance wieder her, ohne Sie überhaupt zu warnen. Weitere Details finden Sie unter [Status von VMs](#status-of-vms).

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Aktivieren von Automanage für VMs mit Azure Policy
Sie können Automanage auch im großen Stil auf VMs aktivieren, indem Sie die Azure Policy-Integration verwenden. Die Richtlinie besitzt einen DeployIfNotExists-Effekt. Dies bedeutet, dass alle berechtigten VMs, die sich innerhalb des Richtlinienbereichs befinden, automatisch in die Best Practices für Automanage-VMs integriert werden.

Einen direkten Link zur Richtlinie finden Sie [hier](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3).

### <a name="how-to-apply-the-policy"></a>Anwenden der Richtlinie
1. Zeigen Sie die Richtliniendefinition an, und klicken Sie auf die Schaltfläche **Zuweisen**.
1. Wählen Sie den Bereich aus, in dem Sie die Richtlinie anwenden möchten. (Dabei kann es sich um eine Verwaltungsgruppe, ein Abonnement oder eine Ressourcengruppe handeln.)
1. Geben Sie unter **Parameter** Parameter für das Automanage-Konto, das Konfigurationsprofil und den Effekt an. (Der Effekt sollte normalerweise „DeployIfNotExists“ lauten.)
    1. Wenn Sie über kein Automanage-Konto verfügen, müssen Sie [eines erstellen](./automanage-account.md).
1. Aktivieren Sie unter **Wartung** das Kontrollkästchen „Auf Wartungsaufgabe klicken“. Dadurch wird das Onboarding in Automanage durchgeführt.
1. Klicken Sie auf **Überprüfen und erstellen**, und stellen Sie sicher, dass alle Einstellungen fehlerfrei sind.
1. Klicken Sie auf **Erstellen**.

## <a name="environment-configuration"></a>Umgebungskonfiguration

Wenn Sie Automanage für Ihren virtuellen Computer aktivieren, ist eine Umgebung erforderlich. Umgebungen sind die Grundlage dieses Diensts. Sie legen fest, für welche Dienste wir das Onboarding für Ihre Computer durchführen. Sie legen zu einem gewissen Grad ebenfalls fest, wie diese Dienste konfiguriert werden sollen.

### <a name="default-environments"></a>Standardumgebungen

Zurzeit sind zwei Umgebungen verfügbar.

- Die **Dev/Test**-Umgebung ist für Entwicklungs-/Testcomputer (Dev/Test) konzipiert.
- Die **Produktions** umgebung ist für die Produktion vorgesehen.

Der Grund für diese Differenzierung liegt darin, dass bestimmte Dienste auf der Grundlage der aktiven Workload empfohlen werden. Auf einem Computer in der Produktion werden wir für Sie z. B. automatisch das Onboarding für Azure Backup durchführen. Für einen Dev/Test-Computer wäre ein Sicherungsdienst jedoch ein unnötiger Kostenfaktor, da Dev/Test-Computer in der Regel geringere geschäftliche Auswirkungen haben.

### <a name="customizing-an-environment-using-preferences"></a>Anpassen einer Umgebung mithilfe von Voreinstellungen

Zusätzlich zu den Standarddiensten, für die wir das Onboarding für Sie durchführen, ermöglichen wir Ihnen die Konfiguration einer bestimmten Teilmenge von Voreinstellungen. Diese Voreinstellungen sind innerhalb einer Reihe von Konfigurationsoptionen zulässig. Im Falle von Azure Backup gestatten wir Ihnen z. B., die Häufigkeit der Sicherung zu definieren und festzulegen, an welchem Wochentag sie erfolgt.

> [!NOTE]
> In der Dev/Test-Umgebung werden wir die VM überhaupt nicht sichern.

Sie können die Einstellungen eine Standardumgebung über Voreinstellungen anpassen. Erfahren Sie [hier](virtual-machines-custom-preferences.md), wie Sie eine Voreinstellung erstellen.

> [!NOTE]
> Sie können die Umgebungskonfiguration auf Ihrer VM nicht ändern, solange Automanage aktiviert ist. Sie müssen Automanage für diese VM deaktivieren und dann Automanage mit der gewünschten Umgebung und den gewünschten Voreinstellungen erneut aktivieren.

Die vollständige Liste der teilnehmenden Azure-Dienste, und ob sie Voreinstellungen unterstützen, finden Sie hier:
- [Automanage für Linux](automanage-windows-server.md)
- [Automanage für Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Konto für die automatische Verwaltung

Das Konto für die automatische Verwaltung ist der Sicherheitskontext oder die Identität, unter der die automatisierten Vorgänge auftreten. Normalerweise müssen Sie die Option „Automanage-Konto“ nicht auswählen. Wenn Sie aber in einem Delegierungsszenario die automatisierte Verwaltung Ihrer Ressourcen aufteilen möchten (vielleicht zwischen zwei Systemadministratoren), können Sie mit dieser Option im Aktivierungsfluss für jeden der beiden Administratoren eine Azure-Identität definieren.

Weitere Informationen zum Automanage-Konto und zum Erstellen eines solchen Kontos finden Sie im [Dokument „Automanage-Konto“](./automanage-account.md).

## <a name="status-of-vms"></a>Status von VMs

Navigieren Sie im Azure-Portal zur Seite **Automatische Verwaltung – Bewährte Methoden für Azure-VMs**, auf der alle automatisch verwalteten virtuellen Computer aufgeführt sind. Hier wird der Gesamtstatus der einzelnen virtuellen Computer angezeigt.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Liste der konfigurierten virtuellen Computer":::

Für jede aufgelistete VM werden die folgenden Details angezeigt: Name, Umgebung, Konfigurationsvoreinstellung, Status, Betriebssystem, Konto, Abonnement und Ressourcengruppe.

Die Spalte **Status** kann die folgenden Zustände anzeigen:
- *In Bearbeitung* – Die VM wurde gerade aktiviert und wird konfiguriert
- *Konfiguriert* – Die VM ist konfiguriert und es wurde kein Datendrift erkannt
- *Fehler* – Die VM weist einen Datendrift auf und wir konnten das Problem nicht beheben
- *Ausstehend*: Der virtuelle Computer wird zurzeit nicht ausgeführt, und bei der nächsten Ausführung des virtuellen Computers wird Automanage versuchen, die VM zu integrieren oder zu korrigieren

Wenn der **Status** *Fehler* angezeigt wird, können Sie die Bereitstellung über die Ressourcengruppe beheben, in der sich Ihre VM befindet. Wechseln Sie zu **Ressourcengruppen**, wählen Sie Ihre Ressourcengruppe aus, klicken Sie auf **Bereitstellungen**, und beachten Sie dort den Status *Fehler* zusammen mit Fehlerdetails.


## <a name="disabling-automanage-for-vms"></a>Deaktivieren der automatischen Verwaltung für VMs

Möglicherweise entscheiden Sie sich eines Tages dafür, die automatische Verwaltung für bestimmte VMs zu deaktivieren. Auf Ihrem Computer wird z. B. eine hochgradig vertrauliche, sichere Workload ausgeführt, und Sie müssen sie noch weiter sperren, als es Azure von sich aus vorgenommen hätte, sodass Sie den Computer außerhalb der bewährten Methoden von Azure konfigurieren müssen.

Navigieren Sie dazu im Azure-Portal zur Seite **Automatische Verwaltung – Bewährte Methoden für Azure-VMs**, auf der alle automatisch verwalteten virtuellen Computer aufgeführt sind. Aktivieren Sie das Kontrollkästchen neben dem virtuellen Computer, den Sie über die automatische Verwaltung deaktivieren möchten, und klicken Sie dann auf die Schaltfläche zum **Deaktivieren der automatischen Verwaltung**.

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

In diesem Artikel haben Sie erfahren, dass die automatische Verwaltung für virtuelle Computer ein Mittel darstellt, mit dem Sie die Kenntnis, das Onboarding und die Konfiguration bewährter Methoden von Azure-Diensten beseitigen können. Wenn ein Computer, für den Sie bei Automanage für virtuelle Computer das Onboarding durchgeführt haben, von der Umgebungseinrichtung abweicht, sorgen wir automatisch wieder für seine Compliance.

Versuchen Sie, die automatische Verwaltung für virtuelle Computer im Azure-Portal zu aktivieren.

> [!div class="nextstepaction"]
> [Aktivieren der automatischen Verwaltung für virtuelle Computer im Azure-Portal](quick-create-virtual-machines-portal.md)
