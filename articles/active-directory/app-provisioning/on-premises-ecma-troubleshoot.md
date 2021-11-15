---
title: Behandeln von Problemen bei der Bereitstellung für lokale Anwendungen
description: Hier wird die Behandlung verschiedener Probleme beschrieben, die bei der Installation und Verwendung des ECMA-Connectorhosts auftreten können.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a364995ae443f121112c7e259525735bc7b8a13c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456345"
---
# <a name="troubleshoot-on-premises-application-provisioning"></a>Problembehandlung bei der Bereitstellung lokaler Anwendungen

>[!IMPORTANT]
> Die Vorschauversion zur lokalen Bereitstellung ist derzeit nur mit Einladung verfügbar. Um Zugriff auf die Funktion anzufordern, verwenden Sie das [Zugriffsanforderungsformular](https://aka.ms/onpremprovisioningpublicpreviewaccess). Wir öffnen die Vorschauversion im Rahmen der Vorbereitung auf die allgemeine Verfügbarkeit in den nächsten Monaten für weitere Kunden und Connectors.

## <a name="troubleshoot-test-connection-issues"></a>Beheben von Testverbindungsproblemen
Nach dem Konfigurieren des Bereitstellungs-Agents und des ECMA-Hosts ist es an der Zeit, die Konnektivität zwischen dem Azure AD-Bereitstellungsdienst (Azure Active Directory) und dem Bereitstellungs-Agent, dem ECMA-Host und der Anwendung zu testen. Dieser End-to-End-Test kann durch Auswählen von **Verbindung testen** in der Anwendung im Azure-Portal durchgeführt werden. Wenn beim Testen der Verbindung ein Fehler auftritt, führen Sie die folgenden Schritte zur Problembehandlung aus:

 1. Überprüfen Sie, ob der Agent und der ECMA-Host ausgeführt werden:
     1. Öffnen Sie auf dem Server, auf dem der Agent installiert ist, die Seite **Dienste**, indem Sie zu **Start** > **Ausführen** > **Services.msc** navigieren.
     2. Vergewissern Sie sich, dass unter **Dienste** die Dienste **Microsoft Azure AD Connect Agent Updater**, **Microsoft Azure AD Connect-Bereitstellungs-Agent** und **Microsoft ECMA2Host** angezeigt werden und ihr Status *Wird ausgeführt* lautet.
    
        ![Screenshot: Ausgeführter ECMA-Dienst](./media/on-premises-ecma-troubleshoot/tshoot-1.png)

 2. Navigieren Sie zu dem Ordner, in dem der ECMA-Host installiert wurde, indem Sie **Problembehandlung** > **Skripts** > **TestECMA2HostConnection** auswählen. Führen Sie das Skript aus. Dieses Skript sendet eine SCIM GET- oder POST-Anforderung, um zu überprüfen, ob der ECMA-Connectorhost ausgeführt wird und auf Anforderungen reagiert. Es sollte auf dem gleichen Computer wie der eigentliche ECMA-Connectorhostdienst ausgeführt werden.
 3. Vergewissern Sie sich, dass der Agent aktiv ist, indem Sie zu Ihrer Anwendung im Azure-Portal navigieren, **Administratorkonnektivität** und die Dropdownliste der Agents auswählen und überprüfen, ob Ihr Agent aktiv ist.
 4. Überprüfen Sie, ob das angegebene geheime Token mit dem lokalen geheimen Token identisch ist. Wechseln Sie zur lokalen Umgebung, geben Sie das geheime Token erneut an, und kopieren Sie es in das Azure-Portal.
 5. Vergewissern Sie sich, dass Sie der Anwendung im Azure-Portal mindestens einen Agent zugewiesen haben.
 6. Nach dem Zuweisen eines Agents müssen Sie 10 bis 20 Minuten warten, bis die Registrierung abgeschlossen ist. Der Konnektivitätstest funktioniert erst, wenn die Registrierung abgeschlossen ist.
 7. Achten Sie darauf, ein gültiges Zertifikat zu verwenden. Navigieren Sie zur Registerkarte **Einstellungen** des ECMA-Hosts, um ein neues Zertifikat zu generieren.
 8. Führen Sie einen Neustart des Bereitstellungs-Agents durch, indem Sie auf Ihrer VM zur Taskleiste navigieren und nach dem Microsoft Azure AD Connect-Bereitstellungs-Agent suchen. Klicken Sie mit der rechten Maustaste auf **Beenden**, und wählen Sie dann **Starten** aus.
 9. Achten Sie bei Angeben der Mandanten-URL im Azure-Portal darauf, dass sie dem folgenden Muster entspricht. Sie können `localhost` durch Ihren Hostnamen ersetzen, dies ist aber nicht zwingend erforderlich. Ersetzen Sie `connectorName` durch den Namen des Connectors, den Sie im ECMA-Host angegeben haben. Die Fehlermeldung „Ungültige Ressource“ weist in der Regel darauf hin, dass die URL nicht dem erwarteten Format entspricht.
 
    ```
    https://localhost:8585/ecma2host_connectorName/scim
    ```

## <a name="unable-to-configure-the-ecma-host-view-logs-in-event-viewer-or-start-the-ecma-host-service"></a>Der ECMA-Host kann nicht konfiguriert, Protokolle in der Ereignisanzeige können nicht angezeigt oder der ECMA-Hostdienst kann nicht gestartet werden.

Führen Sie den ECMA-Host als Administrator aus, um die folgenden Probleme zu beheben:

* Beim Öffnen des ECMA-Host-Assistenten wird ein Fehler angezeigt.

   ![Screenshot: Fehler im ECMA-Assistenten](./media/on-premises-ecma-troubleshoot/tshoot-2.png)

* Ich kann den ECMA-Host-Assistenten konfigurieren, aber die ECMA-Hostprotokolle werden nicht angezeigt. In diesem Fall müssen Sie den Host als Administrator öffnen und einen End-to-End-Connector einrichten. Dieser Schritt lässt sich durch Exportieren eines vorhandenen Connectors und dessen erneuten Import vereinfachen. 

   ![Screenshot: Hostprotokolle](./media/on-premises-ecma-troubleshoot/tshoot-3.png)

* Ich kann den ECMA-Host-Assistenten konfigurieren, aber den ECMA-Hostdienst nicht starten.

   ![Screenshot: Hostdienst](./media/on-premises-ecma-troubleshoot/tshoot-4.png)


## <a name="turn-on-verbose-logging"></a>Aktivieren der ausführlichen Protokollierung 

Standardmäßig ist `switchValue` für den ECMA-Connectorhost auf `Error` festgelegt. Diese Einstellung bedeutet, dass nur Fehlerereignisse protokolliert werden. Wenn Sie die ausführliche Protokollierung für den ECMA-Hostdienst oder -Assistenten aktivieren möchten, legen Sie `switchValue` an beiden Stellen wie gezeigt auf `Verbose` fest.

Dateispeicherort für die ausführliche Dienstprotokollierung: C:\Programme\Microsoft ECMA2Host\Service\Microsoft.ECMA2Host.Service.exe.config
  ```
  <?xml version="1.0" encoding="utf-8"?> 
  <configuration> 
      <startup>  
          <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6" /> 
      </startup> 
      <appSettings> 
        <add key="Debug" value="true" /> 
      </appSettings> 
      <system.diagnostics> 
        <sources> 
      <source name="ConnectorsLog" switchValue="Verbose"> 
            <listeners> 
              <add initializeData="ConnectorsLog" type="System.Diagnostics.EventLogTraceListener, System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" name="ConnectorsLog" traceOutputOptions="LogicalOperationStack, DateTime, Timestamp, Callstack"> 
                <filter type=""/> 
              </add> 
            </listeners> 
          </source> 
          <!-- Choose one of the following switchTrace:  Off, Error, Warning, Information, Verbose --> 
          <source name="ECMA2Host" switchValue="Verbose"> 
            <listeners>  
              <add initializeData="ECMA2Host" type="System.Diagnos
  ```

Dateispeicherort für die ausführliche Assistentenprotokollierung: C:\Programme\Microsoft ECMA2Host\Wizard\Microsoft.ECMA2Host.ConfigWizard.exe.config
  ```
        <source name="ConnectorsLog" switchValue="Verbose"> 
          <listeners> 
            <add initializeData="ConnectorsLog" type="System.Diagnostics.EventLogTraceListener, System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" name="ConnectorsLog" traceOutputOptions="LogicalOperationStack, DateTime, Timestamp, Callstack"> 
              <filter type=""/> 
            </add> 
          </listeners> 
        </source> 
        <!-- Choose one of the following switchTrace:  Off, Error, Warning, Information, Verbose --> 
        <source name="ECMA2Host" switchValue="Verbose"> 
          <listeners> 
            <add initializeData="ECMA2Host" type="System.Diagnostics.EventLogTraceListener, System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" name="ECMA2HostListener" traceOutputOptions="LogicalOperationStack, DateTime, Timestamp, Callstack" /> 
  ```

## <a name="target-attribute-is-missing"></a>Das Zielattribut fehlt. 
Der Bereitstellungsdienst erkennt Attribute in Ihrer Zielanwendung automatisch. Wenn Sie feststellen, dass ein Zielattribut in der Liste der Zielattribute im Azure-Portal fehlt, führen Sie den folgenden Problembehandlungsschritt aus:

 1. Überprüfen Sie die Seite **Attribute auswählen** Ihrer ECMA-Hostkonfiguration, um sicherzustellen, dass das Attribut dafür ausgewählt wurde, für das Azure-Portal verfügbar gemacht zu werden.
 1. Vergewissern Sie sich, dass der ECMA-Hostdienst aktiviert ist. 
 1. Überprüfen Sie die ECMA-Hostprotokolle, um zu bestätigen, dass eine /schemas-Anforderung erfolgt ist, und überprüfen Sie die Attribute in der Antwort. Diese Informationen sind für den Support bei der Problembehandlung nützlich.

## <a name="collect-logs-from-event-viewer-as-a-zip-file"></a>Sammeln von Protokollen aus der Ereignisanzeige als ZIP-Datei

Navigieren Sie zu dem Ordner, in dem der ECMA-Host installiert wurde, indem Sie **Problembehandlung** > **Skripts** auswählen. Führen Sie das Skript `CollectTroubleshootingInfo` als Administrator aus. Sie können damit die Protokolle in einer ZIP-Datei erfassen und exportieren.

## <a name="review-events-in-event-viewer"></a>Überprüfen von Ereignissen in der Ereignisanzeige

Nachdem die Hostschemazuordnung des ECMA-Connectors konfiguriert wurde, starten Sie den Dienst, damit er auf eingehende Verbindungen lauscht. Überwachen Sie dann eingehende Anforderungen.

  1. Wählen Sie das **Startmenü** aus, geben Sie **Ereignisanzeige** ein, und wählen Sie **Ereignisanzeige** aus. 
  1. Erweitern Sie in der **Ereignisanzeige** den Eintrag **Anwendungs- und Dienstprotokolle**, und wählen Sie **Microsoft ECMA2Host-Protokolle** aus. 
  1. Wenn Änderungen vom Connectorhost empfangen werden, werden Ereignisse in das Anwendungsprotokoll geschrieben. 

### <a name="common-errors"></a>Häufige Fehler

| Fehler      | Lösung |
| ----------- | ----------- |
| Die Datei oder Assembly „file:///C:\Program Files\Microsoft ECMA2Host\Service\ECMA\Cache\8b514472-c18a-4641-9a44-732c296534e8\Microsoft.IAM.Connector.GenericSql.dll“ oder eine ihrer Abhängigkeiten konnte nicht geladen werden. Zugriff verweigert.“      | Stellen Sie sicher, dass das Netzwerkdienstkonto für den Cacheordner über Berechtigungen für den „Vollzugriff“ verfügt. |
| Ungültiger LDAP-Stil des DN des Objekts. DN: „username@domain.com“   | Stellen Sie sicher, dass das Kontrollkästchen „DN is Anchor“ (DN ist Anker) auf der Seite „connectivity“ (Konnektivität) des ECMA-Hosts nicht aktiviert ist. Stellen Sie sicher, dass das Kontrollkästchen „autogenerated“ (automatisch generiert) auf der Seite „object types“ (Objekttypen) des ECMA-Hosts aktiviert ist.  Weitere Informationen finden Sie unter [Grundlegendes zu Ankerattributen und DNs (Distinguished Names)](on-premises-application-provisioning-architecture.md#about-anchor-attributes-and-distinguished-names).|

## <a name="understand-incoming-scim-requests"></a>Verstehen eingehender SCIM-Anforderungen

Anforderungen von Azure AD an den Bereitstellungs-Agent und den Connectorhost verwenden das SCIM-Protokoll. Anforderungen, die vom Host an Apps gerichtet werden, verwenden das von der App unterstützte Protokoll. Die Anforderungen vom Host an den Agent und an Azure AD basieren auf SCIM. Weitere Informationen zur SCIM-Implementierung finden Sie unter [Tutorial: Entwickeln eines SCIM-Endpunkts und Planen seiner Bereitstellung in Azure Active Directory](use-scim-to-provision-users-and-groups.md).

Der Azure AD-Bereitstellungsdienst führt im Allgemeinen zu Beginn jedes Bereitstellungszyklus, vor der bedarfsbasierten Bereitstellung und im Rahmen der Testverbindung einen Benutzerabruf für einen [Dummybenutzer](use-scim-to-provision-users-and-groups.md#request-3) aus, um sicherzustellen, dass der Zielendpunkt verfügbar ist und SCIM-kompatible Antworten zurückgibt. 


## <a name="how-do-i-troubleshoot-the-provisioning-agent"></a>Gewusst wie: Problembehandlung für den Bereitstellungs-Agent
Möglicherweise treten die folgenden Fehlerszenarien auf.

### <a name="agent-failed-to-start"></a>Fehler beim Starten des Agents

Möglicherweise wird eine Fehlermeldung angezeigt, die folgendermaßen lautet:

„Der Dienst 'Microsoft Azure AD Connect Provisioning Agent' wurde nicht gestartet. Überprüfen Sie, ob Sie ausreichende Berechtigungen zum Starten von Systemdiensten besitzen.“ 

Dieses Problem wird in der Regel von einer Gruppenrichtlinie verursacht, die verhindert, dass Berechtigungen auf das vom Installationsprogramm erstellte Anmeldekonto für den lokalen NT-Dienst (NT SERVICE\AADConnectProvisioningAgent) angewendet werden. Diese Berechtigungen sind zum Starten des Diensts erforderlich.

Dieses Problem lässt sich wie folgt beheben:

 1. Melden Sie sich beim Server mit einem Administratorkonto an.
 2. Öffnen Sie die Seite **Dienste**, indem Sie zu dieser Seite navigieren oder **Start** > **Ausführen** > **Services.msc** ausführen.
 3. Doppelklicken Sie unter **Dienste** auf **Microsoft Azure AD Connect Provisioning Agent**.
 4. Ändern Sie auf der Registerkarte **Anmelden** den Eintrag für **Dieses Konto** in das Konto eines Domänenadministrators. Starten Sie den Dienst dann neu. 

Mithilfe dieses Tests wird überprüft, ob Ihre Agents über Port 443 mit Azure kommunizieren können. Öffnen Sie einen Browser, und navigieren Sie von dem Server, auf dem der Agent installiert ist, zur obigen URL.

### <a name="agent-times-out-or-certificate-is-invalid"></a>Bei der Ausführung des Agents tritt ein Timeout auf, oder das Zertifikat ist ungültig.

Beim Versuch, den Agent zu registrieren, kann die folgende Fehlermeldung angezeigt werden:

![Screenshot: Timeout des Agents](./media/on-premises-ecma-troubleshoot/tshoot-5.png)

Dieses Problem wird in der Regel dadurch verursacht, dass der Agent keine Verbindung mit dem Hybrididentitätsdienst herstellen kann und die Konfiguration eines HTTP-Proxys erforderlich ist. Konfigurieren Sie zur Problembehebung einen Proxy für den ausgehenden Datenverkehr. 

Der Bereitstellungs-Agent unterstützt die Verwendung von Proxys für ausgehenden Datenverkehr. Dies können Sie durch das Konfigurieren der Konfigurationsdatei des Agent *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config* konfigurieren. Fügen Sie in der Datei gegen Ende unmittelbar vor dem `</configuration>`-Tag die folgenden Zeilen ein.
Ersetzen Sie die Variablen `[proxy-server]` und `[proxy-port]` durch den Namen Ihres Proxyservers und die Portwerte.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
### <a name="agent-registration-fails-with-security-error"></a>Fehler bei der Agent-Registrierung mit Sicherheitsfehler

Möglicherweise erhalten Sie während der Installation des Cloudbereitstellungs-Agent eine Fehlermeldung.

Dieses Problem wird in der Regel dadurch verursacht, dass der Agent die PowerShell-Registrierungsskripts aufgrund von lokalen PowerShell-Ausführungsrichtlinien nicht ausführen kann.

Ändern Sie zur Lösung dieses Problems die PowerShell-Ausführungsrichtlinien auf dem Server. Sie müssen die Computer- und Benutzerrichtlinien auf *Undefined* oder *RemoteSigned* festlegen. Wenn sie auf *Unrestricted* festgelegt sind, wird diese Fehlermeldung angezeigt. Weitere Informationen finden Sie unter [PowerShell-Ausführungsrichtlinien](/powershell/module/microsoft.powershell.core/about/about_execution_policies). 

### <a name="log-files"></a>Protokolldateien

Standardmäßig gibt der Agent minimalistische Fehlermeldungen und Überwachungsinformationen aus. Ablaufverfolgungsprotokolle finden Sie im Ordner „C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace“.

So sammeln Sie weitere Informationen zur Behandlung von Problemen mit dem Agent:

 1. Installieren Sie das PowerShell-Modul „AADCloudSyncTools“ wie unter [Installieren des PowerShell-Moduls AADCloudSyncTools](../../active-directory/cloud-sync/reference-powershell.md#install-the-aadcloudsynctools-powershell-module) beschrieben.
 2. Verwenden Sie das PowerShell-Cmdlet `Export-AADCloudSyncToolsLogs`, um die Informationen zu erfassen. Optimieren Sie Ihre Datensammlung mithilfe der folgenden Switches. Verwendung:

      - Verwenden Sie **SkipVerboseTrace**, um nur aktuelle Protokolle zu exportieren, ohne ausführliche Protokolle zu erfassen (Standard: false).
      - Verwenden Sie **TracingDurationMins**, um eine andere Erfassungsdauer anzugeben (Standard: 3 Minuten).
      - Verwenden Sie **OutputPath**, um einen anderen Ausgabepfad anzugeben (Standard: Ordner „Dokumente“ des Benutzers)

---------------------

Mithilfe von Azure AD können Sie den Bereitstellungsdienst in der Cloud überwachen und Protokolle lokal sammeln. Der Bereitstellungsdienst gibt Protokolle für jeden Benutzer aus, der im Rahmen des Synchronisierungsprozesses ausgewertet wurde. Diese Protokolle können über die [Azure-Portal-Benutzeroberfläche, APIs und Log Analytics](../reports-monitoring/concept-provisioning-logs.md) genutzt werden. Der ECMA-Host generiert Protokolle auch lokal. Er zeigt jede empfangene Bereitstellungsanforderung sowie die an Azure AD gesendete Antwort an.

### <a name="agent-installation-fails"></a>Fehler bei der Agent-Installation
* Der Fehler `System.ComponentModel.Win32Exception: The specified service already exists` gibt an, dass der vorherige ECMA-Host nicht erfolgreich deinstalliert wurde. Deinstallieren Sie die Hostanwendung. Navigieren Sie zu „Programme“, und entfernen Sie den Ordner für den ECMA-Host. Es kann sinnvoll sein, die Konfigurationsdatei zu sichern. 
* Der folgende Fehler gibt an, dass eine Voraussetzung nicht erfüllt wurde. Vergewissern Sie sich, dass .NET 4.7.1 installiert ist.

  ```
    Method Name : <>c__DisplayClass0_1 : 
    RegisterNotLoadedAssemblies Error during load assembly: System.Management.Automation.resources.dll
    --------- Outer Exception Data ---------
    Message: Could not load file or assembly 'file:///C:\Program Files\Microsoft ECMA2Host\Service\ECMA\System.Management.Automation.resources.dll' or one of its dependencies. The system cannot find the file specified.

  ```

### <a name="i-am-getting-an-invalid-ldap-style-dn-error-when-trying-to-configure-the-ecma-connector-host-with-sql"></a>Beim Versuch, den ECMA-Connectorhost mit SQL zu konfigurieren, tritt ein DN-Fehler „Ungültiger LDAP-Stil“ auf.
Standardmäßig erwartet der genericSQL-Connector, dass der DN mithilfe des LDAP-Stils aufgefüllt wird (wenn das Attribut „DN is anchor“ (DN ist Anker) auf der ersten Konnektivitätsseite deaktiviert bleibt). In der obigen Fehlermeldung sehen Sie, dass der DN ein UPN und kein vom Connector erwarteter LDAP-DN ist. 

Um dies zu beheben, stellen Sie sicher, dass **Autogenerated** (Automatisch generiert) auf der Objekttypenseite ausgewählt ist, wenn Sie den Connector konfigurieren.

Weitere Informationen finden Sie unter [Grundlegendes zu Ankerattributen und DNs (Distinguished Names)](on-premises-application-provisioning-architecture.md#about-anchor-attributes-and-distinguished-names).

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Generischer SQL-Connector mit ECMA-Connectorhost](tutorial-ecma-sql-connector.md)
