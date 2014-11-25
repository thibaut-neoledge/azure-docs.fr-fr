<properties linkid="dev-java-vm-application-server" urlDisplayName="Tomcat on Virtual Machine" pageTitle="Tomcat on a virtual machine - Azure tutorial" metaKeywords="Azure vm, creating vm Tomcat, configuring vm Tomcat" description="Learn how to create a Windows Virtual machine and configure the machine to run a Apache Tomcat application server." metaCanonical="" services="virtual-machines" documentationCenter="Java" title="How to run a Java application server on a virtual machine" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Exécution d'un serveur d'applications Java sur une machine virtuelle

Azure permet d'utiliser une machine virtuelle pour obtenir des fonctionnalités de serveur. Par exemple, vous pouvez configurer une machine virtuelle exécutée sur Azure pour qu'elle héberge un serveur d'applications Java, comme Apache Tomcat. Ce guide permet de comprendre comment créer une machine virtuelle exécutée sur Azure et comment la configurer pour exécuter un serveur d'applications Java.

Vous apprendrez à effectuer les opérations suivantes :

-   créer une machine virtuelle sur laquelle un JDK est déjà installé ;
-   se connecter à distance à votre machine virtuelle ;
-   installer un serveur d'applications Java sur votre machine virtuelle ;
-   créer un point de terminaison pour votre machine virtuelle ;
-   ouvrir un port dans le pare-feu pour votre serveur d'applications.

Ce didacticiel nécessite l'installation d'un serveur d'applications Apache Tomcat sur une machine virtuelle. Une fois terminée, l'installation Tomcat donne le résultat suivant :

![Machine virtuelle exécutant Apache Tomcat][Machine virtuelle exécutant Apache Tomcat]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Création d'une machine virtuelle

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **New**, sur **Compute**, sur **Virtual machine**, puis sur **From Gallery**.
3.  Dans la boîte de dialogue **Virtual machine image select** sélectionnez **JDK 7 Windows Server 2012**.
    Notez que **Windows Server 2012 JDK 6** est disponible si vous ne pouvez pas exécuter certaines de vos applications héritées dans JDK 7.
4.  Cliquez sur **Suivant**.
5.  Dans la boîte de dialogue **Virtual machine configuration** :
    1.  Entrez un nom pour la machine virtuelle.
    2.  Entrez la taille de la machine virtuelle.
    3.  Entrez un nom pour l'administrateur dans le champ **Nom d'utilisateur**. Notez le nom et le mot de passe que vous allez entrer, car vous les utiliserez pour vous connecter à distance à votre machine virtuelle.
    4.  Entrez un mot de passe dans le champ **Nouveau mot de passe**, puis entrez-le de nouveau dans le champ **Confirmer**. Il s'agit du mot de passe du compte Administrateur.
    5.  Cliquez sur **Suivant**.
6.  Dans la boîte de dialogue **Virtual machine configuration** suivante :
    1.  Pour **Cloud service**, utilisez la valeur par défaut **Create a new cloud service**.
    2.  La valeur pour **Cloud service DNS name** doit être unique sur cloudapp.net. Le cas échéant, modifiez cette valeur pour qu'Azure indique qu'elle est unique.
    3.  Indiquez une région, un groupe d'affinités ou un réseau virtuel. Dans le cadre de ce didacticiel, indiquez une région comme **Bretagne**.
    4.  Pour **Storage Account**, sélectionnez **Use an automatically generated storage account**.
    5.  Pour **Availability Set**, sélectionnez **(None)**.
    6.  Cliquez sur **Suivant**.
7.  Dans la dernière boîte de dialogue **Virtual machine configuration** :
    1.  Validez les entrées de points de terminaison par défaut.
    2.  Cliquez sur **Terminé**.

## Connexion distante à votre machine virtuelle

1.  Connectez-vous au [portail de gestion][portail de gestion Azure].
2.  Cliquez sur **Machines virtuelles**.
3.  Cliquez sur le nom de la machine virtuelle à laquelle vous voulez vous connecter.
4.  Cliquez sur **Connecter**.
5.  Répondez aux invites pour vous connecter à la machine virtuelle. Lorsque vous y êtes invité, entrez le nom d'administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

## Installation d'un serveur d'applications Java sur votre machine virtuelle

Vous pouvez copier un serveur d'applications Java sur votre machine virtuelle ou l'installer via un programme d'installation.

Dans le cadre de ce didacticiel, nous allons installer Tomcat.

1.  Lorsque vous êtes connecté à votre machine virtuelle, ouvrez une session de navigateur vers <http://tomcat.apache.org/download-70.cgi>.
2.  Double-cliquez sur le lien **32-bit/64-bit Windows Service Installer**. Cette procédure installe Tomcat en tant que service Windows.
3.  ﻿Lorsque vous y êtes invité, exécutez le programme d'installation.
4.  Dans l'Assistant **Apache Tomcat Setup**, suivez les consignes pour installer Tomcat. Dans le cadre de ce didacticiel, vous pouvez valider les valeurs par défaut. Lorsque vous accédez à la boîte de dialogue **Completing the Apache Tomcat Setup Wizard**, vous pouvez éventuellement activer la case à cocher **Run Apache Tomcat** pour démarrer Tomcat immédiatement. Cliquez sur **Terminer** pour terminer le processus d'installation de Tomcat.

## Démarrage de Tomcat

