# Cocktail 설치 후 기본 확인 사항

Cocktail 설치 및 환경설정 작업이 완료되면 기본적인 서비스 배포, 빌드, 템플릿을 이용한 배포등 기본 기능이 정상적으로 동작하는지 확인한다.

1.서비스 생성하기

아래와 같이 간단한 tomcat server를 deploy 하고 완료시 해당 service port로 접속하여 정상적으로 tomcat 초기 화면이 표시되는지 확인한다. **단, tomcat server의 특성상 Memory request 양과 limit양을 각각 512, 1024로 설정해 주어야 정상 기동하게 된다.**

홈 &gt; 서비스 &gt; 맵생성 &gt; 서 생성 화면에서 아래 그림을 참고해서 등록한 후 생버튼을 클릭한다.

![](/assets/deploy1.jpeg)

상태가 Running으로 완료되면 tomcat 제목아래에 있는 webport:192.168... 을 클릭한다.





![](/assets/deploy2.jpeg)

정상적으로 배포가 완료되면 아래와 같이  tomcat 초기화면이 표시된다. 주의할 점은 초기에 tomcat이 기동되는 시간이 다소 소요될 수 있으므로 이를 감안하여 화면으로 reload 하면 된다.![](/assets/deploy3.jpeg)





![](/assets/deploy4.jpeg)

![](/assets/deploy5.jpeg)

![](/assets/deploy6.jpeg)

![](/assets/deploy7.jpeg)

![](/assets/deploy11.jpeg)

![](/assets/deploy8.jpeg)![](/assets/deploy9.jpeg)![](/assets/deploy10.jpeg)![](/assets/deploy12.jpeg)

