---
title: "Débogage des applications Azure dans Eclipse"
description: "En savoir plus sur le débogage des applications Azure à l’aide de la Boîte à outils Azure pour Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 3d1b34bf-399e-421e-bdcc-da2d422dbb4f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 06b33c09f5c316f974efde1116cae699d8df1065


---
# <a name="debugging-azure-applications-in-eclipse"></a>Débogage des applications Azure dans Eclipse
La Boîte à outils Azure pour Eclipse vous permet de déboguer vos applications, qu’elles s’exécutent dans Azure ou dans l’émulateur de calcul si vous utilisez Windows comme système d’exploitation. L’illustration suivante montre la boîte de dialogue de propriétés **Débogage** qui sert à activer le débogage distant :

![][ic719504]

Ce didacticiel part du principe que vous avez déjà créé une application et que vous savez utiliser l’émulateur de calcul et effectuer un déploiement sur Azure.

Nous allons utiliser l’application du didacticiel [Utilisation de la bibliothèque Azure Service Runtime en JSP][Utilisation de la bibliothèque Azure Service Runtime en JSP] comme point de départ pour cette rubrique. Avant de commencer, créez cette application si ce n’est déjà fait.

## <a name="to-debug-your-application-while-running-in-azure"></a>Pour déboguer votre application pendant son exécution dans Azure
> [!WARNING]
> La prise en charge actuelle par la boîte à outils du débogage Java distant est destinée principalement aux déploiements exécutés dans l’émulateur de calcul Azure. La connexion de débogage n’étant pas sécurisée, vous ne devez pas activer le débogage distant dans les déploiements de production. Si vous devez déboguer une application exécutée dans le cloud Azure, utilisez un déploiement intermédiaire, mais sachez que l’accès non autorisé à votre session de débogage est possible si quelqu’un connaît l’adresse IP de votre déploiement cloud, même s’il s’agit d’un déploiement intermédiaire.
> 
> 

1. Générez votre projet pour le tester dans l’émulateur : dans l’Explorateur de projets d’Eclipse, cliquez avec le bouton droit de la souris sur **MonProjetAzure**, cliquez sur **Propriétés**, sur **Azure**, puis affectez la valeur **Déploiement dans le cloud** à **Générer pour**.
2. Régénérez votre projet : dans le menu Eclipse, cliquez sur **Projet**, puis sur **Générer tout**.
3. Effectuez un déploiement *intermédiaire* de votre application dans Azure.
    >[AZURE.IMPORTANT] Comme mentionné ci-dessus, nous vous recommandons vivement de déboguer dans l’émulateur de calcul dans la plupart des cas, puis de déboguer dans l’environnement intermédiaire uniquement si un débogage supplémentaire est nécessaire. Nous vous recommandons de ne pas déboguer dans l’environnement de production.
4. Une fois votre déploiement prêt dans Azure, récupérez le nom DNS du déploiement sur le [portail de gestion Azure][portail de gestion Azure]. Un déploiement intermédiaire a un nom DNS au format http://*&lt;guid&gt;*.cloudapp.net, où *&lt;guid&gt;* est une valeur GUID attribuée par Azure.
5. Dans l’Explorateur de projets d’Eclipse, cliquez avec le bouton droit de la souris sur **WorkerRole1**, cliquez sur **Azure**, puis sur **Débogage**.
6. Dans la boîte de dialogue **Propriétés de débogage pour WorkerRole1** :
   1. Cochez la case **Activer le débogage distant pour ce rôle**
   2. Pour **Point de terminaison d’entrée à utiliser**, utilisez **Débogage (public:8090, privé:8090)**.
   3. Vérifiez que l’option **Démarrer la JVM en mode d’attente, en attendant la connexion au débogueur** est désactivée.
       >[AZURE.IMPORTANT] L’option **Démarrer la JVM en mode d’attente, en attendant la connexion au débogueur** est conçue pour les scénarios de débogage avancés dans l’émulateur de calcul uniquement (et non pour les déploiements cloud). Si l’option **Démarrer la JVM en mode d’attente, en attendant la connexion au débogueur** est activée, elle interrompt le processus de démarrage du serveur jusqu’à ce que le débogueur Eclipse soit connecté à sa machine virtuelle Java. Vous pouvez utiliser cette option pour une session de débogage avec l’émulateur de calcul, mais ne l’utilisez pas pour une session de débogage dans un déploiement cloud. L’initialisation d’un serveur a lieu dans une tâche de démarrage Azure, et le cloud Azure ne rend pas les points de terminaison publics disponibles tant que la tâche de démarrage n’est pas terminée. Ainsi, un processus de démarrage ne se termine pas correctement si cette option est activée dans un déploiement cloud, car il ne pourra pas recevoir de connexion à partir d’un client Eclipse externe.
   4. Cliquez sur **Créer des configurations de débogage**.
