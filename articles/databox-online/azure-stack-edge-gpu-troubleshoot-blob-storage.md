---
title: Problembehandlung bei Blob Storage für Azure Stack Edge Pro GPU | Microsoft-Dokumentation
description: Beschreibt die Problembehandlung bei Blob Storage für Azure Stack Edge-Geräte.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 2a77d4992d5ee2e505f9e3f29112d45c3776d8d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438760"
---
# <a name="troubleshoot-blob-storage-issues-for-an-azure-stack-edge-device"></a>Behandeln von Blob Storage-Problemen für Azure Stack Edge-Geräte 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Dieser Artikel beschreibt die Problembehandlung bei Blob Storage für Ihr Azure Stack Edge-Gerät. 

## <a name="errors-for-blob-storage-on-device"></a>Fehler für Blob Storage auf dem Gerät 

Dies sind die Fehler bezüglich Blob Storage auf Azure Stack Edge-Geräten.

| **Problem/Fehler** |  **Lösung** | 
|--------------------|-----------------|
|Untergeordnete Ressourcen können nicht abgerufen werden. Der Wert eines der HTTP-Header weist nicht das richtige Format auf.| Wählen Sie im Menü **Bearbeiten** die Option **Target Azure Stack APIs** (Azure Stack-APIs als Ziel verwenden) aus. Starten Sie dann Azure Storage-Explorer neu.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` in der Hosts-Datei eingetragen ist, die sich unter Windows am Pfad `C:\Windows\System32\drivers\etc\hosts` und unter Linux am Pfad `/etc/hosts` befindet.|
|Untergeordnete Ressourcen können nicht abgerufen werden.<br> Details: selbstsigniertes Zertifikat |Importieren Sie das SSL-Zertifikat für Ihr Gerät in Azure Storage-Explorer: <ol><li>[Das Zertifikat generieren und herunterladen](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>Wählen Sie im Menü **Bearbeiten** die Option SSL-Zertifikate und dann **Zertifikate importieren** aus.</li></ol>|
|Der AzCopy-Befehl reagiert eine Minute lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `C:\Windows\System32\drivers\etc\hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert eine Minute lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importieren Sie das SSL-Zertifikat für Ihr Gerät in Azure Storage-Explorer: <ol><li>[Das Zertifikat generieren und herunterladen](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>Wählen Sie im Menü **Bearbeiten** die Option SSL-Zertifikate und dann **Zertifikate importieren** aus.</li></ol>|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `/etc/hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location… The SSL connection could not be established`. |Importieren Sie das SSL-Zertifikat für Ihr Gerät in Azure Storage-Explorer: <ol><li>[Das Zertifikat generieren und herunterladen](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>Wählen Sie im Menü **Bearbeiten** die Option SSL-Zertifikate und dann **Zertifikate importieren** aus.</li></ol>|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `/etc/hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt: `Error parsing source location… The SSL connection could not be established`|Importieren Sie das SSL-Zertifikat für Ihr Gerät in den Zertifikatsspeicher des Systems. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Der Wert eines der HTTP-Header weist nicht das richtige Format auf.|Die installierte Version der Microsoft Azure Storage-Bibliothek für Python wird von Azure Stack Edge nicht unterstützt. Unterstützte Bibliotheksversionen finden Sie unter [Unterstützte Azure-Clientbibliotheken](azure-stack-edge-gpu-system-requirements-rest.md#supported-azure-client-libraries).|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …| Legen Sie vor dem Ausführen von Python die Umgebungsvariable REQUESTS_CA_BUNDLE auf den Pfad der Base64-codierten SSL-Zertifikatsdatei fest. (Weitere Informationen zum [Herunterladen des Zertifikats](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).) Führen Sie zum Beispiel aus:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Alternativ können Sie das Zertifikat dem Zertifikatspeicher des Systems hinzufügen und dann diese Umgebungsvariable auf den Pfad dieses Speichers festlegen. Beispiel für Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`|
|Bei der Verbindung ist eine Zeitüberschreitung aufgetreten.|Melden Sie sich auf Ihrem Gerät an, und überprüfen Sie dann, ob es entsperrt ist. Bei jedem Neustart des Geräts bleibt es gesperrt, bis sich ein Benutzer anmeldet.|
|Das Speicherkonto konnte nicht erstellt oder aktualisiert werden. Stellen Sie sicher, dass der Zugriffsschlüssel für Ihr Speicherkonto gültig ist. Aktualisieren Sie bei Bedarf den Schlüssel auf dem Gerät.|Synchronisieren Sie die Speicherkontoschlüssel. Führen Sie die [hier](azure-stack-edge-gpu-manage-storage-accounts.md#sync-storage-keys) beschriebenen Schritte aus.|

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei Fehlern bezüglich des Hochladens und Aktualisierens von Geräten](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-device-upload-and-refresh-errors).
