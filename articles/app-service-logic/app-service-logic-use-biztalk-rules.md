<properties
   pageTitle="Règles BizTalk"
   description="Cette rubrique traite des fonctionnalités de règles BizTalk et fournit des instructions sur leur utilisation"
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
   ms.date="07/02/2015"
   ms.author="andalmia"/>

#Règles BizTalk

Les règles d'entreprise incarnent les stratégies et décisions qui contrôlent les processus d'entreprise. Ces stratégies peuvent être définies formellement dans des manuels de procédure, des contrats ou des accords, ou elles peuvent exister en tant que connaissances ou expertise des collaborateurs. Ces stratégies sont dynamiques et sujettes à modification dans le temps, en raison des changements apportés aux plans d'activités, aux réglementations ou autres raisons.

L'implémentation de ces stratégies dans des langages de programmation traditionnels nécessite un temps et une coordination considérables et ne permet pas aux non-programmeurs de participer à la création et à la maintenance des stratégies d'entreprise. Les règles d’entreprise BizTalk permettent d’implémenter rapidement ces stratégies et de séparer le reste du processus d’entreprise. Cela permet d'apporter des modifications nécessaires aux stratégies d'entreprise sans affecter le reste du processus d'entreprise.

##Pourquoi des règles ?

Il existe trois principales raisons d'utiliser des règles d'entreprise BizTalk dans un processus d'entreprise :

* séparer la logique métier à partir du code d'application ;
- permettre aux analystes d'entreprise de mieux contrôler la gestion de la logique métier ;
+ accélérer la mise en production de la logique métier.

##Concepts de règles

##Vocabulaire

Les termes utilisés pour définir les actions et les conditions des règles sont généralement exprimés par une nomenclature spécifique au domaine ou à l'industrie. Par exemple, un utilisateur de messagerie écrit des règles en termes de messages « reçus de » et « reçus après », tandis qu'un analyste du secteur des assurances écrit des règles en termes de « facteurs de risque » et de « montant de couverture ». Sous-jacents à cette terminologie spécifique à un domaine, il existe des artefacts technologiques (objets, tables de base de données et documents XML) qui implémentent les conditions et actions des règles. Les vocabulaires ont pour but de combler le fossé entre la sémantique d'entreprise et l'implémentation.

Par exemple, une liaison de données pour un état d'approbation peut pointer vers une certaine colonne donnée sur une certaine ligne d'une base de données, représentée sous la forme d'une requête SQL. Au lieu d'insérer cette représentation complexe dans une règle, vous pouvez créer une définition de vocabulaire associée à cette liaison de données avec le nom convivial « État ». Par la suite, vous pouvez inclure « État » dans les règles de votre choix et le moteur de règles peut extraire les données correspondantes de la table. Un _vocabulaire_ est une collection de définitions composée de noms conviviaux pour les objets informatiques utilisés dans les actions et les conditions de règles. Les définitions de vocabulaire simplifient la compréhension, la lecture et le partage de ces règles par des personnes dans un domaine d'entreprise particulier.

##Règle

Les règles d'entreprise sont des instructions déclaratives qui régissent le fonctionnement des processus d'entreprise. Une règle se compose d'une condition et d'actions. La condition est évaluée et si sa valeur est vraie, le moteur de règles initie une ou plusieurs actions. Les règles de l'Infrastructure de règles d'entreprise sont définies au format suivant :

_IF_ _condition_ _THEN_ _action_

Considérez l'exemple suivant :

*SI le montant est inférieur ou égal aux fonds disponibles* *THEN Exécuter la transaction et imprimer un reçu*

Cette règle détermine si une transaction sera réalisée en appliquant la logique métier, sous la forme d'une comparaison de deux valeurs monétaires, sous la forme du montant d'une transaction et de fonds disponibles. Vous pouvez utiliser la Règle d'entreprise pour créer, modifier et déployer des règles d'entreprise. Vous pouvez également effectuer les tâches précédentes par programmation.

###Conditions

Une condition est une expression vrai/faux (booléenne) qui se compose d'un ou plusieurs prédicats. Dans notre exemple, le prédicat « inférieur ou égal à » est appliqué à la quantité et aux fonds disponibles. Cette condition sera toujours évaluée comme vraie ou fausse. Vous pouvez combiner les prédicats avec les opérateurs logiques AND, OR et NOT pour former une expression logique potentiellement longue, mais ils sont toujours évalués comme vrais ou faux.

###Actions

