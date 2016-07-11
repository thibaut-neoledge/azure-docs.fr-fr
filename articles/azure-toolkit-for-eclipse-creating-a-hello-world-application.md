<properties
    pageTitle="Créer un service cloud « Hello World » pour Azure dans Eclipse"
    description="Découvrez comment créer une application Hello World simple à l’aide du kit de ressources Azure pour Eclipse."
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
    ms.date="06/24/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# Créer un service cloud « Hello World » pour Azure dans Eclipse #

Les étapes suivantes vous montrent comment créer et déployer une application JSP de base dans Azure à l’aide du kit de ressources Azure pour Eclipse. Un exemple JSP est présenté par souci de simplicité, mais des étapes très similaires conviennent également pour un servlet Java en ce qui concerne le déploiement d’Azure.

L’application sera semblable à ce qui suit :

![][ic600360]

## Composants requis ##

* JDK (Java Development Kit) version 1.7 ou ultérieure.
* IDE (environnement de développement intégré) Eclipse pour développeurs Java EE, Indigo ou ultérieur, Vous pouvez le télécharger à partir de <http://www.eclipse.org/downloads/>.
* Distribution d’un serveur web ou d’un serveur d’applications basé sur Java, tel qu’Apache Tomcat, GlassFish, JBoss Application Server, Jetty ou IBM® WebSphere® Application Server Liberty Core.
* Un abonnement à Azure, pouvant être souscrit à l’adresse suivante : <http://azure.microsoft.com/pricing/purchase-options/>.
* Kit de ressources Azure pour Eclipse. Pour plus d’informations, consultez [Installation du kit de ressources Azure pour Eclipse][].

## Pour créer une application Hello World ##

Tout d’abord, nous allons commencer par créer un projet Java.

*  Démarrez Eclipse, puis, dans le menu, cliquez successivement sur **Fichier**, **Nouveau** et **Projet web dynamique**. (Si **Projet web dynamique** n’est pas répertorié en tant que projet disponible une fois que vous avez cliqué sur **Fichier** et **Nouveau**, procédez comme suit : cliquez successivement sur **Fichier**, **Nouveau**, **Projet...**, développez **Web**, puis cliquez sur **Projet web dynamique** et sur **Suivant**.)
*  Pour l’exemple de ce didacticiel, nommez le projet **MyHelloWorld**. Assurez-vous de bien utiliser ce nom, car les étapes suivantes de ce didacticiel partent du principe que votre fichier WAR se nomme MyHelloWorld. Votre écran se présente comme suit : ![][ic589576]
* Cliquez sur **Terminer**.
* Dans la vue Explorateur de projets d’Eclipse, développez **MyHelloWorld**. Cliquez avec le bouton droit sur **WebContent**, cliquez sur **New**, puis sur **JSP File**.
* Dans la boîte de dialogue **New JSP File**, nommez le fichier **index.jsp**. Conservez **MyHelloWorld/WebContent** comme dossier parent, comme illustré ci-dessous : ![][ic659262]
* Dans la boîte de dialogue **Sélectionner un modèle JSP**, sélectionnez **Nouveau fichier JSP (html)** pour les besoins de ce didacticiel, puis cliquez sur **Terminer**.
* Lorsque le fichier index.jsp s’ouvre dans Eclipse, ajoutez un texte pour afficher dynamiquement **Hello World!** dans l’élément `<body>` existant. Le contenu `<body>` mis à jour doit se présenter de la façon suivante :
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Enregistrez index.jsp.

## Méthode simple et rapide pour déployer votre application sur Azure ##

Dès qu’une application web Java est prête à être testée, vous pouvez utiliser le raccourci suivant pour l’essayer directement dans le cloud Azure.

1. Dans l’Explorateur de projets d’Eclipse, cliquez sur **MyHelloWorld**.
1. Dans la barre d’outils Eclipse, cliquez sur le bouton déroulant **Publier**, puis cliquez sur **Publier en tant que service Cloud Azure** ![][publishDropdownButton]
1. Si vous publiez cette application sur Azure pour la première fois et que vous n’avez pas créé de projet de déploiement Azure pour cette application auparavant, un projet de déploiement Azure est créé automatiquement pour vous. L’invite suivante doit s’afficher. Elle répertorie également le package JDK et le serveur d’applications qui seront déployés automatiquement pour exécuter votre application. ![][ic789598]

    Cette approche rapide permet de tester rapidement et facilement votre application dans Azure sans avoir à configurer un serveur ou un JDK spécifiques différents des valeurs par défaut. Si les valeurs par défaut vous conviennent, vous pouvez cliquer sur **OK** pour passer aux étapes suivantes. Toutefois, si vous souhaitez modifier le JDK ou le serveur d’applications à utiliser pour votre application, vous pouvez le faire ultérieurement en modifiant le projet de déploiement Azure qui a été créé automatiquement pour vous. Vous pouvez également cliquer sur **Annuler** et lire la section **À propos des projets de déploiement Azure** de ce didacticiel.
