---
title: Bereitstellen einer Anwendung mit dem MSIX-Feature zum Anfügen von Apps für Azure Virtual Desktop – Azure
description: Hier erfahren Sie, wie Sie Apps mit dem MSIX-Feature zum Anfügen von Apps für Azure Virtual Desktop bereitstellen.
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 929f69ca4503a48e8e8456111c85043cbf2db9f2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730242"
---
# <a name="deploy-apps-with-msix-app-attach"></a>Bereitstellen von Apps mit dem MSIX-Feature zum Anfügen von Apps

Dieser Artikel bietet eine grundlegende Übersicht über die Veröffentlichung einer Anwendung in Azure Virtual Desktop mit dem MSIX-Feature zum Anfügen von Apps. In diesem Artikel erhalten Sie auch Links zu Ressourcen, die Ihnen ausführlichere Erläuterungen und Anweisungen geben können.

## <a name="what-is-msix-app-attach"></a>Was ist das MSIX-Feature zum Anfügen von Apps?

Das MSIX-Feature zum Anfügen von Apps ist eine Lösung für Anwendungsschichten, mit der Sie Anwendungen für aktive Benutzersitzungen in Azure Virtual Desktop bereitstellen können. Das MSIX-Paketsystem trennt Apps vom Betriebssystem und vereinfacht so das Erstellen von Images für VMs. Mit MSIX-Paketen haben Sie auch mehr Kontrolle darüber, auf welche Apps Benutzer auf ihren VMs zugreifen können. Sie können sogar Apps vom Masterimage abtrennen und erst später an die Benutzer übergeben.

Weitere Informationen finden Sie unter [Was ist das MSIX-Feature zum Anfügen von Apps?](../what-is-app-attach.md).

## <a name="requirements"></a>Requirements (Anforderungen)

Für die Verwendung des MSIX-Features zum Anfügen von Apps in Azure Virtual Desktop ist Folgendes erforderlich:

- Eine mit MSIX gepackte Anwendung
- Ein aus der erweiterten MSIX-Anwendung erstelltes MSIX-Image
- Eine MSIX-Freigabe, bei der es sich um den Netzwerkspeicherort handelt, an dem Sie MSIX-Images speichern
- Mindestens ein fehlerfreier und aktiver Sitzungshost im zu verwendenden Hostpool
- Wenn Ihre mit MSIX gepackte Anwendung über ein privates Zertifikat verfügt, muss dieses Zertifikat auf allen Sitzungshosts im Hostpool verfügbar sein.
- Azure Virtual Desktop-Konfiguration für das MSIX-Feature zum Anfügen von Apps (Benutzerzuweisung, Zuordnung der MSIX-Anwendung zur App-Gruppe, Hinzufügen des MSIX-Images zum Hostpool)

## <a name="create-an-msix-package-from-an-existing-installer"></a>Erstellen eines MSIX-Pakets auf der Grundlage eines vorhandenen Installers

Sie müssen Ihre Anwendung in ein MSIX-Paket integrieren, um das MSIX-Feature zum Anfügen von Apps verwenden zu können. Einige Apps liegen bereits im MSIX-Format vor, doch wenn Sie ein älteres Installationsprogramm wie MSI, ClickOnce usw. verwenden, müssen Sie die App in das MSIX-Paketformat konvertieren. Informationen zum Konvertieren Ihrer vorhandenen Apps in das MSIX-Format finden Sie in unserem [MSIX-Übersichtsartikel](/windows/msix/packaging-tool/create-an-msix-overview).

## <a name="test-the-application-fidelity-of-your-packaged-app"></a>Testen der Anwendungsgenauigkeit Ihrer gepackten App 

Nachdem Sie Ihre Anwendung als MSIX-Paket neu verpackt haben, müssen Sie sicherstellen, dass ihre Anwendungsgenauigkeit hoch ist. App-Genauigkeit ist das Verhalten und die Leistung der Anwendung vor und nach dem erneuten Packen. Ein App-Paket mit hoher App-Genauigkeit bietet vorher und nachher eine ähnliche Leistung.

Wenn Sie feststellen, dass die App-Genauigkeit nach dem erneuten Packen abnimmt, muss Ihre Organisation die App testen, um sicherzustellen, dass ihre Leistung den Benutzerstandards entspricht. Ist dies nicht der Fall, müssen Sie Ihre App aktualisieren, um das Problem zu vermeiden, oder versuchen Sie, das erneute Verpacken zu wiederholen.

## <a name="create-an-msix-image"></a>Erstellen eines MSIX-Images

Als nächstes müssen Sie ein MSIX-Image aus Ihrem App-Paket erstellen. Ein MSIX-Image entsteht, wenn Sie ein MSIX-App-Paket erweitern und die daraus resultierende App in einem VHD(X)- oder CIM-Speicher speichern. Informationen zum Erstellen eines MSIX-Images finden Sie unter [Erstellen eines MSIX-Images](../app-attach-msixmgr.md#create-an-msix-image).

## <a name="configure-an-msix-file-share"></a>Konfigurieren einer MSIX-Dateifreigabe

Als nächstes müssen Sie eine MSIX-Netzwerkfreigabe einrichten, um MSIX-Images zu speichern. Nach der Konfiguration verwenden Ihre Sitzungshosts die MSIX-Freigabe, um MSIX-Pakete an aktive Benutzersitzungen anfügen und Ihren Benutzern Apps bereitstellen zu können. Informationen zum Einrichten einer MSIX-Freigabe finden Sie unter [Einrichten einer Dateifreigabe für das MSIX-Feature zum Anfügen von Apps](../app-attach-file-share.md).

## <a name="configure-msix-app-attach-for-azure-virtual-desktop-host-pool"></a>Konfigurieren des MSIX-Features zum Anfügen von Apps für einen Azure Virtual Desktop-Hostpool

Nachdem Sie ein MSIX-Image auf die MSIX-Freigabe hochgeladen haben, müssen Sie das Azure-Portal öffnen und den Hostpool konfigurieren, den Sie zum Akzeptieren des MSIX-Features zum Anfügen von Apps verwenden möchten. Informationen zum Konfigurieren Ihres Hostpools finden Sie unter [Einrichten des MSIX-Features zum Anfügen von Apps über das Azure-Portal](../app-attach-azure-portal.md).
