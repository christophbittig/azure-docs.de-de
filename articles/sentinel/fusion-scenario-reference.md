---
title: Vom Microsoft Sentinel Fusion Modul erkannte Szenarien
description: Erfahren Sie mehr über die von Fusion erkannten Szenarien, die hier gruppiert nach Bedrohungsklassifizierung aufgeführt sind.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b5c03e94dfd1bea5453a449d7c6ae045371150b7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523541"
---
# <a name="scenarios-detected-by-the-microsoft-sentinel-fusion-engine"></a>Vom Microsoft Sentinel Fusion Modul erkannte Szenarien

In diesem Dokument werden die Arten von szenariobasierten mehrstufigen Angriffen nach Bedrohungsklassifizierung aufgelistet, die Microsoft Sentinel mit dem Fusion-Korrelations-Modul erkennt.

Da [Fusion](fusion.md) mehrere Signale von verschiedenen Produkten korreliert, um erweiterte mehrstufige Angriffe zu erkennen, werden erfolgreiche Erkennungen von Fusion als **Fusion-Vorfälle** auf der Seite Microsoft Sentinel **Vorfälle** und nicht als **Warnungen** angezeigt und in der Tabelle *Vorfälle* in **Protokollen** und nicht in der Tabelle *Sicherheitswarnungen* gespeichert.

Um diese von Fusion gestützten Angriffserkennungsszenarien zu ermöglichen, müssen alle aufgeführten Datenquellen in Ihrem Log Analytics-Arbeitsbereich erfasst werden.

> [!NOTE]
> Einige dieser Szenarios befinden sich in der **VORSCHAU**. Diese sind entsprechend gekennzeichnet.


## <a name="compute-resource-abuse"></a>Missbrauch von Computeressourcen

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Mehrere Aktivitäten zur Erstellung von VMs im Anschluss an verdächtige Anmeldungen bei Azure Active Directory
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Beeinträchtigung 

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Ressourcen-Hijacking (T1496)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass virtuelle Computer in ungewöhnlicher Anzahl in einer einzelnen Sitzung im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto erstellt wurden. Diese Art von Benachrichtigung zeigt mit einem hohen Grad an Vertrauenswürdigkeit an, dass das in der Fusion-Vorfallsbeschreibung genannte Konto beschädigt und zum Erstellen neuer VMs für nicht autorisierte Zwecke verwendet wurde, etwa zum Ausführen von Vorgängen zum Kryptografiemining. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung über mehrere Aktivitäten zur VM-Erstellung lauten:

- **Unmöglicher Ortswechsel an einen atypischen Speicherort führt zu mehreren Aktivitäten zur VM-Erstellung**

- **Anmeldeereignis von einem unbekannten Ort führt zu mehreren Aktivitäten zur VM-Erstellung**

- **Anmeldeereignis von einem infizierten Gerät führt zu mehreren Aktivitäten zur VM-Erstellung**

- **Anmeldeereignis von einer anonymen IP-Adresse führt zu mehreren Aktivitäten zur VM-Erstellung**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen führt zu mehreren Aktivitäten zur VM-Erstellung**


## <a name="credential-access"></a>Zugriff über Anmeldeinformationen
(Neue Bedrohungsklassifizierung)

### <a name="multiple-passwords-reset-by-user-following-suspicious-sign-in"></a>Zurücksetzen mehrerer Kennwörter durch Benutzer nach verdächtiger Anmeldung
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Zugriff auf Anmeldeinformationen

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Brute Force (T1110)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Azure Active Directory Identity Protection

**Beschreibung**: Fusion-Vorfälle dieser Art deuten darauf hin, dass ein Benutzer nach einer verdächtigen Anmeldung bei einem Azure AD-Konto mehrere Kennwörter zurückgesetzt hat. Dies ist ein Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto kompromittiert und dazu verwendet wurde, mehrere Kennwörter zurückzusetzen, um Zugriff auf mehrere Systeme und Ressourcen zu erhalten. Die Manipulation von Konten (einschließlich Zurücksetzung von Kennwörtern) kann Angreifern dabei helfen, den Zugriff auf Anmeldeinformationen und bestimmte Berechtigungsebenen innerhalb einer Umgebung aufrechtzuerhalten. Die Permutationen von Warnungen zu verdächtigen Azure AD-Anmeldungen mit Warnungen zum Zurücksetzen mehrerer Kennwörter sind wie folgt:

- **Unmöglicher Ortswechsel an einen atypischen Ort, der zu mehreren Kennwortzurücksetzungen führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu mehreren Kennwortzurücksetzungen führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu mehreren Kennwortzurücksetzungen führt**

- **Anmeldeereignis mit einer anonymen IP-Adresse, das zu mehreren Kennwortzurücksetzungen führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu mehreren Kennwortzurücksetzungen führt**

### <a name="suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>Verdächtige Anmeldung, die mit einer erfolgreichen Anmeldung bei Palo Alto VPN über IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen einhergeht
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Zugriff auf Anmeldeinformationen

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Brute Force (T1110)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieser Art deuten darauf hin, dass eine verdächtige Anmeldung bei einem Azure AD-Konto mit einer erfolgreichen Anmeldung über ein Palo Alto VPN über eine IP-Adresse einherging, von der aus mehrere fehlgeschlagene Azure AD-Anmeldungen in einem ähnlichen Zeitrahmen erfolgt sind. Obwohl dies kein Hinweis auf einen mehrstufigen Angriff ist, ergibt die Korrelation dieser beiden Warnungen mit geringerer Zuverlässigkeit einen Vorfall mit hoher Zuverlässigkeit, der auf einen ersten böswilligen Zugriff auf das Netzwerk der Organisation schließen lässt. Alternativ kann dies ein Hinweis darauf sein, dass ein Angreifer versucht, sich mit Brute-Force-Techniken Zugriff auf ein Azure AD-Konto zu verschaffen. Die Permutationen verdächtiger Azure AD-Anmeldewarnungen mit Warnungen des Typs „IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen meldet sich erfolgreich bei Palo Alto VPN an“ sind wie folgt:

- **Unmöglicher Ortswechsel an einen atypischen Ort, der mit einer IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen einhergeht, die sich erfolgreich bei Palo Alto VPN anmeldet**

- **Anmeldeereignis von einem nicht vertrauten Ort aus, das mit einer IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen einhergeht, die sich erfolgreich bei Palo Alto VPN anmeldet**

- **Anmeldeereignis von einem infizierten Gerät aus, das mit einer IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen einhergeht, die sich erfolgreich bei Palo Alto VPN anmeldet**

- **Anmeldeereignis mit einer anonymen IP-Adresse, die mit einer IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen einhergeht, die sich erfolgreich bei Palo Alto VPN anmeldet**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das mit einer IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen einhergeht, die sich erfolgreich bei Palo Alto VPN anmeldet**

