<properties 
	pageTitle="Vue d’ensemble de X12 et d’Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
	description="Découvrez comment utiliser les contrats X12 pour créer des applications logiques" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Intégration d’entreprise avec X12 

## Créer un contrat X12 
Avant de pouvoir échanger des messages X12, vous devez créer un contrat X12 et le stocker dans votre compte d’intégration. Les étapes suivantes vous guideront lors du processus de création d’un contrat X12.

### Voici ce dont vous avez besoin pour commencer
- Un [compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) défini dans votre abonnement Azure
- Au moins deux [partenaires](./app-service-logic-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration

>[AZURE.NOTE]Lorsque vous créez un contrat, le contenu du fichier de contrat doit correspondre au type de contrat.


Une fois que vous avez [créé un compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) et [ajouté des partenaires](./app-service-logic-enterprise-integration-partners.md), vous pouvez créer un contrat X12 en suivant ces étapes :

### À partir de la page d’accueil du portail Azure

Une fois que vous êtes connecté au [portail Azure](http://portal.azure.com "Portail Azure") :
1. Sélectionnez **Parcourir** dans le menu à gauche.

>[AZURE.TIP]Si le lien **Parcourir** ne s’affiche pas, vous devrez peut-être développer le menu. Pour ce faire, sélectionnez le lien **Afficher le menu** situé dans le coin supérieur gauche du menu réduit.

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez *intégration* dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats. ![](./media/app-service-logic-enterprise-integration-x12/x12-1-3.png)
3. Dans le panneau **Integration Accounts** (Comptes d’intégration) qui s’affiche, sélectionnez le compte d’intégration dans lequel vous allez créer le contrat. Si aucun compte d’intégration ne s’affiche, [créez-en un](./app-service-logic-enterprise-integration-accounts.md "Tout sur les comptes d’intégration"). ![](./media/app-service-logic-enterprise-integration-x12/x12-1-4.png)
4.  Sélectionnez la mosaïque **Contrats**. Si vous ne voyez pas la mosaïque Contrats, commencez par l’ajouter. ![](./media/app-service-logic-enterprise-integration-x12/x12-1-5.png)
5. Sélectionnez le bouton **Ajouter** dans le panneau Contrats qui s’affiche. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. Entrez un **nom** pour votre contrat, puis sélectionnez le **type de contrat**, le **partenaire hôte**, **l’identité de l’hôte**, le **partenaire invité**, **l’identité de l’invité**, dans le panneau Contrats qui s’affiche. ![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)
7. Après avoir défini les propriétés des paramètres de réception, sélectionnez le bouton **OK** pour continuer :
8. Sélectionnez les **Paramètres de réception** pour configurer la façon dont les messages reçus dans le cadre de ce contrat doivent être traités.
9. Le contrôle Paramètres de réception est divisé en plusieurs sections : identificateurs, accusé de réception, schémas, enveloppes, numéros de contrôle, validations et paramètres internes. Configurez ces propriétés selon le contrat conclu avec le partenaire avec lequel vous échangerez des messages. Voici un aperçu de ces contrôles ; configurez-les en fonction de la manière dont ce contrat doit identifier et gérer les messages entrants : ![](./media/app-service-logic-enterprise-integration-x12/x12-2.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-3.png)
10. Sélectionnez le bouton **OK** pour enregistrer vos paramètres.

### Identificateurs

|Propriété|Description |
|---|---|
|ISA1 (qualificateur d’autorisation)|Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation.|
|ISA2|facultatif. Entrez la valeur d’autorisation. Si la valeur entrée pour ISA1 est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum).|
|ISA3 (qualificateur de sécurité)|Dans la liste déroulante, sélectionnez la valeur du qualificateur de sécurité.|
|ISA4|facultatif. Entrez la valeur de sécurité. Si la valeur entrée pour ISA3 est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum).|

### Remerciements 

