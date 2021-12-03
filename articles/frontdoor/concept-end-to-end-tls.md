---
title: End-to-End-TLS mit Azure Front Door
description: Weitere Informationen über die End-to-End-TLS-Verschlüsselung bei Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 5e0cac6f9ba6a245ec201666adb2c5cfeed79c38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343095"
---
# <a name="end-to-end-tls-with-azure-front-door"></a>End-to-End-TLS mit Azure Front Door

Transport Layer Security (TLS), zuvor bekannt als Secure Sockets Layer (SSL), ist die standardmäßige Sicherheitstechnologie, mit der eine verschlüsselte Verbindung zwischen einem Webserver und einem Browser hergestellt wird. Über diese Verbindung wird sichergestellt, dass alle Daten privat und verschlüsselt zwischen dem Webserver und dem Webbrowser übertragen werden.

Um Ihre Sicherheits- oder Complianceanforderungen zu erfüllen, unterstützt Azure Front Door (AFD) die End-to-End-TLS-Verschlüsselung. Die Front Door-TLS/SSL-Auslagerung beendet die TLS-Verbindung, entschlüsselt den Datenverkehr in Azure Front Door und verschlüsselt den Datenverkehr erneut, bevor er an das Back-End weitergeleitet wird. Da Verbindungen mit dem Back-End über die öffentliche IP-Adresse erfolgen, wird es dringend empfohlen, dass Sie HTTPS als Weiterleitungsprotokoll in Azure Front Door konfigurieren, um die End-to-End-TLS-Verschlüsselung vom Client zum Back-End zu erzwingen.

## <a name="end-to-end-tls-encryption"></a>End-to-End-TLS-Verschlüsselung

Mit End-to-End-TLS können Sie vertrauliche Daten während der Übertragung an das Back-End sichern und gleichzeitig von Azure Front Door-Features wie z. B. einem globalem Lastenausgleich und Zwischenspeichern profitieren. Weitere Features sind u. a. das URL-basierte Routing, die TCP-Aufteilung, das Zwischenspeichern an dem Edge-Standort, der den Clients am nächsten ist und das benutzerdefinierte Anpassen von HTTP-Anforderungen am Edge.

Azure Front Door lagert die TLS-Sitzungen am Edge aus und entschlüsselt Clientanforderungen. Anschließend werden die konfigurierten Routingregeln angewendet, um die Anforderungen an das entsprechende Back-End im Back-End-Pool weiterzuleiten. Azure Front Door stellt dann eine neue TLS-Verbindung mit dem Back-End her und verschlüsselt alle Daten mithilfe des Back-End-Zertifikats erneut, bevor die Anforderung an das Back-End übertragen wird. Antworten vom Back-End durchlaufen denselben Verschlüsselungsprozess zurück an den Endbenutzer. Sie können Azure Front Door so konfigurieren, dass HTTPS als Weiterleitungsprotokoll verwendet wird, um End-to-End-TLS zu aktivieren.

## <a name="supported-tls-versions"></a>Unterstützte TLS-Versionen

Azure Front Door unterstützt drei Versionen des TLS-Protokolls: TLS-Versionen 1.0, 1.1 und 1.2. Alle Azure Front Door-Profile, die nach September 2019 erstellt wurden, verwenden TLS 1.2 als Mindeststandard, TLS 1.0 und TLS 1.1 werden jedoch aus Gründen der Abwärtskompatibilität auch weiterhin unterstützt.

Obwohl Azure Front Door TLS 1.2 unterstützt, bei dem die Clientauthentizierung/gegenseitige Authentifizierung in RFC 5246 eingeführt wurde, unterstützt Azure Front Door diese Authentifizierungsart zurzeit nicht.

Sie können die TLS-Mindestversion in Azure Front Door in den HTTPS-Einstellungen der benutzerdefinierten Domäne über das Azure-Portal oder die  [Azure-REST-API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) konfigurieren. Derzeit können Sie zwischen 1.0 und 1.2 wählen. Wenn Sie TLS 1.2 als Mindestversion angeben, steuern Sie so die TLS-Mindestversion, die Azure Front Door von einem Client akzeptiert. Wenn Azure Front Door TLS-Datenverkehr an das Back-End initiiert, versucht es, die beste TLS-Version auszuhandeln, die vom Back-End zuverlässig und konsistent akzeptiert wird.

