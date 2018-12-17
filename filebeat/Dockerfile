FROM docker.elastic.co/beat/filebeat:6.5.1

VOLUME ["~/projects/docker_auto_build_cloud_docker/logs", "/tmp/logs"]

COPY filebeat.yml /usr/share/filebeat/filebeat.yml

CMD ["./filebeat -e -c filebeat.yml'"]