|Propriété|Description |
|----|----|
|TA1 attendu|Activez cette case à cocher pour renvoyer un accusé de réception technique (TA1) à l’expéditeur. Ces accusés de réception sont envoyés à l’expéditeur en fonction des paramètres d’envoi du contrat.|
|FA attendu|Cochez cette case pour renvoyer un accusé de réception fonctionnel (FA) à l’expéditeur. Indiquez ensuite si vous souhaitez utiliser les accusés de réception 997 ou 999, selon les versions de schéma sur lesquelles vous travaillez. Ces accusés de réception sont envoyés à l’expéditeur en fonction des paramètres d’envoi du contrat.|
|Inclure une boucle AK2/IK2|Cochez cette case pour activer la génération de boucles AK2 dans les accusés de réception fonctionnels pour les ensembles de transactions acceptés. Remarque : cette case est cochée uniquement si vous avez sélectionné la case à cocher FA attendu.|

### Schémas

Choisissez un schéma pour chaque type de transaction (ST1) et application de l’expéditeur (GS2). Le pipeline de réception désassemble le message entrant en faisant correspondre les valeurs ST1 et GS2 dans le message entrant avec les valeurs que vous définissez ici, et le schéma du message entrant avec le schéma que vous définissez ici.

|Propriété|Description |
|----|----|
|Version|Sélectionnez la version X12|
|Type de transaction (ST01)|Sélectionnez le type de transaction|
|Application de l’expéditeur (GS02)|Sélectionnez l’application de l’expéditeur|
|Schéma|Sélectionnez le fichier de schéma que vous souhaitez utiliser. Les fichiers de schéma se trouvent dans votre compte d’intégration.|

### Enveloppes

|Propriété|Description |
|----|----|
|Utilisation d’ISA11|Ce champ permet de spécifier le séparateur dans un document informatisé :</br></br>Sélectionnez l’identificateur Standard afin d’utiliser la notation décimale « . » au lieu de la notation décimale du document entrant dans le pipeline de réception EDI.</br></br>Sélectionnez le séparateur de répétition pour spécifier le séparateur des occurrences répétées d’un élément de données simple ou une structure de données répétées. Par exemple, (^) est généralement utilisé comme séparateur de répétition. Pour les schémas HIPAA, vous pouvez uniquement utiliser (^).|

### Numéros de contrôle

|Propriété|Description |
|----|----|
|Interdire les doublons de numéro de contrôle d’échange|Cochez cette option pour bloquer les échanges en double. Si cette option est sélectionnée, le portail BizTalk Services vérifie que le numéro de contrôle d’échange (ISA13) pour l’échange reçu ne correspond pas au numéro de contrôle. Si une correspondance est détectée, le pipeline de réception ne traite pas l’échange.<br/>Si vous avez choisi de ne pas autoriser les numéros de contrôle d’échange en double, vous pouvez spécifier le nombre de jours après lequel le contrôle est effectué en définissant la valeur appropriée dans le champ Chercher ISA13 en double dans le nombre de jours suivant.|
|Interdire les numéros de contrôle de groupe en double|Cochez cette option pour bloquer les échanges avec des numéros de contrôle de groupe en double.|
|Interdire les numéros de contrôle de document informatisé en double|Cochez cette option pour bloquer les échanges avec des numéros de contrôle de document informatisé en double.|

### Validations

|Propriété|Description |
|----|----|
|Type de message|Type de Message EDI, par exemple 850-Purchase Order ou 999-Implementation Acknowledgement.|
|Validation EDI|Effectue une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin.|
|Validation étendue|Si le type de données n’est pas EDI, la validation s’effectue sur la spécification d’élément de données et la répétition autorisée, les énumérations et la validation de la longueur d’élément de données (min/max).|
|Autoriser les zéros de début ou de fin|Les espaces supplémentaires et les zéros de début ou de fin sont conservés. Ils ne sont pas supprimés.|
|Stratégie de séparateur de fin|Génère des séparateurs de fin sur l’échange reçu. Les options incluent NotAllowed (non autorisé), Optional (facultatif) et Mandatory (obligatoire).|

### Paramètres internes

