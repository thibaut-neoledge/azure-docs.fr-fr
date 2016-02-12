<properties 
	pageTitle="Créer une solution IoT à l’aide de Stream Analytics | Microsoft Azure" 
	description="prise en main du didacticiel de la solution iot Stream Analytics d’un scénario de station de péage"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"
/>

# Créer une solution IoT à l’aide de Stream Analytics #

## Introduction ##

Dans ce didacticiel, vous allez apprendre à obtenir des informations en temps réel de vos données à l’aide d’Azure Stream Analytics. Grâce au service de traitement de flux d’Azure, les développeurs peuvent facilement traiter l’espace de données en mouvement en combinant les flux de données tels que les flux de clics, les journaux et les événements générés par les appareils avec les enregistrements historiques ou les données de référence pour dégager des perspectives rapidement et facilement. En tant que service de calcul de flux en temps réel entièrement géré hébergé dans Microsoft Azure, Azure Stream Analytics offre la résilience, la faible latence et l’extensibilité nécessaires pour être opérationnel en quelques minutes.

Après avoir effectué ce didacticiel, vous pourrez :

-   vous familiariser avec le portail Azure Stream Analytics ;
-   configurer et déployer un travail de diffusion en continu ;
-   exprimer des problèmes concrets et les résoudre à l’aide du langage de requête Stream Analytics ;
-   développer des solutions de diffusion en continu pour vos clients à l’aide d’Azure Stream Analytics en toute confiance ;
-   vous appuyer sur l’expérience de surveillance et de journalisation pour résoudre les problèmes.

## Configuration requise ##

Ce didacticiel nécessite les éléments suivants :

