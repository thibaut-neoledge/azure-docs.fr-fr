---
title: "Intégration d’entreprise avec EDIFACT | Microsoft Docs"
description: "Découvrez comment utiliser les contrats EDIFACT pour créer des applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: cgronlun
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2016
ms.author: jonfan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 30c26d1fb2dfb7c458789e70f4f8d1a6c39cd0ca


---
# <a name="enterprise-integration-with-edifact"></a>Intégration d'entreprise avec EDIFACT
> [!NOTE]
> Cette page traite des fonctionnalités EDIFACT de Logic Apps. Pour plus d’informations, consultez [X12](logic-apps-enterprise-integration-x12.md).
> 
> 

## <a name="create-an-edifact-agreement"></a>Créer un contrat EDIFACT
Avant de pouvoir échanger des messages EDIFACT, vous devez créer un contrat EDIFACT et le stocker dans votre compte d’intégration. Les étapes suivantes vous guideront lors du processus de création d’un contrat EDIFACT.

### <a name="heres-what-you-need-before-you-get-started"></a>Voici ce dont vous avez besoin pour commencer
* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) défini dans votre abonnement Azure  
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration  

> [!NOTE]
> Lorsque vous créez un contrat, le contenu des messages que vous échangerez avec le partenaire doit correspondre au type de contrat.    
> 
> 

Une fois que vous avez [créé un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) et [ajouté des partenaires](logic-apps-enterprise-integration-partners.md), vous pouvez créer un contrat EDIFACT en suivant ces étapes :  

