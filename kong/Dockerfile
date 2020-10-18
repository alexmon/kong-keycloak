FROM kong:2.1.4-ubuntu

LABEL description="Ubuntu + Kong 2.1.4 + kong-oidc plugin"

USER root

RUN apt install -y git unzip 

RUN luarocks install kong-oidc

USER kong