# Cocktail 설치 후 기본 확인 사항

Cocktail 설치 및 환경설정 작업이 완료되면 기본적인 서비스 배포, 빌드, 템플릿을 이용한 배포등 기본 기능이 정상적으로 동작하는지 확인한다.

1.서비스 생성하기

아래와 같이 간단한 tomcat server를 deploy 하고 완료시 해당 service port로 접속하여 정상적으로 tomcat 초기 화면이 표시되는지 확인한다. **단, tomcat server의 특성상 Memory request 양과 limit양을 각각 512, 1024로 설정해 주어야 정상 기동하게 된다.**

홈 &gt; 서비스 &gt; 맵생성 &gt; 서버 생성 화면에서 아래 그림을 참고해서 등록한 후 생성 버튼을 클릭한다.

![](/assets/deploy1.jpeg)

상태가 Running으로 완료되면 tomcat 제목아래에 있는 webport:192.168... 을 클릭한다.

![](/assets/deploy2.jpeg)

정상적으로 배포가 완료되면 아래와 같이  tomcat 초기화면이 표시된다. 주의할 점은 초기에 tomcat이 기동되는 시간이 다소 소요될 수 있으므로 이를 감안하여 화면으로 reload 하면 된다.![](/assets/deploy3.jpeg)2.카탈로그를 이용한 배포 기능 확인

상기 배포한  tomcat server를 템플릿으로 저장하여 다른 사람이 이를 사용할 수 있도록 하는 기능을 확인한다. 

홈 &gt; 서비스 &gt; 어플리케이션맵 &gt; 배포 화면에서 "카탈로그로 저장" 버튼을 클릭하고 아래 그림과 같이 등록한다.

![](/assets/deploy4.jpeg)

![](/assets/deploy5.jpeg)

등록이 완료된 tomcat 템플릿으로 신규 배포 기능을 확인한다.

홈 &gt; 카탈로그 &gt; tomcat 화면에서 배포버튼을 클릭하고 아래 화면과 같이 서비스,  클러스터, 어플리케이션 맵을 입력한 후 배포버튼을 클릭한다. 

정상배포되면 이전 tomcat 서버배포시와 같이 해당 화면이 정상 접속되는지 확인한다

![](/assets/deploy6.jpeg)

  ![](/assets/deploy7.jpeg).

![](/assets/deploy11.jpeg)

![](/assets/deploy8.jpeg)![](/assets/deploy9.jpeg)![](/assets/deploy10.jpeg)![](/assets/deploy12.jpeg)

