---
title: Utiliser Apache Beeline avec Apache Hive - Azure HDInsight
description: Découvrez comment utiliser le client Beeline pour exécuter des requêtes Hive avec Hadoop sur HDInsight. Beeline est un utilitaire qui permet d’utiliser HiveServer2 sur JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: hrasheed
ms.openlocfilehash: 392c34e1896106c39b31876308084ef4fd6a7e54
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449047"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Utiliser le client Apache Beeline avec Apache Hive

Découvrez comment utiliser [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) pour exécuter des requêtes Apache Hive sur HDInsight.

Beeline est un client Hive inclus dans les nœuds principaux de votre cluster HDInsight. Beeline utilise JDBC pour se connecter à HiveServer2, un service hébergé sur votre cluster HDInsight. Vous pouvez également utiliser Beeline pour accéder à Hive sur HDInsight à distance via internet. Les exemples suivants présentent les chaînes de connexion les plus courantes utilisées pour se connecter à HDInsight à partir de Beeline :

* __Utilisation de Beeline à partir d’une connexion SSH vers un nœud principal ou un nœud de périphérie__ : `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`

* __Utilisation de Beeline sur un client, se connectant à HDInsight sur un réseau virtuel Azure__ :`-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

* __Utilisation de Beeline sur un client, se connectant à un cluster Pack Sécurité Entreprise HDInsight sur un réseau virtuel Azure__ : `-u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-DOMAIN>;auth-kerberos;transportMode=http' -n <username>` 

* __Utilisation de Beeline sur un client, se connectant à HDInsight sur l’internet public__ :`-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]  
> Remplacez `admin` par le compte de connexion de votre cluster.
>
> Remplacez `password` par le mot de passe du compte de connexion du cluster.
>
> Remplacez `clustername` par le nom de votre cluster HDInsight :
>
> Lors de la connexion au cluster via un réseau virtuel, remplacez `<headnode-FQDN>` par le nom de domaine complet d’un nœud principal de cluster.
>
> Lors de la connexion à un cluster Pack Sécurité Entreprise, remplacez `<AAD-DOMAIN>` par le nom de l'instance Azure Active Directory (AAD) à laquelle le cluster est joint. Utiliser une chaîne en majuscules pour les `<AAD-DOMAIN>` valeur, sinon les informations d’identification ne peut pas être trouvée. Vérifiez `/etc/krb5.conf` pour les noms de domaine si nécessaire. Remplacez `<username>` par le nom d’un compte de domaine autorisé à accéder au cluster. 

## <a id="prereq"></a>Configuration requise

* Un cluster Hadoop Linux sur HDInsight version 3.4 ou ultérieure.

  > [!IMPORTANT]  
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un client SSH ou un client Beeline local. La plupart des étapes décrites dans ce document supposent que vous utilisez Beeline à partir d’une session SSH sur le cluster. Pour plus d’informations sur l’exécution de Beeline à partir de l’extérieur du cluster, voir la section [Utiliser Beeline à distance](#remote).

    Pour plus d’informations sur l’utilisation de SSH, voir [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Exécuter une requête Hive

1. Lors du démarrage de Beeline, vous devez fournir une chaîne de connexion pour HiveServer2 sur votre cluster HDInsight :

    * Lors de la connexion via l’internet public, vous devez fournir le nom de compte de connexion de cluster (par défaut `admin`) et le mot de passe. Par exemple, utilisation de Beeline à partir d’un système client pour se connecter à l’adresse `<clustername>.azurehdinsight.net`. Cette connexion est établie via le port `443`et est chiffrée à l’aide de SSL :

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Lors de la connexion à partir d’une session SSH à un nœud principal de cluster, vous pouvez vous connecter à l’adresse `headnodehost` sur le port `10001` :

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Lors de la connexion sur un réseau virtuel Azure, vous devez fournir le nom de domaine complet d’un nœud principal de cluster. Puisque cette connexion est établie directement aux nœuds de cluster, la connexion utilise le port `10001` :

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```
    * Lors de la connexion à un cluster Pack Sécurité Entreprise joint à Azure Active Directory (AAD), vous devez également spécifier le nom de domaine `<AAD-Domain>` et le nom d’un compte d’utilisateur de domaine autorisé à accéder au cluster `<username>` :
        
        ```bash
        kinit <username>
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
        ```

2. Les commandes Beeline commencent par un caractère `!`. Par exemple, `!help` affiche l’aide. Toutefois, `!` peut être omis pour certaines commandes. Par exemple, `help` fonctionne également.

    `!sql` est utilisé pour exécuter des instructions HiveQL. Cependant, HiveQL est si souvent utilisé que vous pouvez omettre le `!sql`qui précède. Les deux instructions suivantes sont équivalentes :

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Sur un nouveau cluster, une seule table est listée : **hivesampletable**.

3. Utilisez la commande suivante pour afficher le schéma correspondant à hivesampletable :

    ```hiveql
    describe hivesampletable;
    ```

    Cette commande renvoie les informations suivantes :

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Ces informations décrivent les colonnes de la table.

