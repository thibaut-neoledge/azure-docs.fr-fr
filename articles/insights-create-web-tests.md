<properties 
	pageTitle="Création de tests web" 
	description="Découvrez comment créer des tests web dans Azure." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-17" 
	ms.author="awills"/>

# Tests web pour les sites web Microsoft Azure
Votre site web Azure fonctionne-t-il encore ? Répond-il correctement et rapidement ? Testez-le régulièrement en configurant un test web. Si le site ne fonctionne plus ou répond lentement ou de façon incorrecte, vous recevez une alerte par courrier électronique, accompagnée de graphiques illustrant sa disponibilité et sa réactivité dans le temps.  

*Vous souhaitez tester d'autres sites web ? Utilisez la disponibilité des [tests web dans Application Insights][] pour les applications web non-Azure.*

![Browse Hub](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

Vous pouvez configurer la surveillance de la disponibilité des sites web Azure qui utilisent un plan De base ou Standard.  Vous pouvez créer jusqu'à 3 tests Web et les exécuter chacun à partir de 3 régions géographiques sans modifier le site web.

Vous pouvez interrompre les tests web pendant les déploiements ou les coupures prévues pour ne pas affecter la disponibilité globale.  Cette dernière est calculée sur l'ensemble des tests web dans les différentes régions sélectionnées.

## Configuration d'un test Web
1. Pour configurer un test web, vérifiez d'abord que le site web est dans l'un des modes **De base** ou **Standard**.
2. Dans le panneau **Site web**, choisissez ensuite **Test web** :  
    ![Configure Web Tests](./media/insights-create-web-tests/Insights_ConfigurePart.png)
3. Dans le panneau **Créer un test web**, donnez un nom au test et spécifiez l'URL sur laquelle réexécuter le test.  
    ![Create Web Test](./media/insights-create-web-tests/Insights_CreateTest.png)
4. Choisissez 3 emplacements au maximum sur les 8.
5. Spécifiez les critères de réussite, y compris les vérifications des codes d'état HTTP ou les contenus de chaîne sur le site.
6. Choisissez les paramètres d'alerte, y compris la sensibilité et le destinataire des courriers électroniques.  
    ![Alerts](./media/insights-create-web-tests/Inisghts_AlertCreation.png)
    - Une sensibilité élevée crée une alerte chaque fois que l'échec d'un test est détecté dans un emplacement.
    - Pour une sensibilité moyenne, au moins la moitié des emplacements doit avoir connu un échec en 10 minutes.
    - Pour une sensibilité faible, le test de tous les emplacements doit avoir échoué dans les 15 minutes.

Lorsque vous avez terminé, cliquez sur le bouton **Créer**. Une fois créé, le test web s'exécute toutes les 5 minutes dans les emplacements spécifiés, ce qui explique pourquoi les données peuvent mettre un certain temps avant de s'afficher.

### À quoi servent les emplacements ?
Nous envoyons une demande au site web à partir de ces emplacements, de la même façon que les utilisateurs accèdent au site de différentes parties du monde. Si votre site est indisponible aux États-Unis, mais disponible en Europe, vous savez que le problème vient du réseau plutôt que de votre serveur.

### Utilisation des critères de réussite
En général, vous devez tester que le code d'état HTTP est égal à 200, ce qui signale que le serveur reconnaît l'URI et a renvoyé une page.

Vous ne pouvez pas utiliser de caractères génériques dans la chaîne de correspondance de contenu, mais vous pouvez tester du texte brut.

## Mon site ne fonctionne pas
Si votre test web ne répond pas aux critères de réussite, il sera marqué comme ayant échoué et la disponibilité globale de votre site web sera limitée. Les tests qui ont échoué (comme ceux qui ont réussi) sont affichés dans une fenêtre de discussion de type Scatter Chat sur le panneau du test web.  

![Failed Test](./media/insights-create-web-tests/Insights_FailedWebTest.png)

Les tests ayant échoué peuvent être analysés pour déterminer la cause de l'échec.  Explorez un test web ayant échoué, puis téléchargez et ouvrez le fichier de résultat du test web Visual Studio pour analyser et comprendre les causes de l'échec.

![Open in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)

[availability]: ../app-insights-monitor-web-app-availability/

<!--HONumber=46--> 
