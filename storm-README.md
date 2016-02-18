# Storm Distributed Mode ��ġ


**������**
https://tedwon.atlassian.net/wiki/pages/viewpage.action?pageId=1212933
http://www.michael-noll.com/tutorials/running-multi-node-storm-cluster/


#### ���� �غ����
* CentOS 6.6 
* �ݵ�� root �� �۾��Ѵ�. 
* hosts ���ϰ� windows ��� hosts  ���Ͽ� VM����� ����صд�. (���� �׽�Ʈ ���ǻ�)
* kafka �� zookeeper �� ��ġ�Ǿ��ٰ� �����ϰ� �����Ѵ�. 

#### VM ȯ��
Storm  | hostname  | private ip | program
------------ | ------------ | ------------- | -------------
nimbus | kf-broker-1  |  192.168.13.11   |  kafka + zookeeper
supervisor | kf-broker-2   |  192.168.13.12  |  kafka + zookeeper
supervisor | kf-broker-3   |  192.168.13.13  |  kafka + zookeeper


#### ��ġ ����

// os update
yum update

// openJDK  ��ġ 
yum -y install java-1.7.0-openjdk-devel

// JAVA_HOME ȯ�溯�� �����ϱ� - �ҽ� �����Ҷ� �ʼ�.

vi ~/.bashrc

// ��θ� �ݵ�� Ȯ���� �� �������� �߰� 
JAVA_HOME="/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64"
export JAVA_HOME

// ���Ӽ� ��ġ 
yum -y install gcc gcc-c++ boost boost-devel pkgconfig uuidd libtool autoconf make coreutils uuid-dev uuid-devel libuuid-devel e2fsprogs-devel


//��� �ٲٰ� �����Ѵ�. 
cd /usr1/program

//zeroMQ ��ġ

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

//�ۼ��� ���� �������� make�Ҷ�  ������ �ִ�.  �Ʒ�ó�� Ư�������� �����Ѵ� 
sed -i 's/classdist_noinst.stamp/classnoinst.stamp/g' /usr1/program/jzmq/src/Makefile.am

make
make install

*Storm
cd /usr1/program
wget http://apache.mirror.cdnetworks.com/storm/apache-storm-0.10.0/apache-storm-0.10.0.tar.gz


tar zxf apache-storm-0.10.0.tar.gz  
mv apache-storm-0.10.0  /usr1/storm-0.10.0




*conf/storm.yaml ���� �� **��� ��� ����**

//vi�� ����
vi /usr1/storm-0.10.0/conf/storm.yaml 

//�Ʒ� ������ �Է��Ѵ�. 
storm.zookeeper.servers:
    - "kf-broker-1"
    - "kf-broker-2"
	- "kf-broker-3"

storm.local.dir: "/usr1/storm-0.10.0/data"
nimbus.host: "kf-broker-1"

# how many workers run on that machine
supervisor.slots.ports:
    - 6700
    - 6701
    - 6702
    - 6703


//�����Ϸ��Ͽ����� �� vm���� �����Ѵ�. 
** �̺κ��� ���Ŀ� �߰� 	
	
	
	

*Storm ������� 
**Nimbus**
/usr1/storm-0.10.0/bin/storm nimbus

**supervisor**
/usr1/storm-0.10.0/bin/storm supervisor

**Web UI** (default port :8080)
/usr1/storm-0.10.0/bin/storm ui 