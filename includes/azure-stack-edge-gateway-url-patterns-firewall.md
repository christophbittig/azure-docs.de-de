---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 4359d8288627f425a4a61fc0d4bf91d63bea174f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378451"
---
|    URL-Muster |    Komponente oder Funktion  |
|-----------------------------------------------------|-----------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.microsoftonline.com |    Azure Stack Edge-Dienst<br>Azure-Servicebus<br>Authentifizierungsdienst: Azure Active Directory                           |
|    http:\//crl.microsoft.com/pki/\*<br>http:\//www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    Zertifikatswiderruf                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com<br>https://www.msftconnecttest.com/connecttest.txt |    Azure-Speicherkonten und Überwachung |
|    http:\//windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Microsoft Update-Server                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    Akamai CDN                                                                                           |
|    http://\*.data.microsoft.com     |    Telemetriedienst in Windows. Informationen finden Sie im Update für Kundenzufriedenheit und Diagnosetelemetrie. |
|    `http://<vault-name>.vault.azure.net:443`     |    Key Vault |
<!--|    http://www.msftconnecttest.com/connecttest.txt  |    Für die Diagnose     ||  |-->   

