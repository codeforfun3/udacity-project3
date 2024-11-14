## Create cluster and setup DB service

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

# setup port-forwarding from local:5433 to postgresql-service:5432
```bash
kubectl port-forward service/postgresql-service 5433:5432 &
```

# set DB env variables

```bash
export DB_PASSWORD=mypassword
export DB_USERNAME=myuser
export DB_HOST="127.0.0.1"
export DB_NAME="mydatabase"
export DB_PORT=5433
```

# Create table and insert data
```bash
PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433 < db/<files.sql>
```

# connect db using port forwarding
```bash
PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433
```

# build app locally 
```bash
apt update -y && apt install build-essential libpq-dev -y
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
python app.py

curl 127.0.0.1:5153/api/reports/daily_usage
curl 127.0.0.1:5153/api/reports/user_visits
```

# build app with docker
```bash
docker build -t test-coworking-analytics .
docker run --network="host" test-coworking-analytics
```

## Setup Continuous Integration with CodeBuild and Deploy to EKS
# Create ECR repository
coworking

# Create CodeBuild 
Add permission 'FullECRAccess' policy to IAM role of CodeBuild to allow access ECR, Add trigger Merge event 

# Configure ENV 
```bash
kubectl apply -f deployment/configmap.yaml
kubectl apply -f deployment/mysecret.yaml.yaml
```

# Verify DB Password
```bash
kubectl get secret mysecret -o jsonpath="{.data.DB_PASSWORD}" | base64 --decode
```

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