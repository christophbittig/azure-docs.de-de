---
title: Was ist Azure AD Connect V2.0? | Microsoft-Dokumentation
description: Erfahren Sie mehr über die nächste Version von Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management, has-adal-ref
ms.openlocfilehash: 06b982a43c5155ce5a111bafbac41862363ec07d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355491"
---
# <a name="introduction-to-azure-ad-connect-v20"></a>Einführung in Azure AD Connect V2.0 

Azure AD Connect wurde vor einigen Jahren veröffentlicht.  Seit diesem Zeitpunkt wurden einige der Komponenten, die von Azure AD Connect verwendet werden, als veraltet geplant und auf neuere Versionen aktualisiert.  Der Versuch, alle diese Komponenten einzeln zu aktualisieren, würde Zeit kosten und Planung erfordern.  

Um dies zu tun, wollten wir so viele dieser neueren Komponenten wie möglich in einem neuen, einzelnen Release bündeln, sodass Sie nur einmal aktualisieren müssen. Dieses Release wird in Azure AD Connect V2.0 veröffentlicht.  Dies ist eine neue Version von derselben Software, die verwendet wird, um ihre Hybrididentitätsziele zu erreichen, die mit den neuesten grundlegenden Komponenten erstellt wurden. 

## <a name="what-are-the-major-changes"></a>Worin bestehen die wichtigsten Änderungen? 

### <a name="sql-server-2019-localdb"></a>SQL Server 2019 LocalDB-Funktion 