### <a name="from-the-azure-portal-home-page"></a>À partir de la page d’accueil du portail Azure
Une fois que vous êtes connecté au [portail Azure](http://portal.azure.com "portail Azure"):  

1. Sélectionnez **Parcourir** dans le menu à gauche.  

> [!TIP]
> Si le lien **Parcourir** ne s’affiche pas, vous devrez peut-être développer le menu. Pour ce faire, sélectionnez le lien **Afficher le menu** situé dans le coin supérieur gauche du menu réduit.  
> 
> 

![](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)    

1. Tapez *intégration* dans la zone de recherche de filtre, puis sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats.       
   ![](./media/logic-apps-enterprise-integration-edifact/edifact-1-3.png)    
2. Dans le panneau **Comptes d’intégration** qui s’affiche, sélectionnez le compte d’intégration dans lequel vous allez créer le contrat. Si aucun compte d’intégration ne s’affiche, [créez-en un](../logic-apps/logic-apps-enterprise-integration-accounts.md "All about integration accounts").  
   ![](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)  
3. Sélectionnez la mosaïque **Contrats** . Si vous ne la voyez pas, commencez par l’ajouter.   
   ![](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)     
4. Sélectionnez le bouton **Ajouter** dans le panneau Contrats qui s’affiche.  
   ![](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)  
5. Entrez un **nom** pour votre contrat, puis sélectionnez le **type de contrat** EDIFACT, le **partenaire hôte**, **l’identité de l’hôte**, le **partenaire invité**, **l’identité de l’invité**, dans le panneau Contrats qui s’affiche.  
   ![](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)  
6. Une fois les propriétés du contrat définies, sélectionnez les **Paramètres de réception** pour configurer la façon dont les messages reçus dans le cadre de ce contrat doivent être traités.  
7. Le contrôle Paramètres de réception est divisé en plusieurs sections : identificateurs, accusé de réception, schémas, numéros de contrôle, validations, paramètres internes et traitement par lots. Configurez ces propriétés selon le contrat conclu avec le partenaire avec lequel vous échangerez des messages. Voici un aperçu de ces contrôles ; configurez-les en fonction de la manière dont ce contrat doit identifier et gérer les messages entrants :   
   ![](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  
8. Sélectionnez le bouton **OK** pour enregistrer vos paramètres.  

### <a name="identifiers"></a>Identificateurs
| Propriété | Description |
| --- | --- |
| UNB6.1 (mot de passe de référence du destinataire) |Entrez une valeur alphanumérique comprise entre 1 et 14 caractères. |
| UNB6.2 (qualificateur de référence du destinataire) |Entrez une valeur numérique contenant deux caractères maximum. |

### <a name="acknowledgments"></a>Remerciements
| Propriété | Description |
| --- | --- |
| Réception des messages (CONTRL) |Activez cette case à cocher pour renvoyer un accusé de réception technique (CONTRL) à l’expéditeur. Cet accusé de réception est envoyé à l’expéditeur en fonction des paramètres d’envoi du contrat. |
| Accusé de réception (CONTRL) |Cochez cette case pour renvoyer un accusé de réception (CONTRL) fonctionnel à l’expéditeur. L’accusé de réception est envoyé à l’expéditeur en fonction des paramètres d’envoi du contrat. |

### <a name="schemas"></a>Schémas
| Propriété | Description |
| --- | --- |
| UNH2.1 (TYPE) |Sélectionnez un type de document informatisé. |
| UNH2.2 (VERSION) |Entrez le numéro de version du message. (un caractère minimum ;&3; caractères maximum). |
| UNH2.3 (VERSION FINALE) |Entrez le numéro de version finale du message. (un caractère minimum ;&3; caractères maximum). |
| UNH2.5 (CODE AFFECTÉ ASSOCIÉ) |Entrez le code affecté. (6 caractères maximum. Doit contenir des valeurs alphanumériques). |
| UNG2.1 (ID DE L’EXPÉDITEUR D’APPLICATION) |Entrez une valeur numérique comprenant entre un et 35 caractères. |
| UNG2.2 (QUALIFICATEUR DE L’EXPÉDITEUR D’APPLICATION) |Entrez une valeur alphanumérique comprenant quatre caractères maximum. |
| SCHÉMA |Sélectionnez le schéma que vous avez déjà téléchargé et que vous souhaitez utiliser à partir de votre compte d’intégration associé. |

### <a name="control-numbers"></a>Numéros de contrôle
| Propriété | Description |
| --- | --- |
| Interdire les doublons de numéro de contrôle d’échange |Cochez cette case pour bloquer les échanges en double. Si cette option est sélectionnée, l’action de décodage EDIFACT vérifie que le numéro de contrôle d’échange (UNB5) pour l’échange reçu ne correspond pas à un numéro de contrôle déjà traité. Si une correspondance est détectée, l’échange n’est pas traité. |
| Check for duplicate UNB5 every (days) (Vérifier les doublons UNB5 tous les (jours)) |Si vous avez choisi de ne pas autoriser les numéros de contrôle d’échange en double, vous pouvez spécifier le nombre de jours après lequel le contrôle est effectué en définissant la valeur appropriée dans le champ **Check for duplicate UNB5 every (days)** (Vérifier les doublons UNB5 tous les (jours)). |
| Interdire les numéros de contrôle de groupe en double |Cochez cette case pour bloquer les échanges avec des numéros de contrôle de groupe en double (UNG5). |
| Interdire les numéros de contrôle de document informatisé en double |Cochez cette case pour bloquer les échanges avec des numéros de contrôle de document informatisé en double (UNH1). |
| Numéro de contrôle d’accusé de réception EDIFACT |Pour désigner les numéros de référence de document informatisé à utiliser dans un accusé de réception, entrez une valeur de préfixe, une plage de numéros de référence et un suffixe. |

### <a name="validations"></a>Validations
| Propriété | Description |
| --- | --- |
| Type de message |Spécifiez le type de message. Une nouvelle ligne de validation est automatiquement ajoutée dès que la ligne précédente est terminée. Si aucune règle n’est spécifiée, la ligne marquée comme ligne par défaut est utilisée pour la validation. |
| Validation EDI |Cochez cette case pour effectuer une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin. |
| Validation étendue |Cochez cette case pour permettre la validation étendue (XSD) des échanges reçus de l’expéditeur. Cela inclut la validation de la longueur de champ, du caractère facultatif et du nombre de répétitions en plus de la validation du type de données XSD. |
| Autoriser les zéros de début ou de fin |Sélectionnez **Autoriser** pour autoriser les zéros de début ou de fin, **Non autorisé** pour ne pas autoriser les zéros de début ou de fin, ou **Supprimer** pour supprimer les zéros de début et de fin. |
| Supprimer les zéros de début ou de fin |Cochez cette case pour supprimer les zéros de début ou de fin |
| Stratégie de séparateur de fin |Sélectionnez **Non autorisé** si vous ne souhaitez pas autoriser les délimiteurs et les séparateurs dans un échange reçu de l’expéditeur. Si l’échange contient des délimiteurs et des séparateurs, il est déclaré non valide. Sélectionnez **Facultatif** pour accepter les échanges avec ou sans délimiteurs et séparateurs. Sélectionnez **Obligatoire** si l’échange reçu doit contenir des délimiteurs et des séparateurs. |

### <a name="internal-settings"></a>Paramètres internes
| Propriété | Description |
| --- | --- |
| Créer des balises XML vides si les séparateurs de fin sont autorisés |Cochez cette case afin d’inclure des balises XML vides pour les séparateurs de l’expéditeur. |
| Processus de traitement par lot entrant |Options disponibles :</br></br>**Fractionner l’échange en documents informatisés - suspendre les documents informatisés en cas d’erreur**: analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée au document informatisé. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, seuls ces documents informatisés seront suspendus. Fractionner l’échange en documents informatisés - suspendre l’échange en cas d’erreur : analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, la totalité de l’échange sera suspendue.</br></br>**Préserver l’échange - suspendre les documents informatisés en cas d’erreur**: laisse l’échange intact, en créant un document XML pour l’ensemble de l’échange par lot. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, seuls ces documents informatisés seront suspendus et tous les autres documents informatisés seront traités.</br></br>**Préserver l’échange - suspendre l’échange en cas d’erreur**: laisse l’échange intact, en créant un document XML pour l’ensemble de l’échange par lot. Avec cette option, si la validation d’un ou plusieurs documents informatisés de l’échange échoue, la totalité de l’échange sera suspendue. |

Votre contrat est prêt à traiter les messages entrants qui sont conformes aux paramètres que vous avez sélectionnés.

Pour configurer les paramètres qui gèrent les messages que vous envoyez aux partenaires :  

1. Sélectionnez **Paramètres d’envoi** pour configurer la façon dont les messages envoyés dans le cadre de ce contrat doivent être traités.  

Le contrôle Paramètres d’envoi est divisé en plusieurs sections : identificateurs, accusé de réception, schémas, enveloppes, jeux de caractères et séparateurs, numéros de contrôle et validation. 

Voici une vue de ces contrôles. Effectuez les sélections selon la façon dont vous souhaitez gérer les messages envoyés aux partenaires via ce contrat :    
![](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

1. Sélectionnez le bouton **OK** pour enregistrer vos paramètres.  

### <a name="identifiers"></a>Identificateurs
| Propriété | Description |
| --- | --- |
| UNB1.2 (version de la syntaxe) |Sélectionnez une valeur comprise entre **1** et **4**. |
| UNB2.3 (adresse de routage inverse de l’expéditeur) |Entrez une valeur numérique comprenant entre un et 14 caractères. |
| UNB3.3 (adresse de routage inverse du destinataire) |Entrez une valeur numérique comprenant entre un et 14 caractères. |
| UNB6.1 (mot de passe de référence du destinataire) |Entrez une valeur numérique comprenant entre un et 14 caractères. |
| UNB6.2 (qualificateur de référence du destinataire) |Entrez une valeur numérique contenant deux caractères maximum. |
| UNB7 (identifiant de référence de l’application) |Entrez une valeur numérique comprenant entre un et 14 caractères |

### <a name="acknowledgment"></a>Accusé de réception
| Propriété | Description |
| --- | --- |
| Réception des messages (CONTRL) |Activez cette case à cocher si le partenaire hébergé s’attend à recevoir un accusé de réception (CONTRL) technique. Ce paramètre spécifie que le partenaire hébergé qui envoie le message demande un accusé de réception de la part du partenaire invité. |
| Accusé de réception (CONTRL) |Activez cette case à cocher si le partenaire hébergé s’attend à recevoir un accusé de réception (CONTRL) fonctionnel. Ce paramètre spécifie que le partenaire hébergé qui envoie le message demande un accusé de réception de la part du partenaire invité. |
| Générer une boucle SG1/SG4 pour les documents informatisés acceptés |Si vous choisissez de demander un accusé de réception fonctionnel, activez cette case à cocher pour forcer la génération de boucles SG1/SG4 dans les accusés de réception CONTRL fonctionnels pour les documents informatisés acceptés. |

### <a name="schemas"></a>Schémas
| Propriété | Description |
| --- | --- |
| UNH2.1 (TYPE) |Sélectionnez un type de document informatisé. |
| UNH2.2 (VERSION) |Entrez le numéro de version du message. |
| UNH2.3 (VERSION FINALE) |Entrez le numéro de version finale du message. |
| SCHÉMA |Sélectionnez le schéma à utiliser. Les schémas se trouvent dans votre compte d’intégration. Pour accéder à vos schémas, vous devez tout d’abord lier votre compte d’intégration à votre application logique. |

### <a name="envelopes"></a>Enveloppes
| Propriété | Description |
| --- | --- |
| UNB8 (code de priorité de traitement) |Entrez une valeur alphabétique contenant plus d’un caractère. |
| UNB10 (contrat de communication) |Entrez une valeur numérique comprenant entre un et 40 caractères. |
| UNB11 (indicateur de test) |Activez cette case à cocher pour indiquer que l’échange généré contient des données de test |
| Appliquer le segment UNA (conseil de service de chaîne) |Activez cette case à cocher pour générer un segment UNA pour l’échange à envoyer. |
| Appliquer des segments UNG (En-tête de groupe fonctionnel) |Activez cette case à cocher pour créer des segments de regroupement dans l’en-tête de groupe fonctionnel dans les messages envoyés au partenaire invité. Les valeurs suivantes sont utilisées pour créer les segments UNG :</br></br>Pour **UNG1**, entrez une valeur alphanumérique comportant un caractère minimum et six caractères maximum.</br></br>Pour **UNG2.1**, entrez une valeur alphanumérique comportant un caractère minimum et 35 caractères maximum.</br></br>Pour **UNG2.2**, entrez une valeur alphanumérique comprenant quatre caractères maximum.</br></br>Pour **UNG3.1**, entrez une valeur alphanumérique comportant un caractère minimum et 35 caractères maximum.</br></br>Pour **UNG3.2**, entrez une valeur alphanumérique comprenant quatre caractères maximum.</br></br>Pour **UNG6**, entrez une valeur alphanumérique comportant un caractère minimum et trois caractères maximum.</br></br>Pour **UNG7.1**, entrez une valeur alphanumérique comportant un caractère minimum et trois caractères maximum.</br></br>Pour **UNG7.2**, entrez une valeur alphanumérique comportant un caractère minimum et trois caractères maximum.</br></br>Pour **UNG7.3**, entrez une valeur alphanumérique comportant un caractère minimum et six caractères maximum.</br></br>Pour **UNG8**, entrez une valeur alphanumérique comportant un caractère minimum et 14 caractères maximum. |

### <a name="character-sets-and-separators"></a>Jeux de caractères et séparateurs
outre le jeu de caractères, vous pouvez entrer un autre ensemble de délimiteurs à utiliser pour chaque type de message. Si un jeu de caractères n’est pas spécifié pour un schéma de message donné, le jeu de caractères par défaut est utilisé.

| Propriété | Description |
| --- | --- |
| UNB1.1 (identificateur système) |Sélectionnez le jeu de caractères EDIFACT à appliquer à l’échange sortant. |
| SCHÉMA |Dans la liste déroulante, sélectionnez un schéma. Une nouvelle ligne sera ajoutée à la fin de chaque ligne. Pour le schéma sélectionné, sélectionnez les séparateurs à utiliser :</br></br>**Séparateur d’éléments composites** : entrez un caractère unique pour séparer les éléments de données composites.</br></br>**Séparateur d’éléments de données** : entrez un caractère unique pour séparer les éléments de données simples au sein des éléments de données composites.</br></br></br></br>**Caractère de remplacement** : cochez cette case si les données de charge utile contiennent des caractères également utilisés comme séparateurs de composant, de données ou de segment. Vous pouvez ensuite entrer un caractère de remplacement. Lors de la génération du message X12 sortant, toutes les instances des caractères de séparation dans les données de charge utile sont remplacées par le caractère spécifié.</br></br>**Terminateur de segment** : entrez un seul caractère pour indiquer la fin d’un segment EDI.</br></br>**Suffixee** : sélectionnez le caractère utilisé avec l’identificateur de segment. Si vous désignez un suffixe, l’élément de données de terminateur de segment peut être vide. Si le terminateur de segment est laissé vide, vous devez désigner un suffixe. |

### <a name="control-numbers"></a>Numéros de contrôle
| Propriété | Description |
| --- | --- |
| UNB5 (Numéro de contrôle de l’échange) |Entrez un préfixe, une plage de valeurs pour le numéro de contrôle de l’échange, ainsi qu’un suffixe. Ces valeurs sont utilisées pour générer un échange sortant. Le préfixe et le suffixe sont facultatifs ; le numéro de contrôle est obligatoire. Le numéro de contrôle est incrémenté pour chaque nouveau message ; le préfixe et le suffixe restent les mêmes. |
| UNG5 (Numéro de contrôle de groupe) |Entrez un préfixe, une plage de valeurs pour le numéro de contrôle de l’échange, ainsi qu’un suffixe. Ces valeurs sont utilisées pour générer le numéro de contrôle du groupe. Le préfixe et le suffixe sont facultatifs ; le numéro de contrôle est obligatoire. Le numéro de contrôle est incrémenté pour chaque nouveau message jusqu’à ce que la valeur maximale soit atteinte ; le préfixe et le suffixe restent les mêmes. |
| UNH1 (Numéro de référence de l’en-tête de message) |Entrez un préfixe, une plage de valeurs pour le numéro de contrôle de l’échange, ainsi qu’un suffixe. Ces valeurs sont utilisées pour générer le numéro de référence de l’en-tête de message. Le préfixe et le suffixe sont facultatifs ; le numéro de référence est obligatoire. Le numéro de référence est incrémenté pour chaque nouveau message ; le préfixe et le suffixe restent les mêmes. |

### <a name="validations"></a>Validations
| Propriété | Description |
| --- | --- |
| Type de message |Cette option active la validation sur le récepteur d’échange. Cette validation réalise une validation EDI sur des éléments de données de document informatisé, une validation des types de données, des restrictions de longueur, des éléments de données vides et des séparateurs de fin. |
| Validation EDI |Cochez cette case pour effectuer une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin. |
| Validation étendue |Cette option permet la validation étendue des échanges reçus de l’expéditeur. Cela inclut la validation de la longueur de champ, du caractère facultatif et du nombre de répétitions en plus de la validation du type de données XSD. Vous pouvez activer la validation étendue sans activer la validation EDI, ou vice versa. |
| Autoriser les zéros de début ou de fin |Cette option indique que la validation d’un échange EDI reçu d’un tiers n’échoue pas si un élément de données d’un échange EDI n’est pas conforme à sa spécification de longueur en raison d’espaces de fin, mais est conforme à la restriction de longueur lorsque ces espaces sont supprimés. |
| Supprimer les zéros de début ou de fin |Cette option permet de supprimer les zéros de début et de fin. |
| Séparateur de fin |Cette option indique que la validation d’un échange EDI reçu d’un tiers n’échoue pas si un élément de données d’un échange EDI n’est pas conforme à sa spécification de longueur en raison de zéros de début (ou de fin) ou d’espaces de fin, mais est conforme à la restriction de longueur lorsqu’ils sont supprimés.</br></br>Sélectionnez **Non autorisé** si vous ne souhaitez pas autoriser les délimiteurs et les séparateurs dans un échange reçu de l’expéditeur. Si l’échange contient des délimiteurs et des séparateurs, il est déclaré non valide.</br></br>Sélectionnez **Facultatif** pour accepter les échanges avec ou sans délimiteurs et séparateurs.</br></br>Sélectionnez **Obligatoire** si l’échange reçu doit contenir des délimiteurs et des séparateurs. |

Après avoir cliqué sur **OK** dans le panneau ouvert :  

1. Sélectionnez la mosaïque **Contrats** dans le panneau du compte d’intégration pour afficher le nouveau contrat ajouté.  
   ![](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="learn-more"></a>En savoir plus
* [En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")  




<!--HONumber=Jan17_HO3-->


