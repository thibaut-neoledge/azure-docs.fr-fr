<properties 
   pageTitle="Résolution des problèmes de démarrage des rôles | Microsoft Azure"
   description="Vous trouverez ici quelques raisons fréquentes aux problèmes de démarrage d’un rôle Cloud Service. Nous vous présentons également des solutions à ces problèmes."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# Résolution des problèmes de démarrage des rôles Cloud Service

Voici des solutions à quelques problèmes courants liés à des rôles Azure Cloud Service qui ne parviennent pas à démarrer.

## Contacter le Support technique Azure

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](http://azure.microsoft.com/support/forums/).

Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez la [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).


## DLL ou dépendances manquantes
Les rôles qui ne répondent pas ou qui basculent sans cesse entre les états **initialisation**, **occupé** et **arrêt** peuvent être liés à l’absence de DLL ou d’assemblies.

**Symptôme :** exemples de symptômes liés à des DLL ou des assemblies manquants :

- Votre instance de rôle passe de l’état d’**initialisation** à l’état **occupé** ou **arrêt**
- Votre instance de rôle est passée à l’état **prêt** mais le page ne s’affiche pas lorsque vous accédez à votre application Web

Solution : nous recommandons trois méthodes pour rechercher une solution à ces problèmes.

## Diagnostic des problèmes de DLL manquantes dans un rôle Web

Lorsque vous accédez à un site Web déployé dans un rôle Web et que le navigateur affiche une erreur de serveur du type :

![Erreur de serveur dans l’application « / ».](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## Diagnostiquer les problèmes en désactivant les erreurs personnalisées

Vous pouvez afficher des messages d’erreur plus complets en configurant le fichier web.config du rôle Web pour qu’il désactive le mode d’erreur personnalisée et redéploie le service.

Pour afficher des erreurs plus détaillées sans utiliser le Bureau à distance :

1. Ouvrez la solution dans Visual Studio.

2. Dans l’**Explorateur de solutions**, recherchez et ouvrez le fichier web.config.

3. Dans le fichier web.config, recherchez la section system.web et ajoutez la ligne suivante :

    ```xml
    <customErrors mode="Off" />
    ```

4. Enregistrez le fichier.

5. Recréez le package et redéployez le service.

Une fois le service redéployé, l’erreur s’affiche avec le nom de la DLL ou de l’assembly manquant.

## Diagnostiquer les problèmes en affichant l’erreur à distance

Vous pouvez utiliser le Bureau à distance pour accéder au rôle et afficher des erreurs plus détaillées à distance. Pour afficher les erreurs à l’aide du Bureau à distance, procédez comme suit :

1. Assurez-vous que vous disposez du SDK Azure version 1.3 ou ultérieure.

2. Lors du déploiement de la solution à l’aide de Visual Studio, choisissez « Configurer les connexions Bureau à distance... ». Pour plus d’informations sur la configuration de la connexion Bureau à distance, consultez l’article [Utilisation du Bureau à distance avec des rôles Azure](https://msdn.microsoft.com/library/gg443832.aspx).

3. Dans le portail de gestion Azure Microsoft, une fois que l’instance affiche l’état **prêt**, cliquez sur l’une des instances de rôle.

4. Cliquez sur l’icône **Connexion** située dans la zone **Accès à distance** du ruban

5. Connectez-vous à la machine virtuelle à l’aide des informations d’identification spécifiées lors de la configuration du Bureau à distance.

6. Ouvrez une invite de commandes.

7. Saisissez `IPconfig`.

8. Notez la valeur de l’adresse IPV4.

9. Ouvrez Internet Explorer.

10. Entrez l’adresse et le nom de l’application Web. Par exemple, `http://<IPV4 Address>/default.aspx`.

L’accès au site Web renvoie des messages d’erreur plus explicites.

* Erreur de serveur dans l’application « / »

* Description : une exception non gérée s’est produite lors de l’exécution de la requête Web en cours. Veuillez consulter l’arborescence des appels de procédure pour plus d’informations sur l’erreur et sa source dans le code.

* Détails de l’exception : System.IO.FIleNotFoundException : impossible de charger le fichier ou l’assembly « Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35 » ou l’une de ses dépendances. Le système ne peut pas trouver le fichier spécifié.

Par exemple :

![Erreur de serveur explicite dans l’application « / »](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## Diagnostiquer les problèmes à l’aide de l’émulateur de calcul

Vous pouvez utiliser l’émulateur de calcul Microsoft Azure pour diagnostiquer et résoudre les problèmes de dépendances manquantes et les erreurs du fichier web.config.

Pour obtenir de meilleurs résultats à l’aide de cette méthode de diagnostic, vous devez utiliser un ordinateur ou une machine virtuelle disposant d’une nouvelle installation de Windows. Pour mieux simuler l’environnement Windows Azure, vous devez utiliser Windows Server 2008 R2 x64.

1. Installez la version autonome du [SDK Azure](https://azure.microsoft.com/downloads)

2. Sur l’ordinateur de développement, générez le projet Cloud Service.

3. Dans l’Explorateur Windows, accédez au dossier bin\\debug du projet Cloud Service.

4. Copiez le dossier .csx et le fichier .cscfg sur l’ordinateur que vous utilisez pour déboguer les problèmes.

5. Sur le nouvel ordinateur, ouvrez une invite de commandes du SDK Azure et entrez `csrun.exe /devstore:start`.

6. Dans l’invite de commandes, entrez `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Au démarrage du rôle, le détail de l’erreur s’affiche dans Internet Explorer. Vous pouvez également utiliser les outils de dépannage Windows standard pour diagnostiquer le problème.

## Diagnostiquer les problèmes à l’aide d’IntelliTrace

Pour les rôles de travail et les rôles web qui utilisent le framework .NET 4, vous pouvez utiliser l’utilitaire [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx) disponible dans [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Pour déployer le service avec la fonction IntelliTrace activée, procédez comme suit :

1. Vérifiez que vous disposez du SDK Azure version 1.3 ou ultérieure.

2. Déployez la solution à l’aide de Visual Studio. Au cours du déploiement, cochez la case **Activer IntelliTrace pour les rôles .NET 4**.

3. Une fois l’instance démarrée, ouvrez l’**Explorateur de serveurs**.

4. Développez le nœud **Azure\\Cloud Services** et recherchez le déploiement.

5. Développez le déploiement jusqu’à ce que les instances de rôle s’affichent. Cliquez avec le bouton droit de la souris sur l’une des instances.

6. Sélectionnez **Afficher les fichiers journaux IntelliTrace**. Le **Résumé IntelliTrace** s’ouvre.

7. Recherchez la section du résumé relative aux exceptions. S’il existe des exceptions, celles-ci s’afficheront sous le libellé **Données d’exception**.

8. Développez les **Données d’exception** et recherchez les erreurs **System.IO.FileNotFoundException** semblables à ce qui suit :

![Données d’exception, fichier ou assembly manquant](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## Résolution du problème de DLL et d’assemblies manquants

Pour résoudre les erreurs liées à des DLL et des assemblies manquants, procédez comme suit :

1. Ouvrez la solution dans Visual Studio.

2. Dans l’**Explorateur de solutions**, ouvrez le dossier **Références**.

3. Cliquez sur l’assembly identifié dans l’erreur.

4. Dans le volet **Propriétés**, recherchez la propriété Copie locale et définissez la valeur sur **True**.

5. Redéployez le service hébergé.

Après avoir vérifié que toutes les erreurs ont été corrigées, vous pouvez redéployer le service sans activer le paramètre **Activer IntelliTrace pour les rôles .NET 4**.

## Étapes suivantes

Affichez plus d’[articles de résolution des problèmes](..\?tag=top-support-issue&service=cloud-services) liés aux services cloud.

<!---HONumber=Oct15_HO4-->