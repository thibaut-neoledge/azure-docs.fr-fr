<properties 
	pageTitle="Déploiement du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication" 
	description="Voici la page Azure Multi-Factor Authentication qui explique la prise en main de Azure MFA et du portail de l’utilisateur." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/04/2016" 
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

1. Dans le serveur Azure Multi-Factor Authentication, cliquez sur l'icône Portail de l'utilisateur dans le menu de gauche, puis cliquez sur le bouton Installer le portail de l'utilisateur.
1. Cliquez sur Suivant.
1. Cliquez sur Suivant.
1. Si l'ordinateur est joint à un domaine et que la configuration d'Active Directory pour sécuriser la communication entre le portail de l'utilisateur et le service Azure Multi-Factor Authentication est incomplète, l'étape Active Directory s'affiche. Cliquez sur le bouton Suivant pour terminer automatiquement cette configuration.
1. Cliquez sur Suivant.
1. Cliquez sur Suivant.
1. Cliquez sur Fermer.
1. Ouvrez un navigateur web à partir de n'importe quel ordinateur et accédez à l'URL où le portail de l'utilisateur a été installé (par exemple, https://www.publicwebsite.com/MultiFactorAuth ). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.

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


### Installation du Kit de développement logiciel (SDK) du service Web

Si le Kit de développement logiciel (SDK) Azure Multi-Factor Authentication Web Service n'est pas déjà installé sur le serveur Azure Multi-Factor Authentication, accédez à ce serveur et ouvrez le serveur Azure Multi-Factor Authentication. Cliquez sur l'icône Kit de développement logiciel (SDK) Web Service, cliquez sur le bouton Installer le Kit de développement logiciel (SDK) Web Service... et suivez les instructions affichées. Le Kit de développement logiciel (SDK) Web Service doit être sécurisé avec un certificat SSL. Un certificat autosigné peut être ajouté à cet effet, mais il doit être importé dans le magasin « Racine des autorités de certification approuvée » du compte Ordinateur local sur le serveur web du portail de l'utilisateur afin qu'il approuve ce certificat lors de l'initialisation de la connexion SSL.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### Installation du portail de l'utilisateur

Avant d'installer le portail de l'utilisateur sur un serveur distinct, tenez compte des éléments suivants :

- Il est utile d'ouvrir un navigateur web sur le serveur web sur Internet et d’accéder à l'URL du Kit de développement logiciel (SDK) Web Service qui a été saisie dans le fichier web.config. Si le navigateur peut accéder correctement au service Web, il vous invite à saisir des informations d'identification. Saisissez le nom d'utilisateur et le mot de passe qui ont été saisis dans le fichier web.config, exactement comme cela apparaît dans le fichier. Assurez-vous qu'aucun avertissement ou erreur de certificat ne soit affiché.
- Si un pare-feu ou un proxy inverse est assis devant le serveur web de portail de l’utilisateur et effectue un déchargement SSL, vous pouvez modifier le fichier web.config du portail de l’utilisateur et ajouter la clé suivante à la section <appSettings> afin que le portail de l’utilisateur puisse utiliser http au lieu de https. <add key="SSL\_REQUIRED" value="false"/>

#### Installation du portail de l'utilisateur

