<properties 
   pageTitle="Règles BizTalk" 
   description="Cette rubrique couvre les fonctionnalités des règles BizTalk et fournit des instructions sur leur utilisation." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="andalmia"/>

# Règles BizTalk 

Les règles d'entreprise incarnent les stratégies et décisions qui contrôlent les processus d'entreprise. Ces stratégies peuvent être définies formellement dans des manuels de procédures, des contrats ou des accords ; elles peuvent aussi s'exprimer dans le savoir ou le savoir-faire mis en œuvre par les employés. Ces stratégies sont dynamiques et évoluent dans le temps en raison des modifications apportées aux projets de l'entreprise, à la réglementation, etc.

L'implémentation de ces stratégies dans des langages de programmation traditionnels nécessite un temps et une coordination considérables, et ne permet pas aux non-programmeurs de participer à la création et à la maintenance des stratégies d'entreprise. Les règles d'entreprise BizTalk offrent un moyen d'implémenter rapidement ces stratégies indépendamment du reste des processus d'entreprise. Elles permettent d'apporter les modifications requises aux stratégies d'entreprise sans affecter le reste des processus d'entreprise.

## Pourquoi des règles ?

Il existe 3 raisons principales d'utiliser des règles d'entreprise BizTalk dans un processus d'entreprise :

* Séparer la logique métier du code d'application
- Permettre aux analystes d'entreprise de mieux contrôler la gestion de la logique métier
+ Accélérer la mise en production de la logique métier

# Concepts des règles

## Vocabulaire

Les termes utilisés pour définir les conditions et actions des règles sont généralement exprimés par domaine ou selon une nomenclature propre à l'activité. Par exemple, un utilisateur de messagerie électronique rédige des règles en termes de messages reçus de contacts particuliers ou de messages reçus après une date particulière, tandis qu'un analyste d'une entreprise d'assurance en détermine en termes de facteurs de risque et de montant de la couverture.
Derrière cette terminologie propre à l'activité se trouvent des artefacts technologiques (objets, tables de base de données et documents XML) qui implémentent les conditions et actions des règles. Le vocabulaire est conçu pour rapprocher la sémantique d'entreprise de l'implémentation.

Par exemple, une liaison de données pour un état d'approbation peut pointer vers une colonne particulière d'une ligne particulière d'une base de données particulière, et être représentée sous la forme d'une requête SQL. Au lieu d'insérer ce type de représentation complexe dans une règle, vous pouvez créer une définition de vocabulaire, associée à cette liaison de données, avec un nom convivial comme " État ". Par la suite, vous pouvez inclure " État " dans des règles pour que le moteur de règles puisse récupérer les données correspondantes dans la table.
Un _vocabulaire_ est une collection de définitions constituée de noms conviviaux pour les objets informatiques utilisés dans les conditions et actions des règles. Les définitions de vocabulaire rendent les règles plus faciles à lire, comprendre et partager par les protagonistes d'un domaine d'activité particulier.

## Règle

Les règles d'entreprise sont des instructions déclaratives qui régissent le fonctionnement des processus d'entreprise. Une règle se compose d'une condition et d'actions. La condition est évaluée, et si elle prend la valeur true, le moteur de règles lance une ou plusieurs actions.
Dans l'infrastructure des règles d'entreprise, les règles sont définies selon le format suivant :

_IF_ _condition_ _THEN_ _action_

Examinez l'exemple suivant :

*IF montant inférieur ou égal aux fonds disponibles*  
*THEN effectuer la transaction et imprimer un reçu*

Cette règle détermine si une transaction est réalisée en appliquant une logique métier, sous la forme d'une comparaison de deux valeurs monétaires, sous la forme d'un montant de transaction et de fonds disponibles.
Vous pouvez utiliser la règle d'entreprise pour créer, modifier et déployer des règles d'entreprise. Vous pouvez également effectuer les tâches précédentes par programmation.

### Conditions

Une condition est une expression (booléenne) vrai/faux qui se compose d'un ou plusieurs prédicats.
Dans notre exemple, le prédicat inférieur ou égal est appliquée au montant et aux fonds disponibles. Cette condition prend toujours la valeur true ou false.
Les prédicats peuvent être combinés avec des opérateurs logiques AND, OR et NOT pour former une expression logique potentiellement très longue, mais qui prend toujours la valeur true ou false.

### Actions

Les actions sont les conséquences fonctionnelles de l'évaluation de la condition. Si la condition d'une règle est remplie, une ou plusieurs actions correspondantes sont lancées.
Dans notre exemple, " réaliser la transaction " et " imprimer un reçu " sont des actions effectuées quand, et seulement quand, la condition (ici " SI le montant est inférieur ou égal aux fonds disponibles ") a la valeur true.
Les actions sont représentées dans l'infrastructure des règles d'entreprise en effectuant des opérations définies sur des documents XML.

