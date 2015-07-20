<properties 
	pageTitle="Déploiement du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication" 
	description="Voici la page Azure Multi-Factor Authentication qui explique la prise en main de Azure MFA et du portail de l’utilisateur." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Déploiement du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication

Le portail de l'utilisateur permet à l'administrateur installer et de configurer le portail utilisateur Azure Multi-Factor Authentication. Le portail de l'utilisateur est un site web IIS qui permet aux utilisateurs de s'inscrire dans Azure Multi-Factor Authentication et de gérer leurs comptes. L’utilisateur peut modifier son numéro de téléphone ou son code PIN, ou contourner Azure Multi-Factor Authentication lors de sa prochaine connexion.

Les utilisateurs se connecteront au portail de l’utilisateur grâce à leur nom d'utilisateur normal et leur mot de passe et effectueront soit un appel Azure Multi-Factor Authentication soit une réponse aux questions de sécurité pour terminer leur authentification. Si l'inscription de l'utilisateur est autorisée, il pourra configurer son numéro de téléphone et un PIN la première fois qu'il se connecte au portail de l’utilisateur.

Les administrateurs du portail de l’utilisateur peuvent être configurés et autorisés à ajouter de nouveaux utilisateurs et à mettre à jour les utilisateurs existants.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Déploiement du portail de l'utilisateur sur le même serveur qu’Azure Multi-Factor Authentication

Les conditions préalables suivantes sont requises pour l'installation du portail de l’utilisateur sur le même serveur qu’Azure Multi-Factor Authentication :

- IIS doit être installé, y compris asp.net et IIS 6 avec la métabase (IIS 7 ou version ultérieure) 
- L’utilisateur connecté doit disposer des droits d'administrateur sur l'ordinateur et le domaine le cas échéant, car le compte doit être autorisé à créer des groupes de sécurité Active Directory.

### Déploiement du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication

<ol>
<li>Dans le serveur Azure Multi-Factor Authentication, cliquez sur l'icône Portail de l’utilisateur dans le menu de gauche puis cliquez sur le bouton Installer le portail de l’utilisateur. <li>Cliquez sur Suivant. <li>Cliquez sur Suivant. <li>Si l'ordinateur est joint à un domaine et que la configuration d'Active Directory pour sécuriser la communication entre le portail de l'utilisateur et le service Azure Multi-Factor Authentication est incomplète, l'étape Active Directory s'affichera. Cliquez sur le bouton Suivant pour terminer automatiquement cette configuration. <li>Cliquez sur Suivant. <li>Cliquez sur Suivant. <li>Cliquez sur Fermer. <li>Ouvrez un navigateur web à partir de n'importe quel ordinateur et accédez à l'URL où le portail de l'utilisateur a été installé (par exemple https://www.publicwebsite.com/MultiFactorAuth). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soient affiché.</li>

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## Déploiement du portail de l'utilisateur d’Azure Multi-Factor Authentication sur un serveur distinct

Pour utiliser l'application Azure Multi-Factor Authentication, les éléments suivants sont requis afin que l'application puisse communiquer avec le portail de l'utilisateur :

Consultez Configuration matérielle et logicielle requise pour la configuration matérielle et logicielle requise :

- Vous devez utiliser la version 6.0 ou ultérieure du serveur Azure Multi-Factor Authentication.
- Le portail de l'utilisateur doit être installé sur un serveur web orienté Internet exécutant Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x ou une version ultérieure.
- Lorsque vous utilisez IIS 6.x, assurez-vous d'ASP.NET v2.0.50727 est installé, inscrit et autorisé.
- Les services de rôle requis lors de l'utilisation d'IIS 7.x ou version ultérieure incluent ASP.NET et une compatibilité avec la métabase IIS 6.
- Le portail de l'utilisateur doit être sécurisé avec un certificat SSL.
- Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service doit être installé dans IIS 6.x, IIS 7.x ou une version ultérieure sur le serveur sur lequel Azure Multi-Factor Authentication est installé.
- Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service doit être sécurisé avec un certificat SSL.
- Le portail de l'utilisateur doit pouvoir se connecter au Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service via SSL.
- Le portail de l'utilisateur doit être en mesure de s'authentifier auprès du Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service en utilisant les informations d'identification d'un compte de service qui est membre d'un groupe de sécurité appelé « Administrateurs PhoneFactor ». Ce compte et ce groupe de service existent dans Active Directory si le serveur Azure Multi-Factor Authentication s'exécute sur un serveur appartenant à un domaine. Ce compte et ce groupe de service existent localement sur le serveur Azure Multi-Factor Authentication s'il n'est pas joint à un domaine.

Une installation du portail de l'utilisateur sur un serveur autre que le serveur Azure Multi-Factor Authentication nécessite les trois étapes suivantes :

1. Installation du Kit de développement logiciel (SDK) du service web
2. Installation du portail de l'utilisateur
3. Configuration des paramètres du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication


### Installation du Kit de développement logiciel (SDK) du service web

Si le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service n'est pas déjà installé sur le serveur Azure Multi-Factor Authentication, accédez à ce serveur et ouvrez le serveur Azure Multi-Factor Authentication. Cliquez sur l'icône Kit de développement logiciel (SDK) Web Service, cliquez sur le bouton Installer le Kit de développement logiciel (SDK) Web Service... et suivez les instructions affichées. Le Kit de développement logiciel (SDK) Web Service doit être sécurisé avec un certificat SSL. Un certificat autosigné peut être ajouté à cet effet, mais il doit être importé dans le magasin « Racine des autorités de certification approuvée » du compte Ordinateur local sur le serveur web du portail de l'utilisateur afin qu'il approuve ce certificat lors de l'initialisation de la connexion SSL.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### Installation du portail de l'utilisateur