1. Dans la boîte de dialogue **Publier sur Azure** :
    1. Si la liste **Abonnement** ne comporte pas encore d’abonnements, procédez comme suit pour importer vos informations d’abonnement :
        1. Cliquez sur **Importer à partir du fichier PUBLISH-SETTINGS**.
        1. Dans la boîte de dialogue **Importation des informations d’abonnement**, cliquez sur **Télécharger le fichier PUBLISH-SETTINGS**. Si vous n’êtes pas encore connecté à votre compte Azure, vous y êtes maintenant invité. Vous êtes ensuite invité à enregistrer un fichier de paramètres de publication Azure. Enregistrez-le sur votre ordinateur local.
        1. Toujours dans la boîte de dialogue **Importation des informations d’abonnement**, cliquez sur le bouton **Parcourir**, sélectionnez le fichier de paramètres de publication que vous avez précédemment enregistré localement, puis cliquez sur **Ouvrir**. Votre écran doit ressembler à l’écran suivant : ![][ic644267]
        1. Cliquez sur **OK**.
    1. Dans **Abonnement**, sélectionnez l’abonnement que vous voulez utiliser pour le déploiement.
    1. Dans **Compte de stockage**, sélectionnez le compte de stockage que vous souhaitez utiliser ou cliquez sur **Nouveau** pour en créer un.
    1. Dans **Nom du service**, sélectionnez le service cloud que vous souhaitez utiliser ou cliquez sur **Nouveau** pour en créer un.
    1. Dans **Système d’exploitation cible**, sélectionnez la version du système d’exploitation que vous souhaitez utiliser pour le déploiement.
    1. Dans **Environnement cible**, pour les besoins de ce didacticiel, sélectionnez **Intermédiaire**. (Lorsque vous êtes prêt à procéder au déploiement sur votre site de production, vous devez modifier ce paramètre et le définir sur **Production**.)
    1. Facultatif : vérifiez que la case **Remplacer le déploiement précédent** est cochée si vous souhaitez que votre nouveau déploiement remplace automatiquement le déploiement précédent. Si vous activez cette option, vous ne rencontrerez pas de problèmes « Conflit (409) » lors de la publication vers le même emplacement. Notez que la boîte de dialogue **Publier sur Azure** contient une section **Accès à distance**. Par défaut, l’accès à distance n’est pas activé, et nous ne l’activerons pas pour cet exemple. Pour activer l’accès à distance, vous devez entrez un nom d’utilisateur et un mot de passe à utiliser lors de la connexion à distance. Pour plus d’informations sur l’accès à distance, consultez la page [Activation de l’accès à distance pour les déploiements Azure dans Eclipse][]. La boîte de dialogue **Publier sur Azure** apparaît, semblable à ce qui suit : ![][ic719488]
1. Cliquez sur **Publier** pour publier dans l’environnement intermédiaire. Lorsque vous êtes invité à effectuer une génération complète, cliquez sur **Oui**. La première génération peut prendre plusieurs minutes. Un **journal des activités Azure** s’affiche dans la section des vues Eclipse avec onglets. ![][ic719489] Vous pouvez utiliser ce journal, ainsi que la vue **Console** pour afficher la progression de votre déploiement. Vous pouvez également vous connecter au [portail de gestion Azure][] et utiliser la section **Cloud Services** pour surveiller l’état.
1. Si votre déploiement est effectué avec succès, le **journal des activités Azure** indique l’état **Publié**. Cliquez sur **Publié**, comme illustré dans l’image suivante pour ouvrir une instance de votre déploiement dans le navigateur. ![][ic719490]

Comme ce déploiement a été effectué vers un environnement intermédiaire, le nom DNS a la forme http://&lt;*guid*&gt;.cloudapp.net, et l’URL contient le nom DNS plus un suffixe correspondant à votre application. Par exemple : http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (La partie **MyHelloWorld** respecte la casse.) Vous pouvez également afficher le nom DNS si vous cliquez sur le nom du déploiement dans le portail de gestion de la plateforme Azure (dans la partie Cloud Services du portail de gestion).

Même si cette procédure pas à pas concernait un déploiement vers l’environnement intermédiaire, un déploiement vers un environnement de production suit la même procédure, à l’exception suivante : dans la boîte de dialogue **Publier sur Azure**, vous devez sélectionner **Production** à la place d’**Intermédiaire** dans le champ **Environnement cible**. Un déploiement effectué vers un environnement de production se traduit par une URL basée sur le nom DNS de votre choix, au lieu d’un GUID utilisé pour le développement intermédiaire.

