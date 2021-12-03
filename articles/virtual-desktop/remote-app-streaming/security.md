---
title: Sicherheitsleitfaden für organisationsübergreifende Apps in Azure Virtual Desktop – Azure
description: Eine Anleitung, wie Sie die Apps, die Sie in Azure Virtual Desktop hosten, in mehreren Organisationen schützen können.
author: Heidilohr
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 3216a8f82bc066cded33f133d9fe8823484d50b0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463143"
---
# <a name="security-guidelines-for-cross-organizational-apps"></a>Sicherheitsrichtlinien für organisationsübergreifende Apps

Wenn Sie Apps in Azure Virtual Desktop hosten oder streamen, erreichen Sie eine Vielzahl von Benutzern innerhalb und außerhalb Ihrer Organisation. Daher ist es äußerst wichtig zu wissen, wie Sie die Bereitstellung sicher gestalten, damit Sie Ihre Organisation und Ihre Kunden entsprechend schützen können. In diesem Leitfaden werden Sie grundlegend mit den möglichen Sicherheitsbedenken vertraut gemacht und erfahren, wie Sie diese ausräumen können.

## <a name="shared-responsibility"></a>Gemeinsame Verantwortung

Vor Azure Virtual Desktop erforderten lokale Virtualisierungslösungen wie Remotedesktopdienste, dass Benutzern Zugriff auf Rollen wie Gateway, Broker, Webzugriff usw. gewährt wird. Diese Rollen mussten vollständig redundant und dazu in der Lage sein, Spitzenkapazitäten zu bewältigen. Administratoren installierten diese Rollen als Teil des Windows Server-Betriebssystems; zudem mussten sie mit bestimmten Ports in Domänen eingebunden werden, auf die über öffentliche Verbindungen zugegriffen werden konnte. Zur Gewährleistung der Sicherheit von Bereitstellungen mussten Administratoren kontinuierlich dafür sorgen, dass alle Komponenten der Infrastruktur gewartet und auf dem neuesten Stand waren.

Inzwischen werden Teile der Dienste im Auftrag des Kunden von Azure Virtual Desktop verwaltet. Insbesondere hostet und verwaltet Microsoft die Infrastrukturkomponenten als Teil des Diensts. Partner und Kunden müssen die erforderliche Infrastruktur nicht mehr manuell verwalten, damit Benutzer auf virtuelle Sitzungshostcomputer (VMs) zugreifen können. Der Dienst verfügt auch über integrierte erweiterte Sicherheitsfunktionen wie Reverse Connect. Dadurch wird das Risiko verringert, das sich ergibt, wenn Benutzer die Möglichkeit erhalten, standortunabhängig auf Remotedesktops zugreifen zu können.

Damit der Dienst flexibel bleibt, werden die Sitzungshosts im Azure-Abonnement des Partners oder Kunden gehostet. Dadurch können Kunden den Dienst mit anderen Azure-Diensten integrieren und eine Verbindung zwischen der lokalen Netzwerkinfrastruktur und ExpressRoute oder einem virtuellen privaten Netzwerk (Virtual Private Network, VPN) herstellen.

Wie bei vielen Clouddiensten gibt es [gemeinsame Sicherheitsaufgaben](../../security/fundamentals/shared-responsibility.md) zwischen Ihnen und Microsoft. Wenn Sie Azure Virtual Desktop verwenden, ist es wichtig zu verstehen, dass einige Teile des Diensts zwar für Sie geschützt sind, aber auch andere Teile des Diensts vorhanden sind, die Sie selbst gemäß den Sicherheitsanforderungen Ihrer Organisation konfigurieren müssen.

Sie müssen die Sicherheit in den folgenden Bereichen konfigurieren:

- Endbenutzergeräte
- Anwendungssicherheit
- Betriebssysteme von Sitzungshosts
- Bereitstellungskonfiguration
- Netzwerksteuerungen

Weitere Informationen zum Konfigurieren der einzelnen Bereiche finden Sie in unseren [bewährten Sicherheitsmethoden](./../security-guide.md).