## <a name="credential-harvesting"></a>Abgreifen von Anmeldeinformation
(Neue Bedrohungsklassifizierung)
### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Ausführung eines Tools für den Diebstahl von Anmeldeinformationen nach einer verdächtigen Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Zugriff auf Anmeldeinformationen

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Sicherung von Betriebssystem-Anmeldeinformation (T1003)

**Datenconnector-Quellen:** Azure Active Directory Identity Protection, Microsoft Defender für Endpunkt

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass ein bekanntes Tool zum Diebstahl von Anmeldeinformationen nach einer verdächtigen Azure AD-Anmeldung ausgeführt wurde. Dies bedeutet mit hoher Zuverlässigkeit, dass das in der Warnungsbeschreibung genannte Benutzerkonto kompromittiert wurde und unter diesem möglicherweise ein Tool wie **Mimikatz** verwendet wurde, um Anmeldeinformationen wie Schlüssel, unverschlüsselte Kennwörter oder Kennworthashes aus dem System abzugreifen. Mithilfe der gesammelten Anmeldeinformationen kann ein Angreifer auf vertrauliche Daten zugreifen, Berechtigungen erhöhen und im gesamten Netzwerk navigieren. Die Permutationen verdächtiger Azure AD-Anmeldewarnungen im Zusammenhang mit der Warnung zum Tool für den Diebstahl von Anmeldeinformationen sind:

- **Unmöglicher Ortswechsel zu atypischen Orten, der zur Ausführung eines böswilligen Tools zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis von einem unbekannten Ort, das zur Ausführung eines böswilligen Tools zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis von einem infizierten Gerät, das zur Ausführung eines böswilligen Tools zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zur Ausführung eines böswilligen Tools zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zur Ausführung eines böswilligen Tools zum Diebstahl von Anmeldeinformationen führt**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Möglicher Diebstahl von Anmeldeinformationen nach einer verdächtigen Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Zugriff auf Anmeldeinformationen

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Anmeldeinformationen aus Kennwortspeichern (T1555), Sicherung von Betriebssystem-Anmeldeinformationen (T1003)

**Datenconnector-Quellen:** Azure Active Directory Identity Protection, Microsoft Defender für Endpunkt

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass nach einer verdächtigen Azure AD-Anmeldung eine Aktivität ausgeführt wurde, die Muster des Diebstahls von Anmeldeinformationen aufweist. Dies bedeutet mit hoher Zuverlässigkeit, dass das in der Warnungsbeschreibung genannte Benutzerkonto kompromittiert wurde, um unter diesem Anmeldeinformationen wie Schlüssel, unverschlüsselte Kennwörter, Kennworthashes usw. zu stehlen. Mithilfe der gestohlenen Anmeldeinformationen kann ein Angreifer auf vertrauliche Daten zugreifen, Berechtigungen erhöhen und im gesamten Netzwerk navigieren. Die Permutationen verdächtiger Azure AD-Anmeldewarnungen im Zusammenhang mit der Warnung zum Diebstahl von Anmeldeinformationen sind:

- **Unmöglicher Ortswechsel zu atypischen Orten, der zu einer Aktivität zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu einer Aktivität zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu einer Aktivität zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu einer Aktivität zum Diebstahl von Anmeldeinformationen führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu einer Aktivität zum Diebstahl von Anmeldeinformationen führt**

## <a name="crypto-mining"></a>Crypto Mining
(Neue Bedrohungsklassifizierung)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Aktivität zum Crypto Mining nach verdächtiger Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Zugriff auf Anmeldeinformationen

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Ressourcen-Hijacking (T1496)

**Datenconnector-Quellen:** Azure Active Directory Identity Protection, Microsoft Defender für Cloud

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten auf eine Aktivität zum Crypto Mining hin, die einer verdächtigen Anmeldung bei einem Azure AD-Konto zugeordnet ist. Dies bedeutet mit hoher Zuverlässigkeit, dass das in der Warnungsbeschreibung genannte Benutzerkonto kompromittiert und zum Übernehmen von Ressourcen in Ihrer Umgebung verwendet wurde, um mit diesen das Mining einer Kryptowährung zu betreiben. Dies kann Ihre Computeressourcen und damit die Computingleistung beeinträchtigen und zu deutlich höheren als den erwarteten Cloudnutzungsgebühren führen. Die Permutationen verdächtiger Azure AD-Anmeldewarnungen im Zusammenhang mit der Warnung zum Crypto Mining sind:  

- **Unmöglicher Ortswechsel zu atypischen Orten, der zu Crypto Mining führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu Crypto Mining führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu Crypto Mining führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu Crypto Mining führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu Crypto Mining führt**

## <a name="data-destruction"></a>Datenvernichtung

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Massenlöschung von Dateien im Anschluss an eine verdächtige Azure AD-Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Beeinträchtigung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Datenvernichtung (T1485)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass einzigartige Dateien in anomaler Anzahl von einem Benutzer im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto gelöscht wurden. Dies ist ein Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto möglicherweise kompromittiert und zu böswilligen Zwecken zur Vernichtung von Daten verwendet wurde. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung zur Massenlöschung von Dateien sind:  

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zur Massenlöschung von Dateien führt**

- **Anmeldeereignis von einem unbekannten Ort, das zur Massenlöschung von Dateien führt**

- **Anmeldeereignis mit einem infizierten Gerät, das zur Massenlöschung von Dateien führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zur Massenlöschung von Dateien führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zur Massenlöschung von Dateien führt**

### <a name="mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>Massenlöschung von Dateien nach Azure AD-Anmeldung über eine IP-Adresse, die von einer Cisco Firewall-Appliance blockiert wurde
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Beeinträchtigung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Datenvernichtung (T1485)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung:** Fusion-Vorfälle dieser Art deuten darauf hin, dass eine anomale Anzahl eindeutiger Dateien nach einer erfolgreichen Azure AD-Anmeldung gelöscht wurde, obwohl die IP-Adresse des Benutzers von einer Cisco Firewall-Appliance blockiert wurde. Die ist ein Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto kompromittiert und zu böswilligen Zwecken zur Vernichtung von Daten verwendet wurde. Da die IP-Adresse von der Firewall blockiert wurde, ist dieselbe IP-Adresse, die sich erfolgreich bei Azure AD anmeldet, potenziell verdächtig und könnte eine Kompromittierung der Anmeldeinformationen für das Benutzerkonto bedeuten.

