<properties
   pageTitle="Hébergement d’applications web dans Docker | Microsoft Azure"
   description="Apprenez à utiliser Visual Studio pour héberger une application web dans un conteneur Docker."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/10/2016"
   ms.author="tarcher" />

# Hébergement d’applications web dans Docker

[Docker](https://www.docker.com/whatisdocker/) est un moteur de conteneur léger, semblable à certains égards à une machine virtuelle, que vous pouvez utiliser pour héberger des applications et des services. Visual Studio prend en charge Docker sur Ubuntu, CoreOS et Windows.

Cet exemple montre comment utiliser l’extension **Visual Studio 2015 Tools pour Docker** pour publier une application ASP.NET 5 sur une machine virtuelle Ubuntu Linux (appelée ici hôte Docker) sur Azure. Vous pouvez également suivre cet exemple pour publier l’application dans un conteneur Windows.

Après la publication de votre application sur un hôte Docker, vous pouvez utiliser les outils en ligne de commande Docker pour interagir avec le conteneur dans lequel l’application a été publiée.

## Créer un conteneur Docker et y publier un projet

Dans la section suivante, vous allez créer un projet d’application web ASP.NET 5, créer un hôte conteneur, puis générer et exécuter le projet d’application web dans un conteneur Docker. Pour commencer, téléchargez et installez [Visual Studio 2015 Tools pour Docker](https://aka.ms/DockerToolsForVS).

### Ajouter un projet d’application web ASP.NET 5

1. Dans le menu Visual Studio, sélectionnez **Fichier > Nouveau > Projet**. 

1. Sous la section **Modèles** de la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#** > **Web**.

1. Sélectionnez **Application Web ASP.NET** et nommez votre nouveau projet. (Les captures d’écran de cet article utilisent le nom par défaut **WebApplication1**).

1. Appuyez sur **OK**.

1. Étant donné que l’application web sera hébergée/exécutée dans Docker, décochez la case **Héberger dans le cloud** si elle est cochée, puis appuyez sur **OK**.

  ![][0]

  C’est à ce stade que vous devez généralement ajouter du code à l’application web pour définir ses fonctionnalités utiles. Pour cet exemple, nous allons simplifier les choses pour nous concentrer sur Docker. (Notez que vous pouvez également choisir d’utiliser une application web ASP.NET 5 existante.)

### Publier le projet
Une fois que le projet a été créé (ou que vous avez ouvert un projet existant), les étapes suivantes vous guident tout au long de la publication du projet sur un conteneur Docker dans Azure.

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

1. Dans la section **Sélectionner une cible de publication** de la boîte de dialogue **Publier le site Web**, appuyez sur **Conteneurs Docker**.

    Si vous ne voyez pas d’option Conteneurs Docker, vérifiez que vous avez installé Visual Studio 2015 Tools pour Docker et que vous avez sélectionné un modèle de site web ASP.NET 5 dans la section précédente.

    ![][1]

    La boîte de dialogue **Sélectionner la machine virtuelle Docker** vous permet de spécifier l’hôte Docker dans lequel vous voulez publier le projet. Vous pouvez créer un hôte Docker, ou choisir une machine virtuelle existante hébergée sur Azure ou ailleurs. Plus loin dans cet exemple, nous créerons un hôte Docker Azure.

1. Si vous êtes déjà connecté à un compte Azure, passez à l’étape 5. Si vous n’êtes pas connecté à un compte, appuyez sur **Ajouter un compte**.

    ![][2]

1. Dans la boîte de dialogue **Connectez-vous à Visual Studio**, entrez le compte de messagerie associé à votre abonnement Azure, puis appuyez sur **Continuer**.

1. Cliquez sur **Nouveau** pour créer une machine virtuelle Docker Azure, puis appuyez sur **OK**.

    ![][3]

    Notez que vous pouvez également choisir d’utiliser un hôte Docker existant. Pour cela, sélectionnez l’hôte dans la liste déroulante **Machines virtuelles Docker Azure existantes** au lieu d’appuyer sur **Nouveau**. Notez que la liste ne se limite pas simplement aux hôtes de conteneur. Toutes les machines virtuelles de votre client Azure sont répertoriées.

    La publication sur un hôte Docker personnalisé est une autre option possible. Pour plus d’informations, consultez la section [Spécifier un hôte Docker personnalisé](#provide-a-custom-docker-host), plus loin dans cette rubrique.

1. Entrez les informations suivantes dans la boîte de dialogue **Créer une machine virtuelle sur Microsoft Azure**. Quand vous avez terminé, appuyez sur **OK**. Cette action crée une machine virtuelle Linux avec une extension Docker configurée.

    ![][4]

    Notez que vous avez maintenant également la possibilité de créer un hôte conteneur Windows en utilisant Windows Server 2016 Technical Preview 3 (TP3).

    ![][8]

	|Nom de la propriété|Paramètre|
	|---|---|
	|Emplacement|Modifiez ce paramètre pour indiquer la région la plus proche de votre emplacement.|
	|Nom DNS|Entrez un nom unique pour la machine virtuelle. Si le nom est accepté par Azure, un cercle vert avec une coche blanche apparaît à droite. Sinon, un cercle rouge avec un x blanc s’affiche. Dans ce cas, entrez un nouveau nom unique.|
	|Image|Choisissez une image du système d’exploitation à utiliser dans l’hôte Docker, le cas échéant. Pour cet exemple, choisissez une image Ubuntu Server. (Notez qu’une image Windows Server est désormais proposée dans la liste des images disponibles).|
	|Nom d’utilisateur|Entrez un nom d’utilisateur unique pour la machine virtuelle.|
	|Mot de passe|Entrez un mot de passe pour l’utilisateur, puis confirmez-le.|
	|Répertoire des certificats |Spécifie le répertoire où sont stockés vos certificats Docker. Vous pouvez créer un répertoire ou pointer vers un répertoire existant, mais il est recommandé d’utiliser le répertoire de certificats par défaut (C:\\Users\\[*nom\_utilisateur*]\\.docker). Sinon, les options d’authentification ne peuvent pas être automatiquement récupérées quand vous utilisez le même hôte sur un autre projet ou système.|

1. Appuyez sur les points de suspension (...) situés en regard de l’entrée **Répertoire des certificats**, puis créez un répertoire pour les certificats Docker ou accédez à un répertoire de certificats Docker existant.

    Si les certificats Docker nécessaires pour la machine virtuelle sont introuvables, un point d’exclamation s’affiche à côté de l’entrée pour vous en informer. Si vous continuez, les certificats existants seront supprimés, puis recréés.

1. Appuyez sur **OK** pour commencer à créer la machine virtuelle. Un message s’affiche pour indiquer que la machine virtuelle est créée dans Azure.

    Visual Studio crée un fichier de modèle Azure Resource Manager (ARM), un fichier de paramètres et un script PowerShell pour pouvoir exécuter les mêmes commandes plus tard.

    ![][7]

    Vous pouvez suivre la progression de cette opération dans la fenêtre **Sortie**. Visual Studio appelle un script PowerShell pour déployer la machine virtuelle. Le script utilise des applets de commande Azure PowerShell pour déployer un groupe de ressources Azure. Ensuite, un autre script PowerShell utilise les commandes Docker émises pour la publication, comme vous le feriez si vous deviez créer manuellement l’hôte Docker.

    L’approvisionnement de l’hôte Docker peut prendre du temps. Vérifiez l’état dans la fenêtre Sortie de Visual Studio pour savoir quand la tâche est terminée.

1. Quand vous avez fini l’approvisionnement de l’hôte Docker dans Azure, vous pouvez vérifier votre compte sur le portail Azure. Vous devez normalement voir la nouvelle machine virtuelle sous la catégorie **Machine virtuelle** sur le portail Azure.

1. Quand l’hôte Docker est prêt, revenez en arrière et publiez le projet d’application web. Dans le menu contextuel associé au nœud du projet d’application web dans l’**Explorateur de solutions**, appuyez sur **Publier**. Visual Studio crée un fichier de publication approprié pour la machine virtuelle que vous avez créée.

1. Sous l’onglet **Connexion** de la boîte de dialogue **Publier le site Web**, appuyez sur **Valider la connexion** pour vous assurer que l’hôte Docker est prêt. Si la connexion est bonne, cliquez sur **Publier** pour publier l’application web.

    La première fois que vous publiez une application sur un hôte Docker, le téléchargement des images de base qui sont référencées dans votre fichier Docker (par exemple, **FROM** *imagename*) peut prendre du temps.

    Notez que le fichier Docker est propre au système d’exploitation. Si vous republiez l’application sur un autre système d’exploitation, vous devez renommer le fichier Docker pour que Visual Studio crée une nouvelle valeur par défaut en fonction du système d’exploitation cible. Par exemple, si vous publiez d’abord l’application dans un conteneur Linux et que vous la publiez ensuite sur Windows, renommez le fichier Docker avec un nom significatif, mais unique, tel que DockerLinux. Quand vous republiez l’application sur Windows, Visual Studio recrée le fichier Docker par défaut pour Windows. La prochaine fois que vous republierez l’application, vous devrez simplement sélectionner le fichier Docker approprié pour le système d’exploitation cible.

## Spécifier un hôte Docker personnalisé

Dans la section précédente, vous avez vu comment créer une machine virtuelle Docker hébergée sur Azure. Toutefois, si vous avez déjà un hôte Docker existant ailleurs, vous pouvez choisir de publier le projet sur cet hôte au lieu de le publier sur Azure.

### Pour spécifier un hôte Docker personnalisé

1. Dans la boîte de dialogue **Sélectionner une machine virtuelle Docker**, cochez la case **Hôte Docker personnalisé**.

    ![][5]

1. Appuyez sur **OK**.

1. Dans la boîte de dialogue **Publier le site Web**, ajoutez des valeurs aux paramètres dans la section **CustomDockerHost** (par exemple, l’URL du serveur, le nom de l’image, l’emplacement du fichier Docker ou les numéros de port du conteneur et de l’hôte).

    Dans la section **Options Docker avancées**, vous pouvez afficher ou modifier les options d’authentification et d’exécution ainsi que la ligne de commande Docker.

    ![][6]

1. Après avoir entré toutes les valeurs exigées, appuyez sur **Valider la connexion** pour vérifier que la connexion à l’hôte Docker fonctionne correctement.

1. Si c’est le cas, vous pouvez appuyer sur **Suivant** pour afficher la liste des composants à publier, ou sur **Publier** pour publier immédiatement le projet.

## Tester l’hôte Docker

Une fois que le projet a été publié sur un hôte Docker sur Azure, vous pouvez le tester en vérifiant ses paramètres. Étant donné que les outils en ligne de commande Docker s’installent avec l’extension Visual Studio, vous pouvez envoyer des commandes à Docker directement à partir d’une invite de commandes Windows.

La procédure ci-dessous permet de communiquer avec un hôte Docker déployé sur Azure.

### Pour tester l’hôte Docker

1. Ouvrez une invite de commandes Windows.

1. Affectez l’hôte Docker et vérifiez les variables d’environnement. Pour ce faire, entrez les commandes suivantes à l’invite de commandes. (Indiquez le nom de votre hôte Docker à la place de *NameofAzureVM* et la région dans laquelle il a été créé à la place de *region*.)

    ```
    Set DOCKER_HOST=tcp://[NameOfAzureVM].[Region].cloudapp.azure.com:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    La définition de ces variables d’environnement vous évite d’avoir à les spécifier manuellement pour chaque commande Docker que vous émettez.

1. Maintenant, vous pouvez émettre des commandes comme les suivantes pour tester la présence et le bon fonctionnement de l’hôte Docker.

	|Ligne de commande|Description|
	|---|---|
	|`docker info`|Affiche des informations sur la version de Docker.|
	|`docker ps`|Affiche la liste des conteneurs actuellement exécutés.|
	|`docker ps –a`|Affiche la liste de tous les conteneurs, y compris ceux qui sont arrêtés.|
	|`docker logs <Docker container name>`|Crée un journal pour le conteneur spécifié.|
	|`docker images`|Affiche une liste des images.|

    Pour obtenir la liste complète des commandes Docker, entrez la commande `docker` dans l’invite de commandes et appuyez sur la touche **&lt;Entrée>**. Pour plus d’informations, consultez la documentation sur la [ligne de commande Docker](https://docs.docker.com/reference/commandline/cli/).

## Étapes suivantes

Maintenant que vous disposez d’un hôte Docker, vous pouvez lui envoyer des commandes Docker. Pour plus d’informations sur Docker, consultez la [documentation Docker](https://docs.docker.com/) et le [didacticiel en ligne Docker](https://www.docker.com/tryit/).

Pour en savoir plus sur l'utilisation de l'extension Docker VM pour Linux dans Azure, consultez [Extension Docker VM pour Linux dans Azure](virtual-machines/virtual-machines-linux-dockerextension.md).

Pour résoudre les problèmes d’utilisation de Docker dans Visual Studio, consultez [Dépannage d’erreurs client sur Windows avec Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md).

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=AcomDC_0323_2016-->