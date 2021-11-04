---
title: Häufig gestellte Fragen zu Azure Automation
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a245c858c15a8a33b6347c86124a7b45f0c4290
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450892"
---
# <a name="azure-automation-frequently-asked-questions"></a>Häufig gestellte Fragen zu Azure Automation

Dieser Microsoft-Artikel enthält eine Liste häufig gestellter Fragen zu Azure Automation. Wenn Sie andere Fragen zu dessen Funktionen haben, besuchen Sie das Diskussionsforum, und stellen Sie Ihre Fragen. Wenn eine Frage häufiger gestellt wird, fügen wir sie diesem Artikel hinzu, damit sie schnell und einfach gefunden werden kann.

## <a name="can-update-management-prevent-unexpected-os-level-upgrades"></a>Kann die Updateverwaltung unerwartete Upgrades auf Betriebssystemebene verhindern?

Ja. Weitere Informationen finden Sie unter [Ausschließen von Updates](./update-management/manage-updates-for-vm.md#exclude-updates).

## <a name="why-arent-criticalsecurity-updates-applied-to-a-linux-machine-with-update-management"></a>Warum werden keine kritischen bzw. Sicherheitsupdates auf Linux-Computern mit Updateverwaltung angewandt?

Beim Bereitstellen von Updates für einen Linux-Computer können Sie Updateklassifizierungen auswählen. Durch diese Option werden die Updates gefiltert, die die definierten Kriterien erfüllen. Weitere Informationen finden Sie unter [Linux-Updateklassifizierungen](./update-management/manage-updates-for-vm.md#linux-update-classifications).

## <a name="can-update-management-deploy-updates-across-azure-tenants"></a>Kann die Updateverwaltung Updates über Azure-Mandanten hinweg bereitstellen?

Ja. Anweisungen finden Sie unter [Bereitstellen von Updates über Azure-Mandanten hinweg](./update-management/deploy-updates.md#deploy-updates-across-azure-tenants).

## <a name="which-python-3-version-is-supported-in-azure-automation"></a>Welche Python 3-Version wird in Azure Automation unterstützt?

Für Cloudaufträge wird Python 3.8 unterstützt. Skripts und Pakete aus einer beliebigen 3.x-Version funktionieren möglicherweise, wenn der Code mit mehreren Versionen kompatibel ist.

Für Hybridaufträge auf Windows Hybrid Runbook Workers können Sie jede beliebige 3.x-Version installieren, die Sie verwenden möchten. Für Hybridaufträge auf Linux Hybrid Runbook Workers ist die auf dem Computer installierte Version von Python 3 zum Ausführen von DSC OMSConfig und der Linux Hybrid Worker erforderlich. Es wird empfohlen, Version 3.6 zu installieren. Andere Versionen sollten jedoch ebenfalls funktionieren, wenn zwischen den Versionen von Python 3 keine Breaking Changes bei Methodensignaturen oder Verträgen aufgetreten sind.

## <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Können Python 2- und Python 3-Runbooks im selben Automation-Konto ausgeführt werden?

Ja, es gibt keine Einschränkung für die Verwendung von Python 2- und Python 3-Runbooks im selben Automation-Konto.  

## <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Wie sieht der Plan für die Migration vorhandener Python 2-Runbooks und -Pakete zu Python 3 aus?

Azure Automation plant nicht, Python 2-Runbooks und -Pakete zu Python 3 zu migrieren. Sie müssen diese Migration selbst durchführen. Vorhandene und neue Python 2-Runbooks und -Pakete werden weiterhin funktionieren.

## <a name="what-packages-are-supported-by-default-in-python-3-environment"></a>Welche Pakete werden standardmäßig in der Python 3-Umgebung unterstützt?

Azure-Paket 4.0.0. Weitere Informationen finden Sie unter [Verwalten von Python 3-Paketen](python-3-packages.md).

## <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>Was geschieht, wenn ein Python 3-Runbook ausgeführt wird, das auf ein Python 2-Paket verweist oder umgekehrt?

Python 2 und Python 3 verfügen über unterschiedliche Ausführungsumgebungen. Während ein Python 2-Runbook ausgeführt wird, können nur Python 2-Pakete importiert werden. Für Python 3 gilt dasselbe Prinzip.

## <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Wie kann man zwischen Python 2- und Python 3-Runbooks und -Paketen unterscheiden?

Python 3 ist eine neue Runbookdefinition, die zwischen Python 2- und Python 3-Runbooks unterscheidet. Ebenso wird ein weiterer Pakettyp für Python 3-Pakete eingeführt.

## <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>Woher weiß ein Hybrid Runbook Worker, welche Version von Python ausgeführt werden soll, wenn Sowohl Python2 als auch Python3 installiert sind?

Ein Windows Runbook Worker sucht beim Ausführen eines Python 2-Runbooks zuerst nach der Umgebungsvariable `PYTHON_2_PATH` und überprüft, ob sie auf eine gültige ausführbare Datei verweist. Wenn der Installationsordner beispielsweise `C:\Python2` lautet, wird überprüft, ob `C:\Python2\python.exe` ein gültiger Pfad ist. Wenn er nicht gefunden wird, sucht der Worker nach der Umgebungsvariable `PATH`, um eine ähnliche Überprüfung durchzuführen.

Für Python 3 sucht er zuerst nach der Umgebungsvariable `PYTHON_3_PATH` und greift dann auf die Umgebungsvariable `PATH` zurück.

Weitere Informationen finden Sie unter [Mehrere Python-Versionen](automation-runbook-types.md#multiple-python-versions).

## <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>Wie findet ein Hybrid Runbook Worker den Python-Interpreter?

Die Suche nach dem Python-Modul wird durch Umgebungsvariablen gesteuert, wie weiter oben erläutert.

## <a name="is-python-3-supported-in-source-control"></a>Wird Python 3 in der Quellcodeverwaltung unterstützt?

Nein. Die Quellcodeverwaltung wird für Python 3 derzeit nicht unterstützt. Python-Runbooks werden standardmäßig als Python 2-Runbooks synchronisiert.

## <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Wie kann ein Runbookautor wissen, welche Python-Pakete in einer Azure-Sandbox verfügbar sind?

Weitere Informationen finden Sie unter [Identifizieren verfügbarer Pakete in der Sandbox](python-3-packages.md#identify-available-packages-in-sandbox).

## <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>Wie kann ein Runbookautor festlegen, welche Version eines Paketmoduls verwendet werden soll, wenn mehrere Module vorhanden sind?

Weitere Informationen finden Sie unter [Verwalten von Python 3-Paketen](python-3-packages.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihre Frage hier nicht beantwortet wurde, finden Sie in den folgenden Quellen weitere Fragen und Antworten.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Feedbackforum](https://feedback.azure.com/d365community/forum/721a322e-bd25-ec11-b6e6-000d3a4f0f1c)