### <a name="mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>Massenlöschung von Dateien nach erfolgreicher Anmeldung bei Palo Alto VPN mit IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Zugriff auf Anmeldeinformationen, Beeinträchtigung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Brute Force (T1110), Datenvernichtung (T1485)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung**: Fusion-Vorfälle dieser Art geben an, dass eine anomale Anzahl eindeutiger Dateien von einem Benutzer gelöscht wurde, der sich erfolgreich über ein Palo Alto VPN mit einer IP-Adresse angemeldet hat, von der aus in einem ähnlichen Zeitrahmen mehrere fehlgeschlagene Azure AD-Anmeldungen erfolgt sind. Dies ist ein Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto möglicherweise kompromittiert und zu böswilligen Zwecken zur Vernichtung von Daten verwendet wurde.

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Verdächtige Aktivität zur E-Mail-Löschung im Anschluss an eine verdächtige Azure AD-Anmeldung
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Beeinträchtigung 

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Datenvernichtung (T1485)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass E-Mails in ungewöhnlicher Anzahl in einer einzelnen Sitzung im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto gelöscht wurden. Dies ist ein Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto möglicherweise kompromittiert und zu böswilligen Zwecken zur Vernichtung von Daten verwendet wurde, etwa um die Organisation zu schädigen oder E-Mail-Aktivität im Zusammenhang mit Spam zu verbergen. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung über verdächtige Aktivitäten zur E-Mail-Löschung lauten:   

- **Unmöglicher Ortswechsel an einen atypischen Speicherort führt zu verdächtiger Aktivität zur E-Mail-Löschung**

- **Anmeldeereignis von einem unbekannten Ort führt zu verdächtiger Aktivität zur E-Mail-Löschung**

- **Anmeldeereignis von einem infizierten Gerät führt zu verdächtiger Aktivität zur E-Mail-Löschung**

- **Anmeldeereignis von einer anonymen IP-Adresse führt zu verdächtiger Aktivität zur E-Mail-Löschung**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen führt zu verdächtiger Aktivität zur E-Mail-Löschung**

## <a name="data-exfiltration"></a>Datenexfiltration

### <a name="mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>E-Mail-Weiterleitungsaktivitäten nach Einrichtung eines neuen Administratorkontos, das bisher nicht bekannt war
Dieses Szenario ist zwei Klassifikationen in dieser Liste zuzuordnen: **Datenexfiltration** und **böswillige administrative Aktivität**. Aus Gründen der Klarheit wird es in beiden Abschnitten aufgeführt.

In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Sammlung, Exfiltration

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), E-Mail-Sammlung (T1114), Exfiltration über einen Webdienst (T1567)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung**: Fusion-Vorfälle dieser Art sind ein Hinweis, dass entweder ein neues Exchange-Administratorkonto erstellt wurde oder ein bestehendes Exchange-Administratorkonto in den letzten zwei Wochen zum ersten Mal eine administrative Aktion durchgeführt hat, und dass das Konto dann einige E-Mail-Weiterleitungsaktionen durchgeführt hat, die für ein Administratorkonto ungewöhnlich sind. Dies deutet darauf hin, dass das in der Beschreibung des Fusion-Vorfalls angegebene Benutzerkonto kompromittiert bzw. manipuliert und verwendet wurde, um Daten aus dem Netzwerk Ihrer Organisation zu exfiltrieren.

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Massendownload von Dateien im Anschluss an eine verdächtige Azure AD-Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Exfiltration

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass Dateien in anomaler Anzahl von einem Benutzer im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto heruntergeladen wurden. Diese Indikation bietet hohe Vertrauenswürdigkeit für die Annahme, dass das in der Fusion-Vorfallsbeschreibung angegebene Konto kompromittiert und zur Exfiltration von Daten aus dem Netzwerk Ihrer Organisation verwendet wurde. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung zum Massendownload von Dateien sind:  

- **Unmöglicher Ortswechsel zu einem atypischen Ort mit Massendownload von Dateien**

- **Anmeldeereignis von einem unbekannten Ort, das zum Massendownload von Dateien führt**

- **Anmeldeereignis mit einem infizierten Gerät, das zum Massendownload von Dateien führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zum Massendownload von Dateien führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zum Massendownload von Dateien führt**

### <a name="mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>Massendownload von Dateien nach Azure AD-Anmeldung über eine IP-Adresse, die von einer Cisco Firewall-Appliance blockiert wurde
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Exfiltration

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Exfiltration über einen Webdienst (T1567)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass eine anomale Anzahl von Dateien von einem Benutzer heruntergeladen wurde, obwohl die IP-Adresse des Benutzers von einer Cisco Firewall-Appliance blockiert wurde. Dies kann ein möglicher Versuch eines Angreifers sein, Daten aus dem Netzwerk der Organisation zu exfiltrieren, nachdem ein Benutzerkonto kompromittiert wurde. Da die IP-Adresse von der Firewall blockiert wurde, ist dieselbe IP-Adresse, die sich erfolgreich bei Azure AD anmeldet, potenziell verdächtig und könnte eine Kompromittierung der Anmeldeinformationen für das Benutzerkonto bedeuten.

### <a name="mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip"></a>Massendownload von Dateien bei gleichzeitigem SharePoint-Dateivorgang von zuvor unbekannter IP-Adresse
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktik**: Exfiltration

**MITRE ATT&CK-Techniken:** Exfiltration über Webdienst (T1567), Größenbeschränkungen für Datenübertragungen (T1030)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung**: Fusion-Vorfälle dieses Typs sind ein Hinweis, dass eine anomale Anzahl von Dateien von einem Benutzer heruntergeladen wurde, der über eine zuvor nicht verwendete IP-Adresse verbunden war. Obwohl dies kein Beweis für einen mehrstufigen Angriff ist, ergibt die Korrelation dieser beiden Warnungen mit geringerer Zuverlässigkeit einen Vorfall mit hoher Zuverlässigkeit, der auf den Versuch eines Angreifers hindeutet, Daten aus dem Netzwerk der Organisation über ein möglicherweise kompromittiertes Benutzerkonto zu exfiltrieren. In stabilen Umgebungen können solche Verbindungen über bislang nicht bekannte IP-Adressen unzulässig sein, insbesondere wenn sie mit Volumenspitzen verbunden sind, die mit einer groß angelegten Exfiltration von Dokumenten in Verbindung gebracht werden könnten.

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Massenfreigabe von Dateien im Anschluss an eine verdächtige Azure AD-Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Exfiltration

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Exfiltration über einen Webdienst (T1567)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass eine Anzahl von Dateien oberhalb eines bestimmten Schwellenwerts im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto für andere freigegeben wurden. Diese Indikation bietet hohe Vertrauenswürdigkeit für die Annahme, dass das in der Fusion-Vorfallsbeschreibung angegebene Konto kompromittiert und zur Exfiltration von Daten aus dem Netzwerk Ihrer Organisation verwendet wurde, indem Dateien wie Dokumente, Kalkulationstabellen usw. mit böswilliger Absicht für unberechtigte Benutzer freigegeben wurden. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung zur Massenfreigabe von Dateien sind:  

- **Unmöglicher Ortswechsel zu einem atypischen Ort mit Massenfreigabe von Dateien**

- **Anmeldeereignis von einem unbekannten Ort, das zur Massenfreigabe von Dateien führt**