## <a name="combined-microsoft-security-platform"></a>Kombinierte Microsoft-Sicherheitsplattform

Sie können Workloads schützen, indem Sie Sicherheitsfeatures und Steuerelemente aus Microsoft 365, Azure und Azure Virtual Desktop verwenden.

Wenn der Benutzer über das Internet eine Verbindung mit dem Dienst herstellt, werden von Azure Active Directory (Azure AD) die Anmeldeinformationen des Benutzers authentifiziert, sodass Schutzfunktionen wie [bedingter Zugriff](../../active-directory/conditional-access/overview.md) und [mehrstufige Authentifizierung](../../active-directory/authentication/concept-mfa-howitworks.md) aktiviert werden. Mit diesen Features wird das Risiko, dass Benutzeridentitäten kompromittiert werden, erheblich verringert.

Azure Virtual Desktop verfügt über Features wie [Reverse Connection](../network-connectivity.md#reverse-connect-transport), mit denen Benutzer auf den Sitzungshost zugreifen können, ohne eingehende Ports öffnen zu müssen. Dieses Feature wurde mit Blick auf Skalierbarkeit und Betrieb konzipiert und sollte daher auch nicht die Möglichkeit einschränken, Sitzungshosts zu erweitern. Sie können auch vorhandene Gruppenrichtlinienobjekte mit diesem Feature verwenden, um zusätzliche Sicherheit mit Unterstützung für mit Active Directory verknüpfte VMs oder, für Sitzungshosts unter Windows 10, die möglicherweise Azure Active Directory-Join-Szenarios beinhalten, für [Microsoft Endpoint Manager](/mem/intune/fundamentals/windows-virtual-desktop-multi-session) anzuwenden.

## <a name="defense-in-depth"></a>Tiefgehende Verteidigung

Die heutige Bedrohungslandschaft erfordert Entwürfe unter Berücksichtigung von Sicherheitsansätzen. Im Idealfall sollten Sie eine Reihe von Sicherheitsmechanismen und Steuerungen erstellen, die im gesamten Computernetzwerk verteilt sind, um Ihre Daten und das Netzwerk vor einer Kompromittierung oder vor Angriffen zu schützen. Diese Art von Sicherheitsentwurf wird von der US-Cybersicherheitsbehörde Cybersecurity and Infrastructure Security Agency (CISA) als „Defense in Depth“ (Verteidigung in der Tiefe) bezeichnet. Weitere Informationen zur „Defense in Depth“-Sicherheitsstrategie finden Sie auf der [CISA-Website](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth) (Englisch).

## <a name="session-host-operating-systems"></a>Betriebssysteme von Sitzungshosts

Azure Virtual Desktop unterstützt die folgenden Betriebssysteme:

- Windows 7
- Windows 10 Enterprise
- Windows Server 2012 R2, 2016, 2019 und 2022
- Windows 10 Enterprise (mehrere Sitzungen)

Windows 10 Enterprise Multi-Session ist ein exklusives Azure-Betriebssystem, das als Remotedesktop-Sitzungshost (RDSH) fungiert, der mehrere gleichzeitige interaktive Sitzungen zulässt, ähnlich wie Windows Server. Die Windows 10-Umgebung bietet Benutzern eine vertraute und konsistente Umgebung. Die pro Benutzer erfolgende Windows-Lizenzierung des Diensts ermöglicht auch Kostensenkungen für die gesamte Organisation, da die IT nicht weiterhin RDS-Clientzugriffslizenzen (Client Access Licenses, CALs) erwerben muss. Aufgrund dieser Vorteile ist Windows 10 Enterprise Multi-Session das am häufigsten bereitgestellte Betriebssystem für Sitzungshosts in Azure Virtual Desktop.

## <a name="security-boundaries"></a>Sicherheitsbegrenzungen

Sicherheitsgrenzen trennen den Code und die Daten von Sicherheitsdomänen mit unterschiedlichen Vertrauensebenen. Beispielsweise gibt es in der Regel eine Sicherheitsgrenze zwischen Kernelmodus und Benutzermodus. Die meisten Microsoft-Softwareanwendungen und -Dienste sind von mehreren Sicherheitsgrenzen abhängig, damit Geräte in Netzwerken, VMs und Anwendungen auf Geräten isoliert werden können. In der folgenden Tabelle sind die einzelnen Sicherheitsgrenzen für Windows und ihre jeweiligen Beiträge zur Gesamtsicherheit aufgeführt.

| Sicherheitsgrenze             | Zweck der Grenze      |
|-----------------------------------|--------------------------------------|
| Netzwerkbegrenzung              | Ein nicht autorisierter Netzwerkendpunkt kann nicht auf den Code und die Daten eines Kundengeräts zugreifen oder diese manipulieren.                                                                   |
| Kernelbegrenzung               | Ein nicht administrativer Benutzermodusprozess kann nicht auf den Kernelcode und die Daten zugreifen oder diese manipulieren. Administrator-to-Kernel ist keine Sicherheitsgrenze.                             |
| Prozessbegrenzung               | Ein nicht autorisierter Benutzermodusprozess kann nicht auf den Code und die Daten eines anderen Prozesses zugreifen oder diese manipulieren.                                                                      |
| AppContainer-Sandboxbegrenzung  | Ein auf AppContainer basierender Sandboxprozess kann nicht auf den Code und die Daten außerhalb der Sandbox, die auf den Containerfunktionen basiert, zugreifen oder diese manipulieren.                               |
| Benutzerbegrenzung                  | Ein Benutzer kann ohne entsprechende Autorisierung nicht auf den Code und die Daten eines anderen Benutzers zugreifen.                                                                           |
| Sitzungsbegrenzung               | Eine Benutzersitzung kann ohne entsprechende Autorisierung nicht auf eine andere Benutzersitzung zugreifen oder diese manipulieren.                                                                    |
| Webbrowserbegrenzung           | Eine nicht autorisierte Website kann weder gegen eine Richtlinie desselben Ursprungs verstoßen noch auf den nativen Code und die Daten der Microsoft Edge-Webbrowsersandbox zugreifen oder diese manipulieren.       |
| Begrenzung virtueller Computer       | Ein nicht autorisierter virtueller Hyper-V-Gastcomputer kann nicht auf den Code und die Daten eines anderen virtuellen Gastcomputers zugreifen oder diese manipulieren. Dies gilt auch für isolierte Hyper-V-Container. |
| Grenze für den virtuellen sicheren Modus (Virtual Secure Mode, VSM)  | Code, der außerhalb des vertrauenswürdigen VSM-Prozesses oder des entsprechend geschützten Bereichs ausgeführt wird, kann nicht auf Daten und Code innerhalb des vertrauenswürdigen Prozesses zugreifen oder diese manipulieren.                              |

## <a name="security-features"></a>Sicherheitsfeatures

Sicherheitsfeatures bauen auf Sicherheitsgrenzen auf, sodass der Schutz vor bestimmten Bedrohungen gestärkt wird. In einigen Fällen kann es jedoch entwurfsbedingte Einschränkungen geben, die verhindern, dass das Sicherheitsfeature die Bereitstellung vollständig schützt.

Azure Virtual Desktop unterstützt die meisten Sicherheitsfeatures, die in Windows verfügbar sind. Sicherheitsfeatures, die auf Hardwarefeatures wie (v)TPM oder geschachtelter Virtualisierung basieren, erfordern möglicherweise eine separate Supportanweisung vom Azure Virtual Desktop-Team.

Weitere Informationen zur Unterstützung und Wartung von Sicherheitsfeatures finden Sie in den [Kriterien für die Sicherheitswartung von Microsoft für Windows](https://www.microsoft.com/msrc/windows-security-servicing-criteria).

## <a name="recommended-security-boundaries-for-azure-virtual-desktop-scenarios"></a>Empfohlene Sicherheitsgrenzen für Azure Virtual Desktop-Szenarios

Sie müssen auch von Fall zu Fall bestimmte Entscheidungen zu Sicherheitsgrenzen treffen. Wenn ein Benutzer in Ihrer Organisation beispielsweise lokale Administratorrechte zum Installieren von Anwendungen benötigt, müssen Sie diesem einen persönlichen Desktop anstelle eines freigegebenen Remotedesktop-Sitzungshosts verfügbar machen. Es wird nicht empfohlen, Benutzern lokale Administratorberechtigungen in Szenarios mit einem Pool für mehrere Sitzungen zu erteilen, da diese Benutzer Sicherheitsgrenzen für Sitzungen oder NTFS-Datenberechtigungen überschreiten, VMs mit mehreren Sitzungen herunterfahren oder andere Vorgänge ausführen können, die den Dienst unterbrechen oder Datenverluste verursachen könnten.

Benutzer aus derselben Organisation, z. B. Wissensarbeiter mit Anwendungen, die keine Administratorrechte benötigen, sind hervorragende Kandidaten für Remotedesktop-Sitzungshosts mit mehreren Sitzungen, etwa unter Windows 10 Enterprise Multi-Session. Diese Sitzungshosts ermöglichen Kostensenkungen für Ihre Organisation, da mehrere Benutzer einen einzelnen virtuellen Computer gemeinsam nutzen können. Dabei fallen nur die Gemeinkosten eines einzelnen Betriebssystems an. Mit Profiltechnologie wie FSLogix können Benutzer jedem virtuellen Computer in einem Hostpool zugewiesen werden, ohne dass Dienstunterbrechungen auftreten. Mit diesem Feature können Sie auch die Kosten optimieren, indem Sie beispielsweise VMs außerhalb der Spitzenzeiten herunterfahren.

Wenn Ihre Situation erfordert, dass Benutzer aus verschiedenen Organisationen eine Verbindung mit Ihrer Bereitstellung herstellen, wird empfohlen, einen separaten Mandanten für Identitätsdienste wie Active Directory und Azure AD zu verwenden. Außerdem wird empfohlen, ein separates Abonnement zum Hosten von Azure-Ressourcen wie Azure Virtual Desktop und VMs zu verwenden.

In der folgenden Tabelle sind Empfehlungen für jedes Szenario aufgeführt.

| Szenario mit Vertrauensebene                                 | Empfohlene Lösung                |
|------------------------------------------------------|-------------------------------------|
| Benutzer aus einer einzigen Organisation mit Standardberechtigungen | Windows 10 Enterprise (mehrere Sitzungen) |
| Benutzer benötigen Administratorrechte.             | Persönliche Desktops (VDI)             |
| Benutzer aus verschiedenen Organisationen, die eine Verbindung herstellen        | Separates Azure-Abonnement         |

Nachstehend werden die genannten Empfehlungen für einige Beispielszenarios erläutert.

### <a name="should-i-share-identity-resources-to-reduce-costs"></a>Sollte ich Identitätsressourcen freigeben, um Kosten zu senken?

Es wird derzeit nicht empfohlen, in Azure Virtual Desktop ein System für gemeinsame Identitäten zu verwenden. Es wird empfohlen, separate Identitätsressourcen zu verwenden, die Sie in einem separaten Azure-Abonnement bereitstellen. Zu diesen Ressourcen gehören Active Directory-, Azure AD- und VM-Workloads. Jeder Benutzer, der für eine einzelne Organisation arbeitet, verursacht zusätzliche Infrastrukturkosten und zugehörige Wartungskosten, aber dies ist derzeit aus Sicherheitsgründen die optimale Lösung. 

### <a name="should-i-share-a-multi-session-remote-desktop-rd-session-host-vm-to-reduce-costs"></a>Sollte ich einen virtuellen Computer eines Remotedesktop(RD)-Sitzungshosts mit mehreren Sitzungen freigeben, um Kosten zu senken?

Mithilfe von RD-Sitzungshosts mit mehreren Sitzungen können Sie Kosteneinsparungen erzielen, indem Sie Hardwareressourcen wie CPU und Arbeitsspeicher zwischen Benutzern gemeinsam nutzen. Mit dieser Ressourcenfreigabe können Sie die Planung auf die maximale Kapazität auslegen, auch wenn es unwahrscheinlich ist, dass alle Benutzer gleichzeitig die maximale Anzahl von Ressourcen benötigen. In einer gemeinsam genutzten Umgebung mit mehreren Sitzungen werden Hardwareressourcen gemeinsam genutzt und zugeordnet, sodass die Lücke zwischen Nutzung und Kosten verkleinert wird.

In vielen Fällen ist die Verwendung mehrerer Sitzungen eine akzeptable Möglichkeit zur Kostensenkung, aber ob sie empfohlen werden kann, hängt von der Vertrauensebene zwischen Benutzern mit gleichzeitigem Zugriff auf eine gemeinsam genutzte Instanz mit mehreren Sitzungen ab. In der Regel verfügen Benutzer, die derselben Organisation angehören, über eine ausreichende und entsprechend vereinbarte Vertrauensstellung. Beispielsweise ist eine Abteilung oder Arbeitsgruppe, in der Personen zusammenarbeiten und auf die persönlichen Informationen des jeweils anderen zugreifen können, eine Organisation mit hoher Vertrauensebene.

Windows verwendet Sicherheitsgrenzen und Steuerelemente, um sicherzustellen, dass Benutzerprozesse und Daten zwischen Sitzungen isoliert werden. Windows bietet jedoch weiterhin Zugriff auf die Instanz, in der der Benutzer arbeitet.

Diese Bereitstellung würde von einer Security-in-Depth-Strategie (Sicherheit in der Tiefe) profitieren, bei der mehr Sicherheitsgrenzen hinzugefügt werden, die verhindern, dass Benutzer innerhalb und außerhalb der Organisation nicht autorisierten Zugriff auf die persönlichen Informationen anderer Benutzer erhalten. Nicht autorisierter Datenzugriff erfolgt aufgrund eines Fehlers im Konfigurationsprozess des Systemadministrators, z. B. eines nicht offengelegten oder eines bekannten Sicherheitsrisikos, das noch nicht gepatcht wurde.

Andererseits bietet Microsoft keine Unterstützung dafür, Benutzern, die für verschiedene oder konkurrierende Unternehmen arbeiten, Zugriff auf dieselbe Umgebung mit mehreren Sitzungen zu gewähren. Diese Szenarios weisen mehrere Sicherheitsgrenzen auf, die angegriffen oder missbräuchlich verwendet werden können, z. B. Netzwerk, Kernel, Prozess, Benutzer oder Sitzungen. Ein einzelnes Sicherheitsrisiko kann zu nicht autorisiertem Daten- und Anmeldeinformationsdiebstahl, Verlusten personenbezogener Informationen, Identitätsdiebstahl und anderen Problemen führen. Anbieter von virtualisierten Umgebungen sind dafür verantwortlich, gut entworfene Systeme mit mehreren starken Sicherheitsgrenzen und nach Möglichkeit aktivierten zusätzlichen Sicherheitsfeatures verfügbar zu machen.

Die Reduzierung dieser potenziellen Bedrohungen erfordert eine fehlersichere Konfiguration, einen Entwurfsprozess für die Patchverwaltung und Zeitpläne für die regelmäßige Patchbereitstellung. Es ist besser, die Defense-in-Depth-Prinzipien zu befolgen und Umgebungen getrennt zu halten.

## <a name="next-steps"></a>Nächste Schritte

Unsere empfohlenen Richtlinien zum Konfigurieren der Sicherheit für Ihre Azure Virtual Desktop-Bereitstellung finden Sie unter [Bewährte Sicherheitsmethoden](./../security-guide.md).