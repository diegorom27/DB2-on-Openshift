# Documentación de Configuración para DB2 en OpenShift

###Antes de empezar

-Revisar que exista el storageClass referenciado en la PVC.
-Anadir al storageClass usado la sgt configuracion:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ibmc-vpc-block-10iops-tier
mountOptions:
  - suid
```

### Crear namespace
Crea un nuevo proyecto (namespace) para la implementación de DB2.
```bash
oc new-project db2-test-4
```
### Crear scc
Aplica la configuración de SCC desde el archivo scc.yml.
```bash
oc apply -f scc.yml
```
### Crear service account
Crea una cuenta de servicio necesaria para el acceso a los recursos.
```bash
oc create serviceaccount db2-sa -n db2-test-4
```
### Realizar binding entre scc y service account
```bash
oc create rolebinding db2-sa-scc-binding \
--clusterrole=system:openshift:scc:db2u-scc \
--serviceaccount=db2-test-4:db2-sa \
--namespace=db2-test-4
```
###  Crear pvc
Aplica la configuración de PVC desde el archivo pvc.yaml.
```bash
oc apply -f pvc.yaml
```
### Crear deployment 
Crea el deployment de DB2 usando el archivo deployment.yaml.
```bash
oc apply -f deployment.yaml
```
### Verifica service account en los pods
Revisa la configuración de la cuenta de servicio en los pods existentes.
```bash
oc describe pod <pod-name> -n db2-test-4
```
### Login
Entrar al pod del db2 por la consola de openshift
```shell
su - db2inst1

db2

CONNECT TO testdb

CREATE TABLE employees(id INT PRIMARY KEY NOT NULL, name VARCHAR(50), salary DECIMAL(10, 2))

INSERT INTO employees (id, name, salary) VALUES (1, 'Alice', 50000.00)

SELECT * FROM employees

```
### Documentacion img

https://hub.docker.com/r/ibmcom/db2

### Documentacion Adicional(scc)

https://www.ibm.com/docs/en/db2/11.5?topic=db2-deploying