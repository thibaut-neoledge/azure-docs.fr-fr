---
title: "Résoudre les problèmes liés aux stratégies personnalisées grâce à Application Insights - Azure AD B2C | Documents Microsoft"
description: "configuration d’Application Insights pour suivre l’exécution de stratégies personnalisées"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeedakhter-msft
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ad31e5f4ef3be78d8d2dd6b9c7d83e447d9ef776
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C : collecte des journaux

Cet article explique comment collecter les journaux à partir d’Azure AD B2C afin que vous puissiez diagnostiquer les problèmes liés à vos stratégies personnalisées.

## <a name="use-application-insights"></a>Utiliser Application Insights

Azure AD B2C prend en charge une fonctionnalité d’envoi de données à Application Insights.  Application Insights permet de diagnostiquer les exceptions et de visualiser les problèmes de performances des applications.

### <a name="setup-application-insights"></a>Configurer Application Insights

1. Accédez au [portail Azure](https://portal.azure.com). Vérifiez que vous êtes sur le locataire avec votre abonnement Azure (pas votre locataire Azure AD B2C).
1. Cliquez sur **+ Nouveau** dans le menu de navigation de gauche.
1. Recherchez et sélectionnez **Application Insights**, puis cliquez sur **Créer**.
1. Remplissez le formulaire et cliquez sur **Créer**. Sélectionnez **Général** comme **Type d’application**.
1. Une fois que la ressource a été créée, ouvrez la ressource Application Insights.
1. Recherchez **Propriétés** dans le menu de gauche, puis cliquez dessus.
1. Copiez la **Clé d’instrumentation** et enregistrez-la pour la section suivante.

### <a name="set-up-the-custom-policy"></a>Configurer la stratégie personnalisée

1. Ouvrez le fichier RP (par exemple, SignUpOrSignin.xml).
1. Ajoutez les attributs suivants à l’élément `<TrustFrameworkPolicy>` :

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. S’il n’existe pas déjà, ajoutez un nœud enfant `<UserJourneyBehaviors>` au nœud `<RelyingParty>`. Il doit être placé immédiatement après le `<DefaultUserJourney ReferenceId="YourPolicyName" />`
2. Ajoutez le nœud suivant en tant qu’enfant de l’élément `<UserJourneyBehaviors>`. Veillez à remplacer `{Your Application Insights Key}` par la **Clé d’instrumentation** que vous avez obtenue à partir d’Application Insights dans la section précédente.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` indique à ApplicationInsights d’accélérer l’envoi de la télémétrie via le pipeline de traitement, valable pour le développement, mais limité à des volumes élevés.
  * `ClientEnabled="true"` envoie le script côté client ApplicationInsights pour l’affichage de la page de suivi et les erreurs côté client (pas nécessaire).
  * `ServerEnabled="true"` envoie le JSON UserJourneyRecorder existant en tant qu’événement personnalisé à Application Insights.
  Le fichier XML final doit ressembler à ce qui suit :

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Téléchargez la stratégie.

### <a name="see-the-logs-in-application-insights"></a>Afficher des journaux dans Application Insights

>[!NOTE]
> Il y a un court délai (moins de 5 minutes) avant de pouvoir afficher les nouveaux journaux dans Application Insights.

1. Ouvrez la ressource Application Insights que vous avez créée sur le [portail Azure](https://portal.azure.com).
1. Dans le menu **Aperçu**, cliquez sur **Analytics**.
1. Ouvrez un nouvel onglet dans Application Insights.
1. Voici une liste de requêtes que vous pouvez utiliser pour afficher les journaux

| Requête | Description |
|---------------------|--------------------|
traces | Consultez tous les journaux générés par Azure AD B2C |
traces \| where timestamp > ago(1d) | Consultez tous les journaux générés par Azure AD B2C pour le dernier jour

Les entrées peuvent être longues.  Exporter au format CSV pour une étude plus approfondie.

Pour plus d’informations sur l’outil Analytics, cliquez [ici](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>La communauté a développé une visionneuse userjourney pour aider les développeurs d’identité.  Elle n’est pas prise en charge par Microsoft et est mise à disposition tel quel.  Elle effectue une lecture à partir de votre instance d’Application Insights et fournit une vue bien structurée des événements userjourney.  Vous obtenez le code source et le déployez dans votre propre solution.

[Référentiel Github pour exemples de stratégies personnalisées non pris en charge et outils connexes](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)





