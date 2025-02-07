# 8장 병합과 충돌 요약 정리입니다.
# 🔔병합
❗독립된 브랜치에서 개발 작업이 끝나면 다시 원본 브랜치에 작업한 결과를 반영해야 합니다. 분리된 브랜치를 한 브랜치로 작업을 병합(합치기)이라고 합니다.두 코드를 하나씩 직접 비교해가며 수동으로 병합하거나 깃 도구를 사용하여 자동으로 병합할 수 있습니다.❗

## 📌8.1.1 하나씩 직접 비교하는 수동 병합
#### 수동으로 병합하려면 양쪽 파일을 일일이 비교하며 바뀐 점을 찾아서 적용해야 한다.하지만 오류 없이 코드를 병합하는 것은 간단하지 않다. <br>
❗예를들어 소스 코드가 2개보다 많다면 어떻게 될까요? 원본 소스 A를 복제하여 B를 수정하고, B를 복제하여 C도 수정하고 있습니다.<br>
수정이 완료되면 B는 원본 소스 A에 수정 내역을 반영하는 작업을 해야하고, C 또한 B를 반영하여 수정된 원본 소스 A에 또다시 수정 작업을 반영해야 합니다.병합하려면 코드의 변경된 시점을 기억해야 합니다.변경된 시점을 기준으로 순차적으로 병합 처리를 하는 것이 좀 더 쉽습니다.❗ <br>