Les actions sont les conséquences fonctionnelles de l'évaluation d'une condition. Si une condition de règle est remplie, une ou plusieurs actions correspondantes sont initiées. Dans notre exemple, « effectuer la transaction » et « imprimer un reçu » sont les actions exécutées quand, et uniquement quand, la condition (dans ce cas, « IF montant inférieur ou égal aux fonds disponibles ») est vraie. Les actions sont représentées dans l'Infrastructure de règles d'entreprise par des opérations exécutées sur des documents XML.

##Stratégie

Une stratégie est un regroupement logique de règles. Vous composez une stratégie, l'enregistrez, la testez et, quand vous êtes satisfait des résultats, vous l'utilisez dans un environnement de production.

###Composition d'une stratégie

Vous pouvez créer des stratégies dans le portail Règles. Une stratégie peut contenir un ensemble arbitrairement grand de règles, mais en général vous créez une stratégie à partir de règles qui se rapportent à un domaine d'entreprise spécifique dans le contexte de l'application qui utilisera la stratégie.

###Test de stratégie
Vous pouvez exécuter une série de tests sur votre stratégie avant de l'utiliser dans un environnement de production. Le portail Règles vous permet de fournir des entrées pour une stratégie, de l'exécuter et d'afficher sa sortie. La sortie comprend des journaux, l'exécution de la règle, l'évaluation des conditions et les sorties qui en résulte.

##Exemple de scénario : demandes d'indemnisation
Prenons un exemple de scénario et étudions-le à mesure que nous composons la logique métier.

![Alt text][1]

Dans un scénario d'indemnisation vraiment simple, le demandeur envoie sa demande d'indemnisation (via n'importe quel client tel qu'un site web, une application de téléphone, etc.). Cette demande d'indemnisation est envoyée au Service de traitement des indemnisations de la compagnie d'assurance et, en fonction du résultat du traitement, elle peut être approuvée, rejetée ou transférée en vue d'un traitement manuel plus approfondi. Le Service de traitement des indemnisations dans notre scénario serait celui englobant la logique métier pour le système. Si nous examinons de plus près ce service, nous pouvons observer ce qui suit :

![Alt text][2]

Utilisons maintenant des règles d'entreprise pour implémenter cette logique métier.


##Création d'une application API de règles


1. Connectez-vous au portail Azure et accédez à la page d'accueil.
1. Cliquez sur Nouveau -> Azure Marketplace -> API Apps - > Règles BizTalk -> Créer ![Alt text][3]
1. Dans le nouveau panneau qui s'ouvre, entrez les informations suivantes :  
	1. Nom : donnez un nom à votre application API de règles.
	1. Plan d'hébergement d'application : sélectionnez ou créez un plan d'hébergement web.
	1. Niveau de tarification : choisissez le niveau de tarification où doit résider cette application.
	1. Groupe de ressources : sélectionnez ou créez le groupe de ressources où doit résider cette application.
	1. Emplacement : choisissez l'emplacement géographique où vous souhaitez déployer l'application.
4.	Cliquez sur Créer. Votre application API de règles BizTalk est créée au bout de quelques minutes.

##Création des vocabulaires
Une fois que vous avez créé une application API de règles BizTalk, l'étape suivante consiste à créer des vocabulaires. Le développeur est en principe la personne la plus susceptible d'effectuer cette tâche. Pour cela, suivez les étapes ci-dessous :


1. Accédez à l'application API créée en accédant à ->Applications API-><Your Rules API App>. Cela vous permet d'accéder à un tableau de bord de l'application API de règles semblable à celui-ci :

   ![Alt text][4]

2. Cliquez ensuite sur « Définitions de vocabulaire ». L'écran de création de vocabulaire s'affiche. Cliquez sur « Ajouter » pour commencer à ajouter de nouvelles définitions de vocabulaire. Deux types de définitions de vocabulaire sont actuellement prises en charge : les définitions littérales et XML.

##Définition littérale
1.	Après un clic sur « Ajouter », un nouveau panneau « Ajouter une définition » s'ouvre. Entrez les valeurs suivantes.
  1.	Nom : seuls des caractères alphanumériques sont attendus, sans caractères spéciaux. Ce nom doit également être unique dans votre liste de définitions de vocabulaire existante.
  2.	Description : ce champ est facultatif.
  3.	Type : deux types sont pris en charge. Pour cet exemple, choisissez Littéral.
  4.	Type d'entrée : permet aux utilisateurs de sélectionner le type de données de la définition. Actuellement, quatre types de données peuvent être sélectionnés : i. Chaîne : ces valeurs doivent être placées entre guillemets doubles ("exemple de chaîne") ii. Booléen : peut être vrai ou faux iii. Nombre : peut être n’importe quel nombre décimal iv. DateHeure : signifie que la définition est de type « date ». Vous devez entrer les données au format suivant : mm/jj/aaaa hh:mm: AM\PM v. Entrée : c'est là que vous entrez la valeur de votre définition. Les valeurs entrées ici doivent être conformes au type de données choisi. L'utilisateur peut entrer une valeur unique, un ensemble de valeurs séparées par des virgules ou une plage de valeurs à l'aide du mot clé « to ». Pa exemple, l'utilisateur peut entrer la valeur unique 1 ; un ensemble 1, 2, 3 ; ou la plage 1 to 5. Notez que la plage est prise en charge uniquement pour les nombres.