|Propriété|Description |
|----|----|
|Convertir le format décimal implicite Nn en valeur numérique de base 10|Convertit un numéro EDI spécifié au format Nn en une valeur numérique de base 10 dans le fichier XML intermédiaire sur le portail BizTalk Services.|
|Créer des balises XML vides si les séparateurs de fin sont autorisés|Cochez cette case afin d’inclure des balises XML vides pour les séparateurs de l’expéditeur.|
|Processus de traitement par lot entrant|Fractionner l’échange en documents informatisés - suspendre les documents informatisés en cas d’erreur : analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée au document informatisé. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, BizTalk Services suspend uniquement ces documents informatisés. </br></br>Fractionner l’échange en documents informatisés - suspendre l’échange en cas d’erreur : analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, BizTalk Services suspend la totalité de l’échange.</br></br>Préserver l’échange : suspendre les documents informatisés en cas d’erreur : laisse l’échange intact, en créant un document XML pour l’ensemble de l’échange par lot. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, BizTalk Services suspend uniquement ces documents informatisés et continue de traiter tous les autres documents informatisés.</br></br>Préserver l’échange : suspendre l’échange en cas d’erreur : laisse l’échange intact, en créant un document XML pour l’ensemble de l’échange par lot. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, BizTalk Services suspend la totalité de l’échange.</br></br>|

Votre contrat est prêt à traiter les messages entrants qui sont conformes au schéma que vous avez sélectionné.

Pour configurer les paramètres qui gèrent les messages que vous envoyez aux partenaires :
11. Sélectionnez les **Paramètres d’envoi** pour configurer la façon dont les messages envoyés dans le cadre de ce contrat doivent être traités.

Le contrôle Paramètres d’envoi est divisé en plusieurs sections : identificateurs, accusé de réception, schémas, enveloppes, numéros de contrôle, jeux de caractères, séparateurs et validation.

Voici une vue de ces contrôles. Effectuez les sélections selon la façon dont vous souhaitez gérer les messages envoyés aux partenaires via ce contrat : ![](./media/app-service-logic-enterprise-integration-x12/x12-4.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-5.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-6.png)
12. Sélectionnez le bouton **OK** pour enregistrer vos paramètres.

### Identificateurs
|Propriété|Description |
|----|----|
|Qualificateur d’autorisation (ISA1)|Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation.|
|ISA2|Entrez la valeur d’autorisation. Si cette valeur est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum).|
|Qualificateur de sécurité (ISA3)|Dans la liste déroulante, sélectionnez la valeur du qualificateur de sécurité.|
|ISA4|Entrez la valeur de sécurité. Si cette valeur est autre que 00, dans la zone de texte Valeur (ISA4), entrez au moins un caractère alphanumérique (et 10 caractères maximum).|

### Accusé de réception
|Propriété|Description |
|----|----|
|TA1 attendu|Activez cette case à cocher pour renvoyer un accusé de réception technique (TA1) à l’expéditeur. Ce paramètre spécifie que le partenaire hôte qui envoie le message demande un accusé de réception de la part du partenaire invité figurant dans le contrat. Ces accusés de réception sont attendus par le partenaire hôte en fonction des paramètres de réception du contrat.|
|FA attendu|Cochez cette case pour renvoyer un accusé de réception fonctionnel (FA) à l’expéditeur, puis indiquez si vous souhaitez utiliser les accusés de réception 997 ou 999, selon les versions de schéma sur lesquelles vous travaillez. Ces accusés de réception sont attendus par le partenaire hôte en fonction des paramètres de réception du contrat.|
|Version FA|Sélectionnez la version FA|

### Schémas
|Propriété|Description |
|----|----|
|Version|Sélectionnez la version X12|
|Type de transaction (ST01)|Sélectionnez le type de transaction|
|SCHÉMA|Sélectionnez le schéma à utiliser. Les schémas se trouvent dans votre compte d’intégration. Pour accéder à vos schémas, vous devez tout d’abord lier votre compte d’intégration à votre application logique.|