4. Saisissez les instructions suivantes pour créer une table nommée **log4jLogs** avec les exemples de données fournies avec le cluster HDInsight :

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Ces instructions effectuent les opérations suivantes :

    * `DROP TABLE`: si la table existe, elle est supprimée.

    * `CREATE EXTERNAL TABLE` : crée une table **externe** dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.

    * `ROW FORMAT` : formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.

    * `STORED AS TEXTFILE LOCATION` : emplacement de stockage des données et format de fichier.

    * `SELECT` : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette requête renvoie la valeur **3**, car trois lignes contiennent cette valeur.

    * `INPUT__FILE__NAME LIKE '%.log'` : Hive tente d’appliquer le schéma à tous les fichiers dans le répertoire. Dans ce cas, le répertoire contient des fichiers qui ne correspondent pas au schéma. Pour éviter que des données incorrectes n’apparaissent dans les résultats, cette instruction indique à Hive de retourner uniquement des données provenant de fichiers se terminant par .log.

   > [!NOTE]  
   > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, Par exemple, un processus de chargement de données automatisé ou une opération MapReduce.
   >
   > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

    Le résultat de la commande se présente ainsi :

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Pour quitter Beeline, utilisez `!exit`.

### <a id="file"></a>Utiliser Beeline pour exécuter un fichier HiveQL

Utilisez les étapes suivantes pour créer un fichier, puis exécutez-le à l’aide de Beeline.

1. Utilisez la commande suivante pour créer un fichier nommé **query.hql**:

    ```bash
    nano query.hql
    ```

2. Utilisez le texte ci-après comme contenu du fichier. Cette requête crée une table « interne » nommée **errorLogs** :

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ces instructions effectuent les opérations suivantes :

   * **CREATE TABLE IF NOT EXISTS** : si la table n’existe pas, elle est créée. Étant donné que le mot-clé **EXTERNAL** n’est pas utilisé, cette instruction crée une table interne. Les tables internes sont stockées dans l’entrepôt de données Hive et entièrement gérées par Hive.
   * **STORED AS ORC** : stocke les données dans un format ORC (Optimized Row Columnar). Le format ORC est particulièrement efficace et optimisé pour le stockage de données Hive.
   * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.

     > [!NOTE]  
     > Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

3. Pour enregistrer le fichier, utilisez **Ctrl**+_+**X**, saisissez ensuite **Y**, puis appuyez sur **Entrée**.

4. Pour exécuter le fichier à l’aide de Beeline, utilisez les éléments suivants :

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Le paramètre `-i` démarre Beeline et exécute les instructions dans le fichier `query.hql`. Une fois la requête terminée, vous accédez à l’invite `jdbc:hive2://headnodehost:10001/>`. Vous pouvez également exécuter un fichier avec le paramètre `-f`, qui ferme Beeline une fois la requête terminée.

5. Pour vérifier que la table **errorLogs** a été créée, utilisez l’instruction suivante pour renvoyer toutes les lignes à partir **d’errorLogs** :

    ```hiveql
    SELECT * from errorLogs;
    ```

    Trois lignes de données doivent normalement être renvoyées. Elles contiennent toutes **[ERROR]** dans la colonne t4 :

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Utiliser Beeline à distance

Si vous avez Beeline installé localement et que vous vous connectez via l’internet public, utilisez les paramètres suivants :

* __Chaîne de connexion__ : `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Nom de connexion du cluster__ : `-n admin`

* __Mot de passe de connexion du cluster__ : `-p 'password'`

Remplacez le `clustername` de la chaîne de connexion par le nom de votre cluster HDInsight.

Remplacez `admin` par le nom de connexion de votre cluster, puis remplacez `password` par le mot de passe de votre cluster.

Si vous avez Beeline installé localement et que vous vous connectez sur un réseau virtuel Azure, utilisez les paramètres suivants :

* __Chaîne de connexion__ : `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Pour rechercher le nom de domaine complet d’un nœud principal, utilisez les informations contenues dans le document [Gérer des clusters HDInsight à l’aide de l’API REST d’Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

## <a id="sparksql"></a>Utiliser Beeline avec Apache Spark

Apache Spark fournit sa propre implémentation de HiveServer2, qui est quelquefois appelée « serveur Spark Thrift ». Ce service utilise Spark SQL pour résoudre les requêtes à la place de Hive, et peut offrir de meilleures performances selon la requête.

La __chaîne de connexion__ utilisée lors de la connexion à Internet est légèrement différente. Au lieu de contenir `httpPath=/hive2`, elle contient `httpPath/sparkhive2`. Voici un exemple de connexion à Internet :

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Lorsque vous vous connectez directement depuis le nœud principal du cluster, ou à partir d’une ressource à l’intérieur du même réseau virtuel Azure que le cluster HDInsight, le port `10002` doit être utilisé pour le serveur Spark Thrift à la place de `10001`. Voici un exemple de connexion directe au nœud principal :

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a id="summary"></a><a id="nextsteps"></a>Étapes suivantes

Pour plus d’informations générales sur Hive sur HDInsight, consultez le document suivant :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres façons de travailler avec Hadoop sur HDInsight, consultez les documents suivants :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
