# ELK stack 


## Filebeat
data의 forwardingf & centralizing하기 위한 가벼운 shipper


* 지정한 폴더 및 로그파일을 monitor하면서 (second지정 가능) 로그event를 모으고 indexing을 위해 elasticsearch나 logstash에게 forwarding

* config에 기술한 log 위치에 harvester가 로그을 읽어 libbeat에 보내 libbeat는 event들을 aggregate를 해서 data를 out으로 보낸다
   * harvester
      * single파일을 line by line으로 읽어 output으로 보낸다
      * harvester는 file을 읽을 때 파일을 열고 닫는데 책임이 있는데 harvester가 동작하는 동안은 FD(file descripter)가 계속 열려있다
        * FD는 os에서 process가 file에 io를 하는 동안 file이 열려있는지 식별하는 값이다
        * FD는 linux 기준 64개로 제한한다
      * harvester가 동작하는 동안 해당 file이 지워지거나 이름이 변경되더라도 filebeat는 감지하지 못하고 harvester가 close될 때 까지 계속 파일을 읽는다
      * harvester는 close_inactive가 됬을 때까지 도는데 config에서 read 타임을 지정해서 명시적으로 close_inactive 시킬 수 있다.
* filebeat는 registry file에 정보 전달 state를 저장함으로써 최소 한번은 데이터를 보내는걸 guaratee한다
* filebeat는 내부적으로 tcp로 동작을 하고 data를 보내는 도중 shutdown 됬고 output으로 부터 acknowledge를 받지 못한 데이터는 다시 전송한다. 이는 중복 데이터를 보낼 여지를 준다
  * filebeat가 shutdown되기 전에 특정 시간 대기하는 옵션을 줄 수 있다??
* filebeat는 beat로 libbeat framework을 기반으로 한다. beat는 data shipper를 위한 플램폼으로 서비스로는 다음과 같은 것들이 있다
  * filebeat
  * matricbeat
  * packetbeat
  * winlogbeat
  * auditbeat
  * heartbeat
  * functionbeat (for serverless)
* docker filebeat config file location:/usr/share/filebeat/filebeat.yml
* input의 document_type은 logstash의 type으로 전달된다


## logstash
config : [logstash config file](https://www.elastic.co/guide/en/logstash/6.5/config-setting-files.html)
sample data : [catalog](https://catalog.data.gov/dataset)


## kibana
* filebeat를 활용해 로그를 logstash로 보냄
* logstash는 데이터를 변형하여 elastricsearch에 쌓고
* kibana를 활용해 visualization을 한다

* elasticsearch의 용량이 부족하면? -> elasticsearch-curator 서비스를 통해 s3에 오래된 로그들을 저장하고 비운다 (data perging)


* filebeat에서 filebeat.config에서 output.logstash를 설정하려면 logstash의 ip를 cluster내에서 찾아야 하는데 내부 dns를 활용해서 service discovery하는 방법을 알아야 한다
