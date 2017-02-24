---
title: "Créer un contrat X12 dans Azure Logic Apps | Microsoft Docs"
description: "Apprendre à créer un contrat X12 pour Enterprise Integration Pack | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 1a982309fe771c0c244c691ae648bd5f4844a825
ms.openlocfilehash: f7b61b85cadfabde6637e46f0e65108a29901198


---
# <a name="enterprise-integration-with-x12"></a>Intégration d’entreprise avec X12

## <a name="prereqs"></a>Conditions préalables
Avant de pouvoir échanger des messages X12, vous devez créer un contrat X12 et le stocker dans votre compte d’intégration. Les étapes suivantes vous guideront lors du processus de création d’un contrat X12.

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) défini dans votre abonnement Azure  
* Au moins deux [partenaires](../logic-apps/logic-apps-enterprise-integration-partners.md) configurés avec le qualificateur EDI X12 sous des identités d’entreprise   
* Un [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) requis à charger dans le [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md)

## <a name="create-an-x12-agreement"></a>Créer un contrat X12
Une fois que vous êtes connecté au [portail Azure](http://portal.azure.com "portail Azure"):  

1. Connectez-vous au [portail Azure](http://portal.azure.com "portail Azure").
2. Sélectionnez **Plus de services** et entrez **intégration** dans la zone de recherche de filtre. Sélectionnez **Comptes d’intégration** dans la liste des résultats :    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Sélectionnez le compte d’intégration auquel ajouter le certificat :    
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)   
4. Sélectionnez la mosaïque **Contrats** . Si vous ne la voyez pas, commencez par l’ajouter :     
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. Sélectionnez le bouton **Ajouter** dans le panneau Contrats qui s’affiche.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)     
6. Entrez un **nom** pour votre contrat, puis sélectionnez le **type de contrat**, le **partenaire hôte**, **l’identité de l’hôte**, le **partenaire invité** et **l’identité de l’invité** dans le panneau Contrats qui s’affiche.    
![](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

| Propriété | Description |
| --- | --- |
| Nom |Nom du contrat. |
| Type de contrat | Doit être X12. |
| Partenaire hôte |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire hôte représente l’organisation qui configure le contrat. |
| Identité de l’hôte |Identificateur du partenaire hôte. |
| Partenaire invité |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire invité représente l’organisation qui travaille avec le partenaire hôte. |
| identité de l’invité |Identificateur du partenaire invité. |
| Paramètres de réception |Ces propriétés s’appliquent à tous les messages reçus par un contrat |
| Paramètres d’envoi |Ces propriétés s’appliquent à tous les messages envoyés par un contrat |  

> [!NOTE]
> La résolution du contrat X12 dépend de la mise en correspondance des qualificateurs et des identificateurs de l’expéditeur et du destinataire définis dans le partenaire et le message entrant.  Si des modifications sont apportées à ces valeurs au niveau du partenaire, modifiez également le contrat.
> 
> 

## <a name="receive-settings"></a>Paramètres de réception

1. Sélectionnez les **Paramètres de réception** pour configurer la façon dont les messages reçus dans le cadre de ce contrat doivent être traités.  
2. Le contrôle Paramètres de réception est divisé en plusieurs sections : identificateurs, accusé de réception, schémas, enveloppes, numéros de contrôle, validations et paramètres internes. Configurez ces propriétés selon le contrat conclu avec le partenaire avec lequel vous échangez des messages. Voici un aperçu de ces contrôles ; configurez-les en fonction de la manière dont ce contrat doit identifier et gérer les messages entrants  

### <a name="identifiers"></a>Identificateurs

![](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriété | Description |
| --- | --- |
| ISA1 (qualificateur d’autorisation) |Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation. |
| ISA2 |facultatif. Entrez la valeur d’autorisation. Si la valeur entrée pour ISA1 est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum). |
| ISA3 (qualificateur de sécurité) |Dans la liste déroulante, sélectionnez la valeur du qualificateur de sécurité. |
| ISA4 |facultatif. Entrez la valeur de sécurité. Si la valeur entrée pour ISA3 est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum). |

### <a name="acknowledgments"></a>Remerciements

