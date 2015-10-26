<properties 
   pageTitle="Prise en main du masquage de données dynamiques de base de données SQL (portail Azure)" 
   description="Prise en main du masquage des données dynamiques de base de données SQL dans le portail Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein; ronitr"/>

# Prise en main du masquage de données dynamiques de base de données SQL (portail Azure)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure Preview portal](sql-database-dynamic-data-masking-get-started.md)

## Vue d'ensemble

Le masquage des données dynamiques de base de données SQL limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. Le masquage des données dynamiques est en version préliminaire pour les niveaux de service de Base, Standard et Premium dans la version V12 de la base de données SQL Azure.

Le masquage des données dynamiques empêche tout accès non autorisé aux données sensibles en permettant aux clients d’indiquer la quantité de données sensibles à faire apparaître, avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées.

Par exemple, une personne chargée du soutien technique au centre d’appel peut identifier les appelants par plusieurs chiffres de leur numéro de sécurité sociale ou de carte de crédit, mais ces éléments de données ne doivent pas être divulgués entièrement à la personne du support technique. Un développeur peut définir une règle de masquage à appliquer à chaque résultat de requête, qui ne fait apparaître que les quatre derniers chiffres du numéro de sécurité sociale ou de carte de crédit dans l’ensemble de résultats. En utilisant le masque de données approprié pour protéger les informations d’identification personnelle (PII), un développeur peut interroger les environnements de production à des fins de dépannage, sans aller à l’encontre des réglementations de conformité.

## Principes fondamentaux du masquage de données dynamiques de base de données SQL

Vous définissez une stratégie de masquage des données dynamiques dans le portail Azure pour votre base de données, dans l’onglet Audit et sécurité. Avant de configurer le masquage des données dynamiques, assurez-vous que vous utilisez bien un [« Client de bas niveau »](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


> [AZURE.NOTE]Pour configurer le masquage des données dynamiques dans le portail Azure en version préliminaire, voir [Prise en main du masquage des données dynamiques de base de données SQL (portail Azure en version préliminaire)](sql-database-dynamic-data-masking-get-started.md).


### Autorisations du masquage des données dynamiques

Le masquage des données dynamiques peut être configuré par l’administrateur de base de données Azure, l’administrateur de serveur ou le responsable de la sécurité.

### Stratégie de masquage des données dynamiques

* **Connexions privilégiées** : ensemble de connexions qui obtiennent les données masquées dans les résultats de requêtes SQL.
  
* **Règles de masquage** : ensemble de règles qui définissent les champs désignés qui seront masqués et la fonction de masquage qui sera utilisée. Les champs désignés peuvent être définis à l’aide d’un nom de table de base de données et d’un nom de colonne.

* **Fonctions de masquage** : ensemble de méthodes qui contrôlent l’exposition des données dans différents scénarios.

| Fonction de masquage | Logique de masquage |
|----------|---------------|
| **Par défaut** |**Masquage complet selon les types de données des champs désignés**<br/><br/>• Utilisez XXXXXXXX ou moins de X si la taille du champ est inférieure à 8 caractères pour les données de type chaîne (nchar, ntext, nvarchar).<br/>• Utilisez la valeur zéro pour les types de données numériques (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Utilisez 01-01-1900 pour les types de données date/heure (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Pour les variantes SQL, la valeur par défaut du type en cours est utilisé.<br/>• Pour XML, le document <masked/> est utilisé.<br/>• Utilisez une valeur vide pour les types de données spéciaux (table timestamp, hierarchyid, GUID, binary, image, varbinary spatial).
| **Carte de crédit** |**Méthode de masquage qui affiche les quatre derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d’une carte de crédit.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Numéro de sécurité sociale** |**Méthode de masquage qui affiche les deux derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d’un numéro de sécurité sociale américain. <br/><br/>XXX-XX-XX12 |
| **E-mail** | **Méthode de masquage qui affiche la première lettre et remplace le domaine par XXX.com** à l’aide d’une chaîne constante comme préfixe sous la forme d’une adresse de messagerie.<br/><br/>aXX@XXXX.com |
| **Nombre aléatoire** | **Méthode de masquage qui génère un nombre aléatoire** selon les limites sélectionnées et les types de données réelles. Si les limites désignées sont égales, la fonction de masquage sera un nombre constant.<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Texte personnalisé** | **Méthode de masquage qui affiche le premier et le dernier caractères** et ajoute une chaîne de remplissage personnalisée entre les deux.<br/>préfixe[remplissage]suffixe<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### Chaîne de connexion sécurisée

Si vous utilisez un [« Client de bas niveau »](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md), alors vous devez mettre à jour les clients existants (exemple : applications) pour utiliser un format de chaîne de connexion modifiée. Pour plus d’informations, cliquez [ici](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


## Configuration du masquage des données dynamiques pour votre base de données à l’aide du portail Azure

1. Accédez à l’adresse [https://manage.windowsazure.com](https://manage.windowsazure.com) et exécutez le portail Azure.

2. Cliquez sur la base de données à masquer, puis sur l’onglet **AUDIT ET SÉCURITÉ**.

3. Dans **masquage de données dynamiques**, cliquez sur **ACTIVÉ** pour activer la fonctionnalité de masquage des données dynamiques.

4. Tapez les noms d’accès privilégiés qui doivent avoir accès aux données sensibles non masquées.

	>[AZURE.TIP]Pour que la couche d’application puisse afficher des données sensibles aux utilisateurs d’application privilégiés, ajoutez le nom de connexion SQL utilisé pour interroger la base de données. Il est vivement recommandé de limiter le nombre de connexions dans cette liste afin de limiter l’exposition des données sensibles.

	![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. Au bas de la page, dans la barre de menus, cliquez sur **Ajouter un MASQUE** pour ouvrir la fenêtre de configuration des règles de masquage.

6. Entrez le nom de la **Table** et de la **Colonne** dans les listes déroulantes pour définir les champs désignés qui seront masqués.

7. Choisissez une **FONCTION DE MASQUAGE** dans la liste des catégories de masquage des données sensibles.

	![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)
 	
8. Cliquez sur **OK** dans la fenêtre des règles de masquage des données pour mettre à jour l’ensemble des règles de la stratégie de masquage des données dynamiques.

9. Cliquez sur **ENREGISTRER** pour enregistrer la stratégie de masquage, nouvelle ou mise à jour.


## Configuration du masquage des données dynamiques pour votre base de données à l’aide des cmdlets Powershell

Voir [Cmdlets de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt163521.aspx).

## Configuration du masquage des données dynamiques pour votre base de données à l’aide de l’API REST

Voir [Opérations pour les bases de données SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=Oct15_HO3-->