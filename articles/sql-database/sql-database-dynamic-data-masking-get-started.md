<properties 
   pageTitle="Prise en main du masquage des données dynamiques de base de données SQL (portail Azure en version préliminaire)" 
   description="Comment prendre en main le masquage de données dynamiques de base de données SQL dans le portail Azure en version préliminaire" 
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
   ms.date="04/02/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Prise en main du masquage des données dynamiques de base de données SQL (portail Azure en version préliminaire)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Vue d'ensemble

Le masquage des données dynamiques de base de données SQL limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. Le masquage des données dynamiques est en version préliminaire pour les niveaux de service de Base, Standard et Premium dans la version V12 de la base de données SQL Azure.

Le masquage des données dynamiques empêche tout accès non autorisé aux données sensibles en permettant aux clients d’indiquer la quantité de données sensibles à faire apparaître, avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées.

Par exemple, une personne chargée du soutien technique au centre d’appel peut identifier les appelants par plusieurs chiffres de leur numéro de sécurité sociale ou de carte de crédit, mais ces éléments de données ne doivent pas être divulgués entièrement à la personne du support technique. Un développeur peut définir une règle de masquage à appliquer à chaque résultat de requête, qui ne fait apparaître que les quatre derniers chiffres du numéro de sécurité sociale ou de carte de crédit dans l’ensemble de résultats. En utilisant le masque de données approprié pour protéger les informations d’identification personnelle (PII), un développeur peut interroger les environnements de production à des fins de dépannage, sans aller à l’encontre des réglementations de conformité.

## Principes fondamentaux du masquage de données dynamiques de base de données SQL

La stratégie de masquage des données dynamiques est définie dans le portail Azure en version préliminaire et l’installation est effectuée à l’aide de la chaîne de connexion sécurisée utilisée par l’application ou d’autres clients qui accèdent à la base de données.

### Autorisations du masquage des données dynamiques

Le masquage des données dynamiques peut être configuré par l’administrateur de base de données Azure, l’administrateur de serveur ou le responsable de la sécurité.

### Stratégie de masquage des données dynamiques

* **Connexions privilégiées** : un ensemble de connexions qui obtiendra les données masquées dans les résultats de requêtes SQL.
  
* **Règles de masquage** : un ensemble de règles qui définissent les champs désignés qui seront masqués et la fonction de masquage qui sera utilisée. Les champs désignés peuvent être définis à l’aide d’un nom de table de base de données et d’un nom de colonne ou avec un nom d’alias.

* **Masquage par** : peut être effectué à la source ou à la destination. Le masquage peut être configuré au niveau de la source en identifiant le nom de la **Table** et le nom de la **Colonne** ou au niveau des résultats en identifiant l’**Alias** utilisé dans la requête. Si vous connaissez l’architecture de données de votre base de données et que vous souhaitez limiter l’exposition de tous les résultats de requête, une règle de masquage à la source peut être plus appropriée. Vous pouvez ajouter une règle de masquage des résultats si vous souhaitez limiter l’exposition des résultats de la requête sans analyser l’architecture de données de la base de données ou pour un champ provenant de sources différentes.
  
* **Restriction étendue** : limite l’exposition des données sensibles, mais peut avoir une incidence négative sur les fonctionnalités de certaines applications.

>[AZURE.TIP]Utilisez l’option **Restriction étendue** pour limiter l’accès aux développeurs qui accèdent directement à la base de données et exécutent des requêtes SQL pour résoudre des problèmes.

* **Fonctions de masquage** : un ensemble de méthodes qui contrôle l’exposition des données dans différents scénarios.

