# automatic-cd-gke-env
## This is a procedures for making the GKE development enviroment cheaper.
# Contents
1. [preface](#anchor1)
1. [Architecture](#anchor2)
1. [Procedure](#anchor3)
4. [Conclusion](#anchor4)

<a id="anchor1"></a>
### 1. Preface
The default machine type of nodes in GKE is n1-standard-1 the number of it is 3.
Therefore the monthly amount would be as bellows if you unconsciously use the defaula enviroment and carelessly forget to eliminate resources.
|  Period               |  Cost                                            |
| ----                  | ----                                             |
|  before 2020/06/06    |   24H×30Days×3Nodes×$0.0475=$102.6..!!😵         |
|  From 2020/06/06      |   24H×30Days×(3Nodes×($0.0475)+$0.1)=$174.6..!!😭  |
<img width="855" alt="cost" src="https://user-images.githubusercontent.com/32224266/80065242-c4a3ef80-8574-11ea-8ed6-bf87f424f40a.png">
<img width="852" alt="conf" src="https://user-images.githubusercontent.com/32224266/80071837-451c1d80-8580-11ea-9291-b1d624c9538e.png">
It might be a tiny bit of expensive for individuals

<a id="anchor2"></a>
### 2. Architecture
The Architecutre is as bellows. This development enviroment is available from 19:00 to 26:00(Weekdays) and 14:00-26:00(Holidays)
It's just one example and you can choose any period by changing the cron configuration of Cloud Scheduler.
<img width="782" alt="Architecture" src="https://user-images.githubusercontent.com/32224266/80072663-83fea300-8581-11ea-9808-c3981e444e6c.png">

<a id="anchor3"></a>
### 3. Procedure(You can execute following procedures via cloud-shell)
#### 3.0. Adjust configurations for your GCP projects
- Write your GCP project name to $YOUR_PROJECT in following files.
```
create_env.sh
delete_env.sh
```
#### 3.1. Cloud Pub/Sub → Cloud Run
- Build the Docker images
```
docker build -f Dockerfile_c -t gcr.io/$YOUR_PROJECT_NAME/create-k8s-dev-env ./
docker build -f Dockerfile_d -t gcr.io/$YOUR_PROJECT_NAME/delete-k8s-dev-env ./
```
- Push these images to Container Registry
```
docker push gcr.io/$YOUR_PROJECT_NAME/create-k8s-dev-env
docker push gcr.io/$YOUR_PROJECT_NAME/delete-k8s-dev-env
```
- Creat The Service of Cloud Run by using the above images.
    - Creat The Service of Cloud Run(The case of create-k8s-dev-env is described as bellows. ( Do the same thing for delete-k8s-dev-env) )
<img width="792" alt="create22" src="https://user-images.githubusercontent.com/32224266/80112386-03a96380-85bc-11ea-9fde-eb96ffe06310.png"> 
<img width="532" alt="CloudRun" src="https://user-images.githubusercontent.com/32224266/80115402-aca58d80-85bf-11ea-82f6-9b462e8d8497.png">    
<img width="747" alt="choosebuild" src="https://user-images.githubusercontent.com/32224266/80118771-d3fe5980-85c3-11ea-9937-859e9a57e41e.png">

- Associate the CloudRun with CloudPubSub(The case of create-k8s-dev-env is described as bellows. ( Do the same thing for delete-k8s-dev-env) )
<img width="793" alt="pubsubrun" src="https://user-images.githubusercontent.com/32224266/80121278-107f8480-85c7-11ea-8ffe-7b37fba3ad5d.png">

#### 3.2. Cloud Pub/Sub → Cloud Function(Option for Slack notification)
- Create the function of Cloud Function by inserting following python scripts.(Python3.7)
    - Describe the Webhook URL as SLACK_URL into the Environment variables in Cloud Function
```
notification-create-k8s-dev-env.py
notification-delete-k8s-dev-env.py
```
- Associate the Cloud Function with CloudPubSub(The case of create-k8s-dev-env is described as bellows. ( Do the same thing for delete-k8s-dev-env) )
<img width="793" alt="cloudfunc" src="https://user-images.githubusercontent.com/32224266/80122874-17a79200-85c9-11ea-91cc-a636177eeec1.png">

#### 3.3. Cloud Scheduler → Cloud Pub/Sub
- Associate the CloudPubSub with Cloud Scheduler(You can choose any period by changing the cron configuration of Cloud Scheduler.)
<img width="779" alt="scheduler" src="https://user-images.githubusercontent.com/32224266/80123970-8c2f0080-85ca-11ea-9332-c5d350ffa22a.png">

<a id="anchor4"></a>
### 4. Conclusion
In this architecture, construction of nodes are 2 n1-standard-1(preemptive) and 1 f1-micro(ALL-FREE)
By assuming that you use this enviroment after your work, This development enviroment is available from 19:00 to 26:00(Weekdays) and 14:00-26:00(Holidays)
|  Period            |  Cost(Weekdays)                         |         Cost(Holidays)                  |  Cost(Total)     |
| ----               | ----                                    | ----                                    |  ----            |
|  before 2020/06/06 | 7H×22Days×2Nodes×$0.0100=$3.08          | 12H×8Days×2Nodes×$0.0100=$1.92          |  $5.0!🤑(95%OFF) |
|  From 2020/06/06   | 7H×22Days×(2Nodes×($0.0100)+$0.1)=$18.5 | 12H×8Days×(2Nodes×($0.0100)+$0.1)=$11.5 |  $30😥(82%OFF)   |

### We might need to think another effective way from 2020/06/06 ...🤔

