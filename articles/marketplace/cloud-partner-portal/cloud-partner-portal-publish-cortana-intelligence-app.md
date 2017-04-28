---
title: "Vous publiez une application Cortana Intelligence sur AppSource ? | Microsoft Docs"
description: "Guide pas à pas pour la publication d’une application Cortana Intelligence ou Microsoft R Services sur AppSource"
services: marketplace
documentationcenter: 
author: hiavi
manager: judym
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: abathula
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 62aa3148406068f06d1fbdaf556ff2d526c0e17b
ms.lasthandoff: 04/13/2017


---
# <a name="publish-a-cortana-intelligence-app-to-appsource"></a>Vous publiez une application Cortana Intelligence sur AppSource ?
Guide pas à pas pour la publication d’une application Cortana Intelligence ou Microsoft R Services sur AppSource

## <a name="1-introduction"></a>1. Introduction

Merci de votre intérêt pour la publication de votre application Cortana Intelligence ou Microsoft R Solutions sur Microsoft AppSource. Microsoft est heureux de travailler avec ses partenaires dédiés (ISV/SI) pour fournir le premier emplacement permettant aux clients, aux revendeurs et aux partenaires d’implémentation d’essayer des solutions d’entreprise et de tirer le meilleur parti de leurs investissements. Ce guide vous aide à chacune des étapes menant à la publication de votre solution.

## <a name="2-getting-started"></a>2. Prise en main

Suivez les instructions

1.  [Guide de démarrage pour la présentation avec Microsoft](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/CESIPartnerJourneyOverview_1232017.pdf) pour être référencé comme un partenaire d’analytique avancée.

