<properties
   pageTitle="Prise en main du masquage des données dynamiques de base de données SQL (portail Azure Classic)"
   description="Comment prendre en main le masquage de données dynamiques de base de données SQL dans le portail Azure Classic"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jeffreyg"
   editor="jeffreyg"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/01/2015"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# Prise en main du masquage des données dynamiques de base de données SQL (portail Azure Classic)

> [AZURE.SELECTOR]
- [Masquage des données dynamiques - Portail Azure](sql-database-dynamic-data-masking-get-started.md)

## Vue d'ensemble

Le masquage des données dynamiques de base de données SQL limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. Le masquage des données dynamiques est pris en charge pour la version V12 de la base de données SQL Azure.

Le masquage des données dynamiques empêche tout accès non autorisé aux données sensibles en permettant aux clients d’indiquer la quantité de données sensibles à faire apparaître, avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées.

Par exemple, un représentant du centre d'appel peut identifier les appelants par plusieurs chiffres de leur numéro de sécurité sociale ou de carte de crédit, mais ces éléments de données ne doivent pas être divulgués entièrement au représentant du centre d'appel. Une règle de masquage peut être définie pour ne faire apparaître que les quatre derniers chiffres du numéro de sécurité sociale ou de carte de crédit dans l'ensemble de résultats de chaque requête. Autre exemple, un masque de données approprié peut être défini pour protéger les informations d'identification personnelle (PII), de telle sorte qu'un développeur puisse interroger les environnements de production à des fins de dépannage sans aller à l'encontre des réglementations de conformité.

## Principes fondamentaux du masquage de données dynamiques de base de données SQL

Vous définissez une stratégie de masquage des données dynamiques dans le portail Azure Classic pour votre base de données, dans l’onglet Audit et sécurité.


> [AZURE.NOTE] Pour configurer le masquage des données dynamiques dans le portail Azure, consultez [Prise en main du masquage des données dynamiques de base de données SQL (portail Azure)](sql-database-dynamic-data-masking-get-started.md).


### Autorisations du masquage des données dynamiques

Le masquage des données dynamiques peut être configuré par l’administrateur de base de données Azure, l’administrateur de serveur ou le responsable de la sécurité.

### Stratégie de masquage des données dynamiques

* **Utilisateurs SQL exclus du masquage** : ensemble d'utilisateurs SQL ou d'identités AAD qui obtiendront des données non masquées dans les résultats de requête SQL. Les utilisateurs possédant des privilèges administrateur sont toujours exclus du masquage et verront les données d'origine sans masque.

* **Règles de masquage** : ensemble de règles qui définissent les champs désignés à masquer et la fonction de masquage à utiliser. Les champs désignés peuvent être définis à l'aide d'un nom de schéma de base de données, d'un nom de table et d'un nom de colonne.

* **Fonctions de masquage** : ensemble de méthodes qui contrôlent l'exposition des données dans différents scénarios.

| Fonction de masquage | Logique de masquage |
|----------|---------------|
| **Par défaut** |**Masquage complet selon le type de données des champs désignés**<br/><br/>• Utilisez XXXX ou moins de X si la taille du champ est inférieure à 4 caractères pour les données de type chaîne (nchar, ntext, nvarchar).<br/>• Utilisez la valeur zéro pour les types de données numériques (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Utilisez 01-01-1900 pour les données de type date/heure (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Pour les variantes SQL, la valeur par défaut du type en cours est utilisée.<br/>• Pour XML, le document <masked/> est utilisé.<br/>• Utilisez une valeur vide pour les types de données spéciaux (table timestamp, hierarchyid, GUID, binary, image, varbinary spatial).
| **Carte de crédit** |**Méthode de masquage qui affiche les quatre derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d'une carte de crédit.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Numéro de sécurité sociale** |**Méthode de masquage qui affiche les quatre derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d'un numéro de sécurité sociale américain.<br/><br/>XXX-XX-1234 |
| **E-mail** | **Méthode de masquage qui affiche la première lettre et remplace le domaine par XXX.com** à l'aide d'une chaîne constante comme préfixe sous la forme d'une adresse de messagerie.<br/><br/>aXX@XXXX.com |
| **Nombre aléatoire** | **Méthode de masquage qui génère un nombre aléatoire** selon les limites sélectionnées et les types de données réelles. Si les limites désignées sont égales, la fonction de masquage sera un nombre constant.<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Texte personnalisé** | **Méthode de masquage qui affiche le premier et le dernier caractères** et ajoute une chaîne de remplissage personnalisée entre les deux. Si la chaîne d'origine est plus courte que le préfixe et le suffixe affichés, seule la chaîne de remplissage sera utilisée.<br/>préfixe[remplissage]suffixe<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## Configuration du masquage des données dynamiques pour votre base de données à l’aide du portail Azure Classic

1. Lancez le portail Azure Classic à l’adresse [https://manage.windowsazure.com/](https://manage.windowsazure.com).

2. Cliquez sur la base de données à masquer, puis sur l’onglet **AUDIT ET SÉCURITÉ**.

3. Dans **masquage des données dynamiques**, cliquez sur **ACTIVÉ** pour activer la fonctionnalité de masquage des données dynamiques.

4. Tapez les utilisateurs SQL ou les identités AAD à exclure du masquage et qui doivent avoir accès aux données sensibles non masquées. La liste d'utilisateurs doit être délimitée par des points-virgules. Les utilisateurs disposant de privilèges administrateur ont toujours accès aux données d'origine non masquées.

	>[AZURE.TIP] Pour que la couche d’application puisse afficher des données sensibles aux utilisateurs d’application privilégiés, ajoutez l’utilisateur SQL ou l’identité AAD qu’utilise l’application pour interroger la base de données. Il est vivement recommandé de limiter le nombre d’utilisateurs privilégiés dans cette liste afin de limiter l’exposition des données sensibles.

	![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. En bas de la page, dans la barre de menus, cliquez sur **Ajouter un MASQUE** pour ouvrir la fenêtre de configuration des règles de masquage.

6. Entrez le nom du **Schéma**, de la **Table** et de la **Colonne** dans les listes déroulantes pour définir les champs désignés qui seront masqués.

7. Choisissez une **FONCTION DE MASQUAGE** dans la liste des catégories de masquage des données sensibles.

	![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Cliquez sur **OK** dans la fenêtre des règles de masquage des données pour mettre à jour l’ensemble des règles de la stratégie de masquage des données dynamiques.

9. Cliquez sur **ENREGISTRER** pour enregistrer la stratégie de masquage, nouvelle ou mise à jour.


## Configuration du masquage des données dynamiques pour votre base de données à l’aide d’instructions Transact-SQL

Consultez [Masquage des données dynamiques](https://msdn.microsoft.com/library/mt130841.aspx).

## Configuration du masquage des données dynamiques pour votre base de données à l’aide des cmdlets Powershell

Voir [Cmdlets de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## Configuration du masquage des données dynamiques pour votre base de données à l’aide de l’API REST

Voir [Opérations pour les bases de données SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=AcomDC_0406_2016-->