Die vorherigen Versionen von Azure AD Connect wurden mit einer SQL Server 2012 LocalDB-Funktion im Lieferumfang geliefert. Das V2.0-Release wird mit einer SQL Server 2019 LocalDB-Funktion geliefert. Das verspricht eine verbesserte Stabilität und Leistung und bietet mehrere sicherheitsbezogene Fehlerbehebungen. Für den SQL Server 2012 wird der erweiterte Support ab Juli 2022 nicht mehr unterstützt. Weitere Informationen finden Sie in der Dokumentation [Microsoft SQL 2019 Support](https://www.microsoft.com/sql-server/sql-server-2019).

### <a name="msal-authentication-library"></a>MSAL-Authentifizierungsbibliothek 

Bei den vorherigen Versionen von Azure AD Connect war im Lieferumfang die ADAL-Authentifizierungsbibliothek enthalten. Diese Bibliothek wird im Juni 2022 veraltet sein. Das V2.0-Release wird mit der neueren MSAL-Bibliothek geliefert. Weitere Informationen finden Sie in der [Übersicht über die MSAL-Bibliothek](../../active-directory/develop/msal-overview.md).

### <a name="visual-c-redist-14"></a>Visual C++ Redistributable 14 

Der SQL Server 2019 erfordert die Visual C++ Redistributable 14-Runtime, daher aktualisieren wir die C++-Laufzeitbibliothek für die Verwendung dieser Version. Diese wird mit dem Paket Azure AD Connect V2.0 installiert, sodass Sie keine Maßnahmen für die Aktualisierung des C++-Runtime ergreifen müssen. 

### <a name="tls-12"></a>TLS 1.2 

TLS 1.0 und TLS 1.1 sind Protokolle, die als unsicher angesehen und von Microsoft als veraltet eingestuft werden. Dieses Release von Azure AD Connect unterstützt nur TLS 1.2. Alle Versionen von Windows Server, die für Azure AD Connect V2.0 unterstützt werden, verwenden standardmäßig TLS 1.2. Wenn Ihr Server TLS 1.2 nicht unterstützt, müssen Sie dieses aktivieren, bevor Sie Azure AD Connect V2.0 bereitstellen können. Weitere Informationen finden Sie unter [Erzwingen von TLS 1.2 für Azure AD Connect](reference-connect-tls-enforcement.md).

### <a name="all-binaries-signed-with-sha2"></a>Alle mit SHA2 signierten Binärdateien 

Wir haben festgestellt, dass einige Komponenten des Secure-Hash-Algorithmus 1 signierte Binärdateien hatten. Der Secure-Hash-Algorithmus 1 wird für herunterladbare Binärdateien nicht mehr unterstützt, und alle Binärdateien wurden auf die SHA2-Signatur aktualisiert. Die digitalen Signaturen werden verwendet, um sicherzustellen, dass die Aktualisierungen direkt von Microsoft stammen und während der Übermittlung nicht manipuliert wurden. Aufgrund von Schwachstellen im SHA-1-Algorithmus und zur Ausrichtung auf Branchenstandards haben wir das Signieren von Windows-Aktualisierungen so geändert, dass der sicherere SHA-2-Algorithmus verwendet wird."  

Es ist keine Maßnahme von Ihrer Seite erforderlich. 

### <a name="windows-server-2012-and-windows-server-2012-r2-are-no-longer-supported"></a>Der Windows Server 2012 und der Windows Server 2012 R2 werden nicht mehr unterstützt 

Der SQL Server 2019 erfordert den Windows Server 2016 oder neuer als Serverbetriebssystem. Da AAD Connect v2 SQL Server 2019-Komponenten enthält, können ältere Versionen von Windows Server nicht mehr unterstützt werden.  

Sie können diese Version nicht auf einer älteren Windows Server Version installieren. Es empfohlen, dass Sie ihren Azure AD Connect-Server auf den Windows Server 2019 aktualisieren. Das ist die neueste Version des Windows Server-Betriebssystems. 

In diesem [Artikel](/windows-server/get-started-19/install-upgrade-migrate-19) wird das Upgrade von älteren Windows Server-Versionen auf den Windows Server 2019 beschrieben. 

### <a name="powershell-50"></a>PowerShell 5.0 

Dieses Release von Azure AD Connect enthält mehrere Cmdlets, die PowerShell 5.0 erfordern. Daher ist diese Anforderung eine neue Voraussetzung für Azure AD Connect.  

Weitere Informationen zu PowerShell-Voraussetzungen finden Sie [hier](/powershell/scripting/windows-powershell/install/windows-powershell-system-requirements#windows-powershell-50).

 >[!NOTE]
 >PowerShell 5 ist bereits ein Teil von Windows Server 2016, sodass Sie wahrscheinlich keine Maßnahmen ergreifen müssen, solange Sie eine aktuelle Windows Server-Version verwenden. 

## <a name="what-else-do-i-need-to-know"></a>Was muss ich außerdem noch beachten? 


**Warum ist dieses Upgrade für mich wichtig?** </br>
Im nächsten Jahr werden mehrere Komponenten in Ihren aktuellen Azure AD Connect-Serverinstallationen nicht mehr unterstützt. Wenn Sie nicht unterstützte Produkte verwenden, ist es für unser Supportteam schwieriger, Ihnen die Supporterfahrung zu bieten, die Ihr Unternehmen benötigt. Daher empfehlen wir allen Kunden, so schnell wie möglich ein Upgrade auf diese neuere Version zu durchführen. 

Dieses Upgrade ist besonders wichtig, da wir unsere Voraussetzungen für Azure AD Connect aktualisieren mussten und Sie möglicherweise zusätzliche Zeit benötigen, um Ihre Server auf die neueren Versionen dieser Voraussetzungen zu planen und zu aktualisieren 

**Gibt es neue Funktionen, die ich kennen muss?** </br>
Nein – Dieses Release enthält keine neue Funktionen. Dieses Release enthält nur Updates einiger der grundlegenden Komponenten auf Azure AD Connect. 

**Kann ich ein Upgrade von einer früheren Version auf V2.0 durchführen?** </br>
Ja – Upgrades von einer früheren Version von Azure AD Connect auf Azure AD Connect V2.0 werden unterstützt. Befolgen Sie den Leitfaden in [diesem Artikel](how-to-upgrade-previous-version.md), um zu ermitteln, welche Upgradestrategie für Sie die beste ist. 

**Kann ich die Konfiguration meines aktuellen Servers exportieren und in Azure AD Connect V2.0 importieren?** </br>
Ja, sie können dies tun, und es ist eine hervorragende Möglichkeit, zu Azure AD Connect V2.0 zu migrieren. Insbesondere, wenn Sie auch ein Upgrade auf eine neue Betriebssystemversion durchführen. Weitere Informationen zu der Import/Export-Konfigurationsfunktion und zur deren Verwendung finden Sie in diesem [Artikel](how-to-connect-import-export-config.md). 

**Ich habe das automatische Upgrade für Azure AD Connect aktiviert. Erhalte ich diese neue Version automatisch?** </br> Nein – Azure AD Connect V2.0 wird derzeit nicht für das automatische Upgrade verfügbar gemacht. 

**Ich bin noch nicht bereit für ein Upgrade – wie viel Zeit habe ich?** </br>
Sie sollten so schnell wie möglich auf Azure AD Connect V2.0 aktualisieren. **__Alle Azure AD Connect V1-Versionen werden am 31. August  2022 eingestellt.__** Vorerst werden wir weiterhin ältere Versionen von Azure AD Connect unterstützen, aber es kann sich schwierig gestalten, eine gute Supporterfahrung zu bieten, wenn einige Komponenten in Azure AD Connect nicht mehr unterstützt werden. Dieses Upgrade ist besonders wichtig für ADAL und TLS 1.0/1.1, da diese Dienste möglicherweise nicht mehr wie erwartet funktionieren, nachdem sie veraltet sind. 

**Ich verwende eine externe SQL-Datenbank und nicht SQL 2012 LocalDb – muss ich trotzdem ein Upgrade durchführen?** </br>
Ja, Sie müssen trotzdem ein Upgrade durchführen, um in einem unterstützten Zustand zu bleiben, auch wenn Sie SQL Server 2012 nicht verwenden, da TLS 1.0/1.1 und ADAL veraltet sind. Beachten Sie, dass SQL Server 2012 weiterhin als externe SQL-Datenbank mit Azure AD Connect V2.0 verwendet werden kann. Die SQL 2019-Treiber in Azure AD Connect V2.0 sind mit SQL Server 2012 kompatibel.

**Werden nach dem Upgrade meiner Azure AD Connect-Instanz auf V2.0 die SQL 2012-Komponenten automatisch deinstalliert?** </br>
Nein, durch das Upgrade auf SQL 2019 werden keine SQL 2012-Komponenten von Ihrem Server entfernt. Wenn Sie diese Komponenten nicht mehr benötigen, befolgen Sie [die Anweisungen zum Deinstallieren von SQL Server](/sql/sql-server/install/uninstall-an-existing-instance-of-sql-server-setup).

**Was geschieht, wenn ich kein Upgrade durchführe?** </br>
Bis eine der Komponenten, die zurückgezogen werden, tatsächlich veraltet ist, werden Sie keine Auswirkungen erfahren. Azure AD Connect wird weiterhin funktionieren. 

Wir gehen davon aus, dass TLS 1.0/1.1 im Januar 2022 veraltet ist. Sie müssen sicherstellen, dass Sie diese Protokolle ab diesem Datum nicht mehr verwenden, da Ihr Dienst dann möglicherweise nicht mehr wie erwartet funktioniert. Sie können Ihren Server jedoch manuell für TLS 1.2 konfigurieren, und dafür ist kein Update von Azure AD Connect V2.0 erforderlich 

Im Juni 2022 wird der Support für ADAL nicht mehr unterstützt. Wenn der Support für die ADAL beendet wird, funktioniert die Authentifizierung möglicherweise nicht mehr wie erwartet wodurch der Azure AD Connect-Server nicht mehr richtig funktioniert. Es wird dringend empfohlen vor Juni 2022, ein Upgrade auf Azure AD Connect V2.0 durchführen. Sie können kein Upgrade auf eine unterstützte Authentifizierungsbibliothek mit Ihrer aktuellen Azure AD Connect durchführen. 

**Nach dem Upgrade auf 2.0 funktionieren die ADSync PowerShell-Cmdlets nicht?** </br>
Dieses Problem ist bekannt.  Um dies zu beheben, starten Sie Ihre PowerShell-Sitzung nach der Installation oder dem Upgrade auf die Version 2.0 neu und importieren Sie das Modul dann erneut.  Verwenden Sie die folgenden Anweisungen, um das Modul zu importieren.
 
 1.  Öffnen Sie Windows PowerShell mit Administratorrechten.
 2.  Geben Sie den folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): 
    ``` powershell
              Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync"
    ```
 

## <a name="license-requirements-for-using-azure-ad-connect-v20"></a>Die Lizenzanforderungen für die Verwendung von Azure AD Connect V2.0

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Lizenzanforderungen für die Verwendung von Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md) 
- [Express-Einstellungen](how-to-connect-install-express.md)
- [Benutzerdefinierte Einstellungen](how-to-connect-install-custom.md)

In diesem Artikel wird das Upgrade von älteren Windows Server-Versionen auf den Windows Server 2019 beschrieben.
