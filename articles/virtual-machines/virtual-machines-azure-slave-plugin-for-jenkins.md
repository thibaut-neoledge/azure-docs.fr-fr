---
title: "Utilisation du plug-in subordonné Azure avec la solution d’intégration continue Jenkins | Microsoft Docs"
description: "Décrit comment utiliser le plug-in subordonné Azure avec la solution d’intégration continue Jenkins."
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 09233bd4-957d-41bf-bccc-9dd2355ed1bf
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a40d829c65067dba90a212390e858b7995452b6


---
# <a name="how-to-use-the-azure-slave-plugin-with-jenkins-continuous-integration"></a>Utilisation du plug-in subordonné Azure avec la solution d’intégration continue Jenkins
Le plug-in subordonné Azure pour Jenkins facilite l’approvisionnement de nœuds subordonnés sur Azure lors de l’exécution de builds distribués, et prend en charge la création de :

* subordonnés Windows sur le cloud Azure avec SSH et JNLP (Java Network Launch Protocol)
  
  * Pour lancer une image Windows via SSH, l’image doit être préconfigurée avec SSH.
  * Pour plus d’informations sur la préparation d’une image Windows personnalisée, consultez [Capturer une machine virtuelle Windows selon le modèle de déploiement Resource Manager][windows-image-capture].
* subordonnés Linux sur le cloud Azure avec SSH
  
  * Pour plus d’informations sur la préparation d’une image Linux personnalisée, consultez [Capturer une machine virtuelle Linux pour l’utiliser comme modèle Resource Manager][linux-image-capture].

### <a name="prerequisites"></a>Composants requis
Avant de suivre les étapes décrites dans cet article, vous devez enregistrer et autoriser votre application cliente, puis récupérer votre ID client et votre clé secrète client qui seront envoyés à Azure Active Directory lors de l’authentification. Pour plus d’informations sur la configuration requise, consultez les articles suivants :

* [Intégration d’applications dans Azure Active Directory][integrate-apps-with-AAD]
* [Inscrire une application cliente][register-client-app]

