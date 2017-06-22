---
title: "Service Web de l’application mobile du serveur Azure MFA | Microsoft Docs"
description: "L'application Microsoft Authenticator offre une option d'authentification hors bande supplémentaire.  Il permet au serveur MFA d’utiliser des notifications Push pour les utilisateurs."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.contentlocale: fr-fr
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Activation de l'authentification par application mobile avec le serveur Azure Multi-Factor Authentication

L'application Microsoft Authenticator offre une option de vérification hors bande supplémentaire. Au lieu de passer un appel téléphonique automatisé ou de rédiger un SMS à l'utilisateur lors de la connexion, Azure Multi-Factor Authentication envoie une notification à l'application Microsoft Authenticator sur le smartphone ou la tablette de l'utilisateur. L'utilisateur appuie simplement sur **Vérifier** (ou saisit un code PIN et appuie sur « Authentifier ») dans l'application pour compléter leur inscription.

Il est préférable d’utiliser une application mobile pour la vérification en deux étapes si la réception par téléphone n’est pas fiable. Si vous utilisez l’application comme un générateur de jetons OATH, aucune connexion réseau ou Internet n’est nécessaire.

Une installation du portail de l'utilisateur sur un serveur autre que le serveur Azure Multi-Factor Authentication nécessite les étapes suivantes :

1. Installation du Kit de développement logiciel (SDK) du service web
2. Installation du service Web de l’application mobile
3. Configuration des paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication
4. Activation de l’application Microsoft Authenticator pour les utilisateurs finaux

## <a name="requirements"></a>Configuration requise

Pour utiliser l'application Microsoft Authenticator, les éléments suivants sont requis afin que l'application puisse communiquer avec le service Web de l’application mobile :

