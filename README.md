# Gogs on Openshift

## I. Create an Openshift Project

```bash
oc create new-project gogs-scm-server
```

## II. Create/Setup MySql Database Instance

Create the database with the Mysql Persistent Openshift Template

```bash
oc new-app mysql-persistent -p=NAMESPACE=openshift -p=DATABASE_SERVICE_NAME=mysql-gogs -p=MYSQL_DATABASE=gogs -p=MYSQL_USER=gogs -p=MYSQL_PASSWORD=password -p=MYSQL_ROOT_PASSWORD=password -p=MEMORY_LIMIT=1Gi -p=VOLUME_CAPACITY=1Gi -p=MYSQL_VERSION=8.0-el8
```

After the database has been created, open a terminal into the database pod

```bash
mpod=$(oc get pods --selector name=mysql-gogs --output name | awk -F/ '{print $NF}')
oc rsh $mpod
```

Once terminaled into the pod, open the mysql commandline terminal and enter as followed: 

```bash
# terminal shell into the mysql database pod

mysql -u root

# at mysql terminal: 
mysql>GRANT ALL PRIVILEGES ON *.* TO 'gogs'@'%' WITH GRANT OPTION;
mysql>exit;

exit
```

# III. Deploy Gogs

```bash
# from the root of the source repository
oc apply -k .
```