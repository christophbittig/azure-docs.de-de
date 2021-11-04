---
title: Was ist Azure Virtual Desktop? - Azure
description: Enthält eine Übersicht über Azure Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c0e61760b130631c4f688b06f2cebdb163ea3910
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452146"
---
# <a name="what-is-azure-virtual-desktop"></a>Was ist Azure Virtual Desktop?

Bei Azure Virtual Desktop handelt es sich um einen in der Cloud ausgeführten Dienst für die Desktop- und App-Virtualisierung.

Azure Virtual Desktop in Azure ermöglicht Folgendes:

* Einrichten einer Windows 10-Bereitstellung mit mehreren Sitzungen, die eine vollständige Windows 10-Umgebung mit Skalierbarkeit bietet
* Virtualisieren von Microsoft 365 Apps for Enterprise und Optimieren der Ausführung in virtuellen Szenarien mit mehreren Benutzern
* Bereitstellen virtueller Windows 7-Desktops mit kostenlosen erweiterten Sicherheitsupdates
* Verwenden bereits vorhandener Remotedesktopdienste (Remote Desktop Services, RDS) und Windows Server-Desktops/-Apps auf einem beliebigen Computer
* Virtualisieren von Desktops und Apps
* Verwalten von Windows 10-, Windows Server- und Windows 7-Desktops und -Apps über eine einheitliche Verwaltungsoberfläche

## <a name="introductory-video"></a>Einführungsvideo