## <a name="supported-certificates"></a>Unterstützte Zertifikate

Wenn Sie Ihr TLS/SSL-Zertifikat erstellen, müssen Sie eine vollständige Zertifikatkette mit einer zulässigen Zertifizierungsstelle erstellen, die in der  [Microsoft-Liste der vertrauenswürdigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) enthalten ist. Bei Verwendung einer unzulässigen Zertifizierungsstelle wird Ihre Anforderung abgelehnt.

Zertifikate von internen Zertifizierungsstellen oder selbstsignierte Zertifikate sind nicht zulässig.

## <a name="online-certificate-status-protocol-ocsp-stapling"></a>OCSP-Anheftung (Online Certificate Status Protocol)

Die OCSP-Anheftung wird in Azure Front Door standardmäßig unterstützt, und es ist keine Konfiguration erforderlich.

## <a name="backend-tls-connection-azure-front-door-to-backend"></a>Back-End-TLS-Verbindung (Azure Front Door zum Back-End)

Bei HTTPS-Verbindungen erwartet Azure Front Door, dass Ihr Back-End ein Zertifikat von einer gültigen Zertifizierungsstelle vorlegt, und dass die Antragstellernamen mit dem Back-End-*Hostnamen* übereinstimmen. Wenn beispielsweise der Back-End-Hostname auf `myapp-centralus.contosonews.net` festgelegt ist und das Zertifikat, das Ihr Back-End während des TLS-Handshakes vorlegt, weder `myapp-centralus.contosonews.net` noch`*.contosonews.net` im Antragstellernamen aufweist, verweigert Azure Front Door die Verbindung, und ein Fehler tritt auf.

> [!NOTE]
> Das Zertifikat muss über eine vollständige Zertifikatkette mit Blatt- und Zwischenzertifikaten verfügen. Die Stammzertifizierungsstelle muss in der  [Microsoft-Liste der vertrauenswürdigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) enthalten sein. Wenn ein Zertifikat ohne vollständige Kette präsentiert wird, funktionieren die Anforderungen, die dieses Zertifikat beinhalten, nicht wie erwartet.

Aus Sicherheitsgründen wird es nicht empfohlen, die Überprüfung des Zertifikatsubjektnamens zu deaktivieren. In bestimmten Anwendungsfällen, z. B. bei Tests, muss Ihr Ursprung ein selbstsigniertes Zertifikat verwenden. Um das Problem fehlerhafter HTTPS-Verbindungen zu umgehen, können Sie die Überprüfung des Zertifikatsubjektnamens für Azure Front Door deaktivieren. Die Deaktivierungsoption finden Sie in den Azure Front Door-Einstellungen im Azure Portal und unter BackendPoolsSettings in der Azure Front Door-API. 

## <a name="frontend-tls-connection-client-to-front-door"></a>Front-End-TLS-Verbindung (Client zu Front Door)

Um das HTTPS-Protokoll für die sichere Bereitstellung von Inhalten in einer benutzerdefinierten Azure Front Door-Domäne zu aktivieren, können Sie ein von Azure Front Door verwaltetes oder Ihr eigenes Zertifikat verwenden.  

* Ein von Azure Front Door verwaltetes Zertifikat stellt über Digicert ein TLS/SSL-Standardzertifikat zur Verfügung und wird im Schlüsseltresor von Azure Front Door gespeichert.   

* Wenn Sie ein eigenes Zertifikat verwenden möchten, können Sie ein Zertifikat einer unterstützten Zertifizierungsstelle integrieren. Hierbei kann es sich um ein Standard-TLS-, ein erweitertes Validierungs- oder sogar um ein Platzhalterzertifikat handeln.  

