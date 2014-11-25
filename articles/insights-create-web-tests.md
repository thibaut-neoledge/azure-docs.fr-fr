<properties title="How to create web test" pageTitle="How to create web test" description="Learn how to create web tests in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills" />

# Tests Web

Votre site web Azure fonctionne-t-il encore ? Répond-il correctement et rapidement ? Testez-le régulièrement en configurant un test web. Si le site ne fonctionne plus ou répond lentement ou de façon incorrecte, vous recevez une alerte par courrier électronique, accompagnée de graphiques illustrant sa disponibilité et sa réactivité dans le temps.

![Parcourir le Hub][Parcourir le Hub]

Vous pouvez configurer la surveillance de la disponibilité des sites Web Azure qui utilisent un plan Basique ou Standard. Vous pouvez créer jusqu'à 3 tests Web et les exécuter chacun à partir de 3 régions géographiques sans modifier le site Web.

Vous pouvez interrompre les tests Web pendant les déploiements ou les coupures prévues pour ne pas affecter la disponibilité globale. Cette dernière est calculée sur l'ensemble des tests Web dans les différentes régions sélectionnées.

## Configuration d'un test Web

1.  Pour configurer un test Web, vérifiez que le site Web est dans l'un des modes **Basique** ou **Standard**.
2.  Dans le volet **Web site**, choisissez **Web test** :  
    ![Configurer les tests Web][Configurer les tests Web]
3.  Dans le volet **Create Web test**, donnez un nom au test et spécifiez l'URL soumise au test.  
    ![Créer un test Web][Créer un test Web]
4.  Choisissez 3 emplacements au maximum sur les 8.
5.  Spécifiez les critères de réussite, y compris les vérifications des codes d'état HTTP ou les contenus de chaîne sur le site.
6.  Choisissez les paramètres d'alerte, y compris la sensibilité et le destinataire des courriers électroniques.  
    ![Alertes][Alertes]
    - Une sensibilité élevée crée une alerte chaque fois que l'échec d'un test est détecté dans un emplacement.
    - Pour une sensibilité moyenne, au moins la moitié des emplacements doit avoir connu un échec en 10 minutes.
    - Pour une sensibilité faible, le test de tous les emplacements doit avoir échoué dans les 15 minutes.

Lorsque vous avez terminé, cliquez sur le bouton **Create**. Une fois créé, le test Web s'exécute toutes les 5 minutes dans les emplacements spécifiés, ce qui explique pourquoi les données peuvent mettre un certain temps avant de s'afficher.

### À quoi servent les emplacements ?

Nous envoyons une demande au site Web à partir de ces emplacements, de la même façon que les utilisateurs accèdent au site de différentes parties du monde. Si votre site est indisponible aux États-Unis, mais disponible en Europe, vous savez que le problème vient du réseau plutôt que de votre serveur.

### Utilisation des critères de réussite

En général, vous devez tester que le code d'état HTTP est égal à 200, ce qui signale que le serveur reconnaît l'URI et a renvoyé une page.

Vous ne pouvez pas utiliser de caractères génériques dans la chaîne de correspondance de contenu, mais vous pouvez tester du texte brut.

## Mon site ne fonctionne pas

Si votre test Web ne répond pas aux critères de réussite, il sera marqué comme ayant échoué et la disponibilité globale de votre site Web sera limitée. Les tests qui ont échoué (comme ceux qui ont réussi) sont affichés dans une fenêtre de discussion de type Scatter Chat sur le volet du test Web.

![Test ayant échoué][Test ayant échoué]

Les tests ayant échoué peuvent être analysés pour déterminer la cause de l'échec. Explorez un test Web ayant échoué, puis téléchargez et ouvrez le fichier de résultat du test Web Visual Studio pour analyser et comprendre les causes de l'échec.

![Ouverture dans VS][Ouverture dans VS]

  [Parcourir le Hub]: ./media/insights-create-web-tests/Inisghts_WebTestBlade.png
  [Configurer les tests Web]: ./media/insights-create-web-tests/Insights_ConfigurePart.png
  [Créer un test Web]: ./media/insights-create-web-tests/Insights_CreateTest.png
  [Alertes]: ./media/insights-create-web-tests/Inisghts_AlertCreation.png
  [Test ayant échoué]: ./media/insights-create-web-tests/Insights_FailedWebTest.png
  [Ouverture dans VS]: ./media/insights-create-web-tests/Insights_OpenInVS.png