![Alt text][5]
##Définition XML
Si le type de vocabulaire choisi est XML, vous devez spécifier les entrées suivantes a. Schéma : un clic sur cette option ouvre un nouveau panneau où l’utilisateur peut choisir parmi une liste de schémas déjà téléchargés ou en télécharger un nouveau b. XPATH : cette entrée est déverrouillée uniquement quand vous avez choisi un schéma à l'étape précédente. Un clic sur cette option affiche le schéma sélectionné et permet à l’utilisateur de choisir le nœud pour lequel une définition de vocabulaire doit être créée. c. FAIT : cette entrée identifie l'objet de données qui serait transmis au moteur de règles pour le traitement. Il s'agit d'une propriété avancée. Par défaut, elle a comme valeur le parent de l'expression XPATH sélectionnée. L'entrée FACT est particulièrement importante pour les scénarios de chaînage et de collection.

![Alt text][6]

### Ajouter en bloc
Les étapes ci-dessus décrivent l'expérience de création de définitions de vocabulaire. Une fois créées, les définitions de vocabulaire sont affichées sous forme de liste. Il existe des exigences pour pouvoir générer plusieurs définitions à partir du même schéma au lieu de répéter chaque fois les étapes ci-dessus. La fonctionnalité « Ajouter en bloc » est très utile dans ce cas. Un clic sur « Ajouter en bloc » vous permet d'accéder à un nouveau panneau. La première étape consiste à sélectionner le schéma pour lequel vous voulez créer plusieurs définitions. Un nouveau panneau s'ouvre, dans lequel vous pouvez choisir parmi une liste de schémas déjà téléchargés ou en télécharger un nouveau. La propriété XPATHS est maintenant déverrouillée. Un clic dessus ouvre la Visionneuse de schéma, où vous pouvez sélectionner plusieurs nœuds. Les noms des définitions créées correspondent par défaut au nom du nœud sélectionné. Vous pouvez toujours les modifier après la création.

![Alt text][7]

##Création d'une stratégie
Une fois que le développeur a créé les vocabulaires nécessaires, il incombe normalement à l'analyste d'entreprise de créer des stratégies d'entreprise via le portail Azure. 1. Dans l'application de règles créée figure une thématique Stratégie. Un clic dessus permet à l'utilisateur d'accéder à la page de création de stratégie. 2. Cette page affiche la liste des stratégies de cette application de règles. Pour ajouter une nouvelle stratégie, il suffit à l'utilisateur de taper un nom de stratégie et d'appuyer sur la touche Tab. Plusieurs stratégies peuvent résider dans une même application API de règles. 3. Un clic sur la stratégie créée redirige l'utilisateur vers la page de détails de la stratégie, où figurent ses règles. ![Alt text][8] 4. Cliquez sur « Ajouter nouveau » pour ajouter une nouvelle règle. Un nouveau panneau s'affiche.

##Création de règles
Une règle est une collection d'instructions de condition et d'action. Les actions sont exécutées si la condition est remplie. Dans le panneau Créer une règle, donnez un nom unique à la règle (pour cette stratégie) et une description (facultative). La zone Condition peut servir à créer des instructions conditionnelles complexes. Voici les mots clés pris en charge : 1. And : opérateur conditionnel 2. Or : opérateur conditionnel 3. does_not_exist 4. exists 5. false 6. is_equal_to 7. is_greater_than 8. is_greater_than_equal_to 9. is_in 10. is_less_than 11. is_less_than_equal_to 12. is_not_in 13. is_not_equal_to 14. mod 15. true

La zone Action(Then) peut contenir plusieurs instructions, une par ligne, pour créer des actions à exécuter. Voici les mots clés pris en charge : 1. equals 2. false 3. true 4. halt 5. mod 6. null 7. update

Les zones de condition et d'action offrent des fonctionnalités Intellisense pour aider les utilisateurs à créer une règle rapidement. Vous pouvez les déclencher en appuyant sur Ctrl+Espace ou simplement en commençant à taper. Les mots clés correspondant aux caractères tapés sont automatiquement filtrés et affichés. La fenêtre Intellisense affiche tous les mots clés et les définitions de vocabulaire. ![Alt text][9]

