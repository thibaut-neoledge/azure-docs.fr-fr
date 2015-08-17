<properties 
	pageTitle="Mise en route du service Web de l’application mobile du serveur MFA" 
	description="L'application Azure Multi-Factor Authentication offre une option d'authentification hors bande supplémentaire. Il permet au serveur MFA d’utiliser des notifications Push pour les utilisateurs." 
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

# Mise en route du service Web de l’application mobile du serveur MFA

L'application Azure Multi-Factor Authentication offre une option d'authentification hors bande supplémentaire. Au lieu de passer un appel téléphonique automatisé ou de rédiger un SMS à l'utilisateur lors de la connexion, Azure Multi-Factor Authentication envoie une notification à l'application Azure Multi-Factor Authentication sur le smartphone ou la tablette de l'utilisateur. L'utilisateur appuie simplement sur « Authentifier » (ou saisit un code PIN et appuie sur « Authentifier ») dans l'application pour se connecter.

Pour utiliser l'application Azure Multi-Factor Authentication, les éléments suivants sont requis afin que l'application puisse communiquer avec le service Web de l’application mobile :

- Consultez Configuration matérielle et logicielle requise pour la configuration matérielle et logicielle requise.
- Vous devez utiliser la version 6.0 ou ultérieure du serveur Azure Multi-Factor Authentication.
- Le service Web de l’application mobile doit être installé sur un serveur Web sur Internet exécutant Microsoft® Internet Information Services (IIS) 6.x, ou IIS 7.x.
- Lorsque vous utilisez IIS 6.x, assurez-vous qu'ASP.NET v2.0.50727 est installé, inscrit et autorisé.
- Les services de rôle requis lors de l'utilisation d'IIS 7.x incluent ASP.NET et une compatibilité avec la métabase IIS 6.
- Le service Web de l’application mobile doit être accessible via une URL publique.
- Le service Web de l’application mobile doit être sécurisé avec un certificat SSL.
- Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service doit être installé dans IIS 6.x, IIS 7.x sur le serveur sur lequel Azure Multi-Factor Authentication est installé.
- Le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service doit être sécurisé avec un certificat SSL.
- Le service Web de l’application mobile doit pouvoir se connecter au Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service via SSL.
- Le service Web de l’application mobile doit être en mesure de s'authentifier auprès du Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service en utilisant les informations d'identification d'un compte de service qui est membre d'un groupe de sécurité appelé « Administrateurs Azure Multi-Factor Authentication ». Ce compte et ce groupe de service existent dans Active Directory si le serveur Azure Multi-Factor Authentication s'exécute sur un serveur appartenant à un domaine. Ce compte et ce groupe de service existent localement sur le serveur Azure Multi-Factor Authentication s'il n'est pas joint à un domaine.


Une installation du portail de l'utilisateur sur un serveur autre que le serveur Azure Multi-Factor Authentication nécessite les trois étapes suivantes :

1. Installation du Kit de développement logiciel (SDK) du service Web
2. Installation du service Web de l’application mobile
3. Configuration des paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication
4. Activation de l'application Azure Multi-Factor Authentication pour les utilisateurs finaux

## Installation du Kit de développement logiciel (SDK) du service Web

Si le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service n'est pas déjà installé sur le serveur Azure Multi-Factor Authentication, accédez à ce serveur et ouvrez le serveur Azure Multi-Factor Authentication. Cliquez sur l'icône Kit de développement logiciel (SDK) Web Service, cliquez sur le bouton Installer le Kit de développement logiciel (SDK) Web Service... et suivez les instructions affichées. Le Kit de développement logiciel (SDK) Web Service doit être sécurisé avec un certificat SSL. Un certificat autosigné peut être ajouté à cet effet, mais il doit être importé dans le magasin « Racine des autorités de certification approuvée » du compte Ordinateur local sur le serveur Web du portail de l'utilisateur afin qu'il approuve ce certificat lors de l'initialisation de la connexion SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## Installation du service Web de l’application mobile
Avant d'installer le service Web de l’application mobile, tenez compte des éléments suivants :

- Si le portail utilisateur Azure Multi-Factor Authentication est déjà installé sur le serveur sur Internet, le nom d'utilisateur, le mot de passe et l'URL vers le SDK du service Web peuvent être copiés à partir du fichier web.config du portail de l'utilisateur. 
- Il est utile d'ouvrir un navigateur Web sur le serveur sur Internet et d’accéder à l'URL du Kit de développement logiciel (SDK) Web Service qui a été saisie dans le fichier web.config. Si le navigateur peut accéder correctement au service Web, il vous invite à saisir des informations d'identification. Saisissez le nom d'utilisateur et le mot de passe qui ont été saisis dans le fichier web.config, exactement comme cela apparaît dans le fichier. Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.
- Si un pare-feu ou un proxy inverse est assis devant le serveur Web du service Web de l’application mobile et effectue un déchargement SSL, vous pouvez modifier le fichier web.config du service Web de l’application mobile et ajouter la clé suivante à la section <appSettings> afin que le service Web de l’application mobile puisse utiliser http au lieu de https. Cependant, SSL est toujours requis depuis l'application mobile vers le pare-feu/proxy inverse. <add key="SSL_REQUIRED" value="false"/> 

