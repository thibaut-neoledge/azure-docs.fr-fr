---
title: "Résolution des codes d’erreur liés à l’extension NPS pour Azure MFA | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes liés à l’extension NPS pour Azure Multi-Factor Authentication avec des solutions spécifiques aux messages d’erreur courants"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 95c1eb534b4b51db18a2caf46f17a559243ea036
ms.contentlocale: fr-fr
ms.lasthandoff: 05/02/2017


---

# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Résoudre les messages d’erreur liés à l’extension NPS pour Azure Multi-Factor Authentication

Si vous rencontrez des erreurs au niveau de l’extension NPS pour Azure Multi-Factor Authentication, utilisez cet article pour les résoudre plus rapidement. 

## <a name="troubleshooting-steps-for-common-errors"></a>Étapes de résolution des erreurs courantes

| Code d'erreur | Étapes de dépannage |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Contactez le support technique](#contact-microsoft-support) et mentionnez la liste des étapes de collecte des journaux. Fournissez autant d’informations que possible sur ce qui s’est produit avant l’erreur, y compris l’ID de locataire et le nom d’utilisateur principal. |
| **CLIENT_CERT_INSTALL_ERROR** | Il peut y avoir un problème avec la façon dont le certificat client a été installé ou associé à votre locataire. Suivez les instructions de [Résolution des problèmes liés à l’extension NPS pour MFA](multi-factor-authentication-nps-extension.md#troubleshooting) pour explorer les problèmes de certificat client. |
| **ESTS_TOKEN_ERROR** | Suivez les instructions de [Résolution des problèmes liés à l’extension NPS pour MFA](multi-factor-authentication-nps-extension.md#troubleshooting) pour explorer les problèmes de certificat client et de jeton ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | Le serveur NPS ne reçoit pas les réponses d’Azure MFA. Vérifiez que vos pare-feu sont ouverts en mode bidirectionnel et qu’ils autorisent le trafic vers et depuis https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Sur le serveur qui exécute l’extension NPS, vérifiez que vous pouvez accéder à https://adnotifications.windowsazure.com et https://login.windows.net/. Si ces sites ne se chargent pas, résolvez les problèmes de connectivité sur ce serveur. |
| **REGISTRY_CONFIG_ERROR** | Une clé est manquante dans le registre de l’application. Un [script PowerShell](multi-factor-authentication-nps-extension.md#install-the-nps-extension) n’a peut-être pas été exécuté après l’installation. Ce message d’erreur doit inclure la clé manquante. Assurez-vous que la clé se trouve dans HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Attribut userName\Identifier Radius obligatoire manquant dans la demande Radius. Vérifiez que le serveur NPS reçoit les demandes RADIUS. | Cette erreur indique généralement un problème d’installation. L’extension NPS doit être installée sur des serveurs NPS pouvant recevoir des demandes RADIUS. Les serveurs NPS installés en tant que dépendances de services comme RDG et RRAS ne reçoivent pas les demandes RADIUS. L’extension NPS ne fonctionne pas dans le cadre d’une telle installation et renvoie une erreur, car elle ne peut pas lire les détails de la demande d’authentification. |
| **REQUEST_MISSING_CODE** | En cas d’utilisation de SMS ou de jetons OATH comme méthode d’authentification secondaire, le protocole PAP de chiffrement de mot de passe entre les serveurs NPS et NAS doit être utilisé. L’extension NPS ne prend pas en charge les autres méthodes de chiffrement de mot de passe à ce stade.|
| **USERNAME_CANONICALIZATION_ERROR** | Vérifiez que l’utilisateur se trouve dans votre instance locale d’Active Directory et que le service NPS dispose des autorisations pour accéder au répertoire. Si vous utilisez des approbations inter-forêts, [contactez le support technique](#contact-microsoft-support) pour plus d’informations. |


## <a name="errors-your-users-may-encounter"></a>Erreurs que vos utilisateurs pourraient rencontrer

| Code d'erreur | Message d’erreur | Étapes de dépannage |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Le locataire de l’appelant n’a pas les autorisations d’accès pour l’authentification de l’utilisateur | Vérifiez que le domaine du locataire et le domaine du nom d’utilisateur principal sont identiques. Par exemple, assurez-vous que user@contoso.com essaie de s’authentifier sur le locataire Contoso. Le nom d’utilisateur principal représente un utilisateur valide du locataire dans Azure. |
| **AuthenticationMethodNotConfigured** | La méthode d’authentification spécifiée n’est pas configurée pour l’utilisateur | Invitez l’utilisateur à ajouter ou vérifier ses méthodes de vérification en suivant les instructions de la page [Gérer les paramètres de la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | La méthode d’authentification spécifiée n’est pas prise en charge. | Effectuez la collecte de tous les journaux incluant cette erreur et [contactez le support technique](#contact-microsoft-support). Lorsque vous contactez le support technique, indiquez le nom d’utilisateur et la méthode de vérification secondaire ayant déclenché l’erreur. |
| **BecAccessDenied** | L’appel de MSODS Bec a retourné une erreur de type accès refusé. Le nom d’utilisateur n’est probablement pas défini dans le locataire | L’utilisateur est présent dans le répertoire Active Directory local, mais il n’est pas synchronisé dans Azure AD par AD Connect. Ou bien, l’utilisateur est manquant pour le locataire. Ajoutez l’utilisateur dans Azure AD et invitez-le à ajouter ses méthodes de vérification en suivant les instructions de la page [Gérer les paramètres de la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** ou **StrongAuthenticationServiceInvalidParameter** | Le format du numéro de téléphone est inconnu | Demandez à l’utilisateur de corriger son numéro de téléphone de vérification. |
| **InvalidSession** | La session spécifiée n’est pas valide ou a expiré | La session a duré plus de trois minutes. Vérifiez que l’utilisateur entre le code de vérification ou répond à la notification de l’application dans les trois minutes suivant l’initiation de la demande d’authentification. Si le problème persiste, vérifiez qu’il n’y a aucune latence réseau entre le client, le serveur NAS, le serveur NPS et le point de terminaison Azure MFA.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Aucune méthode d’authentification par défaut n’est configurée pour l’utilisateur | Invitez l’utilisateur à ajouter ou vérifier ses méthodes de vérification en suivant les instructions de la page [Gérer les paramètres de la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-manage-settings.md). Vérifiez que l’utilisateur a choisi une méthode d’authentification par défaut et configuré cette méthode pour son compte. |
| **OathCodePinIncorrect** | Code et PIN saisis erronés. | Cette erreur ne devrait pas survenir avec l’extension NPS. Si l’utilisateur la rencontre, [contactez le support technique](#contact-microsoft-support) pour obtenir de l’aide. |
| **ProofDataNotFound** | Les données de vérification ne sont pas configurées pour la méthode d’authentification sélectionnée. | Invitez l’utilisateur à essayer une nouvelle méthode de vérification ou à en ajouter d’autres en suivant les instructions de la page [Gérer les paramètres de la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-manage-settings.md). Si l’erreur persiste après que vous avez confirmé la bonne configuration de sa méthode de vérification, [contactez le support technique](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Code et PIN saisis erronés. (OneWaySMS) | Cette erreur ne devrait pas survenir avec l’extension NPS. Si l’utilisateur la rencontre, [contactez le support technique](#contact-microsoft-support) pour obtenir de l’aide. |
| **TenantIsBlocked** | Locataire bloqué | [Contactez le support technique](#contact-microsoft-support) et indiquez l’ID de répertoire figurant dans la page de propriétés Azure AD du portail Azure. |
| **UserNotFound** | L’utilisateur spécifié est introuvable | Le locataire n’apparaît plus comme étant actif dans Azure AD. Vérifiez que votre abonnement est actif et que vous disposez des applications internes requises. Assurez-vous également que le locataire spécifié dans l’objet du certificat est le locataire attendu et que le certificat est toujours valide et enregistré sous le principal de service. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Messages qui ne sont pas des erreurs que vos utilisateurs peuvent rencontrer

Il peut arriver que vos utilisateurs reçoivent des messages de Multi-Factor Authentication en cas d’échec de leur demande d’authentification. Ceux-ci ne sont pas des erreurs de configuration. Il s’agit d’avertissements intentionnels expliquant pourquoi une demande d’authentification a été refusée.

| Code d'erreur | Message d’erreur | Étapes recommandées | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Code saisi erroné\Code OATH incorrect | Ce n’est pas une erreur, l’utilisateur a entré un code erroné. | L’utilisateur a entré un code erroné. Demandez-lui de réessayer en demandant un nouveau code ou en se reconnectant. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Limite maximale autorisée de tentatives de saisie de code atteinte | L’utilisateur n’a pas rempli les conditions de vérification correctement un trop grand nombre de fois. En fonction de vos paramètres, un administrateur devra peut-être le débloquer.  |
| **SMSAuthFailedWrongCodeEntered** | Code saisi erroné/Mot de passe SMS à usage unique incorrect | L’utilisateur a entré un code erroné. Demandez-lui de réessayer en demandant un nouveau code ou en se reconnectant. |

## <a name="errors-that-require-support"></a>Erreurs qui nécessitent un support

Si vous rencontrez l’une de ces erreurs, nous vous recommandons de [contacter le support technique](#contact-microsoft-support) pour un diagnostic. Il n’existe pas d’étapes standard à suivre pour résoudre ces erreurs. Lorsque vous contactez le support technique, veillez à inclure autant d’informations que possible sur les étapes ayant conduit à l’erreur, ainsi que les informations de votre locataire.

| Code d'erreur | Message d’erreur |
| ---------- | ------------- |
| **InvalidParameter** | Request ne doit pas être null |
| **InvalidParameter** | ObjectId ne doit pas être null ou vide pour ReplicationScope : {0} |
| **InvalidParameter** | La longueur de CompanyName \{0} \ dépasse la longueur maximale autorisée {{1} |
| **InvalidParameter** | UserPrincipalName ne doit pas être null ou vide |
| **InvalidParameter** | Le TenantId fourni n’est pas dans le bon format |
| **InvalidParameter** | SessionId ne doit pas être null ou vide |
| **InvalidParameter** | Résolution impossible des ProofData de la demande ou de Msods. Paramètre ProofData inconnu |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Étapes suivantes

### <a name="troubleshoot-user-accounts"></a>Résoudre les problèmes relatifs aux comptes utilisateur

Si vos utilisateurs ont [des difficultés avec la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-troubleshoot.md), aidez-les à diagnostiquer eux-mêmes les problèmes. 

### <a name="contact-microsoft-support"></a>Contact Microsoft support

Si vous avez besoin d’une assistance supplémentaire, contactez un professionnel du support via le [Support du serveur Azure Multi-Factor Authentication](https://support.microsoft.com/oas/default.aspx?prid=14947). Lorsque vous nous contactez, veillez à inclure autant d’informations que possible concernant votre problème. Vous pouvez notamment préciser la page sur laquelle vous avez rencontré l’erreur, le code d’erreur spécifique, l’ID de session spécifique, l’ID de l’utilisateur qui a vu l’erreur et les journaux de débogage.

Pour collecter les journaux de débogage pour les diagnostics de support, procédez comme suit : 

1. Ouvrez une invite de commandes administrateur et exécutez les commandes suivantes :

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

2. Reproduisez le problème

3. Arrêtez le suivi à l’aide des commandes suivantes :

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

4. Compressez le contenu du dossier C:\NPS et joignez le fichier compressé au dossier de support.



