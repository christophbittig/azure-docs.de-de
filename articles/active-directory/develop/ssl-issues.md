---
title: Beheben von TLS/SSL-Problemen (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie verschiedene Probleme behandeln, die bei der Verwendung von TLS/SSL-Zertifikaten mit der MSAL.Objective-C-Bibliothek auftreten können.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: aceddf008979453383a1460da4342fd2a066d6b1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039832"
---
# <a name="troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Behandeln von TLS/SSL-Problemen mit der MSAL für iOS und macOS

Dieser Artikel enthält Informationen zur Behandlung von Problemen, die bei der Verwendung der [Microsoft Authentication Library (MSAL) für iOS und macOS](reference-v2-libraries.md) auftreten können.

## <a name="network-issues"></a>Netzwerkfehler

**Fehler -1200**: „Ein SSL-Fehler ist aufgetreten, und es kann keine sichere Verbindung mit dem Server hergestellt werden.“

Dieser Fehler bedeutet, dass die Verbindung nicht sicher ist. Er tritt auf, wenn ein Zertifikat ungültig ist. Weitere Informationen, z. B. dazu, welcher Server die TLS-Überprüfung nicht bestanden hat, finden Sie im `userInfo`-Wörterbuch des Fehlerobjekts unter `NSURLErrorFailingURLErrorKey`.

Dieser Fehler stammt aus der Netzwerkbibliothek von Apple. Eine vollständige Liste mit den NSURL-Fehlercodes ist in der Datei „NSURLError.h“ in den SDKs für macOS und iOS enthalten. Weitere Informationen zu diesem Fehler finden Sie auf der Seite zum Thema [Systemfehlercodes für URL-Ladevorgang](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Zertifikatprobleme

Wenn über die URL mit einem ungültigen Zertifikat eine Verbindung mit dem Server hergestellt wird, den Sie im Rahmen des Authentifizierungsablaufs nutzen möchten, ist ein guter Ansatz zum Diagnostizieren des Problems das Testen der URL per SSL-Überprüfungsdienst, z. B. [SSL-Servertest](https://www.ssllabs.com/ssltest/analyze.html). Hiermit wird der Server anhand von unterschiedlichen Szenarien und Browsern getestet und eine Überprüfung auf viele bekannte Sicherheitsrisiken durchgeführt.

Beim neuen Feature [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) von Apple werden auf Apps, für die TLS/SSL-Zertifikate verwendet werden, standardmäßig strengere Sicherheitsrichtlinien angewendet. Für einige Betriebssysteme und Webbrowser werden einige dieser Richtlinien bereits standardmäßig angewendet. Aus Sicherheitsgründen empfehlen wir Ihnen, ATS nicht zu deaktivieren.

Für Zertifikate mit Verwendung von SHA-1-Hashes bestehen bekannte Sicherheitsrisiken. Bei den meisten modernen Webbrowsern sind Zertifikate mit SHA-1-Hashes nicht zulässig.

## <a name="captive-portals"></a>Captive Portals

Über ein Captive Portal wird einem Benutzer eine Webseite angezeigt, wenn er zum ersten Mal auf ein WLAN-Netzwerk zugreift und noch nicht über die Zugriffsberechtigung für das Netzwerk verfügt. Der Internetdatenverkehr wird abgefangen, bis der Benutzer die Anforderungen des Portals erfüllt hat. Bis der Benutzer die Verbindung über das Portal hergestellt hat, können Netzwerkfehler auftreten, weil der Benutzer nicht auf die Netzwerkressourcen zugreifen kann.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Captive Portals](https://en.wikipedia.org/wiki/Captive_portal) und das neue Feature [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) von Apple.