* Azure Multi-Factor Authentication Server&6;.0 ou version ultérieure
* Installez le service Web de l’application mobile sur un serveur Web sur Internet exécutant Microsoft® [Internet Information Services (IIS) 7.x ou version ultérieure](http://www.iis.net/)
* ASP.NET v4.0.30319 est installé, inscrit et autorisé
* Les services de rôle requis incluent ASP.NET et une compatibilité avec la métabase IIS 6.
* Le service Web de l’application mobile est accessible via une URL publique
* Le service Web de l’application mobile est sécurisé avec un certificat SSL.
* Installez le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service dans IIS 7.x ou version ultérieure sur le même serveur sur lequel Azure Multi-Factor Authentication est installé
* Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service est sécurisé avec un certificat SSL.
* Le service Web de l’application mobile peut se connecter au Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service via SSL
* Mobile App Web Service peut s'authentifier auprès du Kit de développement logiciel (SDK) Azure MFA Web Service en utilisant les informations d'identification d'un compte de service qui est membre du groupe de sécurité « Administrateurs PhoneFactor ». Ce compte et ce groupe de service existent dans Active Directory si le serveur Azure Multi-Factor Authentication s'exécute sur un serveur appartenant à un domaine. Ce compte et ce groupe de service existent localement sur le serveur Azure Multi-Factor Authentication s'il n'est pas joint à un domaine.


## <a name="install-the-web-service-sdk"></a>Installation du Kit de développement logiciel (SDK) du service web
Si le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service n'est pas déjà installé sur le serveur Azure Multi-Factor Authentication (MFA), accédez à ce serveur et ouvrez le serveur Azure MFA.

1. Cliquez sur l’icône du Kit de développement logiciel (SDK) du service Web.
2. Cliquez sur **Installer le SDK du service Web** et suivez les instructions affichées.

Le Kit de développement logiciel (SDK) Web Service doit être sécurisé avec un certificat SSL. Un certificat auto-signé peut être ajouté à cet effet. Importez le certificat dans le magasin « Racine des autorités de certification approuvée » du compte Ordinateur local sur le serveur Web du portail de l'utilisateur afin qu'il approuve ce certificat lors de l'initialisation de la connexion SSL.

![Paramétrage](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>Installation du service Web de l’application mobile
Avant d'installer le service Web de l’application mobile, tenez compte des éléments suivants :

* Si le portail utilisateur Azure MFA est déjà installé sur le serveur sur Internet, le nom d'utilisateur, le mot de passe et l'URL vers le SDK du service Web peuvent être copiés à partir du fichier web.config du portail de l'utilisateur.
* Il est utile d'ouvrir un navigateur web sur le serveur web sur Internet et d’accéder à l'URL du Kit de développement logiciel (SDK) Web Service qui a été saisie dans le fichier web.config. Si le navigateur peut accéder correctement au service Web, il vous invite à saisir des informations d'identification. Saisissez le nom d'utilisateur et le mot de passe qui ont été saisis dans le fichier web.config, exactement comme cela apparaît dans le fichier. Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.
* Si un pare-feu ou un proxy inverse est assis devant le serveur Web du service Web de l’application mobile et effectue un déchargement SSL, vous pouvez modifier le fichier web.config du service Web de l’application mobile afin que le service Web de l’application mobile puisse utiliser http au lieu de https. SSL est toujours requis depuis l’application mobile vers le pare-feu/proxy inverse. Ajoutez la clé suivante à la section \<appSettings\> :

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>Installer le service

1. Ouvrez l'explorateur Windows sur le serveur Azure Multi-Factor Authentication et accédez au dossier où est installé Azure MFA (généralement C:\Program Files\Azure Multi-Factor Authentication). Choisissez la version 32 bits ou 64 bits du fichier d’installation Azure multi-Factor AuthenticationPhoneAppWebServiceSetup. Copiez le fichier d'installation sur le serveur sur Internet.

2. Sur le serveur Web sur Internet, exécutez le fichier d'installation avec des droits d'administrateur. Ouvrez une invite de commandes en tant qu'administrateur et accédez à l'emplacement où le fichier d'installation a été copié.

3. Exécutez le fichier d'installation Multi-Factor AuthenticationMobileAppWebServiceSetup, modifiez le site si vous le souhaitez et raccourcissez le nom du répertoire virtuel en le nommant « PA » par exemple.

  Un nom de répertoire virtuel court est recommandé, car les utilisateurs doivent entrer l'URL du service Web de l’application mobile dans l’appareil mobile lors de l'activation.

4. Après avoir terminé l'installation d’Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, accédez à C:\inetpub\wwwroot\PA (ou au répertoire approprié basé sur le nom du répertoire virtuel) et modifiez le fichier web.config.

5. Recherchez les clés WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD. Définissez les valeurs de nom d’utilisateur et de mot de passe du compte de service membre du groupe de sécurité PhoneFactor Admins. Cela peut être le même compte utilisé comme identité du portail utilisateur Azure Multi-Factor Authentication s'il a été installé précédemment. Veillez à saisir le nom d'utilisateur et le mot de passe entre guillemets à la fin de la ligne (valeur = «  » / >). Utilisez un nom d’utilisateur complet comme domaine\nom d’utilisateur ou ordinateur\nom d’utilisateur.  

6. Recherchez le paramètre pfMobile App Web Service_pfwssdk_PfWsSdk. Modifiez la valeur *http://localhost:4898/PfWsSdk.asmx* pour l'URL du Kit de développement logiciel (SDK) Web Service qui s'exécute sur le serveur Azure Multi-Factor Authentication (par exemple https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).

  SSL étant utilisé pour cette connexion, vous devez référencer le Kit de développement logiciel (SDK) du service Web avec le nom du serveur et non l’adresse IP. Le certificat SSL aura été émis pour le nom du serveur, et l’URL utilisée doit correspondre au nom sur le certificat. Le nom du serveur ne peut pas aboutir à une adresse IP à partir du serveur sur Internet. Dans ce cas, ajoutez une entrée au fichier hosts sur ce serveur pour mapper le nom du serveur Azure Multi-Factor Authentication en son adresse IP. Enregistrez le fichier web.config après que les modifications ont été apportées.

7. Si le site Web sur lequel le service Web de l’application mobile a été installé n'a pas encore été lié avec un certificat signé publiquement, installez le certificat sur le serveur, ouvrez le gestionnaire IIS et liez le certificat au site Web.

8. Ouvrez un navigateur Web à partir de n'importe quel ordinateur et accédez à l'URL où le service Web de l’application mobile a été installé (par exemple https://www.publicwebsite.com/PA). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configuration des paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication
Maintenant que le service Web de l’application mobile est installé, vous devez configurer le serveur Azure Multi-Factor Authentication pour qu’il fonctionne avec le portail.

1. Dans le serveur Azure MFA, cliquez sur l’icône du portail de l’utilisateur. Si les utilisateurs sont autorisés à contrôler leurs méthodes d'authentification, sélectionnez **Application mobile** dans l'onglet Paramètres, sous **Autoriser les utilisateurs à sélectionner la méthode**. Sans cette fonctionnalité activée, les utilisateurs finaux devront contacter votre support technique pour effectuer l'activation pour l'application mobile.
2. Cochez la case **Autoriser les utilisateurs à activer l'application mobile**.
3. Cochez la case **Autoriser l'inscription utilisateur**.
4. Cliquez sur l'icône de l'application mobile.
5. Entrez l'URL qui est utilisée avec le répertoire virtuel qui a été créé lors de l'installation d'Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Un nom de compte peut être entré dans l'espace fourni. Ce nom de société s'affiche dans l'application mobile. Si ce champ est vide, le nom de votre fournisseur Multi-Factor Auth créé dans le portail Azure Classic s'affichera.

<center>![Configuration](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