1. Ouvrez l'explorateur Windows sur le serveur Azure Multi-Factor Authentication et accédez au dossier où est installé le serveur Azure Multi-Factor Authentication (par exemple C:\\Program Files\\Multi-Factor Authentication Server). Choisissez la version 32 bits ou 64 bits du fichier d’installation MultiFactorAuthenticationUserPortalSetup comme il convient pour le serveur sur lequel le portail de l'utilisateur sera installé. Copiez le fichier d'installation sur le serveur sur Internet.
2. Sur le serveur Web sur Internet, le fichier d'installation doit être exécuté avec des droits d'administrateur. Le plus simple consiste à ouvrir une invite de commandes en tant qu'administrateur et à accéder à l'emplacement où le fichier d'installation a été copié.
3. Exécutez le fichier d'installation MultiFactorAuthenticationUserPortalSetup64, modifiez le nom du site et du répertoire virtuel si vous le souhaitez.
4. Après avoir terminé l'installation du portail de l'utilisateur, accédez à C:\\inetpub\\wwwroot\\MultiFactorAuth (ou au répertoire approprié basé sur le nom du répertoire virtuel) et modifiez le fichier web.config.
5. Recherchez la clé USE\_WEB\_SERVICE\_SDK et modifiez la valeur de false à true. Recherchez les clés WEB\_SERVICE\_SDK\_AUTHENTICATION\_USERNAME et WEB\_SERVICE\_SDK\_AUTHENTICATION\_PASSWORD et définissez les valeurs pour le nom d'utilisateur et le mot de passe du compte de service qui est membre du groupe de la sécurité PhoneFactor Admins (voir la section Configuration requise ci-dessus). Veillez à saisir le nom d'utilisateur et le mot de passe entre guillemets à la fin de la ligne (valeur = «  » / >). Nous vous recommandons d'utiliser un nom d'utilisateur complet (par exemple domaine\\nom d'utilisateur ou ordinateur\\nom d'utilisateur)
6. Recherchez le paramètre pfup\_pfwssdk\_PfWsSdk et modifiez la valeur « http://localhost:4898/PfWsSdk.asmx » pour l’URL du Kit de développement logiciel (SDK) Web Service qui s’exécute sur le serveur Azure Multi-Factor Authentication (par exemple, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Étant donné que SSL est utilisé pour cette connexion, vous devez référencer le Kit de développement logiciel (SDK) Web Service par le nom du serveur et non l'adresse IP, car le certificat SSL aura été émis pour le nom du serveur et l'URL utilisée doit correspondre au nom sur le certificat. Si le nom du serveur n’aboutit pas à une adresse IP du serveur sur Internet, ajoutez une entrée au fichier hosts sur ce serveur pour mapper le nom du serveur Azure Multi-Factor Authentication en son adresse IP. Enregistrez le fichier web.config après que les modifications ont été apportées.
7. Si le site web sur lequel le portail de l'utilisateur a été installé (par exemple site web par défaut) n'a pas encore été lié avec un certificat signé publiquement, installez le certificat sur le serveur si ce n'est pas déjà fait, ouvrez le gestionnaire IIS et liez le certificat au site web.
8. Ouvrez un navigateur web à partir de n'importe quel ordinateur et accédez à l'URL où le portail de l'utilisateur a été installé (par exemple, https://www.publicwebsite.com/MultiFactorAuth ). Assurez-vous qu'aucun avertissement ou erreur de certificat ne soient affiché.



## Configuration des paramètres du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication
Maintenant que le portail est installé, vous devez configurer le serveur Azure Multi-Factor Authentication pour travailler avec le portail.

Le serveur Azure Multi-Factor Authentication fournit plusieurs options pour le portail de l'utilisateur. Le tableau suivant fournit une liste de ces options, ainsi qu'une explication de leur utilisation.

Paramètres du portail de l'utilisateur|Description|
:------------- | :------------- | 
URL du portail de l'utilisateur| Vous permet d'entrer l'URL d'hébergement du portail.
Authentification principale| Vous permet de spécifier le type d'authentification à utiliser lors de la connexion au portail. Authentification Windows, Radius ou LDAP.
Autoriser les utilisateurs à se connecter|Permet aux utilisateurs d'entrer un nom d'utilisateur et un mot de passe sur la page de connexion pour le portail de l'utilisateur. Si cette option n'est pas sélectionnée, les zones sont grisées.
Autoriser l'inscription utilisateur|Permet à l'utilisateur de s'inscrire à l'authentification multifacteur grâce à un écran de configuration qui l'invite à entrer des informations supplémentaires, comme son numéro de téléphone. L'invite pour saisir un téléphone de secours permet aux utilisateurs de spécifier un numéro de téléphone secondaire. L'invite pour saisir un jeton OATH tiers permet aux utilisateurs de spécifier un jeton OATH tiers.
Autoriser les utilisateurs à lancer le contournement à usage unique| Ceci permet aux utilisateurs de lancer le contournement à usage unique. Si un utilisateur configure cette option, celle-ci prendra effet lors de la prochaine connexion de l'utilisateur. L'invite à saisir les secondes du contournement affiche une zone qui permet à l'utilisateur de modifier la valeur par défaut de 300 secondes. Dans le cas contraire, le contournement à usage unique n'est valable que 300 secondes.
Autoriser les utilisateurs à sélectionner la méthode| Permet aux utilisateurs de spécifier leur méthode de contact principale. Cela peut être un appel téléphonique, un message texte, une application mobile ou un jeton OATH.
Autoriser les utilisateurs à sélectionner la langue| Permet à l'utilisateur de modifier la langue utilisée pour l'appel téléphonique, le message texte, l'application mobile ou le jeton OATH.
Autoriser les utilisateurs à activer l'application mobile| Permet aux utilisateurs de générer un code d'activation pour terminer le processus d'activation de l'application mobile utilisée avec le serveur. Vous pouvez également définir le nombre de périphériques sur lesquels les utilisateurs peuvent activer cette option. Entre 1 et 10.
Utiliser les questions de sécurité de secours|Vous permet d'utiliser les questions de sécurité en cas d'échec de l'authentification multifacteur. Vous pouvez spécifier le nombre de questions de sécurité qui doivent être traitées avec succès.
Autoriser les utilisateurs à associer un jeton OATH tiers| Permet aux utilisateurs de spécifier un jeton OATH tiers.
Utiliser le jeton OATH de secours|Permet l'utilisation d'un jeton OATH si l'authentification multifacteur échoue. Vous pouvez également spécifier le délai d'expiration de la session en minutes.
Activation de la journalisation|Active la journalisation sur le portail de l'utilisateur. Les fichiers journaux sont situés sous : C:\\Program Files\\Multi-Factor Authentication Server\\Logs.

