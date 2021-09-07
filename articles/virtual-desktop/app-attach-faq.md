---
title: 'Häufig gestellte Fragen zu Azure Virtual Desktop: MSIX-Feature zum Anfügen von Apps – Azure'
description: Häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps für Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 487d1516e82f856f6c42188248b698b432117906
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112201458"
---
# <a name="msix-app-attach-faq"></a>Häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps für Azure Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Worin besteht der Unterschied zwischen MSIX und dem MSIX-Feature zum Anfügen von Apps?

MSIX ist ein Verpackungsformat für Apps, während das MSIX-Feature zum Anfügen von Apps MSIX-Pakete an Ihre Bereitstellung übergibt.

## <a name="does-msix-app-attach-use-fslogix"></a>Verwendet das MSIX-Feature zum Anfügen von Apps FSLogix?

Das MSIX-Feature zum Anfügen von Apps verwendet FSLogix nicht. Das MSIX-Feature zum Anfügen von Apps und FSLogix sind jedoch so konzipiert, dass sie zusammen verwendet werden können, um eine einheitliche Benutzererfahrung zu ermöglichen.

## <a name="can-i-use-the-msix-app-attach-outside-of-azure-virtual-desktop"></a>Kann ich das MSIX-Feature zum Anfügen von Apps außerhalb von Azure Virtual Desktop verwenden?

Die APIs, die das MSIX-Feature zum Anfügen von Apps benötigt, sind für Windows 10 Enterprise verfügbar. Diese APIs können außerhalb von Azure Virtual Desktop verwendet werden. Es gibt jedoch keine Verwaltungsebene für das MSIX-Feature zum Anfügen von Apps außerhalb von Azure Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Wie rufe ich ein MSIX-Paket ab?

Sie erhalten ein MSIX-Paket bei Ihrem Softwarehersteller. Sie können auch Nicht-MSIX-Pakete in das MSIX-Format konvertieren. Weitere Informationen finden Sie unter [Verschieben vorhandener Installationsprogramme in MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Welche Betriebssysteme unterstützen das MSIX-Feature zum Anfügen von Apps?

Windows 10 Enterprise und Windows 10 Enterprise Multisession, mindestens Version 2004.

## <a name="is-msix-app-attach-currently-generally-available"></a>Ist das MSIX-Feature zum Anfügen von Apps zurzeit allgemein verfügbar?

Das MSIX-Feature zum Anfügen von Apps ist ein Teil von Windows 10 Enterprise und Windows 10 Enterprise Multisession, mindestens Version 2004. Beide Betriebssysteme sind zurzeit allgemein verfügbar. 

## <a name="can-i-use-msix-app-attach-outside-of-azure-virtual-desktop"></a>Kann ich das MSIX-Feature zum Anfügen von Apps außerhalb von Azure Virtual Desktop verwenden?

Die APIs für MSIX und das MSIX-Feature zum Anfügen von Apps sind Teil von Windows 10 Enterprise und Windows 10 Enterprise Multisession, mindestens Version 2004. Wir bieten derzeit außerhalb von Azure Virtual Desktop keine Verwaltungssoftware für das MSIX-Feature zum Anfügen von Apps.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Kann ich zwei Versionen derselben Anwendung gleichzeitig ausführen?

Damit zwei Versionen derselben MSIX-Anwendungen gleichzeitig ausgeführt werden können, muss in der Datei „appxmanifest.xml“ für jede App eine andere MSIX-Paketfamilie definiert werden.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Sollte ich die automatische Aktualisierung deaktivieren, wenn ich das MSIX-Feature zum Anfügen von Apps verwende?

Ja. Das MSIX-Feature zum Anfügen von Apps unterstützt keine automatische Aktualisierung für MSIX-Anwendungen.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Wie funktionieren Berechtigungen in Verbindung mit dem MSIX-Feature zum Anfügen von Apps?

Alle virtuellen Computer (VMs) in einem Hostpool, der das MSIX-Feature zum Anfügen von Apps verwendet, müssen über Leseberechtigungen für die Dateifreigabe verfügen, in der die MSIX-Images gespeichert sind. Wenn er auch Azure Files verwendet, müssen ihnen sowohl rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) als auch NTFS-Berechtigungen (New Technology File System) gewährt werden.

## <a name="how-many-users-can-use-an-msix-image-handle"></a>Wie viele Benutzer können ein MSIX-Imagehandle verwenden?