## Stratégie

Une stratégie est un regroupement logique de règles. Vous rédigez une stratégie, l'enregistrez, la testez et, quand vous êtes satisfait des résultats, vous l'utilisez dans un environnement de production.

### Composition d'une stratégie

Vous pouvez créer des stratégies dans le portail des règles. Une stratégie peut contenir un ensemble arbitrairement grand de règles, mais en général, vous rédigez une stratégie à partir de règles qui se rapportent à un domaine d'activité spécifique dans le contexte de l'application qui utilisera la stratégie.

### Test de la stratégie
Vous pouvez effectivement exécuter une série de tests de votre stratégie avant de l'utiliser dans un environnement de production. Le portail des règles vous permet de fournir des entrées à une stratégie, d'exécuter la stratégie et d'afficher sa sortie. La sortie inclut des journaux, l'exécution de la règle, l'évaluation de la condition et les sorties qui en résultent.

## Exemple de scénario : déclaration de sinistre
Prenons un exemple de scénario et déroulons-le au fur et à mesure que nous rédigeons la logique métier qui s'y rapporte.

![Alt text][1]

Dans un scénario de déclaration de sinistre vraiment simple, le requérant envoie sa déclaration (via n'importe quel client comme un site web, une application téléphonique, etc.). Cette demande d'indemnisation est alors envoyée à l'unité de traitement des sinistres de l'entreprise et en fonction du résultat du traitement, l'indemnisation peut être approuvée, rejetée ou transférée à des fins de traitement manuel supplémentaire.
L'unité de traitement des sinistres dans notre scénario est celle qui englobe la logique métier du système. En examinant de plus près cette unité, voici ce que nous observons :

![Alt text][2]
 
Utilisons à présent des règles d'entreprise pour implémenter cette logique métier.


## Création d'une application API de règles


1. Connectez-vous au portail Azure et accédez à la page d'accueil. 
1. Cliquez sur Nouveau -> Azure Marketplace -> API Apps - > Règles BizTalk -> Créer.
![Alt text][3]
1. Dans le nouveau panneau qui s'ouvre, entrez les informations suivantes :  
	1. Nom : donnez un nom à votre application API de règles.
	1. Plan d'hébergement de l'application : sélectionnez ou créez un plan d'hébergement web.
	1. Niveau de tarification : choisissez le niveau de tarification dans lequel vous voulez que cette application se trouve.
	1. Groupe de ressources : sélectionnez ou créez le groupe de ressources dans lequel l'application doit se trouver.
	1. Emplacement : choisissez l'emplacement géographique dans lequel vous voulez déployer l'application.
4.	Cliquez sur Créer. Au bout de quelques minutes, votre application API de règles BizTalk est créée.

## Création du vocabulaire
Après avoir créé une application API de règles BizTalk, l'étape suivante consiste à créer le vocabulaire. Le développeur est la personne la plus à même d'effectuer cet exercice. Pour cela, procédez comme suit : 


1. Accédez à l'application API créée en sélectionnant Parcourir -> API Apps -> <Votre application API de règles>. Vous devez accéder à un tableau de bord de l'application API de règles similaire à celui-ci :
 
   ![Alt text][4]

2. Ensuite, cliquez sur " Définitions de vocabulaire ". L'écran de création de vocabulaire s'affiche. Cliquez sur " Ajouter " pour commencer à ajouter de nouvelles définitions de vocabulaire.
Actuellement, deux types de définitions de vocabulaire sont pris en charge : littéral et XML.

## Définition littérale
1.	Après avoir cliqué sur " Ajouter ", un nouveau panneau " Ajouter une définition " s'ouvre. Entrez les valeurs suivantes.
  1.	Nom : seuls les caractères alphanumériques sont acceptés sans caractères spéciaux. Ce nom doit également être unique dans votre liste de définitions de vocabulaire existante.
  2.	Description : ce champ est facultatif.
  3.	Type : 2 types sont pris en charge. Choisissez Littéral dans cet exemple.
  4.	Type d'entrée : permet aux utilisateurs de sélectionner le type de données de la définition. Actuellement, quatre types de données peuvent être sélectionnés :
    i.	Chaine : ces valeurs doivent être entrées entre des guillemets doubles ("Exemple de chaîne").  
    ii.	Booléen : la valeur peut être true ou false.  
    iii.	Numérique : tout nombre décimal.  
    iv.	DateHeure : cela signifie que la définition est de type date. Les données doivent être entrées dans ce format : jj/mm/aaaa hh:mm:ss  
    v.	Entrée : c'est ici que vous entrez la valeur de votre définition. Les valeurs entrées ici doivent être conformes au type de données choisi. L'utilisateur peut entrer une valeur unique, un ensemble de valeurs séparées par des virgules ou une plage de valeurs à l'aide d'un mot clé. Par exemple, l'utilisateur peut entrer la valeur unique 1, un ensemble 1, 2, 3 ou une plage de 1 à 5. Notez que cette plage est uniquement prise en charge pour le type numérique.

![Alt text][5]
## Définition XML
Si le type de vocabulaire choisi est XML, les entrées suivantes doivent être spécifiées :  
  a.	Schéma : cliquez sur cette entrée pour ouvrir un nouveau panneau qui permet à l'utilisateur de choisir parmi une liste de schémas déjà téléchargés ou d'en télécharger un nouveau.   
  b.	XPATH : cette entrée est déverrouillée uniquement après avoir choisi un schéma à l'étape précédente. En cliquant sur cette entrée, le schéma qui a été sélectionné s'affiche, ce qui permet à l'utilisateur de sélectionner le nœud pour lequel une définition de vocabulaire doit être créée.  
  c.	FACT : cette entrée identifie l'objet de données transmis au moteur de règles à des fins de traitement. Il s'agit d'une propriété avancée, dont la valeur par défaut est celle du parent de l'expression XPATH sélectionnée. FACT s'avère particulièrement important pour les scénarios de chaînage et de collection.

![Alt text][6]

### Ajouter en bloc
Les étapes ci-dessus ont décrit l'expérience de création des définitions de vocabulaire. Une fois créées, les définitions de vocabulaire apparaissent sous forme de liste. Dans certaines conditions, il est possible de générer plusieurs définitions à partir du même schéma au lieu de répéter les étapes ci-dessus à chaque fois. C'est là que la fonctionnalité Ajouter en bloc s'avère très utile. 
En cliquant sur " Ajouter en bloc ", un nouveau panneau s'ouvre. La première étape consiste à sélectionner le schéma pour lequel plusieurs définitions doivent être créées. Le fait de cliquer sur le schéma permet d'ouvrir un nouveau panneau qui permet à l'utilisateur de choisir parmi une liste de schémas déjà téléchargés ou d'en télécharger un nouveau.
À présent, la propriété XPATHS est déverrouillée. Le fait de cliquer dessus ouvre la visionneuse de schéma où plusieurs nœuds peuvent être sélectionnés.
Les noms des multiples définitions créées prennent par défaut celui du nœud sélectionné. Il est toujours possible de les modifier après la création.

![Alt text][7]

## Création d'une stratégie
Une fois que le développeur a créé le vocabulaire requis, il est d'usage que ce soit l'analyste d'entreprise qui crée des stratégies d'entreprise via le portail Azure.  
	1.	Dans l'application Règles créée figure un filtre Stratégie sur lequel l'utilisateur clique pour accéder à la page de création de la stratégie.  
	2.	Cette page présente la liste des stratégies que possède cette application Règles. L'utilisateur peut ajouter une nouvelle stratégie tout simplement en tapant un nom de stratégie et en appuyant sur la touche Tab. Plusieurs stratégies peuvent résider dans une seule application API de règles.  
	3.	Le fait de cliquer sur la stratégie créée redirige l'utilisateur vers la page des détails de la stratégie où chacun peut consulter les règles incluses dans la stratégie.  
	![Alt text][8]
	4.	Cliquez sur " Ajouter nouveau " pour ajouter une nouvelle règle. Un nouveau panneau s'ouvre.

## Création de règles
Une règle est la collection d'instructions de condition et d'action. Les actions sont exécutées si la condition prend la valeur true. Dans le panneau Créer une règle, donnez un nom unique à la règle (pour cette stratégie) et une description (facultative).
La zone Condition peut servir à créer des instructions conditionnelles complexes. Voici les mots clés pris en charge :  
1. 	And : opérateur conditionnel  
2. 	Or : opérateur conditionnel  
3. 	does\_not\_exist  
4. 	exists  
5. 	false  
6. 	is\_equal\_to  
7. 	is\_greater\_than  
8. 	is\_greater\_than\_equal\_to  
9. 	is\_in  
10. is\_less\_than  
11. is\_less\_than\_equal\_to  
12. is\_not\_in  
13. is\_not\_equal\_to  
14. mod  
15. true  

La zone Action(Alors) peut contenir plusieurs instructions, une par ligne, pour créer des actions à exécuter. Voici les mots clés pris en charge :  
1.	equals  
2.	false  
3.	true  
4.	halt  
5.	mod  
6.	null  
7.	update  

Les zones de condition et d'action proposent Intellisense pour aider les utilisateurs à créer rapidement une règle. Pour le déclencher, il suffit d'appuyer sur Ctrl+Espace ou de commencer à taper. Les mots clés correspondant aux caractères tapés sont automatiquement filtrés et affichés. La fenêtre Intellisense affiche tous les mots clés et toutes les définitions de vocabulaire.
![Alt text][9]

## Chaînage avant explicite
Les règles BizTalk prennent en charge le chaînage avant explicite. Cela signifie que, si les utilisateurs veulent réévaluer des règles en réponse à certaines actions, ils peuvent le déclencher à l'aide de certains mots clés. Voici les mots clés pris en charge :  
   1.	update <définition de vocabulaire> : ce mot clé réévalue toutes les règles qui utilisent la définition de vocabulaire spécifiée dans leur condition.  
   2.	Halt : ce mot clé arrête toutes les exécutions de règle.

## Activer/désactiver les règles
Chaque règle de la stratégie peut être activée ou désactivée. Par défaut, toutes les règles sont activées. Les règles désactivées ne sont pas exécutées pendant l'exécution de la stratégie. L'activation/désactivation des règles peuvent être effectuées à partir du panneau de la règle directement : les commandes sont disponibles dans la barre de commandes en haut du panneau ou à partir de la stratégie, du menu contextuel (clic droit sur une règle) qui propose l'option d'activation/désactivation.

## Priorité des règles
Toutes les règles d'une stratégie sont exécutées dans l'ordre. La priorité d'exécution est déterminée par l'ordre dans lequel elles apparaissent dans la stratégie. Pour modifier cette priorité, il suffit tout simplement de faire glisser-déplacer la règle. 

## Tester la stratégie
Après avoir créé votre stratégie, avant de l'utiliser en production, il est possible de la tester. À l'aide de la commande " Tester la stratégie ", les utilisateurs peuvent accéder au panneau éponyme. Dans ce panneau, vous pouvez voir la liste des définitions de vocabulaire utilisées dans la stratégie qui requièrent une entrée utilisateur. Les utilisateurs peuvent ajouter manuellement des valeurs pour ces entrées dans le cadre de leur scénario de test. Ils peuvent également choisir d'importer des XML de test pour les entrées. Une fois toutes les entrées renseignées, le test peut être exécuté et les sorties pour chaque définition de vocabulaire s'affichent dans la colonne de sortie pour en faciliter la comparaison. Pour consulter les journaux conviviaux de l'analyste d'entreprise, cliquez sur " Afficher les journaux " pour afficher les journaux d'exécution. Pour enregistrer les journaux, l'option " Enregistrer la sortie " est disponible pour stocker toutes les données de test à des fins d'analyse indépendante.

## Utilisation de règles dans des applications logiques
Une fois que la stratégie a été créée et testée, elle est prête à être consommée. Les utilisateurs peuvent créer une application logique via Nouveau -> Application logique. Dans le concepteur, les règles BizTalk sont  disponibles dans la galerie à droite. Elles peuvent maintenant être déplacées vers l'aire du concepteur. Après quoi, il est possible de choisir l'application API de règles (action) à cibler. Les actions incluent la liste des stratégies à exécuter. Choisissez une stratégie spécifique d'après laquelle les entrées requises pour la stratégie doivent être renseignées. Les utilisateurs peuvent utiliser la sortie de l'application API de règles en aval à des fins de prise de décision supplémentaire.

## Utilisation de règles via des API
L'application API de règles peut également être appelée à l'aide d'un ensemble complet d'API disponibles. Ainsi, les utilisateurs ne sont pas limités à la simple utilisation de flux et peuvent utiliser des règles dans n'importe quelle application en effectuant des appels REST. Les API REST exactes disponibles sont consultables en cliquant sur le filtre " Définition API " dans le tableau de bord des règles.
![Alt text][10]

## Modification de vocabulaire et de stratégie
Un des principaux avantages à utiliser des règles d'entreprise est que les modifications apportées à la logique métier peuvent être transmises à la production beaucoup plus rapidement. Toute modification apportée au vocabulaire et aux stratégies est immédiatement appliquée en production. L'utilisateur a simplement besoin d'accéder à la définition de vocabulaire ou stratégie respective et d'apporter les modifications pour que celles-ci prennent effet.

<!--Image references-->
[1]: ./media/app-service-logic-biztalk-rules/InsuranceScenario.PNG	
[2]: ./media/app-service-logic-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-biztalk-rules/APIDef.PNG


<!--HONumber=49-->