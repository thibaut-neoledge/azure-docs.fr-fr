---
title: "Créer une application web Java et MySQL dans Azure | Microsoft Docs"
description: "Découvrez comment obtenir une application Java qui se connecte au service de base de données Azure MySQL dans Azure App Service."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Créer une application web Java et MySQL dans Azure
Ce didacticiel vous montre comment créer une application web Java dans Azure et comment la connecter à une base de données MySQL. La première étape consiste à cloner une application sur votre ordinateur local et à la faire fonctionner avec une instance locale de MySQL.
L’étape suivante consiste à configurer les services Azure pour l’application Java et MySQL, puis à déployer l’application sur un Azure App Service.
Lorsque vous avez terminé, vous disposez d’une application de liste des tâches en cours d’exécution sur Azure et de la connexion au service de base de données Azure MySQL.

![Application Java s’exécutant dans Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>Avant de commencer

Avant d’exécuter cet exemple, tenez compte des conditions préalables suivantes :

1. [Téléchargement et installation de GIT](https://git-scm.com/)
1. [Téléchargement et installation de Java 7 ou version ultérieure](http://Java.net/downloads.Java)
1. [Téléchargement et installation de Maven](https://maven.apache.org/download.cgi)
1. [Téléchargement, installation et démarrage de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Téléchargement et installation de l’interface Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>Préparation de la base de données MySQL locale

Dans cette étape, vous allez créer une base de données dans un serveur MySQL local qui vous sera utile dans ce didacticiel.

### <a name="connect-to-mysql-server"></a>Connexion au serveur MySQL
Connexion à votre serveur MySQL local à partir de la ligne de commande :

```bash
mysql -u root -p
```

Si la commande est exécutée correctement, votre serveur MySQL est déjà en cours d’exécution. Dans le cas contraire, assurez-vous que votre serveur MySQL local est démarré en suivant ces [étapes consécutives à l’installation de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Si vous êtes invité à entrer un mot de passe, tapez le mot de passe du compte `root`. Si vous avez oublié votre mot de passe de compte racine, consultez [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL : réinitialisation du mot de passe racine).


### <a name="create-a-database-and-table"></a>Création d’une base de données et d’une table

Dans l’invite `mysql`, créez une base de données et une table pour les éléments d’action.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

## <a name="create-local-java-application"></a>Création d’une application Java locale
Dans cette étape, vous clonez un référentiel GitHub, configurez la connexion de base de données MySQL et exécutez l’application localement. 

### <a name="clone-the-sample"></a>Clonage de l’exemple

À partir de l’invite de commandes, accédez au répertoire de travail.  

Exécutez les commandes suivantes pour cloner l’exemple de référentiel. 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

Ensuite, configurez lombok.jar en suivant les étapes dans le fichier Lisezmoi du référentiel.


### <a name="configure-mysql-connection"></a>Configuration de la connexion MySQL

Cette application utilise le plug-in Maven Jetty pour exécuter l’application localement et se connecter à la base de données MySQL.
Pour activer l’accès à l’instance locale de MySQL, définissez votre ID d’utilisateur MySQL local et votre mot de passe dans WebContent/WEB-INF/jetty-env.xml.

Mettez à jour les valeurs d’utilisateur et de mot de passe avec l’ID d’utilisateur et le mot de passe de votre instance locale de MySQL :

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt;Pour plus d’informations sur la manière dont Jetty utilise le fichier `jetty-env.xml`, consultez la [référence XML Jetty](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html).
&gt; &gt;

### <a name="run-the-sample"></a>Exécution de l'exemple

Utilisez une commande Maven pour exécuter l’exemple : 

```bash
mvn package jetty:run
```

Ensuite, dans un navigateur, accédez à `http://localhost:8080`. Ajoutez quelques tâches dans la page.

Pour arrêter l’application à tout moment, tapez `Ctrl`+`C` à l’invite de commandes. 

## <a name="create-a-mysql-database-in-azure"></a>Création d’une base de données MySQL dans Azure

Dans cette étape, vous créez une base de données MySQL dans [Azure Database pour MySQL (Version préliminaire)](/azure/mysql). Ensuite, vous configurerez votre application Java pour vous connecter à cette base de données.

### <a name="log-in-to-azure"></a>Connexion à Azure

Utilisez l’interface Azure CLI 2.0 dans une fenêtre de terminal pour créer les ressources nécessaires pour héberger votre application Java dans Azure App Service. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources dans la région Europe du Nord :

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

Pour connaître les valeurs disponibles pour `--location`, utilisez la commande [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Création d’un serveur MySQL

Créez un serveur Azure Database pour MySQL (préversion) avec la commande [az mysql server create](/cli/azure/mysql/server#create).

Indiquez le nom unique de votre propre serveur MySQL là où se trouve l’espace réservé `&lt;mysql_server_name&gt;`. Ce nom fait partie du nom d’hôte de votre serveur MySQL, `&lt;mysql_server_name&gt;.database.windows.net`, et doit donc être globalement unique. Remplacez également `&lt;admin_user&gt;` et `&lt;admin_password&gt;` par vos propres valeurs.

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

Lorsque le serveur MySQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>Configuration d’un pare-feu de serveur

Créez une règle de pare-feu pour votre serveur MySQL afin d’autoriser les connexions client à l’aide de la commande [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; Azure Database pour MySQL (version préliminaire) ne permet pas encore les connexions uniquement à partir des services Azure. Étant donné que les adresses IP sont affectées dynamiquement dans Azure, il est préférable d’activer toutes les adresses IP pour le moment. Le service est pour le moment disponible en version préliminaire, mais de meilleures méthodes de sécurisation de votre base de données seront bientôt activées.
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>Connexion au serveur MySQL

Dans la fenêtre de terminal, connectez-vous au serveur MySQL dans Azure. Utilisez la valeur que vous avez spécifiée précédemment pour `&lt;admin_user&gt;` et `&lt;mysql_server_name&gt;`.

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>Création d’une base de données et d’une table dans le service MySQL Azure

Dans l’invite `mysql`, créez une base de données et une table pour les éléments d’action.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>Création d’un utilisateur avec des autorisations

Créez un utilisateur de base de données et accordez-lui tous les privilèges dans la base de données `todoItemDb`. Remplacez les espaces réservés `&lt;Javaapp_user&gt;` et `&lt;Javaapp_password&gt;` avec le nom de votre propre application unique.

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>Configuration de la connexion MySQL locale avec le nouveau service Azure MySQL

Dans cette étape, vous allez connecter votre application Java à la base de données MySQL que vous avez créée dans Azure Database pour MySQL (version préliminaire). 

Pour activer l’accès à partir de l’application locale au service Azure MySQL, définissez vos nouveaux point de terminaison, ID d’utilisateur et mot de passe MySQL dans WebContent/WEB-INF/jetty-env.xml :

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

Enregistrez vos modifications.

## <a name="test-the-application"></a>Test de l'application

Utilisez la même commande Maven qu’auparavant pour exécuter l’exemple localement à nouveau, mais cette fois en vous connectant au service Azure MySQL : 

```bash
mvn package jetty:run
```

Dans un navigateur, accédez à `http://localhost:8080`. Si la page se charge sans erreur, votre application Java se connecte à la base de données MySQL dans Azure. 

Vous ne devriez pas voir la mention Ajoutez quelques tâches dans la page.

Pour arrêter l’application à tout moment, tapez `Ctrl`+`C` dans le terminal. 

### <a name="secure-sensitive-data"></a>Données sensibles sécurisées

Assurez-vous que les données sensibles dans `WebContent/WEB-INF/jetty-env.xml` ne sont pas validées dans Git.

Pour ce faire, ouvrez `.gitignore` à partir du référentiel racine et ajoutez `WebContent/WEB-INF/jetty-env.xml` dans une nouvelle ligne. Enregistrez vos modifications.

Validez vos modifications apportées à `.gitignore`.

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>Déploiement de l’application Java dans Azure
Ensuite, nous déployons l’application Java sur un Azure App Service.

### <a name="create-an-appservice-plan"></a>Création d’un plan App Service

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan#create). 

&gt; [!NOTE] 
&gt; Un plan App Service représente la collection des ressources physiques utilisées pour héberger vos applications. Toutes les applications affectées à un plan App Service partagent les ressources qu’il définit, ce qui vous permet de réduire les coûts lors de l’hébergement de plusieurs applications. 
&gt; &gt; Les plans App Service définissent : &gt; &gt; * Région (Europe du Nord, États-Unis de l’Est, Sud-Est asiatique) &gt; * Taille d’instance (« Petit », « Moyen » ou « Grand ») &gt; * Comptage (une, deux ou trois instances, etc.) &gt; * Référence (SKU) (gratuit, partagé, basique, standard, premium) &gt; 

L’exemple suivant crée un plan App Service nommé `myAppServicePlan` en appliquant le niveau tarifaire **GRATUIT** :

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>Création d’une application web Azure

Maintenant qu’un plan App Service est créé, créez une application web Azure dans le plan App Service `myAppServicePlan`. L’application web vous offre un espace d’hébergement pour déployer votre code, et fournit une URL pour vous permettre d’afficher l’application déployée. Utilisez la commande [az appservice web create](/cli/azure/appservice/web#create) pour créer l’application web. 

Dans la commande suivante, remplacez l’espace réservé `&lt;app_name&gt;` par le nom unique de votre propre application. Ce nom unique sera utilisé dans le nom de domaine par défaut de l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez ultérieurement mapper toute entrée DNS personnalisée vers l’application web avant de l’exposer à vos utilisateurs. 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant : 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>Définition de la version de Java, du type de serveur d’applications Java et de la version du serveur d’applications

Définissez la version Java, le serveur d’applications Java (conteneur) et la version du conteneur à l’aide de la commande [az appservice web config update](/cli/azure/appservice/web/config#update).

La commande suivante définit la version de Java à 8, le serveur d’applications Java sur Jetty, et la version de Jetty à la version 9.3, la plus récente.

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>Obtention d’informations d’identification pour le déploiement de l’application web à l’aide du FTP 

Vous pouvez déployer votre application dans Azure App Service de plusieurs façons, notamment FTP, Git local ainsi que GitHub, Visual Studio Team Services et BitBucket. Pour cet exemple, nous utilisons Maven pour compiler un fichier .WAR, et le FTP pour déployer le fichier .WAR sur l’application web

Pour déterminer les informations d’identification à transmettre dans une commande ftp à l’application web, utilisez la commande [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles), comme suit : 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>Compilation de l’application locale à déployer sur l’application web 

Pour préparer l’application Java locale afin qu’elle s’exécute sur l’application web Azure, recompilez toutes les ressources dans l’application Java en un seul fichier .WAR prêt pour le déploiement. Accédez au répertoire où se trouve le fichier pom.xml d’applications et tapez :

```bash 
mvn clean package
``` 
Vers la fin du processus de package Maven, vous verrez l’emplacement du fichier .WAR.  Le résultat suivant doit ressembler à ce qui suit :

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

Notez l’emplacement du fichier .WAR et utilisez votre méthode FTP préférée pour déployer le fichier .WAR dans le dossier Jetty WebApps.  Notez que le nom de dossier Jetty WebApps se trouve à l’emplacement /site/wwwroot/webapps dans une application web Azure. 

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à `http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` et ajoutez quelques tâches à la liste. 

![Application Java s’exécutant dans Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**Félicitations !** Vous exécutez une application Java orientée données dans Azure App Service.
Pour mettre à jour l’application, répétez la commande de package propre maven et redéployez l’application via le FTP.

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au portail Azure pour voir l’application web que vous avez créée en vous connectant à [https://portal.azure.com](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **App Service**, puis cliquez sur le nom de votre application web Azure.

Vous devriez maintenant être dans le _panneau_ de votre application web (une page du portail qui s’ouvre horizontalement).

Par défaut, le panneau de votre application web affiche la page **Présentation**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets figurant sur le côté gauche du panneau affichent les différentes pages de configuration que vous pouvez ouvrir.

Dans la page **Paramètres d’application**, 

![Paramètres d’application de l’application web Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



Ces onglets affichent les nombreuses fonctionnalités exceptionnelles que vous pouvez ajouter à votre application web. La liste suivante fournit quelques exemples des possibilités :

* Mapper un nom DNS personnalisé
* Lier un certificat SSL personnalisé
* Configurer le déploiement continu
* Montée en puissance et augmentation de la taille des instances
* Ajouter une authentification utilisateur

## <a name="more-resources"></a>Autres ressources

- [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)
- [Lier un certificat SSL existant à des applications web Azure](app-service-web-tutorial-custom-ssl.md)
- [Scripts de l’interface de ligne de commande de Web Apps](app-service-cli-samples.md)</PRE>

