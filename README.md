Create IAM role - ecsTaskExecutionRole
```shell
npm init
npm install express
npm start

docker build -t test-fargate:1.0 .
docker run -d -p 80:80 --rm test-fargate:1.0

aws configure
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin ****.dkr.ecr.us-east-2.amazonaws.com
aws ecr create-repository --repository-name test-fargate --image-scanning-configuration scanOnPush=true --region us-east-2

docker tag test-fargate:1.0 ****.dkr.ecr.us-east-2.amazonaws.com/test-fargate:1.0
docker push ****.dkr.ecr.us-east-2.amazonaws.com/test-fargate:1.0

aws ecs create-cluster --cluster-name test-fargate
aws ecs register-task-definition --cli-input-json  file://aws-ecs-fargate-task-definition.json

aws ecs create-service --cluster test-fargate --service-name test-fargate-service --task-definition test-fargate:1 --desired-count 1 --launch-type "FARGATE" --network-configuration "awsvpcConfiguration={subnets=[subnet-91d56cdd],securityGroups=[sg-0caaea902eb0f5632],assignPublicIp=ENABLED}"

aws ecs delete-service --cluster test-fargate --service test-fargate-service --force
aws ecs delete-cluster --cluster test-fargate
```