### Enveloppes
|Propriété|Description |
|----|----|
|Utilisation d’ISA11|Ce champ permet de spécifier le séparateur dans un document informatisé :</br></br>Sélectionnez l’identificateur Standard afin d’utiliser la notation décimale « . » au lieu de la notation décimale du document entrant dans le pipeline de réception EDI.</br></br>Sélectionnez le séparateur de répétition pour spécifier le séparateur des occurrences répétées d’un élément de données simple ou une structure de données répétées. Par exemple, (^) est généralement utilisé comme séparateur de répétition. Pour les schémas HIPAA, vous pouvez uniquement utiliser (^).</br>|
|Séparateur de répétition|Entrez le séparateur de répétition|
|Numéro de contrôle de version (ISA12)|Sélectionnez la version du protocole X12 utilisée par le portail BizTalk Services pour générer un échange sortant.|
|Indicateur d’utilisation (ISA15)|Indiquez si le contexte d’un échange est de type information (I), données de production (P), ou données de test (T). Le pipeline de réception EDI adapte cette propriété au contexte.|
|Schéma|Vous pouvez spécifier la façon dont le portail BizTalk Services génère les segments GS et ST pour un échange X12 encodé qu’il envoie au pipeline d’envoi.</br></br>Vous pouvez associer les valeurs des éléments de données GS1, GS2, GS3, GS4, GS5, GS7 et GS8 avec les valeurs du type de transaction et les éléments de données de version/publication. Lorsque le portail BizTalk Services détermine qu’un message XML possède des valeurs définies pour les éléments Type de Transaction et Version/publication dans une ligne de la grille, il remplit les éléments de données GS1, GS2, GS3, GS4, GS5, GS7 et GS8 dans l’enveloppe de l’échange sortant avec les valeurs de la même ligne de la grille. Les valeurs Type de Transaction et les éléments de Version/publication doivent être uniques.</br></br>Facultatif. Pour GS1, sélectionnez une valeur pour le code fonctionnel dans la liste déroulante.</br></br>Requis. Pour GS2, entrez une valeur alphanumérique pour l’expéditeur de l’application avec un minimum de deux caractères et un maximum de 15 caractères.</br></br>Requis. Pour GS3, entrez une valeur alphanumérique pour le récepteur de l’application avec un minimum de deux caractères et un maximum de 15 caractères.</br></br>Facultatif. Pour GS4, sélectionnez SSAAMMJJ ou AAMMJJ.</br></br>Facultatif. Pour GS5, sélectionnez HHMM, HHMMSS ou HHMMSSjj.</br></br>Facultatif. Pour GS7, sélectionnez une valeur pour l’agence responsable dans la liste déroulante.</br></br>Facultatif. Pour GS8, entrez une valeur alphanumérique pour le document identifié avec un minimum d’un caractère et un maximum de 12 caractères.</br></br>**Remarque** : il s’agit des valeurs que le portail BizTalk Services entre dans les champs GS de l’échange qu’il crée si les éléments Type de Transaction et Version/publication dans la même ligne correspondent à ceux associés à l’échange.|

### Numéros de contrôle
|Propriété|Description |
|----|----|
|Numéro de contrôle de l’échange (ISA13)|Obligatoire. Entrez une plage de valeurs pour le numéro de contrôle de l’échange utilisé par le portail BizTalk Services lors de la génération d’un échange sortant. Entrez une valeur numérique comprise entre 1 et 999 999 999.|
|Numéro de contrôle de groupe (GS06)|Obligatoire. Entrez la plage de valeurs que le portail BizTalk Services doit utiliser pour le numéro de contrôle de groupe. Entrez une valeur numérique avec un minimum d’un caractère et un maximum de neuf caractères.|
|Numéro de contrôle de document informatisé (ST02)|Pour le numéro de contrôle de document informatisé (ST02), entrez une plage de valeurs numériques pour les champs du milieu requis et des valeurs alphanumériques pour le préfixe et le suffixe facultatifs. La longueur maximale des quatre champs est de neuf caractères.|
|Préfixe|Pour désigner la plage de numéros de contrôle de documents informatisés utilisés dans un accusé de réception, entrez des valeurs dans les champs Numéro de contrôle d’accusé de réception (ST02). Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont requis et contiennent les valeurs minimales et maximales pour le numéro de contrôle ; le préfixe et le suffixe sont facultatifs. La longueur maximale des trois champs est de neuf caractères.|
|Suffixe|Pour désigner la plage de numéros de contrôle de documents informatisés utilisés dans un accusé de réception, entrez des valeurs dans les champs Numéro de contrôle d’accusé de réception (ST02). Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont requis et contiennent les valeurs minimales et maximales pour le numéro de contrôle ; le préfixe et le suffixe sont facultatifs. La longueur maximale des trois champs est de neuf caractères.|