Avant d'installer le portail de l'utilisateur sur un serveur distinct, tenez compte des éléments suivants :

- Il est utile d'ouvrir un navigateur web sur le serveur web sur Internet et d’accéder à l'URL du Kit de développement logiciel (SDK) Web Service qui a été saisie dans le fichier web.config. Si le navigateur peut accéder correctement au service web, il vous invite à saisir des informations d'identification. Saisissez le nom d'utilisateur et le mot de passe qui ont été saisis dans le fichier web.config, exactement comme cela apparaît dans le fichier. Assurez-vous qu'aucun avertissement ou erreur de certificat ne soient affiché.
- Si un pare-feu ou un proxy inverse est assis devant le serveur web de portail de l'utilisateur et effectue un déchargement SSL, vous pouvez modifier le fichier web.config du portail de l'utilisateur et ajouter la clé suivante à la section <appSettings> afin que le portail de l'utilisateur puisse utiliser http au lieu de https. <add key="SSL_REQUIRED" value="false"/>

#### Installation du portail de l'utilisateur

<ol>




<li>Ouvrez l'explorateur Windows sur le serveur Azure Multi-Factor Authentication et accédez au dossier où est installé le serveur Azure Multi-Factor Authentication (par exemple C:\Program Files\Multi-Factor Authentication Server). Choisissez la version 32&#160;bits ou 64&#160;bits du fichier d’installation MultiFactorAuthenticationUserPortalSetup comme il convient pour le serveur sur lequel le portail de l'utilisateur sera installé. Copiez le fichier d'installation sur le serveur sur Internet.</li>

<li>Sur le serveur web sur Internet, le fichier d'installation doit être exécuté avec des droits d'administrateur. Le plus simple consiste à ouvrir une invite de commandes en tant qu'administrateur et à accéder à l'emplacement où le fichier d'installation a été copié.</li>

<li>Exécutez le fichier d'installation MultiFactorAuthenticationUserPortalSetup64, modifiez le nom du site et du répertoire virtuel si vous le souhaitez.</li>

<li>Après avoir terminé l'installation du portail de l'utilisateur, accédez à C:\inetpub\wwwroot\MultiFactorAuth (ou au répertoire approprié basé sur le nom du répertoire virtuel) et modifiez le fichier web.config.</li>

<li>Recherchez la clé USE_WEB_SERVICE_SDK et modifiez la valeur de false à true. Recherchez les clés WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD et définissez les valeurs pour le nom d'utilisateur et le mot de passe du compte de service qui est membre du groupe de la sécurité PhoneFactor Admins (voir la section Configuration requise ci-dessus). Veillez à saisir le nom d'utilisateur et le mot de passe entre guillemets à la fin de la ligne (valeur = «&#160;&#160;» / >). Il est recommandé d'utiliser un nom d'utilisateur complet (par exemple domaine\nom d'utilisateur ou ordinateur\nom d'utilisateur).</li>

<li>Recherchez le paramètre pfup_pfwssdk_PfWsSdk et modifiez la valeur «&#160;http://localhost:4898/PfWsSdk.asmx&#160;» pour l'URL du Kit de développement logiciel (SDK) Web Service qui s'exécute sur le serveur Azure Multi-Factor Authentication (par exemple https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Étant donné que SSL est utilisé pour cette connexion, vous devez référencer le Kit de développement logiciel (SDK) Web Service par le nom du serveur et non l'adresse IP, car le certificat SSL aura été émis pour le nom du serveur et l'URL utilisée doit correspondre au nom sur le certificat. Si le nom du serveur n’aboutit pas à une adresse IP du serveur sur Internet, ajoutez une entrée au fichier hosts sur ce serveur pour mapper le nom du serveur Azure Multi-Factor Authentication en son adresse IP. Enregistrez le fichier web.config après que les modifications ont été apportées.</li>

<li>Si le site web sur lequel le portail de l'utilisateur a été installé (par exemple site web par défaut) n'a pas encore été lié avec un certificat signé publiquement, installez le certificat sur le serveur si ce n'est pas déjà fait, ouvrez le gestionnaire IIS et liez le certificat au site web.</li>

<li>Ouvrez un navigateur web à partir de n'importe quel ordinateur et accédez à l'URL où le portail de l'utilisateur a été installé (par exemple https://www.publicwebsite.com/MultiFactorAuth). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soient affiché.</li>

## Configuration des paramètres du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication
Maintenant que le portail est installé, vous devez configurer le serveur Azure Multi-Factor Authentication pour travailler avec le portail.

### Configuration des paramètres du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication




1. Dans le serveur Azure Multi-Factor Authentication, cliquez sur l’icône Portail de l’utilisateur. Sous l'onglet Paramètres, saisissez l'URL qui mène au portail de l'utilisateur dans la zone de texte URL du portail de l’utilisateur. Cette URL sera insérée dans des courriers électroniques qui sont envoyés aux utilisateurs lorsqu'ils sont importés dans le serveur Azure Multi-Factor Authentication si la fonctionnalité de courrier électronique a été activée.
2. Choisissez les paramètres que vous souhaitez utiliser dans le portail de l'utilisateur. Par exemple, si les utilisateurs sont autorisés à contrôler leurs méthodes d'authentification, assurez-vous que l'option Autoriser les utilisateurs à sélectionner la méthode est activée, ainsi que les méthodes qu’ils peuvent choisir.
3. Cliquez sur le lien Aide dans le coin supérieur droit pour comprendre les paramètres affichés.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/config.png)</center>

<!---HONumber=July15_HO2-->