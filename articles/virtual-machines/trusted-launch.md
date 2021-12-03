---
title: Vertrauenswürdiger Start für Azure-VMs
description: Informationen zum vertrauenswürdigen Start für Azure-VMs.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 10/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 0db7b5a92820e299658d793e66edba1e6e84c087
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281455"
---
# <a name="trusted-launch-for-azure-virtual-machines"></a>Vertrauenswürdiger Start für Azure-VMs

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen

Azure bietet den vertrauenswürdigen Start als nahtlose Möglichkeit zur Verbesserung der Sicherheit von VMs der [Generation 2](generation-2.md) an. Der vertrauenswürdige Start bietet Schutz vor komplexen und permanenten Angriffstechniken. Der vertrauenswürdige Start besteht aus mehreren koordinierten Infrastrukturtechnologien, die unabhängig voneinander aktiviert werden können. Jede Technologie bietet eine eigene Schutzschicht gegen komplexe Bedrohungen.

> [!IMPORTANT]
> Der vertrauenswürdige Start erfordert die Erstellung neuer VMs. Sie können den vertrauenswürdigen Start nicht für vorhandene VMs aktivieren, die ursprünglich ohne vertrauenswürdigen Start erstellt wurden.



## <a name="benefits"></a>Vorteile 

- Sichere Bereitstellung von VMs mit überprüften Startladeprogrammen, Betriebssystemkernels und Treibern
- Sicherer Schutz von Schlüsseln, Zertifikaten und Geheimnissen auf den VMs
- Einblicke und Vertrauen in die Integrität der gesamten Startkette
- Sicherstellen der Vertrauenswürdigkeit und Überprüfbarkeit von Workloads

## <a name="limitations"></a>Einschränkungen

**Unterstützte VM-Größen:**
- B-Serie
- Dav4-, Dasv4-Serie
- DCsv2-Serie
- Dv4-, Dsv4-, Dsv3-, Dsv2-Serie
- Ddv4-, Ddsv4-Serie
- Fsv2-Serie
- Eav4-, Easv4-Serie
- Ev4-, Esv4-, Esv3-Serie
- Edv4-, Edsv4-Serie
- Lsv2-Reihe

**Betriebssystemunterstützung:**
- Red Hat Enterprise Linux 8.3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Debian 11
- CentOS 8.4
- Oracle Linux 8.3
- CBL-Mariner
- Windows Server 2022
- Windows Server 2019
- Windows Server 2016
- Windows 11 Pro
- Windows 11 Enterprise
- Windows 11 Enterprise (mehrere Sitzungen)
- Windows 10 Pro
- Windows 10 Enterprise
- Windows 10 Enterprise (mehrere Sitzungen)

**Regionen**: 
- Alle öffentlichen Regionen

**Preise:** keine zusätzlichen Kosten zu den bestehenden VM-Preisen.

**Folgende Funktionen werden nicht unterstützt**:
- Backup
- Azure Site Recovery
- Azure Compute Gallery (früher Shared Image Gallery genannt)
- Kurzlebiger Betriebssystemdatenträger
- Freigegebener Datenträger
- Ultra-Datenträger
- Verwaltetes Image
- Azure Dedicated Host 
- Geschachtelte Virtualisierung

## <a name="secure-boot"></a>Sicherer Start

