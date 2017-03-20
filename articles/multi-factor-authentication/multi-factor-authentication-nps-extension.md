---
title: "Utiliser les serveurs NPS existants pour exploiter les fonctionnalités d’Azure MFA | Microsoft Docs"
description: "L’extension de serveur NPS (Network Policy Server) pour Azure Multi-Factor Authentication est une solution simple permettant d’ajouter des fonctionnalités de vérification en deux étapes basées sur le cloud à votre infrastructure d’authentification existante."
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
ms.date: 02/23/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: e4ef137656c12cf6495a00450eed308ac6a8a872
ms.openlocfilehash: 804e811c0db5f72b6764b3cb120eb5ec8898baac
ms.lasthandoff: 02/28/2017

---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication---public-preview"></a>Intégrer votre infrastructure NPS existante à Azure Multi-Factor Authentication - Version préliminaire publique

L’extension de serveur NPS (Network Policy Server) pour Azure MFA permet d’ajouter des fonctionnalités de MFA basées sur le cloud à votre infrastructure d’authentification à l’aide de vos serveurs existants. Avec l’extension NPS, vous pouvez ajouter des vérifications basées sur des appels téléphoniques, des SMS ou des applications mobiles à votre flux d’authentification existant sans avoir à installer, configurer et gérer les nouveaux serveurs. 
 
Lorsque vous utilisez l’extension NPS pour Azure MFA, le flux d’authentification inclut les composants suivants : 

1. **Le serveur NAS/VPN** reçoit les demandes des clients VPN et les convertit en demandes RADIUS à des serveurs NPS. 
2. **Le serveur NPS** se connecte à Active Directory pour procéder à l’authentification principale pour les demandes RADIUS et, en cas de réussite, les transmet à toutes les extensions installées.  
3. **L’extension NPS** déclenche une demande destinée à Azure MFA pour l’authentification secondaire. Une fois que l’extension reçoit la réponse, et si la demande MFA réussit, elle termine la demande d’authentification en fournissant au serveur NPS des jetons de sécurité qui incluent une revendication MFA, émise par Azure STS.  
4. **Azure MFA** communique avec Azure Active Directory pour récupérer les informations de l’utilisateur et procède à l’authentification secondaire à l’aide d’une méthode de vérification configurée par l’utilisateur.

Le diagramme suivant illustre ce flux de demande d’authentification de niveau supérieur : 

