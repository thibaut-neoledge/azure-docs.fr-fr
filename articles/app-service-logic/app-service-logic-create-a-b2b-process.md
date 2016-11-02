<properties 
   pageTitle="Création d’un processus B2B dans Azure App Service | Microsoft Azure" 
   description="Vue d’ensemble de la création d’un processus B2B (entreprise-entreprise)" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Création d'un processus B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## Scénario d'entreprise 
Contoso et Northwind sont des partenaires commerciaux. Contoso (le détaillant) envoie des bons de commande à Northwind (le fournisseur) via un transport standard tel que AS2. Northwind stocke toutes les commandes entrantes dans son stockage cloud. Les bons de commande sont des messages XML entre ces deux partenaires. Une fois le message stocké dans le stockage cloud de Northwind, les processus internes de Northwind gèrent la commande.
 
L'objectif de ce didacticiel consiste à déterminer comment Northwind peut établir un processus d'entreprise via lequel elle peut recevoir des messages (bons de commande au format XML) envoyés par son partenaire Contoso via AS2, puis les conserver dans son stockage cloud.


## Capacités démontrées 
Ce didacticiel aide à illustrer les capacités suivantes :

- **Transport des messages** : le détaillant et le fournisseur peuvent utiliser différentes plateformes, mais ils peuvent toujours établir des communications entre ces plateformes. Dans ce didacticiel, ils communiquent via AS2 (Applicability Statement 2). AS2 est une norme couramment utilisée pour transporter des données entre des partenaires commerciaux lors de communications B2B.
- **Persistance des données** :une fois le message reçu via AS2, Northwind souhaite le conserver avant tout traitement supplémentaire. Elle peut utiliser un connecteur pour conserver les messages dans son stockage cloud. Dans ce didacticiel, des objets blob Azure sont utilisés comme stockage cloud pour Northwind.
- **Création d’un processus d’entreprise** : dans un flux, plusieurs applications API peuvent être assemblées pour obtenir un résultat semblable à celui illustré ici.


## Avant de commencer
Ce didacticiel part du principe que vous connaissez Azure App Services et que vous savez comment créer des applications API et combiner un flux.


## Étapes pour réaliser le scénario d'entreprise
**Créer et configurer les applications API nécessaires**

1. Créez une instance du **Connecteur d’objet blob de stockage Azure**. Vous devez pour cela connaître les informations d'identification d'un compte Azure Storage. Assurez-vous qu'il est prêt avant de commencer cette création.
2. Créez une instance de **Gestion des partenaires commerciaux BizTalk**. Cela nécessite une base de données SQL vide. Assurez-vous qu'elle est prête avant de commencer cette création.
3. Créez une instance du **connecteur AS2**. Cela nécessite également une base de données SQL vide. Assurez-vous qu'elle est prête avant de commencer cette création. En outre, si vous souhaitez archiver des messages dans le cadre du traitement AS2, vous pouvez fournir les informations d'identification d'un objet Blob Azure lors de sa création.
4. Configurez le service de gestion des partenaires commerciaux créé :
	1. Accédez à l'instance du service de gestion des partenaires commerciaux créé dans le cadre de la procédure ci-dessus.
	2. Utilisez l’option **Partenaires** sous *Composants* pour **Ajouter** un nouveau partenaire nommé **Contoso** et, dans son profil, ajoutez l’identité AS2 nécessaire.
	3. Utilisez l’option **Partenaires** sous *Composants* pour **Ajouter** un nouveau partenaire nommé **Northwind** et, dans son profil, ajoutez l’identité AS2 nécessaire.
	4. Utilisez l’option **Accords** sous *Composants* pour **Ajouter** un nouvel accord AS2 entre Northwind et Contoso. Northwind sera ici le partenaire hébergé et Contoso sera le partenaire invité. Le cas échéant, configurez la signature, le chiffrement, la compression et les accusés de réception lors de la création de cet accord. Au cas où des certificats doivent être utilisés, vous pouvez les télécharger via l’option **Certificats** quand vous parcourez le service de gestion des partenaires commerciaux créé.


## Créer un flux / processus d'entreprise
1. Créez un flux dans lequel la première étape est AS2. Faites glisser le **Connecteur AS2** et choisissez l’instance déjà créée. Choisissez Déclencheur comme fonctionnalité : ![][1]
2. Ensuite, faites glisser le **Connecteur d’objet blob de stockage Azure** et choisissez l’instance déjà créée. Choisissez Action comme fonctionnalité et sélectionnez **Charger l’objet blob** comme fonctionnalité souhaitée. Configurez selon les besoins.
3. Maintenant, créez/déployez le flux.


## Traitement des messages et dépannage
1. Il est temps de tester le flux que nous avons déployé. Envoyez des messages XML encapsulés dans AS2 (conformément à l’accord AS2 créé ci-dessus) au point de terminaison AS2 exposé par l’instance AS2Connector que vous avez créée. Vous devrez peut-être configurer l'authentification pour le point de terminaison pour qu'il soit accessible publiquement.
2. Les informations d'exécution concernant le flux sont exposées en accédant au flux, puis en parcourant pas à pas l'instance du flux qui a été exécutée.
3. Pour obtenir des informations sur le traitement AS2, naviguez jusqu'à l'instance AS2Connector impliquée, puis parcourez pas à pas le composant Suivi. Vous pouvez utiliser les filtres impliqués pour limiter l'affichage aux informations souhaitées.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 

<!---HONumber=AcomDC_0803_2016-->