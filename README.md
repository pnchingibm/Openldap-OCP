# Openldap-OCP
Setting up OpenLDAP on OpenShift


'''oc new-project openldap
oc adm policy add-scc-to-user anyuid system:serviceaccount:openldap:default'''
or
oc adm policy add-scc-to-user anyuid -z default

The openldap pod should be running without any errors. However, the LDAP server still doesn't have any users. The user will need to be added using a ldif file. 
