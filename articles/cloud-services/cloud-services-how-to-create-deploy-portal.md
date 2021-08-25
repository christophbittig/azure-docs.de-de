---
title: Erstellen und Bereitstellen von Cloud Services (klassisch) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Schnellerfassungsmethode zum Erstellen eines Clouddiensts und „Hochladen“ verwenden, um ein Clouddienstpaket in Azure hochzuladen und bereitzustellen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0cc7e5c0e9c335c7ef1892cd0b7495011586266e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824407"
---
# <a name="how-to-create-and-deploy-an-azure-cloud-service-classic"></a>Erstellen und Bereitstellen von Azure Cloud Services (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Das Azure-Portal bietet zwei Methoden zum Erstellen und Bereitstellen eines Clouddiensts: *Schnellerfassung* und *Benutzerdefiniert erstellen*.

In diesem Thema wird erläutert, wie Sie die Schnellerfassungsmethode zum Erstellen eines neuen Clouddiensts und dann **Hochladen** verwenden, um ein Clouddienstpaket in Azure hochzuladen und bereitzustellen. Wenn Sie diese Methode verwenden, werden im Azure-Portal praktische Links zum Erfüllen aller Anforderungen zur Verfügung gestellt. Wenn Sie Ihren Cloud-Dienst bei der Erstellung auch bereitstellen möchten, können Sie beides mithilfe von Benutzerdefinierte Erstellung durchführen.

> [!NOTE]
> Wenn Sie Ihren Clouddienst aus Azure DevOps veröffentlichen möchten, verwenden Sie die Schnellerstellung. Richten Sie die Azure DevOps-Veröffentlichung dann über den Azure-Schnellstart oder das Dashboard ein. Weitere Informationen finden Sie unter [Continuous Delivery für Azure mithilfe von Azure DevOps][TFSTutorialForCloudService] oder in der Hilfe zur Seite **Schnellstart**.
>
>

## <a name="concepts"></a>Konzepte
Für die Bereitstellung einer Anwendung als Clouddienst in Azure sind drei Komponenten erforderlich:

* **Dienstdefinition:**  
  Die Clouddienst-Definitionsdatei (.csdef) definiert das Dienstmodell einschließlich der Rollenanzahl.
* **Dienstkonfiguration:**  
  Die Clouddienst-Konfigurationsdatei (.cscfg) enthält Konfigurationseinstellungen für den Clouddienst sowie einzelne Rollen, darunter die Anzahl der Rolleninstanzen.
* **Dienstpaket:**  
  Das Dienstpaket (.cspkg) enthält den Anwendungscode, die Konfigurationen und die Dienstdefinitionsdatei.

