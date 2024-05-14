# Openldap-OCP
Setting up OpenLDAP on OpenShift


```
oc new-project openldap
oc adm policy add-scc-to-user anyuid system:serviceaccount:openldap:default
```

or you can use the following command to set security context. 

```
oc adm policy add-scc-to-user anyuid -z default
```

The openldap pod should be running without any errors. If you need to access the LDAP server outside of the cluster, you can create a Nodeport on the service.

```
oc patch svc openldap -p '{"spec": {"type": "NodePort", "ports": [{"port": 1389, "targetPort": 1389, "nodePort": 31389}]}}'
```

To test the LDAP server, you will need to find the cluster api server
```
oc cluster-info
```

The Default LDAP passowrd is: adminpassword
```
ldapsearch -x -H ldap://<API Server>:31389 -b "dc=example,dc=org" -D "cn=admin,dc=example,dc=org" -W\n
```

Once all are verified. You can add users to the LDAP server. The user will need to be added using a ldif file. 
You can either use the openldap pod terminal from OpenShift Console or rsh to the pod from your machine. 
```
oc get pods
oc rsh <openldap_pod>
```
Use following code to create the ldif file. If you are adding users for IBM RPA, the username needs to be the user name for the Owner (defined in ower secret in your RPA namespace)
```
echo "dn: cn=<username>,ou=users,dc=example,dc=org 
changetype: add
cn: <username>
sn: Bar2
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
userpassword: rpapassw0rd
uid: <username>
uidNumber: 1004
gidNumber: 1004
homeDirectory: /home/<username>
mail: <YOUR_EMAIL>" >> /tmp/addUser.ldif
```
Once the ldif is created, you can add the user to LDAP server by running the following command. 
```
ldapadd -x -D "cn=admin,dc=example,dc=org" -w "adminpassword" -H ldap://localhost:1389 -f addUser.ldif
```
You can verify if the user being added by running the following command. 
```
ldapsearch -x -H ldap://localhost:389 -b "ou=users,dc=example,dc=org" "(uid=<username>)"
```