- **Anmeldeereignis mit einem infizierten Gerät, das zur Massenfreigabe von Dateien führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zur Massenfreigabe von Dateien führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zur Massenfreigabe von Dateien führt**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Mehrere Aktivitäten zur Freigabe von Power BI-Berichten nach verdächtiger Azure AD Anmeldung 
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Exfiltration 

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Exfiltration über einen Webdienst (T1567)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass Power BI-Berichte in ungewöhnlicher Anzahl in einer einzelnen Sitzung im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto freigegeben wurden. Diese Indikation bietet hohe Vertrauenswürdigkeit für die Annahme, dass das in der Fusion-Vorfallsbeschreibung angegebene Konto kompromittiert und zur Exfiltration von Daten aus dem Netzwerk Ihrer Organisation verwendet wurde, indem Power BI-Berichte in böswilliger Absicht für unberechtigte Benutzer freigegeben wurden. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung über mehrere Aktivitäten zur Freigabe von Power BI-Berichten lauten:  

- **Unmöglicher Ortswechsel an einen atypischen Speicherort führt zu mehreren Aktivitäten zur Freigabe von Power BI-Berichten**

- **Anmeldeereignis von einem unbekannten Ort führt zu mehreren Aktivitäten zur Freigabe von Power BI-Berichten**

- **Anmeldeereignis von einem infizierten Gerät führt zu mehreren Aktivitäten zur Freigabe von Power BI-Berichten**

- **Anmeldeereignis von einer anonymen IP-Adresse führt zu mehreren Aktivitäten zur Freigabe von Power BI-Berichten**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen führt zu mehreren Aktivitäten zur Freigabe von Power BI-Berichten**

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Office 365-Postfachexfiltration im Anschluss an eine verdächtige Azure AD-Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Exfiltration, Sammlung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), E-Mail-Sammlung (T1114), Automatisierte Exfiltration (T1020)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass eine verdächtige Posteingangs-Weiterleitungsregel für den Posteingang eines Benutzers im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto festgelegt wurde. Diese Indikation bietet hohe Vertrauenswürdigkeit für die Annahme, dass das Konto des Benutzers (das in der Fusion-Vorfallsbeschreibung angegeben ist) kompromittiert und zur Exfiltration von Daten aus dem Netzwerk Ihrer Organisation verwendet wurde, indem ohne Wissen des wahren Benutzers eine Weiterleitungsregel für ein Postfach aktiviert wurde. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung zur Office 365-Postfachexfiltration sind:

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zu einer Exfiltration des Office 365-Postfachs führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu einer Exfiltration des Office 365-Postfachs führt**

- **Anmeldeereignis mit einem infizierten Gerät, das zu einer Exfiltration des Office 365-Postfachs führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu einer Exfiltration des Office 365-Postfachs führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zur Exfiltration des Office 365-Postfachs führt**

### <a name="sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection"></a>SharePoint-Dateivorgang von zuvor unbekannter IP-Adresse nach Schadsoftwareerkennung
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktik**: Exfiltration, Umgehen von Verteidigungsmaßnahmen

**MITRE ATT&CK-Techniken**: Größenbeschränkungen für die Datenübertragung (T1030)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung**: Fusion-Vorfälle dieser Art deuten darauf hin, dass ein Angreifer mithilfe von Schadsoftware versucht hat, große Datenmengen durch Herunterladen oder Freigeben über SharePoint zu exfiltrieren. In stabilen Umgebungen können solche Verbindungen über bislang nicht bekannte IP-Adressen unzulässig sein, insbesondere wenn sie mit Volumenspitzen verbunden sind, die mit einer groß angelegten Exfiltration von Dokumenten in Verbindung gebracht werden könnten. 

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Verdächtige Regeln zur Posteingangsänderung im Anschluss an verdächtige Azure AD-Anmeldung festgelegt
Dieses Szenario ist zwei Klassifikationen in dieser Liste zuzuordnen: **Datenexfiltration** und **Lateral Movement**. Aus Gründen der Klarheit wird es in beiden Abschnitten aufgeführt.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Lateral Movement, Exfiltration

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), internes Spear-Phishing (T1534), Automatisierte Exfiltration (T1020)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass anomale Posteingangs-Weiterleitungsregeln für den Posteingang eines Benutzers im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto festgelegt wurden. Dies gibt einen sehr zuverlässigen Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto kompromittiert und zu böswilligen Zwecken zur Manipulation der Regeln des Benutzerposteingangs verwendet wurde, um möglicherweise Daten aus dem Netzwerk der Organisation zu exfiltrieren. Alternativ könnte der Angreifer versuchen, Phishing-E-Mails aus dem Innern der Organisation zu erstellen (durch Umgehen der Mechanismen zur Erkennung von Phishing, die auf E-Mails aus externen Quellen gerichtet sind), um ihm durch Erwerb des Zugriffs auf weitere Benutzerkonten und/oder bevorrechtigte Konten eine Seitwärtsbewegung zu ermöglichen. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung über verdächtige Regeln zur Postfachänderung lauten:

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zu einer verdächtigen Regel zur Posteingangs-Änderung führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu verdächtiger Regel zur Posteingangsänderung führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu verdächtiger Regel zur Posteingangsänderung führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu verdächtiger Regel zur Posteingangsänderung führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu verdächtiger Regel zur Posteingangsänderung führt**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Verdächtige Freigabe von Power BI-Berichten im Anschluss an verdächtige Azure AD-Anmeldung
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Exfiltration 

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Exfiltration über einen Webdienst (T1567)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass eine verdächtige Aktivität zur Freigabe von Power BI-Berichten im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto auftrat. Die Freigabeaktivität wurde als verdächtig identifiziert, weil der Power BI-Bericht vertrauliche Informationen enthielt, wie mithilfe der natürlichsprachlichen Verarbeitung festgestellt wurde, und weil er mit einer externen E-Mail-Adresse geteilt, im Web veröffentlicht oder als Momentaufnahme an eine extern abonnierte E-Mail-Adresse übermittelt wurde. Diese Benachrichtigung deutet mit hoher Vertrauenswürdigkeit darauf hin, dass das in der Fusion-Vorfallsbeschreibung angegebene Konto kompromittiert und zur Exfiltration vertraulicher Daten aus Ihrer Organisation verwendet wurde, indem Power BI-Berichte in böswilliger Absicht für unberechtigte Benutzer freigegeben wurden. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung über die verdächtige Freigabe von Power BI-Berichten lauten:  

- **Unmöglicher Ortswechsel an einen atypischen Speicherort führt zu verdächtiger Freigabe von Power BI-Berichten**

- **Anmeldeereignis von einem unbekannten Ort führt zu verdächtiger Freigabe von Power BI-Berichten**

- **Anmeldeereignis von einem infizierten Gerät führt zu verdächtiger Freigabe von Power BI-Berichten**

- **Anmeldeereignis von einer anonymen IP-Adresse führt zu verdächtiger Freigabe von Power BI-Berichten**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen führt zu verdächtiger Freigabe von Power BI-Berichten**

## <a name="denial-of-service"></a>Denial of Service

