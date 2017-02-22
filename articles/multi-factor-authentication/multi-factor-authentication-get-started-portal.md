---
title: "Portail de l’utilisateur pour le serveur Azure MFA | Microsoft Docs"
description: "Voici la page Azure Multi-Factor Authentication qui explique la prise en main de Azure MFA et du portail de l’utilisateur."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Déploiement du portail de l’utilisateur pour le serveur Azure Multi-Factor Authentication
Le portail de l’utilisateur permet à l’administrateur d’installer et de configurer le portail Azure Multi-Factor Authentication. Le portail de l’utilisateur est un site web IIS qui permet aux utilisateurs de s’inscrire dans Azure Multi-Factor Authentication et de gérer leurs comptes. L’utilisateur peut modifier son numéro de téléphone ou son code PIN, ou décider de contourner Azure Multi-Factor Authentication lors de sa prochaine connexion.

Les utilisateurs se connectent au portail de l’utilisateur grâce à leur nom d’utilisateur et à leur mot de passe, puis effectuent soit un appel de vérification en deux étapes soit répondent à des questions de sécurité pour s’authentifier. Si l’inscription est autorisée, les utilisateurs configurent leur numéro de téléphone et leur code PIN lors de leur première connexion au portail de l’utilisateur.

Les administrateurs du portail de l’utilisateur peuvent être configurés et autorisés à ajouter de nouveaux utilisateurs et à mettre à jour les utilisateurs existants.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Déploiement du portail de l’utilisateur sur le même serveur qu’Azure Multi-Factor Authentication
La configuration suivante est requise pour installer le portail de l’utilisateur sur le même serveur qu’Azure Multi-Factor Authentication :

* IIS, avec asp.net et IIS 6 avec compatibilité de la métabase (pour IIS 7 ou version ultérieure)
* L’utilisateur connecté doit disposer des droits d'administrateur sur l'ordinateur et le domaine le cas échéant,  car le compte doit être autorisé à créer des groupes de sécurité Active Directory.

