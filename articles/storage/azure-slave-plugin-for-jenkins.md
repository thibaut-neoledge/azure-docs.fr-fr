<properties
    pageTitle="Utilisation du plug-in esclave Azure avec la solution d'intégration continue Jenkins"
    description="Décrit comment utiliser le plug-in esclave Azure avec la solution d'intégration continue Jenkins."
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="v-dedomi"/>

#Utilisation du plug-in esclave Azure avec la solution d'intégration continue Jenkins

Le plug-in esclave Azure pour Jenkins vous permet de mettre en service des nœuds subordonnés sur Azure lors de l'exécution de builds distribués.

## Installer le plug-in esclave Azure
1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins** (Gérer Jenkins).
2. Sur la page **Manage Jenkins** (Gérer Jenkins), cliquez sur **Manage Plugins** (Gérer les plug-ins).
3. Cliquez sur l'onglet **Available**.
4. Dans le champ de filtre au-dessus de la liste des plug-ins disponibles, tapez **Azure** pour limiter la liste des plug-ins appropriés.

	Si vous choisissez de faire défiler la liste des plug-ins disponibles, vous trouverez le plug-in esclave Azure sous la section **Cluster Management and Distributed Build** (Gestion du cluster et build distribué).
	 
5. Cochez la case **Azure Slave Plugin** (Plug-in esclave Azure).
6. Cliquez sur **Install without restart** (Installer sans redémarrer) ou sur **Download now and install after restart** (Télécharger maintenant et installer après redémarrage).

Maintenant que le plug-in est installé, les étapes suivantes consistent à configurer le plug-in avec votre profil d'abonnement Azure et à créer un modèle qui sera utilisé lors de la création de l'ordinateur virtuel pour le nœud subordonné.


## Configurer le plug-in esclave Azure avec votre profil d'abonnement

Un profil d'abonnement, également appelé paramètres de publication, est un fichier XML qui contient des informations d'identification sécurisées, ainsi que des informations supplémentaires, dont vous aurez besoin pour utiliser Azure dans votre environnement de développement. Pour configurer le plug-in esclave Azure, vous avez besoin des éléments suivants :

* votre ID d'abonnement
* un certificat de gestion pour votre abonnement

