<properties 
   pageTitle="Encodeur de fichier plat BizTalk" 
   description="Encodeur de fichier plat BizTalk" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/01/2015"
   ms.author="rajram"/>

# Encodeur de fichier plat BizTalk

Utilisez le connecteur encodeur de fichier plat BizTalk pour faciliter les interactions entre des données de fichiers plats (par exemple, des fichiers excel, csv) et des données au format XML. Il peut convertir une instance de fichier plat donnée au format XML et vice versa.

##Utilisation de l’encodeur de fichier plat BizTalk
Pour utiliser l'encodeur de fichier plat BizTalk, vous devez d'abord créer une instance de l'application API d’encodeur de fichier plat BizTalk. Cela est possible inline, lors de la création d'une application logique, ou en sélectionnant l'application API d’encodeur de fichier plat BizTalk dans Azure Marketplace.

###Configurer l’encodeur de fichier plat BizTalk
L’encodeur de fichier plat BizTalk utilise des schémas dans le cadre de sa configuration. Les utilisateurs peuvent lancer le panneau de configuration de l'application API soit en lançant directement l'application API depuis le portail Azure, soit en double-cliquant sur l'application API dans l'aire du concepteur.

![Configuration de l’encodeur de fichier plat BizTalk][1]

Dans le panneau de l'application API, les utilisateurs peuvent configurer des schémas en cliquant sur la partie *Schémas*.

![Partie du schéma de l’encodeur de fichier plat BizTalk][2]

Les utilisateurs peuvent télécharger des schémas à partir d’un disque ou en générer un à partir d'une instance de fichier plat ou d’une instance JSON.

![Partie du schéma de l’encodeur de fichier plat BizTalk][3]


###Utilisation de l’encodeur de fichier plat BizTalk dans l’aire de conception
Une fois celui-ci configuré, les utilisateurs peuvent cliquer sur *->* et choisir une action dans une liste.

![Liste d’actions de l’encodeur de fichier plat BizTalk][4]

####Convertir un fichier plat en fichier XML

![Liste d’actions de l’encodeur de fichier plat BizTalk][5]

Paramètre|Type|Description du paramètre
---|---|---
Fichiers plats|string|Contenu du fichier plat d'entrée
Nom du schéma|string|Nom du schéma qui représente le fichier plat d'entrée
Nom de la racine|string|Nom du nœud racine du schéma de fichier plat


L'action retourne le résultat sous forme de chaîne - XML de sortie. Le XML de sortie contient la représentation XML du contenu du fichier plat d'entrée.

####Convertir un fichier XML dans un format de fichier plat

![Liste d’actions de l’encodeur de fichier plat BizTalk][6]

Paramètre|Type|Description du paramètre
---|---|---
XML d'entrée|string|Contenu du XML d’entrée

L'action retourne le résultat sous forme de chaîne - fichier plat. La sortie contient la représentation du fichier plat du contenu du fichier XML d'entrée.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
 

<!---HONumber=Oct15_HO3-->