---
title: Geräteidentität und Desktopvirtualisierung – Azure Active Directory
description: Erfahren Sie, wie Sie die virtuelle Desktopinfrastruktur (VDI) und Azure AD-Geräteidentitäten gemeinsam verwenden können.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b81bdc056bd56e96669bde7e6d0473ef3feb3402
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049841"
---
# <a name="device-identity-and-desktop-virtualization"></a>Geräteidentität und Desktopvirtualisierung

Administratoren stellen in ihren Organisationen häufig VDI-Plattformen (Virtual Desktop Infrastructure) bereit, auf denen Windows-Betriebssysteme gehostet werden. Administratoren stellen VDI-Plattformen für Folgendes bereit:

- Einfachere Verwaltung
- Kostensenkung durch Konsolidierung und Zentralisierung von Ressourcen
- Endbenutzermobilität mit der Freiheit, jederzeit, überall und von jedem Gerät aus auf virtuelle Desktops zuzugreifen

Es gibt zwei Hauptarten von virtuellen Desktops:

- Beständig
- Nicht beständig

Bei der beständigen Version wird ein eindeutiges Desktopimage für jeden Benutzer oder einen Benutzerpool verwendet. Diese eindeutigen Desktops können angepasst und zur späteren Verwendung gespeichert werden. 

Bei der nicht beständigen Version wird eine Sammlung von Desktops verwendet, auf die Benutzer bei Bedarf zugreifen können. Diese nicht beständigen Desktops werden in ihren ursprünglichen Zustand zurückversetzt. Bei aktuellen Windows-Geräten<sup>1</sup> geschieht dies, wenn ein virtueller Computer heruntergefahren, neu gestartet oder sein Betriebssystem zurückgesetzt wird. Bei kompatiblen Windows-Geräten<sup>2</sup> geschieht dies, wenn ein Benutzer sich abmeldet.

Die Anzahl von nicht beständigen VDI-Bereitstellungen hat zugenommen, da Remotearbeit weiterhin stark verbreitet ist. Wenn Kunden nicht beständige VDI-Plattformen bereitstellen, ist es wichtig, veraltete Geräte zu verwalten, die sich durch häufige Geräteregistrierungen und das Fehlen einer geeigneten Strategie für die Gerätelebenszyklusverwaltung des anhäufen.

> [!IMPORTANT]
> Ohne Verwaltung veralteter Geräte kann sich der Druck auf die Mandantenkontingente erhöhen und zu Dienstunterbrechungen führen, wenn die Mandantenkontingente nicht ausreichen. Um diese Situation zu vermeiden, beachten Sie den folgenden Leitfaden, wenn Sie nicht beständige VDI-Umgebungen bereitstellen.

Dieser Artikel enthält den Microsoft-Leitfaden für Administratoren zur Unterstützung von Geräteidentität und VDI. Weitere Informationen zur Geräteidentität finden Sie im Artikel [Was ist eine Geräteidentität](overview.md).

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Bevor Sie die Geräteidentitäten in Azure AD für Ihre VDI-Umgebung konfigurieren, machen Sie sich mit den unterstützten Szenarien vertraut. In der folgenden Tabelle wird veranschaulicht, welche Bereitstellungsszenarien unterstützt werden. Eine Bereitstellung in diesem Zusammenhang impliziert, dass ein Administrator Geräteidentitäten skaliert konfigurieren kann, ohne dass eine Endbenutzerinteraktion erforderlich ist.