### <a name="multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in"></a>Mehrere Aktivitäten zur Löschung von VMs nach verdächtiger Azure AD Anmeldung
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Beeinträchtigung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Denial-of-Service am Endpunkt (T1499)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass virtuelle Computer in ungewöhnlicher Anzahl in einer einzelnen Sitzung im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto gelöscht wurden. Diese Indikation bietet hohe Vertrauenswürdigkeit für die Annahme, dass das in der Fusion-Vorfallsbeschreibung genannte Konto kompromittiert und zum Versuch der Betriebsunterbrechung oder Vernichtung der Cloudumgebung der Organisation verwendet wurde. Die Permutationen von Warnungen zu verdächtigen Azure AD-Anmeldungen im Zusammenhang mit der Warnung über mehrere Aktivitäten zur Löschung von VMs lauten:  

- **Unmöglicher Ortswechsel an einen atypischen Speicherort, der zu mehreren Aktivitäten zur Löschung von VMs führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu mehreren Aktivitäten zur Löschung von VMs führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu mehreren Aktivitäten zur Löschung von VMs führt**

- **Anmeldeereignis mit einer anonymen IP-Adresse, das zu mehreren Aktivitäten zur Löschung von VMs führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu mehreren Aktivitäten zur Löschung von VMs führt**

## <a name="lateral-movement"></a>Seitwärtsbewegung

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Office 365-Identitätswechsel im Anschluss an eine verdächtige Azure AD-Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Lateral Movement

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), internes Spear-Phishing (T1534)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass Identitätswechselaktionen in anomaler Anzahl im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto aufgetreten sind. Einige Softwareanwendungen bieten Optionen, mit denen Benutzer die Identität anderer Benutzer annehmen können. Beispielsweise geben E-Mail-Dienste ihren Benutzern die Möglichkeit, andere Benutzer zum Senden von E-Mails in ihrem Auftrag zu ermächtigen. Diese Benachrichtigung gibt einen ziemlich vertrauenswürdigen Hinweis darauf, dass das in der Fusion-Vorfallsbeschreibung genannte Konto kompromittiert und dazu verwendet wurde, Identitätswechselaktivitäten zu böswilligen Zwecken durchzuführen, etwa um Phishing-E-Mails zur Verteilung von Malware oder zur Seitwärtsbewegung zu senden. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung zum Office 365-Identitätswechsel sind:  

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zu einem Office 365-Identitätswechsel führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu einem Office 365-Identitätswechsel führt**

- **Anmeldeereignis mit einem infizierten Gerät, das zu einem Office 365-Identitätswechsel führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu einem Office 365-Identitätswechsel führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu einem Office 365-Identitätswechsel führt**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Verdächtige Regeln zur Posteingangsänderung im Anschluss an verdächtige Azure AD-Anmeldung festgelegt
Dieses Szenario ist zwei Klassifikationen in dieser Liste zuzuordnen: **Seitwärtsbewegung** und **Datenexfiltration**. Aus Gründen der Klarheit wird es in beiden Abschnitten aufgeführt.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Lateral Movement, Exfiltration

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), internes Spear-Phishing (T1534), Automatisierte Exfiltration (T1020)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass anomale Posteingangs-Weiterleitungsregeln für den Posteingang eines Benutzers im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto festgelegt wurden. Dies gibt einen sehr zuverlässigen Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto kompromittiert und zu böswilligen Zwecken zur Manipulation der Regeln des Benutzerposteingangs verwendet wurde, um möglicherweise Daten aus dem Netzwerk der Organisation zu exfiltrieren. Alternativ könnte der Angreifer versuchen, Phishing-E-Mails aus dem Innern der Organisation zu erstellen (durch Umgehen der Mechanismen zur Erkennung von Phishing, die auf E-Mails aus externen Quellen gerichtet sind), um ihm durch Erwerb des Zugriffs auf weitere Benutzerkonten und/oder bevorrechtigte Konten eine Seitwärtsbewegung zu ermöglichen. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung über verdächtige Regeln zur Postfachänderung lauten:

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zu einer verdächtigen Regel zur Posteingangs-Änderung führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu verdächtiger Regel zur Posteingangsänderung führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu verdächtiger Regel zur Posteingangsänderung führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu verdächtiger Regel zur Posteingangsänderung führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu verdächtiger Regel zur Posteingangsänderung führt**

## <a name="malicious-administrative-activity"></a>Böswillige administrative Aktivität

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Verdächtige administrative Aktivität in der Cloud-App im Anschluss an eine verdächtige Azure AD-Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Persistenz, Umgehung von Verteidigungsmaßnahmen, Lateral Movement, Sammlung, Exfiltration und Beeinträchtigung

**MITRE ATT&CK-Techniken:** –

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass administrative Aktivitäten in anomaler Anzahl in einer einzelnen Sitzung im Anschluss an eine verdächtige Azure AD-Anmeldung des gleichen Kontos durchgeführt wurden. Dies ist ein Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto möglicherweise kompromittiert und in böswilliger Absicht zum Ausführen einer beliebigen Anzahl nicht autorisierter administrativer Aktionen verwendet wurde. Dies ist zugleich ein Hinweis darauf, dass möglicherweise ein Konto mit Administratorberechtigungen kompromittiert wurde. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung über verdächtige administrative Aktivitäten in der Cloud-App lauten:  

- **Unmöglicher Ortswechsel zu einem typischen Ort, der zu verdächtiger Verwaltungsaktivität für die Cloud-App führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu verdächtiger Verwaltungsaktivität für die Cloud-App führt**

- **Anmeldeereignis mit einem infizierten Gerät, das zu verdächtiger Verwaltungsaktivität für die Cloud-App führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu verdächtiger Verwaltungsaktivität für die Cloud-App führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu verdächtiger Verwaltungsaktivität für die Cloud-App führt**

### <a name="mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>E-Mail-Weiterleitungsaktivitäten nach Einrichtung eines neuen Administratorkontos, das bisher nicht bekannt war
Dieses Szenario ist zwei Klassifikationen in dieser Liste zuzuordnen: **böswillige administrative Aktivität** und **Datenexfiltration**. Aus Gründen der Klarheit wird es in beiden Abschnitten aufgeführt.

In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Sammlung, Exfiltration

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), E-Mail-Sammlung (T1114), Exfiltration über einen Webdienst (T1567)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung**: Fusion-Vorfälle dieser Art sind ein Hinweis, dass entweder ein neues Exchange-Administratorkonto erstellt wurde oder ein bestehendes Exchange-Administratorkonto in den letzten zwei Wochen zum ersten Mal eine administrative Aktion durchgeführt hat, und dass das Konto dann einige E-Mail-Weiterleitungsaktionen durchgeführt hat, die für ein Administratorkonto ungewöhnlich sind. Dies deutet darauf hin, dass das in der Beschreibung des Fusion-Vorfalls angegebene Benutzerkonto kompromittiert bzw. manipuliert und verwendet wurde, um Daten aus dem Netzwerk Ihrer Organisation zu exfiltrieren.