![](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriété | Description |
| --- | --- |
| TA1 attendu |Renvoie un accusé de réception technique à l’expéditeur de l’échange. |
| FA attendu |Renvoie un accusé de réception fonctionnel à l’expéditeur de l’échange. Indiquez ensuite si vous souhaitez utiliser les accusés de réception 997 ou 999, selon la version du schéma. |
| Inclure une boucle AK2/IK2 |Permet la génération de boucles AK2 dans les accusés de réception fonctionnels des documents informatisés acceptés. |


### <a name="schemas"></a>Schémas
Choisissez un schéma pour chaque type de transaction (ST1) et application de l’expéditeur (GS2). Le pipeline de réception désassemble le message entrant en faisant correspondre les valeurs ST1 et GS2 dans le message entrant avec les valeurs que vous définissez ici, et le schéma du message entrant avec le schéma que vous définissez ici.

![](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriété | Description |
| --- | --- |
| Version |Sélectionnez la version X12 |
| Type de transaction (ST01) |Sélectionnez le type de transaction |
| Application de l’expéditeur (GS02) |Sélectionnez l’application de l’expéditeur |
| Schéma |Sélectionnez le fichier de schéma que vous souhaitez utiliser. Les schémas sont ajoutés à votre compte d’intégration. |

> [!NOTE]
> Configurez le [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) requis qui est téléchargé dans le [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md).
> 
> 

### <a name="envelopes"></a>Enveloppes

![](./media/logic-apps-enterprise-integration-x12/x12-34.png) 

| Propriété | Description |
| --- | --- |
| Utilisation d’ISA11 |Ce champ permet de spécifier le séparateur dans un document informatisé :</br></br>Sélectionnez l’identificateur Standard afin d’utiliser la notation décimale « . » au lieu de la notation décimale du document entrant dans le pipeline de réception EDI.</br></br>Sélectionnez le séparateur de répétition pour spécifier le séparateur des occurrences répétées d’un élément de données simple ou une structure de données répétées. Par exemple, (^) est généralement utilisé comme séparateur de répétition. Pour les schémas HIPAA, vous pouvez uniquement utiliser (^). |

### <a name="control-numbers"></a>Numéros de contrôle

![](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriété | Description |
| --- | --- |
| Interdire les doublons de numéro de contrôle d’échange |Bloque les échanges en double. Vérifie le numéro de contrôle d’échange (ISA13) du numéro de contrôle de l’échange reçu. Si une correspondance est détectée, le pipeline de réception ne traite pas l’échange. Vous pouvez spécifier la fréquence en nombre de jours à laquelle le contrôle est effectué en définissant la valeur appropriée dans le champ *Check for duplicate ISA13 every x days* (Vérifier les doublons ISA13 tous les x jours). |
| Interdire les numéros de contrôle de groupe en double |Bloque les échanges avec des numéros de contrôle de groupe en double. |
| Interdire les numéros de contrôle de document informatisé en double |Bloque les échanges avec des numéros de contrôle de document informatisé en double. |

### <a name="validations"></a>Validations

![](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

| Propriété | Description |
| --- | --- |
| Type de message |Types de message EDI, par exemple 850-Purchase Order ou 999-Implementation Acknowledgement. |
| Validation EDI |Effectue une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin. |
| Validation étendue |Si le type de données n’est pas EDI, la validation s’effectue sur la spécification d’élément de données et la répétition autorisée, les énumérations et la validation de la longueur d’élément de données (min/max). |
| Autoriser les zéros de début ou de fin |Les espaces supplémentaires et les zéros de début ou de fin sont conservés. Ils ne sont pas supprimés. |
| Stratégie de séparateur de fin |Génère des séparateurs de fin sur l’échange reçu. Les options incluent NotAllowed (non autorisé), Optional (facultatif) et Mandatory (obligatoire). |

### <a name="internal-settings"></a>Paramètres internes

![](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriété | Description |
| --- | --- |
| Convertir le format décimal implicite Nn en valeur numérique de base 10 |Convertit un numéro EDI spécifié au format Nn en une valeur numérique de base&10;. |
| Créer des balises XML vides si les séparateurs de fin sont autorisés |Cochez cette case afin d’inclure des balises XML vides pour les séparateurs de l’expéditeur. |
| Scinder l’échange en documents informatisés : suspendre les documents informatisés en cas d’erreur|Analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée au document informatisé. Suspend uniquement les transactions dont la validation échoue. |
| Scinder l’échange en documents informatisés : suspendre l’échange en cas d’erreur|Analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée. Suspend l’intégralité de l’échange si la validation d’un ou plusieurs documents informatisés de l’échange échoue. | 
| Préserver l'échange : suspendre les documents informatisés en cas d’erreur |Préserve l’échange : crée un document XML pour l’intégralité de l’échange par lot. Suspend uniquement les documents informatisés dont la validation échoue, tout en continuant à traiter tous les autres documents informatisés. |
| Préserver l’échange : suspendre l’échange en cas d’erreur |Préserve l’échange : crée un document XML pour l’intégralité de l’échange par lot.  Suspend l’intégralité de l’échange si la validation d’un ou plusieurs documents informatisés de l’échange échoue. |

Après avoir défini les propriétés des paramètres de réception, sélectionnez le bouton **OK** pour continuer.  
   
Votre contrat est prêt à traiter les messages entrants qui sont conformes au schéma que vous avez sélectionné.

## <a name="send-settings"></a>Paramètres d’envoi

1. Sélectionnez les **Paramètres d’envoi** pour configurer la façon dont les messages envoyés dans le cadre de ce contrat doivent être traités.  

2. Le contrôle Paramètres d’envoi est divisé en plusieurs sections : identificateurs, accusé de réception, schémas, enveloppes, numéros de contrôle, jeux de caractères, séparateurs et validation.  Configurez ces propriétés selon le contrat conclu avec le partenaire avec lequel vous échangez des messages. Voici un aperçu de ces contrôles ; configurez-les en fonction de la manière dont ce contrat doit identifier et gérer les messages sortants.


### <a name="identifiers"></a>Identificateurs

![](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriété | Description |
| --- | --- |
| Qualificateur d’autorisation (ISA1) |Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation. |
| ISA2 |Entrez la valeur d’autorisation. Si cette valeur est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum). |
| Qualificateur de sécurité (ISA3) |Dans la liste déroulante, sélectionnez la valeur du qualificateur de sécurité. |
| ISA4 |Entrez la valeur de sécurité. Si cette valeur est autre que 00, dans la zone de texte Valeur (ISA4), entrez au moins un caractère alphanumérique (et 10 caractères maximum). |

### <a name="acknowledgment"></a>Accusé de réception

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriété | Description |
| --- | --- |
| TA1 attendu |Activez cette case à cocher pour renvoyer un accusé de réception technique (TA1) à l’expéditeur. Ce paramètre spécifie que le partenaire hôte qui envoie le message demande un accusé de réception de la part du partenaire invité figurant dans le contrat. Ces accusés de réception sont attendus par le partenaire hôte en fonction des paramètres de réception du contrat. |
| FA attendu |Cochez cette case pour renvoyer un accusé de réception fonctionnel (FA) à l’expéditeur, puis indiquez si vous souhaitez utiliser les accusés de réception 997 ou 999, selon les versions de schéma sur lesquelles vous travaillez. Ces accusés de réception sont attendus par le partenaire hôte en fonction des paramètres de réception du contrat. |
| Version FA |Sélectionnez la version FA |

### <a name="schemas"></a>Schémas

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriété | Description |
| --- | --- |
| Version |Sélectionnez la version X12 |
| Type de transaction (ST01) |Sélectionnez le type de transaction |
| Schéma |Sélectionnez le schéma à utiliser. Les schémas se trouvent dans votre compte d’intégration. Si vous sélectionnez schéma d’abord, il configure automatiquement la version et le type de transaction.  |

> [!NOTE]
> Configurez le [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) requis qui est téléchargé dans le [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md).
> 
> 

### <a name="envelopes"></a>Enveloppes

![](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriété | Description |
| --- | --- |
| Utilisation d’ISA11 |Ce champ permet de spécifier le séparateur dans une transaction. |
| Identificateur standard |Sélectionnez l’identificateur Standard afin d’utiliser la notation décimale « . |
| Séparateur de répétition |Sélectionnez le séparateur de répétition pour spécifier le séparateur des occurrences répétées d’un élément de données simple ou une structure de données répétées. Par exemple, (^) est généralement utilisé comme séparateur de répétition. Pour les schémas HIPAA, vous pouvez uniquement utiliser (^). |

### <a name="control-numbers"></a>Numéros de contrôle

![](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriété | Description |
| --- | --- |
| Numéro de contrôle de version (ISA12) |Sélectionnez la version de la X12 standard. |
| Indicateur d’utilisation (ISA15) |Sélectionnez le contexte d’un échange.  Les valeurs sont des informations, des données de production ou des données de test. |
| Schéma |Génère les segments GS et ST pour un échange X12 encodé qu’il envoie au pipeline d’envoi. |
| GS1 |Facultatif, sélectionnez une valeur pour le code fonctionnel dans la liste déroulante. |
| GS2 |Facultatif, expéditeur de l’application. |
| GS3 |Facultatif, destinataire de l’application. |
| GS4 |Facultatif, sélectionnez SSAAMMJJ ou AAMMJJ. |
| GS5 |Facultatif, sélectionnez HHMM, HHMMSS ou HHMMSSjj. |
| GS7 |Facultatif, sélectionnez une valeur pour l’agence responsable dans la liste déroulante. |
| GS8 |Facultatif, version du document. |
| Numéro de contrôle de l’échange (ISA13) |Obligatoire, entrez une plage de valeurs pour le numéro de contrôle de l’échange. Entrez une valeur numérique comprise entre 1 et 999999999. |
| Numéro de contrôle de groupe (GS06) |Obligatoire, entrez une plage de nombres pour le numéro de contrôle du groupe. Entrez une valeur numérique comprise entre 1 et 999999999. |
| Numéro de contrôle de document informatisé (ST02) |Obligatoire, entrez une plage de nombres pour le numéro de contrôle de document informatisé. Entrez une plage de valeurs numériques comprises entre 1 et 999999999. |
| Préfixe |Facultatif, désigné pour la plage de numéros de contrôle de documents informatisés utilisés dans l’accusé de réception. Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont requis et contiennent les valeurs minimales et maximales pour le numéro de contrôle. |
| Suffixe |Facultatif, désigné pour la plage de numéros de contrôle de documents informatisés utilisés dans l’accusé de réception. Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont requis et contiennent les valeurs minimales et maximales pour le numéro de contrôle. |

### <a name="character-sets-and-separators"></a>Jeux de caractères et séparateurs

outre le jeu de caractères, vous pouvez entrer un autre ensemble de délimiteurs à utiliser pour chaque type de message. Si un jeu de caractères n’est pas spécifié pour un schéma de message donné, le jeu de caractères par défaut est utilisé.

![](./media/logic-apps-enterprise-integration-x12/x12-9.png) 


| Propriété | Description |
| --- | --- |
| Jeu de caractères à utiliser |Sélectionnez le jeu de caractères X12 pour valider les propriétés.  Options : De base, Étendu et UTF-8. |
| Schéma |Dans la liste déroulante, sélectionnez un schéma. Pour le schéma sélectionné, sélectionnez les séparateurs à utiliser. |
| Type d’entrée |Dans la liste déroulante, sélectionnez un type d’entrée. |
| Séparateur d’éléments de composants |Entrez un caractère unique pour séparer les éléments de données composites. |
| Séparateur d'éléments de données |Entrez un caractère unique pour séparer les éléments de données simples au sein des éléments de données composites. |
| Caractère de remplacement |Entrez un caractère de remplacement. Lors de la génération du message X12 sortant, toutes les instances des caractères de séparation dans les données de charge utile sont remplacées par le caractère spécifié. |
| Terminateur de segment |Entrez un caractère unique pour indiquer la fin d’un segment EDI. |
| Suffixe |Sélectionnez le caractère utilisé avec l’identificateur de segment. Si vous désignez un suffixe, l’élément de données de terminateur de segment peut être vide. Si le terminateur de segment est laissé vide, vous devez désigner un suffixe. |

### <a name="validation"></a>Validation

![](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

| Propriété | Description |
| --- | --- |
| Type de message |Sélectionnez le type de message dans la liste. |
| Validation EDI |Cette option active la validation sur le récepteur d’échange. Cette validation réalise une validation EDI sur des éléments de données de document informatisé, une validation des types de données, des restrictions de longueur, des éléments de données vides et des séparateurs de fin. |
| Validation étendue |Cette option permet la validation étendue des échanges reçus de l’expéditeur. Cela inclut la validation de la longueur de champ, du caractère facultatif et du nombre de répétitions en plus de la validation du type de données XSD. Vous pouvez activer la validation étendue sans activer la validation EDI, ou vice versa. |
| Autoriser les zéros de début ou de fin |Cette option indique que la validation d’un échange EDI reçu d’un tiers n’échoue pas si un élément de données d’un échange EDI n’est pas conforme à sa spécification de longueur en raison d’espaces de fin, mais est conforme à la restriction de longueur lorsque ces espaces sont supprimés. |
| Séparateur de fin |Cette option indique que la validation d’un échange EDI reçu d’un tiers n’échoue pas si un élément de données d’un échange EDI n’est pas conforme à sa spécification de longueur en raison de zéros de début (ou de fin) ou d’espaces de fin, mais est conforme à la restriction de longueur lorsqu’ils sont supprimés.</br></br>Sélectionnez Non autorisé si vous souhaitez exclure les délimiteurs et les séparateurs de fin d’un échange reçu de l’expéditeur. Si l’échange contient des délimiteurs et des séparateurs de fin, il est déclaré non valide.</br></br>Sélectionnez Facultatif pour accepter les échanges avec ou sans délimiteurs et séparateurs.</br></br>Sélectionnez Obligatoire si l’échange reçu doit contenir des délimiteurs et des séparateurs de fin. |

Après avoir défini les propriétés des paramètres d’envoi, sélectionnez le bouton **OK**.  Votre contrat est prêt à traiter les messages sortants qui sont conformes au schéma que vous avez sélectionné.

Sélectionnez OK pour créer le contrat.

Sélectionnez la mosaïque **Contrats** dans le panneau du compte d’intégration pour afficher le nouveau contrat ajouté.  
![](./media/logic-apps-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>En savoir plus
* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")  




<!--HONumber=Feb17_HO1-->


