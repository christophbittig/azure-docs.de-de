---
title: Anbieter Microsoft-kompatibler FIDO2-Sicherheitsschlüssel für die Anmeldung bei Azure AD werden
description: Hier erfahren Sie, wie Sie ein FIDO2-Hardwarepartner werden.
ms.date: 08/02/2021
services: active-directory
ms.service: active-directory
ms.subservice: authentication
author: knicholasa
ms.author: nichola
ms.topic: conceptual
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d21b99fd1def56b60896d157c8a35b9381a003
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355485"
---
# <a name="become-a-microsoft-compatible-fido2-security-key-vendor"></a>Anbieter Microsoft-kompatibler FIDO2-Sicherheitsschlüssel werden

Bei den meisten Sicherheitsverletzungen im Zusammenhang mit Hackerangriffen handelt es sich um gestohlene oder schwache Kennwörter. Häufig erzwingt die IT eine höhere Kennwortkomplexität oder häufige Kennwortänderungen, um das Risiko eines Sicherheitsvorfalls zu verringern. Hierdurch erhöhen sich jedoch die Helpdeskkosten, und es führt zu negativen Benutzererfahrungen, da Benutzer sich neue, komplexe Kennwörter merken oder speichern müssen.

FIDO2-Sicherheitsschlüssel bieten hier eine Alternative. FIDO2-Sicherheitsschlüssel können schwache Anmeldeinformationen durch starke hardwarebasierte Anmeldeinformationen mit öffentlichem/privatem Schlüssel ersetzen, die nicht wiederverwendet, wiedergegeben oder dienstübergreifend geteilt werden können. Sicherheitsschlüssel unterstützen Szenarien mit gemeinsam genutzten Geräten, sodass Sie Ihre Anmeldeinformationen mitnehmen und sich sicher bei einem Windows 10-Gerät, das einem Azure Active Directory beigetreten ist und zu Ihrer Organisation gehört, authentifizieren können.

Microsoft arbeitet mit FIDO2-Sicherheitsschlüsselanbietern als Partnern, um sicherzustellen, dass Sicherheitsgeräte unter Windows, mit dem Microsoft Edge-Browser und mit Microsoft-Onlinekonten funktionieren, um eine sichere Authentifizierung ohne Kennwort zu ermöglichen.

Mit dem folgenden Prozess können Sie Anbieter Microsoft-kompatibler FIDO2-Sicherheitsschlüssel werden.  Microsoft verpflichtet sich nicht, Markteinführungsaktivitäten mit dem Partner zu unternehmen, und bewertet die Partnerpriorität basierend auf der Kundennachfrage.

1. Zunächst muss Ihr Authentifikator über eine FIDO2-Zertifizierung verfügen. Wir können nicht mit Anbietern zusammenarbeiten, die keine FIDO2-Zertifizierung besitzen. Weitere Informationen zur Zertifizierung finden Sie auf dieser Website: [https://fidoalliance.org/certification/](https://fidoalliance.org/certification/).
2. Sobald Sie über eine FIDO2-Zertifizierung verfügen, tragen Sie ihre Anfrage hier in unser Formular ein: [https://forms.office.com/r/NfmQpuS9hF](https://forms.office.com/r/NfmQpuS9hF). Unser Engineering-Team testet nur die Kompatibilität Ihrer FIDO2-Geräte. Wir testen nicht die Sicherheit Ihrer Lösungen.
3. Sobald wir einen Wechsel zur Testphase bestätigt haben, dauert der Prozess in der Regel etwa 3 bis 6 Monate. Die Schritte umfassen in der Regel:
    - Erste Diskussion zwischen Microsoft und Ihrem Team.
        - Überprüfung der FIDO Alliance-Zertifizierung oder des Pfads zur Zertifizierung, falls noch nicht abgeschlossen.
        - Erhalten einer Übersicht über das Gerät vom Anbieter.
    - Microsoft teilt unsere Testskripts mit Ihnen. Unser Engineering-Team kann Fragen beantworten, wenn Sie spezielle Anforderungen haben.
    - Sie stellen alle übergebenen Ergebnisse fertig und senden sie an das Microsoft Engineering-Team.
    - Nach der Bestätigung durch Microsoft senden Sie mehrere Hardwaremuster/Lösungsbeispiele jedes Geräts an das Microsoft Engineering-Team.
        - Nach Erhalt führt das Microsoft Engineering-Team die Testskriptüberprüfung und den Benutzererfahrungsflow durch.
4. Nach erfolgreichem Bestehen aller Tests durch das Microsoft Engineering-Team bestätigt Microsoft, dass das Gerät des Anbieters im [FIDO MDS](https://fidoalliance.org/metadata/) aufgeführt ist.
5. Microsoft fügt Ihren FIDO2-Sicherheitsschlüssel im Azure AD-Back-End sowie zu unserer Liste der genehmigten FIDO2-Anbieter hinzu.

## <a name="current-partners"></a>Aktuelle Partner

In der folgenden Tabelle sind Partner aufgeführt, die Anbieter Microsoft-kompatibler FIDO2-Sicherheitsschlüssel sind.

| **Anbieter** | **Link** |
| --- | --- |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| Go-Trust-ID | [https://www.gotrustid.com/](https://www.gotrustid.com/idem-key) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Kensington  | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Nymi   | [https://www.nymi.com/product](https://www.nymi.com/product) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| Thales | [https://cpl.thalesgroup.com/access-management/authenticators/fido-devices](https://cpl.thalesgroup.com/access-management/authenticators/fido-devices) |
| Thetis | [https://thetis.io/collections/fido2](https://thetis.io/collections/fido2) |
| Token2, Schweiz | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| TrustKey-Lösungen | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net/) |
| Yubico | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |

## <a name="next-steps"></a>Nächste Schritte

[FIDO2-Kompatibilität](fido2-compatibility.md)