## <a name="malicious-execution-with-legitimate-process"></a>Böswillige Ausführung mit legitimem Prozess

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell hat eine verdächtige Netzwerkverbindung hergestellt, auf die anomaler, durch die Palo Alto Networks-Firewall gekennzeichneter Datenverkehr folgte.
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Ausführung

**MITRE ATT&CK-Techniken:** Befehls- und Skript-Interpreter (T1059)

**Datenconnector-Quellen:** Microsoft Defender for Endpoint (früher Microsoft Defender Advanced Threat Protection oder MDATP), Palo Alto Networks 

**Beschreibung:** Fusion-Vorfälle dieses Typs zeigen an, dass eine ausgehende Verbindungsanforderung mithilfe eines PowerShell-Befehls durchgeführt wurde. Im Anschluss daran wurde von der Palo Alto Networks-Firewall eine ungewöhnliche eingehende Aktivität erkannt. Dies ist ein Hinweis darauf, dass ein Angreifer sich wahrscheinlich Zugriff auf Ihr Netzwerk verschafft hat und versucht, böswillige Aktionen auszuführen. Verbindungsversuche von PowerShell, die diesem Muster folgen, können einen Hinweis auf Command-and-Control-Aktivitäten von Malware, Anforderungen für den Download zusätzlicher Malware oder einen Angreifer darstellen, der interaktiven Remotezugriff einrichtet. Wie bei allen Living-off-the-Land-Angriffen könnte diese Aktivität auch eine legitime Verwendung von PowerShell darstellen. Die Ausführung eines PowerShell-Befehls, auf die verdächtige eingehende Firewall-Aktivitäten folgen, steigert aber die Glaubwürdigkeit der Annahme, dass PowerShell in böswilliger Weise verwendet wird und der Vorfall genauer untersucht werden sollte. In Palo Alto-Protokollen sucht Microsoft Sentinel hauptsächlich nach [Bedrohungen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), und der Datenverkehr wird als verdächtig eingestuft, wenn Bedrohungen durchgelassen werden (verdächtige Daten, Dateien, Überflutungen, Pakete, Scans, Spyware, URLs, Viren, Sicherheitsrisiken, Wildfireviren, Wildfires). Konsultieren Sie außerdem das Palo Alto-Bedrohungsprotokoll, das dem in der Fusion-Vorfallsbeschreibung aufgelisteten [Bedrohungs-/Inhaltstyp](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) entspricht, um weitere Details zur Benachrichtigung zu erhalten.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Verdächtige WMI-Remoteausführung, auf die anomaler, durch die Palo Alto Networks-Firewall gekennzeichneter Datenverkehr folgte.
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Ausführung, Ermittlung

**MITRE ATT&CK-Techniken:** Windows-Verwaltungsinstrumentation (T1047)

**Datenconnector-Quellen:** Microsoft Defender für den Endpunkt (vormals MDATP), Palo Alto Networks 

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass WMI-Befehle (Windows Management Interface) remote auf einem System ausgeführt wurden. Im Anschluss daran wurden von der Palo Alto Networks-Firewall verdächtige eingehende Aktivitäten erkannt. Dies ist ein Hinweis darauf, dass sich ein Angreifer möglicherweise Zugriff auf Ihr Netzwerk verschafft hat und Lateral Movement, das Heraufstufen von Berechtigungen und/oder Ausführen schädlicher Nutzdaten versucht. Wie bei allen Living-off-the-Land-Angriffen könnte diese Aktivität auch eine legitime Verwendung von WMI darstellen. Die Remoteausführung eines WMI-Befehls, auf die verdächtige eingehende Firewall-Aktivitäten folgen, steigert aber die Glaubwürdigkeit der Annahme, dass WMI in böswilliger Weise verwendet wird und der Vorfall genauer untersucht werden sollte. In Palo Alto-Protokollen sucht Microsoft Sentinel hauptsächlich nach [Bedrohungen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), und der Datenverkehr wird als verdächtig eingestuft, wenn Bedrohungen durchgelassen werden (verdächtige Daten, Dateien, Überflutungen, Pakete, Scans, Spyware, URLs, Viren, Sicherheitsrisiken, Wildfireviren, Wildfires). Konsultieren Sie außerdem das Palo Alto-Bedrohungsprotokoll, das dem in der Fusion-Vorfallsbeschreibung aufgelisteten [Bedrohungs-/Inhaltstyp](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) entspricht, um weitere Details zur Benachrichtigung zu erhalten.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Verdächtige PowerShell-Befehlszeile nach verdächtiger Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Ausführung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Befehls- und Skript-Interpreter (T1059)

**Datenconnector-Quellen:** Azure Active Directory Identity Protection, Microsoft Defender für Endpunkt (ehemals MDATP)

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass ein Benutzer im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto potenziell böswillige PowerShell-Befehle ausgeführt hat. Dies bedeutet mit hoher Zuverlässigkeit, dass das in der Warnungsbeschreibung genannte Konto kompromittiert wurde und weitere schädliche Aktionen durchgeführt wurden. Angreifer nutzen häufig PowerShell, um schädliche Nutzdaten im Arbeitsspeicher auszuführen, ohne Artefakte auf dem Datenträger zu hinterlassen. Dadurch wird die Erkennung durch datenträgerbasierte Sicherheitsmechanismen wie Virenscanner vermieden. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Warnung über die verdächtige PowerShell-Befehle lauten:

- **Unmöglicher Ortswechsel zu atypischen Orten, der zu einer verdächtigen PowerShell-Befehlszeile führt**

- **Anmeldeereignis von einem unbekannten Ort, das zu einer verdächtigen PowerShell-Befehlszeile führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu einer verdächtigen PowerShell-Befehlszeile führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zu einer verdächtigen PowerShell-Befehlszeile führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu einer verdächtigen PowerShell-Befehlszeile führt**

## <a name="malware-c2-or-download"></a>Malware C2 oder Download

### <a name="beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service"></a>Von Fortinet erkanntes Beaconmuster nach mehreren fehlgeschlagenen Benutzeranmeldungen bei einem Dienst

In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken**: Erstzugriff, Command-and-Control

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Kein Standardport (T1571), T1065 (eingestellt)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Microsoft Defender für Cloud Apps

**Beschreibung**: Fusion-Vorfälle dieser Art deuten auf Kommunikationsmuster von einer internen zu einer externen IP-Adresse hin, die mit Beaconing übereinstimmen, nachdem die Anmeldung eines Benutzers bei einem Dienst von einer zugehörigen internen Entität mehrfach fehlgeschlagen ist. Die Kombination dieser beiden Ereignisse könnte ein Hinweis auf eine Infektion mit Schadsoftware oder auf einen kompromittierten Host sein, der Daten exfiltriert. 

### <a name="beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in"></a>Von Fortinet erkanntes Beaconmuster nach verdächtiger Azure AD-Anmeldung

In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken**: Erstzugriff, Command-and-Control

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Kein Standardport (T1571), T1065 (eingestellt)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Azure Active Directory Identity Protection

