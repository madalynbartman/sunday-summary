## Breaking Down Helm Chart Output

# Release Information
The first section has the Release Information

```yaml
NAME: mydb
LAST DEPLOYED: Thu Mar 13 14:10:10 2025
NAMESPACE: default
STATUS: pending-install
REVISION: 1
TEST SUITE: None
HOOKS:
MANIFEST:
```
# Key Section
The second section is the Key Section (Kuberentes templates are dumped here)

```yaml
---
# Source: mysql/templates/networkpolicy.yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: mydb-mysql
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
spec:
  podSelector:
    matchLabels:
      app.kubernetes.io/instance: mydb
      app.kubernetes.io/managed-by: Helm
      app.kubernetes.io/name: mysql
      app.kubernetes.io/version: 8.4.4
      helm.sh/chart: mysql-12.3.1
  policyTypes:
    - Ingress
    - Egress
  egress:
    - {}
  ingress:
    # Allow connection from other cluster pods
    - ports:
        - port: 3306
```
```yaml
---
# Source: mysql/templates/primary/pdb.yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: mydb-mysql
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
    app.kubernetes.io/component: primary
spec:
  maxUnavailable: 1
  selector:
    matchLabels:
      app.kubernetes.io/instance: mydb
      app.kubernetes.io/name: mysql
      app.kubernetes.io/part-of: mysql
      app.kubernetes.io/component: primary
```
```yaml
---
# Source: mysql/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: mydb-mysql
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
automountServiceAccountToken: false
secrets:
  - name: mydb-mysql
```
```yaml
---
# Source: mysql/templates/secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mydb-mysql
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
type: Opaque
data:
  mysql-root-password: "dGVzdDEyMzQ="
  mysql-password: "RVVtWG96cDNPRQ=="
```
```yaml
---
# Source: mysql/templates/primary/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mydb-mysql
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
    app.kubernetes.io/component: primary
data:
  my.cnf: |-
    [mysqld]
    authentication_policy='* ,,'
    skip-name-resolve
    explicit_defaults_for_timestamp
    basedir=/opt/bitnami/mysql
    plugin_dir=/opt/bitnami/mysql/lib/plugin
    port=3306
    mysqlx=0
    mysqlx_port=33060
    socket=/opt/bitnami/mysql/tmp/mysql.sock
    datadir=/bitnami/mysql/data
    tmpdir=/opt/bitnami/mysql/tmp
    max_allowed_packet=16M
    bind-address=*
    pid-file=/opt/bitnami/mysql/tmp/mysqld.pid
    log-error=/opt/bitnami/mysql/logs/mysqld.log
    character-set-server=UTF8
    slow_query_log=0
    long_query_time=10.0
    
    [client]
    port=3306
    socket=/opt/bitnami/mysql/tmp/mysql.sock
    default-character-set=UTF8
    plugin_dir=/opt/bitnami/mysql/lib/plugin
    
    [manager]
    port=3306
    socket=/opt/bitnami/mysql/tmp/mysql.sock
    pid-file=/opt/bitnami/mysql/tmp/mysqld.pid
```
```yaml
---
# Source: mysql/templates/primary/svc-headless.yaml
apiVersion: v1
kind: Service
metadata:
  name: mydb-mysql-headless
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
    app.kubernetes.io/component: primary
spec:
  type: ClusterIP
  clusterIP: None
  publishNotReadyAddresses: true
  ports:
    - name: mysql
      port: 3306
      targetPort: mysql
  selector:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/name: mysql
    app.kubernetes.io/component: primary
```
```yaml
---
# Source: mysql/templates/primary/svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: mydb-mysql
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
    app.kubernetes.io/component: primary
spec:
  type: ClusterIP
  sessionAffinity: None
  ports:
    - name: mysql
      port: 3306
      protocol: TCP
      targetPort: mysql
      nodePort: null
  selector:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/name: mysql
    app.kubernetes.io/part-of: mysql
    app.kubernetes.io/component: primary
```
```yaml
---
# Source: mysql/templates/primary/statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mydb-mysql
  namespace: "default"
  labels:
    app.kubernetes.io/instance: mydb
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: mysql
    app.kubernetes.io/version: 8.4.4
    helm.sh/chart: mysql-12.3.1
    app.kubernetes.io/part-of: mysql
    app.kubernetes.io/component: primary
spec:
  replicas: 1
  podManagementPolicy: ""
  selector:
    matchLabels:
      app.kubernetes.io/instance: mydb
      app.kubernetes.io/name: mysql
      app.kubernetes.io/part-of: mysql
      app.kubernetes.io/component: primary
  serviceName: mydb-mysql-headless
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      annotations:
        checksum/configuration: 983f9661977665ed8f98b7fd7356be81701d4c7bcb2bde74af23b0d005b57358
      labels:
        app.kubernetes.io/instance: mydb
        app.kubernetes.io/managed-by: Helm
        app.kubernetes.io/name: mysql
        app.kubernetes.io/version: 8.4.4
        helm.sh/chart: mysql-12.3.1
        app.kubernetes.io/part-of: mysql
        app.kubernetes.io/component: primary
    spec:
      serviceAccountName: mydb-mysql
      
      automountServiceAccountToken: false
      affinity:
        podAffinity:
          
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/instance: mydb
                    app.kubernetes.io/name: mysql
                topologyKey: kubernetes.io/hostname
              weight: 1
        nodeAffinity:
          
      securityContext:
        fsGroup: 1001
        fsGroupChangePolicy: Always
        supplementalGroups: []
        sysctls: []
      initContainers:
        - name: preserve-logs-symlinks
          image: docker.io/bitnami/mysql:8.4.4-debian-12-r4
          imagePullPolicy: "IfNotPresent"
          securityContext:
            allowPrivilegeEscalation: false
            capabilities:
              drop:
              - ALL
            readOnlyRootFilesystem: true
            runAsGroup: 1001
            runAsNonRoot: true
            runAsUser: 1001
            seLinuxOptions: {}
            seccompProfile:
              type: RuntimeDefault
          resources:
            limits:
              cpu: 750m
              ephemeral-storage: 2Gi
              memory: 768Mi
            requests:
              cpu: 500m
              ephemeral-storage: 50Mi
              memory: 512Mi
          command:
            - /bin/bash
          args:
            - -ec
            - |
              #!/bin/bash

              . /opt/bitnami/scripts/libfs.sh
              # We copy the logs folder because it has symlinks to stdout and stderr
              if ! is_dir_empty /opt/bitnami/mysql/logs; then
                cp -r /opt/bitnami/mysql/logs /emptydir/app-logs-dir
              fi
          volumeMounts:
            - name: empty-dir
              mountPath: /emptydir
      containers:
        - name: mysql
          image: docker.io/bitnami/mysql:8.4.4-debian-12-r4
          imagePullPolicy: "IfNotPresent"
          securityContext:
            allowPrivilegeEscalation: false
            capabilities:
              drop:
              - ALL
            readOnlyRootFilesystem: true
            runAsGroup: 1001
            runAsNonRoot: true
            runAsUser: 1001
            seLinuxOptions: {}
            seccompProfile:
              type: RuntimeDefault
          env:
            - name: BITNAMI_DEBUG
              value: "false"
            - name: MYSQL_ROOT_PASSWORD_FILE
              value: /opt/bitnami/mysql/secrets/mysql-root-password
            - name: MYSQL_ENABLE_SSL
              value: "no"
            - name: MYSQL_PORT
              value: "3306"
            - name: MYSQL_DATABASE
              value: "my_database"
          envFrom:
          ports:
            - name: mysql
              containerPort: 3306
          livenessProbe:
            failureThreshold: 3
            initialDelaySeconds: 5
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MYSQL_ROOT_PASSWORD:-}"
                  if [[ -f "${MYSQL_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MYSQL_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin status -uroot -p"${password_aux}"
          readinessProbe:
            failureThreshold: 3
            initialDelaySeconds: 5
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MYSQL_ROOT_PASSWORD:-}"
                  if [[ -f "${MYSQL_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MYSQL_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin ping -uroot -p"${password_aux}" | grep "mysqld is alive"
          startupProbe:
            failureThreshold: 10
            initialDelaySeconds: 15
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MYSQL_ROOT_PASSWORD:-}"
                  if [[ -f "${MYSQL_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MYSQL_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin ping -uroot -p"${password_aux}" | grep "mysqld is alive"
          resources:
            limits:
              cpu: 750m
              ephemeral-storage: 2Gi
              memory: 768Mi
            requests:
              cpu: 500m
              ephemeral-storage: 50Mi
              memory: 512Mi
          volumeMounts:
            - name: data
              mountPath: /bitnami/mysql
            - name: empty-dir
              mountPath: /tmp
              subPath: tmp-dir
            - name: empty-dir
              mountPath: /opt/bitnami/mysql/conf
              subPath: app-conf-dir
            - name: empty-dir
              mountPath: /opt/bitnami/mysql/tmp
              subPath: app-tmp-dir
            - name: empty-dir
              mountPath: /opt/bitnami/mysql/logs
              subPath: app-logs-dir
            - name: config
              mountPath: /opt/bitnami/mysql/conf/my.cnf
              subPath: my.cnf
            - name: mysql-credentials
              mountPath: /opt/bitnami/mysql/secrets/
      volumes:
        - name: config
          configMap:
            name: mydb-mysql
        - name: mysql-credentials
          secret:
            secretName: mydb-mysql
            items:
              - key: mysql-root-password
                path: mysql-root-password
              - key: mysql-password
                path: mysql-password
        - name: empty-dir
          emptyDir: {}
  volumeClaimTemplates:
    - metadata:
        name: data
        labels:
          app.kubernetes.io/instance: mydb
          app.kubernetes.io/name: mysql
          app.kubernetes.io/component: primary
      spec:
        accessModes:
          - "ReadWriteOnce"
        resources:
          requests:
            storage: "8Gi"
```
# The third and final section is the Release Notes:
```yaml
NOTES:
CHART NAME: mysql
CHART VERSION: 12.3.1
APP VERSION: 8.4.4
```

