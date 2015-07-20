<properties 
	pageTitle="Prise en main du serveur Azure Multi-Factor Authentication" 
	description="Ceci est la page d'authentification multifacteur Azure qui explique la prise en main du serveur Azure MFA." 
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

# Prise en main du serveur Azure Multi-Factor Authentication




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Maintenant que nous avons déterminé s'il fallait utiliser l'authentification multifacteur sur site, allons-y. Cette page vous indique comment installer le serveur et le configurer avec votre Active Directory local. Si vous avez déjà installé Phonefactor et que vous souhaitez découvrir cette procédure de configuration, consultez la rubrique Mise à niveau vers le serveur Azure Multi-Factor. Si vous souhaitez, par contre, obtenir simplement des informations sur l'installation du service Web, consultez la rubrique Déploiement du service Web de l’application mobile du serveur Azure Multi-Factor Authentication.



## Téléchargement du serveur Azure Multi-Factor Authentication

Vous pouvez télécharger le serveur Azure Multi-Factor Authentication de deux manières différentes : La première consiste à vous connecter au portail Azure et la seconde s’effectue directement sur [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).


### Pour télécharger le serveur Azure Multi-Factor Authentication à partir du portail Azure
--------------------------------------------------------------------------------

1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez à gauche Active Directory.
3. En haut de la page Active Directory, cliquez sur **Fournisseurs Multi-Factor Auth**
4. Cliquez sur **Gérer** au bas de la page.
5. Cliquez sur **Téléchargements**
6. Au-dessus de **Générer les informations d'identification de l'activation**, cliquez sur **Télécharger**
7. Enregistrez le fichier téléchargé.

### Pour télécharger directement le serveur Azure Multi-Factor Authentication
--------------------------------------------------------------------------------

1. Ouvrez une session sur [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).
2. Cliquez sur **Téléchargements**
<center>![Cloud](./media/multi-factor-authentication-get-started-server/download2.png)</center>
3. Au-dessus de **Générer les informations d'identification de l'activation**, cliquez sur **Télécharger** - Laissez cette page ouverte, car vous serez redirigé vers elle.
4. Enregistrez le fichier téléchargé.



## Installation et configuration du serveur Azure Multi-Factor Authentication
Maintenant que vous avez téléchargé le serveur, vous pouvez l'installer et le configurer. Assurez-vous que le serveur que vous installez répond aux exigences suivantes :



Configuration requise du serveur Azure Multi-Factor Authentication|Description|
:------------- | :------------- | 
Matériel|<li>200 Mo d'espace disque</li><li>processeur compatible x32 ou x64</li><li>1 Go de RAM ou plus</li>
Logiciel|<li>Windows Server 2003 ou version ultérieure si l'hôte est un système d’exploitation du serveur</li><li>Windows Vista ou version ultérieure si l'hôte est un système d'exploitation client</li><li>Microsoft .NET 2.0 Framework</li><li>IIS 6.0 ou version ultérieure si vous procédez à l’installation du portail utilisateur ou du Kit de développement logiciel (SDK) du service Web</li>

### Configuration requise du serveur Azure Multi-Factor Authentication
--------------------------------------------------------------------------------
Chaque serveur MFA doit pouvoir communiquer sur les éléments sortants du port 443, à savoir :

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

Si les pare-feu sortants sont limités sur le port 443, les plages d'adresses IP suivantes devront être ouvertes :

Sous-réseau IP|Masque réseau|Plage d’adresses IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Si vous n'utilisez pas les fonctionnalités de confirmation d'événements Azure Multi-Factor Authentication et que les utilisateurs ne s’authentifient pas avec des applications mobiles Multi-Factor Auth depuis des appareils connectés au réseau d'entreprise, les plages d’adresses IP peuvent se limiter aux éléments suivants :


Sous-réseau IP|Masque réseau|Plage d’adresses IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Pour installer et configurer le serveur Azure Multi-Factor Authentication
--------------------------------------------------------------------------------


1. Double-cliquez sur le fichier exécutable. Ceci lancera l'installation.
2. Sur l'écran Sélectionner le dossier d’installation, assurez-vous que le dossier est correct, puis cliquez sur Suivant.
3. Une fois l'installation terminée, cliquez sur Terminer. Ceci lancera l'Assistant de configuration.
4. Sur l'écran de bienvenue de l'Assistant de configuration, cochez l’option **Ignorer l’utilisation de l'Assistant de configuration de l'authentification**, puis cliquez sur **Suivant**. Ceci fermera l'Assistant et lancera le serveur.
<center>![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)</center>

5. Revenez à la page à partir de laquelle vous avez téléchargé le serveur, cliquez sur le bouton **Générer les informations d'identification de l’activation**. Copiez ces informations dans le serveur Azure MFA, dans les zones appropriées et cliquez sur **Activer**.


Pour une installation rapide via l'Assistant de configuration, veuillez suivre les étapes mentionnées ci-dessus. Vous pouvez réexécuter l'Assistant d'authentification en le sélectionnant sur le serveur, dans le menu Outils.



##Importation des utilisateurs à partir d'Active Directory

Maintenant que le serveur est installé et configuré, vous pouvez importer très rapidement des utilisateurs sur le serveur Azure MFA.

### Pour importer des utilisateurs à partir d'Active Directory
--------------------------------------------------------------------------------


1. Sur la gauche du serveur Azure MFA, sélectionnez **Utilisateurs**.
2. En bas de la page, sélectionnez **Importer à partir d'Active Directory**.
3. Vous pouvez désormais rechercher des utilisateurs individuels ou bien le répertoire Active des unités d'organisation, composé lui-même d’utilisateurs. Dans ce cas, nous spécifierons l'unité d'organisation des utilisateurs.
4. Mettez en surbrillance tous les utilisateurs mentionnés sur la droite, puis cliquez sur **Importer**. Vous devriez recevoir un message vous indiquant que l’opération a été réalisée avec succès. Fermez la fenêtre d'importation.

<center>![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)</center>

<!---HONumber=July15_HO2-->