-   Dernière version d’[Azure PowerShell](../install-configure-powershell.md)
-   Visual Studio 2015 ou la version gratuite de [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Abonnement Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Des privilèges d’administrateur sur l’ordinateur
-   Dernière version de TollApp.zip téléchargeable à partir de [GitHub](https://github.com/streamanalytics/samples/releases)

## Présentation du scénario - « Hello, Toll ! » ##


Les stations de péage sont courantes ; nous les rencontrons sur de nombreuses autoroutes, ponts et tunnels dans le monde entier. Chaque station de péage dispose de plusieurs guichets qui peuvent être manuels (vous effectuez votre paiement auprès d’un employé) ou automatisés (un capteur installé sur le poste analyse une carte RFID apposée au pare-brise de votre véhicule quand vous franchissez le péage). Il est facile de visualiser le franchissement de ces stations de péage par les véhicules sous la forme d’un flux d’événements sur lequel des opérations intéressantes peuvent être effectuées.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## Données entrantes ##

Nous allons utiliser deux flux de données générés par des capteurs installés à l’entrée et à la sortie des stations de péage et un jeu de données statique comportant les données d’inscription des véhicules.

### Flux des données d’entrée ###

Le flux des données d’entrée contient des informations sur les véhicules qui arrivent aux stations de péage.
  
  
| TollId | EntryTime | LicensePlate | State | Make | Model | VehicleType | VehicleWeight | Toll | Tag |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 2014-09-10 12:01:00.000 | JNB 7001 | NY | Honda | CRV | 1 | 0 | 7 | |
| 1 | 2014-09-10 12:02:00.000 | YXZ 1001 | NY | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 2014-09-10 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 2014-09-10 12:03:00.000 | XYZ 1003 | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 2014-09-10 12:03:00.000 | BNJ 1007 | NY | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 2014-09-10 12:05:00.000 | CDE 1007 | NJ | Toyota | 4x4 | 1 | 0 | 6 | 321987654 |
  

Voici une brève description des colonnes :
  
  
| TollID | Identifiant de la station de péage |
|--------------|----------------------------------------------------------------|
| EntryTime | Date et heure UTC auxquelles le véhicule arrive à la station de péage |
| LicensePlate | Numéro de plaque d’immatriculation du véhicule |
| State | État des États-Unis |
| Make | Fabricant du véhicule |
| Model | Numéro de modèle du véhicule |
| VehicleType | 1 pour véhicule de tourisme, 2 pour véhicule commercial |
| WeightType | Poids du véhicule en tonnes ; 0 pour les véhicules de tourisme |
| Toll | Prix du péage en dollar américain |
| Tag | Balise électronique sur la voiture qui automatise le paiement (vide si le paiement a été effectué manuellement) |


### Flux des données de sortie

Le flux des données de sortie contient des informations sur les véhicules qui quittent la station de péage.
  
  
| **TollId** | **ExitTime** | **LicensePlate** |
|------------|------------------------------|------------------|
| 1 | 2014-09-10T12:03:00.0000000Z | JNB 7001 |
| 1 | 2014-09-10T12:03:00.0000000Z | YXZ 1001 |
| 3 | 2014-09-10T12:04:00.0000000Z | ABC 1004 |
| 2 | 2014-09-10T12:07:00.0000000Z | XYZ 1003 |
| 1 | 2014-09-10T12:08:00.0000000Z | BNJ 1007 |
| 2 | 2014-09-10T12:07:00.0000000Z | CDE 1007 |

Voici une brève description des colonnes :
  
  
| des colonnes | Description |
|--------------|-----------------------------------------------------------------|
| TollID | Identifiant de la station de péage |
| ExitTime | Date et heure UTC auxquelles le véhicule quitte la station de péage |
| LicensePlate | Numéro de plaque d’immatriculation du véhicule |

###Données d’inscription des véhicules commerciaux

Nous allons utiliser un instantané de la base de données d’inscription des véhicules commerciaux.
  
  
| LicensePlate | RegistrationId | Expired |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |                      

Voici une brève description des colonnes :
  
  
| des colonnes | Description |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Numéro de plaque d’immatriculation du véhicule |
| RegistrationId | ID d’inscription |
| Expired | 0 si l’inscription du véhicule est active, 1 si elle a expiré. |


## Configuration de l’environnement pour Azure Stream Analytics

Pour effectuer ce didacticiel, vous devez disposer d’un abonnement Microsoft Azure. Microsoft propose un essai gratuit des services Microsoft Azure, comme décrit ci-après.

Si vous ne disposez pas d’un compte Azure, vous pouvez demander un essai gratuit en accédant à <http://azure.microsoft.com/pricing/free-trial/>.

Remarque : pour vous inscrire pour un essai gratuit, vous devez disposer d’un appareil mobile pouvant recevoir des messages texte et d’une carte de crédit valide.

Veillez à suivre les étapes de la section « Nettoyer votre compte Azure » à la fin de cet exercice pour tirer pleinement parti de votre crédit Azure gratuit de 200 $.

## Approvisionnement des ressources Azure nécessaires pour le didacticiel

Ce didacticiel nécessite 2 hubs d’événements Azure pour la réception des flux de données d’entrée et de sortie. Nous allons utiliser Azure SQL Database pour enregistrer les résultats des travaux Stream Analytics. En outre, nous allons utiliser Azure Storage pour stocker les données de référence relatives à l’inscription des véhicules.

Le script Setup.ps1 dans le dossier TollApp sur GitHub permet de créer toutes les ressources nécessaires. Pour gagner du temps, nous vous recommandons de l’exécuter. Pour plus d’informations sur la configuration de ces ressources dans le portail Azure, consultez l’annexe « Configuration des ressources du didacticiel dans le portail Azure ».

Téléchargez et enregistrez les dossier et fichiers [TollApp](https://github.com/streamanalytics/samples/releases) de prise en charge. Assurez-vous que vous téléchargez la dernière version.

Ouvrez une fenêtre « Microsoft Azure PowerShell » **en tant qu’administrateur**. Si vous ne disposez toujours pas d’Azure PowerShell, installez-le en suivant les instructions de la page [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md).

Windows bloque automatiquement les fichiers ps1, dll et exe téléchargés depuis Internet. Nous devons définir la stratégie d’exécution avant d’exécuter le script. Assurez-vous que la fenêtre Azure PowerShell s’exécute en tant qu’administrateur. Exécutez « Set-ExecutionPolicy unrestricted ». Quand vous y êtes invité, tapez « O ».

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Exécutez Get-ExecutionPolicy pour vous assurer que la commande a fonctionné.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Accédez au répertoire comportant les scripts et l’application du générateur.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Tapez « .\\Setup.ps1 » pour configurer votre compte Azure, créer et configurer toutes les ressources nécessaires et commencer à générer des événements. Le script choisit au hasard une région pour créer vos ressources. Si vous souhaitez spécifier la région vous-même, vous pouvez passer le paramètre -location, comme dans l’exemple ci-dessous :

**.\\Setup.ps1 -location "Central US"**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Le script ouvre la page de connexion à Microsoft Azure. Entrez les informations d’identification de votre compte.

Si votre compte a accès à plusieurs abonnements, vous devez entrer le nom de l’abonnement à utiliser pour le didacticiel.

L’exécution du script peut prendre plusieurs minutes. Une fois le script terminé, la sortie doit ressembler à la capture d’écran ci-dessous.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Vous obtenez également une autre fenêtre, similaire à la capture d’écran ci-dessous. Cette application envoie des événements à votre hub d’événements et est nécessaire pour exécuter les exercices du didacticiel. Ainsi, n’arrêtez pas l’application ou ne fermez pas cette fenêtre avant d’avoir terminé le didacticiel.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Normalement, toutes les ressources créées doivent maintenant apparaître dans le Portail de gestion Azure. Accédez à <https://manage.windowsazure.com> et connectez-vous avec les informations d’identification de votre compte.

### Hubs d'événements

Cliquez sur l’élément de menu « Service Bus » sur le côté gauche du Portail de gestion Azure pour voir les hubs d’événements créés par le script de la section précédente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Tous les espaces de noms disponibles apparaissent dans votre abonnement. Cliquez sur celui commençant par « TollData » (TollData4637388511 dans notre exemple). Cliquez sur l’onglet « Hubs d’événements ».

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Deux hubs d’événements nommés *entry* et *exit* apparaissent dans cet espace de noms.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Conteneur Azure Storage

Cliquez sur l’élément de menu « Stockage » sur le côté gauche du Portail de gestion Azure pour voir le conteneur de stockage utilisé dans le laboratoire.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

Cliquez sur celui commençant par « tolldata » (tolldata4637388511 dans notre exemple). Ouvrez l’onglet « Conteneurs » pour voir le conteneur créé.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

Cliquez sur le conteneur « tolldata » pour voir le fichier JSON chargé qui contient les données d’inscription des véhicules.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Base de données SQL Azure

Cliquez sur l’élément de menu « Bases de données SQL » sur le côté gauche du Portail de gestion Azure pour afficher la base de données SQL Azure à utiliser dans le laboratoire.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

Cliquez sur « TollDataDB ».

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

Copiez le nom du serveur sans le numéro de port (*nom\_serveur*.database.windows.net, par exemple).

##Se connecter à la base de données à partir de Visual Studio

Nous allons utiliser Visual Studio pour accéder aux résultats de requête dans la base de données de sortie.

Connectez-vous à la base de données Azure (destination) à partir de Visual Studio :

1) Ouvrez Visual Studio et cliquez sur « Outils », puis sur l’élément de menu « Se connecter à la base de données...».

2) Si vous y êtes invité, sélectionnez « Microsoft SQL Server » comme source de données.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) Dans le champ Nom du serveur, collez le nom du serveur SQL copié dans la section précédente à partir du portail Azure (c’est-à-dire *nom\_serveur*.database.windows.net).