![image](https://user-images.githubusercontent.com/105197524/200317261-770b1de7-a2cb-47fc-bb13-4209cc696593.png)

## 📌8.1.2 깃으로 자동 병합

1. 깃은 자동 병합은 원본을 기준으로 두 파일의 변경 이력을 비교합니다. <br>
2. 깃의 병합은 브랜치를 기준으로 합니다. <br>

따라서 과거에는 모든 병합 작업을 수동으로 해서 여려웠지만, 이제는 이러한 수동 병합 작업을 깃이 대신 처리합니다. 하지만 깃이 모든 코드의 병합을 완벽하게 처리는 할수 없고 아무리 좋은 도구라해도 자동으로 반영하지 못하는 것들이 있는데 이를 **충돌**이라 합니다.

## 📌8.1.2 병합 방식
깃은 병합을 위해 두 가지 기본적인 알고리즘 방식을 제공한다.
- Fast-Forword 병합
- 3-way 병합

두 방식으로 실습하기 전에 미리 별도의 저장소를 생성해 두겠습니다.

![제목 없음](https://user-images.githubusercontent.com/105197524/200338767-50a1d7b6-84e1-4e58-9d4b-c348dcb7e216.png)

## 📌Fast-Forward 병합
**깃의 가장 간단한 브랜치 병합은 Fast-Forward 방식이다.일반적으로 혼자 개발할 때 사용한다.**

## 📌 8.2.1 브랜치 생성과 수정 작업
브랜치를 생성 후 브랜치 커밋 확인
```
$ git checkout -b feature --- 브랜치 생성 및 이동
PC18@LAPTOP-L4CJE14H MINGW64 ~/Desktop/e/study08 (feature)

$ git rev-parse feature  --- 브랜치 커밋 확인
525bf0f445d8829a9759d215071901799791db5a
```
index.htm 파일 수정후 등록 및 커밋 후 커밋 이력 확인
```
$ code index.htm  --- feature 브랜치 안에 있는 index.htm 파일 수정

$ git commit -am "add header" --- 등록 및 커밋
[feature a209baf] add header
1 file changed, 2 insertions(+)

$ git log  --- 커밋 이력 확인하기
commit a209bafd738333feebf1c37c6f914f7df05e9fce (HEAD -> feature)
Author: uh004 <uh004@naver.com>
Date:   Mon Nov 7 23:54:56 2022 +0900

    add header

commit 525bf0f445d8829a9759d215071901799791db5a (main)
Author: uh004 <uh004@naver.com>
Date:   Mon Nov 7 23:43:53 2022 +0900

    frist
```
feature 브랜치에 등록 및 커밋 2번하기
```
$ code index.htm  --- feature 브랜치 안에 있는 index.htm 파일 수정

$ git commit -am "add menu1"  --- 등록 및 커밋
[feature 3c0edb4] add menu1
 1 file changed, 3 insertions(+)

$ code index.htm  --- feature 브랜치 안에 있는 index.htm 파일 수정

$ git commit -am "add menu2"  --- 등록 및 커밋
[feature 140f38d] add menu2
 1 file changed, 1 insertion(+)
```
**소스트리에서 브랜치 커밋 로그를 확인해 보면 feature 브랜치가 main 브랜치에서 분리되어 header, menu1, menu2 커밋 작업을 세 번 수행한 것을 볼수 있다.**
```
$ git log --graph --oneline --all  --- 모든 커밋 이력을 한줄그래프로 확인
* 140f38d (HEAD -> feature) add menu2
* 3c0edb4 add menu1
* a209baf add header
* 525bf0f (main) frist
```
소스트리에서 확인

![제목 없음](https://user-images.githubusercontent.com/105197524/200343762-e7737cdb-1a70-4c5e-b9f3-7bcfdfbf9965.png)

## 📌8.2.2 병합 위치
**브랜치를 병합하려면 가준과 대상이 있어야 합니다.기준은 체크아웃된 현재 브랜치입니다. 우리는 main 브랜치에서 feature 브랜치로 분기하여 작업했습니다.
병합을 하기위해서 main 브랜치로 체크아웃 하겠습니다.**
```
$ git checkout main
Switched to branch 'main'
```
main 브랜치로 체크아웃하고 파일을 확인하면 feature에서 했던 작업이 사려졌습니다. 이제 feature 브랜치에서 작업한 내용을 병합해 보겠습니다.
```
code index.htm --- VS Code 실행
```

## 📌8.2.3 Fast-Forward 병합 적용
커밋 작업은 분기된 feature 브랜치에서 모두 수행했습니다. 아직 main 브랜치에는 추가된 커밋이 없습니다. 이러한 상태에서 두 브랜치를 병합해보겠습니다.
```
$ git merge feature
Updating 525bf0f..140f38d
Fast-forward
 index.htm | 6 ++++++
 1 file changed, 6 insertions(+)
```
main 브랜치와 feature 브랜치가 잘 병합된것을 확인할 수 있다. vscode를 실행하면 feature에서 수정한 내용이 적용되었습니다.
```
$ git log --oneline
140f38d (HEAD -> main, feature) add menu2
3c0edb4 add menu1
a209baf add header
525bf0f frist

$ code index.htm  --- vscode 실행
```
**Fast-Forward 병합은 병합할 하나의 브랜치 파일을 기준 브랜치로 복하여 수정된 파일을 원본에 그대로 적용한 것과 같습니다.** <br>
**즉 원본에 추가된 내용이 없다는 가정하에 변경한 파일을 대체하는 것입니다.**

## 📌8-3 3way 병합

3way 병합은 복잡한 병합을 처리할 수 있는 방법이며, 여러 개발자와 협업으로 작업하는 경우 대부분 이 방법을 사용함.

![1](https://user-images.githubusercontent.com/105197472/200488119-1dae5b2b-4ac9-4dcc-9760-3ca6fb6d5e9b.PNG)

hotfix 브랜치를 생성하고, hotfix 브랜치로 체크아웃 하는 과정. (현재 브랜치 위치는 hotfix)

![2](https://user-images.githubusercontent.com/105197472/200488262-87d863f7-9427-4e47-8485-44730993db88.PNG)

<footer></footer> 태그를 추가하고 내용을 적는 실습 과정 진행. 커밋은 두 번으로 나누어 진행. 우선 <footer> 태그 추가하고 저장.

![3](https://user-images.githubusercontent.com/105197472/200488336-ee1ffeb4-d15a-48a4-9343-53e079cb2ea5.PNG)

    
수정한 파일은 Modified 상태가 됨. 수정한 파일을 다시 Staging Area에 등록한 후 Commit.


![4](https://user-images.githubusercontent.com/105197472/200488569-04abb2d2-56f8-4f95-9ab6-8508fa9995d8.PNG)

다시 index.html 파일에 <footer> 내용을 추가한 후 Commit.

![5](https://user-images.githubusercontent.com/105197472/200488779-f0a26cf2-01ca-43c3-b8ac-6c4026545fb8.PNG)

    
![6](https://user-images.githubusercontent.com/105197472/200488844-d57d24a6-f613-431c-8109-d80c3a3f8997.PNG)

이를 그림으로 나타내면 다음과 같음.

![7](https://user-images.githubusercontent.com/105197472/200488992-0000c7cd-9032-4884-b1fc-993a17c8e4e2.PNG)
   
소스트리에서 커밋 로그 확인.
    
![8](https://user-images.githubusercontent.com/105197472/200489090-1e22e592-a1db-4900-9901-1660fd07dc68.PNG)

현재 hotfix 브랜치에 새로운 Commit이 2개 추가 되었음. 지금까지 hotfix 브랜치에서 진행한 수정은 Fast-Forward 병합과 유사함.

## 📌 8-3-2. 마스터 변경.

브랜치 모양을 변경하는 실습을 진행. 우선 hotfix 브랜치에서 master 브랜치로 이동.
    
![9](https://user-images.githubusercontent.com/105197472/200489283-ff9c20f2-6123-42ea-87f6-9d7215dd0f7e.PNG)


![10](https://user-images.githubusercontent.com/105197472/200489318-90d90043-c43c-4511-8899-416e7d2990a7.PNG)

hotfix 브랜치의 마지막 커밋은 7277f2d, master 브랜치의 마지막 커밋은 7caf5f0임. 시간순으로 우선인 master 브랜치에 새로운 커밋을 추가할 예정.
    
![11](https://user-images.githubusercontent.com/105197472/200489473-5c01870f-1a9f-4e1e-989b-9b66be7b5554.PNG)


![12](https://user-images.githubusercontent.com/105197472/200489631-5a71743d-848b-4047-b0bf-b0ca3d26b9c6.PNG)

master 브랜치의 index.htm 파일에 menu3 추가 후 Commit
 
![13](https://user-images.githubusercontent.com/105197472/200489816-32bfe652-aac0-4c60-be3e-5819098e7c83.PNG)

![14](https://user-images.githubusercontent.com/105197472/200489859-b1f79c0c-d698-446b-a8c0-a299404e075c.PNG)

    
menu4를 추가 후 Commit

위와 같은 실습을 진행한다면 메뉴가 4개가 생성됨. master 브랜치에 추가 Commit이 발생하면 브랜치는 7caf5f0을 기준으로 hotfix와 master 브랜치로 갈라짐. 기준 Commit에서 서로 다른 브랜치의 Commit이 연결됨.
    
![15](https://user-images.githubusercontent.com/105197472/200490000-9419e370-b773-4810-93a6-d22f6a7b440f.PNG)

소스트리에서 master ㅂ랜치의 그래프 모양을 확인해 보면 Fast-Forward 병합과 다르게 브랜치가 확실하게 나뉘어져 있음.
    
![16](https://user-images.githubusercontent.com/105197472/200490106-e36cc5d8-0c8d-4760-8cc0-2662f04b475d.PNG)
    
## 📌 8.3.3 공통 조상
    
8-3-2에서 진행 실습에서 브랜치별로 각각 Commit시, hotfix와 master 브랜치로 갈라지는 것을 확인 할 수 있었음. 브랜치 모양이 갈라지는 형태로 나뉠 때는 Fast-Forward 방식의 알고리즘을 적용하여 병합할 수 없음. 이 때 사용하는 방법이 3-way 방식.

    
![17](https://user-images.githubusercontent.com/105197472/200490292-2c36fa06-bdfb-4d4a-bb79-205c38615db2.PNG)

2개의 브랜치를 병합하려면 공통 커밋을 찾아야 함. 이 공통 커밋을 공통 조상 커밋이라고 함. 공통 조상 커밋을 포함하는 브랜치와 새로운 두 브랜치, 세 개의 브랜치를 하나로 병합하는 과정에서 브랜치가 3개 있기 때문에 3-way 병합이라고 함.        
깃은 공통 조상 커밋을 자동으로 찾아줌. 이것이 다른 VCS보다 서로 다른 브랜치를 편리하게 병합할 수 있는 장점임.

    
## 📌 8.3.4 병합 커밋

    
병합은 각 브랜치에서 독립적으로 작업된 소스를 파일 하나로 결합. 깃을 사용하면 복잡한 병합도 손쉽게 처리 가능. 3-wqy 병합은 두 브랜치의 공통 조상 커밋을 자동으로 찾아주고, 이를 기준으로 병합.
그리고 병합을 완료하면 새로운 커밋을 추가해 추가로 하나를 생성. 이 생성된 커밋을 병합 커밋이라고 함. 병합 커밋은 부모 커밋이 2개임.
    
    
![18](https://user-images.githubusercontent.com/105197472/200490947-fee6a4c9-1f3b-4d53-bb61-3d29f33fd0ed.PNG)

hotfix 브랜치를 master 브랜치에 병합하는 과정 실습. hotfix 브랜치를 병합하려면 master 브랜치로 Check-out 해야함.
    

![19](https://user-images.githubusercontent.com/105197472/200492379-24ae4341-f539-4ae5-af1b-4f34c155b341.PNG)


 3-way 방식으로 두 브랜치를 병합한것을 그림으로 나타내면 아래와 같음.
    
![20](https://user-images.githubusercontent.com/105197472/200491191-7f9457d9-b8bc-44b5-a435-8eaf25d98bf6.PNG)
    
병합 후에는 커밋 로그를 확인함.
    
    
![21](https://user-images.githubusercontent.com/105197472/200491247-c3cb6df9-4152-4aa3-97a1-f8767234128b.PNG)
    
소스트리에서 확인.
    

![22](https://user-images.githubusercontent.com/105197472/200491286-6839928f-cd80-4758-b304-2b1d08da75d9.PNG)
    
## 📌 8.3.5 병합 메세지
    
3-way 병합 방식은 Fast-Forward 병합과 달리 메시지가 필요하다. 깃은 두 브랜치를 병합한 후에 새로운 커밋을 하면 동시에 메세지를 자동 생성함.자동 메세지 외에 직접 메세지를 작성 할 수 있음 merge 명령어 -e 또는 --edit 옵션을 사용하면 됨.
    

![23](https://user-images.githubusercontent.com/105197472/200491549-063f21c7-895b-4376-8537-f77d266f5e5f.PNG)
    
직접 병합 메세지를 작성하기 위한, 앞선 병합을 취소하고 새로운 메세지를 작성하여 다시 병합.
    
![24](https://user-images.githubusercontent.com/105197472/200491686-52fa7a0e-987b-4725-b89b-89bb2c4e586a.PNG)
    

![25](https://user-images.githubusercontent.com/105197472/200491830-b40c44e2-8957-4cac-86f3-baff8c8fbf53.PNG)


이를 실습하면 vi 에디터 창이 열리게 됨.
    
    
![26](https://user-images.githubusercontent.com/105197472/200491760-e83b7572-62ad-48f4-b913-26a9727417ab.PNG)
    
병합 메세지를 작성하고 종료하면, 결과 메세지가 출력되고 직접 작성한 메세지로 커밋됨.
    
## 📌 8.4 브랜치 삭제
## 8.4.1 병합 후 삭제
    
병합된 브랜치의 커밋은 원본 브랜치에 모두 적용됨. 따라서 불필요한 브랜치는 삭제 하면 됨.
삭제 할 때는 -d 옵션을 사용하는데, 이는 병합을 완료한 브랜치만 삭제 가능함.
    
![28](https://user-images.githubusercontent.com/105197472/200492097-1b0ee0cb-da73-4755-91cd-6efd4e14111e.PNG)
    
병합이 완료되지 않은 브랜치를 삭제 한다면 대문자 -D 옵션을 사용해야함.


