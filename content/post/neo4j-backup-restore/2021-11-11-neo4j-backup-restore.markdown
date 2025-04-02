---
title:  "Backup and Restore Neo4j in a Casual Cluster"
categories: 
    - kubernetes
    - architecture
tags: 
    - causual cluster
    - neo4j
    - backup & restore
    - kubernetes
    - architecture
date: 2021-11-11
---

If you're managing a data engine inside a kubernetes cluster then implementing a backup and restore process can be challenging. A few months ago I developed a solution architecture deploying Neo4j into Kubernetes as a casual cluster. There's a Medium post by Neo4j's David Allen to explain what that configuration looks like <a target="_new" href="https://medium.com/neo4j/querying-neo4j-clusters-7d6fde75b5b4">here</a>. Unfortunately Neo4j doesn't officially support a casual cluster deployment, but there are community maintained helm charts endorsed by Neo4j that make this achieveable. For this solution I needed a simple backup and restore (nothing more) which is what I wanted to focus on here.

![GitHub Logo](/assets/images/containers.jpg)
Source: https://www.pexels.com/

## Technology-native versus Snapshots

The approach of snapshotting persistent volumes for a distributed data engine as a means to backup data can and does lead to situations where a subsequent restore will fail because of an inconsistent state. In this situation a transactional database should run the transactions from the write-ahead logs but I ran into this exact issue when testing this approach with Velero and Neo4j and was unable to complete the restore. Postgres and timescaledb also failed to restore using this approach. Implementing a primary backup and restore mechanism using the officially supported, native database tools (for neo4j the neo4j-backup utility) is my recommended approach.

For Neo4j, the community helm chart includes a child helm chart for backing up to AWS, GCP or Azure. The helm chart utilises the neo4j-admin backup image provided by Neo4j which runs as a sidecar to neo4j. That approach works well if you want to backup to these providers but if you are backing up to an alternative provider like Digital Ocean it might make more sense to start over and work towards an implementation that is customised to your environment, has less bloat and is easier to maintain. Here's how I did it.

## Kubernetes CronJob to Backup

For backup, I created a Kubernetes CronJob. The backup happens in two steps.

### Step 1: 
The neo4j-backup utility is run as an initialisation container. This produces an online backup which is written to a mounted volume. There is no downtime involved here but be aware that this will have performance implications on your running database. The schedule is set to meet the recovery point objective.

### Step 2:
A custom container runs which copies the backup (from the mounted volume) to Digital Ocean S3 using s3cmd. The reason for the custom container here is that at the time of writing there was no easy way to set the s3cmd configuration values at runtime using the CLI so this is configured at the application layer and baked into the image. 

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: backupdir-neo4j
  labels:
    app: neo4j-backup
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
---
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: neo4j-backup
  namespace: neo4j
spec:
  schedule: "0 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          volumes:
            - name: backupdir-neo4j
              persistentVolumeClaim:
                claimName: backupdir-neo4j
          initContainers:
            - name: neo4j-backup
              image: neo4j:4.2.8-enterprise
              env:
                - name: NEO4J_ACCEPT_LICENSE_AGREEMENT
                  value: "yes"
              volumeMounts:
              - name: backupdir-neo4j
                mountPath: /tmp
              command: ["/bin/sh", "-c"]
              args:
                - echo cleaning /tmp from PV;
                  rm -rf /tmp/*;
                  bin/neo4j-admin backup --backup-dir /tmp --database neo4j --from neo-neo4j.neo4j.svc.cluster.local:6362 --verbose;
                  echo backup completed
          containers:
            - name: copy-to-spaces
              image: registry.gitlab.com/custom-neo-backup-tool:latest
              imagePullPolicy: Always
              command: ["/bin/sh", "-c"]
              args:
                - yum install python36 -y;
                  pip3 install s3cmd;
                  cp /usr/app/s3cfg /root/.s3cfg;
                  s3cmd --config=/usr/app/s3cfg;
                  s3cmd put /tmp s3://path/to/backup/`date +%d%b%Y-%H:%M:%S`/ --recursive;
              volumeMounts:
              - name: backupdir-neo4j
                mountPath: /tmp
          imagePullSecrets: 
          - name: gitlab-registry-credentials  
          restartPolicy: OnFailure
```

## Restore

The restore process happens in two parts:
1. A initContainer runs in the helm chart to copy the data from S3.
2. A command is run inside the POD to restore the backup

### Step 1: 
The initContainer is a custom built image with the S3cmd config that copies the backup specified into the plugins mount. Note that the path to the backup in the s3cmd get command needs to be specified.

```
- name: custom-neo-recovery-tool
    image: registry.gitlab.com/custom-neo-backup-tool
    imagePullPolicy: Always
    volumeMounts:
    - name: plugins
      mountPath: /plugins
    command: ["/bin/sh", "-c"]
    args:
      - yum install python36 -y;
        pip3 install s3cmd;
        cp /usr/app/s3cfg /root/.s3cfg;
        s3cmd --config=/usr/app/s3cfg;
        s3cmd get --recursive --force s3://path/to/backup/timestamp/ /plugins;
```

### Step 2:
Once the neo core has started to perform the recovery:

```
1. In CYPHER-SHELL OR NEO4j BROWSER run: STOP DATABASE {name}
2. On Pod run: bin/neo4j-admin restore --from /plugins/tmp/neo4j --database neo4j --force;
3. In CYPHER-SHELL or NEO4J BROWSER run: START DATABASE {name}
```