**Beschreibung**: Fusion-Vorfälle dieser Art deuten auf Kommunikationsmuster von einer internen zu einer externen IP-Adresse hin, die mit Beaconing übereinstimmen, nachdem eine verdächtige Benutzeranmeldung bei Azure AD erfolgt ist. Die Kombination dieser beiden Ereignisse könnte ein Hinweis auf eine Infektion mit Schadsoftware oder auf einen kompromittierten Host sein, der Daten exfiltriert. Die Permutationen der von Fortinet erkannten Beaconmuster mit Warnungen zu verdächtigen Azure AD-Anmelderungen sind wie folgt:   

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zu einem von Fortinet erkannten Beaconmuster führt**

- **Anmeldeereignis von einem nicht bekannten Ort, das zu einem von Fortinet erkannten Beaconmuster führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu einem von Fortinet erkannten Beaconmuster führt**

- **Anmeldeereignis mit einer anonymen IP-Adresse, das zu einem von Fortinet erkannten Beaconmuster führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zu einem von Fortinet erkannten Beaconmuster führt**

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Netzwerkanforderung an den TOR-Anonymisierungsdienst gefolgt von anomalem, durch die Palo Alto Networks-Firewall gekennzeichnetem Datenverkehr
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Befehl und Steuerung

**MITRE ATT&CK-Techniken:** Verschlüsselter Kanal (T1573), Proxy (T1090)

**Datenconnector-Quellen:** Microsoft Defender für den Endpunkt (vormals MDATP), Palo Alto Networks 

**Beschreibung:** Fusion-Vorfälle dieses Typs zeigen an, dass eine ausgehende Verbindungsanforderung an den TOR-Anonymisierungsdienst durchgeführt wurde. Im Anschluss daran wurde dann von der Palo Alto Networks-Firewall eine ungewöhnliche eingehende Aktivität erkannt. Dies ist einen Hinweis darauf, dass ein Angreifer sich wahrscheinlich Zugriff auf Ihr Netzwerk verschafft hat und versucht, seine Aktionen und Absichten zu verbergen. Verbindungsversuche mit dem TOR-Netzwerk, die diesem Muster folgen, können einen Hinweis auf Command-and-Control-Aktivitäten von Malware, Anforderungen für den Download zusätzlicher Malware oder einen Angreifer darstellen, der interaktiven Remotezugriff einrichtet. In Palo Alto-Protokollen sucht Microsoft Sentinel hauptsächlich nach [Bedrohungen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), und der Datenverkehr wird als verdächtig eingestuft, wenn Bedrohungen durchgelassen werden (verdächtige Daten, Dateien, Überflutungen, Pakete, Scans, Spyware, URLs, Viren, Sicherheitsrisiken, Wildfireviren, Wildfires). Konsultieren Sie außerdem das Palo Alto-Bedrohungsprotokoll, das dem in der Fusion-Vorfallsbeschreibung aufgelisteten [Bedrohungs-/Inhaltstyp](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) entspricht, um weitere Details zur Benachrichtigung zu erhalten.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Ausgehende Verbindung mit IP mit einem Verlauf nicht autorisierter Zugriffsversuche, gefolgt von anomalem, durch die Palo Alto Networks-Firewall gekennzeichnetem Datenverkehr
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Befehl und Steuerung

**MITRE ATT&CK-Techniken:** Nicht verfügbar

**Datenconnector-Quellen:** Microsoft Defender für den Endpunkt (vormals MDATP), Palo Alto Networks 

**Beschreibung:** Fusion-Vorfälle dieses Typs zeigen an, dass eine ausgehende Verbindung mit einer IP-Adresse mit einem Backlog unberechtigter Zugriffsversuche hergestellt wurde. Im Anschluss daran wurde von der Palo Alto Networks-Firewall eine ungewöhnliche Aktivität erkannt. Dies deutet darauf hin, dass ein Angreifer sich wahrscheinlich Zugriff auf Ihr Netzwerk verschafft hat. Verbindungsversuche, die diesem Muster folgen, können einen Hinweis auf Command-and-Control-Aktivitäten von Malware, Anforderungen für den Download zusätzlicher Malware oder einen Angreifer darstellen, der interaktiven Remotezugriff einrichtet. In Palo Alto-Protokollen sucht Microsoft Sentinel hauptsächlich nach [Bedrohungen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), und der Datenverkehr wird als verdächtig eingestuft, wenn Bedrohungen durchgelassen werden (verdächtige Daten, Dateien, Überflutungen, Pakete, Scans, Spyware, URLs, Viren, Sicherheitsrisiken, Wildfireviren, Wildfires). Konsultieren Sie außerdem das Palo Alto-Bedrohungsprotokoll, das dem in der Fusion-Vorfallsbeschreibung aufgelisteten [Bedrohungs-/Inhaltstyp](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) entspricht, um weitere Details zur Benachrichtigung zu erhalten.

## <a name="persistence"></a>Persistenz
(Neue Bedrohungsklassifizierung)

### <a name="rare-application-consent-following-suspicious-sign-in"></a>Seltene Anwendungseinwilligung nach verdächtiger Anmeldung

In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken**: Persistenz, Erstzugriff

**MITRE ATT&CK-Techniken:** Konto erstellen (T1136), gültiges Konto (T1078)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieser Art deuten darauf hin, dass ein Benutzer seine Einwilligung für eine Anwendung gegeben hat, der dies noch nie oder nur selten getan hat, nachdem eine verdächtige Anmeldung bei einem Azure AD-Konto erfolgt war. Dies ist ein Hinweis darauf, dass das in der Beschreibung des Fusion-Vorfalls genannte Konto möglicherweise kompromittiert und zu böswilligen Zwecken für den Zugriff auf oder die Manipulation der Anwendung verwendet wurde.  Einwilligen in eine Anwendung, Hinzufügen eines Dienstprinzipals und Hinzufügen von OAuth2PermissionGrant sollten in der Regel seltene Ereignisse sein. Angreifer können diese Art der Konfigurationsänderung nutzen, um sich in Systemen dauerhaft festzusetzen. Die Permutationen von Warnungen zu verdächtigen Azure AD-Anmeldungen im Zusammenhang mit der Warnung zur seltenen Einwilligung in Anwendungen sind wie folgt:

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zur seltenen Einwilligung in Anwendung führt**

- **Anmeldeereignis von einem unbekannten Ort, das zur seltenen Einwilligung in Anwendung führt**

- **Anmeldeereignis von einem infizierten Gerät, das zur seltenen Einwilligung in Anwendung führt**

- **Anmeldeereignis mit einer anonymen IP-Adresse, das zur seltenen Einwilligung in Anwendung führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zur seltenen Einwilligung in Anwendung führt**

## <a name="ransomware"></a>Ransomware

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Ausführung von Ransomware im Anschluss an eine verdächtige Azure AD-Anmeldung

**MITRE ATT&CK-Taktiken:** Erstzugriff, Beeinträchtigung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Verschlüsselung von Daten zur Beeinträchtigung (T1486)