### Pour installer le service Web de l’application mobile

<ol>
<li>Ouvrez l'explorateur Windows sur le serveur Azure Multi-Factor Authentication et accédez au dossier où est installé le serveur Azure Multi-Factor Authentication (par exemple C:\Program Files\Azure Multi-Factor Authentication). Choisissez la version 32&#160;bits ou 64&#160;bits du fichier d'installation Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup selon le serveur sur lequel le service Web de l’application mobile est installé. Copiez le fichier d'installation sur le serveur sur Internet.</li>

<li>Sur le serveur Web sur Internet, le fichier d'installation doit être exécuté avec des droits d'administrateur. Le plus simple consiste à ouvrir une invite de commandes en tant qu'administrateur et à accéder à l'emplacement où le fichier d'installation a été copié.</li>

<li>Exécutez le fichier d'installation Multi-Factor AuthenticationMobileAppWebServiceSetup, modifiez le site si vous le souhaitez et raccourcissez le nom du répertoire virtuel en le nommant «&#160;PA&#160;» par exemple. Un nom de répertoire virtuel court est recommandé, car les utilisateurs doivent entrer l'URL du service Web de l’application mobile dans l’appareil mobile lors de l'activation.</li>

<li>Après avoir terminé l'installation d’Azure&#160;Multi-Factor AuthenticationMobileAppWebServiceSetup, accédez à C:\inetpub\wwwroot\PA (ou au répertoire approprié basé sur le nom du répertoire virtuel) et modifiez le fichier web.config.</li>

<li>Recherchez les clés WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD et définissez les valeurs pour le nom d'utilisateur et le mot de passe du compte de service qui est membre du groupe de la sécurité PhoneFactor Admins (voir la section Configuration requise ci-dessus). Cela peut être le même compte utilisé comme identité du portail utilisateur Azure Multi-Factor Authentication s'il a été installé précédemment. Veillez à saisir le nom d'utilisateur et le mot de passe entre guillemets à la fin de la ligne (valeur = «&#160;&#160;» / >). Il est recommandé d'utiliser un nom d'utilisateur complet (par exemple domaine\nom d'utilisateur ou ordinateur\nom d'utilisateur).</li>

<li>Recherchez le paramètre pfMobile App Web Service_pfwssdk_PfWsSdk et modifiez la valeur «&#160;http://localhost:4898/PfWsSdk.asmx&#160;» pour l'URL du Kit de développement logiciel (SDK) Web Service qui s'exécute sur le serveur Azure Multi-Factor Authentication (par exemple https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Étant donné que SSL est utilisé pour cette connexion, vous devez référencer le Kit de développement logiciel (SDK) Web Service par le nom du serveur et non l'adresse IP, car le certificat SSL aura été émis pour le nom du serveur et l'URL utilisée doit correspondre au nom sur le certificat. Si le nom du serveur n’aboutit pas à une adresse IP du serveur sur Internet, ajoutez une entrée au fichier hosts sur ce serveur pour mapper le nom du serveur Azure Multi-Factor Authentication en son adresse IP. Enregistrez le fichier web.config après que les modifications ont été apportées.</li>

<li>Si le site Web sur lequel le service Web de l’application mobile a été installé (par exemple site Web par défaut) n'a pas encore été lié avec un certificat signé publiquement, installez le certificat sur le serveur si ce n'est pas déjà fait, ouvrez le gestionnaire IIS et liez le certificat au site Web.</li>

<li>Ouvrez un navigateur Web à partir de n'importe quel ordinateur et accédez à l'URL où le service Web de l’application mobile a été installé (par exemple https://www.publicwebsite.com/PA). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.</li>

### Configuration des paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication
Maintenant que le service Web de l’application mobile est installé, vous devez configurer le serveur Azure Multi-Factor Authentication pour qu’il fonctionne avec le portail.

#### Pour configurer les paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication

1. Dans le serveur Azure Multi-Factor Authentication, cliquez sur l’icône Portail de l’utilisateur. Si les utilisateurs sont autorisés à contrôler leurs méthodes d'authentification, sous l'onglet Paramètres, sous Autoriser les utilisateurs à sélectionner la méthode, sélectionnez Application mobile. Sans cette fonctionnalité activée, les utilisateurs finaux devront contacter votre support technique pour effectuer l'activation pour l'application mobile.
2. Cochez la case Autoriser les utilisateurs à activer l'application mobile.
3. Cochez la case Autoriser l'inscription utilisateur.
4. Cliquez sur l'icône de l'application mobile.
5. Entrez l'URL qui est utilisée avec le répertoire virtuel qui a été créé lors de l'installation d'Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Un nom de compte peut être entré dans l'espace fourni. Ce nom de société s'affiche dans l'application mobile. Si ce champ est vide, le nom de votre fournisseur Multi-Factor Auth créé dans le portail de gestion Azure s'affichera. 



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

<!---HONumber=August15_HO6-->