7. Dans la boîte de dialogue **Configuration de débogage Azure** :
   1. Pour **Projet Java à déboguer**, sélectionnez le projet **MyHelloWorld**.
   2. Pour **Configurer le débogage pour**, cochez **Cloud Azure (intermédiaire)**.
   3. Assurez-vous que l’option **Émulateur de calcul Azure** soit désactivée.
   4. Pour **Hôte**, entrez le nom DNS de votre déploiement intermédiaire, mais sans le préfixe **http://**. Par exemple (insérez votre GUID à la place du GUID indiqué ici) : **4e616d65-6f6e-6d65-6973-526f62657274.cloudapp.net**
8. Cliquez sur **OK** pour fermer la boîte de dialogue **Configuration de débogage Azure**.
9. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de débogage pour WorkerRole1**.
10. Si vous n’avez pas encore défini de point d’arrêt dans index.jsp, faites-le maintenant :
    1. Dans l’Explorateur de projets d’Eclipse, développez **MyHelloWorld**, puis **WebContent**, puis double-cliquez sur **index.jsp**.
    2. Dans index.jsp, cliquez avec le bouton droit de la souris dans la barre bleue à gauche de votre code Java et cliquez sur **Activer/désactiver les points d’arrêt**, comme illustré ci-dessous :  ![][ic551537]
11. Dans le menu Eclipse, cliquez sur **Exécuter** puis sur **Déboguer les configurations**.
12. Dans la boîte de dialogue **Déboguer les configurations**, développez **Application Java distante** dans le volet gauche, sélectionnez **Cloud Azure (WorkerRole1)**, puis cliquez sur **Déboguer**.
13. Dans votre navigateur, exécutez votre application intermédiaire, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, en remplaçant *&lt;guid&gt;* par le GUID de votre nom DNS. Si la boîte de dialogue **Confirmer la commutation de perspective** s’affiche, cliquez sur **Oui**. Votre session de débogage doit maintenant s’exécuter jusqu’à la ligne de code où le point d’arrêt a été défini.

> [!NOTE]
> Si vous tentez de démarrer une connexion de débogage à distance vers un déploiement où plusieurs instances de rôles sont en cours d’exécution, vous ne pouvez pas contrôler à quelle instance le débogueur se connecte initialement, car l’équilibreur de charge Azure choisit une instance au hasard. Une fois connecté à cette instance, cependant, vous continuerez à déboguer la même instance. Notez également qu’en cas d’inactivité supérieure à quatre minutes (par exemple, lorsque vous êtes arrêté à un point d’arrêt pendant trop longtemps), Azure peut fermer la connexion.
> 
> 

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Débogage d’une instance de rôle spécifique dans un déploiement à plusieurs instances
Quand vous exécutez votre déploiement dans le cloud, vous l’exécutez généralement dans plusieurs instances de calcul, ou rôles. Cela vous permet de tirer parti de la garantie de disponibilité à 99,95 % offerte par Azure et de faire monter votre application en charge.