* Selbstsignierte Zertifikate werden nicht unterstützt. Weitere Informationen zum  [Aktivieren von HTTPS für eine benutzerdefinierte Domäne](front-door-custom-domain-https.md).

### <a name="certificate-autorotation"></a>Automatische Zertifikatrotation

Von Azure Front Door verwaltete Zertifikate werden innerhalb von 90 Tagen vor dem Ende der Ablaufzeit automatisch von Azure Front Door zur neuesten Version rotiert. Von Azure Front Door verwaltete Standard-/Premium-Zertifikate werden innerhalb von 45 Tagen vor dem Ende der Ablaufzeit automatisch von Azure Front Door zur neuesten Version rotiert. Wenn Sie ein von Azure Front Door verwaltetes Zertifikat verwenden und feststellen, dass das Ablaufdatum des Zertifikats weniger als 60 Tage oder, was die Standard-/Premium-SKU angeht, weniger als 30 Tage entfernt ist, sollten Sie ein Supportticket erstellen. 

Für Ihr eigenes benutzerdefiniertes TLS/SSL-Zertifikat:

1. Legen Sie die Geheimnisversion auf „Neueste“ fest, damit das Zertifikat automatisch zur neuesten Version rotiert wird, wenn eine neuere Version des Zertifikats in Ihrem Schlüsseltresor verfügbar ist. Bei benutzerdefinierten Zertifikaten wird das Zertifikat innerhalb von 1 bis 2 Tagen unabhängig von der Ablauffrist automatisch zu einer neueren Version des Zertifikats rotiert.

1. Bei der Auswahl einer bestimmten Version wird die automatische Rotation nicht unterstützt. Sie müssen die neue Version manuell auswählen, um das Zertifikat zu rotieren. Es dauert bis zu 24 Stunden, bis die neue Version des Zertifikats/Geheimnisses bereitgestellt wird.

    Sie müssen sicherstellen, dass der Dienstprinzipal für Front Door Zugriff auf den Schlüsseltresor hat. Informationen finden Sie unter „Gewähren des Zugriffs auf Ihren Schlüsseltresor“. Der aktualisierte Zertifikatrolloutvorgang von Azure Front Door verursacht keine Downtime in der Produktion, sofern sich der Antragstellername oder der alternative Antragstellername (SAN) für das Zertifikat nicht ändert.

## <a name="supported-cipher-suites"></a>Unterstützte Verschlüsselungssammlungen:

Bei TLS1.2 werden folgende Verschlüsselungssammlungen unterstützt:

* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

> [!NOTE]
> Für Windows 10 und höhere Versionen wird empfohlen, eine oder beide Suites mit ECDHE-Verschlüsselungsverfahren zu aktivieren, um die Sicherheit zu erhöhen. Windows 8.1, 8 und 7 sind nicht mit diesen Suites mit ECDHE-Verschlüsselungsverfahren kompatibel. Die Suites mit DHE-Verschlüsselungsverfahren wurden aus Gründen der Kompatibilität mit diesen Betriebssystemen bereitgestellt.

Wenn Sie benutzerdefinierte Domänen mit TLS 1.0/1.1 verwenden, werden die folgenden Verschlüsselungssammlungen unterstützt:

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
* TLS_RSA_WITH_AES_256_GCM_SHA384
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA
* TLS_RSA_WITH_AES_128_CBC_SHA
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

Azure Front Door unterstützt das Konfigurieren bestimmter Chiffrensammlungen (Cipher Suites) nicht. Sie können Ihr eigenes benutzerdefiniertes TLS/SSL-Zertifikat von Ihrer Zertifizierungsstelle (z. B. VeriSign, Entrust oder DigiCert) erhalten. Anschließend können Sie festlegen, dass beim Generieren bestimmte Verschlüsselungssammlungen für das Zertifikat markiert werden sollen. 

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren einer benutzerdefinierten Domäne](front-door-custom-domain.md) für Azure Front Door.
* [Aktivieren von HTTPS für eine benutzerdefinierte Domäne](front-door-custom-domain-https.md).
