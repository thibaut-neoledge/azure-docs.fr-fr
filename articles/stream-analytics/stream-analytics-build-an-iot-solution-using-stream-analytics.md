<properties
    pageTitle="Créer une solution IoT à l’aide de Stream Analytics | Microsoft Azure"
    description="Prise en main du didacticiel pour la solution IoT Stream Analytics d’un scénario de station de péage"
    keywords="solution IOT, fonctions de fenêtre"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Créer une solution IoT à l’aide de Stream Analytics

## <a name="introduction"></a>Introduction

Ce didacticiel explique comment utiliser Azure Stream Analytics pour obtenir des informations en temps réel à partir de vos données. Les développeurs peuvent aisément combiner des flux de données, tels que des flux de clics, des journaux et des événements générés par des appareils, avec des enregistrements d’historique ou des données de référence pour recueillir des perspectives. En tant que service de calcul de flux en temps réel entièrement géré hébergé dans Microsoft Azure, Azure Stream Analytics offre la résilience, la faible latence et la scalabilité nécessaires pour être opérationnel en quelques minutes.

Après avoir effectué ce didacticiel, vous pourrez :

-   vous familiariser avec le portail Azure Stream Analytics ;
-   configurer et déployer un travail de diffusion en continu ;
-   exposer des problèmes concrets et les résoudre à l’aide du langage de requête Stream Analytics ;
-   développer des solutions de streaming pour vos clients à l’aide de Stream Analytics en toute confiance ;
-   vous appuyer sur l’expérience de surveillance et de journalisation pour résoudre les problèmes.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel, vous avez besoin des composants suivants :