| Geräteidentitätstyp | Identitätsinfrastruktur | Windows-Geräte | VDI-Plattformversion | Unterstützt |
| --- | --- | --- | --- | --- |
| Hybrid in Azure AD eingebunden | Im Verbund<sup>3</sup> | Aktuelle Windows-Geräte und kompatible Windows-Geräte | Beständig | Ja |
|   |   | Aktuelle Windows-Geräte | Nicht beständig | Ja<sup>5</sup> |
|   |   | Kompatible Windows-Geräte | Nicht beständig | Ja<sup>6</sup> |
|   | Verwaltet<sup>4</sup> | Aktuelle Windows-Geräte und kompatible Windows-Geräte | Beständig | Ja |
|   |   | Aktuelle Windows-Geräte | Nicht beständig | Nein |
|   |   | Kompatible Windows-Geräte | Nicht beständig | Ja<sup>6</sup> |
| In Azure AD eingebunden | Im Verbund | Aktuelle Windows-Geräte | Beständig | Begrenzt<sup>7</sup> |
|   |   |   | Nicht beständig | Nein |
|   | Verwaltet | Aktuelle Windows-Geräte | Beständig | Begrenzt<sup>7</sup> |
|   |   |   | Nicht beständig | Nein |
| Bei Azure AD registriert | Im Verbund/verwaltet | Aktuelle Windows-Geräte/kompatible Windows-Geräte | Beständig/nicht beständig | Nicht zutreffend |

<sup>1</sup> **Aktuelle Windows-Geräte** bezieht sich auf Geräte unter Windows 10, Windows Server 2016 v1803 oder höher und Windows Server 2019.