In diesem Video lernen Sie Azure Virtual Desktop kennen und erfahren, warum dieser Dienst so einzigartig ist und was es Neues für den Dienst gibt:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Weitere Videos zu Azure Virtual Desktop finden Sie in [unserer Playlist](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Wichtige Funktionen

Mit Azure Virtual Desktop können Sie eine skalierbare und flexible Umgebung einrichten:

* Erstellen Sie in Ihrem Azure-Abonnement eine vollständige Desktopvirtualisierungsumgebung, ohne dass Gatewayserver ausgeführt werden müssen.
* Veröffentlichen Sie so viele Hostpools, wie Sie für Ihre verschiedenen Workloads benötigen.
* Verwenden Sie Ihr eigenes Image für Produktionsworkloads, oder testen Sie ein Image aus dem Azure-Katalog.
* Senken Sie Kosten mit in einem Pool zusammengefassten Ressourcen für mehrere Sitzungen. Mit der neuen exklusiven Azure Virtual Desktop-Funktion unter Windows 10 Enterprise (mehrere Sitzungen) und der Rolle „Remotedesktop-Sitzungshost“ (Remote Desktop Session Host, RDSH) unter Windows Server können Sie die Anzahl virtueller Computer sowie den Mehraufwand für das Betriebssystem erheblich verringern und Ihren Benutzern weiterhin die gleichen Ressourcen bereitstellen.
* Ermöglichen Sie individuelle Besitzer über persönliche (dauerhafte) Desktops.

Sie können virtuelle Desktops bereitstellen und verwalten:

* Verwenden Sie das Azure-Portal oder die PowerShell- und die REST-Oberfläche von Azure Virtual Desktop, um die Hostpools zu konfigurieren, App-Gruppen zu erstellen, Benutzer zuzuweisen und Ressourcen zu veröffentlichen.
* Sie können vollständige Desktops oder einzelne Remote-Apps über einen einzelnen Hostpool veröffentlichen, individuelle App-Gruppen für unterschiedliche Benutzergruppen erstellen und sogar Benutzer mehreren App-Gruppen zuweisen, um die Anzahl von Images zu verringern.
* Bei der Umgebungsverwaltung können Sie dank des integrierten delegierten Zugriffs Rollen zuweisen und Diagnoseinformationen erfassen, um verschiedene Konfigurations- und Benutzerfehler nachzuvollziehen.
* Verwenden Sie den Diagnosedienst, um Fehler zu behandeln.
* Verwalten Sie nur das Image und die virtuellen Computer, nicht die Infrastruktur. Sie müssen die Remotedesktop-Rollen nicht wie bei den Remotedesktopdiensten selbst verwalten, sondern nur die virtuellen Computer in Ihrem Azure-Abonnement.

Darüber hinaus können Sie Benutzer zuweisen und mit Ihren virtuellen Desktops verbinden:

* Zugewiesene Benutzer können einen beliebigen Azure Virtual Desktop-Client starten, um eine Verbindung mit ihren veröffentlichten Windows-Desktops und -Anwendungen herzustellen. Stellen Sie eine Verbindung über ein beliebiges Gerät her – entweder über eine native Anwendung auf Ihrem Gerät oder über den HTML5-Webclient für Azure Virtual Desktop.
* Richten Sie Benutzer auf sichere Weise über umgekehrte Verbindungen mit dem Dienst ein, um keine eingehenden Ports mehr geöffnet lassen zu müssen.

## <a name="requirements"></a>Requirements (Anforderungen)

Für die Einrichtung von Azure Virtual Desktop und die erfolgreiche Verknüpfung Ihrer Benutzer mit ihren Windows-Desktops und -Anwendungen müssen bei Ihnen einige Voraussetzungen erfüllt sein.

Die folgenden Betriebssysteme werden unterstützt. Vergewissern Sie sich daher, dass Sie über die [erforderlichen Lizenzen](https://azure.microsoft.com/pricing/details/virtual-desktop/) für die Desktops und Apps verfügen, die Sie für Ihre Benutzer bereitstellen möchten:

|OS|Erforderliche Lizenz|
|---|---|
|Windows 10 Enterprise (mehrere Sitzungen) oder Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019, 2022|RDS-Clientzugriffslizenz (Client Access License, CAL) mit Software Assurance|

In Ihrer Infrastruktur muss Folgendes vorhanden sein, um Azure Virtual Desktop verwenden zu können:

* Eine [Azure Active Directory-Instanz](../active-directory/index.yml)
* Eine mit Azure Active Directory synchronisierte Windows Server Active Directory-Instanz. Diese können Sie mit Azure AD Connect (für Hybridorganisationen) oder mit Azure AD Domain Services (für Hybrid- oder Cloudorganisationen) konfigurieren.
  * Eine mit Azure Active Directory synchronisierte Windows Server AD-Instanz. Der Benutzer stammt aus Windows Server AD, und die Azure Virtual Desktop-VM wird in die Windows Server AD-Domäne eingebunden.
  * Eine mit Azure Active Directory synchronisierte Windows Server AD-Instanz. Der Benutzer stammt aus Windows Server AD, und die Azure Virtual Desktop-VM wird in die Azure AD Domain Services-Domäne eingebunden.
  * Eine Azure AD Domain Services-Domäne. Der Benutzer stammt aus Azure Active Directory, und die Azure Virtual Desktop-VM wird in die Azure AD Domain Services-Domäne eingebunden.
* Ein Azure-Abonnement, das dem gleichen Azure AD-Mandanten übergeordnet ist und ein virtuelles Netzwerk enthält, das die Windows Server Active Directory- oder Azure AD DS-Instanz entweder enthält oder mit ihr verbunden ist

Benutzeranforderungen zum Herstellen einer Verbindung mit Azure Virtual Desktop:

* Der Benutzer muss aus derselben Active Directory Domain Services-Instanz stammen, die mit Azure AD verbunden ist. Azure Virtual Desktop unterstützt keine B2B- oder MSA-Konten.
* Der UPN, den Sie zum Abonnieren von Azure Virtual Desktop verwenden, muss in der Active Directory-Domäne vorhanden sein, in die der virtuelle Computer eingebunden ist.

Die virtuellen Azure-Computer, die Sie für Azure Virtual Desktop erstellen, müssen folgende Anforderungen erfüllen:

* Sie müssen in eine [Standard-Domäne](../active-directory-domain-services/compare-identity-solutions.md) oder in [Hybrid AD](../active-directory/devices/hybrid-azuread-join-plan.md) eingebunden sein. Virtuelle Computer mit [Einbindung in Azure AD](deploy-azure-ad-joined-vm.md) sind als Vorschauversion verfügbar.
* Auf ihnen muss eines der folgenden [unterstützten Betriebssystemimages](#supported-virtual-machine-os-images) ausgeführt werden:

>[!NOTE]
>Sollten Sie ein Azure-Abonnement benötigen, können Sie sich [für eine einmonatige kostenlose Testversion registrieren](https://azure.microsoft.com/free/). Bei Verwendung der kostenlosen Testversion von Azure müssen Sie Azure AD Domain Services verwenden, um Ihre Windows Server Active Directory-Instanz mit Azure Active Directory zu synchronisieren.

Eine Liste mit den URLs, die Sie freigeben sollten, damit Ihre Azure Virtual Desktop-Bereitstellung wie vorgesehen funktioniert, finden Sie in unserer [Liste mit den erforderlichen URLs](safe-url-list.md).

Azure Virtual Desktop enthält die Windows-Desktops und -Apps, die Sie für Benutzer bereitstellen, und zusätzlich die Verwaltungslösung. Letztere wird von Microsoft in Azure gehostet. Desktops und Apps können auf virtuellen Computern (VMs) in einer beliebigen Azure-Region bereitgestellt werden. Die Verwaltungslösung und Daten für diese virtuellen Computer befinden sich dagegen in den Vereinigten Staaten. Dies kann zu Datenübertragungen in die USA führen.

Ihr Netzwerk muss folgende Anforderungen erfüllen, um eine optimale Leistung zu erzielen:

* Die Roundtriplatenz zwischen dem Netzwerk des Clients und der Azure-Region, in der die Hostpools bereitgestellt wurden, muss unter 150 ms liegen. Zeigen Sie mithilfe des [Qualitätsschätzers](https://azure.microsoft.com/services/virtual-desktop/assessment) die Verbindungsintegrität und die empfohlene Azure-Region an.
* Netzwerkdatenverkehr wird ggf. außerhalb der Grenzen des Landes bzw. der Region übertragen, wenn virtuelle Computer, die Desktops und Apps hosten, eine Verbindung mit dem Verwaltungsdienst herstellen.
* Zur Optimierung der Netzwerkleistung empfiehlt es sich, die virtuellen Computer des Sitzungshosts in der Azure-Region zu platzieren, die dem Benutzer am nächsten ist.

Sie finden eine typische Architektureinrichtung von Azure Virtual Desktop für das Unternehmen in unserer [Architekturdokumentation](/azure/architecture/example-scenario/wvd/windows-virtual-desktop).

## <a name="supported-remote-desktop-clients"></a>Unterstützte Remotedesktopclients

Die folgenden Remotedesktopclients unterstützen Azure Virtual Desktop:

* [Windows Desktop](./user-documentation/connect-windows-7-10.md)
* [Web](./user-documentation/connect-web.md)
* [macOS](./user-documentation/connect-macos.md)
* [iOS](./user-documentation/connect-ios.md)
* [Android](./user-documentation/connect-android.md)
* Microsoft Store-Client

> [!IMPORTANT]
> Azure Virtual Desktop unterstützt nicht den RemoteApp-Client und den Client für Desktopverbindungen (RADC). Der Client für die Remotedesktopverbindung (MSTSC) wird ebenfalls nicht unterstützt.

Weitere Informationen zu URLs, die Sie für die Verwendung der Clients entsperren müssen, finden Sie in der [Liste sicherer URLs](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Unterstützte Betriebssystemimages virtueller Computer

Für Azure Virtual Desktop gilt die [Microsoft-Lebenszyklusrichtlinie](/lifecycle/), und es werden die folgenden x64-Betriebssystemimages unterstützt:

* Windows 11 Enterprise mit mehreren Sitzungen (Vorschau)
* Windows 11 Enterprise (Vorschau)
* Windows 10 Enterprise (mehrere Sitzungen)
* Windows 10 Enterprise
* Windows 7 Enterprise
* Windows Server 2022
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Azure Virtual Desktop unterstützt keine Betriebssystemimages vom Typ x86 (32 Bit), Windows 10 Enterprise N, Windows 10 LTSB, Windows 10 LTSC, Windows 10 Pro oder Windows 10 Enterprise KN. Aufgrund einer Sektorgrößenbeschränkung unterstützt Windows 7 auch keine VHD- oder VHDX-basierten Profillösungen, die in einer verwalteten Azure Storage-Instanz gehostet werden.

Die verfügbaren Automatisierungs- und Bereitstellungsoptionen hängen davon ab, welches Betriebssystem und welche Version gewählt werden; siehe dazu die folgende Tabelle:

|Betriebssystem|Azure-Imagekatalog|Manuelle VM-Bereitstellung|Integration von Azure Resource Manager-Vorlagen|Bereitstellungshost-Pools auf Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 11 Enterprise mit mehreren Sitzungen (Vorschau)|Ja|Ja|Ja|Ja|
|Windows 11 Enterprise (Vorschau)|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise mit mehreren Sitzungen, Version 1909 oder höher|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise, Version 1909 oder höher|Ja|Ja|Ja|Ja|
|Windows 7 Enterprise|Ja|Ja|Nein|Nein|
|Windows Server 2022|Ja|Ja|Nein|Nein|
|Windows Server 2019|Ja|Ja|Nein|Nein|
|Windows Server 2016|Ja|Ja|Ja|Ja|
|Windows Server 2012 R2|Ja|Ja|Nein|Nein|

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Azure Virtual Desktop (klassisch) verwenden, können Sie mit dem Tutorial [Erstellen eines Mandanten in Azure Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) beginnen.

Wenn Sie Azure Virtual Desktop mit Azure Resource Manager-Integration verwenden, müssen Sie stattdessen einen Hostpool erstellen. Im folgenden Tutorial finden Sie Informationen zu den ersten Schritten:

> [!div class="nextstepaction"]
> [Erstellen eines Hostpools mit dem Azure-Portal](create-host-pools-azure-marketplace.md)
