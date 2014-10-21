<properties title="Monitor a DocumentDB Account" pageTitle="Manage a DocumentDB account | Azure" description="Learn how to manage your DocumentDB account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/03/2014" ms.author="hawong"></tags>

# Gestion d'un compte DocumentDB

## Sommaire

-   [Affichage, copie et régénération de clés d'accès DocumentDB][]
-   [Gestion des paramètres de cohérence DocumentDB][]
-   [Gestion des paramètres de capacité DocumentDB][]
-   [Suppression d'un compte DocumentDB][]
-   [Étapes suivantes][]

## <span id="keys"></span></a> Affichage, copie et régénération de clés d'accès

Lorsque vous créez un compte DocumentDB, deux clés d'accès primaires sont
générées et peuvent être utilisées pour l'authentification lors de l'accès au compte
DocumentDB. En fournissant deux clés d'accès, DocumentDB vous permet
de régénérer les clés sans interruption de votre compte
DocumentDB.

Dans la [version préliminaire du portail de gestion
Azure](https://portal.azure.com/)[](http://manage.windowsazure.com),
accédez à la section **Clés** à partir du volet de votre compte DocumentDB afin d'afficher,
copier et régénérer les clés utilisées pour accéder à votre
compte DocumentDB.

![][]

### Affichage et copie d'une clé d'accès

1.      Dans la [version préliminaire du portail de gestion
Azure](https://portal.azure.com/), accédez à votre compte DocumentDB. 

2.      Dans l'objectif Récapitulatif, cliquez sur **Clés**.

3.      Dans le volet Clés, cliquez sur le bouton **Copier** à droite de la clé
que vous souhaitez copier.

4.      Appuyez sur Ctrl+C pour copier la clé.

  ![][1]

### Régénération de clés d'accès

Vous devez modifier périodiquement les clés d'accès à votre compte DocumentDB
pour garantir la sécurité des connexions. Deux clés d'accès
vous sont affectées afin de vous permettre de conserver des connexions au compte DocumentDB
à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

**Avertissement**

La régénération de vos clés d'accès affecte toutes les applications qui
dépendent de la clé actuelle. Tous les clients qui utilisent la clé d'accès pour
accéder au compte DocumentDB doivent être mis à jour pour utiliser la nouvelle clé.

Si certains de vos services de cloud ou applications utilisent le compte DocumentDB,
vous perdrez les connexions si vous régénérez les clés, sauf si vous remplacez
vos clés. Voici une procédure que vous pouvez utiliser :

1.      Mettez à jour la clé d'accès dans le code de votre application afin de référencer la
clé d'accès secondaire du compte DocumentDB.

2.      Régénérez la clé d'accès primaire pour votre compte DocumentDB.
Dans la  [version préliminaire du portail de gestion Azure](https://portal.azure.com/),
accédez à votre compte DocumentDB.

3.      Dans l'objectif Récapitulatif, cliquez sur **Clés**.

4.      Dans le volet Clés, cliquez sur la commande **Régénérer la clé primaire**, puis
cliquez sur **OK** pour confirmer que vous souhaitez générer une nouvelle clé.

5.      Une fois que vous avez vérifié que la nouvelle clé peut être utilisée
(environ 5 minutes après la régénération), mettez à jour la clé d'accès dans
le code de votre application afin de référencer la nouvelle clé d'accès primaire.

6.      Régénérez la clé d'accès secondaire.

*Notez qu'il faut attendre plusieurs minutes avant de pouvoir utiliser une clé qui vient d'être
générée pour accéder à votre compte DocumentDB.*

## <span id="consistency"></span></a> Gestion des paramètres de cohérence DocumentDB

DocumentDB prend en charge quatre niveaux de cohérence des données spécifiques configurables
afin de permettre aux développeurs de créer des compromis
cohérence-disponibilité-latence prévisibles.

·         **Strong** (Forte) - niveau de cohérence qui garantit que les opérations de lecture renvoient
toujours la dernière valeur écrite.

·         **Bounded Staleness** (En fonction de l'obsolescence) - niveau de cohérence qui garantit que les lectures ne sont
pas trop obsolètes. Il garantit en particulier que la version des lectures n'est pas
plus ancienne qu'un certain nombre de versions par rapport à la dernière version écrite.

·         **Session** (Par session) - niveau de cohérence qui garantit les lectures unitones (vous ne lisez jamais
d'anciennes données, puis de nouvelles données, puis d'anciennes données à nouveau), les écritures unitones (les écritures sont
ordonnées), et que vous lisez les écritures les plus récentes dans n'importe quel point de vue unique du
client.

·         **Eventual** (Éventuel) - niveau de cohérence qui garantit que les opérations de lecture
lisent toujours un sous-ensemble d'écritures valides, qui finiront par converger.

*Notez que, par défaut, les comptes DocumentDB utilisent un niveau de cohérence Session
(Par session). Pour plus d'informations sur les paramètres de cohérence
DocumentDB, voir la section [Niveau de
cohérence](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Pour spécifier le niveau de cohérence par défaut d'un compte DocumentDB

1.      Dans la [version préliminaire du portail de gestion
Azure](https://portal.azure.com/), accédez à votre compte DocumentDB. 

2.      Dans l'objectif Configuration, cliquez sur **Cohérence**.

3.      Dans le volet Cohérence par défaut, sélectionnez le niveau de cohérence
par défaut souhaité pour votre compte DocumentDB.

4.      Cliquez sur **Enregistrer**.

5.      La progression de l'opération peut être contrôlée via le hub Notifications
de la version préliminaire du portail de gestion Azure.

![][2]

![][3]

*Notez qu'il faut attendre plusieurs minutes avant qu'une modification du paramètre de cohérence
par défaut soit appliquée à votre compte DocumentDB.*

## <span id="capacity"></span></a> Gestion des paramètres de capacité DocumentDB

Microsoft Azure DocumentDB vous permet d'effectuer une mise à l'échelle souple, en fonction de l'évolution des
demandes de votre application au cours de son cycle de vie. La mise à l'échelle de
DocumentDB est réalisée en augmentant la capacité de votre compte de base de données DocumentDB
via la version préliminaire du portail de gestion Azure.

Lorsque vous créez un compte de base de données, il inclut un stockage
de base de données et un débit réservé. Vous pouvez à tout moment modifier le stockage de base de données
et le débit par défaut de votre compte en ajoutant
ou en supprimant des unités de capacité via la version préliminaire du portail de gestion Azure. 

### Pour ajouter ou supprimer des unités de capacité

1.      Dans la [version préliminaire du portail de gestion
Azure](https://portal.azure.com/), accédez à votre compte DocumentDB. 

2.      Dans l'objectif Utilisation, cliquez sur **Mettre à l'échelle**.

3.      Dans le volet Mettre à l'échelle, spécifiez le nombre d'unités de capacité
désiré pour votre compte DocumentDB.

4.      Cliquez sur **Enregistrer** (notez que l'opération de mise à l'échelle peut prendre
plusieurs minutes ; la progression de l'opération peut être contrôlée via le
hub Notifications de la version préliminaire du portail de gestion Azure).

*Notez que la version préliminaire de DocumentDB prend en charge un maximum de 5 unités de
capacité par compte DocumentDB.*

![][4]

 

## <span id="delete"></span></a> Suppression d'un compte DocumentDB

Pour supprimer un compte DocumentDB que vous n'utilisez plus, utilisez la commande
**Supprimer** du volet Compte DocumentDB.

**Avertissement**

*Dans la version préliminaire, il est impossible de récupérer le contenu d'un
compte DocumentDB supprimé. Supprimer un compte DocumentDB supprimera
toutes les ressources du compte, y compris les bases de données, collections,
documents et pièces jointes.*

![][5]

1.      Dans la [version préliminaire du portail de gestion
Azure](https://portal.azure.com/), accédez au compte DocumentDB à
supprimer. 

2.      Dans le volet du compte, cliquez sur la commande **Supprimer**.

3.      Dans le volet de confirmation qui s'affiche, entrez le nom du compte DocumentDB
afin de confirmer que vous souhaitez le supprimer.

4.      Cliquez sur le bouton **Supprimer** dans le volet de confirmation.

## <span id="next"></span></a>Étapes suivantes

-   [Prenez en main votre compte
    DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).
-   Pour en savoir plus sur DocumentDB, voir la documentation Azure DocumentDB
    sur
    [azure.com][]

 

  [Affichage, copie et régénération de clés d'accès DocumentDB]: #keys
  [Gestion des paramètres de cohérence DocumentDB]: #consistency
  [Gestion des paramètres de capacité DocumentDB]: #capacity
  [Suppression d'un compte DocumentDB]: #delete
  [Étapes suivantes]: #next
  []: http://manage.windowsazure.com
  []: ./media/documentdb-manage-account/image002.jpg
  [1]: ./media/documentdb-manage-account/image004.jpg
  [2]: ./media/documentdb-manage-account/image005.png
  [3]: ./media/documentdb-manage-account/image006.png
  [4]: ./media/documentdb-manage-account/image007.png
  [5]: ./media/documentdb-manage-account/image009.png
  [azure.com]: http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409
