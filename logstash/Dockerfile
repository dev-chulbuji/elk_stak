FROM docker.elastic.co/logstash/logstash:6.5.1

EXPOSE 5044

COPY logstash.yml /usr/share/logstash/config/logstash.yml
COPY logstash.conf /usr/share/logstash/pipeline/logstash.conf
