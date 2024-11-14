## Project 3

# create cluster
```bash
eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --node-type t3.small --nodes 1 --nodes-min 1 --nodes-max 2 --profile udacity-cloud
```

# Update kubeconfig
```bash
aws eks --region us-east-1 update-kubeconfig --name my-cluster --profile udacity-cloud
kubectl config current-context
```

# Create db service
```bash
kubectl get storageclass
kubectl apply -f deployment/pvc.yaml
kubectl apply -f deployment/pv.yaml
kubectl apply -f deployment/postgresql-deployment.yaml
kubectl apply -f deployment/postgresql-service.yaml
```

# Create env
```bash
kubectl apply -f deployment/configmap.yaml
kubectl apply -f deployment/mysecret.yaml.yaml
```

# setup port-forwarding from local:5433 to postgresql-service:5432
```bash
kubectl port-forward service/postgresql-service 5433:5432 &
```

# Create table and insert data
```bash
export DB_PASSWORD=`kubectl get secret mysecret -o jsonpath="{.data.DB_PASSWORD}" | base64 --decode`
PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433 < db/1_create_tables.sql
PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433 < db/2_seed_users.sql
PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433 < db/3_seed_tokens.sql
```

# connect db using port forwarding
```bash
PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433
```

# Create ECR repository
coworking

# Create CodeBuild 
Add permission 'FullECRAccess' policy to IAM role of CodeBuild to allow access ECR, Add trigger Merge event 

# Deploy application
```bash
kubectl apply -f deployment/coworking.yaml
```

# CloudWatch - Log
```bash
aws iam attach-role-policy \
--role-name eksctl-my-cluster-nodegroup-my-nod-NodeInstanceRole-jKrgIeJMpgSK \
--policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy --profile udacity-cloud

aws eks create-addon --addon-name amazon-cloudwatch-observability --cluster-name my-cluster --profile udacity-cloud

```
# Screenshots
Check screenshots folder