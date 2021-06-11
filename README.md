# Gogs SCM Server on Openshift

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

## III. Deploy Gogs

```bash
# from the root of the source repository
oc apply -k .
```

## IV. Create a Uri Route

```bash
oc expose deployment/gogs
oc expose svc/gogs
gogs_uri=$(oc get routes gogs -o 'jsonpath={.spec.host}')

# print the full uri and navigate to that in your browser
# example: http://gogs-gogs-git-server.apps.okd.thekeunster.local
echo http://$gogs_uri
```

## V. Setup

There will be an initial setup screen. 
- Select "MySql" as the database 
- Adjust the database uri to: "mysql-gogs"
- Adjust the database password to: "password"
- Leave everything else as is, scroll to the bottom and continue

FYI, you may end up at an unreachable address at http://localhost:3000, in which case, redirect yourself to the url as described in step IV.

You should end up at a page that looks like this:

![Screenshot from 2021-06-11 01-10-18](https://user-images.githubusercontent.com/61749/121639258-e2bb0080-ca51-11eb-89e0-c5006745efc3.png)


