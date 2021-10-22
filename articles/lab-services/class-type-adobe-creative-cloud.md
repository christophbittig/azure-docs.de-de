---
title: Einrichten eines Labs mit Adobe Creative Cloud unter Verwendung von Azure Lab Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Lab für Kurse im Bereich „Digitale Kunst und Medien“ einrichten, bei denen Adobe Creative Cloud zum Einsatz kommt.
author: nicolela
ms.topic: how-to
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 3802687b76929bda96e34a9b1cb402096bee40c5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177154"
---
# <a name="set-up-a-lab-for-adobe-creative-cloud"></a>Einrichten eines Labs für Adobe Creative Cloud
Bei [Adobe Creative Cloud](https://www.adobe.com/creativecloud.html) handelt es sich um eine Sammlung von Desktopanwendungen und Webdiensten, die für Fotografie, Design, Videos, Web, Benutzererfahrungen und Ähnliches verwendet werden.  Universitäten und weiterführende Schulen nutzen Creative Cloud in Kursen für digitale Kunst und Medien.  Einige der Medienprozesse von Creative Cloud erfordern möglicherweise mehr Rechen- und Visualisierungsleistung (GPU) als bei einem typischen Tablet oder Laptop bzw. bei einer typischen Arbeitsstation zur Verfügung steht.  Mit Azure Lab Services können Sie flexibel zwischen verschiedenen VM-Größen (und auch GPU-Größen) wählen.

In diesem Artikel erfahren Sie, wie Sie einen Kurs mit Creative Cloud einrichten.

## <a name="licensing"></a>Lizenzierung
Wenn Sie Creative Cloud auf einem virtuellen Lab-Computer verwenden möchten, müssen Sie die [Lizenzierung mit benannten Benutzern](https://helpx.adobe.com/enterprise/kb/technical-support-boundaries-virtualized-server-based.html#main_Licensing_considerations) verwenden. Die Bereitstellung auf einem virtuellen Computer wird nur bei dieser Lizenzierungsart unterstützt.  Jeder virtuelle Lab-Computer verfügt über Internetzugriff, sodass Ihre Kursteilnehmer Creative Cloud-Apps aktivieren können, indem sie sich bei der Software anmelden.  Nach der Anmeldung wird das Authentifizierungstoken des Kursteilnehmers im Benutzerprofil zwischengespeichert, damit er sich auf seinem virtuellen Computer nicht erneut anmelden muss.  Weitere Informationen finden Sie in [diesem Artikel zur Lizenzierung von Adobe](https://helpx.adobe.com/enterprise/using/licensing.html).

## <a name="lab-configuration"></a>Labkonfiguration
Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im [Tutorial zum Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen für das Lab-Konto, die in der nachfolgenden Tabelle beschrieben werden. Weitere Informationen zum Aktivieren von Marketplace-Images finden Sie im Artikel zum [Angeben von für Lab-Ersteller verfügbaren Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| ------------------- | ------------ |
|Marketplace-Image| Aktivieren Sie das Windows 10-Image für die Verwendung in Ihrem Lab-Konto.|

### <a name="lab-settings"></a>Lab-Einstellungen

Die erforderliche VM-Größe für Ihr Lab hängt davon ab, welche Arten von Projekten Ihre Kursteilnehmer erstellen.  Die meisten [Creative Cloud-Apps](https://helpx.adobe.com/creative-cloud/system-requirements.html) unterstützen GPU-basierte Beschleunigung und erfordern eine GPU, damit Features ordnungsgemäß funktionieren.  Es empfiehlt sich, die Projekte zu testen, die von Ihren Kursteilnehmern erstellt werden, um eine angemessene Leistung sicherzustellen und die Wahl der passenden VM-Größe zu gewährleisten.  Die folgende Tabelle gibt Aufschluss über die empfohlene [VM-Größe](./administrator-guide.md#vm-sizing) für die Verwendung mit Creative Cloud:  

| Lab-Einstellungen | Wert/Anweisungen |
| ------------ | ------------------ |
|VM-Größe| **Kleine GPU (Visualisierung)** .  Diese VM eignet sich am besten für Remotevisualisierung, Streaming, Gaming und Codierung mit Frameworks wie OpenGL und DirectX.|  
|VM-Image| Windows 10 |

> [!NOTE]
> Die VM-Größe **Kleine GPU (Visualisierung)** ist für hohe Grafikleistung konfiguriert und erfüllt die [Systemanforderungen von Adobe für die einzelnen Anwendungen](https://helpx.adobe.com/creative-cloud/system-requirements.html).  Achten Sie darauf, dass Sie anstelle von „Kleine GPU (Visualisierung)“ nicht versehentlich „Kleine GPU (Compute)“ auswählen.  Weitere Informationen zu dieser VM-Größe finden Sie im Artikel zum [Einrichten eines Labs mit GPUs](./how-to-setup-lab-gpu.md).

## <a name="template-virtual-machine-configuration"></a>Vorlage für die Konfiguration des virtuellen Computers

### <a name="creative-cloud-deployment-package"></a>Creative Cloud-Bereitstellungspaket
Für die Installation von Creative Cloud muss ein Bereitstellungspaket verwendet werden. Das Bereitstellungspaket wird in der Regel von Ihrer IT-Abteilung über die Administratorkonsole von Adobe erstellt.  Bei der Erstellung des Bereitstellungspakets kann die IT-Abteilung auch Self-Service aktivieren.  Self-Service kann auf mehrere Arten für das Bereitstellungspaket aktiviert werden:
-    Erstellen eines Self-Service-Pakets
-    Erstellen eines verwalteten Pakets mit erhöhten Self-Service-Rechten

Wenn Self-Service aktiviert ist, installieren Sie nicht die gesamte Sammlung von Creative Cloud-Apps.  Stattdessen können Kursteilnehmer Apps selbst über die Creative Cloud-Desktop-App installieren.  Dieser Ansatz hat unter anderem folgende Vorteile:
- Die vollständige Creative Cloud-Installation umfasst etwa 25 GB.  Wenn Kursteilnehmer nur die Apps installieren, die sie benötigen, trägt dies zur Optimierung des Speicherplatzes bei. Die Datenträgergröße virtueller Lab-Computer beträgt 128 GB.
- Sie können eine Teilmenge der Apps auf dem virtuellen Vorlagencomputer installieren, bevor Sie ihn veröffentlichen.  Dadurch sind auf den virtuellen Computern der Kursteilnehmer einige Apps standardmäßig installiert, und die Kursteilnehmer können bei Bedarf weitere Apps selbst hinzufügen.
- Sie können eine erneute Veröffentlichung des virtuellen Vorlagencomputers vermeiden, da Kursteilnehmer während der Lebensdauer des Labs jederzeit zusätzliche Apps auf ihrem virtuellen Computer installieren können.  Andernfalls muss entweder die IT-Abteilung oder der Kursleiter zusätzliche Apps auf dem virtuellen Vorlagencomputer installieren und ihn erneut veröffentlichen.  Im Zuge der erneuten Veröffentlichung werden die virtuellen Computer der Kursteilnehmer zurückgesetzt, und alles, was nicht extern gespeichert wurde, geht verloren.

Bei Verwendung eines verwalteten Bereitstellungspakets ohne Self-Service haben Kursteilnehmer nicht die Möglichkeit, ihre eigenen Apps zu installieren.  In diesem Fall muss die IT-Abteilung die zu installierenden Creative Cloud-Apps angeben.

Weitere Informationen finden Sie in den [Paketerstellungsschritten von Adobe](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/create-nul-packages.ug.html).

### <a name="install-creative-cloud"></a>Installieren von Creative Cloud
Führen Sie nach Erstellung des Vorlagencomputers die folgenden Schritte aus, um den virtuellen Vorlagencomputer (virtual machine, VM) Ihres Labs mit Creative Cloud einzurichten.
1. Starten Sie den virtuellen Vorlagencomputer, und stellen Sie eine RDP-Verbindung her.
1. Laden Sie zum Installieren von Creative Cloud das Bereitstellungspaket herunter. Dieses erhalten Sie entweder von der IT-Abteilung oder direkt über die [Administratorkonsole von Adobe](https://adminconsole.adobe.com/).
1. Führen Sie die Bereitstellungspaketdatei aus.  Dadurch wird/werden je nach Self-Service-Aktivierungsstatus die Creative Cloud Desktop-App und/oder die angegebenen Creative Cloud-Apps installiert.
Weitere Informationen finden Sie in den [Bereitstellungsschritten von Adobe](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/deploy-packages.ug.html).
1. Nachdem der virtuelle Vorlagencomputer eingerichtet wurde, können Sie das [Image des virtuellen Vorlagencomputers veröffentlichen](how-to-create-manage-template.md). Auf der Grundlage dieses Images werden alle virtuellen Computer der Kursteilnehmer im Lab erstellt.

### <a name="storage"></a>Storage
Wie bereits erwähnt, haben virtuelle Azure Lab-Computer eine Datenträgergröße von 128 GB.  Falls Ihre Kursteilnehmer zusätzlichen Speicherplatz zum Speichern großer Medienressourcen benötigen oder auf gemeinsam genutzte Medienressourcen zugreifen müssen, empfiehlt sich ggf. die Verwendung eines externen Dateispeichers.  Weitere Informationen finden Sie in den folgenden Artikeln:
-    [Verwenden von externem Dateispeicher in Lab Services](how-to-attach-external-storage.md)
-    [Installieren und Konfigurieren von OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive)

### <a name="save-template-vm-image"></a>Speichern des Images des virtuellen Vorlagencomputers
Es empfiehlt sich gegebenenfalls, den virtuellen Vorlagencomputer für die zukünftige Verwendung zu speichern.  Informationen zum Speichern des virtuellen Vorlagencomputers finden Sie unter [Speichern eines Images im Katalog mit freigegebenen Images](./how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery).
- Wenn Self-Service *aktiviert* ist, ist die Creative Cloud-Desktop-App im Image des virtuellen Vorlagencomputers installiert.  Kursleiter können dieses Image für die Lab-Erstellung wiederverwenden und die zu installierenden Creative Cloud-Apps auswählen.  Dadurch wird die IT-Abteilung entlastet, da Kursleiter Labs unabhängig einrichten können und die vollständige Kontrolle über die Installation der Creative Cloud-Apps haben, die für ihre jeweiligen Kurse erforderlich sind.
- Wenn Self-Service *deaktiviert* ist, sind die angegebenen Creative Cloud-Apps bereits im Image des virtuellen Vorlagencomputers installiert.  Kursleiter können dieses Image zwar wiederverwenden, um Labs zu erstellen, sie können aber keine zusätzlichen Creative Cloud-Apps installieren.

## <a name="cost"></a>Kosten

Dieser Abschnitt enthält ein Beispiel für eine mögliche Kostenschätzung für diesen Kurs.  Angenommen, Sie haben einen Kurs mit 25 Kursteilnehmern und einer geplanten Kurszeit von 20 Stunden.  Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 Stunden für Hausaufgaben und Aufgaben außerhalb der regulären Kurszeiten. Als VM-Größe wurde **Kleine GPU (Visualisierung)** ausgewählt, was 160 Lab-Einheiten entspricht.

25 Kursteilnehmer \* (20 geplante Stunden + 10 Kontingentstunden) \* 160 Lab-Einheiten * 0,01 USD pro Stunde = 1.200,00 USD

>[!IMPORTANT]
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Vorlage](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)