**Datenconnector-Quellen:** Microsoft Defender für Cloud Apps, Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass anomales Benutzerverhalten, das auf einen Ransomware-Angriff hinweist, im Anschluss an eine verdächtige Anmeldung bei einem Azure AD-Konto entdeckt wurden. Dieser Hinweis weist mit hoher Vertrauenswürdigkeit darauf hin, dass das in der Fusion-Vorfallsbeschreibung genannte Konto kompromittiert und dazu verwendet wurde, Daten zum Zweck der Erpressung des Datenbesitzers oder zur Hinderung des Datenbesitzers am Zugriff auf seine Daten zu verschlüsseln. Die Permutationen verdächtiger Azure AD-Anmeldebenachrichtigungen im Zusammenhang mit der Benachrichtigung zur Ausführung von Ransomware sind:  

- **Unmöglicher Ortswechsel zu einem typischen Ort, der zum Vorhandensein von Ransomware in der Cloud-App führt**

- **Anmeldeereignis von einem unbekannten Ort, das zum Vorhandensein von Ransomware in der Cloud-App führt**

- **Anmeldeereignis mit einem infizierten Gerät, das zum Vorhandensein von Ransomware in der Cloud-App führt**

- **Anmeldeereignis von einer anonymen IP-Adresse, das zum Vorhandensein von Ransomware in der Cloud-App führt**

- **Anmeldeereignis eines Benutzers mit kompromittierten Anmeldeinformationen, das zum Vorhandensein von Ransomware in der Cloud-App führt**

## <a name="remote-exploitation"></a>Remoteausnutzung

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Verdacht der Nutzung eines Angriffs-Frameworks, auf den anomaler, durch die Palo Alto Networks-Firewall gekennzeichneter Datenverkehr folgt.
Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Ausführung, Lateral Movement, Rechteausweitung

**MITRE ATT&CK-Techniken:** Ausnutzung von öffentlichen Anwendungen (T1190), Ausnutzung zur Clientausführung (T1203), Ausnutzung von Remotediensten (T1210), Ausnutzung zur Rechteausweitung (T1068)

**Datenconnector-Quellen:** Microsoft Defender für den Endpunkt (vormals MDATP), Palo Alto Networks 

**Beschreibung:** Fusion-Vorfälle dieses Typs deuten darauf hin, dass die nicht standardmäßige Verwendung von Protokollen, die der Verwendung von Angriffs-Frameworks wie z. B. Metasploit ähnelt, erkannt wurde. Im Anschluss daran wurden von der Palo Alto Networks-Firewall verdächtige eingehende Aktivitäten erkannt. Dies kann ein früher Hinweis darauf, sein, dass ein Angreifer einen Dienst ausgenutzt hat, um Zugriff auf Ihre Netzwerkressourcen zu erlangen, oder den Zugriff bereits erlangt hat und versucht, verfügbare Systeme/Dienste weiter für Seitwärtsbewegung und/oder Rechteausweitung auszunutzen. In Palo Alto-Protokollen sucht Microsoft Sentinel hauptsächlich nach [Bedrohungen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), und der Datenverkehr wird als verdächtig eingestuft, wenn Bedrohungen durchgelassen werden (verdächtige Daten, Dateien, Überflutungen, Pakete, Scans, Spyware, URLs, Viren, Sicherheitsrisiken, Wildfireviren, Wildfires). Konsultieren Sie außerdem das Palo Alto-Bedrohungsprotokoll, das dem in der Fusion-Vorfallsbeschreibung aufgelisteten [Bedrohungs-/Inhaltstyp](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) entspricht, um weitere Details zur Benachrichtigung zu erhalten.

## <a name="resource-hijacking"></a>Ressourcenübernahme
(Neue Bedrohungsklassifizierung)

### <a name="suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in"></a>Verdächtige Ressourcen-/Ressourcengruppenbereitstellung durch einen zuvor unbekannten Aufrufer nach verdächtiger Azure AD Anmeldung
In diesem Szenario werden Warnungen verwendet, die von **geplanten Analyseregeln** generiert werden.

Dieses Szenario befindet sich derzeit in der **VORSCHAU**.

**MITRE ATT&CK-Taktiken:** Erstzugriff, Beeinträchtigung

**MITRE ATT&CK-Techniken:** Gültiges Konto (T1078), Ressourcen-Hijacking (T1496)

**Datenconnector-Quellen:** Microsoft Sentinel (Regel für geplante Analysen), Azure Active Directory Identity Protection

**Beschreibung:** Fusion-Vorfälle dieser Art deuten darauf hin, dass ein Benutzer eine Azure-Ressource oder -Ressourcengruppe bereitgestellt hat (eine seltene Aktivität), nachdem er sich bei einem verdächtigen Azure AD-Konto mit zuvor nicht bekannten Eigenschaften angemeldet hat. Dies könnte möglicherweise ein Versuch eines Angreifers sein, Ressourcen oder Ressourcengruppen für böswillige Zwecke bereitzustellen, nachdem er das in der Beschreibung des Fusion-Vorfalls genannte Konto des Benutzers kompromittiert hat.

Die Permutationen von Warnungen zu verdächtigen Azure AD-Anmeldungen bei der Bereitstellung einer verdächtigen Ressource/Ressourcengruppe durch einen bislang nicht bekannten Aufrufer sind wie folgt:

- **Unmöglicher Ortswechsel zu einem atypischen Ort, der zu verdächtiger Bereitstellung von Ressourcen-/Ressourcengruppen durch einen bislang nicht bekannten Aufrufer führt**

- **Anmeldeereignis von einem atypischen Ort, das zu verdächtiger Bereitstellung von Ressourcen-/Ressourcengruppen durch einen bislang nicht bekannten Aufrufer führt**

- **Anmeldeereignis von einem infizierten Gerät, das zu verdächtiger Ressourcen-/Ressourcengruppenbereitstellung durch einen bislang nicht bekannten Aufrufer führt**

- **Anmeldeereignis mit einer anonymen IP-Adresse, das zu verdächtiger Bereitstellung von Ressourcen-/Ressourcengruppen durch einen bislang nicht bekannten Aufrufer führt**

- **Anmeldeereignis mit einer anonymen IP-Adresse, das zu verdächtiger Bereitstellung von Ressourcen-/Ressourcengruppen durch einen bislang nicht bekannten Aufrufer führt**

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die erweiterte Erkennung von mehrstufigen Angriffen erfahren haben, ist für Sie ggf. die folgende Schnellstartanleitung interessant. Darin wird veranschaulicht, wie Sie Einblicke in Ihre Daten und potenzielle Bedrohungen erhalten: [Erste Schritte mit Microsoft Sentinel](get-visibility.md).

Wenn Sie bereit sind, die Vorfälle zu untersuchen, die für Sie erstellt werden, lesen Sie das folgende Tutorial: [Untersuchen von Vorfällen mit Azure Sentinel](investigate-cases.md).