```text
Did you know there are enterprise versions of the Bitnami catalog? For enhanced secure software supply chain features, unlimited pulls from Docker, LTS support, or application customization, see Bitnami Premium or Tanzu Application Catalog. See https://www.arrow.com/globalecs/na/vendors/bitnami for more information.

** Please be patient while the chart is being deployed **

Tip:

  Watch the deployment status using the command: kubectl get pods -w --namespace default

Services:

  echo Primary: mydb-mysql.default.svc.cluster.local:3306

Execute the following to get the administrator credentials:

  echo Username: root
  MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default mydb-mysql -o jsonpath="{.data.mysql-root-password}" | base64 -d)

To connect to your database:

  1. Run a pod that you can use as a client:

      kubectl run mydb-mysql-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mysql:8.4.4-debian-12-r4 --namespace default --env MYSQL_ROOT_PASSWORD=$MYSQL_ROOT_PASSWORD --command -- bash

  2. To connect to primary service (read/write):

      mysql -h mydb-mysql.default.svc.cluster.local -uroot -p"$MYSQL_ROOT_PASSWORD"






WARNING: There are "resources" sections in the chart not set. Using "resourcesPreset" is not recommended for production. For production installations, please set the following values according to your workload needs:
  - primary.resources
  - secondary.resources
+info https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
```