Das MSIX-Feature zum Anfügen von Apps bindet MSIX-Images auf Computerbasis und nicht auf Benutzerbasis ein. Die Anzahl der Benutzer, die ein MSIX-Imagehandle verwenden können, basiert auf der Größe des Dateisystems des Computers und dem Durchsatz des Netzwerks. Außerdem gilt für Azure Files ein Grenzwert von 2.000 geöffneten Handles pro Datei. 

## <a name="can-i-use-azure-active-directory-domain-services-azure-ad-ds-with-msix-app-attach"></a>Kann ich Azure Active Directory Domain Services (Azure AD DS) mit dem MSIX-Feature zum Anfügen von Apps verwenden?

Das MSIX-Feature zum Anfügen von Apps bietet derzeit keine Azure AD DS-Unterstützung. Da Azure AD DS-Computerobjekte nicht mit Azure Active Directory (Azure AD) synchronisiert werden, kann der Administrator nicht die erforderlichen Berechtigungen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Azure Files angeben.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Kann ich das MSIX-Feature zum Anfügen von Apps für HTTP oder HTTPS verwenden?

Die Verwendung des MSIX-Features zum Anfügen von Apps über HTTP oder HTTPS wird derzeit nicht unterstützt.

## <a name="can-i-restage-the-same-msix-application"></a>Kann ich dieselbe MSIX-Anwendung erneut bereitstellen?

Ja. Sie können bereits bereitgestellte Anwendungen erneut bereitstellen. Dies sollte keine Fehler verursachen.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Unterstützt das MSIX-Feature zum Anfügen von Apps selbstsignierte Zertifikate?

Ja. Sie müssen das selbst signierte Zertifikat auf allen Sitzungshost-VMs installieren, auf denen das MSIX-Feature zum Anfügen von Apps verwendet wird, um die selbst signierte Anwendung zu hosten. Informationen zum Erstellen eines selbstsignierten Zertifikats finden Sie unter [Erstellen eines Zertifikats für die Paketsignierung](/windows/msix/package/create-certificate-package-signing).

## <a name="what-applications-can-i-repackage-to-msix"></a>Welche Anwendungen kann ich für MSIX neu packen?

Jede Anwendung verwendet verschiedene Features des Betriebssystems, der Programmiersprachen und der Frameworks. Um Ihre Anwendung neu zu packen, befolgen Sie die Anweisungen unter [Umstellen Ihrer vorhandenen Installer auf MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix). Eine Liste der Elemente, die Sie zum erneuten Packen einer Anwendung benötigen, finden Sie unter [Vorbereiten des Verpackens einer Desktopanwendung](/windows/msix/desktop/desktop-to-uwp-prepare). 

Bestimmte Anwendungen unterstützen keine Anwendungsschichten, d. h., sie können nicht in einer MSIX-Datei neu gepackt werden. Dies gilt für die folgenden Anwendungen:

- Treiber 
- Active-X oder Silverlight
- VPN-Clients
- Antivirenprogramme

## <a name="how-many-misx-applications-can-i-add-to-each-session-host"></a>Wie viele MISX-Anwendungen kann ich pro Sitzungshost hinzufügen?

Für jeden Sitzungshost gelten je nach CPU, Arbeitsspeicher und Betriebssystem unterschiedliche Grenzwerte. Das Überschreiten dieser Grenzwerte kann sich negativ auf die Anwendungsleistung und die allgemeine Benutzerfreundlichkeit auswirken. Für das MSIX-Feature zum Anfügen von Apps selbst gilt aber kein Grenzwert für die Anzahl von Anwendungen, die verwendet werden können.

## <a name="how-many-vhd-or-vhdx-files-can-i-mount-on-a-host-pool"></a>Wie viele VHD- oder VHDX-Dateien kann ich in einem Hostpool einbinden?

Das MSIX-Feature zum Anfügen von Apps weist keine Beschränkung für die Anzahl von Dateien auf, die Sie einbinden können. Für den Hostpool können aber die folgenden Einschränkungen gelten:

- Fähigkeit des Betriebssystems, eingebundene Volumes zu verarbeiten
- Maximale Anzahl geöffneter Dateien, die Ihre Speicherlösung oder Ihr Dateisystem verwalten kann
- Auslastung des Sitzungshostspeichers und der CPU des Hostpools

Anders ausgedrückt: Die Grenzwerte des Hostpools sind die gleichen wie bei der lokalen Installation und Ausführung der Apps.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über das MSIX-Feature zum Anfügen von Apps finden Sie in unserer [Übersicht](what-is-app-attach.md) und im [Glossar](app-attach-glossary.md). Oder legen Sie direkt mit dem [Einrichten des MSIX-Features zum Anfügen von Apps](app-attach.md) los.
