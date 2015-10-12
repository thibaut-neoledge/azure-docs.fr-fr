<properties
	pageTitle="Tomcat sur une machine virtuelle | Microsoft Azure"
	description="Ce didacticiel utilise des ressources créées avec le modèle de déploiement classique, et montre comment créer une machine virtuelle Windows et la configurez pour exécuter un serveur d’applications Apache Tomcat."
	services="virtual-machines"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"
    tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="robmcm"/>

# Exécution d’un serveur d’applications Java sur une machine virtuelle créée avec le modèle de déploiement classique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création d’une ressource avec le modèle de déploiement classique.

Azure permet d'utiliser une machine virtuelle pour obtenir des fonctionnalités de serveur. Par exemple, vous pouvez configurer une machine virtuelle exécutée sur Azure pour qu'elle héberge un serveur d'applications Java, comme Apache Tomcat. Ce guide permet de comprendre comment créer une machine virtuelle exécutée sur Azure et comment la configurer pour exécuter un serveur d’applications Java.

Vous apprendrez à effectuer les opérations suivantes :

* créer une machine virtuelle déjà dotée d’un kit de développement Java (JDK) ;
* se connecter à distance à votre machine virtuelle ;
* installer un serveur d'applications Java sur votre machine virtuelle ;
* créer un point de terminaison pour votre machine virtuelle ;
* ouvrir un port dans le pare-feu pour votre serveur d'applications.

Ce didacticiel nécessite l’installation d’un serveur d’applications Apache Tomcat sur une machine virtuelle. Une fois terminée, l'installation Tomcat donne le résultat suivant :

![Machine virtuelle exécutant Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Création d’une machine virtuelle

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com).
2. Cliquez sur **New**, sur **Compute**, sur **Virtual machine**, puis sur **From Gallery**.
3. Dans la boîte de dialogue **Sélectionner une image de machine virtuelle**, sélectionnez **Windows Server 2012 JDK 7**. Notez que **Windows Server 2012 JDK 6** est disponible si vous ne pouvez pas exécuter certaines de vos applications héritées dans la version JDK 7.
4. Cliquez sur **Next**.
5. Dans la boîte de dialogue **Configuration de la machine virtuelle** :
    1. Entrez un nom pour la machine virtuelle.
    2. Entrez la taille de la machine virtuelle.
    3. Entrez un nom pour l'administrateur dans le champ **Nom d'utilisateur**. Notez le nom et le mot de passe que vous allez saisir, car vous devrez les réutiliser pour vous connecter à distance à votre machine virtuelle.
    4. Entrez un mot de passe dans le champ **Nouveau mot de passe**, puis entrez-le de nouveau dans le champ **Confirmer**. Il s'agit du mot de passe du compte Administrateur.
    5. Cliquez sur **Next**.
6. Dans la boîte de dialogue **Configuration de la machine virtuelle** suivante :
    1. Pour **Cloud service**, utilisez la valeur par défaut **Create a new cloud service**.
    2. La valeur du **Nom du cloud Service DNS** doit être unique sur cloudapp.net. Si nécessaire, modifiez cette valeur afin qu'Azure indique qu'elle est unique.
    2. Indiquez une région, un groupe d'affinités ou un réseau virtuel. Dans le cadre de ce didacticiel, indiquez une région, par exemple, **Bretagne**.
    2. Pour **Storage Account**, sélectionnez **Use an automatically generated storage account**.
    3. Pour **Availability Set**, sélectionnez **(None)**.
    4. Cliquez sur **Next**.
7. Dans la dernière boîte de dialogue **Configuration de la machine virtuelle** :
    1. Validez les entrées de points de terminaison par défaut.
    2. Cliquez sur **Terminé**.

## Connexion distante à votre machine virtuelle