Vous les trouverez dans votre profil d'abonnement. Si vous n'avez pas de copie de votre profil d'abonnement, vous pouvez le télécharger à partir d'[ici](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0). Vous trouverez ci-dessous un exemple de profil d'abonnement.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID value>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Une fois que vous avez votre profil d'abonnement, procédez comme suit pour configurer le plug-in esclave Azure.

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins** (Gérer Jenkins).
2. Cliquez sur **Configure System** (Configurer le système).
3. Faites défiler la page vers le bas pour trouver la section **Cloud**.
4. Cliquez sur **Add new cloud > Microsoft Azure** (Ajouter nouveau cloud > Microsoft Azure).

	![section cloud](./media/azure-slave-plugin-for-jenkins/jenkins-cloud-section.png)

	Cette commande affiche les champs où vous devez entrer les détails de votre abonnement.

	![configuration de l'abonnement](./media/azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png)

5. Copiez les valeurs de l'ID d'abonnement et du certificat de gestion de votre profil d'abonnement et collez-les dans les champs appropriés.

	Lors de la copie de l'ID d'abonnement et du certificat de gestion, n'incluez **pas** les guillemets qui entourent les valeurs.
	
6. Cliquez sur **Verify configuration** (Vérifier la configuration).
7. Lorsque la configuration est vérifiée comme étant correcte, cliquez sur **Save** (Enregistrer).

## Configurer un modèle d'ordinateur virtuel pour le plug-in esclave Azure

Un modèle d'ordinateur virtuel définit les paramètres qui seront utilisés par le plug-in pour créer un nœud subordonné sur Azure. Dans les étapes suivantes, nous allons créer un modèle pour un ordinateur virtuel Ubuntu.

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins** (Gérer Jenkins).
2. Cliquez sur **Configure System** (Configurer le système).
3. Faites défiler la page vers le bas pour trouver la section **Cloud**.

4. Dans la section Cloud, recherchez « Add Azure Virtual Machine Template » (Ajouter modèle d'ordinateur virtuel Azure) et cliquez sur le bouton « Add » (Ajouter).

	![ajouter un modèle d'ordinateur virtuel](./media/azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png)

	Cette commande affiche les champs où vous entrez des informations sur le modèle que vous créez.

	![configuration générale vide](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png)

5. Entrez un nom de service cloud Azure dans le champ **Name** (Nom). Si le nom entré fait référence à un service cloud existant, l'ordinateur virtuel sera déployé dans ce service. Dans le cas contraire, Azure en crée un nouveau.

6. Dans le champ **Description**, entrez du texte qui décrit le modèle que vous créez. Ces informations sont uniquement à des fins documentaires et ne sont pas utilisées dans la mise en service d'un ordinateur virtuel.
7. Le champ **Labels** (Étiquettes) est utilisé pour identifier le modèle que vous créez. Il est ensuite utilisé pour référencer le modèle lors de la création d'un travail Jenkins. Dans notre cas, entrez **linux** dans ce champ. 
8. Sélectionnez une région où l'ordinateur virtuel sera créé.
9. Sélectionnez la taille appropriée pour l'ordinateur virtuel.
10. Spécifiez un compte de stockage dans lequel l'ordinateur virtuel sera créé. Assurez-vous qu'il se trouve dans la même région que le service Cloud que vous allez utiliser. Si vous souhaitez créer un nouveau stockage, vous pouvez laisser ce champ vide.
11. La durée de conservation spécifie le nombre de minutes avant la suppression d'un esclave inactif par Jenkins. Conservez la valeur par défaut de 60. Vous pouvez également choisir d'arrêter l'esclave plutôt que de le supprimer lorsqu'il est inactif. Pour ce faire, cochez la case **Shutdown Only (Do Not Delete) After Retention Time** (Arrêt uniquement (ne pas supprimer) après le délai de conservation).
12. Dans **Usage** (Utilisation), sélectionnez la condition appropriée lorsque ce nœud subordonné sera utilisé. Pour l'instant, sélectionnez **Utilize this node as much as possible** (Utiliser ce nœud autant que possible).

	À ce stade, votre formulaire doit ressembler à ce qui suit :

	![configuration de modèle général de point de contrôle](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png)

	L'étape suivante consiste à fournir des détails sur l'image du système d'exploitation dans laquelle vous voulez créer l'esclave.
 
13. Dans **Image Family or Id** (Famille d'images ou ID), vous devez spécifier l'image système qui sera installée sur votre ordinateur virtuel. Vous pouvez faire votre choix dans une liste de familles d'images ou spécifier une image personnalisée.

	Si vous souhaitez faire votre choix dans une liste de familles d'images, entrez le premier caractère (respectez la casse) du nom de famille de l'image. Par exemple, le fait de taper **U** affichera une liste des familles de serveurs Ubuntu. Une fois que vous avez fait votre choix dans la liste, Hudson utilisera la version la plus récente de cette image système de cette famille lors de la mise en service de l'ordinateur virtuel.

	![exemple de liste d'images de système d'exploitation](./media/azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png)
	
	Si vous avez une image personnalisée que vous souhaitez utiliser à la place, entrez son nom. Les noms des images personnalisées ne figurent pas dans une liste. Vous devez donc vous assurer que le nom est entré correctement.

	Pour ce didacticiel, tapez **U** pour afficher une liste d'images Ubuntu et sélectionnez **Ubuntu Server 14.04 LTS**.
 
14. Pour la **méthode de lancement**, sélectionnez **SSH**.
15. Copiez le script ci-dessous et collez-le dans le champ du **script init**.

		# Install Java
		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk



		# Install git

		sudo apt-get install -y git



		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

	Le **script init** sera exécuté une fois l'ordinateur virtuel créé. Dans cet exemple, le script installe Java, git et ant.
	
16. Dans les champs **Username** (Nom d'utilisateur) et **Password** (Mot de passe), saisissez les valeurs de votre choix pour le compte administrateur qui sera créé sur l'ordinateur virtuel.
17. Cliquez sur **Verify Template** (Vérifier le modèle) pour vérifier si les paramètres spécifiés sont valides.
18. Cliquez sur **Save** (Enregistrer).


## Créer un travail Jenkins qui s'exécute sur un nœud subordonné sur Azure

Dans cette section, vous allez créer un travail Jenkins qui s'exécutera sur un nœud subordonné sur Azure. Vous devrez disposer de votre propre projet sur github pour suivre la procédure.

1. Dans le tableau de bord Jenkins, cliquez sur **New Item** (Nouvel élément). 
2. Entrez un nom pour le travail que vous créez.
3. Pour le type de projet, sélectionnez **Freestyle project** (Projet libre).
4. Cliquez sur **OK**.
5. Dans la page de configuration du travail, sélectionnez **Restrict where this project can be run** (Limiter où ce projet peut être exécuté).
6. Dans le champ **Label Expression** (Expression d'étiquette), entrez **linux**. Dans la section précédente, nous avons créé un modèle esclave que nous avons nommé **linux** qui correspond à ce que nous définissons ici.
7. Dans la section **Build**, cliquez sur **Add build step** (Ajouter une étape de build) et sélectionnez **Execute shell** (Exécuter shell).
8. Modifiez le script suivant, en remplaçant **(nom de votre compte github)**, **(nom de votre projet)** et **(répertoire de votre projet)** par les valeurs appropriées et collez le script modifié dans la zone de texte qui s'affiche.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your github account name)/(your project name).git

		fi
		
		# change directory to project

		cd $currentDir/(your project directory)



		#Execute build task

		ant
		
9. Cliquez sur **Save** (Enregistrer).
10. Dans le tableau de bord Jenkins, survolez sur le travail que vous venez de créer, puis cliquez sur la flèche vers le bas pour afficher les options du travail.
11. Cliquez sur **Build now** (Générer maintenant).

Jenkins crée ensuite un nœud subordonné à l'aide du modèle créé dans la section précédente, puis il exécute le script que vous avez spécifié dans l'étape de build pour ce travail.






  

  

<!-----HONumber=July15_HO4-->