<properties
   pageTitle="Dépannage d’erreurs client Docker sur Windows avec Visual Studio | Microsoft Azure"
   description="Résolvez les problèmes d’utilisation de Visual Studio pour créer et déployer des applications web dans Docker sur Windows avec Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# Résolution des problèmes de développement avec Docker pour Visual Studio

Lorsque vous travaillez avec Visual Studio Tools pour Docker Preview, vous pourriez rencontrer des problèmes en raison de la nature de la version préliminaire. Voici quelques problèmes courants et leurs solutions.

##Impossible de configurer Program.cs pour la prise en charge de Docker

Lorsque vous ajoutez la prise en charge de Docker, `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS"))` doit être ajouté à WebHostBuilder(). Si Program.cs, la fonction `Main()` ou une nouvelle classe WebHostBuilder est introuvable, un avertissement s’affichera. `.UseUrls()` est requis pour activer l’écoute du trafic entrant par Kestrel, au-delà de localhost lors de l’exécution au sein d’un conteneur Docker. Une fois terminé, le code standard doit ressembler à ce qui suit :

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() a configuré WebHost pour écouter le trafic URL entrant. [Outils docker pour Visual Studio](http://aka.ms/DockerToolsForVS) configurera la variable d’environnement dans le mode dockerfile.debug/release comme suit :

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## Le mappage de volumes ne fonctionne pas
Pour activer les fonctionnalités de modification et d’actualisation, le mappage de volumes est configuré pour partager le code source de votre projet sur le dossier .app au sein du conteneur. Lorsque des fichiers sont modifiés sur votre machine hôte, le répertoire containers/app utilise le même répertoire. Dans docker-compose.debug.yml, la configuration suivante active le mappage de volumes :

```
volumes:
    - ..:/app
```

Pour vérifier si le mappage de volumes fonctionne, exécutez la commande suivante :

**À partir de Windows**

```
a
/ # ls
```

Vous devriez voir une liste de répertoires dans le dossier Users/Public. Si aucun fichier ne s’affiche et que votre dossier /c/Users/Public n’est pas vide, le mappage de volumes n’est pas configuré correctement.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Passez au répertoire wormhole pour voir le contenu du répertoire `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

**Remarque :** *lorsque vous travaillez avec des machines virtuelles Linux, le système de fichiers du conteneur respecte la casse.*

Si vous ne parvenez pas à afficher le contenu, essayez ce qui suit :

**Version bêta de Docker pour Windows**
- Vérifiez que l’application de bureau Docker pour Windows est en cours d’exécution en recherchant l’icône moby dans la barre d’état système, et assurez-vous qu’elle est en blanc et fonctionnelle.
- Vérifiez que le mappage de volumes est configuré en cliquant avec le bouton droit sur l’icône moby dans la barre d’état système, puis en sélectionnant les paramètres et en cliquant sur **Gérer les lecteurs partagés...**

**Boîte à outils Docker avec VirtualBox**

Par défaut, VirtualBox partage `C:\Users` en tant que `c:/Users`. Si possible, déplacez votre projet sous ce répertoire. Sinon, vous pouvez l’ajouter manuellement aux [dossiers partagés](https://www.virtualbox.org/manual/ch04.html#sharedfolders) VirtualBox.
	
##Génération : échec de génération de l’image, erreur lors de la vérification de la connexion TLS : l’hôte n’est pas en cours d’exécution.

- Vérifiez que l’hôte Docker par défaut est en cours d’exécution. Consultez l’article [Configurer le client Docker](./vs-azure-tools-docker-setup.md).

##Utiliser Microsoft Edge en tant que navigateur par défaut

Si vous utilisez le navigateur Microsoft Edge, le site risque de ne pas s’ouvrir, car Edge considère l’adresse IP comme non sécurisée. Pour résoudre ce problème, procédez comme suit :

1. Accédez à **Options Internet**.
    - Sur Windows 10, vous pouvez taper `Internet Options` dans la zone Exécuter.
    - Dans Internet Explorer, vous pouvez accéder au menu **Paramètres**, puis sélectionner **Options Internet**. 
1. Sélectionnez **Options Internet**. 
1. Sélectionnez l’onglet **Sécurité**.
1. Sélectionnez la zone **Intranet local**.
1. Sélectionnez **Sites**. 
1. Ajoutez l’IP de votre machine virtuelle (dans ce cas, l’hôte Docker) à la liste. 
1. Actualisez la page dans Edge et vérifiez que le site est opérationnel. 
1. Pour plus d’informations sur ce problème, reportez-vous au billet de blog de Scott Hanselman [Microsoft Edge can’t see or open VirtualBox-hosted local web sites (Microsoft Edge ne peut pas voir ou ouvrir des sites web locaux hébergés sur VirtualBox)](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx). 

##Résolution des problèmes de la version 0.15 ou version antérieures


###L’exécution de l’application entraîne l’ouverture de PowerShell, qui affiche l’erreur avant de se refermer. La page du navigateur ne s’ouvre pas.

Cela peut être dû à une erreur au cours de `docker-compose-up`. Procédez comme suit pour afficher l’erreur :

1. Ouvrez le fichier `Properties\launchSettings.json`
1. Recherchez l’entrée Docker.
1. Notez la ligne qui commence comme suit :

    ```
    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
    ```
	
1. Ajoutez le paramètre `-noexit` afin que la ligne soit comme suit. Cette opération permet de garder PowerShell ouvert afin que vous puissiez afficher l’erreur.

    ```
	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”
    ```

<!---HONumber=AcomDC_0622_2016-->