| Fonction de masquage | Logique de masquage |
|----------|---------------|
| **Par défaut** |**Masquage complet selon les types de données des champs désignés**<br/><br/>• Utilisez XXXXXXXX ou moins de X si la taille du champ est inférieure à 8 caractères pour les données de type chaîne (nchar, ntext, nvarchar).<br/>• Utilisez la valeur zéro pour les types de données numériques (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Utilisez l’heure actuelle pour les types de données date/heure (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Pour les variantes SQL, la valeur par défaut du type en cours est utilisé.<br/>• Pour XML le document <masked/> est utilisé.<br/>• Utilisez une valeur vide pour les types de données spéciaux (table timestamp, hierarchyid, GUID, binary, image, varbinary spatial).
| **Carte de crédit** |**Méthode de masquage qui affiche les quatre derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d’une carte de crédit.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Numéro de sécurité sociale** |**Méthode de masquage qui affiche les deux derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d’un numéro de sécurité sociale américain. <br/><br/>XXX-XX-XX12 |
| **E-mail** | **Méthode de masquage qui affiche la première lettre et le domaine** à l’aide d’une chaîne constante comme préfixe sous la forme d’une adresse de messagerie.<br/><br/>aXX@XXXX.com |
| **Nombre aléatoire** | **Méthode de masquage qui génère un nombre aléatoire** selon les limites sélectionnées et les types de données réelles. Si les limites désignées sont égales, la fonction de masquage sera un nombre constant.<br/><br/>![Volet de navigation][Image1] |
| **Texte personnalisé** | **Méthode de masquage qui affiche la première et la dernière lettre** et ajoute une chaîne de remplissage personnalisée entre les deux.<br/>préfixe[remplissage]suffixe<br/><br/>![Volet de navigation][Image2] |

  
<a name="Anchor1"></a>
### Chaîne de connexion sécurisée

Lorsque vous configurez le masquage des données dynamiques, Azure vous fournit une chaîne de connexion sécurisée pour la base de données. Seules les données sensibles des clients qui utilisent cette chaîne de connexion sont masquées, conformément à la stratégie de masquage des données dynamiques. Vous devez également mettre à jour les clients existants (exemple : les applications) pour utiliser le nouveau format de chaîne de connexion.

* Format de la chaîne de connexion d’origine : <*server name*>.database.windows.net
* Chaîne de connexion sécurisée : <*server name*>.database.**secure**.windows.net

Vous pouvez également modifier le paramètre **ACCÈS SÉCURISÉ** et le faire passer de **FACULTATIF** à **OBLIGATOIRE**, ce qui garantit qu’il n’existe aucune possibilité d’accéder à la base de données avec la chaîne de connexion d’origine et d’ignorer la stratégie de masquage des données dynamiques. Lorsque vous testez le masquage des données dynamiques à l’aide de clients spécifiques (par exemple, une application de phase de développement ou SSMS), choisissez **FACULTATIF**. Pour la production, choisissez **OBLIGATOIRE**.<br/><br/>

![Volet de navigation][Image3]<br/><br/>

## Configuration du masquage des données dynamiques pour votre base de données à l’aide du portail Azure en version préliminaire

1. Lancez la version préliminaire du portail Azure via l’adresse [https://portal.azure.com](https://portal.azure.com).
	 
2. Accédez au volet de configuration de la base de données comprenant les données sensibles que vous souhaitez masquer.
	
3. Cliquez sur la vignette **masquage des données dynamiques** qui lance le panneau de configuration **masquage des données dynamiques**.

	* Vous pouvez également faire défiler vers le bas vers la section **Opérations** et cliquer sur **masquage de données dynamiques**.
	 
	![Volet de navigation][Image4]<br/><br/>

4. Dans le panneau de configuration **masquage de données dynamiques**, cliquez sur **ACTIVÉ** pour activer la fonctionnalité de masquage des données dynamiques.

	![Volet de navigation][Image5]<br/><br/>

5. Tapez les noms d’accès privilégiés qui doivent avoir accès aux données sensibles non masquées.
 
	![Volet de navigation][Image6]

	>[AZURE.TIP]Pour que la couche d'application puisse afficher des données sensibles aux utilisateurs d'application privilégiés, ajoutez le nom de connexion SQL de l'application utilisée pour les requêtes et de la base de données. Il est vivement recommandé de limiter le nombre de connexions incluses dans cette liste afin de limiter l’exposition des données sensibles.

6. Cliquez sur **Ajouter un masque** pour ouvrir le panneau de configuration **Ajouter une règle de masquage**.
	
7. Choisissez **Masquage par** pour indiquer si le masquage aura lieu à la source ou à la destination. Le masquage peut être configuré au niveau de la source en identifiant le nom de la **Table** et le nom de la **Colonne** ou au niveau des résultats en identifiant l’**Alias** utilisé dans la requête. Notez que le nom de la **Table** fait référence à tous les schémas dans la base de données et ne doit pas inclure un préfixe de schéma. Si vous connaissez l’architecture de données de votre base de données et que vous souhaitez limiter l’exposition de tous les résultats de requête, une règle de masquage à la source peut être plus appropriée. Vous pouvez ajouter une règle de masquage des résultats si vous souhaitez limiter l’exposition des résultats de la requête sans analyser l’architecture de données de la base de données ou pour un champ provenant de sources différentes.

8. Entrez le nom de la **Table** et de la **Colonne** ou le nom d’**Alias** pour définir les champs désignés qui seront masqués.

9. Choisissez un **Format de champ de masquage** dans la liste des catégories de masquage des données sensibles.

	![Volet de navigation][Image7]<br/><br/>

10. Cliquez sur **Enregistrer** dans le panneau de règles de masquage pour mettre à jour l’ensemble des règles de la stratégie de masquage dynamique.

11. Vous pouvez sélectionner **UTILISER LES RESTRICTIONS ÉTENDUES** afin de limiter l’exposition des données sensibles via des requêtes ad hoc.

12. Cliquez sur **Enregistrer** dans le panneau de configuration du masquage des données afin d’enregistrer la règle de masquage, nouvelle ou mise à jour.

13. Sous **Accès sécurisé**, cliquez sur **FACULTATIF** ou **OBLIGATOIRE**. Pour plus d’informations, consultez la page [Chaîne de connexion sécurisée](#Anchor1).

	![Volet de navigation][Image8]
	
## Configuration du masquage des données dynamiques pour votre base de données à l’aide de l’API REST

Consultez la page [Opérations pour les bases de données SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).

[Image1]: ./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png
[Image2]: ./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png
[Image3]: ./media/sql-database-dynamic-data-masking-get-started/3_DDM_Current_Preview.png
[Image4]: ./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png
[Image5]: ./media/sql-database-dynamic-data-masking-get-started/5_DMM_Policy_Tile.png
[Image6]: ./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png
[Image7]: ./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png
[Image8]: ./media/sql-database-dynamic-data-masking-get-started/8_DDM_Security_Enabled_Access.png
 

<!---HONumber=July15_HO4-->