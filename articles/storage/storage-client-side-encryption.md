<properties 
	pageTitle="Chiffrement côté client pour Microsoft Azure Storage | Microsoft Azure" 
	description="La version préliminaire de la bibliothèque cliente de stockage Azure pour .NET prend en charge le chiffrement côté client et l’intégration avec le coffre de clés Azure. Étant donné que vos clés d’accès ne sont jamais accessibles au service, le chiffrement côté client offre une sécurité maximale pour vos applications Azure Storage. Le chiffrement côté client est disponible pour les objets blob, les files d’attente et les tables." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="tamram"/>


# Chiffrement côté client pour Microsoft Azure Storage (version préliminaire)

## Vue d'ensemble

Bienvenue dans la [version préliminaire de la nouvelle bibliothèque cliente de stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview). Cette bibliothèque en version préliminaire contient de nouvelles fonctionnalités qui permettent aux développeurs de chiffrer les données dans les applications clientes, avant de les télécharger vers Azure Storage, et de déchiffrer les données pendant le téléchargement. La bibliothèque en version préliminaire prend également en charge l’intégration avec le [coffre de clés](http://azure.microsoft.com/services/key-vault/) Azure pour la gestion des clés de compte de stockage.

## Pourquoi utiliser le chiffrement côté client ?

Le chiffrement côté client offre un avantage significatif sur le chiffrement côté serveur : vous avez le contrôle total des clés d’accès aux comptes. Le chiffrement côté client offre une sécurité maximale pour vos applications, car Azure Storage ne voit jamais vos clés et ne peut jamais déchiffrer vos données. La bibliothèque en version préliminaire est disponible sans limitation sur [GitHub](https://github.com/Azure/azure-storage-net/tree/preview). Ainsi, vous pouvez voir comment elle chiffre vos données et vous assurer qu’elle satisfait à vos normes.

## Pourquoi proposons-nous une bibliothèque avec prise en charge du chiffrement côté client ?

Bien que les développeurs puissent chiffrer leurs données côté client avant de les télécharger, cette opération suppose qu’ils soient experts en chiffrement. Elle requiert également une conception axée sur la performance et la sécurité. Chaque développeur concevrait alors sa propre solution de chiffrement, et chaque solution étant différente, aucune d’entre elles ne pourrait fonctionner avec les autres.

La bibliothèque en version préliminaire est conçue pour :

- Mettre en œuvre les meilleures pratiques pour vous.
- Maximiser les performances.
- Assurer la simplicité d’utilisation pour les scénarios courants.
- Prendre en charge l’interopérabilité entre langages. Désormais, les données chiffrées à l’aide de la bibliothèque cliente .NET sont déchiffrables par les bibliothèques clientes pour les autres langages que nous prenons en charge, y compris Java, Node.js et C++ (et inversement).

## Qu’est-ce qui est disponible maintenant ?

La bibliothèque en version préliminaire prend actuellement en charge le chiffrement des objets blob, des tables et des files d’attente à l’aide de la technique d’enveloppe. Le chiffrement et le déchiffrement à l’aide de clés asymétriques sont onéreux en termes de calculs. Par conséquent, dans la technique d’enveloppe, les données elles-mêmes ne sont pas chiffrées directement avec ces clés mais à l’aide d’une clé de chiffrement de contenu symétrique aléatoire. Cette clé de chiffrement de contenu est ensuite chiffrée à l’aide d’une clé publique. La prise en charge du coffre de clés Azure vous permet de gérer vos clés efficacement.

Le chiffrement côté client est simple. Vous pouvez spécifier des options de requête avec la stratégie de chiffrement appropriée (objet blob, file d’attente ou table) et les transmettre aux API de chargement/téléchargement de données. La bibliothèque cliente chiffre automatiquement les données sur le client lors du téléchargement vers Azure Storage et déchiffre les données lors de leur récupération. Vous trouverez de plus amples informations et des exemples de code dans le billet de blog intitulé [Prise en main du chiffrement côté client pour Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx).

Informations complémentaires sur le chiffrement côté client :

- **Sécurité** : il n’est pas possible de lire les données chiffrées même si les clés de compte de stockage du client sont compromises.
- **Coûts indirects de chiffrement fixes** : le volume de vos données chiffrées est prévisible en fonction du volume d’origine.
- **Chiffrement intégré** : chaque objet blob, entité de table ou message de file d’attente stocke toutes les informations de chiffrement dans l’objet lui-même ou dans ses métadonnées. La seule valeur externe requise est votre clé de chiffrement.
- **Rotation de clés** : les utilisateurs peuvent effectuer une rotation de clés eux-mêmes, et plusieurs clés sont prises en charge lors du processus de rotation de clés.
- **Chemin de mise à niveau propre** : des algorithmes de chiffrement et versions de protocole supplémentaires seront prochainement pris en charge, sans que vous deviez apporter de modifications importantes à votre code.
- **Prise en charge du chiffrement d’objets blob** :
	- **Téléchargement complet d’objets blob** : vous pouvez chiffrer et télécharger des fichiers tels que des documents, des photos et des vidéos dans leur intégralité.
	- **Téléchargement d’objets blob complet ou basé sur une plage** : vous pouvez télécharger et déchiffrer un objet blob dans son intégralité ou par plage.


>[AZURE.IMPORTANT]Tenez compte des points importants suivants lors de l’utilisation de la bibliothèque en version préliminaire :
>
>- N’utilisez pas la bibliothèque en version préliminaire avec des données de production. Les modifications apportées à la bibliothèque affecteront les schémas utilisés. Le déchiffrement des données qui ont été chiffrées avec la bibliothèque en version préliminaire n’est pas garanti dans les versions futures.  
>- Lors de la lecture d’un objet blob chiffré ou de l’écriture dans un objet blob chiffré, utilisez les commandes de téléchargement complet d’objets blob et de téléchargement d’objets blob complet/par plage. N’écrivez pas dans un objet blob chiffré à l’aide d’opérations de protocole telles que Put Block, Put Block List, Write Pages ou Clear Pages au risque d’endommager l’objet blob chiffré et de le rendre illisible.
>- Pour les tables, une contrainte similaire existe. Veillez à ne pas mettre à jour les propriétés chiffrées sans aussi mettre à jour les métadonnées de chiffrement.
>- Si vous définissez des métadonnées sur l’objet blob chiffré, vous risquez de remplacer les métadonnées relatives au chiffrement et nécessaires au déchiffrement, car la définition des métadonnées n’est pas additive. Cela est également vrai pour les instantanés : évitez de spécifier des métadonnées lors de la création d’un instantané d’objet blob chiffré.

## Voir aussi

- [Prise en main du chiffrement côté client pour Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)  
- [Bibliothèque cliente Azure Storage pour le package .NET NuGet (version préliminaire)](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview)  
- [Bibliothèque cliente Azure Storage pour le code source .NET (version préliminaire)](https://github.com/Azure/azure-storage-net/tree/preview)
 

<!---HONumber=62-->