Das Fundament des vertrauenswürdigen Starts bildet der VM-Modus „Sicherer Start“. Dieser Modus, der in der Plattformfirmware implementiert ist, schützt vor der Installation schadsoftwarebasierter Rootkits und Bootkits. Der sichere Start bewirkt, dass nur signierte Betriebssysteme und Treiber gestartet werden können. Damit wird ein Vertrauensanker für den Softwarestapel der VM erstellt. Bei aktiviertem sicherem Start müssen alle Betriebssystem-Startkomponenten (Startladeprogramm, Kernel, Kerneltreiber) von vertrauenswürdigen Herausgebern signiert werden. Der sichere Start wird unter Windows sowie ausgewählten Linux-Distributionen unterstützt. Wenn beim sicheren Start nicht authentifiziert werden kann, dass das Image von einem vertrauenswürdigen Herausgeber signiert wurde, kann die VM nicht gestartet werden. Weitere Informationen finden Sie unter [Sicherer Start](/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

Der vertrauenswürdige Start umfasst auch vTPM für Azure-VMs. Dabei handelt es sich um eine virtualisierte mit der TPM 2.0-Spezifikation konforme Version von TPM-Hardware ([Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview)). Sie dient als dedizierter sicherer Tresor für Schlüssel und Messungen. Der vertrauenswürdige Start bietet für die VM eine eigene dedizierte TPM-Instanz, die in einer sicheren Umgebung außerhalb der Reichweite von VMs ausgeführt wird. vTPM ermöglicht den [Nachweis](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) durch Messung der gesamten Startkette der VM (UEFI, Betriebssystem, System und Treiber). 

Beim vertrauenswürdigen Start wird vTPM verwendet, um einen Remotenachweis in der Cloud durchzuführen. Dies wird für Plattformintegritätsprüfungen und vertrauensbasierte Entscheidungsprozesse verwendet. Als Integritätsprüfung kann beim vertrauenswürdigen Start kryptografisch zertifiziert werden, dass die VM ordnungsgemäß gestartet wurde. Wenn bei diesem Prozess Fehler auftreten, z. B. da eventuell auf dem virtuellen Computer eine nicht autorisierte Komponente ausgeführt wird, werden in Microsoft Defender für Cloud Integritätswarnungen ausgegeben. Die Warnungen enthalten Details zu den Komponenten, bei denen die Integritätsprüfungen nicht erfolgreich durchgeführt wurden.

## <a name="virtualization-based-security"></a>Virtualisierungsbasierte Sicherheit

Für die [virtualisierungsbasierte Sicherheit](/windows-hardware/design/device-experiences/oem-vbs) (VBS) wird mit dem Hypervisor ein sicherer und isolierter Speicherbereich erstellt. Unter Windows werden in diesen Bereichen verschiedene Sicherheitslösungen mit erhöhtem Schutz vor Sicherheitsrisiken und schädlichen Exploits ausgeführt. Mit dem vertrauenswürdigen Start können Sie Hypervisor Code Integrity (HVCI) und Windows Defender Credential Guard aktivieren.

HVCI bietet eine leistungsstarke Risikominderung für das System und schützt Windows-Kernelmodusprozesse vor der Einschleusung und Ausführung von schädlichem oder nicht überprüftem Code. Kernelmodustreiber und -binärdateien werden vor der Ausführung überprüft, sodass nicht signierte Dateien nicht in den Speicher geladen werden können. Dadurch wird sichergestellt, dass ausführbarer Code nicht mehr geändert werden kann, nachdem er geladen wurde. Weitere Informationen zu VBS und HVCI finden Sie unter [Virtualization Based Security (VBS) and Hypervisor Enforced Code Integrity (HVCI)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571) (Virtualisierungsbasierte Sicherheit (VBS) und Hypervisor Enforced Code Integrity (HVCI)).

Mit dem vertrauenswürdigen Start und VBS können Sie Windows Defender Credential Guard aktivieren. Mit diesem Feature werden Geheimnisse isoliert und geschützt, sodass der Zugriff darauf nur über privilegierte Systemsoftware erfolgen kann. Dadurch können der nicht autorisierte Zugriff auf Geheimnisse und Angriffe zum Diebstahl von Anmeldeinformationen, z. B. Pass-the-Hash-Angriffe (PtH-Angriffe), verhindert werden. Weitere Informationen finden Sie unter [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="defender-for-cloud-integration"></a>Integration bei Defender für Cloud

Der vertrauenswürdige Start ist in Microsoft Defender für Cloud integriert, um sicherzustellen, dass Ihre virtuellen Computer ordnungsgemäß konfiguriert sind. In Microsoft Defender für Cloud werden kompatible virtuelle Computer kontinuierlich bewertet und relevante Empfehlungen ausgegeben.

- **Empfehlung zum Aktivieren des sicheren Starts**: Diese Empfehlung gilt nur für virtuelle Computer, die den vertrauenswürdigen Start unterstützen. In Microsoft Defender für Cloud werden virtuelle Computer identifiziert, auf denen der sichere Start aktiviert werden kann, dieser jedoch deaktiviert ist. Es wird eine Empfehlung mit niedrigem Schweregrad für die Aktivierung ausgegeben.
- **Empfehlung zum Aktivieren von vTPM:** Wenn auf Ihrem virtuellen Computer vTPM aktiviert ist, können in Microsoft Defender für Cloud Nachweisvorgänge für Gäste durchgeführt und komplexe Bedrohungsmuster identifiziert werden. Wenn in Microsoft Defender für Cloud virtuelle Computer identifiziert werden, auf denen der vertrauenswürdige Start unterstützt wird und vTPM deaktiviert ist, wird eine Empfehlung von geringer Wichtigkeit für die Aktivierung abgegeben. 
- **Empfehlung zum Installieren der Gastnachweiserweiterung:** Wenn auf Ihrem virtuellen Computer der sichere Start und vTPM aktiviert ist, aber die Gastnachweiserweiterung nicht installiert ist, gibt Microsoft Defender für Cloud eine Empfehlung von geringer Wichtigkeit zum Installieren der Gastnachweiserweiterung ab. Mit dieser Erweiterung kann Microsoft Defender für Cloud die Startintegrität Ihres virtuellen Computers proaktiv bestätigen und überwachen. Der Nachweis der Startintegrität erfolgt per Remotenachweis.  
- **Bewertung des Integritätsnachweises:** Wenn auf Ihrem virtuellen Computer vTPM aktiviert und eine Nachweiserweiterung installiert ist, kann Microsoft Defender für Cloud aus der Ferne (remote) überprüfen, ob Ihr virtueller Computer fehlerfrei gestartet wurde. Dies wird als Remotenachweis bezeichnet. Microsoft Defender für Cloud gibt eine Bewertung ab, die den Status des Remotenachweises angibt.

## <a name="microsoft-defender-for-cloud-integration"></a>Integration bei Microsoft Defender für Cloud

Wenn Ihre virtuellen Computer ordnungsgemäß in puncto vertrauenswürdiger Start eingerichtet sind, kann Microsoft Defender für Cloud Integritätsprobleme eines virtuellen Computers erkennen und entsprechende Warnungen ausgeben.

- **Warnung bei Nachweisfehlern virtueller Computer:** Microsoft Defender für Cloud führt in regelmäßigen Abständen Nachweisvorgänge für Ihre virtuellen Computer durch. Dies erfolgt auch nach dem Start von VMs. Wenn beim Nachweis Fehler auftreten, wird eine Warnung mit mittlerem Schweregrad ausgelöst.
    Beim VM-Nachweis können aus folgenden Gründen Fehler auftreten:
    - Die nachgewiesenen Informationen, einschließlich eines Startprotokolls, weichen von einer vertrauenswürdigen Baseline ab. Dies kann darauf hinweisen, dass nicht vertrauenswürdige Module geladen wurden und das Betriebssystem möglicherweise kompromittiert ist.
    - Es konnte nicht überprüft werden, ob das Nachweisangebot von der vTPM-Instanz der VM stammt, für die der Nachweis erstellt wurde. Dies kann darauf hinweisen, dass Schadsoftware vorhanden ist und Datenverkehr an die vTPM-Instanz abgefangen wird.
    
    > [!NOTE]
    >  Diese Warnung ist für VMs mit aktivierter vTPM-Instanz und installierter Nachweiserweiterung verfügbar. Der sichere Start muss aktiviert sein, damit der Nachweisvorgang erfolgreich durchgeführt wird. Beim Nachweisvorgang treten Fehler auf, wenn der sichere Start deaktiviert ist. Wenn Sie den sicheren Start deaktivieren möchten, können Sie diese Warnung unterdrücken, um False Positives zu vermeiden.

- **Warnung bei nicht vertrauenswürdigem Linux-Kernelmodul:** Beim vertrauenswürdigen Start mit aktiviertem sicherem Start ist es möglich, dass ein virtueller Computer gestartet wird, obwohl bei der Überprüfung eines Kerneltreibers Fehler auftreten sind und das Laden nicht zulässig ist. In diesem Fall gibt Microsoft Defender für Cloud eine Warnung von geringer Wichtigkeit aus. Es liegt zwar keine unmittelbare Bedrohung vor, da der nicht vertrauenswürdige Treiber nicht geladen wurde, dennoch sollten diese Ereignisse untersucht werden. Beachten Sie Folgendes:
    - Bei welchem Kerneltreiber sind Fehler aufgetreten? Kennen Sie diesen Treiber, und erwarten Sie, dass er geladen wird?
    - Handelt es sich um die richtige Version des erwarteten Treibers? Sind die Binärdateien des Treibers intakt? Wenn es sich um einen Treiber eines Drittanbieters handelt, wurden für den Hersteller beim Signieren des Treibers die Kompatibilitätstests für das Betriebssystem erfolgreich durchgeführt?


## <a name="faq"></a>Häufig gestellte Fragen

Häufig gestellte Fragen zum vertrauenswürdigen Start

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Warum sollte der vertrauenswürdige Start verwendet werden? Worin besteht der Schutz des vertrauenswürdigen Starts?

Der vertrauenswürdige Start bietet Schutz vor Bootkits, Rootkits und Schadsoftware auf Kernelebene. Diese komplexen Schadsoftwaretypen werden im Kernelmodus ausgeführt und bleiben für Benutzer verborgen. Beispiel:
- Firmwarerootkits: Diese Kits überschreiben die Firmware des BIOS der VM, sodass das Rootkit vor dem Betriebssystem gestartet werden kann. 
- Bootkits: Diese Kits ersetzen den Bootloader des Betriebssystems, sodass die VM das Bootkit vor dem Betriebssystem lädt.
- Kernelrootkits: Diese Kits ersetzen einen Teil des Betriebssystemkernels, sodass das Rootkit automatisch gestartet werden kann, wenn das Betriebssystem geladen wird.
- Treiberrootkits: Diese Kits geben vor, einer der vertrauenswürdigen Treiber zu sein, die im Betriebssystem zur Kommunikation mit den Komponenten der VM verwendet werden.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Worin bestehen die Unterschiede zwischen dem sicheren Start und dem kontrollierten Start?

In der sicheren Startkette wird in jedem Schritt im Startprozess eine kryptografische Signatur der nachfolgenden Schritte geprüft. Das BIOS prüft z. B. eine Signatur für das Ladeprogramm, das Ladeprogramm prüft die Signaturen für alle Kernelobjekte, die geladen werden, usw. Wenn eines der Objekte kompromittiert ist, stimmt die Signatur nicht überein und die VM wird nicht gestartet. Weitere Informationen finden Sie unter [Sicherer Start](/windows-hardware/design/device-experiences/oem-secure-boot). Beim kontrollierten Start wird der Startvorgang nicht unterbrochen, sondern jeweils der Hash der nächsten Objekte in der Kette gemessen oder berechnet. Die Hashwerte werden in den PCRs (Platform Configuration Registers) in der vTPM-Instanz gespeichert. Die Datensätze des kontrollierten Starts werden für die Überwachung der Startintegrität verwendet.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Was geschieht, wenn ein Integritätsfehler erkannt wird?

Der vertrauenswürdige Start für Azure-VMs wird für komplexe Bedrohungen überwacht. Wenn solche Bedrohungen erkannt werden, wird eine Warnung ausgelöst. Warnungen sind nur verfügbar, wenn [die erweiterten Sicherheitsfunktionen von Defender für Cloud](../security-center/enable-enhanced-security.md) aktiviert sind.

Defender für Cloud führt in regelmäßigen Abständen einen Nachweis durch. Wenn dabei Fehler auftreten, wird eine Warnung mit mittlerem Schweregrad ausgelöst. Bei Nachweisvorgängen beim vertrauenswürdigen Start können aus folgenden Gründen Fehler auftreten:

- Die nachgewiesenen Informationen, einschließlich eines Protokolls der vertrauenswürdigen Computingbasis (Trusted Computing Base, TCB), weichen von einer vertrauenswürdigen Baseline ab (z. B. wenn der sichere Start aktiviert ist). Dies kann darauf hinweisen, dass nicht vertrauenswürdige Module geladen wurden und das Betriebssystem möglicherweise kompromittiert ist.
- Es konnte nicht überprüft werden, ob das Nachweisangebot von der vTPM-Instanz der VM stammt, für die der Nachweis erstellt wurde. Dies kann darauf hinweisen, dass Schadsoftware vorhanden ist und Datenverkehr an die TPM-Instanz abgefangen wird.
- Die Nachweiserweiterung auf der VM reagiert nicht. Dies kann auf einen Denial-of-Service-Angriff durch Schadsoftware oder einen Betriebssystemadministrator hindeuten.

### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Wie unterscheidet sich der vertrauenswürdige Start von einer abgeschirmten Hyper-V-VM?

Abgeschirmte Hyper-V-VMs sind derzeit nur für Hyper-V verfügbar. [Abgeschirmte Hyper-V-VMs](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) werden normalerweise in Verbindung mit einem geschützten Fabric bereitgestellt. Ein geschütztes Fabric besteht aus einem Hostüberwachungsdienst (Host Guardian Service, HGS), einem oder mehreren geschützten Hosts und einer Gruppe von abgeschirmten VMs. Abgeschirmte Hyper-V-VMs sind zur Verwendung in Fabrics vorgesehen, in denen die Daten und der Zustand der VM vor Fabricadministratoren sowie vor nicht vertrauenswürdiger Software geschützt werden müssen, die möglicherweise auf den Hyper-V-Hosts ausgeführt wird. Der vertrauenswürdige Start kann dagegen in Azure als eigenständige VM oder als VM-Skalierungsgruppe ohne zusätzliche Bereitstellung und Verwaltung von HGS bereitgestellt werden. Alle Funktionen des vertrauenswürdigen Starts können mit einer einfachen Änderung im Bereitstellungscode oder einem Kontrollkästchen im Azure-Portal aktiviert werden.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Wie kann ich vorhandene VMs auf den vertrauenswürdigen Start umstellen?

Für VMs der Generation 2 ist der Migrationspfad zur Konvertierung in den vertrauenswürdigen Start nach der allgemeinen Verfügbarkeit vorgesehen.

### <a name="what-is-vm-guest-state-vmgs"></a>Was ist der VM-Gastzustand (VMGS)?  

Der VM-Gastzustand (VMGS) ist für VMs mit vertrauenswürdigem Start spezifisch. Es handelt sich um ein von Azure verwaltetes Blob, das die UEFI-Datenbanken (Unified Extensible Firmware Interface) für sichere Startsignaturen und andere Sicherheitsinformationen enthält. Der Lebenszyklus des VMGS-Blobs ist an den Lebenszyklus des Betriebssystemdatenträgers gebunden.  

## <a name="next-steps"></a>Nächste Schritte

Bereitstellen einer [VM mit aktiviertem vertrauenswürdigen Start über das Portal](trusted-launch-portal.md)
