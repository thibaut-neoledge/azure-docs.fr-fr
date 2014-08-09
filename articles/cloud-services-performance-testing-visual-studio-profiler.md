<properties linkid="dev-net-common-tasks-profiling-in-compute-emulator" urlDisplayName="Team Foundation Service" headerExpose="" pageTitle="Profiling a Cloud Service Locally in the Compute Emulator" metaKeywords="" footerExpose="" description="" umbracoNaviHide="0" disqusComments="1" title="Testing the Performance of a Cloud Service Locally in the Azure Compute Emulator Using the Visual Studio Profiler" authors="" />

Test des performances d'un service cloud local dans l'émulateur de calcul Azure avec le profileur Visual Studio
===============================================================================================================

Différents outils et diverses techniques permettent de tester les performances des services cloud. Lorsque vous publiez un service cloud sur Azure, vous pouvez demander à ce que Visual Studio collecte des données de profilage, puis les analyse en local, comme décrit dans la page [Analyse du profil d'une application Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh369930.aspx). Vous pouvez également utiliser le diagnostic pour suivre tout un ensemble de compteurs de performances, comme décrit dans la rubrique [Utilisation de compteurs de performances dans Azure](http://www.windowsazure.com/fr-fr/develop/net/common-tasks/performance-profiling). Vous pouvez également profiler votre application en local dans l'émulateur de calcul avant de la déployer dans le cloud.

Cet article présente la méthode de profilage par échantillonnage de l'UC, qui peut se faire en local dans l'émulateur. Cette méthode de profilage est peu intrusive. Selon une fréquence d'échantillonnage définie, le profileur enregistre un instantané de la pile d'appels. Les données sont collectées pendant un certain temps, puis sont présentées dans un rapport. Cette méthode de profilage indique plutôt, dans une application qui effectue beaucoup de calculs, où se fait la plus grande part du travail du processeur. Ceci vous permet de vous occuper en priorité des « points chauds », là où votre application passe le plus de temps.

Configuration requise
---------------------

Vous pouvez exécuter le profileur en local uniquement si vous disposez de Visual Studio Premium ou Visual Studio Ultimate.

Dans cet article :
------------------

-   [Étape 1 : configuration de Visual Studio pour le profilage](#step1)

-   [Étape 2 : attachement à un processus](#step2)

-   [Étape 3 : affichage des rapports de profilage](#step3)

-   [Étape 4 : application de modifications et comparaison des performances](#step4)

-   [Résolution des problèmes](#troubleshooting)

-   [Étapes suivantes](#nextSteps)

Étape 1 : configuration de Visual Studio pour le profilage
----------------------------------------------------------

Tout d'abord, certaines options de configuration de Visual Studio peuvent s'avérer utiles dans le cadre du profilage. Afin de bien comprendre les rapports de profilage, vous aurez besoin de symboles (fichiers .pdb) pour votre application, ainsi que de symboles pour les bibliothèques système. Assurez-vous que vous faites référence aux serveurs de symboles disponibles. Pour cela, dans le menu **Outils** de Visual Studio, sélectionnez **Options**, puis **Débogage**, et enfin **Symboles**. Assurez-vous que Microsoft Symbol Servers figure bien dans **Emplacements du fichier de symboles (.pdb)**. Vous pouvez également faire référence à http://referencesource.microsoft.com/symbols, qui peut comporter d'autres fichiers de symboles.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png)

Si vous le souhaitez, vous pouvez simplifier les rapports générés par le profileur en choisissant Uniquement mon code. Lorsque cette option est activée, les piles d'appels de fonction sont simplifiées afin que les appels purement internes aux bibliothèques et à .NET Framework soient masqués dans les rapports. Dans le menu **Outils**, choisissez **Options**. Développez le nœud **Outils de performances** et choisissez **Général**. Activez la case à cocher **Activer Uniquement mon code pour les rapports du profileur**.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png)

Vous pouvez utiliser ces instructions dans un projet existant ou un nouveau projet. Si vous créez un projet pour tester une des techniques décrites plus bas, choisissez un projet C\# **Service cloud Azure** et sélectionnez un **rôle Web** et un **rôle de travail**.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png)

Pour l'exemple, ajoutez à votre projet du code qui demande beaucoup de temps et provoque des problèmes de performances évidents. Par exemple, ajoutez le code suivant à un projet de rôle de travail :

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = ""
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
        }

Appelez ce code depuis la méthode Run dans la classe RoleEntryPoint-derived du rôle de travail.

    public override void Run()
    {
        while (true)
        {
            Concatenator.Concatenate(10000);
        }
        }

Générez le service cloud et exécutez-le en local avec la configuration de solution **Release**. Les fichiers et dossiers sont ainsi créés pour l'exécution de l'application en local et tous les émulateurs sont démarrés.

Étape 2 : attachement à un processus
------------------------------------

Au lieu de profiler l'application en la démarrant depuis l'interface de développement de Visual Studio 2010, vous devez attacher le profileur à un processus en cours d'exécution.

Pour ce faire, dans le menu **Analyse**, sélectionnez **Profileur**, puis **Attacher/Détacher**.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png)

Pour un rôle de travail, recherchez le processus WaWorkerHost.exe.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png)

Si votre dossier de projet se trouve sur un disque réseau, le profileur vous demande d'indiquer un autre emplacement pour enregistrer les rapports de profilage.

Vous pouvez également l'attacher à un rôle Web en l'attachant à WaIISHost.exe. Si votre application comporte plusieurs processus de rôle de travail, utilisez l'ID de processus pour les distinguer les uns des autres. Vous pouvez effectuer une requête par programme sur l'ID de processus en accédant à l'objet Process. Par exemple, si vous ajoutez ce code à la méthode Run de la classe RoleEntryPoint-derived dans un rôle, vous pouvez consulter le journal dans l'interface de l'émulateur de calcul pour savoir à quel processus se connecter.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Pour consulter le journal, ouvrez l'interface utilisateur de l'émulateur de calcul.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png)

Ouvrez la fenêtre de console du journal du rôle de travail dans l'interface utilisateur de l'émulateur de calcul en cliquant sur la barre de titre de la fenêtre de la console. L'ID du processus figure dans le journal.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png)

Une fois le profileur attaché, suivez les étapes de l'interface utilisateur de votre application (le cas échéant) afin de reproduire le scénario.

Lorsque vous souhaitez arrêter le profilage, cliquez sur le lien **Terminer le profilage**.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png)

Étape 3 : affichage des rapports de performances
------------------------------------------------

Le rapport de performances de votre application s'affiche.

À ce stade, le profileur s'arrête, il enregistre les données dans un fichier .vsp et il affiche un rapport présentant une analyse des données.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png)

Si vous voyez String.wstrcpy dans le Chemin réactif, cliquez sur Uniquement mon code pour modifier l'affichage et ne voir que le code utilisateur. Si vous voyez String.Concat, essayez de cliquer sur le bouton Afficher tout le code.

Vous devez voir que la méthode Concatenate et String.Concat occupent une grande partie du temps d'exécution.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png)

Si vous avez ajouté le code de concaténation de chaîne dans cet article, vous devez obtenir un avertissement correspondant dans la Liste des tâches. Il est possible que vous voyiez également un avertissement lié au nettoyage de la mémoire, ce qui est dû au nombre de chaînes rejetées.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png)

Étape 4 : application de modifications et comparaison des performances
----------------------------------------------------------------------

Vous pouvez également comparer les performances avant et après la modification du code. Modifiez le code ou remplacez l'opération de concaténation de chaîne par StringBuilder :

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Lancez un nouveau test de performances, et comparez les résultats. Dans l'Explorateur de performances, si les tests font partie de la même session, vous pouvez simplement sélectionner les deux rapports, ouvrir le menu contextuel et sélectionner **Comparer les rapports de performances**. Si vous souhaitez comparer un test avec un test d'une autre session de performances, ouvrez le menu **Analyse**, puis sélectionnez **Comparer les rapports de performances**. Spécifiez les deux fichiers dans la boîte de dialogue qui s'affiche.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png)

Les rapports indiquent les différences entre les deux tests.

![](./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png)

Félicitations ! Vous avez fait connaissance avec le profileur.

Résolution des problèmes
------------------------

-   Assurez-vous que le profilage porte bien sur une build Release.

-   Si l'option Attacher/Détacher n'est pas activée dans le menu Profileur, exécutez l'Assistant Performance.

-   Utilisez l'émulateur de calcul pour connaître l'état de votre application.

-   Si vous rencontrez des problèmes pour démarrer les applications dans l'émulateur de calcul ou pour attacher le profileur, arrêtez l'émulateur de calcul et redémarrez-le. Si cela ne résout pas le problème, essayez de redémarrer votre ordinateur. Ce problème peut survenir lorsque vous utilisez l'émulateur de calcul pour suspendre et supprimer des déploiements en cours d'exécution.

-   Si vous avez utilisé une des commandes de profilage depuis la ligne de commande, en particulier les paramètres globaux, assurez-vous que VSPerfClrEnv /globaloff a été appelé et que VsPerfMon.exe est arrêté.

-   Si lors de l'échantillonnage, vous obtenez le message « PRF0025: Aucune donnée n'a été collectée », vérifiez si le processus que vous avez attaché présente une activité dans le processeur. Les applications qui n'effectuent pas de calculs peuvent ne pas produire de données d'échantillonnage. Il est également possible que le processus se soit terminé avant l'exécution de l'échantillonnage. Vérifiez si la méthode Run du rôle pour lequel vous établissez le profil ne s'est pas terminée.

Étapes suivantes
----------------

L'instrumentalisation d'exécutables Azure dans l'émulateur de calcul n'est pas prise en charge par le profileur Visual Studio 2010, mais si vous souhaitez tester l'allocation de la mémoire, vous pouvez choisir cette option au moment du profilage. Vous pouvez également choisir le profilage d'accès concurrentiel, qui vous aide à savoir si des threads perdent du temps à se disputer les verrouillages, ou bien le profilage d'interaction de couche, qui permet de détecter les problèmes de performances lors de l'interaction entre différentes couches de l'application, la plupart du temps entre la couche de données et un rôle de travail. Vous pouvez consulter les requêtes de base de données que votre application génère et utiliser les données de profilage pour optimiser l'utilisation de la base de données. Pour plus d'informations sur le profilage d'interaction de couche, consultez la page [Procédure pas à pas : utilisation du profileur d'interaction de couche dans Visual Studio Team System 2010](http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx).

