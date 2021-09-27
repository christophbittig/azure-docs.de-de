---
ms.openlocfilehash: e64446e138b2606f8b868a707d21c6b0c4be6d46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910175"
---
| Programmiersprache/Framework | Projekt auf<br/>GitHub                                                                                                    | Paket                                                                      | Erste Schritte<br/>gestartet                             | Anmelden von Benutzern                                         | Zugriff auf Web-APIs                                                 | Allgemein verfügbar (Generally Available, GA) *oder*<br/>Öffentliche Vorschau<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Tutorial](../articles/active-directory/develop/tutorial-v2-angular-auth-code.md) | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | — | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/maintenance/msal-angularjs)<sup>3</sup>         | [msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Public Preview                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Tutorial](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [msal-core](https://www.npmjs.com/package/@azure/msal-core)    | — | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [msal-react](https://www.npmjs.com/package/@azure/msal-react)         | [Tutorial](../articles/active-directory/develop/tutorial-v2-react.md) | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                          |

<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> Für Bibliotheken in der *öffentlichen Vorschau* gelten [ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen][preview-tos].

<sup>2</sup> [Authentifizierungscodeflow][auth-code-flow] nur mit PCKE (empfohlen) 

<sup>3</sup> nur [impliziter Genehmigungsflow][implicit-flow]

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md
