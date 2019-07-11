FROM alpine
MAINTAINER nn.tran@systel-sa.com
ARG VERSION
ARG SERVER
#ENV VERSION v1.0.0
ENV VERSION ${VERSION:-v1.0.0}
ENV SERVER ${SERVER:-localhost}
RUN echo "Pinger version" $VERSION
CMD ping $SERVER