Weitere Informationen zu diesen Komponenten sowie zum Erstellen eines Pakets finden Sie [hier](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Vorbereiten Ihrer App
Bevor Sie einen Clouddienst bereitstellen können, müssen Sie das Clouddienstpaket (CSPKG) aus dem Anwendungscode und eine Clouddienstkonfigurationsdatei (CSCFG) erstellen. Das Azure-SDK stellt Tools zum Vorbereiten dieser erforderlichen Bereitstellungsdateien bereit. Sie können das SDK auf der Seite [Azure-Downloads](https://azure.microsoft.com/downloads/) in der Sprache herunterladen, in der Sie den Anwendungscode entwickeln möchten.

Drei Clouddienstfunktionen benötigen vor dem Export eines Dienstpakets spezielle Konfigurationen:

* Wenn Sie einen Clouddienst bereitstellen möchten, der Transport Layer Security (TLS) verwendet, das zuvor als „Secure Sockets Layer“ (SSL) bezeichnet wurde, müssen Sie zur Datenverschlüsselung [Ihre Anwendung für TLS konfigurieren](cloud-services-configure-ssl-certificate-portal.md#modify).
* Wenn Sie Remotedesktopverbindungen zu Rolleninstanzen konfigurieren möchten, [konfigurieren Sie die Rollen](cloud-services-role-enable-remote-desktop-new-portal.md) für Remotedesktop.
* Wenn Sie die ausführliche Überwachung für den Clouddienst konfigurieren möchten, aktivieren Sie für den Clouddienst die Azure-Diagnose. *Minimale Überwachung* (die Standardüberwachungsstufe) verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst wurden. *ausführlichen Überwachung* werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Informationen zum Aktivieren der Azure-Diagnose finden Sie unter [Aktivieren der Diagnose in Azure](cloud-services-dotnet-diagnostics.md).

Sie müssen das [Dienstpaket erstellen](cloud-services-model-and-package.md#servicepackagecspkg), um einen Clouddienst mit Bereitstellungen von Webrollen oder Workerrollen zu erstellen.

## <a name="before-you-begin"></a>Voraussetzungen
* Falls Sie das Azure SDK noch nicht installiert haben, klicken Sie auf **Install Azure SDK** (Azure SDK installieren), um die [Azure-Downloadseite](https://azure.microsoft.com/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten. (Dazu haben Sie auch später noch die Möglichkeit.)
* Falls Rolleninstanzen ein Zertifikat erfordern, erstellen Sie die Zertifikate. Clouddienste erfordern eine PFX-Datei mit einem privaten Schlüssel. Sie können die Zertifikate in Azure hochladen, wenn Sie den Clouddienst erstellen und bereitstellen.

## <a name="create-and-deploy"></a>Erstellen und Bereitstellen
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie auf **Ressource erstellen > Compute**, scrollen Sie nach unten zu **Clouddienst**, und klicken Sie darauf.

    ![Veröffentlichen des Clouddiensts 1](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Geben Sie im Bereich für den neuen **Clouddienst** einen Wert für **DNS-Name** ein.
4. Erstellen Sie eine neue **Ressourcengruppe** , oder wählen Sie eine vorhandene Ressourcengruppe aus.
5. Wählen Sie einen **Speicherort** aus.
6. Klicken Sie auf **Paket**. Der Bereich **Paket hochladen** wird geöffnet. Füllen Sie die erforderlichen Felder aus. Wenn eine der Rollen eine einzelne Instanz enthält, stellen Sie sicher, dass **Auch dann bereitstellen, wenn für mindestens eine Rolle nur eine Instanz vorhanden ist.** aktiviert ist.
7. Stellen Sie sicher, dass **Bereitstellung starten** aktiviert ist.
8. Klicken Sie auf **OK**, um den Bereich **Paket hochladen** zu schließen.
9. Wenn Sie keine Zertifikate hinzufügen möchten, klicken Sie auf **Erstellen**.

    ![Veröffentlichen des Clouddiensts 2](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Hochladen eines Zertifikats
Wenn Ihr Bereitstellungspaket [für die Verwendung von Zertifikaten konfiguriert](cloud-services-configure-ssl-certificate-portal.md#modify)wurde, können Sie das Zertifikat jetzt hochladen.

1. Wählen Sie **Zertifikate** und im Bereich **Zertifikate hinzufügen** die PFX-Datei mit dem TLS/SSL-Zertifikat aus. Geben Sie dann das **Kennwort** für das Zertifikat ein.
2. Klicken Sie auf **Zertifikat anfügen** und anschließend im Bereich **Zertifikate hinzufügen** auf **OK**.
3. Klicken Sie im Bereich **Clouddienst** auf **Erstellen**. Wenn sich die Bereitstellung im Status **Ready** befindet, können Sie mit den nächsten Schritten fortfahren.

    ![Veröffentlichen des Clouddiensts 3](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Überprüfen, ob Ihre Bereitstellung erfolgreich abgeschlossen wurde
1. Klicken Sie auf die Instanz des Clouddiensts.

    Der Status sollte anzeigen, dass der Dienst **Ausgeführt** wird.
2. Klicken Sie unter **Essentials** auf die **Website-URL**, um den Clouddienst in einem Webbrowser zu öffnen.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: ./cloud-services-choose-me.md

## <a name="next-steps"></a>Nächste Schritte
* [Allgemeine Konfiguration Ihres Clouddiensts](cloud-services-how-to-configure-portal.md)
* [Konfigurieren eines benutzerdefinierten Domänennamens](cloud-services-custom-domain-name-portal.md)
* [Verwalten Ihres Clouddiensts](cloud-services-how-to-manage-portal.md)
* Konfigurieren von [TLS/SSL-Zertifikaten](cloud-services-configure-ssl-certificate-portal.md)