Dans la boîte de dialogue **Completing the Apache Tomcat Setup Wizard**, si vous n'avez pas activé la case à cocher pour démarrer Tomcat immédiatement, démarrez-le en ouvrant une invite de commandes sur votre machine virtuelle, puis en exécutant **net start Tomcat7**.

Tomcat doit maintenant démarrer lorsque vous lancez le navigateur de la machine virtuelle et ouvrez <http://localhost:8080>.

Pour démarrer Tomcat depuis des machines externes, vous devez créer un point de terminaison et ouvrir un port.

## Création d'un point de terminaison pour votre machine virtuelle

1.  Connectez-vous au [portail de gestion][portail de gestion Azure].
2.  Cliquez sur **Machines virtuelles**.
3.  Cliquez sur le nom de la machine virtuelle exécutant votre serveur d'applications Java.
4.  Cliquez sur **Endpoints**.
5.  Cliquez sur **Add**.
6.  Dans la boîte de dialogue **Ajouter un point de terminaison**, vérifiez que la case à cocher **Add standalone endpoint** est activée, puis cliquez sur **Suivant**.
7.  Dans la boîte de dialogue **New endpoint details** :
    1.  Entrez un nom pour le point de terminaison, par exemple **HttpIn**.
    2.  Indiquez **TCP** pour le protocole.
    3.  Indiquez **80** pour le port public.
    4.  Indiquez **8080** pour le port privé.
    5.  Cliquez sur le bouton **Complete** pour fermer la boîte de dialogue. Votre point de terminaison est maintenant créé.

## Ouverture d'un port dans le pare-feu pour votre machine virtuelle

1.  Connectez-vous à votre machine virtuelle.
2.  Cliquez sur le menu **Démarrage de Windows**.
3.  Cliquez sur **Panneau de configuration**.
4.  Cliquez sur **Système et sécurité**, sur **Pare-feu Windows**, puis sur **Paramètres avancés**.
5.  Cliquez sur **Règles de trafic entrant**, puis sur **Nouvelle règle**.

	![Nouvelle règle de trafic entrant][Nouvelle règle de trafic entrant]

1.  Pour la nouvelle règle, sélectionnez **Port** pour le **Type de règle**, puis cliquez sur **Suivant**.

	![Porte de nouvelle règle de trafic entrant][Porte de nouvelle règle de trafic entrant]

1.  Sélectionnez **TCP** pour le protocole et indiquez **8080** pour le port, puis cliquez sur **Suivant**.

	![Nouvelle règle de trafic entrant][1]

1.  Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	![Action de nouvelle règle de trafic entrant][Action de nouvelle règle de trafic entrant]

1.  Vérifiez que les cases à cocher **Domaine**, **Privé** et **Public** sont activées pour le profil, puis cliquez sur **Suivant**.

	![Profil de nouvelle règle de trafic entrant][Profil de nouvelle règle de trafic entrant]

1.  Indiquez un nom pour la règle, comme **HttpIn** (il n'est pas nécessaire que ce nom corresponde à celui du point de terminaison), puis cliquez sur **Terminer**.

	![Nom de nouvelle règle de trafic entrant][Nom de nouvelle règle de trafic entrant]

Vous pouvez désormais afficher votre site Web Tomcat dans un navigateur externe, en utilisant une URL au format **http://*votre_nom_DNS*.cloudapp.net**, où ***votre_nom_DNS*** correspond au nom DNS que vous avez indiqué lors de la création de la machine virtuelle.

## Considérations relatives au cycle de vie de l'application

-   Vous pouvez créer votre propre archive Web d'application (WAR) et l'ajouter au dossier **webapps**. Par exemple, créez un projet Web dynamique JSP (Java Service Page) de base et exportez-le en tant que fichier WAR, copiez celui-ci dans le dossier **webapps** d'Apache Tomcat sur la machine virtuelle, puis exécutez-le dans un navigateur.
-   Par défaut, lorsque le service Tomcat est installé, il est configuré pour être démarré manuellement. Vous pouvez le configurer pour démarrer automatiquement en utilisant le composant logiciel enfichable Services. Démarrez-le en cliquant sur le menu **Démarrage de Windows**, **Outils d'administration**, puis **Services**. Pour configurer Tomcat pour qu'il démarre automatiquement, double-cliquez sur le service **Apache Tomcat** dans le composant logiciel enfichable Services et définissez le **Type de démarrage** sur **Automatique**, comme sur la capture d'écran suivante.

    ![Configurer un service pour qu'il démarre automatiquement][Configurer un service pour qu'il démarre automatiquement]

    L'activation du démarrage automatique de Tomcat lui permet de redémarrer en même temps que la machine virtuelle (par exemple, après des mises à jour logicielles nécessitant un redémarrage).

## Étapes suivantes

-   Pour en savoir plus sur les autres services, tels que le stockage Azure, Service Bus, la base de données SQL, etc. à inclure dans vos applications Java, consultez les informations disponibles sur la page <http://www.windowsazure.com/fr-fr/develop/java/>.

  [Machine virtuelle exécutant Apache Tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Nouvelle règle de trafic entrant]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
  [Porte de nouvelle règle de trafic entrant]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
  [1]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
  [Action de nouvelle règle de trafic entrant]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
  [Profil de nouvelle règle de trafic entrant]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png
  [Nom de nouvelle règle de trafic entrant]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
  [Configurer un service pour qu'il démarre automatiquement]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png
