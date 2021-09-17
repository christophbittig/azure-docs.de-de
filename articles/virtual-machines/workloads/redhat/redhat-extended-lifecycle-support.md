---
title: Erweiterte Lebenszyklusunterstützung für Red Hat Enterprise Linux
description: Informationen zum Hinzufügen des Add-Ons zur erweiterten Lebenszyklusunterstützung für Red Hat Enterprise
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 82aa54f1b9aea331c7c08128487fbc9711cac2df
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696838"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Erweiterte Lebenszyklusunterstützung für Red Hat Enterprise Linux (RHEL)

**Gilt für:** :heavy_check_mark: Linux-VMs 

Dieser Artikel bietet Informationen zur erweiterten Lebenszyklusunterstützung für die Red Hat Enterprise-Images:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 Lifecycle
Am 30. November 2020 ist die Wartungsphase für Red Hat Enterprise Linux 6 beendet. Danach folgt eine Phase des erweiterten Lebenszyklus. Da die Phasen der vollständigen Unterstützung und Wartung für Red Hat Enterprise Linux 6 enden, wird dringend empfohlen, ein Upgrade auf Red Hat Enterprise Linux 7 oder 8 durchzuführen. Wenn Kunden weiterhin Red Hat Enterprise Linux 6 verwenden müssen, empfiehlt es sich, das Add-On zur erweiterten Lebenszyklusunterstützung (Extended Life Cycle Support, ELS) für Red Hat Enterprise Linux hinzuzufügen.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Schritte zum Hinzufügen von erweiterter Lebenszyklusunterstützung für VMs mit nutzungsbasierter Bezahlung im Marketplace
1. Füllen Sie [dieses ELS-Formular](https://aka.ms/els-form) mit Ihren Kontakt- und Abonnementinformationen zu den VMs aus, für die Sie die ELS-Unterstützung hinzufügen möchten. Die Details zu den Zusatzkosten sind ebenfalls im Formular enthalten.
1. Das Azure Red Hat Enterprise Linux-Team wird sich innerhalb von 1–2 Werktagen mit Ihnen in Verbindung setzen und Ihnen die Liste der VMs senden, auf denen das Add-On zur ELS-Unterstützung hinzugefügt wird. Überprüfen Sie die Liste, und stimmen Sie den Add-On-Preisen zu.
1. Das Azure Red Hat Enterprise Linux-Team wird Sie über die Schritte zum Hinzufügen des ELS-Clientpakets auf den virtuellen Computern informieren. Befolgen Sie die Schritte in der E-Mail, um weiterhin Softwarewartungsaktivitäten (Fehler- und Sicherheitskorrekturen) und Unterstützung für Red Hat Enterprise Linux 6 zu erhalten.

> [!Note]
> Geben Sie die Schritte für die Verwendung des RHEL ELS-Add-Ons nicht an Personen außerhalb Ihrer Organisation weiter. Wenden Sie sich für Support oder bei weiteren Fragen an AzureRedHatELS@microsoft.com.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Ich arbeite mit Red Hat Enterprise Linux 6 und kann derzeit nicht zu einer höheren Version migrieren. Welche Optionen stehen mir zur Verfügung?
* Führen Sie Red Hat Enterprise Linux 6 weiter aus, und erwerben Sie die Add-On-Repositorys zur erweiterten Lebenszyklusunterstützung (Extended Life Cycle Support, ELS). So erhalten Sie weiterhin eingeschränkten Support für Softwarewartung und technische Probleme (mehr dazu erfahren Sie in den folgenden Abschnitten zu Upgrades und Kosten).
* Migrieren Sie zu Red Hat Enterprise Linux 7 oder 8, so schnell es Ihnen möglich ist.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Welche zusätzlichen Gebühren fallen für das Add-On zur erweiterten Lebenszyklusunterstützung (Extended Life Cycle Support, ELS) für Red Hat Enterprise Linux an?
Die Kosten im Zusammenhang mit der erweiterten Lebenszyklusunterstützung finden Sie im [ELS-Formular](https://aka.ms/els-form).

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Ich habe eine VM mithilfe eines benutzerdefinierten Images bereitgestellt. Wie kann ich dieser VM erweiterte Lebenszyklusunterstützung hinzufügen?
Sie müssen sich direkt an Red Hat wenden, um Unterstützung direkt von Red Hat zu erhalten.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Ich habe eine VM mithilfe eines benutzerdefinierten Images bereitgestellt. Kann ich diese VM in eine VM mit nutzungsbasierter Zahlung umwandeln?
Nein, das ist nicht möglich. Die Umwandlung wird in Azure derzeit nicht unterstützt.


## <a name="next-steps"></a>Nächste Schritte

* Die vollständige Liste der RHEL-Images in Azure finden Sie unter [Verfügbare RHEL-Images (Red Hat Enterprise Linux) in Azure](./redhat-imagelist.md).
* Weitere Informationen zur Azure Red Hat-Updateinfrastruktur finden Sie unter [Red Hat-Updateinfrastruktur für On-Demand-RHEL-VMs in Azure](./redhat-rhui.md).
* Weitere Informationen zum RHEL BYOS-Angebot finden Sie unter [Bring-Your-Own-Subscription-Gold-Images für Red Hat Enterprise Linux in Azure](./byos.md).
* Weitere Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie unter [Red Hat Enterprise Linux Life Cycle (Red Hat Enterprise Linux-Lebenszyklus)](https://access.redhat.com/support/policy/updates/errata).