### <a name="to-deploy-the-user-portal"></a>Déploiement du portail de l’utilisateur
1. Dans le serveur Azure Multi-Factor Authentication, cliquez sur l’icône **Portail de l’utilisateur** dans le menu de gauche, puis cliquez sur le bouton **Installer le portail de l’utilisateur**.
2. Cliquez sur **Next**.
3. Cliquez sur **Suivant**.
4. Si l’ordinateur est joint à un domaine et si Active Directory n’est pas configuré pour sécuriser la communication entre le portail de l’utilisateur et le service Azure Multi-Factor Authentication, l’étape Active Directory s’affiche. Cliquez sur le bouton **Suivant** pour terminer automatiquement cette configuration.
5. Cliquez sur **Suivant**.
6. Cliquez sur **Suivant**.
7. Cliquez sur **Fermer**.
8. Ouvrez un navigateur web sur un ordinateur et accédez à l’URL où le portail de l’utilisateur a été installé (par exemple, https://www.publicwebsite.com/MultiFactorAuth). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Déploiement du portail de l’utilisateur du serveur Azure Multi-Factor Authentication sur un autre serveur
Pour permettre à l’application Microsoft Authenticator de communiquer avec le portail de l’utilisateur, utilisez la configuration suivante : 

* Vous devez utiliser la version 6.0 ou ultérieure du serveur Azure Multi-Factor Authentication.
* Le portail de l’utilisateur doit être installé sur un serveur web orienté Internet exécutant Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x ou une version ultérieure.
* Lorsque vous utilisez IIS 6.x, vérifiez qu’ASP.NET v2.0.50727 est installé, inscrit et **autorisé**.
* Les services de rôle requis lors de l'utilisation d'IIS 7.x ou version ultérieure incluent ASP.NET et une compatibilité avec la métabase IIS 6.
* Le portail de l’utilisateur doit être sécurisé avec un certificat SSL.
* Le Kit de développement logiciel (SDK) du service web Azure Multi-Factor Authentication doit être installé dans IIS 6.x, 7.x ou une version ultérieure sur le serveur hébergeant Azure Multi-Factor Authentication.
* Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication  Web Service doit être sécurisé avec un certificat SSL.
* Le portail de l’utilisateur doit pouvoir se connecter au Kit de développement logiciel (SDK) du service web Azure Multi-Factor Authentication via SSL.
* Le portail de l’utilisateur doit pouvoir s’authentifier auprès du Kit de développement logiciel (SDK) du service web Azure Multi-Factor Authentication à l’aide des informations d’identification d’un compte de service membre du groupe de sécurité « PhoneFactor Admins ». Ce compte et ce groupe de service existent dans Active Directory si le serveur Azure Multi-Factor Authentication s'exécute sur un serveur appartenant à un domaine. Ce compte et ce groupe de service existent localement sur le serveur Azure Multi-Factor Authentication s'il n'est pas joint à un domaine.

Une installation du portail de l'utilisateur sur un serveur autre que le serveur Azure Multi-Factor Authentication nécessite les trois étapes suivantes :

1. Installation du Kit de développement logiciel (SDK) du service web
2. Installation du portail de l'utilisateur
3. Configuration des paramètres du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication

### <a name="step-1-install-the-web-service-sdk"></a>Étape 1 : installer le Kit de développement logiciel (SDK) du service web
Si le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service n'est pas déjà installé sur le serveur Azure Multi-Factor Authentication, accédez à ce serveur et ouvrez le serveur Azure Multi-Factor Authentication. Cliquez sur l’icône **SDK du service Web** puis sur **Installer le SDK du service Web**. Suivez les instructions affichées. 

Le Kit de développement logiciel (SDK) Web Service doit être sécurisé avec un certificat SSL. Un certificat autosigné convient, mais il doit être importé dans le magasin « Autorités de certification racines de confiance » du compte Ordinateur local sur le serveur. Le Kit de développement logiciel (SDK) du service web approuve ce certificat lors de l’établissement de la connexion SSL.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>Étape 2 : installer le portail de l’utilisateur
Avant d’installer le portail de l’utilisateur sur un autre serveur, familiarisez-vous avec les bonnes pratiques suivantes :

* Il est utile d'ouvrir un navigateur web sur le serveur web sur Internet et d’accéder à l'URL du Kit de développement logiciel (SDK) Web Service qui a été saisie dans le fichier web.config. Si le navigateur peut accéder correctement au service Web, il vous invite à saisir des informations d'identification. Saisissez le nom d'utilisateur et le mot de passe qui ont été saisis dans le fichier web.config, exactement comme cela apparaît dans le fichier. Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.
* Si un pare-feu ou un proxy inverse est situé devant le serveur web du portail de l’utilisateur et effectue un déchargement SSL, vous pouvez modifier le fichier web.config du portail de l’utilisateur et ajouter la clé suivante à la section `<appSettings>` pour qu’il utilise le protocole http au lieu de https.

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>Installation du portail de l'utilisateur
1. Ouvrez l'explorateur Windows sur le serveur Azure Multi-Factor Authentication et accédez au dossier où est installé le serveur Azure Multi-Factor Authentication (par exemple C:\Program Files\Multi-Factor Authentication Server). Choisissez la version 32 bits ou 64 bits du fichier d’installation MultiFactorAuthenticationUserPortalSetup comme il convient pour le serveur sur lequel le portail de l'utilisateur sera installé. Copiez le fichier d'installation sur le serveur sur Internet.
2. Sur le serveur Web sur Internet, le fichier d'installation doit être exécuté avec des droits d'administrateur. Le plus simple consiste à ouvrir une invite de commandes en tant qu'administrateur et à accéder à l'emplacement où le fichier d'installation a été copié.
3. Exécutez le fichier d'installation MultiFactorAuthenticationUserPortalSetup64, modifiez le nom du site et du répertoire virtuel si vous le souhaitez.
4. Après avoir terminé l'installation du portail de l'utilisateur, accédez à C:\inetpub\wwwroot\MultiFactorAuth (ou au répertoire approprié basé sur le nom du répertoire virtuel) et modifiez le fichier web.config.
5. Recherchez la clé USE_WEB_SERVICE_SDK et modifiez la valeur de false à true. Recherchez les clés WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD et définissez les valeurs du nom d’utilisateur et du mot de passe du compte de service qui est membre du groupe de sécurité PhoneFactor Admins (consultez la section Configuration requise ci-dessus). Veillez à saisir le nom d'utilisateur et le mot de passe entre guillemets à la fin de la ligne (valeur = «  » / >). Nous vous recommandons d’utiliser un nom d’utilisateur complet (par exemple domaine\nom d’utilisateur ou ordinateur\nom d’utilisateur).
6. Recherchez le paramètre pfup_pfwssdk_PfWsSdk et modifiez la valeur « http://localhost:4898/PfWsSdk.asmx » pour l'URL du Kit de développement logiciel (SDK) Web Service qui s'exécute sur le serveur Azure Multi-Factor Authentication (par exemple https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Cette connexion utilisant SSL, reportez-vous au Kit de développement logiciel (SDK) du service Web par le nom du serveur, et non par l’adresse IP, car le certificat SSL a été émis pour le nom du serveur. Si le nom du serveur n’aboutit pas à une adresse IP du serveur sur Internet, ajoutez une entrée au fichier hosts sur ce serveur pour mapper le nom du serveur Azure Multi-Factor Authentication en son adresse IP. Enregistrez le fichier web.config après que les modifications ont été apportées.

    Pour plus d’informations sur la modification du fichier de configuration, consultez [Sécurisez vos ressources cloud et locales à l’aide du serveur Azure Multi-Factor Authentication avec AD FS dans Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file).

7. Si le site web où le portail de l’utilisateur a été installé (par exemple, le site web par défaut) n’est pas encore lié à un certificat signé publiquement, installez le certificat sur le serveur, ouvrez le gestionnaire IIS et liez le certificat au site web.
8. Ouvrez un navigateur web sur un ordinateur et accédez à l’URL où le portail de l'utilisateur a été installé (par exemple, https://www.publicwebsite.com/MultiFactorAuth). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Étape 3 : configurer les paramètres du portail de l’utilisateur dans le serveur Azure Multi-Factor Authentication
Maintenant que le portail de l’utilisateur est installé, vous devez configurer le serveur Azure Multi-Factor Authentication pour qu’il travaille avec ce dernier.

Le serveur Azure Multi-Factor Authentication fournit plusieurs options pour le portail de l'utilisateur.  Le tableau suivant fournit une liste de ces options, ainsi qu’une explication de leur utilisation.

| Paramètres du portail de l'utilisateur | Description |
|:--- |:--- |
| URL du portail de l'utilisateur | Entrez l’URL hébergeant le portail. |
| Authentification principale | Indiquez le type d’authentification à utiliser lors de la connexion au portail.  Authentification Windows, Radius ou LDAP. |
| Autoriser les utilisateurs à se connecter | Permet aux utilisateurs d’entrer un nom d’utilisateur et un mot de passe dans la page de connexion au portail de l’utilisateur.  Si cette option n’est pas sélectionnée, les zones sont grisées. |
| Autoriser l'inscription utilisateur | Permet à l’utilisateur de s’inscrire dans Multi-Factor Authentication grâce à un écran de configuration qui l’invite à entrer des informations supplémentaires, comme son numéro de téléphone. L'invite pour saisir un téléphone de secours permet aux utilisateurs de spécifier un numéro de téléphone secondaire. L’invite pour saisir un jeton OATH tiers permet aux utilisateurs de spécifier un jeton OATH tiers. |
| Autoriser les utilisateurs à lancer le contournement à usage unique | Autorise les utilisateurs à lancer un contournement à usage unique.  Si un utilisateur configure cette option, celle-ci prendra effet lors de la prochaine connexion de celui-ci. L'invite à saisir les secondes du contournement affiche une zone qui permet à l'utilisateur de modifier la valeur par défaut de 300 secondes. Dans le cas contraire, le contournement à usage unique n'est valable que 300 secondes. |
| Autoriser les utilisateurs à sélectionner la méthode | Permet aux utilisateurs de spécifier leur méthode de contact principale.  Cela peut être un appel téléphonique, un message texte, une application mobile ou un jeton OATH. |
| Autoriser les utilisateurs à sélectionner la langue | Permet aux utilisateurs de modifier la langue utilisée pour l’appel téléphonique, le message texte, l’application mobile ou le jeton OATH. |
| Autoriser les utilisateurs à activer l'application mobile | Permet aux utilisateurs de générer un code d’activation pour terminer le processus d’activation de l’application mobile, utilisé avec le serveur.  Vous pouvez également définir le nombre d’appareils (entre 1 et 10) sur lesquels les utilisateurs peut activer l’application. |
| Utiliser les questions de sécurité de secours | Autorise les questions de sécurité en cas d’échec de la vérification en deux étapes.  Vous pouvez spécifier le nombre de questions de sécurité qui doivent être traitées avec succès. |
| Autoriser les utilisateurs à associer un jeton OATH tiers | Permet aux utilisateurs de spécifier un jeton OATH tiers. |
| Utiliser le jeton OATH de secours | Permet d’utiliser un jeton OATH si la vérification en deux étapes échoue. Vous pouvez également spécifier le délai d'expiration de la session en minutes. |
| Activation de la journalisation | Active la journalisation sur le portail de l’utilisateur. Les fichiers journaux sont situés sous : C:\Program Files\Multi-Factor Authentication Server\Logs. |

L’utilisateur peut voir la plupart de ces paramètres dès qu’ils sont activés et que l’utilisateur se connecte au portail de l’utilisateur.

![Paramètres du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configuration des paramètres du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication
1. Dans le serveur Azure Multi-Factor Authentication, cliquez sur l’icône **Portail de l’utilisateur**. Dans l’onglet Paramètres, saisissez l’URL qui mène au portail de l’utilisateur dans la zone de texte **URL du portail de l’utilisateur**. Cette URL est insérée dans les courriers électroniques qui sont envoyés aux utilisateurs lorsqu’ils sont importés dans le serveur Azure Multi-Factor Authentication, si la fonctionnalité de courrier électronique a été activée.
2. Choisissez les paramètres que vous souhaitez utiliser dans le portail de l'utilisateur. Par exemple, si les utilisateurs sont autorisés à contrôler leurs méthodes d’authentification, vérifiez que l’option **Autoriser les utilisateurs à sélectionner une méthode** est activée, ainsi que les méthodes qu’ils peuvent choisir.
3. Cliquez sur le lien **Aide** dans le coin supérieur droit pour comprendre les paramètres affichés.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Onglet Administrateurs
Cet onglet vous permet simplement d'ajouter des utilisateurs disposant des privilèges d'administrateur.  Lorsque vous ajoutez un administrateur, vous pouvez régler les autorisations qu’il reçoit. Cliquez sur le bouton **Ajouter**, sélectionnez un utilisateur et ses autorisations, puis sur cliquez sur **Ajouter**.

![Administrateurs du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>Questions de sécurité
Cet onglet vous permet de spécifier les questions de sécurité auxquelles les utilisateurs devront répondre si l’option **Utiliser les questions de sécurité de secours** est sélectionnée.  Le serveur Azure Multi-Factor Authentication contient des questions par défaut que vous pouvez utiliser. Vous pouvez en modifier l’ordre ou ajouter vos propres questions.  Si vous ajoutez des questions, vous pouvez également spécifier la langue dans laquelle celles-ci doivent s’afficher.

![Questions de sécurité du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
Utilisez cet onglet pour configurer le portail de l’utilisateur afin qu’il accepte les revendications d’un fournisseur d’identité utilisant SAML.  Vous pouvez spécifier le délai d’expiration de la session, le certificat de vérification et l’URL de redirection après déconnexion.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Adresses IP approuvées
Cet onglet vous permet de spécifier des adresses IP uniques ou des plages d’adresses IP qui peuvent être ajoutées, permettant aux utilisateurs qui se connectent à partir de l’une de ces adresses IP de contourner la vérification en deux étapes.

![Adresses IP de confiance du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Inscription utilisateur libre-service
Si vous souhaitez que vos utilisateurs se connectent et s’inscrivent, vous devez sélectionner les options **Autoriser la connexion des utilisateurs** et **Autoriser l’inscription utilisateur** dans l’onglet Paramètres. N’oubliez pas que les paramètres sélectionnés modifieront l’expérience de connexion des utilisateurs.

Par exemple, lorsqu’un utilisateur se connecte au portail de l’utilisateur pour la première fois, il est dirigé vers la page de configuration d’utilisateur d’Azure Multi-Factor Authentication.  Selon la façon dont vous avez configuré Azure Multi-Factor Authentication, l'utilisateur peut être en mesure de choisir sa méthode d'authentification.  

S’il sélectionne la méthode d’authentification Appel vocal ou si cette méthode a été préconfigurée, la page invite l’utilisateur à entrer son numéro de téléphone principal et son numéro de poste le cas échéant.  Il peut également être autorisé à entrer un numéro de téléphone de secours.  

![Adresses IP de confiance du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si l’utilisateur doit utiliser un code PIN pour s’authentifier, la page l’invite également à créer un code PIN.  Après avoir entré son ou ses numéros de téléphone et son code PIN (le cas échéant), l’utilisateur clique sur le bouton **M’appeler maintenant pour m’authentifier**.  Azure Multi-Factor Authentication vérifie le numéro de téléphone principal indiqué en l’appelant l’utilisateur.  L'utilisateur doit répondre à l'appel, entrer son code PIN (le cas échéant) et appuyer sur la touche # pour passer à l'étape suivante du processus d'inscription automatique.   

Si l’utilisateur sélectionne la méthode d’authentification par SMS ou si cette méthode a été préconfigurée, la page invite l’utilisateur à saisir son numéro de téléphone mobile.  Si l'utilisateur doit utiliser un code PIN pour s'authentifier, la page l'invite également à entrer un code PIN.  Après avoir entré son numéro de téléphone et son code PIN (le cas échéant), l’utilisateur clique sur le bouton **M’envoyer un SMS maintenant pour m’authentifier**.  Azure Multi-Factor Authentication envoie un SMS au téléphone mobile de l’utilisateur.  L’utilisateur reçoit le SMS avec un code secret à usage unique, puis répond au message en renvoyant ce code et son code PIN (le cas échéant).

![SMS du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/text.png)   

Si l’utilisateur sélectionne la méthode de vérification Application mobile, la page invite l’utilisateur à installer l’application Microsoft Authenticator sur son appareil et à générer un code d’activation.  Une fois l’application installée, l’utilisateur clique sur le bouton Générer le code d’activation.    

> [!NOTE]
> Pour utiliser l’application Microsoft Authenticator, l’utilisateur doit activer les notifications Push sur son appareil.

La page affiche alors un code d'activation et une URL, ainsi qu'une image de code-barres.  Si l'utilisateur doit utiliser un code PIN pour s'authentifier, la page l'invite également à entrer un code PIN.  L’utilisateur saisit le code d’activation et l’URL dans l’application Microsoft Authenticator ou utilise le scanneur de codes-barres pour numériser l’image du code-barres, puis il clique sur le bouton Activer.    

Une fois l’activation terminée, l’utilisateur clique sur le bouton **Authenticate Me Now (M’authentifier maintenant)**.  Azure Multi-Factor Authentication effectue une vérification sur l’application mobile de l’utilisateur.  L'utilisateur doit entrer son code PIN (le cas échéant) et appuyer sur le bouton Authentifier dans l'application mobile pour passer à l'étape suivante du processus d'inscription automatique.  

Si les administrateurs ont configuré le serveur Azure Multi-Factor Authentication pour collecter les questions de sécurité et les réponses, l'utilisateur est alors dirigé vers la page Questions de sécurité.  L'utilisateur doit sélectionner quatre questions de sécurité et y répondre.    

![Questions de sécurité du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/secq.png)  

L’inscription automatique de l’utilisateur est à présent terminée et l’utilisateur est connecté au portail de l’utilisateur.  Les utilisateurs peuvent se reconnecter au portail de l’utilisateur à tout moment pour modifier leurs numéros de téléphone, leur code PIN, leurs méthodes d’authentification et leurs questions de sécurité, si les administrateurs les y autorisent.




<!--HONumber=Feb17_HO3-->


