---
title: "Connexion d’applications à la base de données Azure pour MySQL | Microsoft Docs"
description: "Ce document répertorie les chaînes de connexion actuellement prises en charge pour les applications qui se connectent à la base de données Azure pour MySQL, notamment ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python et Ruby."
services: mysql
author: mswutao
ms.author: wuta
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/12/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 2f40da41bcfda7e35f6fc63ead5d055246ab390c
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Connexion d’applications à Azure Database pour MySQL
Ce document répertorie les types de chaînes de connexion pris en charge par la base de données Azure pour MySQL, ainsi que des modèles et des exemples. Vous pouvez avoir des paramètres et des réglages différents dans votre chaîne de connexion.

- Pour obtenir le certificat, consultez [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md).
- {votre_hôte} = <servername>.mysql.database.azure.com
- {votre_utilisateur}@{nomserveur} = format ID utilisateur pour une authentification correcte.  L’authentification échouera si vous utilisez uniquement l’ID utilisateur.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Dans cet exemple, le nom du serveur est `myserver4demo`, le nom de la base de données est `wpdb`, le nom d’utilisateur est `WPAdmin` et le mot de passe est `mypassword!2`. La chaîne de connexion devrait être :

```ado.net
Server= "myserver4demo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@myserver4demo"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obtenir les détails de la chaîne de connexion dans le portail Azure
Dans le [portail Azure](https://portal.azure.com), accédez à la base de données Azure pour MySQL Server, puis cliquez sur **Chaînes de connexion** pour obtenir la liste des chaînes de votre instance : ![Le panneau Chaînes de connexion dans le portail Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

La chaîne fournit des détails tels que le pilote, le serveur et d’autres paramètres de connexion de base de données. Modifiez ces exemples en utilisant vos propres paramètres, tels que le nom de la base de données, le mot de passe, etc. Vous pouvez ensuite utiliser cette chaîne pour vous connecter au serveur à partir de votre code et de vos applications.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les bibliothèques de connexions, consultez [Concepts - Bibliothèques de connexions](./concepts-connection-libraries.md).