2.  Sur AppSource, en complétant le formulaire [Indiquer une application](https://appsource.microsoft.com/partners/list-an-app).
    - En réponse à la question *« Choisissez les produits pour lesquels votre application est conçue* », activez la case à cocher **Autres** et sélectionnez « Intelligence Cortana » dans le contrôle d’édition.

## <a name="3-solution-evaluation-criteria"></a>3. Critères d’évaluation de la solution

Voici la liste des critères que l’application doit remplir

1.  L’application doit résoudre un problème de cas métier spécifique au sein d’un domaine fonctionnel donné d’une manière reproductible. Avec des configurations/personnalisations minimales, elle doit pouvoir fournir une proposition de valeur prédéfinie sur une courte période de temps.

2.  La solution doit utiliser au moins l’un des composants d’analyse suivants

    1. HDInsight
    2. Apprentissage automatique
    3. Data Lake Analytics
    4. Stream Analytics
    5. Cognitive Services
    6. Bot Framework
    7. Microsoft R Server autonome ou services R sur SQL 2016/HDInsight Premium

3.  La solution doit générer au moins \$1 Ko/mois/client à l’aide de programmes Partenaire de référence (POR) numérique ou Fournisseur de solutions cloud (CSP).

4.  La solution doit utiliser l’authentification unique fédérée Azure Active Directory (SSO fédérée AAD) avec le consentement activé pour l’authentification des utilisateurs et les contrôles d’accès aux ressources. Si votre application n’est pas encore activée, voir

    1. [Prise en main](https://identity.microsoft.com/)
    2. [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)

5.  Utilisez Power BI dans votre solution et partagez le fichier Power BI Desktop (PBIX) : cela est facultatif mais fortement recommandé, car il est prouvé que cela génère un plus grand nombre de prospects

    1. Vérifiez que toutes les données sont importées dans le fichier PBIX
    2. Pas de données référencées en externe : nous créons le rapport incorporé pour vous.

## <a name="4-decide-on-the-type-of-offer-you-would-like-to-publish-on-appsource"></a>4. Choix du type d’offre que vous voulez publier sur AppSource

AppSource permet aux partenaires d’offrir des deux types d’expériences d’évaluation aux observateurs
1.  Évaluations conduites par le client, où les clients peuvent essayer l’application par eux-mêmes
    1.  Essai gratuit
        1.  AppSource dirige l’utilisateur vers une URL que vous (partenaire) fournissez, qui mène l’utilisateur au travers de la SSO fédérée AAD et offre une expérience d’évaluation limitée dans le temps.
        2.  Le but est ici que votre application soit une application SaaS prenant en charge de la mutualisation pour isoler les données/la configuration d’un utilisateur de celles des autres OU que l’expérience d’évaluation n’offre qu’une expérience de sous-ensemble nécessitant uniquement des opérations en lecture seule.

        Exemple : voir [AFS POP commerciale exécution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview) (Exécution de vente au point d’achat AFS), [AvePoint prestations](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) (Prestations AvePoint) (Cette application fournit une expérience organisée avec des orientations claires pour l’ensemble sélectionné d’utilisateurs), etc.
    2.  Version d’évaluation
        1.  Votre solution (de partenaire) ou un sous-ensemble de celle-ci peuvent être empaquetés à l’aide de modèles Azure Resource Manager qu’AppSource peut instancier et gérer dans un abonnement Azure de partenaire avec limitation dans le temps et maintien de pool d’instances à chaud/froid, etc.
        2.  Nous pouvons fournir des modèles et des exemples de code pour vous aider si vous décidez de vous engager dans cette voie.
        
        Exemple : voir [Optimisation des stocks](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) de Neal Analytics.

2.    Évaluations conduites pas un partenaire, qui requièrent que les clients remplissent un formulaire d’informations de contact afin que le partenaire puisse effectuer le suivi et donner une démonstration/évaluation etc.

Pour une vue d’ensemble globale, regardez la vidéo présentant la [procédure pas à pas d’expérience d’évaluation d’AppSource](http://aka.ms/trialexperienceforwebapps).

Les données montrent clairement que les évaluations conduites par les clients ont un potentiel élevé de génération de prospects par rapport aux évaluations conduites par un partenaire. Pensez-y. Qui aime remplir des formulaires ?😊

Décidez du type d’offre que vous voulez ajouter à AppSource.

## <a name="5-getting-approved-to-use-azure-marketplace-publishing-portal"></a>5. Obtention de l’approbation nécessaire pour utiliser le portail de publication de la Place de marché Microsoft Azure

Désormais, les ID d’e-mail AAD/MSA doivent être approuvés avant que vous puissiez accéder au portail de publication de la Place de marché Microsoft Azure pour commencer à publier. Merci de fournir les détails suivants à [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)

-   Nom complet de l’éditeur : &lt;nom de l’entreprise tel qu’il apparaît sur AppSource en tant qu’éditeur de l’application&gt;

-   Nom unique de l’éditeur : &lt;nom unique comprenant uniquement des caractères minuscules et des tirets « - »&gt;

-   Site web : &lt;site web de l’entreprise&gt;

-   E-mail du propriétaire : &lt;ID d’e-mail AAD/MSA&gt;

-   E-mail du contributeur2 : &lt;ID d’e-mail AAD/MSA&gt;

 Ajoutez d’autres contributeurs si nécessaire.

## <a name="6-get-info-about-your-companys-lead-management"></a>6. Obtention d’informations sur la gestion des prospects de votre société

Lorsque des visiteurs manifestent de l’intérêt pour des solutions, ces prospects sont transmis directement aux systèmes de gestion des prospects du partenaire. AppSource prend en charge plusieurs types de systèmes de gestion des prospect (Dynamics CRM, Salesforce, Marketo, etc.).

Pour plus de détails sur l’ensemble du processus et la manière de trouver les informations appropriées pour configurer des prospects d’AppSource, voir [Gestion des prospects de Marketing AppSource](./cloud-partner-portal-get-customer-leads.md).    

## <a name="7-publish-your-app-on-the-publishing-portal"></a>7. Publier votre application sur le portail de publication

Si vous avez besoin d’informations sur des champs que nous avons ignorés, envoyez un e-mail à <appsourcecissupport@microsoft.com>

Procédez comme suit

1.  À l’aide du navigateur Google Chrome, accédez au [portail Microsoft Cloud Partner](https://cloudpartner.azure.com)

2.  Connectez-vous à l’aide votre ID d’e-mail AAD/MSA autorisé

3.  Sélectionnez Nouvelle offre -&gt; Cortana Intelligence

    ![publishaaapp1][1]
4.  Remplissez les détails des Paramètres de l’offre

    ![publishaaapp2][2]

    1. L’ID de l’offre est utilisé pour identifier votre application sur AppSource dans l’URL AppSource unique. Exemple : « appsource-saas-app ». Il sera visible aux utilisateurs dans des emplacements tels que les modèles Azure Resource Manager et les états de facturation. Notez que les informations d’ID d’offre et d’éditeur ne peuvent pas être modifiées une fois enregistrées.

    2. Pour Nom de l’offre, utilisez le nom de votre solution. N’ajoutez pas le nom de votre société, car il est ajouté automatiquement sous le nom de la solution

5.  Remplissez la section Info technique. La plupart de celles-ci sont explicites et possèdent des info-bulles contenant des informations supplémentaires.

    1.  Les vidéos de démonstration doivent être des enregistrements modifiés le moins possible des fonctionnalités de la solution réelle, mettant en évidence l’authentification des utilisateurs à l’aide d’AAD, les principaux aspects des fonctionnalités auxquelles les utilisateurs ont accès, et l’intégration avec la plateforme Cortana Intelligence.  Les vidéos de démonstration **ne sont pas** disponibles publiquement pour les clients, mais doivent refléter la façon dont la solution *s’afficherait* à un client potentiel.  Par conséquent, elles doivent être scriptées et reproductibles.

        Pour préparer vos vidéos de démonstration, vous pouvez vous servir de n’importe quel outil d’enregistrement d’écran capable d’exporter un format vidéo standard (par exemple, MPEG). Voici les instructions si vous avez Skype Entreprise

        1.  [Commencer une réunion](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
        2. [Partager votre bureau](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
        3. [Commencer l’enregistrement](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
        4. À la fin de l’enregistrement, [utiliser le gestionnaire d’enregistrements pour publier votre enregistrement](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

        Vous devez charger votre vidéo enregistrée sur un service qui vous permet de générer une URL partagée.  Par exemple, vous pouvez utiliser un [lien Invité dans OneDrive/Sharepoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232). 

    2.  Pour obtenir des instructions concernant le chargement d’un diagramme d’architecture de solution, voir le [modèle de flux de travail de solution analytique avancée](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)

6.  Remplissez la section Détails de la vitrine

    1.  Remarque : les champs « Description de l’offre » et « Conditions d’utilisation » peuvent recevoir du contenu HTML. Vous pouvez utiliser n’importe quel éditeur HTML en ligne pour ajuster votre contenu HTML jusqu’à obtenir le résultat voulu. La description de l’offre doit fournir très clairement et brièvement un argument de vente expliquant la solution qu’elle apporte, le retour sur investissement que le client peut obtenir et toute garantie associée.

    2.  Type d’application : en fonction de la section précédente intitulée « Choix du type d’offre que vous voulez publier sur AppSource », sélectionnez l’option
        1.  « Gratuit » si vous souhaitez offrir aux clients une expérience entièrement gratuite sans limitation dans le temps.
        2.  « Essai » si vous souhaitez offrir aux clients une expérience d’essai/de version d’évaluation limitée dans le temps.
        3.  « Demander un essai » si vous souhaitez offrir aux clients une expérience d’évaluation conduite par un partenaire.

    3.  Options Masquer la clé pour tester votre offre en version intermédiaire : cette option permet de créer une URL d’aperçu pour votre application une fois celle-ci en version intermédiaire. Il ne s’agit pas d’un mot de passe. N’hésitez à faire simple. Exemple : « AppSourceHideKey »

    4.  Les images chargées doivent être des images lisibles de haute qualité pour que la publication de l’offre soit approuvée.

    5.  Étude de cas : fournissez un étude de cas/un récit avec les détails suivants

        1.  Implémentation chez un client
        2. Définition claire du problème rencontré par des clients réels ou anonymes
        3. Mesures prises pour résoudre le problème
        4. Référencez les éléments de configuration/composants d’analyse avancée Microsoft qui vous ont aidé à résoudre le problème.
        5.  Bénéfice net ou retour sur investissement associés à la solution
        6. *À éviter* : brochure ou vidéo de marketing comprenant simplement une explication de la plateforme globale sans la configuration requise ci-dessus.

    6.  Fortement recommandé car cela génère plus de prospects : vidéo avec la même configuration requise que l’étude de cas à afficher sur AppSource. L’URL de la vidéo peut être celle d’une vidéo accessible sur Youtube ou Viemo. Assurez-vous que les vidéos sont dans l’un des formats suivants.
        1. https://vimeo.com/########## ou
        2. https://www.youtube.com/watch?v=##########

7.  Remplissez la section Contacts : elle sert à envoyer des notifications d’utilisation, des alertes de la Place de marché, etc.

8.  Publiez votre offre. Vous recevrez des notifications par e-mail à mesure que la solution progressera dans le processus de certification. Vous pouvez voir les détails de la progression sous l’onglet « État » de l’interface utilisateur de publication de l’offre.

    1.  Vos solutions sont d’abord publiées en version intermédiaire.
    2.  Vous pouvez vérifier votre contenu intermédiaire en cliquant sur le lien AppSource que recevez par e-mail.
    3.  Apportez les modifications requises, puis soumettez à nouveau. Lorsque vous êtes satisfait de contenu final, mettez l’application en production.
    4.  À ce stade, nous validons les métadonnées de votre application, puis approuvons sa publication sur AppSource ou vous adressons un motif de rejet si nous la refusons.

[1]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp1.png
[2]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp2.png    