Dans ces scénarios-là, vous devrez peut-être déboguer à distance votre application Java dans une instance de rôle spécifique. Toutefois, si vous activez uniquement un point de terminaison d’entrée régulier pour le débogage, l’équilibreur de charge Azure rend pratiquement impossible la connexion du débogueur à une instance de rôle spécifique. Il vous connecte à une instance de rôle qu’il choisit au hasard.

C’est le type de scénario où le fait de tirer parti des points de terminaison d’entrée d’instance facilite le débogage d’une instance de rôle spécifique.

Supposez que vous prévoyez d’exécuter jusqu’à cinq instances de rôle de votre déploiement. À l’aide de la page de propriétés **Points de terminaison** dans la boîte de dialogue de propriétés de rôle, créez un point de terminaison d’entrée d’instance et attribuez-lui une plage de ports publics, plutôt qu’un numéro de port unique. Par exemple, dans la zone d’entrée **Port public**, spécifiez **81-85**.

Une fois que vous avez déployé votre application avec ce point de terminaison d’instance, Azure affecte un numéro de port unique compris dans cette plage à chacune des instances de rôle. Ensuite, pour connaître le numéro de port qui a été attribué à chaque instance, vous pouvez utiliser la variable d’environnement *nom_point_de_terminaison_instance ***_PUBLICPORT** (où *nom_point_de_terminaison_instance* est le nom que vous avez affecté lorsque vous avez créé le point de terminaison d’instance) configurée automatiquement par la boîte à outils dans votre déploiement (par exemple, en retournant sa valeur dans le pied de page d’une page web afin de pouvoir consulter le numéro de port quand vous y accéderez).

Une fois que vous connaissez le numéro de port public qui a été attribué à cette instance, vous pouvez y faire référence dans votre configuration de débogage dans Eclipse, en l’apposant au nom d’hôte de votre service. Cela permet au débogueur Eclipse de se connecter à cette instance spécifique, et non à d’autres instances.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Windows uniquement : pour déboguer votre application pendant son exécution dans l’émulateur de calcul
> [!NOTE]
> L’émulateur Azure est disponible uniquement sur Windows. Ignorez cette section si vous utilisez un système d’exploitation autre que Windows. 
> 
> 

1. Générez votre projet de test dans l’émulateur : dans l’Explorateur de projets d’Eclipse, cliquez avec le bouton droit de la sourie sur **MonProjetAzure**, cliquez sur **Propriétés**, sur **Azure**, puis affectez la valeur **Test** dans l’émulateur à **Générer pour**.
2. Régénérez votre projet : dans le menu Eclipse, cliquez sur **Projet**, puis sur **Générer tout**.
3. Dans l’Explorateur de projets d’Eclipse, cliquez avec le bouton droit de la souris sur **WorkerRole1**, cliquez sur **Azure**, puis sur **Débogage**.
4. Dans la boîte de dialogue **Propriétés de débogage pour WorkerRole1** :
   1. Cochez la case **Activer le débogage distant pour ce rôle**
   2. Pour **Point de terminaison d’entrée à utiliser**, utilisez le point de terminaison par défaut généré automatiquement par la boîte à outils, répertorié comme **Débogage (public:8090, privé:8090)**.
   3. Vérifiez que l’option **Démarrer la JVM en mode d’attente, en attendant la connexion au débogueur** est désactivée.
       >[AZURE.IMPORTANT] L’option **Démarrer la JVM en mode d’attente, en attendant la connexion au débogueur** est conçue pour les scénarios de débogage avancés dans l’émulateur de calcul uniquement (et non pour les déploiements cloud). Si l’option **Démarrer la JVM en mode d’attente, en attendant la connexion au débogueur** est activée, elle interrompt le processus de démarrage du serveur jusqu’à ce que le débogueur Eclipse soit connecté à sa machine virtuelle Java. Vous pouvez utiliser cette option pour une session de débogage avec l’émulateur de calcul, mais ne l’utilisez pas pour une session de débogage dans un déploiement cloud. L’initialisation d’un serveur a lieu dans une tâche de démarrage Azure, et le cloud Azure ne rend pas les points de terminaison publics disponibles tant que la tâche de démarrage n’est pas terminée. Ainsi, un processus de démarrage ne se termine pas correctement si cette option est activée dans un déploiement cloud, car il ne pourra pas recevoir de connexion à partir d’un client Eclipse externe.
   4. Cliquez sur **Créer des configurations de débogage**.
