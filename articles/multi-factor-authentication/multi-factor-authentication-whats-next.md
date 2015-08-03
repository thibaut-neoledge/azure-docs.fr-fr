<properties 
	pageTitle="Azure Multi-Factor Authentication - Étapes suivantes" 
	description="Voici la page Multi-Factor Authentication qui vous indique ce qu’il faut faire avec MFA. Cela inclut les rapports, l’alerte de fraude, le contournement à usage unique, les messages vocaux personnalisés, la mise en cache, les adresses IP approuvées et les mots de passe d’application." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Configuration d’Azure Multi-Factor Authentication

L'article suivant vous aidera à gérer Azure Multi-Factor Authentication, maintenant que vous êtes opérationnel. Il aborde divers sujets qui vous permettent de tirer le meilleur parti d'Azure Multi-Factor Authentication. N'oubliez pas que toutes ces fonctionnalités sont disponibles dans chaque version d’Azure Multi-Factor Authentication.

Fonctionnalité| Description| Éléments
:------------- | :------------- | :------------- | 
[Alerte de fraude](#fraud-alert)|Alerte de fraude peut être configuré et installé de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources.|Installation, configuration et signalement d’une fraude
[Contournement à usage unique](#one-time-bypass) |Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois en « contournant » l'authentification multifacteur.|Installation et configuration d’un contournement à usage unique
[Messages vocaux personnalisés](#custom-voice-messages) |Les messages vocaux personnalisés vous permettent d'utiliser vos propres enregistrements ou messages d’accueil avec l'authentification multifacteur. |Installation et configuration des messages et messages d’accueil personnalisés
[Mise en cache](#caching)|La mise en cache vous permet de définir une période spécifique pour que les tentatives d'authentification suivantes aboutissent automatiquement. |Installation et configuration de la mise en cache de l’authentification.
[Adresses IP approuvées](#trusted-ips)|Adresses IP approuvées est une fonctionnalité d'authentification multifacteur qui permet aux administrateurs d'un client géré ou fédéré de contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet local de l'entreprise.|Configurer et définir les adresses IP qui ne sont pas soumis à l'authentification multifacteur	
[Mots de passe d'application](#app-passwords)|Mots de passe d'application permet à une application, qui ne prend pas en charge MFA, de contourner l'authentification multifacteur et de continuer à fonctionner.|Informations sur les mots de passe d'application.
[Interrompre Multi-Factor Authentication pour les appareils et les navigateurs mémorisés (version préliminaire publique)](#suspend-multi-factor-authentication-for-remembered-devices-and-browsers-public-preview)|Vous permet d’interrompre l'authentification MFA pour un nombre défini de jours après qu'un utilisateur soit parvenu à se connecter à l'aide de MFA.|Informations sur l'activation de cette fonctionnalité et la configuration du nombre de jours.




## Alerte de fraude
Alerte de fraude peut être configuré et installé de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources. Les utilisateurs peuvent signaler une fraude à l’aide de l'application mobile ou de leur téléphone.

### Pour installer et configurer l'alerte de fraude


1. Ouvrez une session sur [http://azure.microsoft.com](http://azure.microsoft.com)
2. Sélectionnez Active Directory à gauche.
3. En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui sur lequel vous souhaitez activer les alertes de fraude et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure Multi-Factor Authentication.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sur la gauche, cliquez sur Paramètres.
6. Sous la section Alerte de fraude, cochez l’option Permettre aux utilisateurs d’envoyer des alertes de fraude.
7. Si vous souhaitez que les utilisateurs soient bloqués lorsqu'une fraude est signalée, cochez l’option Bloquer l'utilisateur lorsqu'une fraude est signalée.
8. Dans la zone de texte située sous Code permettant de signaler une fraude durant le message d'accueil initial, saisissez un code qui peut être utilisé lors de la vérification de l'appel. Si un utilisateur saisit ce code, à la place du signe #, une alerte de fraude est alors signalée.
9. Cliquez sur Enregistrer au bas de la page.

<center>![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)</center>

### Pour signaler une alerte de fraude
Une alerte de fraude peut être déclarée de deux façons. À l’aide de l'application mobile ou de votre téléphone.

### Pour signaler une alerte de fraude à l’aide de l'application mobile
<ol>
<li>Lorsqu'une vérification est envoyée sur votre téléphone, cliquez dessus pour lancer l'application Multi-Factor&#160;Authentication</li>
<li>Pour signaler une fraude, cliquez sur Annuler et Signaler une fraude. Cela fera apparaître une zone de texte qui vous indiquera que le personnel du service informatique</li> de votre organisation va être averti d’une éventuelle fraude. Cliquez sur Signaler une fraude.
<li>Dans l'application, cliquez sur Fermer.</li></ol>

<center>![Cloud](./media/multi-factor-authentication-whats-next/report1.png)</center>

### Pour signaler une alerte de fraude à l’aide de votre téléphone
<ol>
<li>Lorsque vous recevrez un appel de vérification sur votre téléphone, répondez-y.</li>
<li>Pour signaler une fraude, saisissez le code qui a été configuré pour correspondre au signalement de fraude via votre téléphone, à la place du signe #. Vous serez averti qu'une alerte de fraude a été soumise.</li>
<li>Terminez l'appel.</li></ol>

### Pour afficher le rapport de fraude

1. Ouvrez une session sur [http://azure.microsoft.com](http://azure.microsoft.com)
2. Sélectionnez Active Directory à gauche.
3. En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui pour lequel vous souhaitez afficher le rapport d’alertes de fraude et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure Multi-Factor Authentication.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sur la gauche, sous Afficher un rapport, cliquez sur Alerte de fraude.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d'utilisateur spécifiques, les numéros de téléphone et l'état des utilisateurs.
7. Cliquez sur Exécuter. Cela fera apparaître un rapport similaire à celui mentionné ci-dessous. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter le rapport.

## Contournement à usage unique

Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois en « contournant » l'authentification multifacteur. Le contournement est temporaire et expire après le nombre de secondes spécifié. Par conséquent, lorsque l'application mobile ou le téléphone ne reçoit pas de notification ou d’appel téléphonique, vous pouvez activer un contournement à usage unique afin que l'utilisateur puisse accéder à la ressource souhaitée.

### Pour créer un contournement à usage unique

<ol>
<li>Ouvrez une session sur [http://azure.microsoft.com] (http://azure.microsoft.com)</li>
<li>Sélectionnez à gauche Active&#160;Directory .</li>
<li>En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.</li>
<li>Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui qui est associé au répertoire de l'utilisateur pour lequel vous souhaitez créer un contournement à usage unique et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure&#160;Multi-Factor&#160;Authentication.</li>
<li>Dans le portail de gestion Azure&#160;Multi-Factor&#160;Authentication, sur la gauche, sous Administration des utilisateurs, cliquez sur Paramètres.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create1.png)</center>

<li>Dans la page Contournement à usage unique, cliquez sur Nouveau contournement à usage unique.</li>
<li>Saisissez le nom d'utilisateur, le nombre de secondes pendant lesquelles le contournement se produira, la raison du contournement, puis cliquez sur Contourner.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create2.png)</center>

<li>À ce stade, l'utilisateur doit se connecter avant l'expiration du contournement à usage unique.</li>

### Pour afficher le rapport de contournement à usage unique

1. Ouvrez une session sur [http://azure.microsoft.com](http://azure.microsoft.com)
2. Sélectionnez Active Directory à gauche.
3. En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui pour lequel vous souhaitez afficher le rapport d’alertes de fraude et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure Multi-Factor Authentication.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sur la gauche, sous Afficher un rapport, cliquez sur Contournement à usage unique.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d'utilisateur spécifiques, les numéros de téléphone et l'état des utilisateurs.
7. Cliquez sur Exécuter. Cela fera apparaître un rapport similaire à celui mentionné ci-dessous. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter le rapport.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## Messages vocaux personnalisés

Les messages vocaux personnalisés vous permettent d'utiliser vos propres enregistrements ou messages d’accueil avec l'authentification multifacteur. Ils peuvent être utilisés pour compléter ou remplacer les enregistrements Microsoft.

Avant de commencer, tenez compte des informations suivantes :

- Les formats de fichiers pris en charge sont .wav et .mp3.
- La taille limite des fichiers est de 5 Mo.
- La durée des messages d’authentification ne doit pas dépasser les 20 secondes. Toute durée supérieure pourrait entraîner l’échec de la vérification, car l'utilisateur ne répondra peut-être pas avant que le message se termine et que la vérification expire.



### Pour configurer les messages vocaux personnalisés dans Azure Multi-Factor Authentication
<ol>
<li>Créer un message vocal personnalisé à l'aide d'un des formats de fichiers pris en charge. Consultez les recommandations des messages vocaux personnalisés ci-dessous.</li>
<li>Ouvrez une session sur [http://azure.microsoft.com] (http://azure.microsoft.com)</li>
<li>Sélectionnez à gauche Active&#160;Directory .</li>
<li>En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.</li>
<li>Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui sur lequel vous souhaitez configurer le message vocal personnalisé et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure&#160;Multi-Factor&#160;Authentication.</li>
<li>Dans le portail de gestion Azure Multi-Factor&#160;Authentication, sur la gauche, cliquez sur Messages vocaux.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)</center>

<li>Sous la section Messages vocaux, cliquez sur Nouveau message vocal.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)</center>

<li>Dans la page Configurer&#160;: nouveaux messages vocaux, cliquez sur Gérer les fichiers audio.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)</center>

<li>Dans la page Configurer&#160;: fichiers audio, cliquez sur Télécharger un fichier audio.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)</center>

<li>Dans la page Configurer&#160;: télécharger un fichier audio, cliquez sur Parcourir, accédez à votre message vocal, puis cliquez sur Ouvrir.</li>
<li>Ajoutez une description et cliquez sur Télécharger.</li>
<li>Une fois cette opération terminée, vous verrez un message indiquant que vous avez téléchargé le fichier.</li>
<li>Sur la gauche, cliquez sur Messages vocaux.</li>
<li>Sous la section Messages vocaux, cliquez sur Nouveau message vocal.</li>
<li>Dans la liste déroulante des langues, sélectionnez une langue.</li>
<li>Si ce message concerne une application spécifique, spécifiez-la dans la zone de l'application.</li>
<li>Dans le type de message, sélectionnez le type de message qui sera remplacé par notre nouveau message personnalisé.</li>
<li>Dans le menu déroulant du fichier audio, sélectionnez le fichier audio.</li>
<li>Cliquez sur Créer. Vous verrez un message indiquant que vous avez créé un message vocal.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>


## Mise en cache dans Azure Multi-Factor Authentication

La mise en cache vous permet de définir une période spécifique pour que les tentatives d'authentification suivantes aboutissent automatiquement. Grâce à elle, vos utilisateurs ne sont pas obligés d’attendre la réception d’appels téléphoniques ou de SMS lorsqu’ils s'authentifient durant cette période.



### Pour configurer la mise en cache dans Azure Multi-Factor Authentication
<ol>

1. Ouvrez une session sur [http://azure.microsoft.com](http://azure.microsoft.com)
2. Sélectionnez Active Directory à gauche.
3. En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui sur lequel vous souhaitez activer les alertes de fraude et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure Multi-Factor Authentication.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sur la gauche, cliquez sur Mise en cache.
6. Dans la page Configurer la mise en cache, cliquez sur Nouveau Cache
7. Sélectionnez le type de cache et les secondes du cache. Cliquez sur Créer.


<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## Adresses IP de confiance

Adresses IP approuvées est une fonctionnalité d'authentification multifacteur qui permet aux administrateurs d'un client géré ou fédéré de contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet local de l'entreprise. Les fonctionnalités sont disponibles pour les clients Azure AD titulaires d’une licence Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.

 
Type de client Azure AD| Options d’Adresses IP approuvées disponibles
:------------- | :------------- | 
Adresses IP gérées|Plages d'adresses IP spécifiques : les administrateurs peuvent spécifier une plage d'adresses IP pouvant contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet de l’entreprise.
Adresses IP fédérées|<li>Tous les utilisateurs fédérés : tous les utilisateurs fédérés, qui se connectent au sein de l'organisation, contourneront l'authentification multifacteur à l'aide d'une revendication émise par AD FS.</li><li>Plages d'adresses IP spécifiques : les administrateurs peuvent spécifier une plage d'adresses IP pouvant contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet de l'entreprise.

Ce contournement ne fonctionne qu’à partir de l'intranet d'une entreprise. Par exemple, si vous n'avez sélectionné que les utilisateurs fédérés, et qu’un utilisateur se connecte en dehors de l'intranet de l'entreprise, cet utilisateur devra s'authentifier à l'aide de l'authentification multifacteur, même si celui-ci présente une revendication AD FS. Le tableau suivant vous indique lorsque l’authentification multifacteur et les mots de passe d’application sont requis au sein de votre réseau d'entreprise et en dehors de votre réseau d'entreprise lorsque Adresses IP approuvées est activé.


|Adresses IP approuvées activé| Adresses IP approuvées désactivé
:------------- | :------------- | :------------- | 
Au sein du réseau d'entreprise|Pour les flux de navigateur, l’authentification multifacteur n’est PAS obligatoire.|Pour les flux de navigateur, l’authentification multifacteur est obligatoire.
|Pour les applications clientes riches, les mots de passe traditionnels fonctionnent si l'utilisateur n'a créé aucun mot de passe d'application. Une fois qu'un mot de passe d’application a été créé, les mots de passe d'application sont obligatoires.|Pour les applications clientes riches, les mots de passe d'application sont obligatoires
En dehors du réseau d'entreprise|Pour les flux de navigateur, l'authentification multifacteur est obligatoire.|Pour les flux de navigateur, l'authentification multifacteur est obligatoire.
|Pour les applications clientes riches, les mots de passe d'application sont obligatoires.|Pour les applications clientes riches, les mots de passe d'application sont obligatoires.

### Pour activer Adresses IP approuvées

 
<ol>
<li>Connectez-vous au portail de gestion Azure.</li>
<li>Cliquez à gauche sur Active&#160;Directory.</li>
<li>Sous Directory, cliquez sur le répertoire sur lequel vous souhaitez configurer Adresses IP approuvées.</li>
<li>Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.</li>
<li>Dans la section Authentification multifacteur, cliquez sur Gérer les paramètres de service.</li>
<li>Dans la page Paramètres de service, sous Adresses IP approuvées, sélectionnez : <ul> <li>pour les demandes effectuées par des utilisateurs fédérés provenant de mon intranet : tous les utilisateurs fédérés qui se connectent à partir du réseau d'entreprise contourneront l'authentification multifacteur à l'aide d'une revendication émise par AD FS.

<li>Pour les demandes provenant d'une plage spécifique d'adresses IP publiques, saisissez les adresses IP dans les zones de texte à l'aide de la notation CIDR. Par exemple : xxx.xxx.xxx.0/24 pour les adresses IP dans la plage xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 pour une adresse IP unique. Vous pouvez saisir jusqu'à 12 plages d'adresses IP.</li></ul>


<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>


<li>Cliquez sur Enregistrer.</li>
<li>Une fois les mises à jour appliquées, cliquez sur Fermer.</li>
## Mots de passe d'application

Dans certaines applications, telles qu’Office 2010 (ou version antérieure) et Apple Mail, vous ne pouvez pas utiliser l’authentification multifacteur. Pour utiliser ces applications, vous devrez utiliser des « mots de passe d’application » à la place de votre mot de passe traditionnel. Le mot de passe d'application permet à l’application de contourner l'authentification multifacteur et de continuer à fonctionner.

>[AZURE.NOTE]Authentification moderne pour les clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prennent désormais en charge de nouveaux protocoles d’authentification et ils peuvent être activés pour prendre en charge Multi-Factor Authentication. Ainsi, une fois activés, les mots de passe d’application ne sont pas obligatoires avec les clients Office 2013. Pour plus d'informations, consultez la rubrique [Version préliminaire publique de l'authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### Informations à connaître sur les mots de passe d’application

Voici ce qu’il faut absolument savoir sur les mots de passe d'application.

Expérience d'authentification|Pour les applications avec navigateur|Pour les applications sans navigateur
:------------- | :------------- | :------------- 
|<ul><li>Le premier facteur d'authentification est exécuté localement</li><li>Le second facteur est une méthode effectuée par téléphone via l'identité de Cloud.</li>|<ul><li>Les administrateurs et utilisateurs peuvent utiliser des mots de passe d'application pour se connecter.

- Les utilisateurs peuvent avoir plusieurs mots de passe d’application, ce qui augmente leur surface d'exposition au vol. Puisque les mots de passe d'application sont difficiles à mémoriser, cela peut encourager les utilisateurs à les noter. Ceci n'est pas recommandé et doit être proscrit, car pour se connecter, l’utilisateur n’a besoin que d’un mot de passe d’application.
- Les applications qui mettent en cache des mots de passe et les utilisent dans des scénarios locaux, peuvent échouer, car le mot de passe d'application ne sera pas connu en dehors de l'id d'organisation. Des courriers électroniques Exchange sont, par exemple, stockés localement, mais la messagerie archivée se trouve dans le cloud. Le même mot de passe ne fonctionnera pas.
- Le mot de passe est généré automatiquement et il n'est pas fourni par l'utilisateur. Le mot de passe automatiquement généré est en effet beaucoup plus difficile à pirater et bien mieux sécurisé.
- À l’heure actuelle, un utilisateur peut posséder jusqu’à 40 mots de passe. Il vous faudra supprimer l’un de vos mots de passe d’application existants pour pouvoir en créer un nouveau.


### Recommandations en matière d'affectation de noms pour les mots de passe d'application
Les noms des mots de passe d'application doivent refléter l’appareil sur lequel ils seront utilisés. Si vous disposez, par exemple, d’un ordinateur portable qui contient des applications sans navigateur, telles qu’Outlook, Word et Excel, vous ne devez créer qu’un seul mot de passe d’application intitulé Ordinateur portable et l'utiliser dans toutes ces applications. Vous avez la possibilité de créer des mots de passe distincts pour toutes ces applications, mais ceci n'est pas conseillé. Il est recommandé de n'utiliser qu’un seul mot de passe par appareil.


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### Mots de passe d'application fédérés (SSO)
Azure AD prend en charge la fédération avec les services de domaine Windows Server Active Directory (AD DS) locaux. Si votre organisation est fédérée (SSO) avec Azure AD et que vous vous apprêtez à utiliser Azure Multi-Factor Authentication, voici ce que vous devez absolument savoir à propos de l'utilisation des mots de passe d'application. Ceci s'applique uniquement aux clients fédérés (SSO).

- Le mot de passe est vérifié par Azure AD et il contourne ainsi la fédération. La fédération n'est utilisée activement que lorsque vous configurez le mot de passe d’application.
- Pour les utilisateurs fédérés (SSO), nous n’accéderons jamais au fournisseur d'identité (IdP), contrairement au flux passif. Les mots de passe seront stockés dans l'id d'organisation. Si l'utilisateur quitte l'entreprise, ces informations doivent être stockées en temps réel dans l'id d'organisation à l'aide de DirSync. La désactivation/suppression de compte peut mettre jusqu'à 3 heures à se synchroniser, ce qui peut retarder la désactivation/suppression du mot de passe dans Azure AD.
- Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
- Aucune authentification locale de journalisation/fonctionnalité d’audit n'est disponible pour Mot de passe
- Pour utiliser le client Microsoft Lync 2013, l’utilisateur final doit être très qualifié. Pour découvrir les étapes obligatoires, consultez la rubrique Modification du mot de passe de votre courrier électronique en mot de passe d’application.
- Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d'une combinaison de noms d'utilisateur, de mots de passe et de mots de passe d'application, lors de l'utilisation de l'authentification multifacteur avec les clients, selon l'emplacement de cette dernière. Pour les clients qui s'authentifient auprès d'une infrastructure locale, vous utiliseriez le nom d'utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.

Supposons, par exemple, que vous disposez d'une architecture qui se compose des éléments suivants :

- Vous fédérez votre instance locale d'Active Directory avec Azure AD
- Vous utilisez Exchange Online
- Vous utilisez Lync qui est spécifiquement local
- Vous utilisez Azure Multi-Factor Authentication


<center>![Proofup](./media/multi-factor-authentication-whats-next/federated.png)</center>

 Dans ce cas, vous devez procéder comme suit :

- Lorsque vous vous connectez à Lync, utilisez le nom d'utilisateur et le mot de passe de votre organisation.
- Lorsque vous tentez d'accéder au carnet d'adresses via un client Outlook qui se connecte à Exchange Online, utilisez un mot de passe d’application.

### Autorisation de création de mots de passe d'application
Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d'application. Cette fonctionnalité doit être activée. Pour permettre aux utilisateurs de créer des mots de passe d’application, procédez comme suit.

#### Pour permettre aux utilisateurs de créer des mots de passe d'application



1. Connectez-vous au portail de gestion Azure.
2. Cliquez à gauche sur Active Directory.
3. Sous Directory, cliquez sur le répertoire de l'utilisateur que vous souhaitez activer.
4. En haut de la page, cliquez sur Utilisateurs.
5. En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur.
6. En haut de la section Authentification multifacteur, cliquez sur Paramètres de service.
7. Vérifiez que l’option, située à côté de Permettre aux utilisateurs de créer des mots de passe d'application pour se connecter à des applications sans navigateur, est bien sélectionnée.

<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>

### Création de mots de passe d'application
Les utilisateurs peuvent créer des mots de passe d'application lors de leur inscription initiale. Ils ont la possibilité de les créer à la fin du processus d'inscription.

Les utilisateurs pourront également créer des mots de passe d’application par la suite, en modifiant leurs paramètres dans le portail Azure, le portail Office 365 ou via

### Pour créer des mots de passe d'application dans le portail Office 365
--------------------------------------------------------------------------------


1. Connectez-vous au portail Office 365.
2. Dans l’angle supérieur droit, sélectionnez le widget de paramètres.
3. Sur la gauche, sélectionnez Vérification de sécurité supplémentaire
4. Sur la droite, sélectionnez **Mettre à jour mes numéros de téléphone utilisés pour la sécurité du compte**
5. En haut de la page de vérification, sélectionnez les mots de passe d'application
6. Cliquez sur **Créer**
7. Saisissez un nom pour le mot de passe d’application et cliquez sur **Suivant**
8. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### Pour créer des mots de passe d'application dans le portail Azure
--------------------------------------------------------------------------------
1. Connectez-vous au portail de gestion Azure.
3. En haut de la page, cliquez avec le bouton droit sur votre nom d’utilisateur et sélectionnez Vérification de sécurité supplémentaire.
5. En haut de la page de vérification, sélectionnez les mots de passe d'application
6. Cliquez sur **Créer**
7. Saisissez un nom pour le mot de passe d’application et cliquez sur **Suivant**
8. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.


<center>![Cloud](./media/multi-factor-authentication-whats-next/app2.png)</center>

### Pour créer des mots de passe d'application si vous ne possédez pas d'abonnement Office 365 ou Azure
--------------------------------------------------------------------------------
1. Ouvrez une session sur [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. En haut de la page, sélectionnez le profil.
3. Cliquez avec le bouton droit sur votre nom d'utilisateur et sélectionnez Vérification de sécurité supplémentaire.
5. En haut de la page de vérification, sélectionnez les mots de passe d'application
6. Cliquez sur **Créer**
7. Saisissez un nom pour le mot de passe d’application et cliquez sur **Suivant**
8. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.

<center>![Cloud](./media/multi-factor-authentication-whats-next/myapp.png)</center>

## Interrompre Multi-Factor Authentication pour les appareils et les navigateurs mémorisés (version préliminaire publique)

L’interruption de Multi-Factor Authentication pour les navigateurs et appareils mémorisés est une fonctionnalité qui permet de donner aux utilisateurs la possibilité d’interrompre MFA pour un nombre défini de jours après l'exécution d’une connexion réussie à l'aide de MFA. Cette fonctionnalité gratuite facilite grandement la tâche des utilisateurs de MFA. Toutefois, étant donné que les utilisateurs sont autorisés à interrompre MFA, cette fonctionnalité peut réduire la sécurité du compte.

Pour vous assurer que les comptes d'utilisateur sont sécurisés, vous devez restaurer Multi-Factor Authentication sur leurs appareils pour l’un des scénarios suivants :

- Si leur compte d'entreprise est devenu compromis
- Si un appareil mémorisé a été perdu ou volé

> [AZURE.NOTE]Cette fonctionnalité est implémentée comme un cache de cookie du navigateur. Elle ne fonctionnera pas si les cookies de votre navigateur ne sont pas activés.

### Activation/désactivation de MFA pour les appareils mémorisés et définis

<ol>
<li>Connectez-vous au portail de gestion Azure.</li>
<li>Cliquez à gauche sur Active&#160;Directory.</li>
<li>Dans Active&#160;Directory, cliquez sur le répertoire sur lequel vous souhaitez configurer Interrompre Multi-Factor&#160;Authentication pour les appareils mémorisés.</li>
<li>Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.</li>
<li>Dans la section Authentification multifacteur, cliquez sur Gérer les paramètres de service.</li>
<li>Dans la page Paramètres de service, sous Gérer les paramètres des appareils de l’utilisateur, sélectionnez/désélectionnez l’option **Permettre aux utilisateurs d’interrompre l'authentification multifacteur pour qu’un appareil puisse être mémorisé**.</li>
![Interrompre les appareils](./media/multi-factor-authentication-manage-users-and-devices/suspend.png) <li>Définir le nombre de jours pendant lesquels vous souhaitez autoriser l’interruption. La valeur par défaut est de 14 jours.</li> <li>Cliquez sur Enregistrer.</li> <li>Cliquez sur Fermer.</li>

<!---HONumber=July15_HO4-->