-   La version la plus récente d’ [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 ou la version gratuite de [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Des privilèges d’administrateur sur l’ordinateur
-   Téléchargement de [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) à partir du Centre de téléchargement Microsoft
-   Facultatif : Code source du générateur d’événements TollApp dans [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction:-“hello,-toll!”"></a>Présentation du scénario - « Hello, Péage ! »


Une gare de péage est un dispositif très répandu. Il s’en trouve sur de nombreux autoroutes, ponts et tunnels dans le monde entier. Chaque station de péage compte plusieurs guichets. Aux guichets manuels, vous vous arrêtez pour régler le montant du péage à un guichetier. Aux guichets automatisés, un capteur scanne une carte RFID apposée sur le pare-brise de votre véhicule lorsque vous franchissez le péage. Il est facile de visualiser le franchissement de ces stations de péage par les véhicules sous la forme d’un flux d’événements sur lequel des opérations intéressantes peuvent être effectuées.

![Image de voitures à des postes de péage](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Données entrantes

Ce didacticiel fonctionne avec deux flux de données. Des capteurs installés à l’entrée et à la sortie des stations de péage produisent le premier flux. Le deuxième flux de données est un jeu de données de recherche statique contenant les données d’inscription des véhicules.

### <a name="entry-data-stream"></a>Flux des données d’entrée

Le flux des données d’entrée contient les informations relatives aux véhicules qui arrivent aux stations de péage.


| TollID | EntryTime               | LicensePlate | State | Make   | Modèle   | VehicleType | VehicleWeight | Toll | Tag       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 2014-09-10 12:01:00.000 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 2014-09-10 12:02:00.000 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 2014-09-10 12:02:00.000 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 2014-09-10 12:03:00.000 | XYZ 1003     | CT    | Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 2014-09-10 12:03:00.000 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 2014-09-10 12:05:00.000 | CDE 1007     | NJ    | Toyota | 4x4     | 1           | 0             | 6    | 321987654 |


Voici une brève description des colonnes :

| Colonne | Description |
|--------------|--------------------------------------------------------------------|
| TollID       | ID de guichet identifiant de façon unique un poste de péage            |
| EntryTime    | Date et heure UTC d’entrée du véhicule en poste de péage |
| LicensePlate | Numéro de plaque d’immatriculation du véhicule                            |
| State        | État aux États-Unis                                           |
| Make         | Fabricant du véhicule                                 |
| Modèle        | Numéro de modèle du véhicule                                 |
| VehicleType  | 1 pour les véhicules de transport de personnes, 2 pour les véhicules commerciaux       |
| WeightType   | Poids du véhicule en tonnes ; 0 pour les véhicules de tourisme                   |
| Toll         | Prix du péage en dollar américain                                              |
| Tag          | Balise électronique sur la voiture, qui automatise le paiement ; vide si le paiement a été effectué manuellement |


### <a name="exit-data-stream"></a>Flux des données de sortie

Le flux des données de sortie contient des informations sur les véhicules qui quittent la gare de péage.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

Voici une brève description des colonnes :


| Colonne | Description |
|--------------|-----------------------------------------------------------------|
| TollID       | ID de guichet identifiant de façon unique un poste de péage         |
| ExitTime     | Date et heure UTC auxquelles le véhicule quitte le poste de péage |
| LicensePlate | Numéro de plaque d’immatriculation du véhicule                         |

### <a name="commercial-vehicle-registration-data"></a>Données d’inscription des véhicules commerciaux

Le didacticiel utilise un instantané statique de la base de données d’inscription de véhicules commerciaux.


| LicensePlate | ID d’inscription | Expiré |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| BAC 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Voici une brève description des colonnes :


| Colonne | Description |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Numéro de plaque d’immatriculation du véhicule                         |
| ID d’inscription     | ID d’inscription du véhicule |
| Expiré | État d’inscription du véhicule : 0 si l’inscription est active, 1 si elle a expiré                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurer l’environnement pour Azure Stream Analytics

Pour suivre ce didacticiel, vous avez besoin d’un abonnement Microsoft Azure. Microsoft propose un essai gratuit des services Microsoft Azure.

Si vous n’avez pas de compte Azure, vous pouvez [demander un essai gratuit](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Pour pouvoir vous inscrire à un essai gratuit, vous devez disposer d’un appareil mobile pouvant recevoir des messages texte et d’une carte de crédit en cours de validité.

Pour tirer pleinement parti de votre crédit Azure gratuit d’une valeur de 200 $, suivez les instructions de la section « Nettoyer votre compte Azure » à la fin de cet exercice.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Configurer les ressources Azure nécessaires pour le didacticiel

Ce didacticiel nécessite que vous disposiez de 2 concentrateurs d’événements pour la réception des flux de données *d’entrée* et de *sortie*. Azure SQL Database produit les résultats des travaux Stream Analytics. Stockage Azure stocke les données de référence sur les inscriptions de véhicules.

Le script Setup.ps1 dans le dossier TollApp sur GitHub permet de créer toutes les ressources nécessaires. Pour gagner du temps, nous vous recommandons de l’exécuter. Pour plus d’informations sur la configuration de ces ressources dans le portail Azure, consultez l’annexe « Configuration des ressources du didacticiel dans le portail Azure ».

Téléchargez et enregistrez le dossier et les fichiers de support [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) .

Ouvrez une fenêtre **Microsoft Azure PowerShell**_en tant qu’administrateur_. Si vous ne disposez pas encore d’Azure PowerShell, suivez les instructions de la section [Installer et configurer Azure PowerShell](../powershell-install-configure.md) pour l’installer.

Étant donné que Windows bloque automatiquement les fichiers .exe, .dll et .ps1, vous devez définir la stratégie d’exécution avant d’exécuter le script. Assurez-vous que la fenêtre Azure PowerShell s’exécute _en tant qu’administrateur_. Exécutez l’applet de commande **Set-ExecutionPolicy unrestricted**. Quand vous y êtes invité, tapez **O**.

![Capture d’écran de l’applet de commande « Set-ExecutionPolicy illimité » en cours d’exécution dans la fenêtre Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Pour vérifier que l’applet de commande a fonctionné, exécutez **Get-ExecutionPolicy** .

![Capture d’écran de l’applet de commande « Get-ExecutionPolicy » en cours d’exécution dans la fenêtre Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Accédez au répertoire contenant les scripts et l’application du générateur.

![Capture d’écran de l’applet de commande « cd .\TollApp\TollApp » en cours d’exécution dans la fenêtre Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Tapez **.\\Setup.ps1** pour configurer votre compte Azure, créer et configurer toutes les ressources nécessaires, et commencer à générer des événements. Le script choisit au hasard la région dans laquelle créer vos ressources. Pour spécifier explicitement une région, vous pouvez utiliser le paramètre **-location** comme dans l’exemple suivant :

**.\\Setup.ps1 -location “Central US”**

![Capture d’écran de la page de connexion Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Le script ouvre la page **Se connecter** pour Microsoft Azure. Entrez les informations d’identification de votre compte.

> [AZURE.NOTE] Si votre compte a accès à plusieurs abonnements, vous devez entrer le nom de l’abonnement à utiliser pour le didacticiel.

L’exécution du script peut prendre plusieurs minutes. Une fois l’exécution terminée, la sortie doit ressembler à la capture d’écran suivante.

![Capture d’écran de la sortie du script dans la fenêtre Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Vous voyez également une autre fenêtre semblable à la capture d’écran suivante. Cette application envoie des événements aux hubs d’événements Azure, qui sont requis pour exécuter le didacticiel. Vous ne devez donc pas arrêter l’application ou fermer cette fenêtre tant que l’exécution du didacticiel n’est pas terminée.

![Capture d’écran « Envoi de données de hub d’événements »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Normalement, toutes les ressources créées doivent maintenant apparaître dans le portail Azure. Accédez à <https://manage.windowsazure.com> et connectez-vous avec les informations d’identification de votre compte.

### <a name="azure-event-hubs"></a>Hubs d'événements Azure

Cliquez sur **SERVICE BUS** sur le côté gauche du portail Azure pour voir les hubs d’événements que le script a créés dans la section précédente.

![SERVICE BUS](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Tous les espaces de noms disponibles apparaissent dans votre abonnement. Cliquez sur celui commençant par *tolldata* (tolldata4637388511 dans notre exemple). Accédez à l’onglet **HUBS D’ÉVÉNEMENTS** .

![Onglet Hubs d’événements dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Deux concentrateurs d’événements nommés *entry* et *exit* apparaissent dans cet espace de noms.

![Capture d’écran des hubs d’événements d’« entrée » et de « sortie » dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Conteneur Azure Storage

1. Cliquez sur **STOCKAGE** sur le côté gauche du portail Azure pour voir le conteneur Stockage Azure utilisé dans le didacticiel.

    ![Élément de menu de stockage](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Cliquez sur celui commençant par *tolldata* (tolldata4637388511 dans notre exemple). Ouvrez l’onglet **CONTENEURS** pour voir le conteneur créé.

    ![Onglet Conteneurs dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Cliquez sur le conteneur **tolldata** pour voir le fichier JSON chargé, qui contient les données d’inscription des véhicules.

    ![Capture d’écran du fichier registration.json dans le conteneur](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Azure SQL Database

1. Cliquez sur **BASES DE DONNÉES SQL** sur le côté gauche du portail Azure pour voir la base de données SQL à utiliser dans le didacticiel.

    ![BASES DE DONNÉES SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Cliquez sur **tolldatadb**.

    ![Capture d’écran de la base de données SQL créée](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copiez le nom du serveur sans le numéro de port (par exemple,*nom_serveur*.database.windows.net).

## <a name="connect-to-the-database-from-visual-studio"></a>Se connecter à la base de données à partir de Visual Studio

Utilisez Visual Studio pour accéder aux résultats de requête dans la base de données de sortie.

Connectez-vous à la base de données SQL (destination) à partir de Visual Studio :

1. Ouvrez Visual Studio, puis cliquez sur **Outils** > **Connexion à une base de données**.

2. Si vous y êtes invité, sélectionnez la source de données **Microsoft SQL Server** .

    ![Boîte de dialogue Modifier la source de données](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. Dans le champ **Nom du serveur** , collez le nom copié dans la section précédente du portail Azure (c’est-à-dire *nom_serveur*.database.windows.net).

4. Cliquez sur **Utiliser l’authentification SQL Server**.

5. Entrez **tolladmin** dans le champ **Nom d’utilisateur** et **123toll!** in the **Mot de passe** .

6. Cliquez sur **Sélectionner ou entrer un nom de base de données**, puis sélectionnez **TollDataDB** comme base de données.

    ![Boîte de dialogue Ajouter une connexion](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Cliquez sur **OK**.

8. Ouvrez l’Explorateur de serveurs.

    ![Explorateur de serveurs](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Quatre tables sont disponibles dans la base de données TollDataDB.

    ![Tables de la base de données TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator:-tollapp-sample-project"></a>Générateur d’événements - Exemple de projet TollApp

Le script PowerShell commence automatiquement à envoyer des événements à l’aide de l’exemple de programme d’application TollApp. Vous n’avez pas besoin d’effectuer d’étapes supplémentaires.

Toutefois, si vous êtes intéressé par les détails de l’implémentation, le code source de l’application TollApp est disponible dans la page [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)de GitHub.

![Capture d’écran d’exemple de code affiché dans Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le portail Azure, ouvrez Stream Analytics, puis cliquez sur **NOUVEAU** dans l’angle inférieur gauche de la page pour créer un travail d’analyse.

    ![Bouton Nouveau](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Cliquez sur **CRÉATION RAPIDE**. Sélectionnez la région dans laquelle vos autres ressources sont créées par le script.

3. Pour le paramètre **COMPTE DE STOCKAGE DE SURVEILLANCE RÉGIONALE**, sélectionnez **CRÉER UN NOUVEAU COMPTE DE STOCKAGE**, puis attribuez un nom unique à ce compte. Azure Stream Analytics utilisera ce compte pour stocker les informations de surveillance de tous vos travaux futurs.

4. Cliquez sur **CRÉER UNE TÂCHE STREAM ANALYTICS** en bas de la page.

    ![Option Créer une tâche Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Définir les sources d’entrée

1. Cliquez sur la tâche d’analyse créée dans le portail.

    ![Capture d’écran de la tâche Stream Analytics dans le portail](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Ouvrez l’onglet **ENTRÉES** pour définir les données sources.

    ![Onglet Entrées](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Cliquez sur **AJOUTER UNE ENTRÉE**.

    ![Option Ajouter une entrée](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Cliquez sur **FLUX DE DONNÉES** dans la première page.

    ![Option Flux de données](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Sélectionnez **HUB D’ÉVÉNEMENTS** dans la deuxième page de l’Assistant.

    ![Option Hub d’événements](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Tapez **EntryStream** comme **ALIAS D’ENTRÉE**.

7. Cliquez sur la liste déroulante **Hub d’événements** et sélectionnez celui qui commence par « TollData » (par exemple, TollData9518658221).

8. Sélectionnez **entry** comme nom de concentrateur d’événements et **all** comme nom de stratégie du concentrateur d’événements.

    Vos paramètres ressemblent à ceci :

    ![Paramètres de hub d’événements](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. Sur la page suivante, sélectionnez **JSON** pour l’option **FORMAT DE SÉRIALISATION DE L’ÉVÉNEMENT**, et **UTF8** pour l’option **ENCODAGE**.

    ![Paramètres de sérialisation](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Cliquez sur **OK** en bas de la page pour terminer l’exécution de l’Assistant.

    ![Capture d’écran de l’entrée EntryStream dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Maintenant que vous avez créé le flux d’entrée, procéderez de la même façon pour créer le flux de sortie. Dans la troisième page de l’Assistant, veillez à entrer des valeurs comme sur la capture d’écran suivante.

    ![Paramètres pour le flux de sortie](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Vous avez défini deux flux d’entrée :

    ![Flux d’entrée définis dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    À présent, nous allons ajouter une entrée de données de référence pour le fichier blob contenant les données d’inscription des véhicules.

11. Cliquez sur **AJOUTER UNE ENTRÉE**, puis sur **DONNÉES DE RÉFÉRENCE**.

    ![Options « Ajouter une entrée » avec des données de référence sélectionnées](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. Dans la page suivante, sélectionnez le compte de stockage commençant par **tolldata**. Le nom de conteneur doit être **tolldata** et le nom d’objet blob sous **MODÈLE DE CHEMIN D’ACCÈS** doit être **registration.json**. Ce nom de fichier respecte la casse et doit être en minuscules.

    ![Paramètres de stockage d’objets blob](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Dans la page suivante, sélectionnez les valeurs indiquées dans la capture d’écran ci-après, puis cliquez sur **OK** pour terminer l’exécution de l’Assistant.

    ![Sélection de JSON pour « Format de sérialisation de l’événement » et d’UTF-8 pour « Encodage »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Désormais, toutes les entrées sont définies.

![Capture d’écran des trois entrées définies](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Définir la sortie

1. Cliquez sur l’onglet **SORTIE**, puis sur **AJOUTER UNE SORTIE**.

    ![Onglet Sortie et option « Ajouter une sortie »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Cliquez sur **Base de données SQL**.

3. Sélectionnez le nom de serveur utilisé dans la section « Se connecter à la base de données à partir de Visual Studio » de l’article. Le nom de la base de données est **TollDataDB**.

4. Entrez **tolladmin** dans le champ **NOM D’UTILISATEUR**, **123toll!** dans le champ **MOT DE PASSE** et **TollDataRefJoin** dans le champ **TABLE**.

    ![Paramètres SQL Database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Requête Azure Stream Analytics

L’onglet **REQUÊTE** contient une requête SQL qui convertit les données entrantes.

![Requête ajoutée à l’onglet Requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Ce didacticiel tente de répondre à plusieurs questions liées aux données de péage, et montre comment construire des requêtes Stream Analytics utilisables dans Azure Stream Analytics pour fournir une réponse pertinente.

Avant de commencer une première tâche Stream Analytics, examinons quelques scénarios et la syntaxe de requête.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Présentation du langage de requête d’Azure Stream Analytics
-----------------------------------------------------

Supposons que nous devons compter le nombre de véhicules qui entrent dans un poste de péage. Comme il s’agit d’un flux continu d’événements, nous devons définir une « période ». Modifions la question en « Combien de véhicules entrent en poste de péage en trois minutes ? ». C’est ce qu’on appelle communément le « nombre bascule ».

Examinons la requête d’Azure Stream Analytics répondant à cette question :

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Comme vous pouvez le voir, Azure Stream Analytics utilise un langage de requête similaire à SQL et ajoute quelques extensions pour spécifier les aspects de la requête liés au temps.

Pour plus de détails, consultez les constructions [Gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et [Fenêtrage](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilisées dans la requête à partir de MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Test des requêtes Azure Stream Analytics

Notre première requête Azure Stream Analytics étant écrite, nous pouvons la tester à l’aide des exemples de fichiers de données figurant dans votre dossier TollApp au chemin suivant :

**..\\TollApp\\TollApp\\Data**

Ce dossier contient les fichiers suivants :

- Entry.json
- Exit.json
- registration.json

## <a name="question-1:-number-of-vehicles-entering-a-toll-booth"></a>Question 1 : Nombre de véhicules arrivant à un poste de péage

1. Ouvrez le portail Azure et accédez à la tâche Azure Stream Analytics que vous avez créée. Ouvrez l’onglet **REQUÊTE** , puis copiez et collez la requête de la section précédente.

    ![Requête collée dans l’onglet Requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Pour valider cette requête sur des exemples de données, cliquez sur le bouton **Tester** . Dans la boîte de dialogue qui s’affiche, accédez à Entry.json, fichier contenant des exemples de données issues du flux d’événements **EntryTime** .

    ![Capture d’écran du fichier Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Valider que la sortie de la requête est conforme au résultat attendu :

    ![Résultats du test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2:-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Question 2 : Indiquer le temps total que met chaque véhicule pour franchir le poste de péage

Le temps moyen nécessaire pour qu’une voiture franchisse le péage vous aide à évaluer l’efficacité du processus et l’expérience du client.

Pour connaître la durée totale, vous devez joindre le flux EntryTime au flux ExitTime. Joignez les flux sur les colonnes TollId et LicencePlate. L’opérateur **JOIN** exige que vous spécifiiez une marge de manœuvre temporelle décrivant l’écart de temps acceptable entre les événements joints. Vous allez utiliser la fonction **DATEDIFF** pour spécifier que les événements ne doivent pas être séparés de plus de 15 minutes. Vous allez également appliquer la fonction **DATEDIFF** aux heures de sortie et d’entrée pour calculer le temps réel qu’un véhicule passe en gare de péage. Notez que la fonction **DATEDIFF** n’est pas utilisée de la même façon dans une instruction **SELECT** et dans une condition **JOIN**.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Pour tester cette requête, mettez-la à jour sous l’onglet **REQUÊTE** de votre tâche :

    ![Requête mise à jour sous l’onglet Requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Cliquez sur **Tester** et spécifiez des exemples de fichiers d’entrée pour EntryTime et ExitTime.

    ![Capture d’écran de fichiers d’entrée sélectionnés](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Activez la case à cocher pour tester la requête et afficher la sortie :

    ![Résultat du test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3:-report-all-commercial-vehicles-with-expired-registration"></a>Question 3 : Signaler tous les véhicules commerciaux dont l’inscription a expiré

Azure Stream Analytics peut utiliser des instantanés de données pour joindre des flux de données temporelles. Pour illustrer cette fonctionnalité, nous allons utiliser l’exemple de question suivant.

Si un véhicule commercial est inscrit auprès de l’entreprise de péage, il peut franchir directement le poste de péage sans être arrêté pour inspection. Vous allez utiliser une table de recherche contenant les inscriptions de véhicules commerciaux pour identifier tous ceux dont l’inscription a expiré.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Pour tester une requête à l’aide de données de référence, vous devez définir une source d’entrée pour les données de référence, ce que vous avez déjà fait.

1. Pour tester cette requête, collez-la dans l’onglet **REQUÊTE**, cliquez sur **Tester**, puis spécifiez les deux sources d’entrée :

    ![Capture d’écran de fichiers d’entrée sélectionnés](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Observez le résultat de la requête :

    ![Capture d’écran d’un résultat de requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Démarrage de la tâche Stream Analytics


Il est à présent temps d’achever la configuration et de commencer la tâche. Enregistrez la requête répondant à la question 3, dont le résultat correspond au schéma de notre table de sortie **TollDataRefJoin** .

Accédez au **TABLEAU DE BORD** du travail, puis cliquez sur **DÉMARRER**.

![Capture d’écran du bouton Démarrer dans le tableau de bord de la tâche](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Dans la boîte de dialogue qui s’affiche, définissez l’heure **DÉMARRER LA SORTIE** sur **HEURE PERSONNALISÉE**. Modifiez l’heure en définissant une heure antérieure à l’heure actuelle. Cette modification permet de s’assurer que tous les événements du hub d’événements ont été traités à partir du moment où vous avez commencé à générer les événements au début du didacticiel. À présent, cochez la case pour démarrer le travail.

![Sélection de Heure personnalisée](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Le démarrage du travail peut prendre quelques minutes. Vous pouvez voir l’état de la tâche dans la page de niveau supérieur de Stream Analytics.

![Capture d’écran de l’état de la tâche](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Vérifier les résultats dans Visual Studio

1. Ouvrez l’Explorateur de serveurs Visual Studio, puis cliquez avec le bouton droit sur la table **TollDataRefJoin** .
2. Cliquez sur **Afficher les données de la table** pour voir le résultat de votre tâche.

    ![Sélection de « Afficher les données de la table » dans l’Explorateur de serveurs](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Mettre à l’échelle des tâches Azure Stream Analytics

Azure Stream Analytics est conçu pour adapter son échelle en souplesse afin de pouvoir gérer un grand nombre de données. La requête d’Azure Stream Analytics peut utiliser une clause **PARTITION BY** pour informer le système que cette étape doit faire l’objet d’une montée en charge. **PartitionId** est une colonne spéciale ajoutée par le système, qui correspond à l’ID de partition de l’entrée (hub d’événements).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Arrêtez le travail en cours, mettez à jour la requête dans l’onglet **REQUÊTE**, puis ouvrez l’onglet **METTRE À L’ÉCHELLE**.

    **UNITÉS DE DIFFUSION EN CONTINU** définissent la quantité de puissance de calcul allouable à la tâche.

2. Placez le curseur à la position 6.

    ![Capture d’écran de la sélection de 6 unités de diffusion en continu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Accédez à l’onglet **SORTIES**, puis modifiez le nom de la table SQL en **TollDataTumblingCountPartitioned**.

À présent, si vous démarrez la tâche, Azure Stream Analytics peut répartir celle-ci entre plusieurs ressources de calcul et atteindre un meilleur débit. Notez que l’application TollApp envoie également des événements partitionnés par TollId.

## <a name="monitor"></a>Surveiller

L’onglet **SURVEILLER** contient des statistiques sur la tâche en cours d’exécution.

![Capture d’écran de statistiques relatives aux tâches en cours d’exécution](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Vous pouvez accéder aux **Journaux des opérations** à partir de l’onglet **TABLEAU DE BORD**.

![Option « Journaux d’opération »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Capture d’écran des journaux d’opérations où vous pouvez consulter l’état des tâches](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Pour afficher des informations supplémentaires sur un événement particulier, sélectionnez l’événement, puis cliquez sur le bouton **DÉTAILS** .

![Capture d’écran des détails d’un événement sélectionné](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusion

Ce didacticiel vous a présenté le service Azure Stream Analytics. Il vous a expliqué comment configurer les entrées et sorties de la tâche Stream Analytics. À l’aide du scénario sur les données de péage, le didacticiel vous a expliqué les types courants de problèmes qui surviennent dans l’espace de données en mouvement et comment les résoudre avec de simples requêtes de type SQL dans Azure Stream Analytics. Il a décrit des constructions d’extension SQL en vue d’une utilisation avec des données temporelles. Il a montré comment joindre des flux de données et enrichir le flux de données avec des données de référence statiques, et comment faire monter en charge une requête pour obtenir un débit plus élevé.

Bien que ce didacticiel constitue une bonne introduction, il n’est en aucun cas complet. Vous pouvez trouver plusieurs modèles de requête utilisant le langage SAQL dans [Exemples de requête pour les modes d’utilisation courants dans Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
Pour en savoir plus sur Azure Stream Analytics, consultez la [documentation en ligne](https://azure.microsoft.com/documentation/services/stream-analytics/) .

## <a name="clean-up-your-azure-account"></a>Nettoyer votre compte Azure

1. Arrêtez la tâche Stream Analytics dans le portail Azure.

    Le script Setup.ps1 crée deux hubs d’événements et une base de données SQL. Les instructions suivantes vous aideront à nettoyer les ressources à la fin du didacticiel.

2. Dans une fenêtre PowerShell, tapez **.\\Cleanup.ps1** pour démarrer le script qui supprime les ressources utilisées dans le didacticiel.

    > [AZURE.NOTE] Les ressources sont identifiées par leur nom. Assurez-vous de vérifier attentivement chaque élément avant d’en confirmer la suppression.

    ![Capture d’écran du processus de nettoyage](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)



<!--HONumber=Oct16_HO2-->