5. Dans la boîte de dialogue **Configuration de débogage Azure** :
   1. Pour **Projet Java à déboguer**, sélectionnez le projet **MyHelloWorld**.
   2. Pour **Configurer le débogage pour**, cochez **Émulateur de calcul Azure**.
6. Cliquez sur **OK** pour fermer la boîte de dialogue **Configuration de débogage Azure**.
7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de débogage pour WorkerRole1**.
8. Définissez un point d’arrêt dans index.jsp :
   
   1. Dans l’Explorateur de projets d’Eclipse, développez **MyHelloWorld**, puis **WebContent**, puis double-cliquez sur **index.jsp**.
   2. Dans index.jsp, cliquez avec le bouton droit de la souris dans la barre bleue à gauche de votre code Java et cliquez sur **Activer/désactiver les points d’arrêt**, comme illustré ci-dessous :  ![][ic551537]
      
      Un point d’arrêt est défini si une icône de point d’arrêt s’affiche dans la barre bleue à gauche du code Java.
9. Démarrez l’application dans l’émulateur de calcul en cliquant sur le bouton **Exécuter dans l’émulateur Azure** dans la barre d’outils Azure.
10. Dans le menu Eclipse, cliquez sur **Exécuter** puis sur **Déboguer les configurations**.
11. Dans la boîte de dialogue **Déboguer les configurations**, développez **Application Java distante** dans le volet gauche, sélectionnez **Émulateur Azure (WorkerRole1)**, puis cliquez sur **Déboguer**.
12. Une fois que l’émulateur de calcul indique que votre application est en cours d’exécution, dans votre navigateur, exécutez **http://localhost:8080/MyHelloWorld**.
     Si la boîte de dialogue **Confirmer la commutation de perspective** s’affiche, cliquez sur **Oui**.
     Votre session de débogage doit maintenant s’exécuter jusqu’à la ligne de code où le point d’arrêt a été défini.

Nous venons de voir comment déboguer dans l’émulateur de calcul. La section suivante montre comment déboguer une application déployée dans Azure.

## <a name="debugging-notes"></a>Remarques sur le débogage
* Après le débogage, vous pouvez faire basculer la perspective de **Déboguer** à **Java** en cliquant sur le menu Eclipse, sur **Fenêtre**, sur **Ouvrir une perspective**, puis en sélectionnant la perspective que vous souhaitez utiliser.
* Pour activer le débogage à distance dans GlassFish, n’utilisez pas la fonctionnalité de configuration du débogage à distance de la Boîte à outils Azure pour Eclipse. Au lieu de cela, configurez GlassFish manuellement. En raison de la façon dont GlassFish traite les options Java prédéfinies dans les variables d’environnement, la fonctionnalité de configuration du débogage à distance de la boîte à outils ne fonctionne pas correctement avec GlassFish. Si la fonctionnalité de configuration du débogage à distance de la boîte à outils est activée, elle peut empêcher GlassFish de démarrer.

## <a name="see-also"></a>Voir aussi
[Kit de ressources Azure pour Eclipse][Kit de ressources Azure pour Eclipse]

[Création d'une application Hello World pour Azure dans Eclipse][Création d'une application Hello World pour Azure dans Eclipse]

[Installation de la Boîte à outils Azure pour Eclipse][Installation de la Boîte à outils Azure pour Eclipse] 

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure][Centre de développement Java pour Azure].

<!-- URL List -->

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d'une application Hello World pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation de la Boîte à outils Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Utilisation de la bibliothèque Azure Service Runtime en JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->



<!--HONumber=Nov16_HO3-->


