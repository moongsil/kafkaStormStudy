# Storm Distributed Mode 설치


**참고문서**
https://tedwon.atlassian.net/wiki/pages/viewpage.action?pageId=1212933
http://www.michael-noll.com/tutorials/running-multi-node-storm-cluster/


#### 사전 준비사항
* CentOS 6.6 
* 반드시 root 로 작업한다. 
* hosts 파일과 windows 모두 hosts  파일에 VM목록을 등록해둔다. (개발 테스트 편의상)
* kafka 와 zookeeper 는 설치되었다고 가정하고 진행한다. 

#### VM 환경
Storm  | hostname  | private ip | program
------------ | ------------ | ------------- | -------------
nimbus | kf-broker-1  |  192.168.13.11   |  kafka + zookeeper
supervisor | kf-broker-2   |  192.168.13.12  |  kafka + zookeeper
supervisor | kf-broker-3   |  192.168.13.13  |  kafka + zookeeper


#### 설치 순서

// os update  
yum update

// openJDK  설치   
yum -y install java-1.7.0-openjdk-devel

// JAVA_HOME 환경변수 세팅하기 - 소스 빌드할때 필수.

vi ~/.bashrc

// 경로를 반드시 확인한 후 다음라인 추가   
JAVA_HOME="/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64"
export JAVA_HOME

// 종속성 설치   
yum -y install gcc gcc-c++ boost boost-devel pkgconfig uuidd libtool autoconf make coreutils uuid-dev uuid-devel libuuid-devel e2fsprogs-devel


//경로 바꾸고 시작한다.   
cd /usr1/program

//zeroMQ 설치  

*zeroMQ  
wget http://download.zeromq.org/zeromq-2.1.11.tar.gz
tar zxf zeromq-2.1.11.tar.gz
cd zeromq-2.1.11
./configure
make
make install

*JZMQ  
yum -y install git

git clone https://github.com/nathanmarz/jzmq.git // 
cd jzmq
./autogen.sh
./configure

//작성일 현재 버전에서 make할때  문제가 있다.  아래처럼 특정라인을 변경한다   
sed -i 's/classdist_noinst.stamp/classnoinst.stamp/g' /usr1/program/jzmq/src/Makefile.am

make
make install

*Storm  
cd /usr1/program
wget http://apache.mirror.cdnetworks.com/storm/apache-storm-0.10.0/apache-storm-0.10.0.tar.gz


tar zxf apache-storm-0.10.0.tar.gz  
mv apache-storm-0.10.0  /usr1/storm-0.10.0




*conf/storm.yaml 수정 및 **모든 노드 배포*  

//vi로 편집
vi /usr1/storm-0.10.0/conf/storm.yaml 

//아래 내용을 입력한다. 
storm.zookeeper.servers:  
    - "kf-broker-1"  
    - "kf-broker-2"  
	- "kf-broker-3"  

storm.local.dir: "/usr1/storm-0.10.0/data"  
nimbus.host: "kf-broker-1"  


supervisor.slots.ports:  
    - 6700  
    - 6701  
    - 6702  
    - 6703  


//편집완료하였으면 각 vm으로 배포한다. 
이부분은 차후에 추가 	
	


*Storm 실행순서 
###Nimbus
/usr1/storm-0.10.0/bin/storm nimbus

###supervisor
/usr1/storm-0.10.0/bin/storm supervisor

Web UI (default port :8080)
/usr1/storm-0.10.0/bin/storm ui 