L'utilisateur peut voir la plupart de ces paramètres une fois qu'ils sont activés et lorsqu'il se connecte au portail de l'utilisateur.

![Paramètres du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### Configuration des paramètres du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication




1. Dans le serveur Azure Multi-Factor Authentication, cliquez sur l’icône Portail de l’utilisateur. Sous l'onglet Paramètres, saisissez l'URL qui mène au portail de l'utilisateur dans la zone de texte URL du portail de l’utilisateur. Cette URL sera insérée dans des courriers électroniques qui sont envoyés aux utilisateurs lorsqu'ils sont importés dans le serveur Azure Multi-Factor Authentication si la fonctionnalité de courrier électronique a été activée.
2. Choisissez les paramètres que vous souhaitez utiliser dans le portail de l'utilisateur. Par exemple, si les utilisateurs sont autorisés à contrôler leurs méthodes d'authentification, assurez-vous que l'option Autoriser les utilisateurs à sélectionner la méthode est activée, ainsi que les méthodes qu’ils peuvent choisir.
3. Cliquez sur le lien Aide dans le coin supérieur droit pour comprendre les paramètres affichés.

<center>![Configuration](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Onglet Administrateurs
Cet onglet vous permet simplement d'ajouter des utilisateurs disposant des privilèges d'administrateur. Lorsque vous ajoutez un administrateur, vous pouvez régler avec précision les autorisations qu'il reçoit. De cette façon, vous pouvez être sûr de n'accorder que les autorisations nécessaires à l'administrateur. Cliquez simplement sur le bouton Ajouter, sélectionnez l'utilisateur et ses autorisations, puis sur cliquez sur Ajouter.

![Administrateurs du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/admin.png)


## Questions de sécurité
Cet onglet vous permet de spécifier les questions de sécurité auxquelles les utilisateurs devront répondre si l'option Utiliser les questions de sécurité de secours est sélectionnée. Le serveur Azure Multi-Factor Authenticaton contient des questions par défaut que vous pouvez utiliser. Vous pouvez également en modifier l'ordre ou ajouter vos propres questions. Lorsque vous ajoutez des questions, vous pouvez également spécifier la langue dans laquelle elles doivent s'afficher.

![Questions de sécurité du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Sessions précédentes

## SAML
Vous permet de configurer le portail de l'utilisateur afin qu'il accepte les revendications d'un fournisseur d'identité utilisant SAML. Vous pouvez spécifier le délai d'expiration de la session, le certificat de vérification et l'URL de redirection de déconnexion.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## Adresses IP de confiance
Cet onglet vous permet de spécifier des adresses IP uniques ou des plages d'adresses IP qui peuvent être ajoutées, de sorte que si un utilisateur se connecte à partir de l'une de ces adresses IP, l'authentification multifacteur est contournée.

![Adresses IP de confiance du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Inscription utilisateur libre-service
Si vous souhaitez que vos utilisateurs se connectent et s'inscrivent, vous devez sélectionner les options Autoriser les utilisateurs à se connecter et Autoriser l'inscription utilisateur. N'oubliez pas que les paramètres que vous sélectionnez affecteront l'expérience de connexion de l'utilisateur.

Par exemple, lorsqu'un utilisateur se connecte au portail de l'utilisateur et clique sur le bouton de connexion, il est ensuite dirigé vers la page de configuration de l'utilisateur d'Azure Multi-Factor Authentication. Selon la façon dont vous avez configuré Azure Multi-Factor Authentication, l'utilisateur peut être en mesure de choisir sa méthode d'authentification.

S'il sélectionne la méthode d'authentification Appel vocal ou s'il a été préconfiguré pour utiliser cette méthode, la page invite l'utilisateur à entrer son numéro de téléphone principal et le numéro d'extension, le cas échéant. Il peut également être autorisé à entrer un numéro de téléphone de secours.

![Adresses IP de confiance du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si l'utilisateur doit utiliser un code PIN pour s'authentifier, la page l'invite également à entrer un code PIN. Après avoir entré son ou ses numéros de téléphone et son code PIN (le cas échéant), l'utilisateur clique sur le bouton M'appeler maintenant pour m'authentifier. Azure Multi-Factor Authentication effectue une authentification par appel téléphonique au numéro de téléphone principal de l'utilisateur. L'utilisateur doit répondre à l'appel, entrer son code PIN (le cas échéant) et appuyer sur la touche # pour passer à l'étape suivante du processus d'inscription automatique.

Si l'utilisateur sélectionne la méthode d'authentification par texte SMS ou s'il a été préconfiguré pour utiliser cette méthode, la page invite l'utilisateur à saisir son numéro de téléphone mobile. Si l'utilisateur doit utiliser un code PIN pour s'authentifier, la page l'invite également à entrer un code PIN. Après avoir entré son numéro de téléphone et son code PIN (le cas échéant), l'utilisateur clique sur le bouton M'envoyer un SMS maintenant pour m'authentifier. Azure Multi-Factor Authentication effectue une authentification par SMS sur le téléphone mobile de l'utilisateur. L'utilisateur doit recevoir le SMS qui contient un code secret à usage unique (OTP) et il doit répondre au message en envoyant ce code OTP et son code PIN (le cas échéant) pour passer à l'étape suivante du processus d'inscription automatique.

![SMS du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/text.png)

Si l'utilisateur sélectionne la méthode d'authentification par application mobile ou s'il a été préconfiguré pour utiliser cette méthode, la page invite l'utilisateur à installer l'application Azure Multi-Factor Authentication sur son appareil et à générer un code d'activation. Après avoir installé l'application Azure Multi-Factor Authentication, l'utilisateur clique sur le bouton Générer le code d'activation.

>[AZURE.NOTE]Pour utiliser l'application Azure Multi-Factor Authentication, l'utilisateur doit activer les notifications push sur son appareil.

La page affiche alors un code d'activation et une URL, ainsi qu'une image de code-barres. Si l'utilisateur doit utiliser un code PIN pour s'authentifier, la page l'invite également à entrer un code PIN. L'utilisateur saisit le code d'activation et l'URL dans l'application Azure Multi-Factor Authentication ou il utilise le scanneur de codes-barres pour numériser l'image du code-barres, puis il clique sur le bouton Activer.

Une fois l'activation terminée, l'utilisateur clique sur le bouton M'authentifier maintenant. Azure Multi-Factor Authentication effectue une authentification sur l'application mobile de l'utilisateur. L'utilisateur doit entrer son code PIN (le cas échéant) et appuyer sur le bouton Authentifier dans l'application mobile pour passer à l'étape suivante du processus d'inscription automatique.


Si les administrateurs ont configuré le serveur Azure Multi-Factor Authentication pour collecter les questions de sécurité et les réponses, l'utilisateur est alors dirigé vers la page Questions de sécurité. L'utilisateur doit sélectionner quatre questions de sécurité et y répondre.

![Questions de sécurité du portail de l'utilisateur](./media/multi-factor-authentication-get-started-portal/secq.png)

L'inscription automatique de l'utilisateur est à présent terminée et l'utilisateur est connecté au portail de l'utilisateur. Les utilisateurs peuvent se reconnecter au portail de l'utilisateur à tout moment pour modifier leurs numéros de téléphone, les codes PIN, les méthodes d'authentification et les questions de sécurité, si les administrateurs les y autorisent.

 

<!---HONumber=AcomDC_0810_2016-->