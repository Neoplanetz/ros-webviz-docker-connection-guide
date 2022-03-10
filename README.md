# ROS Webviz Docker Connection Guide

Docker 기반 Webviz 서버를 구동하여 다른 컴퓨터의 ROS Topic 정보들을 ROS Bridge 서버로 실시간으로 가져와 웹브라우져를 통해 가시화

- Docker를 사용해 손쉽게 Webviz 서버 설치 및 운용
- 외부에서 웹브라우져를 통해 언제든지 실시간으로 ROS 정보 가시화
- ROS Master PC에서 Rviz 미사용에 따른 리소스 절약 및 성능 증대화

<img src="/images/webviz_based_on_docker.gif" width="70%" height="70%" alt="webviz_docker"></img>

## <구동 환경>
- 2대의 PC로 구성
  + ROS PC: Ubuntu 16.04 기반 ROS Kinetic버전의 ROS Master PC (Ubuntu 및 ROS 버전 무관)
  + Client PC: Windows 10 기반 웹브라우져용 PC (웹브라우저만 가능하면 모든 디바이스 가능)

## <설정>
- ROS PC
  + 기본적인 ROS 설치 후, 추가적으로 ROS Bridge Server Packege 설치
  + ROS Bridge Server는 ROS Master에서 구동되는 ROS Topic 정보들을 Web Socket으로 전송해주는 역할  
  ```bash
  $ sudo apt-get install ros-kinetic-rosbridge-suite
  ```
- Client PC
  + Docker for Windows 를 설치 후 Webviz Server 구동을 위한 ros-Webviz Docker 파일을 다운로드
  ```cmd
  $ docker pull frankjoshua/ros-webviz
  ```

## <실행>
- ROS PC
  + ROS 구동을 위해 ROS Core를 실행하고, ROS Bridge Server를 구동하여 Client PC에서 ROS 정보를 받아 가시화 할수 있도록 실행
  > ROS Bridge Server를 포트를 변경하고 싶은 경우 rosbridge_websocket.launch 파일 안에 port 파라미터를 수정
  ```bash
  $ roscore
  # New Terminal Tab
  $ roslaunch rosbridge_server rosbridge_websocket.launch
  ```
  + 본 테스트에서는 ROS Topic 사용을 위해 저장해둔 Rosbag 파일을 실행
  ```bash
  $ rosbag play test_ros.bag
  ```

- CLient PC
  + ros-webviz server Docker를 포트 설정하여 실행
  ```cmd
  $ docker run -p 8080:80 frankjoshua/ros-webviz
  ```
  
  + 실행된 ros-webviz server를 웹브라우져를 통해 접속 (ros-webviz 초기화면 접속)
  + localhost : 기본적으로는 docker에서 돌아가는 docker의 IP 주소
    * 접속 주소: http://localhost:8080
  + ROS PC에서 구동중인 ROS Topic 정보들을 받아 ros-webviz에 가시화 하기위하여 rosbridge web socket port 정보를 넣어 접속
    * ROS Bridge Server 접속 주소: http://localhost:8080/?rosbridge-websocket-url=ws://10.252.101.201:9090
    * ws : Web Socket의 약자
    * ws 뒤에 주소는 연결할 ROS PC의 IP 및 ROS Bridge Server의 포트번호
  
![Alt webviz-webbrowser](/images/webviz-chrome.png)