### Jeux de caractères et séparateurs
outre le jeu de caractères, vous pouvez entrer un autre ensemble de délimiteurs à utiliser pour chaque type de message. Si un jeu de caractères n’est pas spécifié pour un schéma de message donné, le jeu de caractères par défaut est utilisé.

|Propriété|Description |
|----|----|
|Jeu de caractères à utiliser|Sélectionnez le jeu de caractères X12 pour valider les propriétés que vous entrez pour le contrat.</br></br>**Remarque** : le portail BizTalk Services utilise uniquement ce paramètre pour valider les valeurs entrées pour les propriétés correspondantes du contrat. Le pipeline de réception ou d’envoi ignore cette propriété de jeu de caractères lors du traitement à l’exécution.|
|Schéma|Sélectionnez le symbole signe plus (+) puis choisissez un schéma dans la liste déroulante. Pour le schéma sélectionné, sélectionnez les séparateurs à utiliser :</br></br>séparateur d’éléments de composant – entrez un caractère unique pour séparer les éléments de données composites.</br></br>Séparateur d’éléments de données – entrez un caractère unique pour séparer les éléments de données simples au sein d’éléments de données composites.</br></br></br></br>Caractère de remplacement – cochez cette case si les données de charge utile contiennent des caractères également utilisés comme séparateurs de composant, de données ou de segment. Vous pouvez ensuite entrer un caractère de remplacement. Lors de la génération du message X12 sortant, toutes les instances des caractères de séparation dans les données de charge utile sont remplacées par le caractère spécifié.</br></br>Terminateur de segment : entrez un seul caractère pour indiquer la fin d’un segment EDI.</br></br>Suffixe : sélectionnez le caractère utilisé avec l’identificateur de segment. Si vous désignez un suffixe, l’élément de données de terminateur de segment peut être vide. Si le terminateur de segment est laissé vide, vous devez désigner un suffixe.|

### Validation
|Propriété|Description |
|----|----|
|Type de message|Cette option active la validation sur le récepteur d’échange. Cette validation réalise une validation EDI sur des éléments de données de document informatisé, une validation des types de données, des restrictions de longueur, des éléments de données vides et des séparateurs de fin.|
|Validation EDI||
|Validation étendue|Cette option permet la validation étendue des échanges reçus de l’expéditeur. Cela inclut la validation de la longueur de champ, du caractère facultatif et du nombre de répétitions en plus de la validation du type de données XSD. Vous pouvez activer la validation étendue sans activer la validation EDI, ou vice versa.|
|Autoriser les zéros de début ou de fin|Cette option indique que la validation d’un échange EDI reçu d’un tiers n’échoue pas si un élément de données d’un échange EDI n’est pas conforme à sa spécification de longueur en raison d’espaces de fin, mais est conforme à la restriction de longueur lorsque ces espaces sont supprimés.|
|Séparateur de fin|Cette option indique que la validation d’un échange EDI reçu d’un tiers n’échoue pas si un élément de données d’un échange EDI n’est pas conforme à sa spécification de longueur en raison de zéros de début (ou de fin) ou d’espaces de fin, mais est conforme à la restriction de longueur lorsqu’ils sont supprimés.</br></br>Sélectionnez Not Allowed (non autorisé) si vous souhaitez exclure les délimiteurs et les séparateurs de fin d’un échange reçu de l’expéditeur. Si l’échange contient des délimiteurs et des séparateurs de fin, il est déclaré non valide.</br></br>Sélectionnez Optional (facultatif) pour accepter les échanges avec ou sans délimiteurs et séparateurs de fin.</br></br>Sélectionnez Mandatory (obligatoire) si l’échange reçu doit contenir des délimiteurs et des séparateurs de fin.|

Après avoir sélectionné **OK** sur les panneaux ouverts :
13. Sélectionnez la mosaïque **Contrats** dans le panneau du compte d’intégration pour afficher le nouveau contrat ajouté. ![](./media/app-service-logic-enterprise-integration-x12/x12-7.png)

## En savoir plus
- [En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

<!---HONumber=AcomDC_0713_2016-->