<sup>2</sup> **Kompatible Windows-Geräte** bezieht sich auf Geräte mit Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2. Supportinformationen zu Windows 7 finden Sie unter [Der Support für Windows 7 läuft aus](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Supportinformationen zu Windows Server 2008 R2 finden Sie unter [Der Support für Windows Server 2008 wird eingestellt](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> Eine Identitätsinfrastruktur-Umgebung des Typs **Im Verbund** ist eine Umgebung mit einem Identitätsanbieter wie AD FS oder einem Drittanbieter.

<sup>4</sup> Eine Identitätsinfrastruktur-Umgebung des Typs **Verwaltet** ist eine Umgebung mit Azure AD als Identitätsanbieter und wird entweder mit [Kennworthashsynchronisierung (PHS)](../hybrid/whatis-phs.md) oder [Passthrough-Authentifizierung (PTA)](../hybrid/how-to-connect-pta.md) und [nahtloser einmaliger Anmeldung](../hybrid/how-to-connect-sso.md) bereitgestellt.

<sup>5</sup> **Die Unterstützung nicht beständiger Versionen für aktuelle Windows-Geräte** erfordert zusätzliche Überlegungen, wie unten im Leitfaden beschrieben. Für dieses Szenario ist Windows 10 1803, Windows Server 2019 oder Windows Server (halbjährlicher Kanal) ab Version 1803 erforderlich.

<sup>6</sup> **Die Unterstützung nicht beständiger Versionen für kompatible Windows-Geräte** erfordert zusätzliche Überlegungen, wie unten im Leitfaden beschrieben.

<sup>7</sup> **Azure AD Join-Unterstützung** ist nur mit Azure Virtual Desktop und Windows 365 verfügbar.

## <a name="microsofts-guidance"></a>Leitfaden von Microsoft

Administratoren sollten auf Grundlage ihrer Identitätsinfrastruktur die folgenden Artikel heranziehen, um zu erfahren, wie sie die Azure AD-Hybrideinbindung konfigurieren können.

- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-federated-domains.md)
- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine verwaltete Umgebung](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Nicht persistente VDI-Plattform

Microsoft empfiehlt IT-Administratoren, den folgenden Leitfaden zu implementieren, wenn eine nicht beständige VDI-Plattform bereitgestellt wird. Wenn dies nicht beachtet wird, enthält Ihr Verzeichnis eine Vielzahl veralteter, in Azure AD Hybrid eingebundener Geräte, die über Ihre nicht beständige VDI-Plattform registriert wurden. Dies erhöht den Druck auf die Mandantenkontingente und kann zu Dienstunterbrechungen führen, wenn die Anzahl der Mandantenkontingente nicht ausreicht.

- Wenn Sie das Systemvorbereitungstool (sysprep.exe) und ein Image einer niedrigeren Version als Windows 10 1809 für die Installation verwenden, stellen Sie sicher, dass dieses Image nicht von einem Gerät stammt, das bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.
- Wenn Sie zusätzliche VMs mit einer Momentaufnahme des virtuellen Computers erstellen, stellen Sie sicher, dass diese Momentaufnahme nicht von einem Computer stammt, der bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.
- Erstellen und verwenden Sie ein Präfix für den Anzeigenamen (z. B. „NBVDI-“) des Computers, das darauf hinweist, dass der Desktop VDI-basiert und nicht beständig ist.
- Für kompatible Windows-Geräte:
   - Implementieren Sie den Befehl **autoworkplacejoin /leave** als Teil des Abmeldeskripts. Dieser Befehl sollte im Kontext des Benutzers ausgelöst werden und ausgeführt werden, bevor sich der Benutzer vollständig abgemeldet hat, während noch Netzwerkkonnektivität besteht.
- Für aktuelle Windows-Geräte in einer Verbundumgebung (z. B. AD FS):
   - Implementieren Sie **dsregcmd /join** als Teil der Startsequenz/Reihenfolge des virtuellen Computers und vor der Benutzeranmeldung.
   - Führen Sie „dsregcmd /leave“ **NICHT** als Teil des Prozesses zum Herunterfahren/Neustarten der VM aus.
- Definieren und implementieren Sie einen Prozess zum [Verwalten veralteter Geräte](manage-stale-devices.md).
   - Sobald Sie über eine Strategie zum Identifizieren Ihrer nicht beständigen, in Azure AD Hybrid eingebundenen Geräte verfügen (wie z. B. das Verwenden eines Präfixes für den Computeranzeigenamen), können Sie die Bereinigung dieser Geräte aggressiver betreiben, um sicherzustellen, dass Ihr Verzeichnis nicht von vielen veralteten Geräten verwendet wird.
   - Für nicht beständige VDI-Bereitstellungen von aktuellen und kompatiblen Windows-Geräten sollten Sie Geräte löschen, deren **ApproximateLastLogonTimestamp** älter als 15 Tage ist.

> [!NOTE]
> Wenn Sie eine nicht-persistente VDI verwenden und das Hinzufügen eines Geschäfts-, Schul- oder Unikontos verhindern möchten, muss der folgende Registrierungsschlüssel festgelegt sein:  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Stellen Sie sicher, dass Sie Windows 10, Version 1803 oder höher, verwenden.  
>
> Das Roaming jeglicher Daten unter dem Pfad `%localappdata%` wird nicht unterstützt. Wenn Sie sich für das Verschieben von Inhalten unter `%localappdata%` entscheiden, stellen Sie sicher, dass der Inhalt der folgenden Ordner und Registrierungsschlüssel das Gerät **niemals** verlässt. Zum Beispiel: Profil-Migrations-Tools müssen die folgenden Ordner und Schlüssel auslassen:
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WorkplaceJoin`
>
> Ein Roaming des Gerätezertifikats des Geschäftskontos wird nicht unterstützt. Das von "MS-Organization-Access" ausgestellte Zertifikat wird im persönlichen Zertifikatspeicher des aktuellen Benutzers und auf dem lokalen Computer gespeichert.


### <a name="persistent-vdi"></a>Dauerhafte VDI

Microsoft empfiehlt IT-Administratoren, den folgenden Leitfaden zu implementieren, wenn eine persistente VDI-Plattform bereitgestellt wird. Andernfalls kann es zu Problemen bei der Bereitstellung und Authentifizierung kommen. 

- Wenn Sie das Systemvorbereitungstool (sysprep.exe) und ein Image einer niedrigeren Version als Windows 10 1809 für die Installation verwenden, stellen Sie sicher, dass dieses Image nicht von einem Gerät stammt, das bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.
- Wenn Sie zusätzliche VMs mit einer Momentaufnahme des virtuellen Computers erstellen, stellen Sie sicher, dass diese Momentaufnahme nicht von einem Computer stammt, der bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.

Zudem wird empfohlen, dass Sie einen Prozess zur [Verwaltung veralteter Geräte](manage-stale-devices.md) implementieren. Dadurch wird sichergestellt, dass Ihr Verzeichnis nicht von einer Vielzahl veralteter Geräte ausgelastet wird, wenn Sie Ihre VMs regelmäßig zurücksetzen.
 
## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-federated-domains.md)