>[AZURE.WARNING] À ce stade, vous avez déployé votre application Azure dans le cloud. Toutefois, avant de continuer, n’oubliez pas qu’une application déployée ne cesse de cumuler des heures facturables pour votre abonnement même si elle n’est pas en cours d’exécution. Il est donc extrêmement important de supprimer les déploiements non souhaités de votre abonnement Azure.

## À propos des projets de déploiement Azure ##

Pour déployer une ou plusieurs applications Java sur Azure, il est nécessaire de disposer d’un projet de déploiement Azure. Il sert de « package » dans lequel vos applications doivent être encapsulées en vue de leur publication sur Azure.

Outre les informations sur vos applications, un projet de déploiement Azure contient également des informations sur les autres composants clés de votre déploiement, particulièrement, le conteneur de serveur d’applications dans lequel exécuter votre application web, et l’environnement d’exécution Java dans lequel l’exécuter. Azure prend en charge une large sélection d’environnements d’exécution Java et de serveurs d’applications Java parmi lesquels vous pouvez choisir.

Même si l’exemple utilisé ici est considérablement simplifié à des fins pédagogiques, un projet de déploiement Azure peut également contenir d’autres informations de configuration importantes, qui vous permettent de créer des services cloud complexes, évolutifs, hautement disponibles, multiniveaux avec vos applications. Vous pouvez activer l’**affinité de session (« sessions rémanentes »)**, la **mise en cache rapide**, le **débogage à distance**, le **déchargement SSL**, le **routage de pare-feu/port**, l’**accès à distance** et plusieurs autres fonctionnalités puissantes.

Si vous avez terminé la section précédente de ce didacticiel (« Méthode simple et rapide pour déployer votre application sur Azure »), vous voyez maintenant un nouveau projet de déploiement Azure dans l’Explorateur de projets, généré automatiquement et nommé « **MyHelloWorld\_onAzure** ».

Vous pourriez également avoir débuté ce didacticiel en commençant par créer un projet de déploiement Azure vide avant d’y ajouter vos applications. Si ce processus est plus long, il vous permet cependant de contrôler davantage la configuration initiale et ce, dès le début.

Pour créer un projet de déploiement Azure depuis le début, cliquez sur le bouton **Nouveau projet de déploiement Azure** ![][ic710876].

Que vous utilisiez un projet de déploiement Azure déjà existant ou que vous en créiez un depuis le début, vous pouvez modifier ses paramètres et ses composants de configuration comme le JDK ou le serveur d’applications, tout aussi facilement et à tout moment.

Pour modifier le JDK, le serveur d’applications ou la liste des applications dans un projet de déploiement Azure existant :

1. Développez le nœud du projet (par exemple, **MyHelloWorld\_onAzure**) dans l’Explorateur de projets.
2. Cliquez avec le bouton droit sur **WorkerRole1**.
3. Développez le sous-menu **Azure** dans le menu contextuel.
4. Cliquez sur **Configuration du serveur**.

Que vous ayez démarré cette procédure de configuration du serveur en modifiant un projet de déploiement Azure existant comme indiqué ci-dessus ou en en créant un depuis le début, les mêmes types de boîte de dialogue s’affichent et vous permettent de configurer vos composants JDK, serveur et application. Pour découvrir comment modifier les paramètres dans ces boîtes de dialogue, par exemple pour modifier le JDK, le serveur d’applications, et pour ajouter ou supprimer des applications dans un déploiement, consultez l’article [Propriétés de configuration de serveur][].

## Windows uniquement : Pour déployer votre application vers l’émulateur de calcul ##

>[AZURE.NOTE] L’émulateur Azure est disponible uniquement sur Windows. Ignorez cette section si vous utilisez un autre système d’exploitation.

Si vous avez créé un projet de déploiement Azure, en suivant la procédure décrite plus haut, c’est-à-dire implicitement en publiant votre application sur Azure, le JDK et les serveurs d’applications ont été configurés pour le cloud, mais pas pour l’émulation locale. Pour préparer votre projet pour les tests dans l’émulateur local, procédez comme suit :

