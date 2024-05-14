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
```oc cluster-info
```

The Default LDAP passowrd is: adminpassword
```
ldapsearch -x -H ldap://<API Server>:31389 -b "dc=example,dc=org" -D "cn=admin,dc=example,dc=org" -W\n
```

Once all are verified. You can add users to the LDAP server. The user will need to be added using a ldif file. 