En outre, pour pouvoir suivre les étapes de la section [Créer un travail Jenkins qui s’exécute sur un nœud subordonné sur Azure](#create-jenkins-project) de cet article, vous devez disposer d’une configuration de projet sur GitHub.

<a name="install-azure-slave-plugin"></a>

## <a name="install-the-azure-slave-plugin"></a>Installer le plug-in subordonné Azure
1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins**(Gérer Jenkins).
   
    ![Gérer Jenkins][jenkins-dashboard-manage]
2. Sur la page **Manage Jenkins** (Gérer Jenkins), cliquez sur **Manage Plugins** (Gérer les plug-ins).
   
    ![Gérer les plug-ins][jenkins-manage-plugins]
3. Cliquez sur l’onglet **Available** (Disponible) et entrez « Azure » dans le filtre, puis sélectionnez le **Plug-in subordonné Azure**. 
   
    ![Plug-in subordonné Azure][search-plugins]
   
    Si vous choisissez de faire défiler la liste des plug-ins disponibles, vous trouverez le plug-in subordonné Azure listé sous la section **Cluster Management and Distributed Build** (Gestion du cluster et build distribué).
4. Cliquez sur **Install without restart** (Installer sans redémarrer) ou sur **Download now and install after restart** (Télécharger maintenant et installer après redémarrage).
   
    ![Installer le plug-in][install-plugin]

Maintenant que le plug-in est installé, les étapes suivantes consistent à le configurer avec votre profil d’abonnement Azure et à créer un modèle qui sera utilisé lors de la création de la machine virtuelle pour le nœud subordonné.

## <a name="configure-the-azure-slave-plugin-to-use-your-subscription-profile"></a>Configurer le plug-in subordonné Azure pour utiliser votre profil d’abonnement
Un profil d'abonnement, également appelé paramètres de publication, est un fichier XML qui contient des informations d'identification sécurisées, ainsi que des informations supplémentaires, dont vous aurez besoin pour utiliser Azure dans votre environnement de développement. Pour configurer le plug-in subordonné Azure, vous aurez besoin des éléments suivants :

* Votre ID d’abonnement
* Un certificat de gestion pour votre abonnement

Ils se trouvent dans votre [profil d’abonnement], qui peut ressembler à l’exemple suivant :

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

Une fois que vous avez votre profil d’abonnement, procédez comme suit pour configurer le plug-in subordonné Azure :

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins**(Gérer Jenkins).
   
    ![Gérer Jenkins][jenkins-dashboard-manage]
2. Cliquez sur **Configure System**(Configurer le système).
   
    ![Configurer le système][jenkins-configure-system]
3. Faites défiler la page vers le bas pour trouver la section **Cloud** .
4. Cliquez sur **Add a new cloud** (Ajouter un nouveau cloud) puis sélectionnez **Microsoft Azure**.
   
    ![Ajouter un nouveau cloud][cloud-section]
   
    Cette commande affiche les champs où vous devez entrer les détails de votre abonnement.
   
    ![Configuration de l’abonnement][subscription-configuration]
5. Copiez les valeurs de l'ID d'abonnement et du certificat de gestion de votre profil d'abonnement et collez-les dans les champs appropriés.
   
    Lors de la copie de l’ID d’abonnement et du certificat de gestion, n’incluez pas les guillemets qui entourent les valeurs.
6. Cliquez sur **Verify Configuration** (Vérifier la configuration) pour vérifier si les paramètres spécifiés sont valides, puis cliquez sur **Save** (Enregistrer).

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plugin"></a>Configurer un modèle de machine virtuelle pour le plug-in subordonné Azure
Dans cette section, vous ajouterez un modèle de machine virtuelle définissant les paramètres qui seront utilisés par le plug-in subordonné Azure pour créer un nœud subordonné sur Azure. Dans les étapes suivantes, vous allez créer un modèle pour une machine virtuelle Ubuntu.

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins**(Gérer Jenkins).
   
    ![Gérer Jenkins][jenkins-dashboard-manage]
2. Cliquez sur **Configure System**(Configurer le système).
   
    ![Configurer le système][jenkins-configure-system]
3. Faites défiler la page vers le bas pour trouver la section **Cloud** .
4. Dans la section **Cloud**, recherchez **Add Azure Virtual Machine Template** (Ajouter un modèle de machine virtuelle Azure) pour le cloud que vous avez ajouté dans la section précédente, puis cliquez sur **Add** (Ajouter).
   
    ![Ajouter un modèle de machine virtuelle Azure][add-vm-template]
   
    Jenkins affiche un formulaire qui contient les champs où vous entrez des informations sur le modèle que vous créez.
   
    ![Configuration générale vide][blank-general-configuration]
5. Entrez les informations de **Configuration générale** de votre modèle :
   
   1. Dans la zone **Name** (Nom), entrez le nom de votre nouveau modèle ; il n’est destiné qu’à vous et ne sera pas utilisé dans l’approvisionnement d’une machine virtuelle.
   2. Dans la zone **Description** , entrez du texte décrivant le modèle que vous créez. Ces informations sont uniquement destinées à vos dossiers et ne sont pas utilisées dans la mise en service d’une machine virtuelle.
   3. La zone **Labels** (Étiquettes) est utilisée pour identifier le modèle que vous créez. Elle est ensuite utilisée pour référencer le modèle lors de la création d’un travail Jenkins. Dans notre cas, entrez « **linux** » dans cette zone.
   4. Dans la liste **Region** (Région), cliquez sur la région Azure où la machine virtuelle sera créée.
   5. Dans la liste **Virtual Machine Size** (Taille de la machine virtuelle), choisissez la taille appropriée dans le menu déroulant.
   6. Dans la zone **Storage Account Name** , spécifiez un compte de stockage où la machine virtuelle sera créée. Si vous souhaitez que Jenkins crée un compte de stockage avec la valeur par défaut « jenkinsarmst », vous pouvez laisser cette zone vide.
   7. **Retention Time** (durée de rétention) spécifie le nombre de minutes avant la suppression d’un subordonné inactif par Jenkins ; conservez la valeur par défaut de 60.
      
      * Vous pouvez également choisir d’arrêter le subordonné plutôt que de le supprimer lorsqu’il est inactif. Pour ce faire, cochez la case **Shutdown Only (Do Not Delete) After Retention Time** .
      * Vous pouvez également spécifier 0 si vous ne souhaitez pas que les subordonnés inactifs soient supprimés automatiquement.
   8. Dans la liste **Usage** (Utilisation), vous pouvez choisir parmi les options suivantes :
      
      * **Utilize this node as much as possible** (Utiliser ce nœud autant que possible) : Jenkins peut exécuter n’importe quel travail sur le subordonné tant qu’il est disponible.
      * **Leave this node for tied jobs only** (Réserver ce nœud aux travaux liés) : Jenkins ne génère un projet (ou travail) sur ce nœud que lorsque ce projet en particulier a été lié à ce nœud ; cela permet de réserver le subordonné à certains types de travaux.
      
      Pour ce didacticiel, cliquez sur **Utilize this node as much as possible**.
      
      À ce stade, votre formulaire doit ressembler à ce qui suit :
      
      ![Configuration générale du modèle][general-template-config]
6. Fournissez la **Configuration d’image** de votre modèle :
   
   1. Pour la famille de l’image, vous avez deux options :
      
      * **Custom User Image** (Image personnalisée par l’utilisateur) : avec cette option, vous devez fournir l’URL d’une image personnalisée qui se trouve dans le compte de stockage dans lequel vous allez créer des nœuds subordonnés.
      * **Image Reference** (Référence de l’image) : avec cette option, vous devez spécifier *l’Éditeur*, *l’Offre* et la *Référence SKU* d’une image, que vous pouvez trouver dans la [Place de marché des Machines Virtuelles Azure][azure-images].
        
        Pour ce didacticiel, choisissez **Image Reference** (Référence de l’image) et utilisez les valeurs suivantes :
      * **Image Publisher** (Éditeur d’images) : Canonical
      * **Image Offer** (Offre de l’image) : UbuntuServer
      * **Image SKU **(Référence SKU de l’image) : 14.04.4-LTS
   2. Pour la liste **Launch Method** (Méthode de lancement), vous avez deux options : **SSH** et **JNLP**. Pour les besoins de ce didacticiel, choisissez **SSH**. Toutefois, vous devez prendre en compte plusieurs points lorsque vous choisissez votre méthode de lancement :
      
      * Les subordonnés Linux ne peuvent être lancés qu’avec SSH.
      * Les subordonnés Windows peuvent utiliser SSH ou JNLP mais, lorsque SSH est utilisé pour les subordonnés Windows, l’image doit être préparée sur mesure avec un serveur SSH.
        
        Si vous utilisez JNLP comme méthode de lancement, vous devez vous assurer que les éléments suivants sont configurés :
      * Le subordonné Azure doit être en mesure d’atteindre l’URL Jenkins ; vous devez donc configurer les pare-feu en conséquence. Vous trouverez votre URL Jenkins en cliquant sur **Manage Jenkins** (Gérer Jenkins), puis sur **Configure System** (Configurer le système) et en recherchant la section **Jenkins Location** (Emplacement de Jenkins).
   3. Le subordonné Azure qui est lancé avec JNLP doit être en mesure d’atteindre le port TCP Jenkins ; pour cette raison, il est recommandé d’utiliser un port TCP fixe de façon à pouvoir configurer les pare-feu en conséquence. Vous pouvez spécifier le port TCP Jenkins en cliquant sur **Manage Jenkins** (Gérer Jenkins), puis sur **Configure Global Security** (Configurer la sécurité globale), en cochant l’option **Enable security** (Activer la sécurité) et en utilisant un port **Fixed** (fixe), puis en spécifiant le port à utiliser.
   4. Pour le **Init Script** (Script d’initialisation), vous devez fournir un script d’initialisation qui sera exécuté une fois la machine virtuelle créée, avec les considérations suivantes :
      
      * Au minimum, votre script doit installer Java.
      * Si vous utilisez JNLP, le script d’initialisation doit être écrit dans PowerShell.
      * Si votre script d’initialisation risque de prendre beaucoup de temps à l’exécution, par exemple pour installer une longue série de packages, il est recommandé de préparer une image personnalisée avec tous les logiciels nécessaires préinstallés plutôt que de les installer avec votre script d’initialisation.
        
        Pour ce didacticiel, copiez le script ci-dessous et collez-le dans la zone **Init Script** :
        
              # Install Java
              sudo apt-get -y update
              sudo apt-get install -y openjdk-7-jdk
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y openjdk-7-jdk
              # Install git
              sudo apt-get install -y git
              # Install ant
              sudo apt-get install -y ant
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y ant
        
        Dans l’exemple ci-dessus, le script d’initialisation installe *Java*, *Git* et *ant*.
   5. Dans les zones **Username** (Nom d’utilisateur) et **Password** (Mot de passe), saisissez les valeurs de votre choix pour le compte administrateur qui sera créé sur votre machine virtuelle.
      
      À ce stade, votre formulaire doit ressembler à ce qui suit :
      
      ![Configuration d’une Image Jenkins][jenkins-image-configuration]
7. Cliquez sur **Verify Template** (Vérifier le modèle) pour vérifier si les paramètres spécifiés sont valides, puis cliquez sur **Save** (Enregistrer).
   
    ![Enregistrer un modèle Jenkins][jenkins-save-template]

<a name="create-jenkins-project"></a>

## <a name="create-a-jenkins-job-which-runs-on-a-slave-node-on-azure"></a>Créer un travail Jenkins qui s’exécute sur un nœud subordonné sur Azure
Dans cette section, vous allez créer un travail Jenkins qui s'exécutera sur un nœud subordonné sur Azure. Pour effectuer ces étapes, vous devez disposer d’un projet sur GitHub.

1. Dans le tableau de bord Jenkins, cliquez sur **New Item**(Nouvel élément).
   
    ![Nouvel élément du tableau de bord Jenkins][jenkins-dashboard-new-item]
2. Entrez le nom de la tâche que vous créez, cliquez sur le type de projet **Freestyle project** (projet libre), puis sur **OK**.
   
    ![Créer un nouvel élément Jenkins][jenkins-create-new-item]
3. Sur la page configuration des tâches, sélectionnez **Restrict where this project can be run** (Restreindre les emplacements d’exécution de ce projet), et entrez «**linux**» dans la zone **Label Expression** (Expression de l’étiquette) ; cette valeur correspond à l’étiquette du modèle subordonné que vous avez créé dans la section précédente.
   
    ![Restreindre un nouvel élément Jenkins][jenkins-new-item-restrict]
4. Dans la section **Build**, cliquez sur **Add build step** (Ajouter une étape de build) et sélectionnez **Execute shell** (Exécuter shell).
   
    ![Générer un nouvel élément Jenkins][jenkins-new-item-build]
5. Modifiez le script suivant, en remplaçant **(your GitHub account name)**, **(your project name)** et **(your project directory)** par les valeurs appropriées et collez le script modifié dans la zone de texte qui s’affiche.
   
            # Clone from git repo
            currentDir="$PWD"
            if [ -e (your project directory) ]; then
                cd (your project directory)
                git pull origin master
            else
                git clone https://github.com/(your GitHub account name)/(your project name).git
            fi
            # change directory to project
            cd $currentDir/(your project directory)
            #Execute build task
            ant
   
    Votre formulaire doit ressembler à l’exemple suivant :
   
    ![Script de nouvel élément Jenkins][jenkins-new-item-script]
6. Lorsque vous avez terminé toutes les étapes de configuration, cliquez sur **Save** (Enregistrer).
   
    ![Enregistrer un nouvel élément Jenkins][jenkins-new-item-save]
7. Dans le tableau de bord Jenkins, cliquez sur la flèche déroulante en regard du projet que vous venez de créer, puis cliquez sur **Build Now** (Générer maintenant).
   
    ![Générer maintenant Jenkins][jenkins-build-now]

Jenkins crée un nœud subordonné à l’aide du modèle que vous avez créé dans la section précédente, puis il exécute le script que vous avez spécifié dans l’étape de build de cette tâche.

<a name="image-template-considerations"></a>

## <a name="considerations-when-working-with-image-templates"></a>Considérations relatives à l’utilisation des modèles d’images
Les sections suivantes contiennent des informations utiles pour la configuration des différents modèles.

### <a name="when-you-are-using-ubuntu-image-templates"></a>Si vous utilisez des modèles d’images Ubuntu
* Si votre script d’initialisation risque de prendre beaucoup de temps à l’exécution, par exemple pour installer une longue série de packages, il est recommandé de préparer une image Ubuntu personnalisée avec tous les logiciels nécessaires préinstallés plutôt que de les installer avec votre script d’initialisation.
* Au minimum, votre script doit installer Java. Comme le montrent les exemples précédents, vous pouvez utiliser le script suivant pour installer Java, Git et ant.
  
        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk
        # Install git
        sudo apt-get install -y git
        # Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y ant

### <a name="when-you-are-using-windows-image-templates-and-jnlp-launch-method"></a>Si vous utilisez des modèles d’images Windows et la méthode de lancement JNLP
* Si la sécurité de votre principal Jenkins n’est pas configurée :
  
  * Laissez le champ **Init Script** (Script d’initialisation) vide pour le script par défaut à exécuter sur le subordonné.
* Si la sécurité de votre principal Jenkins est configurée : 
  
  * Copiez le script à partir de [Windows Slaves Setup][windows-slaves-setup] (Configuration des subordonnés Windows) et modifiez-le avec vos informations d’identification Jenkins.
  * Au minimum, le script doit être modifié avec un jeton d’API et un ID d’utilisateur Jenkins. Pour récupérer le jeton d’API d’un utilisateur, procédez comme suit :
    
    1. Dans le tableau de bord Jenkins, cliquez sur **People**(Personnes).
    2. Cliquez sur le compte d’utilisateur correspondant.
    3. Cliquez sur **Configure** (Configurer) dans le menu de gauche.
    4. Cliquez sur **Show API Token...** (Afficher le jeton d’API...).

<a name="see-also"></a>

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

Pour plus d’informations sur le plug-in subordonné Azure pour Jenkins, consultez le projet [Plug-in subordonné Azure] sur GitHub.

<!-- URL List -->

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[Plug-in subordonné Azure]: https://github.com/jenkinsci/azure-slave-plugin/
[profil d’abonnement]: http://go.microsoft.com/fwlink/?LinkID=396395
[azure-images]: https://azure.microsoft.com/marketplace/virtual-machines/all/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[windows-slaves-setup]: https://gist.github.com/snallami/5aa9ea2c57836a3b3635

<!-- IMG List -->

[jenkins-dashboard-manage]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-manage.png
[jenkins-manage-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-manage-plugins.png
[jenkins-configure-system]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-configure-system.png
[jenkins-dashboard-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-new-item.png
[search-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/install-plugin.png
[jenkins-create-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-create-new-item.png
[cloud-section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add-vm-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank-general-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[general-template-config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[jenkins-new-item-restrict]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-restrict.png
[jenkins-new-item-build]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-build.png
[jenkins-new-item-script]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-script.png
[jenkins-new-item-save]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-save.png
[jenkins-build-now]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-build-now.png
[jenkins-image-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-image-configuration.png
[jenkins-save-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-save-template.png
[windows-image-capture]: ./virtual-machines-windows-classic-capture-image.md
[linux-image-capture]: ./virtual-machines-linux-capture-image.md



<!--HONumber=Nov16_HO3-->


