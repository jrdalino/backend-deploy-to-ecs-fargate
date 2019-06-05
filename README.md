# My Project Deploy Product Rest API To AWS ECS Fargate

## Deploy Product Rest API To ECS Fargate

### Step 1.1: Create our Task Definition
```
$ cd ~/environment/calculator-backend
$ vi ~/environment/calculator-backend/aws-cli/task-definition.json
```

```
{
  "family": "consumermanagementservice",
  "cpu": "256",
  "memory": "512",
  "networkMode": "awsvpc",
  "requiresCompatibilities": [
    "FARGATE"
  ],
  "executionRoleArn": "arn:aws:iam::707538076348:role/ModernAppStack-EcsServiceRole-7F5DK8MRXP22",
  "taskRoleArn": "arn:aws:iam::707538076348:role/ModernAppStack-ECSTaskRole-8ZJCOCUWTWOH",
  "containerDefinitions": [
    {
      "name": "consumer-management-service",
      "image": "707538076348.dkr.ecr.ap-southeast-1.amazonaws.com/consumer-management/service:latest",
      "portMappings": [
        {
          "containerPort": 3000,
          "protocol": "http"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "consumer-management-logs",
          "awslogs-region": "ap-southeast-1",
          "awslogs-stream-prefix": "awslogs-consumer-management"
        }
      },
      "essential": true
    }
  ]
}
```

### Step 1.2: Create our Service
```
$ vi ~/environment/calculator-backend/aws-cli/service-definition.json
```

```
{
  "serviceName": "Consumer-Management-Service",
  "cluster": "ModernApp-Cluster",
  "launchType": "FARGATE",
  "deploymentConfiguration": {
    "maximumPercent": 200,
    "minimumHealthyPercent": 0
  },
  "desiredCount": 1,
  "networkConfiguration": {
    "awsvpcConfiguration": {
      "assignPublicIp": "DISABLED",
      "securityGroups": [
        "sg-0a02742b48ae9712c"
      ],
      "subnets": [
        "subnet-0de07f0b2d4303a60",
        "subnet-02f30df2af672607a"
      ]
    }
  },
  "taskDefinition": "consumermanagementservice",
  "loadBalancers": [
    {
      "containerName": "consumer-management-service",
      "containerPort": 3000,
      "targetGroupArn": "arn:aws:elasticloadbalancing:ap-southeast-1:707538076348:targetgroup/consumer-management-target-group/2077d49bdb330042"
    }
  ]
}
```

### Step 1.3: Ensure ELB service Role exists
```
$ aws iam create-service-linked-role \
--aws-service-name ecs.amazonaws.com
```

### Step 1.4: Deploy our Backend REST API and watch progress
```
$ cd ~/environment/calculator-backend
$ aws ecs register-task-definition \
--cli-input-json file://~/environment/calculator-backend/aws-cli/task-definition.json
$ aws ecs create-service \
--cli-input-json file://~/environment/modern-app-workshop/aws-cli/service-definition.json
```

### Step 1.5: Scale the Backend Service
- https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html

### Step 1.6: Find the Service Address

### (Optional) Clean up