1. Ouvrez une session sur le [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Machines virtuelles**.
3. Cliquez sur le nom de la machine virtuelle à laquelle vous souhaitez vous connecter.
4. Une fois que la machine virtuelle a démarré, un menu contextuel au bas de la page autorise les connexions.
5. Cliquez sur **Connecter**.
6. Répondez aux invites pour vous connecter à la machine virtuelle. Cela implique d'enregistrer ou d'ouvrir le fichier .rdp qui contient les informations de connexion. Vous devrez peut-être copier le port url:port en tant que dernière partie de la première ligne du fichier .rdp et le coller dans une application de connexion distante.

## Installation d'un serveur d'applications Java sur votre machine virtuelle

Vous pouvez copier un serveur d’applications Java sur votre machine virtuelle ou l’installer via un programme d’installation.

Pour les besoins de ce didacticiel, nous allons installer Tomcat.

1. Une fois connecté à votre machine virtuelle, ouvrez une session de navigateur sur [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Double-cliquez sur le lien **32-bit/64-bit Windows Service Installer**. Cette procédure installe Tomcat en tant que service Windows.
3. Lorsque vous y êtes invité, exécutez le programme d'installation.
4. Dans l'Assistant **Apache Tomcat Setup**, suivez les consignes pour installer Tomcat. Dans le cadre de ce didacticiel, vous pouvez valider les valeurs par défaut. Quand vous accédez à la boîte de dialogue **Completing the Apache Tomcat Setup Wizard**, vous pouvez éventuellement activer la case à cocher **Run Apache Tomcat** pour démarrer Tomcat immédiatement. Cliquez sur **Terminer** pour terminer le processus d'installation de Tomcat.

## Démarrage de Tomcat
Dans la boîte de dialogue **Completing the Apache Tomcat Setup Wizard**, si vous n’avez pas activé la case à cocher pour démarrer Tomcat immédiatement, démarrez-le en ouvrant une invite de commandes sur votre machine virtuelle, puis en exécutant **net start Tomcat7**.

Tomcat doit maintenant démarrer lorsque vous lancez le navigateur de la machine virtuelle et ouvrez <http://localhost:8080>.

Pour démarrer Tomcat depuis des machines externes, vous devez créer un point de terminaison et ouvrir un port.

## Création d'un point de terminaison pour votre machine virtuelle
1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Machines virtuelles**.
3. Cliquez sur le nom de la machine virtuelle exécutant votre serveur d'applications Java.
4. Cliquez sur **Endpoints**.
5. Cliquez sur **Add**.
6. Dans la boîte de dialogue **Ajouter un point de terminaison**, vérifiez que la case **Ajouter un point de terminaison autonome ** est cochée, puis cliquez sur **Suivant**.
7. Dans la boîte de dialogue **Détails du nouveau point de terminaison** :
    1. Entrez un nom pour le point de terminaison, par exemple **HttpIn**.
    2. Indiquez **TCP** pour le protocole.
    3. Indiquez **80** pour le port public.
    4. Indiquez **8080** pour le port privé.
    5. Cliquez sur le bouton **Terminé** pour fermer la boîte de dialogue. Votre point de terminaison est maintenant créé.

## Ouverture d'un port dans le pare-feu pour votre machine virtuelle
1. Connectez-vous à votre machine virtuelle.
2. Cliquez sur le menu **Démarrage de Windows**.
3. Cliquez sur **Panneau de configuration**.
4. Cliquez sur **Système et sécurité**, sur **Pare-feu Windows**, puis sur **Paramètres avancés**.
5. Cliquez sur **Règles de trafic entrant**, puis sur **Nouvelle règle**. ![Nouvelle règle de trafic entrant][NewIBRule]
6. Pour **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**. ![Porte de nouvelle règle de trafic entrant][NewRulePort]
7. Dans l’écran **Protocole et ports**, sélectionnez **TCP**, spécifiez **8080** comme **Port local spécifique**, puis cliquez sur **Suivant**. ![Nouvelle règle de trafic entrant][NewRuleProtocol]
8. Dans la boîte de dialogue **Action**, sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**. ![Action de nouvelle règle de trafic entrant][NewRuleAction]
9. Dans l’écran **Profil**, vérifiez que les options **Domaine**, **Privé** et **Public** sont sélectionnées, puis cliquez sur **Suivant**. ![Profil de nouvelle règle de trafic entrant][NewRuleProfile]
10. Dans l’écran **Name**, indiquez un nom pour la règle, par exemple **HttpIn** (il n’est pas nécessaire que ce nom corresponde à celui du point de terminaison), puis cliquez sur **Terminer**. ![Nom de nouvelle règle de trafic entrant][NewRuleName]

À ce stade, vous pouvez afficher votre site web Tomcat dans un navigateur externe, en utilisant une URL au format ****http://*your\_DNS\_name*.cloudapp.net**, où ***votre\_nom\_DNS*** correspond au nom DNS que vous avez indiqué lors de la création de la machine virtuelle.

## Considérations relatives au cycle de vie de l'application
* Vous pouvez créer votre propre archive web d’application (WAR) et l’ajouter au dossier **webapps**. Par exemple, créez un projet Web dynamique JSP (Java Service Page) de base et exportez-le en tant que fichier WAR, copiez celui-ci dans le dossier **webapps** d'Apache Tomcat sur la machine virtuelle, puis exécutez-le dans un navigateur.
* Par défaut, lorsque le service Tomcat est installé, il est configuré pour être démarré manuellement. Vous pouvez le configurer pour démarrer automatiquement en utilisant le composant logiciel enfichable Services. Démarrez-le en cliquant sur le menu **Démarrer de Windows**, **Outils d’administration**, puis **Services**. Double-cliquez sur le service **Apache Tomcat** et affectez au **Type de démarrage** la valeur **Automatique**.

    ![Configurer un service pour qu'il démarre automatiquement][service_automatic_startup]

    L’activation du démarrage automatique de Tomcat lui permet de redémarrer en même temps que la machine virtuelle (par exemple, après des mises à jour logicielles nécessitant un redémarrage).

## Étapes suivantes
Pour en savoir plus sur les autres services (Azure Storage, bus des services, base de données SQL) à inclure dans vos applications Java, voir les informations disponibles de [Java Developer Center](http://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png

<!---HONumber=Oct15_HO1-->