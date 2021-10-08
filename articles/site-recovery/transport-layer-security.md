---
title: Transport Layer Security in Azure Site Recovery
description: Erfahren Sie, wie Sie Azure Site Recovery ermöglichen, TLS (Transport Layer Security) als Verschlüsselungsprotokoll zu verwenden, um Daten bei der Übertragung über ein Netzwerk zu schützen.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 3937fd9f88a844c0257bc6cb66b4c3f97a112987
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598504"
---
# <a name="transport-layer-security-in-azure-site-recovery"></a>Transport Layer Security in Azure Site Recovery

Transport Layer Security (TLS) ist ein Verschlüsselungsprotokoll, mit dem Daten bei der Übertragung über ein Netzwerk geschützt werden. Azure Site Recovery verwendet TLS, um zu übertragende Daten zu schützen. Azure Site Recovery verwendet jetzt das TLS 1.2-Protokoll, um die Sicherheit zu verbessern.

## <a name="enable-tls-on-older-versions-of-windows"></a>Aktivieren von TLS in älteren Versionen von Windows

Wenn auf dem Computer frühere Versionen von Windows laufen, installieren Sie die entsprechenden Updates wie unten beschrieben, und nehmen Sie die Änderungen in der Registrierung vor, wie in den entsprechenden KB-Artikeln dokumentiert.

|Betriebssystem  |KB-Artikel |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>Das Update installiert die erforderlichen Komponenten für das Protokoll. Nach der Installation stellen Sie sicher, dass die Registrierungsschlüssel wie in den obigen KB-Artikeln beschrieben aktualisiert werden, um die erforderlichen Protokolle zu aktivieren.

## <a name="verify-windows-registry"></a>Überprüfen der Windows-Registrierung

### <a name="configure-schannel-protocols"></a>Konfigurieren von SChannel-Protokollen

Die folgenden Registrierungsschlüssel stellen sicher, dass das TLS 1.2-Protokoll auf der Ebene der SChannel-Komponente aktiviert ist:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Standardmäßig werden die oben genannten Registrierungsschlüssel in den angezeigten Werten festgelegt, die in Windows Server 2012 R2 und höher festgelegt sind. Wenn die Registrierungsschlüssel nicht vorhanden sind, müssen sie für diese Versionen von Windows nicht erstellt werden.

### <a name="configure-net-framework"></a>Konfigurieren von .NET Framework

Mit den folgenden Registrierungsschlüsseln wird .NET Framework für die Unterstützung starker Kryptografie konfiguriert. Weitere Informationen zum Konfigurieren von .NET Framework finden Sie [hier](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="why-enable-tls-12"></a>Warum muss TLS 1.2 aktiviert werden?

TLS 1.2 ist sicherer als ältere Kryptografieprotokolle wie SSL 2.0, SSL 3.0, TLS 1.0 und TLS 1.1. Azure Site Recovery-Dienste unterstützen vollständig TLS 1.2.

### <a name="what-determines-the-encryption-protocol-used"></a>Wodurch wird das verwendete Verschlüsselungsprotokoll festgelegt?

Beim Einrichten der verschlüsselten Konversation wird die höchste Protokollversion ausgehandelt, die sowohl vom Client als auch vom Server unterstützt wird. Weitere Informationen zum TLS-Handshakeprotokoll finden Sie unter [Einrichten einer sicheren Sitzung mithilfe von TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-if-tls-12-is-not-enabled"></a>Welche Auswirkungen hat es, wenn TLS 1.2 nicht aktiviert ist?

Um den Schutz vor Angriffen durch Protokolldowngrades zu verbessern, beginnt Azure Site Recovery damit, TLS-Versionen vor 1.2. zu deaktivieren. Dies ist Teil einer langfristigen Umstellung aller Dienste, um Verbindungen mit älteren Protokollen und Verschlüsselungssammlungen zu unterbinden. Azure Site Recovery-Dienste und -Komponenten unterstützen TLS 1.2 vollständig. Windows-Versionen, auf denen die erforderlichen Updates oder bestimmte angepasste Konfigurationen fehlen, können jedoch dennoch verhindern, dass TLS 1.2-Protokolle bereitgestellt werden. Dies kann unter anderem zu folgenden Fehlern führen:

- Die Replikation kann an der Quelle scheitern.
- Azure Site Recovery-Komponentenverbindung scheitert mit Fehler 10054 (Eine vorhandene Verbindung wurde erzwungenermaßen vom Remotehost geschlossen).
- Dienste im Zusammenhang mit Azure Site Recovery stoppen oder starten nicht wie gewohnt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Transport Layer Security-Protokoll](/windows/win32/secauthn/transport-layer-security-protocol)
- [Sicherstellen der Unterstützung von TLS 1.2 in den bereitgestellten Betriebssystemen](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Bewährte Methoden für Transport Layer Security (TLS) mit .NET Framework](/dotnet/framework/network-programming/tls)