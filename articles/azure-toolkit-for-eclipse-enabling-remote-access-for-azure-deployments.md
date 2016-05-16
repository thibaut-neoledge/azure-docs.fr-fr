<properties
    pageTitle="Activation de l’accès à distance pour les déploiements Azure dans Eclipse"
    description="Découvrez comment activer l’accès à distance pour les déploiements Azure à l’aide de la Boîte à outils Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="05/04/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# Activation de l’accès à distance pour les déploiements Azure dans Eclipse #

Pour faciliter le dépannage de vos déploiements, vous pouvez activer et utiliser l’accès à distance pour vous connecter à la machine virtuelle hébergeant votre déploiement. La fonctionnalité d’accès à distance s’appuie sur le protocole RDP (Remote Desktop Protocol). Vous pouvez configurer l’accès à distance pour votre déploiement après l’avoir publié dans Azure ou, si vous utilisez Eclipse avec un système d’exploitation Windows, avant de le publier dans Azure. Notez que, pour vous connecter à la machine virtuelle de votre déploiement dans Azure, vous avez besoin d’un client Bureau à distance compatible avec votre système d’exploitation.

## Comment activer l’accès distant avant le déploiement dans Azure ##

>[AZURE.NOTE] Pour activer l’accès à distance avant de déployer votre application dans Azure, vous devez exécuter Eclipse dans Windows.

L’illustration suivante montre la boîte de dialogue de propriétés **Accès distant** qui permet d’activer l’accès à distance.

![][ic719494]

Il existe deux manières d’afficher la boîte de dialogue de propriétés **Accès distant** :

* Cliquer sur le lien **Avancé** dans la section **Accès distant** de la boîte de dialogue **Publier sur Azure**.
* Ouvrir la boîte de dialogue **Propriétés** de votre projet Azure.

Lorsque vous créez un projet de déploiement Azure, par défaut l’accès à distance n’est pas activé pour celui-ci. Toutefois, vous pouvez facilement activer l’accès à distance en spécifiant le nom d’utilisateur et le mot de passe dans la boîte de dialogue **Publier sur Azure**. Le mot de passe d’accès à distance est chiffré à l’aide de certificats X.509. Si vous n’utilisez pas votre propre certificat, le chiffrement s’appuie sur un certificat auto-signé fourni avec le plug-in Azure pour Eclipse. Ce certificat auto-signé se trouve dans le dossier **cert** de votre projet Azure, stocké à la fois en tant que fichier de certificat public (SampleRemoteAccessPublic.cer) et en tant que fichier de certificat d’échange d’informations personnelles (PFX) (SampleRemoteAccessPrivate.pfx). Ce dernier contient la clé privée du certificat, et il a un mot de passe par défaut, **Password1**. Toutefois, ce mot de passe étant publiquement connu, le certificat par défaut convient uniquement pour l’apprentissage, et non pour un déploiement de production. Si ce n’est à des fins d’apprentissage, quand vous souhaitez activer les sessions à distance pour vos déploiements, vous devez cliquer sur le lien **Avancé** dans la boîte de dialogue **Publier sur Azure** pour spécifier votre propre certificat. Notez que vous devez charger la version PFX du certificat vers votre service hébergé dans le portail de gestion Azure, pour qu’Azure puisse déchiffrer le mot de passe utilisateur.

La suite de ce didacticiel montre comment activer l’accès à distance pour un projet de déploiement Azure créé initialement avec l’accès à distance désactivé. Pour les besoins de ce didacticiel, nous allons créer un certificat auto-signé, dont le fichier .pfx aura le mot de passe de votre choix. Vous pouvez également utiliser un certificat émis par une autorité de certification.

## Comment activer l’accès à distance après le déploiement dans Azure ##

Pour activer l’accès à distance après avoir déployé votre application dans Azure, procédez comme suit :

1. Connectez-vous au portail de gestion Azure avec votre compte Azure.
1. Dans la liste **Cloud Services**, sélectionnez votre service cloud déployé.
1. Dans la page web du service cloud, cliquez sur le lien **Configurer**.
1. Au bas de la page de configuration, cliquez sur le lien **À distance**.
1. Lorsque la boîte de dialogue contextuelle s’affiche :
    1. Spécifiez le rôle pour lequel vous souhaitez activer l’accès à distance.
    1. Cochez la case **Activer le Bureau à distance**.
    1. Spécifiez un nom d’utilisateur et un mot de passe à utiliser pour l’accès à distance.
    1. Sélectionnez le certificat à utiliser.
1. Cliquez sur **OK** 

Un message indique que la modification de votre configuration est en cours et que cela peut prendre quelques minutes. Une fois la modification de la configuration terminée, suivez les étapes de la section **Connexion à distance** plus loin dans cet article.
	
## Comment activer l’accès à distance dans votre package ##

