---
title: "Configuration de la connectivité SSL pour se connecter en toute sécurité à la base de données Azure pour MySQL | Microsoft Docs"
description: "Instructions pour configurer correctement la base de données Azure pour MySQL et les applications associées afin d’utiliser correctement les connexions SSL"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 07/28/2017
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 77e1b6266a2cf47949fa06358ec003f6b6b38065
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL
La base de données Azure pour MySQL prend en charge la connexion de votre serveur Azure Database pour MySQL aux applications clientes à l’aide de Secure Sockets Layer (SSL). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="step-1-obtain-ssl-certificate"></a>Étape 1 : obtention d’un certificat SSL
Téléchargez le certificat nécessaire pour communiquer via le protocole SSL avec votre serveur Azure Database pour MySQL à partir de [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) et enregistrez le fichier de certificat sur votre disque local (dans ce didacticiel, nous avons utilisé c:\ssl).
**Pour Microsoft Internet Explorer et Microsoft Edge :** une fois le téléchargement terminé, renommez le certificat BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Étape 2 : création d’une liaison SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Connexion au serveur à l’aide de MySQL Workbench via le protocole SSL
Configurez MySQL Workbench pour vous connecter en toute sécurité via le protocole SSL. Accédez à l’onglet **SSL** dans MySQL Workbench, dans la boîte de dialogue Configurer une nouvelle connexion. Renseignez l’emplacement du fichier **BaltimoreCyberTrustRoot.crt.pem** dans le champ **SSL CA File:** (Fichier CA SSL :).
![Enregistrement d’une mosaïque personnalisée](./media/howto-configure-ssl/mysql-workbench-ssl.png)

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Connexion au serveur à l’aide de l’interface de ligne de commande MySQL via le protocole SSL
À l’aide de l’interface de ligne de commande MySQL, exécutez la commande suivante :
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Étape 3 : application des connexions SSL dans Azure 
### <a name="using-azure-portal"></a>En passant par le portail Azure
À l’aide du portail Azure, accédez à votre serveur Azure Database pour MySQL et cliquez sur **Sécurité de la connexion**. Utilisez le bouton bascule pour activer ou désactiver le paramètre **Appliquer une connexion SSL**. Cliquez ensuite sur **Enregistrer**. Microsoft Azure recommande de toujours activer le paramètre **Appliquer une connexion SSL** pour renforcer la sécurité.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Vous pouvez activer ou désactiver le paramètre **ssl-mise en œuvre** en utilisant respectivement les valeurs Activé ou Désactivé dans l’interface de ligne de commande Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-ssl-connection"></a>Étape 4 : vérification de votre connexion SSL
Exécuter la commande mysql **status** pour vérifier que vous êtes connecté à votre serveur MySQL à l’aide de SSL :
```dos
mysql> status
```
Confirmez le chiffrement de la connexion en vérifiant la sortie. Elle doit indiquer : **SSL: Cipher in use is AES256-SHA** (SSL : le chiffrement utilisé est AES256-SHA). 

## <a name="sample-code"></a>Exemple de code
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>Étapes suivantes
Passez en revue les différentes options de connectivité d’application de la rubrique [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md)

