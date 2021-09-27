---
title: Erzwingen von TLS 1.2 – Azure Active Directory Device Registration-Dienst
description: Entfernen der Unterstützung von TLS 1.0 und TLS 1.1 für den Azure AD Device Registration-Dienst
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a36d856252f3ff63fd909d63f254a15930666e8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616040"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Erzwingen von TLS 1.2 für den Azure AD Device Registration-Dienst

Der Azure Active Directory (Azure AD) Device Registration-Dienst wird verwendet, um Geräte mit einer Geräteidentität mit der Cloud zu verbinden. Der Azure AD Device Registration-Dienst unterstützt derzeit die Verwendung von Transport Layer Security (TLS) 1.2 für die Kommunikation mit Azure. Um die Sicherheit und eine erstklassige Verschlüsselung sicherzustellen, empfiehlt Microsoft die Deaktivierung von TLS 1.0 und 1.1. In diesem Dokument finden Sie Informationen dazu, wie Sie sicherstellen können, dass auf zum Ausführen der Registrierung und zum Kommunizieren mit dem Azure AD Device Registration-Dienst verwendeten Computern TLS 1.2 verwendet wird.

Das TLS-Protokoll (Version 1.2) ist ein Kryptografieprotokoll, das auf die Bereitstellung sicherer Kommunikation ausgelegt ist. Das TLS-Protokoll zielt in erster Linie darauf ab, Datenschutz und die Datenintegrität zu gewährleisten. TLS hat schon viele Iterationen durchlaufen, wobei Version 1.2 in [RFC 5246 (externer Link)](https://tools.ietf.org/html/rfc5246) definiert ist.

Aus aktuellen Verbindungsanalysen geht eine geringe Verwendung von TLS 1.1 und TLS 1.0 hervor, aber wir stellen diese Informationen bereit, damit Sie alle betroffenen Clients oder Server ggf. aktualisieren können, bevor die Unterstützung für TLS 1.1 und TLS 1.0 endet. Wenn Sie eine lokale Infrastruktur für Hybridszenarien oder Active Directory-Verbunddienste (Active Directory Federation Services, AD FS) verwenden, müssen Sie sicherstellen, dass die Infrastruktur sowohl eingehende als auch ausgehende Verbindungen unterstützen kann, die TLS 1.2 verwenden.

## <a name="update-windows-servers"></a>Aktualisieren von Windows-Servern

Führen Sie auf Windows-Servern, die den Azure AD Device Registration-Dienst verwenden oder als Proxys fungieren, die folgenden Schritte aus, um sicherzustellen, dass TLS 1.2 aktiviert ist:

> [!IMPORTANT]
> Nachdem Sie die Registrierung aktualisiert haben, müssen Sie den Windows-Server neu starten, damit die Änderungen wirksam werden.

### <a name="enable-tls-12"></a>Aktivieren von TLS 1.2

Stellen Sie sicher, dass die folgenden Registrierungszeichenfolgen wie folgt konfiguriert sind:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client
  - "DisabledByDefault"=dword:00000000
  - "Enabled"=dword:00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server
  - "DisabledByDefault"=dword:00000000
  - "Enabled"=dword:00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>Aktualisieren von Nicht-Windows-Proxys

Sie müssen sicherstellen, dass TLS 1.2 auf allen Computern aktiviert ist, die als Proxys zwischen Geräten und dem Azure AD Device Registration-Dienst fungieren. Folgen Sie den Anweisungen des jeweiligen Herstellers, um die Unterstützung sicherzustellen.

## <a name="update-ad-fs-servers"></a>Aktualisieren von AD FS-Servern

Sie müssen sicherstellen, dass TLS 1.2 auf allen AD FS-Servern aktiviert ist, die für die Kommunikation mit dem Azure AD Device Registration-Dienst verwendet werden. Informationen zum Aktivieren/Überprüfen dieser Konfiguration finden Sie unter [Verwalten von SSL/TLS-Protokollen und Verschlüsselungssammlungen für AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

## <a name="client-updates"></a>Clientupdates

Da alle Client-Server- und Browser-Server-Kombinationen TLS 1.2 verwenden müssen, um eine Verbindung mit dem Azure AD Device Registration-Dienst herzustellen, müssen Sie diese Geräte möglicherweise aktualisieren.

Die folgenden Clients können TLS 1.2 bekanntermaßen nicht unterstützen. Aktualisieren Sie Ihre Clients, um den unterbrechungsfreien Zugriff sicherzustellen.

- Android, Version 4.3 und früher
- Firefox, Version 5.0 und früher
- Internet Explorer, Version 8-10 unter Windows 7 und früher
- Internet Explorer 10 unter Windows Phone 8.0
- Safari, Version 6.0.4 unter OS X 10.8.4 und früher

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über TLS/SSL (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)