4) dans le champ Authentification, choisissez Authentification SQL Server.

5) Entre le nom de connexion « tolladmin » et le mot de passe de connexion « 123toll! ».

6) Choisissez TollDataDB comme base de données.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
    
7) Cliquez sur OK.

8) Ouvrez l’Explorateur de serveurs.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
  
9) Observez les 4 tables créées dans la base de données TollDataDB.
  
![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)
  
##Générateur d’événements - Exemple de projet TollApp

Le script PowerShell démarre automatiquement l’envoi d’événements à l’aide de l’exemple de programme d’application TollApp. Vous n’avez pas besoin d’effectuer d’étapes supplémentaires.

Toutefois, si vous êtes intéressé par les détails de l’implémentation, le code source de l’application TollApp est disponible à la page [samples/TollApp](https://github.com/streamanalytics/samples/tree/master/TollApp) de GitHub.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##Créer un travail Stream Analytics

Dans le portail Azure, ouvrez Stream Analytics et cliquez sur « Nouveau » dans le coin inférieur gauche de la page pour créer un travail d’analyse.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

Cliquez sur « Création rapide ». Sélectionnez la région dans laquelle vos autres ressources sont créées par le script.

Pour le paramètre « Compte de stockage de surveillance régionale », sélectionnez « Créer un nouveau compte de stockage » et donnez un nom unique à ce compte. Azure Stream Analytics utilisera ce compte pour stocker les informations de surveillance de tous vos travaux futurs.

Cliquez sur « Créer une tâche Stream Analytics » en bas de la page.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

##Définir les sources d’entrée

Cliquez sur le travail d’analyse créé dans le portail.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

Ouvrez l’onglet « Entrées » pour définir les données sources.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

Cliquez sur « Ajouter une entrée ».

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

Sélectionnez « Flux de données » dans la première page.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

Sélectionnez « Hub d’événements » dans la deuxième page de l’Assistant.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

Entrez « EntryStream » comme alias d’entrée.

Cliquez sur la liste déroulante « Hub d’événements » et sélectionnez celui qui commence par « TollData » (par exemple, TollData9518658221).

Sélectionnez « entry » comme nom de hub d’événements et « tout » comme nom de stratégie de hub d’événements.

Vos paramètres ressemblent à ceci :

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

Accédez à la page suivante. Sélectionnez le format JSON et le codage UTF8.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

Cliquez sur OK en bas de la boîte de dialogue pour terminer l’Assistant.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

Vous devez suivre la même séquence d’étapes pour créer la deuxième entrée de hub d’événements pour le flux des événements de sortie. Dans la 3e page, veillez à entrer les valeurs indiquées dans la capture d’écran ci-dessous.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

Vous disposez maintenant de deux flux d’entrée définis :

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

À présent, nous allons ajouter une entrée de données de référence pour le fichier blob des données d’inscription des véhicules.

Cliquez sur « Ajouter une entrée ». Sélectionnez « Données de référence ».

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

Dans la page suivante, sélectionnez le compte de stockage commençant par « tolldata ». Le nom de conteneur doit être « tolldata », tandis que le nom d’objet blob sous Modèle de chemin d’accès doit être « registration.json ». Ce nom de fichier respecte la casse et doit être tout en minuscules.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

Sélectionnez les valeurs comme indiqué ci-dessous dans la page suivante, puis cliquez sur OK pour terminer l’Assistant.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Désormais, toutes les entrées sont définies.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

##Définir la sortie

Accédez à l’onglet « Sortie », puis cliquez sur « Ajouter une sortie ».

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

Choisissez « Base de données SQL ».

Sélectionnez le nom de serveur utilisé dans « Se connecter à la base de données à partir de Visual Studio ». Le nom de la base de données doit être TollDataDB.

Entrez « tolladmin » comme nom d’utilisateur et « 123toll! » comme mot de passe. Le nom de la table doit être défini sur « TollDataRefJoin »

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

##Requête Azure Stream Analytics

L’onglet Requête contient une requête SQL qui exécute la transformation sur les données entrantes.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

À travers ce didacticiel, nous allons tenter de répondre à plusieurs questions liées aux données de péage et de construire des requêtes Stream Analytics qui peuvent être utilisées dans Azure Stream Analytics pour fournir une réponse pertinente.

Avant de commencer notre premier travail Azure Stream Analytics, examinons quelques scénarios et la syntaxe de requête.

##Présentation du langage de requête d’Azure Stream Analytics
-----------------------------------------------------

Supposons que nous devons compter le nombre de véhicules qui arrivent à une station de péage. Dans la mesure où il s’agit d’un flux continu d’événements, il est essentiel de définir une « période ». Nous devons donc modifier notre question en « Nombre de véhicules qui arrivent à une station de péage toutes les 3 minutes », communément appelé « nombre bascule ».

Examinons la requête d’Azure Stream Analytics qui répond à cette question :

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Comme vous pouvez le voir, Azure Stream Analytics utilise un langage de requête semblable à SQL avec quelques extensions supplémentaires pour spécifier les aspects de la requête liés au temps.

Pour plus de détails, vous pouvez consulter les constructions de [gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et de [fenêtrage](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilisées dans la requête à partir de MSDN.

##Test des requêtes Azure Stream Analytics

Notre première requête Azure Stream Analytics étant écrite, nous pouvons la tester à l’aide des exemples de fichiers de données situés dans votre dossier TollApp dans le chemin ci-dessous :

**..\\TollApp\\TollApp\\Data**

Ce dossier contient les fichiers suivants :

1.  Entry.json

2.  Exit.json

3.  Registration.json

## Question 1 : nombre de véhicules arrivant à une station de péage

Ouvrez le portail de gestion Azure et accédez à votre travail Azure Stream Analytics. Ouvrez l’onglet Requête, puis copiez et collez la requête à partir de la section précédente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

Pour valider cette requête sur les exemples de données, cliquez sur le bouton Tester. Dans la boîte de dialogue qui s’affiche, accédez à Entry.json, fichier contenant des exemples de données issues du flux d’événements EntryTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

Valider que la sortie de la requête est conforme au résultat attendu :

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

##Question 2 : indiquer le temps total que met chaque véhicule pour franchir la station de péage

Afin d’évaluer l’efficacité et l’expérience client, nous souhaitons déterminer la durée moyenne que doit mettre un véhicule pour franchir le péage.

Pour cela, nous devons joindre le flux contenant EntryTime au flux contenant ExitTime. Nous joignons les flux sur les colonnes TollId et LicencePlate. L’opérateur JOIN nécessite de spécifier une marge de manœuvre temporelle décrivant la différence de temps acceptable entre les événements joints. Nous allons utiliser la fonction DATEDIFF pour spécifier que les événements ne doivent pas être séparés de plus de 15 minutes. Nous allons également appliquer la fonction DATEDIFF aux heures de sortie et d’entrée pour calculer le temps réel qu’un véhicule passe au péage. Notez que la fonction DATEDIFF n’est pas utilisée de la même façon dans une instruction SELECT et dans une condition JOIN.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

Pour tester cette requête, mettez à jour la requête sous l’onglet Requête de votre travail :

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

Cliquez sur Tester et spécifier des exemples de fichiers d’entrée pour EntryTime et ExitTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

Cochez la case pour tester la requête et afficher le résultat :

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

##Question 3 : indiquer tous les véhicules commerciaux dont l’inscription est arrivé à expiration

Azure Stream Analytics peut utiliser des instantanés de données pour joindre des flux de données temporelles. Pour illustrer cette fonctionnalité, nous allons utiliser l’exemple de question suivant.

Si un véhicule commercial est inscrit auprès de l’entreprise de péage, il franchit directement la station de péage. Nous allons utiliser la table de recherche d’inscription des véhicules commerciaux pour identifier tous les véhicules commerciaux dont l’inscription a expiré.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Notez que le test d’une requête avec des données de référence nécessite la définition d’une source d’entrée pour ces données, ce que nous avons fait à l’étape 5.

Pour tester cette requête, collez la requête sous l’onglet Requête, cliquez sur Tester, puis spécifiez les 2 sources d’entrée :

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

Observez le résultat de la requête :

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

##Démarrer un travail Stream Analytics


Notre première requête Azure Stream Analytics étant écrite, nous pouvons terminer la configuration et démarrer le travail. Enregistrez la requête répondant à la question 3, dont le résultat correspond au schéma de la table de sortie **TollDataRefJoin**.

Accédez au travail Tableau de bord, puis cliquez sur Démarrer.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Dans la boîte de dialogue qui s’affiche, définissez l’heure Démarrer la sortie sur Heure personnalisée. Reculez l’heure d’une unité. Ainsi, nous sommes sûrs de traiter tous les événements du hub d’événements depuis que nous avons commencé à générer les événements au début du didacticiel. À présent, cochez la case pour démarrer le travail.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Le démarrage du travail peut prendre quelques minutes. L’état du travail apparaît dans la page de niveau supérieur de Stream Analytics.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

##Vérifier les résultats dans Visual Studio

Ouvrez l’Explorateur de serveurs Visual Studio et cliquez avec le bouton droit sur la table TollDataRefJoin. Sélectionnez « Afficher les données de la table » pour voir le résultat de votre travail.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

##Mise à l’échelle des travaux Azure Stream Analytics

Azure Stream Analytics est conçu pour pouvoir être mis à l’échelle en toute souplesse et gérer une charge de données élevée. La requête d’Azure Stream Analytics peut utiliser une clause **PARTITION BY** pour informer le système que cette étape doit faire l’objet d’une mise à l’échelle. PartitionId est une colonne spéciale ajoutée par le système qui correspond à l’id de partition de l’entrée (hub d’événements).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId    

Arrêtez le travail en cours, mettez à jour la requête sous l’onglet Requête, puis ouvrez l’onglet Mettre à l’échelle.

Les unités de diffusion en continu définissent la quantité de puissance de calcul allouable au travail.

Placez le curseur à la position 6.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

Accédez à l’onglet Sortie et modifiez le nom de la table SQL en « TollDataTumblingCountPartitioned ».

À présent, si vous démarrez le travail, Azure Stream Analytics peut répartir le travail sur plusieurs ressources de calcul et atteindre un meilleur débit. Notez que l’application TollApp envoie également des événements partitionnés par TollId.

##Analyse

L’onglet Surveillance contient des statistiques sur le travail en cours d’exécution.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Vous pouvez accéder aux journaux des opérations à partir de l’onglet Tableau de bord.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Pour afficher des informations supplémentaires sur un événement particulier, sélectionnez l’événement et cliquez sur « Détails ».

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

##Conclusion

Dans ce didacticiel, nous avons présenté le service Azure Stream Analytics. Nous avons expliqué comment configurer les entrées et les sorties du travail Stream Analytics. À l’aide du scénario sur les données de péage, nous avons expliqué les types courants de problèmes qui surviennent dans l’espace de données en mouvement et comment les résoudre avec de simples requêtes de type SQL dans Azure Stream Analytics. Nous avons décrit des constructions d’extension SQL en vue d’une utilisation avec des données temporelles. Nous avons montré comment joindre des flux de données et comment enrichir le flux de données avec des données de référence statiques. Nous avons expliqué comment mettre à l’échelle une requête pour obtenir un débit plus élevé.

Bien qu’il constitue une bonne présentation, ce didacticiel n’est pas pour autant complet. D’autres modèles de requête utilisant le langage SAQL sont disponibles [ici](stream-analytics-stream-analytics-query-patterns.md). Reportez-vous à la [documentation en ligne](https://azure.microsoft.com/documentation/services/stream-analytics/) pour en savoir plus sur Azure Stream Analytics.

##Nettoyer votre compte Azure

Arrêtez le travail Stream Analytics à partir du portail Azure.

Le script Setup.ps1 crée 2 hubs d’événements Azure et le serveur de base de données SQL Azure. Les instructions suivantes vous aideront à nettoyer les ressources à la fin du didacticiel.

Dans la fenêtre PowerShell, tapez « .\\Cleanup.ps1 » pour démarrer le script qui supprime les ressources utilisées dans le didacticiel.

Notez que les ressources sont identifiées par leur nom. Assurez-vous de vérifier attentivement chaque élément avant d’en confirmer la suppression.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0204_2016-->