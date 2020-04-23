# automatic-cd-gke-env
## This is one procedure for making the GKE development enviroment cheaper.
# Contents
1. [preface](#anchor1)
1. [Architecture](#anchor2)
1. [Procedure](#anchor3)

<a id="anchor1"></a>
### 1. Preface
The default machine type of nodes in GKE is n1-standard-1 the number of it is 3.
Therefore the monthly amount would be as bellows if you unconsciously use the defaula enviroment and carelessly forget to eliminate resources.
|  Period               |  Cost  |
| ----                  | ----                                             |
|  before 2020/06/06    |   24H×30Days×3Nodes×$0.0475=$102.6..!!😵         |
|  From 2020/06/06      |   24H×30Days×3Nodes×($0.0475+$0.1)=$318.6..!!😭  |
<img width="855" alt="cost" src="https://user-images.githubusercontent.com/32224266/80065242-c4a3ef80-8574-11ea-8ed6-bf87f424f40a.png">
<img width="852" alt="conf" src="https://user-images.githubusercontent.com/32224266/80071837-451c1d80-8580-11ea-9291-b1d624c9538e.png">
It might be a tiny bit of expensive for individuals

<a id="anchor2"></a>
### 2. Architecture
The Architecutre is as bellows. This development enviroment is available from 19:00 to 26:00(Weekdays) and 14:00-26:00(Holidays)
<img width="782" alt="Architecture" src="https://user-images.githubusercontent.com/32224266/80072663-83fea300-8581-11ea-9808-c3981e444e6c.png">