1. Dans l’Explorateur de projets d’Eclipse, cliquez sur **MyHelloWorld\_onAzure**.
1. Cliquez avec le bouton droit sur **WorkerRole1**.
1. Développez le sous-menu **Azure** dans le menu contextuel.
1. Cliquez sur **Configuration du serveur**.
1. Dans l’onglet **JDK**, vérifiez si le kit de ressources a préconfiguré un JDK local par défaut pour vous. Si tel n’est pas le cas ou si vous souhaitez modifier les valeurs par défaut, vérifiez que la case **Utiliser le JDK à partir de ce chemin d’accès pour un test local** est cochée et que l’emplacement d’installation du JDK que vous souhaitez utiliser est spécifié. Si vous souhaitez le modifier, cliquez sur le bouton **Parcourir**, puis sélectionnez l’emplacement du répertoire du JDK à utiliser à l’aide du contrôle de navigation.
1. Cliquez sur l’onglet **Serveur**.
1. Dans la zone de texte **Chemin d’accès du serveur local** située au bas de la boîte de dialogue, entrez le chemin d’accès d’un serveur installé localement, qui correspond au type et au numéro de version principale du serveur sélectionné en haut de la boîte de dialogue au-dessous de la case à cocher **Déployer un serveur de ce type**. Pour utiliser un autre type ou une autre version principale du serveur d’applications, modifiez d’abord la sélection sous cette case à cocher.
1. Cliquez sur **OK**.
1. Dans la barre d’outils Eclipse, cliquez sur le bouton **Exécuter dans l’émulateur Azure** ![][ic710879]. Si le bouton **Exécuter dans l’émulateur Azure** n’est pas activé, vérifiez que **MyHelloWorld\_onAzure** est sélectionné dans l’Explorateur de projets d’Eclipse, et assurez-vous que l’Explorateur de projets d’Eclipse est bien la fenêtre active. Ce faisant, une génération complète de votre projet est démarrée, puis votre application web Java est lancée dans l’émulateur de calcul. (Notez qu’en fonction des caractéristiques des performances de votre ordinateur, la première génération peut prendre de quelques secondes à quelques minutes, mais les générations suivantes seront plus rapides.) À l’issue de l’étape de la première génération, vous êtes invité par le contrôle de compte d’utilisateur (UAC) Windows à autoriser cette commande à apporter des modifications sur votre ordinateur. Cliquez sur **Oui**.

>[AZURE.IMPORTANT] Si l’invite UAC ne s’affiche pas, recherchez l’icône UAC dans la barre des tâches Windows et cliquez dessus. Il arrive que l’invite UAC ne s’affiche pas en tant que fenêtre de premier plan, mais qu’elle soit visible uniquement sous forme d’icône de barre des tâches.

1. Examinez la sortie de l’interface utilisateur de l’émulateur de calcul pour déterminer si votre projet comporte des problèmes. En fonction du contenu de votre déploiement, le démarrage complet de votre application dans l’émulateur de calcul peut prendre quelques minutes.
1. Démarrez votre navigateur et utilisez l’URL `http://localhost:8080/MyHelloWorld` comme adresse (la partie `MyHelloWorld` de l’URL respecte la casse). Votre application MyHelloWorld (sortie du fichier index.jsp) doit ressembler à l’image suivante : ![][ic589579]

Lorsque vous êtes prêt à arrêter l’exécution de votre application dans l’émulateur de calcul, dans la barre d’outils Eclipse, cliquez sur le bouton **Réinitialiser l’émulateur Azure** ![][ic710880].

## Pour supprimer votre déploiement ##

Pour supprimer votre déploiement dans le kit de ressources Azure pour Eclipse, vérifiez que le projet **MyHelloWorld\_onAzure** est sélectionné dans l’Explorateur de projets d’Eclipse, assurez-vous que l’Explorateur de projets Eclipse est la fenêtre active, puis cliquez sur le bouton **Annuler la publication** ![][ic710883] dans la barre d’outils Eclipse. (Vous pouvez faire de même en cliquant avec le bouton droit sur **MyHelloWorld\_onAzure** dans l’Explorateur de projets d’Eclipse, puis en cliquant sur **Azure** et sur **Annuler le déploiement à partir du cloud Azure**.) La boîte de dialogue **Annuler la publication d’un projet Azure** s’affiche.

![][ic719491]

Sélectionnez l’abonnement et le service cloud qui contient votre déploiement, sélectionnez le déploiement à supprimer, puis cliquez sur **Annuler la publication**.

(Une alternative à l’utilisation du kit de ressources pour supprimer le déploiement consiste à utiliser la section **Cloud Services** du portail de gestion Azure : accédez à votre déploiement, sélectionnez-le, puis cliquez sur le bouton **Supprimer**. Le déploiement est alors arrêté, puis supprimé. Si vous voulez uniquement arrêter le déploiement sans le supprimer, cliquez sur le bouton **Arrêter** et non sur le bouton **Supprimer**. Cependant, comme indiqué précédemment, si vous ne le supprimez pas, des frais facturables continueront de s’accumuler pour votre déploiement, même s’il est arrêté).

## Voir aussi ##

[Kit de ressources Azure pour Eclipse][]

[Installation du kit de ressources Azure pour Eclipse][]

[Nouveautés du kit de ressources Azure pour Eclipse][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure][].

<!-- URL List -->

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Activation de l’accès à distance pour les déploiements Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Installation du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriétés de configuration de serveur]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Nouveautés du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png

<!---HONumber=AcomDC_0629_2016-->