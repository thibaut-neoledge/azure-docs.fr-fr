<properties
   pageTitle="Extraction de données SQL dans des hubs d’événements Azure | Microsoft Azure"
   description="Vue d’ensemble de l’importation de hubs d’événements à partir de l’exemple SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/31/2016"
   ms.author="spyros;sethm" />

# Extraction de données de SQL dans un hub d’événements Azure

Dans l’architecture standard d’une application de traitement de données en temps réel, les données sont placées dans un hub d’événements Azure. Nous pouvons retrouver cette architecture dans un scénario IoT, tel que la surveillance du trafic sur différentes portions d’une autoroute, dans un scénario de jeu, tel que la surveillance des actions d’une horde d’adversaires déchaînés, ou dans un scénario d’entreprise, tel que la surveillance de l’occupation d’un bâtiment. Dans ces cas de figure, les producteurs de données sont généralement des objets externes qui génèrent des données de série chronologique que vous devez collecter, analyser, stocker et manipuler. Vous avez déployé peut-être beaucoup d’efforts pour créer l’infrastructure de ces processus. Que faire si vous souhaitez importer des données d’un emplacement tel qu’une base de données plutôt que d’une source de données de diffusion et les utiliser conjointement avec vos autres données de diffusion ? Prenez le cas où vous souhaitez utiliser Azure Stream Analytics, Remote Data Explorer (RDX) ou un autre outil pour analyser et manipuler des données qui évoluent lentement à partir de Microsoft Dynamics AX ou d’un système de gestion d’installations personnalisé. Pour résoudre ce problème, nous avons écrit et publié un exemple de petit cloud open source que vous pouvez modifier et déployer. Ce petit cloud vous permet d’extraire des données d’une table SQL et de les transmettre à un hub d’événements Azure en vue de leur utilisation comme entrée de vos applications analytiques en aval. Ce scénario est rare, et est l’inverse de ce que vous faites normalement avec un hub d’événements. Toutefois, si vous êtes amené à agir ainsi, le code se trouve dans la galerie d’exemples Azure, [ici](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).

Notez que le code dans cet exemple n’est, justement, qu’un exemple. Il **ne s’agit pas** d’une application de production et aucun essai n’a été effectué pour la rendre utilisable dans un tel environnement : il s’agit strictement d’un exemple que les développeurs peuvent manipuler. Vous devez vérifier toutes sortes de facteurs de sécurité, performance, fonctionnalité et coût avant de choisir une architecture de bout en bout.

## Structure d’application

L’application est écrite en code C#, et le fichier readme.md de l’exemple contient toutes les informations dont vous avez besoin pour modifier, générer et publier l’application. Les sections suivantes fournissent une vue d’ensemble détaillée de l’application.

Nous commençons en partant du principe que vous avez accès à une table SQL Azure. Vous devez également avoir configuré un hub d’événements Azure et connaître la chaîne de connexion nécessaire pour y accéder.

Quand la solution SqlToEventHub démarre, elle lit un fichier de configuration (App.config) pour obtenir plusieurs éléments, décrits dans le fichier readme.md. Ceux-ci étant relativement explicites, à l’image du nom de la table de données, il est inutile de les décrire ici.

Après avoir lu le fichier de configuration, l’application entre dans une boucle, au cours de laquelle elle lit la table SQL, envoie les enregistrements au hub d’événements, puis attend pendant un intervalle de veille défini par l’utilisateur avant de tout recommencer. Plusieurs choses sont à noter :

1. L’application est basée sur l’hypothèse que la table SQL est mise à jour par un processus externe, et que vous souhaitez envoyer toutes les mises à jour, et uniquement celles-ci, à un hub d’événements.
2. La table SQL doit avoir un champ qui contient un numéro unique et croissant, par exemple, un numéro d’enregistrement. Il peut s’agir d’un simple champ appelé « ID », ou d’un champ qui est incrémenté à mesure que la base de données ajoute des enregistrements, tel que « date\_création » ou « numéro\_séquence ». L’application note et stocke la valeur de ce champ à chaque itération. À chaque itération suivante de la boucle, l’application interroge essentiellement la table pour connaître tous les enregistrements où la valeur de ce champ est supérieure à la valeur constatée pendant la dernière itération de la boucle. Nous allons appeler cette dernière valeur « offset » (écart).
3. Au démarrage, l’application crée une table « TableOffsets », pour y stocker les écarts. La table est créée avec la requête « CreateOffsetTableQuery » définie dans le fichier de configuration. 
4. Plusieurs requêtes sont liées à la table offset ; elles sont définies dans le fichier de configuration sous les termes « OffsetQuery », « UpdateOffsetQuery » et « InsertOffsetQuery ». Vous ne devez pas les modifier.
5. Enfin, la requête « DataQuery » définie dans le fichier de configuration est la requête à exécuter pour extraire les enregistrements de la table SQL. Elle est actuellement limitée aux 1 000 premiers enregistrements dans chaque itération de la boucle à des fins d’optimisation ; si, par exemple, 25 000 enregistrements ont été ajoutés à la base de données depuis la dernière requête, l’exécution de la requête peut prendre un certain temps. En étant systématiquement limitées à 1 000 enregistrements, les requêtes sont beaucoup plus rapides. Sélectionner les 1 000 premiers enregistrements permet d’alimenter le hub d’événements par lots successifs de 1 000 enregistrements.    

## Étapes suivantes

Pour déployer la solution, clonez ou téléchargez l’application SqlToEventHub, modifiez le fichier App.config, générez-le, puis publiez-le. Une fois que vous avez publié l’application, vous pouvez la voir s’exécuter dans le portail Azure Classic sous Cloud Services et surveiller les événements arrivant dans votre hub d’événements. Notez que la fréquence est déterminée par deux éléments : la fréquence des mises à jour de la table SQL et l’intervalle de veille que vous avez spécifié dans le fichier de configuration de l’application.

<!---HONumber=AcomDC_0601_2016-->