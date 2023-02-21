# CKA - UDEMY CKA 강의 내용 정리

https://lg-cns.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/learn/lecture/14298420#overview  
✔ 해당 영상의 내용을 바탕으로 정리한 문서입니다.  

------------------------------------------------------------------------
## <b>ETCD
+ ETCD란?
>1. K8s 기반 스토리지이며 모든 데이터가 ETCD에 보관된다. 클러스터에 어떤 노드가 몇개있고 어떤 파드가 어떤 노드에서 동작하는지 등등
>2. ETCD is a distributed reliable <b>key-value store<b> that is Simple, Secure & Fast   
>> key-value store?  
>> + RDB 형태가 아니라 key, value 두가지 컬럼을 가진 DB  
>> + Data가 복잡해지면 Jason / YANO를 사용한다.
>3. 2013년 8월 출시, 2018년 11월 CNCF Incubation (Version 3)  
>( version 2 -> 3 넘어갈때 API에 많은 변화가 생김 )  
> .  
> ![img.png](img.png)  
>버전 확인을 해봤다. 위에는 ETCD Utility version, 아래는 ETCD API version이다.  
>ETCD v3 이상은 API version : 3이 Default이다.  
> .   
> ![img_1.png](img_1.png)  
>API 버전 바꾸고 싶으면 이런식으로 하면 된다.

---
## <b>쿠버네티스 아키텍처구조</b>
![img_2.png](img_2.png)
* 간단한 아키텍처 흐름을 알아보자
>1. 사용자가 kubectl 커맨드를 실행하면, kubectl utility가 kube-apiserver에 접근한다.  
>2. kube-apiserver는 사용자의 명령에 대해 Authentication, Validation을 한다.
>3. 검증 절차를 통과하면 ETCD Cluster에서 요청받은 정보를 돌려준다. 
* Pod를 생성할때 아키텍처 흐름은 어떨까?
>1. 만약 Pod를 만든다고하면, 우선 api server가 Auth, Valid를 한다. (api server는 노드를 할당해주지 않는다.)  
>2. 그 후 User가 pod를 생성한다는 정보를 ETCD에 update한다.  
>3. 이때 kube-scheduler는 게속해서 apiserver를 모니터링한다.  
>4. node가 할당되지 않은 새로운 파드가 있다고 판단되면 scheduler는 올바른 노드를 식별해 새로운 파드를 해당 노드에 할당하고, api server에 알린다.  
>5. api server는 내용을 다시 etcd에 알리고 etcd는 데이터를 update한다.  
>6. api server는 생성된 node가 있는 worker node에도 정보를 전달한다.  
>7. 그러면 받은 정보대로 kublet은 pod를 생성한다.  
>8. kublet은 worker node 내의 Container Runtime Engine에 이미지 배포를 위해 정보를 전달한다.  
>9. 완료되면 kublet은 다시 api server로 업데이트된 데이터를 전달하고 api server는 다시 etcd로 데이터를 전달한다. 당연히 etcd는 다시 데이터를 update 한다.  
