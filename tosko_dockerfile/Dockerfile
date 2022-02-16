FROM debian

LABEL app="giropops"
ENV ISA="primeiro_DockerFile"

RUN apt-get update && apt-get install stress

CMD stress --cpu 1 --vm-bytes 1024M --vm 2