![Diagramme du flux d’authentification](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="prerequisites"></a>Composants requis

L’extension NPS est conçue pour fonctionner avec votre infrastructure existante. Vérifiez que les conditions préalables suivantes sont remplies avant de commencer.

### <a name="licenses"></a>Licences

L’extension NPS pour Azure MFA est disponible pour les clients disposant de [licences pour Azure MFA](multi-factor-authentication.md) (incluses dans Azure AD Premium, EMS ou un abonnement MFA).

### <a name="software"></a>Logiciel

Windows Server 2008 R2 SP1 ou version ultérieure avec le composant NPS activé.

### <a name="libraries"></a>Bibliothèques

-    [Visual C++ Redistributable Packages pour Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Module Microsoft Azure Active Directory pour Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

Tous les utilisateurs de l’extension NPS doivent être synchronisés avec Azure Active Directory à l’aide d’Azure AD Connect et doivent être activés pour l’authentification MFA.

Lorsque vous installez l’extension, vous devez disposer de l’ID de répertoire et des informations d’identification de l’administrateur pour votre client Azure AD. Vous trouverez votre ID de répertoire dans le [portail Azure](https://portal.azure.com). Connectez-vous en tant qu’administrateur, sélectionnez l’icône **Azure Active Directory** sur la gauche, puis cliquez sur **Propriétés**. Copiez le GUID dans la zone **ID répertoire** et enregistrez.

![Identification de votre ID de répertoire dans les propriétés Azure Active Directory](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="install-the-nps-extension"></a>Installer l’extension NPS

> [!IMPORTANT]
> Installez l’extension NPS sur un serveur autre que le point d’accès VPN. 

Pour installer l’extension NPS pour Azure MFA :

1.    [Téléchargez l’extension NPS](https://aka.ms/npsmfa) à partir du Centre de téléchargement Microsoft.
2.    Copiez le fichier binaire sur le serveur NPS (Network Policy Server) que vous souhaitez configurer.
3.    Exécutez le fichier *setup.exe* et suivez les instructions d’installation.

Une fois l’installation terminée, le programme d’installation crée un script PowerShell dans cet emplacement : `C:\Program Files\Microsoft\AzureMfa\Config` (C:\ étant le lecteur d’installation). Ce script PowerShell effectue les opérations suivantes :

-    Vous pouvez créer un certificat auto-signé.
-    Associer la clé publique du certificat au principal du service sur Azure AD.
-    Stocker le certificat dans le magasin de certificats de l’ordinateur local.
-    Accorder l’accès à la clé privée du certificat à l’utilisateur réseau.
-    Redémarrer le serveur NPS.

À moins que vous ne souhaitiez utiliser vos propres certificats (au lieu des certificats auto-signés générés par le script PowerShell), exécutez le script PowerShell pour terminer l’installation.

## <a name="configure-your-nps-extension"></a>Configurer votre extension NPS

Cette section comprend des considérations relatives à la conception ainsi que des suggestions visant à garantir la réussite des déploiements d’extension NPS.

### <a name="configurations-limitations"></a>Limitations des configurations

- L’extension NPS est conçue pour fonctionner avec votre déploiement existant, mais pas avec les nouveaux déploiements. Ainsi, l’extension NPS pour Azure MFA n’inclut pas les outils permettant de migrer les utilisateurs et les paramètres du serveur MFA vers le cloud.

- L’extension NPS utilise l’UPN de l’annuaire Active Directory local pour identifier l’utilisateur sur Azure MFA afin de procéder à l’authentification secondaire. L’extension ne peut pas être configurée pour utiliser un identificateur autre que l’UPN (un ID de connexion alternatif ou un champ AD personnalisé, par exemple).  

### <a name="control-radius-clients-that-require-mfa"></a>Contrôler les clients RADIUS nécessitant l’authentification MFA

Une fois que vous activez l’authentification MFA pour un client RADIUS à l’aide de l’extension NPS, toutes les authentifications de ce client doivent utiliser la méthode MFA. Si vous souhaitez activer l’authentification MFA pour certains clients RADIUS et d’autres non, vous pouvez configurer deux serveurs NPS et installer l’extension sur un seul d’entre eux. Configurez les clients RADIUS pour lesquels vous souhaitez exiger l’authentification MFA de sorte qu’ils envoient des demandes au serveur NPS configuré avec l’extension, et les autres clients RADIUS pour qu’ils les envoient au serveur NPS non configuré avec l’extension.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Anticiper la présence d’utilisateurs qui ne sont pas inscrits pour l’authentification MFA

Si vous avez des utilisateurs qui ne sont pas inscrits pour l’authentification MFA, vous pouvez déterminer ce qui se passe lorsqu’ils tentent de s’authentifier. Utilisez le paramètre de Registre *REQUIRE_USER_MATCH* dans le chemin d’accès au Registre *HKLM\Software\Microsoft\AzureMFA* pour contrôler le comportement de la fonctionnalité. Ce paramètre ne dispose que d’une seule option de configuration :

| Clé | Valeur | Default |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Non défini (équivaut à TRUE) |

L’objectif de ce paramètre est de déterminer l’action à exécuter lorsqu’un utilisateur n’est pas inscrit pour l’authentification MFA. Lorsque la clé n’existe pas, n’est pas définie ou est définie sur TRUE, et si l’utilisateur n’est pas inscrit, l’extension échoue à la demande d’authentification MFA. Lorsque la clé est définie sur FALSE et que l’utilisateur n’est pas inscrit, l’authentification s’effectue sans procéder à l’authentification MFA.

Vous pouvez choisir de créer cette clé et de lui affecter la valeur FALSE lors de l’intégration des utilisateurs. Étant donné que la définition de la clé permet aux utilisateurs qui ne sont pas inscrits pour l’authentification MFA de se connecter sans être testés, vous devez supprimer cette clé avant de passer en production.

## <a name="troubleshooting"></a>résolution des problèmes

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Comment vérifier que le certificat client est installé comme prévu ?

Recherchez le certificat auto-signé créé par le programme d’installation dans le magasin de certificats et vérifiez que la clé privée dispose d’autorisations accordées à l’utilisateur **NETWORK SERVICE**. Le certificat portera un nom d’objet de type **CN \<tenantid\>, UO = Extension Microsoft NPS**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Comment vérifier que mon certificat client est associé à mon client dans Azure Active Directory ?

Ouvrez une invite de commandes PowerShell et exécutez les commandes suivantes :

```
> import-module MSOnline
> Connect-MsolService
> Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Ces commandes impriment tous les certificats qui associent votre client à votre instance de l’extension NPS dans votre session PowerShell. Recherchez votre certificat en exportant votre certificat client en tant que fichier « Codé à base&64; X.509 (.cer) » sans la clé privée et comparez-le à la liste de PowerShell.

Les horodatages Valid-From (Valide à partir de) et Valid-Until (Valide jusqu’à), qui sont dans un format explicite, peuvent être utilisés pour filtrer les certificats de toute évidence non appropriés si la commande en renvoie plusieurs.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Pourquoi mes demandes échouent-elles avec une erreur de jeton ADAL ?

Cette erreur peut être due à plusieurs raisons. Suivez ces étapes pour résoudre le problème :

1. Redémarrez votre serveur NPS.
2. Vérifiez que le certificat client est installé comme prévu.
3. Vérifiez que le certificat est associé à votre client sur Azure AD.
4. Vérifiez que https://login.windows.net/ est accessible depuis le serveur exécutant l’extension.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Pourquoi l’authentification échoue-t-elle avec une erreur dans les journaux HTTP indiquant que l’utilisateur est introuvable ?

Vérifiez qu’AD Connect est en cours d’exécution et que l’utilisateur se trouve dans Windows Active Directory et Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Pourquoi existe-t-il des erreurs de connexion HTTP dans les journaux pour tous mes échecs d’authentification ?

Vérifiez que https://adnotifications.windowsazure.com est accessible à partir du serveur exécutant l’extension NPS.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment intégrer Azure MFA avec [Active Directory](multi-factor-authentication-get-started-server-dirint.md), [l’authentification RADIUS](multi-factor-authentication-get-started-server-radius.md) et [l’authentification LDAP](multi-factor-authentication-get-started-server-ldap.md).