##Chaînage avant explicite
Les règles BizTalk prennent en charge le chaînage avant explicite. Cela signifie que si des utilisateurs souhaitent réévaluer des règles en réponse à certaines actions, ils peuvent déclencher cette réévaluation en utilisant certains mots clés. Les mots clés pris en charge sont les suivants : 1. update <vocabulary definition> : ce mot clé réévalue toutes les règles qui utilisent la définition de vocabulaire spécifiée dans sa condition. 2. Halt : ce mot clé arrête toutes les exécutions de règles.

##Activer/désactiver des règles
Chaque règle de la stratégie peut être activée ou désactivée. Par défaut, toutes les règles sont activées. Les règles désactivées ne sont pas exécutées lors de l'exécution de la stratégie. Vous pouvez activer/désactiver une règle directement à partir du panneau de la règle (les commandes sont disponibles dans la barre de commandes en haut du panneau) ou à partir de la stratégie (le menu contextuel, accessible par un clic droit sur une règle, contient une option pour activer/désactiver la règle).

##Priorité des règles
Toutes les règles d'une stratégie sont exécutées dans l'ordre. La priorité d'exécution est déterminée par l'ordre dans lequel elles apparaissent dans la stratégie. Vous pouvez changer cette priorité en faisant simplement glisser la règle.

##Tester une stratégie
Après avoir créé votre stratégie, vous pouvez la tester avant de l'utiliser en production. La commande « Tester la stratégie » permet aux utilisateurs d'accéder au panneau du même nom. Dans ce panneau figure une liste de définitions de vocabulaire utilisées dans la stratégie et qui nécessitent une entrée utilisateur. Les utilisateurs peuvent ajouter manuellement des valeurs pour ces entrées pour leur scénario de test. Ils peuvent aussi choisir d'importer des données XML de test comme entrées. Une fois toutes les entrées ajoutées, vous pouvez exécuter le test. Les sorties de chaque définition de vocabulaire sont affichées dans la colonne de sortie pour faciliter la comparaison. Pour afficher des journaux d'exécution compréhensibles par les analystes d'entreprise, cliquez sur « Afficher les journaux ». Pour enregistrer les journaux, cliquez sur l'option « Enregistrer la sortie » pour stocker toutes les données liées au test en vue de procéder à une analyse indépendante.

## Utilisation de règles dans des applications logiques
Une fois la stratégie créée et testée, elle est prête pour la consommation. Les utilisateurs peuvent créer une application logique en sélectionnant Nouveau->Application logique. Dans le concepteur, les règles BizTalk sont disponibles dans la galerie de droite. Vous pouvez maintenant la glisser-déplacer sur l'aire du concepteur. Après quoi, il est possible de choisir l'application API de règles (action) à cibler. Les actions incluent la liste des stratégies à exécuter. Choisissez une stratégie spécifique, puis spécifiez les entrées nécessaires pour la stratégie. Les utilisateurs peuvent utiliser la sortie de l'application API de règles en aval pour toute prise de décision ultérieure.

## Utilisation de règles via des API
Vous pouvez aussi appeler l'application API de règles à l'aide d'un ensemble complet d'API. Ainsi, les utilisateurs ne sont pas limités à la simple utilisation de flux, mais peuvent utiliser des règles dans n'importe quelle application en effectuant des appels REST. Les API REST exactes disponibles sont consultables en cliquant sur le filtre « Définition API » dans le tableau de bord des règles.

![Alt text][10]

Voici un exemple de la façon dont il est possible d’utiliser cette API en C#

   			// Constructing the JSON message to use in API call to Rules API App

			// xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0="http://tempuri.org/XMLSchema.xsd">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

			// The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
			// In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
			// This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

			// The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Notez que les paramètres de sécurité de l’application API Règles ci-dessus ont la valeur « Public Anon ». Cela peut être défini à partir de l’application API à l’aide de - Tous les paramètres -> Paramètres de l’application -> Niveau d’accès

![Alt text][11]

## Modification de vocabulaire et de stratégie
L'un des principaux avantages offerts par l'utilisation des règles d'entreprise est que les modifications apportées à la logique métier peuvent être transmises en production beaucoup plus rapidement. Toute modification apportée aux stratégies et aux vocabulaires est immédiatement appliquée en production. Il suffit à l'utilisateur d'accéder à la stratégie ou à la définition de vocabulaire concernée et d'apporter la modification pour qu'elle soit appliquée.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG

<!---HONumber=July15_HO4-->