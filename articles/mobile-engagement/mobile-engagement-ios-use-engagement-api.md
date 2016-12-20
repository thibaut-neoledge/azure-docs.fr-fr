---
title: Utilisation de l&quot;API Engagement sur iOS
description: "Kit de développement logiciel (SDK) iOS le plus récent - Utilisation de l&quot;API Engagement sur iOS"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf672384407588ddc2c4998f42f6893e2638c592


---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Utilisation de l'API Engagement sur iOS
Ce document est un module complémentaire du document Intégration d'Engagement sur iOS : il fournit des informations détaillées sur l'utilisation de l'API Engagement pour signaler les statistiques de votre application.

Rappelez-vous que si vous souhaitez qu’Engagement établisse uniquement le rapport des sessions, des activités, des incidents et des informations techniques de votre application, dans ce cas le moyen le plus simple est de faire `UIViewController`hériter tous vos objets personnalisés de la classe `EngagementViewController` correspondante.

Si vous souhaitez aller plus loin, par exemple si vous avez besoin de signaler des événements, des erreurs et des tâches spécifiques à l'application, ou si vous devez signaler les activités de votre application d'une autre manière que celle implémentée dans les classes `EngagementViewController`, vous devez alors utiliser l'API Engagement.

L'API Engagement est fournie par la classe `EngagementAgent` . Une instance de cette classe peut être récupérée en appelant la méthode statique `[EngagementAgent shared]` (notez que l'objet `EngagementAgent` retourné est un singleton).

Avant les appels d'API, l'objet `EngagementAgent` doit être initialisé en appelant la méthode `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Concepts liés à Engagement
Les sections qui suivent affinent les [concepts Mobile Engagement](mobile-engagement-concepts.md) courants pour la plateforme iOS.

### <a name="session-and-activity"></a>`Session` et `Activity`
Une *activité* est généralement associée à un écran de l’application, c’est-à-dire que *l’activité* démarre lorsque l’écran s’affiche et s’arrête lorsque l’écran est fermé. C’est le cas lorsque le Kit de développement logiciel (SDK) Engagement est intégré à l’aide des classes `EngagementViewController`.

Mais les *activités* peuvent également être contrôlées manuellement à l'aide de l'API Engagement. Cela permet de fractionner un écran donné en plusieurs sous-parties pour obtenir plus d'informations sur l'utilisation de cet écran (par exemple la fréquence et la durée d'utilisation des boîtes de dialogue dans cet écran).

## <a name="reporting-activities"></a>Rapports d'activités
### <a name="user-starts-a-new-activity"></a>L'utilisateur démarre une nouvelle activité
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Vous devez appeler `startActivity()` chaque fois que l'activité utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

### <a name="user-ends-his-current-activity"></a>L'utilisateur met fin à l'activité en cours
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> Vous ne devez **JAMAIS** appeler cette fonction vous-même, sauf si vous souhaitez fractionner une utilisation de votre application dans plusieurs sessions : un appel de cette fonction mettrait immédiatement fin à la session en cours, et par conséquent un appel ultérieur à `startActivity()` démarrerait une nouvelle session. Cette fonction est appelée automatiquement par le SDK lorsque votre application est fermée.
> 
> 

## <a name="reporting-events"></a>Rapports d'événements
### <a name="session-events"></a>Événements de session
Les événements de session servent généralement à signaler les actions effectuées par un utilisateur lors de sa session.

**Exemple sans données supplémentaires :**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Exemple avec des données supplémentaires :**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Événements autonomes
Contrairement aux événements de session, les événements d'autonomes peuvent être utilisés en dehors du contexte d'une session.

**Exemple :**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Rapports d'erreurs
### <a name="session-errors"></a>Erreurs de session
Les erreurs de session servent généralement à signaler les erreurs affectant l'utilisateur lors de sa session.

**Exemple :**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Erreurs autonomes
Contrairement aux erreurs de session, les erreurs autonomes peuvent être utilisées en dehors du contexte d'une session.

**Exemple :**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Rapports de travaux
**Exemple :**

Supposons que vous voulez créer un rapport de la durée de votre processus de connexion :

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Signaler les erreurs lors d'un travail
Les erreurs peuvent être associées à un travail en cours d'exécution plutôt qu'à la session utilisateur en cours.

**Exemple :**

Supposons que vous souhaitez signaler une erreur pendant le processus de connexion :

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Événements lors d'un travail
Les événements peuvent être associés à un travail en cours d'exécution au lieu de se rapporter à la session utilisateur en cours.

**Exemple :**

Supposons que nous disposons d'un réseau social et que nous utilisons une tâche pour signaler la durée totale pendant laquelle l'utilisateur est connecté au serveur. L'utilisateur peut recevoir des messages de ses amis : il s'agit d'un événement de tâche.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Paramètres supplémentaires
Des données arbitraires peuvent être associées à des événements, des erreurs, des activités et des tâches.

Ces données peuvent être structurées, elles utilisent la classe NSDictionary d'iOS.

Notez que les données supplémentaires peuvent contenir des `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` ou d'autres instances `NSDictionary`.

> [!NOTE]
> Le paramètre supplémentaire est sérialisé au format JSON. Si vous souhaitez transmettre des objets différents de ceux décrits ci-dessus, vous devez implémenter la méthode suivante dans votre classe :
> 
> -(NSString*)JSONRepresentation;
> 
> La méthode doit retourner une représentation JSON de votre objet.
> 
> 

### <a name="example"></a>Exemple
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limites
#### <a name="keys"></a>de clés symétriques
Chaque clé dans `NSDictionary` doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille
Les données supplémentaires sont limitées à **1024** caractères par appel (une fois codées au format JSON par l'agent Engagement).

Dans l'exemple précédent, le JSON envoyé au serveur compte 58 caractères :

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Rapports d'informations sur l'application
Vous pouvez signaler manuellement les informations de suivi (ou toutes autres informations spécifiques aux applications) à l'aide de la fonction `sendAppInfo:` .

Notez que ces informations peuvent être envoyées de façon incrémentielle : seule la dernière valeur d'une clé donnée sera conservée pour un périphérique donné.

Comme c'est le cas avec les suppléments d'événement, la classe `NSDictionary` est utilisée pour extraire des informations de l'application. Notez que les tableaux ou sous-dictionnaires seront traités en tant que chaînes plates (à l'aide de la sérialisation JSON).

**Exemple :**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limites
#### <a name="keys"></a>de clés symétriques
Chaque clé dans `NSDictionary` doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille
Les informations sur l'application sont limitées à **1024** caractères par appel (une fois codées au format JSON par l'agent Engagement).

Dans l'exemple précédent, le JSON envoyé au serveur fait 44 caractères :

    {"birthdate":"1983-12-07","gender":"female"}



<!--HONumber=Nov16_HO3-->