* Dans le volet de l’Explorateur de projets d’Eclipse, cliquez avec le bouton droit sur votre projet Azure, puis sur **Propriétés**.
* Dans la boîte de dialogue **Propriétés**, développez **Azure** dans le volet gauche et cliquez sur **Accès distant**.
* Dans la boîte de dialogue **Accès distant**, assurez-vous que l’option **Activer tous les rôles pour qu’ils acceptent les connexions Bureau à distance avec les informations d’identification de connexion suivantes** soit activée.
* Spécifiez un nom d’utilisateur pour la connexion Bureau à distance.
* Spécifiez et confirmez le mot de passe de l’utilisateur. Les valeurs de nom d’utilisateur et de mot de passe définies dans cette boîte de dialogue seront utilisées lors de l’établissement de votre connexion Bureau à distance. (Notez qu’il s’agit d’un mot de passe distinct de celui de votre fichier PFX.)
* Spécifiez la date d’expiration du compte d’utilisateur.
* Cliquez sur **Nouveau** pour créer un certificat auto-signé. (Vous pouvez aussi sélectionner un certificat de votre espace de travail ou de votre système de fichiers, respectivement à l’aide des boutons **Espace de travail** ou **Système de fichiers**, mais pour les besoins de ce didacticiel nous allons créer un certificat.)
* Dans la boîte de dialogue **Nouveau certificat**, spécifiez et confirmez le mot de passe que vous utiliserez pour votre fichier PFX.
* Acceptez la valeur fournie pour **Nom (CN)** ou utilisez un nom personnalisé.
* Spécifiez le chemin d’accès et le nom de fichier sous lesquels le nouveau certificat sera enregistré au format .cer. Pour cette étape et la suivante, vous pouvez utiliser le dossier **cert** de votre projet Azure, mais vous êtes libre de choisir un autre emplacement. Dans le cadre de ce didacticiel, nous utiliserons **c:\\mycert\\mycert.cer**. (Avant de continuer, créez le dossier **c:\\mycert** ou choisissez un dossier existant.)
* Spécifiez le chemin d’accès et le nom de fichier sous lesquels le nouveau certificat et sa clé privée seront enregistrés au format .pfx. Dans le cadre de ce didacticiel, nous utiliserons **c:\\mycert\\mycert.pfx**. La boîte de dialogue **Nouveau certificat** doit ressembler à ce qui suit (si vous n’utilisez pas **c:\\mycert**, mettez à jour les chemins d’accès de dossier) :
    ![][ic712275]
* Cliquez sur **OK** pour fermer la boîte de dialogue **Nouveau certificat**.
* La boîte de dialogue **Accès distant** doit ressembler à celle-ci :</p>
    ![][ic719495]
* Cliquez sur **OK** pour fermer la boîte de dialogue **Accès distant**.
	
Régénérez votre application, avec la build définie pour le déploiement dans le cloud.

## Connexion à distance ##

Une fois votre instance de rôle prête, vous pouvez vous connecter à distance à la machine virtuelle hébergeant votre application.

* Si vous utilisez Eclipse dans Windows et que vous avez sélectionné l’option **Démarrer le Bureau à distance lors du déploiement** durant le déploiement dans Azure, l’écran d’ouverture de session Connexion Bureau à distance s’affiche au démarrage du déploiement. Lorsque vous êtes invité à entrer le nom d’utilisateur et le mot de passe, entrez les valeurs que vous avez spécifiées pour l’utilisateur distant pour établir la connexion.
* Une autre façon de se connecter à distance consiste à utiliser le <a href="http://go.microsoft.com/fwlink/?LinkID=512959">portail de gestion Azure</a> :
    * Dans l’affichage **Cloud Services** du portail de gestion Azure, cliquez sur votre service cloud, sur **Instances**, sur une instance spécifique, puis sur le bouton **Connexion**. Le bouton **Connexion** s’affiche comme suit dans la barre de commandes : 
    ![][ic659273]  
    >[AZURE.NOTE] Si vous exécutez un système d’exploitation autre que Windows, vous devez utiliser un client Bureau à distance compatible avec votre système d’exploitation et suivre les étapes de configuration de ce client avec les paramètres définis dans le fichier RDP que vous avez téléchargé.
    * Après avoir cliqué sur le bouton **Connexion**, vous êtes invité à ouvrir un fichier RDP. Ouvrez le fichier et suivez les invites. (Vous pouvez aussi enregistrer ce fichier sur votre ordinateur local, puis l’exécuter en double-cliquant dessus pour vous connecter à distance à votre machine virtuelle sans avoir à accéder d’abord au portail de gestion).
    * Lorsque vous êtes invité à entrer le nom d’utilisateur et le mot de passe, entrez les valeurs que vous avez spécifiées pour l’utilisateur distant pour établir la connexion.

## Voir aussi ##

[Kit de ressources Azure pour Eclipse][]

[Création d’une application « Hello World » pour Azure dans Eclipse][]

[Installation de la Boîte à outils Azure pour Eclipse][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure][].

<!-- URL List -->

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une application « Hello World » pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation de la Boîte à outils Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

<!---HONumber=AcomDC_0504_2016-->